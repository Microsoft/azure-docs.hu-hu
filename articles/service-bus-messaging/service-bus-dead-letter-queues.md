---
title: Kézbesítetlen levelek várólistáinak Service Bus | Microsoft Docs
description: A Azure Service Bus kézbesítetlen levelek várólistáinak leírása. Service Bus várólisták és témakör-előfizetések másodlagos alvárólistát biztosítanak, amelyet kézbesítetlen levelek várólistájának nevezünk.
ms.topic: article
ms.date: 04/08/2021
ms.custom: fasttrack-edit, devx-track-csharp
ms.openlocfilehash: 6459c8edd03427357810c1ad30161e87c18e059c
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304324"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>A kézbesítetlen levelek várólistájának Service Bus áttekintése

Azure Service Bus várólisták és témakör-előfizetések másodlagos alvárólistát biztosítanak, amelyet *kézbesítetlen levelek várólistájának* (DLQ) nevezünk. Nem kell explicit módon létrehozni a kézbesítetlen levelek várólistáját, és nem lehet törölni vagy felügyelni a fő entitástól függetlenül.

Ez a cikk a Service Bus kézbesítetlen levelek várólistáit ismerteti. A megbeszélések nagy részét a GitHubon a [kézbesítetlen levelek várólistájára vonatkozó példa](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/DeadletterQueue) szemlélteti.
 
## <a name="the-dead-letter-queue"></a>A kézbesítetlen levelek várólistája

A kézbesítetlen levelek várólistája olyan üzenetek tárolására szolgál, amelyeket nem lehet kézbesíteni a fogadóknak vagy nem feldolgozható üzeneteknek. Az üzenetek ezután eltávolíthatók a DLQ, és megtekinthetők a vizsgálatuk. Előfordulhat, hogy egy alkalmazás az operátorral, a probléma megoldásával és az üzenet újbóli elküldésével, a hiba elhárítása és a javítási művelet elvégzésével kapcsolatos hibát jelez. 

Az API-k és a protokollok szempontjából a DLQ többnyire a többi várólistához hasonlít, kivéve, hogy az üzeneteket csak a szülő entitás kézbesítetlen levelek művelete küldheti el. Emellett az élettartam nem észlelhető, és nem kézbesítheti a DLQ üzenetet. A kézbesítetlen levelek várólistája teljes mértékben támogatja a betekintés-zárolási kézbesítést és a tranzakciós műveleteket.

A DLQ automatikus törlése nem történik meg. Az üzenetek a DLQ maradnak, amíg explicit módon le nem kéri őket a DLQ, és elvégzi a kézbesítetlen levelek üzenetét.


## <a name="dlq-message-count"></a>DLQ üzenetek száma
A kézbesítetlen levelek várólistáján lévő üzenetek száma nem lehetséges a témakör szintjén. Ennek az az oka, hogy az üzenetek nem ülnek a témakör szintjén, hacsak Service Bus belső hibát okoz. Ehelyett, amikor egy feladó üzenetet küld egy témakörnek, az üzenet továbbításra kerül a témakör előfizetései között ezredmásodpercben, így a továbbiakban nem a témakör szintjén található. Így a témakörhöz tartozó előfizetéshez társított DLQ is láthat üzeneteket. A következő példában a **Service Bus Explorer** azt mutatja, hogy a "test1" előfizetés DLQ jelenleg 62 üzenet van. 

![DLQ üzenetek száma](./media/service-bus-dead-letter-queues/dead-letter-queue-message-count.png)

A DLQ-üzenetek számát az Azure CLI parancs használatával is lekérheti: [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az-servicebus-topic-subscription-show) . 

## <a name="moving-messages-to-the-dlq"></a>Üzenetek áthelyezése a DLQ
Számos olyan tevékenység van Service Bus, amely az üzenetek DLQ való leküldését eredményezi az üzenetküldési motoron belül. Az alkalmazások explicit módon is helyezhetnek üzeneteket a DLQ. A rendszer a következő két tulajdonságot (a kézbesítetlen levelek okát és a kézbesítetlen leveleket leíró leírást) hozzáadja a kézbesítetlen üzenetekhez. Az alkalmazások meghatározhatják saját kódokat a kézbesítetlen levelek okának tulajdonságához, de a rendszer a következő értékeket állítja be.

| Kézbesítetlen levelek oka | Kézbesítetlen levelek hibájának leírása |
| --- | --- |
|HeaderSizeExceeded |A stream méretkvótája túl lett lépve. |
|TTLExpiredException |Az üzenet lejárt, és a kézbesítetlenek üzenetek közé került. A részletekért tekintse meg az [élettartam](#time-to-live) szakaszt. |
|A munkamenet-azonosító null értékű. |A munkamenet engedélyezett entitása nem engedélyezi az olyan üzeneteket, amelyek munkamenet-azonosítója null értékű. |
|MaxTransferHopCountExceeded | A várólisták közötti továbbítás esetén engedélyezett ugrások maximális száma. Az érték értéke 4. |
| MaxDeliveryCountExceededExceptionMessage | Az üzenet nem használható fel a kézbesítési kísérletek maximális száma után. A részletekért tekintse meg a [kézbesítések maximális száma](#maximum-delivery-count) szakaszt. |

## <a name="maximum-delivery-count"></a>Kézbesítések maximális száma
A Service Bus várólisták és előfizetések üzeneteinek kézbesítésére tett kísérletek száma korlátozott. Az alapértelmezett érték 10. Minden alkalommal, amikor egy üzenet betekintési zár alá kerül, de explicit módon elhagyták, vagy a zárolás lejárt, az üzenet kézbesítési száma nő. Ha a kézbesítések száma meghaladja a korlátot, a rendszer áthelyezi az üzenetet a DLQ. A DLQ lévő üzenet kézbesítetlen levelekre vonatkozó oka a következőre van beállítva: MaxDeliveryCountExceeded. Ez a viselkedés nem tiltható le, de a maximális kézbesítések száma nagy számra állítható be.

## <a name="time-to-live"></a>Élettartam
Amikor engedélyezi a kézbesítetlen levelek várólistán vagy előfizetésen való engedélyezését, a rendszer az összes lejáró üzenetet áthelyezi a DLQ. A kézbesítetlen levelek okának kódja a következőre van beállítva: TTLExpiredException.

A lejárt üzenetek csak akkor törlődnek és átkerülnek a DLQ, ha van legalább egy aktív fogadó a fő sorból vagy előfizetésből. A késleltetett üzeneteket a rendszer nem törli és a kézbesítetlen levelek várólistára helyezi a lejárat után. Ez a működésmód szándékos.

## <a name="errors-while-processing-subscription-rules"></a>Hibák az előfizetési szabályok feldolgozásakor
Ha engedélyezi a kézbesítő üzenetek szűrésére vonatkozó kivételeket, akkor az előfizetés SQL-szűrési szabályának végrehajtása során felmerülő hibák rögzítése a DLQ és a jogsértő üzenettel együtt történik. Ne használja ezt a beállítást olyan éles környezetben, amelyben nem minden üzenetnek van előfizetője.

## <a name="application-level-dead-lettering"></a>Alkalmazás szintű kézbesítetlen levelek
A rendszerszintű kézbesítetlen levelek szolgáltatáson kívül az alkalmazások a DLQ is használhatják, hogy explicit módon visszautasítsák az elfogadhatatlan üzeneteket. Tartalmazhatnak olyan üzeneteket, amelyeket nem lehet megfelelően feldolgozni a rendszerhibák, a helytelenül formázott adattartalom tárolására szolgáló üzenetek, illetve az egyes üzenet szintű biztonsági sémák használata esetén sikertelen hitelesítéssel rendelkező üzenetek miatt.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Továbbítás-vagy küldés-forgatókönyvekben kézbesítetlen levelek
Az üzeneteket a rendszer a következő feltételekkel küldi el a kézbesítetlen levelek várólistára történő átviteléhez:

- Egy üzenet négynél több várólistán vagy [egymással összeláncolt](service-bus-auto-forwarding.md)témakörön halad át.
- A célvárólista vagy a témakör le van tiltva vagy törölve van.
- A célvárólista vagy a témakör mérete meghaladja az entitások maximális méretét.

## <a name="path-to-the-dead-letter-queue"></a>A kézbesítetlen levelek várólistájának elérési útja
A kézbesítetlen levelek várólistáját a következő szintaxissal érheti el:

```
<queue path>/$deadletterqueue
<topic path>/Subscriptions/<subscription path>/$deadletterqueue
```


## <a name="next-steps"></a>Következő lépések

Service Bus várólistákkal kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [Az Azure Queues és a Service Bus-várólisták összehasonlítása](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

