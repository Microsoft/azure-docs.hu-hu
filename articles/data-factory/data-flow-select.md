---
title: Átalakítás kiválasztása az adatforgalom leképezése elemben
description: Azure Data Factory leképezési adatfolyam kiválasztása – átalakítás
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/02/2020
ms.openlocfilehash: 2d8c4d1915e22ccabf193f1b34c5fc4797ead549
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93040229"
---
# <a name="select-transformation-in-mapping-data-flow"></a>Átalakítás kiválasztása az adatforgalom leképezése elemben

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Az oszlopok átnevezéséhez, eldobásához vagy átrendezéséhez használja az átalakítás kiválasztása lehetőséget. Ez az átalakítás nem változtatja meg a sor adatváltozását, de kiválasztja, hogy mely oszlopok legyenek propagálva az alsóbb rétegben. 

A kiválasztott átalakításban a felhasználók megadhatják a rögzített leképezéseket, mintákat használhatnak a szabályokon alapuló leképezéshez, vagy engedélyezhetik az automatikus leképezést is. A rögzített és a szabályon alapuló leképezések is használhatók ugyanazon a kiválasztási átalakításon belül. Ha egy oszlop nem egyezik meg a megadott leképezések egyikével sem, a rendszer elveti.

## <a name="fixed-mapping"></a>Rögzített leképezés

Ha a leképezésben kevesebb, mint 50 oszlop van meghatározva, az összes definiált oszlophoz alapértelmezés szerint rögzített leképezés lesz hozzárendelve. A rögzített hozzárendelések egy meghatározott, bejövő oszlopot vesznek fel, és pontos nevet képeznek.

![Rögzített leképezés](media/data-flow/fixedmapping.png "Rögzített leképezés")

> [!NOTE]
> Rögzített leképezés használatával nem képezhető le és nem nevezhető át egy lebegett oszlop

### <a name="mapping-hierarchical-columns"></a>Hierarchikus oszlopok megfeleltetése

A rögzített leképezések használatával egy hierarchikus oszlop aloszlopa képezhető le egy legfelső szintű oszlopra. Ha van definiált hierarchiája, az oszlop legördülő listából válassza ki az aloszlopot. Az átalakítás kiválasztása egy új oszlopot hoz létre az aloszlop értékével és adattípusával.

![hierarchikus leképezés](media/data-flow/select-hierarchy.png "hierarchikus leképezés")

## <a name="rule-based-mapping"></a>Szabály alapú leképezés


> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4xiXz]

Ha egyszerre sok oszlopot szeretne leképezni, vagy átadni a lebegő oszlopokat, a szabályok alapján történő leképezés használatával definiálhatja a leképezéseket az oszlopok mintázatával. Egyezés a,, `name` `type` `stream` és `position` oszlopok alapján. A rögzített és a szabályokon alapuló leképezések tetszőleges kombinációja lehet. Alapértelmezés szerint az 50-nál nagyobb számú összes kivetítés alapértelmezett értéke egy olyan szabály-alapú hozzárendelés, amely minden oszlop esetében megfelel, és a megjelenő nevet adja eredményül. 

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

Ha az oszlopnév több regex-egyezéssel rendelkezik, akkor az `$n` "n" kifejezéssel megegyező egyezésekre hivatkozhat. Például a "$2" az oszlopnév második egyezésére hivatkozik.

### <a name="rule-based-hierarchies"></a>Szabály alapú hierarchiák

Ha a megadott leképezés rendelkezik hierarchiával, akkor a szabályokon alapuló leképezés használatával képezhető le a hierarchiák aloszlopai. Meg kell adnia egy megfelelő feltételt és azt a komplex oszlopot, amelynek aloszlopait szeretné leképezni. Minden egyező aloszlop kimenete a jobb oldalon megadott "Name as" szabály használatával történik.

![A képernyőfelvétel egy, a hierarchiát használó szabály alapú leképezést mutat be.](media/data-flow/rule-based-hierarchy.png "Szabály alapú leképezés")

A fenti példa a komplex oszlopok összes aloszlopára illeszkedik `a` . `a` két aloszlopot tartalmaz `b` , és `c` . A kimeneti séma két oszlopot fog tartalmazni, `b` és `c` a "Name as" feltételnek kell lennie `$$` .

### <a name="parameterization"></a>Paraméterezés

Az oszlopnevek parametrizálja leképezés használatával is megadhatók. A kulcsszó használatával a ```name``` bejövő oszlopnevek megegyeznek egy paraméterrel. Ha például egy adatáramlási paraméterrel rendelkezik ```mycolumn``` , létrehozhat egy olyan szabályt, amely megegyezik a következővel egyenlő oszlop nevével: ```mycolumn``` . Átnevezheti az egyező oszlopot egy nehezen kódolt sztringre, például az "üzleti kulcs" kifejezésre, és hivatkozását explicit módon. Ebben a példában a megfelelő feltétel, ```name == $mycolumn``` a név feltétel pedig az "üzleti kulcs". 

## <a name="auto-mapping"></a>Automatikus leképezés

Ha kiválasztott átalakítást ad hozzá, az **automatikus** leképezés lehetőségre kattintva engedélyezheti az automatikus megfeleltetés csúszkáját. Az automatikus leképezéssel az átalakítás kiválasztása leképezi az összes bejövő oszlopot az ismétlődések kivételével, ugyanazzal a névvel, mint a bemenet. Ez magában foglalja a lebegő oszlopokat is, ami azt jelenti, hogy a kimeneti adatokat tartalmazó oszlopok nem definiáltak a sémában. Az átsodródott oszlopokkal kapcsolatos további információkért lásd: [séma-eltolódás](concepts-data-flow-schema-drift.md).

![Automatikus leképezés](media/data-flow/automap.png "Automatikus leképezés")

Az automatikus leképezés bekapcsolásával az átalakítás kiválasztása megtartja az ismétlődő beállítások kihagyását, és új aliast biztosít a meglévő oszlopokhoz. Az aliasok akkor hasznosak, ha több illesztést vagy keresést végeznek ugyanazon az adatfolyamon, és önillesztési helyzetekben. 

## <a name="duplicate-columns"></a>Ismétlődő oszlopok

Alapértelmezés szerint a Select átalakítás elveszíti az ismétlődő oszlopokat a bemeneti és a kimeneti leképezésben is. Az ismétlődő beviteli oszlopok gyakran a JOIN és a keresési átalakításokból származnak, ahol az oszlopok nevei duplikálva vannak az illesztés mindkét oldalán. Ismétlődő kimeneti oszlopok is előfordulhatnak, ha két különböző bemeneti oszlopot rendel ugyanahhoz a névhez. A jelölőnégyzet bejelölésével adja meg, hogy szeretné-e eldobni vagy átadni a duplikált oszlopokat.

![Ismétlődések kihagyása](media/data-flow/select-skip-dup.png "Ismétlődések kihagyása")

## <a name="ordering-of-columns"></a>Oszlopok rendezése

A leképezések sorrendje határozza meg a kimeneti oszlopok sorrendjét. Ha egy bemeneti oszlop többször van leképezve, a rendszer csak az első leképezést fogja tiszteletben venni. Minden ismétlődő oszlop esetében az első egyezés lesz megtartva.

## <a name="data-flow-script"></a>Adatfolyamszkript

### <a name="syntax"></a>Syntax

```
<incomingStream>
    select(mapColumn(
        each(<hierarchicalColumn>, match(<matchCondition>), <nameCondition> = $$), ## hierarchical rule-based matching
        <fixedColumn>, ## fixed mapping, no rename
        <renamedFixedColumn> = <fixedColumn>, ## fixed mapping, rename
        each(match(<matchCondition>), <nameCondition> = $$), ## rule-based mapping
        each(patternMatch(<regexMatching>), <nameCondition> = $$) ## regex mapping
    ),
    skipDuplicateMapInputs: { true | false },
    skipDuplicateMapOutputs: { true | false }) ~> <selectTransformationName>
```

### <a name="example"></a>Példa

Alább látható egy példa a Select leképezésre és az adatfolyam-parancsfájlra:

![Példa parancsfájl kiválasztására](media/data-flow/select-script-example.png "Példa parancsfájl kiválasztására")

```
DerivedColumn1 select(mapColumn(
        each(a, match(true())),
        movie,
        title1 = title,
        each(match(name == 'Rating')),
        each(patternMatch(`(y)`),
            $1 + 'regex' = $$)
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> Select1
```

## <a name="next-steps"></a>Következő lépések
* Miután a Select paranccsal átnevezi, átrendezi és alias oszlopokat, a fogadó [transzformáció](data-flow-sink.md) használatával az adatait egy adattárba helyezheti.
