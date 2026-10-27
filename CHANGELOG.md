# Ändringslogg

Ändringar i MWSE Web8 Library, mallar för Saia PG5 Web Editor 8 bundna till
A-symbolerna i MWSE FBox-biblioteket. Rubriken anger paketets version. Varje mall har dessutom
en egen version, som visas i Web8:s mallbibliotek.

## 0.9.0-beta.2 (tidig åtkomst, 2026-10-27)

Andra förhandsversionen. Paketet har nu också `NOTICE` med villkoren för Larmlistas delar från
Saia; `LICENSE` är den rena MIT-licensen.

Hela biblioteket ligger på Web8:s rutnät om 10 px: mallarnas storlekar och platser, varje ram,
fält, knapp, band och rad, och där en kanal eller ledning möter en symbol. Alla knappar och fält
är 40 px höga; bredden följer platsen eller innehållet.

- Topplist 1.1.0 är 1020 × 50 px. Larmsummering 1.1.0 är 100 × 50 px och läggs på (920, 0).
  Sidofält 1.1.0 läggs på (770, 50) och är 250 × 550 px. Navigeringsmeny 1.1.0 läggs på (0, 50).
- Panelknapp 1.1.0 är 230 × 40 px och läggs på (780, 410), 50 px nedåt per knapp, med 10 px luft
  mot sidofältets kanter, panelen ovanför och varandra.
- Larmlista 1.1.0 är 1000 × 480 px och läggs 10 px från vyns kant.
- Inloggning 1.1.0 är 360 × 260 px.
- Symbolerna (2.1.0, Trepunktsventil 1.1.0) är 80 × 100 px: bilden ligger 5 px lägre, så att
  kanalen möter dess mitt 50 px ner. Utegivare är 140 × 60 px. Roterande värmeväxlare 1.1.0 möter
  kanalerna 40 och 210 px ner.
- Batteriernas låda är 20 px (Värmebatteri, Kylbatteri och Kombibatteri 1.0.1), så att kretsen
  hänger 10 px under kanalen. Kretsarna 1.1.0 är 60 × 140 px med 120 px långa ben. Radiator 1.0.1
  tar ledningarna 20 och 70 px in. Tankarnas väggar ligger på rutnätet (Tank och Värmetank 1.0.1);
  Liten tank 1.0.1 är 60 × 60 px.
- Faceplates är fortfarande 250 × 350 px, med raderna på rutnätet och krysset i en ruta på
  50 × 50 px.
- Inställningsblocken 1.1.0 behåller bredden och blir 10–60 px lägre.
- Kurvblocken 2.1.0 är 740, 910 eller 980 px breda och 380 eller 420 px höga. Grafen är lika stor
  i alla, 250 × 240 px.

Flytta redan placerade mallar till de nya platserna, och dra om kanaler och ledningar till
symbolerna på rutnätet.

Alla knappar har raka hörn och ritas som Panelknapp: en Web8-rektangel med bara ikonen som bild.
Larmsummering blir röd genom rektangelns färg i stället för genom att byta hela bilden.
Knappbilderna med ram finns inte längre i `grafik/`.

## 0.9.0-beta.1 (tidig åtkomst, 2026-10-27)

Första förhandsversionen. Mallarna är prövade mot en simulerad PCD och, där guiden säger det, mot
en riktig PCD; import och koppling i Web8-editorn är ännu inte prövade för alla mallar. Rapportera
fel och förslag under Issues.

För MWSE FBox-biblioteket V20.6.001; A-symbolerna som mallarna använder finns kvar med samma namn
i V25.10.003 och V26.09.015. Undantag: Givarskalningsinställning med en nyare Analog ingång, se
integratörsguidens avsnitt 2.

Första utgåvan som nedladdningspaket.

Larmfärgen är SmartFrames larm hög (alarm high): fyllning #EFAD9C, kant #B10000, larmtext #5F1506.

P-faktorn (`PFac00`) i Regulatorinställning och PID-regulatorinställning visas med två decimaler
(`DEC.2`), eftersom FBoxen anger den i hundradelar. Referens-GUI:t visar den med en decimal,
tio gånger för stort.

Kurvblocken (Kurvinställning, Kurvinställning 2–10 och med sänkning 2 och 5–10) är version 2.0.0:
punkterna i en tabell med Fram min och Fram max under, och kurvan ritad bredvid i samma kort.
De ersätter version 1.0.0, ett fält per värde, och de separata kurvdiagrammen, som inte längre
finns i biblioteket.

### Symboler

| Mall | Kategori i Web8 | Mallversion |
|---|---|---|
| Pump | `MWSE\Symbol\Motor` | 2.0.0 |
| Fläkt | `MWSE\Symbol\Motor` | 2.0.0 |
| Motor | `MWSE\Symbol\Motor` | 2.0.0 |
| Kompressor | `MWSE\Symbol\Motor` | 2.0.0 |
| Spjäll | `MWSE\Symbol\Ställdon` | 2.0.0 |
| Brandspjäll | `MWSE\Symbol\Ställdon` | 2.0.0 |
| Tvåvägsventil | `MWSE\Symbol\Ställdon` | 2.0.0 |
| Trevägsventil | `MWSE\Symbol\Ställdon` | 2.0.0 |
| Givare | `MWSE\Symbol\Givare` | 2.0.0 |
| Differensgivare | `MWSE\Symbol\Givare` | 2.0.0 |
| Integrerad Givare | `MWSE\Symbol\Givare` | 2.0.0 |
| Utegivare | `MWSE\Symbol\Givare` | 2.0.0 |
| Trepunktsventil | `MWSE\Symbol\Ställdon` | 1.0.0 |
| Roterande värmeväxlare | `MWSE\Symbol\Värmeväxlare` | 1.0.0 |
| Panelknapp | `MWSE\Symbol\Knapp` | 1.0.0 |

Trepunktsventil binds till Trepunktsventil (`MWSE_BAROM`) och har bara lägena vila och aktiv,
eftersom FBoxen inte har någon larmutgång. Roterande värmeväxlare binds till rotorns motor
(`MWSE_MOTOR`) och till verkningsgradsblocket (`MWSE_VGRAD1`), som ska heta `<motornamn>.VG`.
Panelknapp är en knapp som öppnar panelen för sin grupp, för paneler utan egen symbol
(regulatorpanelerna och handpanelerna). Den har ingen pil, eftersom pilen betyder byte av vy.

### Statiska symboler

Bilder utan signaler, med en namnetikett: Värmebatteri, Kylbatteri, Kombibatteri, Filter,
Radiator, Värmetank, Krets, Krets med bypass, Krets med backventil, Tank, Liten tank,
Värmeväxlare X, Värmeväxlare V och Plattvärmeväxlare (vatten–vatten, till exempel i en
fjärrvärmecentral). Kategori `MWSE\Symbol\Statisk`, mallversion 1.0.0.

### Paneler

| Mall | Kategori i Web8 | Mallversion | Används av |
|---|---|---|---|
| Motorpanel | `MWSE\Panel\Motor` | 1.0.0 | Pump, Fläkt, Motor, Kompressor |
| Givarpanel | `MWSE\Panel\Givare` | 1.0.0 | Givare, Utegivare, Differensgivare, Integrerad Givare |
| Ventilpanel | `MWSE\Panel\Ventil` | 1.0.0 | Tvåvägsventil, Trevägsventil |
| Spjällpanel | `MWSE\Panel\Spjäll` | 1.0.0 | Spjäll, Brandspjäll (`MWSE_Lflaps2`) |
| Trepunktsventilpanel | `MWSE\Panel\Ventil` | 1.0.0 | Trepunktsventil (`MWSE_BAROM`) |
| Regulatorpanel | `MWSE\Panel\Regulator` | 1.0.0 | Regulator PI (`MWSE_heaPI3`): mätvärde, börvärde (visas bara), utsignal, Läge, Handvärde, P-faktor, I-tid; öppnas av en egen knapp |
| PID-regulatorpanel | `MWSE\Panel\Regulator` | 1.0.0 | Regulator PID (`MWSE_heaPID3`): som Regulatorpanel och D-tid; öppnas av en egen knapp |
| P-regulatorpanel | `MWSE\Panel\Regulator` | 1.0.0 | Regulator P (`MWSE_heaPP2`): mätvärde, börvärde (visas bara), utsignal, Läge, Handvärde, P-faktor, arbetspunkt; öppnas av en egen knapp |
| Handpanel digital | `MWSE\Panel\Hand` | 1.0.0 | Handstyrning digital (`MWSE_AHdi`, `MWSE_AHdigS`, `MWSE_AHdigS2`); öppnas av en egen knapp |
| Handpanel analog | `MWSE\Panel\Hand` | 1.0.0 | Handstyrning analog (`MWSE_AHan`, `MWSE_AHanaS`, `MWSE_AHanaS2`); öppnas av en egen knapp |
| Digitalsignalpanel | `MWSE\Panel\Hand` | 1.0.0 | Digital signal (`MWSE_DigSignal`); öppnas av en egen knapp |

### Inställningar

| Mall | Kategori i Web8 | Mallversion | FBox |
|---|---|---|---|
| Givarinställning | `MWSE\Inställning\Givare` | 1.0.0 | Ingång, analog, larm (`MWSE_INANAL`) |
| Motorinställning | `MWSE\Inställning\Motor` | 1.0.0 | Motor (`MWSE_MOTOR`) |
| Regleravvikelseinställning | `MWSE\Inställning\Regleravvikelse` | 1.0.0 | Larm, regleravvikelse (`MWSE_RAVB`) |
| Regulatorinställning | `MWSE\Inställning\Regulator` | 1.0.0 | Regulator PI (`MWSE_HEAPI3`) |
| Kurvinställning | `MWSE\Inställning\Börvärdeskurva` | 2.0.0 | Börvärdeskurva 5 (`MWSE_BVK5`): tabell och kurva |
| Pumpstartinställning | `MWSE\Inställning\Pumpstart` | 1.0.0 | Pumpstart (`MWSE_PUMPST`) |
| Motorfördröjningsinställning | `MWSE\Inställning\Motor` | 1.0.0 | Motor (`MWSE_MOTOR`): start- och stoppfördröjning |
| Givarskalningsinställning | `MWSE\Inställning\Givare` | 1.0.0 | Ingång, analog, larm (`MWSE_INANAL`): filtertid, kalibrering, skalning |
| Givarhandinställning | `MWSE\Inställning\Givare` | 1.0.0 | Ingång, analog, larm (`MWSE_INANAL`): handläge och handvärde |
| Ventilskalningsinställning | `MWSE\Inställning\Ventil` | 1.0.0 | Utgång, analog (`MWSE_UTANA`): skalning |
| Trepunktsventilinställning | `MWSE\Inställning\Ventil` | 1.0.0 | Trepunktsventil (`MWSE_BAROM`): gångtider, dödzon |
| Spjällinställning | `MWSE\Inställning\Spjäll` | 1.0.0 | Spjäll (`MWSE_Lflaps2`): larmfördröjningar |
| Kurvinställning 2, 3, 4, 6, 7, 8, 9, 10 | `MWSE\Inställning\Börvärdeskurva` | 2.0.0 | Börvärdeskurva med 2–10 punkter (`MWSE_BvK2` … `MWSE_BvK10`): tabell med min och max, och kurva |
| Kurvinställning med sänkning 2, 5, 6, 7, 8, 9, 10 | `MWSE\Inställning\Börvärdeskurva` | 2.0.0 | Börvärdeskurva med sänkning (`MWSE_BvK2s`, `MWSE_BvK5s` … `MWSE_BvK10s`): tabell med min och max, och kurva med den sänkta kurvan |
| Veckoschemainställning | `MWSE\Inställning\Tidkanal` | 1.0.0 | Tidkanal, veckoschema (`MWSE_TKref`, `MWSE_TKing`) |
| Veckodagsinställning | `MWSE\Inställning\Tidkanal` | 1.0.0 | Tidkanal, en veckodag (`MWSE_TKM`) |
| Veckopulsinställning | `MWSE\Inställning\Tidkanal` | 1.0.0 | Veckopuls (`MWSE_Vpuls`) |
| Frysvaktsinställning | `MWSE\Inställning\Larm` | 1.0.0 | Frysvakt (`MWSE_LFrV`) |
| Hög-låglarminställning | `MWSE\Inställning\Larm` | 1.0.0 | Hög- och låglarm (`MWSE_RHL`) |
| Driftfelsinställning | `MWSE\Inställning\Larm` | 1.0.0 | Driftfel (`MWSE_DRIFTFEL2`) |
| Drifttidsinställning | `MWSE\Inställning\Larm` | 1.0.0 | Drifttid (`MWSE_heaCtW2`), med återställning |
| PID-regulatorinställning | `MWSE\Inställning\Regulator` | 1.0.0 | Regulator PID (`MWSE_heaPID3`) |
| P-regulatorinställning | `MWSE\Inställning\Regulator` | 1.0.0 | Regulator P (`MWSE_heaPP2`) |
| Handsummering | `MWSE\Inställning\Status` | 1.0.0 | Handstyrningar i anläggningen (`MWSE_AH_MNG`), med Alla till Auto |
| Handstatus analog | `MWSE\Inställning\Status` | 1.0.0 | Auto/hand via register, analog (`MWSE_MwStatI_`) |
| Handstatus digital | `MWSE\Inställning\Status` | 1.0.0 | Auto/hand via register, digital (`MWSE_StatB`) |

### Sidor

| Mall | Kategori i Web8 | Mallversion | Binds till |
|---|---|---|---|
| Larmlista | `MWSE\Sida\Larm` | 1.0.0 | Larmlistan från FBoxen Alarm PCD init (`A.Alarm.<namn>.MyName`). Bygger på Saias Online Alarm List (generation 1), förenklad som labbets larmlista: senaste larmet överst, bara aktiva eller okvitterade larm, tre knappar (Kvittera vald, Kvittera alla, Rensa) och bläddringspilar, utan filter, sortering, kolumnval, flerval, rubrikrad och rullningslist. Ritad som ett inställningsblock med kolumnrubrikerna i bandet, Arial 12 och 38 px rader |
| Larmsummering | `MWSE\Sida\Larm` | 1.0.0 | Antal larm från samma FBox (`<namn>.antal`); öppnar larmvyn |
| Topplist | `MWSE\Sida\Navigering` | 1.0.0 | Meny, bakåt, sidtitel (`sidTitel`), inloggad användare, PCD:ns klocka (`S.HVC.Clock`), utloggning. Plats för Larmsummering längst till höger |
| Navigeringsmeny | `MWSE\Sida\Navigering` | 1.0.0 | Öppnas från Topplist; knappar till vyerna Start, Larm, Inställningar, Info och login, som integratören riktar om i Actions |
| Sidofält | `MWSE\Sida\Sidofält` | 1.0.0 | Inget. Den fasta högerspalten där panelerna öppnas och panelknapparna ligger, med en hänvisning när ingen panel är öppen |
| Inloggning | `MWSE\Sida\Inloggning` | 1.0.0 | Web8:s egen inloggning mot användarlistan i projektet, med MWSE:s färger |
| Bakgrund | `MWSE\Sida\Inloggning` | 1.0.0 | Inget. Bakgrundsbild för inloggningsvyn |
