---
title: 'Gyors útmutató: útvonal-kiszolgáló létrehozása és konfigurálása Azure PowerShell használatával'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre és konfigurálhat egy útválasztó kiszolgálót Azure PowerShell használatával.
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: c56e7318e24b802ae9ad605a0c9ae5f88397ec8b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101680619"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-powershell"></a>Gyors útmutató: útvonal-kiszolgáló létrehozása és konfigurálása Azure PowerShell használatával

Ebből a cikkből megtudhatja, hogyan konfigurálhatja az Azure Route Servert egy hálózati virtuális berendezéssel (NVA) a virtuális hálózaton a PowerShell használatával. Az Azure Route Server a NVA érkező útvonalakat tanulja meg, és a virtuális hálózatban lévő virtuális gépeken is programba veszi őket. Az Azure Route Server a virtuális hálózati útvonalakat is meghirdeti a NVA. További információért olvassa el az [Azure Route Servert](overview.md).

> [!IMPORTANT]
> Az Azure Route Server (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Győződjön meg arról, hogy rendelkezik a legújabb PowerShell-modulokkal, vagy a portálon Azure Cloud Shell is használhatja.
* Tekintse át az [Azure Route Server szolgáltatási korlátozásait](route-server-faq.md#limitations).

## <a name="create-a-route-server"></a>Útvonal-kiszolgáló létrehozása

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Jelentkezzen be Azure-fiókjába, és válassza ki előfizetését.

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="create-a-resource-group-and-virtual-network"></a>Erőforráscsoport és virtuális hálózat létrehozása

Az Azure Route Server létrehozása előtt szüksége lesz egy virtuális hálózatra az üzemelő példány üzemeltetéséhez. Az alábbi parancs használatával hozzon létre egy erőforráscsoportot és egy virtuális hálózatot. Ha már rendelkezik virtuális hálózattal, ugorjon a következő szakaszra.

```azurepowershell-interactive
New-AzResourceGroup –Name “RouteServerRG” -Location “West US”
New-AzVirtualNetwork –ResourceGroupName “RouteServerRG -Location “West US” -Name myVirtualNetwork –AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Alhálózat hozzáadása

1. Adjon hozzá egy *RouteServerSubnet* nevű alhálózatot az Azure Route-kiszolgáló üzembe helyezéséhez a alkalmazásban. Ez az alhálózat csak az Azure Route Server dedikált alhálózata. A RouteServerSubnet/27 vagy egy rövidebb előtagnak kell lennie (például/26,/25), vagy hibaüzenet jelenik meg az Azure-útválasztó kiszolgáló hozzáadásakor.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork –Name “myVirtualNetwork” - ResourceGroupName “RouteServerRG”
    Add-AzVirtualNetworkSubnetConfig –Name “RouteServerSubnet” -AddressPrefix 10.0.0.0/24 -VirtualNetwork $vnet
    $vnet | Set-AzVirtualNetwork
    ```

1. Szerezze be a RouteServerSubnet AZONOSÍTÓját. A virtuális hálózat összes alhálózatának erőforrás-AZONOSÍTÓjának megtekintéséhez használja a következő parancsot:

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork –Name “vnet_name” -ResourceGroupName “
    $vnet.Subnets
    ```

A RouteServerSubnet-azonosító a következőhöz hasonlóan néz ki:

`/subscriptions/<subscriptionID>/resourceGroups/RouteServerRG/providers/Microsoft.Network/virtualNetworks/myVirtualNetwork/subnets/RouteServerSubnet`

## <a name="create-the-route-server"></a>Az útvonal-kiszolgáló létrehozása

Hozza létre az útválasztási kiszolgálót a következő paranccsal:

```azurepowershell-interactive 
New-AzRouteServer -Name myRouteServer -ResourceGroupName RouteServerRG -Location "West US” -HostedSubnet “RouteServerSubnet_ID”
```

A helynek meg kell egyeznie a virtuális hálózat helyével. A HostedSubnet az előző szakaszban beszerzett RouteServerSubnet-azonosító.

## <a name="create-peering-with-an-nva"></a>Peering létrehozása NVA

A következő parancs használatával hozzon létre BGP-társat az útvonal-kiszolgálóról a NVA:

```azurepowershell-interactive 
Add-AzRouteServerPeer -PeerName "myNVA” -PeerIp “nva_ip” -PeerAsn “nva_asn” -RouteServerName "myRouteServer -ResourceGroupName ”RouteServerRG”
```

a "nva_ip" a NVA hozzárendelt virtuális hálózati IP-cím. a "nva_asn" a NVA konfigurált autonóm rendszer száma (ASN). Az ASN bármely 16 bites szám lehet, amely nem a 65515-65520-es tartományba esik. A ASN ezen tartományát a Microsoft foglalta le.

A következő parancs használatával állíthatja be a társítást különböző NVA vagy ugyanazon NVA egy másik példányával a redundancia érdekében:

```azurepowershell-interactive 
Add-AzRouteServerPeer -PeerName “NVA2_name” -PeerIp “nva2_ip” -PeerAsn “nva2_asn” -RouteServerName “myRouteServer” -ResourceGroupName “RouteServerRG” 
```

## <a name="complete-the-configuration-on-the-nva"></a>A konfiguráció befejezése a NVA

A NVA konfigurálásának befejezéséhez és a BGP-munkamenetek engedélyezéséhez szüksége lesz az Azure Route Server IP-címére és ASN-ra. Ezt az információt a következő paranccsal kérheti le:

```azurepowershell-interactive 
Get-AzRouteServer -RouterName “myRouteServer” -ResourceGroupName “RouteServerRG”
```

A kimenet a következő információkból áll:

``` 
RouteServerAsn : 65515
RouteServerIps : {10.5.10.4, 10.5.10.5}
```

## <a name="configure-route-exchange"></a><a name = "route-exchange"></a>Útvonal-Exchange konfigurálása

Ha egy ExpressRoute-átjáróval és egy Azure-beli VPN-átjáróval rendelkezik ugyanabban a VNet, és szeretné Exchange-útvonalakat cserélni, engedélyezheti az útválasztási Exchange használatát az Azure Route Serveren.

1. Az Azure Route Server és az átjáró (k) közötti útvonal-csere engedélyezéséhez használja a következő parancsot:

```azurepowershell-interactive 
Update-AzRouteServer -RouteServerName “myRouteServer” -ResourceGroupName “RouteServerRG” -AllowBranchToBranchTraffic 
```

2. Az Azure Route Server és az átjáró (k) közötti útválasztási váltás letiltásához használja a következő parancsot:

```azurepowershell-interactive 
Update-AzRouteServer -RouteServerName “myRouteServer” -ResourceGroupName “RouteServerRG” 
```

## <a name="troubleshooting"></a>Hibaelhárítás

Az Azure Route Server által hirdetett és fogadott útvonalakat az alábbi paranccsal tekintheti meg:

```azurepowershell-interactive
Get-AzRouteServerPeerAdvertisedRoute
Get-AzRouteServerPeerLearnedRoute
```
## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Ha már nincs szüksége az Azure Route Serverre, az alábbi parancsokkal távolítsa el a BGP-társat, majd távolítsa el az útválasztási kiszolgálót. 

1. Távolítsa el a BGP-társat az Azure Route Server és egy NVA között a következő paranccsal:

```azurepowershell-interactive 
Remove-AzRouteServerPeer -PeerName “nva_name” -RouteServerName “myRouteServer” -ResourceGroupName “RouteServerRG” 
```

2. Az Azure Route Server eltávolítása ezzel a paranccsal:

```azurepowershell-interactive 
Remove-AzRouteServer -RouteServerName “myRouteServer” -ResourceGroupName “RouteServerRG” 
```

## <a name="next-steps"></a>Következő lépések

Az Azure Route Server létrehozása után folytassa a további tudnivalókat arról, hogy az Azure Route Server hogyan kommunikál a ExpressRoute és a VPN-átjárókkal: 

> [!div class="nextstepaction"]
> [Azure ExpressRoute és Azure VPN-támogatás](expressroute-vpn-support.md)