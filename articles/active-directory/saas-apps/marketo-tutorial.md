---
title: 'Oktatóanyag: Azure Active Directory integráció a Marketo-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Marketo között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: 433303bf0d51eff3bd3ab37726c9e98e8a766d25
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101686954"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Oktatóanyag: Azure Active Directory integráció a Marketo

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Marketo a Azure Active Directory (Azure AD) szolgáltatással.
A Marketo és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a Marketo.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Marketo (egyszeri bejelentkezés) az Azure AD-fiókokkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Marketo való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Marketo egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Marketo támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="adding-marketo-from-the-gallery"></a>Marketo hozzáadása a gyűjteményből

A Marketo Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Marketo a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Marketo** kifejezést a keresőmezőbe.
1. Válassza ki a **Marketo** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-marketo"></a>Azure AD SSO konfigurálása és tesztelése a Marketo-hez

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az Marketo-mel konfigurálja és teszteli a **Britta Simon** nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Marketo kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés Marketo való konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad SSO teszteléséhez a Britta Simon használatával.
    1. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon használatának engedélyezése az Azure ad SSO használatával.
2. **[MARKETO SSO konfigurálása](#configure-marketo-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre Marketo-teszt felhasználót](#create-marketo-test-user)** – hogy a Marketo Britta, a felhasználó Azure ad-képviseletéhez kapcsolódó partnerrel rendelkezzen.
3. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **Marketo** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet: `https://saml.marketo.com/sp`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://login.marketo.com/saml/assertion/<munchkinid>`

    c. A **továbbítási állapot** szövegmezőbe írja be a következő mintát használó URL-címet: `https://<munchkinid>.marketo.com/`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges válasz URL-címével és a továbbítási állapottal. Az értékek lekéréséhez forduljon a Marketo ügyfélszolgálati [csapatához](https://investors.marketo.com/contactus.cfm) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Marketo beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Marketo.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Marketo** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-marketo-sso"></a>Marketo SSO konfigurálása

1. A Marketo belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **Marketo beállítása** elemre, majd a Marketo alkalmazásra irányítja. Itt adja meg a rendszergazdai hitelesítő adatokat a Marketo való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-6-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a Marketo, egy másik böngészőablakban jelentkezzen be a Marketo vállalati webhelyre rendszergazdaként.

1. Az alkalmazás Munchkin-AZONOSÍTÓjának beszerzéséhez hajtsa végre a következő műveleteket:
   
    a. Jelentkezzen be a Marketo alkalmazásba a rendszergazdai hitelesítő adatok használatával.
   
    b. Kattintson a **rendszergazda** gombra a felső navigációs ablaktáblán.
   
    ![Egyetlen Sign-On1 konfigurálása](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navigáljon az integráció menüre, és kattintson a **Munchkin hivatkozásra**.
   
    ![Egyetlen Sign-On2 konfigurálása](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. Másolja a képernyőn megjelenő Munchkin-azonosítót, és fejezze be a válasz URL-címét az Azure AD konfigurációs varázslóban.
   
    ![Egyetlen Sign-On3 konfigurálása](./media/marketo-tutorial/tutorial_marketo_12.png) 

2. Az egyszeri bejelentkezés az alkalmazásban való konfigurálásához kövesse az alábbi lépéseket:
   
    a. Jelentkezzen be a Marketo alkalmazásba a rendszergazdai hitelesítő adatok használatával.
   
    b. Kattintson a **rendszergazda** gombra a felső navigációs ablaktáblán.
   
    ![Egyetlen Sign-On4 konfigurálása](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navigáljon az integráció menüre, és kattintson az **egyszeri bejelentkezés** lehetőségre.
   
    ![Egyetlen Sign-On5 konfigurálása](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Az SAML-beállítások engedélyezéséhez kattintson a **Szerkesztés** gombra.
   
    ![Egyetlen Sign-On6 konfigurálása](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. **Engedélyezve** Egyetlen Sign-On beállítások.
   
    f. Illessze be az **Azure ad-azonosítót** a **kiállító azonosítója** szövegmezőbe.
   
    : Az **entitás-azonosító** szövegmezőbe írja be az URL-címet `http://saml.marketo.com/sp` .
   
    h. Válassza ki a felhasználói azonosító helyét **név-azonosító elemként**.
   
    ![Egyetlen Sign-On7 konfigurálása](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Ha a felhasználói azonosító nem UPN-érték, akkor módosítsa az értéket az attribútum lapon.
   
    i. Töltse fel a tanúsítványt, amelyet az Azure AD konfigurációs varázslójával töltött le. **Mentse** a beállításokat.
   
    j. Az átirányítási lapok beállításainak szerkesztése.
   
    k. Illessze be a **bejelentkezési URL-címet** a **bejelentkezési URL** szövegmezőbe.
   
    l. Illessze be a **kijelentkezési URL-címet** a **KIjelentkezési URL** -szövegmezőbe.
   
    m. A **hiba URL-címében** másolja a **Marketo-példány URL-címét** , és kattintson a **Save (Mentés** ) gombra a beállítások mentéséhez.
   
    ![Egyetlen Sign-On8 konfigurálása](./media/marketo-tutorial/tutorial_marketo_10.png)

3. Ha engedélyezni szeretné az egyszeri bejelentkezést a felhasználók számára, hajtsa végre a következő műveleteket:
   
    a. Jelentkezzen be a Marketo alkalmazásba a rendszergazdai hitelesítő adatok használatával.
   
    b. Kattintson a **rendszergazda** gombra a felső navigációs ablaktáblán.
   
    ![Egyetlen Sign-On9 konfigurálása](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Lépjen a **Biztonság** menüre, és kattintson a **bejelentkezési beállítások** elemre.
   
    ![Egyetlen Sign-On10 konfigurálása](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. Keresse meg az **egyszeri bejelentkezés megkövetelése** beállítást, és **mentse** a beállításokat.
   
    ![Egyetlen Sign-On11 konfigurálása](./media/marketo-tutorial/tutorial_marketo_14.png)


### <a name="create-marketo-test-user"></a>Marketo-tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a Marketo-ben. a következő lépésekkel hozhat létre felhasználót a Marketo platformon.

1. Jelentkezzen be a Marketo alkalmazásba a rendszergazdai hitelesítő adatok használatával.

2. Kattintson a **rendszergazda** gombra a felső navigációs ablaktáblán.
   
    ![Felhasználó1 tesztelése](./media/marketo-tutorial/tutorial_marketo_06.png) 

3. Navigáljon a **Biztonság** menüre, és kattintson a **felhasználók & szerepkörök** elemre.
   
    ![Felhasználó2 tesztelése](./media/marketo-tutorial/tutorial_marketo_19.png)  

4. Kattintson az **új felhasználó meghívása** hivatkozásra a felhasználók lapon
   
    ![user3 tesztelése](./media/marketo-tutorial/tutorial_marketo_15.png) 

5. Az új felhasználó meghívása varázsló adja meg a következő adatokat
   
    a. Adja meg a felhasználó **e-mail-** címét a szövegmezőben
   
    ![user4 tesztelése](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. Adja meg az **első nevet** a szövegmezőben
   
    c. Adja meg az **utolsó nevet**  a szövegmezőben
   
    d. Kattintson a **tovább** gombra

6. Az **engedélyek** lapon válassza ki a **userRoles** , és kattintson a **tovább** gombra.
   
    ![user5 tesztelése](./media/marketo-tutorial/tutorial_marketo_17.png)
7. Kattintson a **Küldés** gombra a felhasználó meghívásának elküldéséhez
   
    ![user6 tesztelése](./media/marketo-tutorial/tutorial_marketo_18.png)

8. A felhasználó megkapja az e-mail-értesítést, és a hivatkozásra kattintva megváltoztathatja a jelszót a fiók aktiválásához. 

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját.

* Kattintson az alkalmazás tesztelése Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a Marketo, amelyhez be szeretné állítani az egyszeri bejelentkezést

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások Marketo csempére kattint, automatikusan be kell jelentkeznie arra a Marketo, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

A Marketo konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).