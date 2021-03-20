---
title: A csoport adatainak szerkesztése – Azure Active Directory | Microsoft Docs
description: Útmutatás a csoport adatainak Azure Active Directory használatával történő szerkesztéséhez.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 08/27/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: c346877d29c5fef03b45c895cbf8d462f90d0979
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92369131"
---
# <a name="edit-your-group-information-using-azure-active-directory"></a>A csoport adatainak szerkesztése Azure Active Directory használatával

A Azure Active Directory (Azure AD) használatával szerkesztheti a csoportok beállításait, beleértve a név, a leírás vagy a tagság típusának frissítését is.

## <a name="to-edit-your-group-settings"></a>A csoport beállításainak szerkesztése
1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com).

2. Válassza a **Azure Active Directory**, majd a **csoportok** lehetőséget.

    Megjelenik a **csoportok – minden csoport** lap, amely az összes aktív csoportot megjeleníti.

3. A **csoportok – minden csoport** lapon írja be a csoportnév nagy részét a **keresőmezőbe** . Ebben a cikkben a **Mdm Policy-West** csoportot keressük.

    A keresési eredmények a **keresőmező** alatt jelennek meg, így a további karakterek beírásakor frissülnek.

    ![Minden csoport lap, a keresési szöveg szövegmezőben](media/active-directory-groups-settings-azure-portal/search-for-specific-group.png)

4. Válassza ki a **Mdm házirend-Nyugat** lehetőséget, majd válassza a **Tulajdonságok** lehetőséget a **kezelés** területen.

    ![Csoport áttekintése lap, a tag lehetőséggel és a Kiemelt információkkal](media/active-directory-groups-settings-azure-portal/group-overview-blade.png)

5. Szükség szerint frissítse az **általános beállítások** információit, beleértve a következőket:

    ![Csoport tulajdonságainak beállításai](media/active-directory-groups-settings-azure-portal/group-properties-settings.png)

    - **Csoport neve.** Szerkessze a létező csoportnév-nevet.
    
    - **Csoport leírása** A meglévő csoport leírásának szerkesztése.

    - **Csoport típusa** A csoport típusa nem módosítható a létrehozása után. A **csoport típusának** módosításához törölnie kell a csoportot, és létre kell hoznia egy újat.
    
    - **Tagság típusa** Módosítsa a tagság típusát. További információ a különböző rendelkezésre álló tagsági típusokról [: útmutató: alapszintű csoport létrehozása és Tagok hozzáadása a Azure Active Directory portál használatával](active-directory-groups-create-azure-portal.md).
    
    - **Objektumazonosító.** Az objektumazonosító nem módosítható, de a csoport PowerShell-parancsaiban használható. További információ a PowerShell-parancsmagok használatáról: [Azure Active Directory parancsmagok a csoport beállításainak konfigurálásához](../enterprise-users/groups-settings-v2-cmdlets.md).

## <a name="next-steps"></a>Következő lépések
E cikkekben további információk találhatók az Azure Active Directoryval kapcsolatban.

- [Csoportok és tagok megtekintése](active-directory-groups-view-azure-portal.md)

- [Alapszintű csoport létrehozása és tagok hozzáadása](active-directory-groups-create-azure-portal.md)

- [Tagok hozzáadása vagy eltávolítása egy csoportból](active-directory-groups-members-azure-portal.md)

- [A csoportban lévő felhasználók dinamikus szabályainak kezelése](../enterprise-users/groups-create-rule.md)

- [Csoporttagságok kezelése](active-directory-groups-membership-azure-portal.md)

- [Az erőforrásokhoz való hozzáférés kezelése csoportokkal](active-directory-manage-groups.md)

- [Azure-előfizetés társítása vagy hozzáadása az Azure Active Directoryhoz](active-directory-how-subscriptions-associated-directory.md)
