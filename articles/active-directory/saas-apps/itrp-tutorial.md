---
title: 'Oktatóanyag: Azure Active Directory integráció a ITRP-szel | Microsoft Docs'
description: Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és ITRP között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 2dff68f98e2922d5fc7a4fca1e6de8740bc2ae68
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92459679"
---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>Oktatóanyag: Azure Active Directory integráció a ITRP

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a ITRP a Azure Active Directory (Azure AD) szolgáltatással.
Ez az integráció az alábbi előnyöket biztosítja:

* Az Azure AD segítségével szabályozhatja, hogy ki férhet hozzá a ITRP.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a ITRP (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg az [egyszeri bejelentkezést a Azure Active Directory alkalmazásaihoz](../manage-apps/what-is-single-sign-on.md)című témakört.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció ITRP való konfigurálásához a következők szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Az egyszeri bejelentkezést engedélyező ITRP-előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A ITRP támogatja az SP által kezdeményezett egyszeri bejelentkezést.

## <a name="add-itrp-from-the-gallery"></a>ITRP hozzáadása a gyűjteményből

A ITRP Azure AD-be való integrálásának beállításához hozzá kell adnia a ITRP a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. A [Azure Portal](https://portal.azure.com)a bal oldali ablaktáblán válassza a **Azure Active Directory**:

    ![Válassza az Azure Active Directory elemet.](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**  >  **minden alkalmazás**:

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

3. Alkalmazás hozzáadásához válassza az ablak tetején található **új alkalmazás** elemet:

    ![Új alkalmazás kiválasztása](common/add-new-app.png)

4. A keresőmezőbe írja be a **ITRP** kifejezést. A keresési eredmények között válassza a **ITRP** lehetőséget, majd válassza a **Hozzáadás** lehetőséget.

     ![Keresési eredmények](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli a ITRP használatával egy Britta Simon nevű teszt felhasználó használatával.
Az egyszeri bejelentkezés engedélyezéséhez létre kell hoznia egy kapcsolatot az Azure AD-felhasználó és a megfelelő felhasználó között a ITRP-ben.

Az Azure AD egyszeri bejelentkezés ITRP való konfigurálásához és teszteléséhez a következő lépéseket kell elvégeznie:

1. **[Konfigurálja az Azure ad egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** , hogy engedélyezze a szolgáltatást a felhasználók számára.
2. **[Konfigurálja az ITRP egyszeri bejelentkezést](#configure-itrp-single-sign-on)** az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez.
4. **[Az Azure ad-teszt felhasználójának hozzárendelésével](#assign-the-azure-ad-test-user)** engedélyezheti az Azure ad egyszeri bejelentkezést a felhasználó számára.
5. **[Hozzon létre egy ITRP-teszt felhasználót](#create-an-itrp-test-user)** , amely a felhasználó Azure ad-képviseletéhez van társítva.
6. Az **[egyszeri bejelentkezés tesztelésével](#test-single-sign-on)** ellenőrizheti, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezést fogja engedélyezni a Azure Portal.

Az Azure AD egyszeri bejelentkezés ITRP való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)ITRP alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés** lehetőséget:

    ![Egyszeri bejelentkezés kiválasztása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez:

    ![Egyszeri bejelentkezési módszer kiválasztása](common/select-saml-option.png)

3. Az **egyszeres Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához:

    ![Képernyőfelvétel: az egyszeri Sign-On beállítása az SAML-oldallal lapon a Szerkesztés ikon kiválasztva.](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** párbeszédpanelen hajtsa végre a következő lépéseket.

    ![Alapszintű SAML-konfiguráció párbeszédpanel](common/sp-identifier.png)

    1. A **bejelentkezési URL** -cím mezőben adjon meg egy URL-címet ebben a mintában:
    
       `https://<tenant-name>.itrp.com`

    1. Az **azonosító (entitás azonosítója)** mezőben adjon meg egy URL-címet ebben a mintában:

       `https://<tenant-name>.itrp.com`

    > [!NOTE]
    > Ezek az értékek helyőrzők. A tényleges bejelentkezési URL-címet és azonosítót kell használnia. Az értékek lekéréséhez lépjen kapcsolatba a [ITRP támogatási csoportjával](https://www.4me.com/support/) . A Azure Portal az **alapszintű SAML-konfiguráció** párbeszédpanelen látható mintázatokat is megtekintheti.

5. Az **SAML aláíró tanúsítvány** szakaszban válassza a **Szerkesztés** ikont az **SAML aláíró tanúsítvány** párbeszédpanel megnyitásához:

    ![Képernyőfelvétel: az SAML aláíró tanúsítvány lapja, amelyen a Szerkesztés ikon van kiválasztva.](common/edit-certificate.png)

6. Az **SAML aláíró tanúsítvány** párbeszédpanelen másolja az **ujjlenyomat** értékét, és mentse azt:

    ![Az ujjlenyomat értékének másolása](common/copy-thumbprint.png)

7. A **ITRP beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján:

    ![A konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    1. **Bejelentkezési URL-cím**.

    1. **Azure ad-azonosító**.

    1. **Kijelentkezési URL-cím**.

### <a name="configure-itrp-single-sign-on"></a>ITRP egyszeri bejelentkezés konfigurálása

1. Egy új böngészőablakban jelentkezzen be a ITRP vállalati webhelyre rendszergazdaként.

1. Az ablak tetején válassza a **Beállítások** ikont:

    ![Beállítások ikon](./media/itrp-tutorial/ic775570.png "Beállítások ikon")

1. A bal oldali panelen válassza az **egyszeri bejelentkezés** lehetőséget:

    ![Egyszeri bejelentkezés kiválasztása](./media/itrp-tutorial/ic775571.png "Egyetlen Sign-On kiválasztása")

1. Az **egyszeri bejelentkezési** konfiguráció szakaszban hajtsa végre az alábbi lépéseket.

    ![Képernyőfelvétel: az egyszeres Sign-On szakasz, amelyen engedélyezve van a kijelölés.](./media/itrp-tutorial/ic775572.png "Egy Sign-On szakasz")

    ![Képernyőfelvétel: az egyetlen Sign-On szakasz, ahol felveheti az ebben a lépésben ismertetett információkat.](./media/itrp-tutorial/ic775573.png "Egy Sign-On szakasz")

    1. Válassza az **Engedélyezve** lehetőséget.

    1. A **távoli kijelentkezési URL-cím** mezőben illessze be a Azure Portalból másolt **kijelentkezési URL-** értéket.

    1. Az **SAML SSO URL-cím** mezőben illessze be a Azure Portalból másolt **bejelentkezési URL** -értéket.

    1. A **Tanúsítvány ujjlenyomata** mezőben illessze be a tanúsítvány **ujjlenyomatának** értékét, amelyet a Azure Portal másolt.

    1. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy Britta Simon nevű teszt felhasználót hoz létre a Azure Portal.

1. A Azure Portal a bal oldali ablaktáblán válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók** lehetőséget, majd válassza a **minden felhasználó** lehetőséget:

    ![Válassza a Minden felhasználó lehetőséget](common/users.png)

2. Válassza ki a képernyő felső részén található **új felhasználó** elemet:

    ![Új felhasználó kiválasztása](common/new-user.png)

3. A **felhasználó** párbeszédpanelen hajtsa végre a következő lépéseket.

    ![Felhasználó párbeszédpanel](common/user-properties.png)

    1. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    1. A **Felhasználónév** mezőbe írja be a **BrittaSimon@ \<yourcompanydomain> . \<extension>**. (Például: BrittaSimon@contoso.com .)

    1. Válassza a **jelszó megjelenítése** lehetőséget, majd írja le a **jelszó** mezőben található értéket.

    1. Válassza a **Létrehozás** lehetőséget.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a ITRP.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **ITRP** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **ITRP** lehetőséget.

    ![Alkalmazások listája](common/all-applications.png)

3. A bal oldali ablaktáblán válassza a **felhasználók és csoportok** lehetőséget:

    ![Felhasználók és csoportok kiválasztása](common/users-groups-blade.png)

4. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![Felhasználó hozzáadása kiválasztása](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listában, majd kattintson az ablak alján található **kiválasztás** gombra.

6. Ha az SAML-állításban a szerepkör értéke várható, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából. Kattintson az ablak alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés** lehetőséget.

### <a name="create-an-itrp-test-user"></a>ITRP-teszt felhasználó létrehozása

Ha engedélyezni szeretné, hogy az Azure AD-felhasználók bejelentkezzenek a ITRP, hozzá kell adnia őket a ITRP-hez. Ezeket manuálisan kell felvennie.

Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:

1. Jelentkezzen be a ITRP-bérlőbe.

1. Az ablak tetején válassza a **rekordok** ikont:

    ![Rekordok ikon](./media/itrp-tutorial/ic775575.png "Rekordok ikon")

1. A menüben válassza a **személyek** lehetőséget:

    ![Személyek kiválasztása](./media/itrp-tutorial/ic775587.png "Személyek kiválasztása")

1. Új személy hozzáadásához válassza a pluszjelet ( **+** ):

    ![Kattintson a pluszjelre](./media/itrp-tutorial/ic775576.png "Kattintson a pluszjelre")

1. Az **új személy hozzáadása** párbeszédpanelen hajtsa végre a következő lépéseket.

    ![Új személy hozzáadása párbeszédpanel](./media/itrp-tutorial/ic775577.png "Új személy hozzáadása párbeszédpanel")

    1. Adja meg a hozzáadni kívánt érvényes Azure AD-fiók nevét és e-mail-címét.

    1. Kattintson a **Mentés** gombra.

> [!NOTE]
> Az Azure AD felhasználói fiókjainak kiépítéséhez a ITRP által biztosított felhasználói fiók-létrehozási eszközt vagy API-t használhatja.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Most az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panel használatával kell tesztelni.

Amikor kiválasztja a ITRP csempét a hozzáférési panelen, automatikusan be kell jelentkeznie arra a ITRP-példányra, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [alkalmazások elérése és használata a saját alkalmazások portálon](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Oktatóanyagok SaaS-alkalmazások az Azure Active Directoryval való integrálásához](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)