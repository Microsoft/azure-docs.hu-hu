---
title: Adatok betöltése a Azure Data Lake Storageból
description: A MÁSOLÁSi utasítással adatok tölthetők be a Azure Data Lake Storageból a dedikált SQL-készletekbe.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: ca57c6200cf7006a89be4b1fd621974559e5b514
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104606123"
---
# <a name="load-data-from-azure-data-lake-storage-into-dedicated-sql-pools-in-azure-synapse-analytics"></a>Adatok betöltése a Azure Data Lake Storageból dedikált SQL-készletekbe az Azure szinapszis Analyticsben

Ez az útmutató ismerteti, hogyan tölthetők be az adatok a Azure Data Lake Storageból a [copy utasítás](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) használatával. Ha gyors példákat kíván használni a MÁSOLÁSi utasítás használatával az összes hitelesítési módszer esetében, látogasson el a következő dokumentációba: [adatok biztonságos betöltése DEDIKÁLT SQL-készletek használatával](./quickstart-bulk-load-copy-tsql-examples.md).

> [!NOTE]  
> Ha visszajelzést vagy jelentést szeretne küldeni a COPY utasításban, küldjön egy e-mailt a következő terjesztési listára: sqldwcopypreview@service.microsoft.com .
>
> [!div class="checklist"]
>
> * Hozza létre a cél táblát az adatok Azure Data Lake Storageból való betöltéséhez.
> * Hozza létre a MÁSOLÁSi utasítást az adatok adattárházba való betöltéséhez.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="before-you-begin"></a>Előkészületek

Az oktatóanyag megkezdése előtt töltse le és telepítse az [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SSMS) legújabb verzióját.

Az oktatóanyag futtatásához a következőkre lesz szüksége:

* Egy dedikált SQL-készlet. Lásd: [DEDIKÁLT SQL-készlet létrehozása és a lekérdezési adat](create-data-warehouse-portal.md).
* Egy Data Lake Storage-fiók. Lásd: [Azure Data Lake Storage első lépései](../../data-lake-store/data-lake-store-get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Ehhez a Storage-fiókhoz konfigurálnia kell vagy meg kell adnia a következő hitelesítő adatok egyikét a betöltéshez: A Storage-fiók kulcsa, a közös hozzáférésű aláírás (SAS) kulcsa, az Azure Directory alkalmazás felhasználója vagy egy olyan HRE-felhasználó, amely rendelkezik a megfelelő Azure-szerepkörrel a Storage-fiókhoz.

## <a name="create-the-target-table"></a>A céltábla létrehozása

Kapcsolódjon a dedikált SQL-készlethez, és hozza létre a cél táblát, amelyet be szeretne tölteni. Ebben a példában egy cikkdimenzió-táblázatot hozunk létre.

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

Kapcsolódjon az SQL dedikált készletéhez, és futtassa a COPY utasítást. A példák teljes listáját a következő dokumentációban tekintheti meg: [biztonságos adattárolás DEDIKÁLT SQL-készletek használatával](./quickstart-bulk-load-copy-tsql-examples.md).

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

Alapértelmezés szerint a táblák fürtözött oszlopcentrikus-indexként vannak meghatározva. A betöltés befejeződése után előfordulhat, hogy egyes adatsorok nem lesznek tömörítve a oszlopcentrikus.  Ennek számos oka lehet. További információ: [oszlopcentrikus indexek kezelése](sql-data-warehouse-tables-index.md).

A lekérdezés teljesítményének és a oszlopcentrikus a terhelés utáni tömörítésének optimalizálásához építse újra a táblázatot a oszlopcentrikus index kikényszerítéséhez az összes sor tömörítéséhez.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Statisztikák optimalizálása

A legjobb, ha egy terhelés után azonnal létrehoz egy egyoszlopos statisztikát. Néhány statisztikai lehetőség is rendelkezésre áll. Ha például egyoszlopos statisztikát hoz létre minden egyes oszlophoz, akkor hosszú időt vehet igénybe az összes statisztika újraépítéséhez. Ha tudja, hogy bizonyos oszlopok nem szerepelnek a lekérdezési predikátumokban, kihagyhatja ezeket az oszlopokat a statisztikák létrehozásával.

Ha úgy dönt, hogy egyoszlopos statisztikát hoz létre minden táblázat minden oszlopához, használhatja a statisztikai cikk tárolt eljárás kódja mintáját `prc_sqldw_create_stats` . [](sql-data-warehouse-tables-statistics.md)

Az alábbi példa jó kiindulási pont a statisztikák létrehozásához. Egyoszlopos statisztikát hoz létre a dimenzió tábla minden egyes oszlopán, valamint a táblák egyes összekapcsolási oszlopaiban. Később is hozzáadhat egy vagy több oszlopos statisztikát más táblák oszlopaihoz.

## <a name="achievement-unlocked"></a>A megvalósítás feloldva!

Sikeresen betöltötte az adatait az adattárházba. Remek!

## <a name="next-steps"></a>Következő lépések
Az adatraktár-megoldás az Azure szinapszis Analytics használatával történő fejlesztésének első lépése az adatgyűjtés. Tekintse meg fejlesztési erőforrásait.

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan fejleszthet táblázatokat az adattárházak számára](sql-data-warehouse-tables-overview.md)

További példákat és referenciákat a következő dokumentációban talál:
- [A COPY utasítás referenciájának dokumentációja](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax)
- [Példák másolása az egyes hitelesítési módszerekhez](./quickstart-bulk-load-copy-tsql-examples.md)
- [Rövid útmutató másolása egyetlen táblához](./quickstart-bulk-load-copy-tsql.md)