---
title: 'Oktatóanyag: Azure Active Directory webMethods-integráció Integration Suite | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory és a webMethods Integration Suite.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2021
ms.author: jeedes
ms.openlocfilehash: c3eaac19ffbf3fd93311073ff69e28532b1c15e6
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520054"
---
# <a name="tutorial-azure-active-directory-integration-with-webmethods-integration-suite"></a>Oktatóanyag: Azure Active Directory webMethods-integráció Integration Suite

Ez az oktatóanyag bemutatja, hogyan integrálhatja a webMethods-Integration Suite a Azure Active Directory (Azure AD) használatával. Ha az Azure AD-Integration Suite webMethods-et, a következő funkciókat használhatja:

* Az Azure AD vezérlése, aki hozzáfér a webMethods Integration Suite.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve jelentkeznek be a webMethods szolgáltatásba Integration Suite Azure AD-fiókjukkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésekhez a következő elemekre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes fiókot [is kaphat.](https://azure.microsoft.com/free/)
* A webMethods Integration Suite egyszeri bejelentkezést (SSO) engedélyező előfizetést.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD egyszeri bejelentkezését konfigurálja és teszteli tesztkörnyezetben.

* A webMethods Integration Suite **támogatja** az sp és az **idP által** kezdeményezett SSO-t.

* A webMethods Integration Suite **a felhasználóátépítést** is támogatja.

## <a name="add-webmethods-integration-suite-from-the-gallery"></a>WebMethods-Integration Suite hozzáadása a katalógusból

A webMethods Integration Suite Azure AD-be való integrálásának konfigurálához hozzá kell adni a webMethods Integration Suite-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal vagy személyes fiókkal Microsoft-fiók.
1. A bal oldali navigációs panelen válassza ki **a Azure Active Directory** szolgáltatást.
1. Lépjen a Vállalati **alkalmazások lapra,** majd válassza a **Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az Új **alkalmazás lehetőséget.**
1. A Hozzáadás **a katalógusból szakaszban** írja be a **webMethods Integration Suite** a keresőmezőbe.
1. Válassza **a webMethods Integration Suite** az eredmények panelén, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzá lesz adva a bérlőhöz.

## <a name="configure-and-test-azure-ad-sso-for-webmethods-integration-suite"></a>Az Azure AD SSO konfigurálása és tesztelése webMethods Integration Suite

Konfigurálja és tesztelje az Azure AD SSO-t a webMethods Integration Suite egy **B.Simon** nevű tesztfelhasználóval. Ahhoz, hogy az SSO működjön, kapcsolati kapcsolatot kell létesítenie egy Azure AD-felhasználó és a webMethods szolgáltatásban található kapcsolódó Integration Suite.

Ha az Azure AD SSO-t webMethods Integration Suite konfigurálni és tesztelni, hajtsa végre a következő lépéseket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy a felhasználók használják ezt a funkciót.
    1. **[Azure AD-tesztfelhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének tesztelése a B.Simon használatával.
    1. **[Rendelje hozzá az Azure AD-tesztfelhasználót](#assign-the-azure-ad-test-user)** – ezzel engedélyezheti a B.Simon számára az Azure AD egyszeri bejelentkezés használatát.
1. **[Konfigurálja a webMethods Integration Suite SSO-t](#configure-webmethods-integration-suite-sso)** – az egyszeri bejelentkezési beállítások alkalmazásoldali konfigurálásához.
    1. **[WebMethods](#create-webmethods-integration-suite-test-user)** Integration Suite tesztfelhasználó számára – hogy a B.Simon megfelelője legyen a webMethods Integration Suite, amely a felhasználó Azure AD-reprezentációjával van összekapcsolva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzéséhez, hogy működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez a Azure Portal.

1. A Azure Portal **webMethods** Integration Suite alkalmazásintegráció oldalán keresse meg  a Kezelés szakaszt, és válassza az egyszeri **bejelentkezés lehetőséget.**
1. Az Egyszeri **bejelentkezési módszer kiválasztása lapon** válassza az **SAML lehetőséget.**
1. A Set **up single sign-on with SAML** (Egyszeri bejelentkezés beállítása SAML-sel) oldalon kattintson az **SamL-konfiguráció** alapszintű konfigurációja ceruza ikonjára a beállítások szerkesztéséhez.

   ![SamL alapszintű konfigurációjának szerkesztése](common/edit-urls.png)

4. Ha a **webMethods integrációs** felhőt () az SamL-alapkonfiguráció szakaszban szeretné konfigurálni, akkor ha az alkalmazást **idP** által kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket: 

    a. Az Azonosító **szövegmezőbe** írjon be egy URL-címet az alábbi minták egyikével:

    | Azonosító URL-címe |
    |----------------------------------------------|
    | `<SUBDOMAIN>.webmethodscloud.com`|
    | `<SUBDOMAIN>.webmethodscloud.eu` |
    | `<SUBDOMAIN>.webmethodscloud.de` |
    |

    b. A Válasz **URL-cím** szövegmezőbe írjon be egy URL-címet az alábbi minták egyikével:

    | Válasz URL-cím |
    |----------------------------------------------|
    | `https://<SUBDOMAIN>.webmethodscloud.com/integration/live/saml/ssoResponse`|
    | `https://<SUBDOMAIN>.webmethodscloud.eu/integration/live/saml/ssoResponse`|
    | `https://<SUBDOMAIN>.webmethodscloud.de/integration/live/saml/ssoResponse`|
    |

    c. Kattintson **a További URL-címek beállítása lehetőségre,** és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban szeretné konfigurálni:

    d. A **Bejelentkezési URL-cím** szövegmezőbe írjon be egy URL-címet az alábbi minták egyikével:

    | Bejelentkezési URL-cím |
    |--------------------------------|
    |`https://<SUBDOMAIN>.webmethodscloud.com/integration/live/saml/ssoRequest`|
    |`https://<SUBDOMAIN>.webmethodscloud.eu/integration/live/saml/ssoRequest`|
    |`https://<SUBDOMAIN>.webmethodscloud.de/integration/live/saml/ssoRequest`|
    |

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címével. Ezeket az értékeket a [webMethods Integration Suite az](https://empower.softwareag.com/) ügyféltámogatási csapattól. Az **SAML** alapkonfiguráció szakaszában látható mintákra is hivatkozhat a Azure Portal.

5. Ha a **webMethods API Cloudot** szeretné konfigurálni az **SamL-konfiguráció** alapszintű szakaszában, akkor ha az alkalmazást **idP** által kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    a. Az Azonosító **szövegmezőbe** írjon be egy URL-címet az alábbi minták egyikével:

    | Azonosító URL-címe |
    |----------------------------------------------|
    | `<SUBDOMAIN>.webmethodscloud.com`|
    |`<SUBDOMAIN>.webmethodscloud.eu`|
    | `<SUBDOMAIN>.webmethodscloud.de`|
    |

    b. A Válasz **URL-cím** szövegmezőbe írjon be egy URL-címet az alábbi minták egyikével:

    | Válasz URL-cím |
    |----------------------------------------------|
    | `https://<SUBDOMAIN>.webmethodscloud.com/umc/rest/saml/initsso`|
    | `https://<SUBDOMAIN>.webmethodscloud.eu/umc/rest/saml/initsso`|
    | `https://<SUBDOMAIN>.webmethodscloud.de/umc/rest/saml/initsso`|
    |

    c. Kattintson **a További URL-címek beállítása lehetőségre,** és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban szeretné konfigurálni:

    d. A **Bejelentkezési URL-cím** szövegmezőbe írjon be egy URL-címet az alábbi minták egyikével:
    
    | Bejelentkezési URL-cím |
    |--------------------------------|
    | `https://api.webmethodscloud.com/umc/rest/saml/initsso/?tenant=<TENANTID>`|
    | `https://api.webmethodscloud.eu/umc/rest/saml/initsso/?tenant=<TENANTID>`|
    | `https://api.webmethodscloud.de/umc/rest/saml/initsso/?tenant=<TENANTID>`|
    |

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címével. Ezeket az értékeket a [webMethods Integration Suite az](https://empower.softwareag.com/) ügyféltámogatási csapattól. Az **SAML** alapkonfiguráció szakaszában látható mintákra is hivatkozhat a Azure Portal.

6. A **Set up Single Sign-On with SAML** (Egyszeri bejelentkezés beállítása SAML-tanúsítványsal) lapon, az **SAML signing Certificate (SAML-aláíró** tanúsítvány) szakaszban kattintson a **Download** (Letöltés) elemre az összevonási metaadatok **XML-fájljának** letöltéséhez a megadott beállításokból igény szerint, majd mentse a számítógépre.

    ![A Tanúsítvány letöltése hivatkozás](common/metadataxml.png)

7. A **WebMethods beállítása Integration Suite** igény szerint másolja a megfelelő URL-címet/URL-címeket.

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

Ebben a szakaszban engedélyezni fogja a B.Simon számára az Azure-beli egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a webMethods Integration Suite.

1. A Azure Portal válassza a **Vállalati alkalmazások,** majd a **Minden alkalmazás lehetőséget.**
1. Az alkalmazások listájában válassza a **webMethods Integration Suite.**
1. Az alkalmazás áttekintő oldalán keresse meg a Kezelés **szakaszt,** és válassza a **Felhasználók és csoportok lehetőséget.**
1. Válassza **a Felhasználó hozzáadása** lehetőséget, majd a Hozzárendelés hozzáadása **párbeszédpanelen válassza a** Felhasználók és **csoportok** lehetőséget.
1. A Felhasználók **és csoportok** párbeszédpanelen válassza a **B.Simon**  lehetőséget a Felhasználók listában, majd kattintson a képernyő alján található Kijelölés gombra.
1. Ha azt várja, hogy egy szerepkör hozzá lesz rendelve a felhasználókhoz, kiválaszthatja **azt a Szerepkör kiválasztása** legördülő menüből. Ha nincs beállítva szerepkör ehhez az alkalmazáshoz, az "Alapértelmezett hozzáférés" szerepkör van kiválasztva.
1. A Hozzárendelés **hozzáadása párbeszédpanelen** kattintson a Hozzárendelés **gombra.**

## <a name="configure-webmethods-integration-suite-sso"></a>WebMethods konfigurálása Integration Suite SSO-val

Az egyszeri bejelentkezés **webMethods** Integration Suite oldalán való konfigurálához el kell küldenie a letöltött összevonási metaadatok XML-ét és a megfelelő másolt **URL-címeket** a Azure Portal-ből a [webMethods Integration Suite támogatási csapatának.](https://empower.softwareag.com/) Ezt a beállítást úgy állítják be, hogy az SAML SSO-kapcsolat megfelelően legyen beállítva mindkét oldalon.

### <a name="create-webmethods-integration-suite-test-user"></a>WebMethods létrehozása Integration Suite tesztfelhasználóhoz

Ebben a szakaszban egy Britta Simon nevű felhasználót hozunk létre a webMethods Integration Suite. A webMethods Integration Suite támogatja az alapértelmezés szerint engedélyezett, felhasználóhoz való, időben való kiépítést. Ebben a szakaszban nem található műveletelem. Ha a felhasználó még nem létezik a webMethods Integration Suite, a hitelesítés után létrejön egy új.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az alábbi beállításokkal teszteli az Azure AD egyszeri bejelentkezési konfigurációját. 

#### <a name="sp-initiated"></a>Sp kezdeményezve:

* Kattintson az **Alkalmazás tesztelése elemre a** Azure Portal. Ez átirányítja a webMethods webhelyre, Integration Suite bejelentkezési URL-címet, ahol elindíthatja a bejelentkezési folyamatot.  

* Lépjen a webMethods Integration Suite a bejelentkezési URL-cím közvetlen útjára, és onnan indítsa el a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>Az internetszolgáltató által kezdeményezett:

* Kattintson az **Alkalmazás tesztelése a** Azure Portal lehetőségre, és automatikusan be kell jelentkezve lennie a webMethods Integration Suite, amelyhez beállította az SSO-t. 

A Microsoft Saját alkalmazások bármilyen módban tesztelheti az alkalmazást. Ha a Saját alkalmazások-ban a webMethods Integration Suite csempére kattint, akkor ha SP módban van konfigurálva, a rendszer átirányítja az alkalmazás bejelentkezési oldalára a bejelentkezési folyamat kezdeményezésére, és ha idP módban van konfigurálva, automatikusan be kell jelentkeznie a webMethods Integration Suite-be, amelyhez beállította az SSO-t. További információ a Saját alkalmazások: [Bevezetés a](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)Saját alkalmazások.

## <a name="next-steps"></a>Következő lépések

A webMethods Integration Suite kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlés a feltételes hozzáféréstől terjed ki. [Ismerje meg, hogyan kényszeríthető ki a munkamenet-vezérlés a Microsoft Cloud App Security.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)
