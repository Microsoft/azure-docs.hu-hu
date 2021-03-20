---
title: Oktatóanyag – multi-Factor Authentication a B2B-Azure AD-ben
description: Ebből az oktatóanyagból megtudhatja, hogyan megkövetelheti a többtényezős hitelesítés (MFA) használatát, ha az Azure AD B2B-t használja a külső felhasználókkal és partneri szervezetekkel való együttműködéshez.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f5f7f823d6c5f5860c2e78fe8cacdd1d788c581
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95974119"
---
# <a name="tutorial-enforce-multi-factor-authentication-for-b2b-guest-users"></a>Oktatóanyag: Többtényezős hitelesítés kényszerítése B2B vendégfelhasználók számára

Ha külső B2B-vendégfelhasználókkal dolgozik együtt, ajánlatos az alkalmazásokat többtényezős hitelesítési (MFA-) szabályzatokkal védeni. Ilyen esetben ugyanis a külső felhasználók az erőforrásokat nem érhetik el csupán egy felhasználónév és egy jelszó használatával. Azure Active Directory (Azure AD) esetében ezt a célt olyan feltételes hozzáférési szabályzattal hajthatja végre, amely az MFA elérését igényli. Az MFA-szabályzatok kötelezővé tehetők a bérlő, az alkalmazás vagy az egyes vendégfelhasználók szintjén is – ugyanúgy, ahogyan azt a saját cége felhasználóinál is megteheti.

Példa:

![Egy vállalati alkalmazásba bejelentkező vendég-felhasználó ábráját bemutató ábra](media/tutorial-mfa/aad-b2b-mfa-example.png)

1.  Az „A” cég egyik rendszergazdája vagy alkalmazottja azt kéri egy vendégfelhasználótól, hogy használjon egy olyan felhőalapú vagy helyszíni alkalmazást, amely kötelező MFA-használatra van konfigurálva.
2.  A vendégfelhasználó saját munkahelyi, iskolai vagy közösségi identitásával jelentkezik be. 
3.  A felhasználót felkéri a rendszer, hogy végezze el a többtényezős hitelesítést. 
4.  A felhasználó beállítja az „A” céghez tartozó MFA-hitelesítést, és elvégzi magának az MFA-beállításokat. A felhasználó hozzáférhet az alkalmazáshoz.

Az oktatóanyagban a következőket végezheti el:

> [!div class="checklist"]
> * A bejelentkezési folyamat tesztelése az MFA beállítása előtt.
> * Hozzon létre egy feltételes hozzáférési szabályzatot, amely megköveteli az MFA-t a környezetében lévő felhőalapú alkalmazásokhoz való hozzáféréshez. Ebben az oktatóanyagban ezt a folyamatot a Microsoft Azure felügyeleti alkalmazásának használatával mutatjuk be.
> * A What If eszköz használata az MFA-bejelentkezés szimulációjához.
> * Tesztelje a feltételes hozzáférési szabályzatot.
> * A tesztfelhasználó és a szabályzat törlése.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

 - **Hozzáférés prémium szintű Azure ad kiadáshoz**, amely magában foglalja a feltételes hozzáférési házirend képességeit. Az MFA betartatásához létre kell hoznia egy Azure AD feltételes hozzáférési szabályzatot. Fontos megjegyezni, hogy az MFA-szabályzatok mindig kötelezőek lesznek az Ön cégénél, függetlenül attól, hogy a partner rendelkezik-e MFA-képeséggel vagy sem. Ha MFA-t állít be a saját cégénél, ellenőrizze, hogy rendelkezésre állnak-e a szükséges Azure AD Premium-licencek a vendégfelhasználók számára is. 
 - **Egy érvényes külső e-mail-fiók**, amelyet vendégfelhasználóként hozzá tud majd adni a bérlői címtárhoz, és amellyel be lehet majd jelentkezni. Ha nem tudja, hogyan lehet vendégfiókot létrehozni, olvassa el a [B2B-vendégfelhasználó hozzáadása az Azure Portalon](add-users-administrator.md) című cikket.

## <a name="create-a-test-guest-user-in-azure-ad"></a>Tesztelési célú vendégfelhasználó felvétele Azure AD-ben

1. Jelentkezzen be az [Azure Portalba](https://portal.azure.com/) Azure ad-rendszergazdaként.
2. A bal oldali panelen válassza az **Azure Active Directory** lehetőséget.
3.  A **Kezelés** menüpontban válassza a **Felhasználók** lehetőséget.
4.  Válassza az **Új vendégfelhasználót**.

    ![Képernyőfelvétel az új vendég felhasználói lehetőség kiválasztásának helyéről](media/tutorial-mfa/tutorial-mfa-user-3.png)

5.  A **Felhasználónév** alatt adja meg a külső felhasználó e-mail-címét. Ha szeretné, üdvözlő üzenetet is megadhat. 

    ![A vendég Meghívási üzenet beírásának helyét bemutató képernyőkép](media/tutorial-mfa/tutorial-mfa-user-4.png)

6.  Válassza ki **Meghívás** elemet az meghívó automatikus elküldéséhez a vendégfelhasználó számára. Megjelenik a **Felhasználó meghívása sikerült** üzenet. 
7.  Miután elküldte a meghívót, a felhasználói fiók automatikusan hozzáadódik a címtárhoz vendégként.

## <a name="test-the-sign-in-experience-before-mfa-setup"></a>A bejelentkezési folyamat tesztelése az MFA beállítása előtt
1.  A tesztfelhasználó felhasználónevével és jelszavával jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2.  Az Azure Portalra be lehet jelentkezni csupán a bejelentkezési hitelesítő adatokkal. További hitelesítés nem szükséges hozzá.
3.  Jelentkezzen ki.

## <a name="create-a-conditional-access-policy-that-requires-mfa"></a>MFA-t igénylő feltételes hozzáférési szabályzat létrehozása
1.  Jelentkezzen be a [Azure Portal](https://portal.azure.com/) biztonsági rendszergazdaként vagy feltételes hozzáférésű rendszergazdaként.
2.  Az Azure Portalon válassza az **Azure Active Directory** lehetőséget. 
3.  A **Azure Active Directory** oldalon, a **Biztonság** szakaszban válassza a **feltételes hozzáférés** lehetőséget.
4.  A **Feltételes hozzáférés** oldalon, a felső eszköztáron válassza az **Új szabályzat** lehetőséget.
5.  Az **Új** lapon a **Név** szövegmezőben írja be az **MFA kötelező a B2B portál eléréséhez** szöveget.
6.  A **Hozzárendelések** szakaszban válassza a **Felhasználók és csoportok** lehetőséget.
7.  A **Felhasználók és csoportok** lapon válassza a **Felhasználók és csoportok kiválasztása** lehetőséget, majd válassza ki a **Minden vendégfelhasználó (előzetes verzió)** lehetőséget.

    ![Az összes vendég felhasználó kijelölését bemutató képernyőkép](media/tutorial-mfa/tutorial-mfa-policy-6.png)
9.  Válassza a **Kész** lehetőséget.
10. A **Hozzárendelések** szakaszban az **Új** lapon válassza a **Felhőalkalmazások** lehetőséget.
11. A **Felhőalkalmazások** lapon válassza az **Alkalmazások kiválasztása** elemet, majd válassza a **Kiválasztás** lehetőséget.

    ![A Cloud apps oldalt és a Select lehetőséget bemutató képernyőkép](media/tutorial-mfa/tutorial-mfa-policy-10.png)

12. A **Kiválasztás** lapon válassza a **Microsoft Azure-felügyelet** elemet, majd a **Kiválaszt** lehetőséget.

    ![Képernyőfelvétel: a Microsoft Azure felügyeleti lehetőség kiemelése.](media/tutorial-mfa/tutorial-mfa-policy-11.png)

13. A **Felhőalkalmazások** lapon válassza a **Kész** lehetőséget.
14. Az **Új** lapon a **Hozzáférés-vezérlések** szakaszban válassza az **Engedélyezés** lehetőséget.
15. Az **Engedélyezés** lapon válassza a **Hozzáférés engedélyezése** lehetőséget, jelölje be a **Többtényezős hitelesítés megkövetelése** jelölőnégyzetet, majd válassza a **Kiválaszt** elemet.

    ![A többtényezős hitelesítés megkövetelését bemutató képernyőkép](media/tutorial-mfa/tutorial-mfa-policy-13.png)

16. A **Szabályzat engedélyezése** alatt válassza a **Be** lehetőséget.

    ![Képernyőfelvétel – a házirend engedélyezése beállítás beállítása a következőre](media/tutorial-mfa/tutorial-mfa-policy-14.png)

17. Válassza a **Létrehozás** lehetőséget.

## <a name="use-the-what-if-option-to-simulate-sign-in"></a>A What If lehetőség használata a bejelentkezés szimulációjára

1.  A **feltételes hozzáférés – házirendek** lapon válassza a **What if** lehetőséget. 

    ![Képernyőkép, amely kiemeli, hogy hol válassza ki a mi, ha lehetőséget a feltételes hozzáférés – házirendek oldalon.](media/tutorial-mfa/tutorial-mfa-whatif-1.png)

2.  Válassza a **Felhasználó** lehetőséget, majd válassza ki a tesztelési vendégfelhasználót, majd pedig a **Kiválaszt** lehetőséget.

    ![Képernyőfelvétel a kiválasztott vendég felhasználóról](media/tutorial-mfa/tutorial-mfa-whatif-2.png)

3.  Válassza a **Felhőalkalmazások** elemet.
4.  A **Felhőalkalmazások** lapon válassza az **Alkalmazások kiválasztása** elemet, majd válassza a **Kiválasztás** lehetőséget. Az alkalmazások listájában válassza a **Microsoft Azure-felügyelet** elemet, majd kattintson a **Kiválaszt** lehetőségre. 

    ![A kiválasztott Microsoft Azure felügyeleti alkalmazást bemutató képernyőkép](media/tutorial-mfa/tutorial-mfa-whatif-3.png)

5.  A **Felhőalkalmazások** lapon válassza a **Kész** lehetőséget.
6.  Válassza a **What If** lehetőséget, és ellenőrizze, hogy megjelenik-e az új szabályzat az **Értékelési eredmények** alatt az **Érvényes szabályzatok** fülön.

    ![A mi ha lehetőség kiválasztását bemutató képernyőkép](media/tutorial-mfa/tutorial-mfa-whatif-4.png)

## <a name="test-your-conditional-access-policy"></a>A feltételes hozzáférési szabályzat tesztelése
1.  A tesztfelhasználó felhasználónevével és jelszavával jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2.  Kérés jelenik meg a további hitelesítési módszerek hozzáadására. A szabályzat hatályba lépéséhez bizonyos időre van szükség.

    ![A szükséges további információkat tartalmazó üzenet képernyőképe](media/tutorial-mfa/mfa-required.png)
 
3.  Jelentkezzen ki.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, távolítsa el a teszt felhasználót és a feltételes hozzáférési szabályzat tesztelését.
1.  Jelentkezzen be az [Azure Portalba](https://portal.azure.com/) Azure ad-rendszergazdaként.
2.  A bal oldali panelen válassza az **Azure Active Directory** lehetőséget.
3.  A **Kezelés** menüpontban válassza a **Felhasználók** lehetőséget.
4.  Válassza ki a tesztfelhasználót, majd utána a **Felhasználó törlése** lehetőséget.
5.  A bal oldali panelen válassza az **Azure Active Directory** lehetőséget.
6.  A **Biztonsági** alatt válassza a **Feltételes hozzáférés** lehetőséget.
7.  A **Szabályzatnév** listában válassza a tesztszabályzathoz tartozó helyi menüt (...), majd kattintson a **Törlés** lehetőségre. Válassza az **Igen** lehetőséget a megerősítéshez.

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban létrehozott egy feltételes hozzáférési szabályzatot, amely megköveteli, hogy a vendég felhasználók az MFA-t használják az egyik felhőalapú alkalmazásba való bejelentkezéskor. Az együttműködő vendégfelhasználók hozzáadásáról további tudnivalók: [Azure Active Directory B2B együttműködő felhasználók hozzáadása az Azure portálon](add-users-administrator.md).
