# Ändringslogg

Ändringar i MWSE Web8 Library, mallar för Saia PG5 Web Editor 8 bundna till
A-symbolerna i MWSE FBox-biblioteket. Rubriken anger paketets version. Varje mall har dessutom
en egen version, som visas i Web8:s mallbibliotek.

## 1.0.0 (ej utgiven)

Första utgåvan som nedladdningspaket.

Larmfärgen är SmartFrames larm hög (alarm high): fyllning #EFAD9C, kant #B10000, larmtext #5F1506.

P-faktorn (`PFac00`) i Regulatorinställning och PID-regulatorinställning visas med två decimaler
(`DEC.2`), eftersom FBoxen anger den i hundradelar. Referens-GUI:t visar den med en decimal,
tio gånger för stort.

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
(Regulatorpanel och handpanelerna). Den har ingen pil, eftersom pilen betyder byte av vy.

### Statiska symboler

Bilder utan signaler, med en namnetikett: Värmebatteri, Kylbatteri, Kombibatteri, Filter,
Radiator, Värmetank, Krets, Krets med bypass, Krets med backventil, Tank, Liten tank,
Värmeväxlare X och Värmeväxlare V. Kategori `MWSE\Symbol\Statisk`, mallversion 1.0.0.

### Paneler

| Mall | Kategori i Web8 | Mallversion | Används av |
|---|---|---|---|
| Motorpanel | `MWSE\Panel\Motor` | 1.0.0 | Pump, Fläkt, Motor, Kompressor |
| Givarpanel | `MWSE\Panel\Givare` | 1.0.0 | Givare, Utegivare, Differensgivare, Integrerad Givare |
| Ventilpanel | `MWSE\Panel\Ventil` | 1.0.0 | Tvåvägsventil, Trevägsventil |
| Spjällpanel | `MWSE\Panel\Spjäll` | 1.0.0 | Spjäll, Brandspjäll (`MWSE_Lflaps2`) |
| Trepunktsventilpanel | `MWSE\Panel\Ventil` | 1.0.0 | Trepunktsventil (`MWSE_BAROM`) |
| Regulatorpanel | `MWSE\Panel\Regulator` | 1.0.0 | Regulator PI, PID och P (`MWSE_heaPI3`, `MWSE_heaPID3`, `MWSE_heaPP2`); öppnas av en egen knapp |
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
| Kurvinställning | `MWSE\Inställning\Börvärdeskurva` | 1.0.0 | Börvärdeskurva 5 (`MWSE_BVK5`) |
| Pumpstartinställning | `MWSE\Inställning\Pumpstart` | 1.0.0 | Pumpstart (`MWSE_PUMPST`) |
| Motorfördröjningsinställning | `MWSE\Inställning\Motor` | 1.0.0 | Motor (`MWSE_MOTOR`): start- och stoppfördröjning |
| Givarskalningsinställning | `MWSE\Inställning\Givare` | 1.0.0 | Ingång, analog, larm (`MWSE_INANAL`): filtertid, kalibrering, skalning |
| Givarhandinställning | `MWSE\Inställning\Givare` | 1.0.0 | Ingång, analog, larm (`MWSE_INANAL`): handläge och handvärde |
| Ventilskalningsinställning | `MWSE\Inställning\Ventil` | 1.0.0 | Utgång, analog (`MWSE_UTANA`): skalning |
| Trepunktsventilinställning | `MWSE\Inställning\Ventil` | 1.0.0 | Trepunktsventil (`MWSE_BAROM`): gångtider, dödzon |
| Spjällinställning | `MWSE\Inställning\Spjäll` | 1.0.0 | Spjäll (`MWSE_Lflaps2`): larmfördröjningar |
| Kurvinställning 2, 3, 4, 6, 7, 8, 9, 10 | `MWSE\Inställning\Börvärdeskurva` | 1.0.0 | Börvärdeskurva med 2–10 punkter (`MWSE_BvK2` … `MWSE_BvK10`), med min och max |
| Kurvinställning med sänkning 2, 5, 6, 7, 8, 9, 10 | `MWSE\Inställning\Börvärdeskurva` | 1.0.0 | Börvärdeskurva med sänkning (`MWSE_BvK2s`, `MWSE_BvK5s` … `MWSE_BvK10s`) |
| Kurvdiagram | `MWSE\Inställning\Börvärdeskurva` | 1.0.0 | Börvärdeskurva 5 (`MWSE_BvK5`): kurvan ritad med aktuella värden |
| Kurvdiagram 2, 3, 4, 6, 7, 8, 9, 10 | `MWSE\Inställning\Börvärdeskurva` | 1.0.0 | Börvärdeskurva med 2–10 punkter (`MWSE_BvK2` … `MWSE_BvK10`): kurvan ritad med aktuella värden |
| Kurvdiagram med sänkning 2, 5, 6, 7, 8, 9, 10 | `MWSE\Inställning\Börvärdeskurva` | 1.0.0 | Börvärdeskurva med sänkning (`MWSE_BvK2s`, `MWSE_BvK5s` … `MWSE_BvK10s`): kurvan och den sänkta kurvan |
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
| Larmlista | `MWSE\Sida\Larm` | 1.0.0 | Larmlistan från FBoxen Alarm PCD init (`A.Alarm.<namn>.MyName`). Bygger på Saias Online Alarm List (generation 1), med MWSE:s färger och svenska knappar, utan flerval, rubrikrad och rullningslist |
| Larmsummering | `MWSE\Sida\Larm` | 1.0.0 | Antal larm från samma FBox (`<namn>.antal`); öppnar larmvyn |
| Topplist | `MWSE\Sida\Navigering` | 1.0.0 | Meny, bakåt, sidtitel (`sidTitel`), inloggad användare, PCD:ns klocka (`S.HVC.Clock`), utloggning. Plats för Larmsummering längst till höger |
| Navigeringsmeny | `MWSE\Sida\Navigering` | 1.0.0 | Öppnas från Topplist; knappar till vyerna Start, Larm, Inställningar, Info och login, som integratören riktar om i Actions |
| Sidofält | `MWSE\Sida\Sidofält` | 1.0.0 | Inget. Den fasta högerspalten där panelerna öppnas och panelknapparna ligger |
| Inloggning | `MWSE\Sida\Inloggning` | 1.0.0 | Web8:s egen inloggning mot användarlistan i projektet, med MWSE:s färger |
| Bakgrund | `MWSE\Sida\Inloggning` | 1.0.0 | Inget. Bakgrundsbild för inloggningsvyn |
