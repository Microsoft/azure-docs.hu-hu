---
title: Gépek konfigurálása a kívánt állapotba Azure Automation
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja a gépeket a kívánt állapotba Azure Automation állapot konfigurációjának használatával.
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: f16db3f55ebd0f09e4d7b75750fa319daf03977e
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99053567"
---
# <a name="configure-machines-to-a-desired-state"></a>Gépek célállapotba történő konfigurálása

Azure Automation állapot-konfiguráció lehetővé teszi a kiszolgálók konfigurációjának megadását, és biztosítja, hogy ezek a kiszolgálók a megadott állapotban legyenek az idő múlásával.

> [!div class="checklist"]
> - Azure Automation DSC által felügyelni kívánt virtuális gép előkészítése
> - Konfiguráció feltöltése Azure Automationre
> - Konfiguráció fordítása csomópont-konfigurációban
> - Csomópont-konfiguráció társítása felügyelt csomóponthoz
> - Felügyelt csomópont megfelelőségi állapotának ellenőrzése

Ebben az oktatóanyagban egy egyszerű DSC- [konfigurációt](/powershell/scripting/dsc/configurations/configurations) használunk, amely biztosítja, hogy az IIS telepítve legyen a virtuális gépen.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure Automation-fiókra. Az Automation-fiók és a hozzá tartozó követelmények megismeréséhez tekintse meg az [Automation-fiók hitelesítésének áttekintése](./automation-security-overview.md)című témakört.
- Egy Azure Resource Manager Windows Server 2008 R2 vagy újabb rendszert futtató virtuális gép (klasszikus). A virtuális gépek létrehozásával kapcsolatos utasításokért lásd: az [első Windows rendszerű virtuális gép létrehozása a Azure Portal](../virtual-machines/windows/quick-create-portal.md).
- Azure PowerShell modul 3,6-es vagy újabb verziója. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/azurerm/install-azurerm-ps) ismertető cikket.
- A kívánt állapot-konfiguráció (DSC) ismerete. A DSC-vel kapcsolatos információkért lásd: a [Windows PowerShell kívánt állapot konfigurációjának áttekintése](/powershell/scripting/dsc/overview/overview).

## <a name="support-for-partial-configurations"></a>Részleges konfigurációk támogatása

Azure Automation állapot-konfiguráció támogatja a [részleges konfigurációk](/powershell/scripting/dsc/pull-server/partialconfigs)használatát. Ebben az esetben a DSC-t úgy konfigurálták, hogy egymástól függetlenül kezelhesse a beállításokat, és minden egyes konfigurációt beolvas a Azure Automationból. Az Automation-fiókok esetében azonban csak egy konfigurációt lehet hozzárendelni. Ez azt jelenti, hogy ha két konfigurációt használ egy csomóponthoz, két Automation-fiókra lesz szüksége.

A részleges konfiguráció lekéréses szolgáltatásból való regisztrálásával kapcsolatos részletekért tekintse meg a [részleges konfigurációk](/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode)dokumentációját.

További információ arról, hogy a csapatok hogyan működhetnek együtt a kiszolgálók és a konfiguráció kód használatával történő kezelésével: [a DSC szerepének ismertetése CI/CD-folyamatokban](/powershell/scripting/dsc/overview/authoringadvanced).

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a [AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) parancsmaggal, és kövesse a képernyőn megjelenő utasításokat.

```powershell
Connect-AzAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Konfiguráció létrehozása és feltöltése Azure Automation

Egy szövegszerkesztőben írja be a következőt, és mentse helyileg **TestConfig.ps1ként**.

```powershell
configuration TestConfig {
   Node WebServer {
      WindowsFeature IIS {
         Ensure               = 'Present'
         Name                 = 'Web-Server'
         IncludeAllSubFeature = $true
      }
   }
}
```

> [!NOTE]
> A fejlettebb forgatókönyvek esetében, ahol több, a DSC-erőforrásokat biztosító modult kell importálnia, győződjön meg arról, hogy minden modul rendelkezik egyedi `Import-DscResource` vonallal a konfigurációban.

Hívja meg az [import-AzAutomationDscConfiguration](/powershell/module/Az.Automation/Import-AzAutomationDscConfiguration) parancsmagot, hogy feltöltse a konfigurációt az Automation-fiókjába.

```powershell
 Import-AzAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Konfiguráció fordítása csomópont-konfigurációban

A DSC-konfigurációt egy csomópont-konfigurációba kell lefordítani, mielőtt hozzá lehetne rendelni egy csomóponthoz. Lásd: [DSC-konfigurációk](/powershell/scripting/dsc/configurations/configurations).

Hívja meg a [Start-AzAutomationDscCompilationJob](/powershell/module/Az.Automation/Start-AzAutomationDscCompilationJob) parancsmagot, hogy a `TestConfig` konfigurációt egy Automation- `TestConfig.WebServer` fiókban elnevezett csomópont-konfigurációba fordítsa.

```powershell
Start-AzAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>A virtuális gép regisztrálása az állapot-konfigurációval felügyelhető

Azure Automation állapot-konfigurációval felügyelheti az Azure-beli virtuális gépeket (a klasszikus és a Resource Managert is), a helyszíni virtuális gépeket, a linuxos gépeket, az AWS-alapú virtuális gépeket és a helyszíni fizikai gépeket. Ebben a témakörben bemutatjuk, hogyan regisztrálhat csak Azure Resource Manager virtuális gépeket. További információ az egyéb típusú gépek regisztrálásáról: [bevezetési gépek felügyeletre Azure Automation állapot-konfiguráció alapján](automation-dsc-onboarding.md).

A [Register-AzAutomationDscNode](/powershell/module/Az.Automation/Register-AzAutomationDscNode) parancsmag meghívásával regisztrálja a virtuális gépet felügyelt csomópontként Azure Automation állapot-konfigurációval. 

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

### <a name="specify-configuration-mode-settings"></a>Konfigurációs mód beállításainak megadása

A [Register-AzAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) parancsmag használatával regisztrálja a virtuális gépet felügyelt csomópontként, és határozza meg a konfigurációs tulajdonságokat. Megadhatja például, hogy a gép állapota csak egyszer legyen alkalmazva a tulajdonság értékének megadásával `ApplyOnly` `ConfigurationMode` . Az állapot-konfiguráció nem próbálja alkalmazni a konfigurációt a kezdeti vizsgálat után.

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Azt is megadhatja, hogy a DSC milyen gyakran ellenőrizze a konfigurációs állapotot a `ConfigurationModeFrequencyMins` tulajdonság használatával. A DSC konfigurációs beállításaival kapcsolatos további információkért lásd: [a helyi Configuration Manager konfigurálása](/powershell/scripting/dsc/managing-nodes/metaConfig).

```powershell
# Run a DSC check every 60 minutes
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Csomópont-konfiguráció társítása felügyelt csomóponthoz

Most hozzárendelheti a lefordított csomópont-konfigurációt a konfigurálni kívánt virtuális géphez.

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Ez hozzárendeli a nevű csomópont-konfigurációt `TestConfig.WebServer` a regisztrált DSC-csomóponthoz `DscVm` . Alapértelmezés szerint a DSC-csomópont 30 percenként be van jelölve a csomópont-konfigurációnak való megfeleléshez. További információ a megfelelőségi ellenőrzési időköz módosításáról: [a helyi Configuration Manager konfigurálása](/powershell/scripting/dsc/managing-nodes/metaConfig).

## <a name="check-the-compliance-status-of-a-managed-node"></a>Felügyelt csomópont megfelelőségi állapotának ellenőrzése

A [Get-AzAutomationDscNodeReport](/powershell/module/Az.Automation/Get-AzAutomationDscNodeReport) parancsmag használatával jelentéseket kaphat a felügyelt csomópontok megfelelőségi állapotáról.

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="remove-nodes-from-service"></a>Csomópontok eltávolítása a szolgáltatásból

Ha csomópontot ad hozzá Azure Automation állapot-konfigurációhoz, a helyi Configuration Manager beállításai a szolgáltatásba való regisztrálásra, valamint a gép konfigurálásához szükséges modulok és a szükséges modulok beállítására vannak beállítva.
Ha úgy dönt, hogy eltávolítja a csomópontot a szolgáltatásból, ezt a Azure Portal vagy az az parancsmagok használatával teheti meg.

> [!NOTE]
> A csomópontok szolgáltatásból való regisztrációjának törlése csak a helyi Configuration Manager beállításokat állítja be, így a csomópont már nem csatlakozik a szolgáltatáshoz.
> Ez nem befolyásolja a csomóponton jelenleg alkalmazott konfigurációt.
> Az aktuális konfiguráció eltávolításához használja a [PowerShellt](/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument) , vagy törölje a helyi konfigurációs fájlt (ez az egyetlen lehetőség a Linux-csomópontok számára).

### <a name="azure-portal"></a>Azure Portal

Azure Automation a tartalomjegyzékben kattintson az **állapot konfigurálása (DSC)** elemre.
Ezután kattintson a **csomópontokra** a szolgáltatásban regisztrált csomópontok listájának megtekintéséhez.
Kattintson az eltávolítani kívánt csomópont nevére.
A megnyíló csomópont nézetben kattintson a **Regisztráció törlése** elemre.

### <a name="powershell"></a>PowerShell

Ha meg szeretné szüntetni a csomópont regisztrációját Azure Automation állapot-konfigurációs szolgáltatásból a PowerShell használatával, kövesse a következő parancsmag dokumentációját: [unregister-AzAutomationDscNode](/powershell/module/az.automation/unregister-azautomationdscnode).

## <a name="next-steps"></a>Következő lépések

- Első lépésként tekintse meg [az Azure Automation állapot konfigurációjának első lépései](automation-dsc-getting-started.md)című témakört.
- A csomópontok engedélyezésével kapcsolatos további információkért lásd: [Azure Automation állapot konfigurációjának engedélyezése](automation-dsc-onboarding.md).
- Ha szeretne többet megtudni a DSC-konfigurációk fordításáról, hogy hozzá lehessen rendelni őket a célcsoportokhoz, tekintse meg [a DSC-konfigurációk fordítása Azure Automation állapot konfigurációjában](automation-dsc-compile.md)című témakört.
- Ha szeretné megtekinteni a Azure Automation állapot konfigurációjának folyamatos üzembe helyezési folyamatban való használatát, tekintse [meg a folyamatos üzembe helyezés a chocolatey](automation-dsc-cd-chocolatey.md)használatával című témakört.
- A díjszabással kapcsolatos információkért lásd: [Azure Automation állapot konfigurációjának díjszabása](https://azure.microsoft.com/pricing/details/automation/).
- A PowerShell-parancsmagok leírása: [az. Automation](/powershell/module/az.automation).
