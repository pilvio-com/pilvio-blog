---
title: "Milline kohalik AI-mudel on parim? Testisime viit mudelit Mac Studio peal"
author: "Kaur Kiisler"
publishedAt: "3. aprill 2026"
category: "AI"
tags: ["AI", "kohalik AI", "LLM", "benchmark", "Apple Silicon", "Ollama"]
featured: false
excerpt: "Pilvio meeskond testis viit avatud LLM-mudelit reaalsetel tööülesannetel — kodeerimisest ärianalüüsini. Jagame metoodikat, tulemusi ja praktilist soovitust igale Apple Silicon konfiguratsioonile."
imageUrl: "/media/llm-test.png"
slug: "milline-kohalik-ai-mudel-on-parim"
---

# Milline kohalik AI-mudel on parim? Testisime viit mudelit Mac Studio peal

*Pilvio meeskond testis viit avatud LLM-mudelit reaalsetel tööülesannetel -- kodeerimisest ärianalüüsini. Jagame metoodikat, tulemusi ja praktilist soovitust igale Apple Silicon konfiguratsioonile.*

---

Kohalikud keelemudelid (LLM-id) on 2026. aastal jõudnud punkti, kus need pole enam pelgalt tehnilised huviväärsused. Õige riistvara ja tarkvara kombinatsiooniga saab arendaja oma laual jooksutada mudeleid, mis vastavad kvaliteedilt pilveteenustele. Seda ilma andmeid välja saatmata, ilma igakuise arvet saamata ja ilma rate limit'idesse jooksmata.

Aga milline mudel valida? Avatud mudeleid on kümneid ja uusi tuleb iga nädal. Benchmarkide tabelid lubavad kõike, aga päris töös selgub tihti midagi muud. Tegime ära testi, mida benchmarkid ei näita: panime viis mudelit lahendama **kaheksat reaalset tööülesannet** ja mõõtsime nii kvaliteeti kui kiirust.

## Testimise ülesehitus

### Riistvara

Testid jooksid **Mac Studio M3 Ultra** peal — Apple'i tippklassi tööjaam 96 GB ühismäluga ja 819 GB/s mäluribilaiusega. Mudeleid teenindas Ollama (versioon 0.20+), mis kasutab Apple Silicon peal MLX framework'i.

### Testitud mudelid

| Mudel            | Suurus kettalt | Tüüp              | Arendaja        |
|------------------|----------------|-------------------|-----------------|
| **qwen3-coder:30b**  | 18 GB          | MoE (3B aktiivne) | Alibaba         |
| **qwen3-coder-next** | 51 GB          | MoE (3B aktiivne) | Alibaba         |
| **gemma4:26b**       | 17 GB          | MoE (4B aktiivne) | Google DeepMind |
| **gemma4:31b**       | 19 GB          | Dense             | Google DeepMind |
| **devstral:latest**  | 14 GB          | Dense             | Mistral AI      |

Kõik mudelid jooksutati Ollama default tag'iga, ilma kvanteerimist käsitsi valimata. Testid viis läbi ja kokkuvõtted tegi Claude Code. Tulemusi valideerisime OpenAI Codex’iga. Lisavõrdlusena jooksutasime samu teste ka **Claude Opus 4.6** peal (Anthropic API), et anda kohalikele mudelitele kontekst. Claude'i tulemusi tuleb võtta reservatsiooniga: see on massiivne pilvemudel, mille jooksutamine maksab raha, ja hindaja hindab iseennast.

### Kaheksa testi

Disainisime testid nii, et igaüks mõõdaks **erinevat oskust**, mida arendaja igapäevaselt vajab:

| \# | Test                                             | Mida mõõdab                             |
|---|--------------------------------------------------|-----------------------------------------|
| 1 | Express.js middleware refactoring TypeScript'iks | Koodi kvaliteet, turvanüansid           |
| 2 | FastAPI „forgot password" flow mitmes failis     | Agentne võimekus, failideülene kooskõla |
| 3 | Concurrency bugi leidmine RateLimiter klassist   | Reasoning sügavus, veatuvastus          |
| 4 | Eesti pilveettevõtte K8s-teenuse strateegia      | Äriline mõtlemine, turuanalüüs          |
| 5 | Terraform konfiguratsiooni turvaülevaatus        | Detailsus, severity hinnang             |
| 6 | Eestikeelne WireGuard VPN artikkel               | Keelekvaliteet, tehniline täpsus        |
| 7 | Multi-tenant SaaS billing PostgreSQL skeem       | Struktuurne disain, andmebaasiteadmised |
| 8 | Kiire debugging — RecursionError diagnoos        | Latency, täpne juurpõhjuse tuvastamine  |

Iga test sai hindeks 1–5, kus 5 tähendab production-ready tulemust ja 1 vale lähenemist. Kõik testid jooksutati `ollama run MODEL --verbose` käsuga, et mõõta ka token'ite genereerimise kiirust.

## Tulemused

### Ülevaade

| Mudel            | Skoor | tok/s |  Mälu | Koguaeg | Parim test                         |
|------------------|:-----:|------:|------:|--------:|------------------------------------|
| **gemma4:31b**       | **32/40** |   ~25 | 19 GB |  718.8s | DB disain (5/5), ärianalüüs (5/5)  |
| **qwen3-coder-next** | **32/40** |   ~36 | 51 GB |  423.5s | Concurrency (5/5), DB disain (5/5) |
| gemma4:26b       | 28/40 |   ~73 | 17 GB |  459.4s | Ärianalüüs (5/5)                   |
| qwen3-coder:30b  | 27/40 |   ~92 | 18 GB |  108.6s | Kodeerimine (4/5 × 3 testi)        |
| devstral:latest  | 26/40 |   ~41 | 14 GB |  206.7s | Bugi leidmine (4/5)                |

Esimene üllatus: kaks mudelit jagavad esikohta **täpselt sama skooriga (32/40)**, aga täiesti erinevat tüüpi ülesannetes. gemma4:31b — 19 GB Dense mudel — saab sama tulemuse mis 51 GB qwen3-coder-next. See on 2.7-kordne vahe mälukasutuses sama kvaliteedi juures.

Teine üllatus: kiiruse ja kvaliteedi vahel on selge kompromiss. Kiireim mudel (qwen3-coder:30b, ~92 tok/s) ja kvaliteetseimad (gemma4:31b ja qwen3-coder-next, ~25–36 tok/s) erinevad kiiruselt 2.5–3.7 korda. Koguajas on vahe veelgi suurem: 108.6 sekundit versus 423–719 sekundit.

### Mälu-efektiivsus

Üks number, mida benchmarkid tavaliselt ei näita: mitu kvaliteedipunkti saab iga gigabaidi mälu kohta?

| Mudel            | Skoor |  Mälu | Punkti/GB |
|------------------|:-----:|------:|----------:|
| devstral         |  26   | 14 GB |      **1.86** |
| gemma4:31b       |  32   | 19 GB |      1.68 |
| gemma4:26b       |  28   | 17 GB |      1.65 |
| qwen3-coder:30b  |  27   | 18 GB |      1.50 |
| qwen3-coder-next |  32   | 51 GB |      0.63 |

Puhast punkti/GB suhet vaadates võidab devstral — väikseim mudel annab iga gigabaidi kohta kõige rohkem. Aga see number ei räägi kogu lugu. Kui sul on 32 GB masin ja vaja parimat tulemust mis RAM’ mahub, on gemma4:31b (32/40, 19 GB) oluliselt targem kui devstral (26/40, 14 GB) — 6 punkti kvaliteedivahet ei kompenseeri 5 GB mälukokkuhoid. Kõige madalam efektiivsus on qwen3-coder-next'il: sama 32/40 skoor mis gemma4:31b, aga 2.7 korda rohkem mälu.

### Kiiruse detailid

| Mudel            | Genereerimiskiirus | Prompti töötlus | Kommentaar                               |
|------------------|-------------------:|----------------:|------------------------------------------|
| qwen3-coder:30b  |        88–93 tok/s |  610–1147 tok/s | Stabiilselt kiireim                      |
| gemma4:26b       |        64–79 tok/s |  283–1618 tok/s | Ebastabiilne — ühes testis 3 min 48 sek! |
| devstral:latest  |        40–41 tok/s |  335–3300 tok/s | Stabiilne, aga aeglane                   |
| qwen3-coder-next |        36–36 tok/s |   220–651 tok/s | Stabiilne, aga 51 GB mälu hinnaga        |
| gemma4:31b       |        24–26 tok/s |   201–257 tok/s | Aeglaseim, kõige ühtlasem                |

Gemma4:26b anomaalia väärib mainimist: test 3-s (bugi leidmine) genereeris mudel **14 515 tokenit** ja kulutas selleks peaaegu neli minutit. Mudeli sisemise mõtteprotsessi token'id lekkisid väljundisse ja ta jäi sisuliselt „mõtteringi." Agentsetes workflow'des — näiteks kui Claude Code jooksutab mudelit tsüklis — tähendab see, et üks selline episood võib kogu sessiooni mitmeks minutiks seisata.

## Detailsem vaade tulemustele

### Kaks võitjat, üks valik

gemma4:31b ja qwen3-coder-next jagavad esikohta, aga vaatame, kus nad erinevad:

| Test             | gemma4:31b | qwen3-coder-next |
|------------------|:----------:|:----------------:|
| Concurrency bugi |    3/5     |       **5/5**        |
| Ärianalüüs       |    **5/5**     |       4/5        |
| DB disain        |    **5/5**     |       **5/5**        |
| Eesti keel       |    **4/5**     |       3/5        |
| FastAPI kood     |    **4/5**     |       3/5        |
| Debug kiirus     |   18.1s    |       **5.8s**       |
| Mälu             |   **19 GB**    |      51 GB       |

Need ei ole vahetatavad mudelid, vaid erinevad tööriistad. qwen3-coder-next on parem seal, kus vaja sügavat tehnilist reasoning'ut: concurrency analüüsis sai ta ainsa lokaalse mudelina maksimumhinde (5/5), pakkudes õpiku-kvaliteediga selgitust koos stress-testi ja per-client lock'i soovitusega. gemma4:31b on parem seal, kus vaja laiapõhjalist mõtlemist — ärianalüüsis arvutas ta ARPU välja antud andmete põhjal ja tegi nüansikaid turusoovitusi Eesti kontekstis.

### gemma4:31b — kompaktne kvaliteediliider

Google DeepMind'i Dense mudel oli testide üllataja.

**Andmebaasi disain (5/5):** skeem peegeldab Stripe'i arhitektuuri — eraldi Plan ja Price tabelid versioneerimiseks, NUMERIC(19,4) rahaväljadele, subscription_events proration jälgimiseks. See on tase, mida võiks oodata kogenud backend-arendajalt.

**Ärianalüüs (5/5):** arvutas antud andmete põhjal ARPU välja, mainis „resume-driven development" ohtu hüperskaalarite kontekstis ja pakkus alternatiivina GPU hosting'ut.

**Eesti keel (4/5):** parim lokaalsetest, aga mitte vigadeta — kirjutas „rautvara" pro „riistvara" ja „sülearut" pro „sülearvuti". Kasutatav draft, vajab toimetamist.

### qwen3-coder-next — sügav mõtleja

Alibaba suurim avatud coding-mudel (80B parameetrit, 3B aktiivset) nõuab 51 GB mälu, aga tasub end ära spetsiifilistes ülesannetes.

**Concurrency analüüs (5/5):** ainus lokaalne mudel, kes andis õpiku-kvaliteediga TOCTOU selgituse koos praktilise stress-testiga ja per-client lock'i lahendusega. Teised mudelid leidsid probleemi osaliselt, aga mitte nii põhjalikult.

**DB disain (5/5):** täielik billing lifecycle koos trigger'itega — plans, tiers, subscriptions, proration, usage metering, payments, audit trail.

**Debug (4/5, 5.8 sekundit):** kolm korda kiirem vastus kui gemma4:31b debug-testis, konkreetse juurpõhjuse ja kolme fix-variandiga.

Nõrkus: test 2-s (FastAPI multi-file) tegi kriitilise vea — O(n) bcrypt token lookup kõigi kasutajate üle, mis on sisuliselt DoS vektor. Ka suurem mudel ei ole immuunne loogikavigadele.

### qwen3-coder:30b — igapäevane tööhobune

~92 tok/s ja kokku ainult 108.6 sekundit — tüüpiline vastus tuleb 5–25 sekundiga. Kodeerimisel on ta tugev: SQL injection parandatud, TypeScript tüübid lisatud, Bearer prefix käsitletud.

Nõrkused ilmnesid reasoning'us: debug-testis (2/5) ei tuvastanud juurpõhjust, ja Terraform review'st jäi osa probleeme leidmata. Need on ülesanded, kus kiirus ei asenda sügavust.

### devstral — väikseim

14 GB ja ühtlane töökiirus. Paistis silma bugi leidmisel: ainus mudel peale qwen3-coder-next'i, kes tuvastas TOCTOU race condition'i terminoloogiliselt õigesti.

## Milline mudel millisele masinale?

Apple Silicon'i ühismälu tähendab, et kogu RAM on GPU jaoks kättesaadav — erinevalt eraldiseisvatest GPU-dest, kus mudel peab mahtuma eraldi VRAM-i. Rusikareegel: mudeli jaoks on kasutatav **kogu RAM miinus 6–8 GB** (macOS, Ollama ja taustatöö). Kui mudel ületab selle piiri, hakkab macOS swap'ima ja jõudlus langeb järsult.

### 16 GB (MacBook Air, MacBook Pro baas)

Kasutatav mudelitele: ~8–10 GB. Ükski testitud mudel ei mahu mugavalt. Reaalsed valikud on väiksemad mudelid (7–8B parameetrit), mida siin ei testitud, või pilvepõhised API-d.

### 32 GB (MacBook Pro, Mac Mini)

Kasutatav mudelitele: ~24–26 GB. Enamik testitud mudeleid mahub, aga ainult üks korraga.

| Eesmärk         | Mudel           | Mälu  |
|-----------------|-----------------|-------|
| Parim kvaliteet | **gemma4:31b**      | 19 GB |
| Parim kiirus    | qwen3-coder:30b | 18 GB |

Soovitus: **gemma4:31b** üksinda. 19 GB mudel jätab 8 GB süsteemile ja brauserile. Kaks suurt mudelit korraga ei mahu.

### 64 GB (MacBook Pro Max, Mac Studio)

Kasutatav mudelitele: ~56–58 GB. Kaks mudelit korraga — kohene vahetus ilma laadimisajata.

| Eesmärk       | Mudelid                      | Kokku mälu |
|---------------|------------------------------|------------|
| **Parim combo**   | **gemma4:31b** + **qwen3-coder:30b** | 37 GB      |
| Max reasoning | qwen3-coder-next üksi        | 51 GB      |

Soovitus: **gemma4:31b + qwen3-coder:30b** kõrvuti. Igapäevaseks tööks kiire mudel, oluliste otsuste jaoks kvaliteetne mudel. Ollama hoiab mõlemat mälus ja vahetus on kohene.

### 96 GB (Mac Studio Ultra, Mac Pro)

Kasutatav mudelitele: ~88–90 GB. Kolm suurt mudelit korraga.

| Eesmärk       | Mudelid                                         | Kokku mälu |
|---------------|-------------------------------------------------|------------|
| **Ideaalne trio** | **qwen3-coder-next** + **gemma4:31b** + **qwen3-coder:30b** | 88 GB      |

Soovitus: kolm mudelit „soojana":

1. **qwen3-coder:30b** (18 GB, ~92 tok/s) — igapäevane kiire kodeerimine
2. **qwen3-coder-next** (51 GB, ~36 tok/s) — sügav analüüs ja reasoning
3. **gemma4:31b** (19 GB, ~25 tok/s) — ärianalüüs, eestikeelne sisu, arhitektuur

96 GB puhul ei ole mälu-efektiivsus oluline — mahub kõik. Oluline on ainult see, et igal ülesandel on parim tööriist käeulatuses.

## Mida me ei testinud

See test kattis ainult ühe prompti mudelile korraga (zero-shot). Päris tööelus kasutad mudeleid dialoogis — multi-turn kvaliteet võib mudelist mudelisse tugevalt erineda. Samuti ei testinud me agentset tool-calling'ut, mis on eraldi testikomplekti väärt.

## Praktiline soovitus

Testimise põhjal soovitame **mitme mudeli strateegiat**, mitte universaalset valikut:

```
Tavaline feature, bugfix, refactor     →  qwen3-coder:30b    (~5-25 sek)
Koodiülevaatus, DB disain              →  gemma4:31b          (~30-120 sek)
Concurrency, sügav debugging           →  qwen3-coder-next    (~5-55 sek)
Äri- ja arhitektuurianalüüs            →  gemma4:31b          (~60-90 sek)
Eestikeelne draft                      →  gemma4:31b          (vajab toimetamist)
Kriitilised otsused                    →  Claude API           (pilv, maksuline)
```

Suurim õppetund: **universaalset parimat mudelit ei ole**. Kaks mudelit said täpselt sama skoori (32/40), aga erinevad ülesannetes, mälukasutuses ja kiiruses. Hea workflow kasutab mitut mudelit — nii nagu hea arendaja kasutab mitut tööriista.

Ja üks asi, mis kehtib kõigi kohta: **ükski mudel — ka pilvepõhised — ei ole täielikult usaldusväärne eestikeelse sisu loomises ilma emakeelse toimetajata.**

Kõik testid jooksid kohalikult, ilma andmeid välja saatmata. See on kohalike mudelite peamine eelis pilveteenuste ees: sinu kood, sinu andmed, sinu kontroll.

---

*Pilvio pakub Eesti ettevõtetele pilvetaristut, mille peal saab jooksutada nii veebiteenuseid kui kohalikke AI-lahendusi. Kui tahad oma AI-töövooge Eesti territooriumil hoida, [võta meiega ühendust](https://pilvio.com).*

*Testide täistulemused ja üksikasjalik metoodika on saadaval [meie GitHubis](https://github.com/pilvio).*