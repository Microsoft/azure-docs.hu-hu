---
title: Service Bus üzenetsorok és a | Microsoft Docs
description: A szolgáltatásban lévő, nem Azure Service Bus. Service Bus üzenetsorok és témakör-előfizetések egy másodlagos alsort biztosítanak, az úgynevezett holtbetűs üzenetsort.
ms.topic: article
ms.date: 04/08/2021
ms.custom: fasttrack-edit, devx-track-csharp
ms.openlocfilehash: 6293a3a9a760ece137644578d8ee7dccebc63d95
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812371"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>A Service Bus üzenetsorok áttekintése

Azure Service Bus üzenetsorok és *témakör-előfizetések* egy másodlagos alsort biztosítanak, az úgynevezett holtbetűs üzenetsort (DLQ). A dead-letter üzenetsort nem kell explicit módon létrehozni, és nem törölhető és nem kezelhető a fő entitástól függetlenül.

Ez a cikk a szolgáltatásban lévő, nem Service Bus. A beszélgetés nagy része a GitHubon található [Dead-Letter üzenetsorok mintája](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/DeadletterQueue) mutatja be.
 
## <a name="the-dead-letter-queue"></a>A dead-letter üzenetsor

A kézbesített üzenetek üzenetsorának célja, hogy olyan üzeneteket tartalmaz, amelyek egyetlen fogadónak sem kézbesítve, vagy olyan üzeneteket tartalmaznak, amelyek nem feldolgozhatóak. Az üzenetek ezután eltávolíthatók a DLQ-ból, és megvizsgálhatók. Egy alkalmazás egy operátor segítségével kijavíthatja a problémákat, és újra elküldhet egy üzenetet, naplózhatja a hibát, és korrekciós műveleteket is el tud majd küldeni. 

API és protokoll szempontjából a DLQ többnyire hasonló bármely más üzenetsorhoz, azzal a kivétellel, hogy az üzenetek csak a szülőentitás "dead-letter" művelete révén küldhetőek el. Emellett az élő idő nem figyelhető meg, és nem lehet egy DLQ-tól származó üzenetet holtbetűvel írni. A kézbesítő üzenetek várólistája teljes mértékben támogatja a betekintés-zárolási kézbesítést és a tranzakciós műveleteket.

A DLQ-t nem lehet automatikusan megtisztítani. Az üzenetek addig maradnak a DLQ-ban, amíg ön explicit módon le nem olvassa őket a DLQ-ból, és be nem fejeződik a nem olvasható üzenet.


## <a name="dlq-message-count"></a>DLQ-üzenetek száma
A témakör szintjén nem lehet lekért üzenetek számát lekért üzenetsorban. Ennek az az oka, hogy az üzenetek nem a témakör szintjén ülnek el, Service Bus belső hibát jeleznek. Ehelyett amikor egy küldő üzenetet küld egy témakörbe, az üzenetet a rendszer ezredmásodpercek alatt továbbítja a témakör előfizetéseibe, így már nem a témakör szintjén található. Így a témakör előfizetéséhez társított DLQ-ban láthatja az üzeneteket. A következő példában **Service Bus Explorer,** hogy jelenleg 62 üzenet van a "test1" előfizetés DLQ-ében. 

![DLQ-üzenetek száma](./media/service-bus-dead-letter-queues/dead-letter-queue-message-count.png)

A DLQ-üzenetek számát az Azure CLI-paranccsal is le tudja kapni: [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_show) . 

## <a name="moving-messages-to-the-dlq"></a>Üzenetek áthelyezése a DLQ-be
A szolgáltatásban számos tevékenység Service Bus amelyek miatt az üzenetek az üzenetkezelési motoron belülről küldik le az üzeneteket a DLQ-nak. Az alkalmazások explicit módon is áthelyezheti az üzeneteket a DLQ-be. A rendszer az alábbi két tulajdonságot (a holtbetű okát és a holtbetűk leírását) hozzáadja a nem olvasható üzenetekhez. Az alkalmazások meghatároznak saját kódokat a holtbetű ok tulajdonsághoz, de a rendszer a következő értékeket állítja be.

| A holtbetű oka | A holtbetűs hibaüzenet leírása |
| --- | --- |
|HeaderSizeExceeded |A stream méretkvótája túl lett lépve. |
|TTLExpiredException (TTLExpiredException) |Az üzenet lejárt, és a kézbesítetlenek üzenetek közé került. A [részletekért tekintse](#time-to-live) meg az Idő az élhez című szakaszt. |
|A munkamenet-azonosító null értékű. |A munkamenet engedélyezett entitása nem engedélyezi az olyan üzeneteket, amelyek munkamenet-azonosítója null értékű. |
|MaxTransferHopCountExceeded | Az üzenetsorok közötti továbbításkor engedélyezett ugrások maximális száma. Az érték 4-re van állítva. |
| MaxDeliveryCountExceededExceptionMessage | Az üzenetet a maximális kézbesítési kísérlet után nem sikerült felhozni. A [részletekért tekintse](#maximum-delivery-count) meg a Kézbesítések maximális száma című szakaszt. |

## <a name="maximum-delivery-count"></a>Kézbesítések maximális száma
Az üzenetsorok és előfizetések üzenet-kézbesítési kísérletei száma korlátozva Service Bus van. Az alapértelmezett érték 10. Ha egy üzenet betekintés után kerül kézbesítésre, de explicit módon fel lett függedve, vagy a zárolás lejárt, az üzenet kézbesítési száma növekszik. Ha a kézbesítések száma meghaladja a korlátot, az üzenet átkerül a DLQ-be. A DLQ-ban az üzenet holtbetűjelének oka a következő: MaxDeliveryCountExceeded. Ez a viselkedés nem tiltható le, de beállíthatja a maximális kézbesítési számot nagy számra.

## <a name="time-to-live"></a>Élettartam
Ha engedélyezi a nem küldött leveleket az üzenetsorok vagy előfizetések számára, az összes lejáró üzenet átkerül a DLQ-be. A holtbetű okkódjának beállítása: TTLExpiredException.

A lejárt üzenetek csak akkor vannak kiürítve és áthelyezve a DLQ-be, ha legalább egy aktív fogadó van lekért a fő üzenetsorból vagy előfizetésből. A késleltetett üzenetek a lejáratuk után sem lesznek kiürítve, és nem kerülnek át a dead-letter üzenetsorba. Ez a működésmód szándékos.

## <a name="errors-while-processing-subscription-rules"></a>Hibák az előfizetési szabályok feldolgozása során
Ha engedélyezi a nem küldhető leveleket a szűrőértékelési kivételeknél, az előfizetés SQL-szűrési szabályának végrehajtása során előforduló hibákat a rendszer rögzíti a DLQ-ban a szabálysértő üzenettel együtt. Ne használja ezt a beállítást olyan éles környezetben, amelyben nem minden üzenettípus rendelkezik előfizetővel.

## <a name="application-level-dead-lettering"></a>Alkalmazásszintű dead-lettering
A rendszer által biztosított dead-lettering funkciók mellett az alkalmazások a DLQ-val explicit módon elutasítják az elfogadhatatlan üzeneteket. Tartalmazhatnak olyan üzeneteket, amelyek valamilyen rendszerhiba miatt nem lesznek megfelelően feldolgozhatóak, helytelenül formázott hasznos adatokat tartalmaznak, vagy olyan üzeneteket, amelyek valamilyen üzenetszintű biztonsági séma használata esetén nem tudnak hitelesítést használni.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Dead-lettering in ForwardTo or SendVia scenarios (A ForwardTo vagy a SendVia forgatókönyvben)
Az üzenetek a következő feltételek teljesülése esetén lesznek elküldve a nem továbbítható üzenetek várólistájára:

- Egy üzenet több mint négy üzenetsoron vagy témakören halad át, amelyek [össze vannak láncolva.](service-bus-auto-forwarding.md)
- A cél üzenetsor vagy témakör le van tiltva vagy törölve van.
- A cél üzenetsor vagy témakör mérete meghaladja az entitások maximális méretét.

## <a name="path-to-the-dead-letter-queue"></a>A holtbetűs üzenetsor elérési útja
A holtbetűs üzenetsort a következő szintaxissal lehet elérni:

```
<queue path>/$deadletterqueue
<topic path>/Subscriptions/<subscription path>/$deadletterqueue
```


## <a name="next-steps"></a>Következő lépések
Az üzenetek elévülési beállításának különböző konfigurálásával kapcsolatos további információkért lásd: Enable [dead lettering for a](enable-dead-letter.md) queue or subscription (Üzenetsor vagy előfizetés holtbetűjel-ének **engedélyezése).**
