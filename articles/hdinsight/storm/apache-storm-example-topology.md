---
title: Példa Apache Storm topológiák az Azure HDInsight
description: A Apache Storm on HDInsight létrehozott és tesztelt Storm-topológiák listája, beleértve az alapszintű C#-és Java-topológiákat, valamint a Event Hubsekkel való munkát.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/27/2019
ms.openlocfilehash: 8dabfec18cb904fa72518428220991b817b53529
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98928927"
---
# <a name="example-apache-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>Példa Apache Storm-topológiák és-összetevők a HDInsight Apache Storm

A következő lista a Microsoft által létrehozott és karbantartott példákat mutatja be a HDInsight- [Apache Stormokkal](https://storm.apache.org/) való használatra. Ezek a példák számos témakörre vonatkoznak, amelyek alapszintű C#-és Java-topológiákat hoznak létre az Azure-szolgáltatások, például a Event Hubs, a Cosmos DB, a SQL Database, az [Apache HBase](https://hbase.apache.org/) a HDInsight és az Azure Storage használatához. Néhány példa azt is bemutatja, hogyan dolgozhat a nem Azure-beli, vagy akár nem Microsoft-technológiákkal, például a Signaler és a Socket.IO.

| Leírás | Útmutató ehhez: | Nyelv/keretrendszer |
|:--- |:--- |:--- |
| [Írás a Azure Data Lake Storageba Apache Storm](apache-storm-write-data-lake-store.md) |Írás a Azure Data Lake Storageba |Java |
| [Event hub kiöntő és bolt forrása](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Az Event hub kiöntő és a bolt forrása |Java |
| [Java-alapú topológiák fejlesztése a HDInsight-hez Apache Storm][5797064f] |Maven |Java |
| [C#-topológiák fejlesztése a Visual Studio használatával történő HDInsight Apache Stormhoz][16fce2d1] |A Visual studióhoz készült HDInsight-eszközök |C#, Java |
| [Események feldolgozása az Azure Event Hubs és az Apache Storm on HDInsight (C#) használatával][844d1d81] |Event Hubs |C# és Java |
| [Az Azure Event Hubs-eseményközpontok eseményeinek feldolgozása a HDInsight alatt futó Stormmal (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) |Event Hubs |Java |
| [Jármű-érzékelő adatainak feldolgozása a Event Hubs Apache Storm használatával a HDInsight][246ee964] |Event Hubs, Cosmos DB, Azure Storage Blob (WASB) |C#, Java |
| [Kinyerési, átalakítási és betöltési (ETL) az Azure Event Hubs az Apache HBase-be a HDInsight-Apache Storm használatával][b4b68194] |Event Hubs, HBase |C# |
| [Sablon C# Storm-topológiai projekt az Azure-szolgáltatásokkal való együttműködéshez Apache Storm on HDInsight][ce0c02a2] |Event Hubs, Cosmos DB, SQL Database, HBase, Signaler |C#, Java |
| [Skálázhatósági referenciaértékek az Azure Event Hubs való olvasáshoz a HDInsight Apache Storm használatával][d6c540e3] |Üzenet átviteli sebessége, Event Hubs, SQL Database |C#, Java |
| [Apache Kafka használata a HDInsight Apache Storm használatával](../hdinsight-apache-storm-with-kafka.md) | Apache Kafka Apache Storm olvasása és írása | Java |

> [!WARNING]  
> A listában szereplő C#-példák eredetileg Windows-alapú HDInsight lettek létrehozva és tesztelve, és előfordulhat, hogy nem működnek megfelelően a Linux-alapú HDInsight-fürtökkel. A Linux-alapú fürtök a Mono használatával futtatják a .NET-kódot, és kompatibilitási problémákba ütköznek a példában használt keretrendszerek és csomagok esetében.
>
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3,4-es vagy újabb verziójában használatos.

## <a name="python-only"></a>Csak Python

Lásd: a [Python és a Apache Storm használata a HDInsight](apache-storm-develop-python-topology.md) -ben a Flux-topológiával rendelkező Python-összetevőkre.

## <a name="next-steps"></a>Következő lépések

* [Apache Storm topológia létrehozása és figyelése az Azure HDInsight](./apache-storm-quickstart.md)
* [Megtudhatja, hogyan helyezhet üzembe és kezelhet Apache Storm-topológiákat a HDInsight Apache Storm használatával][6eb0d3b8]

[6eb0d3b8]:apache-storm-deploy-monitor-topology-linux.md "Ismerje meg, hogyan helyezheti üzembe és kezelheti a topológiákat a webalapú Apache Storm irányítópulton és a Storm felhasználói felületén vagy a Visual studióhoz készült HDInsight Tools használatával."
[16fce2d1]:apache-storm-develop-csharp-visual-studio-topology.md "Ismerje meg, hogyan hozhat létre C# Storm-topológiákat a Visual studióhoz készült HDInsight Tools használatával."
[5797064f]:apache-storm-develop-java-topology.md "Megtudhatja, hogyan hozhat létre Storm-topológiákat Java-ban a Maven használatával egy alapszintű WordCount-topológia létrehozásával."
[844d1d81]:apache-storm-develop-csharp-event-hub-topology.md "Megtudhatja, hogyan olvashatja és írhatja az Azure Event Hubsról származó adatokkal kapcsolatos HDInsight."
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Megtudhatja, hogyan használhatja a Storm-topológiát az Azure-Event Hubs üzenetek olvasására, a Azure Cosmos DB dokumentumok beolvasására az adatokra hivatkozó és az Azure Storage-ba való adatmentéssel."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Több topológia mutatja be az átviteli sebességet az Azure-Event Hubsről való olvasáskor, és a Apache Storm on HDInsight használatával történő tároláskor SQL Database."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Ismerje meg, hogyan olvashatja el az adatokat az Azure Event Hubsról, összesítheti & alakíthatja át az adatokat, majd a HDInsight HBase tárolhatja azokat."
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Ez a projekt a kiöntő, a boltokhoz és a topológiához tartozó sablonokat tartalmaz, amelyek különböző Azure-szolgáltatásokkal, például Event Hubsokkal, Cosmos DBekkel és SQL Databaseekkel működnek együtt."
