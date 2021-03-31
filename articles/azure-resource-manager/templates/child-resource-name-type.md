---
title: Gyermekek erőforrásai a sablonokban
description: Leírja, hogyan kell beállítani a gyermek-erőforrások nevét és típusát egy Azure Resource Manager sablonban (ARM-sablon).
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: a950d72751b829c0a2aa3ba5ca27316a0544d9cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97963912"
---
# <a name="set-name-and-type-for-child-resources"></a>Adja meg a gyermek erőforrások nevét és típusát

A gyermek erőforrások olyan erőforrások, amelyek csak egy másik erőforrás kontextusában vannak. A virtuálisgép- [bővítmények](/azure/templates/microsoft.compute/virtualmachines/extensions) például nem létezhetnek [virtuális gép](/azure/templates/microsoft.compute/virtualmachines)nélkül. A bővítmény erőforrás a virtuális gép gyermeke.

Minden szülő erőforrás csak bizonyos típusú erőforrásokat fogad el alárendelt erőforrásként. A gyermek erőforrás típusa tartalmazza a szülő erőforrás típusát. Például `Microsoft.Web/sites/config` `Microsoft.Web/sites/extensions` a és a alárendelt erőforrásai `Microsoft.Web/sites` . Az elfogadott erőforrástípusok a szülő erőforrás [sablon sémájában](https://github.com/Azure/azure-resource-manager-schemas) vannak megadva.

Egy Azure Resource Manager sablonban (ARM-sablon) a gyermek erőforrást a szülő erőforráson belül vagy a szülő erőforráson kívül is megadhatja. Az alábbi példa a szülő erőforrás erőforrások tulajdonságában található gyermek erőforrást mutatja be.

```json
"resources": [
  {
    <parent-resource>
    "resources": [
      <child-resource>
    ]
  }
]
```

A gyermek erőforrások csak öt szint mélységben adhatók meg.

A következő példa a alárendelt erőforrást a szülő erőforráson kívül mutatja. Ezt a módszert akkor érdemes használni, ha a fölérendelt erőforrás nem ugyanabban a sablonban van telepítve, vagy ha a [Másolás](copy-resources.md) használatával több alárendelt erőforrást szeretne létrehozni.

```json
"resources": [
  {
    <parent-resource>
  },
  {
    <child-resource>
  }
]
```

Az erőforrás neve és típusa számára megadott értékek attól függően változnak, hogy a gyermek erőforrás a szülő erőforráson belül vagy kívül van-e definiálva.

## <a name="within-parent-resource"></a>Szülő erőforráson belül

Ha a szülő erőforrástípus definiálja, a típus és a név értékeket perjel nélkül formázhatja egyetlen szóval.

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

Az alábbi példa egy virtuális hálózatot és egy alhálózatot mutat be. Figyelje meg, hogy az alhálózat szerepel a virtuális hálózathoz tartozó erőforrások tömbben. A név a **Subnet1** értékre van állítva, a típus pedig **alhálózatokra** van beállítva. A gyermek erőforrás a szülő erőforrástól függőként van megjelölve, mert a szülő erőforrásnak léteznie kell a gyermek erőforrás üzembe helyezése előtt.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    },
    "resources": [
      {
        "type": "subnets",
        "apiVersion": "2018-10-01",
        "name": "Subnet1",
        "location": "[parameters('location')]",
        "dependsOn": [
          "VNet1"
        ],
        "properties": {
          "addressPrefix": "10.0.0.0/24"
        }
      }
    ]
  }
]
```

A teljes erőforrás-típus még mindig `Microsoft.Network/virtualNetworks/subnets` . Nem adja meg, `Microsoft.Network/virtualNetworks/` mert a szülő erőforrástípus feltételezi.

A gyermek erőforrás neve **Subnet1** értékre van állítva, de a teljes név tartalmazza a szülő nevét. Nem biztosít **VNet1** , mert a szülő erőforrásból feltételezhető.

## <a name="outside-parent-resource"></a>Fölérendelt erőforráson kívül

Ha a szülő erőforráson kívül van definiálva, a típust és a perjelet formázva adja meg a szülő típusát és nevét.

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

Az alábbi példa egy virtuális hálózatot és alhálózatot mutat be, amely a legfelső szinten van meghatározva. Figyelje meg, hogy az alhálózat nem része a virtuális hálózathoz tartozó erőforrások tömbnek. A név értéke **VNet1/Subnet1** , és a típus értéke: `Microsoft.Network/virtualNetworks/subnets` . A gyermek erőforrás a szülő erőforrástól függőként van megjelölve, mert a szülő erőforrásnak léteznie kell a gyermek erőforrás üzembe helyezése előtt.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    }
  },
  {
    "type": "Microsoft.Network/virtualNetworks/subnets",
    "apiVersion": "2018-10-01",
    "location": "[parameters('location')]",
    "name": "VNet1/Subnet1",
    "dependsOn": [
      "VNet1"
    ],
    "properties": {
      "addressPrefix": "10.0.0.0/24"
    }
  }
]
```

## <a name="next-steps"></a>Következő lépések

* Az ARM-sablonok létrehozásával kapcsolatos további tudnivalókért lásd [az ARM-sablonok szerkezetének és szintaxisának megismerését](template-syntax.md)ismertető témakört.
* Ha többet szeretne megtudni az erőforrás nevének formátumáról az erőforrásra való hivatkozáskor, tekintse meg a [Reference függvényt](template-functions-resource.md#reference).
