---
title: Alapfogalmak – Magánfelhők Azure VMware Solution javítása
description: Megtudhatja, Azure VMware Solution hogyan monitorozza és javítja VMware ESXi kiszolgálókat egy Azure VMware Solution magánfelhőben.
ms.topic: conceptual
ms.custom: contperf-fy21q2
ms.date: 02/16/2021
ms.openlocfilehash: 9fa94d6093e03432f20672ecf5c0160ce479e175
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871668"
---
# <a name="monitor-and-repair-azure-vmware-solution-private-clouds"></a>Magánfelhők Azure VMware Solution javítása

Azure VMware Solution monitorozza a VMware ESXi kiszolgálókat egy Azure VMware Solution magánfelhőben. 

## <a name="what-azure-vmware-solution-monitors"></a>A Azure VMware Solution figyelők

Azure VMware Solution a következő feltételeket figyeli a gazdagépen:  

- Processzor állapota 
- Memória állapota 
- Kapcsolat és energiaállapot 
- Hardverventillátor állapota 
- Hálózati kapcsolat megszakadt 
- Hardveres rendszertábla állapota 
- Hibák történtek egy vSAN-gazdagép lemezén vagy lemezén 
- Hardveres feszültség 
- Hardverhőmérséklet állapota 
- Hardverteljesítmény állapota 
- Tárterület állapota 
- Kapcsolódási hiba 

> [!NOTE]
> Azure VMware Solution bérlői rendszergazdák nem szerkeszthetik vagy törölhetik a fent meghatározott VMware vCenter-riasztásokat, mivel ezeket a vCenter Azure VMware Solution vezérlősíkja kezeli. Ezeket a riasztásokat a Azure VMware Solution használják a gazdagép szervizelési Azure VMware Solution aktiválásához.

## <a name="azure-vmware-solution-host-remediation"></a>Azure VMware Solution szervizelése  

Amikor Azure VMware Solution teljesítménycsökkenést vagy hibát észlel egy Azure VMware Solution csomóponton, elindítja a gazdagép szervizelési folyamatát. A gazdagép szervizelése magában foglalja a hibás csomópont új, kifogástalan állapotú csomópontra való cseréjét.  

A gazdagép szervizelése egy új, kifogástalan állapotú csomópont hozzáadásával kezdődik a fürtben. Amikor lehetséges, a hibás gazdagép karbantartási módba VMware vSphere kerül. A VMware vMotion áthelyezi a virtuális gépeket a hibás gazdagépről a fürt más elérhető kiszolgálóira, így a számítási feladatok élő áttelepítése nulla állásidőt tehet lehetővé. Ha a hibás gazdagép nem helyezhető karbantartási módba, a gazdagép el lesz távolítva a fürtből.

## <a name="next-steps"></a>Következő lépések

Most, hogy már tudja, hogyan monitorozza Azure VMware Solution és javítja a magánfelhőket, a következővel ismerkedhet meg:

- [Azure VMware Solution felhőfrissítések](concepts-upgrades.md)
- [Az erőforrás Azure VMware Solution engedélyezése](enable-azure-vmware-solution.md)
