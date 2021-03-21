---
title: Azure Automation Start/Stop VMs during off-hours áttekintésének eltávolítása
description: Ez a cikk bemutatja, hogyan távolíthatja el az Start/Stop VMs during off-hours funkciót, és hogyan törölhet Automation-fiókot a Log Analytics munkaterületről.
services: automation
ms.subservice: process-automation
ms.date: 03/04/2021
ms.topic: conceptual
ms.openlocfilehash: 0bab5d8e82ce432e9b3834fe4c003316545eb338
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102122085"
---
# <a name="remove-startstop-vms-during-off-hours-from-automation-account"></a>Start/Stop VMs during off-hours eltávolítása az Automation-fiókból

Miután engedélyezte a Start/Stop VMs during off-hours funkció számára az Azure-beli virtuális gépek futó állapotának kezelését, dönthet úgy, hogy leállítja a használatát. A funkció eltávolításához az alábbi módszerek egyikét használhatja a támogatott telepítési modellek alapján:

* Törölje az Automation-fiókot tartalmazó erőforráscsoportot és a társított Azure Monitor Log Analytics munkaterületet, amelyek mindegyike támogatja ezt a funkciót.
* A Log Analytics munkaterület leválasztása az Automation-fiókból, és a szolgáltatáshoz dedikált Automation-fiók törlése.
* Törölje a szolgáltatást egy Automation-fiókból és a társított munkaterületből, amely más felügyeleti és figyelési célokat is támogat.

A szolgáltatás törlésével a rendszer csak a társított runbookok távolítja el, nem törli az üzembe helyezés során létrehozott ütemezett vagy változókat, illetve az azt követően létrehozott egyéni definíciókat.

## <a name="delete-the-dedicated-resource-group"></a>A dedikált erőforráscsoport törlése

Az erőforráscsoport törléséhez kövesse az [Azure Resource Manager erőforráscsoport és az erőforrás-törlés](../azure-resource-manager/management/delete-resource-group.md) című cikkben ismertetett lépéseket.

## <a name="delete-the-automation-account"></a>Az Automation-fiók törlése

A következő lépésekkel törölheti a Start/Stop VMs during off-hours dedikált Automation-fiókját.

1. Jelentkezzen be az Azure-ba [https://portal.azure.com](https://portal.azure.com) .

2. Navigáljon az Automation-fiókjához, és a **kapcsolódó erőforrások** területen válassza a **csatolt munkaterület** lehetőséget.

3. Válassza **a ugrás munkaterületre** lehetőséget.

4. Kattintson az **általános** területen a **megoldások** elemre.

5. A megoldások lapon válassza a **Start-Stop-VM [munkaterület]** elemet.

6. A **VMManagementSolution [munkaterület]** lapon válassza a **Törlés** lehetőséget a menüből.

7. Az információk ellenőrzése és a szolgáltatás törlése után nyomon követheti a menüből kiválasztott **értesítések** szakaszban található előrehaladást. Az eltávolítási folyamat után visszatért a megoldások oldalára.

### <a name="unlink-workspace-from-automation-account"></a>Munkaterület leválasztása Automation-fiókról

Az Log Analytics munkaterület az Automation-fiókból való leválasztására két lehetőség áll rendelkezésre. Ezt a folyamatot az Automation-fiókból vagy a társított munkaterületről is végrehajthatja.

Az Automation-fiókból való leválasztáshoz hajtsa végre az alábbi lépéseket.

1. A Azure Portal válassza az **Automation-fiókok** elemet.

2. Nyissa meg az Automation-fiókját, és a bal oldali **kapcsolódó erőforrások** területen válassza a **csatolt munkaterület** lehetőséget.

3. A **munkaterület** leválasztása lapon válassza a **munkaterület megszüntetése** lehetőséget, és válaszoljon a felszólításokra.

   ![Munkaterület leválasztása lap](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Miközben megpróbálja leválasztani az Log Analytics munkaterületet, nyomon követheti a menü **értesítések** részén látható előrehaladást.

A munkaterületről való leválasztáshoz hajtsa végre az alábbi lépéseket.

1. A Azure Portal válassza a **log Analytics munkaterületek** lehetőséget.

2. A munkaterületen válassza az **Automation-fiók** lehetőséget a **kapcsolódó erőforrások** területen.

3. Az Automation-fiók lapon válassza a **fiók megszüntetése** lehetőséget, és válaszoljon a felszólításokra.

Miközben megpróbálja leválasztani az Automation-fiókot, a menü **értesítések** részén nyomon követheti a folyamat állapotát.

### <a name="delete-automation-account"></a>Automation-fiók törlése

1. A Azure Portal válassza az **Automation-fiókok** elemet.

2. Nyissa meg az Automation-fiókját, és válassza a menü **Törlés** elemét.

Az információk ellenőrzése és a fiók törlése után nyomon követheti a menüből kiválasztott **értesítések** területen látható előrehaladást.

## <a name="delete-the-feature"></a>A szolgáltatás törlése

Ha Start/Stop VMs during off-hours szeretne törölni az Automation-fiókból, hajtsa végre a következő lépéseket. Az Automation-fiók és Log Analytics munkaterület nem törlődik a folyamat részeként. Ha nem szeretné megtartani a Log Analytics munkaterületet, manuálisan kell törölnie. További információ a munkaterület törléséről: az [Azure log Analytics munkaterület törlése és helyreállítása](../azure-monitor/logs/delete-workspace.md).

1. Navigáljon az Automation-fiókjához, és a **kapcsolódó erőforrások** területen válassza a **csatolt munkaterület** lehetőséget.

2. Válassza **a ugrás munkaterületre** lehetőséget.

3. Kattintson az **általános** területen a **megoldások** elemre.

4. A megoldások lapon válassza a **Start-Stop-VM [munkaterület]** elemet.

5. A **VMManagementSolution [munkaterület]** lapon válassza a **Törlés** lehetőséget a menüből.

    ![Virtuálisgép-felügyeleti szolgáltatás törlése](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. A megoldás törlése ablakban erősítse meg, hogy törölni kívánja a szolgáltatást.

7. Az információk ellenőrzése és a szolgáltatás törlése után nyomon követheti a menüből kiválasztott **értesítések** szakaszban található előrehaladást. Az eltávolítási folyamat után visszatért a megoldások oldalára.

8. Ha nem szeretné megtartani a szolgáltatás által vagy később létrehozott [erőforrásokat](automation-solution-vm-management.md#components) (például változók, ütemtervek stb.), manuálisan kell törölnie azokat a fiókból.

## <a name="next-steps"></a>Következő lépések

A szolgáltatás újbóli engedélyezéséhez tekintse meg a következő témakört: [Indítás/Leállítás engedélyezése munkaidőn kívül](automation-solution-vm-management-enable.md).