---
title: Kapcsolódjon a Apache Spark-alkalmazáshoz – Azure Event Hubs | Microsoft Docs
description: Ez a cikk tájékoztatást nyújt arról, hogyan használható a Apache Spark az Azure Event Hubs for Kafka szolgáltatással.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 84184ed3dffee97863b93c592d1cd577df313605
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92913738"
---
# <a name="connect-your-apache-spark-application-with-azure-event-hubs"></a>Apache Spark-alkalmazás összekötése az Azure-Event Hubs
Ez az oktatóanyag végigvezeti a Spark-alkalmazás a valós idejű folyamatos átvitelhez Event Hubs való csatlakoztatásának lépésein. Ez az integráció lehetővé teszi a streaming használatát anélkül, hogy módosítani kellene a protokoll ügyfelét, vagy futtatnia kell a saját Kafka-vagy Zookeeper-fürtöket. Ehhez az oktatóanyaghoz Apache Spark v 2.4 + és Apache Kafka 2.0 + verzió szükséges.

> [!NOTE]
> Ez a minta elérhető a [GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark/).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Event Hubs-névtér létrehozása
> * A példaprojekt klónozása
> * A Spark futtatása
> * Olvasás a Kafkához készült Event Hubsból
> * Írás a Kafkához készült Event Hubsba

## <a name="prerequisites"></a>Előfeltételek

Mielőtt nekikezdene az oktatóanyagnak, győződjön meg arról, hogy rendelkezik a következőkkel:
-   Egy Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).
-   [Apache Spark 2.4-es verziója](https://spark.apache.org/downloads.html)
-   [Apache Kafka 2.0-s verziója]( https://kafka.apache.org/20/documentation.html)
-   [Git](https://www.git-scm.com/downloads)

> [!NOTE]
> A Spark-Kafka adapter frissítve lett, hogy a Spark 2.4-es verziójától kezdődően támogassa a Kafka 2.0-s verzióját. A Spark korábbi kiadásaiban az adapter támogatta ugyan a Kafka 0.10-es és újabb verzióit, de elsősorban a Kafka 0.10-es verziójának API-jaira támaszkodott. Mivel a Kafkához készült Event Hubs nem támogatja a Kafka 0.10-es verzióját, az ökoszisztémája nem támogatja a 2.4-es előtti Spark-verziók Spark-Kafka adaptereit.


## <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása
Az Event Hubs-szolgáltatásokból való küldéshez és fogadáshoz szükség van egy Event Hubs-névtérre. A névtér és az Event hub létrehozásával kapcsolatos utasításokért tekintse meg az [Event hub létrehozása](event-hubs-create.md) című témakört. Szerezze be az Event Hubs kapcsolati sztringjét és teljes tartománynevét (FQDN) későbbi használatra. Útmutatásért lásd az [Event Hubs kapcsolati sztring lekérésével](event-hubs-get-connection-string.md) foglalkozó témakört. 

## <a name="clone-the-example-project"></a>A példaprojekt klónozása
Klónozza az Azure Event Hubs-adattárat, és keresse meg a `tutorials/spark` almappát:

```bash
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/spark
```

## <a name="read-from-event-hubs-for-kafka"></a>Olvasás a Kafkához készült Event Hubsból
Valamennyit módosítani kell a konfiguráció beállításain, hogy megkezdhesse a beolvasást a Kafkához készült Event Hubsból. Frissítse a **BOOTSTRAP_SERVERS** és az **EH_SASL** elemet a névtér részleteivel, és már meg is kezdheti a streamelést az Event Hubs segítségével, ugyanúgy, ahogy a Kafkával tenné. A teljes mintakódért tekintse meg a sparkConsumer.scala fájlt a GitHubon. 

```scala
//Read from your Event Hub!
val df = spark.readStream
    .format("kafka")
    .option("subscribe", TOPIC)
    .option("kafka.bootstrap.servers", BOOTSTRAP_SERVERS)
    .option("kafka.sasl.mechanism", "PLAIN")
    .option("kafka.security.protocol", "SASL_SSL")
    .option("kafka.sasl.jaas.config", EH_SASL)
    .option("kafka.request.timeout.ms", "60000")
    .option("kafka.session.timeout.ms", "30000")
    .option("kafka.group.id", GROUP_ID)
    .option("failOnDataLoss", "true")
    .load()

//Use dataframe like normal (in this example, write to console)
val df_write = df.writeStream
    .outputMode("append")
    .format("console")
    .start()
```

## <a name="write-to-event-hubs-for-kafka"></a>Írás a Kafkához készült Event Hubsba
Azt is megteheti, hogy a Kafka-íráshoz hasonlóan Event Hubs is ír. Ne felejtse el frissíteni a konfigurációt, és módosítani a **BOOTSTRAP_SERVERS** és az **EH_SASL** elemet az Event Hubs-névtér adataival.  A teljes mintakódért tekintse meg a sparkProducer.scala fájlt a GitHubon. 

```scala
df = /**Dataframe**/

//Write to your Event Hub!
df.writeStream
    .format("kafka")
    .option("topic", TOPIC)
    .option("kafka.bootstrap.servers", BOOTSTRAP_SERVERS)
    .option("kafka.sasl.mechanism", "PLAIN")
    .option("kafka.security.protocol", "SASL_SSL")
    .option("kafka.sasl.jaas.config", EH_SASL)
    .option("checkpointLocation", "./checkpoint")
    .start()
```



## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni a Kafka Event Hubséről és Event Hubsról, tekintse meg a következő cikkeket:  

- [Kafka-közvetítő tükrözése egy eseményközpontba](event-hubs-kafka-mirror-maker-tutorial.md)
- [Apache Flink csatlakoztatása egy eseményközponthoz](event-hubs-kafka-flink-tutorial.md)
- [A Kafka-kapcsolat integrálása az Event hubhoz](event-hubs-kafka-connect-tutorial.md)
- [További példák a GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Az Akka Streams csatlakoztatása eseményközponthoz](event-hubs-kafka-akka-streams-tutorial.md)
- [Apache Kafka fejlesztői útmutató az Azure-hoz Event Hubs](apache-kafka-developer-guide.md)

