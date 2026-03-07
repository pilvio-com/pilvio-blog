---
title: "Terraform Pilvio näitel: paigaldame Nextcloud'i"           # Required: Post title
author: "Kaur Kiisler"                   # Required: Author name
publishedAt: "4. juuni 2025"        # Required: Estonian format date
category: "Õpetused"                  # Required: Post category
tags: ["terraform", "devops"]          # Required: Array of tags
excerpt: "Selle juhendi lõpuks on sul täielikult toimiv Nextcloudi instants, mis töötab sinu enda Pilvio pilves."
featured: false                         # Required: Boolean (only one post should be true)
imageUrl: "/media/tf_nextcloud.png" # Optional: Featured image URL
---
# Kuidas paigaldada Nextcloud Pilvio pilve Terraformi, Docker Compose'i ja S3-ga

## Sissejuhatus

Nextcloud on populaarne avatud lähtekoodiga failide jagamise ja koostööplatvorm. See võimaldab sul luua oma privaatse pilvesalvestusruumi, mida saad täielikult kontrollida. Selles juhendis õpid, kuidas automatiseerida Nextcloudi täielikku paigaldust Pilvio pilveinfrastruktuurile.

Me kasutame **Terraformi**, et infrastruktuur koodina (Infrastructure as Code) defineerida ja hallata. See hõlmab virtuaalmasina (VM) loomist, võrguseadete konfigureerimist ja Pilvio S3-ühilduva *bucketi* loomist Nextcloudi andmete salvestamiseks. Lõpuks kasutame **Docker Compose'i**, et Nextcloudi rakendus ja selle MariaDB andmebaas VM-is lihtsalt ja kiirelt käivitada.

Selle juhendi lõpuks on sul täielikult toimiv Nextcloudi instants, mis töötab sinu enda Pilvio pilves. See kasutab skaleeritavat S3 objektisalvestust failide hoidmiseks, tagades andmete turvalisuse ja kättesaadavuse.

## Eeldused

Enne alustamist veendu, et sul on olemas järgmised asjad:

- **Pilvio konto ja API võti:** Sul peab olema aktiivne Pilvio arvelduskonto (`billing_account_id`) ja genereeritud API võti. API võtme ja arvelduskonto ID leiad Pilvio portaalist.
- **Terraform paigaldatud:** Terraform (versioon 1.3.0 või uuem) peab olema paigaldatud sinu kohalikku masinasse. Juhised leiad [Terraformi ametlikult veebilehelt](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli).
- **Põhiteadmised käsureast ja SSH-st:** Sa peaksid olema tuttav käsurea kasutamisega ja oskama SSH kaudu serverisse sisse logida.
- **Pilvio S3 võtmed:** Sul on vaja oma Pilvio S3 *access key* ja *secret key*. Need leiad Pilvio portaalist peale *bucketi* loomist või kui sul on juba varasemalt võtmepaar genereeritud. Selles juhendis loome *bucketi* Terraformiga, kuid võtmed pead ise eelnevalt Pilvio keskkonnast hankima või looma uued.

> ⚠️ **Tähelepanu:** See juhend eeldab, et oled tuttav Terraformi põhimõtetega. Kui oled Terraformiga uus, soovitame esmalt tutvuda [Terraformi dokumentatsiooniga](https://developer.hashicorp.com/terraform/intro).

## Samm 1 – Terraformi konfiguratsioonifailide loomine

Selles sammus loome kõik vajalikud Terraformi konfiguratsioonifailid Nextcloudi infrastruktuuri defineerimiseks. Loo oma projekti jaoks uus kaust (nt `pilvio-nextcloud`) ja salvesta järgnevad failid sinna.

### Fail: `versions.tf`

See fail määratleb Terraformi ja vajalike *providerite* (antud juhul Pilvio *provideri*) versioonid. Samuti konfigureeritakse siin Pilvio *provider*.

Loo fail nimega `versions.tf` ja lisa sinna järgmine sisu:

```
terraform {
  required_version = ">= 1.3.0"
  required_providers {
    pilvio = {
      source  = "pilvio-com/pilvio"
      version = ">= 1.0.0" # Kasuta uusimat sobivat versiooni
    }
  }
}

provider "pilvio" {
  apikey   = var.apikey
  host     = var.host     # Vaikimisi: api.pilvio.com
  location = var.location # Lubatud: "tll01", "jhvi", "jhv02"
}

# VALIKULINE: Pilvio S3 backend Terraform state'i jaoks
# Kui soovid Terraformi olekufaili (state file) hoida Pilvio S3 bucketis,
# eemalda kommentaarid ja kohanda järgnevat plokki oma andmetega.
# See on soovitatav praktika, eriti meeskonnatöö puhul.
# terraform {
#   backend "s3" {
#     bucket                      = "sinu-unikaalne-tfstate-bucket-nimi" # ASENDA OMA BUCKETI NIMEGA
#     key                         = "nextcloud/terraform.tfstate"
#     region                      = "eu-west-1" # Pilvio S3 vaikeregioon
#     endpoint                    = "s3.pilw.io" # Pilvio S3 endpoint
#     access_key                  = "SINU_S3_ACCESS_KEY" # ASENDA
#     secret_key                  = "SINU_S3_SECRET_KEY" # ASENDA
#     skip_credentials_validation = true
#     skip_metadata_api_check     = true
#     skip_requesting_account_id  = true
#     force_path_style            = true
#   }
# }
```

> **Märkus:** Kui otsustad kasutada S3 *backendi* Terraformi olekufaili jaoks, asenda kindlasti kommentaarides märgitud väärtused oma tegelike andmetega. S3 *backend* on hea praktika meeskonnatööks ja olekufaili turvaliseks ning tsentraliseeritud hoidmiseks.

### Fail: `variables.tf`

Selles failis deklareerime kõik muutujad, mida meie Terraformi konfiguratsioon kasutab. See võimaldab meil hoida konfiguratsiooni paindliku ja vältida tundlike andmete otse koodi kirjutamist.

Loo fail nimega `variables.tf`:

```
variable "apikey" {
  description = "Sinu Pilvio API võti."
  type        = string
  sensitive   = true
}

variable "host" {
  description = "Pilvio API endpoint."
  type        = string
  default     = "api.pilvio.com"
}

variable "location" {
  description = "Pilvio asukoht, kus ressursid luuakse (nt 'tll01', 'jhvi', 'jhv02')."
  type        = string
  default     = "tll01" # Vali endale sobiv asukoht
}

variable "billing_account_id" {
  description = "Sinu Pilvio arvelduskonto ID."
  type        = number
}

# VM ja võrgu seadistused
variable "vpc_name" {
  description = "Loodava VPC (Virtual Private Cloud) nimi."
  type        = string
  default     = "nextcloud-vpc"
}

variable "vm_name" {
  description = "Nextcloudi virtuaalmasina nimi."
  type        = string
  default     = "nextcloud-server"
}

variable "vm_os_name" {
  description = "Virtuaalmasina operatsioonisüsteemi nimi (nt 'ubuntu', 'debian')."
  type        = string
  default     = "ubuntu"
}

variable "vm_os_version" {
  description = "Operatsioonisüsteemi versioon (nt '22.04' Ubuntu jaoks)."
  type        = string
  default     = "22.04"
}

variable "vm_username" {
  description = "Virtuaalmasina administraatori kasutajanimi."
  type        = string
  default     = "pilvioadmin" # Vali turvaline kasutajanimi
}

variable "vm_password" {
  description = "Virtuaalmasina administraatori parool (tundlik)."
  type        = string
  sensitive   = true
  # Vaikimisi väärtust ei tohiks siin olla, sisesta .tfvars failis
}

variable "vm_memory" {
  description = "Virtuaalmasina RAM-i maht megabaitides (MB)."
  type        = number
  default     = 2048 # 2GB, Nextcloudile soovitatav miinimum
}

variable "vm_vcpu" {
  description = "Virtuaalmasina vCPU-de arv."
  type        = number
  default     = 2
}

variable "vm_disk_size" {
  description = "Virtuaalmasina põhiketta suurus gigabaitides (GB)."
  type        = number
  default     = 40
}

# Nextcloudi ja MariaDB andmebaasi seadistused
variable "db_user" {
  description = "Nextcloudi andmebaasi kasutajanimi."
  type        = string
  default     = "nextclouduser"
}

variable "db_password" {
  description = "Nextcloudi andmebaasi kasutaja parool (tundlik)."
  type        = string
  sensitive   = true
}

variable "db_root_password" {
  description = "MariaDB root kasutaja parool (tundlik)."
  type        = string
  sensitive   = true
}

# Nextcloudi admin seaded
variable "nextcloud_admin_user" {
  description = "Nextcloudi administraatori kasutajanimi."
  type        = string
  default     = "ncadmin"
}

variable "nextcloud_admin_password" {
  description = "Nextcloudi administraatori parool (tundlik)."
  type        = string
  sensitive   = true
}

# S3 bucket ja võtmed
variable "bucket_name" {
  description = "Pilvio S3 bucketi nimi (peab olema globaalselt unikaalne!)."
  type        = string
  default     = "pilvio-nextcloud-data-bucket-tf" # Muuda see kindlasti unikaalseks!
}

variable "s3_access_key" {
  description = "Sinu Pilvio S3 access key."
  type        = string
  sensitive   = true
}

variable "s3_secret_key" {
  description = "Sinu Pilvio S3 secret key."
  type        = string
  sensitive   = true
}

variable "s3_host" {
  description = "Pilvio S3 endpoint."
  type        = string
  default     = "s3.pilw.io"
}

variable "s3_region" {
  description = "Pilvio S3 regioon."
  type        = string
  default     = "eu-west-1" # Pilvio S3 vaikeregioon
}

# Avalik IP
variable "floatingip_name" {
  description = "Loodava Floating IP (avaliku IP) nimi."
  type        = string
  default     = "nextcloud-fip"
}
```

> 💡 **Nõuanne:** Kasuta muutujate kirjeldustes (`description`) selget keelt, et hiljem oleks lihtne aru saada, mida iga muutuja teeb. Tundlike andmete jaoks (`apikey`, paroolid, S3 võtmed) kasuta alati `sensitive = true`.

### Fail: `terraform.tfvars` (Loo see ise)

See fail on mõeldud sinu konkreetsete väärtuste, eriti tundlike andmete, hoidmiseks. **Terraform laeb automaatselt `.tfvars` failist muutujate väärtused.**

Loo fail nimega `terraform.tfvars` ja täida see oma andmetega:

```
# Pilvio API ja arveldus
apikey             = "SINU_PILVIO_API_VÕTI_SIIA"
billing_account_id = 12345 # ASENDA OMA ARVELDUSKONTO ID-GA

# VM parool
vm_password = "GENEREERI_VÄGA_TUGEV_PAROOL_VM_JAOKS"

# MariaDB paroolid
db_password      = "GENEREERI_TUGEV_PAROOL_NEXTCLOUD_DB_KASUTAJALE"
db_root_password = "GENEREERI_VÄGA_TUGEV_PAROOL_DB_ROOT_KASUTAJALE"

# Nextcloud admin parool
nextcloud_admin_password = "GENEREERI_TUGEV_PAROOL_NEXTCLOUD_ADMINILE"

# S3 bucket ja võtmed
s3_access_key = "SINU_PILVIO_S3_ACCESS_KEY_SIIA"
s3_secret_key = "SINU_PILVIO_S3_SECRET_KEY_SIIA"

# Vajadusel saad siin üle kirjutada ka teisi vaikimisi väärtusi variables.tf failist:
# location    = "tll01"
# vm_name     = "minu-nextcloud"
# bucket_name = "minu-unikaalne-nextcloud-bucket-123"
```

> ⚠️ **Hoiatus:** Ära kunagi lisa `terraform.tfvars` faili versioonihaldusesse (nt Git), kui see sisaldab tundlikke andmeid! Lisa see fail oma `.gitignore` faili.

### Fail: `main.tf`

See on peamine konfiguratsioonifail, kus defineerime kõik ressursid, mida Terraform Pilvios loob: VPC, virtuaalmasin, S3 *bucket* ja ujuv IP-aadress. Samuti kasutame siin `cloud-init`, et automatiseerida Docker'i, Docker Compose'i ja Nextcloudi esmane seadistamine VM-is.

Loo fail nimega `main.tf`:

```
# Loome Virtual Private Cloud (VPC)
resource "pilvio_vpc" "main" {
  name     = var.vpc_name
  location = var.location
}

# Loome Nextcloudi virtuaalmasina
resource "pilvio_vm" "nextcloud" {
  name               = var.vm_name
  os_name            = var.vm_os_name
  os_version         = var.vm_os_version
  memory             = var.vm_memory
  vcpu               = var.vm_vcpu
  username           = var.vm_username
  password           = var.vm_password # See on tundlik, tuleb .tfvars failist
  disks              = var.vm_disk_size
  billing_account_id = var.billing_account_id
  location           = var.location
  network_uuid       = pilvio_vpc.main.uuid

  # cloud-init skript VM-i esmaseks seadistamiseks
  cloud_init = jsonencode({
    write_files = [
      {
        path        = "/home/${var.vm_username}/docker-compose.yml"
        permissions = "0644" # Omanik saab lugeda/kirjutada, teised lugeda
        content     = <<-EOT
        version: '3.8' # Kasuta sobivat Docker Compose versiooni

        services:
          db:
            image: mariadb:10.11 # Soovitatav on kasutada kindlat versiooni
            container_name: nextcloud_db
            restart: always
            command: --transaction-isolation=READ-COMMITTED --log-bin=ROW --innodb-read-only-compressed=OFF
            environment:
              MYSQL_DATABASE: nextcloud
              MYSQL_USER: ${var.db_user}
              MYSQL_PASSWORD: ${var.db_password}
              MYSQL_ROOT_PASSWORD: ${var.db_root_password}
            volumes:
              - db_data:/var/lib/mysql
            networks:
              - nextcloud_network

          app:
            image: nextcloud:28 # Kasuta uusimat stabiilset Nextcloudi versiooni
            container_name: nextcloud_app
            restart: always
            ports:
              - "80:80" # HTTP port, HTTPS seadista hiljem reverse proxy'ga
            depends_on:
              - db
            environment:
              MYSQL_HOST: db
              MYSQL_DATABASE: nextcloud
              MYSQL_USER: ${var.db_user}
              MYSQL_PASSWORD: ${var.db_password}
              NEXTCLOUD_ADMIN_USER: ${var.nextcloud_admin_user}
              NEXTCLOUD_ADMIN_PASSWORD: ${var.nextcloud_admin_password}
              NEXTCLOUD_TRUSTED_DOMAINS: "${pilvio_floatingip.nextcloud.address}" # Dünaamiliselt seadistatud
              # S3 seadistused Nextcloudile
              OBJECTSTORE_S3_HOST: "${var.s3_host}"
              OBJECTSTORE_S3_BUCKET: "${var.bucket_name}"
              OBJECTSTORE_S3_KEY: "${var.s3_access_key}"
              OBJECTSTORE_S3_SECRET: "${var.s3_secret_key}"
              OBJECTSTORE_S3_PORT: 443
              OBJECTSTORE_S3_SSL: "true"
              OBJECTSTORE_S3_REGION: "${var.s3_region}"
              OBJECTSTORE_S3_AUTOCREATE: "false" # Bucket luuakse Terraformiga
              OBJECTSTORE_S3_USEPATH_STYLE: "true" # Vajalik Pilvio S3 jaoks
              # Need on vajalikud, et Nextcloud saaks S3-ga suhelda, isegi kui OBJECTSTORE_S3_AUTOCREATE on false
              AWS_ACCESS_KEY_ID: "${var.s3_access_key}"
              AWS_SECRET_ACCESS_KEY: "${var.s3_secret_key}"
            volumes:
              - nextcloud_data:/var/www/html
            networks:
              - nextcloud_network

        volumes:
          db_data:
          nextcloud_data:

        networks:
          nextcloud_network:
            driver: bridge
        EOT
      }
    ],
    packages = [ # Paigaldatavad paketid
      "curl",
      "apt-transport-https",
      "ca-certificates",
      "gnupg-agent",
      "software-properties-common"
    ],
    runcmd = [ # Käsklused, mis käivitatakse pärast pakettide paigaldamist
      # Docker'i paigaldamine
      "curl -fsSL [https://download.docker.com/linux/ubuntu/gpg](https://download.docker.com/linux/ubuntu/gpg) | apt-key add -",
      "add-apt-repository \"deb [arch=amd64] [https://download.docker.com/linux/ubuntu](https://download.docker.com/linux/ubuntu) $(lsb_release -cs) stable\"",
      "apt-get update -y",
      "apt-get install -y docker-ce docker-ce-cli containerd.io",
      "usermod -aG docker ${var.vm_username}",
      # Docker Compose'i paigaldamine
      "curl -L \"[https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname](https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname) -s)-$(uname -m)\" -o /usr/local/bin/docker-compose",
      "chmod +x /usr/local/bin/docker-compose",
      # Käivita Docker Compose Nextcloudi ja andmebaasi jaoks (kasutaja kodukaustas)
      "su - ${var.vm_username} -c \"cd /home/${var.vm_username} && docker-compose up -d\""
    ]
  })

  # Oota, kuni cloud-init on lõpetanud, enne kui ressursi loomine loetakse edukaks
  # See on oluline, et järgnevad sammud (nt Floating IP sidumine) ei ebaõnnestuks
  provisioner "remote-exec" {
    inline = [
      "cloud-init status --wait"
    ]
    connection {
      type     = "ssh"
      user     = var.vm_username
      password = var.vm_password
      host     = self.access_ipv4 != "" ? self.access_ipv4 : self.private_ipv4 # Proovi avalikku IP-d, kui see on olemas
    }
  }
}

# Loome S3 bucketi Nextcloudi andmete jaoks
resource "pilvio_bucket" "nextcloud" {
  name               = var.bucket_name # Peab olema unikaalne!
  billing_account_id = var.billing_account_id
  # Asukohta ei saa Pilvio bucketile määrata, see on globaalne Pilvio S3 teenuse piires
}

# Loome ujuva (avaliku) IP-aadressi
resource "pilvio_floatingip" "nextcloud" {
  name               = var.floatingip_name
  billing_account_id = var.billing_account_id
  location           = var.location # Floating IP on seotud asukohaga
}

# Seome ujuva IP-aadressi meie Nextcloudi VM-iga
resource "pilvio_floatingip_assignment" "nextcloud" {
  # Sõltub VM-ist, et see oleks enne loodud
  depends_on = [pilvio_vm.nextcloud]

  address     = pilvio_floatingip.nextcloud.address
  assigned_to = pilvio_vm.nextcloud.uuid # VM-i UUID
  location    = var.location
}
```

> **Selgitus `cloud-init` kohta:** `cloud-init` on standardne tööriist pilveserverite esmaseks konfigureerimiseks. Meie `main.tf` failis kasutame seda, et:
>
> 1. Kirjutada `docker-compose.yml` fail VM-i. See fail sisaldab Nextcloudi ja MariaDB konteinerite konfiguratsiooni.
> 2. Paigaldada vajalikud paketid, sealhulgas Docker.
> 3. Laadida alla ja paigaldada Docker Compose.
> 4. Käivitada `docker-compose up -d`, et Nextcloud ja andmebaas taustal tööle panna. `remote-exec` provisioner koos `cloud-init status --wait` käsuga tagab, et Terraform ootab, kuni `cloud-init` protsess on VM-is lõpule jõudnud, enne kui jätkab teiste ressursside (nagu Floating IP sidumine) loomisega.

### Fail: `outputs.tf`

See fail defineerib väljundväärtused, mida Terraform kuvab pärast infrastruktuuri edukat loomist. Need on kasulikud, et kiiresti kätte saada olulist infot, nagu VM-i avalik IP-aadress.

Loo fail nimega `outputs.tf`:

```
output "nextcloud_vm_id" {
  description = "Loodud Nextcloudi virtuaalmasina UUID."
  value       = pilvio_vm.nextcloud.uuid
}

output "nextcloud_vm_private_ip" {
  description = "Nextcloudi virtuaalmasina privaatne IP-aadress."
  value       = pilvio_vm.nextcloud.private_ipv4
}

output "nextcloud_vm_public_ip" {
  description = "Nextcloudi virtuaalmasina avalik IP-aadress (Floating IP)."
  value       = pilvio_floatingip.nextcloud.address
}

output "nextcloud_s3_bucket_name" {
  description = "Loodud S3 bucketi nimi Nextcloudi andmete jaoks."
  value       = pilvio_bucket.nextcloud.name
}

output "nextcloud_login_info" {
  description = "Informatsioon Nextcloudi sisselogimiseks."
  value       = "Ava brauseris http://${pilvio_floatingip.nextcloud.address}/. Kasutaja: ${var.nextcloud_admin_user}, Parool: (määratud .tfvars failis)"
  # Parooli ei tohiks siin otse kuvada, kuna see on tundlik.
  # Kasutaja peab parooli ise .tfvars failist meeles pidama.
}
```

Nüüd peaks sul projekti kaustas olema neli `.tf` laiendiga faili ja üks `.tfvars` fail.

## Samm 2 – Infrastruktuuri loomine Terraformiga

Kui kõik konfiguratsioonifailid on loodud ja `terraform.tfvars` fail on sinu andmetega täidetud, saad alustada infrastruktuuri loomisega.

1. **Ava terminal või käsurida** ja navigeeri oma projekti kausta (kus asuvad `.tf` failid).

2. **Initsialiseeri Terraformi projekt.** See laeb alla vajaliku Pilvio *provideri*.

   ```
   terraform init
   ```

3. **Loo Terraformi plaan (execution plan).** See näitab, milliseid ressursse Terraform kavatseb luua, muuta või kustutada. `-out=tfplan` salvestab plaani faili.

   ```
   terraform plan -out=tfplan
   ```

   Vaata plaani hoolikalt üle, et veenduda, et kõik on ootuspärane.

4. **Rakenda Terraformi plaan.** See käsklus loob reaalselt kõik defineeritud ressursid Pilvio pilves.

   ```
   terraform apply tfplan
   ```

   Terraform küsib kinnitust enne muudatuste tegemist. Sisesta `yes` ja vajuta Enter.

   Infrastruktuuri loomine võib võtta mõned minutid. Terraform kuvab protsessi käigus logisid.

> ✨ **Nõuanne:** Kui `terraform apply` on edukalt lõpule jõudnud, kuvatakse väljundid (`outputs.tf` failis defineeritud), sealhulgas sinu Nextcloudi VM-i avalik IP-aadress. Pane see aadress kindlasti kirja.

## Samm 3 – Nextcloudi esmane kontroll ja seadistamine

Pärast Terraformi edukat käivitamist peaks Nextcloud olema VM-is automaatselt paigaldatud ja käivitatud tänu `cloud-init` skriptile.

1. **Oota mõni minut**, et kõik teenused VM-is korralikult käivituksid. `cloud-init` võib võtta aega, eriti Docker'i ja konteinerite esmakordsel seadistamisel.

2. **Ava veebibrauser** ja sisesta aadressiribale oma Nextcloudi VM-i avalik IP-aadress (mille said Terraformi väljundist, nt `http://SINU_AVALIK_IP`).

3. Sind peaks tervitama Nextcloudi sisselogimisleht või esmase seadistamise viisard. Logi sisse administraatori kasutajanimega (`var.nextcloud_admin_user`) ja parooliga (`var.nextcloud_admin_password`), mille määrasid oma `terraform.tfvars` failis.

4. **Kontrolli S3 ühendust (valikuline, aga soovitatav):**

   - Logi SSH-ga oma Nextcloudi VM-i:

     ```
     ssh VAR_VM_USERNAME@SINU_AVALIK_IP
     ```

     Asenda `VAR_VM_USERNAME` väärtusega, mille määrasid `variables.tf` (vaikimisi `pilvioadmin`) ja `SINU_AVALIK_IP` oma VM-i tegeliku IP-ga. Kasuta parooli, mille määrasid `terraform.tfvars` failis.

   - Kontrolli `docker-compose.yml` faili VM-is (asub `/home/VAR_VM_USERNAME/docker-compose.yml`). Veendu, et `OBJECTSTORE_S3_BUCKET`, `OBJECTSTORE_S3_KEY`, `OBJECTSTORE_S3_SECRET` jms väärtused on korrektsed ja vastavad sinu Pilvio S3 seadetele. Terraform peaks need sinna automaatselt sisestama.

   - Kui tegid muudatusi `docker-compose.yml` failis VM-is, siis taaskäivita Nextcloudi konteinerid:

     ```
     cd /home/VAR_VM_USERNAME/ # Või kus iganes su docker-compose.yml asub
     docker-compose down
     docker-compose up -d
     ```

Kui kõik toimib, on sul nüüd Nextcloud, mis kasutab Pilvio S3-e failide salvestamiseks!

## Samm 4 – Olulised tootmissoovitused ja turvalisus

Selles juhendis loodud Nextcloudi paigaldus on hea alguspunkt, kuid tootmiskeskkonna jaoks on oluline arvestada järgmiste täiendavate sammudega:

- **🔒 HTTPS (SSL/TLS sertifikaat):** Ära kunagi kasuta Nextcloudi tootmises ilma HTTPS-ita! Seadista *reverse proxy* (nt Nginx või Caddy) ja hangi tasuta Let's Encrypt SSL sertifikaat, et kogu liiklus oleks krüpteeritud.
- **🛡️ Tulemüür ja võrgureeglid:** Pilvio portaalis või Terraformiga konfigureeri tulemüüri reeglid nii, et VM-ile oleks avatud ainult vajalikud pordid (tavaliselt 80 HTTP jaoks ja 443 HTTPS jaoks). Piira ligipääs SSH pordile (22) ainult teadaolevatelt IP-aadressidelt.
- **🔑 SSH turvalisus:** Kasuta alati SSH võtmepaariga autentimist paroolipõhise sisselogimise asemel. Keela parooliga sisselogimine SSH konfiguratsioonis (`PasswordAuthentication no` failis `/etc/ssh/sshd_config`).
- **💾 Varundamine:**
  - **Nextcloudi andmebaas:** Seadista regulaarne MariaDB andmebaasi varundamine (nt `mysqldump` abil). Varukoopiad salvesta turvalisse kohta, näiteks teise S3 *bucketi* või eraldi serverisse.
  - **Nextcloudi konfiguratsioonifailid:** Varunda Nextcloudi konfiguratsioonikaust (`config/` Nextcloudi andmekaustas).
  - **S3 Bucket:** Kuigi S3 on vastupidav, kaalu *versioning*'u sisselülitamist oma S3 *bucketis* või *bucketite*ülest replikatsiooni teise regiooni (kui Pilvio seda toetab ja see on vajalik).
- **🔄 Tarkvara uuendamine:** Hoia oma VM-i operatsioonisüsteem, Docker, Docker Compose ja Nextcloudi rakendus (koos selle äppidega) regulaarselt uuendatud, et tagada turvalisus ja saada uusimad funktsioonid.
- **⚙️ Nextcloudi optimeerimine:** Tutvu Nextcloudi dokumentatsiooniga jõudluse optimeerimise ja turvasoovituste osas (nt PHP mälupiirangud, OPCache seadistamine jms).

## Kokkuvõte

Selles juhendis käisime läbi, kuidas automatiseerida Nextcloudi paigaldust Pilvio pilveplatvormile, kasutades Terraformi infrastruktuuri loomiseks ja Docker Compose'i rakenduse enda käivitamiseks. Sa seadistasid virtuaalmasina, võrgu, S3 *bucketi* ja ujuva IP-aadressi ning said toimiva Nextcloudi instantsi, mis kasutab S3 objektisalvestust.

Nüüd on sul olemas tugev alus oma privaatse pilve haldamiseks Pilvios. Ära unusta rakendada tootmiskeskkonna jaoks olulisi turva- ja halduspraktikaid!

Kui sul tekib küsimusi Pilvio teenuste või Terraformi *provideri* kohta, võta julgelt ühendust [Pilvio klienditoega](https://pilvio.com/kontakt) või uuri lähemalt [Pilvio dokumentatsioonist](https://docs.pilvio.com).
