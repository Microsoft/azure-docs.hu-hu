---
title: ETL helyett a design ELT
description: Rugalmas betöltési stratégiákat alkalmazhat a dedikált SQL-készletek számára az Azure szinapszis Analyticsben.
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
ms.openlocfilehash: 04bd4767445f9378aedb303e63bf463f44e40034
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104602196"
---
# <a name="data-loading-strategies-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Betöltési stratégiák a dedikált SQL-készlethez az Azure szinapszis Analytics szolgáltatásban

A hagyományos SMP dedikált SQL-készletek kinyerési, átalakítási és betöltési (ETL) folyamatot használnak az adatok betöltéséhez. A szinapszis SQL az Azure szinapszis Analyticsen belül elosztott lekérdezés-feldolgozási architektúrát használ, amely kihasználja a számítási és tárolási erőforrások méretezhetőségét és rugalmasságát.

A kinyerési, betöltési és átalakítási (ELT) folyamat a beépített elosztott lekérdezés-feldolgozási képességeket használja, és kiküszöböli az adatok átalakításához szükséges erőforrásokat a betöltés előtt.

Míg a dedikált SQL-készletek számos betöltési módszert támogatnak, beleértve a népszerű SQL Server lehetőségeket, például a [BCP](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) -t és a [SqlBulkCopy API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)-t, az adatok betöltésének leggyorsabb és leginkább méretezhető módja a albase külső táblákon és a [copy utasításon](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)keresztül.

A Base és a COPY utasítás használatával az Azure Blob Storage-ban vagy Azure Data Lake Storeban tárolt külső adatokhoz a T-SQL nyelv használatával férhet hozzá. A betöltés legrugalmasabban a MÁSOLÁSi utasítás használata javasolt.


## <a name="what-is-elt"></a>Mi az a ELT?

A kinyerési, betöltési és átalakítási (ELT) folyamat során a rendszer kinyeri az adatforrást egy dedikált SQL-készletbe, majd átalakítja azokat.

A ELT megvalósításának alapvető lépései a következők:

1. A forrásadatok kinyerése szövegfájlokba.
2. Az Azure Blob Storage-ba vagy Azure Data Lake Storeba helyezheti az adatterületet.
3. Az adatgyűjtés előkészítése a betöltéshez.
4. Töltse be az adatokba az átmeneti táblákat a Base vagy a COPY paranccsal.
5. Alakítsa át az adathalmazt.
6. Adatok beszúrása az éles üzemi táblákba.

Betöltési oktatóanyag: [adatok betöltése az Azure Blob Storage-ból](./load-data-from-azure-blob-storage-using-copy.md).

## <a name="1-extract-the-source-data-into-text-files"></a>1. a forrásadatok kibontása szövegfájlba

A forrásrendszer adatokból való beolvasása a tárterület helyétől függ. A cél az, hogy az adatátvitelt támogatott tagolt szöveges vagy CSV-fájlokba helyezze át.

### <a name="supported-file-formats"></a>Támogatott fájlformátumok

A Base és a COPY utasítás használatával az adatok az UTF-8 és az UTF-16 kódolású tagolt szövegből vagy CSV-fájlokból tölthetők be. A tagolt szöveg-vagy CSV-fájlok mellett a Hadoop-fájlformátumokból, például az ORKből és a Parkettaből is betöltődik. A Base és a COPY utasítás a gzip és a Snappy tömörített fájlok adatait is képes betölteni.

A bővített ASCII, a rögzített szélességű formátum és a beágyazott formátumok, például a WinZip vagy az XML nem támogatottak. Ha SQL Serverból exportál, a [BCP parancssori eszköz](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) használatával tagolt szövegfájlba exportálhatja az adatok mennyiségét.

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. az adatgyűjtés az Azure Blob Storage-ba vagy Azure Data Lake Store

Az Azure Storage-ban tárolt adatterületek áthelyezéséhez áthelyezheti azt az [Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) -ba vagy [Azure Data Lake Store Gen2](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Mindkét helyen az adatfájlokat szövegfájlokban kell tárolni. A Base és a COPY utasítás bármelyik helyről tölthető be.

Az Azure Storage-ba történő adatáthelyezéshez használható eszközök és szolgáltatások:

- Az [Azure ExpressRoute](../../expressroute/expressroute-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) szolgáltatás javítja a hálózat teljesítményét, a teljesítményt és a kiszámíthatóságot. A ExpressRoute egy olyan szolgáltatás, amely az Azure-hoz való dedikált privát kapcsolatban továbbítja az adatait. A ExpressRoute-kapcsolatok nem a nyilvános interneten keresztül irányítják az adattovábbítást. A kapcsolatok nagyobb megbízhatóságot, gyorsabb sebességet, kisebb késést és nagyobb biztonságot biztosítanak, mint a szokásos kapcsolatok a nyilvános interneten.
- A [AZCopy segédprogram](../../storage/common/storage-choose-data-transfer-solution.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) az Azure Storage-ba helyezi át az adatátvitelt a nyilvános interneten keresztül. Ez akkor működik, ha az adatméretek 10 TB-nál kisebbek. A AZCopy rendszeres betöltéséhez tesztelje a hálózati sebességet, és ellenőrizze, hogy elfogadható-e.
- [Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) olyan átjáróval rendelkezik, amelyet telepíthet a helyi kiszolgálóra. Ezután létrehozhat egy folyamatot, amely a helyi kiszolgálóról az Azure Storage-ba helyezi át az adatok áthelyezését. Ha a Data Factory dedikált SQL-készletekkel szeretné használni, tekintse meg a [DEDIKÁLT SQL-készletek adatainak betöltése](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)című témakört.

## <a name="3-prepare-the-data-for-loading"></a>3. az adatgyűjtés előkészítése a betöltéshez

Előfordulhat, hogy a betöltés előtt elő kell készítenie és meg kell tisztítania a Storage-fiókban tárolt adatait. Az adatelőkészítés elvégezhető, miközben az adatai a forrásban vannak, miközben az adatait szövegfájlba exportálja, vagy az Azure Storage-ban tárolt adatai.  A lehető leghamarabb dolgozhat az adatkezelési folyamatokkal.  

### <a name="define-the-tables"></a>A táblák megadása

A COPY utasítás használatakor először a dedikált SQL-készletbe betöltött táblázat (oka) t kell meghatároznia.

Ha a Base-t használja, a betöltés előtt meg kell határoznia a külső táblákat a dedikált SQL-készletben. A Base külső táblákat használ az Azure Storage-ban tárolt adatok definiálásához és eléréséhez. Egy külső tábla hasonlít egy adatbázis-nézethez. A külső tábla tartalmazza a tábla sémáját, és a dedikált SQL-készleten kívül tárolt adatra mutat.

A külső táblák meghatározása magában foglalja az adatforrás megadását, a szövegfájlok formátumát és a tábla definícióit. A T-SQL szintaxissal kapcsolatos, a szükséges cikkek a következők:

- [KÜLSŐ ADATFORRÁS LÉTREHOZÁSA](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [KÜLSŐ TÁBLA LÉTREHOZÁSA](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

A következő SQL adattípus-leképezés használata a parketta-fájlok betöltésekor:

|                         Parketta típusa                         |   Parketta logikai típusa (jegyzet)   |  SQL-adattípus   |
| :----------------------------------------------------------: | :-----------------------------------: | :--------------: |
|                           LOGIKAI                            |                                       |       bit        |
|                     BINÁRIS/BYTE_ARRAY                      |                                       |    varbinary     |
|                            DUPLÁN                            |                                       |      float       |
|                            FLOAT                             |                                       |       valós szám       |
|                            INT32                             |                                       |       int        |
|                            INT64                             |                                       |      bigint      |
|                            INT96                             |                                       |    datetime2     |
|                     FIXED_LEN_BYTE_ARRAY                     |                                       |      binary      |
|                            BINÁRIS                            |                 UTF8                  |     nvarchar     |
|                            BINÁRIS                            |                KARAKTERLÁNC                 |     nvarchar     |
|                            BINÁRIS                            |                 ENUM                  |     nvarchar     |
|                            BINÁRIS                            |                 UUID                  | uniqueidentifier |
|                            BINÁRIS                            |                DECIMÁLIS                |     tizedes tört      |
|                            BINÁRIS                            |                 JSON                  |  nvarchar(MAX)   |
|                            BINÁRIS                            |                 BSON                  |  varbinary (max.)  |
|                     FIXED_LEN_BYTE_ARRAY                     |                DECIMÁLIS                |     tizedes tört      |
|                          BYTE_ARRAY                          |               IDŐKÖZ                |  varchar (max),   |
|                            INT32                             |             INT (8, igaz)              |     smallint     |
|                            INT32                             |            INT (16, igaz)            |     smallint     |
|                            INT32                             |             INT (32, true)             |       int        |
|                            INT32                             |            INT (8, hamis)            |     tinyint      |
|                            INT32                             |            INT (16, hamis)             |       int        |
|                            INT32                             |           INT (32, hamis)            |      bigint      |
|                            INT32                             |                 DATE                  |       dátum       |
|                            INT32                             |                DECIMÁLIS                |     tizedes tört      |
|                            INT32                             |            IDŐ (MILLIS)             |       time       |
|                            INT64                             |            INT (64, true)            |      bigint      |
|                            INT64                             |           INT (64, hamis)            |  decimális (20, 0)   |
|                            INT64                             |                DECIMÁLIS                |     tizedes tört      |
|                            INT64                             |         IDŐ (MILLIS)                 |       time       |
|                            INT64                             | IDŐBÉLYEG (MILLIS)                  |    datetime2     |
| [Összetett típus](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23lists&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=6Luk047sK26ijTzfvKMYc%2FNu%2Fz0AlLCX8lKKTI%2F8B5o%3D&reserved=0) |                 LISTÁJÁT                  |   varchar(max)   |
| [Összetett típus](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23maps&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=FiThqXxjgmZBVRyigHzfh5V7Z%2BPZHjud2IkUUM43I7o%3D&reserved=0) |                  Térkép                  |   varchar(max)   |

>[!IMPORTANT] 
>- Az SQL dedikált készletek jelenleg nem támogatják a MICROs és a NANOs pontosságú parketta-adattípusokat. 
>- Előfordulhat, hogy a következő hiba fordul elő, ha a típusok nem egyeznek a parketta és az SQL között, vagy ha nem támogatott a parketta adattípusai: **"HdfsBridge:: recordReaderFillBuffer – váratlan hiba történt a rekord olvasó pufferének kitöltése során: ClassCastException:..."**
>- A 0-127-tartományon kívüli értéknek a parketta és az ork fájlformátum tinyint oszlopba való betöltése nem támogatott.

Külső objektumok létrehozásával kapcsolatos példát a [külső táblák létrehozása](../sql/develop-tables-external-tables.md?tabs=sql-pool)című témakörben talál.

### <a name="format-text-files"></a>Szövegfájlok formázása

Ha a Base-t használja, a külső objektumoknak meg kell igazítaniuk a szövegfájl sorait a külső tábla és a fájlformátum definíciója szerint. A szövegfájl egyes soraiban lévő összes értéknek a tábla definícióját kell igazítania.
A szövegfájlok formázása:

- Ha az adatok nem összehasonlítható forrásból származnak, át kell alakítania azokat sorokra és oszlopokra. Függetlenül attól, hogy az adatok egy rokon vagy nem rokon forrásból származnak-e, az adatoknak át kell alakítani a táblázat azon oszlopainak definícióját, amelybe az adatok betöltését tervezi.
- Formázza a szövegfájlban lévő adattípusokat, hogy a cél táblában lévő oszlopokkal és adattípusokkal igazodjanak. A külső szövegfájlokban lévő adattípusok és a dedikált SQL Pool tábla közötti helytelen igazítás a sorok elutasítását okozza a terhelés során.
- Külön mezők a szövegfájlban egy lezáró fájllal.  Ügyeljen arra, hogy olyan karaktert vagy karaktert használjon, amely nem található meg a forrásadatok között. Használja a [külső fájlformátum létrehozásakor](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)megadott lezárót.

## <a name="4-load-the-data-using-polybase-or-the-copy-statement"></a>4. az adatok betöltése a Base vagy a COPY utasítás használatával

Az ajánlott eljárás az, ha az adatgyűjtést egy előkészítési táblába tölti be. Az előkészítési táblázatok lehetővé teszik a hibák kezelését az éles táblákkal való beavatkozás nélkül. Az előkészítési táblázat lehetővé teszi, hogy a dedikált SQL Pool párhuzamos feldolgozási architektúrát használja az adatátalakításokhoz, mielőtt beilleszti az adatfeldolgozási táblákba.

### <a name="options-for-loading"></a>Betöltési beállítások

Az betöltéshez a következő betöltési lehetőségek bármelyikét használhatja:

- A [másolási utasítás](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) az ajánlott betöltési segédprogram, amely lehetővé teszi az adatok zökkenőmentes és rugalmas betöltését. Az utasítás számos további betöltési lehetőséggel rendelkezik, amelyeket a Base nem biztosít. 
- A [T-SQL-T tartalmazó Albasehoz](./load-data-from-azure-blob-storage-using-copy.md) külső adatobjektumokat kell meghatároznia.
- [A Base és a copy utasítás Azure Data Factory (ADF) egy másik összehangoló](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) eszköz.  Meghatározza a folyamatokat és az ütemezett feladatokat.
- A SSIS jól működik [,](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) ha a forrásadatok SQL Serverban működnek. A SSIS meghatározza a forrást a céltábla leképezéséhez, és összehangolja a terhelést is. Ha már rendelkezik SSIS-csomagokkal, a csomagokat módosíthatja úgy, hogy az új adattárház-célhelyen működjenek.
- A [Azure Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json) a tábla adatait egy Databricks-dataframe továbbítja, és/vagy egy Databricks-dataframe származó adatot ír be egy tábla használatával.

### <a name="other-loading-options"></a>Egyéb betöltési beállítások

A Base és a COPY utasítás mellett a [BCP](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) vagy a [SqlBulkCopy API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)is használható. a BCP az Azure Blob Storage szolgáltatáson keresztül közvetlenül az adatbázisba töltődik be, és csak kis terhelések esetén használható.

> [!NOTE]
> Ezen beállítások betöltési teljesítménye lassabb, mint a "Base" és a "COPY" utasítás.

## <a name="5-transform-the-data"></a>5. az adatátalakítás

Amíg az adatok az előkészítési táblában vannak, hajtsa végre a számítási feladatok által igényelt átalakításokat. Ezután helyezze át az adatátvitelt egy éles táblába.

## <a name="6-insert-the-data-into-production-tables"></a>6. az adattábla beillesztése a termelési táblákba

A Beszúrás a (...) A SELECT utasítás az átmeneti tábla adatait az állandó táblába helyezi át.

Az ETL-folyamat tervezésekor próbálja meg futtatni a folyamatot egy kis tesztelési mintán. Próbálja meg kibontani a 1000 sort a táblából egy fájlba, helyezze át az Azure-ba, majd próbálja meg betölteni egy előkészítési táblába.

## <a name="partner-loading-solutions"></a>Partneri betöltési megoldások

Számos partnerünk betöltési megoldásokkal rendelkezik. További információért tekintse meg a [megoldás partnereink](sql-data-warehouse-partner-business-intelligence.md)listáját.

## <a name="next-steps"></a>Következő lépések

Útmutatás a betöltéshez: [Útmutató az adatok betöltéséhez](guidance-for-loading-data.md).