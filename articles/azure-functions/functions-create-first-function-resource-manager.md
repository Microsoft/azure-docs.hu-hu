---
title: Az első függvény létrehozása Azure Resource Manager sablonok használatával
description: Hozzon létre és helyezzen üzembe az Azure-ban egy egyszerű, HTTP által aktivált kiszolgáló nélküli függvényt egy Azure Resource Manager sablon (ARM-sablon) használatával.
ms.date: 3/5/2020
ms.topic: quickstart
ms.service: azure-functions
ms.custom: subject-armqs
ms.openlocfilehash: 1e623405faa89ff41eccdaa57578bc8ac94cd78c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93422824"
---
# <a name="quickstart-create-and-deploy-azure-functions-resources-from-an-arm-template"></a>Rövid útmutató: Azure Functions-erőforrások létrehozása és üzembe helyezése ARM-sablonból

Ebben a cikkben egy Azure Resource Manager sablon (ARM-sablon) használatával hoz létre egy olyan függvényt, amely válaszol a HTTP-kérelmekre. 

A rövid útmutató elvégzésével az Azure-fiókjában néhány USD értékű vagy annál kisebb költséggel jár. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-function-app-create-dynamic%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-account"></a>Azure-fiók 

Mielőtt elkezdené, rendelkeznie kell egy aktív előfizetéssel rendelkező Azure-fiókkal. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/).

### <a name="create-a-local-functions-project"></a>Helyi functions-projekt létrehozása

Ehhez a cikkhez a létrehozott Azure-erőforrásokon futtatandó helyi functions-kód projekt szükséges. Ha nem hozza létre először a közzétenni kívánt projektet, nem tudja befejezni a jelen cikk központi telepítésének szakaszát. 

Válasszon egyet az alábbi lapok közül, kövesse a hivatkozást, és fejezze be a következő szakaszt egy Function-alkalmazás létrehozásához a választott nyelven:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Hozza létre a helyi functions projektet a kiválasztott nyelven a Visual Studio Code-ban:  

+ [C#](create-first-function-vs-code-csharp.md)
+ [Java](create-first-function-vs-code-java.md)
+ [JavaScript](create-first-function-vs-code-node.md)
+ [PowerShell](create-first-function-vs-code-powershell.md)
+ [Python](create-first-function-vs-code-python.md)
+ [TypeScript](create-first-function-vs-code-typescript.md)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[Helyi functions-projekt létrehozása a Visual Studióban](functions-create-your-first-function-visual-studio.md#create-a-function-app-project)

# <a name="command-line"></a>[Parancssor](#tab/command-line)

Hozza létre a helyi functions projektet a választott nyelven a parancssorból:

+ [C#](create-first-function-cli-csharp.md)
+ [Java](create-first-function-cli-java.md)
+ [JavaScript](create-first-function-cli-node.md)
+ [PowerShell](create-first-function-cli-powershell.md)
+ [Python](create-first-function-cli-python.md)
+ [TypeScript](create-first-function-cli-typescript.md)

---

Miután létrehozta a projektet helyileg, létrehozza az új funkció Azure-ban való futtatásához szükséges erőforrásokat. 

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-function-app-create-dynamic/) közül származik.

:::code language="json" source="~/quickstart-templates/101-function-app-create-dynamic/azuredeploy.json":::

A sablon a következő négy Azure-erőforrást hozza létre:

+ [**Microsoft. Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts): hozzon létre egy Azure Storage-fiókot, amelyet a függvények igényelnek.
+ [**Microsoft. Web/kiszolgálófarmok**](/azure/templates/microsoft.web/serverfarms): hozzon létre egy kiszolgáló nélküli felhasználás üzemeltetési csomagot a Function alkalmazáshoz.
+ [**Microsoft. Web/Sites**](/azure/templates/microsoft.web/sites): Function-alkalmazás létrehozása.
+ [**Microsoft. bepillantások/összetevők**](/azure/templates/microsoft.insights/components): Application Insights-példány létrehozása a figyeléshez.

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

Ezután érvényesítse a létrehozott erőforrásokat futtató Function alkalmazást a projekt az Azure-ba való közzétételével és a függvény HTTP-végpontjának meghívásával.

### <a name="publish-the-function-project-to-azure"></a>A Function projekt közzététele az Azure-ban

A következő lépésekkel teheti közzé a projektjét az új Azure-erőforrásokon:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [functions-republish-vscode](../../includes/functions-republish-vscode.md)]

A kimenetben másolja a HTTP-trigger URL-címét. Ezt az Azure-ban futó függvény tesztelésére használhatja. 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza a **Publish** (Közzététel) lehetőséget.

1. A **Közzététel célhelyének** kiválasztása lapon válassza ki a **Azure functions használati tervet** a **meglévő kijelölése** lehetőséggel, majd válassza a **profil létrehozása** lehetőséget.

    :::image type="content" source="media/functions-create-first-function-arm/choose-publish-target-visual-studio.png" alt-text="Meglévő közzétételi cél kiválasztása":::

1. Válassza ki az **előfizetést**, bontsa ki az erőforráscsoportot, válassza ki a Function alkalmazást, majd kattintson **az OK gombra**.

1. A közzététel befejeződése után másolja a **webhely URL-címét**.

    :::image type="content" source="media/functions-create-first-function-arm/publish-summary-site-url.png" alt-text="A webhely URL-címének másolása a közzétételi összegzésből":::

1. Fűzze hozzá az elérési utat `/api/<FUNCTION_NAME>?name=Functions` , ahol a a `<FUNCTION_NAME>` függvény neve. A HTTP-trigger függvényt meghívó URL-cím formátuma a következő:

    `http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=Functions`

Ezt az URL-címet használhatja az Azure-ban futó HTTP-trigger funkció teszteléséhez.

# <a name="command-line"></a>[Parancssor](#tab/command-line)

Ha a helyi kódot egy Azure-beli Function alkalmazásban szeretné közzétenni, használja a következő `publish` parancsot:

```cmd
func azure functionapp publish <FUNCTION_APP_NAME>
```

Ebben a példában a helyére írja a `<FUNCTION_APP_NAME>` Function alkalmazás nevét. Előfordulhat, hogy a használatával újra be kell jelentkeznie `az login` . 

A kimenetben másolja a HTTP-trigger URL-címét. Ezt az Azure-ban futó függvény tesztelésére használhatja.

---

### <a name="invoke-the-function-on-azure"></a>A függvény meghívása az Azure-ban

Illessze be a HTTP-kérelemhez másolt URL-címet a böngésző címsorába, győződjön meg arról, hogy a `name` lekérdezési karakterláncot `?name=Functions` az URL-cím végéhez csatolták, majd hajtsa végre a kérelmet. 

A következőhöz hasonló választ kell megjelennie:

<pre>Hello Functions!</pre>

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha folytatja a következő lépéssel, és hozzáadja az Azure Storage-várólista kimeneti kötését, az összes erőforrást helyben kell tartania, ahogy a már elvégzett műveletekre épít.

Ellenkező esetben a következő paranccsal törölheti az erőforráscsoportot és az összes benne lévő erőforrást, hogy elkerülje a további költségek felmerülését.

```azurecli
az group delete --name <RESOURCE_GROUP_NAME>
```

Cserélje le az `<RESOURCE_GROUP_NAME>` nevet az erőforráscsoport nevére.

## <a name="next-steps"></a>Következő lépések

Most, hogy közzétette az első függvényt, további információt a függvényhez tartozó kimeneti kötés hozzáadásával ismerheti meg.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

> [!div class="nextstepaction"]
> [Kapcsolódás Azure Storage-várólistához](functions-add-output-binding-storage-queue-vs-code.md)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

> [!div class="nextstepaction"]
> [Kapcsolódás Azure Storage-várólistához](functions-add-output-binding-storage-queue-vs.md)

# <a name="command-line"></a>[Parancssor](#tab/command-line)

> [!div class="nextstepaction"]
> [Kapcsolódás Azure Storage-várólistához](functions-add-output-binding-storage-queue-cli.md)

---
