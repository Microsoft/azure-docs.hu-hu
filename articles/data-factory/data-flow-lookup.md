---
title: Keresési átalakítás a leképezési adatfolyamban
description: Egy másik forrásból származó adatokra történő hivatkozás a leképezési adatforgalomban található keresési transzformáció használatával.
author: kromerm
ms.reviewer: daperlov
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/19/2021
ms.openlocfilehash: b8754742c572a8dbc1f55c64e47bec640d757d65
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101739368"
---
# <a name="lookup-transformation-in-mapping-data-flow"></a>Keresési átalakítás a leképezési adatfolyamban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A keresési transzformáció használatával hivatkozhat egy adatfolyamban található másik forrásból származó adatokra. A keresési átalakítás hozzáfűzi az egyező adatokból származó oszlopokat a forrásadatok adataihoz.

A keresési transzformáció hasonló a bal külső illesztéshez. Az elsődleges adatfolyamból származó összes sor a kimeneti adatfolyamban található, és a keresési adatfolyam további oszlopaival is rendelkezik.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4xsVT]

## <a name="configuration"></a>Konfiguráció

![Képernyőfelvétel: a keresési beállítások lap, amely az alábbi szövegben ismertetett feliratokat tartalmazza.](media/data-flow/lookup1.png "Keresés")

**Elsődleges stream:** A bejövő adatfolyam. Ez az adatfolyam megegyezik a csatlakozás bal oldalával.

**Keresési stream:** Az elsődleges adatfolyamhoz fűzött adatforrások. A felvenni kívánt adatértékeket a keresési feltételek határozzák meg. Ez az adatfolyam egyenértékű a csatlakozás jobb oldalával.

**Több sor egyeztetése:** Ha engedélyezve van, az elsődleges adatfolyamban több egyezést tartalmazó sor több sort ad vissza. Ellenkező esetben csak egyetlen sor lesz visszaadva a "Match on" feltétel alapján.

**Egyezés:** Csak akkor látható, ha nincs bejelölve a "több sor egyeztetése" beállítás. Válassza ki, hogy az összes sor, az első egyezés vagy az utolsó egyezés egyezik-e. Minden sor ajánlott, mert a leggyorsabb végrehajtást hajtja végre. Ha az első sor vagy az utolsó sor van kiválasztva, meg kell adnia a rendezési feltételeket.

**Keresési feltételek:** Válassza ki, hogy mely oszlopok egyeznek meg. Ha az egyenlőségi feltétel teljesül, a sorok egyezésnek tekintendők. Vigye a kurzort, és válassza a "számított oszlop" lehetőséget egy érték kinyeréséhez az [adatfolyam kifejezésének nyelvével](data-flow-expression-functions.md).

Mindkét stream összes oszlopa szerepel a kimeneti adatok között. Ismétlődő vagy nem kívánt oszlopok eldobásához vegyen fel egy [Select transzformációt](data-flow-select.md) a keresési transzformáció után. Az oszlopok elhelyezhetők vagy átnevezhető egy fogadó átalakításban.

### <a name="non-equi-joins"></a>Nem összekapcsolható illesztések

Ha feltételes operátort szeretne használni, például nem egyenlő (! =) vagy nagyobb, mint (>) a keresési feltételekben, módosítsa a kezelő legördülő listát a két oszlop között. A nem megfelelő illesztésekhez szükséges, hogy a két stream közül legalább az egyiket a **rögzített** szórás használatával lehessen közvetíteni az **optimalizálás** lapon.

![Nem-extrák keresése](media/data-flow/non-equi-lookup.png "Nem-extrák keresése")

## <a name="analyzing-matched-rows"></a>Egyező sorok elemzése

A keresési transzformáció után a függvény használatával `isMatch()` megtekintheti, hogy az adott sorokhoz tartozó keresés megfelel-e az egyes soroknak.

![Keresési minta](media/data-flow/lookup111.png "Keresési minta")

Erre a mintára példa a feltételes felosztású átalakítás használata a függvényre való felosztásra `isMatch()` . A fenti példában az egyező sorok a felső streamen haladnak át, a nem egyező sorok pedig az ```NoMatch``` adatfolyamon keresztül áramlanak.

## <a name="testing-lookup-conditions"></a>Keresési feltételek tesztelése

Ha hibakeresési módban végzi a keresési átalakítást az adatelőnézet használatával, az ismert adat kis készletét használja. Nagy adatkészletből származó mintavételezési sorok esetében nem lehet előre jelezni, hogy mely sorok és kulcsok lesznek beolvasva a teszteléshez. Az eredmény nem determinisztikus, ami azt jelenti, hogy az illesztési feltételek nem adnak vissza egyezést.

## <a name="broadcast-optimization"></a>Szórás optimalizálása

![Szórásos csatlakozás](media/data-flow/broadcast.png "Szórásos csatlakozás")

Az illesztések, a keresések és a meglévő átalakítások esetében, ha az egyik vagy mindkét adatfolyam a feldolgozó csomóponti memóriába illeszkedik, a **szórás** engedélyezésével optimalizálhatja a teljesítményt. Alapértelmezés szerint a Spark-motor automatikusan eldönti, hogy az egyik oldalt kívánja-e közvetíteni. A közvetíteni kívánt oldal manuális kiválasztásához válassza a **rögzített** lehetőséget.

Nem ajánlott letiltani a szórást a **kikapcsolási** lehetőséggel, kivéve, ha az illesztések időtúllépési hibákkal futnak.

## <a name="cached-lookup"></a>Gyorsítótárazott keresés

Ha ugyanazon a forráson több kisebb keresési műveletet végez, a gyorsítótárazott fogadó és a keresés a keresési transzformációnál talán jobb használati eset. Gyakori példák, ha egy gyorsítótár-fogadó jobb lehet, ha egy adattárban egy maximális értéket keres, és a hibakódokat egy hibaüzenet-adatbázishoz rendeli. További információt a [gyorsítótár](data-flow-sink.md#cache-sink) -tárolók és a [gyorsítótárazott keresések](concepts-data-flow-expression-builder.md#cached-lookup)című témakörben olvashat.

## <a name="data-flow-script"></a>Adatfolyamszkript

### <a name="syntax"></a>Syntax

```
<leftStream>, <rightStream>
    lookup(
        <lookupConditionExpression>,
        multiple: { true | false },
        pickup: { 'first' | 'last' | 'any' },  ## Only required if false is selected for multiple
        { desc | asc }( <sortColumn>, { true | false }), ## Only required if 'first' or 'last' is selected. true/false determines whether to put nulls first
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <lookupTransformationName>
```
### <a name="example"></a>Példa

![A képernyőképen a következő kódhoz tartozó keresési beállítások lap látható.](media/data-flow/lookup-dsl-example.png "Keresés")

A fenti keresési konfiguráció adatáramlási parancsfájlja az alábbi kódrészletben található.

```
SQLProducts, DimProd lookup(ProductID == ProductKey,
    multiple: false,
    pickup: 'first',
    asc(ProductKey, true),
    broadcast: 'auto')~> LookupKeys
```
## 
Következő lépések

* A [JOIN](data-flow-join.md) és a [meglévő](data-flow-exists.md) átalakítások egyszerre több stream-bemenetet is igénybe vehetik
* [Feltételes felosztású átalakítás](data-flow-conditional-split.md) használata a paranccsal a ```isMatch()``` sorok felosztása a megfeleltetés és a nem egyező értékek alapján
