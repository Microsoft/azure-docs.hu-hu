---
title: Oktatóanyag – Címkék hozzáadása a Azure Resource Manager bicep fájlban lévő erőforrásokhoz
description: Címkék hozzáadása a bicep-fájlokban üzembe helyezett erőforrásokhoz. Címkék lehetővé teszik az erőforrások logikai rendszerezését.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: ea5e078eb692d002b3f86cd43663dd042d692611
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "102632602"
---
# <a name="tutorial-add-tags-in-azure-resource-manager-bicep-files"></a>Oktatóanyag: Címkék hozzáadása Azure Resource Manager bicep-fájlokban

Ebből az oktatóanyagból megtudhatja, hogyan adhat hozzá címkéket a bicep-fájlokban lévő erőforrásokhoz. A [címkék](../management/tag-resources.md) segítségével logikailag rendszerezheti az erőforrásokat. A címke értékei a Cost-jelentésekben jelennek meg. Az oktatóanyag elvégzése **8 percet** vesz igénybe.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

Javasoljuk, hogy fejezze be a gyors üzembe helyezési [sablonokkal kapcsolatos oktatóanyagot](bicep-tutorial-quickstart-template.md), de ez nem kötelező.

A bicep kiterjesztéssel rendelkező Visual Studio Code-nak, valamint Azure PowerShell vagy Azure CLI-nek kell lennie. További információ: [bicep Tools](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>A bicep-fájl áttekintése

Az előző bicep-fájl üzembe helyezte a Storage-fiókot, a App Service-csomagot és a webalkalmazást.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.bicep":::

Az erőforrások telepítése után lehetséges, hogy nyomon kell követnie a költségeket, és meg kell keresnie egy kategóriához tartozó erőforrásokat. A problémák megoldásához címkéket adhat hozzá.

## <a name="add-tags"></a>Címkék hozzáadása

Az erőforráscímkékkel az erőforrások felhasználási célját azonosító értékeket adhat meg. Hozzáadhat például olyan címkéket, amelyek felsorolják a környezetet és a projektet. Hozzáadhat olyan címkéket, amelyek azonosítják a Cost centert vagy az erőforrást birtokló csapatot. Adjon hozzá olyan értékeket, amelyek a szervezet szempontjából jelentéssel bírnak.

A következő példa a bicep-fájl módosításait mutatja be. Másolja a teljes fájlt, és cserélje le a bicep-fájlt annak tartalmára.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.bicep" range="1-81" highlight="27-30,38,51,71":::

## <a name="deploy-bicep-file"></a>Bicep-fájl üzembe helyezése

Itt az ideje, hogy üzembe helyezi a bicep-fájlt, és tekintse meg az eredményeket.

Ha még nem hozta létre az erőforráscsoportot, tekintse meg az [erőforráscsoport létrehozása](bicep-tutorial-create-first-bicep.md#create-resource-group)című témakört. A példa feltételezi `bicepFile` , hogy a változót a bicep-fájl elérési útjára állította, ahogy az az [első oktatóanyagban](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)is látható.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A központi telepítési parancsmag futtatásához a Azure PowerShell [legújabb verziójára](/powershell/azure/install-az-ps) van szükség.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addtags `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az üzembe helyezési parancs futtatásához az [Azure CLI](/cli/azure/install-azure-cli) legújabb verziójára van szükség.

```azurecli
az deployment group create \
  --name addtags \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> Ha a telepítés nem sikerült, a `verbose` kapcsolóval kérheti le a létrehozott erőforrásokra vonatkozó információkat. A `debug` kapcsoló használatával további információkat kaphat a hibakereséshez.

## <a name="verify-deployment"></a>Az üzembe helyezés ellenőrzése

A központi telepítés ellenőrzéséhez tekintse meg az erőforráscsoportot a Azure Portalból.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldali menüben válassza az **erőforráscsoportok** lehetőséget.
1. Válassza ki azt az erőforráscsoportot, amelyet központilag telepített.
1. Válasszon ki egy erőforrást, például a Storage-fiók erőforrását. Láthatja, hogy most már rendelkezik címkékkel.

   ![Címkék megjelenítése](./media/bicep-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha továbblép a következő oktatóanyagra, nem kell törölnie az erőforráscsoportot.

Ha most leáll, érdemes lehet törölni a telepített erőforrásokat az erőforráscsoport törlésével.

1. A Azure Portal válassza ki a bal oldali menüből az **erőforráscsoportot** .
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. Válassza az **erőforráscsoport törlése** lehetőséget a felső menüben.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban címkéket adott hozzá az erőforrásokhoz. A következő oktatóanyagban megtudhatja, hogyan használhatók a paraméterek, hogy egyszerűbbé váljon az értékek átadása az üzembe helyezéshez.

> [!div class="nextstepaction"]
> [Paraméter fájljának használata](bicep-tutorial-use-parameter-file.md)
