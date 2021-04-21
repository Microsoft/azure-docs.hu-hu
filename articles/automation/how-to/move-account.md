---
title: Saját Azure Automation áthelyezése másik előfizetésbe
description: Ez a cikk bemutatja, hogyan helyezze át Automation-fiókját egy másik előfizetésbe.
services: automation
ms.subservice: process-automation
ms.date: 01/07/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1fd9350baf1805c0e6278b6210ad9818fa18c8d8
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831491"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Saját Azure Automation áthelyezése másik előfizetésbe

Azure Automation lehetővé teszi egyes erőforrások áthelyezését egy új erőforráscsoportba vagy előfizetésbe. Az erőforrásokat áthelyezheti a Azure Portal, a PowerShell, az Azure CLI vagy a REST API. További információ a folyamatról: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe.](../../azure-resource-manager/management/move-resource-group-and-subscription.md)

Az Automation-fiók az egyik áthelyezheti erőforrás. Ebből a cikkből megtudhatja, hogyan helyezze át az Automation-fiókokat egy másik erőforrásba vagy előfizetésbe. Az Automation-fiók áthelyezésének magas szintű lépései a következőek:

1. Tiltsa le a funkciókat.
2. A munkaterület leválasztása.
3. Helyezze át az Automation-fiókot.
4. Törölje, majd hozza létre újra a futtatott fiókokat.
5. Engedélyezze újra a funkciókat.

## <a name="remove-features"></a>Szolgáltatások eltávolítása

Ahhoz, hogy leválasztsa a munkaterületet az Automation-fiókról, el kell távolítania a szolgáltatáserőforrásokat a munkaterületről:

- Change Tracking és Inventory
- Frissítéskezelés
- Virtuális gépek indítása és leállítása munkaidőn kívül

1. Keresse meg az erőforráscsoportot az Azure Portalon.
2. Keresse meg az egyes funkciókat, majd válassza a **Törlés** lehetőséget az **Erőforrások törlése** lapon.

    ![Képernyőkép a szolgáltatáserőforrások törléséről a Azure Portal](../media/move-account/delete-solutions.png)

Ha szeretné, törölheti az erőforrásokat a [Remove-AzResource](/powershell/module/Az.Resources/Remove-AzResource) parancsmag használatával:

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="remove-alert-rules-for-startstop-vms-during-off-hours"></a>Riasztási szabályok eltávolítása a Start/Stop VMs during off-hours

A Start/Stop VMs during off-hours a funkció által létrehozott riasztási szabályokat is el kell távolítania.

1. A Azure Portal az erőforráscsoportban válassza a **Figyelési**  >  **riasztások**  >  **Riasztási szabályok kezelése lehetőséget.**

   ![A Riasztások lap képernyőképe, a Riasztási szabályok kezelése kiválasztásával](../media/move-account/alert-rules.png)

2. A Szabályok lapon meg kell jelenni az adott erőforráscsoportban konfigurált riasztások listájának. A funkció a következő szabályokat hozza létre:

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. Válassza ki egyes alkalommal a szabályokat, majd válassza a **Törlés lehetőséget** az eltávolításukhoz.

    ![Képernyőkép a Szabályok lapról, amely a kijelölt szabályok törlésének megerősítését kéri](../media/move-account/delete-rules.png)

    > [!NOTE]
    > Ha nem lát riasztási szabályokat a Szabályok lapon, módosítsa az Állapot mezőt **Letiltottra** a letiltott riasztások megjelenítése érdekében.  

4. A riasztási szabályok eltávolításakor el kell távolítania a riasztási értesítésekhez létrehozott Start/Stop VMs during off-hours csoportot. A Azure Portal a **Riasztások**  >  **figyelése**  >  **Műveletcsoportok kezelése lehetőséget.**

5. Válassza a **StartStop_VM_Notification** lehetőséget. 

6. A műveletcsoport oldalán válassza a Törlés **lehetőséget.**

    ![Képernyőkép a Műveletcsoport lapról](../media/move-account/delete-action-group.png)

Ha szeretné, törölheti a műveletcsoportot a [Remove-AzActionGroup](/powershell/module/az.monitor/remove-azactiongroup) parancsmag használatával:

```azurepowershell-interactive
Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>A munkaterület leválasztása

Most már leválaszthatja a munkaterületet:

1. A munkaterületen Azure Portal **Automation-fiókhoz**  >  **kapcsolódó erőforrások csatolt**  >  **munkaterületet.** 

2. Válassza **a Munkaterület leválasztása** lehetőséget, hogy leválasztsa a munkaterületet az Automation-fiókról.

    ![Képernyőkép a munkaterület Automation-fiókról való leválasztásról](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Automation-fiók áthelyezése

Most már áthelyezheti az Automation-fiókot és annak runbookját. 

1. A Azure Portal keresse meg az Automation-fiók erőforráscsoportját. Válassza **az Áthelyezés másik**  >  **előfizetésbe lehetőséget.**

    ![Képernyőkép az Erőforráscsoport lapról, áthelyezés másik előfizetésbe](../media/move-account/move-resources.png)

2. Válassza ki az áthelyezni kívánt erőforrásokat az erőforráscsoportban. Győződjön meg arról, hogy tartalmazza az Automation-fiókot, a runbookokat és a Log Analytics-munkaterület erőforrásait.

## <a name="re-create-run-as-accounts"></a>Futtatott fiókok újra létrehozása

[A futtatott fiókok](../automation-security-overview.md#run-as-accounts) létrehoznak egy szolgáltatásnév-Azure Active Directory azure-erőforrásokkal való hitelesítéshez. Az előfizetések módosításakor az Automation-fiók már nem használja a meglévő run as fiókot. A run as fiókok újra létrehozásához:

1. Az új előfizetésben válassza ki Automation-fiókját, és válassza a **Fiókbeállítások** alatt a Futtatás **fiókként lehetőséget.** Látni fogja, hogy a futó fiókok most hiányosként látszanának.

    ![A hiányosan látható, futó fiókok képernyőképe](../media/move-account/run-as-accounts.png)

2. A Tulajdonságok lap Törlés lehetőségének kiválasztásával  egyszerre csak egyszer törölje a **fiókokat.** 

    > [!NOTE]
    > Ha nem rendelkezik a szükséges engedélyekkel a futtatott fiókok létrehozásához vagy megtekintéséhez, a következő üzenet jelenik meg: További információ: A futó fiókok konfigurálásához szükséges `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` [engedélyek.](../automation-security-overview.md#permissions)

3. A run as fiókok törlése után válassza a **Létrehozás** lehetőséget az **Azure-beli fiók alatt.** 

4. Az Azure-beli run as-fiók hozzáadása lapon válassza a **Létrehozás** lehetőséget a futtatás fiók és a szolgáltatásnév létrehozásához. 

5. Ismételje meg a fenti lépéseket a klasszikus Azure-beli futtatás fiókjával.

## <a name="enable-features"></a>Funkciók engedélyezése

A run as fiókok újra létrehozása után újra engedélyeznie kell az áthelyezés előtt eltávolított szolgáltatásokat:

1. A fiók változáskövetés és leltározás **Automation-változáskövetés és leltározás** válassza a megfelelő 5000 000 000 000 000 000 000 000 000 000 Válassza ki az áthelyezni kívánt Log Analytics-munkaterületet, majd válassza az **Engedélyezés lehetőséget.**

2. Ismételje meg az 1. lépést Update Management.

    ![Képernyőkép az átvitt Automation-fiók funkcióinak újra engedélyezéséről](../media/move-account/reenable-solutions.png)

3. A funkciókkal engedélyezett gépek akkor láthatók, ha csatlakoztatta a meglévő Log Analytics-munkaterületet. A szolgáltatás Start/Stop VMs during off-hours újra engedélyeznie kell. A **Kapcsolódó erőforrások alatt** válassza a **Start/Stop VMs**(Virtuális gépek létrehozása/leállítása) További információ lehetőséget, és engedélyezze a Create (Létrehozás) megoldást az üzembe helyezés  >    >   elkezdéséhez.

4. A Megoldás hozzáadása lapon válassza ki a Log Analytics-munkaterületet és az Automation-fiókot.

    ![A Megoldás hozzáadása menü képernyőképe](../media/move-account/add-solution-vm.png)

5. Konfigurálja a szolgáltatást az [áttekintésben Start/Stop VMs during off-hours leírtak szerint.](../automation-solution-vm-management.md)

## <a name="verify-the-move"></a>Az áthelyezés ellenőrzése

Az áthelyezés befejezése után ellenőrizze, hogy az alább felsorolt képességek engedélyezve vannak-e. 

|Képesség|Tesztek|Hibaelhárítás|
|---|---|---|
|Runbookok|A runbookok sikeresen futtathatók, és csatlakozhatnak az Azure-erőforrásokhoz.|[Runbookok hibaelhárítása](../troubleshoot/runbooks.md)
|Verziókövetés|Manuális szinkronizálást is futtathat a forrásvezérlő adattáron.|[Verziókövetés integrálása](../source-control-integration.md)|
|Változáskövetés és leltár|Ellenőrizze, hogy látja-e a gépek aktuális leltáradatát.|[A change tracking hibaelhárítása](../troubleshoot/change-tracking.md)|
|Frissítéskezelés|Ellenőrizze, hogy látja-e a gépeket, és hogy kifogástalan állapotúak-e.</br>Futtatassa a szoftverfrissítések központi telepítésének tesztelését.|[Frissítéskezelés hibaelhárítása](../troubleshoot/update-management.md)|
|Megosztott erőforrások|Ellenőrizze, hogy látja-e az összes megosztott erőforrást, például a [hitelesítő adatokat](../shared-resources/credentials.md) [és a változókat.](../shared-resources/variables.md)|

## <a name="next-steps"></a>Következő lépések

Az azure-beli erőforrások áthelyezését lásd: [Erőforrások áthelyezése az Azure-ban.](../../azure-resource-manager/management/move-support-resources.md)
