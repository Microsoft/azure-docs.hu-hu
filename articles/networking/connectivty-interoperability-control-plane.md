---
title: 'Együttműködés az Azure-ban: vezérlési sík elemzése'
description: Ez a cikk a ExpressRoute, a helyek közötti VPN-kapcsolat és az Azure-beli virtuális hálózatok közötti együttműködés elemzéséhez használható tesztelési sík analízisét ismerteti.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 4dfd869b92e042e71eed1ee692d90fc44a8ac6c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98234239"
---
# <a name="interoperability-in-azure--control-plane-analysis"></a>Együttműködés az Azure-ban: vezérlési sík elemzése

Ez a cikk a [teszt telepítésének][Setup]vezérlési síkon végzett elemzését ismerteti. Emellett áttekintheti a [tesztelési beállítások konfigurációját][Configuration] és a tesztelési beállítások [adatsíkok elemzését][Data-Analysis] is.

A vezérlési sík elemzése lényegében megvizsgálja azokat az útvonalakat, amelyek a hálózatokon belül, a topológián belül vannak cserélve. A vezérlési sík elemzése segítségével megismerheti, hogy a különböző hálózatok hogyan tekintik meg a topológiát.

## <a name="hub-and-spoke-vnet-perspective"></a>A hub és a küllő VNet perspektívája

Az alábbi ábra szemlélteti a hálózatot a hub Virtual Network (VNet) szempontjából, és egy küllős VNet (kék színnel). Az ábra a különböző hálózatok és útvonalak különböző hálózatai közötti autonóm rendszer számát (ASN) is megjeleníti: 

![1][1]

A VNet Azure ExpressRoute-átjáró ASN-je eltér a Microsoft Enterprise Edge-útválasztók (Msee) ASN-től. Az ExpressRoute-átjáró egy privát ASN-t ( **65515**-as értéket) használ, és a MSEE nyilvános ASN-t (a **12076**-es értéket) globálisan használja. Ha a ExpressRoute-társítást konfigurálja, mert a MSEE a társ, a **12076** -as társ ASN-t használja. Az Azure oldalon a MSEE eBGP-társítást hoz létre az ExpressRoute-átjáróval. A MSEE által az egyes ExpressRoute-létesítésekhez létrehozott kettős eBGP-társítás transzparens a vezérlési sík szintjén. Ezért amikor megtekint egy ExpressRoute, az VNet ExpressRoute-átjáró ASN-je jelenik meg a VNet előtagjaihoz. 

Az alábbi ábrán egy példa ExpressRoute-útválasztási táblázat látható: 

![5][5]

Az Azure-on belül az ASN-t csak egyenrangú perspektívából érdemes kiszolgálni. Alapértelmezés szerint a ExpressRoute-átjáró és a VPN-átjáró (Azure VPN Gateway) ASN-je **65515**.

## <a name="on-premises-location-1-and-the-remote-vnet-perspective-via-expressroute-1"></a>Helyszíni hely 1 és a távoli VNet perspektíva a ExpressRoute 1 használatával

Mind a helyszíni, mind a távoli VNet az 1. ExpressRoute keresztül csatlakoznak a hub VNet. Osztoznak a topológia azonos perspektívájában, ahogy az a következő ábrán látható:

![2][2]

## <a name="on-premises-location-1-and-the-branch-vnet-perspective-via-a-site-to-site-vpn"></a>Helyszíni hely 1 és a fiókiroda VNet perspektívája helyek közötti VPN-kapcsolaton keresztül

Mind a helyszíni, mind a fiókirodai VNet a helyek közötti VPN-kapcsolaton keresztül csatlakoznak a hub-VNet VPN-átjáróhoz. Osztoznak a topológia azonos perspektívájában, ahogy az a következő ábrán látható:

![3][3]

## <a name="on-premises-location-2-perspective"></a>Helyszíni hely 2 perspektívája

A helyszíni 2. hely egy hub-VNet van csatlakoztatva, a 2. ExpressRoute privát közvetítésével: 

![4][4]

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

Ismerje meg a tesztelési beállítások és az Azure Network monitoring szolgáltatás nézeteinek [adatsíkjainak elemzését][Data-Analysis] .

Tekintse meg a [ExpressRoute kapcsolatos gyakori kérdéseket][ExR-FAQ] :
-   Megtudhatja, hogy hány ExpressRoute-áramkört tud csatlakozni egy ExpressRoute-átjáróhoz.
-   Megtudhatja, hány ExpressRoute-átjárót tud csatlakozni egy ExpressRoute-áramkörhöz.
-   A ExpressRoute egyéb méretezési korlátainak megismerése.


<!--Image References-->
[1]: ./media/backend-interoperability/HubView.png "A topológia központi és küllős VNet perspektívája"
[2]: ./media/backend-interoperability/Loc1ExRView.png "1. hely és a topológia távoli VNet perspektívája az 1. ExpressRoute keresztül"
[3]: ./media/backend-interoperability/Loc1VPNView.png "1. hely és a topológia ág-VNet perspektívája helyek közötti VPN-kapcsolaton keresztül"
[4]: ./media/backend-interoperability/Loc2View.png "2. hely – a topológia perspektívája"
[5]: ./media/backend-interoperability/ExR1-RouteTable.png "ExpressRoute 1 útválasztási táblázat"

<!--Link References-->
[Setup]: ./connectivty-interoperability-preface.md
[Configuration]: ./connectivty-interoperability-configuration.md
[ExpressRoute]: ../expressroute/expressroute-introduction.md
[VPN]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[VNet]: ../virtual-network/tutorial-connect-virtual-networks-portal.md
[Configuration]: ./connectivty-interoperability-configuration.md
[Control-Analysis]:https://docs.microsoft.com/azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: ./connectivty-interoperability-data-plane.md
[ExR-FAQ]: ../expressroute/expressroute-faqs.md
[S2S-Over-ExR]: ../expressroute/site-to-site-vpn-over-microsoft-peering.md
[ExR-S2S-CoEx]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[Hub-n-Spoke]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: /azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: ../virtual-network/virtual-network-manage-peering.md