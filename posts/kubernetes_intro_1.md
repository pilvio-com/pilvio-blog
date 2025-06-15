---
title: "Sissejuhatus Kubernetesesse: Sinu esimene rakendus"           # Required: Post title
author: "Pilvio Admin Team"                   # Required: Author name
publishedAt: "16. juuni 2025"        # Required: Estonian format date
category: "Õpetused"                  # Required: Post category
tags: ["kubernetes", "devops"]          # Required: Array of tags
excerpt: "Selles postituses juhendame sind läbi Kubernetes'e põhikontseptsioonide, alustades sinu esimese rakenduse paigaldamisest kuni selle kättesaadavaks tegemiseni klastri sees.
"         # Optional: Custom excerpt (auto-generated if not provided)
imageUrl: "/media/kubernetes_intro_1" # Optional: Featured image URL
slug: "sissejuhatus-kubernetesesse-1"                    # Optional: Custom URL slug (auto-generated from title if not provided)
---
### Sissejuhatus Kubernetesesse: Sinu esimene rakendus

Tere tulemast meie blogiseeriasse, mis on pühendatud Kubernetesesse sisseelamisele! See on esimene postitus, mis aitab sul astuda esimesi samme konteinerite orkestreerimismaailmas. Kubernetes, tihti lühendatud kui K8s, on võimas avatud lähtekoodiga platvorm, mis automatiseerib konteineriseeritud rakenduste paigaldamist, skaleerimist ja haldamist.

Mõtle Kubernetesest kui sümfooniaorkestri dirigendist. Sinu rakendused on muusikud. Dockeriga saad sa öelda igale muusikule (konteinerile), mida mängida. Aga Kubernetes on dirigent, kes juhatab kogu orkestrit: ta tagab, et kõik alustavad õigel ajal, mängivad harmoonias, asendab muusiku, kui see peaks ära väsima, ja toob juurde uusi viiuleid, kui muusika peab muutuma võimsamaks.

Selles postituses juhendame sind läbi Kubernetes'e põhikontseptsioonide, alustades sinu esimese rakenduse paigaldamisest kuni selle kättesaadavaks tegemiseni klastri sees.

#### Eeldused

Enne alustamist eeldame, et sul on:

- Põhiteadmised Dockerist ja konteineritest. Sa mõistad, mis on tõmmis (`image`) ja mis on konteiner.
- Soov õppida ja katsetada käsureal.

Selles juhendis kasutame kohaliku arenduskeskkonna jaoks Minikube'i. **Siin Pilvios oleme kirjutanud eraldi põhjaliku juhendi**, kuidas seadistada Minikube koos Terraformiga meie platvormil. Palun järgi neid juhiseid, et oma Kubernetes'e testkeskkond püsti panna: [Kuidas paigaldada Minikube Pilvios Terraformiga](https://blog.pilvio.com/blog/minikube-terraform).

Kui oled sellega valmis, liigume edasi.

### Tööriistade Mõistmine: `kubectl` ja Minikube

Enne kui loome oma esimese rakenduse, on ülioluline mõista kahte põhilist tööriista, mille just seadistasid: `kubectl` ja Minikube.

#### `kubectl`: Sinu Kaugjuhtimispult

`kubectl` (hääldatakse "kjuub-kontroll") on sinu peamine ja kõige olulisem tööriist Kubernetes'e klastriga suhtlemiseks.

**Analoogia:** Mõtle `kubectl`-ist kui oma teleri kaugjuhtimispuldist. Pult ise ei tea, kuidas teleri sisemine elektroonika pilti tekitab. Sa lihtsalt vajutad nuppu "Kanal 5" ja teler teab, mida teha. `kubectl` on samasugune – sa annad käsu (`kubectl get pods`) ja ei pea muretsema, kuidas klaster selle info täpselt hangib. Sa suhtled ainult puldiga, mitte ei hakka teleri tagapaneeli lahti kruvima.

Kuidas see töötab?

Iga kord, kui sa käivitad käsu, teeb kubectl järgmist:

1. **Loeb konfiguratsiooni:** Sinu kodukataloogis on peidetud fail `.kube/config`. See on nagu puldi ja teleri "paaristamise" info. Sinna on kirja pandud sinu Minikube'i klastri unikaalne aadress ja turvavõtmed, mis tõestavad, et just sinul on õigus sellele klastrile käske anda.
2. **Saadab API päringu:** `kubectl` vormistab sinu käsu standardseks API-päringuks ja saadab selle üle võrgu sinu klastri "juhtimiskeskusesse", mida nimetatakse **API Serveriks**. See on ainus värav, mille kaudu klastriga suheldakse.
3. **Näitab vastust:** API Server töötleb päringu, kogub kokku vajaliku info ja saadab selle puldile tagasi. `kubectl` vormindab selle sinu jaoks kenasti terminalis loetavaks.

#### Minikube: Sinu Isiklik Kubernetes'e Klaster

Minikube'i eesmärk on anda sulle täisfunktsionaalne, aga lihtsustatud Kubernetes'e klaster, mis jookseb sinu enda arvutis.

**Analoogia:** Kujuta ette, et tahad õppida linna planeerimist. Selle asemel, et ehitada päris linna (mis oleks tohutult kallis ja keeruline), ehitad sa oma tuppa väikesele lauale miniatuurse Lego-linna. Selles Lego-linnas on olemas kõik olulised hooned: raekoda, postkontor, arhiiv. Minikube ongi sinu Lego-linn. Päris Kubernetes'e klaster on päris linn, aga õppimiseks sobib Lego-mudel ideaalselt.

Kuidas see töötab?

Kui sa käivitasid minikube start, ehitas see sinu Lego-linna valmis, luues ühe Docker'i konteineri, mille sees pandi tööle kõik linna olulised teenused (Kubernetes'e juhtplaan ehk Control Plane):

- **API Server:** See on linna raekoda ja postkontor ühes. Kõik käsud (`kubectl` päringud) saabuvad siia.
- **etcd:** See on linna arhiiv. Ülimalt turvaline ja töökindel andmebaas, kus hoitakse kirjas absoluutselt kõike, mis linnas toimub: millised majad on ehitatud, kellele need kuuluvad jne.
- **Scheduler:** See on linnaarhitekt. Kui keegi tahab ehitada uut maja (Pod'i), siis arhitekt vaatab plaani ja otsustab, millisele vabale krundile (sõlmele ehk *node*'ile) see maja ehitatakse. Minikube'i puhul on meil ainult üks krunt.

Nüüd, kui me mõistame oma tööriistu, oleme valmis ehitama oma esimese maja.

### Samm 1: Sinu esimene Pod

Kõige väiksem ja fundamentaalsem ehituskivi Kuberneteses on **Pod**.

**Analoogia:** Kui Kubernetes on linn, siis **Pod on korter** kortermajas. Korter ise ei tee midagi, aga see pakub elamiseks vajalikke tingimusi: seinu, elektrit, vett ja ühte kindlat postiaadressi. Need tingimused ongi konteineri jaoks vajalik jagatud võrguruum ja salvestusruum. Tavaliselt elab korteris üks elanik (sinu rakenduse konteiner). Mõnikord võib seal olla ka väike abiline, kes elab panipaigas (see oleks *sidecar*-konteiner).

Loome deklaratiivselt oma esimese korteri. See tähendab, et me ei anna käsku "ehita korter!", vaid anname ehitajale (Kubernetesele) detailse joonise (`.yaml` fail) ja ütleme: "tee nii, et reaalsus vastaks sellele joonisele."

Loo fail nimega `pod.yaml`:

YAML

```
apiVersion: v1
kind: Pod
metadata:
  name: minu-esimene-pod
  labels:
    app: veebiserver
spec:
  containers:
  - name: nginx-konteiner
    image: nginx
```

**YAML-faili lahkamine:**

- `apiVersion: v1`: "Me räägime API Serveriga keele versioonis 1."

- `kind: Pod`: "Joonise tüüp on 'Korter' (Pod)."

- ```
  metadata
  ```

  : "Korteri andmed."

  - `name: minu-esimene-pod`: "Anname korterile nime (uksesildi)."
  - `labels: app: veebiserver`: "Värvime korteri ukse punaseks (silt `app: veebiserver`). See on ülioluline, sest hiljem saame öelda: 'leia kõik punase uksega korterid'."

- ```
  spec
  ```

  : "Korteri detailne siseplaan."

  - `containers:`: "Siin on nimekiri elanikest (konteineritest), kes siin korteris elavad."
  - `- name: nginx-konteiner`: "Elaniku nimi on 'nginx-konteiner'."
  - `image: nginx`: "See elanik on loodud 'nginx' tõmmise (DNA) põhjal."

Nüüd anname joonise Kubernetes'ele:

Bash

```
kubectl apply -f pod.yaml
```

Kontrollime, kas korter sai valmis ja elanik kolis sisse:

Bash

```
kubectl get pods
```

Näeme, et `minu-esimene-pod` on staatuses `Running`. Nüüd koristame selle ära, sest me õppisime just, et Podid on surelikud. Me ei taha hallata üksikuid kortereid, vaid tervet kortermaja.

Bash

```
kubectl delete -f pod.yaml
```

### Samm 2: Usaldusväärne rakendus Deploymentiga

Pod on liiga habras. Kui rakendus jookseb kokku, on Pod surnud. Me vajame kedagi, kes automaatselt uue asemele loob. See keegi on **Deployment**.

**Analoogia:** Kui Pod on korter, siis **Deployment on kortermaja haldur**. Sa ei ütle haldurile, et "ehita üks korter". Sa annad talle maja joonise ja reegli: "Selles majas peab ALATI olema kaks identset, elanikega asustatud korterit (`replicas: 2`)." Kui ühes korteris toimub uputus (Pod jookseb kokku), siis haldur märkab seda, sulgeb selle korteri ja ehitab koheselt joonise alusel uue asemele. Sina ei pea sekkuma.

Loome haldurile tööjuhendi failis `deployment.yaml`:



YAML

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: veebiserver
  template:
    metadata:
      labels:
        app: veebiserver
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
```

**Tööjuhendi lahkamine:**

- `kind: Deployment`: "See on tööjuhend 'Kortermaja Haldurile'."
- `replicas: 2`: "Halduri põhireegel: alati hoia töös 2 identset korterit."
- `selector`: "Kuidas haldur teab, millised korterid on tema omad? Ta otsib neid, millel on uksel silt `app: veebiserver`." See on tema vastutusala.
- `template`: "See on detailne korteri joonis, mida haldur kasutab IGA KORD, kui on vaja uus korter ehitada. Pane tähele, et joonisel endal peab olema sama silt (`labels: app: veebiserver`), et haldur saaks pärast ehitamist aru, et see on tema hallatav korter."

Anname tööjuhendi haldurile:

Bash

```
kubectl apply -f deployment.yaml
```

Kontrollime halduri tööd:

Bash

```
kubectl get pods
```

Näeme kahte NGINX Pod'i, mille haldur on loonud. Nüüd on meil töökindel süsteem!

### Samm 3: Rakenduse kättesaadavaks tegemine Service'iga

Meil on nüüd kaks korterit, aga nende uksekoodid (IP-aadressid) on ajutised. Kui haldur ehitab uue korteri, saab see uue uksekoodi. Kuidas saaksime saata kirja "NGINX elanikele", kui me ei tea nende täpset aadressi? Lahendus on **Service**.

**Analoogia:** **Service on kortermaja fuajees asuv postkontor või infopunkt**. Sellel postkontoril on üks, püsiv ja avalik aadress (selle `ClusterIP` ja DNS-nimi). Kõik kirjad (võrguliiklus) saadetakse sellele aadressile. Postkontori töötaja (`selector`) vaatab kirja ja teab täpselt, millistel ustel on silt `app: veebiserver`. Ta võtab kirja ja viib selle ühele neist elanikest. Kui elanikke on mitu, jaotab ta kirjad nende vahel võrdselt ära (see ongi koormuse jaotamine ehk *load balancing*).

Loome postkontorile reeglid failis `service.yaml`:

YAML

```
apiVersion: v1
kind: Service
metadata:
  name: minu-nginx-service
spec:
  type: ClusterIP
  selector:
    app: veebiserver
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

**Postkontori reeglite lahkamine:**

- `type: ClusterIP`: "See postkontor teenindab ainult maja-siseseid kirju (liiklus on avatud ainult klastri sees)."

- `selector: app: veebiserver`: "Postitöötaja reegel: vii kirjad edasi ainult neile korteritele, mille uksel on silt `app: veebiserver`." See ühendab postkontori õigete korteritega.

- ```
  ports
  ```

  : "Kuidas kirju vastu võetakse ja edastatakse."

  - `port: 80`: "Kirja vastuvõtmise postkasti number fuajees on 80."
  - `targetPort: 80`: "Korteri ukse küljes oleva postipilu number, kuhu kiri tuleb panna, on samuti 80."

Avame postkontori:

Bash

```
kubectl apply -f service.yaml
```

Kontrollime, kas postkontor töötab:

Bash

```
kubectl get service minu-nginx-service
```

Väljund näitab, et meie teenusel on nüüd stabiilne, maja-sisene aadress (`CLUSTER-IP`).

### Kokkuvõte

Palju õnne! Sa oled edukalt loonud oma esimese rakenduse Kuberneteses, kasutades selleks professionaalseid ja töökindlaid meetodeid.

Mida sa täna õppisid läbi analoogiate:

- Sa mõistad põhilisi tööriistu: **`kubectl`** on sinu pult ja **Minikube** on sinu isiklik Lego-linn.
- Sa tead, mis on Kubernetes'e ehituskivid: **Pod** on korter, **Deployment** on tark maja haldur ja **Service** on maja postkontor.
- Sa oskad luua töökindla ja skaleeritava rakenduse, mis suudab end ise parandada.

Hetkel on meie rakendus kättesaadav ainult klastri seest (maja-siseselt). Meie seeria järgmises postituses vaatame, kuidas oma rakendus turvaliselt ka välismaailmale avada, ehitades maja ette korraliku sissepääsu – selleks kasutame **Ingress**'i. Püsige lainel!
