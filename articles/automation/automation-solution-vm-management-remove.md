---
title: Áttekintés Azure Automation Start/Stop VMs during off-hours eltávolítása
description: Ez a cikk azt ismerteti, hogyan távolítható el a Start/Stop VMs during off-hours funkció, és hogyan lehet leválasztni egy Automation-fiókot a Log Analytics-munkaterületről.
services: automation
ms.subservice: process-automation
ms.date: 04/15/2021
ms.topic: conceptual
ms.openlocfilehash: 9ec76197bfde6bb679f70c44ab01712f9f52bfd2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533951"
---
# <a name="remove-startstop-vms-during-off-hours-from-automation-account"></a>Fiók Start/Stop VMs during off-hours Automation-fiókból

Miután engedélyezi a Start/Stop VMs during off-hours az Azure-beli virtuális gépek futó állapotának kezeléséhez, dönthet úgy, hogy nem használja tovább. A szolgáltatás eltávolítása a támogatott üzembe helyezési modellek alapján az alábbi módszerek egyikével létezik:

* Törölje az Automation-fiókot tartalmazó erőforráscsoportot és a Azure Monitor Log Analytics-munkaterületet, amelyek mind a funkció támogatásához vannak kivetve.
* A Log Analytics-munkaterület leválasztása az Automation-fiókról és a funkcióhoz dedikált Automation-fiók törlése.
* Törölje a funkciót egy Automation-fiókból és egy olyan csatolt munkaterületről, amely más felügyeleti és monitorozási célkitűzéseket támogat.

A funkció törlésével a rendszer csak a társított runbookokat távolítja el, nem törli az üzembe helyezés során létrehozott ütemezéseket vagy változókat, illetve az azt követően létrehozott egyéni forgatókönyveket.

> [!NOTE]
> A folytatás előtt ellenőrizze, hogy [nincsenek-e](../azure-resource-manager/management/lock-resources.md) Resource Manager az előfizetésre, erőforráscsoportra vagy erőforrásra alkalmazott zárolások, amelyek megakadályozzák a kritikus fontosságú erőforrások véletlen törlését vagy módosítását. Az új Start/Stop VMs during off-hours üzembe helyezésekor a zárolási szintet **CanNotDelete-re** állítja az Automation-fiókban található függő erőforrásokhoz (különösen a forgatókönyveihez és változóihoz). Az Automation-fiók törlése előtt minden zárolást el kell távolítani.

## <a name="delete-the-dedicated-resource-group"></a>A dedikált erőforráscsoport törlése

Az erőforráscsoport törléséhez kövesse a Azure Resource Manager erőforráscsoport és [erőforrás-törlés című cikkben ismertetett lépéseket.](../azure-resource-manager/management/delete-resource-group.md)

## <a name="delete-the-automation-account"></a>Az Automation-fiók törlése

A dedikált Automation-fiók törléséhez Start/Stop VMs during off-hours következő lépéseket.

1. Jelentkezzen be az Azure-ba a következő portálon: [https://portal.azure.com](https://portal.azure.com) .

2. Lépjen az Automation-fiókjához, és válassza a **Kapcsolódó erőforrások** alatt a Csatolt munkaterület **lehetőséget.**

3. Válassza **az Ugrás a munkaterületre lehetőséget.**

4. Kattintson a **Megoldások elemre** az **Általános alatt.**

5. A Megoldások lapon válassza a **Start-Stop-VM[Workspace] lehetőséget.**

6. A **VMManagementSolution[Workspace]** oldalon válassza a **menü Törlés** parancsát.

7. Bár a rendszer ellenőrzi az adatokat és törli a szolgáltatást, a menüből kiválasztott **Értesítések** alatt nyomon követheti a folyamat előrehaladását. Az eltávolítási folyamat után visszatér a Megoldások lapra.

### <a name="unlink-workspace-from-automation-account"></a>Munkaterület leválasztása Automation-fiókról

A Log Analytics-munkaterületet két lehetőség közül választhatja ki az Automation-fiókból. Ezt a folyamatot az Automation-fiókból vagy a csatolt munkaterületről hajthatja végre.

Az Automation-fiókról való leválasztáshoz hajtsa végre az alábbi lépéseket.

1. A Azure Portal **Automation-fiókok lehetőséget.**

2. Nyissa meg Automation-fiókját, és válassza a csatolt **munkaterületet** **a kapcsolódó erőforrások alatt** a bal oldalon.

3. A Munkaterület **leválasztása lapon** válassza a Munkaterület **leválasztása** lehetőséget, és válaszoljon a kérésekre.

   ![Munkaterület leválasztása lap](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Miközben megkísérli leválasztni a Log Analytics-munkaterületet, a menü Értesítések menüpontjában nyomon követheti **a** folyamat előrehaladását.

A munkaterületről való leválasztáshoz hajtsa végre a következő lépéseket.

1. A munkaterület Azure Portal Log **Analytics-munkaterületek lehetőséget.**

2. A munkaterületen válassza az **Automation-fiók lehetőséget a** **Kapcsolódó erőforrások alatt.**

3. Az Automation-fiók lapon válassza a **Fiók leválasztása** lehetőséget, és válaszoljon a kérésekre.

Miközben megkísérli leválasztni az Automation-fiókot, a menü Értesítések területén nyomon **követheti** a folyamat előrehaladását.

### <a name="delete-automation-account"></a>Automation-fiók törlése

1. A Azure Portal **Automation-fiókok lehetőséget.**

2. Nyissa meg Automation-fiókját, **és válassza a Menü** Törlés parancsát.

Az adatok ellenőrzése és a fiók törlése során a menüből kiválasztott **Értesítések** alatt követheti nyomon a folyamat előrehaladását.

## <a name="delete-the-feature"></a>A funkció törlése

Ha törölni Start/Stop VMs during off-hours Automation-fiókjából, hajtsa végre a következő lépéseket. Az Automation-fiók és a Log Analytics-munkaterület nem törlődik a folyamat részeként. Ha nem szeretné megtartani a Log Analytics-munkaterületet, manuálisan kell törölnie. A munkaterület törlésével kapcsolatos további információkért lásd: [Az Azure Log Analytics-munkaterület törlése és helyreállítása.](../azure-monitor/logs/delete-workspace.md)

1. Lépjen az Automation-fiókjához, és válassza a **Kapcsolódó erőforrások** alatt a Csatolt munkaterület **lehetőséget.**

2. Válassza **az Ugrás a munkaterületre lehetőséget.**

3. Kattintson a **Megoldások elemre** az **Általános alatt.**

4. A Megoldások lapon válassza a **Start-Stop-VM[Workspace] lehetőséget.**

5. A **VMManagementSolution[Workspace]** oldalon válassza a **menü Törlés** parancsát.

    ![Virtuálisgép-kezelési szolgáltatás törlése](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. A Megoldás törlése ablakban erősítse meg, hogy törölni szeretné a funkciót.

7. Bár a rendszer ellenőrzi az adatokat és törli a szolgáltatást, a menüből kiválasztott **Értesítések** alatt nyomon követheti a folyamat előrehaladását. Az eltávolítási folyamat után visszatér a Megoldások lapra.

8. Ha nem szeretné megtartani a [](automation-solution-vm-management.md#components) funkció vagy később létrehozott erőforrásokat (például változókat, ütemezéseket stb.), manuálisan kell törölnie őket a fiókból.

## <a name="next-steps"></a>Következő lépések

A szolgáltatás újra engedélyezéséhez lásd: [Indítás/leállítás engedélyezése munkaidőn túl.](automation-solution-vm-management-enable.md)