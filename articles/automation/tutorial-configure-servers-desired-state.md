---
title: Gépek célállapotba konfigurálása a Azure Automation
description: Ez a cikk bemutatja, hogyan konfigurálhatja a gépeket célállapotra a Azure Automation State Configuration.
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 08/08/2018
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 70631490e248c801b9465c51548efe8dff704a3e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830141"
---
# <a name="configure-machines-to-a-desired-state"></a>Gépek célállapotba történő konfigurálása

Azure Automation State Configuration megadhatja a kiszolgálók konfigurációit, és biztosíthatja, hogy ezek a kiszolgálók a megadott állapotban vannak az idő során.

> [!div class="checklist"]
> - DSC által felügyelt virtuális gép Azure Automation üzembe
> - Konfiguráció feltöltése a Azure Automation
> - Konfiguráció lefordítása csomópont-konfigurációra
> - Csomópont-konfiguráció hozzárendelése felügyelt csomóponthoz
> - Felügyelt csomópont megfelelőségi állapotának ellenőrzése

Ebben az oktatóanyagban egy egyszerű [DSC-konfigurációt](/powershell/scripting/dsc/configurations/configurations) használunk, amely biztosítja, hogy az IIS telepítve van a virtuális gépen.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure Automation-fiókra. Az Automation-fiókkal és annak követelményeivel kapcsolatos további információkért lásd: [Automation-fiók hitelesítésének áttekintése.](./automation-security-overview.md)
- Egy Azure Resource Manager Windows Server 2008 R2 vagy újabb rendszert futtató virtuális gép (nem klasszikus). A virtuális gépek létrehozásával vonatkozó utasításokért lásd: Az első Windows rendszerű virtuális gép létrehozása a [Azure Portal.](../virtual-machines/windows/quick-create-portal.md)
- Azure PowerShell modul 3.6-os vagy újabb verziója. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/azurerm/install-azurerm-ps) ismertető cikket.
- A Desired State Configuration (DSC) ismerete. További információ a DSC-ről: [Windows PowerShell Desired State Configuration áttekintése.](/powershell/scripting/dsc/overview/overview)

## <a name="support-for-partial-configurations"></a>Részleges konfigurációk támogatása

Azure Automation State Configuration támogatja a részleges [konfigurációk használatát.](/powershell/scripting/dsc/pull-server/partialconfigs) Ebben a forgatókönyvben a DSC több konfiguráció egymástól függetlenül történő kezelésére van konfigurálva, és mindegyik konfigurációt a rendszer a Azure Automation. Automation-fiókonként azonban egy csomóponthoz csak egy konfiguráció rendelhető hozzá. Ez azt jelenti, hogy ha két konfigurációt használ egy csomóponthoz, két Automation-fiókra lesz szüksége.

A részleges konfigurációk lekért szolgáltatásból való regisztrálásának részleteiért tekintse meg a részleges konfigurációk [dokumentációját.](/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode)

További információ arról, hogyan dolgozhatnak együtt a csapatok a kiszolgálók közös kezelésén kódként konfigurált konfiguráció használatával: [Understanding DSC's role in a CI/CD Pipeline](/powershell/scripting/dsc/overview/authoringadvanced)(A DSC CI/CD-folyamatokban betöltött szerepének ismertetése).

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) parancsmaggal, és kövesse a képernyőn megjelenő utasításokat.

```powershell
Connect-AzAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Konfiguráció létrehozása és feltöltése a Azure Automation

Írja be a következőt egy szövegszerkesztőbe, és mentse helyileg az **TestConfig.ps1.**

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
> Összetettebb forgatókönyvekben, ahol több olyan modul importálására van szükség, amelyek DSC-erőforrásokat biztosítanak, győződjön meg arról, hogy minden modul egyedi vonallal rendelkezik `Import-DscResource` a konfigurációban.

Az [Import-AzAutomationDscConfiguration](/powershell/module/Az.Automation/Import-AzAutomationDscConfiguration) parancsmag hívásával töltse fel a konfigurációt az Automation-fiókjába.

```powershell
 Import-AzAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Konfiguráció fordítása csomópont-konfigurációba

Ahhoz, hogy csomóponthoz rendelhető legyen, a DSC-konfigurációt csomópont-konfigurációra kell fordítani. Lásd: [DSC-konfigurációk.](/powershell/scripting/dsc/configurations/configurations)

A [Start-AzAutomationDscCompilationJob](/powershell/module/Az.Automation/Start-AzAutomationDscCompilationJob) parancsmag hívásával fordítsa le a konfigurációt egy nevű csomópont-konfigurációra az `TestConfig` `TestConfig.WebServer` Automation-fiókjában.

```powershell
Start-AzAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Virtuális gép regisztrálása a State Configuration

A virtuális Azure Automation State Configuration (klasszikus és Resource Manager), helyszíni virtuális gépeket, Linux rendszerű gépeket, AWS virtuális gépeket és helyszíni fizikai gépeket is kezelhet. Ebben a témakörben azt fedjük le, hogyan regisztrálhat csak Azure Resource Manager virtuális gépeket. További információ más típusú gépek regisztrálásról: [Gépek](automation-dsc-onboarding.md)regisztrálása felügyeletre a Azure Automation State Configuration.

Hívja meg a [Register-AzAutomationDscNode](/powershell/module/Az.Automation/Register-AzAutomationDscNode) parancsmagot a virtuális gép felügyelt csomópontként való Azure Automation State Configuration regisztráláshoz. 

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

### <a name="specify-configuration-mode-settings"></a>Konfigurációs mód beállításainak megadása

A [Register-AzAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) parancsmaggal regisztrálhat egy virtuális gépet felügyelt csomópontként, és megadhatja a konfigurációs tulajdonságokat. Megadhatja például, hogy a gép állapota csak egyszer legyen alkalmazva, ha megadja a tulajdonság `ApplyOnly` `ConfigurationMode` értékét. State Configuration a kezdeti ellenőrzés után nem próbálja meg alkalmazni a konfigurációt.

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Azt is megadhatja, hogy a DSC milyen gyakran ellenőrzi a konfigurációs állapotot a tulajdonság `ConfigurationModeFrequencyMins` használatával. További információ a DSC konfigurációs beállításairól: [Configuring the Local Konfigurációkezelő](/powershell/scripting/dsc/managing-nodes/metaConfig).

```powershell
# Run a DSC check every 60 minutes
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Csomópont-konfiguráció hozzárendelése felügyelt csomóponthoz

Most hozzárendelheti a lefordított csomópont konfigurációját a konfigurálni kívánt virtuális géphez.

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Ez hozzárendeli a nevű csomópontkonfigurációt a `TestConfig.WebServer` regisztrált DSC-csomóponthoz. `DscVm` Alapértelmezés szerint a rendszer 30 percenként ellenőrzi, hogy a DSC-csomópont megfelel-e a csomópont konfigurációjának. A megfelelőség-ellenőrzés időközének beállításával kapcsolatos információkért lásd: [Configuring the Local Konfigurációkezelő](/powershell/scripting/dsc/managing-nodes/metaConfig).

## <a name="check-the-compliance-status-of-a-managed-node"></a>Felügyelt csomópont megfelelőségi állapotának ellenőrzése

A felügyelt csomópontok megfelelőségi állapotáról a [Get-AzAutomationDscNodeReport parancsmag](/powershell/module/Az.Automation/Get-AzAutomationDscNodeReport) használatával kaphat jelentéseket.

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="remove-nodes-from-service"></a>Csomópontok eltávolítása a szolgáltatásból

Amikor csomópontot ad hozzá a Azure Automation State Configuration, a Local Konfigurációkezelő beállításai úgy vannak beállítva, hogy regisztrálja magát a szolgáltatásban, valamint lekért konfigurációkat és a gép konfigurálásához szükséges modulokat.
Ha úgy dönt, hogy eltávolítja a csomópontot a szolgáltatásból, ezt az Azure Portal vagy az Az parancsmagok használatával is megadhatja.

> [!NOTE]
> A csomópont szolgáltatásból való regisztrációjának a regisztrációja csak a Helyi Konfigurációkezelő beállításait állítja be, így a csomópont már nem csatlakozik a szolgáltatáshoz.
> Ez nem befolyásolja a csomóponton aktuálisan alkalmazott konfigurációt.
> Az aktuális konfiguráció eltávolításához használja a [PowerShellt,](/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument) vagy törölje a helyi konfigurációs fájlt (linuxos csomópontok esetén ez az egyetlen lehetőség).

### <a name="azure-portal"></a>Azure Portal

A Azure Automation a tartalomjegyzékben kattintson az Állapotkonfiguráció **(DSC)** elemre.
Ezután kattintson **a Csomópontok** elemre a szolgáltatásban regisztrált csomópontok listájának megtekintéséhez.
Kattintson az eltávolítani kívánt csomópont nevére.
A megnyíló Csomópont nézetben kattintson a **Regisztrációjának törlése elemre.**

### <a name="powershell"></a>PowerShell

Ha egy csomópont regisztrációját meg Azure Automation State Configuration szolgáltatásból a PowerShell használatával, kövesse a [Unregister-AzAutomationDscNode](/powershell/module/az.automation/unregister-azautomationdscnode)parancsmag dokumentációját.

## <a name="next-steps"></a>Következő lépések

- Első lépések: [Első lépések a Azure Automation State Configuration.](automation-dsc-getting-started.md)
- További információ a csomópontok engedélyezéséről: [Enable Azure Automation State Configuration.](automation-dsc-onboarding.md)
- A DSC-konfigurációk célcsomópontokhoz való hozzárendelése érdekében való fordításával kapcsolatos további információkért lásd: DSC-konfigurációk fordítása a [Azure Automation State Configuration.](automation-dsc-compile.md)
- A folyamatos üzembe helyezési folyamatban Azure Automation State Configuration példát a Folyamatos üzembe helyezés beállítása a [Chocolatey használatával.](automation-dsc-cd-chocolatey.md)
- Díjszabási információkért lásd: [Azure Automation State Configuration díjszabása.](https://azure.microsoft.com/pricing/details/automation/)
- A PowerShell-parancsmagok referenciáját lásd: [Az.Automation](/powershell/module/az.automation).
