---
title: Azure AD-szerepkörök kiosztása a felhasználók számára – Azure Active Directory
description: Megtudhatja, hogyan biztosíthat hozzáférést az Azure Active Directory lévő felhasználóknak Azure AD-szerepkörök hozzárendelésével.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 03/07/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36ced586db1b4e417e623431c137c43dac8ba56f
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/15/2021
ms.locfileid: "103466644"
---
# <a name="assign-azure-ad-roles-to-users"></a>Azure AD-szerepkörök kiosztása a felhasználók számára

Most már megtekintheti és kezelheti a rendszergazdai szerepkörök összes tagját az Azure AD felügyeleti központban. Ha gyakran felügyeli a szerepkör-hozzárendeléseket, valószínűleg ezt a folyamatot fogja előnyben részesíteni. Ez a cikk azt ismerteti, hogyan rendelhet hozzá Azure AD-szerepköröket az Azure AD felügyeleti központon keresztül.

## <a name="assign-a-role"></a>Szerepkör kiosztása

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://aad.portal.azure.com) globális rendszergazdai vagy Kiemelt szerepkörű rendszergazdai jogosultságokkal.

1. Válassza a **Azure Active Directory** lehetőséget.

1. Válassza a **szerepkörök és rendszergazdák** lehetőséget az összes elérhető szerepkör listájának megtekintéséhez.

    ![Képernyőkép a szerepkörökről és a rendszergazdák lapról](./media/manage-roles-portal/roles-and-administrators.png)

1. Válasszon ki egy szerepkört a hozzárendelések megtekintéséhez.

    Ha segítségre van szüksége a szükséges szerepkör megtalálásához, az Azure AD a szerepkör-kategóriák alapján jeleníti meg a szerepkörök részhalmazait. Tekintse meg a **típus** szűrőt, hogy csak a kiválasztott típusú szerepkörök jelenjenek meg.

1. Válassza a **hozzárendelések hozzáadása** lehetőséget, majd válassza ki a szerepkörhöz hozzárendelni kívánt felhasználókat.

    Ha az alábbi ábrán nem látható, olvassa el a [Privileged Identity Management (PIM)](#privileged-identity-management-pim) megjegyzését annak ellenőrzéséhez, hogy a PIM-t használja-e.

    ![rendszergazdai szerepkör engedélyeinek listája](./media/manage-roles-portal/add-assignments.png)

1. A szerepkör hozzárendeléséhez válassza a **Hozzáadás** lehetőséget.

## <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

Az [Azure ad Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md)használatával további felügyeleti képességeket adhat meg a **PIM** szolgáltatásban. A Kiemelt szerepkörű rendszergazdák az "állandó" (a szerepkörben mindig aktív) hozzárendeléseket "jogosult" értékre módosíthatják (csak akkor, ha emelt szintű). Ha nem rendelkezik Privileged Identity Managementval, akkor továbbra is kiválaszthatja a **felügyelet a PIM-ben** lehetőséget a próbaverzióra való feliratkozáshoz. A Privileged Identity Management [prémium szintű Azure ad P2-licencet](../privileged-identity-management/subscription-requirements.md)igényel.

![Képernyőfelvétel: a "felhasználói rendszergazda – hozzárendelések" lap, amelyen a "kezelés a PIM-ban" művelet be van jelölve](./media/manage-roles-portal/member-list-pim.png)

Ha Ön globális rendszergazda vagy Kiemelt szerepkörű rendszergazda, egyszerűen hozzáadhat vagy eltávolíthat tagokat, szűrheti a listát, vagy kijelölhet egy tagot az aktív hozzárendelt szerepkörök megjelenítéséhez.

> [!Note]
> Ha rendelkezik egy prémium szintű Azure AD-licenccel, és már használja a Privileged Identity Management-t, az összes szerepkör-felügyeleti feladat elvégzése a Privilege Identity Management szolgáltatásban történik, és nem az Azure AD-ben.
>
> ![A PIM-ben felügyelt Azure AD-szerepkörök olyan felhasználók számára, akik már használják a PIM-t, és prémium P2 licenccel rendelkeznek](./media/manage-roles-portal/pim-manages-roles-for-p2.png)

## <a name="next-steps"></a>Következő lépések

* Nyugodtan ossza meg velünk az [Azure ad rendszergazdai szerepkörökkel foglalkozó fórumát](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* További információ a szerepkörökről: az [Azure ad beépített szerepkörei](permissions-reference.md).
* Az alapértelmezett felhasználói engedélyek összehasonlítását lásd: a [vendég és a tag alapértelmezett felhasználói engedélyeinek összehasonlítása](../fundamentals/users-default-permissions.md).
