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
- `Integratorsguide.md`: integratörsguiden
- `CHANGELOG.md`: ändringsloggen

Mallarna och guiden finns också direkt i det här förrådet.

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
| `MWSE\Inställning\...` | Inställnings- och statusblock för larm, fördröjningar, skalning, regulatorer, kurvor, tidkanaler och handstyrning |
| `MWSE\Sida\...` | Sidmallar: larmlista, larmsummering, topplist, navigeringsmeny och inloggning |

Alla mallar, deras versioner och vilken MWSE-FBox de hör till står i [ändringsloggen](CHANGELOG.md).

## Frågor och synpunkter

Biblioteket ges ut av Malthe Winje Automation AB. Kontakta vår tekniska support med frågor, fel
och synpunkter, gärna med version och en skärmbild: support@mwa.se eller +46 8 594 118 40,
vardagar kl. 08–16. Mer om supporten finns på
[mwa.se/supportcenter/teknisk-support](https://mwa.se/supportcenter/teknisk-support).

## Villkor

Villkoren för användning publiceras inför den officiella lanseringen.
