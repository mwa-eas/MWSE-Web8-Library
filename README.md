# MWSE Web8 Library

Mallar för Saia PG5 Web Editor 8 (Web8) som kopplas till A-symbolerna i MWSE FBox-biblioteket:
processymboler, faceplates, inställningsblock och sidmallar som larmlista, topplist, meny och
inloggning. Med dem byggs ett Web8-GUI för en MWSE-anläggning av färdiga mallar i stället för att
ritas för hand.

> **Early access.** Biblioteket är i tidig åtkomst (beta) inför den officiella lanseringen.
> Mallar och guide kan ändras mellan versionerna; läs ändringsloggen före en uppdatering.

## Ladda ned

Hämta den senaste versionen under **Releases**. Zip-filen innehåller:

- `MWSE-Web8-Library-<version>-mallar.zip`: alla mallar, för import i ett steg i Web8
- `mallar/`: samma mallar som lösa `.xtml`-filer
- `grafik/`: symbolernas bilder som lösa `.svg`-filer, i alla lägen, för användning utan mallarna
- `Integratorsguide.md`: integratörsguiden
- `CHANGELOG.md`: ändringsloggen

Mallarna, bilderna och guiden finns också direkt i det här förrådet.

## MWSE FBox-biblioteket

Mallarna är byggda och prövade mot MWSE FBox-biblioteket **V20.6.001**. Alla A-symboler som
mallarna använder finns kvar med samma namn i V25.10.003 och V26.09.015, men se
integratörsguidens avsnitt 2 om Givarskalningsinställning. Mallarna är inte prövade mot dessa
versioner.

## Kom igång

Börja med [integratörsguiden](Integratorsguide.md). Den går igenom förutsättningarna i
PG5-programmet, installationen i Web8, hur en symbol och dess faceplate placeras och kopplas till
ett objekt, sidmallarna, och hur vyn byggs och laddas ned till PCD:n. Avsnitt 9 är ett komplett
exempel med en pump, en ventil och en givare.

## Innehåll

| Mapp i Web8-biblioteket | Vad |
|---|---|
| `MWSE\Symbol\...` | Processymboler med lägena vila, drift och fel, statiska symboler och Panelknapp |
| `MWSE\Panel\...` | Faceplates som öppnas när man klickar på en symbol |
| `MWSE\Inställning\...` | Inställnings- och statusblock för larm, fördröjningar, skalning, regulatorer, kurvor, tidkanaler och handstyrning, och kurvdiagram som ritar börvärdeskurvorna |
| `MWSE\Sida\...` | Sidmallar: larmlista, larmsummering, topplist, navigeringsmeny, sidofält, inloggning och inloggningsbakgrund |

Alla mallar, deras versioner och vilken MWSE-FBox de hör till står i [ändringsloggen](CHANGELOG.md).

## Fel och förslag

Biblioteket ges ut av Malthe Winje Automation AB. Rapportera fel och föreslå förbättringar under
**Issues**, med formuläret *Fel eller förslag*. Ange bibliotekets version och bifoga gärna en
skärmbild. Skriv inte anläggningsnamn, IP-adresser, inloggningar eller kunduppgifter i ett issue.

## Villkor

Biblioteket är fritt att använda under MIT-licensen, se [LICENSE](LICENSE). Undantag: Larmlista
bygger på Saia Burgess Controls Online Alarm List, och för de delar som kommer därifrån gäller
Saias villkor.
