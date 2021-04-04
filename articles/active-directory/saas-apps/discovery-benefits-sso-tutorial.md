---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a felderítési előnyökkel (SSO) | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a felderítési előnyök egyszeri bejelentkezése között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: jeedes
ms.openlocfilehash: 0e5d67e00ee56b5c4006a8422c713e3cabb32bfc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92454762"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-discovery-benefits-sso"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a felderítési előnyökkel (SSO)

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a felderítési előnyöket az SSO-val Azure Active Directory (Azure AD-val). Ha a felderítési előnyöket az Azure AD-vel integrálja, a következőket teheti:

* Az Azure AD azon vezérlése, amely hozzáfér a felderítési előnyökhöz SSO-hoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a felderítési Előnyökbe az egyszeri bejelentkezéshez az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A felderítési funkció egyszeri bejelentkezéses egyszeri bejelentkezést (SSO) engedélyező előfizetést biztosít.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A felderítési előnyök SSO támogatja a **identitásszolgáltató** KEZDEMÉNYEZett SSO-t

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="adding-discovery-benefits-sso-from-the-gallery"></a>A felderítési előnyök beolvasása a katalógusból

A felderítési előnyök Azure AD-ba való BEJELENTKEZÉSének konfigurálásához a katalógusból a felügyelt SaaS-alkalmazások listájához fel kell vennie a felderítési előnyöket.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **felderítési előnyök SSO** kifejezést a keresőmezőbe.
1. Válassza a **felderítési előnyök egyszeri bejelentkezés** az eredmények panelen lehetőséget, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-discovery-benefits-sso"></a>Azure AD-alapú egyszeri bejelentkezés konfigurálása és tesztelése a felderítési előnyök SSO-hoz

Konfigurálja és tesztelje az Azure AD SSO-t a felderítési előnyökkel rendelkező egyszeri bejelentkezéssel egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a felderítési előnyökben (SSO).

Ha az Azure AD SSO-t a felderítési előnyökkel rendelkező SSO-val szeretné konfigurálni és tesztelni, hajtsa végre a következő építőelemeket

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[felderítési előnyök konfigurálása SSO SSO](#configure-discovery-benefits-sso-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Felderítési előnyök létrehozása SSO-teszt felhasználó](#create-discovery-benefits-sso-test-user)** – ha B. Simon-nek kell lennie a felderítési előnyökben, amely a felhasználó Azure ad-képviseletéhez kapcsolódik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **felderítési előnyök SSO** -alkalmazás integrációja lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfigurációs** szakaszban az alkalmazás előre konfigurálva van a **identitásszolgáltató** által kezdeményezett módban, és a szükséges URL-címek már előre fel vannak töltve az Azure-ban. A felhasználónak mentenie kell a konfigurációt a **Save (Mentés** ) gombra kattintva.

1. A felderítési előnyök SSO-alkalmazása egy adott formátumban elvárja az SAML-állításokat, így egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a felhasználói attribútumok párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

    a. Kattintson a **Szerkesztés**  ikonra az **egyedi felhasználói azonosító (név azonosítója)** párbeszédpanel megnyitásához.

    ![Képernyőfelvétel: a "szükséges jogcím" (a "felhasználói attribútumok & jogcímek") szakasz a jobb oldalon kiválasztva.](./media/discovery-benefits-sso-tutorial/attribute01.png)

    ![Észlelési előnyök – SSO-konfiguráció](./media/discovery-benefits-sso-tutorial/attribute02.png)

    b. Kattintson a **Szerkesztés** ikonra az **átalakítás kezelése** párbeszédpanel megnyitásához.

    c. Az **átalakítás** szövegmezőbe írja be a sorban látható **ToUppercase ()** .

    d. Az **1. paraméter** szövegmezőbe írja be a (z) paramétert `<Name Identifier value>` .

    e. Kattintson a **Hozzáadás** parancsra.

    > [!NOTE]
    > A felderítési előnyök egyszeri bejelentkezéséhez egy rögzített karakterlánc-értéket kell átadni az **egyedi felhasználói azonosító (Name ID)** mezőben az integráció működésének megkezdéséhez. Az Azure AD jelenleg nem támogatja ezt a funkciót, így például a **ToUpper** vagy a NameID **ToLower** -transzformációk segítségével rögzített karakterlánc-értékeket állíthat be a képernyőképen látható módon.

    f. Automatikusan kitöltöttük az SSO-konfigurációhoz szükséges további jogcímeket ( `SSOInstance` és `SSOID` ). A **Szerkesztés** ikon használatával képezhető le az értékek a szervezeten belül.

    ![Képernyőfelvétel: a "felhasználói attribútumok & jogcímek" a "S S O-példány" és "S S O I D" értékek kiemelve.](./media/discovery-benefits-sso-tutorial/attribute03.png)

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **felderítési előnyök beállítása SSO** szakaszban a követelmények alapján másolja ki a megfelelő URL-címeket.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a felderítési előnyökhöz az egyszeri bejelentkezéshez.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **felderítési előnyök egyszeri bejelentkezés** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-discovery-benefits-sso-sso"></a>A felderítési előnyök konfigurálása SSO SSO

Ha be szeretné állítani az egyszeri bejelentkezést a **felderítési előnyök egyszeri bejelentkezéses** oldalán, el kell küldenie a letöltött **tanúsítványt (Base64)** és a megfelelő másolt url-címeket a Azure Portalról a [felderítési előnyökre az egyszeri bejelentkezést támogató csoport](mailto:Jsimpson@DiscoveryBenefits.com) Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-discovery-benefits-sso-test-user"></a>Felderítési előnyök létrehozása SSO-teszt felhasználó

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a felderítési előnyök SSO-ban. A felderítési előnyökkel rendelkező [egyszeri bejelentkezéses támogatási csapattal](mailto:Jsimpson@DiscoveryBenefits.com) felveheti a felhasználókat a felderítési előnyök SSO-platformba. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a felderítési előnyök SSO csempére kattint, automatikusan be kell jelentkeznie arra a felderítési Előnyökbe, amelyhez az SSO-t beállította. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [A felderítési előnyök kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)