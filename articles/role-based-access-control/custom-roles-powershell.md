---
title: Egyéni Azure-szerepkörök létrehozása vagy frissítése a Azure PowerShell-Azure RBAC
description: Megtudhatja, hogyan listázhat, hozhat létre, frissíthet vagy törölhet egyéni szerepköröket a Azure PowerShell és az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával.
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
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 799475db567c88f067192d027589e9185ee1782b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97369174"
---
# <a name="create-or-update-azure-custom-roles-using-azure-powershell"></a>Egyéni Azure-szerepkörök létrehozása vagy frissítése Azure PowerShell használatával

> [!IMPORTANT]
> A felügyeleti csoport hozzáadása a `AssignableScopes` jelenleg előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ha az [Azure beépített szerepkörei](built-in-roles.md) nem felelnek meg a szervezet konkrét igényeinek, létrehozhat saját egyéni szerepköröket is. Ez a cikk az egyéni szerepkörök listázását, létrehozását, frissítését és törlését ismerteti Azure PowerShell használatával.

Az egyéni szerepkörök létrehozásával kapcsolatos részletes oktatóanyagért lásd [: oktatóanyag: egyéni Azure-szerepkör létrehozása Azure PowerShell használatával](tutorial-custom-role-powershell.md).

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Egyéni szerepkörök létrehozásához a következőkre lesz szüksége:

- Egyéni szerepkörök létrehozására vonatkozó engedélyre, amely lehet például [Tulajdonos](built-in-roles.md#owner) vagy [Felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) vagy [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-custom-roles"></a>Egyéni szerepkörök listázása

A hatókörben való hozzárendeléshez rendelkezésre álló szerepkörök listázásához használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) parancsot. Az alábbi példa felsorolja az összes olyan szerepkört, amely elérhető a kijelölt előfizetésben való hozzárendeléshez.

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

Az alábbi példa csak azokat az egyéni szerepköröket sorolja fel, amelyek elérhetők a kijelölt előfizetésben való hozzárendeléshez.

```azurepowershell
Get-AzRoleDefinition -Custom | FT Name, IsCustom
```

```Example
Name                     IsCustom
----                     --------
Virtual Machine Operator     True
```

Ha a kiválasztott előfizetés nem szerepel a `AssignableScopes` szerepkörben, az egyéni szerepkör nem jelenik meg.

## <a name="list-a-custom-role-definition"></a>Egyéni szerepkör-definíció listázása

Egyéni szerepkör-definíciók listázásához használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition). Ez ugyanaz a parancs, mint amelyet egy beépített szerepkörhöz használ.

```azurepowershell
Get-AzRoleDefinition <role_name> | ConvertTo-Json
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

A következő példa csak a szerepkör műveleteit sorolja fel:

```azurepowershell
(Get-AzRoleDefinition <role_name>).Actions
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

Egyéni szerepkör létrehozásához használja a [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) parancsot. A szerepkört két módon lehet strukturálni, `PSRoleDefinition` objektum vagy JSON-sablon használatával. 

### <a name="get-operations-for-a-resource-provider"></a>Erőforrás-szolgáltató műveleteinek beolvasása

Egyéni szerepkörök létrehozásakor fontos tudni az erőforrás-szolgáltatók összes lehetséges műveletét.
Megtekintheti az erőforrás- [szolgáltatói műveletek](resource-provider-operations.md) listáját, vagy a [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) paranccsal kérheti le ezeket az adatokat.
Ha például a virtuális gépek összes elérhető műveletét szeretné megtekinteni, használja a következő parancsot:

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

Ha a PowerShell használatával hoz létre egyéni szerepkört, kiindulási pontként használhatja a [beépített szerepkörök](built-in-roles.md) egyikét, vagy akár teljesen is elindíthat. Az ebben a szakaszban szereplő első példa egy beépített szerepkörrel kezdődik, majd a további engedélyekkel testreszabja azt. Szerkessze az attribútumokat, és adja hozzá a `Actions` , `NotActions` vagy a kívánt tulajdonságokat `AssignableScopes` , majd mentse a módosításokat új szerepkörként.

Az alábbi példa a virtuálisgép- [közreműködő](built-in-roles.md#virtual-machine-contributor) beépített szerepkörével kezdődik a *Virtuálisgép-kezelő* nevű egyéni szerepkör létrehozásához. Az új szerepkör hozzáférést biztosít a *Microsoft. számítás*, a *Microsoft. Storage* és a *Microsoft. Network* erőforrás-szolgáltatók összes olvasási műveletéhez, és hozzáférést biztosít a virtuális gépek elindításához, újraindításához és figyeléséhez. Az egyéni szerepkör két előfizetésben is használható.

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

A következő példa egy másik módszert mutat be a *Virtuálisgép-kezelő* egyéni szerepkörének létrehozásához. Egy új objektum létrehozásával kezdődik `PSRoleDefinition` . A műveleti műveletek a `perms` változóban vannak megadva, és a `Actions` tulajdonságra vannak beállítva. A `NotActions` tulajdonságot úgy állítja be, hogy beolvassa a `NotActions` [virtuális gép közreműködői](built-in-roles.md#virtual-machine-contributor) beépített szerepkörét. Mivel a [virtuálisgép-közreműködő](built-in-roles.md#virtual-machine-contributor) nem rendelkezik a `NotActions` szolgáltatással, ez a sor nem kötelező, de azt mutatja, hogyan kérhető le az információ egy másik szerepkörből.

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

A JSON-sablonok az egyéni szerepkör forrás-definíciója használhatók. Az alábbi példa egy egyéni szerepkört hoz létre, amely olvasási hozzáférést biztosít a tárolóhoz és a számítási erőforrásokhoz, hozzáférést biztosít a támogatáshoz, és hozzáadja ezt a szerepkört két előfizetéshez. Hozzon létre egy új fájlt `C:\CustomRoles\customrole1.json` a következő példával. Az azonosítót úgy kell beállítani, hogy `null` a kezdeti szerepkör létrehozásakor automatikusan új azonosítót hozzon létre. 

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

Ha hozzá szeretné adni a szerepkört az előfizetésekhez, futtassa a következő PowerShell-parancsot:

```azurepowershell
New-AzRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="update-a-custom-role"></a>Egyéni szerepkörök frissítése

Az egyéni szerepkör létrehozásához hasonlóan egy meglévő egyéni szerepkör is módosítható az `PSRoleDefinition` objektum vagy egy JSON-sablon használatával.

### <a name="update-a-custom-role-with-the-psroledefinition-object"></a>Egyéni szerepkör frissítése a PSRoleDefinition objektummal

Egyéni szerepkör módosításához először használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) parancsot a szerepkör-definíció lekéréséhez. Másodszor, végezze el a kívánt módosításokat a szerepkör-definícióban. Végül a [set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) parancs használatával mentse a módosított szerepkör-definíciót.

A következő példa hozzáadja a `Microsoft.Insights/diagnosticSettings/*` műveletet a *virtuális gép kezelője* egyéni szerepkörhöz.

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

Az alábbi példa egy Azure-előfizetést ad hozzá a virtuálisgép- *kezelő* egyéni szerepkör hozzárendelhető hatóköréhez.

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

Az alábbi példa egy felügyeleti csoportot telepít `AssignableScopes` a virtuálisgép- *kezelő* egyéni szerepkörbe. A felügyeleti csoport hozzáadása a `AssignableScopes` jelenleg előzetes verzióban érhető el.

```azurepowershell
Get-AzManagementGroup

$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/providers/Microsoft.Management/managementGroups/{groupId1}")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzManagementGroup

Id          : /providers/Microsoft.Management/managementGroups/marketing-group
Type        : /providers/Microsoft.Management/managementGroups
Name        : marketing-group
TenantId    : 99999999-9999-9999-9999-999999999999
DisplayName : Marketing group

PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/providers/Microsoft.Management/managementGroups/marketing-group")
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
                   /subscriptions/22222222-2222-2222-2222-222222222222,
                   /providers/Microsoft.Management/managementGroups/marketing-group}
```

### <a name="update-a-custom-role-with-a-json-template"></a>Egyéni szerepkör frissítése JSON-sablonnal

Az előző JSON-sablon használatával egyszerűen módosíthat egy meglévő egyéni szerepkört a műveletek hozzáadásához vagy eltávolításához. Frissítse a JSON-sablont, és adja hozzá az olvasási műveletet a hálózatkezeléshez, ahogy az az alábbi példában is látható. A sablonban felsorolt definíciók nem alkalmazhatók összesítve egy meglévő definícióra, ami azt jelenti, hogy a szerepkör pontosan a sablonban megadott módon jelenik meg. Az id mezőt is frissítenie kell a szerepkör azonosítójával. Ha nem tudja, mi ez az érték, a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) parancsmaggal kérheti le ezeket az adatokat.

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

A meglévő szerepkör frissítéséhez futtassa a következő PowerShell-parancsot:

```azurepowershell
Set-AzRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>Egyéni szerepkörök törlése

Egyéni szerepkör törléséhez használja a [Remove-AzRoleDefinition](/powershell/module/az.resources/remove-azroledefinition) parancsot.

Az alábbi példa eltávolítja a *virtuális gép operátorának* egyéni szerepkörét.

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

## <a name="next-steps"></a>Következő lépések

- [Oktatóanyag: egyéni Azure-szerepkör létrehozása Azure PowerShell használatával](tutorial-custom-role-powershell.md)
- [Egyéni Azure-szerepkörök](custom-roles.md)
- [Azure erőforrás-szolgáltatói műveletek](resource-provider-operations.md)
