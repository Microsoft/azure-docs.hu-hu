---
title: Az átalakítás összekapcsolása a leképezési adatfolyamban
description: Két adatforrásból származó adatok összevonása az Azure Data Factory-leképezési folyamat illesztési átalakításának használatával
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/15/2020
ms.openlocfilehash: ac84ce17f53145ffd85ffa31b6633d8b4b184962
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93042654"
---
# <a name="join-transformation-in-mapping-data-flow"></a>Az átalakítás összekapcsolása a leképezési adatfolyamban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Az illesztési transzformáció használatával két forrásból vagy streamből származó adatok egyesíthetők egy leképezési adatfolyamban. A kimeneti adatfolyam magában foglalja az összekapcsolási feltételtől függően mindkét forrás összes oszlopát. 

## <a name="join-types"></a>Illesztési típusok

Az adatforgalom leképezése jelenleg öt különböző illesztési típust támogat.

### <a name="inner-join"></a>Belső illesztés

A belső illesztés csak a két táblában egyező értékeket tartalmazó sorokat jeleníti meg.

### <a name="left-outer"></a>Bal oldali külső

A bal oldali külső illesztés visszaadja az összes sort a bal oldali adatfolyamból, és a megfelelő streamből egyeztetett rekordokat. Ha a bal oldali adatfolyamból egy sor nem egyezik, a jobb oldali adatfolyamból származó kimeneti oszlopok NULL értékre vannak állítva. A kimenet a belső illesztés és a bal oldali adatfolyam nem egyező sorai által visszaadott sorokból áll.

> [!NOTE]
> Az adatfolyamatok által használt Spark-motor esetenként sikertelen lesz, mert az illesztési feltételekben lehetséges Descartes-termékek vannak. Ha ez történik, átválthat egy egyéni keresztre, és manuálisan is megadhatja az illesztési feltételt. Ez lassabb teljesítményt eredményezhet az adatforgalomban, mivel a végrehajtó motornak ki kell számítania az összes sort a kapcsolat mindkét oldaláról, majd szűrnie kell a sorokat.

### <a name="right-outer"></a>Jobb oldali külső

A jobb oldali külső illesztés a jobb oldali adatfolyamból származó összes sort visszaadja a bal oldali adatfolyamból. Ha a jobb oldali adatfolyamból származó sor nem egyezik, a bal oldali adatfolyam kimeneti oszlopai NULL értékre vannak állítva. A kimenet a belső illesztés által visszaadott sorok, valamint a jobb oldali adatfolyamban található nem egyező sorokból áll.

### <a name="full-outer"></a>Teljes külső

A teljes külső illesztés kimenete a két oldalról származó összes oszlopot és sort a nem egyező oszlopok esetében NULL értékekkel adja vissza.

### <a name="custom-cross-join"></a>Egyéni keresztbe illesztés

A Cross JOIN egy feltétel alapján a két stream termékeit adja vissza. Ha olyan feltételt használ, amely nem egyenlő, adja meg az egyéni kifejezést a kereszt illesztési feltételként. A kimeneti adatfolyam az illesztési feltételnek megfelelő összes sor lesz.

Ezt az illesztési típust nem megfelelő illesztésekhez és ```OR``` feltételekhez is használhatja.

Ha explicit módon szeretne létrehozni egy teljes Descartes-szorzatot, használja a két független adatfolyamban található származtatott oszlop-átalakítást, mielőtt a JOIN (szintetikus) kulcsot létre szeretne hozni az egyeztetéshez. Hozzon létre például egy új oszlopot a származtatott oszlopban az egyes streamek nevű adatfolyamban, ```SyntheticKey``` és állítsa be egyenlő értékre ```1``` . Ezután használja az ```a.SyntheticKey == b.SyntheticKey``` Egyéni illesztési kifejezést.

> [!NOTE]
> Ügyeljen arra, hogy a bal és a jobb oldali kapcsolat mindegyik oldaláról legalább egy oszlopot tartalmazzon egy egyéni kereszt-illesztésben. Az egymáshoz tartozó oszlopok helyett a statikus értékekkel való összekapcsolások végrehajtása a teljes adatkészlet teljes vizsgálatát eredményezi, így az adatfolyamatok nem tudnak megfelelően elvégezni.

## <a name="configuration"></a>Konfiguráció

1. Válassza ki, hogy melyik adatfolyamot kívánja csatlakoztatni a **megfelelő stream** legördülő menüben.
1. Válassza ki az **illesztés típusát**
1. Válassza ki, hogy mely kulcs oszlopokat szeretné összekapcsolni a csatlakozás feltételéhez. Alapértelmezés szerint az adatforgalom az egyes adatfolyamok egy oszlopa között keresi az egyenlőséget. Számított érték alapján történő összehasonlításhoz vigye az egérmutatót az oszlop legördülő menüjére, és válassza a **számított oszlop** lehetőséget.

![Csatlakozás az átalakításhoz](media/data-flow/join.png "Csatlakozás")

### <a name="non-equi-joins"></a>Nem összekapcsolható illesztések

Ha feltételes operátort szeretne használni, például nem egyenlő (! =) vagy nagyobb, mint (>) az illesztési feltételekben, módosítsa a kezelő legördülő listát a két oszlop között. A nem megfelelő illesztésekhez szükséges, hogy a két stream közül legalább az egyiket a **rögzített** szórás használatával lehessen közvetíteni az **optimalizálás** lapon.

![Nem illesztések csatlakoztatása](media/data-flow/non-equi-join.png "Nem illesztések csatlakoztatása")

## <a name="optimizing-join-performance"></a>Az illesztési teljesítmény optimalizálása

Az egyesítési illesztéstől eltérően az olyan eszközökhöz, mint a SSIS, az illesztési átalakítás nem kötelező egyesítő illesztési művelet. Az illesztési kulcsok nem igénylik a rendezést. Az illesztési művelet a Spark-beli optimális illesztési műveleten alapul, vagy szórásos vagy térképes illesztés.

![Összekapcsolási átalakítás optimalizálása](media/data-flow/joinoptimize.png "Csatlakozás optimalizálása")

Az illesztések, a keresések és a meglévő átalakítások esetében, ha az egyik vagy mindkét adatfolyam a feldolgozó csomóponti memóriába illeszkedik, a **szórás** engedélyezésével optimalizálhatja a teljesítményt. Alapértelmezés szerint a Spark-motor automatikusan eldönti, hogy az egyik oldalt kívánja-e közvetíteni. A közvetíteni kívánt oldal manuális kiválasztásához válassza a **rögzített** lehetőséget.

Nem ajánlott letiltani a szórást a **kikapcsolási** lehetőséggel, kivéve, ha az illesztések időtúllépési hibákkal futnak.

## <a name="self-join"></a>Self-Join

Ha önmagához szeretne csatlakoztatni egy adatfolyamot, alias egy meglévő streamet egy kiválasztott átalakítással. Hozzon létre egy új ágat a transzformáció melletti plusz ikonra kattintva, majd válassza az **új ág** lehetőséget. Adjon hozzá egy Select transzformációt az eredeti stream aliasához. Vegyen fel egy JOIN transzformációt, és válassza ki az eredeti streamet a **bal oldali streamként** , a **jobb oldali** adatfolyamként pedig válassza az átalakítás lehetőséget.

![Önálló csatlakozás](media/data-flow/selfjoin.png "Önálló csatlakozás")

## <a name="testing-join-conditions"></a>Csatlakozási feltételek tesztelése

Az adatelőnézetsel rendelkező illesztési átalakítások hibakeresési módban történő tesztelésekor használjon egy ismert adat kis készletét. Nagy adatkészletből származó mintavételezési sorok esetében nem lehet előre jelezni, hogy mely sorok és kulcsok lesznek beolvasva a teszteléshez. Az eredmény nem determinisztikus, ami azt jelenti, hogy az illesztési feltételek nem adnak vissza egyezést.

## <a name="data-flow-script"></a>Adatfolyamszkript

### <a name="syntax"></a>Syntax

```
<leftStream>, <rightStream>
    join(
        <conditionalExpression>,
        joinType: { 'inner'> | 'outer' | 'left_outer' | 'right_outer' | 'cross' }
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <joinTransformationName>
```

### <a name="inner-join-example"></a>Példa belső illesztésre

Az alábbi példa egy nevű összekapcsolási átalakítás, `JoinMatchedData` amely a stream `TripData` és a jobb oldali streamet veszi át `TripFare` .  Az illesztési feltétel az a kifejezés, `hack_license == { hack_license} && TripData@medallion == TripFare@medallion && vendor_id == { vendor_id} && pickup_datetime == { pickup_datetime}` amely igaz értéket ad vissza, ha a `hack_license` ,, `medallion` `vendor_id` és `pickup_datetime` oszlopok az egyes adatfolyam-egyezésekben szerepelnek. A `joinType` : `'inner'` . Csak a bal oldali adatfolyamban engedélyezzük a szórást, így az `broadcast` értéke `'left'` .

Az Data Factory UX-ben ez az átalakítás az alábbi képhez hasonlóan néz ki:

![A képernyőképen az összekapcsolási beállítások lap van kiválasztva, valamint egy belső illesztési típus látható.](media/data-flow/join-script1.png "Példa csatlakoztatásra")

Az átalakításhoz tartozó adatfolyam-szkript az alábbi kódrészletben található:

```
TripData, TripFare
    join(
        hack_license == { hack_license}
        && TripData@medallion == TripFare@medallion
        && vendor_id == { vendor_id}
        && pickup_datetime == { pickup_datetime},
        joinType:'inner',
        broadcast: 'left'
    )~> JoinMatchedData
```

### <a name="custom-cross-join-example"></a>Példa az egyéni keresztbe való csatlakozásra

Az alábbi példa egy nevű összekapcsolási átalakítás, `JoiningColumns` amely a stream `LeftStream` és a jobb oldali streamet veszi át `RightStream` . Ez az átalakítás két adatfolyamot vesz igénybe, és összekapcsolja az összes olyan sort, ahol `leftstreamcolumn` az oszlop nagyobb az oszlopnál `rightstreamcolumn` . A `joinType` : `cross` . A szórás nincs engedélyezve `broadcast` érték `'none'` .

Az Data Factory UX-ben ez az átalakítás az alábbi képhez hasonlóan néz ki:

![A képernyőképen az összekapcsolási beállítások lap van kiválasztva, valamint az egyéni (Cross) illesztési típus látható.](media/data-flow/join-script2.png "Példa csatlakoztatásra")

Az átalakításhoz tartozó adatfolyam-szkript az alábbi kódrészletben található:

```
LeftStream, RightStream
    join(
        leftstreamcolumn > rightstreamcolumn,
        joinType:'cross',
        broadcast: 'none'
    )~> JoiningColumns
```

## <a name="next-steps"></a>Következő lépések

Az adategyesítést követően hozzon létre egy [származtatott oszlopot](data-flow-derived-column.md) [, és az](data-flow-sink.md) adatait egy célhely adattárba.
