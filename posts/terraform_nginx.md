---
title: "Terraform Pilvio näitel: seame üles Nginx-i"           # Required: Post title
author: "Kaur Kiisler"                   # Required: Author name
publishedAt: "4. juuni 2025"        # Required: Estonian format date
category: "Terraform"                  # Required: Post category
tags: ["terraform", "devops"]          # Required: Array of tags
featured: false                         # Required: Boolean (only one post should be true)
readingTime: "8 min"                   # Optional: Reading time (auto-calculated if not provided)
imageUrl: "https://example.com/img.jpg" # Optional: Featured image URL
---
# Juhend: Õpime tundma Terraformi Pilvio näitel ja seame üles Nginx veebiserveri

## Sissejuhatus

Pilveteenused ja nende haldamine on tänapäeva IT-maailma lahutamatu osa. Eestis on mitmeid platvorme, mis pakuvad virtuaalserverite haldamist, kuid sageli puudub neil võimalus taristut hallata kaasaegsete, API-põhiste tööriistadega nagu Terraform. See piirab DevOps-praktikate rakendamist ja automatiseerimist.

**Pilvio** on Eestis esimene pilveteenusepakkuja, mis on ehitatud API-esmalt põhimõttel, võimaldades sul hallata ressursse (virtuaalvõrgud, serverid jms) sarnaselt suurtele globaalsetele pilveplatvormidele nagu AWS või Google Cloud. Tänu sellele API-le saab Pilvio peale ehitada tarkvarakihte, mis võimaldavad kasutada levinud DevOps tööriistu, sealhulgas **Terraformi**.

Selles juhendis näitame, kuidas Terraformi abil Pilvio platvormil lihtne Nginx veebiserver püsti panna. See on suurepärane esimene samm Terraformi ja Pilvio võimaluste tundmaõppimiseks. Kõik Pilvio kasutajad saavad alustamiseks ka tasuta krediiti, millega seda ja teisi juhendeid proovida.

### Miks Terraform?

Terraform on HashiCorpi loodud avatud lähtekoodiga tööriist, mis võimaldab sul defineerida ja provisioneerida taristut koodina (Infrastructure as Code - IaC). Selle asemel, et käsitsi klikkida veebiliidestes või kirjutada keerukaid skripte, kirjeldad sa oma soovitud taristu deklaratiivsetes konfiguratsioonifailides.

**Terraformi peamised eelised:**

- **Taristu koodina (IaC):** Kogu sinu serverite, võrkude, salvestusruumi jms konfiguratsioon on kirjas failides. Neid faile saab versioonihallata (nt Gitiga), jagada ja korduvalt kasutada.
- **Täitmise plaanid (Execution Plans):** Enne muudatuste tegemist genereerib Terraform plaani, mis näitab täpselt, milliseid ressursse luuakse, muudetakse või kustutatakse. See annab sulle täieliku kontrolli ja aitab vältida ootamatusi.
- **Ressursside graaf (Resource Graph):** Terraform ehitab sinu ressurssidest sõltuvuste graafi ja haldab neid paralleelselt ning efektiivselt.
- **Muutuste automatiseerimine:** Korduvad ülesanded, nagu testkeskkondade loomine või rakenduste skaleerimine, muutuvad lihtsaks ja kiireks.
- **Olekuhaldus (State Management):** Terraform hoiab alles sinu hallatava taristu hetkeseisu. See võimaldab Terraformil teada, millised reaalsed ressursid vastavad sinu konfiguratsioonile ja kuidas neid uuendada. Kui keegi kustutab käsitsi Terraformiga loodud serveri, püüab Terraform selle vastavalt olemasolevale konfiguratsioonile ja seisundifailile taastada.

Erinevalt lihtsatest skriptidest, mis võivad olla ettearvamatud ja mille puhul on raske jälgida taristu tegelikku seisu, pakub Terraform robustset ja usaldusväärset viisi pilveressursside haldamiseks.

## Eeldused

See juhend on loodud olema võimalikult lihtne ja sobib hästi ka algajatele, kes Terraformiga alles tutvust teevad. Enne alustamist veendu, et sul on olemas järgmised asjad:

- **Pilvio kasutajakonto:** Kui sul veel kontot pole, saad selle luua aadressil https://pilvio.com/.
- **Pilvio API võti:** Logi sisse oma Pilvio kontole. Vasakul menüüribal vali "Access" ja genereeri sealt uus API võti. Hoia see võti turvaliselt alles!
- **Pilvio arvelduskonto ID (`billing_account_id`):** Selle leiad Pilvio portaalist, minnes menüüvalikusse "Billing", klikkides oma maksevahendile ja leides sealt "Account ID" väärtuse.
- **Terraform CLI paigaldatud:** Kui sul Terraformi veel pole, järgi paigaldusjuhiseid [Terraformi ametlikul veebilehel](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli).

## Samm 1 – Terraformi projekti algväärtustamine

Alustame Terraformi projekti ülesseadmisega. Loo oma arvutisse uus kaust projekti jaoks (nt `pilvio-terraform-nginx`) ja liigu käsureal sellesse kausta.

### 1. Loo fail `variables.tf`

Selles failis deklareerime muutujad, mida meie Terraformi konfiguratsioonides kasutame. Esialgu vajame muutujaid Pilvio API võtme ja arvelduskonto ID jaoks.

Loo fail nimega `variables.tf` järgmise sisuga:

```
variable "pilvio" {
  type = object({
    api_key            = string
    billing_account_id = number
  })
  description = "Pilvio konto andmed: API võti ja arvelduskonto ID."
  sensitive   = true # Märgib, et see muutuja sisaldab tundlikke andmeid
}
```

> **Selgitus:** Me loome siin `object`-tüüpi muutuja nimega `pilvio`. Sellel objektil on kaks atribuuti:
>
> - `api_key` (tüüp `string`): Sinu Pilvio API võti.
> - `billing_account_id` (tüüp `number`): Sinu Pilvio arvelduskonto ID.
>
> Atribuut `sensitive = true` tagab, et Terraform ei kuva selle muutuja väärtust logides ega käsurea väljundites pärast selle esmast seadistamist.
>
> Kuigi `billing_account_id` pole kohe esimeses sammus (ainult API võtme edastamisel *providerile*) vajalik, on hea see juba siia lisada, kuna hiljem serverit luues läheb seda vaja. Muutujate grupeerimine objektiks `pilvio` teeb koodi selgemaks – näiteks API võtmele viitame edaspidi kui `var.pilvio.api_key`.

### 2. Loo fail `init.tf`

Selles failis määratleme Terraformi enda ja vajalike *providerite* (antud juhul Pilvio *provideri*) seaded.

Loo fail nimega `init.tf` järgmise sisuga:

```
terraform {
  required_providers {
    pilvio = {
      source  = "pilvio-com/pilvio"
      version = "1.0.9" # Soovitatav on kasutada kindlat versiooni või vahemikku, nt ">= 1.0.9"
    }
  }
}

provider "pilvio" {
  host     = "api.pilvio.com" # Pilvio API endpoint
  apikey   = var.pilvio.api_key
  location = "tll01"          # Vaikimisi asukoht, saab hiljem muuta või muutujana defineerida
}
```

> **Selgitus:**
>
> - `terraform { ... }` plokk:
>   - `required_providers`: Siin määrame, et meie projekt vajab Pilvio *providerit*. `source` ütleb Terraformile, kust see *provider* leida (Terraform Registry'st) ja `version` määrab kasutatava versiooni.
> - `provider "pilvio" { ... }` plokk:
>   - See konfigureerib Pilvio *provideri*.
>   - `host`: Pilvio API serveri aadress.
>   - `apikey`: Sinu Pilvio API võti. Viitame siin eelmises failis defineeritud muutujale `var.pilvio.api_key`.
>   - `location`: Vaikimisi asukoht Pilvios, kus ressursse luuakse (nt `tll01` Tallinna jaoks). Seda saab hiljem ka ressursipõhiselt üle kirjutada või teha eraldi muutujaks.

### 3. Algväärtusta Terraformi projekt

Nüüd, kui esimesed konfiguratsioonifailid on olemas, ava terminal oma projekti kaustas ja käivita järgmine käsk:

```
terraform init
```

See käsklus teeb järgmist:

- Laeb alla ja paigaldab Pilvio *provideri*, mis on defineeritud `init.tf` failis.
- Seadistab *backendi* (kui see on defineeritud, meie näites praegu mitte).
- Kontrollib konfiguratsioonifailide süntaksit.

Kui kõik õnnestus, peaksid nägema teadet: `Terraform has been successfully initialized!`

## Samm 2 – Veebiserveri loomine

Nüüd, kui projekt on algväärtustatud, saame hakata defineerima ressursse, mida soovime Pilvios luua. Meie eesmärk on luua virtuaalmasin ja paigaldada sinna Nginx veebiserver.

### 1. Täienda faili `variables.tf`

Lisame `variables.tf` faili uue muutuja virtuaalmasina (VM) kasutajanime ja parooli jaoks.

Ava `variables.tf` ja lisa olemasoleva `pilvio` muutuja **järele** järgmine kood:

```
variable "vm" {
  type = object({
    username = string
    password = string
  })
  description = "Virtuaalmasina sisselogimisandmed (kasutajanimi ja parool)."
  sensitive   = true
}
```

> **Selgitus:** See `vm` objekt sisaldab:
>
> - `username` (tüüp `string`): Kasutajanimi VM-i sisselogimiseks.
> - `password` (tüüp `string`): Parool VM-i sisselogimiseks. Ka siin on `sensitive = true` oluline, et parooli ei kuvataks ebaturvaliselt.

### 2. Loo fail `server.tf`

Selles failis defineerime Pilvio ressursid: virtuaalmasina, ujuva (avaliku) IP-aadressi ja selle IP-aadressi sidumise virtuaalmasinaga.

Loo fail nimega `server.tf` järgmise sisuga:

```
resource "pilvio_vm" "web_server" {
  name       = "nginx-web-server-tf" # VM-i nimi Pilvios
  os_name    = "ubuntu"              # Operatsioonisüsteem
  os_version = "22.04"             # OS versioon
  memory     = 512                 # RAM MB-des (väike testserver)
  vcpu       = 1                   # vCPU-de arv
  username   = var.vm.username       # Kasutajanimi variables.tf failist
  password   = var.vm.password       # Parool variables.tf failist
  disks      = 20                  # Ketta suurus GB-des
  # billing_account_id puudub siit, kuna see on provideri tasemel juba määratud,
  # aga kui on mitu arvelduskontot, saab siin spetsiifilise määrata.
  # location on samuti provideri tasemel, aga saab siin üle kirjutada.

  # cloud-init konfiguratsioon Nginx'i paigaldamiseks
  cloud_init = jsonencode({
    packages = [
      "nginx" # Paigaldatav pakett
    ],
    runcmd = [
      "systemctl enable nginx", # Veendu, et Nginx käivituks automaatselt
      "systemctl start nginx"   # Käivita Nginx kohe pärast paigaldust
    ]
  })
}

resource "pilvio_floatingip" "web_public_ip" {
  name               = "nginx-public-ip-tf"
  billing_account_id = var.pilvio.billing_account_id # Vajalik Floating IP loomiseks
  # location on provideri tasemel, aga saab siin üle kirjutada, kui vaja.
}

resource "pilvio_floatingip_assignment" "web_server_ip_assignment" {
  # Sõltuvused tagavad, et VM ja IP on enne sidumist loodud
  depends_on = [
    pilvio_vm.web_server,
    pilvio_floatingip.web_public_ip
  ]

  assigned_to = pilvio_vm.web_server.uuid # Loodud VM-i UUID
  address     = pilvio_floatingip.web_public_ip.address # Loodud Floating IP aadress
  # location on provideri tasemel.
}
```

> **Selgitus:**
>
> - `resource "pilvio_vm" "web_server"`: See plokk defineerib virtuaalmasina.
>   - Määrame nime, OS-i, mälu, vCPU-d, kasutajanime, parooli ja kettasuuruse.
>   - `cloud_init`: See on võtmetähtsusega osa. `cloud-init` on standardne tööriist pilveserverite esmaseks konfigureerimiseks. Siin kasutame seda, et:
>     - `packages = ["nginx"]`: Paigaldada Nginx veebiserveri tarkvara.
>     - `runcmd`: Käivitada käsud pärast pakettide paigaldamist, et Nginx teenus lubada (automaatseks käivitumiseks) ja kohe käivitada.
>   - `jsonencode()`: Terraformi funktsioon, mis konverteerib HCL-sarnase struktuuri korrektseks JSON-iks, mida `cloud-init` ootab.
> - `resource "pilvio_floatingip" "web_public_ip"`: See loob ujuva (avaliku) IP-aadressi. Selleks on vaja `billing_account_id`-d.
> - `resource "pilvio_floatingip_assignment" "web_server_ip_assignment"`: See seob loodud ujuva IP-aadressi loodud virtuaalmasinaga.
>   - `assigned_to = pilvio_vm.web_server.uuid`: Viitab loodava VM-i unikaalsele ID-le (UUID).
>   - `address = pilvio_floatingip.web_public_ip.address`: Viitab loodava ujuva IP-aadressile.
>   - Terraform mõistab neid viiteid ja loob ressursid õiges järjekorras (esmalt VM ja IP, seejärel sidumine). `depends_on` on siin lisatud selguse mõttes, kuigi Terraform suudaks sõltuvused ka automaatselt tuvastada.

### 3. Loo fail `outputs.tf`

Väljundid (`outputs`) on kasulikud, et Terraform kuvaks pärast ressursside loomist teatud informatsiooni, näiteks loodud serveri avaliku IP-aadressi.

Loo fail nimega `outputs.tf` järgmise sisuga:

```
output "web_server_public_ip" {
  value       = "http://${pilvio_floatingip.web_public_ip.address}"
  description = "Nginx veebiserveri avalik IP-aadress (koos http:// prefiksiga)."
  # sensitive = false # Vaikimisi false, võib soovi korral lisada
}
```

> **Selgitus:**
>
> - `output "web_server_public_ip"`: Defineerib väljundi nimega `web_server_public_ip`.
> - `value`: Määrab väljundi väärtuse. Siin konstrueerime URL-i, kasutades loodud ujuva IP-aadressi (`pilvio_floatingip.web_public_ip.address`). Terraformi interpolatsioonisüntaks `${...}` võimaldab viidata teiste ressursside atribuutidele.

### 4. Loo fail `terraform.tfvars` ja väärtusta muutujad

Nagu eelnevalt mainitud, on `terraform.tfvars` fail koht, kuhu paned oma konkreetsed väärtused, eriti tundlikud andmed. Terraform loeb selle faili sisu automaatselt.

Loo fail nimega `terraform.tfvars` (kui sa seda juba Samm 1-s ei teinud või soovid täiendada) järgmise sisuga:

```
pilvio = {
  api_key            = "SINU_PILVIO_API_VÕTI_SIIA" # ASENDA PÄRIS VÕTMEGA
  billing_account_id = 12345                     # ASENDA OMA ARVELDUSKONTO ID-GA
}

vm = {
  username = "nginxuser" # VÕID VALIDA OMALE SOBIVA KASUTAJANIME
  password = "GENEREERI_VÄGA_TUGEV_PAROOL_SIIA" # ASENDA PÄRIS PAROOLIGA
}
```

> ⚠️ **Hoiatus:** Asenda kindlasti ülaltoodud näidisväärtused (`SINU_PILVIO_API_VÕTI_SIIA` jne) oma tegelike andmetega. **Ära lisa seda faili versioonihaldusesse (nt Git), kui see sisaldab sinu API võtit ja paroole!** Lisa `terraform.tfvars` oma `.gitignore` faili. Parool peab tõenäoliselt vastama Pilvio nõuetele (nt sisaldama suur- ja väiketähti, numbreid, sümboleid ning olema piisava pikkusega). Kui parool ei vasta nõuetele, võib VM-i loomine ebaõnnestuda.

## Samm 3 – Ressursside tööle panemine

Nüüd, kui kõik konfiguratsioonifailid on paigas ja `terraform.tfvars` on sinu andmetega täidetud, saame infrastruktuuri Pilvios luua.

1. **Kontrolli konfiguratsiooni ja vaata plaani:** Enne muudatuste rakendamist on alati hea mõte vaadata, mida Terraform täpselt teha kavatseb. Käivita käsk:

   ```
   terraform plan
   ```

   Terraform analüüsib sinu konfiguratsioonifaile ja kuvab nimekirja ressurssidest, mida ta kavatseb luua, muuta või kustutada. Kui kõik tundub korrektne, võid edasi liikuda.

   > ✨ **Nõuanne:** Võid plaani ka faili salvestada (`terraform plan -out=myplan`) ja hiljem selle sama plaani rakendada (`terraform apply myplan`). See tagab, et rakendatakse täpselt seda, mida planeeriti, isegi kui vahepeal on konfiguratsioonifaile muudetud.

2. **Rakenda muudatused (loo ressursid):** Ressursside reaalseks loomiseks käivita:

   ```
   terraform apply
   ```

   Terraform kuvab uuesti plaani ja küsib sinult kinnitust (`Do you want to perform these actions?`). Sisesta `yes` ja vajuta Enter.

   Terraform hakkab nüüd Pilvios sinu defineeritud ressursse looma. See võib võtta mõned minutid. Jälgi käsurea väljundit.

3. **Kontrolli väljundit:** Kui `terraform apply` on edukalt lõpule jõudnud, peaks Terraform kuvama `outputs.tf` failis defineeritud väljundid. Meie näites peaks see olema `web_server_public_ip`. Näiteks:

   ```
   Outputs:
   
   web_server_public_ip = "[http://123.45.67.89](http://123.45.67.89)"
   ```

   Pane see IP-aadress kirja.

   > ✨ **Nõuanne:** Pärast `terraform apply` käsu edukat lõppu võib kuluda veel mõni hetk (tavaliselt kuni minut), kuni `cloud-init` skript VM-is lõpetab Nginx'i paigaldamise ja käivitamise.

4. **Testi veebiserverit:** Ava veebibrauser ja sisesta aadressiribale Terraformi poolt antud `web_server_public_ip` (nt `http://123.45.67.89`). Sa peaksid nägema Nginx'i vaikimisi tervituslehte ("Welcome to nginx!").

   Kui näed Nginx'i lehte, siis palju õnne – oled edukalt Terraformiga Pilvios veebiserveri püsti pannud!

## Samm 4 – Ressursside maha võtmine

Kui oled katsetamise lõpetanud ja soovid loodud ressursid eemaldada, et vältida tarbetuid kulusid, on see Terraformiga väga lihtne.

Käivita oma projekti kaustas järgmine käsk:

```
terraform destroy
```

Terraform kuvab nimekirja ressurssidest, mida ta kavatseb kustutada, ja küsib sinult kinnitust. Sisesta `yes` ja vajuta Enter.

Terraform kustutab kõik selle projektiga loodud ressursid Pilviost (VM, Floating IP ja selle sidumine).

## Kokkuvõte

Selles juhendis õppisid sa tundma Terraformi põhitõdesid Pilvio pilveplatvormi näitel. Sa seadsid üles Terraformi projekti, defineerisid lihtsa veebiserveri infrastruktuuri koodina ja lasid Terraformil selle automaatselt Pilviosse paigaldada. Samuti õppisid, kuidas loodud ressursse hiljem eemaldada.

See on alles algus! Terraform pakub palju võimsamaid võimalusi keerukate taristute haldamiseks, moodulite loomiseks ja olemasoleva taristu importimiseks. Pilvio API-põhine platvorm koos Terraformiga avab uksed kaasaegsetele DevOps-praktikatele ja taristu automatiseerimisele Eestis.

Järgmiste sammudena võid proovida:

- Lisada Terraformi konfiguratsiooni keerukamaid võrguseadeid (nt Pilvio VPC).
- Automatiseerida rakenduse enda paigaldust `cloud-init` või Ansible'i abil.
- Uurida Terraformi mooduleid, et luua korduvkasutatavaid taristukomponente.

Kui sul tekib küsimusi Pilvio teenuste või Terraformi *provideri* kohta, võta julgelt ühendust [Pilvio klienditoega](https://pilvio.com/kontakt) või uuri lähemalt [Pilvio dokumentatsioonist](https://docs.pilvio.com).
