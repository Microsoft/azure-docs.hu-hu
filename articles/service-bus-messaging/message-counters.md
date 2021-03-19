---
title: Azure Service Bus üzenetek száma
description: A várólistákban és előfizetésekben tárolt üzenetek számának lekérése Azure Resource Manager és az Azure Service Bus NamespaceManager API-k használatával.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: d0e1a7a5c6eb0b281b4e6ac08135f41f28ecbec8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85341280"
---
# <a name="message-counters"></a>Üzenetszámlálók

A várólistákban és előfizetésekben tárolt üzenetek számát az Azure Resource Manager és a .NET-keretrendszer SDK Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API-k használatával kérheti le.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A PowerShell-lel a következőképpen kérheti le a darabszámot:

```powershell
(Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>Üzenetek számának részletei

Az aktív üzenetek számának ismerete hasznos annak meghatározásához, hogy egy várólista felépít-e egy várakozó várólistát, amely több erőforrást igényel a jelenleg üzembe helyezett erőforrások feldolgozásához. A következő számláló részletei a [MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) osztályban érhetők el:

-   [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount): az üzenetsor vagy az előfizetés, amely aktív állapotban van, és készen áll a kézbesítésre.
-   [DeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount): üzenetek a kézbesítetlen levelek várólistáján.
-   [ScheduledMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount): az ütemezett állapotú üzenetek.
-   [TransferDeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount): nem sikerült átvinni az üzeneteket egy másik várólistába vagy témakörbe, és áthelyezték azokat a kézbesítetlen levelek várólistába.
-   [TransferMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount): egy másik várólistára vagy témakörbe való átvitelre váró üzenetek.

Ha egy alkalmazás a várólista hossza alapján szeretné méretezni az erőforrásokat, azt a mért tempóval kell végrehajtania. Az üzenetek számlálóinak beszerzése egy költséges művelet az üzenetkezelőben, és gyakran közvetlenül, illetve az entitás teljesítményének hátrányos kihatásával jár.

> [!NOTE]
> A Service Bus témakörbe küldött üzenetek továbbítva lesznek az adott témakör előfizetéséhez. Így az aktív üzenetek száma a témakörben 0, mivel az üzenetek sikeresen továbbítódnak az előfizetésbe. Szerezze be az üzenetek számait az előfizetésben, és ellenőrizze, hogy a nullánál nagyobb-e. Annak ellenére, hogy az előfizetéshez tartozó üzenetek jelennek meg, valójában a témakörben tárolt tárolóban tárolódnak. 

Ha megtekinti az előfizetéseket, akkor nem nulla számú üzenetnek kellene meglennie (amelyek a teljes entitáshoz 323MB fel).

## <a name="next-steps"></a>Következő lépések

Az Service Bus üzenetkezeléssel kapcsolatos további tudnivalókért tekintse meg a következő témaköröket:

* [Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)
