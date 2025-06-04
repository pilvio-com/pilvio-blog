# Long read: VMware on-premise lahenduse kuluarvutusloogika analüüs ja mudel 

## 1. Kokkuvõte

[cite_start]Käesolev aruanne analüüsib kriitiliselt VMware kohapealsete (on-premise) lahenduste tüüpilisi kuluarvutusloogikaid, mida sageli kasutatakse turundusmaterjalide kalkulaatorites, ning esitab täiustatud ja põhjalikuma mudeli kogukulu (TCO) hindamiseks.  [cite_start]VMware kohapealsete lahenduste TCO täpne hindamine on keerukas ülesanne, mis hõlmab arvukalt otseseid ja kaudseid kulusid, mida lihtsustatud kalkulaatorid sageli eiravad.  [cite_start]Olukorda komplitseerib veelgi VMware'i hiljutine litsentsimisstrateegia muutus Broadcomi omanduses, mis on fundamentaalselt muutnud tarkvarakulude struktuuri.  [cite_start]Turunduskalkulaatorite loogika keskendub sageli esialgsetele, nähtavatele kuludele, nagu baasriistvara ja tarkvara esialgsed litsentsitasud, jättes tähelepanuta märkimisväärsed jooksvad tegevuskulud (OpEx).  [cite_start]Nende hulka kuuluvad energiatarve, jahutus, IT-administratsioon, tarkvara elutsükli kogukulud ja füüsilise infrastruktuuri üldkulud.  [cite_start]Selline lähenemine viib kogukulu olulise alahindamiseni.  [cite_start]Käesolev aruanne pakub nende puuduste kriitilist analüüsi ja esitleb mitmetahulist TCO mudelit.  [cite_start]See mudel on loodud selleks, et anda organisatsioonidele realistlikum ja usaldusväärsem raamistik VMware kohapealsete investeeringute hindamiseks.  [cite_start]Turunduskalkulaatorite peamine probleem seisneb nende loomupärases kalduvuses esitleda madalamat esialgset hinda klientide ligimeelitamiseks, mis harva peegeldab pikaajalist rahalist kohustust.  [cite_start]See aruanne püüab seda lahknevust parandada, pakkudes läbipaistvat ja kõikehõlmavat lähenemist kulude hindamisele. 

## 2. Sissejuhatus: VMware Kohapealse Lahenduse Kulude Tegelikkus

[cite_start]VMware kohapealsete lahenduste kogukulu (Total Cost of Ownership, TCO) täpne hindamine on kriitilise tähtsusega strateegiliste IT-investeeringute tegemisel.  [cite_start]Lihtsustatud lähenemised, mida sageli kasutatakse turundusmaterjalides, ei suuda tabada kõiki asjakohaseid kuluaspekte, viies potentsiaalselt ekslike otsusteni.  [cite_start]Olukorda on viimastel aastatel oluliselt muutnud Broadcomi poolt VMware'i omandamine ja sellele järgnenud litsentsimismudelite reform. 

### Broadcomi Litsentsimuudatuste Mõju VMware TCO-le

[cite_start]Broadcomi strateegia on toonud kaasa fundamentaalseid muutusi VMware'i toodete, nagu vSphere ja vSAN, litsentsimises.  [cite_start]Kõige olulisem neist on loobumine püsivatest (perpetual) litsentsidest uute müükide puhul ning üleminek ainult tellimuspõhisele (subscription-only) mudelile.  [cite_start]See tähendab, et organisatsioonid ei saa enam osta vSphere'i või vSANi ühekordse tasu eest, vaid peavad sõlmima ajaliselt piiratud tellimusi, tavaliselt 1, 3 või 5 aastaks.  [cite_start]Pikemad tellimusperioodid võivad pakkuda soodsamaid tingimusi.  [cite_start]See muudatus transformeerib VMware'i kulud peamiselt kapitalimahukast (CapEx) mudelist tegevuskuludele (OpEx) keskenduvaks. 

Teine märkimisväärne muudatus on üleminek tuumapõhisele (per-core) litsentsimisele, asendades varasema protsessoripõhise (per-socket) mudeli. [cite_start]Nüüd tuleb litsentseerida serveri kõik füüsilised tuumad, kusjuures kehtib miinimummäära nõue – näiteks loetakse üks protsessor vähemalt 16-tuumaliseks, isegi kui sellel on tegelikult vähem tuumasid.  [cite_start]See mõjutab eriti väiksemaid ja keskmise suurusega ettevõtteid (VKE), kes võivad olla sunnitud maksma kasutamata tuumade eest.  [cite_start]Näiteks vSphere Standardi soovituslik hind (MSRP) on $50 tuuma kohta (3-aastase lepingu aastane väärtus, ACV) ja see sisaldab vCenter Standardit.  [cite_start]vSphere Essentials Plus Kit maksab $35 tuuma kohta (3-aastane ACV), kuid seda müüakse ainult 96-tuumalise komplektina, mis on mõeldud maksimaalselt kolmele hostile ja sisaldab vCenter Essentialsi. 

[cite_start]Lisaks on Broadcom lihtsustanud VMware'i tooteportfelli, koondades tooted suurematesse pakettidesse nagu VMware Cloud Foundation (VCF) ja vSphere Foundation (VVF).  [cite_start]Kuigi see võib tunduda lihtsustamisena, võib see sundida kliente maksma komponentide eest, mida nad tegelikult ei vaja.  [cite_start]Näiteks vSphere Foundation (VVF) maksab $135 tuuma kohta (3-aastane ACV) ja sisaldab vSphere Enterprise Plus, vCenter Standard, Tanzu Kubernetes Grid ja Aria Suite Standard. 

[cite_start]Need litsentsimuudatused on globaalsed ja jõustusid 2024. aasta alguses, mil VMware lõpetas püsivate litsentside müügi ja paljudel juhtudel ka olemasolevate püsilepingute toe (SnS) uuendamise.  [cite_start]Kuigi Broadcom on teinud mõningaid järeleandmisi, näiteks pakkudes teatud püsivate litsentsiversioonide jaoks tasuta kriitilisi turvapaiku ka pärast toe lõppemist, on üldine suund selge: VMware'i tehnoloogia jätkuv kasutamine nõuab edaspidi aktiivset tellimust.  [cite_start]See üleminek on Broadcomi jaoks strateegiline samm prognoositava korduva tulu suurendamiseks, mis aga klientidele, eriti neile, kes said kasu püsivate litsentside pikaajalisest kasutamisest, tähendab potentsiaalselt kõrgemaid ja jäigemaid jooksvaid kulusid. 

### "Turunduskalkulaatori" Loogika Dekonstrueerimine: Levinud Väljajätmised ja Lihtsustused

[cite_start]Tüüpilised turunduskalkulaatorid, mida kasutatakse VMware kohapealsete lahenduste kulude hindamiseks, keskenduvad sageli piiratud hulgale otsestele kuludele.  [cite_start]Need hõlmavad tavaliselt baasriistvara (protsessor, mälu, minimaalne salvestusruum) ja tarkvara esialgseid litsentsikulusid, mis tihti ei peegelda mitmeaastaste tellimuste kogusummat ega kõiki vajalikke lisandmooduleid. 

Levinud väljajätmised hõlmavad:

* [cite_start]**Põhjalik riistvara**: Võrguseadmed (lülitid, tulemüürid, ruuterid, mis ületavad baasvajadusi), spetsiaalsed salvestusmassiivid (SAN/NAS), robustsed katkematu toite allikad (UPS), füüsilise turvalisuse infrastruktuur. 
* [cite_start]**Tarkvara täielik elutsükkel**: Mitmeaastased tellimuskulud, serveri operatsioonisüsteemide kliendipääsulitsentsid (CAL), oluliste lisandmoodulite (nt täiustatud varundus, avariitaaste lahendused, kui need pole sobivalt komplekteeritud) kulud, andmebaaside litsentsid. 
* [cite_start]**Tegevuskulud**: Andmekeskuse ruum (rent/kolokatsioon), energiatarve (sageli oluliselt alahinnatud, PUE faktorit ignoreeritakse), jahutus, täismahus IT-administratsiooni palgad ja ajakulu, riistvara hoolduslepingud, tarkvara tugiteenuste uuendamine pärast esialgseid perioode, varundusmeediumid/väljaspool asukohta varundamine, avariitaaste testimine, interneti ribalaius ja personali koolitus. 

[cite_start]Turunduskalkulaatorite peamine puudus on "jäämäe tipp" probleem.  [cite_start]Need näitavad kulude nähtavat, sageli väiksemat osa, samal ajal kui palju suurem, veealune osa (pikaajalised tegevuskulud ja põhjalikud kapitalikulud) jääb varjatuks.  [cite_start]See loob eksitava mulje taskukohasusest.  [cite_start]Turunduskalkulaatorid on loodud müügivihjete genereerimiseks ja esmaseks kaasamiseks.  [cite_start]Keerukas ja detailne TCO on selle etapi jaoks liiga koormav.  [cite_start]Seetõttu lihtsustavad nad, keskendudes kergesti kvantifitseeritavatele ja sageli madalamatele esialgsetele kuludele.  [cite_start]See lihtsustamine jätab aga välja kriitilised pikaajalised tegevus- ja terviklikud kapitalikulud, mis viib tegeliku TCO märkimisväärse alahindamiseni. 

## 3. VMware Kohapealse Lahenduse Kogukulu (TCO) Põhikomponendid

[cite_start]Selles jaotises kirjeldatakse üksikasjalikult kõiki kuluosi, mis on liigitatud kapitali- (CapEx) ja tegevuskuludeks (OpEx).  [cite_start]Need moodustavad täiustatud TCO mudeli aluse. 

### A. Kapitalikulud (CapEx)

[cite_start]Kapitalikulud hõlmavad esialgseid investeeringuid riistvarasse, tarkvarasse ja infrastruktuuri ettevalmistamisse. 

#### Riistvaraline Infrastruktuur

* [cite_start]**Serverid (Arvutusvõimsus: Protsessor, RAM, Emaplaat, Kest, Toiteplokk)**: Serverikomponentide maksumus võib oluliselt varieeruda. [cite: 28] [cite_start]Ettevõtlusklassi riistvara äriserveritele maksab tavaliselt $1000 kuni $2500 serveri kohta [cite: 29][cite_start], kuid algtaseme serverid võivad maksta $3000–$5000 ja ettevõtlusklassi serverid üle $10 000.  [cite_start]Näiteks komponentide hinnad: Xeon D protsessor (võib olla komplektis emaplaadiga), 2x 1TB ettevõtlusklassi SSD-d ($240), 64GB ECC RAM ($329), Supermicro 1U kest ($160), serveri emaplaat (nt Supermicro MBD-M11SDV-8C-LN4F-O hinnaga $929), toiteplokk ($100).  [cite_start]Teine allikas pakub protsessoritele hinda $100-$800+, ECC RAM $50-$150 16GB mooduli kohta, 500GB SSD $50-$150, emaplaat $150-$350, toiteplokk $80-$200, kest $50-$250. 

    **Tabel 1: Näidis Serverikonfiguratsioonid ja Hinnangulised Kulud (VKEdele)** 

    | Serveri Roll      | Protsessor (Tüüp, Tuumad, Kogus) | RAM (GB) | Salvestusruum (Tüüp, Maht, SSD/HDD Kogus)        | Emaplaat (Klass) | Kest (Vormitegur) | Toiteplokk (Võimsus, Redundantsus) | Hinnangulised Komponendikulud (€) | Hinnanguline Serveri Kogukulu (€) |
    | :---------------- | :------------------------------- | :------- | :----------------------------------------------- | :--------------- | :---------------- | :----------------------------------- | :-------------------------------- | :-------------------------------- |
    | Väike Host        | Intel Xeon E-2xxx, 4-6 tuuma, 1  | 32-64    | 2x 1TB NVMe SSD (RAID1)                          | Serveriklass     | 1U Rack           | 450W, Mitte-redundantne              | 1200 - 1800                       | 1500 - 2200                       |
    | Keskmine Host     | Intel Xeon Silver, 8-12 tuuma, 2 | 128-256  | 4x 1TB NVMe SSD (RAID10) + 2x 4TB SATA HDD       | Serveriklass     | 2U Rack           | 750W, Redundantne                    | 3500 - 5500                       | 4000 - 6000                       |
    | Suur VKE Host     | Intel Xeon Gold, 16+ tuuma, 2    | 512+     | 6x 2TB NVMe SSD (RAID5/6) + SAN ühendus          | Serveriklass     | 2U/4U Rack        | 1200W, Redundantne                   | 7000 - 12000                      | 8000 - 14000                      |
    
    *\*Märkus: Hinnad on illustratiivsed ja võivad oluliselt varieeruda sõltuvalt konkreetsest konfiguratsioonist, tootjast ja hetke turuhindadest.*  *Tabelis on kasutatud allikates [3, 4, 11] toodud andmeid ja kohandatud eurodesse ligikaudse vahetuskursiga.*  See tabel aitab lahti mõtestada abstraktse "serverikulu", näidates, kuidas erinevad konfiguratsioonid hinda mõjutavad ja pakkudes praktilist lähtepunkti riistvara eelarvestamiseks. 

* [cite_start]**Salvestusruum (Keskendutakse algtaseme/keskklassi iSCSI SAN/NAS lahendustele VMware jaoks, sh kettad)**: Keskklassi salvestuslahendused võivad maksta $20 000–$50 000, samas kui täiustatud süsteemid võivad ulatuda kuuekohaliste summadeni.  [cite_start]VKEde jaoks otsitakse algtaseme iSCSI SAN-e alla $10 000, kuigi see võib olla keeruline.  [cite_start]Dell SCv3000 seeria võib olla alla $10k.  [cite_start]Infortrendi karbid 10TB mahuga alla $10k.  [cite_start]Broadberry CyberStore 212WSS-NVME (2U, 12 lahtrit NVMe Windows Storage Server) alates $4347 (ilma ketasteta).  [cite_start]VKEdele/võimsatele kasutajatele mõeldud NAS-seadmed (4-8 lahtrit) maksavad $300-$600 (ilma ketasteta). 

    **Tabel 2: Hinnangulised Kulud VKE SAN/NAS Lahendustele (10-20TB Kasutatav Maht)** 

    | Salvestusruumi Tüüp | Näidis Bränd/Mudel              | Kettalahtrid | Ketta Tüüp (nt Ettevõtlusklassi SATA/SSD) | Vajalik Ketaste Arv Sihtmahuks | Ketaste Hinnanguline Kulu (€) | Kesta/Kontrolleri Hinnanguline Kulu (€) | Hinnanguline Kogukulu (€) |
    | :------------------ | :------------------------------ | :----------- | :---------------------------------------- | :----------------------------- | :---------------------------- | :-------------------------------------- | :------------------------ |
    | NAS                 | Synology DS1621+/QNAP TS-673A   | 6            | 6x 4TB Enterprise SATA HDD (RAID6)        | 6                              | 1200 - 1800                   | 700 - 1000                              | 1900 - 2800               |
    | iSCSI SAN (Algtase) | Dell PowerVault ME5012/HPE MSA 1060 | 12           | 8x 2.4TB 10K SAS HDD (RAID6)              | 8                              | 2000 - 3200                   | 5000 - 8000                             | 7000 - 11200              |
    | iSCSI SAN (SSD)     | Dell PowerVault ME5012/HPE MSA 1060 | 12           | 6x 1.92TB Enterprise SSD (RAID5)          | 6                              | 2400 - 4000                   | 5000 - 8000                             | 7400 - 12000              |
    
    *\*Märkus: Hinnad on illustratiivsed. Kasutatav maht sõltub RAID tasemest. Allikad:.[3, 12, 13, 14, 15, 16]*  Jagatud salvestusruum on VMware funktsioonide, nagu vMotion ja HA, jaoks kriitilise tähtsusega.  See tabel aitab eelarvestada seda sageli märkimisväärset kulu. 

* [cite_start]**Võrguseadmed (Lülitid, Ruuterid, Tulemüürid VKE serveriruumi jaoks)**: Lülitid, ruuterid ja tulemüürid võivad maksta $5000 kuni $50 000 sõltuvalt jõudlusest/turvalisusest.  [cite_start]Serveripõhiselt võib võrguseadmete (lüliti, ruuter, tulemüür) kulu olla $250-$500 serveri kohta.  [cite_start]VKE ruuter: $80-$350  [cite_start]või $150-$500.  [cite_start]Hallatav lüliti: $250-$450  [cite_start]või $50-$500+.  [cite_start]Riistvaraline tulemüür: $400-$2300  [cite_start]või $200-$1000+.  [cite_start]Täiustatud tulemüürid võivad maksta $5000–$20 000 seadme kohta. 

    [cite_start]**Tabel 3: Tüüpilised Kulud VKE Võrguinfrastruktuuri Komponentidele** 

    | Komponent                   | Spetsifikatsiooni Märkused (nt PoE, Läbilaskevõime) | Hinnanguline Hinnavahemik (€) |
    | :-------------------------- | :------------------------------------------------- | :-------------------------- |
    | Hallatav GbE Lüliti (24-porti) | L2/L3 funktsioonid, VLAN tugi                      | 250 - 700                   |
    | Hallatav GbE PoE+ Lüliti (24-porti) | Sama, mis eelmine + PoE+                           | 400 - 1000                  |
    | VKE Tulemüüri Seade         | 500 Mbps - 1 Gbps läbilaskevõime, VPN tugi         | 300 - 1500                  |
    | VKE Ruuter                  | Gigabit WAN/LAN, VPN                               | 100 - 400                   |
    
    *\*Märkus: Hinnad on illustratiivsed. Allikad:.[3, 17, 18, 19]*  Võrgundus on fundamentaalne, kuid sageli TCO baasarvutustes tähelepanuta jäetud.  See tabel toob esile usaldusväärse ja turvalise kohapealse keskkonna jaoks vajalike äriklassi seadmete kulud. 

* [cite_start]**Katkematu Toite Allikas (UPS)**: Proportsionaalne kulu on tavaliselt 25-50% serveri maksumusest.  [cite_start]Redundantsed toitesüsteemid varundamiseks võivad maksta $1500 - $3000.  [cite_start]UPSi enda energiakulu võib olla $100-$200 kuus.  [cite_start]UPSi kulud ei piirdu ainult riistvaraga, vaid hõlmavad ka pidevat akude vahetust (tavaliselt iga 3-5 aasta tagant) ja nende tarbitavat elektrit.  [cite_start]Need tegurid lisanduvad TCO-le ja jäävad sageli tähelepanuta. 

* [cite_start]**Esialgsed Füüsilise Turvalisuse Meetmed (Juurdepääsukontroll, baasvalve serveriruumile)**: Biomeetrilised juurdepääsukontrollid: $10 000–$50 000 seadistuse kohta.  [cite_start]Klahvistikuga juurdepääsukontroll ukse kohta: $1000–$2500;  [cite_start]võtmekaardi/kiibi süsteemid: $1500–$3500 ukse kohta.  [cite_start]Baas-CCTV paigaldus: $100-$2500+; seirekulu: $30-$150+ kaamera kohta kuus.  [cite_start]Füüsiline turvalisus on kohapealse lahenduse baaskulu.  [cite_start]Isegi serveriruumi ukse baasjuurdepääs klahvistiku/kaardiga ja paar kaamerat kujutavad endast arvestatavat kapitalikulu ja potentsiaalset jooksvat tegevuskulu. 

* [cite_start]**Riistvara Amortisatsioon**: Tavapärane 3-5 aastane kasutusiga/amortisatsiooniperiood serveritele, salvestusruumile ja võrguseadmetele TCO arvutustes.  [cite_start]See teisendab kapitalikulud aastaseks kuluks võrdluse tegemiseks.  [cite_start]Riistvara ei kesta igavesti; selle maksumus tuleb TCO täpseks arvutamiseks jaotada selle kasulikule elueale.  [cite_start]3-5 aastane tsükkel on tavaline, mille järel jõudlus, töökindlus ja garantiikate vähenevad, käivitades sageli uuendustsükli. 

#### [cite_start]Tarkvara Litsentsimine (Esialgsed ja Jooksvad Tellimuskulud) 

* **VMware vSphere (vSphere Standard, Essentials Plus Kit; tuumapõhine, miinimumid, vCenter)**:
    vSphere Standard: $50/tuum MSRP (3-aastane ACV), sisaldab vCenter Standardit.  Minimaalselt 16 tuuma protsessori kohta. 
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
    
    *\*Märkus: Hinnad on MSRP-põhised ja teisendatud eurodesse ligikaudse vahetuskursiga. Tegelikud hinnad võivad erineda. Allikad:.[2, 26]*  See tabel illustreerib uue litsentsimismudeli rahalist mõju tüüpilistele VKE konfiguratsioonidele, muutes abstraktse "tuumapõhise" hinnakujunduse konkreetseks. 

* [cite_start]**VMware Site Recovery Manager (SRM) / Live Site Recovery**: SRM oli varem litsentseeritud kaitstud virtuaalmasina (VM) kohta (Standard kuni 75 VM-i saidi kohta, Enterprise ilma piiranguta).  [cite_start]Nüüd pakutakse VMware Live Recovery't (mis sisaldab Live Site Recovery't, SRM-i edasiarendust) "Kaitstud VM-i tellimuste" ja "Kaitstud mahu tellimustega".  [cite_start]VMware Live Site Recovery kasutamiseks on vaja ainult "Kaitstud VM-i tellimusi".  [cite_start]Kaitstud VM-ide soovituslik hind (3-aastane tellimus ja pikem, aastahind): $400 VM-i kohta aastas (USD).  [cite_start]See sisaldab tootmistuge ja arveldatakse ettemaksuna.  [cite_start]Live Site Recovery jaoks ei ole VM-ide miinimumostu nõuet.  [cite_start]Olemasolevad SRM-litsentsid konverteeritakse aktiveerimisel Live Site Recovery litsentsideks.  [cite_start]See on lisandmoodul, mitte automaatselt VCF/VVF baaspakettide osa.  [cite_start]Avariitaaste on kriitiline, kuid sageli eraldi hinnastatud komponent.  [cite_start]Üleminek VMware Live Recovery tellimusele VM-i kohta lisab veel ühe korduva kulukihi. 

* [cite_start]**Serveri Operatsioonisüsteemid (nt Windows Server Standard/Datacenter - tuumapõhine, CALid)**: Windows Server 2022 Standard (16-tuumaline pakett): ~$527.  [cite_start]Microsofti poolt on 16-tuumaline pakett + 10 CAL-i hinnaga $1680.  [cite_start]Windows Server 2022 Datacenter (16-tuumaline OEM): ~$4700 - $5063.  [cite_start]Mõlemad nõuavad tuumapõhist litsentsimist (minimaalselt 16 tuuma serveri kohta, 8 tuuma protsessori kohta) ja CAL-e (kasutaja- või seadmepõhised).  [cite_start]Standard CAL-id: ~$39/CAL;  [cite_start]5 kasutaja CAL-i ~$190. 

    **Tabel 5: Windows Serveri Litsentsimiskulude Näited** 

    | Serveri OS Väljaanne (Standard/Datacenter) | Litsentseeritavate Tuumade Arv (nt 16, 32) | Hinnanguline Tuumalitsentsi Kulu (€) | Kasutaja/Seadme CALide Arv | Hinnanguline CALide Kulu (€) | Hinnanguline OS Litsentsimise Kogukulu (€) |
    | :------------------------------------------ | :----------------------------------------- | :----------------------------------- | :------------------------- | :--------------------------- | :----------------------------------------- |
    | Windows Server 2022 Standard              | 16                                         | ~500 - 700                           | 10 Kasutajat               | ~350                         | ~850 - 1050                                |
    | Windows Server 2022 Standard              | 32                                         | ~1000 - 1400                         | 25 Kasutajat               | ~875                         | ~1875 - 2275                               |
    | Windows Server 2022 Datacenter            | 16                                         | ~4200 - 4700                         | 10 Kasutajat               | ~350                         | ~4550 - 5050                               |
    | Windows Server 2022 Datacenter            | 32                                         | ~8400 - 9400                         | 25 Kasutajat               | ~875                         | ~9275 - 10275                              |
    
    *\*Märkus: Hinnad on illustratiivsed ja põhinevad jaemüügihindadel. CALide vajadus sõltub keskkonnast. Allikad:.[33, 34, 35, 36, 37, 38, 39, 40]*  Serveri OS-i litsentsid on märkimisväärne tarkvarakulu.  See tabel aitab mõista tuumalitsentside ja CALide kahekordset kulustruktuuri. 

* [cite_start]**Varundus- ja Replikatsioonitarkvara (nt Veeam, Commvault - kui ei ole mujal komplekteeritud/kaetud)**: Veeam Data Platform Advanced Universal License (10 eksemplari pakett, 1-aastane uuendus): Hinnapakkumised varieeruvad suuresti, nt $1710 10 eksemplari paketi kohta või kõikuvad pakkumised $9000 kuni $22 000 110 eksemplari kohta.  [cite_start]Veeam Universal License (VUL) on 5-10 litsentsiga kimpudes;  [cite_start]1 VM = 1 litsents.  [cite_start]Commvault Backup & Recovery for VMs (tellimus, 10 VM-i, 3 aastat): $4259.99.  [cite_start]Tüüpilised RPO/RTO 2. taseme rakendustele: RTO 4 tundi, RPO 2 tundi.  3. [cite_start]tase: RTO 8-24 tundi, RPO 4 tundi.  [cite_start]Need eesmärgid mõjutavad varundusstrateegiat ja tarkvara valikut.  [cite_start]Varundustarkvara on hädavajalik ja selle maksumus võib olla märkimisväärne. 

### B. Tegevuskulud (OpEx)

[cite_start]Tegevuskulud on jooksvad kulud, mis on seotud infrastruktuuri haldamise ja hooldamisega. 

#### [cite_start]Andmekeskuse / Serveriruumi Kulud 

* [cite_start]**Ruum (Kolokatsioonikulud 1/3 kuni täisräkile kui proksi)**: Hetzneri 1/3 räkk (14U): €100/kuus + seadistustasu.  [cite_start]Täisräkk (42U+): €167-€251/kuus + seadistustasu.  [cite_start]Atlexi 42U räkk Euroopas: alates €569/kuus.  [cite_start]Andmekeskuse ettevalmistus võib maksta $200-$1000 ruutjala kohta.  [cite_start]Isegi kui kolokatsiooni ei kasutata, annavad need arvud aimu serveriruumi füüsilise ruumi, turvalisuse ja keskkonnatingimuste väärtusest. 

* [cite_start]**Energiatarve (Serverid, Salvestusruum, Võrguseadmed, Jahutus – PUE mõju)**: Elektrihind Eestis: Väga muutlik, Nord Pooli hetkehind + marginaal (nt 0.0045 €/kWh + kuutasu) + jaotustariif (~0.01-0.017 €/kWh + püsitasu) + taastuvenergiatasu (0.0084 €/kWh 2025. aastal) + aktsiis (0.0021 €/kWh) + käibemaks (22%).  [cite_start]Lihtsuse huvides võib tuletada keskmise koguhinna, nt ~0.15-0.20 €/kWh ettevõtetele.  [cite_start]Näide serveri energiatarbimisest: 200W pidevalt tarbiv server maksab ~$300 aastas hinnaga $0.168/kWh.  [cite_start]IT-seadmed moodustavad 50-60% andmekeskuse energiatarbimisest;  [cite_start]jahutus 35-45%.  [cite_start]Energiatõhususe näitaja (Power Usage Effectiveness, PUE): Keskmine PUE on ~1.8;  [cite_start]tõhusad andmekeskused sihivad <1.2.  [cite_start]Google'i PUE on ~1.08-1.11.  [cite_start]VKE serveriruumide PUE on tõenäoliselt kõrgem (vähem optimeeritud), võib-olla 1.6-2.0.  [cite_start]Energiakulud ja jahutus võivad moodustada 5-10% esialgsest riistvarakulust aastas.  [cite_start]Väikeste andmekeskuste puhul võib see ületada $10 000 aastas. 

    **Tabel 6: Hinnangulised Aastased Energiakulud Näidiskonfiguratsioonidele** 

    | Konfiguratsioon (nt Väike/Keskmine/Suur VKE Serveriseadistus CapEx-ist) | Hinnanguline Riistvara Koguvõimsus (W) | Eeldatav PUE | Energiatarve Kokku (kWh/aastas) | Segatud Elektrihind (€/kWh - Eesti näide) | Hinnanguline Aastane Energiakulu (€) |
    | :--------------------------------------------------------------------- | :------------------------------------ | :----------- | :----------------------------- | :--------------------------------------- | :---------------------------------- |
    | "Väike VKE (1-2 serverit, NAS, baasvõrk)"                              | 500 - 800                             | 1.8          | 7884 - 12614                   | 0.18                                     | 1419 - 2271                         |
    | "Keskmine VKE (3-4 serverit, SAN, võrk)"                               | 1500 - 2500                           | 1.6          | 21024 - 35040                  | 0.18                                     | 3784 - 6307                         |
    
    *\*Märkus: Hinnad on illustratiivsed. PUE väärtus on hinnanguline VKE keskkonnale. Allikad:.[3, 5, 6, 47, 48, 49, 50, 51]*  Energia on suur korduvkulu.  See tabel näitab, kuidas riistvaravalikud, operatiivne tõhusus (PUE) ja kohalikud elektrihinnad kombineeruvad märkimisväärseks kuluks. 

* [cite_start]**Jahutus (osana energiakulust või eraldi, kui andmed võimaldavad)**: Tavaliselt arvestatakse PUE arvutustes.  [cite_start]Kui jahutussüsteemide spetsiifilised kulud on teada (nt serveriruumi eraldi kliimaseade), võib need eraldi välja tuua.  [cite_start]Jahutus võib maksta $200-$400 kuus kohapealsete varundussüsteemide puhul. 

#### IT Administratsioon ja Haldus

* [cite_start]**Personalikulud (VMware Süsteemiadministraatori palk - Eesti keskmine)**: Keskmine süsteemiadministraatori netokuupalk Eestis: €1129 - €3037.  [cite_start]Brutopalga saamiseks tuleb arvestada Eesti tulumaksu, sotsiaalmaksu, töötuskindlustust.  [cite_start]Ligikaudne brutopalk: €1800 - €5000 kuus ehk €21 600 - €60 000 aastas.  [cite_start]VMware spetsialisti puhul tõenäoliselt selle vahemiku keskpaigast ülespoole.  [cite_start]IT-töötajate palgad võivad moodustada 30-50% esialgsest riistvarakulust aastas. 

* [cite_start]**Ajaeraldus (Hinnanguline % administraatori ajast VMware haldamiseks VKEdes)**: Uuring näitab, et 30% VMware administraatori ajast kulub hooldusele;  [cite_start]keskmiselt 12.3 tundi nädalas VMware lahenduste haldamisele ja paikamisele.  [cite_start]See uuring ei ole spetsiifiline VKEdele, kuid annab üldise ettekujutuse.  [cite_start]Teine allikas viitab, et virtualiseerimiseelne administraatori/serveri suhe on 1:10 kuni 1:30, virtualiseerimisjärgne võib olla palju kõrgem (nt 1:200), kuid varieerub suuresti.  [cite_start]VKEdes võib üks administraator hallata kogu infrastruktuuri, seega on tema koguajast VMware'ile kuluv % asjakohasem.  [cite_start]Administraatori aja kvantifitseerimine on ülioluline.  [cite_start]Isegi kui VKE-l ei ole pühendunud VMware administraatorit, kulub osa üldise süsteemiadministraatori ajast VMware ülesannetele.  [cite_start]Sellel "ajamaksul" on reaalne kulu. 

#### Hooldus ja Tugi

* [cite_start]**Riistvara Hoolduslepingud (Garantiijärgsed, kui kohaldatav)**: Tavaliselt 5-10%  [cite_start]või 10-15%  [cite_start]esialgsest riistvarakulust aastas.  [cite_start]$500k investeeringu puhul $50k-$75k aastas. 
* [cite_start]**Tarkvara Tellimuste Uuendamine/Tugi (Pärast esialgset perioodi/kaasatud tuge)**: VMware tellimused sisaldavad nüüd tuge perioodi vältel.  [cite_start]Muu tarkvara (OS, varundus) puhul lisanduvad jooksvad toe/tellimuse tasud. 

#### Varundus- ja Avariitaasteoperatsioonid

* [cite_start]**Meedia, Väljaspool Asukohta Varundamine, Avariitaaste Testimine**: Varunduslintide/ketaste kulud, turvalised väljaspool asukohta varundusteenused ning ribalaiuse/personali ajakulu regulaarseks avariitaaste testimiseks.  [cite_start]Pilvevarundus 10TB jaoks: $2760–$3600 aastas. 
* [cite_start]**Andmete Väljavool (pilvepõhise avariitaaste või varunduskomponentide puhul)**: Kui avariitaasteks/varunduseks kasutatakse pilve, kohalduvad väljavoolutasud.  [cite_start]AWS/Azure/GCP: esimene 100GB/kuus sageli tasuta, seejärel ~$0.08-$0.09/GB esialgsetele tasemetele, suuremate mahtude puhul madalamad.  [cite_start]See on kriitiline taastestsenaariumide puhul.  [cite_start]Pilvekomponendid avariitaaste strateegias võivad taastejuhtumi ajal kaasa tuua muutuvaid, potentsiaalselt kõrgeid väljavoolukulusid, mida tuleb TCO-s arvesse võtta. 

#### Ühenduvus

* [cite_start]**Interneti Ribalaius**: Äriinternetiühenduse maksumus, maht sõltub replikatsioonivajadustest, kasutajate juurdepääsust jne. 
* **VPN Kulud (kui kohaldatav kaughalduseks/avariitaasteks)**:
    [cite_start]AWS Site-to-Site VPN: ~$0.05/tund ühenduse kohta + andmeedastus. 
    Azure VPN Gateway (VpnGw1): ~$139/kuus + andmeedastus. 
    Google Cloud VPN: ~$0.065/tund tunneli kohta + IPsec liiklus. 
    Paljude "väikeste" tegevuskulude (nagu VPNid, spetsiifilise tarkvara tugi, meedia) summa võib koguneda märkimisväärseks korduvaks kuluks, mis sageli jääb kõrgetasemelistes TCO vaadetes tähelepanuta. 

## 4. VMware Kohapealse Lahenduse TCO Võrdlusanalüüs

[cite_start]Selles jaotises võrreldakse VMware kohapealse lahenduse hinnangulist kogukulu (TCO) avalike pilveteenuste pakkujatega, kasutades tüüpilist VKE töökoormuse stsenaariumi.  [cite_start]Eesmärk on anda kvantitatiivne alus kohapealse ja pilvepõhise infrastruktuuri kulude võrdlemiseks. 

### [cite_start]Illustratiivne TCO Võrdlus: Kohapealne VMware vs. Avalik Pilv 

Hüpoteetiline VKE töökoormus:

* [cite_start]**Hostid**: 2-3 füüsilist serverit 
* [cite_start]**vCPU kokku**: 32-64 
* [cite_start]**RAM kokku**: 256-512 GB 
* [cite_start]**Salvestusruum**: 10-20 TB kiire (SSD-põhine) jagatud salvestusruum (SAN/NAS) 
* [cite_start]**Võrk**: Baasvõrgundus (hallatavad lülitid, tulemüür) 
* [cite_start]**Varundus**: Igapäevane varundus, 30-päevane säilitamine 
* [cite_start]**Avariitaaste (DR)**: Baasvõimekus (nt VMware Live Site Recovery või samaväärne) 
* [cite_start]**TCO periood**: 3 aastat 

[cite_start]Kohapealse lahenduse TCO arvutatakse jaotises 5 esitatud täiustatud mudeli alusel, kasutades Eesti turule vastavaid hinnanguid (nt elektrihind, tööjõukulud).  [cite_start]Pilveteenuste pakkujate (AWS, Azure, GCP) samaväärsete ressursside hinnastamisel keskendutakse Euroopa regioonidele (nt Frankfurt, Iirimaa, Madalmaad). 

* [cite_start]**Amazon Web Services (AWS)**: 
    * [cite_start]Arvutusvõimsus (EC2): m5/m6i/m6a seeria üldotstarbelisteks ja r5/r6i seeria mäluoptimeeritud instantsideks.  [cite_start]Näiteks m5.2xlarge (8 vCPU, 32GB RAM) Iirimaal maksab ~$0.4280/tund (~$312/kuus).  [cite_start]r5.2xlarge (8 vCPU, 64GB RAM) Iirimaal maksab ~$0.5640/tund (~$412/kuus). 
    * [cite_start]Salvestusruum (EBS, S3): EBS gp3 SSD-d (nt Iirimaal: salvestusruum ~$0.088/GB-kuus; IOPS ~$0.0055/lisatud IOPS-kuus üle 3000; läbilaskevõime ~$0.044/lisatud MB/s-kuus üle 125).  [cite_start]S3 Standard (Iirimaa, esimesed 50TB): ~$0.023/GB-kuus. 
    * [cite_start]Võrk: Application Load Balancer ~$0.0225/tund + $0.008/LCU-tund.  [cite_start]Site-to-Site VPN ~$0.05/tund + andmeedastus. 
    * [cite_start]Tugi: Business Support miinimum $100/kuus või astmeline % kuludest. 
    * [cite_start]Andmete väljavool: Esimene 100GB/kuus tasuta, seejärel ~$0.09/GB. 

* **Microsoft Azure**:
    * [cite_start]Arvutusvõimsus (VMs): Dsv5/Dasv5 seeria üldotstarbelisteks ja Esv5/Easv5 seeria mäluoptimeeritud Linuxi VM-ideks.  [cite_start]Näiteks D4s v3 (Linux, 4 vCPU, 16GB RAM) Põhja-Euroopas maksab tellimuslepingu alusel ~$175/kuus.  [cite_start]E-seeria (nt E4s v3, 4 vCPU, 32GB RAM) alghinnad väikseimatele instantsidele alates ~$58.40/kuus. 
    * [cite_start]Salvestusruum (Managed Disks, Blob Storage): Premium SSD P10 (128GB) Lääne-Euroopas ~$19/kuus.  [cite_start]Standard SSD E10 (128GB) Lääne-Euroopas ~$9/kuus.  [cite_start]Blob Storage Hot LRS (Lääne-Euroopa, esimesed 50TB) ~$0.0184/GB-kuus. 
    * [cite_start]Võrk: Standard Load Balancer ~$0.025/tund + $0.005/GB andmetöötlus.  [cite_start]VPN Gateway VpnGw1 ~$138.70/kuus + andmeedastus. 
    * [cite_start]Tugi: Standard Support $100/kuus. 
    * [cite_start]Andmete väljavool: Esimene 100GB/kuus tasuta, seejärel ~$0.087/GB. 

* **Google Cloud Platform (GCP)**:
    * [cite_start]Arvutusvõimsus (Compute Engine): E2-standard, N2-standard, N2D-standard üldotstarbelisteks;  [cite_start]M1, M2, M3 mäluoptimeeritud.  [cite_start]Näiteks e2-standard-2 (2 vCPU, 8GB RAM) europe-west1 ~$0.074/tund (~$54/kuus). 
    * [cite_start]Salvestusruum (Persistent Disks, Cloud Storage): Persistent Disk SSD (europe-west1) ~$0.170/GB-kuus.  [cite_start]Cloud Storage Standard (europe-west1, mitme regiooni) ~$0.023/GB-kuus. 
    * [cite_start]Võrk: HTTP(S) Load Balancer (keerukas, vt GCP hinnakirja).  [cite_start]HA VPN ~$0.065/tund tunneli kohta + liiklus. 
    * [cite_start]Tugi: Standard Support miinimum $29/kuus või 3% kuutasudest. 
    * [cite_start]Andmete väljavool: Esimene 100GB/kuus tasuta (teatud sihtkohtadesse), seejärel ~$0.08-$0.12/GB. 

[cite_start]**Tabel 7: 3 Aasta TCO Ülevaade – Kohapealne vs. Tüüpiline Pilve Stsenaarium (Illustratiivne)** 

| Kulukategooria                      | Kohapealne Aasta 1 (€) | Kohapealne Aasta 2 (€) | Kohapealne Aasta 3 (€) | Kohapealne 3-a TCO (€) | Pilv X Aasta 1 (€) | Pilv X Aasta 2 (€) | Pilv X Aasta 3 (€) | Pilv X 3-a TCO (€) |
| :---------------------------------- | :--------------------- | :--------------------- | :--------------------- | :--------------------- | :----------------- | :----------------- | :----------------- | :----------------- |
| Riistvara CapEx (Amorditud)         | 10 000                 | 10 000                 | 10 000                 | 30 000                 | 0                  | 0                  | 0                  | 0                  |
| Tarkvara Tellimus                   | 8 000                  | 8 000                  | 8 000                  | 24 000                 | 12 000             | 12 000             | 12 000             | 36 000             |
| Energia/Ruum                        | 3 000                  | 3 150                  | 3 300                  | 9 450                  | Sisaldub teenuses  | Sisaldub teenuses  | Sisaldub teenuses  | Sisaldub teenuses  |
| Administraatori Tööjõud             | 15 000                 | 15 750                 | 16 500                 | 47 250                 | 5 000              | 5 250              | 5 500              | 15 750             |
| Hooldus/Tugi (Riistvara, muu tarkvara) | 2 500                  | 2 500                  | 2 500                  | 7 500                  | Sisaldub teenuses  | Sisaldub teenuses  | Sisaldub teenuses  | Sisaldub teenuses  |
| Muud Tegevuskulud (Varundus, DR, ühenduvus) | 2 000                  | 2 000                  | 2 000                  | 6 000                  | 3 000              | 3 000              | 3 000              | 9 000              |
| **KOKKU** | **40 500** | **41 400** | **42 300** | **124 200** | **20 000** | **20 250** | **20 500** | **60 750** |
[cite_start][cite: 106]
[cite_start]*Märkus: See tabel on väga illustratiivne ja põhineb hüpoteetilistel väärtustel, et näidata kulustruktuuri erinevusi.*  *"Pilv X" esindab tüüpilist avaliku pilve pakkujat. Administraatori tööjõukulu pilves on väiksem, eeldades vähem infrastruktuuri haldamist.*  [cite_start]Pilve TCO võib tunduda esialgu madalam tänu puuduvale riistvara esialgsele investeeringule, kuid andmete väljavoolutasud, kõrgema taseme tugiteenused ja premium-klassi salvestusruumi/instantside tüübid võivad kulusid aja jooksul suurendada.  [cite_start]Kohapealsel lahendusel on kõrge esialgne kapitalikulu, kuid potentsiaalselt madalamad ja prognoositavamad tegevuskulud, kui seda hästi hallatakse ja riistvara kasutatakse kogu selle eluea jooksul.  [cite_start]"Tasuvuspunkt" varieerub oluliselt. 

### Madalama Hinnaklassi Pilve-/Virtualiseerimisteenuse Pakkujate Kaalumine

[cite_start]On oluline märkida, et turul on ka teenusepakkujaid nagu Hetzner ja OVHcloud, kes pakuvad virtuaalmasinaid ja pühendatud servereid märkimisväärselt madalamate hindadega võrreldes suurte hüperkvaliteetsete pilveteenuste pakkujatega.  [cite_start]Näiteks Hetzner CX22 (2 vCPU, 4GB RAM, 40GB NVMe) maksab ligikaudu €3.79 kuus.  [cite_start]OVHcloud Value VPS (1 vCore, 2-4GB RAM, 40-80GB NVMe) maksab alates ~$5.95 kuus. 

[cite_start]Kuigi need valikud võivad tunduda odavamad, kaasnevad nendega sageli vähem hallatud teenuseid, erinevad tugistruktuurid ja need võivad nõuda rohkem ettevõttesisest ekspertiisi haldamiseks ja turvamiseks.  [cite_start]See võib potentsiaalselt nihutada osa "pilve" tegevuskuludest tagasi sisemistele tööjõukuludele.  [cite_start]Seega, kuigi "odavamad" pilvevalikud eksisteerivad, vajab ka nende TCO hoolikat hindamist.  [cite_start]Madalam esialgne hind võib kompenseeruda suurema halduskoormuse või vähemate integreeritud ettevõtlusfunktsioonidega (nt täiustatud turvalisus, DRaaS, põhjalikud haldusportaalid) võrreldes hüperkvaliteetsete pakkujate või hästi struktureeritud VMware kohapealse keskkonnaga. 

## 5. Täiustatud TCO Mudel VMware Kohapealsete Lahenduste Jaoks

[cite_start]Selles jaotises esitatakse raamistik VMware kohapealsete lahenduste kogukulu (TCO) põhjalikumaks hindamiseks.  [cite_start]Mudel on loodud kohandatavaks ja arvestab laiaulatuslikult erinevaid kuluaspekte, mis ületavad turunduskalkulaatorite lihtsustatud loogikat. 

### Raamistiku Ülevaade: Sisendid, Arvutusloogika, Väljundid

[cite_start]Esitatud TCO mudel on mõeldud kasutamiseks tabelarvutusprogrammis (nt Excel) ja katab tüüpiliselt 3- kuni 5-aastast TCO arvutusperioodi. 

* [cite_start]**Sisendid**: Kasutajapõhised andmed, nagu hostide arv, virtuaalmasinate arv, tuumade arv protsessori kohta, salvestusruumi vajadus (TB), kohalikud tööjõumäärad (€/tund), elektrienergia maksumus (€/kWh), serveriruumi PUE, riistvara/tarkvara allahindlusmäärad. 
* [cite_start]**Arvutusloogika**: Kõigi jaotises 3 kirjeldatud kapitalikulude (CapEx) summeerimine (amordituna aastaseks kuluks) ja kõigi aastaste tegevuskulude (OpEx) summeerimine. 
* [cite_start]**Väljundid**: Aastane TCO, kumulatiivne TCO perioodi lõikes, TCO ühe virtuaalmasina kohta aastas. 

### Kulukategooriate ja Arvutusmeetodite Üksikasjalik Jaotus

[cite_start]Järgnev tabelilaadne struktuur kirjeldab mudeli loogikat, tuues välja peamised kuluartiklid, mõõtühikud, näidisväärtused ja arvutusvalemid.  [cite_start]Organisatsioonid peaksid asendama näidisväärtused oma tegelike või hinnanguliste andmetega. 

[cite_start]**Tabel 8: Täiustatud TCO Mudeli Loogika VMware Kohapealsetele Lahendustele** 

| Kuluartikkel                                  | Mõõtühik                       | Kogus (Näidis) | Ühiku Hind (€, Näidis) | Aastane Kulu (€) | Märkused/Eeldused                                                                                                         |
| :-------------------------------------------- | :----------------------------- | :------------- | :--------------------- | :--------------- | :------------------------------------------------------------------------------------------------------------------------ |
| **A. KAPITALIKULUD (AMORDITUD AASTASEKS)** |                                |                |                        |                  | Amortisatsiooniperiood: 3-5 aastat                                                                                        |
| Serverid (kokku)                              | tk                             | 3              | 4500                   | 4500             | Nt 3 serverit á €4500, amordituna 3a peale = (€4500*3)/3 = €4500/aastas. Vt Tabel 1.                                       |
| Jagatud Salvestusruum (SAN/NAS)               | seade                          | 1              | 8000                   | 2667             | Nt €8000 seade, amordituna 3a peale. Vt Tabel 2.                                                                        |
| Võrguseadmed (lülitid, tulemüür)              | komplekt                       | 1              | 1500                   | 500              | Nt €1500 komplekt, amordituna 3a peale. Vt Tabel 3.                                                                     |
| Katkematu Toite Allikas (UPS)                 | tk                             | 1              | 1000                   | 333              | Nt €1000 UPS, amordituna 3a peale. Arvesta ka akuvahetustega elutsükli jooksul (eraldi OpEx reana või siin amortiseerituna). |
| Füüsiline Turvalisus (algne)                  | komplekt                       | 1              | 1200                   | 400              | Nt ukselukk, 1-2 kaamerat, amordituna 3a peale.                                                                           |
| VMware vSphere Litsentsid                     | tuum                           | 48             | 46 (Standard, aastane) | 2208             | Nt 3 hosti, 2 CPU/host, 8 tuuma/CPU = 48 tuuma. vSphere Standard @ ~$46/tuum/aasta (3a leping). Vt Tabel 4.                 |
| Serveri OS Litsentsid (nt Windows Server)     | server/CAL                     | 3 serverit, 50 CAL | 600/server, 35/CAL     | 1183             | Nt 3x€600 (16 tuuma Standard litsents, amordituna 3a) + 50x€35 (CALid, amordituna 3a). Vt Tabel 5.                      |
| Varundustarkvara Litsents                     | VM                             | 15             | 50 (aastane)           | 750              | Nt 15 VM-i @ ~$50/VM/aastas.                                                                                              |
| **KAPITALIKULUD KOKKU (AASTANE)** |                                |                |                        | **12541** |                                                                                                                           |
| **B. TEGEVUSKULUD (AASTASED)** |                                |                |                        |                  |                                                                                                                           |
| Serveriruumi Ruum (kolokatsiooni analoog)     | räkk                           | 1              | 1200 (aastane)         | 1200             | [cite_start]Nt 1/3 räki hind Hetzneris.                                                                                      |
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
[cite_start][cite: 123]
*Märkus: See tabel on illustratiivse iseloomuga ja näitab mudeli struktuuri. [cite_start]Reaalsed väärtused tuleb asendada organisatsiooni spetsiifiliste andmetega.* 

[cite_start]Arvutusvalemite näited: 

* [cite_start]Energiakulu Aastas (€) = (((Serverite koguvõimsus (W)+Salvestusruumi võimsus (W)+Võrguseadmete võimsus (W))/1000) × PUE × 24 × 365 × Elektrihind (€/kWh)) 
* [cite_start]Riistvara Aastane Amortisatsioonikulu (€) = Riistvaraühiku ostuhind (€)/Amortisatsiooniperiood (aastates) 
* [cite_start]IT Administraatori Aastane Kulu VMware Haldusele (€) = Administraatori aastapalk (€) × (VMware haldusele kuluv aeg (%) / 100) 

### Peamised Eeldused ja Kohandatavad Muutujad

[cite_start]Mudeli täpsus sõltub sisendandmete kvaliteedist.  [cite_start]Kasutajad peavad kohandama järgmisi muutujaid: 

* [cite_start]**Riistvara uuendustsükkel**: Tavaliselt 3, 4 või 5 aastat. 
* [cite_start]**Tööjõukulud**: Kohalikud keskmised palgamäärad IT-spetsialistidele. 
* [cite_start]**Elektrienergia tariifid**: Kohalikud keskmised hinnad (€/kWh), arvestades kõiki komponente. 
* [cite_start]**PUE (Power Usage Effectiveness)**: Hinnanguline väärtus serveriruumi/andmekeskuse kohta (VKE puhul tõenäoliselt 1.6-2.0). 
* [cite_start]**Allahindlusmäärad**: Riist- ja tarkvara ostmisel saadud allahindlused. 
* [cite_start]**Mahtude näitajad**: VM-ide arv, hostide arv, tuumade arv, salvestusmaht. 
* [cite_start]**Tarkvara valikud**: Konkreetsed varundus-, turva- jm tarkvarad ja nende litsentsimismudelid. 

### Andmete Kogumise Juhised Mudeli Sisendite Jaoks

[cite_start]Täpse TCO arvutuse aluseks on usaldusväärsed sisendandmed.  [cite_start]Soovitused andmete kogumiseks: 

* [cite_start]**Riistvara ja tarkvara**: Hankige mitmelt tarnijalt ametlikud hinnapakkumised. 
* [cite_start]**Elektrikulud**: Analüüsige viimaseid elektriarveid, et määrata keskmine €/kWh hind, arvestades kõiki tasusid. 
* [cite_start]**Administraatori ajakulu**: Hinnake või küsitlege IT-personali, kui palju aega kulub VMware'i haldusele, hooldusele ja probleemide lahendamisele. 
* **Kolokatsioon/Ruumikulud**: Kui kasutate kolokatsiooni, küsige pakkumisi. [cite_start]Oma ruumide puhul hinnake proportsionaalset osa üldkuludest (rent, kommunaalid). 
* [cite_start]**Hoolduslepingud**: Uurige kehtivaid või tüüpilisi hoolduslepingute hindu riist- ja tarkvarale. 

[cite_start]Staatiline TCO mudel on piiratud väärtusega.  [cite_start]Selle tegelik väärtus seisneb kohandatavuses.  [cite_start]Selgelt määratletud sisendmuutujad ja eeldused muudavad mudeli dünaamiliseks vahendiks stsenaariumide planeerimisel ja erinevate konfiguratsioonide võrdlemisel. 

## 6. Strateegilised Soovitused Kulude Täpseks Hindamiseks ja Optimeerimiseks

[cite_start]Kogukulu (TCO) analüüs ei ole pelgalt arvutuslik harjutus, vaid strateegiline vahend, mis võimaldab teha teadlikke otsuseid ja optimeerida IT-investeeringuid.  [cite_start]Järgnevalt on esitatud soovitused VMware kohapealsete lahenduste kulude täpsemaks hindamiseks ja optimeerimiseks. 

### Lihtsustatud Kalkulaatoritest Edasiminek: Põhjaliku TCO Lähenemise Rakendamine

[cite_start]On ülioluline mõista, et esialgne ostuhind moodustab vaid väikese osa IT-investeeringu kogukulust.  [cite_start]Organisatsioonid peavad arvesse võtma kõiki otseseid ja kaudseid kulusid kogu lahenduse elutsükli vältel.  [cite_start]See hõlmab mitte ainult riist- ja tarkvara soetamist, vaid ka paigaldust, energiatarvet, jahutust, haldust, hooldust, koolitust ja lõpuks ka kasutuselt kõrvaldamist.  [cite_start]Põhjalik TCO analüüs, nagu käesolevas aruandes kirjeldatud mudel, aitab vältida ootamatuid kulusid ja tagada eelarve täpsuse. 

### Regulaarsed TCO Ülevaatused: Kohanemine Muutuvate Ärivajaduste ja Tehnoloogiaga

[cite_start]TCO analüüs ei tohiks olla ühekordne tegevus.  [cite_start]IT-maailm on dünaamiline: tarkvara litsentsimismudelid (nagu VMware'i hiljutised muudatused), riistvara võimalused ja hinnad ning ärivajadused muutuvad pidevalt.  [cite_start]Seetõttu on soovitatav TCO-d regulaarselt üle vaadata, näiteks kord aastas või enne suuremaid infrastruktuuri uuendusi.  [cite_start]See tagab, et otsused põhinevad ajakohasel teabel ja et olemasolevad lahendused on endiselt kulutõhusad. 

### Kuluoptimeerimise Võimaluste Tuvastamine Kohapealses Keskkonnas

[cite_start]Põhjalik TCO analüüs aitab tuvastada valdkondi, kus on võimalik kulusid optimeerida: 

* **Riistvara**:
    * [cite_start]Õige suurusega lahendused (Right-sizing): Vältige üledimensioneerimist, valides serverid ja salvestusruumi vastavalt tegelikele vajadustele. 
    * [cite_start]Serverite konsolideerimine: Maksimeerige virtualiseerimise abil füüsiliste serverite arvu vähendamist. 
    * [cite_start]Energiatõhusad komponendid: Valige madalama energiatarbega protsessorid, toiteplokid ja muud komponendid. 
    * [cite_start]Riistvara eluea pikendamine: Kaaluge kolmanda osapoole hoolduslepinguid pärast tootjapoolse garantii lõppu, kuigi see võib kaasa tuua muid riske seoses toe kvaliteedi ja varuosade saadavusega. 
* **Tarkvara**:
    * [cite_start]Litsentside optimeerimine: Tagage vastavus litsentsitingimustele ilma üleliigsete litsentside eest maksmata.  [cite_start]Hinnake hoolikalt VMware'i uute komplekteeritud pakkumiste (VVF, VCF) sobivust, et vältida mittevajalike komponentide eest tasumist. 
    * [cite_start]Alternatiivsed tarkvaralahendused: Uurige võimalusel avatud lähtekoodiga või odavamaid alternatiive teatud funktsionaalsuste jaoks (nt seire, varundus), kui need vastavad nõuetele. 
* **Tegevuskulud**:
    * [cite_start]PUE parandamine: Investeerige serveriruumi/andmekeskuse jahutuse ja õhuvoolu optimeerimisse, et vähendada energiakadu. 
    * [cite_start]Energiahindade läbirääkimine: Suuremad tarbijad võivad saada soodsamaid elektritarne lepinguid. 
    * [cite_start]Administratiivsete ülesannete automatiseerimine: Kasutage skripte ja automatiseerimisvahendeid (nt VMware Aria Suite, kui see on litsentsitud), et vähendada käsitsi tehtava töö mahtu ja säästa tööjõukulusid. 

### Hübriidstsenaariumide ja Pilvemigratsiooni Käivitajate Hindamine

[cite_start]Kohapealse TCO analüüsi tulemused on väärtuslik sisend otsustamaks, kas teatud töökoormused tuleks viia pilve või rakendada hübriidmudeleid.  Näiteks:

* [cite_start]Kui kohapealse avariitaaste (DR) lahenduse TCO (sh VMware Live Site Recovery litsentsid, eraldi riistvara teises asukohas, testimiskulud) osutub ülemäära kõrgeks, võib pilvepõhine DRaaS (Disaster Recovery as a Service) olla kulutõhusam alternatiiv. 
* [cite_start]Muutuvate või ettearvamatute töökoormuste (nt hooajalised tipud, arendus- ja testimiskeskkonnad) puhul võib avaliku pilve skaleeritavus ja "maksa-kasutamise-eest" mudel pakkuda paremat kuluefektiivsust võrreldes kohapealse infrastruktuuri üledimensioneerimisega. 
* [cite_start]Spetsiifilised rakendused, mis nõuavad suurt arvutusvõimsust lühiajaliselt (nt AI/ML treening), võivad olla ökonoomsemad käitada pilves. 

[cite_start]TCO analüüs ei ole pelgalt numbrite kokkulöömine; see on vahend strateegiliseks otsustamiseks ja pidevaks parendamiseks.  [cite_start]Eesmärk ei ole mitte ainult kuluarvu saamine, vaid kulutegurite mõistmine ja optimeerimisvõimaluste või strateegiliste nihete tuvastamine. 

## 7. Järeldused

[cite_start]VMware kohapealsete lahenduste kogukulu (TCO) täpne hindamine on keerukas, kuid hädavajalik protsess iga organisatsiooni jaoks, kes kaalub või haldab sellist infrastruktuuri.  [cite_start]Pealiskaudne kulude hindamine, mis tugineb sageli lihtsustatud turunduskalkulaatoritele, võib viia ekslike investeerimisotsuste ja eelarve ületamiseni.  [cite_start]Need kalkulaatorid keskenduvad tavaliselt vaid osale esialgsetest kapitalikuludest, jättes arvestamata märkimisväärse hulga jooksvaid tegevuskulusid ja tarkvara elutsükli kogumaksumust. 

[cite_start]Broadcomi poolt VMware'i omandamise järgsed litsentsimismudeli muudatused – üleminek püsivatelt litsentsidelt tellimuspõhisele mudelile, tuumapõhine hinnastamine koos miinimumtuumade nõudega ja toodete komplekteerimine – on TCO arvutamist veelgi komplitseerinud ja nihutanud kulustruktuuri tugevalt tegevuskulude (OpEx) poole.  [cite_start]See nõuab organisatsioonidelt senisest hoolikamat ja pikaajalisemat finantsplaneerimist. 

[cite_start]Käesolevas aruandes esitatud täiustatud TCO mudel pakub põhjalikku raamistikku VMware kohapealsete lahenduste tegelike kulude hindamiseks.  [cite_start]See mudel arvestab laia spektrit kuluartikleid, sealhulgas: 

* [cite_start]**Kapitalikulud**: Serverid, salvestusruum (SAN/NAS), võrguseadmed, UPS, füüsiline turvalisus ning tarkvara esialgsed (või esimese perioodi) litsentsi- ja tellimuskulud (VMware vSphere, serveri OS, varundustarkvara, VMware Live Site Recovery). 
* [cite_start]**Tegevuskulud**: Serveriruumi/andmekeskuse ruumikulud, energiatarve (arvestades PUE-d), jahutus, IT-administratsiooni personalikulud ja ajakulu, riist- ja tarkvara hoolduslepingud ning tugiteenuste uuendamine, varundus- ja avariitaasteoperatsioonide kulud (sh meedia, väljaspool asukohta hoidmine, testimine, võimalikud andmete väljavoolutasud pilvepõhise DR korral), internetiühendus, VPN-kulud ja personali koolitus. 

[cite_start]Selle detailse mudeli kasutamine võimaldab IT-juhtidel ja finantsanalüütikutel saada realistliku pildi VMware kohapealse lahenduse finantsmõjudest.  [cite_start]See omakorda aitab paremini eelarvestada, põhjendada kulusid ja teha teadlikke võrdlusi alternatiivsete lahendustega, sealhulgas avalike pilveteenustega. 

[cite_start]Lõpetuseks tuleb rõhutada, et kuigi kohapealsed lahendused pakuvad kontrolli ja kohandamisvõimalusi, kaasnevad nendega mitmesugused kulud, mida tuleb hoolsalt jälgida ja hallata.  [cite_start]VMware'i üleminek tellimuspõhisele litsentsimisele rõhutab veelgi vajadust tugeva ja pideva TCO analüüsi järele, et tagada IT-investeeringute vastavus nii tehnoloogilistele kui ka rahalistele eesmärkidele. 

## Works cited

1.  [cite_start]CIO Playbook: Broadcom's VMware 2024 Licensing Changes - Redress Compliance, accessed June 4, 2025, https://redresscompliance.com/cio-playbook-broadcoms-vmware-2024-licensing-changes/ 
2.  Decoding the new Broadcom VMware vSphere Licensing Packages (for Small Deployments) | [cite_start]Veeam Community Resource Hub, accessed June 4, 2025, https://community.veeam.com/blogs-and-podcasts-57/decoding-the-new-broadcom-vmware-vsphere-licensing-packages-for-small-deployments-6398 
3.  How much does on premise hardware cost? - [cite_start]Interwest Communications, accessed June 4, 2025, https://www.interwestcommunications.com/blog/how-much-does-on-premise-hardware-cost/ 
4.  [cite_start]Server Cost Guide: Building and Renting | phoenixNAP KB, accessed June 4, 2025, https://phoenixnap.com/kb/server-cost 
5.  [cite_start]Server Room Power Consumption: Demand and Efficiency - ServerWatch, accessed June 4, 2025, https://www.serverwatch.com/servers/server-room-power-consumption/ 
6.  [cite_start]How to Drastically Reduce your Electricity Costs: Print Server vs. ezeep Hub, accessed June 4, 2025, https://www.ezeep.com/electricity-costs-print-server-vs-ezeep-hub/ 
7.  [cite_start]Cost of Server Comparison: On-Premises vs Cloud - Parallels, accessed June 4, 2025, https://www.parallels.com/blogs/ras/cost-of-server/ 
8.  [cite_start]Salary Estonia, Systems Administrator, Information... - Palgad.ee, accessed June 4, 2025, https://www.palgad.ee/en/salaryinfo/information-technology/systems-administrator 
9.  [cite_start]VMware Private Cloud vs On-Prem VMware: A Complete Cost and ..., accessed June 4, 2025, https://www.theitvortex.com/vmware-private-cloud-vs-on-prem-vmware-a-complete-cost-and-performance-analysis/ 
10. [cite_start]CALCULATING THE TCO: CLOUD VS ON PREMISE ... - Criticalcase, accessed June 4, 2025, https://www.criticalcase.com/blog/calculating-the-tco-cloud-vs-on-premise-infrastructure.html 
11. [cite_start]How to Build a Server with your Budget in 2025 - ServerMania, accessed June 4, 2025, https://www.servermania.com/kb/articles/how-to-build-a-server-with-costs-considered 
12. [cite_start]Affordable SAN for SMB - sysadmin - Reddit, accessed June 4, 2025, https://www.reddit.com/r/sysadmin/comments/evf5zt/affordable_san_for_smb/ 
13. [cite_start]iSCSI SAN / NAS StorageWindows Storage Server and Linux-based Storage - Broadberry Data Systems, accessed June 4, 2025, https://www.broadberry.com/iscsi-san-nas-storage-servers 
14. [cite_start]How Much Does a NAS Cost in 2025?, accessed June 4, 2025, https://nas.ugreen.com/blogs/buying-guide/how-much-does-a-nas-cost-in-2025 
15. [cite_start]The Best NAS (Network Attached Storage) Devices for 2025 - PCMag, accessed June 4, 2025, https://www.pcmag.com/picks/the-best-nas-network-attached-storage-devices 
16. [cite_start]VMware vSphere Pricing in 2024: Licensing and Overhead Costs - V2 Cloud, accessed June 4, 2025, https://v2cloud.com/blog/vmware-vsphere-licensing-and-costs 
17. [cite_start]On-Premise Cost Calculator - Transact Campus, accessed June 4, 2025, https://transactcampus.com/sales/on-premise-calculator 
18. [cite_start]How Much Does It Cost To Set Up A Small Business Network - Abacus, accessed June 4, 2025, https://goabacus.com/how-much-does-it-cost-to-set-up-a-small-business-network/ 
19. [cite_start]How Much Does It Cost To Set Up A Small Business Network, accessed June 4, 2025, https://www.montrealitservices.com/blog/setting-up-an-smb-network/ 
20. On-Premises vs Cloud Backup: Cost Comparison | [cite_start]News - Essential Designs, accessed June 4, 2025, https://www.essentialdesigns.net/news/on-premises-vs-cloud-backup-cost-comparison 
21. [cite_start]Access Control System Cost: Pricing Breakdown & Installation Fees, accessed June 4, 2025, https://www.getkisi.com/blog/breakdown-of-access-control-system-installation-costs 
22. How Much Do Remote Video Monitoring Services Cost? - [cite_start]Scout Security, accessed June 4, 2025, https://www.scoutsecurity.com/how-much-do-remote-video-monitoring-services-cost/ 
23. [cite_start]Total Cost of Ownership (TCO) Model for Storage - SNIA.org, accessed June 4, 2025, https://www.snia.org/sites/default/files/SSSI/SNIA_TCO_Whitepaper_03-2021.pdf 
24. [cite_start]Total Cost of Ownership (TCO) - Nutanix, accessed June 4, 2025, https://portal.nutanix.com/page/documents/details?targetId=Nutanix-Beam-User-Guide:bea-vm-nutanix-cg-tco-c.html 
25. What Is TCO? [cite_start]Total Cost of Ownership & Hidden Costs - ViewSonic Library, accessed June 4, 2025, https://www.viewsonic.com/library/business/what-is-tco-total-cost-ownership/ 
26. Vsphere free license ?! | [cite_start]VMware vSphere - Broadcom Community, accessed June 4, 2025, https://community.broadcom.com/vmware-cloud-foundation/communities/community-home/digestviewer/viewthread?GroupId=5107&MessageKey=726bbbb9-8d9b-4ee4-a478-263219560a56&CommunityKey=c24ac095-2065-4261-a4b5-6de9dade8734 
27. [cite_start]www.vmware.com, accessed June 4, 2025, https://www.vmware.com/content/dam/digitalmarketing/vmware/en/pdf/products/site-recovery-manager/vmware-site-recovery-manager-datasheet.pdf 
28. [cite_start]Purchasing VMware Live Recovery - Broadcom Techdocs, accessed June 4, 2025, https://techdocs.broadcom.com/de/de/vmware-cis/live-recovery/live-recovery/saas/vmware-live-recovery/vmware-live-recovery/set-up-vmware-live-recovery/purchasing-vmware-live-recovery.html 
29. [cite_start]Purchasing VMware Live Recovery - Broadcom Tech Docs, accessed June 4, 2025, https://techdocs.broadcom.com/us/en/vmware-cis/live-recovery/live-recovery/saas/vmware-live-recovery/set-up-vmware-live-recovery/purchasing-vmware-live-recovery.html 
30. [cite_start]assets.applytosupply.digitalmarketplace.service.gov.uk, accessed June 4, 2025, https://assets.applytosupply.digitalmarketplace.service.gov.uk/g-cloud-14/documents/92355/554756111604737-pricing-document-2024-05-01-1419.pdf 
31. [cite_start]VMware Live Site Recovery Licensing - Broadcom Tech Docs, accessed June 4, 2025, https://techdocs.broadcom.com/us/en/vmware-cis/live-recovery/live-site-recovery/9-0/overview/site-recovery-manager-system-requirements/srm-licensing.html 
32. [cite_start]VMware Live Site Recovery Licensing - Broadcom Techdocs, accessed June 4, 2025, https://techdocs.broadcom.com/kr/ko/vmware-cis/live-recovery/live-recovery/saas/vmware-live-site-recovery-installation-and-configuration/overview/site-recovery-manager-system-requirements/srm-licensing.html 
33. Microsoft Windows Server 2022 Standard 16 Core License | [cite_start]TTT, accessed June 4, 2025, https://www.trustedtechteam.com/products/microsoft-windows-server-2022-standard-16-core 
34. [cite_start]Buy Windows Server 2022 Standard Edition - Microsoft Store, accessed June 4, 2025, https://www.microsoft.com/en-us/d/windows-server-2022-standard-cal/dg7gmgf0d6m5 
35. [cite_start]Buy Windows Server 2022 Standard Edition - Microsoft Store, accessed June 4, 2025, https://www.microsoft.com/en-us/d/windows-server-2022-standard-cal/dg7gmgf0d6m5?activetab=pivot:overviewtab 
36. [cite_start]Microsoft Windows Server 2022 Datacenter 64-bit License (16 Core, OEM, DVD) - Newegg, accessed June 4, 2025, https://www.newegg.com/microsoft-windows-svr-datacntr-2022-64bit-english-1pk-dsp-oei-dvd-16-core/p/N82E16832350880 
37. [cite_start]Microsoft Windows Server 2022 Datacenter - license - 16 cores - P71-09389 - CDW, accessed June 4, 2025, https://www.cdw.com/product/microsoft-windows-server-2022-datacenter-license-16-cores/6729586 
38. Microsoft Windows Server 2022 User CAL | Client Access Licenses | 5 pack | [cite_start]OEM, accessed June 4, 2025, https://www.amazon.com/Windows-Server-2022-User-CAL/dp/B09DVPF4ZH 
39. [cite_start]Microsoft Windows Server 2022 Datacenter - 24 Core - Trusted Tech Team, accessed June 4, 2025, https://www.trustedtechteam.com/products/microsoft-windows-server-2022-datacenter-24-core 
40. Windows Server 2025 Licensing & Pricing | [cite_start]Microsoft, accessed June 4, 2025, https://www.microsoft.com/en-us/windows-server/pricing 
41. [cite_start]Veeam pricing is WILD : r/sysadmin - Reddit, accessed June 4, 2025, https://www.reddit.com/r/sysadmin/comments/1b9bfv4/veeam_pricing_is_wild/ 
42. [cite_start]Veeam Universal License (VUL), accessed June 4, 2025, https://www.veeam.com/universal-license.html 
43. [cite_start]Backup Software, accessed June 4, 2025, https://www.cdw.ca/category/software/utilities/backup/?w=FP1 
44. [cite_start]RPO & RTO Best Practices - Docs - OnePro Cloud, accessed June 4, 2025, https://docs.oneprocloud.com/userguide/presales/hyperbdr-rpo-rto-planning-best-practices.html 
45. [cite_start]Colocation data center: Top service by Hetzner, accessed June 4, 2025, https://www.hetzner.com/colocation/ 
46. [cite_start]42U rack rental in Europe - ATLEX.Ru, accessed June 4, 2025, https://www.atlex.ru/rack-in-europe-en/ 
47. [cite_start]Estonia - Electricity prices in Europe, accessed June 4, 2025, https://www.dayaheadmarket.eu/estonia 
48. [cite_start]High-Performance Computing Data Center Power Usage Effectiveness - NREL, accessed June 4, 2025, https://www.nrel.gov/computational-science/measuring-efficiency-pue 
49. [cite_start]Power usage effectiveness - Google Data Centers, accessed June 4, 2025, https://datacenters.google/efficiency 
50. The common 2025 Post: How are people getting free servers? [cite_start]: r/homelab - Reddit, accessed June 4, 2025, https://www.reddit.com/r/homelab/comments/1jdcobu/the_common_2025_post_how_are_people_getting_free/ 
51. [cite_start]Estonia EE: Electricity Price: HC: Between 5000 & 14999 KwH: incl All Taxes & Levies, accessed June 4, 2025, https://www.ceicdata.com/en/estonia/electricity-price-household-consumers/ee-electricity-price-hc-between-5000--14999-kwh-incl-all-taxes--levies 
52. [cite_start]Survey Reveals the True Costs of VMWare Deployments ..., accessed June 4, 2025, https://www.scalecomputing.com/blog/survey-reveals-the-true-costs-of-vmware-deployments-demonstrates-interest-in-vmware-alternatives 
53. Server to admin ratios | [cite_start]VMware vSphere - Broadcom Community, accessed June 4, 2025, https://community.broadcom.com/vmware-cloud-foundation/discussion/server-to-admin-ratios 
54. [cite_start]Data Egress Cost: How To Take Back Control And Reduce Egress ..., accessed June 4, 2025, https://cast.ai/blog/data-egress-cost-how-to-take-back-control-and-reduce-egress-charges/ 
55. [cite_start]Cloud Data Egress Fee Tussle Plays Out with $250k AWS Comp -- AWSInsider, accessed June 4, 2025, https://awsinsider.net/articles/2025/05/09/cloud-data-egress-fee-tussle-plays-out-with-$250k-aws-comp.aspx 
56. AWS VPN | Pricing | [cite_start]Amazon Web Services (AWS), accessed June 4, 2025, https://aws.amazon.com/vpn/pricing/ 
57. VPN Gateway Pricing | [cite_start]Microsoft Azure, accessed June 4, 2025, https://azure.microsoft.com/en-us/pricing/details/vpn-gateway/ 
58. [cite_start]Network Connectivity pricing - Google Cloud, accessed June 4, 2025, https://cloud.google.com/network-connectivity/pricing 
59. Pricing | Cloud VPN | [cite_start]Google Cloud, accessed June 4, 2025, https://cloud.google.com/network-connectivity/docs/vpn/pricing 
60. [cite_start]m5.2xlarge Pricing and Specs: AWS EC2, accessed June 4, 2025, https://costcalc.cloudoptimo.com/aws-pricing-calculator/ec2/m5.2xlarge 
61. [cite_start]r5.2xlarge Pricing and Specs: AWS EC2, accessed June 4, 2025, https://costcalc.cloudoptimo.com/aws-pricing-calculator/ec2/r5.2xlarge 
62. [cite_start]Managed Disks pricing - Microsoft Azure, accessed June 4, 2025, https://azure.microsoft.com/en-us/pricing/details/managed-disks/ 
63. [cite_start]EC2 On-Demand Instance Pricing – Amazon Web Services - AWS, accessed June 4, 2025, https://aws.amazon.com/ec2/pricing/on-demand/ 
64. [cite_start]Application and Network Traffic Distribution - Elastic Load Balancing ..., accessed June 4, 2025, https://aws.amazon.com/elasticloadbalancing/pricing/ 
65. Pricing for AWS Support Plans | Starting at $29 Per Month | [cite_start]AWS ..., accessed June 4, 2025, https://aws.amazon.com/premiumsupport/pricing/ 
66. [cite_start]Azure VM Pricing: What You Don't Know (not yet) - Intercept, accessed June 4, 2025, https://intercept.cloud/en-gb/blogs/azure-vm-pricing 
67. [cite_start]Complete Guide to Azure VM: Pricing Models, Types & More - Umbrella, accessed June 4, 2025, https://umbrellacost.com/blog/azure-vm-pricing/ 
68. Pricing—Load Balancer | [cite_start]Microsoft Azure, accessed June 4, 2025, https://azure.microsoft.com/en-us/pricing/details/load-balancer/ 
69. Azure Support—Standard | [cite_start]Microsoft Azure, accessed June 4, 2025, https://azure.microsoft.com/en-us/support/plans/standard 
70. How Much Is Microsoft Azure Enterprise Support? - [cite_start]US Cloud, accessed June 4, 2025, https://www.uscloud.com/blog/how-much-is-microsoft-azure-enterprise-support/ 
71. Pricing | [cite_start]Compute Engine: Virtual Machines (VMs) - Google Cloud, accessed June 4, 2025, https://cloud.google.com/compute/all-pricing 
72. [cite_start]e2-standard-2 pricing: $48.92 monthly - Economize Cloud, accessed June 4, 2025, https://www.economize.cloud/resources/gcp/pricing/compute-engine/e2-standard-2/ 
73. [cite_start]Cloud Storage pricing, accessed June 4, 2025, https://cloud.google.com/storage/pricing 
74. Disk and image pricing | [cite_start]Google Cloud, accessed June 4, 2025, https://cloud.google.com/compute/disks-image-pricing 
75. Standard Support | [cite_start]Google Cloud, accessed June 4, 2025, https://cloud.google.com/support/standard 
76. [cite_start]Google Cloud Customer Care, accessed June 4, 2025, https://cloud.google.com/support 
77. Hetzner | [cite_start]Review, Pricing & Alternatives - GetDeploying, accessed June 4, 2025, https://getdeploying.com/hetzner 
78. [cite_start]Cheap hosted VPS by Hetzner: our cloud hosting services, accessed June 4, 2025, https://www.hetzner.com/cloud 
79. VPS Hosting Germany - Fast & Affordable Virtual Private Servers | [cite_start]OVHcloud Worldwide, accessed June 4, 2025, https://www.ovhcloud.com/en/vps/vps-deutschland/ 
80. OVHcloud | [cite_start]Review, Pricing & Alternatives - GetDeploying, accessed June 4, 2025, https://getdeploying.com/ovh 
81. Cloud Provider Pricing Calculator | [cite_start]Canine - An open source alternative to Heroku, accessed June 4, 2025, https://canine.sh/calculator 
82. [cite_start]Object Storage - Hetzner, accessed June 4, 2025, https://www.hetzner.com/storage/object-storage/ 
83. Cloud VPS Hosting | Flexible and High-Performance Solutions | [cite_start]OVHcloud Worldwide, accessed June 4, 2025, https://www.ovhcloud.com/en/vps/vps-cloud/ 
84. Low-Cost, High-Performance VPS | Starter VPS | [cite_start]OVHcloud Worldwide, accessed June 4, 2025, https://www.ovhcloud.com/en/vps/cheap-vps/ 
85. VPS – Your virtual private server in the cloud | [cite_start]OVHcloud Worldwide, accessed June 4, 2025, https://www.ovhcloud.com/en/vps/ 
86. [cite_start]Cheap hosted VPS by Hetzner: our cloud hosting services, accessed June 4, 2025, https://www.hetzner.com/cloud/ 
87. [cite_start]VMware Licensing in 2025: How Opscompass and House of Brick Manage Risks and Costs, accessed June 4, 2025, https://opscompass.com/vmware-licensing-challenges-2025/ 
88. Cloud vs. On-Premises: Choosing the Right Path for Your Data | [cite_start]Gart, accessed June 4, 2025, https://gartsolutions.com/cloud-vs-on-premises/ 
89. [cite_start]90+ Cloud Computing Statistics: A 2025 Market Snapshot - CloudZero, accessed June 4, 2025, https://www.cloudzero.com/blog/cloud-computing-statistics/
