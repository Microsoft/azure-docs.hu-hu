---
title: Azure hálózati erőforrások áthelyezése új előfizetésre vagy erőforráscsoporthoz
description: A Azure Resource Manager használatával áthelyezheti a virtuális hálózatokat és egyéb hálózati erőforrásokat egy új erőforráscsoporthoz vagy előfizetésbe.
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 0cd6887d3489f2ffede0f5e3d63533a33a6ccc04
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "75485233"
---
# <a name="move-guidance-for-networking-resources"></a>Útmutató a hálózati erőforrások áthelyezéséhez

Ez a cikk bemutatja, hogyan helyezhetők át virtuális hálózatok és egyéb hálózati erőforrások adott forgatókönyvekhez.

## <a name="dependent-resources"></a>Függő erőforrások

Egy virtuális hálózat áthelyezésekor a függő erőforrásokat is át kell helyeznie. A VPN-átjárók esetében át kell helyeznie az IP-címeket, a virtuális hálózati átjárókat és az összes társított kapcsolati erőforrást. A helyi hálózati átjárók egy másik erőforráscsoporthoz is lehetnek.

Ha egy virtuális gépet egy hálózati kártyával szeretne áthelyezni egy új előfizetésbe, az összes függő erőforrást át kell helyeznie. Helyezze át a virtuális hálózatot a hálózati adapterhez, a virtuális hálózathoz tartozó összes többi hálózati adaptert és a VPN-átjárókat.

További információ: [forgatókönyv az előfizetések közötti áthelyezéshez](../move-resource-group-and-subscription.md#scenario-for-move-across-subscriptions).

## <a name="peered-virtual-network"></a>Egyenrangú virtuális hálózat

Egy társ virtuális hálózat áthelyezéséhez először le kell tiltania a virtuális hálózat társítását. A letiltást követően áthelyezheti a virtuális hálózatot. Az áthelyezést követően engedélyezze újra a virtuális hálózat társítását.

## <a name="subnet-links"></a>Alhálózati kapcsolatok

A virtuális hálózat nem helyezhető át másik előfizetésbe, ha a virtuális hálózat olyan alhálózatot tartalmaz, amely erőforrás-navigációs hivatkozásokkal rendelkezik. Ha például egy Redis erőforráshoz tartozó Azure cache-t egy alhálózatba helyeznek üzembe, az alhálózat erőforrás-navigációs hivatkozással rendelkezik.

## <a name="next-steps"></a>Következő lépések

Az erőforrások áthelyezésére szolgáló parancsokért lásd: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](../move-resource-group-and-subscription.md).
