---
title: Kinyerés, átalakítás és betöltés (ETL) skálán – Azure HDInsight
description: Megtudhatja, hogyan használják a kinyerési, átalakítási és betöltési HDInsight a Apache Hadoop.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: fc488cecb762ff13bcb2999bb06521ebb70b44fb
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946582"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>Kinyerés, átalakítás és betöltés (ETL) skálán

A kinyerési, átalakítási és betöltési (ETL) folyamat a különböző forrásokból származó adatok beszerzésének folyamata. Az adatokat a rendszer a szabványos helyen, megtisztítva és feldolgozva gyűjti. Végső soron az adatok betöltődik egy adattárba, amelyről lekérdezhető. Az örökölt ETL-folyamatok importálják az adatimportálást, megtisztítják a helyet, majd egy rokon adatkezelőben tárolják őket. Az Azure HDInsight számos Apache Hadoop környezeti összetevő támogatja az ETL méretezését.

A HDInsight az ETL-folyamatban való használatát az alábbi folyamat összegzi:

![HDInsight ETL – áttekintés](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

A következő szakaszok az ETL fázisok és a hozzájuk társított összetevők megismerését ismertetik.

## <a name="orchestration"></a>Vezénylés

Az összehangolás az ETL-folyamat minden fázisában megível. A HDInsight-ben található ETL-feladatok gyakran több különböző, egymással együtt működő terméket is tartalmaznak. Például:

- Előfordulhat, hogy a Apache Hive használatával törli az adatmennyiséget, az Apache Pig pedig egy másik rész tisztítására.
- Előfordulhat, hogy a Azure Data Factory használatával tölti be az adatait a Azure Data Lake Store Azure SQL Databaseba.

A megfelelő feladatok a megfelelő időben történő futtatásához szükséges.

### <a name="apache-oozie"></a>Apache Oozie

Az Apache Oozie egy munkafolyamat-koordinációs rendszer, amely a Hadoop-feladatokat kezeli. A Oozie egy HDInsight-fürtön belül fut, és integrálva van a Hadoop-verembe. A Oozie támogatja a Apache Hadoop MapReduce, a Pig, a kaptár és a Sqoop Hadoop-feladatait. A Oozie használatával ütemezhet olyan feladatokat, amelyek egy adott rendszerre vonatkoznak, például a Java-programokra vagy a rendszerhéj-parancsfájlokra.

További információ: az [Apache Oozie és a Apache Hadoop használata munkafolyamatok definiálásához és futtatásához a HDInsight](../hdinsight-use-oozie-linux-mac.md)-ben. Lásd még: [működővé tenni az adatfolyamatot](../hdinsight-operationalize-data-pipeline.md).

### <a name="azure-data-factory"></a>Azure Data Factory

A Azure Data Factory a szolgáltatásként nyújtott platform (a "Péter") formájában biztosítja a szervezési képességeket. Az Azure Data Factory egy felhőalapú integrációs szolgáltatás. Lehetővé teszi, hogy adatvezérelt munkafolyamatokat hozzon létre az adatáthelyezés és az adatátalakítás előkészítéséhez és automatizálásához.

Azure Data Factory használata a következőhöz:

1. Adatvezérelt munkafolyamatokat hozhat létre és ütemezhet. Ezek a folyamatok különböző adattárakból származó adatokból állnak.
1. Az adatok feldolgozása és átalakítása számítási szolgáltatások, például a HDInsight vagy a Hadoop használatával. Ehhez a lépéshez Spark, Azure Data Lake Analytics, Azure Batch vagy Azure Machine Learning is használható.
1. A kimeneti adatokat az adattárakba (például az Azure szinapszis Analyticsbe) teheti közzé a BI-alkalmazások felhasználása érdekében.

A Azure Data Factoryről a [dokumentációban](../../data-factory/introduction.md)talál további információt.

## <a name="ingest-file-storage-and-result-storage"></a>Fájlok tárolási és eredményes tárterületének beolvasása

A forrásadatok fájljai általában egy Azure Storage-beli vagy Azure Data Lake Storage-beli helyre vannak betöltve. A fájlok általában egyszerű formában, például CSV-formátumban vannak formázva. Azonban bármilyen formátumban is lehetnek.

### <a name="azure-storage"></a>Azure Storage

Az Azure Storage-ban speciális alkalmazkodóképesség-célkitűzések szerepelnek. További információkért lásd: [a blob Storage skálázhatósági és teljesítménybeli céljai](../../storage/blobs/scalability-targets.md) . A legtöbb analitikus csomópont esetében az Azure Storage a legjobbat méretezi a sok kisebb fájl kezelésekor. Ha a fiókja korlátain belül van, az Azure Storage ugyanazt a teljesítményt garantálja, függetlenül attól, hogy mekkora a fájlok mérete. Több terabájtos adat is tárolható, és továbbra is konzisztens teljesítményt érhet el. Ez az utasítás igaz, hogy egy részhalmazt vagy az összes adathalmazt használja-e.

Az Azure Storage többféle típusú blobot tartalmaz. A *hozzáfűző blob* nagyszerű lehetőség webnaplók vagy érzékelők-adattárolók tárolására.

Több blob is elosztható több kiszolgáló között a hozzáférés kiskálázásához. Egyetlen blobot azonban csak egyetlen kiszolgáló szolgáltat. Bár a Blobok logikai csoportokba helyezhetők a blob-tárolókban, a csoportosítás nem érinti a particionálást.

Az Azure Storage egy WebHDFS API-réteget tartalmaz a blob Storage-hoz. Az HDInsight-szolgáltatások az Azure Blob Storage-ban lévő fájlokhoz férnek hozzá az adattisztításhoz és az adatfeldolgozáshoz. Ez hasonló ahhoz, ahogyan ezek a szolgáltatások a Hadoop elosztott fájlrendszer (HDFS) használják.

Az Azure Storage-ban általában a PowerShell, az Azure Storage SDK vagy a AZCopy használatával történik az adatgyűjtés.

### <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

A Azure Data Lake Storage egy felügyelt, nagy kapacitású adattár az elemzési szolgáltatásokhoz. Kompatibilis a és a HDFS hasonló tervezési paradigma használatával. Data Lake Storage korlátlan alkalmazkodóképesség biztosítható a teljes kapacitáshoz és az egyes fájlok méretéhez. A nagyméretű fájlok használata jó választás, mivel több csomóponton is tárolhatók. A Data Lake Storageban lévő adatparticionálás a színfalak mögött történik. Nagy átviteli sebességű, több ezer párhuzamos végrehajtóval rendelkező elemzési feladatok futtatásához, amelyek hatékonyan olvassák és leírják a több száz terabájtos adat mennyiségét.

Az adatfeldolgozás általában a Data Lake Storageon keresztül történik Azure Data Factoryon keresztül. Data Lake Storage SDK-kat, a AdlCopy szolgáltatást, az Apache DistCp vagy az Apache Sqoop-t is használhatja. A kiválasztott szolgáltatás attól függ, hogy hol található az adatok. Ha egy meglévő Hadoop-fürtben van, akkor az Apache DistCp, a AdlCopy szolgáltatást vagy a Azure Data Factory is használhatja. Az Azure Blob Storage-ban lévő adattárakban Azure Data Lake Storage .NET SDK-t, Azure PowerShellt vagy Azure Data Factory használhat.

A Data Lake Storage az Azure Event Hubs vagy Apache Storm használatával történő esemény-betöltésre van optimalizálva.

### <a name="considerations-for-both-storage-options"></a>Mindkét tárolási lehetőség szempontjai

Az adatkészletek terabájt tartományban való feltöltéséhez a hálózati késés lehet jelentős probléma. Ez különösen akkor igaz, ha az adatok egy helyszíni helyről érkeznek. Ilyen esetekben a következő lehetőségeket használhatja:

- **Azure-ExpressRoute:** Hozzon létre privát kapcsolatokat az Azure-adatközpontok és a helyszíni infrastruktúra között. Ezek a kapcsolatok megbízható lehetőséget biztosítanak nagy mennyiségű adatátvitelre. További információ: az [Azure ExpressRoute dokumentációja](../../expressroute/expressroute-introduction.md).

- **Adatok feltöltése a merevlemez-meghajtókról:** Az [Azure import/export szolgáltatást](../../import-export/storage-import-export-service.md) használhatja az Azure-adatközpontok adataihoz tartozó merevlemez-meghajtók szállítására. Az adatait először feltölti az Azure Blob Storage-ba. Ezután a Azure Data Factory vagy a AdlCopy eszközzel másolhat adatokat az Azure Blob Storage-ból a Data Lake Storageba.

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

Az Azure szinapszis Analytics megfelelő választás az előkészített eredmények tárolására. Az Azure HDInsight segítségével az Azure szinapszis Analytics szolgáltatáshoz is elvégezheti ezeket a szolgáltatásokat.

Az Azure szinapszis Analytics egy, az analitikus számítási feladatokra optimalizált, összehasonlítható adatbázis-tároló. Particionált táblák alapján méretezhető. A táblák több csomópont között is particionálva lehetnek. A csomópontok a létrehozáskor vannak kiválasztva. Az is előfordulhat, hogy a tény után méretezhetők, de ez egy aktív folyamat, amely adatáthelyezést igényelhet. További információ: [a számítások kezelése az Azure szinapszis Analyticsben](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).

### <a name="apache-hbase"></a>Apache HBase

Az Apache HBase egy kulcs/érték tároló, amely az Azure HDInsight érhető el. Ez egy nyílt forráskódú, NoSQL-adatbázis, amely a Hadoop-ra épül, és a Google BigTable után modellezhető. A HBase nagy mennyiségű strukturálatlan és félig strukturált adatok esetében biztosítja a véletlenszerű hozzáférést és erős konzisztenciát.

Mivel a HBase séma nélküli adatbázis, nem kell oszlopokat és adattípusokat meghatároznia a használata előtt. Az adattárolás egy tábla soraiban történik, és az oszlop család szerint van csoportosítva.

A nyílt forráskód lineáris módon méreteződik át a több ezer csomópontnyi adat petabájtjainak kezelése érdekében. A HBase az adatredundancia, a kötegelt feldolgozás és a Hadoop-környezetben található elosztott alkalmazások által biztosított egyéb funkciók alapján működik.

A HBase jó cél az érzékelő és a naplózási adatai számára a jövőbeli elemzésekhez.

A HBase alkalmazkodóképesség a HDInsight-fürt csomópontjainak számától függ.

### <a name="azure-sql-databases"></a>Azure SQL-adatbázisok

Az Azure három, a (z):

* A [Azure SQL Database](../../azure-sql/database/sql-database-paas-overview.md) Microsoft SQL Server implementációja. A teljesítményre vonatkozó további információkért lásd: [a teljesítmény finomhangolása Azure SQL Databaseban](../../azure-sql/database/performance-guidance.md).
* [Azure Database for MySQL](../../mysql/overview.md)  az Oracle MySQL implementációja.
* [Azure Database for PostgreSQL](../../postgresql/quickstart-create-server-database-portal.md) a PostgreSQL implementációja.

További CPU és memória hozzáadása a termékek vertikális felskálázásához.  Azt is megteheti, hogy prémium szintű lemezeket használ a termékekkel a jobb I/O-teljesítmény érdekében.

## <a name="azure-analysis-services"></a>Azure Analysis Services

Azure Analysis Services a döntési támogatásban és az üzleti elemzésben használt analitikai adatmotor. Az üzleti jelentések és ügyfélalkalmazások, például a Power BI elemzési információit biztosítja. Az analitikai szolgáltatás az Excelben, SQL Server Reporting Services jelentésekben és más adatvizualizációs eszközökkel is együttműködik.

Az elemzési kockák méretezése az egyes adatkockák szintjeinek módosításával. További információ: [Azure Analysis Services díjszabása](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="extract-and-load"></a>Kinyerés és betöltés

Miután az adatok megtalálhatók az Azure-ban, számos szolgáltatás használatával kinyerheti és betöltheti más termékekbe. A HDInsight támogatja a Sqoop és a Flume.

### <a name="apache-sqoop"></a>Apache Sqoop

Az Apache Sqoop egy olyan eszköz, amely a strukturált, részben strukturált és strukturálatlan adatforrások közötti adatátvitelhez készült.

A Sqoop a MapReduce használatával importálja és exportálja az adatgyűjtést, így párhuzamos működést és hibatűrést biztosít.

### <a name="apache-flume"></a>Apache Flume

Az Apache Flume egy elosztott, megbízható és elérhető szolgáltatás a nagy mennyiségű naplózási adatok hatékony gyűjtéséhez, összesítéséhez és áthelyezéséhez. Rugalmas architektúrája az adatfolyam-továbbítási folyamatokon alapul. A Flume robusztus és hibatűrő megbízhatósági mechanizmusokkal rendelkezik. Számos feladatátvételi és helyreállítási mechanizmussal rendelkezik. A Flume egy egyszerű, bővíthető adatmodellt használ, amely lehetővé teszi az online, analitikus alkalmazások használatát.

Az Apache Flume nem használható az Azure HDInsight. Egy helyszíni Hadoop-telepítés azonban a Flume használatával küldheti el az Azure Blob Storage-ba vagy a Azure Data Lake Storageba. További információ: az [Apache Flume és a HDInsight használata](https://web.archive.org/web/20190217104751/https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/).

## <a name="transform"></a>Átalakítás

Miután az adatok megtalálhatók a kiválasztott helyen, meg kell tisztítania, össze kell állítania, vagy elő kell készítenie egy adott használati mintához. A kaptár, a Pig és a Spark SQL minden jó választás az adott típusú munkához. Ezek mind támogatottak a HDInsight.

## <a name="next-steps"></a>Következő lépések

- [Apache Hive használata ETL-eszközként](apache-hadoop-using-apache-hive-as-an-etl-tool.md)
- [Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
- [Adatok áthelyezése Azure SQL Databaseból egy Apache Hive táblázatba](./apache-hadoop-use-sqoop-mac-linux.md)
