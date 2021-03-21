---
title: Tudnivalók a ExpressRoute virtuális hálózati átjárókkal kapcsolatban – Azure | Microsoft Docs
description: A ExpressRoute virtuális hálózati átjáróinak megismerése. Ez a cikk az átjáró-SKU-ról és-típusokról tartalmaz információkat.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: duau
ms.openlocfilehash: 038e018a22af3546f5d3c66f6d8ee3963483cce1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102615057"
---
# <a name="about-expressroute-virtual-network-gateways"></a>Tudnivalók a ExpressRoute virtuális hálózati átjárókkal kapcsolatban

Az Azure-beli virtuális hálózat és a helyszíni hálózat ExpressRoute-on keresztüli összekapcsolásához először létre kell hoznia egy virtuális hálózati átjárót. A virtuális hálózati átjáró két célt szolgál: az Exchange IP-útvonalakat a hálózatok között, és átirányítja a hálózati forgalmat. Ez a cikk az átjárók típusait, az átjáró-SKU-ket és a becsült teljesítményt mutatja be SKU alapján. Ez a cikk a ExpressRoute [FastPath](#fastpath)is ismerteti, amely lehetővé teszi a helyszíni hálózatról érkező hálózati forgalmat a teljesítmény javítása érdekében a virtuális hálózati átjáró megkerülése érdekében.

## <a name="gateway-types"></a>Átjárótípusok

Virtuális hálózati átjáró létrehozásakor több beállítást kell megadnia. A kötelező beállítások egyike, a "-GatewayType", megadja, hogy az átjáró a ExpressRoute vagy a VPN-forgalomhoz van-e használva. A két átjáró típusa:

* **VPN** – titkosított forgalom küldése a nyilvános interneten keresztül, a "VPN" átjáró-típust használja. Ezt VPN-átjárónak is nevezzük. A hely–hely, pont–hely és a virtuális hálózatok közötti kapcsolat kapcsolatok mind VPN-átjárót használnak.

* **ExpressRoute** – a hálózati forgalom privát kapcsolatban való küldéséhez használja a "ExpressRoute" átjáró típusát. Ezt a ExpressRoute-átjárónak is nevezik, és a ExpressRoute konfigurálásakor használt átjáró típusa.

Mindegyik virtuális hálózat csak egy virtuális hálózati átjáróval rendelkezhet átjárótípusonként. Rendelkezhet például egy virtuális hálózati átjáróval, amely a -GatewayType Vpn típust, és egy másikkal, amelyik a -GatewayType ExpressRoute típust használja.

## <a name="gateway-skus"></a><a name="gwsku"></a>Átjáró-termékváltozatok
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Ha az átjárót egy nagyobb teljesítményű átjáró-SKU-ra szeretné frissíteni, akkor a legtöbb esetben használhatja az "átméretezés – AzVirtualNetworkGateway" PowerShell-parancsmagot. Ez a standard és a HighPerformance SKU-ra való frissítés esetén fog működni. A UltraPerformance SKU-ra való frissítéshez azonban újra létre kell hoznia az átjárót. Az átjárók újbóli létrehozása leállást okoz.

### <a name="estimated-performances-by-gateway-sku"></a><a name="aggthroughput"></a>Az átjárói SKU becsült teljesítménye
A következő táblázat az átjárók típusát és a becsült teljesítményt mutatja be. Ez a tábla a Resource Managerre és a klasszikus üzembe helyezési modellre is érvényes.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Az alkalmazás teljesítménye több tényezőtől függ, például a végpontok közötti késéstől, valamint az alkalmazás által megnyitott adatforgalom számától. A táblázatban szereplő számok azt a felső korlátot jelentik, amelyet az alkalmazás elméletileg érhet el ideális környezetben.
>
>

## <a name="gateway-subnet"></a><a name="gwsub"></a>Átjáró alhálózata

ExpressRoute-átjáró létrehozása előtt létre kell hoznia egy átjáró-alhálózatot. Az átjáró-alhálózat tartalmazza azokat az IP-címeket, amelyeket a Virtual Network Gateway-beli virtuális gépek és szolgáltatások használnak. A virtuális hálózati átjáró létrehozásakor az átjáróként működő virtuális gépek üzembe helyezése az átjáró alhálózatán történik, és a szükséges ExpressRoute-átjáró beállításaival van konfigurálva. Soha ne helyezzen üzembe semmilyen mást (például további virtuális gépeket) az átjáró-alhálózathoz. A megfelelő működéshez az átjáró alhálózatának "GatewaySubnet" nevűnek kell lennie. A "GatewaySubnet" átjáró alhálózatának elnevezése lehetővé teszi, hogy az Azure tudja, hogy ez az alhálózat a virtuális hálózati átjárók és a szolgáltatások üzembe helyezéséhez.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Az átjáróalhálózat létrehozásakor meg kell adnia, hogy hány IP-címet tartalmaz az alhálózat. Az átjáró-alhálózat IP-címei az átjáró virtuális gépei és az átjáró szolgáltatások számára vannak lefoglalva. Egyes konfigurációknak a többinél nagyobb számú IP-címre van szükségük. 

Az átjáró-alhálózat méretének tervezésekor tekintse meg a létrehozni kívánt konfiguráció dokumentációját. A ExpressRoute/VPN Gateway egyazon konfiguráció például nagyobb átjáró-alhálózatot igényel, mint a legtöbb más konfiguráció. Emellett érdemes lehet gondoskodni arról, hogy az átjáró-alhálózat elegendő IP-címet tartalmazzon a lehetséges jövőbeli további konfigurációkhoz. Míg az átjáró-alhálózatot kisebb as/29-ként is létrehozhatja, javasoljuk, hogy hozzon létre egy/27-ös vagy nagyobb átjáró-alhálózatot (/27,/26 stb.), ha a rendelkezésre álló címtartomány erre van szüksége. Ha Dual stack Gateway-alhálózatot hoz létre, javasoljuk, hogy a/64 vagy a nagyobb IPv6-tartományt is használja. Ez a legtöbb konfigurációt kielégíti.

A következő Resource Manager PowerShell-példa egy GatewaySubnet nevű átjáró-alhálózatot mutat be. Láthatja, hogy a CIDR jelölése egy/27, amely elegendő IP-címet biztosít a jelenleg létező konfigurációkhoz.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="zone-redundant-gateway-skus"></a><a name="zrgw"></a>Zóna – redundáns átjárók SKU-i

A ExpressRoute-átjárókat Azure Availability Zones is üzembe helyezheti. Ez fizikailag és logikailag elkülöníti őket különböző Availability Zones, így biztosítva a helyszíni hálózati kapcsolatot az Azure-hoz a zóna szintű hibák miatt.

![Zóna – redundáns ExpressRoute-átjáró](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

Zóna – a redundáns átjárók a ExpressRoute-átjáróhoz megadott új Gateway SKU-ket használnak.

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

Az új átjárói SKU-ket más üzembe helyezési lehetőségek is támogatják az igényeinek leginkább megfelelő módon. Ha az új átjárók használatával hoz létre virtuális hálózati átjárót, lehetősége van arra is, hogy az átjárót egy adott zónába telepítse. Ezt egy zóna-átjárónak nevezzük. Ha egy zóna-átjárót helyez üzembe, az átjáró összes példánya ugyanabban a rendelkezésre állási zónában lesz telepítve.

> [!IMPORTANT]
> Ha IPv6-alapú ExpressRoute-t szeretne használni, ügyeljen arra, hogy az az SKU-t válassza ki a kettős verem átjáró-alhálózatán üzembe helyezett átjáróhoz.
> 
>

## <a name="fastpath"></a><a name="fastpath"></a>FastPath

A ExpressRoute virtuális hálózati átjáró a hálózati útvonalak cseréjére és a hálózati forgalom irányítására szolgál. A FastPath úgy lett kialakítva, hogy javítsa a helyszíni hálózat és a virtuális hálózat adatelérési útjainak teljesítményét. Ha engedélyezve van, a FastPath közvetlenül a virtuális hálózatban lévő virtuális gépekre küldi a hálózati forgalmat, megkerülve az átjárót.

További információ a FastPath, beleértve a korlátozásokat és a követelményeket, lásd: [a FastPath névjegye](about-fastpath.md).

## <a name="rest-apis-and-powershell-cmdlets"></a><a name="resources"></a>REST API-k és PowerShell-parancsmagok
A REST API-k és a virtuális hálózati átjárók konfigurációinak PowerShell-parancsmagok használata esetén a következő lapokon talál további technikai forrásokat és konkrét szintaxisi követelményeket:

| **Klasszikus** | **Resource Manager** |
| --- | --- |
| [PowerShell](/powershell/module/servicemanagement/azure.service/#azure) |[PowerShell](/powershell/module/az.network#networking) |
| [REST API](/previous-versions/azure/reference/jj154113(v=azure.100)) |[REST API](/rest/api/virtual-network/) |

## <a name="next-steps"></a>Következő lépések

További információ az elérhető kapcsolatok konfigurációjáról: [ExpressRoute – áttekintés](expressroute-introduction.md).

A ExpressRoute-átjárók létrehozásával kapcsolatos további információkért lásd: [virtuális hálózati átjáró létrehozása a ExpressRoute](expressroute-howto-add-gateway-resource-manager.md).

A zóna redundáns átjárók konfigurálásával kapcsolatos további információkért lásd: [zóna – redundáns virtuális hálózati átjáró létrehozása](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md).

A FastPath kapcsolatos további információkért lásd: [a FastPath névjegye](about-fastpath.md).