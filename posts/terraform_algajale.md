---
title: "Terraformi põhitõed: sissejuhatus algajale"           # Required: Post title
author: "Kaur Kiisler"                   # Required: Author name
publishedAt: "4. juuni 2025"        # Required: Estonian format date
category: "Terraform"                  # Required: Post category
tags: ["terraform", "devops"]          # Required: Array of tags
featured: true                         # Required: Boolean (only one post should be true)
excerpt: "Brief description..."         # Optional: Custom excerpt (auto-generated if not provided)
readingTime: "8 min"                   # Optional: Reading time (auto-calculated if not provided)
imageUrl: "https://example.com/img.jpg" # Optional: Featured image URL
---
# Terraformi põhitõed: sissejuhatus algajale

## Sissejuhatus

Tere tulemast Terraformi maailma! Kui oled kunagi mõelnud, kuidas suured ettevõtted ja väledad idufirmad haldavad oma keerukaid IT-taristuid pilves või oma andmekeskustes, siis Terraform on üks võtmesõnadest. See juhend on mõeldud just sulle, kui oled algaja ja soovid aru saada, mis Terraform on, miks see kasulik on ja kuidas sellega esimesi samme astuda.

Kujuta ette, et pead ehitama maja. Sa võid seda teha käsitsi, kivi kivi haaval, või kasutada detailset ehitusplaani ja tööriistu, mis aitavad sul protsessi automatiseerida ja tagada, et lõpptulemus vastab täpselt sinu ootustele. Terraform on justkui see detailne ehitusplaan ja nutikad tööriistad sinu IT-taristu jaoks.

Selles juhendis keskendume Terraformi põhimõtetele, eelistele ja peamistele käskudele, et saaksid kindla aluse edasiseks õppimiseks ja praktiseerimiseks.

### Mis on Terraform?

Terraform on avatud lähtekoodiga tööriist, mille on loonud ettevõte nimega HashiCorp. See kuulub kategooriasse "Taristu koodina" (Infrastructure as Code - IaC), mis tähendab, et sa saad oma servereid, andmebaase, võrke ja muid IT-ressursse kirjeldada ja hallata konfiguratsioonifailides, kasutades lihtsasti loetavat keelt nimega HCL (HashiCorp Configuration Language).

Terraform ei ole seotud ühegi konkreetse pilveteenuse pakkujaga. See toetab laia valikut platvorme, sealhulgas suuri tegijaid nagu Amazon Web Services (AWS), Microsoft Azure, Google Cloud Platform (GCP), aga ka spetsiifilisemaid teenusepakkujaid nagu **Pilvio**.

### Kellele see juhend mõeldud on?

* **Süsteemiadministraatorid ja DevOps insenerid:** Kes soovivad automatiseerida taristu haldamist ja muuta protsessid korratavamaks ning usaldusväärsemaks.
* **Tarkvaraarendajad:** Kes puutuvad kokku rakenduste jaoks vajaliku taristu loomise ja haldamisega.
* **IT-juhid ja arhitektid:** Kes soovivad paremat ülevaadet ja kontrolli oma organisatsiooni IT-ressursside üle.
* **Kõik tehnikahuvilised:** Kes tahavad õppida tundma kaasaegseid taristuhalduse praktikaid.

## Terraformi Tööpõhimõte

Terraformi tööpõhimõte on deklaratiivne. See tähendab, et sa kirjeldad oma konfiguratsioonifailides **soovitud lõppseisundit** – milliseid ressursse sa vajad ja kuidas need peaksid olema seadistatud – mitte ei kirjuta samm-sammult juhiseid, kuidas selleni jõuda. Terraform ise nuputab välja, kuidas sinu kirjeldatud seisundini jõuda.

Terraformi tuumkomponendid on järgmised:

1.  **Konfiguratsioonifailid (.tf):** Siia kirjutad sa HCL keeles oma taristu kirjelduse. Tavaliselt on projektis mitu `.tf` laiendiga faili, näiteks `main.tf` ressursside jaoks, `variables.tf` muutujate jaoks ja `outputs.tf` väljundite jaoks.
2.  **Providerid (Providers):** Need on justkui pistikprogrammid, mis võimaldavad Terraformil suhelda erinevate platvormide API-dega (nt Pilvio, AWS, Azure, Docker jne). Iga *provider* pakub spetsiifilisi ressursitüüpe, mida saad oma konfiguratsioonis kasutada (nt `pilvio_vm` virtuaalmasina loomiseks Pilvios).
3.  **Olekufail (State File - `terraform.tfstate`):** See on Terraformi jaoks kriitilise tähtsusega JSON-fail. Terraform salvestab siia informatsiooni sinu hallatavate ressursside ja nende tegeliku seisu kohta. See võimaldab Terraformil:
    * Kaardistada sinu konfiguratsioonis olevad ressursid reaalsete ressurssidega pilves.
    * Jälgida ressursside metaandmeid.
    * Parandada jõudlust suuremate taristute puhul, kuna ei pea alati kõiki ressursse uuesti API kaudu pärima.
    * Hallata sõltuvusi ressursside vahel.

    > ⚠️ **Hoiatus:** Olekufail võib sisaldada tundlikku informatsiooni. Seda tuleks hoida turvaliselt ja meeskonnatöö puhul kasutada kaugoleku *backende* (nt Pilvio S3, HashiCorp Consul, AWS S3), et tagada selle jagamine ja lukustamine.

4.  **Terraform Core:** See on Terraformi tuumik, mis võtab vastu sinu konfiguratsioonifailid ja olekufaili, koostab tegevusplaani ja suhtleb *providerite* kaudu platvormidega, et soovitud seisund saavutada.

Lihtsustatud töövoog näeb välja selline:
1.  Sa kirjutad oma taristu konfiguratsiooni `.tf` failidesse.
2.  Käivitad `terraform init`, et laadida alla vajalikud *providerid*.
3.  Käivitad `terraform plan`, et näha, milliseid muudatusi Terraform kavatseb teha.
4.  Käivitad `terraform apply`, et need muudatused reaalselt ellu viia. Terraform suhtleb *provideri* kaudu sihtplatvormiga ja loob/muudab/kustutab ressursse.
5.  Terraform uuendab olekufaili vastavalt tehtud muudatustele.

## Terraformi Peamised Eelised

Terraformi kasutuselevõtt võib tuua mitmeid olulisi eeliseid:

* **Taristu koodina (Infrastructure as Code - IaC):**
    * **Versioonihaldus:** Saad oma taristu konfiguratsiooni hoida versioonihaldussüsteemis (nt Git) nagu iga teist koodi. See võimaldab jälgida muudatusi, minna tagasi varasemate versioonide juurde ja teha koostööd meeskonnaga.
    * **Korduvkasutatavus:** Saad luua korduvkasutatavaid taristumooduleid (nt tüüpiline veebiserveri seadistus), mida saad erinevates projektides või keskkondades (arendus, test, tootmine) uuesti kasutada.
    * **Dokumentatsioon:** Sinu Terraformi kood ise ongi sinu taristu dokumentatsioon. See on alati ajakohane ja kirjeldab täpselt, milline sinu taristu olema peaks.

* **Automatiseerimine ja Kiirus:**
    * Taristu loomine, muutmine ja hävitamine on automatiseeritud. See vähendab manuaalset tööd ja kiirendab protsesse märkimisväärselt. Uue testkeskkonna loomine võib võtta minuteid, mitte tunde või päevi.

* **Järjepidevus ja Vigade Vähendamine:**
    * Kuna taristu luuakse alati sama koodi põhjal, on keskkonnad järjepidevad ja väheneb inimlike vigade oht, mis võivad tekkida manuaalsel seadistamisel.

* **Muudatuste Planeerimine ja Eelvaade:**
    * `terraform plan` käsklus annab sulle selge ülevaate sellest, mida Terraform teha kavatseb, enne kui ühtegi reaalset muudatust tehakse. See aitab vältida ootamatusi.

* **Mitme Pilve ja Teenuse Tugi (Multi-Cloud / Hybrid-Cloud):**
    * Terraform võimaldab hallata ressursse erinevates pilvekeskkondades ja isegi omaenda andmekeskustes (kui vastav *provider* on olemas) ühtse tööriista ja keele abil.

* **Koostöö:**
    * Jagatud olekufaili (*remote state*) ja versioonihalduse abil saavad meeskonnad efektiivselt koos töötada sama taristu kallal.

## Terraformi Peamised Käsklused

Siin on mõned kõige olulisemad Terraformi käsklused, mida sa igapäevaselt kasutama hakkad:

* ### `terraform init`
    * **Eesmärk:** Initsialiseerib Terraformi töökausta.
    * **Mida teeb:**
        * Laeb alla ja paigaldab konfiguratsioonifailides (`.tf`) määratletud *providerid*.
        * Seadistab *backendi* olekufaili hoidmiseks (kui see on konfigureeritud).
        * Laeb alla moodulid (kui neid kasutatakse).
    * **Millal kasutada:** Seda käsklust tuleb käivitada iga kord, kui alustad uut Terraformi projekti või kui oled lisanud uusi *providerid* või mooduleid olemasolevasse projekti.

    ```bash
    terraform init
    ```

* ### `terraform validate`
    * **Eesmärk:** Kontrollib konfiguratsioonifailide süntaktilist korrektsust.
    * **Mida teeb:** Vaatab üle sinu `.tf` failid ja annab teada, kui seal on süntaksivigu või muid ilmselgeid probleeme. See ei kontrolli väärtuste õigsust ega suhtle pilveteenuse pakkujaga.
    * **Millal kasutada:** Hea käivitada pärast konfiguratsioonifailides muudatuste tegemist, et kiiresti vigu avastada.

    ```bash
    terraform validate
    ```

* ### `terraform fmt`
    * **Eesmärk:** Vormindab konfiguratsioonifailid standardsele stiilile.
    * **Mida teeb:** Kirjutab sinu `.tf` failid ümber, et need vastaksid Terraformi ametlikele stiilijuhistele (nt taanded, tühikud). See teeb koodi loetavamaks ja ühtlasemaks, eriti meeskonnatöös.
    * **Millal kasutada:** Soovitatav käivitada regulaarselt, et hoida kood korras.

    ```bash
    terraform fmt
    ```
    > ✨ **Nõuanne:** `terraform fmt -recursive` vormindab ka alamkaustades olevad failid.

* ### `terraform plan`
    * **Eesmärk:** Loob tegevusplaani, mis näitab, milliseid muudatusi Terraform teeks, et saavutada konfiguratsioonifailides kirjeldatud seisund.
    * **Mida teeb:** Võrdleb sinu praegust konfiguratsiooni olekufailis (kui see on olemas) kirjeldatud seisundiga ja reaalsete ressurssidega pilves. Seejärel kuvab, milliseid ressursse on vaja luua (`+`), muuta (`~`) või kustutada (`-`).
    * **Millal kasutada:** Alati enne `terraform apply` käivitamist, et olla kindel, et tehtavad muudatused on ootuspärased.

    ```bash
    terraform plan
    ```
    > ✨ **Nõuanne:** `terraform plan -out=myplan.tfplan` salvestab plaani faili, mida saab hiljem `terraform apply myplan.tfplan` käsuga rakendada. See tagab, et rakendatakse täpselt seda plaani, isegi kui vahepeal on konfiguratsioonifaile muudetud.

* ### `terraform apply`
    * **Eesmärk:** Rakendab tegevusplaanis kirjeldatud muudatused, et luua, muuta või kustutada ressursse.
    * **Mida teeb:** Kui käivitad ilma argumendita, genereerib Terraform esmalt plaani (nagu `terraform plan`) ja küsib seejärel sinult kinnitust (`yes` või `no`) enne muudatuste tegemist. Kui annad argumendina ette eelnevalt salvestatud plaani faili (nt `terraform apply myplan.tfplan`), rakendab ta selle plaani ilma uuesti küsimata (kui plaan ei ole aegunud).
    * **Millal kasutada:** Siis, kui oled `terraform plan` väljundiga rahul ja soovid reaalselt taristut muuta.

    ```bash
    terraform apply
    # Või kui kasutasid -out lipuga plan'i:
    # terraform apply "myplan.tfplan"
    ```

* ### `terraform show`
    * **Eesmärk:** Kuvab praeguse olekufaili sisu inimloetaval kujul või näitab salvestatud plaani detaile.
    * **Mida teeb:** Vaikimisi kuvab olekufailis olevate ressursside atribuudid. Kui annad argumendina plaani faili, kuvab selle plaani sisu.
    * **Millal kasutada:** Et saada ülevaade Terraformi poolt hallatavatest ressurssidest või uurida plaani detaile.

    ```bash
    terraform show
    # Plaani faili vaatamiseks:
    # terraform show myplan.tfplan
    ```

* ### `terraform state <SUBCOMMAND>`
    * **Eesmärk:** Võimaldab teha edasijõudnud operatsioone olekufailiga.
    * **Mida teeb:** Sellel on mitu alamkäsklust, näiteks:
        * `terraform state list`: Kuvab nimekirja olekufailis olevatest ressurssidest.
        * `terraform state show <resource_address>`: Kuvab konkreetse ressursi detailid olekufailist.
        * `terraform state mv <source_address> <destination_address>`: Võimaldab ressursse olekufailis ümber nimetada või liigutada (nt kui muudad ressursi nime konfiguratsioonis).
        * `terraform state rm <resource_address>`: Eemaldab ressursi Terraformi olekufailist (ei kustuta reaalset ressurssi pilvest). Kasulik, kui soovid, et Terraform enam mingit ressurssi ei haldaks.
    * **Millal kasutada:** Tavaliselt siis, kui on vaja olekufaili käsitsi korrigeerida või uurida. Algajad ei pruugi seda kohe vajada.

    ```bash
    terraform state list
    terraform state show pilvio_vm.web_server
    ```
    > ⚠️ **Hoiatus:** Ole `terraform state` käskudega ettevaatlik, eriti `mv` ja `rm` puhul, kuna valesti kasutades võid olekufaili rikkuda.

* ### `terraform destroy`
    * **Eesmärk:** Kustutab kõik Terraformi konfiguratsiooniga loodud ja olekufailis hallatavad ressursid.
    * **Mida teeb:** Genereerib plaani, mis näitab, millised ressursid kustutatakse, ja küsib sinult kinnitust enne kustutamist.
    * **Millal kasutada:** Siis, kui soovid loodud taristu täielikult eemaldada (nt testkeskkonna puhul).

    ```bash
    terraform destroy
    ```

## Kuidas Alustada?

Parim viis Terraformi õppimiseks on praktiline proovimine.

1.  **Paigalda Terraform CLI:** Järgi juhiseid [Terraformi ametlikul veebilehel](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli).
2.  **Hangi ligipääs pilveplatvormile:** Loo endale konto mõnel Terraformi toetatud platvormil (nt Pilvio, AWS, Azure, GCP) ja hangi vajalikud API võtmed või autentimisandmed.
3.  **Järgi praktilist juhendit:** Hea esimene samm on proovida läbi mõni lihtne "Hello World" tüüpi juhend. Näiteks meie eelmine artikkel, "[Juhend: Õpime tundma Terraformi Pilvio näitel ja seame üles Nginx veebiserveri](LINK_EELMISELE_ARTIKLILE_SIIA)", on suurepärane alguspunkt, et näha Terraformi töös Pilvio platvormil. (Kui see artikkel on avaldatud, asenda see link tegeliku URL-iga).

## Kokkuvõte

Terraform on võimas tööriist, mis muudab IT-taristu haldamise efektiivsemaks, usaldusväärsemaks ja automatiseeritumaks. Selle deklaratiivne lähenemine, olekuhaldus ja lai platvormide tugi teevad sellest DevOps-maailmas asendamatu vahendi.

Kuigi esialgu võib tunduda, et õppida on palju, tasub investeeritud aeg end kiiresti ära. Alustades lihtsatest projektidest ja liikudes järk-järgult keerukamate poole, saad peagi aru Terraformi eelistest ja võimekusest.

Loodame, et see sissejuhatav juhend andis sulle hea ülevaate Terraformi põhitõdedest. Jõudu katsetamisel!
