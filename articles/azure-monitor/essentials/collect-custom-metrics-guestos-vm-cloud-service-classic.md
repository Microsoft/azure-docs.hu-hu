---
title: Klasszikus Cloud Services metrikák küldése Azure Monitor metrikai adatbázisba
description: Ismerteti a vendég operációs rendszer teljesítményére vonatkozó metrikák küldésének folyamatát a klasszikus Azure-Cloud Services a Azure Monitor metrika-tárolóba.
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.openlocfilehash: 0ef9d8118a8ff1d9fdd69566dd60033f5847f810
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048962"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-services"></a>Vendég operációs rendszer metrikáinak küldése a klasszikus Azure Monitor metrikai tárolóba Cloud Services 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A Azure Monitor [Diagnostics bővítmény](../agents/diagnostics-extension-overview.md)használatával metrikákat és naplókat gyűjthet a virtuális gép, a Cloud Service vagy a Service Fabric fürt részeként futó vendég operációs rendszerből (vendég operációs rendszerből). A bővítmény [több különböző helyen](../data-platform.md?toc=%2fazure%2fazure-monitor%2ftoc.json) is elküldheti a telemetria.

Ez a cikk ismerteti a vendég operációs rendszer teljesítményének mérőszámait a klasszikus Azure Cloud Services a Azure Monitor metrika-tárolóba való küldésének folyamatát. A diagnosztika 1,11-es verziójától kezdődően a metrikák közvetlenül a Azure Monitor metrikák tárolójába írhatók, ahol a standard platform metrikái már begyűjtése megtörtént. 

Az ezen a helyen való tárolás lehetővé teszi, hogy ugyanazokat a műveleteket érheti el, mint a platform metrikái esetében. A műveletek közé tartozik a közel valós idejű riasztás, a diagramok, az Útválasztás, a hozzáférés egy REST API és egyebek.  Korábban a diagnosztikai bővítmény az Azure Storage-ba írt, de nem a Azure Monitor adattárba.  

Az ebben a cikkben ismertetett folyamat csak az Azure Cloud Services teljesítményszámlálói esetében működik. Más egyéni metrikák esetében nem működik. 

## <a name="prerequisites"></a>Előfeltételek

- Az Azure-előfizetéséhez [szolgáltatás-rendszergazdának vagy társ-rendszergazdának](../../cost-management-billing/manage/add-change-subscription-administrator.md) kell lennie. 

- Az előfizetést regisztrálni kell a [Microsoft. ininsights](../../azure-resource-manager/management/resource-providers-and-types.md)szolgáltatásban. 

- [Azure PowerShell](/powershell/azure) vagy [Azure Cloud Shell](../../cloud-shell/overview.md) telepítve kell lennie.

- A felhőalapú szolgáltatásnak olyan régióban kell lennie [, amely támogatja az egyéni metrikákat](./metrics-custom-overview.md#supported-regions).

## <a name="provision-a-cloud-service-and-storage-account"></a>Cloud Service-és Storage-fiók kiépítése 

1. Klasszikus felhőalapú szolgáltatás létrehozása és üzembe helyezése. Az [Azure Cloud Services és a ASP.net első lépéseiben](../../cloud-services/cloud-services-dotnet-get-started.md)megtalálható a klasszikus Cloud Services alkalmazás és üzembe helyezés. 

2. Használhat meglévő Storage-fiókot, vagy üzembe helyezhet egy új Storage-fiókot. A legjobb megoldás, ha a Storage-fiók ugyanabban a régióban található, mint a létrehozott klasszikus felhőalapú szolgáltatás. A Azure Portal nyissa meg a **Storage-fiókok** erőforrás panelt, majd válassza a **kulcsok** lehetőséget. Jegyezze fel a Storage-fiók nevét és a Storage-fiók kulcsát. A későbbi lépések során szükség lesz erre az információra.

   ![Tárfiókkulcsok](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/storage-keys.png)

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása 

Hozzon létre egy egyszerű szolgáltatásnevet a Azure Active Directory-bérlőben a használati portál utasításait követve [hozzon létre egy Azure Active Directory alkalmazást és egyszerű szolgáltatásnevet, amely hozzáférhet az erőforrásokhoz](../../active-directory/develop/howto-create-service-principal-portal.md). Vegye figyelembe, hogy a folyamat a következő lépésekkel halad át: 

- A bejelentkezési URL-címhez bármilyen URL-címet behelyezhet.  
- Hozzon létre új ügyfél-titkos kulcsot ehhez az alkalmazáshoz.  
- Mentse a kulcsot és az ügyfél-azonosítót a későbbi lépésekben való használatra.  

Adja meg az alkalmazás számára az előző lépésben *figyelt metrikák közzétevője* engedélyt arra az erőforrásra, amelynek a metrikáit el szeretné bocsátani. Ha azt tervezi, hogy az alkalmazás használatával több erőforrásra vonatkozó egyéni metrikákat bocsát ki, ezeket az engedélyeket az erőforráscsoport vagy az előfizetés szintjén adhatja meg.  

> [!NOTE]
> A diagnosztikai bővítmény az egyszerű szolgáltatásnév használatával hitelesíti a Azure Monitor és mérőszámokat bocsát ki a felhőalapú szolgáltatáshoz.

## <a name="author-diagnostics-extension-configuration"></a>Diagnosztikai bővítmény konfigurációjának létrehozása 

Készítse elő a diagnosztikai bővítmény konfigurációs fájlját. Ez a fájl határozza meg, hogy mely naplók és teljesítményszámlálók esetében kell gyűjteni a diagnosztikai bővítményt a felhőalapú szolgáltatáshoz. A következő egy példa diagnosztikai konfigurációs fájl:  

```XML
<?xml version="1.0" encoding="utf-8"?> 
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <WadCfg> 
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096"> 
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" /> 
        <Directories scheduledTransferPeriod="PT1M"> 
          <IISLogs containerName="wad-iis-logfiles" /> 
          <FailedRequestLogs containerName="wad-failedrequestlogs" /> 
        </Directories> 
        <PerformanceCounters scheduledTransferPeriod="PT1M"> 
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Page Faults/sec" sampleRate="PT15S" /> 
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

A diagnosztikai fájl "SinksConfig" szakaszában Definiáljon egy új Azure Monitor fogadót: 

```XML
  <SinksConfig> 
    <Sink name="AzMonSink"> 
    <AzureMonitor> 
      <ResourceId>-Provide ClassicCloudService’s Resource ID-</ResourceId> 
      <Region>-Azure Region your Cloud Service is deployed in-</Region> 
    </AzureMonitor> 
    </Sink> 
  </SinksConfig> 
```

Adja hozzá a Azure Monitor fogadót a konfigurációs fájl szakaszához, ahol a gyűjteni kívánt teljesítményszámlálók listáját listázza. Ez a bejegyzés biztosítja, hogy a megadott teljesítményszámlálók a metrikák Azure Monitor legyenek átirányítva. A teljesítményszámlálók igény szerint adhatók hozzá vagy távolíthatók el. 

```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
```

Végül a privát konfigurációban vegyen fel egy *Azure monitor fiók* szakaszt. Adja meg a korábban létrehozott egyszerű szolgáltatásnév ügyfél-azonosítót és titkot. 

```XML
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <StorageAccount name="" endpoint="" /> 
    <AzureMonitorAccount> 
      <ServicePrincipalMeta> 
        <PrincipalId>clientId from step 3</PrincipalId> 
        <Secret>client secret from step 3</Secret> 
      </ServicePrincipalMeta> 
    </AzureMonitorAccount> 
</PrivateConfig> 
```

Mentse helyileg a diagnosztikai fájlt.  

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>A diagnosztikai bővítmény üzembe helyezése a Cloud Service-ben 

Indítsa el a PowerShellt, és jelentkezzen be az Azure-ba. 

```powershell
Login-AzAccount 
```

A korábban létrehozott Storage-fiók adatainak tárolásához használja az alábbi parancsokat. 

```powershell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```

Hasonlóképpen állítsa be a diagnosztikai fájl elérési útját egy változóra a következő parancs használatával:

```powershell
$diagconfig = “<path of the Diagnostics configuration file with the Azure Monitor sink configured>” 
```

Telepítse a diagnosztikai bővítményt a Cloud Service-be a diagnosztikai fájllal a következő paranccsal konfigurált Azure Monitor-gyűjtővel:  

```powershell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```

> [!NOTE] 
> Továbbra is kötelező megadni a Storage-fiókot a diagnosztikai bővítmény telepítésének részeként. A diagnosztikai konfigurációs fájlban megadott naplók vagy teljesítményszámlálók a megadott Storage-fiókba íródnak.  

## <a name="plot-metrics-in-the-azure-portal"></a>Mérőszámok ábrázolása a Azure Portalban 

1. Nyissa meg az Azure Portalt. 

   ![Képernyőfelvétel: a Azure Portal a figyelővel, majd a metrikák kiválasztásával.](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/navigate-metrics.png)

2. A bal oldali menüben válassza a **figyelő lehetőséget.**

3. A **figyelő** panelen válassza a **metrikák előnézet** fület.

4. Az erőforrások legördülő menüben válassza ki a klasszikus felhőalapú szolgáltatást.

5. A névterek legördülő menüben válassza az **Azure. VM. Windows. Guest** lehetőséget. 

6. A metrikák legördülő menüben válassza a **Memory\Committed bájtok használatban** lehetőséget. 

A dimenzió-szűrés és a felosztási képességek segítségével megtekintheti egy adott szerepkör vagy szerepkör-példány által használt teljes memóriát. 

 ![A képernyőfelvételen a metrikák adatai láthatók.](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/metrics-graph.png)

## <a name="next-steps"></a>Következő lépések

- További információ az [Egyéni metrikákkal](./metrics-custom-overview.md)kapcsolatban.