---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció SD-elemekkel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és SD elemek között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.openlocfilehash: a9bcda4affa19cf8793cd078fdc5b96d842eb42b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92893595"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sd-elements"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció SD-elemekkel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhat SD-elemeket Azure Active Directory (Azure AD) használatával. Ha SD-elemeket integrál az Azure AD-vel, a következőket teheti:

* Az SD-elemekhez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az SD-elemekbe az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* SD-elemek egyszeri bejelentkezéses (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az SD-elemek támogatják a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

## <a name="adding-sd-elements-from-the-gallery"></a>SD-elemek hozzáadása a katalógusból

Az SD-elemek Azure AD-integrációjának konfigurálásához SD-elemeket kell hozzáadnia a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be az **SD-elemek** kifejezést a keresőmezőbe.
1. Válassza ki az **SD-elemeket** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-sd-elements"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése SD-elemekhez

Konfigurálja és tesztelje az Azure AD SSO-t SD-elemekkel egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó számára az SD-elemekben.

Az Azure AD SSO SD-elemekkel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[SD-elemek egyszeri bejelentkezésének konfigurálása](#configure-sd-elements-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[SD-elemek létrehozása – teszt felhasználó](#create-sd-elements-test-user)** – a B. Simon egy, a felhasználó Azure ad-beli képviseletéhez kapcsolódó SD-elemekben található.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **SD-elemek** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<tenantname>.sdelements.com/sso/saml2/metadata`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<tenantname>.sdelements.com/sso/saml2/acs/`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel. Vegye fel a kapcsolatot az [SD-elemek ügyfél-támogatási csapatával](mailto:support@sdelements.com) az értékek lekéréséhez. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az SD-elemek alkalmazás meghatározott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül az SD-elemek alkalmazás néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.

    | Name |  Forrás attribútum|
    | --- | --- |
    | e-mail |User. mail |
    | FirstName |User. givenName |
    | LastName |felhasználó. vezetéknév |

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. Az **SD-elemek beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést az SD-elemek elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **SD-elemek elemet**.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-sd-elements-sso"></a>SD-elemek egyszeri bejelentkezésének konfigurálása

1. Ha engedélyezni szeretné az egyszeri bejelentkezést, lépjen kapcsolatba az [SD-elemek támogatási csapatával](mailto:support@sdelements.com) , és adja meg a letöltött tanúsítványfájl-fájlt.

1. Egy másik böngészőablakban jelentkezzen be az SD-elemek bérlője rendszergazdaként.

1. A felső menüben kattintson a **rendszer**, majd az **egyszeri bejelentkezés** lehetőségre.

    ![Képernyőkép, amely a legördülő menüből kiválasztott "rendszer" és "egyszeri bejelentkezés" beállítást jeleníti meg.](./media/sd-elements-tutorial/tutorial_sd-elements_09.png)

1. Az **egyszeres Sign-On beállítások** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Egyetlen Sign-On konfigurálása](./media/sd-elements-tutorial/tutorial_sd-elements_10.png)

    a. **SSO-típusként** válassza az **SAML** lehetőséget.

    b. Az **Identity Provider entitás-azonosító** szövegmezőbe illessze be az **Azure ad-azonosító** értékét, amelyet a Azure Portalból másolt.

    c. Az **Identitáskezelés egyetlen Sign-On szolgáltatás** szövegmezőbe illessze be a **bejelentkezési URL-cím** értékét, amelyet a Azure Portalból másolt.

    d. Kattintson a **Mentés** gombra.

### <a name="create-sd-elements-test-user"></a>SD-elemek létrehozása teszt felhasználó

Ennek a szakasznak a célja, hogy egy B. Simon nevű felhasználót SD-elemekben hozzon létre. SD-elemek esetén az SD-elemek létrehozása a felhasználók számára manuális feladat.

**Ha SD-elemekben szeretné létrehozni a B. Simon-t, hajtsa végre a következő lépéseket:**

1. A webböngésző ablakban jelentkezzen be az SD-elemek vállalati webhelyre rendszergazdaként.

1. A felső menüben kattintson a **felhasználói kezelés**, majd a **felhasználók** lehetőségre.

    ![Képernyőkép, amely a "felhasználók" elemet mutatja a "felhasználói kezelés" legördülő menüből.](./media/sd-elements-tutorial/tutorial_sd-elements_11.png) 

1. Kattintson az **új felhasználó hozzáadása** lehetőségre.

    ![Képernyőkép, amely megjeleníti az "új felhasználó hozzáadása" gombot.](./media/sd-elements-tutorial/tutorial_sd-elements_12.png)

1. Az **új felhasználó hozzáadása** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![SD-elemek tesztelési felhasználójának létrehozása](./media/sd-elements-tutorial/tutorial_sd-elements_13.png) 

    a. Az **e-mail** szövegmezőbe írja be a következőhöz hasonló felhasználó e-mail címét: **b.simon@contoso.com** .

    b. Az **Utónév** szövegmezőbe írja be a felhasználó utónevét (például **B).**

    c. A **vezetékneve** szövegmezőbe írja be a felhasználó vezetéknevét, például **Simon** nevet.

    d. **Szerepkörként** válassza a **felhasználó** lehetőséget.

    e. Kattintson a **felhasználó létrehozása** gombra.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a SD-elemek csempére kattint, akkor automatikusan be kell jelentkeznie azon SD-elemekre, amelyekhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [SD-elemek kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)