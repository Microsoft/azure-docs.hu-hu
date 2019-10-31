---
title: 'Oktatóanyag: Azure Active Directory integráció a Picturepark-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Picturepark között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 08c5bd8da0dda74156b2d44c8106ed345ef749dd
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177020"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Oktatóanyag: Azure Active Directory integráció a Picturepark

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Picturepark a Azure Active Directory (Azure AD) szolgáltatással.
A Picturepark és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a Picturepark.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Picturepark (egyszeri bejelentkezés) az Azure AD-fiókokkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Picturepark való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* Picturepark egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Picturepark támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-picturepark-from-the-gallery"></a>Picturepark hozzáadása a gyűjteményből

A Picturepark Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Picturepark a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha Picturepark szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Picturepark**kifejezést, válassza a **Picturepark** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Picturepark az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az Picturepark-mel konfigurálja és teszteli a **Britta Simon**nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Picturepark kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés Picturepark való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Picturepark egyszeri bejelentkezés konfigurálása](#configure-picturepark-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre Picturepark-teszt felhasználót](#create-picturepark-test-user)** – hogy a Picturepark Britta, a felhasználó Azure ad-képviseletéhez kapcsolódó partnerrel rendelkezzen.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés Picturepark való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **Picturepark** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![Picturepark tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<companyname>.picturepark.com`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával:

    |  |
    |--|
    | `https://<companyname>.current-picturepark.com`|
    | `https://<companyname>.picturepark.com`|
    | `https://<companyname>.next-picturepark.com`|
    | |

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Az értékek lekéréséhez forduljon a Picturepark ügyfélszolgálati [csapatához](https://picturepark.com/company/picturepark-customer-support) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **SAML aláíró tanúsítvány** szakaszban kattintson a **Szerkesztés** gombra az **SAML aláíró tanúsítvány** párbeszédpanel megnyitásához.

    ![SAML aláíró tanúsítvány szerkesztése](common/edit-certificate.png)

6. Az **SAML aláíró tanúsítvány** szakaszban másolja az **ujjlenyomatot** , és mentse a számítógépre.

    ![Ujjlenyomat értékének másolása](common/copy-thumbprint.png)

7. A **Picturepark beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint. A **bejelentkezési URL-cím**esetében használja az értéket a következő mintával: `https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > a _my_directory_id_ az Azure ad-előfizetés bérlői azonosítója.

    ![Konfigurációs URL-címek másolása](./media/picturepark-tutorial/configurls.png)

    a. Azure AD-azonosító

    b. Kijelentkezési URL-cím

### <a name="configure-picturepark-single-sign-on"></a>Picturepark egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Picturepark vállalati webhelyre rendszergazdaként.

2. A felső eszköztáron kattintson a **felügyeleti eszközök**, majd a **felügyeleti konzol**elemre.
   
    ![Felügyeleti konzol](./media/picturepark-tutorial/ic795062.png "Felügyeleti konzol")

3. Kattintson a **hitelesítés**, majd az **identitás-szolgáltatók**elemre.
   
    ![Hitelesítés](./media/picturepark-tutorial/ic795063.png "Hitelesítés")

4. Az **Identity Provider konfigurálása** szakaszban hajtsa végre a következő lépéseket:
   
    ![Identitás-szolgáltató konfigurációja](./media/picturepark-tutorial/ic795064.png "Identitás-szolgáltató konfigurációja")
   
    a. Kattintson a **Hozzáadás** parancsra.
  
    b. Adja meg a konfiguráció nevét.
   
    c. Válassza **a beállítás alapértelmezettként**lehetőséget.
   
    d. A **kiállító URI** szövegmezőben illessze be azt a **bejelentkezési URL-címet** , amelyet a Azure Portalból másolt.
   
    e. A **megbízható kiállítói hüvelykujj nyomtatása** szövegmezőbe illessze be a **SAML-aláíró tanúsítvány** szakaszból másolt **ujjlenyomat** értékét. 

5. Kattintson a **JoinDefaultUsersGroup**elemre.

6. Ha a **jogcím** szövegmezőben az **EmailAddress** attribútumot szeretné beállítani, írja be a `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`, majd kattintson a **Mentés**gombra.

      ![Konfigurálás](./media/picturepark-tutorial/ic795065.png "Konfiguráció")

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a következőt: `brittasimon@yourcompanydomain.extension`. Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a  **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a Picturepark hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **Picturepark**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Picturepark**lehetőséget.

    ![Az Picturepark hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-picturepark-test-user"></a>Picturepark-tesztelési felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek a Picturepark, a Picturepark-ben kell kiépíteni őket. Picturepark esetén a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **Picturepark** -bérlőbe.

1. A felső eszköztáron kattintson a **felügyeleti eszközök**elemre, majd kattintson a **felhasználók**elemre.
   
    ![Felhasználók](./media/picturepark-tutorial/ic795067.png "Felhasználók")

1. A **felhasználók áttekintése** lapon kattintson az **új**elemre.
   
    ![Felhasználói felügyelet](./media/picturepark-tutorial/ic795068.png "Felhasználói felügyelet")

1. A **felhasználó létrehozása** párbeszédpanelen hajtsa végre a következő lépéseket a kiépíteni kívánt érvényes Azure Active Directory felhasználó számára:
   
    ![Felhasználó létrehozása](./media/picturepark-tutorial/ic795069.png "Felhasználó létrehozása")
   
    a. Az **E-mail cím** szövegmezőbe írja be a felhasználó `BrittaSimon@contoso.com`**e-mail-címét** .  
   
    b. **A jelszó és** **Jelszó megerősítése** szövegmezőbe írja be a BrittaSimon **jelszavát** . 
   
    c. A **Utónév** szövegmezőbe írja be a felhasználó **Britta** **utónevét** . 
   
    d. A **vezetéknév** szövegmezőbe írja be a Simon **nevű felhasználó vezetéknevét** .
   
    e. A **vállalati** szövegmezőbe írja be a felhasználó **vállalatának nevét** . 
   
    f. Az **ország** szövegmezőben válassza ki a felhasználó **országát/régióját** .
  
    g. A **zip** szövegmezőbe írja be a város **irányítószámát** .
   
    h. A **City (város** ) szövegmezőbe írja be a felhasználó **városának nevét** .

    i. Válasszon **nyelvet**.
   
    j. Kattintson a  **Create** (Létrehozás) gombra.

>[!NOTE]
>Az Azure AD felhasználói fiókjainak kiépítéséhez bármilyen más, a Picturepark által biztosított Picturepark felhasználói fiók létrehozására szolgáló eszközt vagy API-t használhat.
>

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Picturepark csempére kattint, automatikusan be kell jelentkeznie arra a Picturepark, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

