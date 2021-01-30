---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Samsung Knox és a Business Services szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Samsung Knox és üzleti szolgáltatások között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: e1cf12d676de84bc18a123fbdf05b1170725eda8
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99072878"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-samsung-knox-and-business-services"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Samsung Knox és az üzleti szolgáltatásokkal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Samsung Knoxt és az üzleti szolgáltatásokat Azure Active Directory (Azure AD) használatával. Ha a Samsung Knox-t és az üzleti szolgáltatásokat az Azure AD-vel integrálja, a következőket teheti:

* A Samsung Knox és az üzleti szolgáltatások elérésére jogosult Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Samsung Knox-be és az üzleti szolgáltatásokba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Samsung Knox és Business Services egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Samsung Knox és az üzleti szolgáltatások támogatják az **SP** által kezdeményezett SSO-t

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="adding-samsung-knox-and-business-services-from-the-gallery"></a>Samsung Knox-és üzleti szolgáltatások hozzáadása a katalógusból

A Samsung Knox és az üzleti szolgáltatások Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Samsung Knox és az üzleti szolgáltatásokat a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Samsung Knox és az üzleti szolgáltatások** kifejezést a keresőmezőbe.
1. Válassza a **Samsung Knox és az üzleti szolgáltatások** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-samsung-knox-and-business-services"></a>Azure AD SSO konfigurálása és tesztelése Samsung Knox és Business Services esetén

Konfigurálja és tesztelje az Azure AD SSO-t a Samsung Knox és az üzleti szolgáltatások segítségével egy **B. Simon** nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Samsung Knox-ben és az üzleti szolgáltatásokban.

Az Azure AD SSO és a Samsung Knox és az üzleti szolgáltatások konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[Samsung Knox és az üzleti szolgáltatások egyszeri bejelentkezésének konfigurálása](#configure-samsung-knox-and-business-services-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozza létre a Samsung Knox és az üzleti szolgáltatások tesztelésére szolgáló felhasználót](#create-samsung-knox-and-business-services-test-user)** – hogy a B. Simon partnere legyen a Samsung Knox-ban és az üzleti szolgáltatásokban, amelyek a felhasználó Azure ad-beli képviseletéhez kapcsolódnak.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **Samsung Knox és az üzleti szolgáltatások** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet:  `https://www.samsungknox.com`

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést a Samsung Knox és az üzleti szolgáltatások elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Samsung Knox és az üzleti szolgáltatások** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-samsung-knox-and-business-services-sso"></a>A Samsung Knox és az üzleti szolgáltatások egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Samsung Knox és a Business Services vállalati webhelyre rendszergazdaként.

1. Kattintson a jobb felső sarokban található **avatarra** .

    ![Samsung Knox avatar](./media/samsung-knox-and-business-services-tutorial/avatar.png)

1. A bal oldali oldalsávon kattintson az **Active Directory-beállítások** elemre, és hajtsa végre a következő lépéseket.

    ![ACTIVE DIRECTORY-BEÁLLÍTÁSOK](./media/samsung-knox-and-business-services-tutorial/sso-settings.png)

    a. Az **azonosító (entitás azonosítója)** szövegmezőbe illessze be a Azure Portalba beírt **azonosító** értékét.

    b. Az **alkalmazás-összevonási metaadatok URL-címe** szövegmezőbe illessze be az **alkalmazás-összevonási metaadatok URL-címét** , amelyet a Azure Portal másolt.

    c. kattintson a **Kapcsolódás ad SSO-hoz** elemre.

### <a name="create-samsung-knox-and-business-services-test-user"></a>Samsung Knox és Business Services tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a Samsung Knoxban és az üzleti szolgáltatásokban. A Samsung Knox és az [üzleti szolgáltatások támogatási csapatával](mailto:noreplyk.sec@samsung.com) a felhasználók hozzáadhatók a Samsung Knox és a Business Services platformhoz. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a Samsung Knox és az üzleti szolgáltatások bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül a Samsung Knox és az üzleti szolgáltatások bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Ha a saját alkalmazások Samsung Knox és Business Services csempére kattint, a rendszer átirányítja a Samsung Knox és az üzleti szolgáltatások bejelentkezési URL-címére. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)használatába.


## <a name="next-steps"></a>Következő lépések

A Samsung Knox és az üzleti szolgáltatások konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


