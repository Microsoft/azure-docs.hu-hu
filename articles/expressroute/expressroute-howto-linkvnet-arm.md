---
title: 'ExpressRoute: VNet csatolása egy áramkörhöz: Azure PowerShell'
description: Ez a dokumentum áttekintést nyújt a Virtual Networks (virtuális hálózatok) és a ExpressRoute-áramkörök összekapcsolásáról a Resource Manager-alapú üzemi modell és a PowerShell használatával.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: how-to
ms.date: 05/20/2018
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: c8fd3bc4a2efd90857d0696491102790b069d5e1
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2020
ms.locfileid: "85984150"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit"></a>Virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoporttal
> [!div class="op_single_selector"]
> * [Azure Portalra](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Videó – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-linkvnet-classic.md)
>

Ez a cikk segítséget nyújt a Virtual Networks (virtuális hálózatok) és az Azure ExpressRoute-áramkörök összekapcsolásához a Resource Manager-alapú üzemi modell és a PowerShell használatával. A virtuális hálózatok lehetnek ugyanabban az előfizetésben vagy egy másik előfizetés részeként. A cikk azt is bemutatja, hogyan frissítheti a virtuális hálózati kapcsolatot.

* Akár 10 virtuális hálózatot is összekapcsolhat egy standard ExpressRoute áramkörrel. Szabványos ExpressRoute áramkör használata esetén minden virtuális hálózatnak ugyanabban a geopolitikai régióban kell lennie. 

* Egyetlen VNet legfeljebb négy ExpressRoute-áramkörhöz lehet kapcsolni. A cikkben ismertetett lépésekkel új kapcsolati objektumot hozhat létre minden olyan ExpressRoute-áramkörhöz, amelyhez csatlakozik. A ExpressRoute-áramkörök lehetnek ugyanabban az előfizetésben, különböző előfizetésekben vagy a kettő kombinációjában is.

* Az ExpressRoute áramkör geopolitikai régióján kívüli virtuális hálózatokat is csatolhat, vagy ha engedélyezte a prémium szintű ExpressRoute, akkor nagyobb számú virtuális hálózatot csatlakoztathat a ExpressRoute-áramkörhöz. A prémium szintű bővítménysel kapcsolatos további információkért olvassa el a [Gyakori kérdések](expressroute-faqs.md) című részt.


## <a name="before-you-begin"></a>Előkészületek

* A konfigurálás megkezdése előtt tekintse át az [előfeltételeket](expressroute-prerequisites.md), az [útválasztási követelményeket](expressroute-routing.md)és a [munkafolyamatokat](expressroute-workflows.md) .

* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége. 
  * Az utasításokat követve [hozzon létre egy ExpressRoute áramkört](expressroute-howto-circuit-arm.md) , és engedélyezze az áramkört a kapcsolat szolgáltatója számára. 
  * Győződjön meg arról, hogy az Ön áramköréhez konfigurálva van az Azure-beli magánhálózati kapcsolat. Az útválasztási utasításokért tekintse meg az [Útválasztás konfigurálása](expressroute-howto-routing-arm.md) című cikket. 
  * Győződjön meg arról, hogy az Azure Private peering konfigurálva van, és a hálózat és a Microsoft közötti BGP-társak is elérhetők, így a végpontok közötti kapcsolat engedélyezhető.
  * Győződjön meg arról, hogy van egy virtuális hálózat és egy virtuális hálózati átjáró létrehozva és teljesen kiépítve. A [ExpressRoute virtuális hálózati átjárójának létrehozásához](expressroute-howto-add-gateway-resource-manager.md)kövesse az utasításokat. A ExpressRoute virtuális hálózati átjárója a "ExpressRoute" GatewayType használja, nem pedig a VPN-t.

### <a name="working-with-azure-powershell"></a>A Azure PowerShell használata

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Ugyanahhoz az előfizetéshez tartozó virtuális hálózat összekapcsolása egy áramkörrel
A virtuális hálózati átjárókat a következő parancsmag használatával lehet összekapcsolni egy ExpressRoute-áramkörrel. Győződjön meg arról, hogy a virtuális hálózati átjáró létrejött, és készen áll a csatolásra a parancsmag futtatása előtt:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Egy másik előfizetéshez tartozó virtuális hálózat bevonása egy kapcsolatcsoportba
Megoszthat egy ExpressRoute-áramkört több előfizetésen keresztül is. Az alábbi ábrán egy egyszerű sematikus ábrán látható, hogy a megosztás hogyan működik a ExpressRoute-áramkörök több előfizetésen keresztül.

A nagyméretű felhőben lévő kisebb felhőket a szervezeten belüli különböző részlegekhez tartozó előfizetések ábrázolására használják. A szervezeten belüli részlegek saját előfizetést használhatnak a szolgáltatásaik üzembe helyezéséhez – de egyetlen ExpressRoute áramkört is megoszthatnak a helyszíni hálózathoz való kapcsolódáshoz. Egyetlen részleg (ebben a példában: IT) a ExpressRoute áramkör tulajdonosa lehet. A szervezeten belüli egyéb előfizetések a ExpressRoute áramkört is használhatják.

> [!NOTE]
> A ExpressRoute áramkör kapcsolati és sávszélesség-díjait az előfizetés tulajdonosa fogja alkalmazni. Minden virtuális hálózat ugyanazt a sávszélességet használja.
> 
> 

![Előfizetések közötti kapcsolat](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)


### <a name="administration---circuit-owners-and-circuit-users"></a>Felügyelet – az áramkör tulajdonosai és felhasználói

Az "áramkör tulajdonosa" a ExpressRoute Circuit erőforrásának jogosult Kiemelt felhasználója. Az áramkör tulajdonosa létrehozhat olyan engedélyeket, amelyeket az "áramköri felhasználók" is beválthatnak. Az áramköri felhasználók olyan virtuális hálózati átjárók tulajdonosai, amelyek nem a ExpressRoute áramkörhöz tartozó előfizetésben találhatók. Az áramköri felhasználók beválthatják az engedélyeket (egy-egy hitelesítés virtuális hálózatonként).

Az áramkör tulajdonosa bármikor módosíthatja és visszavonhatja az engedélyeket. Az engedély visszavonása az összes olyan kapcsolati kapcsolat törlését eredményezi, amelynek a hozzáférését visszavonták.

### <a name="circuit-owner-operations"></a>Áramköri tulajdonosi műveletek

**Engedélyezés létrehozása**

Az áramkör tulajdonosa létrehoz egy engedélyt. Ez egy olyan engedélyezési kulcs létrehozását eredményezi, amelyet egy áramköri felhasználó a virtuális hálózati átjárók ExpressRoute áramkörhöz való kapcsolódására használhat. Egy engedély csak egy kapcsolatban érvényes.

A következő parancsmag-kódrészlet azt mutatja be, hogyan lehet engedélyt létrehozni:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$auth1 = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
```


Az erre adott válasz az engedélyezési kulcsot és az állapotot fogja tartalmazni:

```azurepowershell
Name                   : MyAuthorization1
Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
AuthorizationKey       : ####################################
AuthorizationUseStatus : Available
ProvisioningState      : Succeeded
```



**Engedélyek áttekintése**

Az áramkör tulajdonosa a következő parancsmag futtatásával áttekintheti az adott áramkörön kiadott összes engedélyt:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Engedélyek hozzáadása**

Az áramkör tulajdonosa a következő parancsmag használatával adhat hozzá engedélyeket:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Engedélyek törlése**

Az áramkör tulajdonosa visszavonhatja vagy törölheti az engedélyeket a felhasználónak a következő parancsmag futtatásával:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit
```    

### <a name="circuit-user-operations"></a>Áramkör felhasználói műveletei

Az áramkör-felhasználónak szüksége van a társ-AZONOSÍTÓra és egy engedélyezési kulcsra az áramköri tulajdonostól. Az engedélyezési kulcs egy GUID.

A társ-azonosító a következő parancs segítségével ellenőrizhető:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**A kapcsolódási engedély beváltása**

Az áramkör-felhasználó a következő parancsmagot futtathatja egy hivatkozás engedélyezésének beváltásához:

```azurepowershell-interactive
$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"    
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**A kapcsolódási engedély felszabadítása**

Az engedélyezést a ExpressRoute áramkört a virtuális hálózathoz összekötő kapcsolat törlésével lehet felszabadítani.

## <a name="modify-a-virtual-network-connection"></a>Virtuális hálózati kapcsolatok módosítása
A virtuális hálózati kapcsolatok bizonyos tulajdonságainak frissítését végezheti el. 

**A kapcsolatok súlyozásának frissítése**

A virtuális hálózat több ExpressRoute-áramkörhöz is csatlakoztatható. Előfordulhat, hogy ugyanazt az előtagot több ExpressRoute áramkörből is megkapja. Annak kiválasztásához, hogy melyik kapcsolódási forgalmat küldje el az előtagja számára, módosíthatja a kapcsolatok *RoutingWeight* . A rendszer a forgalmat a legmagasabb *RoutingWeight*kapcsolatban küldi el.

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyVirtualNetworkConnection" -ResourceGroupName "MyRG"
$connection.RoutingWeight = 100
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
```

A *RoutingWeight* tartománya 0 és 32000 között van. Az alapértelmezett érték a 0.

## <a name="configure-expressroute-fastpath"></a>ExpressRoute-FastPath konfigurálása 
A [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) is engedélyezheti, ha a virtuális hálózati átjáró Ultra Performance vagy ErGw3AZ. A FastPath javítja az adatelérési utak teljesítményét, például a csomagok másodpercenkénti számát, valamint a helyszíni hálózat és a virtuális hálózat közötti kapcsolatok másodpercenkénti számát. 

**FastPath konfigurálása új kapcsolatban**

```azurepowershell-interactive 
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG" 
$gw = Get-AzVirtualNetworkGateway -Name "MyGateway" -ResourceGroupName "MyRG" 
$connection = New-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" -ExpressRouteGatewayBypass -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute -Location "MyLocation" 
``` 

**Meglévő kapcsolatok frissítése a FastPath engedélyezéséhez**

```azurepowershell-interactive 
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" 
$connection.ExpressRouteGatewayBypass = $True
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
``` 

## <a name="next-steps"></a>További lépések
A ExpressRoute kapcsolatos további információkért tekintse meg a [ExpressRoute gyakori kérdések](expressroute-faqs.md)című témakört.
