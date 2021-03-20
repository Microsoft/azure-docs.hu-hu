---
title: Üzenetkezelés – Java üzenet-szolgáltatási entitások Azure Service Bus
description: Ez a cikk áttekintést nyújt a Java Message Service API-n keresztül elérhető Azure Service Bus üzenetkezelési entitásokról.
ms.topic: article
ms.date: 07/20/2020
ms.openlocfilehash: ee4e0124dced16b86d5292c647e129aa87645f22
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100652581"
---
# <a name="java-message-service-jms-20-entities"></a>Java Message Service (JMS) 2,0 entitások

Az Azure Service Bus Premiumhoz csatlakozó ügyfélalkalmazások és a [Azure Service Bus JMS-függvénytár](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) használatával az alábbi entitások használhatók.

## <a name="queues"></a>Üzenetsorok

A JMS lévő várólisták szemantikailag összehasonlíthatóak a hagyományos [Service Bus várólistákkal](service-bus-queues-topics-subscriptions.md#queues).

Várólista létrehozásához használja az alábbi metódusokat a osztályban: `JMSContext`

```java
Queue createQueue(String queueName)
```

## <a name="topics"></a>Témakörök

A JMS-ban található témakörök szemantikailag összehasonlíthatóak a hagyományos [Service Bus témakörökkel](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions).

Témakör létrehozásához használja az alábbi metódusokat a osztályban: `JMSContext`

```java
Topic createTopic(String topicName)
```

## <a name="temporary-queues"></a>Ideiglenes várólisták

Ha egy ügyfélalkalmazás olyan ideiglenes entitást igényel, amely már létezik az alkalmazás élettartamához, használhat ideiglenes várólistákat. Ezek az entitások a [kérelem-válasz](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) mintában használatosak.

Ideiglenes várólista létrehozásához használja az alábbi metódusokat a osztályban: `JMSContext`

```java
TemporaryQueue createTemporaryQueue()
```

## <a name="temporary-topics"></a>Ideiglenes témakörök

Az ideiglenes várólistákhoz hasonlóan ideiglenes témakörök is léteznek, amelyek lehetővé teszik a közzétételt és az előfizetést egy olyan ideiglenes entitáson keresztül, amely már létezik az alkalmazás élettartamára.

Ideiglenes témakör létrehozásához használja az alábbi metódusokat a osztályban: `JMSContext`

```java
TemporaryTopic createTemporaryTopic()
```

## <a name="java-message-service-jms-subscriptions"></a>Java Message Service-(JMS-) előfizetések

Habár ezek szemantikai hasonlóságot mutatnak az [előfizetésekhez](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) (ez a témakör létezik, és lehetővé teszi a közzétételi/előfizetési szemantikai), a Java Message Service spec bevezeti a **megosztott**, nem **megosztott**, * * tartós és **nem tartós** attribútumok fogalmait egy adott előfizetéshez.

> [!NOTE]
> Az alábbi előfizetések Azure Service Bus prémium szinten érhetők el az [Azure Service Bus JMS könyvtár](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)használatával a Azure Service Bushoz csatlakozó ügyfélalkalmazások számára.
>
> Csak tartós előfizetések hozhatók létre a Azure Portal használatával.
>

### <a name="shared-durable-subscriptions"></a>Közös tartós előfizetések

A rendszer közös tartós előfizetést használ, ha egy adott témakörben közzétett összes üzenetet egy alkalmazásnak kell megkapnia és feldolgoznia, függetlenül attól, hogy az alkalmazás az előfizetésből aktívan használja-e az alkalmazást.

A Service Bustól fogadott bármely alkalmazás a megosztott tartós előfizetésből is fogadhat.

Megosztott tartós előfizetés létrehozásához használja az alábbi metódusokat a osztályban: `JMSContext`

```java
JMSConsumer createSharedDurableConsumer(Topic topic, String name)

JMSConsumer createSharedDurableConsumer(Topic topic, String name, String messageSelector)
```

A megosztott tartós előfizetés továbbra is fennáll, hacsak nem törli a `unsubscribe` metódust az `JMSContext` osztályban.

```java
void unsubscribe(String name)
```

### <a name="unshared-durable-subscriptions"></a>Nem megosztott tartós előfizetések

A közös tartós előfizetéshez hasonlóan a rendszer nem megosztott tartós előfizetést használ, ha egy adott témakörben közzétett összes üzenetet fogadni és feldolgozni kívánja egy alkalmazás, függetlenül attól, hogy az alkalmazás aktívan használja-e az előfizetést.

Mivel azonban ez egy nem megosztott előfizetés, csak az előfizetést létrehozó alkalmazás tud fogadni.

Nem megosztott tartós előfizetés létrehozásához használja az alábbi metódusokat az `JMSContext` osztályból: 

```java
JMSConsumer createDurableConsumer(Topic topic, String name)

JMSConsumer createDurableConsumer(Topic topic, String name, String messageSelector, boolean noLocal)
```

> [!NOTE]
> A `noLocal` szolgáltatás jelenleg nem támogatott és figyelmen kívül lesz hagyva.
>

A nem megosztott tartós előfizetés továbbra is fennáll, hacsak nem törli a `unsubscribe` metódust az `JMSContext` osztályban.

```java
void unsubscribe(String name)
```

### <a name="shared-non-durable-subscriptions"></a>Megosztott nem tartós előfizetések

Megosztott, nem tartós előfizetést akkor kell használni, ha több ügyfélalkalmazás számára egyetlen előfizetésből származó üzeneteket kell fogadnia és feldolgoznia, csak addig, amíg aktívan nem veszik igénybe, illetve nem kapják meg őket.

Mivel az előfizetés nem tartós, nem őrzi meg a rendszer. Az előfizetés nem fogad üzenetet, ha nincs aktív felhasználó.

Megosztott, nem tartós előfizetés létrehozásához hozzon létre egy, az `JmsConsumer` alábbi metódusokban látható módon a `JMSContext` osztályból:

```java
JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName)

JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName, String messageSelector)
```

A megosztott nem tartós előfizetés addig is fennáll, amíg aktív fogyasztók érkeznek.

### <a name="unshared-non-durable-subscriptions"></a>Nem megosztva nem tartós előfizetések

A nem megosztott, nem tartós előfizetést akkor használja a rendszer, ha az ügyfélalkalmazás egy előfizetésből származó üzenetet kap és dolgoz fel, csak addig, amíg aktívan nem veszik igénybe. Ezen az előfizetésen csak egy fogyasztó létezhet, azaz az az ügyfél, amely létrehozta az előfizetést.

Mivel az előfizetés nem tartós, nem őrzi meg a rendszer. Az előfizetés nem fogad üzenetet, ha nincs aktív felhasználó.

A nem megosztott, nem tartós előfizetés létrehozásához hozzon létre egy, az `JMSConsumer` alábbi metódusokban látható módon a `JMSContext` osztályból:

```java
JMSConsumer createConsumer(Destination destination)

JMSConsumer createConsumer(Destination destination, String messageSelector)

JMSConsumer createConsumer(Destination destination, String messageSelector, boolean noLocal)
```

> [!NOTE]
> A `noLocal` szolgáltatás jelenleg nem támogatott és figyelmen kívül lesz hagyva.
>

A nem megosztott, nem tartós előfizetés addig is fennáll, amíg aktív fogyasztó nem kap tőle.

### <a name="message-selectors"></a>Üzenetek választói

A normál Service Bus-előfizetésekhez hasonlóan a **szűrők és a műveletek** is léteznek, a JMS-előfizetések esetében pedig **üzenetek választhatók** .

Az üzenet-választókat beállíthatja az egyes JMS-előfizetések esetében, és szűrési feltételként létezik az üzenetfejléc tulajdonságaiban. Csak az üzenet-választó kifejezésnek megfelelő fejléc-tulajdonságokkal rendelkező üzenetek érkeznek. A Null érték vagy egy üres karakterlánc azt jelzi, hogy az JMS előfizetés/fogyasztó számára nincs megadva üzenet.

## <a name="additional-concepts-for-java-message-service-jms-20-subscriptions"></a>További fogalmak a Java Message Service (JMS) 2,0-előfizetésekhez

### <a name="client-scoping"></a>Ügyfél-hatókör

Az előfizetések, a Java Message Service (JMS) 2,0 API-ban meghatározottak szerint, vagy nem vonatkozhatnak *meghatározott ügyfélalkalmazás/s* (a megfelelővel azonosított `clientId` ) alkalmazásokra.

Ha az előfizetés hatókörön belül van, akkor csak az azonos ügyfél-azonosítóval rendelkező ügyfélalkalmazások **férhetnek hozzá** . 

Minden olyan előfizetés, amely egy adott ügyfél-AZONOSÍTÓra (például clientId1) vonatkozik egy másik ügyfél-azonosítóval (clientId2) rendelkező alkalmazáshoz, egy másik előfizetésnek a másik ügyfél-AZONOSÍTÓra (clientId2) való létrehozására fog irányulni.

> [!NOTE]
> Az ügyfél-azonosító lehet null értékű vagy üres, de meg kell egyeznie a JMS ügyfélalkalmazás beállított ügyfél-azonosítójával. A Azure Service Bus szempontból a null ügyfél-azonosító és az üres ügyfél-azonosító ugyanaz a viselkedés.
>
> Ha az ügyfél-azonosító értéke null vagy üres, csak azok az ügyfélalkalmazások férhetnek hozzá, amelyek ügyfél-azonosítója null vagy üres értékre van állítva.
>

### <a name="shareability"></a>Megoszthatóságot

A **megosztott** előfizetések lehetővé teszik, hogy több ügyfél/fogyasztó (azaz JMSConsumer-objektum) fogadja az üzeneteket.

>[!NOTE]
> Az adott ügyfél-AZONOSÍTÓra hatókörrel rendelkező megosztott előfizetéseket több ügyfél/fogyasztó is elérheti (például JMSConsumer-objektumok), de az ügyfélalkalmazások mindegyikének ugyanazzal az ügyfél-AZONOSÍTÓval kell rendelkeznie.
>
 

A nem **megosztott** előfizetések csak egyetlen ügyfél/fogyasztó (azaz JMSConsumer objektum) számára engedélyezik az üzenetek fogadását. Ha a egy `JMSConsumer` nem megosztott előfizetésen jön létre, miközben már aktív `JMSConsumer` figyelést folytat az üzenetekben, akkor a `JMSException` rendszer eldobott állapotban van.


### <a name="durability"></a>Tartósság

A **tartós** előfizetések megmaradnak, és továbbra is gyűjthetik az üzeneteket a témakörből, attól függetlenül, hogy egy alkalmazás ( `JMSConsumer` ) nem használ-e üzeneteket.

A **nem tartós** előfizetések nem maradnak meg, és nem gyűjtenek üzeneteket a témakörből, feltéve, hogy az alkalmazás ( `JMSConsumer` ) elfogyasztja az üzeneteket. 

## <a name="representation-of-client-scoped-subscriptions"></a>Az ügyfél hatókörű előfizetések ábrázolása

Mivel az ügyfél hatókörű (JMS) előfizetéseit együtt kell létrehozni a meglévő [előfizetésekkel](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions), az ügyfél hatókörű (JMS) előfizetéseit a következő formátumban kell megjeleníteni.

   * **\<SUBSCRIPTION-NAME\>**$**\<CLIENT-ID\>**$**D** (tartós előfizetések esetén)
   * **\<SUBSCRIPTION-NAME\>**$**\<CLIENT-ID\>**$**ND** (nem tartós előfizetésekhez)

Itt az elválasztó **$** karakter.

## <a name="next-steps"></a>Következő lépések

További információt és példákat a Service Bus üzenetkezelés használatával kapcsolatban a következő speciális témakörökben talál:

* [Service Bus üzenetkezelés áttekintése](service-bus-messaging-overview.md)
* [A Java Message Service 2,0 API és a Azure Service Bus Premium használata](how-to-use-java-message-service-20.md)



