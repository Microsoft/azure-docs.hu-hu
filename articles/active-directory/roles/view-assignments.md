---
title: Egyéni szerepkör-hozzárendelések megtekintése a Azure Active Directory portálon | Microsoft Docs
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
ms.openlocfilehash: c3e2bbc88b896d65592ba742fece723d3f5e168f
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103015760"
---
# <a name="view-custom-role-assignments-using-azure-active-directory"></a>Egyéni szerepkör-hozzárendelések megtekintése Azure Active Directory használatával

Ez a cikk azt ismerteti, hogyan lehet megtekinteni a Azure Active Directoryhoz rendelt egyéni szerepköröket (Azure AD). Azure Active Directory (Azure AD) esetében a szerepköröket szervezetre kiterjedő hatókörben vagy egyetlen alkalmazási hatókörben lehet hozzárendelni.

- A szerepkör-hozzárendelések a szervezetre kiterjedő hatókörben hozzáadódnak a szolgáltatáshoz, és az egyetlen alkalmazás szerepkör-hozzárendelések listájában láthatók.
- Az Egyalkalmazásos hatókörhöz tartozó szerepkör-hozzárendelések nem jelennek meg, és nem láthatók a szervezetre kiterjedő hatókörű hozzárendelések listájában.

## <a name="view-role-assignments-in-the-azure-portal"></a>Szerepkör-hozzárendelések megtekintése a Azure Portal

Ez az eljárás azt ismerteti, hogyan kell megtekinteni egy szerepkör hozzárendeléseit az egész szervezetre kiterjedő hatókörrel.

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://aad.portal.azure.com) a Kiemelt szerepkörű rendszergazda vagy a globális rendszergazdai engedélyekkel az Azure ad-szervezetben.
1. Válassza a **Azure Active Directory** lehetőséget, válassza a **szerepkörök és rendszergazdák** lehetőséget, majd válasszon ki egy szerepkört a megnyitásához, és tekintse meg a tulajdonságait.
1. Válassza a **hozzárendelések** lehetőséget a szerepkör hozzárendeléseinek megtekintéséhez.

    ![Szerepkör-hozzárendelések és engedélyek megtekintése, amikor megnyit egy szerepkört a listából](./media/view-assignments/role-assignments.png)

## <a name="view-role-assignments-using-azure-ad-powershell"></a>Szerepkör-hozzárendelések megtekintése az Azure AD PowerShell-lel

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

### <a name="view-the-assignments-of-a-role"></a>Szerepkör hozzárendeléseinek megtekintése

Példa egy szerepkör hozzárendeléseinek megtekintésére.

``` PowerShell
# Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole

# Fetch a specific directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Fetch role membership for a role
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="view-role-assignments-using-microsoft-graph-api"></a>Szerepkör-hozzárendelések megtekintése Microsoft Graph API használatával

Ez a szakasz azt ismerteti, hogyan tekintheti meg egy szerepkör hozzárendeléseit a szervezetre kiterjedő hatókörrel.  Ha Graph API használatával szeretné megtekinteni az alkalmazásra vonatkozó hatókör-hozzárendeléseket, akkor az [Egyéni szerepkörök hozzárendelése a Graph APIhoz](custom-assign-graph.md)című részében ismertetett műveleteket használhatja.

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

## <a name="view-assignments-of-single-application-scope"></a>Egyalkalmazásos hatókör hozzárendeléseinek megtekintése

Ez a szakasz azt ismerteti, hogyan tekintheti meg egy szerepkör hozzárendeléseit egy adott alkalmazási hatókörrel. Ez a szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://aad.portal.azure.com) a Kiemelt szerepkörű rendszergazda vagy a globális rendszergazdai engedélyekkel az Azure ad-szervezetben.
1. Válassza a **Alkalmazásregisztrációk** lehetőséget, majd válassza ki az alkalmazás regisztrációját a tulajdonságainak megtekintéséhez. Előfordulhat, hogy az **összes alkalmazás** lehetőséget kell választania az Azure ad-szervezetben az alkalmazások regisztrálásának teljes listájának megtekintéséhez.

    ![Alkalmazások regisztrálásának létrehozása vagy szerkesztése a Alkalmazásregisztrációk lapon](./media/view-assignments/app-reg-all-apps.png)

1. Az alkalmazás regisztrálása területen válassza a **szerepkörök és rendszergazdák** lehetőséget, majd válasszon egy szerepkört a tulajdonságainak megtekintéséhez.

    ![Alkalmazás-regisztrációs szerepkör-hozzárendelések megtekintése a Alkalmazásregisztrációk lapról](./media/view-assignments/app-reg-assignments.png)

1. Válassza a **hozzárendelések** lehetőséget a szerepkör hozzárendeléseinek megtekintéséhez. Ha megnyitja a hozzárendelések nézetet az alkalmazás regisztrációján belül, megjelenik az erre az Azure AD-erőforrásra hatókört képező hozzárendelések.

    ![Alkalmazás-regisztrációs szerepkör-hozzárendelések megtekintése az alkalmazások regisztrációjának tulajdonságaiból](./media/view-assignments/app-reg-assignments-2.png)

## <a name="next-steps"></a>Következő lépések

* Nyugodtan ossza meg velünk az [Azure ad rendszergazdai szerepkörökkel foglalkozó fórumát](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* A szerepkörökkel és a rendszergazdai szerepkör-hozzárendeléssel kapcsolatos további információkért lásd: [rendszergazdai szerepkörök hozzárendelése](permissions-reference.md).
* Az alapértelmezett felhasználói engedélyek összehasonlítását lásd: a [vendég és a tag alapértelmezett felhasználói engedélyeinek összehasonlítása](../fundamentals/users-default-permissions.md).
