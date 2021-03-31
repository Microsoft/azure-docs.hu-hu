---
title: Azure Service Bus Microsoft Docs
description: Azure Service Bus üzenetek előhívásával javíthatja a teljesítményt. Az üzenetek az alkalmazások kérelme előtt azonnal elérhetők a helyi lekéréshez.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 05e23b0590f0c04171efda8fb561b4c2664ed096
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85341051"
---
# <a name="prefetch-azure-service-bus-messages"></a>A prefektus Azure Service Bus üzenetek

Ha *bármelyik* hivatalos Service Bus ügyfélben engedélyezve van a kinyerés, a fogadó csendesen több üzenetet kap, akár a [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) -korlátot, az eredetileg kért alkalmazás után.

Az egyszeri kezdeti [fogadási](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive) vagy [ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) hívása ezért azonnali, azonnal visszaadott felhasználásra vonatkozó üzenetet küld. Az ügyfél ezután további üzeneteket olvas be a háttérben, hogy kitöltse a prefektusi puffert.

## <a name="enable-prefetch"></a>A prefektus engedélyezése

A .NET-tel a **MessageReceiver**, a **QueueClient** vagy a **SubscriptionClient** [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) tulajdonságát nullánál nagyobb értékre állíthatja be. Ha az értéket nullára állítja, a kikapcsolja a prefektust.

Ezt a beállítást egyszerűen hozzáadhatja a [QueuesGettingStarted](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/QueuesGettingStarted) vagy a [ReceiveLoop](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ReceiveLoop) Samples beállítások fogadó-oldalához, így láthatja ennek hatását.

Amíg az üzenetek elérhetők a visszahívási pufferben, a további **fogadási** / **ReceiveAsync** -hívások azonnal teljesülnek a pufferből, és a puffer a háttérben lesz feltöltve, mert elérhetővé válik. Ha nincs elérhető üzenet a kézbesítéshez, a fogadási művelet kiüríti a puffert, majd megvárja vagy blokkolja a várt módon.

A prefektus is ugyanúgy működik együtt a [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) és a [OnMessageAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessageasync) API-kkal.

## <a name="if-it-is-faster-why-is-prefetch-not-the-default-option"></a>Ha gyorsabb, miért nem ez az alapértelmezett beállítás?

A kiállítók felgyorsítják az üzenetek folyamatát azáltal, hogy egy üzenetet azonnal elérhetővé tesznek a helyi lekéréshez, amikor az alkalmazás kér egyet. Ez az átviteli sebesség azt eredményezi, hogy az alkalmazás szerzője explicit módon végzi el a kompromisszumot:

A [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) fogadási mód használata esetén a rendszer az összes, a Meghívási pufferben beszerzett üzenet már nem érhető el a várólistában, és csak a memóriában tárolt kiküldési pufferben tárolja, amíg a **fogadási** / **ReceiveAsync** vagy **OnMessage** / **OnMessageAsync** API-kkal nem érkeznek az alkalmazásba. Ha az alkalmazás leáll, mielőtt az üzenetek beérkeznek az alkalmazásba, az üzenetek visszaszerezhetetlenül elvesznek.

A [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) fogadási módban a visszahívási pufferbe beolvasott üzeneteket a rendszer zárolt állapotba tölti be a pufferbe, és a zárolási ketyegő időtúllépési idővel rendelkezik. Ha a visszahívási puffer mérete nagy, és a feldolgozás addig tart, amíg az üzenetek zárolása lejár, miközben a rendszer a visszahívási pufferben tartózkodik, vagy amíg az alkalmazás feldolgozza az üzenetet, előfordulhat, hogy az alkalmazás kezelésére bizonyos zavaros események szükségesek.

Előfordulhat, hogy az alkalmazás lejárt vagy közeljövőben lejáró zárolással rendelkező üzenetet szerez be. Ha igen, az alkalmazás feldolgozhatja az üzenetet, de azt is megtudhatja, hogy a zárolás lejárata miatt nem hajtható végre. Az alkalmazás megtekintheti a [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) tulajdonságot (amely a közvetítő és a helyi gép órája között eldönthető óra alapján változhat). Ha az üzenet zárolása lejárt, az alkalmazásnak figyelmen kívül kell hagynia az üzenetet; nincs szükség API-hívásra vagy az üzenet elküldésére. Ha az üzenet nem járt le, de a lejárat küszöbön van, a zárolás megújítható és kiterjeszthető egy másik alapértelmezett zárolási időszakra az üzenet meghívásával [. RenewLock ()](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_)

Ha a zárolás csendesen lejár a visszahívási pufferben, az üzenet az elhagyott értékként lesz kezelve, és újra elérhetővé válik a várólistából való lekéréshez. Ez azt eredményezheti, hogy a rendszer beolvassa a kihívási pufferbe; a végponton helyezhető el. Ha a visszahívási puffer általában nem használható az üzenetek lejárati ideje alatt, akkor az üzenetek ismételt beolvasása megtörténik, de a rendszer soha nem kézbesíti hatékonyan a felhasználható (érvényesen zárolt) állapotot, és végül áthelyezi a kézbesítetlen levelek várólistára, ha túllépi a maximális kézbesítési értéket.

Ha magas fokú megbízhatóságra van szüksége az üzenetek feldolgozásához, és a feldolgozás jelentős munkát és időt vesz igénybe, akkor azt javasoljuk, hogy a kivonási funkciót konzervatívan, vagy egyáltalán ne használja.

Ha nagy átviteli sebességre van szüksége, és az üzenetek feldolgozására általában olcsó, akkor jelentős teljesítménybeli előnyökkel jár.

A rendszer a várólistán vagy az előfizetésen beállított maximális visszahívási időtartamot és a zárolási időtartamot úgy kell kiegyenlíteni, hogy a zárolási időkorlát legalább meghaladja a Meghívási puffer maximális méretét, valamint egy üzenetet. Ugyanakkor a zárolás időkorlátja nem lehet olyan sokáig, hogy az üzenetek túllépik a maximális [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) , amikor véletlenül eldobják őket, így a zárolást az újbóli kézbesítés előtt le kell zárni.

## <a name="next-steps"></a>Következő lépések

Az Service Bus üzenetkezeléssel kapcsolatos további tudnivalókért tekintse meg a következő témaköröket:

* [Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)
