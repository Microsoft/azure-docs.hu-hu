---
title: Tudnivalók az Azure VPN Gateway
description: Megtudhatja, mi a VPN Gateway, és hogyan használható VPN Gateway az IPsec IKE-helyek közötti, a VNet és a VNet, valamint pont – hely VPN virtuális hálózatokhoz való csatlakozáshoz.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: overview
ms.date: 08/27/2020
ms.author: cherylmc
ms.custom:
- contperf-fy21q1
- e2e-hybrid
ms.openlocfilehash: b4a2e1b80fe62c5e8f74e35a1a7f61bdddf89c53
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066534"
---
# <a name="what-is-vpn-gateway"></a>Mi az a VPN-átjáró?

A VPN-átjáró a virtuális hálózati átjárók egy olyan típusa, amely titkosított adatforgalmat továbbít a nyilvános interneten keresztül egy Azure virtuális hálózat és egy helyszíni hely között. A VPN-átjáróval továbbá titkosított adatforgalmat továbbíthat az Azure-alapú virtuális hálózatok között a Microsoft hálózatán keresztül. Minden egyes virtuális hálózat kizárólag egy VPN-átjáróval rendelkezhet. Egy VPN-átjáróhoz azonban létrehozhat több kapcsolatot is. Amikor többhelyes csatlakozást hoz létre egyetlen VPN-átjáróhoz, az összes VPN-alagút az elérhető sávszélességen osztozkodik.

## <a name="what-is-a-virtual-network-gateway"></a><a name="whatis"></a>Mi az a virtuális hálózati átjáró?

A virtuális hálózati átjárók két vagy több, az *átjáró alhálózatának* nevezett alhálózatra telepített VM-ből állnak. A virtuális hálózati átjáró virtuális gépei útválasztási táblákat tartalmaznak, és adott átjáró szolgáltatásokat futtatnak. Ezek a virtuális gépek a virtuális hálózati átjáró létrehozásakor jönnek létre. A virtuális hálózati átjáró részét képező virtuális gépeket nem lehet közvetlenül konfigurálni.

Virtuális hálózati átjáró konfigurálásakor az átjáró típusát megadó beállítást kell konfigurálnia. Az átjáró típusa határozza meg a virtuális hálózati átjáró használatának módját és az átjáró által végrehajtott műveleteket. A "VPN" átjáró azt adja meg, hogy a létrehozott virtuális hálózati átjáró típusa "VPN Gateway". Ez megkülönbözteti azt egy ExpressRoute-átjárótól, amely egy másik átjáró-típust használ. A virtuális hálózatok két virtuális hálózati átjáróval rendelkezhetnek; egy VPN-átjáró és egy ExpressRoute-átjáró. További információért lásd: [Átjárótípusok](vpn-gateway-about-vpn-gateway-settings.md#gwtype).

Egy virtuális hálózati átjáró létrehozása akár 45 percet is igénybe vehet. Virtuális hálózati átjáró létrehozásakor a rendszer telepíti az átjáró virtuális gépeit az átjáróalhálózatra, és konfigurálja őket az Ön által megadott beállításokkal. Miután létrehozott egy VPN-átjárót, létrehozhat egy IPsec/IKE VPN-alagútkapcsolatot az adott VPN-átjáró és egy másik VPN-átjáró (VNet–VNet) között, vagy létrehozhat egy létesítmények közötti IPsec/IKE VPN-alagútkapcsolatot a VPN-átjáró és egy helyszíni VPN-eszköz között (helyek között). Létrehozhat egy pont – hely típusú VPN-kapcsolatot (az OpenVPN, a IKEv2 vagy az SSTP protokollon keresztül), amely lehetővé teszi, hogy távoli helyről, például egy konferenciáról vagy otthonról csatlakozhasson a virtuális hálózathoz.

## <a name="configuring-a-vpn-gateway"></a><a name="configuring"></a>VPN Gateway átjáró konfigurálása

A VPN-átjárós kapcsolatok több erőforrást használnak, amelyek speciális beállításokkal konfigurálhatók. Az erőforrások többsége külön konfigurálható, néhány erőforrást azonban egy bizonyos sorrendben kell konfigurálni.

### <a name="design"></a><a name="diagrams"></a>Tervezés

Fontos szem előtt tartani, hogy a VPN-átjárós kapcsolatokhoz különböző konfigurációk érhetők el. Önnek kell eldöntenie, melyik konfiguráció felel meg a legjobban az igényeinek. Például pont – hely, helyek közötti, valamint egyidejű ExpressRoute-vagy helyek közötti kapcsolatok mind eltérő utasításokkal és konfigurációs követelményekkel rendelkeznek. További információ a tervezési és a kapcsolódási topológiai diagramok megtekintéséhez: [tervezés](design.md).

### <a name="planning-table"></a><a name="planningtable"></a>Tervezési táblázat

Az alábbi táblázat segíthet eldönteni, melyik az Ön megoldásához legmegfelelőbb kapcsolat.

[!INCLUDE [cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

### <a name="settings"></a><a name="settings"></a>Beállítások

Az egyes erőforrások megfelelő beállítása kritikus fontosságú a sikeres kapcsolat létrehozásához. További információ a VPN Gateway önálló erőforrásairól és beállításairól: [About VPN Gateway settings](vpn-gateway-about-vpn-gateway-settings.md) (Információk a VPN Gateway beállításairól). A cikk az átjáró-, a VPN- és a kapcsolattípusokkal, az átjáró-alhálózatokkal, a helyi hálózati átjárókkal, az átjáró-termékváltozatokkal, valamint a különböző erőforrások beállításaival kapcsolatos további hasznos információkat tartalmaz.

### <a name="deployment-tools"></a><a name="tools"></a>Üzembehelyezési eszközök

Az erőforrások létrehozásának és konfigurálásának megkezdéséhez használjon egy konfigurációs eszközt, például az Azure Portalt. A további erőforrások konfigurálásához, vagy adott esetekben a létező erőforrások módosításához később átválthat egy másik eszközre, például a PowerShellre. Jelenleg nem lehet a minden erőforrást és erőforrás-beállítást az Azure Portalon konfigurálni. Az egyes kapcsolati topológiákhoz tartozó cikkekben lévő utasítások egyértelműsítik, hogy mikor van szükség egy speciális konfigurációs eszközre.

## <a name="gateway-skus"></a><a name="gwsku"></a>Átjáró-termékváltozatok

Egy virtuális hálózati átjáró létrehozásakor meg kell adnia a használni kívánt termékváltozatot. Válassza ki a számítási feladatok, az átviteli sebesség, a funkciók és a szolgáltatói szerződés igényeinek megfelelő termékváltozatot.

* További információ az átjárók SKU-ról, beleértve a támogatott szolgáltatásokat, a termelési és fejlesztési-tesztelési és konfigurációs lépéseket a [VPN Gateway Settings-Gateway SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku) -ról szóló cikkben talál.
* Örökölt SKU-információk: az [örökölt SKU-használat használata](vpn-gateway-about-skus-legacy.md).

### <a name="gateway-skus-by-tunnel-connection-and-throughput"></a><a name="benchmark"></a>Átjáró-termékváltozatok alagút, kapcsolat és átviteli sebesség szerint

[!INCLUDE [Aggregated throughput by SKU](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="availability-zones"></a><a name="availability"></a>Rendelkezésre állási zónák

A VPN-átjárók üzembe helyezhetők Azure Availability Zonesban. Ez rugalmasságot, méretezhetőséget és magasabb szintű rendelkezésre állást biztosít a virtuális hálózati átjárók számára. Az átjárók Azure-beli rendelkezésre állási zónákban történő üzembe helyezésével fizikailag és logikailag is elválaszthatók a régióban található átjárók, miközben az Azure-ral létesített helyszíni hálózati kapcsolat megvédhető a zónaszintű hibáktól. Tekintse [meg a Azure Availability Zones-beli virtuális hálózati átjárókkal](about-zone-redundant-vnet-gateways.md)foglalkozó témakört.

## <a name="pricing"></a><a name="pricing"></a>Díjszabás

[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

További információ a VPN Gateway átjáró-termékváltozatokról: [Gateway SKUs](vpn-gateway-about-vpn-gateway-settings.md#gwsku) (Átjáró-termékváltozatok).

## <a name="faq"></a><a name="faq"></a>GYIK

A VPN Gateway-re vonatkozó gyakori kérdésekért lásd a [VPN Gateway gyakori kérdéseit](vpn-gateway-vpn-faq.md).

## <a name="whats-new"></a><a name="new"></a>Mi újság?

Fizessen elő az RSS-hírcsatornára, és tekintse meg a legújabb VPN Gateway szolgáltatás frissítéseit az [Azure Updates](https://azure.microsoft.com/updates/?category=networking&query=VPN%20Gateway) oldalon.

## <a name="next-steps"></a>Következő lépések

- További információkért tekintse meg [A VPN Gatewayjel kapcsolatos gyakori kérdések](vpn-gateway-vpn-faq.md) című szakaszt.
- Tekintse meg az [Előfizetés- és szolgáltatáskorlátok](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) című szakaszt.
- Ebben a dokumentumban az Azure egyéb lényeges [hálózat képességeivel](../networking/networking-overview.md) ismerkedhet meg.
