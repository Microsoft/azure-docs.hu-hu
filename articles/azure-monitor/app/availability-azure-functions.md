---
title: Egyéni rendelkezésre állási tesztek létrehozása és futtatása Azure Functions használatával
description: Ez a dokumentum azt ismerteti, hogyan hozhat létre egy Azure-függvényt a TrackAvailability () használatával, amely rendszeres időközönként a TimerTrigger függvényben megadott konfigurációnak megfelelően fog futni. A teszt eredményét a rendszer elküldi a Application Insights-erőforrásnak, ahol a rendelkezésre állási eredmények adataira vonatkozó lekérdezéssel és riasztással kapcsolatos értesítéseket fog kapni. A testreszabott tesztek lehetővé teszik az összetettebb rendelkezésre állási tesztek megírását, mint amennyi a portál felhasználói felületén lehetséges, figyelheti az alkalmazást az Azure-VNET belül, módosíthatja a végponti címeket, vagy létrehozhat egy rendelkezésre állási tesztet, ha az nem érhető el a régióban.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 98d9eaadb31ffdeabe85752f7c76bdd4f7c0d4f3
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589936"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Egyéni rendelkezésre állási tesztek létrehozása és futtatása Azure Functions használatával

Ez a cikk bemutatja, hogyan hozhat létre egy Azure-függvényt a TrackAvailability () használatával, amely rendszeres időközönként, a TimerTrigger függvényben megadott konfiguráció alapján, saját üzleti logikával fog futni. A teszt eredményét a rendszer elküldi a Application Insights-erőforrásnak, ahol a rendelkezésre állási eredmények adataira vonatkozó lekérdezéssel és riasztással kapcsolatos értesítéseket fog kapni. Ez lehetővé teszi, hogy a portálon a [rendelkezésre állási monitorozáshoz](./monitor-web-app-availability.md) hasonló testreszabott teszteket hozzon létre. A testreszabott tesztek lehetővé teszik összetettebb rendelkezésre állási tesztek megírását, mint amennyi a portál felhasználói felületén lehetséges, figyelheti az alkalmazást az Azure-VNET, módosíthatja a végponti címeket, vagy létrehozhat egy rendelkezésre állási tesztet, még akkor is, ha ez a funkció nem érhető el a régióban.

> [!NOTE]
> Ez a példa kizárólag arra szolgál, hogy megmutassa, hogyan működik a TrackAvailability () API-hívás egy Azure-függvényen belül. Nem kell megírnia a mögöttes HTTP-teszt kódját/üzleti logikáját, amely a teljes funkcionalitású rendelkezésre állási teszthez szükséges lenne. Ha ezt a példát mutatja be, alapértelmezés szerint létre fog hozni egy rendelkezésre állási tesztet, amely mindig hibát eredményez.

## <a name="create-timer-triggered-function"></a>Időzítő által aktivált függvény létrehozása

- Ha Application Insights erőforrással rendelkezik:
    - Alapértelmezés szerint a Azure Functions egy Application Insights-erőforrást hoz létre, de ha a már létrehozott erőforrások valamelyikét szeretné használni, akkor a létrehozás során meg kell adnia.
    - Kövesse az [Azure functions erőforrás és időzítő által aktivált függvény létrehozásához](../../azure-functions/functions-create-scheduled-function.md) szükséges lépéseket (leállítás a tisztítás előtt) a következő lehetőségekkel.
        -  Kattintson a felül található **figyelés** fülre.

            ![ Hozzon létre egy Azure Functions alkalmazást a saját alkalmazás-keresési erőforrással](media/availability-azure-functions/create-function-app.png)

        - Jelölje be a Application Insights legördülő listát, és írja be vagy válassza ki az erőforrás nevét.

            ![Meglévő Application Insights erőforrás kiválasztása](media/availability-azure-functions/app-insights-resource.png)

        - Válassza a **Felülvizsgálat + létrehozás** lehetőséget
- Ha még nem rendelkezik olyan Application Insights erőforrással, amelyet az időzítő által aktivált függvényhez még nem hozott létre:
    - Alapértelmezés szerint a Azure Functions-alkalmazás létrehozásakor létrejön egy Application Insights erőforrás.
    - Kövesse az [Azure functions erőforrás és időzítő által aktivált függvény létrehozásával](../../azure-functions/functions-create-scheduled-function.md) kapcsolatos utasításokat (leállítás a tisztítás előtt).

## <a name="sample-code"></a>Mintakód

Másolja az alábbi kódot a Run. CSX fájlba (Ezzel felülírja a már meglévő kódot). Ehhez lépjen a Azure Functions alkalmazásba, és válassza ki a bal oldali időzítő trigger funkciót.

>[!div class="mx-imgBorder"]
>![Az Azure-függvény Run. CSX Azure Portal](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> A használni kívánt végpont címe: `EndpointAddress= https://dc.services.visualstudio.com/v2/track` . Kivéve, ha az erőforrás olyan régióban található, mint Azure Government vagy az Azure China, ahol ebben az esetben az [alapértelmezett végpontok felülbírálásával](./custom-endpoints.md#regions-that-require-endpoint-modification) és a régió megfelelő telemetria-csatornájának végpontjának megadásával foglalkozó cikkben tájékozódhat.

```C#
#load "runAvailabilityTest.csx"
 
using System;
using System.Diagnostics;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
 
// The Application Insights Instrumentation Key can be changed by going to the overview page of your Function App, selecting configuration, and changing the value of the APPINSIGHTS_INSTRUMENTATIONKEY Application setting.
// DO NOT replace the code below with your instrumentation key, the key's value is pulled from the environment variable/application setting key/value pair.
private static readonly string instrumentationKey = Environment.GetEnvironmentVariable("APPINSIGHTS_INSTRUMENTATIONKEY");
 
//[CONFIGURATION_REQUIRED]
// If your resource is in a region like Azure Government or Azure China, change the endpoint address accordingly.
// Visit https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification for more details.
private const string EndpointAddress = "https://dc.services.visualstudio.com/v2/track";
 
private static readonly TelemetryConfiguration telemetryConfiguration = new TelemetryConfiguration(instrumentationKey, new InMemoryChannel { EndpointAddress = EndpointAddress });
private static readonly TelemetryClient telemetryClient = new TelemetryClient(telemetryConfiguration);
 
public async static Task Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Entering Run at: {DateTime.Now}");
 
    if (myTimer.IsPastDue)
    {
        log.LogWarning($"[Warning]: Timer is running late! Last ran at: {myTimer.ScheduleStatus.Last}");
    }
 
    // [CONFIGURATION_REQUIRED] provide {testName} accordingly for your test function
    string testName = "AvailabilityTestFunction";
 
    // REGION_NAME is a default environment variable that comes with App Service
    string location = Environment.GetEnvironmentVariable("REGION_NAME");
 
    log.LogInformation($"Executing availability test run for {testName} at: {DateTime.Now}");
    string operationId = Guid.NewGuid().ToString("N");
 
    var availability = new AvailabilityTelemetry
    {
        Id = operationId,
        Name = testName,
        RunLocation = location,
        Success = false
    };
 
    var stopwatch = new Stopwatch();
    stopwatch.Start();
 
    try
    {
        await RunAvailbiltyTestAsync(log);
        availability.Success = true;
    }
    catch (Exception ex)
    {
        availability.Message = ex.Message;
 
        var exceptionTelemetry = new ExceptionTelemetry(ex);
        exceptionTelemetry.Context.Operation.Id = operationId;
        exceptionTelemetry.Properties.Add("TestName", testName);
        exceptionTelemetry.Properties.Add("TestLocation", location);
        telemetryClient.TrackException(exceptionTelemetry);
    }
    finally
    {
        stopwatch.Stop();
        availability.Duration = stopwatch.Elapsed;
        availability.Timestamp = DateTimeOffset.UtcNow;
 
        telemetryClient.TrackAvailability(availability);
        // call flush to ensure telemetry is sent
        telemetryClient.Flush();
    }
}

```

A fájlok megtekintése lehetőségnél kattintson a **Hozzáadás** gombra. Hívja meg az új file **function. Proj** fájlt a következő konfigurációval.

```C#
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights" Version="2.15.0" /> <!-- Ensure you’re using the latest version -->
    </ItemGroup>
</Project>

```

>[!div class="mx-imgBorder"]
>![A jobb gombbal válassza a Hozzáadás lehetőséget. Nevezze el a file function. Proj fájlt.](media/availability-azure-functions/addfile.png)

A fájlok megtekintése lehetőségnél kattintson a **Hozzáadás** gombra. Hívja meg az új **runAvailabilityTest. CSX** fájlt a következő konfigurációval.

```C#
public async static Task RunAvailbiltyTestAsync(ILogger log)
{
    // Add your business logic here.
    throw new NotImplementedException();
}

```

## <a name="check-availability"></a>Rendelkezésre állás keresése

Az összes működésének ellenőrzéséhez tekintse meg a diagramot a Application Insights erőforrás rendelkezésre állás lapján.

> [!NOTE]
> Ha a saját üzleti logikáját implementálta a runAvailabilityTest. CSX-ben, akkor a sikeres eredményeket fogja látni, például az alábbi képernyőképeken, ha nem, akkor a sikertelen találatok jelennek meg. A-ben létrehozott tesztek a `TrackAvailability()` teszt neve mellett **egyéniként** jelennek meg.

>[!div class="mx-imgBorder"]
>![Rendelkezésre állás lap sikeres eredményekkel](media/availability-azure-functions/availability-custom.png)

A végpontok közötti tranzakció részleteinek megtekintéséhez válassza a **sikeres** vagy **sikertelen** művelet elemet a részletezés szakaszban, majd válasszon ki egy mintát. A végpontok közötti tranzakció részleteit úgy is elérheti, ha kijelöl egy adatpontot a diagramon.

>[!div class="mx-imgBorder"]
>![Minta rendelkezésre állási teszt kiválasztása](media/availability-azure-functions/sample.png)

>[!div class="mx-imgBorder"]
>![Végpontok közötti tranzakció részletei](media/availability-azure-functions/end-to-end.png)

Ha mindent futtatott (üzleti logika hozzáadása nélkül), akkor a teszt sikertelen lesz.

## <a name="query-in-logs-analytics"></a>Lekérdezés a naplókban (Analitika)

A naplók (Analitika) használatával megtekintheti a rendelkezésre állási eredményeket, a függőségeket és egyebeket. A naplókról további információt a [log Query áttekintése című](../logs/log-query-overview.md)témakörben találhat.

>[!div class="mx-imgBorder"]
>![Rendelkezésre állási eredmények](media/availability-azure-functions/availabilityresults.png)

>[!div class="mx-imgBorder"]
>![Képernyőfelvétel: az új lekérdezés lap, amelynek függőségei a 50-ra korlátozódnak.](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>Következő lépések

- [Alkalmazástérkép](./app-map.md)
- [Tranzakció diagnosztikája](./transaction-diagnostics.md)

