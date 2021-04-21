---
title: DSC-konfigurációk fordítása a Azure Automation State Configuration
description: Ez a cikk bemutatja, hogyan fordítsa Desired State Configuration (DSC) konfigurációit a Azure Automation.
services: automation
ms.subservice: dsc
ms.date: 04/06/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4e0a8bc3057bc098855eb7ff65a8feeb83b7c746
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834821"
---
# <a name="compile-dsc-configurations-in-azure-automation-state-configuration"></a>DSC-konfigurációk fordítása a Azure Automation State Configuration

A következő Desired State Configuration (DSC) Azure Automation State Configuration fordíthatja le:

- Azure State Configuration fordítási szolgáltatás
  - Kezdő metódus interaktív felhasználói felülettel
   - A feladat állapotának egyszerű nyomon követése

- Windows PowerShell
  - Hívás a Windows PowerShell munkaállomásról vagy buildszolgáltatásból
  - Integrálás a fejlesztési tesztelési folyamatba
  - Összetett paraméterértékek megadása
  - Csomópont- és nem csomóponton kívüli adatok nagy léptékű munkához
  - Jelentős teljesítményjavak

A konfigurációs sablonokat Azure Resource Manager Azure Desired State Configuration (DSC) bővítővel is használhatja a konfigurációk Azure-beli virtuális gépekre való leküldéséhez. A Azure DSC bővítmény az Azure-beli virtuálisgép-ügynök keretrendszerét használja az Azure-beli virtuális gépeken futó DSC-konfigurációk kézbesítésére, lefuttatására és jelentésére. A sablonokkal való fordítás részleteiért Azure Resource Manager lásd: Desired State Configuration [bővítmény Azure Resource Manager sablonokkal.](../virtual-machines/extensions/dsc-template.md#details) 

## <a name="compile-a-dsc-configuration-in-azure-state-configuration"></a>DSC-konfiguráció fordítása az Azure State Configuration

### <a name="portal"></a>Portál

1. Az Automation-fiókjában kattintson az **Állapotkonfiguráció (DSC) elemre.**
1. Kattintson a **Konfigurációk lapra,** majd kattintson a fordítani szükséges konfiguráció nevére.
1. Kattintson a **Fordítás gombra.**
1. Ha a konfiguráció nem rendelkezik paraméterekkel, a rendszer megerősítést kér a fordításhoz. Ha a konfiguráció paraméterekkel rendelkezik, megnyílik a **Konfiguráció** fordítása panel, hogy meg tudja adni a paraméterértékeket.
1. Megnyílik a Fordítási feladat lap, hogy nyomon tudja követni a fordítási feladat állapotát. Ezen a lapon a lekért kiszolgálóra helyezett csomópont-konfigurációkat (MOF-konfigurációs dokumentumokat) Azure Automation State Configuration nyomon.

### <a name="azure-powershell"></a>Azure PowerShell

A [Start-AzAutomationDscCompilationJob használatával](/powershell/module/az.automation/start-azautomationdsccompilationjob) elkezdheti a fordítást a Windows PowerShell. A következő mintakód megkezdi a **SampleConfig** nevű DSC-konfiguráció fordítását.

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzAutomationDscCompilationJob` A egy fordítási feladatobjektumot ad vissza, amely a feladat állapotának nyomon követésére használható. Ezután használhatja ezt a fordítási feladatobjektumot a [Get-AzAutomationDscCompilationJob](/powershell/module/az.automation/get-azautomationdsccompilationjob) parancs használatával a fordítási feladat állapotának meghatározásához, a [Get-AzAutomationDscCompilationJobOutput](/powershell/module/az.automation/get-azautomationdscconfiguration) parancs használatával pedig megtekintheti a streameket (kimenetet). A következő minta megkezdi a SampleConfig konfiguráció fordítását, megvárja, amíg befejeződik, majd megjeleníti a streameket.

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

A DSC-konfigurációk paraméterdeklarációja, beleértve a paramétertípusokat és a tulajdonságokat, ugyanúgy működik, mint Azure Automation runbookok esetében. További [információ a runbook paramétereiről: Starting a runbook in Azure Automation Azure Automation](./start-runbooks.md) runbook in Azure Automation.

Az alábbi példa a és paraméterek használatával határozza meg a `FeatureName` `IsPresent` **ParametersExample.sample** csomópont konfigurációjában található tulajdonságok fordítás során létrehozott értékeit.

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

Olyan DSC-konfigurációkat fordíthat le, amelyek alapszintű paramétereket Azure Automation State Configuration a Azure PowerShell.

#### <a name="portal"></a>Portál

A portálon a Compile (Fordítás) gombra kattintva megadhatja a **paraméterértékeket.**

![Konfigurációs fordítási paraméterek](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

A PowerShellhez paraméterekre van szükség [egy](/powershell/module/microsoft.powershell.core/about/about_hash_tables)kivonattáblában, ahol a kulcs megegyezik a paraméter nevével, és az érték megegyezik a paraméter értékével.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

További információ az objektumok `PSCredential` paraméterként való átadásról: [Hitelesítőadat-objektumok.](#credential-assets)

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Összetett erőforrásokat tartalmazó konfigurációk fordítása a Azure Automation

Az **Összetett erőforrások** funkcióval beágyazott erőforrásokként használhatja a DSC-konfigurációkat egy konfiguráción belül. Ez a funkció lehetővé teszi több konfiguráció alkalmazását egyetlen erőforrásra. További [információ az összetett erőforrásokról: Összetett erőforrások: DSC-konfiguráció](/powershell/scripting/dsc/resources/authoringresourcecomposite) használata erőforrásként.

> [!NOTE]
> Az összetett erőforrásokat tartalmazó konfigurációk megfelelő fordítása érdekében először importálnia kell azokat Azure Automation DSC-erőforrásokba, amelyekre az összetettek támaszkodnak. Az összetett DSC-erőforrások hozzáadása nem különbözik a PowerShell-modulok hozzáadásától a Azure Automation. A folyamat dokumentációja a Modulok kezelése a [Azure Automation.](./shared-resources/modules.md)

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>ConfigurationData kezelése konfigurációk fordításakor a Azure Automation

`ConfigurationData` A egy beépített DSC-paraméter, amely lehetővé teszi a szerkezeti konfigurációk és a környezetspecifikus konfigurációk elválasztését a PowerShell DSC használata során. További információ: [A "Mit" és a "Hol" elkülönítése a PowerShell DSC-ban.](https://devblogs.microsoft.com/powershell/separating-what-from-where-in-powershell-dsc/)

> [!NOTE]
> A fordítási Azure Automation State Configuration használható a `ConfigurationData` Azure PowerShell, de a Azure Portal.

Az alábbi példa DSC-konfigurációja a `ConfigurationData` és a `$ConfigurationData` kulcsszót `$AllNodes` használja. Ehhez a példához az [xWebAdministration modulra](https://www.powershellgallery.com/packages/xWebAdministration/) is szüksége lesz.

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

Az előző DSC-konfigurációt lefordíthatja a Windows PowerShell. A következő szkript két csomópont-konfigurációt ad a Azure Automation State Configuration lekért szolgáltatáshoz: **ConfigurationDataSample.MyVM1** és **ConfigurationDataSample.MyVM3**.

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

### <a name="work-with-assets-in-azure-automation-during-compilation"></a>A fordítás során a Azure Automation való munka

Az eszközhivatkozások ugyanazok a két Azure Automation State Configuration runbookban. További információkat a következő cikkekben talál:

- [Tanúsítványok](./shared-resources/certificates.md)
- [Kapcsolatok](automation-connections.md)
- [Hitelesítő adatok](./shared-resources/credentials.md)
- [Változók](./shared-resources/variables.md)

#### <a name="credential-assets"></a>Hitelesítőadat-eszközök

A DSC-konfigurációk Azure Automation Automation hitelesítőadat-eszközeire hivatkoznak a `Get-AutomationPSCredential` parancsmag használatával. Ha egy konfigurációban van olyan paraméter, amely egy objektumot ad meg, használja a következőt: adja meg egy Azure Automation hitelesítőadat-objektum sztringnevét a parancsmagnak a hitelesítő adatok `PSCredential` `Get-AutomationPSCredential` lekérésére. Ezután használja ezt az objektumot az objektumot igénylő `PSCredential` paraméterhez. A színfalak mögött a Azure Automation nevű hitelesítő adateszközt lekéri, és továbbadja a konfigurációnak. Az alábbi példa ezt a forgatókönyvet mutatja be.

A hitelesítő adatok csomópont-konfigurációkban (MOF-konfigurációs dokumentumok) való biztonságának biztosítása megköveteli a csomópont-konfigurációs MOF-fájlban található hitelesítő adatok titkosítását. Jelenleg engedélyt kell adni a PowerShell DSC-nek a hitelesítő adatok egyszerű szövegként történő kimenetére a csomópont-konfiguráció MOF-létrehozása során. A PowerShell DSC nem tudja, Azure Automation a teljes MOF-fájlt egy fordítási feladaton keresztül titkosítja annak létrehozása után.

Megmondhatja a PowerShell DSC-nek, hogy a hitelesítő adatok egyszerű szövegként vannak kiírva a létrehozott csomópont-konfiguráció MFS-jében a konfigurációs adatok használatával. A `PSDscAllowPlainTextPassword = $true` DSC-konfigurációban megjelenő és hitelesítő adatokat használó csomópontblokkok neveinél adja meg a `ConfigurationData` következőt: .

Az alábbi példa egy Automation hitelesítőadat-eszközt használó DSC-konfigurációt mutat be.

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

A fenti DSC-konfigurációt a PowerShell használatával fordíthatja le. A következő PowerShell-kód két csomópontkonfigurációt ad hozzá a Azure Automation State Configuration lekért kiszolgálóhoz: **CredentialSample.MyVM1** és **CredentialSample.MyVM2**.

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
> Ha a fordítás befejeződött, a következő hibaüzenet jelenhet meg: Nyugodtan figyelmen kívül `The 'Microsoft.PowerShell.Management' module was not imported because the 'Microsoft.PowerShell.Management' snap-in was already imported.` hagyhatja ezt az üzenetet.

## <a name="compile-your-dsc-configuration-in-windows-powershell"></a>A DSC-konfiguráció fordítása a Windows PowerShell

A DSC-konfigurációk Windows PowerShell a PowerShell DSC-dokumentáció Írás, Fordítás és [Konfiguráció alkalmazása dokumentációjában található.](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration)
Ezt a folyamatot egy fejlesztői munkaállomásról vagy egy buildszolgáltatásból, például az [Azure DevOpsból hajthatja végre.](https://dev.azure.com) Ezután importálhatja a konfigurációt az Azure State Configuration szolgáltatásba fordításával előállított MOF-fájlokat.

A fordítás a Windows PowerShell konfigurációs tartalom aláírására is lehetőséget nyújt. A DSC-ügynök helyben ellenőrzi az aláírt csomópontok konfigurációját egy felügyelt csomóponton. Az ellenőrzés biztosítja, hogy a csomópontra alkalmazott konfiguráció egy engedélyezett forrásból származik.

Az Azure-n kívül lefordított csomópont-konfigurációkat (MOF-fájlokat) is importálhat. Az importálás magában foglalja a fordítást egy fejlesztői munkaállomásról vagy egy olyan szolgáltatásban, mint az [Azure DevOps.](https://dev.azure.com) Ennek a megközelítésnek több előnye is van, többek között a teljesítmény és a megbízhatóság.

> [!NOTE]
> A csomópont konfigurációs fájljának nem lehet nagyobb 1 MB-nál, hogy Azure Automation importálni.

A csomópont-konfigurációk aláírásával kapcsolatos további információkért lásd: [A WMF 5.1](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations)fejlesztései – Konfiguráció és modul aláírása.

### <a name="import-a-node-configuration-in-the-azure-portal"></a>Csomópont-konfiguráció importálása a Azure Portal

1. Az Automation-fiókjában kattintson az **Állapotkonfiguráció (DSC) elemre a** **Konfigurációkezelés alatt.**
1. Az Állapotkonfiguráció (DSC) lapon kattintson a **Konfigurációk** fülre, majd a Hozzáadás **parancsra.**
1. Az Importálás lapon kattintson a Csomópont-konfigurációs fájl mező melletti mappa ikonra egy csomópont-konfigurációs MOF-fájl megkeresése a helyi számítógépen. 

   ![Helyi fájl tallózása](./media/automation-dsc-compile/import-browse.png)

1. Adjon meg egy nevet a **Konfiguráció neve mezőben.** Ennek a névnek meg kell egyeznie annak a konfigurációnak a nevével, amelyből a csomópont-konfiguráció le lett fordítva.
1. Kattintson az **OK** gombra.

### <a name="import-a-node-configuration-with-azure-powershell"></a>Csomópont-konfiguráció importálása Azure PowerShell

Az [Import-AzAutomationDscNodeConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) parancsmag használatával importálhat egy csomópont-konfigurációt az Automation-fiókjába.

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Következő lépések

- Első lépések: [Első lépések a Azure Automation State Configuration.](automation-dsc-getting-started.md)
- A DSC-konfigurációk célcsomópontokhoz való hozzárendelése érdekében való fordításával kapcsolatos további információkért lásd: DSC-konfigurációk fordítása a [Azure Automation State Configuration.](automation-dsc-compile.md)
- A PowerShell-parancsmagok referenciáját lásd: [Az.Automation](/powershell/module/az.automation).
- Díjszabási információkért lásd: [Azure Automation State Configuration díjszabása.](https://azure.microsoft.com/pricing/details/automation/)
- A folyamatos üzembe helyezési folyamatban State Configuration példát a Folyamatos üzembe helyezés beállítása a [Chocolateyval.](automation-dsc-cd-chocolatey.md)
