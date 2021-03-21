---
title: Teljesítményszámlálók gyűjtése az Azure Cloud Services (klasszikus) szolgáltatásban | Microsoft Docs
description: Ismerje meg, hogyan derítheti fel, használhatja és hogyan hozhatja létre a teljesítményszámlálók Cloud Servicesban Azure Diagnostics és Application Insights használatával.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: fa5dd61c0764be45cdba68b73a4f55745ee5e55a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100585503"
---
# <a name="collect-performance-counters-for-your-azure-cloud-service-classic"></a>Teljesítményszámlálók gyűjtése az Azure Cloud Service-hez (klasszikus)

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.

A teljesítményszámlálók lehetővé teszik az alkalmazás és a gazdagép teljesítményének nyomon követését. A Windows Server számos különböző teljesítményszámlálókat biztosít a hardverrel, az alkalmazásokkal, az operációs rendszerrel és egyebekkel kapcsolatban. A teljesítményszámlálók Azure-ba való gyűjtésével és küldésével a jobb döntések érdekében elemezheti ezeket az adatokat. 

## <a name="discover-available-counters"></a>Rendelkezésre álló számlálók felderítése

A teljesítményszámláló két részből áll, a készlet nevét (más néven kategóriát) és egy vagy több számlálót. Az elérhető teljesítményszámlálók listáját a PowerShell segítségével szerezheti be:

```powershell
Get-Counter -ListSet * | Select-Object CounterSetName, Paths | Sort-Object CounterSetName

CounterSetName                                  Paths
--------------                                  -----
.NET CLR Data                                   {\.NET CLR Data(*)\SqlClient...
.NET CLR Exceptions                             {\.NET CLR Exceptions(*)\# o...
.NET CLR Interop                                {\.NET CLR Interop(*)\# of C...
.NET CLR Jit                                    {\.NET CLR Jit(*)\# of Metho...
.NET Data Provider for Oracle                   {\.NET Data Provider for Ora...
.NET Data Provider for SqlServer                {\.NET Data Provider for Sql...
.NET Memory Cache 4.0                           {\.NET Memory Cache 4.0(*)\C...
AppV Client Streamed Data Percentage            {\AppV Client Streamed Data ...
ASP.NET                                         {\ASP.NET\Application Restar...
ASP.NET Apps v4.0.30319                         {\ASP.NET Apps v4.0.30319(*)...
ASP.NET State Service                           {\ASP.NET State Service\Stat...
ASP.NET v2.0.50727                              {\ASP.NET v2.0.50727\Applica...
ASP.NET v4.0.30319                              {\ASP.NET v4.0.30319\Applica...
Authorization Manager Applications              {\Authorization Manager Appl...

#... results cut to save space ...
```

A `CounterSetName` tulajdonság a készletet (vagy kategóriát) jelöli, és jól jelzi, hogy a teljesítményszámlálók milyen kapcsolatban állnak a szolgáltatással. A `Paths` tulajdonság a készlet számlálóinak gyűjteményét jelöli. A `Description` számlálóval kapcsolatos további információkért a tulajdonságot is kérheti.

Egy készlet összes számlálójának lekéréséhez használja az értéket, `CounterSetName` és bontsa ki a `Paths` gyűjteményt. Mindegyik elérésiút-tétel egy lekérdezhető számláló. Ha például a készlethez kapcsolódó elérhető számlálókat szeretné lekérni `Processor` , bontsa ki a `Paths` gyűjteményt:

```powershell
Get-Counter -ListSet * | Where-Object CounterSetName -eq "Processor" | Select -ExpandProperty Paths

\Processor(*)\% Processor Time
\Processor(*)\% User Time
\Processor(*)\% Privileged Time
\Processor(*)\Interrupts/sec
\Processor(*)\% DPC Time
\Processor(*)\% Interrupt Time
\Processor(*)\DPCs Queued/sec
\Processor(*)\DPC Rate
\Processor(*)\% Idle Time
\Processor(*)\% C1 Time
\Processor(*)\% C2 Time
\Processor(*)\% C3 Time
\Processor(*)\C1 Transitions/sec
\Processor(*)\C2 Transitions/sec
\Processor(*)\C3 Transitions/sec
```

Ezek az egyéni számlálók elérési útjai hozzáadhatók a felhőalapú szolgáltatás által használt diagnosztikai keretrendszerhez. További információ a teljesítményszámláló elérési útjának létrehozásáról: [számláló elérési](/windows/win32/perfctrs/specifying-a-counter-path)útjának megadása.

## <a name="collect-a-performance-counter"></a>Teljesítményszámláló összegyűjtése

A teljesítményszámláló a felhőalapú szolgáltatáshoz Azure Diagnostics vagy Application Insights is felvehető.

### <a name="application-insights"></a>Application Insights

Az Azure Application Insights for Cloud Services lehetővé teszi a gyűjteni kívánt teljesítményszámlálók megadását. Miután [hozzáadta Application Insights a projekthez](../azure-monitor/app/cloudservices.md#sdk), egy **ApplicationInsights.config** nevű konfigurációs fájlt ad hozzá a Visual Studio-projekthez. Ez a konfigurációs fájl határozza meg, hogy milyen típusú információkat Application Insights gyűjt és küld az Azure-nak.

Nyissa meg a **ApplicationInsights.config** fájlt, és keresse meg a **ApplicationInsights**  >  **TelemetryModules** elemet. Minden `<Add>` gyermek elem meghatározza a gyűjteni kívánt telemetria-típust, valamint annak konfigurációját. A teljesítményszámláló telemetria moduljának típusa: `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector` . Ha ez az elem már definiálva van, ne adja hozzá második alkalommal. A gyűjteni kívánt teljesítményszámlálók a nevű csomópont alatt vannak meghatározva `<Counters>` . Íme egy példa, amely a meghajtó-teljesítményszámlálókat gyűjti:

```xml
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">

  <TelemetryModules>

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\LogicalDisk(C:)\Disk Write Bytes/sec" ReportAs="Disk write (C:)" />
        <Add PerformanceCounter="\LogicalDisk(C:)\Disk Read Bytes/sec" ReportAs="Disk read (C:)" />
      </Counters>
    </Add>

  </TelemetryModules>

<!-- ... cut to save space ... -->
```

Az egyes teljesítményszámlálók a `<Add>` alatt elemként jelennek meg `<Counters>` . Az `PerformanceCounter` attribútum határozza meg, hogy melyik teljesítményszámláló legyen összegyűjtve. Az `ReportAs` attribútum a teljesítményszámláló Azure Portal megjelenítendő cím. Minden összegyűjtött teljesítményszámláló egy **Egyéni** kategóriába kerül a portálon. A Azure Diagnosticstól eltérően nem állíthatja be a teljesítményszámlálók összegyűjtésének és az Azure-ba való továbbításának időközét. A Application Insights a teljesítményszámlálók összegyűjtése és küldése percenként történik. 

A Application Insights automatikusan gyűjti a következő teljesítményszámlálókat:

* \Process(?? APP_WIN32_PROC??) \% Processzoridő
* \Memory\Available Bytes
* \.NET CLR-kivételek (??APP_CLR_PROC??)\# az összes kivétel közül másodpercenként
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\% Processor Time

További információ: [rendszerteljesítmény-számlálók Application Insights](../azure-monitor/app/performance-counters.md) és [Application Insights az Azure Cloud Services](../azure-monitor/app/cloudservices.md#performance-counters).

### <a name="azure-diagnostics"></a>Azure Diagnostics

> [!IMPORTANT]
> Noha az összes ilyen adatokat összesíti a rendszer a Storage-fiókba, a portál **nem** biztosít natív módot az adatdiagramhoz. Erősen ajánlott egy másik diagnosztikai szolgáltatást, például az Application Insights-t integrálni az alkalmazásba.

A Cloud Services Azure Diagnostics bővítménye lehetővé teszi a gyűjteni kívánt teljesítményszámlálók megadását. Azure Diagnostics beállításához tekintse meg a [Cloud Service-figyelés áttekintése](cloud-services-how-to-monitor.md#setup-diagnostics-extension)című témakört.

A gyűjteni kívánt teljesítményszámlálók a **Diagnostics. wadcfgx** fájlban vannak meghatározva. A Visual Studióban nyissa meg ezt a fájlt (ez a szerepkör definiálva van), és keresse meg a **DiagnosticsConfiguration**  >  **PublicConfig**  >  **WadCfg**  >  **DiagnosticMonitorConfiguration**  >  **PerformanceCounters** elemét. Adjon hozzá egy új **PerformanceCounterConfiguration** elemet gyermekként. Ez az elem két attribútummal rendelkezik: `counterSpecifier` és `sampleRate` . Az `counterSpecifier` attribútum határozza meg, hogy melyik rendszerteljesítmény-számláló készletet (az előző szakaszban vázolt) kell összegyűjteni. Az `sampleRate` érték azt jelzi, hogy az adott érték milyen gyakran van lekérdezve. Egészében az összes teljesítményszámláló átkerül az Azure-ba a szülő `PerformanceCounters` elem `scheduledTransferPeriod` attribútumérték alapján.

A `PerformanceCounters` Schema elemről a [Azure Diagnostics sémában](../azure-monitor/agents/diagnostics-extension-schema-windows.md#performancecounters-element)talál további információt.

Az attribútum által meghatározott időszak `sampleRate` az XML-időtartam adattípust használja a teljesítményszámláló lekérdezési gyakoriságának jelzésére. Az alábbi példában a ráta értéke `PT3M` , ami azt jelenti, hogy a következő `[P]eriod[T]ime[3][M]inutes` három percenként történik:.

A és a definiálásával kapcsolatos további információkért `sampleRate` `scheduledTransferPeriod` tekintse meg az **időtartam adattípus** szakaszát a [W3 XML dátum és idő dátum típusok](https://www.w3schools.com/XML/schema_dtypes_date.asp) oktatóanyagában.

```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig>
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">

        <!-- ... cut to save space ... -->

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />

          <!-- This is a new perf counter which will track the C: disk read activity in bytes per second, every minute -->
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(C:)\Disk Read Bytes/sec" sampleRate="PT1M" />

        </PerformanceCounters>
      </DiagnosticMonitorConfiguration>
    </WadCfg>
    
    <!-- ... cut to save space ... -->

  </PublicConfig>
</DiagnosticsConfiguration>
```

## <a name="create-a-new-perf-counter"></a>Új teljesítményszámláló-számláló létrehozása

Új teljesítményszámláló hozható létre és használható a kód alapján. Az új teljesítményszámláló létrehozására szolgáló kódnak emelt szintűnek kell lennie, ellenkező esetben sikertelen lesz. A Cloud Service `OnStart` indítási kódja képes a teljesítményszámláló létrehozására, amely megköveteli, hogy a szerepkört egy emelt szintű környezetben futtassa. Vagy létrehozhat egy emelt szintű indítási feladatot, amely létrehozza a teljesítményszámláló értékét. További információ az indítási feladatokról: [indítási feladatok konfigurálása és futtatása felhőalapú szolgáltatáshoz](cloud-services-startup-tasks.md).

Ha a szerepkört emelt szintű futtatásra szeretné konfigurálni, adjon hozzá egy `<Runtime>` elemet a [. csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) fájlhoz.

```xml
<ServiceDefinition name="CloudServiceLoadTesting" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Large">

    <!-- ... cut to save space ... -->

    <Runtime executionContext="elevated">
      
    </Runtime>

    <!-- ... cut to save space ... -->

  </WorkerRole>
</ServiceDefinition>
```

Létrehozhat és regisztrálhat egy új teljesítményszámláló néhány sornyi kóddal. Használja a `System.Diagnostics.PerformanceCounterCategory.Create` módszer túlterhelését, amely létrehozza a kategóriát és a számlálót is. A következő kód először ellenőrzi, hogy létezik-e a kategória, és ha hiányzik, a létrehozza a kategóriát és a számlálót is.

```csharp
using System.Diagnostics;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRoleWithSBQueue1
{
    public class WorkerRole : RoleEntryPoint
    {
        // Perf counter variable representing times service was used.
        private PerformanceCounter counterServiceUsed;

        public override bool OnStart()
        {
            // ... Other startup code here ...

            // Define the category and counter names.
            string perfCounterCatName = "MyService";
            string perfCounterName = "Times Used";

            // Create the counter if needed. Our counter category only has a single counter.
            // Both the category and counter are created in the same method call.
            if (!PerformanceCounterCategory.Exists(perfCounterCatName))
            {
                PerformanceCounterCategory.Create(perfCounterCatName, "Collects information about the cloud service.", 
                                                  PerformanceCounterCategoryType.SingleInstance, 
                                                  perfCounterName, "How many times the cloud service was used.");
            }

            // Get reference to our counter
            counterServiceUsed = new PerformanceCounter(perfCounterCatName, perfCounterName);
            counterServiceUsed.ReadOnly = false;
            
            return base.OnStart();
        }

        // ... cut class code to save space
    }
}
```

Ha a számlálót szeretné használni, hívja meg a `Increment` vagy a `IncrementBy` metódust.

```csharp
// Increase the counter by 1
counterServiceUsed.Increment();
```

Most, hogy az alkalmazás használja az egyéni számlálót, konfigurálnia kell Azure Diagnostics vagy Application Insights a számláló nyomon követéséhez.


### <a name="application-insights"></a>Application Insights

Ahogy azt korábban említettük, a Application Insights teljesítményszámlálók a **ApplicationInsights.config** fájlban vannak meghatározva. Nyissa meg **ApplicationInsights.config** és keresse meg a **ApplicationInsights**  >  **TelemetryModules**  >  **hozzáadása**  >   elemet. Hozzon létre egy `<Add>` alárendelt elemet, és állítsa be az `PerformanceCounter` attribútumot a kódban létrehozott teljesítményszámláló kategóriájára és nevére. Állítsa az `ReportAs` attribútumot egy rövid névre, amelyet látni szeretne a portálon.

```xml
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">

  <TelemetryModules>

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <!-- ... cut other perf counters to save space ... -->

        <!-- This new perf counter matches the [category name]\[counter name] defined in your code -->
        <Add PerformanceCounter="\MyService\Times Used" ReportAs="Service used counter" />
      </Counters>
    </Add>

  </TelemetryModules>

<!-- ... cut to save space ... -->
```

### <a name="azure-diagnostics"></a>Azure Diagnostics

Ahogy azt korábban említettük, a gyűjteni kívánt teljesítményszámlálók a **Diagnostics. wadcfgx** fájlban vannak meghatározva. A Visual Studióban nyissa meg ezt a fájlt (ez a szerepkör definiálva van), és keresse meg a **DiagnosticsConfiguration**  >  **PublicConfig**  >  **WadCfg**  >  **DiagnosticMonitorConfiguration**  >  **PerformanceCounters** elemét. Adjon hozzá egy új **PerformanceCounterConfiguration** elemet gyermekként. Állítsa be az `counterSpecifier` attribútumot a kódban létrehozott teljesítményszámláló kategóriájára és nevére. 

```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig>
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">

        <!-- ... cut to save space ... -->

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <!-- ... cut other perf counters to save space ... -->
          
          <!-- This new perf counter matches the [category name]\[counter name] defined in your code -->
          <PerformanceCounterConfiguration counterSpecifier="\MyService\Times Used" sampleRate="PT1M" />

        </PerformanceCounters>
      </DiagnosticMonitorConfiguration>
    </WadCfg>
    
    <!-- ... cut to save space ... -->

  </PublicConfig>
</DiagnosticsConfiguration>
```

## <a name="more-information"></a>További információ

- [Application Insights az Azure Cloud Servicesben](../azure-monitor/app/cloudservices.md#performance-counters)
- [Rendszerteljesítmény-számlálók a Application Insightsban](../azure-monitor/app/performance-counters.md)
- [Számláló elérési útjának megadása](/windows/win32/perfctrs/specifying-a-counter-path)
- [Azure Diagnostics séma – teljesítményszámlálók](../azure-monitor/agents/diagnostics-extension-schema-windows.md#performancecounters-element)