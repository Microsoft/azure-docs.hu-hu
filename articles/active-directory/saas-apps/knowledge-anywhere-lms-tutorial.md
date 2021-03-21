---
title: 'Oktatóanyag: Azure Active Directory integráció az ismeretekkel bárhol az LMS szolgáltatással | Microsoft Docs'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory és az ismeretek között bárhol az LMS-ben.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: jeedes
ms.openlocfilehash: eab1304408f896f5c9d736488997c1246ae167f2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92458982"
---
# <a name="tutorial-integrate-knowledge-anywhere-lms-with-azure-active-directory"></a>Oktatóanyag: az ismeretek integrálása bárhová az LMS Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a tudásbázist a Azure Active Directory (Azure AD) használatával az LMS szolgáltatásba. Ha az Azure AD-vel bárhonnan integrálja a tudást, a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér az ismeretekhez bárhol az LMS-ben.
* Lehetővé teheti a felhasználók számára, hogy az Azure AD-fiókjával bárhonnan automatikusan bejelentkezzenek a tudásba, bárhol is legyenek az LMS.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Az egyszeri bejelentkezési (SSO) előfizetés engedélyezett előfizetésének ismerete.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben. A Knowledge Anywhere LMS támogatja az **SP** által kezdeményezett egyszeri bejelentkezést, és a felhasználó általi üzembe helyezést **is** támogatja.

## <a name="adding-knowledge-anywhere-lms-from-the-gallery"></a>Tudásbázisbeli LMS hozzáadása a gyűjteményhez

Ahhoz, hogy az Azure AD-ben az ismereteket bárhová integrálni tudja, a katalógusból a felügyelt SaaS-alkalmazások listájára is hozzá kell adnia a tudást, bárhol az LMS-t.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Knowledge Anywhere LMS** kifejezést a keresőmezőbe.
1. Válassza az **ismeretek bárhol az LMS** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t a Knowledge bárhol, az LMS használatával egy **B. Simon** nevű teszt felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Tudásbázisban.

Ha az Azure AD SSO-t a Knowledge Anywhere-sel szeretné konfigurálni és tesztelni, végezze el a következő építőelemeket:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t, hogy a felhasználók használhatják ezt a funkciót.
2. **[Állítsa be a Knowledge Anywhere](#configure-knowledge-anywhere-lms)** -t az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
4. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** , hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
5. **[Hozzon létre tudást bárhol az LMS-teszt felhasználónál](#create-knowledge-anywhere-lms-test-user)** , hogy a "B.
6. Ellenőrizze az **[SSO](#test-sso)** -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/), a **Knowledge Anywhere** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    1. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<CLIENTNAME>.knowledgeanywhere.com/`

    1. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<CLIENTNAME>.knowledgeanywhere.com/SSO/SAML/Response.aspx?<IDPNAME>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel, amelyet az oktatóanyag későbbi részében ismertet.

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<CLIENTNAME>.knowledgeanywhere.com/`

    > [!NOTE]
    > A bejelentkezési URL-cím értéke nem valós. Frissítse ezt az értéket a tényleges bejelentkezési URL-címmel. Ha ezt az értéket szeretné megkapni, lépjen kapcsolatba az ügyfélszolgálattal [bárhol az LMS ügyfél-támogatási csapattal](https://knowany.zendesk.com/hc/en-us/articles/360000469034-SAML-2-0-Single-Sign-On-SSO-Set-Up-Guide) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

   ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **tudás beállítása bárhol LMS** -ben szakaszban másolja ki a megfelelő URL-címeket a követelmény alapján.

   ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-knowledge-anywhere-lms"></a>Az ismeretek konfigurálása bárhol az LMS-ben

1. Ha szeretné automatizálni a konfigurációt a Tudásbázisban, a **bővítmény telepítése** lehetőségre kattintva telepítenie kell **az alkalmazások biztonságos bejelentkezési böngésző bővítményét** .

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **beállítási ismeretek bárhová az LMS** -ben lehetőségre, hogy a tudás bárhonnan elérhetővé válik az LMS alkalmazásban. Itt adja meg a rendszergazdai hitelesítő adatokat, amelyekkel bármikor bejelentkezhet a tudásba. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-7-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha kézzel szeretné beállítani az LMS szolgáltatást, nyisson meg egy új böngészőablakot, és jelentkezzen be a tudomására bárhová az LMS vállalati webhelyén rendszergazdaként, és hajtsa végre a következő lépéseket:

4. Válassza a **site (hely** ) lapot.

    ![A képernyőképen a hely lap látható.](./media/knowledge-anywhere-lms-tutorial/configure1.png)

5. Válassza az **SAML-beállítások** lapot.

    ![A képernyőfelvételen a kiválasztott SAML-beállításokkal rendelkező oldalak láthatók.](./media/knowledge-anywhere-lms-tutorial/configure2.png)

6. Kattintson az **új hozzáadása** gombra.

    ![Képernyőfelvétel: az új hozzáadása gomb a szolgáltató beállításaiban.](./media/knowledge-anywhere-lms-tutorial/configure3.png)

7. Az **SAML-beállítások hozzáadása/frissítése** oldalon hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: az SAML-beállítások hozzáadása/frissítése oldal, ahol az itt ismertetett módosításokat végezheti el.](./media/knowledge-anywhere-lms-tutorial/configure4.png)

    a. Adja meg a IDENTITÁSSZOLGÁLTATÓ nevét a szervezete számára. Pl.: – `Azure` .

    b. Az **identitásszolgáltató entitás-azonosító** szövegmezőben illessze be az **Azure ad-azonosító** értékét, amelyet a Azure Portalból másolt.

    c. A **identitásszolgáltató URL-címe** szövegmezőbe illessze be a **bejelentkezési URL-cím** értéket, amelyet a Azure Portalból másolt.

    d. Nyissa meg a letöltött tanúsítványfájl a Azure Portal a Jegyzettömbben, másolja a tanúsítvány tartalmát, majd illessze be a **tanúsítvány** szövegmezőbe.

    e. A **kijelentkezési URL** szövegmezőben illessze be a **KIJELENTKEZÉSI URL-címet** , amelyet a Azure Portalból másolt.

    f. Válassza ki a **fő helyet** a **tartomány** legördülő menüjéből.

    : Másolja az **SP-entitás azonosítójának** értékét, és illessze be **azonosító** szövegmezőbe a Azure Portal **alapszintű SAML-konfiguráció** szakaszában.

    h. Másolja az **SP Response (ACS) URL** értékét, és illessze be a **Válasz URL** -szövegmezőbe a Azure Portal **alapszintű SAML-konfiguráció** szakaszában.

    i. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B. Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `BrittaSimon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban lehetővé teszi, hogy a B. Simon az Azure egyszeri bejelentkezést használja azáltal, hogy hozzáférést biztosít a tudáshoz bárhol az LMS-ben.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **ismeretek bárhová az LMS** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-knowledge-anywhere-lms-test-user"></a>Tudás létrehozása bárhol az LMS-tesztkörnyezet felhasználója

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre a tudásban, bárhol az LMS-ben. A Knowledge Anywhere-LMS az igény szerinti felhasználói üzembe helyezést is támogatja, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a tudásban bárhol az LMS-ben, akkor a hitelesítés után létrejön egy újat.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ha a hozzáférési panelen kiválasztja a tudás bárhol az LMS csempét, akkor automatikusan be kell jelentkeznie arra a tudásra, amelybe az SSO-t beállította. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)