---
title: Tudnivalók az Azure VPN Gateway designról
description: Ismerje meg a VPN Gateway-topológia kialakításának módszereit az Azure Virtual Networks szolgáltatáshoz való csatlakozáshoz.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 99b8cdbb1528caefd30fb11c9702dff1e817c0ab
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061808"
---
# <a name="vpn-gateway-design"></a>VPN Gateway kialakítás

Fontos szem előtt tartani, hogy a VPN-átjárós kapcsolatokhoz különböző konfigurációk érhetők el. Önnek kell eldöntenie, melyik konfiguráció felel meg a legjobban az igényeinek. Az alábbi szakaszban megtekintheti az alábbi VPN Gateway-kapcsolatokra vonatkozó tervezési információkat és topológiai diagramokat. Az ábrák és a leírások segítségével kiválaszthatja az igényeinek megfelelő kapcsolati topológiát. A diagramok megjelenítik a fő alapkonfigurációkat, de a diagramok útmutatásként használhatók összetettebb konfigurációk létrehozásához.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a><a name="s2smulti"></a>Helyek közötti és többhelyes (IPsec/IKE VPN-alagút)

### <a name="site-to-site"></a><a name="S2S"></a>Helyek közötti

A helyek közötti (Site-to-Site, S2S) VPN Gateway-kapcsolat egy IPsec/IKE (IKEv1 vagy IKEv2) VPN-alagúton keresztüli kapcsolat. A helyek közötti kapcsolatok létesítmények közötti és hibrid konfigurációk esetében is alkalmazhatók. A S2S-kapcsolathoz olyan helyi VPN-eszközre van szükség, amely a hozzá tartozó nyilvános IP-címmel rendelkezik. A VPN-eszköz kiválasztásával kapcsolatos információkért lásd [a VPN Gateway gyakori kérdései közül a VPN-eszközökkel foglalkozókat](vpn-gateway-vpn-faq.md#s2s).

![Azure VPN Gateway helyek közti kapcsolat – példa](./media/design/vpngateway-site-to-site-connection-diagram.png)

A VPN Gateway konfigurálható aktív-készenléti módban egy nyilvános IP-cím vagy aktív-aktív mód használatával két nyilvános IP-cím használatával. Aktív-készenléti módban az egyik IPsec-alagút aktív, a másik alagút pedig készenléti állapotban van. Ebben a beállításban a forgalom az aktív alagúton keresztül áramlik, és ha valamilyen probléma merül fel ezzel az alagúttal, a forgalom átvált a készenléti alagútra. Az aktív-aktív módban való VPN Gateway beállítása *ajánlott* , ha az IPSec-alagutak egyszerre aktívak, és mindkét alagúton keresztül áramlanak. Az aktív-aktív mód további előnye, hogy az ügyfelek nagyobb átviteli sebességet tapasztalnak.

### <a name="multi-site"></a><a name="Multi"></a>Több hely

Ez a típusú kapcsolat a helyek közötti kapcsolat egy változata. A virtuális hálózati átjáróról több VPN-kapcsolatot hoz létre, amelyek általában több helyszíni helyhez csatlakoznak. Ha több kapcsolatot használ, RouteBased (útvonalalapú) VPN-típust kell alkalmaznia (klasszikus virtuális hálózatok használatakor ezt dinamikus átjárónak nevezik). Mivel minden virtuális hálózat csak egy VPN-átjáróval rendelkezhet, az átjárón keresztüli összes kapcsolat osztozik a rendelkezésre álló sávszélességen. Ezt a kapcsolattípust gyakran „többhelyes” kapcsolatnak nevezik.

![Azure VPN Gateway többhelyes kapcsolat – példa](./media/design/vpngateway-multisite-connection-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>Üzembe helyezési modellek és módszerek a helyek közötti és többhelyes kapcsolatokhoz

[!INCLUDE [site-to-site and multi-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

## <a name="point-to-site-vpn"></a><a name="P2S"></a>Pont – hely típusú VPN

A pont–hely (P2S) VPN-átjátókapcsolat lehetővé teszi biztonságos kapcsolat létesítését a virtuális hálózattal egy különálló ügyfélszámítógépről. A pont–hely kapcsolat létesítéséhez a kapcsolatot az ügyfélszámítógépről kell elindítani. Ez a megoldás főleg távmunkások számára hasznos, akik egy távoli helyről szeretnének csatlakozni egy Azure virtuális hálózatokhoz, például otthonról vagy konferenciáról. A pont–hely VPN emellett akkor is hasznos megoldás lehet a helyek közötti VPN helyett, ha csak néhány ügyfelet szeretne egy virtuális hálózathoz csatlakoztatni.

A helyek közötti kapcsolatoktól eltérően a pont–hely kapcsolatok nem igényelnek helyszíni nyilvános IP-címet vagy VPN-eszközt a működéshez. A pont–hely (P2S) kapcsolatok helyek közötti (S2S) kapcsolatokkal együtt is használhatók ugyanazon a VPN-átjárón keresztül, ha a kapcsolatok minden konfigurációs követelménye kompatibilis egymással. További információk a pont–hely kapcsolatokról: [Információk a pont–hely VPN-ről](point-to-site-about.md).

![Azure VPN Gateway pont–hely kapcsolat – példa](./media/design/point-to-site.png)

### <a name="deployment-models-and-methods-for-p2s"></a>Üzemi modellek és módszerek a pont–hely (P2S) kapcsolatokhoz

[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="vnet-to-vnet-connections-ipsecike-vpn-tunnel"></a><a name="V2V"></a>Virtuális hálózatok közötti kapcsolatok (IPsec/IKE VPN-alagút)

Egy virtuális hálózat egy másikkal való összekapcsolása (a virtuális hálózatok közötti kapcsolat) nagyon hasonlít egy virtuális hálózat egy helyszíni helyhez való csatlakoztatásához. Mindkét kapcsolattípus egy VPN-átjárót használ a biztonságos alagút IPsec/IKE használatával való kialakításához. A virtuális hálózatok közötti kommunikációt kombinálhatja többhelyes kapcsolati konfigurációkkal is. Így létrehozhat olyan hálózati topológiákat, amelyek a létesítmények közötti kapcsolatokat a virtuális hálózatok közötti kapcsolatokkal kombinálják.

Az összekapcsolt virtuális hálózatok lehetnek:

* azonos vagy eltérő régiókban
* azonos vagy eltérő előfizetésekben 
* azonos vagy eltérő üzemi modellekben

![Azure VPN Gateway virtuális hálózatok közötti kapcsolat – példa](./media/design/vpngateway-vnet-to-vnet-connection-diagram.png)

### <a name="connections-between-deployment-models"></a>Üzemi modellek közötti kapcsolat

Az Azure jelenleg kétféle üzemi modellt kínál: a klasszikust és a Resource Managert. Ha már egy ideje használja az Azure-t, valószínűleg futtat Azure virtuális gépeket és példányszerepköröket egy klasszikus virtuális hálózaton. Lehetséges, hogy az újabb virtuális gépek és szerepkörpéldányok egy Resource Managerben létrehozott virtuális hálózatban futnak. Létrehozhat egy kapcsolatot a virtuális hálózatok között, így lehetővé teheti, hogy az egyik virtuális hálózatban lévő erőforrások közvetlenül kommunikáljanak a másikban lévő erőforrásokkal.

### <a name="vnet-peering"></a>Virtuális hálózatok közötti társviszony

A kapcsolat létrehozására használhat virtuális hálózatok közötti társviszonyt, ha a virtuális hálózat megfelel bizonyos követelményeknek. A virtuális hálózatok közötti társviszony nem használ virtuális hálózati átjárót. További információ: [Társviszony létesítése virtuális hálózatok között](../virtual-network/virtual-network-peering-overview.md).

### <a name="deployment-models-and-methods-for-vnet-to-vnet"></a>Üzembe helyezési modellek és módszerek virtuális hálózatok közötti kapcsolatokhoz

[!INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="expressroute-private-connection"></a><a name="ExpressRoute"></a>ExpressRoute (privát kapcsolat)

Az ExpressRoute használatával kiterjesztheti helyszíni hálózatait a Microsoft Cloudba egy privát kapcsolaton keresztül, amelyet egy kapcsolatszolgáltató biztosít. A ExpressRoute használatával kapcsolatokat létesíthet a Microsoft Cloud Services, például a Microsoft Azure, a Microsoft 365 és a CRM Online számára. Ezek lehetnek hálózatközi (IP VPN), pontok közötti Ethernet-hálózati, vagy kapcsolatszolgáltatón keresztüli virtuális keresztkapcsolatok egy közös elhelyezési létesítményen belül.

Az ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át. Így az ExpressRoute-kapcsolatok a tipikus internetes kapcsolatoknál megbízhatóbbak, gyorsabbak, gyorsabb a válaszidejük, és biztonságosabbak.

Az ExpressRoute-kapcsolatok virtuális hálózati átjárót használnak a szükséges konfiguráció részeként. Az ExpressRoute-kapcsolatokban a virtuális hálózati átjáró a „VPN” helyett az „ExpressRoute” átjárótípussal van konfigurálva. Bár az ExpressRoute-kapcsolatcsoporton keresztül haladó forgalom alapértelmezés szerint nincs titkosítva, lehetséges olyan megoldást létrehozni, amely lehetővé teszi a titkosított forgalom küldését ExpressRoute-kapcsolatcsoporton. További információ az ExpressRoute-tal kapcsolatban: [Az ExpressRoute technikai áttekintése](../expressroute/expressroute-introduction.md).

## <a name="site-to-site-and-expressroute-coexisting-connections"></a><a name="coexisting"></a>Egyidejű helyek közötti és ExpressRoute-kapcsolatok

Az ExpressRoute egy, a Microsoft-szolgáltatásokkal (köztük az Azure-ral) a WAN-on, és nem a nyilvános interneten keresztül kialakított közvetlen, privát kapcsolat. A helyek közötti VPN-adatforgalom titkosítva, a nyilvános interneten halad át. A helyek közötti VPN- és az ExpressRoute-kapcsolatok azonos virtuális hálózathoz való konfigurálásának lehetősége több előnnyel jár.

A helyek közötti VPN-t konfigurálhatja biztonságos feladatátvételi útvonalként az ExpressRoute számára, vagy használhat helyek közötti VPN-eket is a hálózat részét nem képező, az ExpressRoute-on keresztül kapcsolódó helyekhez való csatlakozáshoz. Ebben a konfigurációban két virtuális hálózati átjáróra van szükség ugyanahhoz a virtuális hálózathoz, amelyek közül az egyik a „Vpn” átjárótípust, a másik pedig az „ExpressRoute” átjárótípust használja.

![ExpressRoute és VPN egyidejű kapcsolatok – példa](./media/design/expressroute-vpngateway-coexisting-connections-diagram.png)

### <a name="deployment-models-and-methods-for-s2s-and-expressroute-coexist"></a>Üzemi modellek és módszerek egyidejű helyek közötti (S2S) és ExpressRoute-kapcsolatokhoz

[!INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]

## <a name="highly-available-connections"></a><a name="highly-available"></a>Magasan elérhető kapcsolatok

A magasan elérhető kapcsolatok tervezéséhez és kialakításához tekintse meg a [magasan elérhető kapcsolatok](vpn-gateway-highlyavailable.md)című témakört.

## <a name="next-steps"></a>Következő lépések

* További információkért tekintse meg [A VPN Gatewayjel kapcsolatos gyakori kérdések](vpn-gateway-vpn-faq.md) című szakaszt.

* További információ a [VPN Gateway konfigurációs beállításairól](vpn-gateway-about-vpn-gateway-settings.md).

* Az VPN Gateway BGP-megfontolásokat lásd: [About BGP](vpn-gateway-bgp-overview.md).

* Tekintse meg az [Előfizetés- és szolgáltatáskorlátok](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) című szakaszt.

* Ebben a dokumentumban az Azure egyéb lényeges [hálózat képességeivel](../networking/networking-overview.md) ismerkedhet meg.
