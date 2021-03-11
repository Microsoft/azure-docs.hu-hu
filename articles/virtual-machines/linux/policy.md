---
title: Biztonság kikényszerítása az Azure-beli Linux rendszerű virtuális gépek házirendjeivel
description: Szabályzat alkalmazása Azure Resource Manager Linux rendszerű virtuális gépre
author: mimckitt
ms.service: virtual-machines
ms.collection: linux
ms.subservice: security
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 08/02/2017
ms.author: mimckitt
ms.openlocfilehash: c1fb80001f0669672e9f6e8a567685b799314df1
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102549821"
---
# <a name="apply-policies-to-linux-vms-with-azure-resource-manager"></a>Szabályzatok alkalmazása Linux rendszerű virtuális gépekre Azure Resource Manager
A házirendek használatával a szervezetek különböző egyezményeket és szabályokat alkalmazhatnak a vállalaton belül. A kívánt viselkedés kényszerítésével csökkentheti a kockázatokat, miközben hozzájárul a szervezet sikeréhez. Ebben a cikkben bemutatjuk, hogyan használhatók Azure Resource Manager házirendek a szervezet Virtual Machines kívánt viselkedésének meghatározásához.

A szabályzatok bevezetését a [Mi az Azure Policy?](../../governance/policy/overview.md)című témakörben tekintheti meg.

## <a name="permitted-virtual-machines"></a>Engedélyezett Virtual Machines
Annak biztosítása érdekében, hogy a szervezete virtuális gépei kompatibilisek legyenek az alkalmazásokkal, korlátozhatja a megengedett operációs rendszereket. A következő házirend-példában csak az Ubuntu 14.04.2-LTS Virtual Machines létrehozását engedélyezi.

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "in": [
          "Microsoft.Compute/disks",
          "Microsoft.Compute/virtualMachines",
          "Microsoft.Compute/VirtualMachineScaleSets"
        ]
      },
      {
        "not": {
          "allOf": [
            {
              "field": "Microsoft.Compute/imagePublisher",
              "in": [
                "Canonical"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "UbuntuServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "14.04.2-LTS"
              ]
            },
            {
              "field": "Microsoft.Compute/imageVersion",
              "in": [
                "latest"
              ]
            }
          ]
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Használjon helyettesítő kártyát az előző szabályzat módosításához az Ubuntu LTS-képek engedélyezéséhez: 

```json
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*LTS"
}
```

További információ a szabályzat mezőiről: [házirend-aliasok](../../governance/policy/concepts/definition-structure.md#aliases).

## <a name="managed-disks"></a>Felügyelt lemezek

A felügyelt lemezek használatának megköveteléséhez használja a következő szabályzatot:

```json
{
  "if": {
    "anyOf": [
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachines"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/osDisk.uri",
            "exists": true
          }
        ]
      },
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/VirtualMachineScaleSets"
          },
          {
            "anyOf": [
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osDisk.vhdContainers",
                "exists": true
              },
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl",
                "exists": true
              }
            ]
          }
        ]
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="images-for-virtual-machines"></a>Virtual Machines rendszerképek

Biztonsági okokból megkövetelheti, hogy csak a jóváhagyott egyéni lemezképek legyenek telepítve a környezetében. Megadhatja vagy a jóváhagyott képeket tartalmazó erőforráscsoportot, vagy a megadott jóváhagyott képeket.

Az alábbi példában egy jóváhagyott erőforráscsoporthoz tartozó képek szükségesek:

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in": [
                    "Microsoft.Compute/virtualMachines",
                    "Microsoft.Compute/VirtualMachineScaleSets"
                ]
            },
            {
                "not": {
                    "field": "Microsoft.Compute/imageId",
                    "contains": "resourceGroups/CustomImage"
                }
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
} 
```

A következő példa a jóváhagyott rendszerkép-azonosítókat adja meg:

```json
{
    "field": "Microsoft.Compute/imageId",
    "in": ["{imageId1}","{imageId2}"]
}
```

## <a name="virtual-machine-extensions"></a>Virtuálisgép-bővítmények

Előfordulhat, hogy meg szeretné tiltani bizonyos típusú bővítmények használatát. Előfordulhat például, hogy egy bővítmény nem kompatibilis bizonyos egyéni virtuálisgép-rendszerképekkel. Az alábbi példa azt szemlélteti, hogyan lehet letiltani egy adott bővítményt. A közzétevőt és a típust használja a blokkolni kívánt bővítmény meghatározásához.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "equals": "{extension-type}"

      }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```


## <a name="next-steps"></a>Következő lépések
* A házirend-szabály meghatározása után (ahogy az előző példákban is látható) létre kell hoznia a házirend-definíciót, és hozzá kell rendelnie egy hatókörhöz. A hatókör lehet előfizetés, erőforráscsoport vagy erőforrás. A szabályzatok hozzárendeléséhez tekintse meg az erőforrás-házirendek hozzárendelésére [és kezelésére szolgáló Azure Portal használata](../../governance/policy/assign-policy-portal.md), a [PowerShell használatával házirendek hozzárendelését](../../governance/policy/assign-policy-powershell.md), vagy az [Azure CLI használatával házirendek hozzárendelését](../../governance/policy/assign-policy-azurecli.md).
* Az erőforrás-házirendek bemutatása: [Mi az Azure Policy?](../../governance/policy/overview.md)
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.
