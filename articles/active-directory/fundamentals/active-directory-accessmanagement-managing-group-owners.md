---
title: Csoport tulajdonosainak hozzáadása vagy eltávolítása – Azure Active Directory | Microsoft Docs
description: Útmutatás a tulajdonosok Azure Active Directory használatával történő hozzáadásához vagy eltávolításához.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 09/11/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b255f64547c3bae56d31415dc94a751989ca1f45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97504899"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>Csoport tulajdonosainak hozzáadása vagy eltávolítása Azure Active Directory
A Azure Active Directory-(Azure AD-) csoportokat a csoport tulajdonosai és kezelik. A csoport tulajdonosai lehetnek felhasználók vagy egyszerű szolgáltatások, és képesek a csoport kezelésére, beleértve a tagságot is. A csoport tulajdonosai csak a meglévő tulajdonosok vagy csoport-felügyeleti rendszergazdák számára rendelhetők hozzá. A csoport tulajdonosai nem szükségesek a csoport tagjainak lennie.

Ha egy csoport nem rendelkezik tulajdonossal, a csoport kezelésére szolgáló rendszergazdák továbbra is kezelhetik a csoportot. Azt javasoljuk, hogy minden csoportban legyen legalább egy tulajdonos. Ha a tulajdonosok egy csoporthoz vannak rendelve, a csoport utolsó tulajdonosa nem távolítható el. Győződjön meg arról, hogy a csoport utolsó tulajdonosának eltávolítása előtt kiválaszt egy másik tulajdonost.

## <a name="add-an-owner-to-a-group"></a>Tulajdonos hozzáadása egy csoporthoz
Az alábbi útmutatást követve adhat hozzá felhasználót tulajdonosként egy csoporthoz az Azure AD-portál használatával. Ha egy egyszerű szolgáltatást szeretne hozzáadni egy csoport tulajdonosaként, kövesse az utasításokat a [PowerShell](/powershell/module/Azuread/Add-AzureADGroupOwner)használatával.

### <a name="to-add-a-group-owner"></a>Csoport tulajdonosának hozzáadása
1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com).

2. Válassza a **Azure Active Directory** lehetőséget, válassza a **csoportok** lehetőséget, majd válassza ki azt a csoportot, amelyhez hozzá kíván adni egy tulajdonost (ebben a példában a *Mdm Policy-West*).

3. A **Mdm házirend – Nyugat áttekintése** lapon válassza a **tulajdonosok** lehetőséget.

    ![MDM házirend – Nyugat-Áttekintés lap a tulajdonosokkal lehetőség kiemelve](media/active-directory-accessmanagement-managing-group-owners/add-owners-option-overview-blade.png)

4. A **Mdm házirend-Nyugat-tulajdonosok** lapon válassza a **tulajdonosok hozzáadása** lehetőséget, majd keresse meg és válassza ki azt a felhasználót, aki az új csoport tulajdonosa lesz, majd válassza a **kiválasztás** lehetőséget.

    ![MDM házirend – Nyugat – tulajdonosok lap a tulajdonos hozzáadása lehetőség kiemelve](media/active-directory-accessmanagement-managing-group-owners/add-owners-owners-blade.png)

    Miután kiválasztotta az új tulajdonost, frissítheti a **tulajdonosok** lapot, és megtekintheti a tulajdonosok listájához hozzáadott nevet.

## <a name="remove-an-owner-from-a-group"></a>Tulajdonos eltávolítása egy csoportból
Tulajdonos eltávolítása egy csoportból az Azure AD használatával.

### <a name="to-remove-an-owner"></a>Tulajdonos eltávolítása
1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com).

2. Válassza a **Azure Active Directory** lehetőséget, válassza a **csoportok** lehetőséget, majd válassza ki azt a csoportot, amelynek el szeretné távolítani a tulajdonosát (ebben a példában a *Mdm Policy-West*).

3. A **Mdm házirend – Nyugat áttekintése** lapon válassza a **tulajdonosok** lehetőséget.

    ![MDM házirend – Nyugat-Áttekintés lap a tulajdonosok eltávolítása lehetőség kiemelve](media/active-directory-accessmanagement-managing-group-owners/remove-owners-option-overview-blade.png)

4. A **Mdm házirend-Nyugat-tulajdonosok** lapon válassza ki az eltávolítani kívánt felhasználót a csoport tulajdonosaként, válassza az **Eltávolítás** lehetőséget a felhasználó adatai lapról, és válassza az **Igen** lehetőséget a döntés megerősítéséhez.

    ![A felhasználó információs lapja Kiemelt eltávolítási lehetőséggel](media/active-directory-accessmanagement-managing-group-owners/remove-owner-info-blade.png)

    A tulajdonos eltávolítása után visszatérhet a **tulajdonosok** lapra, és megtekintheti a név eltávolítását a tulajdonosok listájából.

## <a name="next-steps"></a>Következő lépések
- [Erőforráshozzáférés-kezelés Azure Active Directory-csoportokkal](active-directory-manage-groups.md)

- [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához](../enterprise-users/groups-settings-cmdlets.md)

- [Csoportok használata egy integrált SaaS-alkalmazáshoz való hozzáféréshez](../enterprise-users/groups-saasapps.md)

- [Helyszíni identitások integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md)

- [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához](../enterprise-users/groups-settings-v2-cmdlets.md)
