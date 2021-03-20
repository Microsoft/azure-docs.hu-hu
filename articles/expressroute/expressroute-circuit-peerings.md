---
title: 'Azure ExpressRoute: áramkörök és peering'
description: Ez az oldal áttekintést nyújt a ExpressRoute-áramkörökről és az útválasztási tartományokról és a társításokról.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: duau
ms.openlocfilehash: 87fed1d2ac4f5fa85c01d7af10bec10c1412744f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92202344"
---
# <a name="expressroute-circuits-and-peering"></a>ExpressRoute-kapcsolatcsoportok és -társviszonyok

A ExpressRoute áramkörök kapcsolati szolgáltatón keresztül kapcsolódnak a Microsofthoz a helyszíni infrastruktúrához. Ez a cikk segít megérteni a ExpressRoute-áramköröket és az útválasztási tartományok/társítások megismerését. Az alábbi ábra a WAN és a Microsoft közötti kapcsolat logikai ábrázolását mutatja be.

![Ábra, amely azt mutatja, hogy a ExpressRoute áramkörök hogyan csatlakoztatják a helyszíni infrastruktúrát a Microsofthoz egy kapcsolati szolgáltatón keresztül.](./media/expressroute-circuit-peerings/expressroute-basic.png)

> [!IMPORTANT]
> Az Azure nyilvános társ-kezelési szolgáltatás elavult, és nem érhető el az új ExpressRoute-áramkörökhöz. Az új áramkörök támogatják a Microsoft társ-és magánszféra általi használatát.  
>

## <a name="expressroute-circuits"></a><a name="circuits"></a>ExpressRoute-kapcsolatcsoportok

A ExpressRoute áramkör a helyszíni infrastruktúra és a Microsoft Cloud Services közötti logikai kapcsolatot jelenti a kapcsolat szolgáltatóján keresztül. Több ExpressRoute-áramkört is rendelhet. Minden áramkör lehet azonos vagy különböző régiókban, és a különböző kapcsolati szolgáltatókon keresztül csatlakozhat a telephelyéhez.

A ExpressRoute-áramkörök nem képezhetők le fizikai entitásokra. Az áramkört egyedileg azonosítják egy standard GUID azonosítóval, amelyet szolgáltatási kulcsként (s-Key) hívnak. A szolgáltatás kulcsa az egyetlen, a Microsoft, a kapcsolat szolgáltatója és Ön között cserélt információ. Az s-Key nem titkos biztonsági célokra. Egy ExpressRoute-áramkör és az s-kulcs között 1:1 leképezés található.

Az új ExpressRoute-áramkörök két független társat tartalmazhatnak: a privát és a Microsoft-partneri kapcsolatot. Míg a meglévő ExpressRoute-áramkörök három alhálózatot tartalmazhatnak: az Azure nyilvános, az Azure Private és a Microsoft. Az egyes társítások független BGP-munkamenetek, amelyek mindegyike redundánsan van konfigurálva a magas rendelkezésre állás érdekében. A ExpressRoute áramkör és az útválasztási tartományok között 1: N (1 <= N <= 3) leképezés található. Az ExpressRoute-áramkör lehet egy, kettő vagy mindhárom, ExpressRoute áramkörön engedélyezett kapcsolat.

Minden áramkör rögzített sávszélességgel rendelkezik (50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 GB/s, 10 GB/s), és egy kapcsolati szolgáltatóhoz és egy egyenrangú helyhez van rendelve. A kiválasztott sávszélesség az összes áramköri kapcsolat között meg van osztva

### <a name="quotas-limits-and-limitations"></a><a name="quotas"></a>Kvóták, korlátozások és korlátozások

Az alapértelmezett kvóták és korlátozások minden ExpressRoute-áramkör esetében érvényesek. Tekintse meg az [Azure-előfizetések és-szolgáltatások korlátozásait, kvótáit és megkötéseit](../azure-resource-manager/management/azure-subscription-service-limits.md) ismertető oldalt a kvótákkal kapcsolatos naprakész információkhoz.

## <a name="expressroute-peering"></a><a name="routingdomains"></a>ExpressRoute-társítás

Egy ExpressRoute-áramkörhöz több útválasztási tartomány/társ tartozik: az Azure Public, az Azure Private és a Microsoft. Minden társítás azonos módon van konfigurálva egy útválasztón (aktív-aktív vagy terheléselosztási konfigurációban) a magas rendelkezésre állás érdekében. Az Azure-szolgáltatások az IP-címzési sémákat képviselő Azure-beli *nyilvános* és *Azure Private* -ként vannak kategorizálva.

![Ábra, amely bemutatja, hogyan vannak konfigurálva az Azure nyilvános, az Azure Private-és a Microsoft-partnerek egy ExpressRoute-áramkörben.](./media/expressroute-circuit-peerings/expressroute-peerings.png)

### <a name="azure-private-peering"></a><a name="privatepeering"></a>Azure-beli privát társhálózat-létesítés

A virtuális hálózatokon belül üzembe helyezett Azure számítási szolgáltatások, nevezetesen a virtuális gépek (IaaS-EK) és a Cloud Services (Pásti) a privát társ-létrehozási tartományon keresztül csatlakoztathatók. A privát társ-létrehozási tartomány az alapvető hálózat megbízható kiterjesztésének tekintendő Microsoft Azureba. Az alapvető hálózat és az Azure-beli virtuális hálózatok (virtuális hálózatok-EK) között kétirányú kapcsolat is beállítható. Ez a társítás lehetővé teszi a virtuális gépekhez és a felhőalapú szolgáltatásokhoz való közvetlen kapcsolódást a magánhálózati IP-címeken.  

Több virtuális hálózatot is összekapcsolhat a privát társ-létrehozási tartományhoz. A korlátozásokkal és korlátozásokkal kapcsolatos információkért tekintse át a [GYIK oldalt](expressroute-faqs.md) . Az [Azure-előfizetések és-szolgáltatások korlátozásait, kvótáit és megkötéseit](../azure-resource-manager/management/azure-subscription-service-limits.md) ismertető oldalt megtekintheti a korlátozásokkal kapcsolatos naprakész információkhoz.  Az útválasztási konfigurációval kapcsolatos részletes információkért tekintse meg az [Útválasztás](expressroute-routing.md) lapot.

### <a name="microsoft-peering"></a><a name="microsoftpeering"></a>Microsoft-partnerek

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

A Microsoft online szolgáltatások (Microsoft 365 és az Azure Pásti Services) kapcsolata a Microsoft-partnereken keresztül történik. A WAN és a Microsoft Cloud Services közötti kétirányú kapcsolatot a Microsoft társközi útválasztási tartományán keresztül engedélyezzük. Csak olyan nyilvános IP-címeken keresztül csatlakozhat a Microsoft Cloud Services szolgáltatáshoz, amelyek tulajdonosa Ön vagy a kapcsolat szolgáltatója, és be kell tartania az összes definiált szabályt. További információkért tekintse meg a [ExpressRoute előfeltételek](expressroute-prerequisites.md) oldalát.

A támogatott szolgáltatásokkal, a költségekkel és a konfigurációs adatokkal kapcsolatos további információkért tekintse meg a [GYIK oldalt](expressroute-faqs.md) . A Microsoft társközi támogatását kínáló kapcsolati szolgáltatók listáját a [ExpressRoute helye](expressroute-locations.md) oldalon tekintheti meg.

## <a name="peering-comparison"></a><a name="peeringcompare"></a>Egyenrangú összehasonlítás

A következő táblázat összehasonlítja a három társat:

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

Egy vagy több útválasztási tartományt engedélyezhet a ExpressRoute-áramkör részeként. Megadhatja, hogy az összes útválasztási tartomány ugyanarra a VPN-re kerüljön, ha egyetlen útválasztási tartományba kívánja összevonni őket. Ezeket a diagramhoz hasonlóan különböző útválasztási tartományokra is felhelyezheti. Az ajánlott konfiguráció az, hogy a privát társak közvetlenül az alaphálózathoz csatlakoznak, a nyilvános és a Microsoft-kapcsolatok pedig a DMZ-hez csatlakoznak.

Minden egyes társításhoz külön BGP-munkamenet szükséges (egy pár az egyes társítási típusokhoz). A BGP-munkamenet párok egy magasan elérhető hivatkozást biztosítanak. Ha 2. rétegbeli kapcsolati szolgáltatón keresztül csatlakozik, Ön felelős az útválasztás konfigurálásához és kezeléséhez. További információ: a ExpressRoute beállításához szükséges [munkafolyamatok](expressroute-workflows.md) áttekintése.

## <a name="expressroute-health"></a><a name="health"></a>Az ExpressRoute állapota

A ExpressRoute-áramköröket a rendelkezésre állás, a virtuális hálózatok és a sávszélesség-használat között a [Network Performance monitor](../networking/network-monitoring-overview.md) (NPM) használatával lehet figyelni.

A NPM az Azure-beli privát és a Microsoft-partnerek állapotát figyeli. További információért tekintse meg a [hozzászólását](https://azure.microsoft.com/blog/monitoring-of-azure-expressroute-in-preview/) .

## <a name="next-steps"></a>Következő lépések

* Találjon egy szolgáltatót. Lásd: [ExpressRoute Service Providers és Locations](expressroute-locations.md).
* Ellenőrizze, hogy minden előfeltétel teljesül-e. Lásd: [ExpressRoute-előfeltételek](expressroute-prerequisites.md).
* Az ExpressRoute-kapcsolat konfigurálása.
  * [ExpressRoute-kapcsolatcsoportok létrehozása és kezelése](expressroute-howto-circuit-portal-resource-manager.md)
  * [Útválasztás (társviszony-létesítés) konfigurálása ExpressRoute-kapcsolatcsoportok számára](expressroute-howto-routing-portal-resource-manager.md)