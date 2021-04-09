---
title: 'Oktatóanyag: Azure Active Directory integráció a xMatters OnDemand | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és xMatters OnDemand között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/19/2020
ms.author: jeedes
ms.openlocfilehash: 762bd1c536df0ca307149ba7c201f08f5bdfded5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735608"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Oktatóanyag: Azure Active Directory integráció a xMatters OnDemand

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a xMatters-OnDemand a Azure Active Directory (Azure AD) szolgáltatással.
A xMatters OnDemand és az Azure AD integrálásával az alábbi előnyökkel jár:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a xMatters OnDemand.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a xMatters OnDemand (egyszeri bejelentkezés) az Azure AD-fiókokkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció xMatters OnDemand való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* xMatters OnDemand egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* a xMatters OnDemand támogatja a **identitásszolgáltató** által KEZDEMÉNYEZett SSO-t

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>XMatters-OnDemand hozzáadása a gyűjteményből

A xMatters-OnDemand Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a xMatters-OnDemand a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **xMatters OnDemand** kifejezést a keresőmezőbe.
1. Válassza ki a **XMatters OnDemand** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-xmatters-ondemand"></a>Azure AD SSO konfigurálása és tesztelése xMatters-OnDemand

Konfigurálja és tesztelje az Azure AD SSO-t a xMatters OnDemand egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a xMatters OnDemand.

Az Azure AD SSO xMatters-OnDemand való konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
    2. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
2. **[XMatters-ONDEMAND SSO konfigurálása](#configure-xmatters-ondemand-sso)** – az egyes Sign-On beállításainak konfigurálása az alkalmazás oldalán.
    1. **[XMatters-OnDemand-teszt felhasználó létrehozása](#create-xmatters-ondemand-test-user)** – a Britta Simon-nek a felhasználó Azure ad-képviseletéhez kapcsolódó XMatters-OnDemand.
3. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **XMatters OnDemand** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minták egyikének használatával:

    | Azonosító |
    | ---------- |
    | `https://<companyname>.au1.xmatters.com.au/` |
    | `https://<companyname>.cs1.xmatters.com/` |
    | `https://<companyname>.xmatters.com/` |
    | `https://www.xmatters.com` |
    | `https://<companyname>.xmatters.com.au/` |

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minták egyikének használatával:

    | Válasz URL-cím |
    | ---------- |
    |  `https://<companyname>.au1.xmatters.com.au` |
    | `https://<companyname>.xmatters.com/sp/<instancename>` |
    | `https://<companyname>.cs1.xmatters.com/sp/<instancename>` |
    | `https://<companyname>.au1.xmatters.com.au/<instancename>` |

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel. Az értékek lekéréséhez forduljon a XMatters OnDemand-ügyfélszolgálati [csapatához](https://www.xmatters.com/company/contact-us/) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

    > [!IMPORTANT]
    > Továbbítania kell a tanúsítványt a [XMatters OnDemand támogató csapatának](https://www.xmatters.com/company/contact-us/). Az egyszeri bejelentkezés konfigurációjának véglegesítése előtt a tanúsítványt fel kell tölteni a xMatters támogatási csapatával.

6. A **XMatters OnDemand beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a xMatters OnDemand.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **XMatters OnDemand** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a &quot;default Access&quot; szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.


## <a name=&quot;configure-xmatters-ondemand-sso&quot;></a>XMatters OnDemand SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a XMatters OnDemand vállalati webhelyre rendszergazdaként.

2. Kattintson a **rendszergazda** elemre, majd a **vállalati adatok** elemre.

    ![Adminisztrátor lap](./media/xmatters-ondemand-tutorial/admin.png &quot;Rendszergazda")

3. Az **SAML-konfiguráció** lapon hajtsa végre a következő lépéseket:

    ![SAML-konfiguráció szakasz ](./media/xmatters-ondemand-tutorial/saml-configuration.png "SAML-konfiguráció")

    a. Válassza az **SAML engedélyezése** lehetőséget.

    b. Az **identitás-szolgáltató azonosítója** szövegmezőbe illessze be az **Azure ad-azonosító** értékét, amelyet a Azure Portal másolt.

    c. Az **egyszeri bejelentkezés URL-címe** szövegmezőbe illessze be a **bejelentkezési URL-cím** értéket, amelyet a Azure Portal másolt.

    d. A **kijelentkezési URL átirányítása** szövegmezőbe illessze be a **KIjelentkezési URL-címet**, amelyet a Azure Portal másolt.

    e. Kattintson a **fájl kiválasztása** lehetőségre a Azure Portal letöltött **tanúsítvány (Base64)** feltöltéséhez. 

    f. A vállalati adatok lap tetején kattintson a **módosítások mentése** gombra.

    ![Cégadatok](./media/xmatters-ondemand-tutorial/save-button.png "Cégadatok")

### <a name="create-xmatters-ondemand-test-user"></a>XMatters OnDemand-tesztelési felhasználó létrehozása

1. Jelentkezzen be a **XMatters OnDemand** -bérlőbe.

2. Lépjen a felhasználók **ikonra**  >   , majd kattintson a **felhasználók hozzáadása** elemre.

    ![Felhasználók](./media/xmatters-ondemand-tutorial/add-user.png "Felhasználók")

3. A **felhasználók hozzáadása** szakaszban adja meg a kötelező mezőket, majd kattintson a **felhasználó hozzáadása** gombra.

    ![Felhasználó hozzáadása](./media/xmatters-ondemand-tutorial/add-user-2.png "Felhasználó hozzáadása")



### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját.

* Kattintson az alkalmazás tesztelése Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a xMatters-OnDemand, amelyhez be szeretné állítani az egyszeri bejelentkezést

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások xMatters OnDemand csempére kattint, automatikusan be kell jelentkeznie arra a xMatters-OnDemand, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

A xMatters-OnDemand konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).