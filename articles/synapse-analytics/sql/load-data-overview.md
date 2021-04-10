---
title: Alapszintű adatok betöltésére szolgáló stratégia kialakítása dedikált SQL-készlethez
description: ETL helyett egy kinyerési, betöltési és átalakítási (ELT) folyamatot kell megterveznie az adatok dedikált SQL-sel való betöltéséhez.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: gaursa
ms.reviewer: igorstan
ms.openlocfilehash: 850c03d5cbda8fb11bbc804f2cd2c848a5a2411f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104589499"
---
# <a name="design-a-polybase-data-loading-strategy-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Alapszintű betöltési stratégia kialakítása dedikált SQL-készlethez az Azure szinapszis Analyticsben

A hagyományos SMP-adattárházak kinyerési, átalakítási és betöltési (ETL) folyamatot használnak az adatok betöltéséhez. Az Azure SQL Pool egy nagymértékben párhuzamos feldolgozási (MPP) architektúra, amely kihasználja a számítási és tárolási erőforrások méretezhetőségét és rugalmasságát. A kinyerési, betöltési és átalakítási (ELT) folyamat használatával kihasználhatja a beépített elosztott lekérdezés-feldolgozási képességek előnyeit, és eltávolíthatja az adatoknak a betöltés előtt való átalakításához szükséges erőforrásokat.

Míg az SQL-készlet számos betöltési módszert támogat, többek között a nem alapszintű beállításokat, például a BCP-t és az SQL bulkcopy objektum céloszlopa API-t, a leggyorsabban és leginkább méretezhető módon a betöltési idő a  A Base egy olyan technológia, amely az Azure Blob Storage-ban vagy Azure Data Lake Storeban tárolt külső adatokhoz fér hozzá a T-SQL nyelv használatával.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="extract-load-and-transform-elt"></a>Kinyerési, betöltési és átalakítási (ELT)

A kinyerési, betöltési és átalakítási (ELT) folyamat során a rendszer kinyeri az adatforrásrendszer adatait, betölti azokat egy adattárházba, majd átalakítja azokat.

A következő lépésekkel végezhető el az alapszintű ELT a dedikált SQL-készlet megvalósításához:

1. A forrásadatok kinyerése szövegfájlokba.
2. Az Azure Blob Storage-ba vagy Azure Data Lake Storeba helyezheti az adatterületet.
3. Az adatgyűjtés előkészítése a betöltéshez.
4. Az adatok betöltése a dedikált SQL-készlet előkészítési tábláiba a Base használatával.
5. Alakítsa át az adathalmazt.
6. Adatok beszúrása az éles üzemi táblákba.

A betöltési oktatóanyagért lásd: az [Azure Blob Storage-ból az Azure szinapszis analyticsbe való adatok betöltésének használata](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).

További információ: [minták betöltése blog](/archive/blogs/sqlcat/azure-sql-data-warehouse-loading-patterns-and-strategies).

## <a name="1-extract-the-source-data-into-text-files"></a>1. a forrásadatok kibontása szövegfájlba

A forrásrendszer adatokból való beolvasása a tárterület helyétől függ.  A cél az, hogy az adatok a következőre legyenek áthelyezve: Base támogatott tagolt szövegfájlok.

### <a name="polybase-external-file-formats"></a>Alapszintű külső fájlformátumok

A kiinduló adatok az UTF-8 és az UTF-16 kódolású, tagolt szövegfájlokból is betöltődik. A tagolt szövegfájlok mellett az RC-fájlból, az ORKből és a Parkettaből is betöltődik a Hadoop fájlformátum. A rendszer a gzip és a Snappy tömörített fájlok adatait is képes betölteni. A Base jelenleg nem támogatja a bővített ASCII, a rögzített szélességű formátumot és a beágyazott formátumokat, például a WinZip, a JSON és az XML formátumot.

Ha SQL Serverból exportál, a [BCP parancssori eszköz](/sql/tools/bcp-utility?view=azure-sqldw-latest&preserve-view=true) használatával tagolt szövegfájlba exportálhatja az adatok mennyiségét. Az Azure szinapszis Analytics adattípusának leképezése a következő:

| **Parketta adattípusa** |                      **SQL-adattípus**                       |
| :-------------------: | :----------------------------------------------------------: |
|        tinyint        |                           tinyint                            |
|       smallint        |                           smallint                           |
|          int          |                             int                              |
|        bigint         |                            bigint                            |
|        boolean        |                             bit                              |
|        double         |                            float                             |
|         float         |                             valós szám                             |
|        double         |                            pénzt                             |
|        double         |                          túlcsordulási                          |
|        sztring         |                            NCHAR                             |
|        sztring         |                           nvarchar                           |
|        sztring         |                             char                             |
|        sztring         |                           varchar                            |
|        binary         |                            binary                            |
|        binary         |                          varbinary                           |
|       időbélyeg       |                             dátum                             |
|       időbélyeg       |                        idő adattípusúra                         |
|       időbélyeg       |                          datetime2                           |
|       időbélyeg       |                           dátum/idő                           |
|       időbélyeg       |                             time                             |
|       dátum            |                             dátum                             |
|        tizedes tört        |                            tizedes tört                           |

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. az adatgyűjtés az Azure Blob Storage-ba vagy Azure Data Lake Store

Az Azure Storage-ban tárolt adatterületek áthelyezéséhez áthelyezheti azt az [Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md) -ba vagy [Azure Data Lake Storeba](../../data-lake-store/data-lake-store-overview.md). Mindkét helyen az adatfájlokat szövegfájlokban kell tárolni. A Base bármelyik helyről betölthető.

Az Azure Storage-ba történő adatáthelyezéshez használható eszközök és szolgáltatások:

- Az [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) szolgáltatás javítja a hálózat teljesítményét, a teljesítményt és a kiszámíthatóságot. A ExpressRoute egy olyan szolgáltatás, amely az Azure-hoz való dedikált privát kapcsolatban továbbítja az adatait. A ExpressRoute-kapcsolatok nem a nyilvános interneten keresztül irányítják az adattovábbítást. A kapcsolatok nagyobb megbízhatóságot, gyorsabb sebességet, kisebb késést és nagyobb biztonságot biztosítanak, mint a szokásos kapcsolatok a nyilvános interneten.
- A [AZCopy segédprogram](../../storage/common/storage-use-azcopy-v10.md) az Azure Storage-ba helyezi át az adatátvitelt a nyilvános interneten keresztül. Ez akkor működik, ha az adatméretek 10 TB-nál kisebbek. A AZCopy rendszeres betöltéséhez tesztelje a hálózati sebességet, és ellenőrizze, hogy elfogadható-e.
- [Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) olyan átjáróval rendelkezik, amelyet telepíthet a helyi kiszolgálóra. Ezután létrehozhat egy folyamatot, amely a helyi kiszolgálóról az Azure Storage-ba helyezi át az adatok áthelyezését. Ha a Data Factory dedikált SQL-készlettel szeretné használni, tekintse meg az [adat betöltése DEDIKÁLT SQL-készletbe](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)című témakört.

## <a name="3-prepare-the-data-for-loading"></a>3. az adatgyűjtés előkészítése a betöltéshez

Előfordulhat, hogy elő kell készítenie és törölnie kell a Storage-fiókban tárolt adatait, mielőtt a dedikált SQL-készletbe betölti azt. Az adatelőkészítés elvégezhető, miközben az adatai a forrásban vannak, miközben az adatait szövegfájlba exportálja, vagy az Azure Storage-ban tárolt adatai.  A lehető leghamarabb dolgozhat az adatkezelési folyamatokkal.  

### <a name="define-external-tables"></a>Külső táblák definiálása

Az adatbetöltése előtt meg kell határoznia a külső táblákat az adattárházban. A Base külső táblákat használ az Azure Storage-ban tárolt adatok definiálásához és eléréséhez. Egy külső tábla hasonlít egy adatbázis-nézethez. A külső tábla tartalmazza a tábla sémáját, és az adatraktáron kívül tárolt adatértékekre mutat.

A külső táblák meghatározása magában foglalja az adatforrás megadását, a szövegfájlok formátumát és a tábla definícióit. A következők a T-SQL szintaxissal kapcsolatos témakörök, amelyekre szüksége lesz:

- [KÜLSŐ ADATFORRÁS LÉTREHOZÁSA](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [KÜLSŐ TÁBLA LÉTREHOZÁSA](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest&preserve-view=true)

### <a name="format-text-files"></a>Szövegfájlok formázása

A külső objektumok meghatározása után a szövegfájl sorait a külső tábla és a fájlformátum definíciója szerint kell összehangolni. A szövegfájl egyes soraiban lévő összes értéknek a tábla definícióját kell igazítania.
A szövegfájlok formázása:

- Ha az adatok nem összehasonlítható forrásból származnak, át kell alakítania azokat sorokra és oszlopokra. Függetlenül attól, hogy az adatok egy rokon vagy nem rokon forrásból származnak-e, az adatoknak át kell alakítani a táblázat azon oszlopainak definícióját, amelybe az adatok betöltését tervezi.
- Formázza a szövegfájlban található adattípusokat az oszlopokhoz és az adattípusokhoz való igazításhoz az SQL-készlet célhelye táblában. A külső szövegfájlokban lévő adattípusok és az adatraktár tábla közötti helytelen igazítás a sorok elutasítását okozza a terhelés során.
- Külön mezők a szövegfájlban egy lezáró fájllal.  Ügyeljen arra, hogy olyan karaktert vagy karaktert használjon, amely nem található a forrásadatok között. Használja a [külső fájlformátum létrehozásakor](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true)megadott lezárót.

## <a name="4-load-the-data-into-dedicated-sql-pool-staging-tables-using-polybase"></a>4. az adatok betöltése a dedikált SQL-készlet előkészítési tábláiba a Base használatával

Az ajánlott eljárás az, ha az adatgyűjtést egy előkészítési táblába tölti be. Az előkészítési táblázatok lehetővé teszik a hibák kezelését az éles táblákkal való beavatkozás nélkül. Az előkészítési táblázat lehetővé teszi az SQL Pool beépített elosztott lekérdezés-feldolgozási funkcióinak használatát az adatátalakításokhoz, mielőtt az adatfeldolgozási táblákba beszúrja azokat.

### <a name="options-for-loading-with-polybase"></a>A-alapú betöltés lehetőségei

Az adatok alapszintű betöltéséhez használhatja a következő betöltési lehetőségeket:

- A [T-SQL-](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) sel jól használható, ha az adatok Azure Blob Storage-ban vagy Azure Data Lake Storeban vannak. A leghatékonyabban szabályozhatja a betöltési folyamatot, de külső adatobjektumokat is meg kell határoznia. A többi módszer ezeket az objektumokat a háttérben úgy definiálja, ahogy a forrástábla táblázatokra van leképezve.  A T-SQL-terhelések összehangolása érdekében Azure Data Factory, SSIS vagy Azure functions-T használhat.
- A SSIS jól működik [,](/sql/integration-services/load-data-to-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) ha a forrásadatok SQL Serverban működnek. A SSIS meghatározza a forrást a céltábla leképezéséhez, és összehangolja a terhelést is. Ha már rendelkezik SSIS-csomagokkal, a csomagokat módosíthatja úgy, hogy az új adattárház-célhelyen működjenek.
- [A Azure Data Factory (ADF) használatával egy másik összehangoló](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) eszköz van.  Meghatározza a folyamatokat és az ütemezett feladatokat.
- [A Azure Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) az Azure szinapszis Analytics-táblázatból származó adatok átvitele egy Databricks-dataframe, és/vagy egy Databricks-dataframe származó adatok beírása egy Azure szinapszis Analytics-táblába a Base használatával.

### <a name="non-polybase-loading-options"></a>Nem albase betöltési beállítások

Ha az adatok nem kompatibilisek a SQLBulkCopy, használhatja a [BCP](/sql/tools/bcp-utility?view=azure-sqldw-latest&preserve-view=true) -t vagy a [API](/dotnet/api/system.data.sqlclient.sqlbulkcopy)-t. a BCP közvetlenül a dedikált SQL-készletbe töltődik be anélkül, hogy az Azure Blob Storage szolgáltatáson keresztül kellene eljutnia, és kizárólag kis terhelések esetén. Vegye figyelembe, hogy ezeknek a beállításoknak a betöltési teljesítménye jóval lassabb, mint a kiinduló.

## <a name="5-transform-the-data"></a>5. az adatátalakítás

Amíg az adatok az előkészítési táblában vannak, hajtsa végre a számítási feladatok által igényelt átalakításokat. Ezután helyezze át az adatátvitelt egy éles táblába.

## <a name="6-insert-the-data-into-production-tables"></a>6. az adattábla beillesztése a termelési táblákba

A Beszúrás a (...) A SELECT utasítás az átmeneti tábla adatait az állandó táblába helyezi át.

Az ETL-folyamat tervezésekor próbálja meg futtatni a folyamatot egy kis tesztelési mintán. Próbálja meg kibontani a 1000 sort a táblából egy fájlba, helyezze át az Azure-ba, majd próbálja meg betölteni egy előkészítési táblába.

## <a name="partner-loading-solutions"></a>Partneri betöltési megoldások

Számos partnerünk betöltési megoldásokkal rendelkezik. További információért tekintse meg a [megoldás partnereink](../sql-data-warehouse/sql-data-warehouse-partner-business-intelligence.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)listáját.

## <a name="next-steps"></a>Következő lépések

Útmutatást a [betöltési útmutatóban](data-loading-best-practices.md)talál.