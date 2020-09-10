---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a DocuSign | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és DocuSign között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: c91f9d38922cc1bddf252fde59291c2f233e1aa2
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89650196"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-docusign"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a DocuSign

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a DocuSign a Microsoft Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az DocuSign-t az Azure AD-vel, a következőket teheti:

* Az Azure AD segítségével szabályozhatja, hogy ki férhet hozzá a DocuSign.
* Engedélyezze az automatikus bejelentkezést a felhasználók Azure AD-fiókjain keresztül történő DocuSign.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

Ha többet szeretne megtudni a szolgáltatott szoftver (SaaS) alkalmazás Azure AD-integrációval kapcsolatban, tekintse meg az [egyszeri bejelentkezés az Azure ad](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)-beli alkalmazásokba című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Az egyszeri bejelentkezést (SSO) engedélyező DocuSign-előfizetés.

> [!NOTE]
> Ez az integráció az Azure AD USA kormányzati felhőalapú környezetének használatával is elérhető. Ezt az alkalmazást az Azure AD US government Cloud Application Galleryben találja, és ugyanúgy konfigurálhatja, mint a nyilvános felhőben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését tesztkörnyezetben végezheti el annak ellenőrzéséhez, hogy:

* A DocuSign támogatja a szolgáltató (SP) által kezdeményezett egyszeri bejelentkezést.

* A DocuSign támogatja **az igény** szerinti felhasználói üzembe helyezést.

* A DocuSign támogatja az [automatikus felhasználó-kiépítés](https://docs.microsoft.com/azure/active-directory/saas-apps/docusign-provisioning-tutorial)használatát.
* A DocuSign konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-docusign-from-the-gallery"></a>DocuSign hozzáadása a gyűjteményből

A DocuSign Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a DocuSign a katalógusból a felügyelt SaaS-alkalmazások listájához:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, illetve személyes Microsoft-fiók használatával.
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatást.
1. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **DocuSign** kifejezést a keresőmezőbe.
1. Válassza az **DocuSign** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-docusign"></a>Azure AD SSO konfigurálása és tesztelése a DocuSign-hez

Konfigurálja és tesztelje az Azure AD SSO-t a DocuSign-mel egy **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a megfelelő felhasználó között a DocuSign-ben.

Az Azure AD SSO és a DocuSign konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. [Konfigurálja az Azure ad SSO](#configure-azure-ad-sso) -t úgy, hogy a felhasználók használhatják ezt a funkciót.
    1. [Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user) az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. [Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user) , hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. [Konfigurálja a DOCUSIGN SSO](#configure-docusign-sso) -t az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
    1. [Hozzon létre egy DocuSign-tesztelési felhasználót](#create-docusign-test-user) , amely a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-DocuSign hoz létre.
1. Ellenőrizze az [SSO](#test-sso) -t a konfiguráció működésének ellenőrzéséhez.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Ha engedélyezni szeretné az Azure AD SSO használatát a Azure Portalban, kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **DocuSign** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, majd válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon válassza az **alapszintű SAML-konfigurációhoz** tartozó toll ikont a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban kövesse az alábbi lépéseket:

    a. A **bejelentkezési URL** szövegmezőbe írja be az URL-címet a következő minta használatával:

    `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. Az **azonosító (entitás azonosítója)** szövegmezőbe írja be az URL-címet a következő minta használatával:

    `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    c. A **Válasz URL-címe** szövegmezőbe írja be az alábbi URL-minták bármelyikét:
    
    | Válasz URL-cím |
    |-------------|
    |`https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/<IDPID>`|
    |`https://<subdomain>.docusign.net/SAML/`|

    > [!NOTE]
    > Ezek a zárójeles értékek helyőrzők. Cserélje le őket a tényleges bejelentkezési URL-cím, az azonosító és a válasz URL-cím értékeire. Ezeket az adatokat az oktatóanyag későbbi részében, az "SAML 2,0-végpontok megtekintése" című szakaszban ismertetjük.

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** című szakaszt. A **Letöltés** gombra kattintva letöltheti a tanúsítványt, és mentheti a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **DocuSign beállítása** szakaszban másolja a megfelelő URL-címet (vagy URL-címeket) a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy B. Simon nevű teszt felhasználót hoz létre a Azure Portal.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. A képernyő felső részén válassza az **új felhasználó**lehetőséget.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A név mezőbe írja be a **B. Simon** **nevet** .  
   1. A **Felhasználónév** mezőbe írja be a nevet `<username>@<companydomain>.<extension>` . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd jegyezze fel a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban B. Simon hozzáférést biztosít a DocuSign, így ez a felhasználó használhatja az Azure egyszeri bejelentkezést.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **DocuSign**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok**lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a **felhasználók** listából a **B. Simon** lehetőséget, majd a képernyő alján kattintson a **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-docusign-sso"></a>DocuSign SSO konfigurálása

1. A DocuSign konfigurációjának automatizálásához telepítenie kell a saját alkalmazások biztonságos bejelentkezési böngésző bővítményét a **bővítmény telepítése**lehetőség kiválasztásával.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, válassza a **telepítő DocuSign**elemet. A rendszer átirányítja a DocuSign alkalmazást. Itt adja meg a rendszergazdai hitelesítő adatokat a DocuSign való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja a 3 – 5. lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a DocuSign, nyisson meg egy új böngészőablakot, és jelentkezzen be a DocuSign vállalati webhelyére rendszergazdaként.

4. A lap jobb felső sarkában válassza ki a profil emblémáját, majd válassza az **Ugrás a rendszergazdához**lehetőséget.
  
    ![Nyissa meg a rendszergazdát a profil területen][51]

5. A tartományi megoldások lapon válassza a **tartományok**lehetőséget.

    ![Tartományi megoldások/tartományok][50]

6. A **tartományok** szakaszban válassza a **jogcím tartomány**elemet.

    ![Jogcím-tartomány lehetőség][52]

7. A **jogcím a tartományhoz** párbeszédpanelen a **tartomány neve** mezőbe írja be a vállalati tartományt, majd válassza a **jogcím**elemet. Győződjön meg arról, hogy a tartomány ellenőrzése megtörténik, és hogy az állapota aktív.

    ![Tartomány/tartomány neve párbeszédpanel igénylése][53]

8. A tartományi megoldások lapon válassza az **identitás-szolgáltatók**elemet.
  
    ![Identitás-szolgáltatók lehetőség][54]

9. Az **identitás-szolgáltatók** szakaszban válassza az **identitás-szolgáltató hozzáadása**elemet.

    ![Identitás-szolgáltató hozzáadása lehetőség][55]

10. Az **Identity Provider beállításai** lapon kövesse az alábbi lépéseket:

    ![Identity Provider beállítások mezői][56]

    a. A **név** mezőbe írjon be egy egyedi nevet a konfigurációnak. Ne használjon szóközt.

    b. Az **Identity Provider kiállító mezőjébe**illessze be az **Azure ad-azonosító** értékét, amelyet a Azure Portal másolt.

    c. Az **Identity Provider bejelentkezési URL-címe** mezőbe illessze be a **bejelentkezési URL-címet** , amelyet a Azure Portalból másolt.

    d. Az **Identity Provider kijelentkezési URL-címe** mezőbe illessze be a **KIjelentkezési URL-cím**értékét, amelyet a Azure Portalból másolt.

    e. Válassza a **AuthN-kérelem aláírása**lehetőséget.

    f. A **AuthN-kérelem küldéséhez**válassza a **post**lehetőséget.

    : A **kijelentkezési kérelmének elküldéséhez**válassza a **beolvasás**lehetőséget.

    h. Az **Egyéni attribútumok leképezése** szakaszban válassza az **Új leképezés hozzáadása**elemet.

       ![Egyéni attribútumok leképezése felhasználói felület][62]

    i. Válassza ki azt a mezőt, amelyet az Azure AD-jogcímhez szeretne rendelni. Ebben a példában az **EmailAddress** jogcím a következő értékkel van leképezve: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` . Ez az alapértelmezett jogcím neve az Azure AD-től az e-mail-jogcímhez. Válassza a **Mentés**lehetőséget.

       ![Egyéni attribútum-leképezési mezők][57]

       > [!NOTE]
       > A megfelelő **felhasználói azonosító** használatával rendelje hozzá a felhasználót az Azure ad-ből DocuSign felhasználói leképezéshez. Válassza ki a megfelelő mezőt, és adja meg a megfelelő értéket a szervezeti beállítások alapján.

    j. Az **Identity Provider-tanúsítványok** szakaszban válassza a **tanúsítvány hozzáadása**lehetőséget, töltse fel az Azure ad-portálról letöltött tanúsítványt, és kattintson a **Mentés**gombra.

       ![Identitás-szolgáltató tanúsítványainak/tanúsítvány hozzáadása][58]

    k. Az **Identity Providers** szakaszban válassza a **műveletek**, majd a **végpontok**lehetőséget.

       ![Identitás-szolgáltatók/végpontok][59]

    l. A DocuSign felügyeleti portál **SAML 2,0-végpontok megtekintése** szakaszában kövesse az alábbi lépéseket:

       ![SAML 2,0-végpontok megtekintése][60]
       
       1. Másolja a **szolgáltatói kiállító URL-címét**, majd illessze be a Azure Portal **ALAPszintű SAML-konfigurációjának** **azonosító** mezőjébe.
       
       1. Másolja a szolgáltatói szolgáltatáshoz tartozó **fogyasztói szolgáltatás URL-címét**, majd illessze be a **Válasz URL-cím** mezőbe az **alapszintű SAML-konfiguráció** szakaszban a Azure Portal.
       
       1. Másolja a **szolgáltatói bejelentkezési URL-címet**, majd illessze be a **bejelentkezési URL-cím** mezőbe az **alapszintű SAML-konfiguráció** szakaszban a Azure Portal. A **szolgáltató bejelentkezési URL-címének** végén a IDPID értéket fogja kapni.

       1. Válassza a **Bezárás** lehetőséget.

### <a name="create-docusign-test-user"></a>DocuSign-tesztelési felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre a DocuSign-ben. A DocuSign támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a DocuSign-ben, a rendszer egy újat hoz létre a hitelesítés után.

> [!Note]
> Ha manuálisan kell létrehoznia egy felhasználót, lépjen kapcsolatba a [DocuSign támogatási csapatával](https://support.docusign.com/).

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Amikor kiválasztja a DocuSign csempét a hozzáférési panelen, automatikusan be kell jelentkeznie arra a DocuSign-példányra, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További erőforrások

- [Oktatóanyagok az SaaS-alkalmazások Azure AD-vel való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure AD-ben? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure AD-ben?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A DocuSign kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

<!--Image references-->

[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png
