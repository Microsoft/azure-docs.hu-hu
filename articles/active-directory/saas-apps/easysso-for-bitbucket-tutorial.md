---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrálása a BitBuckethez használható EasySSO-val | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory és a BitBuckethez használható EasySSO között.
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
ms.openlocfilehash: 56f2eea9dc485c69b6070fda6e9519887f7b30cb
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519581"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-bitbucket"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrálása a BitBuckethez használható EasySSO-val

Ez az oktatóanyag bemutatja, hogyan integrálhatja a BitBuckethez használható EasySSO-t a Azure Active Directory (Azure AD) használatával. Ha az EasySSO for BitBucketet integrálja az Azure AD-val, a következőt teszi:

* Az Azure AD vezérlése, aki hozzáfér a BitBuckethez használható EasySSO-hoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve jelentkeznek be a BitBuckethez használható EasySSO-ba az Azure AD-fiókjukkal.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésekhez a következőkre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes fiókot [is kaphat.](https://azure.microsoft.com/free/)
* Egyszeri bejelentkezésre (SSO) engedélyezett EasySSO for BitBucket-előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD SSO-t.

* A BitBuckethez használható EasySSO támogatja az SP által kezdeményezett és az idP által kezdeményezett SSO-t.
* A BitBuckethez használható EasySSO támogatja az "időben elérhető" felhasználókiépítést.

## <a name="add-easysso-for-bitbucket-from-the-gallery"></a>EasySSO for BitBucket hozzáadása a katalógusból

Ahhoz, hogy a BitBuckethez használható EasySSO integrálható legyen az Azure AD-be, hozzá kell adni az EasySSO for BitBucketet a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal vagy személyes fiókkal Microsoft-fiók.
1. A bal oldali navigációs panelen válassza ki **a Azure Active Directory** szolgáltatást.
1. Válassza a **Vállalati alkalmazások lehetőséget,** majd válassza a **Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az Új **alkalmazás lehetőséget.**
1. A Hozzáadás **a katalógusból szakaszban** írja be a keresőmezőbe a **Következőt: EasySSO for BitBucket.**
1. Az **eredmények közül válassza az EasySSO lehetőséget a BitBuckethez,** majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzá lesz adva a bérlőhöz.

## <a name="configure-and-test-azure-ad-sso-for-easysso-for-bitbucket"></a>Az Azure AD SSO for EasySSO for BitBucket konfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t a BitBuckethez használható EasySSO-val egy **B.Simon nevű tesztfelhasználóval.** Ahhoz, hogy az SSO működjön, összekapcsolt kapcsolatot kell létrehoznia egy Azure AD-felhasználó és a kapcsolódó felhasználó között az EasySSO for BitBucketben.

Ha az Azure AD SSO-t az EasySSO for BitBucket használatával konfigurálja és teszteli, hajtsa végre a következő lépéseket:

1. [Konfigurálja úgy az Azure AD SSO-t,](#configure-azure-ad-sso) hogy a felhasználók használják ezt a funkciót.
    1. [Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezésének tesztelésére a B.Simon használatával.
    1. [Rendelje hozzá az Azure AD-tesztfelhasználót,](#assign-the-azure-ad-test-user) hogy engedélyezze a B.Simon számára az Azure AD egyszeri bejelentkezés használatát.
1. [Konfigurálja az EasySSO-t a BitBucket SSO-hoz](#configure-easysso-for-bitbucket-sso) az egyszeri bejelentkezés beállításainak konfigurálához az alkalmazásoldalon.
    1. [Hozzon létre egy EasySSO-t a BitBucket](#create-an-easysso-for-bitbucket-test-user) tesztfelhasználóhoz, hogy a B.Simon megfelelője legyen az EasySSO for BitBucketben, amely a felhasználó Azure AD-reprezentációjával van összekapcsolva.
1. [Az SSO tesztelése](#test-sso) annak ellenőrzéséhez, hogy működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez a Azure Portal.

1. A Azure Portal **EasySSO for BitBucket** alkalmazásintegrációs oldalán keresse meg a **Kezelés** szakaszt. Válassza **az egyszeri bejelentkezés lehetőséget.**
1. Az Egyszeri **bejelentkezési módszer** kiválasztása lapon válassza az **SAML lehetőséget.**
1. Az Egyszeri **bejelentkezés beállítása SAML-sel** lapon kattintson az SamL-alapkonfiguráció ceruza ikonjára a beállítások szerkesztéséhez. 

   ![Képernyőkép a Set up Single Sign-On with SAML (SamL-alkalmazás beállítása) lapról, kiemelt ceruza ikonnal](common/edit-urls.png)

1. Ha az **SAML-konfiguráció** alapszintű szakaszában az alkalmazást **idP** által kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    a. Az Azonosító **szövegmezőbe** írjon be egy URL-címet, amely a következő mintát használja: `https://<server-base-url>/plugins/servlet/easysso/saml`

    b. A Válasz **URL-cím** szövegmezőbe írjon be egy URL-címet, amely a következő mintát használja: `https://<server-base-url>/plugins/servlet/easysso/saml`

1. Válassza **a További URL-címek beállítása** lehetőséget, és ha sp által kezdeményezett módban szeretné konfigurálni az alkalmazást, tegye a következő lépést: 

    - A **Bejelentkezési URL-cím** szövegmezőbe írjon be egy URL-címet, amely a következő mintát használja: `https://<server-base-url>/login.jsp`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címével. Ha kétségei vannak, vegye fel a kapcsolatot az [EasySSO](mailto:support@techtime.co.nz) támogatási csapatával. Az **SAML** alapkonfiguráció szakaszában látható mintákra is hivatkozhat a Azure Portal.

1. A BitBuckethez készült EasySSO az SAML helyességi feltételeket egy adott formátumban várja, amelyhez hozzá kell adni egyéni attribútumleképezéseket az SAML-jogkivonat attribútumainak konfigurációjában. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![Az alapértelmezett attribútumok képernyőképe](common/default-attributes.png)

1. Az EasySSO for BitBucket alkalmazás arra számít, hogy néhány további attribútumot is vissza kell adni az SAML-válasznak. Az alábbi táblázatban ezek láthatóak. Ezek az attribútumok is előre ki vannak töltve, de a követelményeknek megfelelően áttekintheti őket.
    
    | Name | Forrásattribútum|
    | ---------------| --------- |
    | urn:oid:0.9.2342.19200300.100.1.1 | user.userprincipalname |
    | urn:oid:0.9.2342.19200300.100.1.3 | user.mail |
    | urn:oid:2.16.840.1.113730.3.1.241 | user.displayname |
    | urn:oid:2.5.4.4 | user.surname |
    | urn:oid:2.5.4.42 | user.givenname |
    
    Ha az Azure AD-felhasználókhoz **sAMAccountName** van konfigurálva, akkor az **urn:oid:0.9.2342.19200300.100.1.1** attribútumot kell leképezni az **sAMAccountName attribútumra.**
    
1. Az Egyszeri bejelentkezés beállítása **SAML-sel** lap **SAML** aláíró tanúsítvány szakaszában válassza a Tanúsítvány **(Base64)** vagy az Összevonási metaadatok XML-beállítások letöltési **hivatkozását.** Mentse vagy mindkettőt a számítógépre. Később szüksége lesz rá a BitBucket EasySSO konfiguráláshoz.

    ![Képernyőkép az SAML aláíró tanúsítvány szakaszról, kiemelt letöltési hivatkozásokkal](./media/easysso-for-bitbucket-tutorial/certificate.png)
    
    Ha azt tervezi, hogy manuálisan konfigurálja az EasySSO for BitBucketet egy tanúsítvánnyal, a bejelentkezési **URL-címet** és az **Azure AD-azonosítót** is ki kell másolnia, és mentenie kell azokat a számítógépre.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy B.Simon nevű tesztfelhasználót fog létrehozni a Azure Portal.

1. A bal oldali panelen válassza Azure Portal Felhasználók **Azure Active Directory**  >    >  **lehetőséget.**
1. Válassza **az Új felhasználó** lehetőséget a képernyő tetején.
1. A Felhasználó **tulajdonságai** között kövesse az alábbi lépéseket:
   1. A **Név alatt** adja meg a `B.Simon` nevet.
   1. Felhasználónévként **adja** meg a következőt: username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszót.
   1. Válassza a **Létrehozás** lehetőséget.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezheti a B.Simon számára az Azure-beli egyszeri bejelentkezés használatát. Ehhez hozzáférést biztosít az EasySSO for BitBucket szolgáltatáshoz.

1. A Azure Portal válassza a **Vállalati alkalmazások**  >  **Minden alkalmazás lehetőséget.**
1. Az alkalmazások listájában válassza az **EasySSO lehetőséget a BitBucket esetében.**
1. Az alkalmazás áttekintő oldalán keresse meg a Kezelés **szakaszt,** és válassza a **Felhasználók és csoportok lehetőséget.**
1. Válassza a **Felhasználó hozzáadása** elemet. A Hozzárendelés **hozzáadása párbeszédpanelen** válassza a **Felhasználók és csoportok lehetőséget.**
1. A Felhasználók **és csoportok** párbeszédpanelen válassza a  **B.Simon** lehetőséget  a Felhasználók listában, majd válassza a Képernyő alján található Kijelölés lehetőséget.
1. Ha azt várja, hogy egy szerepkör hozzá lesz rendelve a felhasználókhoz, kiválaszthatja **azt a Szerepkör kiválasztása** legördülő menüből. Ha nincs beállítva szerepkör ehhez az alkalmazáshoz, az "Alapértelmezett hozzáférés" szerepkör van kiválasztva.
1. A Hozzárendelés **hozzáadása párbeszédpanelen** válassza a Hozzárendelés **lehetőséget.**

## <a name="configure-easysso-for-bitbucket-sso"></a>Az EasySSO konfigurálása a BitBucket SSO-hez

1. A Zoomban a konfiguráció automatizálása érdekében telepítenie kell **Saját alkalmazások Biztonságos** bejelentkezés böngészőbővítményt a Bővítmény **telepítése gombra kattintva.**

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **Nagyítás** beállítása elemre, amely a Nagyítás alkalmazáshoz irányítja. Itt adja meg a rendszergazdai hitelesítő adatokat a Zoomba való bejelentkezéshez. A böngészőbővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3–10. lépést.

    ![Beállítási konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a Nagyítást, egy másik webböngészőablakban jelentkezzen be a Zoom céges webhelyre rendszergazdaként.

1. Ugrás az Adminisztráció **szakaszra.**

    ![A BitBucket-példány képernyőképe, kiemelt fogaskerék ikonnal](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-1.png)
1. Keresse meg és válassza az **EasySSO lehetőséget.**

    ![Képernyőkép az Egyszerű SSO lehetőségről](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-2.png)

1. Válassza az **SAML lehetőséget.** Ez az SAML-konfiguráció szakaszra viszi.

    ![Képernyőkép az EasySSO admin lapról, kiemelt SAML-sel](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-3.png)

1. Válassza a **Tanúsítványok** lapot, és megjelenik a következő képernyő:

    ![Képernyőkép a Tanúsítványok lapról, különböző kiemelt beállításokkal](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-4.png)

1. Keresse meg az oktatóanyag  előző szakaszában mentett tanúsítványt **(Base64)** vagy metaadatfájlt. A következő módokon folytathatja:

    - Használja az **alkalmazás-összevonási metaadatfájlt,** amely a helyi fájlba van letöltve a számítógépen. Válassza a **Feltöltés választógombot,** és kövesse az operációs rendszerének megfelelő elérési utat.

    - Nyissa meg az Alkalmazás-összevonási **metaadatfájlt** a fájl tartalmának bármely egyszerű szövegszerkesztőben való megnyitásához. Másolja a vágólapra. Válassza **a Bemenet** lehetőséget, és illessze be a vágólap tartalmát a szövegmezőbe.
 
    - Teljes mértékben manuális konfigurációt kell konfigurálni. Nyissa meg az Alkalmazás-összevonási **tanúsítványt (Base64)** a fájl tartalmának bármely egyszerű szövegszerkesztőben való megnyitásához. Másolja a vágólapra, és illessze be az **IdP Token Signing Certificates (Azonosító jogkivonat-aláíró tanúsítványai)** szövegmezőbe. Ezután lépjen az **Általános** lapra, és  töltse ki a **POST-kötés URL-címe** és az Entitásazonosító mezőket a korábban mentett bejelentkezési **URL-cím** és **Azure AD-azonosító** megfelelő értékeivel.
 
1. A **lap** alján válassza a Mentés lehetőséget. Látni fogja, hogy a metaadatok vagy tanúsítványfájlok tartalma a konfigurációs mezőkben van elemezve. Az EasySSO for BitBucket konfigurálása befejeződött.

1. A konfiguráció tesztelése előtt lépjen a Look & Feel (Felhasználói **&)** lapra, és válassza az **SAML Login Button (SAML-bejelentkezés gomb) lehetőséget.** Ez lehetővé teszi egy külön gomb beállítását a BitBucket bejelentkezési képernyőjén, kifejezetten az Azure AD SAML-integráció teljes tesztelésére. Ezt a gombot beállítva hagyhatja, és konfigurálhatja annak elhelyezését, színét és fordítását is az éles módhoz.

    ![Képernyőkép az SAML Look & Feel lapról, kiemelve az SAML Login (SAML-bejelentkezés) gombot](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-5.png)
    > [!NOTE]
    >Ha bármilyen problémája van, lépjen kapcsolatba [az EasySSO támogatási csapatával.](mailto:support@techtime.co.nz)

### <a name="create-an-easysso-for-bitbucket-test-user"></a>EasySSO létrehozása BitBucket-tesztfelhasználóhoz

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a BitBucketben. A BitBuckethez használható EasySSO támogatja az időponthoz időben való felhasználóátépítést, amely alapértelmezés szerint le van tiltva. Az engedélyezéséhez explicit módon be kell ellenőriznie  a **Felhasználó** létrehozása sikeres bejelentkezés esetén szakaszt az EasySSO beépülő modul konfigurációjának Általános szakaszában. Ha a felhasználó még nem létezik a BitBucketben, a hitelesítés után létrejön egy új.

Ha azonban nem szeretné engedélyezni az automatikus felhasználóátépítést, amikor a felhasználó először jelentkezik be, a felhasználóknak léteznie kell a BitBucket példánya által használt felhasználói könyvtárakban. Ez a könyvtár lehet például LDAP vagy Atlassian Crowd.

![Képernyőkép az EasySSO beépülő modul konfigurációjának Általános szakaszról, kiemelt Create user on successful login (Felhasználó létrehozása sikeres bejelentkezés esetén) beállítással](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-6.png)

## <a name="test-sso"></a>Az SSO tesztelése 

Ebben a szakaszban az alábbi beállításokkal teszteli az Azure AD egyszeri bejelentkezési konfigurációját.

#### <a name="sp-initiated"></a>Sp kezdeményezve:

* Kattintson az **Alkalmazás tesztelése elemre** a Azure Portal. Ez átirányítja az EasySSO for BitBucket bejelentkezési URL-címre, ahol elindíthatja a bejelentkezési folyamatot.

* Lépjen közvetlenül az EasySSO for BitBucket bejelentkezési URL-címhez, és onnan indítsa el a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>Az internetszolgáltató által kezdeményezett:

* Kattintson az **Alkalmazás tesztelése a** Azure Portal lehetőségre, és automatikusan be kell jelentkezve lennie a BitBucket easySSO szolgáltatásba, amelyhez beállította az SSO-t

A Microsoft Saját alkalmazások bármilyen módban tesztelheti az alkalmazást. Ha az Saját alkalmazások-ban az EasySSO for BitBucket csempére kattint, a rendszer átirányítja az alkalmazás bejelentkezési oldalára a bejelentkezési folyamat kezdeményezésére, és ha idP módban van konfigurálva, automatikusan be kell jelentkeznie az EasySSO for BitBucketbe, amelyhez beállította az SSO-t. További információ a Saját alkalmazások: [Bevezetés a](../user-help/my-apps-portal-end-user-access.md)Saját alkalmazások.

## <a name="next-steps"></a>Következő lépések

Miután konfigurálta az EasySSO-t a BitBuckethez, kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlés a feltételes hozzáféréstől terjed ki. [Ismerje meg, hogyan kényszeríthető ki a munkamenet-vezérlés a Microsoft Cloud App Security.](/cloud-app-security/proxy-deployment-any-app)