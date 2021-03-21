---
title: Apache Kafka MirrorMaker használata – Azure Event Hubs | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan használhatja a Kafka-MirrorMaker a Kafka-fürtök AzureEvent-Hubokban való tükrözéséhez.
ms.topic: how-to
ms.date: 01/04/2021
ms.openlocfilehash: 654e9e19dfde0d0c58d00e41cf8ab0ba8e1484d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97860994"
---
# <a name="use-apache-kafka-mirrormaker-with-event-hubs"></a>Apache Kafka MirrorMaker használata Event Hubs

Ez az oktatóanyag bemutatja, hogyan tükrözheti a Kafka-közvetítőt egy Azure Event hub-ban a Kafka MirrorMaker használatával. Ha a CNCF Strimzi operátor használatával Apache Kafka üzemelteti a Kubernetes-t, akkor a [blogbejegyzésben](https://strimzi.io/blog/2020/06/09/mirror-maker-2-eventhub/) találhatja meg a Kafka használatát a Strimzi és a Mirror Maker 2 segítségével. 

   ![Kafka-MirrorMaker Event Hubs](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

> [!NOTE]
> Ez a minta elérhető a [GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker).

> [!NOTE]
> Ez a cikk az *engedélyezési* feltételekre mutató hivatkozásokat tartalmaz, amelyek egy kifejezés, amelyet a Microsoft már nem használ. Ha a rendszer eltávolítja a kifejezést a szoftverből, azt a cikkből távolítjuk el.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Event Hubs-névtér létrehozása
> * A példaprojekt klónozása
> * Kafka-fürt beállítása
> * Kafka-MirrorMaker konfigurálása
> * A Kafka MirrorMaker futtatása

## <a name="introduction"></a>Bevezetés
Ez az oktatóanyag azt mutatja be, hogy az Event hub és a Kafka MirrorMaker hogyan integrálhat egy meglévő Kafka-folyamatot az Azure-ba a "tükrözés" a Event Hubs-szolgáltatásban található Kafka bemeneti streamtel, amely lehetővé teszi Apache Kafka streamek integrálását több [összevonási minta](event-hubs-federation-overview.md)használatával. 

Az Azure Event Hubs Kafka-végpont lehetővé teszi, hogy a Kafka-protokoll (azaz Kafka-ügyfelek) használatával kapcsolódjon az Azure Event Hubshoz. Egy Kafka-alkalmazás minimális módosításaival csatlakozhat az Azure Event Hubshoz, és élvezheti az Azure-ökoszisztéma előnyeit. A Event Hubs jelenleg Apache Kafka 1,0-es és újabb verziókban támogatja a protokollt.

A Apache Kafka MirrorMaker 1 központilag Apache Kafkaról Event Hubsre is használhatja. A 2. MirrorMaker mindkét irányban használható, de a (MirrorMaker) 2. verziójában [ `MirrorCheckpointConnector` `MirrorHeartbeatConnector` konfigurálható](https://cwiki.apache.org/confluence/display/KAFKA/KIP-382%3A+MirrorMaker+2.0) , hogy mindkettőt úgy kell konfigurálni, hogy az Apache Kafka-közvetítőre mutasson, és ne Event Hubs. Ez az oktatóanyag az 1. MirrorMaker konfigurálását mutatja be.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik a következőkkel:

* Olvassa át az [Apache Kafkához készült Event Hubsot](event-hubs-for-kafka-ecosystem-overview.md) ismertető cikket. 
* Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), mielőtt hozzákezd.
* [Java Development Kit (JDK) 1.7 +](/azure/developer/java/fundamentals/java-jdk-long-term-support)
    * Ubuntu rendszeren futtassa az `apt-get install default-jdk` parancsot a JDK telepítéséhez.
    * Ügyeljen arra, hogy a JAVA_HOME környezeti változó arra a mappára mutasson, ahová a JDK telepítve lett.
* [Maven](https://maven.apache.org/download.cgi) bináris archívum [letöltése](https://maven.apache.org/install.html) és telepítése
    * Ubuntu rendszeren futtathatja az `apt-get install maven` parancsot a Maven telepítéséhez.
* [Git](https://www.git-scm.com/downloads)
    * Ubuntu rendszeren futtathatja a `sudo apt-get install git` parancsot a Git telepítéséhez.

## <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása

Az Event Hubs-szolgáltatásokból való küldéshez és fogadáshoz szükség van egy Event Hubs-névtérre. A névtér és az Event hub létrehozásával kapcsolatos utasításokért tekintse meg az [Event hub létrehozása](event-hubs-create.md) című témakört. Ügyeljen arra, hogy későbbi használatra másolja a Event Hubs-kapcsolódási karakterláncot.

## <a name="clone-the-example-project"></a>A példaprojekt klónozása

Most, hogy rendelkezik egy Event Hubs kapcsolódási karakterlánccal, klónozott az Azure-Event Hubs a Kafka-tárházhoz, és navigáljon az `mirror-maker` almappába:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Kafka-fürt beállítása

A [Kafka gyors útmutatója](https://kafka.apache.org/quickstart) segítségével beállíthat egy fürtöt a kívánt beállításokkal (vagy használhat egy meglévő Kafka-fürtöt).

## <a name="configure-kafka-mirrormaker"></a>Kafka-MirrorMaker konfigurálása

A Kafka MirrorMaker lehetővé teszi egy adatfolyam "tükrözését". A forrás-és a cél Kafka-fürtök esetében a MirrorMaker biztosítja, hogy a forrás-és a cél fürtök is megkapják a forrásoldali fürtnek küldött üzeneteket. Ebből a példából megtudhatja, hogyan tükrözheti a forrás Kafka-fürtöt egy cél Event hub használatával. Ez a forgatókönyv felhasználható egy meglévő Kafka-folyamat adatainak küldésére Event Hubs az adatáramlás megszakítása nélkül. 

A Kafka-MirrorMaker kapcsolatos részletes információkért lásd a [Kafka tükrözési/MirrorMaker útmutatóját](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330).

A Kafka-MirrorMaker konfigurálásához adjon neki egy Kafka-fürtöt fogyasztóként/forrásként, valamint egy Event hub-t, amely a gyártója/célja.

#### <a name="consumer-configuration"></a>Fogyasztói konfiguráció

Frissítse a fogyasztói konfigurációs fájlt `source-kafka.config` , amely tájékoztatja a MirrorMaker a forrás Kafka-fürt tulajdonságairól.

##### <a name="source-kafkaconfig"></a>source-kafka.config

```
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Producer konfigurációja

Most frissítse a producer konfigurációs fájlját `mirror-eventhub.config` , amely azt jelzi, hogy a MirrorMaker küldi a duplikált (vagy "tükrözött") adattípust a Event Hubs szolgáltatásnak. Pontosabban módosítsa `bootstrap.servers` és `sasl.jaas.config` mutasson a Event Hubs Kafka-végpontra. A Event Hubs szolgáltatásnak biztonságos (SASL) kommunikációra van szüksége, amely a következő konfiguráció utolsó három tulajdonságának beállításával érhető el: 

##### <a name="mirror-eventhubconfig"></a>mirror-eventhub.config

```
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!IMPORTANT]
> Cserélje le a helyére `{YOUR.EVENTHUBS.CONNECTION.STRING}` a Event Hubs névtérhez tartozó kapcsolatok karakterláncát. A kapcsolatok karakterláncának beolvasásával kapcsolatos utasításokért lásd: [Event Hubs-kapcsolatok karakterláncának beolvasása](event-hubs-get-connection-string.md). Íme egy példa a konfigurációra: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`

## <a name="run-kafka-mirrormaker"></a>A Kafka MirrorMaker futtatása

Futtassa a Kafka MirrorMaker szkriptet a root Kafka-könyvtárból az újonnan frissített konfigurációs fájlok használatával. Győződjön meg arról, hogy a konfigurációs fájlokat másolja a legfelső szintű Kafka-könyvtárba, vagy frissítse az elérési útját a következő parancsban.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Annak ellenőrzéséhez, hogy az események elérik-e az Event hub-t, tekintse meg a bejövő statisztikát a [Azure Portal](https://azure.microsoft.com/features/azure-portal/), vagy futtasson egy fogyasztót az Event hub-on.

Ha a MirrorMaker fut, a rendszer a Kafka-fürtnek küldött összes eseményt fogadja a Kafka-fürt és a tükrözött Event hub között. A MirrorMaker és egy Event Hubs Kafka-végpont használatával áttelepítheti a meglévő Kafka-folyamatokat a felügyelt Azure Event Hubs szolgáltatásba anélkül, hogy módosítaná a meglévő fürtöt, vagy meg kellene szakítania a folyamatban lévő adatfolyamot.

## <a name="samples"></a>Példák
Tekintse meg a következő mintákat a GitHubon:

- [Mintakód ehhez az oktatóanyaghoz a GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)
- [Azure Event Hubs Kafka-MirrorMaker Azure Container-példányon futtatva](https://github.com/djrosanova/EventHubsMirrorMaker)

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni a Kafka-Event Hubsről, tekintse meg a következő cikkeket:  

- [Apache Spark csatlakoztatása egy eseményközponthoz](event-hubs-kafka-spark-tutorial.md)
- [Apache Flink csatlakoztatása egy eseményközponthoz](event-hubs-kafka-flink-tutorial.md)
- [A Kafka-kapcsolat integrálása az Event hubhoz](event-hubs-kafka-connect-tutorial.md)
- [További példák a GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Az Akka Streams csatlakoztatása eseményközponthoz](event-hubs-kafka-akka-streams-tutorial.md)
- [Apache Kafka fejlesztői útmutató az Azure-hoz Event Hubs](apache-kafka-developer-guide.md)