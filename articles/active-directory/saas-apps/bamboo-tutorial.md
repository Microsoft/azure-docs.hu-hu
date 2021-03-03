---
title: 'Oktatóanyag: Azure Active Directory integráció az SAML SSO-vel a Bamboo by Solution GmbH használatával | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést az Azure Active Directory és az SAML egyszeri bejelentkezés a Bamboo-hoz a Solution GmbH használatával.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/12/2021
ms.author: jeedes
ms.openlocfilehash: 8ec834d76692f78d1d7bc60ddbd4c73fe4adaede
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101646106"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>Oktatóanyag: Azure Active Directory integráció az SAML SSO-val a Bamboo by Solution GmbH használatával

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az SAML SSO-t a Bamboo-hoz a Azure Active Directory (Azure AD) megoldással. Ha az Azure AD-vel integrálja az SAML SSO-t a Bamboo-hoz, a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáféréssel rendelkezik a Bamboo-hez készült SAML SSO-hoz a Solution GmbH használatával.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az SAML SSO-be a Bamboo-ba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Ha az Azure AD-integrációt a Bamboo által használt SAML SSO-val szeretné konfigurálni, a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* SAML egyszeri bejelentkezés a Bamboo-hoz az egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Bamboo by Solution GmbH SAML SSO-je támogatja az **SP és a identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést
* A Bamboo által a Solution GmbH SAML SSO-je a felhasználó üzembe helyezésének **időpontját** támogatja

## <a name="add-saml-sso-for-bamboo-by-resolution-gmbh-from-the-gallery"></a>SAML egyszeri bejelentkezés hozzáadása a Bamboo-hoz a katalógusból a Solution GmbH használatával

Ahhoz, hogy az Azure AD-be az SAML SSO-t a Bamboo-hoz az Azure AD-be, hozzá kell adnia a Bamboo-hoz készült SAML SSO-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a következőt: **SAML SSO for Bamboo by Solution GmbH** a keresőmezőbe.
1. Válassza a **következőt: SAML SSO a Bamboo by Solution GmbH** from Results panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>Az Azure AD SSO konfigurálása és tesztelése az SAML egyszeri bejelentkezéssel a Bamboo by Solution GmbH használatával

Konfigurálja és tesztelje az Azure AD SSO-t a Bamboo által használt SAML SSO-vel, egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy összekapcsolt kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között, amely az SAML SSO-t a megoldási GmbH-ban a Bamboo-hoz.

Az Azure AD SSO konfigurálásához és teszteléséhez a Bamboo által használt SAML SSO-t a következő lépésekkel végezheti el:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
     1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
     1. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
2. Az **[SAML SSO konfigurálása a Bamboo számára a Solution GmbH SSO](#configure-saml-sso-for-bamboo-by-resolution-gmbh-sso)** -vel – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
    1. **[SAML SSO létrehozása a Bamboo számára a Solution GmbH test User](#create-saml-sso-for-bamboo-by-resolution-gmbh-test-user)** -to have a Britta Simon in SAML SSO a Bamboo-hoz a GmbHby feloldási GmbH, amely a felhasználó Azure ad-képviseletéhez kapcsolódik.
6. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD SSO-t a Azure Portalban.
 
1. A Azure Portal a Bamboo SAML-on a **Resolution GmbH** Application Integration lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyetlen Sign-On módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon válassza az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikont a beállítások szerkesztéséhez.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)
4. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<server-base-url>/plugins/servlet/samlsso`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<server-base-url>/plugins/servlet/samlsso`

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

     A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Vegye fel a kapcsolatot az [SAML SSO-val a Bamboo by Solution GmbH ügyfél-támogatási csapatával](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) az értékek beszerzéséhez. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

6. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

7. A **set up SAML SSO for Bamboo by Solution GmbH** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**  >  **felhasználók**  >  **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszót.
   1. Válassza a **Létrehozás** lehetőséget.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban engedélyezi a B. Simon számára az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a Bamboo egyszeri bejelentkezéshez a (z) a Solution GmbH számára.

1. A Azure Portal válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **következőt: SAML SSO a Bamboo by Solution GmbH**.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **Felhasználó hozzáadása** elemet. Ezután a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza ki a felhasználók listájából a **B. Simon** elemet. Ezután válassza a **kijelölés** lehetőséget a képernyő alján.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés** lehetőséget.

### <a name="configure-saml-sso-for-bamboo-by-resolution-gmbh-sso"></a>Az SAML SSO konfigurálása a Bamboo számára a Solution GmbH SSO-val

1. Jelentkezzen be a Bamboo-hoz készült SAML SSO-ra a megoldási GmbH vállalati webhely rendszergazdaként.

1. A fő eszköztár jobb oldalán kattintson a **Beállítások**  >  **bővítmények** elemre.

    ![A beállítások](./media/bamboo-tutorial/tutorial_bamboo_setings.png)

1. Lépjen a biztonság szakaszra, kattintson a menüsávban található **SAML-SingleSignon** .

    ![A Samlsingle](./media/bamboo-tutorial/tutorial_bamboo_samlsingle.png)

1. Az **SAML SIngleSignOn beépülő modul konfigurálása lapon** kattintson a **identitásszolgáltató hozzáadása** elemre.

    ![A identitásszolgáltató hozzáadása](./media/bamboo-tutorial/tutorial_bamboo_addidp.png)

1. Az **SAML-azonosító kiválasztása** lapon hajtsa végre a következő lépéseket:

    ![Az identitás szolgáltatója](./media/bamboo-tutorial/tutorial_bamboo_identityprovider.png)

    a. Válassza a **identitásszolgáltató típust** **Azure ad**-ként.

    b. A **név** szövegmezőbe írja be a nevet.

    c. A **Leírás** szövegmezőbe írja be a leírást.

    d. Kattintson a **Tovább** gombra.

1. Az **identitás-szolgáltató konfigurációja** lapon kattintson a **tovább** gombra.

    ![Az identitás konfigurációja](./media/bamboo-tutorial/tutorial_bamboo_identityconfig.png)

1. Az **SAML identitásszolgáltató-Metaadatok importálása** lapon kattintson a **fájl betöltése** gombra, hogy feltöltse a **metaadatokat tartalmazó XML-** fájlt, amelyet a Azure Portal letöltött.

    ![A idpmetadata](./media/bamboo-tutorial/tutorial_bamboo_idpmetadata.png)

1. Kattintson a **Tovább** gombra.

1. Kattintson a **Beállítások mentése** lehetőségre.

### <a name="create-saml-sso-for-bamboo-by-resolution-gmbh-test-user"></a>SAML SSO létrehozása a Bamboo számára a Solution GmbH test User

Ennek a szakasznak a célja, hogy egy Britta Simon nevű felhasználót hozzon létre a Bamboo által használt SAML SSO-hoz. A Bamboo by Solution GmbH SAML SSO-je támogatja az igény szerinti üzembe helyezést, és a felhasználók manuálisan is létrehozhatók, [Ha a megoldás alapján az ügyfél-támogatási csapat](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) felveszi a kapcsolatot az SAML SSO-val a Bamboo-hoz.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ez átirányítja az SAML SSO-t a Bamboo-ra a Solution GmbH bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.  

* Nyissa meg a következőt: SAML SSO for Bamboo by Solution GmbH bejelentkezési URL-címe közvetlenül, és onnan kezdeményezheti a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie a Bamboo egyszeri bejelentkezéshez készült SAML SSO-ba, amelyhez be kell állítania az egyszeri bejelentkezést.

A Microsoft My Apps használatával bármilyen módban tesztelheti az alkalmazást. Ha a saját alkalmazások szolgáltatásban a Bamboo egyszeri bejelentkezés a Bamboo-hoz elemre kattint, akkor az SP-módban való konfiguráláskor a rendszer átirányítja az alkalmazás bejelentkezési lapjára a bejelentkezési folyamat elindításához, és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie a Bamboo szolgáltatáshoz készült SAML SSO-ba, amelyhez beállítja az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

Miután konfigurálta az SAML SSO-t a Bamboo by Solution GmbH szolgáltatásban, kikényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).