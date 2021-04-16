---
title: Azure-hálózatkezelés architektúra dokumentációja
description: Ismerje meg az Azure hálózati szolgáltatásaihoz elérhető referenciaarchitektúra dokumentációját.
services: networking
author: KumudD
ms.service: virtual-network
ms.topic: article
ms.date: 03/30/2021
ms.author: kumud
ms.openlocfilehash: c98bdbb9fba2a6ba01e4ce590c36d57e68390f17
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484795"
---
# <a name="azure-networking-architecture-documentation"></a>Azure-hálózatkezelés architektúra dokumentációja

Ez a cikk az architektúra-útmutatókról nyújt információkat, amelyek segítségével megismerheti az Azure különböző, az alkalmazások felépítéséhez elérhető hálózati szolgáltatásait.

## <a name="networking-overview"></a>Hálózatok – áttekintés

Az alábbi táblázat olyan cikkeket tartalmaz, amelyek a virtuális adatközpontok és az Azure-beli küllős topológiák hálózatának áttekintését tartalmazzák.

|Cím |Leírás  |
|---------|---------|
|[Virtuális adatközpontok](/azure/architecture/vdc/networking-virtual-datacenter)   | Hálózati perspektívát biztosít az Azure-beli virtuális adatközpontok számára.       |
|[Küllős topológia](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)  |Áttekintést nyújt az Azure-beli küllős hálózati topológiáról, valamint az előfizetési korlátokkal és több központtal kapcsolatos információkkal.          |

## <a name="connect-to-azure-resources"></a>Csatlakozás Azure-erőforrásokhoz

Az alábbi táblázatban olyan cikkek Azure-hálózatkezelés, amelyek kapcsolatot biztosítanak az Azure-erőforrások, a helyszíni hálózat és az Azure-erőforrások közötti kapcsolatról, valamint az ágak és ágak közötti kapcsolatról az Azure-ban.

|Cím |Leírás  |
|---------|---------|
|[IP-címterek hozzáadása társviszonyban lévő virtuális hálózatokhoz](/azure/architecture/networking/prefixes/add-ip-space-peered-vnet)     | Olyan szkripteket biztosít, amelyek segítségével IP-címtereket adhat hozzá a virtuális társhálózathoz.        |
|[Különálló kiszolgálók csatlakoztatása az Azure Network Adapterrel](/azure/architecture/hybrid/azure-network-adapter)   | Bemutatja, hogyan csatlakoztathat helyszíni önálló kiszolgálót Microsoft Azure virtuális hálózatokhoz a virtuális hálózaton keresztül üzembe helyezett Azure hálózati adapter Windows Admin Center.        |
|[Választás a virtuális hálózatok közötti társviszony-létesítés és a VPN-átjárók között](/azure/architecture/reference-architectures/hybrid-networking/vnet-peering)   | Két módszert hasonlít össze az Azure-beli virtuális hálózatok csatlakoztatásához: virtuális hálózatok közötti társviszony-létesítés és VPN-átjárók.        |
|[Helyszíni hálózat csatlakoztatása az Azure-hoz](/azure/architecture/reference-architectures/hybrid-networking/)  | Összehasonlítja a helyszíni hálózat Azure-Virtual Network (VNet) csatlakoztatásának lehetőségeit. Mindegyik lehetőséghez elérhető egy referenciaarchitektúra.        |
|[SD-WAN kapcsolati architektúra Azure Virtual WAN](../../virtual-wan/sd-wan-connectivity-architecture.md)|A privát szoftverrel definiált WAN (SD-WAN) és a hálózati Azure Virtual WAN.|

## <a name="deploy-highly-available-applications"></a>Magas rendelkezésre álló alkalmazások üzembe helyezése

Az alábbi táblázat olyan cikkeket tartalmaz, amelyek ismertetik, hogyan helyezheti üzembe az alkalmazásait magas rendelkezésre állásra különböző Azure-hálózatkezelés használatával.

|Cím |Leírás  |
|---------|---------|
|[Többrépontos N szintű alkalmazás](/azure/architecture/reference-architectures/n-tier/multi-region-sql-server))  | Egy többrégió N szintű alkalmazást ismertet, amely Traffic Manager-t használ a bejövő kérések elsődleges régióba való útválasztásához, és ha ez a régió elérhetetlenné válik, a Traffic Manager a másodlagos régióba.      |
| [Több-bérlős SaaS az Azure-ban](https://docs.microsoft.com/azure/architecture/example-scenario/multi-saas/multitenant-saas)       |   Több-bérlős megoldást használ, amely magában foglalja a Front Door és Application Gateway.  Front Door a régiók és útvonalak közötti forgalom terheléselosztását, Application Gateway az alkalmazáson belüli forgalom terheléselosztását pedig az ügyfél üzleti igényeinek megfelelő különböző szolgáltatások között.  |
| [Magas rendelkezésre állásra és vészhelyreállításra készült többrétegű webalkalmazás ](https://docs.microsoft.com/azure/architecture/example-scenario/infrastructure/multi-tier-app-disaster-recovery)        |      Magas rendelkezésre állásra és vészhelyreállításra készült rugalmas többrétegű alkalmazásokat helyez üzembe. Ha az elsődleges régió elérhetetlenné válik, Traffic Manager másodlagos régió veszi át a feladat-vissza.  |
|[IaaS: Webalkalmazás relációs adatbázissal](/azure/architecture/high-availability/ref-arch-iaas-web-and-db)    |   Ismerteti, hogyan használhatók a több zónában lévő erőforrások egy magas rendelkezésre állású architektúra biztosítanak egy IaaS-webalkalmazás és egy SQL Server számára.     |
|[Hely valós idejű megosztása alacsony költségekkel járó, kiszolgáló nélküli Azure-szolgáltatások használatával](/azure/architecture/example-scenario/signalr/#azure-front-door)       |   A Azure Front Door magasabb rendelkezésre állást biztosít az alkalmazások számára, mint az egyetlen régióban való üzembe helyezés. Ha egy régió üzemkimaradása hatással van az elsődleges régióra, a Front Door szolgáltatással feladatátvételt hajthat végre a másodlagos régióba.      |
|[Magas rendelkezésre állású virtuális hálózati készülékek](/azure/architecture/reference-architectures/dmz/nva-ha)     | Bemutatja, hogyan helyezhet üzembe hálózati virtuális berendezéseket (NVA-k) magas rendelkezésre álláshoz az Azure-ban.        |
|[Többrépontos terheléselosztás Traffic Manager és Application Gateway](/azure/architecture/high-availability/reference-architecture-traffic-manager-application-gateway)     | Ismerteti, hogyan helyezhetők üzembe rugalmas többrétegű alkalmazások több Azure-régióban a rendelkezésre állás és a robusztus vészhelyreállítási infrastruktúra elérése érdekében.        |

## <a name="secure-your-network-resources"></a>A hálózati erőforrások biztonságossá teve

Az alábbi táblázat olyan cikkeket tartalmaz, amelyek ismertetik, hogyan védheti meg a hálózati erőforrásokat Azure-hálózatkezelés szolgáltatásokkal.

|Cím |Leírás  |
|---------|---------|
|[Ajánlott hálózati biztonsági eljárások](../../security/fundamentals/network-best-practices.md) |A hálózati biztonság növelése érdekében ajánlott Azure-eljárások gyűjteményét ismerteti.         |
[Útmutató az Azure Firewall architektúrájához](/azure/architecture/example-scenario/firewalls/) | Strukturált megközelítést biztosít a magas rendelkezésre álló tűzfalak tervezésére az Azure-ban külső virtuális berendezések használatával.        |
|[Biztonságos hibrid hálózat implementálása](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz)     | Egy DMZ-t ( más néven szegélyhálózatot) megvalósító architektúrát ismertet a helyszíni hálózat és egy Azure-beli virtuális hálózat között. Minden bejövő és kimenő forgalom áthalad a Azure Firewall.        |
|[A számítási feladatok biztonságossá tétele és szabályozása hálózati szintű szegmentálással](/azure/architecture/reference-architectures/hybrid-networking/network-level-segmentation) | A számítási feladatok Azure-ban való hálózatépítési szempontból való rendszerezéséhez használt három gyakori mintát ismerteti.   Mindegyik minta más típusú elkülönítést és kapcsolatot biztosít.      |
|[Tűzfal és Application Gateway virtuális hálózatokhoz](/azure/architecture/example-scenario/gateway/firewall-application-gateway) | Ismerteti az Azure Virtual Network biztonsági szolgáltatásokat Azure Firewall Azure Application Gateway, az egyes szolgáltatások használatát, valamint a mindkettőt kombináló hálózattervezési lehetőségeket.      |

## <a name="next-steps"></a>Következő lépések

További tudnivalók az [Azure Virtual Network.](../../virtual-network/virtual-networks-overview.md)
