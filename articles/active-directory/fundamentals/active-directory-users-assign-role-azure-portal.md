---
title: Azure AD-szerepkörök hozzárendelése felhasználókhoz – Azure Active Directory | Microsoft Docs
description: Útmutatás rendszergazdai és nem rendszergazdai szerepkörök hozzárendelésére a Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 08/31/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4ffcad6f6be16ba7ac3674d710dd543f729f0c3
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575411"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Rendszergazdai és nem rendszergazdai szerepkörök hozzárendelése a Azure Active Directory

A Azure Active Directory (Azure AD) szolgáltatásban, ha az egyik felhasználónak engedélyre van szüksége az Azure AD-erőforrások kezeléséhez, hozzá kell rendelnie azokat egy olyan szerepkörhöz, amely biztosítja a szükséges engedélyeket. További információ arról, hogy mely szerepkörök kezelik az Azure-erőforrásokat, és mely szerepkörök kezelik az Azure AD-erőforrásokat: Klasszikus előfizetés-rendszergazdai szerepkörök, Azure-szerepkörök és [Azure AD-szerepkörök.](../../role-based-access-control/rbac-and-directory-admin-roles.md)

További információ az elérhető Azure AD-szerepkörökről: Rendszergazdai szerepkörök hozzárendelése a [Azure Active Directory.](../roles/permissions-reference.md) Felhasználók hozzáadásához lásd: [Új felhasználók hozzáadása a Azure Active Directory.](add-users-azure-active-directory.md)

## <a name="assign-roles"></a>Szerepkörök hozzárendelése

Az Azure AD-szerepkörök felhasználókhoz való hozzárendelésének gyakori módja a felhasználók **Hozzárendelt** szerepkörök lapján található. A felhasználók jogosultságát úgy is beállíthatja, hogy az adott időpontban jogosultságot emeltek egy szerepkörre a Privileged Identity Management (PIM) használatával. A PIM használatával kapcsolatos további információkért lásd: [Privileged Identity Management.](../privileged-identity-management/index.yml)

> [!Note]
> Ha P2 prémium szintű Azure AD rendelkezik, és már használja a PIM-et, minden szerepkör-felügyeleti feladat a következő [Privileged Identity Management történik:](../roles/manage-roles-portal.md). Ez a funkció jelenleg csak egy szerepkör hozzárendelését teszi lehetővé egyszerre. Jelenleg nem jelölhet ki több szerepkört, és nem rendelheti hozzá őket egyszerre egy felhasználóhoz.
>
> ![PIM-ben felügyelt Azure AD-szerepkörök olyan felhasználók számára, akik már használják a PIM-et, és prémium P2 licenccel](./media/active-directory-users-assign-role-azure-portal/pim-manages-roles-for-p2.png)

## <a name="assign-a-role-to-a-user"></a>Szerepkör hozzárendelése felhasználóhoz

1. Az Azure Portal, és jelentkezzen be a címtárhoz globális rendszergazda fiókkal. [](https://portal.azure.com/)

2. Keresse meg és válassza ki az **Azure Active Directoryt**.

      ![Azure Portal keresés a Azure Active Directory](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)

3. Válassza a **Felhasználók** lehetőséget.

4. Keresse meg és válassza ki a szerepkör-hozzárendelést kapó felhasználót. Például: _Alain Charon._

      ![Minden felhasználó lap – válassza ki a felhasználót](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. Az **Alain Charon – Profil lapon** válassza a Hozzárendelt **szerepkörök lehetőséget.**

    Megjelenik **az Alain Charon - Administrative roles (Alain Charon – Rendszergazdai szerepkörök)** lap.

6. Válassza **a Hozzárendelések hozzáadása** lehetőséget, válassza ki az Alainhez hozzárendelni kívánt szerepkört _(például_ alkalmazás-rendszergazda ), majd válassza a **Kijelölés lehetőséget.**

    ![Hozzárendelt szerepkörök lap – a kiválasztott szerepkör megjelenítése](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    A alkalmazás-rendszergazda szerepkör Alain Charonhoz van rendelve, és az **Alain Charon – Rendszergazdai szerepkörök lapon jelenik** meg.

## <a name="remove-a-role-assignment"></a>Szerepkör-hozzárendelés eltávolítása

Ha el kell távolítania a szerepkör-hozzárendelést egy felhasználótól, azt az **Alain Charon – Rendszergazdai** szerepkörök oldalon is használhatja.

### <a name="to-remove-a-role-assignment-from-a-user"></a>Szerepkör-hozzárendelés eltávolítása egy felhasználóból

1. Válassza **Azure Active Directory** lehetőséget, válassza a **Felhasználók** lehetőséget, majd keresse meg és válassza ki azt a felhasználót, aki eltávolítja a szerepkör-hozzárendelést. Például: _Alain Charon._

2. Válassza **a Hozzárendelt szerepkörök** lehetőséget, válassza **alkalmazás-rendszergazda,** majd a Hozzárendelés eltávolítása **lehetőséget.**

    ![A Hozzárendelt szerepkörök lap, amely a kiválasztott szerepkört és az eltávolítási lehetőséget mutatja](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    A alkalmazás-rendszergazda szerepkör el lett távolítva Alain Charonból, és többé nem jelenik meg az **Alain Charon – Rendszergazdai szerepkörök** lapon.

## <a name="next-steps"></a>Következő lépések

- [Felhasználók hozzáadása vagy törlése](add-users-azure-active-directory.md)

- [Profilinformációk hozzáadása vagy módosítása](active-directory-users-profile-azure-portal.md)

- [Vendégfelhasználók hozzáadása másik címtárból](../external-identities/what-is-b2b.md)

A további felhasználókezelési feladatok a felhasználókezelési [dokumentációban Azure Active Directory érhetők el.](../enterprise-users/index.yml)
