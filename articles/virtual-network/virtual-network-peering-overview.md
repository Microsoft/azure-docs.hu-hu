---
title: Azure-beli virtuális hálózati társviszony
titlesuffix: Azure Virtual Network
description: Ismerkedjen meg az Azure-beli virtuális hálózatokkal, beleértve az Azure-Virtual Network hálózatok összekapcsolását.
services: virtual-network
documentationcenter: na
author: altambaw
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2019
ms.author: kumud
ms.openlocfilehash: 1356c896abff8c525df283251240d027123405fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778289"
---
# <a name="virtual-network-peering"></a>Társviszony létesítése virtuális hálózatok között

A virtuális hálózatok közötti kapcsolat lehetővé teszi két vagy több [virtuális hálózat](virtual-networks-overview.md) zökkenőmentes összekapcsolását az Azure-ban. A virtuális hálózatok a kapcsolati céloknak megfelelően jelennek meg. A virtuális gépek közötti forgalom a Microsoft gerinc-infrastruktúrát használja. Az azonos hálózatban lévő virtuális gépek közötti forgalomhoz hasonlóan a forgalmat csak a Microsoft *magánhálózati* hálózata irányítja át.

Az Azure a következő típusú társításokat támogatja:

* **Virtuális hálózati** társítás: virtuális hálózatok összekötése ugyanazon az Azure-régión belül.
* **Globális virtuális hálózati** társítás: virtuális hálózatok összekapcsolása az Azure-régiók között.

A virtuális társhálózatok akár helyi, akár globális létesítésének előnyei:

* Kis késésű, nagy sávszélességű kapcsolat jön létre eltérő virtuális hálózatokba tartozó erőforrások között.
* Az egyik virtuális hálózat erőforrásai egy másik virtuális hálózat erőforrásaival való kommunikációra képesek.
* Az Azure-előfizetések, Azure Active Directory-bérlők, üzembe helyezési modellek és Azure-régiók közötti adatátvitel lehetősége a virtuális hálózatok között.
* A Azure Resource Manageron keresztül létrehozott virtuális hálózatok társ-létrehozási képessége.
* A Resource Manager használatával létrehozott virtuális hálózatokat a klasszikus üzemi modellen keresztül létrehozhatja. Az Azure üzembehelyezési modellekkel kapcsolatos további információkért lásd: [Az Azure üzemi modelljeinek megismerése](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Ez nem okoz leállást egyik virtuális hálózat erőforrásaiban sem a társításkor és azt követően sem.

A társított virtuális hálózatok közti hálózati adatforgalom nem nyilvános. A virtuális hálózatok közötti forgalom a Microsoft gerinchálózatán belül marad. A virtuális hálózatok közti forgalomhoz nincs szükség nyilvános internetre, átjárókra vagy titkosításra.

## <a name="connectivity"></a>Kapcsolatok

A egyenrangú virtuális hálózatok esetében bármelyik virtuális hálózat erőforrásai közvetlenül kapcsolódhatnak a virtuális hálózatban lévő erőforrásokhoz.

Az azonos régióban lévő virtuális társhálózaton belüli virtuális gépek közötti hálózati késés megegyezik az egyetlen virtuális hálózaton belüli hálózati késéssel. A hálózat átbocsátóképessége attól függ, hogy milyen, a virtuális gépek méretével arányos sávszélesség van engedélyezve. A társviszonyon belül más korlátozás nem vonatkozik a sávszélességre.

A virtuális társhálózatokon belüli virtuális gépek közötti forgalom közvetlenül a Microsoft gerincinfrastruktúráján halad át, nem pedig átjárón vagy a nyilvános interneten.

Bármelyik virtuális hálózaton hálózati biztonsági csoportokat is alkalmazhat, hogy letiltsa a hozzáférést más virtuális hálózatokhoz vagy alhálózatokhoz.
A virtuális hálózati kapcsolatok konfigurálásakor nyissa meg vagy zárjunk be a hálózati biztonsági csoport szabályait a virtuális hálózatok között. Ha megnyitja a teljes kapcsolatot a kihelyezett virtuális hálózatok között, akkor a hálózati biztonsági csoportok alkalmazásával blokkolhatja vagy megtagadhatja a megadott hozzáférést. A teljes kapcsolat az alapértelmezett beállítás. A hálózati biztonsági csoportokkal kapcsolatos további tudnivalókért tekintse meg a [biztonsági csoportok](./network-security-groups-overview.md)című témakört.

## <a name="service-chaining"></a>Szolgáltatásláncolás

A szolgáltatás-láncolás lehetővé teszi, hogy az egyik virtuális hálózatról egy virtuális készülékre vagy átjáróra irányítsa át a forgalmat a felhasználó által megadott útvonalakon keresztül.

A szolgáltatások láncolásának engedélyezéséhez olyan felhasználó által megadott útvonalakat konfigurálhat, amelyek a *következő ugrás* IP-címének megfelelően a virtuális gépekre mutatnak. A felhasználó által megadott útvonalak a virtuális hálózati átjáróra is rámutatnak a szolgáltatások láncolásának engedélyezéséhez.

Központi és küllős hálózatokat is telepíthet, ahol a hub virtuális hálózat infrastruktúra-összetevőket, például hálózati virtuális berendezést vagy VPN-átjárót üzemeltet. Az ágakon lévő virtuális hálózatok társhálózatai lehetnek a középponti hálózatnak. A forgalom a hub virtuális hálózatán keresztül áramlik a hálózati virtuális berendezéseken vagy a VPN-átjárón keresztül.

A virtuális hálózatok közötti társviszony-létesítéssel a felhasználó által definiált útvonalon egy virtuális társhálózat egyik virtuális gépének vagy egy VPN-átjárónak az IP-címe beállítható a következő ugrás IP-címeként. A virtuális hálózatok között nem lehet átirányítani egy olyan felhasználó által megadott útvonallal, amely a következő ugrási típusként ad meg egy Azure ExpressRoute-átjárót. A felhasználó által megadott útvonalakkal kapcsolatos további információkért lásd a [felhasználó által megadott útvonalak áttekintését](virtual-networks-udr-overview.md#user-defined). A sugaras hálózati topológia létrehozásával kapcsolatos információkért lásd: [sugaras hálózati topológia az Azure-ban](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="gateways-and-on-premises-connectivity"></a>Átjárók és kapcsolat helyszíni rendszerekkel

Minden virtuális hálózatnak, beleértve a társ virtuális hálózatot, saját átjáróval is rendelkezhet. A virtuális hálózatok használhatják az átjárót a helyszíni hálózathoz való kapcsolódáshoz. Az átjárók használatával is konfigurálhat [virtuális hálózat – virtuális hálózati kapcsolatokat](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , még a egyenrangú virtuális hálózatok esetében is.

Ha mindkét lehetőséget konfigurálja a virtuális hálózat összekapcsolására, a virtuális hálózatok közötti forgalom a társítási konfiguráción keresztül folyik. A forgalom az Azure-gerincet használja.

Azt is megteheti, hogy az átjárót a társ virtuális hálózatban is konfigurálhatja egy helyszíni hálózatra irányuló továbbítási pontként. Ebben az esetben a távoli átjárót használó virtuális hálózatnak nem lehet saját átjárója. Egy virtuális hálózatnak csak egy átjárója van. Az átjáró vagy egy helyi vagy távoli átjáró a társ virtuális hálózaton, az alábbi ábrán látható módon:

![virtuális társhálózatok közötti átvitel](./media/virtual-networks-peering-overview/local-or-remote-gateway-in-peered-virual-network.png)

A virtuális hálózatok és a globális virtuális hálózatok egymáshoz való továbbítása is támogatja az átjárót.

A különböző üzembe helyezési modelleken keresztül létrehozott virtuális hálózatok közötti átjáró átvitele támogatott. Az átjárónak a Resource Manager-modellben lévő virtuális hálózatban kell lennie. További információ az átjárók adatátvitelre való használatáról: [VPN-átjáró konfigurálása adatátvitelhez virtuális hálózatok közötti társviszony-létesítésben](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Ha egyetlen Azure ExpressRoute-kapcsolattal rendelkező társ virtuális hálózattal rendelkezik, a közöttük zajló forgalom a társítási kapcsolaton halad át. Ez a forgalom az Azure gerinc hálózatot használja. Az egyes virtuális hálózatok helyi átjárói ennek ellenére használhatók arra, hogy kapcsolatot létesítsenek a helyszíni kapcsolatcsoporttal. Ellenkező esetben használhat megosztott átjárót, és konfigurálhatja az átvitelt a helyszíni kapcsolathoz.

## <a name="troubleshoot"></a>Hibaelhárítás

Annak ellenőrzéséhez, hogy a virtuális hálózatok egyenrangúak-e, ellenőrizheti a hatályos útvonalakat. A virtuális hálózat bármely alhálózatán lévő hálózati adapter útvonalait vizsgálja meg. Ha létezik a virtuális hálózatok közötti társviszony, a virtuális hálózat összes alhálózata *Virtuális hálózatok közötti társviszony* következő ugrási típusú útvonalakkal rendelkezik minden virtuális társhálózat minden címterében. További információ: [a virtuális gép útválasztási problémáinak diagnosztizálása](diagnose-network-routing-problem.md).

Az Azure Network Watcher használatával a virtuális gépekhez való kapcsolódást is elháríthatja egy társ virtuális hálózaton. A kapcsolat ellenőrzése lehetőséggel megtekintheti, hogyan irányítja a rendszer a forgalmat a forrás virtuális gép hálózati adapteréről a célként megadott virtuális gép hálózati adapteréhez. További információkért lásd: [Az Azure Network Watcher kapcsolatok hibáinak megoldása a Azure Portal használatával](../network-watcher/network-watcher-connectivity-portal.md#check-connectivity-to-a-virtual-machine).

Kipróbálhatja a [virtuális hálózati problémák elhárításával kapcsolatos problémákat](virtual-network-troubleshoot-peering-issues.md)is.

## <a name="constraints-for-peered-virtual-networks"></a>Megkötések a társ virtuális hálózatokhoz<a name="requirements-and-constraints"></a>

A következő megkötések csak akkor érvényesek, ha a virtuális hálózatok globálisan vannak kiképezve:

* Az egyik virtuális hálózat erőforrásai nem tudnak kommunikálni egy alapszintű belső Load Balancer (ILB) előtér-IP-címével egy globálisan összetartozó virtuális hálózaton.
* Az alapszintű Load balancert használó szolgáltatások nem működnek a globális virtuális hálózati kapcsolaton keresztül. További információ: [Mik a globális VNet-társítással és-terheléselosztóokkal kapcsolatos korlátozások?](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers).

További információ: [követelmények és megkötések](virtual-network-manage-peering.md#requirements-and-constraints). Ha többet szeretne megtudni a támogatott számú társáról, tekintse meg a [hálózati korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)című témakört.

## <a name="permissions"></a>Engedélyek

A virtuális hálózati társítások létrehozásához szükséges engedélyekkel kapcsolatos további információkért lásd: [engedélyek](virtual-network-manage-peering.md#permissions).

## <a name="pricing"></a>Díjszabás

A virtuális hálózatokat összekapcsoló kapcsolatot használó bejövő és kimenő forgalom névleges díja. További információ: [Virtual Network díjszabása](https://azure.microsoft.com/pricing/details/virtual-network).

Az átjáró-átvitel egy olyan társítási tulajdonság, amely lehetővé teszi, hogy a virtuális hálózatok a VPN/ExpressRoute átjárót használják egy társ virtuális hálózatban. Az átjárók közötti átvitel mind a létesítmények, mind a hálózat és a hálózat közötti kapcsolat esetében működik. Az átjáróra (bejövő vagy kimenő forgalomra) irányuló forgalmat a virtuális hálózatban található, a küllős VNet (vagy nem átjáró VNet) tartozó virtuális hálózati társítási díjak jelentik. További információkért VPN Gateway tekintse meg a ExpressRoute-átjáró díjainak [díjszabását](https://azure.microsoft.com/pricing/details/vpn-gateway/) a VPN Gateway díjaival és a ExpressRoute-átjáró díjszabásával kapcsolatban.

>[!NOTE]
> A dokumentum egy korábbi verziója azt állapította meg, hogy a virtuális hálózati társítási díjak nem vonatkoznak a küllős VNet (vagy nem átjáró VNet) az átjáró-Átvitelsel. Mostantól a díjszabási oldalon a pontos díjszabást tükrözi.

## <a name="next-steps"></a>Következő lépések

* Két virtuális hálózat közötti társítást hozhat létre. A hálózatok ugyanahhoz az előfizetéshez, különböző üzembe helyezési modellekhez tartozhatnak ugyanabban az előfizetésben, vagy eltérő előfizetésekben is. Végezzen el egy oktatóanyagot a következő forgatókönyvek egyikéhez:

    |Azure üzembehelyezési modell             | Előfizetés  |
    |---------                          |---------|
    |Mindkét Resource Manager              |[Ugyanaz](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Különböző](create-peering-different-subscriptions.md)|
    |Egy Resource Manager, egy klasszikus  |[Ugyanaz](create-peering-different-deployment-models.md)|
    |                                   |[Különböző](create-peering-different-deployment-models-subscriptions.md)|

* A sugaras hálózati topológia létrehozásával kapcsolatos információkért lásd: [sugaras hálózati topológia az Azure-ban](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Az összes virtuális hálózati társítási beállítással kapcsolatos további tudnivalókért lásd: [virtuális hálózati társak létrehozása, módosítása vagy törlése](virtual-network-manage-peering.md).
* A gyakori virtuális hálózati és a globális virtuális hálózati kérdéseket érintő kérdésekre adott válaszokért lásd: [VNet peering](virtual-networks-faq.md#vnet-peering).
