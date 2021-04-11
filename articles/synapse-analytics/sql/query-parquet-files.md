---
title: A Parquet-fájlok lekérdezése kiszolgáló nélküli SQL-készlettel
description: Ebből a cikkből megtudhatja, hogyan kérdezheti le a Parquet-fájlokat a kiszolgáló nélküli SQL-készlet használatával.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 72bf8e76217e8a3bcecd381d8d61815c539dd196
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107209587"
---
# <a name="query-parquet-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>A Parquet-fájlok lekérdezése kiszolgáló nélküli SQL-készlettel az Azure szinapszis Analyticsben

Ebből a cikkből megtudhatja, hogyan írhat egy lekérdezést olyan kiszolgáló nélküli SQL-készlet használatával, amely beolvassa a parketta-fájlokat.

## <a name="quickstart-example"></a>Rövid útmutató – példa

`OPENROWSET` a függvény lehetővé teszi a parketta-fájl tartalmának olvasását a fájl URL-címének megadásával.

### <a name="read-parquet-file"></a>Parketta-fájl olvasása

A fájl tartalmának megtekintésére a legegyszerűbb módszer, ha a `PARQUET` fájl URL-címét megadja a `OPENROWSET` függvénynek, és megadja a parkettát `FORMAT` . Ha a fájl nyilvánosan elérhető, vagy ha az Azure AD-identitása hozzáfér ehhez a fájlhoz, akkor az alábbi példában látható módon láthatja a fájl tartalmát:

```sql
select top 10 *
from openrowset(
    bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet',
    format = 'parquet') as rows
```

Győződjön meg arról, hogy el tudja érni ezt a fájlt. Ha a fájl SAS-kulccsal vagy egyéni Azure-identitással védett, akkor az SQL-bejelentkezéshez be kell állítania a [kiszolgálói szintű hitelesítő adatokat](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential).

> [!IMPORTANT]
> Győződjön meg arról, hogy UTF-8 adatbázis-rendezést használ (például `Latin1_General_100_BIN2_UTF8` ), mert a parkettázott fájlokban lévő karakterlánc-értékek UTF-8 kódolással vannak kódolva.
> A PARQUEt fájlban lévő szöveg kódolása nem egyezik, és a rendezés váratlan konverziós hibákat okozhat.
> Az aktuális adatbázis alapértelmezett rendezését az alábbi T-SQL-utasítás használatával egyszerűen módosíthatja: `alter database current collate Latin1_General_100_BIN2_UTF8`

### <a name="data-source-usage"></a>Adatforrás használata

Az előző példa a fájl teljes elérési útját használja. Alternatív megoldásként létrehozhat egy külső adatforrást is, amely a tároló gyökérkönyvtárára mutat, és ezt az adatforrást és a fájl relatív elérési útját használja a következő `OPENROWSET` függvényben:

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.parquet',
        data_source = 'covid',
        format = 'parquet'
    ) as rows
```

Ha egy adatforrás SAS-kulccsal vagy egyéni identitással védett, az [adatforrást adatbázis-hatókörű hitelesítő](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential)adatokkal is konfigurálhatja.

### <a name="explicitly-specify-schema"></a>Séma explicit meghatározása

`OPENROWSET` lehetővé teszi explicit módon megadhatja, hogy mely oszlopokat szeretné beolvasni a fájlból a `WITH` záradék használatával:

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.parquet',
        data_source = 'covid',
        format = 'parquet'
    ) with ( date_rep date, cases int, geo_id varchar(6) ) as rows
```

> [!IMPORTANT]
> Győződjön meg arról, hogy a explicilty valamilyen UTF-8 rendezést határoz meg (például `Latin1_General_100_BIN2_UTF8` ) a záradékban található összes karakterlánc-oszlophoz, `WITH` vagy állítson be néhány UTF-8 rendezést az adatbázis szintjén.
> A fájl-és karakterlánc-oszlopok rendezésének szöveges kódolása nem megfelelő, ezért váratlan konverziós hibák merülhetnek fel.
> Az aktuális adatbázis alapértelmezett rendezését az alábbi T-SQL-utasítás használatával egyszerűen módosíthatja: `alter database current collate Latin1_General_100_BIN2_UTF8`
> A UnitPrice-típusok rendezését könnyedén megadhatja a következő definíció használatával: `geo_id varchar(6) collate Latin1_General_100_BIN2_UTF8`

A következő részekben láthatja, hogyan lehet lekérdezni a különböző típusú parketta-fájlokat.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként létre kell **hoznia egy adatbázist** egy olyan adatforrással, amely a [New York Yellow taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) Storage-fiókra hivatkozik. Ezután inicializálja az objektumokat a [telepítési parancsfájl](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) végrehajtásával az adatbázison. Ez a telepítési parancsfájl létrehozza az adatforrásokat, az adatbázis-hatókörrel rendelkező hitelesítő adatokat, valamint az ezekben a mintákban használt külső fájlformátumokat.

## <a name="dataset"></a>Adathalmaz

Ebben a példában a [New York Yellow taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) -adatkészletet használjuk. A parketta-fájlok lekérdezése ugyanúgy végezhető el, mint a [CSV-fájlok olvasása](query-parquet-files.md). Az egyetlen különbség, hogy a `FILEFORMAT` paramétert a értékre kell beállítani `PARQUET` . A jelen cikkben szereplő példák a Parquet-fájlok olvasásának sajátosságait mutatják be.

## <a name="query-set-of-parquet-files"></a>A Parquet-fájlok lekérdezési készlete

A Parquet-fájlok lekérdezése során csak a fontos oszlopokat adhatja meg.

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) WITH (
        tpepPickupDateTime DATETIME2,
        passengerCount INT
    ) AS nyc
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="automatic-schema-inference"></a>Automatikus séma-következtetés

A Parquet-fájlok olvasásakor nem szükséges a OPENROWSET WITH záradék használata. Az oszlopnevek és az adattípusok automatikusan beolvashatók a Parquet-fájlokból.

Az alábbi minta a Parquet-fájlok automatikus sémájának következtetéseit mutatja be. A sorok számát a 2018 szeptemberében adja vissza a séma meghatározása nélkül.

> [!NOTE]
> A Parquet-fájlok olvasásakor nem szükséges oszlopokat megadni a OPENROWSET WITH záradékban. Ebben az esetben a kiszolgáló nélküli SQL Pool Query szolgáltatás metaadatokat használ a Parquet fájlban, és az oszlopokat név alapján köti össze.

```sql
SELECT TOP 10 *
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=9/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) AS nyc
```

### <a name="query-partitioned-data"></a>Particionált adatlekérdezés

Az ebben a mintában megadott adathalmaz külön almappákba van osztva (particionálva). A filepath függvény használatával konkrét partíciókat is megcélozhat. Ez a példa a viteldíjak összegét mutatja év, hónap és payment_type szerint az 2017-as első három hónapban.

> [!NOTE]
> A kiszolgáló nélküli SQL-készlet lekérdezése kompatibilis a kaptár/Hadoop particionálási sémával.

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=*/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) nyc
WHERE
    nyc.filepath(1) = 2017
    AND nyc.filepath(2) IN (1, 2, 3)
    AND tpepPickupDateTime BETWEEN CAST('1/1/2017' AS datetime) AND CAST('3/31/2017' AS datetime)
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="type-mapping"></a>Típus leképezése

A Parquet típusú SQL natív típushoz való leképezéshez a [Parquet típusú leképezéshez](develop-openrowset.md#type-mapping-for-parquet).

## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan lehet [lekérdezni a parketta beágyazott típusait](query-parquet-nested-types.md).
