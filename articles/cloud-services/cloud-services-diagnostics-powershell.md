---
title: Diagnosztika engedélyezése az Azure Cloud Services (klasszikus) szolgáltatásban a PowerShell használatával | Microsoft Docs
description: Megtudhatja, hogyan használhatja a PowerShellt a diagnosztikai adatok Azure Cloud Service-ből való gyűjtésének engedélyezéséhez a Azure Diagnostics bővítménnyel.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 23528190d2eb60cc6ea3fe94bb9f7a2374526f6a
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102618440"
---
# <a name="enable-diagnostics-in-azure-cloud-services-classic-using-powershell"></a>Diagnosztika engedélyezése az Azure Cloud Services (klasszikus) PowerShell használatával

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.

A Azure Diagnostics-bővítmény használatával diagnosztikai adatokat gyűjthet, például az alkalmazás naplóit, a teljesítményszámlálókat stb. a felhőalapú szolgáltatásokból. Ez a cikk azt ismerteti, hogyan engedélyezhető a Azure Diagnostics bővítmény a Cloud Service-hez a PowerShell használatával.  Tekintse meg, [hogyan telepítheti és konfigurálhatja a Azure PowerShell](/powershell/azure/) a cikkhez szükséges előfeltételekhez.

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>A diagnosztikai bővítmény engedélyezése egy felhőszolgáltatás telepítésének részeként
Ez a megközelítés a forgatókönyvek folyamatos integrációs típusára vonatkozik, ahol a diagnosztikai bővítmény engedélyezhető a felhőalapú szolgáltatás üzembe helyezésének részeként. Új felhőalapú szolgáltatás központi telepítésének létrehozásakor engedélyezheti a diagnosztikai bővítményt, ha a *ExtensionConfiguration* paramétert a [New-AzureDeployment](/powershell/module/servicemanagement/azure.service/new-azuredeployment) parancsmagra továbbítja. A *ExtensionConfiguration* paraméter a [New-AzureServiceDiagnosticsExtensionConfig](/powershell/module/servicemanagement/azure.service/new-azureservicediagnosticsextensionconfig) parancsmaggal létrehozható diagnosztikai konfigurációk tömbjét veszi fel.

Az alábbi példa bemutatja, hogyan engedélyezheti a diagnosztika szolgáltatást egy olyan WorkerRole, amely egy másik diagnosztika-konfigurációval rendelkezik.

```powershell
$service_name = "MyService"
$service_package = "CloudService.cspkg"
$service_config = "ServiceConfiguration.Cloud.cscfg"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)
```

Ha a diagnosztikai konfigurációs fájl egy olyan `StorageAccount` elemet ad meg, amely egy Storage-fiók nevét tartalmazza, akkor a `New-AzureServiceDiagnosticsExtensionConfig` parancsmag automatikusan ezt a Storage-fiókot fogja használni. Ahhoz, hogy működjön, a Storage-fióknak ugyanabban az előfizetésben kell lennie, mint az üzembe helyezett felhőalapú szolgáltatásnak.

Az Azure SDK 2,6-tól kezdve az MSBuild közzétételi cél kimenetében létrehozott bővítmény konfigurációs fájljai a Storage-fiók nevét is tartalmazzák a szolgáltatás konfigurációs fájljában (. cscfg) megadott diagnosztikai konfigurációs karakterlánc alapján. Az alábbi szkript bemutatja, hogyan elemezheti a bővítmény konfigurációs fájljait a közzétételi cél kimenetből, és hogyan konfigurálhatja a diagnosztikai bővítményt az egyes szerepkörökhöz a Cloud Service telepítésekor.

```powershell
$service_name = "MyService"
$service_package = "C:\build\output\CloudService.cspkg"
$service_config = "C:\build\output\ServiceConfiguration.Cloud.cscfg"

#Find the Extensions path based on service configuration file
$extensionsSearchPath = Join-Path -Path (Split-Path -Parent $service_config) -ChildPath "Extensions"

$diagnosticsExtensions = Get-ChildItem -Path $extensionsSearchPath -Filter "PaaSDiagnostics.*.PubConfig.xml"
$diagnosticsConfigurations = @()
foreach ($extPath in $diagnosticsExtensions)
{
    #Find the RoleName based on file naming convention PaaSDiagnostics.<RoleName>.PubConfig.xml
    $roleName = ""
    $roles = $extPath -split ".",0,"simplematch"
    if ($roles -is [system.array] -and $roles.Length -gt 1)
    {
        $roleName = $roles[1]
        $x = 2
        while ($x -le $roles.Length)
            {
               if ($roles[$x] -ne "PubConfig")
                {
                    $roleName = $roleName + "." + $roles[$x]
                }
                else
                {
                    break
                }
                $x++
            }
        $fullExtPath = Join-Path -path $extensionsSearchPath -ChildPath $extPath
        $diagnosticsconfig = New-AzureServiceDiagnosticsExtensionConfig -Role $roleName -DiagnosticsConfigurationPath $fullExtPath
        $diagnosticsConfigurations += $diagnosticsconfig
    }
}
New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration $diagnosticsConfigurations
```

A Visual Studio online hasonló módszert használ a Cloud Services automatikus üzembe helyezéséhez a diagnosztikai bővítménnyel. Teljes példa: [Publish-AzureCloudDeployment.ps1](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureCloudPowerShellDeploymentV1/Publish-AzureCloudDeployment.ps1) .

Ha nem `StorageAccount` lett megadva a diagnosztika konfigurációjában, akkor át kell adni a *StorageAccountName* paramétert a parancsmagnak. Ha a *StorageAccountName* paraméter meg van adva, a parancsmag mindig a paraméterben megadott Storage-fiókot fogja használni, nem pedig a diagnosztikai konfigurációs fájlban megadott értéket.

Ha a diagnosztikai Storage-fiók egy másik előfizetésben található a Cloud Service-ben, akkor explicit módon át kell adni a *StorageAccountName* és a *StorageAccountKey* paramétereket a parancsmagnak. A *StorageAccountKey* paraméter nem szükséges, ha a diagnosztika Storage-fiók ugyanabban az előfizetésben van, mivel a parancsmag automatikusan kérdezheti le és állíthatja be a kulcs értékét a diagnosztikai bővítmény engedélyezésekor. Ha azonban a diagnosztikai tároló fiók egy másik előfizetésben található, előfordulhat, hogy a parancsmag nem tudja automatikusan beolvasni a kulcsot, és explicit módon meg kell adnia a kulcsot a *StorageAccountKey* paraméterrel.

```powershell
$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>A diagnosztikai bővítmény engedélyezése egy meglévő felhőszolgáltatáson
A [set-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azureservicediagnosticsextension) parancsmag használatával engedélyezheti vagy frissítheti a diagnosztikai konfigurációt egy már futó felhőalapú szolgáltatásban.

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

```powershell
$service_name = "MyService"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

Set-AzureServiceDiagnosticsExtension -DiagnosticsConfiguration @($webrole_diagconfig,$workerrole_diagconfig) -ServiceName $service_name
```

## <a name="get-current-diagnostics-extension-configuration"></a>Az aktuális diagnosztikai bővítmény konfigurációjának lekérése
Használja a [Get-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/get-azureservicediagnosticsextension) parancsmagot a felhőalapú szolgáltatás aktuális diagnosztikai konfigurációjának lekéréséhez.

```powershell
Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

## <a name="remove-diagnostics-extension"></a>A diagnosztikai bővítmény eltávolítása
A diagnosztika a Cloud Service-ben való kikapcsolásához használhatja a [Remove-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/remove-azureservicediagnosticsextension) parancsmagot.

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Ha engedélyezte a diagnosztikai bővítményt a *set-AzureServiceDiagnosticsExtension* vagy a *New-AzureServiceDiagnosticsExtensionConfig* *szerepkör* -paraméter nélkül, akkor a *szerepkör* -paraméter nélkül eltávolíthatja a bővítményt a *Remove-AzureServiceDiagnosticsExtension* parancs használatával. Ha a *szerepkör* -paramétert a bővítmény engedélyezésekor használták, akkor azt is használni kell a bővítmény eltávolításához.

A diagnosztika bővítmény egyes szerepkörökből való eltávolítása:

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```

## <a name="next-steps"></a>Következő lépések
* Az Azure Diagnostics és a problémák elhárítására szolgáló egyéb technikák használatával kapcsolatos további útmutatásért lásd: [a diagnosztika engedélyezése az Azure-ban Cloud Services és Virtual Machines](cloud-services-dotnet-diagnostics.md).
* A [diagnosztika konfigurációs sémája](../azure-monitor/agents/diagnostics-extension-schema-windows.md) a diagnosztikai bővítmény különböző XML-konfigurációk beállításait ismerteti.
* Ha meg szeretné tudni, hogyan engedélyezhető a Virtual Machines diagnosztikai bővítménye, tekintse meg a [Windows rendszerű virtuális gép létrehozása figyelési és diagnosztikai Azure Resource Manager sablon használatával](../virtual-machines/extensions/diagnostics-template.md) című témakört.