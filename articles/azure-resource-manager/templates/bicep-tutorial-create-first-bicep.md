---
title: Oktatóanyag – Azure Resource Manager bicep-fájlok létrehozása & üzembe helyezése
description: Hozza létre az első bicep-fájlt az Azure-erőforrások üzembe helyezéséhez. Az oktatóanyagban megismerheti a bicep fájl szintaxisát és a Storage-fiók üzembe helyezésének módját.
author: mumian
ms.date: 03/17/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 8979585d7ec0fa6eac1866375fe1e80214f2d2e2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594274"
---
# <a name="tutorial-create-and-deploy-first-azure-resource-manager-bicep-file"></a>Oktatóanyag: az első Azure Resource Manager bicep-fájl létrehozása és üzembe helyezése

Ez az oktatóanyag bemutatja a [bicep](./bicep-overview.md). Bemutatja, hogyan hozhat létre egy kezdő bicep-fájlt, és hogyan helyezheti üzembe az Azure-ban. Megismerheti a bicep-fájl felépítését és a bicep-fájlok használatához szükséges eszközöket. Az oktatóanyag elvégzése körülbelül **12 percet** vesz igénybe, de a tényleges idő attól függően változik, hogy hány eszközt kell telepítenie.

Ez az oktatóanyag egy sorozat első része. Ahogy halad a sorozaton, a bicep-fájl indítása lépésről lépésre módosítja, amíg meg nem vizsgálja a bicep-fájl összes alapvető részét. Ezek az elemek a sokkal összetettebb bicep-fájlok építőelemei. Reméljük, hogy a sorozat végén biztosan létrehozza a saját bicep-fájljait, és készen áll arra, hogy automatizálja az üzemelő példányokat a bicep Files használatával.

Ha szeretne többet megtudni a bicep-fájlok használatának előnyeiről, és miért érdemes automatizálni az üzembe helyezést a bicep Files használatával, lásd: [bicep](./bicep-overview.md).

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="get-tools"></a>Eszközök beolvasása

Először is győződjön meg arról, hogy rendelkezik a bicep-fájlok létrehozásához és üzembe helyezéséhez szükséges eszközökkel. Telepítse ezeket az eszközöket a helyi gépre.

### <a name="editor"></a>Szerkesztő

A bicep-fájlok létrehozásához jó szerkesztőre van szükség. Javasoljuk, hogy a Visual Studio Code-ot a bicep kiterjesztéssel. Ha telepítenie kell ezeket az eszközöket, tekintse meg a [bicep fejlesztői környezet konfigurálása](./bicep-install.md#development-environment)című témakört.

### <a name="command-line-deployment"></a>Parancssori telepítés

A bicep-fájlokat az Azure CLI vagy a Azure PowerShell használatával helyezheti üzembe. Az Azure CLI esetén a 2.20.0 vagy újabb verzióra van szükség; Azure PowerShell esetén a 5.6.0 vagy újabb verzióra van szükség. A telepítési utasításokért lásd:

- [Az Azure PowerShell telepítése](/powershell/azure/install-az-ps)
- [Az Azure CLI telepítése Windows rendszeren](/cli/azure/install-azure-cli-windows)
- [Az Azure CLI telepítése Linux rendszeren](/cli/azure/install-azure-cli-linux)
- [Az Azure CLI telepítése macOS rendszeren](/cli/azure/install-azure-cli-macos)

A Azure PowerShell vagy az Azure CLI telepítését követően ellenőrizze, hogy az első alkalommal jelentkezik-e be. Segítségért lásd: [Bejelentkezés – PowerShell](/powershell/azure/install-az-ps#sign-in) vagy [Bejelentkezés – Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in).

Most már készen áll arra, hogy megkezdje a bicep megismerését.

## <a name="create-your-first-bicep-file"></a>Az első bicep-fájl létrehozása

1. Nyissa meg a Visual Studio Code alkalmazást a bicep bővítménnyel.
1. Új fájl létrehozásához a **fájl** menüben válassza az **új fájl** elemet.
1. A **fájl** menüben válassza a **Mentés másként** lehetőséget.
1. Nevezze el a fájlt _azuredeploy_ , és válassza ki a _bicep_ fájlkiterjesztés elemet. A fájl teljes neve _azuredeploy. bicep_.
1. Mentse a fájlt a munkaállomásra. Olyan elérési utat adjon meg, amely könnyen megjegyezhető, mert később a bicep-fájl telepítésekor megadhatja ezt az útvonalat.
1. Másolja és illessze be a következő tartalmat a fájlba:

    ```bicep
    resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
      name: '{provide-unique-name}'
      location: 'eastus'
      sku: {
        name: 'Standard_LRS'
      }
      kind: 'StorageV2'
      properties: {
        supportsHttpsTrafficOnly: true
      }
    }
    ```

    A Visual Studio Code-környezete így néz ki:

    ![Visual Studio Code első bicep-fájl](./media/Bicep-tutorial-create-first-bicep/resource-manager-visual-studio-code-first-bicep.png)

    Az erőforrás deklarációja négy összetevőből áll:

    - **erőforrás**: kulcsszó.
    - **szimbolikus név** (STG): a szimbolikus név egy azonosító, amely az erőforrásra hivatkozik az egész bicep-fájlban. Az erőforrás neve nem az, hogy mikor lesz telepítve. Az erőforrás nevét a **Name (név** ) tulajdonság határozza meg.  Lásd a lista negyedik összetevőjét. Ahhoz, hogy az oktatóanyagok könnyen követhető legyenek, a **STG** a Storage-fiók erőforrásának szimbolikus neveként használja ebben az oktatóanyag-sorozatban. Ha szeretné megtudni, hogyan használhatja a szimbolikus nevet az objektum tulajdonságainak teljes listájának megjelenítéséhez, tekintse meg a [kimenetek hozzáadása](./bicep-tutorial-add-outputs.md)című témakört.
    - **erőforrástípus** ( Microsoft.Storage/storageAccounts@2019-06-01 ): az erőforrás-szolgáltató (Microsoft. Storage), az erőforrás típusa (storageAccounts) és a apiVersion (2019-06-01) áll. Mindegyik erőforrás-szolgáltató közzéteszi a saját API-verzióit, így ez az érték a típusra jellemző. Több típust és apiVersions is találhat az [ARM-sablonok referenciájának](/azure/templates/)különböző Azure-erőforrásaihoz.
    - **Tulajdonságok** (minden belül = {...}): ezek a megadott tulajdonságok, amelyeket meg szeretne adni az adott erőforrástípus számára. Ezek pontosan ugyanazok a tulajdonságok, mint az ARM-sablonokban. Minden erőforrás rendelkezik egy `name` tulajdonsággal. A legtöbb erőforráshoz tartozik egy `location` tulajdonság is, amely azt a régiót állítja be, ahol az erőforrás telepítve van. A többi tulajdonság az erőforrás típusa és az API-verzió szerint változhat. Fontos tisztában lenni az API-verzió és a rendelkezésre álló tulajdonságok közötti kapcsolattal, így részletesebben is megtudhatja.

        Ehhez a Storage-fiókhoz az API-verziót láthatja a következő címen: [storageAccounts 2019-06-01](/azure/templates/microsoft.storage/2019-06-01/storageaccounts). Figyelje meg, hogy az összes tulajdonságot nem adta hozzá a bicep-fájlhoz. A tulajdonságok többsége nem kötelező. Az `Microsoft.Storage` erőforrás-szolgáltató egy új API-verziót szabadít fel, de a telepítendő verziónak nem kell megváltoznia. Továbbra is használhatja ezt a verziót, és biztos lehet benne, hogy a telepítés eredményei konzisztensek lesznek.

        Ha egy régebbi API-verziót (például [storageAccounts 2016-05-01](/azure/templates/microsoft.storage/2016-05-01/storageaccounts)) tekint meg, láthatja, hogy a tulajdonságok kisebb készlete áll rendelkezésre.

        Ha úgy dönt, hogy megváltoztatja egy erőforrás API-verzióját, ügyeljen rá, hogy kiértékelje az adott verzió tulajdonságait, és megfelelően módosítsa a bicep-fájlt.

1. Cserélje le `{provide-unique-name}` a kapcsos zárójeleket `{}` egy egyedi Storage-fiók nevére.

    > [!IMPORTANT]
    > A tárfiók nevének egyedinek kell lennie az egész Azure rendszerben. A névnek csak kisbetűvel vagy számmal kell rendelkeznie. Nem lehet hosszabb 24 karakternél. Lehet, hogy megpróbál egy elnevezési mintát használni, például a **store1** előtagként való használatát, majd hozzáadja a monogramját és a mai dátumot. A használt név például a következőhöz hasonló: **store1abc09092019**.

    A Storage-fiókok egyedi nevének kitalálása nem egyszerű, és nem jól működik a nagyméretű központi telepítések automatizálásához. Az oktatóanyag-Sorozat későbbi részében a bicep funkciókat fogja használni, amelyek megkönnyítik egy egyedi név létrehozását.

1. Mentse a fájlt.

Gratulálunk, létrehozta az első bicep-fájlt.

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

A bicep-fájlok telepítésekor meg kell adnia egy erőforráscsoportot, amely az erőforrásokat fogja tartalmazni. Az üzembe helyezési parancs futtatása előtt hozza létre az erőforráscsoportot az Azure CLI-vel vagy az Azure PowerShell-lel.

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

## <a name="deploy-bicep-file"></a>Bicep-fájl üzembe helyezése

A bicep a Azure Resource Manager-sablonok (ARM-sablonok) transzparens absztrakciója. Mindegyik bicep-fájl egy standard ARM-sablonra van lefordítva, mielőtt üzembe lenne. A bicep-fájlt lefordíthatja egy ARM-sablonba, mielőtt telepítené, vagy közvetlenül üzembe helyezi a bicep-fájlt. A bicep-fájl üzembe helyezéséhez használja az Azure CLI-t vagy a Azure PowerShell. Használja az Ön által létrehozott erőforráscsoportot. Adjon nevet a központi telepítésnek, hogy könnyen azonosítható legyen az üzembe helyezési előzményekben. A kényelem érdekében hozzon létre egy olyan változót is, amely a bicep-fájl elérési útját tárolja. Ez a változó megkönnyíti az üzembe helyezési parancsok futtatását, mert nem kell újra megadnia az elérési utat minden egyes üzembe helyezéskor. Cserélje le a `{provide-the-path-to-the-bicep-file}` kapcsos zárójeleket a `{}` bicep-fájl elérési útjára a _. bicep_ kiterjesztési névvel.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A központi telepítési parancsmag futtatásához a Azure PowerShell [legújabb verziójára](/powershell/azure/install-az-ps) van szükség.

```azurepowershell
$bicepFile = "{provide-the-path-to-the-bicep-file}"
New-AzResourceGroupDeployment `
  -Name firstbicep `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az üzembe helyezési parancs futtatásához az [Azure CLI](/cli/azure/install-azure-cli) legújabb verziójára van szükség.

```azurecli
bicepFile="{provide-the-path-to-the-bicep-file}"
az deployment group create \
  --name firstbicep \
  --resource-group myResourceGroup \
  --template-file $bicepFile
```

---

Az üzembehelyezési parancs az eredményeket adja vissza. Ellenőrizze, `ProvisioningState` hogy az üzembe helyezés sikeres volt-e.

> [!NOTE]
> Ha a telepítés nem sikerült, a `verbose` kapcsolóval kérheti le a létrehozott erőforrásokra vonatkozó információkat. A `debug` kapcsoló használatával további információkat kaphat a hibakereséshez.

## <a name="verify-deployment"></a>Az üzembe helyezés ellenőrzése

A központi telepítés ellenőrzéséhez tekintse meg az erőforráscsoportot a Azure Portalból.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A bal oldali menüben válassza az **erőforráscsoportok** lehetőséget.

1. Válassza ki az erőforráscsoport központi telepítését a legutóbbi eljárásban. Az alapértelmezett név a **myResourceGroup**. Az erőforráscsoporthoz tartozó erőforrás nem jelenik meg.

1. Az Áttekintés jobb felső sarkában megjelenik az üzemelő példány állapota. Válassza az **1 sikeres** lehetőséget.

   ![Központi telepítés állapotának megtekintése](./media/bicep-tutorial-create-first-bicep/deployment-status.png)

1. Az erőforráscsoport központi telepítésének előzményei láthatók. Válassza a **firstbicep** lehetőséget.

   ![Központi telepítés kiválasztása](./media/bicep-tutorial-create-first-bicep/select-from-deployment-history.png)

1. A központi telepítés összegzése látható. Van egy telepített Storage-fiók. A bal oldalon megtekintheti a bemeneteket, a kimeneteket és az üzembe helyezés során használt sablont.

   ![Központi telepítés összegzésének megtekintése](./media/bicep-tutorial-create-first-bicep/view-deployment-summary.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha továbblép a következő oktatóanyagra, nem kell törölnie az erőforráscsoportot.

Ha most leáll, érdemes törölni az erőforráscsoportot.

1. A Azure Portal válassza ki a bal oldali menüből az **erőforráscsoportot** .
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. Válassza az **erőforráscsoport törlése** lehetőséget a felső menüben.

## <a name="next-steps"></a>Következő lépések

Létrehozott egy egyszerű bicep-fájlt az Azure-ba való üzembe helyezéshez.  A későbbi oktatóanyagokban megtudhatja, hogyan adhat hozzá paramétereket, változókat, kimeneteket és modulokat egy bicep-fájlhoz. Ezek a funkciók sokkal összetettebb bicep-fájlok építőelemei.

> [!div class="nextstepaction"]
> [Paraméterek hozzáadása](bicep-tutorial-add-parameters.md)
