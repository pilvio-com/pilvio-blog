---
title: "Long read: VMware kohapealse lahenduse kuluarvutusloogika analüüs ja mudel"
author: "Kaur Kiisler"
publishedAt: "4. juuni 2025"
category: "Üldine"
tags: ["pilvio.pro", "tco", "vmware"]
featured: false
excerpt: "Käesolev aruanne analüüsib kriitiliselt VMware kohapealsete lahenduste tüüpilisi kuluarvutusloogikaid."
imageUrl: "/media/vmware_long.png"
---

## 1. Kokkuvõte

Käesolev aruanne analüüsib kriitiliselt VMware kohapealsete lahenduste tüüpilisi kuluarvutusloogikaid, mida sageli kasutatakse turundusmaterjalide kalkulaatorites, ning esitab täiustatud ja põhjalikuma mudeli kogukulu (TCO) hindamiseks. VMware kohapealsete lahenduste TCO täpne hindamine on keerukas ülesanne, mis hõlmab arvukalt otseseid ja kaudseid kulusid, mida lihtsustatud kalkulaatorid sageli eiravad. Olukorda komplitseerib veelgi VMware'i hiljutine litsentsimisstrateegia muutus Broadcomi omanduses, mis on fundamentaalselt muutnud tarkvarakulude struktuuri. Turunduskalkulaatorite loogika keskendub sageli esialgsetele, nähtavatele kuludele, nagu baasriistvara ja tarkvara esialgsed litsentsitasud, jättes tähelepanuta märkimisväärsed jooksvad tegevuskulud (OpEx). Nende hulka kuuluvad energiatarve, jahutus, IT-administratsioon, tarkvara elutsükli kogukulud ja füüsilise infrastruktuuri üldkulud. Selline lähenemine viib kogukulu olulise alahindamiseni. Käesolev aruanne pakub nende puuduste kriitilist analüüsi ja esitleb mitmetahulist TCO mudelit. See mudel on loodud selleks, et anda organisatsioonidele realistlikum ja usaldusväärsem raamistik VMware kohapealsete investeeringute hindamiseks. Turunduskalkulaatorite peamine probleem seisneb nende loomupärases kalduvuses esitleda madalamat esialgset hinda klientide ligimeelitamiseks, mis harva peegeldab pikaajalist rahalist kohustust. See aruanne püüab seda lahknevust parandada, pakkudes läbipaistvat ja kõikehõlmavat lähenemist kulude hindamisele.

Oluline märkus: see tekst kasutab 2025. aasta juuni hinnanäiteid ja ligikaudseid vahetuskursse. Võta seda otsustusraamistikuna, mitte jooksva hinnakirjana. Enne päris ostuotsust küsi värsked hinnapakkumised Broadcomilt, riistvarapartneritelt ja pilvepakkujalt.

Hinnaviited on selles versioonis värskendatud 16. märtsil 2026. Tarkvara, pilve ja hosted teenuste hinnad on allpool seotud eeskätt ametlike hinnalehtede või ametlike dokumentidega. Riistvaratabelid on jäetud teadlikult eelarvestuse orientiiriks, sest serveri, SAN-i ja võrguriistvara lõpphind sõltub liiga palju kanalist, garantiist, konfiguratsioonist ja allahindlustest.

Kui tahad sellest artiklist ainult kolm asja meelde jätta:

- ära võrdle VMware kohapealset lahendust ainult hosti- või litsentsihinna järgi
- pane 3-5 aasta vaates samasse tabelisse energia, tööjõud, varundus, DR ja ruumikulud
- võrdle eraldi hüperskaalerit ja odavamat hosted teenusepakkujat, sest nende kulumudel ei ole sama

## 2. Sissejuhatus: VMware Kohapealse Lahenduse Kulude Tegelikkus

VMware kohapealsete lahenduste kogukulu (Total Cost of Ownership, TCO) täpne hindamine on kriitilise tähtsusega strateegiliste IT-investeeringute tegemisel. Lihtsustatud lähenemised, mida sageli kasutatakse turundusmaterjalides, ei suuda tabada kõiki asjakohaseid kuluaspekte, viies potentsiaalselt ekslike otsusteni. Olukorda on viimastel aastatel oluliselt muutnud Broadcomi poolt VMware'i omandamine ja sellele järgnenud litsentsimismudelite reform.

### Broadcomi Litsentsimuudatuste Mõju VMware TCO-le

Broadcomi strateegia on toonud kaasa fundamentaalseid muutusi VMware'i toodete, nagu vSphere ja vSAN, litsentsimises. Kõige olulisem neist on loobumine püsivatest (perpetual) litsentsidest uute müükide puhul ning üleminek ainult tellimuspõhisele (subscription-only) mudelile. See tähendab, et organisatsioonid ei saa enam osta vSphere'i või vSANi ühekordse tasu eest, vaid peavad sõlmima ajaliselt piiratud tellimusi, tavaliselt 1, 3 või 5 aastaks. Pikemad tellimusperioodid võivad pakkuda soodsamaid tingimusi. See muudatus transformeerib VMware'i kulud peamiselt kapitalimahukast (CapEx) mudelist tegevuskuludele (OpEx) keskenduvaks.

Teine märkimisväärne muudatus on üleminek tuumapõhisele (per-core) litsentsimisele, asendades varasema protsessoripõhise (per-socket) mudeli. Nüüd tuleb litsentseerida serveri kõik füüsilised tuumad, kusjuures kehtib miinimummäära nõue – näiteks loetakse üks protsessor vähemalt 16-tuumaliseks, isegi kui sellel on tegelikult vähem tuumasid. See mõjutab eriti väiksemaid ja keskmise suurusega ettevõtteid (VKE), kes võivad olla sunnitud maksma kasutamata tuumade eest. Väiksemas otsas tähendab see, et isegi tagasihoidliku protsessoriga host võib hinnastuses muutuda 16-tuuma serveriks; samal ajal on `Essentials Plus` ametliku tootevõrdluse järgi tellimuspõhine 96-tuuma väikepakett kuni kolmele hostile.

Lisaks on Broadcom lihtsustanud VMware'i tooteportfelli, koondades tooted suurematesse pakettidesse nagu VMware Cloud Foundation (VCF) ja vSphere Foundation (VVF). Kuigi see võib tunduda lihtsustamisena, võib see sundida kliente maksma komponentide eest, mida nad tegelikult ei vaja. VVF sisaldab ametlike dokumentide järgi lisaks vSphere'ile ja vCenterile ka vSAN-i entitlement'i ning seob seega varem eraldi ostetud komponente tugevamalt ühe subscription-rida alla.

Need litsentsimuudatused on globaalsed ja jõustusid 2024. aasta alguses, mil VMware lõpetas püsivate litsentside müügi ja paljudel juhtudel ka olemasolevate püsilepingute toe (SnS) uuendamise. Kuigi Broadcom on teinud mõningaid järeleandmisi, näiteks pakkudes teatud püsivate litsentsiversioonide jaoks tasuta kriitilisi turvapaiku ka pärast toe lõppemist, on üldine suund selge: VMware'i tehnoloogia jätkuv kasutamine nõuab edaspidi aktiivset tellimust. See üleminek on Broadcomi jaoks strateegiline samm prognoositava korduva tulu suurendamiseks, mis aga klientidele, eriti neile, kes said kasu püsivate litsentside pikaajalisest kasutamisest, tähendab potentsiaalselt kõrgemaid ja jäigemaid jooksvaid kulusid.

### "Turunduskalkulaatori" Loogika Dekonstrueerimine: Levinud Väljajätmised ja Lihtsustused

Tüüpilised turunduskalkulaatorid, mida kasutatakse VMware kohapealsete lahenduste kulude hindamiseks, keskenduvad sageli piiratud hulgale otsestele kuludele. Need hõlmavad tavaliselt baasriistvara (protsessor, mälu, minimaalne salvestusruum) ja tarkvara esialgseid litsentsikulusid, mis tihti ei peegelda mitmeaastaste tellimuste kogusummat ega kõiki vajalikke lisandmooduleid.

Levinud väljajätmised hõlmavad:

* **Põhjalik riistvara**: Võrguseadmed (switchid, tulemüürid, ruuterid, mis ületavad baasvajadusi), spetsiaalsed salvestusmassiivid (SAN/NAS), robustsed katkematu toite allikad (UPS), füüsilise turvalisuse infrastruktuur.
* **Tarkvara täielik elutsükkel**: Mitmeaastased tellimuskulud, serveri operatsioonisüsteemide kliendipääsulitsentsid (CAL), oluliste lisandmoodulite (nt täiustatud varundus, avariitaaste lahendused, kui need pole sobivalt komplekteeritud) kulud, andmebaaside litsentsid.
* **Tegevuskulud**: Andmekeskuse ruum (rent/kolokatsioon), energiatarve (sageli oluliselt alahinnatud, PUE faktorit ignoreeritakse), jahutus, täismahus IT-administratsiooni palgad ja ajakulu, riistvara hoolduslepingud, tarkvara tugiteenuste uuendamine pärast esialgseid perioode, varundusmeediumid/väljaspool asukohta varundamine, avariitaaste testimine, interneti ribalaius ja personali koolitus.

Turunduskalkulaatorite peamine puudus on "jäämäe tipp" probleem. Need näitavad kulude nähtavat, sageli väiksemat osa, samal ajal kui palju suurem, veealune osa (pikaajalised tegevuskulud ja põhjalikud kapitalikulud) jääb varjatuks. See loob eksitava mulje taskukohasusest. Turunduskalkulaatorid on loodud müügivihjete genereerimiseks ja esmaseks kaasamiseks. Keerukas ja detailne TCO on selle etapi jaoks liiga koormav. Seetõttu lihtsustavad nad, keskendudes kergesti kvantifitseeritavatele ja sageli madalamatele esialgsetele kuludele. See lihtsustamine jätab aga välja kriitilised pikaajalised tegevus- ja terviklikud kapitalikulud, mis viib tegeliku TCO märkimisväärse alahindamiseni.

## 3. VMware Kohapealse Lahenduse Kogukulu (TCO) Põhikomponendid

Selles jaotises kirjeldatakse üksikasjalikult kõiki kuluosi, mis on liigitatud kapitali- (CapEx) ja tegevuskuludeks (OpEx). Need moodustavad täiustatud TCO mudeli aluse.

### A. Kapitalikulud (CapEx)

Kapitalikulud hõlmavad esialgseid investeeringuid riistvarasse, tarkvarasse ja infrastruktuuri ettevalmistamisse.

Riistvara puhul ei tasu otsida üht "õiget" avalikku hinda. Sama klassi serveri või salvestuslahenduse hind võib partneri, garantiitaseme, ketaste, protsessorite ja tugilepingu järgi väga palju kõikuda. Seetõttu on allolevad riistvaratabelid eelarvestuse orientiir, mitte värske hinnakiri.

#### Riistvaraline Infrastruktuur

* **Serverid (Arvutusvõimsus: Protsessor, RAM, Emaplaat, Kest, Toiteplokk)**: Serverikomponentide maksumus võib oluliselt varieeruda. Ettevõtlusklassi riistvara äriserveritele maksab tavaliselt $1000 kuni $2500 serveri kohta, kuid algtaseme serverid võivad maksta $3000–$5000 ja ettevõtlusklassi serverid üle $10 000. Näiteks komponentide hinnad: Xeon D protsessor (võib olla komplektis emaplaadiga), 2x 1TB ettevõtlusklassi SSD-d ($240), 64GB ECC RAM ($329), Supermicro 1U kest ($160), serveri emaplaat (nt Supermicro MBD-M11SDV-8C-LN4F-O hinnaga $929), toiteplokk ($100). Teine allikas pakub protsessoritele hinda $100-$800+, ECC RAM $50-$150 16GB mooduli kohta, 500GB SSD $50-$150, emaplaat $150-$350, toiteplokk $80-$200, kest $50-$250.

    **Tabel 1: Näidis Serverikonfiguratsioonid ja Hinnangulised Kulud (VKEdele)**

    | Serveri Roll      | Protsessor (Tüüp, Tuumad, Kogus) | RAM (GB) | Salvestusruum (Tüüp, Maht, SSD/HDD Kogus)        | Emaplaat (Klass) | Kest (Vormitegur) | Toiteplokk (Võimsus, Redundantsus) | Hinnangulised Komponendikulud (€) | Hinnanguline Serveri Kogukulu (€) |
    | :---------------- | :------------------------------- | :------- | :----------------------------------------------- | :--------------- | :---------------- | :----------------------------------- | :-------------------------------- | :-------------------------------- |
    | Väike Host        | Intel Xeon E-2xxx, 4-6 tuuma, 1  | 32-64    | 2x 1TB NVMe SSD (RAID1)                          | Serveriklass     | 1U Rack           | 450W, Mitte-redundantne              | 1200 - 1800                       | 1500 - 2200                       |
    | Keskmine Host     | Intel Xeon Silver, 8-12 tuuma, 2 | 128-256  | 4x 1TB NVMe SSD (RAID10) + 2x 4TB SATA HDD       | Serveriklass     | 2U Rack           | 750W, Redundantne                    | 3500 - 5500                       | 4000 - 6000                       |
    | Suur VKE Host     | Intel Xeon Gold, 16+ tuuma, 2    | 512+     | 6x 2TB NVMe SSD (RAID5/6) + SAN ühendus          | Serveriklass     | 2U/4U Rack        | 1200W, Redundantne                   | 7000 - 12000                      | 8000 - 14000                      |
   
    *\*Märkus: Hinnad on illustratiivsed ja võivad oluliselt varieeruda sõltuvalt konkreetsest konfiguratsioonist, tootjast ja hetke turuhindadest.* *Tabelis on kasutatud allikates `` toodud andmeid ja kohandatud eurodesse ligikaudse vahetuskursiga.* See tabel aitab lahti mõtestada abstraktse "serverikulu", näidates, kuidas erinevad konfiguratsioonid hinda mõjutavad ja pakkudes praktilist lähtepunkti riistvara eelarvestamiseks.

* **Salvestusruum (Keskendutakse algtaseme/keskklassi iSCSI SAN/NAS lahendustele VMware jaoks, sh kettad)**: Keskklassi salvestuslahendused võivad maksta $20 000–$50 000, samas kui täiustatud süsteemid võivad ulatuda kuuekohaliste summadeni. VKEde jaoks otsitakse algtaseme iSCSI SAN-e alla $10 000, kuigi see võib olla keeruline. Dell SCv3000 seeria võib olla alla $10k. Infortrendi karbid 10TB mahuga alla $10k. Broadberry CyberStore 212WSS-NVME (2U, 12 lahtrit NVMe Windows Storage Server) alates $4347 (ilma ketasteta). VKEdele/võimsatele kasutajatele mõeldud NAS-seadmed (4-8 lahtrit) maksavad $300-$600 (ilma ketasteta).

    **Tabel 2: Hinnangulised Kulud VKE SAN/NAS Lahendustele (10-20TB Kasutatav Maht)**

    | Salvestusruumi Tüüp | Näidis Bränd/Mudel              | Kettalahtrid | Ketta Tüüp (nt Ettevõtlusklassi SATA/SSD) | Vajalik Ketaste Arv Sihtmahuks | Ketaste Hinnanguline Kulu (€) | Kesta/Kontrolleri Hinnanguline Kulu (€) | Hinnanguline Kogukulu (€) |
    | :------------------ | :------------------------------ | :----------- | :---------------------------------------- | :----------------------------- | :---------------------------- | :-------------------------------------- | :------------------------ |
    | NAS                 | Synology DS1621+/QNAP TS-673A   | 6            | 6x 4TB Enterprise SATA HDD (RAID6)        | 6                              | 1200 - 1800                   | 700 - 1000                              | 1900 - 2800               |
    | iSCSI SAN (Algtase) | Dell PowerVault ME5012/HPE MSA 1060 | 12           | 8x 2.4TB 10K SAS HDD (RAID6)              | 8                              | 2000 - 3200                   | 5000 - 8000                             | 7000 - 11200              |
    | iSCSI SAN (SSD)     | Dell PowerVault ME5012/HPE MSA 1060 | 12           | 6x 1.92TB Enterprise SSD (RAID5)          | 6                              | 2400 - 4000                   | 5000 - 8000                             | 7400 - 12000              |
   
    *\*Märkus: Hinnad on illustratiivsed. Kasutatav maht sõltub RAID tasemest. Allikate loetelu on artikli lõpus.* Jagatud salvestusruum on VMware funktsioonide, nagu vMotion ja HA, jaoks kriitilise tähtsusega. See tabel aitab eelarvestada seda sageli märkimisväärset kulu.

* **Võrguseadmed (Switchid, Ruuterid, Tulemüürid VKE serveriruumi jaoks)**: Switchid, ruuterid ja tulemüürid võivad maksta $5000 kuni $50 000 sõltuvalt jõudlusest/turvalisusest. Serveripõhiselt võib võrguseadmete (switch, ruuter, tulemüür) kulu olla $250-$500 serveri kohta. VKE ruuter: $80-$350 või $150-$500. Hallatav switch: $250-$450 või $50-$500+. Riistvaraline tulemüür: $400-$2300 või $200-$1000+. Täiustatud tulemüürid võivad maksta $5000–$20 000 seadme kohta.

    **Tabel 3: Tüüpilised Kulud VKE Võrguinfrastruktuuri Komponentidele**

    | Komponent                   | Spetsifikatsiooni Märkused (nt PoE, Läbilaskevõime) | Hinnanguline Hinnavahemik (€) |
    | :-------------------------- | :------------------------------------------------- | :-------------------------- |
    | Hallatav GbE Switch (24-porti) | L2/L3 funktsioonid, VLAN tugi                      | 250 - 700                   |
    | Hallatav GbE PoE+ Switch (24-porti) | Sama, mis eelmine + PoE+                           | 400 - 1000                  |
    | VKE Tulemüüri Seade         | 500 Mbps - 1 Gbps läbilaskevõime, VPN tugi         | 300 - 1500                  |
    | VKE Ruuter                  | Gigabit WAN/LAN, VPN                               | 100 - 400                   |
   
    *\*Märkus: Hinnad on illustratiivsed. Allikate loetelu on artikli lõpus.* Võrgundus on fundamentaalne, kuid sageli TCO baasarvutustes tähelepanuta jäetud. See tabel toob esile usaldusväärse ja turvalise kohapealse keskkonna jaoks vajalike äriklassi seadmete kulud.

* **Katkematu Toite Allikas (UPS)**: UPSi kulu on tavaliselt märkimisväärne kõrvalrida, mitte tasuta lisavidin. Lisaks seadmele endale tuleb arvestada akude vahetuse, energiakao ja hooldusega. TCO vaates on oluline mitte hinnata ainult UPSi ostuhinda, vaid selle kogu elutsükli mõju.

* **Esialgsed Füüsilise Turvalisuse Meetmed (Juurdepääsukontroll, baasvalve serveriruumile)**: Füüsiline turvalisus on kohapealse lahenduse baaskulu. Isegi lihtne serveriruumi ukse juurdepääsukontroll, mõned kaamerad ja baasvalve kujutavad endast eraldi kapitalikulu ning võimalikku jooksvat seirekulu. Kui neid ei ole juba olemas, tuleb need TCO-s eraldi välja tuua.

* **Riistvara Amortisatsioon**: Tavapärane 3-5 aastane kasutusiga/amortisatsiooniperiood serveritele, salvestusruumile ja võrguseadmetele TCO arvutustes. See teisendab kapitalikulud aastaseks kuluks võrdluse tegemiseks. Riistvara ei kesta igavesti; selle maksumus tuleb TCO täpseks arvutamiseks jaotada selle kasulikule elueale. 3-5 aastane tsükkel on tavaline, mille järel jõudlus, töökindlus ja garantiikate vähenevad, käivitades sageli uuendustsükli.

#### Tarkvara Litsentsimine (Esialgsed ja Jooksvad Tellimuskulud)

* **VMware vSphere (vSphere Standard, Essentials Plus Kit; tuumapõhine, miinimumid, vCenter)**:
    Broadcomi ja VMware ametlikud dokumendid kinnitavad, et praegune loogika on tuumapõhine: kõik füüsilised tuumad tuleb litsentseerida ning iga füüsilise protsessori kohta kehtib vähemalt 16 tuuma miinimum. Ametlik tootevõrdlus näitab, et `vSphere Essentials Plus Kit` on tellimuspõhine väikepakett, mis sisaldab 96 tuumalitsentsi ja on mõeldud kuni kolmele füüsilisele serverile. `vSphere Standard` ja `vSphere Foundation (VVF)` on samuti tellimuspõhised, sisaldavad vastavalt `vCenter Standardit` ning VVF puhul ka vSAN-i entitlement'i. VVF puhul tuleb arvestada, et Broadcomi 2025. aasta ametliku info järgi sisaldub nüüd `0.25 TiB` vSAN-i mahtu iga litsentseeritud tuuma kohta.

    Sisuline muutus võrreldes varasema versiooniga on see, et ma ei hoia siin enam vanu MSRP dollarinumbreid. Broadcom ei kuva avalikke lõpphindu sama läbipaistvalt kui klassikalised hinnalehed ja päris ostuhind sõltub partnerist, regioonist ja lepingutingimustest. Seetõttu on mõistlik kasutada allolevat tabelit litsentsimismahu, mitte näilise hinnatäpsuse kontrolliks.

    **Tabel 4: VMware vSphere Litsentsimismahu Stsenaariumid**

    | Stsenaarium | Valitud vSphere pakett | Tegelikud tuumad | Litsentseeritud tuumad | Mida see tähendab ostus |
    | :---------- | :--------------------- | :--------------- | :--------------------- | :---------------------- |
    | 1 host, 1 CPU, 8 tuuma | vSphere Standard | 8 | 16 | vähemalt 16 tuuma tellimus |
    | 2 hosti, 1 CPU/host, 12 tuuma/CPU | vSphere Standard | 24 | 32 | vähemalt 32 tuuma tellimus |
    | 2 hosti, 1 CPU/host, 12 tuuma/CPU | vSphere Essentials Plus | 24 | 96 | üks Essentials Plus Kit (96 core) |
    | 3 hosti, 2 CPU/host, 16 tuuma/CPU | vSphere Foundation | 96 | 96 | 96 tuuma VVF tellimus |

    *\*Märkus: tabel on nüüd teadlikult hinnavaba. Selle eesmärk on vältida valearvestust tuumade ja pakettide lõikes. Päris hinna jaoks küsi partnerilt quote sama mahu ja sama lepinguperioodiga.* See tabel illustreerib uue litsentsimismudeli praktilist mõju tüüpilistele VKE konfiguratsioonidele, muutes abstraktse tuumapõhise loogika konkreetseks.

* **VMware Site Recovery Manager (SRM) / Live Site Recovery**: SRM oli varem litsentseeritud kaitstud virtuaalmasina (VM) kohta. Broadcomi ametlikud ostu- ja litsentsidokumendid näitavad, et tänane loogika käib `protected VM subscriptions` ja `protected capacity subscriptions` kaudu. VMware Live Site Recovery kasutamiseks on vaja `protected VM` tellimusi; protected capacity puudutab eeskätt Live Recovery Cloud / Cyber Recovery võimekust. Oluline TCO vaates on see, et DR jääb jätkuvalt eraldi hinnastatud kihiks, mitte ei tule automaatselt VCF/VVF baaspaketiga kaasa. Avalikku stabiilset hinnakirja Broadcom siin samal kujul ei kuva, seega tuleb see rida võtta partneri hinnapakkumisena.

* **Serveri Operatsioonisüsteemid (nt Windows Server Standard/Datacenter - tuumapõhine, CALid)**: Microsofti ametlik `Windows Server 2025` hinnaleht annab 16 tuuma viitehinnaks `Standard` väljaande puhul `$1,176` ja `Datacenter` väljaande puhul `$6,771`. Microsoft Store'i ametlik jaehind `Windows Server 2025 Standard 16 Core License Pack + 10 CALs` paketile on `$1,848`. Mõlemad väljaanded jäävad tuumapõhiseks ning nõuavad CAL-e. Praktikas tähendab see, et serveri OS-i rida on nüüd lihtsam siduda ametliku viitehinnaga, aga suuremate või mahuostu keskkondade puhul tuleb lõpphind ikkagi partnerilt.

    **Tabel 5: Windows Serveri Ametlikud Viitehinnad (2025)**

    | Serveri OS väljaanne | Litsentseeritavate tuumade arv | Ametlik viitehind (USD) | CALide märkus | Mida TCO-sse kanda |
    | :------------------- | :----------------------------- | :---------------------- | :------------ | :----------------- |
    | Windows Server 2025 Standard | 16 | $1,176 | CALid eraldi; Microsoft Store'is 16 core + 10 CAL pakett $1,848 | vähemalt tuumalitsents + vajalik CAL maht |
    | Windows Server 2025 Standard | 32 | $2,352 | CALid eraldi | 2x 16-core viitehind + vajalik CAL maht |
    | Windows Server 2025 Datacenter | 16 | $6,771 | CALid eraldi | vähemalt tuumalitsents + vajalik CAL maht |
    | Windows Server 2025 Datacenter | 32 | $13,542 | CALid eraldi | 2x 16-core viitehind + vajalik CAL maht |

    *\*Märkus: siin on kasutatud Microsofti ametlikke 2025. aasta viitehindu USD-s, mitte ligikaudseks teisendatud eurovahemikke. Mahulitsents, CSP ja kohaliku partneri hind võivad sellest erineda.* Serveri OS-i litsentsid on märkimisväärne tarkvarakulu. See tabel aitab mõista tuumalitsentside ja CALide kahekordset kulustruktuuri ilma kolmanda osapoole hinnaskaneeringuid dubleerimata.

* **Varundus- ja Replikatsioonitarkvara (nt Veeam, Commvault - kui ei ole mujal komplekteeritud/kaetud)**: Siin oli varem liiga palju kolmandate osapoolte hinnapakkumisi. Värskendatud kujul tasub toetuda ametlikele hinnalehtedele. `Veeam Data Platform Essentials` ametlik MSRP väikestele keskkondadele on `$89.20` litsentsi kohta aastas; seda müüakse 5-litsentsiliste kimpudena ja kuni 50 workloadi jaoks. `Commvault SaaS Pricing` leht kuvab `Virtual Machine Backup` reale ametliku hinnataseme `$102.49 per VM 10-pack / month`. Need kaks toodet ei ole üks-ühele võrreldavad, kuid annavad nüüd ametlikuma referentsi kui juhuslikud reselleri või Redditi hinnad. Tüüpilised RPO/RTO eesmärgid jäävad endiselt arhitektuurivaliku keskseks sisendiks: 2. taseme rakendustele on tüüpiline `RTO 4 tundi / RPO 2 tundi`, 3. tasemele `RTO 8-24 tundi / RPO 4 tundi`.

### B. Tegevuskulud (OpEx)

Tegevuskulud on jooksvad kulud, mis on seotud infrastruktuuri haldamise ja hooldamisega.

#### Andmekeskuse / Serveriruumi Kulud

* **Ruum (Kolokatsioonikulud 1/3 kuni täisräkile kui proksi)**: Hetzneri ametlik colocation-hinnakiri annab 14U jagatud räki hinnaks `€100/kuus`, `Rack Basic` lahenduse hinnaks `€167.23/kuus` ja `Rack Advanced` lahenduse hinnaks `€251.26/kuus`, millele lisanduvad seadistustasud ja elektrikulu. Need on head proksi-andmed ka siis, kui sa päriselt kolokatsiooni ei kasuta, sest need annavad aimu füüsilise ruumi, turvalisuse ja keskkonnatingimuste väärtusest. Oma serveriruumi puhul võib ruumi ettevalmistus ja tehniline valmidus endiselt tähendada märkimisväärset algkulu.

* **Energiatarve (Serverid, Salvestusruum, Võrguseadmed, Jahutus – PUE mõju)**: Elektrihind Eestis on dünaamiline ja TCO mudelis ei tasu seda lahata ühe vana tariifinäite kaudu. Kasuta oma viimase 12 kuu tegelikku all-in hinda või vähemalt turuandmete ja võrguarve kombinatsioonist saadud keskmist. Konservatiivse rusikareeglina on ettevõtte kogukulu puhul sageli mõistlik testida vähemalt vahemikke `0.15`, `0.20` ja `0.25 €/kWh`. Energiatõhususe näitaja `PUE` on endiselt keskne: üldine keskmine võib olla umbes `1.8`, väga tõhusad andmekeskused sihivad alla `1.2`, samas kui väikesed serveriruumid jäävad sageli pigem `1.6-2.0` vahemikku.

    **Tabel 6: Hinnangulised Aastased Energiakulud Näidiskonfiguratsioonidele**

    | Konfiguratsioon (nt Väike/Keskmine/Suur VKE Serveriseadistus CapEx-ist) | Hinnanguline Riistvara Koguvõimsus (W) | Eeldatav PUE | Energiatarve Kokku (kWh/aastas) | Segatud Elektrihind (€/kWh - Eesti näide) | Hinnanguline Aastane Energiakulu (€) |
    | :--------------------------------------------------------------------- | :------------------------------------ | :----------- | :----------------------------- | :--------------------------------------- | :---------------------------------- |
    | "Väike VKE (1-2 serverit, NAS, baasvõrk)"                              | 500 - 800                             | 1.8          | 7884 - 12614                   | 0.18                                     | 1419 - 2271                         |
    | "Keskmine VKE (3-4 serverit, SAN, võrk)"                               | 1500 - 2500                           | 1.6          | 21024 - 35040                  | 0.18                                     | 3784 - 6307                         |
   
    *\*Märkus: Hinnad on illustratiivsed. PUE väärtus on hinnanguline VKE keskkonnale. Allikate loetelu on artikli lõpus.* Energia on suur korduvkulu. See tabel näitab, kuidas riistvaravalikud, operatiivne tõhusus (PUE) ja kohalikud elektrihinnad kombineeruvad märkimisväärseks kuluks.

* **Jahutus (osana energiakulust või eraldi, kui andmed võimaldavad)**: Tavaliselt arvestatakse jahutust PUE kaudu. Kui sul on eraldi serveriruumi kliimaseade või muu mõõdetav jahutuskulu, tasub see TCO mudelis siiski omaette reale tõsta, et energiakulu ei muutuks liiga üldiseks hinnanguks.

#### IT Administratsioon ja Haldus

* **Personalikulud (VMware Süsteemiadministraatori palk - Eesti keskmine)**: Palgad.ee värske palgavahemik `Systems Administrator` rollile Eestis näitab netona ligikaudu `€1,381-€2,266` kuus ning brutona umbes `€1,529-€3,675` kuus. VMware-spetsiifilise oskusega roll võib sellest liikuda ülespoole. TCO vaates on mõistlik kasutada oma ettevõtte tegelikku palgataset, mitte ainult üldist turuvahemikku.

* **Ajaeraldus (Hinnanguline % administraatori ajast VMware haldamiseks VKEdes)**: Uuring näitab, et 30% VMware administraatori ajast kulub hooldusele; keskmiselt 12.3 tundi nädalas VMware lahenduste haldamisele ja paikamisele. See uuring ei ole spetsiifiline VKEdele, kuid annab üldise ettekujutuse. Teine allikas viitab, et virtualiseerimiseelne administraatori/serveri suhe on 1:10 kuni 1:30, virtualiseerimisjärgne võib olla palju kõrgem (nt 1:200), kuid varieerub suuresti. VKEdes võib üks administraator hallata kogu infrastruktuuri, seega on tema koguajast VMware'ile kuluv % asjakohasem. Administraatori aja kvantifitseerimine on ülioluline. Isegi kui VKE-l ei ole pühendunud VMware administraatorit, kulub osa üldise süsteemiadministraatori ajast VMware ülesannetele. Sellel "ajamaksul" on reaalne kulu.

#### Hooldus ja Tugi

* **Riistvara Hoolduslepingud (Garantiijärgsed, kui kohaldatav)**: Tavaliselt 5-10% või 10-15% esialgsest riistvarakulust aastas. $500k investeeringu puhul $50k-$75k aastas.
* **Tarkvara Tellimuste Uuendamine/Tugi (Pärast esialgset perioodi/kaasatud tuge)**: VMware tellimused sisaldavad nüüd tuge perioodi vältel. Muu tarkvara (OS, varundus) puhul lisanduvad jooksvad toe/tellimuse tasud.

#### Varundus- ja Avariitaasteoperatsioonid

* **Meedia, Väljaspool Asukohta Varundamine, Avariitaaste Testimine**: Varunduslintide/ketaste kulud, turvalised väljaspool asukohta varundusteenused ning ribalaiuse/personali ajakulu regulaarseks avariitaaste testimiseks. Kui varundus läheb objektsalvestusse või pilve, arvuta see oma mahupõhise retention'i, requestide, restore-testide ja väljavoolutasude põhjal, mitte ühe üldise "$/10 TB" reana.
* **Andmete Väljavool (pilvepõhise avariitaaste või varunduskomponentide puhul)**: Kui avariitaasteks või varunduseks kasutatakse pilve, kohalduvad väljavoolutasud. Suurtel pakkujatel on esimene `100 GB/kuus` sageli tasuta, kuid sealt edasi sõltub hind regioonist, sihtkohast ja mahuastmest. Taastetesti või päris DR juhtumi ajal võib see kulurida kiiresti nähtavaks muutuda, seega tuleb see TCO-sse eraldi sisse kirjutada.

#### Ühenduvus

* **Interneti Ribalaius**: Äriinternetiühenduse maksumus, maht sõltub replikatsioonivajadustest, kasutajate juurdepääsust jne.
* **VPN Kulud (kui kohaldatav kaughalduseks/avariitaasteks)**:
    AWS-i ametlik hinnaleht sisaldab nii tunnipõhist ühendustasu kui ka andmeedastust; Azure ja GCP puhul sõltub hind valitud gateway või tunnelite SKU-st, tsoonist ja liiklusest. Mõistlik lähenemine on võtta see TCO-sse eraldi reana otse ametlikust pricing kalkulaatorist, mitte eeldada, et "VPN on niikuinii marginaalne". Paljude selliste väikeste tegevuskulude summa võib koguneda märkimisväärseks korduvaks kuluks.

## 4. VMware Kohapealse Lahenduse TCO Võrdlusanalüüs

Selles jaotises võrreldakse VMware kohapealse lahenduse hinnangulist kogukulu (TCO) avalike pilveteenuste pakkujatega, kasutades tüüpilist VKE töökoormuse stsenaariumi. Eesmärk on anda kvantitatiivne alus kohapealse ja pilvepõhise infrastruktuuri kulude võrdlemiseks.

### Illustratiivne TCO Võrdlus: Kohapealne VMware vs. Avalik Pilv

Hüpoteetiline VKE töökoormus:

* **Hostid**: 2-3 füüsilist serverit
* **vCPU kokku**: 32-64
* **RAM kokku**: 256-512 GB
* **Salvestusruum**: 10-20 TB kiire (SSD-põhine) jagatud salvestusruum (SAN/NAS)
* **Võrk**: Baasvõrgundus (hallatavad switchid, tulemüür)
* **Varundus**: Igapäevane varundus, 30-päevane säilitamine
* **Avariitaaste (DR)**: Baasvõimekus (nt VMware Live Site Recovery või samaväärne)
* **TCO periood**: 3 aastat

Jaotises 5 toodud mudel on detailsem raamistik päris arvutuse tegemiseks. Järgmine võrdlustabel ei ole selle üks-ühele ümberkirjutus, vaid suunatundlik näide, mille eesmärk on näidata kohapealse ja pilvepõhise lähenemise erinevat kulukuju. Päris otsuse jaoks tuleks sama töökoormus läbi arvutada eraldi nii jaotise 5 mudelis kui ka konkreetse pilvepakkuja kalkulaatoris.

* **Amazon Web Services (AWS)**:
    * Arvutusvõimsus (EC2): üldotstarbelisteks võrdlusteks vaata `m6i/m7i` või `m6a/m7a` klasse, mäluoptimeeritud tööks `r6i/r7i` või `r6a/r7a`. Täpsed hinnad on regioonipõhised ja tuleks võtta ametlikult `EC2 On-Demand Pricing` lehelt või AWS Pricing Calculatorist.
    * Salvestusruum (EBS, S3): ametlik `EBS gp3` hinnaleht näitab, et lisaks `GB-kuu` reale tuleb eraldi arvestada provisioneeritud `IOPS`-i ja läbilaskevõimet. `S3` puhul sõltub hind storage class'ist ja regioonist.
    * Võrk: ametlik `Application Load Balancer` hinnaleht kasutab tunnipõhist tasu pluss `LCU`-põhist komponenti; `Site-to-Site VPN` lisab tunnipõhise ühendustasu ja andmeedastuse.
    * Tugi: AWS ametlik tugihinnakiri algab `Developer Support` puhul `$29/kuus` ja `Business Support` puhul `$100/kuus` või astmelise protsendiga kuludest.
    * Andmete väljavool: esimene `100 GB/kuus` on tavaliselt tasuta, edasi rakenduvad mahu- ja sihtkohapõhised tasud.

* **Microsoft Azure**:
    * Arvutusvõimsus (VMs): üldotstarbelisteks võrdlusteks vaata `Dv5/Dasv5` klasse, mäluoptimeeritud tööks `Ev5/Easv5`. Täpne hind sõltub regioonist, reservatsioonist, Azure Hybrid Benefit'ist ja VM-i generatsioonist, seega tasub see võtta ametlikust kalkulaatorist.
    * Salvestusruum (Managed Disks, Blob Storage): ametlikud `Managed Disks` ja `Blob Storage` hinnalehed annavad hinnastuse SKU, redundantsi ja regiooni järgi. VKE TCO jaoks on oluline eristada vähemalt `Premium SSD`, `Standard SSD` ja objektsalvestuse `Hot/Cool` klasse.
    * Võrk: `Load Balancer` ja `VPN Gateway` on Azure'is eraldi hinnastatud read ning neid ei tohiks liita automaatselt VM hinna sisse.
    * Tugi: `Standard Support` ametlik hind on endiselt `$100/kuus`.
    * Andmete väljavool: esimene `100 GB/kuus` on sageli tasuta, edasi sõltub hind regioonist ja mahuastmest.

* **Google Cloud Platform (GCP)**:
    * Arvutusvõimsus (Compute Engine): VKE võrdlustes on kõige mõistlikum alustada `E2`, `N2` ja `N2D` klassidest. Täpne hind sõltub regioonist, machine family'st, committed use allahindlustest ja sellest, kas kasutad spot-instantsse.
    * Salvestusruum (Persistent Disks, Cloud Storage): ametlik `Cloud Storage` hinnaleht algab `Standard` klassi puhul umbes `$0.02/GiB-kuus`, kuid lõpphind sõltub regioonist ja klassist. Persistent diskid on eraldi hinnastatud ketta tüübi ja mahu järgi.
    * Võrk: `Cloud VPN` ja muu `Network Connectivity` hinnastamine on GCP-s eraldi hinnaread; eriti taastestsenaariumi puhul arvesta ka võrguliikluse tasudega.
    * Tugi: `Standard Support` ametlik miinimum on `$29/kuus`; kõrgemad paketid liiguvad protsendipõhiseks.
    * Andmete väljavool: tasuta künnised ja hinnad sõltuvad sihtkohast ning teenusest, seega kasuta alati ametlikku pricing lehte või kalkulaatorit.

**Tabel 7: 3 Aasta TCO Ülevaade – Kohapealne vs. Näidispilv (Illustratiivne)**

| Kulukategooria                      | Kohapealne Aasta 1 (€) | Kohapealne Aasta 2 (€) | Kohapealne Aasta 3 (€) | Kohapealne 3-a TCO (€) | Näidispilv Aasta 1 (€) | Näidispilv Aasta 2 (€) | Näidispilv Aasta 3 (€) | Näidispilv 3-a TCO (€) |
| :---------------------------------- | :--------------------- | :--------------------- | :--------------------- | :--------------------- | :----------------- | :----------------- | :----------------- | :----------------- |
| Riistvara CapEx (Amorditud)         | 10 000                 | 10 000                 | 10 000                 | 30 000                 | 0                  | 0                  | 0                  | 0                  |
| Tarkvara Tellimus                   | 8 000                  | 8 000                  | 8 000                  | 24 000                 | 12 000             | 12 000             | 12 000             | 36 000             |
| Energia/Ruum                        | 3 000                  | 3 150                  | 3 300                  | 9 450                  | Sisaldub teenuses  | Sisaldub teenuses  | Sisaldub teenuses  | Sisaldub teenuses  |
| Administraatori Tööjõud             | 15 000                 | 15 750                 | 16 500                 | 47 250                 | 5 000              | 5 250              | 5 500              | 15 750             |
| Hooldus/Tugi (Riistvara, muu tarkvara) | 2 500                  | 2 500                  | 2 500                  | 7 500                  | Sisaldub teenuses  | Sisaldub teenuses  | Sisaldub teenuses  | Sisaldub teenuses  |
| Muud Tegevuskulud (Varundus, DR, ühenduvus) | 2 000                  | 2 000                  | 2 000                  | 6 000                  | 3 000              | 3 000              | 3 000              | 9 000              |
| **KOKKU** | **40 500** | **41 400** | **42 300** | **124 200** | **20 000** | **20 250** | **20 500** | **60 750** |

*Märkus: See tabel on tahtlikult jäme ja illustratiivne, mitte hinnapakkumine.* *"Näidispilv" esindab tüüpilist avaliku pilve pakkujat; reaalses võrdluses tuleks teha eraldi arvutus vähemalt AWS-i, Azure'i ja GCP jaoks.* Administraatori tööjõukulu pilves on väiksem, eeldades vähem infrastruktuuri haldamist. Pilve TCO võib tunduda esialgu madalam tänu puuduvale riistvara esialgsele investeeringule, kuid andmete väljavoolutasud, kõrgema taseme tugiteenused ja premium-klassi salvestusruumi/instantside tüübid võivad kulusid aja jooksul suurendada. Kohapealsel lahendusel on kõrge esialgne kapitalikulu, kuid potentsiaalselt madalamad ja prognoositavamad tegevuskulud, kui seda hästi hallatakse ja riistvara kasutatakse kogu selle eluea jooksul. "Tasuvuspunkt" varieerub oluliselt.

### Madalama Hinnaklassi Pilve-/Virtualiseerimisteenuse Pakkujate Kaalumine

On oluline märkida, et turul on ka teenusepakkujaid nagu Hetzner ja OVHcloud, kes pakuvad virtuaalmasinaid ja pühendatud servereid märkimisväärselt madalamate hindadega võrreldes suurte avaliku pilve pakkujatega. Hetzneri ametlik `CX22` plaan algab `€3.79/kuus`, samas kui OVHcloudi ametlik `VPS` hinnastus algab ligikaudu `$6.46/kuus` sõltuvalt regioonist ja paketist.

Kuigi need valikud võivad tunduda odavamad, kaasnevad nendega sageli vähem hallatud teenuseid, erinevad tugistruktuurid ja need võivad nõuda rohkem ettevõttesisest ekspertiisi haldamiseks ja turvamiseks. See võib potentsiaalselt nihutada osa "pilve" tegevuskuludest tagasi sisemistele tööjõukuludele. Seega, kuigi "odavamad" pilvevalikud eksisteerivad, vajab ka nende TCO hoolikat hindamist. Madalam esialgne hind võib kompenseeruda suurema halduskoormuse või vähemate integreeritud ettevõtlusfunktsioonidega (nt täiustatud turvalisus, DRaaS, põhjalikud haldusportaalid) võrreldes suurte avaliku pilve pakkujate või hästi struktureeritud VMware kohapealse keskkonnaga.

## 5. Täiustatud TCO Mudel VMware Kohapealsete Lahenduste Jaoks

Selles jaotises esitatakse raamistik VMware kohapealsete lahenduste kogukulu (TCO) põhjalikumaks hindamiseks. Mudel on loodud kohandatavaks ja arvestab laiaulatuslikult erinevaid kuluaspekte, mis ületavad turunduskalkulaatorite lihtsustatud loogikat.

### Raamistiku Ülevaade: Sisendid, Arvutusloogika, Väljundid

Esitatud TCO mudel on mõeldud kasutamiseks tabelarvutusprogrammis (nt Excel) ja katab tüüpiliselt 3- kuni 5-aastast TCO arvutusperioodi.

* **Sisendid**: Kasutajapõhised andmed, nagu hostide arv, virtuaalmasinate arv, tuumade arv protsessori kohta, salvestusruumi vajadus (TB), kohalikud tööjõumäärad (€/tund), elektrienergia maksumus (€/kWh), serveriruumi PUE, riistvara/tarkvara allahindlusmäärad.
* **Arvutusloogika**: Kõigi jaotises 3 kirjeldatud kapitalikulude (CapEx) summeerimine (amordituna aastaseks kuluks) ja kõigi aastaste tegevuskulude (OpEx) summeerimine.
* **Väljundid**: Aastane TCO, kumulatiivne TCO perioodi lõikes, TCO ühe virtuaalmasina kohta aastas.

### Kulukategooriate ja Arvutusmeetodite Üksikasjalik Jaotus

Järgnev tabelilaadne struktuur kirjeldab mudeli loogikat, tuues välja peamised kuluartiklid, mõõtühikud, näidisväärtused ja arvutusvalemid. Organisatsioonid peaksid asendama näidisväärtused oma tegelike või hinnanguliste andmetega.

**Tabel 8: Täiustatud TCO Mudeli Loogika VMware Kohapealsetele Lahendustele**

| Kuluartikkel                                  | Mõõtühik                       | Kogus (Näidis) | Ühiku Hind (€, Näidis) | Aastane Kulu (€) | Märkused/Eeldused                                                                                                         |
| :-------------------------------------------- | :----------------------------- | :------------- | :--------------------- | :--------------- | :------------------------------------------------------------------------------------------------------------------------ |
| **A. KAPITALIKULUD (AMORDITUD AASTASEKS)** |                                |                |                        |                  | Amortisatsiooniperiood: 3-5 aastat                                                                                        |
| Serverid (kokku)                              | tk                             | 3              | 4500                   | 4500             | Nt 3 serverit á €4500, amordituna 3a peale = (€4500*3)/3 = €4500/aastas. Vt Tabel 1.                                       |
| Jagatud Salvestusruum (SAN/NAS)               | seade                          | 1              | 8000                   | 2667             | Nt €8000 seade, amordituna 3a peale. Vt Tabel 2.                                                                        |
| Võrguseadmed (switchid, tulemüür)              | komplekt                       | 1              | 1500                   | 500              | Nt €1500 komplekt, amordituna 3a peale. Vt Tabel 3.                                                                     |
| Katkematu Toite Allikas (UPS)                 | tk                             | 1              | 1000                   | 333              | Nt €1000 UPS, amordituna 3a peale. Arvesta ka akuvahetustega elutsükli jooksul (eraldi OpEx reana või siin amortiseerituna). |
| Füüsiline Turvalisus (algne)                  | komplekt                       | 1              | 1200                   | 400              | Nt ukselukk, 1-2 kaamerat, amordituna 3a peale.                                                                           |
| VMware vSphere Litsentsid                     | tellimus / tuum                | vastavalt paketile | partner quote          | sisesta          | Nt Standard, Essentials Plus või VVF quote vastavalt litsentseeritud tuumadele. Vt Tabel 4.                              |
| Serveri OS Litsentsid (nt Windows Server)     | server/CAL                     | 3 serverit, 50 CAL | ametlik viitehind / quote | sisesta       | Nt Windows Server 2025 Standard 16 core + vajalik CAL maht. Vt Tabel 5.                                                   |
| Varundustarkvara Litsents                     | workload / VM                  | 15             | ametlik hinnaleht / quote | sisesta        | Nt Veeam Essentials või Commvault vastavalt valitud tootele.                                                             |
| **KAPITALIKULUD KOKKU (AASTANE)** |                                |                |                        | **arvuta** |                                                                                                                           |
| **B. TEGEVUSKULUD (AASTASED)** |                                |                |                        |                  |                                                                                                                           |
| Serveriruumi Ruum (kolokatsiooni analoog)     | räkk                           | 1              | 1200 (aastane)         | 1200             | Nt 1/3 räki hind Hetzneris.                                                                                               |
| Energiatarve                                  | kWh                            | 21024          | 0.18                   | 3784             | Nt 1.5kW riistvara @ PUE 1.6 * 24 * 365 * €0.18/kWh. Vt Tabel 6.                                                          |
| IT Administraatori Tööjõud (VMware haldus)    | % täistööajast                 | 25%            | 40000 (aastapalk)      | 10000            | Nt 25% ühe admin. ajast, kelle brutopalk on €40000/aastas.                                                               |
| Riistvara Hoolduslepingud                     | % algsest riistvarakulust      | 10%            | 23000 (riistvara kokku)| 2300             | 10% (serverid+SAN+võrk+UPS) algsest maksumusest.                                                                          |
| Tarkvara Toe Uuendused (v.a. VMware)          | aastane                        | 1              | 300                    | 300              | Nt Windows Server SA, backup tarkvara tugi.                                                                               |
| Varundusmeedia/Väljaspool Asukohta Varundamine | TB/aasta                       | 2              | 100                    | 200              | Nt pilvevarunduse salvestusruum.                                                                                          |
| Avariitaaste Testimine (tööjõud, ressursid)   | kord aastas                    | 1              | 500                    | 500              | Hinnanguline kulu.                                                                                                        |
| Internetiühendus                              | kuutasu                        | 12             | 50                     | 600              | Nt €50/kuus äriinterneti eest.                                                                                            |
| VPN Kulud (vajadusel)                         | kuutasu                        | 12             | 10                     | 120              | Nt baas VPN teenus.                                                                                                       |
| Koolituskulud (IT personal)                   | aastane                        | 1              | 500                    | 500              | Hinnanguline.                                                                                                             |
| **TEGEVUSKULUD KOKKU (AASTANE)** |                                |                |                        | **19504** |                                                                                                                           |
| **KOGUKULU (TCO) AASTAS** |                                |                |                        | **arvuta** |                                                                                                                           |

*Märkus: See tabel on illustratiivse iseloomuga ja näitab mudeli struktuuri. Reaalsed väärtused tuleb asendada organisatsiooni spetsiifiliste andmetega.*

Arvutusvalemite näited:

* Energiakulu Aastas (€) = (((Serverite koguvõimsus (W)+Salvestusruumi võimsus (W)+Võrguseadmete võimsus (W))/1000) × PUE × 24 × 365 × Elektrihind (€/kWh))
* Riistvara Aastane Amortisatsioonikulu (€) = Riistvaraühiku ostuhind (€)/Amortisatsiooniperiood (aastates)
* IT Administraatori Aastane Kulu VMware Haldusele (€) = Administraatori aastapalk (€) × (VMware haldusele kuluv aeg (%) / 100)

### Peamised Eeldused ja Kohandatavad Muutujad

Mudeli täpsus sõltub sisendandmete kvaliteedist. Kasutajad peavad kohandama järgmisi muutujaid:

* **Riistvara uuendustsükkel**: Tavaliselt 3, 4 või 5 aastat.
* **Tööjõukulud**: Kohalikud keskmised palgamäärad IT-spetsialistidele.
* **Elektrienergia tariifid**: Kohalikud keskmised hinnad (€/kWh), arvestades kõiki komponente.
* **PUE (Power Usage Effectiveness)**: Hinnanguline väärtus serveriruumi/andmekeskuse kohta (VKE puhul tõenäoliselt 1.6-2.0).
* **Allahindlusmäärad**: Riist- ja tarkvara ostmisel saadud allahindlused.
* **Mahtude näitajad**: VM-ide arv, hostide arv, tuumade arv, salvestusmaht.
* **Tarkvara valikud**: Konkreetsed varundus-, turva- jm tarkvarad ja nende litsentsimismudelid.

### Andmete Kogumise Juhised Mudeli Sisendite Jaoks

Täpse TCO arvutuse aluseks on usaldusväärsed sisendandmed. Soovitused andmete kogumiseks:

* **Riistvara ja tarkvara**: Hankige mitmelt tarnijalt ametlikud hinnapakkumised.
* **Elektrikulud**: Analüüsige viimaseid elektriarveid, et määrata keskmine €/kWh hind, arvestades kõiki tasusid.
* **Administraatori ajakulu**: Hinnake või küsitlege IT-personali, kui palju aega kulub VMware'i haldusele, hooldusele ja probleemide lahendamisele.
* **Kolokatsioon/Ruumikulud**: Kui kasutate kolokatsiooni, küsige pakkumisi. Oma ruumide puhul hinnake proportsionaalset osa üldkuludest (rent, kommunaalid).
* **Hoolduslepingud**: Uurige kehtivaid või tüüpilisi hoolduslepingute hindu riist- ja tarkvarale.

Staatiline TCO mudel on piiratud väärtusega. Selle tegelik väärtus seisneb kohandatavuses. Selgelt määratletud sisendmuutujad ja eeldused muudavad mudeli dünaamiliseks vahendiks stsenaariumide planeerimisel ja erinevate konfiguratsioonide võrdlemisel.

## 6. Strateegilised Soovitused Kulude Täpseks Hindamiseks ja Optimeerimiseks

Kogukulu (TCO) analüüs ei ole pelgalt arvutuslik harjutus, vaid strateegiline vahend, mis võimaldab teha teadlikke otsuseid ja optimeerida IT-investeeringuid. Järgnevalt on esitatud soovitused VMware kohapealsete lahenduste kulude täpsemaks hindamiseks ja optimeerimiseks.

### Lihtsustatud Kalkulaatoritest Edasiminek: Põhjaliku TCO Lähenemise Rakendamine

On ülioluline mõista, et esialgne ostuhind moodustab vaid väikese osa IT-investeeringu kogukulust. Organisatsioonid peavad arvesse võtma kõiki otseseid ja kaudseid kulusid kogu lahenduse elutsükli vältel. See hõlmab mitte ainult riist- ja tarkvara soetamist, vaid ka paigaldust, energiatarvet, jahutust, haldust, hooldust, koolitust ja lõpuks ka kasutuselt kõrvaldamist. Põhjalik TCO analüüs, nagu käesolevas aruandes kirjeldatud mudel, aitab vältida ootamatuid kulusid ja tagada eelarve täpsuse.

### Regulaarsed TCO Ülevaatused: Kohanemine Muutuvate Ärivajaduste ja Tehnoloogiaga

TCO analüüs ei tohiks olla ühekordne tegevus. IT-maailm on dünaamiline: tarkvara litsentsimismudelid (nagu VMware'i hiljutised muudatused), riistvara võimalused ja hinnad ning ärivajadused muutuvad pidevalt. Seetõttu on soovitatav TCO-d regulaarselt üle vaadata, näiteks kord aastas või enne suuremaid infrastruktuuri uuendusi. See tagab, et otsused põhinevad ajakohasel teabel ja et olemasolevad lahendused on endiselt kulutõhusad.

### Kuluoptimeerimise Võimaluste Tuvastamine Kohapealses Keskkonnas

Põhjalik TCO analüüs aitab tuvastada valdkondi, kus on võimalik kulusid optimeerida:

* **Riistvara**:
    * Õige suurusega lahendused (Right-sizing): Vältige üledimensioneerimist, valides serverid ja salvestusruumi vastavalt tegelikele vajadustele.
    * Serverite konsolideerimine: Maksimeerige virtualiseerimise abil füüsiliste serverite arvu vähendamist.
    * Energiatõhusad komponendid: Valige madalama energiatarbega protsessorid, toiteplokid ja muud komponendid.
    * Riistvara eluea pikendamine: Kaaluge kolmanda osapoole hoolduslepinguid pärast tootjapoolse garantii lõppu, kuigi see võib kaasa tuua muid riske seoses toe kvaliteedi ja varuosade saadavusega.
* **Tarkvara**:
    * Litsentside optimeerimine: Tagage vastavus litsentsitingimustele ilma üleliigsete litsentside eest maksmata. Hinnake hoolikalt VMware'i uute komplekteeritud pakkumiste (VVF, VCF) sobivust, et vältida mittevajalike komponentide eest tasumist.
    * Alternatiivsed tarkvaralahendused: Uurige võimalusel avatud lähtekoodiga või odavamaid alternatiive teatud funktsionaalsuste jaoks (nt seire, varundus), kui need vastavad nõuetele.
* **Tegevuskulud**:
    * PUE parandamine: Investeerige serveriruumi/andmekeskuse jahutuse ja õhuvoolu optimeerimisse, et vähendada energiakadu.
    * Energiahindade läbirääkimine: Suuremad tarbijad võivad saada soodsamaid elektritarne lepinguid.
    * Administratiivsete ülesannete automatiseerimine: Kasutage skripte ja automatiseerimisvahendeid (nt VMware Aria Suite, kui see on litsentsitud), et vähendada käsitsi tehtava töö mahtu ja säästa tööjõukulusid.

### Hübriidstsenaariumide ja Pilvemigratsiooni Käivitajate Hindamine

Kohapealse TCO analüüsi tulemused on väärtuslik sisend otsustamaks, kas teatud töökoormused tuleks viia pilve või rakendada hübriidmudeleid. Näiteks:

* Kui kohapealse avariitaaste (DR) lahenduse TCO (sh VMware Live Site Recovery litsentsid, eraldi riistvara teises asukohas, testimiskulud) osutub ülemäära kõrgeks, võib pilvepõhine DRaaS (Disaster Recovery as a Service) olla kulutõhusam alternatiiv.
* Muutuvate või ettearvamatute töökoormuste (nt hooajalised tipud, arendus- ja testimiskeskkonnad) puhul võib avaliku pilve skaleeritavus ja "maksa-kasutamise-eest" mudel pakkuda paremat kuluefektiivsust võrreldes kohapealse infrastruktuuri üledimensioneerimisega.
* Spetsiifilised rakendused, mis nõuavad suurt arvutusvõimsust lühiajaliselt (nt AI/ML treening), võivad olla ökonoomsemad käitada pilves.

TCO analüüs ei ole pelgalt numbrite kokkulöömine; see on vahend strateegiliseks otsustamiseks ja pidevaks parendamiseks. Eesmärk ei ole mitte ainult kuluarvu saamine, vaid kulutegurite mõistmine ja optimeerimisvõimaluste või strateegiliste nihete tuvastamine.

## 7. Järeldused

VMware kohapealsete lahenduste kogukulu (TCO) täpne hindamine on keerukas, kuid hädavajalik protsess iga organisatsiooni jaoks, kes kaalub või haldab sellist infrastruktuuri. Pealiskaudne kulude hindamine, mis tugineb sageli lihtsustatud turunduskalkulaatoritele, võib viia ekslike investeerimisotsuste ja eelarve ületamiseni. Need kalkulaatorid keskenduvad tavaliselt vaid osale esialgsetest kapitalikuludest, jättes arvestamata märkimisväärse hulga jooksvaid tegevuskulusid ja tarkvara elutsükli kogumaksumust.

Broadcomi poolt VMware'i omandamise järgsed litsentsimismudeli muudatused – üleminek püsivatelt litsentsidelt tellimuspõhisele mudelile, tuumapõhine hinnastamine koos miinimumtuumade nõudega ja toodete komplekteerimine – on TCO arvutamist veelgi komplitseerinud ja nihutanud kulustruktuuri tugevalt tegevuskulude (OpEx) poole. See nõuab organisatsioonidelt senisest hoolikamat ja pikaajalisemat finantsplaneerimist.

Käesolevas aruandes esitatud täiustatud TCO mudel pakub põhjalikku raamistikku VMware kohapealsete lahenduste tegelike kulude hindamiseks. See mudel arvestab laia spektrit kuluartikleid, sealhulgas:

* **Kapitalikulud**: Serverid, salvestusruum (SAN/NAS), võrguseadmed, UPS, füüsiline turvalisus ning tarkvara esialgsed (või esimese perioodi) litsentsi- ja tellimuskulud (VMware vSphere, serveri OS, varundustarkvara, VMware Live Site Recovery).
* **Tegevuskulud**: Serveriruumi/andmekeskuse ruumikulud, energiatarve (arvestades PUE-d), jahutus, IT-administratsiooni personalikulud ja ajakulu, riist- ja tarkvara hoolduslepingud ning tugiteenuste uuendamine, varundus- ja avariitaasteoperatsioonide kulud (sh meedia, väljaspool asukohta hoidmine, testimine, võimalikud andmete väljavoolutasud pilvepõhise DR korral), internetiühendus, VPN-kulud ja personali koolitus.

Selle detailse mudeli kasutamine võimaldab IT-juhtidel ja finantsanalüütikutel saada realistliku pildi VMware kohapealse lahenduse finantsmõjudest. See omakorda aitab paremini eelarvestada, põhjendada kulusid ja teha teadlikke võrdlusi alternatiivsete lahendustega, sealhulgas avalike pilveteenustega.

Lõpetuseks tuleb rõhutada, et kuigi kohapealsed lahendused pakuvad kontrolli ja kohandamisvõimalusi, kaasnevad nendega mitmesugused kulud, mida tuleb hoolsalt jälgida ja hallata. VMware'i üleminek tellimuspõhisele litsentsimisele rõhutab veelgi vajadust tugeva ja pideva TCO analüüsi järele, et tagada IT-investeeringute vastavus nii tehnoloogilistele kui ka rahalistele eesmärkidele.

## Allikad

Hinnaviited ja ametlikud viited värskendatud 16. märtsil 2026. Nimekiri on teadlikult kärbitud peamiselt ametlikele hinnalehtedele, tootjadokumentidele ja litsentsijuhistele. Riistvaravahemikud artikli sees on jäetud eelarvestuse orientiiriks, mitte esitletud kui üht ametlikku hinnakirja.

### VMware / Broadcom

- VMware End Of Availability of Perpetual Licensing and SaaS Services: https://blogs.vmware.com/cloud-foundation/2024/01/22/vmware-end-of-availability-of-perpetual-licensing-and-saas-services/
- VMware vSphere Product Line Comparison: https://www.vmware.com/docs/vmw-datasheet-vsphere-product-line-comparison
- Broadcom Knowledge Base Article 313548: https://knowledge.broadcom.com/external/article/313548
- Broadcom Knowledge Base Article 428834: https://knowledge.broadcom.com/external/article/428834
- VMware Live Site Recovery Licensing: https://techdocs.broadcom.com/us/en/vmware-cis/live-recovery/live-site-recovery/9-0/overview/site-recovery-manager-system-requirements/srm-licensing.html

### Microsoft

- Windows Server 2025 Licensing & Pricing: https://www.microsoft.com/en-us/windows-server/pricing
- Windows Server 2025 Licensing Guidance: https://www.microsoft.com/licensing/guidance/Windows-Server-2025
- Windows Server 2025 Standard 16 Core + 10 CALs, Microsoft Store: https://www.microsoft.com/en-us/d/windows-server-2025-standard/dg7gmgf0wzrw

### Backup

- Veeam Data Platform Essentials: https://www.veeam.com/products/veeam-data-platform/essentials.html
- Commvault SaaS Pricing: https://www.commvault.com/saas-pricing

### Hosted / Colocation

- Hetzner Colocation: https://www.hetzner.com/colocation/
- Hetzner Cloud: https://www.hetzner.com/cloud
- OVHcloud VPS: https://www.ovhcloud.com/en/vps/

### AWS

- AWS Pricing Calculator: https://aws.amazon.com/aws-cost-management/aws-pricing-calculator/
- Amazon EC2 On-Demand Pricing: https://aws.amazon.com/ec2/pricing/on-demand/
- Amazon EBS Pricing: https://aws.amazon.com/ebs/pricing/
- Amazon S3 Pricing: https://aws.amazon.com/s3/pricing/
- Elastic Load Balancing Pricing: https://aws.amazon.com/elasticloadbalancing/pricing/
- AWS Support Pricing: https://aws.amazon.com/premiumsupport/pricing/
- AWS VPN Pricing: https://aws.amazon.com/vpn/pricing/

### Azure

- Azure Pricing Overview: https://azure.microsoft.com/en-us/pricing/
- Azure Managed Disks Pricing: https://azure.microsoft.com/en-us/pricing/details/managed-disks/
- Azure Load Balancer Pricing: https://azure.microsoft.com/en-us/pricing/details/load-balancer/
- Azure VPN Gateway Pricing: https://azure.microsoft.com/en-us/pricing/details/vpn-gateway/
- Azure Standard Support: https://azure.microsoft.com/en-us/support/plans/standard

### Google Cloud

- Google Cloud Pricing Calculator: https://cloud.google.com/products/calculator
- Compute Engine Pricing: https://cloud.google.com/compute/all-pricing
- Cloud Storage Pricing: https://cloud.google.com/storage/pricing
- Network Connectivity Pricing: https://cloud.google.com/network-connectivity/pricing
- Cloud VPN Pricing: https://cloud.google.com/network-connectivity/docs/vpn/pricing
- Google Cloud Support: https://cloud.google.com/support

### Taust

- Palgad.ee, Systems Administrator salary in Estonia: https://www.palgad.ee/en/salaryinfo/information-technology/systems-administrator
- Elering Price List and Standard Terms and Conditions: https://elering.ee/en/price-list-and-standard-terms-and-conditions
- NREL: Measuring Efficiency with PUE: https://www.nrel.gov/computational-science/measuring-efficiency-pue
- Google Data Centers Efficiency: https://datacenters.google/efficiency
