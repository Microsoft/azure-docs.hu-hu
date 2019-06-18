---
title: Mik azok az Apache Hadoop és az Apache Spark-technológia? - Azure HDInsight
description: A HDInsight és az Apache Hadoop és az Apache Spark-technológiák és összetevők, mint a big data-elemzéshez a Kafka, Hive, Storm és HBase bemutatása.
keywords: azure hadoop, hadoop azure, hadoop bevezetés, hadoop bemutatása, hadoop-technológiák, bevezetés a hadoop szolgáltatásba, a hadoop bemutatása, mi az a hadoop-fürt, mi a hadoop-fürt, mire való a hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.topic: overview
ms.date: 06/11/2019
ms.openlocfilehash: 42bfa571818efa4d4a5ad96f62a47fb2b80dca3c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057564"
---
# <a name="what-is-apache-hadoop-in-azure-hdinsight"></a>Mi az Azure HDInsight az Apache Hadoop?

Ebben a cikkben megismerkedhet az Azure HDInsight-alapú Apache Hadooppal. Az Azure HDInsight egy teljes körűen felügyelt, teljes körű, nyílt forráskódú analitikai szolgáltatás nagyvállalatok számára a felhőben. Nyílt forráskódú keretrendszereket, mint a Hadoop-, Apache Spark, az Apache Hive, LLAP, Apache Kafka, Apache Storm, R, és további is használhatja. 


## <a name="what-is-hdinsight-and-the-hadoop-technology-stack"></a>Mi a HDInsight, és mik azok a Hadoop-technológiák?

Az [Apache Hadoop](https://hadoop.apache.org/) volt a big data jellegű adatkészletek fürtökön végzett elosztott feldolgozásának, tárolásának és elemzésének eredeti nyílt forráskódú keretrendszere. A Hadoop-technológiák tartalmaz a kapcsolódó szofverek és segédprogramok, többek között az Apache Hive, az Apache HBase, Spark, Kafka és sok más.


Az Azure HDInsight Hadoop-összetevők felhőalapú terjesztett. Az Azure HDInsight segítségével könnyen, gyorsan és költséghatékonyan dolgozható fel nagy mennyiségű adat. Használhat olyan népszerű, nyílt forráskódú keretrendszereket, mint a Hadoop, a Spark, a Hive, az LLAP, a Kafka, a Storm, az R stb. Ezekkel a keretrendszerekkel a forgatókönyvek széles körét alkalmazhatja, köztük kinyerési, átalakítási és betöltési (ETL) feladatokat, adatraktározást, gépi tanulást és az IoT-t.

A HDInsight elérhető Hadoop-technológiák összetevőinek megtekintéséhez lásd: [elérhető HDInsight-összetevők és verziók](../hdinsight-component-versioning.md). További tudnivalók a HDInsightban használt Hadoopról [az Azure-szolgáltatások HDInsightra vonatkozó oldalán](https://azure.microsoft.com/services/hdinsight/) olvashatók.

## <a name="what-is-big-data"></a>Mik azok a big data típusú adatok?

A big data gyűjtése egyre nagyobb mennyiségben és sebességgel, minden korábbinál többféle formátumban történik. Ezek lehetnek előzményadatok (azaz tárolt) vagy valós idejű adatok (vagyis a forrásból streamelt adatok). A big data leggyakoribb alkalmazási helyzetei: [A HDInsight használatára vonatkozó forgatókönyvek](#scenarios-for-using-hdinsight).

## <a name="why-should-i-use-hadoop-on-hdinsight"></a>Miért érdemes használni a HDInsight-alapú Hadoopot?

Ez a szakasz felsorolja az Azure HDInsight képességeit.


|Képesség  |Leírás  |
|---------|---------|
|Natív felhőalapú     |     Az Azure HDInsight segítségével optimalizált fürtöket hozhat létre az Azure-ban a következőkhöz: [Hadoop](apache-hadoop-linux-tutorial-get-started.md), [Spark](../spark/apache-spark-jupyter-spark-sql.md), [Interaktív lekérdezés (LLAP)](../interactive-query/apache-interactive-query-get-started.md), [Kafka](../kafka/apache-kafka-get-started.md), [Storm](../storm/apache-storm-tutorial-get-started-linux.md), [HBase](../hbase/apache-hbase-tutorial-get-started-linux.md) és  [ML-szolgáltatások](../r-server/r-server-get-started.md). A HDInsight egy végpontok közötti SLA-t is biztosít az összes éles környezetben futó számítási feladathoz.  |
|Alacsony költségű és méretezhető     | A HDInsight segítségével a számítási feladatok vertikális [skálázását](../hdinsight-administer-use-portal-linux.md#scale-clusters)  felfelé vagy lefelé is elvégezheti. Csökkentheti a költségeket  [a fürtök igény szerinti létrehozása](../hdinsight-hadoop-create-linux-clusters-adf.md) és a használatalapú fizetés révén. A feladatokat adatfolyamatok létrehozásával is működtetheti. A leválasztott számítás és tárolás jobb teljesítményt és rugalmasságot biztosít. |
|Biztonságos és megfelelő    | Az [Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md), a [titkosítás](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md) és az [Azure Active Directoryval való integráció](../domain-joined/apache-domain-joined-introduction.md) segítségével a HDInsight lehetővé teszi a vállalati adategységek védelmét. A HDInsight továbbá megfelel a legnépszerűbb iparági és kormányzati [megfelelőségi szabványoknak](https://azure.microsoft.com/overview/trusted-cloud).        |
|Figyelés    | Az Azure HDInsight integrálható [naplózza az Azure Monitor](../hdinsight-hadoop-oms-log-analytics-tutorial.md) , amellyel követheti az összes fürt egységes felületet biztosít.        |
|Globális rendelkezésre állás | A HDInsight több  [régióban](https://azure.microsoft.com/regions/services/)  érhető el, mint bármelyik másik big data-elemzési ajánlat. Az Azure HDInsight elérhető az Azure Governmentben, Kínában, és Németországban is, így megfelelhet a vállalati igényeknek a főbb szuverén területeken. |  
|Termelékenység     |  Az Azure HDInsight számos hatékony eszközt biztosít a Hadoop és a Spark használatához a választott fejlesztési környezetben. Ez a fejlesztési környezet lehet a [Visual Studio](apache-hadoop-visual-studio-tools-get-started.md), a [VSCode](../hdinsight-for-vscode.md), az [Eclipse](../spark/apache-spark-eclipse-tool-plugin.md) vagy az [IntelliJ](../spark/apache-spark-intellij-tool-plugin.md) a Scala, a Python, az R, a Java és a .NET támogatásához. Az adatelemzők is együttműködhetnek olyan népszerű notebookok használatával, mint a [Jupyter](../spark/apache-spark-jupyter-notebook-kernels.md) vagy a [Zeppelin](../spark/apache-spark-zeppelin-notebook.md).    |
|Bővíthetőség     |  A HDInsight-fürtöket bővítheti telepített összetevőkkel (Hue, Presto stb.) [szkriptműveletek](../hdinsight-hadoop-customize-cluster-linux.md) használatával, [élcsomópontok felvételével](../hdinsight-apps-use-edge-node.md) vagy [más big data minősítéssel rendelkező alkalmazások integrálásával](../hdinsight-apps-install-applications.md). A HDInsight [egykattintásos](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) üzembe helyezéssel biztosít zökkenőmentes integrációt a legnépszerűbb big data-megoldásokkal.|

## <a name="scenarios-for-using-hdinsight"></a>A HDInsight használatára vonatkozó forgatókönyvek

Az Azure HDInsight számos, a big data koncepción alapuló adatfeldolgozási forgatókönyvben alkalmazható. Ezek lehetnek előzményadatok (már összegyűjtött és tárolt adatok) vagy valós idejű adatok (közvetlenül a forrásból streamelt adatok). Az ilyen adatok feldolgozásának forgatókönyveit a következő kategóriákban lehet összegezni: 

### <a name="batch-processing-etl"></a>Kötegelt feldolgozás (ETL)

A kinyerési, átalakítási és betöltési (ETL) folyamat során a rendszer strukturálatlan és strukturált adatokat nyer ki heterogén adatforrásokból. Ezután strukturált formátumúvá alakítja azokat, majd betölti egy adattárba. Az átalakított adatok adatelemzéshez vagy adatraktározáshoz használhatók.

### <a name="data-warehousing"></a>Adatraktározás

A HDInsight segítségével petabájtos nagyságrendű interaktív lekérdezéseket végezhet strukturált vagy strukturálatlan adatokon. Modelleket is létrehozhat BI-eszközökhöz való csatlakoztatással. További információkért [olvassa el ezt az ügyféltörténetet](https://customers.microsoft.com/story/milliman). 

![HDInsight-architektúra: Az adattárházak](./media/apache-hadoop-introduction/hdinsight-architecture-data-warehouse.png "HDInsight adattárház-architektúra")

### <a name="internet-of-things-iot"></a>Eszközök internetes hálózata (IoT)

A HDInsight segítségével számos eszközről fogadott streamelési adatok dolgozhatók fel valós időben. További információkért [olvassa el ezt az Azure-blogbejegyzést, amely az Azure Managed Disksben HDInsight alatt futó Apache Kafka nyilvános előzetes verzióját jelenti be](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/).

![HDInsight-architektúra: Az eszközök internetes hálózatát](./media/apache-hadoop-introduction/hdinsight-architecture-iot.png "HDInsight IoT-architektúra") 

### <a name="data-science"></a>Adatelemzés

A HDInsight segítségével olyan alkalmazásokat hozhat létre, amelyek kritikus fontosságú elemzéseket nyernek ki az adatokból. Az Azure Machine Learning segítségével ezen felül a vállalata várható trendjeiről is kaphat előrejelzéseket. További információkért [olvassa el ezt az ügyféltörténetet](https://customers.microsoft.com/story/pros).

![HDInsight-architektúra: Az adatelemzés](./media/apache-hadoop-introduction/hdinsight-architecture-data-science.png "HDInsight data science-architektúra")

### <a name="hybrid"></a>Hibrid

A HDInsight segítségével kiterjesztheti a meglévő helyszíni big data-infrastruktúráját az Azure-ba, hogy kihasználhassa a felhő fejlett elemzési képességeit.

![HDInsight-architektúra: Hibrid](./media/apache-hadoop-introduction/hdinsight-architecture-hybrid.png "HDInsight hibrid architektúra")

## <a name="cluster-types-in-hdinsight"></a>Fürttípusok a HDInsightban

A HDInsight adott fürttípusokat és fürttestreszabási képességeket is tartalmaz, például lehetővé teszi összetevők, segédprogramok és nyelvek hozzáadását. A HDInsight a következő fürttípusokat kínálja:

|Fürt típusa | Leírás |
|---|---|
|[Az Apache Hadoop](https://wiki.apache.org/hadoop)|Egy keretrendszer, amelyben HDFS, a YARN erőforrás-kezelés és a egy egyszerű MapReduce programozási modell a batch-adatok párhuzamos feldolgozásához és elemzéséhez.|
|[Apache Spark](https://spark.apache.org/)|Egy nyílt forráskódú, párhuzamos feldolgozást végző keretrendszer, amely támogatja a memórián belüli feldolgozást a big data elemző alkalmazások teljesítményének növelése érdekében. Lásd: [Mi a HDInsight-alapú Apache Spark?](../spark/apache-spark-overview.md).|
|[Apache HBase](https://hbase.apache.org/)|A Hadoop, amely véletlenszerű hozzáférést és erős konzisztenciát biztosít a nagy mennyiségű strukturálatlan és félig strukturált adatot – sorok potenciálisan több milliárd épülő NoSQL-adatbázis túllépi az oszlopok millió. Lásd: [Mi a HDInsight-alapú HBase?](../hbase/apache-hbase-overview.md)|
|[Machine Learning-szolgáltatások](https://docs.microsoft.com/machine-learning-server/rebranding-microsoft-r-server)|A kiszolgáló és párhuzamos, elosztott R-folyamatok kezelésére szolgál. Skálázható, elosztott elemzési módszereket kínál az adatszakértők, statisztikusok és R-programozók számára a HDInsightban. Tekintse meg az [ML-szolgáltatások használatának áttekintését a HDInsighton](../r-server/r-server-overview.md).|
|[Apache Storm](https://storm.incubator.apache.org/)|Egy elosztott, valós idejű számítási rendszer a nagy méretű adatfolyamok gyors feldolgozására. A Storm a HDInsightban felügyelt fürtként érhető el. Lásd: [Analyze real-time sensor data using Storm and Hadoop](../storm/apache-storm-sensor-data-analysis.md) (Valós idejű érzékelőadatok elemzése a Storm és a Hadoop segítségével).|
|[Apache interaktív lekérdezés](https://cwiki.apache.org/confluence/display/Hive/LLAP)|Memóriában történő gyorsítótárazás az interaktív és gyorsabb Hive-lekérdezésekhez. Lásd: [Az interaktív lekérdezés használata a HDInsightban](../interactive-query/apache-interactive-query-get-started.md).|
|[Apache Kafka](https://kafka.apache.org/)|Egy nyílt forráskódú platform streamelt adatfolyamatok és alkalmazások létrehozásához használt. A Kafka egy Üzenetsor funkciót is biztosít, amelynek segítségével közzétehet adatstreameket vagy feliratkozhat rájuk. Lásd: [A HDInsight alatt futó Apache Kafka bemutatása](../kafka/apache-kafka-introduction.md).|

## <a name="open-source-components-in-hdinsight"></a>Nyílt forráskódú összetevők a HDInsightban

Az Azure HDInsight lehetővé teszi, hogy a nyílt forráskódú keretrendszereket, mint a Hadoop, Spark, Hive, LLAP, Kafka, Storm, HBase és r-fürtök létrehozása Ezek a fürtök alapértelmezés szerint származnak, más nyílt forráskódú összetevőkkel, mint például a fürtön képező [Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md), [Avro](https://avro.apache.org/docs/current/spec.html), [Apache Hive](https://hive.apache.org), [ HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog/), [Apache Mahout](https://mahout.apache.org/), [Apache Hadoop MapReduce](https://wiki.apache.org/hadoop/MapReduce), [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Apache Phoenix](https://phoenix.apache.org/), [Apache Pig](https://pig.apache.org/), [Apache Sqoop](https://sqoop.apache.org/), [Apache Tez](https://tez.apache.org/), [Apache Oozie](https://oozie.apache.org/), [Apache ZooKeeper](https://zookeeper.apache.org/).  

## <a name="programming-languages-in-hdinsight"></a>Programozási nyelvek a HDInsightban

A HDInsight-fürtök, például többek közt a Spark, a HBase, a Kafka, és a Hadoop, számos programozási nyelvet támogatnak. Egyes programozási nyelvek nincsenek alapértelmezés szerint telepítve. Alapértelmezés szerint nem telepített könyvtárak, modulok vagy csomagok esetén [használjon szkriptműveletet az összetevő telepítéséhez](../hdinsight-hadoop-script-actions-linux.md).

|Programozási nyelv  |Információ  |
|---------|---------|
|Alapértelmezés szerint támogatott programozási nyelvek     | Alapértelmezés szerint a HDInsight-fürtök a következőket támogatják:<ul><li>Java</li><li>Python</li><li>.NET</li><li>Indítás</li></ul>  |
|JVM (Java virtuális gép) nyelvek     | A Java-alapú virtuális gépeken (JVM) a Javán kívül számos más nyelv is futtatható. Előfordulhat azonban, hogy egyes nyelvek futtatásához további összetevőket is telepítenie kell a fürtön. A HDInsight-fürtök az alábbi JVM-alapú nyelveket támogatják: <ul><li>Clojure</li><li>Jython (Python a Javához)</li><li>Scala</li></ul>     |
|Hadoop-specifikus nyelvek     | A HDInsight-fürtök az alábbi, a Hadoop-technológiákra jellemző nyelveket támogatják: <ul><li>Pig Latin a Pig-feladatokhoz</li><li>HiveQL a Hive-feladatokhoz és a SparkSQL-hez</li></ul>        |

## <a name="development-tools-for-hdinsight"></a>A HDInsight fejlesztői eszközei

A HDInsight fejlesztői eszközei (köztük az IntelliJ, az Eclipse, a Visual Studio Code és a Visual Studio) használatával HDInsight-adatlekérdezéseket és feladatokat szerkeszthet és küldhet be Azure-ral zökkenőmentesen együttműködve.

* [Az IntelliJ-hez készült Azure-eszközkészlet](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin)

* [Az Eclipse-hez készült Azure-eszközkészlet](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-eclipse-tool-plugin)

* [A VS Code-hoz készült Azure HDInsight-eszközök](https://docs.microsoft.com/azure/hdinsight/hdinsight-for-vscode?branch=pr-en-us-22999)

* [A Visual Studióhoz készült Azure Data Lake-eszközök](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started)

## <a name="business-intelligence-on-hdinsight"></a>Üzleti intelligencia a HDInsighton

A jól ismert üzletiintelligencia- (BI-) eszközök a Power Query beépülő modul vagy a Microsoft Hive ODBC-illesztő segítségével kérik le, elemzik és jelentik a HDInsight rendszerébe integrált adatokat:

* [Az Apache Spark BI adatvizualizációs eszközeinek használata az Azure HDInsighttal](../spark/apache-spark-use-bi-tools.md)

* [A Microsoft Power bi-ban az Azure HDInsight az Apache Hive-adatok megjelenítése](apache-hadoop-connect-hive-power-bi.md)

* [Interaktív lekérdezéses Hive-adatok vizualizálása a Microsoft Power BI segítségével az Azure HDInsightban](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)

* [Excel csatlakoztatása a Power Query az Apache Hadoop](apache-hadoop-connect-excel-power-query.md) (Windows igényel)

* [Excel csatlakoztatása a Microsoft Hive ODBC-illesztő az Apache Hadoop](apache-hadoop-connect-excel-hive-odbc-driver.md) (Windows igényel)

* [Az SQL Server Analysis Services használata a HDInsighttal](https://docs.microsoft.com/previous-versions/msp-n-p/dn749857(v=pandp.10))

* [Az SQL Server Reporting Services használata a HDInsighttal](https://docs.microsoft.com/previous-versions/msp-n-p/dn749856(v=pandp.10))

## <a name="next-steps"></a>További lépések

* [A HDInsight Apache Hadoop-fürt létrehozása](apache-hadoop-linux-create-cluster-get-started-portal.md)
