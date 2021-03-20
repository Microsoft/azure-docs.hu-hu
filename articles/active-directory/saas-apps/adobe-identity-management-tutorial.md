---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az Adobe Identity Management szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az Adobe Identity Management között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: fdca04c645e1bb956c8e9f294c702b639c8e2f74
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98726401"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-identity-management"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az Adobe Identity Management szolgáltatással

Ez az oktatóanyag azt ismerteti, hogyan integrálható az Adobe Identity Management Azure Active Directory (Azure AD) szolgáltatással. Ha az Azure AD-val integrálja az Adobe Identity managementet, a következőket teheti:

* Az Adobe Identity Management szolgáltatáshoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az Adobe Identity Managementbe az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Az Adobe Identity Management egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az Adobe Identity Management az **SP** által kezdeményezett egyszeri bejelentkezést támogatja

## <a name="adding-adobe-identity-management-from-the-gallery"></a>Adobe Identity Management hozzáadása a katalógusból

Az Adobe Identity Management Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az Adobe Identity Management szolgáltatást a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a keresőmezőbe az **Adobe Identity Management** kifejezést.
1. Válassza az **Adobe Identity Management** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-adobe-identity-management"></a>Az Azure AD SSO konfigurálása és tesztelése az Adobe Identity Management szolgáltatásban

Konfigurálja és tesztelje az Azure AD SSO-t az Adobe Identity Management segítségével egy **B. Simon** nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az Adobe Identity Management szolgáltatásban.

Az Azure AD SSO az Adobe Identity managementtel való konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. Az **[Adobe Identity Management SSO konfigurálása](#configure-adobe-identity-management-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre egy Adobe Identity Management test User](#create-adobe-identity-management-test-user)** -t, hogy a B. Simon partnere legyen a felhasználó Azure ad-képviseletéhez kapcsolódó, az Adobe Identity Management szolgáltatásban.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal az **Adobe Identity Management** alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet: `https://adobe.com`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://federatedid-na1.services.adobe.com/federated/saml/metadata/alias/<CUSTOM_ID>`

    > [!NOTE]
    > Az azonosító értéke nem valódi. Frissítse az értéket a tényleges azonosítóval. Az érték beszerzéséhez lépjen kapcsolatba az [Adobe Identity Management-ügyfél támogatási csoportjával](mailto:identity@adobe.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. Az **Adobe Identity Management beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést az Adobe Identity Management elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **Adobe Identity Management** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-adobe-identity-management-sso"></a>Az Adobe Identity Management egyszeri bejelentkezésének konfigurálása

1. Az Adobe Identity Management konfigurációjának automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, az **Adobe Identity Management beállítása** lehetőségre kattintva megnyithatja az Adobe Identity Management alkalmazást. Itt adja meg a rendszergazdai hitelesítő adatokat az Adobe Identity Management szolgáltatásba való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-8-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani az Adobe Identity managementet, egy másik böngészőablakban jelentkezzen be az Adobe Identity Management vállalati webhelyre rendszergazdaként.

4. Lépjen a **Beállítások** lapra, és kattintson a **create Directory (könyvtár létrehozása**) elemre.

    ![Adobe Identity Management-beállítások](./media/adobe-identity-management-tutorial/settings.png)

5. Adja meg a könyvtárnév nevét a szövegmezőben, és válassza az **összevont azonosító** lehetőséget, majd kattintson a **tovább** gombra.

    ![Adobe Identity Management – címtár létrehozása](./media/adobe-identity-management-tutorial/create-directory.png)

6. Válassza ki a **többi SAML-szolgáltatót** , és kattintson a **tovább** gombra.
 
    ![Adobe Identity Management SAML-szolgáltatók](./media/adobe-identity-management-tutorial/saml-providers.png)

7. Kattintson a **kiválasztás** elemre a Azure Portal letöltött **metaadat-XML-** fájl feltöltéséhez.

    ![Adobe Identity Management SAML-konfiguráció](./media/adobe-identity-management-tutorial/saml-configuration.png)

8. Kattintson a **kész** gombra.

### <a name="create-adobe-identity-management-test-user"></a>Adobe Identity Management test felhasználó létrehozása

1. Lépjen a **felhasználók** lapra, és kattintson a **felhasználó hozzáadása** elemre.

    ![Adobe Identity Management – felhasználó hozzáadása](./media/adobe-identity-management-tutorial/add-user.png)

2. Az adja meg a **felhasználó e-mail címe** szövegmezőben adja meg az **e-mail-címet**.

    ![Adobe Identity Management – felhasználó mentése](./media/adobe-identity-management-tutorial/save-user.png)

3. Kattintson a **Mentés** gombra.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját.

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja az Adobe Identity Management bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.

* Lépjen közvetlenül az Adobe Identity Management bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások Adobe Identity Management csempére kattint, a rendszer átirányítja az Adobe Identity Management bejelentkezési URL-címére. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

Az Adobe Identity Management konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).