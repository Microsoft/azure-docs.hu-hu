---
title: Oktatóanyag – paraméterek hozzáadása Azure Resource Manager bicep-fájlhoz
description: Adjon hozzá paramétereket a bicep-fájlhoz, hogy újrafelhasználható legyen.
author: mumian
ms.date: 03/01/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 603aa8f8bdb8136f4418d8f9a77bb40ec39243c0
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748209"
---
# <a name="tutorial-add-parameters-to-azure-resource-manager-bicep-file"></a>Oktatóanyag: paraméterek hozzáadása Azure Resource Manager bicep-fájlhoz

Az [előző oktatóanyagban](bicep-tutorial-create-first-bicep.md)megtanulta, hogyan helyezhet üzembe egy Storage-fiókot. Ebből az oktatóanyagból megtudhatja, hogyan fejlesztheti a bicep-fájlt paraméterek hozzáadásával. Ez az oktatóanyag körülbelül **14 percet** vesz igénybe.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

Javasoljuk, hogy végezze el az [első bicep-fájl létrehozását](bicep-tutorial-create-first-bicep.md), de ez nem kötelező.

A bicep kiterjesztéssel rendelkező Visual Studio Code-nak, valamint Azure PowerShell vagy Azure CLI-nek kell lennie. További információ: [bicep Tools](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>A bicep-fájl áttekintése

Az előző oktatóanyag végén a bicep így néz ki:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.bicep":::

Előfordulhat, hogy észrevette, hogy probléma merült fel a bicep-fájllal kapcsolatban. A tárfiók neve rögzített. Ezt a bicep-fájlt csak akkor használhatja, ha minden alkalommal üzembe helyezi ugyanazt a Storage-fiókot. Egy másik névvel rendelkező Storage-fiók üzembe helyezéséhez létre kell hoznia egy új bicep-fájlt, ami nyilvánvalóan nem praktikus módszer az üzemelő példányok automatizálására.

## <a name="make-bicep-file-reusable"></a>A bicep-fájl újrafelhasználhatóvé tétele

A bicep-fájl újrafelhasználhatóvé tételéhez vegyünk fel egy paramétert, amely segítségével átadható a Storage-fiók neve. Az alábbi példában látható bicep a következő példában a fájl változásait mutatja be. A `storageName` paraméter karakterláncként van azonosítva. A maximális hossz 24 karakter, ami megakadályozza a túl hosszú nevek megadását.

Másolja a teljes fájlt, és cserélje le a következő tartalomra.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.bicep" range="1-15" highlight="1-3,6":::

Figyelje meg, hogy a paraméterek közvetlenül is szerepelhetnek a bicep felhasznált nevükkel, az ARM JSON-sablon [parameters (' storageName ')] megkövetelésével szemben.

## <a name="deploy-bicep-file"></a>Bicep-fájl üzembe helyezése

Helyezzük üzembe a bicep fájlt. A következő példa a bicep-fájlt telepíti az Azure CLI vagy a PowerShell használatával. Figyelje meg, hogy a Storage-fiók nevét a telepítési parancs egyik értékeként adja meg. A Storage-fiók neve mezőben adja meg az előző oktatóanyagban használt nevet.

Ha még nem hozta létre az erőforráscsoportot, tekintse meg az [erőforráscsoport létrehozása](bicep-tutorial-create-first-bicep.md#create-resource-group)című témakört. A példa feltételezi `bicepFile` , hogy a változót a bicep-fájl elérési útjára állította, ahogy az az [első oktatóanyagban](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)is látható.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A központi telepítési parancsmag futtatásához a Azure PowerShell [legújabb verziójára](/powershell/azure/install-az-ps) van szükség.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az üzembe helyezési parancs futtatásához az [Azure CLI](/cli/azure/install-azure-cli) legújabb verziójára van szükség.

```azurecli
az deployment group create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>Az erőforrás-frissítések ismertetése

Az előző szakaszban a korábban létrehozott nevű Storage-fiókot telepítette. Előfordulhat, hogy kíváncsi, hogy az erőforrás milyen hatással van az újratelepítésre.

Ha az erőforrás már létezik, és a tulajdonságokban nem észlelhető változás, a rendszer nem végez további műveleteket. Ha az erőforrás már létezik, és egy tulajdonság módosult, az erőforrás frissül. Ha az erőforrás nem létezik, akkor a rendszer létrehozza.

A frissítések kezelési módja azt jelenti, hogy a bicep-fájl tartalmazza az Azure-megoldáshoz szükséges összes erőforrást. Biztonságosan újratelepítheti a bicep-fájlt, és megtudhatja, hogy az erőforrások csak szükség esetén változnak vagy jönnek létre. Ha például hozzáadta a fájlokat a Storage-fiókjához, akkor a fájlok elvesztése nélkül újra üzembe helyezheti a Storage-fiókot.

## <a name="customize-by-environment"></a>Testreszabás környezet szerint

Paraméterek megadásával testreszabhatja az üzemelő példányt úgy, hogy az adott környezetnek megfelelő értékeket ad meg. Például különböző értékeket adhat át, attól függően, hogy fejlesztési, tesztelési és éles környezetben végzi-e a telepítést.

Az előző bicep-fájl mindig **Standard_LRS** Storage-fiókot telepített. A környezettől függően előfordulhat, hogy a rugalmasságot a különböző SKU-ket érdemes telepíteni. Az alábbi példa bemutatja, hogyan adhat hozzá egy paramétert az SKU-hoz. Másolja a teljes fájlt, és illessze be a bicep-fájlt.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.bicep" range="1-27" highlight="5-15,21":::

A `storageSKU` paraméter alapértelmezett értéke. Ezt az értéket akkor kell használni, ha nincs megadva érték az üzemelő példányban. Emellett az engedélyezett értékek listája is szerepel. Ezek az értékek megegyeznek a Storage-fiók létrehozásához szükséges értékekkel. Nem szeretné, hogy a bicep-fájl felhasználói ne legyenek átadni a nem működő SKU-ket.

## <a name="redeploy-bicep-file"></a>A bicep-fájl újbóli üzembe helyezése

Most már készen áll a telepítésre. Mivel az alapértelmezett SKU a **Standard_LRS** értékre van állítva, nem kell megadnia a paraméter értékét.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageName={your-unique-name}
```

---

> [!NOTE]
> Ha a telepítés nem sikerült, a `verbose` kapcsolóval kérheti le a létrehozott erőforrásokra vonatkozó információkat. A `debug` kapcsoló használatával további információkat kaphat a hibakereséshez.

A bicep-fájl rugalmasságának megtekintéséhez próbálkozzon újra a telepítéssel. Ezúttal állítsa az SKU paramétert **Standard_GRSra**. Átadhat egy új nevet egy másik Storage-fiók létrehozásához, vagy használhatja ugyanazt a nevet a meglévő Storage-fiók frissítéséhez. Mindkét beállítás működik.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usenondefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name usenondefaultsku \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

Végül futtasson még egy tesztet, és nézze meg, mi történik, ha olyan SKU-t továbbít, amely nem a megengedett értékek egyike. Ebben az esetben teszteljük azt a forgatókönyvet, amelyben a bicep-fájl felhasználója úgy gondolja, hogy az **alapszintű** az egyik SKU.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name testskuparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageSKU=basic storageName={your-unique-name}
```

---

A parancs azonnali hibát jelez, amely jelzi, hogy mely értékek engedélyezettek. A Resource Manager a telepítés megkezdése előtt azonosítja a hibát.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha továbblép a következő oktatóanyagra, nem kell törölnie az erőforráscsoportot.

Ha most leáll, érdemes lehet törölni a telepített erőforrásokat az erőforráscsoport törlésével.

1. A Azure Portal válassza ki a bal oldali menüből az **erőforráscsoportot** .
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. Válassza az **erőforráscsoport törlése** lehetőséget a felső menüben.

## <a name="next-steps"></a>Következő lépések

A paraméterek hozzáadásával javította az [első oktatóanyagban](bicep-tutorial-create-first-bicep.md) létrehozott bicep fájlt. A következő oktatóanyagban megismerheti a bicep functions-t.

> [!div class="nextstepaction"]
> [Függvények hozzáadása](bicep-tutorial-add-functions.md)
