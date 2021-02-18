---
title: Azure Service Bus JMS 2,0 – fejlesztői útmutató
description: A Java Message Service (JMS) 2,0 API használata a Azure Service Bussal való kommunikációhoz
ms.topic: article
ms.date: 01/17/2021
ms.openlocfilehash: 6c535b12906b6d9385029896dc5d0caf85d3399a
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654395"
---
# <a name="azure-service-bus-jms-20-developer-guide"></a>Azure Service Bus JMS 2,0 – fejlesztői útmutató

Ez az útmutató részletes információkat tartalmaz, amelyek segítséget nyújtanak a Azure Service Bus a Java Message Service (JMS) 2,0 API használatával történő kommunikáció sikerességéhez.

Java-fejlesztőként, ha a Azure Service Bus új, vegye fontolóra az alábbi cikkek olvasását.

| Első lépések | Alapelvek |
|----------------|-------|
| <ul> <li> [Mi az Azure Service Bus?](service-bus-messaging-overview.md) </li> <li> [Várólisták, témakörök és előfizetések](service-bus-queues-topics-subscriptions.md) </li> </ul> | <ul> <li> [Azure Service Bus – prémium szint](service-bus-premium-messaging.md) </li> </ul> |

## <a name="java-message-service-jms-programming-model"></a>Java Message Service-(JMS-) programozási modell

A Java Message Service API programozási modellje az alábbi ábrán látható: 

> [!NOTE]
>
>A **prémium szintű Azure Service Bus** támogatja a JMS 1,1 és a JMS 2,0.
> <br> <br>
> **Azure Service Bus – a standard** szint korlátozott JMS 1,1 funkciót támogat. További részletekért tekintse meg ezt a [dokumentációt](service-bus-java-how-to-use-jms-api-amqp.md).
>

# <a name="jms-20-programming-model"></a>[JMS 2,0 programozási modell](#tab/JMS-20)

:::image type="content" source="./media/jms-developer-guide/java-message-service-20-programming-model.png"alt-text="A JMS 2,0 programozási modellt bemutató ábra."border="false":::

# <a name="jms-11-programming-model"></a>[JMS 1,1 programozási modell](#tab/JMS-11)

:::image type="content" source="./media/jms-developer-guide/java-message-service-11-programming-model.png"alt-text="A JMS 1,1 programozási modellt bemutató ábra."border="false":::

---

## <a name="jms---building-blocks"></a>JMS – építőelemek

Az alábbi építőelemek a JMS alkalmazással való kommunikációhoz érhetők el.

> [!NOTE]
> Az alábbi útmutató a [Java Message Service (JMS) Oracle Java EE 6 oktatóanyaga](https://docs.oracle.com/javaee/6/tutorial/doc/bnceh.html) alapján lett átalakítva
>
> Erre az oktatóanyagra hivatkozva érdemes a Java Message Service (JMS) jobb megismerésére.
>

### <a name="connection-factory"></a>A kapcsolatok gyára
Az ügyfél a JMS-szolgáltatóhoz való kapcsolódáshoz használja a kapcsolat-előállító objektumot. A Csatlakozáskezelő a rendszergazda által meghatározott konfigurációs paramétereket ágyaz be.

Minden kapcsolat-előállító a vagy az illesztőfelület egy példánya `ConnectionFactory` `QueueConnectionFactory` `TopicConnectionFactory` .

A Azure Service Bus-vel való csatlakozás egyszerűbbé tétele érdekében ezek a felületek a és a használatával valósíthatók meg `ServiceBusJmsConnectionFactory` `ServiceBusJmsQueueConnectionFactory` `ServiceBusJmsTopicConnectionFactory` . A Csatlakozáskezelő a következő paraméterekkel hozható létre: 
   * Kapcsolatok karakterlánca – a Azure Service Bus prémium szintű névtérhez tartozó kapcsolatok karakterlánca.
   * A (ServiceBusJmsConnectionFactorySettings) tulajdonságot tartalmazó táska
      * connectionIdleTimeoutMS – üresjárati kapcsolat időtúllépése ezredmásodpercben.
      * traceFrames – logikai jelző, amely a hibakereséshez AMQP nyomkövetési kereteket gyűjt.
      * *egyéb konfigurációs paraméterek*

A gyár az alábbi módon hozható létre. A kapcsolatok karakterlánca kötelező paraméter, de a további tulajdonságok megadása nem kötelező.

```java
ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, null);
```

> [!IMPORTANT]
> A JMS 2,0 API-t használó Java-alkalmazásoknak csak a kapcsolati karakterlánccal kell csatlakozniuk a Azure Service Bushoz. Jelenleg a JMS-ügyfelek hitelesítése csak a kapcsolatok karakterláncának használatával támogatott.
>
> Az Azure Active Directory (HRE) által támogatott hitelesítés jelenleg nem támogatott.
>

### <a name="jms-destination"></a>JMS célhelye

A cél az a objektum, amelyet az ügyfél használ a általa előállított üzenetek céljának megadására, valamint az általa felhasznált üzenetek forrásának megadására.

A célhelyek Azure Service Bus-várólistákban (pont – pont) és témakörökben (a pub-sub-forgatókönyvekben) található entitásokhoz képezhetők.

### <a name="connections"></a>Kapcsolatok

A kapcsolatok virtuális kapcsolatokat ágyaznak be egy JMS-szolgáltatóval. A Azure Service Bus ez az alkalmazás és az AMQP közötti Azure Service Bus állapot-nyilvántartó kapcsolatot jelöli.

A kapcsolatok a kapcsolatok gyárból jönnek létre a lent látható módon.

```java
Connection connection = factory.createConnection();
```

### <a name="sessions"></a>Munkamenetek

A munkamenetek egyszálas környezetek, amelyek üzenetek készítésére és felhasználására szolgálnak. Az üzenetek, az üzenetek előállítói és a fogyasztók létrehozására is használható, de egy tranzakciós kontextust is biztosít, amely lehetővé teszi a Küldés és a fogadás egy atomi munkaegységbe való bevonását.

A Connection objektumból a lent látható módon hozhatók létre munkamenetek.

```java
Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
```

#### <a name="session-modes"></a>Munkamenet-üzemmódok

A munkamenetek az alábbi módok bármelyikével hozhatók létre.

| Munkamenet-üzemmódok | Működés |
| ----------------- | -------- |
|**Session.AUTO_ACKNOWLEDGE** | A munkamenet automatikusan visszaigazolja az ügyfél üzenetének kézhezvételét akkor is, ha a munkamenet sikeresen visszakapott a fogadási hívásból, vagy ha az üzenet figyelője azt kérte, hogy a munkamenet sikeresen visszaadja az üzenetet.|
|**Session.CLIENT_ACKNOWLEDGE** |Az ügyfél visszaigazolja a felhasználható üzenetet az üzenet nyugtázási metódusának meghívásával.|
|**Session.DUPS_OK_ACKNOWLEDGE**|Ez a visszaigazolási mód arra utasítja a munkamenetet, hogy lustul nyugtázza az üzenetek kézbesítését.| 
|**Session.SESSION_TRANSACTED**|Ez az érték a kapcsolati objektum createSession (int sessionMode) argumentuma lehet, amely megadja, hogy a munkamenetnek helyi tranzakciót kell használnia.| 

Ha nincs megadva a munkamenet mód, a rendszer alapértelmezés szerint a **Session.AUTO_ACKNOWLEDGE** választotta.

### <a name="jmscontext"></a>JMSContext

> [!NOTE]
> A JMSContext a JMS 2,0 specifikációjának részeként van definiálva.
>

A JMSContext egyesíti a kapcsolat és a munkamenet objektum által biztosított funkciókat. Ez a Csatlakozáskezelő-objektumból hozható létre.

```java
JMSContext context = connectionFactory.createContext();
```

#### <a name="jmscontext-modes"></a>JMSContext módok

A **munkamenet** -objektumhoz hasonlóan a JMSContext is létrehozhatók ugyanazzal a visszaigazolási móddal, mint a [munkamenet-üzemmódok](#session-modes).

```java
JMSContext context = connectionFactory.createContext(JMSContext.AUTO_ACKNOWLEDGE);
```

Ha nincs megadva a mód, a rendszer alapértelmezés szerint a **JMSContext.AUTO_ACKNOWLEDGE** választotta.

### <a name="jms-message-producers"></a>JMS-üzenetek gyártói

Az üzenet előállítója olyan objektum, amely egy JMSContext vagy egy munkamenet használatával jön létre, és az üzenetek célhelyre való küldésére szolgál.

Akár önálló objektumként is létrehozható az alábbi módon: 

```java
JMSProducer producer = context.createProducer();
```

vagy létrehozva futásidőben, ha üzenet küldésére van szükség.

```java
context.createProducer().send(destination, message);
```

### <a name="jms-message-consumers"></a>JMS üzenetek fogyasztói

Az üzenet fogyasztója egy JMSContext vagy-munkamenet által létrehozott objektum, amely a célhelyre küldött üzenetek fogadására szolgál. Az alábbi ábrán látható módon hozható létre:

```java
JMSConsumer consumer = context.createConsumer(dest);
```

#### <a name="synchronous-receives-via-receive-method"></a>Szinkron fogadás a Receive () metódus használatával

Az üzenet felhasználója szinkron módon fogadja az üzeneteket a célhelyről a `receive()` metódussal.

Ha nem ad meg argumentumot vagy időkorlátot, vagy ha meg van adva a "0" időtúllépés, akkor a fogyasztó határozatlan ideig blokkolja az üzenet megérkezését, vagy ha a kapcsolat megszakad (attól függően, hogy melyik a korábbi).

```java
Message m = consumer.receive();
Message m = consumer.receive(0);
```

Ha nullától eltérő pozitív argumentumot ad meg, a fogyasztó blokkolja az időzítő lejárati idejét.

```java
Message m = consumer.receive(1000); // time out after one second.
```

#### <a name="asynchronous-receives-with-jms-message-listeners"></a>Aszinkron fogadás a JMS Message figyelőkkel

Az üzenet-figyelő egy olyan objektum, amely a célhelyen lévő üzenetek aszinkron kezelésére szolgál. Megvalósítja a `MessageListener` felületet, amely tartalmazza azt a `onMessage` metódust, amelyben az adott üzleti logikának élőnak kell lennie.

Egy üzenet-figyelő objektumot a metódus használatával kell létrehozni és regisztrálni egy adott üzenet felhasználója számára `setMessageListener` .

```java
Listener myListener = new Listener();
consumer.setMessageListener(myListener);
```

### <a name="consuming-from-topics"></a>Felhasználás a témakörökből

A [JMS-üzenetek fogyasztói](#jms-message-consumers) egy olyan [célhelyen](#jms-destination) jönnek létre, amely lehet üzenetsor vagy témakör.

A várólistákban lévő felhasználók egyszerűen ügyféloldali objektumok, amelyek az ügyfélalkalmazás és a Azure Service Bus közötti munkamenet (és kapcsolat) kontextusában élnek.

A témakörökben azonban a felhasználók 2 részből állnak – 
   * **Ügyféloldali objektum** , amely a munkamenet (vagy JMSContext) kontextusában él, és
   * Olyan **előfizetés** , amely a Azure Service Bus entitása.

Az előfizetéseket [itt](java-message-service-20-entities.md#java-message-service-jms-subscriptions) dokumentálja, és a következők egyike lehet: 
   * Közös tartós előfizetések
   * Megosztott nem tartós előfizetések
   * Nem megosztott tartós előfizetések
   * Nem megosztva nem tartós előfizetések

### <a name="jms-queue-browsers"></a>JMS üzenetsor-tallózók

A JMS API olyan `QueueBrowser` objektumot biztosít, amely lehetővé teszi, hogy az alkalmazás megkeresse a várólistán lévő üzeneteket, és megjeleníti az egyes üzenetek fejlécének értékeit.

A várólista-tallózó az alábbi módon hozható létre a JMSContext használatával.

```java
QueueBrowser browser = context.createBrowser(queue);
```

> [!NOTE]
> A JMS API nem biztosít API-t egy témakör tallózásához.
>
> Ennek az az oka, hogy a témakör maga nem tárolja az üzeneteket. Amint elküldi az üzenetet a témakörnek, a rendszer továbbítja a megfelelő előfizetéseknek.
>

### <a name="jms-message-selectors"></a>JMS üzenetek választói

Az üzenet-választókat az alkalmazások fogadhatják a fogadott üzenetek szűrésére. Az üzenet-választókkal a fogadó alkalmazás kiszervezi az üzenetek szűrését a JMS-szolgáltatónak (ebben az esetben Azure Service Bus) ahelyett, hogy magára a felelősségre kellene irányulnia.

A választókat az alábbi felhasználók bármelyikének létrehozásakor lehet használni – 
   * Megosztott tartós előfizetés
   * Nem megosztott tartós előfizetés
   * Megosztott, nem tartós előfizetés
   * Nem megosztatlan, nem tartós előfizetés
   * Üzenetsor-tallózó


## <a name="summary"></a>Összefoglalás

Ez a fejlesztői útmutató bemutatja, hogyan csatlakozhatnak a Java Message Service (JMS) szolgáltatást használó Java-ügyfélalkalmazások a Azure Service Bushoz.

## <a name="next-steps"></a>Következő lépések

A Azure Service Bus és a Java Message Service-(JMS-) entitásokkal kapcsolatos további információkért tekintse meg az alábbi hivatkozásokat – 
* [Service Bus – várólisták, témakörök és előfizetések](service-bus-queues-topics-subscriptions.md)
* [Service Bus – Java Message Service-entitások](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities)
* [AMQP 1,0-támogatás Azure Service Bus](service-bus-amqp-overview.md)
* [Service Bus AMQP 1,0 fejlesztői útmutató](service-bus-amqp-dotnet.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [Java Message Service API (külső Oracle doc)](https://docs.oracle.com/javaee/7/api/javax/jms/package-summary.html)
* [Ismerje meg, hogyan migrálhat a ActiveMQ-ről Service Bus](migrate-jms-activemq-to-servicebus.md)
