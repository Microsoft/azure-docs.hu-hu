---
title: Fájl metaadatainak használata a lekérdezésekben
description: A OPENROWSET függvény fájl-és elérési útvonalat biztosít a lekérdezésben használt összes fájlról az adatok fájlnév és/vagy mappa elérési útján történő szűréséhez és elemzéséhez.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: d24ae1f42c685589309506b2d5e0eab157b2bc42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96299615"
---
# <a name="use-file-metadata-in-serverless-sql-pool-queries"></a>Fájl metaadatainak használata kiszolgáló nélküli SQL Pool-lekérdezésekben

A kiszolgáló nélküli SQL-készlet több fájlt és mappát is képes kezelni a [lekérdezési mappák és a több fájl](query-folders-multiple-csv-files.md) című cikkben leírtak szerint. Ebből a cikkből megtudhatja, hogyan használhatja a lekérdezésekben a fájl-és mappanevek metaadat-információit.

Előfordulhat, hogy tudnia kell, hogy melyik fájl-vagy mappa-forrás korrelál az eredményhalmaz egy adott sorával.

Használhatja a függvényt, és visszaállíthatja a `filepath` `filename` fájlneveket és/vagy az elérési utat az eredményhalmazban. Vagy használhatja őket az adatszűréshez a fájlnév és/vagy a mappa elérési útja alapján. Ezeket a függvényeket a szintaxis szakasz [filename függvénye](query-data-storage.md#filename-function) és a [filepath függvény](query-data-storage.md#filepath-function)ismerteti. A következő fejezetekben rövid leírásokat talál a minták között.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként létre kell **hoznia egy adatbázist** egy olyan adatforrással, amely a Storage-fiókra hivatkozik. Ezután inicializálja az objektumokat a [telepítési parancsfájl](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) végrehajtásával az adatbázison. Ez a telepítési parancsfájl létrehozza az adatforrásokat, az adatbázis-hatókörrel rendelkező hitelesítő adatokat, valamint az ezekben a mintákban használt külső fájlformátumokat.

## <a name="functions"></a>Functions

### <a name="filename"></a>Filename

Ez a függvény azt a fájlnevet adja vissza, amelyből a sor származik.

A következő minta beolvassa a NYC Yellow taxi adatfájljait az 2017-as utolsó három hónapban, és visszaadja a másodpercenkénti túrák számát. A lekérdezés OPENROWSET része határozza meg, hogy mely fájlok lesznek beolvasva.

```sql
SELECT
    nyc.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM  
    OPENROWSET(
        BULK 'parquet/taxi/year=2017/month=9/*.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) nyc
GROUP BY nyc.filename();
```

Az alábbi példa azt szemlélteti, hogyan használható a *filename ()* a WHERE záradékban az olvasni kívánt fájlok szűréséhez. A lekérdezés OPENROWSET részében a teljes mappát éri el, a WHERE záradékban pedig szűri a fájlokat.

Az eredmények az előző példával megegyezőek lesznek.

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
    BULK 'csv/taxi/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',
        FIRSTROW = 2) 
        WITH (C1 varchar(200) ) AS [r]
WHERE
    r.filename() IN ('yellow_tripdata_2017-10.csv', 'yellow_tripdata_2017-11.csv', 'yellow_tripdata_2017-12.csv')
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

### <a name="filepath"></a>Filepath

A filepath függvény teljes vagy részleges elérési utat ad vissza:

- Ha paraméter nélkül hívja meg a metódust, az a teljes fájl elérési útját adja vissza, amelyből a sor származik. Ha DATA_SOURCE van használatban a OPENROWSET-ben, a rendszer a DATA_SOURCEhoz viszonyított elérési utat adja vissza. 
- Ha paraméterrel hívja meg a metódust, az az elérési út azon részét adja vissza, amely megfelel a paraméterben megadott pozícióban szereplő helyettesítő karakternek. Például az 1. paraméter értéke az első helyettesítő karakternek megfelelő elérési út egy részét fogja visszaadni.

A következő minta a NYC Yellow taxi adatfájljait olvassa be az elmúlt három hónapban 2017-as értékben. A fájlok elérési útjának számát adja vissza. A lekérdezés OPENROWSET része határozza meg, hogy mely fájlok lesznek beolvasva.

```sql
SELECT
    r.filepath() AS filepath
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_2017-1*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id INT
    ) AS [r]
GROUP BY
    r.filepath()
ORDER BY
    filepath;
```

Az alábbi példa azt szemlélteti, hogyan használható a *filepath ()* a WHERE záradékban az olvasni kívánt fájlok szűréséhez.

Használhatja a lekérdezés OPENROWSET részében található helyettesítő karaktereket, és szűrheti a WHERE záradékban található fájlokat. Az eredmények az előző példával megegyezőek lesznek.

```sql
SELECT
    r.filepath() AS filepath
    ,r.filepath(1) AS [year]
    ,r.filepath(2) AS [month]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_*-*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',        
        FIRSTROW = 2
    )
WITH (
    vendor_id INT
) AS [r]
WHERE
    r.filepath(1) IN ('2017')
    AND r.filepath(2) IN ('10', '11', '12')
GROUP BY
    r.filepath()
    ,r.filepath(1)
    ,r.filepath(2)
ORDER BY
    filepath;
```

## <a name="next-steps"></a>Következő lépések

A következő cikkben megtudhatja, hogyan lehet [lekérdezni a parketta-fájlokat](query-parquet-files.md).
