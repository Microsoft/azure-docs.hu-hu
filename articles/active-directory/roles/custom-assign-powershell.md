---
title: Egyéni szerepkörök társítása az Azure AD PowerShell-lel – Azure AD | Microsoft Docs
description: Azure AD-rendszergazdai egyéni szerepkör tagjainak kezelése az Azure AD PowerShell-lel.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f0fb81a4daa57b473e8b2b4b937426eafbf903d
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103014536"
---
# <a name="assign-custom-roles-with-resource-scope-using-powershell-in-azure-active-directory"></a>Egyéni szerepkörök társítása erőforrás-hatókörrel a PowerShell használatával Azure Active Directory

Ez a cikk azt ismerteti, hogyan lehet szerepkör-hozzárendelést létrehozni a szervezeti szintű hatókörben Azure Active Directory (Azure AD). Az Azure AD-szervezeten belül az egész szervezetre kiterjedő hatókörrel rendelkező szerepkör kiosztása biztosít hozzáférést. Ha egy szerepkör-hozzárendelést egyetlen Azure AD-erőforrás hatókörével szeretne létrehozni, tekintse [meg az egyéni szerepkör létrehozása és hozzárendelése az erőforrás-hatókörben](custom-create.md)című témakört. Ez a cikk a [Azure Active Directory PowerShell 2-es verziójának](/powershell/module/azuread/#directory_roles) modulját használja.

További információ az Azure AD rendszergazdai szerepköreiről: [rendszergazdai szerepkörök Kiosztása Azure Active Directoryban](permissions-reference.md).

## <a name="required-permissions"></a>Szükséges engedélyek

A szerepkörök hozzárendeléséhez vagy eltávolításához globális rendszergazdai fiókkal csatlakozhat az Azure AD-szervezethez.

## <a name="prepare-powershell"></a>A PowerShell előkészítése

Telepítse az Azure AD PowerShell-modult a [PowerShell-Galéria](https://www.powershellgallery.com/packages/AzureADPreview). Ezután importálja az Azure AD PowerShell előzetes verzióját a következő parancs használatával:

``` PowerShell
Import-Module -Name AzureADPreview
```

Annak ellenőrzéséhez, hogy a modul készen áll-e a használatra, a következő parancs által visszaadott verziónak kell megfelelnie az itt felsoroltak közül:

``` PowerShell
Get-Module -Name AzureADPreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    AzureADPreview               {Add-AzureADMSAdministrati...}
```

Most már elkezdheti használni a parancsmagokat a modulban. Az Azure AD-modulban található parancsmagok teljes leírását az [Azure ad Preview-modul](https://www.powershellgallery.com/packages/AzureADPreview)online dokumentációjában találja.

## <a name="assign-a-directory-role-to-a-user-or-service-principal-with-resource-scope"></a>Címtárbeli szerepkör kiosztása egy felhasználóhoz vagy egyszerű szolgáltatáshoz erőforrás-hatókörrel

1. Töltse be az Azure AD PowerShell (előzetes verzió) modult.
1. Jelentkezzen be a parancs végrehajtásával `Connect-AzureAD` .
1. Hozzon létre egy új szerepkört a következő PowerShell-parancsfájl használatával.

``` PowerShell
## Assign a role to a user or service principal with resource scope
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

Ha a szerepkört felhasználó helyett egy egyszerű szolgáltatáshoz szeretné rendelni, használja a [Get-AzureADMSServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) parancsmagot.

## <a name="role-definitions"></a>Szerepkör-definíciók

A szerepkör-definíciós objektumok tartalmazzák a beépített vagy az egyéni szerepkör definícióját, valamint az adott szerepkör-hozzárendelés által biztosított engedélyeket. Ez az erőforrás az egyéni szerepkör-definíciókat és a beépített címtárbeli szerepköröket jeleníti meg (amelyek a definíciós egyenértékű formában jelennek meg). Az Azure AD-szervezet jelenleg legfeljebb 30 egyedi egyéni szerepkör-definíciót tartalmazhat.

### <a name="create-a-role-definition"></a>Szerepkör-definíció létrehozása

``` PowerShell
# Basic information
$description = "Can manage credentials of application registrations"
$displayName = "Application Registration Credential Administrator"
$templateId = (New-Guid).Guid

# Set of actions to include
$rolePermissions = @{
    "allowedResourceActions" = @(
        "microsoft.directory/applications/standard/read",
        "microsoft.directory/applications/credentials/update"
    )
}

# Create new custom directory role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="read-and-list-role-definitions"></a>Szerepkör-definíciók olvasása és listázása

``` PowerShell
# Get all role definitions
Get-AzureADMSRoleDefinitions

# Get single role definition by ID
Get-AzureADMSRoleDefinition -Id 86593cfc-114b-4a15-9954-97c3494ef49b

# Get single role definition by templateId
Get-AzureADMSRoleDefinition -Filter "templateId eq 'c4e39bd9-1100-46d3-8c65-fb160da0071f'"
```

### <a name="update-a-role-definition"></a>Szerepkör-definíció frissítése

``` PowerShell
# Update role definition
# This works for any writable property on role definition. You can replace display name with other
# valid properties.
Set-AzureADMSRoleDefinition -Id c4e39bd9-1100-46d3-8c65-fb160da0071f -DisplayName "Updated DisplayName"
```

### <a name="delete-a-role-definition"></a>Szerepkör-definíció törlése

``` PowerShell
# Delete role definition
Remove-AzureADMSRoleDefinitions -Id c4e39bd9-1100-46d3-8c65-fb160da0071f
```

## <a name="role-assignments"></a>Szerepkör-hozzárendelések

A szerepkör-hozzárendelések olyan információkat tartalmaznak, amelyek egy adott rendszerbiztonsági tag (felhasználó vagy alkalmazás-szolgáltatásnév) egy szerepkör-definícióhoz való csatolásával kapcsolatosak. Szükség esetén egyetlen Azure AD-erőforrás hatókörét is hozzáadhatja a hozzárendelt engedélyekhez.  A szerepkör-hozzárendelés hatókörének korlátozása beépített és egyéni szerepkörök esetén támogatott.

### <a name="create-a-role-assignment"></a>Szerepkör-hozzárendelés létrehozása

``` PowerShell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

### <a name="read-and-list-role-assignments"></a>Szerepkör-hozzárendelések olvasása és listázása

``` PowerShell
# Get role assignments for a given principal
Get-AzureADMSRoleAssignment -Filter "principalId eq '27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac'"

# Get role assignments for a given role definition 
Get-AzureADMSRoleAssignment -Filter "roleDefinitionId eq '355aed8a-864b-4e2b-b225-ea95482e7570'"
```

### <a name="delete-a-role-assignment"></a>Szerepkör-hozzárendelés törlése

``` PowerShell
# Delete role assignment
Remove-AzureADMSRoleAssignment -Id 'qiho4WOb9UKKgng_LbPV7tvKaKRCD61PkJeKMh7Y458-1'
```

## <a name="next-steps"></a>Következő lépések

- Ossza meg velünk az [Azure ad rendszergazdai szerepkörökkel foglalkozó fórumát](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)
- A szerepkörökről és az Azure AD rendszergazdai szerepkör-hozzárendeléseiről további információt a [rendszergazdai szerepkörök hozzárendelése](permissions-reference.md) című témakörben talál.
- Az alapértelmezett felhasználói engedélyek összehasonlítását lásd: a [vendég és a tag alapértelmezett felhasználói engedélyeinek összehasonlítása](../fundamentals/users-default-permissions.md)
