---
title: PolyBase-adatbetöltési stratégia tervezése dedikált SQL-készlethez
description: ETL helyett tervezhet kinyerési, betöltési és átalakítási (ELT) folyamatot az adatok dedikált SQL-sel való betöltéséhez.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.openlocfilehash: 518843e688da7f940b36e77aee2667b4984ea5a3
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567351"
---
# <a name="design-a-polybase-data-loading-strategy-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>PolyBase-adatbetöltési stratégia tervezése dedikált SQL-készlethez a Azure Synapse Analytics

A hagyományos SMP-adattárházak kinyerési, átalakítási és betöltési (ETL) folyamatot használnak az adatok betöltéséhez. Azure SQL egy nagymértékben párhuzamos feldolgozási (MPP) architektúra, amely kihasználja a számítási és tárolási erőforrások méretezhetőségét és rugalmasságát. A kinyerési, betöltési és átalakítási (ELT) folyamatokkal kihasználhatja a beépített elosztott lekérdezésfeldolgozási képességeket, és kiküszöbölheti az adatok betöltés előtti átalakításához szükséges erőforrásokat.

Bár az SQL-készlet számos betöltési módszert támogat, beleértve a nem PolyBase-lehetőségeket, például a BCP-t és az SQL BulkCopy API-t, a polyBase a leggyorsabb és leginkább skálázható mód a betöltési dátumok betöltésére.  A PolyBase egy olyan technológia, amely a T-SQL nyelvvel fér hozzá az Azure Blob Storage-ban vagy az Azure-Data Lake Store tárolt külső adatokhoz.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="extract-load-and-transform-elt"></a>Kinyerési, betöltési és átalakítási (ELT)

A kinyerési, betöltési és átalakítási (ELT) folyamat során a rendszer kinyeri az adatokat a forrásrendszerből, betölti az adattárházba, majd átalakítja őket.

A PolyBase ELT dedikált SQL-készlethez való megvalósításának alapvető lépései:

1. A forrásadatok kinyerése szövegfájlokba.
2. Az adatokat az Azure Blob Storage-ba vagy az Azure Data Lake Store.
3. Készítse elő az adatokat a betöltéshez.
4. Töltse be az adatokat a dedikált SQL-készlet előkészítési tábláiba a PolyBase használatával.
5. Az adatok átalakítása.
6. Adatok beszúrása az éles üzemi táblákba.

Betöltési oktatóanyagért lásd: Adatok betöltése a PolyBase használatával az Azure Blob Storage-ból a [Azure Synapse Analytics.](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)

További információ: [Betöltési minták blog.](/archive/blogs/sqlcat/azure-sql-data-warehouse-loading-patterns-and-strategies)

## <a name="1-extract-the-source-data-into-text-files"></a>1. A forrásadatok kinyerása szövegfájlokba

Az adatok forrásrendszerből való kihozása a tárolási helytől függ.  A cél az adatok áthelyezése a PolyBase által támogatott, tagolt szövegfájlokba.

### <a name="polybase-external-file-formats"></a>PolyBase külső fájlformátumok

A PolyBase UTF-8 és UTF-16 kódolású, tagolt szövegfájlokból tölt be adatokat. A tagolt szövegfájlok mellett betöltődik a Hadoop RC File, ORC és Parquet hadoop-fájlformátumaiból. A PolyBase a Gzip- és a Snappy-tömörített fájlokból is képes adatokat betölteni. A PolyBase jelenleg nem támogatja a kiterjesztett ASCII- és rögzített szélességű formátumokat, valamint az olyan beágyazott formátumokat, mint a WinZip, a JSON és az XML.

Ha egy alkalmazásból SQL Server, a [bcp](/sql/tools/bcp-utility?view=azure-sqldw-latest&preserve-view=true) parancssori eszközzel exportálhatja az adatokat tagolt szövegfájlokba. Az adattípus-Azure Synapse Analytics a Parquet a következő:

| **Parquet adattípus** |                      **SQL-adattípus**                       |
| :-------------------: | :----------------------------------------------------------: |
|        tinyint        |                           tinyint                            |
|       smallint        |                           smallint                           |
|          int          |                             int                              |
|        bigint         |                            bigint                            |
|        boolean        |                             bit                              |
|        double         |                            float                             |
|         float         |                             valós szám                             |
|        double         |                            Pénzt                             |
|        double         |                          smallmoney                          |
|        sztring         |                            nchar                             |
|        sztring         |                           nvarchar                           |
|        sztring         |                             Char                             |
|        sztring         |                           varchar                            |
|        binary         |                            binary                            |
|        binary         |                          varbinary                           |
|       időbélyeg       |                             dátum                             |
|       időbélyeg       |                        smalldatetime                         |
|       időbélyeg       |                          datetime2                           |
|       időbélyeg       |                           dátum/idő                           |
|       időbélyeg       |                             time                             |
|       dátum            |                             dátum                             |
|        tizedes tört        |                            tizedes tört                           |

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Az adatok tárolása az Azure Blob Storage-ba vagy az Azure Data Lake Store

Az adatok Azure Storage-ba való áthelyezéséhez áthelyezheti az [adatokat](../../storage/blobs/storage-blobs-introduction.md) az Azure Blob Storage-ba vagy az [Azure Data Lake Store.](../../data-lake-store/data-lake-store-overview.md) Az adatokat mindkét helyen szövegfájlokban kell tárolni. A PolyBase bármelyik helyről betölthet.

Az adatok Azure Storage-ba való áthelyezéséhez használható eszközök és szolgáltatások:

- [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) szolgáltatás javítja a hálózati teljesítményt, a teljesítményt és a kiszámíthatóságot. Az ExpressRoute egy olyan szolgáltatás, amely egy dedikált privát kapcsolaton keresztül átirányítja az adatokat az Azure-ba. Az ExpressRoute-kapcsolatok nem irányítják át az adatokat a nyilvános interneten keresztül. A kapcsolatok megbízhatóbbak, gyorsabbak, alacsonyabb késést és nagyobb biztonságot nyújtanak, mint a nyilvános interneten keresztüli tipikus kapcsolatok.
- [Az AZCopy segédprogram a](../../storage/common/storage-use-azcopy-v10.md) nyilvános interneten keresztül áthelyezi az adatokat az Azure Storage-ba. Ez akkor működik, ha az adatméret kisebb, mint 10 TB. Az AZCopyval való rendszeres terhelés végrehajtásához tesztelje a hálózati sebességet, és ellenőrizze, hogy elfogadható-e.
- [Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) rendelkezik egy átjáróval, amely telepíthető a helyi kiszolgálón. Ezután létrehozhat egy folyamatot, amely áthelyezi az adatokat a helyi kiszolgálóról az Azure Storage-ba. A dedikált SQL Data Factory készletekkel való használathoz lásd: [Adatok betöltése dedikált SQL-készletbe.](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="3-prepare-the-data-for-loading"></a>3. Az adatok előkészítése a betöltéshez

Előfordulhat, hogy elő kell készítenie és meg kell tisztítania a tárfiókban található adatokat, mielőtt betölti őket egy dedikált SQL-készletbe. Az adatok előkészítése elvégezhető addig, amíg az adatok a forrásban vannak, amikor szövegfájlokba exportálja az adatokat, vagy ha az adatok már az Azure Storage-ban vannak.  Az adatokkal a folyamat korai szakaszában lehet a legegyszerűbben dolgozni.  

### <a name="define-external-tables"></a>Külső táblák definiálás

Az adatok betöltése előtt meg kell határoznia külső táblákat az adattárházban. A PolyBase külső táblák használatával határozza meg és fér hozzá az Azure Storage-ban lévő adatokat. A külső tábla hasonló az adatbázisnézethez. A külső tábla tartalmazza a táblasémát, és az adattárházon kívül tárolt adatokra mutat.

A külső táblák definiálása magában foglalja az adatforrás, a szövegfájlok formátumának és a tábladefiníciók megadását. Az alábbiak a T-SQL-szintaxissal kapcsolatos témakörök, amelyekre szüksége lesz:

- [KÜLSŐ ADATFORRÁS LÉTREHOZÁSA](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest&preserve-view=true)

### <a name="format-text-files"></a>Szövegfájlok formázása

A külső objektumok definiálása után a szövegfájlok sorait a külső tábla és a fájlformátum definíciójának megfelelően kell igazítania. A szövegfájl egyes sorában lévő adatoknak igazodniuk kell a tábladefinícióhoz.
A szövegfájlok formázása:

- Ha az adatok nem relációs forrásból származik, sorokat és oszlopokat kell átalakítania. Akár relációs, akár nem relációs forrásból származó adatokról van szó, az adatokat úgy kell átalakítani, hogy igazodni tudjanak annak a táblának az oszlopdefinícióihoz, amelybe az adatokat be fogja tölteni.
- Formázza az adatokat a szövegfájlban, hogy igazodni fog az SQL-készlet céltáblájában található oszlopokhoz és adattípusokhoz. A külső szövegfájlokban és az adatraktár táblájában található adattípusok közötti különbségek miatt a rendszer elutasítja a sorokat a betöltés során.
- A szövegfájl mezőinek elválasztása egy lezáróval.  Mindenképpen olyan karaktert vagy karaktersorozatot használjon, amely nem található meg a forrásadatok között. Használja a [CREATE EXTERNAL FILE FORMAT (KÜLSŐ FÁJLFORMÁTUM) használatával megadott lezárót.](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true)

## <a name="4-load-the-data-into-dedicated-sql-pool-staging-tables-using-polybase"></a>4. Adatok betöltése dedikált SQL-készlet előkészítési tábláiba a PolyBase használatával

Az ajánlott eljárás az adatok átmeneti táblába való betöltése. Az előkészítési táblák lehetővé teszik, hogy a hibákat az éles táblák zavarása nélkül kezelje. Az előkészítési tábla arra is lehetőséget biztosít, hogy az SQL-készlet beépített elosztott lekérdezésfeldolgozási képességeit használja az adatátalakításokhoz, mielőtt az adatokat az éles táblákba szúrja be.

### <a name="options-for-loading-with-polybase"></a>Betöltési lehetőségek a PolyBase-sel

Az adatok PolyBase-sel való betöltéséhez az alábbi betöltési lehetőségek bármelyikét használhatja:

- [A PolyBase és a T-SQL](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) jól működik, ha az adatok az Azure Blob Storage-ban vagy az Azure-ban Data Lake Store. Ez biztosítja a legnagyobb irányítást a betöltési folyamat felett, de külső adatobjektumok definiálása is szükséges hozzá. A többi metódus a háttérben határozza meg ezeket az objektumokat, amikor leképezi a forrástáblákat a céltáblákra.  A T-SQL-terhelések vezénylése érdekében használhatja a Azure Data Factory, az SSIS vagy az Azure Functions függvényeket.
- [A PolyBase és az SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) jól működik, ha a forrásadatok a SQL Server. Az SSIS meghatározza a forrás és a céltábla leképezését, és a terhelést is vezényli. Ha már rendelkezik SSIS-csomagokkal, módosíthatja a csomagokat, hogy az az új adattárház célhelyének megfelelően működjön.
- [A PolyBase Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) egy másik vezénylési eszköz.  Meghatároz egy folyamatot, és ütemezi a feladatokat.
- A [polybase Azure Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) adatokat továbbít egy Azure Synapse Analytics-táblából egy Databricks-adatkeretbe, és/vagy adatokat ír egy Databricks-adatkeretből egy Azure Synapse Analytics táblába a PolyBase használatával.

### <a name="non-polybase-loading-options"></a>Nem PolyBase betöltési lehetőségek

Ha az adatok nem kompatibilisek a PolyBase-sel, használhatja a [bcp-t](/sql/tools/bcp-utility?view=azure-sqldw-latest&preserve-view=true) vagy az [SQLBulkCopy API-t.](/dotnet/api/system.data.sqlclient.sqlbulkcopy) A bcp közvetlenül a dedikált SQL-készletbe tölt be az Azure Blob Storage használata nélkül, és csak kis terhelésekhez tervezték. Vegye figyelembe, hogy ezeknek a beállításoknak a terhelési teljesítménye jelentősen lassabb, mint a PolyBase.

## <a name="5-transform-the-data"></a>5. Az adatok átalakítása

Amíg az adatok az előkészítési táblában vannak, hajtsa végre a számítási feladathoz szükséges átalakításokat. Ezután helyezze át az adatokat egy éles táblába.

## <a name="6-insert-the-data-into-production-tables"></a>6. Adatok beszúrása éles táblákba

Az INSERT INTO... A SELECT utasítás áthelyezi az adatokat az előkészítési táblából az állandó táblába.

Az ETL-folyamat tervezése során próbálja meg egy kisebb tesztmintán futtatni a folyamatot. Próbáljon kinyerni 1000 sort a táblából egy fájlba, helyezze át az Azure-ba, majd próbálja meg betölteni egy előkészítési táblába.

## <a name="partner-loading-solutions"></a>Partnerek betöltési megoldásai

Számos partnerünk betöltési megoldásokkal is. További információért tekintse meg megoldáspartnereink [listáját.](../sql-data-warehouse/sql-data-warehouse-partner-business-intelligence.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="next-steps"></a>Következő lépések

A betöltési útmutatásért lásd: [Útmutatás az adatok betöltéséhez.](data-loading-best-practices.md)