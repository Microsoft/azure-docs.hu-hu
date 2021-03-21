---
title: 'Oktatóanyag: a Azure Active Directory integrációja EGYESÍTve | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és az EGYESÍTő között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: 1fa1c40c96694b71bc762050998257102b687c18
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101650625"
---
# <a name="tutorial-azure-active-directory-integration-with-unifi"></a>Oktatóanyag: a Azure Active Directory integrációja EGYESÍTve

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az EGYSÉGESÍTett Azure Active Directory (Azure AD) szolgáltatással. Az EGYESÍTő és az Azure AD integrálásával a következőket teheti:

* Szabályozhatja az Azure AD-t, hogy ki fér hozzá az EGYESÍTő szolgáltatásokhoz.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkeznek legyenek az Azure AD-fiókjával EGYESÍTve.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* EGYSÉGESÍTett egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* Az EGYSÉGESÍTett verziója **az SP és a identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést
* Az EGYSÉGESÍTett rendszer támogatja a felhasználók **automatikus** üzembe helyezését.

## <a name="add-unifi-from-the-gallery"></a>EGYESÍTő csoport hozzáadása a katalógusból

Az EGYESÍTő integrációjának az Azure AD-be való konfigurálásához EGYESÍTeni kell a katalógusból a felügyelt SaaS-alkalmazások listáját.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be az **egyesítő** kifejezést a keresőmezőbe.
1. Válassza ki az **egyesítő** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-unifi"></a>Azure AD SSO konfigurálása és tesztelése az EGYSÉGESÍTett szolgáltatásokhoz

Konfigurálja és tesztelje az Azure AD SSO-t az EGYESÍTő segítségével egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és az EGYSÉGESÍTett kapcsolódó felhasználó között.

Az Azure AD SSO EGYSÉGESÍTett konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Egységesített egyszeri bejelentkezés konfigurálása](#configure-unifi-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre egyesítő teszt felhasználót](#create-unifi-test-user)** – ha a felhasználó Azure ad-képviseletéhez társított "B
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal az **egyesítő** alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    Az **azonosító** szövegmezőbe írja be az URL-címet: `INVIEWlabs`

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet:  `https://app.discoverunifi.com/login`

6. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

7. Az **egyesítő egység beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

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

Ebben a szakaszban a B. Simon használatával engedélyezheti az Azure egyszeri bejelentkezést az EGYSÉGESÍTett hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **egységesített** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-unifi-sso"></a>EGYSÉGESÍTett egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be az **egységesített** vállalati webhelyre rendszergazdaként.

2. Kattintson a **felhasználók** elemre.

    ![A képernyőfelvételen a EGYSÉGESÍTett helyről kiválasztott felhasználók láthatók.](./media/unifi-tutorial/app-1.png)

3. Kattintson az **új identitás-szolgáltató hozzáadása** lehetőségre.

    ![A képernyőképen a kiválasztott ad új identitás-szolgáltató látható.](./media/unifi-tutorial/app-2.png)

4. Az **identitás-szolgáltató hozzáadása** szakaszban hajtsa végre a következő lépéseket:

    ![A képernyőképen az Identity Provider hozzáadása látható, ahol megadhatja a leírt értékeket.](./media/unifi-tutorial/app-3.png) 

    a. A **szolgáltató neve** szövegmezőbe írja be a személyazonosság-szolgáltató nevét.

    b. A **szolgáltató URL-címe** szövegmezőbe illessze be a **bejelentkezési URL-címet** , amelyet a Azure Portalból másolt.

    c. Nyissa meg a Jegyzettömbben Azure Portal letöltött tanúsítványt, távolítsa el **---a tanúsítványt---** és a **---END Certificate---** címkét, majd illessze be a fennmaradó tartalmat a **tanúsítvány** szövegmezőbe.

    d. Jelölje be az **alapértelmezett szolgáltató** jelölőnégyzetet.

### <a name="create-unifi-test-user"></a>EGYESÍTő teszt felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre. Az **egységesített** rendszer támogatja az automatikus felhasználó-kiépítés használatát, így nincs szükség manuális lépésekre. A rendszer automatikusan létrehozza a felhasználókat az Azure AD-ból való sikeres hitelesítés után.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ez átirányítja a EGYSÉGESÍTett bejelentkezés URL-címére, ahol elindíthatja a bejelentkezési folyamatot.  

* Lépjen az EGYSÉGESÍTett bejelentkezési URL-címre, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra az egységesített csoportba, amelyhez be szeretné állítani az egyszeri bejelentkezést. 

A Microsoft My Apps használatával bármilyen módban tesztelheti az alkalmazást. Ha a saját alkalmazások EGYESÍTő csempére kattint, ha az SP módban van konfigurálva, a rendszer átirányítja az alkalmazás bejelentkezési lapjára a bejelentkezési folyamat elindításához, és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, akkor automatikusan be kell jelentkeznie arra a EGYESÍTő csoportba, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

Az EGYSÉGESÍTett konfigurálását követően kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).