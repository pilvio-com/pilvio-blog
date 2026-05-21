---
title: "Kuidas paigaldada Nextcloud Pilvio pilve Terraformi, Caddy ja S3 objektisalvestusega"
author: "Kaur Kiisler"
publishedAt: "21. mai 2026"
category: "DevOps"
tags: ["Terraform", "Nextcloud", "Pilvio", "S3", "Caddy", "Docker", "DevOps", "StorageVault"]
featured: false
excerpt: "Tootmislähedane ühe VM-i Nextcloudi stack Pilvio peal: Terraform, Caddy automaatse HTTPS-iga, MariaDB, Redis, cron ja S3 primary object storage. Reproduktseeritav, varundatav, uuendatav."
imageUrl: "/media/terraform-nextcloud-pilvio.png"
slug: "terraform-nextcloud-pilvio-prod"
---

# Kuidas paigaldada Nextcloud Pilvio pilve Terraformi, Caddy ja S3 objektisalvestusega

See on praktiline jätk varasemale Terraformi ja Nextcloudi juhendile. Eesmärk ei ole teha minimaalset demo-paigaldust, mis töötab ainult korraks brauseris. Eesmärk on ehitada Pilvio peale tootmislähedane ühe virtuaalmasina Nextcloudi stack, mida on võimalik päriselt hallata, uuendada, varundada ja kasutada ettevõtte dokumendiplatvormi alusena.

Siin juhendis ehitame:

- Pilvio virtuaalmasina;
- Pilvio privaatvõrgu;
- Pilvio StorageVault S3-ühilduva bucket'i;
- avaliku Floating IP;
- Docker Compose stack'i;
- Caddy reverse proxy automaatse HTTPS-iga;
- Nextcloudi ametliku Docker image'i;
- MariaDB andmebaasi;
- Redis cache'i ja faililukkude jaoks;
- eraldi Nextcloud cron konteineri;
- Nextcloudi primary object storage seadistuse Pilvio S3 peale.

See ei ole veel kõrge käideldavusega klaster. Üks VM tähendab endiselt ühte rikkepunkti. Küll aga on see oluliselt lähemal päris paigaldusele kui lihtne `docker run` või ainult HTTP peal töötav Nextcloud.

## Miks see arhitektuur?

Kui Nextcloudi kasutatakse lihtsalt isikliku failikastina, piisab sageli lokaalsest kettast ja lihtsast Docker Compose failist. Kui Nextcloudist saab ettevõtte dokumendiplatvorm ja tulevane AI teadmistekiht, muutuvad oluliseks teised küsimused:

- kus asuvad failid;
- kus asub failide metaandmete tõeallikas;
- kuidas on ligipääsud ja jagamised auditeeritavad;
- kuidas saab paigaldust korrata;
- kuidas varundatakse andmebaasi ja konfiguratsiooni;
- kuidas liigub liiklus kasutaja, reverse proxy, rakenduse ja objektisalvestuse vahel.

Nextcloud + S3 puhul on oluline aru saada ühest põhimõttest: kui S3 on Nextcloudi **primary storage**, siis bucket on Nextcloudi eksklusiivses kasutuses. S3 bucket'is ei ela kasutajale arusaadav kaustapuu koos failinimede ja jagamisõigustega. Failide metaandmed, nimed, kaustad, versioonid ja õigused elavad Nextcloudi andmebaasis. Objektisalvestuses on failisisu sisemiste identifikaatorite järgi.

See on hea arhitektuur siis, kui tahad failisisu skaleeritavalt objektisalvestuses hoida, aga kasutajate, õiguste ja jagamiste tõeallikaks jääb Nextcloud.

## Enne alustamist

Sul on vaja:

1. Pilvio kontot ja arvelduskonto ID-d.
2. Pilvio API võtit.
3. Pilvio StorageVault S3 access key ja secret key.
4. Domeeni, näiteks `cloud.example.ee`.
5. DNS A-kirjet, mis hakkab osutama loodavale Floating IP-le.
6. SSH public key'd.
7. Terraformi.
8. Lokaalselt `aws-cli` või muud S3 klienti testimiseks.

Tootmislähedase paigalduse jaoks ära kasuta Nextcloudi avaliku IP aadressi kaudu. Kasuta domeeni. Caddy saab Let's Encrypt või ZeroSSL sertifikaadi ainult siis, kui domeen osutab sinu serverile ja pordid 80/443 on internetist ligipääsetavad.

## Oluline turvamärkus Terraform state'i kohta

Selles juhendis anname Nextcloudi, MariaDB ja S3 saladused Terraformi muutujate kaudu cloud-init'ile. See tähendab, et need väärtused võivad jõuda Terraform state faili.

See on tavaline Terraformi piirang: `sensitive = true` peidab väärtused käsurea väljundist, aga ei tee state faili maagiliselt saladustevabaks.

Seetõttu:

- ära commiti `terraform.tfstate` faili Git'i;
- ära commiti `terraform.tfvars` faili Git'i;
- hoia state krüpteeritud ja piiratud ligipääsuga asukohas;
- meeskonnatöös kasuta remote state'i;
- production-paigalduse puhul kaalu eraldi secrets management lahendust.

Pilvio StorageVault sobib Terraform remote state'i hoidmiseks, aga tee selle jaoks eraldi bucket. Ära kasuta sama bucket'it Nextcloudi failisisu ja Terraform state'i jaoks.

## Projekti struktuur

Loo lokaalselt uus kaust:

```bash
mkdir pilvio-nextcloud
cd pilvio-nextcloud
mkdir templates
```

Soovitatav failistruktuur:

```text
pilvio-nextcloud/
  versions.tf
  variables.tf
  main.tf
  outputs.tf
  terraform.tfvars
  .gitignore
  templates/
    compose.yaml.tftpl
    Caddyfile.tftpl
    bootstrap-nextcloud.sh.tftpl
```

## `versions.tf`

Pilvio Terraform provideri uusim registry versioon on 1.0.16. Kasutame seda haru ja piirame versiooni `< 2.0.0`, et tulevane major release ei muudaks käitumist ootamatult.

```hcl
terraform {
  required_version = ">= 1.5.0"

  required_providers {
    pilvio = {
      source  = "pilvio-com/pilvio"
      version = ">= 1.0.16, < 2.0.0"
    }
  }

  # Valikuline remote state Pilvio StorageVault'is.
  # Soovitus: hoia Terraform state eraldi bucket'is, mitte Nextcloudi data bucket'is.
  #
  # backend "s3" {
  #   bucket = "minu-tfstate-bucket"
  #   key    = "nextcloud/terraform.tfstate"
  #   region = "eu-west-1"
  #
  #   endpoints = {
  #     s3 = "https://s3.pilw.io"
  #   }
  #
  #   skip_credentials_validation = true
  #   skip_metadata_api_check     = true
  #   skip_requesting_account_id  = true
  #   skip_s3_checksum            = true
  #   use_path_style              = true
  # }
}

provider "pilvio" {
  apikey   = var.apikey
  host     = var.host
  location = var.location
}
```

Kui kasutad Terraform `>= 1.11.2` ja S3 backend annab checksum'iga seotud vea, ekspordi enne `terraform init` käivitamist:

```bash
export AWS_REQUEST_CHECKSUM_CALCULATION=when_required
export AWS_RESPONSE_CHECKSUM_VALIDATION=when_required
```

## `.gitignore`

```gitignore
.terraform/
.terraform.lock.hcl
terraform.tfvars
terraform.tfstate
terraform.tfstate.*
*.tfplan
crash.log
```

`.terraform.lock.hcl` võib meeskonnaga jagatud projektis Git'is olla. Kui tahad provider versioonid täpselt lukustada, ära seda ignoreeri. Kui teed blogi või näidisprojekti, on lihtsam see ignoreerida.

## `variables.tf`

```hcl
variable "apikey" {
  description = "Pilvio API võti."
  type        = string
  sensitive   = true
}

variable "host" {
  description = "Pilvio API endpoint."
  type        = string
  default     = "api.pilvio.com"
}

variable "location" {
  description = "Pilvio asukoht: tll01, jhvi või jhv02."
  type        = string
  default     = "tll01"
}

variable "billing_account_id" {
  description = "Pilvio arvelduskonto ID."
  type        = number
}

variable "domain" {
  description = "Nextcloudi avalik domeen, näiteks cloud.example.ee."
  type        = string
}

variable "admin_email" {
  description = "E-post Caddy ACME konto jaoks."
  type        = string
}

variable "admin_cidr" {
  description = "CIDR, millelt SSH lubada. Tootmises kasuta oma IP/32."
  type        = string
}

variable "ssh_public_key" {
  description = "SSH public key VM-i kasutajale."
  type        = string
}

variable "vm_name" {
  type    = string
  default = "nextcloud-prod"
}

variable "vpc_name" {
  type    = string
  default = "nextcloud-vpc"
}

variable "floatingip_name" {
  type    = string
  default = "nextcloud-ip"
}

variable "vm_username" {
  type    = string
  default = "pilvioadmin"
}

variable "vm_password" {
  description = "Pilvio provider nõuab parooli ka SSH key kasutamisel. Kasuta tugevat juhuslikku parooli."
  type        = string
  sensitive   = true
}

variable "vm_os_name" {
  type    = string
  default = "ubuntu"
}

variable "vm_os_version" {
  type    = string
  default = "24.04"
}

variable "vm_memory" {
  description = "RAM MB. Väikese tiimi jaoks alusta vähemalt 4096 MB-st."
  type        = number
  default     = 4096
}

variable "vm_vcpu" {
  type    = number
  default = 2
}

variable "vm_disk_size" {
  description = "Root ketas GB. Failisisu läheb S3 primary storage'i, aga OS, Docker volume'id ja logid vajavad ruumi."
  type        = number
  default     = 60
}

variable "bucket_name" {
  description = "Nextcloudi primary object storage bucket. Peab olema globaalset unikaalne, lowercase, 3-63 märki."
  type        = string
}

variable "s3_host" {
  type    = string
  default = "s3.pilw.io"
}

variable "s3_region" {
  type    = string
  default = "eu-west-1"
}

variable "s3_access_key" {
  type      = string
  sensitive = true
}

variable "s3_secret_key" {
  type      = string
  sensitive = true
}

variable "nextcloud_admin_user" {
  type    = string
  default = "ncadmin"
}

variable "nextcloud_admin_password" {
  type      = string
  sensitive = true
}

variable "db_password" {
  type      = string
  sensitive = true
}

variable "db_root_password" {
  type      = string
  sensitive = true
}

variable "nextcloud_image" {
  description = "Pin'i major versioon. Ära kasuta productionis latest tag'i."
  type        = string
  default     = "nextcloud:33-apache"
}

variable "mariadb_image" {
  type    = string
  default = "mariadb:11.4"
}

variable "redis_image" {
  type    = string
  default = "redis:7-alpine"
}

variable "caddy_image" {
  type    = string
  default = "caddy:2"
}

variable "timezone" {
  type    = string
  default = "Europe/Tallinn"
}

variable "default_phone_region" {
  description = "Nextcloudi telefoninumbrite vaikeriik."
  type        = string
  default     = "EE"
}
```

## `terraform.tfvars`

Ära lisa seda faili Git'i.

```hcl
apikey             = "PILVIO_API_KEY"
billing_account_id = 12345

domain      = "cloud.example.ee"
admin_email = "admin@example.ee"
admin_cidr  = "203.0.113.10/32"

ssh_public_key = "ssh-ed25519 AAAA... sinu@arvuti"

vm_password = "pikk-juhuslik-vm-parool"

bucket_name = "example-nextcloud-prod-data"

s3_access_key = "PILVIO_S3_ACCESS_KEY"
s3_secret_key = "PILVIO_S3_SECRET_KEY"

nextcloud_admin_user     = "ncadmin"
nextcloud_admin_password = "pikk-juhuslik-nextcloud-admin-parool"

db_password      = "pikk-juhuslik-db-parool"
db_root_password = "pikk-juhuslik-db-root-parool"
```

Kui sul ei ole enda avalikku IP-d teada, saad selle näiteks nii:

```bash
curl https://ifconfig.me
```

Pane tulemuseks saadud IP `admin_cidr` väärtuseks kujul `x.x.x.x/32`.

## `main.tf`

Pilvio Terraform provider ei sisalda praeguse dokumentatsiooni järgi eraldi firewall resource'i. Seetõttu seadistame VM-is UFW reeglid cloud-init'iga ja lisame tootmismärkusena Pilvio perimeter firewall'i portaalis või API kaudu.

Oluline: Docker ja hostipõhine tulemüür vajavad alati tähelepanu, sest Docker haldab ise iptables reegleid. Selles stack'is avaldame hostile ainult Caddy pordid 80 ja 443. MariaDB, Redis ja Nextcloudi app ei ole hosti portidena avatud.

```hcl
locals {
  caddy_ip        = "172.30.0.10"
  frontend_subnet = "172.30.0.0/24"
  nextcloud_url   = "https://${var.domain}"

  compose_yaml = templatefile("${path.module}/templates/compose.yaml.tftpl", {
    domain          = var.domain
    nextcloud_image = var.nextcloud_image
    mariadb_image   = var.mariadb_image
    redis_image     = var.redis_image
    caddy_image     = var.caddy_image
    bucket_name     = var.bucket_name
    s3_host         = var.s3_host
    s3_region       = var.s3_region
    timezone        = var.timezone
    caddy_ip        = local.caddy_ip
    frontend_subnet = local.frontend_subnet
    nextcloud_url   = local.nextcloud_url
  })

  caddyfile = templatefile("${path.module}/templates/Caddyfile.tftpl", {
    domain      = var.domain
    admin_email = var.admin_email
  })

  bootstrap_script = templatefile("${path.module}/templates/bootstrap-nextcloud.sh.tftpl", {
    vm_username          = var.vm_username
    admin_cidr           = var.admin_cidr
    default_phone_region = var.default_phone_region
  })
}

resource "pilvio_vpc" "nextcloud" {
  name     = var.vpc_name
  location = var.location
}

resource "pilvio_bucket" "nextcloud" {
  name               = var.bucket_name
  billing_account_id = var.billing_account_id
}

resource "pilvio_floatingip" "nextcloud" {
  name               = var.floatingip_name
  billing_account_id = var.billing_account_id
  location           = var.location
}

resource "pilvio_vm" "nextcloud" {
  name               = var.vm_name
  os_name            = var.vm_os_name
  os_version         = var.vm_os_version
  memory             = var.vm_memory
  vcpu               = var.vm_vcpu
  username           = var.vm_username
  password           = var.vm_password
  public_key         = var.ssh_public_key
  disks              = var.vm_disk_size
  billing_account_id = var.billing_account_id
  location           = var.location
  network_uuid       = pilvio_vpc.nextcloud.uuid
  backup             = true

  cloud_init = jsonencode({
    write_files = [
      {
        path        = "/opt/nextcloud/compose.yaml"
        owner       = "root:root"
        permissions = "0640"
        content     = local.compose_yaml
      },
      {
        path        = "/opt/nextcloud/Caddyfile"
        owner       = "root:root"
        permissions = "0644"
        content     = local.caddyfile
      },
      {
        path        = "/usr/local/sbin/bootstrap-nextcloud.sh"
        owner       = "root:root"
        permissions = "0750"
        content     = local.bootstrap_script
      },
      {
        path        = "/opt/nextcloud/secrets/db_password"
        owner       = "root:root"
        permissions = "0600"
        content     = var.db_password
      },
      {
        path        = "/opt/nextcloud/secrets/db_root_password"
        owner       = "root:root"
        permissions = "0600"
        content     = var.db_root_password
      },
      {
        path        = "/opt/nextcloud/secrets/nextcloud_admin_user"
        owner       = "root:root"
        permissions = "0600"
        content     = var.nextcloud_admin_user
      },
      {
        path        = "/opt/nextcloud/secrets/nextcloud_admin_password"
        owner       = "root:root"
        permissions = "0600"
        content     = var.nextcloud_admin_password
      },
      {
        path        = "/opt/nextcloud/secrets/s3_access_key"
        owner       = "root:root"
        permissions = "0600"
        content     = var.s3_access_key
      },
      {
        path        = "/opt/nextcloud/secrets/s3_secret_key"
        owner       = "root:root"
        permissions = "0600"
        content     = var.s3_secret_key
      },
      {
        path        = "/etc/ssh/sshd_config.d/99-disable-password-auth.conf"
        owner       = "root:root"
        permissions = "0644"
        content     = <<-EOT
        PasswordAuthentication no
        KbdInteractiveAuthentication no
        ChallengeResponseAuthentication no
        EOT
      }
    ]

    runcmd = [
      "bash /usr/local/sbin/bootstrap-nextcloud.sh"
    ]
  })
}

resource "pilvio_floatingip_assignment" "nextcloud" {
  depends_on  = [pilvio_vm.nextcloud]
  address     = pilvio_floatingip.nextcloud.address
  assigned_to = pilvio_vm.nextcloud.uuid
  location    = var.location
}
```

## `templates/compose.yaml.tftpl`

Selles Compose failis:

- ainult Caddy avaldab hostile pordid 80 ja 443;
- Nextcloudi app on Caddy taga;
- MariaDB ja Redis on ainult sisemisel Docker võrgul;
- saladused loetakse failidest `/run/secrets/...`;
- S3 seadistus läheb Nextcloudi primary object storage konfiguratsiooniks;
- Nextcloudi cron töötab eraldi konteinerina.

```yaml
services:
  caddy:
    image: ${caddy_image}
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
      - "443:443/udp"
    volumes:
      - ./Caddyfile:/etc/caddy/Caddyfile:ro
      - caddy_data:/data
      - caddy_config:/config
    networks:
      frontend:
        ipv4_address: ${caddy_ip}
    depends_on:
      - app

  db:
    image: ${mariadb_image}
    restart: unless-stopped
    command: --transaction-isolation=READ-COMMITTED --binlog-format=ROW
    environment:
      MARIADB_DATABASE: nextcloud
      MARIADB_USER: nextcloud
      MARIADB_PASSWORD_FILE: /run/secrets/db_password
      MARIADB_ROOT_PASSWORD_FILE: /run/secrets/db_root_password
      TZ: ${timezone}
    volumes:
      - db_data:/var/lib/mysql
    secrets:
      - db_password
      - db_root_password
    networks:
      - backend

  redis:
    image: ${redis_image}
    restart: unless-stopped
    networks:
      - backend

  app:
    image: ${nextcloud_image}
    restart: unless-stopped
    depends_on:
      - db
      - redis
    environment:
      MYSQL_HOST: db
      MYSQL_DATABASE: nextcloud
      MYSQL_USER: nextcloud
      MYSQL_PASSWORD_FILE: /run/secrets/db_password

      NEXTCLOUD_ADMIN_USER_FILE: /run/secrets/nextcloud_admin_user
      NEXTCLOUD_ADMIN_PASSWORD_FILE: /run/secrets/nextcloud_admin_password
      NEXTCLOUD_TRUSTED_DOMAINS: ${domain}

      TRUSTED_PROXIES: ${caddy_ip}
      OVERWRITEPROTOCOL: https
      OVERWRITECLIURL: ${nextcloud_url}

      REDIS_HOST: redis

      OBJECTSTORE_S3_HOST: ${s3_host}
      OBJECTSTORE_S3_BUCKET: ${bucket_name}
      OBJECTSTORE_S3_KEY_FILE: /run/secrets/s3_access_key
      OBJECTSTORE_S3_SECRET_FILE: /run/secrets/s3_secret_key
      OBJECTSTORE_S3_PORT: 443
      OBJECTSTORE_S3_SSL: "true"
      OBJECTSTORE_S3_REGION: ${s3_region}
      OBJECTSTORE_S3_AUTOCREATE: "false"
      OBJECTSTORE_S3_USEPATH_STYLE: "true"

      PHP_MEMORY_LIMIT: 1024M
      PHP_UPLOAD_LIMIT: 10G
      TZ: ${timezone}
    volumes:
      - nextcloud_html:/var/www/html
    secrets:
      - db_password
      - nextcloud_admin_user
      - nextcloud_admin_password
      - s3_access_key
      - s3_secret_key
    networks:
      - frontend
      - backend

  cron:
    image: ${nextcloud_image}
    restart: unless-stopped
    entrypoint: /cron.sh
    depends_on:
      - db
      - redis
      - app
    environment:
      MYSQL_HOST: db
      MYSQL_DATABASE: nextcloud
      MYSQL_USER: nextcloud
      MYSQL_PASSWORD_FILE: /run/secrets/db_password
      REDIS_HOST: redis
      TZ: ${timezone}
    volumes:
      - nextcloud_html:/var/www/html
    secrets:
      - db_password
    networks:
      - backend

volumes:
  db_data:
  nextcloud_html:
  caddy_data:
  caddy_config:

secrets:
  db_password:
    file: ./secrets/db_password
  db_root_password:
    file: ./secrets/db_root_password
  nextcloud_admin_user:
    file: ./secrets/nextcloud_admin_user
  nextcloud_admin_password:
    file: ./secrets/nextcloud_admin_password
  s3_access_key:
    file: ./secrets/s3_access_key
  s3_secret_key:
    file: ./secrets/s3_secret_key

networks:
  frontend:
    ipam:
      config:
        - subnet: ${frontend_subnet}
  backend:
    internal: true
```

## `templates/Caddyfile.tftpl`

Caddy teeb siin kolm asja:

- võtab automaatselt TLS sertifikaadi;
- suunab HTTP liikluse HTTPS peale;
- proxy'b päringud Nextcloudi `app:80` teenusele.

Lisaks lisame Nextcloudi jaoks vajalikud CalDAV ja CardDAV `.well-known` redirect'id.

```caddyfile
{
  email ${admin_email}
}

${domain} {
  encode zstd gzip

  redir /.well-known/carddav /remote.php/dav/ 301
  redir /.well-known/caldav /remote.php/dav/ 301

  header Strict-Transport-Security "max-age=15552000; includeSubDomains"

  reverse_proxy app:80 {
    header_up X-Forwarded-Proto {scheme}
    header_up X-Forwarded-Host {host}
    header_up X-Real-IP {remote_host}
  }
}
```

## `templates/bootstrap-nextcloud.sh.tftpl`

See skript paigaldab Docker Engine'i ametliku Docker APT repo kaudu, mitte vana `apt-key` meetodiga. Samuti käivitab see UFW, lubab avalikult ainult 80/443 ning piirab SSH sinu `admin_cidr` väärtusele.

```bash
#!/usr/bin/env bash
set -euo pipefail

export DEBIAN_FRONTEND=noninteractive

apt-get update
apt-get install -y ca-certificates curl gnupg ufw unattended-upgrades

install -m 0755 -d /etc/apt/keyrings
if [ ! -f /etc/apt/keyrings/docker.asc ]; then
  curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
  chmod a+r /etc/apt/keyrings/docker.asc
fi

. /etc/os-release
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $VERSION_CODENAME stable" \
  > /etc/apt/sources.list.d/docker.list

apt-get update
apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

usermod -aG docker ${vm_username}

systemctl enable --now docker

ufw default deny incoming
ufw default allow outgoing
ufw allow 80/tcp
ufw allow 443/tcp
ufw limit from ${admin_cidr} to any port 22 proto tcp
ufw --force enable

systemctl reload ssh || systemctl reload sshd || true

chmod 700 /opt/nextcloud/secrets
chmod 600 /opt/nextcloud/secrets/*
chown -R root:root /opt/nextcloud

cd /opt/nextcloud
docker compose pull
docker compose up -d

for i in $(seq 1 60); do
  if docker compose exec -T -u www-data app php occ status >/dev/null 2>&1; then
    break
  fi
  sleep 5
done

docker compose exec -T -u www-data app php occ background:cron || true
docker compose exec -T -u www-data app php occ config:system:set default_phone_region --value="${default_phone_region}" || true
docker compose exec -T -u www-data app php occ maintenance:update:htaccess || true
```

## `outputs.tf`

```hcl
output "nextcloud_public_ip" {
  value = pilvio_floatingip.nextcloud.address
}

output "nextcloud_url" {
  value = "https://${var.domain}"
}

output "ssh_command" {
  value = "ssh ${var.vm_username}@${pilvio_floatingip.nextcloud.address}"
}

output "bucket_name" {
  value = pilvio_bucket.nextcloud.name
}
```

## Käivitamine

Kõigepealt vorminda ja valideeri:

```bash
terraform fmt
terraform init
terraform validate
```

Seejärel vaata plaan üle:

```bash
terraform plan -out=tfplan
```

Kui kõik on korrektne:

```bash
terraform apply tfplan
```

Kui Floating IP on loodud, lisa või uuenda DNS A-kirje:

```text
cloud.example.ee  A  <terraform output nextcloud_public_ip>
```

Kui DNS ei olnud enne Caddy käivitumist valmis, võib Caddy logides alguses ACME vigu näha. See on normaalne. Kui DNS levib ja pordid 80/443 on avatud, proovib Caddy sertifikaadi uuesti hankida.

## Esmane kontroll serveris

Logi serverisse:

```bash
ssh pilvioadmin@<floating-ip>
```

Kontrolli stack'i:

```bash
cd /opt/nextcloud
docker compose ps
```

Kontrolli Caddy logisid:

```bash
docker compose logs -f caddy
```

Kontrolli Nextcloudi:

```bash
docker compose exec -u www-data app php occ status
docker compose exec -u www-data app php occ config:system:get overwrite.cli.url
docker compose exec -u www-data app php occ config:system:get trusted_domains
docker compose exec -u www-data app php occ config:system:get objectstore
```

Kui kõik on korras, ava:

```text
https://cloud.example.ee
```

## S3 ühenduse kontroll

Laadi Nextcloudi veebiliidesest üles väike testfail. Seejärel kontrolli oma arvutist või serverist S3 bucket'i sisu.

AWS CLI näide:

```bash
aws configure --profile pilvio

aws --profile pilvio \
  --endpoint-url https://s3.pilw.io \
  s3 ls s3://example-nextcloud-prod-data --recursive --summarize
```

Ära oota, et näed seal kasutajasõbralikke failinimesid. Primary object storage puhul hoiab Nextcloud failisisu objektidena ning failipuu metaandmed on andmebaasis.

## Backup: S3 bucket üksi ei ole backup

See on kriitiline. Kui kasutad S3 primary storage'it, siis Nextcloudi failisisu on bucket'is, aga ilma andmebaasita ei ole sul kasutajate failipuu, jagamiste ja metaandmete terviklikku pilti.

Varunda vähemalt:

- MariaDB andmebaas;
- Docker volume `nextcloud_html`, sest seal on Nextcloudi config, apps ja custom_apps;
- `/opt/nextcloud/secrets`;
- Caddy volume'id, eriti `caddy_data`, kus on ACME konto ja sertifikaatide info;
- Terraform state;
- S3 bucket'i sisu või versioonid.

Lihtne andmebaasi dump serveris:

```bash
sudo mkdir -p /var/backups/nextcloud
cd /opt/nextcloud

docker compose exec -T db mariadb-dump \
  -u root \
  --password="$(sudo cat /opt/nextcloud/secrets/db_root_password)" \
  --single-transaction \
  nextcloud \
  | gzip > "/var/backups/nextcloud/nextcloud-db-$(date +%F).sql.gz"
```

Selle faili võid omakorda kopeerida eraldi StorageVault bucket'isse:

```bash
aws --profile pilvio \
  --endpoint-url https://s3.pilw.io \
  s3 cp /var/backups/nextcloud/nextcloud-db-YYYY-MM-DD.sql.gz \
  s3://minu-nextcloud-backups/db/
```

Tootmises tee sellest systemd timer või muu regulaarne backup töö ning testi taastamist. Backup, mida pole taastatud, on oletus.

## Uuendamine

Ära kasuta productionis `nextcloud:latest` tag'i. Selles juhendis on image `nextcloud:33-apache`. See lukustab Nextcloudi major versiooni, aga lubab sama majori patch uuendusi.

Uuendamiseks:

```bash
cd /opt/nextcloud
docker compose pull
docker compose up -d
docker compose exec -u www-data app php occ status
```

Nextcloudi major versioone uuendatakse üks major korraga. Näiteks 33 -> 34 on eraldi otsus, mitte juhuslik `latest` tõmme. Enne major upgrade'i tee andmebaasi dump ja veendu, et backup on taastatav.

## Mida see juhend teadlikult ei lahenda

Tootmislähedane ühe VM-i paigaldus ei tähenda veel täielikku enterprise HA arhitektuuri.

Suurema kasutuse puhul tuleb juurde mõelda:

- eraldi andmebaasiserver või hallatud andmebaas;
- eraldi Redis;
- mitu Nextcloudi app node'i;
- objektisalvestuse versioonimine ja lifecycle poliitikad;
- serveripoolne seire ja alerting;
- logide keskne kogumine;
- SMTP seadistus teavituste jaoks;
- viirusetõrje failidele;
- Collabora või OnlyOffice;
- välise identiteedi sidumine LDAP/SAML/OIDC kaudu;
- regulaarne restore-drill.

Pilvio provider ei eksponeeri praeguse avaliku dokumentatsiooni järgi firewall resource'i. Seetõttu on mõistlik lisada Pilvio perimeter firewall portaalis või API kaudu eraldi, nii et:

- 80/tcp ja 443/tcp on avalikud;
- 22/tcp on lubatud ainult admin IP-lt;
- andmebaasi ja Redis portid ei ole avalikud.

## Kokkuvõte

Selline paigaldus annab praktilise aluse Nextcloud + S3 arhitektuurile Pilvios:

- Terraform loob korratava infrastruktuuri;
- Caddy annab automaatse HTTPS-i;
- Nextcloud kasutab Pilvio StorageVaulti primary object storage'ina;
- Redis ja cron katavad tavapärased Nextcloudi tootmisvajadused;
- andmebaas ja cache ei ole avalikult internetis;
- S3 bucket jääb failisisu kihiks, Nextcloud jääb õiguste ja metaandmete tõeallikaks.

See on hea lähtekoht, kui tahad ettevõtte dokumente hoida enda kontrollitavas pilvekeskkonnas ja hiljem ehitada nende peale otsingu, RAG-i või MCP-põhise AI ligipääsukihi.

## Kontrollitud allikad

- Pilvio Terraform provider: https://registry.terraform.io/providers/pilvio-com/pilvio/latest
- Pilvio Terraform provider GitHub: https://github.com/pilvio-com/terraform-provider-pilvio
- Pilvio StorageVault: https://pilvio.com/et/s3-objektisalvestus-eestis/
- Pilvio API docs: https://api.pilvio.com/
- Nextcloud Docker image: https://hub.docker.com/_/nextcloud/
- Nextcloud Docker GitHub: https://github.com/nextcloud/docker
- Nextcloud primary object storage: https://docs.nextcloud.com/server/latest/admin_manual/configuration_files/primary_storage.html
- Nextcloud reverse proxy configuration: https://docs.nextcloud.com/server/stable/admin_manual/configuration_server/reverse_proxy_configuration.html
- Nextcloud release schedule: https://github.com/nextcloud/server/wiki/Maintenance-and-Release-Schedule
- Docker Engine Ubuntu install: https://docs.docker.com/engine/install/ubuntu/
- Caddy Automatic HTTPS: https://caddyserver.com/docs/automatic-https
- Caddy reverse_proxy directive: https://caddyserver.com/docs/caddyfile/directives/reverse_proxy
- Terraform S3 backend: https://developer.hashicorp.com/terraform/language/backend/s3
