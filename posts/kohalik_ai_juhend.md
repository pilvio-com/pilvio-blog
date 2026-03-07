---
title: "Kuidas käivitada AI mudel oma arvutis"
author: "Kaur Kiisler"
publishedAt: "7. märts 2026"
category: "AI"
tags: ["AI", "kohalik AI", "Ollama", "Qwen", "Mac Studio"]
featured: false
excerpt: "Praktiline juhend, kuidas käivitada kohalik AI oma arvutis Ollama ja Qwen mudelitega, millist riistvara valida ning millal liikuda lokaalselt setup'ilt API-põhise lahenduse peale."
imageUrl: "/media/kohalik-ai.png"
slug: "kuidas-kaivitada-ai-mudel-oma-arvutis"
---

Sa ei pea kõiki AI-päringuid pilve saatma. Täna saab võimeka assistendi käima panna otse oma arvutis, nii et sinu failid, kood ja tootekontekst ei pea vaikimisi masinast lahkuma.

Üks lihtsamaid viise selleks on praegu `Ollama` koos Qwen 3.5 seeria mudelitega. Esimene paigaldus ja mudeli allalaadimine vajavad internetti, aga pärast seda saad sama setup'i kasutada ka offline. Kui sul on arvuti juba olemas, saad esimesed praktilised tulemused kätte umbes poole tunniga.

Kui ostad masina spetsiaalselt kohaliku AI jaoks, siis tasub Apple Siliconit tõsiselt vaadata. Eraldi NVIDIA kast annab endiselt kõige rohkem toorest GPU-jõudlust, aga Maci hinna, mugavuse, vaikuse ja ühtse mälu kombinatsioon on paljudele parema hinna-kvaliteedi suhtega.

---

## Miks üldse kohalik AI?

Enne kui käed külge paneme, tasub selgeks teha, millal kohalik AI on päriselt mõistlik valik.

### Privaatsus

Pilveteenuse puhul saadetakse sinu päring teise osapoole serverisse. Kohaliku mudeli puhul jäävad andmed vaikimisi sinu enda arvutisse. See on oluline siis, kui töötad koodi, sisemiste dokumentide, kliendiinfo või tundliku sisuga.

### Töökindlus

Kohalik mudel ei jää seisma lihtsalt sellepärast, et teenus on maas või internet on katki. Kui mudel on juba alla laaditud, saad sama töövoogu jätkata ka ilma ühenduseta.

### Kulud

Kui riistvara on sul juba olemas, on kohaliku AI marginaalkulu enamasti elekter ja aeg. Pilve-API on õige valik siis, kui vajad skaleerimist, jagatud teenust või suuremaid mudeleid, aga igapäevaseks katsetamiseks on kohalik setup sageli odavam.

### Madalam latentsus

Väiksemate ja keskmiste mudelite puhul on kohalik kasutus tihti vahetuma tundega kui pilv. Vastus ei pea üle võrgu edasi-tagasi liikuma ning korduvate arendajaülesannete puhul on see tunda.

### Kontroll

Sa valid ise mudeli, konteksti pikkuse, töötamisviisi ja selle, kas kasutada mõtlemisrežiimi või mitte. Sa ei ole seotud ühe teenuse hinnakirja, piirangute ega tootepoliitikaga.

Aus piirang on see, et kohalik AI ei asenda alati pilve. Kui vajad väga suuri mudeleid, koormuse jagamist, tiimiühist endpoint'i või tugevat SLA-d, muutub API-põhine teenus kiiresti paremaks variandiks. Selle juurde jõuame artikli lõpus tagasi.

---

## Mida vajad?

### Minimaalsed nõuded

AI mudeleid saab käivitada väga erinevatel masinatel. Küsimus ei ole niivõrd selles, kas see töötab, vaid kui suur mudel mahub, kui kiiresti see vastab ja kui mugav see kogemus on.

| Komponent | Miinimum | Soovituslik |
|---|---|---|
| **RAM / unified memory** | 8 GB | 16+ GB |
| **CPU** | Apple Silicon või x86_64 | 6+ tuuma |
| **GPU** | Pole kohustuslik | Apple Silicon või NVIDIA 8+ GB VRAM |
| **Ketas** | 10 GB vaba ruumi | 30+ GB |

CPU-only setup töötab, aga aeglasemalt. NVIDIA annab endiselt kõige rohkem toorest läbilaskevõimet. Apple Siliconi suur eelis on see, et sa ei pea ehitama eraldi Linuxi kasti, draivereid ja CUDA toolchain'i. Töötav, vaikne ja suhteliselt võimekas masin on kohe karbist väljas.

### Apple Silicon on praegu üks tugevamaid bang-for-buck valikuid

Kui sinu eesmärk on kohalik AI ilma ise masina kokkupanekuta, siis Apple Silicon on väga tugev valik. Põhjus pole ainult GPU-s, vaid ühtses mälus: suur osa lokaalse LLM-i töötamisest kasutab sama mälupakki, mida jagavad CPU ja GPU. See teeb suuremad mudelid realistlikuks ka siis, kui klassikalise eraldiseisva GPU VRAM jääks muidu kitsaks.

Siin on kaks Mac Studio varianti, mis on kohaliku AI vaatest praegu huvitavad:

| Masin | USA alghind | Miks see on huvitav |
|---|---|---|
| **Mac Studio (M4 Max)** | **2 400 €** | 14-tuumaline CPU, 32-tuumaline GPU, 36 GB unified memory. Väga hea 4B-9B klassi mudeliteks, arenduseks ja igapäevaseks tööks. |
| **Mac Studio (M3 Ultra)** | **4 800 €** | 28-tuumaline CPU, 60-tuumaline GPU, 96 GB unified memory, 1 TB SSD. See on juba tõsine lokaalse AI tööjaam 27B-35B klassi mudelitele, pikemale kontekstile ja raskematele workflow'dele. |

M3 Ultra konfiguratsioon on siinkohal eriti oluline. `96 GB` unified memory tähendab, et umbes viie tuhande euro eest saad masina, millega saab juba päriselt tööd teha, mitte ainult 4B demodega mängida. Kui eesmärk on käitada suuremaid kohalikke mudeleid, teha RAG-i, agent-workflow'sid või arendada privaatseid sisemisi tööriistu, on see väga tugev plug-and-play valik. Soovi korral saab sama masina konfigureerida kuni `256 GB` unified memory'ni.

Kui tahad maksimaalset tokenit sekundis numbrit iga euro eest, ehitad tõenäoliselt Linuxi ja NVIDIA peale. Kui tahad aga vaikselt laua peal töötavat masinat, mida on lihtne hallata ja mis mahutab palju kasutatavat mälu, on Mac Studio praegu raske ületada.

### Kui suur mudel sinu masinasse mahub?

Täpne piir sõltub kvantisatsioonist, konteksti pikkusest, sellest kas mõtlemisrežiim on sees ning kui palju tööst jõuab GPU-le. Seega on mõistlikum kasutada allolevat tabelit rusikareeglina, mitte absoluutse tõena.

| Mudel | Sobiv masinaklass | Millal valida |
|---|---|---|
| `qwen3.5:0.8b` | CPU-only, 8 GB RAM | Testimiseks, väga lihtsad ülesanded |
| `qwen3.5:2b` | 8-16 GB RAM | Tõlkimine, lühikesed kokkuvõtted, lihtne abi |
| `qwen3.5:4b` | 16+ GB RAM, Apple Silicon, 6-8 GB GPU | Vaikimisi soovitus enamikele |
| `qwen3.5:9b` | Apple Silicon 24+ GB või 8-12 GB GPU | Parem kvaliteet, mõistlik kiirus |
| `qwen3.5:27b` | 24+ GB GPU või 64+ GB unified memory | Tõsisem analüüs ja koodiabi |
| `qwen3.5:35b-a3b` | 24+ GB GPU või 96+ GB unified memory | Suurem kvaliteet, raskemad kohalikud workflow'd |

Oluline nüanss: suure mudeli allalaadimissuurus ei ole sama mis vajalik GPU mälumaht. Samuti tõstab pikem kontekstiaken mälunõuet kiiresti. Kui mingi mudel "mahutab ära", ei tähenda see veel, et ta teeb seda hästi 128K kontekstiga.

---

## Samm-sammult paigaldamine

### 1. Paigalda Ollama

[Ollama](https://ollama.com) on kõige lihtsam viis kohalike AI mudelite käivitamiseks. See haldab mudelite allalaadimist, käivitamist ja API-d sinu eest.

**macOS:**

Laadi alla [ollama.com/download](https://ollama.com/download) või kasuta Homebrew'd:

```bash
brew install ollama
```

**Linux:**

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

**Windows:**

Laadi alla installer aadressilt [ollama.com/download](https://ollama.com/download) ja käivita see.

Kontrolli, et paigaldus on korras:

```bash
ollama --version
```

Apple Siliconi puhul kasutab Ollama masina GPU võimekust automaatselt. Sul ei ole vaja eraldi CUDA-d ega draiverimajandust.

### 2. Tõmba mudel

Kõige praktilisem vaikimisi soovitus on `qwen3.5:4b`. See on piisavalt kerge, et joosta paljudes masinates mugavalt, ja piisavalt võimekas, et sellest oleks päriselt kasu.

```bash
ollama pull qwen3.5:4b
```

Kui sul on vähem mälu või soovid kõige kiiremat vastamist:

```bash
ollama pull qwen3.5:2b
ollama pull qwen3.5:0.8b
```

Kui sul on tugevam masin:

```bash
ollama pull qwen3.5:9b
ollama pull qwen3.5:27b
ollama pull qwen3.5:35b-a3b
```

Viimane neist on juba tõsine allalaadimine. `qwen3.5:35b-a3b` on Ollamas umbes `24 GB`, seega arvesta nii kettaruumi kui ka mälunõudlusega.

### 3. Alusta vestlust

```bash
ollama run qwen3.5:4b
```

Proovi alustuseks midagi lihtsat ja stabiilset:

```text
>>> Selgita lühidalt, mis vahe on SSD-l ja HDD-l.
```

Vestlusest väljumiseks kirjuta `/bye` või vajuta `Ctrl+D`.

### 4. Lülita välja mõtlemisrežiim, kui vajad kiiremat vastust

Qwen 3.5 mudelid oskavad vajadusel "mõelda" enne vastamist. Keerulisemate ülesannete puhul võib see kvaliteeti parandada, aga igapäevaseks tööks tahad tihti võimalikult otsest vastust.

Interaktiivses sessioonis saad selle välja lülitada nii:

```text
>>> /set nothink
```

Programmiliselt saad sama mõtte rakendada ka päringus endas, näiteks:

```bash
ollama run qwen3.5:4b --think=false "Kirjuta kolm varianti viisakast järelmeilist"
```

See on väike detail, aga praktilises töös muudab tunnetust palju.

---

## Praktiline kasutamine

### Vestlus terminalis

```bash
ollama run qwen3.5:4b
```

```text
>>> Kirjuta lühike Python skript, mis arvutab Fibonacci arve.

def fibonacci(n):
    a, b = 0, 1
    result = []
    for _ in range(n):
        result.append(a)
        a, b = b, a + b
    return result

print(fibonacci(10))
```

```text
>>> Muuda see nüüd generaatoriks.
```

Samal sessioonil hoiab mudel eelmist konteksti meeles, seega saad vastuseid järjest täpsustada.

### API kaudu (`curl`)

Ollama pakub HTTP API-t, mille peale on lihtne ehitada skripte ja sisemisi tööriistu:

```bash
curl http://localhost:11434/api/chat -d '{
  "model": "qwen3.5:4b",
  "messages": [
    {"role": "user", "content": "Tõlgi inglise keelde: Tere, kuidas läheb?"}
  ],
  "think": false,
  "stream": false
}'
```

Vastus tuleb JSON-ina:

```json
{
  "message": {
    "role": "assistant",
    "content": "Hello, how are you?"
  }
}
```

### Python skript (OpenAI SDK)

Kuna Ollama toetab OpenAI-ühilduvat liidest, saad kasutada sama harjumuspärast kliendikoodi ka kohaliku mudeli vastu:

```bash
pip install openai
```

```python
from openai import OpenAI

client = OpenAI(
    base_url="http://localhost:11434/v1",
    api_key="ollama",
)

vastus = client.chat.completions.create(
    model="qwen3.5:4b",
    messages=[
        {"role": "user", "content": "Kirjuta SQL päring, mis leiab kõik aktiivsed kasutajad"}
    ],
)

print(vastus.choices[0].message.content)
```

Praktiline pluss on see, et kohalik prototüüp ja hilisem pilve-API ei pea olema kaks eri maailma. Sageli piisab `base_url` ja `api_key` vahetusest. Kõik teenusepakkujate erivõimalused ei pruugi olla 1:1 samad, aga tavalise chat/completions töövoo jaoks on see väga mugav.

### Lihtsad kasutusjuhud

- Koodiabi ja shelli käskude selgitamine
- Sisemiste tekstide kokkuvõtted ja ümberkirjutamine
- Tõlkimine eesti ja inglise keele vahel
- E-kirjade, teavituste ja tehnilise dokumentatsiooni mustandid

---

## Kui midagi ei tööta ootuspäraselt

Enamik pettumusi kohaliku AI-ga taandub kolmele asjale: mudel jooksis CPU peale, kontekstiaken on liiga suur või valiti lihtsalt liiga raske mudel.

- `ollama ps` aitab kontrollida, kas mudel jookseb GPU peal ja kui palju ressursse ta kasutab.
- `ollama list` näitab, mis mudelid sul tegelikult olemas on.
- Kui vastused on ootamatult aeglased, alusta uuesti `4b` või `2b` mudelist ja kontrolli, kas suurem mudel kukkus CPU peale.
- Pikem kontekstiaken tõstab mälunõuet. Kui mudel töötab 8K kontekstiga, ei tähenda see automaatselt, et sama kogemus püsib 128K juures.
- Kui sinu rakendus ei saa API-st vastust, kontrolli, et `http://localhost:11434` oleks üldse üleval.

---

## Milline mudel valida?

Kui tahad lihtsalt kiiret soovitust, siis kasuta seda:

| Sinu vajadus | Soovitus |
|---|---|
| Lihtsad küsimused, tõlkimine | `qwen3.5:2b` |
| Igapäevane kasutus, koodiabi | `qwen3.5:4b` |
| Parem kvaliteet, aga endiselt mõistlik kiirus | `qwen3.5:9b` |
| Tõsisem kohalik töö Apple Siliconi või suurema GPU-ga | `qwen3.5:27b` |
| Maksimaalne kvaliteet kohalikus masinas | `qwen3.5:35b-a3b` |

Kui sul on `96 GB` Mac Studio, siis oled juba klassis, kus 27B ja 35B mudelid ei ole enam lihtsalt demo, vaid täiesti kasutatav tööriist. Minu hinnangul on see koht, kus kohalik AI muutub "lahedast katsetusest" päris töövahendiks.

---

## Kui kohalik AI jääb kitsaks

Kohalik setup on suurepärane algus. Sellega saad aru, millised mudelid, promptid ja tööriistad sinu jaoks tegelikult töötavad. Mingil hetkel tekib aga tihti uus vajadus:

- sul ei ole endal piisavalt tugevat GPU-d
- tahad sama teenust kasutada kogu tiimiga
- vajad ühtset arveldamist, endpoint'i ja uptime'i
- sa ei taha kõige raskemat töökoormust oma lauaarvutile jätta

Siis on loomulik järgmine samm API.

### Pilvio AI API kui järgmine aste

Pilvio mõte ei ole sundida valima "kas kohalik või pilv". Mõistlikum tee on tihti see:

1. katseta kohalikult Ollamaga
2. ehita esimene workflow valmis
3. kui vajad rohkem läbilaskevõimet või jagatud teenust, vaheta `base_url`

See näeb koodis välja nii:

```python
# Kohalik (Ollama)
client = OpenAI(
    base_url="http://localhost:11434/v1",
    api_key="ollama",
)

# Pilvio AI API (pilves)
client = OpenAI(
    base_url="https://api.pilvio.com/v1",
    api_key="sinu-pilvio-api-voti",
)
```

See ongi põhjus, miks me tahtsime selles artiklis alustada kohalikust setup'ist. Kui arendaja saab sama mental model'i ja peaaegu sama kliendikoodi kasutada mõlemas suunas, on üleminek palju vähem valus.

### Miks see Pilvio kontekstis huvitav on?

- Eesti ettevõte ja eestikeelne tugi
- Eestis asuv taristu
- arveldamine eurodes
- OpenAI-ühilduv API loogika
- sobib hästi olukorda, kus lokaalne prototüüp tahab minna tiimikasutusse

Kui sul ei ole kodus GPU-d, aga tahad siiski sama arendajaliidest kasutada, siis on see praktiline tee edasi.

### Aita kogukonda ehitada

Selle postituse teine eesmärk on kogukond. Kui katsetad kohalikku AI-d Eestis, kirjuta meile, milline setup sul töötab: MacBook, Mac Studio, NVIDIA kast või midagi muud. Tahame Pilvio blogis jagada reaalseid Eesti kasutusjuhtumeid, mitte ainult laborijuttu.

---

## Kokkuvõte

Kohaliku AI käivitamine ei ole enam ainult entusiastide teema. Väiksemad Qwen 3.5 mudelid jooksevad tänapäeval väga tavalisel raual ja Apple Silicon on muutnud suuremate mudelite lokaalse kasutamise oluliselt realistlikumaks.

Kui tahad kõige lihtsamat algust, piisab kolmest käsust:

```bash
ollama pull qwen3.5:4b
ollama run qwen3.5:4b
ollama ps
```

Kui sul on umbes `5000` eurot eelarvet ja eesmärk on teha kohaliku AI-ga juba päris tööd, siis `Mac Studio (M3 Ultra, 96 GB unified memory)` on praegu üks tugevamaid masinaid, mida tasub vaadata. Kui alustad kohalikult ja tahad hiljem sama loogikat pilve viia, siis Pilvio AI API on selle loomulik jätk.

Kas sul on küsimusi või tahad oma setup'i kogemust jagada? Kirjuta meile [info@pilvio.com](mailto:info@pilvio.com) või külasta [pilvio.com](https://pilvio.com).
