---
title: Oktatóanyag – Azure Key Vault használata egy Azure-webalkalmazással a .NET-ben
description: Ebben az oktatóanyagban egy Azure-webalkalmazást fog konfigurálni egy ASP.NET Core alkalmazásban, hogy beolvassa a titkos kulcsot a kulcstartóból.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 2960726cf687908e8e4aed9333fce490dd7ff006
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98788737"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-web-app-in-net"></a>Oktatóanyag: felügyelt identitás használata a Key Vault Azure-webalkalmazáshoz való összekapcsolásához a .NET-ben

A [Azure Key Vault](./overview.md) lehetővé teszi a hitelesítő adatok és más titkos kulcsok tárolását a fokozott biztonság érdekében. A kódnak azonban hitelesítenie kell Key Vault a lekéréséhez. [Az Azure-erőforrások felügyelt identitásai](../../active-directory/managed-identities-azure-resources/overview.md) segítenek megoldani ezt a problémát azáltal, hogy az Azure-szolgáltatások számára automatikusan felügyelt identitást biztosítanak Azure Active Directoryban (Azure ad). Ezt az identitást használhatja bármely olyan szolgáltatás hitelesítéséhez, amely támogatja az Azure AD-hitelesítést, beleértve a Key Vaultt, anélkül, hogy meg kellene adni a hitelesítő adatokat a kódban.

Ebben az oktatóanyagban létrehozhatja és üzembe helyezheti az Azure-webalkalmazást [Azure app Service](../../app-service/overview.md). Felügyelt identitás használatával hitelesítheti az Azure-webalkalmazást az Azure Key vaulttal, [Azure Key Vault titkos .net-ügyfél kódtára](/dotnet/api/overview/azure/key-vault) és az [Azure CLI](/cli/azure/get-started-with-azure-cli)használatával. Ugyanezek az alapelveket akkor kell alkalmazni, ha az Ön által választott fejlesztési nyelvet használja, Azure PowerShell és/vagy a Azure Portal.

Az ebben az oktatóanyagban bemutatott Azure app Service webalkalmazásokkal és telepítéssel kapcsolatos további információkért lásd:
- [Az App Service áttekintése](../../app-service/overview.md)
- [ASP.NET Core Webalkalmazás létrehozása Azure App Service](../../app-service/quickstart-dotnetcore.md)
- [Helyi git üzembe helyezése Azure App Service](../../app-service/deploy-local-git.md)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Azure-előfizetés. [Hozzon létre egyet ingyen.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* A [.net Core 3,1 SDK (vagy újabb)](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* [Git](https://www.git-scm.com/downloads) -telepítés.
* Az [Azure CLI](/cli/azure/install-azure-cli) vagy [Azure PowerShell](/powershell/azure/).
* [Azure Key Vault.](./overview.md) Kulcstartót a [Azure Portal](quick-create-portal.md), az [Azure CLI](quick-create-cli.md)vagy a [Azure PowerShell](quick-create-powershell.md)használatával hozhat létre.
* Key Vault [titok](../secrets/about-secrets.md). A titkos kulcsot a [Azure Portal](../secrets/quick-create-portal.md), a [PowerShell](../secrets/quick-create-powershell.md)vagy az [Azure CLI](../secrets/quick-create-cli.md)használatával lehet létrehozni.

Ha már telepítette a webalkalmazást a Azure App Serviceban, ugorjon a webalkalmazás- [hozzáférés konfigurálása kulcstartóhoz](#create-and-assign-a-managed-identity) lehetőségre, és [módosítsa a webalkalmazási kód](#modify-the-app-to-access-your-key-vault) szakaszt.

## <a name="create-a-net-core-app"></a>.NET Core-alkalmazás létrehozása
Ebben a lépésben a helyi .NET Core-projektet fogja beállítani.

A számítógép egy terminál ablakában hozzon létre egy nevű könyvtárat, `akvwebapp` és tegye az aktuális könyvtárat:

```bash
mkdir akvwebapp
cd akvwebapp
```

Hozzon létre egy .NET Core-alkalmazást a [DotNet New Web](/dotnet/core/tools/dotnet-new) parancs használatával:

```bash
dotnet new web
```

Futtassa helyileg az alkalmazást, hogy tudja, hogyan kell kinéznie az Azure-ba történő üzembe helyezéskor:

```bash
dotnet run
```

A böngészőben nyissa meg az alkalmazást a következő címen: `http://localhost:5000` .

Az oldalon „Hello World!” szöveg jelenik meg az oldalon megjelenő minta alkalmazás üzenete.

További információ az Azure-hoz készült webalkalmazások létrehozásáról: [ASP.net Core Webalkalmazás létrehozása Azure app Service](../../app-service/quickstart-dotnetcore.md)

## <a name="deploy-the-app-to-azure"></a>Az alkalmazás üzembe helyezése az Azure-ban

Ebben a lépésben üzembe helyezi a .NET Core-alkalmazást Azure App Service a helyi git használatával. További információ az alkalmazások létrehozásáról és központi telepítéséről: [ASP.net Core Webalkalmazás létrehozása az Azure-ban](../../app-service/quickstart-dotnetcore.md).

### <a name="configure-the-local-git-deployment"></a>A helyi git-telepítés konfigurálása

A terminál ablakban válassza a **CTRL + C** billentyűkombinációt a webkiszolgáló bezárásához.  Git-Tárház inicializálása a .NET Core-projekthez:

```bash
git init
git add .
git commit -m "first commit"
```

Az FTP és a helyi git használatával üzembe helyezhet egy Azure-webalkalmazást egy *üzembe helyezési felhasználó* használatával. Az üzembe helyezési felhasználó konfigurálása után használhatja azt az összes Azure-beli üzemelő példányhoz. A fiók szintű központi telepítési felhasználóneve és jelszava eltér az Azure-előfizetés hitelesítő adataitól. 

Az üzembe helyezési felhasználó konfigurálásához futtassa az az [WebApp Deployment User set](/cli/azure/webapp/deployment/user?#az-webapp-deployment-user-set) parancsot. Válasszon ki egy felhasználónevet és egy jelszót, amely megfelel az alábbi irányelveknek: 

- A felhasználónévnek egyedinek kell lennie az Azure-on belül. A helyi git-leküldések esetében nem tartalmazhatja a kukac jel (@) karaktert. 
- A jelszónak legalább nyolc karakterből kell állnia, és tartalmaznia kell a következő három elem közül kettőt: betűk, számok és szimbólumok. 

```azurecli-interactive
az webapp deployment user set --user-name "<username>" --password "<password>"
```

A JSON-kimenet a jelszót jeleníti meg `null` . Ha `'Conflict'. Details: 409` hibaüzenetet kap, módosítsa a felhasználónevet. `'Bad Request'. Details: 400` hibaüzenet esetén használjon erősebb jelszót. 

Jegyezze fel a felhasználónevet és a jelszót, hogy a webalkalmazások üzembe helyezéséhez használható legyen.

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforráscsoport olyan logikai tároló, amelyben üzembe helyezheti az Azure-erőforrásokat, és kezelheti azokat. Hozzon létre egy erőforráscsoportot, amely a Key vaultot és a webalkalmazást is tartalmazza az az [Group Create](/cli/azure/group?#az-group-create) parancs használatával:

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

Hozzon létre egy [app Service tervet](../../app-service/overview-hosting-plans.md) az Azure CLI az [appservice Plan Create](/cli/azure/appservice/plan) paranccsal. A következő példa egy nevű App Service-csomagot hoz létre `myAppServicePlan` az `FREE` árképzési szinten:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

A App Service terv létrehozásakor az Azure CLI az itt láthatóhoz hasonló információkat jelenít meg:

<pre>
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
</pre>

További információ: [App Service-csomag kezelése az Azure-ban](../../app-service/app-service-plan-manage.md).

### <a name="create-a-web-app"></a>Webalkalmazás létrehozása

Hozzon létre egy [Azure-webalkalmazást](../../app-service/overview.md) a `myAppServicePlan` app Service tervben. 

> [!Important]
> A Key vaulthoz hasonlóan az Azure-webalkalmazásoknak is egyedi névvel kell rendelkezniük. Cserélje le a `<your-webapp-name>` nevet a webalkalmazás nevére a következő példákban.


```azurecli-interactive
az webapp create --resource-group "myResourceGroup" --plan "myAppServicePlan" --name "<your-webapp-name>" --deployment-local-git
```

A webalkalmazás létrehozása után az Azure CLI az itt láthatóhoz hasonló kimenetet jelenít meg:

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;ayour-webapp-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "clientCertExclusionPaths": null,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;your-webapp-name&gt;.azurewebsites.net",
  "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;your-webapp-name&gt;.git",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>


A git-távirányító URL-címe a `deploymentLocalGitUrl` tulajdonságban, a formátumban jelenik meg `https://<username>@<your-webapp-name>.scm.azurewebsites.net/<your-webapp-name>.git` . Mentse ezt az URL-címet. Erre később még szüksége lesz.

Nyissa meg az új alkalmazást az alábbi parancs használatával. Cserélje le `<your-webapp-name>` az nevet az alkalmazás nevére.

```bash
https://<your-webapp-name>.azurewebsites.net
```

Ekkor megjelenik az új Azure-webalkalmazás alapértelmezett weboldala.

### <a name="deploy-your-local-app"></a>A helyi alkalmazás üzembe helyezése

A helyi terminálablakba visszatérve adjon hozzá egy távoli Azure-mappát a helyi Git-adattárhoz. A következő parancsban cserélje le a parancsot a `<deploymentLocalGitUrl-from-create-step>` [Webalkalmazás létrehozása](#create-a-web-app) szakaszba mentett git-távirányító URL-címére.

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Az alábbi parancs használatával továbbíthatja az Azure távoli eszközét az alkalmazás üzembe helyezéséhez. Ha a git Hitelesítőadat-kezelő kéri a hitelesítő adatok megadását, használja a [helyi git-telepítés konfigurálása](#configure-the-local-git-deployment) szakaszban létrehozott hitelesítő adatokat.

```bash
git push azure main
```

A parancs futtatása eltarthat néhány percig. A futtatása közben a következőhöz hasonló információkat jelenít meg:
<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 285 bytes | 95.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Deploy Async
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'd6b54472f7'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote:
remote: Oryx Version      : 0.2.20200114.13, Commit: 204922f30f8e8d41f5241b8c218425ef89106d1d, ReleaseTagName: 20200114.13
remote: Build Operation ID: |imoMY2y77/s=.40ca2a87_
remote: Repository Commit : d6b54472f7e8e9fd885ffafaa64522e74cf370e1
.
.
.
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;your-webapp-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/d6b54472f7e8e9fd885ffafaa64522e74cf370e1/log'
To https://&lt;your-webapp-name&gt;.scm.azurewebsites.net:443/&lt;your-webapp-name&gt;.git
   d87e6ca..d6b5447  main -> main
</pre>

Nyissa meg a központilag telepített alkalmazást a böngészőben:

```bash
http://<your-webapp-name>.azurewebsites.net
```

Az oldalon „Hello World!” szöveg jelenik meg a korábban meglátogatott üzenet `http://localhost:5000` .

További információ a webalkalmazások git használatával történő üzembe helyezéséről: [helyi git üzembe helyezése Azure app Service](../../app-service/deploy-local-git.md)
 
## <a name="configure-the-web-app-to-connect-to-key-vault"></a>A webalkalmazás konfigurálása Key Vaulthoz való kapcsolódáshoz

Ebben a szakaszban a webes elérést konfigurálja úgy, hogy Key Vault és frissítse az alkalmazás kódját egy titkos kód lekéréséhez Key Vaultból.

### <a name="create-and-assign-a-managed-identity"></a>Felügyelt identitás létrehozása és társítása

Ebben az oktatóanyagban a [felügyelt identitást](../../active-directory/managed-identities-azure-resources/overview.md) használjuk a Key Vault való hitelesítéshez. A felügyelt identitás automatikusan kezeli az alkalmazás hitelesítő adatait.

Az Azure CLI-ben az alkalmazás identitásának létrehozásához futtassa az az [WebApp-Identity assign](/cli/azure/webapp/identity?#az-webapp-identity-assign) parancsot:

```azurecli-interactive
az webapp identity assign --name "<your-webapp-name>" --resource-group "myResourceGroup"
```

A parancs a következő JSON-kódrészletet fogja visszaadni:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

Ahhoz, hogy a webalkalmazás engedélyt kapjon a Key Vault **beolvasási** és **listázási** műveleteire, adja át az eszközt `principalId` Az Azure CLI az kulcstartó [set-Policy](/cli/azure/keyvault?#az-keyvault-set-policy) paranccsal:

```azurecli-interactive
az keyvault set-policy --name "<your-keyvault-name>" --object-id "<principalId>" --secret-permissions get list
```

Hozzáférési szabályzatokat a [Azure Portal](./assign-access-policy-portal.md) vagy a [PowerShell](./assign-access-policy-powershell.md)használatával is hozzárendelhet.

### <a name="modify-the-app-to-access-your-key-vault"></a>Az alkalmazás módosítása a Key Vault eléréséhez

Ebben az oktatóanyagban a [Azure Key Vault Secret ügyféloldali függvénytárat](/dotnet/api/overview/azure/security.keyvault.secrets-readme) fogja bemutató célokra használni. [Azure Key Vault tanúsítvány ügyféloldali függvénytárát](/dotnet/api/overview/azure/security.keyvault.certificates-readme)vagy [Azure Key Vault kulcs ügyféloldali függvénytárát](/dotnet/api/overview/azure/security.keyvault.keys-readme)is használhatja.

#### <a name="install-the-packages"></a>A csomagok telepítése

A terminál ablakban telepítse a .NET-hez készült Azure Key Vault titkos ügyféloldali kódtárat és az Azure Identity ügyféloldali függvénytár-csomagokat:

```console
dotnet add package Azure.Identity
dotnet add package Azure.Security.KeyVault.Secrets
```

#### <a name="update-the-code"></a>A kód frissítése

Keresse meg és nyissa meg a Startup. cs fájlt a akvwebapp-projektben. 

Adja hozzá a következő sorokat a fejléchez:

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
using Azure.Core;
```

Adja hozzá a következő sorokat a `app.UseEndpoints` hívás előtt, és frissítse az URI-t, hogy az tükrözze a `vaultUri` kulcstartót. Ez a kód a  [DefaultAzureCredential ()](/dotnet/api/azure.identity.defaultazurecredential) használatával hitelesíti a Key Vault, amely a felügyelt identitásból származó jogkivonatot használ a hitelesítéshez. További információ a Key Vault hitelesítéséről: [fejlesztői útmutató](./developers-guide.md#authenticate-to-key-vault-in-code). A kód exponenciális leállítási is használ az újrapróbálkozásokhoz abban az esetben, Key Vault a szabályozása folyamatban van. A Key Vault tranzakciós korlátokkal kapcsolatos további információkért lásd: [Azure Key Vault szabályozási útmutató](./overview-throttling.md).

```csharp
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
var client = new SecretClient(new Uri("https://<your-unique-key-vault-name>.vault.azure.net/"), new DefaultAzureCredential(),options);

KeyVaultSecret secret = client.GetSecret("<mySecret>");

string secretValue = secret.Value;
```

A sor frissítése a `await context.Response.WriteAsync("Hello World!");` következő sorhoz hasonlóan:

```csharp
await context.Response.WriteAsync(secretValue);
```

Mielőtt továbblépne a következő lépésre, mentse a módosításokat.

#### <a name="redeploy-your-web-app"></a>A webalkalmazás újbóli üzembe helyezése

Most, hogy frissítette a kódot, újra üzembe helyezheti az Azure-ban a git-parancsok használatával:

```bash
git add .
git commit -m "Updated web app to access my key vault"
git push azure main
```

## <a name="go-to-your-completed-web-app"></a>Ugrás a befejezett webalkalmazásra

```bash
http://<your-webapp-name>.azurewebsites.net
```

A "Hello World!" előtt ekkor megjelenik a titkos kulcs értéke.

## <a name="next-steps"></a>Következő lépések

- [Azure Key Vault használata a .NET-alapú virtuális gépekre telepített alkalmazásokkal](./tutorial-net-virtual-machine.md)
- További információ az [Azure-erőforrások felügyelt identitásáról](../../active-directory/managed-identities-azure-resources/overview.md)
- A [fejlesztői útmutató](./developers-guide.md) megtekintése
- [Biztonságos hozzáférés egy kulcstartóhoz](./secure-your-key-vault.md)