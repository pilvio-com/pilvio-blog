---
title: "Sinu ettevõtte dokumendid on AI-le nähtamatud, kui ligipääsukiht ei ole sinu kontrolli all"
author: "Kaur Kiisler"
publishedAt: "21. mai 2026"
category: "AI"
tags: ["AI", "Nextcloud", "SharePoint", "andmesuveräänsus", "RAG", "MCP", "S3", "dokumendihaldus"]
featured: false
excerpt: "SharePoint ja Nextcloud suudavad mõlemad dokumente hoida. Erinevus tuleb sisse selles, kui palju kontrolli jääb sinu kätte siis, kui tahad nende peale ehitada tootjast sõltumatut AI-kihti."
imageUrl: "/media/sharepoint-vs-nextcloud.png"
slug: "sharepoint-vs-nextcloud-ai"
---

# Sinu ettevõtte dokumendid on AI-le nähtamatud, kui ligipääsukiht ei ole sinu kontrolli all

Suur keelemudel (LLM) ei tea iseenesest sinu SharePointi, Nextcloudi ega failiserveri sisu. Mudel saab vastata ettevõtte tegeliku teadmise põhjal alles siis, kui tal on kontrollitud ligipääs dokumentidele, metaandmetele ja õigustele.

Praktikas tähendab see RAG-arhitektuuri, otsinguindeksit, ühenduskomponente või MCP-serverit. Tehniline küsimus ei ole enam ainult "kus failid asuvad?". Olulisem on: **kes kontrollib andmevoogu failiplatvormi, otsinguindeksi, embedding'ute, logide ja AI-rakenduse vahel?**

See on andmesuveräänsuse praktiline tuum. Kui sinu ettevõtte teadmised on AI jaoks kättesaadavad peamiselt ühe tootja tingimustel, siis sõltub ka sinu tulevane AI-arhitektuur selle tootja kontrolltasandist. Sa kasutad platvormi, mille reeglid, hinnastus, API-piirangud ja integratsioonimudel võivad muutuda sinust sõltumata.

AI-ajastul peab dokumendiplatvorm vastama viiele küsimusele:

1. **Autentimine**: milline kasutaja, teenusekonto või rakendus tohib andmeid lugeda?
2. **Autoriseerimine**: kas AI näeb ainult neid dokumente, mida päringu esitanud kasutaja ise näha tohiks?
3. **Muudatuste jälgimine**: kuidas saab indeks teada, et fail muutus, kustutati või selle õiguseid muudeti?
4. **Sisu ekstraktimine**: kuidas failid API kaudu loetleda, alla laadida, tekstiks töödelda ja tükeldada?
5. **Andmevoo kontroll**: kuhu liiguvad failisisu, embedding'ud, otsinguindeksid, päringud ja auditilogid?

SharePoint ja Nextcloud suudavad mõlemad dokumente hoida. Erinevus tuleb sisse selles, kui palju kontrolli jääb sinu kätte siis, kui tahad nende dokumentide peale ehitada tootjast sõltumatut AI-kihti.

## SharePoint: tugev platvorm Microsofti kontrolltasandil

SharePoint ei ole AI-le tehniliselt suletud. Microsoft Graph API võimaldab SharePoint Online'i ja OneDrive for Businessi dokumente programmiliselt loetleda, lugeda ja muudatusi jälgida. Failid on Graphis `driveItem` ressursid ning kausta sisu saab pärida näiteks nii:

```http
GET /sites/{site-id}/drive/items/{item-id}/children
GET /drives/{drive-id}/items/{item-id}/children
GET /drives/{drive-id}/root:/{path-relative-to-root}:/children
```

See on täiesti toimiv tee, kui ehitad SharePointi peale oma otsingu või RAG-kihi. Aga see ei ole lihtne failikausta ühendamine. See on Microsoft 365 identiteedi-, õiguste- ja API-mudeli projekt.

Tüüpiline SharePointi AI-indekseerija vajab:

- Microsoft Entra ID rakenduse registreerimist;
- OAuth 2.0 voogu, token'ite haldust ja saladuste roteerimist;
- delegated või application permission'i mudeli valikut;
- õiguseid nagu `Files.Read.All`, `Sites.Read.All` või piiratumal juhul `Sites.Selected`;
- administraatori nõusolekut, kui ligipääs on tenant'i või saidikogumi tasemel;
- kasutaja-, grupi- ja jagamisõiguste kaasavõtmist otsinguindeksi metaandmetesse;
- delta query loogikat, et mitte kogu dokumendikogu iga kord uuesti läbi skaneerida;
- paging'u, throttling'u, ajutiste vigade ja `Retry-After` päiste käsitlemist.

SharePointi suurim AI-risk ei ole see, et API puuduks. API on olemas. Risk on kontrolltasandis ja õiguste mudelis.

Kui rakendus saab laia `Sites.Read.All` või `Files.Read.All` õiguse, tekib väga võimekas ligipääsukiht, mis võib lugeda suurt osa tenant'i sisust. Kui kasutada `Sites.Selected` mudelit, saab õiguseid piirata, kuid halduskoormus kasvab: igale saidile tuleb ligipääs eraldi anda ja hallata. Mõlemal juhul peab RAG-indeks säilitama ACL-id, sest muidu võib AI vastata dokumendi põhjal kasutajale, kellel selle dokumendi lugemisõigust ei ole.

Microsoft 365 Copiloti sees on see kogemus kasutajale mugavam. Copilot töötab Microsoft Graphi ja Microsofti turvamudeli sees. Kui ettevõtte strateegia on Microsoft 365 keskne, on SharePoint loogiline dokumentide kodu.

Kui eesmärk on aga kasutada erinevaid mudeleid, lokaalseid LLM-e, oma RAG-teenust või agentide orkestreerimist väljaspool Microsofti standardset Copiloti kogemust, tuleb ligipääsu-, õiguste- ja auditikiht ise ehitada ning Microsofti API-de käitumisega kohandada.

Microsoft pakub ka Copiloti ühenduskomponente. Sünkrooniv connector indekseerib välise allika Microsoft Graphi. Födereeritud connector pärib andmeid reaalajas MCP kaudu ja võib hoida andmed allikas, ilma neid Microsoft 365-sse indekseerimata. See on oluline samm õiges suunas. Samas jäävad kasutuskogemus, administreerimine, autentimine ja ühendusmudeli reeglid endiselt Microsoft 365 kontrolltasandi raamidesse.

See ongi põhiline vahe: SharePoint annab väga hea mugavuse Microsofti ökosüsteemis, kuid tootjast sõltumatu AI-kihi jaoks tuleb leppida Microsofti API, permission'i mudeli ja platvormireeglitega.

## Nextcloud + S3: kontroll andmevoo, salvestuse ja AI-kihi üle

Nextcloudi ja S3-ühilduva objektisalvestuse väärtus AI-ajastul ei ole ainult avatud lähtekood ega litsentsikulu. Olulisem on see, et ligipääsukiht on läbipaistev ja arhitektuuri saab kujundada enda andmepoliitika järgi.

Nextcloud pakub failidele ligipääsu WebDAVi kaudu ning jagamiste ja koostööfunktsioonide jaoks OCS API-sid. AI-indekseerija saab töötada Nextcloudi loogilise failikihi vastu:

- loetleda faile ja kaustu WebDAVi kaudu;
- kasutada `ETag`, `mtime` ja faili-ID-sid muutuste tuvastamiseks;
- lugeda failisisu teenusekonto või rakenduse parooliga;
- küsida jagamiste infot OCS API kaudu;
- salvestada iga tekstilõigu juurde failitee, versioon, omanik, grupid, jagamised ja allika URL;
- filtreerida päringu ajal tulemused samade õiguste järgi, mida Nextcloud kasutajale rakendaks.

See ei tähenda, et Nextcloudil puuduvad tehnilised piirid. Piirid tulevad serverist, andmebaasist, objektisalvestusest, reverse proxy'st ja indekseerija enda arhitektuurist. Erinevus on selles, et need piirid on sinu arhitektuuri osa, mitte ühe välise platvormi teenusepõhised Graph API kvoodid.

## S3 roll: ära mine Nextcloudi metaandmekihist mööda

S3 puhul tuleb teha oluline eristus.

**Primary storage** tähendab, et Nextcloud kasutab S3 bucket'it oma primaarse salvestuskihina. Sel juhul vajab Nextcloud bucket'ile eksklusiivset ligipääsu. Failinimed, kaustastruktuur ja metaandmed elavad Nextcloudi andmebaasis; objektisalvestuses on failisisu sisemiste identifikaatorite järgi. Sellises mudelis ei tohi AI-indekseerija lihtsalt S3 bucket'it otse läbi käia ja eeldada, et näeb korrektset failipuud või õiguste infot. Nextcloud on tõe allikas.

**External storage** tähendab, et S3 bucket on ühendatud Nextcloudi välise kaustana. See võib sobida hübriidmudeliks, kus osa andmevooge liigub Nextcloudi kaudu ja osa AI-töötlusest saab vajadusel töötada otse S3 API vastu. Ka siis tuleb õiguste ja metaandmete mudel selgelt läbi disainida.

Teisisõnu: Nextcloud + S3 ei ole otsetee turvalisusest mööda. See on võimalus ehitada turvaline andmevoog nii, et failid, indeksid, logid ja mudelid saavad jääda sinu kontrollitavasse infrastruktuuri.

## Suveräänne AI-töövool

Tootmiskõlbulik AI-teadmistekiht ei tohiks anda mudelile piiramatut ligipääsu failisüsteemile. Õigem muster on kitsaste tööriistadega ligipääs:

```text
search_documents(query, user_id, filters)
fetch_document(file_id, user_id)
summarize_folder(folder_id, user_id)
list_recent_changes(user_id, since)
```

Sellise lahenduse all võib töötada järgmine andmetoru:

```text
[Nextcloud + S3]
      |
      | WebDAV / OCS API / webhookid
      v
[Indekseerija]
      |
      | PDF, DOCX, XLSX, Markdown, HTML, OCR
      v
[Tekst ja tükid koos ACL-metaandmetega]
      |
      | embedding'u teenus: lokaalne mudel, Pilvio infrastruktuur või valitud API
      v
[Vektorandmebaas + täistekstiotsing]
      |
      | õiguste kontroll päringu ajal
      v
[RAG- või MCP-lüüs]
```

MCP sobib sellesse mustrisse hästi, sest standardiseerib, kuidas AI-rakendus väliseid tööriistu ja andmeallikaid kasutab. Samas ei tee MCP lahendust automaatselt turvaliseks. Turvalisus tuleb ehitada autentimise, autoriseerimise, scope'ide, HTTPS-i, tokenite valideerimise, logimise ja kasutajaõiguste kontrolli kihis.

Nextcloud liigub samas suunas ka oma AI-rakendustega. Context Chat võimaldab dokumentidega vestlemist. Context Agent lisab tööriistapõhise agendi ning Nextcloudi dokumentatsiooni järgi saab see eksponeerida MCP-serveri otspunkti, kuhu teised LLM-rakendused saavad pöörduda rakenduse parooliga. See on kasulik ehitusplokk, kuid tootmiskeskkonnas tuleb selle ümber ikkagi panna ettevõtte autentimise, auditi ja õiguste poliitika.

## Tehniline võrdlus: sõltuvus vs kontroll

| Tehniline parameeter | SharePoint Online | Nextcloud + S3 |
|---|---|---|
| Failide API | Microsofti-spetsiifiline Graph API ja SharePoint REST | WebDAV, OCS API, webhookid, serveripoolsed integratsioonid |
| Identiteet | Microsoft Entra ID, OAuth, Graphi õigused, administraatori nõusolek | Nextcloudi kasutajad/grupid, LDAP/SAML/OIDC, rakenduse paroolid, teenusekontod |
| Ligipääsu piiramine | Laiad õigused (`Files.Read.All`, `Sites.Read.All`) või haldusmahukam `Sites.Selected` | Nextcloudi ACL-id, grupid, jagamised, teenusekontod ja oma gateway loogika |
| Muudatuste jälgimine | Graph delta query, paging, throttling, retry-loogika | WebDAV metaandmed, Nextcloud filecache, webhookid, taustatööd |
| Andmete asukoht | Sõltub Microsoft 365 tenant'i, regiooni ja teenuse seadistustest | Sõltub sinu valitud Nextcloudi, S3, indeksi, logide ja mudelite paigutusest |
| AI ökosüsteem | Kõige sujuvam Microsoft 365 Copiloti sees; väline AI vajab Graph-integratsiooni | Tootjast sõltumatu: lokaalsed mudelid, RAG, MCP, Qdrant, pgvector, OpenSearch |
| Peamine risk | Sõltuvus Microsofti õigustemudelist, API-käitumisest, hinnastusest ja tootearendusest | Vastutus korrektse arhitektuuri, ACL-sünkrooni, auditi ja käitamise eest |

## Kasutaja vaatest: mida päriselt ehitada

Kui eesmärk on teha ettevõtte dokumendid AI-agentidele kasutatavaks, ei tasu mõelda ainult "chatbot failide peale". Tuleb ehitada platvormiülene teadmistekiht, mille iga osa on vahetatav ja auditeeritav.

Minimaalne tootmiskõlbulik arhitektuur:

1. **Suveräänne dokumendiallikas**: Nextcloud või SharePoint jääb failide ja kasutusõiguste esmaseks halduriks.
2. **Ühenduskomponent**: eraldi teenus loetleb failid, jälgib muutusi ja loeb sisu ametlike API-de kaudu.
3. **Parserid**: PDF, DOCX, XLSX, PPTX, Markdown, HTML ja skannitud pildid vajavad eri töötlust.
4. **Hübriidindeks**: täistekstiotsing ja semantiline vektorotsing töötavad koos. Ainult vektoritest täpse ärikonteksti jaoks ei piisa.
5. **ACL-metaandmed**: iga tekstilõigu küljes on kasutaja- või grupipõhine ligipääsuinfo.
6. **RAG/MCP-lüüs**: mudel ei pääse indeksile otse ligi, vaid kasutab kontrollitud tööriistu.
7. **Auditikiht**: logitakse, kes küsis, milliseid allikaid kasutati ja milline kontekst mudelile anti.

Kriitiline reegel: **ära indekseeri sisu ilma õiguste metaandmeteta**. Kui indeks ei tea, kellele fail on jagatud, ei tohiks seda tootmiskeskkonnas kasutada.

## Kokkuvõte: kelle käes on sinu ettevõtte teadmiste võtmed?

SharePoint on tugev ja mugav tööriist, kui ettevõtte IT-strateegia on Microsoft 365 keskne ning AI-kasutus toimub peamiselt Copiloti sees. Selle tugevus on valmis identiteedi-, koostöö- ja õigustemudel.

Nextcloud koos S3-ühilduva objektisalvestusega on parem valik siis, kui ettevõtte eesmärk on kontrollida oma AI-andmevoogu: kus asuvad failid, embedding'ud, otsinguindeksid, logid ja mudelite käitus. See ei eemalda inseneritööd, kuid annab vabaduse vahetada mudeleid, indekseid ja agentide tööriistu ilma ühe tootja kontrolltasandisse kinni jäämata.

Andmesuveräänsus ei ole loosung. See on arhitektuuriline otsus. AI-ajastul algab see küsimusest: kas sinu ettevõtte teadmiste ligipääsukiht kuulub sisuliselt sulle või rendid seda kellegi teise reeglite järgi?

## Kontrollitud allikad

- Microsoft Graph: [driveItem children API](https://learn.microsoft.com/en-us/graph/api/driveitem-list-children?view=graph-rest-1.0)
- Microsoft Graph: [throttling guidance](https://learn.microsoft.com/en-us/graph/throttling)
- Microsoft Graph: [driveItem delta query](https://learn.microsoft.com/en-us/graph/api/driveitem-delta?view=graph-rest-1.0)
- Microsoft 365 Copilot: [connectors overview](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/overview)
- Nextcloud: [WebDAV basic APIs](https://docs.nextcloud.com/server/20/developer_manual/client_apis/WebDAV/basic.html)
- Nextcloud: [OCS Share API](https://docs.nextcloud.com/server/stable/developer_manual/client_apis/OCS/ocs-share-api.html)
- Nextcloud: [object storage as primary storage](https://docs.nextcloud.com/server/latest/admin_manual/configuration_files/primary_storage.html)
- Nextcloud: [Webhook Listeners](https://docs.nextcloud.com/server/stable/admin_manual/webhook_listeners/index.html)
- Nextcloud: [Context Agent and MCP server](https://docs.nextcloud.com/server/latest/admin_manual/ai/app_context_agent.html)
- Model Context Protocol: [introduction](https://modelcontextprotocol.io/docs/getting-started/intro)
- Model Context Protocol: [authorization](https://modelcontextprotocol.io/specification/2025-06-18/basic/authorization)
