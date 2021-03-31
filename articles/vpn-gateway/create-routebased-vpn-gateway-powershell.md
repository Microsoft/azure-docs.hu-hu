---
title: 'Azure VPN Gateway: Route-alapú átjáró létrehozása: PowerShell'
description: A PowerShell használatával gyorsan létrehozhat egy Route-alapú Azure VPN-átjárót a helyszíni hálózathoz való VPN-kapcsolathoz vagy a virtuális hálózatok összekapcsolásához.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 8b6ab5dcbd38925afbd98381e427426d27110a53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89393513"
---
# <a name="create-a-route-based-vpn-gateway-using-powershell"></a>Route-alapú VPN-átjáró létrehozása a PowerShell használatával

Ez a cikk segítséget nyújt egy Route-alapú Azure VPN-átjáró gyors létrehozásához a PowerShell használatával. VPN-átjárót a helyszíni hálózathoz való VPN-kapcsolat létrehozásakor használ a rendszer. VPN-átjárót is használhat a virtuális hálózatok összekapcsolásához.

## <a name="before-you-begin"></a>Előkészületek

A cikkben szereplő lépések egy VNet, egy alhálózatot, egy átjáró-alhálózatot és egy Route-alapú VPN-átjárót (virtuális hálózati átjárót) hoznak létre. Miután az átjáró létrehozása befejeződött, létrehozhat kapcsolatokat. Ezeknek a lépéseknek Azure-előfizetésre van szükségük. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

### <a name="working-with-azure-powershell"></a>A Azure PowerShell használata

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy Azure-erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozzon létre egy erőforráscsoportot. Ha helyileg futtatja a PowerShellt, nyissa meg emelt szintű jogosultságokkal a PowerShell-konzolt, és kapcsolódjon az Azure-hoz a `Connect-AzAccount` parancs használatával.

```azurepowershell-interactive
New-AzResourceGroup -Name TestRG1 -Location EastUS
```

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Virtuális hálózat létrehozása

Hozzon létre egy új virtuális hálózatot a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). A következő példában létrehozunk egy **VNet1** nevű virtuális hálózatot a **EastUS** helyen:

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName TestRG1 `
  -Location EastUS `
  -Name VNet1 `
  -AddressPrefix 10.1.0.0/16
```

Hozzon létre egy alhálózat-konfigurációt a [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) parancsmag használatával.

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Frontend `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Állítsa be a virtuális hálózat alhálózati konfigurációját a [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork) parancsmag használatával.


```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="add-a-gateway-subnet"></a><a name="gwsubnet"></a>Átjáróalhálózat hozzáadása

Az átjáró-alhálózat tartalmazza azokat a fenntartott IP-címeket, amelyeket a Virtual Network Gateway Services használ. Átjáró-alhálózat hozzáadásához használja az alábbi példákat:

Állítson be egy változót a VNet.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -ResourceGroupName TestRG1 -Name VNet1
```

Hozza létre az átjáró-alhálózatot az [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-azVirtualNetworkSubnetConfig) parancsmag használatával.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $vnet
```

Állítsa be a virtuális hálózat alhálózati konfigurációját a [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork) parancsmag használatával.

```azurepowershell-interactive
$vnet | Set-AzVirtualNetwork
```

## <a name="request-a-public-ip-address"></a><a name="PublicIP"></a>Nyilvános IP-cím kérése

A VPN-átjárónak dinamikusan lefoglalt nyilvános IP-címmel kell rendelkeznie. Amikor létrehoz egy VPN-átjáróval létesített csatlakozást, ez a megadott IP-cím. Nyilvános IP-cím igényléséhez használja a következő példát:

```azurepowershell-interactive
$gwpip= New-AzPublicIpAddress -Name VNet1GWIP -ResourceGroupName TestRG1 -Location 'East US' -AllocationMethod Dynamic
```

## <a name="create-the-gateway-ip-address-configuration"></a><a name="GatewayIPConfig"></a>Átjáró IP-címének konfigurációjának létrehozása

Az átjáró konfigurációja meghatározza az alhálózatot és a használandó nyilvános IP-címet. A következő példa használatával hozza létre az átjáró konfigurációját.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
```
## <a name="create-the-vpn-gateway"></a><a name="CreateGateway"></a>A VPN-átjáró létrehozása

A VPN-átjáró létrehozása több mint 45 percet is igénybe vehet. Miután az átjáró befejeződött, létrehozhat egy kapcsolatot a virtuális hálózat és egy másik VNet között. Vagy hozzon létre kapcsolatot a virtuális hálózat és a helyszíni hely között. Hozzon létre egy VPN-átjárót a [New-AzVirtualNetworkGateway](/powershell/module/az.network/New-azVirtualNetworkGateway) parancsmag használatával.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1
```

## <a name="view-the-vpn-gateway"></a><a name="viewgw"></a>A VPN-átjáró megtekintése

A VPN-átjáró a [Get-AzVirtualNetworkGateway](/powershell/module/az.network/Get-azVirtualNetworkGateway) parancsmag használatával tekinthető meg.

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -Name Vnet1GW -ResourceGroup TestRG1
```

A kimenet a következő példához hasonlóan fog kinézni:

```
Name                   : VNet1GW
ResourceGroupName      : TestRG1
Location               : eastus
Id                     : /subscriptions/<subscription ID>/resourceGroups/TestRG1/provide
                         rs/Microsoft.Network/virtualNetworkGateways/VNet1GW
Etag                   : W/"0952d-9da8-4d7d-a8ed-28c8ca0413"
ResourceGuid           : dc6ce1de-2c4494-9d0b-20b03ac595
ProvisioningState      : Succeeded
Tags                   :
IpConfigurations       : [
                           {
                             "PrivateIpAllocationMethod": "Dynamic",
                             "Subnet": {
                               "Id": "/subscriptions/<subscription ID>/resourceGroups/Te
                         stRG1/providers/Microsoft.Network/virtualNetworks/VNet1/subnets/GatewaySubnet"
                             },
                             "PublicIpAddress": {
                               "Id": "/subscriptions/<subscription ID>/resourceGroups/Te
                         stRG1/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP"
                             },
                             "Name": "default",
                             "Etag": "W/\"0952d-9da8-4d7d-a8ed-28c8ca0413\"",
                             "Id": "/subscriptions/<subscription ID>/resourceGroups/Test
                         RG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/de
                         fault"
                           }
                         ]
GatewayType            : Vpn
VpnType                : RouteBased
EnableBgp              : False
ActiveActive           : False
GatewayDefaultSite     : null
Sku                    : {
                           "Capacity": 2,
                           "Name": "VpnGw1",
                           "Tier": "VpnGw1"
                         }
VpnClientConfiguration : null
BgpSettings            : {
     
```

## <a name="view-the-public-ip-address"></a><a name="viewgwpip"></a>A nyilvános IP-cím megtekintése

A VPN-átjáró nyilvános IP-címének megtekintéséhez használja a [Get-AzPublicIpAddress](/powershell/module/az.network/Get-azPublicIpAddress) parancsmagot.

```azurepowershell-interactive
Get-AzPublicIpAddress -Name VNet1GWIP -ResourceGroupName TestRG1
```

A példában szereplő válaszban az IP-cím értéke a nyilvános IP-cím.

```
Name                     : VNet1GWIP
ResourceGroupName        : TestRG1
Location                 : eastus
Id                       : /subscriptions/<subscription ID>/resourceGroups/TestRG1/provi
                           ders/Microsoft.Network/publicIPAddresses/VNet1GWIP
Etag                     : W/"5001666a-bc2a-484b-bcf5-ad488dabd8ca"
ResourceGuid             : 3c7c481e-9828-4dae-abdc-f95b383
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Dynamic
IpAddress                : 13.90.153.3
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                             "Id": "/subscriptions/<subscription ID>/resourceGroups/Test
                           RG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/
                           default"
                           }
DnsSettings              : null
Zones                    : {}
Sku                      : {
                             "Name": "Basic"
                           }
IpTags                   : {}
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a létrehozott erőforrásokra, használja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsot az erőforráscsoport törléséhez. Ez a parancs törli az erőforráscsoportot és a benne lévő összes erőforrást.

```azurepowershell-interactive
Remove-AzResourceGroup -Name TestRG1
```

## <a name="next-steps"></a>Következő lépések

Miután az átjáró elkészült, létrehozhat egy kapcsolatot a virtuális hálózat és egy másik VNet között. Vagy hozzon létre kapcsolatot a virtuális hálózat és a helyszíni hely között.

> [!div class="nextstepaction"]
> [Helyek közötti kapcsolat létrehozása](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Pont–hely kapcsolat létrehozása](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Kapcsolatok létrehozása másik VNet](vpn-gateway-vnet-vnet-rm-ps.md)
