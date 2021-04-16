---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrációja az desknets NEO-| Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory ÉS az desknetes NEO között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: ef3e024e1b704cc9f8711d9ca78ee916acf72680
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107580764"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-desknets-neo"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrálása az desknetES EGYSZERŰ hálózatával

Ez az oktatóanyag bemutatja, hogyan integrálhatja az desknetESES-ét a Azure Active Directory (Azure AD) használatával. Az desknetES ÉS az Azure AD integrálása után a következő funkciókat használhatja:

* Az Azure AD vezérlése, aki hozzáféréssel rendelkezik az asztalhálózati NEO-hoz.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezve jelentkeznek be az desknetES FIÓKJÁba az Azure AD-fiókjukkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésekhez a következő elemekre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes fiókot [is kaphat.](https://azure.microsoft.com/free/)
* az desknetES EGYSZERI bejelentkezéssel (SSO) kompatibilis előfizetésével.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD SSO-t.

* az desknetesUNK támogatja az **SP által kezdeményezett** SSO-t.

## <a name="adding-desknets-neo-from-the-gallery"></a>DesknetESES-ének hozzáadása a katalógusból

Az desknetES ÉS az Azure AD integrációjának konfigurálásához hozzá kell adni az desknetNEK a katalógusból a felügyelt SaaS-alkalmazások listájához az desknetES-t.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal vagy személyes fiókkal Microsoft-fiók.
1. A bal oldali navigációs panelen válassza ki **a Azure Active Directory** szolgáltatást.
1. Lépjen a Vállalati **alkalmazások lapra,** majd válassza a **Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az Új **alkalmazás lehetőséget.**
1. A Hozzáadás **a katalógusból szakaszba** írja be a keresőmezőbe az **desknetESIVE** kifejezését.
1. Válassza **ki az desknetES-et** az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzá lesz adva a bérlőhöz.


## <a name="configure-and-test-azure-ad-sso-for-desknets-neo"></a>Az Azure AD SSO konfigurálása és tesztelése az desknetES FIÓKJÁhoz

Konfigurálja és tesztelje az Azure AD SSO-t a desknetESES-sel egy **B.Simon nevű tesztfelhasználóval.** Ahhoz, hogy az SSO működjön, kapcsolati kapcsolatot kell létesítenie egy Azure AD-felhasználó és a kapcsolódó felhasználó között az desknetES FIÓKJÁban.

Ha az Azure AD SSO-t a desknetES AD SSO-val konfigurálja és teszteli, hajtsa végre a következő lépéseket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy a felhasználók használják ezt a funkciót.
    1. **[Azure AD-tesztfelhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének tesztelése a B.Simon használatával.
    1. **[Rendelje hozzá az Azure AD-tesztfelhasználót](#assign-the-azure-ad-test-user)** – ezzel engedélyezheti a B.Simon számára az Azure AD egyszeri bejelentkezés használatát.
1. **[Az desknetES SSO](#configure-desknets-neo-sso)** konfigurálása – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozza létre az desknetES-tesztfelhasználót,](#create-desknets-neo-test-user)** hogy a B.Simon megfelelője legyen az desknetES AD-hez, amely a felhasználó Azure AD-reprezentációjával van összekapcsolva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzéséhez, hogy működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez a Azure Portal.

1. A Azure Portal a **desknetESES** alkalmazásintegrációs oldalán keresse meg a **Kezelés** szakaszt, és válassza az **egyszeri bejelentkezés lehetőséget.**
1. Az Egyszeri **bejelentkezési módszer** kiválasztása lapon válassza az **SAML lehetőséget.**
1. A Set **up single sign-on with SAML** (Egyszeri bejelentkezés beállítása SAML-sel) oldalon kattintson az SamL-alapkonfiguráció ceruza ikonjára a beállítások szerkesztéséhez. 

   ![SamL alapszintű konfigurációjának szerkesztése](common/edit-urls.png)

1. Az **SAML-alapkonfiguráció szakaszban** adja meg a következő mezők értékeit:

    a. Az Azonosító **(Entitásazonosító) szövegmezőbe** írjon be egy URL-címet a következő mintával: `https://<CUSTOMER_NAME>.dn-cloud.com`

    b. A Válasz **URL-cím** szövegmezőbe írjon be egy URL-címet a következő mintával: `https://<CUSTOMER_NAME>.dn-cloud.com/cgi-bin/dneo/zsaml.cgi`

    c. A **Bejelentkezési URL-cím** szövegmezőbe írjon be egy URL-címet a következő mintával: `https://<CUSTOMER_NAME>.dn-cloud.com/cgi-bin/dneo/dneo.cgi`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címével. Ezeket [az értékeket az desknetESESES-ügyféltámogatási](mailto:cloudsupport@desknets.com) csapattól lehet lekérte. Az **SAML** alapkonfiguráció szakaszában látható mintákra is hivatkozhat a Azure Portal.

1. Az Egyszeri bejelentkezés beállítása **SAML-sel** lap **SAML** aláíró tanúsítvány szakaszában keresse meg a  **Tanúsítvány (Base64)** et, majd válassza a Letöltés lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A Tanúsítvány letöltése hivatkozás](common/certificatebase64.png)

1. A **Set up desknet's NEO (A desknetES beállítása)** szakaszban másolja ki a követelménynek megfelelő URL-cím(eke)t.

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

Ebben a szakaszban engedélyezni fogja a B.Simon számára az Azure-beli egyszeri bejelentkezés használatát. Ehhez hozzáférést ad az desknetNEK a NEO-hoz.

1. A Azure Portal válassza a **Vállalati alkalmazások,** majd a **Minden alkalmazás lehetőséget.**
1. Az alkalmazások listájában válassza ki **az desknetESet.**
1. Az alkalmazás áttekintő oldalán keresse meg a Kezelés **szakaszt,** és válassza a **Felhasználók és csoportok lehetőséget.**
1. Válassza **a Felhasználó hozzáadása** lehetőséget, majd a Hozzárendelés hozzáadása **párbeszédpanelen válassza** a Felhasználók és **csoportok** lehetőséget.
1. A Felhasználók **és csoportok** párbeszédpanelen válassza a **B.Simon**  lehetőséget a Felhasználók listában, majd kattintson a képernyő alján található Kijelölés gombra.
1. Ha azt várja, hogy egy szerepkör hozzá lesz rendelve a felhasználókhoz, kiválaszthatja azt **a Szerepkör kiválasztása** legördülő menüből. Ha nincs beállítva szerepkör ehhez az alkalmazáshoz, az "Alapértelmezett hozzáférés" szerepkör lesz kiválasztva.
1. A Hozzárendelés **hozzáadása párbeszédpanelen** kattintson a Hozzárendelés **gombra.**

## <a name="configure-desknets-neo-sso"></a>Az desknetES SSO konfigurálása

1. Jelentkezzen be rendszergazdaként az desknetES céges webhelyére.

1. A menüben kattintson az **SAML-hitelesítési hivatkozás beállításainak ikonjára.**

    ![Képernyőkép az SAML-hitelesítési hivatkozás beállításairól.](./media/desknets-neo-tutorial/saml-authentication-icon.png)

1. A Common **settings (Általános beállítások) lapon kattintson** a use **from** SAML Authentication Collaboration (HASZNÁLAT) elemre az SAML Authentication Collaboration (SAML-hitelesítés együttműködése) lapon.

    ![Az SAML-hitelesítés használatának képernyőképe.](./media/desknets-neo-tutorial/saml-authentication-use.png)

1. Hajtsa végre az alábbi lépéseket az **SAML-hitelesítési hivatkozás beállításai szakaszban.**

    ![Képernyőkép az SAML-hitelesítési hivatkozás beállításai szakaszról.](./media/desknets-neo-tutorial/saml-authentication.png)

    a. Az **Access URL (Url-cím** elérése) szövegmezőbe illessze be a bejelentkezési **URL-cím** értékét, amelyet a Azure Portal.

    b. Az **SP-entitásazonosító szövegmezőbe** illessze be az **Azonosító** értéket, amelyet kimásott a Azure Portal.

    c. Kattintson **a Fájl kiválasztása** elemre a letöltött Tanúsítvány **(Base64)** fájl feltöltéséhez Azure Portal **x.509-tanúsítvány** szövegmezőbe.

    d. Kattintson a **módosítás gombra.**

### <a name="create-desknets-neo-test-user"></a>Az desknetES tesztfelhasználó létrehozása

1. Jelentkezzen be rendszergazdaként az desknetES céges webhelyére.

1. A **menüben kattintson** a **Rendszergazdai beállítások ikonra.**

    ![A Rendszergazdai beállítások képernyőképe.](./media/desknets-neo-tutorial/administrator-settings.png)

1. Kattintson **a beállítások ikonra,** és válassza **a Felhasználókezelés** lehetőséget az **Egyéni beállítások között.**

    ![Képernyőkép a Felhasználókezelési beállításokról.](./media/desknets-neo-tutorial/user-management.png)

1. Kattintson **a Felhasználói adatok létrehozása elemre.**

    ![Képernyőkép a Felhasználói adatok gombról.](./media/desknets-neo-tutorial/create-new-user.png)

1. Töltse ki a kötelező mezőket az alábbi oldalon, és kattintson a **létrehozás elemre.**

    ![Képernyőkép a Felhasználó létrehozása szakaszról.](./media/desknets-neo-tutorial/create-new-user-2.png)

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az alábbi beállításokkal teszteli az Azure AD egyszeri bejelentkezési konfigurációját. 

* Kattintson az **Alkalmazás tesztelése elemre a** Azure Portal. Ez átirányítja az desknetESES bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül az desknetESES bejelentkezési URL-címére, és onnan indítsa el a bejelentkezési folyamatot.

* Használhatja a Microsoft Saját alkalmazások. Amikor a Saját alkalmazások az asztalhálózatNAK az ASZTALCSempére kattint, a rendszer átirányítja az asztalhálózatNAK a NEO bejelentkezési URL-címére. További információ a Saját alkalmazások: [Bevezetés a](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)Saját alkalmazások.


## <a name="next-steps"></a>Következő lépések

Az desknetES konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlés a feltételes hozzáféréstől terjed ki. [Ismerje meg, hogyan kényszeríthető ki a munkamenet-vezérlés a Microsoft Cloud App Security.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)


