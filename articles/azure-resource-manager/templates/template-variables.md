---
title: Változók a sablonokban
description: Leírja, hogyan lehet változókat definiálni egy Azure Resource Manager sablonban (ARM-sablon) és a bicep-fájlban.
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: e00a9e8e1801725707bac2abdc67512477e2cf07
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700337"
---
# <a name="variables-in-arm-templates"></a>Az ARM-sablonokban szereplő változók

Ez a cikk bemutatja, hogyan határozhat meg és használhat változókat a Azure Resource Manager-sablonban (ARM-sablon) vagy a bicep-fájlban. Változók használatával egyszerűsítheti a sablont. Ahelyett, hogy a sablonban megismételje a bonyolult kifejezéseket, Definiáljon egy változót, amely tartalmazza a bonyolult kifejezést. Ezt követően a változót igény szerint használhatja a sablonban.

A Resource Manager a telepítési műveletek megkezdése előtt feloldja a változókat. Mindenhol, ahol a sablonban a változót kell használni, a Resource Manager lecseréli a változót a feloldott értékre.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="define-variable"></a>Változó meghatározása

Változó meghatározásakor nem kell megadnia a változó [adattípusát](template-syntax.md#data-types) . Ehelyett adjon meg egy értéket vagy egy sablon kifejezést. A változó típusa a megoldott értékből lett kikövetkeztetve. A következő példa egy változót állít be egy karakterláncra.

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "stringVar": "example value"
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var stringVar = 'example value'
```

---

A változó létrehozásakor egy paraméter vagy egy másik változó értékét is használhatja.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "inputValue": {
    "defaultValue": "deployment parameter",
    "type": "string"
  }
},
"variables": {
  "stringVar": "myVariable",
  "concatToVar": "[concat(variables('stringVar'), '-addtovar') ]",
  "concatToParam": "[concat(parameters('inputValue'), '-addtoparam')]"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param inputValue string = 'deployment parameter'

var stringVar = 'myVariable'
var concatToVar =  '${stringVar}-addtovar'
var concatToParam = '${inputValue}-addtoparam'
```

---

A változó értékét a [template functions](template-functions.md) használatával hozhatja létre.

A következő példa egy karakterlánc-értéket hoz létre egy Storage-fiók nevéhez. Számos sablon függvényt használ a paraméterérték beolvasásához, és összefűzi azt egy egyedi karakterlánchoz.

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var storageName = '${toLower(storageNamePrefix)}${uniqueString(resourceGroup().id)}'
```

---

A JSON-sablonokban nem használható a változó deklarációjában a [Reference](template-functions-resource.md#reference) függvény vagy a [List](template-functions-resource.md#list) függvény bármelyike. Ezek a függvények egy erőforrás futásidejű állapotát kapják meg, és a változók feloldása előtt nem hajthatók végre.

A bicep-fájlokban a hivatkozás és a lista függvények érvényesek a változók deklarálása során.

## <a name="use-variable"></a>Változó használata

Az alábbi példa bemutatja, hogyan használható a változó egy erőforrás-tulajdonsághoz.

# <a name="json"></a>[JSON](#tab/json)

Egy JSON-sablonban a változó értékét a [változók](template-functions-deployment.md#variables) függvény használatával kell hivatkozni.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

Egy bicep-fájlban a változó értékének megadásával hivatkozhat a változóra.

```bicep
var storageName = '${toLower(storageNamePrefix)}${uniqueString(resourceGroup().id)}'

resource demoAccount 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageName
```

---

## <a name="example-template"></a>Példa sablonra

A következő sablon egyetlen erőforrást sem helyez üzembe. A különböző típusú változók deklarálása néhány módon megmutatható.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variables.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

A bicep jelenleg nem támogatja a hurkokat.

:::code language="bicep" source="~/resourcemanager-templates/azure-resource-manager/variables.bicep":::

---

## <a name="configuration-variables"></a>Konfigurációs változók

Meghatározhatja a környezet konfigurálásához kapcsolódó értékeket tároló változókat. A változót az értékekkel rendelkező objektumként definiálhatja. A következő példa egy olyan objektumot mutat be, amely két környezet, a **test** és a **Prod** értékeit tárolja. Az üzembe helyezés során adja át az alábbi értékek egyikét.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variablesconfigurations.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variablesconfigurations.bicep":::

---

## <a name="next-steps"></a>Következő lépések

* A változók elérhető tulajdonságainak megismeréséhez tekintse meg [az ARM-sablonok szerkezetének és szintaxisának megismerését](template-syntax.md)ismertető témakört.
* A változók létrehozásával kapcsolatos javaslatokért lásd: [ajánlott eljárások – változók](template-best-practices.md#variables).
* Egy hálózati biztonsági csoportra vonatkozó biztonsági szabályok hozzárendelésére szolgáló sablonhoz lásd: [hálózati biztonsági szabályok](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) és [paraméter fájl](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json).
