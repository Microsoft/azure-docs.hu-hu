---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Supermood |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Supermood között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: afc04efa-2eba-4e47-8ce4-b71eb293cd09
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 177b393b475250e5e019a5829ea17279e1b0f44c
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64922104"
---
# <a name="tutorial-azure-active-directory-integration-with-supermood"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Supermood

Ebben az oktatóanyagban elsajátíthatja, hogyan Supermood integrálása az Azure Active Directory (Azure AD).
Supermood integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá Supermood Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve Supermood (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Supermood az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókkal](https://azure.microsoft.com/free/)
* Supermood egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a supermood **SP és IDP** által kezdeményezett egyszeri bejelentkezés
* Támogatja a supermood **igény szerinti** felhasználók átadása

## <a name="adding-supermood-from-the-gallery"></a>Supermood hozzáadása a katalógusból

Az Azure ad-ben Supermood integráció konfigurálásához, hozzá kell Supermood a galériából a felügyelt SaaS-alkalmazások listájára.

**Supermood hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Supermood**válassza **Supermood** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában supermood](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az Supermood nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó Supermood hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Supermood tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Supermood egyszeri bejelentkezés konfigurálása](#configure-supermood-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Supermood tesztfelhasználót](#create-supermood-test-user)**  – egy megfelelője a Britta Simon Supermood, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Supermood, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **Supermood** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, hajtsa végre az alábbi lépéseket:

    ![Supermood tartomány és URL-címeket egyetlen bejelentkezési adatait](common/idp-preintegrated-relay.png)

    a. Ellenőrizze **további URL-címet beállítani**.

    b. Ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, a **továbbítási állapot** szövegmezőbe írja be egy URL-címe: `https://supermood.co/auth/sso/saml20`

5. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Supermood tartomány és URL-címeket egyetlen bejelentkezési adatait](common/metadata-upload-additional-signon.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be az URL-cím:  `https://supermood.co/app/#!/loginv2`

6. Supermood alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **felhasználói attribútumok** párbeszédpanel.

    ![image](common/edit-attribute.png)

7. Az a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen, a jogcímek szerkesztése használatával **Szerkesztés ikon** , vagy adja hozzá a jogcímek használatával **hozzáadása új jogcímet**SAML-jogkivonat attribútum beállítása, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:

    | Name (Név) | Adatforrás-attribútum|
    | ---------------| ------|
    | Keresztnév | user.givenname |
    | Vezetéknév | user.surname |

    a. Kattintson a **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Hagyja a **Namespace** üres.

    d. Válassza ki a forrás, **attribútum**.

    e. Az a **forrásattribútum** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

8. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson a Másolás gombra, hogy **alkalmazás összevonási metaadatainak URL-címe** és mentse a számítógép.

    ![A tanúsítvány letöltési hivatkozás](common/copy-metadataurl.png)

### <a name="configure-supermood-single-sign-on"></a>Supermood egyszeri bejelentkezés konfigurálása

1. Nyissa meg a Supermood.co felügyeleti panel biztonsági rendszergazdaként.

1. Kattintson a **fiókom** (bal alsó sarokban található) és **egyszeri bejelentkezés (SSO)**.

    ![A tanúsítvány egyetlen](./media/supermood-tutorial/tutorial_supermood_single.png)

1. A **a SAML 2.0 konfigurációk**, kattintson a **adjon hozzá egy e-mail-tartomány egy SAML 2.0 konfigurációs**.

    ![A tanúsítvány hozzáadása](./media/supermood-tutorial/tutorial_supermood_add.png)

1. A **adjon hozzá egy e-mail-tartomány egy SAML 2.0 konfigurációs**. szakaszban, hajtsa végre az alábbi lépéseket:

    ![A tanúsítvány az saml](./media/supermood-tutorial/tutorial_supermood_saml.png)

    a. Az a **az identitásszolgáltató az e-mail-tartományhoz** szövegmezőbe írja be a tartomány.

    b. Az a **-metaadatok URL-cím használata** szövegmezőjébe illessze be a **alkalmazás összevonási metaadatainak URL-címe** Azure Portalról másolt.

    c. Kattintson a **Hozzáadás** parancsra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be `brittasimon@yourcompanydomain.extension`. Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Supermood Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Supermood**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Supermood**.

    ![Az alkalmazások listáját a Supermood hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-supermood-test-user"></a>Supermood tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó Supermood jön létre. Supermood támogatja a just-in-time-felhasználók létrehozásának, amely alapértelmezés szerint engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Ha a felhasználó már nem létezik az Supermood, egy új jön létre a hitelesítés után. Ha manuálisan hozzon létre egy felhasználót van szüksége, forduljon a [Supermood támogatási csapatának](mailto:hello@supermood.fr).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Supermood csempére kattint, meg kell lehet automatikusan bejelentkezett a Supermood, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)