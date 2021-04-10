---
title: Szerepkörök kiosztása és listázása a felügyeleti egység hatókörével – Azure Active Directory | Microsoft Docs
description: A felügyeleti egységekkel korlátozhatja a szerepkör-hozzárendelések hatókörét Azure Active Directoryban.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fc0c4bf9f71a8fe7e8cf49b83d32ac594dbe062
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103011389"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Hatókörrel rendelkező szerepkörök társítása egy felügyeleti egységhez

Azure Active Directory (Azure AD) esetében a részletesebb felügyeleti felügyelet érdekében a felhasználókat egy vagy több felügyeleti egységre korlátozódó hatókörrel rendelheti hozzá egy Azure AD-szerepkörhöz.

A PowerShell és a Microsoft Graph használatának előkészítése a felügyeleti egység felügyeletéhez: első [lépések](admin-units-manage.md#get-started).

## <a name="available-roles"></a>Elérhető szerepkörök

Szerepkör  |  Leírás
----- |  -----------
Hitelesítés rendszergazdája  |  A csak a hozzárendelt felügyeleti egységben lévő nem rendszergazda felhasználók számára biztosít hozzáférést a hitelesítési módszer megtekintéséhez, beállításához és visszaállításához.
Csoportok rendszergazdája  |  A csak a hozzárendelt felügyeleti egységben kezelheti a csoportok és csoportok beállításait, például az elnevezési és lejárati házirendeket.
Segélyszolgálat rendszergazdája  |  A nem rendszergazdák és az ügyfélszolgálati rendszergazdák jelszavait alaphelyzetbe állíthatja a hozzárendelt felügyeleti egységben.
Licenc rendszergazdája  |  Csak a felügyeleti egységen belül lehet hozzárendelni, eltávolítani és frissíteni a licenc-hozzárendeléseket.
Jelszó-rendszergazda  |  A csak a hozzárendelt felügyeleti egységen belül állíthatja alaphelyzetbe a nem rendszergazdák és a jelszó-rendszergazdák jelszavát.
Felhasználói rendszergazda  |  A a felhasználók és csoportok minden aspektusát képes kezelni, beleértve a korlátozott rendszergazdák jelszavainak alaphelyzetbe állítását a hozzárendelt felügyeleti egységen belül.

## <a name="security-principals-that-can-be-assigned-to-a-scoped-role"></a>Hatókörrel rendelkező szerepkörhöz hozzárendelhető rendszerbiztonsági tag

A következő rendszerbiztonsági tag a felügyeleti egység hatókörével rendelkező szerepkörhöz rendelhető hozzá:

* Felhasználók
* Szerepkörhöz hozzárendelhető felhőalapú csoportok (előzetes verzió)
* Egyszerű szolgáltatásnév (SPN)

## <a name="assign-a-scoped-role"></a>Hatókörrel rendelkező szerepkör kiosztása

Hatókörrel rendelkező szerepkört a Azure Portal, a PowerShell vagy a Microsoft Graph használatával rendelhet hozzá.

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

1. A Azure Portal nyissa meg az **Azure ad**-t.

1. Válassza a **felügyeleti egységek** lehetőséget, majd válassza ki azt a felügyeleti egységet, amelyhez felhasználói szerepkör hatókörét hozzá szeretné rendelni. 

1. A bal oldali ablaktáblán válassza a **szerepkörök és rendszergazdák** lehetőséget az összes elérhető szerepkör listázásához.

   ![Képernyőkép a "szerepkör és rendszergazdák" panelről, ahol kiválaszthatja azt a felügyeleti egységet, amelynek a szerepkör-hatókörét hozzá szeretné rendelni.](./media/admin-units-assign-roles/select-role-to-scope.png)

1. Válassza ki a hozzárendelni kívánt szerepkört, majd válassza a **hozzárendelések hozzáadása** lehetőséget. 

1. A **hozzárendelések hozzáadása** panelen válasszon ki egy vagy több felhasználót, amelyet hozzá szeretne rendelni a szerepkörhöz.

   ![Válassza ki a hatókörhöz tartozó szerepkört, majd válassza a hozzárendelések hozzáadása elemet.](./media/admin-units-assign-roles/select-add-assignment.png)

> [!Note]
> Ha Azure AD Privileged Identity Management (PIM) használatával szeretne szerepkört hozzárendelni egy felügyeleti egységhez, tekintse meg a következőt: [Azure ad-szerepkörök társítása a PIM-ben](../privileged-identity-management/pim-how-to-add-role-to-user.md?tabs=new#assign-a-role-with-restricted-scope).

### <a name="use-powershell"></a>A PowerShell használata

```powershell
$adminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Account Administrator"
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$roleMember = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo
$roleMember.ObjectId = $adminUser.ObjectId
Add-AzureADMSScopedRoleMembership -ObjectId $adminUnitObj.ObjectId -RoleObjectId $role.ObjectId -RoleMemberInfo $roleMember
```

A Kiemelt szakaszt szükség szerint módosíthatja az adott környezetben.

### <a name="use-microsoft-graph"></a>Microsoft Graph használata

Kérés

```http
POST /directory/administrativeUnits/{admin-unit-id}/scopedRoleMembers
```
    
Törzs

```http
{
  "roleId": "roleId-value",
  "roleMemberInfo": {
    "id": "id-value"
  }
}
```

## <a name="view-a-list-of-the-scoped-admins-in-an-administrative-unit"></a>A hatókörrel rendelkező rendszergazdák listájának megtekintése egy felügyeleti egységben

A hatókörrel rendelkező rendszergazdák listáját a Azure Portal, a PowerShell vagy a Microsoft Graph használatával tekintheti meg.

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

A felügyeleti egység hatókörével létrehozott összes szerepkör-hozzárendelést megtekintheti az [Azure ad felügyeleti egységek szakaszában](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit). 

1. A Azure Portal nyissa meg az **Azure ad**-t.

1. A bal oldali panelen válassza a **felügyeleti egységek** lehetőséget, majd válassza ki a megtekinteni kívánt szerepkör-hozzárendelések listáját a felügyeleti egységben. 

1. Válassza a **szerepkörök és rendszergazdák** lehetőséget, majd nyisson meg egy szerepkört a hozzárendelések megtekintéséhez a felügyeleti egységben.

### <a name="use-powershell"></a>A PowerShell használata

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADMSScopedRoleMembership -ObjectId $adminUnitObj.ObjectId | fl *
```

A Kiemelt szakaszt szükség szerint módosíthatja az adott környezetben.

### <a name="use-microsoft-graph"></a>Microsoft Graph használata

Kérés

```http
GET /directory/administrativeUnits/{admin-unit-id}/scopedRoleMembers
```

Törzs

```http
{}
```

## <a name="next-steps"></a>Következő lépések

- [Felhőbeli csoportok használata a szerepkör-hozzárendelések kezeléséhez](groups-concept.md)
- [Felhőalapú csoportokhoz rendelt szerepkörök – problémamegoldás](groups-faq-troubleshooting.md)
