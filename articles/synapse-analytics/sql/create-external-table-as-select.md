---
title: Lekérdezési eredmények tárolása kiszolgáló nélküli SQL-készletből
description: Ebből a cikkből megtudhatja, hogyan tárolhat lekérdezési eredményeket a Storage szolgáltatásban a kiszolgáló nélküli SQL-készlet használatával.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 12841c747116cc9e14f348dfcf81acaa5da5e8c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98165365"
---
# <a name="store-query-results-to-storage-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Lekérdezési eredmények tárolása a Storage-ban kiszolgáló nélküli SQL-készlettel az Azure szinapszis Analyticsben

Ebből a cikkből megtudhatja, hogyan tárolhat lekérdezési eredményeket a Storage szolgáltatásban a kiszolgáló nélküli SQL-készlet használatával.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként létre kell **hoznia egy adatbázist** , amelyen végre fogja hajtani a lekérdezéseket. Ezután inicializálja az objektumokat a [telepítési parancsfájl](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) végrehajtásával az adatbázison. Ez a telepítési parancsfájl létrehozza az adatforrásokat, az adatbázis-hatókörrel rendelkező hitelesítő adatokat, valamint az ezekben a mintákban lévő adatok olvasásához használt külső fájlformátumokat.

A cikk utasításait követve adatforrásokat, adatbázis-hatókörrel rendelkező hitelesítő adatokat és külső fájlformátumokat hozhat létre, amelyek az adatok kimeneti tárolóba való írásához használatosak.

## <a name="create-external-table-as-select"></a>Külső tábla létrehozása kijelölésként

A CREATE EXTERNAL TABLE AS SELECT (CETAS) utasítás használatával tárolhatja a lekérdezési eredményeket a tárolóban.

> [!NOTE]
> Módosítsa a lekérdezés első sorát, azaz: [mydbname], így Ön a létrehozott adatbázist használja.

```sql
USE [mydbname];
GO

CREATE DATABASE SCOPED CREDENTIAL [SasTokenWrite]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO

CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH (
    LOCATION = 'https://<storage account name>.blob.core.windows.net/csv', CREDENTIAL = [SasTokenWrite]
);
GO

CREATE EXTERNAL FILE FORMAT [ParquetFF] WITH (
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
);
GO

CREATE EXTERNAL TABLE [dbo].[PopulationCETAS] WITH (
        LOCATION = 'populationParquet/',
        DATA_SOURCE = [MyDataSource],
        FILE_FORMAT = [ParquetFF]
) AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'csv/population-unix/population.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0'
    ) WITH (
        CountryCode varchar(4),
        CountryName varchar(64),
        Year int,
        PopulationCount int
    ) AS r;

```

> [!NOTE]
> Módosítania kell ezt a parancsfájlt, és módosítania kell a célhelyet, hogy újra végre lehessen hajtani. Külső táblák nem hozhatók létre azon a helyen, ahol már vannak adatforrások.

## <a name="use-the-external-table"></a>A külső tábla használata

Használhatja a CETAS által létrehozott külső táblázatot, például egy normál külső táblázatot.

> [!NOTE]
> Módosítsa a lekérdezés első sorát, azaz: [mydbname], így Ön a létrehozott adatbázist használja.

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM PopulationCETAS
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="remarks"></a>Megjegyzések

Az eredmények tárolása után a külső tábla adatai nem módosíthatók. Ezt a parancsfájlt nem lehet megismételni, mert a CETAS nem írja felül az előző végrehajtás során létrehozott mögöttes adatokat. Szavazzon az alábbi visszajelzési elemekre, ha ezek némelyike szükséges a forgatókönyvekben, vagy az új Azure feedback-webhelyre kell javaslatot tennie:
- [Új adatbevitel engedélyezése külső táblába](https://feedback.azure.com/forums/307516-azure-synapse-analytics/suggestions/32981347-polybase-allow-insert-new-data-to-existing-exteran)
- [Adatok törlésének engedélyezése külső táblából](https://feedback.azure.com/forums/307516-azure-synapse-analytics/suggestions/15158034-polybase-delete-from-external-tables)
- [Partíciók meghatározása a CETAS-ben](https://feedback.azure.com/forums/307516-azure-synapse-analytics/suggestions/19520860-polybase-partitioned-by-functionality-when-creati)
- [Fájlméretek és darabszámok meghatározása](https://feedback.azure.com/forums/307516-azure-synapse-analytics/suggestions/42263617-cetas-specify-number-of-parquet-files-file-size)

## <a name="next-steps"></a>Következő lépések

A különböző fájltípusok lekérdezésével kapcsolatos további információkért tekintse meg az [egyszerű CSV-fájl lekérdezése](query-single-csv-file.md), a [Parquet-fájlok lekérdezése](query-parquet-files.md)és a [JSON-fájlok lekérdezése](query-json-files.md) című cikket.
