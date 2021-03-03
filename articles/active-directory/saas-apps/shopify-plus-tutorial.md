---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Shopify Plus szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Shopify plusz között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 65f4963f23d97ca2e3af34febb0d5dbea652fc12
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101646979"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-shopify-plus"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Shopify Plus szolgáltatással

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Shopify Plust Azure Active Directory (Azure AD) használatával. A Shopify Plus és az Azure AD integrálásával a következőket teheti:

* Szabályozza az Azure AD-t, hogy ki férhet hozzá a Shopify Plushoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Shopify-be és az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A Shopify és az egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Shopify Plus **az SP és a identitásszolgáltató** által kezdeményezett SSO-t is támogatja.

## <a name="add-shopify-plus-from-the-gallery"></a>A Shopify Plus hozzáadása a gyűjteményhez

A Shopify és az Azure AD integrálásának konfigurálásához hozzá kell adnia a Shopify Plust a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a keresőmezőbe a **Shopify Plus** kifejezést.
1. Válassza az **Shopify Plus** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-shopify-plus"></a>Azure AD SSO konfigurálása és tesztelése a Shopify Plushoz

Konfigurálja és tesztelje az Azure AD SSO-t a Shopify Plus használatával egy **B. Simon** nevű tesztelési felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Shopify Plusban.

Az Azure AD SSO konfigurálásához és teszteléséhez a Shopify Plus használatával hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. Az **[Shopify és az egyszeri bejelentkezés konfigurálása](#configure-shopify-plus-sso)** – az egyszeri bejelentkezési beállítások konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre Shopify Plus test User](#create-shopify-plus-test-user)** -to have a B. Simon in Shopify Plus partnere, amely a felhasználó Azure ad-képviseletéhez van társítva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **Shopify Plus** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://accounts.shopify.com/saml/consume/organization/<ORGANIZATION_ID>`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet:  `https://shopify.plus/login`

    > [!NOTE]
    > A válasz URL-cím értéke nem valódi. Frissítse az értéket a tényleges válasz URL-címével. Az érték beszerzéséhez lépjen kapcsolatba a [Shopify és az ügyfél-támogatási csapattal](mailto:plus-user-management@shopify.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A Shopify Plus alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül az Shopify Plus alkalmazás néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.

    | Name | Forrás attribútum|
    | ---- | --------------- |
    | e-mail | User. mail |

1. Módosítsa a **név azonosítójának** formátumát **állandó** értékre. Válassza ki az **egyedi felhasználói azonosító (név azonosítója)** beállítást, majd válassza ki a **név azonosítójának** formátumát. Válassza az **állandó** lehetőséget ehhez a beállításhoz. Mentse a módosításokat.
1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban válassza a másolás gombot az **alkalmazás-összevonási metaadatok URL-címének** másolásához, majd mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a Shopify Plushoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Shopify plusz** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-shopify-plus-sso"></a>Shopify és egyszeri bejelentkezés konfigurálása

A teljes lépések megtekintéséhez tekintse meg az [SAML-integrációk beállításával kapcsolatos Shopify dokumentációját](https://help.shopify.com/en/manual/shopify-plus/saml).

Ha az egyszeri bejelentkezést az **Shopify plusz** oldalon szeretné konfigurálni, másolja az **alkalmazás-összevonási metaadatok URL-címét** a Azure Active Directoryból. Ezután jelentkezzen be a [szervezeti rendszergazdába](https://shopify.plus) , és lépjen a **felhasználók**  >  **biztonsága** elemre. Válassza a **konfiguráció beállítása** lehetőséget, majd illessze be az alkalmazás-összevonási metaadatok URL-címét az **Identity Provider metaadatok URL-címe** szakaszba. A lépés befejezéséhez válassza a **Hozzáadás** lehetőséget.

### <a name="create-shopify-plus-test-user"></a>Shopify és tesztelési felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre a Shopify Plusban. Térjen vissza a **felhasználók** szakaszhoz, és adjon hozzá egy felhasználót az e-mail-cím és az engedélyek megadásával. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="enforce-saml-authentication"></a>SAML-hitelesítés betartatása

> [!NOTE]
> Javasoljuk, hogy az integrációt az egyes felhasználók használatával széles körben alkalmazza.

Egyéni felhasználók:
1. Nyissa meg a Shopify egy egyéni felhasználó lapját, és egy Azure AD által felügyelt e-mail-tartománnyal és a Shopify Plus-ben ellenőrzött.
1. Az SAML hitelesítés szakaszban válassza a **Szerkesztés** lehetőséget, válassza a **kötelező** lehetőséget, majd kattintson a **Mentés** gombra.
1. Ellenőrizze, hogy a felhasználó sikeresen be tud-e jelentkezni a identitásszolgáltató által kezdeményezett és az SP által kezdeményezett folyamatokon keresztül.

Egy e-mail tartományba tartozó összes felhasználó esetén:
1. Térjen vissza a **Biztonság** lapra.
1. Válassza ki az SAML hitelesítési beállításhoz **szükséges** értéket. Ez az SAML-t az e-mail-tartománnyal rendelkező összes felhasználóra kikényszeríti az Shopify Plus-ben.
1. Kattintson a **Mentés** gombra.

> [!IMPORTANT]
> Az SAML engedélyezése az e-mail-tartomány összes felhasználója számára hatással van az alkalmazást használó összes felhasználóra. A felhasználók nem tudnak bejelentkezni a szokásos bejelentkezési oldalára. Csak Azure Active Directoryon keresztül férhetnek hozzá az alkalmazáshoz. A Shopify nem biztosít olyan biztonsági mentési bejelentkezési URL-címet, amelyen a felhasználók bejelentkezhetnek a normál felhasználónevével és jelszavával. Ha szükséges, az SAML kikapcsolásához forduljon a Shopify támogatási szolgálatához.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a Shopify és a bejelentkezés URL-címére, ahol elindíthatja a bejelentkezési folyamatot.  

* Lépjen közvetlenül a Shopify és a bejelentkezési URL-címre, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a Shopify, amelyhez be szeretné állítani az egyszeri bejelentkezést. 

A Microsoft My Apps használatával bármilyen módban tesztelheti az alkalmazást. Ha a saját alkalmazások Shopify plusz csempére kattint, ha az SP módban van konfigurálva, a rendszer átirányítja az alkalmazás bejelentkezési lapjára a bejelentkezési folyamat elindításához, és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie a Shopify, amelyhez be kell állítania az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

A Shopify és a konfigurálását követően kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).