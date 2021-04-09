---
title: Hozzáférés biztosítása a PIM kezeléséhez – Azure Active Directory | Microsoft Docs
description: Megtudhatja, hogyan biztosíthat hozzáférést más adminisztrációs szolgáltatásokhoz Azure AD Privileged Identity Management (PIM) kezeléséhez.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 08/06/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fba46ee5632f7411c433e4bba29201c59c552f21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94835203"
---
# <a name="delegate-access-to-privileged-identity-management"></a>Privileged Identity Managementhoz való hozzáférés delegálása

Privileged Identity Management (PIM) elérésének delegálásához a globális rendszergazda más felhasználókat is hozzárendelhet a Kiemelt szerepkörű rendszergazda szerepkörhöz. Alapértelmezés szerint a biztonsági rendszergazdák és a biztonsági olvasók csak olvasási hozzáféréssel rendelkeznek a Privileged Identity Managementhoz. A Privileged Identity Managementhoz való hozzáférés biztosítása érdekében az első felhasználó hozzárendelheti másokat a **Kiemelt szerepkörű rendszergazda** szerepkörhöz. A Kiemelt szerepkörű rendszergazda szerepkör csak az Azure AD-szerepkörök kezeléséhez szükséges. A Kiemelt szerepkörű rendszergazdai jogosultságok nem szükségesek az Azure-erőforrások beállításainak kezeléséhez.

> [!NOTE]
> A Privileged Identity Management kezeléséhez Azure AD-Multi-Factor Authentication szükséges. Mivel a Microsoft-fiókok nem regisztrálhatnak az Azure AD-Multi-Factor Authenticationra, a Microsoft-fiókba bejelentkező felhasználók nem férhetnek hozzá a Privileged Identity Managementhoz.

Győződjön meg arról, hogy mindig van legalább két felhasználó egy kiemelt szerepkörű rendszergazda szerepkörben, ha az egyik felhasználó ki van zárva, vagy a fiókja törölve lett.

## <a name="delegate-access-to-manage-pim"></a>Hozzáférés delegálása a PIM kezeléséhez

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Az Azure AD-ben nyissa meg **Privileged Identity Management**.

1. Válassza az **Azure ad-szerepkörök** elemet.

1. Válassza a **szerepkörök** lehetőséget.

    ![Azure AD-szerepkörök Privileged Identity Management – szerepkörök](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. Válassza ki a **Kiemelt szerepkörű rendszergazda** szerepkört a tagok lap megnyitásához.

    ![Kiemelt szerepkörű rendszergazda – tagok](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. Válassza a **tag hozzáadása**  elemet a **felügyelt Tagok hozzáadása** ablaktábla megnyitásához.

1. Válassza a **Tagok kiválasztása** lehetőséget a **Tagok kiválasztása** panel megnyitásához.

    ![Kiemelt szerepkörű rendszergazda – tagok kiválasztása](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. Válasszon ki egy tagot, majd kattintson a **kiválasztás** elemre.

1. Kattintson az **OK** gombra, hogy a tag jogosult legyen a **Kiemelt szerepkörű rendszergazda** szerepkörre.

    Ha Privileged Identity Management-ben valakihöz rendel hozzá egy új szerepkört, a rendszer automatikusan **jogosultként** konfigurálja őket a szerepkör aktiválásához.

1. Ahhoz, hogy a tag állandó legyen, válassza ki a felhasználót a Kiemelt szerepkörű rendszergazda tagok listájában.

1. Válassza a **továbbiak** , majd a **végleges** lehetőséget, hogy a hozzárendelés állandó legyen.

    ![Kiemelt szerepkörű rendszergazda – állandó](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. Küldje el a felhasználónak egy hivatkozást az [Privileged Identity Management használatának megkezdéséhez](pim-getting-started.md).

## <a name="remove-access-to-manage-pim"></a>A PIM kezelése hozzáférésének eltávolítása

Mielőtt eltávolít valakit a Kiemelt szerepkörű rendszergazda szerepkörből, mindig győződjön meg arról, hogy még legalább két felhasználó van hozzárendelve.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza az **Azure ad-szerepkörök** elemet.

1. Válassza a **szerepkörök** lehetőséget.

1. Válassza ki a **Kiemelt szerepkörű rendszergazda** szerepkört a tagok lap megnyitásához.

1. Jelölje be az eltávolítani kívánt felhasználó melletti jelölőnégyzetet, majd válassza a **tag eltávolítása** lehetőséget.

    ![Kiemelt szerepkörű rendszergazda – tag eltávolítása](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. Ha a rendszer arra kéri, hogy erősítse meg, hogy el kívánja távolítani a tagot a szerepkörből, válassza az **Igen** lehetőséget.

## <a name="next-steps"></a>Következő lépések

- [A Privileged Identity Management használatának első lépései](pim-getting-started.md)
