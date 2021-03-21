---
title: Külső táblák létrehozása és használata a kiszolgáló nélküli SQL-készletben
description: Ebből a szakaszból megtudhatja, hogyan hozhat létre és használhat külső táblákat a kiszolgáló nélküli SQL-készletben.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 5dcd4b7b76752ca5396fc68afc8d4c8e4e1edca5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96462298"
---
# <a name="create-and-use-external-tables-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Külső táblák létrehozása és használata kiszolgáló nélküli SQL-készlettel az Azure szinapszis Analyticsben

Ebből a szakaszból megtudhatja, hogyan hozhat létre és használhat [külső táblákat](develop-tables-external-tables.md) a kiszolgáló nélküli SQL-készletben. A külső táblák akkor hasznosak, ha a kiszolgáló nélküli SQL-készletben lévő külső adatbázisokhoz való hozzáférést szeretné vezérelni, és ha olyan eszközöket szeretne használni, mint például a Power BI, a kiszolgáló nélküli SQL-készlettel együtt. A külső táblák két típusú tárterületet érhetnek el:
- Nyilvános tárterület, ahol a felhasználók hozzáférhetnek a nyilvános tárolási fájlokhoz.
- Védett tároló, amelyben a felhasználók az SAS hitelesítő adatokkal, az Azure AD-identitással vagy a szinapszis munkaterület felügyelt identitásával férnek hozzá a tárolóhelyekhez.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként létre kell hoznia egy adatbázist, amelybe a táblákat létre kívánja hozni. Ezután inicializálja az objektumokat a [telepítési parancsfájl](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) végrehajtásával az adatbázison. Ez a telepítési parancsfájl a következő, a mintában használt objektumokat fogja létrehozni:
- Az ADATBÁZIShoz kötődő HITELESÍTő adatok `sqlondemand` , amelyek lehetővé teszik a hozzáférést az SAS által védett `https://sqlondemandstorage.blob.core.windows.net` Azure Storage-fiókhoz.

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL [sqlondemand]
    WITH IDENTITY='SHARED ACCESS SIGNATURE',  
    SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
    ```

- KÜLSŐ ADATFORRÁS, `sqlondemanddemo` amely sas-kulccsal védett demó Storage-fiókra hivatkozik, illetve `YellowTaxi` a nyilvánosan elérhető Azure Storage-fiókra hivatkozó külső adatforrások `https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/` .

    ```sql
    CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
        LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
        CREDENTIAL = sqlondemand
    );
    GO
    CREATE EXTERNAL DATA SOURCE YellowTaxi
    WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/')
    ```

- `QuotedCSVWithHeaderFormat` `ParquetFormat` A CSV-és a parketta-fájltípusokat leíró fájlformátumok.

    ```sql
    CREATE EXTERNAL FILE FORMAT QuotedCsvWithHeaderFormat
    WITH (  
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( FIELD_TERMINATOR = ',', STRING_DELIMITER = '"', FIRST_ROW = 2   )
    );
    GO
    CREATE EXTERNAL FILE FORMAT ParquetFormat WITH (  FORMAT_TYPE = PARQUET );
    ```

A cikkben szereplő lekérdezések a mintául szolgáló adatbázison lesznek végrehajtva, és ezeket az objektumokat használják. 

## <a name="create-an-external-table-on-protected-data"></a>Külső tábla létrehozása a védett adatelemeken

Olyan külső táblákat hozhat létre, amelyek egy Azure Storage-fiókban lévő adatokhoz férnek hozzá, amelyek lehetővé teszik a felhasználók számára az Azure AD-identitással vagy SAS-kulccsal való hozzáférést. Külső táblákat ugyanúgy hozhat létre, mint a normál SQL Server külső táblákat. 

A következő lekérdezés egy külső táblát hoz létre, amely beolvassa *population.csv* fájlt a SynapseSQL demo Azure Storage-fiókból, amely az adatforrás használatával hivatkozik, `sqlondemanddemo` és az adatbázis-hatókörű hitelesítő adatokkal van ellátva `sqlondemand` . 

Az adatforrás és az adatbázis hatókörű hitelesítő adatai a [telepítési parancsfájlban](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)jönnek létre.

> [!NOTE]
> Módosítsa a lekérdezés első sorát, azaz: [mydbname], így Ön a létrehozott adatbázist használja. 

```sql
USE [mydbname];
GO
CREATE EXTERNAL TABLE populationExternalTable
(
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
)
WITH (
    LOCATION = 'csv/population/population.csv',
    DATA_SOURCE = sqlondemanddemo,
    FILE_FORMAT = QuotedCSVWithHeaderFormat
);
```

## <a name="create-an-external-table-on-public-data"></a>Külső tábla létrehozása nyilvános adatelemeken

Olyan külső táblákat is létrehozhat, amelyek a nyilvánosan elérhető Azure Storage-ban elhelyezett fájlokból származó adatokból olvasnak be. Ez a [telepítési parancsfájl](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) létrehozza a nyilvános külső adatforrást és a parketta fájlformátumának definícióját, amelyet a következő lekérdezés használ:

```sql
CREATE EXTERNAL TABLE Taxi (
     vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
     pickup_datetime DATETIME2, 
     dropoff_datetime DATETIME2,
     passenger_count INT,
     trip_distance FLOAT,
     fare_amount FLOAT,
     tip_amount FLOAT,
     tolls_amount FLOAT,
     total_amount FLOAT
) WITH (
         LOCATION = 'puYear=*/puMonth=*/*.parquet',
         DATA_SOURCE = YellowTaxi,
         FILE_FORMAT = ParquetFormat
);
```
## <a name="use-an-external-table"></a>Külső tábla használata

A lekérdezésekben a [külső táblákat](develop-tables-external-tables.md) ugyanúgy használhatja, mint SQL Server lekérdezésekben.

A következő lekérdezés azt mutatja be, hogy az előző szakaszban létrehozott, a *populáció* külső tábláját használja. Az ország/régió nevét adja vissza a 2019-es populációban csökkenő sorrendben.

> [!NOTE]
> Módosítsa a lekérdezés első sorát, azaz: [mydbname], így Ön a létrehozott adatbázist használja.

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationExternalTable
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Következő lépések

A lekérdezés eredményeinek a tárolóra való tárolásával kapcsolatos további információkért tekintse meg az [áruház lekérdezési eredményeit a Storage](../sql/create-external-table-as-select.md) cikkben.
