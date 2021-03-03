---
title: Azure Resource Manager sablonok konvertálása JSON és bicep között
description: Összehasonlítja a JSON-val és a bicep-vel fejlesztett Azure Resource Manager sablonokat.
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: 9388ed50f13d6885d0a0668b61a9141dae375244
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745105"
---
# <a name="comparing-json-and-bicep-for-templates"></a>A JSON és a bicep összehasonlítása a sablonokkal

Ez a cikk a bicep szintaxist hasonlítja össze a Azure Resource Manager-sablonok JSON-szintaxisával (ARM-sablonok). A legtöbb esetben a bicep olyan szintaxist biztosít, amely kevésbé részletes, mint a JSON-ban.

## <a name="syntax-equivalents"></a>Szintaxissal egyenértékű

Ha ismeri a JSON-t az ARM-sablonok fejlesztéséhez, használja az alábbi táblázatot a bicep megfelelő szintaxisának megismeréséhez.

| Eset | ARM-sablon | Bicep |
| -------- | ------------ | ----- |
| Kifejezés készítése | `"[func()]"` | `func()` |
| Paraméter értékének beolvasása | `[parameters('exampleParameter'))]` | `exampleParameter` |
| Változó értékének beolvasása | `[variables('exampleVar'))]` | `exampleVar` |
| Sztringek összefűzése | `[concat(parameters('namePrefix'), '-vm')]` | `'${namePrefix}-vm'` |
| Erőforrás-tulajdonság beállítása | `"sku": "2016-Datacenter",` | `sku: '2016-Datacenter'` |
| A logikai és a | `[and(parameter('isMonday'), parameter('isNovember'))]` | `isMonday && isNovember` |
| Erőforrás-azonosító beolvasása a sablonban | `[resourceId('Microsoft.Network/networkInterfaces', variables('nic1Name'))]` | `nic1.id` |
| Tulajdonság beolvasása a sablonban lévő erőforrásból | `[reference(resourceId('Microsoft.Storage/storageAccounts', variables('diagStorageAccountName'))).primaryEndpoints.blob]` | `diagsAccount.properties.primaryEndpoints.blob` |
| Érték feltételes beállítása | `[if(parameters('isMonday'), 'valueIfTrue', 'valueIfFalse')]` | `isMonday ? 'valueIfTrue' : 'valueIfFalse'` |
| Megoldás elkülönítése több fájlba | Csatolt sablonok használata | Modulok használata |
| A központi telepítés cél hatókörének beállítása | `"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#"` | `targetScope = 'subscription'` |
| Függőség beállítása | `"dependsOn": ["[resourceId('Microsoft.Storage/storageAccounts', 'parameters('storageAccountName'))]"]` | A függőségek automatikus észlelésére vagy a függőségek manuális beállítására támaszkodhat `dependsOn: [ stg ]` |

Az erőforrás típusának és verziójának deklarálása esetén használja a következőt a bicep:

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  ...
}
```

Az egyenértékű szintaxis helyett a JSON-ben:

```json
"resources": [
  {
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2020-06-01",
    ...
  }
]
```

## <a name="recommendations"></a>Javaslatok

* Ha lehetséges, kerülje a [hivatkozási](template-functions-resource.md#reference) és a [resourceId](template-functions-resource.md#resourceid) függvények használatát a bicep fájlban. Ha ugyanarra a bicep-telepítésre hivatkozik egy erőforrásra, használja helyette az erőforrás-azonosítót. Ha például telepített egy erőforrást a bicep-fájlban `stg` erőforrás-azonosítóként, használja a (z) szintaxist, `stg.id` vagy adja `stg.properties.primaryEndpoints.blob` meg a tulajdonság értékeit. Az erőforrás-azonosító használatával implicit függőséget hoz létre az erőforrások között. Nem kell explicit módon beállítania a függőséget a dependsOn tulajdonsággal.
* Használjon konzisztens burkolatot az azonosítók számára. Ha nem tudja biztosan, hogy milyen típusú burkolatot szeretne használni, próbálja ki a Camel burkolatot. Például: `param myCamelCasedParameter string`.
* Csak akkor adjon meg egy leírást a paraméterhez, ha a Leírás alapvető információkat biztosít a felhasználóknak. `//`Néhány információhoz megjegyzéseket is használhat.

## <a name="decompile-json-to-bicep"></a>JSON lefordítása a bicep

A bicep CLI olyan parancsot biztosít, amellyel lefordíthatja a meglévő ARM-sablonokat egy bicep-fájlba. A JSON-fájlok defordításához használja a következőt: `bicep decompile "path/to/file.json"`

Ez a parancs kiindulási pontot biztosít a bicep authoring számára, de a parancs nem működik minden sablon esetében. Előfordulhat, hogy a parancs végrehajtása sikertelen, vagy a defordítás után problémákat kell kijavítania. Jelenleg a parancs a következő korlátozásokkal rendelkezik:

* A másolási hurkokat használó sablonok nem fordíthatók le.
* A beágyazott sablonok csak akkor fordíthatók le, ha a belső kifejezés kiértékelési hatókörét használják.

Exportálhatja az erőforráscsoport sablonját, majd átadhatja azt közvetlenül a bicep defordítási parancsnak. Az alábbi példa egy exportált sablon defordítását szemlélteti.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group export --name "your_resource_group_name" > main.json
bicep decompile main.json
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Export-AzResourceGroup -ResourceGroupName "your_resource_group_name" -Path ./main.json
bicep decompile main.json
```

# <a name="portal"></a>[Portál](#tab/azure-portal)

[Exportálja a sablont](export-template-portal.md) a portálon keresztül. Használja a `bicep decompile <filename>` fájlt a letöltött fájlon.

---

## <a name="build-json-from-bicep"></a>JSON létrehozása a Bicepből

A bicep CLI-vel a bicep a JSON-re alakítható parancs is elérhető. JSON-fájl létrehozásához használja a következőt: `bicep build "path/to/file.json"`

## <a name="side-by-side-view"></a>Párhuzamos nézet

A [bicep játszótér](https://aka.ms/bicepdemo) lehetővé teszi az egyenértékű JSON-és bicep-fájlok megtekintését egymás mellett. Kiválaszthat egy minta sablont mindkét verzió megjelenítéséhez. Vagy válassza a `Decompile` saját JSON-sablonjának feltöltését, és tekintse meg az egyenértékű bicep-fájlt.

## <a name="next-steps"></a>Következő lépések

További információ a bicep projektről: [Project bicep](https://github.com/Azure/bicep).
