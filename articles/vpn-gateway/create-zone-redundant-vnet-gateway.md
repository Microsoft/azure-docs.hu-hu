---
title: Zóna – redundáns virtuális hálózati átjáró létrehozása Azure Availability Zones
description: Megtudhatja, hogyan helyezhet üzembe zónában redundáns VPN-átjárókat és ExpressRoute átjárókat Azure Availability Zones.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 2eaf1470e2d861ecfc1c1bc96f6040a1c3e0a644
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89425228"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones"></a>Zóna – redundáns virtuális hálózati átjáró létrehozása Azure Availability Zones

A VPN-és ExpressRoute-átjárók üzembe helyezhetők a Azure Availability Zonesban. Ez rugalmasságot, méretezhetőséget és magasabb szintű rendelkezésre állást biztosít a virtuális hálózati átjárók számára. Az átjárók Azure-beli rendelkezésre állási zónákban történő üzembe helyezésével fizikailag és logikailag is elválaszthatók a régióban található átjárók, miközben az Azure-ral létesített helyszíni hálózati kapcsolat megvédhető a zónaszintű hibáktól. További információ:  [a zóna-redundáns virtuális hálózati átjárók](about-zone-redundant-vnet-gateways.md) és [a Azure Availability Zones](../availability-zones/az-overview.md).

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="1-declare-your-variables"></a><a name="variables"></a>1. a változók deklarálása

Deklarálja a használni kívánt változókat. Használja a következő példát, és szükség szerint cserélje le az értékeket a sajátjaira. Ha a gyakorlat során bármikor lezárta a PowerShell-vagy Cloud Shell-munkamenetet, csak másolja és illessze be újra az értékeket a változók újbóli bejelentéséhez. A hely megadásakor ellenőrizze, hogy a megadott régió támogatott-e. További információt a [Gyakori kérdések](#faq)című témakörben talál.

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "CentralUS"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="2-create-the-virtual-network"></a><a name="configure"></a>2. a virtuális hálózat létrehozása

Hozzon létre egy erőforráscsoportot.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

Hozzon létre egy virtuális hálózatot.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$vnet = New-AzVirtualNetwork -Name $VNet1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNet1Prefix -Subnet $fesub1,$besub1
```

## <a name="3-add-the-gateway-subnet"></a><a name="gwsub"></a>3. az átjáró alhálózatának hozzáadása

Az átjáró-alhálózat tartalmazza azokat a fenntartott IP-címeket, amelyeket a Virtual Network Gateway Services használ. Az alábbi példákat követve adhat hozzá és állíthat be átjáró-alhálózatot:

Adja hozzá az átjáró alhálózatát.

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

Állítsa be a virtuális hálózat átjáró-alhálózatának konfigurációját.

```azurepowershell-interactive
$getvnet | Set-AzVirtualNetwork
```
## <a name="4-request-a-public-ip-address"></a><a name="publicip"></a>4. nyilvános IP-cím kérése
 
Ebben a lépésben válassza ki a létrehozni kívánt átjáróra vonatkozó utasításokat. Az átjárók telepítésére szolgáló zónák kiválasztása a nyilvános IP-címhez megadott zónáktól függ.

### <a name="for-zone-redundant-gateways"></a><a name="ipzoneredundant"></a>A Zone-redundáns átjárók esetében

Igényeljen egy nyilvános IP-címet **standard** PublicIpaddress SKU-val, és ne határozzon meg zónát. Ebben az esetben a létrehozott szabványos nyilvános IP-cím egy zóna-redundáns nyilvános IP-cím lesz.   

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard
```

### <a name="for-zonal-gateways"></a><a name="ipzonalgw"></a>A zóna-átjárók esetében

Igényeljen egy nyilvános IP-címet egy **standard** PublicIpaddress SKU-val. Határozza meg a zónát (1, 2 vagy 3). Ebben a zónában minden átjáró-példány üzembe lesz helyezve.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard -Zone 1
```

### <a name="for-regional-gateways"></a><a name="ipregionalgw"></a>Regionális átjárók esetén

Igényeljen egy nyilvános IP-címet egy **Alapszintű** PublicIpaddress SKU-val. Ebben az esetben az átjáró regionális átjáróként van telepítve, és nincs olyan zónája, amely az átjáróhoz van építve. Az átjáró példányai bármely zónában létrejönnek.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="5-create-the-ip-configuration"></a><a name="gwipconfig"></a>5. az IP-konfiguráció létrehozása

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="6-create-the-gateway"></a><a name="gwconfig"></a>6. az átjáró létrehozása

Hozza létre a virtuális hálózati átjárót.

### <a name="for-expressroute"></a>ExpressRoute

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute -GatewaySku ErGw1AZ
```

### <a name="for-vpn-gateway"></a>VPN Gateway

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1AZ
```

## <a name="faq"></a><a name="faq"></a>GYIK

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Mi változik, ha az új SKU-t üzembe helyezem?

Az átjárók az Ön szemszögéből is üzembe helyezhetők a zóna-redundanciával. Ez azt jelenti, hogy az átjárók összes példánya Azure Availability Zones lesz telepítve, és az egyes rendelkezésre állási zónák egy másik hiba-és frissítési tartomány. Így az átjárók megbízhatóbbak, elérhetők és rugalmasak lesznek a zónák meghibásodása miatt.

### <a name="can-i-use-the-azure-portal"></a>Használhatom a Azure Portal?

Igen, a Azure Portal használatával telepítheti az új SKU-t. Ezeket az új SKU-ket azonban csak azokban az Azure-régiókban fogja látni, amelyek Azure Availability Zones rendelkeznek.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Milyen régiókban érhetők el az új SKU-i használata?

Tekintse meg az elérhető régiók legújabb listáját [Availability Zones](../availability-zones/az-region.md) .

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Módosíthatom/telepíthetem vagy frissíthetem a meglévő virtuális hálózati átjárókat a zóna-redundáns vagy a zónák közötti átjáróra?

Jelenleg nem támogatott a meglévő virtuális hálózati átjárók áttelepítése a zóna-redundáns vagy a zónák közötti átjáróra. Azonban törölheti a meglévő átjárót, és újból létrehozhatja a zóna-redundáns vagy a zónákhoz tartozó átjárót.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Telepíthetek VPN-és Express Route Gateway-átjárókat is ugyanazon a virtuális hálózaton?

Az azonos virtuális hálózatban található VPN-és Express Route-átjárók együttes létezése is támogatott. Az átjáró-alhálózat esetében azonban foglaljon le egy/27 IP-címtartományt.
