---
title: Az Azure hálózati szolgáltatásai – áttekintés
description: Ismerje meg az Azure hálózatkezelési szolgáltatásait, beleértve a kapcsolódást, az alkalmazások védelmét, az alkalmazások kézbesítését és a hálózati figyelési szolgáltatásokat.
services: networking
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/28/2020
ms.author: kumud
ms.openlocfilehash: d02f85647d947c2f28527ba6f0397c2a98e73840
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98234188"
---
# <a name="azure-networking-services-overview"></a>Az Azure hálózati szolgáltatásai – áttekintés

Az Azure hálózati szolgáltatásai különféle hálózati funkciókat biztosítanak, amelyek együtt vagy külön is használhatók. A következő főbb képességek bármelyikére kattintva további információkat tudhat meg róluk:
- [**Csatlakozási szolgáltatások**](#connect): az Azure-erőforrások és a helyszíni erőforrások összekapcsolása az azure-Virtual Network (VNet), a Virtual WAN, a ExpressRoute, a VPN Gateway, a Virtual Network NAT Gateway, a Azure DNS, a peering Service és az Azure Bastion használatával.
- [**Alkalmazás-védelmi szolgáltatások**](#protect): az alkalmazásokat az Azure-Private link, a DDoS Protection, a tűzfal, a hálózati biztonsági csoportok, a webalkalmazási tűzfal és a Virtual Network végpontok bármely vagy több kombinációjának használatával biztosíthatja az alkalmazások védelmére.
- [**Application Delivery Services**](#deliver): az Azure-hálózatban lévő alkalmazásokat az azure-Content DELIVERY Network (CDN), az Azure bejárati szolgáltatás, a Traffic Manager, a Application Gateway, az Internet Analyzer és a Load Balancer használatával biztosíthatja.
- [**Hálózatfigyelő**](#monitor): az Azure-Network Watcher, a ExpressRoute monitor, a Azure monitor vagy a VNet terminál-hozzáférési pont (TAP) használatával figyelheti a hálózati erőforrásokat.

## <a name="connectivity-services"></a><a name="connect"></a>Kapcsolati szolgáltatások
 
Ez a szakasz azokat a szolgáltatásokat ismerteti, amelyek kapcsolatot biztosítanak az Azure-erőforrások, a helyszíni hálózat és az Azure-erőforrások közötti kapcsolat, valamint az Azure-Virtual Network (VNet), a ExpressRoute, a VPN Gateway, a Virtual WAN, a Virtual Network NAT Gateway, a Azure DNS, az Azure peering Service és az Azure Bastion szolgáltatásokhoz való csatlakozással.


### <a name="virtual-network"></a><a name="vnet"></a>Virtuális hálózat

Az Azure Virtual Network (VNet) az Azure-beli magánhálózat alapvető építőeleme. A virtuális hálózatok a következőket végezheti el:
- **Kommunikáció az Azure-erőforrások között**: telepíthet virtuális gépeket és számos más típusú Azure-erőforrást egy virtuális hálózatra, például Azure app Service környezetekre, az Azure Kubernetes szolgáltatásra (ak) és az Azure Virtual Machine Scale Setsre. A virtuális hálózatokon üzembe helyezhető Azure-erőforrások teljes listájáért lásd: [Virtuális hálózati szolgáltatás integrálása](../virtual-network/virtual-network-for-azure-services.md).
- **Kommunikáció egymás között**: a virtuális hálózatok összekapcsolhatók egymással, így a virtuális hálózatban lévő erőforrások egymással is kommunikálhatnak, a virtuális hálózatok használatával. Az összekacsolt virtuális hálózatok lehetnek azonos vagy eltérő Azure-régiókban. További információ: [Virtual Network peering](../virtual-network/virtual-network-peering-overview.md).
- **Kommunikáció az internettel**: a VNet összes erőforrása alapértelmezés szerint képes kommunikálni a kimenő internettel. Bejövő kommunikációt létesíthet egy erőforrással egy nyilvános IP-cím vagy Load Balancer hozzárendelésével. A kimenő kapcsolatok kezeléséhez [nyilvános IP-címeket](../virtual-network/virtual-network-public-ip-address.md) vagy nyilvános [Load Balancer](../load-balancer/load-balancer-overview.md) is használhat.
- **Kommunikáció helyszíni hálózatokkal**: a helyszíni számítógépeit és hálózatait [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) vagy [ExpressRoute](../expressroute/expressroute-introduction.md)használatával összekapcsolhatja egy virtuális hálózattal.

További információ: [Mi az az Azure Virtual Network?](../virtual-network/virtual-networks-overview.md)

### <a name="expressroute"></a><a name="expressroute"></a>ExpressRoute
A ExpressRoute lehetővé teszi, hogy a helyszíni hálózatait a Microsoft-felhőben a kapcsolati szolgáltató által biztosított privát kapcsolaton keresztül bővítse. Ez a kapcsolat nem nyilvános. A forgalom nem az interneten keresztül halad át. Az ExpressRoute-tal kapcsolatokat létesíthet a Microsoft-felhőszolgáltatásokkal, például a Microsoft Azure-ral, a Microsoft 365-tel és a Dynamics 365-tel.  További információ: [What is ExpressRoute?](../expressroute/expressroute-introduction.md).

:::image type="content" source="./media/networking-overview/expressroute-connection-overview.png" alt-text="Azure ExpressRoute" border="false":::

### <a name="vpn-gateway"></a><a name="vpngateway"></a>VPN Gateway
A VPN Gateway segítségével titkosított létesítmények közötti kapcsolatokat hozhat létre a virtuális hálózattal a helyszíni helyekről, illetve titkosított kapcsolatokat hozhat létre a virtuális hálózatok között. A VPN Gateway kapcsolatok esetében különböző konfigurációk érhetők el, például helyek közötti, pont – hely vagy VNet – VNet.
A következő ábra több helyek közötti VPN-kapcsolatot mutat be ugyanahhoz a virtuális hálózathoz.

:::image type="content" source="./media/networking-overview/vpngateway-multisite-connection-diagram.png" alt-text="Helyek közötti Azure VPN Gateway kapcsolatok":::

További információ a VPN-kapcsolat különböző típusairól: [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="virtual-wan"></a><a name="virtualwan"></a>Virtuális WAN
Az Azure Virtual WAN egy hálózati szolgáltatás, amely optimalizált és automatizált ág-kapcsolatot biztosít az Azure-hoz és a-n keresztül. Az Azure-régiók olyan hubok, amelyekhez az ágakat összekapcsolhatjuk. Az Azure-gerinc kihasználható az ágak összekapcsolásához és a VNet közötti kapcsolathoz is. Az Azure Virtual WAN számos Azure Cloud connectivity-szolgáltatást kínál, többek között a helyek közötti VPN-t, a ExpressRoute, a pont – hely típusú felhasználói VPN-t egyetlen operatív felületre. Az Azure virtuális hálózatok-hez való kapcsolódás virtuális hálózati kapcsolatok használatával történik. További információ: [Mi az az Azure Virtual WAN?](../virtual-wan/virtual-wan-about.md).

:::image type="content" source="./media/networking-overview/virtualwan1.png" alt-text="Virtuális WAN ábrája":::

### <a name="azure-dns"></a><a name="dns"></a>Azure DNS
Az Azure DNS egy üzemeltetési szolgáltatás DNS-tartományokhoz, amely a Microsoft Azure infrastruktúrájával történő névfeloldást nyújt. Ha tartományait az Azure-ban üzemelteti, DNS-rekordjait a többi Azure-szolgáltatáshoz is használt hitelesítő adatokkal, API-kkal, eszközökkel és számlázási információkkal kezelheti. További információ: [Mi az Azure DNS?](../dns/dns-overview.md)

### <a name="azure-bastion"></a><a name="bastion"></a>Azure Bastion
Az Azure Bastion szolgáltatás egy új, teljes körűen felügyelt, a virtuális hálózaton belül kiépített, teljesen platform által felügyelt Pásti szolgáltatás. Biztonságos és zökkenőmentes RDP/SSH-kapcsolatot biztosít a virtuális gépekhez közvetlenül a TLS-en keresztül Azure Portal. Ha az Azure Bastionon keresztül csatlakozik, a virtuális gépeinek nincs szüksége nyilvános IP-címre. További információ: [Mi az az Azure Bastion?](../bastion/bastion-overview.md).

:::image type="content" source="./media/networking-overview/architecture.png" alt-text="Azure Bastion-architektúra":::

### <a name="virtual-network-nat-gateway"></a><a name="nat"></a>Virtuális hálózati NAT-átjáró
Virtual Network NAT (hálózati címfordítás) egyszerűbbé teszi a csak kimenő internetkapcsolatot a virtuális hálózatokhoz. Ha egy alhálózaton van konfigurálva, minden kimenő kapcsolat a megadott statikus nyilvános IP-címeket használja. A kimenő kapcsolat a virtuális gépekhez közvetlenül csatlakoztatott terheléselosztó vagy nyilvános IP-címek nélkül is lehetséges. További információ: [Mi a Virtual Network NAT Gateway?](../virtual-network/nat-overview.md).

:::image type="content" source="./media/networking-overview/flow-map.png" alt-text="Virtuális hálózati NAT-átjáró":::

### <a name="azure-peering-service"></a><a name="azurepeeringservice"></a> Azure-partneri szolgáltatás
Az Azure-alapú szolgáltatások a Microsoft Cloud Services, például a Microsoft 365, a Dynamics 365, a szolgáltatott szoftver (SaaS) szolgáltatások, az Azure vagy bármely, a nyilvános interneten keresztül elérhető Microsoft-szolgáltatás számára lehetővé teszik az ügyfelek kapcsolódását. További információ: [Mi az Azure-partneri szolgáltatás?](../peering-service/about.md).

### <a name="azure-edge-zones"></a><a name="edge-zones"></a>Azure Edge-zónák

Az Azure Edge Zone olyan Microsoft Azure kínál, amely az adatfeldolgozást a felhasználóhoz közelebb teszi. A virtuális gépeket, tárolókat és más kiválasztott Azure-szolgáltatásokat üzembe helyezheti peremhálózati zónákba az alkalmazások alacsony késésű és nagy teljesítményű követelményeinek kielégítése érdekében. További információ: [Mi az az Azure Edge Zones?](edge-zones-overview.md).

### <a name="azure-orbital"></a><a name="orbital"></a>Azure orbitális

Az Azure orbitális egy teljes körűen felügyelt, felhőalapú szolgáltatás, amely lehetővé teszi, hogy kommunikáljon az űrhajóval vagy a műholdas konstellációkkal, a leküldés és a kimenő adatokkal, feldolgozza az adatait a felhőben, az Azure-szolgáltatásokkal és az ügyfelekkel kapcsolatos szolgáltatások létrehozásával. Ez a rendszer az Azure globális infrastruktúrára és az alacsony késleltetésű globális Fiber-hálózatra épül. További információ: [Mi az az Azure orbitális?](azure-orbital-overview.md).

:::image type="content" source="./media/azure-orbital-overview/orbital-communications-use-flow.png" alt-text="Azure orbitális kommunikációs diagram":::

## <a name="application-protection-services"></a><a name="protect"></a>Alkalmazás-védelmi szolgáltatások

Ez a szakasz az Azure-ban található hálózati szolgáltatásokat ismerteti, amelyek segítenek a hálózati erőforrások védelmében – az alkalmazások védelmét az Azure-DDoS Protection, a privát hivatkozás, a tűzfal, a webalkalmazási tűzfal, a hálózati biztonsági csoportok és a Virtual Network szolgáltatási végpontok használatával.

### <a name="ddos-protection"></a><a name="ddosprotection"></a>DDoS Protection 
A [Azure DDoS Protection](../ddos-protection/manage-ddos-protection.md) a legkifinomultabb DDoS-fenyegetések elleni védelmet nyújt. A szolgáltatás továbbfejlesztett DDoS-elhárítási képességeket biztosít az alkalmazáshoz és a virtuális hálózatokban üzembe helyezett erőforrásokhoz. Emellett a Azure DDoS Protectiont használó ügyfeleink hozzáférhetnek a DDoS gyors reagálás támogatásához, hogy aktív támadás közben is bekapcsolódjanak a DDoS-szakértőkbe.

:::image type="content" source="./media/networking-overview/ddos-protection.png" alt-text="DDoS Protection":::

### <a name="azure-private-link"></a><a name="privatelink"></a>Azure Private Link
Az [Azure Private link](../private-link/private-link-overview.md) lehetővé teszi az Azure Pásti-szolgáltatások (például az Azure Storage és a SQL Database) és az Azure által üzemeltetett felhasználói/partneri szolgáltatások elérését a virtuális hálózat privát végpontján keresztül.
A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinc hálózatán halad át. A szolgáltatás nyilvános internetre való kimutatása már nem szükséges. Létrehozhatja saját privát kapcsolati szolgáltatását a virtuális hálózatban, és továbbíthatja az ügyfeleknek.

:::image type="content" source="./media/networking-overview/private-endpoint.png" alt-text="Privát végpont áttekintése":::

### <a name="azure-firewall"></a><a name="firewall"></a>Azure Firewall
Az Azure Firewall egy felügyelt, felhőalapú hálózatbiztonsági szolgáltatás, amely Azure Virtual Network-erőforrásait védi. A Azure Firewall használatával központilag hozhat létre, kényszerítheti és naplózhatja az alkalmazás-és hálózati kapcsolati szabályzatokat az előfizetések és a virtuális hálózatok között. Az Azure Firewall statikus nyilvános IP-címet használ a virtuális hálózat erőforrásaihoz, így a külső tűzfalak azonosíthatják a virtuális hálózatból érkező forgalmat. 

A Azure Firewallról további információt a [Azure Firewall dokumentációjában](../firewall/overview.md)talál.

:::image type="content" source="./media/networking-overview/firewall-threat.png" alt-text="Tűzfal áttekintése":::

### <a name="web-application-firewall"></a><a name="waf"></a>Webalkalmazási tűzfal
Az [Azure webalkalmazási tűzfal](../web-application-firewall/overview.md) (WAF) védelmet nyújt a webalkalmazásoknak a gyakori webes biztonsági rések és sebezhetőségek, például az SQL-injektálás és a helyek közötti parancsfájlok használatával. Az Azure WAF a felügyelt szabályok segítségével a OWASP 10 legfontosabb biztonsági résen kívülről is biztosít védelmet a box-ban. Emellett az ügyfelek egyéni szabályokat is megadhatnak, amelyek az ügyfél által felügyelt szabályok a forrás IP-címtartomány alapján további védelmet biztosítanak, és olyan attribútumokat igényelnek, mint a fejlécek, a cookie-k, az űrlap adatmezői vagy a lekérdezési karakterlánc

Az ügyfelek dönthetnek úgy, hogy üzembe helyezik az [Azure WAF-t Application Gateway,](../web-application-firewall/ag/ag-overview.md) amely regionális védelmet biztosít a nyilvános és privát címtartomány entitásai számára. Az ügyfelek dönthetnek úgy is, hogy az [Azure WAF](../web-application-firewall/afds/afds-overview.md) üzembe helyezését is lehetővé teszi, amely a hálózati peremhálózat védelmét biztosítja a nyilvános végpontok számára.

:::image type="content" source="./media/networking-overview/waf-overview.png" alt-text="Webalkalmazási tűzfal":::

### <a name="network-security-groups"></a><a name="nsg"></a>Hálózati biztonsági csoportok
Az Azure-beli virtuális hálózatokban az Azure-erőforrások bejövő és kimenő hálózati forgalmát hálózati biztonsági csoportokkal szűrheti. További információ: [hálózati biztonsági csoportok](../virtual-network/network-security-groups-overview.md).

### <a name="service-endpoints"></a><a name="serviceendpoints"></a>Szolgáltatásvégpontok
A virtuális hálózatok (VNet) szolgáltatásvégpontjai egy közvetlen kapcsolaton keresztül kiterjesztik a virtuális hálózat magáncímterét és a VNet identitását az Azure-szolgáltatásokra. A végpontok segítségével biztosíthatja, hogy kritikus fontosságú Azure-szolgáltatási erőforrásai csak a virtuális hálózatain legyenek elérhetőek. A VNet felől az Azure-szolgáltatás felé irányuló forgalom mindig a Microsoft Azure gerinchálózatán halad át. További információ: [Virtual Network szolgáltatás-végpontok](../virtual-network/virtual-network-service-endpoints-overview.md).

:::image type="content" source="./media/networking-overview/vnet-service-endpoints-overview.png" alt-text="Virtuális hálózati szolgáltatásvégpontok":::

## <a name="application-delivery-services"></a><a name="deliver"></a>Application Delivery Services

Ez a szakasz az Azure-ban elérhető hálózati szolgáltatásokat ismerteti, amelyek segítenek az alkalmazások – Content Delivery Network, az Azure bejárati szolgáltatás, a Traffic Manager, a Load Balancer és a Application Gateway szolgáltatás nyújtásában.

### <a name="content-delivery-network"></a><a name="cdn"></a>Content Delivery Network
Az Azure tartalomkézbesítési hálózat CDN a tartalmakat világszerte, stratégiai alapon elhelyezett fizikai csomópontokon gyorsítótárazva globális megoldást kínál a fejlesztők számára a tartalmak nagy sávszélességű gyors kézbesítéséhez a felhasználók felé. További információ a Azure CDNről: [Azure Content Delivery Network](../cdn/cdn-overview.md).

:::image type="content" source="./media/networking-overview/cdn-overview.png" alt-text="Azure CDN":::

### <a name="azure-front-door-service"></a><a name="frontdoor"></a>Azure Front Door Service
Az Azure Front Door Service segítségével meghatározhatja, felügyelheti és monitorozhatja a webes forgalmának globális forgalomirányítását, optimalizálhatja annak teljesítményét, és azonnali globális feladatátvétellel magas rendelkezésre állást biztosíthat. A Front Door használatával a globális (több régióban található) fogyasztói és nagyvállalati alkalmazásait olyan robusztus, nagy teljesítményű, személyre szabott és modern alkalmazásokká, API-kká és tartalmakká alakíthatja, amelyek az Azure-on keresztül elérhetik a globális közönségüket. További információ: [Azure bejárati ajtó](../frontdoor/front-door-overview.md).

:::image type="content" source="./media/networking-overview/front-door-visual-diagram.png" alt-text="A bejárati ajtó szolgáltatás áttekintése":::

### <a name="traffic-manager"></a><a name="trafficmanager"></a>Traffic Manager

Az Azure Traffic Manager egy DNS-alapú forgalom-terheléselosztó, amely lehetővé teszi a szolgáltatásokhoz érkező forgalom optimális elosztását a globális Azure-régiókban, miközben magas rendelkezésre állást és válaszkészséget biztosít. Traffic Manager számos forgalom-útválasztási módszert biztosít a forgalom, például a prioritás, a súlyozott, a teljesítmény, a földrajzi, a többértékes vagy az alhálózat elosztására. További információ a forgalom-útválasztási módszerekről: [Traffic Manager útválasztási módszerek](../traffic-manager/traffic-manager-routing-methods.md).

A következő ábra a végpontok prioritáson alapuló útválasztását mutatja Traffic Manager:

:::image type="content" source="./media/networking-overview/priority.png" alt-text="Azure Traffic Manager &quot;prioritás&quot; forgalom – útválasztási módszer":::

További információ a Traffic Managerról: [Mi az az Azure Traffic Manager?](../traffic-manager/traffic-manager-overview.md)

### <a name="load-balancer"></a><a name="loadbalancer"></a>Load Balancer
Az Azure Load Balancer nagy teljesítményű, kis késleltetésű 4. rétegbeli terheléselosztást biztosít az összes UDP-és TCP-protokollhoz. Felügyeli a bejövő és kimenő kapcsolatokat. A nyilvános és a belső terheléselosztású végpontokat is konfigurálhatja. Meghatározhatja azokat a szabályokat, amelyekkel leképezheti a bejövő kapcsolatokat a háttér-készletek célhelyei számára a TCP és a HTTP Health-Probing beállításainak használatával a szolgáltatás rendelkezésre állásának kezeléséhez. Ha többet szeretne megtudni a Load Balancerről, olvassa el a [Load Balancer áttekintő](../load-balancer/load-balancer-overview.md) cikket.

Az alábbi képen egy internetre irányuló többrétegű alkalmazás jelenik meg, amely külső és belső terheléselosztót is használ:

:::image type="content" source="./media/networking-overview/load-balancer.png" alt-text="Azure Load Balancer példa":::

### <a name="application-gateway"></a><a name="applicationgateway"></a>Application Gateway
Az Azure Application Gateway egy webes forgalomra vonatkozó terheléselosztó, amellyel kezelheti a webalkalmazásai forgalmát. Ez egy Application Delivery Controller (ADC) szolgáltatás, amely különböző 7. rétegbeli terheléselosztási funkciókat kínál alkalmazásai számára. További információ: [Mi az az Azure Application Gateway?](../application-gateway/overview.md)

Az alábbi ábra az URL-alapú útválasztást Application Gatewaysal mutatja.

:::image type="content" source="./media/networking-overview/figure1-720.png" alt-text="Application Gateway példa":::

## <a name="network-monitoring-services"></a><a name="monitor"></a>Hálózati figyelési szolgáltatások
Ez a szakasz az Azure hálózati szolgáltatásait ismerteti, amelyek segítenek a hálózati erőforrások figyelésében – Network Watcher, hálózatok Azure Monitor, ExpressRoute, Azure Monitor és Virtual Network KOPPINTanak.

### <a name="network-watcher"></a><a name="networkwatcher"></a>Network Watcher
Az Azure Network Watcher eszközeivel monitorozhatja és diagnosztizálhatja az erőforrásokat egy Azure virtuális hálózaton belül, illetve megtekintheti azok metrikáit, és engedélyezheti vagy letiltja azok naplóit. További információ: [Mi az Network Watcher?](../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)

### <a name="azure-monitor-for-networks-preview"></a>Hálózatok Azure Monitor előzetes verzió
A hálózatok Azure Monitor az összes telepített hálózati erőforrás állapotának és metrikáinak átfogó áttekintését teszi lehetővé anélkül, hogy konfigurációt kellene igényelni. Emellett hozzáférést biztosít a hálózati figyelési funkciókhoz, például a [kapcsolati figyelőhöz](../network-watcher/connection-monitor-overview.md), [a hálózati biztonsági csoportokhoz tartozó adatfolyam-naplózáshoz](../network-watcher/network-watcher-nsg-flow-logging-overview.md)és a [Traffic Analyticshoz](../network-watcher/traffic-analytics.md). További információ: [Azure monitor for Networks Preview](../azure-monitor/insights/network-insights-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

### <a name="expressroute-monitor"></a><a name="expressroutemonitor"></a>ExpressRoute-figyelő
A ExpressRoute-áramköri metrikák, az erőforrás-naplók és a riasztások megtekintésével kapcsolatban lásd: [ExpressRoute-figyelés,-metrikák és-riasztások](../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="azure-monitor"></a><a name="azuremonitor"></a>Azure Monitor
Az Azure Monitor az alkalmazások rendelkezésre állását és teljesítményét növeli a lehető legnagyobbra egy olyan átfogó megoldás biztosításával, amely a felhőből és a helyszíni környezetből származó telemetriaadatokat gyűjt, elemez, valamint műveleteket hajt végre az adatok alapján. Ez a szolgáltatás segít megérteni azt, hogy az alkalmazásai hogyan teljesítenek, valamint proaktív módon azonosítja a működésüket befolyásoló problémákat és azokat az erőforrásokat, amelyektől függenek. További információ: [Azure monitor Overview (áttekintés](../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json)).
### <a name="virtual-network-tap"></a><a name="vnettap"></a>Virtual Network KOPPINTson
Az Azure Virtual Network (terminál-hozzáférési pont) funkció lehetővé teszi a virtuális gép hálózati forgalmának folyamatos továbbítását egy hálózati csomag gyűjtője vagy analitikai eszköze számára. A gyűjtő vagy az elemzési eszközt egy [hálózati virtuális berendezési](https://azure.microsoft.com/solutions/network-appliances/) partner kapja meg.

Az alábbi képen látható, hogyan működik a virtuális hálózat:

:::image type="content" source="./media/networking-overview/virtual-network-tap-architecture.png" alt-text="A virtuális hálózat KOPPINTÁSának működése":::

További információ: [Mi az Virtual Network koppint](../virtual-network/virtual-network-tap-overview.md).

## <a name="next-steps"></a>Következő lépések

- Hozza létre az első virtuális hálózatot, és kapcsolódjon hozzá néhány virtuális géphez. ehhez végezze el az [első virtuális hálózat létrehozása](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) című cikk lépéseit.
- A számítógép csatlakoztatása egy virtuális hálózathoz a [pont – hely kapcsolat konfigurálása című cikkben](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)ismertetett lépések végrehajtásával.
- Az internetre irányuló [terheléselosztó létrehozása](../load-balancer/quickstart-load-balancer-standard-public-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) című cikkben ismertetett lépések végrehajtásával terheléselosztást végezhet a nyilvános kiszolgálók internetes forgalmával.