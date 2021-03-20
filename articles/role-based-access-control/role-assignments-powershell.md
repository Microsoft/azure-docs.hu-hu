---
title: Azure-szerepkörök kiosztása Azure PowerShell használatával – Azure RBAC
description: Megtudhatja, hogyan biztosíthat hozzáférést az Azure-erőforrásokhoz a felhasználók, csoportok, egyszerű szolgáltatások és felügyelt identitások számára a Azure PowerShell és az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával.
services: active-directory
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.openlocfilehash: 00f663b90f34f3b557329692f844bbbc1bf3207d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100556797"
---
# <a name="assign-azure-roles-using-azure-powershell"></a>Azure-szerepkörök kiosztása Azure PowerShell használatával

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Ez a cikk a szerepkörök Azure PowerShell használatával történő hozzárendelését ismerteti.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

A szerepkörök hozzárendeléséhez a következőket kell tennie:

- `Microsoft.Authorization/roleAssignments/write` engedélyek, például a [felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) vagy a [tulajdonos](built-in-roles.md#owner)
- [PowerShell Azure Cloud Shell](../cloud-shell/overview.md) vagy [Azure PowerShell](/powershell/azure/install-az-ps)
- A PowerShell-parancs futtatásához használt fióknak rendelkeznie kell Microsoft Graph `Directory.Read.All` engedéllyel.

## <a name="steps-to-assign-an-azure-role"></a>Azure-szerepkörök hozzárendelésének lépései

A szerepkörök hozzárendeléséhez a következő három elemből áll: rendszerbiztonsági tag, szerepkör-definíció és hatókör.

### <a name="step-1-determine-who-needs-access"></a>1. lépés: annak meghatározása, hogy kinek van hozzáférése

Szerepkört hozzárendelhet egy felhasználóhoz, csoporthoz, egyszerű szolgáltatásnévhez vagy felügyelt identitáshoz. A szerepkörök hozzárendeléséhez szükség lehet az objektum egyedi AZONOSÍTÓjának megadására. Az azonosító formátuma: `11111111-1111-1111-1111-111111111111` . Az azonosítót a Azure Portal vagy a Azure PowerShell használatával kérheti le.

**Felhasználó**

Azure AD-felhasználó esetén szerezze be az egyszerű felhasználónevet, például a *patlong \@ contoso.com* vagy a felhasználói objektum azonosítóját. Az objektumazonosító lekéréséhez használhatja a [Get-AzADUser](/powershell/module/az.resources/get-azaduser).

```azurepowershell
Get-AzADUser -StartsWith <userName>
(Get-AzADUser -DisplayName <userName>).id
```

**Csoport**

Azure AD-csoport esetén a csoport objektum-AZONOSÍTÓra van szükség. Az objektumazonosító lekéréséhez használhatja a [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup).

```azurepowershell
Get-AzADGroup -SearchString <groupName>
(Get-AzADGroup -DisplayName <groupName>).id
```

**Szolgáltatásnév**

Az Azure AD egyszerű szolgáltatásnév (az alkalmazás által használt identitás) esetében szüksége lesz a szolgáltatásnév objektum AZONOSÍTÓra. Az objektumazonosító lekéréséhez használhatja a [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal). Egyszerű szolgáltatásnév esetén használja az objektumazonosító azonosítót, **ne** pedig az alkalmazás azonosítóját.

```azurepowershell
Get-AzADServicePrincipal -SearchString <principalName>
(Get-AzADServicePrincipal -DisplayName <principalName>).id
```

**Kezelt identitás**

A rendszer által hozzárendelt vagy felhasználó által hozzárendelt felügyelt identitáshoz szüksége lesz az objektum AZONOSÍTÓJÁRA. Az objektumazonosító lekéréséhez használhatja a [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).

```azurepowershell
Get-AzADServicePrincipal -SearchString <principalName>
(Get-AzADServicePrincipal -DisplayName <principalName>).id
```
    
### <a name="step-2-select-the-appropriate-role"></a>2. lépés: válassza ki a megfelelő szerepkört

Az engedélyek szerepkörökbe vannak csoportosítva. Több [Azure beépített szerepkörből](built-in-roles.md) is választhat, vagy használhatja saját egyéni szerepköreit is. Az ajánlott eljárás az, hogy hozzáférést biztosítson a legkevésbé szükséges jogosultsággal, ezért ne rendeljen hozzá szélesebb körű szerepkört.

A szerepkörök listázásához és az egyedi szerepkör-azonosító lekéréséhez használhatja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition | FT Name, IsCustom, Id
```

A következőképpen listázhatja egy adott szerepkör részleteit.

```azurepowershell
Get-AzRoleDefinition <roleName>
```

További információ: az [Azure szerepkör-definíciók listázása](role-definitions-list.md#azure-powershell).
 
### <a name="step-3-identify-the-needed-scope"></a>3. lépés: a szükséges hatókör azonosítása

Az Azure a hatókör négy szintjét biztosítja: erőforrás, [erőforráscsoport](../azure-resource-manager/management/overview.md#resource-groups), előfizetés és [felügyeleti csoport](../governance/management-groups/overview.md). Az ajánlott eljárás az, hogy hozzáférést biztosítson a legkevésbé szükséges jogosultsággal, ezért ne rendeljen hozzá egy szerepkört szélesebb körben. A hatókörrel kapcsolatos további információkért lásd a [hatókör ismertetése](scope-overview.md)című témakört.

**Erőforrás hatóköre**

Erőforrás-hatókör esetén szüksége lesz az erőforrás erőforrás-AZONOSÍTÓJÁRA. Az erőforrás-azonosítót a Azure Portalban található erőforrás tulajdonságainak megtekintésével tekintheti meg. Az erőforrás-azonosító formátuma a következő:

```
/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/<providerName>/<resourceType>/<resourceSubType>/<resourceName>
```

**Erőforráscsoport hatóköre**

Az erőforráscsoport hatóköréhez szüksége lesz az erőforráscsoport nevére. A név a Azure Portal **erőforráscsoportok** lapján található, vagy a [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup)is használható.

```azurepowershell
Get-AzResourceGroup
```

**Előfizetés hatóköre** 

Az előfizetés hatóköréhez szüksége lesz az előfizetés-AZONOSÍTÓra. Az azonosító a Azure Portal **előfizetések** lapján található, vagy a [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription)is használható.

```azurepowershell
Get-AzSubscription
```

**Felügyeleti csoport hatóköre** 

A felügyeleti csoport hatóköréhez szükség van a felügyeleti csoport nevére. A név a Azure Portal **felügyeleti csoportok** lapján található, vagy a [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup)is használható.

```azurepowershell
Get-AzManagementGroup
```
    
### <a name="step-4-assign-role"></a>4. lépés: szerepkör kiosztása

Szerepkör hozzárendeléséhez használja a [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) parancsot. A hatókörtől függően a parancs általában a következő formátumok valamelyikével rendelkezik.

**Erőforrás hatóköre**

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/<providerName>/<resourceType>/<resourceSubType>/<resourceName>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionId <roleId> `
-ResourceName <resourceName> `
-ResourceType <resourceType> `
-ResourceGroupName <resourceGroupName>
```

**Erőforráscsoport hatóköre**

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-ResourceGroupName <resourceGroupName>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-ResourceGroupName <resourceGroupName>
```

**Előfizetés hatóköre** 

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>
```

**Felügyeleti csoport hatóköre** 

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-Scope /providers/Microsoft.Management/managementGroups/<groupName>
``` 

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /providers/Microsoft.Management/managementGroups/<groupName>
``` 
    
## <a name="assign-role-examples"></a>Szerepkör-hozzárendelési példák

#### <a name="assign-a-role-for-all-blob-containers-in-a-storage-account-resource-scope"></a>Szerepkör kiosztása a Storage-fiók erőforrás-hatókörében lévő összes blob-tárolóhoz

Hozzárendeli a [Storage blob-adatközreműködői](built-in-roles.md#storage-blob-data-contributor) szerepkört egy, a *storage12345* nevű Storage-fiók erőforrás-HATÓKÖRében a *55555555-5555-5555-5555-555555555555* azonosítójú azonosítójú szolgáltatáshoz.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 55555555-5555-5555-5555-555555555555 `
-RoleDefinitionName "Storage Blob Data Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/providers/Microsoft.Authorization/roleAssignments/cccccccc-cccc-cccc-cccc-cccccccccccc
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345
DisplayName        : example-identity
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 55555555-5555-5555-5555-555555555555
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-specific-blob-container-resource-scope"></a>Szerepkör társítása egy adott blob-tároló erőforrás-hatóköréhez

Hozzárendeli a [Storage blob-adatközreműködői](built-in-roles.md#storage-blob-data-contributor) szerepkört egy olyan szolgáltatáshoz, amelynek a *55555555-5555-5555-5555-555555555555* -es azonosítójú objektuma egy *blob-Container-01* nevű blob-tároló erőforrás-hatókörében van.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 55555555-5555-5555-5555-555555555555 `
-RoleDefinitionName "Storage Blob Data Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01/providers/Microsoft.Authorization/roleAssignm
                     ents/dddddddd-dddd-dddd-dddd-dddddddddddd
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01
DisplayName        : example-identity
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 55555555-5555-5555-5555-555555555555
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-group-in-a-specific-virtual-network-resource-scope"></a>Szerepkör társítása egy adott virtuális hálózati erőforrás-hatókörben lévő csoporthoz

A [virtuális gép közreműködői](built-in-roles.md#virtual-machine-contributor) szerepkört a Pharma *-Sales-Project-Network* nevű virtuális hálózat erőforrás-HATÓKÖRében lévő aaaaaaaa-AAAA-AAAA-AAAA-aaaaaaaaaaaa azonosítóval rendeli hozzá a *Pharma Sales rendszergazdák* csoportjához.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceName pharma-sales-project-network `
-ResourceType Microsoft.Network/virtualNetworks `
-ResourceGroupName MyVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-user-at-a-resource-group-scope"></a>Szerepkör társítása egy erőforráscsoport-hatókörben lévő felhasználóhoz

Hozzárendeli a [virtuális gépi közreműködő](built-in-roles.md#virtual-machine-contributor) szerepkört a *patlong \@ contoso.com* -felhasználóhoz a *Pharma-Sales* erőforráscsoport hatókörében.

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

Másik lehetőségként megadhatja a teljesen minősített erőforráscsoportot a (z) `-Scope` paraméterrel:

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-user-using-the-unique-role-id-at-a-resource-group-scope"></a>Szerepkör társítása egy adott felhasználóhoz egy erőforráscsoport-hatókörben lévő egyedi szerepkör-azonosító használatával

Néhány alkalommal, amikor a szerepkör neve változhat, például:

- Saját egyéni szerepkört használ, és úgy dönt, hogy megváltoztatja a nevet.
- Olyan előzetes verziójú szerepkört használ, amely **(előzetes verzió)** szerepel a névben. A szerepkör megjelenése után a rendszer átnevezi a szerepkört.

A szerepkör-azonosító nem változik, még akkor is, ha a szerepkör át lett nevezve. Ha parancsfájlokat vagy automationt használ a szerepkör-hozzárendelések létrehozásához, ajánlott az egyedi szerepkör-azonosítót használni a szerepkör neve helyett. Ezért ha egy szerepkört átneveznek, a parancsfájlok nagyobb valószínűséggel fognak működni.

A következő példa a [virtuális gép közreműködői](built-in-roles.md#virtual-machine-contributor) szerepkört rendeli hozzá a *patlong \@ contoso.com* felhasználóhoz a *Pharma-Sales erőforráscsoport-* hatókörben.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 44444444-4444-4444-4444-444444444444 `
-RoleDefinitionId 9980e02c-c2be-4d73-94e8-173b1dc7cf3c `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

#### <a name="assign-a-role-for-an-application-at-a-resource-group-scope"></a>Szerepkör társítása egy adott alkalmazáshoz egy erőforráscsoport-hatókörben

Hozzárendeli a [virtuálisgép-közreműködő](built-in-roles.md#virtual-machine-contributor) szerepkört egy, az 77777777-7777-7777-7777-777777777777-es azonosítójú alkalmazáshoz a *Pharma-Sales* erőforráscsoport hatókörében.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-user-at-a-subscription-scope"></a>Szerepkör kiosztása egy felhasználóhoz előfizetési hatókörben

Hozzárendeli az [olvasó](built-in-roles.md#reader) szerepkört a *annm \@ example.com* -felhasználóhoz egy előfizetési hatókörben.

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName annm@example.com `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : Ann M
SignInName         : annm@example.com
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-user-at-a-management-group-scope"></a>Szerepkör társítása egy felügyeleti csoport hatókörében lévő felhasználóhoz

A [Számlázási olvasó](built-in-roles.md#billing-reader) szerepkört a felügyeleti csoport hatókörében lévő *Alain \@ example.com* -felhasználóhoz rendeli.

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"

RoleAssignmentId   : /providers/Microsoft.Management/managementGroups/marketing-group/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /providers/Microsoft.Management/managementGroups/marketing-group
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Billing Reader
RoleDefinitionId   : fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

## <a name="next-steps"></a>Következő lépések

- [Azure-beli szerepkör-hozzárendelések listázása Azure PowerShell használatával](role-assignments-list-powershell.md)
- [Oktatóanyag: csoporthoz való hozzáférés biztosítása az Azure-erőforrásokhoz Azure PowerShell használatával](tutorial-role-assignments-group-powershell.md)
- [Erőforrások kezelése a Azure PowerShell](../azure-resource-manager/management/manage-resources-powershell.md)
