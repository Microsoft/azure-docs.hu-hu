---
title: Meglévő virtuális hálózat referenciája egy Azure Scale set-sablonban
description: Megtudhatja, hogyan adhat hozzá virtuális hálózatot egy meglévő Azure virtuálisgép-méretezési csoport sablonhoz
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: networking
ms.date: 03/30/2021
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: f15fddc54f4b7c5a03843da1bcc11d1991b70d02
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076666"
---
# <a name="reference-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Meglévő virtuális hálózat referenciája egy Azure Scale set-sablonban

Ebből a cikkből megtudhatja, hogyan módosíthatja az [alapszintű méretezési csoport sablonját](virtual-machine-scale-sets-mvss-start.md) egy meglévő virtuális hálózatra való központi telepítéshez egy új létrehozása helyett.

## <a name="prerequisites"></a>Előfeltételek

Egy korábbi cikkben egy [alapszintű méretezési csoport sablont](virtual-machine-scale-sets-mvss-start.md)hoztunk létre. Szüksége lesz erre a korábbi sablonra, hogy módosítani tudja egy olyan sablon létrehozásához, amely egy méretezési csoport központi telepítését végzi egy meglévő virtuális hálózatban.

## <a name="identify-subnet"></a>Alhálózat azonosítása

Először adjon hozzá egy `subnetId` paramétert. A rendszer átadja ezt a karakterláncot a méretezési csoport konfigurációjához, így a méretezési csoport azonosítja az előre létrehozott alhálózatot a virtuális gépek üzembe helyezéséhez. A karakterláncnak a következő formátumúnak kell lennie:

`/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`


Ha például a méretezési csoportot egy meglévő `myvnet` , névvel, alhálózattal `mysubnet` , erőforráscsoporthoz és előfizetéssel rendelkező virtuális hálózatra szeretné telepíteni `myrg` `00000000-0000-0000-0000-000000000000` , a következő lesz: 

`/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

```diff
      },
      "adminPassword": {
        "type": "securestring"
+    },
+    "subnetId": {
+      "type": "string"
      }
    },
```

## <a name="delete-extra-virtual-network-resource"></a>További virtuális hálózati erőforrás törlése

Ezután törölje a virtuális hálózati erőforrást a `resources` tömbből, mivel meglévő virtuális hálózatot használ, és nem kell újat telepítenie.

```diff
    "variables": {},
    "resources": [
-    {
-      "type": "Microsoft.Network/virtualNetworks",
-      "name": "myVnet",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "2018-11-01",
-      "properties": {
-        "addressSpace": {
-          "addressPrefixes": [
-            "10.0.0.0/16"
-          ]
-        },
-        "subnets": [
-          {
-            "name": "mySubnet",
-            "properties": {
-              "addressPrefix": "10.0.0.0/16"
-            }
-          }
-        ]
-      }
-    },
```
## <a name="remove-dependency-clause"></a>Függőségi záradék eltávolítása

A virtuális hálózat már létezik a sablon üzembe helyezése előtt, így nem kell megadnia egy `dependsOn` záradékot a méretezési csoportból a virtuális hálózatra. Törölje a következő sorokat:

```diff
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets",
        "name": "myScaleSet",
        "location": "[resourceGroup().location]",
        "apiVersion": "2019-03-01",
-      "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
-      ],
        "sku": {
          "name": "Standard_A1",
          "capacity": 2
```

## <a name="pass-subnet-parameter"></a>Alhálózat paraméterének továbbítása

Végül adja `subnetId` meg a felhasználó által beállított paramétert (a használata helyett `resourceId` egy vnet azonosítójának lekérése ugyanazon az üzemelő példányon, amely az alapszintű, életképes méretezési csoport sablonja).

```diff
                        "name": "myIpConfig",
                        "properties": {
                          "subnet": {
-                          "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
+                          "id": "[parameters('subnetId')]"
                          }
                        }
                      }
```


## <a name="next-steps"></a>Következő lépések

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
