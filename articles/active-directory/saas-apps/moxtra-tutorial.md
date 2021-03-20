---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Moxtra | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Moxtra között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: jeedes
ms.openlocfilehash: be63ea7ebf905b825ffe73dfc2b37dcdc3bb88fd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92507516"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-moxtra"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Moxtra

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Moxtra a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Moxtra-t az Azure AD-vel, a következőket teheti:

* A Moxtra-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Moxtra az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Moxtra egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Moxtra támogatja az **SP** által KEZDEMÉNYEZett SSO-t

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="adding-moxtra-from-the-gallery"></a>Moxtra hozzáadása a gyűjteményből

A Moxtra Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Moxtra a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Moxtra** kifejezést a keresőmezőbe.
1. Válassza ki a **Moxtra** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-moxtra"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Moxtra

Konfigurálja és tesztelje az Azure AD SSO-t a Moxtra a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Moxtra-ben.

Az Azure AD SSO és a Moxtra konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[MOXTRA SSO konfigurálása](#configure-moxtra-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre Moxtra-teszt felhasználót](#create-moxtra-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-Moxtra rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **Moxtra** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **bejelentkezési URL** szövegmezőbe írja be a következő URL-címet:  `https://www.moxtra.com/service/#login`

1. A Moxtra alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a felhasználói attribútumok párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

1. A fentieken kívül a Moxtra alkalmazás néhány további attribútumot vár, amelyeket az SAML-válaszban vissza kell adni. A felhasználó attribútumai párbeszédpanel felhasználói jogcímek szakaszában a következő lépésekkel adja hozzá az SAML-jogkivonat attribútumát az alábbi táblázatban látható módon:

    | Name | Forrás attribútum|
    | ------------------- | -------------------- |    
    | FirstName | User. givenName |
    | LastName | felhasználó. vezetéknév |
    | idpid    | < Azure AD-azonosító >

    > [!Note]
    > A **idpid** attribútum értéke nem valódi. A tényleges értéket lekérheti a **Moxtra beállítása** szakaszban a 8. lépésből. 

    1. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    1. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    1. Hagyja üresen a **névteret** .

    1. Válassza a forrás **attribútumként** lehetőséget.

    1. A **forrás attribútum** listáról írja be az adott sorhoz megjelenő attribútum értékét.

    1. Kattintson **az OK** gombra

    1. Kattintson a **Mentés** gombra.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Moxtra beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Moxtra.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Moxtra** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-moxtra-sso"></a>Moxtra SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Moxtra vállalati webhelyre rendszergazdaként.

2. A bal oldali eszköztáron kattintson a **felügyeleti konzol > SAML egyszeri bejelentkezés** elemre, majd az **új** elemre.
   
    ![Képernyőfelvétel: az SAML egyszeri bejelentkezés lapja, amely egy új SAML egyszeri bejelentkezés létrehozását mutatja be.](./media/moxtra-tutorial/tutorial_moxtra_06.png) 

3. Az **SAML** oldalon hajtsa végre a következő lépéseket:
   
    ![Képernyőfelvétel: az SAML-oldal, ahol megadhatja a leírt értékeket.](./media/moxtra-tutorial/tutorial_moxtra_08.png)   
 
    a. A **név** szövegmezőbe írja be a konfiguráció nevét (pl.: *SAML*). 
  
    b. Az **identitásszolgáltató entitás-azonosító** szövegmezőbe illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét. 
 
    c. A **bejelentkezési URL** szövegmezőbe illessze be a Azure Portalból másolt **bejelentkezési URL-cím** értékét. 
 
    d. A **AuthnContextClassRef** szövegmezőbe írja be az **urn: Oasis: Names: TC: SAML: 2.0: AC: classs: Password nevet**. 
 
    e. A **NameID formátum** szövegmezőbe írja be az **urn: Oasis: Names: TC: SAML: 1.1: NameID-Format: emailAddress** értéket. 
 
    f. Nyissa meg a Jegyzettömbben Azure Portal letöltött tanúsítványt, másolja a tartalmat, majd illessze be a **tanúsítvány** szövegmezőbe.    
 
    : Az SAML e-mail tartomány szövegmezőbe írja be az SAML e-mail tartományát.    
  
    >[!NOTE]
    >A tartomány ellenőrzésének lépéseinek megtekintéséhez kattintson az alábbi "**i**" gombra.

    h. Kattintson a **Frissítés** parancsra.

### <a name="create-moxtra-test-user"></a>Moxtra-tesztelési felhasználó létrehozása

Ennek a szakasznak a célja egy B. Simon nevű felhasználó létrehozása a Moxtra-ben.

**Ha B. Simon nevű felhasználót szeretne létrehozni a Moxtra-ben, hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a Moxtra vállalati webhelyre rendszergazdaként.

1. A bal oldali eszköztáron kattintson a **felügyeleti konzol > felhasználói kezelés**, majd a **felhasználó hozzáadása** elemre.
   
    ![Képernyőfelvétel: a felhasználó-felügyeleti oldal, amelyen a felhasználó hozzáadása lehetőség be van jelölve.](./media/moxtra-tutorial/tutorial_moxtra_10.png) 

1. A **felhasználó hozzáadása** párbeszédpanelen hajtsa végre a következő lépéseket:
  
    a. Az utónév szövegmezőbe írja be a " **B**" **nevet** .
  
    b. A vezetéknév szövegmezőbe írja be a **Simon** **nevet** .
  
    c. Az **e-mail** szövegmezőbe írja be a B. Simon e-mail-címét, mint Azure Portal.
  
    d. A **divízió** szövegmezőbe írja be a **dev** értéket.
  
    e. A **részleg** szövegmezőbe **írja be** a következőt:.
  
    f. Válassza a **rendszergazda** elemet.
  
    : Kattintson a **Hozzáadás** parancsra.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Moxtra csempére kattint, automatikusan be kell jelentkeznie arra a Moxtra, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [A Moxtra kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)