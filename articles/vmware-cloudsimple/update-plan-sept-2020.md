---
title: Azure VMware-megoldás a CloudSimple szeptember 2020 frissítésével
description: Ebből a cikkből megtudhatja, mire számíthat a karbantartási művelet során, és hogyan változtatja meg a saját felhőjét.
author: dikamath
ms.author: dikamath
ms.date: 09/3/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
manager: dikamath
ms.openlocfilehash: ae9c1ba5259e95ed030d7099e5dafe2d4f7935b4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89447975"
---
# <a name="azure-vmware-solution-by-cloudsimple-september-2020-update"></a>Azure VMware-megoldás a CloudSimple szeptember 2020 frissítésével

Szeptemberben az Azure VMware megoldás szolgáltatás fontos frissítése lesz elvégezve. A karbantartás részeként elküldött e-mail-értesítés tartalmazza a karbantartás idővonalát is. Ebből a cikkből megtudhatja, mire számíthat a karbantartási művelet során, és megváltoztathatja a saját felhőjét.

> [!NOTE]
> Ez egy nem zavaró frissítés. A frissítés során előfordulhat, hogy a redundáns összetevők egyike leáll.

## <a name="vmware-infrastructure-upgrade"></a>VMware-infrastruktúra frissítése

A privát felhő VMware-infrastruktúráját egy újabb verzióra frissíti a rendszer. Ide tartoznak a vCenter, az ESXi, a NSX-T és a Hybrid Cloud Extension (HCX) összetevőinek (ha telepítve vannak) a privát felhőben.

A frissítés során a rendszer egy új csomópontot ad hozzá a saját felhőhöz, mielőtt a csomópontot karbantartási módba helyezi a frissítési művelethez. Ez biztosítja a saját felhő kapacitását és a privát felhő rendelkezésre állását a frissítési folyamat során. A VMware-összetevők frissítése során a vCenter webes felhasználói felületén megjelenő riasztások jelenhetnek meg. A riasztások a Service Operations csapat által végrehajtott karbantartási műveletek részét képezik.

**Összetevő verziója**

- ESXi 6.7 U3
- vCenter 6.7 U3
- vSAN 6,7
- NSX adatközpont 2.5.1
- HCX 3.5.2

## <a name="datacenter-updates"></a>Adatközpont frissítései

Ez a frissítés az adatközpont-infrastruktúra frissítéseit tartalmazza. A nem zavaró frissítések a karbantartási időszak alatt lesznek elvégezve. A frissítési folyamat során a csökkentett redundancia jelenik meg. Riasztások hozhatók létre a saját Felhőbeli VMware-infrastruktúrában, a ExpressRoute-áramkörökben, a GlobalReach-kapcsolatokban, valamint a helyek közötti VPN-eszközök egyikével a kapcsolat elérhetőségével kapcsolatban. Ez normális a frissítés során, mivel az összetevők a frissítés részeként újra lesznek indítva.

-   Ha a helyek közötti VPN-t egyetlen példányként telepítik (nem HA), előfordulhat, hogy újra létre kell hoznia a VPN-kapcsolatot.

-   Ha pont – hely típusú VPN-kapcsolatot használ, újra létre kell hoznia a VPN-kapcsolatot.

## <a name="post-update"></a>Frissítés utáni

A frissítések befejezését követően a VMware-összetevők újabb verzióit kell látnia. Ha bármilyen problémát észlel, vagy kérdése van, forduljon a támogatási csapathoz egy [támogatási jegy](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)megnyitásával.
