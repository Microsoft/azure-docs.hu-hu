---
title: 'Oktatóanyag: Azure Active Directory integráció a AnswerHub-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és AnswerHub között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: 284ec65955de60f41d7bb0f97bf608faba3d4b79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97672590"
---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Oktatóanyag: Azure Active Directory integráció a AnswerHub

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a AnswerHub a Azure Active Directory (Azure AD) szolgáltatással.
A AnswerHub az Azure AD-vel való integrálása a következő előnyöket biztosítja:

* Az Azure AD segítségével szabályozhatja, hogy ki férhet hozzá a AnswerHub.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a AnswerHub az Azure AD-fiókjával (egyszeri bejelentkezéssel).
* A fiókokat egy központi helyről kezelheti: a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg az [egyszeri bejelentkezést a Azure Active Directory alkalmazásaihoz](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció AnswerHub való konfigurálásához a következőkre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, elindíthat egy [hónapos próbaverziót](https://azure.microsoft.com/pricing/free-trial/).
* Az egyszeri bejelentkezést engedélyező AnswerHub-előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A AnswerHub támogatja az SP által kezdeményezett egyszeri bejelentkezést.

## <a name="add-answerhub-from-the-gallery"></a>AnswerHub hozzáadása a gyűjteményből

A AnswerHub Azure AD-be való integrálásának beállításához hozzá kell adnia a AnswerHub a katalógusból a felügyelt SaaS-alkalmazásokhoz.

**AnswerHub hozzáadása a gyűjteményből:**

1. A [Azure Portal](https://portal.azure.com)a bal oldali ablaktáblán válassza a **Azure Active Directory** lehetőséget.

    ![Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

3. Alkalmazás hozzáadásához válassza az ablak tetején található **új alkalmazás** lehetőséget.

    ![Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **AnswerHub** kifejezést. Válassza a **AnswerHub** lehetőséget az eredmények listájában, majd kattintson a **Hozzáadás** gombra.

    ![AnswerHub az eredmények listájában](common/search-new-app.png)

## <a name="set-up-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés beállítása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli a AnswerHub használatával egy Britta Simon nevű teszt felhasználó használatával.
Az egyszeri bejelentkezéshez létre kell hoznia egy kapcsolatot az Azure AD-felhasználó és a megfelelő felhasználó között a AnswerHub-ben.

Az Azure AD egyszeri bejelentkezés AnswerHub való konfigurálásához és teszteléséhez a következő feladatokat kell elvégeznie:

1. Az [Azure ad egyszeri bejelentkezés konfigurálásával](#configure-azure-ad-single-sign-on) engedélyezheti a felhasználók számára a funkció használatát.
2. [Konfigurálja az AnswerHub egyszeri bejelentkezést](#configure-answerhub-single-sign-on) az alkalmazás oldalának egyszeri bejelentkezési beállításainak beállításához.
3. [Hozzon létre egy Britta Simon nevű Azure ad-teszt felhasználót](#create-an-azure-ad-test-user) .
4. [Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user) , hogy a Britta Simon engedélyezze az Azure ad egyszeri bejelentkezés használatát.
5. Hozzon létre egy olyan AnswerHub-teszt felhasználót, amely megfelel a-nek, és az az Azure AD-teszt felhasználóhoz van társítva.
6. Az [egyszeri bejelentkezés tesztelésével](#test-single-sign-on) ellenőrizheti, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezést állítja be a Azure Portal.

**Az Azure AD egyszeri bejelentkezés konfigurálása a AnswerHub:**

1. A [Azure Portal](https://portal.azure.com/) **AnswerHub** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezés gomb](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezés – metódus kiválasztása párbeszédpanel](common/select-saml-option.png)

3. Az **egyszeres Sign-On beállítása az SAML-vel** lapon kattintson a Szerkesztés ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszeri Sign-On beállítása SAML-oldallal](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![Alapszintű SAML-konfiguráció szakasz](common/sp-identifier.png)

    a. A **bejelentkezési URL-cím** mezőben adjon meg egy URL-címet, amely a következő mintával rendelkezik: `https://<company>.answerhub.com`

    b. Az **azonosító (entitás azonosítója)** mezőben adjon meg egy URL-címet, amely a következő mintával rendelkezik: `https://<company>.answerhub.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Az értékek lekéréséhez lépjen kapcsolatba a [AnswerHub támogatási csoportjával](mailto:success@answerhub.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban válassza a **tanúsítvány (Base64)** melletti **letöltési** hivatkozást, és az igényeinek megfelelő beállítást, és mentse a tanúsítványt a számítógépre.

    ![Tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **AnswerHub beállítása** szakaszban másolja a megfelelő URL-címet vagy URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

   A következő URL-címeket másolhatja:
    - Bejelentkezési URL

    - Azure AD-azonosító

    - Kijelentkezési URL-cím

### <a name="configure-answerhub-single-sign-on"></a>AnswerHub egyszeri bejelentkezés konfigurálása

Ebben a szakaszban a AnswerHub való egyszeri bejelentkezést állíthatja be.  

**Az AnswerHub egyszeri bejelentkezés konfigurálása:**

1. Egy másik böngészőablakban jelentkezzen be a AnswerHub vállalati webhelyre rendszergazdaként.

    > [!NOTE]
    > Ha segítségre van szüksége a AnswerHub konfigurálásához, forduljon a [AnswerHub támogatási csapatához](mailto:success@answerhub.com.).

2. Nyissa meg az **adminisztrációt**.

3. A **felhasználók és csoportok** lap bal oldali ablaktábláján, a **közösségi beállítások** szakaszban válassza az **SAML-telepítő** elemet.

4. A **identitásszolgáltató-konfiguráció** lapon hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: a AnswerHub lap, amelyen a felhasználók & csoportok lap van kijelölve.](./media/answerhub-tutorial/ic785172.png "SAML-telepítés")  

    a. A **identitásszolgáltató bejelentkezési URL-címe** mezőbe illessze be a Azure Portalból másolt **bejelentkezési URL-címet** .

    b. A **identitásszolgáltató kijelentkezési URL-címe** mezőben illessze be a Azure Portalból másolt **KIJELENTKEZÉSI URL-címet** .

    c. A **identitásszolgáltató-név azonosítójának formátuma** mezőbe írja be a Azure Portal **felhasználói attribútumok** szakaszában kiválasztott **azonosító** értékét.

    d. Válassza **a kulcsok és tanúsítványok** lehetőséget.

5. A **kulcsok és tanúsítványok** szakaszban hajtsa végre a következő lépéseket:

    ![Kulcsok és tanúsítványok szakasz](./media/answerhub-tutorial/ic785173.png "Kulcsok és tanúsítványok")  

    a. Nyissa meg a Jegyzettömbben a Azure Portal letöltött Base64 kódolású tanúsítványt, másolja a tartalmát, majd illessze be a tartalmat a **identitásszolgáltató nyilvános kulcs (X509 formátum)** mezőbe.

    b. Kattintson a **Mentés** gombra.

6. A **identitásszolgáltató-konfiguráció** lapon válassza a **Mentés** újra lehetőséget.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy Britta Simon nevű teszt felhasználót hoz létre a Azure Portal.

**Azure AD-teszt felhasználó létrehozása:**

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.

    ![Azure Active Directory, felhasználók, minden felhasználó kiválasztása](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![Felhasználói tulajdonságok](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .

    b. A **Felhasználónév** mezőbe írja be a **brittasimon \@<yourcompanydomain. Extension>** értéket.  
    Például: BrittaSimon@contoso.com.

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.

    d. Válassza a **Létrehozás** lehetőséget.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés használatához a Britta-t kell beállítania a felhasználóknak a AnswerHub való hozzáférés biztosításával.

**Az Azure AD-teszt felhasználójának hozzárendeléséhez:**

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **AnswerHub** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **AnswerHub** lehetőséget.

    ![Alkalmazások listája](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![Felhasználók és csoportok kiválasztása](common/users-groups-blade.png)

4. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![Hozzárendelési ablaktábla hozzáadása](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a **felhasználók** listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-állításban a szerepkör értékét várja, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a listáról a felhasználó számára. 

7. Kattintson a képernyő alján található **kiválasztás** gombra.

8. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés** lehetőséget.

### <a name="create-an-answerhub-test-user"></a>AnswerHub-teszt felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a AnswerHub, hozzá kell adnia őket a AnswerHub-ben. A AnswerHub-ben ez a feladat manuálisan történik.

**Felhasználói fiók beállítása:**

1. Jelentkezzen be a **AnswerHub** vállalati webhelyre rendszergazdaként.

2. Nyissa meg az **adminisztrációt**.

3. Válassza a **felhasználók & csoportok** lapot.

4. A bal oldali ablaktábla **felhasználók kezelése** területén válassza a **felhasználók létrehozása vagy importálása** lehetőséget, majd válassza a **felhasználók & csoportok** lehetőséget.

    ![Képernyőfelvétel: a AnswerHub lap, amelyen a felhasználók & csoportok lap van kiválasztva, és a felhasználók létrehozása vagy importálása hivatkozás kinevezett.](./media/answerhub-tutorial/ic785175.png "Felhasználók & csoportok")

5. A megfelelő mezőkben adja meg a hozzáadni kívánt érvényes Azure AD **-fiók e-mail-címét**, **felhasználónevét** és **jelszavát** , majd kattintson a **Mentés** gombra.

> [!NOTE]
> Az Azure AD felhasználói fiókjainak beállításához bármely más, a AnswerHub által biztosított felhasználói fiók-létrehozási eszközt vagy API-t használhat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Amikor kiválasztja a AnswerHub csempét a hozzáférési panelen, automatikusan be kell jelentkeznie arra a AnswerHub, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Oktatóanyagok az SaaS-alkalmazások integrálásához Azure Active Directory](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)