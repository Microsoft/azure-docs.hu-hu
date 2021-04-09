---
title: A parketta beágyazott típusai lekérdezése kiszolgáló nélküli SQL-készlettel
description: Ebből a cikkből megtudhatja, hogyan kérdezheti le a parketta beágyazott típusait a kiszolgáló nélküli SQL-készlet használatával.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 2ef547bea9ba84665ec7187a5adf2f7a7d87e0a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101666321"
---
# <a name="query-nested-types-in-parquet-and-json-files-by-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Beágyazott típusok lekérdezése a parketta és a JSON-fájlokban kiszolgáló nélküli SQL-készlet használatával az Azure szinapszis Analyticsben

Ebből a cikkből megtudhatja, hogyan írhat lekérdezéseket kiszolgáló nélküli SQL-készlettel az Azure szinapszis Analytics szolgáltatásban. A lekérdezés a parketta beágyazott típusait fogja olvasni.
A beágyazott típusok olyan összetett struktúrák, amelyek objektumokat vagy tömböket jelképeznek. A beágyazott típusok a ben tárolhatók: 
- [Parketta](query-parquet-files.md), ahol több, tömböket és objektumokat tartalmazó összetett oszlop is lehet.
- Hierarchikus [JSON-fájlok](query-json-files.md), ahol egy összetett JSON-dokumentumot egyetlen oszlopként lehet beolvasni.
- Azure Cosmos DB gyűjtemények (jelenleg a nyilvános előzetes verzió alatt), ahol minden dokumentum összetett beágyazott tulajdonságokat tartalmazhat.

A kiszolgáló nélküli SQL-készlet összes beágyazott típusát JSON-objektumként és tömbökként formázza. Így [összetett objektumokat is kinyerhet vagy MÓDOSÍTHAT JSON-függvények használatával](/sql/relational-databases/json/validate-query-and-change-json-data-with-built-in-functions-sql-server) vagy [a JSON-adatok elemzésével a openjson utasítással függvény használatával](/sql/relational-databases/json/convert-json-data-to-rows-and-columns-with-openjson-sql-server). 

Az alábbi példa egy olyan lekérdezést mutat be, amely a [COVID-19 nyílt kutatási adatkészlet JSON-](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) fájljából Kinyeri a skaláris és az objektum értékeit, amelyek beágyazott objektumokat tartalmaznak: 

```sql
SELECT
    title = JSON_VALUE(doc, '$.metadata.title'),
    first_author = JSON_QUERY(doc, '$.metadata.authors[0]'),
    first_author_name = JSON_VALUE(doc, '$.metadata.authors[0].first'),
    complex_object = doc
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
        FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b'
    )
    WITH ( doc varchar(MAX) ) AS docs;
```

A `JSON_VALUE` függvény egy skaláris értéket ad vissza a megadott elérési úton lévő mezőből. A `JSON_QUERY` függvény a megadott elérési úton található mezőből JSON-ként formázott objektumot ad vissza.

> [!IMPORTANT]
> Ez a példa egy fájlt használ a COVID-19 nyílt kutatási adatkészletből. [Tekintse meg a licencet és az adat szerkezetét itt](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/).

## <a name="prerequisites"></a>Előfeltételek

Első lépésként létre kell hoznia egy adatbázist, amely az adatforrás létrehozását fogja létrehozni. Ezután inicializálja az objektumokat egy [telepítési parancsfájl](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) futtatásával az adatbázison. A telepítési parancsfájl létrehozza az adatforrásokat, az adatbázis-hatókörű hitelesítő adatokat és a mintákban használt külső fájlformátumokat.

## <a name="project-nested-or-repeated-data"></a>Beágyazott vagy ismétlődő projekt

A parketta-fájlokhoz több, összetett típusú oszlop is tartozhat. Az ezekből az oszlopokból származó értékek JSON-szövegként vannak formázva, és VARCHAR-oszlopként lesznek visszaadva. A következő lekérdezés beolvassa a structExample. Parque fájlt, és bemutatja, hogyan olvashatja el a beágyazott oszlopok értékeit: 

```sql
SELECT
    DateStruct, TimeStruct, TimestampStruct, DecimalStruct, FloatStruct
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        DateStruct VARCHAR(8000),
        TimeStruct VARCHAR(8000),
        TimestampStruct VARCHAR(8000),
        DecimalStruct VARCHAR(8000),
        FloatStruct VARCHAR(8000)
    ) AS [r];
```

Ez a lekérdezés a következő eredményt adja vissza. A rendszer minden beágyazott objektum tartalmát JSON-szövegként adja vissza.

| DateStruct    | TimeStruct    | TimestampStruct   | DecimalStruct | FloatStruct |
| --- | --- | --- | --- | --- |
|{"Date": "2009-04-25"}| {"Time": "20:51:54.3598000"}|    {"Időbélyeg": "5501-04-08 12:13:57.4821000"}|    {"Decimális": 11143412.25350}| {"Float": 0,5}|
|{"Date": "1916-04-29"}| {"Time": "00:16:04.6778000"}|    {"Időbélyeg": "1990-06-30 20:50:52.6828000"}|    {"Decimális": 1963545.62800}|  {"Float":-2,125}|

A következő lekérdezés beolvassa a justSimpleArray. Parque fájlt. A program a Parquet fájl összes oszlopát feltervezi, beleértve a beágyazott és az ismétlődő adatok listáját is.

```sql
SELECT
    SimpleArray
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

A lekérdezés a következő eredményt fogja visszaadni:

|SimpleArray|
| --- |
|[11, 12, 13]|
|[21, 22, 23]|

## <a name="read-properties-from-nested-object-columns"></a>Tulajdonságok beolvasása a beágyazott objektumok oszlopaiból

A `JSON_VALUE` függvény lehetővé teszi, hogy a rendszer JSON-szövegként formázott oszlopokból származó értékeket ad vissza:

```sql
SELECT
    title = JSON_VALUE(complex_column, '$.metadata.title'),
    first_author_name = JSON_VALUE(complex_column, '$.metadata.authors[0].first'),
    body_text = JSON_VALUE(complex_column, '$.body_text.text'),
    complex_column
FROM
    OPENROWSET( BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
                FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b' ) WITH ( complex_column varchar(MAX) ) AS docs;
```

Az eredmény az alábbi táblázatban látható:

|cím  | first_author_name | body_text | complex_column |
| --- | --- | --- | --- |
| Kiegészítő információk öko-epidemiolo... | Julien   | – S1: törzsfejlődés... | `{    "paper_id": "000b7d1517ceebb34e1e3e817695b6de03e2fa78",    "metadata": {        "title": "Supplementary Information An eco-epidemiological study of Morbilli-related paramyxovirus infection in Madagascar bats reveals host-switching as the dominant macro-evolutionary mechanism",        "authors": [            {                "first": "Julien"` |

A JSON-fájlokkal ellentétben, amelyek a legtöbb esetben egy összetett JSON-objektumot tartalmazó egyetlen oszlopot adnak vissza, a Parquet-fájlok több összetett oszloppal is rendelkezhetnek. A beágyazott oszlopok tulajdonságait az `JSON_VALUE` egyes oszlopokban található függvény használatával olvashatja. `OPENROWSET` lehetővé teszi egy záradékban lévő beágyazott tulajdonságok elérési útjának közvetlen megadását `WITH` . Megadhatja az elérési utakat egy oszlop neveként, vagy hozzáadhat egy [JSON Path kifejezést](/sql/relational-databases/json/json-path-expressions-sql-server) az oszlop típusa után.

A következő lekérdezés beolvassa a structExample. Parque fájlt, és bemutatja, hogyan lehet egy beágyazott oszlop felületi elemeit beolvasni. Két módon hivatkozhat egy beágyazott értékre:
- A beágyazott érték elérési útjának kifejezésének megadásával a típus meghatározása után.
- Az oszlop nevének beágyazott elérési úttal való formázásához használja a do "." kifejezést a mezőkre való hivatkozáshoz.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        [DateValue] DATE '$.DateStruct.Date',
        [TimeStruct.Time] TIME,
        [TimestampStruct.Timestamp] DATETIME2,
        DecimalValue DECIMAL(18, 5) '$.DecimalStruct.Decimal',
        [FloatStruct.Float] FLOAT
    ) AS [r];
```

## <a name="access-elements-from-repeated-columns"></a>Elemek elérése ismétlődő oszlopokból

A következő lekérdezés beolvassa a justSimpleArray. Parque fájlt, és a [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?view=azure-sqldw-latest&preserve-view=true) használatával beolvas egy skaláris elemet egy ismétlődő oszlopból, például egy tömbből vagy térképből:

```sql
SELECT
    *,
    JSON_VALUE(SimpleArray, '$[0]') AS FirstElement,
    JSON_VALUE(SimpleArray, '$[1]') AS SecondElement,
    JSON_VALUE(SimpleArray, '$[2]') AS ThirdElement
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

Az eredmény a következő:

|SimpleArray    | FirstElement  | SecondElement | ThirdElement |
| --- | --- | --- | --- |
| [11, 12, 13] | 11   | 12 | 13 |
| [21, 22, 23] | 21   | 22 | 23 |

## <a name="access-sub-objects-from-complex-columns"></a>Az alárendelt objektumok elérése összetett oszlopokból

A következő lekérdezés beolvassa a mapExample. Parque fájlt, és a [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?view=azure-sqldw-latest&preserve-view=true) használatával lekéri a nem skaláris elemet egy ismétlődő oszlopból, például egy tömbből vagy térképből:

```sql
SELECT
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

Explicit módon hivatkozhat a záradékban visszaadni kívánt oszlopokra is `WITH` :

```sql
SELECT DocId,
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) 
    WITH (DocId bigint, MapOfPersons VARCHAR(max)) AS [r];
```

A struktúra `MapOfPersons` varchar-oszlopként lesz visszaadva, és JSON-sztringként van formázva.

## <a name="project-values-from-repeated-columns"></a>Projekt értékei ismétlődő oszlopokból

Ha egyes oszlopokban skaláris értékek (például) tömbje van `[1,2,3]` , egyszerűen kibonthatja őket, és a fő sorral csatlakoztathatja őket a következő parancsfájl használatával:

```sql
SELECT
    SimpleArray, Element
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS arrays
    CROSS APPLY OPENJSON (SimpleArray) WITH (Element int '$') as array_values
```

## <a name="next-steps"></a>Következő lépések

A következő cikk bemutatja, hogyan lehet [lekérdezni a JSON-fájlokat](query-json-files.md).