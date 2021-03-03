---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a tabló-kiszolgálóval | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a tabló-kiszolgáló között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/25/2021
ms.author: jeedes
ms.openlocfilehash: 810f57afceda10c10cf5919a0c50ca6be2950703
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650583"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-server"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a tabló-kiszolgálóval

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a tabló-kiszolgálót Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a tabló-kiszolgálót, a következőket teheti:

* A tabló-kiszolgálóhoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a tabló kiszolgálóra az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.


## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A tabló-kiszolgáló egyszeri bejelentkezésének (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A tabló-kiszolgáló támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="add-tableau-server-from-the-gallery"></a>Tabló-kiszolgáló hozzáadása a katalógusból

A tabló-kiszolgáló az Azure AD-be való integrálásának konfigurálásához fel kell vennie a tabló-kiszolgálót a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **tabló kiszolgáló** kifejezést a keresőmezőbe.
1. Válassza ki a **tabló-kiszolgálót** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-tableau-server"></a>Azure AD SSO konfigurálása és tesztelése a tabló-kiszolgálóhoz

Konfigurálja és tesztelje az Azure AD SSO-t a tabló-kiszolgálóval egy **B. Simon** nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a tabló-kiszolgáló kapcsolódó felhasználója között.

Az Azure AD SSO és a tabló-kiszolgáló konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[tabló kiszolgáló egyszeri bejelentkezésének konfigurálása](#configure-tableau-server-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. A **[tabló-kiszolgáló tesztelési felhasználójának létrehozása](#create-tableau-server-test-user)** – a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-partneri kapcsolattal rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **tabló kiszolgáló** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://azure.<domain name>.link`

    b. Az **azonosító** mezőbe írjon be egy URL-címet a következő minta használatával: `https://azure.<domain name>.link`

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://azure.<domain name>.link/wg/saml/SSO/index.html`

    > [!NOTE]
    > Az előző értékek nem valós értékek. Frissítse az értékeket a tabló-kiszolgáló konfigurációs oldalának tényleges bejelentkezési URL-címével, azonosítójának és válaszának URL-címével, amelyet az oktatóanyag későbbi részében ismertet.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **tabló-kiszolgáló beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény alapján.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a tabló-kiszolgálóhoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **tabló-kiszolgáló** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-tableau-server-sso"></a>A tabló kiszolgáló egyszeri bejelentkezésének konfigurálása

1. Ha be szeretné állítani az egyszeri bejelentkezést az alkalmazáshoz, rendszergazdaként be kell jelentkeznie a tabló-kiszolgáló bérlőbe.

2. A **konfiguráció** lapon válassza a **felhasználói identitás & hozzáférés** lehetőséget, majd válassza a **hitelesítési** módszer lapot.

    ![Képernyőfelvétel: a felhasználói identitásból kiválasztott hitelesítés & hozzáférés.](./media/tableauserver-tutorial/auth.png)

3. A **konfiguráció** lapon hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: a konfigurációs oldal, ahol megadhatja a leírt értékeket.](./media/tableauserver-tutorial/config.png)

    a. A **hitelesítési módszer** beállításnál válassza az SAML lehetőséget.

    b. Jelölje be az **SAML-hitelesítés engedélyezése a kiszolgálón** jelölőnégyzetet.

    c. Tabló-kiszolgáló visszatérési URL-címe – a tabló-kiszolgáló felhasználói által elérhető URL-cím, például: `http://tableau_server` . A használata `http://localhost` nem ajánlott. A záró perjelet (például) használó URL-cím használata `http://tableau_server/` nem támogatott. Másolja a **tabló-kiszolgáló visszatérési URL-címét** , és illessze be az **URL-cím** szövegmezőbe a Azure Portal **alapszintű SAML-konfiguráció** szakaszában.

    d. SAML-entitás azonosítója – az entitás azonosítója egyedileg azonosítja a tabló-kiszolgáló telepítését a identitásszolgáltató. Itt megadhatja a tabló-kiszolgáló URL-címét, ha szeretné, de nem kell a tabló-kiszolgáló URL-címének lennie. Másolja az **SAML-entitás azonosítóját** , és illessze be az **azonosító** szövegmezőbe a Azure Portal **alapszintű SAML-konfiguráció** szakaszában.

    e. Kattintson az **XML letöltése metaadat-fájlra** , és nyissa meg a szövegszerkesztő alkalmazásban. Keresse meg az állítási fogyasztói szolgáltatás URL-címét http post és index 0 értékkel, és másolja az URL-címet. Most illessze be a **Válasz URL** szövegmezőbe a Azure Portal **ALAPszintű SAML-konfiguráció** szakaszában.

    f. Keresse meg Azure Portalról letöltött összevonási metaadat-fájlt, majd töltse fel az **SAML-identitásszolgáltató metaadat-fájljába**.

    : Adja meg azoknak az attribútumoknak a nevét, amelyeket a identitásszolgáltató a felhasználónevek, a megjelenítendő nevek és az e-mail-címek tárolására használ.

    h. Kattintson a **Mentés** gombra.

    > [!NOTE]
    > Az ügyfélnek fel kell töltenie egy. CRT kiterjesztésű és egy olyan RSA-vagy DSA titkos kulcsfájl-fájlt, amely a. Key kiterjesztéssel rendelkezik, mint a tanúsítvány-kulcsfájl. A tanúsítványfájl és a tanúsítvány kulcsfájl részletes ismertetését [ebben](https://help.tableau.com/current/server/en-us/saml_requ.htm) a dokumentumban találja. Ha segítségre van szüksége az SAML a tabló-kiszolgálón való konfigurálásához, tekintse meg ezt a cikket a [Server Wide SAML konfigurálása](https://help.tableau.com/current/server/en-us/config_saml.htm)című cikkben.

### <a name="create-tableau-server-test-user"></a>Tabló-kiszolgáló tesztelési felhasználójának létrehozása

Ennek a szakasznak a célja, hogy létrehozzon egy B. Simon nevű felhasználót a tabló-kiszolgálón. A tabló-kiszolgálón ki kell építenie az összes felhasználót.

A felhasználó felhasználónevének egyeznie kell azzal az értékkel, amelyet a **Felhasználónév** egyéni Azure ad-attribútumában konfigurált. A megfelelő leképezéssel az integrációnak működnie kell az Azure AD egyszeri bejelentkezés konfigurálásával.

> [!NOTE]
> Ha manuálisan kell létrehoznia egy felhasználót, lépjen kapcsolatba a cég tabló-kiszolgáló rendszergazdájával.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a tabló-kiszolgáló bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül a tabló-kiszolgáló bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások felületén a tabló-kiszolgáló csempére kattint, a rendszer átirányítja a tabló-kiszolgáló bejelentkezési URL-címére. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

A tabló-kiszolgáló konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).