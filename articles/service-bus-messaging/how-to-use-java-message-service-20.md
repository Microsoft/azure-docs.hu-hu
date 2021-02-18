---
title: A Java Message Service 2,0 API és a Azure Service Bus Premium használata
description: A Java Message Service (JMS) használata a Azure Service Bus
ms.topic: article
ms.date: 07/17/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: b7e4bf0ad69b6cd183296a7245ad3f720ced76c5
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652606"
---
# <a name="use-java-message-service-20-api-with-azure-service-bus-premium"></a>A Java Message Service 2,0 API és a Azure Service Bus Premium használata

Ez a cikk azt ismerteti, hogyan használhatja a népszerű **Java Message Service (JMS) 2,0** API-t a speciális üzenetsor-kezelési protokoll (AMQP 1,0) protokollal való Azure Service Bus interakcióhoz.

> [!NOTE]
> A Java Message Service (JMS) 2,0 API támogatása csak a **prémium szintű Azure Service Bus** érhető el.
>

## <a name="pre-requisites"></a>Előfeltételek

### <a name="get-started-with-service-bus"></a>A Service Bus használatának első lépései

Ez az útmutató azt feltételezi, hogy már rendelkezik Service Bus névtérrel. Ha nem, akkor a [névtér és a várólista](service-bus-create-namespace-portal.md) a [Azure Portal](https://portal.azure.com)használatával hozható létre. 

Service Bus névterek és várólisták létrehozásával kapcsolatos további információkért tekintse meg [a Service Bus queues Azure Portal használatával történő ismerkedést](service-bus-quickstart-portal.md)ismertető témakört.

### <a name="set-up-a-java-development-environment"></a>Java-fejlesztési környezet beállítása

A Java-alkalmazások fejlesztéséhez be kell állítania a megfelelő fejlesztési környezetet – 
   * A JDK (Java Development Kit) vagy a JRE (Java Runtime Environment) telepítve van.
   * A JDK vagy JRE hozzá lesz adva a létrehozási útvonalhoz és a megfelelő rendszerváltozóhoz.
   * A JDK-vagy JRE-t használó Java IDE települ. Például: Eclipse vagy IntelliJ.

Ha többet szeretne megtudni arról, hogyan készítheti elő a Java Azure-beli fejlesztői környezetét, használja [ezt az útmutatót](https://docs.microsoft.com/azure/developer/java/fundamentals/).

## <a name="what-jms-features-are-supported"></a>Milyen JMS funkciók támogatottak?

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

## <a name="downloading-the-java-message-service-jms-client-library"></a>A Java Message Service (JMS) ügyféloldali kódtár letöltése

Azure Service Bus Premium szinten elérhető összes funkció kihasználásához az alábbi könyvtárat hozzá kell adni a projekt Build elérési útjához.

[Azure-servicebus – JMS](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)

> [!NOTE]
> Ha hozzá szeretné adni az [Azure-servicebus-JMS](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) a Build elérési úthoz, használja az előnyben részesített függőségi kezelési eszközt a projekthez, például a [Maven](https://maven.apache.org/) vagy a [Gradle](https://gradle.org/).
>

## <a name="coding-java-applications"></a>Java-alkalmazások kódolása

A függőségek importálása után a Java-alkalmazások JMS-szolgáltatótól független módon is megírhatók.

### <a name="connecting-to-azure-service-bus-using-jms"></a>Csatlakozás a Azure Service Bushoz a JMS használatával

Ha a JMS-ügyfelekkel szeretne csatlakozni a Azure Service Bushoz, akkor az **elsődleges kapcsolati karakterlánc** alatt lévő [Azure Portal](https://portal.azure.com) "megosztott elérési szabályzatok" részében elérhető **kapcsolati sztringre** van szükség.

1. A `ServiceBusJmsConnectionFactorySettings`

    ```java
    ServiceBusJmsConnectionFactorySettings connFactorySettings = new ServiceBusJmsConnectionFactorySettings();
    connFactorySettings.setConnectionIdleTimeoutMS(20000);
    ```
2. Hozza létre a `ServiceBusJmsConnectionFactory` megfelelő példányát `ServiceBusConnectionString` .

    ```java
    String ServiceBusConnectionString = "<SERVICE_BUS_CONNECTION_STRING_WITH_MANAGE_PERMISSIONS>";
    ConnectionFactory factory = new ServiceBusJmsConnectionFactory(ServiceBusConnectionString, connFactorySettings);
    ```

3. A `ConnectionFactory` vagy a létrehozásához használja `Connection` a `Session` 

    ```java
    Connection connection = factory.createConnection();
    Session session = connection.createSession();
    ```
    vagy a `JMSContext` (JMS 2,0-ügyfelek esetében)

    ```java
    JMSContext jmsContext = factory.createContext();
    ```

    >[!IMPORTANT]
    > Bár hasonló névvel rendelkezik, a JMS "Session" és a Service Bus "Session" teljesen független egymástól.
    >
    > A JMS 1,1-ben a munkamenet az API alapvető építőeleme, amely lehetővé teszi a MessageProducer, a MessageConsumer és maga az üzenet létrehozását. További részletekért tekintse át a [JMS API programozási modellt](https://docs.oracle.com/javaee/6/tutorial/doc/bnceh.html)
    >
    > Service Bus a [munkamenetek](message-sessions.md) szolgáltatás-és ügyféloldali szerkezet, amely lehetővé teszi a FIFO-feldolgozást a várólistákon és előfizetéseken.
    >

### <a name="write-the-jms-application"></a>A JMS alkalmazás írása

A vagy a létrejötte után az `Session` `JMSContext` alkalmazás az ismerős JMS API-k használatával is végrehajthatja a felügyeleti és az adatműveleteket.

Tekintse át a [támogatott JMS-funkciók](how-to-use-java-message-service-20.md#what-jms-features-are-supported) listáját, és ellenőrizze, hogy mely API-k támogatottak.

Az alábbiakban néhány mintakód-kódrészletet talál a JMS használatába

#### <a name="sending-messages-to-a-queue-and-topic"></a>Üzenetek küldése egy várólistára és témakörbe

```java
// Create the queue and topic
Queue queue = jmsContext.createQueue("basicQueue");
Topic topic = jmsContext.createTopic("basicTopic");
// Create the message
Message msg = jmsContext.createMessage();

// Create the JMS message producer
JMSProducer producer = jmsContext.createProducer();

// send the message to the queue
producer.send(queue, msg);
// send the message to the topic
producer.send(topic, msg);
```

#### <a name="receiving-messages-from-a-queue"></a>Üzenetek fogadása egy várólistából

```java
// Create the queue
Queue queue = jmsContext.createQueue("basicQueue");

// Create the message consumer
JMSConsumer consumer = jmsContext.createConsumer(queue);

// Receive the message
Message msg = (Message) consumer.receive();
```

#### <a name="receiving-messages-from-a-shared-durable-subscription-on-a-topic"></a>Üzenetek fogadása egy megosztott tartós előfizetésből egy témakörből

```java
// Create the topic
Topic topic = jmsContext.createTopic("basicTopic");

// Create a shared durable subscriber on the topic
JMSConsumer sharedDurableConsumer = jmsContext.createSharedDurableConsumer(topic, "sharedDurableConsumer");

// Receive the message
Message msg = (Message) sharedDurableConsumer.receive();
```

## <a name="summary"></a>Összefoglalás

Ez az útmutató bemutatja, hogyan használhatók a Java-üzenetküldést (JMS) használó Java-ügyfélalkalmazások a AMQP 1,0-mel a Azure Service Bus használatával.

Más nyelvekről is használhat Service Bus AMQP 1,0-et, beleértve a .NET, a C, a Python és a PHP-t is. Az ezekkel a különböző nyelvekkel létrehozott összetevők megbízhatóan és teljes hűséggel cserélhetik az üzeneteket a Service Bus AMQP 1,0-támogatásának használatával.

## <a name="next-steps"></a>Következő lépések

A Azure Service Bus és a Java Message Service-(JMS-) entitásokkal kapcsolatos további információkért tekintse meg az alábbi hivatkozásokat – 
* [Service Bus – várólisták, témakörök és előfizetések](service-bus-queues-topics-subscriptions.md)
* [Service Bus – Java Message Service-entitások](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities)
* [AMQP 1,0-támogatás Azure Service Bus](service-bus-amqp-overview.md)
* [Service Bus AMQP 1,0 fejlesztői útmutató](service-bus-amqp-dotnet.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [Java Message Service API (külső Oracle doc)](https://docs.oracle.com/javaee/7/api/javax/jms/package-summary.html)
* [Ismerje meg, hogyan migrálhat a ActiveMQ-ről Service Bus](migrate-jms-activemq-to-servicebus.md)
