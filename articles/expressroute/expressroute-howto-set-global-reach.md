---
title: 'Azure-ExpressRoute: Global Reach konfigurálása'
description: Ennek a cikknek a segítségével összekapcsolhatja a ExpressRoute-áramköröket, hogy magánhálózat legyen a helyszíni hálózatok között, és engedélyezze a Global Reach.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/25/2019
ms.author: duau
ms.openlocfilehash: 2365317d83a4c11fa17cb5c449a25b70da17c2eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92368366"
---
# <a name="configure-expressroute-global-reach"></a>Az ExpressRoute Global Reach konfigurálása

Ez a cikk segítséget nyújt a ExpressRoute-Global Reach konfigurálásához a PowerShell használatával. További információ: [ExpressRouteRoute Global REACH](expressroute-global-reach.md).

 ## <a name="before-you-begin"></a>Előkészületek

A konfigurálás megkezdése előtt erősítse meg a következőket:

* Megismerheti az ExpressRoute áramkör kiépítési [munkafolyamatait](expressroute-workflows.md).
* A ExpressRoute-áramkörök kiépített állapotban vannak.
* Az Azure-beli privát kapcsolatok konfigurálása a ExpressRoute-áramkörökben történik.
* Ha helyileg szeretné futtatni a PowerShellt, ellenőrizze, hogy a Azure PowerShell legújabb verziója van-e telepítve a számítógépen.

### <a name="working-with-azure-powershell"></a>A Azure PowerShell használata

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="identify-circuits"></a>Áramkörök azonosítása

1. A konfiguráció elindításához jelentkezzen be az Azure-fiókjába, és válassza ki a használni kívánt előfizetést.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. Azonosítsa a használni kívánt ExpressRoute-áramköröket. Engedélyezheti a ExpressRoute Global Reach bármely két ExpressRoute-áramkör privát társítása között, ha azok a támogatott országokban/régiókban találhatók, és különböző, egymástól eltérő helyeken hozták létre. 

   * Ha az előfizetés mindkét áramkört birtokolja, akkor a következő részekben választhatja ki a konfiguráció futtatásához.
   * Ha a két áramkör különböző Azure-előfizetésekben található, akkor egy Azure-előfizetésre van szükség. Ezután adja meg az engedélyezési kulcsot, amikor a másik Azure-előfizetésben futtatja a konfigurációs parancsot.

## <a name="enable-connectivity"></a>Kapcsolat engedélyezése

Engedélyezze a kapcsolatot a helyszíni hálózatok között. Az azonos Azure-előfizetésben és a különböző előfizetésekben lévő áramkörökhöz külön utasítások tartoznak.

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>ExpressRoute-áramkörök ugyanabban az Azure-előfizetésben

1. Használja az alábbi parancsokat az 1. és a 2. áramkör beszerzéséhez. A két áramkör ugyanahhoz az előfizetéshez tartoznak.

   ```azurepowershell-interactive
   $ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   ```
2. Futtassa a következő parancsot az 1. áramkörön, és adja át a 2. áramkör privát társ-AZONOSÍTÓját. A parancs futtatásakor vegye figyelembe a következőket:

   * A privát társ-létrehozási azonosító a következő példához hasonlóan néz ki: 

     ```
     /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
     ```
   * *– A AddressPrefix-* nek egy/29 IPv4-alhálózatnak kell lennie, például "10.0.0.0/29". Ebben az alhálózatban az IP-címeket használjuk a két ExpressRoute-áramkör közötti kapcsolat létesítéséhez. Az ezen az alhálózaton található címeket nem használhatja az Azure-beli virtuális hálózatokban vagy a helyszíni hálózaton.

     ```azurepowershell-interactive
     Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
     ```
3. Mentse a konfigurációt az 1. áramkörön a következőképpen:

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Ha az előző művelet befejeződik, mindkét oldalon a két ExpressRoute-áramkörön keresztül kapcsolatot fog létesíteni a helyszíni hálózatok között.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>ExpressRoute-áramkörök különböző Azure-előfizetésekben

Ha a két áramkör nem ugyanabban az Azure-előfizetésben található, akkor engedélyre van szüksége. A következő konfigurációban az engedélyezés a 2. áramkör-előfizetésben jön létre, az engedélyezési kulcsot pedig az 1. áramkörhöz adja át.

1. Engedélyezési kulcs létrehozása.

   ```azurepowershell-interactive
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_2
   ```

   Jegyezze fel a 2. áramkör privát társ-AZONOSÍTÓját, valamint az engedélyezési kulcsot.
2. Futtassa a következő parancsot az 1. áramkörön. Adja át a 2. áramkör privát társ-AZONOSÍTÓját és az engedélyezési kulcsot.

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
   ```
3. Mentse a konfigurációt az 1. áramkörön.

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Ha az előző művelet befejeződik, mindkét oldalon a két ExpressRoute-áramkörön keresztül kapcsolatot fog létesíteni a helyszíni hálózatok között.

## <a name="verify-the-configuration"></a>A konfiguráció ellenőrzése

A következő paranccsal ellenőrizheti a konfigurációt azon az áramkörön, amelyen a konfigurációt végezték (például az előző példában az 1. áramkört).
```azurepowershell-interactive
$ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Ha egyszerűen *$CKT _1* futtat a PowerShellben, a kimenetben megjelenik a *CircuitConnectionStatus* . Megtudhatja, hogy létrejött-e a kapcsolat, a "csatlakoztatott" vagy a "leválasztott". 

## <a name="disable-connectivity"></a>Az összekapcsolhatóság letiltása

A helyszíni hálózatok közötti kapcsolat letiltásához futtassa a parancsokat azon az áramkörön, ahol a konfiguráció létrejött (például az előző példában szereplő 1. áramkör).

```azurepowershell-interactive
$ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Az állapot ellenőrzéséhez futtathatja a Get műveletet.

Az előző művelet befejezése után már nincs kapcsolat a helyszíni hálózat és a ExpressRoute-áramkörök között.

## <a name="next-steps"></a>Következő lépések
1. [További információ a ExpressRoute Global Reach](expressroute-global-reach.md)
2. [ExpressRoute-kapcsolat ellenőrzése](expressroute-troubleshooting-expressroute-overview.md)
3. [ExpressRoute-áramkör összekapcsolása egy Azure-beli virtuális hálózattal](expressroute-howto-linkvnet-arm.md)
