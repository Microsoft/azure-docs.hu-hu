---
title: Webalkalmazások profilja Azure-beli virtuális gépen – Application Insights Profiler
description: Az Azure-beli virtuális gépen Application Insights Profiler használatával webalkalmazásokat is felhasználhat.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 11/08/2019
ms.reviewer: mbullwin
ms.openlocfilehash: f514dd7b54ac091535aeab43a8a7d2a645b50a09
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87315825"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>Azure-beli virtuális gépen vagy virtuálisgép-méretezési csoporton futó profil Web Apps Application Insights Profiler használatával

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Az Azure Application Insights Profiler a következő szolgáltatásokon is üzembe helyezhető:
* [Azure App Service](./profiler.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](?toc=%2fazure%2fazure-monitor%2ftoc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>A Profiler üzembe helyezése virtuális gépen vagy virtuálisgép-méretezési csoporton
Ez a cikk bemutatja, hogyan érheti el az Azure-beli virtuális gépen (VM) vagy az Azure virtuálisgép-méretezési csoporton futó Application Insights Profiler. A Profiler a virtuális gépek Azure Diagnostics-bővítményével együtt települ. Konfigurálja a bővítményt a Profiler futtatásához, és hozza létre az Application Insights SDK-t az alkalmazásba.

1. Adja hozzá a Application Insights SDK-t a [ASP.NET-alkalmazáshoz](./asp-net.md).

   A kérések profiljainak megtekintéséhez el kell küldenie a kérelmek telemetria Application Insights.

1. Telepítse Azure Diagnostics bővítményt a virtuális gépre. A teljes Resource Manager-sablonokra vonatkozó példákat a következő témakörben talál:  
   * [Virtuális gép](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
   * [Virtuálisgép-méretezési csoport](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
     A kulcs része a WadCfg lévő ApplicationInsightsProfilerSink. Ahhoz, hogy Azure Diagnostics engedélyezze a Profiler számára a Rendszerállapotkulcsot való adatküldést, adjon hozzá egy másik fogadót ehhez a szakaszhoz.
    
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

1. Telepítse a módosított környezet központi telepítési definícióját.  

   A módosítások alkalmazása általában teljes sablont vagy felhőalapú, a PowerShell-parancsmagokkal vagy a Visual Studióval végzett közzétételt tartalmaz.  

   A következő PowerShell-parancsok alternatív megközelítést biztosítanak a meglévő virtuális gépekhez, amelyek csak a Azure Diagnostics-bővítményt érintik. Adja hozzá a korábban említett ProfilerSink az Get-AzVMDiagnosticsExtension parancs által visszaadott konfigurációhoz. Ezután adja át a frissített konfigurációt a Set-AzVMDiagnosticsExtension parancsnak.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. Ha a kívánt alkalmazás az [IIS](https://www.microsoft.com/web/downloads/platform.aspx)-en keresztül fut, engedélyezze a `IIS Http Tracing` Windows-szolgáltatást.

   a. Hozzon létre távoli hozzáférést a környezethez, majd használja a [Windows-szolgáltatások hozzáadása](/iis/configuration/system.webserver/tracing/) ablakot. Vagy futtassa a következő parancsot a PowerShellben (rendszergazdaként):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Ha probléma van a táveléréssel, az [Azure CLI](/cli/azure/get-started-with-azure-cli) használatával futtathatja a következő parancsot:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. Az alkalmazás üzembe helyezése.

## <a name="set-profiler-sink-using-azure-resource-explorer"></a>Profiler-fogadó beállítása Azure Erőforrás-kezelő használatával
Még nem tudjuk beállítani a Application Insights Profiler-gyűjtőt a portálról. A fent ismertetett PowerShell helyett a Azure Erőforrás-kezelő használatával állíthatja be a fogadót. Azonban vegye figyelembe, hogy ha újra telepíti a virtuális gépet, a fogadó el fog veszni. A beállítás megőrzése érdekében frissítenie kell a virtuális gép telepítésekor használt konfigurációt.

1. A virtuális géphez telepített bővítmények megtekintésével győződjön meg arról, hogy a Windows Azure Diagnostics bővítmény telepítve van.  

    ![Ellenőrizze, hogy telepítve van-e a WAD bővítmény][wadextension]

2. Keresse meg a VM Diagnostics bővítményt a virtuális géphez. Ugrás a következőre: [https://resources.azure.com](https://resources.azure.com) . Bontsa ki az erőforráscsoportot, a Microsoft. számítási virtualMachines, a virtuális gép nevét és a bővítményeket.  

    ![Navigáljon a WAD-konfigurációhoz Azure Erőforrás-kezelő][azureresourceexplorer]

3. Adja hozzá a Application Insights Profiler-gyűjtőt a SinksConfig csomóponthoz a WadCfg alatt. Ha még nem rendelkezik SinksConfig résszel, előfordulhat, hogy hozzá kell adnia egyet. Ügyeljen arra, hogy a megfelelő Application Insights Rendszerállapotkulcsot adja meg a beállításokban. A Térkezelő üzemmódot a jobb felső sarokban olvasható/olvashatóra kell váltania, majd a kék "szerkesztés" gombra kell kattintania.

    ![Application Insights Profiler fogadó hozzáadása][resourceexplorersinksconfig]

4. Ha elkészült a konfiguráció szerkesztésével, nyomja meg az "put" gombot. Ha a Put sikeres, egy zöld pipa jelenik meg a képernyő közepén.

    ![Put kérelem küldése a módosítások alkalmazásához][resourceexplorerput]






## <a name="can-profiler-run-on-on-premises-servers"></a>A Profiler a helyszíni kiszolgálókon is futtatható?
Nem tervezzük a helyszíni kiszolgálók Application Insights Profiler támogatását.

## <a name="next-steps"></a>Következő lépések

- Adatforgalom létrehozása az alkalmazáshoz (például egy [rendelkezésre állási teszt](monitor-web-app-availability.md)elindításához). Ezután várjon 10 – 15 percet, amíg a Nyomkövetések el nem jutnak a Application Insights példányba.
- Lásd: [Profiler-Nyomkövetések](profiler-overview.md?toc=/azure/azure-monitor/toc.json) a Azure Portalban.
- A Profiler hibaelhárításával kapcsolatos segítségért lásd a [Profiler hibaelhárítását](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)ismertető témakört.

[azureresourceexplorer]: ./media/profiler-vm/azure-resource-explorer.png
[resourceexplorerput]: ./media/profiler-vm/resource-explorer-put.png
[resourceexplorersinksconfig]: ./media/profiler-vm/resource-explorer-sinks-config.png
[wadextension]: ./media/profiler-vm/wad-extension.png

