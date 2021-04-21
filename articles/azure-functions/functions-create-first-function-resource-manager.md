---
title: Az első függvény létrehozása Azure Resource Manager sablonokkal
description: Hozzon létre és telepítsen az Azure-ban egy egyszerű, HTTP által aktivált kiszolgáló nélküli függvényt egy Azure Resource Manager sablon (ARM-sablon) használatával.
ms.date: 3/5/2020
ms.topic: quickstart
ms.service: azure-functions
ms.custom: subject-armqs, devx-track-azurepowershell
ms.openlocfilehash: 0badc233597c13228e9826ed062cc15828c65cf2
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833381"
---
# <a name="quickstart-create-and-deploy-azure-functions-resources-from-an-arm-template"></a>Rövid útmutató: Erőforrás-Azure Functions létrehozása és üzembe helyezése ARM-sablonból

Ebben a cikkben egy Azure Resource Manager (ARM-sablon) használatával hoz létre egy http-kérésekre válaszoló függvényt. 

Ennek a rövid útmutatónak az elvégzése néhány dollár vagy annál kisebb költséggel jár az Azure-fiókjában. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-function-app-create-dynamic%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-account"></a>Azure-fiók 

Mielőtt hozzákezd, aktív előfizetéssel kell lennie egy Azure-fióknak. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/)

### <a name="create-a-local-functions-project"></a>Helyi függvényprojekt létrehozása

Ehhez a cikkhez egy helyi függvénykód-projektre van szükség a létrehozott Azure-erőforrásokon való futtatáshoz. Ha nem hoz létre először közzétenni tervezett projektet, nem fogja tudni befejezni a cikk üzembe helyezési szakaszát. 

Válasszon az alábbi lapok közül, kövesse a hivatkozást, és töltse ki a szakaszt egy függvényalkalmazás létrehozásához a választott nyelven:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Hozza létre a helyi függvényprojektet a választott nyelven Visual Studio Code-ban:  

+ [C#](create-first-function-vs-code-csharp.md)
+ [Java](create-first-function-vs-code-java.md)
+ [JavaScript](create-first-function-vs-code-node.md)
+ [PowerShell](create-first-function-vs-code-powershell.md)
+ [Python](create-first-function-vs-code-python.md)
+ [TypeScript](create-first-function-vs-code-typescript.md)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[Hozza létre a helyi függvényprojektet a Visual Studio](functions-create-your-first-function-visual-studio.md#create-a-function-app-project)

# <a name="command-line"></a>[Parancssor](#tab/command-line)

Hozza létre a helyi függvényprojektet a választott nyelven a parancssorból:

+ [C#](create-first-function-cli-csharp.md)
+ [Java](create-first-function-cli-java.md)
+ [JavaScript](create-first-function-cli-node.md)
+ [PowerShell](create-first-function-cli-powershell.md)
+ [Python](create-first-function-cli-python.md)
+ [TypeScript](create-first-function-cli-typescript.md)

---

A projekt helyi létrehozása után létrehozza az új függvény Azure-ban való futtatásához szükséges erőforrásokat. 

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-function-app-create-dynamic/) közül származik.

:::code language="json" source="~/quickstart-templates/101-function-app-create-dynamic/azuredeploy.json":::

Ez a sablon a következő négy Azure-erőforrást hozta létre:

+ [**Microsoft.Storage/storageAccounts:**](/azure/templates/microsoft.storage/storageaccounts)hozzon létre egy Azure Storage-fiókot, amelyre a Functionsnek szüksége van.
+ [**Microsoft.Web/serverfarms:**](/azure/templates/microsoft.web/serverfarms)kiszolgáló nélküli használatra szánt üzemeltetési csomag létrehozása a függvényalkalmazáshoz.
+ [**Microsoft.Web/sites:**](/azure/templates/microsoft.web/sites)függvényalkalmazás létrehozása.
+ [**microsoft.insights/components:**](/azure/templates/microsoft.insights/components)hozzon létre Application Insights-példányt a monitorozáshoz.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli-interactive
read -p "Enter a resource group name that is used for generating resource names:" resourceGroupName &&
read -p "Enter the location (like 'eastus' or 'northeurope'):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter a resource group name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (like 'eastus' or 'northeurope')"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```
---

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

A következő lépés a létrehozott erőforrásokat üzemeltető függvényalkalmazás ellenőrzése a projekt Azure-ban való közzétételével és a függvény HTTP-végpontjának hívása.

### <a name="publish-the-function-project-to-azure"></a>A függvényprojekt közzététele az Azure-ban

A következő lépésekkel tegye közzé a projektet az új Azure-erőforrásokon:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [functions-republish-vscode](../../includes/functions-republish-vscode.md)]

A kimenetbe másolja a HTTP-eseményindító URL-címét. Ezzel teszteli az Azure-ban futó függvényt. 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza a **Publish** (Közzététel) lehetőséget.

1. A **Közzétételi cél kiválasztása részen válassza a** Azure Functions csomag **lehetőséget** a **Meglévő** kiválasztása gombra, majd válassza a Profil **létrehozása lehetőséget.**

    :::image type="content" source="media/functions-create-first-function-arm/choose-publish-target-visual-studio.png" alt-text="Meglévő közzétételi cél kiválasztása":::

1. Válassza ki az **előfizetését,** bontsa ki az erőforráscsoportot, válassza ki a függvényalkalmazást, majd kattintson az **OK gombra.**

1. A közzététel befejezése után másolja ki a **webhely URL-címét.**

    :::image type="content" source="media/functions-create-first-function-arm/publish-summary-site-url.png" alt-text="A webhely URL-címének másolása a közzétételi összegzésből":::

1. Fűzheti hozzá a `/api/<FUNCTION_NAME>?name=Functions` útvonalat, ahol `<FUNCTION_NAME>` a a függvény neve. A HTTP-eseményindító függvényt indító URL-cím formátuma a következő:

    `http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=Functions`

Ezt az URL-címet az Azure-ban futó HTTP-eseményindító függvény tesztelésére használhatja.

# <a name="command-line"></a>[Parancssor](#tab/command-line)

A helyi kód Azure-beli függvényalkalmazásban való közzétételéhez használja az `publish` parancsot:

```cmd
func azure functionapp publish <FUNCTION_APP_NAME>
```

Ebben a példában cserélje `<FUNCTION_APP_NAME>` le a helyére a függvényalkalmazás nevét. Előfordulhat, hogy újra be kell jelentkeznie a `az login` használatával. 

A kimenetbe másolja a HTTP-eseményindító URL-címét. Ezzel teszteli az Azure-ban futó függvényt.

---

### <a name="invoke-the-function-on-azure"></a>A függvény meghívása az Azure-ban

Illessze be a HTTP-kéréshez kimásolt URL-címet a böngésző címsorába, győződjön meg arról, hogy a lekérdezési sztring hozzá lett fűzve az URL-cím végéhez, majd hajtsa végre a `name` `?name=Functions` kérést. 

A következő választ kell látnia:

<pre>Hello Functions!</pre>

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha folytatja a következő lépéssel, és hozzáad egy Azure Storage-üzenetsor kimeneti kötését, tartsa a helyén az összes erőforrást, miközben a már végzett munka alapján épít.

Ellenkező esetben a következő paranccsal törölheti az erőforráscsoportot és annak összes tartalmazott erőforrását, hogy elkerülje a további költségeket.

```azurecli
az group delete --name <RESOURCE_GROUP_NAME>
```

Cserélje `<RESOURCE_GROUP_NAME>` le a helyére az erőforráscsoport nevét.

## <a name="next-steps"></a>Következő lépések

Most, hogy közzétette az első függvényét, további információért adja hozzá a függvényhez a kimeneti kötést.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

> [!div class="nextstepaction"]
> [Csatlakozás Azure Storage-üzenetsorhoz](functions-add-output-binding-storage-queue-vs-code.md)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

> [!div class="nextstepaction"]
> [Csatlakozás Azure Storage-üzenetsorhoz](functions-add-output-binding-storage-queue-vs.md)

# <a name="command-line"></a>[Parancssor](#tab/command-line)

> [!div class="nextstepaction"]
> [Csatlakozás Azure Storage-üzenetsorhoz](functions-add-output-binding-storage-queue-cli.md)

---
