---
title: "Praktiline sissejuhatus Kubernetesesse: sinu esimene rakendus (1. osa)"
author: "Pilvio Admin Team"
publishedAt: "16. juuni 2025"
category: "Õpetused"
tags: ["kubernetes", "devops", "alustamine", "õpetus"]
excerpt: "Alusta oma teekonda Kubernetesega! See on meie artiklisarja esimene osa, kus õpid praktiliste näidete varal selgeks Pod'i, Deployment'i ja Service'i põhimõtted."
imageUrl: "/media/kubernetes_intro_1.png"
slug: "praktiline-sissejuhatus-kubernetesesse-1"
---

### Tere tulemast Kubernetes'e maailma!

Tere tulemast meie uude artiklisarja **"Nullist kangelaseks Kubernetesega"**! See sari on mõeldud kõigile, kes soovivad samm-sammult ja praktiliste näidete varal selgeks õppida Kubernetes'e – tänapäeva ühe olulisima konteinerite orkestreerimise platvormi.

Oled sa arendaja, kes tahab oma rakendusi paremini hallata, või süsteemiadministraator, kes ehitab tulevikukindlaid lahendusi, see sari on just sulle. Me ei uputa sind kuiva teooriasse, vaid ehitame koos, klots klotsi haaval, toimiva süsteemi, seletades iga sammu juures lahti, **miks** me midagi teeme.

Selleks, et teekond oleks selge, oleme koostanud järgmise õppekava, millega liigume algaja tasemelt enesekindla kasutaja tasemeni:

* **1. osa: Aluste loomine (see artikkel)**
    * Mis on Kubernetes ja miks seda vaja on?
    * Tööriistade (`kubectl`, Minikube) seadistamine ja mõistmine.
    * Põhikontseptsioonid: **Pod**, **Deployment** ja **Service**. Eesmärk on saada lihtne rakendus klastri-siseselt tööle.

* **2. osa: Rakenduse avalikustamine ja konfigureerimine**
    * Rakenduse turvaline avamine välismaailmale **Ingress**'i abil.
    * Rakenduse seadete ja paroolide haldamine **ConfigMap**'ide ja **Secret**'itega.

* **3. osa: Andmete püsivus ja olekuga rakendused**
    * Kuidas hallata andmebaase ja teisi olekuga rakendusi? Tutvume **PersistentVolume**'ite ja **StatefulSet**'idega.

* **4. osa: Jälgimine ja automatiseerimine**
    * Kuidas oma klastri ja rakenduste tervisel silma peal hoida?
    * Sissejuhatus CI/CD torujuhtmetesse – kuidas automatiseerida rakenduse paigaldamist Kubernetesesse.

Selles esimeses osas paneme paika vundamendi. Alustame!

---

### Tööriistade ettevalmistus ja mõistmine

**Kus me oleme?** Enne ehitama asumist peame oma tööriistad valmis panema ja nendega tuttavaks saama. See on meie teekonna "Samm 0".

#### Eeldused

Enne alustamist eeldame, et sul on:

* Põhiteadmised Dockerist ja konteineritest. Sa mõistad, mis on tõmmis (`image`) ja mis on konteiner.
* Soov õppida ja katsetada käsureal.

Selles juhendis kasutame kohaliku arenduskeskkonna jaoks Minikube'i. **Siin Pilvios oleme kirjutanud eraldi põhjaliku juhendi**, kuidas seadistada Minikube koos Terraformiga meie platvormil. Palun järgi neid juhiseid, et oma Kubernetes'e testkeskkond püsti panna: [Kuidas paigaldada Minikube Pilvios Terraformiga](https://blog.pilvio.com/blog/minikube-terraform).

Kui `kubectl` ja Minikube on paigaldatud, liigume edasi nende hingeeluga tutvumisele.

---

### `kubectl` ja Minikube: sinu pult ja mänguväljak

**Kus me oleme?** Mõistame oma kahte kõige olulisemat tööriista. `kubectl` on sinu "pult" klastriga suhtlemiseks ja Minikube on sinu isiklik "Lego-linn" ehk testklaster.

#### `kubectl`: sinu kaugjuhtimispult

`kubectl` (hääldatakse "kjuub-kontroll") on sinu peamine ja kõige olulisem tööriist Kubernetes'e klastriga suhtlemiseks.

**Analoogia:** Mõtle `kubectl`-ist kui oma teleri kaugjuhtimispuldist. Sa lihtsalt vajutad nuppu "Kanal 5" ja teler teab, mida teha. `kubectl` on samasugune – sa annad käsu (`kubectl get pods`) ja ei pea muretsema, kuidas klaster selle info täpselt hangib.

Iga kord, kui sa käivitad käsu, loeb `kubectl` sinu kodukataloogist `.kube/config` faili, kus on kirjas klastri aadress ja turvavõtmed. Seejärel vormistab ta sinu käsu API-päringuks ja saadab selle klastri "juhtimiskeskusesse", mida nimetatakse **API Serveriks**.

#### Minikube: sinu isiklik Kubernetes'e klaster

Minikube'i eesmärk on anda sulle täisfunktsionaalne, aga lihtsustatud Kubernetes'e klaster, mis jookseb sinu enda arvutis.

**Analoogia:** Mõtle sellest kui miniatuursest Lego-linnast oma laual. Päris Kubernetes'e klaster on nagu päris linn, aga õppimiseks sobib Lego-mudel ideaalselt. Selles Lego-linnas on olemas kõik olulised hooned:
* **API Server:** Linna raekoda, kuhu kõik käsud saabuvad.
* **etcd:** Linna turvaline arhiiv, kus hoitakse kogu infot.
* **Scheduler:** Linnaarhitekt, kes otsustab, kuhu uus maja (Pod) ehitada.

Nüüd, kui me mõistame oma tööriistu, oleme valmis ehitama oma esimese maja.

---

### Samm 1: sinu esimene Pod

**Kus me oleme?** Loome kõige lihtsama ja väiksema ehitusploki Kuberneteses – **Pod'i**. See on meie esimene praktiline samm klastri sees.

**Analoogia:** Kui Kubernetes on linn, siis **Pod on korter**. Korter pakub elamiseks vajalikke tingimusi (seinu, elektrit, vett), milleks on konteineri jaoks jagatud võrgu- ja salvestusruum.

Loome deklaratiivselt oma esimese korteri, andes Kubernetes'ele detailse joonise (`.yaml` fail).

Loo oma arvutisse fail nimega `pod.yaml`:
```yaml
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
* `kind: Pod`: Määrame, et loome "korteri".
* `metadata`: Lisame nimesildi (`name`) ja värvime ukse punaseks (`labels`), et selle hiljem üles leiaksime.
* `spec`: Kirjeldame korteri sisu – ühe elaniku (`container`), kes on loodud `nginx` tõmmise (DNA) põhjal.

Nüüd anname joonise Kubernetes'ele käsuga `kubectl apply`:
```bash
kubectl apply -f pod.yaml
```
**Oodatav tulemus:**
```
pod/minu-esimene-pod created
```
Kontrollime, kas korter sai valmis ja elanik kolis sisse:
```bash
kubectl get pods
```
**Oodatav tulemus:**
```
NAME               READY   STATUS    RESTARTS   AGE
minu-esimene-pod   1/1     Running   0          30s
```
Näeme, et Pod on staatuses `Running`. Kuna aga Pod'id on surelikud ja me tahame ehitada töökindlamaid süsteeme, siis koristame selle üksiku korteri kohe ära.
```bash
kubectl delete -f pod.yaml
```
**Oodatav tulemus:**
```
pod "minu-esimene-pod" deleted
```

---

### Samm 2: töökindel rakendus Deployment'iga

**Kus me oleme?** Liigume edasi üksikult "korterilt" terve "kortermaja" haldamise juurde. Loome **Deployment**'i, mis tagab, et meie rakendus on alati töös.

**Analoogia:** Kui Pod on korter, siis **Deployment on kortermaja haldur**. Sa annad talle reegli: "Selles majas peab ALATI olema kaks identset korterit (`replicas: 2`)." Kui üks korter läheb katki, ehitab haldur kohe uue asemele.

Loome haldurile tööjuhendi failis `deployment.yaml`:
```yaml
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
* `replicas: 2`: Halduri põhireegel – hoia töös 2 identset Pod'i.
* `selector`: Kuidas haldur teab, millised Pod'id on tema omad? Ta otsib neid, millel on silt `app: veebiserver`.
* `template`: See on detailne korteri joonis, mille alusel haldur uusi Pod'e ehitab.

Anname tööjuhendi haldurile:
```bash
kubectl apply -f deployment.yaml
```
**Oodatav tulemus:**
```
deployment.apps/nginx-deployment created
```
Kontrollime halduri tööd:
```bash
kubectl get pods
```
**Oodatav tulemus:**
```
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-7848d4b8d5-7c5jc   1/1     Running   0          25s
nginx-deployment-7848d4b8d5-x5zvm   1/1     Running   0          25s
```
Näeme kahte NGINX Pod'i, mille haldur on loonud. Nüüd on meil töökindel süsteem!

---

### Samm 3: rakenduse ühendamine Service'iga

**Kus me oleme?** Meil on töökindel rakendus, aga sellel puudub stabiilne aadress klastri sees. Loome **Service**'i, et anda meie rakendusele püsiv võrguidentiteet.

**Analoogia:** **Service on kortermaja fuajees asuv postkontor**. Sellel on üks, püsiv aadress (`ClusterIP`). Kõik kirjad (võrguliiklus) saadetakse sinna ja postkontori töötaja (`selector`) suunab need õigele elanikule (Pod'ile).

Loome postkontorile reeglid failis `service.yaml`:
```yaml
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
* `type: ClusterIP`: See postkontor teenindab ainult maja-siseseid kirju.
* `selector`: Postitöötaja reegel – suuna kirjad edasi Pod'idele, mille silt on `app: veebiserver`.
* `ports`: Määrame, et postkontor võtab liiklust vastu pordil 80 ja suunab selle Pod'i pordile 80.

Avame postkontori:
```bash
kubectl apply -f service.yaml
```
**Oodatav tulemus:**
```
service/minu-nginx-service created
```
Kontrollime, kas postkontor töötab:
```bash
kubectl get service minu-nginx-service
```
**Oodatav tulemus:**
```
NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
minu-nginx-service   ClusterIP   10.108.111.44   <none>        80/TCP    15s
```
Väljund näitab, et meie teenusel on nüüd stabiilne, maja-sisene aadress (`CLUSTER-IP`).

---

### Kokkuvõte ja järgmised sammud

**Kus me oleme?** Oleme esimese osa lõpus. Sa oled edukalt loonud oma esimese rakenduse Kuberneteses!

Mida sa täna õppisid:
* Mõistad põhilisi tööriistu: **`kubectl`** on sinu pult ja **Minikube** on sinu Lego-linn.
* Tead Kubernetes'e põhilisi ehituskive: **Pod** on korter, **Deployment** on tark maja haldur ja **Service** on maja postkontor.
* Oskad luua töökindla ja skaleeritava rakenduse, mis on kättesaadav klastri sees.

Hetkel on meie rakendus veel välismaailma eest peidus. **Meie sarja teises osas** ehitame maja ette korraliku sissepääsu, kasutades **Ingress**'i, et rakendus turvaliselt avalikuks teha. Samuti vaatame, kuidas hallata rakenduse seadeid ja paroole.

Püsi lainel!
