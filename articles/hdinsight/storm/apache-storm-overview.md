---
title: Mi az az Apache Storm? – Azure HDInsight
description: Az Apache Stormmal valós időben dolgozhat fel adatfolyamokat. Az Azure HDInsighttal könnyen létrehozhat Storm-fürtöket az Azure-felhőben. A Visual Studióval C# használatával hozhat létre Storm-megoldásokat, amelyeket a HDInsight Storm-fürtjeiben helyezhet üzembe.
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: a2bca34f683df27c97b131d57c6c3278f7cb175e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98928907"
---
# <a name="what-is-apache-storm-on-azure-hdinsight"></a>Mi az Azure HDInsight alatt futó Apache Storm?

Az [Apache Storm](https://storm.apache.org/) egy elosztott, nagy hibatűrésű, nyílt forráskódú számítási rendszer. A Storm segítségével valós időben dolgozhat fel adatfolyamokat [Apache Hadoop](../hadoop/apache-hadoop-introduction.md)használatával. A Storm-megoldások emellett garantált adatfeldolgozást is biztosítanak, amely képes visszajátszani az elsőre sikeresen fel nem dolgozott adatokat.

## <a name="why-use-apache-storm-on-hdinsight"></a>Miért érdemes Apache Storm a HDInsight?

A HDInsight alatt futó Storm a következő szolgáltatásokat biztosítja:

* __99% szolgáltatói szerződés (SLA) a Storm üzemidőre__: a Storm on HDInsight teljes körű támogatással jár. A HDInsight alatt futó Storm emellett szolgáltatásiszint-szerződésben garantált 99,9%-os elérhetőséggel rendelkezik. Ennek értelmében a Microsoft garantálja, hogy az egyes Storm-fürtök az idő legalább 99,9%-ában elérhetők lesznek kívülről. További információk: [Azure-ügyfélszolgálat](https://azure.microsoft.com/support/options/). Lásd még: [SLA-információk a HDInsight](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/) -dokumentumhoz.

* Könnyen testre szabható a szkriptek Storm-fürtön történő futtatásával a létrehozás során vagy után. További információ: [HDInsight-fürtök testre szabása szkriptműveletekkel](../hdinsight-hadoop-customize-cluster-linux.md).

* **Megoldások létrehozása több nyelven**: A Storm-összetevőket tetszőleges nyelven megírhatja, például Java, C# és Python nyelven is.

    * A Visual Studio és a HDInsight integrációjával biztosítja a C#-topológiák fejlesztését, felügyeletét és figyelését. További információ: [C# Storm-topológiák fejlesztése a HDInsight Tools for Visual Studio szolgáltatással](apache-storm-develop-csharp-visual-studio-topology.md).

    * Támogatja a Trident Java-felületet. Olyan Storm-topológiákat hozhat létre, amelyek támogatják az üzenetek pontosan egyszeri feldolgozását, a tranzakciós adattároló-megőrzést és számos gyakori Stream Analytics-műveletet.

* **Dinamikus méretezés**: A futó Storm-topológiák befolyásolása nélkül adhat hozzá vagy távolíthat el feldolgozó csomópontokat. A skálázási műveletekkel hozzáadott új csomópontok kihasználása érdekében inaktiválja és aktiválja a futó topológiákat.

* **Több Azure-szolgáltatást használó streaming-folyamatok létrehozása**: a Storm on HDInsight integrálható más Azure-szolgáltatásokkal. Például Event Hubs, SQL Database, Azure Storage és Azure Data Lake Storage. Az Azure-szolgáltatásokkal való integrációt bemutató megoldásért lásd: [események feldolgozása Event Hubsról Apache Storm a HDInsight](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub).

A valós idejű elemzési megoldásaikhoz Apache Stormot használó vállalatok listája itt található: [Az Apache Stormot használó vállalatok](https://storm.apache.org/Powered-By.html).

A Storm használatának megkezdéséhez lásd: [Apache Storm topológia létrehozása és figyelése az Azure HDInsight-ben](apache-storm-quickstart.md).

## <a name="how-does-apache-storm-work"></a>Hogyan működik Apache Storm

A Storm a [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html)  feladatok helyett topológiákat futtat. A Storm-topológiák több összetevőből állnak, amelyek egy irányított aciklikus gráfba (DAG) vannak rendezve. Az adatáramlás a gráf összetevői között zajlik. Minden összetevőbe egy vagy több stream érkezik be, valamint egy vagy több streamet sugároz. Az alábbi ábrán az összetevők közti adatfolyamok láthatók egy alapszintű szószámlálási topológiában:

![Példa az összetevők elrendezésére egy Storm-topológiában](./media/apache-storm-overview/example-apache-storm-topology-diagram.png)

* A spout-összetevők adatokat importálnak a topológiába. Egy vagy több streamet sugároznak a topológiába.

* A bolt-összetevők a spout- vagy más bolt-összetevők által sugárzott streameket dolgozzák fel. A boltok opcionálisan streameket is sugározhatnak a topológiába. A boltok felelősek az adatok külső szolgáltatásokba vagy tárolókba, például HDFS-, Kafka- vagy HBase-tárolókba történő kiírásáért.

## <a name="reliability"></a>Megbízhatóság

Az Apache Storm garantálja, hogy mindig minden bejövő üzenetet feldolgoz, még akkor is, ha az adatok elemzése több száz csomópont között oszlik meg.

A Nimbus csomópont a Apache Hadoop JobTracker hasonló funkciókat biztosít. A Nimbus Apache ZooKeeper használatával rendeli hozzá a feladatokat a fürt más csomópontjaihoz. A Zookeeper-csomópontok koordinációt biztosítanak a fürt számára, és segítik a Nimbus és a felügyelői folyamat közötti kommunikációt a munkavégző csomópontokon. Ha egy feldolgozó csomópont leáll, arról a Nimbus csomópont értesítést kap, és kiosztja a feladatot és a kapcsolódó adatokat egy másik csomópontnak.

Az alapértelmezett konfiguráció szerint az Apache Storm-fürtök csak egyetlen Nimbus csomóponttal rendelkeznek. A HDInsight alatt futó Storm két Nimbus csomóponttal rendelkezik. Ha az elsődleges csomópont meghibásodik, a Storm-fürt átvált a másodlagos csomópontra, amíg az elsődleges csomópont helyreállítása be nem következik. A következő ábra a Storm on HDInsight feladatfolyam-konfigurációját mutatja be:

![Diagram: Nimbus, Zookeeper és Supervisor](./media/apache-storm-overview/storm-diagram-nimbus.png)

## <a name="ease-of-use"></a>Egyszerű használat

|Használat |Description |
|---|---|
|Secure Shell-(SSH-) kapcsolat|A Storm-fürt fő csomópontjait az interneten keresztül érheti el az SSH használatával. Az SSH használatával közvetlenül futtathat parancsokat a fürtön. További információ: az [SSH használata a HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).|
|Webes kapcsolat|Az összes HDInsight-fürt biztosítja a Ambari webes felhasználói felületét. Az Ambari webes felülettel egyszerűen figyelheti, konfigurálhatja és kezelheti a fürtön futó szolgáltatásokat. A Storm felhasználói felülete a Storm-fürtökön is elérhető. A Storm felhasználói felülettel böngészőből figyelhetők és kezelhetők a futó Storm-topológiák. További információkért tekintse meg a [HDInsight kezelése az Apache Ambari webes felülettel](../hdinsight-hadoop-manage-ambari.md) és a [figyelés és kezelés a Apache Storm felhasználói felületi dokumentumok használatával](apache-storm-deploy-monitor-topology-linux.md#monitor-and-manage-a-topology-using-the-storm-ui) című témakört.|
|Azure PowerShell és Azure CLI|A PowerShell és az Azure CLI egyaránt biztosít olyan parancssori segédprogramokat, amelyeket az HDInsight és más Azure-szolgáltatásokkal való együttműködéshez használhat.|
|Visual Studio integráció|A Visual studióhoz készült Azure Data Lake Tools tartalmazza a C# Storm-topológiák létrehozásához szükséges SCP.NET keretrendszert. A Data Lake Tools emellett megoldások üzembe helyezéséhez, figyeléséhez és felügyeletéhez is tartalmaz eszközöket a HDInsight alatt futó Stormhoz. További információ: [C# Storm-topológiák fejlesztése a HDInsight Tools for Visual Studio szolgáltatással](apache-storm-develop-csharp-visual-studio-topology.md).|

## <a name="integration-with-other-azure-services"></a>Integráció más Azure-szolgáltatásokkal

* __Azure Data Lake Storage__: tekintse [meg a Azure Data Lake Storage használata a Apache Storm használatával HDInsight](apache-storm-write-data-lake-store.md).

* __Event Hubs__: Az Event Hubs és egy Storm-fürt együttes használatára vonatkozó példáért tekintse meg az alábbi dokumentumokat:

    * [Események feldolgozása az Azure Event Hubs és az Apache Storm on HDInsight (Java) szolgáltatással](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

    * [Események feldolgozása az Azure Event Hubs és az Apache Storm on HDInsight (C#) használatával](apache-storm-develop-csharp-event-hub-topology.md)

* __SQL Database__, __Cosmos DB__, __Event Hubs__ és __HBase__: A Data Lake Tools for Visual Studio tartalmaz példasablonokat. További információ: C#- [topológia fejlesztése a HDInsight-hez Apache Storm](apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="apache-storm-use-cases"></a>Apache Storm használati esetek

Az alábbiakban néhány gyakori forgatókönyvet ismertetünk, amelyek esetén a HDInsight alatt futó Storm használható:

* Eszközök internetes hálózata (IoT)
* Csalások észlelése
* Közösségi hálók adatainak elemzése
* Kinyerés, átalakítás és betöltés (ETL)
* Hálózatfigyelés
* Keresés
* Mobilmarketing

A valós forgatókönyvekkel kapcsolatos információkért tekintse meg a [vállalatok hogyan használják Apache Storm](https://storm.apache.org/Powered-By.html) dokumentumot.

## <a name="development"></a>Fejlesztés

A Data Lake Tools for Visual Studio használatával a .NET-fejlesztők C# nyelven tervezhetnek és valósíthatnak meg topológiákat. Létrehozhatók Java- és C#-összetevőket egyaránt használó hibrid topológiák is. További információk: [C#-topológiák fejlesztése HDInsight alatt futó Apache Stormra a Visual Studio használatával](apache-storm-develop-csharp-visual-studio-topology.md)

Java-megoldásokat is fejleszthet tetszőleges IDE használatával. További információ: Java- [topológiák fejlesztése a HDInsight-hez Apache Storm](apache-storm-develop-java-topology.md).

A Python is használható Storm-összetevők fejlesztéséhez. További információ: [Apache Storm topológiák fejlesztése a Python használatával a HDInsight-on](apache-storm-develop-python-topology.md).

## <a name="common-development-patterns"></a>Gyakori fejlesztési minták

### <a name="guaranteed-message-processing"></a>Garantált üzenetfeldolgozás

Az Apache Storm különböző szinteken biztosít garantált üzenetfeldolgozást. Egy alapszintű Storm-alkalmazás például legalább egyszeri feldolgozást garantál, a Trident pedig pontosan egyszer tudja feldolgozni a feldolgozást. Lásd: az [adatfeldolgozás garanciái](https://storm.apache.org/about/guarantees-data-processing.html) a Apache.org címen.

### <a name="ibasicbolt"></a>IBasicBolt

Egy bemeneti rekord olvasásának mintája, amely nulla vagy több rekordok, majd a végrehajtási metódus végén azonnal megerősíti a bemeneti rekordot. A Storm lehetővé teszi ennek a mintának az automatizálását az [IBasicBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/IBasicBolt.html) felület segítségével.

### <a name="joins"></a>Összekapcsolások

Az adatstreamek alkalmazások közötti csatlakoztatásának különböző módjai. Például összekapcsolhatja az egyes rekordokat több streamből egy új adatfolyamba, vagy csak a rekordok egy adott ablakhoz csatlakozhat. Az illesztés mindkét módszer esetén a [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) használatával történik. Ez egy mód annak definiálására, hogyan legyenek átirányítva a rekordok a boltokhoz.

A következő Java-példában az „1”, „2” és „3” jelű összetevőktől eredő rekordok a fieldsGrouping használatával vannak átirányítva a MyJoiner bolthoz:

```java
builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));
```

### <a name="batches"></a>Kötegek

A Storm rendelkezik egy belső időzítő mechanizmussal, az úgynevezett „tick tuple” rekordórajellel. Ennek segítségével beállíthatja, hogy a topológia milyen gyakran bocsásson ki egy-egy rekordórajelet.

Példa egy rekordórajel C#-összetevőből való használatára: [PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java).

### <a name="caches"></a>Gyorsítótárak

A memóriában történő gyorsítótárazás gyakran használatos a feldolgozást felgyorsító mechanizmusként, mivel a memóriában tartja a gyakran használt objektumokat. Mivel a topológiák több csomópont, és az egyes csomópontokon belül is több folyamat között oszlanak meg, érdemes megfontolni a [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) használatát. A `fieldsGrouping` használata biztosítja, hogy a gyorsítótárban való kereséshez használt mezőket tartalmazó rekordok mindig ugyanahhoz a folyamathoz legyenek irányítva. Ezzel a csoportosítási funkcióval elkerülhető, hogy a különböző folyamatok ismétlődő gyorsítótár-bejegyzéseket hozzanak létre.

### <a name="stream-top-n"></a>A „legfelső N” számú elem streamelése

Ha egy topológia az első N elem értékének kiszámításától függ, az első N értéket párhuzamosan számítsa ki. Ezután összesítse a számítások eredményét egy globális értékben. Ezt a műveletet a [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) használatával, a mezők párhuzamos feldolgozáshoz való továbbításával hajthatja végre. Ezt követően továbbíthat egy bolthoz, amely globálisan meghatározza a legfelső N számú elem értékét.

Példa az első N elem értékének kiszámítására: [RollingTopWords](https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/org/apache/storm/starter/RollingTopWords.java) példa.

## <a name="logging"></a>Naplózás

A Storm az Apache Log4j 2 használatával naplózza az adatokat. Alapértelmezés szerint nagy mennyiségű adat rögzítése történik, aminek az áttekintése nehézségekkel járhat. A Storm-topológia részeként hozzáadhat egy naplózáskonfigurációs fájlt, amely a naplózás működését vezérli.

A naplózás konfigurálását bemutató példatopológiát a HDInsight alatt futó Stormra vonatkozó [Java-alapú WordCount](apache-storm-develop-java-topology.md) példában tekintheti meg.

## <a name="next-steps"></a>Következő lépések

További információ a Apache Storm on HDInsight valós idejű elemzési megoldásairól:

* [Apache Storm topológia létrehozása és figyelése az Azure HDInsight](apache-storm-quickstart.md)
* [HDInsight alatt futó Apache Storm példatopológiái](apache-storm-example-topology.md)
