---
title: Hibák és kivételek diagnosztizálása az Azure Application Insights
description: A ASP.NET-alkalmazásokból származó kivételek rögzítése a kérelem telemetria együtt.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 07/11/2019
ms.openlocfilehash: 36e916eabfca8e997fc3d46ff10f6201203457cd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88936503"
---
# <a name="diagnose-exceptions-in-your-web-apps-with-application-insights"></a>Webalkalmazások kivételeinek diagnosztizálása az Application Insightsszal
Az élő webalkalmazásban lévő kivételeket [Application Insights](./app-insights-overview.md). A sikertelen kérelmeket a kivételekkel és más eseményekkel is összekapcsolhatja az ügyfélen és a kiszolgálón is, így gyorsan diagnosztizálhatja az okokat.

## <a name="set-up-exception-reporting"></a>A kivételek jelentésének beállítása
* A kiszolgálói alkalmazás által jelentett kivételek:
  * Azure Web Apps: a [Application Insights bővítmény](./azure-web-apps.md) hozzáadása
  * Azure-beli virtuális gépek és Azure virtuálisgép-méretezési csoport IIS által üzemeltetett alkalmazások: az [alkalmazás figyelési bővítményének](./azure-vm-vmss-apps.md) hozzáadása
  * Telepítse [Application INSIGHTS SDK](./asp-net.md) -t az alkalmazás kódjába, vagy
  * IIS-webkiszolgálók: [Application Insights ügynök](./monitor-performance-live-website-now.md)futtatása; vagy
  * Java-webalkalmazások: a [Java-ügynök](./java-in-process-agent.md) engedélyezése
* Telepítse a [JavaScript-kódrészletet](./javascript.md) a weboldalain a böngészőbeli kivételek elfogásához.
* Néhány alkalmazás-keretrendszerben vagy bizonyos beállításokkal további lépéseket kell elvégeznie, hogy több kivételt kapjon:
  * [Webes űrlapok](#web-forms)
  * [MVC](#mvc)
  * [Webes API 1. *](#web-api-1x)
  * [Webes API 2. *](#web-api-2x)
  * [WCF](#wcf)

  Ez a cikk kifejezetten a .NET-keretrendszerbeli alkalmazásokra összpontosít egy példaként szolgáló nézőpontból. A .NET-keretrendszerrel kapcsolatos néhány módszer elavult a .NET Core SDK. Ha .NET Core-alkalmazással rendelkezik, tekintse meg a [.net Core SDK dokumentációját](./asp-net-core.md) .

## <a name="diagnosing-exceptions-using-visual-studio"></a>Kivételek diagnosztizálása a Visual Studióval
A hibakereséshez nyissa meg az App Solution eszközt a Visual Studióban.

Futtassa az alkalmazást a kiszolgálón vagy a fejlesztői gépen az F5 használatával.

Nyissa meg a Application Insights keresési ablakot a Visual Studióban, és állítsa be az alkalmazás eseményeinek megjelenítéséhez. A hibakeresés során ezt csak a Application Insights gombra kattintva teheti meg.

![Kattintson a jobb gombbal a projektre, és válassza a Application Insights, majd a Megnyitás elemet.](./media/asp-net-exceptions/34.png)

Figyelje meg, hogy a jelentés szűrése csak a kivételek megjelenítéséhez végezhető el.

*Nincsenek kivételek? Lásd: [kivételek rögzítése](#exceptions).*

Kattintson egy kivétel jelentésre a verem nyomkövetésének megjelenítéséhez.
A verem nyomon követése elemre kattintva nyissa meg a megfelelő programkódot.

A kódban figyelje meg, hogy a Codelensben a kivételekkel kapcsolatos információkat jeleníti meg:

![A kivételek Codelensben.](./media/asp-net-exceptions/35.png)

## <a name="diagnosing-failures-using-the-azure-portal"></a>Hibák diagnosztizálása a Azure Portal használatával
A Application Insights a megfigyelt alkalmazásokban előforduló hibák diagnosztizálásához segítséget nyújtó, beszerzett APM-élményt nyújt. A kezdéshez kattintson a hibák lehetőségre a vizsgálat szakaszban található Application Insights erőforrás menüben.
Meg kell jelennie egy teljes képernyős nézetnek, amely megjeleníti a kérések meghibásodási arányának tendenciáit, a hibák számát, és azt, hogy hány felhasználót érint. A jobb oldalon láthatja a kiválasztott sikertelen művelethez kapcsolódó leghasznosabb disztribúciókat, beleértve az első három választ, a három legfontosabb kivételt és a három legfontosabb sikertelen függőségi típust.

![Hibák osztályozása nézet (Operations TAB)](./media/asp-net-exceptions/failures0719.png)

Egyetlen kattintással áttekintheti a reprezentatív mintákat a műveletek egyes részhalmazai számára. Különösen a kivételek diagnosztizálásához kattintson a végpontok közötti tranzakció részletei lapon megjelenő kivételek számára, például az alábbiakra:

![Végpontok közötti tranzakció részletei lap](./media/asp-net-exceptions/end-to-end.png)

**Másik lehetőségként** az adott sikertelen művelet kivételeit is megvizsgálhatja, ha a kivételek teljes nézetét választja, akkor a felül található kivételek lapra vált. Itt láthatja a figyelt alkalmazáshoz összegyűjtött összes kivételt.

*Nincsenek kivételek? Lásd: [kivételek rögzítése](#exceptions).*


## <a name="custom-tracing-and-log-data"></a>Egyéni nyomkövetési és naplózási adatszolgáltatások
Az alkalmazásra vonatkozó diagnosztikai adatgyűjtéshez kódot szúrhat be a saját telemetria-adatai elküldéséhez. Ez a diagnosztikai keresésben a kérelem, az oldal nézet és az egyéb automatikusan összegyűjtött adatok mellett jelenik meg.

Erre számos lehetősége van:

* A [TrackEvent ()](./api-custom-events-metrics.md#trackevent) általában a használati minták figyelésére használatos, de az általa küldött adatok is megjelennek az egyéni események szakaszban a diagnosztikai keresésben. Az események neve, és a karakterlánc-tulajdonságokat és numerikus mérőszámokat is végezhet, amelyeken [szűrheti a diagnosztikai kereséseket](./diagnostic-search.md).
* A [TrackTrace ()](./api-custom-events-metrics.md#tracktrace) lehetővé teszi, hogy több adatot, például post-adatokat küldjön.
* A [TrackException () verem-](#exceptions) nyomkövetéseket küld. [További információ a kivételekről](#exceptions).
* Ha már olyan naplózási keretrendszert használ, mint például a Log4Net vagy a NLog, [rögzítheti ezeket a naplókat](asp-net-trace-logs.md) , és megtekintheti azokat a diagnosztikai keresésben a kérelem és a kivétel adatai mellett.

Az események megjelenítéséhez nyissa meg a bal oldali menü [Keresés](./diagnostic-search.md) elemét, válassza a legördülő menü **eseménytípus** lehetőséget, majd válassza az egyéni esemény, nyomkövetés vagy kivétel lehetőséget.

![Részletezés](./media/asp-net-exceptions/customevents.png)

> [!NOTE]
> Ha az alkalmazása sok telemetriát hoz létre, az adaptív mintavételezési modul automatikusan csökkenti a portálra küldött mennyiséget, és csupán az eseményeket megjelenítő töredékeket küld. Az azonos művelet részét képező események ki lesznek választva, vagy ki lesznek választva a csoportnak, így a kapcsolódó események között lehet navigálni. [További tudnivalók a mintavételezésről.](./sampling.md)
>
>

### <a name="how-to-see-request-post-data"></a>A kérelem utáni információk megjelenítése
A kérelem részletei nem tartalmazzák az alkalmazásba egy POST-hívásban továbbított adatokat. Az adatjelentések meghívásához:

* [Telepítse az SDK](./asp-net.md) -t az alkalmazás projektben.
* A [Microsoft. ApplicationInsights. TrackTrace ()](./api-custom-events-metrics.md#tracktrace)hívásához helyezzen be kódot az alkalmazásba. Küldje el a POST (üzenet) paramétert. Korlátozva van a megengedett méret, ezért csak az alapvető adatmennyiséget kell elküldenie.
* Sikertelen kérelem kivizsgálásakor keresse meg a kapcsolódó nyomkövetéseket.

## <a name="capturing-exceptions-and-related-diagnostic-data"></a><a name="exceptions"></a> Kivételek és kapcsolódó diagnosztikai adat rögzítése
Először nem jelenik meg a portálon az alkalmazás hibáit okozó kivételek. A böngészőre vonatkozó kivételek jelennek meg (ha a [JavaScript SDK](./javascript.md) -t használja a weboldalain). Az IIS azonban a legtöbb kiszolgálói kivételt felveszi, és egy kódot kell megírnia, hogy megtekintse őket.

A következőket teheti:

* A kivételek jelentéséhez szúrja be a kivételeket úgy, hogy a kivételeket a kód beszúrásával **explicit módon naplózza** .
* A **kivételek automatikus rögzítése** a ASP.NET-keretrendszer konfigurálásával. A szükséges hozzáadások eltérőek a különböző típusú keretrendszerekhez.

## <a name="reporting-exceptions-explicitly"></a>Jelentéskészítési kivételek explicit módon
A legegyszerűbb módszer a TrackException () hívásának beszúrása egy kivétel-kezelőben.

```javascript
    try
    { ...
    }
    catch (ex)
    {
      appInsights.trackException(ex, "handler loc",
        {Game: currentGame.Name,
         State: currentGame.State.ToString()});
    }
```

```csharp
    var telemetry = new TelemetryClient();
    ...
    try
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string>
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }
```

```VB
    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)

      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try
```

A tulajdonságok és a mérések paramétereinek megadása nem kötelező, de hasznos lehet a [szűréshez és további információk hozzáadásához](./diagnostic-search.md) . Ha például olyan alkalmazással rendelkezik, amely több játékot is futtathat, megtalálhatja az adott játékhoz kapcsolódó összes kivételről szóló jelentést. Az egyes szótárakhoz tetszőleges számú elemet adhat hozzá.

## <a name="browser-exceptions"></a>Böngészőkivételek
A legtöbb böngészőbeli kivételt jelenteni kell.

Ha a weblap parancsfájlokat tartalmaz a Content Delivery Networks vagy más tartományokból, győződjön meg arról, hogy a parancsfájl címkéje rendelkezik az attribútummal ```crossorigin="anonymous"``` , és hogy a kiszolgáló [CORS-fejléceket](https://enable-cors.org/)küld. Ez lehetővé teszi, hogy lekérje a verem nyomon követését és részleteit a nem kezelt JavaScript-kivételekről ezekből az erőforrásokból.

## <a name="reuse-your-telemetry-client"></a>A telemetria-ügyfél újrafelhasználása

> [!NOTE]
> A TelemetryClient-t ajánlott egyszer létrehozni, és az alkalmazás teljes élettartama során újra felhasználni.

Az alábbi példa a TelemetryClient helyes használatát mutatja be.

```csharp
public class GoodController : ApiController
{
    // OK
    private static readonly TelemetryClient telemetryClient;

    static GoodController()
    {
        telemetryClient = new TelemetryClient();
    }
}
```


## <a name="web-forms"></a>Webes űrlapok
A webes űrlapok esetében a HTTP-modul képes lesz összegyűjteni a kivételeket, ha nincsenek átirányítások konfigurálva a CustomErrors.

Ha azonban aktív átirányítással rendelkezik, adja hozzá a következő sorokat a Application_Error függvényhez a Global. asax. cs. (Adjon hozzá egy Global. asax fájlt, ha még nem rendelkezik ilyennel.)

```csharp
    void Application_Error(object sender, EventArgs e)
    {
      if (HttpContext.Current.IsCustomErrorEnabled && Server.GetLastError () != null)
      {
         var ai = new TelemetryClient(); // or re-use an existing instance

         ai.TrackException(Server.GetLastError());
      }
    }
```
## <a name="mvc"></a>MVC
A Application Insights web SDK 2,6-es verziójától (beta3 és újabb verzióktól) kezdve a Application Insights a nem kezelt kivételeket az MVC 5 + Controller metódusokban automatikusan kiváltva gyűjti. Ha korábban már hozzáadott egy egyéni kezelőt az ilyen kivételek nyomon követéséhez (az alábbi példákban leírtak szerint), akkor előfordulhat, hogy a kivételek kétszeres nyomon követésének megakadályozásához eltávolíthatja azt.

Számos esetben a kivétel szűrői nem kezelhetik. Például:

* A vezérlőkonstruktorokban történt kivételek.
* Az üzenetkezelőkben történt kivételek.
* Az útválasztás során történt kivételek.
* A választartalmak szerializálása során történt kivételek.
* Az alkalmazás indítása során történt kivételek.
* A háttérfeladatokban történt kivételek.

Az alkalmazás által *kezelt* összes kivételt manuálisan kell nyomon követni.
A vezérlőkből származó nem kezelt kivételek általában 500 "belső kiszolgálóhiba" választ eredményeznek. Ha az ilyen választ a kezelt kivétel (vagy egyáltalán nem kivétel) eredményeképpen manuálisan készíti el, azt a 500-es megfelelő kérelem telemetria nyomon követheti `ResultCode` , azonban Application INSIGHTS SDK nem tudja követni a megfelelő kivételt.

### <a name="prior-versions-support"></a>Korábbi verziók támogatása
Ha a Application Insights web SDK 2,5-as (és korábbi) MVC 4 (és korábbi) változatát használja, a kivételek nyomon követéséhez tekintse meg az alábbi példákat.

Ha a [CustomErrors](/previous-versions/dotnet/netframework-4.0/h0hfz6fc(v=vs.100)) -konfiguráció `Off` , akkor a kivételek lesznek elérhetők a [http-modul](/previous-versions/dotnet/netframework-3.0/ms178468(v=vs.85)) összegyűjtéséhez. Ha azonban az `RemoteOnly` (alapértelmezett) vagy `On` a, akkor a rendszer törli a kivételt, és nem lesz elérhető Application Insights számára az automatikus gyűjtéshez. A [System. Web. MVC. HandleErrorAttribute osztály](/dotnet/api/system.web.mvc.handleerrorattribute?view=aspnet-mvc-5.2)felülbírálásával és a felülbírált osztály alkalmazásával az alábbi, a különböző MVC-verzióknál ([GitHub-forrás](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs)) látható módon kijavíthatja a következőt:

```csharp
    using System;
    using System.Web.Mvc;
    using Microsoft.ApplicationInsights;

    namespace MVC2App.Controllers
    {
      [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
      public class AiHandleErrorAttribute : HandleErrorAttribute
      {
        public override void OnException(ExceptionContext filterContext)
        {
            if (filterContext != null && filterContext.HttpContext != null && filterContext.Exception != null)
            {
                //If customError is Off, then AI HTTPModule will report the exception
                if (filterContext.HttpContext.IsCustomErrorEnabled)
                {   //or reuse instance (recommended!). see note above
                    var ai = new TelemetryClient();
                    ai.TrackException(filterContext.Exception);
                }
            }
            base.OnException(filterContext);
        }
      }
    }
```

#### <a name="mvc-2"></a>MVC 2
Cserélje le a HandleError attribútumot a vezérlők új attribútumára.

```csharp
    namespace MVC2App.Controllers
    {
        [AiHandleError]
        public class HomeController : Controller
        {
    ...
```

[Sample](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions)

#### <a name="mvc-3"></a>MVC 3
Regisztráljon globális `AiHandleErrorAttribute` szűrőként a Global. asax. cs:

```csharp
    public class MyMvcApplication : System.Web.HttpApplication
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
         filters.Add(new AiHandleErrorAttribute());
      }
     ...
```

[Sample](https://github.com/AppInsightsSamples/Mvc3UnhandledExceptionTelemetry)

#### <a name="mvc-4-mvc5"></a>MVC 4, MVC5
Regisztrálja a AiHandleErrorAttribute globális szűrőként a FilterConfig. cs:

```csharp
    public class FilterConfig
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
        // Default replaced with the override to track unhandled exceptions
        filters.Add(new AiHandleErrorAttribute());
      }
    }
```

[Sample](https://github.com/AppInsightsSamples/Mvc5UnhandledExceptionTelemetry)

## <a name="web-api"></a>Webes API
A Application Insights web SDK 2,6-es verziójától (beta3 és újabb verzióktól) kezdve a Application Insights a vezérlő metódusokban automatikusan kiváltott kezeletlen kivételeket gyűjt a WebAPI 2 +-hoz. Ha korábban már hozzáadott egy egyéni kezelőt az ilyen kivételek nyomon követéséhez (az alábbi példákban leírtak szerint), akkor előfordulhat, hogy a kivételek kétszeres nyomon követésének megakadályozásához eltávolíthatja azt.

Számos esetben a kivétel szűrői nem kezelhetik. Például:

* A vezérlőkonstruktorokban történt kivételek.
* Az üzenetkezelőkben történt kivételek.
* Az útválasztás során történt kivételek.
* A választartalmak szerializálása során történt kivételek.
* Az alkalmazás indítása során történt kivételek.
* A háttérfeladatokban történt kivételek.

Az alkalmazás által *kezelt* összes kivételt manuálisan kell nyomon követni.
A vezérlőkből származó nem kezelt kivételek általában 500 "belső kiszolgálóhiba" választ eredményeznek. Ha az ilyen választ a kezelt kivétel (vagy egyáltalán nem kivétel) eredményeképpen manuálisan készíti el, azt a 500-es megfelelő kérelem telemetria nyomon követheti `ResultCode` , azonban Application INSIGHTS SDK nem tudja követni a megfelelő kivételt.

### <a name="prior-versions-support"></a>Korábbi verziók támogatása
Ha Application Insights web SDK 2,5 (és korábbi) WebAPI 1 (és korábbi) használatát használja, a kivételek nyomon követéséhez tekintse meg az alábbi példákat.

#### <a name="web-api-1x"></a>Webes API 1. x
Felülbírálja a System. Web. http. Filters. ExceptionFilterAttribute:

```csharp
    using System.Web.Http.Filters;
    using Microsoft.ApplicationInsights;

    namespace WebAPI.App_Start
    {
      public class AiExceptionFilterAttribute : ExceptionFilterAttribute
      {
        public override void OnException(HttpActionExecutedContext actionExecutedContext)
        {
            if (actionExecutedContext != null && actionExecutedContext.Exception != null)
            {  //or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(actionExecutedContext.Exception);
            }
            base.OnException(actionExecutedContext);
        }
      }
    }
```

Ezt a felülbírált attribútumot adott vezérlőkhöz hozzáadhatja, vagy hozzáadhatja a globális szűrő konfigurációjához a WebApiConfig osztályban:

```csharp
    using System.Web.Http;
    using WebApi1.x.App_Start;

    namespace WebApi1.x
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            config.Routes.MapHttpRoute(name: "DefaultApi", routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional });
            ...
            config.EnableSystemDiagnosticsTracing();

            // Capture exceptions for Application Insights:
            config.Filters.Add(new AiExceptionFilterAttribute());
        }
      }
    }
```

[Sample](https://github.com/AppInsightsSamples/WebApi_1.x_UnhandledExceptions)

#### <a name="web-api-2x"></a>Webes API 2. x
Adja hozzá a Iexceptionlogger felület implementációját:

```csharp
    using System.Web.Http.ExceptionHandling;
    using Microsoft.ApplicationInsights;

    namespace ProductsAppPureWebAPI.App_Start
    {
      public class AiExceptionLogger : ExceptionLogger
      {
        public override void Log(ExceptionLoggerContext context)
        {
            if (context !=null && context.Exception != null)
            {//or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(context.Exception);
            }
            base.Log(context);
        }
      }
    }
```

Adja hozzá ezt a WebApiConfig-szolgáltatásokhoz:

```csharp
    using System.Web.Http;
    using System.Web.Http.ExceptionHandling;
    using ProductsAppPureWebAPI.App_Start;

    namespace WebApi2WithMVC
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            // Web API configuration and services

            // Web API routes
            config.MapHttpAttributeRoutes();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
            config.Services.Add(typeof(IExceptionLogger), new AiExceptionLogger());
        }
      }
     }
```

[Sample](https://github.com/AppInsightsSamples/WebApi_2.x_UnhandledExceptions)

Alternatív megoldásként a következőket teheti:

1. Az egyetlen ExceptionHandler cserélje le a IExceptionHandler egyéni megvalósítására. Ezt csak akkor hívja meg a rendszer, ha a keretrendszer továbbra is kiválaszthatja, hogy melyik válaszüzenetet szeretné elküldeni (nem a csatlakozás megszakított példánya esetén)
2. Kivételt képező szűrők (a fenti, a webes API 1. x vezérlői című szakaszban leírtak szerint) – minden esetben nem hívható meg.

## <a name="wcf"></a>WCF
Adjon hozzá egy osztályt, amely kibővíti az attribútumot, és megvalósítja a IErrorHandler és a IServiceBehavior.

```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.ServiceModel.Description;
    using System.ServiceModel.Dispatcher;
    using System.Web;
    using Microsoft.ApplicationInsights;

    namespace WcfService4.ErrorHandling
    {
      public class AiLogExceptionAttribute : Attribute, IErrorHandler, IServiceBehavior
      {
        public void AddBindingParameters(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase,
            System.Collections.ObjectModel.Collection<ServiceEndpoint> endpoints,
            System.ServiceModel.Channels.BindingParameterCollection bindingParameters)
        {
        }

        public void ApplyDispatchBehavior(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
            foreach (ChannelDispatcher disp in serviceHostBase.ChannelDispatchers)
            {
                disp.ErrorHandlers.Add(this);
            }
        }

        public void Validate(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
        }

        bool IErrorHandler.HandleError(Exception error)
        {//or reuse instance (recommended!). see note above
            var ai = new TelemetryClient();

            ai.TrackException(error);
            return false;
        }

        void IErrorHandler.ProvideFault(Exception error,
            System.ServiceModel.Channels.MessageVersion version,
            ref System.ServiceModel.Channels.Message fault)
        {
        }
      }
    }

Add the attribute to the service implementations:

    namespace WcfService4
    {
        [AiLogException]
        public class Service1 : IService1
        {
         ...
```

[Sample](https://github.com/AppInsightsSamples/WCFUnhandledExceptions)

## <a name="exception-performance-counters"></a>Kivételi teljesítményszámlálók
Ha [telepítette a Application Insights ügynököt](./monitor-performance-live-website-now.md) a-kiszolgálóra, a kivételek aránya a .net alapján mérhető. Ez magában foglalja a kezelt és a nem kezelt .NET-kivételeket is.

Nyisson meg egy mérőszám-kezelő lapot, adjon hozzá egy új diagramot, és válassza a **kivételek aránya** elemet a teljesítményszámlálók területen.

A .NET-keretrendszer kiszámítja az arányt a kivételek számának és az intervallum hosszának megszámlálásával.

Ez eltér a "kivételek" számával, amelyet a Application Insights-portál a TrackException jelentéseinek számlálása alapján számít ki. A mintavételi időközök eltérőek, és az SDK nem küld TrackException-jelentéseket az összes kezelt és nem kezelt kivételhez.

## <a name="next-steps"></a>Következő lépések
* [A REST, az SQL és más függőségek hívásának figyelése](./asp-net-dependencies.md)
* [Az oldal betöltési idejének, a böngésző kivételeinek és az AJAX-hívások figyelése](./javascript.md)
* [Teljesítményszámlálók figyelése](./performance-counters.md)

