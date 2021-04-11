---
title: Azure Virtual Network
description: Ismerkedjen meg az Azure Virtual Network-fogalmakkal és-szolgáltatásokkal, többek között a címterület, alhálózatok, régiók és előfizetések.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/03/2020
ms.author: kumud
ms.openlocfilehash: 927ff80a544ef8fbff348aeb8781f8a55c84e109
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061893"
---
# <a name="what-is-azure-virtual-network"></a>Mi az Azure Virtual Network?

Az Azure Virtual Network (VNet) az Azure-beli magánhálózat alapvető építőeleme. A VNet számos Azure-erőforrást, például Azure Virtual Machines (VM) tesz lehetővé az egymással, az internettel és a helyszíni hálózatokkal való biztonságos kommunikációhoz. A VNet hasonló egy hagyományos hálózathoz, amelyet a saját adatközpontban fog használni, de az Azure infrastruktúrájának, például a méretezhetőség, a rendelkezésre állás és az elkülönítés további előnyeit biztosítja.

## <a name="why-use-an-azure-virtual-network"></a>Miért érdemes Azure-beli virtuális hálózatot használni?
Az Azure Virtual Network lehetővé teszi az Azure-erőforrások számára, hogy biztonságosan kommunikáljanak egymással, az internettel és a helyszíni hálózatokkal. A virtuális hálózattal végrehajtható főbb forgatókönyvek közé tartozik az Azure-erőforrások internettel való kommunikációja, az Azure-erőforrások közötti kommunikáció, a helyszíni erőforrásokkal való kommunikáció, a hálózati forgalom szűrése, a hálózati forgalom irányítása és az Azure-szolgáltatásokkal való integráció.

### <a name="communicate-with-the-internet"></a>Kommunikáció az internettel

A VNet összes erőforrása alapértelmezés szerint képes kommunikálni az internet felé. Bejövő kommunikációt létesíthet egy erőforrással egy nyilvános IP-cím vagy Load Balancer hozzárendelésével. A kimenő kapcsolatok kezeléséhez is használhat nyilvános IP-címet vagy Load Balancert.  Az Azure kimenő kapcsolataira vonatkozó további információkat a [kimenő kapcsolatokat](../load-balancer/load-balancer-outbound-connections.md), a [nyilvános IP-címeket](virtual-network-public-ip-address.md) vagy a [Load Balancert](../load-balancer/load-balancer-overview.md) ismertető cikkekben talál.

>[!NOTE]
>Ha csak belső [Standard Load Balancert](../load-balancer/load-balancer-overview.md) használ, akkor a kimenő kapcsolat mindaddig nem lesz elérhető, amíg meg nem határozza, hogyan működjenek együtt a [kimenő kapcsolatok](../load-balancer/load-balancer-outbound-connections.md) egy példányszintű nyilvános IP-vel vagy egy nyilvános Load Balancerrel.

### <a name="communicate-between-azure-resources"></a>Azure-erőforrások közötti kommunikáció

Az Azure-erőforrások biztonságosan kommunikálnak egymással az alábbi módok egyikével:

- **Virtuális hálózaton keresztül**: Virtuális gépeket és számos egyéb típusú Azure-erőforrást is üzembe helyezhet egy virtuális hálózaton (pl. Azure App Service Environment-környezetek, Azure Kubernetes Service (AKS) és Azure Virtual Machine Scale Sets). A virtuális hálózatokon üzembe helyezhető Azure-erőforrások teljes listájáért lásd: [Virtuális hálózati szolgáltatás integrálása](virtual-network-for-azure-services.md).
- **Virtuális hálózati szolgáltatás végpontján keresztül**: kiterjesztheti a virtuális hálózat privát címterület-területét és a virtuális hálózat identitását az Azure-szolgáltatások erőforrásaira, például az Azure Storage-fiókokra és a Azure SQL Databaseokra közvetlen kapcsolaton keresztül. A szolgáltatásvégpontok segítségével biztosíthatja, hogy kritikus fontosságú Azure-szolgáltatási erőforrásai csak egy virtuális hálózaton legyenek elérhetőek. További információ: [A virtuális hálózati szolgáltatásvégpontok áttekintése](virtual-network-service-endpoints-overview.md).
- **A VNet**-társításon keresztül: a virtuális hálózatokat összekapcsolhatja egymással, így a virtuális hálózatban lévő erőforrások a virtuális hálózatok közötti kommunikációt is lehetővé teszik. Az összekacsolt virtuális hálózatok lehetnek azonos vagy eltérő Azure-régiókban. További információ: [Virtuális hálózatok közötti társviszony létesítése](virtual-network-peering-overview.md).

### <a name="communicate-with-on-premises-resources"></a>Kommunikáció helyszíni erőforrásokkal

Az alábbiak bármely kombinációjával csatlakoztathatja helyszíni számítógépeit és hálózatait egy virtuális hálózathoz:

- **Pont–hely típusú virtuális magánhálózat (VPN):** Egy virtuális hálózat és a hálózat egyetlen számítógépe között létesített kapcsolat. Minden számítógép kapcsolatát konfigurálni kell, amely csatlakozni szeretne egy virtuális hálózathoz. Ez a kapcsolattípus remek választás, ha csak most ismerkedik az Azure szolgáltatással, illetve a fejlesztők számára, mert a meglévő hálózatot csak kis mértékben vagy egyáltalán nem kell módosítani. A számítógép és a virtuális hálózat közötti kommunikáció egy titkosított csatornán, az interneten keresztül zajlik. További tudnivalókért lásd: [Pont–hely típusú VPN](../vpn-gateway/point-to-site-about.md?toc=%2fazure%2fvirtual-network%2ftoc.json#).
- **Helyek közötti VPN:** A helyszíni VPN-eszköz és egy virtuális hálózaton üzembe helyezett Azure VPN Gateway átjáró között létesített kapcsolat. Ez a kapcsolattípus lehetővé teszi az összes engedélyezett helyszíni erőforrás számára, hogy elérjék a virtuális hálózatot. A helyszíni VPN-eszköz és egy Azure VPN Gateway átjáró közötti kommunikáció egy titkosított csatornán, az interneten keresztül zajlik. További tudnivalókért lásd: [Helyek közötti VPN](../vpn-gateway/design.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti).
- **Azure ExpressRoute**: Saját hálózata és az Azure között egy ExpressRoute-partneren keresztül létesített kapcsolat. Ez a kapcsolat nem nyilvános. A forgalom nem az interneten keresztül halad át. További tudnivalókért lásd: [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="filter-network-traffic"></a>Hálózati forgalom szűrése

A hálózati forgalom alhálózatok közötti szűrése az alábbi lehetőségek egyikével vagy akár mindkettővel elvégezhető:

- **Hálózati biztonsági csoportok:** A hálózati biztonsági csoportok és az alkalmazás biztonsági csoportjai több bejövő és kimenő biztonsági szabályt is tartalmazhatnak, amelyekkel a forrás és a cél IP-címe, portja és protokollja alapján szűrheti a forgalmat és az erőforrásokat. További információ: [hálózati biztonsági csoportok](./network-security-groups-overview.md#network-security-groups) vagy [alkalmazás biztonsági csoportjai](./network-security-groups-overview.md#application-security-groups).
- **Hálózati virtuális berendezések:** A hálózati virtuális berendezések olyan virtuális gépek, amelyek egy hálózati funkciót látnak el, például tűzfal, WAN-optimalizáció vagy egyéb hálózati funkciók. A virtuális hálózatokban üzembe helyezhető hálózati virtuális berendezések listáját az [Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) találja.

### <a name="route-network-traffic"></a>Hálózati forgalom továbbítása

Az Azure alapértelmezés szerint elosztja a forgalmat az alhálózatok, a csatlakoztatott virtuális hálózatok, a helyszíni hálózatok és az internet között. Az Azure által létrehozott alapértelmezett útvonalak felülírásához valósítsa meg az alábbi lehetőségek egyikét, vagy akár mindkettőt:

- **Útválasztási táblázatok:** Létrehozhat egyéni útválasztási táblázatokat, amelyek útvonalai szabályozzák, hogy melyik alhálózat esetében hova érkezzen a forgalom. További tudnivalókért tekintse meg az [útválasztási táblázatokat](virtual-networks-udr-overview.md#user-defined) ismertető cikket.
- **Border Gateway Protocol (BGP-) útvonalak:** Ha a virtuális hálózatát VPN-átjáró vagy ExpressRoute-kapcsolat használatával csatlakoztatja a helyszíni hálózathoz, propagálhatja a helyszíni BGP-útvonalakat a virtuális hálózatára. Tudjon meg többet a BGP [Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#dynamic-route-exchange) szolgáltatással együtt való használatáról

### <a name="virtual-network-integration-for-azure-services"></a>Virtuális hálózat integrációja Azure-szolgáltatásokhoz

Az Azure-szolgáltatások Azure-beli virtuális hálózatba való integrálása lehetővé teszi a szolgáltatáshoz való privát hozzáférést a virtuális gépekről vagy a virtuális hálózatban lévő számítási erőforrásokról.
A virtuális hálózatban az alábbi beállításokkal integrálhatja az Azure-szolgáltatásokat:
- [A szolgáltatás dedikált példányainak](virtual-network-for-azure-services.md) üzembe helyezése virtuális hálózatban. A szolgáltatások ezután a virtuális hálózaton és a helyszíni hálózatokon is elérhetők.
- [Privát hivatkozás](../private-link/private-link-overview.md) használatával a szolgáltatás egy adott példányát a virtuális hálózatról és a helyszíni hálózatokról is elérheti.
- A szolgáltatást nyilvános végpontok használatával is elérheti, ha a virtuális hálózatot kiterjeszti a szolgáltatásra a szolgáltatási [végpontokon](virtual-network-service-endpoints-overview.md)keresztül. A szolgáltatási végpontok lehetővé teszik a szolgáltatási erőforrások védelmét a virtuális hálózattal.
 

## <a name="azure-vnet-limits"></a>Az Azure VNet korlátai

Bizonyos korlátozások vonatkoznak az üzembe helyezhető Azure-erőforrások számának körére. A legtöbb Azure-hálózat korlátja a maximális érték. Azonban [bizonyos hálózati korlátokat](../azure-portal/supportability/networking-quota-requests.md) a [VNet korlátai lapon](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)adhat meg. 

## <a name="pricing"></a>Díjszabás

Az Azure VNet díjmentesen vehető igénybe. A standard díjak olyan erőforrásokra vonatkoznak, mint a Virtual Machines (VM) és más termékek. További információ: a [VNet díjszabása](https://azure.microsoft.com/pricing/details/virtual-network/) és az Azure [díjszabásának kalkulátora](https://azure.microsoft.com/pricing/calculator/).

## <a name="next-steps"></a>Következő lépések
 - Ismerkedjen meg az [Azure Virtual Network-fogalmakkal és az ajánlott eljárásokkal](concepts-and-best-practices.md) .
 - A virtuális hálózatok használatának megkezdéséhez hozzon létre egyet, helyezzen üzembe rajta néhány virtuális gépet, majd létesítsen kommunikációt a virtuális gépek között. Ennek ismertetéséhez tekintse meg a [Virtuális hálózat létrehozása](quick-create-portal.md) című rövid útmutatót.
