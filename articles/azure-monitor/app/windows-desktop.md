---
title: Windowsos asztali alkalmazások használatának és teljesítményének figyelése
description: A windowsos asztali alkalmazások használatát és teljesítményét az Application Insights segítségével elemezheti.
ms.topic: conceptual
ms.date: 06/11/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 15331494b40021f10c162fba75abf6fe88d2d419
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100583325"
---
# <a name="monitoring-usage-and-performance-in-classic-windows-desktop-apps"></a>Klasszikus windowsos asztali alkalmazások használatának és teljesítményének figyelése

A helyszínen, az Azure-ban és más felhőben üzemeltetett alkalmazások is kihasználhatják az Application Insights előnyeit. Az egyetlen korlát, hogy [engedélyezni kell a kommunikációt](./ip-addresses.md) az Application Insights szolgáltatással. Az univerzális Windows-platformra (UWP) épülő alkalmazásokhoz a [Visual Studio App Center](../app/mobile-center-quickstart.md) használatát javasoljuk.

## <a name="to-send-telemetry-to-application-insights-from-a-classic-windows-application"></a>Klasszikus Windows-alkalmazásból származó telemetriai adatok küldése az Application Insightsba
1. [Hozzon létre egy Application Insights-erőforrást](./create-new-resource.md) az [Azure Portalon](https://portal.azure.com). 
2. Végezze el a kialakítási kulcs másolását.
3. A Visual Studióban szerkessze az alkalmazási projekt NuGet-csomagjait, és vegye fel a Microsoft.ApplicationInsights.WindowsServer csomagot. (Vagy válassza a Microsoft. ApplicationInsights lehetőséget, ha csak az alap API-t szeretné használni, a standard szintű telemetria-gyűjtési modulok nélkül.)
4. Állítsa be a kialakítási kulcsot a kódban:
   
    `TelemetryConfiguration.Active.InstrumentationKey = "`*a kulcs*`";`
   
    vagy az ApplicationInsights.config fájlban (ha telepítette valamelyik normál telemetriai csomagot):
   
    `<InstrumentationKey>`*a kulcs*`</InstrumentationKey>` 
   
    Az ApplicationInsights.config használatakor győződjön meg arról, hogy annak tulajdonságait a következőre állította a Megoldáskezelőben: **Build Action = Content, Copy to Output Directory = Copy**.
5. [Az API-val](./api-custom-events-metrics.md) telemetriai adatokat küldhet.
6. Futtassa az alkalmazást, és tekintse meg a telemetria a Azure Portalban létrehozott erőforrásban.

## <a name="example-code"></a><a name="telemetry"></a>Mintakód

```csharp
using Microsoft.ApplicationInsights;

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnFormClosing(System.Windows.Forms.FormClosingEventArgs e)
        {
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnFormClosing(e);
        }
        
        ...
```

## <a name="override-storage-of-computer-name"></a>Számítógépnév tárolójának felülbírálása

Alapértelmezés szerint ez az SDK összegyűjti és tárolja a rendszer-előállítók telemetria számítógép nevét.

A számítógép nevét a Application Insights [örökölt vállalati (csomóponton belüli) árképzési szinten](./pricing.md#legacy-enterprise-per-node-pricing-tier) használják a belső számlázási célokra. Alapértelmezés szerint, ha telemetria inicializáló használ a felülbíráláshoz `telemetry.Context.Cloud.RoleInstance` , a rendszer külön tulajdonságot `ai.internal.nodeName` küld, amely továbbra is a számítógépnév értéket fogja tartalmazni. Ez az érték nem lesz tárolva a Application Insights telemetria, de belsőleg használatos a betöltéskor, hogy lehetővé váljon a visszamenőleges kompatibilitás az örökölt node-alapú számlázási modellel.

Ha az [örökölt Enterprise (node) árképzési szinten](./pricing.md#legacy-enterprise-per-node-pricing-tier) van, és egyszerűen csak a számítógépnév tárterületét szeretné felülbírálni, használjon egy telemetria inicializáló:

**Az alábbi módon írhat egyéni TelemetryInitializer.**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleInstance))
            {
                // Set custom role name here. Providing an empty string will result
                // in the computer name still be sent via this property.
                  telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```

Hozza létre az inicializálást az `Program.cs` `Main()` alábbi metódusban a kialakítási kulcs beállításával:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

   static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
            //...
        }
```

## <a name="override-transmission-of-computer-name"></a>Számítógépnév átvitelének felülbírálása

Ha nem az [örökölt Enterprise (node) árképzési](./pricing.md#legacy-enterprise-per-node-pricing-tier) szinten található, és teljes mértékben meg szeretné akadályozni, hogy a számítógép nevét tartalmazó összes telemetria el lehessen juttatni, telemetria processzort kell használnia.

### <a name="telemetry-processor"></a>Telemetria processzor

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;


namespace WindowsFormsApp2
{
    public class CustomTelemetryProcessor : ITelemetryProcessor
    {
        private readonly ITelemetryProcessor _next;

        public CustomTelemetryProcessor(ITelemetryProcessor next)
        {
            _next = next;
        }

        public void Process(ITelemetry item)
        {
            if (item != null)
            {
                item.Context.Cloud.RoleInstance = string.Empty;
            }

            _next.Process(item);
        }
    }
}
```

Hozza létre a telemetria processzort az `Program.cs` `Main()` alábbi metódusban a kialakítási kulcs beállításával:

```csharp
using Microsoft.ApplicationInsights.Extensibility;

namespace WindowsFormsApp2
{
    static class Program
    {
        static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
            builder.Use((next) => new CustomTelemetryProcessor(next));
            builder.Build();
            //...
        }
    }
}

```

> [!NOTE]
> Bár a fentiekben leírtak szerint technikailag használhatja a telemetria-processzort, még akkor is, ha az [örökölt Enterprise (node) díjszabási](./pricing.md#legacy-enterprise-per-node-pricing-tier)szinten van, ez a túlszámlázás lehetséges lehetőségét eredményezi, mert a csomópontok díjszabása nem képes megfelelően megkülönböztetni a csomópontokat.

## <a name="next-steps"></a>Következő lépések
* [Irányítópult létrehozása](./overview-dashboard.md)
* [Diagnosztikai keresés](./diagnostic-search.md)
* [Metrikák böngészése](../essentials/metrics-charts.md)
* [Analytics-lekérdezések](../logs/log-query-overview.md)

