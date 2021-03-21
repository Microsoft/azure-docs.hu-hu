---
title: Azure Service Bus – üzenetek tallózása
description: Service Bus üzenetek tallózása és betekintés funkció lehetővé teszi, hogy egy Azure Service Bus-ügyfél enumerálja egy üzenetsor vagy előfizetés összes üzenetét.
ms.topic: article
ms.date: 11/11/2020
ms.openlocfilehash: c52c9c967d4eada1a931e188ed4d25f7691cfb91
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94553641"
---
# <a name="message-browsing"></a>Üzenetek tallózása

Az üzenetek böngészése vagy betekintése lehetővé teszi, hogy egy Service Bus-ügyfél egy üzenetsor vagy egy előfizetés összes üzenetének enumerálását diagnosztikai és hibakeresési célból.

A várólistán lévő betekintés művelet visszaadja az üzenetsor összes üzenetét, nem csak az azonnali beszerzéshez `Receive()` vagy a `OnMessage()` hurokhoz elérhető üzeneteket. Az `State` egyes üzenetek tulajdonsága jelzi, hogy az üzenet aktív-e (elérhető), [késleltetve](message-deferral.md)vagy [ütemezve](message-sequencing.md). Az előfizetések betekintés művelete az előfizetési üzenet naplójában lévő ütemezett üzenetek kivételével minden üzenetet visszaadja. 

A rendszer a felhasznált és a lejárt üzeneteket egy aszinkron "Garbage Collection" futtatással takarítja meg. Előfordulhat, hogy ez a lépés nem feltétlenül lép fel azonnal az üzenetek lejárta után. Ezért előfordulhat, hogy a `Peek` már lejárt üzeneteket is visszaküldi. Ezek az üzenetek el lesznek távolítva vagy kézbesítve lesznek, amikor a következő alkalommal fogad egy fogadási műveletet a várólistán vagy az előfizetésen. Tartsa szem előtt ezt a viselkedést, amikor megkísérli visszaállítani a késleltetett üzeneteket a várólistából. Egy lejárt üzenet már nem jogosult a rendszeres lekérésre bármilyen más módon, még akkor is, ha azt a betekintés visszaadja. Az üzenetek visszaküldése a következőképpen történik: betekintés a napló aktuális állapotát tükröző diagnosztikai eszköz.

A betekintés a zárolt és jelenleg más fogadók által feldolgozott üzeneteket is visszaadja. Mivel azonban a betekintés egy leválasztott pillanatképet ad vissza, egy üzenet zárolási állapota nem figyelhető meg a beérkező üzenetekben. A [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) és a [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) tulajdonság akkor dobja el a [InvalidOperationException](/dotnet/api/system.invalidoperationexception) , amikor az alkalmazás megpróbálja beolvasni azokat.

## <a name="peek-apis"></a>Betekintési API-k

A [betekintési/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) és [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) metódusok léteznek a .net-és Java-ügyféloldali kódtárakban és a fogadó objektumokon: **MessageReceiver**, **MessageSession**. A betekintés a várólistákon, előfizetéseken és a hozzájuk tartozó kézbesítetlen levelek várólistáján működik.

Ha többször hívja meg a hívást, a **betekintés** a várólista összes üzenetét vagy az előfizetési naplóban lévő összes üzenetet a legalacsonyabb rendelkezésre álló sorozatszámból a legmagasabb értékre sorolja fel. Ez az a sorrend, amelyben az üzenetek várólistán lévő voltak, nem pedig az, hogy az üzenetek végül milyen sorrendben lesznek lekérdezve.

A [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) több üzenetet kér le, és visszaadja őket enumerálásként. Ha nem áll rendelkezésre üzenet, a számbavételi objektum üres, nem null értékű.

A metódus túlterhelését is használhatja egy olyan [sorszám](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) , amelyen el szeretné indítani a metódust, majd a paraméter nélküli metódus túlterhelésének meghívásával további enumerálást készíthet. A **PeekBatch** függvények egyenértékűek, de egyszerre lekérik az üzenetek készletét.

## <a name="next-steps"></a>Következő lépések

Az Service Bus üzenetkezeléssel kapcsolatos további tudnivalókért tekintse meg a következő témaköröket:

* [Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)
