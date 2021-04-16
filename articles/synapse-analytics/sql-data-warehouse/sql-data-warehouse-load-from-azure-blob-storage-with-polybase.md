---
title: A Contoso kiskereskedelmi adatainak betöltése dedikált SQL-készletekbe
description: PolyBase- és T-SQL-parancsokkal töltsön be két táblát a Contoso kiskereskedelmi adataiból dedikált SQL-készletekbe.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: b1afcdfa74245eb566663d5dec6ce2e2276fbdc8
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568233"
---
# <a name="load-contoso-retail-data-into-dedicated-sql-pools-in-azure-synapse-analytics"></a>A Contoso kiskereskedelmi adatainak betöltése dedikált SQL-készletekbe a Azure Synapse Analytics

Ebből az oktatóanyagból megtudhatja, hogyan használhat PolyBase- és T-SQL-parancsokat két tábla a Contoso kereskedelmi adataiból dedikált SQL-készletekbe való betöltéséhez.

Ebben az oktatóanyagban a következőt fogja:

1. A PolyBase konfigurálása az Azure Blob Storage-ból való betöltésre
2. Nyilvános adatok betöltése az adatbázisba
3. Optimalizálás végrehajtása a betöltés befejezése után.

## <a name="before-you-begin"></a>Előkészületek

Az oktatóanyag futtatásához olyan Azure-fiókra lesz szüksége, amely már rendelkezik dedikált SQL-készlet használatával. Ha nem rendelkezik kiépítve adattárházkal, tekintse meg az adattárház létrehozása és a kiszolgálószintű [tűzfalszabály beállítását.](create-data-warehouse-portal.md)

## <a name="configure-the-data-source"></a>Az adatforrás konfigurálása

A PolyBase T-SQL külső objektumok használatával határozza meg a külső adatok helyét és attribútumát. A külső objektumdefiníciókat a rendszer dedikált SQL-készletekben tárolja. Az adatok külsőleg tárolódnak.

## <a name="create-a-credential"></a>Hitelesítő adat létrehozása

**Ha a** Contoso nyilvános adatait tölti be, hagyja ki ezt a lépést. Nincs szükség biztonságos hozzáférésre a nyilvános adatokhoz, mivel az már bárki számára elérhető.

**Ne hagyja ki ezt a lépést,** ha ezt az oktatóanyagot sablonként használja saját adatok betöltéséhez. Az adatok hitelesítő adatokon keresztüli eléréséhez használja a következő szkriptet egy adatbázisra vonatkozó hitelesítő adat létrehozásához. Ezután használja az adatforrás helyének meghatározásához.

```sql
-- A: Create a master key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.


CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;


-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure blob storage.
-- LOCATION: Provide Azure storage account name and blob container name.
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'wasbs://<blob_container_name>@<azure_storage_account_name>.blob.core.windows.net',
    CREDENTIAL = AzureStorageCredential
);
```

## <a name="create-the-external-data-source"></a>A külső adatforrás létrehozása

Ezzel a [CREATE EXTERNAL DATA SOURCE paranccsal](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) tároljuk az adatok helyét és az adattípust.

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH
(  
    TYPE = Hadoop
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
);
```

> [!IMPORTANT]
> Ha úgy dönt, hogy nyilvánosra teszi az Azure Blob Storage-tárolókat, ne feledje, hogy adattulajdonosként az adatközpontból kilépő adatokért díjat kell fizetnie az adatbehozásért.

## <a name="configure-the-data-format"></a>Az adatformátum konfigurálása

Az adatok szövegfájlokban vannak tárolva az Azure Blob Storage-ban, és az egyes mezőket elválasztójel választja el egymástól. Az SSMS-ben futtassa az alábbi CREATE EXTERNAL FILE FORMAT parancsot a szövegfájlokban lévő adatok formátumának megadásához. A Contoso adatai tömörítetlenek, és a pipe tagolt.

```sql
CREATE EXTERNAL FILE FORMAT TextFileFormat
WITH
(   FORMAT_TYPE = DELIMITEDTEXT
,    FORMAT_OPTIONS    (   FIELD_TERMINATOR = '|'
                    ,    STRING_DELIMITER = ''
                    ,    DATE_FORMAT         = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,    USE_TYPE_DEFAULT = FALSE
                    )
);
```

## <a name="create-the-schema-for-the-external-tables"></a>A külső táblák sémának létrehozása

Most, hogy az adatforrás és a fájlformátum meg van adva, készen áll a külső táblák sémája létrehozására.

Hozzon létre egy helyet a Contoso adatainak az adatbázisban való tárolására, hozzon létre egy sémát.

```sql
CREATE SCHEMA [asb]
GO
```

## <a name="create-the-external-tables"></a>A külső táblák létrehozása

Futtassa a következő szkriptet a DimProduct és FactOnlineSales külső táblák létrehozásához. Itt csak definiálja az oszlopneveket és az adattípusokat, és az Azure Blob Storage-fájlok helyéhez és formátumhoz köti őket. A definíció az adattárházban van tárolva, és az adatok továbbra is a Azure Storage Blob.

A  **LOCATION** paraméter a mappában található gyökérmappában található Azure Storage Blob. Minden tábla más mappában található.

```sql
--DimProduct
CREATE EXTERNAL TABLE [asb].DimProduct (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL,
    [ProductDescription] [nvarchar](400) NULL,
    [ProductSubcategoryKey] [int] NULL,
    [Manufacturer] [nvarchar](50) NULL,
    [BrandName] [nvarchar](50) NULL,
    [ClassID] [nvarchar](10) NULL,
    [ClassName] [nvarchar](20) NULL,
    [StyleID] [nvarchar](10) NULL,
    [StyleName] [nvarchar](20) NULL,
    [ColorID] [nvarchar](10) NULL,
    [ColorName] [nvarchar](20) NOT NULL,
    [Size] [nvarchar](50) NULL,
    [SizeRange] [nvarchar](50) NULL,
    [SizeUnitMeasureID] [nvarchar](20) NULL,
    [Weight] [float] NULL,
    [WeightUnitMeasureID] [nvarchar](20) NULL,
    [UnitOfMeasureID] [nvarchar](10) NULL,
    [UnitOfMeasureName] [nvarchar](40) NULL,
    [StockTypeID] [nvarchar](10) NULL,
    [StockTypeName] [nvarchar](40) NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [AvailableForSaleDate] [datetime] NULL,
    [StopSaleDate] [datetime] NULL,
    [Status] [nvarchar](7) NULL,
    [ImageURL] [nvarchar](150) NULL,
    [ProductURL] [nvarchar](150) NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/DimProduct/'
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

--FactOnlineSales
CREATE EXTERNAL TABLE [asb].FactOnlineSales
(
    [OnlineSalesKey] [int]  NOT NULL,
    [DateKey] [datetime] NOT NULL,
    [StoreKey] [int] NOT NULL,
    [ProductKey] [int] NOT NULL,
    [PromotionKey] [int] NOT NULL,
    [CurrencyKey] [int] NOT NULL,
    [CustomerKey] [int] NOT NULL,
    [SalesOrderNumber] [nvarchar](20) NOT NULL,
    [SalesOrderLineNumber] [int] NULL,
    [SalesQuantity] [int] NOT NULL,
    [SalesAmount] [money] NOT NULL,
    [ReturnQuantity] [int] NOT NULL,
    [ReturnAmount] [money] NULL,
    [DiscountQuantity] [int] NULL,
    [DiscountAmount] [money] NULL,
    [TotalCost] [money] NOT NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/FactOnlineSales/'
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;
```

## <a name="load-the-data"></a>Az adatok betöltése

A külső adatok elérésének különböző módjai vannak.  Közvetlenül lekérdezhet adatokat a külső táblákból, betöltheti az adatokat az adattárház új tábláiba, vagy külső adatokat adhat hozzá a meglévő adattárháztáblákhoz.  

### <a name="create-a-new-schema"></a>Új séma létrehozása

A CTAS létrehoz egy új táblát, amely adatokat tartalmaz.  Először hozzon létre egy sémát a contoso adataihoz.

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="load-the-data-into-new-tables"></a>Adatok betöltése új táblákba

Az Adatok Azure Blob Storage-ból az adattárház táblába való betöltéséhez használja a [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) utasítást. A [CTAS-val történő](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) betöltés a létrehozott, erősen típusos külső táblákat használja. Az adatok új táblákba való betöltéséhez táblánként egy CTAS utasítást használjon.

A CTAS létrehoz egy új táblát, és feltölti egy select utasítás eredményeivel. A CTAS úgy definiálja az új táblát, hogy a select utasítás eredményeivel azonos oszlopokkal és adattípusokkal legyen. Ha az összes oszlopot egy külső táblából választja ki, az új tábla a külső tábla oszlopainak és adattípusainak replikája lesz.

Ebben a példában kivonatos elosztott táblákként hozjuk létre a dimenziót és a ténytáblát is.

```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="track-the-load-progress"></a>A betöltési folyamat nyomon követése

A terhelés előrehaladását dinamikus felügyeleti nézetek (DMV-k) segítségével követheti nyomon.

```sql
-- To see all requests
SELECT * FROM sys.dm_pdw_exec_requests;

-- To see a particular request identified by its label
SELECT * FROM sys.dm_pdw_exec_requests as r
WHERE r.[label] = 'CTAS : Load [cso].[DimProduct]             '
      OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
;

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files,
    sum(s.bytes_processed)/1024/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
WHERE
    r.[label] = 'CTAS : Load [cso].[DimProduct]             '
    OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="optimize-columnstore-compression"></a>Oszlopcentrikus tömörítés optimalizálása

Alapértelmezés szerint a dedikált SQL-készletek fürtözött oszlopcentrikus indexként tárolják a táblát. A betöltés befejezése után előfordulhat, hogy egyes adatsorok nem lesznek az oszloptárba tömörítve.  Ennek több oka is lehet. További információ: [Oszlopcentrikus indexek kezelése.](sql-data-warehouse-tables-index.md)

A lekérdezési teljesítmény és az oszlopcentrikus tömörítés terhelés utáni optimalizálásához építse újra a táblát, hogy az oszlopcentrikus index az összes sort tömörítse.

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

Az oszlopcentrikus indexek karbantartásával kapcsolatos további információkért tekintse meg az oszlopcentrikus [indexek kezelését tartalmazó cikket.](sql-data-warehouse-tables-index.md)

## <a name="optimize-statistics"></a>Statisztikák optimalizálása

A legjobb egyoszlopos statisztikákat közvetlenül a betöltés után létrehozni. Ha tudja, hogy bizonyos oszlopok nem lesznek a lekérdezési predikátumban, kihagyhatja az oszlopok statisztikáinak létrehozását. Ha minden oszlophoz egyoszlopos statisztikákat hoz létre, az összes statisztika újraépítése hosszú időbe is tért.

Ha úgy dönt, hogy minden tábla minden oszlopához egyoszlopos statisztikákat hoz létre, használhatja a tárolt eljárás kódmintáját a `prc_sqldw_create_stats` [statisztikai cikkben.](sql-data-warehouse-tables-statistics.md)

Az alábbi példa jó kiindulási pont a statisztikák létrehozásához. Egyoszlopos statisztikákat hoz létre a dimenziótábla minden egyes oszlopához, valamint a ténytáblák minden egyes egymáshoz csatlakozó oszlopához. Később mindig hozzáadhat egy- vagy többoszlopos statisztikákat más ténytábla-oszlopokhoz.

```sql
CREATE STATISTICS [stat_cso_DimProduct_AvailableForSaleDate] ON [cso].[DimProduct]([AvailableForSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_BrandName] ON [cso].[DimProduct]([BrandName]);
CREATE STATISTICS [stat_cso_DimProduct_ClassID] ON [cso].[DimProduct]([ClassID]);
CREATE STATISTICS [stat_cso_DimProduct_ClassName] ON [cso].[DimProduct]([ClassName]);
CREATE STATISTICS [stat_cso_DimProduct_ColorID] ON [cso].[DimProduct]([ColorID]);
CREATE STATISTICS [stat_cso_DimProduct_ColorName] ON [cso].[DimProduct]([ColorName]);
CREATE STATISTICS [stat_cso_DimProduct_ETLLoadID] ON [cso].[DimProduct]([ETLLoadID]);
CREATE STATISTICS [stat_cso_DimProduct_ImageURL] ON [cso].[DimProduct]([ImageURL]);
CREATE STATISTICS [stat_cso_DimProduct_LoadDate] ON [cso].[DimProduct]([LoadDate]);
CREATE STATISTICS [stat_cso_DimProduct_Manufacturer] ON [cso].[DimProduct]([Manufacturer]);
CREATE STATISTICS [stat_cso_DimProduct_ProductDescription] ON [cso].[DimProduct]([ProductDescription]);
CREATE STATISTICS [stat_cso_DimProduct_ProductKey] ON [cso].[DimProduct]([ProductKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductLabel] ON [cso].[DimProduct]([ProductLabel]);
CREATE STATISTICS [stat_cso_DimProduct_ProductName] ON [cso].[DimProduct]([ProductName]);
CREATE STATISTICS [stat_cso_DimProduct_ProductSubcategoryKey] ON [cso].[DimProduct]([ProductSubcategoryKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductURL] ON [cso].[DimProduct]([ProductURL]);
CREATE STATISTICS [stat_cso_DimProduct_Size] ON [cso].[DimProduct]([Size]);
CREATE STATISTICS [stat_cso_DimProduct_SizeRange] ON [cso].[DimProduct]([SizeRange]);
CREATE STATISTICS [stat_cso_DimProduct_SizeUnitMeasureID] ON [cso].[DimProduct]([SizeUnitMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_Status] ON [cso].[DimProduct]([Status]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeID] ON [cso].[DimProduct]([StockTypeID]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeName] ON [cso].[DimProduct]([StockTypeName]);
CREATE STATISTICS [stat_cso_DimProduct_StopSaleDate] ON [cso].[DimProduct]([StopSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_StyleID] ON [cso].[DimProduct]([StyleID]);
CREATE STATISTICS [stat_cso_DimProduct_StyleName] ON [cso].[DimProduct]([StyleName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitCost] ON [cso].[DimProduct]([UnitCost]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureID] ON [cso].[DimProduct]([UnitOfMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureName] ON [cso].[DimProduct]([UnitOfMeasureName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitPrice] ON [cso].[DimProduct]([UnitPrice]);
CREATE STATISTICS [stat_cso_DimProduct_UpdateDate] ON [cso].[DimProduct]([UpdateDate]);
CREATE STATISTICS [stat_cso_DimProduct_Weight] ON [cso].[DimProduct]([Weight]);
CREATE STATISTICS [stat_cso_DimProduct_WeightUnitMeasureID] ON [cso].[DimProduct]([WeightUnitMeasureID]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CurrencyKey] ON [cso].[FactOnlineSales]([CurrencyKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CustomerKey] ON [cso].[FactOnlineSales]([CustomerKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_DateKey] ON [cso].[FactOnlineSales]([DateKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_OnlineSalesKey] ON [cso].[FactOnlineSales]([OnlineSalesKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_ProductKey] ON [cso].[FactOnlineSales]([ProductKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_PromotionKey] ON [cso].[FactOnlineSales]([PromotionKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_StoreKey] ON [cso].[FactOnlineSales]([StoreKey]);
```

## <a name="achievement-unlocked"></a>Eredmény feloldva!

Sikeresen betöltötte a nyilvános adatokat az adattárházba. Remek!

Most már elkezdheti lekérdezni a táblákat az adatok feltárására. Futtassa a következő lekérdezést a márkaenkénti összes értékesítés kijelzéséhez:

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>Következő lépések

A teljes adatkészlet betöltéséhez futtassa a példát, és töltse be a [contosoi](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md) kereskedelmi adattárházat Microsoft SQL Server mintaadattárból.
További fejlesztési tippekért lásd: Tervezési döntések és kódolási technikák [adattárházhoz.](sql-data-warehouse-overview-develop.md)
