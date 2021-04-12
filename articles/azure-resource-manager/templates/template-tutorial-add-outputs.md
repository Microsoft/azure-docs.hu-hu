---
title: Oktatóanyag – kimenetek hozzáadása a sablonhoz
description: Adja hozzá a kimeneteket a Azure Resource Manager-sablonhoz (ARM-sablon) a szintaxis egyszerűsítése érdekében.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 7e022822f1306a5351cef43498f00a0ac0f2ef63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101707285"
---
# <a name="tutorial-add-outputs-to-your-arm-template"></a>Oktatóanyag: kimenetek hozzáadása az ARM-sablonhoz

Ebből az oktatóanyagból megtudhatja, hogyan adhat vissza értéket a Azure Resource Manager-sablonból (ARM-sablon). A kimenetek akkor használhatók, ha egy központilag telepített erőforrásból származó értékre van szüksége. Az oktatóanyag elvégzése **7 percet** vesz igénybe.

## <a name="prerequisites"></a>Előfeltételek

Javasoljuk, hogy fejezze be a [változókról szóló oktatóanyagot](template-tutorial-add-variables.md), de ez nem kötelező.

A Visual Studio Code-nak rendelkeznie kell a Resource Manager-eszközök bővítménnyel, valamint Azure PowerShell vagy az Azure CLI-vel. További információ: [sablon eszközei](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Sablon áttekintése

Az előző oktatóanyag végén a sablon a következő JSON-t használta:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json":::

Üzembe helyez egy Storage-fiókot, de nem ad vissza semmilyen információt a Storage-fiókról. Előfordulhat, hogy a tulajdonságokat egy új erőforrásból kell rögzítenie, hogy később is elérhetők legyenek a hivatkozáshoz.

## <a name="add-outputs"></a>Kimenetek hozzáadása

A kimenetek segítségével adhat vissza értékeket a sablonból. Előfordulhat például, hogy hasznos lehet az új Storage-fiókhoz tartozó végpontok beszerzése.

A következő példa kiemeli a sablon módosítását a kimeneti érték hozzáadásához. Másolja a teljes fájlt, és cserélje le a sablont a tartalmára.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json" range="1-53" highlight="47-52":::

Néhány fontos elemet érdemes megjegyezni a hozzáadott kimeneti értékről.

A visszaadott érték típusa `object` , ami azt jelenti, hogy egy JSON-objektumot ad vissza.

A [hivatkozási](template-functions-resource.md#reference) függvényt használja a Storage-fiók futásidejű állapotának lekéréséhez. Egy erőforrás futásidejű állapotának lekéréséhez egy erőforrás nevét vagy AZONOSÍTÓját kell átadnia. Ebben az esetben ugyanazt a változót használja, amelyet a Storage-fiók nevének létrehozásához használt.

Végül visszaadja a `primaryEndpoints` tulajdonságot a Storage-fiókból.

## <a name="deploy-template"></a>Sablon üzembe helyezése

Készen áll a sablon üzembe helyezésére, és megtekinteni a visszaadott értéket.

Ha még nem hozta létre az erőforráscsoportot, tekintse meg az [erőforráscsoport létrehozása](template-tutorial-create-first-template.md#create-resource-group)című témakört. A példa feltételezi `templateFile` , hogy a változót a sablonfájl elérési útjára állította, ahogy az [első oktatóanyagban](template-tutorial-create-first-template.md#deploy-template)is látható.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addoutputs `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az üzembe helyezési parancs futtatásához az [Azure CLI](/cli/azure/install-azure-cli) legújabb verziójára van szükség.

```azurecli
az deployment group create \
  --name addoutputs \
  --resource-group myResourceGroup \
  --template-file $templateFile \
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

Sokat tett az elmúlt hat oktatóanyagban. Szánjon egy kis időt a megtörténtek áttekintésére. Létrehozott egy könnyen elérhető paramétereket tartalmazó sablont. A sablon többször is felhasználható a különböző környezetekben, mivel lehetővé teszi a testreszabást, és dinamikusan létrehozza a szükséges értékeket. Emellett a parancsfájlban használható Storage-fiókkal kapcsolatos információkat is adja vissza.

Most nézzük meg az erőforráscsoportot és az üzembe helyezési előzményeket.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldali menüben válassza az **erőforráscsoportok** lehetőséget.
1. Válassza ki azt az erőforráscsoportot, amelyet központilag telepített.
1. Attól függően, hogy milyen lépések történtek, rendelkeznie kell legalább egy, és talán több Storage-fiókkal az erőforráscsoporthoz.
1. Emellett több sikeres központi telepítést is meg kell jelennie az előzményekben. Válassza ki a hivatkozást.

   ![Központi telepítések kiválasztása](./media/template-tutorial-add-outputs/select-deployments.png)

1. Az összes üzemelő példány megjelenik az előzmények között. Válassza ki a **addoutputs** nevű központi telepítést.

   ![Telepítési előzmények megjelenítése](./media/template-tutorial-add-outputs/show-history.png)

1. A bemenetek áttekinthetők.

   ![Bemenetek megjelenítése](./media/template-tutorial-add-outputs/show-inputs.png)

1. A kimenetek áttekinthetők.

   ![Kimenetek megjelenítése](./media/template-tutorial-add-outputs/show-outputs.png)

1. Áttekintheti a sablont.

   ![Sablon megjelenítése](./media/template-tutorial-add-outputs/show-template.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha továbblép a következő oktatóanyagra, nem kell törölnie az erőforráscsoportot.

Ha most leáll, érdemes lehet törölni a telepített erőforrásokat az erőforráscsoport törlésével.

1. A Azure Portal válassza ki a bal oldali menüből az **erőforráscsoportot** .
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. Válassza az **erőforráscsoport törlése** lehetőséget a felső menüben.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban hozzáadott egy visszatérési értéket a sablonhoz. A következő oktatóanyagban megtudhatja, hogyan exportálhat sablont, és hogyan használhatja az exportált sablon részeit a sablonban.

> [!div class="nextstepaction"]
> [Exportált sablon használata](template-tutorial-export-template.md)
