---
title: 'Oktatóanyag: Azure Active Directory integráció a Symantec Web Security szolgáltatással (WSS) | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a Symantec Web Security szolgáltatás (WSS) között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.openlocfilehash: 230f7fd9c62f657ce8ab893db2256808dce9a7ba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92518364"
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Oktatóanyag: Azure Active Directory integráció a Symantec Web Security szolgáltatással (WSS)

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Symantec Web Security szolgáltatás (WSS) fiókját a Azure Active Directory (Azure AD) fiókkal, hogy a WSS hitelesítse az Azure AD-ben kiépített végfelhasználót az SAML-hitelesítéssel, és kényszerítse a felhasználói vagy a csoportokra vonatkozó házirend-szabályokat.

A Symantec Web Security szolgáltatás (WSS) és az Azure AD integrálásával a következő előnyöket nyújtja:

- Kezelheti az Azure AD-portálon a WSS-fiók által használt összes végfelhasználót és csoportot.

- Lehetővé teszi a végfelhasználók számára, hogy saját Azure AD-beli hitelesítő adataikat használva hitelesítsék magukat a WSS-ben.

- A WSS-fiókban definiált felhasználói és csoportszint házirend-szabályok betartatásának engedélyezése.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció a Symantec Web Security szolgáltatással (WSS) való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Symantec Web Security szolgáltatás (WSS) egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Symantec Web Security szolgáltatás (WSS) támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

## <a name="adding-symantec-web-security-service-wss-from-the-gallery"></a>Symantec Web Security szolgáltatás (WSS) hozzáadása a katalógusból

A Symantec Web Security szolgáltatás (WSS) Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Symantec Web Security szolgáltatást (WSS) a katalógusból a felügyelt SaaS-alkalmazások listájához.

**A Symantec Web Security szolgáltatás (WSS) katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Symantec Web Security szolgáltatás (WSS)** kifejezést, válassza a **Symantec Web Security szolgáltatás (WSS)** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Symantec Web Security szolgáltatás (WSS) az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli a Symantec Web Security szolgáltatással (WSS) a **Britta Simon** nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Symantec Web Security szolgáltatásban (WSS) kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés a Symantec Web Security szolgáltatással (WSS) való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. A **Symantec Web Security szolgáltatás (WSS) egyszeri bejelentkezésének konfigurálása** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. A **[Symantec Web Security szolgáltatás (WSS) tesztelési felhasználójának létrehozása](#create-symantec-web-security-service-wss-test-user)** – hogy a Britta Simon a Symantec Web Security szolgáltatásban (WSS) legyen, amely a felhasználó Azure ad-képviseletéhez van társítva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés a Symantec Web Security szolgáltatással (WSS) való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **Symantec webes biztonsági szolgáltatás (WSS)** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![A Symantec Web Security szolgáltatás (WSS) tartománya és URL-címek egyszeri bejelentkezési adatai](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be az URL-címet: `https://saml.threatpulse.net:8443/saml/saml_realm`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet: `https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > Kapcsolatfelvétel a [Symantec Web Security szolgáltatás (WSS) ügyfél-támogatási csapatával](https://www.symantec.com/contact-us) f az **azonosító** és a **Válasz URL-címének** értékei nem működnek valamilyen okból. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

### <a name="configure-symantec-web-security-service-wss-single-sign-on"></a>A Symantec Web Security szolgáltatás (WSS) egyszeri Sign-On konfigurálása

Ha az egyszeri bejelentkezést a Symantec Web Security szolgáltatás (WSS) oldalán szeretné konfigurálni, tekintse meg a WSS online dokumentációját. A letöltött **összevonási metaadatok XML-fájlját** importálni kell a WSS-portálra. Ha segítségre van szüksége a WSS-portálon való konfiguráláshoz, forduljon a [Symantec Web Security szolgáltatás (WSS) támogatási csapatához](https://www.symantec.com/contact-us) .

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a **brittasimon \@ yourcompanydomain. Extension** nevet  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban lehetővé teszi, hogy Simon Britta az Azure egyszeri bejelentkezés használatára a Symantec Web Security szolgáltatás (WSS) hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd kattintson a **Symantec Web Security szolgáltatás (WSS)** elemre.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában írja be és válassza a **Symantec Web Security szolgáltatás (WSS)** elemet.

    ![A Symantec Web Security szolgáltatás (WSS) hivatkozása az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-symantec-web-security-service-wss-test-user"></a>Symantec Web Security szolgáltatás (WSS) tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a Symantec Web Security szolgáltatásban (WSS). A megfelelő végfelhasználói Felhasználónév manuálisan hozható létre a WSS-portálon, vagy megvárhatja, hogy az Azure AD-ben kiépített felhasználók/csoportok néhány perc múlva (~ 15 perc) szinkronizálva legyenek a WSS Portalon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat. A végfelhasználói számítógép nyilvános IP-címe, amelyet a webhelyek tallózásához kell használni, a Symantec Web Security szolgáltatás (WSS) portálon is üzembe kell helyezni.

> [!NOTE]
> Kattintson [ide](https://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1) a gép nyilvános IP-címe beszerzéséhez.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelheti az egyszeri bejelentkezés funkcióját most, hogy konfigurálta a WSS-fiókját az Azure AD-t az SAML-hitelesítéshez.

Miután konfigurálta a webböngészőt a WSS-alapú forgalomra, amikor megnyitja a webböngészőt, és megpróbál megkeresni egy webhelyet, a rendszer átirányítja az Azure bejelentkezési oldalára. Adja meg az Azure AD-ben (azaz BrittaSimon) és a társított jelszóban kiépített tesztelési végfelhasználó hitelesítő adatait. A hitelesítés után megkeresheti a kiválasztott webhelyet. Ha olyan házirend-szabályt hoz létre a WSS oldalon, amely letiltja a BrittaSimon egy adott helyhez való böngészést, akkor a WSS-blokk lapot kell látnia, amikor a webhelyet felhasználói BrittaSimon próbálja meg megkeresni.

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)