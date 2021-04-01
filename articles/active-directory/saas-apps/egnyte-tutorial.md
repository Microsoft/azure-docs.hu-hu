---
title: 'Oktatóanyag: Azure Active Directory integráció a Egnyte-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Egnyte között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2020
ms.author: jeedes
ms.openlocfilehash: 3d102b492326971ef186ba50a557ad8d1df5b6ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92454196"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-egnyte"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Egnyte

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Egnyte a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Egnyte-t az Azure AD-vel, a következőket teheti:

* A Egnyte-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Egnyte az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Egnyte egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Egnyte támogatja az **SP** által KEZDEMÉNYEZett SSO-t
* A Egnyte konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="adding-egnyte-from-the-gallery"></a>Egnyte hozzáadása a gyűjteményből

A Egnyte Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Egnyte a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Egnyte** kifejezést a keresőmezőbe.
1. Válassza ki a **Egnyte** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso"></a>Az Azure AD SSO konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az Egnyte-mel konfigurálja és teszteli a **Britta Simon** nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Egnyte kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés Egnyte való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

Az Azure AD SSO és a Egnyte konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[EGNYTE SSO konfigurálása](#configure-egnyte-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre Egnyte-teszt felhasználót](#create-egnyte-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-Egnyte rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **Egnyte** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![Egnyte tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-signonurl.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<companyname>.egnyte.com`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<companyname>.egnyte.com/samlconsumer/AzureAD`
    
    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse az értéket a tényleges Sign-On URL-címmel és a válasz URL-címével. Az érték beszerzéséhez forduljon a Egnyte ügyfélszolgálati [csapatához](https://www.egnyte.com/corp/contact_egnyte.html) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

4. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

5. A **Egnyte beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure ad-azonosító

    c. Kijelentkezési URL-cím

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Egnyte.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Egnyte** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-egnyte-sso"></a>Egnyte SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Egnyte vállalati webhelyre rendszergazdaként.

2. Kattintson a **Beállítások** elemre.
   
    ![1. beállítások](./media/egnyte-tutorial/ic787819.png "Beállítások")

3. A menüben kattintson a **Beállítások** elemre.

    ![Beállítások](./media/egnyte-tutorial/ic787820.png "Beállítások")

4. Kattintson a **konfiguráció** lapra, majd a **Biztonság** elemre.

    ![Biztonság](./media/egnyte-tutorial/ic787821.png "Biztonság")

5. Az **egyszeres Sign-On hitelesítés** szakaszban hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezéses hitelesítés](./media/egnyte-tutorial/ic787822.png "Egyszeri bejelentkezéses hitelesítés")   
    
    a. **Egyszeri bejelentkezéses hitelesítésként** válassza az **SAML 2,0** lehetőséget.
   
    b. **Identitás-szolgáltatóként** válassza a **AzureAD** lehetőséget.
   
    c. Illessze be Azure Portalba másolt **bejelentkezési URL-címet** az **Identity Provider bejelentkezési URL-címe** szövegmezőbe.
   
    d. Illessze be a Azure Portalból másolt **Azure ad-azonosítót** az **Identity Provider entitás-azonosító** szövegmezőbe.
      
    e. Nyissa meg az alap-64 kódolású tanúsítványt a Jegyzettömbben, Azure Portal letöltve, másolja ki a tartalmat a vágólapra, majd illessze be az **Identity Provider tanúsítvány** szövegmezőbe.
   
    f. **Alapértelmezett felhasználó-hozzárendelésként** válassza az **e-mail-cím** elemet.
   
    : A **tartományhoz tartozó kibocsátó értékének használatakor** válassza a **Letiltva** lehetőséget.
   
    h. Kattintson a **Mentés** gombra.

### <a name="create-egnyte-test-user"></a>Egnyte-tesztelési felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a Egnyte, a Egnyte kell kiépíteni őket. Egnyte esetén a kiépítés manuális feladat.

**Felhasználói fiókok kiépítéséhez hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **Egnyte** vállalati webhelyre rendszergazdaként.

2. Lépjen a **Beállítások \> felhasználók & csoportok menüpontra**.

3. Kattintson az **új felhasználó hozzáadása** elemre, majd válassza ki a hozzáadni kívánt felhasználó típusát.
   
    ![Felhasználók](./media/egnyte-tutorial/ic787824.png "Felhasználók")

4. Az **új Kiemelt felhasználó** szakaszban hajtsa végre a következő lépéseket:
    
    ![Új általános jogú felhasználó](./media/egnyte-tutorial/ic787825.png "Új általános jogú felhasználó")   

    a. Az **e-mail** szövegmezőbe írja be a felhasználó, például a **Brittasimon \@ contoso.com**-e-mail-címét.

    b. A **Felhasználónév** szövegmezőbe írja be a **Brittasimon**, például a következőt: Felhasználónév.

    c. Válassza az **egyszeri bejelentkezés** **hitelesítési típusként** lehetőséget.
   
    d. Kattintson a **Mentés** gombra.
    
    >[!NOTE]
    >A Azure Active Directory fiók tulajdonosának értesítési e-mailt fog kapni.
    >

>[!NOTE]
>Az Azure AD felhasználói fiókjainak kiépítéséhez bármilyen más, a Egnyte által biztosított Egnyte felhasználói fiók létrehozására szolgáló eszközt vagy API-t használhat.
>

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Egnyte csempére kattint, automatikusan be kell jelentkeznie arra a Egnyte, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)