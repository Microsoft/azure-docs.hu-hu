---
title: Apache Kafka fejlesztői útmutató Event Hubs
description: Ez a cikk a Kafka-alkalmazások Azure-Event Hubs való integrálását ismertető cikkekre mutató hivatkozásokat tartalmaz.
ms.date: 06/23/2020
ms.topic: article
ms.openlocfilehash: 84fa40d7cc4b09f31d78665f901b49adacf3267f
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303083"
---
# <a name="apache-kafka-developer-guide-for-azure-event-hubs"></a>Apache Kafka fejlesztői útmutató az Azure-hoz Event Hubs
Ez a cikk a Apache Kafka-alkalmazások Azure-Event Hubs való integrálását ismertető cikkekre mutató hivatkozásokat tartalmaz. 

## <a name="overview"></a>Áttekintés
A Event Hubs egy Kafka-végpontot biztosít, amelyet a meglévő Kafka-alapú alkalmazásai használhatnak a saját Kafka-fürt futtatására. A Event Hubs számos meglévő Kafka-alkalmazással működik. További információ: [Event Hubs Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)

## <a name="quickstarts"></a>Rövid útmutatók
Itt megtalálhatja a GitHubon található gyors útmutatókat, és ebben a tartalmi készletben gyorsan felhasználhatja a Kafka-Event Hubs.

### <a name="quickstarts-in-github"></a>Rövid útmutatók a GitHubon
Tekintse meg az alábbi rövid útmutatókat az **Azure-Event-hubok-for-Kafka** tárházban: 

| Ügyfél nyelve/keretrendszere | Description | 
| ------------------------- | ----------- | 
| [.NET](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/dotnet) | <p>Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre és csatlakozhat egy Event Hubs Kafka-végponthoz egy, a .NET Core 2,0-t használó, C# nyelven írt gyártóval és fogyasztóval.</p><p>Ez a minta a [Apache Kafka .net-ügyfélen](https://github.com/confluentinc/confluent-kafka-dotnet)alapul, amelyet a rendszer a Kafka-Event Hubs való használatra módosít.</p> | 
| [Java](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java) | Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre és csatlakozhat egy Event Hubs Kafka-végponthoz egy, a Java-ban írt példát gyártó és fogyasztó használatával. |
| [Node.js](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/node) | <p>Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre és csatlakozhat egy Event Hubs Kafka-végponthoz egy, a csomópontban írt gyártó és fogyasztó használatával.</p><p>Ez a példa a [Node-rdkafka](https://github.com/Blizzard/node-rdkafka) könyvtárat használja. </p>| 
| [Python](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/python) | <p>Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre és csatlakozhat egy Event Hubs Kafka-végponthoz egy, a Pythonban írt példát használó gyártóval és fogyasztóval.</p><p>Ez a minta a [Apache Kafka Python-ügyfélen](https://github.com/confluentinc/confluent-kafka-python)alapul, amely a Kafka-Event Hubs való használatra lett módosítva.</p>|
| [Ugrás](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go) | <p>Ez a rövid útmutató bemutatja, hogyan hozhat létre és csatlakozhat egy Event Hubs Kafka-végponthoz egy példaként írt gyártó és fogyasztó használatával.</p><p>Ez a minta a [Apache Kafka Golang-ügyfélen](https://github.com/confluentinc/confluent-kafka-go)alapul, amely a Kafka-Event Hubs való használatra lett módosítva.</p>| 
| [Sarama Kafka – ugrás](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go-sarama-client) | Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre és csatlakozhat egy Event Hubs Kafka-végponthoz egy, a [Sarama Kafka ügyféloldali](https://github.com/Shopify/sarama) kódtár használatával a go-val írt gyártóval és fogyasztóval. |
| [Kafka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafka-cli) | Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre és csatlakozhat egy Event Hubs Kafka-végponthoz a Apache Kafka-eloszlással csomagban lévő CLI használatával.| 
| [Kafkacat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) | a kafkacat egy nem JVM parancssori fogyasztó és gyártó, amely a librdkafka és a kis helyigény miatt népszerű. Ez a rövid útmutató egy minta konfigurációt és számos egyszerű minta kafkacat-parancsot tartalmaz. | 
 
### <a name="quickstarts-in-docs"></a>Rövid útmutatók a DOCS-ban
Tekintse meg a gyors üzembe helyezési útmutatót: a [Event Hubs a Kafka protokoll használatával](event-hubs-quickstart-kafka-enabled-event-hubs.md) történő adatfolyam-továbbítást ebben a tartalomtípusban, amely részletes útmutatást nyújt a Event Hubs való továbbításhoz. Megtudhatja, hogyan használhatja a termelőket és a fogyasztókat, hogy az alkalmazásokban csak egy konfigurációs módosítással beszéljen Event Hubs. 


## <a name="tutorials"></a>Oktatóanyagok 

### <a name="tutorials-in-github"></a>Oktatóanyagok a GitHubban
Tekintse meg az alábbi oktatóanyagokat a GitHubon:

| Oktatóanyag | Description | 
| ------------------------- | ----------- | 
| [Akka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java) | Ebből az oktatóanyagból megtudhatja, hogyan csatlakoztathatók a-beli és a Kafka-kompatibilis Event Hubs a protokoll-ügyfelek módosítása vagy a saját fürtök futtatása nélkül. A **Java** és a **Scala** programozási nyelve két külön oktatóanyagot használ. | 
| [Kapcsolódás](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect) | Ez a dokumentum végigvezeti Önt a Kafka-csatlakozás Azure-Event Hubs való integrálásán, valamint az alapszintű FileStreamSource-és FileStreamSink-összekötők üzembe helyezésén. Habár ezek az összekötők nem éles használatra készültek, egy teljes körű Kafka-kapcsolódási forgatókönyvet mutatnak be, amelyben az Azure Event Hubs egy Kafka-közvetítőként álarcos.| 
| [Filebeat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/filebeat) | Ez a dokumentum végigvezeti Önt a Filebeat és Event Hubs integrálásán a Filebeat Kafka-kimenetén keresztül. | 
| [Flink](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink) | Ebből az oktatóanyagból megtudhatja, hogyan csatlakoztatható az Apache flink a Kafka-kompatibilis Event Hubshoz a protokoll-ügyfelek módosítása vagy a saját fürtök futtatása nélkül. | 
| [FluentD](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/fluentd) | Ez a dokumentum bemutatja, hogyan integrálhatja a folyékonyan és Event Hubst a `out_kafka` kimeneti beépülő modullal. |
| [Interop](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/interop) | Ebből az oktatóanyagból megtudhatja, hogyan cserélhet be különböző protokollokat használó ügyfelek és gyártók közötti eseményeket. |
| [LogStash](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/logstash) | Ez az oktatóanyag végigvezeti a Logstash és a Kafka-kompatibilis Event Hubs a Logstash Kafka bemeneti/kimeneti beépülő modullal történő integrálásán. | 
| [MirrorMaker](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker) | Ez az oktatóanyag azt mutatja be, hogy az Event hub és a Kafka MirrorMaker hogyan integrálhat egy meglévő Kafka-folyamatot az Azure-ba a Event Hubs szolgáltatásban található Kafka bemeneti stream tükrözésével. |
| [NiFi](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/nifi) | Ez az oktatóanyag bemutatja, hogyan csatlakoztatható az Apache NiFi egy Event Hubs névtérhez. | 
| [OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth) | A gyors útmutatók bemutatják, hogyan hozhat létre és csatlakozhat egy Event Hubs Kafka-végponthoz egy go-és Java-programozási nyelveken írt példát előállító és fogyasztó használatával. |
| [Fluent séma beállításjegyzéke](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/schema-registry) | Ez az oktatóanyag végigvezeti a séma beállításjegyzékének és a Kafka-Event Hubs integrálásának lépésein. | 
| [Spark](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark) | Ez az oktatóanyag bemutatja, hogyan csatlakoztathatja a Spark-alkalmazást egy Event hubhoz a protokoll-ügyfelek módosítása vagy a saját Kafka-fürtök futtatása nélkül. | 

### <a name="tutorials-in-docs"></a>Oktatóanyagok a DOCS-ban
Tekintse meg a következő oktatóanyagot: [Apache Kafka feldolgozása Event Hubs eseményekhez a stream Analytics használatával](event-hubs-kafka-stream-analytics.md) ebben a tartalomtípusban, amely bemutatja, hogyan továbbíthatja az adatokat a Event Hubsba, és feldolgozhatja azokat Azure stream Analyticsekkel.

## <a name="how-to-guides"></a>Útmutatók
A dokumentációban a következő útmutatók olvashatók:

| Cikk | Description | 
| ------- | ----------- | 
| [Kafka-közvetítő tükrözése egy eseményközpontba](event-hubs-kafka-mirror-maker-tutorial.md) | Bemutatja, hogyan tükrözött egy Kafka-közvetítő egy Event hub-ban a Kafka MirrorMaker használatával. |
| [Apache Spark csatlakoztatása egy eseményközponthoz](event-hubs-kafka-spark-tutorial.md) | Végigvezeti a Spark-alkalmazás a valós idejű folyamatos átvitelhez való csatlakoztatásának Event Hubsán. |
| [Apache Flink csatlakoztatása egy eseményközponthoz](event-hubs-kafka-flink-tutorial.md) | Bemutatja, hogyan csatlakoztathatja az Apache flink egy Event hubhoz a protokoll-ügyfelek módosítása vagy a saját fürtök futtatása nélkül. |
| [Apache Kafka-kapcsolat integrálása az Event hub-nal (előzetes verzió)](event-hubs-kafka-connect-tutorial.md) | Végigvezeti a Kafka-csatlakozás esemény-hubhoz való integrálásán és az alapszintű FileStreamSource és FileStreamSink-összekötők üzembe helyezésén. |
| [Az Akka Streams csatlakoztatása eseményközponthoz](event-hubs-kafka-akka-streams-tutorial.md) | Azt mutatja be, hogyan csatlakoztathat a kisegítő adatfolyamokat egy Event hubhoz a protokoll-ügyfelek módosítása vagy a saját fürtök futtatása nélkül. |
| [A Spring boot Starter for Apache Kafka használata az Azure-ban Event Hubs](/azure/developer/java/spring-framework/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub) | Bemutatja, hogyan konfigurálhat egy, a Spring boot Inicializálással létrehozott Java-alapú Spring Cloud stream-kötést Apache Kafka Azure Event Hubs használatával. |

## <a name="next-steps"></a>Következő lépések
Tekintse át a GitHub [-adattár Azure-Event-hubok-for-Kafka](https://github.com/Azure/azure-event-hubs-for-kafka) -mintáit a gyors útmutató és oktatóanyagok mappákban.

Továbbá tekintse meg a következő cikkeket:

- [Event Hubs Apache Kafka hibaelhárítási útmutatója](apache-kafka-troubleshooting-guide.md)
- [Gyakori kérdések – Event Hubs Apache Kafka](apache-kafka-frequently-asked-questions.yml)
- [A Event Hubs Apache Kafka áttelepítési útmutatója](apache-kafka-migration-guide.md)



