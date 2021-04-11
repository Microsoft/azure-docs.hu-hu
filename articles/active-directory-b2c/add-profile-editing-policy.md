---
title: Profil szerkesztési folyamatának beállítása
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan állíthat be profilt szerkesztési folyamatot Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/16/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: d84756a2ae4f8897c42e1846e3a91dbb9f7ad7e1
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257041"
---
# <a name="set-up-a-profile-editing-flow-in-azure-active-directory-b2c"></a>Profil-szerkesztési folyamat beállítása Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="profile-editing-flow"></a>Profil szerkesztési folyamata

A profil szerkesztési házirendje lehetővé teszi a felhasználók számára a profil attribútumaik kezelését, például a megjelenítendő név, a vezetéknév, az utónév, a város és egyebek. A profil szerkesztési folyamata a következő lépésekkel jár: 

1. Regisztráció vagy bejelentkezés helyi vagy közösségi fiókkal. Ha a munkamenet továbbra is aktív, Azure AD B2C engedélyezi a felhasználó számára, és a következő lépéshez ugorjon.
1. Azure AD B2C beolvassa a felhasználói profilt a címtárból, és lehetővé teszi a felhasználó számára az attribútumok szerkesztését.

![Profil szerkesztési folyamata](./media/add-profile-editing-policy/profile-editing-flow.png)


## <a name="prerequisites"></a>Előfeltételek

Ha még nem tette meg, [regisztráljon egy webalkalmazást Azure Active Directory B2C](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-profile-editing-user-flow"></a>Profil szerkesztése felhasználói folyamat létrehozása

Ha engedélyezni szeretné a felhasználók számára a profil szerkesztését az alkalmazásban, használja a felhasználói folyamatokat szerkesztő profilt.

1. A Azure AD B2C bérlő áttekintése lap menüjében válassza a **felhasználói folyamatok** lehetőséget, majd válassza az **új felhasználói folyamat** elemet.
1. A **felhasználói folyamat létrehozása** lapon válassza ki a **profilt szerkesztő** felhasználói folyamatot. 
1. **A verzió kiválasztása** területen válassza az **ajánlott** lehetőséget, majd válassza a **Létrehozás** lehetőséget.
1. Adja meg a felhasználói folyamat **nevét** . Például: *profileediting1*.
1. Az **Identity Providers** esetében válassza az **e-mail bejelentkezés** lehetőséget.
1. **Felhasználói attribútumok** esetében válassza ki azokat az attribútumokat, amelyeket szeretné, hogy az ügyfél szerkeszteni tudja a profiljában. Válassza például a **továbbiak megjelenítése** lehetőséget, majd válassza ki a **megjelenítendő név** és a **beosztás** attribútumait és jogcímeit. Kattintson az **OK** gombra.
1. A felhasználói folyamat hozzáadásához kattintson a **Létrehozás** gombra. Az *B2C_1* előtagját a rendszer automatikusan hozzáfűzi a névhez.

### <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. Válassza ki a létrehozott felhasználói folyamatot az Áttekintés oldal megnyitásához, majd válassza a **felhasználói folyamat futtatása** lehetőséget.
1. Az **alkalmazás** lapon válassza ki a korábban regisztrált *webapp1* nevű webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Kattintson a **felhasználói folyamat futtatása** elemre, majd jelentkezzen be a korábban létrehozott fiókkal.
1. Most már lehetősége van módosítani a felhasználó megjelenítendő nevét és a beosztás címét. Kattintson a **Folytatás** gombra. A rendszer visszaküldi a tokent, `https://jwt.ms` és megjelenik Önnek.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-profile-editing-policy"></a>Profilszerkesztési szabályzat létrehozása

Az egyéni házirendek olyan XML-fájlok készletei, amelyeket feltölt a Azure AD B2C bérlőre a felhasználói útvonalak definiálásához. Alapszintű csomagokat biztosítunk számos előre elkészített házirenddel, többek között a regisztrálással és a bejelentkezéssel, a jelszó-visszaállítással és a profil szerkesztési házirendjével. További információ: Ismerkedés [az egyéni szabályzatokkal Azure ad B2Cban](tutorial-create-user-flows.md?pivots=b2c-custom-policy).

::: zone-end

## <a name="next-steps"></a>Következő lépések

* Vegyen fel egy [bejelentkezést a közösségi identitás-szolgáltatóval](add-identity-provider.md).