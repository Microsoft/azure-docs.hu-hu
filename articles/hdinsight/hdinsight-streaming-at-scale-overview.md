---
title: Adatfolyam-méretezés az Azure HDInsight
description: Skálázható Apache-fürtökkel rendelkező adatfolyamok használata az Azure HDInsight-ben.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 2b2dfe9da55548f2648f847a9d7c2cb3478e6bad
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98931712"
---
# <a name="streaming-at-scale-in-hdinsight"></a>Ipari léptékű streamelés a HDInsightban

A valós idejű big data megoldások a mozgásban lévő adatkezelési műveletek során működnek. Ezek az értékek jellemzően a legértékesebb a megérkezéskor. Ha a bejövő adatfolyam ennél a pillanatnál nagyobb lesz, lehet, hogy le kell állítania az erőforrásokat. Azt is megteheti, hogy egy HDInsight-fürt vertikálisan képes megfelelni a folyamatos átviteli megoldásoknak. ehhez szükség van csomópontok hozzáadására.

Egy adatfolyam-alkalmazásban egy vagy több adatforrás olyan eseményeket generál (néha több millió másodpercenként), amelyeket gyorsan kell bevezetni a hasznos információk eldobása nélkül. A bejövő eseményeket *adatfolyam-puffereléssel*, más néven *Event Queuing* szolgáltatással kezelik, például [Apache Kafka](kafka/apache-kafka-introduction.md) vagy [Event Hubs](https://azure.microsoft.com/services/event-hubs/). Az események összegyűjtése után az *adatfolyam-feldolgozási* rétegben található valós idejű elemzési rendszer használatával elemezheti az adatokat, például [Apache Storm](storm/apache-storm-overview.md) vagy [Apache Spark streaming](spark/apache-spark-streaming-overview.md). A feldolgozott adatok tárolhatók hosszú távú tárolási rendszerekben, például a [Azure Data Lake Storageban](https://azure.microsoft.com/services/storage/data-lake-storage/), és valós időben jeleníthetők meg az üzleti intelligencia irányítópultján, például [Power bi](https://powerbi.microsoft.com), tabló vagy egyéni weblap.

![Azure HDInsight streaming-minták](./media/hdinsight-streaming-at-scale-overview/HDInsight-streaming-patterns.png)

## <a name="apache-kafka"></a>Apache Kafka

A Apache Kafka egy nagy átviteli sebességű, kis késleltetésű üzenetsor-kezelési szolgáltatást biztosít, amely már része a nyílt forráskódú szoftverek (OSS) Apache-csomagjának. A Kafka közzétételi és előfizetési üzenetkezelési modellt használ, és biztonságosan tárolja a particionált adatstreameket egy elosztott, replikált fürtben. A Kafka lineárisan méretezhető, ahogy az átviteli sebesség növekszik.

További információ: [Apache Kafka bemutatása a HDInsight-on](kafka/apache-kafka-introduction.md).

## <a name="apache-storm"></a>Apache Storm

A Apache Storm egy elosztott, hibatűrő, nyílt forráskódú számítási rendszer, amely valós idejű adatstreamek feldolgozására van optimalizálva a Hadoop használatával. A bejövő eseményekhez tartozó alapvető adategység egy rekord, amely a kulcs/érték párok nem módosítható készlete. Ezen rekordok nem kötött sorrendje egy streamet képez, amely egy kiöntőből származik. A kiöntő egy streaming adatforrást (például Kafka) csomagol, és rekordok bocsát ki. A Storm-topológia az átalakítások sorozata ezeken a streameken.

További információ: [What is Apache Storm on Azure HDInsight?](storm/apache-storm-overview.md).

## <a name="spark-streaming"></a>Spark Streaming

A Spark streaming egy Spark-bővítmény, amely lehetővé teszi, hogy ugyanazt a kódot használja, amelyet a kötegelt feldolgozáshoz használ. Egyszerre egyesítheti a Batch és az interaktív lekérdezéseket is ugyanabban az alkalmazásban. A Stormtől eltérően a Spark streaming pontosan egyszer dolgozza fel a szemantikai folyamatokat. Ha a [Kafka Direct API](https://spark.apache.org/docs/latest/streaming-kafka-integration.html)-val együtt használja, amely biztosítja, hogy az összes Kafka-adattal pontosan egyszer érkeznek meg a Spark streaming, a teljes körű garanciák elérését is lehetővé teszi. A Spark streaming erősségei közé tartoznak a hibatűrő képességei, így gyorsan helyreállítható a hibás csomópontok, ha több csomópont van használatban a fürtön belül.

További információ: [What is Apache Spark streaming?](./spark/apache-spark-streaming-overview.md).

## <a name="scaling-a-cluster"></a>Fürt skálázása

Bár a fürt csomópontjait is megadhatja a létrehozás során, érdemes lehet növelni vagy csökkenteni a fürtöt, hogy az megfeleljen a munkaterhelésnek. Az összes HDInsight-fürt lehetővé teszi a [fürtben lévő csomópontok számának módosítását](hdinsight-administer-use-portal-linux.md#scale-clusters). A Spark-fürtöket adatvesztés nélkül el lehet dobni, mivel az összes adattal az Azure Storage vagy a Data Lake Storage tárolja.

A technológiák leválasztása számos előnnyel jár. Például a Kafka egy esemény-pufferelési technológia, ezért nagyon i/o-igényű, és nem igényel nagy mennyiségű feldolgozási teljesítményt. Összehasonlítva a stream-processzorok, például a Spark streaming nagy számítási igényűek, és nagyobb teljesítményű virtuális gépeket igényelnek. Azáltal, hogy ezek a technológiák különböző fürtökre vannak leválasztva, a virtuális gépek legjobb kihasználásával egymástól függetlenül méretezhetők.

### <a name="scale-the-stream-buffering-layer"></a>Az adatfolyam-pufferelés rétegének méretezése

Az adatfolyam-pufferelési technológiák Event Hubs és a Kafka is használja a partíciókat és a fogyasztókat. A bemeneti átviteli sebesség méretezése a partíciók számának és a partíciók hozzáadásának növelését teszi lehetővé. Event Hubs a partíciók száma nem módosítható az üzembe helyezés után, ezért fontos, hogy a célként megadott méretezéssel kezdődjön. A Kafka használatával [partíciókat is hozzáadhat](https://kafka.apache.org/documentation.html#basic_ops_cluster_expansion), még akkor is, ha a Kafka adatfeldolgozást végez. A Kafka eszköz lehetővé teszi a partíciók újbóli hozzárendelését  `kafka-reassign-partitions.sh` . A HDInsight egy [partíciós replika-átegyenlítő eszközt](https://github.com/hdinsight/hdinsight-kafka-tools)biztosít  `rebalance_rackaware.py` . Ez a kiegyenlítő eszköz úgy hívja `kafka-reassign-partitions.sh` meg az eszközt, hogy az egyes replikák külön tartalék tartományba és frissítési tartományba tartoznak, így a Kafka rack tisztában lesz, és növeli a hibatűrést.

### <a name="scale-the-stream-processing-layer"></a>Az adatfolyam feldolgozási rétegének méretezése

A Apache Storm és a Spark streaming is támogatja a munkavégző csomópontok hozzáadását a fürtökhöz, még az adatfeldolgozás során is.

Ahhoz, hogy kihasználhassa az új csomópontok skálázásával hozzáadott csomópontokat, újra kell osztania a fürt méretének növelése előtt elindított Storm-topológiákat. Ez a kiegyensúlyozás a Storm webes kezelőfelületén vagy a parancssori felületén végezhető el. További információ: [Apache Storm dokumentáció](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

A Apache Spark három fő paramétert használ a környezet konfigurálásához az alkalmazás követelményeitől függően: `spark.executor.instances` , `spark.executor.cores` és `spark.executor.memory` . A *végrehajtó* egy Spark-alkalmazáshoz indított folyamat. Egy végrehajtó fut a munkavégző csomóponton, és felelős az alkalmazás feladatainak végrehajtásáért. Az egyes fürtökhöz tartozó végrehajtók és a végrehajtói méretek alapértelmezett száma a munkavégző csomópontok száma és a munkavégző csomópont mérete alapján történik. Ezeket a számokat a rendszer a `spark-defaults.conf` fürt minden egyes csomópontján tárolja a fájlban.

Ez a három paraméter a fürt szintjén konfigurálható a fürtön futó összes alkalmazás esetében, és az egyes alkalmazásokhoz is megadható. További információ: Apache Spark- [fürtök erőforrásainak kezelése](spark/apache-spark-resource-manager.md).

## <a name="next-steps"></a>Következő lépések

* [Apache Storm topológia létrehozása és figyelése az Azure HDInsight](storm/apache-storm-quickstart.md)
* [HDInsight alatt futó Apache Storm példatopológiái](storm/apache-storm-example-topology.md)
* [A HDInsight Apache Spark bemutatása](spark/apache-spark-overview.md)
* [Az HDInsight alatt futó Apache Kafka használatának első lépései](kafka/apache-kafka-get-started.md)