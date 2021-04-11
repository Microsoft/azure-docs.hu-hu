---
title: Felügyelt identitások használata az App Configuration eléréséhez
titleSuffix: Azure App Configuration
description: Hitelesítés az Azure-alkalmazások konfigurációjában a felügyelt identitások használatával
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.custom: devx-track-csharp, fasttrack-edit
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: b4b67a6bf4eea72b3a81bd01398cc60cc33c0d0a
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227041"
---
# <a name="use-managed-identities-to-access-app-configuration"></a>Felügyelt identitások használata az App Configuration eléréséhez

Azure Active Directory [felügyelt identitások](../active-directory/managed-identities-azure-resources/overview.md) egyszerűbbé teszik a Felhőbeli alkalmazások titkok kezelését. Felügyelt identitás esetén a kód a szolgáltatásban futtatott Azure-szolgáltatáshoz létrehozott egyszerű szolgáltatásnevet is használhatja. A felügyelt identitást nem külön hitelesítő adat, hanem Azure Key Vault vagy helyi kapcsolatok karakterlánca tárolja.

Az Azure app Configuration és a .NET Core, a .NET Framework és a Java Spring-ügyfél kódtárai felügyelt identitás-támogatással rendelkeznek. Habár nem szükséges a használatához, a felügyelt identitás szükségtelenné teszi a titkos kulcsokat tartalmazó hozzáférési token használatát. A kód csak a szolgáltatási végpont használatával férhet hozzá az alkalmazás konfigurációs tárolójához. Ezt az URL-címet közvetlenül a kódban ágyazhatja be, és nem teheti közzé a titkos kódot.

Ez a cikk bemutatja, hogyan veheti igénybe a felügyelt identitást az alkalmazások konfigurációjának eléréséhez. A szolgáltatás a gyors útmutatókban bemutatott webalkalmazásra épül. A folytatás előtt  [hozzon létre egy ASP.net Core alkalmazást az alkalmazás konfigurálásával](./quickstart-aspnet-core-app.md) először.

> [!NOTE]
> Ez a cikk a Azure App Service példaként használja, de ugyanez a fogalom vonatkozik a felügyelt identitást támogató egyéb Azure-szolgáltatásokra, például az [Azure Kubernetes szolgáltatásra](../aks/use-azure-ad-pod-identity.md), az Azure-beli [virtuális gépre](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)és a [Azure Container Instancesra](../container-instances/container-instances-managed-identity.md). Ha a munkaterhelést ezen szolgáltatások egyikén üzemelteti, használhatja a szolgáltatás felügyelt identitásának támogatását is.

A cikk azt is bemutatja, hogyan használható a felügyelt identitás az alkalmazás konfigurációjának Key Vault hivatkozásaival együtt. Egyetlen felügyelt identitással zökkenőmentesen érheti el mindkét titkot Key Vault és konfigurációs értékektől az alkalmazás konfigurációjában. Ha szeretné felfedezni ezt a képességet, fejezze be [a Key Vault referenciák használatát a ASP.net Core](./use-key-vault-references-dotnet-core.md) először.

Az oktatóanyag lépéseihez bármilyen Kódszerkesztő használható. A [Visual Studio Code](https://code.visualstudio.com/) kiváló lehetőség a Windows, MacOS és Linux platformokon.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Felügyelt identitás elérésének biztosítása az alkalmazás konfigurációjához.
> * Konfigurálja úgy az alkalmazást, hogy felügyelt identitást használjon az alkalmazás konfigurálásához való csatlakozáskor.
> * Ha szeretné, úgy konfigurálhatja az alkalmazást, hogy felügyelt identitást használjon, amikor egy alkalmazás-konfigurációs Key Vault-hivatkozáson keresztül csatlakozik Key Vaulthoz.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* [.Net Core SDK](https://www.microsoft.com/net/download/windows).
* [Azure Cloud Shell konfigurálva](../cloud-shell/quickstart.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Felügyelt identitás hozzáadása

Ha felügyelt identitást szeretne beállítani a portálon, először hozzon létre egy alkalmazást, majd engedélyezze a szolgáltatást.

1. A szokásos módon hozzon létre egy App Services példányt a [Azure Portalban](https://portal.azure.com) . Nyissa meg a portálon.

1. Görgessen le a **Beállítások** csoportba a bal oldali ablaktáblán, és válassza az **Identity (identitás**) lehetőséget.

1. A **rendszerhez rendelt** lapon váltson az **állapot** bekapcsolva értékre, majd **válassza a** **Mentés** lehetőséget.

1. Válasz **Igen** , ha a rendszer kéri, hogy engedélyezze a rendszerhez rendelt felügyelt identitást.

    ![Felügyelt identitás beállítása App Serviceban](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Hozzáférés biztosítása az alkalmazás konfigurációjához

1. A [Azure Portal](https://portal.azure.com)válassza a **minden erőforrás** lehetőséget, majd válassza ki a gyors útmutatóban létrehozott alkalmazás-konfigurációs tárolót.

1. Válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget.

1. A **hozzáférés engedélyezése** lapon válassza a **Hozzáadás** lehetőséget a **szerepkör-hozzárendelési kártya hozzáadása** felhasználói felületen.

1. A **szerepkör** területen válassza **az alkalmazás-konfigurációs Adatolvasó** lehetőséget. **A hozzáférés hozzárendelése lehetőségnél** válassza a **app Service** a **rendszerhez rendelt felügyelt identitás** területen.

1. Az **előfizetés** területen válassza ki az Azure-előfizetését. Válassza ki az alkalmazás App Service erőforrását.

1. Kattintson a **Mentés** gombra.

    ![Felügyelt identitás hozzáadása](./media/add-managed-identity.png)

1. Nem kötelező: Ha hozzáférést szeretne biztosítani Key Vaulthoz is, kövesse az [Key Vault hozzáférési szabályzat hozzárendelése](../key-vault/general/assign-access-policy-portal.md)című témakör utasításait.

## <a name="use-a-managed-identity"></a>Felügyelt identitás használata

1. Adjon hozzá egy hivatkozást az *Azure. Identity* csomaghoz:

    ```bash
    dotnet add package Azure.Identity
    ```

1. Keresse meg az alkalmazás konfigurációs tárolójához tartozó végpontot. Ez az URL-cím a Azure Portal tárolójának **hozzáférési kulcsok** lapján jelenik meg.

1. Nyissa meg *appsettings.jsa on*, és adja hozzá a következő szkriptet. Cserélje le *\<service_endpoint>* , beleértve a zárójeleket is, az alkalmazás konfigurációs tárolójának URL-címével.

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. Nyissa meg a *program. cs programot*, és adjon hozzá egy hivatkozást a `Azure.Identity` és a `Microsoft.Azure.Services.AppAuthentication` névterekhez:

    ```csharp-interactive
    using Azure.Identity;
    ```

1. Ha csak az alkalmazás konfigurációjában tárolt értékeket kívánja elérni, frissítse a `CreateWebHostBuilder` metódust úgy, hogy lecseréli a `config.AddAzureAppConfiguration()` metódust (ez a `Microsoft.Azure.AppConfiguration.AspNetCore` csomagban található).

    > [!IMPORTANT]
    > `CreateHostBuilder``CreateWebHostBuilder`a .net Core 3,0-es verzióra vált.  Válassza ki a megfelelő szintaxist a környezet alapján.

    ### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

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

    ### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

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
    > Ha **felhasználó által hozzárendelt felügyelt identitást** szeretne használni, ügyeljen arra, hogy a ClientId a [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential)létrehozásakor határozza meg.
    >```
    >config.AddAzureAppConfiguration(options =>
    >   options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential(<your_clientId>)));
    >```
    >Ahogy az Azure- [erőforrások felügyelt identitások szolgáltatásával kapcsolatos gyakori kérdések](../active-directory/managed-identities-azure-resources/managed-identities-faq.md#what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request), az alapértelmezett módszer a felügyelt identitás használatának feloldása. Ebben az esetben az Azure Identity Library arra kényszeríti, hogy adja meg a kívánt identitást, hogy elkerülje a bokréták futásidejű problémáit (például ha új felhasználó által hozzárendelt felügyelt identitást ad hozzá, vagy ha a rendszer által hozzárendelt felügyelt identitás engedélyezve van). Ezért akkor is meg kell adnia a clientId, ha csak egy felhasználó által hozzárendelt felügyelt identitás van definiálva, és nincs rendszerhez rendelt felügyelt identitás.


1. Ha az alkalmazás konfigurációs értékeit és a Key Vault hivatkozásokat is szeretné használni, frissítse a *program. cs* frissítést az alábbi ábrán látható módon. Ez a kód a (z `SetCredential` ) részeként meghívja `ConfigureKeyVault` a konfigurációs szolgáltatót, hogy milyen hitelesítő adatokat használjon a Key Vault hitelesítéshez.

    ### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

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

    ### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

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

    Mostantól ugyanúgy érheti el Key Vault hivatkozásokat, mint bármely más alkalmazás-konfigurációs kulcshoz. A konfigurációs szolgáltató a használatával `ManagedIdentityCredential` hitelesíti Key Vault és beolvassa az értéket.

    > [!NOTE]
    > A `ManagedIdentityCredential` csak a felügyelt identitások hitelesítését támogató szolgáltatások Azure-környezetekben működik. Nem működik a helyi környezetben. A [`DefaultAzureCredential`](/dotnet/api/azure.identity.defaultazurecredential) kód használata a helyi és az Azure-környezetekben egyaránt működik, mivel a szolgáltatás néhány hitelesítési lehetőségre visszaesik, beleértve a felügyelt identitást is.
    > 
    > Ha az Azure-ban üzembe helyezett **asigned felügyelt identitást** szeretne használni `DefaultAzureCredential` , akkor [a clientId kell megadnia](/dotnet/api/overview/azure/identity-readme#specifying-a-user-assigned-managed-identity-with-the-defaultazurecredential).

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="deploy-from-local-git"></a>Üzembe helyezés a helyi Gitből

A kudu-Build kiszolgálóval a legegyszerűbben engedélyezhető a helyi git üzembe helyezése az alkalmazáshoz a [Azure Cloud Shell](https://shell.azure.com)használatával.

### <a name="configure-a-deployment-user"></a>Üzembe helyező felhasználó konfigurálása

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Helyi git engedélyezése a kudu
Ha nem rendelkezik helyi git-adattárral az alkalmazáshoz, inicializálnia kell egyet. A helyi git-tárház inicializálásához futtassa a következő parancsokat az alkalmazás projekt könyvtárából:

```cmd
git init
git add .
git commit -m "Initial version"
```

Ha engedélyezni szeretné a helyi git-telepítést az alkalmazáshoz a kudu Build-kiszolgálóval, futtassa [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/#az-webapp-deployment-source-config-local-git) Cloud shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Ez a parancs a következő kimenethez hasonlót nyújt:

```json
{
  "url": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
}
```

### <a name="deploy-your-project"></a>A projekt üzembe helyezése

A _helyi terminál ablakban_ adjon hozzá egy távoli Azure-t a helyi git-tárházhoz. Cserélje le a _\<url>_ elemet a távoli git-távirányító URL-címére, amely a [helyi git engedélyezése a kudu](#enable-local-git-with-kudu).

```bash
git remote add azure <url>
```

A távoli Azure-mappához történő küldéssel helyezze üzembe az alkalmazást a következő paranccsal. Amikor a rendszer jelszót kér, adja meg az [üzembe helyezési felhasználó konfigurálása](#configure-a-deployment-user)területen létrehozott jelszót. Ne használja a Azure Portalba való bejelentkezéshez használt jelszót.

```bash
git push azure main
```

Előfordulhat, hogy a kimenetben futtatókörnyezet-specifikus automatizálás látható, például az MSBuild for ASP.NET, `npm install` a Node.js és `pip install` a Python.

### <a name="browse-to-the-azure-web-app"></a>Az Azure webalkalmazás megkeresése

Tallózással keresse meg a webalkalmazást böngésző használatával annak ellenőrzéséhez, hogy a tartalom telepítve van-e.

```bash
http://<app_name>.azurewebsites.net
```

## <a name="use-managed-identity-in-other-languages"></a>Felügyelt identitás használata más nyelveken

A .NET-keretrendszer és a Java Spring alkalmazás-konfigurációs szolgáltatói beépített támogatást is biztosítanak a felügyelt identitásokhoz. Az áruház URL-végpontját a teljes kapcsolati karakterlánca helyett használhatja a szolgáltatók egyikének konfigurálásakor.

Frissítheti például a gyors útmutatóban létrehozott .NET-keretrendszer konzol alkalmazást a következő beállítások megadásához a *App.config* fájlban:

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
Ebben az oktatóanyagban egy Azure által felügyelt identitást adott hozzá, amellyel egyszerűbbé válik az alkalmazások konfigurációjának elérése, és javítható a hitelesítő adatok kezelése az alkalmazásban. Ha többet szeretne megtudni az alkalmazások konfigurációjának használatáról, folytassa az Azure CLI-mintákkal.

> [!div class="nextstepaction"]
> [CLI-minták](./cli-samples.md)