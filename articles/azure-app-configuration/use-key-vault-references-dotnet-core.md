---
title: Oktatóanyag a Azure App Configuration Key Vault hivatkozások ASP.NET Core-alkalmazásalkalmazásban való | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan használhatja Azure App Configuration Core Key Vault alkalmazások ASP.NET hivatkozását
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/08/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 57abbeefe8e3f2abe527f2b282d643db766b9dc9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775744"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>Oktatóanyag: Key Vault hivatkozások használata ASP.NET Core-alkalmazásokban

Ez az oktatóanyag bemutatja, hogyan használhatja az Azure App Configuration szolgáltatást a Azure Key Vault. App Configuration és Key Vault kiegészítő szolgáltatások, amelyek a legtöbb alkalmazástelepítésben egymás mellett vannak használva.

App Configuration segítségével együtt használhatja a szolgáltatásokat a szolgáltatásokban tárolt értékekre hivatkozó kulcsok létrehozásával Key Vault. Amikor App Configuration ilyen kulcsokat hoz létre, a kulcs URI-ját Key Vault tárolja, nem magukat az értékeket.

Az alkalmazás a App Configuration ügyfélszolgáltató használatával lekéri a Key Vault-hivatkozásokat, ahogyan a App Configuration. Ebben az esetben a tárolóban tárolt App Configuration olyan URI-k, amelyek a Key Vault. Ezek nem Key Vault hitelesítő adatok. Mivel az ügyfélszolgáltató hivatkozásként ismeri fel Key Vault kulcsokat, Key Vault az értékeiket.

Az alkalmazás felelős a megfelelő hitelesítésért a App Configuration és Key Vault. A két szolgáltatás nem kommunikál közvetlenül.

Ez az oktatóanyag bemutatja, hogyan valósíthat meg Key Vault-hivatkozásokat a kódban. A rövid útmutatókban bemutatott webalkalmazásra épül. A folytatás előtt a [Create an ASP.NET Core app with App Configuration](./quickstart-aspnet-core-app.md) (App Configuration Core-alkalmazás létrehozása) lépéssel.

Az oktatóanyagban található lépéseket bármilyen kódszerkesztővel el is használhatja. A [Visual Studio Code](https://code.visualstudio.com/) például egy platformfüggetlen kódszerkesztő, amely Windows, macOS és Linux operációs rendszerekhez érhető el.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy App Configuration kulcsot, amely a tárolóban tárolt Key Vault.
> * A kulcs értékét egy ASP.NET Core-webalkalmazásból érik el.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené ezt az oktatóanyagot, telepítse a [.NET Core SDK.](https://dotnet.microsoft.com/download)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Tároló létrehozása

1. Válassza **az Erőforrás létrehozása** lehetőséget a képernyő bal felső sarkában Azure Portal:

    ![Képernyőkép az Erőforrás létrehozása lehetőségről a Azure Portal.](./media/quickstarts/search-services.png)
1. A keresőmezőbe írja be a **Key Vault** nevet.
1. Az eredmények listájában válassza a **bal oldalon található Kulcstartók** lehetőséget.
1. A **Key Vaultsban válassza** a Hozzáadás **lehetőséget.**
1. A Kulcstartó létrehozása jobb **oldalon** adja meg a következő információkat:
    - Válassza **az Előfizetés** lehetőséget az előfizetés kiválasztásához.
    - Az **Erőforráscsoport mezőben** válassza az **Új létrehozása lehetőséget,** és adjon meg egy erőforráscsoport-nevet.
    - A **Key Vault neve alatt** egy egyedi névre van szükség. Ebben az oktatóanyagban adja meg a **Contoso-vault2 tárolót.**
    - A Régió **legördülő** listában válasszon egy helyet.
1. Hagyja meg a **Többi Kulcstartó létrehozása beállítást** az alapértelmezett értékekkel.
1. Válassza a **Létrehozás** lehetőséget.

Ezen a ponton az Ön Azure-fiókja az egyetlen, aki jogosult hozzáférni ehhez az új tárolóhoz.

![Képernyőkép a kulcstartóról.](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Titkos kulcs hozzáadása a Key Vaulthoz

Ha titkos kulcsokat szeretne hozzáadni a tárolóhoz, néhány további lépést is végre kell vennie. Ebben az esetben adjon hozzá egy üzenetet, amely a lekérés tesztelésére Key Vault használható. Az üzenet neve **Message**, és a "Hello from Key Vault" értéket tárolja benne.

1. A tulajdonságokat Key Vault oldalon válassza a Titkos **kulcsok lehetőséget.**
1. Válassza **a Generate/Import (Generálás/Importálás) lehetőséget.**
1. A Titkos **adat létrehozása panelen** adja meg a következő értékeket:
    - **Feltöltési beállítások:** Adja meg a **Manuális lehetőséget.**
    - **Név:** Írja be a **következő üzenetet:**.
    - **Érték:** Írja be a **Hello Key Vault.**
1. A többi **Titkos adat létrehozása tulajdonságot** hagyja meg az alapértelmezett értékekkel.
1. Válassza a **Létrehozás** lehetőséget.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Hivatkozás Key Vault a App Configuration

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza **a Minden erőforrás** lehetőséget, majd App Configuration a rövid útmutatóban létrehozott tárolópéldányt.

1. Válassza a **Configuration Explorer lehetőséget.**

1. Válassza **a +**  >  **Kulcstartó-referencia létrehozása lehetőséget,** majd adja meg a következő értékeket:
    - **Kulcs:** Válassza a **TestApp:Settings:KeyVaultMessage lehetőséget.**
    - **Címke:** Ezt az értéket hagyja üresen.
    - **Előfizetés,** **Erőforráscsoport** és **Kulcstartó:** Adja meg az előző szakaszban létrehozott kulcstartóban található értékeknek megfelelő értékeket.
    - **Titkos:** Válassza ki az előző szakaszban létrehozott **Message** (Üzenet) nevű titkos secret et.

## <a name="connect-to-key-vault"></a>Csatlakozás Key Vault

1. Ebben az oktatóanyagban egy szolgáltatásnévvel fog hitelesítést használni a Key Vault. A szolgáltatásnév létrehozásához használja az Azure CLI [az ad sp create-for-rbac parancsot:](/cli/azure/ad/sp#az_ad_sp_create_for_rbac)

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Ez a művelet kulcs/érték párok sorozatát adja vissza:

    ```console
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. Futtassa a következő parancsot, hogy a szolgáltatásnév hozzáfér a kulcstartóhoz:

    ```cmd
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
    ```

1. Környezeti változók hozzáadása a *clientId,* *clientSecret* és *tenantId értékek tárolására.*

    #### <a name="windows-command-prompt"></a>[Windows-parancssor](#tab/cmd)

    ```cmd
    setx AZURE_CLIENT_ID <clientId-of-your-service-principal>
    setx AZURE_CLIENT_SECRET <clientSecret-of-your-service-principal>
    setx AZURE_TENANT_ID <tenantId-of-your-service-principal>
    ```

    #### <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```PowerShell
    $Env:AZURE_CLIENT_ID = <clientId-of-your-service-principal>
    $Env:AZURE_CLIENT_SECRET = <clientSecret-of-your-service-principal>
    $Env:AZURE_TENANT_ID = <tenantId-of-your-service-principal>
    ```

    #### <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    export AZURE_CLIENT_ID = <clientId-of-your-service-principal>
    export AZURE_CLIENT_SECRET = <clientSecret-of-your-service-principal>
    export AZURE_TENANT_ID = <tenantId-of-your-service-principal>
    ```

    ---

    > [!NOTE]
    > Ezek Key Vault hitelesítő adatokat csak az alkalmazáson belül használja. Az alkalmazás közvetlenül hitelesíti magát Key Vault hitelesítő adatokkal. A szolgáltatásnak soha nem App Configuration át.

1. Indítsa újra a terminált az új környezeti változók betöltéséhez.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>A kód frissítése egy Key Vault használatára

1. Adjon hozzá egy hivatkozást a szükséges NuGet-csomagokhoz a következő parancs futtatásával:

    ```dotnetcli
    dotnet add package Azure.Identity
    ```

1. Nyissa *meg a Program.cs programot,* és adjon hozzá hivatkozásokat a következő szükséges csomagokhoz:

    ```csharp
    using Azure.Identity;
    ```

1. Frissítse `CreateWebHostBuilder` a metódust, hogy App Configuration metódust hívja `config.AddAzureAppConfiguration` meg. Adja meg a beállítást, és adja meg a megfelelő `ConfigureKeyVault` hitelesítő adatokat a Key Vault.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>();
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

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
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>());
    ```

1. Amikor inicializálta a kapcsolatot a App Configuration, a metódus hívásával Key Vault a `ConfigureKeyVault` kapcsolatot. Az inicializálást követően a hivatkozások értékei ugyanúgy Key Vault, mint a hagyományos kulcsok App Configuration.

    A folyamat művelet közbeni megnyitásához nyissa meg az *Index.cshtml fájlt* a Nézetek   >  **kezdőlapmappában.** Cserélje le a tartalmát a következő kódra:

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"]px;
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]
        and @Configuration["TestApp:Settings:KeyVaultMessage"]</h1>
    ```

    A **TestApp:Settings:KeyVaultMessage** referencia Key Vault ugyanúgy férhet hozzá, mint a **TestApp:Settings:Message konfigurációs értékéhez.**

## <a name="build-and-run-the-app-locally"></a>Az alkalmazás helyi összeállítása és futtatása

1. Az alkalmazás parancssori paranccsal való a .NET Core parancssori felülete futtassa a következő parancsot a parancshéjban:

    ```dotnetcli
    dotnet build
    ```

1. A build elkészülte után a következő paranccsal futtassa helyileg a webalkalmazást:

    ```dotnetcli
    dotnet run
    ```

1. Nyisson meg egy böngészőablakot, és nyissa meg a címet, amely a helyileg üzemeltetett `http://localhost:5000` webalkalmazás alapértelmezett URL-címe.

    ![Rövid útmutató: Helyi alkalmazás indítása](./media/key-vault-reference-launch-local.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy App Configuration kulcsot, amely egy, a Key Vault. A következő oktatóanyagból megtudhatja, hogyan adhat hozzá egy Azure által felügyeltszolgáltatás-identitást, amely leegyszerűsíti a App Configuration és Key Vault hozzáférését.

> [!div class="nextstepaction"]
> [Felügyelt identitás integrációja](./howto-integrate-azure-managed-service-identity.md)
