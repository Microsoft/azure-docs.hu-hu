---
title: Oktatóanyag – sablon exportálása a Azure Portalból
description: Megtudhatja, hogyan használhatja az exportált sablont a sablon fejlesztésének befejezéséhez.
author: mumian
ms.date: 09/09/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: ba1797da5a78eeebd25f5df1b6e37eb92470f584
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97106920"
---
# <a name="tutorial-use-exported-template-from-the-azure-portal"></a>Oktatóanyag: az exportált sablon használata a Azure Portal

Ebben az oktatóanyag-sorozatban létrehozott egy sablont egy Azure Storage-fiók üzembe helyezéséhez. A következő két oktatóanyagban hozzá kell adnia egy *app Service csomagot* és egy *webhelyet*. A sablonok a semmiből való létrehozása helyett megtudhatja, hogyan exportálhat sablonokat a Azure Portalból, és hogyan használhatók a sablonok az Azure-beli [Gyorsindítás sablonokból](https://azure.microsoft.com/resources/templates/). Ezeket a sablonokat saját használatra testreszabhatja. Ez az oktatóanyag a sablonok exportálására, valamint a sablon eredményének testreszabására koncentrál. A művelet elvégzése körülbelül **14 percet** vesz igénybe.

## <a name="prerequisites"></a>Előfeltételek

Javasoljuk, hogy fejezze be a [kimenetekkel kapcsolatos oktatóanyagot](template-tutorial-add-outputs.md), de ez nem kötelező.

A Visual Studio Code-nak rendelkeznie kell a Resource Manager-eszközök bővítménnyel, valamint Azure PowerShell vagy az Azure CLI-vel. További információ: [sablon eszközei](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Sablon áttekintése

Az előző oktatóanyag végén a sablon a következő JSON-t használta:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json":::

Ez a sablon jól működik a Storage-fiókok telepítéséhez, de érdemes lehet további erőforrásokat hozzáadni hozzá. Egy meglévő erőforrásból is exportálhat sablonokat, így gyorsan lekérheti a JSON-t az adott erőforráshoz.

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

    ![Resource Manager-sablon exportálása sablon portál](./media/template-tutorial-export-template/resource-manager-template-export.png)
1. Válassza **a felülvizsgálat és létrehozás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget. Az erőforrás létrehozása néhány percet vesz igénybe.

## <a name="export-template"></a>Sablon exportálása

1. Válassza az **Erőforrás megnyitása** lehetőséget.

    ![Erőforrás megnyitása](./media/template-tutorial-export-template/resource-manager-template-export-go-to-resource.png)

1. Válassza a **sablon exportálása** lehetőséget.

    ![Resource Manager-sablon exportálása sablon](./media/template-tutorial-export-template/resource-manager-template-export-template.png)

   A sablon exportálása funkció egy erőforrás aktuális állapotát veszi alapul, és létrehoz egy sablont az üzembe helyezéséhez. A sablon exportálása hasznos lehet a JSON gyors beszerzéséhez, amely az erőforrások üzembe helyezéséhez szükséges.

1. Tekintse meg a `Microsoft.Web/serverfarms` definíciót és a paraméter definícióját az exportált sablonban. Ezeket a szakaszt nem kell átmásolnia. Ezt az exportált sablont használhatja példaként arra, hogyan szeretné hozzáadni ezt az erőforrást a sablonhoz.

    ![Resource Manager-sablon exportálása sablon exportált sablon](./media/template-tutorial-export-template/resource-manager-template-exported-template.png)

> [!IMPORTANT]
> Az exportált sablon általában részletesebb, mint a sablon létrehozásakor. Az exportált sablon SKU objektuma például öt tulajdonsággal rendelkezik. Ez a sablon működik, de csak használhatja a `name` tulajdonságot. Elindíthatja az exportált sablont, majd módosíthatja azt úgy, hogy illeszkedjen az igényeinek megfelelően.

## <a name="revise-existing-template"></a>Meglévő sablon módosítása

Az exportált sablon lehetővé teszi a legtöbb szükséges JSON-t, de testre kell szabnia azt a sablonhoz. Fordítson különös figyelmet a sablon és az exportált sablon közötti paraméterek és változók különbségére. Az exportálási folyamat természetesen nem ismeri a sablonban már definiált paramétereket és változókat.

Az alábbi példa kiemeli a sablon kiegészítéseit. Az exportált kódot és néhány módosítást is tartalmaz. Először is megváltoztatja a paraméter nevét, hogy az megfeleljen az elnevezési konvenciónak. Másodszor, a Location paramétert használja az App Service-csomag helyéhez. Harmadszor, eltávolítja azokat a tulajdonságokat, amelyekben az alapértelmezett érték rendben van.

Másolja a teljes fájlt, és cserélje le a sablont a tartalmára.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json" range="1-77" highlight="28-31,50-69":::

## <a name="deploy-template"></a>Sablon üzembe helyezése

Sablon üzembe helyezéséhez használja az Azure CLI-t vagy a Azure PowerShell-t.

Ha még nem hozta létre az erőforráscsoportot, tekintse meg az [erőforráscsoport létrehozása](template-tutorial-create-first-template.md#create-resource-group)című témakört. A példa feltételezi `templateFile` , hogy a változót a sablonfájl elérési útjára állította, ahogy az [első oktatóanyagban](template-tutorial-create-first-template.md#deploy-template)is látható.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addappserviceplan `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az üzembe helyezési parancs futtatásához az [Azure CLI](/cli/azure/install-azure-cli) legújabb verziójára van szükség.

```azurecli
az deployment group create \
  --name addappserviceplan \
  --resource-group myResourceGroup \
  --template-file $templateFile \
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

Megtanulta, hogyan exportálhat sablont a Azure Portalból, és hogyan használhatja az exportált sablont a sablon fejlesztéséhez. Az Azure rövid útmutató sablonjait is használhatja a sablonok fejlesztésének egyszerűbbé tételéhez.

> [!div class="nextstepaction"]
> [Az Azure Gyorsindítás sablonjainak használata](template-tutorial-quickstart-template.md)
