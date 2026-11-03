# MWSE Web8 Library – Integratörsguide

Malthe Winje Automation AB.

> **Early access (beta).** MWSE Web8 Library är i tidig åtkomst inför den officiella lanseringen.
> Mallar och guide kan ändras mellan versionerna. Läs [ändringsloggen](CHANGELOG.md) före en
> uppdatering.

Guiden beskriver hur du installerar MWSE Web8 Library (nedan biblioteket) i Saia PG5 Web Editor
8 (nedan Web8), placerar symboler, faceplates, inställningsblock och sidmallar i en vy, kopplar
dem till MWSE-FBoxar i PG5-programmet och laddar ned vyn till en PCD. Den förutsätter att du kan
Web8 och Fupla men inte känner till biblioteket.

Prova varje vy mot anläggningens PCD innan den tas i drift.

---

## 1. Vad biblioteket innehåller

Biblioteket har 91 mallar:

- **18 symboler med signaler:** pump, fläkt, motor, kompressor, spjäll, brandspjäll, tre ventiler
  (tvåväg, treväg, trepunkt), fyra givare, roterande värmeväxlare, Panelknapp, Indikator,
  Tryckknapp och Tidkanalknapp. En symbol visar vila, drift/aktiv och i förekommande fall fel,
  hand och larm. Ett klick öppnar objektets faceplate; Indikator och Tidkanalknapp visar bara.
- **14 statiska symboler:** batterier, filter, radiator, tankar, kretsar, två värmeväxlare för
  ventilation och en plattvärmeväxlare för vatten. Bara en bild och en namnetikett, utan signaler.
- **11 faceplates** (manöverpaneler) för motor, givare, ventil, spjäll, trepunktsventil,
  regulator PI, PID och P och handstyrning.
- **41 inställnings- och statusblock** för larmgränser, fördröjningar, skalning, regulatorer,
  tidkanaler, medelvärde, verkningsgrad och handstatus, bland dem 16 kurvblock som visar börvärdeskurvans punkter i en tabell
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

1. PCD-programmet använder MWSE FBox-biblioteket. Mallarna är byggda för version **V20.6.001**.
   Alla A-symboler som mallarna använder finns också i V25.10.003 och V26.09.015. De nya
   versionerna har också nya FBoxar och signaler som mallarna inte använder än, till exempel
   `GfErsätt` i Analog ingång.

   **Medelvärdesinställning och Verkningsgradsstatus kräver V25.10.003 eller senare.** Deras
   FBoxar, Justerat Medel, Justerat Medel Array och Verkningsgrad V2, finns inte i V20.6.001
   ([6.7](#67-fler-inställningsblock-och-statusblock)).

   **Undantag: Givarskalningsinställning.** I V25.10.003 och V26.09.015 får en Analog ingång
   (`MWSE_InAnaL`) av version 21.05.001 eller senare inga brytpunkter `BPX1`, `BPX2`, `BPY1` och
   `BPY2`, och blockets fält blir tomma. Använd Givarskalningsinställning bara med en Analog ingång
   från V20.6.001 tills FBox-biblioteket är rättat.
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

Filnamnen i zip-filen saknar å, ä och ö, men **Libraries** visar mallarnas namn med å, ä och ö.

Om importen misslyckas, importera de lösa filerna i `mallar/` enligt
[3.2](#32-alternativ-lösa-mallfiler).

### 3.2 Alternativ: lösa mallfiler

- **Importera:** som i [3.1](#31-rekommenderat-importera-zip-filen), men markera de `.xtml`-filer
  i `mallar/` som du vill ha.
- **Kopiera:** lägg filerna i mappen `SBC\WebEditor\Templates\` under Windows gemensamma
  programdatamapp, normalt `C:\ProgramData\SBC\WebEditor\Templates\`. Web8 läser egna mallar från
  den mappen; en mall som ligger någon annanstans visas inte i biblioteket. Starta om Web8 efter
  kopieringen.

### 3.3 Uppdatera till en ny version

- Web8 hoppar över en mall som redan finns i biblioteket. Ta därför bort den gamla versionen
  först: markera mallarna i **Libraries**, högerklicka och välj **Delete**. Importera sedan den
  nya versionen.
- Bara egna mallar kan tas bort; Web8:s inbyggda mallar är skrivskyddade.
- Räkna med att instanser som redan är placerade i en vy inte följer med när mallen byts ut:
  placera om symbolen och gör om bytet i [avsnitt 5.2](#52-koppla-symbolen-till-objektet).

## 4. Förbered Web8-projektet

### 4.1 Koppla projektet till PG5-devicen

Öppna **Project Options** (dubbelklicka på projektet i fönstret **Solution**) och fyll i
**Symbol data source** med PG5-projektets device-katalog. Web8 behöver den för att slå upp
symbolerna. Utan den kan Web8 inte heller erbjuda grupperna från PG5-programmet vid bytet i
[avsnitt 5.2](#52-koppla-symbolen-till-objektet).

### 4.2 Sätt författarnamnet

Web8 lägger in författaruppgifterna i varje bygge. De går att läsa i den färdiga
visualiseringen som byggkonstanter (`?S_BC_AuthorInfoName`, `?S_BC_AuthorInfoCompany` med
flera). Byggda GUI:n på PCD:n bär dessutom namnet i filen `$$DeploymentInfo.dat`. Sätt därför
företagets namn, inte en persons, innan du bygger:

1. **Tools > Options > Author Information**: standardförfattare. Gäller alla lösningar som skapas
   därefter, om inte lösningen anger något annat.
2. **Project > Solution Options > Author Information**: för en lösning som redan finns. Kryssa i
   rutan och fyll i formuläret; det skriver över standardvärdet för den lösningen.

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
([5.8](#58-statiska-symboler-och-panelknapp)). Inställningsblock, statiska symboler, Indikator,
Tidkanalknapp och sidmallar använder inte `valdSymbol`. Sidmallarna har två egna delade variabler, `visaMeny` och
`sidTitel` ([avsnitt 7](#7-sidmallar)).

### 4.4 Format på PCD-variabler

Mallarna deklarerar ett format för varje signal som har decimaler, tid eller datum. Formatet
styr hur ett värde visas och hur en inmatningsruta skriver det till PCD:n. Signaler utan format
(lägen, flaggor, antal, enheter och val i listor) visas och skrivs som heltal.

| Format | Signaler |
|---|---|
| `DEC.1` | `MV_Ut`, ventilens `Min`, `Max` och `ManV`, larmgränserna `HLLarmGrL`, `HLLarmGrH`, `GfLarmGrL`, `GfLarmGrH`, `LarmGrAvv`, kurvans `BPX1`–`BPX5` och `BPY1`–`BPY5`, regulatorns `ActVal`, `SetPt`, `YOut`, `IntTime`, `Offset`, pumpstartens `StartGrD`, `StartGr`, `StoppGr`; skalningen `BPX1`–`BPY2`, `Filtertid` och `Kalibrering`; Givarhandinställningens `ManV`; regulatorernas `PFac`, `PtIni`, `LimLow`, `LimHigh`, `ManVal`; kurvornas punkt 6–10, `YminV`, `YmaxV` och de sänkta värdena `BPY1n`–`BPY10n`; larmgränserna `LGr`, `HGr`, `LarmGrL`, `LarmGrH`; PID-regulatorns `DerTime`, `FT1Time`; `BeräknVärde`, `Dödzon`; Verkningsgradens `PV_Ut` och `PV_Utx`; handvärdena `ManV` (Handpanel analog) och `V_Ut` (Handstatus analog) |
| `DEC.2` | PI- och PID-regulatorns P-faktor `PFac00` (se nedan) |
| `DEC` | `DTtid`, `DTLarmGr`, `DTLarmF`, `StartF_tid`, `StoppF_tid`, `StartF`, `StoppF`, `LarmF_O`, `LarmF_S`, `GångtidÖ`, `GångtidM`, Drifttidsinställningens `LarmGr` och `AntalTimmar` |
| `DD.MM.YY` | `DTdatum` |
| `HH:MM:SS`, `DD.MM.YY` | Topplistens klocka `S.HVC.Clock.HHMMSS` och `S.HVC.Clock.YYYYMMDD` |
| `HH:MM` | Tidkanalernas tider (registret håller TTMM, 07:30 = 730) |

**P-faktorn i PI- och PID-regulatorn har två decimaler.** FBoxen anger P-faktorn i
hundradelar (`Yp = Xw·Fp/100`), så `PFac00` = 100 betyder 1,00 % per K och visas som `1.00`.
Har ditt projekt redan deklarerat `PFac00` med ett annat format i **PCD Variables Editor**,
gäller projektets format före mallens. Ändra det då till `DEC.2`. P-regulatorns `PFac` är
regulatorns egen inställning i tiondelar och behåller `DEC.1`.

Kontrollera efter nedladdning att ett inskrivet värde, till exempel 22.5, läses tillbaka med rätt
decimaler, och ändra formatet i **PCD Variables Editor** om det inte gör det.

> **Varning:** om samma PCD-variabel används någonstans i projektet utan format kan Web8 tappa
> mallens format. En inmatningsruta skriver då 22.5 som 22. Deklarera därför formatet för
> variabeln i **PCD Variables Editor** (kolumnen **Format**), eller koppla variabeln för hand bara
> med formatet satt.

## 5. Placera en symbol och koppla den

### 5.1 Placera

1. Öppna vyn och välj det lager symbolen ska ligga i (se [6.1](#61-lager-för-faceplates) för
   lagerordningen).
2. Dubbelklicka på mallen i **Libraries**, eller dra den till vyn. Den hamnar i vyns övre vänstra
   hörn; dra den till rätt plats.
3. Använder mallen PCD-variabler öppnar Web8 dialogen **Cross-Reference** direkt, och om
   PG5-programmet har passande grupper även **Find and Replace Template Group**. Koppla mallen
   enligt [5.2](#52-koppla-symbolen-till-objektet).

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

### 5.4 Namnetikett

Under symbolen står texten `Namn`; på statiska symboler och på Indikator, Tryckknapp och
Tidkanalknapp står den ovanför bilden. Markera
textelementet `Namn` i instansen och skriv objektets namn i egenskapen **Text**, till exempel
`P01`. Etiketten är 80 px bred, så ett namn längre än ungefär 12 tecken klipps.

Ska etiketten inte synas, töm dess text.

### 5.5 Vad symbolen visar

| Typ | Vila | Drift/aktiv | Fel och larmindikering | Text vid symbolen |
|---|---|---|---|---|
| Pump, Fläkt, Motor, Kompressor | `R_Ut` = 0 | `R_Ut` = 1 | `LD_Ut` eller `LDT_Ut` | Okänd / Drift / Stopp |
| Spjäll, Brandspjäll | `R_Ut` = 0 | `R_Ut` = 1 | `LA_Ut` | Okänd / Öppet / Stängt |
| Tvåvägsventil, Trevägsventil | `MV_Ut` = 0 | `MV_Ut` > 0 | inget (FBoxen har ingen larmutgång) | värdet `MV_Ut` |
| Trepunktsventil | båda utgångarna 0 | `IncUt` eller `DecUt` = 1 | inget (FBoxen har ingen larmutgång) | värdet `BeräknVärde` |
| Givare, Utegivare, Differensgivare, Integrerad Givare | normalt | inget | `LG_Ut`, `LL_Ut` eller `HL_Ut` | värdet `MV_Ut` |
| Roterande värmeväxlare | `R_Ut` = 0 | `R_Ut` = 1 | `LD_Ut`, `LDT_Ut` eller låg verkningsgrad `VG.LAL_Ut` | verkningsgraden `VG.PV_Ut` |
| Indikator | `StaUt` = 0 | `StaUt` = 1 | `LrmUt` = 1 | – |
| Tryckknapp | `ManV` = 0 | `ManV` = 1 | inget | – |
| Tidkanalknapp | `V_Ut` = 0 | `V_Ut` = 1 | inget | – |

Handindikeringen visas när `Man` = 1; Trepunktsventil har ingen, eftersom FBoxen saknar
handläge, och inte heller Indikator och Tidkanalknapp
([5.9](#59-indikator-tryckknapp-och-tidkanalknapp)). Spjällen visar Öppet när `R_Ut` = 1, eftersom Spjällstyrnings `R_Ut` är manöversignalen
ut.

### 5.6 Enhet på värdefält

Givare och ventiler visar sitt värde med en decimal. Enheten (till exempel `°C` eller `%`) är
inte en del av mallen. I Web8 sätts enheten per PCD-variabel: öppna projektets `.wvar`-fil, sidan
**PCD Variables Editor**, och fyll i kolumnen **Units** för `A.MWSE.<grupp>.MV_Ut` (för
Trepunktsventil `BeräknVärde`, för Roterande värmeväxlare `VG.PV_Ut`). Texten visas efter värdet i
webbläsaren. Flera rader kan markeras och få samma enhet på en gång. Sätt enheten för varje givare
och ventil. Kurvblockens signaler ska däremot inte ha någon enhet ([6.9](#69-kurvblock)).

### 5.7 Roterande värmeväxlare: två grupper

`Roterande värmeväxlare` (kategori `MWSE\Symbol\Värmeväxlare`; bilden 70 × 210 px, med etikett
och värde 80 × 250 px; kanalerna möter bilden 40 och 210 px ner i mallen) binder två FBoxar:

| Grupp i mallen | FBox | Signaler |
|---|---|---|
| `A.MWSE.Namn.` | Motor, för rotorn | `R_Ut`, `LD_Ut`, `LDT_Ut`, `Man` |
| `A.MWSE.Namn.VG.` | Verkningsgrad (`MWSE_VGRAD1`) | `PV_Ut` (verkningsgraden), `LAL_Ut` (låg verkningsgrad) |

Ge Verkningsgrad-FBoxen motorns namn plus `.VG`: motorn `LB01.VVX1`, verkningsgraden
`LB01.VVX1.VG`. Då byter ett och samma byte av `A.MWSE.Namn.` båda grupperna. Ge inte båda
FBoxarna samma namn: Motor och Verkningsgrad har båda en symbol som heter `Status`, och de kan
krocka.

Dialogen **Find and Replace Template Group** klarar inte en mall som refererar mer än en grupp.
Koppla därför så här:

1. Markera instansen och öppna **Cross-Reference**. Ta bort alla filter.
2. Skriv i **Find what**: `A.MWSE.Namn.` och i **Replace with**: motorns grupp, till exempel
   `A.MWSE.LB01.VVX1.`. Alternativt: högerklicka på motorns `R_Ut` och välj **Select Group for
   Find and Replace** (Ctrl+G), som fyller i fälten.
3. Klicka **Replace All** och sedan **OK**.
4. Kontrollera med **Value Filter** `A.MWSE.Namn` att inga rader finns kvar, och att
   verkningsgradens rader nu heter `A.MWSE.LB01.VVX1.VG.PV_Ut` och `...VG.LAL_Ut`.

Står `VG`-raderna kvar, byt dem i ett andra steg med **Find what** `A.MWSE.Namn.VG.` och
**Replace with** `A.MWSE.LB01.VVX1.VG.`.

Ett klick på symbolen öppnar den `Motorpanel` som är kopplad till motorns grupp. Symbolen har
ingen egen faceplate.

### 5.8 Statiska symboler och Panelknapp

**Statiska symboler** (kategori `MWSE\Symbol\Statisk`) har inga signaler, inga lägen och inget
klick. De behöver ingen koppling: placera dem och skriv namnet i `Namn`, eller töm texten om ingen
etikett ska synas ([5.4](#54-namnetikett)). Bilderna har följande storlek:

| Mall | Storlek (px) |
|---|---|
| Värmebatteri, Kylbatteri, Kombibatteri, Filter | 40 × 40 |
| Radiator | 90 × 60 |
| Värmetank | 100 × 150 |
| Krets, Krets med bypass, Krets med backventil | 60 × 140 |
| Tank | 100 × 110 |
| Liten tank | 60 × 60 |
| Värmeväxlare X, Värmeväxlare V, Plattvärmeväxlare | 250 × 210 |

Etiketten ligger ovanför bilden, så mallen är 20 px högre än bilden och minst 80 px bred.

Alla mallar ligger på Web8:s rutnät om 10 px, och där en kanal eller ledning möter en symbol ligger
också på rutnätet, när mallen är placerad på det. En symbols bild har sin mitt 50 px ner i mallen,
där kanalen går. En givares stam slutar 69 px ner, mot ovankanten av en kanal på 70 px. Ett
batteris låda slutar 10 px under kanalen, och kretsens övre del ligger där. Kretsens ben slutar
120 px längre ner. En radiators ledningar går in i dess underkant 20 och 70 px från mallens
vänsterkant. Tankarnas väggar ligger på rutnätet.

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

**Panelknapp** (kategori `MWSE\Symbol\Knapp`, 230 × 40 px) öppnar den faceplate som är kopplad
till samma grupp, för de faceplates som ingen symbol öppnar: de tre regulatorpanelerna, Handpanel digital
när ingen Tryckknapp öppnar den ([5.9](#59-indikator-tryckknapp-och-tidkanalknapp)), Handpanel analog
och Digitalsignalpanel. Den fungerar som en symbol: klicket skriver gruppen till
`valdSymbol`, och knappen markeras som vald. Skriv knappens text i `Namn`, till exempel `Reg`.
Knappen har ingen pil: pilen betyder att man går till en annan vy, som i menyn. Lägg
panelknapparna i sidofältet under panelerna ([7.6](#76-placering-och-lagerordning)).

Panelknapp har inga PCD-variabler, så knappen **Find and Replace Template Group** är avstängd.
Koppla den med den handskrivna vägen i [5.2](#52-koppla-symbolen-till-objektet): **Find what**
`A.MWSE.Namn.`, **Replace with** panelens grupp, till exempel `A.MWSE.VS01.Reg.`, **Replace All**.

### 5.9 Indikator, Tryckknapp och Tidkanalknapp

Tre små symboler för digitala signaler. Mallen är 80 × 80 px: bilden är 50 × 50 px mitt i en
ruta på 60 × 60 px, med mitten 40 px in och 50 px ner, och namnet står ovanför
([5.4](#54-namnetikett)). Koppla dem som andra symboler ([5.2](#52-koppla-symbolen-till-objektet)).

- **Indikator** (kategori `MWSE\Symbol\Indikator`) visar en driftfelsövervakning, FBoxen Larm,
  driftfel (`MWSE_DRIFTFEL2`): vila när driftsvaret är från (`StaUt` = 0), drift när det är till
  (`StaUt` = 1) och fel med larmmärke när driftfelslarmet är aktivt (`LrmUt` = 1). Den har inget
  klick och ingen faceplate; fördröjningen ställs i Driftfelsinställning
  ([6.7](#67-fler-inställningsblock-och-statusblock)).
- **Tryckknapp** (kategori `MWSE\Symbol\Knapp`) visar utsignalen från en Auto/Hand, digital
  (`MWSE_AHdi`, `MWSE_AHdigS` eller `MWSE_AHdigS2`): aktiv när utsignalen är till (`ManV` = 1),
  med handmärke när den är i Hand (`Man` = 1). Handmärket står till höger om bilden, utanför
  valmarkeringen. Ett klick väljer symbolen och öppnar den Handpanel digital som är kopplad till
  samma grupp; där ställs läge och utsignal. Symbolen skriver ingenting själv. Placera därför en
  Handpanel digital per Tryckknapp i lagret `Paneler` och koppla den till samma grupp, som andra
  faceplates ([6.2](#62-motorpanel), [6.5](#65-fler-faceplates)).
- **Tidkanalknapp** (kategori `MWSE\Symbol\Tidkanal`) visar utsignalen från en Tidkanal, veckodag
  (`MWSE_TKM`): aktiv när kanalen är till (`V_Ut` = 1). Den har inget klick och ingen faceplate;
  tiderna ställs i Veckodagsinställning, kopplad till samma grupp
  ([6.7](#67-fler-inställningsblock-och-statusblock)).

Indikator och Tidkanalknapp har ingen klickyta och ingen markering, så raderna för `Knapp` och
`Vald` i [5.3](#53-kontrollera-bytet) gäller inte dem.

## 6. Faceplates och inställningsblock

En faceplate är en mall som ligger i vyn hela tiden men är dold tills dess objekt är valt. Den
visar sig bara när `valdSymbol` är lika med dess grupp; annars är den dold och tar inte emot
klick. Den stängs med krysset uppe till höger, eller med ett nytt klick på symbolen. Krysset
skriver ingenting till PCD:n.
Vyn behöver därför **en faceplate-instans per objekt**, kopplad till samma grupp som objektets
symbol eller Panelknapp. Alla faceplates är 250 × 350 px, så att de öppnas på samma plats
([7.6](#76-placering-och-lagerordning)).

Ett inställningsblock är alltid synligt ([6.6](#66-inställningsblock)).

### 6.1 Lager för faceplates

1. Skapa ett nytt lager med **Edit > Layer > New**, till exempel med namnet `Paneler`.
2. Flytta det överst med **Edit > Layer > Move Up**, så att panelerna ritas ovanpå symbolerna.
   Det översta lagret är den första raden i fönstret **Layers**.
3. Lägg alla faceplates i det lagret. De kan ligga på samma plats, eftersom bara en i taget är
   synlig.

Navigeringsmeny ligger i förgrundsvyn och därmed ovanför panelerna ([7.6](#76-placering-och-lagerordning)).

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
| Handpanel digital | `MWSE\Panel\Hand` | Tryckknapp, Panelknapp | Läge Hand/Auto (`Man`), Utsignal Till/Från (`ManV`) | `Man`, `ManV` | 250 × 350 |
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
  ([6.9](#69-kurvblock)) eller i det register som är kopplat till ingången W.
- **Läge och Handvärde** fungerar som i Ventilpanel: handvärdet går att skriva i bara i Hand.
  Det är regulatorns handläge; ventilens eget handläge styrs fortfarande i Ventilpanel. FBoxen
  återställer `Mode` till Auto vid omstart.
- **Inställningarna** är samma symboler som i inställningsblocket och har samma format;
  P-faktorn `PFac00` har två decimaler ([4.4](#44-format-på-pcd-variabler)). Offset och PID:ns
  filtertid finns bara i inställningsblocket ([6.6](#66-inställningsblock),
  [6.7](#67-fler-inställningsblock-och-statusblock)).
- **Handpanel digital och analog** gäller alla tre varianterna av Auto/Hand, digital
  (`MWSE_AHdi`, `MWSE_AHdigS`, `MWSE_AHdigS2`) respektive analog (`MWSE_AHan`, `MWSE_AHanaS`,
  `MWSE_AHanaS2`). I Auto kopierar FBoxen sin ingång till `ManV`, så rutan och knapparna visar
  utsignalen i båda lägena. Utsignalen går att ändra bara i Hand; i Auto är den ritad som inaktiv.
- **Trepunktsventilpanel** skriver ingenting: FBoxen har inget handläge. Ge inte en Utgång,
  analog och en Beräknat ärvärde samma FBox-namn; då öppnas Ventilpanel och Trepunktsventilpanel
  samtidigt. Ge varje FBox ett eget namn.
- Enheten i Digitalsignalpanels listor är s, min eller h (sekunder, minuter, timmar).

Prova panelernas knappar i en anläggning som inte är i drift.

### 6.6 Inställningsblock

Inställningsblocken visar och ändrar en FBox inställningar. De har ingen koppling till
`valdSymbol` och är alltid synliga, så de läggs i ett vanligt lager, gärna i en egen vy för
inställningar. Placera ett block som en faceplate ([6.2](#62-motorpanel)), byt `A.MWSE.Namn.` mot
FBoxens grupp med samma Find and Replace ([5.2](#52-koppla-symbolen-till-objektet)) och skriv
blockets rubrik i textelementet `Namn`, till exempel `GT11 - Tilloppsgivare`.

| Mall | Kategori | Grupp, till exempel | Visar | Skriver | Storlek (px) |
|---|---|---|---|---|---|
| Givarinställning | `MWSE\Inställning\Givare` | givarens, `A.MWSE.VS01.GT11.` | – | Larmgräns låg och hög (`HLLarmGrL`, `HLLarmGrH`), fördröjning och enhet (`HLLarmF`, `HLLarmFenhet`), givarfel låg och hög (`GfLarmGrL`, `GfLarmGrH`) och dess fördröjning (`GfLarmF`) | 310 × 340 |
| Motorinställning | `MWSE\Inställning\Motor` | motorns, `A.MWSE.VS01.P01.` | Drifttimmar (`DTtid`), antal starter (`DTstarter`), senast återställd (`DTdatum`) | Larmgräns och fördröjning för drifttid (`DTLarmGr`, `DTLarmF`); knappen Återställ skriver 1 till `DTreset` | 310 × 350 |
| Regleravvikelseinställning | `MWSE\Inställning\Regleravvikelse` | `A.MWSE.VS01.RegAvv.` | – | Larmgräns (`LarmGrAvv`), fördröjning och enhet (`Fördr`, `TidsEnh`) | 310 × 200 |
| Regulatorinställning | `MWSE\Inställning\Regulator` | `A.MWSE.VS01.Reg.` | Mätvärde (`ActVal`), börvärde (`SetPt`), utsignal (`YOut`) | P-faktor (`PFac00`), I-tid (`IntTime`), offset (`Offset`) | 310 × 430 |
| Pumpstartinställning | `MWSE\Inställning\Pumpstart` | `A.MWSE.VS01.Pumpstart.` | – | Startgräns direkt (`StartGrD`), startgräns och stoppgräns fördröjd (`StartGr`, `StoppGr`), fördröjningarna och deras enheter (`StartF_tid`, `StartF_enhet`, `StoppF_tid`, `StoppF_enhet`) | 310 × 410 |

Utom knappen Återställ är allt i kolumnen Skriver inmatningsrutor eller listor som skriver
direkt till PCD:n. Listorna
för enhet har valen Sekunder, Minuter och Timmar. Knappen Återställ nollställer drifttimmar och
starter. Kurvblocken, också Kurvinställning för Börvärdeskurva 5, står i
[6.9](#69-kurvblock). Kontrollera formaten enligt [4.4](#44-format-på-pcd-variabler) innan du låter någon skriva i
blocken.

### 6.7 Fler inställningsblock och statusblock

Placering och koppling som i [6.6](#66-inställningsblock). Allt som går att skriva i, välja i
eller trycka på skriver direkt till PCD:n. Signalerna står i
[avsnitt 10](#10-mallar-och-fbox-signaler).

**Motor, givare, ventil och spjäll**

| Mall | Kategori | FBox | Innehåll | Storlek (px) |
|---|---|---|---|---|
| Motorfördröjningsinställning | `MWSE\Inställning\Motor` | Motor | Startfördröjning och stoppfördröjning (s) | 310 × 200 |
| Givarskalningsinställning | `MWSE\Inställning\Givare` | Ingång, analog, larm | Filtertid (s), kalibrering, skalning invärde och utvärde, min och max | 310 × 480 |
| Givarhandinställning | `MWSE\Inställning\Givare` | Ingång, analog, larm | Läge Auto/Hand och handvärde | 310 × 200 |
| Ventilskalningsinställning | `MWSE\Inställning\Ventil` | Utgång, analog | Skalning invärde och utvärde, min och max | 310 × 340 |
| Trepunktsventilinställning | `MWSE\Inställning\Ventil` | Beräknat ärvärde, öka/minska | Gångtid min till max och max till min (s), dödzon (%) | 310 × 270 |
| Spjällinställning | `MWSE\Inställning\Spjäll` | Spjällstyrning | Larmfördröjning öppna och stäng (s) | 310 × 200 |

- **Etiketterna i skalningen följer registren, inte A-symbolernas namn.** FBoxarna lägger
  `BPX1`, `BPX2`, `BPY1` och `BPY2` på fyra register i ordningen X1, Y1, X2, Y2. `BPX2` håller
  alltså Y1 och `BPY1` håller X2. Etiketten *Skalning, utvärde min (Y1)* är därför bunden till
  `BPX2`, och *Skalning, invärde max (X2)* till `BPY1`. Byt inte bindningarna. Det gäller både
  Givarskalnings- och Ventilskalningsinställning.
- **Givarhandinställning** handstyr givarens värde: i Hand är `MV_Ut` handvärdet, inte
  mätvärdet. Placera blocket bara där någon ska få göra det.

**Tidkanaler**

| Mall | FBox | Innehåll | Storlek (px) |
|---|---|---|---|
| Veckoschemainställning | Tidkanal m. referens (`MWSE_TKref`) eller m. ingångar (`MWSE_TKing`) | Till och Från för måndag–söndag, dag före helg och helgdag | 640 × 330 |
| Veckodagsinställning | Tidkanal, veckodag (`MWSE_TKM`) | Dag, Tid till, Tid från; Utsignal Till/Från | 310 × 250 |
| Veckopulsinställning | Veckopuls (`MWSE_Vpuls`) | Dag, Tid till, Pulslängd; Enhet visas | 310 × 200 |

- Tiderna skrivs som TT:MM, till exempel 07:30 ([4.4](#44-format-på-pcd-variabler)).
- Veckodagsinställningens Dag har valen måndag–söndag och Permanent från. Permanent stänger av
  tidkanalen. Raden Utsignal visar kanalens utsignal (`V_Ut`), Till eller Från, och går inte att
  ändra. Samma utsignal visar Tidkanalknapp ([5.9](#59-indikator-tryckknapp-och-tidkanalknapp)).
- Veckopulsinställningens Dag har valen måndag–söndag och Alla dagar. Enhet visar pulslängdens
  enhet, 1/10 sekunder, Sekunder, Minuter eller Timmar, och går inte att ändra i blocket: den
  ställs i FBoxens inställningar i Fupla och gäller när programmet byggs.

**Larm**

| Mall | FBox | Innehåll | Storlek (px) |
|---|---|---|---|
| Frysvaktsinställning | Larm, frysvakt (`MWSE_LFrV`) | Larmgräns, återställningsgräns, fördröjning med enhet | 310 × 270 |
| Hög-låglarminställning | Larm HögLåg (`MWSE_RHL`) | Låglarmgräns, höglarmgräns, fördröjning med enhet | 310 × 270 |
| Driftfelsinställning | Larm, driftfel (`MWSE_DRIFTFEL2`) | Fördröjning (s) | 310 × 130 |
| Drifttidsinställning | Larm, drifttid (`MWSE_heaCtW2`) | Larmgräns för drifttid; visar drifttimmar och antal starter; knappen Återställ | 310 × 250 |

- Driftfelets larmvärde (`LarmVal`) är en inställning på FBoxen när programmet byggs, inte ett
  värde i PCD:n, och finns inte i blocket.
- Drifttidsinställningens Återställ skriver till `ResetDT`. Hjälpen kallar signalen `Reset`, men
  FBoxen publicerar `ResetDT`.
- FBoxarnas larmutgångar visas inte i blocken.

**Regulatorer**

| Mall | FBox | Visar | Skriver | Storlek (px) |
|---|---|---|---|---|
| PID-regulatorinställning | Regulator PID (`MWSE_heaPID3`) | Mätvärde, börvärde, utsignal | P-faktor (`PFac00`), I-tid, D-tid, filtertid, offset | 310 × 450 |
| P-regulatorinställning | Regulator P (`MWSE_heaPP2`) | Mätvärde, börvärde, utsignal | P-faktor (`PFac`), arbetspunkt (`PtIni`) | 310 × 360 |

Faceplates med Auto/Hand och inställningarna: Regulatorpanel, PID-regulatorpanel och
P-regulatorpanel ([6.5](#65-fler-faceplates)).

- **Koppla PID-regulatorinställning bara till en Regulator PID.** På en PI-regulator visar D-tid
  och filtertid (`DerTime`, `FT1Time`) `null`, eftersom PI-regulatorn inte har dem.

**Status för handstyrning**

| Mall | FBox | Innehåll | Storlek (px) |
|---|---|---|---|
| Handsummering | Auto/Hand MNG (`MWSE_AH_MNG`) | Antal objekt i Hand (`AntalHand`); knappen Alla till Auto (`TillAuto`) | 310 × 160 |
| Handstatus analog | Auto/Hand, analog via R (`MWSE_MwStatI_`) | Läge och utsignal; S-värdena för Auto och Hand | 310 × 290 |
| Handstatus digital | Auto/Hand, digital via R (`MWSE_StatB`) | Läge och utsignal; S-värdena för Auto, Hand till och Hand från | 310 × 360 |

- **Alla till Auto** sätter alla anslutna Auto/Hand-FBoxar av version 2 i Auto. FBoxen nollställer
  `TillAuto` själv. Raden *Objekt i hand* visas i handfärgen när antalet är större än 0.
- I Handstatus jämför FBoxen ingången S med registervärdena; Läge visar Hand när utsignalen är
  handstyrd.

**Medelvärde och verkningsgrad**

Båda kräver MWSE FBox-biblioteket V25.10.003 eller senare ([avsnitt 2](#2-förutsättningar-i-pg5-programmet)).

| Mall | Kategori | FBox | Innehåll | Storlek (px) |
|---|---|---|---|---|
| Medelvärdesinställning | `MWSE\Inställning\Medelvärde` | Justerat Medel (`MWSE_JUSTERAT_MEDEL`) eller Justerat Medel Array (`MWSE_JUSTERAT_MEDEL_ARRAY`) | Högvärde, Lågvärde, Utvärde vid fel | 310 × 270 |
| Verkningsgradsstatus | `MWSE\Inställning\Värmeväxlare` | Verkningsgrad V2 (`MWSE_VGRAD1_V2`) | Varför larm inte kan genereras; aktuell verkningsgrad (%) | 440 × 190 |

- **Medelvärdesinställning** gäller båda FBoxarna, som har samma signaler. **Högvärde** är hur
  många av de högsta värdena som tas bort innan medelvärdet räknas, **Lågvärde** hur många av de
  lägsta. **Utvärde vid fel** är det värde FBoxen ger ut vid fel, när den är inställd att ge
  utvärdet vid fel; det valet görs på FBoxen i Fupla och finns inte i blocket. Alla tre är heltal.
- **Verkningsgradsstatus** skriver ingenting. Raden *Larm kan inte genereras pga:* visar FBoxens
  status som text: *Larm kan genereras* när inget blockerar larmet, annars orsaken med FBoxens
  egna texter, till exempel `RVVX < Larmblockering, VVX`, `UteT > Gräns utetemp start` eller
  `Valfel`, ensamma eller kombinerade. En okänd statuskod visas som *Okänd status*. Under den står
  aktuell verkningsgrad i procent med en decimal (`PV_Utx`), oavsett larmstatus.
- **Koppla Verkningsgradsstatus till verkningsgrads-FBoxens eget namn**, till exempel
  `A.MWSE.LB01.VVX1.VG.` för FBoxen `LB01.VVX1.VG`, inte till motorns grupp. Blocket kan ligga
  bredvid en Roterande värmeväxlare ([5.7](#57-roterande-värmeväxlare-två-grupper)), som också
  fungerar med Verkningsgrad V2: den har samma signaler `PV_Ut` och `LAL_Ut`.

Kontrollera efter nedladdning att varje block visar FBoxens värden och att ett ändrat värde läses
tillbaka rätt ([4.4](#44-format-på-pcd-variabler)).

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

### 6.9 Kurvblock

Ett kurvblock visar och ändrar en börvärdeskurva: punkterna i en tabell och kurvan ritad bredvid,
i ett kort. Det finns ett block per kurv-FBox:

| Mall | FBox | Tabellen | Storlek (px) |
|---|---|---|---|
| Kurvinställning | Börvärdeskurva 5 (`MWSE_BvK5`) | Ute och Fram för punkt 1–5 | 740 × 380 |
| Kurvinställning 2, 3, 4, 6, 7, 8, 9, 10 | Börvärdeskurva 2–10 (`MWSE_BvK<n>`) | Ute och Fram per punkt | 740 × 380 (2–6), 740 × 420 (7), 910 × 380 (8–10) |
| Kurvinställning med sänkning 2, 5, 6, 7, 8, 9, 10 | Börvärdesk. m. sänkning (`MWSE_BvK<n>s`) | Ute, Fram och Sänkt per punkt | 740 × 380 (2, 5, 6), 740 × 420 (7), 980 × 380 (8–10) |

Kategorin är `MWSE\Inställning\Börvärdeskurva`. Placera och koppla blocket som de andra
inställningsblocken ([6.6](#66-inställningsblock)). Alla kurvblock får plats i en vy på 1020 × 600,
till exempel på (20, 110) under Topplist och en flikrad. MWSE har ingen kurva med sänkning för 3
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

- Grafen är lika stor i alla kurvblock, 240 × 240 px, och står till höger om tabellen.
- Utetemperaturen går längs botten, från kurvans kallaste punkt till vänster till dess varmaste
  till höger. Framledningen går uppåt från Fram min till Fram max. På båda axlarna visar
  etiketterna vid ändarna gränserna och etiketten i mitten deras medelvärde, med en decimal.
  Axlarna följer värdena när de ändras.
- Kurvan är heldragen med punkter; den sänkta kurvan är streckad.
- **En punkt under Fram min eller över Fram max döljs**, liksom kurvdelarna till och från den.
  Alla punkter får plats längs utetemperaturen.
- **Är Fram max lika med eller lägre än Fram min, eller har alla punkter samma utetemperatur,
  ritas ingen kurva.** Bara rutnätet och etiketterna syns tills värdena är rätt.
- Grafen ritas om inom ungefär en sekund när ett värde ändras.
- Grafen visar ingen aktuell driftpunkt. Kurv-FBoxarna publicerar inte sin ingång
  (utetemperaturen) eller utgång (börvärdet) som A-symboler.
- Mittetiketten räknas fram i blocket och förutsätter att gränserna är positiva.

Kontrollera efter nedladdning att tabellen visar FBoxens värden och att kurvan står där tabellen
säger.

## 7. Sidmallar

Sidmallarna bygger själva sidan: larmlista, larmsummering, topplist, meny, sidofält och
inloggning. De binder inte under `A.MWSE.Namn.`:

| Mall | Kategori | Storlek (px) | Kopplas till |
|---|---|---|---|
| Larmlista | `MWSE\Sida\Larm` | 1000 × 480 | Larmlistan i en Alarm PCD init-FBox, under `Namn.` |
| Larmsummering | `MWSE\Sida\Larm` | 100 × 50 | Antalet larm, symbolen `<grupp>.antal`, under `Namn.`; öppnar vyn `Larm` |
| Topplist | `MWSE\Sida\Navigering` | 1020 × 50 | Web8:s inloggningsvariabler, HEAVAC:s klocka, `sidTitel`, `visaMeny`; utloggningen öppnar vyn `login` |
| Navigeringsmeny | `MWSE\Sida\Navigering` | 1020 × 1030 | `visaMeny`; raderna öppnar vyerna `Start`, `Larm`, `Inställningar`, `Info` och `login` |
| Sidofält | `MWSE\Sida\Sidofält` | 250 × 550 | Inget; spalten där panelerna och panelknapparna ligger, med texten ”Ingen panel öppen” där panelerna öppnas tills en panel täcker den |
| Inloggning | `MWSE\Sida\Inloggning` | 360 × 260 | Projektets användardatabas |
| Bakgrund | `MWSE\Sida\Inloggning` | 1020 × 600 | Inget; bakgrundsbild under Inloggning |

Larmlista bygger på Saias Online Alarm List (generation 1), med MWSE:s färger och svenska texter.

### 7.1 Koppla en sidmall

**Larmlista och Larmsummering** binder under `Namn.`, som byts mot larm-FBoxens namn med punkt.
Namnet ska ha **exakt samma stora och små bokstäver** som i PG5-programmet. Gör bytet för varje
sidmall för sig när namnen skiljer sig åt. Heter larm-FBoxen till exempel `Larmlista` men
antalssymbolen `LARMLISTA.antal`, kopplas Larmlista till `Larmlista.` och Larmsummering till
`LARMLISTA.` ([7.2](#72-larmlista-och-larmsummering)).

**Vyerna som sidmallarna öppnar** står som vynamn i instruktionen Open View (ShowView). Har
projektet inte vyer med just de namnen, rikta om dem i fliken **Actions**: markera elementet,
välj händelsen och ändra vynamnet. Skriv vynamnen exakt som i projektets vylista.

| Mall | Element | Vynamn i mallen |
|---|---|---|
| Larmsummering | `Knapp` (MouseUp) | `Larm` |
| Navigeringsmeny | raderna Översikt, Larm, Inställningar, Info, Inloggning, `Knapp` (MouseUp, funktionen `Navigera`) | `Start`, `Larm`, `Inställningar`, `Info`, `login` |
| Topplist | `Logga ut` (MouseUp, funktionen `Logout`) | `login` |

### 7.2 Larmlista och Larmsummering

**Larmlista** visar larmen i en larmlista från FBoxen Alarm PCD init, det senaste larmet överst.
Larm-FBoxen skapar symbolen `A.Alarm.<FBox-namn>.MyName`; mallen har `A.Alarm.Namn.MyName`.
Mallen är ritad som ett inställningsblock: ett kort med ett band överst som bär kolumnrubrikerna,
listan under bandet, bläddringspilarna till höger och en rad med tre knappar längst ner. Den får
plats under Topplist i en vy på 1020 × 600; placera den på (10, 60).

1. Placera `Larmlista` i larmvyn, den vy som Larmsummering och menyn öppnar.
2. Markera mallen, öppna fliken **Template** i Properties och sätt **Alarm List PPO Name** till
   larmlistans symbol, till exempel `A.Alarm.Larmlista.MyName`.
3. Sätt **Alarm List Size** till **antalet larm i larmlistan plus 1** (mallen har 10). Har
   PCD:ns lista 10 larm, sätt 11. Den styr bara hur många texttaggar Web8 skapar: taggarna heter
   `_0` till `_<storlek−1>`, men listan hämtar texten för larm nummer 1 till N. Med storlek 10
   saknar larm 10 sin text och listan visar taggen. Hur många larm listan rymmer bestäms i
   PG5-programmet, se nedan.
4. Larmtexterna läses från en CSV-fil (**Alarm Texts From CSV File** är på). Bygg projektet en
   gång, så skapas taggarna, till exempel `A.Alarm.Larmlista.MyName_1`. Skriv larmtexten för
   tagg 1 till N i CSV-filen; tagg `_0` används inte. Utan text visar listan taggen.

**Fler larm än tio.** Larmlistan finns i PCD:n och skapas av FBoxen Alarm PCD init. Dess
flaggfält (Flags, och Ack, Del och Text om de används) är fält i den lokala symboltabellen, och
fältets storlek är det största antalet larm. Ett fält som `F1001[10]` rymmer alltså tio larm. För
fler larm:

1. Gör fälten större i PG5, alla lika stora, till exempel `[30]`.
2. Koppla varje larm till listan med en FBox 1 alarm, 10 alarms eller 20 alarms. Varje larm får
   ett eget nummer (**Idx**) från 1 till fältets storlek.
3. Sätt **Alarm List Size** i Web8 till fältets storlek plus 1, bygg och skriv texterna för de
   nya taggarna.

Kolumnerna heter ID, Larmtext, Kommande, Gående och Kvitt. Raderna är 38 px höga, så att en
rad går att välja med fingret, och en lång larmtext bryts till två rader. Knapparna:

- **Kvittera vald** kvitterar den valda raden, **Kvittera alla** alla rader. Gångna larm som
  kvitteras försvinner ur listan.
- **Rensa** tar bort larmen ur den aktuella larmlistan, så långt larm-FBoxen tillåter. Larmet
  finns kvar som larmtyp och kommer tillbaka som ny post nästa gång det löser ut. Sätt **System
  clear acceptance** på FBoxen Alarm PCD init till **Off** eller **Ack&Off**, så rensar knappen
  bara återgångna larm. Med **All** rensas även aktiva larm.

Sorteringen är **Sort** = Active Time Sorted (egenskapen `SortMode` = 1): det larm som senast
kom överst, efter Kommande-tiden. Aktiva larm sorteras inte före gångna; ett larm som kommer igen
flyttas upp. Filtret är **Filter Default Value** = Active or NAK Alarms (egenskapen
`FilterMode` = 3): listan visar bara aktiva larm och gångna larm som inte är kvitterade; normala
larm syns inte. Sorteringen och filtret görs i PCD:n, inte i webbläsaren. Kontrollera efter
nedladdning, med två larm, att det senaste står överst.

Mallen har inga knappar för filter, sortering eller kolumner. Ska kolumnerna flyttas, gör det i
egenskapen **Columns** på fliken **Template**.

En rad i taget kan väljas. Kvitt-kolumnen visar körningens egna `NAK` och `ACK`, och den valda
raden ritas svart med vit text. Text i en rad står överst i raden. Allt detta kommer från Web8:s
körning, inte från mallens egenskaper.

**Larmsummering** visar antalet larm med en klocka, i normalfärg när antalet är 0 och i
larmfärg när det är större. Ett klick öppnar vyn `Larm`. Den läser symbolen `Namn.antal`:

1. I Fupla: koppla larm-FBoxens antal (utgången Num) till ett register med symbolen
   `<grupp>.antal`, till exempel `LARMLISTA.antal`.
2. Placera `Larmsummering` på Topplist i projektets förgrundsvy ([7.6](#76-placering-och-lagerordning)).
3. Byt `Namn.antal` mot `<grupp>.antal`: med **Find and Replace Template Group** om dialogen
   erbjuder gruppen, annars **Find what** `Namn.antal`, **Replace with** `LARMLISTA.antal`,
   **Replace All**.
4. Rikta om vyn `Larm` om larmvyn heter något annat ([7.1](#71-koppla-en-sidmall)).

### 7.3 Topplist och sidtitel

**Topplist** är sidans övre list, 1020 × 50 px: menyknapp, Tillbaka, sidtitel, inloggad användare,
klocka och Logga ut. Längst till höger, från x = 920, finns plats för Larmsummering.

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

Kontrollera titeln i webbläsaren efter bygget.

### 7.4 Navigeringsmeny

**Navigeringsmeny** öppnas med Topplists menyknapp. Den är dold tills `visaMeny` = 1 och tar då
emot klick. Menypanelen är 240 px bred till vänster; resten av ytan är en gardin (scrim) som
tonar ned sidan. Ett klick på gardinen eller på krysset stänger menyn.

Raderna är Översikt, Larm, Inställningar, Info och Inloggning. Varje rad öppnar sin vy och
stänger menyn. Rikta om vyerna enligt [7.1](#71-koppla-en-sidmall).

`visaMeny` delas mellan mallarna genom namnet, som `valdSymbol`. Placera Topplist och
Navigeringsmeny en gång, i projektets förgrundsvy, så finns de på alla vyer
([7.6](#76-placering-och-lagerordning)).

### 7.5 Inloggning

**Inloggning** är en inloggningsruta mot projektets användardatabas, som du fyller i Web8:s
användareditor. Efter inloggningen sätts användarens språk, rättigheterna jämförs med de 16
grupperna, och Web8 hoppar till användarens startsida. Mallen använder Saias sessionsvariabler
för inloggningen (bland annat `_NOINITSESSCO_18` och `_NOINITSESSCO_19`, och de som börjar på
`?S_`). Använd dem inte till något annat.

Ge **varje användare en startsida**. Mallen sätter felmarkeringen (`felInloggning`) även efter en
lyckad inloggning. Utan startsida står rutan kvar med fälten markerade som fel.

Prova inloggningen med en testanvändare innan anläggningen tas i drift.

### 7.6 Placering och lagerordning

Det som är lika på alla sidor ligger i två egna vyer: en förgrundsvy (foreground view) och en
bakgrundsvy (background view). Gör förgrundsvyn till projektets standard under **Project
Options**. Nya vyer har egenskaperna för förgrunds- och bakgrundsvy satt till **Project Default**
och får då förgrundsvyn utan att du lägger något i dem. En vy som inte ska ha listen, som
inloggningsvyn, får ingen förgrundsvy. Förgrundsvyn ritas efter vyn och ligger därför ovanpå
vyns faceplates.

För vyer på 1020 × 600 px:

1. I förgrundsvyn: **Topplist** på (0, 0), sedan **Larmsummering** på (920, 0), **efter**
   Topplist så att den ritas ovanpå, och sist **Navigeringsmeny** på (0, 50), direkt under
   topplisten. Menyn och gardinen räcker till nederkanten av en vy som är upp till 1080 px hög.
   I en lägre vy går de utanför vyn i editorn, men i körningen skärs de av vid vyns kant.
2. I bakgrundsvyn: det som ska ligga under allt på sidan, till exempel en bakgrundsbild.
3. **Sidofält** på (770, 50), i ett lager under `Paneler`, på varje vy som har faceplates eller
   panelknappar. Vyer utan dem, som larmvyn, använder hela bredden. I sidofältet öppnas alla
   faceplates överst, på (770, 50) i lagret `Paneler`
   ([6.1](#61-lager-för-faceplates)), och panelknapparna läggs under dem, från (780, 410) och
   50 px nedåt per knapp, så att de har 10 px luft mot sidofältets kanter, panelen och varandra. Vyns länk till sina inställningar (en knapp med pil) ligger längst ner
   i sidofältet.
4. Vyns symboler och inställningsblock under topplisten och till vänster om sidofältet, x under
   770.
5. **Larmlista** i larmvyn. I inloggningsvyn: **Bakgrund** på (0, 0) först och **Inloggning**
   ovanpå den.

Låt knapparnas utseende säga vad de gör, på alla vyer:

- **Till en annan vy:** en knapp med pil, som menyns rader.
- **Öppnar en faceplate:** Panelknapp, utan pil, i sidofältet.
- **Byter mellan undersidor på samma sida**, till exempel inställningssidor: flikar i en rad
  överst, utan pil, där fliken för den sida som visas är markerad som vald.

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
| Pump | `MWSE\Symbol\Motor` | Motor (`MWSE_Motor`) | `R_Ut`, `LD_Ut`, `LDT_Ut`, `Man` | 2.1.0 |
| Fläkt | `MWSE\Symbol\Motor` | Motor | `R_Ut`, `LD_Ut`, `LDT_Ut`, `Man` | 2.1.0 |
| Motor | `MWSE\Symbol\Motor` | Motor | `R_Ut`, `LD_Ut`, `LDT_Ut`, `Man` | 2.1.0 |
| Kompressor | `MWSE\Symbol\Motor` | Motor ¹ | `R_Ut`, `LD_Ut`, `LDT_Ut`, `Man` | 2.1.0 |
| Spjäll | `MWSE\Symbol\Ställdon` | Spjällstyrning (`MWSE_Lflaps2`) | `R_Ut`, `LA_Ut`, `Man` | 2.1.0 |
| Brandspjäll | `MWSE\Symbol\Ställdon` | Spjällstyrning ¹ | `R_Ut`, `LA_Ut`, `Man` | 2.1.0 |
| Tvåvägsventil | `MWSE\Symbol\Ställdon` | Utgång, analog (`MWSE_UtAna`) | `MV_Ut`, `Man` | 2.1.0 |
| Trevägsventil | `MWSE\Symbol\Ställdon` | Utgång, analog ² | `MV_Ut`, `Man` | 2.1.0 |
| Trepunktsventil | `MWSE\Symbol\Ställdon` | Beräknat ärvärde, öka/minska (`MWSE_BAROM`) | `BeräknVärde`, `IncUt`, `DecUt` | 1.1.0 |
| Givare | `MWSE\Symbol\Givare` | Ingång, analog, larm (`MWSE_InAnaL`) | `MV_Ut`, `LG_Ut`, `LL_Ut`, `HL_Ut`, `Man` | 2.1.0 |
| Utegivare | `MWSE\Symbol\Givare` | Ingång, analog, larm | `MV_Ut`, `LG_Ut`, `LL_Ut`, `HL_Ut`, `Man` | 2.1.0 |
| Differensgivare | `MWSE\Symbol\Givare` | Ingång, analog, larm ³ | `MV_Ut`, `LG_Ut`, `LL_Ut`, `HL_Ut`, `Man` | 2.1.0 |
| Integrerad Givare | `MWSE\Symbol\Givare` | Ingång, analog, larm ³ | `MV_Ut`, `LG_Ut`, `LL_Ut`, `HL_Ut`, `Man` | 2.1.0 |
| Roterande värmeväxlare | `MWSE\Symbol\Värmeväxlare` | Motor och, under `VG.`, Verkningsgrad (`MWSE_VGRAD1`) | `R_Ut`, `LD_Ut`, `LDT_Ut`, `Man`, `VG.PV_Ut`, `VG.LAL_Ut` | 1.1.0 |
| Panelknapp | `MWSE\Symbol\Knapp` | – (gruppen för panelen den öppnar) | – | 1.1.0 |
| Indikator | `MWSE\Symbol\Indikator` | Larm, driftfel (`MWSE_DRIFTFEL2`) | `StaUt`, `LrmUt` | 1.0.0 |
| Tryckknapp | `MWSE\Symbol\Knapp` | Auto/Hand, digital (`MWSE_AHdi`, `MWSE_AHdigS`, `MWSE_AHdigS2`) | `ManV`, `Man` | 1.0.0 |
| Tidkanalknapp | `MWSE\Symbol\Tidkanal` | Tidkanal, veckodag (`MWSE_TKM`) | `V_Ut` | 1.0.0 |
| Värmebatteri, Kylbatteri, Kombibatteri, Filter, Radiator, Värmetank, Krets, Krets med bypass, Krets med backventil, Tank, Liten tank, Värmeväxlare X, Värmeväxlare V, Plattvärmeväxlare | `MWSE\Symbol\Statisk` | – | – | 1.1.0 (kretsarna), 1.0.1 (batterierna, Radiator, tankarna), 1.0.0 (Filter, växlarna) |

**Faceplates**

| Mall | Kategori i Web8 | MWSE-FBox | Signaler | Version |
|---|---|---|---|---|
| Motorpanel | `MWSE\Panel\Motor` | Motor | `LD_Ut`, `LDT_Ut`, `Man`, `ManV`, `DTtid`, `DTstarter` | 1.0.2 |
| Givarpanel | `MWSE\Panel\Givare` | Ingång, analog, larm | `MV_Ut`, `LG_Ut`, `LL_Ut`, `HL_Ut`, `HLLarmGrL`, `HLLarmGrH` | 1.0.1 |
| Ventilpanel | `MWSE\Panel\Ventil` | Utgång, analog | `MV_Ut`, `Min`, `Max`, `Man`, `ManV` | 1.0.2 |
| Spjällpanel | `MWSE\Panel\Spjäll` | Spjällstyrning | `LA_Ut`, `Man`, `ManV` | 1.0.2 |
| Trepunktsventilpanel | `MWSE\Panel\Ventil` | Beräknat ärvärde, öka/minska | `BeräknVärde`, `IncUt`, `DecUt`, `KalUt` | 1.0.1 |
| Regulatorpanel | `MWSE\Panel\Regulator` | Regulator PI (`MWSE_heaPI3`) | `Mode`, `ManVal`, `ActVal`, `SetPt`, `YOut`, `PFac00`, `IntTime` | 1.0.2 |
| PID-regulatorpanel | `MWSE\Panel\Regulator` | Regulator PID (`MWSE_heaPID3`) | `Mode`, `ManVal`, `ActVal`, `SetPt`, `YOut`, `PFac00`, `IntTime`, `DerTime` | 1.0.2 |
| P-regulatorpanel | `MWSE\Panel\Regulator` | Regulator P (`MWSE_heaPP2`) | `Mode`, `ManVal`, `ActVal`, `SetPt`, `YOut`, `PFac`, `PtIni` | 1.0.2 |
| Handpanel digital | `MWSE\Panel\Hand` | Auto/Hand, digital (`MWSE_AHdi`, `MWSE_AHdigS`, `MWSE_AHdigS2`) | `Man`, `ManV` | 1.0.2 |
| Handpanel analog | `MWSE\Panel\Hand` | Auto/Hand, analog (`MWSE_AHan`, `MWSE_AHanaS`, `MWSE_AHanaS2`) | `Man`, `ManV` | 1.0.2 |
| Digitalsignalpanel | `MWSE\Panel\Hand` | Digital signal (`MWSE_DigSignal`) | `Man`, `ManV`, `Invertering`, `StartF_tid`, `StartF_enhet`, `StoppF_tid`, `StoppF_enhet` | 1.0.2 |

**Inställningar och status**

| Mall | Kategori i Web8 | MWSE-FBox | Signaler | Version |
|---|---|---|---|---|
| Givarinställning | `MWSE\Inställning\Givare` | Ingång, analog, larm | `HLLarmGrL`, `HLLarmGrH`, `HLLarmF`, `HLLarmFenhet`, `GfLarmGrL`, `GfLarmGrH`, `GfLarmF` | 1.1.0 |
| Givarskalningsinställning | `MWSE\Inställning\Givare` | Ingång, analog, larm | `Filtertid`, `Kalibrering`, `BPX1`, `BPX2`, `BPY1`, `BPY2` | 1.1.0 |
| Givarhandinställning | `MWSE\Inställning\Givare` | Ingång, analog, larm | `Man`, `ManV` | 1.1.0 |
| Motorinställning | `MWSE\Inställning\Motor` | Motor | `DTLarmGr`, `DTLarmF`, `DTtid`, `DTstarter`, `DTdatum`, `DTreset` | 1.1.0 |
| Motorfördröjningsinställning | `MWSE\Inställning\Motor` | Motor | `StartF`, `StoppF` | 1.1.0 |
| Ventilskalningsinställning | `MWSE\Inställning\Ventil` | Utgång, analog | `BPX1`, `BPX2`, `BPY1`, `BPY2` | 1.1.0 |
| Trepunktsventilinställning | `MWSE\Inställning\Ventil` | Beräknat ärvärde, öka/minska | `GångtidÖ`, `GångtidM`, `Dödzon` | 1.1.0 |
| Spjällinställning | `MWSE\Inställning\Spjäll` | Spjällstyrning | `LarmF_O`, `LarmF_S` | 1.1.0 |
| Regleravvikelseinställning | `MWSE\Inställning\Regleravvikelse` | Larm, regleravvikelse (`MWSE_RAVB`) | `LarmGrAvv`, `Fördr`, `TidsEnh` | 1.1.0 |
| Regulatorinställning | `MWSE\Inställning\Regulator` | Regulator PI (`MWSE_heaPI3`) | `ActVal`, `SetPt`, `YOut`, `PFac00`, `IntTime`, `Offset` | 1.1.0 |
| PID-regulatorinställning | `MWSE\Inställning\Regulator` | Regulator PID (`MWSE_heaPID3`) | `ActVal`, `SetPt`, `YOut`, `PFac00`, `IntTime`, `DerTime`, `FT1Time`, `Offset` | 1.1.0 |
| P-regulatorinställning | `MWSE\Inställning\Regulator` | Regulator P (`MWSE_heaPP2`) | `ActVal`, `SetPt`, `YOut`, `PFac`, `PtIni` | 1.1.0 |
| Kurvinställning | `MWSE\Inställning\Börvärdeskurva` | Börvärdeskurva 5 (`MWSE_BvK5`) | `BPX1`–`BPX5`, `BPY1`–`BPY5`, `YminV`, `YmaxV` | 2.2.0 |
| Kurvinställning 2, 3, 4, 6, 7, 8, 9, 10 | `MWSE\Inställning\Börvärdeskurva` | Börvärdeskurva *n* (`MWSE_BvK<n>`) | `BPX1`–`BPX<n>`, `BPY1`–`BPY<n>`, `YminV`, `YmaxV` | 2.2.0 |
| Kurvinställning med sänkning 2, 5, 6, 7, 8, 9, 10 | `MWSE\Inställning\Börvärdeskurva` | Börvärdesk. m. sänkning *n* (`MWSE_BvK<n>s`) | `BPX1`–`BPX<n>`, `BPY1`–`BPY<n>`, `BPY1n`–`BPY<n>n`, `YminV`, `YmaxV` | 2.2.0 |
| Pumpstartinställning | `MWSE\Inställning\Pumpstart` | Pumpstart (`MWSE_Pumpst`) | `StartGrD`, `StartGr`, `StartF_tid`, `StartF_enhet`, `StoppGr`, `StoppF_tid`, `StoppF_enhet` | 1.1.0 |
| Veckoschemainställning | `MWSE\Inställning\Tidkanal` | Tidkanal m. referens eller m. ingångar (`MWSE_TKref`, `MWSE_TKing`) | `månTill`, `månFrån`, `tisTill`, `tisFrån`, `onsTill`, `onsFrån`, `torsTill`, `torsFrån`, `freTill`, `freFrån`, `lörTill`, `lörFrån`, `sönTill`, `sönFrån`, `dfhTill`, `dfhFrån`, `hdTill`, `hdFrån` | 1.1.0 |
| Veckodagsinställning | `MWSE\Inställning\Tidkanal` | Tidkanal, veckodag (`MWSE_TKM`) | `Dag`, `TidT`, `TidF`, `V_Ut` | 1.2.0 |
| Veckopulsinställning | `MWSE\Inställning\Tidkanal` | Veckopuls (`MWSE_Vpuls`) | `Dag`, `TidTill`, `Pulslängd`, `Tidsenhet` | 1.2.0 |
| Frysvaktsinställning | `MWSE\Inställning\Larm` | Larm, frysvakt (`MWSE_LFrV`) | `LGr`, `HGr`, `LarmF`, `Enhet` | 1.1.0 |
| Hög-låglarminställning | `MWSE\Inställning\Larm` | Larm HögLåg (`MWSE_RHL`) | `LarmGrL`, `LarmGrH`, `TidsF`, `Enhet` | 1.1.0 |
| Driftfelsinställning | `MWSE\Inställning\Larm` | Larm, driftfel (`MWSE_DRIFTFEL2`) | `LarmF` | 1.1.0 |
| Drifttidsinställning | `MWSE\Inställning\Larm` | Larm, drifttid (`MWSE_heaCtW2`) | `LarmGr`, `AntalTimmar`, `AntalStarter`, `ResetDT` | 1.1.0 |
| Handsummering | `MWSE\Inställning\Status` | Auto/Hand MNG (`MWSE_AH_MNG`) | `AntalHand`, `TillAuto` | 1.1.0 |
| Handstatus analog | `MWSE\Inställning\Status` | Auto/Hand, analog via R (`MWSE_MwStatI_`) | `Man_Ut`, `V_Ut`, `RegisterV1`, `RegisterV2` | 1.1.0 |
| Handstatus digital | `MWSE\Inställning\Status` | Auto/Hand, digital via R (`MWSE_StatB`) | `ManUt`, `V_Ut`, `RegisterV1`, `RegisterV2`, `RegisterV3` | 1.1.0 |
| Medelvärdesinställning | `MWSE\Inställning\Medelvärde` | Justerat Medel (`MWSE_JUSTERAT_MEDEL`), Justerat Medel Array (`MWSE_JUSTERAT_MEDEL_ARRAY`) | `Högvärde`, `Lågvärde`, `Utvärde` | 1.0.0 |
| Verkningsgradsstatus | `MWSE\Inställning\Värmeväxlare` | Verkningsgrad V2 (`MWSE_VGRAD1_V2`) | `StatusText`, `PV_Utx` | 1.0.0 |

**Sidmallar**

| Mall | Kategori i Web8 | Binds till | Version |
|---|---|---|---|
| Larmlista | `MWSE\Sida\Larm` | `A.Alarm.Namn.MyName` (egenskapen Alarm List PPO Name) | 1.1.0 |
| Larmsummering | `MWSE\Sida\Larm` | `Namn.antal` | 1.1.0 |
| Topplist | `MWSE\Sida\Navigering` | `S.HVC.Clock.HHMMSS`, `S.HVC.Clock.YYYYMMDD`; interna `visaMeny`, `sidTitel`; Web8:s inloggningsvariabler | 1.1.0 |
| Navigeringsmeny | `MWSE\Sida\Navigering` | intern `visaMeny` | 1.1.0 |
| Sidofält | `MWSE\Sida\Sidofält` | – | 1.1.0 |
| Inloggning | `MWSE\Sida\Inloggning` | projektets användardatabas och Web8:s inloggningsvariabler | 1.1.0 |
| Bakgrund | `MWSE\Sida\Inloggning` | – | 1.0.0 |

Alla symboler utom Indikator och Tidkanalknapp, Panelknapp och faceplates använder dessutom den
interna variabeln `valdSymbol`; symbolerna och de statiska symbolerna även `doljNamn`.

**Att välja FBox.** För de markerade mallarna gäller:

1. **Kompressor och Brandspjäll:** MWSE har ingen egen FBox för kompressor eller brandspjäll.
   Motor och Spjällstyrning är de som passar bäst, och mallarna fungerar när objektet styrs med
   dem. Brandspjäll visar Öppet när `R_Ut` = 1 och fel på `LA_Ut`, precis som Spjäll. Ett objekt
   som styrs med en FBox utanför MWSE-biblioteket skapar inga `A.MWSE`-symboler och kan inte
   kopplas.
2. **Trevägsventil:** passar ett ställdon 0–10 V som styrs med Utgång, analog. Ett
   trelägesställdon (öka/minska) styrs med FBoxen Beräknat ärvärde, öka/minska; använd då
   `Trepunktsventil`.
3. **Differensgivare och Integrerad Givare:** givare på Ingång, analog, larm, som Givare.

Kontrollera efter bygget att PCD Variables Editor inte visar några röda rader för mallarnas
signaler ([avsnitt 8](#8-bygga-och-ladda-ned)).

## 11. Mer att läsa

- **Web Editor 8-hjälpen**, som installeras med Saia PG5: mallar och biblioteket, Cross-Reference,
  PCD Variables Editor, Condition Editor, Actions, interna variabler, Online Alarm List, Login
  Dialog och bygge och nedladdning.
- **MWSE FBox-hjälpen**, som följer med MWSE FBox-biblioteket: varje FBox sida, med avsnitten
  "Beskrivning" och "A-symboler".

## 12. Fel och förslag

MWSE Web8 Library ges ut av Malthe Winje Automation AB. Rapportera fel och föreslå förbättringar
som ett issue i [förrådet på GitHub](https://github.com/mwa-eas/MWSE-Web8-Library/issues), med
formuläret **Fel eller förslag**. Ange bibliotekets version och bifoga gärna en skärmbild.

Förrådet är publikt: skriv inte anläggningsnamn, IP-adresser, inloggningar eller kunduppgifter i
ett issue, och maskera sådant i skärmbilder.
