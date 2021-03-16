---
title: Azure AD szerepkör-hozzárendelések listázása
description: Most már megtekintheti és felügyelheti Azure Active Directory rendszergazdai szerepkör tagjait a Azure Active Directory felügyeleti központban.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: de546ef091b1a8e996f286b0c9af45e93488b5b4
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467653"
---
# <a name="list-azure-ad-role-assignments"></a>Azure AD szerepkör-hozzárendelések listázása

Ez a cikk a Azure Active Directory (Azure AD) által hozzárendelt szerepkörök listázását ismerteti. Azure Active Directory (Azure AD) esetében a szerepköröket szervezetre kiterjedő hatókörben vagy egyetlen alkalmazási hatókörben lehet hozzárendelni.

- A szerepkör-hozzárendelések a szervezetre kiterjedő hatókörben hozzáadódnak a szolgáltatáshoz, és az egyetlen alkalmazás szerepkör-hozzárendelések listájában láthatók.
- Az Egyalkalmazásos hatókörhöz tartozó szerepkör-hozzárendelések nem jelennek meg, és nem láthatók a szervezetre kiterjedő hatókörű hozzárendelések listájában.

## <a name="list-role-assignments-in-the-azure-portal"></a>Szerepkör-hozzárendelések listázása a Azure Portal

Ez az eljárás azt ismerteti, hogyan listázhatja a szerepkör-hozzárendeléseket a szervezetre kiterjedő hatókörrel.

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://aad.portal.azure.com) a Kiemelt szerepkörű rendszergazda vagy a globális rendszergazdai engedélyekkel az Azure ad-szervezetben.
1. Válassza a **Azure Active Directory** lehetőséget, válassza a **szerepkörök és rendszergazdák** lehetőséget, majd válasszon ki egy szerepkört a megnyitásához, és tekintse meg a tulajdonságait.
1. A szerepkör-hozzárendelések listázásához válassza a **hozzárendelések** lehetőséget.

    ![Szerepkör-hozzárendelések és engedélyek listázása, amikor megnyit egy szerepkört a listából](./media/view-assignments/role-assignments.png)

## <a name="list-my-role-assignments"></a>Saját szerepkör-hozzárendelések listázása

Egyszerűen listázhatja saját engedélyeit is. Válassza ki a **szerepkört** a **szerepkörök és rendszergazdák** lapon, hogy megtekintse a jelenleg Önhöz rendelt szerepköröket.

## <a name="download-role-assignments"></a>Szerepkör-hozzárendelések letöltése

Egy adott szerepkör összes hozzárendelésének letöltéséhez a **szerepkörök és rendszergazdák** lapon válasszon ki egy szerepkört, majd válassza a **szerepkör-hozzárendelések letöltése** lehetőséget. Egy CSV-fájl, amely felsorolja a szerepkör összes hatókörében lévő hozzárendeléseket.

![szerepkör összes hozzárendelésének letöltése](./media/view-assignments/download-role-assignments.png)

## <a name="list-role-assignments-using-azure-ad-powershell"></a>Szerepkör-hozzárendelések listázása az Azure AD PowerShell használatával

Ez a szakasz azt ismerteti, hogyan tekintheti meg egy szerepkör hozzárendeléseit a szervezetre kiterjedő hatókörrel. Ez a cikk a [Azure Active Directory PowerShell 2-es verziójának](/powershell/module/azuread/#directory_roles) modulját használja. Ha az Egyalkalmazásos hatókör-hozzárendeléseket a PowerShell használatával szeretné megtekinteni, használja a parancsmagokat az [Egyéni szerepkörök hozzárendelése a PowerShell](custom-assign-powershell.md)-lel című részhez.

### <a name="prepare-powershell"></a>A PowerShell előkészítése

Először [le kell töltenie az Azure ad Preview PowerShell-modult](https://www.powershellgallery.com/packages/AzureAD/).

Az Azure AD PowerShell-modul telepítéséhez használja az alábbi parancsokat:

``` PowerShell
Install-Module -Name AzureADPreview
Import-Module -Name AzureADPreview
```

A következő parancs használatával ellenőrizheti, hogy a modul használatra kész-e:

``` PowerShell
Get-Module -Name AzureADPreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    AzureADPreview               {Add-AzureADAdministrati...}
```

### <a name="list-role-assignments"></a>Szerepkör-hozzárendelések felsorolása

Példa a szerepkör-hozzárendelések listázására.

``` PowerShell
# Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole

# Fetch a specific directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Fetch role membership for a role
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="list-role-assignments-using-microsoft-graph-api"></a>Szerepkör-hozzárendelések listázása Microsoft Graph API használatával

Ez a szakasz azt ismerteti, hogyan listázhatja a szerepkör-hozzárendeléseket a szervezetre kiterjedő hatókörrel.  Ha Graph API használatával kívánja listázni az Egyalkalmazásos hatókörű szerepkör-hozzárendeléseket, használhatja az [Egyéni szerepkörök hozzárendelése a Graph APIhoz](custom-assign-graph.md)műveleteket.

HTTP-kérelem egy adott szerepkör-definícióhoz tartozó szerepkör-hozzárendelés beszerzéséhez.

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

Reagálás

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

## <a name="list-role-assignments-with-single-application-scope"></a>Szerepkör-hozzárendelések listázása egyetlen alkalmazási hatókörrel

Ez a szakasz azt ismerteti, hogyan lehet kilistázni a szerepkör-hozzárendeléseket az Egyalkalmazásos hatókörrel. Ez a szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://aad.portal.azure.com) a Kiemelt szerepkörű rendszergazda vagy a globális rendszergazdai engedélyekkel az Azure ad-szervezetben.
1. Válassza a **Alkalmazásregisztrációk** lehetőséget, majd válassza ki az alkalmazás regisztrációját a tulajdonságainak megtekintéséhez. Előfordulhat, hogy az **összes alkalmazás** lehetőséget kell választania az Azure ad-szervezetben az alkalmazások regisztrálásának teljes listájának megtekintéséhez.

    ![Alkalmazások regisztrálásának létrehozása vagy szerkesztése a Alkalmazásregisztrációk lapon](./media/view-assignments/app-reg-all-apps.png)

1. Az alkalmazás regisztrálása területen válassza a **szerepkörök és rendszergazdák** lehetőséget, majd válasszon egy szerepkört a tulajdonságainak megtekintéséhez.

    ![Alkalmazás-regisztrációs szerepkör-hozzárendelések listázása a Alkalmazásregisztrációk lapról](./media/view-assignments/app-reg-assignments.png)

1. A szerepkör-hozzárendelések listázásához válassza a **hozzárendelések** lehetőséget. Ha az alkalmazás regisztrációján belül megnyitja a hozzárendelések lapot, megjelenik az ehhez az Azure AD-erőforráshoz tartozó szerepkör-hozzárendelés.

    ![Alkalmazás-regisztrálási szerepkör-hozzárendelések listázása egy alkalmazás-regisztráció tulajdonságaiból](./media/view-assignments/app-reg-assignments-2.png)

## <a name="next-steps"></a>Következő lépések

* Nyugodtan ossza meg velünk az [Azure ad rendszergazdai szerepkörökkel foglalkozó fórumát](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* A szerepkörökkel és a rendszergazdai szerepkör-hozzárendeléssel kapcsolatos további információkért lásd: [rendszergazdai szerepkörök hozzárendelése](permissions-reference.md).
* Az alapértelmezett felhasználói engedélyek összehasonlítását lásd: a [vendég és a tag alapértelmezett felhasználói engedélyeinek összehasonlítása](../fundamentals/users-default-permissions.md).
