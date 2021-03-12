---
title: Oktatóanyag – Azure Resource Manager bicep-fájl üzembe helyezésére szolgáló paraméter-fájl használata
description: Használjon olyan paramétereket, amelyek tartalmazzák a bicep-fájl üzembe helyezéséhez használt értékeket.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: ca3a73cde9549bfcdfd47bc4f1955904fac69d1c
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "102632356"
---
# <a name="tutorial-use-parameter-files-to-deploy-azure-resource-manager-bicep-file"></a>Oktatóanyag: paraméterek használata Azure Resource Manager bicep-fájl üzembe helyezéséhez

Ebből az oktatóanyagból megtudhatja, hogyan használhatók a [paraméter-fájlok](parameter-files.md) az üzembe helyezés során beadott értékek tárolására. Az előző oktatóanyagokban beágyazott paramétereket használt a telepítési paranccsal. Ez a megközelítés a bicep-fájl tesztelésére szolgál, de a központi telepítések automatizálása során könnyebben lehet átadni a környezete értékeit. A paraméter-fájlok megkönnyítik egy adott környezet paramétereinek értékének becsomagolását. JSON-sablon telepítésekor ugyanazt a JSON-paramétert használja. Ebben az oktatóanyagban paramétereket hozhat létre fejlesztési és éles környezetekhez. A művelet végrehajtása körülbelül **12 percet** vesz igénybe.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

Javasoljuk, hogy fejezze be a [címkékre vonatkozó oktatóanyagot](bicep-tutorial-add-tags.md), de ez nem kötelező.

A bicep kiterjesztéssel rendelkező Visual Studio Code-nak, valamint Azure PowerShell vagy Azure CLI-nek kell lennie. További információ: [bicep Tools](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>A bicep-fájl áttekintése

A bicep-fájl számos paramétert tartalmaz, amelyeket az üzembe helyezés során megadhat. Az előző oktatóanyag végén a bicep-fájl a következőképpen néz ki:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.bicep":::

Ez a bicep-fájl jól működik, de most egyszerűen kezelheti a bicep-fájlhoz megadott paramétereket.

## <a name="add-parameter-files"></a>Paraméter-fájlok hozzáadása

A paraméterek a JSON-sablonokhoz hasonló szerkezettel rendelkező JSON-fájlok. A fájlban adja meg az üzembe helyezés során átadni kívánt paramétereket.

A paraméter fájljában megadja a bicep fájlban lévő paraméterek értékeit. A paramétert tartalmazó fájlban szereplő paraméterek nevének meg kell egyeznie a bicep fájlban lévő paraméter nevével. A név megkülönbözteti a kis-és nagybetűket, de a megfelelő értékeket egyszerűen megtekintheti a bicep-fájlból származó burkolattal.

Minden paraméterhez nem kell értéket megadnia. Ha egy meghatározatlan paraméter alapértelmezett értékkel rendelkezik, akkor az üzembe helyezés során a rendszer ezt az értéket használja. Ha egy paraméter nem rendelkezik alapértelmezett értékkel, és nincs megadva a paraméter fájljában, a rendszer felszólítja, hogy adjon meg egy értéket az üzembe helyezés során.

Nem adhat meg paraméter-nevet a paraméter-fájlban, amely nem felel meg a paraméter neveként a bicep fájlban. Hibaüzenet jelenik meg, ha ismeretlen paraméterek vannak megadva.

A Visual Studio Code-ban hozzon létre egy új fájlt a következő tartalommal. Mentse a fájlt _azuredeploy.parameters.dev.jsa_ következő néven:.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json":::

Ez a fájl a fejlesztési környezethez tartozó paraméter-fájl. Figyelje meg, hogy **Standard_LRSt** használ a Storage-fiókhoz, és a megkeresi az erőforrásokat egy **fejlesztői** előtaggal, és beállítja a " `Environment` **dev**" címkét.

Ismét hozzon létre egy új fájlt a következő tartalommal. Mentse a fájlt _azuredeploy.parameters.prod.jsa_ következő néven:.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json":::

Ez a fájl az éles környezethez tartozó paraméter-fájl. Figyelje meg, hogy **Standard_GRSt** használ a Storage-fiókhoz, és megnevezi az erőforrásokat egy **contoso** -előtaggal, és beállítja a `Environment` címkét **éles környezetbe**. Valós éles környezetben érdemes lehet olyan app Service-t használni, amely nem ingyenes, de továbbra is ezt az SKU-t fogjuk használni ehhez az oktatóanyaghoz.

## <a name="deploy-bicep-file"></a>Bicep-fájl üzembe helyezése

Használja az Azure CLI-t vagy a Azure PowerShell a bicep-fájl üzembe helyezéséhez.

Ebben az oktatóanyagban két új erőforráscsoportot hozunk létre. Egyet a fejlesztői környezethez, egyet pedig az éles környezethez.

A sablon és a paraméter változóknál cserélje le a,, `{path-to-the-bicep-file}` `{path-to-azuredeploy.parameters.dev.json}` `{path-to-azuredeploy.parameters.prod.json}` és a kapcsos zárójeleket a `{}` bicep-fájl és a paraméter fájlelérési útjaira.

Először üzembe kell helyezni a fejlesztői környezetet.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A központi telepítési parancsmag futtatásához a Azure PowerShell [legújabb verziójára](/powershell/azure/install-az-ps) van szükség.

```azurepowershell
$bicepFile = "{path-to-the-bicep-file}"
$parameterFile="{path-to-azuredeploy.parameters.dev.json}"
New-AzResourceGroup `
  -Name myResourceGroupDev `
  -Location "East US"
New-AzResourceGroupDeployment `
  -Name devenvironment `
  -ResourceGroupName myResourceGroupDev `
  -TemplateFile $bicepFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az üzembe helyezési parancs futtatásához az [Azure CLI](/cli/azure/install-azure-cli) legújabb verziójára van szükség.

```azurecli
bicepFile="{path-to-the-bicep-file}"
devParameterFile="{path-to-azuredeploy.parameters.dev.json}"
az group create \
  --name myResourceGroupDev \
  --location "East US"
az deployment group create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $bicepFile \
  --parameters $devParameterFile
```

---

Most üzembe helyezzük az éles környezetben.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$parameterFile="{path-to-azuredeploy.parameters.prod.json}"
New-AzResourceGroup `
  -Name myResourceGroupProd `
  -Location "West US"
New-AzResourceGroupDeployment `
  -Name prodenvironment `
  -ResourceGroupName myResourceGroupProd `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
prodParameterFile="{path-to-azuredeploy.parameters.prod.json}"
az group create \
  --name myResourceGroupProd \
  --location "West US"
az deployment group create \
  --name prodenvironment \
  --resource-group myResourceGroupProd \
  --template-file $templateFile \
  --parameters $prodParameterFile
```

---

> [!NOTE]
> Ha a telepítés nem sikerült, a `verbose` kapcsolóval kérheti le a létrehozott erőforrásokra vonatkozó információkat. A `debug` kapcsoló használatával további információkat kaphat a hibakereséshez.

## <a name="verify-deployment"></a>Az üzembe helyezés ellenőrzése

A központi telepítés ellenőrzéséhez tekintse meg az Azure Portal lévő erőforráscsoportokat.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldali menüben válassza az **erőforráscsoportok** lehetőséget.
1. Ekkor megjelenik az oktatóanyagban üzembe helyezett két új erőforráscsoport.
1. Válassza ki az erőforráscsoportot, és tekintse meg a telepített erőforrásokat. Figyelje meg, hogy az adott környezethez tartozó paraméterben megadott értékeknek felelnek meg.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

1. A Azure Portal válassza ki a bal oldali menüből az **erőforráscsoportot** .
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. Válassza az **erőforráscsoport törlése** lehetőséget a felső menüben.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy paraméterérték használatával helyezheti üzembe a bicep-fájlt. A következő oktatóanyagban megtudhatja, hogyan modularize a bicep-fájljait.

> [!div class="nextstepaction"]
> [Modulok hozzáadása](./bicep-tutorial-add-modules.md)
