---
title: Fogalmak – Hálózati összekapcsolás
description: Megismeri a hálózatépítés és az összekapcsolás kulcsfontosságú aspektusait és a használatának esetét a Azure VMware Solution.
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: a6c86c0a9eb8e07a91a094ddf5d6fb77b0eddf67
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871686"
---
# <a name="azure-vmware-solution-networking-and-interconnectivity-concepts"></a>Azure VMware Solution és összekapcsolási fogalmak

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

A magánfelhőben kétféleképpen lehet Azure VMware Solution kapcsolatot:

- [**A csak Azure-beli**](#azure-virtual-network-interconnectivity) alapszintű összekapcsolás lehetővé teszi a magánfelhő kezelését és használatát egyetlen virtuális hálózattal az Azure-ban. Ez a megvalósítás olyan Azure VMware Solution vagy implementációkhoz, amelyekhez nincs szükség helyszíni környezetből való hozzáférésre.

- [**A teljes körű helyszíni és**](#on-premises-interconnectivity) a magánfelhő közötti összekapcsolás kibővíti a csak Azure-beli alapszintű implementációt, hogy a helyszíni és a Azure VMware Solution magánfelhők közötti kapcsolatot is magában foglalja.
 
Ebben a cikkben a hálózat és az összekapcsolás létrehozására vonatkozó fő fogalmakat, többek között a követelményeket és a korlátozásokat fogjuk átfésülni. Ez a cikk a hálózat konfigurálásához szükséges információkat biztosítja a Azure VMware Solution.

## <a name="azure-vmware-solution-private-cloud-use-cases"></a>Azure VMware Solution magánfelhők használatának esete

A magánfelhők Azure VMware Solution a következő esetekben használhatók:
- Új VMware virtuálisgép-számítási feladatok a felhőben
- Virtuális gépek számítási feladatainak felhőbe való átlése (csak a Azure VMware Solution)
- Virtuális gépek számítási feladatainak migrálása a felhőbe (csak a Azure VMware Solution)
- Vészhelyreállítás (Azure VMware Solution a Azure VMware Solution vagy a helyszínről a Azure VMware Solution)
- Az Azure-szolgáltatások használatának

> [!TIP]
> A helyszíni és Azure VMware Solution szolgáltatás minden esetben engedélyezve van a helyszíni és a magánfelhők között.

## <a name="azure-virtual-network-interconnectivity"></a>Azure-beli virtuális hálózatok összekapcsolása

Az Azure-beli virtuális hálózatot összekapcsolhatja a Azure VMware Solution implementációval. Kezelheti a magánfelhő Azure VMware Solution, használhatja a magánfelhő számítási feladatait, és hozzáférhet más Azure-szolgáltatásokhoz.

Az alábbi ábrán a magánfelhő üzembe helyezésekor létrehozott alapszintű hálózati kapcsolat látható. Az Azure-beli virtuális hálózat és a magánfelhő közötti logikai hálózatokat jeleníti meg. Ez a kapcsolat egy olyan háttér-ExpressRoute-on keresztül jön létre, amely a Azure VMware Solution része. Az összekapcsolás a következő elsődleges eseteket teljesíti:

- Bejövő hozzáférés a vCenter-kiszolgálóhoz és az NSX-T kezelőhöz, amely elérhető az Azure-előfizetésben található virtuális gépekről.
- Kimenő hozzáférés a magánfelhőben lévő virtuális gépekről az Azure-szolgáltatásokhoz.
- A magánfelhőben futó számítási feladatok bejövő elérése.


:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Alapszintű virtuális hálózat és magánfelhő összekapcsolhatósága" border="false":::

## <a name="on-premises-interconnectivity"></a>Helyszíni összekapcsolás

A teljes mértékben összekapcsolt forgatókönyvben a virtuális Azure VMware Solution azure-beli virtuális hálózat(ak)ból vagy a helyszíni környezetből is elérheti. Ez az implementáció az előző szakaszban ismertetett alapszintű implementáció kiterjesztése. A helyszíni hálózatról az Azure-ban Azure VMware Solution ExpressRoute-kapcsolati kapcsolatra van szükség.

Az alábbi ábrán a helyszíni és a magánfelhő közötti kapcsolat látható, amely a következő eseteket teszi lehetővé:

- Hot/Cold vCenter vMotion a helyszíni és a Azure VMware Solution.
- Helyszíni és Azure VMware Solution felhőalapú felügyeleti hozzáféréshez.

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Virtuális hálózat és helyszíni teljes magánfelhő-kapcsolat" border="false":::

A magánfelhőhöz való teljes körű kapcsolat érdekében engedélyeznie kell az ExpressRoute Global Reach-t, majd le kell kérnie egy engedélyezési kulcsot és egy privát társviszony Global Reach azonosítót a Azure Portal. Az engedélyezési kulcs és a társviszony-létesítés azonosítója Global Reach az előfizetésben lévő ExpressRoute-kapcsolathálózat és a magánfelhőHöz használt ExpressRoute-kapcsolatkör között. A összekapcsolás után a két ExpressRoute-kapcsolathálózat a helyszíni környezetek közötti hálózati forgalmat a magánfelhőbe irányítja. Az eljárásokkal kapcsolatos további információkért tekintse meg a magánfelhővel Global Reach [ExpressRoute-társviszony-létesítés létrehozását ismertető oktatóanyagot.](tutorial-expressroute-global-reach-private-cloud.md)

## <a name="limitations"></a>Korlátozások
[!INCLUDE [azure-vmware-solutions-limits](includes/azure-vmware-solutions-limits.md)]

## <a name="next-steps"></a>Következő lépések 

Most, hogy megismerte a Azure VMware Solution és az összekapcsolási fogalmakat, a következővel ismerkedhet meg:

- [Azure VMware Solution tárolási fogalmak](concepts-storage.md)
- [Azure VMware Solution identitással kapcsolatos fogalmak](concepts-identity.md)
- [Az erőforrás Azure VMware Solution engedélyezése](enable-azure-vmware-solution.md)

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md
[concepts-storage]: ./concepts-storage.md
