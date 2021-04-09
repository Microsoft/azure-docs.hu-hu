---
title: 'Együttműködés az Azure-ban: teszt beállítása | Microsoft Docs'
description: Ez a cikk azt ismerteti, hogyan elemezhető a ExpressRoute, a helyek közötti VPN és az Azure-beli virtuális hálózatok közötti együttműködés.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 206cc70b5ee636ec3cc54727c3e94a10ad2426d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98234137"
---
# <a name="interoperability-in-azure--test-setup"></a>Együttműködés az Azure-ban: tesztelési beállítások

Ez a cikk egy tesztelési beállítást ismertet, amellyel elemezheti, hogy az Azure hálózati szolgáltatásai hogyan működnek együtt a vezérlési síkon és az adatsík szintjén. Nézzük meg röviden az Azure hálózatkezelési összetevőit:

-   **Azure-ExpressRoute**: az Azure ExpressRoute-beli privát kapcsolattal közvetlenül csatlakoztathatók a helyszíni hálózat magánhálózati IP-területei az Azure Virtual Network-környezetekhez. A ExpressRoute segítségével nagyobb sávszélességet és privát kapcsolatokat érhet el. Számos ExpressRoute Eco-partner ExpressRoute-kapcsolatot biztosít SLA-val. A ExpressRoute és a ExpressRoute konfigurálásával kapcsolatos további tudnivalókért tekintse meg a [ExpressRoute bemutatása][ExpressRoute]című témakört.
-   **Helyek közötti VPN**: az Azure VPN Gateway-t helyek közötti VPN-ként használva biztonságosan összekapcsolhatja a helyszíni hálózatot az Azure-ban az interneten vagy a ExpressRoute használatával. A helyek közötti VPN konfigurálásáról az Azure-hoz való csatlakozásról további információt a [VPN Gateway konfigurálása][VPN]című témakörben talál.
-   **VNet**-társítás: használja a Virtual Network (VNet) társait az Virtual Network Azure-beli virtuális hálózatok közötti kapcsolat létesítéséhez. Ha többet szeretne megtudni a VNet-kezelésről, tekintse meg a VNet-társítással [foglalkozó oktatóanyagot][VNet].

## <a name="test-setup"></a>Teszt beállítása

Az alábbi ábra a teszt beállítását szemlélteti:

![1][1]

A teszt központi beállítása az Azure 1. régiójában lévő VNet központ. A hub VNet a következő módokon csatlakozik a különböző hálózatokhoz:

-   A hub VNet a küllős VNet csatlakozik a VNet-társítás használatával. A küllős VNet távelérést biztosít mindkét átjáróhoz a hub VNet.
-   A hub-VNet helyek közötti VPN-kapcsolattal csatlakozik a fiókiroda VNet. A kapcsolat a eBGP használatával cseréli át az útvonalakat.
-   A hub-VNet az elsődleges elérési úttal együtt a ExpressRoute magánhálózati kapcsolat használatával csatlakozik a helyszíni helyhez 1 hálózathoz. A helyek közötti VPN-kapcsolatot használja a biztonsági mentési útvonalként. A cikk további részében ezt a ExpressRoute-áramkört nevezzük ExpressRoute 1 néven. Alapértelmezés szerint a ExpressRoute-áramkörök redundáns kapcsolatot biztosítanak a magas rendelkezésre állás érdekében. Az 1. ExpressRoute a másodlagos ügyfél peremhálózati (CE) útválasztójának alfelülete le van tiltva a másodlagos Microsoft Enterprise Edge-útválasztó (MSEE) számára. Az előző ábrán látható dupla vonal nyílra mutató piros vonal a letiltott CE útválasztó alkapcsolatot jelöli.
-   A hub-VNet egy másik ExpressRoute-kapcsolat használatával csatlakozik a helyszíni hely 2 hálózathoz. A cikk további részében ezt a második ExpressRoute-áramkört tekintjük meg ExpressRoute 2 néven.
-   A ExpressRoute 1 a hub VNet és a helyszíni hely 1 hálózatot is csatlakoztatja egy távoli VNet az Azure-régióban 2.

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>ExpressRoute és helyek közötti VPN-kapcsolat párhuzamosan

###  <a name="site-to-site-vpn-over-expressroute"></a>Helyek közötti VPN a ExpressRoute-en keresztül

A helyek közötti VPN-t konfigurálhatja úgy, hogy a Microsoft ExpressRoute a saját helyszíni hálózata és az Azure-virtuális hálózatok közötti személyes információcserére használja. Ezzel a konfigurációval az adatok titkosságot, hitelességet és integritást cserélhetnek. Az adatcsere is a visszajátszás elleni védelem. Ha további információt szeretne arról, hogyan konfigurálhat helyek közötti IPsec VPN-t bújtatási módban a Microsoft ExpressRoute használatával, tekintse meg a [helyek közötti VPN-t a ExpressRoute Microsoft-partneri kapcsolaton keresztül][S2S-Over-ExR]. 

A helyek közötti VPN Microsoft-társítást használó konfigurálásának elsődleges korlátozása az átviteli sebesség. Az IPsec-alagút átviteli sebességét a VPN Gateway kapacitása korlátozza. A VPN-átjáró átviteli sebessége kisebb, mint a ExpressRoute átviteli sebessége. Ebben az esetben az IPsec-alagúton keresztül, a biztonságos forgalomhoz és a privát és a többi forgalomhoz való használat révén optimalizálhatja a ExpressRoute sávszélesség-kihasználtságot.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>Helyek közötti VPN biztonságos feladatátvételi útvonalként a ExpressRoute

A ExpressRoute redundáns áramköri párokként szolgál a magas rendelkezésre állás biztosításához. Különböző Azure-régiókban is konfigurálhatja a Geo-redundáns ExpressRoute-kapcsolatot. Továbbá, ahogyan azt a tesztelési beállítás is mutatja, egy Azure-régión belül egy helyek közötti VPN használatával létrehozhat egy feladatátvételi útvonalat a ExpressRoute-kapcsolathoz. Ha ugyanazokat az előtagokat hirdeti meg mindkét ExpressRoute és egy helyek közötti VPN-en, az Azure rangsorolja a ExpressRoute-t. Ha el szeretné kerülni a ExpressRoute és a két hálózat közötti pont-pont típusú VPN közötti aszimmetrikus útválasztást, a helyszíni hálózati konfigurációnak a ExpressRoute-kapcsolat használatával is be kell állnia, mielőtt a helyek közötti VPN-kapcsolatot használja.

A ExpressRoute és helyek közötti VPN-hez való egyidejű kapcsolatok konfigurálásával kapcsolatos további információkért lásd: [ExpressRoute és helyek közötti együttélés][ExR-S2S-CoEx].

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Háttérbeli kapcsolat kiterjesztése küllős virtuális hálózatok és fiókirodák számára

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Küllős VNet-kapcsolat a VNet-társítás használatával

A hub és a küllős VNet architektúra széles körben használatos. A hub egy VNet az Azure-ban, amely központi kapcsolódási pontként szolgál a küllős virtuális hálózatok és a helyszíni hálózat között. A küllők a virtuális hálózatok, és a munkaterhelések elkülönítésére használhatók. A forgalom a helyszíni adatközpont és a hub között egy ExpressRoute vagy VPN-kapcsolaton keresztül áramlik. További információ az architektúráról: [sugaras hálózati topológia implementálása az Azure-ban][Hub-n-Spoke].

Egy adott régión belüli VNet-társítás esetén a küllő virtuális hálózatok az VNet-átjárókat (VPN-és ExpressRoute-átjárók) is használhatják a távoli hálózatokkal való kommunikációhoz.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Ág VNet-kapcsolat a helyek közötti VPN használatával

Előfordulhat, hogy a fiókirodák virtuális hálózatok, amelyek különböző régiókban vannak, és a helyszíni hálózatok egy hub-VNet keresztül kommunikálnak egymással. A konfiguráció natív Azure-megoldása a helyek közötti VPN-kapcsolat VPN használatával. Egy másik lehetőség, hogy hálózati virtuális berendezést (NVA) használ az útválasztáshoz a központban.

További információ: [Mi a VPN Gateway?][VPN] és [telepítsen egy magasan elérhető NVA][Deploy-NVA].

## <a name="next-steps"></a>Következő lépések

A tesztelési topológia [konfigurációs adatainak][Configuration] megismerése.

Ismerje meg a [vezérlési sík elemzését][Control-Analysis] a tesztelési beállításról, valamint a topológia különböző virtuális hálózatok vagy VLAN-ok nézeteiről.

Ismerje meg a tesztelési beállítások és az Azure Network monitoring szolgáltatás nézeteinek [adatsíkjainak elemzését][Data-Analysis] .

Tekintse meg a [ExpressRoute kapcsolatos gyakori kérdéseket][ExR-FAQ] :
-   Megtudhatja, hogy hány ExpressRoute-áramkört tud csatlakozni egy ExpressRoute-átjáróhoz.
-   Megtudhatja, hány ExpressRoute-átjárót tud csatlakozni egy ExpressRoute-áramkörhöz.
-   A ExpressRoute egyéb méretezési korlátainak megismerése.


<!--Image References-->
[1]: ./media/backend-interoperability/TestSetup.png "A tesztelési topológia ábrája"

<!--Link References-->
[ExpressRoute]: ../expressroute/expressroute-introduction.md
[VPN]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[VNet]: ../virtual-network/tutorial-connect-virtual-networks-portal.md
[Configuration]: connectivty-interoperability-configuration.md
[Control-Analysis]: connectivty-interoperability-control-plane.md
[Data-Analysis]: connectivty-interoperability-data-plane.md
[ExR-FAQ]: ../expressroute/expressroute-faqs.md
[S2S-Over-ExR]: ../expressroute/site-to-site-vpn-over-microsoft-peering.md
[ExR-S2S-CoEx]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[Hub-n-Spoke]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: /azure/architecture/reference-architectures/dmz/nva-ha