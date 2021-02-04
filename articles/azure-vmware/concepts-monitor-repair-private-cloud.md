---
title: Fogalmak – az Azure VMware-megoldás privát felhők monitorozása és javítása
description: Ismerje meg, hogyan figyeli és javítja az Azure VMware megoldás a VMware ESXi-kiszolgálókat egy Azure VMware-megoldás privát felhőben.
ms.topic: conceptual
ms.custom: contperf-fy21q2
ms.date: 02/03/2021
ms.openlocfilehash: 6174df429fd9b21c7f685c8ba14e6d5c0bba4c83
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99538958"
---
# <a name="monitor-and-repair-azure-vmware-solution-private-clouds"></a>Azure VMware-megoldás privát felhők monitorozása és javítása

Az Azure VMware megoldás folyamatosan figyeli a VMware ESXi-kiszolgálókat egy Azure VMware-megoldás saját felhőben. 

## <a name="what-azure-vmware-solution-monitors"></a>Az Azure VMware-megoldás figyelők

Az Azure VMware-megoldás a következő esetekben figyeli a meghibásodási feltételeket a gazdagépen:  

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

Ha az Azure VMware-megoldás romlást vagy hibát észlel egy bérlő privát felhőben lévő Azure VMware megoldás-csomóponton, elindítja a gazdagép szervizelési folyamatát. A gazdagép szervizelése azt jelenti, hogy a hibás csomópontot egy új kifogástalan állapotú csomópontra cseréli.  

A gazdagép szervizelési folyamata egy új, kifogástalan állapotú csomópont hozzáadásával indul el a fürtben. Ezután, ha lehetséges, a hibás gazdagép VMware vSphere karbantartási módba kerül. A VMware vMotion arra szolgál, hogy a virtuális gépeket a fürtön kívül más elérhető kiszolgálókra helyezze át, ami potenciálisan lehetővé teszi a számítási feladatok élő áttelepítését. Olyan esetekben, amikor a hibás gazdagép nem helyezhető karbantartási módba, a rendszer eltávolítja a gazdagépet a fürtből.

## <a name="next-steps"></a>Következő lépések

Most, hogy lefedette, hogy az Azure VMware-megoldás hogyan figyeli és javítja a privát felhőket, érdemes megismernie az alábbiakat:

- [Azure VMware-megoldás saját Felhőbeli frissítése](concepts-upgrades.md).
- [Az Azure VMware megoldás erőforrásának engedélyezése](enable-azure-vmware-solution.md).
