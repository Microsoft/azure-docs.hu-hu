---
title: Az ETL helyett tervezzete meg az ELT-t
description: Rugalmas adatbetöltési stratégiákat valósíthat meg dedikált SQL-készletekhez a Azure Synapse Analytics.
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
ms.openlocfilehash: 8a8f857dcfdc271a3aaad71f4b9c26d474033383
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566103"
---
# <a name="data-loading-strategies-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Adatbetöltési stratégiák dedikált SQL-készlethez a Azure Synapse Analytics

A hagyományos SMP dedikált SQL-készletek kinyerési, átalakítási és betöltési (ETL) folyamatot használnak az adatok betöltéséhez. Synapse SQL a Azure Synapse Analytics elosztott lekérdezésfeldolgozási architektúrát használ, amely kihasználja a számítási és tárolási erőforrások méretezhetőségét és rugalmasságát.

A kinyerési, betöltési és átalakítási (ELT) folyamat beépített elosztott lekérdezésfeldolgozási képességeket használ, és kiküszöböli az adatátalakításhoz szükséges erőforrásokat a betöltés előtt.

Bár a dedikált SQL-készletek számos betöltési módszert támogatnak, beleértve a népszerű SQL Server-beállításokat, például a [bcp-t](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) és az [SqlBulkCopy](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)API-t, az adatok betöltésének leggyorsabb és leginkább skálázható módja a PolyBase külső táblákon és a [COPY](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)utasításon keresztül történik.

A PolyBase és a COPY utasítás használatával az Azure Blob Storage-ban vagy az Azure Data Lake Store T-SQL nyelven keresztül férhet hozzá a tárolt külső adatokhoz. A betöltés során a legnagyobb rugalmasság érdekében javasoljuk a COPY utasítás használatát.


## <a name="what-is-elt"></a>Mi az az ELT?

A kinyerési, betöltési és átalakítási (ELT) folyamat során a rendszer kinyeri az adatokat a forrásrendszerből, betölti őket egy dedikált SQL-készletbe, majd átalakítja őket.

Az ELT megvalósításának alapvető lépései a következőek:

1. A forrásadatok kinyerése szövegfájlokba.
2. Az adatokat az Azure Blob Storage-ba vagy az Azure Data Lake Store.
3. Készítse elő az adatokat a betöltéshez.
4. Töltse be az adatokat előkészítési táblákba a PolyBase vagy a COPY paranccsal.
5. Az adatok átalakítása.
6. Adatok beszúrása az éles üzemi táblákba.

Betöltési oktatóanyagért lásd: [Adatok betöltése az Azure Blob Storage-ból.](./load-data-from-azure-blob-storage-using-copy.md)

## <a name="1-extract-the-source-data-into-text-files"></a>1. Forrásadatok kinyerása szövegfájlokba

Az adatok forrásrendszerből való kihozása a tárolási helytől függ. A cél az adatok áthelyezése a támogatott tagolt szöveg- vagy CSV-fájlokba.

### <a name="supported-file-formats"></a>Támogatott fájlformátumok

A PolyBase és a COPY utasítással UTF-8 és UTF-16 kódolású, tagolt szöveg- vagy CSV-fájlokból is betölthet adatokat. A tagolt szöveg- vagy CSV-fájlokon kívül betöltődik a Hadoop-fájlformátumok, például az ORC és a Parquet. A PolyBase és a COPY utasítás a Gzip és a Snappy tömörített fájlokból is betölthet adatokat.

A kiterjesztett ASCII, a rögzített szélességű formátum és a beágyazott formátumok, például a WinZip vagy az XML nem támogatottak. Ha az adatokat a SQL Server exportálja, a [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) parancssori eszközzel tagolt szövegfájlokba exportálhatja az adatokat.

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Az adatok tárolása az Azure Blob Storage-ba vagy az Azure Data Lake Store

Az adatok Azure Storage-ba való áthelyezéséhez áthelyezheti az [adatokat](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) az Azure Blob Storage-ba vagy az [Azure Data Lake Store Gen2-be.](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Az adatokat mindkét helyen szövegfájlokban kell tárolni. A PolyBase és a COPY utasítás bármelyik helyről betölthet.

Az adatok Azure Storage-ba való áthelyezéséhez használható eszközök és szolgáltatások:

- [Azure ExpressRoute](../../expressroute/expressroute-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) szolgáltatás javítja a hálózati teljesítményt, a teljesítményt és az előrejelzhetőséget. Az ExpressRoute egy olyan szolgáltatás, amely egy dedikált privát kapcsolaton keresztül átirányítja az adatokat az Azure-ba. Az ExpressRoute-kapcsolatok nem irányítják át az adatokat a nyilvános interneten keresztül. A kapcsolatok nagyobb megbízhatóságot, gyorsabb sebességet, alacsonyabb késést és nagyobb biztonságot nyújtanak, mint a nyilvános interneten keresztüli tipikus kapcsolatok.
- [Az AZCopy segédprogram a](../../storage/common/storage-choose-data-transfer-solution.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) nyilvános interneten keresztül áthelyezi az adatokat az Azure Storage-ba. Ez akkor működik, ha az adatméret kisebb, mint 10 TB. Az AZCopyval való rendszeres terhelések végrehajtásához tesztelje a hálózati sebességet, és ellenőrizze, hogy elfogadható-e.
- [Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) rendelkezik egy átjáróval, amely telepíthető a helyi kiszolgálóra. Ezután létrehozhat egy folyamatot, amely áthelyezi az adatokat a helyi kiszolgálóról az Azure Storage-ba. A dedikált SQL Data Factory készletekkel való használathoz lásd: [Adatok betöltése dedikált SQL-készletekhez.](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

## <a name="3-prepare-the-data-for-loading"></a>3. Az adatok előkészítése a betöltéshez

Előfordulhat, hogy a betöltés előtt elő kell készítenie és meg kell tisztítania a tárfiókban az adatokat. Az adatok előkészítése elvégezhető addig, amíg az adatok a forrásban vannak, amikor szövegfájlokba exportálja az adatokat, vagy ha az adatok már az Azure Storage-ban vannak.  Az adatokkal a folyamat korai szakaszában lehet a legegyszerűbben dolgozni.  

### <a name="define-the-tables"></a>A táblák meghatározása

A COPY utasítás használata során először meg kell határoznia a táblá(k)t, amelybe a betöltés történik a dedikált SQL-készletben.

PolyBase használata esetén betöltés előtt meg kell határoznia külső táblákat a dedikált SQL-készletben. A PolyBase külső táblák használatával határozza meg és fér hozzá az adatokat az Azure Storage-ban. A külső tábla hasonló az adatbázisnézethez. A külső tábla tartalmazza a táblasémát, és a dedikált SQL-készleten kívül tárolt adatokra mutat.

A külső táblák definiálása magában foglalja az adatforrás, a szövegfájlok formátumának és a tábladefiníciók megadását. A T-SQL szintaxisával kapcsolatos referenciacikkek, amelyekre szüksége lesz:

- [KÜLSŐ ADATFORRÁS LÉTREHOZÁSA](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

Parquet-fájlok betöltésekor használja a következő SQL-adattípus-leképezést:

|                         Parquet-típus                         |   Parquet logikai típus (jegyzet)   |  SQL-adattípus   |
| :----------------------------------------------------------: | :-----------------------------------: | :--------------: |
|                           Logikai                            |                                       |       bit        |
|                     BINARY /BYTE_ARRAY                      |                                       |    varbinary (varbinary)     |
|                            Dupla                            |                                       |      float       |
|                            Úszó                             |                                       |       valós szám       |
|                            INT32                             |                                       |       int        |
|                            INT64                             |                                       |      bigint      |
|                            INT96                             |                                       |    datetime2     |
|                     FIXED_LEN_BYTE_ARRAY                     |                                       |      binary      |
|                            Bináris                            |                 UTF8                  |     nvarchar     |
|                            Bináris                            |                Karakterlánc                 |     nvarchar     |
|                            Bináris                            |                 Enum                  |     nvarchar     |
|                            Bináris                            |                 Uuid                  | uniqueidentifier |
|                            Bináris                            |                Decimális                |     tizedes tört      |
|                            Bináris                            |                 JSON                  |  nvarchar(MAX)   |
|                            Bináris                            |                 BSON                  |  varbinary(max)  |
|                     FIXED_LEN_BYTE_ARRAY                     |                Decimális                |     tizedes tört      |
|                          BYTE_ARRAY                          |               Intervallum                |  varchar(max),   |
|                            INT32                             |             INT(8, true)              |     smallint     |
|                            INT32                             |            INT(16, true)            |     smallint     |
|                            INT32                             |             INT(32, true)             |       int        |
|                            INT32                             |            INT(8, false)            |     tinyint      |
|                            INT32                             |            INT(16, false)             |       int        |
|                            INT32                             |           INT(32, false)            |      bigint      |
|                            INT32                             |                 DATE                  |       dátum       |
|                            INT32                             |                Decimális                |     tizedes tört      |
|                            INT32                             |            TIME (MILLIS )             |       time       |
|                            INT64                             |            INT(64, true)            |      bigint      |
|                            INT64                             |           INT(64, false )            |  decimal(20,0)   |
|                            INT64                             |                Decimális                |     tizedes tört      |
|                            INT64                             |         TIME (MILLIS)                 |       time       |
|                            INT64                             | TIMESTAMP (MILLIS)                  |    datetime2     |
| [Összetett típus](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23lists&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=6Luk047sK26ijTzfvKMYc%2FNu%2Fz0AlLCX8lKKTI%2F8B5o%3D&reserved=0) |                 Lista                  |   varchar(max)   |
| [Összetett típus](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23maps&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=FiThqXxjgmZBVRyigHzfh5V7Z%2BPZHjud2IkUUM43I7o%3D&reserved=0) |                  Megjelenítése                  |   varchar(max)   |

>[!IMPORTANT] 
>- A dedikált SQL-készletek jelenleg nem támogatják a MicroS és NANOS pontosságú Parquet-adattípusokat. 
>- A következő hiba jelenhet meg, ha a típusok nem egyezőek a Parquet és az SQL között, vagy ha nem támogatott Parquet-adattípusokkal rendelkezik: **"HdfsBridge::recordReaderFillBuffer – Váratlan** hiba történt a rekordolvasó puffer kitöltése során: ClassCastException: ..."
>- A 0 és 127 tartományon kívüli érték a Parquet- és ORC-fájlformátumok tinyint oszlopába való betöltése nem támogatott.

Példa külső objektumok létrehozására: [Külső táblák létrehozása.](../sql/develop-tables-external-tables.md?tabs=sql-pool)

### <a name="format-text-files"></a>Szövegfájlok formázása

PolyBase használata esetén a definiált külső objektumoknak a szövegfájlok sorait a külső tábla- és fájlformátum-definícióhoz kell igazítania. A szövegfájl egyes sorában lévő adatoknak igazodni kell a tábladefinícióhoz.
A szövegfájlok formázása:

- Ha az adatok nem relációs forrásból származik, sorokat és oszlopokat kell átalakítania. Akár relációs, akár nem relációs forrásból származó adatokról van szó, az adatokat át kell alakítani annak a táblának az oszlopdefinícióihoz igazodva, amelybe az adatokat be fogja tölteni.
- Formázza az adatokat a szövegfájlban, hogy igazodni fog a céltábla oszlopaihoz és adattípusához. A külső szövegfájlokban és a dedikált SQL-készlet táblájában található adattípusok közötti különbségek miatt a rendszer elutasítja a sorokat a betöltés során.
- A szövegfájl mezőinek elválasztása egy lezáróval.  Mindenképpen olyan karaktert vagy karaktersorozatot használjon, amely nem található meg a forrásadatok között. Használja a CREATE [EXTERNAL FILE FORMAT (KÜLSŐ FÁJLFORMÁTUM LÉTREHOZÁSA) használatával megadott lezárót.](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="4-load-the-data-using-polybase-or-the-copy-statement"></a>4. Adatok betöltése a PolyBase vagy a COPY utasítás használatával

Az ajánlott eljárás az adatok átmeneti táblába való betöltése. Az előkészítési táblák lehetővé teszik a hibák az éles táblák zavarása nélküli kezelését. Az előkészítési tábla arra is lehetőséget biztosít, hogy a dedikált SQL-készlet párhuzamos feldolgozási architektúráját használja az adatátalakításokhoz, mielőtt beszúrja az adatokat az éles táblákba.

### <a name="options-for-loading"></a>Betöltési lehetőségek

Az adatok betöltéséhez a következő betöltési lehetőségek bármelyikét használhatja:

- A [COPY utasítás](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) az ajánlott betöltési segédprogram, mivel lehetővé teszi az adatok zökkenőmentes és rugalmas betöltését. A utasítás számos további betöltési képességgel rendelkezik, amit a PolyBase nem biztosít. 
- [A PolyBase és a T-SQL használatához](./load-data-from-azure-blob-storage-using-copy.md) külső adatobjektumokat kell definiálni.
- [A PolyBase és a COPY utasítás Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) egy másik vezénylési eszköz.  Meghatároz egy folyamatot, és ütemezi a feladatokat.
- [A PolyBase és az SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) jól működik, ha a forrásadatok a SQL Server. Az SSIS meghatározza a forrás és a céltábla leképezését, valamint összehangolja a terhelést. Ha már rendelkezik SSIS-csomagokkal, módosíthatja a csomagokat úgy, hogy az az új adattárház célhelyének megfelelően működjön.
- [A PolyBase Azure Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json) adatokat továbbít egy táblából Egy Databricks-adatkeretbe, és/vagy adatokat ír egy Databricks-adatkeretből egy táblába a PolyBase használatával.

### <a name="other-loading-options"></a>Egyéb betöltési lehetőségek

A PolyBase és a COPY utasítás mellett használhatja a [bcp-t](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) vagy az [SqlBulkCopy API-t is.](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) A bcp közvetlenül az adatbázisba tölt be az Azure Blob Storage nélkül, és csak kis terhelések esetén szolgál.

> [!NOTE]
> Ezeknek a beállításoknak a betöltési teljesítménye lassabb, mint a PolyBase és a COPY utasítás.

## <a name="5-transform-the-data"></a>5. Az adatok átalakítása

Amíg az adatok az előkészítési táblában vannak, hajtsa végre a számítási feladathoz szükséges átalakításokat. Ezután helyezze át az adatokat egy éles táblába.

## <a name="6-insert-the-data-into-production-tables"></a>6. Adatok beszúrása éles táblákba

AZ INSERT INTO... A SELECT utasítás áthelyezi az adatokat az előkészítési táblából az állandó táblába.

Az ETL-folyamat tervezése során próbálja meg egy kisebb tesztmintán futtatni a folyamatot. Próbáljon kinyerni 1000 sort a táblából egy fájlba, helyezze át az Azure-ba, majd próbálja meg betölteni egy előkészítési táblába.

## <a name="partner-loading-solutions"></a>Partnerek betöltési megoldásai

Számos partnerünk kínál betöltési megoldásokat. További információért tekintse meg megoldáspartnereink [listáját.](sql-data-warehouse-partner-business-intelligence.md)

## <a name="next-steps"></a>Következő lépések

Útmutatás a betöltéshez: [Útmutató az adatok betöltéséhez](guidance-for-loading-data.md).