---
title: Oktatóanyag – & üzembe helyezési sablon létrehozása
description: Hozza létre az első Azure Resource Manager sablonját (ARM-sablon). Az oktatóanyagban megismerheti a sablonfájl szintaxisát és a Storage-fiók központi telepítését.
author: mumian
ms.date: 12/17/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 001a20c38a82d053ae22a2614a32f0be9d8aabce
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97656988"
---
# <a name="tutorial-create-and-deploy-your-first-arm-template"></a>Oktatóanyag: az első ARM-sablon létrehozása és üzembe helyezése

Ez az oktatóanyag bemutatja, hogyan Azure Resource Manager sablonokat (ARM-sablonok). Bemutatja, hogyan hozhat létre kezdő sablont, és hogyan helyezheti üzembe az Azure-ban. Megismerheti a sablon felépítését és a sablonokkal való munkához szükséges eszközöket. Az oktatóanyag elvégzése körülbelül **12 percet** vesz igénybe, de a tényleges idő attól függően változik, hogy hány eszközt kell telepítenie.

Ez az oktatóanyag egy sorozat első része. Ahogy halad a sorozaton, módosítja a sablon indítása lépésről lépésre, amíg meg nem tárja az ARM-sablonok összes alapvető részét. Ezek az elemek a sokkal összetettebb sablonok építőelemei. Reméljük, hogy a sorozat végén biztos lehet abban, hogy saját sablonokat hoz létre, és készen áll a sablonokkal való üzembe helyezések automatizálására.

Ha szeretné megismerni a sablonok használatának előnyeit, és miért érdemes automatizálni az üzembe helyezést a sablonokkal, tekintse meg az [ARM-sablonok áttekintése](overview.md)című témakört. Ha szeretne többet megtudni az ARM-sablonokról a Microsoft Learn modulok irányított készletén keresztül, tekintse meg az [erőforrások üzembe helyezése és kezelése az Azure-ban ARM-sablonok használatával című részt](/learn/paths/deploy-manage-resource-manager-templates/).

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="get-tools"></a>Eszközök beolvasása

Először is győződjön meg arról, hogy rendelkezik a sablonok létrehozásához és üzembe helyezéséhez szükséges eszközökkel. Telepítse ezeket az eszközöket a helyi gépre.

### <a name="editor"></a>Szerkesztő

A sablonok JSON-fájlok. Sablonok létrehozásához jó JSON-szerkesztőre van szükség. A Visual Studio Code-ot a Resource Manager-eszközök bővítménnyel ajánljuk. Ha telepítenie kell ezeket az eszközöket, tekintse meg a gyors útmutató [: ARM-sablonok létrehozása a Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md)használatával című témakört.

### <a name="command-line-deployment"></a>Parancssori telepítés

A sablon telepítéséhez Azure PowerShell vagy Azure CLI is szükséges. Ha az Azure CLI-t használja, a legújabb verziót kell használnia. A telepítési utasításokért lásd:

- [Az Azure PowerShell telepítése](/powershell/azure/install-az-ps)
- [Az Azure CLI telepítése Windows rendszeren](/cli/azure/install-azure-cli-windows)
- [Az Azure CLI telepítése Linux rendszeren](/cli/azure/install-azure-cli-linux)
- [Az Azure CLI telepítése macOS rendszeren](/cli/azure/install-azure-cli-macos)

A Azure PowerShell vagy az Azure CLI telepítését követően ellenőrizze, hogy az első alkalommal jelentkezik-e be. Segítségért lásd: [Bejelentkezés – PowerShell](/powershell/azure/install-az-ps#sign-in) vagy [Bejelentkezés – Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in).

> [!IMPORTANT]
> Ha az Azure CLI-t használja, ellenőrizze, hogy rendelkezik-e a 2,6-es vagy újabb verziójával. Az oktatóanyagban megjelenő parancsok nem fognak működni, ha régebbi verziókat használ. A telepített verziójának vizsgálatához használja a következőt: `az --version` .

Most már készen áll a sablonok megismerésének megkezdésére.

## <a name="create-your-first-template"></a>Az első saját sablon létrehozása

1. Nyissa meg a Visual Studio Code-ot, és telepítse a Resource Manager Tools bővítményt.
1. Új fájl létrehozásához a **fájl** menüben válassza az **új fájl** elemet.
1. A **fájl** menüben válassza a **Mentés másként** lehetőséget.
1. Nevezze el a fájlt _azuredeploy_ , és válassza ki a _JSON_ -fájl kiterjesztését. A fájl teljes neve _azuredeploy.js_.
1. Mentse a fájlt a munkaállomásra. Olyan elérési utat adjon meg, amely könnyen megjegyezhető, mert a sablon telepítésekor később is megadhatja ezt az útvonalat.
1. Másolja és illessze be a következő JSON-fájlt a fájlba:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": []
    }
    ```

    A Visual Studio Code-környezete így néz ki:

    ![ARM-sablon – Visual Studio Code – első sablon](./media/template-tutorial-create-first-template/resource-manager-visual-studio-code-first-template.png)

    Ez a sablon nem telepít semmilyen erőforrást. A rendszer egy üres sablont használ, hogy megismerje a sablon üzembe helyezésének lépéseit, miközben minimalizálja a hiba valószínűségét.

    A JSON-fájl a következő elemeket tartalmazza:

    - `$schema`: Megadja a JSON-séma fájljának helyét. A sémafájl a sablonon belül elérhető tulajdonságokat írja le. A séma például a `resources` sablon érvényes tulajdonságainak egyikét határozza meg. Ne aggódjon, hogy a séma dátuma 2019-04-01. A séma verziója naprakész, és tartalmazza a legújabb funkciókat. A séma dátumának módosítása nem történt meg, mert a bevezetése óta nem történt változás.
    - `contentVersion`: Megadja a sablon verzióját (például 1.0.0.0). Ehhez az elemhez bármilyen értéket megadhat. Ezzel az értékkel dokumentálhatja a sablon jelentős változásait. Ha a sablonnal telepít erőforrásokat, ezzel az értékkel meggyőződhet arról, hogy a megfelelő sablon használatban van-e.
    - `resources`: A központilag telepíteni vagy frissíteni kívánt erőforrásokat tartalmazza. Jelenleg üres, de később további erőforrásokat fog hozzáadni.

1. Mentse a fájlt.

Gratulálunk, létrehozta az első sablont.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

A Azure PowerShell/Azure CLI használatának megkezdéséhez jelentkezzen be az Azure-beli hitelesítő adataival.

A következő kódrészletek lapjain válassza ki a Azure PowerShell és az Azure CLI közötti választást. A cikkben található CLI-példák a bash-rendszerhéjra íródnak.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az login
```

---

Ha több Azure-előfizetéssel rendelkezik, válassza ki a használni kívánt előfizetést. Cserélje le `[SubscriptionID/SubscriptionName]` a és a szögletes zárójeleket az `[]` előfizetési adataira:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzContext [SubscriptionID/SubscriptionName]
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az account set --subscription [SubscriptionID/SubscriptionName]
```

---

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Sablon központi telepítésekor megadhat egy erőforráscsoportot, amely az erőforrásokat fogja tartalmazni. Az üzembe helyezési parancs futtatása előtt hozza létre az erőforráscsoportot az Azure CLI-vel vagy az Azure PowerShell-lel.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name myResourceGroup `
  -Location "Central US"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroup \
  --location "Central US"
```

---

## <a name="deploy-template"></a>Sablon üzembe helyezése

A sablon üzembe helyezéséhez használja az Azure CLI-t vagy a Azure PowerShell. Használja az Ön által létrehozott erőforráscsoportot. Adjon nevet a központi telepítésnek, hogy könnyen azonosítható legyen az üzembe helyezési előzményekben. A kényelem érdekében hozzon létre egy olyan változót is, amely a sablonfájl elérési útját tárolja. Ez a változó megkönnyíti az üzembe helyezési parancsok futtatását, mert nem kell újra megadnia az elérési utat minden egyes üzembe helyezéskor. Cserélje le a `{provide-the-path-to-the-template-file}` és a kapcsos zárójeleket a `{}` sablonfájl elérési útjára.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
New-AzResourceGroupDeployment `
  -Name blanktemplate `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az üzembe helyezési parancs futtatásához az [Azure CLI](/cli/azure/install-azure-cli) legújabb verziójára van szükség.

```azurecli
templateFile="{provide-the-path-to-the-template-file}"
az deployment group create \
  --name blanktemplate \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

Az üzembehelyezési parancs az eredményeket adja vissza. Ellenőrizze, `ProvisioningState` hogy az üzembe helyezés sikeres volt-e.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

![PowerShell üzembe helyezési állapota](./media/template-tutorial-create-first-template/resource-manager-deployment-provisioningstate.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

![Azure CLI üzembe helyezési állapot](./media/template-tutorial-create-first-template/azure-cli-provisioning-state.png)

---

> [!NOTE]
> Ha a telepítés nem sikerült, a `verbose` kapcsolóval kérheti le a létrehozott erőforrásokra vonatkozó információkat. A `debug` kapcsoló használatával további információkat kaphat a hibakereséshez.

## <a name="verify-deployment"></a>Az üzembe helyezés ellenőrzése

A központi telepítés ellenőrzéséhez tekintse meg az erőforráscsoportot a Azure Portalból.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A bal oldali menüben válassza az **erőforráscsoportok** lehetőséget.

1. Válassza ki az erőforráscsoport központi telepítését a legutóbbi eljárásban. Az alapértelmezett név a **myResourceGroup**. Az erőforráscsoporthoz tartozó erőforrás nem jelenik meg.

1. Az Áttekintés jobb felső sarkában megjelenik az üzemelő példány állapota. Válassza az **1 sikeres** lehetőséget.

   ![Központi telepítés állapotának megtekintése](./media/template-tutorial-create-first-template/deployment-status.png)

1. Az erőforráscsoport központi telepítésének előzményei láthatók. Válassza a **blanktemplate** lehetőséget.

   ![Központi telepítés kiválasztása](./media/template-tutorial-create-first-template/select-from-deployment-history.png)

1. A központi telepítés összegzése látható. Ebben az esetben nem sokat kell látnia, mert nincs üzembe helyezett erőforrás. A sorozat későbbi részében hasznos lehet megtekinteni az összefoglalás áttekintését az üzembe helyezési előzményekben. A bal oldalon megtekintheti a bemeneteket, a kimeneteket és az üzembe helyezés során használt sablont.

   ![Központi telepítés összegzésének megtekintése](./media/template-tutorial-create-first-template/view-deployment-summary.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha továbblép a következő oktatóanyagra, nem kell törölnie az erőforráscsoportot.

Ha most leáll, érdemes törölni az erőforráscsoportot.

1. A Azure Portal válassza ki a bal oldali menüből az **erőforráscsoportot** .
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. Válassza az **erőforráscsoport törlése** lehetőséget a felső menüben.

## <a name="next-steps"></a>Következő lépések

Létrehozott egy egyszerű sablont az Azure-ba való üzembe helyezéshez. A következő oktatóanyagban hozzá kell adnia egy Storage-fiókot a sablonhoz, és telepítenie kell azt az erőforráscsoporthoz.

> [!div class="nextstepaction"]
> [Erőforrás hozzáadása](template-tutorial-add-resource.md)
