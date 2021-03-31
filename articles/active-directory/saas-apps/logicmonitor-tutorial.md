---
title: 'Oktatóanyag: Azure Active Directory integráció a LogicMonitor-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és LogicMonitor között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: d5342782c26b5c274699bacc4ea0c7cdf5b7f880
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649408"
---
# <a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>Oktatóanyag: Azure Active Directory integráció a LogicMonitor

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a LogicMonitor a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az LogicMonitor-t az Azure AD-vel, a következőket teheti:

* A LogicMonitor-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a LogicMonitor az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció LogicMonitor való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/)kérhet egy hónapos próbaverziót.
* LogicMonitor egyszeri bejelentkezésre engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A LogicMonitor támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="add-logicmonitor-from-the-gallery"></a>LogicMonitor hozzáadása a gyűjteményből

A LogicMonitor Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a LogicMonitor a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **LogicMonitor** kifejezést a keresőmezőbe.
1. Válassza ki a **LogicMonitor** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-logicmonitor"></a>Azure AD SSO konfigurálása és tesztelése a LogicMonitor-hez

Konfigurálja és tesztelje az Azure AD SSO-t a LogicMonitor a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a LogicMonitor-ben.

Az Azure AD SSO és a LogicMonitor konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[LOGICMONITOR SSO konfigurálása](#configure-logicmonitor-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre LogicMonitor-teszt felhasználót](#create-logicmonitor-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-LogicMonitor rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **LogicMonitor** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![LogicMonitor tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<companyname>.logicmonitor.com`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<companyname>.logicmonitor.com`
    
    c. A **Válasz URL-címe (a fogyasztói szolgáltatás URL-címe)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://companyname.logicmonitor.com/santaba/saml/SSO/` 
  
    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Az értékek lekéréséhez forduljon a LogicMonitor ügyfélszolgálati [csapatához](https://www.logicmonitor.com/contact/) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **LogicMonitor beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a LogicMonitor.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **LogicMonitor** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="configure-logicmonitor-sso"></a>LogicMonitor SSO konfigurálása

1. Jelentkezzen be a **LogicMonitor** vállalati webhelyre rendszergazdaként.

2. A felső menüben kattintson a **Beállítások** elemre.

    ![Beállítások](./media/logicmonitor-tutorial/ic790052.png "Beállítások")

3. A bal oldalon található navigációs Denevérben kattintson az **egyszeri bejelentkezés** elemre.

    ![Egyszeri bejelentkezés](./media/logicmonitor-tutorial/ic790053.png "Egyszeri bejelentkezés")

4. Az **egyszeri bejelentkezés (SSO) beállításai** szakaszban hajtsa végre a következő lépéseket:

    ![Egyszeres Sign-On beállítások](./media/logicmonitor-tutorial/ic790054.png "Egyszeres Sign-On beállítások")

    a. Válassza az **egyszeri bejelentkezés engedélyezése** lehetőséget.

    b. **Alapértelmezett szerepkör-hozzárendelésként** válassza a **readonly** lehetőséget.

    c. Nyissa meg a letöltött metaadat-fájlt a Jegyzettömbben, majd illessze be a fájl tartalmát az **Identity Provider metaadatok** szövegmezőbe.

    d. Kattintson a **Save Changes** (Módosítások mentése) gombra.

### <a name="create-logicmonitor-test-user"></a>LogicMonitor-tesztelési felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók be tudjanak jelentkezni, a LogicMonitor alkalmazásba kell azokat kiépíteni a Azure Active Directory felhasználónevek használatával.

**A felhasználók üzembe helyezésének konfigurálásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a LogicMonitor vállalati webhelyre rendszergazdaként.

2. A felső menüben kattintson a **Beállítások**, majd a **szerepkörök és felhasználók** elemre.

    ![Szerepkörök és felhasználók](./media/logicmonitor-tutorial/ic790056.png "Szerepkörök és felhasználók")

3. Kattintson a **Hozzáadás** parancsra.

4. A **fiók hozzáadása** szakaszban hajtsa végre a következő lépéseket:

    ![Fiók hozzáadása](./media/logicmonitor-tutorial/ic790057.png "Fiók hozzáadása")

    a. Írja be annak a Azure Active Directory felhasználónak a **felhasználónevét**, **e-mail-címét**, **jelszavát** és **Újraírási** azonosítóját, amelyet szeretne a kapcsolódó szövegmezőbe beépíteni.

    b. Válassza a **szerepkörök**, az **engedélyek megtekintése** és az **állapot** lehetőséget.

    c. Kattintson a **Submit (Küldés**) gombra.

> [!NOTE]
> A LogicMonitor által biztosított egyéb LogicMonitor-létrehozási eszközöket vagy API-kat használhatja Azure Active Directory felhasználói fiókok kiépítéséhez.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a LogicMonitor bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül a LogicMonitor bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások LogicMonitor csempére kattint, automatikusan be kell jelentkeznie arra a LogicMonitor, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

A LogicMonitor konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).