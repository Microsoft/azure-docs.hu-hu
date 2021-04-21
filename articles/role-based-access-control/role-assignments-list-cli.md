---
title: Azure-beli szerepkör-hozzárendelések felsorolása az Azure CLI használatával – Azure RBAC
description: Megtudhatja, hogyan állapíthatja meg, hogy a felhasználók, csoportok, szolgáltatásnévk vagy felügyelt identitások mely erőforrásokhoz férhetnek hozzá az Azure CLI és az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával.
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
ms.openlocfilehash: 2d30571b68ba7e38e9960d1e434cf7844f6be852
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780100"
---
# <a name="list-azure-role-assignments-using-azure-cli"></a>Azure-beli szerepkör-hozzárendelések felsorolása az Azure CLI használatával

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control/definition-list.md)] Ez a cikk a szerepkör-hozzárendelések felsorolását ismerteti az Azure CLI használatával.

> [!NOTE]
> Ha a szervezet kiszervezett felügyeleti funkciókkal [](../lighthouse/concepts/azure-delegated-resource-management.md)rendelkezik az Azure-beli delegált erőforrás-kezeléseket használó szolgáltatónál, az adott szolgáltató által engedélyezett szerepkör-hozzárendelések itt nem jelennek meg.

## <a name="prerequisites"></a>Előfeltételek

- [Bash az Azure Cloud Shell](../cloud-shell/overview.md) [vagy az Azure CLI-ban](/cli/azure)

## <a name="list-role-assignments-for-a-user"></a>Felhasználó szerepkör-hozzárendeléseinek felsorolása

Egy adott felhasználó szerepkör-hozzárendelésének listához használja [az az role assignment list et:](/cli/azure/role/assignment#az_role_assignment_list)

```azurecli
az role assignment list --assignee {assignee}
```

Alapértelmezés szerint csak az aktuális előfizetés szerepkör-hozzárendelései jelennek meg. Az aktuális előfizetés és az alatta lévő szerepkör-hozzárendelések megtekintéséhez adja hozzá a `--all` paramétert. Az örökölt szerepkör-hozzárendelések megtekintéséhez adja hozzá a `--include-inherited` paramétert.

Az alábbi példa felsorolja a közvetlenül a patlong-felhasználóhoz rendelt *\@ contoso.com* hozzárendeléseket:

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

Az erőforráscsoport hatókörében meglévő szerepkör-hozzárendelések listához használja [az az role assignment list et:](/cli/azure/role/assignment#az_role_assignment_list)

```azurecli
az role assignment list --resource-group {resourceGroup}
```

Az alábbi példa a gyógyszer-értékesítési erőforráscsoport *szerepkör-hozzárendelését* sorolja fel:

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

Az előfizetési hatókörben található összes szerepkör-hozzárendelés listához használja [az az role assignment list (szerepkör-hozzárendelési lista) lehetőséget.](/cli/azure/role/assignment#az_role_assignment_list) Az előfizetés azonosítóját a fiók Előfizetések panelén Azure Portal az account list használatával [kaphatja meg.](/cli/azure/account#az_account_list) 

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

## <a name="list-role-assignments-for-a-management-group"></a>Felügyeleti csoport szerepkör-hozzárendelésének felsorolása

A felügyeleti csoport hatókörében található összes szerepkör-hozzárendelés listához használja [az az role assignment list et.](/cli/azure/role/assignment#az_role_assignment_list) A felügyeleti csoport azonosítóját a felügyeleti  csoport panelének Felügyeleti csoportok panelén találja Azure Portal [az az account management-group list használhatja.](/cli/azure/account/management-group#az_account_management_group_list)

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

## <a name="list-role-assignments-for-a-managed-identity"></a>Felügyelt identitás szerepkör-hozzárendelésének felsorolása

1. Szerezze be a rendszer által hozzárendelt vagy felhasználó által hozzárendelt felügyelt identitás egyszerű azonosítóját.

    A felhasználó által hozzárendelt felügyelt identitás egyszerű azonosítójának leához használhatja az [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list) vagy az az identity list [használhatja a következőt:](/cli/azure/identity#az_identity_list).

    ```azurecli
    az ad sp list --display-name "{name}" --query [].objectId --output tsv
    ```

    A rendszer által hozzárendelt felügyelt identitás főazonosítójának lekért azonosítóját az az ad sp list használatával [kaphatja meg.](/cli/azure/ad/sp#az_ad_sp_list)

    ```azurecli
    az ad sp list --display-name "{vmname}" --query [].objectId --output tsv
    ```

1. A szerepkör-hozzárendelések listához használja [az az role assignment list et.](/cli/azure/role/assignment#az_role_assignment_list)

    Alapértelmezés szerint csak az aktuális előfizetés szerepkör-hozzárendelései jelennek meg. Az aktuális előfizetés és az alatta lévő szerepkör-hozzárendelések megtekintéséhez adja hozzá a `--all` paramétert. Az örökölt szerepkör-hozzárendelések megtekintéséhez adja hozzá a `--include-inherited` paramétert.

    ```azurecli
    az role assignment list --assignee {objectId}
    ```

## <a name="next-steps"></a>Következő lépések

- [Azure-szerepkörök hozzárendelése az Azure CLI használatával](role-assignments-cli.md)