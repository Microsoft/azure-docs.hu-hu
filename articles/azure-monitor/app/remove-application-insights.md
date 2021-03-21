---
title: Application Insights eltávolítása a Visual Studióban – Azure Monitor
description: A ASP.NET és a ASP.NET Core Application Insights SDK eltávolítása a Visual Studióban.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 1d70413fa6a47e2d41693db6eb705f31b2a2b1b2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101704310"
---
# <a name="how-to-remove-application-insights-in-visual-studio"></a>Application Insights eltávolítása a Visual Studióban

Ez a cikk bemutatja, hogyan távolíthatja el a ASP.NET és a ASP.NET Core Application Insights SDK-t a Visual Studióban.

Application Insights eltávolításához el kell távolítania az alkalmazás API-NuGet-csomagjait és hivatkozásait. A NuGet-csomagokat a csomagkezelő konzol használatával vagy a Visual Studióban található NuGet-megoldással távolíthatja el. A következő részekben két módszert láthat a NuGet-csomagok eltávolítására és a projektben automatikusan hozzáadott értékekre. Győződjön meg arról, hogy a hozzáadott fájlok és a saját kódjában található területek el vannak távolítva.

## <a name="uninstall-using-the-package-management-console"></a>Eltávolítás a csomagkezelő konzol használatával

# <a name="net"></a>[.NET](#tab/net)

1. A csomagkezelő konzol megnyitásához a felső menüben válassza az eszközök > NuGet Package Manager > csomagkezelő konzolt.
     
    ![A felső menüben kattintson az eszközök > NuGet Package Manager > csomagkezelő konzol](./media/remove-application-insights/package-manager.png)

    > [!NOTE]
    > Ha a nyomkövetési gyűjtemény engedélyezve van, először el kell távolítania a Microsoft. ApplicationInsights. TraceListener. Adja meg `Uninstall-package Microsoft.ApplicationInsights.TraceListener` az alábbi lépéseket a Microsoft. ApplicationInsights. Web eltávolításához.

1. Adja meg a következő parancsot: `Uninstall-Package Microsoft.ApplicationInsights.Web -RemoveDependencies`

    A parancs beírása után a rendszer eltávolítja a Application Insights csomagot és annak összes függőségét a projektből.
    
    ![Adja meg a parancsot a konzolon](./media/remove-application-insights/package-management-console.png)

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. A csomagkezelő konzol megnyitásához a felső menüben válassza az eszközök > NuGet Package Manager > csomagkezelő konzolt.

    ![A felső menüben kattintson az eszközök > NuGet Package Manager > csomagkezelő konzol](./media/remove-application-insights/package-manager.png)

1. Adja meg a következő parancsot: ` Uninstall-Package Microsoft.ApplicationInsights.AspNetCore -RemoveDependencies`

    A parancs beírása után a rendszer eltávolítja a Application Insights csomagot és annak összes függőségét a projektből.

---

## <a name="uninstall-using-the-visual-studio-nuget-ui"></a>Eltávolítás a Visual Studio NuGet felhasználói felületének használatával

# <a name="net"></a>[.NET](#tab/net)

1. A jobb oldali *megoldáskezelő* kattintson a jobb gombbal a **megoldás** elemre, és válassza a **megoldás NuGet-csomagok kezelése** lehetőséget.

    Ekkor megjelenik egy képernyő, amely lehetővé teszi a projekt részét képező összes NuGet szerkesztését.
    
     ![Kattintson a jobb gombbal a megoldásra, a Megoldáskezelő, majd válassza a megoldás NuGet-csomagok kezelése lehetőséget.](./media/remove-application-insights/manage-nuget-framework.png)

    > [!NOTE]
    > Ha a nyomkövetési gyűjtemény engedélyezve van, először el kell távolítania a Microsoft. ApplicationInsights. TraceListener elemet a függőségek eltávolítása nélkül, majd az alábbi lépések végrehajtásával távolítsa el a Microsoft. ApplicationInsights. Web elemet a kiválasztott függőségek eltávolítása lehetőséggel.

1. Kattintson a **Microsoft. ApplicationInsights. Web** csomagra. A jobb oldalon jelölje be a **projekt** melletti jelölőnégyzetet az összes projekt kiválasztásához.

1. Ha az eltávolításkor az összes függőséget el szeretné távolítani, jelölje be a beállítások legördülő menü alatti **lehetőséget** a projekt kijelölt részében.

    Az *eltávolítási beállítások* területen jelölje be a *függőségek eltávolítása* elem melletti jelölőnégyzetet.

1. Válassza az **Eltávolítás** lehetőséget.
    
    ![Képernyőfelvétel: a Microsoft. ApplicationInsights. Web ablak, melyben a függőségek eltávolítása és a Kiemelt eltávolítás van kijelölve.](./media/remove-application-insights/uninstall-framework.png)

    Ekkor megjelenik egy párbeszédpanel, amely megjeleníti az alkalmazásból eltávolítandó összes függőséget. Az eltávolításhoz kattintson **az OK gombra** .
    
    ![A képernyőképen egy párbeszédpanel jelenik meg, amelyen az eltávolítandó függőségek jelennek meg.](./media/remove-application-insights/preview-uninstall-framework.png)
    
1.  A rendszer minden eltávolítása után továbbra is "ApplicationInsights.config" és "AiHandleErrorAttribute. cs" kifejezést láthat a *Megoldáskezelőban*. A két fájlt manuálisan is törölheti.

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. A jobb oldali *megoldáskezelő* kattintson a jobb gombbal a **megoldás** elemre, és válassza a **megoldás NuGet-csomagok kezelése** lehetőséget.

    Ekkor megjelenik egy képernyő, amely lehetővé teszi a projekt részét képező összes NuGet szerkesztését.

    ![Kattintson a jobb gombbal a megoldásra, a Megoldáskezelő, majd válassza a megoldás NuGet-csomagok kezelése lehetőséget.](./media/remove-application-insights/manage-nuget-core.png)

1. Kattintson a "Microsoft. ApplicationInsights. AspNetCore" csomagra. A jobb oldalon jelölje be a *projekt* melletti jelölőnégyzetet az összes projekt kiválasztásához, majd válassza az **Eltávolítás** lehetőséget.

    ![Jelölje be a függőségek eltávolítása, majd az Eltávolítás elemet.](./media/remove-application-insights/uninstall-core.png)

---

## <a name="what-is-created-when-you-add-application-insights"></a>Mi jön létre a Application Insights hozzáadásakor

Ha Application Insightst ad hozzá a projekthez, a fájlokat hoz létre, és kódot ad hozzá a fájlokhoz. Kizárólag a NuGet-csomagok eltávolításával a fájlok és a kódok nem lesznek mindig elvetve. Application Insights teljes eltávolításához tekintse meg és törölje kézzel a hozzáadott kódot vagy fájlokat a projektben hozzáadott API-hívásokkal együtt.

# <a name="net"></a>[.NET](#tab/net)

Ha Application Insights Telemetria hoz létre egy Visual Studio ASP.NET-projekthez, a következő fájlokat adja hozzá:

- ApplicationInsights.config
- AiHandleErrorAttribute. cs

A következő kódrészletek lettek hozzáadva:

- [A projekt neve]. csproj

    ```C#
     <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4</ApplicationInsightsResourceId>
    ```

- Packages.config

    ```xml
    <packages>
    ...
    
      <package id="Microsoft.ApplicationInsights" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.Agent.Intercept" version="2.4.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.DependencyCollector" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.PerfCounterCollector" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.Web" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.WindowsServer" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" version="2.12.0" targetFramework="net472" />
    
      <package id="Microsoft.AspNet.TelemetryCorrelation" version="1.0.7" targetFramework="net472" />
    
      <package id="System.Buffers" version="4.4.0" targetFramework="net472" />
      <package id="System.Diagnostics.DiagnosticSource" version="4.6.0" targetFramework="net472" />
      <package id="System.Memory" version="4.5.3" targetFramework="net472" />
      <package id="System.Numerics.Vectors" version="4.4.0" targetFramework="net472" />
      <package id="System.Runtime.CompilerServices.Unsafe" version="4.5.2" targetFramework="net472" />
    ...
    </packages>
    ```

- Layout. cshtml

    Ha a projektnek van egy layout. cshtml fájlja, az alábbi kódot adja hozzá.
    
    ```html
    <head>
    ...
        <script type = 'text/javascript' >
            var appInsights=window.appInsights||function(config)
            {
                function r(config){ t[config] = function(){ var i = arguments; t.queue.push(function(){ t[config].apply(t, i)})} }
                var t = { config:config},u=document,e=window,o='script',s=u.createElement(o),i,f;for(s.src=config.url||'//az416426.vo.msecnd.net/scripts/a/ai.0.js',u.getElementsByTagName(o)[0].parentNode.appendChild(s),t.cookie=u.cookie,t.queue=[],i=['Event','Exception','Metric','PageView','Trace','Ajax'];i.length;)r('track'+i.pop());return r('setAuthenticatedUserContext'),r('clearAuthenticatedUserContext'),config.disableExceptionTracking||(i='onerror',r('_'+i),f=e[i],e[i]=function(config, r, u, e, o) { var s = f && f(config, r, u, e, o); return s !== !0 && t['_' + i](config, r, u, e, o),s}),t
            }({
                instrumentationKey:'00000000-0000-0000-0000-000000000000'
            });
            
            window.appInsights=appInsights;
            appInsights.trackPageView();
        </script>
    ...
    </head>
    ```

- ConnectedService.jsbekapcsolva

    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "16.0.0.0",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=613413"
      }
    }
    ```

- FilterConfig. cs

    ```csharp
            public static void RegisterGlobalFilters(GlobalFilterCollection filters)
            {
                filters.Add(new ErrorHandler.AiHandleErrorAttribute());// This line was added
            }
    ```

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Ha Application Insights Telemetriat ad hozzá egy Visual Studio ASP.NET Core-sablon projekthez, a következő kódot adja hozzá:

- [A projekt neve]. csproj

    ```csharp
      <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4core</ApplicationInsightsResourceId>
      </PropertyGroup>
    
      <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.12.0" />
      </ItemGroup>
    
      <ItemGroup>
        <WCFMetadata Include="Connected Services" />
      </ItemGroup>
    ```

- Appsettings.jsa következőn:

    ```json
    "ApplicationInsights": {
        "InstrumentationKey": "00000000-0000-0000-0000-000000000000"
    ```

- ConnectedService.jsbekapcsolva
    
    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "16.0.0.0",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
      }
    }
    ```
- Startup.cs

    ```csharp
       public void ConfigureServices(IServiceCollection services)
            {
                services.AddRazorPages();
                services.AddApplicationInsightsTelemetry(); // This is added
            }
    ```

---

## <a name="next-steps"></a>Következő lépések

- [Azure Monitor](../overview.md)
