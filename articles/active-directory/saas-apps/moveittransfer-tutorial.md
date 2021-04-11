---
title: 'Oktatóanyag: Azure Active Directory integráció az MOVEit-Átvitelsel – Azure AD-integráció | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a MOVEit átvitele között – Azure AD-integráció.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 73f378bb0f71df4df3731a3ef2a1fdb7f8abb4aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653050"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Oktatóanyag: Azure Active Directory integráció az MOVEit-Átvitelsel – Azure AD-integráció

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a MOVEit-átvitelt – Azure AD-integrációt Azure Active Directory (Azure AD-val). Ha integrálja a MOVEit-átvitelt – az Azure ad-integrációt az Azure AD-vel, a következőket teheti:

* A MOVEit-átvitelhez hozzáférő Azure AD-beli vezérlés – Azure AD-integráció.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a MOVEit átvitelbe – Azure AD-integráció az Azure AD-fiókokkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* MOVEit-átvitel – Azure AD-integrációs egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* MOVEit-átvitel – az Azure AD-integráció támogatja az **SP** által kezdeményezett egyszeri bejelentkezést

## <a name="add-moveit-transfer---azure-ad-integration-from-the-gallery"></a>MOVEit-átvitel hozzáadása – Azure AD-integráció a katalógusból

A MOVEit-átvitel – Azure ad-integráció az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a MOVEit-átvitelt – az Azure AD-integrációt a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **MOVEit átvitel – Azure ad-integráció** kifejezést a keresőmezőbe.
1. Válassza az **MOVEit átvitel lehetőséget – Azure ad-integráció** az eredmények paneljén, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-moveit-transfer---azure-ad-integration"></a>Azure AD SSO konfigurálása és tesztelése MOVEit-átvitelhez – Azure AD-integráció

Azure AD SSO konfigurálása és tesztelése MOVEit-Átvitelsel – Azure AD-integráció egy **B. Simon** nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a MOVEit-átvitelben – Azure AD-integráció.

Az Azure AD SSO konfigurálása és tesztelése MOVEit-Átvitelsel – Azure AD-integráció, hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[MOVEit-átvitel konfigurálása – Azure ad Integration SSO](#configure-moveit-transfer---azure-ad-integration-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[MOVEit-átvitel létrehozása – Azure ad Integration test User](#create-moveit-transfer---azure-ad-integration-test-user)** –, hogy a B. Simon MOVEit-átvitele – az Azure ad integrációja, amely a felhasználó Azure ad-képviseletéhez van társítva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **MOVEit-átvitel – Azure ad Integration** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban, ha **szolgáltatói metaadatokat tartalmazó fájllal** rendelkezik, hajtsa végre a következő lépéseket:

    a. Kattintson a **metaadat-fájl feltöltése** elemre.

    ![Metaadat-fájl feltöltése](common/upload-metadata.png)

    b. Kattintson a **mappa emblémára** a metaadat-fájl kiválasztásához, majd kattintson a **feltöltés** elemre.

    ![metaadat-fájl kiválasztása](common/browse-upload-metadata.png)

    c. A metaadat-fájl feltöltése után az **azonosító** és a **Válasz URL-címe** automatikusan feltöltve lesz az **alapszintű SAML-konfiguráció** szakaszban:

    ![MOVEit-átvitel – Azure AD-integrációs tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier-reply.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet:  `https://contoso.com`

    > [!NOTE]
    > A **bejelentkezési URL-cím** értéke nem valós. Frissítse az értéket a tényleges Sign-On URL-címmel. Az érték beszerzéséhez lépjen kapcsolatba a [MOVEit-átvitelsel – az Azure ad integrációs ügyfelének ügyfélszolgálati](https://community.ipswitch.com/s/support) csapatával. A szolgáltatói **metaadatok fájlját** letöltheti a szolgáltatói **metaadatok URL-címéről** , amelyet később a **MOVEit átvitel konfigurálása – az Azure ad Integration egyszeri bejelentkezés** szakasza ismertet az oktatóanyagban. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

4. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. Az **MOVEit átvitelének beállítása – Azure ad-integráció** szakaszban adja meg a megfelelő URL (eke) t a követelménynek megfelelően.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a MOVEit-átvitelhez – Azure AD-integráció.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **MOVEit átvitel – Azure ad-integráció** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="configure-moveit-transfer---azure-ad-integration-sso"></a>MOVEit-átvitel konfigurálása – Azure AD-integráció SSO

1. Jelentkezzen be a MOVEit-áthelyezési bérlőre rendszergazdaként.

2. A bal oldali navigációs ablaktáblán kattintson a **Beállítások** elemre.

    ![Beállítások szakasz az alkalmazás oldalán](./media/moveittransfer-tutorial/settings.png)

3. Kattintson az **egyszeri bejelentkezés** hivatkozásra, amely a **biztonsági házirendek alatt > a felhasználó hitelesítése** lehetőségre.

    ![Biztonsági házirendek az alkalmazás oldalán](./media/moveittransfer-tutorial/sso.png)

4. Kattintson a metaadatok URL-hivatkozására a metaadat-dokumentum letöltéséhez.

    ![Szolgáltatói metaadatok URL-címe](./media/moveittransfer-tutorial/metadata.png)
    
   * Ellenőrizze, hogy a **entityID** egyezik- **e az** **alapszintű SAML-konfiguráció** szakaszban.
   * Ellenőrizze, hogy a **AssertionConsumerService** hely URL-címe megfelel-e a **Válasz URL-címének** az **alapszintű SAML konfiguráció**
    
     ![Egyetlen Sign-On konfigurálása az alkalmazás oldalán](./media/moveittransfer-tutorial/xml.png)

5. Az új összevont identitás-Szolgáltató hozzáadásához kattintson az **identitás-szolgáltató hozzáadása** gombra.

    ![Identitás-szolgáltató hozzáadása](./media/moveittransfer-tutorial/idp.png)

6. Kattintson a **Tallózás...** elemre, és válassza ki a Azure Portalból letöltött metaadat-fájlt, majd kattintson az **Identity Provider hozzáadása** lehetőségre a letöltött fájl feltöltéséhez.

    ![SAML-identitás szolgáltatója](./media/moveittransfer-tutorial/saml.png)

7. Az **Összevont identitás-szolgáltató beállításainak szerkesztése..** . lapon válassza az "**Igen**" **lehetőséget, majd** kattintson a **Save (Mentés**) gombra.

    ![Összevont identitás-szolgáltató beállításai](./media/moveittransfer-tutorial/save.png)

8. Az **Összevont identitás-szolgáltató felhasználói beállítások szerkesztése** oldalon hajtsa végre a következő műveleteket:
    
    ![Összevont identitás-szolgáltató beállításainak szerkesztése](./media/moveittransfer-tutorial/attributes.png)
    
    a. Válassza ki az **SAML-NameID** **bejelentkezési nevet**.
    
    b. Válassza a **másik** a **teljes név** lehetőséget, majd az **attribútum neve** szövegmezőbe tegye a következőket: `http://schemas.microsoft.com/identity/claims/displayname` .
    
    c. Válassza  az egyéb **e-mail-cím** lehetőséget, majd az **attribútum neve** szövegmezőbe tegye a következőket: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .
    
    d. Válassza az **Igen** lehetőséget, ha **a bejelentkezéskor automatikusan létrehozza a fiókot**.
    
    e. Kattintson a **Save (Mentés** ) gombra.

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>MOVEit-átvitel létrehozása – Azure AD Integration test User

Ennek a szakasznak a célja egy Britta Simon nevű felhasználó létrehozása a MOVEit-átvitelben – Azure AD-integráció. MOVEit-átvitel – az Azure AD-integráció támogatja az igény szerinti üzembe helyezést, amelyet engedélyez. Ez a szakasz nem tartalmaz műveleti elemeket. Egy új felhasználó jön létre az MOVEit-átvitelhez való hozzáférés során – az Azure AD-integráció, ha még nem létezik.

>[!NOTE]
>Ha manuálisan kell létrehoznia egy felhasználót, lépjen kapcsolatba a [MOVEit-átvitelsel – az Azure ad Integration Client ügyfélszolgálati csapatával](https://community.ipswitch.com/s/support).

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ez átirányítja a MOVEit átvitelre – az Azure AD integrációs bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen a MOVEit átvitel – Azure AD-integráció bejelentkezési URL-címére közvetlenül, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Ha a saját alkalmazások MOVEit átvitele – Azure AD-integráció csempére kattint, automatikusan be kell jelentkeznie a MOVEit-átvitelbe – az Azure AD-integrációba, amelyhez be kell állítania az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

Miután konfigurálta a MOVEit-átvitelt – az Azure AD-integrációval kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).