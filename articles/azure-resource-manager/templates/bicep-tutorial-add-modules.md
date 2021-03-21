---
title: Oktatóanyag – modulok hozzáadása Azure Resource Manager bicep-fájlhoz
description: Modulok használata a nyers erőforrás-deklaráció összetett részleteinek beágyazásához.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 6efd9c230df49c83adc17361082af85b0ef9edc5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102633138"
---
# <a name="tutorial-add-modules-to-azure-resource-manager-bicep-file"></a>Oktatóanyag: modulok hozzáadása Azure Resource Manager bicep-fájlhoz

Az [előző oktatóanyagban](bicep-tutorial-use-parameter-file.md)megtanulta, hogyan használhat egy paraméter-fájlt a bicep-fájl üzembe helyezéséhez. Ebből az oktatóanyagból megtudhatja, hogyan használhatja a bicep modulokat a nyers erőforrás-deklaráció összetett részleteinek beágyazásához. A modulok megoszthatók és újra felhasználhatók a megoldáson belül.  A művelet végrehajtása körülbelül **12 percet** vesz igénybe.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

Javasoljuk, hogy végezze el a [paraméterrel kapcsolatos oktatóanyag](bicep-tutorial-use-parameter-file.md)elvégzését, de ez nem kötelező.

A bicep kiterjesztéssel rendelkező Visual Studio Code-nak, valamint Azure PowerShell vagy Azure CLI-nek kell lennie. További információ: [bicep Tools](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>A bicep-fájl áttekintése

Az előző oktatóanyag végén a bicep-fájl a következő tartalmakat használta:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.bicep":::

Ez a bicep-fájl jól működik. Nagyobb megoldások esetében azonban a bicep-fájlt számos kapcsolódó modulban szeretné megszüntetni, így megoszthatja és újra használhatja ezeket a modulokat. A jelenlegi bicep-fájl egy Storage-fiókot, egy app Service-csomagot és egy webhelyet helyez üzembe.  Válassza szét a Storage-fiókot egy modulban.

## <a name="create-bicep-module"></a>Bicep-modul létrehozása

Minden bicep-fájl felhasználható modulként, így nincs specifikus szintaxis a modul definiálásához. Hozzon létre egy _Storage. bicep_ fájlt a következő tartalommal:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-module/storage.bicep":::

Ez a modul tartalmazza a Storage-fiók erőforrását, valamint a kapcsolódó paramétereket és változókat. A _Location_ paraméter és a _resourceTags_ paraméterek értékei el lettek távolítva. Ezek az értékek a fő bicep-fájlból lesznek átadva.

## <a name="consume-bicep-module"></a>A bicep modul felhasználása

Cserélje le a Storage-fiók erőforrás-definícióját a meglévő _azuredeploy. bicep_ fájlra a következő bicep-tartalommal:

```bicep
module stg './storage.bicep' = {
  name: 'storageDeploy'
  params: {
    storagePrefix: storagePrefix
    location: location
    resourceTags: resourceTags
  }
}
```

- **modul**: kulcsszó.
- **szimbolikus név** (STG): Ez a modul azonosítója.
- **modul-fájl**: ebben a példában a modul elérési útja relatív elérési úttal (./Storage.bicep) van megadva. A teljes munkafolyamaton belüli összes elérési utat meg kell adni a továbbítási perjel (/) címtár-elválasztó használatával, hogy biztosítsa a konzisztens fordítást a platformon. A Windows fordított perjel (a \) karakter nem támogatott.

A tárolási végpont lekéréséhez frissítse a _azuredeploy. bicep_ kimenetét a következő bicep:

```bicep
output storageEndpoint object = stg.outputs.storageEndpoint
```

A Completed azuredeploy. bicep a következő tartalmakat tartalmazta:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-module/azuredeploy.bicep":::

## <a name="deploy-template"></a>Sablon üzembe helyezése

A sablon üzembe helyezéséhez használja az Azure CLI-t vagy a Azure PowerShell.

Ha még nem hozta létre az erőforráscsoportot, tekintse meg az [erőforráscsoport létrehozása](bicep-tutorial-create-first-bicep.md#create-resource-group)című témakört. A példa feltételezi `bicepFile` , hogy a változót a bicep-fájl elérési útjára állította, ahogy az az [első oktatóanyagban](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)is látható.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A központi telepítési parancsmag futtatásához a Azure PowerShell [legújabb verziójára](/powershell/azure/install-az-ps) van szükség.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addmodule `
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
  --name addmodule \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
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

Gratulálunk, készen áll a bicep-fájlok Azure-ba történő üzembe helyezésére. Tudassa velünk, ha megjegyzésekkel és javaslatokkal rendelkezik a visszajelzések szakaszban. Köszönjük!

A következő oktatóanyag-Sorozat részletesebben ismerteti a sablonok üzembe helyezését.

> [!div class="nextstepaction"]
> [Modulok hozzáadása](./bicep-tutorial-add-modules.md)
