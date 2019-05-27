---
title: Hozzon létre egyéni szerepkörök az Azure-erőforrások Azure PowerShell-lel |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egyéni szerepköröket a szerepköralapú hozzáférés-vezérlés (RBAC) az Azure-erőforrások Azure PowerShell-lel. Ez magában foglalja a listában, létrehozása, frissítése és egyéni szerepkörök törlése.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: ad1185cab2b2bd2d0fea10f21b7859fd9ab1339f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "66158448"
---
# <a name="create-custom-roles-for-azure-resources-using-azure-powershell"></a>Hozzon létre egyéni szerepkörök az Azure-erőforrások Azure PowerShell-lel

Ha a [beépített szerepkörök az Azure-erőforrások](built-in-roles.md) nem felelnek meg a szervezet konkrét igényeinek, saját egyéni szerepköröket is létrehozhat. Ez a cikk bemutatja, hogyan hozhat létre és kezelhet, egyéni szerepkörök az Azure PowerShell használatával.

Egyéni szerepkör létrehozásával egy részletes útmutató: [oktatóanyag: Hozzon létre egy egyéni szerepkört az Azure-erőforrások Azure PowerShell-lel](tutorial-custom-role-powershell.md).

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Egyéni szerepkörök létrehozása, az alábbiak szükségesek:

- Egyéni szerepkörök létrehozására vonatkozó engedélyre, amely lehet például [Tulajdonos](built-in-roles.md#owner) vagy [Felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator)
- [Az Azure Cloud Shell](../cloud-shell/overview.md) vagy [Azure PowerShell-lel](/powershell/azure/install-az-ps)

## <a name="list-custom-roles"></a>Egyéni szerepkörök listázása

A szerepkörök, amelyek rendelhető hozzá hatókör listájában, használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) parancsot. Az alábbi példa felsorolja a hozzárendelés a kijelölt előfizetésben elérhető összes szerepkört.

```azurepowershell
Get-AzRoleDefinition | FT Name, IsCustom
```

```Example
Name                                              IsCustom
----                                              --------
Virtual Machine Operator                              True
AcrImageSigner                                       False
AcrQuarantineReader                                  False
AcrQuarantineWriter                                  False
API Management Service Contributor                   False
...
```

Az alábbi példa felsorolja a csak az egyéni szerepkörök hozzárendelése a kiválasztott előfizetéshez elérhető.

```azurepowershell
Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
```

```Example
Name                     IsCustom
----                     --------
Virtual Machine Operator     True
```

Ha a kijelölt előfizetésben nem szerepel a `AssignableScopes` a szerepkör az egyéni szerepkör nem jelenik meg.

## <a name="list-a-custom-role-definition"></a>Egy egyéni szerepkör-definíció listázása

Egy egyéni szerepkör-definíció listázásához használja [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition). Ez a lehetőség ugyanazt a parancsot egy beépített szerepkör használja.

```azurepowershell
Get-AzRoleDefinition <role name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Virtual Machine Operator" | ConvertTo-Json

{
  "Name": "Virtual Machine Operator",
  "Id": "00000000-0000-0000-0000-000000000000",
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
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Az alábbi példa felsorolja a szerepkör csak a műveletek:

```azurepowershell
(Get-AzRoleDefinition <role name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Operator").Actions

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
```

## <a name="create-a-custom-role"></a>Egyéni szerepkör létrehozása

Egyéni szerepkör létrehozásához használja a [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) parancsot. Strukturálja a szerepkör a két módszer használatával `PSRoleDefinition` objektum vagy egy JSON-sablon. 

### <a name="get-operations-for-a-resource-provider"></a>Erőforrás-szolgáltató műveleteinek beolvasása

Egyéni szerepkörök létrehozásakor fontos tudni, hogy a lehetséges műveletekről az erőforrás-szolgáltató.
Megtekintheti a listája [erőforrás-szolgáltatói műveletek](resource-provider-operations.md) vagy használhatja a [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) parancs használatával beszerezheti ezeket az információkat.
Például ha azt szeretné ellenőrizni a rendelkezésre álló műveletek a virtuális gépek, használja ezt a parancsot:

```azurepowershell
Get-AzProviderOperation <operation> | FT OperationName, Operation, Description -AutoSize
```

```Example
PS C:\> Get-AzProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation, Description -AutoSize

OperationName                                  Operation                                                      Description
-------------                                  ---------                                                      -----------
Get Virtual Machine                            Microsoft.Compute/virtualMachines/read                         Get the propertie...
Create or Update Virtual Machine               Microsoft.Compute/virtualMachines/write                        Creates a new vir...
Delete Virtual Machine                         Microsoft.Compute/virtualMachines/delete                       Deletes the virtu...
Start Virtual Machine                          Microsoft.Compute/virtualMachines/start/action                 Starts the virtua...
...
```

### <a name="create-a-custom-role-with-the-psroledefinition-object"></a>Egyéni szerepkör létrehozása a PSRoleDefinition objektummal

Ha egy egyéni szerepkör létrehozása a PowerShell használatával, egyikét használhatja a [beépített szerepkörök](built-in-roles.md) kiindulási pontot, vagy megkezdheti a teljesen új módon. Ebben a szakaszban az első példában egy beépített szerepkör kezdődik, és majd testreszabja további engedélyekkel. Szerkessze az attribútumokat kíván hozzáadni a `Actions`, `NotActions`, vagy `AssignableScopes` , és mentse a módosításokat, egy új szerepkör.

Az alábbi példa kezdődik a [virtuális gépek Közreműködője](built-in-roles.md#virtual-machine-contributor) nevű egyéni szerepkör létrehozása beépített szerepkör *virtuális gépet üzemeltető*. Az új szerepkör hozzáférést biztosít az összes olvasási műveletek *Microsoft.Compute*, *Microsoft.Storage*, és *Microsoft.Network* erőforrás-szolgáltatók és biztosít hozzáférést elindításához , indítsa újra, és a virtuális gépek figyelése céljából. Az egyéni szerepkör is használható két előfizetéssel.

```azurepowershell
$role = Get-AzRoleDefinition "Virtual Machine Contributor"
$role.Id = $null
$role.Name = "Virtual Machine Operator"
$role.Description = "Can monitor and restart virtual machines."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/*/read")
$role.Actions.Add("Microsoft.Network/*/read")
$role.Actions.Add("Microsoft.Compute/*/read")
$role.Actions.Add("Microsoft.Compute/virtualMachines/start/action")
$role.Actions.Add("Microsoft.Compute/virtualMachines/restart/action")
$role.Actions.Add("Microsoft.Authorization/*/read")
$role.Actions.Add("Microsoft.ResourceHealth/availabilityStatuses/read")
$role.Actions.Add("Microsoft.Resources/subscriptions/resourceGroups/read")
$role.Actions.Add("Microsoft.Insights/alertRules/*")
$role.Actions.Add("Microsoft.Support/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/00000000-0000-0000-0000-000000000000")
$role.AssignableScopes.Add("/subscriptions/11111111-1111-1111-1111-111111111111")
New-AzRoleDefinition -Role $role
```

Az alábbi példa bemutatja, hozzon létre egy másik módja a *virtuális gépet üzemeltető* egyéni szerepkör. Hozzon létre egy új elindítja `PSRoleDefinition` objektum. A művelet műveletek vannak megadva a `perms` változó és értéke a `Actions` tulajdonság. A `NotActions` tulajdonság értéke, olvassa el a `NotActions` származó a [virtuális gépek Közreműködője](built-in-roles.md#virtual-machine-contributor) beépített szerepkör. Mivel [virtuális gépek Közreműködője](built-in-roles.md#virtual-machine-contributor) sem tartalmaz `NotActions`, ezt a sort nem kötelező, de bemutatja, hogyan információkat is kérhető le egy másik szerepkört.

```azurepowershell
$role = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
$role.Name = 'Virtual Machine Operator 2'
$role.Description = 'Can monitor and restart virtual machines.'
$role.IsCustom = $true
$perms = 'Microsoft.Storage/*/read','Microsoft.Network/*/read','Microsoft.Compute/*/read'
$perms += 'Microsoft.Compute/virtualMachines/start/action','Microsoft.Compute/virtualMachines/restart/action'
$perms += 'Microsoft.Authorization/*/read'
$perms += 'Microsoft.ResourceHealth/availabilityStatuses/read'
$perms += 'Microsoft.Resources/subscriptions/resourceGroups/read'
$perms += 'Microsoft.Insights/alertRules/*','Microsoft.Support/*'
$role.Actions = $perms
$role.NotActions = (Get-AzRoleDefinition -Name 'Virtual Machine Contributor').NotActions
$subs = '/subscriptions/00000000-0000-0000-0000-000000000000','/subscriptions/11111111-1111-1111-1111-111111111111'
$role.AssignableScopes = $subs
New-AzRoleDefinition -Role $role
```

### <a name="create-a-custom-role-with-json-template"></a>Egyéni szerepkör létrehozása JSON-sablonnal

Az adatforrás-definíciót az egyéni szerepkör egy JSON-sablon is használható. A következő példában létrehozunk egy egyéni biztonsági szerepkört, amely olvasási hozzáférést biztosít a tárolási és számítási erőforrásokat, hozzáférés támogatásához, és hozzáadja a szerepkör két előfizetéssel. Hozzon létre egy új fájlt `C:\CustomRoles\customrole1.json` a következő példában. Az azonosítót kell megadni `null` kezdeti szerep létrehozása új azonosító az automatikusan generált. 

```json
{
  "Name": "Custom Role 1",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Adja hozzá a szerepkört az előfizetéseket, futtassa a következő PowerShell-parancsot:

```azurepowershell
New-AzRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="update-a-custom-role"></a>Egyéni szerepkör frissítése

Egyéni szerepkörök létrehozása hasonló, módosíthatja egy meglévő egyéni szerepkör segítségével a `PSRoleDefinition` objektum vagy egy JSON-sablon.

### <a name="update-a-custom-role-with-the-psroledefinition-object"></a>Egyéni szerepkör frissítése a PSRoleDefinition objektummal

Egyéni szerepkör módosításához először használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) parancs használatával kérje le a szerepkör-definíció. A második végezze el a kívánt módosításokat a szerepkör-definíció. Végül a [Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) paranccsal mentse a módosított szerepkör-definíció.

Az alábbi példa hozzáadja a `Microsoft.Insights/diagnosticSettings/*` művelet a *virtuális gépet üzemeltető* egyéni szerepkör.

```azurepowershell
$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}
```

Az alábbi példa egy Azure-előfizetés felvétele a hozzárendelhető hatókörökkel, az *virtuális gépet üzemeltető* egyéni szerepkör.

```azurepowershell
Get-AzSubscription -SubscriptionName Production3

$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzSubscription -SubscriptionName Production3

Name     : Production3
Id       : 22222222-2222-2222-2222-222222222222
TenantId : 99999999-9999-9999-9999-999999999999
State    : Enabled

PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111,
                   /subscriptions/22222222-2222-2222-2222-222222222222}
```

### <a name="update-a-custom-role-with-a-json-template"></a>Egyéni szerepkör frissítése a JSON-sablonnal

Az előző JSON-sablon használatával egyszerűen módosíthatja egy meglévő egyéni szerepkör hozzáadása vagy eltávolítása a műveleteket. A JSON-sablon frissítéséhez, és adja hozzá a olvasási műveletet, a hálózatkezeléshez, a következő példában látható módon. A sablon szerepel a definíciók összesítve nem vonatkoznak a egy meglévő definíciót, ami azt jelenti, hogy a szerepkör megjelenik-e pontosan úgy, ahogy a sablonban megadott. Emellett frissítenie az azonosító mező azonosítójú, a szerepkör. Ha nem tudja, mi az az érték, használhatja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) parancsmagot, hogy ezt az információt.

```json
{
  "Name": "Custom Role 1",
  "Id": "acce7ded-2559-449d-bcd5-e9604e50bad1",
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

A meglévő szerepkör-frissítéséhez futtassa a következő PowerShell-parancsot:

```azurepowershell
Set-AzRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>Egyéni szerepkörök törlése

Egyéni szerepkör törléséhez használja a [Remove-AzRoleDefinition](/powershell/module/az.resources/remove-azroledefinition) parancsot.

A következő példában eltávolítjuk a *virtuális gépet üzemeltető* egyéni szerepkör.

```azurepowershell
Get-AzRoleDefinition "Virtual Machine Operator"
Get-AzRoleDefinition "Virtual Machine Operator" | Remove-AzRoleDefinition
```

```Example
PS C:\> Get-AzRoleDefinition "Virtual Machine Operator"

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}

PS C:\> Get-AzRoleDefinition "Virtual Machine Operator" | Remove-AzRoleDefinition

Confirm
Are you sure you want to remove role definition with name 'Virtual Machine Operator'.
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
```

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: Hozzon létre egy egyéni szerepkört az Azure-erőforrások Azure PowerShell-lel](tutorial-custom-role-powershell.md)
- [Egyéni szerepkörök Azure-erőforrásokhoz](custom-roles.md)
- [Az Azure Resource Manager erőforrás-szolgáltatói műveletek](resource-provider-operations.md)
