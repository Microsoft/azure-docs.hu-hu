---
title: Adatátalakítás elemzése a leképezési adatfolyamban
description: Beágyazott oszlop dokumentumainak elemzése
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 4db9503ea84ae13148a89a03048c73399413e5cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101710192"
---
# <a name="parse-transformation-in-mapping-data-flow"></a>Átalakítás elemzése a leképezési adatfolyamban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Az elemzés átalakításával elemezheti az olyan oszlopokat, amelyek a dokumentum űrlapján vannak. Az elemezhető beágyazott dokumentumok jelenleg támogatott típusai a JSON és a tagolt szöveg.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWykdO]

## <a name="configuration"></a>Konfiguráció

Az elemzés átalakításának konfigurálása panelen először a beágyazott oszlopokban szereplő adattípusokat fogja kiválasztani. Az elemzési transzformáció a következő konfigurációs beállításokat is tartalmazza.

![Elemzési beállítások](media/data-flow/data-flow-parse-1.png "Elemzés")

### <a name="column"></a>Oszlop

A származtatott oszlopokhoz és összesítésekhez hasonlóan a legördülő választóból kiválasztva a kilépési oszlopot is módosíthatja. Vagy egy új oszlop nevét is beírhatja. Az ADF ebben az oszlopban tárolja az elemzett forrásadatokat.

### <a name="expression"></a>Expression

A Kifejezésszerkesztő segítségével állítsa be az elemzés forrását. Ez olyan egyszerű lehet, mint a forrás oszlop kiválasztása az elemezni kívánt önálló adattal, vagy összetett kifejezéseket hozhat létre az elemzéshez.

### <a name="output-column-type"></a>Kimeneti oszlop típusa

Itt konfigurálhatja a célként megadott kimeneti sémát az elemzésből, amely egyetlen oszlopba lesz írva.

![Példa elemzése](media/data-flow/data-flow-parse-2.png "Példa elemzése")

Ebben a példában a "jsonString" bejövő mező elemzését definiáljuk egyszerű szövegként, de JSON-struktúraként formázva. Az elemzett eredményeket JSON-ként fogjuk tárolni egy "JSON" nevű új oszlopban a következő sémával:

```(trade as boolean, customers as string[])```

A kimenet megfelelő leképezésének ellenőrzéséhez tekintse meg a vizsgálat lapot és az adatelőnézett.

## <a name="examples"></a>Példák

```
source(output(
        name as string,
        location as string,
        satellites as string[],
        goods as (trade as boolean, customers as string[], orders as (orderId as string, orderTotal as double, shipped as (orderItems as (itemName as string, itemQty as string)[]))[])
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false,
    documentForm: 'documentPerLine') ~> JsonSource
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false) ~> CsvSource
JsonSource derive(jsonString = toString(goods)) ~> StringifyJson
StringifyJson parse(json = jsonString ? (trade as boolean,
        customers as string[]),
    format: 'json',
    documentForm: 'arrayOfDocuments') ~> ParseJson
CsvSource derive(csvString = 'Id|name|year\n\'1\'|\'test1\'|\'1999\'') ~> CsvString
CsvString parse(csv = csvString ? (id as integer,
        name as string,
        year as string),
    format: 'delimited',
    columnNamesAsHeader: true,
    columnDelimiter: '|',
    nullValue: '',
    documentForm: 'documentPerLine') ~> ParseCsv
ParseJson select(mapColumn(
        jsonString,
        json
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> KeepStringAndParsedJson
ParseCsv select(mapColumn(
        csvString,
        csv
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> KeepStringAndParsedCsv
```

## <a name="data-flow-script"></a>Adatfolyamszkript

### <a name="syntax"></a>Syntax

### <a name="examples"></a>Példák

```
parse(json = jsonString ? (trade as boolean,
                                customers as string[]),
                format: 'json',
                documentForm: 'singleDocument') ~> ParseJson

parse(csv = csvString ? (id as integer,
                                name as string,
                                year as string),
                format: 'delimited',
                columnNamesAsHeader: true,
                columnDelimiter: '|',
                nullValue: '',
                documentForm: 'documentPerLine') ~> ParseCsv
```    

## <a name="next-steps"></a>Következő lépések

* A [lelapul átalakítással](data-flow-flatten.md) sorokat szúrhat be oszlopokra.
* A [származtatott oszlop átalakításával](data-flow-derived-column.md) sorokra mutathat oszlopokat.
