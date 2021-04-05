---
title: 'Azure VPN Gateway: konfigurációs beállítások'
description: A Resource Manager-alapú üzemi modellben létrehozott virtuális hálózatok VPN Gateway erőforrásainak és beállításainak megismerése.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: cherylmc
ms.openlocfilehash: 1aba87b2139fb8a7d395fb3180d2074e47310fa9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010816"
---
# <a name="about-vpn-gateway-configuration-settings"></a>Tudnivalók a VPN Gateway konfigurációs beállításairól

A VPN Gateway olyan virtuális hálózati átjáró, amely titkosított forgalmat küld a virtuális hálózat és a helyszíni hely között egy nyilvános kapcsolaton keresztül. A VPN Gateway használatával a virtuális hálózatok közötti adatforgalom az Azure-gerincen keresztül is elküldhető.

A VPN Gateway-kapcsolatok több erőforrás konfigurációján alapulnak, amelyek mindegyike konfigurálható beállításokat tartalmaz. A jelen cikk fejezetei a Resource Manager-alapú üzemi modellben létrehozott virtuális hálózatok VPN Gateway-hez kapcsolódó erőforrásait és beállításait tárgyalják. Az egyes csatlakoztatási megoldásokhoz tartozó leírások és topológiai diagramok a [névjegy VPN Gateway](vpn-gateway-about-vpngateways.md) cikkben találhatók.

A cikkben szereplő értékek a VPN-átjárókat alkalmazzák (a virtuális hálózati átjárókat, amelyek a-GatewayType VPN-t használják). Ez a cikk nem fedi le az összes átjáró típusát vagy a zóna redundáns átjáróit.

* A GatewayType "ExpressRoute" értékre vonatkozó értékekért lásd: [Virtual Network átjárók a ExpressRoute](../expressroute/expressroute-about-virtual-network-gateways.md).

* A zónákra redundáns átjárók esetében lásd: [a zóna – redundáns átjárók](about-zone-redundant-vnet-gateways.md).

* Virtuális WAN esetén lásd: [a virtuális WAN ismertetése](../virtual-wan/virtual-wan-about.md).

## <a name="gateway-types"></a><a name="gwtype"></a>Átjárótípusok

Minden virtuális hálózatnak csak egy virtuális hálózati átjárója lehet. Ha virtuális hálózati átjárót hoz létre, meg kell győződnie arról, hogy az átjáró típusa helyes a konfigurációhoz.

A-GatewayType elérhető értékei a következők:

* Vpn
* ExpressRoute

A VPN-átjáróhoz a `-GatewayType` *VPN* szükséges.

Példa:

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased
```

## <a name="gateway-skus"></a><a name="gwsku"></a>Átjáró-termékváltozatok

[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configure-a-gateway-sku"></a>Átjáró SKU konfigurálása

**Azure Portal**

Ha a Azure Portal használatával hoz létre Resource Manager virtuális hálózati átjárót, akkor a legördülő listából kiválaszthatja az átjáró SKU-t. A megjelenített beállítások az átjáró típusa és a választott VPN-típus szerint jelennek meg.

**PowerShell**

A következő PowerShell-példa az `-GatewaySku` as VpnGw1 adja meg. Ha a PowerShell-lel átjárót hoz létre, először létre kell hoznia az IP-konfigurációt, majd egy változót kell használnia. Ebben a példában a konfigurációs változó $gwipconfig.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'US East' -IpConfigurations $gwipconfig -GatewaySku VpnGw1 `
-GatewayType Vpn -VpnType RouteBased
```

**Azure CLI**

```azurecli
az network vnet-gateway create --name VNet1GW --public-ip-address VNet1GWPIP --resource-group TestRG1 --vnet VNet1 --gateway-type Vpn --vpn-type RouteBased --sku VpnGw1 --no-wait
```

###  <a name="resizing-or-changing-a-sku"></a><a name="resizechange"></a>SKU átméretezése vagy módosítása

Ha VPN-átjáróval rendelkezik, és egy másik átjáró-SKU-t szeretne használni, a lehetőségek az átjáró SKU-jának átméretezése, vagy egy másik SKU-ra való váltás. Ha másik átjáró SKU-ra vált, a meglévő átjárót teljesen törölni kell, és újat kell létrehoznia. Egy átjáró akár 45 percet is igénybe vehet. Összehasonlításban az átjáró SKU-jának átméretezése nem sok állásidőt eredményez, mert nem kell törölnie és újraépítenie az átjárót. Ha azt szeretné, hogy az átjáró SKU-jának átméretezése megtörténjen, és nem kell megváltoztatnia. Vannak azonban szabályok az átméretezéssel kapcsolatban:

1. Az alapszintű SKU kivételével átméretezheti a VPN Gateway SKU-t egy másik VPN Gateway SKU-ra ugyanazon a generáción belül (Generation1 vagy Generation2). Például a Generation1 VpnGw1 átméretezhetők a Generation1 VpnGw2, de a VpnGw2 nem Generation2.
2. Ha a régi átjárók termékváltozataival dolgozik, az átméretezéskor az Alapszintű, a Standard és a Nagy teljesítményű termékváltozatok közül választhat.
3. Az alapszintű/standard/HighPerformance SKU-ról VpnGw SKU-ra **nem** lehet átméretezni. Ehelyett az új SKU-ra kell [váltania](#change) .

#### <a name="to-resize-a-gateway"></a><a name="resizegwsku"></a>Átjáró átméretezése

**Azure Portal**

[!INCLUDE [Resize a SKU - portal](../../includes/vpn-gateway-resize-gw-portal-include.md)]

**PowerShell**

[!INCLUDE [Resize a SKU](../../includes/vpn-gateway-gwsku-resize-include.md)]

####  <a name="to-change-from-an-old-legacy-sku-to-a-new-sku"></a><a name="change"></a>Váltás régi (örökölt) SKU-ról egy új SKU-ra

[!INCLUDE [Change a SKU](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="connection-types"></a><a name="connectiontype"></a>Kapcsolattípusok

A Resource Manager-alapú üzemi modellben minden konfigurációhoz egy adott virtuális hálózati átjáró kapcsolódási típus szükséges. A `-ConnectionType` lehetséges Resource Manager PowerShell-értékei a következők:

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

A következő PowerShell-példában egy olyan S2S-kapcsolat jön létre, amely megköveteli a kapcsolat típusát ( *IPSec*).

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

## <a name="vpn-types"></a><a name="vpntype"></a>VPN-típusok

Amikor létrehoz egy VPN Gateway-konfigurációhoz tartozó virtuális hálózati átjárót, meg kell adnia egy VPN-típust. A választott VPN-típus a létrehozni kívánt kapcsolati topológiától függ. Egy P2S-kapcsolathoz például Útvonalalapú VPN-típus szükséges. A VPN-típus a használt hardvertől is függhet. A S2S-konfigurációkhoz VPN-eszköz szükséges. Egyes VPN-eszközök csak bizonyos VPN-típusokat támogatnak.

A kiválasztott VPN-típusnak meg kell felelnie a létrehozni kívánt megoldáshoz tartozó összes kapcsolati követelménynek. Ha például egy S2S VPN Gateway-kapcsolatra és egy P2S VPN Gateway-kapcsolatra van szüksége ugyanahhoz a virtuális hálózathoz, akkor a *útvonalalapú* VPN-típust kell használnia, mert a P2S útvonalalapú VPN-típust igényel. Azt is ellenőriznie kell, hogy a VPN-eszköz támogatja-e a Útvonalalapú VPN-kapcsolat használatát. 

A virtuális hálózati átjáró létrehozása után a VPN-típus nem módosítható. Törölnie kell a virtuális hálózati átjárót, és létre kell hoznia egy újat. Kétféle VPN-típus létezik:

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

A következő PowerShell-példa az `-VpnType` as *útvonalalapú* adja meg. Egy átjáró létrehozásakor biztosítania kell, hogy -VpnType megfeleljen a konfigurációnak.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig `
-GatewayType Vpn -VpnType RouteBased
```

## <a name="gateway-requirements"></a><a name="requirements"></a>Az átjáróra vonatkozó követelmények

[!INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## <a name="gateway-subnet"></a><a name="gwsub"></a>Átjáró alhálózata

A VPN-átjáró létrehozása előtt létre kell hoznia egy átjáró-alhálózatot. Az átjáró-alhálózat tartalmazza azokat az IP-címeket, amelyeket a Virtual Network Gateway-beli virtuális gépek és szolgáltatások használnak. A virtuális hálózati átjáró létrehozásakor az átjáróként működő virtuális gépek üzembe helyezése az átjáró alhálózatán történik, és a VPN-átjáró szükséges beállításaival van konfigurálva. Soha ne helyezzen üzembe semmilyen mást (például további virtuális gépeket) az átjáró-alhálózathoz. A megfelelő működéshez az átjáró alhálózatának "GatewaySubnet" nevűnek kell lennie. A "GatewaySubnet" átjáró alhálózatának elnevezése lehetővé teszi, hogy az Azure tudja, hogy ez az alhálózat a virtuális hálózati átjárók és a szolgáltatások üzembe helyezéséhez.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Az átjáróalhálózat létrehozásakor meg kell adnia, hogy hány IP-címet tartalmaz az alhálózat. Az átjáró-alhálózat IP-címei az átjáró virtuális gépei és az átjáró szolgáltatások számára vannak lefoglalva. Egyes konfigurációknak a többinél nagyobb számú IP-címre van szükségük. 

Az átjáró-alhálózat méretének tervezésekor tekintse meg a létrehozni kívánt konfiguráció dokumentációját. A ExpressRoute/VPN Gateway egyazon konfiguráció például nagyobb átjáró-alhálózatot igényel, mint a legtöbb más konfiguráció. Emellett érdemes lehet gondoskodni arról, hogy az átjáró-alhálózat elegendő IP-címet tartalmazzon a lehetséges jövőbeli további konfigurációkhoz. Míg az átjáró-alhálózatot kisebb as/29-ként is létrehozhatja, javasoljuk, hogy hozzon létre egy/27-ös vagy nagyobb átjáró-alhálózatot (/27,/26 stb.), ha a rendelkezésre álló címtartomány erre van szüksége. Ez a legtöbb konfigurációt kielégíti.

A következő Resource Manager PowerShell-példa egy GatewaySubnet nevű átjáró-alhálózatot mutat be. Láthatja, hogy a CIDR jelölése egy/27, amely elegendő IP-címet biztosít a jelenleg létező konfigurációkhoz.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="local-network-gateways"></a><a name="lng"></a>Helyi hálózati átjárók

A helyi hálózati átjáró eltér a virtuális hálózati átjárótól. A VPN-átjárók konfigurációjának létrehozásakor a helyi hálózati átjáró általában a helyszíni hálózatot és a megfelelő VPN-eszközt jelöli. A klasszikus üzembe helyezési modellben a helyi hálózati átjárót a Helyi névvel illettük.

Adja meg a helyi hálózati átjáró nevét, a nyilvános IP-címet vagy a helyszíni VPN-eszköz teljes tartománynevét (FQDN), és adja meg a helyszíni helyen található címek előtagjait. Az Azure a hálózati forgalomhoz tartozó célcím előtagjait tekinti át, a helyi hálózati átjáróhoz megadott konfigurációt, és ennek megfelelően irányítja a csomagokat. Ha Border Gateway Protocolt (BGP) használ a VPN-eszközön, meg kell adnia a VPN-eszköz BGP-társ IP-címét és a helyszíni hálózatának autonóm rendszerbeli számát (ASN). Helyi hálózati átjárókat is megadhat a VPN Gateway-kapcsolatokat használó VNet-VNet konfigurációkhoz.

A következő PowerShell-példa egy új helyi hálózati átjárót hoz létre:

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

Előfordulhat, hogy módosítania kell a helyi hálózati átjáró beállításait. Például a címtartomány hozzáadásakor vagy módosításakor, vagy a VPN-eszköz IP-címének megváltozásakor. Lásd: [helyi hálózati átjáró beállításainak módosítása a PowerShell használatával](vpn-gateway-modify-local-network-gateway.md).

## <a name="rest-apis-powershell-cmdlets-and-cli"></a><a name="resources"></a>REST API-k, PowerShell-parancsmagok és parancssori felület

A REST API-k, a PowerShell-parancsmagok vagy az Azure CLI VPN Gateway konfigurációkhoz való használata esetén a következő lapokon talál további technikai forrásokat és konkrét szintaxisi követelményeket:

| **Klasszikus** | **Resource Manager** |
| --- | --- |
| [PowerShell](/powershell/module/az.network/#networking) |[PowerShell](/powershell/module/az.network#vpn) |
| [REST API](/previous-versions/azure/reference/jj154113(v=azure.100)) |[REST API](/rest/api/network/virtualnetworkgateways) |
| Nem támogatott | [Azure CLI](/cli/azure/network/vnet-gateway)|

## <a name="next-steps"></a>Következő lépések

További információ az elérhető kapcsolatok konfigurációjáról: [Tudnivalók a VPN Gatewayról](vpn-gateway-about-vpngateways.md).