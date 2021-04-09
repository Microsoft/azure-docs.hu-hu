---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a EasySSO for torkolatánál | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a EasySSO között a torkolatánál.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: 325f6ad7d9685fac17e17b28c4ffbe31b1245cca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98734529"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-confluence"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a EasySSO for torkolatánál

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a EasySSO a Azure Active Directory (Azure AD) összefolyásánál. Ha integrálja az EasySSO-t az Azure AD-nal való összevonáshoz, a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér a torkolatánál.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az Azure AD-fiókjával való összefolyásánál.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* EasySSO a torkolatánál egyszeri bejelentkezés (SSO) engedélyezett előfizetéséhez.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A EasySSO for torkolatánál támogatja az **SP és a identitásszolgáltató** által kezdeményezett SSO-t
* A EasySSO for torkolatánál a felhasználók üzembe helyezésének **időpontját is** támogatja

## <a name="adding-easysso-for-confluence-from-the-gallery"></a>EasySSO hozzáadása a gyűjteményhez való összefolyásánál

A EasySSO az Azure AD-be való összefolyásánál való integrálásának konfigurálásához hozzá kell adnia a EasySSO a gyűjteményből a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **EasySSO for torkolatánál** kifejezést a keresőmezőbe.
1. Válassza ki a **EasySSO az eredmények panelen való összefolyásánál** , majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-easysso-for-confluence"></a>Azure AD SSO konfigurálása és tesztelése a EasySSO for torkolatánál

Konfigurálja és tesztelje az Azure AD SSO-t az EasySSO for torkolatánál egy **B. Simon** nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a EasySSO for torkolatánál.

Az Azure AD SSO és a EasySSO közötti összefolyásánál konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[EasySSO konfigurálása a TORKOLATÁNÁL egyszeri](#configure-easysso-for-confluence-sso)** bejelentkezéshez – az alkalmazás oldalának egyszeri bejelentkezési beállításainak konfigurálása.
    1. **[EasySSO létrehozása az összefolyásánál tesztelési felhasználóhoz](#create-easysso-for-confluence-test-user)** – ha a "B. Simon" párja van a EasySSO-ben a felhasználó Azure ad-képviseletéhez kapcsolódó összefolyásánál.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **EasySSO for torkolatánál** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<server-base-url>/plugins/servlet/easysso/saml`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<server-base-url>/plugins/servlet/easysso/saml`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<server-base-url>/login.jsp`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Ha kétségei vannak, lépjen kapcsolatba a [EasySSO támogatási csapatával](mailto:support@techtime.co.nz) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az EasySSO for torkolatánál alkalmazás megadott formátumban várja az SAML-kikötéseket, így egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül az EasySSO for torkolatánál alkalmazás néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.
    
    | Name | Forrás attribútum|
    | ---------------| --------- |
    | urn: OID: 0.9.2342.19200300.100.1.1 | User. userPrincipalName |
    | urn: OID: 0.9.2342.19200300.100.1.3 | User. mail |
    | urn: OID: 2.16.840.1.113730.3.1.241 | felhasználó. DisplayName |
    | urn: OID: 2.5.4.4 | felhasználó. vezetéknév |
    | urn: OID: 2.5.4.42 | User. givenName |
    
    Abban az esetben, ha az Azure AD-felhasználók **sAMAccountName** konfigurálva van, le kell képeznie az **urn: OID: 0.9.2342.19200300.100.1.1** alakzatot a **sAMAccountName** attribútumra.
    
1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a tanúsítványok **letöltése** a **tanúsítványhoz (Base64)** vagy az **összevonási metaadatok XML-** beállításai elemre, és mentse a számítógépére a vagy az összes beállítást. Később szüksége lesz rá a torkolatánál EasySSO konfigurálásához.

    ![A tanúsítvány letöltési hivatkozása](./media/easysso-for-confluence-tutorial/certificate.png)
    
    Ha azt tervezi, hogy a EasySSO-t manuálisan kívánja végrehajtani a tanúsítvány használatával, a **bejelentkezési URL-címet** és az **Azure ad-azonosítót** is át kell másolnia az alábbi szakaszból, és el kell mentenie azokat a számítógépen.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a EasySSO for torkolatánál.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a EasySSO lehetőséget a **torkolatánál**.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-easysso-for-confluence-sso"></a>EasySSO konfigurálása a torkolati egyszeri bejelentkezéshez

1. A EasySSO for torkolatánál való konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **EasySSO beállítása a torkolatánál** lehetőségre, majd a EasySSO for torkolatánál alkalmazásra irányítja. Itt adja meg a rendszergazdai hitelesítő adatokat, hogy bejelentkezzen a EasySSO for torkolatánál. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-9-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

1. Ha manuálisan szeretné beállítani a EasySSO-t, jelentkezzen be a Atlassian-összevonási példányba rendszergazdai jogosultságokkal, és navigáljon az **Alkalmazások kezelése** szakaszhoz. 

    ![Alkalmazások kezelése](./media/easysso-for-confluence-tutorial/confluence-admin-1.png)

2. A bal oldalon keresse meg a **EasySSO** , és kattintson rá. Ezután kattintson a **Konfigurálás** gombra.

    ![Egyszerű egyszeri bejelentkezés](./media/easysso-for-confluence-tutorial/confluence-admin-2.png)

3. Válassza az **SAML** lehetőséget. Ekkor megjelenik az SAML konfigurációs szakasza.

    ![SAML](./media/easysso-for-confluence-tutorial/confluence-admin-3.png)

4. Válassza a fenti **tanúsítványok** fület, és a következő képernyő jelenik meg: 

    ![Metaadatok URL-címe](./media/easysso-for-confluence-tutorial/confluence-admin-4.png)

5. Most keresse meg az **Azure ad SSO** -konfiguráció korábbi lépéseiben mentett **tanúsítvány (Base64)** vagy **metaadat-fájlt** . A folytatáshoz a következő lehetőségek állnak rendelkezésre:

    a. Használja a számítógép helyi fájljába letöltött alkalmazás-összevonási **metaadat-fájlt** . Válassza a Radio **feltöltése** gombot, és kövesse a fájl feltöltése párbeszédpanelt, amely az operációs rendszerre jellemző.

    **OR**

    b. Nyissa meg az alkalmazás-összevonási **metaadatokat tartalmazó fájlt** , és tekintse meg a fájl tartalmát (bármilyen egyszerű szövegszerkesztőben), és másolja a vágólapra. Válassza a **beviteli** beállítás lehetőséget, majd illessze be a vágólap tartalmát a szövegmezőbe.
 
    **OR**

    c. Teljesen manuális konfiguráció. Az alkalmazás-összevonási **tanúsítvány (Base64)** megnyitásával tekintse meg a fájl tartalmát (bármilyen egyszerű szövegszerkesztőben), és másolja a vágólapra. Illessze be a **identitásszolgáltató jogkivonat-aláíró tanúsítványok** szövegmezőbe. Ezután navigáljon az **általános** lapra, és töltse ki a **kötési URL-címet** és az entitás- **azonosító** mezőket a **bejelentkezési URL-cím** és a korábban mentett **Azure ad-azonosító** megfelelő értékeivel.
 
6. Kattintson a lap alján található **Save (Mentés** ) gombra. Ekkor megjelenik a metaadatok vagy a tanúsítványfájl tartalmának elemzése a konfigurációs mezőkbe. A EasySSO for torkolatánál konfiguráció befejeződött.

7. A legjobb tesztelési élmény érdekében keresse meg **& a Feel (nézet** ) fület, és ellenőrizze az **SAML bejelentkezési gombját** . Ezzel a lehetőséggel külön gomb jelenik meg a torkolatánál a bejelentkező bejelentkezési képernyőn, hogy az Azure AD SAML-integráció teljes körűen tesztelve legyen. Ezt a gombot bekapcsolhatja, és beállíthatja az elhelyezését, színét és fordítását is az éles üzemmódra.

    ![Look & Feel](./media/easysso-for-confluence-tutorial/confluence-admin-5.png)

    > [!NOTE]
    > Ha bármilyen problémája van, forduljon a [EasySSO támogatási csapatához](mailto:support@techtime.co.nz).

### <a name="create-easysso-for-confluence-test-user"></a>EasySSO létrehozása az összefolyásánál tesztelő felhasználó számára

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a torkolatánál. A EasySSO for torkolatánál az igény szerinti felhasználói üzembe helyezést is támogatja, ami alapértelmezés szerint **le van tiltva** . A felhasználók üzembe helyezésének engedélyezéséhez explicit módon ellenőriznie kell, hogy a EasySSO beépülő modul konfigurációjának általános szakaszában be van-e **kapcsolva a felhasználó létrehozása a sikeres bejelentkezés** lehetőségnél. Ha egy felhasználó még nem létezik a torkolatánál, a hitelesítés után létrejön egy újat.

Ha azonban nem kívánja engedélyezni az automatikus felhasználó-kiépítés használatát a felhasználó első bejelentkezésekor, a felhasználóknak léteznie kell a háttérbeli felhasználói könyvtárakban, amelyek az összefolyásánál példányt használják, például LDAP vagy Atlassian Crowd.

![Felhasználók átadása](./media/easysso-for-confluence-tutorial/confluence-admin-6.png)

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

### <a name="idp-initiated-workflow"></a>Identitásszolgáltató által kezdeményezett munkafolyamat

Ebben a szakaszban a saját alkalmazások használatával tesztelheti az Azure AD egyszeri bejelentkezési konfigurációját.

Ha a saját alkalmazások EasySSO az összefolyásánál csempére kattint, akkor automatikusan be kell jelentkeznie arra a összefolyásánál-példányra, amelyhez be szeretné állítani az SSO-t. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

### <a name="sp-initiated-workflow"></a>Az SP által kezdeményezett munkafolyamat

Ebben a szakaszban tesztelheti az Azure AD egyszeri bejelentkezési konfigurációját a összefolyásánál **SAML bejelentkezési** gomb használatával.

![Felhasználói SAML-bejelentkezés](./media/easysso-for-confluence-tutorial/confluence-admin-7.png)

Ez a forgatókönyv feltételezi, hogy engedélyezte az **SAML-bejelentkezési gomb** megjelenését a EasySSO-konfiguráció lapjának **megjelenés & működés** lapján (lásd fent). A meglévő munkamenetek zavarásának elkerüléséhez nyissa meg az összefolyásánál bejelentkezési URL-címét böngésző inkognitóban módban. Kattintson az **SAML-bejelentkezési** gombra, és a rendszer átirányítja az Azure ad felhasználói hitelesítési folyamatára. A sikeres befejezést követően a rendszer átirányítja az összefolyásánál lévő példányra az SAML-n keresztül hitelesített felhasználóként.

Az Azure AD-ből való átirányítást követően lehetséges, hogy a következő képernyővel találkozhat:

![EasySSO-meghibásodási képernyő](./media/easysso-for-confluence-tutorial/confluence-admin-8.png)

Ebben az esetben az [oldalon található utasításokat]( https://techtime.co.nz/display/TECHTIME/EasySSO+How+to+get+the+logs#EasySSOHowtogetthelogs-RETRIEVINGTHELOGS) követve hozzáférhet a **Atlassian-Confluence. log** fájlhoz. A hiba részleteit a EasySSO-hiba oldalon található hivatkozási azonosító fogja tartalmazni.

Ha problémák merülnek fel a naplóüzenetek kivonatolásával kapcsolatban, lépjen kapcsolatba a [EasySSO támogatási csapatával](mailto:support@techtime.co.nz).

## <a name="next-steps"></a>Következő lépések

Ha a EasySSO-t a torkolatánál konfigurálja, akkor kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).