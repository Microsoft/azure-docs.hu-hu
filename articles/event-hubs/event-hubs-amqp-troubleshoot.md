---
title: Az Azure Event Hubs AMQP hibáinak elhárítása | Microsoft Docs
description: Az Azure Event Hubs használatakor esetlegesen megjelenő AMQP-hibák listáját jeleníti meg, valamint a hibák okát.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 409552bb6176f2023901b4518646fbfcb2d51adf
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2021
ms.locfileid: "103235011"
---
# <a name="amqp-errors-in-azure-event-hubs"></a>AMQP hibák az Azure-ban Event Hubs
Ez a cikk néhány olyan hibát tartalmaz, amelyeket a AMQP Azure Event Hubs használatával történő használatakor kapott. A szolgáltatás minden szabványos viselkedése. Elkerülheti őket úgy, hogy küldési/fogadási hívásokat végez a kapcsolaton/hivatkozáson, amely automatikusan újra létrehozza a kapcsolatot vagy a hivatkozást.

## <a name="link-is-closed"></a>A hivatkozás be van zárva 
A következő hiba jelenik meg, amikor a AMQP-kapcsolat és a hivatkozás aktív, de nem (például a Send vagy a Receive) hívás a hivatkozással 10 percet vesz igénybe. Tehát a hivatkozás le van zárva. A hálózat továbbra is nyitva van.

"AMQP: link: leválasztásra kényszerítve: a (z)" G2:7223832: user.tenant0.cud_00000000000-0000-0000-0000-00000000000000 "hivatkozás le van választva a-közvetítőtől, mert hibák történtek a közzétevőben (link164614). Leválasztási forrás: AmqpMessagePublisher. IdleTimerExpired: Üresjárati időkorlát: 00:10:00. TrackingId: 00000000000000000000000000000000000000_G2_B3, SystemTracker: mynamespace: topic: MyTopic, időbélyeg: 2/16/2018 11:10:40 PM "

## <a name="connection-is-closed"></a>A csatlakoztatás be van zárva
A AMQP-kapcsolaton a következő hiba jelenik meg, ha a kapcsolat összes hivatkozása le van zárva, mert nem volt tevékenység (inaktív), és egy új hivatkozás nem jött létre 5 percen belül.

"Hiba {Condition = amqp: reaktivitás: erőltetett, Description =" a kapcsolatok inaktívak voltak az engedélyezett 300000 ezredmásodpercnél, és a "LinkTracker" tároló lezárta. TrackingId: 00000000000000000000000000000000000_G21, SystemTracker: gateway5, időbélyeg: 2019-03-06T17:32:00 ', info = null} "

## <a name="link-isnt-created"></a>A hivatkozás nincs létrehozva 
Ez a hiba akkor jelenik meg, ha új AMQP-kapcsolat jön létre, de a kapcsolat nem jön létre a AMQP-kapcsolat létrehozását követően 1 percen belül.

"Hiba {Condition = amqp: reaktivitás: erőltetett, Description =" a kapcsolatok inaktívak voltak az engedélyezett 60000 ezredmásodpercnél, és a "LinkTracker" tároló lezárta. TrackingId: 0000000000000000000000000000000000000_G21, SystemTracker: gateway5, időbélyeg: 2019-03-06T18:41:51 ', info = null} "

## <a name="next-steps"></a>Következő lépések
További információ a AMQP: [AMQP 1,0 protokoll útmutató](../service-bus-messaging/service-bus-amqp-protocol-guide.md).
