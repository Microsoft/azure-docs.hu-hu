---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrálása a ProcessUnity | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory És a ProcessUnity között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/09/2021
ms.author: jeedes
ms.openlocfilehash: bf38a013dbe59fdc4be646264840fe8906d0f11b
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520937"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-processunity"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrálása a ProcessUnityvel

Ez az oktatóanyag bemutatja, hogyan integrálhatja a ProcessUnityt a Azure Active Directory (Azure AD) használatával. A ProcessUnity és az Azure AD integrálása során a következő funkciókat használhatja:

* Az Azure AD vezérlése, aki hozzáféréssel rendelkezik a ProcessUnity szolgáltatáshoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve jelentkeznek be a ProcessUnity szolgáltatásba az Azure AD-fiókjukkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésekhez a következő elemekre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes fiókot [is kaphat.](https://azure.microsoft.com/free/)
* ProcessUnity egyszeri bejelentkezést (SSO) engedélyező előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD SSO-t.

* A ProcessUnity támogatja **az sp és az idP által** kezdeményezett SSO-t.
* A ProcessUnity támogatja **az időben való felhasználóátépítést.**

## <a name="add-processunity-from-the-gallery"></a>ProcessUnity hozzáadása a katalógusból

A ProcessUnity Azure AD-be való integrálásának konfigurálához hozzá kell adni a ProcessUnity adatokat a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal vagy személyes fiókkal Microsoft-fiók.
1. A bal oldali navigációs panelen válassza ki **a Azure Active Directory** szolgáltatást.
1. Lépjen a Vállalati **alkalmazások lapra,** majd válassza a **Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az Új **alkalmazás lehetőséget.**
1. A Hozzáadás **a katalógusból szakaszban** írja be a **ProcessUnity (Folyamatunitás)** adatokat a keresőmezőbe.
1. Válassza **a ProcessUnity (Folyamatunitás)** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzá lesz adva a bérlőhöz.

## <a name="configure-and-test-azure-ad-sso-for-processunity"></a>Az Azure AD SSO konfigurálása és tesztelése a ProcessUnity szolgáltatáshoz

Konfigurálja és tesztelje az Azure AD SSO-t a ProcessUnity használatával egy **B.Simon nevű tesztfelhasználóval.** Ahhoz, hogy az SSO működjön, kapcsolati kapcsolatot kell létesítenie egy Azure AD-felhasználó és a kapcsolódó felhasználó között a ProcessUnity (Folyamatunitás) alatt.

Az Azure AD SSO ProcessUnity beállításához és teszteléshez hajtsa végre a következő lépéseket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy a felhasználók használják ezt a funkciót.
    1. **[Azure AD-tesztfelhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének tesztelése a B.Simon használatával.
    1. **[Rendelje hozzá az Azure AD-tesztfelhasználót](#assign-the-azure-ad-test-user)** – ezzel engedélyezheti a B.Simon számára az Azure AD egyszeri bejelentkezés használatát.
1. **[A ProcessUnity SSO konfigurálása](#configure-processunity-sso)** – az egyszeri bejelentkezési beállítások alkalmazásoldalon való konfigurálása.
    1. **[ProcessUnity-tesztfelhasználó](#create-processunity-test-user)** létrehozása – a B.Simon megfelelője a ProcessUnityben, amely a felhasználó Azure AD-reprezentációjával van összekapcsolva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzéséhez, hogy működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez a Azure Portal.

1. A Azure Portal **ProcessUnity** alkalmazásintegráció lapján keresse meg a **Kezelés** szakaszt, és válassza az **egyszeri bejelentkezés lehetőséget.**
1. Az Egyszeri **bejelentkezési módszer kiválasztása lapon** válassza az **SAML lehetőséget.**
1. A Set **up single sign-on with SAML** (Egyszeri bejelentkezés beállítása SAML-sel) oldalon kattintson az **SamL-konfiguráció** alapszintű konfigurációja ceruza ikonjára a beállítások szerkesztéséhez.

   ![SamL alapszintű konfigurációjának szerkesztése](common/edit-urls.png)

1. Az **SAML-alapkonfiguráció** szakaszban, ha az alkalmazást **idP** által kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    a. Az Azonosító **szövegmezőbe** írjon be egy URL-címet a következő mintával: `https://<SUBDOMAIN>.processunity.net/<DOMAIN_NAME>`

    b. A Válasz **URL-cím** szövegmezőbe írjon be egy URL-címet a következő mintával: `https://<SUBDOMAIN>.processunity.net/<DOMAIN_NAME>/SAML/AssertionConsumerServiceV2.aspx`

1. Kattintson **a További URL-címek beállítása lehetőségre,** és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban szeretné konfigurálni:

    A **Bejelentkezési URL-cím** szövegmezőbe írjon be egy URL-címet a következő mintával:  `https://<SUBDOMAIN>.processunity.net/<DOMAIN_NAME>/SAML/SamlLoginV2.aspx`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címével. Ezeket az értékeket a [ProcessUnity-ügyfél](mailto:customer.support@processunity.com) támogatási csapatával vegye fel a kapcsolatot. Az **SAML** alapkonfiguráció szakaszában látható mintákra is hivatkozhat a Azure Portal.

1. Az Egyszeri bejelentkezés beállítása **SAML-sel** lap **SAML** aláíró tanúsítvány szakaszában keresse  meg az Összevonási metaadatok XML-fájlját, majd válassza a Letöltés lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez. 

    ![A Tanúsítvány letöltése hivatkozás](common/metadataxml.png)

1. A **Set up ProcessUnity (Folyamatunitás** beállítása) szakaszban másolja ki a követelménynek megfelelő URL-címet/URL-címeket.

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

Ebben a szakaszban engedélyezheti a B.Simon számára az Azure-beli egyszeri bejelentkezés használatát úgy, hogy hozzáférést ad a ProcessUnity szolgáltatáshoz.

1. A Azure Portal válassza a Vállalati **alkalmazások,** majd a Minden **alkalmazás lehetőséget.**
1. Az alkalmazások listájában válassza a **FolyamatUnity lehetőséget.**
1. Az alkalmazás áttekintő oldalán keresse meg a Kezelés **szakaszt,** és válassza a **Felhasználók és csoportok lehetőséget.**
1. Válassza **a Felhasználó hozzáadása** lehetőséget, majd a Hozzárendelés hozzáadása **párbeszédpanelen válassza** a Felhasználók és **csoportok** lehetőséget.
1. A Felhasználók **és csoportok** párbeszédpanelen válassza a **B.Simon**  lehetőséget a Felhasználók listában, majd kattintson a képernyő alján található Kijelölés gombra.
1. Ha azt várja, hogy egy szerepkör hozzá lesz rendelve a felhasználókhoz, kiválaszthatja azt **a Szerepkör kiválasztása** legördülő menüből. Ha nincs beállítva szerepkör ehhez az alkalmazáshoz, az "Alapértelmezett hozzáférés" szerepkör lesz kiválasztva.
1. A Hozzárendelés **hozzáadása párbeszédpanelen** kattintson a Hozzárendelés **gombra.**

## <a name="configure-processunity-sso"></a>A ProcessUnity SSO konfigurálása

Az egyszeri bejelentkezés **ProcessUnity** oldalán való konfigurálásához el kell küldenie a letöltött összevonási metaadatok XML-fájlját és a megfelelő másolt URL-címeket a Azure Portal [FolyamatUnity támogatási csapatának.](mailto:customer.support@processunity.com)  Ezt a beállítást úgy állítják be, hogy az SAML SSO-kapcsolat megfelelően legyen beállítva mindkét oldalon.

### <a name="create-processunity-test-user"></a>ProcessUnity-tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hozunk létre a ProcessUnity területen. A ProcessUnity támogatja az alapértelmezés szerint engedélyezett, időponthoz időben való felhasználóátépítést. Ebben a szakaszban nem található műveletelem. Ha a felhasználó még nem létezik a ProcessUnity szolgáltatásban, a hitelesítés után létrejön egy új.

## <a name="test-sso"></a>Az SSO tesztelése 

Ebben a szakaszban az alábbi beállításokkal teszteli az Azure AD egyszeri bejelentkezési konfigurációját. 

#### <a name="sp-initiated"></a>Sp kezdeményezve:

* Kattintson az **Alkalmazás tesztelése elemre a** Azure Portal. Ez átirányítja a ProcessUnity bejelentkezési URL-címre, ahol elindíthatja a bejelentkezési folyamatot.  

* Lépjen közvetlenül a ProcessUnity bejelentkezési URL-címére, és onnan indítsa el a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>Az internetszolgáltató által kezdeményezett:

* Kattintson az **Alkalmazás tesztelése a** Azure Portal lehetőségre, és automatikusan be kell jelentkezve a ProcessUnity szolgáltatásba, amelyhez beállította az SSO-t. 

A Microsoft Saját alkalmazások bármilyen módban tesztelheti az alkalmazást. Ha az Saját alkalmazások-ban a ProcessUnity csempére kattint, a rendszer átirányítja az alkalmazás bejelentkezési oldalára a bejelentkezési folyamat kezdeményezésére, és ha idP módban van konfigurálva, automatikusan be kell jelentkeznie arra a ProcessUnity szolgáltatásra, amelyhez az SSO-t beállította. További információ a Saját alkalmazások: [Bevezetés a](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)Saját alkalmazások.

## <a name="next-steps"></a>Következő lépések

A ProcessUnity konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlés a feltételes hozzáféréstől terjed ki. [Ismerje meg, hogyan kényszeríthető ki a munkamenet-vezérlés a Microsoft Cloud App Security.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)
