---
title: 'Oktatóanyag: az alkalmazás konfigurációjának dinamikus konfigurálása a ASP.NET Coreban'
titleSuffix: Azure App Configuration
description: Ez az oktatóanyag azt ismerteti, hogyan lehet dinamikusan frissíteni a ASP.NET Core-alkalmazások konfigurációs információit
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: lcozzens
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 217c564a6bdb340ec15262c1eaf54a75bbffc833
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585015"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Oktatóanyag: dinamikus konfiguráció használata egy ASP.NET Core alkalmazásban

ASP.NET Core olyan csatlakoztatható konfigurációs rendszerrel rendelkezik, amely különböző forrásokból származó konfigurációs adatok olvasására képes. A módosításokat dinamikusan képes kezelni anélkül, hogy az alkalmazás újraindul. ASP.NET Core támogatja a konfigurációs beállítások kötését a .NET-osztályokhoz. A különböző minták használatával beinjektálja őket a kódban `IOptions<T>` . Az egyik ilyen minta, különösen a `IOptionsSnapshot<T>` automatikusan újratölti az alkalmazás konfigurációját, amikor az alapul szolgáló adat megváltozik. Az `IOptionsSnapshot<T>` alkalmazásban lévő vezérlőkbe beillesztheti az Azure-alkalmazás konfigurációjában tárolt legújabb konfiguráció elérését.

Emellett beállíthatja az alkalmazás konfigurációját ASP.NET Core ügyfél-függvénytárat, hogy dinamikusan frissítse a konfigurációs beállításokat egy middleware használatával. A konfigurációs beállítások minden alkalommal frissülnek a konfigurációs tárolóval, amíg a webalkalmazás fogadja a kéréseket.

Az alkalmazás konfigurációja automatikusan gyorsítótárazza az egyes beállításokat, hogy ne legyen túl sok hívás a konfigurációs tárolóba. A frissítési művelet megvárja, amíg a beállítás gyorsítótárazott értéke lejár a beállítás frissítése előtt, még akkor is, ha az értéke megváltozik a konfigurációs tárolóban. Az alapértelmezett gyorsítótár lejárati ideje 30 másodperc. Szükség esetén felülbírálhatja ezt a lejárati időt.

Ez az oktatóanyag bemutatja, hogyan valósítható meg a dinamikus konfigurációs frissítések a kódban. A szolgáltatás a gyors útmutatókban bemutatott webalkalmazásra épül. A folytatás előtt fejezze be a [ASP.net Core alkalmazás létrehozása az alkalmazás-konfigurációval](./quickstart-aspnet-core-app.md) először.

Az oktatóanyag lépéseihez bármilyen Kódszerkesztő használható. A [Visual Studio Code](https://code.visualstudio.com/) egy kiváló lehetőség, amely a Windows, MacOS és Linux platformokon érhető el.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Állítsa be úgy az alkalmazást, hogy frissítse a konfigurációját az alkalmazás konfigurációs tárolójában történt változásokra reagálva.
> * Adja meg a legújabb konfigurációt az alkalmazás vezérlői között.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez telepítse a [.net Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

A folytatás előtt fejezze be a [ASP.net Core alkalmazás létrehozása az alkalmazás-konfigurációval](./quickstart-aspnet-core-app.md) először.

## <a name="add-a-sentinel-key"></a>Sentinel-kulcs hozzáadása

A *Sentinel-kulcs* egy speciális kulcs, amelyet a rendszer a konfiguráció megváltozásakor jelez. Az alkalmazás figyeli a Sentinel-kulcsot a változásokhoz. Ha változást észlel, az összes konfigurációs értéket frissíti. Ez a megközelítés csökkenti az alkalmazás által az alkalmazás konfigurálására irányuló kérések teljes számát, a változások összes kulcsának figyelésével szemben.

1. A Azure Portal válassza a **Configuration Explorer > > kulcs-érték létrehozása**lehetőséget.

1. A **Key (kulcs**) mezőbe írja be az *TestApp: Settings: Sentinel*értéket. Az **érték**mezőben adja meg az 1 értéket. Hagyja üresen a **címke** és a **tartalom típusát** .

1. Válassza az **Alkalmaz** lehetőséget.

    > [!NOTE]
    > Ha nem használ Sentinel-kulcsot, manuálisan kell regisztrálnia minden megtekinteni kívánt kulcsot.

## <a name="reload-data-from-app-configuration"></a>Adatok újratöltése az alkalmazás konfigurációjától

1. Adja hozzá a NuGet- `Microsoft.Azure.AppConfiguration.AspNetCore` csomagra mutató hivatkozást a következő parancs futtatásával:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. Nyissa meg a *program.cs*, és frissítse a `CreateWebHostBuilder` metódust a metódus hozzáadásához `config.AddAzureAppConfiguration()` .

    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .ConfigureRefresh(refresh =>
                                {
                                    refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                           .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                });
                });
            })
            .UseStartup<Startup>();
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                    {
                        options.Connect(settings["ConnectionStrings:AppConfig"])
                               .ConfigureRefresh(refresh =>
                                    {
                                        refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                               .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                    });
                    });
                })
            .UseStartup<Startup>());
    ```
    ---

    A `ConfigureRefresh` metódussal adhatja meg azokat a beállításokat, amelyeket a konfigurációs adatainak az alkalmazás konfigurációs tárolójával való frissítéséhez használ a frissítési művelet elindításakor. A `refreshAll` `Register` metódus paramétere azt jelzi, hogy az összes konfigurációs értéket frissíteni kell, ha a Sentinel-kulcs módosul.

    Emellett a `SetCacheExpiration` metódus felülbírálja az alapértelmezett gyorsítótár lejárati idejét 30 másodpercen belül, az 5 perces időpontot megadva. Ez csökkenti az alkalmazás konfigurációjával kapcsolatos kérések számát.

    > [!NOTE]
    > Tesztelési célokra érdemes lehet csökkenteni a gyorsítótár lejárati idejét.

    A frissítési művelet tényleges kiváltásához konfigurálnia kell egy, az alkalmazásra vonatkozó frissítést, amely a konfigurációs adatai frissítését eredményezi. Ezt egy későbbi lépésben láthatja.

2. Adjon hozzá egy új osztályt definiáló és megvalósító *Settings.cs* -fájlt `Settings` .

    ```csharp
    namespace TestAppConfig
    {
        public class Settings
        {
            public string BackgroundColor { get; set; }
            public long FontSize { get; set; }
            public string FontColor { get; set; }
            public string Message { get; set; }
        }
    }
    ```

3. Nyissa meg a *Startup.cs*, és használja `IServiceCollection.Configure<T>` a `ConfigureServices` metódust a konfigurációs `Settings` adatosztályhoz való kötéshez.

    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddControllersWithViews();
    }
    ```
    ---

    > [!TIP]
    > Ha többet szeretne megtudni a konfigurációs értékek olvasásával kapcsolatos beállításokról, tekintse meg a [ASP.net Coreban található beállítások mintáit](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options?view=aspnetcore-3.1).

4. Frissítse a `Configure` metódust, és adja hozzá a `UseAzureAppConfiguration` middleware-t, hogy a frissítéshez regisztrált konfigurációs beállítások frissüljenek, miközben a ASP.net Core webalkalmazás továbbra is fogadja a kéréseket.


    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        app.UseMvc();
    }
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }

            // Add the following line:
            app.UseAzureAppConfiguration();

            app.UseHttpsRedirection();
            
            app.UseStaticFiles();

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    ---
    
    A middleware a metódusában megadott frissítési konfigurációt használja az `AddAzureAppConfiguration` `Program.cs` ASP.net Core webalkalmazás által fogadott minden kérelem frissítésének elindításához. Minden kérelem esetében egy frissítési művelet aktiválódik, és az ügyféloldali kódtár ellenőrzi, hogy a regisztrált konfigurációs beállítás gyorsítótárazott értéke lejárt-e. Ha lejárt, frissül.

## <a name="use-the-latest-configuration-data"></a>A legújabb konfigurációs adatértékek használata

1. Nyissa meg a *HomeController.cs* a vezérlők könyvtárban, és adjon hozzá egy hivatkozást a `Microsoft.Extensions.Options` csomaghoz.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. Frissítse az `HomeController` osztályt a `Settings` függőségi befecskendezésen keresztüli fogadáshoz, és használja az értékeket.

    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        public HomeController(IOptionsSnapshot<Settings> settings)
        {
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }
    }
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        private readonly ILogger<HomeController> _logger;

        public HomeController(ILogger<HomeController> logger, IOptionsSnapshot<Settings> settings)
        {
            _logger = logger;
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }

        // ...
    }
    ```
    ---



3. Nyissa meg az *index. cshtml* a views > Home könyvtárban, és cserélje le a tartalmát a következő parancsfájlra:

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @ViewData["BackgroundColor"]
        }
        h1 {
            color: @ViewData["FontColor"];
            font-size: @ViewData["FontSize"]px;
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@ViewData["Message"]</h1>
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>Az alkalmazás helyi létrehozása és futtatása

1. Ha az alkalmazást a a .NET Core parancssori felülete használatával szeretné felépíteni, futtassa a következő parancsot a parancs-rendszerhéjban:

```console
        dotnet build
```

1. A létrehozás sikeres befejezése után futtassa a következő parancsot a webalkalmazás helyi futtatásához:

```console
        dotnet run
```

1. Nyisson meg egy böngészőablakot, és keresse meg a kimenetben látható URL-címet `dotnet run` .

    ![Gyorsindítás alkalmazás helyi indítása](./media/quickstarts/aspnet-core-app-launch-local-before.png)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **minden erőforrás**lehetőséget, majd válassza ki a gyors útmutatóban létrehozott app Configuration Store-példányt.

1. Válassza a **Configuration Explorer**lehetőséget, és frissítse a következő kulcsok értékeit:

    | Kulcs | Érték |
    |---|---|
    | TestApp: beállítások: BackgroundColor | zöld |
    | TestApp: beállítások: FontColor | lightGray |
    | TestApp: beállítások: üzenet | Adatok az Azure app Configuration szolgáltatásból – mostantól élő frissítésekkel! |
    | TestApp: beállítások: Sentinel | 2 |

1. A böngésző oldalának frissítésével tekintheti meg az új konfigurációs beállításokat. Előfordulhat, hogy a módosítások megjelenítéséhez többször is frissítenie kell.

    ![Frissített gyors útmutató alkalmazás helyi indítása](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban engedélyezte a ASP.NET Core webalkalmazásának, hogy dinamikusan frissítse a konfigurációs beállításokat az alkalmazás konfigurációjában. Ha szeretné megtudni, hogyan használható az Azure által felügyelt identitás az alkalmazás-konfigurációhoz való hozzáférés egyszerűsítéséhez, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Felügyelt identitások integrációja](./howto-integrate-azure-managed-service-identity.md)
