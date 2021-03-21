---
title: DSC-konfigurációk fordítása Azure Automation állapot-konfigurációban
description: Ez a cikk azt ismerteti, hogyan lehet lefordítani a Azure Automation kívánt állapot-konfiguráció (DSC) konfigurációit.
services: automation
ms.subservice: dsc
ms.date: 04/06/2020
ms.topic: conceptual
ms.openlocfilehash: b45aad71e04418c7c7dda4fc3f0c84a5fe99ecdf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896342"
---
# <a name="compile-dsc-configurations-in-azure-automation-state-configuration"></a>DSC-konfigurációk fordítása Azure Automation állapot-konfigurációban

A kívánt állapot-konfiguráció (DSC) konfigurációit a következő módokon lehet lefordítani Azure Automation állapot konfigurációjában:

- Azure State Configuration fordítási szolgáltatás
  - Kezdő módszer interaktív felhasználói felülettel
   - A feladatok állapotának egyszerű nyomon követése

- Windows PowerShell
  - Hívás a Windows PowerShellből helyi munkaállomáson vagy Build szolgáltatáson
  - Integrálás fejlesztési tesztelési folyamattal
  - Összetett paraméterek értékeinek megadása
  - Csomópont-és nem csomópontos adatmennyiség használata nagy méretekben
  - Jelentős teljesítmény-javulás

Azure Resource Manager sablonokat az Azure desired State Configuration (DSC) bővítménnyel is használhatja a konfigurációk Azure-beli virtuális gépekre történő leküldéséhez. Az Azure DSC bővítmény az Azure virtuálisgép-ügynök keretrendszerét használja az Azure-beli virtuális gépeken futó DSC-konfigurációk továbbítására, meghozatalára és jelentésére. Azure Resource Manager sablonok használatával történő fordítás részleteiért lásd: [a kívánt állapot konfigurációs bővítménye Azure Resource Manager-sablonokkal](../virtual-machines/extensions/dsc-template.md#details). 

## <a name="compile-a-dsc-configuration-in-azure-state-configuration"></a>DSC-konfiguráció fordítása az Azure állapot-konfigurációjában

### <a name="portal"></a>Portál

1. Az Automation-fiókban kattintson az **állapot konfigurálása (DSC)** elemre.
1. Kattintson a **konfigurációk** fülre, majd a fordításhoz kattintson a konfiguráció nevére.
1. Kattintson a **fordítás** gombra.
1. Ha a konfiguráció nem rendelkezik paraméterekkel, a rendszer felszólítja, hogy erősítse meg, hogy szeretné-e lefordítani. Ha a konfiguráció paraméterekkel rendelkezik, megnyílik a **fordítási konfiguráció** panel, ahol megadhatja a paraméterek értékét.
1. Megnyílik a fordítási feladatok lap, amelyen nyomon követheti a fordítási feladatok állapotát. Ezen a lapon nyomon követheti a csomópont-konfigurációkat (MOF konfigurációs dokumentumokat), amelyek a Azure Automation állapot-konfiguráció lekérési kiszolgálóján vannak elhelyezve.

### <a name="azure-powershell"></a>Azure PowerShell

A [Start-AzAutomationDscCompilationJob](/powershell/module/az.automation/start-azautomationdsccompilationjob) segítségével megkezdheti a fordítást a Windows PowerShell használatával. Az alábbi mintakód megkezdi a **SampleConfig** nevű DSC-konfiguráció fordítását.

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzAutomationDscCompilationJob` egy fordítási feladattípust ad vissza, amelyet a feladatok állapotának nyomon követésére használhat. Ezt követően ezt a fordítási feladatot a [Get-AzAutomationDscCompilationJob](/powershell/module/az.automation/get-azautomationdsccompilationjob) használatával határozhatja meg a fordítási feladatok állapotát, és a [Get-AzAutomationDscCompilationJobOutput](/powershell/module/az.automation/get-azautomationdscconfiguration) segítségével megtekintheti a streameket (kimenet). Az alábbi példa elindítja a SampleConfig-konfiguráció összeállítását, megvárja, amíg befejeződik, majd megjeleníti a folyamait.

```powershell
$CompilationJob = Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzAutomationDscCompilationJobOutput –Stream Any
```

### <a name="declare-basic-parameters"></a>Alapszintű paraméterek deklarálása

A DSC-konfigurációk paramétereinek deklarációja, beleértve a paramétereket és a tulajdonságokat, ugyanúgy működik, mint a Azure Automation runbookok. A runbook paraméterekkel kapcsolatos további tudnivalókért tekintse meg a [runbook Megkezdése Azure Automationban](./start-runbooks.md) című témakört.

A következő példa a `FeatureName` és a paramétereket használja a `IsPresent` Tulajdonságok értékének meghatározásához a **ParametersExample. Sample** Node konfigurációban, amely a fordítás során jött létre.

```powershell
Configuration ParametersExample
{
    param(
        [Parameter(Mandatory=$true)]
        [string] $FeatureName,

        [Parameter(Mandatory=$true)]
        [boolean] $IsPresent
    )

    $EnsureString = 'Present'
    if($IsPresent -eq $false)
    {
        $EnsureString = 'Absent'
    }

    Node 'sample'
    {
        WindowsFeature ($FeatureName + 'Feature')
        {
            Ensure = $EnsureString
            Name   = $FeatureName
        }
    }
}
```

Az alapszintű paramétereket használó DSC-konfigurációkat a Azure Automation állapot-konfigurációs portálon vagy a Azure PowerShell használatával is lefordíthatja.

#### <a name="portal"></a>Portál

A portálon a **fordítás** lehetőségre kattintva adhatja meg a paraméterek értékeit.

![Konfigurációs fordítási paraméterek](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

A PowerShell paramétereket igényel a [szórótábla](/powershell/module/microsoft.powershell.core/about/about_hash_tables), ahol a kulcs megegyezik a paraméter nevével, és az érték megegyezik a paraméter értékével.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Az objektumok paraméterként való átadásával kapcsolatos információkért `PSCredential` lásd: [hitelesítőadat-eszközök](#credential-assets).

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Összetett erőforrásokat tartalmazó konfigurációk fordítása Azure Automation

Az **összetett erőforrások** funkció lehetővé teszi, hogy a DSC-konfigurációkat beágyazott erőforrásként használja egy konfiguráción belül. Ez a szolgáltatás lehetővé teszi több konfiguráció alkalmazását egyetlen erőforrásra. Az összetett erőforrásokkal kapcsolatos további információkért tekintse meg az [összetett erőforrások: DSC-konfiguráció használata erőforrásként](/powershell/scripting/dsc/resources/authoringresourcecomposite) című témakört.

> [!NOTE]
> Annak érdekében, hogy az összetett erőforrásokat tartalmazó konfigurációk megfelelően legyenek lefordítva, először importálnia kell a Azure Automation minden olyan DSC-erőforrásra, amelyre a kompozitok támaszkodnak. A DSC összetett erőforrások hozzáadása nem különbözik a PowerShell-modulok Azure Automationhoz való hozzáadásával. Ez a folyamat dokumentálva van a [Azure Automationban található modulok kezelésében](./shared-resources/modules.md).

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>ConfigurationData kezelése a konfigurációk fordításakor Azure Automation

`ConfigurationData` a egy beépített DSC-paraméter, amely lehetővé teszi a strukturális konfiguráció elkülönítését bármely környezet-specifikus konfigurációból a PowerShell DSC használata közben. További információ: a ["mi" a "WHERE" a POWERSHELL DSC-ben való elválasztása](https://devblogs.microsoft.com/powershell/separating-what-from-where-in-powershell-dsc/).

> [!NOTE]
> Azure Automation állapot-konfiguráció összeállításakor a `ConfigurationData` Azure PowerShellban, de nem a Azure Portalban is használható.

A következő példa DSC-konfigurációt használ `ConfigurationData` a `$ConfigurationData` és a kulcsszavak használatával `$AllNodes` . Ehhez a példához a [xWebAdministration modulra](https://www.powershellgallery.com/packages/xWebAdministration/) is szüksége lesz.

```powershell
Configuration ConfigurationDataSample
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

    Write-Verbose $ConfigurationData.NonNodeData.SomeMessage

    Node $AllNodes.Where{$_.Role -eq 'WebServer'}.NodeName
    {
        xWebsite Site
        {
            Name         = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure       = 'Present'
        }
    }
}
```

A korábbi DSC-konfiguráció a Windows PowerShell használatával fordítható le. A következő parancsfájl két csomópont-konfigurációt hoz létre a Azure Automation állapot-konfiguráció lekérési szolgáltatáshoz: **ConfigurationDataSample. MyVM1** és **ConfigurationDataSample. MyVM3**.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = 'MyVM1'
            Role = 'WebServer'
        },
        @{
            NodeName = 'MyVM2'
            Role = 'SQLServer'
        },
        @{
            NodeName = 'MyVM3'
            Role = 'WebServer'
        }
    )

    NonNodeData = @{
        SomeMessage = 'I love Azure Automation State Configuration and DSC!'
    }
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ConfigurationDataSample' -ConfigurationData $ConfigData
```

### <a name="work-with-assets-in-azure-automation-during-compilation"></a>Azure Automation-eszközök használata a fordítás során

Az eszközök hivatkozásai a Azure Automation állapot-konfigurációban és a runbookok egyaránt megegyeznek. További információkat a következő cikkekben talál:

- [Tanúsítványok](./shared-resources/certificates.md)
- [Kapcsolatok](automation-connections.md)
- [Hitelesítő adatok](./shared-resources/credentials.md)
- [Változók](./shared-resources/variables.md)

#### <a name="credential-assets"></a>Hitelesítőadat-eszközök

A Azure Automation DSC-konfigurációk hivatkozhatnak az Automation hitelesítőadat-eszközeire a `Get-AutomationPSCredential` parancsmag használatával. Ha egy konfiguráció egy olyan paramétert tartalmaz, amely egy objektumot határoz meg `PSCredential` , akkor a `Get-AutomationPSCredential` hitelesítő adatok lekéréséhez adja át egy Azure Automation hitelesítőadat-eszköz sztring nevét a parancsmagnak. Ezt követően használja ezt az objektumot az objektumot igénylő paraméterhez `PSCredential` . A színfalak mögött a rendszer beolvassa a Azure Automation hitelesítő adatokat, és átadja a konfigurációnak. Az alábbi példa ezt a forgatókönyvet mutatja be működés közben.

A hitelesítő adatok biztonságos tárolásához a csomópont-konfigurációkban (MOF konfigurációs dokumentumok) a hitelesítő adatok titkosítását igényli a csomópont-konfiguráció MOF-fájljában. Jelenleg a PowerShell DSC-nek engedélyt kell adnia a hitelesítő adatok egyszerű szövegként való kiírására a csomópont-konfiguráció MOF-generálásakor. A PowerShell DSC nem ismeri, hogy Azure Automation egy fordítási feladatokon keresztül titkosítja a teljes MOF-fájlt.

Megadhatja a PowerShell DSC-t, hogy a hitelesítő adatok egyszerű szövegként legyenek leképezve a generált csomópont-konfigurációs MOF a konfigurációs adatok használatával. Az `PSDscAllowPlainTextPassword = $true` `ConfigurationData` összes csomópont-blokk nevét meg kell adni, amely megjelenik a DSC-konfigurációban, és hitelesítő adatokat használ.

Az alábbi példa egy olyan DSC-konfigurációt mutat be, amely egy Automation hitelesítőadat-eszközt használ.

```powershell
Configuration CredentialSample
{
    Import-DscResource -ModuleName PSDesiredStateConfiguration
    $Cred = Get-AutomationPSCredential 'SomeCredentialAsset'

    Node $AllNodes.NodeName
    {
        File ExampleFile
        {
            SourcePath      = '\\Server\share\path\file.ext'
            DestinationPath = 'C:\destinationPath'
            Credential      = $Cred
        }
    }
}
```

Az előző DSC-konfigurációt a PowerShell használatával fordíthatja le. A következő PowerShell-kód két csomópont-konfigurációt hoz létre a Azure Automation állapot-konfiguráció lekérési kiszolgálója számára: **CredentialSample. MyVM1** és **CredentialSample. MyVM2**.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = '*'
            PSDscAllowPlainTextPassword = $True
        },
        @{
            NodeName = 'MyVM1'
        },
        @{
            NodeName = 'MyVM2'
        }
    )
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'CredentialSample' -ConfigurationData $ConfigData
```

> [!NOTE]
> A fordítás befejezésekor előfordulhat, hogy az `The 'Microsoft.PowerShell.Management' module was not imported because the 'Microsoft.PowerShell.Management' snap-in was already imported.` üzenet biztonságos figyelmen kívül hagyása érdekében hibaüzenetet kap.

## <a name="compile-your-dsc-configuration-in-windows-powershell"></a>A DSC-konfiguráció fordítása a Windows PowerShellben

A DSC-konfigurációk Windows PowerShellben való fordításának folyamata tartalmazza a PowerShell DSC dokumentációjának [írását, fordítását és konfigurációjának alkalmazását](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration).
Ezt a folyamatot egy fejlesztői munkaállomáson vagy egy Build szolgáltatáson belül, például az [Azure DevOps](https://dev.azure.com)lehet végrehajtani. Ezután importálhatja a létrehozott MOF-fájlokat a konfiguráció fordításával az Azure állapot-konfigurációs szolgáltatásba.

A Windows PowerShellben való fordítás lehetőséget biztosít a konfigurációs tartalom aláírására is. A DSC-ügynök a felügyelt csomóponton helyileg ellenőrzi az aláírt csomópontok konfigurációját. Az ellenőrzéssel biztosítható, hogy a csomópontra alkalmazott konfiguráció egy jóváhagyott forrásból származik.

Az Azure-on kívül lefordított csomópont-konfigurációk (MOF-fájlok) is importálhatók. Az importálás egy fejlesztői munkaállomás vagy egy szolgáltatás, például az [Azure DevOps](https://dev.azure.com)fordítását tartalmazza. Ennek a megközelítésnek több előnye is van, beleértve a teljesítményt és a megbízhatóságot.

> [!NOTE]
> A csomópont-konfigurációs fájl nem lehet nagyobb, mint 1 MB, hogy a Azure Automation importálni lehessen.

A csomópont-konfigurációk aláírásával kapcsolatos további információkért lásd: [a WMF 5,1-es verziójának fejlesztése – a konfiguráció és a modul aláírása](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations).

### <a name="import-a-node-configuration-in-the-azure-portal"></a>Csomópont-konfiguráció importálása a Azure Portal

1. Az Automation-fiókban kattintson az **állapot konfigurációja (DSC)** elemre a **konfiguráció** felügyelete alatt.
1. Az állapot-konfiguráció (DSC) lapon kattintson a **konfigurációk** fülre, majd a **Hozzáadás** gombra.
1. Az importálás lapon kattintson a **csomópont-konfigurációs fájl** mező melletti mappa ikonra, és keresse meg a csomópont-konfiguráció MOF-fájlját a helyi számítógépen.

   ![Helyi fájl keresése tallózással](./media/automation-dsc-compile/import-browse.png)

1. Adjon meg egy nevet a **konfiguráció neve** mezőben. Ennek a névnek meg kell egyeznie annak a konfigurációnak a nevével, amelyből a csomópont-konfigurációt leállították.
1. Kattintson az **OK** gombra.

### <a name="import-a-node-configuration-with-azure-powershell"></a>Csomópont-konfiguráció importálása Azure PowerShell

Az [import-AzAutomationDscNodeConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) parancsmag használatával importálhat egy csomópont-konfigurációt az Automation-fiókjába.

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Következő lépések

- Első lépésként tekintse meg [az Azure Automation állapot konfigurációjának első lépései](automation-dsc-getting-started.md)című témakört.
- Ha szeretne többet megtudni a DSC-konfigurációk fordításáról, hogy hozzá lehessen rendelni őket a célcsoportokhoz, tekintse meg [a DSC-konfigurációk fordítása Azure Automation állapot konfigurációjában](automation-dsc-compile.md)című témakört.
- A PowerShell-parancsmagok leírása: [az. Automation](/powershell/module/az.automation).
- A díjszabással kapcsolatos információkért lásd: [Azure Automation állapot konfigurációjának díjszabása](https://azure.microsoft.com/pricing/details/automation/).
- Az állapot-konfiguráció folyamatos üzembe helyezési folyamatban való használatának példáját a [folyamatos üzembe helyezés a chocolatey](automation-dsc-cd-chocolatey.md)használatával című témakörben tekintheti meg.
