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
ms.openlocfilehash: 7a9eb992ff0cb98fdae2920da2beeda0bbd8941b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877533"
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
> * Beállíthatja, hogy az alkalmazás felügyelt identitást használjon, amikor Key Vault egy App Configuration Key Vault keresztül.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* [.NET Core SDK:](https://dotnet.microsoft.com/download).
* [Azure Cloud Shell van konfigurálva.](../cloud-shell/quickstart.md)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Felügyelt identitás hozzáadása

A felügyelt identitás portálon való beállításához először létre kell hoznia egy alkalmazást, majd engedélyeznie kell a funkciót.

1. Hozzon létre App Services-példányt [a Azure Portal](https://portal.azure.com) a szokásos módon. A portálon ássa el.

1. Görgessen le a **Beállítások csoporthoz** a bal oldali panelen, és válassza az **Identitás lehetőséget.**

1. A Rendszer **által hozzárendelt lapon** kapcsolja be az **Állapotot,** **majd** válassza a **Mentés lehetőséget.**

1. Ha a **rendszer a** rendszer által hozzárendelt felügyelt identitás engedélyezését kéri, válaszoljon az Igen válaszra.

    ![Felügyelt identitás beállítása a App Service](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Hozzáférés megadása a App Configuration

1. A [Azure Portal](https://portal.azure.com)válassza a **Minden** erőforrás lehetőséget, majd App Configuration a rövid útmutatóban létrehozott erőforrástárat.

1. Válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget.

1. A Hozzáférés **ellenőrzése lapon** válassza a Hozzáadás **lehetőséget** a **Szerepkör-hozzárendelési kártya** hozzáadása felhasználói felületén.

1. A **Szerepkör alatt** válassza a App Configuration **adatolvasó lehetőséget.** A **Hozzáférés hozzárendelése a következőhöz alatt** válassza **App Service** a Rendszer által **hozzárendelt felügyelt identitás alatt.**

1. Az **Előfizetés alatt** válassza ki az Azure-előfizetését. Válassza ki App Service az alkalmazáshoz szükséges erőforrást.

1. Kattintson a **Mentés** gombra.

    ![Felügyelt identitás hozzáadása](./media/add-managed-identity.png)

1. Nem kötelező: Ha a hozzáférését is Key Vault, kövesse a Következő hozzáférési szabályzat [hozzárendelése Key Vault utasításokat.](../key-vault/general/assign-access-policy-portal.md)

## <a name="use-a-managed-identity"></a>Felügyelt identitás használata

1. Adjon hozzá egy, az *Azure.Identity csomagra hivatkozó* hivatkozást:

    ```bash
    dotnet add package Azure.Identity
    ```

1. Keresse meg a App Configuration végpontját. Ez az URL-cím a tároló **Hozzáférési** kulcsok lapján található a Azure Portal.

1. Nyissa *appsettings.jsa fájlban,* és adja hozzá a következő szkriptet. Cserélje le a et a szögletes zárójelekkel együtt a saját App Configuration *\<service_endpoint>* URL-címére.

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. Nyissa *meg a Program.cs programot,* és adjon hozzá egy, a és a `Azure.Identity` `Microsoft.Azure.Services.AppAuthentication` névtérre hivatkozó hivatkozást:

    ```csharp-interactive
    using Azure.Identity;
    ```

1. Ha csak a közvetlenül a App Configuration tárolt értékeket szeretné elérni, frissítse a metódust a metódus lecserélével (ez a csomagban `CreateWebHostBuilder` `config.AddAzureAppConfiguration()` `Microsoft.Azure.AppConfiguration.AspNetCore` található).

    > [!IMPORTANT]
    > `CreateHostBuilder` A a `CreateWebHostBuilder` .NET Core 3.0-ban cseréli le a helyére.  Válassza ki a környezetnek megfelelő szintaxist.

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
    > Ha felhasználó által hozzárendelt felügyelt identitást szeretne használni, a [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential)létrehozásakor mindenképpen adja meg a clientId azonosítót.
    >```
    >config.AddAzureAppConfiguration(options =>
    >   options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential(<your_clientId>)));
    >```
    >Az [Azure-erőforrások felügyelt](../active-directory/managed-identities-azure-resources/managed-identities-faq.md#what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request)identitásai – gyakori kérdések részben leírtaknak megfelelő alapértelmezett módszer a használt felügyelt identitás feloldására. Ebben az esetben az Azure Identity Library kényszeríti a kívánt identitás megadását, hogy a jövőben elkerülje a lehetséges futásidejű problémákat (például ha új, felhasználó által hozzárendelt felügyelt identitást adnak hozzá, vagy ha a rendszer által hozzárendelt felügyelt identitás engedélyezve van). Ezért akkor is meg kell adnia a clientId azonosítót, ha csak egy felhasználó által hozzárendelt felügyelt identitás van meghatározva, és nincs rendszer által hozzárendelt felügyelt identitás.


1. Ha mindkét értéket App Configuration és a Key Vault használni, frissítse a *Program.cs-t* az alább látható módon. Ez a kód a részeként hívja meg a kódot, hogy megszabadulja a konfigurációszolgáltatónak, hogy milyen hitelesítő adatokat kell `SetCredential` `ConfigureKeyVault` használnia az Key Vault.

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

    Mostantól úgy férhet hozzá Key Vault hivatkozásokhoz, mint bármely App Configuration kulcshoz. A konfigurációszolgáltató a használatával hitelesíti magát `ManagedIdentityCredential` Key Vault és lekéri az értéket.

    > [!NOTE]
    > A csak olyan Azure-környezetekben működik, amelyek támogatják a `ManagedIdentityCredential` felügyelt identitások hitelesítését. A helyi környezetben nem működik. A használatával a kód helyi és Azure-környezetben is működik, mivel néhány hitelesítési beállításra, például a felügyelt [`DefaultAzureCredential`](/dotnet/api/azure.identity.defaultazurecredential) identitásra fog visszaesni.
    > 
    > Ha felhasználó által hozzárendelt felügyelt identitást szeretne használni a-hez az **Azure-ban** való üzembe helyezéskor, adja meg a `DefaultAzureCredential` [clientId azonosítót.](/dotnet/api/overview/azure/identity-readme#specifying-a-user-assigned-managed-identity-with-the-defaultazurecredential)

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="deploy-from-local-git"></a>Üzembe helyezés a helyi Gitből

A legegyszerűbben úgy engedélyezheti az alkalmazás helyi Git-üzembe helyezését a Kudu buildkiszolgálóval, ha a következő [Azure Cloud Shell.](https://shell.azure.com)

### <a name="configure-a-deployment-user"></a>Üzembe helyező felhasználó konfigurálása

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Helyi Git engedélyezése a Kuduval
Ha nincs helyi Git-adattára az alkalmazáshoz, inicializálni kell egyet. Helyi Git-adattár inicializálásához futtassa a következő parancsokat az alkalmazás projektkönyvtárában:

```cmd
git init
git add .
git commit -m "Initial version"
```

Ha engedélyezni szeretné az alkalmazás helyi Git-üzembe helyezését a Kudu buildkiszolgálóval, futtassa a [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/#az_webapp_deployment_source_config_local_git) következőt: Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Ez a parancs a következő kimenethez hasonló eredményt ad:

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

A távoli Azure-mappához történő küldéssel helyezze üzembe az alkalmazást a következő paranccsal. Amikor a rendszer jelszót kér, adja meg az Üzembe helyezési felhasználó konfigurálása [során létrehozott jelszót.](#configure-a-deployment-user) Ne használja a bejelentkezéshez használt jelszót a Azure Portal.

```bash
git push azure main
```

A kimenetben futásidő-specifikus automatizálást láthat, például msBuild for ASP.NET, Node.js `npm install` és `pip install` Python esetén.

### <a name="browse-to-the-azure-web-app"></a>Az Azure webalkalmazás megkeresése

Nyissa meg a webalkalmazást egy böngészőben, és ellenőrizze, hogy a tartalom telepítve van-e.

```bash
http://<app_name>.azurewebsites.net
```

## <a name="use-managed-identity-in-other-languages"></a>Felügyelt identitás használata más nyelveken

App Configuration és a Java Spring .NET-keretrendszer szolgáltatók beépített támogatást is támanak a felügyelt identitáshoz. Ezen szolgáltatók konfigurálásakor használhatja az áruház URL-végpontját a teljes kapcsolati sztring helyett.

Frissítheti például a .NET-keretrendszer létrehozott konzolalkalmazást, hogy a következő beállításokat adja meg a *App.config* fájlban:

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
Ebben az oktatóanyagban hozzáadott egy Azure-beli felügyelt identitást, amely leegyszerűsíti a hozzáférést App Configuration és javítja az alkalmazás hitelesítő adatainak kezelését. Ha többet szeretne megtudni a App Configuration, folytassa az Azure CLI-mintákkal.

> [!div class="nextstepaction"]
> [CLI-minták](./cli-samples.md)