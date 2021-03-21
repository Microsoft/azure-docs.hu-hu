---
title: Oktatóanyag – gyorsindítási sablonok használata
description: Ismerje meg, hogyan végezheti el a sablonok fejlesztését az Azure Gyorsindítás sablonjaival.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 46b32ae7aeb971c9391a69e3ca3d01f669774248
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97106903"
---
# <a name="tutorial-use-azure-quickstart-templates"></a>Oktatóanyag: az Azure Gyorsindítás sablonjainak használata

Az [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/) a Közösség által biztosított sablonok tárháza. A sablonok fejlesztésében a sablonokat is használhatja. Ebben az oktatóanyagban megtalálja a webhely erőforrás-definícióját, és hozzáadja a saját sablonhoz. A művelet végrehajtása körülbelül **12 percet** vesz igénybe.

## <a name="prerequisites"></a>Előfeltételek

Javasoljuk, hogy végezze el az [exportált sablonokkal kapcsolatos oktatóanyagot](template-tutorial-export-template.md), de ez nem kötelező.

A Visual Studio Code-nak rendelkeznie kell a Resource Manager-eszközök bővítménnyel, valamint Azure PowerShell vagy az Azure CLI-vel. További információ: [sablon eszközei](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Sablon áttekintése

Az előző oktatóanyag végén a sablon a következő JSON-t használta:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json":::

Ez a sablon a Storage-fiókok és az App Service-csomagok üzembe helyezésére használható, de előfordulhat, hogy hozzá szeretne adni egy webhelyet. Az előre elkészített sablonok segítségével gyorsan felderítheti az erőforrások üzembe helyezéséhez szükséges JSON-t.

## <a name="find-template"></a>Sablon keresése

1. [Azure Gyorsindítás sablonok](https://azure.microsoft.com/resources/templates/) megnyitása
1. A **Search (keresés**) mezőbe írja be a _linuxos webalkalmazás üzembe helyezése_ kifejezést.
1. Válassza ki az **alapszintű Linux-webalkalmazást** tartalmazó csempét. Ha nem találja meg a problémát, a [közvetlen hivatkozás](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/)látható.
1. Válassza **a Tallózás lehetőséget a githubon**.
1. Válassza _aazuredeploy.js_ lehetőséget.
1. Tekintse át a sablont. Különösen keresse meg az `Microsoft.Web/sites` erőforrást.

    ![Resource Manager-sablon – gyors üzembe helyezési webhely](./media/template-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

## <a name="revise-existing-template"></a>Meglévő sablon módosítása

A gyors üzembe helyezési sablon egyesítése a meglévő sablonnal:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json" range="1-108" highlight="32-45,49,85-100":::

A webalkalmazás nevének egyedinek kell lennie az Azure-ban. Ha meg szeretné akadályozni, hogy ismétlődő nevek legyenek, a változó frissítve lett a verzióról a verzióra `webAppPortalName` `"webAppPortalName": "[concat(parameters('webAppName'), '-webapp')]"` `"webAppPortalName": "[concat(parameters('webAppName'), uniqueString(resourceGroup().id))]"` .

Adjon hozzá egy vesszőt a definíció végén, `Microsoft.Web/serverfarms` hogy elkülönítse az erőforrás-definíciót a `Microsoft.Web/sites` definícióból.

Ebben az új erőforrásban néhány fontos szempontot figyelembe kell venni.

Megfigyelheti, `dependsOn` hogy egy, az App Service-csomagra beállított elemmel rendelkezik. Erre a beállításra azért van szükség, mert az App Service-csomagnak léteznie kell a webalkalmazás létrehozása előtt. Az `dependsOn` elem közli, hogy a Resource Manager hogyan rendeli hozzá az erőforrásokat az üzembe helyezéshez.

A `serverFarmId` tulajdonság a [resourceId](template-functions-resource.md#resourceid) függvényt használja. Ez a függvény egy erőforrás egyedi azonosítóját kapja meg. Ebben az esetben az App Service-csomag egyedi azonosítóját kapja meg. A webalkalmazás egy adott app Service-csomaghoz van társítva.

## <a name="deploy-template"></a>Sablon üzembe helyezése

Sablon üzembe helyezéséhez használja az Azure CLI-t vagy a Azure PowerShell-t.

Ha még nem hozta létre az erőforráscsoportot, tekintse meg az [erőforráscsoport létrehozása](template-tutorial-create-first-template.md#create-resource-group)című témakört. A példa feltételezi, hogy a **templateFile** változót a sablonfájl elérési útjára állította, ahogy az az [első oktatóanyagban](template-tutorial-create-first-template.md#deploy-template)is látható.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az üzembe helyezési parancs futtatásához az [Azure CLI](/cli/azure/install-azure-cli) legújabb verziójára van szükség.

```azurecli
az deployment group create \
  --name addwebapp \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> Ha a telepítés nem sikerült, a `verbose` kapcsolóval kérheti le a létrehozott erőforrásokra vonatkozó információkat. A `debug` kapcsoló használatával további információkat kaphat a hibakereséshez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha továbblép a következő oktatóanyagra, nem kell törölnie az erőforráscsoportot.

Ha most leáll, érdemes lehet törölni a telepített erőforrásokat az erőforráscsoport törlésével.

1. A Azure Portal válassza ki a bal oldali menüből az **erőforráscsoportot** .
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. Válassza az **erőforráscsoport törlése** lehetőséget a felső menüben.

## <a name="next-steps"></a>Következő lépések

Megtanulta, hogyan használhat egy rövid útmutató sablont a sablonok fejlesztéséhez. A következő oktatóanyagban címkéket adhat hozzá az erőforrásokhoz.

> [!div class="nextstepaction"]
> [Címkék hozzáadása](template-tutorial-add-tags.md)
