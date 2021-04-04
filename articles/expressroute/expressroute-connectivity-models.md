---
title: 'Azure ExpressRoute: kapcsolati modellek'
description: Tekintse át az ügyfél hálózati, Microsoft Azure és Microsoft 365 szolgáltatásainak kapcsolatát. Az ügyfelek használhatják a MPLS-szolgáltatókat, a felhőalapú cseréket és az Ethernetet.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: duau
ms.openlocfilehash: fb35a03b5dd8780445eb27f485966e3c3452feea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91978754"
---
# <a name="expressroute-connectivity-models"></a>ExpressRoute kapcsolati modellek
Négy különböző módon hozhat létre kapcsolatot a helyszíni hálózat és a Microsoft Cloud között, [CloudExchange a közös elhelyezést](#CloudExchange), [pont-pont típusú Ethernet-kapcsolatot](#Ethernet), [bármely-a-minden (IPVPN) kapcsolatot](#IPVPN)és a [közvetlen ExpressRoute](#Direct). A kapcsolódási szolgáltatók egy vagy több kapcsolati modellt is tartalmazhatnak. Kapcsolatszolgáltatójával együttműködve választhatja ki az Ön számára optimális modellt.
<br><br>

:::image type="content" source="./media/expressroute-connectivity-models/expressroute-connectivity-models-diagram.png" alt-text="Az ExpressRoute kapcsolati modellek diagramja":::

## <a name="co-located-at-a-cloud-exchange"></a><a name="CloudExchange"></a>Közös elhelyezés felhőalapú adatcsere keretében
Ha a Felhőbeli Exchange-ben közösen helyezkedik el, a közös elhelyezésű szolgáltató Ethernet-adatcseréjén keresztül megoszthatja a virtuális kapcsolatok közötti kapcsolatokat a Microsoft Cloud szolgáltatásban. A közöskörnyezet-szolgáltatók 2. rétegbeli keresztkapcsolatokat vagy felügyelt, 3. rétegbeli keresztkapcsolatokat kínálnak a közös elhelyezési létesítményben lévő infrastruktúra és a Microsoft-felhő között.

## <a name="point-to-point-ethernet-connections"></a><a name="Ethernet"></a>Pontok közti Ethernet-kapcsolatok
Helyszíni adatközpontjait/irodáit pontok közötti Ethernet-hivatkozásokon keresztül is csatlakoztathatja a Microsoft Cloudhoz. A pontok közötti Ethernet-szolgáltatók vagy 2. rétegbeli kapcsolatokat vagy felügyelt 3. rétegbeli kapcsolatokat kínálnak a hely és a Microsoft Cloud között.

## <a name="any-to-any-ipvpn-networks"></a><a name="IPVPN"></a>Bármely elemek közötti (IPVPN-) hálózat
WAN hálózatát integrálhatja a Microsoft Clouddal. Az IPVPN-szolgáltatók (jellemzően MPLS VPN) bármilyen elemek közötti kapcsolódást kínálnak a fiókirodák és az adatközpontok közti kapcsolatokhoz. A Microsoft Cloud összekapcsolható a WAN hálózatával, így ugyanúgy jelenik meg, mint bármely másik fiókiroda. A WAN-szolgáltatók jellemzően felügyelt 3. rétegbeli kapcsolatokat kínálnak. Az ExpressRoute képességi és szolgáltatásai a fenti kapcsolódási modellek mindegyikében megegyeznek.

## <a name="direct-from-expressroute-sites"></a><a name="Direct"></a>Közvetlen a ExpressRoute-webhelyekről
Közvetlenül kapcsolódhat a Microsoft globális hálózatához egy, a világ minden pontján elérhető stratégiai módon elosztott helyen. A [ExpressRoute Direct](expressroute-erdirect-about.md) kettős 100 GB/s-os vagy 10 GB/s közötti kapcsolatot biztosít, amely támogatja az aktív/aktív kapcsolatot a skálán.

## <a name="next-steps"></a>Következő lépések
* Ismerje meg az ExpressRoute-kapcsolatokat és útválasztási tartományokat. Lásd: [ExpressRoute-kapcsolatcsoportok és útválasztási tartományok](expressroute-circuit-peerings.md).
* Tudjon meg többet az ExpressRoute funkcióiról. Lásd: [Az ExpressRoute technikai áttekintése](expressroute-introduction.md)
* Találjon egy szolgáltatót. Lásd: [ExpressRoute-partnerek és társviszony-létesítési helyszínek](expressroute-locations.md).
* Ellenőrizze, hogy minden előfeltétel teljesül-e. Lásd: [ExpressRoute-előfeltételek](expressroute-prerequisites.md).
* Tekintse meg az [Útválasztás](expressroute-routing.md), a [NAT](expressroute-nat.md) és a [QoS](expressroute-qos.md) követelményeit.
* Az ExpressRoute-kapcsolat konfigurálása.
  * [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-portal-resource-manager.md)
  * [Útválasztás konfigurálása](expressroute-howto-routing-portal-resource-manager.md)
  * [VNet csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz](expressroute-howto-linkvnet-portal-resource-manager.md)