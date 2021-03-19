---
title: Azure-beli erőforrás-szerepkörök kiosztása Privileged Identity Management-Azure Active Directoryban | Microsoft Docs
description: Ismerje meg, hogyan rendelhet hozzá Azure-beli erőforrás-szerepköröket Azure AD Privileged Identity Management (PIM) szolgáltatáshoz.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 011b50449f0a02b128bc000535f93e1809fd9777
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92673876"
---
# <a name="assign-azure-resource-roles-in-privileged-identity-management"></a>Azure-beli erőforrás-szerepkörök kiosztása Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) felügyelheti a beépített Azure-erőforrás-szerepköröket, valamint az egyéni szerepköröket, beleértve a (de nem kizárólag):

- Tulajdonos
- Felhasználói hozzáférés rendszergazdája
- Közreműködő
- Biztonsági rendszergazda
- Security Manager

> [!NOTE]
> A tulajdonos vagy a felhasználói hozzáférés rendszergazdai előfizetési szerepköreihez rendelt felhasználók vagy csoportok tagjai, valamint az Azure ad-előfizetések felügyeletét engedélyező globális rendszergazdák alapértelmezés szerint erőforrás-rendszergazdai jogosultságokkal rendelkeznek. Ezek a rendszergazdák szerepköröket oszthatnak ki, konfigurálják a szerepkör beállításait, és áttekinthetik a hozzáférést az Azure-erőforrások Privileged Identity Management használatával. A felhasználók nem kezelhetnek erőforrás-rendszergazdai jogosultságokkal nem rendelkező erőforrások Privileged Identity Management. Tekintse meg az [Azure beépített szerepköreinek](../../role-based-access-control/built-in-roles.md)listáját.

## <a name="assign-a-role"></a>Szerepkör kiosztása

Kövesse az alábbi lépéseket, hogy a felhasználók jogosultak legyenek az Azure-erőforrások szerepkörre.

1. Jelentkezzen be [Azure Portal](https://portal.azure.com/) tulajdonos vagy felhasználói hozzáférés rendszergazdai szerepkör engedélyeivel.

    További információ a Privileged Identity Management kezeléséhez szükséges további rendszergazdai hozzáférésről: [hozzáférés biztosítása más rendszergazdák számára a Privileged Identity Management kezeléséhez](pim-how-to-give-access-to-pim.md).

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza ki az **Azure-erőforrásokat**.

1. Az erőforrás-szűrő segítségével keresse meg a keresett felügyelt erőforrásokat.

    ![A felügyelni kívánt Azure-erőforrások listája](./media/pim-resource-roles-assign-roles/resources-list.png)

1. Válassza ki a kezelni kívánt erőforrást az erőforrás-Áttekintés lap megnyitásához.

1. A **kezelés** területen válassza ki a **szerepkörök** elemet az Azure-erőforrások szerepköreinek megtekintéséhez.

    ![Azure-erőforrások szerepkörei](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. Válassza a **hozzárendelések hozzáadása** lehetőséget a **hozzárendelések hozzáadása** panel megnyitásához.

1. Válassza a **szerepkör kiválasztása** lehetőséget a **szerepkör kiválasztása** lap megnyitásához.

    ![Új hozzárendelés ablaktábla](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. Válassza ki a hozzárendelni kívánt szerepkört, majd kattintson a **kiválasztás** gombra.

    Megnyílik a **tag vagy csoport kiválasztása** panel.

1. Válassza ki a szerepkörhöz hozzárendelni kívánt tagot vagy csoportot, majd kattintson a **kiválasztás** elemre.

    ![Tag vagy csoport ablaktábla kiválasztása](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

1. A **Beállítások** lap **hozzárendelés típusa** listájában válassza a **jogosult** vagy az **aktív** lehetőséget.

    ![Tagságok beállításai panel](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    Az Azure-erőforrások Privileged Identity Management két különböző hozzárendelési típust biztosít:

    - A **jogosult** hozzárendelésekhez a szerepkör tagjának kell lennie a szerepkör használatára vonatkozó művelet végrehajtásához. A műveletek tartalmazhatják a többtényezős hitelesítés (MFA) ellenőrzését, üzleti indoklást biztosítanak, vagy a kijelölt jóváhagyók jóváhagyását kérik.

    - Az **aktív** hozzárendelésekhez nem szükséges, hogy a tag bármilyen műveletet hajtson végre a szerepkör használatához. Az aktívként hozzárendelt tagok rendelkeznek a szerepkörhöz tartozó jogosultságokkal.

1. Egy adott hozzárendelés időtartamának megadásához módosítsa a kezdő és a záró dátumokat és időpontokat.

1. Ha elkészült, válassza a **hozzárendelés** lehetőséget.

1. Az új szerepkör-hozzárendelés létrehozása után megjelenik egy állapotjelző üzenet.

    ![Új hozzárendelés – értesítés](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Meglévő szerepkör-hozzárendelés frissítése vagy eltávolítása

A meglévő szerepkör-hozzárendelések frissítéséhez vagy eltávolításához kövesse az alábbi lépéseket.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza ki az **Azure-erőforrásokat**.

1. Válassza ki a kezelni kívánt erőforrást az Áttekintés oldal megnyitásához.

1. A **kezelés** területen válassza ki a **szerepkörök** elemet az Azure-erőforrások szerepköreinek megtekintéséhez.

    ![Azure-erőforrás szerepkörei – szerepkör kiválasztása](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. Válassza ki a frissíteni vagy eltávolítani kívánt szerepkört.

1. Keresse meg a szerepkör-hozzárendelést a **jogosult szerepkörök** vagy az **aktív szerepkörök** lapon.

    ![Szerepkör-hozzárendelés frissítése vagy eltávolítása](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. A szerepkör-hozzárendelés frissítéséhez vagy eltávolításához válassza a **frissítés** vagy az **Eltávolítás** lehetőséget.

    További információ a szerepkör-hozzárendelés kibővítéséről: [Azure-beli erőforrás-szerepkörök kiterjesztése vagy megújítása Privileged Identity Managementban](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>Következő lépések

- [Azure-beli erőforrás-szerepkörök kiterjesztése vagy megújítása Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Az Azure erőforrás-szerepkör beállításainak konfigurálása Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Azure AD-szerepkörök kiosztása Privileged Identity Management](pim-how-to-add-role-to-user.md)
