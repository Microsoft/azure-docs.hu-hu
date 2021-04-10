---
title: Szerepkör társítása egy csoporthoz Privileged Identity Management használatával az Azure AD-ben | Microsoft Docs
description: Megtudhatja, hogyan rendelhet hozzá Azure Active Directory (Azure AD) szerepkört egy csoporthoz Azure AD Privileged Identity Management (PIM) használatával.
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
ms.openlocfilehash: 02cd3f54823b80ae201316fee29c02616b9d8502
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103012037"
---
# <a name="assign-a-role-to-a-group-using-privileged-identity-management"></a>Szerepkör társítása egy csoporthoz Privileged Identity Management használatával

Ez a cikk azt ismerteti, hogyan rendelhet hozzá Azure Active Directory (Azure AD) szerepkört egy csoporthoz Azure AD Privileged Identity Management (PIM) használatával.

> [!NOTE]
> A Privileged Identity Management frissített verzióját kell használnia ahhoz, hogy a PIM használatával hozzá lehessen rendelni egy csoportot egy Azure AD-szerepkörhöz. Előfordulhat, hogy a PIM régebbi verziójában van, ha az Azure AD-szervezete a Privileged Identity Management API-t használja. Ha igen, lépjen kapcsolatba az aliassal a pim_preview@microsoft.com szervezet áthelyezéséhez és az API frissítéséhez. További információ: [Az Azure ad szerepkörei és funkciói a PIM-ben](../privileged-identity-management/azure-ad-roles-features.md).

## <a name="using-azure-ad-admin-center"></a>Az Azure AD felügyeleti központ használata

1. Jelentkezzen be [Azure ad Privileged Identity Management](https://ms.portal.azure.com/?Microsoft_AAD_IAM_GroupRoles=true&Microsoft_AAD_IAM_userRolesV2=true&Microsoft_AAD_IAM_enablePimIntegration=true#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) Kiemelt szerepkörű rendszergazdaként vagy globális rendszergazdaként a szervezetében.

1. Válassza **Privileged Identity Management**  >  **Azure ad-szerepkörök**  >  **szerepkörök**  >  **hozzáadása hozzárendeléseket**

1. Válasszon ki egy szerepkört, majd válasszon ki egy csoportot. Csak a szerepkör-hozzárendelésre jogosult csoportok jelennek meg, nem minden csoport.

    ![Képernyőkép: a "hozzárendelések hozzáadása" lap, amely kiemelve a "szerepkör kiválasztása" és a "tag (ok)" szakaszban látható.](./media/groups-pim-eligible/select-member.png)

1. Válassza ki a kívánt tagsági beállítást. Az aktiválást igénylő szerepkörök esetében válassza a **jogosult** lehetőséget. Alapértelmezés szerint a felhasználó véglegesen jogosult lenne, de a felhasználó jogosultságának kezdési és befejezési idejét is megadhatja. Ha elkészült, kattintson a Mentés és Hozzáadás gombra a szerepkör-hozzárendelés befejezéséhez.

    ![Válassza ki azt a felhasználót, akihez a szerepkört hozzárendeli](./media/groups-pim-eligible/set-assignment-settings.png)

## <a name="using-powershell"></a>A PowerShell használata

### <a name="download-the-azure-ad-preview-powershell-module"></a>Az Azure AD Preview PowerShell-modul letöltése

Az Azure AD #PowerShell modul telepítéséhez használja a következő parancsmagokat:

```powershell
Install-Module -Name AzureADPreview
Import-Module -Name AzureADPreview
```

Annak ellenőrzéséhez, hogy a modul készen áll-e a használatra, használja a következő parancsmagot:

```powershell
Get-Module -Name AzureADPreview
```

### <a name="assign-a-group-as-an-eligible-member-of-a-role"></a>Csoport társítása egy szerepkör jogosult tagjaként

```powershell
$schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
$schedule.Type = "Once"
$schedule.StartDateTime = "2019-04-26T20:49:11.770Z"
$schedule.endDateTime = "2019-07-25T20:49:11.770Z"
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId aadRoles -Schedule $schedule -ResourceId "[YOUR TENANT ID]" -RoleDefinitionId "9f8c1837-f885-4dfd-9a75-990f9222b21d" -SubjectId "[YOUR GROUP ID]" -AssignmentState "Eligible" -Type "AdminAdd"
```

## <a name="using-microsoft-graph-api"></a>Microsoft Graph API használata

```http
POST
https://graph.microsoft.com/beta/privilegedAccess/aadroles/roleAssignmentRequests
{
 "roleDefinitionId": {roleDefinitionId},
 "resourceId": {tenantId},
 "subjectId": {GroupId},
 "assignmentState": "Eligible",
 "type": "AdminAdd",
 "reason": "reason string",
 "schedule": {
   "startDateTime": {DateTime},
   "endDateTime": {DateTime},
   "type": "Once"
 }
}
```

## <a name="next-steps"></a>Következő lépések

- [Felhőbeli csoportok használata a szerepkör-hozzárendelések kezeléséhez](groups-concept.md)
- [A felhőbeli csoportokhoz hozzárendelt szerepkörökkel kapcsolatos hibák elhárítása](groups-faq-troubleshooting.md)
- [Az Azure AD rendszergazdai szerepkör beállításainak konfigurálása Privileged Identity Management](../privileged-identity-management/pim-how-to-change-default-settings.md)
- [Azure-beli erőforrás-szerepkörök kiosztása Privileged Identity Management](../privileged-identity-management/pim-resource-roles-assign-roles.md)
