---
title: A sablon specifikációinak áttekintése
description: Leírja, hogyan lehet létrehozni a sablon specifikációit, és megoszthatja őket a szervezet más felhasználóival.
ms.topic: conceptual
ms.date: 10/02/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: b0dfc41bddccc6b5c5c924168044cffc0aa5e2b5
ms.sourcegitcommit: 638f326d02d108cf7e62e996adef32f2b2896fd5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91728471"
---
# <a name="azure-resource-manager-template-specs-preview"></a>Azure Resource Manager sablon specifikációi (előzetes verzió)

A sablon specifikációja egy Azure Resource Manager sablon (ARM-sablon) az Azure-ban való tárolásához szükséges erőforrástípus a későbbi üzembe helyezéshez. Ez az erőforrástípus lehetővé teszi, hogy megossza az ARM-sablonokat a szervezet más felhasználóival. A többi Azure-erőforráshoz hasonlóan az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával is megoszthatja a sablon specifikációját.

A **Microsoft. Resources/templateSpecs** a sablonhoz tartozó specifikációk típusa. Egy fő sablonból és tetszőleges számú csatolt sablonból áll. Az Azure biztonságosan tárolja a sablonhoz tartozó specifikációkat az erőforráscsoportok között. A sablon specifikációi támogatják a [verziószámozást](#versioning).

A sablon specifikációjának üzembe helyezéséhez szabványos Azure-eszközöket (például PowerShell, Azure CLI, Azure Portal, REST és más támogatott SDK-kat és ügyfeleket) kell használnia. Ugyanazokat a parancsokat használja, mint a sablonhoz.

> [!NOTE]
> A sablonra vonatkozó specifikációk jelenleg előzetes verzióban érhetők el. A használatához regisztrálnia kell [a várakozási listára](https://aka.ms/templateSpecOnboarding).

## <a name="why-use-template-specs"></a>Miért érdemes használni a sablon specifikációit?

Ha jelenleg a sablonok egy GitHub-tárházban vagy egy Storage-fiókban vannak, a sablonok megosztásának és használatának megkísérlése során több kihívást is jelent. Ahhoz, hogy egy felhasználó telepíteni tudja, a sablonnak helyinek kell lennie, vagy a sablon URL-címének nyilvánosan elérhetőnek kell lennie. Ennek a korlátozásnak a megszerzéséhez megoszthatja a sablon példányait azokkal a felhasználókkal, akiknek telepíteniük kell, vagy meg kell nyitnia a hozzáférést a tárházhoz vagy a Storage-fiókhoz. Ha a felhasználók egy sablon helyi példányait futtatják, akkor ezek a másolatok végül az eredeti sablontól eltérőek lehetnek. Ha a tárház vagy a Storage-fiók nyilvánosan elérhetővé válik, lehetővé teheti, hogy a nem kívánt felhasználók hozzáférjenek a sablonhoz.

A sablon specifikációinak használatának előnye, hogy kanonikus sablonokat hozhat létre, és megoszthatja őket a szervezet munkacsoportjaival. A sablon specifikációi biztonságosak, mert elérhetők Azure Resource Manager számára a központi telepítéshez, de az Azure RBAC engedélye nélkül nem hozzáférhetők a felhasználók számára. A felhasználóknak csak olvasási hozzáférésre van szükségük a sablon specifikációhoz a sablon üzembe helyezéséhez, így a sablon megosztható anélkül, hogy mások is módosíthassák.

A sablonban szerepeltetni kívánt sablonokat a szervezet rendszergazdáinak ellenőriznie kell a szervezet követelményeinek és útmutatásának követése érdekében.

## <a name="create-template-spec"></a>Sablon létrehozása – spec

Az alábbi példa egy egyszerű sablont mutat be egy Storage-fiók létrehozásához az Azure-ban.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ]
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[concat('store', uniquestring(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "kind": "StorageV2",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      }
    }
  ]
}
```

A sablon specifikációjának létrehozásakor a PowerShell-vagy CLI-parancsok át lesznek adva a fő sablonfájl számára. Ha a fő sablon a csatolt sablonokra hivatkozik, a parancsok megkeresik és becsomagolják őket a sablon specifikációjának létrehozásához. További információ: [sablon létrehozása spec csatolt sablonokkal](#create-a-template-spec-with-linked-templates).

Hozzon létre egy sablont a specifikáció használatával:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzTemplateSpec -Name storageSpec -Version 1.0 -ResourceGroupName templateSpecsRg -Location westus2 -TemplateFile ./mainTemplate.json
```

# <a name="cli"></a>[Parancssori felület](#tab/azure-cli)

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json"
```

---

Az előfizetéshez tartozó összes sablon specifikációját a alábbiak használatával tekintheti meg:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzTemplateSpec
```

# <a name="cli"></a>[Parancssori felület](#tab/azure-cli)

```azurecli
az ts list
```

---

Megtekintheti a sablon specifikációjának részleteit, beleértve a verzióit a következőkkel:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzTemplateSpec -ResourceGroupName templateSpecsRG -Name storageSpec
```

# <a name="cli"></a>[Parancssori felület](#tab/azure-cli)

```azurecli
az ts show \
    --name storageSpec \
    --resource-group templateSpecRG \
    --version "1.0"
```

---

## <a name="deploy-template-spec"></a>Sablon üzembe helyezése – spec

A sablon specifikációjának létrehozása után a felhasználók olvasási hozzáféréssel láthatják **el** a sablon specifikációját. További információ a hozzáférés biztosításáról: [oktatóanyag: csoport hozzáférésének biztosítása az Azure-erőforrásokhoz Azure PowerShell használatával](../../role-based-access-control/tutorial-role-assignments-group-powershell.md).

A sablonok specifikációi a portálon, a PowerShellen, az Azure CLI-n, vagy egy nagyobb sablon-telepítésben csatolt sablonként is üzembe helyezhetők. A szervezet felhasználói a specifikációt bármely hatókörre üzembe helyezhetik az Azure-ban (erőforráscsoport, előfizetés, felügyeleti csoport vagy bérlő).

Ahelyett, hogy egy sablon elérési útját vagy URI azonosítóját kellene átadnia, az erőforrás-AZONOSÍTÓjának megadásával egy sablont kell telepítenie. Az erőforrás-azonosító formátuma a következő:

**/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Resources/templateSpecs/{template-spec-name}/versions/{template-spec-version}**

Figyelje meg, hogy az erőforrás-azonosító tartalmazza a sablon specifikációjának verziószámát.

Tegyük fel például, hogy a következő paranccsal telepíti a sablonhoz tartozó specifikációt.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0"

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG
```

# <a name="cli"></a>[Parancssori felület](#tab/azure-cli)

```azurecli
id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0"

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

---

A gyakorlatban általában a `Get-AzTemplateSpec` telepíteni kívánt sablon azonosítójának lekéréséhez fog futni.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$id = (Get-AzTemplateSpec -Name storageSpec -ResourceGroupName templateSpecsRg -Version 1.0).Versions.Id

New-AzResourceGroupDeployment `
  -ResourceGroupName demoRG `
  -TemplateSpecId $id
```

# <a name="cli"></a>[Parancssori felület](#tab/azure-cli)

```azurecli
id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

---

## <a name="parameters"></a>Paraméterek

A paraméterek a sablon specifikációba való átadása pontosan olyan, mint a paraméterek átadása egy ARM-sablonba. Adja hozzá a paraméter értékeit a beágyazott vagy egy paraméter-fájlban.

Ha egy paramétert szeretne átadni, használja a következőt:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG `
  -StorageAccountType Standard_GRS
```

# <a name="cli"></a>[Parancssori felület](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group demoRG \
  --template-spec $id \
  --parameters storageAccountType='Standard_GRS'
```

---

Helyi paraméter fájljának létrehozásához használja a következőt:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "StorageAccountType": {
      "value": "Standard_GRS"
    }
  }
}
```

És adja át a paramétert a fájlnak:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG `
  -TemplateParameterFile ./mainTemplate.parameters.json
```

# <a name="cli"></a>[Parancssori felület](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group demoRG \
  --template-spec $id \
  --parameters "./mainTemplate.parameters.json"
```

---

## <a name="create-a-template-spec-with-linked-templates"></a>Sablon létrehozása a csatolt sablonokkal

Ha a sablon fő sablonja a csatolt sablonokra hivatkozik, akkor a PowerShell és a CLI parancsok automatikusan megtalálják és becsomagolják a csatolt sablonokat a helyi meghajtóról. Nem kell manuálisan konfigurálnia a Storage-fiókokat vagy-adattárakat a sablonhoz tartozó specifikációk futtatásához – minden a sablon spec-erőforrása önálló.

Az alábbi példa egy két csatolt sablonnal rendelkező fő sablonból áll. A példa csak a sablon kivonata. Figyelje meg, hogy egy nevű tulajdonságot használ `relativePath` a többi sablonhoz való hivatkozáshoz. A (z `apiVersion` `2020-06-01` ) vagy újabb verziót kell használnia az üzemelő példányok erőforrásához.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  ...
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "relativePath": "artifacts/webapp.json"
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "relativePath": "artifacts/database.json"
        }
      }
    }
  ],
  "outputs": {}
}
```

Ha a fenti példában a PowerShell-vagy CLI-parancs létrehozta a sablon specifikációját, a parancs három fájlt talál: a fő sablont, a webalkalmazás sablonját () `webapp.json` és az adatbázis sablonját ( `database.json` ), és becsomagolja őket a sablon specifikációba.

További információ: [oktatóanyag: sablon létrehozása a csatolt sablonokkal](template-specs-create-linked.md).

## <a name="deploy-template-spec-as-a-linked-template"></a>A sablon specifikációjának üzembe helyezése csatolt sablonként

Miután létrehozta a sablon specifikációját, egyszerűen felhasználhatja azt egy ARM-sablonból vagy egy másik sablon-specifikációból. A sablonhoz való hivatkozáshoz adja hozzá az erőforrás-AZONOSÍTÓját a sablonhoz. A társított sablon specifikációja automatikusan települ a fő sablon telepítésekor. Ez a viselkedés lehetővé teszi, hogy moduláris sablon-specifikációkat fejlesszen, és szükség szerint újra felhasználhassa őket.

Létrehozhat például egy olyan sablont, amely hálózati erőforrásokat telepít, és egy másik, a tárolási erőforrásokat telepítő specifikációt. Az ARM-sablonokban erre a két sablonra vonatkozó specifikációra lehet hivatkozni, amikor hálózati vagy tárolási erőforrásokat kell konfigurálnia.

A következő példa hasonló a korábbi példához, de a tulajdonsággal hivatkozik a `id` sablonra, és nem a `relativePath` tulajdonságot a helyi sablonhoz való hivatkozáshoz. Használja az `2020-06-01` API-verziót a központi telepítések erőforrásához. A példában a sablon specifikációi egy **templateSpecsRG**nevű erőforráscsoporthoz tartoznak.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  ...
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "networkingDeployment",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'networkingSpec', '1.0')]"
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "storageDeployment",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0')]"
        }
      }
    }
  ],
  "outputs": {}
}
```

A sablon specifikációinak összekapcsolásával kapcsolatos további információkért lásd [: oktatóanyag: sablonra vonatkozó spec telepítése csatolt sablonként](template-specs-deploy-linked-template.md).

## <a name="versioning"></a>Verziókezelés

A sablon specifikációjának létrehozásakor meg kell adnia a verziószámát. A sablon kódjának megismétlése után frissítheti a meglévő verziót (gyorsjavítások esetén), vagy közzétehet egy új verziót. A verzió egy szöveges karakterlánc. Dönthet úgy, hogy bármely verziószámozási rendszer követését választja, beleértve a szemantikai verziószámozást is. A sablon specifikációjának felhasználói megadhatják a telepítéskor használni kívánt verziószámot.

## <a name="next-steps"></a>További lépések

* A sablon specifikációjának létrehozásához és üzembe helyezéséhez lásd: gyors útmutató [: sablon létrehozása és üzembe helyezése specifikáció](quickstart-create-template-specs.md).

* A sablonokhoz tartozó specifikációk összekapcsolásával kapcsolatos további információkért lásd [: oktatóanyag: sablon létrehozása spec csatolt sablonokkal](template-specs-create-linked.md).

* További információ a sablon specifikációjának csatolt sablonként való üzembe helyezéséről [: oktatóanyag: sablon központi telepítése csatolt sablonként](template-specs-deploy-linked-template.md).
