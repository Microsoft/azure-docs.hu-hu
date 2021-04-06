---
title: Útmutató a szolgáltatás-jelzők hozzáadásához a Azure Functionshoz | Microsoft Docs
description: Ebben a rövid útmutatóban a Azure Functions az Azure-alkalmazás konfigurációjában található szolgáltatás-jelzőket használja, és a függvényt helyileg tesztelheti.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 8/26/2020
ms.author: alkemper
ms.openlocfilehash: 96efc0ea6300e482ddeeda8fa177847f02b7e126
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724251"
---
# <a name="quickstart-add-feature-flags-to-an-azure-functions-app"></a>Gyors útmutató: szolgáltatás-jelzők hozzáadása Azure Functions-alkalmazáshoz

Ebben a rövid útmutatóban létrehoz egy Azure Functions alkalmazást, és használja a funkció jelzőit. Az Azure-alkalmazás konfigurációjában található szolgáltatások kezelése funkcióval központilag tárolhatja az összes funkció jelzőjét, és szabályozhatja az állapotukat.

A .NET-szolgáltatások felügyeleti könyvtárai kiterjesztik a keretrendszert a Feature Flag támogatásával. Ezek a kódtárak a .NET konfigurációs rendszerére épülnek. Integrálva vannak az alkalmazások konfigurációjával a .NET-konfigurációs szolgáltatón keresztül.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) az **Azure-fejlesztési** számítási feladattal.
- [Eszközök Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>Alkalmazás-konfigurációs tároló létrehozása

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Válassza a **funkció-kezelő**  >  **+ Hozzáadás** elemet a szolgáltatáshoz tartozó jelző hozzáadásához `Beta` .

    > [!div class="mx-imgBorder"]
    > ![A szolgáltatás jelölő engedélyezése Beta néven](media/add-beta-feature-flag.png)

    `label`Most hagyja és `Description` meghatározatlan maradjon.

8. Válassza az **alkalmaz** lehetőséget az új funkció jelző mentéséhez.

## <a name="create-a-functions-app"></a>Functions-alkalmazás létrehozása

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Kapcsolódás alkalmazás-konfigurációs tárolóhoz

Ez a projekt a [függőségi befecskendezést fogja használni a .net Azure Functionsban](../azure-functions/functions-dotnet-dependency-injection.md). Az Azure-alkalmazások konfigurációját kiegészítő konfigurációs forrásként adja hozzá, ahol a szolgáltatások jelzői vannak tárolva.

1. Kattintson a jobb gombbal a projektre, és válassza a **NuGet-csomagok kezelése** lehetőséget. A **Tallózás** lapon keresse meg és adja hozzá a következő NuGet-csomagokat a projekthez.
   - [Microsoft.Extensions.Configszülő. AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration/) -4.1.0 vagy újabb verzió
   - [Microsoft. FeatureManagement](https://www.nuget.org/packages/Microsoft.FeatureManagement/) 2.2.0 vagy újabb verzió
   - [Microsoft. Azure. functions. Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/) vagy újabb verzió 

2. Adjon hozzá egy új fájlt a *Startup. cs* fájlhoz a következő kóddal. Definiál egy nevű osztályt `Startup` , amely megvalósítja az `FunctionsStartup` absztrakt osztályt. A Azure Functions indításakor használt típus nevének megadásához egy szerelvény-attribútumot kell használni.

    ```csharp
    using System;
    using Microsoft.Azure.Functions.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;
    using Microsoft.FeatureManagement;

    [assembly: FunctionsStartup(typeof(FunctionApp.Startup))]

    namespace FunctionApp
    {
        class Startup : FunctionsStartup
        {
            public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
            {
            }

            public override void Configure(IFunctionsHostBuilder builder)
            {
            }
        }
    }
    ```


3. Frissítse a `ConfigureAppConfiguration` metódust, és adja hozzá az Azure-alkalmazás konfigurációs szolgáltatóját további konfigurációs forrásként a hívásával `AddAzureAppConfiguration()` . 

   A `UseFeatureFlags()` metódus azt jelzi, hogy a szolgáltató betölti a szolgáltatás jelzőit. A módosítások ismételt ellenőrzését megelőzően az összes funkció-jelző alapértelmezett gyorsítótár-lejárata 30 másodperc. A lejárati időköz a `FeatureFlagsOptions.CacheExpirationInterval` metódusnak átadott tulajdonság beállításával frissíthető `UseFeatureFlags` . 

    ```csharp
    public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
    {
        builder.ConfigurationBuilder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   .Select("_")
                   .UseFeatureFlags();
        });
    }
    ```
   > [!TIP]
   > Ha nem szeretné, hogy a szolgáltatáshoz tartozó jelzők ne legyenek betöltve az alkalmazásba, akkor meghívhatja, `Select("_")` hogy csak a nem létező "_" dummy-kulcsot töltsön be. Alapértelmezés szerint a rendszer az alkalmazás konfigurációs tárolójában lévő összes konfigurációs kulcsot betölti, ha nincs `Select` metódus meghívva.

4. Frissítse a `Configure` metódust, hogy az Azure app Configuration Services és a Feature Manager elérhető legyen a függőségi injektáláson keresztül.

    ```csharp
    public override void Configure(IFunctionsHostBuilder builder)
    {
        builder.Services.AddAzureAppConfiguration();
        builder.Services.AddFeatureManagement();
    }
    ```

5. Nyissa meg a *Function1. cs*-t, és adja hozzá a következő névtereket.

    ```csharp
    using System.Linq;
    using Microsoft.FeatureManagement;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

   Vegyen fel egy konstruktort, amely a `_featureManagerSnapshot` és a függőségi befecskendezések példányainak beszerzésére szolgál `IConfigurationRefresherProvider` . A alkalmazásban `IConfigurationRefresherProvider` beszerezheti a példányát `IConfigurationRefresher` .

    ```csharp
    private readonly IFeatureManagerSnapshot _featureManagerSnapshot;
    private readonly IConfigurationRefresher _configurationRefresher;

    public Function1(IFeatureManagerSnapshot featureManagerSnapshot, IConfigurationRefresherProvider refresherProvider)
    {
        _featureManagerSnapshot = featureManagerSnapshot;
        _configurationRefresher = refresherProvider.Refreshers.First();
    }
    ```

6. Frissítse a `Run` metódust a megjelenített üzenet értékének módosításához a szolgáltatás jelzője állapotától függően.

   A `TryRefreshAsync` metódust a functions hívásának elején hívja meg a szolgáltatás-jelzők frissítéséhez. Ha nem éri el a gyorsítótár lejárati idejét, a nem-op lesz. Távolítsa el a `await` kezelőt, ha inkább a funkció jelzőit szeretné frissíteni az aktuális függvények hívásának letiltása nélkül. Ebben az esetben a későbbi functions-hívások frissített értéket kapnak.

    ```csharp
    [FunctionName("Function1")]
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
        ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await _configurationRefresher.TryRefreshAsync();

        string message = await _featureManagerSnapshot.IsEnabledAsync("Beta")
                ? "The Feature Flag 'Beta' is turned ON"
                : "The Feature Flag 'Beta' is turned OFF";

        return (ActionResult)new OkObjectResult(message);
    }
    ```

## <a name="test-the-function-locally"></a>A függvény helyi tesztelése

1. Állítson be egy **ConnectionString** nevű környezeti változót, ahol az érték az alkalmazás konfigurációs tárolójában korábban lekért kapcsolati sztring a **hozzáférési kulcsok** alatt. Ha a Windows-parancssort használja, futtassa a következő parancsot, és indítsa újra a parancssort, hogy a módosítás érvénybe lépjen:

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Ha a Windows PowerShellt használja, futtassa a következő parancsot:

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Ha macOS vagy Linux rendszert használ, futtassa a következő parancsot:

    ```bash
        export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

1. Nyomja le az F5 billentyűt a függvény teszteléséhez. Ha a rendszer kéri, fogadja el a Visual Studiótól érkező kérést **Azure functions Core (CLI)** eszközök letöltéséhez és telepítéséhez. Előfordulhat, hogy egy tűzfal-kivételt is engedélyeznie kell, hogy az eszközök kezelni tudják a HTTP-kérelmeket.

1. Másolja a függvény URL-címét az Azure-függvény futtatókörnyezetéből.

    ![Gyors üzembe helyezési funkció hibakeresése a VS-ben](./media/quickstarts/function-visual-studio-debugging.png)

1. Illessze be a HTTP-kérelem URL-címét a böngésző címsorába. Az alábbi képen látható, hogy a funkció jelzője `Beta` le van tiltva. 

    ![A gyors üzembe helyezés funkció jelzője letiltva](./media/quickstarts/functions-launch-ff-disabled.png)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **minden erőforrás** lehetőséget, majd válassza ki a létrehozott alkalmazás-konfigurációs tárolót.

1. Válassza a **szolgáltatásvezérlő** lehetőséget, majd módosítsa a **bétaverzió** állapotát **a be** értékre.

1. Frissítse a böngészőt néhányszor. Ha a gyorsítótárazott funkció jelzője 30 másodperc elteltével lejár, az oldalnak meg kell változtatnia, hogy a funkció jelzője `Beta` be legyen kapcsolva, ahogy az alábbi képen is látható.
 
    ![A rövid útmutató funkció funkciójának jelzője engedélyezve](./media/quickstarts/functions-launch-ff-enabled.png)

> [!NOTE]
> Az oktatóanyagban használt példa kód az [Azure app Configuration GitHub](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction)-tárházból tölthető le.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozta a szolgáltatás jelölőjét, és egy Azure Functions alkalmazással használta a [Microsoft. FeatureManagement](/dotnet/api/microsoft.featuremanagement) könyvtáron keresztül.

- További információ a [szolgáltatások kezeléséről](./concept-feature-management.md)
- [Funkciójelölők kezelése](./manage-feature-flags.md)
- [Feltételes funkciójelölők használata](./howto-feature-filters-aspnet-core.md)
- [A funkciók lépcsőzetes bevezetésének engedélyezése a megcélzott célközönségek számára](./howto-targetingfilter-aspnet-core.md)
- [Dinamikus konfiguráció használata egy Azure Functions alkalmazásban](./enable-dynamic-configuration-azure-functions-csharp.md)