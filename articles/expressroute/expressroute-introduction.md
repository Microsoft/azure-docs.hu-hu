---
title: 'Azure ExpressRoute – Áttekintés: privát kapcsolaton keresztüli csatlakoztatás'
description: Az ExpressRoute technikai áttekintése ismerteti, hogyan terjeszthető ki a helyszíni hálózatát az Azure-ra egy ExpressRoute-kapcsolattal egy privát kapcsolaton keresztül.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: overview
ms.date: 10/05/2020
ms.author: duau
ms.openlocfilehash: 82562eae748753cd785851c5d91f5f152b4c9960
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92206951"
---
# <a name="what-is-azure-expressroute"></a>Mi az az Azure ExpressRoute?
Az ExpressRoute használatával kiterjesztheti helyszíni hálózatait a Microsoft Cloudba egy privát kapcsolaton keresztül, egy kapcsolatszolgáltató segítségével. Az ExpressRoute-tal kapcsolatokat létesíthet a Microsoft-felhőszolgáltatásokkal, például a Microsoft Azure-ral és a Microsoft 365-tel.

Ezek lehetnek hálózatközi (IP VPN), pontok közötti Ethernet-hálózati, vagy kapcsolatszolgáltatón keresztüli virtuális keresztkapcsolatok egy közös elhelyezési létesítményen belül. Az ExpressRoute-kapcsolatok nem haladnak át a nyilvános interneten. Az ExpressRoute-kapcsolatok ezáltal megbízhatóbbak, gyorsabbak, egyenletesebb késésűek és biztonságosabbak a tipikus, interneten keresztüli kapcsolatoknál. További információ a hálózatnak a Microsofthoz való csatlakoztatásáról a ExpressRoute használatával: [ExpressRoute kapcsolati modellek](expressroute-connectivity-models.md).

![ExpressRoute-kapcsolatok áttekintése](./media/expressroute-introduction/expressroute-connection-overview.png)

## <a name="key-benefits"></a>Főbb előnyök

* 3 rétegbeli kapcsolatok a helyszíni hálózat és a Microsoft Cloud közt egy kapcsolatszolgáltatón keresztül. A kapcsolatok lehetnek: bármely elemek közötti (IPVPN) hálózat, pontok közötti Ethernet-kapcsolat vagy egy virtuális keresztkapcsolat egy Ethernet-adatcserélőn keresztül.
* Kapcsolódás a Microsoft-felhőszolgáltatásokhoz az adott geopolitikai régió minden régiójában.
* Globális kapcsolódás a Microsoft-szolgáltatásokhoz az összes régióban az ExpressRoute prémium bővítmény használatával.
* Dinamikus útválasztás a hálózata és a Microsoft között BGP-protokollon keresztül.
* Beépített redundancia minden társviszony-létesítési helyszínen a nagyobb megbízhatóság érdekében.
* A kapcsolatok üzemidő [SLA](https://azure.microsoft.com/support/legal/sla/)-ja.
* QoS-támogatás a Skype Vállalati verziójához.

További információ: [ExpressRoute GYIK](expressroute-faqs.md).

## <a name="features"></a>Funkciók

### <a name="layer-3-connectivity"></a>3. rétegbeli kapcsolatok
A Microsoft egy iparági szabványnak megfelelő dinamikus útválasztási protokollt, a BGP-t alkalmazza az útvonalak cseréjéhez a helyszíni hálózat, az Azure-ban lévő példányai és a Microsoft nyilvános címek között. Több BGP-munkamenetet létesítünk a hálózattal, különböző forgalomprofilokkal. További részletek az [ExpressRoute circuit and routing domains](expressroute-circuit-peerings.md) (ExpressRoute-kapcsolatcsoportok és útválasztási tartományok) című cikkben találhatók.

### <a name="redundancy"></a>Redundancia
Minden [ExpressRoute-áramkör](./expressroute-locations.md#expressroute-locations) két, két Microsoft Enterprise Edge-útválasztóhoz (msee), a kapcsolati szolgáltatótól/a hálózat szélétől. A Microsoft kettős BGP-kapcsolatot igényel a kapcsolatszolgáltatótól/a peremhálózatról – egyet-egyet mindegyik MSEE-hez. Dönthet úgy, hogy nem helyez üzembe redundáns eszközöket/Ethernet-kapcsolatcsoportokat az Ön oldalán. A kapcsolatszolgáltatók azonban redundáns eszközöket használnak annak biztosítása érdekében, hogy az Ön kapcsolatai redundáns módon jussanak el a Microsofthoz. Az [SLA](https://azure.microsoft.com/support/legal/sla/) érvényességének előfeltétele a redundáns 3. rétegbeli kapcsolódási konfiguráció.

### <a name="connectivity-to-microsoft-cloud-services"></a>Kapcsolódás a Microsoft-felhőszolgáltatásokhoz
Az ExpressRoute-kapcsolatok a következő szolgáltatásokhoz biztosítanak hozzáférést:
* Microsoft Azure-szolgáltatások
* Microsoft 365-szolgáltatások

> [!NOTE]
> [!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]
> 

Az ExpressRoute-on keresztül támogatott szolgáltatások részletes listáját az [ExpressRoute gyakori kérdései](expressroute-faqs.md) között találja.

### <a name="connectivity-to-all-regions-within-a-geopolitical-region"></a>Kapcsolódás az összes régióhoz egy geopolitikai régión belül
Csatlakozhat a Microsofthoz a [társviszony-létesítési helyszínek](expressroute-locations.md) egyikén, és elérheti az összes régiót a geopolitikai régióban.

Ha például az Amszterdamban a ExpressRoute-on keresztül csatlakozik a Microsofthoz. Ön hozzáférhet az Észak-és Nyugat-Európában üzemeltetett összes Microsoft Cloud Services-szolgáltatáshoz. A geopolitikai régiók, a hozzájuk rendelt Microsoft-felhőrégiók és a megfelelő ExpressRoute-társviszonylétesítési helyszínek áttekintését az [ExpressRoute-partnerek és -társviszonylétesítési helyszínek](expressroute-locations.md) oldalán találja.

### <a name="global-connectivity-with-expressroute-premium"></a>Globális kapcsolat a ExpressRoute Premium szolgáltatással
A [ExpressRoute prémium](expressroute-faqs.md) szintű engedélyezésével kiterjesztheti a kapcsolatokat a geopolitikai határokon belül. Ha például az ExpressRoute-ben Amszterdamban a Microsofthoz kapcsolódik, akkor a világ minden régiójában üzemeltetett összes Microsoft Cloud Services hozzáférhet. A Dél-Amerikában vagy Ausztráliában üzembe helyezett szolgáltatásokat ugyanúgy érheti el, mint az Észak-és Nyugat-európai régiókat. A nemzeti felhők ki vannak zárva.

### <a name="local-connectivity-with-expressroute-local"></a>Helyi kapcsolat a helyi ExpressRoute
A [helyi SKU](expressroute-faqs.md)engedélyezésével költséghatékonyan is átviheti az adatátvitelt. A helyi SKU használatával a kívánt Azure-régió közelében helyezheti át az adatait egy ExpressRoute-helyre. A helyi adatátviteli szolgáltatás a ExpressRoute-port díját is tartalmazza. 

### <a name="across-on-premises-connectivity-with-expressroute-global-reach"></a>Helyszínek közötti csatlakozási lehetőségek az ExpressRoute Global Reach segítségével
Az ExpressRoute Global Reach használatával adatokat cserélhet a helyszíni helyek között az ExpressRoute-kapcsolatcsoportok csatlakoztatása révén. Ha például van egy olyan privát adatközpontja Kaliforniában, amely egy ExpressRoute áramkörhöz csatlakozik a Szilícium-völgyben, egy másik privát adatközpont pedig Texasban, amely egy ExpressRoute-áramkörhöz csatlakozik a Dallasban. A ExpressRoute Global Reach segítségével összekapcsolhatja saját adatközpontjait a két ExpressRoute-áramkörön keresztül. Az adatközpontok közötti forgalom a Microsoft hálózatán fog áthaladni.

További információ: [ExpressRoute Global Reach](expressroute-global-reach.md).
### <a name="rich-connectivity-partner-ecosystem"></a>A kapcsolati partnerek gazdag ökoszisztémája
Az ExpressRoute kapcsolati és rendszerintegrátor-partnerek egyre bővülő ökoszisztémájával rendelkezik. A legfrissebb információkért lásd [az ExpressRoute-partnereket és társviszony-létesítési helyszíneket](expressroute-locations.md) ismertető szakaszt.

### <a name="connectivity-to-national-clouds"></a>Kapcsolódás országos felhőkhöz
A Microsoft elkülönített felhőkörnyezeteket tart fenn speciális geopolitikai régiók és ügyfélszegmensek számára. Az országos felhők és szolgáltatók listájáért lásd az [ExpressRoute-partnereket és társviszony-létesítési helyszíneket](expressroute-locations.md) ismertető lapot.

### <a name="expressroute-direct"></a>ExpressRoute Direct
Az ExpressRoute Direct közvetlen csatlakozást tesz lehetővé az ügyfelek számára a Microsoft globális hálózatához a világszerte stratégiai pontokon elhelyezett társviszony-létesítési helyszíneken át. A ExpressRoute Direct kettős 100 GB/s-os kapcsolatot biztosít, amely támogatja az aktív/aktív kapcsolatot a skálán.

A közvetlen ExpressRoute által biztosított főbb funkciók közé tartoznak a következők:

* Nagy mennyiségű adat betöltése az olyan szolgáltatásokba, mint például a Storage és az Cosmos DB
* Fizikai elkülönítés a szabályozott, valamint dedikált és elszigetelt kapcsolódást igénylő iparágak, például a banki, kormányzati és kiskereskedelmi ágazatok számára
* A kapcsolatcsoportok elosztásának üzleti egységen alapuló, részletes szabályzása

További információ: [Az ExpressRoute Direct ismertetése](./expressroute-erdirect-about.md).

### <a name="bandwidth-options"></a>Sávszélesség-lehetőségek
A sávszélességek széles választékához vásárolhat ExpressRoute-kapcsolatcsoportokat. A támogatott sávszélességek a következők szerint vannak felsorolva. Mindenképp egyeztessen kapcsolatszolgáltatójával a támogatott sávszélességekről.

* 50 Mbit/s
* 100 Mbit/s
* 200 Mbit/s
* 500 Mbit/s
* 1 Gbps
* 2 Gbps
* 5 Gbps
* 10 Gbps

### <a name="dynamic-scaling-of-bandwidth"></a>Dinamikus sávszélesség-méretezés
Az ExpressRoute-kapcsolatcsoport sávszélességét (az elérhető legjobb lehetőség alapján) anélkül növelheti, hogy le kellene bontania a kapcsolatokat. További információ: ExpressRoute- [áramkör módosítása](expressroute-howto-circuit-portal-resource-manager.md#modify).

### <a name="flexible-billing-models"></a>Rugalmas számlázási modellek
Kiválaszthatja az Ön számára optimális számlázási modellt. Válassza ki a leírtak szerint felsorolt számlázási modelleket. További információért lásd az [ExpressRoute gyakori kérdéseit](expressroute-faqs.md).

* **Korlátlan adatforgalom**. A számlázás havi díj alapján történik, amely az összes bejövő és kimenő adatátvitelt ingyenesen tartalmazza.
* **Forgalmi díjas adatforgalom**. A számlázás havi díj alapján történik, amely az összes bejövő adatátvitelt ingyenesen tartalmazza. A kimenő adatátvitel számlázása az átvitt adatok GB-jai alapján történik. Az adatátviteli árak régiónként eltérnek.
* **ExpressRoute prémium bővítmény**. Az ExpressRoute prémium az ExpressRoute-kapcsolatcsoport bővítménye. Az ExpressRoute prémium bővítmény az alábbi képességeket biztosítja: 
  * Az Azure nyilvános és az Azure privát társviszony-létesítés útvonalkorlátja 4000 útvonalról 10 000 útvonalra nő.
  * Globális kapcsolódás a szolgáltatásokhoz. A bármely régióban (az országos felhők kivételével) létrehozott ExpressRoute-áramkör a világ minden más régiójában hozzáférhet az erőforrásokhoz. Egy Nyugat-Európában létrehozott virtuális hálózat például elérhető egy, a Szilícium-völgyben kiosztott ExpressRoute-kapcsolatcsoportról is.
  * A VNet-hivatkozások száma ExpressRoute-kapcsolatcsoportonként 10-ről egy magasabb korlátra nő a kör sávszélességének függvényében.

## <a name="faq"></a>GYIK
Az ExpressRoute-ra vonatkozó gyakori kérdésekért lásd az [ExpressRoute gyakori kérdéseit](expressroute-faqs.md).

## <a name="whats-new"></a><a name="new"></a>Mi újság?

Fizessen elő az RSS-hírcsatornára, és tekintse meg a legújabb ExpressRoute-frissítéseket az [Azure Updates](https://azure.microsoft.com/updates/?category=networking&query=ExpressRoute) oldalon.

## <a name="next-steps"></a>Következő lépések
* Ellenőrizze, hogy minden előfeltétel teljesül-e. Lásd: [ExpressRoute-előfeltételek](expressroute-prerequisites.md).
* [Az ExpressRoute kapcsolati modelljeinek](expressroute-connectivity-models.md) ismertetése.
* Találjon egy szolgáltatót. Lásd: [ExpressRoute-partnerek és társviszony-létesítési helyszínek](expressroute-locations.md).