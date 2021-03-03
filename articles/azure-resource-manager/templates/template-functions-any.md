---
title: Sablon functions – bármely
description: Leírja a bicep-ban a típusok átalakításához elérhető bármely funkciót.
ms.topic: conceptual
author: tfitzmac
ms.author: tomfitz
ms.service: azure-resource-manager
ms.subservice: templates
ms.date: 03/02/2021
ms.openlocfilehash: b0cb51c9a79d1100de7f1ef32fe326eddcdd6dcc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745856"
---
# <a name="any-function-for-bicep"></a>Bármilyen függvény a bicep

A bicep egy hívott függvényt támogat `any()` a bicep Type rendszer típusú hibák feloldásához. Ezt a függvényt akkor használja, ha a megadott érték formátuma nem egyezik meg a típus rendszerével. Ha például a tulajdonsághoz meg kell adni egy számot, de karakterláncként kell megadni, például: `'0.5'` . A `any()` függvény használatával letilthatja a típusrendszer által jelentett hibát.

Ez a függvény nem létezik a Azure Resource Manager sablon futtatókörnyezetében. Ezt csak a bicep használja, és a létrehozott sablonhoz nem a JSON-ben van kibocsátva.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="any"></a>bármelyik

`any(value)`

Egy olyan értéket ad vissza, amely bármilyen adattípussal kompatibilis.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| érték | Igen | Minden típus | A kompatibilis típusra konvertálandó érték. |

### <a name="return-value"></a>Visszatérési érték

Egy olyan űrlap értéke, amely bármilyen adattípussal kompatibilis.

### <a name="examples"></a>Példák

A következő példa azt mutatja be, hogyan használható a `any()` függvény a numerikus értékek karakterláncként való megadására.

```bicep
resource wpAci 'microsoft.containerInstance/containerGroups@2019-12-01' = {
  name: 'wordpress-containerinstance'
  location: location
  properties: {
    containers: [
      {
        name: 'wordpress'
        properties: {
          ...
          resources: {
            requests: {
              cpu: any('0.5')
              memoryInGB: any('0.7')
            }
          }
        }
      }
    ]
  }
}
```

A függvény bármilyen hozzárendelt értékkel működik a bicep. A következő példa `any()` egy Ternáris kifejezést használ argumentumként.  

```bicep
publicIPAddress: any((pipId == '') ? null : {
  id: pipId
})
```

## <a name="next-steps"></a>Következő lépések

A függvény összetettebb felhasználási módjai `any()` a következő példákban találhatók:

* [Adott neveket igénylő alárendelt erőforrások](https://github.com/Azure/bicep/blob/main/docs/examples/201/api-management-create-all-resources/main.bicep#L246)
* [Egy erőforrás-tulajdonság nincs definiálva az erőforrás típusában, annak ellenére, hogy létezik](https://github.com/Azure/bicep/blob/main/docs/examples/201/log-analytics-with-solutions-and-diagnostics/main.bicep#L26)

