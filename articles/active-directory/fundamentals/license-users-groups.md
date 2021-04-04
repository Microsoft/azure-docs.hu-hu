---
title: Licencek kiosztása vagy eltávolítása – Azure Active Directory | Microsoft Docs
description: Útmutatás Azure Active Directory licencek felhasználókhoz vagy csoportokhoz való hozzárendeléséről és eltávolításáról.
services: active-directory
author: ajburnle
manager: daveba
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6900647acf7182529f34c8cc065dbb039de38be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97504406"
---
# <a name="assign-or-remove-licenses-in-the-azure-active-directory-portal"></a>Licencek kiosztása vagy eltávolítása a Azure Active Directory portálon

Számos Azure Active Directory (Azure AD) szolgáltatáshoz az adott szolgáltatáshoz tartozó felhasználók vagy csoportok (és a társított tagok) licence szükséges. Csak az aktív licenccel rendelkező felhasználók férhetnek hozzá és használhatják a licencelt Azure AD-szolgáltatásokat, amelyekre igaz. A licencek a bérlők szerint lesznek alkalmazva, és nem vihetők át más bérlők felé. 

## <a name="available-license-plans"></a>Rendelkezésre álló licenc csomagok

Az Azure AD szolgáltatáshoz több engedélyezési csomag is elérhető, többek között:

- Azure AD Free

- Prémium szintű Azure AD P1

- Prémium szintű Azure AD P2

Az egyes licencelési csomagokkal és a hozzájuk kapcsolódó licencelési adatokkal kapcsolatos információkért lásd: [milyen licencre van szükségem?](https://azure.microsoft.com/pricing/details/active-directory/). Az Azure AD Premium-licencszerződések regisztrálásához lásd [itt](./active-directory-get-started-premium.md).

Nem minden Microsoft-szolgáltatás érhető el minden helyen. Ahhoz, hogy egy licencet hozzá lehessen rendelni egy csoporthoz, meg kell adnia az összes tag **használati helyét** . Ezt az értéket megadhatja az Azure AD **Azure Active Directory &gt; felhasználói &gt; profil &gt; beállításai** területén. Minden olyan felhasználó, akinek a használati helye nincs megadva, örökli az Azure AD-szervezet helyét.

## <a name="view-license-plans-and-plan-details"></a>A licencelési csomagok és a csomag részleteinek megtekintése

Megtekintheti az elérhető szolgáltatási csomagokat, beleértve az egyes licenceket, ellenőrizhető a lejárati dátumokat, és megtekintheti az elérhető hozzárendelések számát.

### <a name="to-find-your-service-plan-and-plan-details"></a>A szolgáltatási csomag és a terv részleteinek megkeresése

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) az Azure ad-szervezetben található licenc-rendszergazdai fiók használatával.

1. Válassza a **Azure Active Directory** lehetőséget, majd válassza a **licencek** lehetőséget.

    :::image type="content" source="media/license-users-groups/license-details-blade.png" alt-text="Licencek lap, a megvásárolt szolgáltatások és a hozzárendelt licencek számával":::

1. Válassza a **minden termék** lehetőséget a minden termék lap megtekintéséhez, és tekintse meg a licencelési csomagok **összesített**, **hozzárendelt**, **elérhető** és **hamarosan lejáró** számát.

    :::image type="content" source="media/license-users-groups/license-products-blade-with-products.png" alt-text="szolgáltatások lap – a Service License Plans – társított licenc adatai":::

    > [!NOTE]
    > A számok a következőképpen vannak definiálva: 
    > - Összesen: megvásárolt licencek száma összesen
    > - Hozzárendelt: a felhasználókhoz rendelt licencek száma
    > - Elérhető: a hozzárendeléshez rendelkezésre álló licencek száma, beleértve a hamarosan lejárt
    > - Hamarosan lejár: a hamarosan lejáró licencek száma

1. Válassza ki a csomag nevét a licenccel rendelkező felhasználók és csoportok megtekintéséhez.

## <a name="assign-licenses-to-users-or-groups"></a>Licencek kiosztása felhasználókhoz vagy csoportokhoz

Győződjön meg arról, hogy a licenccel rendelkező Azure AD-szolgáltatás használatához bárkinek rendelkeznie kell a megfelelő licenccel. A licencelési jogosultságokat hozzáadhatja a felhasználókhoz vagy egy teljes csoporthoz is.

### <a name="to-assign-a-license-to-a-user"></a>Licenc kiosztása egy felhasználóhoz

1. A **termékek** lapon válassza ki a felhasználóhoz hozzárendelni kívánt licencelési csomag nevét.

    ![szolgáltatások lap, a Kiemelt szolgáltatási licencelési csomaggal](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. A licencelési terv áttekintése lapon válassza a **hozzárendelés** lehetőséget.

    ![szolgáltatások lap, Kiemelt hozzárendelési lehetőséggel](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. A **hozzárendelés** lapon válassza a **felhasználók és csoportok** lehetőséget, majd keresse meg és válassza ki azt a felhasználót, akinek a licencet hozzárendeli.

    ![Licencek kiosztása oldal, Kiemelt kereséssel és Select beállításokkal](media/license-users-groups/assign-license-blade-with-highlight.png)

1. Válassza a **hozzárendelési beállítások** lehetőséget, győződjön meg arról, hogy a megfelelő licencelési beállítások be vannak kapcsolva, majd kattintson **az OK gombra**.

    ![Licencelési lehetőség lap, a licencelési csomagban elérhető összes lehetőséggel](media/license-users-groups/license-option-blade-assignments.png)

    A **licenc-hozzárendelési** oldal frissítései megjelenítik, hogy a felhasználó ki van-e választva, és hogy a hozzárendelések konfigurálva vannak-e.

    > [!NOTE]
    > Nem minden Microsoft-szolgáltatás érhető el minden helyen. Ahhoz, hogy egy licencet a felhasználóhoz lehessen rendelni, meg kell adnia a **használat helyét**. Ezt az értéket megadhatja az Azure AD **Azure Active Directory &gt; felhasználói &gt; profil &gt; beállításai** területén. Minden olyan felhasználó, akinek a használati helye nincs megadva, örökli az Azure AD-szervezet helyét.

1. Válassza a **Hozzárendelés** elemet.

    A rendszer hozzáadja a felhasználót a licenccel rendelkező felhasználók listájához, és hozzáfér a mellékelt Azure AD-szolgáltatásokhoz.
    > [!NOTE]
    > A licencek a felhasználó **licencek** lapján közvetlenül is hozzárendelhetők a felhasználóhoz. Ha a felhasználó rendelkezik egy csoporttagság által hozzárendelt licenccel, és a felhasználóhoz ugyanazt a licencet szeretné közvetlenül hozzárendelni, akkor csak az 1. lépésben említett **termékek** lapon lehet elvégezni.

### <a name="to-assign-a-license-to-a-group"></a>Licenc társítása egy csoporthoz

1. A **termékek** lapon válassza ki a felhasználóhoz hozzárendelni kívánt licencelési csomag nevét.

    ![Termékek panel, Kiemelt termék licencelési csomaggal](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. A **prémium szintű Azure Active Directory 2. csomag** lapon válassza a **hozzárendelés** lehetőséget.

    ![Termékek lap, Kiemelt hozzárendelési lehetőséggel](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. A **hozzárendelés** lapon válassza a **felhasználók és csoportok** lehetőséget, majd keresse meg és válassza ki azt a csoportot, amelyhez hozzá szeretné rendelni a licencet.

    ![Licenc kiosztása oldal, a kijelölt kereséssel és a 2. lehetőséggel](media/license-users-groups/assign-group-license-blade-with-highlight.png)

1. Válassza a **hozzárendelési beállítások** lehetőséget, győződjön meg arról, hogy a megfelelő licencelési beállítások be vannak kapcsolva, majd kattintson **az OK gombra**.

    ![Licencelési lehetőség lap, a 2. licencelési csomagban elérhető összes lehetőséggel](media/license-users-groups/license-option-blade-group-assignments.png)

    A **licenc-hozzárendelési** oldal frissítései megjelenítik, hogy a felhasználó ki van-e választva, és hogy a hozzárendelések konfigurálva vannak-e.

1. Válassza a **Hozzárendelés** elemet.

    A csoport hozzá lett adva a licencelt csoportok listájához, és az összes tag hozzáférhet a mellékelt Azure AD-szolgáltatásokhoz.

## <a name="remove-a-license"></a>Licenc eltávolítása

A licenceket eltávolíthatja egy felhasználó Azure AD-felhasználó oldaláról, a csoport áttekintés lapjáról, vagy az Azure AD- **licencek** lapról, és megtekintheti a licenchez tartozó felhasználókat és csoportokat.

### <a name="to-remove-a-license-from-a-user"></a>Licenc eltávolítása a felhasználótól

1. A szolgáltatási csomag **licenccel rendelkező felhasználók** lapján válassza ki azt a felhasználót, akinek már nem lesz a licence. Például _Alain Charon_.

1. Válassza a **licenc eltávolítása** lehetőséget.

    ![Licenccel rendelkező felhasználók lap Kiemelt licenc eltávolítása lehetőséggel](media/license-users-groups/license-products-user-blade-with-remove-option-highlight.png)

> [!IMPORTANT]
> A felhasználó által egy csoporttól örökölt licencek nem távolíthatók el közvetlenül. Ehelyett el kell távolítania a felhasználót abból a csoportból, amelyből a licencet örökli.

### <a name="to-remove-a-license-from-a-group"></a>Licenc eltávolítása egy csoportból

1. A licencelési csomag **licencelt csoportok** lapján válassza ki azt a csoportot, amelynek már nem lesz a licence.

1. Válassza a **licenc eltávolítása** lehetőséget.

    ![Licencelt csoportok lap – Kiemelt licenc eltávolítása lehetőség 2](media/license-users-groups/license-products-group-blade-with-remove-option-highlight.png)
    
    > [!NOTE]
    > Ha egy Azure AD-val szinkronizált helyszíni felhasználói fiók a szinkronizálás hatókörén kívül esik, vagy ha a szinkronizálás el lett távolítva, a felhasználó az Azure AD-ben nem törlődik. Ha ez történik, a felhasználóhoz közvetlenül vagy csoportos licencelésen keresztül hozzárendelt licencek nem **töröltként** lesznek **felfüggesztve** .

## <a name="next-steps"></a>Következő lépések

A licencek hozzárendelése után a következő folyamatokat végezheti el:

- [Licenc-hozzárendelési problémák azonosítása és megoldása](../enterprise-users/licensing-groups-resolve-problems.md)

- [Licenccel rendelkező felhasználók felvétele licencelési csoportba](../enterprise-users/licensing-groups-migrate-users.md)

- [Forgatókönyvek, korlátozások és ismert problémák csoportok használatával a licencelés kezeléséhez Azure Active Directory](../enterprise-users/licensing-group-advanced.md)

- [Profil adatainak hozzáadása vagy módosítása](active-directory-users-profile-azure-portal.md)
