---
title: Azure-beli szerepkör-hozzárendelések hozzáadása vagy eltávolítása a Azure Portal-Azure RBAC használatával
description: Ismerje meg, hogyan biztosíthat hozzáférést az Azure-erőforrásokhoz felhasználók, csoportok, egyszerű szolgáltatások vagy felügyelt identitások számára a Azure Portal és az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/11/2021
ms.author: rolyon
ms.openlocfilehash: f1753e7bc50fa9ff2c5512696a37dae7578f23b4
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2021
ms.locfileid: "98117444"
---
# <a name="add-or-remove-azure-role-assignments-using-the-azure-portal"></a>Azure-beli szerepkör-hozzárendelés hozzáadása vagy eltávolítása az Azure Portal használatával

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Ez a cikk azt ismerteti, hogyan rendelhet hozzá szerepköröket a Azure Portal használatával.

Ha Azure Active Directory rendszergazdai szerepköröket kell társítania, tekintse meg [a rendszergazdai szerepkörök megtekintése és társítása a Azure Active Directory-ben](../active-directory/roles/manage-roles-portal.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="add-a-role-assignment"></a>Szerepkör-hozzárendelés hozzáadása

Az Azure-RBAC az Azure-erőforrásokhoz való hozzáférés biztosításához hozzá kell adnia egy szerepkör-hozzárendelést. A szerepkörök hozzárendeléséhez kövesse az alábbi lépéseket. A lépések magas szintű áttekintését a [szerepkör-hozzárendelés hozzáadásának lépéseiben](role-assignments-steps.md)tekintheti meg.

### <a name="step-1-identify-the-needed-scope"></a>1. lépés: a szükséges hatókör azonosítása

[!INCLUDE [Scope for Azure RBAC introduction](../../includes/role-based-access-control/scope-intro.md)]

[!INCLUDE [Scope for Azure RBAC least privilege](../../includes/role-based-access-control/scope-least.md)] A hatókörrel kapcsolatos további információkért lásd a [hatókör ismertetése](scope-overview.md)című témakört.

![Az Azure RBAC hatóköri szintjei](../../includes/role-based-access-control/media/scope-levels.png)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A felső keresőmezőbe keresse meg azt a hatókört, amelyhez hozzáférést szeretne biztosítani. Kereshet például **felügyeleti csoportok**, **előfizetések**, **erőforráscsoportok** vagy egy adott erőforrás között.

    ![Erőforráscsoport keresése Azure Portal](./media/shared/rg-portal-search.png)

1. Kattintson az adott hatókörhöz tartozó erőforrásra.

    A következő példa egy erőforráscsoportot mutat be.

    ![Erőforráscsoport – áttekintés](./media/shared/rg-overview.png)

### <a name="step-2-open-the-add-role-assignment-pane"></a>2. lépés: a szerepkör-hozzárendelés hozzáadása ablaktábla megnyitása

A **hozzáférés-vezérlés (iam)** az a lap, amelyet általában a szerepkörök hozzárendelésére használ az Azure-erőforrásokhoz való hozzáférés biztosításához. Ez az identitás-és hozzáférés-kezelés (IAM) néven is ismert, és a Azure Portal több helyén is megjelenik.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

    Az alábbi példa az erőforráscsoport hozzáférés-vezérlés (IAM) lapját mutatja be.

    ![Egy erőforráscsoport hozzáférés-vezérlés (IAM) lapja](./media/shared/rg-access-control.png)

1. Kattintson a **szerepkör-hozzárendelések** lapra a szerepkör-hozzárendelések ezen a hatókörön való megtekintéséhez.

1. Kattintson a **Hozzáadás**  >  **szerepkör-hozzárendelés hozzáadása** lehetőségre.
   Ha nem rendelkezik jogosultsággal a szerepkörök hozzárendeléséhez, a szerepkör-hozzárendelés hozzáadása lehetőség le lesz tiltva.

   ![Szerepkör-hozzárendelési menü hozzáadása](./media/shared/add-role-assignment-menu.png)

    Megnyílik a Szerepkör-hozzárendelés hozzáadása panel.

   ![Szerepkör-hozzárendelési ablaktábla hozzáadása](./media/shared/add-role-assignment.png)

### <a name="step-3-select-the-appropriate-role"></a>3. lépés: válassza ki a megfelelő szerepkört

1. A **szerepkör** listában keresse meg vagy görgessen a hozzárendelni kívánt szerepkört.

    A megfelelő szerepkör meghatározásához vigye a kurzort az információ ikonra a szerepkör leírásának megjelenítéséhez. További információkért tekintse meg az [Azure beépített szerepköreit](built-in-roles.md) ismertető cikket.

   ![Szerepkör kiválasztása a szerepkör-hozzárendelés hozzáadása](./media/role-assignments-portal/add-role-assignment-role.png)

1. Kattintson ide a szerepkör kiválasztásához.

### <a name="step-4-select-who-needs-access"></a>4. lépés: válassza ki, hogy kinek van szüksége hozzáférésre

1. A **hozzáférés kiosztása** listában válassza ki a rendszerbiztonsági tag típusát, amelyhez hozzáférést szeretne rendelni.

    | Típus | Leírás |
    | --- | --- |
    | **Felhasználó, csoport vagy egyszerű szolgáltatásnév** | Ha a szerepkört egy felhasználóhoz, csoporthoz vagy egyszerű szolgáltatásnév (alkalmazás) számára szeretné hozzárendelni, válassza ki ezt a típust. |
    | **Felhasználóhoz rendelt felügyelt identitás** | Ha a szerepkört [felhasználó által hozzárendelt felügyelt identitáshoz](../active-directory/managed-identities-azure-resources/overview.md)szeretné rendelni, válassza ki ezt a típust. |
    | *Rendszerhez rendelt felügyelt identitás* | Ha a szerepkört egy [rendszerhez rendelt felügyelt identitáshoz](../active-directory/managed-identities-azure-resources/overview.md)szeretné rendelni, válassza ki azt az Azure-szolgáltatási példányt, amelyben a felügyelt identitás található. |

   ![Válasszon rendszerbiztonsági tag-típust a szerepkör-hozzárendelés hozzáadása területen](./media/role-assignments-portal/add-role-assignment-type.png)

1. Ha a felhasználó által hozzárendelt felügyelt identitást vagy a rendszer által hozzárendelt felügyelt identitást választotta, válassza ki azt az **előfizetést** , amelyben a felügyelt identitás található.

1. A **Select (kiválasztás** ) szakaszban keresse meg a rendszerbiztonsági tag karakterlánc beírásával vagy a lista görgetésével.

   ![Válasszon felhasználót a szerepkör-hozzárendelés hozzáadása területen](./media/role-assignments-portal/add-role-assignment-user.png)

1. Ha megtalálta a rendszerbiztonsági tag, kattintson rá a kijelöléshez.

### <a name="step-5-assign-role"></a>5. lépés: szerepkör kiosztása

1. A szerepkör hozzárendeléséhez kattintson a **Mentés** gombra.

   Néhány pillanat elteltével a rendszerbiztonsági tag a kiválasztott hatókörhöz rendeli a szerepkört.

1. A **szerepkör-hozzárendelések** lapon ellenőrizze, hogy megjelenik-e a szerepkör-hozzárendelés a listában.

    ![Szerepkör-hozzárendelés mentett hozzáadása](./media/role-assignments-portal/rg-role-assignments.png)

## <a name="remove-a-role-assignment"></a>Szerepkör-hozzárendelés eltávolítása

Az Azure-RBAC az Azure-erőforrásokhoz való hozzáférés eltávolításához távolítsa el a szerepkör-hozzárendelést. A szerepkör-hozzárendelés eltávolításához kövesse az alábbi lépéseket.

1. Nyissa meg a **hozzáférés-vezérlést (iam)** egy hatókörön, például a felügyeleti csoport, az előfizetés, az erőforráscsoport vagy az erőforrás területen, ahol el szeretné távolítani a hozzáférést.

1. A **szerepkör-hozzárendelések** lapra kattintva megtekintheti az összes szerepkör-hozzárendelést ezen a hatókörön.

1. A szerepkör-hozzárendelések listájában jelölje be az eltávolítani kívánt szerepkör-hozzárendeléssel rendelkező rendszerbiztonsági tag melletti jelölőnégyzetet.

   ![A szerepkör-hozzárendelés eltávolításra van kijelölve](./media/role-assignments-portal/rg-role-assignments-select.png)

1. Kattintson az **Eltávolítás** elemre.

   ![Szerepkör-hozzárendelés eltávolítási üzenete](./media/role-assignments-portal/remove-role-assignment.png)

1. A megjelenő szerepkör-hozzárendelés eltávolítása üzenetben kattintson az **Igen** gombra.

    Ha olyan üzenet jelenik meg, amely szerint az örökölt szerepkör-hozzárendelések nem távolíthatók el, a rendszer megpróbálja eltávolítani a szerepkör-hozzárendelést egy alárendelt hatókörben. Nyissa meg a hozzáférés-vezérlés (IAM) részt azon a hatókörön, ahol a szerepkör hozzá lett rendelve, és próbálkozzon újra. A hozzáférés-vezérlés (IAM) megfelelő hatókörben való megnyitásának gyorsan megtekinthető a **hatókör** oszlop, és a mellette lévő hivatkozásra kattintva **(örökölt)**.

   ![Szerepkör-hozzárendelési üzenet eltávolítása az örökölt szerepkör-hozzárendelésekhez](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Következő lépések

- [Azure-előfizetés rendszergazdai szerepkörének felhasználóhoz rendelése](role-assignments-portal-subscription-admin.md)
- [Szerepkör-hozzárendelés hozzáadása felügyelt identitáshoz](role-assignments-portal-managed-identity.md)
- [Az Azure RBAC hibáinak megoldása](troubleshooting.md)
