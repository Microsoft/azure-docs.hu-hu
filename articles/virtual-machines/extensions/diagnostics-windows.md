---
title: A diagnosztika engedélyezése a Azure PowerShell használatával a Windows rendszerű virtuális gépeken
description: Megtudhatja, hogyan használhatja a PowerShellt a Azure Diagnostics Windows rendszerű virtuális gépeken való engedélyezéséhez
ms.topic: article
author: amjads1
ms.author: amjads
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.date: 12/15/2015
ms.openlocfilehash: e242a45e2c0f8247a1c23bf4d43a6e8b0a033d9b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560091"
---
# <a name="use-powershell-to-enable-azure-diagnostics-in-a-virtual-machine-running-windows"></a>A Azure Diagnostics engedélyezése a PowerShell használatával a Windows rendszerű virtuális gépeken

A Azure Diagnostics az Azure-on belüli képesség, amely lehetővé teszi a diagnosztikai adatgyűjtést egy telepített alkalmazáson. A diagnosztika bővítmény használatával diagnosztikai adatokat gyűjthet, például az alkalmazás naplóit vagy a teljesítményszámlálók egy Windows rendszert futtató Azure-beli virtuális gépről (VM). 

 

## <a name="enable-the-diagnostics-extension-if-you-use-the-resource-manager-deployment-model"></a>A diagnosztikai bővítmény engedélyezése, ha a Resource Manager-alapú üzemi modellt használja
A diagnosztikai bővítményt engedélyezheti a Windows rendszerű virtuális gépek a Azure Resource Manager üzemi modellel való létrehozásakor, ha hozzáadja a bővítmény konfigurációját a Resource Manager-sablonhoz. Lásd: [Windows rendszerű virtuális gép létrehozása figyelési és diagnosztikai szolgáltatásokkal a Azure Resource Manager sablonnal](diagnostics-template.md).

Ha engedélyezni szeretné a diagnosztikai bővítményt egy olyan meglévő virtuális gépen, amely a Resource Manager-alapú üzemi modellel lett létrehozva, használhatja a [set-AzVMDiagnosticsExtension PowerShell-](/powershell/module/az.compute/set-azvmdiagnosticsextension) parancsmagot az alábbi ábrán látható módon.

```azurepowershell
$vm_resourcegroup = "myvmresourcegroup"
$vm_name = "myvm"
$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path
```


*$diagnosticsconfig _path* a diagnosztikai konfigurációt tartalmazó fájl elérési útja az XML-ben, az alábbi [példában](#sample-diagnostics-configuration) leírtak szerint.  

Ha a diagnosztikai konfigurációs fájl egy **StorageAccount** elemet ad meg a Storage-fiók nevével, akkor a *set-AzVMDiagnosticsExtension* parancsfájl automatikusan beállítja a diagnosztika bővítményt, hogy diagnosztikai adatait küldjön erre a Storage-fiókra. Ahhoz, hogy működjön, a Storage-fióknak ugyanabban az előfizetésben kell lennie, mint a virtuális gépnek.

Ha nincs megadva **StorageAccount** a diagnosztika konfigurációjában, akkor át kell adnia a *StorageAccountName* paramétert a parancsmagnak. Ha a *StorageAccountName* paraméter meg van adva, a parancsmag mindig a paraméterben megadott Storage-fiókot fogja használni, nem pedig a diagnosztikai konfigurációs fájlban megadott értéket.

Ha a diagnosztikai Storage-fiók a virtuális gépről eltérő előfizetésben található, akkor explicit módon át kell adni a *StorageAccountName* és a *StorageAccountKey* paramétereket a parancsmagnak. A *StorageAccountKey* paraméter nem szükséges, ha a diagnosztika Storage-fiók ugyanabban az előfizetésben van, mivel a parancsmag automatikusan kérdezheti le és állíthatja be a kulcs értékét a diagnosztikai bővítmény engedélyezésekor. Ha azonban a diagnosztikai tároló fiók egy másik előfizetésben található, előfordulhat, hogy a parancsmag nem tudja automatikusan beolvasni a kulcsot, és explicit módon meg kell adnia a kulcsot a *StorageAccountKey* paraméterrel.  

```azurepowershell
Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

Ha a diagnosztikai bővítmény engedélyezve van egy virtuális gépen, az aktuális beállításokat a [Get-AzVmDiagnosticsExtension](/powershell/module/az.compute/get-azvmdiagnosticsextension) parancsmag használatával érheti el.

```azurepowershell
Get-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name
```

A parancsmag *PublicSettings* ad vissza, amely tartalmazza a diagnosztika konfigurációját. Kétféle konfiguráció támogatott, a WadCfg és a xmlCfg. A WadCfg a JSON-konfiguráció, a xmlCfg pedig Base64 kódolású formátumú XML-konfiguráció. Az XML-fájl olvasásához dekódolni kell.

```azurepowershell
$publicsettings = (Get-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
$encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
$xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
Write-Host $xmlconfig
```

A [Remove-AzVmDiagnosticsExtension](/powershell/module/az.compute/remove-azvmdiagnosticsextension) parancsmag használatával eltávolíthatja a diagnosztikai bővítményt a virtuális gépről.  

## <a name="enable-the-diagnostics-extension-if-you-use-the-classic-deployment-model"></a>A diagnosztikai bővítmény engedélyezése, ha a klasszikus telepítési modellt használja

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

A [set-AzureVMDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azurevmdiagnosticsextension) parancsmaggal engedélyezheti a diagnosztikai bővítményt egy olyan virtuális gépen, amelyet a klasszikus üzemi modell használatával hozott létre. Az alábbi példa bemutatja, hogyan hozhat létre új virtuális gépet a klasszikus üzembe helyezési modellel a diagnosztika bővítmény engedélyezésével.

```azurepowershell
$VM = New-AzureVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
$VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
$VM = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
New-AzVM -Location $Location -ServiceName $Service_Name -VM $VM
```

Ha engedélyezni szeretné a diagnosztikai bővítményt egy olyan meglévő virtuális gépen, amely a klasszikus üzemi modellel lett létrehozva, először használja a [Get-AzureVM](/powershell/module/servicemanagement/azure.service/get-azurevm) parancsmagot a virtuális gép konfigurációjának beszerzéséhez. Ezután frissítse a virtuális gép konfigurációját, hogy tartalmazza a diagnosztikai bővítményt a [set-AzureVMDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azurevmdiagnosticsextension) parancsmag használatával. Végül alkalmazza a frissített konfigurációt a virtuális gépre az [Update-AzureVM](/powershell/module/servicemanagement/azure.service/update-azurevm)használatával.

```azurepowershell
$VM = Get-AzureVM -ServiceName $Service_Name -Name $VM_Name
$VM_Update = Set-AzureVMDiagnosticsExtension  -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
Update-AzureVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM
```

## <a name="sample-diagnostics-configuration"></a>Példa diagnosztikai konfigurációra
A következő XML-kód használható a diagnosztikai nyilvános konfigurációhoz a fenti szkriptek használatával. Ez a minta-konfiguráció különböző teljesítményszámlálókat továbbít a diagnosztika Storage-fiókba, valamint az alkalmazásból, a biztonságból és a rendszercsatornákból származó hibákat a Windows eseménynaplókban, valamint a diagnosztikai infrastruktúra naplófájljaiban előforduló hibákat.

A konfigurációt frissíteni kell, hogy tartalmazza a következőket:

* A **metrikák** elem *resourceID* attribútumát frissíteni kell a virtuális gép erőforrás-azonosítójával.
  
  * Az erőforrás-azonosító a következő mintával állítható össze: "a virtuális gép/Subscriptions/{*előfizetés-azonosítója* a VM-szel}/resourceGroups/{a virtuális gép resourcegroup-*neve*}*"/Providers/Microsoft.Compute/virtualMachines/{.*
  * Ha például a virtuális gépet futtató előfizetés előfizetés-azonosítója **11111111-1111-1111-1111-111111111111**, az erőforráscsoport neve **MyResourceGroup**, a virtuális gép neve pedig **MyWindowsVM**, a *resourceID* értéke pedig a következő lesz:
    
      ```xml
      <Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
      ```
  * További információ a metrikák létrehozásáról a teljesítményszámlálók és a metrikák konfigurálása alapján: [Azure Diagnostics metrika táblázat a Storage-ban](diagnostics-template.md#wadmetrics-tables-in-storage).
* A **StorageAccount** elemet frissíteni kell a Diagnostics Storage-fiók nevével.
  
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
          <DiagnosticMonitorConfiguration overallQuotaInMB="4096">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU utilization" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Privileged Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU privileged time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% User Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU user time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor Information(_Total)\Processor Frequency" sampleRate="PT15S" unit="Count">
            <annotation displayName="CPU frequency" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\System\Processes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Processes" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Thread Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Threads" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Handle Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Handles" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes In Use" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Memory usage" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory available" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory committed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Commit Limit" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory commit limit" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Paged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Nonpaged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory non-paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Read Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active read time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Write Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active write time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Transfers/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Reads/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk read operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Writes/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk write operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Read Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk read speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Write Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk write speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Read Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average read queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Write Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average write queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\% Free Space" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk free space (percentage)" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\Free Megabytes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk free space (MB)" locale="en-us"/>
          </PerformanceCounterConfiguration>
        </PerformanceCounters>
        <Metrics resourceId="(Update with resource ID for the VM)" >
            <MetricAggregation scheduledTransferPeriod="PT1H"/>
            <MetricAggregation scheduledTransferPeriod="PT1M"/>
        </Metrics>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]"/>
          <DataSource name="Security!*[System[(Level = 1 or Level = 2)]"/>
          <DataSource name="System!*[System[(Level = 1 or Level = 2)]]"/>
        </WindowsEventLog>
          </DiagnosticMonitorConfiguration>
        </WadCfg>
        <StorageAccount>(Update with diagnostics storage account name)</StorageAccount>
    </PublicConfig>
    ```

## <a name="next-steps"></a>Következő lépések
* A Azure Diagnostics képesség és a problémák elhárítására szolgáló egyéb technikák használatával kapcsolatos további útmutatásért lásd: a [diagnosztika engedélyezése az Azure-ban Cloud Services és Virtual Machines](../../cloud-services/cloud-services-dotnet-diagnostics.md).
* A [diagnosztikai konfigurációk sémája](../../azure-monitor/agents/diagnostics-extension-versions.md) a diagnosztikai bővítmény különböző XML-konfigurációs beállításait ismerteti.