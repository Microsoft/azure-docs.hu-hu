---
title: Klasszikus Windowsos VM-metrikák küldése Azure Monitor metrikák adatbázisának
description: Vendég operációs rendszer metrikáinak küldése a Windows rendszerű virtuális gépek Azure Monitor adattárához (klasszikus)
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.openlocfilehash: dcb2c0ceb3092bb6905ac1eb9fc58093da935f2f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048981"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metrics-database-for-a-windows-virtual-machine-classic"></a>Vendég operációs rendszer metrikáinak küldése a Windows rendszerű virtuális gépek Azure Monitor metrikáinak adatbázisába (klasszikus)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A Azure Monitor [diagnosztikai bővítmény](../agents/diagnostics-extension-overview.md) (más néven "wad" vagy "diagnosztika") lehetővé teszi mérőszámok és naplók összegyűjtését a virtuális gép, a Cloud Service vagy a Service Fabric-fürt részeként futó vendég operációs rendszerből (vendég operációs rendszerből). A bővítmény [több különböző helyen](../data-platform.md?toc=%2fazure%2fazure-monitor%2ftoc.json) is elküldheti a telemetria.

Ez a cikk a vendég operációs rendszer teljesítményének mérőszámait mutatja be a Windows rendszerű virtuális gépek (klasszikus) Azure Monitor metrika-adatbázisba való küldésének folyamatát. A diagnosztika 1,11-es verziójától kezdődően a metrikák közvetlenül a Azure Monitor metrikák tárolójába írhatók, ahol a standard platform metrikái már begyűjtése megtörtént. 

Az ezen a helyen való tárolása lehetővé teszi, hogy ugyanazokat a műveleteket hajtsa végre, mint a platform metrikái esetében. A műveletek közé tartozik a közel valós idejű riasztás, a diagramok, az Útválasztás, a hozzáférés egy REST API és egyebek. Korábban a diagnosztikai bővítmény az Azure Storage-ba írt, de nem a Azure Monitor adattárba. 

A cikkben leírt folyamat csak a Windows operációs rendszert futtató klasszikus virtuális gépeken működik.

## <a name="prerequisites"></a>Előfeltételek

- Az Azure-előfizetéséhez [szolgáltatás-rendszergazdának vagy társ-rendszergazdának](../../cost-management-billing/manage/add-change-subscription-administrator.md) kell lennie. 

- Az előfizetést regisztrálni kell a [Microsoft. ininsights](../../azure-resource-manager/management/resource-providers-and-types.md)szolgáltatásban. 

- [Azure PowerShell](/powershell/azure) vagy [Azure Cloud Shell](../../cloud-shell/overview.md) telepítve kell lennie.

- A virtuális gép erőforrásának olyan régióban kell lennie [, amely támogatja az egyéni metrikákat](./metrics-custom-overview.md#supported-regions).

## <a name="create-a-classic-virtual-machine-and-storage-account"></a>Klasszikus virtuális gép és Storage-fiók létrehozása

1. Hozzon létre egy klasszikus virtuális gépet a Azure Portal használatával.
   ![Klasszikus virtuális gép létrehozása](./media/collect-custom-metrics-guestos-vm-classic/create-classic-vm.png)

1. A virtuális gép létrehozásakor válassza a klasszikus új Storage-fiók létrehozása lehetőséget. Ezt a Storage-fiókot a későbbi lépésekben használjuk.

1. A Azure Portal nyissa meg a **Storage-fiókok** erőforrás panelt. Válassza a **kulcsok** lehetőséget, és jegyezze fel a Storage-fiók nevét és a Storage-fiók kulcsát. Ezt az információt a későbbi lépésekben kell megadnia.
   ![Tároló-hozzáférési kulcsok](./media/collect-custom-metrics-guestos-vm-classic/storage-access-keys.png)

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Hozzon létre egy egyszerű szolgáltatásnevet a Azure Active Directory-bérlőben az [egyszerű szolgáltatásnév létrehozása](../../active-directory/develop/howto-create-service-principal-portal.md)című részben leírtak alapján. A folyamat során a következő lépéseket kell figyelembe vennie: 
- Hozzon létre új ügyfél-titkos kulcsot ehhez az alkalmazáshoz.
- Mentse a kulcsot és az ügyfél-azonosítót a későbbi lépésekben való használatra.

Adja meg az alkalmazásnak a "figyelési metrika közzétevője" engedélyt ahhoz az erőforráshoz, amelynek a metrikáit el szeretné bocsátani. Egy erőforráscsoportot vagy egy teljes előfizetést is használhat.  

> [!NOTE]
> A diagnosztikai bővítmény az egyszerű szolgáltatásnév használatával hitelesíti a Azure Monitor, és metrikákat bocsát ki a klasszikus virtuális géphez.

## <a name="author-diagnostics-extension-configuration"></a>Diagnosztikai bővítmény konfigurációjának létrehozása

1. Készítse elő a diagnosztikai bővítmény konfigurációs fájlját. Ez a fájl határozza meg, hogy mely naplók és teljesítményszámlálók esetében kell gyűjteni a diagnosztikai bővítményt a klasszikus virtuális géphez. Íme egy példa:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
            <Directories scheduledTransferPeriod="PT1M">
                <IISLogs containerName="wad-iis-logfiles" />
                <FailedRequestLogs containerName="wad-failedrequestlogs" />
            </Directories>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
                <PerformanceCounterConfiguration counterSpecifier="\Processor(*)\% Processor Time" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(*)\Disk Read Bytes/sec" sampleRate="PT15S" />
            </PerformanceCounters>
            <WindowsEventLog scheduledTransferPeriod="PT1M">
                <DataSource name="Application!*[System[(Level=1 or Level=2 or Level=3)]]" />
                <DataSource name="Windows Azure!*[System[(Level=1 or Level=2 or Level=3 or Level=4)]]" />
            </WindowsEventLog>
            <CrashDumps>
                <CrashDumpConfiguration processName="WaIISHost.exe" />
                <CrashDumpConfiguration processName="WaWorkerHost.exe" />
                <CrashDumpConfiguration processName="w3wp.exe" />
            </CrashDumps>
            <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Error" />
            <Metrics resourceId="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicCompute/virtualMachines/MyClassicVM">
                <MetricAggregation scheduledTransferPeriod="PT1M" />
                <MetricAggregation scheduledTransferPeriod="PT1H" />
            </Metrics>
        </DiagnosticMonitorConfiguration>
        <SinksConfig>
        </SinksConfig>
        </WadCfg>
        <StorageAccount />
    </PublicConfig>
    <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <StorageAccount name="" endpoint="" />
    </PrivateConfig>
    <IsEnabled>true</IsEnabled>
    </DiagnosticsConfiguration>
    ```
1. A diagnosztikai fájl "SinksConfig" szakaszában Definiáljon egy új Azure Monitor fogadót, a következőképpen:

    ```xml
    <SinksConfig>
        <Sink name="AzMonSink">
            <AzureMonitor>
                <ResourceId>Provide the resource ID of your classic VM </ResourceId>
                <Region>The region your VM is deployed in</Region>
            </AzureMonitor>
        </Sink>
    </SinksConfig>
    ```

1. A konfigurációs fájl azon szakaszában, ahol a gyűjteni kívánt teljesítményszámlálók listája szerepel, a teljesítményszámlálók átirányítása a Azure Monitor fogadó "AzMonSink" értékre.

    ```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
    ```

1. A privát konfigurációban adja meg a Azure Monitor fiókot. Ezután adja hozzá a metrikák kibocsátásához használni kívánt egyszerű szolgáltatásnév adatait.

    ```xml
    <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="" endpoint="" />
        <AzureMonitorAccount>
            <ServicePrincipalMeta>
                <PrincipalId>clientId for your service principal</PrincipalId>
                <Secret>client secret of your service principal</Secret>
            </ServicePrincipalMeta>
        </AzureMonitorAccount>
    </PrivateConfig>
    ```

1. Mentse helyileg a fájlt.

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>A diagnosztikai bővítmény üzembe helyezése a Cloud Service-ben

1. Indítsa el a PowerShellt, és jelentkezzen be.

    ```powershell
    Login-AzAccount
    ```

1. Először állítsa be a környezetet a klasszikus virtuális géphez.

    ```powershell
    $VM = Get-AzureVM -ServiceName <VM’s Service_Name> -Name <VM Name>
    ```

1. Állítsa be a virtuális géppel létrehozott klasszikus Storage-fiók környezetét.

    ```powershell
    $StorageContext = New-AzStorageContext -StorageAccountName <name of your storage account from earlier steps> -storageaccountkey "<storage account key from earlier steps>"
    ```

1.  Állítsa a diagnosztikai fájl elérési útját egy változóra a következő parancs használatával:

    ```powershell
    $diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>”
    ```

1.  Készítse elő a klasszikus virtuális gép frissítését azon diagnosztikai fájllal, amelyen a Azure Monitor fogadó konfigurálva van.

    ```powershell
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $diagconfig -VM $VM -StorageContext $Storage_Context
    ```

1.  Telepítse a frissítést a virtuális gépre a következő parancs futtatásával:

    ```powershell
    Update-AzureVM -ServiceName "ClassicVMWAD7216" -Name "ClassicVMWAD" -VM $VM_Update.VM
    ```

> [!NOTE]
> Továbbra is kötelező megadni a Storage-fiókot a diagnosztikai bővítmény telepítésének részeként. A diagnosztikai konfigurációs fájlban megadott naplók vagy teljesítményszámlálók a megadott Storage-fiókba lesznek írva.

## <a name="plot-the-metrics-in-the-azure-portal"></a>A metrikák ábrázolása a Azure Portal

1.  Nyissa meg az Azure Portalt. 

1.  A bal oldali menüben válassza a **figyelő lehetőséget.**

1.  A **figyelő** panelen válassza a **metrikák** lehetőséget.

    ![Metrikák navigálása](./media/collect-custom-metrics-guestos-vm-classic/navigate-metrics.png)

1. Az erőforrások legördülő menüben válassza ki a klasszikus virtuális gépet.

1. A névterek legördülő menüben válassza az **Azure. VM. Windows. Guest** lehetőséget.

1. A metrikák legördülő menüben válassza a **Memory\Committed bájtok használatban** lehetőséget.
   ![Kirajzolt metrikák](./media/collect-custom-metrics-guestos-vm-classic/plot-metrics.png)


## <a name="next-steps"></a>Következő lépések
- További információ az [Egyéni metrikákkal](./metrics-custom-overview.md)kapcsolatban.