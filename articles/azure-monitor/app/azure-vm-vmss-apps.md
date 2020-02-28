---
title: Azure-beli virtuális gépek teljesítményének figyelése – Azure Application Insights
description: Alkalmazások teljesítményének figyelése Azure-beli virtuális gépekhez és Azure-beli virtuálisgép-méretezési csoportokhoz. A diagram betöltésének és a válaszidő, a függőségi adatok és a riasztások beállítása a teljesítményre.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: d75e14dccef565f0029d06583e74d5693726dd99
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77661328"
---
# <a name="deploy-the-azure-monitor-application-insights-agent-on-azure-virtual-machines-and-azure-virtual-machine-scale-sets"></a>A Azure Monitor Application Insights-ügynök üzembe helyezése Azure-beli virtuális gépeken és Azure-beli virtuálisgép-méretezési csoportokban

Az [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) szolgáltatásban és az [Azure virtuálisgép-méretezési csoportokon](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) futó .NET-alapú webalkalmazások figyelésének engedélyezése mostantól minden eddiginél egyszerűbb. A kód módosítása nélkül érheti el a Application Insights használatának összes előnyét.

Ez a cikk bemutatja, hogyan engedélyezheti Application Insights monitorozását a Application Insights ügynökkel, és előzetes útmutatást nyújt a nagyméretű központi telepítések folyamatának automatizálásához.

> [!IMPORTANT]
> A .NET-hez készült Azure Application Insights-ügynök jelenleg nyilvános előzetes verzióban érhető el.
> Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk, és nem ajánlott éles környezetben üzemelő számítási feladatokhoz. Előfordulhat, hogy egyes funkciók nem támogatottak, és egyes szolgáltatások korlátozott képességekkel rendelkeznek.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enable-application-insights"></a>Az Application Insights engedélyezése

Az alkalmazások figyelését kétféleképpen engedélyezheti az Azure Virtual Machines szolgáltatásban és az Azure-beli virtuálisgép-méretezési csoportokban üzemeltetett alkalmazások:

* **Kódolás** Application Insights ügynökön keresztül
    * Ez a módszer a legkönnyebben engedélyezhető, és nincs szükség speciális konfigurációra. Ezt gyakran "futtatókörnyezet"-figyelőnek nevezzük.

    * Az Azure-beli virtuális gépek és az Azure virtuálisgép-méretezési csoportok esetében ajánlott minimálisan engedélyezni ezt a monitorozási szintet. Ezt követően az adott forgatókönyv alapján kiértékelheti, hogy szükség van-e a manuális rendszerállapotra.

    * A Application Insights ügynök automatikusan összegyűjti az azonos függőségi jeleket a .NET SDK-val. További információért lásd: [függőségek automatikus gyűjteménye](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies#net) .
        > [!NOTE]
        > Jelenleg csak a .NET IIS által üzemeltetett alkalmazások támogatottak. Az SDK-val egy Azure-beli virtuális gépeken és virtuálisgép-méretezési csoportokon üzemeltetett ASP.NET Core, Java és Node. js-alkalmazásokat használhat.

* SDK **-n alapuló kód**

    * Ez a megközelítés sokkal testreszabható, de a [Application INSIGHTS SDK NuGet-csomagokhoz való függőség hozzáadását](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)igényli. Ez a módszer azt is jelenti, hogy a frissítéseket a csomagok legújabb verziójára kell kezelnie.

    * Ha egyéni API-hívásokat kell megadnia az ügynök-alapú figyeléssel alapértelmezés szerint nem rögzített események/függőségek nyomon követéséhez, ezt a metódust kell használnia. További információért tekintse meg az [Egyéni események és mérőszámok API](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) -ját ismertető cikket.

> [!NOTE]
> Ha mind az ügynök-alapú figyelés, mind a manuális SDK-alapú rendszerállapotot észleli, a rendszer csak a manuális rendszerállapot-beállításokat veszi figyelembe. Ez megakadályozza az ismétlődő adatok küldését. Ha többet szeretne megtudni erről, tekintse meg az alábbi [hibaelhárítási szakaszt](#troubleshooting) .

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machines-using-powershell"></a>Azure-beli virtuális gépeken futó .NET-alkalmazások Application Insights ügynökének kezelése a PowerShell használatával

> [!NOTE]
> A Application Insights-ügynök telepítése előtt szüksége lesz egy kapcsolódó karakterláncra. [Hozzon létre egy új Application Insights-erőforrást](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) , vagy másolja a kapcsolatok karakterláncát egy meglévő Application ininsight-erőforrásból.

> [!NOTE]
> Újdonság a PowerShellben? Tekintse meg az [első lépéseket ismertető útmutatót](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-2.5.0).

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
A telepített bővítmények a portál Azure-beli [virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/extensions/overview) paneljén is megtekinthetők.

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

## <a name="troubleshooting"></a>Hibakeresés

Hibaelhárítási tippek az Azure-beli virtuális gépeken és virtuálisgép-méretezési csoportokon futó .NET-alkalmazások Application Insights monitorozási ügynökének bővítményéhez.

> [!NOTE]
> A .NET Core, a Java és a Node. js alkalmazások csak az Azure Virtual Machines és az Azure virtuálisgép-méretezési csoportok esetében támogatottak a manuális SDK-alapú rendszerállapotok használatával, ezért az alábbi lépések nem vonatkoznak ezekre a forgatókönyvekre.

A bővítmény-végrehajtás kimenete a következő címtárakban található fájlokra van naplózva:
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>Következő lépések
* Ismerje meg, hogyan [helyezhet üzembe egy alkalmazást egy Azure-beli virtuálisgép-méretezési csoporton](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md).
* [Állítsa be a rendelkezésre állási webes tesztek](monitor-web-app-availability.md) riasztását, ha a végpont le van állítva.
