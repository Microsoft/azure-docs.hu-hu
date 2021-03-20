---
title: Oktatóanyag – sablon üzembe helyezése a paraméter használatával
description: A Azure Resource Manager-sablon (ARM-sablon) üzembe helyezéséhez használandó értékeket tartalmazó paraméter-fájlok használata.
author: mumian
ms.date: 09/10/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: b6257161017afc9dab692c43fcc64e5d961a90ba
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97368426"
---
# <a name="tutorial-use-parameter-files-to-deploy-your-arm-template"></a>Oktatóanyag: paraméterek használata az ARM-sablon üzembe helyezéséhez

Ebből az oktatóanyagból megtudhatja, hogyan használhatók a [paraméter-fájlok](parameter-files.md) az üzembe helyezés során beadott értékek tárolására. Az előző oktatóanyagokban beágyazott paramétereket használt a telepítési paranccsal. Ez a megközelítés a Azure Resource Manager-sablon (ARM-sablon) tesztelésére szolgál, de a központi telepítések automatizálása során könnyebben lehet átadni az adott környezethez tartozó értékek halmazát. A paraméter-fájlok megkönnyítik egy adott környezet paramétereinek értékének becsomagolását. Ebben az oktatóanyagban paramétereket hozhat létre fejlesztési és éles környezetekhez. A művelet végrehajtása körülbelül **12 percet** vesz igénybe.

## <a name="prerequisites"></a>Előfeltételek

Javasoljuk, hogy fejezze be a [címkékre vonatkozó oktatóanyagot](template-tutorial-add-tags.md), de ez nem kötelező.

A Visual Studio Code-nak rendelkeznie kell a Resource Manager-eszközök bővítménnyel, valamint Azure PowerShell vagy az Azure CLI-vel. További információ: [sablon eszközei](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Sablon áttekintése

A sablon számos, az üzembe helyezés során megadható paramétert tartalmaz. Az előző oktatóanyag végén a sablon a következőképpen néz ki:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json":::

Ez a sablon jól működik, de most egyszerűen kezelheti a sablonhoz megadott paramétereket.

## <a name="add-parameter-files"></a>Paraméter-fájlok hozzáadása

A paraméter fájljai a sablonhoz hasonló struktúrával rendelkező JSON-fájlok. A fájlban adja meg az üzembe helyezés során átadni kívánt paramétereket.

A paraméter fájljában megadja a sablon paramétereinek értékeit. A paramétert tartalmazó fájlban szereplő paraméterek nevének meg kell egyeznie a sablonban található paraméterek nevével. A név megkülönbözteti a kis-és nagybetűket, de a megfelelő értékeket egyszerűen megtekintheti, ezért azt javasoljuk, hogy a sablon alapján egyeztesse a burkolatot.

Minden paraméterhez nem kell értéket megadnia. Ha egy meghatározatlan paraméter alapértelmezett értékkel rendelkezik, akkor az üzembe helyezés során a rendszer ezt az értéket használja. Ha egy paraméter nem rendelkezik alapértelmezett értékkel, és nincs megadva a paraméter fájljában, a rendszer felszólítja, hogy adjon meg egy értéket az üzembe helyezés során.

Nem adhat meg olyan paraméter-nevet a paraméter-fájlban, amely nem egyezik meg a sablonban szereplő paraméterek nevével. Hibaüzenet jelenik meg, ha ismeretlen paraméterek vannak megadva.

A Visual Studio Code-ban hozzon létre egy új fájlt a következő tartalommal. Mentse a fájlt _azuredeploy.parameters.dev.jsa_ következő néven:.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json":::

Ez a fájl a fejlesztési környezethez tartozó paraméter-fájl. Figyelje meg, hogy **Standard_LRSt** használ a Storage-fiókhoz, és a megkeresi az erőforrásokat egy **fejlesztői** előtaggal, és beállítja a " `Environment` **dev**" címkét.

Ismét hozzon létre egy új fájlt a következő tartalommal. Mentse a fájlt _azuredeploy.parameters.prod.jsa_ következő néven:.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json":::

Ez a fájl az éles környezethez tartozó paraméter-fájl. Figyelje meg, hogy **Standard_GRSt** használ a Storage-fiókhoz, és megnevezi az erőforrásokat egy **contoso** -előtaggal, és beállítja a `Environment` címkét **éles környezetbe**. Valós éles környezetben érdemes lehet olyan app Service-t használni, amely nem ingyenes, de továbbra is ezt az SKU-t fogjuk használni ehhez az oktatóanyaghoz.

## <a name="deploy-template"></a>Sablon üzembe helyezése

A sablon üzembe helyezéséhez használja az Azure CLI-t vagy a Azure PowerShell.

A sablon utolsó tesztje hozzon létre két új erőforráscsoportot. Egyet a fejlesztői környezethez, egyet pedig az éles környezethez.

A sablon és a paraméter változóknál cserélje le a,, `{path-to-the-template-file}` `{path-to-azuredeploy.parameters.dev.json}` `{path-to-azuredeploy.parameters.prod.json}` és a kapcsos zárójeleket a `{}` sablon és a paraméter fájlelérési útjaira.

Először üzembe kell helyezni a fejlesztői környezetet.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{path-to-the-template-file}"
$parameterFile="{path-to-azuredeploy.parameters.dev.json}"
New-AzResourceGroup `
  -Name myResourceGroupDev `
  -Location "East US"
New-AzResourceGroupDeployment `
  -Name devenvironment `
  -ResourceGroupName myResourceGroupDev `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az üzembe helyezési parancs futtatásához az [Azure CLI](/cli/azure/install-azure-cli) legújabb verziójára van szükség.

```azurecli
templateFile="{path-to-the-template-file}"
devParameterFile="{path-to-azuredeploy.parameters.dev.json}"
az group create \
  --name myResourceGroupDev \
  --location "East US"
az deployment group create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $templateFile \
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
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét. Ha elvégezte ezt a sorozatot, három erőforráscsoport törölhető – **myResourceGroup**, **myResourceGroupDev** és **myResourceGroupProd**.
3. Válassza ki az erőforráscsoport nevét.
4. Válassza az **erőforráscsoport törlése** lehetőséget a felső menüben.

## <a name="next-steps"></a>Következő lépések

Gratulálunk, készen áll a sablonok Azure-ba történő üzembe helyezésének bevezetésére. Tudassa velünk, ha megjegyzésekkel és javaslatokkal rendelkezik a visszajelzések szakaszban. Köszönjük!

A következő oktatóanyag-Sorozat részletesebben ismerteti a sablonok üzembe helyezését.

> [!div class="nextstepaction"]
> [Helyi sablon üzembe helyezése](./deployment-tutorial-local-template.md)
