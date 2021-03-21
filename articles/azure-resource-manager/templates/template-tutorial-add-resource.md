---
title: Oktatóanyag – erőforrás hozzáadása sablonhoz
description: Ismerteti az első Azure Resource Manager-sablon (ARM-sablon) létrehozásának lépéseit. Megismerheti a sablonfájl szintaxisát és a Storage-fiók központi telepítését.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 49cee5c98c4099e214a732371269e935db353152
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97106971"
---
# <a name="tutorial-add-a-resource-to-your-arm-template"></a>Oktatóanyag: erőforrás hozzáadása az ARM-sablonhoz

Az [előző oktatóanyagban](template-tutorial-create-first-template.md)megtanulta, hogyan hozhat létre üres Azure Resource Manager SABLONT (ARM-sablon), és hogyan helyezheti üzembe. Most már készen áll egy tényleges erőforrás üzembe helyezésére. Ebben az oktatóanyagban egy Storage-fiókot fog hozzáadni. Az oktatóanyag elvégzése körülbelül **9 percet** vesz igénybe.

## <a name="prerequisites"></a>Előfeltételek

Javasoljuk, hogy végezze el a [sablonokkal kapcsolatos bevezető oktatóanyagot](template-tutorial-create-first-template.md), de ez nem kötelező.

A Visual Studio Code-nak rendelkeznie kell a Resource Manager-eszközök bővítménnyel, valamint Azure PowerShell vagy az Azure CLI-vel. További információ: [sablon eszközei](template-tutorial-create-first-template.md#get-tools).

## <a name="add-resource"></a>Erőforrás hozzáadása

A Storage-fiók definíciójának meglévő sablonhoz való hozzáadásához tekintse meg a Kiemelt JSON-t a következő példában. A sablon részeinek másolása helyett másolja a teljes fájlt, és cserélje le a sablont a tartalmára.

Cserélje le `{provide-unique-name}` a és a kapcsos zárójeleket `{}` egy egyedi Storage-fiók nevére.

> [!IMPORTANT]
> A tárfiók nevének egyedinek kell lennie az egész Azure rendszerben. A névnek csak kisbetűvel vagy számmal kell rendelkeznie. Nem lehet hosszabb 24 karakternél. Lehet, hogy megpróbál egy elnevezési mintát használni, például a **store1** előtagként való használatát, majd hozzáadja a monogramját és a mai dátumot. A használt név például a következőhöz hasonló: **store1abc09092019**.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json" range="1-19" highlight="5-17":::

A Storage-fiókok egyedi nevének kitalálása nem egyszerű, és nem jól működik a nagyméretű központi telepítések automatizálásához. Az oktatóanyag-Sorozat későbbi részében olyan sablon-funkciókat fog használni, amelyek megkönnyítik egy egyedi név létrehozását.

## <a name="resource-properties"></a>Erőforrás tulajdonságai

Lehet, hogy kíváncsi, hogyan lehet megkeresni az egyes erőforrástípusok által használandó tulajdonságokat. Az [ARM-sablon hivatkozásával](/azure/templates/) megkeresheti a telepíteni kívánt erőforrásokat.

Minden üzembe helyezett erőforrás rendelkezik legalább a következő tulajdonságokkal:

- `type`: Az erőforrás típusa. Ez az érték az erőforrás-szolgáltató névterének és az erőforrás típusának kombinációja, például: `Microsoft.Storage/storageAccounts` .
- `apiVersion`: Az erőforrás létrehozásához használt REST API verziója. Mindegyik erőforrás-szolgáltató közzéteszi a saját API-verzióit, így ez az érték a típusra jellemző.
- `name`: Az erőforrás neve.

A legtöbb erőforráshoz tartozik egy `location` tulajdonság is, amely azt a régiót állítja be, ahol az erőforrás telepítve van.

A többi tulajdonság az erőforrás típusa és az API-verzió szerint változhat. Fontos tisztában lenni az API-verzió és a rendelkezésre álló tulajdonságok közötti kapcsolattal, így részletesebben is megtudhatja.

Ebben az oktatóanyagban egy Storage-fiókot adott hozzá a sablonhoz. Ezt az API-verziót a következő helyen tekintheti meg: [storageAccounts 2019-04-01](/azure/templates/microsoft.storage/2019-04-01/storageaccounts). Figyelje meg, hogy az összes tulajdonságot nem adta hozzá a sablonhoz. A tulajdonságok többsége nem kötelező. Az `Microsoft.Storage` erőforrás-szolgáltató egy új API-verziót szabadít fel, de a telepítendő verziónak nem kell megváltoznia. Továbbra is használhatja ezt a verziót, és biztos lehet benne, hogy a telepítés eredményei konzisztensek lesznek.

Ha egy régebbi API-verziót (például [storageAccounts 2016-05-01](/azure/templates/microsoft.storage/2016-05-01/storageaccounts)) tekint meg, láthatja, hogy a tulajdonságok kisebb készlete áll rendelkezésre.

Ha úgy dönt, hogy megváltoztatja egy erőforrás API-verzióját, ügyeljen rá, hogy kiértékelje az adott verzió tulajdonságait, és megfelelően módosítsa a sablont.

## <a name="deploy-template"></a>Sablon üzembe helyezése

A sablont üzembe helyezheti a Storage-fiók létrehozásához. Adjon egy másik nevet az üzembe helyezésnek, hogy könnyen megtalálja az előzményekben.

Ha még nem hozta létre az erőforráscsoportot, tekintse meg az [erőforráscsoport létrehozása](template-tutorial-create-first-template.md#create-resource-group)című témakört. A példa feltételezi `templateFile` , hogy a változót a sablonfájl elérési útjára állította, ahogy az [első oktatóanyagban](template-tutorial-create-first-template.md#deploy-template)is látható.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addstorage `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az üzembe helyezési parancs futtatásához az [Azure CLI](/cli/azure/install-azure-cli) legújabb verziójára van szükség.

```azurecli
az deployment group create \
  --name addstorage \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

> [!NOTE]
> Ha a telepítés nem sikerült, a `verbose` kapcsolóval kérheti le a létrehozott erőforrásokra vonatkozó információkat. A `debug` kapcsoló használatával további információkat kaphat a hibakereséshez.

Két lehetséges üzembe helyezési hiba merülhet fel:

- `Error: Code=AccountNameInvalid; Message={provide-unique-name}` nem érvényes Storage-fióknév. A Storage-fiók nevének 3 – 24 karakter hosszúnak kell lennie, és csak számokat és kisbetűket használjon.

    A sablonban cserélje ki a helyére `{provide-unique-name}` egy egyedi Storage-fiók nevét. Lásd: [erőforrás hozzáadása](#add-resource).

- `Error: Code=StorageAccountAlreadyTaken; Message=The storage account named store1abc09092019` már használatban van.

    A sablonban próbálkozzon egy másik Storage-fiók nevével.

Ez a telepítés hosszabb időt vesz igénybe, mint az üres sablon üzembe helyezése, mivel a rendszer létrehozza a Storage-fiókot. Körülbelül egy percet is igénybe vehet, de általában gyorsabb.

## <a name="verify-deployment"></a>Az üzembe helyezés ellenőrzése

A központi telepítés ellenőrzéséhez tekintse meg az erőforráscsoportot a Azure Portalból.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldali menüben válassza az **erőforráscsoportok** lehetőséget.
1. Válassza ki azt az erőforráscsoportot, amelyet központilag telepített.
1. Láthatja, hogy a Storage-fiók telepítve van.
1. Figyelje meg, hogy az üzembe helyezési címke most már azt mondja: **központi telepítések: 2 sikeres**.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha továbblép a következő oktatóanyagra, nem kell törölnie az erőforráscsoportot.

Ha most leáll, érdemes lehet törölni a telepített erőforrásokat az erőforráscsoport törlésével.

1. A Azure Portal válassza ki a bal oldali menüből az **erőforráscsoportot** .
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. Válassza az **erőforráscsoport törlése** lehetőséget a felső menüben.

## <a name="next-steps"></a>Következő lépések

Létrehozott egy egyszerű sablont egy Azure Storage-fiók üzembe helyezéséhez. A későbbi oktatóanyagokban megtudhatja, hogyan adhat hozzá paramétereket, változókat, erőforrásokat és kimeneteket egy sablonhoz. Ezek a funkciók sokkal összetettebb sablonok építőelemei.

> [!div class="nextstepaction"]
> [Paraméterek hozzáadása](template-tutorial-add-parameters.md)
