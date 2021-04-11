---
title: Oktatóanyag a Feature Flags használatáról egy .NET Core-alkalmazásban | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan implementálhatja a szolgáltatás-jelzőket a .NET Core-alkalmazásokban.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/17/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 4d54e1ff07b250b5595d2f8aee5f022bd2359721
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729507"
---
# <a name="tutorial-use-feature-flags-in-an-aspnet-core-app"></a>Oktatóanyag: funkció-jelzők használata egy ASP.NET Core alkalmazásban

A .NET Core-szolgáltatások felügyeleti könyvtárai köznyelvi támogatást biztosítanak a .NET-vagy ASP.NET Core-alkalmazásban található szolgáltatások jelölők megvalósításához. Ezek a kódtárak lehetővé teszik a szolgáltatásokhoz tartozó jelzők deklaratív hozzáadását, így nem kell kódot írnia a funkciók engedélyezéséhez vagy letiltásához a `if` utasításokkal.

A szolgáltatás-felügyeleti kódtárak a funkciók jelző életciklusait is kezelik a színfalak mögött. Például a tárak frissítési és gyorsítótár-jelző állapota, vagy ha egy kérelem hívásakor a jelző állapot nem változtatható meg. Emellett a ASP.NET Core-függvénytár beépített integrációkat is kínál, beleértve az MVC vezérlő műveleteit, nézeteit, útvonalait és köztes integrálását.

Az ASP.NET Core-alkalmazáshoz tartozó [jelzők hozzáadása](./quickstart-feature-flag-aspnet-core.md) egy egyszerű példát mutat be, hogy miként használhatók a szolgáltatás-jelzők egy ASP.net Core alkalmazásban. Ez az oktatóanyag a szolgáltatás-felügyeleti kódtárak további telepítési lehetőségeit és képességeit mutatja be. A rövid útmutatóban létrehozott minta alkalmazással kipróbálhatja az oktatóanyagban látható mintakód használatát. 

A ASP.NET Core Feature Management API-dokumentációja: [Microsoft. FeatureManagement névtér](/dotnet/api/microsoft.featuremanagement).

Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
> * A szolgáltatások rendelkezésre állásának szabályozásához vegyen fel szolgáltatás-jelzőket az alkalmazás legfontosabb részeibe.
> * Integrálhatja az alkalmazás konfigurációját, ha a funkció-jelzők kezelésére használja.

## <a name="set-up-feature-management"></a>A szolgáltatások kezelésének beállítása

A .NET Core Feature Manager eléréséhez az alkalmazásnak hivatkozásokkal kell rendelkeznie a `Microsoft.FeatureManagement.AspNetCore` NuGet-csomagra.

A .NET Core Feature Manager a keretrendszer natív konfigurációs rendszeréről van konfigurálva. Ennek eredményeképpen meghatározhatja az alkalmazás szolgáltatás-jelző beállításait a .NET Core által támogatott bármely konfigurációs forrással, beleértve a fájl-vagy környezeti változók helyi *appsettings.jsét* is.

Alapértelmezés szerint a szolgáltatás-kezelő a `"FeatureManagement"` .net Core konfigurációs adatok szakaszában lekéri a szolgáltatás jelölőjét. Az alapértelmezett konfigurációs hely használatához hívja meg a IServiceCollection [AddFeatureManagement](/dotnet/api/microsoft.featuremanagement.servicecollectionextensions.addfeaturemanagement) metódusát,  amelyet az **indítási** osztály **ConfigureServices** metódusa adott át.


```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement();
    }
}
```

Megadhatja, hogy a szolgáltatás-felügyeleti konfigurációt egy másik konfigurációs szakaszból kell beolvasnia a [Configuration. GetSection](/dotnet/api/microsoft.web.administration.configuration.getsection) és a kívánt szakasz nevének megadásával. A következő példa azt mutatja be, hogy a Feature Manager beolvassa a helyet egy másik szakaszból `"MyFeatureFlags"` :

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement(Configuration.GetSection("MyFeatureFlags"));
    }
}
```


Ha szűrőket használ a szolgáltatás jelzőjén, meg kell adnia a [Microsoft. FeatureManagement. FeatureFilters](/dotnet/api/microsoft.featuremanagement.featurefilters) névteret, és fel kell vennie egy hívást a [AddFeatureFilters](/dotnet/api/microsoft.featuremanagement.ifeaturemanagementbuilder.addfeaturefilter) -be annak a szűrőnek a nevét, amelyet a metódus általános típusaként kíván használni. A funkciók dinamikus engedélyezésével és letiltásával kapcsolatos további információkért tekintse meg a [szolgáltatások szakaszos bevezetésének engedélyezése a célzott célközönségek számára](./howto-targetingfilter-aspnet-core.md)című témakört.

Az alábbi példa bemutatja, hogyan használható a beépített szolgáltatás szűrője `PercentageFilter` :



```csharp
using Microsoft.FeatureManagement;
using Microsoft.FeatureManagement.FeatureFilters;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement()
                .AddFeatureFilter<PercentageFilter>();
    }
}
```

A szolgáltatás jelölőit az alkalmazásba nem rögzített kódolás helyett azt javasoljuk, hogy az alkalmazáson kívül tartsa meg a szolgáltatás jelölőit, és ezeket külön kell kezelnie. Ezzel a beállítással bármikor módosíthatja a jelző állapotokat, és ezek a módosítások azonnal érvénybe lépnek az alkalmazásban. Az Azure app Configuration szolgáltatás egy dedikált portál felhasználói felületet biztosít az összes funkció-jelző kezeléséhez. Az Azure app Configuration szolgáltatás a szolgáltatáshoz tartozó jelzőket is közvetlenül a .NET Core-ügyfél-kódtárak használatával továbbítja az alkalmazásnak.

A ASP.NET Core alkalmazás konfigurálásának legegyszerűbb módja az NuGet csomagban található konfigurációs szolgáltatón keresztül `Microsoft.Azure.AppConfiguration.AspNetCore` . Ha a csomagra mutató hivatkozást is tartalmaz, kövesse az alábbi lépéseket a NuGet-csomag használatához.

1. Nyissa meg a *program. cs* fájlt, és adja hozzá a következő kódot.
    > [!IMPORTANT]
    > `CreateHostBuilder``CreateWebHostBuilder`a .net Core 3. x helyére kerül. Válassza ki a megfelelő szintaxist a környezet alapján.

    ### <a name="net-5x"></a>[.NET 5. x](#tab/core5x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
                }).UseStartup<Startup>());
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration(config =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
            }).UseStartup<Startup>());
    ```
        
    ### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                    .ConfigureAppConfiguration(config =>
                    {
                        var settings = config.Build();
                        config.AddAzureAppConfiguration(options =>
                            options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
                    }).UseStartup<Startup>();
    ```
    ---

2. Nyissa meg a *Startup. cs* programot, és frissítse a `Configure` és a `ConfigureServices` metódust, és vegye fel a nevű beépített middleware-t `UseAzureAppConfiguration` . Ez a middleware lehetővé teszi, hogy a szolgáltatás jelölő értékei ismétlődő időközönként frissüljenek, miközben a ASP.NET Core webalkalmazás továbbra is fogadja a kéréseket.



    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        app.UseAzureAppConfiguration();
    }
    ```

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddAzureAppConfiguration();
   }
   ```
   
Egy tipikus forgatókönyv esetében rendszeresen frissíti a szolgáltatás jelző értékeit az alkalmazás üzembe helyezése és engedélyezése, illetve az alkalmazások különböző funkcióinak használatakor. Alapértelmezés szerint a szolgáltatás jelölő értékeit a rendszer 30 másodpercen belül gyorsítótárazza, így egy frissítési művelet akkor aktiválódik, ha a middleware kérése nem fogja frissíteni az értéket, amíg a gyorsítótárazott érték lejár. A következő kód bemutatja, hogyan módosíthatja a gyorsítótár lejárati idejét vagy a lekérdezési időközt 5 percre, ha beállítja a [CacheExpirationInterval](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.featuremanagement.featureflagoptions.cacheexpirationinterval) a **UseFeatureFlags**-hívásban.


    
```csharp
config.AddAzureAppConfiguration(options =>
    options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags(featureFlagOptions => {
        featureFlagOptions.CacheExpirationInterval = TimeSpan.FromMinutes(5);
    }));
});
```


## <a name="feature-flag-declaration"></a>Funkció jelző deklarációja

Minden egyes szolgáltatás jelölő deklarációja két részből áll: egy névvel és egy vagy több szűrőből, amelyek segítségével kiértékelheti, hogy a szolgáltatás állapota *be* van-e kapcsolva (azaz ha értéke `True` ). A szűrő meghatározza azt a feltételt, hogy mikor kell bekapcsolni egy szolgáltatást.

Ha egy szolgáltatás jelölője több szűrővel rendelkezik, a rendszer átadja a szűrőlisták sorrendjét, amíg az egyik szűrő nem határozza meg, hogy a szolgáltatást engedélyezni kell. Ekkor a funkció jelzője *be van kapcsolva*, és a rendszer kihagyja a többi szűrő eredményét. Ha nincs szűrő azt jelzi, hogy a funkciót engedélyezni kell, a szolgáltatás jelzője *ki van kapcsolva*.

A Feature Manager a szolgáltatás-jelzők konfigurációs forrásaként támogatja a *appsettings.js* . Az alábbi példa bemutatja, hogyan állíthatja be a szolgáltatás jelzőit egy JSON-fájlban:

```JSON
"FeatureManagement": {
    "FeatureA": true, // Feature flag set to on
    "FeatureB": false, // Feature flag set to off
    "FeatureC": {
        "EnabledFor": [
            {
                "Name": "Percentage",
                "Parameters": {
                    "Value": 50
                }
            }
        ]
    }
}
```

Az egyezmény szerint a `FeatureManagement` JSON-dokumentum szakasza a szolgáltatások jelző beállításaihoz használatos. Az előző példa három funkció jelzőjét mutatja a tulajdonságban definiált szűrőkkel `EnabledFor` :

* `FeatureA`*be van kapcsolva*.
* `FeatureB`*ki van kapcsolva*.
* `FeatureC` egy tulajdonsággal megnevezett szűrőt ad meg `Percentage` `Parameters` . `Percentage` konfigurálható szűrő. Ebben a példában a `Percentage` jelző 50 százalékos valószínűségét adja `FeatureC` *meg*. A szolgáltatási szűrők használatával kapcsolatos útmutatóért lásd a [feltételes funkciók jelzői funkcióinak használata a szolgáltatás szűrőinek](./howto-feature-filters-aspnet-core.md)használatával című témakört.




## <a name="use-dependency-injection-to-access-ifeaturemanager"></a>Függőségi befecskendezés használata a IFeatureManager való hozzáféréshez 

Egyes műveletekhez, például a szolgáltatás-jelzők manuális ellenőrzéséhez le kell kérnie a [IFeatureManager](/dotnet/api/microsoft.featuremanagement.ifeaturemanager?preserve-view=true&view=azure-dotnet-preview)egy példányát. ASP.NET Core MVC-ben a szolgáltatások kezelője `IFeatureManager` függőségi befecskendezéssel érhető el. A következő példában egy típusú argumentumot `IFeatureManager` adnak hozzá a vezérlő konstruktorának aláírásához. A futtatókörnyezet automatikusan feloldja a hivatkozást, és megadja a felületet a konstruktor hívásakor. Ha olyan alkalmazás-sablont használ, amelyben a vezérlőhöz már tartozik egy vagy több függőségi injekció argumentum a konstruktorban, például `ILogger` `IFeatureManager` :

### <a name="net-5x"></a>[.NET 5. x](#tab/core5x)
    
```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(ILogger<HomeController> logger, IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(ILogger<HomeController> logger, IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```
    
### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

---

## <a name="feature-flag-references"></a>Szolgáltatás jelölő hivatkozásai

Adja meg a szolgáltatás-jelzőket karakterlánc-változókként a kód alapján való hivatkozáshoz:

```csharp
public static class MyFeatureFlags
{
    public const string FeatureA = "FeatureA";
    public const string FeatureB = "FeatureB";
    public const string FeatureC = "FeatureC";
}
```

## <a name="feature-flag-checks"></a>Szolgáltatás-jelző ellenőrzése

A szolgáltatások felügyeletének gyakori mintája, hogy ellenőrizze, hogy a szolgáltatás jelölője be van-e *kapcsolva* , és ha igen, futtassa a kód egy szakaszát. Például:

```csharp
IFeatureManager featureManager;
...
if (await featureManager.IsEnabledAsync(MyFeatureFlags.FeatureA))
{
    // Run the following code
}
```

## <a name="controller-actions"></a>Vezérlő műveletei

Az MVC-vezérlőkkel az attribútum segítségével `FeatureGate` szabályozhatja, hogy a teljes vezérlő osztály vagy egy adott művelet engedélyezve van-e. A `HomeController` `FeatureA` vezérlő osztályt tartalmazó művelet végrehajtása előtt *a következő* vezérlőnek kell futnia:

```csharp
using Microsoft.FeatureManagement.Mvc;

[FeatureGate(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

A következő `Index` művelet végrehajtása szükséges a `FeatureA` futtatásához: 

```csharp
using Microsoft.FeatureManagement.Mvc;

[FeatureGate(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

Ha egy MVC vezérlő vagy művelet le van tiltva, mert a vezérlő funkció jelzője *ki van kapcsolva*, a rendszer regisztrált [IDisabledFeaturesHandler](/dotnet/api/microsoft.featuremanagement.mvc.idisabledfeatureshandler?preserve-view=true&view=azure-dotnet-preview) felületet hív meg. Az alapértelmezett `IDisabledFeaturesHandler` illesztőfelület 404 állapotkódot ad vissza az ügyfélnek a válasz törzse nélkül.

## <a name="mvc-views"></a>MVC-nézetek

Nyissa meg a *_ViewImports. cshtml* mappát a *views (nézetek* ) könyvtárban, és adja hozzá a Feature Manager tag helpert:

```html
@addTagHelper *, Microsoft.FeatureManagement.AspNetCore
```

Az MVC-nézetek esetében `<feature>` címkével jelenítheti meg a tartalmakat, attól függően, hogy engedélyezve van-e a funkció jelzője:

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

Alternatív tartalom megjelenítéséhez, ha a követelmények nem teljesülnek, az `negate` attribútum használható.

```html
<feature name="FeatureA" negate="true">
    <p>This will be shown if 'FeatureA' is disabled.</p>
</feature>
```

A szolgáltatás `<feature>` címkéje a tartalom megjelenítésére is használható, ha a lista bármely vagy összes funkciója engedélyezve van.

```html
<feature name="FeatureA, FeatureB" requirement="All">
    <p>This can only be seen if 'FeatureA' and 'FeatureB' are enabled.</p>
</feature>
<feature name="FeatureA, FeatureB" requirement="Any">
    <p>This can be seen if 'FeatureA', 'FeatureB', or both are enabled.</p>
</feature>
```

## <a name="mvc-filters"></a>MVC-szűrők

Az MVC-szűrőket beállíthatja úgy, hogy azok a szolgáltatás jelzőjének állapota alapján legyenek aktiválva. Ez a képesség a [IAsyncActionFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncactionfilter)megvalósító szűrőkre korlátozódik. A következő kód egy nevű MVC szűrőt hoz létre `ThirdPartyActionFilter` . Ez a szűrő csak akkor aktiválódik az MVC-folyamaton belül `FeatureA` , ha engedélyezve van.  

```csharp
using Microsoft.FeatureManagement.FeatureFilters;

IConfiguration Configuration { get; set;}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options => {
        options.Filters.AddForFeature<ThirdPartyActionFilter>(MyFeatureFlags.FeatureA);
    });
}
```

## <a name="middleware"></a>Köztes szoftverek

A funkciók jelzőit is használhatja az alkalmazás-ágak és a köztes alkalmazások feltételes hozzáadásához. A következő kód csak akkor szúr be egy middleware-összetevőt a kérelmek folyamatában, ha `FeatureA` az engedélyezve van:

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(MyFeatureFlags.FeatureA);
```

Ez a kód kiépíti a további általános képességet, hogy a teljes alkalmazást a szolgáltatás jelölője alapján lehessen összekapcsolni:

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan implementálhatja a szolgáltatás-jelzőket a ASP.NET Core alkalmazásban a `Microsoft.FeatureManagement` kódtárak használatával. A ASP.NET Core és az alkalmazások konfigurációjának funkció-kezelési támogatásáról az alábbi forrásokban talál további információt:

* [ASP.NET Core funkció jelölője mintakód](./quickstart-feature-flag-aspnet-core.md)
* [A Microsoft. FeatureManagement dokumentációja](/dotnet/api/microsoft.featuremanagement)
* [Funkciójelölők kezelése](./manage-feature-flags.md)