---
title: Felhasználói profil adatainak hozzáadása vagy frissítése – Azure AD
description: Útmutatás arról, hogyan lehet adatokat felvenni egy felhasználó profiljába Azure Active Directoryban, beleértve a képet és a feladatot.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 04/11/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27896bac6f5436fb6717e4bc1ab0a1f9381904ad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92370916"
---
# <a name="add-or-update-a-users-profile-information-using-azure-active-directory"></a>Felhasználói profil adatainak hozzáadása vagy frissítése az Azure Active Directory használatával
Adja hozzá a felhasználói profil adatait, beleértve a profil képét, a feladatra vonatkozó információkat, valamint néhány beállítást Azure Active Directory (Azure AD) használatával. További információ az új felhasználók hozzáadásáról: [felhasználók hozzáadása vagy törlése Azure Active Directoryban](add-users-azure-active-directory.md).

## <a name="add-or-change-profile-information"></a>Profil adatainak hozzáadása vagy módosítása
Ahogy látja, a felhasználó profiljában további információk érhetők el, mint amit a felhasználó létrehozásakor hozzá tud adni. Ez a további információ nem kötelező, és szükség szerint hozzáadható a szervezete számára.

## <a name="to-add-or-change-profile-information"></a>Profil adatainak hozzáadása vagy módosítása
1. Jelentkezzen be a [Azure Portal](https://portal.azure.com/) felhasználói rendszergazdaként a szervezet számára.

2. Válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók** lehetőséget, majd válasszon ki egy felhasználót. Például _Alain Charon_.

    Megjelenik az **Alain Charon-profil** lap.

    ![A felhasználó profil lapja, beleértve a szerkeszthető adatokat is](media/active-directory-users-profile-azure-portal/user-profile-all-blade.png)

3. Válassza a **Szerkesztés** lehetőséget a rendelkezésre álló részekben foglalt információk hozzáadásához vagy frissítéséhez.

    ![A felhasználó profil lapja, amely a szerkeszthető területeket mutatja](media/active-directory-users-profile-azure-portal/user-profile-edit.png)

    - **Profil képe** Válassza ki a felhasználói fiók miniatűr képét. Ez a kép a Azure Active Directory és a felhasználó személyes oldalain, például a myapps.microsoft.com oldalon jelenik meg.

    - **Identitás.** Adjon hozzá vagy frissítsen egy további azonosító értéket a felhasználóhoz, például egy házas vezetéknevet. Ezt a nevet az utónév és a vezetéknév értékeitől függetlenül is megadhatja. Használhatja például a következőt: Monogram, cégnév vagy a megjelenített nevek sorrendjének módosítása. Egy másik példában két olyan felhasználó esetében, akiknek a neve "Chris Green", az Identity sztring használatával állíthatja be nevüket "Chris B. Green", "Chris R. Green (contoso)" néven.

    - **Feladatok adatai.** Vegyen fel minden olyan feladattal kapcsolatos információt, mint például a felhasználó beosztás, részleg vagy felettes.

    - **Beállítások.** Döntse el, hogy a felhasználó be tud-e jelentkezni Azure Active Directory bérlőbe. Megadhatja a felhasználó globális helyét is.

    - **Kapcsolattartási adatok.** Adja meg a felhasználóhoz tartozó kapcsolattartási adatokat, kivéve egyes felhasználók telefon-vagy mobil kapcsolattartási adatait (csak a globális rendszergazda frissítheti a felhasználókat a rendszergazdai szerepkörökben).

    - **Hitelesítési kapcsolattartási adatok.** Ellenőrizze ezeket az adatokat, és győződjön meg arról, hogy a felhasználó számára aktív telefonszám és e-mail-cím található. Ezeket az információkat a Azure Active Directory használja annak biztosítására, hogy a felhasználó valóban a felhasználó legyen a bejelentkezés során. A hitelesítési kapcsolattartási adatokat csak globális rendszergazda frissítheti.

4. Kattintson a **Mentés** gombra.

    A rendszer minden módosítást elment a felhasználó számára.

    >[!Note]
    >A Windows Server Active Directory segítségével frissítse az identitást, a kapcsolattartási adatokat vagy a feladatokat azon felhasználók számára, akiknek a forrása a Windows Server Active Directory. A frissítés befejezése után meg kell várnia, hogy a következő szinkronizálási ciklus befejeződjön, mielőtt látni fogja a módosításokat.

## <a name="next-steps"></a>Következő lépések
A felhasználói profilok frissítése után a következő alapvető folyamatokat végezheti el:

- [Felhasználók hozzáadása vagy törlése](add-users-azure-active-directory.md)

- [Szerepkörök hozzárendelése felhasználókhoz](active-directory-users-assign-role-azure-portal.md)

- [Alapszintű csoport létrehozása és tagok hozzáadása](active-directory-groups-create-azure-portal.md)

Más felhasználói felügyeleti feladatokat is elvégezhet, például a delegált hozzárendelését, a szabályzatok használatát és a felhasználói fiókok megosztását. További információ az egyéb elérhető műveletekről: [Azure Active Directory felhasználói kezelés dokumentációja](../enterprise-users/index.yml).
