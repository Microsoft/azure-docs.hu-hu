---
title: Azure Service Fabric esemény összesítése a EventFlow segítségével
description: Ismerje meg, hogy az Azure Service Fabric-fürtök monitorozásához és diagnosztizálásához a EventFlow segítségével használatával hogyan összesítheti és gyűjtheti az eseményeket.
author: srrengar
ms.topic: conceptual
ms.date: 2/25/2019
ms.author: srrengar
ms.custom: devx-track-csharp
ms.openlocfilehash: a80eea5a50aa7b1e441049eeb2cae381994cd3ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96006336"
---
# <a name="event-aggregation-and-collection-using-eventflow"></a>Események összesítése és gyűjtése a EventFlow segítségével használatával

A [Microsoft Diagnostics EventFlow segítségével](https://github.com/Azure/diagnostics-eventflow) egy csomópontról egy vagy több megfigyelési célhelyre irányíthatja át az eseményeket. Mivel a szolgáltatási projektben NuGet-csomagként szerepel, a EventFlow segítségével-kód és a konfiguráció a szolgáltatással utazik, így nem kell megszüntetnie a Azure Diagnosticsról korábban említett csomópontos konfigurációs problémát. A EventFlow segítségével a szolgáltatási folyamaton belül fut, és közvetlenül a konfigurált kimenetekhez csatlakozik. A közvetlen kapcsolódás miatt a EventFlow segítségével az Azure, a Container és a helyszíni szolgáltatások központi telepítései esetében működik. Ügyeljen arra, hogy a EventFlow segítségével nagy sűrűségű helyzetekben, például egy tárolóban fusson, mivel minden egyes EventFlow segítségével-folyamat külső kapcsolatokat tesz elérhetővé. Így ha több folyamatot is üzemeltet, több kimenő kapcsolat is megadható. Ez nem annyira fontos a Service Fabric-alkalmazások esetében, mert egy folyamat összes replikája `ServiceType` azonos folyamatban van, és ez korlátozza a kimenő kapcsolatok számát. A EventFlow segítségével az események szűrését is biztosítja, így csak a megadott szűrőnek megfelelő események lesznek elküldve.

## <a name="set-up-eventflow"></a>EventFlow segítségével beállítása

A EventFlow segítségével bináris fájljai NuGet-csomagokként érhetők el. Ha EventFlow segítségével szeretne hozzáadni egy Service Fabric szolgáltatási projekthez, kattintson a jobb gombbal a projektre a Megoldáskezelő, és válassza a "NuGet-csomagok kezelése" lehetőséget. Váltson a "Tallózás" lapra, és keressen rá a "" kifejezésre `Diagnostics.EventFlow` :

![EventFlow segítségével NuGet-csomagok a Visual Studio NuGet Package Manager felhasználói felületén](./media/service-fabric-diagnostics-event-aggregation-eventflow/eventflow-nuget.png)

Megjelenik a különböző csomagok listája, amely a "bemenetek" és a "kimenetek" címkével jelenik meg. A EventFlow segítségével különböző naplózási szolgáltatókat és elemzőket támogat. A EventFlow segítségével üzemeltető szolgáltatásnak tartalmaznia kell a megfelelő csomagokat az alkalmazás naplói forrásáról és céljától függően. Az alapszintű ServiceFabric-csomag mellett legalább egy bemeneti és kimeneti beállításra is szükség van. Például a következő csomagokat adhatja hozzá a EventSource-események küldéséhez a Application Insightsba:

* `Microsoft.Diagnostics.EventFlow.Inputs.EventSource` adatok rögzítése a szolgáltatás EventSource Osztályáról, valamint a szabványos EventSources, például a *Microsoft-ServiceFabric-Services* és a *Microsoft-ServiceFabric-Actors* használatával
* `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights` (a naplókat egy Azure Application Insights-erőforrásba fogjuk elküldeni)
* `Microsoft.Diagnostics.EventFlow.ServiceFabric`(engedélyezi a EventFlow segítségével folyamat inicializálását Service Fabric szolgáltatás konfigurációjában, és a diagnosztikai adatok Service Fabric állapot-jelentésként való elküldésével kapcsolatos problémákat jelenti.)

>[!NOTE]
>`Microsoft.Diagnostics.EventFlow.Inputs.EventSource` a csomaghoz a szolgáltatási projektnek a .NET-keretrendszer 4,6-es vagy újabb verziójának kell megcéloznia. A csomag telepítése előtt győződjön meg arról, hogy a megfelelő cél keretrendszert a projekt tulajdonságainál állítja be.

Az összes csomag telepítése után a következő lépés a EventFlow segítségével konfigurálása és engedélyezése a szolgáltatásban.

## <a name="configure-and-enable-log-collection"></a>Napló-gyűjtemény konfigurálása és engedélyezése
A naplók elküldéséhez felelős EventFlow segítségével folyamat egy konfigurációs fájlban tárolt specifikáció alapján jön létre. A `Microsoft.Diagnostics.EventFlow.ServiceFabric` csomag telepíti a nevű EventFlow segítségével-konfigurációs fájlt a `PackageRoot\Config` megoldás mappájába `eventFlowConfig.json` . Ezt a konfigurációs fájlt úgy kell módosítani, hogy az adatokat az alapértelmezett szolgáltatási `EventSource` osztályból, valamint minden más konfigurálni kívánt bemenetről rögzítsen, és adatokat küldjön a megfelelő helyre.

>[!NOTE]
>Ha a projektfájl VisualStudio 2017 formátumú, a `eventFlowConfig.json` fájl nem lesz automatikusan hozzáadva. A probléma megoldásához hozza létre a fájlt a `Config` mappában, és állítsa a Build műveletet a következőre: `Copy if newer` . 

Íme egy példa *eventFlowConfig.jsa* fent említett NuGet-csomagok alapján:
```json
{
  "inputs": [
    {
      "type": "EventSource",
      "sources": [
        { "providerName": "Microsoft-ServiceFabric-Services" },
        { "providerName": "Microsoft-ServiceFabric-Actors" },
        // (replace the following value with your service's ServiceEventSource name)
        { "providerName": "your-service-EventSource-name" }
      ]
    }
  ],
  "filters": [
    {
      "type": "drop",
      "include": "Level == Verbose"
    }
  ],
  "outputs": [
    {
      "type": "ApplicationInsights",
      // (replace the following value with your AI resource's instrumentation key)
      "instrumentationKey": "00000000-0000-0000-0000-000000000000"
    }
  ],
  "schemaVersion": "2016-08-11"
}
```

A szolgáltatás ServiceEventSource neve a `EventSourceAttribute` ServiceEventSource osztályra alkalmazott Name tulajdonság értéke. Ez mind a fájlban van megadva `ServiceEventSource.cs` , amely a szolgáltatási kód részét képezi. A következő kódrészletben például a ServiceEventSource neve *SajátVállalat-Application1-Stateless1*:

```csharp
[EventSource(Name = "MyCompany-Application1-Stateless1")]
internal sealed class ServiceEventSource : EventSource
{
    // (rest of ServiceEventSource implementation)
}
```

Vegye figyelembe, hogy a `eventFlowConfig.json` fájl a szolgáltatás konfigurációs csomagja részét képezi. A fájl módosításai a szolgáltatás teljes vagy csak konfigurációra vonatkozó frissítéseibe belefoglalhatók, a Service Fabric frissítési állapot-ellenőrzésekre és az automatikus visszaállításra is érvényesek, ha frissítési hiba történik. További információ: [Service Fabric alkalmazás frissítése](service-fabric-application-upgrade.md).

A konfiguráció *szűrők* szakasza lehetővé teszi, hogy tovább testreszabja a EventFlow segítségével folyamaton keresztül haladó adatokat a kimeneteken, így bizonyos adatokat elhúzhatja vagy belefoglalhat, vagy módosíthatja az események szerkezetét. A szűréssel kapcsolatos további információkért lásd: [EventFlow segítségével-szűrők](https://github.com/Azure/diagnostics-eventflow#filters).

Az utolsó lépés a EventFlow segítségével folyamatának példánya a szolgáltatás indítási kódjában, a fájlban található `Program.cs` :

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric;
using Microsoft.ServiceFabric.Services.Runtime;

// **** EventFlow namespace
using Microsoft.Diagnostics.EventFlow.ServiceFabric;

namespace Stateless1
{
    internal static class Program
    {
        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>
        private static void Main()
        {
            try
            {
                // **** Instantiate log collection via EventFlow
                using (var diagnosticsPipeline = ServiceFabricDiagnosticPipelineFactory.CreatePipeline("MyApplication-MyService-DiagnosticsPipeline"))
                {

                    ServiceRuntime.RegisterServiceAsync("Stateless1Type",
                    context => new Stateless1(context)).GetAwaiter().GetResult();

                    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless1).Name);

                    Thread.Sleep(Timeout.Infinite);
                }
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
                throw;
            }
        }
    }
}
```

A (z) metódusának paramétereként átadott név a `CreatePipeline` `ServiceFabricDiagnosticsPipelineFactory` EventFlow segítségével-napló gyűjtési folyamatát jelképező *Health entitás* neve. Ezt a nevet akkor használja a rendszer, ha a EventFlow segítségével találkozik és hibát észlel, és a Service Fabric állapot-alrendszer használatával jelenti azt.

### <a name="use-service-fabric-settings-and-application-parameters-in-eventflowconfig"></a>Service Fabric beállítások és alkalmazás-paraméterek használata a eventFlowConfig-ben

A EventFlow segítségével támogatja a Service Fabric beállítások és az alkalmazás paramétereinek használatát a EventFlow segítségével beállításainak konfigurálásához. A következő speciális szintaxissal tekintheti meg Service Fabric beállítások paramétereit:

```json
servicefabric:/<section-name>/<setting-name>
```

`<section-name>` a Service Fabric konfigurációs szakasz neve, és az `<setting-name>` a konfigurációs beállítás, amely megadja a EventFlow segítségével-beállítás konfigurálásához használandó értéket. Ennek módjáról bővebben a [Service Fabric beállítások és az alkalmazás paramétereinek támogatása](https://github.com/Azure/diagnostics-eventflow#support-for-service-fabric-settings-and-application-parameters)című témakörben olvashat.

## <a name="verification"></a>Ellenőrzés

Indítsa el a szolgáltatást, és figyelje meg a hibakeresési kimenet ablakot a Visual Studióban. A szolgáltatás elindítása után meg kell jelennie arról, hogy a szolgáltatás a konfigurált kimenetre küld rekordokat. Navigáljon az Event Analysis and vizualizációs platformra, és ellenőrizze, hogy a naplók megkezdték-e a megjelenítést (eltarthat néhány percig).

## <a name="next-steps"></a>Következő lépések

* [Események elemzése és vizualizáció Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Események elemzése és vizualizáció Azure Monitor naplókkal](service-fabric-diagnostics-event-analysis-oms.md)
* [A EventFlow segítségével dokumentációja](https://github.com/Azure/diagnostics-eventflow)
