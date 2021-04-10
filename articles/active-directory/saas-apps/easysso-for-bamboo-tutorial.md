---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a EasySSO for Bamboo szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Bamboo Azure Active Directory és EasySSO között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/21/2020
ms.author: jeedes
ms.openlocfilehash: 3d784979876857e94c1e09ffc5b7bef11c0a0d04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735967"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-bamboo"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Bamboo EasySSO

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a EasySSO a Bamboo-ba a Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a EasySSO-t a Bambuszhoz, a következőket teheti:

* A Bambuszhoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Bamboo-ba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* EasySSO a Bamboo egyszeri bejelentkezés (SSO) számára engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Bamboo EasySSO az **SP és a identitásszolgáltató** által kezdeményezett SSO-t támogatja
* A Bamboo EasySSO a felhasználók üzembe helyezésének **időpontját is** támogatja

## <a name="adding-easysso-for-bamboo-from-the-gallery"></a>A Bamboo EasySSO hozzáadása a gyűjteményből

A Bamboo EasySSO az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a EasySSO a Bamboo-hoz a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a keresőmezőbe az **EasySSO for Bamboo** kifejezést.
1. Válassza az **EasySSO for Bamboo** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-easysso-for-bamboo"></a>Az Azure AD SSO konfigurálása és tesztelése a Bamboo EasySSO

Konfigurálja és tesztelje az Azure AD SSO-t az EasySSO for Bamboo használatával egy **B. Simon** nevű tesztelési felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a EasySSO for Bamboo szolgáltatásban.

Az Azure AD SSO a Bamboo EasySSO való konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[Bamboo SSO EasySSO konfigurálása](#configure-easysso-for-bamboo-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre EasySSO a Bamboo test User-hez](#create-easysso-for-bamboo-test-user)** , hogy a "B. Simon" párja legyen a EasySSO for Bamboo szolgáltatásban, amely a felhasználó Azure ad-képviseletéhez van társítva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a Bamboo Application Integration **EasySSO** lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
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

1. A EasySSO for Bamboo alkalmazás megadott formátumban várja az SAML-kijelentéseket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül az EasySSO for Bamboo alkalmazása néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.
    
    | Name | Forrás attribútum |
    | ---------------| --------- |
    | urn: OID: 0.9.2342.19200300.100.1.1 | User. userPrincipalName |
    | urn: OID: 0.9.2342.19200300.100.1.3 | User. mail |
    | urn: OID: 2.16.840.1.113730.3.1.241 | felhasználó. DisplayName |
    | urn: OID: 2.5.4.4 | felhasználó. vezetéknév |
    | urn: OID: 2.5.4.42 | User. givenName |
    
    Abban az esetben, ha az Azure AD-felhasználók **sAMAccountName** konfigurálva van, le kell képeznie az **urn: OID: 0.9.2342.19200300.100.1.1** alakzatot a **sAMAccountName** attribútumra.
    
1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a tanúsítványok **letöltése** a **tanúsítványhoz (Base64)** vagy az **összevonási metaadatok XML-** beállításai elemre, és mentse a számítógépére a vagy az összes beállítást. Később szüksége lesz rá a bambusz-EasySSO konfigurálásához.

    ![A tanúsítvány letöltési hivatkozása](./media/easysso-for-bamboo-tutorial/certificate.png)
    
    Ha a bambusz-konfigurációhoz manuálisan szeretné elvégezni a EasySSO a tanúsítvány használatával, a **bejelentkezési URL-címet** és az **Azure ad-azonosítót** is át kell másolnia az alábbi szakaszból, és el kell mentenie azokat a számítógépen.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a EasySSO a Bamboo-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a EasySSO lehetőséget a **Bamboo** elemnél.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-easysso-for-bamboo-sso"></a>EasySSO konfigurálása a Bamboo SSO-hoz

1. A konfigurációnak a nagyításban való automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **Nagyítás beállítása** lehetőségre a nagyítási alkalmazáshoz. Itt adja meg a rendszergazdai hitelesítő adatokat a nagyításhoz való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-10-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a nagyítást, egy másik böngészőablakban jelentkezzen be a nagyítású vállalati webhelyre rendszergazdaként.

1. Navigáljon az **Alkalmazások kezelése** szakaszra. 

    ![Alkalmazások kezelése](./media/easysso-for-bamboo-tutorial/bamboo-admin-1.png)

2. A bal oldalon keresse meg a **EasySSO** , és kattintson rá.

    ![Egyszerű egyszeri bejelentkezés](./media/easysso-for-bamboo-tutorial/bamboo-admin-2.png)

3. Válassza az **SAML** lehetőséget. Ekkor megjelenik az SAML konfigurációs szakasza.

    ![SAML](./media/easysso-for-bamboo-tutorial/bamboo-admin-3.png)

4. Válassza a fenti **tanúsítványok** fület, és a következő képernyő jelenik meg:

    ![Metaadatok URL-címe](./media/easysso-for-bamboo-tutorial/bamboo-admin-4.png)

5. Most keresse meg az **Azure ad SSO** -konfiguráció korábbi lépéseiben mentett **tanúsítvány (Base64)** vagy **metaadat-fájlt** . A folytatáshoz a következő lehetőségek állnak rendelkezésre:

    a. Használja a számítógép helyi fájljába letöltött alkalmazás-összevonási **metaadat-fájlt** . Válassza a Radio **feltöltése** gombot, és kövesse a fájl feltöltése párbeszédpanelt, amely az operációs rendszerre jellemző.

    **OR**

    b. Nyissa meg az alkalmazás-összevonási **metaadatokat tartalmazó fájlt** , és tekintse meg a fájl tartalmát (bármilyen egyszerű szövegszerkesztőben), és másolja a vágólapra. Válassza a **beviteli** beállítás lehetőséget, majd illessze be a vágólap tartalmát a szövegmezőbe.
 
    **OR**

    c.  Teljesen manuális konfiguráció. Az alkalmazás-összevonási **tanúsítvány (Base64)** megnyitásával tekintse meg a fájl tartalmát (bármilyen egyszerű szövegszerkesztőben), és másolja a vágólapra. Illessze be a **identitásszolgáltató jogkivonat-aláíró tanúsítványok** szövegmezőbe. Ezután navigáljon az **általános** lapra, és töltse ki a **kötési URL-címet** és az entitás- **azonosító** mezőket a **bejelentkezési URL-cím** és a korábban mentett **Azure ad-azonosító** megfelelő értékeivel.
 
6. Kattintson a lap alján található **Save (Mentés** ) gombra. Ekkor megjelenik a metaadatok vagy a tanúsítványfájl tartalmának elemzése a konfigurációs mezőkbe. A bambusz-konfiguráció EasySSO befejeződött.

7. A legjobb tesztelési élmény érdekében keresse meg **& a Feel (nézet** ) fület, és ellenőrizze az **SAML bejelentkezési gombját** . Ezzel a lehetőséggel külön gomb jelenik meg a bambusz-bejelentkezési képernyőn, amely az Azure AD SAML-integráció teljes körű tesztelését teszi lehetővé. Ezt a gombot meghagyhatja, és beállíthatja az elhelyezést, a színt és a fordítást az éles üzemmódra is.

    ![Look & Feel](./media/easysso-for-bamboo-tutorial/bamboo-admin-5.png)

    > [!NOTE]
    > Ha bármilyen problémája van, forduljon a [EasySSO támogatási csapatához](mailto:support@techtime.co.nz).

### <a name="create-easysso-for-bamboo-test-user"></a>EasySSO létrehozása a Bamboo test userhez

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a Bamboo-ban. A Bamboo EasySSO támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint **le van tiltva** . A felhasználók üzembe helyezésének engedélyezéséhez explicit módon ellenőriznie kell, hogy a EasySSO beépülő modul konfigurációjának általános szakaszában be van-e **kapcsolva a felhasználó létrehozása a sikeres bejelentkezés** lehetőségnél. Ha egy felhasználó még nem létezik a Bamboo-ban, akkor a hitelesítés után létrejön egy újat.

Ha azonban nem kívánja engedélyezni az automatikus felhasználó-kiépítés használatát a felhasználó első bejelentkezésekor, a felhasználóknak léteznie kell a háttérbeli felhasználói könyvtárakban a Bamboo-példány, például az LDAP vagy a Atlassian Crowd használatával.

![Felhasználók átadása](./media/easysso-for-bamboo-tutorial/bamboo-admin-6.png)

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

### <a name="idp-initiated-workflow"></a>Identitásszolgáltató által kezdeményezett munkafolyamat

Ebben a szakaszban a saját alkalmazások használatával tesztelheti az Azure AD egyszeri bejelentkezési konfigurációját.

Ha a saját alkalmazások EasySSO a bambusz csempére kattint, akkor automatikusan be kell jelentkeznie a Bamboo-példányba, amelyhez be kell állítania az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

### <a name="sp-initiated-workflow"></a>Az SP által kezdeményezett munkafolyamat

Ebben a szakaszban a Bamboo **SAML bejelentkezési** gomb használatával tesztelheti az Azure ad egyszeri bejelentkezési konfigurációját.

![Felhasználói SAML-bejelentkezés](./media/easysso-for-bamboo-tutorial/bamboo-admin-7.png)

Ez a forgatókönyv feltételezi, hogy engedélyezte az **SAML bejelentkezési gomb** megjelenését a Bamboo EasySSO konfigurációs lapjának **& Feel** (megtekintés) lapján (lásd fent). A meglévő munkamenetek zavarásának elkerülése érdekében nyissa meg a bambusz bejelentkezési URL-címét a böngésző inkognitóban módjában. Kattintson az **SAML-bejelentkezési** gombra, és a rendszer átirányítja az Azure ad felhasználói hitelesítési folyamatára. A sikeres befejezést követően a rendszer átirányítja a Bamboo-példányra az SAML-n keresztül hitelesített felhasználóként.

Az Azure AD-ből való átirányítást követően lehetséges, hogy a következő képernyővel találkozhat:

![EasySSO-meghibásodási képernyő](./media/easysso-for-bamboo-tutorial/bamboo-admin-8.png)

Ebben az esetben az [oldalon található utasításokat]( https://techtime.co.nz/display/TECHTIME/EasySSO+How+to+get+the+logs#EasySSOHowtogetthelogs-RETRIEVINGTHELOGS) követve hozzáférhet a **Atlassian-Bamboo. log** fájlhoz. A hiba részleteit a EasySSO-hiba oldalon található hivatkozási azonosító fogja tartalmazni.

Ha problémák merülnek fel a naplóüzenetek kivonatolásával kapcsolatban, lépjen kapcsolatba a [EasySSO támogatási csapatával](mailto:support@techtime.co.nz).

## <a name="next-steps"></a>Következő lépések

Miután konfigurálta a EasySSO a Bamboo-hoz, kikényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).