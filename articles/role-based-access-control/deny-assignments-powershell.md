---
title: Azure-beli megtagadási hozzárendelések listázása a Azure PowerShell-Azure RBAC
description: Megtudhatja, hogyan listázhatja azokat a felhasználókat, csoportokat, egyszerű szolgáltatásokat és felügyelt identitásokat, amelyek a Azure PowerShell és az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával megtagadták a hozzáférést bizonyos Azure-erőforrás-műveletekhez.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 6cf379b051ba42be2d7df7d288f07cdc2a0002a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94657635"
---
# <a name="list-azure-deny-assignments-using-azure-powershell"></a>Azure-beli megtagadási hozzárendelések listázása Azure PowerShell használatával

Az [Azure-megtagadási hozzárendelések](deny-assignments.md) letiltják a felhasználók számára bizonyos Azure-erőforrás-műveletek végrehajtását, még akkor is, ha egy szerepkör-hozzárendelés Ez a cikk a megtagadási hozzárendelések Azure PowerShell használatával történő listázását ismerteti.

> [!NOTE]
> Nem hozhat létre közvetlenül saját megtagadási hozzárendeléseket. További információ a megtagadási hozzárendelések létrehozásáról: [Azure megtagadási hozzárendelések](deny-assignments.md).

## <a name="prerequisites"></a>Előfeltételek

A megtagadási hozzárendeléssel kapcsolatos információk lekéréséhez a következőket kell tennie:

- `Microsoft.Authorization/denyAssignments/read`a legtöbb [Azure beépített szerepkör](built-in-roles.md) részét képező engedély
- [PowerShell Azure Cloud Shell](../cloud-shell/overview.md) vagy [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-deny-assignments"></a>Megtagadás-hozzárendelések felsorolása

### <a name="list-all-deny-assignments"></a>Az összes megtagadási hozzárendelés listázása

A jelenlegi előfizetés összes megtagadási hozzárendelésének listázásához használja a [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

```azurepowershell
Get-AzDenyAssignment
```

```Example
PS C:\> Get-AzDenyAssignment

Id                      : 22222222-2222-2222-2222-222222222222
DenyAssignmentName      : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          ObjectType:   ServicePrincipal
                          }
IsSystemProtected       : True

Id                      : 33333333-3333-3333-3333-333333333333
DenyAssignmentName      : Deny assignment '33333333-3333-3333-3333-333333333333' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks/providers/Microsoft.Storage/storageAccounts/storep6vkuxmu4m4pq
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          DisplayName:  assignment-locked-storageaccount-TestingBPLocks
                          ObjectType:   ServicePrincipal
                          ObjectId:     2311a0b7-657a-4ca2-af6f-d1c33f6d2fff
                          }
IsSystemProtected       : True
```

### <a name="list-deny-assignments-at-a-resource-group-scope"></a>Erőforráscsoport-hatókörben lévő megtagadási hozzárendelések listázása

Egy erőforráscsoport-hatókör összes megtagadási hozzárendelésének listázásához használja a [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

```azurepowershell
Get-AzDenyAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzDenyAssignment -ResourceGroupName TestingBPLocks | FL DenyAssignmentName, Scope

DenyAssignmentName : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                     '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
Principals         : {
                     DisplayName:       All Principals
                     ObjectType:        SystemDefined
                     ObjectId:  00000000-0000-0000-0000-000000000000
                     }
```

### <a name="list-deny-assignments-at-a-subscription-scope"></a>Előfizetési hatókörben lévő megtagadási hozzárendelések listázása

Az előfizetési hatókörök összes megtagadási hozzárendelésének listázásához használja a [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment). Az előfizetés-azonosító lekéréséhez keresse meg a Azure Portal **előfizetések** paneljén, vagy használja a [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

```azurepowershell
Get-AzDenyAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzDenyAssignment -Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>Következő lépések

- [Az Azure deny-hozzárendelések ismertetése](deny-assignments.md)
- [Azure-beli megtagadási hozzárendelések listázása a Azure Portal használatával](deny-assignments-portal.md)
- [Azure-beli megtagadási hozzárendelések listázása a REST API használatával](deny-assignments-rest.md)