---
title: Oktatóanyag az Azure-alkalmazások konfigurációjának dinamikus konfigurációjának használatához egy Azure Functions alkalmazásban | Microsoft Docs
description: Ez az oktatóanyag azt ismerteti, hogyan lehet dinamikusan frissíteni a Azure Functions-alkalmazások konfigurációs információit
services: azure-app-configuration
documentationcenter: ''
author: zhenlan
manager: qingye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 11/17/2019
ms.author: zhenlwa
ms.custom: devx-track-csharp, azure-functions
ms.tgt_pltfrm: Azure Functions
ms.openlocfilehash: add4b54adb02db09536f4e56a7f039c46245c182
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97963560"
---
# <a name="tutorial-use-dynamic-configuration-in-an-azure-functions-app"></a>Oktatóanyag: dinamikus konfiguráció használata egy Azure Functions alkalmazásban

Az alkalmazás konfigurációja .NET-konfigurációs szolgáltató támogatja a gyorsítótárazást és a konfiguráció frissítését dinamikusan vezérelt alkalmazási tevékenységgel. Ez az oktatóanyag bemutatja, hogyan valósítható meg a dinamikus konfigurációs frissítések a kódban. Ez a rövid útmutatókban bemutatott Azure Functions alkalmazásra épül. Mielőtt továbblépne, először [hozzon létre egy Azure functions-alkalmazást az Azure app Configuration szolgáltatással](./quickstart-azure-functions-csharp.md) .

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Állítsa be a Azure Functions alkalmazást, hogy frissítse a konfigurációját az alkalmazás konfigurációs tárolójának változásaira válaszul.
> * Adja meg a legújabb konfigurációt a Azure Functions-hívásokhoz.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) az **Azure-fejlesztési** számítási feladattal
- [Eszközök Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version)
- [Azure functions-alkalmazás létrehozása az Azure-alkalmazás konfigurálásával –](./quickstart-azure-functions-csharp.md) gyors üzembe helyezési útmutató

## <a name="reload-data-from-app-configuration"></a>Adatok újratöltése az alkalmazás konfigurációjától

1. Nyissa meg a *Startup. cs* programot, és frissítse a `ConfigureAppConfiguration` metódust. 

   A `ConfigureRefresh` metódus regisztrálja azokat a beállításokat, amelyeket a rendszer az alkalmazáson belüli frissítés indításakor végez, amelyet a későbbi lépésben fog elvégezni a hozzáadáskor `_configurationRefresher.TryRefreshAsync()` . A `refreshAll` paraméter arra utasítja az alkalmazás-konfigurációs szolgáltatót, hogy töltse be a teljes konfigurációt, amikor a rendszer változást észlel a regisztrált beállításban.

    A frissítéshez regisztrált összes beállításhoz alapértelmezett gyorsítótár-elévülési idő 30 másodperc. A metódus meghívásával frissíthető `AzureAppConfigurationRefreshOptions.SetCacheExpiration` .

    ```csharp
    public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
    {
        builder.ConfigurationBuilder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   // Load all keys that start with `TestApp:`
                   .Select("TestApp:*")
                   // Configure to reload configuration if the registered 'Sentinel' key is modified
                   .ConfigureRefresh(refreshOptions =>
                      refreshOptions.Register("TestApp:Settings:Sentinel", refreshAll: true));
        });
    }
    ```

   > [!TIP]
   > Ha több kulcs-érték is frissül az alkalmazás konfigurációjában, akkor általában nem szeretné, hogy az alkalmazás az összes módosítás előtt újratöltse a konfigurációt. A **Sentinel** -kulcsot regisztrálhatja, és csak akkor frissítheti, ha az összes többi konfigurációs módosítás befejeződik. Ezzel biztosítható az alkalmazás konfigurációjának konzisztenciája.

2. Frissítse a `Configure` metódust, hogy az Azure app Configuration Services elérhető legyen a függőségi befecskendezéssel.

    ```csharp
    public override void Configure(IFunctionsHostBuilder builder)
    {
        builder.Services.AddAzureAppConfiguration();
    }
    ```

3. Nyissa meg a *Function1. cs*-t, és adja hozzá a következő névtereket.

    ```csharp
    using System.Linq;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

   Frissítse a konstruktort, hogy beszerezze a `IConfigurationRefresherProvider` függőségi injekción keresztüli példányt, amelyről beszerezheti a példányát `IConfigurationRefresher` .

    ```csharp
    private readonly IConfiguration _configuration;
    private readonly IConfigurationRefresher _configurationRefresher;

    public Function1(IConfiguration configuration, IConfigurationRefresherProvider refresherProvider)
    {
        _configuration = configuration;
        _configurationRefresher = refresherProvider.Refreshers.First();
    }
    ```

4. Frissítse a `Run` metódust és a jelet a konfiguráció frissítéséhez a `TryRefreshAsync` függvények hívásának elején található metódus használatával. Ha nem éri el a gyorsítótár lejárati idejét, a nem-op lesz. Távolítsa el az `await` operátort, ha a konfigurációt úgy szeretné frissíteni, hogy blokkolja az aktuális függvények hívását. Ebben az esetben a későbbi functions-hívások frissített értéket kapnak.

    ```csharp
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await _configurationRefresher.TryRefreshAsync(); 

        string keyName = "TestApp:Settings:Message";
        string message = _configuration[keyName];
            
        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>A függvény helyi tesztelése

1. Állítson be egy **ConnectionString** nevű környezeti változót, és állítsa be az alkalmazás konfigurációs tárolójának hozzáférési kulcsára. Ha a Windows-parancssort használja, futtassa a következő parancsot, és indítsa újra a parancssort, hogy a módosítás érvénybe lépjen:

    ```console
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Ha a Windows PowerShellt használja, futtassa a következő parancsot:

    ```powershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Ha macOS vagy Linux rendszert használ, futtassa a következő parancsot:

    ```console
    export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. A függvény teszteléséhez nyomja le az F5 billentyűt. Ha a rendszer kéri, fogadja el a Visual Studiótól érkező kérést **Azure functions Core (CLI)** eszközök letöltéséhez és telepítéséhez. Előfordulhat, hogy egy tűzfal-kivételt is engedélyeznie kell, hogy az eszközök kezelni tudják a HTTP-kérelmeket.

3. Másolja a függvény URL-címét az Azure-függvény futtatókörnyezetéből.

    ![Gyors üzembe helyezési funkció hibakeresése a VS-ben](./media/quickstarts/function-visual-studio-debugging.png)

4. Illessze be a HTTP-kérelem URL-címét a böngésző címsorába. Az alábbi képen a böngészőben a függvény által visszaadott helyi GET kérelemre adott válasz látható.

    ![A Gyorsindítás funkció helyi elindítása](./media/quickstarts/dotnet-core-function-launch-local.png)

5. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **minden erőforrás** lehetőséget, majd válassza ki a gyors útmutatóban létrehozott alkalmazás-konfigurációs tárolót.

6. Válassza a **Configuration Explorer** lehetőséget, és frissítse a következő kulcs értékét:

    | Kulcs | Érték |
    |---|---|
    | TestApp: beállítások: üzenet | Adatok az Azure-alkalmazás konfigurációjában – frissítve |

   Ezután hozza létre a Sentinel-kulcsot, vagy módosítsa az értékét, ha az már létezik, például:

    | Kulcs | Érték |
    |---|---|
    | TestApp: beállítások: Sentinel | v1 |


7. Frissítse a böngészőt néhányszor. Ha a gyorsítótárazott beállítás 30 másodperc elteltével lejár, az oldal megjeleníti a függvények a frissített értékkel való hívásának válaszát.

    ![A gyors üzembe helyezési funkció helyi frissítése](./media/quickstarts/dotnet-core-function-refresh-local.png)

> [!NOTE]
> Az oktatóanyagban használt példa kód letölthető az [app Configuration GitHub](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction)-tárházból.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban engedélyezte a Azure Functions alkalmazást, hogy dinamikusan frissítse a konfigurációs beállításokat az alkalmazás konfigurációjában. Ha meg szeretné tudni, hogyan használható az Azure felügyelt identitása az alkalmazás-konfigurációhoz való hozzáférés egyszerűsítéséhez, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Felügyelt identitások integrációja](./howto-integrate-azure-managed-service-identity.md)
