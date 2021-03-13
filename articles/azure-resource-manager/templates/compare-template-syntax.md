---
title: A JSON-és a bicep Azure Resource Manager-sablonok szintaxisának összehasonlítása
description: Összehasonlítja a JSON-val és a bicep-vel fejlesztett Azure Resource Manager sablonokat, és bemutatja, hogyan lehet váltani a nyelvek között.
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 85f85e66e69eede68bab847e4bc68514e65115eb
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418045"
---
# <a name="comparing-json-and-bicep-for-templates"></a>A JSON és a bicep összehasonlítása a sablonokkal

Ez a cikk a bicep szintaxist hasonlítja össze a Azure Resource Manager-sablonok JSON-szintaxisával (ARM-sablonok). A legtöbb esetben a bicep olyan szintaxist biztosít, amely kevésbé részletes, mint a JSON-ban.

## <a name="syntax-equivalents"></a>Szintaxissal egyenértékű

Ha ismeri a JSON-t az ARM-sablonok fejlesztéséhez, használja az alábbi táblázatot a bicep megfelelő szintaxisának megismeréséhez.

| Eset | Bicep | JSON |
| -------- | ------------ | ----- |
| Kifejezés készítése | `func()` | `"[func()]"` |
| Paraméter értékének beolvasása | `exampleParameter` | `[parameters('exampleParameter'))]` |
| Változó értékének beolvasása | `exampleVar` | `[variables('exampleVar'))]` |
| Sztringek összefűzése | `'${namePrefix}-vm'` | `[concat(parameters('namePrefix'), '-vm')]` |
| Erőforrás-tulajdonság beállítása | `sku: '2016-Datacenter'` | `"sku": "2016-Datacenter",` |
| A logikai és a | `isMonday && isNovember` | `[and(parameter('isMonday'), parameter('isNovember'))]` |
| Erőforrás-azonosító beolvasása a sablonban | `nic1.id` | `[resourceId('Microsoft.Network/networkInterfaces', variables('nic1Name'))]` |
| Tulajdonság beolvasása a sablonban lévő erőforrásból | `diagsAccount.properties.primaryEndpoints.blob` | `[reference(resourceId('Microsoft.Storage/storageAccounts', variables('diagStorageAccountName'))).primaryEndpoints.blob]` |
| Érték feltételes beállítása | `isMonday ? 'valueIfTrue' : 'valueIfFalse'` | `[if(parameters('isMonday'), 'valueIfTrue', 'valueIfFalse')]` |
| Megoldás elkülönítése több fájlba | Modulok használata | Csatolt sablonok használata |
| A központi telepítés cél hatókörének beállítása | `targetScope = 'subscription'` | `"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#"` |
| Függőség beállítása | A függőségek automatikus észlelésére vagy a függőségek manuális beállítására támaszkodhat `dependsOn: [ stg ]` | `"dependsOn": ["[resourceId('Microsoft.Storage/storageAccounts', 'parameters('storageAccountName'))]"]` |
| Erőforrás-deklaráció | `resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {...}` | `"resources": [ { "type": "Microsoft.Compute/virtualMachines", "apiVersion": "2020-06-01", ... } ]` |

## <a name="recommendations"></a>Javaslatok

* Ha lehetséges, kerülje a [hivatkozási](template-functions-resource.md#reference) és a [resourceId](template-functions-resource.md#resourceid) függvények használatát a bicep fájlban. Ha ugyanarra a bicep-telepítésre hivatkozik egy erőforrásra, használja helyette az erőforrás-azonosítót. Ha például telepített egy erőforrást a bicep-fájlban `stg` erőforrás-azonosítóként, használja a (z) szintaxist, `stg.id` vagy adja `stg.properties.primaryEndpoints.blob` meg a tulajdonság értékeit. Az erőforrás-azonosító használatával implicit függőséget hoz létre az erőforrások között. Nem kell explicit módon beállítania a függőséget a dependsOn tulajdonsággal.
* Használjon konzisztens burkolatot az azonosítók számára. Ha nem tudja biztosan, hogy milyen típusú burkolatot szeretne használni, próbálja ki a Camel burkolatot. Például: `param myCamelCasedParameter string`.
* Csak akkor adjon meg egy leírást a paraméterhez, ha a Leírás alapvető információkat biztosít a felhasználóknak. `//`Néhány információhoz megjegyzéseket is használhat.

## <a name="next-steps"></a>Következő lépések

* További információ a bicep: [bicep oktatóanyag](./bicep-tutorial-create-first-bicep.md).
* A sablonok nyelvek közötti átalakításával kapcsolatos további információkért lásd: [ARM-sablonok konvertálása JSON és bicep között](bicep-decompile.md).
