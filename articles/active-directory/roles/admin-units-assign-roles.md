---
title: Szerepkörök hozzárendelése és listába sorolás felügyeletiegység-hatókörrel – Azure Active Directory | Microsoft Docs
description: A felügyeleti egységek használatával korlátozhatja a szerepkör-hozzárendelések hatókörét a Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 04/14/2021
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: e24bf5df84015ada6b62c35fdd29571c66e06ebd
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505258"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Hatókörrel rendelkező szerepkörök hozzárendelése felügyeleti egységhez

A Azure Active Directory (Azure AD) használatával a részletesebb felügyeleti vezérlés érdekében felhasználókat rendelhet hozzá egy Azure AD-szerepkörhöz egy vagy több felügyeleti egységre korlátozott hatókörrel.

A PowerShell és a felügyeleti Microsoft Graph használatának előkészítéséhez lásd: [Első lépések.](admin-units-manage.md#get-started)

## <a name="available-roles"></a>Elérhető szerepkörök

Szerepkör  |  Leírás
----- |  -----------
Hitelesítési rendszergazda  |  Csak a hozzárendelt felügyeleti egységben található összes nem rendszergazda felhasználó hitelesítési módszerének megtekintéséhez, beállításhoz és alaphelyzetbe állításhoz rendelkezik hozzáféréssel.
Csoport-rendszergazda  |  Csak a hozzárendelt felügyeleti egységben kezelheti a csoportok és csoportok beállításainak minden aspektusát, például az elnevezési és elévülési szabályzatokat.
Helpdesk Administrator  |  Csak a hozzárendelt felügyeleti egységben állíthatja vissza a nem rendszergazdai és az informatikai rendszergazdák jelszavát.
Licenc-rendszergazda  |  Csak a felügyeleti egységen belül rendelhet hozzá, távolíthat el és frissíthet licenc-hozzárendeléseket.
Jelszókezelő  |  Csak a hozzárendelt felügyeleti egységen belül állíthatja vissza a nem rendszergazdák és a jelszó-rendszergazdák jelszavát.
Felhasználói rendszergazda  |  Kezelheti a felhasználók és csoportok minden aspektusát, beleértve a jelszavak visszaállítását a korlátozott rendszergazdák számára csak a hozzárendelt felügyeleti egységen belül.

## <a name="security-principals-that-can-be-assigned-to-a-scoped-role"></a>Hatókörrel kapcsolatos szerepkörhöz hozzárendelhető rendszerbiztonsági tag

A következő rendszerbiztonsági tagokat lehet hozzárendelni egy felügyeletiegység-hatókörrel rendelkező szerepkörhöz:

* Felhasználók
* Szerepkörhöz hozzárendelhető felhőcsoportok (előzetes verzió)
* Egyszerű szolgáltatásnév (SPN)

## <a name="assign-a-scoped-role"></a>Hatókörrel felosztott szerepkör hozzárendelése

Hatókörrel hatókörrel tartozó szerepkört az Azure Portal, a PowerShell vagy a Microsoft Graph.

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

1. A Azure Portal az **Azure AD-be.**

1. Válassza **a Felügyeleti egységek** lehetőséget, majd válassza ki azt a felügyeleti egységet, amelyhez felhasználói szerepköri hatókört kíván hozzárendelni. 

1. A bal oldali panelen válassza a **Szerepkörök és** rendszergazdák lehetőséget az összes elérhető szerepkör listához.

   ![Képernyőkép a "Szerepkör és rendszergazdák" panelről azon felügyeleti egység kiválasztásához, amelynek szerepköri hatókörét hozzá szeretné rendelni.](./media/admin-units-assign-roles/select-role-to-scope.png)

1. Válassza ki a hozzárendelni kívánt szerepkört, majd válassza a **Hozzárendelések hozzáadása lehetőséget.** 

1. A **Hozzárendelések hozzáadása panelen** válasszon ki egy vagy több, a szerepkörhöz hozzárendelni kívánt felhasználót.

   ![Válassza ki a hatókörhöz tartozó szerepkört, majd válassza a Hozzárendelések hozzáadása lehetőséget](./media/admin-units-assign-roles/select-add-assignment.png)

> [!Note]
> Ha szerepkört szeretne hozzárendelni egy felügyeleti egységhez a Azure AD Privileged Identity Management (PIM) használatával, lásd: Azure AD-szerepkörök hozzárendelése a [PIM-ban.](../privileged-identity-management/pim-how-to-add-role-to-user.md?tabs=new#assign-a-role-with-restricted-scope)

### <a name="use-powershell"></a>A PowerShell használata

```powershell
$adminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Administrator"
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$roleMember = New-Object -TypeName Microsoft.Open.MSGraph.Model.MsRoleMemberInfo
$roleMember.Id = $adminUser.ObjectId
Add-AzureADMSScopedRoleMembership -Id $adminUnitObj.Id -RoleId $role.ObjectId -RoleMemberInfo $roleMember
```

A kiemelt szakaszt szükség szerint módosíthatja az adott környezetben.

### <a name="use-microsoft-graph"></a>A Microsoft Graph

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

A hatókörrel alá tartozó rendszergazdák listáját a következő parancsokkal Azure Portal, a PowerShell-Microsoft Graph.

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

A felügyeletiegység-hatókörrel létrehozott összes szerepkör-hozzárendelést az Azure AD Felügyeleti egységek [szakaszában tudja megtekinteni.](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit) 

1. A Azure Portal az **Azure AD-be.**

1. A bal oldali panelen válassza **a** Felügyeleti egységek lehetőséget, majd válassza ki a megtekinteni kívánt szerepkör-hozzárendelések listájához a felügyeleti egységet. 

1. Válassza **a Szerepkörök és rendszergazdák** lehetőséget, majd nyisson meg egy szerepkört a hozzárendelések megtekintéséhez a felügyeleti egységben.

### <a name="use-powershell"></a>A PowerShell használata

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADMSScopedRoleMembership -Id $adminUnitObj.Id | fl *
```

A kiemelt szakaszt szükség szerint módosíthatja az adott környezetben.

### <a name="use-microsoft-graph"></a>A Microsoft Graph

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
- [Felhőcsoportokhoz rendelt szerepkörök hibaelhárítása](groups-faq-troubleshooting.md)
