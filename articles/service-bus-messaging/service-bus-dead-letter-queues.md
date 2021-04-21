---
title: Service Bus üzenetsorok | Microsoft Docs
description: A szolgáltatásban lévő, nem Azure Service Bus. Service Bus üzenetsorok és témakör-előfizetések egy másodlagos alsort biztosítanak, az úgynevezett holtbetűs üzenetsort.
ms.topic: article
ms.date: 04/08/2021
ms.custom: fasttrack-edit, devx-track-csharp
ms.openlocfilehash: cb791982b50d7afff7b74d70adfd285bb5e0a11c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773224"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>A Service Bus üzenetsorok áttekintése

Azure Service Bus üzenetsorok és *témakör-előfizetések* egy másodlagos alsort biztosítanak, az úgynevezett holtbetűs üzenetsort (DLQ). A holtbetűs üzenetsort nem kell explicit módon létrehozni, és nem törölhető és nem kezelhető a fő entitástól függetlenül.

Ez a cikk a szolgáltatásban lévő, nem Service Bus. A beszélgetés nagyját a GitHubon található, a hol nem látható üzenetek [üzenetsor-mintája](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/DeadletterQueue) szemlélteti.
 
## <a name="the-dead-letter-queue"></a>A holtbetűs üzenetsor

A kézbesített üzenetek üzenetsorának célja, hogy olyan üzeneteket tartalmaz, amelyek egyetlen fogadónak sem kézbesítve, sem pedig feldolgoz nem feldolgozható üzenetek. Az üzenetek ezután eltávolíthatók a DLQ-ból, és megvizsgálhatók. Egy alkalmazás egy operátor segítségével kijavíthatja a problémákat és újrakérte az üzenetet, naplózhatja a hibát, és korrekciós műveleteket is el lehet küldeni. 

API és protokoll szempontjából a DLQ többnyire hasonlít bármely más üzenetsorhoz, azzal a kivétellel, hogy az üzeneteket csak a szülőentitás holtbetűs műveleteként lehet küldeni. Emellett az élő idő nem figyelhető meg, és nem lehet egy DLQ-ból származó üzenetet holtbetűvel küldeni. A kézbesítő üzenetek várólistája teljes mértékben támogatja a betekintési zárolásos kézbesítést és a tranzakciós műveleteket.

A DLQ-t nem lehet automatikusan megtisztítani. Az üzenetek addig maradnak a DLQ-ban, amíg ön explicit módon le nem kérni őket a DLQ-ból, és be nem fejeződik a holtbetűs üzenet.


## <a name="dlq-message-count"></a>DLQ-üzenetek száma
A témakör szintjén nem lehet lekért üzenetek számát lekért üzenetsorban. Ennek az az oka, hogy az üzenetek nem a témakör szintjén ülnek el, Service Bus belső hibát jeleznek. Ehelyett amikor egy küldő üzenetet küld egy témakörbe, az üzenetet a rendszer ezredmásodpercek alatt továbbítja a témakör előfizetéseibe, így az már nem a témakör szintjén található. Így a témakör előfizetéséhez társított DLQ-ban láthatja az üzeneteket. A következő példában **Service Bus Explorer,** hogy jelenleg 62 üzenet van a "test1" előfizetés DLQ-ében. 

![DLQ-üzenetek száma](./media/service-bus-dead-letter-queues/dead-letter-queue-message-count.png)

A DLQ-üzenetek számát az Azure CLI-paranccsal is lekértheti: [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_show) . 

## <a name="moving-messages-to-the-dlq"></a>Üzenetek áthelyezése a DLQ-be
A szolgáltatásban számos olyan tevékenység Service Bus amelyek miatt az üzenetek lekértek a DLQ-nak magában az üzenetkezelési motorban. Az alkalmazások explicit módon is áthelyezheti az üzeneteket a DLQ-be. A rendszer az alábbi két tulajdonságot (a holtbetű okát és a holtbetűk leírását) hozzáadja a nem olvasható üzenetekhez. Az alkalmazások meghatároznak saját kódokat a holtbetű ok tulajdonsághoz, de a rendszer a következő értékeket állítja be.

| A holtbetű oka | A nem olvasható levél hibaüzenetének leírása |
| --- | --- |
|HeaderSizeExceeded |A stream méretkvótája túl lett lépve. |
|TTLExpiredException (TTLExpiredException) |Az üzenet lejárt, és a kézbesítetlenek üzenetek közé került. A [részletekért tekintse](#time-to-live) meg az Idő az életben című szakaszt. |
|A munkamenet-azonosító null értékű. |A munkamenet engedélyezett entitása nem engedélyezi az olyan üzeneteket, amelyek munkamenet-azonosítója null értékű. |
|MaxTransferHopCountExceeded | Az üzenetsorok közötti továbbításkor engedélyezett ugrások maximális száma. Az érték 4-re van állítva. |
| MaxDeliveryCountExceededExceptionMessage | Az üzenetet nem sikerült a maximális kézbesítési kísérletek után felhozni. A [részletekért lásd a Kézbesítések maximális száma](#maximum-delivery-count) szakaszt. |

## <a name="maximum-delivery-count"></a>Kézbesítések maximális száma
Az üzenetsorok és előfizetések üzenet-kézbesítési kísérletei Service Bus korlátozva vannak. Az alapértelmezett érték 10. Ha egy üzenet betekintés alatt lett kézbesítve, de explicit módon fel lett függedve, vagy a zárolás lejárt, az üzenet kézbesítési száma növekszik. Ha a kézbesítések száma meghaladja a korlátot, az üzenet átkerül a DLQ-be. A DLQ-ban az üzenet holtbetűs oka a következő: MaxDeliveryCountExceeded. Ez a viselkedés nem tiltható le, de beállíthatja a maximális kézbesítési számot nagy számra.

## <a name="time-to-live"></a>Élettartam
Ha engedélyezi a nem szöveges üzeneteket az üzenetsorok vagy előfizetések számára, az összes lejáró üzenet átkerül a DLQ-be. A holtbetű okkódjának beállítása: TTLExpiredException.

A lejárt üzeneteket a rendszer csak akkor kiüríti és áthelyezi a DLQ-be, ha legalább egy aktív fogadó leküldi a fő üzenetsort vagy előfizetést. A késleltetett üzenetek szintén nem lesznek kiürítve, és a lejártuk után átkerülnek a holtbetűs üzenetsorba. Ez a működésmód szándékos.

## <a name="errors-while-processing-subscription-rules"></a>Hibák az előfizetési szabályok feldolgozása során
Ha engedélyezi a holtbetűjeleket a szűrőértékelési kivételeknél, az előfizetés SQL-szűrési szabályának végrehajtása során előforduló hibákat a rendszer rögzíti a DLQ-ban a szabálysértő üzenettel együtt. Ne használja ezt a lehetőséget olyan éles környezetben, amelyben nem minden üzenettípus rendelkezik előfizetővel.

## <a name="application-level-dead-lettering"></a>Alkalmazásszintű holtbetűk
A rendszer által biztosított holtbetűs szolgáltatások mellett az alkalmazások a DLQ-val kifejezetten elutasítják az elfogadhatatlan üzeneteket. Tartalmazhatnak olyan üzeneteket, amelyek valamilyen rendszerhiba miatt nem megfelelően feldolgozhatóak, helytelenül formázott hasznos adatokat tartalmaznak, vagy olyan üzeneteket, amelyek valamilyen üzenetszintű biztonsági séma használata esetén nem tudják megfelelően feldolgozni a hitelesítést.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>A ForwardTo és a SendVia forgatókönyvekben a nem küldött leveleket
Az üzenetek a következő feltételek teljesülése esetén lesznek elküldve az átadási nem szöveges üzenetsorba:

- Egy üzenet több mint négy üzenetsoron vagy témakören halad át, amelyek össze [vannak láncolva.](service-bus-auto-forwarding.md)
- A cél üzenetsor vagy témakör le van tiltva vagy törölve van.
- A cél üzenetsor vagy témakör mérete meghaladja az entitások maximális méretét.

## <a name="path-to-the-dead-letter-queue"></a>A dead-letter üzenetsor elérési útja
A nem használt üzenetek üzenetsorát a következő szintaxissal lehet elérni:

```
<queue path>/$deadletterqueue
<topic path>/Subscriptions/<subscription path>/$deadletterqueue
```


## <a name="next-steps"></a>Következő lépések

Az üzenetsorok Service Bus a következő cikkekben talál további információt:

* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [Azure-üzenetsorok és Service Bus összehasonlítani](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
