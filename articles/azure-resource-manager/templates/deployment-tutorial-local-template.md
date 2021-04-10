---
title: Oktatóanyag – helyi Azure Resource Manager sablon üzembe helyezése
description: Megtudhatja, hogyan helyezhet üzembe egy Azure Resource Manager-sablont (ARM-sablont) a helyi számítógépről
ms.date: 02/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: d8d54acfa345994edcc401170e70495b3826bfdf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100384231"
---
# <a name="tutorial-deploy-a-local-arm-template"></a>Oktatóanyag: helyi ARM-sablon üzembe helyezése

Megtudhatja, hogyan helyezhet üzembe egy Azure Resource Manager-sablont (ARM-sablont) a helyi gépről. A művelet elvégzése körülbelül **8 percet** vesz igénybe.

Ez az oktatóanyag egy sorozat első része. Ahogy halad a sorozaton, egy csatolt sablon létrehozásával modularize a sablont, a csatolt sablont egy Storage-fiókban tárolja, és az SAS-token használatával biztonságossá teheti a csatolt sablont, és megtudhatja, hogyan hozhat létre DevOps-folyamatokat a sablonok telepítéséhez. Ez a sorozat a sablonok központi telepítésére koncentrál. Ha szeretné megismerni a sablonok fejlesztését, tekintse meg a [kezdő oktatóanyagokat](./template-tutorial-create-first-template.md).

## <a name="get-tools"></a>Eszközök beolvasása

Kezdjük azzal, hogy megbizonyosodjon arról, hogy rendelkezik a sablonok üzembe helyezéséhez szükséges eszközökkel.

### <a name="command-line-deployment"></a>Parancssori telepítés

A sablon üzembe helyezéséhez Azure PowerShell vagy Azure CLI szükséges. A telepítési utasításokért lásd:

- [Az Azure PowerShell telepítése](/powershell/azure/install-az-ps)
- [Az Azure CLI telepítése Windows rendszeren](/cli/azure/install-azure-cli-windows)
- [Az Azure CLI telepítése Linux rendszeren](/cli/azure/install-azure-cli-linux)
- [Az Azure CLI telepítése macOS rendszeren](/cli/azure/install-azure-cli-macos)

A Azure PowerShell vagy az Azure CLI telepítését követően ellenőrizze, hogy az első alkalommal jelentkezik-e be. Segítségért lásd: [Bejelentkezés – PowerShell](/powershell/azure/install-az-ps#sign-in) vagy [Bejelentkezés – Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in).

### <a name="editor-optional"></a>Szerkesztő (nem kötelező)

A sablonok JSON-fájlok. A sablonok áttekintéséhez és szerkesztéséhez jó JSON-szerkesztőre van szükség. A Visual Studio Code-ot a Resource Manager-eszközök bővítménnyel ajánljuk. Ha telepítenie kell ezeket az eszközöket, tekintse meg a gyors útmutató [: ARM-sablonok létrehozása a Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md)használatával című témakört.

## <a name="review-template"></a>Sablon áttekintése

A sablon üzembe helyez egy Storage-fiókot, egy app Service-csomagot és egy webalkalmazást. Ha érdekli a sablon létrehozása, tekintse át a [Gyorsindítás sablonokkal kapcsolatos oktatóanyagot](template-tutorial-quickstart-template.md). Az oktatóanyag elvégzéséhez azonban nem szükséges.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

> [!IMPORTANT]
> A Storage-fiókok nevének 3 – 24 karakter hosszúnak kell lennie, és csak számokat és kisbetűket használjon. A névnek egyedinek kell lennie. A sablonban a Storage-fiók neve a projekt neve, amely a **tároló** hozzáfűzésével van ellátva, a projekt nevének pedig 3 – 11 karakterből kell állnia. Így a projekt nevének meg kell felelnie a Storage-fiók nevének, és kevesebb, mint 11 karakterből áll.

Mentse a sablon egy példányát a helyi számítógépre a _. JSON_ kiterjesztéssel, például _azuredeploy.jsbe_. Ezt a sablont később is üzembe helyezheti az oktatóanyagban.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Ha egy sablon üzembe helyezéséhez Azure PowerShell/Azure CLI használatával szeretne dolgozni, jelentkezzen be az Azure-beli hitelesítő adataival.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az login
```

---

Ha több Azure-előfizetéssel rendelkezik, válassza ki a használni kívánt előfizetést. Cserélje le `[SubscriptionID/SubscriptionName]` a és a szögletes zárójeleket az `[]` előfizetési adataira:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzContext [SubscriptionID/SubscriptionName]
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az account set --subscription [SubscriptionID/SubscriptionName]
```

---

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Sablon központi telepítésekor megadhat egy erőforráscsoportot, amely az erőforrásokat fogja tartalmazni. Az üzembe helyezési parancs futtatása előtt hozza létre az erőforráscsoportot az Azure CLI-vel vagy az Azure PowerShell-lel. Válassza ki az alábbi kódrészletet a Azure PowerShell és az Azure CLI közötti választáshoz. A cikkben található CLI-példák a bash-rendszerhéjra íródnak.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource and resource group names"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup `
  -Name $resourceGroupName `
  -Location "Central US"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
echo "Enter a project name that is used to generate resource and resource group names:"
read projectName
resourceGroupName="${projectName}rg"

az group create \
  --name $resourceGroupName \
  --location "Central US"
```

---

## <a name="deploy-template"></a>Sablon üzembe helyezése

A sablon üzembe helyezéséhez használja az egyik vagy mindkét központi telepítési lehetőséget.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
  -Name DeployLocalTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -projectName $projectName `
  -verbose
```

További információ a sablonok Azure PowerShell használatával történő telepítéséről: [erőforrások üzembe helyezése ARM-sablonokkal és Azure PowerShell](./deploy-powershell.md).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
echo "Enter the same project name:"
read projectName
echo "Enter the template file path and file name:"
read templateFile
resourceGroupName="${projectName}rg"

az deployment group create \
  --name DeployLocalTemplate \
  --resource-group $resourceGroupName \
  --template-file $templateFile \
  --parameters projectName=$projectName \
  --verbose
```

Ha többet szeretne megtudni a sablon Azure CLI-vel történő üzembe helyezéséről, olvassa el az [erőforrások üzembe helyezése ARM-sablonokkal és az Azure CLI-vel](./deploy-cli.md)című

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az erőforráscsoport törlésével törölje az üzembe helyezett erőforrásokat.

1. A Azure Portal válassza ki a bal oldali menüből az **erőforráscsoportot** .
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. Válassza az **erőforráscsoport törlése** lehetőséget a felső menüben.

## <a name="next-steps"></a>Következő lépések

Megtanulta, hogyan helyezhet üzembe egy helyi sablont. A következő oktatóanyagban elkülöníti a sablont egy fő sablonba és egy csatolt sablonba, és megtudhatja, hogyan tárolhatja és biztonságossá teheti a csatolt sablont.

> [!div class="nextstepaction"]
> [Csatolt sablon üzembe helyezése](./deployment-tutorial-linked-template.md)
