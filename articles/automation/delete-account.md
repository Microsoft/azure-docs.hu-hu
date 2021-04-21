---
title: A Azure Automation törlése
description: Ez a cikk bemutatja, hogyan törölheti Automation-fiókját a különböző konfigurációs forgatókönyvekben.
services: automation
ms.service: automation
ms.subservice: process-automation
ms.date: 04/15/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fe2d99a610be3877b4a347e4bd0dd17df53ba326
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834155"
---
# <a name="how-to-delete-your-azure-automation-account"></a>A fiók Azure Automation törlése

Miután engedélyezi egy Azure Automation-fiók számára az it- vagy üzleti folyamat automatizálását, vagy más funkcióinak engedélyezését az Azure-beli és nem Azure-beli gépek (például az Update Management) műveleteinek kezeléséhez, dönthet úgy, hogy nem használja tovább az Automation-fiókot. Ha olyan szolgáltatásokat engedélyezett, amelyek egy Azure Monitor Log Analytics-munkaterülettel való integrációtól függenek, további lépésekre van szükség a művelet éhez.

Az Automation-fiók eltávolítása a támogatott üzembe helyezési modellek alapján az alábbi módszerek egyikével létezik:

* Törölje az Automation-fiókot tartalmazó erőforráscsoportot.
* Törölje az Automation-fiókot és a Log Analytics-munkaterülethez Azure Monitor erőforráscsoportot, ha:

    * A fiók és a munkaterület dedikáltan támogatja a Update Management, változáskövetés és leltározás és/vagy Start/Stop VMs during off-hours.
    * A fiók a folyamatautomatizálást és a munkaterülettel való integrációt dedikálta a runbook feladatállapotának és feladatstreamek küldése érdekében.

* A Log Analytics-munkaterület leválasztása az Automation-fiókról és az Automation-fiók törlése.
* Törölje a funkciót a csatolt munkaterületről, válassza le a fiókot a munkaterületről, majd törölje az Automation-fiókot.

Ebből a cikkből megtudhatja, hogyan távolíthatja el teljesen automation-fiókját a Azure Portal használatával a Azure PowerShell, az Azure CLI vagy a REST API.

> [!NOTE]
> Mielőtt továbblépne, ellenőrizze, hogy [nincsenek-e](../azure-resource-manager/management/lock-resources.md) Resource Manager az előfizetésre, erőforráscsoportra vagy erőforrásra alkalmazott zárolások, amelyek megakadályozzák a kritikus fontosságú erőforrások véletlen törlését vagy módosítását. Ha üzembe helyezett egy Start/Stop VMs during off-hours megoldást, a zárolási szintet **CanNotDelete-re** állítja az Automation-fiókban található függő erőforrásokhoz (különösen a forgatókönyveihez és változóihoz). Az Automation-fiók törlése előtt minden zárolást el kell távolítani.

## <a name="delete-the-dedicated-resource-group"></a>A dedikált erőforráscsoport törlése

Ha törölni szeretné az Automation-fiókot, valamint a fiókhoz kapcsolt Log Analytics-munkaterületet is, amely a fiókhoz dedikált erőforráscsoportban jött létre, kövesse az Azure Resource Manager erőforráscsoport és az erőforrás-törlés című cikkben [leírt](../azure-resource-manager/management/delete-resource-group.md) lépéseket.

## <a name="delete-a-standalone-automation-account"></a>Önálló Automation-fiók törlése

Ha az Automation-fiók nincs Log Analytics-munkaterülethez kapcsolva, a következő lépésekkel törölheti.

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

1. Jelentkezzen be az Azure-ba a következő [https://portal.azure.com](https://portal.azure.com) portálon: .

2. A Azure Portal az **Automation-fiókok hez.**

3. Nyissa meg Automation-fiókját, **és válassza a Menü** Törlés parancsát.

Az adatok ellenőrzése és a fiók törlése során a menüből kiválasztott **Értesítések** alatt követheti nyomon a folyamat előrehaladását.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ez a parancs az ellenőrzés kérése nélkül távolítja el az Automation-fiókot.

```powershell
Remove-AzAutomationAccount -Name "automationAccountName" -Force -ResourceGroupName "resourceGroupName"
```

---

## <a name="delete-a-standalone-automation-account-linked-to-workspace"></a>Munkaterülethez csatolt önálló Automation-fiók törlése

Ha az Automation-fiók egy Log Analytics-munkaterülethez van kapcsolva a feladatstreamek és feladatnaplók gyűjtéséhez, a következő lépésekkel törölheti a fiókot.

A Log Analytics-munkaterület és az Automation-fiók leválasztása két lehetőséggel lehetséges. Ezt a folyamatot az Automation-fiókból vagy a csatolt munkaterületről hajthatja végre.

Az Automation-fiókról való leválasztáshoz hajtsa végre az alábbi lépéseket.

1. A Azure Portal az **Automation-fiókok hez.**

2. Nyissa meg Automation-fiókját, és válassza a csatolt **munkaterületet** **a kapcsolódó erőforrások alatt** a bal oldalon.

3. A Munkaterület **leválasztása lapon** válassza a **Munkaterület leválasztása** lehetőséget, és válaszoljon a kérésekre.

   ![Munkaterület leválasztása lap](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Miközben megkísérli leválasztni a Log Analytics-munkaterületet, a menü Értesítések menüpontjában nyomon **követheti** a folyamat előrehaladását.

A munkaterületről való leválasztáshoz hajtsa végre az alábbi lépéseket.

1. A Azure Portal lépjen a **Log Analytics-munkaterületek lapra.**

2. A munkaterületen válassza az **Automation-fiók lehetőséget a** **Kapcsolódó erőforrások alatt.**

3. Az Automation-fiók lapon válassza a **Fiók leválasztása** lehetőséget, és válaszoljon a kérésekre.

Az Automation-fiók leválasztása közben a menü Értesítések területén **nyomon** követheti a folyamat előrehaladását.

Miután sikeresen leválasztotta az Automation-fiókot a munkaterületről, hajtsa végre az [önálló Automation-fiók](#delete-a-standalone-automation-account) szakaszban található lépéseket a fiók törléséhez.

## <a name="delete-a-shared-capability-automation-account"></a>Megosztott képesség automation-fiókjának törlése

A log analytics-munkaterülethez csatolt, a Update Management, változáskövetés és leltározás és/vagy Start/Stop VMs during off-hours kapcsolatos Automation-fiók törléséhez hajtsa végre a következő lépéseket.

### <a name="step-1-delete-the-solution-from-the-linked-workspace"></a>1. lépés A megoldás törlése a csatolt munkaterületről

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

1. Jelentkezzen be az Azure-ba a következő portálon: [https://portal.azure.com](https://portal.azure.com) .

2. Lépjen az Automation-fiókjához, és válassza a **Kapcsolódó erőforrások** alatt a Csatolt **munkaterület lehetőséget.**

3. Válassza **az Ugrás a munkaterületre lehetőséget.**

4. Kattintson a **Megoldások elemre** az **Általános alatt.**

5. A Megoldások lapon válasszon egyet a következők közül a fiókban üzembe helyezett funkció(k) alapján:

    * A Start/Stop VMs during off-hours válassza a **Start-Stop-VM[munkaterület neve] lehetőséget.**
    * A Update Management válassza a **Frissítések(munkaterület neve) lehetőséget.**
    * A változáskövetés és leltározás válassza a **ChangeTracking(munkaterület neve) lehetőséget.**

6. A Megoldás **lapon** válassza a **Törlés lehetőséget** a menüből. Ha a fent felsorolt szolgáltatások közül több is telepítve van az Automation-fiókban és a csatolt munkaterületen, a folytatás előtt mindegyiket ki kell választania és törölnie kell.

7. Bár a rendszer ellenőrzi az adatokat és törli a szolgáltatást, a menüből kiválasztott **Értesítések** alatt nyomon követheti a folyamat előrehaladását. Az eltávolítási folyamat után visszatér a Megoldások lapra.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ha egy telepített megoldást szeretne eltávolítani a Azure PowerShell, használja a [Remove-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/remove-azmonitorloganalyticssolution) parancsmagot.

```powershell
Remove-AzMonitorLogAnalyticsSolution -ResourceGroupName "resourceGroupName" -Name "solutionName"
```

---

### <a name="step-2-unlink-workspace-from-automation-account"></a>2. lépés Munkaterület leválasztása Automation-fiókról

A Log Analytics-munkaterületet két lehetőség közül választhatja ki az Automation-fiókból. Ezt a folyamatot az Automation-fiókból vagy a csatolt munkaterületről hajthatja végre.

Az Automation-fiókról való leválasztáshoz hajtsa végre az alábbi lépéseket.

1. A Azure Portal az **Automation-fiókok hez.**

2. Nyissa meg Automation-fiókját, és válassza a csatolt **munkaterületet** a kapcsolódó **erőforrások** alatt a bal oldalon.

3. A Munkaterület **leválasztása lapon** válassza a **Munkaterület leválasztása** lehetőséget, és válaszoljon a kérésekre.

   ![Munkaterület leválasztása lap](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Miközben megkísérli leválasztni a Log Analytics-munkaterületet, a menü Értesítések menüpontjában nyomon **követheti** a folyamat előrehaladását.

A munkaterületről való leválasztáshoz hajtsa végre a következő lépéseket.

1. A Azure Portal Log **Analytics-munkaterületek lapra.**

2. A munkaterületen válassza az **Automation-fiók lehetőséget a** **Kapcsolódó erőforrások alatt.**

3. Az Automation-fiók lapon válassza a **Fiók leválasztása** lehetőséget, és válaszoljon a kérésekre.

Miközben megkísérli leválasztni az Automation-fiókot, a menü Értesítések területén nyomon **követheti** a folyamat előrehaladását.

### <a name="step-3-delete-automation-account"></a>3. lépés Automation-fiók törlése

Miután sikeresen leválasztotta az Automation-fiókot a munkaterületről, hajtsa végre a különálló [Automation-fiók](#delete-a-standalone-automation-account) szakaszban található lépéseket a fiók törléséhez.

## <a name="next-steps"></a>Következő lépések

Automation-fiók létrehozásához a Azure Portal lásd: Önálló fiók [Azure Automation létrehozása.](automation-create-standalone-account.md) Ha sablon használatával szeretné létrehozni a fiókját, tekintse meg az [Automation-fiók sablonnal való Azure Resource Manager lásd:](quickstart-create-automation-account-template.md).
