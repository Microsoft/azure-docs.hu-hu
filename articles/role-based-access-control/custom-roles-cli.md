---
title: Egyéni Azure-szerepkörök létrehozása vagy frissítése az Azure CLI használatával – Azure RBAC
description: Útmutató egyéni Azure-szerepkörök listához, létrehozásához, frissítéséhez vagy törléséhez az Azure CLI és az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: d3d05ba65e0d3918f1651c36cd17700ebf74de76
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778336"
---
# <a name="create-or-update-azure-custom-roles-using-azure-cli"></a>Egyéni Azure-szerepkörök létrehozása vagy frissítése az Azure CLI használatával

> [!IMPORTANT]
> A felügyeleti csoport hozzáadása jelenleg `AssignableScopes` előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ha az [Azure beépített szerepkörei](built-in-roles.md) nem felelnie meg a szervezet igényeinek, létrehozhatja saját egyéni szerepköreit. Ez a cikk azt ismerteti, hogyan listából, hozhat létre, frissíthet vagy törölhet egyéni szerepköröket az Azure CLI használatával.

Az egyéni szerepkör létrehozásáról részletes oktatóanyagért lásd: Oktatóanyag: Egyéni Azure-szerepkör létrehozása az [Azure CLI használatával.](tutorial-custom-role-cli.md)

## <a name="prerequisites"></a>Előfeltételek

Egyéni szerepkörök létrehozásához a következőre van szükség:

- Egyéni szerepkörök létrehozására vonatkozó engedélyre, amely lehet például [Tulajdonos](built-in-roles.md#owner) vagy [Felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) Azure [CLI](/cli/azure/install-azure-cli)

## <a name="list-custom-roles"></a>Egyéni szerepkörök listázása

A hozzárendeléshez elérhető egyéni szerepkörök listához használja az [az role definition list et.](/cli/azure/role/definition#az_role_definition_list) Az alábbi példa felsorolja az aktuális előfizetés összes egyéni szerepkörét.

```azurecli
az role definition list --custom-role-only true --output json --query '[].{roleName:roleName, roleType:roleType}'
```

```json
[
  {
    "roleName": "My Management Contributor",
    "type": "CustomRole"
  },
  {
    "roleName": "My Service Reader Role",
    "type": "CustomRole"
  },
  {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole"
  }
]
```

## <a name="list-a-custom-role-definition"></a>Egyéni szerepkör-definíciók felsorolása

Egyéni szerepkör-definíció listához használja [az az role definition list et.](/cli/azure/role/definition#az_role_definition_list) Ez ugyanaz a parancs, mint amit a beépített szerepkörhöz használna.

```azurecli
az role definition list --name {roleName}
```

Az alábbi példa a *Virtuálisgép-kezelő szerepkör-definíciót* sorolja fel:

```azurecli
az role definition list --name "Virtual Machine Operator"
```

```json
[
  {
    "assignableScopes": [
      "/subscriptions/{subscriptionId}"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00000000-0000-0000-0000-000000000000",
    "name": "00000000-0000-0000-0000-000000000000",
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/*/read",
          "Microsoft.Network/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action",
          "Microsoft.Authorization/*/read",
          "Microsoft.ResourceHealth/availabilityStatuses/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Insights/diagnosticSettings/*",
          "Microsoft.Support/*"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Virtual Machine Operator",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

Az alábbi példa csak a Virtuálisgép-kezelő *szerepkörhöz szükséges műveleteket sorolja* fel:

```azurecli
az role definition list --name "Virtual Machine Operator" --output json --query '[].permissions[0].actions'
```

```json
[
  [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ]
]
```

## <a name="create-a-custom-role"></a>Egyéni szerepkör létrehozása

Egyéni szerepkör létrehozásához használja az [az role definition create et.](/cli/azure/role/definition#az_role_definition_create) A szerepkör-definíció lehet JSON-leírás vagy egy JSON-leírást tartalmazó fájl elérési útja.

```azurecli
az role definition create --role-definition {roleDefinition}
```

Az alábbi példa egy virtuálisgép-operátor nevű egyéni *szerepkört hoz létre.* Ez az egyéni szerepkör hozzáférést rendel a *Microsoft.Compute,* *a Microsoft.Storage* és a *Microsoft.Network* erőforrás-szolgáltatók összes olvasási művelethez, és hozzáférést rendel a virtuális gépek indításához, újraindításához és figyelése érdekében. Ez az egyéni szerepkör két előfizetésben használható. Ez a példa egy JSON-fájlt használ bemenetként.

vmoperator.jsbe

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}"
  ]
}
```

```azurecli
az role definition create --role-definition ~/roles/vmoperator.json
```

## <a name="update-a-custom-role"></a>Egyéni szerepkörök frissítése

Egyéni szerepkör frissítéséhez először az [az role definition list](/cli/azure/role/definition#az_role_definition_list) használatával olvassa be a szerepkör-definíciót. Másodszor, a kívánt módosításokat kell a szerepkör-definíción változtatni. Végül mentse a frissített [szerepkör-definíciót az az role definition update](/cli/azure/role/definition#az_role_definition_update) használatával.

```azurecli
az role definition update --role-definition {roleDefinition}
```

Az alábbi példa hozzáadja a *Microsoft.Insights/diagnosticSettings/* műveletet, és hozzáad egy felügyeleti csoportot a Virtuálisgép-kezelő `Actions` `AssignableScopes` *egyéni* szerepkörhöz. A felügyeleti csoport hozzáadása jelenleg `AssignableScopes` előzetes verzióban érhető el.

vmoperator.jsbe

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}",
    "/providers/Microsoft.Management/managementGroups/marketing-group"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

## <a name="delete-a-custom-role"></a>Egyéni szerepkörök törlése

Egyéni szerepkör törléséhez használja az [az role definition delete parancsot.](/cli/azure/role/definition#az_role_definition_delete) A törölni kívánt szerepkör megadásához használja a szerepkör nevét vagy azonosítóját. A szerepkör-azonosító meghatározásához használja [az az role definition list et.](/cli/azure/role/definition#az_role_definition_list)

```azurecli
az role definition delete --name {roleNameOrId}
```

Az alábbi példa törli a *Virtuálisgép-kezelő egyéni* szerepkört.

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Következő lépések

- [Oktatóanyag: Egyéni Azure-szerepkör létrehozása az Azure CLI használatával](tutorial-custom-role-cli.md)
- [Egyéni Azure-szerepkörök](custom-roles.md)
- [Azure erőforrás-szolgáltatói műveletek](resource-provider-operations.md)