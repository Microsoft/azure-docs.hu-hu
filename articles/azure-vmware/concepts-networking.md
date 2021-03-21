---
title: Fogalmak – hálózati kapcsolat
description: Ismerkedjen meg az Azure VMware megoldás legfontosabb szempontjaival és hálózati és kapcsolódási eseteivel.
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 4c964151c49e2fea56031dd24bacf4655753a18d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491809"
---
# <a name="azure-vmware-solution-networking-and-interconnectivity-concepts"></a>Azure VMware megoldás hálózatkezelési és összekapcsolási fogalmak

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Az Azure VMware-megoldás saját felhőben kétféleképpen lehet összekapcsolást létesíteni:

- Alapszintű, kizárólag az Azure [**-beli összekapcsolással**](#azure-virtual-network-interconnectivity) az Azure-ban csak egyetlen virtuális hálózattal kezelheti és használhatja a saját felhőjét. Ez a megvalósítás az Azure VMware megoldás-értékelések és olyan implementációk esetében ajánlott, amelyek nem igénylik a helyszíni környezetek elérését.

- A teljes helyszíni és a [**privát felhő közötti kapcsolat**](#on-premises-interconnectivity) kibővíti az alapszintű Azure-alapú megvalósítást, amely magában foglalja a helyszíni és az Azure VMware-megoldás privát felhők közötti összekapcsolást.
 
Ebben a cikkben azokat a főbb fogalmakat mutatjuk be, amelyek a hálózatkezelést és az összekapcsolást, beleértve a követelményeket és a korlátozásokat is. Ez a cikk ismerteti azokat az információkat, amelyeket tudnia kell a hálózatkezelés konfigurálásához az Azure VMware-megoldással való együttműködéshez.

## <a name="azure-vmware-solution-private-cloud-use-cases"></a>Azure VMware-megoldás saját Felhőbeli használati esetei

Az Azure VMware-megoldás privát felhők használati esetei a következők:
- Új VMware virtuális gép számítási feladatok a felhőben
- VIRTUÁLIS gépek számítási feladatainak kitakarítása a felhőbe (csak a helyszínen az Azure VMware megoldás esetében)
- VIRTUÁLIS gépek számítási feladatainak áttelepítése a felhőbe (csak a helyszíni rendszerről az Azure VMware megoldásra)
- Vész-helyreállítás (Azure VMware-megoldás Azure VMware-megoldásra vagy helyszíni Azure VMware-megoldás)
- Azure-szolgáltatások felhasználása

> [!TIP]
> Az Azure VMware megoldás szolgáltatás összes használati esete engedélyezve van a helyszíni és a saját felhőalapú kapcsolat között.

## <a name="azure-virtual-network-interconnectivity"></a>Azure-beli virtuális hálózatok összekapcsolása

Az Azure-beli virtuális hálózatot összekapcsolhatja az Azure VMware-megoldás saját Felhőbeli megvalósításával. Kezelheti az Azure VMware-megoldás saját felhőjét, felhasználhatja a saját Felhőbeli munkaterheléseket, és hozzáférhet más Azure-szolgáltatásokhoz.

Az alábbi ábra a privát Felhőbeli üzembe helyezés időpontjában kialakított hálózati összekapcsolást mutatja be. Az Azure-beli virtuális hálózatok és a privát felhő közötti logikai hálózatkezelést jeleníti meg. Ez a kapcsolat egy, az Azure VMware megoldás szolgáltatás részét képező háttér-ExpressRoute keresztül lett létrehozva. Az összekapcsolási funkció a következő elsődleges használati eseteket teljesíti:

- Bejövő hozzáférés a vCenter-kiszolgálóhoz és a NSX-T kezelőhöz, amely elérhető az Azure-előfizetésében lévő virtuális gépekről.
- Kimenő hozzáférés a saját Felhőbeli virtuális gépekről az Azure-szolgáltatásokhoz.
- A privát felhőben futó számítási feladatok bejövő hozzáférése.


:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Alapszintű virtuális hálózat privát Felhőbeli kapcsolathoz" border="false":::

## <a name="on-premises-interconnectivity"></a>Helyszíni kapcsolat

A teljes mértékben összekapcsolt forgatókönyvekben az Azure VMware-megoldást az Azure-beli virtuális hálózat (ok) ból és a helyszínen is elérheti. Ez a megvalósítás az előző szakaszban leírt alapszintű implementáció kiterjesztése. A helyszíni rendszerről az Azure VMware megoldás saját Azure-beli saját felhőbe való kapcsolódásához ExpressRoute áramkör szükséges.

Az alábbi ábra a helyszíni és a saját felhő közötti összekapcsolást mutatja be, amely a következő használati eseteket teszi lehetővé:

- A gyors/hideg vCenter vMotion a helyszíni és az Azure VMware megoldás között.
- Helyszíni Azure VMware-megoldás saját felhőalapú felügyeleti hozzáférés.

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Virtuális hálózat és helyszíni teljes Private Cloud connectivity" border="false":::

A privát felhőhöz való teljes összekapcsoláshoz engedélyeznie kell a ExpressRoute-Global Reach, majd a Azure Portal a Global Reach az engedélyezési kulcsot és a privát társ-létrehozási azonosítót kell kérnie. Az engedélyezési kulcs és a társ-azonosító használatával Global Reach lehet létrehozni az előfizetésben található ExpressRoute-áramkör és a saját felhő ExpressRoute áramköre között. A csatolást követően a két ExpressRoute áramkör a helyi környezetek közötti hálózati forgalmat átirányítja a saját felhőbe. Az eljárásokkal kapcsolatos további információkért tekintse meg a [ExpressRoute létrehozásához Global REACH a privát felhőbe való kapcsolódáshoz című oktatóanyagot](tutorial-expressroute-global-reach-private-cloud.md).

## <a name="limitations"></a>Korlátozások
[!INCLUDE [azure-vmware-solutions-limits](includes/azure-vmware-solutions-limits.md)]

## <a name="next-steps"></a>Következő lépések 

Most, hogy az Azure VMware megoldás hálózati és összekapcsolási fogalmait is érintette, érdemes megismernie az alábbiakat:

- [Azure VMware megoldás tárolási fogalmak](concepts-storage.md).
- Az [Azure VMware megoldás identitásával kapcsolatos fogalmak](concepts-identity.md).
- [Az Azure VMware megoldás erőforrásának engedélyezése](enable-azure-vmware-solution.md).

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md
[concepts-storage]: ./concepts-storage.md
