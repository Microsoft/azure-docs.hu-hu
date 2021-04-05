---
title: Az Azure és a nyilvános felhők összekapcsolása | Microsoft Docs
description: Az Azure és más nyilvános felhők összekapcsolásának különböző módjai
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 07/24/2019
ms.author: duau
ms.openlocfilehash: fb4c34345ab902e243f22eae06fd5ea174fd2992
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92202191"
---
# <a name="connecting-azure-with-public-clouds"></a>Az Azure csatlakoztatása nyilvános Felhőkkel

Számos vállalat az üzleti és technikai célok miatt több felhős stratégiát folytat. Ilyenek például a következők: Cost, rugalmasság, funkciók rendelkezésre állása, redundancia, adatszuverenitás stb. Ez a stratégia segíti őket abban, hogy mindkét felhőből kihasználja a legjobbat. 

Ez a megközelítés a hálózat és az alkalmazás architektúrájának terén is kihívást jelent a vállalat számára. A kihívások némelyike a késés és az adatátviteli sebesség. A kihívások kezelése érdekében az ügyfelek közvetlenül több felhőhöz csatlakoznak. Egyes szolgáltatók több felhőalapú szolgáltató összekapcsolását teszik lehetővé az ügyfelek számára. Más esetekben az ügyfél telepíthet saját útválasztót több nyilvános felhőhöz való kapcsolódáshoz.
## <a name="connectivity-via-expressroute"></a>Kapcsolódás a ExpressRoute-on keresztül
A ExpressRoute lehetővé teszi, hogy az ügyfelek a kapcsolat szolgáltatója által megvalósuló privát kapcsolaton keresztül bővítsék helyszíni hálózatait a Microsoft-felhőbe. A ExpressRoute segítségével az ügyfelek kapcsolatot létesíthetnek a Microsoft Cloud Services szolgáltatással.

Háromféleképpen csatlakozhat a ExpressRoute-on keresztül.

1. Layer3-szolgáltató
2. Layer2-szolgáltató
3. Közvetlen kapcsolatok

### <a name="layer3-provider"></a>Layer3-szolgáltató

A Layer3-szolgáltatók általában IP VPN-vagy MPLS VPN-szolgáltatók néven ismertek. Az ügyfelek az adatközpontok, a fiókirodák és a felhő közötti multipoint-kapcsolathoz használják ezeket a szolgáltatókat. Az ügyfelek BGP-n keresztül vagy statikus alapértelmezett útvonalon keresztül csatlakoznak az L3-szolgáltatóhoz. A szolgáltató meghirdeti az útvonalakat az ügyfél webhelyei, adatközpontja és a nyilvános felhő között. 
 
A Layer3-szolgáltatón keresztüli csatlakozáskor a Microsoft a BGP-n keresztül hirdeti az ügyfél VNET útvonalakat a szolgáltatónak. A szolgáltatónak két különböző implementációja lehet.

![Egy Layer3-szolgáltatót bemutató diagram.](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l3.png)

Előfordulhat, hogy a szolgáltató minden felhőalapú szolgáltatót külön VRF, ha az összes felhőalapú szolgáltatótól érkező forgalom eléri az ügyfél útválasztóját. Ha az ügyfél a BGP-t futtatja a szolgáltatónál, a rendszer alapértelmezés szerint ezeket az útvonalakat más felhőalapú szolgáltatók számára is újra meghirdeti. 

Ha a szolgáltató az összes felhőalapú szolgáltatót kiadja ugyanabban a VRF, akkor az útvonalakat más felhőalapú szolgáltatók is meghirdetik közvetlenül a szolgáltatótól. Ez olyan Standard BGP-műveletet feltételez, amelyben a eBGP útvonalakat a rendszer alapértelmezés szerint más eBGP-szomszédoknak hirdeti meg.

Minden nyilvános felhő eltérő előtag-korláttal rendelkezik, ezért az útvonalak szolgáltatójának terjesztésekor körültekintően kell eljárni az útvonalak terjesztésekor.

### <a name="layer2-provider-and-direct-connection"></a>Layer2-szolgáltató és közvetlen kapcsolatok

Bár a fizikai kapcsolat mindkét modellben eltérő, de a Layer3 BGP közvetlenül a MSEE és az ügyfél-útválasztó között jön. A közvetlen ExpressRoute ügyfél közvetlenül a MSEE-hoz csatlakozik. A Layer2 esetében a szolgáltató kiterjeszti a VLAN-t az ügyfél helyszínéről a felhőbe. Az ügyfelek BGP-t futtatnak a Layer2-hálózaton a tartományvezérlők felhőhöz való összekapcsolásához.
![Diagram, amely egy Layer2-szolgáltatót és közvetlen kapcsolatokat mutat be.](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l2.png)
Mindkét esetben az ügyfél pont-pont típusú kapcsolattal fog rendelkezni a nyilvános Felhőkkel. Az ügyfél külön BGP-kapcsolatot hoz létre minden nyilvános felhőhöz. Az egyik felhőalapú szolgáltató által fogadott útvonalakat a rendszer alapértelmezés szerint más Felhőbeli szolgáltatónak hirdeti. Az egyes felhőalapú szolgáltatók különböző előtag-korláttal rendelkeznek, így az útvonalakon megjelenő ügyfelek a korlátozásokat is figyelembe vehetik. Az ügyfél a szokásos BGP-gombokat a Microsofttal is használhatja, miközben más nyilvános felhőkből is hirdet útvonalakat.

## <a name="direct-connection-with-expressroute"></a>Közvetlen kapcsolatok a ExpressRoute

Az ügyfelek dönthetnek úgy, hogy közvetlenül csatlakoztatják a ExpressRoute a felhőalapú szolgáltató közvetlen csatlakozási ajánlatához. Két felhőalapú szolgáltató csatlakozik vissza, és a BGP-t közvetlenül az útválasztók között hozza meg. Ez a kapcsolattípus jelenleg az Oracle-vel érhető el.

## <a name="site-to-site-vpn"></a>Helyek közötti VPN

Az ügyfelek az internetet használhatják az Azure-beli példányaik más nyilvános Felhőkkel való összekapcsolására. Szinte az összes felhőalapú szolgáltató kínál webhelyek közötti VPN-képességeket. Bizonyos változatok hiánya miatt azonban előfordulhatnak inkompatibilitások. Előfordulhat például, hogy egyes felhőalapú szolgáltatók csak a IKEv1 támogatják, ezért a felhőben szükség van egy VPN-megszakítási végpontra. A IKEv2-t támogató felhőalapú szolgáltatók a VPN-átjárók között mind a felhőalapú szolgáltatóknál létrehozhatók.

A helyek közötti VPN nem számít nagy átviteli sebességnek és kis késleltetésű megoldásnak. Azonban a fizikai kapcsolat biztonsági mentésére is használható.

## <a name="next-steps"></a>Következő lépések
A ExpressRoute és a virtuális hálózati kapcsolattal kapcsolatos további kérdésekért tekintse meg a [ExpressRoute – gyakori kérdések][ER-FAQ] című témakört.

Lásd: [közvetlen kapcsolat beállítása az Azure és az Oracle Cloud között][ER-OCI] az Azure és az Oracle közötti kapcsolathoz

<!--Link References-->
[ER-FAQ]: ./expressroute-faqs.md
[ER-OCI]: ../virtual-machines/workloads/oracle/configure-azure-oci-networking.md