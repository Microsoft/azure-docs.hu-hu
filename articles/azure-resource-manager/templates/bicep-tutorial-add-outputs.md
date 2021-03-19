---
title: Oktatóanyag – kimenetek hozzáadása Azure Resource Manager bicep-fájlhoz
description: Adja hozzá a kimeneteket a bicep-fájlhoz, hogy leegyszerűsítse a szintaxist.
author: mumian
ms.date: 03/17/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 4b7d7a1414091c516dba2c474e1681ba357b55a1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594308"
---
# <a name="tutorial-add-outputs-to-azure-resource-manager-bicep-file"></a>Oktatóanyag: kimenetek hozzáadása Azure Resource Manager bicep-fájlhoz

Ebből az oktatóanyagból megtudhatja, hogyan adhat vissza értéket az üzembe helyezésből. A kimenetek akkor használhatók, ha egy központilag telepített erőforrásból származó értékre van szüksége. Az oktatóanyag elvégzése **7 percet** vesz igénybe.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

Javasoljuk, hogy fejezze be a [változókról szóló oktatóanyagot](bicep-tutorial-add-variables.md), de ez nem kötelező.

A bicep kiterjesztéssel rendelkező Visual Studio Code-nak, valamint Azure PowerShell vagy Azure CLI-nek kell lennie. További információ: [bicep Tools](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>A bicep-fájl áttekintése

Az előző oktatóanyag végén a bicep-fájl a következő tartalmakat használta:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.bicep":::

Üzembe helyez egy Storage-fiókot, de nem ad vissza semmilyen információt a Storage-fiókról. Előfordulhat, hogy a tulajdonságokat egy új erőforrásból kell rögzítenie, hogy később is elérhetők legyenek a hivatkozáshoz.

## <a name="add-outputs"></a>Kimenetek hozzáadása

A kimenetek segítségével adhat vissza értékeket a központi telepítésből. Előfordulhat például, hogy hasznos lehet az új Storage-fiókhoz tartozó végpontok beszerzése.

A következő példa a bicep-fájl módosítását mutatja a kimeneti érték hozzáadásához. Másolja a teljes fájlt, és cserélje le a bicep-fájlt annak tartalmára.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.bicep" range="1-33" highlight="33":::

Néhány fontos elemet érdemes megjegyezni a hozzáadott kimeneti értékről.

A visszaadott érték típusa `object` , ami azt jelenti, hogy egy sablon objektumot ad vissza.

A tulajdonságnak a Storage-fiókból való beszerzéséhez `primaryEndpoints` használja a Storage-fiók szimbolikus nevét. A Visual Studio Code automatikus kiegészítés funkciója a tulajdonságok teljes listáját jeleníti meg:

   ![Visual Studio Code bicep szimbolikus név objektum tulajdonságai](./media/bicep-tutorial-add-outputs/visual-studio-code-bicep-output-properties.png)

## <a name="deploy-bicep-file"></a>Bicep-fájl üzembe helyezése

Készen áll a bicep-fájl üzembe helyezésére, és megtekinteni a visszaadott értéket.

Ha még nem hozta létre az erőforráscsoportot, tekintse meg az [erőforráscsoport létrehozása](bicep-tutorial-create-first-bicep.md#create-resource-group)című témakört. A példa feltételezi `bicepFile` , hogy a változót a bicep-fájl elérési útjára állította, ahogy az az [első oktatóanyagban](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)is látható.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addoutputs `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az üzembe helyezési parancs futtatásához az [Azure CLI](/cli/azure/install-azure-cli) legújabb verziójára van szükség.

```azurecli
az deployment group create \
  --name addoutputs \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

A központi telepítési parancs kimenetében az alábbi példához hasonló objektum jelenik meg, ha a kimenet JSON formátumú:

```json
{
  "dfs": "https://storeluktbfkpjjrkm.dfs.core.windows.net/",
  "web": "https://storeluktbfkpjjrkm.z19.web.core.windows.net/",
  "blob": "https://storeluktbfkpjjrkm.blob.core.windows.net/",
  "queue": "https://storeluktbfkpjjrkm.queue.core.windows.net/",
  "table": "https://storeluktbfkpjjrkm.table.core.windows.net/",
  "file": "https://storeluktbfkpjjrkm.file.core.windows.net/"
}
```

> [!NOTE]
> Ha a telepítés nem sikerült, a `verbose` kapcsolóval kérheti le a létrehozott erőforrásokra vonatkozó információkat. A `debug` kapcsoló használatával további információkat kaphat a hibakereséshez.

## <a name="review-your-work"></a>A munka áttekintése

Sokat tett az elmúlt hat oktatóanyagban. Szánjon egy kis időt a megtörténtek áttekintésére. Létrehozta a könnyen megadható paraméterekkel rendelkező bicep-fájlt. A bicep-fájl többször is felhasználható a különböző környezetekben, mert lehetővé teszi a testreszabást, és dinamikusan létrehozza a szükséges értékeket. Emellett a parancsfájlban használható Storage-fiókkal kapcsolatos információkat is adja vissza.

Most nézzük meg az erőforráscsoportot és az üzembe helyezési előzményeket.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldali menüben válassza az **erőforráscsoportok** lehetőséget.
1. Válassza ki azt az erőforráscsoportot, amelyet központilag telepített.
1. Attól függően, hogy milyen lépések történtek, rendelkeznie kell legalább egy, és talán több Storage-fiókkal az erőforráscsoporthoz.
1. Emellett több sikeres központi telepítést is meg kell jelennie az előzményekben. Válassza ki a hivatkozást.

   ![Központi telepítések kiválasztása](./media/bicep-tutorial-add-outputs/select-deployments.png)

1. Az összes üzemelő példány megjelenik az előzmények között. Válassza ki a **addoutputs** nevű központi telepítést.

   ![Telepítési előzmények megjelenítése](./media/bicep-tutorial-add-outputs/show-history.png)

1. A bemenetek áttekinthetők.

   ![Bemenetek megjelenítése](./media/bicep-tutorial-add-outputs/show-inputs.png)

1. A kimenetek áttekinthetők.

   ![Kimenetek megjelenítése](./media/bicep-tutorial-add-outputs/show-outputs.png)

1. Tekintse át a JSON-sablont.

   ![Sablon megjelenítése](./media/bicep-tutorial-add-outputs/show-template.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha továbblép a következő oktatóanyagra, nem kell törölnie az erőforráscsoportot.

Ha most leáll, érdemes lehet törölni a telepített erőforrásokat az erőforráscsoport törlésével.

1. A Azure Portal válassza ki a bal oldali menüből az **erőforráscsoportot** .
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. Válassza az **erőforráscsoport törlése** lehetőséget a felső menüben.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban hozzáadott egy visszatérési értéket a bicep-fájlhoz. A következő oktatóanyagban megtudhatja, hogyan exportálhat egy JSON-sablont, és hogyan használhatja az exportált sablon részeit a bicep fájlban.

> [!div class="nextstepaction"]
> [Exportált sablon használata](bicep-tutorial-export-template.md)
