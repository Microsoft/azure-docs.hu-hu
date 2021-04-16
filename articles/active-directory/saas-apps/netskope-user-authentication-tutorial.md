---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrációja a Netskope User Authentication | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory Netskope User Authentication szolgáltatás között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/06/2021
ms.author: jeedes
ms.openlocfilehash: 78ef49740c3e74c4ad5a30838d9721969ca70b36
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516888"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-user-authentication"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrációja a Netskope User Authentication használatával

Ez az oktatóanyag bemutatja, hogyan integrálhatja a Netskope User Authenticationt a Azure Active Directory (Azure AD) használatával. Ha integrálja a Netskope User Authenticationt az Azure AD-val, a következőt teszi:

* Annak vezérlése az Azure AD-ban, hogy ki férhet hozzá a Netskope User Authentication szolgáltatáshoz.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezve jelentkeznek be a Netskope User Authentication szolgáltatásba az Azure AD-fiókjukkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésekhez a következő elemekre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes fiókot [is kaphat.](https://azure.microsoft.com/free/)
* Netskope User Authentication single sign-on (SSO) enabled subscription.Netskope User Authentication single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD SSO-t.

* A Netskope User Authentication támogatja az **sp és az idP által** kezdeményezett SSO-t.

## <a name="add-netskope-user-authentication-from-the-gallery"></a>Netskope User Authentication hozzáadása a katalógusból

A Netskope-felhasználóhitelesítés Azure AD-be való integrálásának konfigurálához fel kell vennie a Netskope User Authenticationt a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal vagy személyes fiókkal Microsoft-fiók.
1. A bal oldali navigációs panelen válassza ki **a Azure Active Directory** szolgáltatást.
1. Lépjen a **Vállalati alkalmazások lapra,** majd válassza a **Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az Új **alkalmazás lehetőséget.**
1. A Hozzáadás **a katalógusból szakaszban** írja be a **Netskope User Authentication (Netskope-felhasználóhitelesítés)** kifejezéseket a keresőmezőbe.
1. Válassza **a Netskope User Authentication lehetőséget** az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzá lesz adva a bérlőhöz.

## <a name="configure-and-test-azure-ad-sso-for-netskope-user-authentication"></a>Az Azure AD SSO konfigurálása és tesztelése Netskope-felhasználóhitelesítéshez

Konfigurálja és tesztelje az Azure AD SSO-t a Netskope User Authentication használatával egy **B.Simon nevű tesztfelhasználó használatával.** Ahhoz, hogy az SSO működjön, kapcsolati kapcsolatot kell létesítenie egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Netskope User Authenticationben.

Az Azure AD SSO Netskope-felhasználóhitelesítéssel való konfiguráláshoz és teszteléséhez töltse ki a következő építőelemeket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy lehetővé tegye a felhasználók számára a funkció használatát.
    1. **[Azure AD-tesztfelhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének tesztelése a B.Simon használatával.
    1. **[Rendelje hozzá az Azure AD-tesztfelhasználót](#assign-the-azure-ad-test-user)** – ezzel engedélyezheti a B.Simon számára az Azure AD egyszeri bejelentkezés használatát.
1. **[A Netskope User Authentication SSO konfigurálása](#configure-netskope-user-authentication-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. Hozza létre a **[Netskope User Authentication](#create-netskope-user-authentication-test-user)** tesztfelhasználót, hogy a B.Simon megfelelője legyen a Netskope User Authenticationben, amely a felhasználó Azure AD-reprezentációjával van összekapcsolva.
1. **[Az SSO tesztelése](#test-sso)** – annak ellenőrzéséhez, hogy működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez a Azure Portal.

1. A **Netskope** Azure Portal alkalmazásintegrációs oldalán keresse meg  a Kezelés szakaszt, és válassza az **egyszeri bejelentkezés lehetőséget.**
1. Az Egyszeri **bejelentkezési módszer** kiválasztása lapon válassza az **SAML lehetőséget.**
1. Az Egyszeri **bejelentkezés beállítása SAML-sel** lapon kattintson az SamL-alapkonfiguráció ceruza ikonjára a beállítások szerkesztéséhez. 

   ![SamL alapszintű konfigurációjának szerkesztése](common/edit-urls.png)

1. Az **SAML-konfiguráció** alapszintű szakaszában, ha az alkalmazást **idP** által kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    a. Az Azonosító **szövegmezőbe** írjon be egy URL-címet a következő mintával: `https://<tenantname>.goskope.com/<customer entered string>`

    b. A Válasz **URL-cím** szövegmezőbe írjon be egy URL-címet a következő mintával: `https://<tenantname>.goskope.com/nsauth/saml2/http-post/<customer entered string>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval és válasz-URL-lel. Ezeket az értékeket az oktatóanyag későbbi leckéiben ismertetni fogjuk.

1. Kattintson **a További URL-címek beállítása lehetőségre,** és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban szeretné konfigurálni:

    A **Bejelentkezési URL-cím** szövegmezőbe írjon be egy URL-címet a következő mintával:  `https://<tenantname>.goskope.com`

    > [!NOTE]
    > A Bejelentkezési URL-cím értékei nem valósak. Frissítse a bejelentkezési URL-címet a tényleges bejelentkezési URL-címre. Lépjen [kapcsolatba a Netskope User Authentication-ügyfél támogatási csapatával](mailto:support@netskope.com) a bejelentkezési URL-cím értékének lekértértért. Az **SAML** alapkonfiguráció szakaszában látható mintákra is hivatkozhat a Azure Portal.

1. A **Set up Single Sign-On with SAML** (Egyszeri bejelentkezés beállítása SAML-tanúsítványsal) lapon, az **SAML signing Certificate (SAML-aláíró** tanúsítvány) szakaszban kattintson a **Download** (Letöltés) elemre az összevonási metaadatok **XML-fájljának** letöltéséhez a megadott beállításokból igény szerint, majd mentse a számítógépre.

    ![A Tanúsítvány letöltése hivatkozás](common/metadataxml.png)

1. A **Netskope-felhasználóhitelesítés** beállítása szakaszban másolja ki a követelménynek megfelelő URL-címet/URL-címeket.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy B.Simon nevű tesztfelhasználót fog létrehozni a Azure Portal területen.

1. A bal oldali panelen válassza Azure Portal lehetőséget, **Azure Active Directory** **a Felhasználók** lehetőséget, majd válassza a Minden **felhasználó lehetőséget.**
1. Válassza **az Új felhasználó** lehetőséget a képernyő tetején.
1. A Felhasználó **tulajdonságai** között kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév **mezőbe írja** be a következőt: username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket. 
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezheti a B.Simon számára az Azure-beli egyszeri bejelentkezés használatát. Ehhez hozzáférést biztosít a Netskope User Authentication szolgáltatáshoz.

1. A Azure Portal válassza a **Vállalati alkalmazások,** majd a **Minden alkalmazás lehetőséget.**
1. Az alkalmazások listájában válassza a **Netskope User Authentication lehetőséget.**
1. Az alkalmazás áttekintő oldalán keresse meg a Kezelés **szakaszt,** és válassza a **Felhasználók és csoportok lehetőséget.**
1. Válassza **a Felhasználó hozzáadása** lehetőséget, majd a Hozzárendelés hozzáadása **párbeszédpanelen válassza a** Felhasználók és **csoportok** lehetőséget.
1. A Felhasználók **és csoportok** párbeszédpanelen válassza a **B.Simon**  lehetőséget a Felhasználók listában, majd kattintson a képernyő alján található Kijelölés gombra.
1. Ha azt várja, hogy egy szerepkör hozzá lesz rendelve a felhasználókhoz, kiválaszthatja **azt a Szerepkör kiválasztása** legördülő menüből. Ha nincs beállítva szerepkör ehhez az alkalmazáshoz, az "Alapértelmezett hozzáférés" szerepkör van kiválasztva.
1. A Hozzárendelés **hozzáadása párbeszédpanelen** kattintson a Hozzárendelés **gombra.**

## <a name="configure-netskope-user-authentication-sso"></a>A Netskope User Authentication SSO konfigurálása

1. Nyisson meg egy új lapot a böngészőben, és jelentkezzen be a Netskope User Authentication vállalati webhelyre rendszergazdaként.

1. Kattintson **az Aktív platform fülre.**

    ![Képernyőkép a Beállítások menüből kiválasztott aktív platformról.](./media/netskope-user-authentication-tutorial/user-1.png)

1. Görgessen le **a FORWARD PROXY (TOVÁBBÍTÁSI PROXY) elemre,** és válassza az **SAML lehetőséget.**

    ![Képernyőkép az Aktív platformról kiválasztott SAML-ről.](./media/netskope-user-authentication-tutorial/configuration.png)

1. Az **SAML-beállítások lapon** hajtsa végre a következő lépéseket:

    ![Képernyőkép az SAML-beállításokról, ahol megadhatja a leírt értékeket.](./media/netskope-user-authentication-tutorial/configure-copyurls.png)

    a. Másolja **ki az SAML-entitásazonosító** értékét, és illessze be az **SamL-konfiguráció** alapszintű szakasz Azonosító szövegmezőjbe a Azure Portal. 

    b. Másolja **az SAML ACS URL-cím** értékét, és illessze be a válasz **URL-cím** szövegmezőjébe a következő szakaszban: Alapszintű **SAML-konfiguráció** Azure Portal.

1. Kattintson **az ADD ACCOUNT (FIÓK HOZZÁADÁSA) elemre.**

    ![Képernyőkép az SAML panelen kiválasztott FIÓK HOZZÁADÁSA elemről.](./media/netskope-user-authentication-tutorial/add-account.png)

1. Az **SAML-fiók hozzáadása lapon** hajtsa végre a következő lépéseket:

    ![Képernyőkép az SAML-fiók hozzáadásáról, ahol megadhatja a leírt értékeket.](./media/netskope-user-authentication-tutorial/configure-settings.png)

    a. A **NÉV szövegmezőben** adja meg az Azure AD-hez hasonló nevet.

    b. Az **IDP URL (Azonosító** URL-címe) szövegmezőbe illessze be a Bejelentkezési **URL-cím** értéket, amelyet kimásott a Azure Portal.

    c. Az **IDP ENTITY ID (Azonosítóazonosító)** szövegmezőbe illessze be az **Azure AD Identifier (Azure AD-azonosító)** értéket, amelyet a Azure Portal.

    d. Nyissa meg a letöltött metaadatfájlt a Jegyzettömbben, másolja a tartalmát a vágólapra, majd illessze be az **IDP CERTIFICATE (IDP-TANÚSÍTVÁNY)** szövegmezőbe.

    e. Kattintson a **SAVE (MENTÉS) gombra.**

### <a name="create-netskope-user-authentication-test-user"></a>Netskope User Authentication tesztfelhasználó létrehozása

1. Nyisson meg egy új lapot a böngészőben, és jelentkezzen be rendszergazdaként a Netskope User Authentication vállalati webhelyre.

1. Kattintson a **Beállítások lapra** a bal oldali navigációs panelen.

    ![A Kiválasztott beállítás képernyőképe.](./media/netskope-user-authentication-tutorial/configuration-settings.png)

1. Kattintson **az Aktív platform fülre.**

    ![Képernyőkép a Beállítások között kiválasztott Aktív platformról.](./media/netskope-user-authentication-tutorial/user-1.png)

1. Kattintson **a Felhasználók fülre.**

    ![Képernyőkép az Aktív platformról kiválasztott felhasználókról.](./media/netskope-user-authentication-tutorial/add-user.png)

1. Kattintson **a FELHASZNÁLÓK HOZZÁADÁSA elemre.**

    ![Képernyőkép a Felhasználók párbeszédpanelről, ahol kiválaszthatja a FELHASZNÁLÓK HOZZÁADÁSA lehetőséget.](./media/netskope-user-authentication-tutorial/user-add.png)

1. Adja meg a hozzáadni kívánt felhasználó e-mail-címét, majd kattintson az **ADD (HOZZÁADÁS) gombra.**

    ![Képernyőkép a Felhasználók hozzáadása mezőről, ahol megadhatja a felhasználók listáját.](./media/netskope-user-authentication-tutorial/add-user-popup.png)

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az alábbi beállításokkal teszteli az Azure AD egyszeri bejelentkezési konfigurációját. 

#### <a name="sp-initiated"></a>Sp kezdeményezve:

* Kattintson az **Alkalmazás tesztelése elemre a** Azure Portal. Ez átirányítja a Netskope User Authentication Bejelentkezési URL-címre, ahol elindíthatja a bejelentkezési folyamatot.  

* Lépjen közvetlenül a Netskope User Authentication bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>Az internetszolgáltató által kezdeményezett:

* Kattintson az **Alkalmazás tesztelése a** Azure Portal lehetőségre, és automatikusan be kell jelentkezve lennie a Netskope User Authenticationbe, amelyhez beállította az SSO-t. 

A Microsoft Saját alkalmazások bármilyen módban tesztelheti az alkalmazást. Amikor a Saját alkalmazások Netskope User Authentication csempéjére kattint, a rendszer sp módban konfigurálva átirányítja az alkalmazás bejelentkezési oldalára a bejelentkezési folyamat kezdeményezéséhez, és ha idP módban van konfigurálva, automatikusan be kell jelentkeznie arra a Netskope felhasználói hitelesítésre, amelyhez az SSO-t beállította. További információ a Saját alkalmazások: [Bevezetés a](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)Saját alkalmazások.

## <a name="next-steps"></a>Következő lépések

A Netskope User Authentication konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlés a feltételes hozzáféréstől terjed ki. [Ismerje meg, hogyan kényszeríthető ki a munkamenet-vezérlés a Microsoft Cloud App Security.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)
