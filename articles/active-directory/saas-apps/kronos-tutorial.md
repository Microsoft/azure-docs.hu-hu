---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a következővel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a a a a a a és a.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/13/2019
ms.author: jeedes
ms.openlocfilehash: 61241f361be3a9a60c8125f891525a078954afdf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91858081"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-kronos"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a következővel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a-t, a következőket teheti:

* Vezérlés az Azure AD-ben, hogy ki férhet hozzá a-hoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a-ba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Az egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A a **identitásszolgáltató** által KEZDEMÉNYEZett SSO-t támogatja

## <a name="adding-kronos-from-the-gallery"></a>A következő gyűjtemény hozzáadása a-katalógusból

Ahhoz, hogy az Azure AD-ba be lehessen állítani a WebApp integrációját, a katalógusból a felügyelt SaaS-alkalmazások listájára kell felvennie a-t.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a következőt: **"a"** keresőmezőbe.
1. Válassza **ki a WebApp** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-kronos"></a>Azure AD-beli egyszeri bejelentkezés konfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t a a " **B. Simon**" nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a-beli kapcsolódó felhasználó között.

Az Azure AD SSO és a következő építőelemek konfigurálásához és teszteléséhez hajtsa végre az alábbi építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A (z)-alapú **[egyszeri bejelentkezés konfigurálása](#configure-kronos-sso)** – az Sign-On beállításainak konfigurálása az alkalmazás oldalán.
    1. A felhasználó által **[létrehozott](#create-kronos-test-user)** , a felhasználó Azure ad-képviseletéhez kapcsolódó, B. Simon-beli, a következőhöz tartozó, "
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **az alkalmazás-** integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<company name>.kronos.net/`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<company name>.kronos.net/wfc/navigator/logonWithUID`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel. Ezekhez az értékekhez forduljon a következőhöz: a- [ügyfél támogatási csapata](https://www.kronos.in/contact/en-in/form) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A az SAML-alkalmazás meghatározott formátumban várja az SAML-kijelentéseket. Konfigurálja a következő jogcímeket ehhez az alkalmazáshoz. Az attribútumok értékeit az alkalmazás-integráció lapon, a **felhasználói attribútumok** szakaszban kezelheti. Az **egyszeres Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** gombra a **felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![A képernyőképen a szerkesztési ikonnal jelölt felhasználói attribútumok láthatók.](common/edit-attribute.png)

1. A **felhasználó attribútumai** párbeszédpanel **felhasználói jogcímek** szakaszában konfigurálja az SAML-jogkivonat attribútumot a fenti képen látható módon, és hajtsa végre a következő lépéseket:

    a. Kattintson a **Szerkesztés ikonra** a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![Képernyőfelvétel: felhasználói attribútumok & jogcímek kijelölése a szerkesztési ikonnal.](./media/kronos-tutorial/tutorial_usermail.png)

    ![Képernyőfelvétel: a felhasználói jogcímek kezelése párbeszédpanel, amelyen megadhatja a leírt értékeket.](./media/kronos-tutorial/tutorial_usermailedit.png)

    b. Az **átalakítás** listából válassza a **ExtractMailPrefix ()** elemet.

    c. Az **1. paraméter** listából válassza a **User. userPrincipalName**elemet.

    d. Kattintson a **Mentés** gombra.

1. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. **A kiinduló** és a szükséges URL-cím (eke) t a saját követelménye alapján másolja.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

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

Ebben a szakaszban a "B. Simon" lehetőséget fogja használni az Azure egyszeri bejelentkezés használatára azáltal, hogy hozzáférést biztosít a következőhöz:.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások **listában válassza a**"a" lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-kronos-sso"></a>A (

Ha az egyszeri bejelentkezést a következőre szeretné beállítani, **akkor a** következőt kell elküldenie: az **összevonási metaadatokat tartalmazó XML-fájl** és a megfelelő másolt url-címek a Azure Portalról a-től a [Kronos support team](https://www.kronos.in/contact/en-in/form) Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-kronos-test-user"></a>A-felhasználó létrehozása a felhasználók számára

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a-ban. A következővel veheti fel a felhasználók munkáját [a a "](https://www.kronos.in/contact/en-in/form) a"-platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférés panelen a "a" gombra kattint, automatikusan be kell jelentkeznie arra a következőre, amelyhez az SSO-t beállította. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A kampány kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)
