---
title: Azure-beli szerepkör-hozzárendelések listázása az Azure CLI-vel – Azure RBAC
description: Megtudhatja, hogyan határozhatja meg, hogy a felhasználók, csoportok, egyszerű szolgáltatások és felügyelt identitások milyen erőforrásokhoz férnek hozzá az Azure CLI és az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával.
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
ms.date: 10/30/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: cc64e314a8acb035736df0521987cb78a7297326
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100556916"
---
# <a name="list-azure-role-assignments-using-azure-cli"></a>Azure-beli szerepkör-hozzárendelések listázása az Azure CLI használatával

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control/definition-list.md)] Ez a cikk bemutatja, hogyan listázhatja ki a szerepkör-hozzárendeléseket az Azure CLI használatával.

> [!NOTE]
> Ha a szervezete egy Azure-beli [delegált erőforrás-kezelést](../lighthouse/concepts/azure-delegated-resource-management.md)használó szolgáltatónál kiszervezett felügyeleti funkciókat használ, az adott szolgáltató által meghatalmazott szerepkör-hozzárendelések nem jelennek meg.

## <a name="prerequisites"></a>Előfeltételek

- [Bash Azure Cloud Shell](../cloud-shell/overview.md) vagy [Azure CLI](/cli/azure) -ben

## <a name="list-role-assignments-for-a-user"></a>Felhasználó szerepkör-hozzárendeléseinek felsorolása

Egy adott felhasználó szerepkör-hozzárendeléseinek listázásához használja az [az role hozzárendelés List](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --assignee {assignee}
```

Alapértelmezés szerint csak a jelenlegi előfizetéshez tartozó szerepkör-hozzárendelések jelennek meg. A jelenlegi előfizetéshez és az alatta lévő szerepkör-hozzárendelések megtekintéséhez adja hozzá a (z `--all` ) paramétert. Az örökölt szerepkör-hozzárendelések megtekintéséhez adja hozzá a `--include-inherited` paramétert.

Az alábbi példa felsorolja azokat a szerepkör-hozzárendeléseket, amelyek közvetlenül a *patlong \@ contoso.com* -felhasználóhoz vannak rendelve:

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Backup Operator",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Virtual Machine Contributor",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  }
]
```

## <a name="list-role-assignments-for-a-resource-group"></a>Erőforráscsoport szerepkör-hozzárendeléseinek felsorolása

Az erőforráscsoport-hatókörben létező szerepkör-hozzárendelések listázásához használja az [az role-hozzárendelési lista](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --resource-group {resourceGroup}
```

A következő példa a *Pharma-Sales* erőforráscsoport szerepkör-hozzárendeléseit sorolja fel:

```azurecli
az role assignment list --resource-group pharma-sales --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Backup Operator",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Virtual Machine Contributor",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  
  ...

]
```

## <a name="list-role-assignments-for-a-subscription"></a>Előfizetés szerepkör-hozzárendeléseinek felsorolása

Az előfizetési hatókör összes szerepkör-hozzárendelésének listázásához használja [az az role-hozzárendelési lista](/cli/azure/role/assignment#az-role-assignment-list)lehetőséget. Az előfizetés-azonosító lekéréséhez a Azure Portal az **előfizetések** panelén található, vagy használhatja az [az Account List](/cli/azure/account#az-account-list)lehetőséget.

```azurecli
az role assignment list --subscription {subscriptionNameOrId}
```

Példa:

```azurecli
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "admin@contoso.com",
    "roleDefinitionName": "Owner",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },
  {
    "principalName": "Subscription Admins",
    "roleDefinitionName": "Owner",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },
  {
    "principalName": "alain@contoso.com",
    "roleDefinitionName": "Reader",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },

  ...

]
```

## <a name="list-role-assignments-for-a-management-group"></a>Felügyeleti csoport szerepkör-hozzárendeléseinek listázása

A felügyeleti csoport hatókörében lévő összes szerepkör-hozzárendelés felsorolásához használja [az az role-hozzárendelési lista](/cli/azure/role/assignment#az-role-assignment-list)lehetőséget. A felügyeleti csoport AZONOSÍTÓjának lekéréséhez a Azure Portal **felügyeleti csoportok** paneljén található, vagy használhatja az [az Account Management-Group listát](/cli/azure/account/management-group#az-account-management-group-list).

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/{groupId}
```

Példa:

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/sales-group --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "admin@contoso.com",
    "roleDefinitionName": "Owner",
    "scope": "/providers/Microsoft.Management/managementGroups/sales-group"
  },
  {
    "principalName": "alain@contoso.com",
    "roleDefinitionName": "Reader",
    "scope": "/providers/Microsoft.Management/managementGroups/sales-group"
  }
]
```

## <a name="list-role-assignments-for-a-managed-identity"></a>Felügyelt identitás szerepkör-hozzárendeléseinek listázása

1. A rendszer által hozzárendelt vagy felhasználó által hozzárendelt felügyelt identitás rendszerbiztonsági AZONOSÍTÓjának beolvasása.

    A felhasználó által hozzárendelt felügyelt identitás résztvevő-AZONOSÍTÓjának lekéréséhez az [az ad SP List](/cli/azure/ad/sp#az-ad-sp-list) vagy [az Identity List](/cli/azure/identity#az-identity-list)lehetőséget használhatja.

    ```azurecli
    az ad sp list --display-name "{name}" --query [].objectId --output tsv
    ```

    A rendszer által hozzárendelt felügyelt identitás rendszerbiztonsági AZONOSÍTÓjának lekéréséhez használhatja az [az ad SP listát](/cli/azure/ad/sp#az-ad-sp-list).

    ```azurecli
    az ad sp list --display-name "{vmname}" --query [].objectId --output tsv
    ```

1. A szerepkör-hozzárendelések listázásához használja az [az role-hozzárendelési lista](/cli/azure/role/assignment#az-role-assignment-list)lehetőséget.

    Alapértelmezés szerint csak a jelenlegi előfizetéshez tartozó szerepkör-hozzárendelések jelennek meg. A jelenlegi előfizetéshez és az alatta lévő szerepkör-hozzárendelések megtekintéséhez adja hozzá a (z `--all` ) paramétert. Az örökölt szerepkör-hozzárendelések megtekintéséhez adja hozzá a `--include-inherited` paramétert.

    ```azurecli
    az role assignment list --assignee {objectId}
    ```

## <a name="next-steps"></a>Következő lépések

- [Azure-szerepkörök kiosztása az Azure CLI-vel](role-assignments-cli.md)