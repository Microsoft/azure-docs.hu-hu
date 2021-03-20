---
title: A Apache Kafka-Azure Event Hubs-hoz készült beadási streamek használata | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan csatlakoztathatók az Azure Event hub-ba a kiszolgált adatfolyamok.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 92ab927189329493696c70b61ffc7f11cad22a66
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92369573"
---
# <a name="using-akka-streams-with-event-hubs-for-apache-kafka"></a>Az Akka Streams használata az Apache Kafkához készült Event Hubs szolgáltatással

Ebből az oktatóanyagból megtudhatja, hogyan csatlakoztathatók a Event Hubs-támogatás a Apache Kafkahoz a protokoll-ügyfelek módosítása vagy a saját fürtök futtatása nélkül. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Event Hubs-névtér létrehozása
> * A példaprojekt klónozása
> * A kiállítók futtatása 
> * A bevezetési streamek fogyasztójának futtatása

> [!NOTE]
> Ez a minta elérhető a [GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik a következő előfeltételekkel:

* Olvassa át az [Apache Kafkához készült Event Hubsot](event-hubs-for-kafka-ecosystem-overview.md) ismertető cikket. 
* Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), mielőtt hozzákezd.
* [Java fejlesztői készlet (JDK) 1.8+](/azure/developer/java/fundamentals/java-jdk-long-term-support)
    * Ubuntu rendszeren futtassa az `apt-get install default-jdk` parancsot a JDK telepítéséhez.
    * Ügyeljen arra, hogy a JAVA_HOME környezeti változó arra a mappára mutasson, ahová a JDK telepítve lett.
* [Maven](https://maven.apache.org/download.cgi) bináris archívum [letöltése](https://maven.apache.org/install.html) és telepítése
    * Ubuntu rendszeren futtathatja az `apt-get install maven` parancsot a Maven telepítéséhez.
* [Git](https://www.git-scm.com/downloads)
    * Ubuntu rendszeren futtathatja a `sudo apt-get install git` parancsot a Git telepítéséhez.

## <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása

Bármely Event Hubs szolgáltatásból történő küldéshez vagy fogadáshoz Event Hubs névtér szükséges. További információt az [Event hub létrehozása](event-hubs-create.md) című témakörben talál. Ügyeljen arra, hogy későbbi használatra másolja a Event Hubs-kapcsolódási karakterláncot.

## <a name="clone-the-example-project"></a>A példaprojekt klónozása

Most, hogy rendelkezik egy Event Hubs kapcsolódási karakterlánccal, klónozott az Azure-Event Hubs a Kafka-tárházhoz, és navigáljon az `akka` almappába:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/akka/java
```

## <a name="run-akka-streams-producer"></a>A kiállítók futtatása

A megadott beadási streamek gyártói példájának használatával üzeneteket küldhet a Event Hubs szolgáltatásnak.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Event Hubs Kafka-végpont megadása

#### <a name="producer-applicationconf"></a>Producer Application. conf

A `bootstrap.servers` és a `sasl.jaas.config` értékének frissítésével `producer/src/main/resources/application.conf` irányítsa a gyártót a Event Hubs Kafka-végpontra a megfelelő hitelesítéssel.

```xml
akka.kafka.producer {
    #Akka Kafka producer properties can be defined here


    # Properties defined by org.apache.kafka.clients.producer.ProducerConfig
    # can be defined in this configuration section.
    kafka-clients {
        bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
        sasl.mechanism=PLAIN
        security.protocol=SASL_SSL
        sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

> [!IMPORTANT]
> Cserélje le a helyére `{YOUR.EVENTHUBS.CONNECTION.STRING}` a Event Hubs névtérhez tartozó kapcsolatok karakterláncát. A kapcsolatok karakterláncának beolvasásával kapcsolatos utasításokért lásd: [Event Hubs-kapcsolatok karakterláncának beolvasása](event-hubs-get-connection-string.md). Íme egy példa a konfigurációra: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`


### <a name="run-producer-from-the-command-line"></a>A gyártó futtatása a parancssorból

Ha a parancssorból szeretné futtatni a gyártót, a JAR-t, majd a Mavenből futtatva futtassa a jar-t, majd futtassa a-t a Maven használatával, majd futtassa a Java-ban a szükséges Kafka-JAR (ok) hozzáadásával a osztályútvonal):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

A gyártó megkezdi az események küldését az Event hub számára a témakörben `test` , és kinyomtatja az eseményeket az stdout-ba.

## <a name="run-akka-streams-consumer"></a>A bevezetési streamek fogyasztójának futtatása

A megadott fogyasztói példa használatával fogadhat üzeneteket az Event hub-ból.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Event Hubs Kafka-végpont megadása

#### <a name="consumer-applicationconf"></a>Consumer Application. conf

A `bootstrap.servers` és a `sasl.jaas.config` értékének frissítésével `consumer/src/main/resources/application.conf` irányítsa a fogyasztót a Event Hubs Kafka-végpontra a megfelelő hitelesítéssel.

```xml
akka.kafka.consumer {
    #Akka Kafka consumer properties defined here
    wakeup-timeout=60s

    # Properties defined by org.apache.kafka.clients.consumer.ConsumerConfig
    # defined in this configuration section.
    kafka-clients {
       request.timeout.ms=60000
       group.id=akka-example-consumer

       bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
       sasl.mechanism=PLAIN
       security.protocol=SASL_SSL
       sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

> [!IMPORTANT]
> Cserélje le a helyére `{YOUR.EVENTHUBS.CONNECTION.STRING}` a Event Hubs névtérhez tartozó kapcsolatok karakterláncát. A kapcsolatok karakterláncának beolvasásával kapcsolatos utasításokért lásd: [Event Hubs-kapcsolatok karakterláncának beolvasása](event-hubs-get-connection-string.md). Íme egy példa a konfigurációra: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`


### <a name="run-consumer-from-the-command-line"></a>A felhasználó futtatása a parancssorból

Ha a parancssorból szeretné futtatni a fogyasztót, adja ki a JAR-t, majd futtassa a Mavenből (vagy a Maven használatával a JAR-t használva, majd futtassa a Java-ban a szükséges Kafka-JAR (ok) hozzáadásával a osztályútvonal):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

Ha az Event hub eseményeivel rendelkezik (például ha a gyártó is fut), akkor a fogyasztó megkezdi az események fogadását a témakörből `test` . 

Tekintse meg a bevezetési [Streams Kafka-útmutatót](https://doc.akka.io/docs/akka-stream-kafka/current/home.html) , ahol részletesebb információkat találhat a bevezető streamekről.

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni a Kafka-Event Hubsről, tekintse meg a következő cikkeket:  

- [Kafka-közvetítő tükrözése egy eseményközpontba](event-hubs-kafka-mirror-maker-tutorial.md)
- [Apache Spark csatlakoztatása egy eseményközponthoz](event-hubs-kafka-spark-tutorial.md)
- [Apache Flink csatlakoztatása egy eseményközponthoz](event-hubs-kafka-flink-tutorial.md)
- [A Kafka-kapcsolat integrálása az Event hubhoz](event-hubs-kafka-connect-tutorial.md)
- [További példák a GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Apache Kafka fejlesztői útmutató az Azure-hoz Event Hubs](apache-kafka-developer-guide.md)