---
title: Azure-beli virtuális hálózatok megtervezése | Microsoft Docs
description: Megtudhatja, hogyan tervezhet virtuális hálózatokat az elkülönítési, kapcsolati és helyhez kapcsolódó követelményeknek megfelelően.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2020
ms.author: kumud
ms.openlocfilehash: 9ed060c1f3fc47aa7050c0f9b25c757953fa22fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98220337"
---
# <a name="plan-virtual-networks"></a>Virtuális hálózatok tervezése

A virtuális hálózatnak a kísérletezéshez való létrehozása elég egyszerű, de előfordulhat, hogy több virtuális hálózatot helyez üzembe az idő múlásával, hogy támogassa a szervezet termelési igényeit. Bizonyos tervezési lehetőséggel virtuális hálózatokat telepíthet, és hatékonyabban csatlakozhat a szükséges erőforrásokhoz. A cikkben szereplő információk akkor hasznosak, ha már ismeri a virtuális hálózatokat, és némi tapasztalattal rendelkezik velük kapcsolatban. Ha nem ismeri a virtuális hálózatokat, javasoljuk, hogy olvassa el a [virtuális hálózatok áttekintése című témakört](virtual-networks-overview.md).

## <a name="naming"></a>Elnevezés

Minden Azure-erőforrásnak van neve. A névnek egyedinek kell lennie egy hatókörön belül, amely az egyes erőforrástípusok esetében eltérő lehet. Például egy virtuális hálózat nevének egyedinek kell lennie egy [erőforráscsoporthoz](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)belül, de az [előfizetésben](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) vagy az Azure- [régióban](https://azure.microsoft.com/regions/#services)is duplikálható. Olyan elnevezési konvenció meghatározása, amely következetesen használható, ha az erőforrások elnevezése hasznos, amikor több hálózati erőforrást kezel egyszerre. Javaslatokért lásd: [elnevezési konvenciók](../azure-resource-manager/management/resource-name-rules.md#microsoftnetwork).

## <a name="regions"></a>Régiók

Az összes Azure-erőforrás egy Azure-régióban és-előfizetésben jön létre. Egy erőforrás csak olyan virtuális hálózatban hozható létre, amely ugyanabban a régióban és előfizetésben található, mint az erőforrás. Azonban a különböző előfizetésekben és régiókban található virtuális hálózatokat is összekapcsolhatjuk. További információ: [kapcsolat](#connectivity). Annak eldöntése során, hogy mely régió (k) hoz üzembe erőforrásokat a alkalmazásban, gondolja át, hogy az erőforrások felhasználóinak fizikailag hol találhatók:

- Az erőforrások felhasználói általában a legalacsonyabb hálózati késést szeretnék erőforrásaik számára. A megadott hely és az Azure-régiók közötti relatív késések meghatározásához tekintse meg a [relatív késések megtekintése](../network-watcher/view-relative-latencies.md?toc=%2fazure%2fvirtual-network%2ftoc.json)című témakört.
- Rendelkezik adattárolási, szuverenitási, megfelelőségi vagy rugalmassági követelményekkel? Ha igen, a követelményekhez igazodó régió kiválasztása kritikus fontosságú. További információ: [Azure földrajzi](https://azure.microsoft.com/global-infrastructure/geographies/)területek.
- Szükség van-e rugalmasságra a központilag telepített erőforrások ugyanazon Azure-régión belüli Azure Availability Zones között? Az erőforrásokat, például a virtuális gépeket (VM) a különböző rendelkezésre állási zónákra is telepítheti ugyanazon a virtuális hálózaton belül. Azonban nem minden Azure-régió támogatja a rendelkezésre állási zónákat. Ha többet szeretne megtudni a rendelkezésre állási zónákról és az azokat támogató régiókról, tekintse meg a [rendelkezésre állási zónákat](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="subscriptions"></a>Előfizetések

Az egyes előfizetésekben igény szerint több virtuális hálózatot is telepíthet, akár a [korlátot](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Néhány szervezet különböző előfizetésekkel rendelkezik a különböző részlegekhez, például:. Az előfizetésekkel kapcsolatos további információkért és szempontokért lásd: [előfizetés-szabályozás](/azure/cloud-adoption-framework/reference/migration-with-enterprise-scaffold#define-your-hierarchy).

## <a name="segmentation"></a>Szegmentálás

Előfizetéshez és régiónként több virtuális hálózatot is létrehozhat. Az egyes virtuális hálózatokon belül több alhálózatot is létrehozhat. A következő megfontolások segítséget nyújtanak a szükséges virtuális hálózatok és alhálózatok számának meghatározásához:

### <a name="virtual-networks"></a>Virtuális hálózatok

A virtuális hálózatok az Azure nyilvános hálózat virtuális, elkülönített részei. Minden virtuális hálózat az előfizetéséhez van hozzárendelve. Figyelembe kell venni, hogy egy virtuális hálózatot vagy egy előfizetésben több virtuális hálózatot kell-e létrehozni:

- Léteznek olyan szervezeti biztonsági követelmények, amelyek elkülönítik a forgalmat különálló virtuális hálózatokra? Választhatja a virtuális hálózatok összekapcsolását is. Ha virtuális hálózatokat kapcsol össze, a virtuális hálózatok közötti adatforgalom szabályozásához létrehozhat egy hálózati virtuális berendezést, például egy tűzfalat. További információ: [Biztonság](#security) és [Kapcsolódás](#connectivity).
- Léteznek szervezeti követelmények a virtuális hálózatok elkülönített [előfizetésekre](#subscriptions) vagy [régiókra](#regions)való elkülönítéséhez?
- A [hálózati adapterek](virtual-network-network-interface.md) lehetővé teszik a virtuális gépek számára más erőforrásokkal való kommunikációt. Minden hálózati adapterhez hozzá van rendelve egy vagy több magánhálózati IP-cím. Hány hálózati adapterre és [magánhálózati IP-címre](./private-ip-addresses.md) van szükség egy virtuális hálózaton? A virtuális hálózaton belül elérhető hálózati adapterek és magánhálózati IP-címek száma [korlátozott](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) .
- Szeretne csatlakozni a virtuális hálózathoz egy másik virtuális hálózathoz vagy helyszíni hálózathoz? Dönthet úgy, hogy egyes virtuális hálózatokat összekapcsol egymással vagy helyszíni hálózatokkal, másokat azonban nem. További információ: [kapcsolat](#connectivity). Minden virtuális hálózatnak, amelyhez egy másik virtuális hálózathoz vagy helyszíni hálózathoz csatlakozik, egyedi címtartomány szükséges. Minden virtuális hálózat egy vagy több nyilvános vagy magánhálózati címtartomány van hozzárendelve a Címterület számára. A címtartomány osztály nélküli internetes tartomány-útválasztási (CIDR) formátumban van megadva, például 10.0.0.0/16. További információ a virtuális hálózatok [címeinek tartományáról](manage-virtual-network.md#add-or-remove-an-address-range) .
- Vannak szervezeti adminisztrációs követelményei a különböző virtuális hálózatok erőforrásaihoz? Ha igen, külön virtuális hálózatra különítheti el az erőforrásokat, hogy leegyszerűsítse az [engedélyek hozzárendelését](#permissions) a szervezeten belüli személyeknek, vagy különböző házirendeket rendeljen a különböző virtuális hálózatokhoz.
- Amikor egyes Azure-szolgáltatási erőforrásokat telepít egy virtuális hálózatba, a saját virtuális hálózatot hoznak létre. Annak megállapításához, hogy egy Azure-szolgáltatás létrehoz-e saját virtuális hálózatot, tekintse meg az egyes Azure-szolgáltatásokra vonatkozó információkat, [amelyeket üzembe helyezhet egy virtuális hálózaton](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network).

### <a name="subnets"></a>Alhálózatok

A virtuális hálózatok egy vagy több alhálózatra oszthatók fel a [korlátokig](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Figyelembe kell vennie, hogy egy adott alhálózatot vagy egy előfizetésben több virtuális hálózatot kell-e létrehozni:

- Minden alhálózatnak rendelkeznie kell egy, a virtuális hálózat CIDR belül megadott egyedi címtartomány-tartománnyal. A címtartomány nem fedi át a virtuális hálózat más alhálózatait.
- Ha egyes Azure-szolgáltatási erőforrásokat egy virtuális hálózatba kíván telepíteni, akkor előfordulhat, hogy a saját alhálózatát kell létrehoznia, vagy létre kell hoznia a saját alhálózatát, így ehhez elegendő szabad területnek kell lennie. Annak megállapításához, hogy egy Azure-szolgáltatás saját alhálózatot hoz létre, tekintse meg az egyes Azure-szolgáltatások információit, [amelyek telepíthetők egy virtuális hálózatba](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Ha például egy Azure VPN Gateway használatával csatlakozik egy virtuális hálózathoz egy helyszíni hálózathoz, a virtuális hálózatnak dedikált alhálózattal kell rendelkeznie az átjáróhoz. További információ az [átjáró-alhálózatokról](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub).
- Az Azure alapértelmezés szerint a virtuális hálózat összes alhálózata között irányítja a hálózati forgalmat. Felülbírálhatja az Azure alapértelmezett útválasztását, így megakadályozhatja az alhálózatok közötti Azure-útválasztást, vagy átirányíthatja az alhálózatok közötti forgalmat egy hálózati virtuális berendezésen keresztül, például a következő módon:. Ha egy hálózati virtuális berendezésen (NVA) keresztül egy virtuális hálózati folyamat erőforrásai közötti adatforgalomra van szüksége, telepítse az erőforrásokat a különböző alhálózatokra. További tudnivalók a [biztonságról](#security).
- Korlátozhatja az Azure-erőforrásokhoz, például az Azure Storage-fiókokhoz vagy a Azure SQL Databaseokhoz való hozzáférést egy virtuális hálózati szolgáltatás végpontját tartalmazó adott alhálózatokra. További lehetőségként megtagadhatja az internetről érkező erőforrásokhoz való hozzáférést. Több alhálózatot is létrehozhat, és egyes alhálózatokon engedélyezheti a szolgáltatási végpontot, másokat azonban nem. További információ a [szolgáltatási végpontokról](virtual-network-service-endpoints-overview.md)és az Azure-erőforrásokról, amelyekhez engedélyezheti azokat.
- A virtuális hálózatok mindegyik alhálózatához nulla vagy egy hálózati biztonsági csoportot is hozzárendelhet. Az egyes alhálózatokhoz ugyanazokat vagy egy másik hálózati biztonsági csoportot lehet hozzárendelni. Minden hálózati biztonsági csoport olyan szabályokat tartalmaz, amelyek engedélyezik vagy megtagadják a forgalmat a forrásokra és a célhelyekre. További információ a [hálózati biztonsági csoportokról](#traffic-filtering).

## <a name="security"></a>Biztonság

Hálózati biztonsági csoportokkal és hálózati virtuális berendezésekkel szűrheti a virtuális hálózatban lévő erőforrásokra irányuló hálózati forgalmat. Megadhatja, hogyan irányítja az Azure az alhálózatokból érkező forgalmat. Azt is korlátozhatja, hogy a szervezeten belül kik is dolgozhatnak a virtuális hálózatok erőforrásaival.

### <a name="traffic-filtering"></a>Forgalomszűrés

- Egy hálózati biztonsági csoporttal, a hálózati forgalmat vagy mindkettőt szűrő NVA használatával szűrheti a virtuális hálózat erőforrásai közötti hálózati forgalmat. NVA (például tűzfal) üzembe helyezéséhez a hálózati forgalom szűréséhez tekintse meg az [Azure piactéren](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?subcategories=appliances&page=1). NVA használatakor egyéni útvonalakat is létrehozhat, amelyek az alhálózatokból a NVA irányítják a forgalmat. További információ a [forgalom útválasztásáról](#traffic-routing).
- A hálózati biztonsági csoport több alapértelmezett biztonsági szabályt tartalmaz, amelyek engedélyezik vagy megtagadják az erőforrások felé irányuló vagy onnan érkező forgalmat. Egy hálózati biztonsági csoport társítható egy hálózati adapterhez, a hálózati adaptert tartalmazó alhálózathoz vagy mindkettőhöz. A biztonsági szabályok kezelésének egyszerűsítése érdekében javasoljuk, hogy rendeljen hozzá egy hálózati biztonsági csoportot az egyes alhálózatokhoz az alhálózaton belüli egyedi hálózati adapterek helyett, amikor csak lehetséges.
- Ha az alhálózaton belüli különböző virtuális gépeken eltérő biztonsági szabályokra van szükség, akkor a virtuális gép hálózati adapterét egy vagy több alkalmazás-biztonsági csoporthoz is hozzárendelheti. Egy biztonsági szabály megadhat egy alkalmazás biztonsági csoportot a forrásában, a célhelyén vagy mindkettőn. Ez a szabály csak az alkalmazás biztonsági csoportjának tagjain lévő hálózati adapterekre vonatkozik. További információ a [hálózati biztonsági csoportokról](./network-security-groups-overview.md) és az [alkalmazás biztonsági csoportjairól](./network-security-groups-overview.md#application-security-groups).
- Az Azure számos alapértelmezett biztonsági szabályt hoz létre az egyes hálózati biztonsági csoportokon belül. Egy alapértelmezett szabály lehetővé teszi a virtuális hálózat összes erőforrása közötti összes forgalom áramlását. Ennek a viselkedésnek a felülbírálásához használja a hálózati biztonsági csoportokat, az egyéni útválasztást, hogy átirányítsa a forgalmat egy NVA, vagy mindkettőt. Javasoljuk, hogy ismerkedjen meg az Azure [alapértelmezett biztonsági szabályaival](./network-security-groups-overview.md#default-security-rules) , és Ismerje meg, hogyan alkalmazza a rendszer a hálózati biztonsági csoportok szabályait az erőforrásokra.

A [NVA](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=%2Fazure%2Fvirtual-network%2Ftoc.json)használatával megtekintheti a peremhálózat (más néven DMZ) megvalósítására szolgáló minta-kialakításokat az Azure és az internet között.

### <a name="traffic-routing"></a>Forgalom útválasztása

Az Azure több alapértelmezett útvonalat hoz létre a kimenő forgalomhoz egy alhálózatból. Az Azure alapértelmezett útválasztását felülbírálhatja egy útválasztási táblázat létrehozásával és egy alhálózathoz társításával. Az Azure alapértelmezett útválasztási felülbírálásának gyakori okai a következők:
- Mivel azt szeretné, hogy az alhálózatok közötti adatforgalom egy NVA keresztül folyjon. Ha többet szeretne megtudni arról, hogyan lehet [útválasztási táblázatokat beállítani egy NVA keresztüli forgalom kényszerítéséhez](tutorial-create-route-table-portal.md).
- Mivel az interneten keresztüli forgalmat egy NVA vagy egy helyszíni Azure-beli VPN-átjárón keresztül szeretné kényszeríteni. Az internetes forgalom helyszíni ellenőrzéshez és naplózáshoz való kényszerítését gyakran kényszerített bújtatásnak nevezzük. További információ a [kényszerített bújtatás](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json)konfigurálásáról.

Ha egyéni útválasztást kell megvalósítani, javasoljuk, hogy ismerkedjen meg az [Azure-beli útválasztással](virtual-networks-udr-overview.md).

## <a name="connectivity"></a>Kapcsolat

Az Azure VPN Gateway használatával virtuális hálózatokat is összekapcsolhat más virtuális hálózatokkal vagy a helyszíni hálózattal.

### <a name="peering"></a>Társviszony-létesítés

A [virtuális hálózati](virtual-network-peering-overview.md)kapcsolatok használatakor a virtuális hálózatok lehetnek azonos vagy különböző támogatott Azure-régiókban. A virtuális hálózatok lehetnek azonos vagy különböző Azure-előfizetésekben (még a különböző Azure Active Directory bérlőhöz tartozó előfizetésekben is). A társítás létrehozása előtt javasoljuk, hogy ismerkedjen meg az összes egyenrangú [követelménysel és megkötéssel](virtual-network-manage-peering.md#requirements-and-constraints). Az azonos régióban található virtuális hálózatok erőforrásai közötti sávszélesség ugyanaz, mint ha az erőforrások ugyanabban a virtuális hálózatban voltak.

### <a name="vpn-gateway"></a>VPN-átjáró

Egy Azure- [VPN Gateway](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) használatával egy virtuális hálózatot csatlakoztathat a helyszíni hálózathoz [helyek közötti VPN-](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json)kapcsolaton keresztül, vagy az Azure [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-vel való dedikált kapcsolat használatával.

Összekapcsolhatja a társítást és a VPN-átjárót olyan [központi és küllős hálózatok](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)létrehozásához, ahol a küllős virtuális hálózatok egy hub virtuális hálózathoz csatlakoznak, és a hub például egy helyszíni hálózathoz csatlakozik.

### <a name="name-resolution"></a>Névfeloldás

Az egyik virtuális hálózat erőforrásai nem tudják feloldani az Azure [beépített DNS-t](virtual-networks-name-resolution-for-vms-and-role-instances.md)használó, egy társ virtuális hálózatban található erőforrások nevét. Egy társ virtuális hálózatban lévő nevek feloldásához [helyezzen üzembe egy saját DNS-kiszolgálót](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server), vagy használjon Azure DNS [privát tartományt](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). A virtuális hálózatok és a helyszíni hálózatok erőforrásai közötti nevek feloldásához a saját DNS-kiszolgálót is telepítenie kell.

## <a name="permissions"></a>Engedélyek

Az Azure [Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC)](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) használ az erőforrásokhoz. Az engedélyek egy [hatókörhöz](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#scope) vannak rendelve a következő hierarchiában: felügyeleti csoport, előfizetés, erőforráscsoport és egyedi erőforrás. A hierarchiával kapcsolatos további tudnivalókért tekintse meg az [erőforrások rendszerezését](../governance/management-groups/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)ismertető témakört. Az Azure-beli virtuális hálózatok és az azokhoz kapcsolódó képességek (például a társítás, a hálózati biztonsági csoportok, a szolgáltatási végpontok és az útválasztási táblák) kezeléséhez a szervezet tagjait hozzárendelheti a beépített [tulajdonosi](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#owner), [közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#contributor)vagy [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörökhöz, majd hozzárendelheti a szerepkört a megfelelő hatókörhöz. Ha konkrét engedélyeket szeretne rendelni a virtuális hálózati képességek egy részhalmazához, hozzon létre egy [Egyéni szerepkört](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , és rendelje hozzá a [virtuális hálózatok](manage-virtual-network.md#permissions), [alhálózatok és szolgáltatási végpontok](virtual-network-manage-subnet.md#permissions), [hálózati adapterek](virtual-network-network-interface.md#permissions) [, társítási,](virtual-network-manage-peering.md#permissions) [hálózati és alkalmazás-biztonsági csoportok](manage-network-security-group.md#permissions)számára szükséges konkrét engedélyeket, vagy [útválasztási táblákat](manage-route-table.md#permissions) a szerepkörhöz.

## <a name="policy"></a>Szabályzat

Azure Policy lehetővé teszi a szabályzat-definíciók létrehozását, hozzárendelését és kezelését. A szabályzat-definíciók különböző szabályokat alkalmaznak az erőforrásokon, így az erőforrások megfelelnek a szervezeti szabványoknak és a szolgáltatói szerződéseknek. Azure Policy futtatja az erőforrások értékelését, megvizsgálja azokat az erőforrásokat, amelyek nem felelnek meg az Ön által használt szabályzat-definícióknak. Meghatározhat és alkalmazhat például egy olyan házirendet, amely lehetővé teszi a virtuális hálózatok létrehozását egy adott erőforráscsoport vagy régió számára. Egy másik házirend megkövetelheti, hogy minden alhálózathoz hozzá legyen rendelve egy hálózati biztonsági csoport. Ezt követően a rendszer kiértékeli a szabályzatokat az erőforrások létrehozásakor és frissítésekor.

A házirendek a következő hierarchiára lesznek alkalmazva: felügyeleti csoport, előfizetés és erőforráscsoport. További információ a [Azure Policyről](../governance/policy/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , illetve a virtuális hálózatok [Azure Policy-definícióinak](./policy-reference.md)üzembe helyezéséről.

## <a name="next-steps"></a>Következő lépések

Ismerje meg a [virtuális hálózat](manage-virtual-network.md), az [alhálózat és a szolgáltatás végpontjának](virtual-network-manage-subnet.md), a [hálózati adapter](virtual-network-network-interface.md) [, a társítás, a](virtual-network-manage-peering.md)  [hálózat és az alkalmazás biztonsági csoportjának](manage-network-security-group.md), illetve az [útválasztási táblázatnak](manage-route-table.md)a feladatait, beállításait és lehetőségeit.
