---
title: Oktatóanyag – paraméterek hozzáadása a sablonhoz
description: Adja hozzá a paramétereket a Azure Resource Manager sablonhoz (ARM-sablonhoz), hogy az újrafelhasználható legyen.
author: mumian
ms.date: 03/31/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: e983f8499cbeaf400a8da6f48d7f6c8b75c4795a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97107062"
---
# <a name="tutorial-add-parameters-to-your-arm-template"></a>Oktatóanyag: paraméterek hozzáadása az ARM-sablonhoz

Az [előző oktatóanyagban](template-tutorial-add-resource.md)megtanulta, hogyan adhat hozzá Storage-fiókot a sablonhoz, és hogyan telepítheti azt. Ebből az oktatóanyagból megtudhatja, hogyan fejlesztheti a Azure Resource Manager sablont (ARM-sablon) paraméterek hozzáadásával. Ez az oktatóanyag körülbelül **14 percet** vesz igénybe.

## <a name="prerequisites"></a>Előfeltételek

Javasoljuk, hogy fejezze be az [erőforrásokkal kapcsolatos oktatóanyagot](template-tutorial-add-resource.md), de nem szükséges.

A Visual Studio Code-nak rendelkeznie kell a Resource Manager-eszközök bővítménnyel, valamint Azure PowerShell vagy az Azure CLI-vel. További információ: [sablon eszközei](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Sablon áttekintése

Az előző oktatóanyag végén a sablon a következő JSON-t használta:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json":::

Előfordulhat, hogy észrevette, hogy probléma merült fel ezzel a sablonnal. A tárfiók neve rögzített. Ezt a sablont csak akkor használhatja, ha minden alkalommal ugyanazt a tárfiókot helyezi üzembe. Egy másik névvel rendelkező Storage-fiók üzembe helyezéséhez létre kell hoznia egy új sablont, ami nyilvánvalóan nem praktikus módszer az üzemelő példányok automatizálására.

## <a name="make-template-reusable"></a>Sablon újrafelhasználhatóvé tétele

A sablon újrafelhasználhatóvé tételéhez vegyünk fel egy paramétert, amely segítségével átadható a Storage-fiók neve. A következő példában szereplő JSON-fájl azt mutatja, hogy mi változott a sablonban. A `storageName` paraméter karakterláncként van azonosítva. A maximális hossz 24 karakter, ami megakadályozza a túl hosszú nevek megadását.

Másolja a teljes fájlt, és cserélje le a sablont a tartalmára.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.json" range="1-26" highlight="4-10,15":::

## <a name="deploy-template"></a>Sablon üzembe helyezése

Végezzük el a sablon üzembe helyezését. Az alábbi példa telepíti a sablont az Azure CLI vagy a PowerShell használatával. Figyelje meg, hogy a Storage-fiók nevét a telepítési parancs egyik értékeként adja meg. A Storage-fiók neve mezőben adja meg az előző oktatóanyagban használt nevet.

Ha még nem hozta létre az erőforráscsoportot, tekintse meg az [erőforráscsoport létrehozása](template-tutorial-create-first-template.md#create-resource-group)című témakört. A példa feltételezi `templateFile` , hogy a változót a sablonfájl elérési útjára állította, ahogy az [első oktatóanyagban](template-tutorial-create-first-template.md#deploy-template)is látható.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az üzembe helyezési parancs futtatásához az [Azure CLI](/cli/azure/install-azure-cli) legújabb verziójára van szükség.

```azurecli
az deployment group create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>Az erőforrás-frissítések ismertetése

Az előző szakaszban a korábban létrehozott nevű Storage-fiókot telepítette. Előfordulhat, hogy kíváncsi, hogy az erőforrás milyen hatással van az újratelepítésre.

Ha az erőforrás már létezik, és a tulajdonságokban nem észlelhető változás, a rendszer nem végez további műveleteket. Ha az erőforrás már létezik, és egy tulajdonság módosult, az erőforrás frissül. Ha az erőforrás nem létezik, akkor a rendszer létrehozza.

A frissítések kezelési módja azt jelenti, hogy a sablon tartalmazhatja az Azure-megoldáshoz szükséges összes erőforrást. Biztonságosan újra üzembe helyezheti a sablont, és biztos lehet benne, hogy az erőforrások módosulnak, vagy csak szükség esetén jönnek létre. Ha például hozzáadta a fájlokat a Storage-fiókjához, akkor a fájlok elvesztése nélkül újra üzembe helyezheti a Storage-fiókot.

## <a name="customize-by-environment"></a>Testreszabás környezet szerint

Paraméterek megadásával testreszabhatja az üzemelő példányt úgy, hogy az adott környezetnek megfelelő értékeket ad meg. Például különböző értékeket adhat át, attól függően, hogy fejlesztési, tesztelési és éles környezetben végzi-e a telepítést.

Az előző sablon mindig **Standard_LRS** Storage-fiókot telepített. A környezettől függően előfordulhat, hogy a rugalmasságot a különböző SKU-ket érdemes telepíteni. Az alábbi példa bemutatja, hogyan adhat hozzá egy paramétert az SKU-hoz. Másolja a teljes fájlt, és illessze be a sablont.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json" range="1-40" highlight="10-23,32":::

A `storageSKU` paraméter alapértelmezett értéke. Ezt az értéket akkor kell használni, ha nincs megadva érték az üzemelő példányban. Emellett az engedélyezett értékek listája is szerepel. Ezek az értékek megegyeznek a Storage-fiók létrehozásához szükséges értékekkel. Nem szeretné, hogy a sablon felhasználói ne adjanak át olyan SKU-ket, amelyek nem működnek.

## <a name="redeploy-template"></a>Sablon ismételt üzembe helyezése

Most már készen áll a telepítésre. Mivel az alapértelmezett SKU a **Standard_LRS** értékre van állítva, nem kell megadnia a paraméter értékét.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

> [!NOTE]
> Ha a telepítés nem sikerült, a `verbose` kapcsolóval kérheti le a létrehozott erőforrásokra vonatkozó információkat. A `debug` kapcsoló használatával további információkat kaphat a hibakereséshez.

A sablon rugalmasságának megtekintéséhez próbálkozzon újra a telepítéssel. Ezúttal állítsa az SKU paramétert **Standard_GRSra**. Átadhat egy új nevet egy másik Storage-fiók létrehozásához, vagy használhatja ugyanazt a nevet a meglévő Storage-fiók frissítéséhez. Mindkét beállítás működik.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usenondefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name usenondefaultsku \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

Végül futtasson még egy tesztet, és nézze meg, mi történik, ha olyan SKU-t továbbít, amely nem a megengedett értékek egyike. Ebben az esetben teszteljük azt a forgatókönyvet, amelyben a sablon felhasználója azt gondolja, hogy az **alapszintű** az egyik SKU.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name testskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
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

Javította az [első oktatóanyagban](template-tutorial-create-first-template.md) létrehozott sablont paraméterek hozzáadásával. A következő oktatóanyagban megismerheti a sablon funkcióit.

> [!div class="nextstepaction"]
> [Sablon funkcióinak hozzáadása](template-tutorial-add-functions.md)
