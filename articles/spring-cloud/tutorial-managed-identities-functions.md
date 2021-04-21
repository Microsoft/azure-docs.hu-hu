---
title: 'Oktatóanyag: Felügyelt identitás a Azure Functions'
description: Felügyelt identitás használata az Azure Functions meghívásához egy Azure Spring Cloud-alkalmazásból
author: MarkGardner
ms.author: margard
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/10/2020
ms.openlocfilehash: b737ea751d3b3d2132691e04a1a2cd853748db65
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792506"
---
# <a name="tutorial-use-a-managed-identity-to-invoke-azure-functions-from-an-azure-spring-cloud-app"></a>Oktatóanyag: Felügyelt identitás használata Azure Functions meghívásához egy Azure Spring Cloud alkalmazásból

Ez a cikk bemutatja, hogyan hozhat létre felügyelt identitást egy Azure Spring Cloud-alkalmazáshoz, és hogyan hívhatja meg vele a HTTP által aktivált függvényeket.

A Azure Functions és App Services is beépített támogatást nyújt a Azure Active Directory (Azure AD) hitelesítéshez. A beépített hitelesítési képesség, valamint a felügyelt identitások Azure Spring Cloud kihasználva a RESTful-szolgáltatásokat modern OAuth-szemantikával hívhatja meg. Ez a módszer nem igényel titkos kódok kódban való tárolását, és részletesebb vezérlést biztosít a külső erőforrásokhoz való hozzáférés szabályozására. 


## <a name="prerequisites"></a>Előfeltételek

* [Regisztráció Azure-előfizetésre](https://azure.microsoft.com/free/)
* [Az Azure CLI 2.0.67-es vagy újabb verziójának telepítése](/cli/azure/install-azure-cli)
* [A Maven 3.0-s vagy azt feletti telepítése](https://maven.apache.org/download.cgi)
* [Telepítse a Azure Functions Core Tools 3.0.2009-es vagy újabb verzióját](../azure-functions/functions-run-local.md#install-the-azure-functions-core-tools)


## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozzon létre egy erőforráscsoportot a függvényalkalmazás és a Spring Cloud [az az group create paranccsal:](/cli/azure/group#az_group_create)

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```


## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása
Függvényalkalmazás létrehozásához először létre kell hoznia egy háttértárfiókot az [az storage account create paranccsal:](/cli/azure/storage/account#az_storage_account_create)

> [!Important]
> Minden függvényalkalmazásnak és tárfióknak egyedi névvel kell lennie. Cserélje <a your-functionapp-name> helyére a függvényalkalmazás nevét, az <your-storageaccount-name> helyére pedig a tárfiók nevét a következő példákban.

```azurecli-interactive
az storage account create --name <your-storageaccount-name> --resource-group myResourceGroup --location eastus --sku Standard_LRS
```

A Tárfiók létrehozása után létrehozhatja a függvényalkalmazást.

```azurecli-interactive
az functionapp create --name <your-functionapp-name> --resource-group myResourceGroup --consumption-plan-location eastus --os-type windows --runtime node --storage-account <your-storageaccount-name> --functions-version 3
```

Jegyezze fel a visszaadott **hostNames** nevet, amelynek formátuma "https://<your-functionapp-name>.azurewebsites.net". Ezt a következő lépésben fogjuk használni.


## <a name="enable-azure-active-directory-authentication"></a>Az Azure Active Directory hitelesítés engedélyezése

Az újonnan létrehozott függvényalkalmazást a Azure Portal válassza a "Hitelesítés /Engedélyezés" lehetőséget a beállítások menüben. [](https://portal.azure.com) Engedélyezze App Service hitelesítést, és állítsa a "Kérés nem hitelesítve" beállításhoz a "Bejelentkezés az Azure Active Directory" beállításra. Ez a beállítás biztosítja, hogy a rendszer minden nem hitelesített kérést elutasít (401-es válasz).

![A hitelesítési beállítások Azure Active Directory alapértelmezett szolgáltatóként való használatát mutatják](media/spring-cloud-tutorial-managed-identities-functions/function-auth-config-1.jpg)

A Hitelesítésszolgáltatók alatt válassza a Azure Active Directory lehetőséget az alkalmazásregisztráció konfigurálásához. Az Express Management Mód kiválasztásakor a rendszer automatikusan létrehoz egy alkalmazásregisztrációt az Azure AD-bérlőben a megfelelő konfigurációval.

![Azure Active Directory Express Management Mode (Expressz felügyeleti mód) beállítású szolgáltató](media/spring-cloud-tutorial-managed-identities-functions/function-auth-config-2.jpg)

A beállítások mentése után a függvényalkalmazás újraindul, és a rendszer minden további kérést kérni fog, hogy jelentkezzen be az Azure AD-n keresztül. A nem hitelesített kérelmek elutasításának teszteléséhez lépjen a függvényalkalmazások gyökér URL-címére (ezt a fenti lépés **hostNames** kimenete tartalmazza). A rendszer átirányítja a szervezet Azure AD bejelentkezési képernyőjére.


## <a name="create-an-http-triggered-function"></a>HTTP által aktivált függvény létrehozása

Egy üres helyi könyvtárban hozzon létre egy új függvényalkalmazást, és adjon hozzá egy HTTP által aktivált függvényt.

```console
func init --worker-runtime node
func new --template HttpTrigger --name HttpTrigger
```

Alapértelmezés szerint a Functions kulcsalapú hitelesítéssel biztosítja a HTTP-végpontok biztonságát. Mivel engedélyezni fogjuk az Azure AD-hitelesítést a Functions-hez való hozzáférés biztonságossá tételéhez, a függvény hitelesítési szintjét névtelenre szeretnénk [állítani.](../azure-functions/functions-bindings-http-webhook-trigger.md#secure-an-http-endpoint-in-production)

```json function.json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      ...
    }
  ]
}
```

Az alkalmazást most már közzé lehet tenni az [előző](#create-a-function-app) lépésben létrehozott függvényalkalmazás-példányban.

```console
func azure functionapp publish <your-functionapp-name>
```

A közzétételi parancs kimenetének meg kell adni az újonnan létrehozott függvény URL-címét.

```output
Deployment completed successfully.
Syncing triggers...
Functions in <your-functionapp-name>:
    HttpTrigger - [httpTrigger]
        Invoke url: https://<your-functionapp-name>.azurewebsites.net/api/httptrigger
```


## <a name="create-azure-spring-cloud-service-and-app"></a>Új Azure Spring Cloud és alkalmazás létrehozása
A spring-cloud bővítmény telepítése után hozzon létre egy Azure Spring Cloud-példányt az Azure CLI-paranccsal. `az spring-cloud create` 

```azurecli-interactive
az extension add --name spring-cloud
az spring-cloud create --name mymsispringcloud --resource-group myResourceGroup --location eastus
```

Az alábbi példa egy nevű alkalmazást hoz létre egy rendszer által hozzárendelt felügyelt `msiapp` identitással, a paraméter által `--assign-identity` kértek szerint.

```azurecli
az spring-cloud app create --name "msiapp" --service "mymsispringcloud" --resource-group "myResourceGroup" --assign-endpoint true --assign-identity
```

## <a name="build-sample-spring-boot-app-to-invoke-the-function"></a>Mintaalkalmazás Spring Boot a függvény meghívásához

Ez a minta úgy hívja meg a HTTP által [](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md#get-a-token-using-http) aktivált függvényt, hogy először egy hozzáférési jogkivonatot kér az MSI-végpontról, és ezzel a jogkivonattal hitelesíti a függvény HTTP-kérését.

1. Klónozza a mintaprojektet. 

    ```console
    git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
    ```

2. Adja meg a függvény URI-ját és az eseményindító nevét az alkalmazás tulajdonságaiban. 

    ```bash
    cd Azure-Spring-Cloud-Samples/managed-identity-function
    vim src/main/resources/application.properties
    ```

    Ha felügyelt identitást szeretne használni Azure Spring Cloud alkalmazásokhoz, adja hozzá a következő tartalommal a tulajdonságokat az *src/main/resources/application.properties fájlhoz.*

    ```
    azure.function.uri=https://<your-functionapp-name>.azurewebsites.net
    azure.function.triggerPath=httptrigger
    ```

3. Csomagolja be a mintaalkalmazást. 

    ```console
    mvn clean package
    ```

4. Most telepítse az alkalmazást az Azure-ban az Azure CLI-paranccsal. `az spring-cloud app deploy` 

    ```azurecli
    az spring-cloud app deploy  --name "msiapp" --service "mymsispringcloud" --resource-group "myResourceGroup" --jar-path target/sc-managed-identity-function-sample-0.1.0.jar
    ```

5. Az alkalmazás teszteléséhez használja a nyilvános végpontot vagy a tesztvégpontot. 

    ```console
    curl https://mymsispringcloud-msiapp.azuremicroservices.io/func/springcloud
    ```

    A válasz törzsében a következő üzenet jelenik meg.
    ```output
    Function Response: Hello, springcloud. This HTTP triggered function executed successfully.
    ```
    
    A path paraméter módosításával megpróbálhat különböző értékeket átadva a függvénynek.

## <a name="next-steps"></a>Következő lépések

* [Rendszer által hozzárendelt felügyelt identitás engedélyezése Azure Spring Cloud alkalmazáshoz](./spring-cloud-howto-enable-system-assigned-managed-identity.md)
* [További információ az Azure-erőforrások felügyelt identitási szolgáltatásról](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Ügyfélalkalmazások konfigurálása a App Service](../app-service/configure-authentication-provider-aad.md#configure-client-apps-to-access-your-app-service)
