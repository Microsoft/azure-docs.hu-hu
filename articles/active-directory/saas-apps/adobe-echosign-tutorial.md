---
title: 'Oktatóanyag: Azure Active Directory integráció az Adobe Sign szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az Adobe Sign között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: 7162c38aae2fec4ea21aae56fa8c3649f7a55425
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101649961"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Oktatóanyag: Azure Active Directory integráció az Adobe Sign szolgáltatással

Ez az oktatóanyag azt ismerteti, hogyan integrálható az Adobe Sign Azure Active Directory (Azure AD) szolgáltatással. Ha az Azure AD-val integrálja az Adobe-bejelentkezést, a következőket teheti:

* Szabályozza az Azure AD-t, hogy ki férhet hozzá az Adobe Sign szolgáltatáshoz.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az Adobe-ba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:
 
* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Az Adobe Sign egyszeri bejelentkezés (SSO) használatára képes előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* Az Adobe Sign támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="add-adobe-sign-from-the-gallery"></a>Adobe-bejelentkezés hozzáadása a gyűjteményhez

Az Adobe-bejelentkezés az Azure AD-be való integrálásának konfigurálásához fel kell vennie az Adobe-bejelentkezést a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be az **Adobe Sign** kifejezést a keresőmezőbe.
1. Válassza az **Adobe Sign** from Results (eredmények) panelt, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-adobe-sign"></a>Az Azure AD SSO konfigurálása és tesztelése az Adobe Sign szolgáltatáshoz

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az Adobe Sign szolgáltatással konfigurálhatja és tesztelheti egy **Britta Simon** nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és az Adobe-bejelentkezéshez kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés az Adobe Sign használatával történő konfigurálásához és teszteléséhez a következő lépéseket kell elvégeznie:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
    1. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
1. Az **[Adobe Sign SSO konfigurálása](#configure-adobe-sign-sso)** – az egyes Sign-On beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre egy Adobe Sign test User](#create-adobe-sign-test-user)** -t, hogy az Azure ad-beli felhasználóhoz kapcsolódó Britta
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés az Adobe Sign használatával történő konfigurálásához hajtsa végre a következő lépéseket:

1. Az Azure Portal az **Adobe Sign** Application Integration lapon válassza az **egyszeri bejelentkezés** lehetőséget.

1. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

1. Az **egyszeres Sign-On beállítása az SAML-vel** lapon kattintson a ceruza ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<companyname>.echosign.com/`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<companyname>.echosign.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Az értékek megszerzéséhez lépjen kapcsolatba az [Adobe Sign ügyfél-támogatási csapatával](https://helpx.adobe.com/in/contact/support.html) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. Az **Adobe-aláírás beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény szerint.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát az Adobe Sign elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **Adobe Sign** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="configure-adobe-sign-sso"></a>Az Adobe Sign SSO konfigurálása

1. A konfiguráció előtt lépjen kapcsolatba az [Adobe Sign ügyfél-támogatási csapatával](https://helpx.adobe.com/in/contact/support.html) , és adja hozzá a tartományt az Adobe Sign engedélyezési listájában. A következő módon adhatja hozzá a tartományt:

    a. Az [Adobe Sign ügyfél-támogatási csapata](https://helpx.adobe.com/in/contact/support.html) véletlenszerűen generált tokent küld. A tartományhoz a token a következőhöz hasonló lesz: **Adobe-Sign-ellenőrzés = xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Tegye közzé az ellenőrző tokent egy DNS-szöveges rekordban, és értesítse az [Adobe Sign ügyfél támogatási csapatát](https://helpx.adobe.com/in/contact/support.html).

    > [!NOTE]
    > Ez eltarthat néhány napig, vagy hosszabb időt is igénybe vehet. Vegye figyelembe, hogy a DNS-propagálás késése azt jelenti, hogy a DNS-ben közzétett érték nem jelenik meg egy órán belül. A rendszergazdának ismernie kell a token DNS-szöveges rekordban való közzétételének módját.

    c. Ha a támogatási jegyen keresztül értesíti az [Adobe Sign-ügyfél támogatási csapatát](https://helpx.adobe.com/in/contact/support.html) , a jogkivonat közzététele után érvényesíti a tartományt, és hozzáadja a fiókjához.

    d. Általában a következő módon teheti közzé a tokent egy DNS-rekordon:

    * Jelentkezzen be a tartományi fiókjába
    * A DNS-rekord frissítésére szolgáló oldal megkeresése. Ezt a lapot DNS-kezelésnek, névkiszolgálók felügyeletének vagy speciális beállításoknak lehet nevezni.
    * Keresse meg a tartományhoz tartozó TXT-rekordokat.
    * Adjon hozzá egy TXT-rekordot az Adobe által megadott teljes jogkivonat-értékkel.
    * Mentse a módosításokat.

1. Egy másik böngészőablakban jelentkezzen be az Adobe Sign céges webhelyre rendszergazdaként.

1. Az SAML menüben válassza a **Fiókbeállítások**  >  **SAML-beállítások** elemet.

    ![Az Adobe Sign SAML-beállítások oldalának képernyőképe](./media/adobe-echosign-tutorial/settings.png "Fiók")

1. Az **SAML-beállítások** szakaszban hajtsa végre a következő lépéseket:

    ![Képernyőkép, amely kiemeli az SAML-beállításokat, beleértve az SAML Kötelezőt is.](./media/adobe-echosign-tutorial/saml1.png "SAML-beállítások")

   ![Az SAML-beállítások képernyőképe](./media/adobe-echosign-tutorial/saml.png "SAML-beállítások")

   a. Az **SAML mód** területen válassza az **SAML kötelező** lehetőséget.

   b. Jelölje be a **EchoSign-fiók rendszergazdáinak engedélyezése a EchoSign hitelesítő adataik használatával történő bejelentkezéshez** lehetőséget.

   c. A **felhasználó létrehozása** területen válassza az SAML-n **keresztül hitelesített felhasználók automatikus hozzáadása** lehetőséget.

   d. Illessze be az **Azure ad-azonosítót**, amelyet a Azure Portal másolt a **IDENTITÁSSZOLGÁLTATÓ-entitás azonosítója** szövegmezőbe.

   e. Illessze be a **bejelentkezési URL-címet**, amelyet Azure Portal a **identitásszolgáltató bejelentkezési URL-címe** szövegmezőbe másolt.

   f. Illessze be a **kijelentkezési URL-címet**, amelyet a Azure Portal a **identitásszolgáltató KIJELENTKEZÉSI URL-címe** szövegmezőbe másolt.

   : Nyissa meg a letöltött **tanúsítvány (Base64)** fájlt a Jegyzettömbben. Másolja a tartalmát a vágólapra, majd illessze be a **identitásszolgáltató-tanúsítvány** szövegmezőbe.

   h. Válassza a **módosítások mentése** lehetőséget.

### <a name="create-adobe-sign-test-user"></a>Adobe Sign test-felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek az Adobe Signbe, az Adobe Sign szolgáltatásba kell azokat kiépíteni. Ez egy manuális feladat.

>[!NOTE]
>Az Azure AD felhasználói fiókjainak kiépítéséhez az Adobe Sign által biztosított bármely más, az Adobe Sign felhasználói fiók létrehozására szolgáló eszközt vagy API-t használhat. 

1. Jelentkezzen be az **Adobe Sign** céges webhelyre rendszergazdaként.

2. A felső menüben válassza a **fiók** lehetőséget. Ezután a bal oldali ablaktáblán válassza a **felhasználók & csoportok**  >  **új felhasználó létrehozása** lehetőséget.

    ![Képernyőkép az Adobe Sign céges webhelyről, a fiókkal, a felhasználókkal &csoportokkal, és új felhasználó kijelölése](./media/adobe-echosign-tutorial/account.png "Fiók")

3. Az **új felhasználó létrehozása** szakaszban hajtsa végre a következő lépéseket:

    ![Képernyőkép az új felhasználó létrehozása szakaszról](./media/adobe-echosign-tutorial/user.png "Felhasználó létrehozása")

    a. Írja be a kapcsolódó szövegmezőbe felépíteni kívánt érvényes Azure AD **-fiók E-mail címét**, **utónevét** és **vezetéknevét** .

    b. Válassza a **felhasználó létrehozása** lehetőséget.

>[!NOTE]
>A Azure Active Directory fiók tulajdonosa egy e-mailt kap, amely tartalmazza a fiók megerősítésére szolgáló hivatkozást, mielőtt az aktívvá válna. 

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja az Adobe Sign bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül az Adobe Sign bejelentkezési URL-címére, és indítsa el innen a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások Adobe Sign csempére kattint, automatikusan be kell jelentkeznie arra az Adobe-aláírásba, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

Az Adobe Sign konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).