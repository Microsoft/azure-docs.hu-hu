---
title: 'Gyors útmutató: az Azure Event Hubs-val folytatott adatfolyamok a Kafka protokoll használatával'
description: 'Gyors útmutató: Ez a cikk az Azure-Event Hubs a Kafka protokoll és az API-k használatával történő továbbításával kapcsolatos információkat tartalmazza.'
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 2020534a3984453bcd6eff7ad0f5c02d9e7a29ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92368349"
---
# <a name="quickstart-data-streaming-with-event-hubs-using-the-kafka-protocol"></a>Gyors útmutató: adatstream Event Hubs a Kafka protokoll használatával
Ebből a rövid útmutatóból megtudhatja, hogyan továbbíthat Event Hubs a protokoll-ügyfelek módosítása vagy a saját fürtök futtatása nélkül. Megtudhatja, hogyan használhatja a termelőket és a fogyasztókat, hogy az alkalmazásokban csak egy konfigurációs módosítással beszéljen Event Hubs. 

> [!NOTE]
> Ez a minta elérhető a [GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java).

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy teljesülnek az alábbi előfeltételek:

* Olvassa át az [Apache Kafkához készült Event Hubsot](event-hubs-for-kafka-ecosystem-overview.md) ismertető cikket.
* Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), mielőtt hozzákezd.
* [Java fejlesztői készlet (JDK) 1.7+](/azure/developer/java/fundamentals/java-jdk-long-term-support).
* [Töltse le](https://maven.apache.org/download.cgi) és [telepítse](https://maven.apache.org/install.html) a Maven bináris archívumát.
* [Git](https://www.git-scm.com/)


## <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása
A **standard** szintű Event Hubs névtér létrehozásakor a rendszer automatikusan engedélyezi a névtérhez tartozó Kafka-végpontot. A Kafka protokollt használó alkalmazásokból olyan eseményeket lehet továbbítani, amelyek standard szintű Event Hubs. A **standard** szintű Event Hubs névtér létrehozásához kövesse az [Event hub létrehozása a Azure Portal használatával](event-hubs-create.md) című témakör részletes utasításait. 

> [!NOTE]
> A Kafka-Event Hubs csak a **standard** és a **dedikált** szinteken érhető el. Az alapszintű **csomag** nem támogatja a Kafka használatát Event Hubson.

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Üzenetek küldése és fogadása a Kafkával az Event Hubsban

1. Klónozza a [Kafkához készült Event Hubs-adattárat](https://github.com/Azure/azure-event-hubs-for-kafka).

2. Nyissa meg a `azure-event-hubs-for-kafka/quickstart/java/producer` címet.

3. Az alábbiakban látható módon módosítsa az előállító konfigurációs adatait az `src/main/resources/producer.config` fájlban:

    **TLS/SSL:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
    
    > [!IMPORTANT]
    > Cserélje le a helyére `{YOUR.EVENTHUBS.CONNECTION.STRING}` a Event Hubs névtérhez tartozó kapcsolatok karakterláncát. A kapcsolatok karakterláncának beolvasásával kapcsolatos utasításokért lásd: [Event Hubs-kapcsolatok karakterláncának beolvasása](event-hubs-get-connection-string.md). Íme egy példa a konfigurációra: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`

    **OAuth**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ```    

    A CustomAuthenticateCallbackHandler a GitHub-osztály forráskódját [itt](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/producer/src/main/java)találja.
4. A termelői kód és a stream eseményeinek futtatása a Event Hubsba:
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
    
5. Nyissa meg a `azure-event-hubs-for-kafka/quickstart/java/consumer` címet.

6. Az alábbiakban látható módon módosítsa a fogyasztó konfigurációs adatait az `src/main/resources/consumer.config` fájlban:
   
    **TLS/SSL:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

    > [!IMPORTANT]
    > Cserélje le a helyére `{YOUR.EVENTHUBS.CONNECTION.STRING}` a Event Hubs névtérhez tartozó kapcsolatok karakterláncát. A kapcsolatok karakterláncának beolvasásával kapcsolatos utasításokért lásd: [Event Hubs-kapcsolatok karakterláncának beolvasása](event-hubs-get-connection-string.md). Íme egy példa a konfigurációra: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`

    **OAuth**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ``` 

    A CustomAuthenticateCallbackHandler a GitHub-osztály forráskódját [itt](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/consumer/src/main/java)találja.

    [Itt](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth)megtalálhatja az Event Hubs for Kafka összes OAuth-mintáját.
7. Futtassa a fogyasztói kódot, és dolgozza fel az Event hub eseményeit a Kafka-ügyfelek használatával:

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Ha az Event Hubs Kafka-fürtön vannak események, most el kell kezdeniük érkezni a fogyasztóról.

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben megtanulta, hogyan végezheti el a Event Hubs továbbítását a protokoll-ügyfelek módosítása vagy a saját fürtök futtatása nélkül. További információ: [Apache Kafka fejlesztői útmutató az Azure Event Hubshoz](apache-kafka-developer-guide.md).