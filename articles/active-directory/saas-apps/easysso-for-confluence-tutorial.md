---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrálása az EasySSO-val a confluence | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory és az EasySSO for Confluence között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/13/2021
ms.author: jeedes
ms.openlocfilehash: ef121112bb4ff3ff6a297677a5c713f642cb3b78
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519537"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-confluence"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrálása az EasySSO-val a confluence-hez

Ez az oktatóanyag bemutatja, hogyan integrálhatja az EasySSO for Confluence-t a Azure Active Directory (Azure AD) használatával. Az EasySSO for Confluence és az Azure AD integrálása után a következőt teszi:

* Vezérlés az Azure AD-ban, aki hozzáféréssel rendelkezik a Confluence-hoz.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezve jelentkeznek be az Azure AD-fiókjukkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésekhez a következő elemekre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes fiókot [is kaphat.](https://azure.microsoft.com/free/)
* EasySSO for Confluence single sign-on (SSO) enabled subscription (EasySSO for Confluence single sign-on, SSO) kompatibilis előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD SSO-t.

* A Confluence-hez használható EasySSO támogatja az sp és az **idP által** kezdeményezett SSO-t.
* A Confluence-hez használható EasySSO támogatja az időben való felhasználóátadást. 

## <a name="add-easysso-for-confluence-from-the-gallery"></a>EasySSO for Confluence hozzáadása a katalógusból

Az EasySSO for Confluence Azure AD-be való integrálásának konfigurálához hozzá kell adni az EasySSO for Confluence-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal vagy személyes fiókkal Microsoft-fiók.
1. A bal oldali navigációs panelen válassza ki **a Azure Active Directory** szolgáltatást.
1. Lépjen a Vállalati **alkalmazások lapra,** majd válassza a **Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az Új **alkalmazás lehetőséget.**
1. A Hozzáadás **a katalógusból szakaszban** írja be a keresőmezőbe az **EasySSO for Confluence (EasySSO for Confluence)** parancsot.
1. Válassza **az EasySSO for Confluence** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzá lesz adva a bérlőhöz.

## <a name="configure-and-test-azure-ad-sso-for-easysso-for-confluence"></a>Az Azure AD SSO konfigurálása és tesztelése az EasySSO-hoz a confluence-hoz

Konfigurálja és tesztelje az Azure AD SSO-t az EasySSO-val a Confluence-hoz egy **B.Simon nevű tesztfelhasználó használatával.** Ahhoz, hogy az SSO működjön, kapcsolati kapcsolatot kell létesítenie egy Azure AD-felhasználó és a kapcsolódó felhasználó között az EasySSO for Confluence szolgáltatásban.

Ha az Azure AD SSO-t az EasySSO for Confluence használatával konfigurálja és teszteli, hajtsa végre a következő lépéseket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy lehetővé tegye a felhasználók számára a funkció használatát.
    1. **[Azure AD-tesztfelhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének tesztelése a B.Simon használatával.
    1. **[Rendelje hozzá az Azure AD-tesztfelhasználót](#assign-the-azure-ad-test-user)** – ezzel engedélyezheti a B.Simon számára az Azure AD egyszeri bejelentkezés használatát.
1. **[Konfigurálja az EasySSO-t a Confluence SSO-hoz](#configure-easysso-for-confluence-sso)** – az egyszeri bejelentkezési beállítások alkalmazásoldalon való konfigurálásához.
    1. **[Hozzon létre EasySSO for Confluence](#create-easysso-for-confluence-test-user)** tesztfelhasználót, hogy a B.Simon megfelelője legyen az EasySSO for Confluence-hez, amely a felhasználó Azure AD-reprezentációjával van összekapcsolva.
1. **[Az SSO tesztelése](#test-sso)** – annak ellenőrzéséhez, hogy működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez a Azure Portal.

1. A Azure Portal **EasySSO for Confluence** alkalmazásintegráció oldalán keresse meg a **Kezelés** szakaszt, és válassza az **egyszeri bejelentkezés lehetőséget.**
1. Az Egyszeri **bejelentkezési módszer** kiválasztása lapon válassza az **SAML lehetőséget.**
1. Az Egyszeri **bejelentkezés beállítása SAML-sel** lapon kattintson az SamL-alapkonfiguráció ceruza ikonjára a beállítások szerkesztéséhez. 

   ![SamL alapszintű konfigurációjának szerkesztése](common/edit-urls.png)

1. Az **SAML-konfiguráció** alapszintű szakaszában, ha az alkalmazást **idP** által kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    a. Az Azonosító **szövegmezőbe** írjon be egy URL-címet a következő mintával: `https://<server-base-url>/plugins/servlet/easysso/saml`

    b. A Válasz **URL-cím** szövegmezőbe írjon be egy URL-címet az alábbi mintát használva: `https://<server-base-url>/plugins/servlet/easysso/saml`

1. Kattintson **a További URL-címek beállítása** lehetőségre, és hajtsa  végre a következő lépést, ha sp által kezdeményezett módban szeretné konfigurálni az alkalmazást:

    A **Bejelentkezési URL-cím** szövegmezőbe írjon be egy URL-címet az alábbi mintát használva:  `https://<server-base-url>/login.jsp`

    > [!NOTE]
    > Ezek az értékek nem valódiak. Frissítse ezeket az értékeket a tényleges Azonosító, Válasz URL-cím és Bejelentkezési URL-cím értékkel. Ha kétségei vannak, vegye fel a kapcsolatot az [EasySSO](mailto:support@techtime.co.nz) támogatási csapatával. A minta az **SAML-konfiguráció** alapszintű szakaszában látható mintákat is Azure Portal.

1. Az EasySSO for Confluence alkalmazás egy adott formátumban várja az SAML helyességi feltételeket, amelyhez egyéni attribútumleképezéseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjában. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül az EasySSO for Confluence alkalmazás arra számít, hogy néhány további attribútumot is vissza kell adni az SAML-válaszba, amelyek alább láthatók. Ezek az attribútumok is előre ki vannak töltve, de a követelményeknek megfelelően áttekintheti őket.
    
    | Name | Forrásattribútum|
    | ---------------| --------- |
    | urn:oid:0.9.2342.19200300.100.1.1 | user.userprincipalname |
    | urn:oid:0.9.2342.19200300.100.1.3 | user.mail |
    | urn:oid:2.16.840.1.113730.3.1.241 | user.displayname |
    | urn:oid:2.5.4.4 | user.surname |
    | urn:oid:2.5.4.42 | user.givenname |
    
    Ha az Azure AD-felhasználók esetében az **sAMAccountName** van konfigurálva, akkor az **urn:oid:0.9.2342.19200300.100.1.1** attribútumot kell leképezni az **sAMAccountName attribútumra.**
    
1. Az Egyszeri bejelentkezés beállítása  SAML-sel lap **SAML-aláíró** tanúsítvány szakaszában kattintson a Tanúsítvány **(Base64)** vagy az Összevonási metaadatok **XML-beállításaira** mutató hivatkozások letöltése elemre, és mentse a számítógépet vagy az összeset.  Később szüksége lesz rá a Confluence EasySSO konfigurálásánál.

    ![A Tanúsítvány letöltése hivatkozás](./media/easysso-for-confluence-tutorial/certificate.png)
    
    Ha azt tervezi, hogy manuálisan konfigurálja az EasySSO for Confluence-t a tanúsítvánnyal, a bejelentkezési **URL-címet** és az **Azure AD-azonosítót** is ki kell másolnia az alábbi szakaszból, és mentenie kell azokat a számítógépre.

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

Ebben a szakaszban engedélyezheti a B.Simon számára az Azure-beli egyszeri bejelentkezés használatát. Ehhez hozzáférést biztosít az EasySSO for Confluence szolgáltatáshoz.

1. A Azure Portal válassza a **Vállalati alkalmazások,** majd a **Minden alkalmazás lehetőséget.**
1. Az alkalmazások listájában válassza az **EasySSO lehetőséget a Confluence számára.**
1. Az alkalmazás áttekintő oldalán keresse meg a Kezelés **szakaszt,** és válassza a **Felhasználók és csoportok lehetőséget.**
1. Válassza **a Felhasználó hozzáadása** lehetőséget, majd a Hozzárendelés hozzáadása **párbeszédpanelen válassza a** Felhasználók és **csoportok** lehetőséget.
1. A Felhasználók **és csoportok** párbeszédpanelen válassza a **B.Simon**  lehetőséget a Felhasználók listában, majd kattintson a képernyő alján található Kijelölés gombra.
1. Ha azt várja, hogy egy szerepkör hozzá lesz rendelve a felhasználókhoz, kiválaszthatja **azt a Szerepkör kiválasztása** legördülő menüből. Ha nincs beállítva szerepkör ehhez az alkalmazáshoz, az "Alapértelmezett hozzáférés" szerepkör van kiválasztva.
1. A Hozzárendelés **hozzáadása párbeszédpanelen** kattintson a Hozzárendelés **gombra.**

## <a name="configure-easysso-for-confluence-sso"></a>Az EasySSO konfigurálása a confluence SSO-nak

1. Az EasySSO for Confluence konfigurációjának automatizálása  érdekében telepítenie kell egy biztonságos Saját alkalmazások böngészőbővítményt a Bővítmény **telepítése gombra kattintva.**

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson az **EasySSO beállítása a Confluence-hez** lehetőségre, amely az EasySSO for Confluence alkalmazáshoz irányítja. Itt adja meg a rendszergazdai hitelesítő adatokat az EasySSO for Confluence szolgáltatásba való bejelentkezéshez. A böngészőbővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3–9. lépést.

    ![Beállítási konfiguráció](common/setup-sso.png)

1. Ha manuálisan szeretné beállítani az EasySSO for Confluence-t, jelentkezzen be az Atlassian Confluence-példányba rendszergazdai jogosultságokkal, és lépjen az **Alkalmazások kezelése szakaszra.** 

    ![Alkalmazások kezelése](./media/easysso-for-confluence-tutorial/confluence-admin-1.png)

2. A bal oldalon keresse meg **az EasySSO et,** és kattintson rá. Ezután kattintson **a Konfigurálás** gombra.

    ![Egyszerű SSO](./media/easysso-for-confluence-tutorial/confluence-admin-2.png)

3. Válassza **az SAML** lehetőséget. Ezzel az SAML-konfiguráció szakaszba fog vinni.

    ![SAML](./media/easysso-for-confluence-tutorial/confluence-admin-3.png)

4. Válassza **a felül** található Tanúsítványok lapot, és a következő képernyő jelenik meg: 

    ![Metaadatok URL-címe](./media/easysso-for-confluence-tutorial/confluence-admin-4.png)

5. Most keresse meg az Azure  **AD SSO-konfiguráció** korábbi lépései során mentett Tanúsítvány **(Base64)** vagy Metaadatfájl adatokat. A folytatáshoz a következő lehetőségek állnak rendelkezésre:

    a. Használja a  helyi fájlba letöltött alkalmazás-összevonási metaadatfájlt a számítógépen. Válassza **a Feltöltés** választógombot, és kövesse az operációs rendszerének megfelelő Fájl feltöltése párbeszédpanelt

    **OR**

    b. Nyissa meg az Alkalmazás-összevonási **metaadatfájlt** a fájl tartalmának (egy egyszerű szövegszerkesztőben) való megnyitásához és a vágólapra másoláshoz. Válassza **a Bevitel lehetőséget,** és illessze be a vágólap tartalmát a szövegmezőbe.
 
    **OR**

    c. Teljes mértékben manuális konfigurálás. Nyissa meg az Alkalmazás-összevonási **tanúsítványt (Base64),** hogy lássa a fájl tartalmát (egy egyszerű szövegszerkesztőben), és másolja a vágólapra. Illessze be az **IdP Token Signing Certificates (Azonosító jogkivonat-aláíró tanúsítványai)** szövegmezőbe. Ezután lépjen az **Általános lapra,** és töltse ki a **POST-kötés URL-címe** és az Entitásazonosító mezőket a bejelentkezési  **URL-cím** és a korábban mentett Azure **AD-azonosító** megfelelő értékeivel.
 
6. Kattintson **a mentés** gombra a lap alján. Látni fogja, hogy a metaadat- vagy tanúsítványfájlok tartalma a konfigurációs mezőkben van elemezve. Az EasySSO for Confluence konfigurálása befejeződött.

7. A legjobb tesztelési élmény érdekében lépjen a **Look & Feel (Megjelenés)** lapra, és jelölje be az **SAML Login Button (SAML-bejelentkezés** gomb) lehetőséget. Ez külön gombot engedélyez a Confluence bejelentkezési képernyőn kifejezetten az Azure AD SAML-integráció teljes tesztelésére. Ezt a gombot beállítva hagyhatja, és konfigurálhatja annak elhelyezését, színét és fordítását éles módban is.

    ![Look & Feel](./media/easysso-for-confluence-tutorial/confluence-admin-5.png)

    > [!NOTE]
    > Ha bármilyen probléma lép fel, forduljon [az EasySSO támogatási csapatához.](mailto:support@techtime.co.nz)

### <a name="create-easysso-for-confluence-test-user"></a>EasySSO létrehozása a Confluence tesztfelhasználóhoz

Ebben a szakaszban egy Britta Simon nevű felhasználót hozunk létre a Confluence-ban. A Confluence-hez használható EasySSO támogatja az időponthoz időben való felhasználóátadást, amely alapértelmezés szerint **le** van tiltva. A felhasználókiépítés engedélyezéséhez explicit módon be kell jelölje a **Felhasználó** létrehozása sikeres bejelentkezés esetén beállítást az EasySSO beépülő modul konfigurációjának Általános szakaszában. Ha egy felhasználó még nem létezik a Confluence-ban, a hitelesítés után létrejön egy új.

Ha azonban nem szeretné engedélyezni az automatikus felhasználóátadást a felhasználó első bejelentkezésén, a felhasználóknak léteznie kell a Confluence-példány által használt háttérfelhasználói címtárakban, például LDAP- vagy Atlassian Crowd-címtárakban.

![Felhasználók átadása](./media/easysso-for-confluence-tutorial/confluence-admin-6.png)

## <a name="test-sso"></a>Az SSO tesztelése 

### <a name="idp-initiated-workflow"></a>Az idP által kezdeményezett munkafolyamat

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a Saját alkalmazások.

Amikor az egyszerű bejelentkezéshez (Confluence) csempére kattint a Saját alkalmazások, automatikusan be kell jelentkezve lennie arra a Confluence-példányra, amelyhez beállította az SSO-t. További információ a Saját alkalmazások: [Bevezetés a](../user-help/my-apps-portal-end-user-access.md)Saját alkalmazások.

### <a name="sp-initiated-workflow"></a>SP által kezdeményezett munkafolyamat

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a Confluence **SAML Login (Konfluence SAML-bejelentkezés) gombbal.**

![Felhasználói SAML-bejelentkezés](./media/easysso-for-confluence-tutorial/confluence-admin-7.png)

Ez a forgatókönyv feltételezi, hogy engedélyezte az **SAML-bejelentkezés** gombot **a Confluence** EasySSO konfigurációs oldal Look & Feel lapján (lásd fent). Nyissa meg a Confluence bejelentkezési URL-címét böngésző inkognitó módban, hogy elkerülje a meglévő munkamenetek interferenciát. Kattintson **az SAML Login (SAML-bejelentkezés)** gombra, és a rendszer átirányítja az Azure AD felhasználói hitelesítési folyamatára. A sikeres befejeződés után a rendszer visszairányítsa a Confluence-példányra hitelesített felhasználóként az SAML-en keresztül.

Előfordulhat, hogy a következő képernyő jelenik meg az Azure AD-ból való visszairányítás után

![EasySSO-hiba képernyő](./media/easysso-for-confluence-tutorial/confluence-admin-8.png)

Ebben az esetben kövesse []( https://techtime.co.nz/display/TECHTIME/EasySSO+How+to+get+the+logs#EasySSOHowtogetthelogs-RETRIEVINGTHELOGS) az oldalon található utasításokat az **atlassian-confluence.log fájlhoz** való hozzáféréshez. A hiba részletei az EasySSO hibalapon található hivatkozásazonosító alapján lesznek elérhetők.

Ha bármilyen probléma lép fel a naplóüzenetek kivonatolásával, forduljon [az EasySSO támogatási csapatához.](mailto:support@techtime.co.nz)

## <a name="next-steps"></a>Következő lépések

Miután konfigurálta az EasySSO-t a Confluence-hez, kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlés a feltételes hozzáféréstől terjed ki. [Ismerje meg, hogyan kényszeríthető ki a munkamenet-vezérlés a Microsoft Cloud App Security.](/cloud-app-security/proxy-deployment-any-app)