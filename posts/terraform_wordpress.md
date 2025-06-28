### WordPress-i paigaldamine Pilviosse Docker Compose'iga, kasutades Terraformi

#### Sissejuhatus

Selles juhendis kasutame Terraformi, et täielikult automatiseerida produktsioonivalmis WordPressi lehe paigaldus Pilvio virtuaalmasinasse. Me ei paigalda ainult virtuaalmasinat, vaid seadistame ka kogu tarkvaralise lahenduse, mis koosneb WordPressist, MySQL andmebaasist ja Caddy veebiserverist. Kõik need komponendid töötavad eraldiseisvates Docker konteinerites, mida haldab Docker Compose. Lisaks seadistab Caddy automaatselt Let's Encrypt abil tasuta HTTPS-sertifikaadi, tagades turvalise ühenduse.

Lõpptulemusena on sul kood, mis suudab mõne minutiga luua täielikult toimiva WordPressi lehe ja selle ka hiljem sama lihtsalt eemaldada.

#### Eeltingimused

Enne alustamist veendu, et sul on olemas järgmised asjad:

- **Pilvio konto:** Sul peab olema aktiveeritud arveldusega Pilvio konto.
- **Pilvio API võti:** Loodud ja salvestatud API võti. Selle leiad Pilvio halduspaneelist.
- **Pilvio arvelduskonto ID:** Sinu konto unikaalne ID. Ka selle leiad halduspaneelist.
- **Domeen:** Registreeritud domeeninimi (nt `minublogi.ee`), mille DNS-kirjeid sa saad muuta.
- **Terraform:** Sinu arvutisse peab olema [paigaldatud Terraform](https://learn.hashicorp.com/tutorials/terraform/install-cli).
- **SSH võtmepaar:** Toimiv SSH võtmepaar (avalik ja privaatne võti), mida kasutada virtuaalmasinaga ühendumiseks.

### Samm 1 — Floating-IP loomine ja domeeni suunamine

Enne Terraformiga alustamist peame looma floating-IP ja suunama oma domeeni sellele IP-aadressile. See on vajalik, sest Caddy veebiserver üritab kohe pärast käivitamist hankida domeenile SSL-sertifikaati. Selleks peab domeen juba viitama serveri korrektsele IP-aadressile.

1. Mine Pilvio halduspaneeli: **Network → Floating IPs**.
2. Loo uus floating-IP ja anna talle nimeks `wp_caddy_ip`. Pilvio omistab sulle staatilise IP-aadressi, näiteks `176.112.152.224`. Salvesta see aadress, sest seda läheb hiljem vaja.
3. Mine oma domeeni registripidaja (või DNS-teenuse pakkuja) halduspaneeli ja loo uus **A-kirje**:
   - **Tüüp:** `A`
   - **Nimi / Host:** `blogi.sinudomeen.ee` (või `@`, kui soovid kasutada juurdomeeni)
   - **Väärtus / Points to:** `176.112.152.224` (Sinu Pilviost saadud ujuv-IP)
   - **TTL:** `3600` (või jäta vaikimisi väärtus)

Nüüd, kui DNS on seadistatud, võime hakata looma oma Terraformi konfiguratsiooni.

### Samm 2 — Terraformi failide ettevalmistamine

Loo oma projekti jaoks uus kaust (nt `wordpress-pilvio`) ja loo sinna sisse järgmised failid.

#### `versions.tf`

See fail määrab ära Terraformi enda ja vajalike *providerite* versioonid. See tagab, et konfiguratsioon töötab ka tulevikus ootuspäraselt.

Terraform

```
terraform {
  required_version = ">= 1.3.0"

  required_providers {
    pilvio = {
      source  = "pilvio-com/pilvio"
      version = ">= 1.0.0"
    }
  }
}

provider "pilvio" {
  apikey   = var.apikey
  host     = "api.pilvio.com"
  location = var.location
}
```

- `terraform {}` plokk nõuab, et kasutatav Terraformi versioon oleks vähemalt `1.3.0`.
- `required_providers {}` plokk defineerib, et me kasutame `pilvio` providerit, mis laetakse alla otse Terraformi registrist.
- `provider "pilvio" {}` plokk konfigureerib Pilvio provideri. See kasutab muutujaid (`var.apikey`, `var.location`), et autentida end API vastu ja valida andmekeskuse asukoht.

#### `variables.tf`

See fail deklareerib kõik muutujad, mida meie projektis kasutatakse. Muutujate kasutamine teeb konfiguratsiooni paindlikuks ja korduvkasutatavaks. Tegelikud väärtused anname ette eraldi `terraform.tfvars` failis.

Terraform

```
variable "apikey" {
  description = "Pilvio API key"
  type        = string
  sensitive   = true
}

variable "billing_account_id" {
  description = "Your Pilvio billing account ID"
  type        = number
}

variable "location" {
  description = "Pilvio region"
  type        = string
  default     = "tll01"
}

variable "vm_name" {
  description = "Name of the VM"
  type        = string
  default     = "wordpress-caddy"
}

variable "vm_username" {
  type    = string
  default = "admin"
}

variable "vm_password" {
  description = "VM login password (still required by API even if you use SSH)"
  type        = string
  sensitive   = true
}

variable "public_key" {
  description = "SSH public key"
  type        = string
}

variable "wp_caddy_ip" {
  description = "Existing floating IP address"
  type        = string
}

variable "domain_name" {
  description = "Your domain pointing to the floating IP"
  type        = string
}

variable "vm_memory" {
  type    = number
  default = 2048
}

variable "vm_vcpu" {
  type    = number
  default = 2
}

variable "vm_disk_size" {
  type    = number
  default = 25
}

variable "bucket_name" {
  description = "Name of S3-compatible bucket"
  type        = string
  default     = "wordpress-assets"
}

variable "db_user" {
  type    = string
  default = "wordpress"
}

variable "db_password" {
  type        = string
  sensitive   = true
}

variable "db_name" {
  type    = string
  default = "wordpress"
}
```

- `sensitive = true` atribuut tagab, et paroolide ja API-võtmete väärtusi ei kuvata Terraformi väljundis.
- `default` väärtused on eeltäidetud, kuid neid saab `terraform.tfvars` failis üle kirjutada.

#### `main.tf`

See on meie infrastruktuuri peamine kirjeldusfail. Siin defineerime kõik ressursid, mida soovime luua: virtuaalvõrgu, virtuaalmasina, S3-ühilduva bucketi ja seome floating-IP serveriga.

Terraform

```
resource "pilvio_vpc" "main" {
  name = "wordpress-vpc"
}

resource "pilvio_vm" "wordpress" {
  name               = var.vm_name
  os_name            = "ubuntu"
  os_version         = "22.04"
  memory             = var.vm_memory
  vcpu               = var.vm_vcpu
  username           = var.vm_username
  password           = var.vm_password
  disks              = var.vm_disk_size
  billing_account_id = var.billing_account_id
  network_uuid       = pilvio_vpc.main.uuid
  public_key         = var.public_key

  cloud_init = jsonencode({
    packages = ["docker.io", "docker-compose"]
    write_files = [
      {
        path        = "/home/${var.vm_username}/docker-compose.yml"
        permissions = "0644"
        encoding    = "b64"
        content     = base64encode(templatefile("${path.module}/docker-compose.tpl", {
          db_user     = var.db_user,
          db_password = var.db_password,
          db_name     = var.db_name,
          domain_name = var.domain_name
        }))
      },
      {
        path        = "/home/${var.vm_username}/Caddyfile"
        permissions = "0644"
        content     = "${var.domain_name} {\n  reverse_proxy wordpress:80\n}"
      }
    ],
    runcmd = [
      "usermod -aG docker ${var.vm_username}",
      "usermod -aG sudo ${var.vm_username}",
      "chown ${var.vm_username}:${var.vm_username} /home/${var.vm_username}/*.yml /home/${var.vm_username}/Caddyfile",
      "cd /home/${var.vm_username}",
      "sudo docker-compose up -d"
    ]
  })
}

data "pilvio_floatingip" "wp_caddy" {
  address = var.wp_caddy_ip
}

resource "pilvio_floatingip_assignment" "attach_wp_caddy" {
  address     = data.pilvio_floatingip.wp_caddy.address
  assigned_to = pilvio_vm.wordpress.uuid
}

resource "pilvio_bucket" "assets" {
  name               = var.bucket_name
  billing_account_id = var.billing_account_id
}
```

- `resource "pilvio_vpc" "main"`: Loob privaatse virtuaalvõrgu (VPC), et meie virtuaalmasin saaks võrguühenduse.
- `resource "pilvio_vm" "wordpress"`: See on peamine ressurss, mis loob virtuaalmasina. Parameetrid nagu `os_name`, `memory`, `vcpu` jne on siin defineeritud.
- `cloud_init`: See on kõige maagilisem osa. `cloud-init` on standard, mis võimaldab käivitada skripte virtuaalmasina esimesel käivitusel.
  - `packages`: Anname korralduse paigaldada `docker.io` ja `docker-compose`.
  - `write_files`: Loob virtuaalmasinasse kaks faili. Esimene on `docker-compose.yml`, mille sisu võetakse `docker-compose.tpl` mallifailist ja täidetakse muutujatega. Sisu kodeeritakse `base64` formaati. Teine on `Caddyfile`, mis on Caddy veebiserveri lihtne konfiguratsioonifail.
  - `runcmd`: Käivitab rea käske pärast failide loomist. Need käsud lisavad kasutaja `docker` ja `sudo` gruppidesse, seavad õiged omanikud failidele ja lõpuks käivitavad `docker-compose up -d` käsuga kogu meie WordPressi lahenduse.
- `data "pilvio_floatingip" "wp_caddy"`: See ei loo uut ressurssi, vaid loeb andmeid juba olemasoleva ujuv-IP kohta, mille me esimeses sammus käsitsi lõime.
- `resource "pilvio_floatingip_assignment" "attach_wp_caddy"`: Seob eelnevalt loetud ujuv-IP äsja loodud `wordpress` virtuaalmasinaga.
- `resource "pilvio_bucket" "assets"`: Loob S3-ühilduva objektisalvestuse bucket'i, mida saab tulevikus kasutada näiteks WordPressi meediafailide hoidmiseks.

#### `docker-compose.tpl`

See on mallifail, mitte valmis `yml`-fail. Terraform kasutab seda malli, et genereerida `docker-compose.yml` fail, asendades `${...}` kohatäited tegelike väärtustega `variables.tf` failist.

YAML

```
version: '3.7'

services:
  wordpress:
    image: wordpress:latest
    restart: always
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: ${db_user}
      WORDPRESS_DB_PASSWORD: ${db_password}
      WORDPRESS_DB_NAME: ${db_name}
      WORDPRESS_CONFIG_EXTRA: |
        define('WP_HOME','https://${domain_name}');
        define('WP_SITEURL','https://${domain_name}');
    volumes:
      - wp_data:/var/www/html
    expose:
      - "80"

  db:
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_DATABASE: ${db_name}
      MYSQL_USER: ${db_user}
      MYSQL_PASSWORD: ${db_password}
      MYSQL_RANDOM_ROOT_PASSWORD: "1"
    volumes:
      - db_data:/var/lib/mysql

  caddy:
    image: caddy:latest
    restart: always
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - caddy_data:/data
      - caddy_config:/config
      - ./Caddyfile:/etc/caddy/Caddyfile
    depends_on:
      - wordpress

volumes:
  wp_data:
  db_data:
  caddy_data:
  caddy_config:
```

- `services`: Defineerib kolm teenust (konteinerit):
  - `wordpress`: Ametlik WordPressi konteiner. `environment` sektsioon seadistab andmebaasi ühenduse parameetrid ja WordPressi aadressi.
  - `db`: MySQL 5.7 andmebaasi konteiner.
  - `caddy`: Caddy veebiserveri konteiner. See avab pordid 80 ja 443 ning kasutab `Caddyfile` faili, et suunata liiklus WordPressi konteinerile ja hankida automaatselt HTTPS-sertifikaat.
- `volumes`: Need loovad püsivad andmekettad (Docker volumes), et WordPressi failid, andmebaas ja Caddy sertifikaadid säiliksid ka siis, kui konteinerid taaskäivitatakse.

#### `outputs.tf`

See fail määrab, millised andmed kuvatakse kasutajale pärast Terraformi edukat käivitamist. See on kasulik, et saada kiire ülevaade loodud infrastruktuuri olulistest parameetritest.

Terraform

```
output "wordpress_domain" {
  value = var.domain_name
}

output "wordpress_ip" {
  value = data.pilvio_floatingip.wp_caddy.address
}

output "bucket_name" {
  value = pilvio_bucket.assets.name
}
```

### Samm 3 — Muutujate konfigureerimine (`terraform.tfvars`)

Nüüd, kus kõik mallid ja konfiguratsioonid on paigas, peame andma Terraformile ette konkreetsed väärtused. Loo samasse kausta fail nimega `terraform.tfvars`. **ÄRA KUNAGI lisa seda faili avalikku koodihoidlasse (nagu GitHub), sest see sisaldab sinu saladusi!**

Kopeeri allolev sisu faili `terraform.tfvars` ja asenda väärtused enda omadega.

Terraform

```
# Asenda väärtused enda omadega
# Pilvio seaded
apikey             = "sinu-pilvio-api-võti-tuleb-siia"
billing_account_id = 123456
vm_password        = "VägaTugevParool123!"
public_key         = "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQD..."

# Infrastruktuuri seaded
wp_caddy_ip = "sinu-loodud-floating-ip-väärtus"
domain_name = "blogi.sinudomeen.ee"

# Andmebaasi seaded
db_user     = "wordpress"
db_password = "turvaline-andmebaasi-parool"
db_name     = "wordpress"
```

**Juhised väärtuste täitmiseks:**

- `apikey`: Kopeeri siia oma Pilvio API võti.
- `billing_account_id`: Sisesta oma Pilvio arvelduskonto ID (numbrina).
- `vm_password`: Mõtle välja ja sisesta turvaline parool virtuaalmasina jaoks. Kuigi kasutame sisselogimiseks SSH-võtit, nõuab Pilvio API parooli olemasolu.
- `public_key`: Kopeeri siia oma **avaliku** SSH-võtme sisu. See näeb tavaliselt välja `ssh-rsa AAAA...` ja asub sinu arvutis failis `~/.ssh/id_rsa.pub`.
- `wp_caddy_ip`: Sisesta siia 1. sammus loodud ujuv-IP aadress.
- `domain_name`: Sisesta täpne domeeninimi, mille suunasid ujuv-IP-le (nt `wordpress.minufirma.ee`).
- `db_user`, `db_password`, `db_name`: Võid jätta need vaikimisi väärtustele või muuta, kui soovid. `db_password` peaks kindlasti olema tugev ja unikaalne.

### Samm 4 — Infrastruktuuri paigaldamine



Nüüd on kõik valmis! Ava terminal, navigeeri oma projekti kausta ja käivita järgmised Terraformi käsud.

1. **Initsialiseeri Terraform:** See käsk laeb alla Pilvio provideri ja valmistab töökataloogi ette.

   Bash

   ```
   terraform init
   ```

2. **Loo ja vaata tegevusplaani:** See käsk analüüsib sinu `.tf` faile ja näitab, milliseid ressursse (VPC, VM jne) ta kavatseb luua, muuta või kustutada. See on turvaline samm, mis ei tee veel reaalseid muudatusi.

   Bash

   ```
   terraform plan -out=tfplan
   ```

3. **Rakenda tegevusplaan:** See käsk viib eelmises sammus loodud plaani ellu. Terraform küsib sinult viimast kinnitust, enne kui hakkab Pilviosse ressursse looma.

   Bash

   ```
   terraform apply tfplan
   ```

Protsess võtab mõne minuti. Terraform loob järjest kõik ressursid ja konfigureerib virtuaalmasina. Kui kõik on valmis, näed terminali väljundis `outputs.tf` failis defineeritud väärtusi.

### Samm 5 — WordPressi lehele ligipääsemine

Kui `terraform apply` on edukalt lõppenud, oota mõned minutid, et cloud-init saaks vastloodud serveris oma paigaldused lõpetada. Seejärel ava veebilehitseja ja mine oma domeeni aadressile, mille sisestasid `terraform.tfvars` faili.

**`https://blogi.sinudomeen.ee`**

Sind peaks ees ootama WordPressi kuulus viieminutiline paigaldusaken. Caddy on taustal juba hankinud ja seadistanud HTTPS-sertifikaadi, seega on sinu leht kohe turvaline. Vali keel ja loo oma administraatori kasutaja.

### Infrastruktuuri eemaldamine (Valikuline)

Terraformi üks suurimaid eeliseid on see, et kogu loodud infrastruktuuri saab sama lihtsalt ka eemaldada. Kui soovid oma WordPressi lehe ja kõik sellega seotud ressursid (VM, VPC, Bucket) kustutada, käivita lihtsalt üks käsk:

Bash

```
terraform destroy
```

Terraform küsib taas kinnitust ja pärast seda kustutab kõik, mis selle konfiguratsiooniga loodi. Ujuv-IP jääb alles, kuna seda ei loonud Terraform.

### Kokkuvõte

Palju õnne! Sa oled edukalt kasutanud Terraformi, et paigaldada täielikult funktsioneeriv ja turvaline WordPressi veebileht Pilvio pilveplatvormile.

Sa seadistasid:

- Infrastruktuuri kui koodi (IaC) lahenduse Terraformiga.
- WordPressi, mis töötab Docker Compose'i abil.
- Automaatse HTTPS-i Caddy veebiserveriga.
- Modulaarse ja turvalise konfiguratsiooni, mida on lihtne hallata ja taaskasutada.

Nüüd on sul olemas võimas ja automatiseeritud viis veebiprojektide käivitamiseks ja haldamiseks.
