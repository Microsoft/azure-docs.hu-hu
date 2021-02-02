---
title: Azure-beli virtuális gépek teljesítményének figyelése – Azure Application Insights
description: Alkalmazások teljesítményének figyelése Azure-beli virtuális gépekhez és Azure-beli virtuálisgép-méretezési csoportokhoz. A diagram betöltésének és a válaszidő, a függőségi adatok és a riasztások beállítása a teljesítményre.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: 01583cf5ecb85e4f66538afaba6984bff455ea99
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99475454"
---
# <a name="deploy-the-azure-monitor-application-insights-agent-on-azure-virtual-machines-and-azure-virtual-machine-scale-sets"></a>A Azure Monitor Application Insights-ügynök üzembe helyezése Azure-beli virtuális gépeken és Azure-beli virtuálisgép-méretezési csoportokban

Az [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) szolgáltatásban futó .net-vagy [Java-alapú](../../virtual-machine-scale-sets/index.yml) webalkalmazások figyelésének engedélyezése mostantól minden eddiginél egyszerűbb. A kód módosítása nélkül érheti el a Application Insights használatának összes előnyét.

Ez a cikk bemutatja, hogyan engedélyezheti Application Insights monitorozását a Application Insights ügynökkel, és előzetes útmutatást nyújt a nagyméretű központi telepítések folyamatának automatizálásához.
> [!IMPORTANT]
> Az Azure-beli virtuális gépeken és VMSS futó **Java** -alapú alkalmazások **[Application Insights Java 3,0-ügynökkel](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent)** vannak felügyelve, amely általánosan elérhető.

> [!IMPORTANT]
> Az Azure-beli **virtuális gépeken és VMSS** futó ASP.NET-alkalmazások Azure Application Insights-ügynöke jelenleg nyilvános előzetes verzióban érhető el. A **helyszínen** futó ASP.NET-alkalmazások figyeléséhez használja az [Azure Application Insights-ügynököt a helyszíni kiszolgálók számára](./status-monitor-v2-overview.md), amely általánosan elérhető és teljes mértékben támogatott.
> Az Azure-beli virtuális gépek és VMSS előzetes verziója szolgáltatási szintű szerződés nélkül érhető el, ezért nem ajánlott éles környezetben üzemelő számítási feladatokhoz adni. Előfordulhat, hogy egyes funkciók nem támogatottak, és egyes szolgáltatások korlátozott képességekkel rendelkeznek.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enable-application-insights"></a>Az Application Insights engedélyezése

Az alkalmazások figyelését kétféleképpen engedélyezheti az Azure Virtual Machines szolgáltatásban és az Azure-beli virtuálisgép-méretezési csoportokban üzemeltetett alkalmazások:

### <a name="auto-inctrumentation-via-application-insights-agent"></a>Automatikus inctrumentation Application Insights-ügynökön keresztül

* Ez a módszer a legkönnyebben engedélyezhető, és nincs szükség speciális konfigurációra. Ezt gyakran "futtatókörnyezet"-figyelőnek nevezzük.

* Az Azure-beli virtuális gépek és az Azure virtuálisgép-méretezési csoportok esetében ajánlott minimálisan engedélyezni ezt a monitorozási szintet. Ezt követően az adott forgatókönyv alapján kiértékelheti, hogy szükség van-e a manuális rendszerállapotra.

> [!NOTE]
> Az automatikus kiépítés jelenleg csak a .NET IIS által üzemeltetett alkalmazásokhoz és a Javához érhető el. SDK használata az Azure-beli virtuális gépeken és virtuálisgép-méretezési csoportokon üzemeltetett ASP.NET Core, Node.js és Python-alkalmazásokhoz.


#### <a name="net"></a>.NET

  * A Application Insights ügynök automatikusan összegyűjti az azonos függőségi jeleket a .NET SDK-val. További információért lásd: [függőségek automatikus gyűjteménye](./auto-collect-dependencies.md#net) .
        
#### <a name="java"></a>Java
  * A Javához a **[Application Insights java 3,0-ügynök](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent)** ajánlott módszer. A legnépszerűbb kódtárak és keretrendszerek, valamint a naplók és a függőségek [automatikusan begyűjthetők](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent#auto-collected-requests-dependencies-logs-and-metrics), számos [további konfigurációval](https://docs.microsoft.com/azure/azure-monitor/app/java-standalone-config)

### <a name="code-based-via-sdk"></a>SDK-n alapuló kód
    
#### <a name="net"></a>.NET
  * A .NET-alkalmazások esetében ez a megközelítés sokkal testreszabható, de a [Application INSIGHTS SDK NuGet-csomagokhoz való függőség hozzáadását](./asp-net.md)igényli. Ez a módszer azt is jelenti, hogy a frissítéseket a csomagok legújabb verziójára kell kezelnie.

  * Ha egyéni API-hívásokat kell megadnia az ügynök-alapú figyeléssel alapértelmezés szerint nem rögzített események/függőségek nyomon követéséhez, ezt a metódust kell használnia. További információért tekintse meg az [Egyéni események és mérőszámok API](./api-custom-events-metrics.md) -ját ismertető cikket.

    > [!NOTE]
    > Csak .NET-alkalmazások esetén – ha az ügynök-alapú figyelési és a manuális SDK-alapú rendszerállapotot is észleli, a rendszer csak a manuális rendszerállapot-beállításokat fogja figyelembe venni. Ez megakadályozza az ismétlődő adatok küldését. Ha többet szeretne megtudni erről, tekintse meg az alábbi [hibaelhárítási szakaszt](#troubleshooting) .

#### <a name="net-core"></a>.NET Core
A .NET Core-alkalmazások figyeléséhez használja az [SDK](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) -t 

#### <a name="java"></a>Java 

Ha további egyéni telemetria van szüksége a Java-alkalmazásokhoz, tekintse meg a mi [elérhető](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent#send-custom-telemetry-from-your-application), [Egyéni dimenziók](https://docs.microsoft.com/azure/azure-monitor/app/java-standalone-config#custom-dimensions)hozzáadása vagy a [telemetria processzorok](https://docs.microsoft.com/azure/azure-monitor/app/java-standalone-telemetry-processors)használata című témakört. 

#### <a name="nodejs"></a>Node.js

A Node.js alkalmazásához használja az [SDK](https://docs.microsoft.com/azure/azure-monitor/app/nodejs)-t.

#### <a name="python"></a>Python

A Python-alkalmazások figyeléséhez használja az [SDK](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python)-t.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machines-using-powershell"></a>Azure-beli virtuális gépeken futó .NET-alkalmazások Application Insights ügynökének kezelése a PowerShell használatával

> [!NOTE]
> A Application Insights-ügynök telepítése előtt szüksége lesz egy kapcsolódó karakterláncra. [Hozzon létre egy új Application Insights-erőforrást](./create-new-resource.md) , vagy másolja a kapcsolatok karakterláncát egy meglévő Application ininsight-erőforrásból.

> [!NOTE]
> Újdonság a PowerShellben? Tekintse meg az [első lépéseket ismertető útmutatót](/powershell/azure/get-started-azureps).

A Application Insights-ügynök telepítése vagy frissítése Azure-beli virtuális gépekhez bővítményként
```powershell
$publicCfgJsonString = '
{
  "redfieldConfiguration": {
    "instrumentationKeyMap": {
      "filters": [
        {
          "appFilter": ".*",
          "machineFilter": ".*",
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          }
        }
      ]
    }
  }
}
';
$privateCfgJsonString = '{}';

Set-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Location "<myVmLocation>" -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -Version "2.8" -SettingString $publicCfgJsonString -ProtectedSettingString $privateCfgJsonString
```

> [!NOTE]
> A Application Insights-ügynököt telepítheti vagy frissítheti kiterjesztésként több Virtual Machines, egy PowerShell-hurok használatával.

Application Insights ügynök bővítmény eltávolítása az Azure-beli virtuális gépről
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

Lekérdezés Application Insights ügynök bővítményének állapota az Azure-beli virtuális gépen
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

Az Azure-beli virtuális géphez telepített bővítmények listájának beolvasása
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```
A telepített bővítmények a portál Azure-beli [virtuális gépek](../../virtual-machines/extensions/overview.md) paneljén is megtekinthetők.

> [!NOTE]
> A telepítés ellenőrzéséhez kattintson a Élő metrikastream elemre az Application Insights-ügynök bővítményének telepítéséhez használt kapcsolódási karakterlánchoz tartozó Application Insights erőforráson belül. Ha több Virtual Machinesról küld adatokat, válassza ki a cél Azure-beli virtuális gépeket a kiszolgáló neve alatt. Az adatforgalom megkezdése akár egy percet is igénybe vehet.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machine-scale-sets-using-powershell"></a>.NET-alkalmazások Application Insights ügynökének kezelése Azure-beli virtuálisgép-méretezési csoportokkal a PowerShell használatával

A Application Insights-ügynök telepítése vagy frissítése Azure-beli virtuálisgép-méretezési csoport kiterjesztéseként
```powershell
$publicCfgHashtable =
@{
  "redfieldConfiguration"= @{
    "instrumentationKeyMap"= @{
      "filters"= @(
        @{
          "appFilter"= ".*";
          "machineFilter"= ".*";
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" # Application Insights connection string, create new Application Insights resource if you don't have one. https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents
          }
        }
      )
    }
  }
};
$privateCfgHashtable = @{};

$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -TypeHandlerVersion "2.8" -Setting $publicCfgHashtable -ProtectedSetting $privateCfgHashtable

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Alkalmazás-figyelési bővítmény eltávolítása az Azure virtuálisgép-méretezési csoportokból
```powershell
$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring"

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Alkalmazás-figyelési bővítmény állapotának lekérdezése Azure-beli virtuálisgép-méretezési csoportokhoz
```powershell
# Not supported by extensions framework
```

Azure-beli virtuálisgép-méretezési csoportokhoz tartozó telepített bővítmények listájának beolvasása
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>Hibaelhárítás

Hibaelhárítási tippek az Azure-beli virtuális gépeken és virtuálisgép-méretezési csoportokon futó .NET-alkalmazások Application Insights monitorozási ügynökének bővítményéhez.

> [!NOTE]
> A .NET Core, a Node.js és a Python alkalmazások csak az Azure Virtual Machines és az Azure virtuálisgép-méretezési csoportok esetében támogatottak a manuális SDK-alapú rendszerállapot-használaton keresztül, ezért az alábbi lépések nem vonatkoznak ezekre a forgatókönyvekre.

A bővítmény-végrehajtás kimenete a következő címtárakban található fájlokra van naplózva:
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>Következő lépések
* Ismerje meg, hogyan [helyezhet üzembe egy alkalmazást egy Azure-beli virtuálisgép-méretezési csoporton](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md).
* [Állítsa be a rendelkezésre állási webes tesztek](monitor-web-app-availability.md) riasztását, ha a végpont le van állítva.
