---
title: Szolgáltatás-jelzők hozzáadásának rövid útmutatója ASP.NET Core
description: Szolgáltatás-jelzők hozzáadása az alkalmazások ASP.NET Coreához és az Azure app Configuration használatával történő kezeléséhez
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 09/28/2020
ms.author: alkemper
ms.openlocfilehash: 8ca4a6e78ab3c6ccf492869bb68c5296ae91de21
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99806070"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>Gyors útmutató: szolgáltatás-jelzők hozzáadása ASP.NET Core-alkalmazáshoz

Ebben a rövid útmutatóban egy ASP.NET Core-alkalmazás teljes körű implementációját hozza létre az Azure app Configuration használatával. Az alkalmazás-konfigurációs szolgáltatás segítségével központilag tárolhatja az összes funkció jelzőjét, és szabályozhatja az állapotukat. 

A .NET Core-szolgáltatások felügyeleti kódtárai kiterjesztik a keretrendszert átfogó funkció-jelölő támogatással. Ezek a kódtárak a .NET Core konfigurációs rendszerre épülnek. Zökkenőmentesen integrálva vannak az alkalmazások konfigurációjával a .NET Core konfigurációs szolgáltatón keresztül.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/dotnet)
* [.NET Core SDK](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Alkalmazás-konfigurációs tároló létrehozása

[!INCLUDE[Azure App Configuration resource creation steps](../../includes/azure-app-configuration-create.md)]

8. Válassza a **Operations**  >  **Feature Manager**  >  **Hozzáadás** elemet a *Beta* nevű szolgáltatás-jelölő hozzáadásához.

    > [!div class="mx-imgBorder"]
    > ![A szolgáltatás jelölő engedélyezése Beta néven](media/add-beta-feature-flag.png)

    Most hagyja üresen a **címkét** . Válassza az **alkalmaz** lehetőséget az új funkció jelző mentéséhez.

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core-webalkalmazás létrehozása

Új ASP.NET Core MVC-projekt létrehozásához használja a [.net Core parancssori felületet (CLI)](/dotnet/core/tools) . A Visual Studio helyett a a .NET Core parancssori felülete használatának előnye, hogy a a .NET Core parancssori felülete a Windows, macOS és Linux platformokon érhető el.

Futtassa a következő parancsot egy ASP.NET Core MVC-projekt létrehozásához egy új *TestFeatureFlags* mappában:

```dotnetcli
dotnet new mvc --no-https --output TestFeatureFlags
```

[!INCLUDE[Add Secret Manager support to an ASP.NET Core project](../../includes/azure-app-configuration-add-secret-manager.md)]

## <a name="connect-to-an-app-configuration-store"></a>Kapcsolódás alkalmazás-konfigurációs tárolóhoz

1. Telepítse a [Microsoft. Azure. AppConfiguration. AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) és a [Microsoft. FeatureManagement. AspNetCore](https://www.nuget.org/packages/Microsoft.FeatureManagement.AspNetCore) NuGet csomagokat a következő parancsok futtatásával:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

    ```dotnetcli
    dotnet add package Microsoft.FeatureManagement.AspNetCore
    ```

1. Futtassa a következő parancsot ugyanabban a könyvtárban, mint a *. csproj* fájlt. A parancs a Secret Manager használatával tárolja a nevű titkos kulcsot `ConnectionStrings:AppConfig` , amely az alkalmazás konfigurációs tárolójához tartozó kapcsolódási karakterláncot tárolja. Cserélje le a `<your_connection_string>` helyőrzőt az alkalmazás konfigurációs tárolójának összekapcsolási karakterláncára. A kapcsolati karakterláncot a Azure Portal **elérési kulcsa** alatt találja.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig "<your_connection_string>"
    ```

    A Secret Manager csak a webalkalmazás helyi tesztelésére szolgál. Ha az alkalmazás telepítve van a [Azure app Servicere](https://azure.microsoft.com/services/app-service/web), a Secret Manager helyett használja a app Service **a kapcsolódási karakterláncok alkalmazása beállítást** a kapcsolódási sztring tárolásához.

    Ezt a titkot a .NET Core konfigurációs API használatával érheti el. A kettőspont ( `:` ) a konfigurációs névvel együtt a konfigurációs API-val működik az összes támogatott platformon. További információ: [konfigurációs kulcsok és értékek](/aspnet/core/fundamentals/configuration#configuration-keys-and-values).

1. A *program. cs programban* frissítse a metódust az `CreateWebHostBuilder` alkalmazás konfigurációjának használatára a metódus meghívásával `AddAzureAppConfiguration` .

    > [!IMPORTANT]
    > `CreateHostBuilder``CreateWebHostBuilder`a .net Core 3. x helyére kerül. Válassza ki a megfelelő szintaxist a környezet alapján.

     #### <a name="net-5x"></a>[.NET 5. x](#tab/core5x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    var connection = settings.GetConnectionString("AppConfig");
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(connection).UseFeatureFlags());
                }).UseStartup<Startup>());
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    var connection = settings.GetConnectionString("AppConfig");
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(connection).UseFeatureFlags());
                }).UseStartup<Startup>());
    ```

    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration(config =>
               {
                   var settings = config.Build();
                   var connection = settings.GetConnectionString("AppConfig");
                   config.AddAzureAppConfiguration(options =>
                       options.Connect(connection).UseFeatureFlags());
               }).UseStartup<Startup>();
    ```

    ---

    Az előző módosítással az alkalmazás konfigurációjának [szolgáltatója](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration) regisztrálva van a .net Core konfigurációs API-val.

1. A *Startup. cs* fájlban adjon hozzá egy hivatkozást a .net Core Feature Managerhez:

    ```csharp
    using Microsoft.FeatureManagement;
    ```

1. Frissítse a `Startup.ConfigureServices` metódust a szolgáltatás jelölő támogatásának hozzáadásához a metódus meghívásával `AddFeatureManagement` . Megadhatja, hogy a szolgáltatás jelzőjén használható szűrőket a következő hívásával is felhasználhatja `AddFeatureFilter<FilterType>()` :

     #### <a name="net-5x"></a>[.NET 5. x](#tab/core5x)

    ```csharp    
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllersWithViews();
        services.AddFeatureManagement();
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp    
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllersWithViews();
        services.AddFeatureManagement();
    }
    ```

    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc()
            .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
        services.AddFeatureManagement();
    }
    ```

    ---

1. Vegyen fel egy *MyFeatureFlags. cs* fájlt a legfelső szintű projekt könyvtárába a következő kóddal:

    ```csharp
    namespace TestFeatureFlags
    {
        public enum MyFeatureFlags
        {
            Beta
        }
    }
    ```

1. Vegyen fel egy *BetaController. cs* fájlt a *vezérlők* könyvtárba a következő kóddal:

    ```csharp
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.FeatureManagement;
    using Microsoft.FeatureManagement.Mvc;

    namespace TestFeatureFlags.Controllers
    {
        public class BetaController: Controller
        {
            private readonly IFeatureManager _featureManager;

            public BetaController(IFeatureManagerSnapshot featureManager) =>
                _featureManager = featureManager;

            [FeatureGate(MyFeatureFlags.Beta)]
            public IActionResult Index() => View();
        }
    }
    ```

1. A *views/_ViewImports. cshtmlban* regisztrálja a Feature Manager-címke segédjét egy `@addTagHelper` direktíva használatával:

    ```cshtml
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

    Az előző kód lehetővé teszi a `<feature>` címke Helper használatát a projekt *. cshtml* fájljaiban.

1. Nyissa meg a *_Layout. cshtml* mappát a *nézetek* \\ *megosztott* könyvtárában. Keresse meg a `<nav>` vonalkódot a alatt `<body>`  >  `<header>` . Szúrjon be egy új `<feature>` címkét a *Kezdőlap* és az *Adatvédelem* navigációs elemei közé, ahogyan az az alábbi kiemelt sorokban látható.

    :::code language="html" source="../../includes/azure-app-configuration-navbar.md" range="15-38" highlight="14-18":::

1. Hozzon létre egy *views/Beta* könyvtárat és egy *index. cshtml* fájlt, amely a következő korrektúrát tartalmazza:

    ```cshtml
    @{
        ViewData["Title"] = "Beta Home Page";
    }

    <h1>This is the beta website.</h1>
    ```

## <a name="build-and-run-the-app-locally"></a>Az alkalmazás helyi létrehozása és futtatása

1. Ha az alkalmazást a a .NET Core parancssori felülete használatával szeretné felépíteni, futtassa a következő parancsot a parancs-rendszerhéjban:

    ```dotnetcli
    dotnet build
    ```

1. A létrehozás sikeres befejezése után futtassa a következő parancsot a webalkalmazás helyi futtatásához:

    ```dotnetcli
    dotnet run
    ```

1. Nyisson meg egy böngészőablakot, és nyissa meg a következőt `http://localhost:5000` :, amely a helyileg üzemeltetett webalkalmazás alapértelmezett URL-címe. Ha dolgozik a Azure Cloud Shellban, válassza a **webes előnézet** gombot, majd a **configure (Konfigurálás**) lehetőséget. Ha a rendszer kéri, válassza a 5000-es portot.

    ![A webes előnézet gomb megkeresése](./media/quickstarts/cloud-shell-web-preview.png)

    A böngészőnek az alábbi képhez hasonló lapot kell megjelenítenie.

    :::image type="content" source="media/quickstarts/aspnet-core-feature-flag-local-before.png" alt-text="Helyi Gyorsindítás alkalmazás a módosítás előtt" border="true":::

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **minden erőforrás** lehetőséget, majd válassza ki a gyors útmutatóban létrehozott app Configuration Store-példányt.

1. Válassza a **szolgáltatás-kezelő** elemet. 

1. Engedélyezze a *béta* jelölőt a jelölőnégyzet bejelölésével **.**

1. Térjen vissza a parancs-rendszerhéjhoz. A `dotnet` <kbd>CTRL + C</kbd>billentyűkombináció lenyomásával szakítsa meg a futó folyamatot. Indítsa újra az alkalmazást a használatával `dotnet run` .

1. A böngésző oldalának frissítésével tekintheti meg az új konfigurációs beállításokat.

    :::image type="content" source="media/quickstarts/aspnet-core-feature-flag-local-after.png" alt-text="Helyi Gyorsindítás alkalmazás a módosítás után" border="true":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE[Azure App Configuration cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy új alkalmazás-konfigurációs tárolót, és felhasználta egy ASP.NET Core webalkalmazás funkcióinak kezelésére a [szolgáltatás-felügyeleti kódtárak](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration)segítségével.

* További információ a [szolgáltatások kezeléséről](./concept-feature-management.md).
* [Szolgáltatás-jelzők kezelése](./manage-feature-flags.md).
* [A szolgáltatás-jelzők használata egy ASP.net Core alkalmazásban](./use-feature-flags-dotnet-core.md).
* [Dinamikus konfiguráció használata egy ASP.NET Core alkalmazásban](./enable-dynamic-configuration-aspnet-core.md)