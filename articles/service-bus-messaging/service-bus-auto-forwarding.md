---
title: Azure Service Bus üzenetkezelési entitások automatikus továbbítása
description: Ez a cikk egy Azure Service Bus üzenetsor vagy előfizetés egy másik várólistára vagy témakörre való láncolását ismerteti.
ms.topic: article
ms.date: 01/20/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 80bef52d568130fa800a1da661f4867abb3df02c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98678988"
---
# <a name="chaining-service-bus-entities-with-autoforwarding"></a>Service Bus entitások láncolása az autoforwarding révén

Az Service Bus- *továbbítási* funkció lehetővé teszi egy üzenetsor vagy előfizetés láncolását egy másik üzenetsor vagy témakör számára, amely ugyanannak a névtérnek a részét képezi. Ha engedélyezve van az automatikus továbbítás, Service Bus automatikusan eltávolítja az első várólistába vagy előfizetésbe helyezett üzeneteket (forrás), és a második várólistába vagy témakörbe (cél) helyezi őket. A cél entitásnak közvetlenül is küldhet üzenetet.

> [!NOTE]
> Az Service Bus alapszintű csomagja nem támogatja az öntovábbítási funkciót. A standard és a prémium szintű csomag támogatja a szolgáltatást. A szintek közötti különbségekért tekintse meg a [Service Bus díjszabását](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="using-autoforwarding"></a>Az autoforwarding használata

A [QueueDescription. továbbítás][QueueDescription.ForwardTo] vagy a [SubscriptionDescription. továbbítás][SubscriptionDescription.ForwardTo] tulajdonságainak beállításával engedélyezheti a forráshoz tartozó [QueueDescription][QueueDescription] -vagy [SubscriptionDescription][SubscriptionDescription] -objektumokon a következő példában látható módon:

```csharp
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

A cél entitásnak léteznie kell a forrás entitás létrehozásakor. Ha a cél entitás nem létezik, Service Bus egy kivételt ad vissza, amikor a rendszer a forrás entitás létrehozását kéri.

Az egyéni témakört az autoforwarding használatával bővítheti. Service Bus az [adott témakör előfizetéseit](service-bus-quotas.md) 2 000-re korlátozza. További előfizetéseket is megadhat a második szintű témakörök létrehozásával. Még ha nem köti az előfizetések számának Service Bus korlátozását, a témakörök második szintjének hozzáadásával növelheti a témakör teljes átviteli sebességét.

![Egy olyan, az Orders (megrendelések) témakörben feldolgozott üzenet, amely a három második szintű megrendeléssel kapcsolatos témakörök bármelyikére képes.][0]

Az üzenetek küldői a fogadók való elválasztására is használható. Vegyünk például egy olyan ERP-rendszer, amely három modulból áll: a rendelések feldolgozása, a Készletkezelés és az Ügyfélkapcsolatok kezelése. Ezen modulok mindegyike egy megfelelő témakörbe várólistán lévő üzeneteket hoz létre. Alice és Bob olyan értékesítési képviselők, akik az ügyfelekhez kapcsolódó összes üzenetet érdeklik. Az üzenetek fogadásához Alice és Bob minden egyes ERP-témakörhöz hozzon létre egy személyes várólistát és egy előfizetést, amely automatikusan továbbítja az összes üzenetet a várólistára.

![Az autotovábbítási forgatókönyv ábrája három feldolgozó modult mutat be, amely három kapcsolódó témakörből két külön várólistára küld üzeneteket.][1]

Ha Alice a vakációra kerül, az ERP-témakör helyett a személyes üzenetsor jelenik meg. Ebben az esetben, mivel egy értékesítési képviselő nem kapott üzenetet, egyetlen ERP-témakör sem éri el a kvótát.

> [!NOTE]
> Az automatikus továbbítás beállításakor a **forráson és a célhelyen** lévő AutoDeleteOnIdle értéke automatikusan az adattípus maximális értékére lesz állítva.
> 
>   - A forrás oldalon az autoforwarding fogadási műveletként működik. Tehát az a forrás, amelynél az autoforwarding beállítás nem igazán "tétlen".
>   - A célhelyen ez azért szükséges, hogy mindig legyen egy célhely az üzenet továbbításához.

## <a name="autoforwarding-considerations"></a>Az öntovábbítási megfontolások

Ha a célként megadott entitás túl sok üzenetet halmoz fel, és meghaladja a kvótát, vagy ha a cél entitás le van tiltva, a forrás entitás hozzáadja az üzeneteket a [kézbesítetlen levelek várólistához](service-bus-dead-letter-queues.md) , amíg a cél nem áll helyre (vagy az entitás újból engedélyezve van). Ezek az üzenetek továbbra is a kézbesítetlen levelek várólistáján maradnak, ezért explicit módon kell fogadnia és feldolgoznia azokat a kézbesítetlen levelek várólistáján.

Ha összekapcsolja az egyes témaköröket egy összetett témakör számos előfizetéssel való beszerzéséhez, javasoljuk, hogy az első szintű témakörben és a második szintű témakörökben számos előfizetéssel rendelkezzen. Például egy, a 20 előfizetéssel rendelkező első szintű témakör, amelynek mindegyike egy, a 200-es előfizetéssel ellátott, második szintű témakörbe van leláncolva, lehetővé teszi a magasabb átviteli sebességet, mint a 200-előfizetéssel rendelkező, második szintű témakört, 20 előfizetéssel.

Service Bus egy műveletet számláz az egyes továbbított üzenetekhez. Ha például egy, a 20 előfizetéssel rendelkező témakörre küld üzenetet, és mindegyik úgy van konfigurálva, hogy az üzeneteket egy másik várólistára vagy témakörbe továbbítsa, akkor a számlázás 21 műveletként történik, ha az összes első szintű előfizetés megkapja az üzenet másolatát.

Egy másik várólistához vagy témakörhöz láncolt előfizetés létrehozásához az előfizetés létrehozójának a forrás és a cél entitáson is **kezelnie** kell a felügyeleti jogosultságokat. Az üzenetek a forrás témakörbe való küldése csak a forrás témakör **küldési** engedélyeit igényli.

Ne hozzon létre olyan láncot, amely meghaladja a 4 ugrást. A 4 ugrásnál hosszabb üzeneteket a rendszer kézbesíti.

## <a name="next-steps"></a>Következő lépések

Az autoforwarding szolgáltatással kapcsolatos részletes információkért tekintse meg a következő témaköröket:

* [Továbbítás][QueueDescription.ForwardTo]
* [QueueDescription][QueueDescription]
* [SubscriptionDescription][SubscriptionDescription]

További információ a Service Bus teljesítményének tökéletesítéséről: 

* [Ajánlott eljárások a teljesítmény javításához a Service Bus-üzenetkezelés használatával](service-bus-performance-improvements.md)
* [Particionált üzenetküldési entitások][Partitioned messaging entities].

[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto#Microsoft_ServiceBus_Messaging_QueueDescription_ForwardTo
[SubscriptionDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.forwardto#Microsoft_ServiceBus_Messaging_SubscriptionDescription_ForwardTo
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[SubscriptionDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[0]: ./media/service-bus-auto-forwarding/IC628631.gif
[1]: ./media/service-bus-auto-forwarding/IC628632.gif
[Partitioned messaging entities]: service-bus-partitioning.md
