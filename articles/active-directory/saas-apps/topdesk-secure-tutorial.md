---
title: 'Oktatóanyag: Azure Active Directory integráció a TOPdesk-Secure szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és TOPdesk között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/18/2021
ms.author: jeedes
ms.openlocfilehash: 5ed23889d8648c65ea0887d2f0f3406b50291f12
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101654297"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Oktatóanyag: Azure Active Directory integráció a TOPdesk – biztonságos

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a TOPdesk-Securet Azure Active Directory (Azure AD) használatával. Ha integrálja a TOPdesk-Secure-t az Azure AD-vel, a következőket teheti:

* Az Azure AD-ben elérhető TOPdesk-védelem.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a TOPdesk (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:
 
 * Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* TOPdesk – biztonságos egyszeri bejelentkezés (SSO) – engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* TOPdesk – az **SP** által kezdeményezett egyszeri bejelentkezés támogatása

## <a name="add-topdesk---secure-from-the-gallery"></a>TOPdesk hozzáadása – biztonságos a katalógusból

A TOPdesk-Secure Azure AD-ba való integrálásának konfigurálásához hozzá kell adnia a TOPdesk-Secure szolgáltatást a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **TOPdesk-Secure** kifejezést a keresőmezőbe.
1. Válassza a **TOPdesk – biztonságos** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-topdesk---secure"></a>Azure AD SSO konfigurálása és tesztelése TOPdesk – biztonságos

Ebben a szakaszban az Azure AD egyszeri bejelentkezést a TOPdesk-Secure szolgáltatással konfigurálja és teszteli a **Britta Simon** nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolat TOPdesk kell létrehozni.

Az Azure AD egyszeri bejelentkezés TOPdesk-Secure használatával történő konfigurálásához és teszteléséhez a következő lépéseket kell elvégeznie:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
    2. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
2. **[TOPdesk – biztonságos egyszeri bejelentkezés konfigurálása](#configure-topdesk---secure-sso)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre TOPdesk – biztonságos teszt felhasználót](#create-topdesk---secure-test-user)** –, hogy a Britta Simon a TOPdesk-Secure, amely a felhasználó Azure ad-képviseletéhez van társítva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés TOPdesk-Secure használatával történő konfigurálásához hajtsa végre a következő lépéseket:

1. A Azure Portal **TOPdesk – biztonságos alkalmazás-** integráció lapon válassza az **egyszeri bejelentkezés** lehetőséget.

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

3. Az **egyszeres Sign-On beállítása az SAML-vel** lapon kattintson a ceruza ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<companyname>.topdesk.net`

    b. Az **azonosító URL-címe** mezőben adja meg a TOPdesk metaadat URL-címét, amelyet a TOPdesk-konfigurációból kérhet le. A következő mintát kell használnia: `https://<companyname>.topdesk.net/saml-metadata/<identifier>`

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<companyname>.topdesk.net/tas/secure/login/verify`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Sign-On URL-címmel, azonosítóval és válasz URL-címmel. Vegye fel a kapcsolatot a [TOPdesk – biztonságos ügyfél-támogatási csapattal](https://www.topdesk.com/us/support/) , hogy megszerezze ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **TOPdesk-Secure beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a TOPdesk-biztonsághoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **TOPdesk – biztonságos** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="configure-topdesk---secure-sso"></a>TOPdesk konfigurálása – biztonságos egyszeri bejelentkezés

1. Jelentkezzen be a **TOPdesk-Secure** céges webhelyre rendszergazdaként.

2. A **TOPdesk** menüben kattintson a **Beállítások** elemre.

    ![Beállítások](./media/topdesk-secure-tutorial/ic790598.png "Beállítások")

3. Kattintson a **bejelentkezési beállítások** elemre.

    ![Bejelentkezési beállítások](./media/topdesk-secure-tutorial/ic790599.png "Bejelentkezési beállítások")

4. Bontsa ki a **bejelentkezési beállítások** menüt, majd kattintson az **általános** elemre.

    ![Általános](./media/topdesk-secure-tutorial/ic790600.png "Általános kérdések")

5. Az **SAML bejelentkezési** konfiguráció szakaszának **biztonságos** részében hajtsa végre a következő lépéseket:

    ![Technikai beállítások](./media/topdesk-secure-tutorial/ic790855.png "Technikai beállítások")

    a. Kattintson a **Letöltés** gombra a nyilvános metaadat-fájl letöltéséhez, majd mentse helyileg a számítógépén.

    b. Nyissa meg a metaadat-fájlt, és keresse meg a **AssertionConsumerService** csomópontot.

    ![Kijelentési fogyasztói szolgáltatás](./media/topdesk-secure-tutorial/ic790856.png "Kijelentési fogyasztói szolgáltatás")

    c. Másolja a **AssertionConsumerService** értéket, illessze be ezt az értéket a válasz URL-címe szövegmezőbe a **TOPdesk – biztonságos tartomány és URL-címek** szakaszban.

6. Tanúsítványfájl létrehozásához hajtsa végre a következő lépéseket:

    ![Tanúsítvány](./media/topdesk-secure-tutorial/ic790606.png "Tanúsítvány")

    a. Nyissa meg a letöltött metaadat-fájlt a Azure Portal.

    b. Bontsa ki a **securitytokenservicetype** csomópontot, amely egy **xsi: Type** of **Fed: ApplicationServiceType**.

    c. Másolja a **x509** csomópont értékét.

    d. Mentse a másolt **x509** -értéket helyileg a számítógépen egy fájlba.

7. A **nyilvános** szakaszban kattintson a **Hozzáadás** gombra.

    ![Hozzáadás](./media/topdesk-secure-tutorial/ic790607.png "Hozzáadás")

8. A **SAML konfigurációs segéd** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![SAML-konfigurációs segéd](./media/topdesk-secure-tutorial/ic790608.png "SAML-konfigurációs segéd")

    a. A letöltött metaadat-fájl Azure Portalból való feltöltéséhez az **összevonási metaadatok** területen kattintson a **Tallózás** gombra.

    b. A tanúsítványfájl feltöltéséhez a **tanúsítvány (RSA)** alatt kattintson a **Tallózás** gombra.

    c. A **titkos kulcs (RSA, PKCS8, der)** esetében feltöltheti saját titkos kulcsát, vagy felveheti a kapcsolatot a [TOPdesk-Secure ügyfélszolgálati csapatával](https://www.topdesk.com/us/support) a titkos kulcs lekéréséhez.

    d. Ha fel szeretné tölteni a TOPdesk támogatási csapatának emblémáját, kattintson a **logo ikon** alatt található **Tallózás** gombra.

    e. A **Felhasználónév attribútum** szövegmezőbe írja be a következőt: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .

    f. A **megjelenítendő név** szövegmezőbe írja be a konfiguráció nevét.

    : Kattintson a **Mentés** gombra.

### <a name="create-topdesk---secure-test-user"></a>TOPdesk létrehozása – biztonságos teszt felhasználó

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek a TOPdesk-biztonságba, a TOPdesk-Secure-ben kell kiépíteni őket.  
TOPdesk-Secure esetén a kiépítés manuális feladat.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>A felhasználók üzembe helyezésének konfigurálásához hajtsa végre a következő lépéseket:

1. Jelentkezzen be a **TOPdesk-Secure** céges webhelyre rendszergazdaként.

2. A felső menüben kattintson a **TOPdesk \> új \> támogatási fájlok \> kezelője** elemre.

    ![Operátor](./media/topdesk-secure-tutorial/ic790610.png "Operátor")

3. Az **új operátor** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Új operátor](./media/topdesk-secure-tutorial/ic790611.png "Új operátor")

    a. Kattintson az **Általános** lapra.

    b. A **vezetéknév** szövegmezőbe írja be a felhasználó vezetéknevét, például **Simon**.

    c. Válasszon egy **helyet a fiók** számára a **hely** szakaszban.

    d. A **TOPdesk bejelentkezési** szakasz **bejelentkezési név** szövegmezőbe írja be a felhasználó bejelentkezési nevét.

    e. Kattintson a **Mentés** gombra.

> [!NOTE]
> Az Azure AD felhasználói fiókjainak kiépítéséhez bármilyen más, a TOPdesk által biztosított TOPdesk-alapú felhasználói fiók létrehozására szolgáló eszközt vagy API-t használhat.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a TOPdesk-Secure bejelentkezési URL-címre, amelyen kezdeményezheti a bejelentkezési folyamatot. 

* Nyissa meg közvetlenül a TOPdesk-Secure bejelentkezési URL-címet, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások TOPdesk-Secure csempére kattint, automatikusan be kell jelentkeznie a TOPdesk-Secure szolgáltatásba, amelyhez be kell állítania az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

A TOPdesk konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós idejű védelmet biztosít a szervezet bizalmas adatai kiszűrése és beszivárgásának. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).