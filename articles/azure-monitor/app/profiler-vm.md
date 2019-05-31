---
title: Az Application Insights Profiler-beli virtuális gépen futó webes alkalmazásprofilok |} A Microsoft Docs
description: Profil webalkalmazások az Application Insights Profiler-beli virtuális gépen.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: ab30351bfff9c5bbf070a1e8a54a4919e4d2231a
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226268"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>Profil web Apps alkalmazások az Azure virtuális gép vagy virtuálisgép-méretezési csoportot beállítani az Application Insights Profiler segítségével

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ezek a szolgáltatások az Azure Application Insights Profiler is telepítheti:
* [Azure App Service](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>Profiler egy virtuális gépet vagy virtuálisgép-méretezési csoportot üzembe
Ez a cikk bemutatja, hogyan tehet szert az Application Insights Profiler az Azure virtuális gép (VM) vagy az Azure-beli virtuálisgép-méretezési csoportban futó. Profiler az Azure Diagnostics bővítmény telepítve van a virtuális gépek számára. A bővítmény futtatásának Profiler konfigurálása, és az Application Insights SDK építve az alkalmazásba.

1. Az Application Insights SDK hozzáadása a [ASP.NET-alkalmazás](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net).

   Profilok számára a kérelmek megtekintése, Application Insights – kéréstelemetria kell küldenie.

1. Azure Diagnostics bővítmény telepítése a virtuális Gépen. Teljes Resource Manager sablon példákért lásd:  
   * [Virtuális gép](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
   * [Virtuálisgép-méretezési csoportot](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
     A kulcs része a ApplicationInsightsProfilerSink a WadCfg a. Ahhoz, hogy az Azure diagnosztikai adatokat küldeni a Rendszerállapotkulcsot Profiler engedélyezése, ez a szakasz egy másik fogadó hozzá.
    
     ```json
     "SinksConfig": {
       "Sink": [
         {
           "name": "ApplicationInsightsSink",
           "ApplicationInsights": "85f73556-b1ba-46de-9534-606e08c6120f"
         },
         {
           "name": "MyApplicationInsightsProfilerSink",
           "ApplicationInsightsProfiler": "85f73556-b1ba-46de-9534-606e08c6120f"
         }
       ]
     },
     ```

1. Helyezze üzembe a módosított környezetben üzemelő példány definíciója.  

   A módosítások alkalmazása általában magában foglalja egy teljes körű sablonalapú telepítés vagy a PowerShell-parancsmagok vagy a Visual Studio közzétételéhez egy cloud service-alapú.  

   A következő PowerShell-parancsok egy meglévő virtuális gépek, amelyek csak az Azure Diagnostics bővítmény touch alternatív módszert is. Adja hozzá a korábban említett ProfilerSink a Get-AzVMDiagnosticsExtension parancs által visszaadott a config. Ezután adja meg a frissített konfiguráció a Set-AzVMDiagnosticsExtension parancsot.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. Ha az importálni kívánt alkalmazást futtató [IIS](https://www.microsoft.com/web/downloads/platform.aspx), engedélyezze a `IIS Http Tracing` Windows-szolgáltatás.

   a. Állítsa be a környezeti távoli elérését, és használja a [hozzáadása Windows-szolgáltatások]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) ablak. Vagy futtassa a következő parancsot a PowerShellben (rendszergazdaként):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Ha létrehozó távelérési probléma, használhatja a [Azure CLI-vel](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) , futtassa a következő parancsot:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. Az alkalmazás üzembe helyezéséhez.

## <a name="set-profiler-sink-using-azure-resource-explorer"></a>Állítsa be a Profiler fogadó Azure erőforrás-kezelő használatával
Még nem is be lehet állítani az Application Insights Profiler fogadó a portálról. Például a powershell használata helyett a fent leírt használhatja az Azure erőforrás-kezelő beállítása a fogadó. De vegye figyelembe, ha a virtuális gép ismételt üzembe helyezése, a fogadó elvesznek. Kell használnia, ha a beállítás megőrzése érdekében ez a virtuális gép üzembe helyezése a konfiguráció frissítése.

1. Ellenőrizze, hogy a Windows Azure Diagnostics bővítmény telepítve van-e a bővítmények a virtuális gépre telepített megtekintésével.  

    ![Ellenőrizze, hogy telepítve van-e a WAD-bővítmény][wadextension]

1. A virtuális gép diagnosztikai bővítményt a virtuális gép található. Bontsa ki az erőforráscsoportot, a Microsoft.Compute virtuális gép, a virtuális gép nevét és a bővítményeket.  

    ![Keresse meg az Azure Resource Explorerben WAD config][azureresourceexplorer]

1. Adja hozzá az Application Insights Profiler fogadó WadCfg SinksConfig csomópontját. Ha még nem rendelkezik egy SinksConfig szakaszt, szükség lehet adjon hozzá egyet. Győződjön meg arról, ha meg szeretné adni a megfelelő Application Insights Rendszerállapotkulcsot a beállítások. Kell olvasási/írási a szoftverkategóriák mód váltson a jobb felső sarkában, és a kék "Szerkesztés" gombra.

    ![Adja hozzá az Application Insights Profiler fogadó][resourceexplorersinksconfig]

1. Ha végzett a konfiguráció szerkesztése nyomja meg az "Put". Ha a put művelet sikeres, egy zöld pipa jelenik meg a képernyő közepén.

    ![Módosítások alkalmazásához a put kérés küldése][resourceexplorerput]






## <a name="can-profiler-run-on-on-premises-servers"></a>Profiler futhatnak a helyszíni kiszolgálók?
Application Insights Profiler támogató helyszíni kiszolgálók terv van.

## <a name="next-steps"></a>További lépések

- Hozzon létre a forgalmat az alkalmazásához (például indítása egy [rendelkezésre állási teszt](monitor-web-app-availability.md)). Várjon 10 – 15 percet nyomok kell küldeni az Application Insights-példány elindításához.
- Lásd: [Profiler nyomkövetések](profiler-overview.md?toc=/azure/azure-monitor/toc.json) az Azure Portalon.
- Segítség a Profiler kapcsolatos hibák elhárítása: [hibaelhárítási Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).

[azureresourceexplorer]: ./media/profiler-vm/azure-resource-explorer.png
[resourceexplorerput]: ./media/profiler-vm/resource-explorer-put.png
[resourceexplorersinksconfig]: ./media/profiler-vm/resource-explorer-sinks-config.png
[wadextension]: ./media/profiler-vm/wad-extension.png
