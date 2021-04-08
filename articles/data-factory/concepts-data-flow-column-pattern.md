---
title: Oszlopok mintái Azure Data Factory leképezési adatfolyamban
description: Általánosított Adatátalakítási minták létrehozása Azure Data Factory leképezési adatforgalmához tartozó oszlopos minták használatával
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 68c211608cfceedaa9d13a595be6d1e5de17f1d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94845006"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>Oszlopok mintáinak használata a leképezési adatfolyamban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Számos leképezési adatfolyam-átalakítás lehetővé teszi a sablon oszlopainak a rögzített oszlopnevek helyett a mintázatok alapján történő hivatkozását. Ezt a megfeleltetést *oszlopbeli mintázatoknak* nevezzük. Megadhat mintázatokat az oszlopoknak a név, az adattípus, a stream, a forrás vagy a pozíció alapján való egyeztetéséhez, a mezők nevének megadása helyett. Az oszlopok mintája két esetben hasznos:

* Ha a bejövő forrás mezői gyakran változnak, például a szövegfájlokban vagy a NoSQL-adatbázisokban lévő oszlopok módosításának esetére. Ez a forgatókönyv a [séma drift](concepts-data-flow-schema-drift.md)néven ismert.
* Ha egy gyakori műveletet szeretne végezni az oszlopok nagy csoportjára. Például minden olyan oszlopot, amely "Total" értékkel rendelkezik az oszlop nevében, dupla értékre kívánja feldolgozni.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4Iui1]

## <a name="column-patterns-in-derived-column-and-aggregate"></a>Oszlopos minták származtatott oszlopban és összesítésben

Ha egy származtatott oszlopba, összesítésbe vagy ablak átalakításba szeretne felvenni egy oszlopot, kattintson az oszlop **hozzáadása** elemre, vagy a meglévő származtatott oszlop melletti plusz ikonra. Válassza az **oszlop hozzáadása minta** lehetőséget.

![A képernyőképen a plusz ikon látható az oszlop mintázatának hozzáadásához.](media/data-flow/add-column-pattern.png "Oszlopminták")

A [Kifejezésszerkesztő](concepts-data-flow-expression-builder.md) segítségével adja meg a egyezési feltételt. Hozzon létre egy olyan logikai kifejezést, amely a (z),,, `name` `type` `stream` `origin` és `position` oszlop alapján egyezik az oszlopokkal. A minta minden olyan oszlopot érint, amely sodródik vagy definiálva lesz, ahol a feltétel igaz értéket ad vissza.

Az egyeztetési feltétel alá tartozó két kifejezés mező adja meg az érintett oszlopok új neveit és értékeit. Ezzel a `$$` beállítással hivatkozhat a megegyező mező meglévő értékére. A bal oldali kifejezés mezőben a név és a jobb oldali kifejezés mező határozza meg az értéket.

![Képernyőfelvétel: a származtatott oszlop beállításainak lapja.](media/data-flow/edit-column-pattern.png "Oszlopminták")

A fenti oszlop minta minden dupla típusú oszlopra illeszkedik, és egy származtatott oszlopot hoz létre egyezés alapján. Az `$$` oszlop neve mezőnek megfelelően a rendszer minden egyező oszlopot ugyanazzal a névvel frissíti. Az egyes oszlopok értéke a meglévő érték két tizedes pontra kerekítve.

Az egyeztetési feltétel helyességének ellenőrzéséhez ellenőrizheti a **megvizsgálandó** lapon definiált oszlopok kimeneti sémáját, vagy beolvashatja az adatok pillanatképét az **adatelőnézet** lapon. 

![A képernyőképen a kimeneti séma lap látható.](media/data-flow/columnpattern3.png "Oszlopminták")

### <a name="hierarchical-pattern-matching"></a>Hierarchikus minta egyeztetése

Az összetett hierarchikus struktúrákon belül is létrehozhat mintázatokat. Bontsa ki azt a szakaszt, `Each MoviesStruct that matches` amelyben a rendszer kéri az adatfolyamban található minden egyes hierarchiát. Ezután összeállíthatja a kiválasztott hierarchiában található tulajdonságok megfeleltetési mintáit.

![A képernyőképen a hierarchikus oszlop mintája látható.](media/data-flow/patterns-hierarchy.png "Oszlopok mintái hierarchiákban")

## <a name="rule-based-mapping-in-select-and-sink"></a>Szabályon alapuló hozzárendelés a Select és a mosogatóban

A forrásban lévő oszlopok leképezése és az átalakítások kiválasztása esetén rögzített leképezési vagy szabály-alapú leképezéseket adhat hozzá. Egyezés a,, `name` , `type` `stream` `origin` és `position` oszlopok alapján. A rögzített és a szabályokon alapuló leképezések tetszőleges kombinációja lehet. Alapértelmezés szerint az 50-nál nagyobb számú összes kivetítés alapértelmezett értéke egy olyan szabály-alapú hozzárendelés, amely minden oszlop esetében megfelel, és a megjelenő nevet adja eredményül. 

Szabály alapú hozzárendelés hozzáadásához kattintson a **leképezés hozzáadása** elemre, és válassza a **szabály alapú leképezés** lehetőséget.

![Képernyőfelvétel: a leképezés hozzáadása lehetőséggel kiválasztott szabály alapú leképezés.](media/data-flow/rule2.png "Szabály alapú leképezés")

Minden szabály alapú leképezéshez két bemenet szükséges: az a feltétel, amelynek a megfeleltetése a és az egyes leképezett oszlopok neve. Mindkét érték a [Kifejezésszerkesztő](concepts-data-flow-expression-builder.md)használatával van megadva. A bal oldali kifejezés mezőbe írja be a logikai egyezési feltételt. A jobb oldali kifejezés mezőben adja meg, hogy az egyeztetett oszlop hogyan lesz leképezve.

![A képernyőfelvétel egy leképezést mutat be.](media/data-flow/rule-based-mapping.png "Szabály alapú leképezés")

`$$`Szintaxis használatával hivatkozhat egy egyező oszlop bemeneti nevére. Tegyük fel, hogy a fenti képen egy felhasználó szeretne egyeztetni az összes olyan karakterlánc-oszlopon, amelynek a neve 6 karakternél rövidebb. Ha az egyik bejövő oszlop neve `test` , a kifejezés `$$ + '_short'` átnevezi az oszlopot `test_short` . Ha ez az egyetlen olyan leképezés, amely nem felel meg a feltételnek, a rendszer elveti a kiszolgált adatokból.

A minták egymásba sodródott és definiált oszlopokkal egyeznek meg. Ha szeretné megtekinteni, hogy mely meghatározott oszlopok vannak leképezve egy szabályhoz, kattintson a szabály melletti szemüveg ikonra. Ellenőrizze a kimenetet az adatelőnézet használatával.

### <a name="regex-mapping"></a>Regex-leképezés

Ha a lefelé mutató Chevron ikonra kattint, megadhat egy regex-leképezési feltételt. A regex-leképezési feltétel minden olyan oszlopnevet megfelel, amely megfelel a megadott regex-feltételnek. Ez használható a szabványos szabályokon alapuló leképezésekkel együtt.

![Képernyőfelvétel: a regex-leképezési feltétel a hierarchia szintjével és a név egyezésével.](media/data-flow/regex-matching.png "Szabály alapú leképezés")

A fenti példa a regex mintára `(r)` vagy bármely olyan oszlop nevére illeszkedik, amely egy kisbetű r betűt tartalmaz. A szabványos szabályokon alapuló leképezéshez hasonlóan az összes egyező oszlop a megfelelő szintaxis használatával módosul `$$` .

### <a name="rule-based-hierarchies"></a>Szabály alapú hierarchiák

Ha a megadott leképezés rendelkezik hierarchiával, akkor a szabályokon alapuló leképezés használatával képezhető le a hierarchiák aloszlopai. Meg kell adnia egy megfelelő feltételt és azt a komplex oszlopot, amelynek aloszlopait szeretné leképezni. Minden egyező aloszlop kimenete a jobb oldalon megadott "Name as" szabály használatával történik.

![A képernyőfelvétel egy, a hierarchiát használó szabály alapú leképezést mutat be.](media/data-flow/rule-based-hierarchy.png "Szabály alapú leképezés")

A fenti példa a komplex oszlopok összes aloszlopára illeszkedik `a` . `a` két aloszlopot tartalmaz `b` , és `c` . A kimeneti séma két oszlopot fog tartalmazni, `b` és `c` a "Name as" feltételnek kell lennie `$$` .

## <a name="pattern-matching-expression-values"></a>Mintázattal egyező kifejezés értékei.

* `$$` az egyes egyezések nevének vagy értékének lefordítása futási időben. Úgy gondolja, `$$` hogy egyenértékű a következővel: `this` .
* `name` az egyes bejövő oszlopok nevét jelöli
* `type` az egyes bejövő oszlopok adattípusát jelöli
* `stream` az egyes adatfolyamokhoz társított nevet vagy a folyamat átalakítását jelöli
* `position` az adatfolyamat oszlopainak sorszáma
* `origin` az a transzformáció, amelyben egy oszlop származik vagy Utoljára frissítve lett

## <a name="next-steps"></a>Következő lépések
* További információ az adatátalakítások hozzárendelési folyamatának [kifejezési nyelvéről](data-flow-expression-functions.md)
* Oszlopok mintáinak használata a fogadó [átalakításban](data-flow-sink.md) és az [átalakítás kiválasztása](data-flow-select.md) szabály alapú leképezéssel
