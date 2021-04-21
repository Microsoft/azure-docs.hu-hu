---
title: Oktatóanyag – Azure Key Vault használata Azure-webalkalmazással a .NET-en
description: Ebben az oktatóanyagban egy Azure-webalkalmazást fog konfigurálni egy ASP.NET Core-alkalmazásban, hogy beolvassa a key vault titkos kulcsát.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 901f2b938512f842a5b4c34adbfc61f9379e5131
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772162"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-web-app-in-net"></a>Oktatóanyag: Felügyelt identitás használata egy azure Key Vault-webalkalmazáshoz való csatlakozáshoz a .NET-en

[Azure Key Vault](./overview.md) lehetővé teszi a hitelesítő adatok és egyéb titkos kulcsok fokozott biztonsággal történő tárolására. A kódnak azonban hitelesítenie kell magát Key Vault lekéréséhez. [Az Azure-erőforrások felügyelt identitása](../../active-directory/managed-identities-azure-resources/overview.md) segít megoldani ezt a problémát azáltal, hogy egy automatikusan felügyelt identitást biztosít az Azure-szolgáltatásoknak a Azure Active Directory (Azure AD) szolgáltatásban. Ezzel az identitással bármely Azure AD-hitelesítést támogató szolgáltatásban hitelesíthet, beleértve az Key Vault-t is anélkül, hogy a hitelesítő adatokat a kódban meg kell jelenítenie.

Ebben az oktatóanyagban Azure-webalkalmazást fog létrehozni és üzembe helyezni a [Azure App Service.](../../app-service/overview.md) Egy felügyelt identitással hitelesítheti Azure-webalkalmazását egy Azure-kulcstartóval egy titkos ügyféloldali kódtár használatával [Azure Key Vault .NET-hez](/dotnet/api/overview/azure/key-vault) és az [Azure CLI-hez.](/cli/azure/get-started-with-azure-cli) Ugyanezek az alapelvek vonatkoznak a választott fejlesztési nyelv, a Azure PowerShell és/vagy a Azure Portal.

Az oktatóanyagban bemutatott Azure App Service-webalkalmazásokkal és üzembe helyezéssel kapcsolatos további információkért lásd:
- [Az App Service áttekintése](../../app-service/overview.md)
- [ASP.NET Core-webalkalmazás létrehozása a Azure App Service](../../app-service/quickstart-dotnetcore.md)
- [Helyi Git-üzembe helyezés Azure App Service](../../app-service/deploy-local-git.md)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Azure-előfizetés. [Hozzon létre egyet ingyenesen.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* A [.NET Core 3.1 SDK (vagy újabb)](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* A [Git](https://www.git-scm.com/downloads) 2.28.0-s vagy újabb verziójának telepítése.
* Az [Azure CLI vagy](/cli/azure/install-azure-cli) a [Azure PowerShell.](/powershell/azure/)
* [Azure Key Vault.](./overview.md) Kulcstartót a következő használatával hozhat [létre:](quick-create-portal.md) [Azure Portal, az Azure CLI](quick-create-cli.md), vagy [Azure PowerShell.](quick-create-powershell.md)
* Egy Key Vault [titkos ként.](../secrets/about-secrets.md) A titkos szolgáltatásokat a következő parancsokkal [hozhatja](../secrets/quick-create-portal.md)Azure Portal, [a PowerShell vagy](../secrets/quick-create-powershell.md)az [Azure CLI használatával:](../secrets/quick-create-cli.md).

Ha már üzembe helyezett webalkalmazását a Azure App Service, ugorjon [a webalkalmazás](#create-and-assign-a-managed-identity) kulcstartóhoz való hozzáférésének konfigurálása és a [webalkalmazás kódszakaszának](#modify-the-app-to-access-your-key-vault) módosítása szakaszra.

## <a name="create-a-net-core-app"></a>.NET Core-alkalmazás létrehozása
Ebben a lépésben be fogja állítani a helyi .NET Core-projektet.

A gép egy terminálablakában hozzon létre egy nevű könyvtárat, és `akvwebapp` azt tegye az aktuális könyvtárként:

```bash
mkdir akvwebapp
cd akvwebapp
```

Hozzon létre egy .NET Core-alkalmazást a [dotnet new web paranccsal:](/dotnet/core/tools/dotnet-new)

```bash
dotnet new web
```

Futtassa helyileg az alkalmazást, hogy tudja, hogyan kell kinéznie az Azure-ban való üzembe helyezéskor:

```bash
dotnet run
```

Egy webböngészőben a következő webhelyen menjen az `http://localhost:5000` alkalmazáshoz: .

Az oldalon „Hello World!” szöveg jelenik meg üzenet jelenik meg az oldalon megjelenő mintaalkalmazásból.

További információ az Azure-webalkalmazások létrehozásáról: [ASP.NET Core-webalkalmazás létrehozása a Azure App Service](../../app-service/quickstart-dotnetcore.md)

## <a name="deploy-the-app-to-azure"></a>Az alkalmazás üzembe helyezése az Azure-ban

Ebben a lépésben üzembe fogja helyezni a .NET Core-alkalmazást a Azure App Service Git használatával. További információ az alkalmazások létrehozásáról és üzembe helyezéséről: [ASP.NET Core-webalkalmazás létrehozása az Azure-ban.](../../app-service/quickstart-dotnetcore.md)

### <a name="configure-the-local-git-deployment"></a>A git helyi üzemelő példányának konfigurálása

A terminálablakban a **Ctrl+C billentyűkombinációval** zárja be a webkiszolgálót.  Inicializáljon egy Git-adattárat a .NET Core-projekthez:

```bash
git init --initial-branch=main
git add .
git commit -m "first commit"
```

Az FTP és a helyi Git használatával üzembe helyezhet egy Azure-webalkalmazást egy üzembe *helyező felhasználó használatával.* Miután konfigurálta az üzembe helyezési felhasználót, azt az összes Azure-beli üzemelő példányhoz használhatja. A fiókszintű üzemelő példány felhasználóneve és jelszava eltér az Azure-előfizetés hitelesítő adataitól. 

Az üzembe helyezési felhasználó konfiguráláshoz futtassa [az az webapp deployment user set](/cli/azure/webapp/deployment/user?#az_webapp_deployment_user_set) parancsot. Válasszon olyan felhasználónevet és jelszót, amely megfelel az alábbi irányelveknek: 

- A felhasználónévnek egyedinek kell lennie az Azure-on belül. Helyi Git-leküldésekkor nem tartalmazhatja az at jel szimbólumot (@). 
- A jelszónak legalább nyolc karakter hosszúságúnak kell lennie, és tartalmaznia kell a következő három elemből kettőt: betűket, számokat és szimbólumokat. 

```azurecli-interactive
az webapp deployment user set --user-name "<username>" --password "<password>"
```

A JSON-kimenetben a jelszó a `null` következő: . Ha hibaüzenetet `'Conflict'. Details: 409` kap, módosítsa a felhasználónevet. `'Bad Request'. Details: 400` hibaüzenet esetén használjon erősebb jelszót. 

Jegyezheti fel a felhasználónevét és jelszavát, hogy a webalkalmazások üzembe helyezéséhez használhatja.

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforráscsoport egy logikai tároló, amelyben Azure-erőforrásokat helyezhet üzembe és kezelhet. Az az group create paranccsal hozzon létre egy erőforráscsoportot, amely a kulcstartót és a webalkalmazást [is tartalmazza:](/cli/azure/group?#az_group_create)

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

Hozzon [létre App Service az](../../app-service/overview-hosting-plans.md) Azure CLI az [appservice plan create parancsával.](/cli/azure/appservice/plan) Az alábbi példa létrehoz egy nevű App Service `myAppServicePlan` tarifacsomagot a `FREE` tarifacsomagban:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

A App Service létrehozásakor az Azure CLI az itt láthatóhoz hasonló információkat jelenít meg:

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

Hozzon létre [egy Azure-webalkalmazást](../../app-service/overview.md) `myAppServicePlan` a App Service tervben. 

> [!Important]
> A kulcstartókhoz hasonló módon az Azure-webalkalmazások nevének is egyedinek kell lennie. Az alábbi példákban cserélje le a `<your-webapp-name>` helyére a webalkalmazás nevét.


```azurecli-interactive
az webapp create --resource-group "myResourceGroup" --plan "myAppServicePlan" --name "<your-webapp-name>" --deployment-local-git
```

A webalkalmazás létrehozása után az Azure CLI az itt láthatóhoz hasonló kimenetet fog látni:

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

A távoli Git URL-címe a `deploymentLocalGitUrl` tulajdonságban, formátumban jelenik `https://<username>@<your-webapp-name>.scm.azurewebsites.net/<your-webapp-name>.git` meg. Mentse ezt az URL-címet. Erre később még szüksége lesz.

Most konfigurálja a webalkalmazást a ágból való üzembe `main` helyezéshez:

```azurecli-interactive
 az webapp config appsettings set -g MyResourceGroup -name "<your-webapp-name>"--settings deployment_branch=main
```

Az alábbi paranccsal ugrás az új alkalmazásra. Cserélje `<your-webapp-name>` le a helyére az alkalmazás nevét.

```bash
https://<your-webapp-name>.azurewebsites.net
```

Egy új Azure-webalkalmazás alapértelmezett weblapja lesz látható.

### <a name="deploy-your-local-app"></a>A helyi alkalmazás üzembe helyezése

A helyi terminálablakba visszatérve adjon hozzá egy távoli Azure-mappát a helyi Git-adattárhoz. A következő parancsban cserélje le a helyére a távoli Git URL-címét, `<deploymentLocalGitUrl-from-create-step>` amit [a Webalkalmazás létrehozása szakaszban mentett.](#create-a-web-app)

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

A következő paranccsal lekküldést telepíthet a távoli Azure-ba az alkalmazás üzembe helyezéséhez. Amikor a Git Hitelesítőadat-kezelő hitelesítő adatokat kér, használja a Configure the local Git deployment (Helyi Git üzembe helyezés [konfigurálása) szakaszban létrehozott hitelesítő adatokat.](#configure-the-local-git-deployment)

```bash
git push azure main
```

A parancs futtatása eltarthat néhány percig. A futtatása közben az itt láthatóhoz hasonló információkat jelenít meg:
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

Az üzembe helyezett alkalmazást a webböngészővel ússítjuk meg (vagy frissítheti):

```bash
http://<your-webapp-name>.azurewebsites.net
```

Az oldalon „Hello World!” szöveg jelenik meg üzenet, amely korábban a felkereste a következőt: `http://localhost:5000` .

További információ a webalkalmazás Git használatával való üzembe helyezéséről: [Local Git deployment to Azure App Service](../../app-service/deploy-local-git.md)
 
## <a name="configure-the-web-app-to-connect-to-key-vault"></a>A webalkalmazás konfigurálása a Key Vault

Ebben a szakaszban webes hozzáférést fog konfigurálni a Key Vault és frissíti az alkalmazáskódot, hogy lekér egy titkos kódot a Key Vault.

### <a name="create-and-assign-a-managed-identity"></a>Felügyelt identitás létrehozása és hozzárendelése

Ebben az oktatóanyagban felügyelt identitást fogunk [használni](../../active-directory/managed-identities-azure-resources/overview.md) a hitelesítéshez a Key Vault. A felügyelt identitás automatikusan kezeli az alkalmazás hitelesítő adatait.

Az Azure CLI-ban az alkalmazás identitásának létrehozásához futtassa [az az webapp-identity assign](/cli/azure/webapp/identity?#az_webapp_identity_assign) parancsot:

```azurecli-interactive
az webapp identity assign --name "<your-webapp-name>" --resource-group "myResourceGroup"
```

A parancs a következő JSON-kódrészletet adja vissza:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

Ha engedélyt ad a  webalkalmazásnak a kulcstartón a le- és listás műveletek végrehajtásához, adja át a parancsot az Azure CLI az  `principalId` [keyvault set-policy parancsának:](/cli/azure/keyvault?#az_keyvault_set_policy)

```azurecli-interactive
az keyvault set-policy --name "<your-keyvault-name>" --object-id "<principalId>" --secret-permissions get list
```

A hozzáférési szabályzatokat a következő parancsokkal is [Azure Portal](./assign-access-policy-portal.md) [vagy a PowerShell használatával:](./assign-access-policy-powershell.md).

### <a name="modify-the-app-to-access-your-key-vault"></a>Az alkalmazás módosítása a kulcstartó eléréséhez

Ebben az oktatóanyagban egy titkos [Azure Key Vault kódtárat fog](/dotnet/api/overview/azure/security.keyvault.secrets-readme) használni bemutató célokra. A tanúsítvány [ügyféloldali kódtárát](/dotnet/api/overview/azure/security.keyvault.certificates-readme)Azure Key Vault, vagy a Azure Key Vault [ügyféloldali kódtárat is használhatja.](/dotnet/api/overview/azure/security.keyvault.keys-readme)

#### <a name="install-the-packages"></a>A csomagok telepítése

A terminálablakból telepítse a titkos ügyféloldali kódtárat Azure Key Vault .NET-hez és az Azure Identity ügyféloldali kódtárcsomagjaihoz:

```console
dotnet add package Azure.Identity
dotnet add package Azure.Security.KeyVault.Secrets
```

#### <a name="update-the-code"></a>A kód frissítése

Keresse meg és nyissa meg a Startup.cs fájlt az akvwebapp projektben. 

Adja hozzá a következő sorokat a fejléchez:

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
using Azure.Core;
```

Adja hozzá a következő sorokat a hívás előtt, és frissítse az `app.UseEndpoints` URI-t úgy, hogy az `vaultUri` tükrözze a kulcstartó azonosítóját. Ez a kód  [a DefaultAzureCredential()](/dotnet/api/azure.identity.defaultazurecredential) használatával hitelesíti magát a Key Vault, amely a felügyelt identitás jogkivonatát használja a hitelesítéshez. További információ a Key Vault való hitelesítésről: [Fejlesztői útmutató.](./developers-guide.md#authenticate-to-key-vault-in-code) A kód exponenciális leépítést is használ az újratitkozáshoz arra az esetre, Key Vault le van-e Key Vault szabályozása. További információ a tranzakciós Key Vault korlátozásokkal kapcsolatban: Azure Key Vault [útmutatás.](./overview-throttling.md)

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

Frissítse a sort, `await context.Response.WriteAsync("Hello World!");` hogy az a következő sorhoz hasonlítsa:

```csharp
await context.Response.WriteAsync(secretValue);
```

Mielőtt folytatná a következő lépéssel, mentse a módosításokat.

#### <a name="redeploy-your-web-app"></a>A webalkalmazás újbóli üzembe helyezése

Most, hogy frissítette a kódot, az alábbi Git-parancsokkal újra üzembe is használhatja az Azure-ban:

```bash
git add .
git commit -m "Updated web app to access my key vault"
git push azure main
```

## <a name="go-to-your-completed-web-app"></a>Ugrás a kész webalkalmazásra

```bash
http://<your-webapp-name>.azurewebsites.net
```

Ahol korábban a "Hello World!" szöveg jelenik meg, meg kell jelenni a titkos secret értékeként.

## <a name="next-steps"></a>Következő lépések

- [A Azure Key Vault a .NET-hez telepített alkalmazásokhoz](./tutorial-net-virtual-machine.md)
- További információ az [Azure-erőforrások felügyelt identitási szolgáltatásról](../../active-directory/managed-identities-azure-resources/overview.md)
- A fejlesztői [útmutató megtekintése](./developers-guide.md)
- [Biztonságos hozzáférés egy kulcstartóhoz](./security-overview.md)