---
title: Oktatóanyag adatok betöltése a Azure Data Lake Storage
description: A COPY utasítással töltse be az adatokat a Azure Data Lake Storage SQL-készletekhez.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 16f95a86169be04eba202b311fc4437b204ec8b3
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566528"
---
# <a name="load-data-from-azure-data-lake-storage-into-dedicated-sql-pools-in-azure-synapse-analytics"></a>Adatok betöltése Azure Data Lake Storage sql-készletekbe a Azure Synapse Analytics

Ez az útmutató azt ismerteti, hogyan használható a [COPY utasítás](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) adatok betöltésére a Azure Data Lake Storage. A COPY utasításnak az összes hitelesítési módszerre való használatával kapcsolatos gyors példákért látogasson el a következő dokumentációra: Adatok biztonságos betöltése [dedikált SQL-készletekkel.](./quickstart-bulk-load-copy-tsql-examples.md)

> [!NOTE]  
> Ha visszajelzést szeretne küldeni vagy problémákat szeretne jelenteni a COPY utasítással kapcsolatban, küldjön egy e-mailt a következő terjesztési listára: sqldwcopypreview@service.microsoft.com .
>
> [!div class="checklist"]
>
> * Hozza létre a céltáblát az adatok betöltéséhez a Azure Data Lake Storage.
> * Hozza létre a COPY utasítást az adatok adattárházba való betöltéséhez.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="before-you-begin"></a>Előkészületek

Az oktatóanyag megkezdése előtt töltse le és telepítse az [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SSMS) legújabb verzióját.

Az oktatóanyag futtatásához a következőre lesz szüksége:

* Dedikált SQL-készlet. Lásd: [Dedikált SQL-készlet létrehozása és adatok lekérdezése.](create-data-warehouse-portal.md)
* Egy Data Lake Storage fiók. Lásd: [Első lépések a Azure Data Lake Storage.](../../data-lake-store/data-lake-store-get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Ehhez a tárfiókhoz be kell állítania vagy meg kell adnia a következő hitelesítő adatok egyikét: Egy tárfiókkulcs, közös hozzáférésű jogosultság jogosultsága (SAS-) kulcs, Egy Azure Directory-alkalmazásfelhasználó vagy egy AAD-felhasználó, amely a tárfiókhoz megfelelő Azure-szerepkörsel rendelkezik.

## <a name="create-the-target-table"></a>A céltábla létrehozása

Csatlakozzon a dedikált SQL-készlethez, és hozza létre azt a céltáblát, amelybe be fogja tölteni. Ebben a példában egy termékdimenzió-táblát hozunk létre.

```sql
-- A: Create the target table
-- DimProduct
CREATE TABLE [dbo].[DimProduct]
(
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    DISTRIBUTION = HASH([ProductKey]),
    CLUSTERED COLUMNSTORE INDEX
    --HEAP
);
```


## <a name="create-the-copy-statement"></a>A COPY utasítás létrehozása

Csatlakozzon a dedikált SQL-készlethez, és futtassa a COPY utasítást. A példák teljes listáját a következő dokumentációban találhatja meg: Adatok biztonságos betöltése [dedikált SQL-készletekkel.](./quickstart-bulk-load-copy-tsql-examples.md)

```sql
-- B: Create and execute the COPY statement

COPY INTO [dbo].[DimProduct] 
--The column list allows you map, omit, or reorder input file columns to target table columns. 
--You can also specify the default value when there is a NULL value in the file.
--When the column list is not specified, columns will be mapped based on source and target ordinality
(
    ProductKey default -1 1,
    ProductLabel default 'myStringDefaultWhenNull' 2,
    ProductName default 'myStringDefaultWhenNull' 3
)
--The storage account location where you data is staged
FROM 'https://storageaccount.blob.core.windows.net/container/directory/'
WITH 
(
   --CREDENTIAL: Specifies the authentication method and credential access your storage account
   CREDENTIAL = (IDENTITY = '', SECRET = ''),
   --FILE_TYPE: Specifies the file type in your storage account location
   FILE_TYPE = 'CSV',
   --FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text (CSV) file
   FIELDTERMINATOR = '|',
   --ROWTERMINATOR: Marks the end of a record in the file
   ROWTERMINATOR = '0x0A',
   --FIELDQUOTE: Specifies the delimiter for data of type string in a delimited text (CSV) file
   FIELDQUOTE = '',
   ENCODING = 'UTF8',
   DATEFORMAT = 'ymd',
   --MAXERRORS: Maximum number of reject rows allowed in the load before the COPY operation is canceled
   MAXERRORS = 10,
   --ERRORFILE: Specifies the directory where the rejected rows and the corresponding error reason should be written
   ERRORFILE = '/errorsfolder',
) OPTION (LABEL = 'COPY: ADLS tutorial');
```

## <a name="optimize-columnstore-compression"></a>Oszlopcentrikus tömörítés optimalizálása

Alapértelmezés szerint a táblák fürtözött oszlopcentrikus indexként vannak definiálva. A betöltés befejezése után előfordulhat, hogy egyes adatsorok nem lesznek tömörítve az oszloptárba.  Ennek számos oka lehet. További információ: [Oszlopcentrikus indexek kezelése.](sql-data-warehouse-tables-index.md)

A lekérdezési teljesítmény és az oszlopcentrikus tömörítés terhelés utáni optimalizálásához építse újra a táblát, hogy az oszlopcentrikus index az összes sort tömörítse.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Statisztikák optimalizálása

A legjobb egyoszlopos statisztikákat közvetlenül a betöltés után létrehozni. A statisztikákhoz több lehetőség is rendelkezésre áll. Ha például minden oszlophoz egyoszlopos statisztikákat hoz létre, az összes statisztika újraépítése hosszú időt is vegyen majd át. Ha tudja, hogy bizonyos oszlopok nem lesznek a lekérdezési predikátumban, kihagyhatja az oszlopok statisztikáinak létrehozását.

Ha úgy dönt, hogy minden tábla minden oszlopához egyoszlopos statisztikákat hoz létre, használhatja a tárolt eljárás kódmintáját a `prc_sqldw_create_stats` [statisztikai cikkben.](sql-data-warehouse-tables-statistics.md)

Az alábbi példa jó kiindulási pont a statisztikák létrehozásához. Egyoszlopos statisztikákat hoz létre a dimenziótábla minden egyes oszlopához, valamint a ténytáblák minden egyes egymáshoz csatlakozó oszlopához. Később mindig hozzáadhat egy- vagy többoszlopos statisztikákat más ténytábla-oszlopokhoz.

## <a name="achievement-unlocked"></a>Eredmény feloldva!

Sikeresen betöltötte az adatokat az adattárházba. Remek!

## <a name="next-steps"></a>Következő lépések
Az adatok betöltése az első lépés az adattárház-megoldások fejlesztéséhez a Azure Synapse Analytics. Tekintse meg a fejlesztési forrásainkat.

> [!div class="nextstepaction"]
> [Megtudhatja, hogyan fejleszthet táblákat az adatraktározáshoz](sql-data-warehouse-tables-overview.md)

További betöltési példákért és hivatkozásokért tekintse meg a következő dokumentációt:
- [A COPY utasítás referenciadokumentációja](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax)
- [Példák másolása az egyes hitelesítési módszerekhez](./quickstart-bulk-load-copy-tsql-examples.md)
- [COPY rövid útmutató egyetlen táblához](./quickstart-bulk-load-copy-tsql.md)