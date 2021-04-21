---
title: Oktatóanyag – Függvények hozzáadása bicep Azure Resource Manager fájlokhoz
description: Függvények hozzáadása a Bicep-fájlokhoz értékek felépítéséhez.
author: mumian
ms.date: 04/20/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: references_regions
ms.openlocfilehash: 43dec6ceb21a6604bc0034b3f14b79ffd2cbe263
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773800"
---
# <a name="tutorial-add-functions-to-azure-resource-manager-bicep-file"></a>Oktatóanyag: Függvények hozzáadása Azure Resource Manager Bicep-fájlhoz

Ez az oktatóanyag bemutatja, hogyan adhat hozzá [sablonfunkciókat a](template-functions.md) Bicep-fájlhoz. A függvények használatával dinamikusan lehet értékeket összeépíteni. A Bicep jelenleg nem támogatja a felhasználó által definiált függvényeket. Az oktatóanyag **befejezése 7** percet vesz igénybe.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

Javasoljuk, hogy a paraméterekkel [kapcsolatos oktatóanyagot](bicep-tutorial-add-parameters.md)is töltse le, de ez nem kötelező.

A Code-Visual Studio Bicep kiterjesztéssel kell, és vagy a Azure PowerShell vagy az Azure CLI-nek kell lennie. További információ: [Bicep-eszközök.](bicep-tutorial-create-first-bicep.md#get-tools)

## <a name="review-bicep-file"></a>Bicep-fájl áttekintése

Az előző oktatóanyag végén a Bicep-fájl tartalma a következő volt:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.bicep":::

A tárfiók helye az USA keleti régiója. Előfordulhat azonban, hogy a tárfiókot más régiókban is üzembe kell helyeznie. Ismét a bicep-fájl rugalmasságának hiányával kell szembesülni. Hozzáadhatna egy paramétert a helyhez, de nagyszerű lenne, ha az alapértelmezett értéke érthetőbb lenne, mint egy nem kötelezően kódolt érték.

## <a name="use-function"></a>Függvény használata

A függvények rugalmasabbá teszi a Bicep-fájlt azáltal, hogy dinamikusan leküldik az értékeket az üzembe helyezés során. Ebben az oktatóanyagban egy függvény használatával lekérte az üzembe helyezéshez használt erőforráscsoport helyét.

Az alábbi példa egy nevű paraméter hozzáadásának módosításait mutatja `location` be. A paraméter alapértelmezett értéke a [resourceGroup függvényt hívja](template-functions-resource.md#resourcegroup) meg. Ez a függvény egy objektumot ad vissza, amely az üzembe helyezéshez használt erőforráscsoportra vonatkozó információkat tartalmaz. Az objektum egyik tulajdonsága a location tulajdonság. Ha az alapértelmezett értéket használja, a tárfiók helye megegyezik az erőforráscsoport helyével. Az erőforráscsoporton belüli erőforrásoknak nem kell ugyanazon a helyen osztozni. Szükség esetén másik helyet is meg lehet adni.

Másolja a teljes fájlt, és cserélje le a Bicep-fájlt annak tartalmára.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.bicep" range="1-30" highlight="18,22":::

## <a name="deploy-bicep-file"></a>Bicep-fájl üzembe helyezése

Az előző oktatóanyagokban létrehozott egy tárfiókot az USA keleti részén, az erőforráscsoportot azonban az USA középső régiója hozta létre. Ebben az oktatóanyagban a tárfiók ugyanabban a régióban jön létre, mint az erőforráscsoport. A helynél használja az alapértelmezett értéket, így ezt a paraméterértéket nem kell adnia. Új nevet kell adnia a tárfióknak, mert egy másik helyen hoz létre tárfiókot. Például használja a **store2 előtagot** a **store1 helyett.**

Ha még nem hozta létre az erőforráscsoportot, lásd: [Erőforráscsoport létrehozása.](bicep-tutorial-create-first-bicep.md#create-resource-group) A példa feltételezi, hogy a változót a Bicep-fájl elérési útjára beállította, ahogy az az `bicepFile` első [oktatóanyagban is látható.](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A telepítési parancsmag futtatásához a [](/powershell/azure/install-az-ps) parancsmag legújabb verziójával kell Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az üzembe helyezési parancs futtatásához az [Azure CLI](/cli/azure/install-azure-cli) legújabb verziójára van szükség.

```azurecli
az deployment group create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageName={new-unique-name}
```

---

> [!NOTE]
> Ha az üzembe helyezés sikertelen volt, a `verbose` kapcsolóval lekért információ a létrehozott erőforrásokról. A `debug` kapcsolóval további információkhoz jut a hibakereséshez.

## <a name="verify-deployment"></a>Az üzembe helyezés ellenőrzése

Az üzembe helyezés ellenőrzéséhez vizsgálja meg az erőforráscsoportot a Azure Portal.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldali menüben válassza az **Erőforráscsoportok lehetőséget.**
1. Válassza ki azt az erőforráscsoportot, amelybe üzembe helyezett.
1. Láthatja, hogy a tárfiók-erőforrás üzembe lett helyezni, és a helye megegyezik az erőforráscsoport helyével.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a következő oktatóanyagra lép, nem kell törölnie az erőforráscsoportot.

Ha most áll le, érdemes törölnie az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. A Azure Portal bal oldali **menüben válassza** az Erőforráscsoport lehetőséget.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. A **felső menüben válassza az** Erőforráscsoport törlése lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy függvényt használt egy paraméter alapértelmezett értékének definiálása során. Ebben az oktatóanyag-sorozatban továbbra is a függvényeket fogja használni. A sorozat végére függvényeket fog hozzáadni a Bicep-fájl minden szakaszához.

> [!div class="nextstepaction"]
> [Változók hozzáadása](bicep-tutorial-add-variables.md)
