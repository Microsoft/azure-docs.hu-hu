---
title: 'Oktatóanyag: Azure Active Directory integráció a help Scout segítségével | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a Scout segítségével.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.openlocfilehash: 7c5e8210bc8b805d72149fd2ef3335c1d637a58f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92445239"
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>Oktatóanyag: Azure Active Directory integráció a help Scout segítségével

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a súgót Azure Active Directory (Azure AD) segítségével.
A Súgó felderítő és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben ellenőrizheti, hogy ki férhet hozzá a Felderítőhöz.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek az Azure AD-fiókjával való Scout (egyszeri bejelentkezés) segítségével.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció a Súgó felderítővel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Súgó Scout egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A help Scout támogatja **az SP és a identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést
* A Súgó felderítő **a** felhasználók üzembe helyezését támogatja

## <a name="adding-help-scout-from-the-gallery"></a>Súgó cserkész hozzáadása a katalógusból

A Súgó Felderítőnek az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia egy súgót a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **help Scout** kifejezést a keresőmezőbe.
1. Válassza az eredmények panel **Súgó felderítője** elemét, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést egy **B. Simon** nevű teszt felhasználó alapján konfigurálja és teszteli a súgóban.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség a súgóban.

Az Azure AD egyszeri bejelentkezés a Súgó felderítővel való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[help Scout SSO konfigurálása](#configure-help-scout-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Súgó Scout-felhasználó létrehozása](#create-help-scout-test-user)** – ha a súgóban a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-partneri jogosultsággal rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés súgóval történő konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **Súgó Scout** -alkalmazás integrációja lapon válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

1. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    ![A képernyőfelvételen az alapszintű SAML-konfiguráció látható, ahol megadható az azonosító, a válasz U R L, majd a Mentés elemre.](common/idp-intiated.png)

    a. A súgóban az **azonosító** a **célközönség URI-ja (szolgáltatói entitás azonosítója)** , amely a következővel kezdődik:`urn:`

    b. A **Válasz URL-címe** : a Súgó felderítője **(a fogyasztó szolgáltatás URL-címe)** , a következővel kezdődik:`https://` 

    > [!NOTE]
    > Ezekben az URL-címekben szereplő értékek csak a bemutatóra vonatkoznak. Ezeket az értékeket frissíteni kell a tényleges válasz URL-címéből és azonosítóból. Ezeket az értékeket az **egyszeri bejelentkezés** lapon, a hitelesítés szakaszban találja, amelyet az oktatóanyag későbbi részében talál.

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    ![Képernyőfelvétel: további U R ls beállítása, ahol megadhatja a bejelentkezést az U R L-ben.](common/metadata-upload-additional-signon.png)

    A **bejelentkezési URL** szövegmezőbe írja be az URL-címet a következőként: `https://secure.helpscout.net/members/login/`

1. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Súgó-felderítő beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure ad-azonosító

    c. Kijelentkezési URL-cím

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ennek a szakasznak a célja, hogy létrehozzon egy teszt felhasználót a B. Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **B. Simon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a **B. Simon \@ yourcompanydomain. Extension nevet.**  
    Például: B.Simon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban engedélyezi, hogy a B. Simon az Azure egyszeri bejelentkezés használatára nyújtson hozzáférést a felderítők számára.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd kattintson a **Súgó felderítő** elemre.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Válassza az alkalmazások lista **Súgó felderítő** elemét.

    ![Az alkalmazások listájában szereplő Súgó felderítő hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **B. Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-help-scout-sso"></a>Súgó-felderítő egyszeri bejelentkezés konfigurálása

1. A súgóban található konfiguráció automatizálásához a **bővítmény telepítése** lehetőségre kattintva telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** .

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

1. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **Súgó létrehozása** lehetőségre, majd a Súgó felderítő alkalmazásra irányítja. Itt adja meg a rendszergazdai hitelesítő adatokat a súgóba való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-7-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

1. Ha manuálisan szeretné beállítani a Súgó Felderítőjét, nyisson meg egy új böngészőablakot, és jelentkezzen be a Súgó felderítő céges webhelyére rendszergazdaként, és hajtsa végre a következő lépéseket:

1. Kattintson a **kezelés** elemre a felső menüben, majd válassza a **vállalat** lehetőséget a legördülő menüből.

    ![Képernyőfelvétel: a kezelés menü, amelyet a vállalat kiválasztott.](./media/helpscout-tutorial/settings1.png)

1. A bal oldali navigációs ablaktáblán válassza a **hitelesítés** lehetőséget.

    ![A képernyőképen a kiválasztott hitelesítés látható.](./media/helpscout-tutorial/settings2.png)

1. Ekkor megjelenik az SAML-beállítások szakasz, és hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: az egyetlen Sign-On lap, amelyen a megadott adatokat adja meg.](./media/helpscout-tutorial/settings3.png)

    a. Másolja a **visszaküldési URL-cím (a fogyasztói szolgáltatás URL-címe)** értéket, és illessze be az értéket a Azure Portal **ALAPszintű SAML-konfiguráció** szakaszának **Válasz URL-címe** szövegmezőbe.

    b. Másolja a **célközönség URI-ját (szolgáltatói entitás azonosítója)** , és illessze be az értéket a Azure Portal **ALAPszintű SAML-konfiguráció** szakaszában található **azonosító** szövegmezőbe.

1. Kapcsolja be az **SAML engedélyezése** beállítást, és hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: az egységes Sign-On lapon engedélyezheti az SAML-t, és további információkat adhat hozzá.](./media/helpscout-tutorial/settings4.png)

    a. Az **egyszeres Sign-On URL** szövegmezőben illessze be a **bejelentkezési URL-cím** értékét, amelyet a Azure Portalból másolt.

    b. Kattintson a **tanúsítvány feltöltése** gombra a Azure Portalról letöltött **tanúsítvány (Base64)** feltöltéséhez.

    c. Adja meg a szervezeti e-mail-tartomány (ok) e.x. `contoso.com` az **e-mail tartományok** szövegmezőben. Vesszővel elválaszthat több tartományt is. A Súgó [felderítő bejelentkezési oldalának](https://secure.helpscout.net/members/login/) az adott tartományba bejelentkező súgójában a felhasználó vagy a rendszergazda a hitelesítő adataikkal való hitelesítés érdekében az identitás-szolgáltatóhoz irányítja a felhasználót.

    d. Végül pedig beállíthatja a **kényszerített SAML-bejelentkezést** , ha azt szeretné, hogy a felhasználók csak a metóduson keresztül jelentkezzenek be a Súgóba. Ha továbbra is szeretné, hogy a Súgó felderítő hitelesítő adataival jelentkezzenek be, hagyja kikapcsolva a funkciót. Még ha ez engedélyezve van, a fiók tulajdonosa mindig képes lesz bejelentkezni a fiók jelszavával.

    e. Kattintson a **Mentés** gombra.

### <a name="create-help-scout-test-user"></a>Súgó felderítő tesztelési felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre a Súgó Felderítőben. A help Scout támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a súgóban, a rendszer egy újat hoz létre a hitelesítés után.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Súgó felderítő csempére kattint, automatikusan be kell jelentkeznie arra a Súgó-Felderítőbe, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)

- [Az Azure AD-vel való Súgó kipróbálása](https://aad.portal.azure.com/)