---
title: "Long read: VMware on-premise lahenduse kuluarvutusloogika analüüs ja mudel"           # Required: Post title
author: "Kaur Kiisler"                   # Required: Author name
publishedAt: "4. juuni 2025"        # Required: Estonian format date
category: "Üldine"                  # Required: Post category
tags: ["pilvio.pro", "tco", "vmware"]          # Required: Array of tags
featured: false                         # Required: Boolean (only one post should be true)
excerpt: "Käesolev aruanne analüüsib kriitiliselt VMware kohapealsete (on-premise) lahenduste tüüpilisi kuluarvutusloogikaid."         # Optional: Custom excerpt (auto-generated if not provided)
imageUrl: "/media/vmware_long.png" # Optional: Featured image URL
---
# VMware on-premise lahenduse kuluarvutusloogika analüüs ja mudel

## 1. Kokkuvõte

Käesolev aruanne analüüsib kriitiliselt VMware kohapealsete (on-premise) lahenduste tüüpilisi kuluarvutusloogikaid, mida sageli kasutatakse turundusmaterjalide kalkulaatorites, ning esitab täiustatud ja põhjalikuma mudeli kogukulu (TCO) hindamiseks. VMware kohapealsete lahenduste TCO täpne hindamine on keerukas ülesanne, mis hõlmab arvukalt otseseid ja kaudseid kulusid, mida lihtsustatud kalkulaatorid sageli eiravad. Olukorda komplitseerib veelgi VMware'i hiljutine litsentsimisstrateegia muutus Broadcomi omanduses, mis on fundamentaalselt muutnud tarkvarakulude struktuuri. Turunduskalkulaatorite loogika keskendub sageli esialgsetele, nähtavatele kuludele, nagu baasriistvara ja tarkvara esialgsed litsentsitasud, jättes tähelepanuta märkimisväärsed jooksvad tegevuskulud (OpEx). Nende hulka kuuluvad energiatarve, jahutus, IT-administratsioon, tarkvara elutsükli kogukulud ja füüsilise infrastruktuuri üldkulud. Selline lähenemine viib kogukulu olulise alahindamiseni. Käesolev aruanne pakub nende puuduste kriitilist analüüsi ja esitleb mitmetahulist TCO mudelit. See mudel on loodud selleks, et anda organisatsioonidele realistlikum ja usaldusväärsem raamistik VMware kohapealsete investeeringute hindamiseks. Turunduskalkulaatorite peamine probleem seisneb nende loomupärases kalduvuses esitleda madalamat esialgset hinda klientide ligimeelitamiseks, mis harva peegeldab pikaajalist rahalist kohustust. See aruanne püüab seda lahknevust parandada, pakkudes läbipaistvat ja kõikehõlmavat lähenemist kulude hindamisele.

## 2. Sissejuhatus: VMware Kohapealse Lahenduse Kulude Tegelikkus

VMware kohapealsete lahenduste kogukulu (Total Cost of Ownership, TCO) täpne hindamine on kriitilise tähtsusega strateegiliste IT-investeeringute tegemisel. Lihtsustatud lähenemised, mida sageli kasutatakse turundusmaterjalides, ei suuda tabada kõiki asjakohaseid kuluaspekte, viies potentsiaalselt ekslike otsusteni. Olukorda on viimastel aastatel oluliselt muutnud Broadcomi poolt VMware'i omandamine ja sellele järgnenud litsentsimismudelite reform.

### Broadcomi Litsentsimuudatuste Mõju VMware TCO-le

Broadcomi strateegia on toonud kaasa fundamentaalseid muutusi VMware'i toodete, nagu vSphere ja vSAN, litsentsimises. Kõige olulisem neist on loobumine püsivatest (perpetual) litsentsidest uute müükide puhul ning üleminek ainult tellimuspõhisele (subscription-only) mudelile. See tähendab, et organisatsioonid ei saa enam osta vSphere'i või vSANi ühekordse tasu eest, vaid peavad sõlmima ajaliselt piiratud tellimusi, tavaliselt 1, 3 või 5 aastaks. Pikemad tellimusperioodid võivad pakkuda soodsamaid tingimusi. See muudatus transformeerib VMware'i kulud peamiselt kapitalimahukast (CapEx) mudelist tegevuskuludele (OpEx) keskenduvaks.

Teine märkimisväärne muudatus on üleminek tuumapõhisele (per-core) litsentsimisele, asendades varasema protsessoripõhise (per-socket) mudeli. Nüüd tuleb litsentseerida serveri kõik füüsilised tuumad, kusjuures kehtib miinimummäära nõue – näiteks loetakse üks protsessor vähemalt 16-tuumaliseks, isegi kui sellel on tegelikult vähem tuumasid. See mõjutab eriti väiksemaid ja keskmise suurusega ettevõtteid (VKE), kes võivad olla sunnitud maksma kasutamata tuumade eest. Näiteks vSphere Standardi soovituslik hind (MSRP) on $50 tuuma kohta (3-aastase lepingu aastane väärtus, ACV) ja see sisaldab vCenter Standardit. vSphere Essentials Plus Kit maksab $35 tuuma kohta (3-aastane ACV), kuid seda müüakse ainult 96-tuumalise komplektina, mis on mõeldud maksimaalselt kolmele hostile ja sisaldab vCenter Essentialsi.

Lisaks on Broadcom lihtsustanud VMware'i tooteportfelli, koondades tooted suurematesse pakettidesse nagu VMware Cloud Foundation (VCF) ja vSphere Foundation (VVF). Kuigi see võib tunduda lihtsustamisena, võib see sundida kliente maksma komponentide eest, mida nad tegelikult ei vaja. Näiteks vSphere Foundation (VVF) maksab $135 tuuma kohta (3-aastane ACV) ja sisaldab vSphere Enterprise Plus, vCenter Standard, Tanzu Kubernetes Grid ja Aria Suite Standard.

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
   
    *\*Märkus: Hinnad on illustratiivsed. Kasutatav maht sõltub RAID tasemest. Allikad: ``.* Jagatud salvestusruum on VMware funktsioonide, nagu vMotion ja HA, jaoks kriitilise tähtsusega. See tabel aitab eelarvestada seda sageli märkimisväärset kulu.

* **Võrguseadmed (Switchid, Ruuterid, Tulemüürid VKE serveriruumi jaoks)**: Switchid, ruuterid ja tulemüürid võivad maksta $5000 kuni $50 000 sõltuvalt jõudlusest/turvalisusest. Serveripõhiselt võib võrguseadmete (switch, ruuter, tulemüür) kulu olla $250-$500 serveri kohta. VKE ruuter: $80-$350 või $150-$500. Hallatav switch: $250-$450 või $50-$500+. Riistvaraline tulemüür: $400-$2300 või $200-$1000+. Täiustatud tulemüürid võivad maksta $5000–$20 000 seadme kohta.

    **Tabel 3: Tüüpilised Kulud VKE Võrguinfrastruktuuri Komponentidele**

    | Komponent                   | Spetsifikatsiooni Märkused (nt PoE, Läbilaskevõime) | Hinnanguline Hinnavahemik (€) |
    | :-------------------------- | :------------------------------------------------- | :-------------------------- |
    | Hallatav GbE Switch (24-porti) | L2/L3 funktsioonid, VLAN tugi                      | 250 - 700                   |
    | Hallatav GbE PoE+ Switch (24-porti) | Sama, mis eelmine + PoE+                           | 400 - 1000                  |
    | VKE Tulemüüri Seade         | 500 Mbps - 1 Gbps läbilaskevõime, VPN tugi         | 300 - 1500                  |
    | VKE Ruuter                  | Gigabit WAN/LAN, VPN                               | 100 - 400                   |
   
    *\*Märkus: Hinnad on illustratiivsed. Allikad: ``.* Võrgundus on fundamentaalne, kuid sageli TCO baasarvutustes tähelepanuta jäetud. See tabel toob esile usaldusväärse ja turvalise kohapealse keskkonna jaoks vajalike äriklassi seadmete kulud.

* **Katkematu Toite Allikas (UPS)**: Proportsionaalne kulu on tavaliselt 25-50% serveri maksumusest. Redundantsed toitesüsteemid varundamiseks võivad maksta $1500 - $3000. UPSi enda energiakulu võib olla $100-$200 kuus. UPSi kulud ei piirdu ainult riistvaraga, vaid hõlmavad ka pidevat akude vahetust (tavaliselt iga 3-5 aasta tagant) ja nende tarbitavat elektrit. Need tegurid lisanduvad TCO-le ja jäävad sageli tähelepanuta.

* **Esialgsed Füüsilise Turvalisuse Meetmed (Juurdepääsukontroll, baasvalve serveriruumile)**: Biomeetrilised juurdepääsukontrollid: $10 000–$50 000 seadistuse kohta. Klahvistikuga juurdepääsukontroll ukse kohta: $1000–$2500; võtmekaardi/kiibi süsteemid: $1500–$3500 ukse kohta. Baas-CCTV paigaldus: $100-$2500+; seirekulu: $30-$150+ kaamera kohta kuus. Füüsiline turvalisus on kohapealse lahenduse baaskulu. Isegi serveriruumi ukse baasjuurdepääs klahvistiku/kaardiga ja paar kaamerat kujutavad endast arvestatavat kapitalikulu ja potentsiaalset jooksvat tegevuskulu.

* **Riistvara Amortisatsioon**: Tavapärane 3-5 aastane kasutusiga/amortisatsiooniperiood serveritele, salvestusruumile ja võrguseadmetele TCO arvutustes. See teisendab kapitalikulud aastaseks kuluks võrdluse tegemiseks. Riistvara ei kesta igavesti; selle maksumus tuleb TCO täpseks arvutamiseks jaotada selle kasulikule elueale. 3-5 aastane tsükkel on tavaline, mille järel jõudlus, töökindlus ja garantiikate vähenevad, käivitades sageli uuendustsükli.

#### Tarkvara Litsentsimine (Esialgsed ja Jooksvad Tellimuskulud)

* **VMware vSphere (vSphere Standard, Essentials Plus Kit; tuumapõhine, miinimumid, vCenter)**:
    vSphere Standard: $50/tuum MSRP (3-aastane ACV), sisaldab vCenter Standardit. Minimaalselt 16 tuuma protsessori kohta.
    vSphere Essentials Plus Kit: $35/tuum MSRP (3-aastane ACV), müüakse 96-tuumalise komplektina (max 3 hosti), sisaldab vCenter Essentialsi.
    Näidisstsenaariumid allikast 2:
    (2) Hosti, kokku 16 tuuma hosti kohta (või vähem), Essentials Plus: $10 080 (3-aastane periood, 96-tuumaline komplekt).
    (1) Host, kokku 16 tuuma (või vähem), vSphere Standard: $2400 (3-aastane periood).
    (2) Hosti, kokku 16 tuuma hosti kohta (või vähem), vSphere Standard: $4800 (3-aastane periood).
    vSphere Foundation (VVF) maksab $135/tuum (3-aastane ACV) ja sisaldab vSphere Enterprise Plus, vCenter Standard, Tanzu Kubernetes Grid, Aria Suite Standard.

    **Tabel 4: VMware vSphere Tellimuskulude Stsenaariumid**

    | Stsenaarium (nt 2 Hosti, 1 Protsessor/Host, 12 Tuuma/Protsessor) | Valitud vSphere Pakett (Standard/Essentials Plus) | Tegelikud Tuumad | Litsentseeritud Tuumad (miinimumide tõttu) | Tuuma MSRP (Aastane, €) | Aastane Kogukulu (€) | 3 Aasta Kogukulu (€) |
    | :--------------------------------------------------------------- | :----------------------------------------------- | :-------------- | :----------------------------------------- | :---------------------- | :------------------- | :------------------- |
    | "1 Host, 1 CPU, 8 tuuma"                                         | vSphere Standard                                 | 8               | 16                                         | ~46                     | ~736                 | ~2208                |
    | "2 Hosti, 1 CPU/Host, 12 tuuma/CPU"                              | vSphere Standard                                 | 24              | 32 (2x16)                                  | ~46                     | ~1472                | ~4416                |
    | "2 Hosti, 1 CPU/Host, 12 tuuma/CPU"                              | vSphere Essentials Plus                          | 24              | 96 (kit)                                   | ~32                     | ~3072                | ~9216                |
    | "3 Hosti, 2 CPU/Host, 16 tuuma/CPU"                              | vSphere Foundation                               | 96              | 96                                         | ~124                    | ~11904               | ~35712               |
   
    *\*Märkus: Hinnad on MSRP-põhised ja teisendatud eurodesse ligikaudse vahetuskursiga. Tegelikud hinnad võivad erineda. Allikad: ``.* See tabel illustreerib uue litsentsimismudeli rahalist mõju tüüpilistele VKE konfiguratsioonidele, muutes abstraktse "tuumapõhise" hinnakujunduse konkreetseks.

* **VMware Site Recovery Manager (SRM) / Live Site Recovery**: SRM oli varem litsentseeritud kaitstud virtuaalmasina (VM) kohta (Standard kuni 75 VM-i saidi kohta, Enterprise ilma piiranguta). Nüüd pakutakse VMware Live Recovery't (mis sisaldab Live Site Recovery't, SRM-i edasiarendust) "Kaitstud VM-i tellimuste" ja "Kaitstud mahu tellimustega". VMware Live Site Recovery kasutamiseks on vaja ainult "Kaitstud VM-i tellimusi". Kaitstud VM-ide soovituslik hind (3-aastane tellimus ja pikem, aastahind): $400 VM-i kohta aastas (USD). See sisaldab tootmistuge ja arveldatakse ettemaksuna. Live Site Recovery jaoks ei ole VM-ide miinimumostu nõuet. Olemasolevad SRM-litsentsid konverteeritakse aktiveerimisel Live Site Recovery litsentsideks. See on lisandmoodul, mitte automaatselt VCF/VVF baaspakettide osa. Avariitaaste on kriitiline, kuid sageli eraldi hinnastatud komponent. Üleminek VMware Live Recovery tellimusele VM-i kohta lisab veel ühe korduva kulukihi.

* **Serveri Operatsioonisüsteemid (nt Windows Server Standard/Datacenter - tuumapõhine, CALid)**: Windows Server 2022 Standard (16-tuumaline pakett): ~$527. Microsofti poolt on 16-tuumaline pakett + 10 CAL-i hinnaga $1680. Windows Server 2022 Datacenter (16-tuumaline OEM): ~$4700 - $5063. Mõlemad nõuavad tuumapõhist litsentsimist (minimaalselt 16 tuuma serveri kohta, 8 tuuma protsessori kohta) ja CAL-e (kasutaja- või seadmepõhised). Standard CAL-id: ~$39/CAL; 5 kasutaja CAL-i ~$190.

    **Tabel 5: Windows Serveri Litsentsimiskulude Näited**

    | Serveri OS Väljaanne (Standard/Datacenter) | Litsentseeritavate Tuumade Arv (nt 16, 32) | Hinnanguline Tuumalitsentsi Kulu (€) | Kasutaja/Seadme CALide Arv | Hinnanguline CALide Kulu (€) | Hinnanguline OS Litsentsimise Kogukulu (€) |
    | :------------------------------------------ | :----------------------------------------- | :----------------------------------- | :------------------------- | :--------------------------- | :----------------------------------------- |
    | Windows Server 2022 Standard              | 16                                         | ~500 - 700                           | 10 Kasutajat               | ~350                         | ~850 - 1050                                |
    | Windows Server 2022 Standard              | 32                                         | ~1000 - 1400                         | 25 Kasutajat               | ~875                         | ~1875 - 2275                               |
    | Windows Server 2022 Datacenter            | 16                                         | ~4200 - 4700                         | 10 Kasutajat               | ~350                         | ~4550 - 5050                               |
    | Windows Server 2022 Datacenter            | 32                                         | ~8400 - 9400                         | 25 Kasutajat               | ~875                         | ~9275 - 10275                              |
   
    *\*Märkus: Hinnad on illustratiivsed ja põhinevad jaemüügihindadel. CALide vajadus sõltub keskkonnast. Allikad: ``.* Serveri OS-i litsentsid on märkimisväärne tarkvarakulu. See tabel aitab mõista tuumalitsentside ja CALide kahekordset kulustruktuuri.

* **Varundus- ja Replikatsioonitarkvara (nt Veeam, Commvault - kui ei ole mujal komplekteeritud/kaetud)**: Veeam Data Platform Advanced Universal License (10 eksemplari pakett, 1-aastane uuendus): Hinnapakkumised varieeruvad suuresti, nt $1710 10 eksemplari paketi kohta või kõikuvad pakkumised $9000 kuni $22 000 110 eksemplari kohta. Veeam Universal License (VUL) on 5-10 litsentsiga kimpudes; 1 VM = 1 litsents. Commvault Backup & Recovery for VMs (tellimus, 10 VM-i, 3 aastat): $4259.99. Tüüpilised RPO/RTO 2. taseme rakendustele: RTO 4 tundi, RPO 2 tundi. 3. tase: RTO 8-24 tundi, RPO 4 tundi. Need eesmärgid mõjutavad varundusstrateegiat ja tarkvara valikut. Varundustarkvara on hädavajalik ja selle maksumus võib olla märkimisväärne.

### B. Tegevuskulud (OpEx)

Tegevuskulud on jooksvad kulud, mis on seotud infrastruktuuri haldamise ja hooldamisega.

#### Andmekeskuse / Serveriruumi Kulud

* **Ruum (Kolokatsioonikulud 1/3 kuni täisräkile kui proksi)**: Hetzneri 1/3 räkk (14U): €100/kuus + seadistustasu. Täisräkk (42U+): €167-€251/kuus + seadistustasu. Atlexi 42U räkk Euroopas: alates €569/kuus. Andmekeskuse ettevalmistus võib maksta $200-$1000 ruutjala kohta. Isegi kui kolokatsiooni ei kasutata, annavad need arvud aimu serveriruumi füüsilise ruumi, turvalisuse ja keskkonnatingimuste väärtusest.

* **Energiatarve (Serverid, Salvestusruum, Võrguseadmed, Jahutus – PUE mõju)**: Elektrihind Eestis: Väga muutlik, Nord Pooli hetkehind + marginaal (nt 0.0045 €/kWh + kuutasu) + jaotustariif (~0.01-0.017 €/kWh + püsitasu) + taastuvenergiatasu (0.0084 €/kWh 2025. aastal) + aktsiis (0.0021 €/kWh) + käibemaks (22%). Lihtsuse huvides võib tuletada keskmise koguhinna, nt ~0.15-0.20 €/kWh ettevõtetele. Näide serveri energiatarbimisest: 200W pidevalt tarbiv server maksab ~$300 aastas hinnaga $0.168/kWh. IT-seadmed moodustavad 50-60% andmekeskuse energiatarbimisest; jahutus 35-45%. Energiatõhususe näitaja (Power Usage Effectiveness, PUE): Keskmine PUE on ~1.8; tõhusad andmekeskused sihivad <1.2. Google'i PUE on ~1.08-1.11. VKE serveriruumide PUE on tõenäoliselt kõrgem (vähem optimeeritud), võib-olla 1.6-2.0. Energiakulud ja jahutus võivad moodustada 5-10% esialgsest riistvarakulust aastas. Väikeste andmekeskuste puhul võib see ületada $10 000 aastas.

    **Tabel 6: Hinnangulised Aastased Energiakulud Näidiskonfiguratsioonidele**

    | Konfiguratsioon (nt Väike/Keskmine/Suur VKE Serveriseadistus CapEx-ist) | Hinnanguline Riistvara Koguvõimsus (W) | Eeldatav PUE | Energiatarve Kokku (kWh/aastas) | Segatud Elektrihind (€/kWh - Eesti näide) | Hinnanguline Aastane Energiakulu (€) |
    | :--------------------------------------------------------------------- | :------------------------------------ | :----------- | :----------------------------- | :--------------------------------------- | :---------------------------------- |
    | "Väike VKE (1-2 serverit, NAS, baasvõrk)"                              | 500 - 800                             | 1.8          | 7884 - 12614                   | 0.18                                     | 1419 - 2271                         |
    | "Keskmine VKE (3-4 serverit, SAN, võrk)"                               | 1500 - 2500                           | 1.6          | 21024 - 35040                  | 0.18                                     | 3784 - 6307                         |
   
    *\*Märkus: Hinnad on illustratiivsed. PUE väärtus on hinnanguline VKE keskkonnale. Allikad: ``.* Energia on suur korduvkulu. See tabel näitab, kuidas riistvaravalikud, operatiivne tõhusus (PUE) ja kohalikud elektrihinnad kombineeruvad märkimisväärseks kuluks.

* **Jahutus (osana energiakulust või eraldi, kui andmed võimaldavad)**: Tavaliselt arvestatakse PUE arvutustes. Kui jahutussüsteemide spetsiifilised kulud on teada (nt serveriruumi eraldi kliimaseade), võib need eraldi välja tuua. Jahutus võib maksta $200-$400 kuus kohapealsete varundussüsteemide puhul.

#### IT Administratsioon ja Haldus

* **Personalikulud (VMware Süsteemiadministraatori palk - Eesti keskmine)**: Keskmine süsteemiadministraatori netokuupalk Eestis: €1129 - €3037. Brutopalga saamiseks tuleb arvestada Eesti tulumaksu, sotsiaalmaksu, töötuskindlustust. Ligikaudne brutopalk: €1800 - €5000 kuus ehk €21 600 - €60 000 aastas. VMware spetsialisti puhul tõenäoliselt selle vahemiku keskpaigast ülespoole. IT-töötajate palgad võivad moodustada 30-50% esialgsest riistvarakulust aastas.

* **Ajaeraldus (Hinnanguline % administraatori ajast VMware haldamiseks VKEdes)**: Uuring näitab, et 30% VMware administraatori ajast kulub hooldusele; keskmiselt 12.3 tundi nädalas VMware lahenduste haldamisele ja paikamisele. See uuring ei ole spetsiifiline VKEdele, kuid annab üldise ettekujutuse. Teine allikas viitab, et virtualiseerimiseelne administraatori/serveri suhe on 1:10 kuni 1:30, virtualiseerimisjärgne võib olla palju kõrgem (nt 1:200), kuid varieerub suuresti. VKEdes võib üks administraator hallata kogu infrastruktuuri, seega on tema koguajast VMware'ile kuluv % asjakohasem. Administraatori aja kvantifitseerimine on ülioluline. Isegi kui VKE-l ei ole pühendunud VMware administraatorit, kulub osa üldise süsteemiadministraatori ajast VMware ülesannetele. Sellel "ajamaksul" on reaalne kulu.

#### Hooldus ja Tugi

* **Riistvara Hoolduslepingud (Garantiijärgsed, kui kohaldatav)**: Tavaliselt 5-10% või 10-15% esialgsest riistvarakulust aastas. $500k investeeringu puhul $50k-$75k aastas.
* **Tarkvara Tellimuste Uuendamine/Tugi (Pärast esialgset perioodi/kaasatud tuge)**: VMware tellimused sisaldavad nüüd tuge perioodi vältel. Muu tarkvara (OS, varundus) puhul lisanduvad jooksvad toe/tellimuse tasud.

#### Varundus- ja Avariitaasteoperatsioonid

* **Meedia, Väljaspool Asukohta Varundamine, Avariitaaste Testimine**: Varunduslintide/ketaste kulud, turvalised väljaspool asukohta varundusteenused ning ribalaiuse/personali ajakulu regulaarseks avariitaaste testimiseks. Pilvevarundus 10TB jaoks: $2760–$3600 aastas.
* **Andmete Väljavool (pilvepõhise avariitaaste või varunduskomponentide puhul)**: Kui avariitaasteks/varunduseks kasutatakse pilve, kohalduvad väljavoolutasud. AWS/Azure/GCP: esimene 100GB/kuus sageli tasuta, seejärel ~$0.08-$0.09/GB esialgsetele tasemetele, suuremate mahtude puhul madalamad. See on kriitiline taastestsenaariumide puhul. Pilvekomponendid avariitaaste strateegias võivad taastejuhtumi ajal kaasa tuua muutuvaid, potentsiaalselt kõrgeid väljavoolukulusid, mida tuleb TCO-s arvesse võtta.

#### Ühenduvus

* **Interneti Ribalaius**: Äriinternetiühenduse maksumus, maht sõltub replikatsioonivajadustest, kasutajate juurdepääsust jne.
* **VPN Kulud (kui kohaldatav kaughalduseks/avariitaasteks)**:
    AWS Site-to-Site VPN: ~$0.05/tund ühenduse kohta + andmeedastus.
    Azure VPN Gateway (VpnGw1): ~$139/kuus + andmeedastus.
    Google Cloud VPN: ~$0.065/tund tunneli kohta + IPsec liiklus.
    Paljude "väikeste" tegevuskulude (nagu VPNid, spetsiifilise tarkvara tugi, meedia) summa võib koguneda märkimisväärseks korduvaks kuluks, mis sageli jääb kõrgetasemelistes TCO vaadetes tähelepanuta.

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

Kohapealse lahenduse TCO arvutatakse jaotises 5 esitatud täiustatud mudeli alusel, kasutades Eesti turule vastavaid hinnanguid (nt elektrihind, tööjõukulud). Pilveteenuste pakkujate (AWS, Azure, GCP) samaväärsete ressursside hinnastamisel keskendutakse Euroopa regioonidele (nt Frankfurt, Iirimaa, Madalmaad).

* **Amazon Web Services (AWS)**:
    * Arvutusvõimsus (EC2): m5/m6i/m6a seeria üldotstarbelisteks ja r5/r6i seeria mäluoptimeeritud instantsideks. Näiteks m5.2xlarge (8 vCPU, 32GB RAM) Iirimaal maksab ~$0.4280/tund (~$312/kuus). r5.2xlarge (8 vCPU, 64GB RAM) Iirimaal maksab ~$0.5640/tund (~$412/kuus).
    * Salvestusruum (EBS, S3): EBS gp3 SSD-d (nt Iirimaal: salvestusruum ~$0.088/GB-kuus; IOPS ~$0.0055/lisatud IOPS-kuus üle 3000; läbilaskevõime ~$0.044/lisatud MB/s-kuus üle 125). S3 Standard (Iirimaa, esimesed 50TB): ~$0.023/GB-kuus.
    * Võrk: Application Load Balancer ~$0.0225/tund + $0.008/LCU-tund. Site-to-Site VPN ~$0.05/tund + andmeedastus.
    * Tugi: Business Support miinimum $100/kuus või astmeline % kuludest.
    * Andmete väljavool: Esimene 100GB/kuus tasuta, seejärel ~$0.09/GB.

* **Microsoft Azure**:
    * Arvutusvõimsus (VMs): Dsv5/Dasv5 seeria üldotstarbelisteks ja Esv5/Easv5 seeria mäluoptimeeritud Linuxi VM-ideks. Näiteks D4s v3 (Linux, 4 vCPU, 16GB RAM) Põhja-Euroopas maksab tellimuslepingu alusel ~$175/kuus. E-seeria (nt E4s v3, 4 vCPU, 32GB RAM) alghinnad väikseimatele instantsidele alates ~$58.40/kuus.
    * Salvestusruum (Managed Disks, Blob Storage): Premium SSD P10 (128GB) Lääne-Euroopas ~$19/kuus. Standard SSD E10 (128GB) Lääne-Euroopas ~$9/kuus. Blob Storage Hot LRS (Lääne-Euroopa, esimesed 50TB) ~$0.0184/GB-kuus.
    * Võrk: Standard Load Balancer ~$0.025/tund + $0.005/GB andmetöötlus. VPN Gateway VpnGw1 ~$138.70/kuus + andmeedastus.
    * Tugi: Standard Support $100/kuus.
    * Andmete väljavool: Esimene 100GB/kuus tasuta, seejärel ~$0.087/GB.

* **Google Cloud Platform (GCP)**:
    * Arvutusvõimsus (Compute Engine): E2-standard, N2-standard, N2D-standard üldotstarbelisteks; M1, M2, M3 mäluoptimeeritud. Näiteks e2-standard-2 (2 vCPU, 8GB RAM) europe-west1 ~$0.074/tund (~$54/kuus).
    * Salvestusruum (Persistent Disks, Cloud Storage): Persistent Disk SSD (europe-west1) ~$0.170/GB-kuus. Cloud Storage Standard (europe-west1, mitme regiooni) ~$0.023/GB-kuus.
    * Võrk: HTTP(S) Load Balancer (keerukas, vt GCP hinnakirja). HA VPN ~$0.065/tund tunneli kohta + liiklus.
    * Tugi: Standard Support miinimum $29/kuus või 3% kuutasudest.
    * Andmete väljavool: Esimene 100GB/kuus tasuta (teatud sihtkohtadesse), seejärel ~$0.08-$0.12/GB.

**Tabel 7: 3 Aasta TCO Ülevaade – Kohapealne vs. Tüüpiline Pilve Stsenaarium (Illustratiivne)**

| Kulukategooria                      | Kohapealne Aasta 1 (€) | Kohapealne Aasta 2 (€) | Kohapealne Aasta 3 (€) | Kohapealne 3-a TCO (€) | Pilv X Aasta 1 (€) | Pilv X Aasta 2 (€) | Pilv X Aasta 3 (€) | Pilv X 3-a TCO (€) |
| :---------------------------------- | :--------------------- | :--------------------- | :--------------------- | :--------------------- | :----------------- | :----------------- | :----------------- | :----------------- |
| Riistvara CapEx (Amorditud)         | 10 000                 | 10 000                 | 10 000                 | 30 000                 | 0                  | 0                  | 0                  | 0                  |
| Tarkvara Tellimus                   | 8 000                  | 8 000                  | 8 000                  | 24 000                 | 12 000             | 12 000             | 12 000             | 36 000             |
| Energia/Ruum                        | 3 000                  | 3 150                  | 3 300                  | 9 450                  | Sisaldub teenuses  | Sisaldub teenuses  | Sisaldub teenuses  | Sisaldub teenuses  |
| Administraatori Tööjõud             | 15 000                 | 15 750                 | 16 500                 | 47 250                 | 5 000              | 5 250              | 5 500              | 15 750             |
| Hooldus/Tugi (Riistvara, muu tarkvara) | 2 500                  | 2 500                  | 2 500                  | 7 500                  | Sisaldub teenuses  | Sisaldub teenuses  | Sisaldub teenuses  | Sisaldub teenuses  |
| Muud Tegevuskulud (Varundus, DR, ühenduvus) | 2 000                  | 2 000                  | 2 000                  | 6 000                  | 3 000              | 3 000              | 3 000              | 9 000              |
| **KOKKU** | **40 500** | **41 400** | **42 300** | **124 200** | **20 000** | **20 250** | **20 500** | **60 750** |

*Märkus: See tabel on väga illustratiivne ja põhineb hüpoteetilistel väärtustel, et näidata kulustruktuuri erinevusi.* *"Pilv X" esindab tüüpilist avaliku pilve pakkujat. Administraatori tööjõukulu pilves on väiksem, eeldades vähem infrastruktuuri haldamist.* Pilve TCO võib tunduda esialgu madalam tänu puuduvale riistvara esialgsele investeeringule, kuid andmete väljavoolutasud, kõrgema taseme tugiteenused ja premium-klassi salvestusruumi/instantside tüübid võivad kulusid aja jooksul suurendada. Kohapealsel lahendusel on kõrge esialgne kapitalikulu, kuid potentsiaalselt madalamad ja prognoositavamad tegevuskulud, kui seda hästi hallatakse ja riistvara kasutatakse kogu selle eluea jooksul. "Tasuvuspunkt" varieerub oluliselt.

### Madalama Hinnaklassi Pilve-/Virtualiseerimisteenuse Pakkujate Kaalumine

On oluline märkida, et turul on ka teenusepakkujaid nagu Hetzner ja OVHcloud, kes pakuvad virtuaalmasinaid ja pühendatud servereid märkimisväärselt madalamate hindadega võrreldes suurte hüperkvaliteetsete pilveteenuste pakkujatega. Näiteks Hetzner CX22 (2 vCPU, 4GB RAM, 40GB NVMe) maksab ligikaudu €3.79 kuus. OVHcloud Value VPS (1 vCore, 2-4GB RAM, 40-80GB NVMe) maksab alates ~$5.95 kuus.

Kuigi need valikud võivad tunduda odavamad, kaasnevad nendega sageli vähem hallatud teenuseid, erinevad tugistruktuurid ja need võivad nõuda rohkem ettevõttesisest ekspertiisi haldamiseks ja turvamiseks. See võib potentsiaalselt nihutada osa "pilve" tegevuskuludest tagasi sisemistele tööjõukuludele. Seega, kuigi "odavamad" pilvevalikud eksisteerivad, vajab ka nende TCO hoolikat hindamist. Madalam esialgne hind võib kompenseeruda suurema halduskoormuse või vähemate integreeritud ettevõtlusfunktsioonidega (nt täiustatud turvalisus, DRaaS, põhjalikud haldusportaalid) võrreldes hüperkvaliteetsete pakkujate või hästi struktureeritud VMware kohapealse keskkonnaga.

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
| VMware vSphere Litsentsid                     | tuum                           | 48             | 46 (Standard, aastane) | 2208             | Nt 3 hosti, 2 CPU/host, 8 tuuma/CPU = 48 tuuma. vSphere Standard @ ~$46/tuum/aasta (3a leping). Vt Tabel 4.                 |
| Serveri OS Litsentsid (nt Windows Server)     | server/CAL                     | 3 serverit, 50 CAL | 600/server, 35/CAL     | 1183             | Nt 3x€600 (16 tuuma Standard litsents, amordituna 3a) + 50x€35 (CALid, amordituna 3a). Vt Tabel 5.                      |
| Varundustarkvara Litsents                     | VM                             | 15             | 50 (aastane)           | 750              | Nt 15 VM-i @ ~$50/VM/aastas.                                                                                              |
| **KAPITALIKULUD KOKKU (AASTANE)** |                                |                |                        | **12541** |                                                                                                                           |
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
| **KOGUKULU (TCO) AASTAS** |                                |                |                        | **32045** |                                                                                                                           |

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

## Works cited

1.  CIO Playbook: Broadcom's VMware 2024 Licensing Changes - Redress Compliance, accessed June 4, 2025, https://redresscompliance.com/cio-playbook-broadcoms-vmware-2024-licensing-changes/
2.  Decoding the new Broadcom VMware vSphere Licensing Packages (for Small Deployments) | Veeam Community Resource Hub, accessed June 4, 2025, https://community.veeam.com/blogs-and-podcasts-57/decoding-the-new-broadcom-vmware-vsphere-licensing-packages-for-small-deployments-6398
3.  How much does on premise hardware cost? - Interwest Communications, accessed June 4, 2025, https://www.interwestcommunications.com/blog/how-much-does-on-premise-hardware-cost/
4.  Server Cost Guide: Building and Renting | phoenixNAP KB, accessed June 4, 2025, https://phoenixnap.com/kb/server-cost
5.  Server Room Power Consumption: Demand and Efficiency - ServerWatch, accessed June 4, 2025, https://www.serverwatch.com/servers/server-room-power-consumption/
6.  How to Drastically Reduce your Electricity Costs: Print Server vs. ezeep Hub, accessed June 4, 2025, https://www.ezeep.com/electricity-costs-print-server-vs-ezeep-hub/
7.  Cost of Server Comparison: On-Premises vs Cloud - Parallels, accessed June 4, 2025, https://www.parallels.com/blogs/ras/cost-of-server/
8.  Salary Estonia, Systems Administrator, Information... - Palgad.ee, accessed June 4, 2025, https://www.palgad.ee/en/salaryinfo/information-technology/systems-administrator
9.  VMware Private Cloud vs On-Prem VMware: A Complete Cost and ..., accessed June 4, 2025, https://www.theitvortex.com/vmware-private-cloud-vs-on-prem-vmware-a-complete-cost-and-performance-analysis/
10. CALCULATING THE TCO: CLOUD VS ON PREMISE ... - Criticalcase, accessed June 4, 2025, https://www.criticalcase.com/blog/calculating-the-tco-cloud-vs-on-premise-infrastructure.html
11. How to Build a Server with your Budget in 2025 - ServerMania, accessed June 4, 2025, https://www.servermania.com/kb/articles/how-to-build-a-server-with-costs-considered
12. Affordable SAN for SMB - sysadmin - Reddit, accessed June 4, 2025, https://www.reddit.com/r/sysadmin/comments/evf5zt/affordable_san_for_smb/
13. iSCSI SAN / NAS StorageWindows Storage Server and Linux-based Storage - Broadberry Data Systems, accessed June 4, 2025, https://www.broadberry.com/iscsi-san-nas-storage-servers
14. How Much Does a NAS Cost in 2025?, accessed June 4, 2025, https://nas.ugreen.com/blogs/buying-guide/how-much-does-a-nas-cost-in-2025
15. The Best NAS (Network Attached Storage) Devices for 2025 - PCMag, accessed June 4, 2025, https://www.pcmag.com/picks/the-best-nas-network-attached-storage-devices
16. VMware vSphere Pricing in 2024: Licensing and Overhead Costs - V2 Cloud, accessed June 4, 2025, https://v2cloud.com/blog/vmware-vsphere-licensing-and-costs
17. On-Premise Cost Calculator - Transact Campus, accessed June 4, 2025, https://transactcampus.com/sales/on-premise-calculator
18. How Much Does It Cost To Set Up A Small Business Network - Abacus, accessed June 4, 2025, https://goabacus.com/how-much-does-it-cost-to-set-up-a-small-business-network/
19. How Much Does It Cost To Set Up A Small Business Network, accessed June 4, 2025, https://www.montrealitservices.com/blog/setting-up-an-smb-network/
20. On-Premises vs Cloud Backup: Cost Comparison | News - Essential Designs, accessed June 4, 2025, https://www.essentialdesigns.net/news/on-premises-vs-cloud-backup-cost-comparison
21. Access Control System Cost: Pricing Breakdown & Installation Fees, accessed June 4, 2025, https://www.getkisi.com/blog/breakdown-of-access-control-system-installation-costs
22. How Much Do Remote Video Monitoring Services Cost? - Scout Security, accessed June 4, 2025, https://www.scoutsecurity.com/how-much-do-remote-video-monitoring-services-cost/
23. Total Cost of Ownership (TCO) Model for Storage - SNIA.org, accessed June 4, 2025, https://www.snia.org/sites/default/files/SSSI/SNIA_TCO_Whitepaper_03-2021.pdf
24. Total Cost of Ownership (TCO) - Nutanix, accessed June 4, 2025, https://portal.nutanix.com/page/documents/details?targetId=Nutanix-Beam-User-Guide:bea-vm-nutanix-cg-tco-c.html
25. What Is TCO? Total Cost of Ownership & Hidden Costs - ViewSonic Library, accessed June 4, 2025, https://www.viewsonic.com/library/business/what-is-tco-total-cost-ownership/
26. Vsphere free license ?! | VMware vSphere - Broadcom Community, accessed June 4, 2025, https://community.broadcom.com/vmware-cloud-foundation/communities/community-home/digestviewer/viewthread?GroupId=5107&MessageKey=726bbbb9-8d9b-4ee4-a478-263219560a56&CommunityKey=c24ac095-2065-4261-a4b5-6de9dade8734
27. www.vmware.com, accessed June 4, 2025, https://www.vmware.com/content/dam/digitalmarketing/vmware/en/pdf/products/site-recovery-manager/vmware-site-recovery-manager-datasheet.pdf
28. Purchasing VMware Live Recovery - Broadcom Techdocs, accessed June 4, 2025, https://techdocs.broadcom.com/de/de/vmware-cis/live-recovery/live-recovery/saas/vmware-live-recovery/vmware-live-recovery/set-up-vmware-live-recovery/purchasing-vmware-live-recovery.html
29. Purchasing VMware Live Recovery - Broadcom Tech Docs, accessed June 4, 2025, https://techdocs.broadcom.com/us/en/vmware-cis/live-recovery/live-recovery/saas/vmware-live-recovery/set-up-vmware-live-recovery/purchasing-vmware-live-recovery.html
30. assets.applytosupply.digitalmarketplace.service.gov.uk, accessed June 4, 2025, https://assets.applytosupply.digitalmarketplace.service.gov.uk/g-cloud-14/documents/92355/554756111604737-pricing-document-2024-05-01-1419.pdf
31. VMware Live Site Recovery Licensing - Broadcom Tech Docs, accessed June 4, 2025, https://techdocs.broadcom.com/us/en/vmware-cis/live-recovery/live-site-recovery/9-0/overview/site-recovery-manager-system-requirements/srm-licensing.html
32. VMware Live Site Recovery Licensing - Broadcom Techdocs, accessed June 4, 2025, https://techdocs.broadcom.com/kr/ko/vmware-cis/live-recovery/live-recovery/saas/vmware-live-site-recovery-installation-and-configuration/overview/site-recovery-manager-system-requirements/srm-licensing.html
33. Microsoft Windows Server 2022 Standard 16 Core License | TTT, accessed June 4, 2025, https://www.trustedtechteam.com/products/microsoft-windows-server-2022-standard-16-core
34. Buy Windows Server 2022 Standard Edition - Microsoft Store, accessed June 4, 2025, https://www.microsoft.com/en-us/d/windows-server-2022-standard-cal/dg7gmgf0d6m5
35. Buy Windows Server 2022 Standard Edition - Microsoft Store, accessed June 4, 2025, https://www.microsoft.com/en-us/d/windows-server-2022-standard-cal/dg7gmgf0d6m5?activetab=pivot:overviewtab
36. Microsoft Windows Server 2022 Datacenter 64-bit License (16 Core, OEM, DVD) - Newegg, accessed June 4, 2025, https://www.newegg.com/microsoft-windows-svr-datacntr-2022-64bit-english-1pk-dsp-oei-dvd-16-core/p/N82E16832350880
37. Microsoft Windows Server 2022 Datacenter - license - 16 cores - P71-09389 - CDW, accessed June 4, 2025, https://www.cdw.com/product/microsoft-windows-server-2022-datacenter-license-16-cores/6729586
38. Microsoft Windows Server 2022 User CAL | Client Access Licenses | 5 pack | OEM, accessed June 4, 2025, https://www.amazon.com/Windows-Server-2022-User-CAL/dp/B09DVPF4ZH
39. Microsoft Windows Server 2022 Datacenter - 24 Core - Trusted Tech Team, accessed June 4, 2025, https://www.trustedtechteam.com/products/microsoft-windows-server-2022-datacenter-24-core
40. Windows Server 2025 Licensing & Pricing | Microsoft, accessed June 4, 2025, https://www.microsoft.com/en-us/windows-server/pricing
41. Veeam pricing is WILD : r/sysadmin - Reddit, accessed June 4, 2025, https://www.reddit.com/r/sysadmin/comments/1b9bfv4/veeam_pricing_is_wild/
42. Veeam Universal License (VUL), accessed June 4, 2025, https://www.veeam.com/universal-license.html
43. Backup Software, accessed June 4, 2025, https://www.cdw.ca/category/software/utilities/backup/?w=FP1
44. RPO & RTO Best Practices - Docs - OnePro Cloud, accessed June 4, 2025, https://docs.oneprocloud.com/userguide/presales/hyperbdr-rpo-rto-planning-best-practices.html
45. Colocation data center: Top service by Hetzner, accessed June 4, 2025, https://www.hetzner.com/colocation/
46. 42U rack rental in Europe - ATLEX.Ru, accessed June 4, 2025, https://www.atlex.ru/rack-in-europe-en/
47. Estonia - Electricity prices in Europe, accessed June 4, 2025, https://www.dayaheadmarket.eu/estonia
48. High-Performance Computing Data Center Power Usage Effectiveness - NREL, accessed June 4, 2025, https://www.nrel.gov/computational-science/measuring-efficiency-pue
49. Power usage effectiveness - Google Data Centers, accessed June 4, 2025, https://datacenters.google/efficiency
50. The common 2025 Post: How are people getting free servers? : r/homelab - Reddit, accessed June 4, 2025, https://www.reddit.com/r/homelab/comments/1jdcobu/the_common_2025_post_how_are_people_getting_free/
51. Estonia EE: Electricity Price: HC: Between 5000 & 14999 KwH: incl All Taxes & Levies, accessed June 4, 2025, https://www.ceicdata.com/en/estonia/electricity-price-household-consumers/ee-electricity-price-hc-between-5000--14999-kwh-incl-all-taxes--levies
52. Survey Reveals the True Costs of VMWare Deployments ..., accessed June 4, 2025, https://www.scalecomputing.com/blog/survey-reveals-the-true-costs-of-vmware-deployments-demonstrates-interest-in-vmware-alternatives
53. Server to admin ratios | VMware vSphere - Broadcom Community, accessed June 4, 2025, https://community.broadcom.com/vmware-cloud-foundation/discussion/server-to-admin-ratios
54. Data Egress Cost: How To Take Back Control And Reduce Egress ..., accessed June 4, 2025, https://cast.ai/blog/data-egress-cost-how-to-take-back-control-and-reduce-egress-charges/
55. Cloud Data Egress Fee Tussle Plays Out with $250k AWS Comp -- AWSInsider, accessed June 4, 2025, https://awsinsider.net/articles/2025/05/09/cloud-data-egress-fee-tussle-plays-out-with-$250k-aws-comp.aspx
56. AWS VPN | Pricing | Amazon Web Services (AWS), accessed June 4, 2025, https://aws.amazon.com/vpn/pricing/
57. VPN Gateway Pricing | Microsoft Azure, accessed June 4, 2025, https://azure.microsoft.com/en-us/pricing/details/vpn-gateway/
58. Network Connectivity pricing - Google Cloud, accessed June 4, 2025, https://cloud.google.com/network-connectivity/pricing
59. Pricing | Cloud VPN | Google Cloud, accessed June 4, 2025, https://cloud.google.com/network-connectivity/docs/vpn/pricing
60. m5.2xlarge Pricing and Specs: AWS EC2, accessed June 4, 2025, https://costcalc.cloudoptimo.com/aws-pricing-calculator/ec2/m5.2xlarge
61. r5.2xlarge Pricing and Specs: AWS EC2, accessed June 4, 2025, https://costcalc.cloudoptimo.com/aws-pricing-calculator/ec2/r5.2xlarge
62. Managed Disks pricing - Microsoft Azure, accessed June 4, 2025, https://azure.microsoft.com/en-us/pricing/details/managed-disks/
63. EC2 On-Demand Instance Pricing – Amazon Web Services - AWS, accessed June 4, 2025, https://aws.amazon.com/ec2/pricing/on-demand/
64. Application and Network Traffic Distribution - Elastic Load Balancing ..., accessed June 4, 2025, https://aws.amazon.com/elasticloadbalancing/pricing/
65. Pricing for AWS Support Plans | Starting at $29 Per Month | AWS ..., accessed June 4, 2025, https://aws.amazon.com/premiumsupport/pricing/
66. Azure VM Pricing: What You Don't Know (not yet) - Intercept, accessed June 4, 2025, https://intercept.cloud/en-gb/blogs/azure-vm-pricing
67. Complete Guide to Azure VM: Pricing Models, Types & More - Umbrella, accessed June 4, 2025, https://umbrellacost.com/blog/azure-vm-pricing/
68. Pricing—Load Balancer | Microsoft Azure, accessed June 4, 2025, https://azure.microsoft.com/en-us/pricing/details/load-balancer/
69. Azure Support—Standard | Microsoft Azure, accessed June 4, 2025, https://azure.microsoft.com/en-us/support/plans/standard
70. How Much Is Microsoft Azure Enterprise Support? - US Cloud, accessed June 4, 2025, https://www.uscloud.com/blog/how-much-is-microsoft-azure-enterprise-support/
71. Pricing | Compute Engine: Virtual Machines (VMs) - Google Cloud, accessed June 4, 2025, https://cloud.google.com/compute/all-pricing
72. e2-standard-2 pricing: $48.92 monthly - Economize Cloud, accessed June 4, 2025, https://www.economize.cloud/resources/gcp/pricing/compute-engine/e2-standard-2/
73. Cloud Storage pricing, accessed June 4, 2025, https://cloud.google.com/storage/pricing
74. Disk and image pricing | Google Cloud, accessed June 4, 2025, https://cloud.google.com/compute/disks-image-pricing
75. Standard Support | Google Cloud, accessed June 4, 2025, https://cloud.google.com/support/standard
76. Google Cloud Customer Care, accessed June 4, 2025, https://cloud.google.com/support
77. Hetzner | Review, Pricing & Alternatives - GetDeploying, accessed June 4, 2025, https://getdeploying.com/hetzner
78. Cheap hosted VPS by Hetzner: our cloud hosting services, accessed June 4, 2025, https://www.hetzner.com/cloud
79. VPS Hosting Germany - Fast & Affordable Virtual Private Servers | OVHcloud Worldwide, accessed June 4, 2025, https://www.ovhcloud.com/en/vps/vps-deutschland/
80. OVHcloud | Review, Pricing & Alternatives - GetDeploying, accessed June 4, 2025, https://getdeploying.com/ovh
81. Cloud Provider Pricing Calculator | Canine - An open source alternative to Heroku, accessed June 4, 2025, https://canine.sh/calculator
82. Object Storage - Hetzner, accessed June 4, 2025, https://www.hetzner.com/storage/object-storage/
83. Cloud VPS Hosting | Flexible and High-Performance Solutions | OVHcloud Worldwide, accessed June 4, 2025, https://www.ovhcloud.com/en/vps/vps-cloud/
84. Low-Cost, High-Performance VPS | Starter VPS | OVHcloud Worldwide, accessed June 4, 2025, https://www.ovhcloud.com/en/vps/cheap-vps/
85. VPS – Your virtual private server in the cloud | OVHcloud Worldwide, accessed June 4, 2025, https://www.ovhcloud.com/en/vps/
86. Cheap hosted VPS by Hetzner: our cloud hosting services, accessed June 4, 2025, https://www.hetzner.com/cloud/
87. VMware Licensing in 2025: How Opscompass and House of Brick Manage Risks and Costs, accessed June 4, 2025, https://opscompass.com/vmware-licensing-challenges-2025/
88. Cloud vs. On-Premises: Choosing the Right Path for Your Data | Gart, accessed June 4, 2025, https://gartsolutions.com/cloud-vs-on-premises/
89. 90+ Cloud Computing Statistics: A 2025 Market Snapshot - CloudZero, accessed June 4, 2025, https://www.cloudzero.com/blog/cloud-computing-statistics/
