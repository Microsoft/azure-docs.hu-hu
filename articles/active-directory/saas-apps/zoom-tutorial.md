---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a nagyítással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és nagyítás között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: jeedes
ms.openlocfilehash: d257935aa3e9ad54b64b0f416119931661809172
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88545964"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zoom"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a nagyítással

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a nagyítást Azure Active Directory (Azure AD-val). Ha az Azure AD-vel integrálja a nagyítást, a következőket teheti:

* A nagyításhoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az Azure AD-fiókjával való nagyításhoz.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Az egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A nagyítás támogatja az **SP** által kezdeményezett SSO-t és 
* A nagyítás támogatja a [felhasználók **automatikus** kiépítési](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)felállítását.

## <a name="adding-zoom-from-the-gallery"></a>Nagyítás hozzáadása a katalógusból

A nagyítás Azure AD-ba való integrálásának konfigurálásához hozzá kell adnia egy nagyítást a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban a keresőmezőbe írja be a **Nagyítás** kifejezést.
1. Válassza az eredmények panel **Nagyítás** elemét, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zoom"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a nagyításhoz

Konfigurálja és tesztelje az Azure AD SSO-t a nagyítással egy **B. Simon**nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a nagyításban.

Az Azure AD SSO nagyítással való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
2. A **[Nagyítás egyszeri bejelentkezésének konfigurálása](#configure-zoom-sso)** – az egyes Sign-On beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Nagyítási teszt felhasználó létrehozása](#create-zoom-test-user)** – a felhasználó Azure ad-beli képviseletéhez csatolt B. Simon-hez tartozó a nagyításban.
3. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **nagyítási** alkalmazás integrációja lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<companyname>.zoom.us`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `<companyname>.zoom.us`

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<companyname>.zoom.us`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Az értékek lekéréséhez vegye fel a kapcsolatot a [nagyítási ügyfél támogatási csoportjával](https://support.zoom.us/hc/) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Nagyítás beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

> [!NOTE]
> Ha meg szeretné tudni, hogyan konfigurálhatja a szerepkört az Azure AD-ben, olvassa el a [vállalati alkalmazások SAML-jogkivonatában kiadott szerepkör-jogcímek konfigurálása](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management)című részt

> [!NOTE]
> A nagyítás várhatóan egy csoportos jogcímet várhat az SAML-adattartalomban. Ha bármilyen csoportot hozott létre, lépjen kapcsolatba a [nagyítási ügyfél támogatási](https://support.zoom.us/hc/) csoportjával, és adja meg a csoport adatait a csoport adatainak konfigurálásához. Emellett meg kell adnia az objektumazonosító számára az [ügyfél-támogatási csapat nagyítását](https://support.zoom.us/hc/) , hogy az objektum azonosítója a végén is konfigurálható legyen. Az objektumazonosító beszerzéséhez tekintse meg a [Nagyítás konfigurálása az Azure](https://support.zoom.us/hc/articles/115005887566)-ban című témakört.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
    1. A **Név** mezőbe írja a következőt: `B.Simon`.  
    1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
    1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
    1. Kattintson a **Létrehozás** elemre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát a nagyításhoz való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **Nagyítás**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-zoom-sso"></a>A nagyítás egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a nagyítású vállalati webhelyre rendszergazdaként.

2. Kattintson az **egyszeri bejelentkezés** fülre.

    ![Egyszeri bejelentkezés lap](./media/zoom-tutorial/zoom-sso1.png "Egyszeri bejelentkezés")

3. Kattintson a **Biztonság-vezérlés** fülre, majd lépjen az **egyszeri bejelentkezési** beállítások menüpontra.

4. Az egyszeres Sign-On szakaszban hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés szakasz](./media/zoom-tutorial/zoom-sso2.png "Egyszeri bejelentkezés")

    a. A **bejelentkezési oldal URL-címe** szövegmezőbe illessze be az Azure Portalból másolt **bejelentkezési URL-címet** .

    b. A kijelentkezési **oldal URL-címéhez** lépjen a Azure Portalra, és kattintson a bal oldali **Azure Active Directoryra** , majd lépjen a **Alkalmazásregisztrációkre**.

    ![A Azure Active Directory gomb](./media/zoom-tutorial/appreg.png)

    c. Kattintson a **végpontokra**

    ![A végpont gomb](./media/zoom-tutorial/endpoint.png)

    d. Másolja ki az **SAML-P** kijelentkezési végpontot, és illessze be a kijelentkezési **oldal URL-** szövegmezőbe.

    ![A másolási végpont gombja](./media/zoom-tutorial/endpoint1.png)

    e. Nyissa meg a Base-64 kódolású tanúsítványt a Jegyzettömbben, másolja a vágólapra a tartalmát, majd illessze be az identitás- **szolgáltatói tanúsítvány** szövegmezőbe.

    f. A **kiállító** szövegmezőbe illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét. 

    : Kattintson a **Save Changes** (Módosítások mentése) gombra.

    > [!NOTE]
    > További információért látogasson el a nagyítási dokumentációba [https://zoomus.zendesk.com/hc/articles/115005887566](https://zoomus.zendesk.com/hc/articles/115005887566)

### <a name="create-zoom-test-user"></a>Nagyítási teszt felhasználó létrehozása

A szakasz célja, hogy létrehozzon egy B. Simon nevű felhasználót a nagyításban. A nagyítás támogatja az automatikus felhasználó-kiépítés beállítást, amely alapértelmezés szerint engedélyezve van. További részletekért tekintse [meg az automatikus](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial) felhasználó-kiépítés konfigurálását ismertető témakört.

> [!NOTE]
> Ha manuálisan kell létrehoznia egy felhasználót, fel kell vennie a kapcsolatot a [nagyítási ügyfél támogatási csoportjával](https://support.zoom.us/hc/) .

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Amikor a hozzáférési panelen a nagyítás csempére kattint, automatikusan be kell jelentkeznie arra a nagyításra, amelyhez be szeretné állítani az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A nagyítás kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)
