---
title: Gyors útmutató az Azure-alkalmazások konfigurálásához a Azure Functionskal | Microsoft Docs
description: Ebben a rövid útmutatóban egy Azure Functions alkalmazást hoz végre az Azure app Configuration és a C# használatával. Hozzon létre és kapcsolódjon egy alkalmazás-konfigurációs tárolóhoz. A függvény helyi tesztelése.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 09/28/2020
ms.author: alkemper
ms.openlocfilehash: 9d378b21132e6646329c459401255ef9a3ed9426
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724238"
---
# <a name="quickstart-create-an-azure-functions-app-with-azure-app-configuration"></a>Gyors útmutató: Azure Functions-alkalmazás létrehozása az Azure-alkalmazás konfigurálásával

Ebben a rövid útmutatóban beépíti az Azure app Configuration szolgáltatást egy Azure Functions alkalmazásba, hogy központilag központosítsa az összes Alkalmazásbeállítások tárolását és kezelését.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/dotnet)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) az **Azure-fejlesztési** számítási feladattal.
- [Eszközök Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>Alkalmazás-konfigurációs tároló létrehozása

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7.   >    >  A következő kulcs-érték párok hozzáadásához válassza a Configuration Explorer +**kulcs létrehozása-érték** elemet:

    | Kulcs | Érték |
    |---|---|
    | TestApp: beállítások: üzenet | Adatok az Azure-alkalmazás konfigurációjától |

    Most hagyja üresen a **címke** és a **tartalom típusát** .

8. Kattintson az **Alkalmaz** gombra.

## <a name="create-a-functions-app"></a>Functions-alkalmazás létrehozása

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Kapcsolódás alkalmazás-konfigurációs tárolóhoz
Ez a projekt a [függőségi befecskendezést fogja használni a .net Azure Functionsban](../azure-functions/functions-dotnet-dependency-injection.md) , és további konfigurációs forrásként adja hozzá az Azure-alkalmazások konfigurációját.

1. Kattintson a jobb gombbal a projektre, és válassza a **NuGet-csomagok kezelése** lehetőséget. A **Tallózás** lapon keresse meg és adja hozzá a következő NuGet-csomagokat a projekthez.
   - [Microsoft.Extensions.Configszülő. AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration/) -4.1.0 vagy újabb verzió
   - [Microsoft. Azure. functions. Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/) vagy újabb verzió 

2. Adjon hozzá egy új fájlt a *Startup. cs* fájlhoz a következő kóddal. Definiál egy nevű osztályt `Startup` , amely megvalósítja az `FunctionsStartup` absztrakt osztályt. A Azure Functions indításakor használt típus nevének megadásához egy szerelvény-attribútumot kell használni.

    A `ConfigureAppConfiguration` metódus felülbírálható, és az Azure-alkalmazás konfigurációs szolgáltatója további konfigurációs forrásként van hozzáadva a hívásával `AddAzureAppConfiguration()` . A `Configure` metódus üres marad, mert nem kell ezen a ponton semmilyen szolgáltatást regisztrálnia.
    
    ```csharp
    using System;
    using Microsoft.Azure.Functions.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;

    [assembly: FunctionsStartup(typeof(FunctionApp.Startup))]

    namespace FunctionApp
    {
        class Startup : FunctionsStartup
        {
            public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
            {
                string cs = Environment.GetEnvironmentVariable("ConnectionString");
                builder.ConfigurationBuilder.AddAzureAppConfiguration(cs);
            }

            public override void Configure(IFunctionsHostBuilder builder)
            {
            }
        }
    }
    ```

3. Nyissa meg a *Function1. cs*-t, és adja hozzá a következő névteret.

    ```csharp
    using Microsoft.Extensions.Configuration;
    ```

   Vegyen fel egy konstruktort, amely a `IConfiguration` függőségi injekción keresztüli példány beszerzésére szolgál.

    ```csharp
    private readonly IConfiguration _configuration;

    public Function1(IConfiguration configuration)
    {
        _configuration = configuration;
    }
    ```

4. Frissítse a `Run` metódust a konfiguráció értékeinek olvasásához.

    ```csharp
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        string keyName = "TestApp:Settings:Message";
        string message = _configuration[keyName];

        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

   Az `Function1` osztály és a `Run` metódus nem lehet statikus. Távolítsa el a `static` módosítót, ha az automatikusan lett létrehozva.

## <a name="test-the-function-locally"></a>A függvény helyi tesztelése

1. Állítson be egy **ConnectionString** nevű környezeti változót, és állítsa be az alkalmazás konfigurációs tárolójának hozzáférési kulcsára. Ha a Windows-parancssort használja, futtassa a következő parancsot, és indítsa újra a parancssort, hogy a módosítás érvénybe lépjen:

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

2. Nyomja le az F5 billentyűt a függvény teszteléséhez. Ha a rendszer kéri, fogadja el a Visual Studiótól érkező kérést **Azure functions Core (CLI)** eszközök letöltéséhez és telepítéséhez. Előfordulhat, hogy egy tűzfal-kivételt is engedélyeznie kell, hogy az eszközök kezelni tudják a HTTP-kérelmeket.

3. Másolja a függvény URL-címét az Azure-függvény futtatókörnyezetéből.

    ![Gyors üzembe helyezési funkció hibakeresése a VS-ben](./media/quickstarts/function-visual-studio-debugging.png)

4. Illessze be a HTTP-kérelem URL-címét a böngésző címsorába. Az alábbi képen a böngészőben a függvény által visszaadott helyi GET kérelemre adott válasz látható.

    ![A Gyorsindítás funkció helyi elindítása](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy új alkalmazás-konfigurációs tárolót, és azt egy Azure Functions alkalmazással használta az [alkalmazás-konfigurációs szolgáltatón](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration)keresztül. Ha szeretné megtudni, hogyan frissítheti Azure Functions alkalmazást a konfiguráció dinamikus frissítéséhez, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Dinamikus konfiguráció engedélyezése az Azure Functionsben](./enable-dynamic-configuration-azure-functions-csharp.md)