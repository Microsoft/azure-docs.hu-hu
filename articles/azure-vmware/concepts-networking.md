---
title: Fogalmak – hálózati kapcsolat
description: Ismerkedjen meg az Azure VMware megoldás legfontosabb szempontjaival és hálózati és kapcsolódási eseteivel.
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 4ffcdd8ea42df127ee1480927f4fdf2eb8f137b8
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91316893"
---
# <a name="azure-vmware-solution-networking-and-interconnectivity-concepts"></a>Azure VMware megoldás hálózatkezelési és összekapcsolási fogalmak

[!INCLUDE [avs-networking-description](includes/avs-networking-description.md)]

Az összekapcsolhatóság hasznos perspektívája az Azure VMware-megoldás saját felhőalapú megvalósításának két típusa:

1. Alapszintű, kizárólag az Azure [**-beli összekapcsolással**](#azure-virtual-network-interconnectivity) az Azure-ban csak egyetlen virtuális hálózattal kezelheti és használhatja a saját felhőjét. Ez a megvalósítás az Azure VMware megoldás-értékelések és olyan implementációk esetében ajánlott, amelyek nem igénylik a helyszíni környezetek elérését.

1. A teljes helyszíni és a [**privát felhő közötti kapcsolat**](#on-premises-interconnectivity) kibővíti az alapszintű Azure-alapú megvalósítást, amely magában foglalja a helyszíni és az Azure VMware-megoldás privát felhők közötti összekapcsolást.
 
Ebben a cikkben a Hálózatkezelés és az összekapcsolhatóság, valamint a követelmények és korlátozások kialakításával kapcsolatos főbb fogalmakat mutatjuk be. Emellett további információkhoz is tájékozódhat az Azure VMware megoldás saját felhőalapú összekapcsolási implementációjának két típusáról. Ebből a cikkből megtudhatja, hogy milyen információkra van szüksége ahhoz, hogy a hálózatkezelést megfelelően konfigurálja az Azure VMware-megoldással.

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

A virtuális hálózatban a saját felhő megvalósításához kezelheti az Azure VMware-megoldás saját felhőjét, felhasználhatja a saját Felhőbeli munkaterheléseket, és hozzáférhet az Azure-szolgáltatásokhoz a ExpressRoute-kapcsolaton keresztül. 

Az alábbi ábra a privát Felhőbeli üzembe helyezés időpontjában kialakított hálózati összekapcsolást mutatja be. Az Azure-beli virtuális hálózatok és a privát felhő közötti logikai, ExpressRoute-alapú hálózatkezelést jeleníti meg. Az összekapcsolási funkció három elsődleges használati esetet teljesít:
* Bejövő hozzáférés a vCenter-kiszolgálóhoz és a NSX-T kezelőhöz, amely elérhető az Azure-előfizetésében lévő virtuális gépekről, és nem a helyszíni rendszerekből. 
* Kimenő hozzáférés a virtuális gépekről az Azure-szolgáltatásokba. 
* A privát felhőt futtató munkaterhelések bejövő hozzáférése és felhasználása.

:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Alapszintű virtuális hálózat privát Felhőbeli kapcsolathoz" border="false":::

## <a name="on-premises-interconnectivity"></a>Helyszíni kapcsolat

A virtuális hálózatban és a helyszínen a teljes körű felhőalapú megvalósítás érdekében az Azure VMware-megoldás saját felhőit is elérheti a helyszíni környezetekben. Ez a megvalósítás az előző szakaszban leírt alapszintű implementáció kiterjesztése. Az alapszintű implementációhoz hasonlóan egy ExpressRoute áramkörre van szükség, de ezzel a megvalósítással a helyszíni környezetek és az Azure-beli privát felhő közötti kapcsolat is használható. 

Az alábbi ábra a helyszíni és a saját felhő közötti összekapcsolást mutatja be, amely a következő használati eseteket teszi lehetővé:
* VCenter vMotion gyors/hideg
* Helyszíni Azure VMware-megoldás saját felhőalapú felügyeleti hozzáférés

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Virtuális hálózat és helyszíni teljes Private Cloud connectivity" border="false":::

A privát felhővel való teljes összekapcsoláshoz engedélyezze a ExpressRoute Global Reach, majd kérjen egy engedélyezési kulcsot és egy privát társ-AZONOSÍTÓt a Azure Portal Global Reachhoz. Az engedélyezési kulcs és a társítási azonosító segítségével Global Reach lehet létrehozni az előfizetésben található ExpressRoute-áramkör és az új privát felhőhöz tartozó ExpressRoute áramkör között. A csatolást követően a két ExpressRoute áramkör a helyi környezetek közötti hálózati forgalmat átirányítja a saját felhőbe.  Az engedélyezési kulcs és a társ-azonosító kéréséhez és használatához szükséges eljárásokért tekintse meg azt az [oktatóanyagot, amely ExpressRoute-Global REACH-társítást hoz létre egy privát felhőben](tutorial-expressroute-global-reach-private-cloud.md) .



## <a name="next-steps"></a>Következő lépések 
További információ a [saját Felhőbeli tárolási fogalmakról](concepts-storage.md).


<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->

