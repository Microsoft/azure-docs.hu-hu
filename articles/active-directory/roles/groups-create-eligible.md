---
title: Csoport létrehozása szerepkörök hozzárendeléséhez a Azure Active Directoryban | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre szerepkörhöz hozzárendelhető csoportot az Azure AD-ben. Az Azure-szerepkörök kezelése a Azure Portal, a PowerShell vagy a Graph API használatával.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9bf0a9ca193be9fd61d0b284338c0f581c9f91e3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103012054"
---
# <a name="create-a-role-assignable-group-in-azure-active-directory"></a>Szerepkörhöz rendelt csoport létrehozása Azure Active Directory

Csak az "isAssignableToRole" tulajdonsággal létrehozott, igaz értékre állított, vagy az Azure ad-portálon az **Azure ad-szerepkörökkel** rendelkező csoportokhoz rendelhető szerepkört lehet hozzárendelni. Ez a Group attribútum teszi azt a csoportot, amely egy Azure Active Directory (Azure AD) szerepkörhöz rendelhető hozzá. Ez a cikk bemutatja, hogyan hozhatja létre ezt a speciális típusú csoportot. **Megjegyzés:** Az isAssignableToRole tulajdonságot True értékre beállított csoport nem lehet dinamikus tagsági típus. További információ: [csoport használata az Azure ad szerepkör-hozzárendelések kezeléséhez](groups-concept.md).

## <a name="using-azure-ad-admin-center"></a>Az Azure AD felügyeleti központ használata

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) a Kiemelt szerepkörű rendszergazda vagy a globális rendszergazdai engedélyekkel az Azure ad-szervezetben.
1. Válassza   >  **a csoportok minden** csoport  >  **új csoport** lehetőséget.

    [![Nyisson meg Azure Active Directory, és hozzon létre egy új csoportot.](./media/groups-create-eligible/new-group.png "Nyisson meg Azure Active Directory, és hozzon létre egy új csoportot.")](./media/groups-create-eligible/new-group.png#<lightbox>)

1. Az **új csoport** lapon adja meg a csoport típusát, nevét és leírását.
1. **Az Azure ad-szerepkörök bekapcsolása a csoporthoz is hozzárendelhető**. Ez a kapcsoló csak a Kiemelt szerepkörű rendszergazdák és a globális rendszergazdák számára látható, mivel ezek csak két szerepkört állíthatnak be.

    [![Az új csoport jogosult legyen a szerepkör-hozzárendelésre](./media/groups-create-eligible/eligible-switch.png "Az új csoport jogosult legyen a szerepkör-hozzárendelésre")](./media/groups-create-eligible/eligible-switch.png#<lightbox>)

1. Válassza ki a csoport tagjait és tulajdonosait. Lehetősége van arra is, hogy szerepköröket rendeljen a csoporthoz, de nincs szükség szerepkör hozzárendelésére.

    [![Vegyen fel tagokat a szerepkörhöz hozzárendelhető csoportba, és rendeljen hozzá szerepköröket.](./media/groups-create-eligible/specify-members.png "Vegyen fel tagokat a szerepkörhöz hozzárendelhető csoportba, és rendeljen hozzá szerepköröket.")](./media/groups-create-eligible/specify-members.png#<lightbox>)

1. A tagok és a tulajdonosok megadása után válassza a **Létrehozás** lehetőséget.

    [![A létrehozás gomb az oldal alján található.](./media/groups-create-eligible/create-button.png "A létrehozás gomb az oldal alján található.")](./media/groups-create-eligible/create-button.png#<lightbox>)

A csoport minden olyan szerepkörrel létrejön, amelyet hozzá lehet rendelni.

## <a name="using-powershell"></a>A PowerShell használata

### <a name="install-the-azure-ad-preview-module"></a>Az Azure AD Preview-modul telepítése

```powershell
Install-Module -Name AzureADPreview
Import-Module -Name AzureADPreview
```

Annak ellenőrzéséhez, hogy a modul készen áll-e a használatra, adja ki a következő parancsot:

```powershell
Get-Module -Name AzureADPreview
```

### <a name="create-a-group-that-can-be-assigned-to-role"></a>Szerepkörhöz hozzárendelhető csoport létrehozása

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true
```

Az ilyen típusú csoportok esetében `isPublic` mindig hamis lesz, és `isSecurityEnabled` mindig igaz lesz.

### <a name="copy-one-groups-users-and-service-principals-into-a-role-assignable-group"></a>Egy csoport felhasználóinak és egyszerű szolgáltatásainak másolása szerepkör-hozzárendelésre szolgáló csoportba

```powershell
#Basic set up
Install-Module -Name AzureADPreview
Import-Module -Name AzureADPreview
Get-Module -Name AzureADPreview

#Connect to Azure AD. Sign in as Privileged Role Administrator or Global Administrator. Only these two roles can create a role-assignable group.
Connect-AzureAD

#Input variabled: Existing group
$idOfExistingGroup = "14044411-d170-4cb0-99db-263ca3740a0c"

#Input variables: New role-assignable group
$groupName = "Contoso_Bellevue_Admins"
$groupDescription = "This group is assigned to Helpdesk Administrator built-in role in Azure AD."
$mailNickname = "contosobellevueadmins"

#Create new security group which is a role assignable group. For creating a Microsoft 365 group, set GroupTypes="Unified" and MailEnabled=$true
$roleAssignablegroup = New-AzureADMSGroup -DisplayName $groupName -Description $groupDescription -MailEnabled $false -MailNickname $mailNickname -SecurityEnabled $true -IsAssignableToRole $true

#Get details of existing group
$existingGroup = Get-AzureADMSGroup -Id $idOfExistingGroup
$membersOfExistingGroup = Get-AzureADGroupMember -ObjectId $existingGroup.Id

#Copy users and service principals from existing group to new group
foreach($member in $membersOfExistingGroup){
if($member.ObjectType -eq 'User' -or $member.ObjectType -eq 'ServicePrincipal'){
Add-AzureADGroupMember -ObjectId $roleAssignablegroup.Id -RefObjectId $member.ObjectId
}
}
```

## <a name="using-microsoft-graph-api"></a>Microsoft Graph API használata

### <a name="create-a-role-assignable-group-in-azure-ad"></a>Szerepkörhöz rendelt csoport létrehozása az Azure AD-ben

```http
POST https://graph.microsoft.com/beta/groups
{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD.",
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

Az ilyen típusú csoportok esetében `isPublic` mindig hamis lesz, és `isSecurityEnabled` mindig igaz lesz.

## <a name="next-steps"></a>Következő lépések

- [Szerepkör hozzárendelése felhőbeli csoporthoz](groups-assign-role.md)
- [Felhőbeli csoportok használata a szerepkör-hozzárendelések kezeléséhez](groups-concept.md)
- [A felhőbeli csoportokhoz hozzárendelt szerepkörökkel kapcsolatos hibák elhárítása](groups-faq-troubleshooting.md)
