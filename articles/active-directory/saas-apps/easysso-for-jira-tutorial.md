---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a EasySSO for JIRA | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a EasySSO között a JIRA számára.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/28/2020
ms.author: jeedes
ms.openlocfilehash: b813f1f77b7bda2627f5275e772a3ee7c9d12834
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92454354"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-jira"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az EasySSO for JIRA

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a EasySSO a JIRA-be a Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a JIRA EasySSO-t, a következőket teheti:

* A JIRA-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a JIRA az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A JIRA egyszeri bejelentkezés (SSO) engedélyezett előfizetéshez tartozó EasySSO.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A EasySSO for JIRA támogatja az **SP és a identitásszolgáltató** által kezdeményezett SSO-t
* A EasySSO for JIRA támogatja a felhasználók üzembe helyezésének **időpontját**
* Miután konfigurálta a EasySSO a JIRA, kikényszerítheti a munkamenet-vezérlést, amely valós időben biztosítja a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlő kiterjeszthető a feltételes hozzáférésből. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-easysso-for-jira-from-the-gallery"></a>EasySSO hozzáadása a JIRA a katalógusból

A JIRA-EasySSO az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a EasySSO a JIRA a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **EasySSO a JIRA** mezőbe a keresőmezőbe.
1. Válassza az **EasySSO lehetőséget a JIRA** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-easysso-for-jira"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a JIRA-hez készült EasySSO

Konfigurálja és tesztelje az Azure AD SSO-t az EasySSO for JIRA használatával a **B. Simon** nevű tesztelési felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a EasySSO for JIRA-ben.

Az Azure AD SSO és a JIRA EasySSO konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[EasySSO konfigurálása a JIRA egyszeri bejelentkezéshez](#configure-easysso-for-jira-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre EasySSO a JIRA-teszt felhasználó számára](#create-easysso-for-jira-test-user)** – a EasySSO-hez tartozó B. Simon-partneri kapcsolattal, amely a felhasználó Azure ad-ábrázolásához van társítva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **EasySSO a JIRA** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
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

1. A EasySSO for JIRA alkalmazás megadott formátumban várja az SAML-kikötéseket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentiek mellett a EasySSO for JIRA alkalmazás néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.
    
    | Name | Forrás attribútum|
    | ---------------| --------- |
    | urn: OID: 0.9.2342.19200300.100.1.1 | User. userPrincipalName |
    | urn: OID: 0.9.2342.19200300.100.1.3 | User. mail |
    | urn: OID: 2.16.840.1.113730.3.1.241 | felhasználó. DisplayName |
    | urn: OID: 2.5.4.4 | felhasználó. vezetéknév |
    | urn: OID: 2.5.4.42 | User. givenName |
    
    Abban az esetben, ha az Azure AD-felhasználók **sAMAccountName** konfigurálva van, le kell képeznie az **urn: OID: 0.9.2342.19200300.100.1.1** alakzatot a **sAMAccountName** attribútumra.
    
1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a tanúsítványok **letöltése** a **tanúsítványhoz (Base64)** vagy az **összevonási metaadatok XML-** beállításai elemre, és mentse a számítógépére a vagy az összes beállítást. Később szüksége lesz rá a JIRA-EasySSO konfigurálásához.

    ![A tanúsítvány letöltési hivatkozása](media/easysso-for-jira-tutorial/azure-ad-SAML-certificate.png)
    
    Ha a tanúsítvány használatával manuálisan szeretné elvégezni a JIRA-konfiguráció EasySSO, a **bejelentkezési URL-címet** és az **Azure ad-azonosítót** is át kell másolnia az alábbi szakaszból, és el kell mentenie azokat a számítógépen.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a EasySSO a JIRA.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a EasySSO lehetőséget a **JIRA** elemnél.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-easysso-for-jira-sso"></a>EasySSO konfigurálása a JIRA SSO-hoz

1. Jelentkezzen be a Atlassian JIRA-példányba rendszergazdai jogosultságokkal, és navigáljon az **Alkalmazások kezelése** szakaszhoz. 

    ![Alkalmazások kezelése](media/easysso-for-jira-tutorial/jira-admin-1.png)

2. A bal oldalon keresse meg a **EasySSO** , és kattintson rá.

    ![Egyszerű egyszeri bejelentkezés](media/easysso-for-jira-tutorial/jira-admin-2.png)

3. Válassza az **SAML** lehetőséget. Ekkor megjelenik az SAML konfigurációs szakasza.

    ![SAML](media/easysso-for-jira-tutorial/jira-admin-3.png)

4. Válassza a fenti **tanúsítványok** fület, és a következő képernyő jelenik meg:

    ![Metaadatok URL-címe](media/easysso-for-jira-tutorial/jira-admin-4.png)

5. Most keresse meg az **Azure ad SSO** -konfiguráció korábbi lépéseiben mentett **tanúsítvány (Base64)** vagy **metaadat-fájlt** . A folytatáshoz a következő lehetőségek állnak rendelkezésre:

    a. Használja a számítógép helyi fájljába letöltött alkalmazás-összevonási **metaadat-fájlt** . Válassza a választógomb **feltöltése** gombot, és kövesse a fájl feltöltése párbeszédpanelt, amely az operációs rendszerre vonatkozik.

    **OR**

    b. Nyissa meg az alkalmazás-összevonási **metaadatokat tartalmazó fájlt** , és tekintse meg a fájl tartalmát (bármilyen egyszerű szövegszerkesztőben), és másolja a vágólapra. Válassza a **beviteli** beállítás lehetőséget, majd illessze be a vágólap tartalmát a szövegmezőbe.

     **OR**

    c. Teljesen manuális konfiguráció. Az alkalmazás-összevonási **tanúsítvány (Base64)** megnyitásával tekintse meg a fájl tartalmát (bármilyen egyszerű szövegszerkesztőben), és másolja a vágólapra. Illessze be a **identitásszolgáltató jogkivonat-aláíró tanúsítványok** szövegmezőbe. Ezután navigáljon az **általános** lapra, és töltse ki a **kötési URL-címet** és az entitás- **azonosító** mezőket a **bejelentkezési URL-cím** és a korábban mentett **Azure ad-azonosító** megfelelő értékeivel.
 
6. Kattintson a lap alján található **Save (Mentés** ) gombra. Ekkor megjelenik a metaadatok vagy a tanúsítványfájl tartalmának elemzése a konfigurációs mezőkbe. A JIRA-konfiguráció EasySSO befejeződött.

7. A legjobb tesztelési élmény érdekében keresse meg **& a Feel (nézet** ) fület, és ellenőrizze az **SAML bejelentkezési gombját** . Ez a funkció lehetővé teszi, hogy az Azure AD SAML-integráció teljes körű teszteléséhez a JIRA bejelentkezési képernyőjén külön gomb legyen kiválasztva. Ezt a gombot bekapcsolhatja, és beállíthatja az elhelyezését, színét és fordítását is az éles üzemmódra.

    ![Look & Feel](media/easysso-for-jira-tutorial/jira-admin-5.png)

    > [!NOTE]
    > Ha bármilyen problémája van, forduljon a [EasySSO támogatási csapatához](mailto:support@techtime.co.nz).

### <a name="create-easysso-for-jira-test-user"></a>EasySSO létrehozása JIRA-teszt felhasználó számára

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a JIRA-ben. A EasySSO for JIRA támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint **le van tiltva** . A felhasználók üzembe helyezésének engedélyezéséhez explicit módon ellenőriznie kell, hogy a EasySSO beépülő modul konfigurációjának általános szakaszában be van-e **kapcsolva a felhasználó létrehozása a sikeres bejelentkezés** lehetőségnél. Ha egy felhasználó még nem létezik a JIRA-ben, a rendszer egy újat hoz létre a hitelesítés után.

Ha azonban nem kívánja engedélyezni az automatikus felhasználó-kiépítés használatát a felhasználó első bejelentkezésekor, a felhasználóknak léteznie kell a háttérbeli felhasználói könyvtárakban a JIRA-példány, például az LDAP vagy a Atlassian tömeg használatával.

![Felhasználók átadása](media/easysso-for-jira-tutorial/jira-admin-6.png)

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

### <a name="idp-initiated-workflow"></a>Identitásszolgáltató által kezdeményezett munkafolyamat

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen rákattint a JIRA csempéhez tartozó EasySSO, automatikusan be kell jelentkeznie arra a JIRA-példányra, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

### <a name="sp-initiated-workflow"></a>Az SP által kezdeményezett munkafolyamat

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli az JIRA **SAML bejelentkezési** gomb használatával.

![Felhasználói SAML-bejelentkezés](media/easysso-for-jira-tutorial/jira-admin-7.png)

Ez a forgatókönyv feltételezi, hogy engedélyezte az **SAML-bejelentkezési gomb** megjelenését a JIRA EasySSO-konfigurációs lapjának **megjelenés & nézet** lapján (lásd fent). A meglévő munkamenetek zavarásának elkerülése érdekében nyissa meg a JIRA bejelentkezési URL-címét a böngészőalapú inkognitóban módban. Kattintson az **SAML-bejelentkezési** gombra, és a rendszer átirányítja az Azure ad felhasználói hitelesítési folyamatára. A sikeres befejezést követően a rendszer átirányítja a JIRA-példányra az SAML-n keresztül hitelesített felhasználóként.

Az Azure AD-ből való átirányítást követően lehetséges, hogy a következő képernyővel találkozhat:

![EasySSO-meghibásodási képernyő](media/easysso-for-jira-tutorial/jira-admin-8.png)

Ebben az esetben az [oldalon található utasításokat]( https://techtime.co.nz/display/TECHTIME/EasySSO+How+to+get+the+logs#EasySSOHowtogetthelogs-RETRIEVINGTHELOGS) követve hozzáférhet a **Atlassian-JIRA. log** fájlhoz. A hiba részleteit a EasySSO-hiba oldalon található hivatkozási azonosító fogja tartalmazni.

Ha problémák merülnek fel a naplóüzenetek kivonatolásával kapcsolatban, lépjen kapcsolatba a [EasySSO támogatási csapatával](mailto:support@techtime.co.nz).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [A JIRA EasySSO kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)

- [A EasySSO és a JIRA elleni védelem speciális láthatósággal és vezérlőkkel](/cloud-app-security/proxy-intro-aad)