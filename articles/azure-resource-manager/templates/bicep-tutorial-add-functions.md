---
title: Oktatóanyag – függvények hozzáadása Azure Resource Manager bicep-fájlokhoz
description: Adja hozzá a függvényeket a bicep-fájlokhoz az értékek létrehozásához.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: references_regions
ms.openlocfilehash: b909beb0cce9ad04ba00068ee25247520dcff47d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102633155"
---
# <a name="tutorial-add-functions-to-azure-resource-manager-bicep-file"></a>Oktatóanyag: függvények hozzáadása Azure Resource Manager bicep-fájlhoz

Ebből az oktatóanyagból megtudhatja, hogyan adhat hozzá [sablon-függvényeket](template-functions.md) a bicep-fájlhoz. A függvények használatával dinamikusan hozhat létre értékeket. A rendszer által biztosított sablon-függvények mellett létrehozhat [felhasználó által definiált függvényeket](./template-user-defined-functions.md)is. Az oktatóanyag elvégzése **7 percet** vesz igénybe.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

Javasoljuk, hogy fejezze be a [paraméterekkel kapcsolatos oktatóanyagot](bicep-tutorial-add-parameters.md), de ez nem kötelező.

A bicep kiterjesztéssel rendelkező Visual Studio Code-nak, valamint Azure PowerShell vagy Azure CLI-nek kell lennie. További információ: [bicep Tools](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>A bicep-fájl áttekintése

Az előző oktatóanyag végén a bicep-fájl a következő tartalmakat használta:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.bicep":::

A Storage-fiók helye az **USA keleti** régiójában rögzített. Előfordulhat azonban, hogy más régiókban is telepítenie kell a Storage-fiókot. A bicep-fájl problémáját a rugalmasság hiánya okozta. Hozzáadhat egy paramétert a helyhez, de nagyszerű lenne, ha az alapértelmezett értéke több értelme, mint a nehezen kódolt érték.

## <a name="use-function"></a>Függvény használata

A függvények rugalmasságot biztosítanak a bicep-fájlhoz az üzembe helyezés során felmerülő értékek dinamikus beolvasásával. Ebben az oktatóanyagban egy függvény használatával beolvassa az üzembe helyezéshez használt erőforráscsoport helyét.

A következő példa a nevű paraméter hozzáadásának módosításait mutatja be `location` . A paraméter alapértelmezett értéke meghívja a [resourceGroup](template-functions-resource.md#resourcegroup) függvényt. Ez a függvény egy objektumot ad vissza, amely az üzembe helyezéshez használt erőforráscsoport adatait ismerteti. Az objektum egyik tulajdonsága a Location tulajdonság. Ha az alapértelmezett értéket használja, a Storage-fiók helye megegyezik az erőforráscsoport helyével. Az erőforráscsoporthoz tartozó erőforrásoknak nem kell ugyanazt a helyet megosztaniuk. Szükség esetén másik helyet is megadhat.

Másolja a teljes fájlt, és cserélje le a bicep-fájlt annak tartalmára.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.bicep" range="1-30" highlight="18,22":::

## <a name="deploy-bicep-file"></a>Bicep-fájl üzembe helyezése

Az előző oktatóanyagokban létrehozott egy Storage-fiókot az USA keleti régiójában, de az erőforráscsoport az USA középső régiójában lett létrehozva. Ebben az oktatóanyagban a Storage-fiók ugyanabban a régióban jön létre, mint az erőforráscsoport. Használja az alapértelmezett értéket a helyhez, így nem kell megadnia a paraméter értékét. Meg kell adnia egy új nevet a Storage-fiókhoz, mert egy másik helyen hoz létre egy Storage-fiókot. Használja például a **store2** előtagot a **store1** helyett.

Ha még nem hozta létre az erőforráscsoportot, tekintse meg az [erőforráscsoport létrehozása](bicep-tutorial-create-first-bicep.md#create-resource-group)című témakört. A példa feltételezi `bicepFile` , hogy a változót a bicep-fájl elérési útjára állította, ahogy az az [első oktatóanyagban](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)is látható.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A központi telepítési parancsmag futtatásához a Azure PowerShell [legújabb verziójára](/powershell/azure/install-az-ps) van szükség.

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
> Ha a telepítés nem sikerült, a `verbose` kapcsolóval kérheti le a létrehozott erőforrásokra vonatkozó információkat. A `debug` kapcsoló használatával további információkat kaphat a hibakereséshez.

## <a name="verify-deployment"></a>Az üzembe helyezés ellenőrzése

A központi telepítés ellenőrzéséhez tekintse meg az erőforráscsoportot a Azure Portalból.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldali menüben válassza az **erőforráscsoportok** lehetőséget.
1. Válassza ki azt az erőforráscsoportot, amelyet központilag telepített.
1. Láthatja, hogy a Storage-fiók erőforrása telepítve van, és ugyanazzal a hellyel rendelkezik, mint az erőforráscsoport.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha továbblép a következő oktatóanyagra, nem kell törölnie az erőforráscsoportot.

Ha most leáll, érdemes lehet törölni a telepített erőforrásokat az erőforráscsoport törlésével.

1. A Azure Portal válassza ki a bal oldali menüből az **erőforráscsoportot** .
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. Válassza az **erőforráscsoport törlése** lehetőséget a felső menüben.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy függvényt használt egy paraméter alapértelmezett értékének definiálásához. Ebben az oktatóanyag-sorozatban továbbra is használhatja a functions funkciót. A sorozat végére a rendszer a bicep-fájl minden szakaszába felveszi a függvényeket.

> [!div class="nextstepaction"]
> [Változók hozzáadása](bicep-tutorial-add-variables.md)
