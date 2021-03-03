---
title: Oktatóanyag – gyors üzembe helyezési sablonok használata Azure Resource Manager bicep-fejlesztéshez
description: Ismerje meg, hogyan használhatja az Azure Gyorsindítás-sablonokat a bicep-fejlesztés befejezéséhez.
author: mumian
ms.date: 03/01/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 12dcf4bfc00c299d94d45a5bd02bf9eea576cc73
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748050"
---
# <a name="tutorial-use-azure-quickstart-templates-for-azure-resource-manager-bicep-development"></a>Oktatóanyag: az Azure gyorsindítási sablonjainak használata a Azure Resource Manager bicep-fejlesztéshez

Az [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/) a Közösség által BEfizetett JSON-sablonok tárháza. A bicep-fejlesztésben a minta sablonokat is használhatja. Ebben az oktatóanyagban megtalál egy webhely-erőforrás-definíciót, lefordítja a bicep, és hozzáadja a bicep-fájlhoz. A művelet végrehajtása körülbelül **12 percet** vesz igénybe.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

Javasoljuk, hogy végezze el az [exportált sablonokkal kapcsolatos oktatóanyagot](bicep-tutorial-export-template.md), de ez nem kötelező.

A bicep kiterjesztéssel rendelkező Visual Studio Code-nak, valamint Azure PowerShell vagy Azure CLI-nek kell lennie. További információ: [bicep Tools](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>A bicep-fájl áttekintése

Az előző oktatóanyag végén a bicep-fájl a következő tartalmakat használta:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.bicep":::

Ez a bicep-fájl a Storage-fiókok és az App Service-csomagok üzembe helyezéséhez használható, de előfordulhat, hogy hozzá szeretne adni egy webhelyet. Az előre elkészített sablonok segítségével gyorsan felderítheti az erőforrások üzembe helyezéséhez szükséges JSON-t. Mielőtt az Azure rövid útmutató sablonjai a bicep példákat is felhasználhatják, a konverziós eszközökkel a JSON-sablonokat a bicep-fájlokba konvertálhatja.

## <a name="find-template"></a>Sablon keresése

Az Azure gyors üzembe helyezési sablonjai jelenleg csak JSON-sablonokat biztosítanak. Vannak olyan eszközök, amelyek segítségével defordíthatja a JSON-sablonokat a bicep sablonokba.

1. [Azure Gyorsindítás sablonok](https://azure.microsoft.com/resources/templates/) megnyitása
1. A **Search (keresés**) mezőbe írja be a _linuxos webalkalmazás üzembe helyezése_ kifejezést.
1. Válassza ki az **alapszintű Linux-webalkalmazást** tartalmazó csempét. Ha nem találja meg a problémát, a [közvetlen hivatkozás](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/)látható.
1. Válassza **a Tallózás lehetőséget a githubon**.
1. Válassza _aazuredeploy.js_ lehetőséget. Ez az a sablon, amelyet használhat.
1. Válassza a **nyers** lehetőséget, majd készítsen másolatot az URL-címről.
1. Keresse meg a **https://bicepdemo.z22.web.core.windows.net/** elemet, válassza ki a **defordítás** lehetőséget, majd adja meg a nyers sablon URL-címét.
1. Tekintse át a bicep sablont. Különösen keresse meg az `Microsoft.Web/sites` erőforrást.

    ![Resource Manager-sablon – gyors üzembe helyezési webhely](./media/bicep-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

    Ebben az új erőforrásban több fontos bicep-funkciót is megjegyezünk, ha JSON-sablonokkal dolgoztak.

    Az ARM JSON-sablonokban manuálisan kell megadnia az erőforrás-függőségeket a _dependsOn_ tulajdonsággal. A webhely-erőforrás az App Service-csomag erőforrásaitól függ. Ha a dependsOn tulajdonságot a szimbolikus név használatával az erőforrás bármely tulajdonságára hivatkozik, a rendszer automatikusan hozzáadja a bicep.

    Egyszerűen hivatkozhat az erőforrás-AZONOSÍTÓra az App Service-csomag (appServicePlanName.id) szimbolikus nevével, amely a lefordított JSON-sablon resourceId (...) függvényében lesz lefordítva.

## <a name="revise-existing-bicep-file"></a>Meglévő bicep-fájl módosítása

Egyesítse a lefordított rövid útmutató sablont a meglévő bicep-fájllal. Ugyanaz, mint amit az előző oktatóanyagban tett, frissítse az erőforrás szimbolikus nevét, és az erőforrás nevét az elnevezési konvenciónak megfelelően.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.bicep" range="1-73" highlight="20-25,28,61-71":::

A webalkalmazás nevének egyedinek kell lennie az Azure-ban. Ha meg szeretné akadályozni, hogy ismétlődő nevek legyenek, a változó frissítve lett a verzióról a verzióra `webAppPortalName` `var webAppPortalName_var = '${webAppName}-webapp'` `var webAppPortalName = '${webAppName}${uniqueString(resourceGroup().id)}'` .

## <a name="deploy-bicep-file"></a>Bicep-fájl üzembe helyezése

Az Azure CLI vagy a Azure PowerShell használatával helyezzen üzembe egy bicep-sablont.

Ha még nem hozta létre az erőforráscsoportot, tekintse meg az [erőforráscsoport létrehozása](bicep-tutorial-create-first-bicep.md#create-resource-group)című témakört. A példa feltételezi, hogy a **bicepFile** változót a bicep-fájl elérési útjára állította, ahogy az az [első oktatóanyagban](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)is látható.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A központi telepítési parancsmag futtatásához a Azure PowerShell [legújabb verziójára](/powershell/azure/install-az-ps) van szükség.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
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
  --name addwebapp \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
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

Megtanulta, hogyan használhat egy rövid útmutató sablont a bicep-fejlesztéshez. A következő oktatóanyagban címkéket adhat hozzá az erőforrásokhoz.

> [!div class="nextstepaction"]
> [Címkék hozzáadása](bicep-tutorial-add-tags.md)
