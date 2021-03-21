---
title: HOZZon létre külső TÁBLÁT SELECT (CETAS) néven a szinapszis SQL-ben
description: A CREATE EXTERNAL TABLE AS SELECT (CETAS) használata a szinapszis SQL használatával
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 09/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 23b74c06ebd45092b9efe36a870eeac7f6fb828a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101677557"
---
# <a name="cetas-with-synapse-sql"></a>CETAS a szinapszis SQL-sel

A következő feladatok végrehajtásához használhatja a CREATE EXTERNAL TABLE AS SELECT (CETAS) elemet a dedikált SQL-készlet vagy kiszolgáló nélküli SQL-készletben:  

- Külső tábla létrehozása
- A Transact-SQL SELECT utasítás eredményeinek párhuzamos exportálása a következőre:

  - Hadoop
  - Azure Storage Blob
  - 2. generációs Azure Data Lake Storage

## <a name="cetas-in-dedicated-sql-pool"></a>CETAS dedikált SQL-készletben

A dedikált SQL-készlet esetében a CETAS-használat és-szintaxis [beállításnál jelölje be a külső tábla létrehozása a Select](/sql/t-sql/statements/create-external-table-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true) cikkként jelölőnégyzetet. Emellett a dedikált SQL Pool használatával történő CTAS kapcsolatos útmutatásért tekintse meg a [CREATE TABLE as Select](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) cikket.

## <a name="cetas-in-serverless-sql-pool"></a>CETAS a kiszolgáló nélküli SQL-készletben

A kiszolgáló nélküli SQL-készlet használatakor a CETAS egy külső tábla létrehozására és a lekérdezési eredmények exportálására szolgál Azure Storage Blob vagy Azure Data Lake Storage Gen2.

## <a name="syntax"></a>Szintaxis

```syntaxsql
CREATE EXTERNAL TABLE [ [database_name  . [ schema_name ] . ] | schema_name . ] table_name
    WITH (
        LOCATION = 'path_to_folder',  
        DATA_SOURCE = external_data_source_name,  
        FILE_FORMAT = external_file_format_name  
)
    AS <select_statement>  
[;]

<select_statement> ::=  
    [ WITH <common_table_expression> [ ,...n ] ]  
    SELECT <select_criteria>
```

## <a name="arguments"></a>Argumentumok

*[[ *database_name* . [ *schema_name* ]. ] | *schema_name* . ] *table_name**

A létrehozandó tábla egy vagy három részének neve. Külső tábla esetén a kiszolgáló nélküli SQL-készlet csak a tábla metaadatait tárolja. Nem történik tényleges adatok áthelyezése vagy tárolása a kiszolgáló nélküli SQL-készletben.

LOCATION = *' path_to_folder '*

Megadja, hogy hol kell írni a SELECT utasítás eredményeit a külső adatforráson. A gyökérmappa a külső adatforrásban megadott adathely. A HELYnek egy mappára kell mutatnia, és egy záró/karakterrel kell rendelkeznie. Példa: aggregated_data/

DATA_SOURCE = *external_data_source_name*

Annak a külső adatforrás-objektumnak a nevét adja meg, amely a külső adattárolási helyet tartalmazza. Külső adatforrás létrehozásához használja a [külső adatforrás létrehozása (Transact-SQL)](develop-tables-external-tables.md#create-external-data-source)lehetőséget.

FILE_FORMAT = *external_file_format_name*

Megadja a külső fájlformátum objektumának nevét, amely a külső adatfájl formátumát tartalmazza. Külső fájlformátum létrehozásához használja a [create External File Format (Transact-SQL) formátumot](develop-tables-external-tables.md#create-external-file-format). Jelenleg csak a FORMAT_TYPE = parketta és a FORMAT_TYPE = DELIMITEDTEXT külső fájlformátumok támogatottak. A DELIMITEDTEXT formátumra vonatkozó GZip-tömörítés nem támogatott.

*<common_table_expression>*

Egy, a közös tábla kifejezésének (TÁBLAKIFEJEZÉSEK) elnevezésű ideiglenes elnevezett eredményhalmaz megadása. További információ: [common_table_expression (Transact-SQL)](/sql/t-sql/queries/with-common-table-expression-transact-sql?view=azure-sqldw-latest&preserve-view=true).

Válassza ki <select_criteria>

Feltölti az új táblát egy SELECT utasítás eredményeivel. *select_criteria* a SELECT utasítás törzse, amely meghatározza, hogy az új táblába milyen adatok legyenek átmásolva. További információ a SELECT utasításokról: [Select (Transact-SQL)](/sql/t-sql/queries/select-transact-sql?view=azure-sqldw-latest&preserve-view=true).

> [!NOTE]
> A SELECT utasítás ORDER BY záradéka nem támogatott a CETAS.

## <a name="permissions"></a>Engedélyek

Engedéllyel kell rendelkeznie a mappa tartalmának listázásához és az írási hely mappájához a CETAS működéséhez.

## <a name="examples"></a>Példák

Ezek a példák a CETAS használatával mentik el az év és a population_ds adatforrásban található aggregated_data mappában lévő összes összesített sokaságot.

Ez a minta a korábban létrehozott hitelesítő adatokra, adatforrásra és külső fájlformátumra támaszkodik. Tekintse meg a [külső táblákat](develop-tables-external-tables.md) tartalmazó dokumentumot. Ha ugyanazon az adatforráson belül más mappába szeretné menteni a lekérdezési eredményeket, módosítsa a LOCATION argumentumot. 

Ha másik Storage-fiókba kívánja menteni az eredményeket, hozzon létre és használjon egy másik adatforrást DATA_SOURCE argumentumhoz.

> [!NOTE]
> A következő minták a nyilvános Azure Open Storage-fiókot használják. Csak olvasható. A lekérdezések végrehajtásához meg kell adnia azt az adatforrást, amelyhez írási engedéllyel rendelkezik.

```sql
-- use CETAS to export select statement with OPENROWSET result to  storage
CREATE EXTERNAL TABLE population_by_year_state
WITH (
    LOCATION = 'aggregated_data/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)  
AS
SELECT decennialTime, stateName, SUM(population) AS population
FROM
    OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=*/*.parquet',
    FORMAT='PARQUET') AS [r]
GROUP BY decennialTime, stateName
GO

-- you can query the newly created external table
SELECT * FROM population_by_year_state
```

Az alábbi minta egy külső táblát használ a CETAS forrásaként. A korábban létrehozott hitelesítő adatokra, adatforrásra, külső fájlformátumra és külső táblára támaszkodik. Tekintse meg a [külső táblákat](develop-tables-external-tables.md) tartalmazó dokumentumot.

```sql
-- use CETAS with select from external table
CREATE EXTERNAL TABLE population_by_year_state
WITH (
    LOCATION = 'aggregated_data/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)  
AS
SELECT decennialTime, stateName, SUM(population) AS population
FROM census_external_table
GROUP BY decennialTime, stateName
GO

-- you can query the newly created external table
SELECT * FROM population_by_year_state
```

## <a name="supported-data-types"></a>Támogatott adattípusok

A CETAS a következő SQL-adattípusokkal rendelkező eredményhalmaz tárolására használható:

- binary
- varbinary
- char
- varchar
- NCHAR
- nvarchar
- smalldate
- dátum
- dátum/idő
- datetime2
- DateTimeOffset
- time
- tizedes tört
- numerikus
- float
- valós szám
- bigint
- tinyint
- smallint
- int
- bigint
- bit
- pénzt
- túlcsordulási
- uniqueidentifier

> [!NOTE]
> Az 1 MB-nál nagyobb LOBs nem használhatók a CETAS.

## <a name="next-steps"></a>Következő lépések

Próbálja meg Apache Spark lekérdezését [Az Azure szinapszis külső tábláihoz](develop-storage-files-spark-tables.md).
