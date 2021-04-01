---
title: 'Oktatóanyag: Azure Active Directory integráció az előre CX Suite-val | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az előre meghatározott CX Suite között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: b4a8ea5c08f66bc0c64d4762e695dd4e2822af44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92452005"
---
# <a name="tutorial-azure-active-directory-integration-with-foresee-cx-suite"></a>Oktatóanyag: Azure Active Directory integráció az előre látható CX-csomaggal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az előrelátható CX Suite-t Azure Active Directory (Azure AD) használatával.
Az Azure AD-vel az alábbi előnyökkel jár:

* Megadhatja az Azure AD-ben, hogy ki férhet hozzá az előre látható CX Suite-hoz.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek, hogy az Azure AD-fiókjával rendelkező CX Suite (egyszeri bejelentkezés) legyenek.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció az előre megadott CX Suite-vel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* Előre meghatározott CX Suite egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* Az előre látható CX Suite támogatja az **SP** által KEZDEMÉNYEZett SSO

* Az előre látható CX Suite **csak időben támogatja a** felhasználók üzembe helyezését

## <a name="adding-foresee-cx-suite-from-the-gallery"></a>A várható CX Suite hozzáadása a katalógusból

Az előre látható CX Suite Azure AD-be való integrálásának konfigurálásához a katalógusból a felügyelt SaaS-alkalmazások listájához hozzá kell adnia az előre megadott CX-csomagot.

**A következő lépések végrehajtásával adhatja hozzá az előre megadott CX Suite-csomagot a katalógusból:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a következőt: **előre megadott CX Suite**, válassza az eredmények panelen az **előre látható CX Suite** elemet, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Az eredmények listájában látható CX Suite](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés konfigurálását és tesztelését a **Britta Simon** nevű tesztelési felhasználó alapján, az előre meghatározott CX Suite-val.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolatra van szükség az adott CX Suite-ban.

Az Azure AD egyszeri bejelentkezés konfigurálásához és teszteléséhez az alábbi építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. Az **[előre beállított CX Suite egyszeri bejelentkezés beállítása](#configure-foresee-cx-suite-single-sign-on)** – az alkalmazás oldalának egyetlen Sign-On beállításának konfigurálása.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre egy előre megadott CX Suite-tesztelési felhasználót](#create-foresee-cx-suite-test-user)** – hogy a Britta Simon-nak egy, a felhasználó Azure ad-képviseletéhez kapcsolódó, előre látható CX Suite-beli partnere legyen.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés a következő lépésekkel konfigurálható az előre meghatározott CX Suite-val:

1. A [Azure Portal](https://portal.azure.com/)az **előre látható CX Suite** -alkalmazás-integráció lapon válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban, ha **szolgáltatói metaadatokat tartalmazó fájllal** rendelkezik, hajtsa végre a következő lépéseket:

    a. Kattintson a **metaadat-fájl feltöltése** elemre.

    ![Metaadat-fájl feltöltése](common/upload-metadata.png)

    b. Kattintson a **mappa emblémára** a metaadat-fájl kiválasztásához, majd kattintson a **feltöltés** elemre.

    ![metaadat-fájl kiválasztása](common/browse-upload-metadata.png)

    c. A metaadat-fájl feltöltése után az **azonosító** érték automatikusan feltöltve lesz az alapszintű SAML-konfiguráció szakaszban.

    ![A CX Suite-tartomány és az URL-címek egyszeri bejelentkezési adatainak előrejelzése](common/sp-identifier.png)

    a. A **bejelentkezési URL** szövegmezőbe írja be a következő URL-címet: `https://cxsuite.foresee.com/`

    b. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: https: \/ /www.okta.com/saml2/Service-Provider/\<UniqueID>

    > [!Note]
    > Ha az **azonosító** értéke nem kap automatikus polulated, akkor a fenti minta alapján manuálisan adja meg az értéket. Az azonosító értéke nem valódi. Frissítse ezt az értéket a tényleges azonosítóval. Az érték beszerzéséhez vegye fel a kapcsolatot a következővel: [CX Suite ügyfél-támogatási csapat](mailto:support@foresee.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **set up CX Suite** című szakaszban a követelmények szerint másolja a megfelelő URL-címeket.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-foresee-cx-suite-single-sign-on"></a>A várható CX Suite egyszeri Sign-On konfigurálása

Ha be szeretné állítani az egyszeri bejelentkezést az **előre látható CX Suite** -oldalon, el kell küldenie a letöltött **összevonási METAADATOKat tartalmazó XML** -fájlt és a megfelelő másolt url-címeket a Azure Portalről, hogy az a [CX Suite támogatási csapat](mailto:support@foresee.com) Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A Felhasználónév mezőbe írja be a **nevet** brittasimon@yourcompanydomain.extension . Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a várható CX Suite-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza az **előre megadott CX Suite** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza az **előre látható CX Suite** lehetőséget.

    ![Az alkalmazások listájában látható CX Suite-hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-foresee-cx-suite-test-user"></a>Hozzon létre egy előre CX Suite-teszt felhasználót

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre az előre látható CX Suite-ban. Az adott CX [Suite támogatási csapatának](mailto:support@foresee.com) használata a bevezetéshez szükséges felhasználók vagy tartományok hozzáadásához, amelyeket hozzá kell adni egy engedélyezési listához az előre megadott CX Suite platformhoz. Ha a tartomány hozzá van adva a csapathoz, a felhasználók automatikusan kiépítik a várható CX Suite platformot. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen az előre megadott CX Suite csempére kattint, automatikusan be kell jelentkeznie az előre látható CX-csomagba, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)