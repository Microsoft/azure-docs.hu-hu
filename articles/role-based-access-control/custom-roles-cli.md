---
title: Egyéni Azure-szerepkörök létrehozása vagy frissítése az Azure CLI-vel – Azure RBAC
description: Megtudhatja, hogyan listázhat, hozhat létre, frissíthet vagy törölhet Azure-beli egyéni szerepköröket az Azure CLI és az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával.
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
ms.openlocfilehash: 31dabcf77f0db76047919fa76d00f1c5ed3c96d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97369140"
---
# <a name="create-or-update-azure-custom-roles-using-azure-cli"></a>Egyéni Azure-szerepkörök létrehozása vagy frissítése az Azure CLI-vel

> [!IMPORTANT]
> A felügyeleti csoport hozzáadása a `AssignableScopes` jelenleg előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ha az [Azure beépített szerepkörei](built-in-roles.md) nem felelnek meg a szervezet konkrét igényeinek, létrehozhat saját egyéni szerepköröket is. Ez a cikk azt ismerteti, hogyan lehet egyéni szerepköröket listázni, létrehozni, frissíteni vagy törölni az Azure CLI használatával.

Az egyéni szerepkörök létrehozásával kapcsolatos lépésenkénti útmutató: [oktatóanyag: egyéni Azure-szerepkör létrehozása az Azure CLI használatával](tutorial-custom-role-cli.md).

## <a name="prerequisites"></a>Előfeltételek

Egyéni szerepkörök létrehozásához a következőkre lesz szüksége:

- Egyéni szerepkörök létrehozására vonatkozó engedélyre, amely lehet például [Tulajdonos](built-in-roles.md#owner) vagy [Felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) vagy [Azure CLI](/cli/azure/install-azure-cli)

## <a name="list-custom-roles"></a>Egyéni szerepkörök listázása

A hozzárendeléshez elérhető egyéni szerepkörök listázásához használja [az az role definition List](/cli/azure/role/definition#az-role-definition-list)lehetőséget. Az alábbi példa felsorolja az aktuális előfizetés összes egyéni szerepkörét.

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

## <a name="list-a-custom-role-definition"></a>Egyéni szerepkör-definíció listázása

Egyéni szerepkör-definíciók listázásához használja [az az role definition List](/cli/azure/role/definition#az-role-definition-list)lehetőséget. Ez ugyanaz a parancs, amelyet egy beépített szerepkörhöz használ.

```azurecli
az role definition list --name {roleName}
```

A következő példa a *virtuális gép operátori* szerepkörének definícióját sorolja fel:

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

Az alábbi példa a *virtuális gép operátori* szerepkörének műveleteit sorolja fel:

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

Egyéni szerepkör létrehozásához használja [az az role definition Create](/cli/azure/role/definition#az-role-definition-create)lehetőséget. A szerepkör-definíció lehet JSON-leírás vagy egy JSON-leírást tartalmazó fájl elérési útja.

```azurecli
az role definition create --role-definition {roleDefinition}
```

Az alábbi példa egy *Virtuálisgép-kezelő* nevű egyéni szerepkört hoz létre. Ez az egyéni szerepkör hozzáférést rendel a *Microsoft. számítás*, a *Microsoft. Storage* és a *Microsoft. Network* erőforrás-szolgáltatók összes olvasási műveletéhez, és hozzáférést rendel a virtuális gépek indításához, újraindításához és figyeléséhez. Ez az egyéni szerepkör két előfizetésben is használható. Ez a példa egy JSON-fájlt használ bemenetként.

vmoperator.jsbekapcsolva

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

Egyéni szerepkör frissítéséhez először az [az role definition List](/cli/azure/role/definition#az-role-definition-list) paranccsal kérheti le a szerepkör-definíciót. Másodszor, végezze el a kívánt módosításokat a szerepkör-definícióban. Végül az [az role definition Update](/cli/azure/role/definition#az-role-definition-update) paranccsal mentse a frissített szerepkör-definíciót.

```azurecli
az role definition update --role-definition {roleDefinition}
```

A következő példa hozzáadja a *Microsoft. bepillantást/diagnosticSettings/* operationt a `Actions` szolgáltatáshoz, és hozzáadja a felügyeleti csoportot a `AssignableScopes` *virtuális gép kezelője* egyéni szerepkörhöz. A felügyeleti csoport hozzáadása a `AssignableScopes` jelenleg előzetes verzióban érhető el.

vmoperator.jsbekapcsolva

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

Egyéni szerepkör törléséhez használja [az az role definition delete](/cli/azure/role/definition#az-role-definition-delete)paranccsal. A törlendő szerepkör megadásához használja a szerepkör nevét vagy a szerepkör AZONOSÍTÓját. A szerepkör-azonosító meghatározásához használja az [az role definition List](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition delete --name {roleNameOrId}
```

A következő példa törli a *virtuális gép operátorának* egyéni szerepkörét.

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Következő lépések

- [Oktatóanyag: egyéni Azure-szerepkör létrehozása az Azure CLI használatával](tutorial-custom-role-cli.md)
- [Egyéni Azure-szerepkörök](custom-roles.md)
- [Azure erőforrás-szolgáltatói műveletek](resource-provider-operations.md)