---
title: A AMQP hibáinak elhárítása a Azure Service Busban | Microsoft Docs
description: A Azure Service Bus használatakor esetlegesen megjelenő AMQP-hibák listáját jeleníti meg, illetve a hibák okát.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 88b10940e0b910f50e6ccf7f8c53134fa7f0ba2f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88064349"
---
# <a name="amqp-errors-in-azure-service-bus"></a>AMQP hibák a Azure Service Busban
Ez a cikk a AMQP és a Azure Service Bus használatával kapott hibákat ismerteti. A szolgáltatás minden szabványos viselkedése. Elkerülheti őket úgy, hogy küldési/fogadási hívásokat végez a kapcsolaton/hivatkozáson, amely automatikusan újra létrehozza a kapcsolatot vagy a hivatkozást.

## <a name="link-is-closed"></a>A hivatkozás be van zárva 
A következő hiba jelenik meg, amikor a AMQP-kapcsolat és a hivatkozás aktív, de nem (például a Send vagy a Receive) hívás a hivatkozással 10 percet vesz igénybe. Tehát a hivatkozás le van zárva. A hálózat továbbra is nyitva van.

```
amqp:link:detach-forced:The link 'G2:7223832:user.tenant0.cud_00000000000-0000-0000-0000-00000000000000' is force detached by the broker due to errors occurred in publisher(link164614). Detach origin: AmqpMessagePublisher.IdleTimerExpired: Idle timeout: 00:10:00. TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:Topic:MyTopic, Timestamp:2/16/2018 11:10:40 PM
```

## <a name="connection-is-closed"></a>A csatlakoztatás be van zárva
A AMQP-kapcsolaton a következő hiba jelenik meg, ha a kapcsolat összes hivatkozása le van zárva, mert nem volt tevékenység (tétlen), és egy új hivatkozás nem jött létre 5 percen belül.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 300000 milliseconds and is closed by container 'LinkTracker'. TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T17:32:00', info=null}
```

## <a name="link-is-not-created"></a>A hivatkozás nincs létrehozva 
Ez a hiba akkor jelenik meg, ha új AMQP-kapcsolat jön létre, de egy hivatkozás nem jön létre a AMQP-kapcsolat létrehozásával 1 percen belül.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 60000 milliseconds and is closed by container 'LinkTracker'. TrackingId:0000000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T18:41:51', info=null}
```

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a AMQP és a Service Busről, látogasson el a következő hivatkozásokra:

* [Service Bus AMQP áttekintése]
* [AMQP 1.0 protokoll – útmutató]
* [A Windows Server Service Bus AMQP]

[Service Bus AMQP áttekintése]: service-bus-amqp-overview.md
[AMQP 1.0 protokoll – útmutató]: service-bus-amqp-protocol-guide.md
[A Windows Server Service Bus AMQP]: /previous-versions/service-bus-archive/dn282144(v=azure.100)