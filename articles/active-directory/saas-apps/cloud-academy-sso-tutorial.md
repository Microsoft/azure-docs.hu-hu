---
title: 'Oktatóanyag: Azure Active Directory SSO-integráció a Cloud Academy-vel – SSO'
description: Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Cloud Academy-SSO között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/15/2020
ms.author: jeedes
ms.openlocfilehash: fa46d6e5c7f1007e3a90e22eb9d4f46e18251a28
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98729831"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-cloud-academy---sso"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses integráció a Cloud Academy-vel – SSO

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Cloud Academy-SSO-t Azure Active Directory (Azure AD) használatával. Ha integrálja a Cloud Academy-SSO-t az Azure AD-val, a következőket teheti:

* Az Azure AD segítségével szabályozhatja, hogy ki férhet hozzá a Cloud Academy-SSO-hoz.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Cloud Academy-SSO-ba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Egy felhőalapú Akadémia – egyszeri bejelentkezést (SSO) használó egyszeri bejelentkezéses előfizetés.

## <a name="tutorial-description"></a>Oktatóanyag leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését tesztkörnyezetben végezheti el.

* Cloud Academy – SSO támogatja az **SP** által kezdeményezett SSO-t
* Cloud Academy – az egyszeri bejelentkezés **csak időben támogatja a** felhasználók üzembe helyezését

## <a name="add-cloud-academy---sso-from-the-gallery"></a>Cloud Academy-SSO hozzáadása a katalógusból

A Cloud Academy-SSO Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Cloud Academy-SSO-t a katalógusból a felügyelt SaaS-alkalmazások listájához:

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók.
1. A bal oldali panelen válassza az **Azure Active Directory** lehetőséget.
1. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban adja meg a **Cloud Academy-SSO** kifejezést a keresőmezőbe.
1. Válassza ki a **Cloud Academy-SSO** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-cloud-academy---sso"></a>Azure AD SSO konfigurálása és tesztelése a Cloud Academy-hez – SSO

Az Azure AD SSO konfigurálását és tesztelését a Cloud Academy-SSO használatával végezheti el egy **B. Simon** nevű teszt felhasználó segítségével. Az SSO működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a hozzá tartozó felhasználó között a Cloud Academy-SSO-ban.

Az Azure AD SSO és a Cloud Academy-SSO konfigurálásához és teszteléséhez hajtsa végre a következő magas szintű lépéseket:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t annak engedélyezéséhez, hogy a felhasználók használják a szolgáltatást.
    1. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez.
    1. **[Hozzáférés biztosítása a tesztelési felhasználónak](#grant-access-to-the-test-user)** , hogy lehetővé váljon a felhasználó számára az Azure ad egyszeri bejelentkezés használata.
1. Az **[egyszeri bejelentkezés konfigurálása a Cloud Academy-hez – SSO](#configure-single-sign-on-for-cloud-academy)** az alkalmazás oldalán.
    1. **[Hozzon létre egy Cloud Academy-SSO teszt felhasználót](#create-a-cloud-academy-test-user)** a felhasználó Azure ad-beli képviseletének megfelelő felhasználóként.
1. Ellenőrizze az **[SSO](#test-sso)** -t a konfiguráció működésének ellenőrzéséhez.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO-t a Azure Portalban:

1. A Azure Portal a **Cloud Academy-SSO** Application Integration oldalon, a **kezelés** szakaszban válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon válassza az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza gombot a beállítások szerkesztéséhez:

   ![Képernyőkép, amely az alapszintű SAML-konfiguráció szerkesztésére szolgáló ceruza gombot mutatja.](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az alábbi URL-címek egyikét:
    
    | Bejelentkezési URL-cím |
    |--------------|
    | `https://cloudacademy.com/login/enterprise/` |
    | `https://app.qa.com/login/enterprise/` |
    |
    
    b. A **Válasz URL-címe** szövegmezőbe írja be az alábbi URL-címek egyikét:
    
    | Válasz URL-cím |
    |--------------|
    | `https://cloudacademy.com/labs/social/complete/saml/` |
    | `https://app.qa.com/labs/social/complete/saml/` |
    |
1. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban válassza a másolás gombot az **alkalmazás-összevonási metaadatok URL-címének** másolásához. Mentse az URL-címet.

    ![Képernyőkép, amely az alkalmazás-összevonási metaadatok URL-címéhez tartozó másolás gombot jeleníti meg.](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy B. Simon nevű teszt felhasználót hoz létre a Azure Portal.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget. Válassza a **felhasználók** lehetőséget, majd válassza **a minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre a következő lépéseket:
   1. A név mezőbe írja be a **B. Simon** **nevet** .  
   1. A **Felhasználónév** mezőbe írja be a nevet \<username> @ \<companydomain> . \<extension> Például: `B.Simon@contoso.com`.
   1. Válassza a **jelszó megjelenítése** lehetőséget, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Válassza a **Létrehozás** lehetőséget.

### <a name="grant-access-to-the-test-user"></a>Hozzáférés biztosítása a tesztelési felhasználónak

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy a felhasználó hozzáférést biztosít a Cloud Academy-SSO-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza ki a **Cloud Academy-SSO** elemet.
1. Az alkalmazás Áttekintés lapjának **kezelés** szakaszában válassza a **felhasználók és csoportok** lehetőséget:
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget:
1. A **felhasználók és csoportok** párbeszédpanelen válassza az **B. Simon** elemet a **felhasználók** listán, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés** lehetőséget.

## <a name="configure-single-sign-on-for-cloud-academy"></a>Egyszeri bejelentkezés konfigurálása a Cloud Academy-hez

1. Egy másik böngészőablakban jelentkezzen be a Cloud Academy-SSO céges webhelyre rendszergazdaként.

1. Válassza ki a vállalat nevét, majd válassza a **beállítások & integrációk** lehetőséget a megjelenő menüben:

    ![Képernyőkép, amely a beállítások & integrációk lehetőséget mutatja.](./media/cloud-academy-sso-tutorial/config-1.PNG)

1. A **beállítások & integrációk** lap **integrációk** lapján válassza az **SSO** -kártyát:

    ![Képernyőkép, amely az SSO-kártyát mutatja az integrációk lapon.](./media/cloud-academy-sso-tutorial/config-2.PNG)

1. Hajtsa végre a következő lépéseket ezen az oldalon:

    ![A Inegrations > SSO-lapot megjelenítő képernyőkép.](./media/cloud-academy-sso-tutorial/config-3.PNG)

    a. Az **entitás-azonosító URL-címe** mezőbe írja be a Azure Portalból másolt entitás-azonosító értékét.

    b. Az **SSO URL-cím** mezőben illessze be a Azure Portalból másolt bejelentkezési URL-értéket.

    c. Nyissa meg a letöltött Base64-tanúsítványt a Azure Portal a Jegyzettömbben. Illessze be a tartalmát a **tanúsítvány** mezőbe.

    d. A **név azonosítójának formátuma** mezőben tartsa meg az alapértelmezett értéket: `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` .

1. Kattintson a **Mentés** gombra.

    > [!NOTE]
    > A Cloud Academy-SSO konfigurálásával kapcsolatos további információkért lásd: az [egyszeri bejelentkezés beállítása](https://support.cloudacademy.com/hc/articles/360043908452-Setting-Up-Single-Sign-On).

### <a name="create-a-cloud-academy-test-user"></a>Felhőalapú Akadémia tesztelési felhasználójának létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a Cloud Academy-SSO-ban. A Cloud Academy-SSO támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a Cloud Academy-SSO-ban, a rendszer egy újat hoz létre a hitelesítés után.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ez átirányítja a Cloud Academy-SSO bejelentkezési URL-címre, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen a Cloud Academy-SSO bejelentkezési URL-címére közvetlenül, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Ha a saját alkalmazások Cloud Academy-SSO csempére kattint, a rendszer átirányítja a Cloud Academy-SSO bejelentkezési URL-címére. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


## <a name="next-steps"></a>Következő lépések

A Cloud Academy-SSO konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).