---
title: Regisztrációs és bejelentkezési folyamat beállítása
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan állíthatja be a regisztrációt és a bejelentkezési folyamatot a Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: fbcdef0002a227d5319fc01e625146480e4c99fc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105043750"
---
# <a name="set-up-a-sign-up-and-sign-in-flow-in-azure-active-directory-b2c"></a>Regisztrációs és bejelentkezési folyamat beállítása Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="sign-up-and-sign-in-flow"></a>Regisztrációs és bejelentkezési folyamat

A regisztrálási és bejelentkezési szabályzat lehetővé teszi a felhasználók számára a következőket: 

* Regisztráció helyi fiókkal
* Bejelentkezés helyi fiókkal
* Közösségi fiókkal való regisztráció vagy bejelentkezés
* Új jelszó létrehozása

![Profil szerkesztési folyamata](./media/add-sign-up-and-sign-in-policy/add-sign-up-and-sign-in-flow.png)

## <a name="prerequisites"></a>Előfeltételek

Ha még nem tette meg, [regisztráljon egy webalkalmazást Azure Active Directory B2C](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Regisztrációs és bejelentkezési felhasználói folyamat létrehozása

A regisztrációs és bejelentkezési felhasználói folyamat egyetlen konfigurációval kezeli a regisztrálási és a bejelentkezési élményt. Az alkalmazás felhasználói a környezettől függően a megfelelő útvonalat vezetik le.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A Azure Portal keresse meg és válassza a **Azure ad B2C** lehetőséget.
1. A **házirendek** területen válassza a **felhasználói folyamatok** lehetőséget, majd válassza az **új felhasználói folyamat** elemet.

    ![Felhasználói folyamatok lap a portálon új felhasználói folyamat gomb kiemelve](./media/add-sign-up-and-sign-in-policy/signup-signin-user-flow.png)

1. A **felhasználói folyamat létrehozása** lapon válassza a **regisztráció és bejelentkezés** felhasználói folyamat elemet.

    ![Válassza ki a felhasználói folyamat lapot, és jelölje ki a regisztrációs és bejelentkezési folyamat elemet](./media/add-sign-up-and-sign-in-policy/select-user-flow-type.png)

1. **A verzió kiválasztása** területen válassza az **ajánlott** lehetőséget, majd válassza a **Létrehozás** lehetőséget. ([További](user-flow-versions.md) információ a felhasználói folyamatok verzióiról.)

    ![Felhasználói folyamat létrehozása lap Azure Portal a tulajdonságok kiemelve](./media/add-sign-up-and-sign-in-policy/select-version.png)

1. Adja meg a felhasználói folyamat **nevét** . Például: *signupsignin1*.
1. Az **Identity Providers** esetében válassza az **e-mail regisztráció** lehetőséget.
1. **Felhasználói attribútumok és jogcímek** esetében válassza ki azokat a jogcímeket és attribútumokat, amelyeket szeretne összegyűjteni, és a felhasználótól a regisztráció során elküldeni. Válassza például a **továbbiak megjelenítése** lehetőséget, majd az **ország/régió**, a **megjelenítendő név** és az **Irányítószám** elemnél válassza az attribútumok és jogcímek lehetőséget. Kattintson az **OK** gombra.

    ![Attribútumok és jogcímek kiválasztása lap három jogcímek kiválasztásával](./media/add-sign-up-and-sign-in-policy/signup-signin-attributes.png)

1. A felhasználói folyamat hozzáadásához kattintson a **Létrehozás** gombra. A *B2C_1* előtagja automatikusan előtagértéke a nevet.
2. Kövesse az ["elfelejtette jelszavát?" folyamat kezelésére szolgáló](add-password-reset-policy.md?pivots=b2c-user-flow.md#self-service-password-reset-recommended) lépéseket a regisztrálási vagy bejelentkezési szabályzaton belül.

### <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. Válassza ki a létrehozott felhasználói folyamatot az Áttekintés oldal megnyitásához, majd válassza a **felhasználói folyamat futtatása** lehetőséget.
1. Az **alkalmazás** lapon válassza ki a korábban regisztrált *webapp1* nevű webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Kattintson a **felhasználói folyamat futtatása** elemre, majd válassza a **regisztráció most** lehetőséget.

    ![Felhasználói folyamat futtatása lap a portálon a felhasználói folyamat futtatása gomb kiemelve](./media/add-sign-up-and-sign-in-policy/signup-signin-run-now.png)

1. Adjon meg egy érvényes e-mail-címet, kattintson az **ellenőrző kód küldése** elemre, adja meg a kapott ellenőrző kódot, majd válassza a **kód ellenőrzése** lehetőséget.
1. Adjon meg egy új jelszót, és erősítse meg a jelszót.
1. Válassza ki az országot és a régiót, adja meg a megjeleníteni kívánt nevet, adjon meg egy irányítószámot, majd kattintson a **Létrehozás** gombra. A rendszer visszaküldi a tokent, `https://jwt.ms` és megjelenik Önnek.
1. Most már újra futtathatja a felhasználói folyamatot, és be kell tudnia jelentkezni a létrehozott fiókkal. A visszaadott jogkivonat tartalmazza az ország/régió, a név és az irányítószám mezőben kiválasztott jogcímeket.

> [!NOTE]
> A "felhasználói folyamat futtatása" élmény jelenleg nem kompatibilis a SPA válasz URL-címével, amely az engedélyezési kód folyamatát használja. Ha a "felhasználói folyamat futtatása" funkciót szeretné használni az ilyen típusú alkalmazásokhoz, regisztráljon egy "web" típusú válasz URL-címet, és az [itt](tutorial-register-spa.md)leírtak alapján engedélyezze az implicit folyamatot.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-sign-up-and-sign-in-policy"></a>Regisztrálási és bejelentkezési szabályzat létrehozása

Az egyéni házirendek olyan XML-fájlok készletei, amelyeket feltölt a Azure AD B2C bérlőre a felhasználói útvonalak definiálásához. Alapszintű csomagokat biztosítunk számos előre elkészített házirenddel, többek között a regisztrálással és a bejelentkezéssel, a jelszó-visszaállítással és a profil szerkesztési házirendjével. További információ: Ismerkedés [az egyéni szabályzatokkal Azure ad B2Cban](custom-policy-get-started.md).

::: zone-end

## <a name="next-steps"></a>Következő lépések

* Vegyen fel egy [bejelentkezést a közösségi identitás-szolgáltatóval](add-identity-provider.md).
* Állítsa be a [jelszó-visszaállítási folyamatot](add-password-reset-policy.md).
