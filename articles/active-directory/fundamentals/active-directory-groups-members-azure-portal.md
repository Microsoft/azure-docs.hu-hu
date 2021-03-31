---
title: Csoporttagok hozzáadása vagy eltávolítása – Azure Active Directory | Microsoft Docs
description: Útmutatás arról, hogyan lehet tagokat felvenni vagy eltávolítani egy csoportból a Azure Active Directory használatával.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 08/23/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: af5a85bad1e7b2a6bf645084d6b78f77e6c0d8b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92371834"
---
# <a name="add-or-remove-group-members-using-azure-active-directory"></a>Csoporttagok hozzáadása vagy eltávolítása Azure Active Directory használatával
A Azure Active Directory használatával továbbra is hozzáadhat és eltávolíthat csoporttagokat.

## <a name="to-add-group-members"></a>Csoporttagok hozzáadása

1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com).

2. Válassza a **Azure Active Directory**, majd a **csoportok** lehetőséget.

3. A **csoportok – minden csoport** lapon keresse meg és válassza ki azt a csoportot, amelyhez hozzá kívánja adni a tagot. Ebben az esetben használja a korábban létrehozott csoportot, a **Mdm Policy-West-** t.

    ![Groups-All csoportok lap, csoport neve kiemelve](media/active-directory-groups-members-azure-portal/group-all-groups-screen.png)

4. Az **MDM policy – West áttekintési** lapján válassza a **Tagok** lehetőséget a **Kezelés** területen.

    ![MDM házirend – Nyugat-Áttekintés lap, a tagok lehetőség kiemelve](media/active-directory-groups-members-azure-portal/group-overview-blade.png)

5. Válassza a **Tagok hozzáadása** lehetőséget, majd keresse meg és válassza ki a csoportba felvenni kívánt tagokat, majd válassza a **kiválasztás** lehetőséget.

    Megjelenik egy üzenet, amely szerint a tagok felvétele sikeresen megtörtént.

    ![Tagok hozzáadása lap, amelyben a keresett tag látható](media/active-directory-groups-members-azure-portal/update-members.png)

6. Frissítse a képernyőt, és tekintse meg a csoportba felvett összes tag nevét.

## <a name="to-remove-group-members"></a>Csoporttagok eltávolítása

1. A **csoportok – minden csoport** lapon keresse meg és válassza ki azt a csoportot, amelyből el szeretné távolítani a tagot. Ismét a **Mdm Policy-West-** t fogjuk használni.

2. Válassza ki a **tagok** elemet a **kezelés** területen, keresse meg és válassza ki az eltávolítandó tag nevét, majd válassza az **Eltávolítás** lehetőséget.

    ![Tag adatai lap eltávolítási lehetőséggel](media/active-directory-groups-members-azure-portal/remove-members-from-group.png)

## <a name="next-steps"></a>Következő lépések

- [Csoportok és tagok megtekintése](active-directory-groups-view-azure-portal.md)

- [A csoport beállításainak szerkesztése](active-directory-groups-settings-azure-portal.md)

- [Az erőforrásokhoz való hozzáférés kezelése csoportokkal](active-directory-manage-groups.md)

- [A csoportban lévő felhasználók dinamikus szabályainak kezelése](../enterprise-users/groups-create-rule.md)

- [Azure-előfizetés társítása vagy hozzáadása az Azure Active Directoryhoz](active-directory-how-subscriptions-associated-directory.md)
