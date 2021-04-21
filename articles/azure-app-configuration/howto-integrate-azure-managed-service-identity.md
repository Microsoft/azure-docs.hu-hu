---
title: Felügyelt identitások használata az App Configuration eléréséhez
titleSuffix: Azure App Configuration
description: Hitelesítés Azure App Configuration identitások használatával
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.custom: devx-track-csharp, fasttrack-edit
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 591f767fe0ef2150f7fe180108f207b56f7c915f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764304"
---
# <a name="use-managed-identities-to-access-app-configuration"></a>Felügyelt identitások használata az App Configuration eléréséhez

Azure Active Directory [identitások leegyszerűsítik](../active-directory/managed-identities-azure-resources/overview.md) a felhőalkalmazás titkos kulcsok kezelését. Felügyelt identitással a kód használhatja a futtatott Azure-szolgáltatáshoz létrehozott egyszerű szolgáltatást. A felügyelt identitást a helyi kapcsolati sztringben vagy helyi kapcsolati sztringben tárolt Azure Key Vault helyett használhatja.

Azure App Configuration .NET Core-, .NET-keretrendszer- és Java Spring-ügyfélkódtárak beépítetten támogatják a felügyelt identitásokat. Bár nem kell használnia, a felügyelt identitással nincs szükség titkos adatokat tartalmazó hozzáférési jogkivonatra. A kód csak a szolgáltatásvégpont App Configuration fér hozzá a tárolóhoz. Ezt az URL-címet közvetlenül beágyazhatja a kódba titkos kód felfedése nélkül.

Ez a cikk bemutatja, hogyan használhatja ki a felügyelt identitás előnyeit a App Configuration. A rövid útmutatókban bemutatott webalkalmazásra épül. A folytatás előtt hozzon létre  [egy ASP.NET Core-alkalmazást a App Configuration](./quickstart-aspnet-core-app.md) használatával.

> [!NOTE]
> Ez a Azure App Service példát használ, de ugyanez a fogalom érvényes minden olyan azure-szolgáltatásra, amely támogatja a felügyelt identitásokat, például a [Azure Kubernetes Service,](../aks/use-azure-ad-pod-identity.md)az [Azure Virtual Machine](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)és a [Azure Container Instances.](../container-instances/container-instances-managed-identity.md) Ha a számítási feladat ezen szolgáltatások valamelyikében üzemel, akkor a szolgáltatás felügyelt identitástámogatását is használhatja.

Ez a cikk azt is bemutatja, hogyan használhatja a felügyelt identitást a App Configuration-hivatkozások Key Vault együtt. Egyetlen felügyelt identitással zökkenőmentesen hozzáférhet mindkét titkos Key Vault és konfigurációs értékből a App Configuration. Ha meg szeretné ismerkedni ezzel a képességgel, először fejezze be a Use Key Vault References with ASP.NET Core (Referencia-hivatkozások használata [ASP.NET Core használatával)](./use-key-vault-references-dotnet-core.md) lehetőséget.

Az oktatóanyagban található lépéseket bármilyen kódszerkesztővel el is használhatja. [Visual Studio Code](https://code.visualstudio.com/) kiváló lehetőség a Windows, macOS és Linux platformokon.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Felügyelt identitás hozzáférésének megadása a App Configuration.
> * Konfigurálja az alkalmazást, hogy felügyelt identitást használjon, amikor App Configuration.
> * Igény szerint konfigurálhatja az alkalmazást felügyelt identitás használatára, amikor egy Key Vault egy App Configuration Key Vault keresztül.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* [.NET Core SDK:](https://www.microsoft.com/net/download/windows).
* [Azure Cloud Shell konfigurálva van.](../cloud-shell/quickstart.md)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Felügyelt identitás hozzáadása

A felügyelt identitás portálon való beállításához először létre kell hoznia egy alkalmazást, majd engedélyeznie kell a funkciót.

1. Hozzon létre App Services-példányt [a Azure Portal](https://portal.azure.com) a szokásos módon. A portálon ássa el.

1. Görgessen le a **Bal oldali panel** Beállítások csoportjához, és válassza az Identitás **lehetőséget.**

1. A Rendszer **által hozzárendelt lapon** kapcsolja be az **Állapotot,** **majd** válassza a **Mentés lehetőséget.**

1. Ha a **rendszer a** rendszer által hozzárendelt felügyelt identitás engedélyezését kéri, az Igen választ.

    ![Felügyelt identitás beállítása a App Service](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Hozzáférés megadása a App Configuration

1. A Azure Portal válassza [a](https://portal.azure.com) **Minden erőforrás** lehetőséget, majd App Configuration a rövid útmutatóban létrehozott tárolót.

1. Válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget.

1. A Hozzáférés **ellenőrzése lapon** válassza a Hozzáadás **lehetőséget** a **Szerepkör-hozzárendelési kártya** hozzáadása felhasználói felületén.

1. A **Szerepkör alatt** válassza a App Configuration **adatolvasó lehetőséget.** A **Hozzáférés hozzárendelése a következőhöz alatt** App Service a Rendszer által  **hozzárendelt felügyelt identitás alatt.**

1. Az **Előfizetés alatt** válassza ki az Azure-előfizetését. Válassza ki App Service alkalmazáshoz szükséges erőforrást.

1. Kattintson a **Mentés** gombra.

    ![Felügyelt identitás hozzáadása](./media/add-managed-identity.png)

1. Nem kötelező: Ha a hozzáférését is Key Vault, kövesse a Következő hozzáférési szabályzat [hozzárendelése Key Vault utasításokat.](../key-vault/general/assign-access-policy-portal.md)

## <a name="use-a-managed-identity"></a>Felügyelt identitás használata

1. Adjon hozzá egy, az *Azure.Identity csomagra hivatkozó* hivatkozást:

    ```bash
    dotnet add package Azure.Identity
    ```

1. Keresse meg a App Configuration végpontját. Ez az URL-cím a tároló Hozzáférési kulcsok **lapján** található a Azure Portal.

1. Nyissa *appsettings.jsa következőt:*, és adja hozzá a következő szkriptet. Cserélje le a helyére a szögletes zárójeleket is, a saját App Configuration *\<service_endpoint>* URL-címére.

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. Nyissa *meg a Program.cs programot,* és adjon hozzá egy hivatkozást a és a `Azure.Identity` `Microsoft.Azure.Services.AppAuthentication` névtérhez:

    ```csharp-interactive
    using Azure.Identity;
    ```

1. Ha csak a közvetlenül a App Configuration tárolt értékeket szeretné elérni, frissítse a metódust a metódus lecserélésével (ez a csomagban `CreateWebHostBuilder` `config.AddAzureAppConfiguration()` `Microsoft.Azure.AppConfiguration.AspNetCore` található).

    > [!IMPORTANT]
    > `CreateHostBuilder` A a `CreateWebHostBuilder` .NET Core 3.0-ban lecseréli a helyére.  Válassza ki a környezetnek megfelelő szintaxist.

    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((hostingContext, config) =>
               {
                   var settings = config.Build();
                   config.AddAzureAppConfiguration(options =>
                       options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
               })
               .UseStartup<Startup>();
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                });
            })
            .UseStartup<Startup>());
    ```
    ---

    > [!NOTE]
    > Ha felhasználó által hozzárendelt felügyelt identitást szeretne **használni,** a [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential)létrehozásakor mindenképpen adja meg a clientId azonosítót.
    >```
    >config.AddAzureAppConfiguration(options =>
    >   options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential(<your_clientId>)));
    >```
    >Az [Azure-erőforrások felügyelt](../active-directory/managed-identities-azure-resources/managed-identities-faq.md#what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request)identitásai – gyakori kérdések részben leírtaknak megfelelő alapértelmezett módszer a felügyelt identitások használatának feloldására. Ebben az esetben az Azure Identity Library kényszeríti a kívánt identitás megadását, hogy a jövőben elkerülje a futásidejű problémákat (például ha új, felhasználó által hozzárendelt felügyelt identitást adnak hozzá, vagy ha a rendszer által hozzárendelt felügyelt identitás engedélyezve van). Ezért akkor is meg kell adnia a clientId azonosítót, ha csak egy felhasználó által hozzárendelt felügyelt identitás van meghatározva, és nincs rendszer által hozzárendelt felügyelt identitás.


1. Ha mindkét értéket és App Configuration használni Key Vault, frissítse a *Program.cs-t* az alább látható módon. Ez a kód a részeként hívja meg a kódot annak megadásához, hogy a konfigurációszolgáltató milyen hitelesítő adatokat használjon a `SetCredential` `ConfigureKeyVault` Key Vault.

    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((hostingContext, config) =>
               {
                   var settings = config.Build();
                   var credentials = new ManagedIdentityCredential();

                   config.AddAzureAppConfiguration(options =>
                   {
                       options.Connect(new Uri(settings["AppConfig:Endpoint"]), credentials)
                              .ConfigureKeyVault(kv =>
                              {
                                 kv.SetCredential(credentials);
                              });
                   });
               })
               .UseStartup<Startup>();
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    var credentials = new ManagedIdentityCredential();

                    config.AddAzureAppConfiguration(options =>
                    {
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), credentials)
                               .ConfigureKeyVault(kv =>
                               {
                                   kv.SetCredential(credentials);
                               });
                    });
                });
            })
            .UseStartup<Startup>());
    ```
    ---

    Most már úgy férhet hozzá Key Vault hivatkozásokhoz, mint bármely más App Configuration kulcshoz. A konfigurációszolgáltató a használatával hitelesíti magát `ManagedIdentityCredential` Key Vault és lekéri az értéket.

    > [!NOTE]
    > A `ManagedIdentityCredential` csak olyan Azure-környezetekben működik, amelyek támogatják a felügyelt identitások hitelesítését. A helyi környezetben nem működik. A kóddal helyi és Azure-környezetben is dolgozhat, mivel néhány hitelesítési lehetőség, például a felügyelt identitás is [`DefaultAzureCredential`](/dotnet/api/azure.identity.defaultazurecredential) használható.
    > 
    > Ha felhasználó által hozzárendelt felügyelt identitást szeretne használni az **Azure-ban** való üzembe helyezéskor, adja meg a `DefaultAzureCredential` [clientId azonosítót.](/dotnet/api/overview/azure/identity-readme#specifying-a-user-assigned-managed-identity-with-the-defaultazurecredential)

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="deploy-from-local-git"></a>Üzembe helyezés a helyi Gitből

A legegyszerűbben úgy engedélyezheti a Git helyi üzembe helyezését az alkalmazáshoz a Kudu buildkiszolgálóval, ha a [következőt Azure Cloud Shell.](https://shell.azure.com)

### <a name="configure-a-deployment-user"></a>Üzembe helyező felhasználó konfigurálása

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Helyi Git engedélyezése a Kuduval
Ha nincs helyi Git-adattára az alkalmazáshoz, inicializálni kell egyet. Helyi Git-adattár inicializálásához futtassa a következő parancsokat az alkalmazás projektkönyvtárában:

```cmd
git init
git add .
git commit -m "Initial version"
```

Ha engedélyezni szeretné a Git helyi üzembe helyezését az alkalmazáshoz a Kudu buildkiszolgálóval, futtassa a [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/#az_webapp_deployment_source_config_local_git) következőt a Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Ez a parancs az alábbi kimenethez hasonló eredményt ad:

```json
{
  "url": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
}
```

### <a name="deploy-your-project"></a>A projekt üzembe helyezése

A helyi _terminálablakban adjon_ hozzá egy távoli Azure-tárházat a helyi Git-adattárhoz. Cserélje le a helyére annak a távoli Gitnek az URL-címét, amely a Helyi Git engedélyezése a _\<url>_ [Kudu használatával parancsban kapott.](#enable-local-git-with-kudu)

```bash
git remote add azure <url>
```

A távoli Azure-mappához történő küldéssel helyezze üzembe az alkalmazást a következő paranccsal. Amikor a rendszer jelszót kér, adja meg az üzembe helyezési felhasználó [konfigurálásával kapcsolatos útmutatóban létrehozott jelszót.](#configure-a-deployment-user) Ne használja a bejelentkezéshez használt jelszót a Azure Portal.

```bash
git push azure main
```

A kimenetben futásidő-specifikus automatizálást láthat, például MSBuild for ASP.NET, Node.js `npm install` python `pip install` esetén.

### <a name="browse-to-the-azure-web-app"></a>Az Azure webalkalmazás megkeresése

Nyissa meg a webalkalmazást egy böngészőben, és ellenőrizze, hogy a tartalom telepítve van-e.

```bash
http://<app_name>.azurewebsites.net
```

## <a name="use-managed-identity-in-other-languages"></a>Felügyelt identitás használata más nyelveken

App Configuration- .NET-keretrendszer Java Spring-szolgáltatók beépített támogatást is támanak a felügyelt identitások számára. Ezen szolgáltatók konfigurálásakor használhatja az áruház URL-végpontját a teljes kapcsolati sztring helyett.

Például frissítheti a rövid .NET-keretrendszer létrehozott konzolalkalmazást, hogy a következő beállításokat adja meg a *App.config* fájlban:

```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" endpoint="${Endpoint}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="Endpoint" value ="Set via an environment variable - for example, dev, test, staging, or production endpoint." />
    </appSettings>
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban hozzáadott egy Azure-beli felügyelt identitást, amely leegyszerűsíti a hozzáférést App Configuration és javítja az alkalmazás hitelesítő adatainak kezelését. Ha többet szeretne megtudni a virtuális App Configuration, folytassa az Azure CLI-mintákkal.

> [!div class="nextstepaction"]
> [CLI-minták](./cli-samples.md)