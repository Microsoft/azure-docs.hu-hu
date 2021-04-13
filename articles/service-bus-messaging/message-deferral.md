---
title: Azure Service Bus – üzenet halasztása
description: Ez a cikk elmagyarázza, hogyan késleltetheti Azure Service Bus üzenetek kézbesítését. Az üzenet az üzenetsorban vagy előfizetésben marad, de a rendszer félreteszi azt.
ms.topic: article
ms.date: 06/23/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 997aab36652b08864892f1171e2b8588ec5f06b4
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306109"
---
# <a name="message-deferral"></a>Üzenetek halasztása

Ha egy üzenetsor vagy előfizetés-ügyfél olyan üzenetet kap, amely készen áll a feldolgozásra, de az alkalmazáson belüli különleges körülmények miatt jelenleg nem lehetséges a feldolgozás, akkor az üzenet "késleltetve" lekérésének lehetősége egy későbbi pontra. Az üzenet az üzenetsorban vagy előfizetésben marad, de a rendszer félreteszi azt.

A halasztás a munkafolyamat-feldolgozási forgatókönyvekhez kifejezetten létrehozott szolgáltatás. A munkafolyamat-keretrendszerek megkövetelhetik bizonyos műveletek feldolgozását egy adott sorrendben, és előfordulhat, hogy el kell halasztania néhány fogadott üzenet feldolgozását, amíg a többi üzenet által nem tájékozott korábbi munkák nem teljesülnek.

Egy egyszerű szemléltető példa egy olyan sorrend-feldolgozási folyamat, amelyben a külső fizetési szolgáltatótól érkező fizetési értesítés egy rendszeren jelenik meg, mielőtt a rendszer a megfelelő beszerzési rendelést propagálta az áruházból a teljesítési rendszerre. Ebben az esetben a teljesítési rendszer elhalaszthatja a fizetési értesítés feldolgozását, amíg meg nem történik egy rendelés, amelyhez társítja. A Rendezvous-forgatókönyvek esetében, ahol a különböző forrásokból származó üzenetek egy munkafolyamatot továbbítanak, a valós idejű végrehajtási sorrend valóban helyes lehet, de az eredményeket tükröző üzenetek eltérhetnek a sorrendtől.

Végső soron a késleltető támogatások az üzenetek érkezési sorrendjéből való átrendezésének sorrendjét a feldolgozható sorrendben, miközben az üzenetek biztonságosan elhagyják az üzenetet tárolóban, hogy melyik feldolgozást kell elhalasztani.

> [!NOTE]
> A késleltetett üzenetek a [lejárat után](./service-bus-dead-letter-queues.md#time-to-live)nem lesznek automatikusan áthelyezve a kézbesítetlen levelek várólistára. Ez a viselkedés a tervezés szerint történik.

## <a name="message-deferral-apis"></a>Üzenet halasztási API-jai

Az API az [BrokeredMessage. elhalaszt](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.defer#Microsoft_ServiceBus_Messaging_BrokeredMessage_Defer) vagy a [BrokeredMessage. DeferAsync](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deferasync#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeferAsync) a .NET-keretrendszer ügyfelében, a [MessageReceiver. DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) a .NET Standard ügyfélprogramban, valamint a [IMessageReceiver. elhalaszt](/java/api/com.microsoft.azure.servicebus.imessagereceiver.defer) vagy [IMessageReceiver. DeferAsync](/java/api/com.microsoft.azure.servicebus.imessagereceiver.deferasync) a Java-ügyfélen. 

A késleltetett üzenetek a fő várólistában maradnak, az összes többi aktív üzenettel együtt (az alvárólistákban élő kézbesítetlen levelektől eltérően), de a normál fogadási/ReceiveAsync függvények már nem fogadhatók. A késleltetett üzeneteket az [üzenetek böngészésével](message-browsing.md) lehet felderíteni, ha egy alkalmazás elveszíti a nyomon követését.

A késleltetett üzenet lekéréséhez a tulajdonosa felelős a [sorszám](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) való emlékezésért. Minden olyan fogadó, amely ismeri a késleltetett üzenet sorszámát, később explicit módon megkapja az üzenetet `Receive(sequenceNumber)` .

Ha egy üzenet nem dolgozható fel, mert az üzenet kezelésére szolgáló adott erőforrás átmenetileg nem érhető el, de az üzenetek feldolgozását nem szabad összefoglalni, akkor a pár percen belül úgy helyezheti el az üzenetet, hogy egy [ütemezett üzenetben](message-sequencing.md) **sorszám** , hogy néhány percen belül megjelenjen a késleltetett üzenet, és újból lekéri az elhalasztott üzenetet az ütemezett üzenet érkezésekor. Ha egy üzenetkezelő egy adatbázistól függ az összes művelettől, és az adatbázis átmenetileg nem érhető el, akkor nem használhatja a késleltetést, hanem inkább felfüggeszti az üzenetek fogadását egészen addig, amíg az adatbázis újra elérhető nem lesz.


## <a name="next-steps"></a>Következő lépések

Az Service Bus üzenetkezeléssel kapcsolatos további tudnivalókért tekintse meg a következő témaköröket:

* [Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)
