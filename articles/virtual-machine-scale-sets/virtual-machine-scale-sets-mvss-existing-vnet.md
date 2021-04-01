---
title: Meglévő virtuális hálózat referenciája egy Azure Scale set-sablonban
description: Megtudhatja, hogyan adhat hozzá virtuális hálózatot egy meglévő Azure virtuálisgép-méretezési csoport sablonhoz
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: networking
ms.date: 04/26/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: fab6e6742fa43e1e38ee661b67896ae4aa11b3ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "83124822"
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Hivatkozás hozzáadása egy meglévő virtuális hálózathoz egy Azure méretezési csoport sablonjában

Ebből a cikkből megtudhatja, hogyan módosíthatja az [alapszintű méretezési csoport sablonját](virtual-machine-scale-sets-mvss-start.md) egy meglévő virtuális hálózatra való központi telepítéshez egy új létrehozása helyett.

## <a name="change-the-template-definition"></a>A sablon definíciójának módosítása

Egy [korábbi cikkben](virtual-machine-scale-sets-mvss-start.md) egy alapszintű méretezési csoport sablont hoztunk létre. Most ezt a korábbi sablont fogjuk használni, és úgy módosítjuk, hogy olyan sablont hozzon létre, amely egy méretezési csoport központi telepítését végzi egy meglévő virtuális hálózatban. 

Először adjon hozzá egy `subnetId` paramétert. A rendszer átadja ezt a karakterláncot a méretezési csoport konfigurációjához, így a méretezési csoport azonosítja az előre létrehozott alhálózatot a virtuális gépek üzembe helyezéséhez. A karakterláncnak a következő formátumúnak kell lennie: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`

Ha például a méretezési csoportot egy meglévő `myvnet` , névvel, alhálózattal `mysubnet` , erőforráscsoporthoz és előfizetéssel rendelkező virtuális hálózatra szeretné telepíteni `myrg` `00000000-0000-0000-0000-000000000000` , a következő lesz: `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet` .

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

A virtuális hálózat már létezik a sablon üzembe helyezése előtt, így nem kell dependsOn záradékot megadnia a méretezési csoportból a virtuális hálózatra. Törölje a következő sorokat:

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
