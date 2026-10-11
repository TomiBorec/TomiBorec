# CarPlay mapy (Apple Mapy / Google Mapy / Waze) v digitálním kokpitu VW – rešerše pro Polo AW 2021 (před faceliftem)

Stav k: 10. 10. 2026. Komunitní projekty se v tomhle oboru mění po týdnech, takže to za pár měsíců může vypadat jinak.

---

## 0. TL;DR

1. **To, co viděl pán v Golfu, je skoro jistě komunitní mod pro jednotku MIB2 High (Harman, „Discover Pro“, interní označení `MHI2`).** Nejpravděpodobněji jde o projekt **MHI2-Carplay-Maps** (vydán 30. 9. 2026) nebo o příbuzný výzkumný projekt **mhi2_altscreen_carplay** (první funkční test ve voze 29. 9. 2026). Oba vezmou „druhou obrazovku“, kterou CarPlay posílá (tzv. AltScreen / Auxiliary Screen, video stream typu 111), a přes optickou sběrnici MOST ji pošlou do Virtual Cockpitu / AID.
2. **Polo AW před faceliftem mělo z výroby skoro jistě jednotku MIB2 *Standard* (`MST2`, Composition Media nebo Discover Media, výrobce Technisat/Preh, případně Delphi), ne MIB2 High.** Pro MST2 dnes **neexistuje žádný veřejný ani komerční mod**, který by dostal CarPlay mapu do AID. Žádný projekt na to ani nemíří. *Jistota: vysoká, že řešení pro MST2 dnes neexistuje. Střední, že tvoje Polo má MST2 (ověříš za 30 s, viz kap. 2).*
3. **OBDeleven to samo o sobě nevyřeší.** Je to nástroj na kódování a adaptace (přepínání existujících funkcí v řídicích jednotkách). CarPlay mapa v kokpitu vyžaduje upravit software v samotné jednotce infotainmentu (nahrát nové binárky, hooky a skripty přes SD kartu nebo SSH). Takový software pro MST2 neexistuje.
4. **Reálné cesty pro tebe:**
   - **A) Nedělat nic radikálního (doporučuju):** CarPlay na středovém displeji + nativní VW mapa v AID (pokud máš Discover Media). Pomocí OBDeleven si můžeš doladit, co se v AID zobrazuje.
   - **B) Přestavba na Discover Pro (MHI2) + mod:** technicky představitelné, ale drahé, nikým nezdokumentované přímo na Polu AW a s nejistým výsledkem (viz kap. 6). Tohle by ti skoro jistě musel dělat retrofit specialista.
   - **C) Počkat:** MIB2 High scéna se rozjela až v září 2026. Na MST2 ale nikdo nepracuje a hardwarově to bude spíš horší, takže na čekání bych nesázel.

---

## 1. Jak to vůbec technicky funguje

### 1.1 Proč to z výroby nejde
- CarPlay je z pohledu auta jen **H.264 video z telefonu**. Jednotka MIB ho dekóduje a vykreslí na středový displej. Auto nemá „mapu“ jako data, má jen obrázek.
- Apple od iOS 17.4 umí posílat **druhý video stream pro přístrojový štít (instrument cluster)** a data o navigaci (iAP2 RouteGuidance: šipky, vzdálenost, ETA). **To ale funguje jen tehdy, když to výrobce auta implementuje do firmware jednotky.** VW to do MIB2 nikdy oficiálně nedotáhl. Při spuštění to podporovaly jen vybrané značky (Volvo, Polestar, BMW, Porsche apod.).
- U MIB2 High se nativní VW mapa do Virtual Cockpitu posílá **jako H.264 video přes optickou sběrnici MOST150** (zařízení `/dev/mlb/isoTX2`, kódování typu `navigation_map_transmission_mode = MOST_streaming`, `compression = H264`). Přesně tohle mody zneužívají: místo VW mapy pošlou do kokpitu CarPlay video.

### 1.2 Co dělají existující mody
| Projekt | Cílová jednotka | Co umí | Stav |
|---|---|---|---|
| **omonob/MHI2-Carplay-Maps** | VW/Škoda **MHI2** (MIB2 High), FW MU1102, MU1367, MU1440 | CarPlay mapové video v AID + zoom/přepnutí pohledu z volantu. Bez šipek, názvů ulic a hudby (to je placený doplněk ~399 RMB) | Vydáno 30. 9. 2026, zdarma (GPL-3.0), instalace přes SD + MQB Toolbox V4.2A nebo M.I.B. 3.6.0 |
| **harman-f/mhi2_altscreen_carplay** | Škoda **MHI2** MU1440 (VW/SEAT „v plánu“) | Apple Mapy, Google Mapy a Waze jako živé video ve VC | Výzkum, ne instalátor. Vyžaduje SSH přístup |
| **OneB1t/VcMOSTRenderMqb** | MIB2 (High) se SSH | Zrcadlí přes VNC obrazovku telefonu do VC (~10 fps). Obecný hack, ne čisté CarPlay | Funkční pro pokročilé |
| **OneB1t/mhi2-android-auto-video-vc** | VW MIB2.5 High MU1367 | Android Auto mapové video do VC | Experimentální, „high-risk“ |
| **adi961/mib2-android-auto-vc** | MHI2 (VW) | Jen šipky, vzdálenost a hudba z Android Auto, žádná mapa. CarPlay výslovně nepodporuje | Funkční |
| **pvc1 / kamgurgul / luka-dev mib2q-carplay**, **yuedizhibo/MHI2Q-CarPlay-AltScreen** | **Audi MHI2Q** | CarPlay mapa a šipky ve VC a HUD | Jen Audi |

**Společný jmenovatel: všechno je to MIB2 High (Harman MHI2/MHI2Q).** Žádný z projektů nepodporuje MIB2 Standard (MST2) ani MIB3.

### 1.3 Jak je to s MIB3 (Golf 8, Polo po faceliftu)
Podle aktuálních zdrojů (září a říjen 2026) **neexistuje veřejný root ani mod pro MIB3**, takže ani CarPlay mapa v kokpitu. Přes kódování lze na MIB3 zapnout jen nativní VW mapu v kokpitu. Pokud tedy pán v Golfu měl CarPlay mapu v kokpitu, měl s velkou pravděpodobností **Golf 7/7.5 s Discover Pro (MHI2)**, ne Golf 8. *Jistota: střední až vysoká.*

### 1.4 Ani v Golfu to není bez problémů
Issue #10 v MHI2-Carplay-Maps (8.–9. 10. 2026): více majitelů Golfu 7.5 (MU1367, cluster 5G1920891A / 5G1920791A) hlásí **silnou pixelizaci, barevné bloky a nízké fps**. U některých se to týká jen Waze, u jiných všech tří aplikací. Oprava zatím není. Mod je tedy týdny starý a nedoladěný ani na své primární platformě.

---

## 2. Co přesně máš v Polu – ověř to jako první

Polo AW před faceliftem (2017 – jaro 2021, výroba MY2021 ještě „stará“ karoserie) mělo podle všech dohledaných zdrojů v Evropě tyto jednotky:
- **Composition Colour** (6,5″) / **Composition Media** (8″): MIB2 Standard, **bez navigace**
- **Discover Media** (8″, sklo): MIB2 Standard s navigací

**Discover Pro (MIB2 High)** jsem pro evropské Polo AW před faceliftem nedohledal. Discover Pro 9,2″ v Polu přišel až s faceliftem 2021, a to už jako MIB3. Japonské Polo mělo „Discover Pro“ 8″, ale to je jiný trh. *Jistota: střední. Proto to ověř přímo v autě.*

**Postup ověření (bez nářadí):**
1. Na jednotce podrž **MENU ~3 s**, nebo jdi do Nastavení → Informace o systému → Verze softwaru.
2. Podívej se na řetězec „train“:
   - `MST2_EU_VW_ZR_P0xxxT` → **MIB2 Standard, Technisat/Preh** (koncové **T**)
   - `MST2_..._D` → **MIB2 Standard, Delphi**
   - `MHI2_ER_VWG1x_...` → **MIB2 High (Harman)**. Pak je mod reálný, pokračuj kap. 5.
3. Případně v OBDeleven: jednotka **5F (Information Electronics)** → identifikace → číslo dílu HW/SW a název systému.

Pošli mi pak ten řetězec a já ti řeknu přesně, kde jsi.

---

## 3. Scénář 1 (pravděpodobný): máš MST2 (Discover Media nebo Composition Media)

### 3.1 Proč to nejde
- **Žádný projekt nepodporuje MST2.** Knowledge base projektu harman-f, která mapuje všechny MIB2 kokpity a cesty, jak se do nich dostane video, MST2 vůbec neřeší.
- **MST2 Toolbox (olli991, Technisat „T“ jednotky)** umí přes GEM a SD kartu patchovat SWaP/FEC (zdarma aktivovat CarPlay, Android Auto, navigaci, hlasové ovládání), skiny a CID-lock map. **Nic z toho nesouvisí s videem do kokpitu.** Pro Delphi MST2 neexistuje ani to.
- Aby šlo CarPlay video přesměrovat, musel by někdo na MST2 najít a zprovoznit (a) příjem druhého CarPlay streamu (AltScreen), (b) jeho dekódování a (c) odeslání přes MOST do AID. Na MHI2 to trvalo komunitě ~5 let (žádost od 4/2021, funkční mod 9/2026), a to je MHI2 výrazně lépe prozkoumaná a výkonnější platforma. Na MST2 tuhle práci nikdo veřejně nedělá.
- **Pozor na podvody:** na eBay, AliExpressu a TikToku se objevují nabídky „CarPlay ve Virtual Cockpitu“. Pro VW MIB2 Std jsem nenašel ani jedno ověřitelné řešení. Pokud ti někdo něco takového nabídne, chtěj video přímo z Pola AW s MST2 a s viditelným train stringem.

### 3.2 Co ti OBDeleven reálně umí udělat (na MST2)
- **Zapnout Developer mode / GEM (Green Engineering Menu)** v 5F. Je to předpoklad pro MST2 Toolbox.
- **Zapnout nebo vypnout App-Connect (Apple DIO / Google GAL) v adaptacích 5F**, po případném patchi toolboxem. U Pola 2021 to nejspíš už aktivní máš.
- **Doladit AID:** layouty, zobrazení navigačních pokynů a mapy v kokpitu (pokud to jednotka a cluster podporují), skrytí nebo zobrazení položek.
- **Nemůže:** poslat CarPlay obraz do AID. Ani One-Click Apps, ani ruční adaptace tohle neumí.

### 3.3 Nejlepší dosažitelný výsledek na MST2
- **Discover Media:** spustíš trasu ve VW navigaci (mapa v AID) a zároveň CarPlay s Waze nebo Apple Mapami na středovém displeji. Nevýhoda: dvě navigace, jedna z nich často s neaktuálními mapami. Ani to nemusí fungovat současně: aktivní navigace v CarPlay může VW mapu v kokpitu potlačit (na MHI2 to komunita řeší patchem „NavActiveIgnore“, na MST2 nic takového neexistuje).
- **Composition Media:** mapu v AID nativně nemáš vůbec. Pouze CarPlay na středovém displeji.

---

## 4. Scénář 2 (méně pravděpodobný): máš MHI2 (Discover Pro)

Pak je to reálné, ale:
- Musí sedět **přesná verze FW** (MU1102 / MU1367 / MU1440, kontroluje se podle velikosti `/ifs/lsd.jxe`) a **typ clusteru**: profil „791/794 AID 12.3“ (Golf, Passat), nebo „790 AID 10.5“ (novější FPK generace; sem logicky patří i AID v Polu a T-Rocu, Polo ale nikde výslovně otestované není).
- Postup v kostce: GEM (OBDeleven) → SD karta s MQB Toolboxem nebo M.I.B. → instalace profilu → restart. Vyžaduje to stabilní napájení (nabíječka nebo zdroj na baterii), zálohu a smíření se s rizikem, že jednotka nenaběhne.
- Tohle **zvládneš sám** s OBDeleven a SD kartou, pokud jsi technicky zdatný a máš plán B (obnova z backupu). Jinak specialista.

---

## 5. Scénář 3: přestavba Pola na Discover Pro (MHI2) kvůli modu

**Teoreticky možné, prakticky to nedoporučuju.** Důvody:

| Položka | Poznámka | Odhad nákladů (můj, nízká jistota) |
|---|---|---|
| Jednotka MHI2 (Discover Pro, ideálně FW MU1367) | Bazar, s nevyřešenou ochranou komponent (CP) | ~8–20 tis. Kč |
| Displej kompatibilní s MHI2 (8″ nebo 9,2″) | 9,2″ do palubky Pola AW není stavěný, 8″ MHI2 displej se musí shodovat s rámečkem | ~3–8 tis. Kč |
| Kabeláž: MOST optika 5F ↔ AID (pokud chybí), GPS anténa, USB, LVDS | Pro mapu v AID je nutná MOST linka | ~1–5 tis. Kč |
| Odstranění ochrany komponent (CP), kódování 5F/17/19, aktivace FEC | OBDeleven sám CP standardně neodstraní. Řeší dealer (GeKo) nebo specialista, popř. M.I.B. | ~2–6 tis. Kč |
| Práce | Specialista na retrofit | ~3–8 tis. Kč |
| **Celkem** | | **~20–45 tis. Kč** |

Pro srovnání: UK retrofiter účtuje za Golf 7 „Virtual Cockpit + MIB2.5 + 9,2″“ £2 348 včetně montáže.

**Rizika:** nikdo veřejně nezdokumentoval MHI2 + CarPlay-to-VC mod přímo v Polu AW. Mod je pár týdnů starý a má známé problémy s obrazem. Přestavbu by ti stejně musel dělat retrofiter. A za tyhle peníze se dá skoro koupit levný HUD nebo si prostě zvyknout dívat se na středový displej.

---

## 6. Musí to dělat někdo jiný?

| Situace | Zvládneš sám s OBDeleven? |
|---|---|
| Ověření typu jednotky (train string) | **Ano** |
| Zapnutí GEM, App-Connect, ladění AID | **Ano** |
| MST2 Toolbox patch (aktivace CarPlay, AA a navigace zdarma) | Ano, pokud jsi technicky zdatný (SD karta + GEM). Riziko zbrickování jednotky |
| CarPlay mapa v AID na MST2 | **Nikdo, řešení neexistuje** |
| CarPlay mapa v AID na MHI2 (kdybys ho měl) | Ano, s SD kartou a opatrností. Jinak specialista |
| Přestavba MST2 → MHI2 | **Specialista** (mechanika, kabeláž, CP, kódování) |

---

## 7. Moje doporučení

1. **Hned:** zjisti train string (kap. 2). Dvě minuty práce, které rozhodnou všechno.
2. **Když `MST2`** (očekávám): neutrácej za žádné nabídky „CarPlay ve VC“ pro Polo s Discover Media nebo Composition Media, je to buď podvod, nebo nedorozumění. Nastav si přes OBDeleven to, co jde, a smiř se s CarPlay na středovém displeji.
3. **Když `MHI2`:** pošli mi celý string a číslo dílu clusteru (jednotka 17), projdeme kompatibilitu profilu 790 a 791 a připravíme postup.
4. **Přestavbu na MHI2** bych dělal jen tehdy, kdybys stejně chtěl lepší jednotku (výkon, displej) a mod bral jako bonus. Ne jako jediný důvod.
5. **Sledovat:** `omonob/MHI2-Carplay-Maps`, `harman-f/mhi2_altscreen_carplay`. Pokud se někdy objeví cokoliv pro MST2, bude to nejspíš v okolí MST2 Toolboxu (olli991). Teď nic.

---

## 8. Doplněk: VW mapy v AID + Waze uprostřed – jde to?

### 8.1 Hlavní háček: CarPlay navigace vypíná mapu v kokpitu
I když budeš mít funkční VW navigaci, kombinace „VW mapa před řidičem + Waze s trasou uprostřed“ **z výroby nefunguje**:
- CarPlay i Android Auto při aktivním navádění (route guidance) **vynutí vypnutí doplňkových map OEM navigace** (kokpit, HUD). Telefon hlásí jednotce „naviguju já“, jednotka ukončí nebo potlačí vlastní navádění a AID přepne na kompas nebo prázdný placeholder. Je to záměr protokolu: v jednu chvíli smí navádět jen jedna navigace.
- Na MIB2 High to komunita obchází patchem v mib2-toolbox („allow both nav systems“ / NavActiveIgnore). I tam jsou hlášené vedlejší efekty (černá obrazovka CarPlay). **Pro MST2 takový patch neexistuje.**
- Starý trik „spustit trasu ve Waze, zastavit, znovu spustit“ fungoval jen do Waze 4.66.0.1 (2021). Byla to chyba ve Waze a je opravená.
- *Jistota: střední až vysoká, že se to týká i Pola s MST2. Chování je v celém koncernu stejné, na Polu konkrétně ověřené ale nemám.*

### 8.2 Kombinace, která reálně funguje
- **Navádí VW navigace** (mapa a šipky v AID) a **Waze běží v CarPlay bez zadané trasy** jen kvůli hlášením (policie, nehody, kolony, radary). Waze bez trasy navádění nehlásí, takže VW mapu v kokpitu nepotlačí. Tuhle kombinaci majitelé VAG aut běžně používají.
- Nevýhoda: VW navigace má horší data o provozu a výpočet trasy než Waze.

### 8.3 Open-source varianta
**Neexistuje a na MST2 ani nejde udělat.** Mapa v AID není samostatná aplikace. Jde o video stream, který renderuje uzavřený navigační engine VW uvnitř jednotky a posílá ho přes MOST. MST2 nemá aplikační platformu, na kterou by šlo nahrát třeba OsmAnd, a nikdo nenapsal náhradní renderer jako OneB1t pro MHI2.

### 8.4 Navigace „zdarma“ hackem
Technicky existuje. MST2 Toolbox (jen Technisat „T“) umí patchnout ověřování SWaP/FEC, takže jednotka přijme aktivační kód navigace bez licence. Mapy se pak nahrávají z neoficiálních zdrojů na originální CID kartu. Fakta k rozhodnutí:
- Jde o nelicencované použití softwaru a mapových dat (HERE/TomTom). Návod krok za krokem ani odkazy na stažené mapy sem dávat nebudu.
- Riziko zbrickování jednotky. U jednotek s jedním SD slotem patch přes toolbox nejde (nutný zápis do eMMC).
- I s tím narazíš na 8.1: Waze s trasou ti VW mapu z AID stejně vyhodí.
- Pro mapu v AID je potřeba MOST spoj 5F ↔ 17. U aut původně bez navigace (Composition Media) nemusí být vůbec natažený, stejně jako GPS anténa.

### 8.5 Legální cesty
- **Dealer, oficiální aktivace nebo dovybavení navigace:** zeptej se s VIN, zda pro tvou jednotku existuje FEC navigace + SD karta s mapou (pro MIB2 se to prodávalo jako příslušenství). Cenu ani dostupnost v roce 2026 jsem neověřil.
- **Výměna za Discover Media (MST2 s navigací):** použitá jednotka zhruba 300–700 € plus odstranění ochrany komponent, kódování a případně MOST a GPS.

### 8.6 Když navigaci máš, ale nefunguje – kontrolní seznam
1. **SD karta s mapami** je ve správném slotu a je to **originální karta** (MST2 má CID-lock vázaný na kartu, kopie na jinou kartu se odmítne).
2. **FEC navigace** je v jednotce aktivní (seznam FEC v servisním menu nebo v OBDeleven, jednotka 5F). Po výměně jednotky či zásahu může chybět nebo může být aktivní ochrana komponent.
3. **GPS:** anténa a kabel (modrý FAKRA konektor) jsou zapojené a pozice se fixuje.
4. **Mapa v AID:** kódování AID (jednotka 17) a 5F pro zobrazení mapy v kokpitu (OBDeleven) a fyzický MOST spoj.
5. **Aktualizace map:** přes oficiální portál VW (SD karta, postup v manuálu „Discover Media Gen.2 Map update“). Zda je pro tvůj rok a jednotku update ještě zdarma, ověř u VW.

---

## 9. Doplněk: „navigaci si můžu koupit“ – pozor, to ukazuje spíš na MIB3

- Nákup navigace přímo v autě (In-Car Shop) nebo ve VW Connect je funkce jednotky **Ready 2 Discover (MIB3)**, kterou Polo dostalo **až s faceliftem 2021**. MIB2 (Composition/Discover Media) žádný In-Car Shop nemá. Tam jde navigace dokoupit jen jako retrofit u dealera. *Jistota: střední až vysoká.*
- Pokud ti auto nebo aplikace nabízí „koupit navigaci“, máš skoro jistě **MIB3**, a tedy nejspíš Polo **po** faceliftu, i když jsi myslel, že je před ním.
- **Na MIB3 je situace lepší:** podle reportů z Golfu 8 a Audi s MIB3 často zůstane nativní VW mapa v kokpitu, i když uprostřed naviguje Waze přes CarPlay. U Google Map se mapa v kokpitu podle jednoho reportu vypíná. Reporty jsou anekdotické a rozporné, *jistota nízká až střední*.
- Podmínky nákupu (VW UK): VW ID účet, platná smlouva VW Connect / We Connect, ověření hlavního uživatele. Licence zůstává u auta. Obsah: 2D mapa a body zájmu offline, živá doprava jen s VW Connect Plus. Cenu jsem v oficiálních zdrojích nenašel, je vidět jen v In-Car Shopu.
- Zdroje: https://www.volkswagen-vans.co.uk/en/connected-services/vw-connect/upgrades.html , https://www.volkswagen-newsroom.com/en/press-releases/configuration-now-possible-launch-of-the-new-polo-with-standard-digital-cockpit-and-infotainment-system-7268 , https://www.vwvortex.com/threads/anyway-to-display-nav-map-on-the-digital-cockpit-and-use-carplay-to-naviigate-at-the-same-time.9279971/ , https://www.audiworld.com/forums/a4-b9-platform-discussion-212/factory-navigation-virtual-cockpit-waze-mmi-3052120/

---

## Zdroje
- jilleb/mib2-toolbox, issue #159 (2021–2024 diskuse, MOST vs. LVDS, H.264 pipeline): https://github.com/jilleb/mib2-toolbox/issues/159
- omonob/MHI2-Carplay-Maps (README, podporované MU a clustery, verze 30. 9. 2026): https://github.com/omonob/MHI2-Carplay-Maps
- omonob/MHI2-Carplay-Maps issue #10 (pixelizace na Golfu 7.5): https://github.com/omonob/MHI2-Carplay-Maps/issues/10
- harman-f/mhi2_altscreen_carplay (PoC 29. 9. 2026, Škoda MU1440): https://github.com/harman-f/mhi2_altscreen_carplay
- harman-f – knowledge base clusterů a přenosu mapy: https://github.com/harman-f/mhi2_altscreen_carplay/blob/main/docs/research/MIB2_CLUSTER_DISPLAY_TRANSPORT_KNOWLEDGE_BASE.md
- OneB1t/VcMOSTRenderMqb (VNC do VC, seznam clusterů): https://github.com/OneB1t/VcMOSTRenderMqb
- OneB1t/mhi2-android-auto-video-vc: https://github.com/OneB1t/mhi2-android-auto-video-vc
- adi961/mib2-android-auto-vc: https://github.com/adi961/mib2-android-auto-vc
- pvc1/mib2q-carplay (Audi MHI2Q): https://github.com/pvc1/mib2q-carplay
- yuedizhibo/MHI2Q-CarPlay-AltScreen (Audi MHI2Q AUG22): https://github.com/yuedizhibo/MHI2Q-CarPlay-AltScreen
- Mr-MIBonk/M.I.B. – More Incredible Bash (jen Harman MHIG/MHI2/MHI2Q): https://github.com/Mr-MIBonk/M.I.B._More-Incredible-Bash
- mattcabb/mib2std-toolbox (MST2 Technisat, nic o kokpitu): https://github.com/mattcabb/mib2std-toolbox
- zsec – přehled CarPlay/AA aktivací VW/Audi 2026, MST2 toolbox jen pro „T“ jednotky, MIB3 bez rootu: https://blog.zsec.uk/audi-carplay-2026/
- onejailbreak – CarPlay mapy ve VC Škoda, MIB3 bez veřejného řešení: https://onejailbreak.com/blog/carplay-maps-on-skoda-virtual-cockpit/
- vag-coding.net – MST2/MHI2 identifikace dle train stringu: https://www.vag-coding.net/tutorials-information/mst2-mhi2-mib2-software-update/
- vag-coding.net – Golf 8 nativní mapa ve VC (MIB3): https://www.vag-coding.net/vw/golf-8-5h/golf-8-5h-virtual-cockpit-and-mib3-navigation-map/
- Briskoda – MST2 Technisat aktivace CarPlay/AA zdarma: https://www.briskoda.net/forums/topic/535622-guide-activated-android-auto-apple-carplay-on-mib2-technisat-for-%E2%82%AC0-no-dealer-no-paid-codes/
- seatcupra.net – mapa v kokpitu potřebuje MOST spoj: https://www.seatcupra.net/forums/threads/digital-virtual-cockpit-retrofit-yet.446414/page-7
- VW Newsroom – Polo (MIB2 vs. MIB3, Discover Pro 9,2″ až u faceliftu, AID 1280×480): https://www.volkswagen-newsroom.com/en/the-new-polo-world-premiere-7136/digitalised-interior-7139
- 9to5mac – iOS 17.4 CarPlay instrument cluster: https://9to5mac.com/2024/02/22/ios-17-4-carplay/
- vagcodingleicester – cena retrofitu Golf 7 VC + MIB2.5: https://vagcodingleicester.co.uk/shop/vw-golf-mk7-7-5-virtual-cockpit-retrofit-9-2screenmib2-5/
- AudiWorld – CarPlay/AA vypíná OEM mapy v kokpitu při navádění: https://www.audiworld.com/forums/a4-b9-platform-discussion-212/factory-navigation-virtual-cockpit-waze-mmi-3052120/page2/
- seatcupra.net – Waze/Google Maps a potlačení kokpitu na MIB2: https://www.seatcupra.net/forums/threads/waze-google-maps-and-the-dashboard-override.463381/
- XDA – Android Auto deaktivuje vestavěnou navigaci: https://xdaforums.com/t/q-how-to-stop-android-auto-deactivating-the-cars-build-in-navigation.3839492/
- vwvortex – mapa v kokpitu + CarPlay navigace současně: https://www.vwvortex.com/threads/anyway-to-display-nav-map-on-the-digital-cockpit-and-use-carplay-to-naviigate-at-the-same-time.9279971/
- seatcupra.net – MST2 mapy, CID karta: https://www.seatcupra.net/forums/threads/updating-the-inbuilt-mib2-satnav-mib2-tricks-and-mib1.388586/post-5106370
- VW – Discover Media Gen.2 map update manuál: https://app-connect.volkswagen.com/mapupdates/manuals/Discover_Media_Gen.2_November_2020.pdf
