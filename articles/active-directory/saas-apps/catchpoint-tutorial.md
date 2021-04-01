---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Catchpoint'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Catchpoint között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: a8515920985c569df74b1e328d6bfe1c4ec97195
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735314"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-catchpoint"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses integráció a Catchpoint

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Catchpoint a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Catchpoint-t az Azure AD-vel, a következőket teheti:

* Felhasználói hozzáférés vezérlése az Catchpoint az Azure AD-ből.
* Engedélyezze az automatikus Catchpoint-bejelentkezést az Azure AD-fiókkal rendelkező felhasználók számára.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Az egyszeri bejelentkezést (SSO) engedélyező Catchpoint-előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Catchpoint támogatja az SP által kezdeményezett és a IDENTITÁSSZOLGÁLTATÓ által kezdeményezett egyszeri bejelentkezést.
* A Catchpoint támogatja az igény szerinti (JIT) felhasználók üzembe helyezését.

## <a name="add-catchpoint-from-the-gallery"></a>Catchpoint hozzáadása a gyűjteményből

A Catchpoint Azure AD-be való integrálásának konfigurálásához adja hozzá a Catchpoint-t a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi, iskolai vagy személyes Microsoft-fiók.
1. A bal oldali panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Catchpoint** kifejezést a keresőmezőbe.
1. Válassza az **Catchpoint** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-catchpoint"></a>Azure AD SSO konfigurálása és tesztelése a Catchpoint-hez

Az egyszeri bejelentkezés működéséhez egy Azure AD-felhasználót kell összekapcsolnia a Catchpoint-ben lévő felhasználóval. Ebben az oktatóanyagban egy **B. Simon** nevű teszt felhasználót konfigurálunk. 

Végezze el a következő szakaszt:

1. [Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)-t, hogy engedélyezze ezt a szolgáltatást a felhasználók számára.
    * [Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * [Rendelje hozzá az Azure ad-teszt felhasználóját](#assign-the-azure-ad-test-user), hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. [Konfigurálja a CATCHPOINT SSO](#configure-catchpoint-sso)-t, hogy konfigurálja az egyszeri bejelentkezési beállításokat az alkalmazás oldalán.
    * [Hozzon létre egy Catchpoint](#create-a-catchpoint-test-user), amely lehetővé teszi a B. Simon Azure ad-teszt fiókjának összekapcsolását a Catchpoint hasonló felhasználói fiókjával.
1. [Tesztelje az egyszeri bejelentkezést](#test-sso), hogy ellenőrizze, a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az Azure AD SSO engedélyezéséhez kövesse az Azure Portal alábbi lépéseit:

1. Jelentkezzen be az Azure Portalra.
1. A **Catchpoint** alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeres Sign-On beállítása az SAML-vel** lapon válassza a ceruza ikont az **alapszintű SAML-konfigurációs** beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. A Catchpoint kezdeményezett üzemmódjának konfigurálása:
   - A **identitásszolgáltató** által kezdeményezett mód esetében adja meg a következő mezők értékeit:
     - **Azonosító**:`https://portal.catchpoint.com/SAML2`
     - **Válasz URL-címe**:`https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`
   - Az **SP** által kezdeményezett mód **beállításnál válassza a további URL-címek beállítása** lehetőséget, és adja meg a következő értéket:
     - **Bejelentkezési URL-cím** esetén:`https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`

1. A Catchpoint alkalmazás megadott formátumban várja az SAML-kijelentéseket. Egyéni attribútum-hozzárendelések hozzáadása az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi táblázat az alapértelmezett attribútumok listáját tartalmazza:

    | Name | Forrás attribútum|
    | ------------ | --------- |
    | GivenName | User. givenneame |
    | vezetéknév; | felhasználó. vezetéknév |
    | EmailAddress | User. mail |
    | Name | User. userPrincipalName |
    | Egyedi felhasználói azonosító | User. userPrincipalName |

    ![Felhasználói attribútumok & jogcímek listája képernyőkép](common/default-attributes.png)

1. Emellett a Catchpoint alkalmazás egy másik attribútumot vár, amelyet egy SAML-válaszban kell átadni. Lásd az alábbi táblázatot. Ez az attribútum előre fel van töltve, de a követelményeinek megfelelően áttekintheti és frissítheti azt.

    | Name | Forrás attribútum|
    | ------------ | --------- |
    | névtér | User. assignedrole |

    > [!NOTE]
    > A `namespace` jogcímet a fiók nevével kell leképezni. Ennek a fióknak a nevét az Azure AD-ben az SAML-válaszban visszaadott szerepkörrel kell beállítani. Az Azure AD szerepköreivel kapcsolatos további információkért lásd: a [vállalati alkalmazások SAML-jogkivonatában kiadott szerepkör-jogcím konfigurálása](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview).

1. Nyissa **meg az egyszeri Sign-On beállítása SAML** -oldallal című lapot. Az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** szakaszt. A **Letöltés** gombra kattintva mentse a tanúsítványt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Catchpoint beállítása** szakaszban másolja ki a szükséges URL-címeket egy későbbi lépésben.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban a Azure Portal használatával hozzon létre egy B. Simon nevű Azure AD-teszt felhasználót.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**  >  **felhasználók**  >  **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Adja meg például a következőt: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet. Jegyezze fel a megjelenített jelszó értékét.
   1. Válassza a **Létrehozás** lehetőséget.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban engedélyezi a B. Simon számára az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a Catchpoint.

1. A Azure Portal válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Catchpoint** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza ki a felhasználók listájából a **B. Simon** elemet. Kattintson a **kijelölés** gombra a képernyő alján.
1. Ha a fentiekben ismertetett módon állította be a szerepköröket, kiválaszthatja a **szerepkör kiválasztása** legördülő listából.
1. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés** lehetőséget.

## <a name="configure-catchpoint-sso"></a>Catchpoint SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a Catchpoint alkalmazásba.

1. Válassza a **Beállítások** ikont, majd az **SSO Identity Provider** elemet.

    ![A Catchpoint-beállítások képernyőképe a kijelölt SSO-identitás-szolgáltatóval](./media/catchpoint-tutorial/configuration1.png)

1. Az **egyszeri bejelentkezés** lapon adja meg a következő mezőket:

   ![Catchpoint egyszeri bejelentkezés lapjának képernyőképe](./media/catchpoint-tutorial/configuration2.png)

   Mező | Érték
   ----- | ----- 
   **Névtér** | Egy érvényes névtér-érték.
   **Identitás-szolgáltató kiállítója** | A `Azure AD Identifier` Azure Portal értékét.
   **Egyszeri bejelentkezés URL-címe** | A `Login URL` Azure Portal értékét.
   **Tanúsítvány** | A letöltött `Certificate (Base64)` fájl tartalma a Azure Portalból. A Jegyzettömb használatával megtekintheti és másolhatja.

   Az **összevonás metaadatait tartalmazó XML-fájlt** is feltöltheti a **metaadatok feltöltése** lehetőség kiválasztásával.

1. Kattintson a **Mentés** gombra.

### <a name="create-a-catchpoint-test-user"></a>Catchpoint-teszt felhasználó létrehozása

A Catchpoint támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nem találhatók műveleti elemek. Ha a B. Simon még nem létezik felhasználóként a Catchpoint-ben, akkor a hitelesítés után jön létre.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a Catchpoint bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.  

* Lépjen közvetlenül a Catchpoint bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a Catchpoint, amelyhez be szeretné állítani az egyszeri bejelentkezést 

A Microsoft My Apps használatával bármilyen módban tesztelheti az alkalmazást. Ha a saját alkalmazások Catchpoint csempére kattint, ha az SP módban van konfigurálva, a rendszer átirányítja az alkalmazás bejelentkezési lapjára a bejelentkezési folyamat kezdeményezéséhez, és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie arra a Catchpoint, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


> [!NOTE]
> Amikor bejelentkezett a Catchpoint alkalmazásba a bejelentkezési oldalon keresztül, a **Catchpoint hitelesítő adatainak** megadása után adja meg az érvényes **névtér** értékét a **vállalati hitelesítő adatok (SSO)** mezőben, és válassza a **Bejelentkezés** lehetőséget.
> 
> ![Catchpoint-konfiguráció](./media/catchpoint-tutorial/loginimage.png)

## <a name="next-steps"></a>Következő lépések

A Catchpoint konfigurálása után kényszerítheti a munkamenet-vezérlést. Ez a figyelmeztetés védelmet nyújt a szervezete bizalmas adatainak valós idejű kiszűrése és beszivárgása ellen. A munkamenet-vezérlő a feltételes hozzáférés kiterjesztése. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).
