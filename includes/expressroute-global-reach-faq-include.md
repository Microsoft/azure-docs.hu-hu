---
title: fájl belefoglalása
description: fájl belefoglalása
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 03/19/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 317a480c13c5c6e00653fd61878a379df3f65ac4
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178855"
---
### <a name="what-is-expressroute-global-reach"></a>Mi az az ExpressRoute globális elérhetőségű?

Az ExpressRoute globális elérhetőségű egy Azure-szolgáltatás, amely a helyszíni hálózat az ExpressRoute szolgáltatáson keresztül kapcsolódik a Microsoft globális hálózata révén. Például ha egy privát adatközpontban Windowsról, szilícium-völgy az ExpressRoute és a egy másik privát adatközpontban részt csatlakozik az ExpressRoute globális elérhetőségű, Dallas, az ExpressRoute csatlakozik csatlakoztathatja a privát adatközpontok együtt a két ExpressRoute-kapcsolatok és a határokon keresztül data center forgalmat fog bejárni a Microsoft gerinchálózatába.

### <a name="how-do-i-enable-or-disable-expressroute-global-reach"></a>Hogyan engedélyezése vagy letiltása az ExpressRoute globális elérhetőségű?

Az ExpressRoute-Kapcsolatcsoportok összekapcsolása engedélyezése ExpressRoute globális elérhetőségű. A Kapcsolatcsoportok leválasztásával letiltja a szolgáltatást. Tekintse meg a [konfigurációs](../articles/expressroute/expressroute-howto-set-global-reach.md).

### <a name="do-i-need-expressroute-premium-for-expressroute-global-reach"></a>Szükséges az ExpressRoute Premium az ExpressRoute globális elérhetőségű?

Ha az ExpressRoute-Kapcsolatcsoportok ugyanazon geopolitikai régióban találhatók, nem kell az ExpressRoute Premium használatával kapcsolhatja össze azokat. Ha két ExpressRoute-Kapcsolatcsoportok különböző geopolitikai régióban, szükség van az ExpressRoute Premium mindkét Kapcsolatcsoportok közöttük kapcsolat. 

### <a name="how-will-i-be-charged-for-expressroute-global-reach"></a>Hogyan kell fizetnem az ExpressRoute globális elérhetőségű?

Az ExpressRoute lehetővé teszi, hogy a kapcsolat a helyszíni hálózat és a Microsoft-felhőszolgáltatásokhoz. Globális elérhetőségű ExpressRoute lehetővé teszi, hogy a meglévő ExpressRoute-Kapcsolatcsoportok, kihasználva a Microsoft globális hálózatának keresztül a saját helyszíni hálózat közötti kapcsolat. Az ExpressRoute globális elérhetőségű külön kell fizetni a meglévő ExpressRoute-szolgáltatásból. Nincs egy engedélyezi ezt a szolgáltatást, mindegyik ExpressRoute-kapcsolatcsoport a bővítmény díját. Az ExpressRoute globális elérhetőségű szerint engedélyezve van a helyszíni hálózatok közötti forgalom számítjuk fel a kilépő üzenetek gyakorisága a forráson és a egy bejövő forgalom a célhelyen. A díjak a zónában, amelyen a Kapcsolatcsoportok találhatók alapulnak.

### <a name="where-is-expressroute-global-reach-supported"></a>Ha az ExpressRoute globális elérhetőségű támogatott?

Az ExpressRoute globális elérhetőségű támogatott [országok/régiók vagy helyek kiválasztása](../articles/expressroute/expressroute-global-reach.md). Az ExpressRoute-Kapcsolatcsoportok, a társviszony-létesítési helyszínek ezen országok vagy régiók vagy helyen kell létrehozni.

### <a name="i-have-more-than-two-on-premises-networks-each-connected-to-an-expressroute-circuit-can-i-enable-expressroute-global-reach-to-connect-all-of-my-on-premises-networks-together"></a>Több mint két helyszíni hálózat van, mindegyik ExpressRoute-kapcsolatcsoport csatlakozik. Lehet engedélyezni az ExpressRoute globális elérhetőségű összekapcsolását saját helyi hálózatok mindegyike?

Igen, akkor is, mindaddig, amíg a Kapcsolatcsoportok szerepelnek a támogatott országok/régiók. Két ExpressRoute-Kapcsolatcsoportok egyszerre csatlakozni kell. Egy teljes körűen szövevényes hálózat létrehozásához szüksége kapcsolatcsoport-párok enumerálása, és ismételje meg a konfigurálást. 

### <a name="can-i-enable-expressroute-global-reach-between-two-expressroute-circuits-at-the-same-peering-location"></a>Lehet engedélyezni az ExpressRoute globális elérhetőségű között két ExpressRoute-Kapcsolatcsoportok társviszony-létesítési ugyanarról a helyről?

Nem. A két Kapcsolatcsoportok másik társviszony-létesítési helyszínek között kell lennie. Ha egy támogatott országot/régiót a metro egynél több ExpressRoute-társviszony-létesítési helyszínen, csatlakoztathatja egymáshoz az ExpressRoute-Kapcsolatcsoportok adott metro eltérő társviszony-létesítési helyen létrehozott. 

### <a name="if-expressroute-global-reach-is-enabled-between-circuit-x-and-circuit-y-and-between-circuit-y-and-circuit-z-will-my-on-premises-networks-connected-to-circuit-x-and-circuit-z-talk-to-each-other-via-microsofts-network"></a>Ha ExpressRoute globális elérhetőségű engedélyezve van a kapcsolatcsoport X és Y kapcsolatcsoport között, valamint a kapcsolatcsoport Y és kapcsolatcsoport Z, a rendszer X-kapcsolatcsoporthoz csatlakoztatott saját helyszíni hálózatok és kapcsolatcsoport Z kommunikál egymással keresztül a Microsoft hálózatához?

Nem. Ahhoz, hogy minden második része a helyszíni hálózatok közötti kapcsolatot, csatlakoznia kell a megfelelő ExpressRoute-Kapcsolatcsoportok explicit módon. A fenti példában kapcsolatcsoport X és kapcsolatcsoport Z kell csatlakoztatni. 

### <a name="what-is-the-network-throughput-i-can-expect-between-my-on-premises-networks-after-i-enable-expressroute-global-reach"></a>Mi az a hálózat átviteli sebessége várt lehet után engedélyezhető az ExpressRoute globális elérhetőségű saját helyszíni hálózat között?

A hálózat átviteli sebessége, a helyszíni hálózatok között, az ExpressRoute globális elérhetőségű, engedélyezte a két ExpressRoute-Kapcsolatcsoportok kisebb maximumon. Helyszíni – Azure és helyszíni – helyszíni forgalom megoszthatja az ugyanazon kapcsolatcsoport, és ugyanazt a sávszélesség napi korlát vonatkozik. 

### <a name="with-expressroute-global-reach-what-are-the-limits-on-the-number-of-routes-i-can-advertise-and-the-number-of-routes-i-will-receive"></a>Az ExpressRoute globális elérhetőségű melyek korlátait I meghirdethet útvonalak számát, valamint a fog kapott útvonalak száma?

A Microsoft a az Azure privát társviszony-létesítés meghirdethet útvonalak száma továbbra is egy szabványos kapcsolatcsoport a 4000-es vagy a prémium szintű expressroute-kapcsolatcsoporthoz 10000. Kapni fog a Microsoft az Azure privát társviszony-létesítés útvonalak száma egyezik meg az útvonalakat az Azure virtuális hálózatok és globális elérhetőségű ExpressRoute-n keresztül csatlakozik az útvonalakat más helyszíni hálózatokról. Ellenőrizze, hogy a helyszíni útválasztón egy megfelelő maximális előtag-korlátot állít be. 

### <a name="what-is-the-sla-for-expressroute-global-reach"></a>Mi az a szolgáltatói szerződés tartalmazza az ExpressRoute globális elérhetőségű?

Az ExpressRoute globális elérhetőségű biztosít azonos [rendelkezésre állási SLA](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/) a rendszeres az ExpressRoute szolgáltatással.
