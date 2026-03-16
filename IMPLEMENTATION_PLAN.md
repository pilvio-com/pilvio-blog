# Pilvio Blog Automation — Implementation Plan

Two tools, one repo. Git is the interface between them.

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                     pilvio-blog repo (GitHub)                   │
│                                                                 │
│  posts/              ← artiklid (md + frontmatter)              │
│  media/              ← illustratsioonid (png)                   │
│  marketing/                                                     │
│    queue/            ← FB postitused ootavad kinnitamist (json)  │
│    published/        ← avaldatud postitused (json, ajalugu)     │
│    newsletters/      ← newsletter draftid (md)                  │
│    reports/          ← nädalased raportid (md)                   │
│    config.json       ← seaded (UTM defaults, posting schedule)  │
│  .claude/                                                       │
│    CLAUDE.md         ← content agent juhised + brändi hääl      │
│    commands/                                                     │
│      write-article.md  ← artikli kirjutamise skill              │
│      marketing.md      ← marketingi agendi skill                │
│  scripts/                                                       │
│    fb-publish.js     ← FB Graph API publisher                   │
│    fb-insights.js    ← FB insights collector                    │
│    send-report.js    ← email report sender                      │
│  .github/workflows/                                             │
│    sync-to-s3.yml    ← olemasolev S3 sync                       │
│    marketing.yml     ← marketing automation trigger             │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Tool 1: Content Agent

### Mis see on

Claude Code projekt koos custom command'iga. Sina jooksutad Claude Code'd blog repo peal ja ütled: `/write-article`. Claude küsib teema ja konteksti, kirjutab artikli, genereerib illustratsiooni, loob PR-i.

### Workflow

```
Sina: claude "/write-article"
  │
  ├─ Claude küsib: "Mis teema? Mis kontekst?"
  │  Sina vastad: "AI agentide monitooring. Kontekst: Langfuse,
  │                Prometheus, minu kogemus võlahalduse agendiga"
  │
  ├─ Claude kirjutab artikli (posts/ai_agentide_monitooring.md)
  │   - Loeb CLAUDE.md → teab brändi häält, formaati, stiili
  │   - Loeb olemasolevaid artikleid → hoiab järjepidevust
  │   - Genereerib frontmatter automaatselt
  │
  ├─ Claude genereerib illustratsiooni prompti
  │   - Kutsub OpenAI DALL-E API'd (või sinu valitud image API'd)
  │   - Salvestab media/ kausta, 1200x675px
  │
  ├─ Claude loob branchi ja PR-i
  │   - Branch: content/ai-agentide-monitooring
  │   - PR title: "Uus artikkel: AI agentide monitooring"
  │   - PR description: artikli kokkuvõte + illustratsioon preview
  │
  └─ Sina vaatad PR-i üle
     ├─ Kõik OK → merge → S3 sync → live
     ├─ Vajab muudatusi → kommenteerid PR-s või
     │  jooksutad claude uuesti sama branchi peal
     └─ Ei sobi → close PR
```

### CLAUDE.md sisu (content agent juhised)

```markdown
# Pilvio Blog — Content Guidelines

## Bränd ja hääl
- Keel: eesti. Tehnilised terminid inglise keeles.
- Toon: praktiline, otsekohene, "no-bullshit".
- Alati isiklik kogemus ja konkreetsed numbrid, mitte abstraktne teooria.
- Lugeja on tehniline inimene (arendaja, DevOps, IT-juht).
- Ära kasuta turunduskeelt. Kirjuta nagu kolleegile.

## Artikli struktuur
- Frontmatter: vaata README.md formaati
- Pealkiri: selge, praktiline, eestikeelne
- Sissejuhatus: 2-3 lauset, miks see teema on oluline
- Sisu: samm-sammult, koodi näited, tabelid
- Kokkuvõte: konkreetsed järgmised sammud
- Artikli lõpus: CTA (uudiskiri, Pilvio teenused, järgmine artikkel)

## Pilvio seos
- Iga artikkel peaks loomulikult viitama Pilvio teenustele
- Mitte "reklaam", vaid "loogiline järgmine samm"
- Näide: "Kui kohalik AI jääb kitsaks → Pilvio AI API"
- Näide: "Terraform + Pilvio VPS → infrastruktuur koodina"

## Visuaal
- Illustratsioon: 1200x675px, PNG
- Stiil: tehniline/skemaatiline, mitte stock-foto
- Salvestatakse media/ kausta

## Olemasolevad artiklid
Loe posts/ kaustast olemasolevaid artikleid enne kirjutamist.
Hoia stiili järjepidevust, viita varasematele artiklitele kus sobiv.

## Kvaliteedikontroll
Enne PR-i loomist kontrolli:
- [ ] Frontmatter on korrektne ja täielik
- [ ] Slug on unikaalne
- [ ] imageUrl viitab õigele failile media/ kaustas
- [ ] Koodi näited on testitud / testivad
- [ ] Lingid töötavad
- [ ] Eesti keele õigekiri on korras
```

### write-article.md (Claude Code custom command)

```markdown
# /write-article

Kirjuta uus blogiartikkel Pilvio blogi jaoks.

## Sammud

1. Küsi kasutajalt:
   - Artikli teema
   - Kontekst ja fookus (mis nurga alt kirjutada)
   - Kas on seotud mõne olemasoleva artikliga (sari?)

2. Loe olemasolevaid artikleid (posts/) stiili tundmaõppimiseks

3. Kirjuta artikkel:
   - Järgi CLAUDE.md juhiseid
   - Genereeri frontmatter (title, author "Kaur Kiisler", publishedAt
     tänane kuupäev eesti formaadis, category, tags, excerpt, slug)
   - Kirjuta sisu eesti keeles

4. Genereeri illustratsioon:
   - Loo DALL-E prompt tehniline/skemaatilise pildi jaoks
   - Genereeri 1200x675px pilt
   - Salvesta media/ kausta (failinimel kasuta slug'i)

5. Salvesta artikkel posts/ kausta

6. Loo branch ja PR:
   - Branch: content/{slug}
   - Commit message: "Add article: {title}"
   - PR: title, description koos artikli kokkuvõttega

7. Teata kasutajale PR-i link
```

### Vajalikud API-d ja credentials

| Credential | Milleks | Kus hoida |
|-----------|---------|-----------|
| `ANTHROPIC_API_KEY` | Claude API artikli kirjutamiseks (kui ei kasuta Claude Code'i enda sessiooni) | Env var |
| `OPENAI_API_KEY` | DALL-E illustratsioonide jaoks | Env var |
| GitHub access | PR-ide loomiseks | Claude Code'i enda git auth |

### Alternatiiv: ilma image genereerimiseta

Kui DALL-E kvaliteet ei rahulda või tahad illustratsioone ise teha:
- Agent kirjutab ainult artikli + loob image prompti eraldi faili
- Sina genereerid pildi ise (ChatGPT, Midjourney, vms)
- Laadid üles → merge

See on täiesti OK stardivariant. Illustratsiooni genereerimine on kõige raskemini automatiseeritav osa.

---

## Tool 2: Marketing Agent

### Mis see on

Kolm komponenti: content generator (LLM), publisher (skript), reporter (skript).
Trigger: uus artikkel repo's VÕI manuaalne käivitus.

### Workflow

```
Trigger: uus artikkel merged main'i
  │
  ▼
GitHub Action käivitab marketing generaatori
  │
  ├─ Loeb uue artikli
  ├─ Claude API genereerib:
  │   - 4 FB postitust (erinev formaat: nipp, link, küsimus, kokkuvõte)
  │   - 1 newsletter draft
  │   - UTM lingid automaatselt
  │   - Soovituslikud avaldamiskuupäevad (E, K, R, P)
  │
  ├─ Salvestab marketing/queue/ kausta JSON failidena
  │   - Loob branchi: marketing/{slug}
  │   - Loob PR-i koos preview'ga
  │
  └─ Sina vaatad PR-i üle
     ├─ Kõik OK → merge
     │   └─ Post-merge hook: fb-publish.js ajastab postitused FB-sse
     ├─ Muudad mõne postituse teksti → merge
     └─ Eemaldad mõne postituse → merge
```

### Queue formaat

`marketing/queue/2026-03-10_ai-agent_tip.json`:
```json
{
  "id": "2026-03-10_ai-agent_tip",
  "type": "tip",
  "source_article": "posts/AI-agent-instr-1.md",
  "scheduled_date": "2026-03-10",
  "scheduled_time": "09:00",
  "status": "pending",
  "text": "AI agendi API kulu saab olla 2€/kuu. Saladus: 80% tööst tee klassikalise automatiseerimisega.\n\nLoe rohkem 👉",
  "link": "https://blog.pilvio.com/kuidas-ehitada-AI-agenti",
  "utm": {
    "source": "facebook",
    "medium": "social",
    "campaign": "ai_agent_w11",
    "content": "tip"
  },
  "full_url": "https://blog.pilvio.com/kuidas-ehitada-AI-agenti?utm_source=facebook&utm_medium=social&utm_campaign=ai_agent_w11&utm_content=tip"
}
```

### Newsletter draft formaat

`marketing/newsletters/2026-w11.md`:
```markdown
---
week: 11
date_range: "9.–15. märts 2026"
subject: "Kohalik AI kolme käsuga käima + AI agendi arhitektuur"
---

Tere!

Sel nädalal Pilvio blogis:

**Kuidas käivitada AI mudel oma arvutis**
Praktiline juhend Ollama + Qwen mudelitega. Kolm käsku
ja esimene kohalik AI töötab.
→ [Loe artiklit](https://blog.pilvio.com/kuidas-kaivitada-ai-mudel-oma-arvutis)

**Kuidas ehitada AI agenti, mis päriselt tootmises töötab**
9 õppetundi demo ja production-ready süsteemi vahel.
→ [Loe artiklit](https://blog.pilvio.com/kuidas-ehitada-AI-agenti)

---

Kas sul on küsimusi või kogemusi jagada? Vasta sellele meilile.

Kaur
Pilvio
```

Sina: kohandad drafti, kopeerid oma newsletter tool'i, saadad. Seda osa ei automatiseeri — sinu isiklik hääl on väärtus.

### marketing.md (Claude Code custom command)

```markdown
# /marketing

Genereeri marketingisisu viimaste artiklite põhjal.

## Sammud

1. Loe marketing/config.json seadeid
2. Leia artiklid, millele pole veel marketingisisu genereeritud
   (võrdle posts/ vs marketing/published/ ja marketing/queue/)
3. Iga uue artikli kohta genereeri:
   a) 4 FB postitust:
      - "tip" — lühike praktiline nipp artiklist (2-3 lauset)
      - "link" — artikli promo koos kontekstiga (3-4 lauset + link)
      - "question" — kogukonna küsimus artikli teema kohta
      - "summary" — artikli kokkuvõte (3-4 lauset)
   b) Newsletter draft (kui nädala jooksul on uusi artikleid)
4. Loo UTM lingid automaatselt (config.json põhjal)
5. Soovita avaldamiskuupäevad (E=tip, K=link, R=question, P=summary)
6. Salvesta marketing/queue/ kausta
7. Loo branch ja PR
```

### config.json

```json
{
  "blog_base_url": "https://blog.pilvio.com",
  "utm_defaults": {
    "source": "facebook",
    "medium": "social"
  },
  "posting_schedule": {
    "tip": "monday 09:00",
    "link": "wednesday 09:00",
    "question": "friday 12:00",
    "summary": "sunday 10:00"
  },
  "facebook": {
    "page_id": "SINU_PAGE_ID"
  },
  "newsletter": {
    "sender_name": "Kaur @ Pilvio",
    "sender_email": "kaur@pilvio.com"
  }
}
```

### Scripts

#### fb-publish.js

Mis ta teeb: loeb `marketing/queue/` failid, mille status on "approved" (merged PR-is), ajastab FB Graph API kaudu.

```
Sisend: marketing/queue/*.json (status: approved, scheduled_date in future)
Väljund: FB scheduled post, faili liigutamine → marketing/published/
API: Facebook Graph API (Page Access Token)
```

Skoop on teadlikult väike:
- POST /{page-id}/feed — tekst + link
- scheduled_publish_time parameter — ajastamine
- Vigade käsitlemine + retry
- ~100 rida koodi

#### fb-insights.js

Mis ta teeb: kogub viimase 7 päeva FB Page insights, salvestab raporti.

```
Sisend: FB Graph API /{page-id}/insights + /posts
Väljund: marketing/reports/2026-w11.md
Meetrikad: reach, engagement, link clicks, top post
```

Taas ~100 rida. Ei ehita dashboardi, lihtsalt struktureeritud markdown.

#### send-report.js

Mis ta teeb: saadab viimase raporti emailiga.

```
Sisend: marketing/reports/ viimane fail
Väljund: email sinu aadressile
Meetod: Nodemailer + SMTP (Gmail app password, Mailgun, või mis iganes)
```

~50 rida.

### GitHub Actions

#### marketing.yml — genereerimise trigger

```yaml
name: Generate Marketing Content

on:
  # Triggerib kui uus artikkel lisatakse
  push:
    branches: [main]
    paths: ['posts/*.md']
  # Manuaalne trigger
  workflow_dispatch:

jobs:
  generate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Check for new articles without marketing content
        id: check
        run: |
          # Võrdle posts/ vs marketing/published/ ja queue/
          # Kui uusi artikleid on, jätka

      - name: Generate marketing content
        if: steps.check.outputs.has_new == 'true'
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        run: |
          # Kutsu Claude API, genereeri FB postitused + newsletter
          # Salvesta marketing/queue/ ja marketing/newsletters/

      - name: Create PR
        env:
          GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        run: |
          git checkout -b marketing/auto-$(date +%Y%m%d)
          git add marketing/
          git commit -m "Generate marketing content for new articles"
          gh pr create --title "Marketing: uued postitused" \
            --body "Automaatselt genereeritud FB postitused ja newsletter draft"
```

#### publish.yml — avaldamise trigger

```yaml
name: Publish Approved Marketing Content

on:
  push:
    branches: [main]
    paths: ['marketing/queue/*.json']

jobs:
  publish:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Publish to Facebook
        env:
          FB_PAGE_ACCESS_TOKEN: ${{ secrets.FB_PAGE_ACCESS_TOKEN }}
          FB_PAGE_ID: ${{ secrets.FB_PAGE_ID }}
        run: node scripts/fb-publish.js

      - name: Move to published
        run: |
          # Liiguta avaldatud failid queue/ → published/
          git add marketing/
          git commit -m "Mark posts as published"
          git push
```

#### weekly-report.yml — nädalane raport

```yaml
name: Weekly Marketing Report

on:
  schedule:
    - cron: '0 8 * * 1'  # Esmaspäev 08:00 UTC
  workflow_dispatch:

jobs:
  report:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Collect FB insights
        env:
          FB_PAGE_ACCESS_TOKEN: ${{ secrets.FB_PAGE_ACCESS_TOKEN }}
          FB_PAGE_ID: ${{ secrets.FB_PAGE_ID }}
        run: node scripts/fb-insights.js

      - name: Send email report
        env:
          SMTP_HOST: ${{ secrets.SMTP_HOST }}
          SMTP_USER: ${{ secrets.SMTP_USER }}
          SMTP_PASS: ${{ secrets.SMTP_PASS }}
          REPORT_EMAIL: kaur.kiisler@gmail.com
        run: node scripts/send-report.js

      - name: Save report to repo
        run: |
          git add marketing/reports/
          git commit -m "Weekly report $(date +%Y-W%V)" || true
          git push || true
```

---

## Vajalikud API credentials

| Secret | Milleks | Kust saada |
|--------|---------|-----------|
| `ANTHROPIC_API_KEY` | Claude API marketingisisu genereerimiseks | console.anthropic.com |
| `OPENAI_API_KEY` | DALL-E illustratsioonid (content agent) | platform.openai.com |
| `FB_PAGE_ACCESS_TOKEN` | FB postituste avaldamine ja insights | Meta Developer Console → Page token (long-lived) |
| `FB_PAGE_ID` | FB lehe identifitseerimine | FB lehe About section või Graph API |
| `SMTP_HOST` / `SMTP_USER` / `SMTP_PASS` | Nädala raporti saatmine emailiga | Sinu email provider |
| `GH_PAT` | PR-ide loomine GitHub Actions'is | GitHub Settings → Tokens |

### FB Page Access Token setup

See on kõige tüütum osa. Sammud:
1. Meta Developer Console → Create App (Business type)
2. Lisa "Facebook Login" ja "Pages API" tooted
3. Genereeri Page Access Token (pages_manage_posts, pages_read_engagement)
4. Vaheta short-lived token long-lived'iks (kehtib ~60 päeva)
5. Seadista token refresh skript või manuaalne uuendamine

Long-lived token'i uuendamine on automatiseeritav, aga stardiks piisab manuaalsest uuendamisest kord 2 kuu tagant.

---

## Ehitamise järjekord

### Faas 1: Content Agent (1-2 päeva)

Minimaalne versioon, mis kohe väärtust annab:

1. Loo `.claude/CLAUDE.md` koos brändi juhiste ja blogireeglitega
2. Loo `.claude/commands/write-article.md` custom command
3. Testi: `claude "/write-article"` → saad artikli + PR
4. Illustratsioon: alusta ilma automaatse genereerimiseta (lisa prompti fail, genereeri ise)

Tulemus: artikli kirjutamine on 1 käsk + review, mitte 2h käsitööd.

### Faas 2: Marketing Generator (1-2 päeva)

1. Loo `marketing/` kaustastruktuur + `config.json`
2. Loo `.claude/commands/marketing.md` custom command
3. Testi: `claude "/marketing"` → saad FB postitused + newsletter draft + PR
4. Seadista `marketing.yml` GitHub Action (automaatne trigger)

Tulemus: iga uue artikli kohta saad automaatselt 4 FB postitust + newsletter drafti.

### Faas 3: FB Publisher (pool päeva)

1. Seadista FB Page Access Token
2. Kirjuta `scripts/fb-publish.js` (~100 rida)
3. Seadista `publish.yml` GitHub Action
4. Testi: merge marketing PR → postitus ilmub FB-sse

Tulemus: approved postitused avaldatakse automaatselt.

### Faas 4: Weekly Reporter (pool päeva)

1. Kirjuta `scripts/fb-insights.js` + `scripts/send-report.js`
2. Seadista `weekly-report.yml` GitHub Action
3. Testi: käivita manuaalselt → email raportiga

Tulemus: esmaspäeva hommikul saad emaili eelmise nädala statistikaga.

### Faas 5 (valikuline): Image Generation

1. Lisa `OPENAI_API_KEY` env'i
2. Uuenda `write-article.md` commandit: genereeri DALL-E pilt
3. Testi kvaliteeti — kui DALL-E ei rahulda, jää manuaalse pildi juurde

---

## Mis see sulle annab

### Enne (praegune flow)

| Tegevus | Sinu aeg |
|---------|----------|
| Artikli kirjutamine (Claude + ChatGPT + käsitöö) | 2-4h |
| Illustratsiooni genereerimine | 30min |
| Git'i üleslaadimine | 15min |
| FB postituste kirjutamine | 30min |
| FB-sse postitamine | 15min |
| Newsletter | 30min |
| **Kokku per artikkel** | **~4-6h** |

### Pärast (automatiseeritud flow)

| Tegevus | Sinu aeg |
|---------|----------|
| Teema ja konteksti andmine | 10min |
| Artikli PR ülevaatamine + approve | 15-30min |
| Marketing PR ülevaatamine + approve | 5-10min |
| Newsletter drafti kohandamine + saatmine | 10-15min |
| **Kokku per artikkel** | **~40min-1h** |

### Sinu nädala rutiin

```
Esmaspäev:  Loe nädala metrics raportit (email, 5 min)
Teisipäev:  Anna uue artikli teema + kontekst (10 min)
Kolmapäev:  Vaata artikli PR üle, merge (15-30 min)
Neljapäev:  Vaata marketing PR üle, merge (5-10 min)
            Kohanda newsletter, saada (10-15 min)
Reede:      — (postitused avalduvad automaatselt)
```

---

## Mida see plaan EI sisalda (teadlikult)

- **Facebook Ads automatiseerimine** — €100-300/kuu eelarvega on Ads Manager UI kiirem kui API. Boost 4 postitust kuus = 10 min käsitööd.
- **Kommentaaridele vastamine** — sinu isiklik hääl. Mitte kunagi automatiseerida.
- **Analytics dashboard** — FB Insights + GA4 on piisav. Custom dashboard on overhead.
- **Multi-platform posting** — alusta FB-ga, lisa LinkedIn hiljem kui vajadus tekib.
- **A/B testing** — liiga vähe traffic'ut selleks, et statistiline olulisus tekiks. Optimeeri vaistuga.
- **Andmebaas** — JSON failid repo's on piisav sinu mahuga (3-4 postitust nädalas).
