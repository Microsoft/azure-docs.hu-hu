---
title: Azure Automation-fiók áthelyezése másik előfizetésre
description: Ez a cikk azt ismerteti, hogyan helyezheti át az Automation-fiókját egy másik előfizetésbe.
services: automation
ms.subservice: process-automation
ms.date: 01/07/2021
ms.topic: conceptual
ms.openlocfilehash: a86d876a723c89eb8dcdf18c8318f2a9c740a229
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99051024"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Azure Automation-fiók áthelyezése másik előfizetésre

Azure Automation lehetővé teszi egyes erőforrások áthelyezését egy új erőforráscsoporthoz vagy előfizetésbe. Az erőforrásokat a Azure Portal, a PowerShell, az Azure CLI vagy a REST API használatával helyezheti át. További információ a folyamatról: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

Az Automation-fiók az egyik áthelyezhető erőforrás. Ebből a cikkből megtudhatja, hogyan helyezheti át az Automation-fiókokat egy másik erőforrásba vagy előfizetésbe. Az Automation-fiók áthelyezésének magas szintű lépései a következők:

1. Tiltsa le a szolgáltatásait.
2. Munkaterület leválasztása.
3. Helyezze át az Automation-fiókot.
4. Törölje, majd hozza létre újra a futtató fiókokat.
5. Engedélyezze újra a szolgáltatásokat.

## <a name="remove-features"></a>Szolgáltatások eltávolítása

A munkaterület Automation-fiókból való leválasztásához el kell távolítania a funkció erőforrásait a munkaterületen:

- Change Tracking és Inventory
- Frissítéskezelés
- Virtuális gépek indítása és leállítása munkaidőn kívül

1. Keresse meg az erőforráscsoportot az Azure Portalon.
2. Keresse meg az egyes szolgáltatások elemet, majd válassza a **Törlés** lehetőséget az **erőforrások törlése** lapon.

    ![Képernyőfelvétel a szolgáltatás erőforrásainak törléséről a Azure Portal](../media/move-account/delete-solutions.png)

Ha szeretné, törölheti az erőforrásokat a [Remove-AzResource](/powershell/module/Az.Resources/Remove-AzResource) parancsmag használatával:

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="remove-alert-rules-for-startstop-vms-during-off-hours"></a>Start/Stop VMs during off-hours riasztási szabályainak eltávolítása

Start/Stop VMs during off-hours esetén el kell távolítania a szolgáltatás által létrehozott riasztási szabályokat is.

1. A Azure Portal nyissa meg az erőforráscsoportot, és válassza a **figyelési**  >  **riasztások**  >  **kezelése riasztási szabályokat**.

   ![Képernyőkép a riasztások oldaláról, amely a riasztási szabályok kezelése lehetőséget mutatja](../media/move-account/alert-rules.png)

2. A szabályok lapon az adott erőforráscsoporthoz konfigurált riasztások listája látható. A szolgáltatás létrehozza ezeket a szabályokat:

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. Egyszerre válassza ki a szabályokat, majd a **Törlés** gombra kattintva távolítsa el őket.

    ![Képernyőfelvétel a szabályokról lap a kijelölt szabályok törlésének megerősítését kéri](../media/move-account/delete-rules.png)

    > [!NOTE]
    > Ha nem lát riasztási szabályt a szabályok lapon, a letiltott riasztások megjelenítéséhez módosítsa az **állapot** mezőt **Letiltva** értékre. 

4. A riasztási szabályok eltávolításakor el kell távolítania Start/Stop VMs during off-hours értesítésekhez létrehozott műveleti csoportot. A Azure Portal válassza a riasztások **figyelése**  >    >  **műveleti csoportok kezelése** lehetőséget.

5. Válassza a **StartStop_VM_Notification** lehetőséget. 

6. A műveleti csoport lapon válassza a **Törlés** lehetőséget.

    ![A műveleti csoport oldalának képernyőképe](../media/move-account/delete-action-group.png)

Ha szeretné, törölheti a műveleti csoportot a [Remove-AzActionGroup](/powershell/module/az.monitor/remove-azactiongroup) parancsmag használatával:

```azurepowershell-interactive
Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Munkaterület leválasztása

Most már leválaszthatja a munkaterületet:

1. A Azure Portal válassza az **Automation-fiókhoz**  >  **kapcsolódó erőforrások**  >  **csatolt munkaterület** elemet. 

2. Válassza a **munkaterület** megszüntetése lehetőséget a munkaterület Automation-fiókból való leválasztásához.

    ![Képernyőfelvétel a munkaterület Automation-fiókból való leválasztásáról](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Az Automation-fiók áthelyezése

Most már áthelyezheti az Automation-fiókját és a runbookok is. 

1. A Azure Portal tallózással keresse meg az Automation-fiókja erőforrás-csoportját. Válassza   >  **az áthelyezés másik előfizetésre** lehetőséget.

    ![Képernyőkép az erőforráscsoport oldaláról, áthelyezés másik előfizetésre](../media/move-account/move-resources.png)

2. Válassza ki az áthelyezni kívánt erőforráscsoport erőforrásait. Győződjön meg arról, hogy az Automation-fiókja, a runbookok és a Log Analytics munkaterület erőforrásai is megtalálhatók.

## <a name="re-create-run-as-accounts"></a>Futtató fiókok újbóli létrehozása

A [futtató fiókok](../automation-security-overview.md#run-as-accounts) az Azure-erőforrásokkal való hitelesítéshez Azure Active Directory egyszerű szolgáltatásnevet hoznak létre. Az előfizetések módosításakor az Automation-fiók már nem használja a meglévő futtató fiókot. A futtató fiókok újbóli létrehozása:

1. Nyissa meg az Automation-fiókját az új előfizetésben, és válassza a **fiók beállításai** alatt a **futtató fiókok** elemet. Láthatja, hogy a futtató fiókok már nem teljesek.

    ![Képernyőkép a futtató fiókokról, hiányos megjelenítéssel](../media/move-account/run-as-accounts.png)

2. Törölje a futtató fiókokat egy időben, a **Tulajdonságok** lapon a **Törlés** lehetőség kiválasztásával. 

    > [!NOTE]
    > Ha nem rendelkezik a futtató fiókok létrehozásához vagy megtekintéséhez szükséges engedélyekkel, tekintse meg a következő üzenetet: `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` További információért lásd a [futtató fiókok konfigurálásához szükséges engedélyeket](../automation-security-overview.md#permissions).

3. Miután törölte a futtató fiókokat, válassza a **Létrehozás** az Azure-beli **futtató fiókban** lehetőséget. 

4. Az Azure-beli futtató fiók hozzáadása lapon válassza a **Létrehozás** lehetőséget a futtató fiók és az egyszerű szolgáltatásnév létrehozásához. 

5. Ismételje meg a fenti lépéseket a klasszikus Azure-beli futtató fiókkal.

## <a name="enable-features"></a>Szolgáltatások engedélyezése

A futtató fiókok újbóli létrehozása után újra engedélyeznie kell azokat a szolgáltatásokat, amelyeket az áthelyezés előtt eltávolítottak:

1. A Change Tracking és a leltár bekapcsolásához válassza a **change Tracking és a leltár** elemet az Automation-fiókban. Válassza ki az áthelyezett Log Analytics munkaterületet, majd válassza az **Engedélyezés** lehetőséget.

2. Ismételje meg az 1. lépést Update Management esetén.

    ![Az áthelyezett Automation-fiókban található szolgáltatások ismételt engedélyezését bemutató képernyőkép](../media/move-account/reenable-solutions.png)

3. A szolgáltatásokkal engedélyezett gépek a meglévő Log Analytics munkaterület csatlakoztatásakor láthatók. A Start/Stop VMs during off-hours funkció bekapcsolásához újra engedélyeznie kell azt. A **kapcsolódó erőforrások** területen válassza a **virtuális gépek indítása/leállítása** további  >  **információk és a megoldás** létrehozásának engedélyezése lehetőséget a  >   telepítés elindításához.

4. A megoldás hozzáadása lapon válassza ki a Log Analytics-munkaterületet és az Automation-fiókot.

    ![Képernyőfelvétel a megoldás hozzáadása menüről](../media/move-account/add-solution-vm.png)

5. Konfigurálja a szolgáltatást a [Start/Stop VMS During off-hours áttekintése](../automation-solution-vm-management.md)című témakörben leírtak szerint.

## <a name="verify-the-move"></a>Az áthelyezés ellenőrzése

Az áthelyezés befejeződése után ellenőrizze, hogy az alábbi képességek engedélyezve vannak-e. 

|Képesség|Tesztek|Hibaelhárítás|
|---|---|---|
|Runbookok|Egy runbook sikeresen futtatható és kapcsolódhat az Azure-erőforrásokhoz.|[Runbookok hibaelhárítása](../troubleshoot/runbooks.md)
|Verziókövetés|A verziókövetés adattárán manuális szinkronizálást is futtathat.|[Verziókövetés integrálása](../source-control-integration.md)|
|Változáskövetés és leltár|Ellenőrizze, hogy az aktuális leltári adatok megjelennek-e a gépekről.|[Változások követésének hibakeresése](../troubleshoot/change-tracking.md)|
|Frissítéskezelés|Ellenőrizze, hogy látja-e a gépeket, és hogy kifogástalanok-e.</br>Futtasson egy teszt szoftverfrissítés központi telepítését.|[Az Update Management hibáinak megoldása](../troubleshoot/update-management.md)|
|Megosztott erőforrások|Győződjön meg arról, hogy az összes megosztott erőforrás, például a [hitelesítő adatok](../shared-resources/credentials.md) és a [változók](../shared-resources/variables.md)láthatók.|

## <a name="next-steps"></a>Következő lépések

Az erőforrások Azure-ban való áthelyezésével kapcsolatos további információkért lásd: [erőforrások áthelyezése az Azure-ban](../../azure-resource-manager/management/move-support-resources.md).
