---
title: 'Oktatóanyag: a felügyelt identitás meghívása Azure Functions'
description: Felügyelt identitás használata az Azure Functions meghívásához egy Azure Spring Cloud-alkalmazásból
author: MarkGardner
ms.author: margard
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/10/2020
ms.openlocfilehash: 99e3611711e9a25119a4428d7736a36073163894
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104879181"
---
# <a name="tutorial-use-a-managed-identity-to-invoke-azure-functions-from-an-azure-spring-cloud-app"></a>Oktatóanyag: felügyelt identitás használata az Azure Spring Cloud app Azure Functionsának meghívásához

Ez a cikk bemutatja, hogyan hozhat létre felügyelt identitást egy Azure Spring Cloud-alkalmazáshoz, és hogyan használhatja a http által aktivált függvények meghívásához.

A Azure Functions és a App Services egyaránt támogatja az Azure Active Directory (Azure AD) hitelesítést. A beépített hitelesítési képességgel és az Azure Spring Cloud felügyelt identitásával együtt a modern OAuth szemantika használatával REST-alapú szolgáltatásokat is meghívhat. Ehhez a metódushoz nem szükséges a titkos kódok tárolása a kódban, és részletesebb szabályozást biztosít a külső erőforrásokhoz való hozzáférés szabályozásához. 


## <a name="prerequisites"></a>Előfeltételek

* [Feliratkozás Azure-előfizetésre](https://azure.microsoft.com/free/)
* [Az Azure CLI 2.0.67 vagy újabb verziójának telepítése](/cli/azure/install-azure-cli)
* [A Maven 3,0-es vagy újabb verziójának telepítése](https://maven.apache.org/download.cgi)
* [A Azure Functions Core Tools 3.0.2009 vagy újabb verziójának telepítése](../azure-functions/functions-run-local.md#install-the-azure-functions-core-tools)


## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozzon létre egy erőforráscsoportot, amely a Function alkalmazást és a Spring Cloudt is tartalmazza a parancs az [Group Create](/cli/azure/group#az-group-create)paranccsal történő használatával:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```


## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása
A Function-alkalmazás létrehozásához először létre kell hoznia egy biztonsági mentést tartalmazó Storage-fiókot az az [Storage Account Create](/cli/azure/storage/account#az-storage-account-create)paranccsal:

> [!Important]
> Minden Function app-és Storage-fióknak egyedi névvel kell rendelkeznie. A következő példákban cserélje le <a-functionapp-Name>t a Function-alkalmazás nevére, és <a storageaccount nevét> a Storage-fiók nevével.

```azurecli-interactive
az storage account create --name <your-storageaccount-name> --resource-group myResourceGroup --location eastus --sku Standard_LRS
```

A Storage-fiók létrehozása után létrehozhatja a Function alkalmazást.

```azurecli-interactive
az functionapp create --name <your-functionapp-name> --resource-group myResourceGroup --consumption-plan-location eastus --os-type windows --runtime node --storage-account <your-storageaccount-name> --functions-version 3
```

Jegyezze fel a visszaadott **gazdagépeket**, amelyek a "https://<Your-functionapp-Name>. azurewebsites.net" formátumban lesznek. Ezt a következő lépésben fogjuk használni.


## <a name="enable-azure-active-directory-authentication"></a>Azure Active Directory hitelesítés engedélyezése

Nyissa meg az újonnan létrehozott Function alkalmazást a [Azure Portal](https://portal.azure.com) , és válassza a "hitelesítés/engedélyezés" lehetőséget a beállítások menüből. Engedélyezze App Service hitelesítést, és állítsa be az "elvégzendő művelet, ha a kérés nincs hitelesítve" lehetőséget a "Bejelentkezés a Azure Active Directory" értékre. Ezzel a beállítással biztosítható, hogy minden nem hitelesített kérelem megtagadva legyen (401 válasz).

![Azure Active Directory alapértelmezett szolgáltatóként megjelenített hitelesítési beállítások](media/spring-cloud-tutorial-managed-identities-functions/function-auth-config-1.jpg)

A hitelesítésszolgáltatók területen válassza a Azure Active Directory lehetőséget az alkalmazás regisztrációjának konfigurálásához. Az expressz felügyeleti mód kiválasztása automatikusan létrehoz egy alkalmazás-regisztrációt az Azure AD-bérlőben a megfelelő konfigurációval.

![Azure Active Directory szolgáltató Express felügyeleti módra beállítva](media/spring-cloud-tutorial-managed-identities-functions/function-auth-config-2.jpg)

A beállítások mentése után a Function alkalmazás újraindul, és a rendszer a további kérelmeket az Azure AD-n keresztül fogja bejelentkezni. Ellenőrizheti, hogy a nem hitelesített kérelmeket a rendszer elutasítja-e, ha a Function apps gyökér URL-címére navigál (a fenti lépésben a **gazdagépek** kimenetében visszaadott). A rendszer átirányítja a szervezet Azure AD bejelentkezési képernyőjére.


## <a name="create-an-http-triggered-function"></a>Http által aktivált függvény létrehozása

Egy üres helyi könyvtárban hozzon létre egy új Function alkalmazást, és adjon hozzá egy http által aktivált függvényt.

```console
func init --worker-runtime node
func new --template HttpTrigger --name HttpTrigger
```

Alapértelmezés szerint a függvények a kulcs alapú hitelesítést használják a http-végpontok biztonságossá tételéhez. Mivel az Azure AD-hitelesítés lehetővé teszi a függvények hozzáférésének biztosítását, [a függvény hitelesítési szintjét névtelen értékre szeretnénk állítani](../azure-functions/functions-bindings-http-webhook-trigger.md#secure-an-http-endpoint-in-production).

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

Az alkalmazás mostantól közzétehető az előző lépésben létrehozott [Function app](#create-a-function-app) -példányon.

```console
func azure functionapp publish <your-functionapp-name>
```

A közzétételi parancs kimenetében fel kell sorolni az újonnan létrehozott függvény URL-címét.

```output
Deployment completed successfully.
Syncing triggers...
Functions in <your-functionapp-name>:
    HttpTrigger - [httpTrigger]
        Invoke url: https://<your-functionapp-name>.azurewebsites.net/api/httptrigger
```


## <a name="create-azure-spring-cloud-service-and-app"></a>Azure Spring Cloud-szolgáltatás és-alkalmazás létrehozása
A Spring-Cloud bővítmény telepítése után hozzon létre egy Azure Spring Cloud-példányt az Azure CLI paranccsal `az spring-cloud create` . 

```azurecli-interactive
az extension add --name spring-cloud
az spring-cloud create --name mymsispringcloud --resource-group myResourceGroup --location eastus
```

Az alábbi példa egy nevű alkalmazást hoz létre `msiapp` egy rendszerhez rendelt felügyelt identitással, a paraméter által kért módon `--assign-identity` .

```azurecli
az spring-cloud app create --name "msiapp" --service "mymsispringcloud" --resource-group "myResourceGroup" --assign-endpoint true --assign-identity
```

## <a name="build-sample-spring-boot-app-to-invoke-the-function"></a>A függvény meghívásához hozzon létre egy minta Spring boot-alkalmazást

Ez a minta elindítja a http által aktivált függvényt, ha először egy hozzáférési jogkivonatot kér az [MSI-végponttól](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md#get-a-token-using-http) , és ezzel a tokenrel hitelesíti a függvény http-kérelmét.

1. A minta projekt klónozása. 

    ```console
    git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
    ```

2. Adja meg a függvény URI-JÁT és az trigger nevét az alkalmazás tulajdonságaiban. 

    ```bash
    cd Azure-Spring-Cloud-Samples/managed-identity-function
    vim src/main/resources/application.properties
    ```

    Az Azure Spring Cloud apps felügyelt identitásának használatához adja hozzá a tulajdonságokat a következő tartalommal a *src/Main/Resources/Application. properties* fájlhoz.

    ```
    azure.function.uri=https://<your-functionapp-name>.azurewebsites.net
    azure.function.triggerPath=httptrigger
    ```

3. A minta alkalmazás becsomagolása. 

    ```console
    mvn clean package
    ```

4. Most telepítse az alkalmazást az Azure-ba az Azure CLI paranccsal  `az spring-cloud app deploy` . 

    ```azurecli
    az spring-cloud app deploy  --name "msiapp" --service "mymsispringcloud" --resource-group "myResourceGroup" --jar-path target/sc-managed-identity-function-sample-0.1.0.jar
    ```

5. Az alkalmazás teszteléséhez nyissa meg a nyilvános végpontot vagy a tesztelési végpontot. 

    ```console
    curl https://mymsispringcloud-msiapp.azuremicroservices.io/func/springcloud
    ```

    A válasz törzsében a következő üzenet jelenik meg.
    ```output
    Function Response: Hello, springcloud. This HTTP triggered function executed successfully.
    ```
    
    A Path paraméter módosításával különböző értékeket adhat át a függvénynek.

## <a name="next-steps"></a>Következő lépések

* [A rendszerhez rendelt felügyelt identitás engedélyezése az Azure Spring Cloud Application szolgáltatáshoz](./spring-cloud-howto-enable-system-assigned-managed-identity.md)
* [További információ az Azure-erőforrások felügyelt identitásáról](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Ügyfélalkalmazások konfigurálása a App Service eléréséhez](../app-service/configure-authentication-provider-aad.md#configure-client-apps-to-access-your-app-service)
