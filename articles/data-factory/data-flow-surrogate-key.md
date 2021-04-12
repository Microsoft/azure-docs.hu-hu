---
title: Helyettes kulcs átalakítása a leképezési adatfolyamban
description: A Azure Data Factory leképezési adatforgalmának helyettes kulcs-átalakításának használata szekvenciális kulcsok értékének létrehozásához
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/30/2020
ms.openlocfilehash: d1f8993b1adc297b1bfadba114df76a66e59afa2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93147178"
---
# <a name="surrogate-key-transformation-in-mapping-data-flow"></a>Helyettes kulcs átalakítása a leképezési adatfolyamban 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A helyettesítő kulcs átalakításával adja hozzá a növekményes kulcs értékét az egyes adatsorokhoz. Ez akkor hasznos, ha dimenziós táblákat tervez kialakítani egy Star Schema analitikai adatmodellben. A Star-sémákban a dimenziós táblázatok egyes tagjainak olyan egyedi kulcsra van szükségük, amely nem üzleti kulcs.

## <a name="configuration"></a>Konfiguráció

![Helyettes kulcs átalakítása](media/data-flow/surrogate.png "Helyettes kulcs átalakítása")

**Kulcs oszlopa:** A generált helyettesítő kulcs oszlopának neve.

**Kezdő érték:** A legkisebb kulcs értéke, amelyet a rendszer generál.

## <a name="increment-keys-from-existing-sources"></a>Kulcsok növelése meglévő forrásokból

Ha olyan értékről szeretné elindítani a sorozatot, amely egy forrásban létezik, javasoljuk, hogy egy gyorsítótár-fogadó használatával mentse ezt az értéket, és használjon egy származtatott oszlop-átalakítást a két érték együttes hozzáadásához. Gyorsítótárazott kereséssel szerezze be a kimenetet, és fűzze hozzá a generált kulcshoz. További információt a [gyorsítótár](data-flow-sink.md#cache-sink) -tárolók és a [gyorsítótárazott keresések](concepts-data-flow-expression-builder.md#cached-lookup)című témakörben olvashat.

![Helyettes kulcs keresése](media/data-flow/cached-lookup-example.png "Helyettes kulcs keresése")

### <a name="increment-from-existing-maximum-value"></a>Növekmény a meglévő maximális értéktől

Ha a kulcs értékét az előző max. módszerrel szeretné kiosztani, két olyan módszert használhat, amelyet a forrásadatok alapján lehet használni.

#### <a name="database-sources"></a>Adatbázis-források

SQL-lekérdezési lehetőséggel válassza ki a MAX () elemet a forrásból. Például: `Select MAX(<surrogateKeyName>) as maxval from <sourceTable>`.

![Helyettes kulcs lekérdezése](media/data-flow/surrogate-key-max-database.png "Helyettes kulcs transzformációs lekérdezése")

#### <a name="file-sources"></a>Fájlok forrásai

Ha az előző maximális érték egy fájlban található, az `max()` összesített transzformációban az előző maximális érték beszerzéséhez használja a függvényt:

![Helyettesítő kulcs fájlja](media/data-flow/surrogate-key-max-file.png "Helyettesítő kulcs fájlja")

Mindkét esetben írnia kell egy gyorsítótár-fogadóba, és meg kell keresnie az értéket. 


## <a name="data-flow-script"></a>Adatfolyamszkript

### <a name="syntax"></a>Syntax

```
<incomingStream> 
    keyGenerate(
        output(<surrogateColumnName> as long),
        startAt: <number>L
    ) ~> <surrogateKeyTransformationName>
```

### <a name="example"></a>Példa

![Helyettes kulcs átalakítása](media/data-flow/surrogate.png "Helyettes kulcs átalakítása")

A fenti helyettesítő kulcs konfigurációjának adatfolyam-parancsfájlja az alábbi kódrészletben található.

```
AggregateDayStats
    keyGenerate(
        output(key as long),
        startAt: 1L
    ) ~> SurrogateKey1
```

## <a name="next-steps"></a>Következő lépések

Ezek a példák a [JOIN](data-flow-join.md) és a [származtatott oszlop](data-flow-derived-column.md) átalakításokat használják.
