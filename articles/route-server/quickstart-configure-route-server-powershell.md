---
title: 'Rövid útmutató: Útválasztási kiszolgáló létrehozása és konfigurálása Azure PowerShell'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre és konfigurálható útvonalkiszolgáló a Azure PowerShell.
services: route-server
author: duongau
ms.author: duau
ms.date: 03/02/2021
ms.topic: quickstart
ms.service: route-server
ms.custom:
- mode-api
ms.openlocfilehash: 608ec3755fcd231d5cc89bbc28a01ce172978144
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538703"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-powershell"></a>Rövid útmutató: Útválasztási kiszolgáló létrehozása és konfigurálása Azure PowerShell

Ez a cikk segít úgy konfigurálni az Azure Route Servert, hogy társviszonyt létesítsen egy hálózati virtuális készülékkel (NVA) a virtuális hálózatban a PowerShell használatával. Az Azure Route Server az NVA-ból tanulja meg az útvonalakat, és beprogramja őket a virtuális hálózat virtuális gépeibe. Az Azure Route Server az NVA virtuális hálózati útvonalait is meghirdeti. További információ: [Azure Route Server.](overview.md)

> [!IMPORTANT]
> Az Azure Route Server (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Győződjön meg arról, hogy a legújabb PowerShell-modulokkal Azure Cloud Shell a portálon.
* Tekintse át [az Azure Route Server szolgáltatási korlátait.](route-server-faq.md#limitations)

## <a name="create-a-route-server"></a>Útvonalkiszolgáló létrehozása

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Jelentkezzen be Azure-fiókjába, és válassza ki előfizetését.

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="create-a-resource-group-and-virtual-network"></a>Erőforráscsoport és virtuális hálózat létrehozása

Az Azure Route Server létrehozása előtt szüksége lesz egy virtuális hálózatra az üzembe helyezéshez. Az alábbi paranccsal hozzon létre egy erőforráscsoportot és egy virtuális hálózatot. Ha már rendelkezik virtuális hálózattal, ugorjon a következő szakaszra.

```azurepowershell-interactive
New-AzResourceGroup –Name "RouteServerRG” -Location “West US"
New-AzVirtualNetwork –ResourceGroupName "RouteServerRG" -Location "West US" -Name myVirtualNetwork –AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Alhálózat hozzáadása

1. Adjon hozzá egy *RouteServerSubnet nevű alhálózatot az* Azure Route Server üzembe helyezéséhez. Ez az alhálózat csak az Azure Route Server számára dedikált alhálózat. A RouteServerSubnetnek /27-nek vagy egy rövidebb előtagnak (például /26, /25) kell lennie, vagy hibaüzenetet kell kapnia az Azure Route Server hozzáadásakor.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork –Name "myVirtualNetwork" - ResourceGroupName "RouteServerRG"
    Add-AzVirtualNetworkSubnetConfig –Name "RouteServerSubnet" -AddressPrefix 10.0.0.0/24 -VirtualNetwork $vnet
    $vnet | Set-AzVirtualNetwork
    ```

1. Szerezze be a RouteServerSubnet azonosítót. A virtuális hálózat összes alhálózatának erőforrás-azonosítóját a következő paranccsal láthatja:

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork –Name "vnet_name" -ResourceGroupName "RouteServerRG"
    $vnet.Subnets
    ```

A RouteServerSubnet azonosító az alábbihoz hasonló:

`/subscriptions/<subscriptionID>/resourceGroups/RouteServerRG/providers/Microsoft.Network/virtualNetworks/myVirtualNetwork/subnets/RouteServerSubnet`

## <a name="create-the-route-server"></a>Az útvonalkiszolgáló létrehozása

Hozza létre az útvonalkiszolgálót a következő paranccsal:

```azurepowershell-interactive 
New-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG -Location "West US" -HostedSubnet "RouteServerSubnet_ID"
```

A helynek egyeznie kell a virtuális hálózat helyével. A HostedSubnet az előző szakaszban lekért RouteServerSubnet azonosító.

## <a name="create-peering-with-an-nva"></a>Társviszony létrehozása NVA-val

A következő paranccsal létesítsen BGP-társviszonyt az útvonalkiszolgáló és az NVA között:

```azurepowershell-interactive 
Add-AzRouteServerPeer -PeerName "myNVA" -PeerIp "nva_ip" -PeerAsn "nva_asn" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

A "nva_ip" az NVA-hoz rendelt virtuális hálózati IP-cím. "nva_asn" az NVA-ban konfigurált autonómrendszer-szám (ASN). Az ASN bármilyen 16 bites szám lehet, a 65515 és 65520 tartományon kívül. Az ASN-eket a Microsoft fenntartja.

Ha egy másik NVA-val vagy ugyanannak az NVA-nak egy másik példányával is társviszonyt létesít a redundancia biztosításához, használja a következő parancsot:

```azurepowershell-interactive 
Add-AzRouteServerPeer -PeerName "NVA2_name" -PeerIp "nva2_ip" -PeerAsn "nva2_asn" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG 
```

## <a name="complete-the-configuration-on-the-nva"></a>A konfiguráció befejezése az NVA-n

Az NVA konfigurációjának befejezéséhez és a BGP-munkamenetek engedélyezéséhez szükség van az Azure Route Server IP-címére és ASN-jére. Ezt az információt a következő paranccsal kaphatja meg:

```azurepowershell-interactive 
Get-AzRouteServer -RouterServerName myRouteServer -ResourceGroupName RouteServerRG
```

A kimenet a következő információkat tartalmazza:

``` 
RouteServerAsn : 65515
RouteServerIps : {10.5.10.4, 10.5.10.5}
```

## <a name="configure-route-exchange"></a><a name = "route-exchange"></a>Útvonalcsere konfigurálása

Ha egy ExpressRoute-átjáróval és egy Azure VPN-átjáróval van ugyanazon a VNeten, és azt szeretné, hogy útvonalakat cseréljenek, engedélyezheti az útvonalcserét az Azure Route Serveren.

1. Az Azure Route Server és az átjáró(k) közötti útvonalcsere engedélyezéséhez használja a következő parancsot:

```azurepowershell-interactive 
Update-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG -AllowBranchToBranchTraffic 
```

2. Az Azure Route Server és az átjáró(k) közötti útvonal-adatcsere letiltásához használja a következő parancsot:

```azurepowershell-interactive 
Update-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

## <a name="troubleshooting"></a>Hibaelhárítás

Az Azure Route Server által meghirdetett és fogadott útvonalakat a következő paranccsal tudja megtekinteni:

```azurepowershell-interactive
Get-AzRouteServerPeerAdvertisedRoute
Get-AzRouteServerPeerLearnedRoute
```
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az Azure Route Serverre, ezekkel a parancsokkal távolítsa el a BGP-társviszonyt, majd távolítsa el az útvonalkiszolgálót. 

1. Távolítsa el a BGP-társviszonyt az Azure Route Server és egy NVA között a következő paranccsal:

```azurepowershell-interactive 
Remove-AzRouteServerPeer -PeerName "nva_name" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG 
```

2. Távolítsa el az Azure Route Servert a következő paranccsal:

```azurepowershell-interactive 
Remove-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

## <a name="next-steps"></a>Következő lépések

Az Azure Route Server létrehozása után további információkkal szolgál arról, hogyan kommunikál az Azure Route Server az ExpressRoute- és VPN-átjárók használatával: 

> [!div class="nextstepaction"]
> [Azure ExpressRoute és Az Azure VPN támogatása](expressroute-vpn-support.md)
