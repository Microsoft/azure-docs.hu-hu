---
title: Oktatóanyag – változó hozzáadása Azure Resource Manager bicep-fájlhoz
description: Adja hozzá a változókat a bicep-fájlhoz a szintaxis egyszerűsítése érdekében.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: da2755c1f2c0f9fa891fe1a99b1fed21f64492c8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632475"
---
# <a name="tutorial-add-variables-to-azure-resource-manager-bicep-file"></a>Oktatóanyag: változók hozzáadása Azure Resource Manager bicep-fájlhoz

Ebből az oktatóanyagból megtudhatja, hogyan adhat hozzá változót a bicep-fájlhoz. A változók egyszerűbbé teszik a bicep-fájlok használatát azáltal, hogy egy kifejezést egyszer kell írni, és újra fel kell használni a bicep-fájl során. Az oktatóanyag elvégzése **7 percet** vesz igénybe.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

Javasoljuk, hogy fejezze be a [functions oktatóanyagot](bicep-tutorial-add-functions.md), de ez nem kötelező.

A bicep kiterjesztéssel rendelkező Visual Studio Code-nak, valamint Azure PowerShell vagy Azure CLI-nek kell lennie. További információ: [bicep Tools](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>A bicep-fájl áttekintése

Az előző oktatóanyag végén a bicep-fájl a következő tartalmakat használta:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.bicep":::

A Storage-fiók nevének paramétere nem használható, mert egyedi nevet kell megadni. Ha elvégezte a sorozat korábbi oktatóanyagait, akkor valószínűleg egy egyedi nevet is kitalál. Ezt a problémát úgy oldja meg, hogy hozzáad egy változót, amely egyedi nevet hoz létre a Storage-fiókhoz.

## <a name="use-variable"></a>Változó használata

Az alábbi példa bemutatja, hogyan adhat hozzá egy változót a bicep-fájlhoz, amely egyedi Storage-fióknevet hoz létre. Másolja a teljes fájlt, és cserélje le a bicep-fájlt annak tartalmára.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.bicep" range="1-31" highlight="1-3,19,22":::

Figyelje meg, hogy tartalmaz egy nevű változót `uniqueStorageName` . Ez a változó három függvényt használ egy karakterlánc-érték létrehozásához.

Már ismeri a [resourceGroup](template-functions-resource.md#resourcegroup) függvényt. Ebben az esetben a tulajdonság helyett a `id` tulajdonságot kapja meg `location` , ahogy az az előző oktatóanyagban is látható. A `id` tulajdonság az erőforráscsoport teljes azonosítóját adja vissza, az előfizetés azonosítóját és az erőforráscsoport nevét is beleértve.

A [uniqueString](template-functions-string.md#uniquestring) függvény egy 13 karakterből álló kivonatoló értéket hoz létre. A visszaadott értéket a megadott paraméterek határozzák meg. Ebben az oktatóanyagban az erőforráscsoport AZONOSÍTÓját használja a kivonat értékének bemenetként. Ez azt jelenti, hogy ezt a bicep-fájlt különböző erőforráscsoporthoz helyezheti üzembe, és egy másik egyedi karakterláncot is beszerezhet. Ugyanakkor ugyanezt az értéket kell megkapnia, ha ugyanarra az erőforrás-csoportra telepíti.

A bicep egy [karakterlánc-interpolációs](https://en.wikipedia.org/wiki/String_interpolation#) szintaxist támogat. Ehhez a változóhoz a rendszer a paraméterből és a függvényből származó karakterláncot veszi át `uniqueString` , és egyetlen karakterlánccá egyesíti őket.

A `storagePrefix` paraméter lehetővé teszi, hogy olyan előtagot adjon át, amely segítséget nyújt a Storage-fiókok azonosításában. Létrehozhat egy saját elnevezési konvenciót, amely megkönnyíti a tárolási fiókok azonosítását az erőforrások hosszú listájáról való üzembe helyezés után.

Végezetül figyelje meg, hogy a tároló neve már egy paraméter helyett a változóra van beállítva.

## <a name="deploy-bicep-file"></a>Bicep-fájl üzembe helyezése

Helyezzük üzembe a bicep fájlt. A bicep-fájl üzembe helyezése egyszerűbb, mint az előző bicep-fájlok, mert csak a tároló nevének előtagját adja meg.

Ha még nem hozta létre az erőforráscsoportot, tekintse meg az [erőforráscsoport létrehozása](bicep-tutorial-create-first-bicep.md#create-resource-group)című témakört. A példa feltételezi `bicepFile` , hogy a változót a bicep-fájl elérési útjára állította, ahogy az az [első oktatóanyagban](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)is látható.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A központi telepítési parancsmag futtatásához a Azure PowerShell [legújabb verziójára](/powershell/azure/install-az-ps) van szükség.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnamevariable `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az üzembe helyezési parancs futtatásához az [Azure CLI](/cli/azure/install-azure-cli) legújabb verziójára van szükség.

```azurecli
az deployment group create \
  --name addnamevariable \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

> [!NOTE]
> Ha a telepítés nem sikerült, a `verbose` kapcsolóval kérheti le a létrehozott erőforrásokra vonatkozó információkat. A `debug` kapcsoló használatával további információkat kaphat a hibakereséshez.

## <a name="verify-deployment"></a>Az üzembe helyezés ellenőrzése

A központi telepítés ellenőrzéséhez tekintse meg az erőforráscsoportot a Azure Portalból.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldali menüben válassza az **erőforráscsoportok** lehetőséget.
1. Válassza ki azt az erőforráscsoportot, amelyet központilag telepített.
1. Láthatja, hogy a Storage-fiók erőforrása telepítve van. A Storage-fiók **neve és egy** véletlenszerű karakterből álló karakterlánc.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha továbblép a következő oktatóanyagra, nem kell törölnie az erőforráscsoportot.

Ha most leáll, érdemes lehet törölni a telepített erőforrásokat az erőforráscsoport törlésével.

1. A Azure Portal válassza ki a bal oldali menüből az **erőforráscsoportot** .
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. Válassza az **erőforráscsoport törlése** lehetőséget a felső menüben.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy olyan változót adott hozzá, amely egyedi nevet hoz létre egy Storage-fiókhoz. A következő oktatóanyagban egy értéket ad vissza a központilag telepített Storage-fiókból.

> [!div class="nextstepaction"]
> [Kimenetek hozzáadása](bicep-tutorial-add-outputs.md)
