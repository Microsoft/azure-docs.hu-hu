---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a AwareGo | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és AwareGo között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/07/2020
ms.author: jeedes
ms.openlocfilehash: 4682396f68d6ff1af0b2fb6a5b1a8419d6963529
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735337"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-awarego"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses integráció a AwareGo

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a AwareGo a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az AwareGo-t az Azure AD-vel, a következőket teheti:

* A AwareGo-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a AwareGo az Azure AD-fiókjával.
* A fiókokat egy központi helyen, a Azure Portal kezelheti.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* AwareGo egyszeri bejelentkezésre (SSO) alkalmas előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben. A AwareGo támogatja a szolgáltató (SP) által kezdeményezett egyszeri bejelentkezést.


## <a name="adding-awarego-from-the-gallery"></a>AwareGo hozzáadása a gyűjteményből

A AwareGo Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a AwareGo a katalógusból a felügyelt szolgáltatott szoftverek (SaaS) alkalmazásai listájához.

1. Jelentkezzen be a Azure Portal munkahelyi fiók, iskolai fiók vagy személyes Microsoft-fiók használatával.
1. A bal oldali panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **AwareGo** kifejezést a keresőmezőbe.
1. Az eredmények ablaktábláján válassza a **AwareGo** lehetőséget, majd adja hozzá az alkalmazást. Néhány másodpercen belül az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-awarego"></a>Azure AD SSO konfigurálása és tesztelése a AwareGo-hez

Konfigurálja és tesztelje az Azure AD SSO-t a AwareGo egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a AwareGo-ben.

Az Azure AD SSO és a AwareGo konfigurálásához és teszteléséhez tegye a következőket:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t, hogy a felhasználók használhatják ezt a funkciót.  

    a. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon felhasználóval.  
    b. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** , hogy engedélyezze a B. Simon felhasználó számára az Azure ad egyszeri bejelentkezés használatát.  

1. **[Konfigurálja a AWAREGO SSO](#configure-awarego-sso)** -t az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.

    a. **[Hozzon létre egy AwareGo-tesztelési felhasználót](#create-an-awarego-test-user)** , amely a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon AwareGo rendelkezik.  
    b. Ellenőrizze az **[SSO](#test-sso)** -t a konfiguráció működésének ellenőrzéséhez.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Ha engedélyezni szeretné az Azure AD SSO használatát a Azure Portalban, tegye a következőket:

1. A Azure Portal a **AwareGo** alkalmazás-integráció lap **kezelés** területén válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. A beállítások szerkesztéséhez az **egyszeri Sign-On beállítása az SAML-vel** panelen válassza a **Szerkesztés** gombot.

   ![Képernyőfelvétel az alapszintű SAML-konfiguráció szerkesztése gombról.](common/edit-urls.png)

1. A szerkesztési ablaktábla **alapszintű SAML-konfiguráció** területén tegye a következőket:

    a. A **bejelentkezési URL-cím** mezőbe írja be a következő URL-címek egyikét:

    * `https://lms.awarego.com/auth/signin/` 
    * `https://my.awarego.com/auth/signin/`

    b. Az **azonosító (entitás azonosítója)** mezőben adja meg az URL-címet a következő formátumban: `https://<SUBDOMAIN>.awarego.com`

    c. A **Válasz URL-címe** mezőbe írja be az URL-címet a következő formátumban: `https://<SUBDOMAIN>.awarego.com/auth/sso/callback`

    > [!NOTE]
    > Az előző értékek nem valósak. Frissítse őket a tényleges azonosító és a válasz URL-címekkel. Az értékek beszerzéséhez forduljon a [AwareGo ügyfél-támogatási csapatához](mailto:support@awarego.com). A Azure Portal az **alapszintű SAML-konfiguráció** szakaszának példáit is áttekintheti.

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon az **SAML aláíró tanúsítvány** szakaszban a **tanúsítvány (Base64)** elem mellett válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépbe mentéséhez.

    ![Képernyőkép a "Letöltés" hivatkozásra az SAML aláíró tanúsítvány paneljén.](common/certificatebase64.png)

1. A **AwareGo beállítása** szakaszban másolja ki a követelményektől függően egy vagy több URL-címet.

    ![Képernyőkép a konfigurációs URL-címek másolására szolgáló "AwareGo beállítása" panelről.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget, majd válassza a **felhasználók**  >  **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságai panelen tegye a következőket:

   a. A név mezőbe írja be a **B. Simon** **nevet** .  
   b. **A Felhasználónév mezőbe írja** be a felhasználónevet a következő formátumban: `<username>@<companydomain>.<extension>` (például B.Simon@contoso.com ).  
   c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd jegyezze fel a **jelszó** mezőben megjelenő értéket későbbi használatra.  
   d. Válassza a **Létrehozás** lehetőséget.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon felhasználó számára engedélyezi az Azure SSO használatát azáltal, hogy hozzáférést biztosít a AwareGo.

1. A Azure Portal válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.
1. Az **alkalmazások** listában válassza a **AwareGo** lehetőséget.
1. Az alkalmazás – Áttekintés lap **kezelés** területén válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** panelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** ablaktábla **felhasználók** listájában válassza ki a **B. Simon** elemet, majd kattintson a **kiválasztás** gombra.
1. Ha egy szerepkört szeretne hozzárendelni a felhasználókhoz, kiválaszthatja azt a **szerepkör kiválasztása** legördülő listában. Ha nem állított be szerepkört ehhez az alkalmazáshoz, akkor az *alapértelmezett hozzáférési* szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** panelen kattintson a **hozzárendelés** gombra.

## <a name="configure-awarego-sso"></a>AwareGo SSO konfigurálása

Ha az egyszeri bejelentkezést szeretné konfigurálni a **AwareGo** oldalon, küldje el a korábban letöltött **tanúsítvány (Base64)** tanúsítványát, valamint azokat az URL-címeket, amelyeket korábban a Azure Portal a [AwareGo támogató csapatnak](mailto:support@awarego.com)másolt. A támogatási csoport ezt a beállítást hozza létre az SAML SSO-kapcsolat megfelelő létrehozásához mindkét oldalon.

### <a name="create-an-awarego-test-user"></a>AwareGo-teszt felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a AwareGo-ben. Együttműködik a [AwareGo támogatási csapatával](mailto:support@awarego.com) , hogy hozzáadja a felhasználókat a AwareGo platformhoz. Az egyszeri bejelentkezés használata előtt létre kell hoznia és aktiválnia kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az alábbi módokon tesztelheti az Azure AD egyszeri bejelentkezési konfigurációját: 

* A Azure Portal válassza az **alkalmazás tesztelése** lehetőséget. Ez átirányítja a AwareGo bejelentkezési oldalára, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül a AwareGo bejelentkezési oldalára, és indítsa el innen a bejelentkezési folyamatot.

* Nyissa meg a Microsoft My Apps szolgáltatást. Amikor kiválasztja a **AwareGo** csempét az alkalmazásokban, a rendszer átirányítja a AwareGo bejelentkezési oldalára. További információ: [Bejelentkezés és alkalmazások indítása a saját alkalmazások portálján](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Következő lépések

A AwareGo konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő kiterjeszthető feltételes hozzáférést biztosító alkalmazás-vezérlőról. További információ: a [munkamenet-vezérlés kényszerített érvényesítése Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).