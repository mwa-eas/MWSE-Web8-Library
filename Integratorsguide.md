# MWSE Web8 Library – Integratörsguide

Malthe Winje Automation AB.

> **Early access (beta).** MWSE Web8 Library är i tidig åtkomst inför den officiella lanseringen.
> Mallar och guide kan ändras mellan versionerna. Läs [ändringsloggen](CHANGELOG.md) före en
> uppdatering.

Guiden beskriver hur du installerar MWSE Web8 Library (nedan biblioteket) i Saia PG5 Web Editor
8 (nedan Web8), placerar symboler, faceplates, inställningsblock och sidmallar i en vy, kopplar
dem till MWSE-FBoxar i PG5-programmet och laddar ned vyn till en PCD. Den förutsätter att du kan
Web8 och Fupla men inte känner till biblioteket.

Påståenden om hur Web8, PG5 och MWSE-FBoxarna fungerar har en källnot: Web Editor 8-hjälpen,
Web Editor-manualen eller MWSE FBox-hjälpen med kapitel, del eller sida (se
[avsnitt 11](#11-källor)), eller **Prövat** när påståendet bara vilar på Malthe Winje Automation
AB:s egna tester. Testmiljön var PG5 2.3.196 med Web Editor 8, en PCD3.M5360 med COSinus 1.28.51
och MWSE FBox-biblioteket V20.6.001 (se [2](#2-förutsättningar-i-pg5-programmet)). De mallar som kom till senast är prövade mot en
simulerad PCD och, där labbprogrammet har FBoxen, mot PCD:n 2026-10-25. Punkter som ännu inte är prövade är markerade **Ännu inte verifierat**, med vad du gör tills
vidare. Ett format eller beteende som bara är läst ur FBox-makrot, och inte ur någon manual, är
markerat som slutsats.

---

## 1. Vad biblioteket innehåller

Biblioteket har 86 mallar:

- **15 symboler med signaler:** pump, fläkt, motor, kompressor, spjäll, brandspjäll, tre ventiler
  (tvåväg, treväg, trepunkt), fyra givare, roterande värmeväxlare och Panelknapp. En symbol visar
  vila, drift/aktiv och i förekommande fall fel, hand och larm. Ett klick öppnar objektets
  faceplate.
- **14 statiska symboler:** batterier, filter, radiator, tankar, kretsar, två värmeväxlare för
  ventilation och en plattvärmeväxlare för vatten. Bara en bild och en namnetikett, utan signaler.
- **11 faceplates** (manöverpaneler) för motor, givare, ventil, spjäll, trepunktsventil,
  regulator PI, PID och P och handstyrning.
- **39 inställnings- och statusblock** för larmgränser, fördröjningar, skalning, regulatorer,
  tidkanaler och handstatus, bland dem 16 kurvblock som visar börvärdeskurvans punkter i en tabell
  och kurvan ritad bredvid. De är alltid synliga.
- **7 sidmallar:** larmlista, larmsummering, topplist, navigeringsmeny, sidofält, inloggning och
  inloggningsbakgrund.

Symboler, faceplates och inställningsblock läser och skriver MWSE-FBoxarnas A-symboler, som heter
`A.MWSE.<anläggning>.<objekt>.<signal>`, till exempel `A.MWSE.VS01.P01.R_Ut`. I mallarna står
gruppen som platshållaren `A.MWSE.Namn.`. Efter placering byter du den mot objektets egen grupp
([avsnitt 5.2](#52-koppla-symbolen-till-objektet)). Sidmallarna kopplas på andra sätt
([avsnitt 7](#7-sidmallar)).

Alla mallar, kategorier och signaler finns i [avsnitt 10](#10-mallar-och-fbox-signaler).

## 2. Förutsättningar i PG5-programmet

1. PCD-programmet använder MWSE FBox-biblioteket. Mallarna är byggda och prövade mot version
   **V20.6.001**. A-symbolerna i V25.10.003 och V26.09.015 är jämförda med den: alla namn som
   mallarna använder finns kvar. De nya versionerna har också nya FBoxar och signaler som mallarna
   inte använder än, till exempel Verkningsgrad V2 och `GfErsätt` i Analog ingång.

   **Undantag: Givarskalningsinställning.** I V25.10.003 och V26.09.015 skapar Analog ingång
   (`MWSE_InAnaL`) brytpunkterna `BPX1`, `BPX2`, `BPY1` och `BPY2` i två block med samma villkor,
   FBox-version före 21.05.001; det andra blocket byter plats på `BPX2` och `BPY1`. En Analog
   ingång av version 21.05.001 eller senare får då inga av de fyra A-symbolerna, och
   blockets fält blir tomma. Använd Givarskalningsinställning bara med en Analog ingång från
   V20.6.001 tills FBox-biblioteket är rättat.

   *Källa: FBox-makrona i MWSEFamilyAllmant.lib, V20.6.001, V25.10.003 och V26.09.015, jämförda
   2026-10-26. Undantaget är en slutsats ur makrot, inte prövat i ett bygge.* **Ännu inte
   verifierat:** mallarna mot en PCD vars program är byggt med V25.10.003 eller V26.09.015.
2. Varje objekt som ska visas har en MWSE-FBox **med namn** i Fupla. FBoxen skapar A-symbolerna
   `A.MWSE.<namn>.<signal>` först när ett namn anges på den. Ge FBoxen namnet
   `<anläggning>.<objekt>`, till exempel `VS01.P01`, så får signalerna namn som
   `A.MWSE.VS01.P01.R_Ut`. Samma gäller FBoxarna bakom inställningsblocken, till exempel
   `VS01.Reg` för en regulator.
3. En roterande värmeväxlare har två FBoxar: rotorns Motor och Verkningsgrad. Ge
   Verkningsgrad-FBoxen motorns namn plus `.VG`, till exempel `LB01.VVX1` och `LB01.VVX1.VG`
   ([5.7](#57-roterande-värmeväxlare-två-grupper)).
4. För larmlistan och larmsummeringen: en Alarm PCD init-FBox från Saias larmbibliotek, och
   antalet larm (FBoxens utgång Num) kopplat till en symbol som du namnger
   `<grupp>.antal` ([7.2](#72-larmlista-och-larmsummering)).
5. För klockan i Topplist: HEAVAC-biblioteket, vars klocka Topplist läser
   ([7.3](#73-topplist-och-sidtitel)).
6. Web8-projektet är kopplat till PG5-devicen, så att Web8 kan slå upp symbolerna
   ([avsnitt 4.1](#41-koppla-projektet-till-pg5-devicen)).

*Källa: MWSE FBox-hjälpen, sidorna Motor, Ingång analog larm, Utgång analog, Spjällstyrning,
Larm regleravvikelse, Börvärdeskurva 5 och Pumpstart, avsnitt "A-symboler"; sidan Regulator PI,
versionshistoriken (A-symboler skapas bara när FBoxen har ett namn); Web Editor-manualen, del 19,
Online Alarm List: Configure The Template (larmlistans symbol skapas av larm-FBoxen och får dess
namn). Namnformen `<anläggning>.<objekt>`: prövat. Namnet `.VG`, symbolen `<grupp>.antal` och
klockan: lästa ur labbets GUI och program, se respektive avsnitt.*

## 3. Installation

Ladda ned `MWSE-Web8-Library-<version>.zip` under **Releases** i förrådet
[mwa-eas/MWSE-Web8-Library](https://github.com/mwa-eas/MWSE-Web8-Library/releases). Under early
access är versionerna markerade som pre-release. Packa upp zip-filen. Den innehåller:

| Fil eller mapp | Innehåll |
|---|---|
| `MWSE-Web8-Library-<version>-mallar.zip` | Alla mallar, för import i ett steg ([3.1](#31-rekommenderat-importera-zip-filen)) |
| `mallar/` | Samma mallar som lösa `.xtml`-filer ([3.2](#32-alternativ-lösa-mallfiler)) |
| `grafik/` | Symbolernas bilder som lösa `.svg`-filer, i alla lägen (`__Idle`, `__Active`, `__Fault`), för den som vill använda bilderna utan mallarna |
| `Integratorsguide.md` | Den här guiden |
| `CHANGELOG.md` | Ändringsloggen |

### 3.1 Rekommenderat: importera zip-filen

1. Öppna fönstret **Libraries** i Web8.
2. Högerklicka i biblioteksträdet och välj **Import...**.
3. Välj `MWSE-Web8-Library-<version>-mallar.zip` och klicka **Open**.
4. Mallarna visas under kategorin `MWSE`, i underkategorierna `MWSE\Symbol\...`,
   `MWSE\Panel\...`, `MWSE\Inställning\...` och `MWSE\Sida\...` (hela listan i
   [avsnitt 10](#10-mallar-och-fbox-signaler)).

Web8 importerar `.xtml`-filer en och en, flera i samma `.xtml`-fil, eller en `.zip` som
innehåller `.xtml`-filer. En mall som redan finns i biblioteket hoppas över, och Web8 visar ett
felmeddelande när importen är klar. Om något går fel visas en importlogg.

I ett prov med de första 21 mallarna kom alla in, och **Libraries** visade namnen med å, ä och ö
rätt. Filnamnen i zip-filen är därför skrivna utan å, ä och ö.

> **Ännu inte verifierat:** importen av alla 86 mallar. Tills vidare: om importen misslyckas,
> importera de lösa filerna i `mallar/` enligt [3.2](#32-alternativ-lösa-mallfiler).

*Källa: Web Editor 8-hjälpen, Windows, s. 25–26 (Libraries Window: Import..., Importing
Templates). Importen och namnen: prövat 2026-10-23.*

### 3.2 Alternativ: lösa mallfiler

- **Importera:** som i [3.1](#31-rekommenderat-importera-zip-filen), men markera de `.xtml`-filer
  i `mallar/` som du vill ha.
- **Kopiera:** lägg filerna i mappen `SBC\WebEditor\Templates\` under Windows gemensamma
  programdatamapp, normalt `C:\ProgramData\SBC\WebEditor\Templates\`. Web8 läser egna mallar från
  den mappen; en mall som ligger någon annanstans visas inte i biblioteket.

> **Ännu inte verifierat:** om Web8 måste startas om efter att filer kopierats in i mappen.
> Hjälpen säger inget om det. Tills vidare: starta om Web8 efter kopiering.

*Källa: Web Editor 8-hjälpen, Dialog Boxes, s. 9 (Create Template: File name). Sökvägen
`C:\ProgramData\SBC\WebEditor\Templates\`: prövat.*

### 3.3 Uppdatera till en ny version

- Web8 hoppar över en mall som redan finns i biblioteket. Ta därför bort den gamla versionen
  först: markera mallarna i **Libraries**, högerklicka och välj **Delete**. Importera sedan den
  nya versionen.
- Bara egna mallar kan tas bort; Web8:s inbyggda mallar är skrivskyddade.

> **Ännu inte verifierat:** om instanser som redan är placerade i en vy följer med när mallen
> byts ut. Hjälpen säger inget. Projektfilerna tyder på att varje instans är en fristående kopia.
> Tills vidare: räkna med att placera om symbolen och göra om bytet i
> [avsnitt 5.2](#52-koppla-symbolen-till-objektet).

*Källa: Web Editor 8-hjälpen, Windows, s. 25–26 (Delete, Import...); Introduction, s. 6.*

## 4. Förbered Web8-projektet

### 4.1 Koppla projektet till PG5-devicen

Öppna **Project Options** (dubbelklicka på projektet i fönstret **Solution**) och fyll i
**Symbol data source** med PG5-projektets device-katalog. Web8 behöver den för att slå upp
symbolerna. Utan den kan Web8 inte heller erbjuda grupperna från PG5-programmet vid bytet i
[avsnitt 5.2](#52-koppla-symbolen-till-objektet).

*Källa: Web Editor 8-hjälpen, Dialog Boxes, s. 12–13 (Project Options: Configuration, Symbol data
source) och s. 28 (Select Group for Find and Replace).*

### 4.2 Sätt författarnamnet

Web8 lägger in författaruppgifterna i varje bygge. De går att läsa i den färdiga
visualiseringen som byggkonstanter (`?S_BC_AuthorInfoName`, `?S_BC_AuthorInfoCompany` med
flera). Byggda GUI:n på PCD:n bär dessutom namnet i filen `$$DeploymentInfo.dat`. Sätt därför
företagets namn, inte en persons, innan du bygger:

1. **Tools > Options > Author Information**: standardförfattare. Gäller alla lösningar som skapas
   därefter, om inte lösningen anger något annat.
2. **Project > Solution Options > Author Information**: för en lösning som redan finns. Kryssa i
   rutan och fyll i formuläret; det skriver över standardvärdet för den lösningen.

*Källa: Web Editor 8-hjälpen, Dialog Boxes, s. 25–27 (Solution Options: Solution Author
Information; Web Editor Options: Author Information); Data Sources, s. 6 (Build Time Constants).
Filen `$$DeploymentInfo.dat`: prövat; hjälpen nämner den inte.*

### 4.3 Den globala variabeln valdSymbol

Symbolerna och faceplates samarbetar genom en intern variabel, `valdSymbol`:

- Klick på en symbol skriver symbolens grupp, till exempel `A.MWSE.VS01.P01.`, till
  `valdSymbol`.
- Varje faceplate är dold och visar sig bara när `valdSymbol` är lika med dess egen grupp.
- Den valda symbolen får en ljus markering: en tunn blå ram med ljus bakgrund.

Variabeln är deklarerad i mallarna och behöver ingen deklaration eller startvärde i projektet.
Innan någon symbol har klickats har den inget värde, och då är alla faceplates dolda. Interna
variabler lever bara i webbläsarens körning och nollställs när visualiseringen laddas. Använd
inte namnet `valdSymbol` till något annat i projektet.

Klicket använder instruktionen Toggle med de två värdena `<grupp>` och tomt. Klick på samma
symbol igen tömmer alltså variabeln och stänger panelen. Klick på en annan symbol sätter dess
grupp, eftersom Toggle börjar på det första värdet när variabeln inte har något av de två.

Panelknapp fungerar likadant, för de faceplates som ingen symbol öppnar
([5.8](#58-statiska-symboler-och-panelknapp)). Inställningsblock, statiska symboler och
sidmallar använder inte `valdSymbol`. Sidmallarna har två egna delade variabler, `visaMeny` och
`sidTitel` ([avsnitt 7](#7-sidmallar)).

*Källa: Web Editor 8-hjälpen, Data Sources, s. 1 (Internal Variables); Variable Editors, s. 3–4
(Internal Variables Editor); Windows, s. 7 (instruktionen Toggle). Att varje symbol öppnar bara
sin egen panel: prövat 2026-10-23.*

### 4.4 Format på PCD-variabler

Mallarna deklarerar ett format för varje signal som har decimaler, tid eller datum. Formatet
styr hur ett värde visas och hur en inmatningsruta skriver det till PCD:n. Signaler utan format
(lägen, flaggor, antal, enheter och val i listor) visas och skrivs som heltal.

| Format | Signaler | Grund |
|---|---|---|
| `DEC.1` | `MV_Ut`, ventilens `Min`, `Max` och `ManV`, larmgränserna `HLLarmGrL`, `HLLarmGrH`, `GfLarmGrL`, `GfLarmGrH`, `LarmGrAvv`, kurvans `BPX1`–`BPX5` och `BPY1`–`BPY5`, regulatorns `ActVal`, `SetPt`, `YOut`, `IntTime`, `Offset`, pumpstartens `StartGrD`, `StartGr`, `StoppGr` | Labbets GUI; prövat mot PCD |
| `DEC.2` | PI- och PID-regulatorns P-faktor `PFac00` | FBox-makrot; prövat mot PCD 2026-10-25 (se nedan) |
| `DEC` | `DTtid`, `DTLarmGr`, `DTLarmF`, `StartF_tid`, `StoppF_tid` | Labbets GUI; prövat mot PCD |
| `DD.MM.YY` | `DTdatum` | Labbets GUI; prövat mot PCD |
| `HH:MM:SS`, `DD.MM.YY` | Topplistens klocka `S.HVC.Clock.HHMMSS` och `S.HVC.Clock.YYYYMMDD` | Labbets GUI; prövat mot PCD 2026-10-25 |
| `DEC` | `StartF`, `StoppF` | Slutsats ur FBox-makrot; prövat mot PCD 2026-10-25 |
| `DEC.1` | Skalningen `BPX1`–`BPY2`, `Filtertid` och `Kalibrering`; Givarhandinställningens `ManV`; regulatorernas `PFac`, `PtIni`, `LimLow`, `LimHigh`, `ManVal` | Slutsats ur FBox-makrot; prövat mot PCD 2026-10-25 |
| `DEC` | `LarmF_O`, `LarmF_S`, `GångtidÖ`, `GångtidM`, Drifttidsinställningens `LarmGr` och `AntalTimmar` | Slutsats: hela sekunder eller timmar i FBox-makrots inställningsfönster |
| `DEC.1` | Kurvornas punkt 6–10, `YminV`, `YmaxV` och de sänkta värdena `BPY1n`–`BPY10n`; larmgränserna `LGr`, `HGr`, `LarmGrL`, `LarmGrH`; PID-regulatorns `DerTime`, `FT1Time`; `BeräknVärde`, `Dödzon`; Verkningsgradens `PV_Ut`; handvärdena `ManV` (Handpanel analog) och `V_Ut` (Handstatus analog) | Slutsats: tiondelar i FBox-makrot, eller samma slags värde som `MV_Ut` |
| `HH:MM` | Tidkanalernas tider | Slutsats: registret håller TTMM (07:30 = 730), och Web8:s körning visar och skriver det så |

**P-faktorn i PI- och PID-regulatorn har två decimaler.** FBoxen anger P-faktorn i
hundradelar (`Yp = Xw·Fp/100`), så `PFac00` = 100 betyder 1,00 % per K och visas som `1.00`.
Labbets GUI visar den med en decimal, alltså som `10.0`, tio gånger för stort; mallarna rättar
det. Har ditt projekt redan deklarerat `PFac00` med ett annat format i **PCD Variables Editor**,
gäller projektets format före mallens. Ändra det då till `DEC.2`. P-regulatorns `PFac` är
regulatorns egen inställning i tiondelar och behåller `DEC.1`. Skalan är läst ur FBox-makrot;
att `PFac` i PI-regulatorn följer `PFac00` 1:1: prövat mot PCD 2026-10-25. Att projektets format
går före mallens, och att mallens `DEC.2` gäller utan egen deklaration: prövat i ett testbygge
2026-10-26.

> **Ännu inte verifierat:** formaten med grunden *Slutsats* som inte är prövade mot PCD.
> Labbprogrammet har ingen av FBoxarna bakom dem (kurvor utöver Börvärdeskurva 5, larm,
> tidkanaler, spjäll, handstyrning, Beräknat ärvärde, Verkningsgrad, Regulator PID). Tills
> vidare: kontrollera efter nedladdning att ett inskrivet värde, till exempel 22.5, läses tillbaka
> med rätt decimaler, och ändra formatet i **PCD Variables Editor** om det inte gör det.

> **Varning:** om samma PCD-variabel används någonstans i projektet utan format kan Web8 tappa
> mallens format. En inmatningsruta skriver då 22.5 som 22. Deklarera därför formatet för
> variabeln i **PCD Variables Editor** (kolumnen **Format**), eller koppla variabeln för hand bara
> med formatet satt. Iakttaget i ett testbygge 2026-10-23; ingen manual beskriver det.

*Källa: Web Editor 8-hjälpen, Variable Editors, s. 1–2 (PCD Variables Editor: Format). Formaten
är lästa ur mallfilerna; grunden för de nya formaten är läst ur FBox-makrona i
`MWSEFamilyAllmant.lib` och deras inställningsfönster (`MWSE_V25_10_003`).*

## 5. Placera en symbol och koppla den

### 5.1 Placera

1. Öppna vyn och välj det lager symbolen ska ligga i (se [6.1](#61-lager-för-faceplates) för
   lagerordningen).
2. Dubbelklicka på mallen i **Libraries**, eller dra den till vyn. Den hamnar i vyns övre vänstra
   hörn; dra den till rätt plats.
3. Använder mallen PCD-variabler öppnar Web8 dialogen **Cross-Reference** direkt, och om
   PG5-programmet har passande grupper även **Find and Replace Template Group**. Koppla mallen
   enligt [5.2](#52-koppla-symbolen-till-objektet).

*Källa: Web Editor 8-hjälpen, Windows, s. 24 (Using a template); How To, s. 9.*

### 5.2 Koppla symbolen till objektet

Mallen kopplas genom att platshållaren `A.MWSE.Namn.` byts mot objektets grupp överallt i
instansen. Det gäller dels PCD-variablerna, dels texter som bär gruppen: villkoren för vald
symbol och synlig panel, klickets värde och panelens taggrad. Dialogen **Find and Replace Template
Group** byter bara PCD-variablerna. Texterna byts först med **Replace All**, så båda stegen behövs:

1. Om **Find and Replace Template Group** inte öppnades vid placeringen: markera instansen,
   öppna **Cross-Reference** (högerklick > **Cross-reference**) och klicka **Find and Replace
   Template Group**.
2. Välj objektets grupp i listan, till exempel `VS01.P01`, och bekräfta. PCD-variablerna byts,
   och fälten **Find what** och **Replace with** i Cross-Reference fylls med den gamla och den nya
   gruppen.
3. Ta bort alla filter i Cross-Reference, så att alla rader syns. **Replace All** ändrar bara de
   rader som syns.
4. Klicka **Replace All**. Nu byts även texterna. Ändrade rader visas i fetstil.
5. Klicka **OK** för att spara. **Cancel** kastar ändringarna.

Erbjuder dialogen inte gruppen, till exempel för att symbolen ännu inte finns i PG5-programmet,
skriv i stället själv i **Find what**: `A.MWSE.Namn.` och i **Replace with**: objektets grupp,
till exempel `A.MWSE.VS01.P01.`, och fortsätt från steg 3. Skriv alltid hela prefixet, med
punkterna. Jämförelsen skiljer inte på stora och små bokstäver, så bara `Namn` skulle även
träffa namnetiketten (texten `Namn`) och den interna variabeln `doljNamn`. Samma handskrivna väg
gäller Panelknapp, som saknar PCD-variabler, och Roterande värmeväxlare, som har två grupper
([5.7](#57-roterande-värmeväxlare-två-grupper)).

Kontrollera bytet: skriv `A.MWSE.Namn` i fältet **Value Filter**. Inga rader ska finnas kvar.

*Källa: Web Editor 8-hjälpen, Dialog Boxes, s. 5–8 (Cross-Reference: Filtering, Find and
Replace, Find and Replace Template Group button) och s. 28 (Select Group for Find and Replace);
Introduction, s. 13. Att dialogen bara byter PCD-variablerna, och att Replace All därefter byter
texterna: prövat 2026-10-23.*

### 5.3 Kontrollera bytet

Efter bytet ska följande gälla, här för gruppen `A.MWSE.VS01.P01.`. Rätta för hand om något står
kvar som `A.MWSE.Namn.`.

| Var | Vad som ska stå | Var du rättar |
|---|---|---|
| Alla PCD-variabler i Cross-Reference | `A.MWSE.VS01.P01.<signal>` | Cross-Reference |
| Symbolen: `Knapp`, händelsen MouseDown, Toggle | `valdSymbol` mellan `A.MWSE.VS01.P01.` och tomt | Fliken **Actions** i Properties |
| Symbolen: markeringen `Vald`, villkoret `Selected` | `valdSymbol` = `A.MWSE.VS01.P01.` | **Condition Editor** |
| Faceplate: panelgruppen, villkoret `Vald` | `valdSymbol` = `A.MWSE.VS01.P01.` | **Condition Editor** |
| Faceplate: taggraden | `A.MWSE.VS01.P01.` | Egenskapen **Text** |
| Faceplate och inställningsblock: knappar som skriver | målet `A.MWSE.VS01.P01.<signal>` | Fliken **Actions** i Properties |

Efter bygget visar **PCD Variables Editor** röda rader för variabler som inte finns i
PG5-programmet ([avsnitt 8](#8-bygga-och-ladda-ned)). En röd rad betyder oftast ett felskrivet
gruppnamn.

*Källa: Web Editor 8-hjälpen, Windows, s. 14–18 (Condition Editor) och s. 20–21 (Actions Tab);
How To, s. 5 (How to use Conditions); Variable Editors, s. 3 (färgerna i PCD Variables Editor).*

### 5.4 Namnetikett

Under symbolen står texten `Namn`; på statiska symboler står den ovanför bilden. Markera
textelementet `Namn` i instansen och skriv objektets namn i egenskapen **Text**, till exempel
`P01`. Etiketten är 80 px bred, så ett namn längre än ungefär 12 tecken klipps.

Mallen har en intern variabel `doljNamn`; värdet `1` döljer namnetiketten.

> **Ännu inte verifierat:** hur `doljNamn` sätts per instans i editorn. Hjälpen beskriver inte
> mallarnas lokala variabler, och i ett prov i editorn hittades inget enkelt sätt. Tills vidare:
> låt namnetiketten stå kvar, eller töm dess text.

### 5.5 Vad symbolen visar

| Typ | Vila | Drift/aktiv | Fel och larmindikering | Text vid symbolen |
|---|---|---|---|---|
| Pump, Fläkt, Motor, Kompressor | `R_Ut` = 0 | `R_Ut` = 1 | `LD_Ut` eller `LDT_Ut` | Okänd / Drift / Stopp |
| Spjäll, Brandspjäll | `R_Ut` = 0 | `R_Ut` = 1 | `LA_Ut` | Okänd / Öppet / Stängt |
| Tvåvägsventil, Trevägsventil | `MV_Ut` = 0 | `MV_Ut` > 0 | inget (FBoxen har ingen larmutgång) | värdet `MV_Ut` |
| Trepunktsventil | båda utgångarna 0 | `IncUt` eller `DecUt` = 1 | inget (FBoxen har ingen larmutgång) | värdet `BeräknVärde` |
| Givare, Utegivare, Differensgivare, Integrerad Givare | normalt | inget | `LG_Ut`, `LL_Ut` eller `HL_Ut` | värdet `MV_Ut` |
| Roterande värmeväxlare | `R_Ut` = 0 | `R_Ut` = 1 | `LD_Ut`, `LDT_Ut` eller låg verkningsgrad `VG.LAL_Ut` | verkningsgraden `VG.PV_Ut` |

Handindikeringen visas när `Man` = 1; Trepunktsventil har ingen, eftersom FBoxen saknar
handläge. Spjällen visar Öppet när `R_Ut` = 1, eftersom Spjällstyrnings `R_Ut` är manöversignalen
ut.

*Källa: MWSE FBox-hjälpen, sidorna Motor, Spjällstyrning, Utgång analog, Ingång analog larm och
Beräknat ärvärde, öka/minska (utgångarna Öka, Minska och KAL, ingen larmutgång), avsnitt
"A-symboler" och "Beskrivning"; MWSE FBox-hjälpen `verkningsgrad.html` (Verkningsgrad). Lägena för
pump, ventil och givare: prövat mot PCD 2026-10-23. Trepunktsventil och Roterande värmeväxlare:
prövat mot en simulerad PCD 2026-10-23; **Ännu inte verifierat** mot PCD, eftersom labbprogrammet
saknar Beräknat ärvärde och Verkningsgrad.*

### 5.6 Enhet på värdefält

Givare och ventiler visar sitt värde med en decimal. Enheten (till exempel `°C` eller `%`) är
inte en del av mallen. I Web8 sätts enheten per PCD-variabel: öppna projektets `.wvar`-fil, sidan
**PCD Variables Editor**, och fyll i kolumnen **Units** för `A.MWSE.<grupp>.MV_Ut` (för
Trepunktsventil `BeräknVärde`, för Roterande värmeväxlare `VG.PV_Ut`). Texten visas efter värdet i
webbläsaren. Flera rader kan markeras och få samma enhet på en gång. Kurvblockens signaler ska
däremot inte ha någon enhet ([6.9](#69-kurvblock)).

> **Ännu inte verifierat:** om ett nytt projekt visar någon enhet utan detta steg. I testmiljön
> visades `°C` bara för att projektet redan hade enheten satt. Tills vidare: sätt enheten för
> varje givare och ventil.

*Källa: Web Editor 8-hjälpen, Variable Editors, s. 1–2 (PCD Variables Editor: Format, Units).*

### 5.7 Roterande värmeväxlare: två grupper

`Roterande värmeväxlare` (kategori `MWSE\Symbol\Värmeväxlare`; bilden 70 × 210 px, med etikett
och värde 80 × 240 px) binder två FBoxar:

| Grupp i mallen | FBox | Signaler |
|---|---|---|
| `A.MWSE.Namn.` | Motor, för rotorn | `R_Ut`, `LD_Ut`, `LDT_Ut`, `Man` |
| `A.MWSE.Namn.VG.` | Verkningsgrad (`MWSE_VGRAD1`) | `PV_Ut` (verkningsgraden), `LAL_Ut` (låg verkningsgrad) |

Ge Verkningsgrad-FBoxen motorns namn plus `.VG`: motorn `LB01.VVX1`, verkningsgraden
`LB01.VVX1.VG`. Då byter ett och samma byte av `A.MWSE.Namn.` båda grupperna. Ge inte båda
FBoxarna samma namn: Motor och Verkningsgrad har båda en symbol som heter `Status`, och de skulle
sannolikt krocka (slutsats ur FBox-makrona).

Dialogen **Find and Replace Template Group** klarar inte en mall som refererar mer än en grupp.
Koppla därför så här:

1. Markera instansen och öppna **Cross-Reference**. Ta bort alla filter.
2. Skriv i **Find what**: `A.MWSE.Namn.` och i **Replace with**: motorns grupp, till exempel
   `A.MWSE.LB01.VVX1.`. Alternativt: högerklicka på motorns `R_Ut` och välj **Select Group for
   Find and Replace** (Ctrl+G), som fyller i fälten.
3. Klicka **Replace All** och sedan **OK**.
4. Kontrollera med **Value Filter** `A.MWSE.Namn` att inga rader finns kvar, och att
   verkningsgradens rader nu heter `A.MWSE.LB01.VVX1.VG.PV_Ut` och `...VG.LAL_Ut`.

Ett klick på symbolen öppnar den `Motorpanel` som är kopplad till motorns grupp. Symbolen har
ingen egen faceplate.

> **Ännu inte verifierat:** att Replace All i editorn byter båda grupperna i ett steg. Tills
> vidare: om `VG`-raderna står kvar, byt dem i ett andra steg med **Find what**
> `A.MWSE.Namn.VG.` och **Replace with** `A.MWSE.LB01.VVX1.VG.`.

*Källa: Web Editor-manualen, del 14, Cross-Reference: Replacing symbol group names in a template
("This procedure cannot be used if the template references more than one group") och Find and
Replace Template Group button; del 17, Select Group for Find and Replace dialog box; MWSE
FBox-hjälpen, sidan Motor och `verkningsgrad.html`. Signalerna under `VG`: lästa ur FBox-makrot
`MWSE_VGRAD1` i `MWSEFamilyAllmant.lib`.*

### 5.8 Statiska symboler och Panelknapp

**Statiska symboler** (kategori `MWSE\Symbol\Statisk`) har inga signaler, inga lägen och inget
klick. De behöver ingen koppling: placera, skriv namnet i `Namn` och dölj det vid behov med
`doljNamn` ([5.4](#54-namnetikett)). Bilderna har sin storlek från MWSE-Symbols:

| Mall | Storlek (px) |
|---|---|
| Värmebatteri, Kylbatteri, Kombibatteri, Filter | 40 × 40 |
| Radiator | 90 × 60 |
| Värmetank | 100 × 150 |
| Krets, Krets med bypass, Krets med backventil | 60 × 130 |
| Tank | 100 × 110 |
| Liten tank | 40 × 60 |
| Värmeväxlare X, Värmeväxlare V, Plattvärmeväxlare | 250 × 210 |

Etiketten ligger ovanför bilden, så mallen är 20 px högre än bilden och minst 80 px bred.

**Plattvärmeväxlare** är växlaren vatten–vatten, till exempel i en fjärrvärmecentral: primärsidan
ansluter till vänster och sekundärsidan till höger. Anslutningarna ligger på samma höjd som på
Värmeväxlare X, 20 och 190 px ner i bilden, så ledningarna kan dras rakt in.

**Kretsarna** är för batterier i en kanal. Centrera kretsen på batteriet och lägg kretsens övre
del exakt på batteriets underkant, så att de hänger ihop; lägg batteriet efter kretsen, så att det
ritas ovanpå. Ingen del av kretsen ska gå upp i kanalen. Dra returen tillbaka parallellt med
tilloppet, samma väg som tilloppet kom. En radiator
får ingen krets: dra tillopp och retur direkt till radiatorns ändar. Lägg reglerventilen på
den vågräta tilloppsledningen som går in i kretsens framledningsben, inte bredvid kretsen:
ventilernas bilder är ritade för vågrät ledning, och kretsens ben är lodräta.

**Panelknapp** (kategori `MWSE\Symbol\Knapp`, 250 × 48 px) öppnar den faceplate som är kopplad
till samma grupp, för de faceplates som ingen symbol öppnar: de tre regulatorpanelerna, Handpanel digital,
Handpanel analog och Digitalsignalpanel. Den fungerar som en symbol: klicket skriver gruppen till
`valdSymbol`, och knappen markeras som vald. Skriv knappens text i `Namn`, till exempel `Reg`.
Knappen har ingen pil: pilen betyder att man går till en annan vy, som i menyn. Lägg
panelknapparna i sidofältet under panelerna ([7.6](#76-placering-och-lagerordning)).

Panelknapp har inga PCD-variabler, så knappen **Find and Replace Template Group** är avstängd.
Koppla den med den handskrivna vägen i [5.2](#52-koppla-symbolen-till-objektet): **Find what**
`A.MWSE.Namn.`, **Replace with** panelens grupp, till exempel `A.MWSE.VS01.Reg.`, **Replace All**.

*Källa: Web Editor-manualen, del 14, Find and Replace Template Group button ("The button is
disabled ... if the template does not use PCD variables"). Storlekarna: lästa ur mallfilerna.
Att Panelknapp öppnar bara sin egen panel och att krysset stänger den: prövat mot en simulerad
PCD 2026-10-23.*

## 6. Faceplates och inställningsblock

En faceplate är en mall som ligger i vyn hela tiden men är dold tills dess objekt är valt. Den
visar sig bara när `valdSymbol` är lika med dess grupp; annars är den dold och tar inte emot
klick. Den stängs med krysset uppe till höger, eller med ett nytt klick på symbolen. Krysset
skriver ingenting till PCD:n.
Vyn behöver därför **en faceplate-instans per objekt**, kopplad till samma grupp som objektets
symbol eller Panelknapp. Alla faceplates är 250 × 350 px, så att de öppnas på samma plats
([7.6](#76-placering-och-lagerordning)).

Ett inställningsblock är alltid synligt ([6.6](#66-inställningsblock)).

*Källa: prövat 2026-10-23: varje symbol öppnar bara sin egen panel, och dolda paneler tar inte
emot klick.*

### 6.1 Lager för faceplates

1. Skapa ett nytt lager med **Edit > Layer > New**, till exempel med namnet `Paneler`.
2. Flytta det överst med **Edit > Layer > Move Up**, så att panelerna ritas ovanpå symbolerna.
   Det översta lagret är den första raden i fönstret **Layers**.
3. Lägg alla faceplates i det lagret. De kan ligga på samma plats, eftersom bara en i taget är
   synlig.

Har vyn en Navigeringsmeny ska den ligga ovanför panelerna ([7.6](#76-placering-och-lagerordning)).

*Källa: Web Editor 8-hjälpen, Menus, s. 2–3 (Edit > Layer); Windows, s. 5 (Layers Window); How
To, s. 4–5 (How to use Layers).*

### 6.2 Motorpanel

`Motorpanel` (kategori `MWSE\Panel\Motor`) används för alla objekt på MWSE-FBoxen Motor: Pump,
Fläkt, Motor, Kompressor och Roterande värmeväxlare. Så placerar och kopplar du en faceplate; de
andra görs likadant:

1. Välj lagret `Paneler` och placera panelen (som i [5.1](#51-placera)).
2. Dra den dit panelen ska visas.
3. Byt `A.MWSE.Namn.` mot objektets grupp, exakt som för symbolen
   ([5.2](#52-koppla-symbolen-till-objektet)), och kontrollera villkoret `Vald`, taggraden och
   knapparnas mål ([5.3](#53-kontrollera-bytet)).
4. Skriv panelens rubrik i textelementet `Namn`, till exempel `P01 - Cirkulationspump`.
5. Upprepa för varje objekt i vyn.

Motorpanel visar:

- **Taggrad:** gruppen, till exempel `A.MWSE.VS01.P01.`.
- **Driftfel** (`LD_Ut`) och **Drifttidslarm** (`LDT_Ut`): Normal eller Larm.
- **Läge:** Hand eller Auto (`Man`).
- **Handkommando:** Start och Stopp (`ManV`). I Auto är knapparna ritade som inaktiva.
- **Drifttid** (`DTtid`) och **Starter** (`DTstarter`).

Knapparna Hand, Auto, Start och Stopp **skriver** till PCD:n, till `Man` och `ManV`. Vill du
begränsa vem som får manövrera kan händelserna på knapparna begränsas till vissa användare eller
grupper på fliken **Actions**.

Taggraden står med avslutande punkt (`A.MWSE.VS01.P01.`) på alla faceplates, eftersom samma
Find and Replace byter raden. Raden är bara text och påverkar inte funktionen.

*Källa: MWSE FBox-hjälpen, sidan Motor, avsnitt "A-symboler" (Man: indikering handkörning, ManV:
värde vid handkörning, DTtid, DTstarter, LD_Ut, LDT_Ut); Web Editor 8-hjälpen, Windows, s. 21
(Actions Tab, åtkomstbegränsning). Att knapparna skriver bara objektets egna `Man` och `ManV`:
prövat mot PCD 2026-10-23.*

### 6.3 Givarpanel

`Givarpanel` (kategori `MWSE\Panel\Givare`) används för alla objekt på FBoxen Ingång, analog,
larm: Givare, Utegivare, Differensgivare och Integrerad Givare. Placering och koppling som för
Motorpanel ([6.2](#62-motorpanel)).

Panelen visar:

- **Taggrad** och rubrik som Motorpanel.
- **Mätvärde** (`MV_Ut`), med en decimal.
- **Höglarm** (`HL_Ut`), **Låglarm** (`LL_Ut`) och **Givarfel** (`LG_Ut`): Normal eller Larm.
- **Larmgränser:** Låg (`HLLarmGrL`) och Hög (`HLLarmGrH`).

Givarpanel **skriver ingenting** till PCD:n. Larmgränserna ändras i inställningsblocket
`Givarinställning` ([6.6](#66-inställningsblock)).

*Källa: MWSE FBox-hjälpen, sidan Ingång analog larm, avsnitt "A-symboler". Visningen av värde och
larm: prövat mot PCD 2026-10-23.*

### 6.4 Ventilpanel

`Ventilpanel` (kategori `MWSE\Panel\Ventil`) används för objekt på FBoxen Utgång, analog:
Tvåvägsventil och Trevägsventil. Placering och koppling som för Motorpanel
([6.2](#62-motorpanel)).

Panelen visar:

- **Taggrad** och rubrik som Motorpanel.
- **Utsignal:** stapel och värde för `MV_Ut`, mellan `Min` och `Max`.
- **Läge:** Hand eller Auto (`Man`).
- **Handvärde:** inmatningsruta för `ManV`. Den går att skriva i bara i läge Hand; i Auto är den
  ritad som inaktiv och spärrad med egenskapen `DisableEvents`, samma spärr som gör en dold
  faceplate okänslig för klick ([avsnitt 6](#6-faceplates-och-inställningsblock)).

Knapparna Hand och Auto och rutan Handvärde **skriver** till PCD:n, till `Man` och `ManV`.
Åtkomst begränsas som för Motorpanel.

*Källa: MWSE FBox-hjälpen, sidan Utgång analog, avsnitt "A-symboler" (Min och Max: lägsta och
högsta värde vid skalering, Man, ManV, MV_Ut). Att Hand, Auto och Handvärde skriver bara
objektets egna `Man` och `ManV`: prövat mot PCD 2026-10-23.*

### 6.5 Fler faceplates

Placering och koppling som för Motorpanel ([6.2](#62-motorpanel)). Alla har taggrad, rubrik i
`Namn` och kryss.

| Mall | Kategori | Öppnas av | Visar | Skriver | Storlek (px) |
|---|---|---|---|---|---|
| Spjällpanel | `MWSE\Panel\Spjäll` | Spjäll, Brandspjäll | Lägesfel (`LA_Ut`), Läge Hand/Auto (`Man`), Handkommando Öppna/Stäng (`ManV`) | `Man`, `ManV` | 250 × 350 |
| Trepunktsventilpanel | `MWSE\Panel\Ventil` | Trepunktsventil | Beräknat ärvärde som värde och stapel 0–100 % (`BeräknVärde`), Manöver: Ökar, Minskar, Kalibrerar eller Stilla (`IncUt`, `DecUt`, `KalUt`) | inget | 250 × 350 |
| Regulatorpanel | `MWSE\Panel\Regulator` | Panelknapp | Mätvärde (`ActVal`), Börvärde (`SetPt`), Utsignal (`YOut`), Läge Hand/Auto (`Mode`), Handvärde (`ManVal`), P-faktor (`PFac00`), I-tid (`IntTime`) | `Mode`, `ManVal`, `PFac00`, `IntTime` | 250 × 350 |
| PID-regulatorpanel | `MWSE\Panel\Regulator` | Panelknapp | Som Regulatorpanel, och D-tid (`DerTime`) | `Mode`, `ManVal`, `PFac00`, `IntTime`, `DerTime` | 250 × 350 |
| P-regulatorpanel | `MWSE\Panel\Regulator` | Panelknapp | Mätvärde, Börvärde och Utsignal som Regulatorpanel, Läge, Handvärde, P-faktor (`PFac`), Arbetspunkt (`PtIni`) | `Mode`, `ManVal`, `PFac`, `PtIni` | 250 × 350 |
| Handpanel digital | `MWSE\Panel\Hand` | Panelknapp | Läge Hand/Auto (`Man`), Utsignal Till/Från (`ManV`) | `Man`, `ManV` | 250 × 350 |
| Handpanel analog | `MWSE\Panel\Hand` | Panelknapp | Läge Hand/Auto (`Man`), Utsignal som inmatningsruta (`ManV`) | `Man`, `ManV` | 250 × 350 |
| Digitalsignalpanel | `MWSE\Panel\Hand` | Panelknapp | Läge och Utsignal som Handpanel digital, Invertering, till- och frånslagsfördröjning med enhet | `Man`, `ManV`, `Invertering`, `StartF_tid`, `StartF_enhet`, `StoppF_tid`, `StoppF_enhet` | 250 × 350 |

Binda och använda:

- **En regulatorpanel per regulator-FBox:** Regulatorpanel för Regulator PI (`MWSE_heaPI3`),
  PID-regulatorpanel för Regulator PID (`MWSE_heaPID3`) och P-regulatorpanel för Regulator P
  (`MWSE_heaPP2`). Välj den som hör till FBoxen; på en annan regulator saknas symbolerna för
  inställningarna. Koppla panelen och en Panelknapp till regulatorns grupp, till exempel
  `A.MWSE.VS01.Reg.`.
- **Börvärdet går inte att ändra i panelen.** `SetPt` är FBoxens ingång W, så panelen visar det
  bara. Ändra det där det kommer ifrån, till exempel i börvärdeskurvan
  ([6.9](#69-kurvblock)) eller i det register som är kopplat till ingången W (slutsats ur
  FBox-makrot: `SetPt EQU cons`, där `cons` är ingången W).
- **Läge och Handvärde** fungerar som i Ventilpanel: handvärdet går att skriva i bara i Hand.
  Det är regulatorns handläge; ventilens eget handläge styrs fortfarande i Ventilpanel. FBoxen
  återställer `Mode` till Auto vid omstart (slutsats ur FBox-makrot).
- **Inställningarna** är samma symboler som i inställningsblocket och har samma format;
  P-faktorn `PFac00` har två decimaler ([4.4](#44-format-på-pcd-variabler)). Offset och PID:ns
  filtertid finns bara i inställningsblocket ([6.6](#66-inställningsblock),
  [6.7](#67-fler-inställningsblock-och-statusblock)).
- **Handpanel digital och analog** gäller alla tre varianterna av Auto/Hand, digital
  (`MWSE_AHdi`, `MWSE_AHdigS`, `MWSE_AHdigS2`) respektive analog (`MWSE_AHan`, `MWSE_AHanaS`,
  `MWSE_AHanaS2`). I Auto kopierar FBoxen sin ingång till `ManV`, så rutan och knapparna visar
  utsignalen i båda lägena (slutsats ur FBox-makrot). Utsignalen går att ändra bara i Hand; i
  Auto är den ritad som inaktiv.
- **Trepunktsventilpanel** skriver ingenting: FBoxen har inget handläge. Ge inte en Utgång,
  analog och en Beräknat ärvärde samma FBox-namn; då öppnas Ventilpanel och Trepunktsventilpanel
  samtidigt.
- Enheten i Digitalsignalpanels listor är s, min eller h (sekunder, minuter, timmar).

**Prövat mot PCD 2026-10-25:** Regulatorpanel på labbets PI-regulator `VS01.Reg`, i den tidigare
utformningen med bara utsignal, Läge och Handvärde. Värdena visades med rätt decimaler, och
skrivningarna nådde bara regulatorns egna symboler. De tre regulatorpanelerna i nuvarande
utformning är prövade mot en simulerad PCD 2026-10-26: varje skrivning nådde bara sin egen symbol,
och P-faktorn 1,25 skrevs som 125.

> **Ännu inte verifierat** på PCD: Spjällpanel, Trepunktsventilpanel, Handpanel digital och
> analog, Digitalsignalpanel och regulatorpanelerna i nuvarande utformning. Labbprogrammet har ingen Spjällstyrning, Beräknat ärvärde,
> Auto/Hand- eller Digital signal-FBox; de är prövade bara mot en simulerad PCD. Om PG5 alls
> tillåter samma FBox-namn på en Utgång, analog och en Beräknat ärvärde är inte heller prövat.
> Tills vidare: ge varje FBox ett eget namn, och prova knapparna i en anläggning som inte är i
> drift.

*Källa: MWSE FBox-hjälpen, sidorna Spjällstyrning (Part 15–16), Beräknat ärvärde, öka/minska
(Part 02), Auto/Hand, analog, analog 2, analog S, digital, digital 2 och digital S (Part 01–02)
och Digital signal (Part 10), avsnitt "A-symboler". Regulator PI, PID och P (Part 15): hjälpen
listar inte A-symbolerna; namnen och beteendet i Auto och Hand är lästa ur FBox-makrona i
`MWSEFamilyAllmant.lib`; att `SetPt` är ingången W: makrot `MWSE_hea_reg_pid` (`SetPt EQU cons`).
Visningen och vad knapparna skriver: prövat mot en simulerad PCD 2026-10-23 och, för
regulatorpanelerna, 2026-10-26; för Regulatorpanel mot PCD 2026-10-25.*

### 6.6 Inställningsblock

Inställningsblocken visar och ändrar en FBox inställningar. De har ingen koppling till
`valdSymbol` och är alltid synliga, så de läggs i ett vanligt lager, gärna i en egen vy för
inställningar. Placera ett block som en faceplate ([6.2](#62-motorpanel)), byt `A.MWSE.Namn.` mot
FBoxens grupp med samma Find and Replace ([5.2](#52-koppla-symbolen-till-objektet)) och skriv
blockets rubrik i textelementet `Namn`, till exempel `GT11 - Tilloppsgivare`.

| Mall | Kategori | Grupp, till exempel | Visar | Skriver | Storlek (px) |
|---|---|---|---|---|---|
| Givarinställning | `MWSE\Inställning\Givare` | givarens, `A.MWSE.VS01.GT11.` | – | Larmgräns låg och hög (`HLLarmGrL`, `HLLarmGrH`), fördröjning och enhet (`HLLarmF`, `HLLarmFenhet`), givarfel låg och hög (`GfLarmGrL`, `GfLarmGrH`) och dess fördröjning (`GfLarmF`) | 310 × 380 |
| Motorinställning | `MWSE\Inställning\Motor` | motorns, `A.MWSE.VS01.P01.` | Drifttimmar (`DTtid`), antal starter (`DTstarter`), senast återställd (`DTdatum`) | Larmgräns och fördröjning för drifttid (`DTLarmGr`, `DTLarmF`); knappen Återställ skriver 1 till `DTreset` | 310 × 380 |
| Regleravvikelseinställning | `MWSE\Inställning\Regleravvikelse` | `A.MWSE.VS01.RegAvv.` | – | Larmgräns (`LarmGrAvv`), fördröjning och enhet (`Fördr`, `TidsEnh`) | 310 × 220 |
| Regulatorinställning | `MWSE\Inställning\Regulator` | `A.MWSE.VS01.Reg.` | Mätvärde (`ActVal`), börvärde (`SetPt`), utsignal (`YOut`) | P-faktor (`PFac00`), I-tid (`IntTime`), offset (`Offset`) | 310 × 460 |
| Pumpstartinställning | `MWSE\Inställning\Pumpstart` | `A.MWSE.VS01.Pumpstart.` | – | Startgräns direkt (`StartGrD`), startgräns och stoppgräns fördröjd (`StartGr`, `StoppGr`), fördröjningarna och deras enheter (`StartF_tid`, `StartF_enhet`, `StoppF_tid`, `StoppF_enhet`) | 310 × 460 |

Utom knappen Återställ är allt i kolumnen Skriver inmatningsrutor eller listor som skriver
direkt till PCD:n. Listorna
för enhet har valen Sekunder, Minuter och Timmar. Knappen Återställ nollställer drifttimmar och
starter. Kurvblocken, också Kurvinställning för Börvärdeskurva 5, står i
[6.9](#69-kurvblock). Kontrollera formaten enligt [4.4](#44-format-på-pcd-variabler) innan du låter någon skriva i
blocken. Regulatorinställningens P-faktor visas tio gånger den verkliga förstärkningen i % per K
([4.4](#44-format-på-pcd-variabler)).

*Källa: MWSE FBox-hjälpen, avsnitt "A-symboler" på sidorna Ingång analog larm, Motor (DTreset:
reset av drifttimmar och starter), Larm regleravvikelse och Pumpstart. Regulator PI: hjälpen listar inte
A-symbolerna; namnen är lästa ur FBox-makrot i `MWSEFamilyAllmant.lib` (`MWSE_V25_10_003`) och
prövade. Att blocken visar PCD:ns värden med rätt decimaler: prövat mot PCD 2026-10-23. Att varje
block skriver bara till sin egen grupp: prövat mot en simulerad PCD 2026-10-23, och för
Regulatorinställning mot PCD 2026-10-25.*

### 6.7 Fler inställningsblock och statusblock

Placering och koppling som i [6.6](#66-inställningsblock). Allt som går att skriva i, välja i
eller trycka på skriver direkt till PCD:n. Signalerna står i
[avsnitt 10](#10-mallar-och-fbox-signaler).

**Motor, givare, ventil och spjäll**

| Mall | Kategori | FBox | Innehåll | Storlek (px) |
|---|---|---|---|---|
| Motorfördröjningsinställning | `MWSE\Inställning\Motor` | Motor | Startfördröjning och stoppfördröjning (s) | 310 × 220 |
| Givarskalningsinställning | `MWSE\Inställning\Givare` | Ingång, analog, larm | Filtertid (s), kalibrering, skalning invärde och utvärde, min och max | 310 × 540 |
| Givarhandinställning | `MWSE\Inställning\Givare` | Ingång, analog, larm | Läge Auto/Hand och handvärde | 310 × 220 |
| Ventilskalningsinställning | `MWSE\Inställning\Ventil` | Utgång, analog | Skalning invärde och utvärde, min och max | 310 × 380 |
| Trepunktsventilinställning | `MWSE\Inställning\Ventil` | Beräknat ärvärde, öka/minska | Gångtid min till max och max till min (s), dödzon (%) | 310 × 300 |
| Spjällinställning | `MWSE\Inställning\Spjäll` | Spjällstyrning | Larmfördröjning öppna och stäng (s) | 310 × 220 |

- **Etiketterna i skalningen följer registren, inte A-symbolernas namn.** FBoxarna lägger
  `BPX1`, `BPX2`, `BPY1` och `BPY2` på fyra register i ordningen X1, Y1, X2, Y2. `BPX2` håller
  alltså Y1 och `BPY1` håller X2. Etiketten *Skalning, utvärde min (Y1)* är därför bunden till
  `BPX2`, och *Skalning, invärde max (X2)* till `BPY1`. Byt inte bindningarna. Det gäller både
  Givarskalnings- och Ventilskalningsinställning. Läst ur FBox-makrona, deras inställningstexter
  och labbprogrammets länkkarta; prövat mot PCD 2026-10-25 på ventilen SV21.
- **Givarhandinställning** handstyr givarens värde: i Hand är `MV_Ut` handvärdet, inte
  mätvärdet. Placera blocket bara där någon ska få göra det.

*Källa: MWSE FBox-hjälpen, sidorna Motor (Part 14), Ingång, analog, larm (Part 11), Utgång,
analog (Part 21), Beräknat ärvärde, öka/minska (Part 02) och Spjällstyrning (Part 16), avsnitt
"A-symboler" och "Inställningsparametrar". Storlekarna: lästa ur mallfilerna.*

**Tidkanaler**

| Mall | FBox | Innehåll | Storlek (px) |
|---|---|---|---|
| Veckoschemainställning | Tidkanal m. referens (`MWSE_TKref`) eller m. ingångar (`MWSE_TKing`) | Till och Från för måndag–söndag, dag före helg och helgdag | 640 × 380 |
| Veckodagsinställning | Tidkanal, veckodag (`MWSE_TKM`) | Dag, Tid till, Tid från | 310 × 220 |
| Veckopulsinställning | Veckopuls (`MWSE_Vpuls`) | Dag, Tid till, Pulslängd, Enhet | 310 × 220 |

- Tiderna skrivs som TT:MM, till exempel 07:30. Formatet `HH:MM` är en slutsats
  ([4.4](#44-format-på-pcd-variabler)).
- Veckodagsinställningens Dag har valen måndag–söndag och Permanent från. Permanent stänger av
  tidkanalen.
- Veckopulsinställningens Dag har valen måndag–söndag och Alla dagar; Enhet har 1/10 sekunder,
  Sekunder, Minuter och Timmar. Texten *1/10 sekunder* går ända fram till listans pil.

*Källa: MWSE FBox-hjälpen, sidorna Tidkanal m. referens och Tidkanal m. ingångar (Part 17–18),
Tidkanal, veckodag (Part 20; hjälpen listar inga A-symboler, namnen är lästa ur FBox-makrot) och
Veckopuls (Part 21).*

**Larm**

| Mall | FBox | Innehåll | Storlek (px) |
|---|---|---|---|
| Frysvaktsinställning | Larm, frysvakt (`MWSE_LFrV`) | Larmgräns, återställningsgräns, fördröjning med enhet | 310 × 300 |
| Hög-låglarminställning | Larm HögLåg (`MWSE_RHL`) | Låglarmgräns, höglarmgräns, fördröjning med enhet | 310 × 300 |
| Driftfelsinställning | Larm, driftfel (`MWSE_DRIFTFEL2`) | Fördröjning (s) | 310 × 140 |
| Drifttidsinställning | Larm, drifttid (`MWSE_heaCtW2`) | Larmgräns för drifttid; visar drifttimmar och antal starter; knappen Återställ | 310 × 270 |

- Driftfelets larmvärde (`LarmVal`) är en inställning på FBoxen när programmet byggs, inte ett
  värde i PCD:n, och finns inte i blocket (slutsats ur FBox-makrot).
- Drifttidsinställningens Återställ skriver till `ResetDT`. Hjälpen kallar signalen `Reset`, men
  FBox-makrot publicerar `ResetDT`.
- FBoxarnas larmutgångar visas inte i blocken.

*Källa: MWSE FBox-hjälpen, sidorna Larm, driftfel (Part 11) och Larm, drifttid, Larm HögLåg och
Larm, frysvakt (Part 12), avsnitt "A-symboler". `ResetDT` och `LarmVal`: lästa ur FBox-makrona i
`MWSEFamilyAllmant.lib`.*

**Regulatorer**

| Mall | FBox | Visar | Skriver | Storlek (px) |
|---|---|---|---|---|
| PID-regulatorinställning | Regulator PID (`MWSE_heaPID3`) | Mätvärde, börvärde, utsignal | P-faktor (`PFac00`), I-tid, D-tid, filtertid, offset | 310 × 490 |
| P-regulatorinställning | Regulator P (`MWSE_heaPP2`) | Mätvärde, börvärde, utsignal | P-faktor (`PFac`), arbetspunkt (`PtIni`) | 310 × 380 |

Hjälpen listar inte regulatorernas A-symboler; namnen är lästa ur FBox-makrot. Faceplates med
Auto/Hand och inställningarna: Regulatorpanel, PID-regulatorpanel och P-regulatorpanel
([6.5](#65-fler-faceplates)).

- **Koppla PID-regulatorinställning bara till en Regulator PID.** På en PI-regulator visar D-tid
  och filtertid (`DerTime`, `FT1Time`) `null`, eftersom PI-regulatorn inte har dem. Prövat mot
  PCD 2026-10-25.
- P-faktorn visas tio gånger den verkliga förstärkningen i % per K, i båda blocken
  ([4.4](#44-format-på-pcd-variabler)).

*Källa: MWSE FBox-hjälpen, sidorna Regulator PID och Regulator P (Part 15), versionshistoriken
(A-symboler skapas bara när FBoxen har ett namn); signalerna ur `MWSE_hea_reg_pid` i
`MWSEFamilyAllmant.lib`.*

**Status för handstyrning**

| Mall | FBox | Innehåll | Storlek (px) |
|---|---|---|---|
| Handsummering | Auto/Hand MNG (`MWSE_AH_MNG`) | Antal objekt i Hand (`AntalHand`); knappen Alla till Auto (`TillAuto`) | 310 × 170 |
| Handstatus analog | Auto/Hand, analog via R (`MWSE_MwStatI_`) | Läge och utsignal; S-värdena för Auto och Hand | 310 × 310 |
| Handstatus digital | Auto/Hand, digital via R (`MWSE_StatB`) | Läge och utsignal; S-värdena för Auto, Hand till och Hand från | 310 × 390 |

- **Alla till Auto** sätter alla anslutna Auto/Hand-FBoxar av version 2 i Auto. FBoxen nollställer
  `TillAuto` själv. Raden *Objekt i hand* visas i handfärgen när antalet är större än 0.
- I Handstatus jämför FBoxen ingången S med registervärdena; Läge visar Hand när utsignalen är
  handstyrd.

*Källa: MWSE FBox-hjälpen, sidorna Auto/Hand MNG, Analog AHstyrn via R och Digital AHstyrn via R
(Part 01–02), avsnitt "Sammanfattning" och "A-symboler". Att FBoxen nollställer `TillAuto`: läst
ur FBox-makrot.*

**Prövat mot PCD 2026-10-25** på labbets FBoxar: Givarskalningsinställning (GT11, GT41),
Givarhandinställning (GT11, GT31), Ventilskalningsinställning (SV21), Motorfördröjningsinställning
(P01) och P-regulatorinställning (`VS01.Reg`). Alla värden visades med rätt decimaler, och varje
skrivning nådde bara sin egen symbol.

> **Ännu inte verifierat** på PCD: övriga block i det här avsnittet. Labbprogrammet har ingen
> Beräknat ärvärde, Spjällstyrning, Regulator PID, tidkanal,
> larm-FBox eller Auto/Hand-FBox; blocken är prövade bara mot en simulerad PCD 2026-10-23. Tills
> vidare: kontrollera efter nedladdning att blocket visar FBoxens värden och att ett ändrat värde
> läses tillbaka rätt ([4.4](#44-format-på-pcd-variabler)).

### 6.8 Rekommenderat: avmarkera när vyn lämnas

Eftersom `valdSymbol` behåller sitt värde så länge visualiseringen är laddad
([4.3](#43-den-globala-variabeln-valdsymbol)), står en panel kvar öppen när man kommer
tillbaka till vyn. Töm variabeln när vyn lämnas och när man klickar på tom yta:

1. Markera vyn (klicka på tom yta) och öppna fliken **Actions** i **Properties**.
2. Välj händelsen **On view close** och åtgärden **Write**.
3. Lämna **Value** tomt och sätt **To destination** till den interna variabeln `valdSymbol`.
4. Ett valfritt bakgrundselement kan få samma åtgärd på musklick, så att klick på tom yta stänger
   panelen.

En vyhändelse behåller bara sin första instruktion i bygget ([7.3](#73-topplist-och-sidtitel)).
Behöver vyn fler åtgärder i samma händelse, lägg dem i en funktion.

*Källa: Web Editor 8-hjälpen, Windows, s. 7 (instruktionen Write) och s. 20–21 (Actions Tab).
Händelsens och fältens namn i editorn: prövat 2026-10-23.*

### 6.9 Kurvblock

Ett kurvblock visar och ändrar en börvärdeskurva: punkterna i en tabell och kurvan ritad bredvid,
i ett kort. Det finns ett block per kurv-FBox:

| Mall | FBox | Tabellen | Storlek (px) |
|---|---|---|---|
| Kurvinställning | Börvärdeskurva 5 (`MWSE_BvK5`) | Ute och Fram för punkt 1–5 | 788 × 382 |
| Kurvinställning 2, 3, 4, 6, 7, 8, 9, 10 | Börvärdeskurva 2–10 (`MWSE_BvK<n>`) | Ute och Fram per punkt | 788 × 382 (2–4), 788 × 432 (6), 788 × 482 (7), 932 × 382 (8–10) |
| Kurvinställning med sänkning 2, 5, 6, 7, 8, 9, 10 | Börvärdesk. m. sänkning (`MWSE_BvK<n>s`) | Ute, Fram och Sänkt per punkt | 788 × 382 (2, 5), 788 × 432 (6), 788 × 482 (7), 1004 × 382 (8–10) |

Kategorin är `MWSE\Inställning\Börvärdeskurva`. Placera och koppla blocket som de andra
inställningsblocken ([6.6](#66-inställningsblock)). Alla kurvblock får plats i en vy på 1020 × 600,
till exempel på (8, 104) under Topplist och en flikrad. MWSE har ingen kurva med sänkning för 3
eller 4 punkter.

**Tabellen**

- En rad per punkt: Ute °C (`BPX<p>`), Fram °C (`BPY<p>`) och, i kurvorna med sänkning, Sänkt °C
  (`BPY<p>n`). Upp till 7 punkter står i en tabell; 8–10 punkter fördelas på två tabeller bredvid
  varandra.
- Under tabellen står **Fram min** (`YminV`) och **Fram max** (`YmaxV`), kurvans lägsta och
  högsta tillåtna framledningstemperatur.
- Fälten skriver direkt till PCD:n. Brytpunkterna ska matas in i fallande ordning,
  X1 > X2 > … > X10.
- **Lämna kurvans signaler utan enhet.** Enheten står i kolumnrubrikerna. Fyll inte i **Units**
  för `BPX…`, `BPY…`, `YminV` och `YmaxV` i projektets PCD Variables Editor
  ([5.6](#56-enhet-på-värdefält)); då visas enheten en gång till i varje fält och vid axlarna.
- **Ställ kurvan här.** Regulatorns börvärde kommer från kurvan och går inte att ändra i
  regulatorns panel ([6.5](#65-fler-faceplates)).

**Grafen**

- Grafen är lika stor i alla kurvblock, 284 × 246 px, och står till höger om tabellen.
- Utetemperaturen går längs botten, fast från −30 till +20 °C. Framledningen går uppåt från Fram
  min till Fram max. Etiketterna vid ändarna visar Fram min och Fram max, och etiketten i mitten
  deras medelvärde, med en decimal.
- Kurvan är heldragen med punkter; den sänkta kurvan är streckad.
- **En punkt utanför axlarna döljs**, liksom kurvdelarna till och från den: ute under −30 eller
  över +20 °C, fram under Fram min eller över Fram max.
- **Är Fram max lika med eller lägre än Fram min ritas ingen kurva.** Bara rutnätet och
  etiketterna syns tills gränserna är rätt.
- Grafen ritas om inom ungefär en sekund när ett värde ändras.
- Grafen visar ingen aktuell driftpunkt. Kurv-FBoxarna publicerar inte sin ingång
  (utetemperaturen) eller utgång (börvärdet) som A-symboler.
- Mittetiketten räknas fram i blocket och förutsätter att gränserna är positiva (slutsats).

**Prövat** 2026-10-26 i HTML5-visningen mot en simulerad PCD: alla 16 block visas, och i
Kurvinställning 2 och 3 och med sänkning 5, 7 och 10 når varje skrivning bara sin egen symbol
och grafen ritas om.

> **Ännu inte verifierat:** mot PCD (version 2.0.0 är inte prövad på labbet), i TEQ och andra
> visningar än HTML5, och placering och Find and Replace i editorn. Tills vidare: kontrollera
> efter nedladdning att tabellen visar FBoxens värden och att kurvan står där tabellen säger.

*Källa: MWSE FBox-hjälpen, sidorna Börvärdeskurva 5 (`BvK5.html`), Börvärdeskurva 2–10 och
Börvärdesk. m. sänkning 2 och 5–10 (Part 03–09), avsnitten "Beskrivning" (fallande ordning) och
"A-symboler"; att `MWSE_BvK5` också publicerar `YminV` och `YmaxV`: testprogrammets symbolfil.
Grafen: Web Editor-manualen, Part 29 ("Event Box": On Refresh; "X/Y Offset" och "Width/Height
Offset") och Part 09 (räkneinstruktionerna). Enheten: Part 13 ("PCD Variables Editor": Units visas
efter värdet).*

## 7. Sidmallar

Sidmallarna bygger själva sidan: larmlista, larmsummering, topplist, meny, sidofält och
inloggning. De binder inte under `A.MWSE.Namn.`:

| Mall | Kategori | Storlek (px) | Kopplas till |
|---|---|---|---|
| Larmlista | `MWSE\Sida\Larm` | 1004 × 508 | Larmlistan i en Alarm PCD init-FBox, under `Namn.` |
| Larmsummering | `MWSE\Sida\Larm` | 104 × 48 | Antalet larm, symbolen `<grupp>.antal`, under `Namn.`; öppnar vyn `Larm` |
| Topplist | `MWSE\Sida\Navigering` | 1020 × 48 | Web8:s inloggningsvariabler, HEAVAC:s klocka, `sidTitel`, `visaMeny`; utloggningen öppnar vyn `login` |
| Navigeringsmeny | `MWSE\Sida\Navigering` | 1020 × 1032 | `visaMeny`; raderna öppnar vyerna `Start`, `Larm`, `Inställningar`, `Info` och `login` |
| Sidofält | `MWSE\Sida\Sidofält` | 250 × 552 | Inget; spalten där panelerna och panelknapparna ligger, med texten ”Ingen panel öppen” där panelerna öppnas tills en panel täcker den |
| Inloggning | `MWSE\Sida\Inloggning` | 360 × 296 | Projektets användardatabas |
| Bakgrund | `MWSE\Sida\Inloggning` | 1020 × 600 | Inget; bakgrundsbild under Inloggning |

Larmlista bygger på Saias Online Alarm List (generation 1), med MWSE:s färger och svenska texter.

Larmlista, Larmsummering, Topplist och Navigeringsmeny är prövade mot PCD 2026-10-25; Inloggning
bara mot en simulerad PCD. Vad som återstår står i varje avsnitt.

### 7.1 Koppla en sidmall

**Larmlista och Larmsummering** binder under `Namn.`, som byts mot larm-FBoxens namn med punkt.
Namnet ska ha **exakt samma stora och små bokstäver** som i PG5-programmet. Gör bytet för varje
sidmall för sig när namnen skiljer sig åt. I labbprogrammet heter larm-FBoxen `Larmlista`, men
antalssymbolen `LARMLISTA.antal`. Larmlista kopplas då till `Larmlista.` och Larmsummering till
`LARMLISTA.` ([7.2](#72-larmlista-och-larmsummering)).

**Vyerna som sidmallarna öppnar** står som vynamn i instruktionen Open View (ShowView). Har
projektet inte vyer med just de namnen, rikta om dem i fliken **Actions**: markera elementet,
välj händelsen och ändra vynamnet.

| Mall | Element | Vynamn i mallen |
|---|---|---|
| Larmsummering | `Knapp` (MouseUp) | `Larm` |
| Navigeringsmeny | raderna Översikt, Larm, Inställningar, Info, Inloggning, `Knapp` (MouseUp, funktionen `Navigera`) | `Start`, `Larm`, `Inställningar`, `Info`, `login` |
| Topplist | `Logga ut` (MouseUp, funktionen `Logout`) | `login` |

Bundna med exakt samma bokstäver fungerade båda på PCD:n: Larmlista mot `Larmlista` och
Larmsummering mot `LARMLISTA.antal` (prövat 2026-10-25).

> **Ännu inte verifierat:** om körningen misslyckas när bokstäverna skiljer sig, och att vynamnen
> går att rikta om i editorns Actions som beskrivet. På PCD:n var vyerna riktade om av ett skript,
> inte i editorn. Tills vidare: skriv namnen exakt som i PG5-programmet och i projektets vylista.

*Källa: Web Editor-manualen, del 8, Function instructions: Open View ("Jumps to the specified
view") och Jump Back. Namnen i labbprogrammet: lästa ur dess symbolfil och Fupla-sida.*

### 7.2 Larmlista och Larmsummering

**Larmlista** visar larmen i en larmlista från FBoxen Alarm PCD init, det senaste larmet överst.
Larm-FBoxen skapar symbolen `A.Alarm.<FBox-namn>.MyName`; mallen har `A.Alarm.Namn.MyName`.
Mallen är ritad som ett inställningsblock: ett kort med ett band överst som bär kolumnrubrikerna,
listan under bandet, bläddringspilarna till höger och en rad med tre knappar längst ner. Den får
plats under Topplist i en vy på 1020 × 600; placera den på (8, 56).

1. Placera `Larmlista` i larmvyn, den vy som Larmsummering och menyn öppnar.
2. Markera mallen, öppna fliken **Template** i Properties och sätt **Alarm List PPO Name** till
   larmlistans symbol, till exempel `A.Alarm.Larmlista.MyName`. Det går också att byta
   `A.Alarm.Namn.` mot `A.Alarm.Larmlista.` med **Replace All** i Cross-Reference.
3. Sätt **Alarm List Size** till **antalet larm i larmlistan plus 1** (mallen har 10). Har
   PCD:ns lista 10 larm, sätt 11. Den styr bara hur många texttaggar Web8 skapar: taggarna heter
   `_0` till `_<storlek−1>`, men listan hämtar texten för larm nummer 1 till N. Med storlek 10
   saknar larm 10 sin text och listan visar taggen. Hur många larm listan rymmer bestäms i
   PG5-programmet, se nedan.
4. Larmtexterna läses från en CSV-fil (**Alarm Texts From CSV File** är på). Bygg projektet en
   gång, så skapas taggarna, till exempel `A.Alarm.Larmlista.MyName_1`. Skriv larmtexten för
   tagg 1 till N i CSV-filen; tagg `_0` används inte. Utan text visar listan taggen.

   **Prövat** mot PCD:n 2026-10-26: med storlek 10 saknade larm 10 sin text, med 11 fanns den.

**Fler larm än tio.** Larmlistan finns i PCD:n och skapas av FBoxen Alarm PCD init. Dess
flaggfält (Flags, och Ack, Del och Text om de används) är fält i den lokala symboltabellen, och
fältets storlek är det största antalet larm. Labbets larmlista har `F1001[10]`, alltså tio. För
fler larm:

1. Gör fälten större i PG5, alla lika stora, till exempel `[30]`.
2. Koppla varje larm till listan med en FBox 1 alarm, 10 alarms eller 20 alarms. Varje larm får
   ett eget nummer (**Idx**) från 1 till fältets storlek.
3. Sätt **Alarm List Size** i Web8 till fältets storlek plus 1, bygg och skriv texterna för de
   nya taggarna.

*Källa: Alarming-FBoxarnas hjälp (`Alarming_en.chm`), sidorna Alarm PCD init ("The size of this
array defines the max. number of alarms") och 10 alarms ("The first index number is 1, and the
last is the Number of alarms entered into the Init FBox"); Web Editor 8-hjälpen, Library, Online
Alarm List, steg 4 (CSV-taggarna `{Alarm-PPO-Name}_[0..N]`).*

Kolumnerna heter ID, Larmtext, Kommande, Gående och Kvitt. Raderna är 38 px höga, så att en
rad går att välja med fingret, och en lång larmtext bryts till två rader. Knapparna:

- **Kvittera vald** kvitterar den valda raden, **Kvittera alla** alla rader. Gångna larm som
  kvitteras försvinner ur listan.
- **Rensa** tar bort larmen ur den aktuella larmlistan, så långt larm-FBoxen tillåter. Larmet
  finns kvar som larmtyp och kommer tillbaka som ny post nästa gång det löser ut. Sätt **System
  clear acceptance** på FBoxen Alarm PCD init till **Off** eller **Ack&Off**, så rensar knappen
  bara återgångna larm. Med **All** rensas även aktiva larm.

  *Källa: Alarming-FBoxarnas hjälp, sidan Alarm PCD init (System clear acceptance, Remove alarms
  automatically when); Web Editor 8-hjälpen, Library, Online Alarm List (Delete Alarm List).*

Sorteringen är **Sort** = Active Time Sorted (egenskapen `SortMode` = 1): det larm som senast
kom överst, efter Kommande-tiden. Aktiva larm sorteras inte före gångna; ett larm som kommer igen
flyttas upp. Filtret är **Filter Default Value** = Active or NAK Alarms (egenskapen
`FilterMode` = 3): listan visar bara aktiva larm och gångna larm som inte är kvitterade; normala
larm syns inte. Sorteringen och filtret görs i PCD:n, inte i webbläsaren.

**Prövat** mot PCD:n 2026-10-26.
Mallen har inga knappar för filter, sortering eller kolumner, som labbets förenklade larmlista. Ska kolumnerna flyttas, gör det i egenskapen **Columns** på fliken **Template**.

En rad i taget kan väljas. Kvitt-kolumnen visar körningens egna `NAK` och `ACK`, och den valda
raden ritas svart med vit text. Text i en rad står överst i raden. Allt detta kommer från Web8:s
körning, inte från mallens egenskaper.

**Larmsummering** visar antalet larm med en klocka, i normalfärg när antalet är 0 och i
larmfärg när det är större. Ett klick öppnar vyn `Larm`. Den läser symbolen `Namn.antal`:

1. I Fupla: koppla larm-FBoxens antal (utgången Num) till ett register med symbolen
   `<grupp>.antal`, till exempel `LARMLISTA.antal`.
2. Placera `Larmsummering` i varje vy, gärna i Topplist ([7.6](#76-placering-och-lagerordning)).
3. Byt `Namn.antal` mot `<grupp>.antal`: med **Find and Replace Template Group** om dialogen
   erbjuder gruppen, annars **Find what** `Namn.antal`, **Replace with** `LARMLISTA.antal`,
   **Replace All**.
4. Rikta om vyn `Larm` om larmvyn heter något annat ([7.1](#71-koppla-en-sidmall)).

**Prövat mot PCD 2026-10-25** (förra utförandet, med filter och sortering som knappar): larm som
löser ut och går tillbaka via ingångar visas och försvinner, `antal` följer (1, 2, 1) och larmens
status ändras efter Kvittera vald och Kvittera alla. Larmsummering räknar på `LARMLISTA.antal`,
visar larmläget över 0, och ett klick öppnar larmvyn. Det nya utförandet är prövat mot en
simulerad PCD 2026-10-26: listan begär sorteringen 1, och kvittering, radering och bläddring
fram och tillbaka fungerar.

> **Ännu inte verifierat:** det nya utförandet på PCD:n, däribland att PCD:n sorterar det senaste
> larmet överst och bläddrar i den ordningen; att Replace All i editorn når mallens egenskap
> Alarm List PPO Name; och Larmsummerings läge vid 0 larm, som inte syntes på PCD:n eftersom ett
> gammalt larm stod kvar. Tills vidare: sätt egenskapen direkt på fliken **Template** enligt steg
> 2, och kontrollera ordningen med två larm efter nedladdning.

*Källa: Web Editor-manualen, del 19, Online Alarm List: Configure The Template (Alarm List PPO
Name, till exempel `A.Alarm.ThisAlarmList.MyName`; symbolen skapas av larm-FBoxen och får dess
namn) och Configure Alarm Texts (CSV-taggen för varje larmtext har formen
`{Alarm-PPO-Name}_[0..N]`; bygg först för att skapa taggarna); del 18, Defining the Alarm Texts.
Sortering: del 19, Sort Mode ("Active Time Sorted: Show the newest alarm on top"); körningen
skickar valet till PCD:n i larmförfrågan. Kolumnerna: del 19, Configure Table Columns.
Utgången Num och `LARMLISTA.antal`: lästa ur labbprogrammet. `NAK`/`ACK` och den svarta raden:
prövat mot PCD 2026-10-25.*

### 7.3 Topplist och sidtitel

**Topplist** är sidans övre list, 1020 × 48 px: menyknapp, Tillbaka, sidtitel, inloggad användare,
klocka och Logga ut. Längst till höger, från x = 916, finns plats för Larmsummering.

- **Meny** växlar `visaMeny` mellan 1 och 0 och öppnar och stänger Navigeringsmeny.
- **Tillbaka** går till förra vyn (Jump Back).
- **Användare** visar den inloggade användaren, eller *Ej inloggad*.
- **Klockan** läser HEAVAC:s klocka, `S.HVC.Clock.HHMMSS` och `S.HVC.Clock.YYYYMMDD`. Ett program
  utan HEAVAC visar ingen tid.
- **Logga ut** loggar ut som Saias Logout Button och öppnar sedan vyn `login`.

**Sidtiteln** är den interna variabeln `sidTitel`. Är den tom visar Topplist vyns namn
(`@CURRENT_PAGE`). Så sätter du en egen titel:

1. Deklarera `sidTitel` under projektets variabler (**Project > Variables**). Utan deklaration
   tar bygget bort vyernas skrivning till den.
2. Lägg i varje vy en **Write** av titeln, till exempel `Översikt`, till `sidTitel` i händelsen
   **On view open** (i projektfilen `FocusGained`). Variabeln behåller sitt värde mellan vyerna,
   så en vy utan egen skrivning visar förra vyns titel.
3. En vyhändelse behåller bara sin **första** instruktion i bygget. Behöver vyn fler åtgärder i
   samma händelse, till exempel också tömma `valdSymbol`, lägg alla i en funktion och låt
   händelsen anropa den.

**Prövat mot PCD 2026-10-25:** klockan visar PCD:ns tid och datum (`03:07:50`, `25.10.26`), och
Användare visar *Ej inloggad* när ingen är inloggad.

> **Ännu inte verifierat:** om deklarationen ensam räcker för att skrivningen ska komma med, och
> menyvägen i editorn. Tills vidare: deklarera `sidTitel` och kontrollera titeln i webbläsaren
> efter bygget.

*Källa: Web Editor-manualen, del 3, Internal Variables: `@CURRENT_PAGE` ("Name of the current
TEQ view"); del 8, Function editor och Function instructions: Jump Back; del 29–30, Actions
Properties: On View Open; del 33, Logout Button. Klockans symboler och format: lästa ur labbets
GUI (top_bar.wev, `$variables.$dat`) och prövade mot PCD; ingen manual i källorna namnger dem.
Att bygget tar bort skrivningen utan deklaration och behåller bara första instruktionen: prövat i ett testbygge
2026-10-23.*

### 7.4 Navigeringsmeny

**Navigeringsmeny** öppnas med Topplists menyknapp. Den är dold tills `visaMeny` = 1 och tar då
emot klick. Menypanelen är 248 px bred till vänster; resten av ytan är en gardin (scrim) som
tonar ned sidan. Ett klick på gardinen eller på krysset stänger menyn.

Raderna är Översikt, Larm, Inställningar, Info och Inloggning. Varje rad öppnar sin vy och
stänger menyn. Rikta om vyerna enligt [7.1](#71-koppla-en-sidmall).

`visaMeny` delas mellan mallarna genom namnet, som `valdSymbol`. En vy har därför en meny.

> **Ännu inte verifierat:** om `visaMeny` når båda mallarna när Topplist och Navigeringsmeny
> ligger i en förgrundsvy (foreground view) som flera vyer delar. Tills vidare: placera dem i varje
> vy.

*Källa: Web Editor-manualen, del 1, Default foreground/background view; del 8, Function
instructions: Open View. Att menyn öppnas och att en rad byter vy och stänger menyn: prövat mot
PCD 2026-10-25.*

### 7.5 Inloggning

**Inloggning** är en inloggningsruta mot projektets användardatabas, som du fyller i Web8:s
användareditor. Efter inloggningen sätts användarens språk, rättigheterna jämförs med de 16
grupperna, och Web8 hoppar till användarens startsida. Mallen använder Saias sessionsvariabler
för inloggningen (bland annat `_NOINITSESSCO_18` och `_NOINITSESSCO_19`, och de som börjar på
`?S_`). Använd dem inte till något annat.

Ge **varje användare en startsida**. Mallen behåller labbets logik, som sätter felmarkeringen
(`felInloggning`) även efter en lyckad inloggning. Utan startsida står rutan kvar med fälten
markerade som fel.

> **Ännu inte verifierat:** en riktig inloggning på PCD, rättigheterna, hoppet till startsidan och
> felmarkeringen efter en lyckad inloggning. Ingen loggade in under provet på PCD:n. Tills vidare:
> prova inloggningen med en testanvändare innan anläggningen tas i drift.

*Källa: Web Editor-manualen, del 32, Login Dialog (användardatabasen, språk, de 16 grupperna,
startsidan, sessionsvariablerna som ska vara reserverade); del 12, User access. `felInloggning`:
läst ur labbets login.wev; prövat mot en simulerad PCD 2026-10-23.*

### 7.6 Placering och lagerordning

En sida med alla sidmallar, för en vy på 1020 × 600 px:

1. **Topplist** överst i vyn, på (0, 0).
2. **Larmsummering** på (916, 0) relativt topplisten, i samma lager och **efter** Topplist, så att
   den ritas ovanpå.
3. **Sidofält** på (770, 48), i ett lager under `Paneler`, på varje vy som har faceplates eller
   panelknappar. Vyer utan dem, som larmvyn, använder hela bredden. I sidofältet öppnas alla
   faceplates överst, på (770, 50) i lagret `Paneler`
   ([6.1](#61-lager-för-faceplates)), och panelknapparna läggs under dem, från (770, 408) och
   56 px nedåt per knapp. Vyns länk till sina inställningar (en knapp med pil) ligger längst ner
   i sidofältet.
4. Vyns symboler och inställningsblock under topplisten och till vänster om sidofältet, x under
   770.
5. **Navigeringsmeny** på (0, 48), direkt under topplisten, och **sist**: efter alla faceplates i
   det översta lagret, eller i ett eget lager överst. Annars ritas en öppen faceplate ovanpå menyns
   gardin. Menyn och gardinen räcker till nederkanten av en vy som är upp till 1080 px hög. I en
   lägre vy går de utanför vyn i editorn, men i körningen skärs de av vid vyns kant.
6. **Larmlista** i larmvyn. I inloggningsvyn: **Bakgrund** på (0, 0) först och **Inloggning**
   ovanpå den.

Låt knapparnas utseende säga vad de gör, på alla vyer:

- **Till en annan vy:** en knapp med pil, som menyns rader.
- **Öppnar en faceplate:** Panelknapp, utan pil, i sidofältet.
- **Byter mellan undersidor på samma sida**, till exempel inställningssidor: flikar i en rad
  överst, utan pil, där fliken för den sida som visas är markerad som vald.

*Källa: Web Editor-manualen, del 7, Document Outline Window (det översta elementet i översikten
är överst i körningen) och Layer Window. Platserna: lästa ur mallfilerna. Att en faceplate annars
ritas ovanpå gardinen: prövat mot en simulerad PCD 2026-10-23. Att menyn skärs av vid vyns kant:
prövat mot en simulerad PCD 2026-10-26.*

## 8. Bygga och ladda ned

1. **Plattform:** **Project Options > Build > Build Platform**. HTML5 för webbläsare, TEQ för Saia
   PCD Web Panel MB, eller hybrid för båda. Hybrid tar mer plats i PCD:ns flash.
2. **Mål:** **Project Options > Up/Download Targets**. Lägg till ett mål med **+** och välj
   typ: **FTP Server (PCD)**, **WebFtp** eller **PG5 Device (S-Bus)**. Välj sedan under **Files to
   Include** vad som ska laddas ned.
3. **Bygg:** **Build > Build <projekt>**, eller högerklicka på projektet i **Solution**. Varningar
   och fel visas i **Error List** med knappen **Build Output**. Röda rader i PCD Variables Editor
   är symboler som inte finns i PG5-programmet: kontrollera gruppnamnet.
4. **Prova lokalt (valfritt):** **Run > Start** bygger och visar sidorna i din webbläsare via en
   lokal webbserver, med värden från PCD:n om den är ansluten.
5. **Ladda ned:** **Build > Download project**, eller nedladdningsknappen i verktygsfältet. Web8
   sparar och bygger först om det behövs.

Var försiktig med nedladdningsvalet **Delete files in destination not belonging to project**:
det tar bort alla filer på målet som inte hör till projektet.

*Källa: Web Editor 8-hjälpen, Dialog Boxes, s. 14 (Build Options, Build Platform), s. 20–21
(Up/Download Targets, Download Options); Menus, s. 5 (Build, Run); How To, s. 9 (Build the
Project, How to download the Project); Variable Editors, s. 3 (färgerna i PCD Variables Editor).*

## 9. Exempel: pump, ventil och givare

Exemplet bygger en vy med pumpen P01, ventilen SV21 och givaren GT11 och deras faceplates.

**Förutsättningar.** PG5-programmet har tre namngivna MWSE-FBoxar
([avsnitt 2](#2-förutsättningar-i-pg5-programmet)):

| Objekt | FBox | FBox-namn | Grupp |
|---|---|---|---|
| P01, pump | Motor | `VS01.P01` | `A.MWSE.VS01.P01.` |
| SV21, ventil | Utgång, analog | `VS01.SV21` | `A.MWSE.VS01.SV21.` |
| GT11, givare | Ingång, analog, larm | `VS01.GT11` | `A.MWSE.VS01.GT11.` |

Biblioteket är installerat ([avsnitt 3](#3-installation)), och Web8-projektet är kopplat till
PG5-devicen och har företagets författarnamn ([4.1](#41-koppla-projektet-till-pg5-devicen),
[4.2](#42-sätt-författarnamnet)).

**Steg.**

1. Skapa eller öppna vyn. Skapa lagret `Paneler` och flytta det överst
   ([6.1](#61-lager-för-faceplates)).
2. Välj vyns vanliga lager (inte `Paneler`) och placera de tre symbolerna ([5.1](#51-placera)).

   | Mall | Kategori |
   |---|---|
   | `Pump` | `MWSE\Symbol\Motor` |
   | `Tvåvägsventil` | `MWSE\Symbol\Ställdon` |
   | `Givare` | `MWSE\Symbol\Givare` |

3. Koppla varje symbol enligt [5.2](#52-koppla-symbolen-till-objektet): välj gruppen i **Find and
   Replace Template Group**, ta bort alla filter i Cross-Reference, klicka **Replace All** och
   sedan **OK**. Skriv sedan namnet i textelementet `Namn` ([5.4](#54-namnetikett)).

   | Mall | Grupp | Namnetikett |
   |---|---|---|
   | `Pump` | `A.MWSE.VS01.P01.` | `P01` |
   | `Tvåvägsventil` | `A.MWSE.VS01.SV21.` | `SV21` |
   | `Givare` | `A.MWSE.VS01.GT11.` | `GT11` |

4. Kontrollera varje symbol enligt tabellen i [5.3](#53-kontrollera-bytet): PCD-variablerna,
   klickets Toggle-värden och villkoret `Selected` ska bära objektets grupp.
5. Sätt enheterna i **PCD Variables Editor**: `%` på `A.MWSE.VS01.SV21.MV_Ut` och `°C` på
   `A.MWSE.VS01.GT11.MV_Ut` ([5.6](#56-enhet-på-värdefält)).
6. Välj lagret `Paneler` och placera de tre faceplates. De kan ligga på samma plats. Koppla och
   ge rubrik på samma sätt som symbolerna ([6.2](#62-motorpanel)):

   | Mall | Kategori | Grupp | Rubrik i `Namn` |
   |---|---|---|---|
   | `Motorpanel` | `MWSE\Panel\Motor` | `A.MWSE.VS01.P01.` | `P01 - Cirkulationspump` |
   | `Ventilpanel` | `MWSE\Panel\Ventil` | `A.MWSE.VS01.SV21.` | `SV21 - Reglerventil` |
   | `Givarpanel` | `MWSE\Panel\Givare` | `A.MWSE.VS01.GT11.` | `GT11 - Tilloppsgivare` |

7. Kontrollera varje faceplate enligt [5.3](#53-kontrollera-bytet): villkoret `Vald`, taggraden
   och knapparnas mål ska bära objektets grupp.
8. Lägg till avmarkeringen ([6.8](#68-rekommenderat-avmarkera-när-vyn-lämnas)).
9. Kontrollera att ingen annan del av projektet använder samma variabler utan format
   ([4.4](#44-format-på-pcd-variabler)).
10. Bygg och ladda ned ([avsnitt 8](#8-bygga-och-ladda-ned)). Rätta eventuella röda rader i PCD
    Variables Editor först.
11. Kontrollera i webbläsaren:
    - Pumpen visar vila, drift och fel enligt `R_Ut`, `LD_Ut` och `LDT_Ut`; ventilen visar
      utsignalen; givaren visar mätvärdet och larm ([5.5](#55-vad-symbolen-visar)).
    - Ingen panel syns innan du klickat. Klick på en symbol ger en ljus markering och öppnar
      objektets egen panel; klick igen, eller på panelens kryss, stänger den.
    - Klick på en annan symbol byter panel.
    - Panelerna visar samma värden som PCD:n.

Knapparna i Motorpanel och Ventilpanel skriver till PCD:n. Tryck inte på dem i en anläggning i
drift om du bara vill prova vyn.

Vill du också visa inställningar, placera till exempel `Givarinställning` för
`A.MWSE.VS01.GT11.` och `Motorinställning` för `A.MWSE.VS01.P01.` ([6.6](#66-inställningsblock)).
Topplist, meny och larm läggs till enligt [7.6](#76-placering-och-lagerordning).

## 10. Mallar och FBox-signaler

Alla mallar har skaparen Malthe Winje Automation AB. Signalerna är suffixet efter gruppen, till
exempel `R_Ut` i `A.MWSE.VS01.P01.R_Ut`.

**Symboler**

| Mall | Kategori i Web8 | MWSE-FBox | Signaler | Version |
|---|---|---|---|---|
| Pump | `MWSE\Symbol\Motor` | Motor (`MWSE_Motor`) | `R_Ut`, `LD_Ut`, `LDT_Ut`, `Man` | 2.0.0 |
| Fläkt | `MWSE\Symbol\Motor` | Motor | `R_Ut`, `LD_Ut`, `LDT_Ut`, `Man` | 2.0.0 |
| Motor | `MWSE\Symbol\Motor` | Motor | `R_Ut`, `LD_Ut`, `LDT_Ut`, `Man` | 2.0.0 |
| Kompressor | `MWSE\Symbol\Motor` | Motor ¹ | `R_Ut`, `LD_Ut`, `LDT_Ut`, `Man` | 2.0.0 |
| Spjäll | `MWSE\Symbol\Ställdon` | Spjällstyrning (`MWSE_Lflaps2`) | `R_Ut`, `LA_Ut`, `Man` | 2.0.0 |
| Brandspjäll | `MWSE\Symbol\Ställdon` | Spjällstyrning ¹ | `R_Ut`, `LA_Ut`, `Man` | 2.0.0 |
| Tvåvägsventil | `MWSE\Symbol\Ställdon` | Utgång, analog (`MWSE_UtAna`) | `MV_Ut`, `Man` | 2.0.0 |
| Trevägsventil | `MWSE\Symbol\Ställdon` | Utgång, analog ² | `MV_Ut`, `Man` | 2.0.0 |
| Trepunktsventil | `MWSE\Symbol\Ställdon` | Beräknat ärvärde, öka/minska (`MWSE_BAROM`) | `BeräknVärde`, `IncUt`, `DecUt` | 1.0.0 |
| Givare | `MWSE\Symbol\Givare` | Ingång, analog, larm (`MWSE_InAnaL`) | `MV_Ut`, `LG_Ut`, `LL_Ut`, `HL_Ut`, `Man` | 2.0.0 |
| Utegivare | `MWSE\Symbol\Givare` | Ingång, analog, larm | `MV_Ut`, `LG_Ut`, `LL_Ut`, `HL_Ut`, `Man` | 2.0.0 |
| Differensgivare | `MWSE\Symbol\Givare` | Ingång, analog, larm ³ | `MV_Ut`, `LG_Ut`, `LL_Ut`, `HL_Ut`, `Man` | 2.0.0 |
| Integrerad Givare | `MWSE\Symbol\Givare` | Ingång, analog, larm ³ | `MV_Ut`, `LG_Ut`, `LL_Ut`, `HL_Ut`, `Man` | 2.0.0 |
| Roterande värmeväxlare | `MWSE\Symbol\Värmeväxlare` | Motor och, under `VG.`, Verkningsgrad (`MWSE_VGRAD1`) | `R_Ut`, `LD_Ut`, `LDT_Ut`, `Man`, `VG.PV_Ut`, `VG.LAL_Ut` | 1.0.0 |
| Panelknapp | `MWSE\Symbol\Knapp` | – (gruppen för panelen den öppnar) | – | 1.0.0 |
| Värmebatteri, Kylbatteri, Kombibatteri, Filter, Radiator, Värmetank, Krets, Krets med bypass, Krets med backventil, Tank, Liten tank, Värmeväxlare X, Värmeväxlare V, Plattvärmeväxlare | `MWSE\Symbol\Statisk` | – | – | 1.0.0 |

**Faceplates**

| Mall | Kategori i Web8 | MWSE-FBox | Signaler | Version |
|---|---|---|---|---|
| Motorpanel | `MWSE\Panel\Motor` | Motor | `LD_Ut`, `LDT_Ut`, `Man`, `ManV`, `DTtid`, `DTstarter` | 1.0.0 |
| Givarpanel | `MWSE\Panel\Givare` | Ingång, analog, larm | `MV_Ut`, `LG_Ut`, `LL_Ut`, `HL_Ut`, `HLLarmGrL`, `HLLarmGrH` | 1.0.0 |
| Ventilpanel | `MWSE\Panel\Ventil` | Utgång, analog | `MV_Ut`, `Min`, `Max`, `Man`, `ManV` | 1.0.0 |
| Spjällpanel | `MWSE\Panel\Spjäll` | Spjällstyrning | `LA_Ut`, `Man`, `ManV` | 1.0.0 |
| Trepunktsventilpanel | `MWSE\Panel\Ventil` | Beräknat ärvärde, öka/minska | `BeräknVärde`, `IncUt`, `DecUt`, `KalUt` | 1.0.0 |
| Regulatorpanel | `MWSE\Panel\Regulator` | Regulator PI (`MWSE_heaPI3`) | `Mode`, `ManVal`, `ActVal`, `SetPt`, `YOut`, `PFac00`, `IntTime` | 1.0.0 |
| PID-regulatorpanel | `MWSE\Panel\Regulator` | Regulator PID (`MWSE_heaPID3`) | `Mode`, `ManVal`, `ActVal`, `SetPt`, `YOut`, `PFac00`, `IntTime`, `DerTime` | 1.0.0 |
| P-regulatorpanel | `MWSE\Panel\Regulator` | Regulator P (`MWSE_heaPP2`) | `Mode`, `ManVal`, `ActVal`, `SetPt`, `YOut`, `PFac`, `PtIni` | 1.0.0 |
| Handpanel digital | `MWSE\Panel\Hand` | Auto/Hand, digital (`MWSE_AHdi`, `MWSE_AHdigS`, `MWSE_AHdigS2`) | `Man`, `ManV` | 1.0.0 |
| Handpanel analog | `MWSE\Panel\Hand` | Auto/Hand, analog (`MWSE_AHan`, `MWSE_AHanaS`, `MWSE_AHanaS2`) | `Man`, `ManV` | 1.0.0 |
| Digitalsignalpanel | `MWSE\Panel\Hand` | Digital signal (`MWSE_DigSignal`) | `Man`, `ManV`, `Invertering`, `StartF_tid`, `StartF_enhet`, `StoppF_tid`, `StoppF_enhet` | 1.0.0 |

**Inställningar och status**

| Mall | Kategori i Web8 | MWSE-FBox | Signaler | Version |
|---|---|---|---|---|
| Givarinställning | `MWSE\Inställning\Givare` | Ingång, analog, larm | `HLLarmGrL`, `HLLarmGrH`, `HLLarmF`, `HLLarmFenhet`, `GfLarmGrL`, `GfLarmGrH`, `GfLarmF` | 1.0.0 |
| Givarskalningsinställning | `MWSE\Inställning\Givare` | Ingång, analog, larm | `Filtertid`, `Kalibrering`, `BPX1`, `BPX2`, `BPY1`, `BPY2` | 1.0.0 |
| Givarhandinställning | `MWSE\Inställning\Givare` | Ingång, analog, larm | `Man`, `ManV` | 1.0.0 |
| Motorinställning | `MWSE\Inställning\Motor` | Motor | `DTLarmGr`, `DTLarmF`, `DTtid`, `DTstarter`, `DTdatum`, `DTreset` | 1.0.0 |
| Motorfördröjningsinställning | `MWSE\Inställning\Motor` | Motor | `StartF`, `StoppF` | 1.0.0 |
| Ventilskalningsinställning | `MWSE\Inställning\Ventil` | Utgång, analog | `BPX1`, `BPX2`, `BPY1`, `BPY2` | 1.0.0 |
| Trepunktsventilinställning | `MWSE\Inställning\Ventil` | Beräknat ärvärde, öka/minska | `GångtidÖ`, `GångtidM`, `Dödzon` | 1.0.0 |
| Spjällinställning | `MWSE\Inställning\Spjäll` | Spjällstyrning | `LarmF_O`, `LarmF_S` | 1.0.0 |
| Regleravvikelseinställning | `MWSE\Inställning\Regleravvikelse` | Larm, regleravvikelse (`MWSE_RAVB`) | `LarmGrAvv`, `Fördr`, `TidsEnh` | 1.0.0 |
| Regulatorinställning | `MWSE\Inställning\Regulator` | Regulator PI (`MWSE_heaPI3`) | `ActVal`, `SetPt`, `YOut`, `PFac00`, `IntTime`, `Offset` | 1.0.0 |
| PID-regulatorinställning | `MWSE\Inställning\Regulator` | Regulator PID (`MWSE_heaPID3`) | `ActVal`, `SetPt`, `YOut`, `PFac00`, `IntTime`, `DerTime`, `FT1Time`, `Offset` | 1.0.0 |
| P-regulatorinställning | `MWSE\Inställning\Regulator` | Regulator P (`MWSE_heaPP2`) | `ActVal`, `SetPt`, `YOut`, `PFac`, `PtIni` | 1.0.0 |
| Kurvinställning | `MWSE\Inställning\Börvärdeskurva` | Börvärdeskurva 5 (`MWSE_BvK5`) | `BPX1`–`BPX5`, `BPY1`–`BPY5`, `YminV`, `YmaxV` | 2.0.0 |
| Kurvinställning 2, 3, 4, 6, 7, 8, 9, 10 | `MWSE\Inställning\Börvärdeskurva` | Börvärdeskurva *n* (`MWSE_BvK<n>`) | `BPX1`–`BPX<n>`, `BPY1`–`BPY<n>`, `YminV`, `YmaxV` | 2.0.0 |
| Kurvinställning med sänkning 2, 5, 6, 7, 8, 9, 10 | `MWSE\Inställning\Börvärdeskurva` | Börvärdesk. m. sänkning *n* (`MWSE_BvK<n>s`) | `BPX1`–`BPX<n>`, `BPY1`–`BPY<n>`, `BPY1n`–`BPY<n>n`, `YminV`, `YmaxV` | 2.0.0 |
| Pumpstartinställning | `MWSE\Inställning\Pumpstart` | Pumpstart (`MWSE_Pumpst`) | `StartGrD`, `StartGr`, `StartF_tid`, `StartF_enhet`, `StoppGr`, `StoppF_tid`, `StoppF_enhet` | 1.0.0 |
| Veckoschemainställning | `MWSE\Inställning\Tidkanal` | Tidkanal m. referens eller m. ingångar (`MWSE_TKref`, `MWSE_TKing`) | `månTill`, `månFrån`, `tisTill`, `tisFrån`, `onsTill`, `onsFrån`, `torsTill`, `torsFrån`, `freTill`, `freFrån`, `lörTill`, `lörFrån`, `sönTill`, `sönFrån`, `dfhTill`, `dfhFrån`, `hdTill`, `hdFrån` | 1.0.0 |
| Veckodagsinställning | `MWSE\Inställning\Tidkanal` | Tidkanal, veckodag (`MWSE_TKM`) | `Dag`, `TidT`, `TidF` | 1.0.0 |
| Veckopulsinställning | `MWSE\Inställning\Tidkanal` | Veckopuls (`MWSE_Vpuls`) | `Dag`, `TidTill`, `Pulslängd`, `Tidsenhet` | 1.0.0 |
| Frysvaktsinställning | `MWSE\Inställning\Larm` | Larm, frysvakt (`MWSE_LFrV`) | `LGr`, `HGr`, `LarmF`, `Enhet` | 1.0.0 |
| Hög-låglarminställning | `MWSE\Inställning\Larm` | Larm HögLåg (`MWSE_RHL`) | `LarmGrL`, `LarmGrH`, `TidsF`, `Enhet` | 1.0.0 |
| Driftfelsinställning | `MWSE\Inställning\Larm` | Larm, driftfel (`MWSE_DRIFTFEL2`) | `LarmF` | 1.0.0 |
| Drifttidsinställning | `MWSE\Inställning\Larm` | Larm, drifttid (`MWSE_heaCtW2`) | `LarmGr`, `AntalTimmar`, `AntalStarter`, `ResetDT` | 1.0.0 |
| Handsummering | `MWSE\Inställning\Status` | Auto/Hand MNG (`MWSE_AH_MNG`) | `AntalHand`, `TillAuto` | 1.0.0 |
| Handstatus analog | `MWSE\Inställning\Status` | Auto/Hand, analog via R (`MWSE_MwStatI_`) | `Man_Ut`, `V_Ut`, `RegisterV1`, `RegisterV2` | 1.0.0 |
| Handstatus digital | `MWSE\Inställning\Status` | Auto/Hand, digital via R (`MWSE_StatB`) | `ManUt`, `V_Ut`, `RegisterV1`, `RegisterV2`, `RegisterV3` | 1.0.0 |

**Sidmallar**

| Mall | Kategori i Web8 | Binds till | Version |
|---|---|---|---|
| Larmlista | `MWSE\Sida\Larm` | `A.Alarm.Namn.MyName` (egenskapen Alarm List PPO Name) | 1.0.0 |
| Larmsummering | `MWSE\Sida\Larm` | `Namn.antal` | 1.0.0 |
| Topplist | `MWSE\Sida\Navigering` | `S.HVC.Clock.HHMMSS`, `S.HVC.Clock.YYYYMMDD`; interna `visaMeny`, `sidTitel`; Web8:s inloggningsvariabler | 1.0.0 |
| Navigeringsmeny | `MWSE\Sida\Navigering` | intern `visaMeny` | 1.0.0 |
| Inloggning | `MWSE\Sida\Inloggning` | projektets användardatabas och Web8:s inloggningsvariabler | 1.0.0 |

Alla symboler, Panelknapp och faceplates använder dessutom den interna variabeln `valdSymbol`;
symbolerna och de statiska symbolerna även `doljNamn`.

**Hur säker kopplingen till FBoxen är.** Varje malls signaler finns på FBoxens sida i
MWSE-hjälpen, utom för regulatorerna, Tidkanal veckodag och Verkningsgrad, vars signaler är lästa
ur FBox-makrona. De första 21 mallarna är prövade mot PCD, och Pump, Tvåvägsventil, Givare,
Utegivare, de tre första faceplates och de sex första inställningsblocken är dessutom
kontrollerade mot ett Fupla-program som använder FBoxarna. För de markerade mallarna gäller:

1. **Kompressor och Brandspjäll:** MWSE har ingen egen FBox för kompressor eller brandspjäll.
   Motor och Spjällstyrning är de som passar bäst, och mallarna fungerar när objektet styrs med
   dem. Brandspjäll visar Öppet när `R_Ut` = 1 och fel på `LA_Ut`, precis som Spjäll. Ett objekt
   som styrs med en FBox utanför MWSE-biblioteket skapar inga `A.MWSE`-symboler och kan inte
   kopplas.
2. **Trevägsventil:** passar ett ställdon 0–10 V som styrs med Utgång, analog. Ett
   trelägesställdon (öka/minska) styrs med FBoxen Beräknat ärvärde, öka/minska; använd då
   `Trepunktsventil`.
3. **Differensgivare och Integrerad Givare:** sannolikt rätt, eftersom de är givare på Ingång,
   analog, larm som Givare, men inte kontrollerade mot ett program som använder dem. Kontrollera
   efter bygget att PCD Variables Editor inte visar några röda rader för dem.

De 60 mallar som kom till 2026-10-23 efter de första 21 är prövade mot en simulerad PCD och i ett
testbygge utan fel. Mot PCD 2026-10-25 är de prövade som har en FBox i labbprogrammet:
Givarskalnings-, Givarhand-, Ventilskalnings- och Motorfördröjningsinställning, Regulatorpanel
(i den tidigare utformningen),
P-regulatorinställning, Larmlista, Larmsummering, Topplist och Navigeringsmeny. **Ännu inte
verifierat:** de övriga mot PCD, eftersom labbprogrammet saknar deras FBoxar. Kontrollera för dem
efter bygget att PCD Variables Editor inte visar några röda rader. Kurvinställning är sedan
version 2.0.0 ett kurvblock och prövad bara mot en simulerad PCD ([6.9](#69-kurvblock)).

*Källa: signalnamnen finns på sidorna Motor, Spjällstyrning, Utgång analog, Ingång analog larm,
Larm regleravvikelse, Börvärdeskurva 5 och Pumpstart i MWSE FBox-hjälpen, avsnitt "A-symboler",
och för de nya mallarna på de sidor som står i avsnitt 6.5–6.7; Beräknat ärvärde, öka/minska på
sidan med samma namn. Kontrollen mot Fupla-programmet: prövat 2026-10-23. Kategorier, versioner
och signaler är lästa ur mallfilerna.*

## 11. Källor

**Web Editor 8-hjälpen.** Hjälpen som installeras med Saia PG5 (prövad version: PG5 2.3.196).
Sidnumren avser hjälpens kapitel utskrivna som PDF: Data Sources, Dialog Boxes, How To,
Introduction, Menus, Variable Editors och Windows.

**Web Editor-manualen.** Saia PG5 Web Editor Manual, samma innehåll som hjälpen, i Malthe Winje
Automation AB:s utgåva i 37 delar. Delnumren avser den utgåvan; avsnittsrubriken står med, så att
avsnittet går att hitta i hjälpen.

**MWSE FBox-hjälpen.** `MWSE.chm` ur biblioteket `MWSE_V25_10_003`, sidorna Motor
(`Motor.html`), Ingång analog larm (`inAna.html`), Utgång analog (`utAna.html`), Spjällstyrning
(`Spjall.html`), Larm regleravvikelse (`LarmRavv.html`), Regulator PI (`PI.html`),
Börvärdeskurva 5 (`BvK5.html`), Pumpstart (`pumpstart.html`), Beräknat ärvärde, öka/minska
(`barom.html`) och Verkningsgrad (`verkningsgrad.html`). Övriga sidor anges med namn och del i
Malthe Winje Automation AB:s utgåva av hjälpen i 22 delar (MWSE FBox Library Documentation).

**FBox-makrona.** `MWSEFamilyAllmant.lib` med inställningsfönstren i `MWSEFamilyAllmant.def` och
`MWSEFamilyAllmant_en.idx`, ur `MWSE_V25_10_003`. Det som bara är läst där är markerat som
slutsats.

**Prövat.** Malthe Winje Automation AB:s egna tester, med PG5 2.3.196 och Web Editor 8, en
PCD3.M5360 med COSinus 1.28.51 och MWSE FBox-biblioteket V20.6.001. Prov mot PCD gjordes
2026-10-23 med de första 21 mallarna och 2026-10-25 med dem vars FBox labbprogrammet har. De
övriga prövades 2026-10-26 mot PCD:n med ett testprogram som har en av varje MWSE-FBox som
biblioteket använder ([avsnitt 10](#10-mallar-och-fbox-signaler)). De 16 kurvblocken i version
2.0.0 är prövade 2026-10-26 bara mot en simulerad PCD ([6.9](#69-kurvblock)).

## 12. Fel och förslag

MWSE Web8 Library ges ut av Malthe Winje Automation AB. Rapportera fel och föreslå förbättringar
som ett issue i [förrådet på GitHub](https://github.com/mwa-eas/MWSE-Web8-Library/issues), med
formuläret **Fel eller förslag**. Ange bibliotekets version och bifoga gärna en skärmbild.

Förrådet är publikt: skriv inte anläggningsnamn, IP-adresser, inloggningar eller kunduppgifter i
ett issue, och maskera sådant i skärmbilder.
