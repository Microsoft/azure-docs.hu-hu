---
title: 'Oktatóanyag: csoport Azure-erőforrásokhoz való hozzáférésének biztosítása Azure PowerShell használatával – Azure RBAC'
description: Ebből az oktatóanyagból megtudhatja, hogyan biztosíthat csoportos hozzáférést az Azure-erőforrásokhoz a Azure PowerShell és az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/02/2019
ms.author: rolyon
ms.openlocfilehash: e25db6213c99afaa46732831534956b01adba0b4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100555231"
---
# <a name="tutorial-grant-a-group-access-to-azure-resources-using-azure-powershell"></a>Oktatóanyag: csoporthoz való hozzáférés biztosítása az Azure-erőforrásokhoz Azure PowerShell használatával

Az Azure [szerepköralapú hozzáférés-vezérlés (Azure RBAC)](overview.md) az Azure-erőforrásokhoz való hozzáférés kezelésének módja. Ebben az oktatóanyagban hozzáférést biztosít egy csoport számára, hogy mindent megtekinthessen az előfizetésben és mindent kezelhessen egy erőforráscsoportban az Azure PowerShell használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzáférés biztosítása egy csoport számára különböző hatókörökben
> * Hozzáférések felsorolása
> * Hozzáférés eltávolítása

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre van szükség:

- Engedélyek csoportok létrehozására az Azure Active Directoryban (vagy egy meglévő csoport)
- [Azure Cloud Shell](../cloud-shell/quickstart-powershell.md)

## <a name="role-assignments"></a>Szerepkör-hozzárendelések

Az Azure RBAC a hozzáférés biztosításához létre kell hoznia egy szerepkör-hozzárendelést. A szerepkör-hozzárendelés három elemből áll: rendszerbiztonsági tagból, szerepkör-definícióból és hatókörből. Az oktatóanyag során a következő két szerepkör-hozzárendelést fogja elvégezni:

| Rendszerbiztonsági tag | Szerepkör-definíció | Hatókör |
| --- | --- | --- |
| Group<br>(RBAC-oktatóanyagbeli csoport) | [Olvasó](built-in-roles.md#reader) | Előfizetés |
| Group<br>(RBAC-oktatóanyagbeli csoport)| [Közreműködő](built-in-roles.md#contributor) | Erőforráscsoport<br>(rbac-tutorial-resource-group) |

   ![Csoport szerepkör-hozzárendelései](./media/tutorial-role-assignments-group-powershell/rbac-role-assignments.png)

## <a name="create-a-group"></a>Csoport létrehozása

Szerepkör hozzárendeléséhez felhasználóra, csoportra vagy szolgáltatásnévre van szükség. Ha még nem rendelkezik csoporttal, akkor létrehozhat egyet.

- Hozzon létre egy új erőforráscsoportot az Azure Cloud Shellben a [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup) paranccsal.

   ```azurepowershell
   New-AzureADGroup -DisplayName "RBAC Tutorial Group" `
     -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
   ```

   ```Example
   ObjectId                             DisplayName         Description
   --------                             -----------         -----------
   11111111-1111-1111-1111-111111111111 RBAC Tutorial Group
   ```

Ha nem rendelkezik a csoportok létrehozásához szükséges engedélyekkel, kipróbálhatja az [oktatóanyagot: felhasználói hozzáférést biztosíthat az Azure-erőforrásokhoz Azure PowerShell](tutorial-role-assignments-user-powershell.md) helyett.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Egy erőforráscsoport használatával bemutatjuk, hogyan rendelhet hozzá egy szerepkört erőforráscsoporti hatókörben.

1. A [Get-AzLocation](/powershell/module/az.resources/get-azlocation) parancs használatával szerezze be a régió helyeinek listáját.

   ```azurepowershell
   Get-AzLocation | select Location
   ```

1. Válasszon ki egy Önhöz közeli helyet, és rendelje hozzá egy változóhoz.

   ```azurepowershell
   $location = "westus"
   ```

1. Hozzon létre egy új erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancs használatával.

   ```azurepowershell
   New-AzResourceGroup -Name "rbac-tutorial-resource-group" -Location $location
   ```

   ```Example
   ResourceGroupName : rbac-tutorial-resource-group
   Location          : westus
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
   ```

## <a name="grant-access"></a>Hozzáférés biztosítása

Ahhoz, hogy hozzáférést biztosítson a csoporthoz, a [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) parancs használatával rendeljen hozzá egy szerepkört. Meg kell adnia a rendszerbiztonsági tagot, a szerepkör-definíciót és a hatókört.

1. Kérje le a csoport objektumazonosítóját a [Get-AzureADGroup](/powershell/module/azuread/new-azureadgroup) paranccsal.

    ```azurepowershell
    Get-AzureADGroup -SearchString "RBAC Tutorial Group"
    ```

    ```Example
    ObjectId                             DisplayName         Description
    --------                             -----------         -----------
    11111111-1111-1111-1111-111111111111 RBAC Tutorial Group
    ```

1. Mentse a csoport objektumazonosítóját egy változóban.

    ```azurepowershell
    $groupId = "11111111-1111-1111-1111-111111111111"
    ```

1. Szerezze be az előfizetés AZONOSÍTÓját a [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) parancs használatával.

    ```azurepowershell
    Get-AzSubscription
    ```

    ```Example
    Name     : Pay-As-You-Go
    Id       : 00000000-0000-0000-0000-000000000000
    TenantId : 22222222-2222-2222-2222-222222222222
    State    : Enabled
    ```

1. Mentse az előfizetési hatókört egy változóban.

    ```azurepowershell
    $subScope = "/subscriptions/00000000-0000-0000-0000-000000000000"
    ```

1. Rendelje hozzá az [Olvasó](built-in-roles.md#reader) szerepkört a csoporthoz az előfizetési hatókörben.

    ```azurepowershell
    New-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/44444444-4444-4444-4444-444444444444
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

1. Rendelje hozzá a [Közreműködő](built-in-roles.md#contributor) szerepkört a csoporthoz az erőforráscsoporti hatókörben.

    ```azurepowershell
    New-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

## <a name="list-access"></a>Hozzáférések felsorolása

1. Az előfizetés hozzáférésének ellenőrzéséhez használja a [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) parancsot a szerepkör-hozzárendelések listázásához.

    ```azurepowershell
    Get-AzRoleAssignment -ObjectId $groupId -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

    A kimenetben láthatja, hogy az Olvasó szerepkör hozzá lett rendelve az RBAC-oktatóanyagbeli csoport az előfizetési hatókörben.

1. Az erőforráscsoport hozzáférésének ellenőrzéséhez használja a [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) parancsot a szerepkör-hozzárendelések listázásához.

    ```azurepowershell
    Get-AzRoleAssignment -ObjectId $groupId -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

    A kimenetben láthatja, hogy a Közreműködő és az Olvasó szerepkör hozzá lett rendelve az RBAC-oktatóanyagbeli csoporthoz. A Közreműködő szerepkör az rbac-tutorial-resource-group hatókörben van, az Olvasó szerepkör pedig örökölt az előfizetési hatókörben.

## <a name="optional-list-access-using-the-azure-portal"></a>(Választható) Hozzáférések felsorolása az Azure Portal használatával

1. Annak megtekintéséhez, hogyan jelennek meg a szerepkör-hozzárendelések az Azure Portalon, tekintse meg az előfizetés **Hozzáférés-vezérlés (IAM)** paneljét.

    ![Csoport szerepkör-hozzárendelései előfizetési hatókörben](./media/tutorial-role-assignments-group-powershell/role-assignments-subscription.png)

1. Tekintse meg az erőforráscsoport **Hozzáférés-vezérlés (IAM)** paneljét.

    ![Csoport szerepkör-hozzárendelései erőforráscsoporti hatókörben](./media/tutorial-role-assignments-group-powershell/role-assignments-resource-group.png)

## <a name="remove-access"></a>Hozzáférés eltávolítása

A felhasználók, csoportok és alkalmazások hozzáférésének eltávolításához a [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) használatával távolítsa el a szerepkör-hozzárendelést.

1. A következő paranccsal távolítsa el a csoport Közreműködő szerepkör-hozzárendelését az erőforráscsoporti hatókörben.

    ```azurepowershell
    Remove-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

1. A következő paranccsal távolítsa el a csoport Olvasó szerepkör-hozzárendelését az előfizetési hatókörben.

    ```azurepowershell
    Remove-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné a jelen oktatóanyag során létrehozott erőforrásokat, törölje az erőforráscsoportot és a csoportot.

1. Törölje az erőforráscsoportot a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancs használatával.

    ```azurepowershell
    Remove-AzResourceGroup -Name "rbac-tutorial-resource-group"
    ```

    ```Example
    Confirm
    Are you sure you want to remove resource group 'rbac-tutorial-resource-group'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```
    
1. Ha a rendszer kéri, írja be a következőt: **Y**. A törlés eltarthat néhány másodpercig.

1. A csoport a [Remove-AzureADGroup](/powershell/module/azuread/remove-azureadgroup) paranccsal törölhető.

    ```azurepowershell
    Remove-AzureADGroup -ObjectId $groupId
    ```
    
    Ha a csoport törlése során hibaüzenet jelenik meg, a csoportot a portálon is törölheti.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure-szerepkörök kiosztása Azure PowerShell használatával](role-assignments-powershell.md)