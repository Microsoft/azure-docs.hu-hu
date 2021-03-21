---
title: Alkalmazás-hozzárendelés az Azure Application Insightsban | Microsoft Docs
description: Összetett alkalmazás-topológiák figyelése az alkalmazás-hozzárendeléssel
ms.topic: conceptual
ms.date: 03/15/2019
ms.custom: devx-track-csharp
ms.reviewer: sdash
ms.openlocfilehash: db8c84334bfce52d34b9fadf73bb2b070fa93a70
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100007108"
---
# <a name="application-map-triage-distributed-applications"></a>Alkalmazás-hozzárendelés: elosztott alkalmazások osztályozása

Az Alkalmazástérkép segítségével felderítheti a teljesítményt rontó szűk keresztmetszeteket és gyakran meghibásodó helyeket az elosztott alkalmazás összetevőinek mindegyikében. A Térkép minden csomópontja egy alkalmazás-összetevőt vagy annak függőségeit jelöli. és az állapot KPI és a riasztások állapota. A bármely összetevőtől kezdve a részletesebb diagnosztika, például a Application Insights események elemre kattintva végezhető el. Ha az alkalmazás az Azure-szolgáltatásokat használja, akkor az Azure Diagnostics szolgáltatásra is kattinthat, például SQL Database Advisor javaslatokat.

## <a name="what-is-a-component"></a>Mi az összetevő?

Az összetevők egymástól függetlenül telepíthetők a Distributed/-Services-alkalmazás részeként. A fejlesztőknek és az operatív csapatoknak kód szintű láthatósággal vagy az alkalmazás-összetevők által generált telemetria való hozzáféréssel kell rendelkezniük. 

* Az összetevők különböznek a "megfigyelt" külső függőségek, például az sqltól, a EventHub stb., amelyek nem férhetnek hozzá a csapatához vagy szervezetéhez (kód vagy telemetria).
* Az összetevők tetszőleges számú kiszolgáló/szerepkör/tároló példányon futnak.
* Az összetevők különálló Application Insights rendszerállapot-kulcsok (még akkor is, ha az előfizetések eltérőek) vagy a különböző szerepkörök, amelyek egyetlen Application Insights kialakítási kulcsnak vannak kialakítva. Az előnézet-leképezési élmény az összetevőket mutatja, függetlenül attól, hogy milyen módon vannak beállítva.

## <a name="composite-application-map"></a>Összetett alkalmazás-hozzárendelés

Az alkalmazás teljes topológiája a kapcsolódó alkalmazás-összetevők több szintjén is megtekinthető. Az összetevők lehetnek különbözőek Application Insights erőforrásai, vagy egy adott erőforrás különböző szerepkörei. Az alkalmazás-hozzárendelés a kiszolgálók között a telepített Application Insights SDK-val végrehajtott HTTP-függőségi hívásokkal megkeresi az összetevőket. 

Ez a élmény az összetevők fokozatos felderítésével kezdődik. Amikor először tölti be az alkalmazás-hozzárendelést, a rendszer elindítja a lekérdezések egy halmazát, hogy felderítse az ehhez az összetevőhöz kapcsolódó összetevőket. A bal felső sarokban található gomb a felderített alkalmazásban lévő összetevők számával fog frissülni. 

Ha a "Térkép-összetevők frissítése" gombra kattint, a Térkép frissül az addig felderített összes összetevővel. Az alkalmazás összetettségétől függően ez egy percet is igénybe vehet.

Ha az összes összetevő szerepkör egyetlen Application Insights erőforráson belül van, akkor ez a felderítési lépés nem szükséges. Az ilyen alkalmazások kezdeti terhelése az összes összetevőjét tartalmazni fogja.

![A képernyőképen egy példa látható az alkalmazás-hozzárendelésre.](media/app-map/app-map-001.png)

Ennek a tapasztalatnak az egyik fő célja, hogy képes legyen a több száz összetevővel rendelkező összetett topológiák megjelenítésére.

Kattintson bármelyik összetevőre a kapcsolódó információk megjelenítéséhez, és lépjen az adott összetevő teljesítményére és meghibásodására vonatkozó osztályozási felületre.

![Flyout](media/app-map/application-map-002.png)

### <a name="investigate-failures"></a>Hibák vizsgálata

Válassza a **hibák vizsgálata** lehetőséget a hibák ablaktábla elindításához.

![Képernyőkép a hibák kivizsgálásáról gomb](media/app-map/investigate-failures.png)

![Képernyőfelvétel a hibákról](media/app-map/failures.png)

### <a name="investigate-performance"></a>Teljesítmény vizsgálata

A teljesítménnyel kapcsolatos problémák elhárításához válassza a **teljesítmény vizsgálata** lehetőséget.

![A teljesítmény vizsgálata gomb képernyőképe](media/app-map/investigate-performance.png)

![A teljesítmény élményét bemutató képernyőkép](media/app-map/performance.png)

### <a name="go-to-details"></a>Ugrás a részletekre

Válassza az **Ugrás a részletekhez** lehetőséget a végpontok közötti tranzakciós élmény megismeréséhez, amely a hívási verem szintjéhez nyújt nézeteket.

![Képernyőkép a részletekről gomb](media/app-map/go-to-details.png)

![Képernyőkép a végpontok közötti tranzakció részleteiről](media/app-map/end-to-end-transaction.png)

### <a name="view-logs-analytics"></a>Naplók megtekintése (Analitika)

Az alkalmazások adatai lekérdezéséhez és kivizsgálásához kattintson a **megtekintés a naplókban (Analitika)** elemre.

![Képernyőkép a nézetről az Analytics gombon](media/app-map/view-logs.png)

![Képernyőkép az elemzési élményről. Az elmúlt 12 órában egy kérelem átlagos válaszának időtartamát összefoglaló grafikon.](media/app-map/log-analytics.png)

### <a name="alerts"></a>Riasztások

Az aktív riasztások és a riasztások indítását kiváltó alapul szolgáló szabályok megtekintéséhez válassza a **riasztások** lehetőséget.

![A riasztások gomb képernyőképe](media/app-map/alerts.png)

![Képernyőkép az elemzési élményről](media/app-map/alerts-view.png)

## <a name="set-or-override-cloud-role-name"></a>Felhőbeli szerepkör nevének beállítása vagy felülbírálása

Az alkalmazás-hozzárendelés a **Felhőbeli szerepkör neve** tulajdonságot használja a térképen található összetevők azonosítására. A Felhőbeli szerepkör nevének manuális beállítása vagy felülbírálása, valamint az alkalmazás-hozzárendelésben megjelenített elemek módosítása:

> [!NOTE]
> Az Application Insights SDK vagy az ügynök automatikusan hozzáadja a Cloud role Name tulajdonságot egy Azure App Service-környezetben található összetevők által kibocsátott telemetria.

# <a name="netnetcore"></a>[.NET/. NetCore](#tab/net)

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
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here
                telemetry.Context.Cloud.RoleName = "Custom RoleName";
                telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```

**ASP.NET-alkalmazások: az inicializáló betöltése az aktív TelemetryConfiguration**

ApplicationInsights.config:

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

A webalkalmazások ASP.NET egy alternatív módszer, amely az inicializálást a kódban hozza létre, például a Global. aspx. cs fájlban:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
    }
```

> [!NOTE]
> Az inicializálás a `ApplicationInsights.config` vagy a használatával `TelemetryConfiguration.Active` való hozzáadása ASP.net Core alkalmazások esetében nem érvényes. 

**ASP.NET Core alkalmazások: az inicializáló betöltése a TelemetryConfiguration**

[ASP.net Core](asp-net-core.md#adding-telemetryinitializers) alkalmazások esetében az új hozzáadását `TelemetryInitializer` a függőségi injektálási tárolóba való hozzáadásával végezheti el, az alább látható módon. Ez az `ConfigureServices` osztály metódusában történik `Startup.cs` .

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

# <a name="java"></a>[Java](#tab/java)

**Java-ügynök**

A [Java-ügynök 3,0](./java-in-process-agent.md) a Felhőbeli szerepkör neve a következőképpen van beállítva:

```json
{
  "role": {
    "name": "my cloud role name"
  }
}
```

A Felhőbeli szerepkör nevét a környezeti változó használatával is beállíthatja ```APPLICATIONINSIGHTS_ROLE_NAME``` .

**Java SDK**

Ha az SDK-t használja, kezdve Application Insights Java SDK 2.5.0-vel, megadhatja a Felhőbeli szerepkör nevét a fájl hozzáadásával `<RoleName>` `ApplicationInsights.xml` , például:

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
   <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
   <RoleName>** Your role name **</RoleName>
   ...
</ApplicationInsights>
```

Ha a Spring Boott a Application Insights Spring boot Starter használatával használja, az egyetlen szükséges módosítás az alkalmazás egyéni nevének beállítása az Application. properties fájlban.

`spring.application.name=<name-of-app>`

A Spring boot Starter automatikusan hozzárendeli a Felhőbeli szerepkör nevét a spring.application.name tulajdonsághoz megadott értékhez.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();
appInsights.defaultClient.context.tags["ai.cloud.role"] = "your role name";
appInsights.defaultClient.context.tags["ai.cloud.roleInstance"] = "your role instance";
```

### <a name="alternate-method-for-nodejs"></a>Node.js alternatív metódusa

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();

appInsights.defaultClient.addTelemetryProcessor(envelope => {
    envelope.tags["ai.cloud.role"] = "your role name";
    envelope.tags["ai.cloud.roleInstance"] = "your role instance"
});
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
appInsights.queue.push(() => {
appInsights.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```

# <a name="python"></a>[Python](#tab/python)

Python esetén a [OpenCensus Python telemetria processzorok](api-filtering-sampling.md#opencensus-python-telemetry-processors) is használhatók.

```python
def callback_function(envelope):
   envelope.tags['ai.cloud.role'] = 'new_role_name'
   
# AzureLogHandler
handler.add_telemetry_processor(callback_function)

# AzureExporter
exporter.add_telemetry_processor(callback_function)
```
---

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>A Felhőbeli szerepkör nevének megértése az alkalmazás-hozzárendelés kontextusában

A **Felhőbeli szerepkör nevének** megismerése érdekében hasznos lehet megtekinteni egy olyan alkalmazás-hozzárendelést, amely több felhőalapú szerepkör-névvel rendelkezik:

![Alkalmazás-Térkép képernyőképe](media/app-map/cloud-rolename.png)

Az alkalmazás-hozzárendelés a zöld mezőkben található nevek felett a Felhőbeli szerepkörök neve értékek az adott elosztott alkalmazás különböző szempontjaihoz. Így ehhez az alkalmazáshoz a szerepkörei a következők: `Authentication` , `acmefrontend` , `Inventory Management` , a `Payment Processing Worker Role` . 

Ebben az alkalmazásban a Felhőbeli szerepkörök nevei egy másik egyedi Application Insights erőforrást is jelentenek a saját kialakítási kulcsaik használatával. Mivel ennek az alkalmazásnak a tulajdonosa a négy különböző Application Insights erőforráshoz fér hozzá, az Application Map képes összekeverni az alapul szolgáló kapcsolatok térképét.

A [hivatalos definíciók](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93)esetében:

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

Másik lehetőségként a **Felhőbeli szerepkör-példány** hasznos lehet olyan helyzetekben, ahol a **Felhőbeli szerepkör neve** azt jelzi, hogy a probléma valahol a webes kezelőfelületen fut, de előfordulhat, hogy a webes kezelőfelület több elosztott terhelésű kiszolgálóra is futtatható, így a Kusto-lekérdezéseken keresztül mélyebben megtudhatja, hogy a probléma hatással van-e az összes webes előtér-kiszolgálóra/példányra, vagy csak egy rendkívül fontos

Egy olyan forgatókönyv, amelyben érdemes lehet felülbírálni a Felhőbeli szerepkör példányának értékét, ha az alkalmazás egy olyan tárolóban fut, ahol az egyes kiszolgálók nem feltétlenül elegendő információval szolgálnak az adott probléma megtalálásához.

További információ arról, hogyan bírálható felül a Felhőbeli szerepkör Name tulajdonsága a telemetria inicializálók használatával [: tulajdonságok hozzáadása: ITelemetryInitializer](api-filtering-sampling.md#addmodify-properties-itelemetryinitializer).

## <a name="troubleshooting"></a>Hibaelhárítás

Ha nem tudja, hogy az alkalmazás-hozzárendelés a várt módon működjön, próbálja meg a következő lépéseket:

### <a name="general"></a>Általános kérdések

1. Győződjön meg róla, hogy hivatalosan támogatott SDK-t használ. Előfordulhat, hogy a nem támogatott/közösségi SDK-k nem támogatják a korrelációt.

    A támogatott SDK-k listáját ebben a [cikkben](./platforms.md) találja.

2. Frissítse az összes összetevőt a legújabb SDK-verzióra.

3. Ha a C# használatával Azure Functionst használ, frissítsen a [functions v2](../../azure-functions/functions-versions.md)-re.

4. Ellenőrizze, hogy a [Felhőbeli szerepkör neve](#set-or-override-cloud-role-name) helyesen van-e konfigurálva.

5. Ha valamelyik függőség hiányzik, ellenőrizze, hogy az [automatikusan gyűjtött függőségek](./auto-collect-dependencies.md) listájában szerepel-e. Ha nem, manuálisan úgy is nyomon követheti egy [függőségek nyomon követése hívással](./api-custom-events-metrics.md#trackdependency).

### <a name="too-many-nodes-on-the-map"></a>Túl sok csomópont van a térképen

Az alkalmazás-hozzárendelés egy alkalmazás-csomópontot hoz létre a kérelem telemetria található minden egyes egyedi felhőalapú szerepkör nevéhez, valamint egy függőségi csomópontot a típus, a cél és a Felhőbeli szerepkör minden egyedi kombinációja számára a függőségi telemetria. Ha a telemetria több mint 10 000 csomópont található, az Application Map nem fogja tudni beolvasni az összes csomópontot és hivatkozást, így a Térkép hiányos lesz. Ha ez történik, egy figyelmeztető üzenet jelenik meg a Térkép megtekintésekor.

Emellett az alkalmazás-hozzárendelés legfeljebb 1000 külön nem csoportosított csomópontot támogat. Az alkalmazás-hozzárendelés csökkenti a vizualizációk összetettségét olyan függőségek csoportosításával, amelyek ugyanazzal a típussal és hívóval rendelkeznek, de ha a telemetria túl sok egyedi Felhőbeli szerepkör-névvel vagy túl sok függőségi típussal rendelkezik, a csoportosítás nem lesz elegendő, és a Térkép nem fog tudni megjeleníteni.

A probléma megoldásához módosítania kell a kialakítást, hogy megfelelően állítsa be a Felhőbeli szerepkör nevét, a függőség típusát és a függőségi cél mezőket.

* A függőségi célnak a függőség logikai nevét kell képviselnie. Sok esetben ez egyenértékű a függőség kiszolgálójának vagy erőforrásának nevével. A HTTP-függőségek esetében például az állomásnévre van beállítva. Nem tartalmazhat olyan egyedi azonosítókat vagy paramétereket, amelyek az egyik kérelemből a másikba változnak.

* A függőségi típusnak a függőség logikai típusát kell képviselnie. Például a HTTP, az SQL vagy az Azure Blob jellemző függőségi típusok. Nem tartalmazhat egyedi azonosítókat.

* A Felhőbeli szerepkör nevét a [fenti szakasz](#set-or-override-cloud-role-name)ismerteti.

## <a name="portal-feedback"></a>Portál visszajelzése

A visszajelzések megadásához használja a visszajelzés lehetőséget.

![MapLink – 1 rendszerkép](./media/app-map/14-updated.png)

## <a name="next-steps"></a>Következő lépések

* Ha többet szeretne megtudni a korreláció működéséről Application Insights tekintse meg a [telemetria korrelációs cikkét](correlation.md).
* A [végpontok közötti tranzakció diagnosztikai felülete](transaction-diagnostics.md) összekapcsolja az összes Application Insights figyelt összetevőből származó kiszolgálóoldali telemetria egyetlen nézetbe.
* A ASP.NET Core-és ASP.NET kapcsolatos speciális korrelációs forgatókönyvek esetében tekintse meg az [Egyéni műveletek nyomon követése](custom-operations-tracking.md) című cikket.
