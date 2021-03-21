---
title: Fogalmak – az Azure VMware-megoldás privát felhők monitorozása és javítása
description: Ismerje meg, hogyan figyeli és javítja az Azure VMware megoldás a VMware ESXi-kiszolgálókat egy Azure VMware-megoldás privát felhőben.
ms.topic: conceptual
ms.custom: contperf-fy21q2
ms.date: 02/16/2021
ms.openlocfilehash: 59319b5598be9770e82b9676a28444648230a019
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100633138"
---
# <a name="monitor-and-repair-azure-vmware-solution-private-clouds"></a>Azure VMware-megoldás privát felhők monitorozása és javítása

Az Azure VMware megoldás folyamatosan figyeli a VMware ESXi-kiszolgálókat egy Azure VMware-megoldás saját felhőben. 

## <a name="what-azure-vmware-solution-monitors"></a>Az Azure VMware-megoldás figyelők

Az Azure VMware-megoldás a következő feltételeket figyeli a gazdagépen:  

- Processzor állapota 
- Memória állapota 
- A kapcsolatok és a tápellátás állapota 
- Hardver ventilátorának állapota 
- Hálózati kapcsolat elvesztése 
- A hardver rendszertáblájának állapota 
- Hibák történtek egy vSAN-állomás lemezén (i) 
- Hardveres feszültség 
- Hardver hőmérsékleti állapota 
- Hardver energiaellátási állapota 
- Tárolási állapot 
- Kapcsolódási hiba 

> [!NOTE]
> Az Azure VMware megoldás bérlői rendszergazdái nem módosíthatják és nem törölhetik a fent meghatározott VMware vCenter riasztásokat, mivel ezeket az Azure VMware megoldás-vezérlési sík felügyeli a vCenter-on. Ezeket a riasztásokat az Azure VMware-megoldás figyelése használja az Azure VMware Solution Host szervizelési folyamatának elindításához.

## <a name="azure-vmware-solution-host-remediation"></a>Azure VMware megoldás gazdagép szervizelése  

Ha az Azure VMware-megoldás romlást vagy hibát észlel egy Azure VMware-megoldási csomóponton, elindítja a gazdagép szervizelési folyamatát. A gazdagép szervizelése azt jelenti, hogy a hibás csomópontot egy új kifogástalan állapotú csomópontra cseréli.  

A gazdagép szervizelése új, kifogástalan állapotú csomópont hozzáadásával kezdődik a fürtben. Ezután, ha lehetséges, a hibás gazdagép VMware vSphere karbantartási módba kerül. A VMware vMotion áthelyezi a virtuális gépeket a hibás gazdagépről a fürt többi elérhető kiszolgálójára, ami potenciálisan nulla állásidőt biztosít a számítási feladatok élő áttelepítéséhez. Ha a hibás gazdagép nem helyezhető karbantartási módba, a rendszer eltávolítja a gazdagépet a fürtből.

## <a name="next-steps"></a>Következő lépések

Most, hogy lefedette, hogy az Azure VMware-megoldás hogyan figyeli és javítja a privát felhőket, érdemes megismernie az alábbiakat:

- [Azure VMware-megoldás saját Felhőbeli frissítése](concepts-upgrades.md).
- [Az Azure VMware megoldás erőforrásának engedélyezése](enable-azure-vmware-solution.md).
