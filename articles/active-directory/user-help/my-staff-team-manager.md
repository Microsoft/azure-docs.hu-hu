---
title: Jelszavak és telefonszámok kezelése a saját munkatársakkal – Azure AD | Microsoft Docs
description: A felhasználók jelszavainak és telefonszámának kezelése a munkatársakkal
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.topic: end-user-help
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 03/17/2021
ms.author: curtand
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 7b435c56f53001275fbdb1a3b3cfae25536a9a63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104583598"
---
# <a name="delegate-user-management-with-my-staff"></a>A felhasználók felügyeletének delegálása a saját munkatársakkal

A szervezet a **saját munkatársaival** delegálhatja a felhasználói felügyeleti feladatokat a hatóságok adataihoz, például egy áruház kezelőjéhez vagy a csoportvezető számára, hogy a személyzet tagjai hozzáférhessenek a szükséges alkalmazásokhoz. Ha a csapattag nem fér hozzá egy alkalmazáshoz, mert elfelejtik a jelszót, a termelékenység elvész. Ez a támogatási költségeket is felgyorsítja, és a felügyeleti folyamatokban szűk keresztmetszetet okoz.  A munkatársakkal egy csapattag, aki nem tud hozzáférni a fiókjához, mindössze néhány kattintással visszanyerheti a hozzáférést, és rendszergazdai segítségre sincs szükség.

## <a name="manage-your-staff-in-my-staff"></a>Munkatársak kezelése

A csapat tagjainak kezelése egyszerű. A kezdéshez [nyissa meg a saját munkatársakat](https://aka.ms/mystaff), válasszon egy csapatot vagy helyet, majd válasszon ki egy felhasználót. A helyeknek és a csoport tagjainak helyét a rendszergazda határozza meg, és ezeket nem lehet módosítani.

Ha egynél több helyet felügyel, a munkatársaihoz való ugráskor ki kell választania egy helyet, amely a helyhez rendelt csapattagot tartalmazza.

Ha még nem rendelkezik megfelelő engedélyekkel a munkatársak eléréséhez, a következő üzenet jelenik meg: "Hoppá, úgy tűnik, hogy jelenleg nem jogosult a munkatársak megtekintésére. További információért forduljon a rendszergazdához. "

### <a name="find-a-staff-member-in-my-staff"></a>Munkatársak megkeresése

A kezelés megkezdése előtt meg kell nyitnia a személyzet tagjainak profilját.

1. [Nyissa meg a saját munkatársakat](https://aka.ms/mystaff) , és szükség esetén válasszon egy helyet.

    ![Válasszon egy helyet a csapat tagjai számára a saját munkatársaiban](media/my-staff-team-manager/allaus.png)

1. Nyisson meg egy csapattag profilját.

    ![Válasszon egy felhasználót a saját munkahelyén](media/my-staff-team-manager/aupage.png)

## <a name="reset-a-user-password"></a>Új felhasználói jelszó beállítása

Ha a szervezete engedélyt adott Önnek, alaphelyzetbe állíthatja a munkatársak jelszavait.

1. [Nyissa meg a saját munkatársakat](https://aka.ms/mystaff).
1. Nyisson meg egy munkatárs profilját.
1. Válassza a **jelszó alaphelyzetbe állítása** lehetőséget.

    ![Felhasználói jelszó alaphelyzetbe állítása a munkatársakban](media/my-staff-team-manager/resetpassword1.png)

1. Az új jelszó előállítása vagy megadása. Előfordulhat, hogy megjelenik egy automatikusan létrehozott ideiglenes jelszó, vagy megkérheti, hogy adjon meg egy ideiglenes jelszót a felhasználó számára.

    ![Az ideiglenes felhasználói jelszó másolása a munkatársaink alaphelyzetbe állítása után](media/my-staff-team-manager/resetpassword2.png)

A felhasználó jelszavának alaphelyzetbe állítása után adja meg a felhasználó számára az ideiglenes jelszót. Ha a felhasználó ideiglenes jelszavával jelentkezik be, akkor módosítania kell azt.

## <a name="manage-a-users-phone-number"></a>Felhasználó telefonszámának kezelése

Ha a szervezete engedélyt adott Önnek, a munkatársainak telefonszámait is kezelheti.

### <a name="add-a-phone-number"></a>Telefonszám hozzáadása

1. [Nyissa meg a saját munkatársakat](https://aka.ms/mystaff).
1. Nyisson meg egy munkatárs profilját.
1. Válassza a **telefonszám hozzáadása** lehetőséget.

    ![Felhasználói telefonszám hozzáadása a munkatársaim számára](media/my-staff-team-manager/addphone1.png)

1. Adja meg a telefonszámot, majd válassza a **Mentés** lehetőséget.

    ![A felvett felhasználó telefonszámának mentése a saját munkatársaiban](media/my-staff-team-manager/addphone2.png)

Miután regisztrált egy felhasználó telefonszámát, használhatja azt az SMS-be való bejelentkezéshez, a kétlépéses ellenőrzés végrehajtásához vagy a saját jelszavának alaphelyzetbe állításához a szervezet beállításaitól függően.

![A munkatársaim által regisztrált új telefonszám](media/my-staff-team-manager/addphone3.png)

### <a name="edit-a-phone-number"></a>Telefonszám szerkesztése

1. [Nyissa meg a saját munkatársakat](https://aka.ms/mystaff).
1. Nyisson meg egy munkatárs profilját.
1. Válassza a **telefonszám szerkesztése** lehetőséget.

    ![A saját munkatársak felhasználói profiljában válassza a szerkesztés lehetőséget](media/my-staff-team-manager/editphone2.png)

1. Adja meg az új telefonszámot, és kattintson a **Mentés** gombra.

    ![Munkatárs telefonszámának szerkesztése a munkatársaim számára](media/my-staff-team-manager/editphone1.png)

### <a name="enable-phone-number-sign-in-for-a-user"></a>Telefonszám bejelentkezésének engedélyezése a felhasználó számára

Ha a bejelentkezéskor a felhasználónévvel (SMS-alapú bejelentkezéssel) rendelkező telefonszámot engedélyez, ezt a hitelesítést egy meglévő felhasználói telefonszámhoz is hozzáadhatja.

1. [Nyissa meg a saját munkatársakat](https://aka.ms/mystaff).
1. Nyisson meg egy munkatárs profilját.
1. Ha a képernyő alján egy üzenet jelenik meg, amely azt jelzi, hogy a felhasználó telefonszámát használva bejelentkezik a felhasználónévként, válassza az **Engedélyezés** lehetőséget a folyamat megkezdéséhez. Ez az üzenet akkor jelenik meg, ha a felhasználó számára engedélyezve van a telefonszámmal való bejelentkezés.

    ![Tekintse meg az üzenetet, amikor a telefonos bejelentkezés támogatott a saját munkatársak egyik helyén.](media/my-staff-team-manager/enableforms1.png)

1. Ha elkészült, kattintson **az OK gombra** .

    ![Képernyőfelvétel: "a telefonszám engedélyezése a bejelentkezéshez?" ablak az "OK" gomb kiválasztásával.](media/my-staff-team-manager/enableforms2.png)

### <a name="remove-a-phone-number"></a>Telefonszám eltávolítása

1. [Nyissa meg a saját munkatársakat](https://aka.ms/mystaff).
1. Nyisson meg egy munkatárs profilját.
1. Válassza a **telefonszám eltávolítása** lehetőséget.
1. Ha elkészült, válassza a **Törlés** lehetőséget.

    ![Munkatárs telefonszámának eltávolítása a munkatársaim számára](media/my-staff-team-manager/deletephone1.png)
