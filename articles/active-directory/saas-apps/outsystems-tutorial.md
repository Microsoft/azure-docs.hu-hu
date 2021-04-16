---
title: 'Oktatóanyag: Azure Active Directory OutSystems Azure AD-| Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory és az OutSystems Azure AD között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/06/2021
ms.author: jeedes
ms.openlocfilehash: 6a4a2f4edb3fcc56bafb3847280958a7803ba3bd
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519867"
---
# <a name="tutorial-integrate-outsystems-azure-ad-with-azure-active-directory"></a>Oktatóanyag: Az OutSystems Azure AD integrálása Azure Active Directory

Ez az oktatóanyag bemutatja, hogyan integrálhatja az OutSystems Azure AD-t az Azure Active Directory (Azure AD) használatával. Ha integrálja az OutSystems Azure AD-t az Azure AD-val, a következő funkciókat használhatja:

* Az Azure AD vezérlése, aki hozzáféréssel rendelkezik az OutSystems Azure AD-hez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve jelentkeznek be az OutSystems Azure AD-be az Azure AD-fiókjukkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésekhez a következő elemekre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes fiókot [is kaphat.](https://azure.microsoft.com/free/)
* OutSystems Azure AD egyszeri bejelentkezést (SSO) engedélyező előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD SSO-t.

* Az OutSystems Azure AD támogatja az SP és az **IDP** által kezdeményezett SSO-t, valamint az **időben való** felhasználóátépítést.

## <a name="add-outsystems-azure-ad-from-the-gallery"></a>OutSystems Azure AD hozzáadása a katalógusból

Az OutSystems Azure AD-nek az Azure AD-be való integrálásának konfigurálához hozzá kell adni az OutSystems Azure AD-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal vagy személyes fiókkal Microsoft-fiók.
1. A bal oldali navigációs panelen válassza ki **a Azure Active Directory** szolgáltatást.
1. Lépjen a Vállalati **alkalmazások lapra,** majd válassza a **Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az Új **alkalmazás lehetőséget.**
1. A Hozzáadás **a katalógusból szakaszban** írja be az **OutSystems Azure AD** kifejezéseket a keresőmezőbe.
1. Válassza **az OutSystems Azure AD** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzá lesz adva a bérlőhöz.

## <a name="configure-and-test-azure-ad-sso-for-outsystems-azure-ad"></a>Az Azure AD SSO konfigurálása és tesztelése outSystems Azure AD-hez

Konfigurálja és tesztelje az Azure AD SSO-t az OutSystems Azure AD-val egy **B.Simon nevű tesztfelhasználóval.** Ahhoz, hogy az SSO működjön, kapcsolati kapcsolatot kell létesítenie egy Azure AD-felhasználó és a kapcsolódó felhasználó között az OutSystems Azure AD-ban.

Ha az Azure AD SSO-t az OutSystems Azure AD-val konfigurálja és teszteli, hajtsa végre a következő lépéseket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy a felhasználók használják ezt a funkciót.
    1. **[Azure AD-tesztfelhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének tesztelése a B.Simon használatával.
    1. **[Rendelje hozzá az Azure AD-tesztfelhasználót](#assign-the-azure-ad-test-user)** – ezzel engedélyezheti a B.Simon számára az Azure AD egyszeri bejelentkezés használatát.
1. **[Az OutSystems Azure AD SSO konfigurálása](#configure-outsystems-azure-ad-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazásoldalon.
    1. **[Hozzon létre OutSystems Azure AD-tesztfelhasználót,](#create-outsystems-azure-ad-test-user)** hogy a B.Simon megfelelője legyen az OutSystems Azure AD-ben, amely a felhasználó Azure AD-reprezentációjával van összekapcsolva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzéséhez, hogy működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez a Azure Portal.

1. A Azure Portal **OutSystems Azure** AD-alkalmazásintegráció lapján keresse meg a **Kezelés** szakaszt, és válassza az **Egyszeri bejelentkezés lehetőséget.**
1. Az Egyszeri **bejelentkezési módszer kiválasztása lapon** válassza az **SAML lehetőséget.**
1. A **Set up Single Sign-On with SAML** (Egyetlen alkalmazás beállítása SAML-sel) lapon kattintson az SamL-alapkonfiguráció ceruza ikonjára a beállítások szerkesztéséhez. 

   ![SamL alapszintű konfigurációjának szerkesztése](common/edit-urls.png)

1. Az **SAML-alapkonfiguráció** szakaszban, ha az alkalmazást **idP** által kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    a. Az Azonosító **szövegmezőbe** írjon be egy URL-címet a következő mintával: `http://<YOURBASEURL>/IdP`

    b. A Válasz **URL-cím** szövegmezőbe írjon be egy URL-címet a következő mintával: `https://<YOURBASEURL>/IdP/SSO.aspx`

1. Kattintson **a További URL-címek beállítása lehetőségre,** és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban szeretné konfigurálni:

    A **Bejelentkezési URL-cím** szövegmezőbe írjon be egy URL-címet a következő mintával:  `https://<YOURBASEURL>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címével. Ezeket [az értékeket az OutSystems-ügyfél](mailto:support@outsystems.com) támogatási csapatával vegye fel a kapcsolatot. Az **SAML** alapkonfiguráció szakaszában látható mintákra is hivatkozhat a Azure Portal.

1. A **Set up Single Sign-On with SAML** (Egyetlen tanúsítvány beállítása SAML-tanúsítványsal)  lapon, az **SAML Signing Certificate (SAML** aláíró tanúsítvány) szakaszban keresse meg az Federation Metadata XML (Összevonási metaadatok XML-fájlja) adatokat, és válassza a Download (Letöltés) lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez. 

   ![A Tanúsítvány letöltése hivatkozás](common/metadataxml.png)

1. Az **OutSystems Azure AD** beállítása szakaszban másolja ki a követelménynek megfelelő URL-címet/URL-címeket.

   ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy B.Simon nevű tesztfelhasználót fog létrehozni a Azure Portal területen.

1. A bal oldali panelen válassza Azure Portal lehetőséget, **Azure Active Directory** **a Felhasználók** lehetőséget, majd válassza a Minden **felhasználó lehetőséget.**
1. Válassza **az Új felhasználó** lehetőséget a képernyő tetején.
1. A Felhasználó **tulajdonságai** között kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév **mezőbe írja** be a következőt: username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket. 
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezheti a B.Simon számára az Azure-beli egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít az OutSystems Azure AD-hez.

1. A Azure Portal válassza a Vállalati **alkalmazások,** majd a Minden **alkalmazás lehetőséget.**
1. Az alkalmazások listájában válassza az **OutSystems Azure AD lehetőséget.**
1. Az alkalmazás áttekintő oldalán keresse meg a Kezelés **szakaszt,** és válassza a **Felhasználók és csoportok lehetőséget.**
1. Válassza **a Felhasználó hozzáadása** lehetőséget, majd a Hozzárendelés hozzáadása **párbeszédpanelen válassza** a Felhasználók és **csoportok** lehetőséget.
1. A Felhasználók **és csoportok** párbeszédpanelen válassza a **B.Simon**  lehetőséget a Felhasználók listában, majd kattintson a képernyő alján található Kijelölés gombra.
1. Ha azt várja, hogy egy szerepkör hozzá lesz rendelve a felhasználókhoz, kiválaszthatja azt **a Szerepkör kiválasztása** legördülő menüből. Ha nincs beállítva szerepkör ehhez az alkalmazáshoz, az "Alapértelmezett hozzáférés" szerepkör lesz kiválasztva.
1. A Hozzárendelés **hozzáadása párbeszédpanelen** kattintson a Hozzárendelés **gombra.**

## <a name="configure-outsystems-azure-ad-sso"></a>OutSystems Azure AD SSO konfigurálása

Az egyszeri bejelentkezés OutSystems oldalon való konfiguráláshoz le kell töltenie az [idP forge](https://www.outsystems.com/forge/component-overview/599/idp) összetevőt, és konfigurálnia kell az [utasításokban említettek szerint.](https://success.outsystems.com/Documentation/Development_FAQs/How_to_configure_OutSystems_to_use_identity_providers_using_SAML#Configure_your_application_to_use_IdP_connector) Miután telepítette az összetevőt, és végrehajtotta a szükséges kódváltozásokat, konfigurálja az Azure AD-t úgy, hogy letölti az összevonási metaadatok XML-fájlját az Azure Portal-ből, és feltölti az OutSystems idP összetevőre az alábbi utasítások [szerint.](https://success.outsystems.com/Documentation/Development_FAQs/How_to_configure_OutSystems_to_use_identity_providers_using_SAML#Azure_AD_.2F_ADFS)

### <a name="create-outsystems-azure-ad-test-user"></a>OutSystems Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy B.Simon nevű felhasználót hozunk létre az OutSystemsben. Az OutSystems támogatja az alapértelmezés szerint engedélyezett, időponthoz időben való felhasználóátépítést. Ebben a szakaszban nem található műveletelem. Ha egy felhasználó még nem létezik az OutSystemsben, a hitelesítés után létrejön egy új.

## <a name="test-sso"></a>Az SSO tesztelése

Ebben a szakaszban az alábbi beállításokkal teszteli az Azure AD egyszeri bejelentkezési konfigurációját. 

#### <a name="sp-initiated"></a>Sp kezdeményezve:

* Kattintson az **Alkalmazás tesztelése elemre** a Azure Portal. Ez átirányítja az OutSystems Azure AD bejelentkezési URL-címre, ahol elindíthatja a bejelentkezési folyamatot.  

* Lépjen közvetlenül az OutSystems Azure AD bejelentkezési URL-címére, és onnan indítsa el a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>Az internetszolgáltató által kezdeményezett:

* Kattintson az **Alkalmazás tesztelése a** Azure Portal lehetőségre, és automatikusan be kell jelentkezve lennie az OutSystems Azure AD-be, amelyhez beállította az SSO-t. 

A Microsoft Saját alkalmazások bármilyen módban tesztelheti az alkalmazást. Ha az outSystems Azure AD csempére kattint az Saját alkalmazások-ban, és SP módban van konfigurálva, a rendszer átirányítja az alkalmazás bejelentkezési oldalára a bejelentkezési folyamat kezdeményezésére, és ha idP módban van konfigurálva, automatikusan be kell jelentkeznie arra az OutSystems Azure AD-be, amelyhez beállította az SSO-t. További információ a Saját alkalmazások: [Bevezetés a](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)Saját alkalmazások.

## <a name="next-steps"></a>Következő lépések

Az OutSystems Azure AD konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlés a feltételes hozzáféréstől terjed ki. [Ismerje meg, hogyan kényszeríthető ki a munkamenet-vezérlés a Microsoft Cloud App Security.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)
