---
title: 'Együttműködés az Azure háttérbeli kapcsolati szolgáltatásaiban: konfigurációs részletek | Microsoft Docs'
description: Ez a cikk a ExpressRoute, a helyek közötti VPN-kapcsolat és az Azure-beli virtuális hálózatok közötti együttműködés elemzéséhez használható konfigurációs adatokat ismerteti.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 50fd5641c61d08939eca1f2cbafb1d077254e37d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98233270"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-configuration-details"></a>Együttműködés az Azure háttérbeli kapcsolati szolgáltatásaiban: konfiguráció részleteinek tesztelése

Ez a cikk a [teszt beállításának][Setup]konfigurációs részleteit ismerteti. A teszt beállítása segít elemezni, hogy az Azure hálózati szolgáltatásai Hogyan működjenek együtt a vezérlési síkon és az adatsík szintjén.

## <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Küllős VNet-kapcsolat a VNet-társítás használatával

Az alábbi ábrán egy küllős virtuális hálózat (VNet) Azure Virtual Network-összevonási részletei láthatók. Ha meg szeretné tudni, hogyan állíthatja be a két virtuális hálózatok közötti társítást, tekintse meg a [VNet-kezelés kezelése][VNet-Config]című témakört. Ha azt szeretné, hogy a küllős VNet a hub VNet csatlakozó átjárókat használják, válassza a **távoli átjárók használata** lehetőséget.

[![1]][1]

Az alábbi ábra a hub-VNet VNet-társítási részleteit mutatja be. Ha azt szeretné, hogy a hub VNet engedélyezze a küllős VNet számára a hub átjáróinak használatát, válassza az **átjáró továbbításának engedélyezése** lehetőséget.

[![2]][2]

## <a name="branch-vnet-connectivity-by-using-a-site-to-site-vpn"></a>Ág-VNet kapcsolódás helyek közötti VPN használatával

A helyek közötti VPN-kapcsolat beállítása a hub és az ág közötti virtuális hálózatok az Azure VPN Gateway VPN-átjárók használatával. Alapértelmezés szerint a VPN-átjárók és az Azure ExpressRoute-átjárók a **65515**-as privát autonóm rendszer számát (ASN) használják. VPN Gatewayban módosíthatja az ASN-értéket. A teszt beállításakor a fiókiroda VNet VPN-átjáró ASN-értéke **65516** -ra változik, hogy támogassa a eBGP útválasztást a hub és az ág virtuális hálózatok között.


[![3]][3]


## <a name="on-premises-location-1-connectivity-by-using-expressroute-and-a-site-to-site-vpn"></a>Helyszíni hely 1. kapcsolódás a ExpressRoute és a helyek közötti VPN használatával

### <a name="expressroute-1-configuration-details"></a>1. ExpressRoute konfigurációs adatok

Az alábbi ábra az Azure régió 1 ExpressRoute áramköri konfigurációját mutatja be a helyszíni hely 1 Customer Edge (CE) útválasztók felé:

[![4]][4]

Az alábbi ábrán a ExpressRoute 1 áramkör és a hub VNet közötti kapcsolati konfiguráció látható:

[![5]][5]

A következő lista az elsődleges CE-útválasztó konfigurációját mutatja be a ExpressRoute magánhálózati kapcsolathoz. (A Cisco ASR1000 útválasztók a tesztelési beállításban CE-útválasztóként használatosak.) Ha a helyek közötti VPN-és ExpressRoute-áramkörök párhuzamosan vannak konfigurálva a helyszíni hálózat Azure-hoz való összekapcsolásához, az Azure alapértelmezés szerint rangsorolja az ExpressRoute áramkört. Az aszimmetrikus útválasztás elkerüléséhez a helyszíni hálózatnak a helyek közötti VPN-kapcsolaton keresztüli ExpressRoute-kapcsolatot is rangsorolnia kell. A következő konfiguráció a BGP **Local-preferencia** attribútum használatával határozza meg a rangsorolást:

```config
interface TenGigabitEthernet0/0/0.300
 description Customer 30 private peering to Azure
 encapsulation dot1Q 30 second-dot1q 300
 ip vrf forwarding 30
 ip address 192.168.30.17 255.255.255.252
!
interface TenGigabitEthernet1/0/0.30
 description Customer 30 to south bound LAN switch
 encapsulation dot1Q 30
 ip vrf forwarding 30
 ip address 192.168.30.0 255.255.255.254
 ip ospf network point-to-point
!
router ospf 30 vrf 30
 router-id 10.2.30.253
 redistribute bgp 65021 subnets route-map BGP2OSPF
 network 192.168.30.0 0.0.0.1 area 0.0.0.0
default-information originate always
 default-metric 10
!
router bgp 65021
 !
 address-family ipv4 vrf 30
  network 10.2.30.0 mask 255.255.255.128
  neighbor 192.168.30.18 remote-as 12076
  neighbor 192.168.30.18 activate
  neighbor 192.168.30.18 next-hop-self
  neighbor 192.168.30.18 soft-reconfiguration inbound
  neighbor 192.168.30.18 route-map prefer-ER-over-VPN in
  neighbor 192.168.30.18 prefix-list Cust30_to_Private out
 exit-address-family
!
route-map prefer-ER-over-VPN permit 10
 set local-preference 200
!
ip prefix-list Cust30_to_Private seq 10 permit 10.2.30.0/25
!
```

### <a name="site-to-site-vpn-configuration-details"></a>Helyek közötti VPN-konfiguráció részletei

Az alábbi lista a helyek közötti VPN-kapcsolat elsődleges CE útválasztó-konfigurációját mutatja be:

```config
crypto ikev2 proposal Cust30-azure-proposal
 encryption aes-cbc-256 aes-cbc-128 3des
 integrity sha1
 group 2
!
crypto ikev2 policy Cust30-azure-policy
 match address local 66.198.12.106
 proposal Cust30-azure-proposal
!
crypto ikev2 keyring Cust30-azure-keyring
 peer azure
  address 52.168.162.84
  pre-shared-key local IamSecure123
  pre-shared-key remote IamSecure123
!
crypto ikev2 profile Cust30-azure-profile
 match identity remote address 52.168.162.84 255.255.255.255
 identity local address 66.198.12.106
 authentication local pre-share
 authentication remote pre-share
 keyring local Cust30-azure-keyring
!
crypto ipsec transform-set Cust30-azure-ipsec-proposal-set esp-aes 256 esp-sha-hmac
 mode tunnel
!
crypto ipsec profile Cust30-azure-ipsec-profile
 set transform-set Cust30-azure-ipsec-proposal-set
 set ikev2-profile Cust30-azure-profile
!
interface Loopback30
 ip address 66.198.12.106 255.255.255.255
!
interface Tunnel30
 ip vrf forwarding 30
 ip address 10.2.30.125 255.255.255.255
 tunnel source Loopback30
 tunnel mode ipsec ipv4
 tunnel destination 52.168.162.84
 tunnel protection ipsec profile Cust30-azure-ipsec-profile
!
router bgp 65021
 !
 address-family ipv4 vrf 30
  network 10.2.30.0 mask 255.255.255.128
  neighbor 10.10.30.254 remote-as 65515
  neighbor 10.10.30.254 ebgp-multihop 5
  neighbor 10.10.30.254 update-source Tunnel30
  neighbor 10.10.30.254 activate
  neighbor 10.10.30.254 soft-reconfiguration inbound
 exit-address-family
!
ip route vrf 30 10.10.30.254 255.255.255.255 Tunnel30
```

## <a name="on-premises-location-2-connectivity-by-using-expressroute"></a>Helyszíni 2. hely – kapcsolat a ExpressRoute használatával

A második ExpressRoute áramkör a helyszíni hely 2. pontjának közelében csatlakozik a helyszíni 2. helyhez a hub VNet. A következő ábra a második ExpressRoute-konfigurációt mutatja be:

[![6]][6]

Az alábbi ábrán a második ExpressRoute áramkör és a hub VNet közötti kapcsolati konfiguráció látható:

[![7]][7]

A ExpressRoute 1 a hub-VNet és a helyszíni 1. helyet is összekapcsolja egy másik Azure-régióban található távoli VNet:

[![8]][8]

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

Ismerje meg a [vezérlési sík elemzését][Control-Analysis] a tesztelési beállításról, valamint a topológia különböző virtuális hálózatok vagy VLAN-ok nézeteiről.

Ismerje meg a tesztelési beállítások és az Azure Network monitoring szolgáltatás nézeteinek [adatsíkjainak elemzését][Data-Analysis] .

Tekintse meg a [ExpressRoute kapcsolatos gyakori kérdéseket][ExR-FAQ] :
-   Megtudhatja, hogy hány ExpressRoute-áramkört tud csatlakozni egy ExpressRoute-átjáróhoz.
-   Megtudhatja, hány ExpressRoute-átjárót tud csatlakozni egy ExpressRoute-áramkörhöz.
-   A ExpressRoute egyéb méretezési korlátainak megismerése.


<!--Image References-->
[1]: ./media/backend-interoperability/SpokeVNet_peering.png "küllős VNet VNet-társának"
[2]: ./media/backend-interoperability/HubVNet-peering.png "hub-VNet VNet" -társítása
[3]: ./media/backend-interoperability/BranchVNet-VPNGW.png "VPN Gateway ág VNet konfigurálása"
[4]: ./media/backend-interoperability/ExR1.png "ExpressRoute 1 konfiguráció"
[5]: ./media/backend-interoperability/ExR1-Hub-Connection.png "ExpressRoute-konfiguráció 1 – hub VNet ExR Gateway"
[6]: ./media/backend-interoperability/ExR2.png "ExpressRoute 2 konfiguráció"
[7]: ./media/backend-interoperability/ExR2-Hub-Connection.pnga "2. ExpressRoute kapcsolódása a hub VNet ExR-átjáróhoz"
[8]: ./media/backend-interoperability/ExR2-Remote-Connection.pnga "2. ExpressRoute kapcsolódása egy távoli VNet ExR-átjáróhoz"

<!--Link References-->
[Setup]: ./connectivty-interoperability-preface.md
[ExpressRoute]: ../expressroute/expressroute-introduction.md
[VPN]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[VNet]: ../virtual-network/tutorial-connect-virtual-networks-portal.md
[Control-Analysis]: ./connectivty-interoperability-control-plane.md
[Data-Analysis]: ./connectivty-interoperability-data-plane.md
[ExR-FAQ]: ../expressroute/expressroute-faqs.md
[S2S-Over-ExR]: ../expressroute/site-to-site-vpn-over-microsoft-peering.md
[ExR-S2S-CoEx]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[Hub-n-Spoke]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: /azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: ../virtual-network/virtual-network-manage-peering.md