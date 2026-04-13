---
title: "Kuidas ma ehitasin SEO agendi, mis saadab mulle iga esmaspäeva hommikul raporti"
author: "Kaur Kiisler"
publishedAt: "13. aprill 2026"
category: "AI"
tags: ["AI", "agent", "SEO", "GA4", "Google Search Console", "Claude API", "automatiseerimine"]
featured: false
excerpt: "Ehitasin Python agendi, mis kogub iga esmaspäev GA4 ja Search Console andmed, analüüsib trende ja saadab emailiga konkreetsed soovitused — 10x odavamalt kui Semrush."
imageUrl: "/media/seo-agent.png"
slug: "kuidas-ehitasin-seo-agendi"
---

# Kuidas ma ehitasin SEO agendi, mis saadab mulle iga esmaspäeva hommikul raporti

*Kaur Kiisler, Pilvio asutaja*

---

Mul oli probleem. Ma teadsin, et Pilvio ei ole Eestis piisavalt nähtav. Aga iga kord kui tahtsin olukorda hinnata, pidin avama Google Analytics 4, siis Search Console'i, siis võrdlema andmeid nädalate lõikes, siis vaatama eraldi blogi numbreid. Tund aega hiljem olin andmetes kadunud ja otsuseid polnud ikkagi teinud.

Nii et ehitasin agendi, mis teeb selle töö mu eest ära.

---

## Mida agent teeb

Iga esmaspäeva hommikul kell 9 saan emaili. 30 sekundiga on pilt selge:

- Mitu inimest käis pilvio.com-is ja blogis
- Mis märksõnad tõusevad, mis langevad
- Mitu lead'i tuli ja kust
- Mis artiklid inimesi tegelikult huvitasid (reading progress ≥75%)
- Kas LinkedIn'ist tulev liiklus konverteerus
- Mis eelmistest soovitustest on tehtud ja mis mitte
- 2–3 konkreetset soovitust, mida sel nädalal teha

Varem kulus selleks tund GA4-s ja Search Console'is. Nüüd 30 sekundit emaili lugedes hommikukohvi kõrvale.

---

## Miks ma ei kasutanud Semrushi ega Ahrefsi

Küsimus on aus. Miks ehitada ise, kui tööriistad on olemas?

**Hind.** Väikseim Semrushi plaan on ~120€ kuus. Mu agent maksab:

| Komponent | Kulu |
|---|---|
| Pilvio VM (väikseim Linux VPS) | ~6€/kuu |
| Claude API (soovituste genereerimine) | ~2–5€/kuu |
| Google Search Console + GA4 API | Tasuta |
| Resend (email saatmine) | Tasuta |
| **Kokku** | **~8–11€/kuu** |

10x odavam. Ja annab mulle täpselt seda, mida vajan — mitte 50 dashboard'i, millest 48 ma ei kasuta.

**Kontekst.** Semrush ei tea, et Pilvio on Eesti ainus 100% kodumaises omanduses täisautomaatne pilveteenus. Ta ei tea, kes on mu konkurendid. Ta ei tea, et mu blogi artiklid on eestikeelsed ja LinkedIn on mu peamine liiklusallikas. Claude API teab — sest ma ütlen talle süsteemi promptis.

**Andmesuveräänsus.** Mu andmed ei lähe kolmanda osapoole SaaS-i. Need liiguvad Google API-st mu Pilvio VM-i, seal analüüsitakse ja raport tuleb emailiga. Kogu ahel on mu kontrolli all.

---

## Arhitektuur

Agent on üllataval kombel lihtne:

```
GSC API (2 saiti)  ───┐
                      ├──  SQLite  ──  Trendianalüüs  ──  Claude API  ──  Email
GA4 API (2 saiti)  ───┘
```

Python skript, mida cron käivitab. Andmed tulevad kahest allikast:

**Google Search Console** — märksõnad, positsioonid, impressionid, klõpsud. Kaks property't: pilvio.com ja blog.pilvio.com. See ütleb mulle, mida inimesed otsivad ja kas Pilvio tuleb vastu.

**GA4** — sessioonid, konversioonid, funnel'id, engagement. Kaks eraldi GA4 property't kahe eraldi GTM containeriga. pilvio.com on lead gen — seal jälgin registreerimisi, demo päringuid ja kalkulaatori kasutust. blog.pilvio.com on sisu — seal jälgin reading progress'i (kas inimesed loevad artikli lõpuni), newsletter'i signup'e ja LinkedIn'ist tulevat liiklust.

Kõik salvestatakse SQLite andmebaasi. Miks SQLite? Sest see on üks fail, backup on triviaalne (kopeerin Pilvio StorageVault S3-sse — dogfooding), ja sellises mahus andmete jaoks on PostgreSQL overkill.

Analüüsikiht arvutab nädal-üle-nädala muutused: mis märksõnad tõusid, mis langesid, kus on uued võimalused, kuidas funnel'id töötavad. See osa on puhas Python — LLM-i pole vaja, et arvutada 15% muutust sessioonides.

Alles siis saadab kogu paketi Claude API-le, kes teeb andmete põhjal 2–3 prioritiseeritud soovitust. LLM-i kasutatakse ainult seal, kus päriselt vaja — 80/20 reegel jälle.

Lõpptulemus on HTML email Resend API kaudu.

---

## Üks üllatus: blogi tracking on tegelikult kõige väärtuslikum

Ma ei oodanud seda. Aga blogi andmed on osutunud väärtuslikumaks kui pilvio.com-i omad.

Põhjus: `blog_engagement` event koos `reading_progress` milestones'iga (25%, 50%, 75%, 100%) ütleb täpselt, millised artiklid inimesi päriselt huvitavad. Mitte ainult "keegi käis lehel" vaid "keegi luges 75% läbi." See on päris signaal.

Koos `newsletter_signup` eventiga, mis salvestab ka `article_title` parameetri, näen täpselt: milline sisu konverteerib subscribereid.

See on mu LinkedIn strateegia otsene feedback loop. Ma postitan LinkedIn'is, inimesed tulevad blogisse, agent ütleb mulle, kas nad lugesid artikli lõpuni ja kas keegi subscribe'is. Ilma selle agendita oleks ma pimedas.

---

## Kuidas ma selle ehitasin

Claude Code. 8 etappi, igaüks iseseisvalt testitav:

1. **Projekti skeleton** — konfiguratsioon, SQLite skeem, .env haldus
2. **GSC collector** — mõlema saidi märksõnad, positsioonid, klõpsud
3. **GA4 collector** — sessioonid, konversioonid, funnel'id, crosssite liiklus
4. **Trendide analüüs** — nädal-üle-nädala muutused puhas Python'iga
5. **LLM analüüs** — Claude API saab andmekonteksti, sülitab soovitused
6. **Email template** — Jinja2 HTML, Resend saadab
7. **Orchestrator** — kogu pipeline kokku, S3 backup, partial failure handling
8. **Cron + deployment** — iga esmaspäev kell 7

Iga etapi lõpus kontrollpunkt: kas töötab eraldi? Alles siis järgmine. Nii ei läinud kordagi katki — ja kui läks, teadsin täpselt kus.

Kogu implementatsioon võttis umbes kaks päeva. Ilma Claude Code'ita oleks sama töö võtnud nädala või rohkem.

---

## Mis on veel lahendamata

Praegu on pilvio.com ja blog.pilvio.com eraldi GA4 property'd. See tähendab, et ma ei näe cross-site journey't — ma tean, et keegi klõppis pilvio.com-il blogi linki ja keegi tuli blogist tagasi, aga ma ei tea, kas see on sama inimene.

Lahendus on cross-domain tracking (GA4 toetab seda), aga see on eraldi seadistustöö. Esialgu raporteerib agent mõlemat saiti eraldi ja näitab agregeeritud crosssite numbreid: "pilvio.com → blog klikke: 23, blog → pilvio.com referral'e: 11." Pole ideaalne, aga piisav otsuste tegemiseks.

---

## Mida see mulle õpetas

**Agent ei pea olema keeruline.** See on Python skript, SQLite andmebaas, paar API'd ja cron job. Kokku ehk 800 rida koodi. Pole Kubernetes'i, pole mikroteenuseid, pole message queue'd.

**80/20 kehtib.** Trendide arvutamine on puhas Python. LLM-i kutsutakse ainult soovituste jaoks. Selle tõttu on Claude API kulu 2–5€ kuus, mitte 50€.

**Feedback loop on kõige väärtuslikum.** Ma ei vajanud paremat dashboard'i. Ma vajasin kedagi, kes ütleks mulle "sel nädalal tee seda." Agent teeb seda.

---

## Kokkuvõte

Kui sa kaevad iga nädal GA4-s ja Search Console'is ja tunned, et aega läheb aga otsuseid ei tule — ehita endale agent. See ei pea olema keeruline. Cron job, paar API'd, SQLite ja LLM soovituste jaoks. Kaks päeva tööd ja iga esmaspäev algab 30-sekundilise ülevaatega.

Mu agent jookseb Pilvio VM-is, backup läheb Pilvio StorageVault'i. Kogu kulu on ~10€ kuus. Ja ma tean iga esmaspäeva hommikul täpselt, mida teha.

---

*Pilvio pakub Eesti ettevõtetele pilvetaristut AI-lahenduste ja veebiteenuste jooksutamiseks. Kui tahad oma agente Eesti territooriumil hoida, [võta ühendust](https://pilvio.com).*
