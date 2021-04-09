---
title: Azure Service Bus – üzenetek tallózása
description: Service Bus üzenetek tallózása és betekintés funkció lehetővé teszi, hogy egy Azure Service Bus-ügyfél enumerálja egy üzenetsor vagy előfizetés összes üzenetét.
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: f4943685f03eccb1c3b8da079973cf083bdcc416
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106090307"
---
# <a name="message-browsing"></a>Üzenetek tallózása

Az üzenetek böngészése vagy betekintése lehetővé teszi, hogy egy Service Bus-ügyfél egy üzenetsor vagy egy előfizetés összes üzenetének enumerálását diagnosztikai és hibakeresési célból.

A **várólistán** lévő betekintés művelet visszaadja az üzenetsor összes üzenetét, nem csak az azonnali beszerzéshez elérhetőket. Az **előfizetések** betekintés művelete az előfizetési üzenet naplójában lévő ütemezett üzenetek kivételével minden üzenetet visszaadja. 

A rendszer a felhasznált és a lejárt üzeneteket egy aszinkron "Garbage Collection" futtatással takarítja meg. Előfordulhat, hogy ez a lépés nem feltétlenül lép fel azonnal az üzenetek lejárta után. Ezért a betekintés művelet a már lejárt üzeneteket is visszaküldheti. Ezek az üzenetek el lesznek távolítva vagy kézbesítve lesznek, amikor a következő alkalommal fogad egy fogadási műveletet a várólistán vagy az előfizetésen. Tartsa szem előtt ezt a viselkedést, amikor megkísérli visszaállítani a késleltetett üzeneteket a várólistából. Egy lejárt üzenet már nem jogosult a rendszeres lekérésre bármilyen más módon, még akkor is, ha azt a betekintés visszaadja. Az üzenetek visszaküldése a következőképpen történik: betekintés a napló aktuális állapotát tükröző diagnosztikai eszköz.

A betekintés a zárolt és jelenleg más fogadók által feldolgozott üzeneteket is visszaadja. Mivel azonban a betekintés egy leválasztott pillanatképet ad vissza, egy üzenet zárolási állapota nem figyelhető meg a beérkező üzenetekben.

## <a name="peek-apis"></a>Betekintési API-k
## <a name="azuremessagingservicebus"></a>[Azure. Messaging. ServiceBus](#tab/dotnet)
A [PeekMessageAsync](/dotnet/api/azure.messaging.servicebus.servicebusreceiver.peekmessageasync) és a [PeekMessagesAsync](/dotnet/api/azure.messaging.servicebus.servicebusreceiver.peekmessagesasync) metódus létezik a fogadó objektumokon: `ServiceBusReceiver` , `ServiceBusSessionReceiver` . A betekintés a várólistákon, előfizetéseken és a hozzájuk tartozó kézbesítetlen levelek várólistáján működik.

Ha többször is meghívják, a `PeekMessageAsync` rendszer az üzenetsor vagy az előfizetési napló összes üzenetét a legalacsonyabb rendelkezésre álló sorozatszámból a legmagasabb értékre sorolja. Ez az a sorrend, amelyben az üzenetek várólistán lévő voltak, nem pedig az, hogy az üzenetek végül milyen sorrendben lesznek lekérdezve.
A PeekMessagesAsync több üzenetet kér le, és visszaadja őket enumerálásként. Ha nem áll rendelkezésre üzenet, a számbavételi objektum üres, nem null értékű.

A [fromSequenceNumber](/dotnet/api/microsoft.servicebus.messaging.eventposition.fromsequencenumber) paramétert egy olyan sorszám is feltöltheti, amelynél el szeretné indítani a metódust, majd meghívja a metódust anélkül, hogy a paramétert meg kellene adni a további enumeráláshoz. `PeekMessagesAsync` a függvények egymással egyenértékűek, de az üzenetek egy készlete egyszerre is lekérdezhető.


## <a name="microsoftazureservicebus"></a>[Microsoft. Azure. ServiceBus](#tab/dotnetold)
A [betekintési/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) és a [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) metódus létezik a fogadó objektumokon: `MessageReceiver` , `MessageSession` . A betekintés a várólistákon, előfizetéseken és a hozzájuk tartozó kézbesítetlen levelek várólistáján működik.

Ha többször is meghívják, a `Peek` rendszer az üzenetsor vagy az előfizetési napló összes üzenetét a legalacsonyabb rendelkezésre álló sorozatszámból a legmagasabb értékre sorolja. Ez az a sorrend, amelyben az üzenetek várólistán lévő voltak, nem pedig az, hogy az üzenetek végül milyen sorrendben lesznek lekérdezve.

A [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) több üzenetet kér le, és visszaadja őket enumerálásként. Ha nem áll rendelkezésre üzenet, a számbavételi objektum üres, nem null értékű.

A metódus túlterhelését is használhatja egy olyan [sorszám](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) , amelyen el szeretné indítani a metódust, majd a paraméter nélküli metódus túlterhelésének meghívásával további enumerálást készíthet. A **PeekBatch** függvények egyenértékűek, de egyszerre lekérik az üzenetek készletét.


---

## <a name="next-steps"></a>Következő lépések

Az Service Bus üzenetkezeléssel kapcsolatos további tudnivalókért tekintse meg a következő témaköröket:

* [Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)
