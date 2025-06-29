---
title: "Kuidas ma oma rakenduse deploy'imise põrgust välja tõin – praktiline juhend"           # Required: Post title
author: "Kaur Kiisler"                   # Required: Author name
publishedAt: "29. juuni 2025"        # Required: Estonian format date
category: "Õpetused"                  # Required: Post category
tags: ["devops", "github"]          # Required: Array of tags
featured: false                         # Required: Boolean (only one post should be true)
excerpt: "See on lugu sellest, kuidas ma seadistasin täisautomaatse CI/CD toru GitHub Actionsi, Dockeri ja ühe virtuaalmasina (VM) abil."         # Optional: Custom excerpt (auto-generated if not provided)
imageUrl: "/media/github_vm.jpg" # Optional: Featured image URL
---
# Kuidas ma oma rakenduse deploy'imise põrgust välja tõin – praktiline juhend

Postituse kuupäev: 29. juuni 2025

Autor: Kogenud Arendaja

Tere tulemast lugema meie DevOps-seeria Uut artiklit! Täna ei räägi me teooriast. Räägime praktikast, vigadest ja sellest magusast tundest, kui pärast mitmeid ebaõnnestumisi hakkab lõpuks kõik tööle. See on lugu sellest, kuidas ma seadistasin täisautomaatse CI/CD toru GitHub Actionsi, Dockeri ja ühe virtuaalmasina (VM) abil. Mis veelgi olulisem, see on juhend, kuidas **sina** saad sama asja teha, vältides kõiki neid karisid, mille otsa mina komistasin.

See juhend on sinu jaoks, kui oled kunagi mõelnud:

- "Miks see töötab minu arvutis, aga mitte serveris?"
- "Kuidas ma saan oma API-võtmed turvaliselt serverisse ilma neid koodi sisse kirjutamata?"
- "Mu *deployment*-skript on muutunud nii keeruliseks, et ma kardan seda puudutada."

Unusta keerulised ja haprad lahendused. Ehitame midagi lihtsat, loogilist ja vastupidavat.

## Lõplik arhitektuur: lihtne ja töökindel süsteem

Enne kui sukeldume detailidesse, vaatame, milline näeb välja meie eesmärk. Pärast mitmeid iteratsioone ja peavalu jõudsin ma lahenduseni, mis on nii elegantne kui ka stabiilne.

1. **GitHub:** Meie koodi ja konfiguratsioonifailide kodu. Iga `git push main` käivitab maagia.
2. **GitHub Actions:** Meie automatiseerimise süda. See ehitab koodist Docker *image*'id.
3. **GitHub Container Registry (ghcr.io):** Turvaline koht, kus hoiame oma valmis ehitatud *image*'eid. See on nagu riiul, kust server võtab alati õige, testitud versiooni.
4. **Virtuaalmasin (VM):** Meie produktsiooniserver. See ei tee midagi muud, kui tõmbab valmis *image*'id ja käivitab need Docker Compose'iga.
5. **Docker Compose:** Konteinerite orkestreerimise tööriist serveris. See tagab, et meie *frontend*, *backend* ja *reverse proxy* töötavad harmooniliselt koos.
6. **Caddy:** Meie geniaalne *reverse proxy*, mis tegeleb automaatselt HTTPS-sertifikaatidega ja suunab liikluse õigesse konteinerisse.

**Põhiprintsiip:** Server ei ehita kunagi ise koodi. Ta ainult tarbib valmis artefakte (Docker *image*'eid). See vähendab drastiliselt võimalike vigade arvu produktsioonikeskkonnas.

## 1. Vundamendi valamine: ära jäta vahele ettevalmistust!

Enne koodi kirjutamist peab vundament olema tugev. Need on sammud, mille vahelejätmine maksab hiljem valusalt kätte.

### **TEE NII:** Kasuta serveris eraldiseisvat kasutajat

Ära kunagi tegutse `root`-kasutajana. Loo eraldi kasutaja oma rakenduse jaoks.

```
# Loo uus kasutaja (asenda 'appuser' endale sobivaga)
sudo adduser appuser

# Lisa kasutaja docker gruppi, et ta saaks käivitada Docker käske
sudo usermod -aG docker appuser

# Logi sisse uue kasutajana
su - appuser
```

### **ÄRA TEE NII:** Ära kasuta parooliga sisselogimist automatiseerimiseks

GitHub Actions peab saama sinu serverisse sisse logida ilma parooli küsimata. Selleks kasutame SSH-võtmeid.

1. **Genereeri spetsiaalne SSH-võti GitHub Actionsi jaoks OMA ARVUTIS:**

   ```
   # See võti on mõeldud AINULT GitHub Actionsi jaoks
   ssh-keygen -t ed25519 -C "github-actions" -f ~/.ssh/github_actions_deploy
   ```
   See loob kaks faili: `github_actions_deploy` (privaatne võti) ja `github_actions_deploy.pub` (avalik võti).

2. **Kopeeri avalik võti oma serverisse:**

   ```
   # See käsk lisab sinu avaliku võtme serveri autoriseeritud võtmete hulka
   ssh-copy-id -i ~/.ssh/github_actions_deploy.pub appuser@SINU_SERVERI_IP
   ```

3. **Lisa privaatne võti GitHub Secrets alla:**

   - Mine oma GitHubi repos: `Settings` > `Secrets and variables` > `Actions`.
   - Loo uus saladus nimega `SSH_PRIVATE_KEY`.
   - Kopeeri sinna **terve sisu** `~/.ssh/github_actions_deploy` failist, alustades `-----BEGIN OPENSSH PRIVATE KEY-----` ja lõpetades `-----END OPENSSH PRIVATE KEY-----`.

### **TEE NII:** Seadista tulemüür

Sinu server peaks olema avatud ainult vajalike portide kaudu.

```
# Luba SSH, HTTP ja HTTPS liiklus
sudo ufw allow OpenSSH
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

# Aktiveeri tulemüür
sudo ufw enable
```

## 2. Tööriistakast korda: konfiguratsioonide kuldreeglid

Sinu konfiguratsioonifailid (`Dockerfile`, `docker-compose.yml`) on sinu arhitektuuri plaanid. Hoia need puhtad, loogilised ja versioonihallatavad.

### Dockerfile'id: ehita targalt, mitte suurilt

**TEE NII:** Kasuta *multi-stage builds* See on kõige olulisem optimeerimine. Me ei taha produktsiooni viia arenduseks vajalikke pakette (nagu `vite`, `nodemon` jne).

**Näide: Frontend Dockerfile (`deployment/Dockerfile.frontend`)**

```
### EHITAMISE ETAPP ###
# Kasutame täisversiooni Node.js-ist, et ehitada meie rakendus
FROM node:18-alpine AS builder

WORKDIR /app
COPY package*.json ./
RUN npm ci

COPY . .

# Siin anname ehitamise ajal kaasa API urli
ARG VITE_API_URL
ENV VITE_API_URL=$VITE_API_URL

RUN npm run build

### PRODUKTSIOONI ETAPP ###
# Kasutame kerget NGINX serverit, et serveerida staatilisi faile
FROM nginx:1.25-alpine

# Kopeerime AINULT valmis ehitatud failid 'builder' etapist
COPY --from=builder /app/dist /usr/share/nginx/html

# Kopeerime oma NGINX konfiguratsiooni
COPY deployment/nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

**Tulemus:** Produktsiooni *image* on pisike ja turvaline, sest see ei sisalda `node_modules` kausta ega muid arendusvahendeid.

**ÄRA TEE NII:** Ära kasuta `latest` *tag*'i oma baas-*image*'itel. See on ettearvamatu. Kasuta alati spetsiifilist versiooni nagu `node:18-alpine`.

### Docker Compose: eralda arendus ja produktsioon

**TEE NII:** Kasuta kahte `docker-compose` faili.

- `docker-compose.yml`: Kohaliku arenduse jaoks. See kasutab `build` direktiivi ja *volume*'eid, et koodimuudatused kajastuksid kohe.
- `docker-compose.prod.yml`: Produktsiooni jaoks. See **ei ehita midagi**. See kasutab `image` direktiivi, et tõmmata valmis *image*'id otse registrist.

**Näide: `deployment/docker-compose.prod.yml`**

```
version: '3.8'

services:
  backend:
    # Tõmbame pildi registrist, kasutades tag'i, mille määrame .env failis
    image: ghcr.io/SINU_KASUTAJA/SINU_REPO/backend:${IMAGE_TAG:-latest}
    env_file: .env.production
    networks:
      - app-network
    restart: unless-stopped

  frontend:
    image: ghcr.io/SINU_KASUTAJA/SINU_REPO/frontend:${IMAGE_TAG:-latest}
    networks:
      - app-network
    restart: unless-stopped

  caddy:
    image: caddy:2-alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./Caddyfile:/etc/caddy/Caddyfile
      - caddy_data:/data
      - caddy_config:/config
    env_file: .env.production
    networks:
      - app-network
    restart: unless-stopped

networks:
  app-network:

volumes:
  caddy_data:
  caddy_config:
```

**ÄRA TEE NII:** Kõige hullem viga, mida ma tegin, oli proovida YAML-faile genereerida lennult `ssh` ja `cat` käskudega GitHub Actionsi töövoos. See on retsept katastroofiks. **Konfiguratsioon peab olema staatiline ja versioonihallatud.**

## 3. Automatiseerimise süda: lollikindel `deploy.yml`

Siin on lihtsustatud ja pommikindel GitHub Actionsi töövoog. See on jagatud kaheks loogiliseks tööks: `build-and-push` ja `deploy`.

```
# .github/workflows/deploy.yml
name: Deploy to Production

on:
  push:
    branches:
      - main
  workflow_dispatch:

env:
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}

jobs:
  build-and-push:
    name: Build and Push Docker Images
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write
    
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Log in to GitHub Container Registry
        uses: docker/login-action@v3
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Build and push frontend and backend images
        # ... (build-push-action sammud, mis on identsed eelmises vastuses tooduga) ...
        # Oluline on kasutada commit SHA-d tag'ina!
        tags: |
          ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}/backend:${{ github.sha }}
          ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}/backend:latest

  deploy:
    name: Deploy to VM
    needs: build-and-push
    runs-on: ubuntu-latest
    
    steps:
      - name: Setup SSH
        # ... (SSH seadistamise samm) ...

      - name: Deploy application on VM
        run: |
          ssh ${{ secrets.VM_USER }}@${{ secrets.VM_HOST }} "
            # 1. Loo rakenduse kaust, kui seda pole
            mkdir -p ~/app
            
            # 2. Loo .env.production fail otse serveris GitHub Secrets baasil
            echo 'Creating production environment file...'
            cat > ~/app/.env.production << EOF
            DOMAIN=${{ secrets.DOMAIN }}
            OPENAI_API_KEY=${{ secrets.OPENAI_API_KEY }}
            GEMINI_API_KEY=${{ secrets.GEMINI_API_KEY }}
            BASIC_AUTH_USER=${{ secrets.BASIC_AUTH_USER }}
            BASIC_AUTH_PASS=${{ secrets.BASIC_AUTH_PASS }}
            IMAGE_TAG=${{ github.sha }}
            EOF
            
            # 3. Kopeeri vajalikud konfiguratsioonifailid
            echo 'Copying deployment files...'
            scp ./deployment/docker-compose.prod.yml ${{ secrets.VM_USER }}@${{ secrets.VM_HOST }}:~/app/
            scp ./deployment/Caddyfile ${{ secrets.VM_USER }}@${{ secrets.VM_HOST }}:~/app/

            # 4. Logi sisse, tõmba ja käivita!
            cd ~/app
            echo 'Logging into GitHub Container Registry...'
            docker login ${{ env.REGISTRY }} -u ${{ github.actor }} -p ${{ secrets.GITHUB_TOKEN }}
            
            echo 'Pulling latest images with tag ${{ github.sha }}...'
            docker-compose -f docker-compose.prod.yml pull
            
            echo 'Restarting services...'
            docker-compose -f docker-compose.prod.yml up -d --force-recreate
            
            echo 'Cleaning up old images...'
            docker image prune -af
          "
      
      - name: Health check
        # ... (Tervisekontrolli samm, mis kontrollib, kas leht vastab) ...
```

**TEE NII:** Märgista oma *image*'id alati unikaalse identifikaatoriga, nagu *commit*i SHA (`${{ github.sha }}`). See tagab, et sa tead täpselt, milline koodiversioon serveris jookseb, ja teeb tagasipööramise trivialseks.

**ÄRA TEE NII:** Ära kunagi kasuta produktsioonis ainult `:latest` *tag*'i. See on nagu loterii – sa ei tea kunagi, millise versiooni sa tegelikult saad, eriti kui mitu inimest töötab sama projekti kallal.

## 4. Õppetunnid: kuidas mitte oma juukseid peast välja kitkuda

- **VIGA: "See töötab minu arvutis!"**
  - **PÕHJUS:** Sinu arenduskeskkond ja produktsioonikeskkond on erinevad. Sul on lokaalselt teised `node_modules` versioonid või erinevad keskkonnamuutujad.
  - **LAHENDUS:** Docker! Ja kahe eraldi `docker-compose` faili kasutamine. See tagab, et keskkonnad on võimalikult sarnased ja ainsad erinevused (nagu andmebaasi ühendus) on hallatud keskkonnamuutujatega.
- **VIGA: Saladuste hoidmine koodis või logides.**
  - **PÕHJUS:** Mugavus. On lihtne jätta API-võti koodi sisse.
  - **LAHENDUS:** GitHub Secrets on su parim sõber. Nagu näidatud `deploy.yml` failis, loome me `.env.production` faili otse serveris, kasutades turvaliselt edastatud saladusi. Lisa `.env*` kindlasti oma `.gitignore` faili!
- **VIGA: Konteinerid ei "näe" üksteist.**
  - **PÕHJUS:** Konteinerid on vaikimisi isoleeritud. Kui su *frontend* üritab suhelda *backend*'iga aadressil `localhost:3001`, siis see ebaõnnestub, sest `localhost` viitab konteineri enda sisemusse.
  - **LAHENDUS:** Docker Compose loob automaatselt ühise võrgu kõikidele teenustele, mis on defineeritud ühes failis. Kasuta teenuste nimesid hostinimedena. Sinu `Caddyfile`'is on `reverse_proxy backend:3001` – siin on `backend` teenuse nimi, mille Docker lahendab automaatselt õige konteineri IP-aadressiks.

## 5. Boonus: palu AI-lt abi nagu proff

Tänapäeval kasutame me kõik AI-assistente. Aga selleks, et saada head nõu, pead esitama hea küsimuse.

**ÄRA ütle:** "Mu *deployment* ei tööta, aita!"

**ÜTLE NII:**

> "Mul on probleem GitHub Actionsi *deployment*-töövooga. See ebaõnnestub "Deploy application on VM" sammus.
>
> **Eesmärk:** Tahan automaatselt *deploy*'da Reacti *frontend*'i ja Node.js *backend*'i Docker konteineritesse ühes VM-is, kasutades Caddy't *reverse proxy*'na.
>
> **Viga:**
>
> ```
> docker-compose: command not found
> ```
>
> **Minu `deploy.yml` fail:**
>
> ```
> # (Kopeeri siia oma relevantne YAML-kood)
> ```
>
> **Mida ma proovisin:** Kontrollisin, kas Docker on VM-i installitud. `docker --version` töötab, kui ma SSH-ga sisse login.
>
> Palun selgita, miks `docker-compose` käsku ei leita ja paku välja lahendus."

**Mida head sa tegid?**

1. Andsid täpse konteksti.
2. Jagad konkreetset veateadet.
3. Näitasid oma konfiguratsiooni.
4. Mainisid, mida oled juba proovinud.

See säästab sinu ja AI aega ning viib sind lahenduseni kordades kiiremini.

## Kokkuvõtteks

Automaatse CI/CD toru ehitamine ei pea olema raketiteadus. Hoides asjad lihtsad, loogilised ja järgides parimaid praktikaid, saad luua süsteemi, mida on rõõm kasutada ja lihtne hooldada.

**Sinu kolm kuldreeglit:**

1. **Automatiseeri kõik:** Manuaalsed sammud on vigade allikas.
2. **Konfiguratsioon on kood:** Hoia kõik oma `Dockerfile`, `docker-compose.yml` ja `*.yml` failid Git'is.
3. **Eralda mured:** Ehita ühes kohas (GitHub Actions), käivita teises (VM).

Loodan, et see põhjalik ülevaade, mis on sündinud reaalsetest lahingutest, aitab sul oma projekte enesekindlalt ja edukalt käima lükata. Edu!
