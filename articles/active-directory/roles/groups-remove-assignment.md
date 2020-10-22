---
title: Szerepkör-hozzárendelések eltávolítása egy csoportból a Azure Active Directoryban | Microsoft Docs
description: Az Identitáskezelés delegálásához egyéni Azure AD-szerepköröket tekinthet meg. Az Azure-szerepkörök kezelése a Azure Portal, a PowerShell vagy a Graph API használatával.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c7f441930d9d99f35c2e53bb040b0db0a427659
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92375969"
---
# <a name="remove-role-assignments-from-a-group-in-azure-active-directory"></a>Szerepkör-hozzárendelések eltávolítása egy csoportból Azure Active Directory

Ez a cikk azt ismerteti, hogy a rendszergazda hogyan távolíthatja el a csoportokhoz rendelt Azure AD-szerepköröket. A Azure Portal mostantól közvetlen és közvetett szerepkör-hozzárendeléseket is eltávolíthat egy felhasználóhoz. Ha a felhasználó egy csoporttagság szerepkört rendel hozzá, akkor a szerepkör-hozzárendelés eltávolításához távolítsa el a felhasználót a csoportból.

## <a name="using-azure-admin-center"></a>Az Azure felügyeleti központ használata

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) a Kiemelt szerepkörű rendszergazda vagy a globális rendszergazdai engedélyekkel az Azure ad-szervezetben.

1. Válassza **a szerepkörök és rendszergazdák** > **_szerepkör neve_*_ lehetőséget.

1. Válassza ki azt a csoportot, amelyből el szeretné távolítani a szerepkör-hozzárendelést, és válassza a _ * hozzárendelés eltávolítása * * elemet.

   ![Szerepkör-hozzárendelés eltávolítása egy kiválasztott csoportból.](./media/groups-remove-assignment/remove-assignment.png)

1. Ha a rendszer kéri, hogy erősítse meg a műveletet, válassza az **Igen**lehetőséget.

## <a name="using-powershell"></a>A PowerShell használata

### <a name="create-a-group-that-can-be-assigned-to-role"></a>Szerepkörhöz hozzárendelhető csoport létrehozása

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true
```

### <a name="get-the-role-definition-you-want-to-assign-the-group-to"></a>Annak a szerepkör-definíciónak a beolvasása, amelyhez hozzá szeretné rendelni a csoportot

```powershell
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Helpdesk Administrator'"
```

### <a name="create-a-role-assignment"></a>Szerepkör-hozzárendelés létrehozása

```powershell
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.objectId
```

### <a name="remove-the-role-assignment"></a>Szerepkör-hozzárendelés eltávolítása

```powershell
Remove-AzureAdMSRoleAssignment -Id $roleAssignment.Id 
```

## <a name="using-microsoft-graph-api"></a>Microsoft Graph API használata

### <a name="create-a-group-that-can-be-assigned-an-azure-ad-role"></a>Azure AD-szerepkörhöz hozzárendelhető csoport létrehozása

```powershell
POST https://graph.microsoft.com/beta/groups

{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD",
"displayName": "Contoso_Helpdesk_Administrators",
"groupTypes": [
"Unified"
],
"mailEnabled": true,
"securityEnabled": true
"mailNickname": "contosohelpdeskadministrators",
"isAssignableToRole": true,
}
```

### <a name="get-the-role-definition"></a>Szerepkör-definíció beolvasása

```powershell
GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter = displayName eq ‘Helpdesk Administrator’
```

### <a name="create-the-role-assignment"></a>Szerepkör-hozzárendelés létrehozása

```powershell
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
{
"principalId":"<Object Id of Group>",
"roleDefinitionId":"<Id of role definition>",
"directoryScopeId":"/"
}
```

### <a name="delete-role-assignment"></a>Szerepkör-hozzárendelés törlése

```powershell
DELETE https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/<Id of role assignment>
```

## <a name="next-steps"></a>Következő lépések

- [Felhőalapú csoportok használata a szerepkör-hozzárendelések kezeléséhez](groups-concept.md)
- [A felhőbeli csoportokhoz hozzárendelt szerepkörökkel kapcsolatos hibák elhárítása](groups-faq-troubleshooting.md)
