---
title: Azure Automation fiók törlése
description: Ez a cikk azt ismerteti, hogyan törölheti az Automation-fiókját a különböző konfigurációs forgatókönyvek között.
services: automation
ms.service: automation
ms.subservice: process-automation
ms.date: 03/18/2021
ms.topic: conceptual
ms.openlocfilehash: c3a514aa507fcf069671f987e175b7ae5be59d10
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105735070"
---
# <a name="how-to-delete-your-azure-automation-account"></a>Azure Automation fiók törlése

Miután engedélyezte, hogy egy Azure Automation fiók automatizálja informatikai vagy üzleti folyamatát, vagy engedélyezze annak egyéb funkcióit az Azure-beli és nem Azure-alapú gépek, például a Update Management működésének támogatásához, dönthet úgy, hogy leállítja az Automation-fiók használatát. Ha engedélyezte a Azure Monitor Log Analytics munkaterülettel való integrációtól függő funkciókat, a művelet végrehajtásához több lépés szükséges.

Az Automation-fiók eltávolítását az alábbi módszerek egyikével végezheti el a támogatott üzembe helyezési modellek alapján:

* Törölje az Automation-fiókot tartalmazó erőforráscsoportot.
* Törölje az Automation-fiókot és a csatolt Azure Monitor Log Analytics munkaterületet tartalmazó erőforráscsoportot, ha:

    * A fiók és a munkaterület a Update Management, a Change Tracking és a leltár, valamint/vagy Start/Stop VMs during off-hours támogatására szolgál.
    * A fiók dedikáltan dolgozza fel az automationt, és integrálva van egy munkaterülettel a runbook feladatok állapotának és a feladatok adatfolyamának elküldéséhez.

* A Log Analytics munkaterület leválasztása az Automation-fiókból és az Automation-fiók törlése.
* Törölje a szolgáltatást a csatolt munkaterületről, válassza le a fiókot a munkaterületről, majd törölje az Automation-fiókot.

Ebből a cikkből megtudhatja, hogyan távolíthatja el az Automation-fiókját a Azure Portal, a PowerShell, az Azure CLI vagy a REST API használatával.

## <a name="delete-the-dedicated-resource-group"></a>A dedikált erőforráscsoport törlése

Az Automation-fiók és a Log Analytics munkaterület törléséhez, ha a fiókhoz van csatolva, és a fiókhoz van hozzárendelve, akkor kövesse az [Azure Resource Manager erőforráscsoport és erőforrás-törlés](../azure-resource-manager/management/delete-resource-group.md) című cikkben ismertetett lépéseket.

## <a name="delete-a-standalone-automation-account"></a>Önálló Automation-fiók törlése

Ha az Automation-fiókja nincs Log Analytics munkaterülethez társítva, akkor a következő lépésekkel törölheti.

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

1. Jelentkezzen be az Azure-ba [https://portal.azure.com](https://portal.azure.com) .

2. A Azure Portal navigáljon az **Automation-fiókokhoz**.

3. Nyissa meg az Automation-fiókját, és válassza a menü **Törlés** elemét.

Az információk ellenőrzése és a fiók törlése után nyomon követheti a menüből kiválasztott **értesítések** területen látható előrehaladást.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ez a parancs az Automation-fiókot az érvényesítés megkérdezése nélkül távolítja el.

```powershell
Remove-AzAutomationAccount -Name "automationAccountName" -Force -ResourceGroupName "resourceGroupName"
```

---

## <a name="delete-a-standalone-automation-account-linked-to-workspace"></a>Munkaterülethez csatolt önálló Automation-fiók törlése

Ha az Automation-fiókja egy Log Analytics munkaterülethez van csatolva a feladatok adatfolyamait és a feladatok naplóinak összegyűjtéséhez, hajtsa végre a következő lépéseket a fiók törléséhez.

Az Log Analytics munkaterület az Automation-fiókból való leválasztására két lehetőség áll rendelkezésre. Ezt a folyamatot az Automation-fiókból vagy a társított munkaterületről is végrehajthatja.

Az Automation-fiókból való leválasztáshoz hajtsa végre az alábbi lépéseket.

1. A Azure Portal navigáljon az **Automation-fiókokhoz**.

2. Nyissa meg az Automation-fiókját, és a bal oldali **kapcsolódó erőforrások** területen válassza a **csatolt munkaterület** lehetőséget.

3. A **munkaterület megszüntetése** lapon válassza a **munkaterület** leválasztása lehetőséget, és válaszoljon a kérdésekre.

   ![Munkaterület leválasztása lap](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Miközben megpróbálja leválasztani az Log Analytics munkaterületet, nyomon követheti a menü **értesítések** részén látható előrehaladást.

A munkaterületről való leválasztáshoz hajtsa végre az alábbi lépéseket.

1. A Azure Portal navigáljon **log Analytics munkaterületek** elemre.

2. A munkaterületen válassza az **Automation-fiók** lehetőséget a **kapcsolódó erőforrások** területen.

3. Az Automation-fiók lapon válassza a **fiók megszüntetése** lehetőséget, és válaszoljon a felszólításokra.

Miközben megpróbálja leválasztani az Automation-fiókot, a menü **értesítések** részén nyomon követheti a folyamat állapotát.

Miután az Automation-fiók sikeresen lekapcsolódott a munkaterületről, hajtsa végre az [önálló Automation-fiók](#delete-a-standalone-automation-account) szakaszban leírt lépéseket a fiók törléséhez.

## <a name="delete-a-shared-capability-automation-account"></a>Megosztott képességű Automation-fiók törlése

A Log Analytics munkaterülethez csatolt Automation-fiók törléséhez Update Management, Change Tracking és leltár, valamint/vagy Start/Stop VMs during off-hours támogatásához hajtsa végre az alábbi lépéseket.

### <a name="step-1-delete-the-solution-from-the-linked-workspace"></a>1. lépés A megoldás törlése a csatolt munkaterületről

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

1. Jelentkezzen be az Azure-ba [https://portal.azure.com](https://portal.azure.com) .

2. Navigáljon az Automation-fiókjához, és a **kapcsolódó erőforrások** területen válassza a **csatolt munkaterület** lehetőséget.

3. Válassza **a ugrás munkaterületre** lehetőséget.

4. Kattintson az **általános** területen a **megoldások** elemre.

5. A megoldások lapon válassza a következők egyikét a fiókban telepített szolgáltatás (ok) alapján:

    * Start/Stop VMs during off-hours válassza a **Start-Stop-VM [munkaterület neve]** elemet.
    * Update Management esetében válassza a **frissítések (munkaterület neve)** lehetőséget.
    * Change Tracking és leltár esetében válassza a **változáskövetési (munkaterület neve)** lehetőséget.

6. A **megoldás** lapon válassza a menü **Törlés** elemét. Ha a fenti felsorolt funkciók közül egynél több van telepítve az Automation-fiókhoz és a társított munkaterülethez, a továbblépés előtt ki kell választania és törölnie kell egyet.

7. Az információk ellenőrzése és a szolgáltatás törlése után nyomon követheti a menüből kiválasztott **értesítések** szakaszban található előrehaladást. Az eltávolítási folyamat után visszatért a megoldások oldalára.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A telepített megoldások Azure PowerShell használatával történő eltávolításához használja a [Remove-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/remove-azmonitorloganalyticssolution) parancsmagot.

```powershell
Remove-AzMonitorLogAnalyticsSolution -ResourceGroupName "resourceGroupName" -Name "solutionName"
```

---

### <a name="step-2-unlink-workspace-from-automation-account"></a>2. lépés Munkaterület leválasztása Automation-fiókról

Az Log Analytics munkaterület az Automation-fiókból való leválasztására két lehetőség áll rendelkezésre. Ezt a folyamatot az Automation-fiókból vagy a társított munkaterületről is végrehajthatja.

Az Automation-fiókból való leválasztáshoz hajtsa végre az alábbi lépéseket.

1. A Azure Portal navigáljon az **Automation-fiókokhoz**.

2. Nyissa meg az Automation-fiókját, és a bal oldali **kapcsolódó erőforrások** területen válassza a **csatolt munkaterület** lehetőséget.

3. A **munkaterület megszüntetése** lapon válassza a **munkaterület** leválasztása lehetőséget, és válaszoljon a kérdésekre.

   ![Munkaterület leválasztása lap](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Miközben megpróbálja leválasztani az Log Analytics munkaterületet, nyomon követheti a menü **értesítések** részén látható előrehaladást.

A munkaterületről való leválasztáshoz hajtsa végre az alábbi lépéseket.

1. A Azure Portal navigáljon **log Analytics munkaterületek** elemre.

2. A munkaterületen válassza az **Automation-fiók** lehetőséget a **kapcsolódó erőforrások** területen.

3. Az Automation-fiók lapon válassza a **fiók megszüntetése** lehetőséget, és válaszoljon a felszólításokra.

Miközben megpróbálja leválasztani az Automation-fiókot, a menü **értesítések** részén nyomon követheti a folyamat állapotát.

### <a name="step-3-delete-automation-account"></a>3. lépés Automation-fiók törlése

Miután az Automation-fiók sikeresen lekapcsolódott a munkaterületről, hajtsa végre az [önálló Automation-fiók](#delete-a-standalone-automation-account) szakaszban leírt lépéseket a fiók törléséhez.

## <a name="next-steps"></a>Következő lépések

Ha Automation-fiókot szeretne létrehozni a Azure Portalből, tekintse meg az [önálló Azure Automation fiók létrehozása](automation-create-standalone-account.md)című témakört. Ha sablon használatával szeretné létrehozni a fiókját, tekintse meg [az Automation-fiók létrehozása Azure Resource Manager sablonnal](quickstart-create-automation-account-template.md)című témakört.