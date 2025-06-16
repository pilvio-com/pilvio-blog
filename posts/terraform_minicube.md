---
title: "Õpetus: Pilviosse Terraformi abil Minikube Kubernetes klastri paigaldamine"           # Required: Post title
author: "Kaur Kiisler"                   # Required: Author name
publishedAt: "16. juuni 2025"        # Required: Estonian format date
category: "Õpetused"                  # Required: Post category
tags: ["kubernetes", "devops", "terraform"]          # Required: Array of tags
featured: false                         # Required: Boolean (only one post should be true)
excerpt: "Selles juhendis kasutad sa Terraformi, et automaatselt ette valmistada turvaline ja ajakohane Ubuntu server, paigaldada Docker, Docker Compose, `kubectl` ja Minikube."         # Optional: Custom excerpt (auto-generated if not provided)
imageUrl: "/media/tf_minicube.png" # Optional: Featured image URL
slug: "minikube-terraform"                    # Optional: Custom URL slug (auto-generated from title if not provided)
---
### Õpetus: Pilviosse Terraformi abil Minikube Kubernetes klastri paigaldamine

Minikube on lihtsaim viis Kubernetesega kohalikult katsetamiseks. Pilvio pilves saad sa seda töökindluse, võrgu- ja salvestusvõimaluste paindlikkuse suurendamiseks käivitada reaalses virtuaalmasinas. Selles juhendis kasutad sa Terraformi, et automaatselt ette valmistada turvaline ja ajakohane Ubuntu server, paigaldada Docker, Docker Compose, `kubectl` ja Minikube ning soovi korral määrata avalik ujuv IP-aadress ja S3-*bucket* oma varade või varukoopiate jaoks.

Selle õpetuse lõpuks on sul arenduseks või testimiseks valmis Minikube'i host, mis on täielikult korratav ja versioonihallatud tänu taristu-kui-kood (infrastructure-as-code) lähenemisele.

### Eeldused

- Pilvio pilve konto koos API-võtme ja arvelduskonto ID-ga
- Terraform 1.3 või uuem versioon
- SSH võtmepaar turvaliseks ligipääsuks virtuaalmasinale

### 1. Samm: Valmista ette oma Terraformi projekt

Loo uus töökataloog ja lisa sinna järgmised failid.

#### 1.1. `versions.tf`

See fail määratleb Terraformi ja Pilvio *provider*'i versioonid.

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
  host     = var.host
  location = var.location
}

# Eemalda kommentaar kaughalduse jaoks:
# backend "s3" {
#   bucket = "minu-terraform-state"
#   key    = "pilvio-nait/terraform.tfstate"
#   region = "eu-west-1"
# }
```

#### 1.2. `variables.tf`

Muuda kõik olulised seaded parameetriteks (API-võtmed, VM-i spetsifikatsioonid, ressursside nimed jne).

Terraform

```
variable "apikey" {
  description = "Pilvio API key"
  type        = string
  sensitive   = true
}

variable "host" {
  description = "Pilvio API endpoint"
  type        = string
  default     = "api.pilvio.com"
}

variable "location" {
  description = "Pilvio resource location (tll01, jhvi, jhv02)"
  type        = string
  default     = "tll01"
}

variable "billing_account_id" {
  description = "Billing account ID to assign resources"
  type        = number
}

variable "vpc_name" {
  description = "Name of the VPC"
  type        = string
  default     = "k8s-vpc"
}

variable "vm_name" {
  description = "Name of the Kubernetes VM"
  type        = string
  default     = "minikube-host"
}

variable "vm_username" {
  description = "Admin username for the VM"
  type        = string
  default     = "ubuntu"
}

variable "vm_password" {
  description = "Admin password for the VM"
  type        = string
  sensitive   = true
}

variable "vm_public_key" {
  description = "Optional SSH public key for the VM"
  type        = string
  default     = ""
}

variable "vm_os_name" {
  description = "VM OS (use 'ubuntu')"
  type        = string
  default     = "ubuntu"
}

variable "vm_os_version" {
  description = "VM OS version (use '22.04' for Ubuntu LTS)"
  type        = string
  default     = "22.04"
}

variable "vm_vcpu" {
  description = "Number of vCPUs for the VM"
  type        = number
  default     = 2
}

variable "vm_memory" {
  description = "RAM for the VM in MB"
  type        = number
  default     = 4096
}

variable "vm_disk" {
  description = "Primary disk size in GB"
  type        = number
  default     = 40
}

variable "enable_floating_ip" {
  description = "Whether to allocate and assign a public floating IP"
  type        = bool
  default     = true
}

variable "bucket_name" {
  description = "Name for S3-compatible bucket (leave empty to skip creation)"
  type        = string
  default     = ""
}
```

#### 1.3. `main.tf`

Defineeri kogu põhiinfrastruktuur: VPC, VM, valikuline ujuv IP-aadress ja S3-*bucket*.

Terraform

```
resource "pilvio_vpc" "main" {
  name     = var.vpc_name
  location = var.location
}

resource "pilvio_vm" "minikube_host" {
  name         = var.vm_name
  os_name      = var.vm_os_name
  os_version   = var.vm_os_version
  memory       = var.vm_memory
  vcpu         = var.vm_vcpu
  username     = var.vm_username
  password     = var.vm_password
  disks        = var.vm_disk
  location     = var.location
  network_uuid = pilvio_vpc.main.uuid
  public_key   = var.vm_public_key != "" ? var.vm_public_key : null

  # cloud-init paigaldab Docker, docker-compose, kubectl, minikube
  cloud_init = jsonencode({
    packages = [
      "docker.io",
      "docker-compose",
      "curl",
      "apt-transport-https",
      "ca-certificates"
    ]
    runcmd = [
      "systemctl enable docker",
      "systemctl start docker",
      "usermod -aG docker ${var.vm_username}",
      "curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | gpg --dearmor -o /etc/apt/trusted.gpg.d/kubernetes.gpg",
      "echo 'deb https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' > /etc/apt/sources.list.d/kubernetes.list",
      "apt-get update",
      "apt-get install -y kubectl",
      "curl -Lo /usr/local/bin/minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64",
      "chmod +x /usr/local/bin/minikube"
    ]
  })
  billing_account_id = var.billing_account_id
}

resource "pilvio_floatingip" "k8s_ip" {
  count              = var.enable_floating_ip ? 1 : 0
  billing_account_id = var.billing_account_id
  location           = var.location
  name               = "${var.vm_name}-public"
}

resource "pilvio_floatingip_assignment" "k8s_ip_assign" {
  count       = var.enable_floating_ip ? 1 : 0
  address     = pilvio_floatingip.k8s_ip[0].address
  assigned_to = pilvio_vm.minikube_host.uuid
  location    = var.location
}

resource "pilvio_bucket" "assets" {
  count              = length(var.bucket_name) > 0 ? 1 : 0
  name               = var.bucket_name
  billing_account_id = var.billing_account_id
}
```

#### 1.4. `outputs.tf`

Kuva olulised väljundid hilisemaks kasutamiseks.

Terraform

```
output "vm_id" {
  description = "Kubernetes VM-i ID"
  value       = pilvio_vm.minikube_host.id
}
output "vm_private_ip" {
  description = "Kubernetes VM-i privaatne IPv4"
  value       = pilvio_vm.minikube_host.private_ipv4
}
output "vm_public_ip" {
  description = "Avalik ujuv IP-aadress (kui on lubatud)"
  value       = var.enable_floating_ip ? pilvio_floatingip.k8s_ip[0].address : null
}
output "bucket_name" {
  description = "Loodud *bucketi* nimi (kui on loodud)"
  value       = length(var.bucket_name) > 0 ? pilvio_bucket.assets[0].name : null
}
output "vpc_uuid" {
  description = "VPC UUID"
  value       = pilvio_vpc.main.uuid
}
```

#### 1.5. `terraform.tfvars`

Sisesta siia oma tegelikud väärtused (ära kunagi lisa salajasi andmeid versioonihaldusesse!).

Terraform

```
apikey              = "ASENDA_MIND_päris_api_võtmega"
billing_account_id  = 123456
vm_password         = "ASENDA_MIND_turvalise_parooliga"
vm_public_key       = "ssh-rsa AAAAB3...sinu_võti_siin"
host                = "api.pilvio.com"
location            = "tll01"
vpc_name            = "k8s-vpc"
vm_name             = "minikube-host"
bucket_name         = "pilvio-minikube-assets"
enable_floating_ip  = true
```

### 2. Samm: Käivita ja rakenda Terraform

Käivita oma projekti kaustas järgmised käsud:

Bash

```
terraform init
terraform plan -out=tfplan
terraform apply tfplan
```

Terraform kuvab väljundis sinu virtuaalmasina IP-aadressid ja S3-*bucketi* nime.

### 3. Samm: Ühendu oma Minikube'i hostiga

Logi oma virtuaalmasinasse SSH-ga (kasuta ujuvat IP-aadressi, kui see on lubatud):

Bash

```
ssh admin@SINU_AVALIK_IP
```

Docker, Docker Compose, `kubectl` ja Minikube on juba paigaldatud. Saad käivitada Minikube'i (`none` draiveriga, mis sobib ühe masina lokaalse klastri jaoks):

Bash

```
sudo minikube start --driver=none
```

**Nõuanne:** Kui sa soovid, et sinu kasutaja saaks Dockerit ja Minikube'i käivitada ilma `sudo`'ta, logi välja ja uuesti sisse või käivita käsk `newgrp docker`.

### 4. Samm: Kasuta Kubernetes't!

Sul on nüüd reaalne virtuaalmasin, mis käitab Minikube Kubernetes klastrit ja mida hallatakse täielikult Terraformi abil. Saad paigaldada rakendusi, katsetada ja integreerida S3-salvestusruumi (kui S3-*bucket* loodi).

### Koristamine

Kui oled lõpetanud, hävita kõik loodud ressursid:

Bash

```
terraform destroy
```

### Kokkuvõte

Selle töövoo abil saad sa usaldusväärselt ja kiiresti käivitada kasutusvalmis Minikube'i keskkonna Pilvio pilves, automatiseerida kogu taristu ning kiiresti kopeerida keskkondi arenduseks, testimiseks või esitlusteks – ilma ühegi käsitsi sammuta. Proovi muuta muutujaid või skaleerida lahendust keerukamate Kubernetes'e stsenaariumite jaoks!

Head kodeerimist Pilvios! 🚀
