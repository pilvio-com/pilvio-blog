---
title: "Juhend: failide HTML vormilt Pilvio S3 *bucket'isse* JavaScriptiga üleslaadimine"           # Required: Post title
author: "Pilvio Tech Team"                   # Required: Author name
publishedAt: "15. november 2024"        # Required: Estonian format date
category: "Õpetused"                  # Required: Post category
tags: ["S3", "CORS"]          # Required: Array of tags
featured: false                         # Required: Boolean (only one post should be true)
excerpt: "Selles juhendis õpime, kuidas luua lihtne HTML-leht koos vormiga, mis võimaldab kasutajal valida faili oma arvutist ja laadida see otse Pilvio S3 *bucket'isse*."         # Optional: Custom excerpt (auto-generated if not provided)
imageUrl: "/media/s3_cors.png" # Optional: Featured image URL
---

# Juhend: failide HTML vormilt Pilvio S3 *bucket'isse* JavaScriptiga üleslaadimine

## Sissejuhatus

Objektisalvestus (nagu Pilvio S3) on suurepärane lahendus staatiliste failide, varukoopiate ja suurte andmemahtude hoidmiseks. Tihti tekib vajadus laadida faile otse veebilehelt oma S3 *bucket'isse*. Selles juhendis õpime, kuidas luua lihtne HTML-leht koos vormiga, mis võimaldab kasutajal valida faili oma arvutist ja laadida see otse Pilvio S3 *bucket'isse*, kasutades selleks JavaScripti ja AWS SDK-d (Software Development Kit).

Me kasutame AWS SDK JavaScripti teeki, kuna Pilvio S3 on S3-ühilduv teenus ja see teek töötab sellega suurepäraselt. Lisaks käsitleme olulist teemat – CORS (Cross-Origin Resource Sharing) poliitikaid, mis on vajalikud, et sinu veebileht saaks turvaliselt S3 *bucketiga* suhelda.

Selle juhendi lõpuks on sul toimiv näidisleht, mis demonstreerib failide üleslaadimist ja kuvab *bucketis* olevaid objekte.

> ⚠️ **Oluline turvahoiatus**
>
> Selles artiklis näidatud `index.html` näide sobib ainult lokaalseks katsetamiseks ja arenduseks.
> **Ära kunagi pane päris S3 `Access Key ID` ega `Secret Access Key` väärtusi brauseris käivitatavasse JavaScripti ega avalikku `index.html` faili.**
>
> Kui veebileht on kasutajatele kättesaadav, saavad need võtmed sattuda igaühe brauserisse ja neid saab kuritarvitada.
>
> **Tootmiskeskkonnas kasuta selle asemel kas:**
> - backendi kaudu loodud **pre-signed URL-e**, või
> - ajutisi ja rangelt piiratud õigustega mandaate.

## Eeldused

Enne alustamist veendu, et sul on olemas järgmised asjad:

- **Pilvio konto ja S3 \*bucket\*:** Sul peab olema aktiivne Pilvio konto ja loodud S3 *bucket*, kuhu faile laadida. Kui sul veel *bucketit* pole, saad selle luua Pilvio iseteenindusportaali kaudu.
- **Pilvio S3 ligipääsuvõtmed:** Sul on vaja oma Pilvio S3 *Access Key ID* ja *Secret Access Key*. Neid võib kasutada selles artiklis toodud näite puhul ainult lokaalses testkeskkonnas. **Ära kasuta siin pikaealisi tootmisvõtmeid ega avalda neid brauserisse jõudvas koodis.** Tootmislahendustes kasuta pigem backendi kaudu loodud pre-signed URL-e või muid ajutisi mandaate.
- **Tekstiredaktor:** Koodi kirjutamiseks (nt VS Code, Sublime Text, Notepad++).
- **Veebibrauser:** Koodi testimiseks (nt Chrome, Firefox).
- **(Valikuline, aga soovitatav CORS seadistamiseks) `s3cmd` tööriist:** See on käsurea tööriist S3-ühilduvate salvestusruumidega suhtlemiseks. Juhised selle paigaldamiseks ja seadistamiseks leiad [s3cmd ametlikult veebilehelt](https://s3tools.org/s3cmd). Alternatiivina võid CORS-reegleid seadistada ka teiste S3-ühilduvate tööriistadega või Pilvio portaali kaudu, kui see võimalus on olemas.

## Samm 1: HTML lehe ja JavaScripti koodi loomine

Alustame lihtsa HTML-faili (`index.html`) loomisega, mis sisaldab failivaliku välja, üleslaadimise nuppu ja kohta tulemuste kuvamiseks. Kogu loogika kirjutame samasse faili `<script>` tagide vahele.

Enne jätkamist veel kord: järgmine näide näitab brauseripõhist otseüleslaadimist kõige lihtsamas vormis. See on kasulik CORS-i ja S3 päringute mõistmiseks, kuid **ei ole soovitatav tootmiskasutuse muster**, sest brauserisse jõudev JavaScript ei sobi püsivate ligipääsuvõtmete hoidmiseks.

Loo fail nimega `index.html` ja lisa sinna järgmine sisu:

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Pilvio S3 faili üleslaadimine</title>
    <script src="https://sdk.amazonaws.com/js/aws-sdk-2.1371.0.min.js"></script>
</head>
<body>
    <h1>Faili üleslaadimine Pilvio S3-e</h1>

    <input type="file" id="file-chooser" />
    <button id="upload-button">Lae üles S3-e</button>
    <div id="results" style="margin-top: 20px; padding: 10px; border: 1px solid #eee;"></div>

<script type="text/javascript">
    // --- DEMO SEADISTUS AINULT LOKAALSEKS TESTIMISEKS ---
    // ⚠️ ÄRA kasuta siin tootmisvõtmeid.
    // ⚠️ ÄRA deploy' seda faili avalikule veebilehele koos päris võtmetega.
    const PILVIO_S3_ENDPOINT_URL = "https://s3.pilw.io"; // Pilvio S3 endpoint
    const PILVIO_ACCESS_KEY_ID = "SINU_ACCESS_KEY_ID_SIIA";
    const PILVIO_SECRET_ACCESS_KEY = "SINU_SECRET_ACCESS_KEY_SIIA";
    const PILVIO_BUCKET_NAME = "SINU_BUCKETI_NIMI_SIIA";
    const PILVIO_S3_REGION = "eu-west-1"; // Pilvio S3 vaikeregioon, vajadusel muuda

    // Initsialiseeri S3 klient
    const s3 = new AWS.S3({
        accessKeyId: PILVIO_ACCESS_KEY_ID,
        secretAccessKey: PILVIO_SECRET_ACCESS_KEY,
        endpoint: new AWS.Endpoint(PILVIO_S3_ENDPOINT_URL),
        s3ForcePathStyle: true, // Vajalik paljude S3-ühilduvate teenuste jaoks, sh Pilvio
        signatureVersion: 'v4',
        region: PILVIO_S3_REGION
    });

    // HTML elemendid
    const fileChooser = document.getElementById('file-chooser');
    const uploadButton = document.getElementById('upload-button');
    const resultsDiv = document.getElementById('results');

    // Nupuvajutuse kuulaja
    uploadButton.addEventListener('click', function() {
        const file = fileChooser.files[0];
        resultsDiv.innerHTML = ''; // Tühjenda varasemad tulemused

        if (!file) {
            resultsDiv.innerHTML = 'Viga: Palun vali fail üleslaadimiseks.';
            return;
        }

        // Faili nimi S3 bucketis (võid lisada kausta prefiksi)
        const fileNameInS3 = 'demo-uploads/' + file.name;

        const params = {
            Bucket: PILVIO_BUCKET_NAME,
            Key: fileNameInS3,
            ContentType: file.type,
            Body: file,
            ACL: 'public-read' // ⚠️ Ettevaatust! See muudab faili avalikult loetavaks.
                               // Tootmiskeskkonnas kaalu privaatseid ACL-e ja pre-signed URL-e.
        };

        resultsDiv.innerHTML = `Laen üles faili: ${file.name}...`;

        s3.putObject(params, function(err, data) {
            if (err) {
                console.error("Üleslaadimise viga:", err);
                resultsDiv.innerHTML = 'VIGA ÜLESLAADIMISEL: ' + err.message;
                return;
            }
            resultsDiv.innerHTML = `Fail '${file.name}' edukalt üles laetud kui '${fileNameInS3}'.<br>Andmed: ${JSON.stringify(data)}<br><br>Loetlen objekte 'demo-uploads/' kaustast:`;
            listBucketObjects(); // Kuva bucket'i sisu pärast edukat üleslaadimist
        });
    }, false);

    // Funktsioon bucket'is olevate objektide loetlemiseks
    // Demo jaoks kasulik, kuid tootmises ei tasu brauserile anda rohkem õigusi kui vaja.
    function listBucketObjects() {
        const params = {
            Bucket: PILVIO_BUCKET_NAME,
            Prefix: 'demo-uploads/' // Näita ainult 'demo-uploads/' kausta sisu
        };

        s3.listObjectsV2(params, function(err, data) {
            if (err) {
                console.error("Objektide loetlemise viga:", err);
                resultsDiv.innerHTML += '<br>VIGA OBJEKTIDE LOETLEMISEL: ' + err.message;
                return;
            }
            let objectKeysHTML = "";
            if (data.Contents.length === 0) {
                objectKeysHTML = "Kaustas 'demo-uploads/' ei leidu objekte.";
            } else {
                data.Contents.forEach(function(obj) {
                    objectKeysHTML += obj.Key + "<br>";
                });
            }
            resultsDiv.innerHTML += "<br>" + objectKeysHTML;
        });
    }
</script>
</body>
</html>
```

**Olulised koodis tähelepanu vajavad asjad:**

- **Ligipääsuvõtmed brauseris:** Selles näites on võtmed pandud JavaScripti sisse ainult selleks, et demonstreerida Pilvio S3 ja CORS-i tööpõhimõtet lokaalses testkeskkonnas. **Tootmiskeskkonnas ei tohi päris `Access Key ID` ja `Secret Access Key` väärtusi kunagi brauserisse saata ega `index.html` faili kirjutada.** Õige lahendus on kasutada backendi kaudu loodud pre-signed URL-e või muid ajutisi piiratud õigustega mandaate.
- **AWS SDK versioon:** Kasutasin uuemat AWS SDK versiooni (`2.1371.0`). Kontrolli alati [AWS SDK for JavaScript dokumentatsioonist](https://aws.amazon.com/sdk-for-javascript/) uusimat stabiilset versiooni ja kasuta seda.
- `s3ForcePathStyle: true`: See on oluline parameeter paljude S3-ühilduvate teenuste (sh Pilvio) puhul, et URL-id korrektselt moodustataks.
- `signatureVersion: 'v4'`: Kasutame allkirjastamise versiooni v4, mis on kaasaegne ja turvaline.
- `ACL: 'public-read'`: See muudab üleslaetud faili avalikult internetis ligipääsetavaks. See võib olla demo jaoks mugav, kuid enamasti ei ole see tootmises sobiv vaikimisi valik. Turvalisem lähenemine on laadida failid üles privaatselt ja anda neile ligipääs vajaduse korral allkirjastatud URL-ide kaudu.
- `listObjectsV2`: Kasutame `listObjectsV2` meetodit objektide loetlemiseks, mis on uuem ja eelistatum versioon võrreldes `listObjects`-iga.
- `listObjectsV2` õigused: üleslaadimise demo jaoks ei ole brauserist objektide loetlemine tegelikult vajalik. Tootmislahenduses tasub järgida minimaalse õiguse põhimõtet ja jätta brauserile ainult need õigused, mida ta päriselt vajab.

## Samm 2: esmane testimine brauseris (ja CORS viga)

Salvesta `index.html` fail ja ava see oma veebibrauseris (topeltklikk failil).

Proovi valida fail ja vajuta "Lae üles S3-e" nuppu. Suure tõenäosusega näed brauseri konsoolis (ava see paremkliki -> Inspect -> Console abil) viga, mis sarnaneb järgmisega:

```
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at https://s3.pilw.io/SINU_BUCKETI_NIMI_SIIA/demo-uploads/failinimi.jpg. (Reason: CORS header 'Access-Control-Allow-Origin' missing).
```

Või midagi sarnast, mis viitab CORS probleemile. See on ootuspärane!

**Miks see viga tekib?**

Veebibrauserid rakendavad turvamehhanismi nimega "Same-Origin Policy" (sama päritolu poliitika). See takistab veebilehel (sinu `index.html`, mis on avatud `file:///` protokolli alt või mõnelt muult domeenilt) teha päringuid teisele domeenile (antud juhul `s3.pilw.io`), kui sihtdomeen (S3 *bucket*) pole seda spetsiaalselt lubanud. CORS (Cross-Origin Resource Sharing) on mehhanism, mis võimaldab serveritel (S3 *bucketil*) määrata, millised teised päritolud (domeenid, protokollid, pordid) tohivad tema ressurssidele ligi pääseda.

## Samm 3: CORS poliitika seadistamine Pilvio S3 *bucketile*

Et lubada sinu veebilehel faile Pilvio S3 *bucketi* üles laadida, peame *bucketile* seadistama CORS-poliitika. See poliitika ütleb S3-le, et sinu veebilehe päritolu on usaldusväärne ja päringud on lubatud.

### Enne CORS-i seadistamist: oluline vahe CORS-i ja autentimise vahel

CORS ei ole turvamehhanism, mis kaitseb sinu S3 võtmeid. CORS määrab ainult selle, millistelt veebilehtedelt brauser päringuid teha lubab. Kui paned päris ligipääsuvõtmed brauserisse, siis CORS ei takista nende sattumist kasutaja kätte.

Seetõttu tuleb CORS-i käsitleda eraldi teemana ja **mitte kasutada seda põhjendusena, miks võiks võtmeid frontendis hoida**.

Loome XML-faili nimega `cors-rules.xml` järgmise sisuga:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<CORSConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
<CORSRule>
    <ID>AllowWebUploads</ID>
    <AllowedOrigin>*</AllowedOrigin>
    <AllowedMethod>GET</AllowedMethod>
    <AllowedMethod>HEAD</AllowedMethod>
    <AllowedMethod>PUT</AllowedMethod>
    <AllowedMethod>POST</AllowedMethod>
    <AllowedHeader>*</AllowedHeader>
    <MaxAgeSeconds>3000</MaxAgeSeconds>
    <ExposeHeader>ETag</ExposeHeader>
</CORSRule>
</CORSConfiguration>
```

**TÄHTIS HOIATUS TURVALISUSE KOHTA:**

- Ülaltoodud CORS näide on mõeldud ainult testimiseks ja õppimiseks.
- `<AllowedOrigin>*</AllowedOrigin>` ning `<AllowedHeader>*</AllowedHeader>` on tootmiskeskkonnas liiga leebed.
- **Veel olulisem:** isegi rangem CORS-poliitika ei tee brauserisse kirjutatud püsivaid S3 võtmeid turvaliseks.
- Tootmises piira `AllowedOrigin` ainult oma tegelike domeenidega.
- Võimalusel väldi üldse püsivate võtmete kasutamist frontendis ja eelista pre-signed URL-e või backendi vahendatud üleslaadimist.

**CORS-reeglite rakendamine `s3cmd` tööriistaga:**

1. Veendu, et sul on `s3cmd` paigaldatud ja seadistatud oma Pilvio S3 ligipääsuvõtmetega. Seadistamiseks käivita `s3cmd --configure` ja sisesta oma Pilvio *Access Key*, *Secret Key* ning Pilvio S3 *endpoint* (`s3.pilw.io`). Jäta vaikimisi regiooniks `US` või vali muu, see ei tohiks Pilvio puhul oluline olla, kui *endpoint* on õige.

2. Salvesta ülaltoodud XML-sisu faili nimega `cors-rules.xml` samasse kausta, kus on sinu `s3cmd` konfiguratsioonifail (või anna täistee failini).

3. Käivita terminalis järgmine käsk, asendades `SINU_BUCKETI_NIMI_SIIA` oma tegeliku *bucketi* nimega:

   ```bash
   s3cmd setcors cors-rules.xml s3://SINU_BUCKETI_NIMI_SIIA
   ```

   Näiteks, kui su *bucketi* nimi on `minu-pilvio-bucket` ja `s3cmd` on seadistatud vaikimisi konfiguratsioonifailiga:

   ```bash
   s3cmd setcors cors-rules.xml s3://minu-pilvio-bucket
   ```

Kui käsklus õnnestub, on CORS-poliitika sinu *bucketile* rakendatud.

> **Märkus:** Kui sa ei soovi `s3cmd` kasutada, siis CORS-reegleid saab seadistada ka graafilise liidese kaudu. Selleks sobib näiteks Cyberduck nimeline tarkvara.

## Samm 4: lõplik testimine

Nüüd, kui CORS-poliitika on (loodetavasti korrektselt) seadistatud:

1. **Värskenda oma `index.html` lehte brauseris** (Ctrl+R või Cmd+R). Mõnikord on vaja ka brauseri vahemälu tühjendada (Ctrl+Shift+R või Cmd+Shift+R).
2. Proovi uuesti faili valida ja üles laadida.

Kui kõik on õigesti seadistatud, peaks fail nüüd edukalt sinu Pilvio S3 *bucketi* üles laadima ja sa näed "results" div-is teadet eduka üleslaadimise kohta ning seejärel loetelu objekte sinu määratud `demo-uploads/` kaustast.

**Veaotsing:**

- Kontrolli veelkord brauseri konsooli vigade osas.
- Veendu, et sinu Pilvio S3 ligipääsuvõtmed ja *bucketi* nimi on JavaScripti koodis õiged.
- Kontrolli, et CORS-reeglid on *bucketile* korrektselt rakendatud. Võid proovida `s3cmd getcors s3://SINU_BUCKETI_NIMI_SIIA`, et näha hetkel kehtivat poliitikat.
- Kui kasutasid `AllowedOrigin` reeglis konkreetset domeeni (mitte `*`), veendu, et testid lehte täpselt sellelt domeenilt (sh protokoll http/https). Kui avad `index.html` otse failisüsteemist (`file:///`), siis `*` on ainus `AllowedOrigin`, mis tavaliselt töötab, aga see on ebaturvaline. Parem on testimiseks kasutada lihtsat lokaalset veebiserverit (nt Pythoni `http.server` või VS Code Live Server laiendus).
- Kui testid seda näidet ainult lokaalselt, kasuta võimalusel eraldi testimiseks loodud piiratud õigustega võtmeid, mitte oma peamisi tootmisvõtmeid.
- Kui plaanid lahendust päriselt kasutusele võtta, ära liigu edasi brauserisse kirjutatud püsivate võtmetega. Ehita üleslaadimine ümber pre-signed URL-ide või backendi vahendatud päringute peale.

**CORS konfiguratsiooni eemaldamine (vajadusel):**

Kui soovid mingil põhjusel CORS konfiguratsiooni *bucketilt* eemaldada, saad seda teha `s3cmd` käsuga:

```bash
s3cmd delcors s3://SINU_BUCKETI_NIMI_SIIA
```

## Kokkuvõte

Selles juhendis õppisime, kuidas brauseripõhine failide üleslaadimine Pilvio S3 *bucket'isse* tehniliselt töötab ja miks CORS-poliitika on selle jaoks vajalik. Näide sobib hästi S3 päringute, CORS-i ja üleslaadimisloogika katsetamiseks lokaalses arenduskeskkonnas.

Oluline on aga meeles pidada, et **päris ligipääsuvõtmeid ei tohi avalikku frontend-koodi panna**. Tootmiskeskkonnas tuleks failide üleslaadimiseks kasutada turvalisemat mustrit, näiteks backendi kaudu loodud pre-signed URL-e või muud serveripoolset vahendust. Samuti peaks CORS-poliitika olema piiratud ainult vajalike domeenide, meetodite ja päistega.

Pilvio S3 pakub paindlikku ja skaleeritavat lahendust sinu andmete hoidmiseks. Kombineerides seda *front-end* tehnoloogiatega, saad luua võimsaid ja interaktiivseid veebirakendusi.

## Kasulikud lingid

- [Pilvio avaleht](https://pilvio.com/)
- [AWS SDK for JavaScript V2 Dokumentatsioon](https://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/getting-started.html) (Kuigi V3 on uuem, on V2 endiselt laialt kasutusel ja sarnaneb sinu viidatud artikli näitega)
- [s3cmd ametlik veebileht](https://s3tools.org/s3cmd)
- [MDN Web Docs: Cross-Origin Resource Sharing (CORS)](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)

Loodetavasti oli see juhend kasulik!
