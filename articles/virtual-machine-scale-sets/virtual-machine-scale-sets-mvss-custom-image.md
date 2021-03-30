---
title: Egyéni rendszerkép referenciája egy Azure méretezési csoport sablonjában
description: Ismerje meg, hogyan adhat hozzá egyéni rendszerképet egy meglévő Azure virtuálisgép-méretezési csoport sablonhoz
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.date: 04/26/2018
ms.reviewer: akjosh
ms.custom: akjosh
ms.openlocfilehash: 5ed9ee79dde73e738417031b928a675ea913179c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "83124907"
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>Egyéni rendszerkép hozzáadása egy Azure méretezésicsoport-sablonhoz

Ez a cikk bemutatja, hogyan módosíthatja az [alapszintű méretezési csoport sablonját](virtual-machine-scale-sets-mvss-start.md) az egyéni lemezképből való üzembe helyezéshez.

## <a name="change-the-template-definition"></a>A sablon definíciójának módosítása
Egy [korábbi cikkben](virtual-machine-scale-sets-mvss-start.md) egy alapszintű méretezési csoport sablont hoztunk létre. Most ezt a korábbi sablont fogjuk használni, és módosítjuk egy olyan sablon létrehozásához, amely egy méretezési csoport üzembe helyezését végzi egy egyéni rendszerképből.  

### <a name="creating-a-managed-disk-image"></a>Felügyelt lemez rendszerképének létrehozása

Ha már rendelkezik egy egyéni felügyelt lemezképtel (egy típusú erőforrással `Microsoft.Compute/images` ), akkor kihagyhatja ezt a szakaszt.

Először adja hozzá a `sourceImageVhdUri` paramétert, amely az Azure Storage-ban található általánosított blob URI-ja, amely a-ból telepítendő egyéni lemezképet tartalmazza.


```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "sourceImageVhdUri": {
+      "type": "string",
+      "metadata": {
+        "description": "The source of the generalized blob containing the custom image"
+      }
     }
   },
   "variables": {},
```

Ezután adjon hozzá egy típusú erőforrást `Microsoft.Compute/images` , amely a felügyelt lemezkép az URI-n található általánosított blob alapján `sourceImageVhdUri` . Ennek a képnek ugyanabban a régióban kell lennie, mint az azt használó méretezési csoportnak. A rendszerkép tulajdonságainál adja meg az operációs rendszer típusát, a blob helyét (a `sourceImageVhdUri` paraméterből) és a Storage-fiók típusát:

```diff
   "resources": [
     {
+      "type": "Microsoft.Compute/images",
+      "apiVersion": "2019-03-01",
+      "name": "myCustomImage",
+      "location": "[resourceGroup().location]",
+      "properties": {
+        "storageProfile": {
+          "osDisk": {
+            "osType": "Linux",
+            "osState": "Generalized",
+            "blobUri": "[parameters('sourceImageVhdUri')]",
+            "storageAccountType": "Standard_LRS"
+          }
+        }
+      }
+    },
+    {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "location": "[resourceGroup().location]",

```

A méretezési csoport erőforrásban vegyen fel egy olyan `dependsOn` záradékot, amely az egyéni lemezképre hivatkozik, hogy a rendszer létrehozza a lemezképet, mielőtt a méretezési csoport megpróbálja telepíteni a lemezképből:

```diff
       "location": "[resourceGroup().location]",
       "apiVersion": "2019-03-01-preview",
       "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
+        "Microsoft.Network/virtualNetworks/myVnet",
+        "Microsoft.Compute/images/myCustomImage"
       ],
       "sku": {
         "name": "Standard_A1",

```

### <a name="changing-scale-set-properties-to-use-the-managed-disk-image"></a>Méretezési csoport tulajdonságainak módosítása a felügyelt lemezkép használatára

A `imageReference` méretezési csoporton belül a `storageProfile` platform-rendszerkép közzétevője, ajánlata, SKU-jának és verziójának megadása helyett adja meg az `id` `Microsoft.Compute/images` erőforrást:

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

Ebben a példában a `resourceId` függvénnyel lekérheti az ugyanabban a sablonban létrehozott rendszerkép erőforrás-azonosítóját. Ha korábban már létrehozta a felügyelt lemezképet, adja meg a rendszerkép AZONOSÍTÓját. Ennek az AZONOSÍTÓnak a következő formátumúnak kell lennie: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>` .


## <a name="next-steps"></a>Következő lépések

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
