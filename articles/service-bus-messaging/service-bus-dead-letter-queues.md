---
title: Kézbesítetlen levelek várólistáinak Service Bus | Microsoft Docs
description: A Azure Service Bus kézbesítetlen levelek várólistáinak leírása. Service Bus várólisták és témakör-előfizetések másodlagos alvárólistát biztosítanak, amelyet kézbesítetlen levelek várólistájának nevezünk.
ms.topic: article
ms.date: 06/23/2020
ms.custom: fasttrack-edit, devx-track-csharp
ms.openlocfilehash: ad62f946584071e7ce6fd55f48b5f7ee8db44a2f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98630098"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>A kézbesítetlen levelek várólistájának Service Bus áttekintése

Azure Service Bus várólisták és témakör-előfizetések másodlagos alvárólistát biztosítanak, amelyet *kézbesítetlen levelek várólistájának* (DLQ) nevezünk. Nem kell explicit módon létrehozni a kézbesítetlen levelek várólistáját, és nem lehet törölni vagy más módon felügyelni a fő entitástól függetlenül.

Ez a cikk a Service Bus kézbesítetlen levelek várólistáit ismerteti. A megbeszélések nagy részét a GitHubon a [kézbesítetlen levelek várólistájára vonatkozó példa](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/DeadletterQueue) szemlélteti.
 
## <a name="the-dead-letter-queue"></a>A kézbesítetlen levelek várólistája

A kézbesítetlen levelek várólistája olyan üzenetek tárolására szolgál, amelyeket nem lehet kézbesíteni a fogadóknak vagy nem feldolgozható üzeneteknek. Az üzenetek ezután eltávolíthatók a DLQ, és megtekinthetők a vizsgálatuk. Előfordulhat, hogy egy alkalmazás az operátorral, a probléma megoldásával és az üzenet újbóli elküldésével, a hiba elhárítása és a javítási művelet elvégzésével kapcsolatos hibát jelez. 

Az API-k és a protokollok szempontjából a DLQ többnyire a többi várólistához hasonlít, kivéve, hogy az üzeneteket csak a szülő entitás kézbesítetlen levelek művelete küldheti el. Emellett az élettartam nem észlelhető, és nem kézbesítheti a DLQ üzenetet. A kézbesítetlen levelek várólistája teljes mértékben támogatja a betekintés-zárolási kézbesítést és a tranzakciós műveleteket.

A DLQ automatikus törlése nem történik meg. Az üzenetek addig maradnak a DLQ, amíg explicit módon le nem kéri őket a DLQ, és meghívja a [Complete ()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) függvényt a kézbesítetlen levelek üzenetében.

## <a name="dlq-message-count"></a>DLQ üzenetek száma
A kézbesítetlen levelek várólistáján lévő üzenetek száma nem lehetséges a témakör szintjén. Ennek az az oka, hogy az üzenetek nem ülnek a témakör szintjén, hacsak Service Bus belső hibát okoz. Ehelyett, amikor egy feladó üzenetet küld egy témakörnek, az üzenet továbbításra kerül a témakör előfizetései között ezredmásodpercben, így a továbbiakban nem a témakör szintjén található. Így a témakörhöz tartozó előfizetéshez társított DLQ is láthat üzeneteket. A következő példában a **Service Bus Explorer** azt mutatja, hogy a "test1" előfizetés DLQ jelenleg 62 üzenet van. 

![DLQ üzenetek száma](./media/service-bus-dead-letter-queues/dead-letter-queue-message-count.png)

A DLQ-üzenetek számát az Azure CLI parancs használatával is lekérheti: [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az-servicebus-topic-subscription-show) . 

## <a name="moving-messages-to-the-dlq"></a>Üzenetek áthelyezése a DLQ

Számos olyan tevékenység van Service Bus, amely az üzenetek DLQ való leküldését eredményezi az üzenetküldési motoron belül. Az alkalmazások explicit módon is helyezhetnek üzeneteket a DLQ. 

Ahogy a közvetítő áthelyezi az üzenetet, két tulajdonságot adnak hozzá az üzenethez, mivel a közvetítő meghívja a [kézbesítetlen levelek](/dotnet/api/microsoft.azure.servicebus.queueclient.deadletterasync) metódus belső verzióját az üzenetre: `DeadLetterReason` és `DeadLetterErrorDescription` .

Az alkalmazások meghatározhatják a tulajdonsághoz tartozó saját kódokat `DeadLetterReason` , de a rendszer a következő értékeket állítja be.

| DeadLetterReason | DeadLetterErrorDescription |
| --- | --- |
|HeaderSizeExceeded |A stream méretkvótája túl lett lépve. |
|TTLExpiredException |Az üzenet lejárt, és a kézbesítetlenek üzenetek közé került. A részletekért tekintse meg a [TimeToLive meghaladó](#exceeding-timetolive) szakaszt. |
|A munkamenet-azonosító null értékű. |A munkamenet engedélyezett entitása nem engedélyezi az olyan üzeneteket, amelyek munkamenet-azonosítója null értékű. |
|MaxTransferHopCountExceeded | A várólisták közötti továbbítás esetén engedélyezett ugrások maximális száma. Az érték értéke 4. |
| MaxDeliveryCountExceededExceptionMessage | Az üzenet nem használható fel a kézbesítési kísérletek maximális száma után. A részletekért tekintse meg a [MaxDeliveryCount meghaladó](#exceeding-maxdeliverycount) szakaszt. |

## <a name="exceeding-maxdeliverycount"></a>MaxDeliveryCount túllépése

A várólisták és előfizetések mindegyike [QueueDescription. MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) és [SubscriptionDescription. MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount) tulajdonsággal rendelkezik; az alapértelmezett érték 10. Ha a zárolás ([ReceiveMode. PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode)) alatt egy üzenet érkezik, de explicit módon elhagyták, vagy a zárolás lejárt, akkor a [BrokeredMessage. DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) üzenet növekszik. Ha a [DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) túllépi a [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount), az üzenet átkerül a DLQ, és megadja az `MaxDeliveryCountExceeded` okkódot.

Ez a viselkedés nem tiltható le, de a [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) nagy számra állítható.

## <a name="exceeding-timetolive"></a>TimeToLive túllépése

Ha a [QueueDescription. EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription) vagy a [SubscriptionDescription. EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) tulajdonság értéke **true** (az alapértelmezett érték a **false**), a rendszer az összes lejáró üzenetet áthelyezi a DLQ, és megadja az  `TTLExpiredException` okkódot.

A lejárt üzenetek csak akkor törlődnek és átkerülnek a DLQ, ha van legalább egy aktív fogadó a fő sorból vagy előfizetésből, a [késleltetett üzenetek](./message-deferral.md) pedig nem törlődnek, és a lejáratuk után a kézbesítetlen levelek várólistára kerülnek. Ezek a viselkedések a tervek szerint vannak kialakítva.

## <a name="errors-while-processing-subscription-rules"></a>Hibák az előfizetési szabályok feldolgozásakor

Ha a [SubscriptionDescription. EnableDeadLetteringOnFilterEvaluationExceptions](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) tulajdonság engedélyezve van egy előfizetéshez, az előfizetés SQL-szűrési szabályának végrehajtása során felmerülő hibák rögzítése a DLQ és a jogsértő üzenettel együtt történik. Ne használja ezt a beállítást olyan éles környezetben, amelyben nem minden üzenetnek van előfizetője.

## <a name="application-level-dead-lettering"></a>Alkalmazás szintű kézbesítetlen levelek

A rendszerszintű kézbesítetlen levelek szolgáltatáson kívül az alkalmazások a DLQ is használhatják, hogy explicit módon visszautasítsák az elfogadhatatlan üzeneteket. Tartalmazhatnak olyan üzeneteket, amelyeket nem lehet megfelelően feldolgozni a rendszerhibák, a helytelenül formázott adattartalom tárolására szolgáló üzenetek, illetve az egyes üzenet szintű biztonsági sémák használata esetén sikertelen hitelesítéssel rendelkező üzenetek miatt.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Továbbítás-vagy küldés-forgatókönyvekben kézbesítetlen levelek

Az üzeneteket a rendszer a következő feltételekkel küldi el a kézbesítetlen levelek várólistára történő átviteléhez:

- Egy üzenet négynél több várólistán vagy [egymással összeláncolt](service-bus-auto-forwarding.md)témakörön halad át.
- A célvárólista vagy a témakör le van tiltva vagy törölve van.
- A célvárólista vagy a témakör mérete meghaladja az entitások maximális méretét.

A kézbesítetlen üzenetek lekéréséhez létrehozhat egy fogadót a [FormatTransferDeadletterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formattransferdeadletterpath) segédprogram használatával.

## <a name="example"></a>Példa

A következő kódrészlet létrehoz egy üzenetet fogadót. A fő üzenetsor fogadási ciklusában a kód lekéri az üzenetet a [Receive (TimeSpan. Zero)](/dotnet/api/microsoft.servicebus.messaging.messagereceiver)üzenettel, amely arra kéri a közvetítőt, hogy azonnal visszaadja az összes azonnal elérhető üzenetet, vagy ha eredmény nélkül szeretne visszatérni. Ha a kód üzenetet kap, azonnal elhagyja azt, ami növeli a következőt:  `DeliveryCount` . Ha a rendszer áthelyezi az üzenetet a DLQ, a fő várólista üres, és a hurok kilép, mivel [](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) a ReceiveAsync **Null** értéket ad vissza.

```csharp
var receiver = await receiverFactory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);
while(true)
{
    var msg = await receiver.ReceiveAsync(TimeSpan.Zero);
    if (msg != null)
    {
        Console.WriteLine("Picked up message; DeliveryCount {0}", msg.DeliveryCount);
        await msg.AbandonAsync();
    }
    else
    {
        break;
    }
}
```

## <a name="path-to-the-dead-letter-queue"></a>A kézbesítetlen levelek várólistájának elérési útja
A kézbesítetlen levelek várólistáját a következő szintaxissal érheti el:

```
<queue path>/$deadletterqueue
<topic path>/Subscriptions/<subscription path>/$deadletterqueue
```

Ha a .NET SDK-t használja, a kézbesítetlen levelek várólistájának elérési útját a SubscriptionClient. FormatDeadLetterPath () metódus használatával érheti el. Ez a metódus a következő témakört veszi fel: név/előfizetés neve és utótagja a **/$DeadLetterQueue**.


## <a name="next-steps"></a>Következő lépések

Service Bus várólistákkal kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [Az Azure Queues és a Service Bus-várólisták összehasonlítása](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

