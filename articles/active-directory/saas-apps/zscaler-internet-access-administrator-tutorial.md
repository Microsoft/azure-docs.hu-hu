---
title: 'Oktatóanyag: Azure Active Directory integráció a Zscaler internet-hozzáférés rendszergazdájával | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a Zscaler internet-hozzáférés rendszergazdája között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/25/2021
ms.author: jeedes
ms.openlocfilehash: 70afa0a02f4e303105aec1884b966796854c6f49
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102449323"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-internet-access-administrator"></a>Oktatóanyag: Azure Active Directory integráció a Zscaler Internet-hozzáférési rendszergazdájával

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Zscaler Internet-hozzáférési rendszergazdáját Azure Active Directory (Azure AD) használatával. Ha integrálja az Zscaler internet-hozzáférés-rendszergazdát az Azure AD-vel, a következőket teheti:

* A Zscaler internet-hozzáférés rendszergazdájához hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az Internet-hozzáférés Zscaler az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Zscaler az Internet-hozzáférés rendszergazdai egyszeri bejelentkezés (SSO) engedélyezett előfizetését.

> [!NOTE]
> Ez az integráció az Azure AD USA kormányzati felhőalapú környezetének használatával is elérhető. Ezt az alkalmazást az Azure AD US government Cloud Application Galleryben találja, és ugyanúgy konfigurálhatja, mint a nyilvános felhőben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Zscaler internet-hozzáférés rendszergazdája támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést.

## <a name="add-zscaler-internet-access-administrator-from-the-gallery"></a>Zscaler internet-hozzáférés rendszergazdájának hozzáadása a katalógusból

A Zscaler Internet-hozzáférési rendszergazdája Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Zscaler internet-hozzáférés rendszergazdáját a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Zscaler internet-hozzáférés rendszergazdája** kifejezést a keresőmezőbe.
1. Válassza az **Zscaler internet-hozzáférés rendszergazdája** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-internet-access-administrator"></a>Az Azure AD SSO konfigurálása és tesztelése a Zscaler internet-hozzáférés rendszergazdája számára

Konfigurálja és tesztelje az Azure AD SSO-t a Zscaler internet-hozzáférés rendszergazdájával egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Zscaler Internet-hozzáférési rendszergazdája között.

Az Azure AD SSO konfigurálásához és teszteléséhez a Zscaler Internet-hozzáférési rendszergazdájával hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
    1. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
2. Az **[Zscaler internet-hozzáférés rendszergazdai egyszeri bejelentkezésének konfigurálása](#configure-zscaler-internet-access-administrator-sso)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
    1. **[Zscaler-alapú internet-hozzáférés rendszergazdai tesztelési felhasználó létrehozása](#create-zscaler-internet-access-administrator-test-user)** – a Zscaler Internet-hozzáférési rendszergazdájához tartozó Britta, amely a felhasználó Azure ad-képviseletéhez van társítva.
3. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **Zscaler internet-hozzáférés rendszergazdai** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeres Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** ceruza ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az alábbi URL-címek egyikét a követelménynek megfelelően:

    | Azonosító |
    |------------|
    | `https://admin.zscaler.net` |
    | `https://admin.zscalerone.net` |
    | `https://admin.zscalertwo.net` |
    | `https://admin.zscalerthree.net` |
    | `https://admin.zscloud.net` |
    | `https://admin.zscalerbeta.net` |

    b. A **Válasz URL-címe** szövegmezőbe írja be az alábbi URL-címek egyikét az Ön által előírt követelmények szerint:

    | Válasz URL-cím |
    |-----------|
    | `https://admin.zscaler.net/adminsso.do` |
    | `https://admin.zscalerone.net/adminsso.do` |
    | `https://admin.zscalertwo.net/adminsso.do` |
    | `https://admin.zscalerthree.net/adminsso.do` |
    | `https://admin.zscloud.net/adminsso.do` |
    | `https://admin.zscalerbeta.net/adminsso.do` |

5. A Zscaler internet-hozzáférés rendszergazdai alkalmazása egy adott formátumban várja az SAML-kijelentéseket. Konfigurálja a következő jogcímeket ehhez az alkalmazáshoz. Ezen attribútumok értékeit az alkalmazás-integráció lapon lévő **felhasználói attribútumok & jogcímek** szakaszban kezelheti. Az **egyszeres Sign-On beállítása az SAML-vel lapon** kattintson a **Szerkesztés** gombra a **felhasználói attribútumok & jogcímek** párbeszédpanel megnyitásához.

    ![Az attribútum hivatkozása](./media/zscaler-internet-access-administrator-tutorial/attributes.png)

6. A **felhasználó attribútumai** párbeszédpanel **felhasználói jogcímek** szakaszában konfigurálja az SAML-jogkivonat attribútumot a fenti képen látható módon, és hajtsa végre a következő lépéseket:

    | Name  | Forrás attribútum  |
    | ---------| ------------ |
    | Szerepkör | User. assignedroles |

    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    b. A **forrás attribútum** listából válassza ki az attribútum értékét.

    c. Kattintson az **OK** gombra.

    d. Kattintson a **Mentés** gombra.

    > [!NOTE]
    > [Ide kattintva](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) megtudhatja, hogyan konfigurálhatja a szerepkört az Azure ad-ben.

7. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

8. Az **Zscaler internet-hozzáférésének beállítása** című szakaszban adja meg a megfelelő URL (eke) t a követelménynek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát, ha hozzáférést biztosít a Zscaler Internet-hozzáférési rendszergazdájához.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **Zscaler internet-hozzáférés rendszergazdája** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a fentiekben ismertetett módon állította be a szerepköröket, kiválaszthatja a **szerepkör kiválasztása** legördülő listából.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-zscaler-internet-access-administrator-sso"></a>Az Zscaler internet-hozzáférés rendszergazdai egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Zscaler internet-hozzáférés rendszergazdai felhasználói felületére.

2. Nyissa meg az **adminisztráció > rendszergazdai felügyelet** eszközt, és hajtsa végre a következő lépéseket, majd kattintson a Mentés gombra:

    ![A képernyőképen az SAML-hitelesítés engedélyezésével, az S S L tanúsítvány feltöltésével és a kiállító megadásával kapcsolatos beállítások láthatók a rendszergazdák számára.](./media/zscaler-internet-access-administrator-tutorial/management.png "Felügyelet")

    a. Jelölje be az **SAML-hitelesítés engedélyezése** címűt.

    b. Kattintson a **feltöltés** gombra, és töltse fel a **nyilvános SSL-tanúsítványban** Azure Portal letöltött Azure SAML-aláíró tanúsítványt.

    c. Ha szükséges, a további biztonság érdekében adja hozzá a **kibocsátó** adatait az SAML-válasz kiállítójának ellenőrzéséhez.

3. A rendszergazdai felhasználói felületen hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: a rendszergazda U I, ahol elvégezheti a lépéseket.](./media/zscaler-internet-access-administrator-tutorial/activation.png)

    a. Vigye az egérmutatót a bal alsó sarokban található **aktiválási** menü fölé.

    b. Kattintson az **aktiválás** gombra.

### <a name="create-zscaler-internet-access-administrator-test-user"></a>Zscaler internet-hozzáférés rendszergazdai tesztelési felhasználójának létrehozása

Ennek a szakasznak a célja, hogy létrehozzon egy Britta Simon nevű felhasználót a Zscaler internet-hozzáférés rendszergazdájánál. A Zscaler internet-hozzáférés nem támogatja az igény szerinti üzembe helyezést a rendszergazdai egyszeri bejelentkezéshez. Manuálisan kell létrehoznia egy rendszergazdai fiókot.
A rendszergazdai fiókok létrehozásával kapcsolatos lépésekért tekintse meg a Zscaler dokumentációját:

https://help.zscaler.com/zia/adding-admins

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját.

* Kattintson az alkalmazás tesztelése Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a Zscaler Internet-hozzáférési Rendszergazdába, amelyhez be szeretné állítani az egyszeri bejelentkezést

* Használhatja a Microsoft saját alkalmazásait. Ha a saját alkalmazások Zscaler internet-hozzáférés rendszergazdája csempére kattint, akkor automatikusan be kell jelentkeznie a Zscaler Internet-hozzáférési Rendszergazdájába, amelyhez be kell állítania az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

Miután konfigurálta a Zscaler Internet-hozzáférési rendszergazdát, kikényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).
