---
title: Jogosult tulajdonosok és tagok kiosztása Kiemelt jogosultságú hozzáférési csoportokhoz – Azure Active Directory
description: Megtudhatja, hogyan rendelhet hozzá jogosult tulajdonosokat vagy szerepkörhöz hozzárendelhető csoport tagjait Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 08/18/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48c4473cfafce1215219251c47bce1d5730645fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91534420"
---
# <a name="assign-eligibility-for-a-privileged-access-group-preview-in-privileged-identity-management"></a>Jogosultsági szintű hozzáférési csoport (előzetes verzió) kiosztása Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) segítségével kezelheti az Azure AD-ben az emelt szintű hozzáférési csoportok hozzárendeléseinek jogosultságát és aktiválását. Jogosultságot rendelhet a csoport tagjaihoz vagy tulajdonosainak.

>[!NOTE]
>Minden olyan felhasználónak, aki jogosultsági szintű hozzáférési csoport tagságához vagy tulajdonosához jogosult, rendelkeznie kell egy prémium szintű Azure AD P2 licenccel. További információ: [a Privileged Identity Management használatára vonatkozó licencfeltételek](subscription-requirements.md).

## <a name="assign-an-owner-or-member-of-a-group"></a>Tulajdonos vagy csoport tagjainak kiosztása

Kövesse az alábbi lépéseket, hogy a felhasználók jogosultak legyenek egy emelt szintű hozzáférési csoport tagjának vagy tulajdonosának a használatára.

1. [Jelentkezzen be az Azure ad-be](https://aad.portal.azure.com) globális rendszergazdai vagy csoport-tulajdonosi engedélyekkel.
1. Válassza a **csoportok** lehetőséget, majd válassza ki a kezelni kívánt szerepkör-hozzárendelhető csoportot. Kereshet vagy szűrheti a listát.

    ![szerepkörhöz hozzárendelhető csoport keresése a PIM-ben való kezeléshez](./media/groups-assign-member-owner/groups-list-in-azure-ad.png)

1. Nyissa meg a csoportot, és válassza a **privilegizált hozzáférés (előzetes verzió)** lehetőséget.

    ![A Privileged Identity Management felület megnyitása](./media/groups-assign-member-owner/groups-discover-groups.png)

1. Válassza a **hozzárendelések hozzáadása** lehetőséget.

    ![Új hozzárendelés ablaktábla](./media/groups-assign-member-owner/groups-add-assignment.png)

1. Válassza ki azokat a tagokat vagy tulajdonosokat, akik jogosultak legyenek az emelt szintű hozzáférési csoportra.

    ![Képernyőkép: a "hozzárendelések hozzáadása" lap megnyitása a "tag kiválasztása vagy a csoport kijelölése" ablaktáblán, és a "kiválasztás" gomb kiemelve.](./media/groups-assign-member-owner/add-assignments.png)

1. Válassza a **tovább** lehetőséget a tagság vagy a tulajdonos időtartamának megadásához.

    ![Tag vagy csoport ablaktábla kiválasztása](./media/groups-assign-member-owner/assignment-duration.png)

1. A **hozzárendelés típusa** listában válassza a **jogosult** vagy az **aktív** lehetőséget. A Kiemelt jogosultságú hozzáférési csoportok két különböző hozzárendelési típust biztosítanak:

    - A **jogosult** hozzárendelésekhez a szerepkör tagjának kell lennie a szerepkör használatára vonatkozó művelet végrehajtásához. A műveletek tartalmazhatják a többtényezős hitelesítés (MFA) ellenőrzését, üzleti indoklást biztosítanak, vagy a kijelölt jóváhagyók jóváhagyását kérik.

    - Az **aktív** hozzárendelésekhez nem szükséges, hogy a tag bármilyen műveletet hajtson végre a szerepkör használatához. Az aktívként hozzárendelt tagok rendelkeznek a szerepkörhöz tartozó jogosultságokkal.

1. Ha a hozzárendelésnek állandónak kell lennie (tartósan jogosult vagy véglegesen kiosztott), jelölje be a **véglegesen** jelölőnégyzetet. A szervezet beállításaitól függően előfordulhat, hogy a jelölőnégyzet nem jelenik meg, vagy előfordulhat, hogy nem szerkeszthető.

1. Ha elkészült, válassza a **hozzárendelés** lehetőséget.

1. Az új szerepkör-hozzárendelés létrehozásához válassza a **Hozzáadás** lehetőséget. Megjelenik az állapot értesítése.

    ![Új hozzárendelés – értesítés](./media/groups-assign-member-owner/groups-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Meglévő szerepkör-hozzárendelés frissítése vagy eltávolítása

A meglévő szerepkör-hozzárendelések frissítéséhez vagy eltávolításához kövesse az alábbi lépéseket.

1. [Jelentkezzen be az Azure ad-be](https://aad.portal.azure.com) globális rendszergazdai vagy csoport-tulajdonosi engedélyekkel.
1. Válassza a **csoportok** lehetőséget, majd válassza ki a kezelni kívánt szerepkör-hozzárendelhető csoportot. Kereshet vagy szűrheti a listát.

    ![szerepkörhöz hozzárendelhető csoport keresése a PIM-ben való kezeléshez](./media/groups-assign-member-owner/groups-list-in-azure-ad.png)

1. Nyissa meg a csoportot, és válassza a **privilegizált hozzáférés (előzetes verzió)** lehetőséget.

    ![A Privileged Identity Management felület megnyitása](./media/groups-assign-member-owner/groups-discover-groups.png)

1. Válassza ki a frissíteni vagy eltávolítani kívánt szerepkört.

1. Keresse meg a szerepkör-hozzárendelést a **jogosult szerepkörök** vagy az **aktív szerepkörök** lapon.

    ![Szerepkör-hozzárendelés frissítése vagy eltávolítása](./media/groups-assign-member-owner/groups-bring-under-management.png)

1. A szerepkör-hozzárendelés frissítéséhez vagy eltávolításához válassza a **frissítés** vagy az **Eltávolítás** lehetőséget.

    További információ a szerepkör-hozzárendelés kibővítéséről: [Azure-beli erőforrás-szerepkörök kiterjesztése vagy megújítása Privileged Identity Managementban](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>Következő lépések

- [Azure-beli erőforrás-szerepkörök kiterjesztése vagy megújítása Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Az Azure erőforrás-szerepkör beállításainak konfigurálása Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Azure AD-szerepkörök kiosztása Privileged Identity Management](pim-how-to-add-role-to-user.md)
