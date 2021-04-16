---
title: 'Oktatóanyag: Azure Active Directory integráció a Form.com | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory és Form.com.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/05/2021
ms.author: jeedes
ms.openlocfilehash: 011f854c7c6885029468c1e93ce60af9a1c06b80
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516063"
---
# <a name="tutorial-azure-active-directory-integration-with-formcom"></a>Oktatóanyag: Azure Active Directory integráció a Form.com

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Form.com az Azure Active Directory (Azure AD) használatával. Az Azure AD Form.com integrálása után a következő funkciókat használhatja:

* Vezérlés az Azure AD-ban, aki hozzáféréssel rendelkezik a Form.com.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve Form.com Azure AD-fiókjukkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésekhez a következő elemekre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes fiókot [is kaphat.](https://azure.microsoft.com/free/)
* Form.com egyszeri bejelentkezést (SSO) engedélyező előfizetést.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD egyszeri bejelentkezését konfigurálja és teszteli tesztkörnyezetben.

* Form.com támogatja az **sp által kezdeményezett** SSO-t.

## <a name="add-formcom-from-the-gallery"></a>Alkalmazás Form.com a katalógusból

Ha konfigurálni Form.com azure aD-be, hozzá kell Form.com a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal vagy személyes fiókkal Microsoft-fiók.
1. A bal oldali navigációs panelen válassza ki **a Azure Active Directory** szolgáltatást.
1. Lépjen a **Vállalati alkalmazások lapra,** majd válassza a **Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az Új **alkalmazás lehetőséget.**
1. A Hozzáadás **a katalógusból szakaszban** írja be a **Form.com** a keresőmezőbe.
1. Válassza **Form.com** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzá lesz adva a bérlőhöz.

## <a name="configure-and-test-azure-ad-sso-for-formcom"></a>Azure AD SSO konfigurálása és tesztelése Form.com

Konfigurálja és tesztelje az Azure AD SSO-t Form.com egy **B.Simon nevű tesztfelhasználóval.** Ahhoz, hogy az SSO működjön, kapcsolati kapcsolatot kell létesítenie egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Form.com.

Ha az Azure AD SSO-t a Form.com konfigurálni és tesztelni, hajtsa végre a következő lépéseket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy lehetővé tegye a felhasználók számára a funkció használatát.
    1. **[Azure AD-tesztfelhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének tesztelése a B.Simon használatával.
    1. **[Rendelje hozzá az Azure AD-tesztfelhasználót](#assign-the-azure-ad-test-user)** – ezzel engedélyezheti a B.Simon számára az Azure AD egyszeri bejelentkezés használatát.
1. **[Az Form.com SSO konfigurálása](#configure-formcom-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon Form.com tesztfelhasználót](#create-formcom-test-user)** , hogy a B.Simon megfelelője legyen a Form.com, amely a felhasználó Azure AD-reprezentációjával van összekapcsolva.
1. **[Az SSO tesztelése](#test-sso)** – annak ellenőrzéséhez, hogy működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez a Azure Portal.

1. A Azure Portal az Form.com **lapon** keresse meg a Kezelés **szakaszt,** és válassza az egyszeri **bejelentkezés lehetőséget.**
1. Az Egyszeri **bejelentkezési módszer** kiválasztása lapon válassza az **SAML lehetőséget.**
1. Az Egyszeri **bejelentkezés beállítása SAML-sel** lapon kattintson az SamL-alapkonfiguráció ceruza ikonjára a beállítások szerkesztéséhez. 

   ![SamL alapszintű konfigurációjának szerkesztése](common/edit-urls.png)

4. Az **SAML-alapkonfiguráció szakaszban** hajtsa végre a következő lépéseket:

    a. A **Bejelentkezési URL-cím** szövegmezőbe írjon be egy URL-címet a következő mintával: `https://<subdomain>.wa-form.com`

    b. Az Azonosító **mezőbe** írjon be egy URL-címet a következő mintával: `https://<subdomain>.form.com`

    c. A Válasz **URL-cím** szövegmezőbe írjon be egy URL-címet az alábbi minták egyikével:
    
    ```http
    https://<subdomain>.wa-form.com/Member/UserAccount/SAML2.action
    https://<subdomain>.form.com/Member/UserAccount/SAML2.action
    ```

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges urlSign-On url-címével, azonosítóval és válasz-URL-címével. Ezeket [az Form.com az ügyféltámogatási](https://form.com/about/company/contact-us/) csapattól. Az **SAML** alapkonfiguráció szakaszában látható mintákra is hivatkozhat a Azure Portal.

5. Az Egyszeres  Sign-On beállítása **SAML-lel** lapon, az **SAML** aláíró tanúsítvány szakaszban kattintson a Letöltés elemre a  tanúsítvány **letöltéséhez (Base64),** majd kattintson a másolás ikonra az alkalmazás összevonási metaadatainak **URL-címének** másolása a megadott beállításokból igény szerint, majd mentse a számítógépre.

    ![A Tanúsítvány letöltése hivatkozás](./media/formcom-tutorial/certificatebase64-url.png)

6. A **Beállítási Form.com** másolja ki a megfelelő URL-cím(eke)t a követelménynek megfelelően.

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

Ebben a szakaszban engedélyezheti a B.Simon számára az Azure-beli egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a Form.com.

1. A Azure Portal válassza a **Vállalati alkalmazások,** majd a **Minden alkalmazás lehetőséget.**
1. Az alkalmazások listájában válassza **a** Form.com.
1. Az alkalmazás áttekintő oldalán keresse meg a Kezelés **szakaszt,** és válassza a **Felhasználók és csoportok lehetőséget.**
1. Válassza **a Felhasználó hozzáadása** lehetőséget, majd a Hozzárendelés hozzáadása **párbeszédpanelen válassza a** Felhasználók és **csoportok** lehetőséget.
1. A Felhasználók **és csoportok** párbeszédpanelen válassza a **B.Simon**  lehetőséget a Felhasználók listában, majd kattintson a képernyő alján található Kijelölés gombra.
1. Ha azt várja, hogy egy szerepkör hozzá lesz rendelve a felhasználókhoz, kiválaszthatja **azt a Szerepkör kiválasztása** legördülő menüből. Ha nincs beállítva szerepkör ehhez az alkalmazáshoz, az "Alapértelmezett hozzáférés" szerepkör van kiválasztva.
1. A Hozzárendelés **hozzáadása párbeszédpanelen** kattintson a Hozzárendelés **gombra.**

## <a name="configure-formcom-sso"></a>Az Form.com SSO konfigurálása

Az egyszeri bejelentkezés Form.com oldalon való konfigurálásához el kell küldenie **a** letöltött **tanúsítványt (Base64),** az alkalmazás összevonási metaadatainak **URL-címét** és a megfelelő másolt URL-címeket a Azure Portal-ről egy Form.com [támogatási csapatnak.](https://form.com/about/company/contact-us/) Ezt a beállítást úgy állítják be, hogy az SAML SSO-kapcsolat megfelelően legyen beállítva mindkét oldalon.

### <a name="create-formcom-test-user"></a>Tesztfelhasználó Form.com létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a Form.com. Működjön együtt [Form.com támogatási csapattal](https://form.com/about/company/contact-us/) a felhasználók hozzáadásához a Form.com platformon. Az egyszeri bejelentkezés használata előtt a felhasználókat létre kell hoznunk és aktiválnunk kell.

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az alábbi beállításokkal teszteli az Azure AD egyszeri bejelentkezési konfigurációját. 

* Kattintson az **Alkalmazás tesztelése elemre a** Azure Portal. Ez átirányítja Form.com bejelentkezési URL-címre, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen a Form.com URL-címére, és onnan indítsa el a bejelentkezési folyamatot.

* Használhatja a Microsoft Saját alkalmazások. Amikor a Form.com csempére Saját alkalmazások, a rendszer átirányítja a Form.com URL-címre. További információ a [Saját alkalmazások: Bevezetés a](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)Saját alkalmazások.

## <a name="next-steps"></a>Következő lépések

Miután konfigurálta a Form.com kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlés a feltételes hozzáféréstől terjed ki. [Ismerje meg, hogyan kényszeríthető a munkamenet-vezérlés a Microsoft Cloud App Security.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)
