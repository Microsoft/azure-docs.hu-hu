---
title: Oktatóanyag – JSON-sablon exportálása a Azure Portal for bicep fejlesztéshez
description: Megtudhatja, hogyan használható egy exportált JSON-sablon a bicep-fejlesztés befejezéséhez.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 3bc7ed4ada4f7810e9864778c7f76a0573c9dc89
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102632549"
---
# <a name="tutorial-use-exported-json-template-from-the-azure-portal"></a>Oktatóanyag: az exportált JSON-sablon használata a Azure Portal

Ebben az oktatóanyag-sorozatban létrehozott egy bicep-fájlt egy Azure Storage-fiók üzembe helyezéséhez. A következő két oktatóanyagban hozzá kell adnia egy *app Service csomagot* és egy *webhelyet*. A sablonok nem hozhatók létre a semmiből, megtudhatja, hogyan exportálhat JSON-sablonokat a Azure Portalból, és hogyan használhatja a mintákat az Azure-beli [Gyorsindítás sablonokból](https://azure.microsoft.com/resources/templates/). Ezeket a sablonokat saját használatra testreszabhatja. Ez az oktatóanyag a sablonok exportálására és a bicep-fájl eredményének testre szabására koncentrál. A művelet elvégzése körülbelül **14 percet** vesz igénybe.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

Javasoljuk, hogy fejezze be a [kimenetekkel kapcsolatos oktatóanyagot](bicep-tutorial-add-outputs.md), de ez nem kötelező.

A bicep kiterjesztéssel rendelkező Visual Studio Code-nak, valamint Azure PowerShell vagy Azure CLI-nek kell lennie. További információ: [bicep Tools](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>A bicep-fájl áttekintése

Az előző oktatóanyag végén a bicep-fájl a következő tartalmakat használta:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.bicep":::

Ez a bicep-fájl jól működik a Storage-fiókok üzembe helyezése során, de lehet, hogy további erőforrásokat szeretne hozzáadni hozzá. A JSON-sablonokat egy meglévő erőforrásból exportálhatja, így gyorsan lekérheti a JSON-t az adott erőforráshoz. Majd alakítsa át a JSON-t a bicep, mielőtt hozzáadja a bicep-fájlhoz.

## <a name="create-app-service-plan"></a>App Service-csomag létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza az **Erőforrás létrehozása** lehetőséget.
1. A **Keresés a piactéren** mezőbe írja be **app Service csomagot**, majd válassza a **app Service tervet**.  Ne válassza ki a **app Service csomagot (klasszikus)**
1. Válassza a **Létrehozás** lehetőséget.
1. Adja meg a következőt:

    - **Előfizetés**: válassza ki az Azure-előfizetését.
    - **Erőforráscsoport**: válassza az **új létrehozása** lehetőséget, majd adjon meg egy nevet. Adjon meg egy másik erőforráscsoport-nevet, mint amelyet a jelen oktatóanyag-sorozatban használt.
    - **Név**: adja meg az App Service-csomag nevét.
    - **Operációs rendszer**: válassza a **Linux** lehetőséget.
    - **Régió**: válasszon ki egy Azure-helyet. Például: **USA középső régiója**.
    - **Árképzési szint**: a költségek megtakarításához módosítsa a SKU-t az **alapszintű B1** -re (fejlesztés/tesztelés).

    ![Resource Manager-sablon exportálása sablon portál](./media/bicep-tutorial-export-template/resource-manager-template-export.png)
1. Válassza **a felülvizsgálat és létrehozás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget. Az erőforrás létrehozása néhány percet vesz igénybe.

## <a name="export-template"></a>Sablon exportálása

Jelenleg a Azure Portal csak a JSON-sablonok exportálását támogatja. Vannak olyan eszközök, amelyekkel defordíthatók a JSON-sablonok a bicep fájlokba.

1. Válassza az **Erőforrás megnyitása** lehetőséget.

    ![Erőforrás megnyitása](./media/bicep-tutorial-export-template/resource-manager-template-export-go-to-resource.png)

1. Válassza a **sablon exportálása** lehetőséget.

    ![Resource Manager-sablon exportálása sablon](./media/bicep-tutorial-export-template/resource-manager-template-export-template.png)

   A sablon exportálása funkció egy erőforrás aktuális állapotát veszi alapul, és létrehoz egy sablont az üzembe helyezéséhez. A sablon exportálása hasznos lehet a JSON gyors beszerzéséhez, amely az erőforrások üzembe helyezéséhez szükséges.

1. A `Microsoft.Web/serverfarms` definíció és a paraméter definíciója a szükséges részek.

    ![Resource Manager-sablon exportálása sablon exportált sablon](./media/bicep-tutorial-export-template/resource-manager-template-exported-template.png)

    > [!IMPORTANT]
    > Az exportált sablon általában részletesebb, mint a sablon létrehozásakor. Az exportált sablon SKU objektuma például öt tulajdonsággal rendelkezik. Ez a sablon működik, de csak használhatja a `name` tulajdonságot. Elindíthatja az exportált sablont, majd módosíthatja azt úgy, hogy illeszkedjen az igényeinek megfelelően.

1. Válassza a **Letöltés** lehetőséget.  A letöltött zip-fájl tartalmaz egy **template.js** és egy **parameters.js**. Bontsa ki a fájlokat.
1. Tallózással keresse meg a (z **https://bicepdemo.z22.web.core.windows.net/** ) elemet, válassza ki a **defordítás** lehetőséget, majd nyissa meg **template.json**. A sablont a bicep-ban szerezheti be.

## <a name="revise-existing-bicep-file"></a>Meglévő bicep-fájl módosítása

A decomplied exportált sablon a szükséges bicep nagy részét biztosítja, de testre kell szabnia a bicep-fájlhoz. Fordítson különös figyelmet a bicep-fájl és az exportált bicep-fájl paramétereinek és változóinak különbségére. Az exportálási folyamat természetesen nem ismeri a bicep fájlban már definiált paramétereket és változókat.

A következő példában a bicep-fájl kiegészítései láthatók. Az exportált kódot és néhány módosítást is tartalmaz. Először is megváltoztatja a paraméter nevét, hogy az megfeleljen az elnevezési konvenciónak. Másodszor, a Location paramétert használja az App Service-csomag helyéhez. Harmadszor, eltávolítja azokat a tulajdonságokat, amelyekben az alapértelmezett érték rendben van.

Másolja a teljes fájlt, és cserélje le a bicep-fájlt annak tartalmára.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.bicep" range="1-53" highlight="18,34-51":::

## <a name="deploy-bicep-file"></a>Bicep-fájl üzembe helyezése

Az Azure CLI vagy a Azure PowerShell használatával helyezzen üzembe egy bicep-fájlt.

Ha még nem hozta létre az erőforráscsoportot, tekintse meg az [erőforráscsoport létrehozása](bicep-tutorial-create-first-bicep.md#create-resource-group)című témakört. A példa feltételezi `bicepFile` , hogy a változót a bicep-fájl elérési útjára állította, ahogy az az [első oktatóanyagban](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)is látható.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A központi telepítési parancsmag futtatásához a Azure PowerShell [legújabb verziójára](/powershell/azure/install-az-ps) van szükség.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addappserviceplan `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az üzembe helyezési parancs futtatásához az [Azure CLI](/cli/azure/install-azure-cli) legújabb verziójára van szükség.

```azurecli
az deployment group create \
  --name addappserviceplan \
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
1. Az erőforráscsoport egy Storage-fiókot és egy App Service tervet tartalmaz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha továbblép a következő oktatóanyagra, nem kell törölnie az erőforráscsoportot.

Ha most leáll, érdemes lehet törölni a telepített erőforrásokat az erőforráscsoport törlésével.

1. A Azure Portal válassza ki a bal oldali menüből az **erőforráscsoportot** .
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. Válassza az **erőforráscsoport törlése** lehetőséget a felső menüben.

## <a name="next-steps"></a>Következő lépések

Megtanulta, hogyan exportálhat egy JSON-sablont a Azure Portalból, hogyan alakíthatja át a JSON-sablont egy bicep-fájlba, és hogyan használhatja az exportált sablont a bicep-fejlesztéshez. Az Azure rövid útmutató sablonjait is használhatja a bicep-fejlesztés egyszerűsítéséhez.

> [!div class="nextstepaction"]
> [Az Azure Gyorsindítás sablonjainak használata](bicep-tutorial-quickstart-template.md)
