---
title: Felhasználó által definiált függvények a sablonokban
description: Ismerteti, hogyan lehet felhasználó által definiált függvényeket definiálni és használni egy Azure Resource Manager sablonban (ARM-sablon).
ms.topic: conceptual
ms.date: 04/12/2021
ms.openlocfilehash: 802ac6e1c70db8caa58aecde3efe814430c79682
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307775"
---
# <a name="user-defined-functions-in-arm-template"></a>Felhasználó által definiált függvények az ARM-sablonban

A sablonon belül létrehozhat saját függvényeket is. Ezek a függvények a sablonban használhatók. A felhasználó által definiált függvények eltérhetnek a sablonon belül automatikusan elérhető [szabványos sablon-függvényektől](template-functions.md) . Saját függvényeket hozhat létre, ha olyan bonyolult kifejezésekkel rendelkezik, amelyeket a sablonban ismételten használnak.

Ez a cikk azt ismerteti, hogyan adhat hozzá felhasználó által definiált függvényeket a Azure Resource Manager-sablonhoz (ARM-sablon).

## <a name="define-the-function"></a>A függvény megadása

A függvények névtér-értéket igényelnek, így elkerülhető a sablon függvényekkel való névütközés. A következő példa egy olyan függvényt mutat be, amely egyedi nevet ad vissza:

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

## <a name="use-the-function"></a>A függvény használata

Az alábbi példa egy olyan sablont mutat be, amely egy felhasználó által definiált függvényt tartalmaz a Storage-fiókok egyedi nevének beszerzéséhez. A sablon egy nevű paraméterrel rendelkezik, `storageNamePrefix` amely paraméterként a függvénynek lesz átadva.

```json
{
 "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
 "contentVersion": "1.0.0.0",
 "parameters": {
   "storageNamePrefix": {
     "type": "string",
     "maxLength": 11
   }
 },
 "functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
 "resources": [
   {
     "type": "Microsoft.Storage/storageAccounts",
     "apiVersion": "2019-04-01",
     "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
     "location": "South Central US",
     "sku": {
       "name": "Standard_LRS"
     },
     "kind": "StorageV2",
     "properties": {
       "supportsHttpsTrafficOnly": true
     }
   }
 ]
}
```

Az üzembe helyezés során a paraméter a következő `storageNamePrefix` függvénynek lesz átadva:

* A sablon definiál egy nevű paramétert `storageNamePrefix` .
* A függvény a funkciót használja, `namePrefix` mert csak a függvényben definiált paramétereket használhatja. További információ: [korlátozások](#limitations).
* A sablon `resources` szakaszban az `name` elem a függvényt használja, és átadja az `storageNamePrefix` értéket a függvénynek `namePrefix` .

## <a name="limitations"></a>Korlátozások

A felhasználói függvények meghatározásakor bizonyos korlátozások vonatkoznak:

* A függvény nem fér hozzá a változókhoz.
* A függvény csak a függvényben definiált paramétereket tudja használni. Ha a [Parameters](template-functions-deployment.md#parameters) függvényt egy felhasználó által definiált függvényen belül használja, akkor a függvény paraméterei vannak korlátozva.
* A függvény nem hívhat meg más, felhasználó által definiált függvényeket.
* A függvény nem tudja használni a [Reference](template-functions-resource.md#reference) függvényt vagy a [List](template-functions-resource.md#list) függvények egyikét sem.
* A függvény nem tudja használni a [dateTimeAdd](template-functions-date.md#datetimeadd) függvényt.
* A függvény paraméterei nem rendelkezhetnek alapértelmezett értékekkel.

## <a name="next-steps"></a>Következő lépések

* A felhasználó által definiált függvények elérhető tulajdonságainak megismeréséhez tekintse meg [az ARM-sablonok szerkezetének és szintaxisának megismerését](template-syntax.md)ismertető témakört.
* Az elérhető sablon-függvények listáját az [ARM-sablon függvények](template-functions.md)című részben tekintheti meg.
