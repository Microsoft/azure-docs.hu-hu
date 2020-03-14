---
title: Azure AD-szerepkörök kiosztása a PIM-Azure Active Directoryban | Microsoft Docs
description: Ismerje meg, hogyan rendelhet hozzá Azure AD-szerepköröket Azure AD Privileged Identity Management (PIM) szolgáltatáshoz.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/07/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5048cefaae10cd55091dd72f0b73a3cf9d731a35
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79253272"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Azure AD-szerepkörök kiosztása Privileged Identity Management

A Azure Active Directory (Azure AD) használatával a globális rendszergazda **állandó** Azure ad-rendszergazdai szerepkör-hozzárendeléseket végezhet. Ezek a szerepkör-hozzárendelések a [Azure Portal](../users-groups-roles/directory-assign-admin-roles.md) vagy a [PowerShell-parancsok](/powershell/module/azuread#directory_roles)használatával hozhatók létre.

A Azure AD Privileged Identity Management (PIM) szolgáltatás azt is lehetővé teszi, hogy a Kiemelt szerepkörű rendszergazdák állandó rendszergazdai szerepkör-hozzárendeléseket hozzanak. Emellett a Kiemelt szerepkörű rendszergazdák **jogosultak** lehetnek az Azure ad rendszergazdai szerepköreire. Egy jogosult rendszergazda szükség esetén aktiválhatja a szerepkört, majd az engedélyeik lejárta után lejárnak.

## <a name="determine-your-version-of-pim"></a>A PIM verziójának meghatározása

A 2019-es verziótól kezdődően a Privileged Identity Management Azure AD-szerepkörök részét egy új verzióra frissíti a rendszer, amely megfelel az Azure-erőforrás-szerepkörökkel kapcsolatos élményeknek. Ez további funkciókat hoz létre, valamint [a meglévő API módosításait](azure-ad-roles-features.md#api-changes)is. Az új verzió bevezetését követően a cikkben ismertetett eljárások a jelenleg használt Privileged Identity Management verziójától függenek. Az ebben a szakaszban ismertetett lépéseket követve meghatározhatja, hogy a Privileged Identity Management melyik verzióját kell megadnia. A Privileged Identity Management-verziójának megismerése után kiválaszthatja a jelen cikkben szereplő, az adott verziónak megfelelő eljárásokat.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) egy olyan felhasználóval, aki tagja a [Kiemelt szerepkörű rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) szerepkörnek.
1. Nyissa meg **Azure ad Privileged Identity Management**. Ha az Áttekintés oldal tetején található egy szalagcím, kövesse a jelen cikk **új verzió** lapján található utasításokat. Ellenkező esetben kövesse az **előző verzió** lapon megjelenő utasításokat.

  [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[Új verzió](#tab/new)

## <a name="assign-a-role"></a>Szerepkör kiosztása

Kövesse az alábbi lépéseket, hogy egy felhasználó jogosult legyen egy Azure AD-rendszergazdai szerepkörre.

1. Jelentkezzen be [Azure Portalba](https://portal.azure.com/) egy olyan felhasználóval, aki tagja a [Kiemelt szerepkörű rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) szerepkörnek.

    További információ a Privileged Identity Management kezeléséhez szükséges további rendszergazdai hozzáférésről: [hozzáférés biztosítása más rendszergazdák számára a Privileged Identity Management kezeléséhez](pim-how-to-give-access-to-pim.md).

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza az **Azure ad-szerepkörök**elemet.

1. Válassza ki a **szerepkörök** elemet az Azure ad-engedélyek szerepköreinek megtekintéséhez.

    ![Azure AD-szerepkörök](./media/pim-how-to-add-role-to-user/roles-list.png)

1. Válassza a **tag hozzáadása** lehetőséget az **új hozzárendelés** lap megnyitásához.

1. Válassza a **szerepkör kiválasztása** lehetőséget a szerepkör kiválasztása lap megnyitásához.

    ![Új hozzárendelés ablaktábla](./media/pim-how-to-add-role-to-user/select-role.png)

1. Válassza ki a hozzárendelni kívánt szerepkört, majd kattintson a **kiválasztás**gombra.

1. Válasszon ki egy tagot, akihez hozzárendelni kívánja a szerepkört, majd válassza a **kiválasztás**lehetőséget.

1. A **tagsági beállítások** ablaktábla **hozzárendelés típusa** listájában válassza a **jogosult** vagy az **aktív**lehetőséget.

    - A **jogosult** hozzárendelésekhez a szerepkör tagjának kell lennie a szerepkör használatára vonatkozó művelet végrehajtásához. A műveletek tartalmazhatják a többtényezős hitelesítés (MFA) ellenőrzését, üzleti indoklást biztosítanak, vagy a kijelölt jóváhagyók jóváhagyását kérik.

    - Az **aktív** hozzárendelésekhez nem szükséges, hogy a tag bármilyen műveletet hajtson végre a szerepkör használatához. Az aktívként hozzárendelt tagok rendelkeznek a szerepkörhöz tartozó jogosultságokkal.

1. Ha a hozzárendelésnek állandónak kell lennie (tartósan jogosult vagy véglegesen kiosztott), jelölje be a **véglegesen** jelölőnégyzetet.

    A szerepkör beállításaitól függően előfordulhat, hogy a jelölőnégyzet nem jelenik meg, vagy lehet, hogy nem módosítható.

1. Egy adott hozzárendelés időtartamának megadásához törölje a jelölőnégyzet jelölését, és módosítsa a kezdő és/vagy befejező dátum és idő mezőket. Ha elkészült, válassza a **kész**gombot.

    ![Tagsági beállítások – dátum és idő](./media/pim-how-to-add-role-to-user/start-and-end-dates.png)

1. Az új szerepkör-hozzárendelés létrehozásához válassza a **Hozzáadás**lehetőséget. Megjelenik az állapot értesítése.

    ![Új hozzárendelés – értesítés](./media/pim-how-to-add-role-to-user/assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Meglévő szerepkör-hozzárendelés frissítése vagy eltávolítása

A meglévő szerepkör-hozzárendelések frissítéséhez vagy eltávolításához kövesse az alábbi lépéseket.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza az **Azure ad-szerepkörök**elemet.

1. Válassza ki a **szerepköröket** az Azure ad-szerepkörök listájának megtekintéséhez.

1. Válassza ki a frissíteni vagy eltávolítani kívánt szerepkört.

1. Keresse meg a szerepkör-hozzárendelést a **jogosult szerepkörök** vagy az **aktív szerepkörök** lapon.

    ![Szerepkör-hozzárendelés frissítése vagy eltávolítása](./media/pim-how-to-add-role-to-user/remove-update-assignments.png)

1. A szerepkör-hozzárendelés frissítéséhez vagy eltávolításához válassza a **frissítés** vagy az **Eltávolítás** lehetőséget.

# <a name="previous-version"></a>[Előző verzió](#tab/previous)

## <a name="make-a-user-eligible-for-a-role"></a>Szerepkörre jogosult felhasználó létrehozása

Kövesse az alábbi lépéseket, hogy egy felhasználó jogosult legyen egy Azure AD-rendszergazdai szerepkörre.

1. Válasszon **szerepköröket** vagy **tagokat**.

    ![Azure AD-szerepkörök](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Válassza a **tag hozzáadása** elemet a **felügyelt tagok hozzáadásának**megnyitásához.

1. Válassza a **szerepkör kiválasztása**lehetőséget, válassza ki a kezelni kívánt szerepkört, majd válassza a **kiválasztás**lehetőséget.

    ![Szerepkör kiválasztása](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Válassza a **Tagok kiválasztása**lehetőséget, válassza ki a szerepkörhöz hozzárendelni kívánt felhasználókat, majd válassza a **kiválasztás**lehetőséget.

    ![Szerepkör kiválasztása](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. A **felügyelt Tagok hozzáadása**lapon kattintson az **OK** gombra a felhasználó szerepkörhöz való hozzáadásához.

1. A szerepkörök listájában válassza ki az imént hozzárendelt szerepkört a tagok listájának megtekintéséhez.

     Ha a szerepkör hozzá van rendelve, a kiválasztott felhasználó a szerepkörhöz **jogosultként** fog megjelenni a tagok listájában.

    ![Szerepkörre jogosult felhasználó](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Most, hogy a felhasználó jogosult a szerepkörre, tájékoztassa őket arról, hogy a [Privileged Identity Management Azure ad-szerepköreinek aktiválása](pim-how-to-activate-role.md)című témakör utasításai szerint aktiválhatja azt.

    A rendszer arra kéri a jogosult rendszergazdákat, hogy regisztráljanak az Azure Multi-Factor Authentication (MFA) az aktiválás során. Ha a felhasználó nem regisztrálhat az MFA-ra, vagy egy Microsoft-fiók (például @outlook.com) használ, az összes szerepkörében állandónak kell lennie.

## <a name="make-a-role-assignment-permanent"></a>Szerepkör-hozzárendelés véglegesvé tétele

Alapértelmezés szerint az új felhasználók csak Azure AD-rendszergazdai szerepkörre *jogosultak* . Kövesse az alábbi lépéseket, ha a szerepkör-hozzárendelést véglegesen szeretné végrehajtani.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza az **Azure ad-szerepkörök**elemet.

1. Válasszon **tagokat**.

    ![Tagok listája](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Válasszon ki egy olyan **jogosult** szerepkört, amelyet véglegesen szeretne végezni.

1. Válassza a **továbbiak** lehetőséget, majd válassza a **make perm**lehetőséget.

    ![Szerepkör-hozzárendelés véglegesvé tétele](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    A szerepkör mostantól **állandóként**van listázva.

    ![Állandó módosítású tagok listája](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Felhasználó eltávolítása a szerepkörből

Eltávolíthatja a felhasználókat a szerepkör-hozzárendelésekről, de győződjön meg arról, hogy mindig van legalább egy olyan felhasználó, aki állandó globális rendszergazda. Ha nem tudja biztosan, hogy mely felhasználóknak van szüksége szerepkör-hozzárendeléseire, [elindíthatja a szerepkör hozzáférési felülvizsgálatát](pim-how-to-start-security-review.md).

A következő lépésekkel távolíthat el egy adott felhasználót egy Azure AD-rendszergazdai szerepkörből.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza az **Azure ad-szerepkörök**elemet.

1. Válasszon **tagokat**.

    ![Tagok listája](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Válassza ki az eltávolítani kívánt szerepkör-hozzárendelést.

1. Válassza a **továbbiak** , majd az **Eltávolítás**lehetőséget.

    ![Szerepkör eltávolítása](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. A megerősítést kérő üzenetben válassza az **Igen**lehetőséget.

    ![Szerepkör eltávolítása](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    A szerepkör-hozzárendelés el lett távolítva.

## <a name="authorization-error-when-assigning-roles"></a>Engedélyezési hiba a szerepkörök hozzárendeléséhez

Ha a közelmúltban engedélyezte a Privileged Identity Managementt egy előfizetéshez, és a felhasználó egy Azure AD-rendszergazdai szerepkörre való jogosultsággal próbálkozik, akkor előfordulhat, hogy az MS-PIM szolgáltatásnév még nem rendelkezik a megfelelő engedélyekkel. Az MS-PIM egyszerű szolgáltatásnak a [felhasználói hozzáférés rendszergazdai](../../role-based-access-control/built-in-roles.md#user-access-administrator) szerepkörrel kell rendelkeznie a szerepkörök másokhoz való hozzárendeléséhez. Ahelyett, hogy megvárná, amíg az MS-PIM hozzá nem rendeli a felhasználói hozzáférés rendszergazdai szerepkört, manuálisan is hozzárendelheti.

Az alábbi lépéseket követve rendelje hozzá a felhasználói hozzáférés rendszergazdai szerepkört az MS-PIM szolgáltatáshoz az előfizetéshez.

1. Jelentkezzen be a Azure Portal globális rendszergazdaként.

1. Válassza **a minden szolgáltatás** , majd az **előfizetések**lehetőséget.

1. Válassza ki az előfizetését.

1. Válassza a **Hozzáférés-vezérlés (IAM)** elemet.

1. Válassza ki a **szerepkör-hozzárendeléseket** az előfizetés hatókörében lévő szerepkör-hozzárendelések aktuális listájának megtekintéséhez.

   ![Egy előfizetés hozzáférés-vezérlés (IAM) panelje](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. Győződjön meg arról, hogy az **MS-PIM** egyszerű szolgáltatásnév hozzá van-e rendelve a **felhasználói hozzáférés rendszergazdai** szerepkörhöz.

1. Ha nem, válassza a **szerepkör-hozzárendelés hozzáadása** lehetőséget a **szerepkör-hozzárendelés hozzáadása** ablaktábla megnyitásához.

1. A **szerepkör** legördülő listában válassza ki a **felhasználói hozzáférés rendszergazdai** szerepkört.

1. A **Select (kiválasztás** ) listában keresse meg és válassza ki az **MS-PIM** egyszerű szolgáltatásnevet.

   ![Szerepkör-hozzárendelési ablaktábla hozzáadása – engedélyek hozzáadása az MS-PIM egyszerű szolgáltatásnév számára](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. A szerepkör hozzárendeléséhez válassza a **Mentés** lehetőséget.

   Néhány pillanat elteltével az MS-PIM egyszerű szolgáltatásnév a felhasználói hozzáférés rendszergazdai szerepköréhez van rendelve az előfizetés hatókörében.

   ![Hozzáférés-vezérlési lap, amely a felhasználói hozzáférés rendszergazdai szerepkörének hozzárendelését mutatja az MS-PIM egyszerű szolgáltatáshoz](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)

 ---

## <a name="next-steps"></a>Következő lépések

- [Az Azure AD rendszergazdai szerepkör beállításainak konfigurálása Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Azure-beli erőforrás-szerepkörök kiosztása Privileged Identity Management](pim-resource-roles-assign-roles.md)