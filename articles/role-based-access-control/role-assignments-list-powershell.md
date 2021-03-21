---
title: Azure-beli szerepkör-hozzárendelések listázása a Azure PowerShell-Azure RBAC
description: Megtudhatja, hogyan határozhatja meg, hogy a felhasználók, csoportok, egyszerű szolgáltatások és felügyelt identitások milyen erőforrásokhoz férhetnek hozzá Azure PowerShell és Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/28/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: bf5445f6ca04e56aab466e97967a58c3e4b735a4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100556943"
---
# <a name="list-azure-role-assignments-using-azure-powershell"></a>Azure-beli szerepkör-hozzárendelések listázása Azure PowerShell használatával

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control/definition-list.md)] Ez a cikk a szerepkör-hozzárendelések Azure PowerShell használatával történő listázását ismerteti.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

> [!NOTE]
> Ha a szervezete egy Azure-beli [delegált erőforrás-kezelést](../lighthouse/concepts/azure-delegated-resource-management.md)használó szolgáltatónál kiszervezett felügyeleti funkciókat használ, az adott szolgáltató által meghatalmazott szerepkör-hozzárendelések nem jelennek meg.

## <a name="prerequisites"></a>Előfeltételek

- [PowerShell Azure Cloud Shell](../cloud-shell/overview.md) vagy [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-role-assignments-for-the-current-subscription"></a>A jelenlegi előfizetéshez tartozó szerepkör-hozzárendelések listázása

Az aktuális előfizetésben lévő összes szerepkör-hozzárendelés (beleértve az örökölt szerepkör-hozzárendeléseket a gyökérből és a felügyeleti csoportokból) lista beszerzésének legegyszerűbb módja, ha paraméterek nélkül szeretné használni a [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) .

```azurepowershell
Get-AzRoleAssignment
```

```Example
PS C:\> Get-AzRoleAssignment

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : Alain
SignInName         : alain@example.com
RoleDefinitionName : Storage Blob Data Reader
RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Marketing
SignInName         :
RoleDefinitionName : Contributor
RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
ObjectId           : 22222222-2222-2222-2222-222222222222
ObjectType         : Group
CanDelegate        : False

...
```

## <a name="list-role-assignments-for-a-subscription"></a>Előfizetés szerepkör-hozzárendeléseinek felsorolása

Az előfizetési hatókör összes szerepkör-hozzárendelésének listázásához használja a [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment). Az előfizetés-azonosító lekéréséhez keresse meg a Azure Portal **előfizetések** paneljén, vagy használja a [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

```azurepowershell
Get-AzRoleAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /subscriptions/00000000-0000-0000-0000-000000000000
```

## <a name="list-role-assignments-for-a-user"></a>Felhasználó szerepkör-hozzárendeléseinek felsorolása

Az adott felhasználóhoz rendelt összes szerepkör listázásához használja a [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname>
```

```Example
PS C:\> Get-AzRoleAssignment -SignInName isabella@example.com | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

A [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)használatával listázhatja az adott felhasználóhoz rendelt összes szerepkört, valamint azokat a csoportokat, amelyekhez a felhasználó tartozik.

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname> -ExpandPrincipalGroups
```

```Example
Get-AzRoleAssignment -SignInName isabella@example.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

## <a name="list-role-assignments-for-a-resource-group"></a>Erőforráscsoport szerepkör-hozzárendeléseinek felsorolása

Egy erőforráscsoport-hatókör összes szerepkör-hozzárendelésének listázásához használja a [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzRoleAssignment -ResourceGroupName pharma-sales | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Alain Charon
RoleDefinitionName : Backup Operator
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Alain Charon
RoleDefinitionName : Virtual Machine Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

## <a name="list-role-assignments-for-a-management-group"></a>Felügyeleti csoport szerepkör-hozzárendeléseinek listázása

A felügyeleti csoport hatókörében lévő összes szerepkör-hozzárendelés felsorolásához használja a [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment). A felügyeleti csoport AZONOSÍTÓjának beszerzéséhez a Azure Portal **felügyeleti csoportok** paneljén található, vagy a [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup)is használható.

```azurepowershell
Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="list-role-assignments-for-a-resource"></a>Erőforráshoz tartozó szerepkör-hozzárendelések listázása

Egy adott erőforráshoz tartozó szerepkör-hozzárendelések listázásához használja a [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) és a `-Scope` paramétert. A hatókör az erőforrástól függően eltérő lesz. A hatókör beszerzéséhez paraméterek nélkül futtathatja az `Get-AzRoleAssignment` összes szerepkör-hozzárendelést, majd megkeresheti a listázni kívánt hatókört.

```azurepowershell
Get-AzRoleAssignment -Scope "/subscriptions/<subscription_id>/resourcegroups/<resource_group_name>/providers/<provider_name>/<resource_type>/<resource>
```

Az alábbi példa bemutatja, hogyan listázhatja ki a Storage-fiók szerepkör-hozzárendeléseit. Vegye figyelembe, hogy ez a parancs a magasabb hatókörű (például erőforráscsoportok és előfizetések) szerepkör-hozzárendeléseket is felsorolja, amelyek erre a tárolási fiókra vonatkoznak.

```Example
PS C:\> Get-AzRoleAssignment -Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/storage-test-rg/providers/Microsoft.Storage/storageAccounts/storagetest0122"
```

Ha csak az erőforráson közvetlenül hozzárendelt szerepkör-hozzárendeléseket szeretné listázni, akkor a [Where-Object](/powershell/module/microsoft.powershell.core/where-object) paranccsal szűrheti a listát.

```Example
PS C:\> Get-AzRoleAssignment | Where-Object {$_.Scope -eq "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/storage-test-rg/providers/Microsoft.Storage/storageAccounts/storagetest0122"}
```

## <a name="list-role-assignments-for-classic-service-administrator-and-co-administrators"></a>Szerepkör-hozzárendelések listázása a klasszikus szolgáltatás-rendszergazda és a társ-rendszergazdák számára

A klasszikus előfizetés-rendszergazda és a társ-rendszergazdák szerepkör-hozzárendeléseinek listázásához használja a [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -IncludeClassicAdministrators
```

## <a name="list-role-assignments-for-a-managed-identity"></a>Felügyelt identitás szerepkör-hozzárendeléseinek listázása

1. Szerezze be a rendszer által hozzárendelt vagy felhasználó által hozzárendelt felügyelt identitás objektumazonosító-AZONOSÍTÓját. 

    A felhasználó által hozzárendelt felügyelt identitás objektum-AZONOSÍTÓjának lekéréséhez használhatja a [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).

    ```azurepowershell
    Get-AzADServicePrincipal -DisplayNameBeginsWith "<name> or <vmname>"
    ```

1. A szerepkör-hozzárendelések listázásához használja a [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

    ```azurepowershell
    Get-AzRoleAssignment -ObjectId <objectid>
    ```

## <a name="next-steps"></a>Következő lépések

- [Azure-szerepkörök kiosztása Azure PowerShell használatával](role-assignments-powershell.md)