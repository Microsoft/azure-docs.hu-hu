---
title: Nézetek létrehozása és használata kiszolgáló nélküli SQL-készletben
description: Ebből a szakaszból megtudhatja, hogyan hozhat létre és használhat nézeteket a kiszolgáló nélküli SQL Pool-lekérdezések becsomagolásához. A nézetek segítségével újra felhasználhatja ezeket a lekérdezéseket. A nézetekre akkor is szükség van, ha olyan eszközöket kíván használni, mint például a Power BI, a kiszolgáló nélküli SQL-készlettel együtt.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 0948c7c82d7577bae07057bff9d1be4d7e09f978
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96462291"
---
# <a name="create-and-use-views-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Nézetek létrehozása és használata kiszolgáló nélküli SQL-készlettel az Azure szinapszis Analyticsben

Ebből a szakaszból megtudhatja, hogyan hozhat létre és használhat nézeteket a kiszolgáló nélküli SQL Pool-lekérdezések becsomagolásához. A nézetek segítségével újra felhasználhatja ezeket a lekérdezéseket. A nézetekre akkor is szükség van, ha olyan eszközöket kíván használni, mint például a Power BI, a kiszolgáló nélküli SQL-készlettel együtt.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként létre kell hoznia egy adatbázist, amelyben létrejön a nézet, és inicializálnia kell az Azure Storage-ban való hitelesítéshez szükséges objektumokat a [telepítési parancsfájlnak](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) az adatbázison történő végrehajtásával. A cikkben szereplő összes lekérdezés végrehajtása a mintaadatbázis alapján történik.

## <a name="create-a-view"></a>Nézet létrehozása

A nézetek ugyanúgy hozhatók létre, mint a rendszeres SQL Server nézetek létrehozásakor. A következő lekérdezés olyan nézetet hoz létre, amely beolvas *population.csv* fájlt.

> [!NOTE]
> Módosítsa a lekérdezés első sorát, azaz: [mydbname], így Ön a létrehozott adatbázist használja.

```sql
USE [mydbname];
GO

DROP VIEW IF EXISTS populationView;
GO

CREATE VIEW populationView AS
SELECT * 
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', 
        FIELDTERMINATOR =',', 
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r];
```

Az ebben a példában szereplő nézet az `OPENROWSET` alapul szolgáló fájlok abszolút elérési útját használó függvényt használja. Ha rendelkezik a `EXTERNAL DATA SOURCE` tároló gyökerének URL-címével, a és a `OPENROWSET` `DATA_SOURCE` relatív elérési út is használható:

```sql
CREATE VIEW TaxiView
AS SELECT *, nyc.filepath(1) AS [year], nyc.filepath(2) AS [month]
FROM
    OPENROWSET(
        BULK 'parquet/taxi/year=*/month=*/*.parquet',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT='PARQUET'
    ) AS nyc
```

## <a name="use-a-view"></a>Nézet használata

A lekérdezésekben a nézeteket ugyanúgy használhatja, mint SQL Server lekérdezésekben.

A következő lekérdezés azt mutatja be, hogyan használja a [nézet létrehozása nézetben](#create-a-view)létrehozott *population_csv* nézetet. Az ország/régió nevét adja vissza a 2019-es populációban csökkenő sorrendben.

> [!NOTE]
> Módosítsa a lekérdezés első sorát, azaz: [mydbname], így Ön a létrehozott adatbázist használja.

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationView
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Következő lépések

A különböző fájltípusok lekérdezésével kapcsolatos további információkért tekintse meg az [egyszerű CSV-fájl lekérdezése](query-single-csv-file.md), a [Parquet-fájlok lekérdezése](query-parquet-files.md)és a [JSON-fájlok lekérdezése](query-json-files.md) című cikket.
