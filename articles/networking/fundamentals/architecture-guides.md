---
title: Az Azure hálózatkezelési architektúrájának dokumentációja
description: Ismerje meg az Azure hálózati szolgáltatásaihoz elérhető hivatkozási architektúra dokumentációját.
services: networking
author: KumudD
ms.service: virtual-network
ms.topic: article
ms.date: 03/30/2021
ms.author: kumud
ms.openlocfilehash: 9b608312d66e6a3e7455c4577ea4644b33e4e82e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080240"
---
# <a name="azure-networking-architecture-documentation"></a>Az Azure hálózatkezelési architektúrájának dokumentációja

Ez a cikk olyan architektúrás útmutatókról nyújt tájékoztatást, amelyek segítségével megismerheti az Azure-ban elérhető különböző hálózati szolgáltatásokat az alkalmazások létrehozásához.

## <a name="networking-overview"></a>Hálózatok – áttekintés

Az alábbi táblázat a virtuális adatközpontok és az Azure-beli sugaras topológiák hálózatkezelési áttekintését tartalmazza.

|Cím |Leírás  |
|---------|---------|
|[Virtuális adatközpontok](/azure/architecture/vdc/networking-virtual-datacenter)   | Hálózati perspektívát biztosít a virtuális adatközpontok számára az Azure-ban.       |
|[Küllős topológia](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)  |Áttekintést nyújt az Azure-beli központról és küllős hálózati topológiáról, valamint az előfizetési korlátokkal és a több hubhoz vonatkozó információkkal.          |

## <a name="connect-to-azure-resources"></a>Kapcsolódás Azure-erőforrásokhoz

Az alábbi táblázat az Azure hálózati szolgáltatásaival kapcsolatos cikkeket tartalmaz, amelyek kapcsolatot biztosítanak az Azure-erőforrások, a helyszíni hálózat és az Azure-erőforrások közötti kapcsolat, valamint az Azure-beli fiókirodák közötti kapcsolat.

|Cím |Leírás  |
|---------|---------|
|[IP-címtartomány hozzáadása a virtuális hálózatokhoz](/azure/architecture/networking/prefixes/add-ip-space-peered-vnet)     | Olyan parancsfájlokat biztosít, amelyek segítenek az IP-címtartomány hozzáadásában a társ virtuális hálózatokhoz.        |
|[Különálló kiszolgálók csatlakoztatása az Azure Network Adapterrel](/azure/architecture/hybrid/azure-network-adapter)   | Azt mutatja be, hogyan csatlakoztatható egy helyszíni önálló kiszolgáló Microsoft Azure virtuális hálózatokhoz a Windows felügyeleti központon keresztül üzembe helyezett Azure hálózati adapter használatával.        |
|[Választás a virtuális hálózatok közötti társviszony-létesítés és a VPN-átjárók között](/azure/architecture/reference-architectures/hybrid-networking/vnet-peering)   | Összehasonlítja a virtuális hálózatok az Azure-ban való összekapcsolásának két módját: virtuális hálózati társítási és VPN-átjárók.        |
|[Helyszíni hálózat csatlakoztatása az Azure-hoz](/azure/architecture/reference-architectures/hybrid-networking/)  | Összehasonlítja a helyszíni hálózat Azure Virtual Networkhoz (VNet) való csatlakoztatásának beállításait. Mindegyik lehetőséghez elérhető egy referenciaarchitektúra.        |
|[SD-WAN kapcsolati architektúra az Azure Virtual WAN-ral](../../virtual-wan/sd-wan-connectivity-architecture.md)|Ismerteti a privát szoftveres WAN (SD-WAN) Azure Virtual WAN-vel való összekapcsolásának különböző csatlakozási lehetőségeit.|

## <a name="deploy-highly-available-applications"></a>Magasan elérhető alkalmazások telepítése

Az alábbi táblázat azokat a cikkeket ismerteti, amelyekből megtudhatja, hogyan helyezheti üzembe az alkalmazásait magas rendelkezésre álláshoz az Azure hálózati szolgáltatások kombinációjának használatával.

|Cím |Leírás  |
|---------|---------|
|[Többrégiós N szintű alkalmazás](/azure/architecture/reference-architectures/n-tier/multi-region-sql-server))  | Egy többrégiós N szintű alkalmazást ismertet, amely a Traffic Manager használatával irányítja át a beérkező kéréseket egy elsődleges régióba, és ha az adott régió elérhetetlenné válik, Traffic Manager feladatátvételt hajt végre a másodlagos régióba.      |
| [Több-bérlős SaaS az Azure-ban](https://docs.microsoft.com/azure/architecture/example-scenario/multi-saas/multitenant-saas)       |   A egy több-bérlős megoldást használ, amely magában foglalja a bejárati ajtó és a Application Gateway kombinációját.  A bevezető ajtó segíti a régiók közötti adatforgalom terheléselosztását, és Application Gateway útvonalakat és terheléselosztási forgalmat az alkalmazáson belül az ügyfél üzleti igényeinek megfelelő különböző szolgáltatásokhoz.  |
| [Magas rendelkezésre állású és vész-helyreállítási többrétegű webes alkalmazás ](https://docs.microsoft.com/azure/architecture/example-scenario/infrastructure/multi-tier-app-disaster-recovery)        |      A magas rendelkezésre álláshoz és a vész-helyreállításhoz készült rugalmas többrétegű alkalmazásokat helyez üzembe. Ha az elsődleges régió elérhetetlenné válik, Traffic Manager feladatátvételt hajt végre a másodlagos régióba.  |
|[IaaS: webalkalmazás a kapcsolati adatbázissal](/azure/architecture/high-availability/ref-arch-iaas-web-and-db)    |   Ismerteti, hogyan használhatók több zónába tartozó erőforrások, hogy magas rendelkezésre állású architektúrát biztosítson a szolgáltatásként nyújtott infrastruktúra (IaaS) webalkalmazás és SQL Server-adatbázis üzemeltetéséhez.     |
|[Hely valós idejű megosztása alacsony költségekkel járó, kiszolgáló nélküli Azure-szolgáltatások használatával](/azure/architecture/example-scenario/signalr/#azure-front-door)       |   Az Azure bejárati ajtót használva magasabb rendelkezésre állást biztosít az alkalmazásokhoz, mint az egyetlen régióban történő üzembe helyezés. Ha egy régió üzemkimaradása hatással van az elsődleges régióra, a Front Door szolgáltatással feladatátvételt hajthat végre a másodlagos régióba.      |
|[Magas rendelkezésre állású virtuális hálózati készülékek](/azure/architecture/reference-architectures/dmz/nva-ha)     | Bemutatja, hogyan helyezhet üzembe egy hálózati virtuális berendezést (NVA) a magas rendelkezésre állás érdekében az Azure-ban.        |

## <a name="secure-your-network-resources"></a>A hálózati erőforrások biztonságossá tétele

Az alábbi táblázat azokat a cikkeket ismerteti, amelyek leírják, hogyan védik a hálózati erőforrásokat az Azure Networking Services használatával.

|Cím |Leírás  |
|---------|---------|
|[Ajánlott hálózati biztonsági eljárások](../../security/fundamentals/network-best-practices.md) |Az Azure-beli ajánlott eljárások gyűjteményét tárgyalja a hálózati biztonság növelése érdekében.         |
[Útmutató az Azure Firewall architektúrájához](/azure/architecture/example-scenario/firewalls/) | Strukturált megközelítést biztosít az Azure-ban található, külső gyártótól származó virtuális készülékek használatával történő, magasan elérhető tűzfalak tervezéséhez.        |
|[Biztonságos hibrid hálózat implementálása](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz)     | Leírja azt az architektúrát, amely a helyszíni hálózat és egy Azure-beli virtuális hálózat közötti DMZ-t, más néven peremhálózati hálózatot implementál. Az összes bejövő és kimenő forgalom a Azure Firewallon halad át.        |
|[A számítási feladatok biztonságossá tétele és szabályozása hálózati szintű szegmentálással](/azure/architecture/reference-architectures/hybrid-networking/network-level-segmentation) | Ismerteti az Azure-beli számítási feladatok hálózati perspektívában való rendszerezésének három gyakori mintáját.   Ezek a minták különböző típusú elkülönítést és kapcsolatot biztosítanak.      |
|[Tűzfal és Application Gateway virtuális hálózatokhoz](/azure/architecture/example-scenario/gateway/firewall-application-gateway) | Leírja az Azure Virtual Network biztonsági szolgáltatásait, például a Azure Firewallt és az Azure Application Gateway, hogy mikor kell használni az egyes szolgáltatásokat és a mindkettőt ötvöző hálózati kialakítási lehetőségeket.      |

## <a name="next-steps"></a>Következő lépések

Az [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md)megismerése.
