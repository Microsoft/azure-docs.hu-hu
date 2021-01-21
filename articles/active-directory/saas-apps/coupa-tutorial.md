---
title: 'Oktatóanyag: Azure Active Directory-integráció a Coupa szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Coupa között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/14/2021
ms.author: jeedes
ms.openlocfilehash: c6932292b86924cfab6d17c315cbd6946bd63f61
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621329"
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>Oktatóanyag: Azure Active Directory a Coupa-integrációval

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Coupa Azure Active Directory (Azure AD) szolgáltatással. Ha az Azure AD-vel integrálja a Coupa-t, a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér a Coupa szolgáltatáshoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Coupa szolgáltatásba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció és a Coupa konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/)kérhet egy hónapos próbaverziót.
* Coupa egyszeri bejelentkezésre alkalmas előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A coupa támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="add-coupa-from-the-gallery"></a>Coupa hozzáadása a katalógusból

A Coupa Azure AD-integrációjának konfigurálásához hozzá kell adnia a Coupa elemet a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **coupa** kifejezést a keresőmezőbe.
1. Válassza a **coupa** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-coupa"></a>Azure AD SSO konfigurálása és tesztelése a Coupa szolgáltatáshoz

Konfigurálja és tesztelje az Azure AD SSO-t a Coupa segítségével egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a Coupa-ben.

Az Azure AD SSO és a Coupa szolgáltatás konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[coupa SSO konfigurálása](#configure-coupa-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre coupa-teszt felhasználót](#create-coupa-test-user)** – hogy rendelkezzen egy, a felhasználó Azure ad-képviseletéhez csatolt B. Simon incoupé-ügyféllel.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **Coupe** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<companyname>.coupahost.com`

    > [!NOTE]
    > A bejelentkezési URL-cím értéke nem valós. Frissítse ezt az értéket a tényleges Sign-On URL-címmel. Az érték beszerzéséhez forduljon a [coupa ügyfél-támogatási csapatához](https://success.coupa.com/Support/Contact_Us?) .

    b. Az **azonosító** mezőbe írja be az URL-címet:

    | Környezet  | URL-cím |
    |:-------------|----|
    | Sandbox | `sso-stg1.coupahost.com`|
    | Production | `sso-prd1.coupahost.com`|
    | | |

    c. A **Válasz URL-cím** szövegmezőbe írja be az URL-címet:

    | Környezet | URL-cím |
    |------------- |----|
    | Sandbox | `https://sso-stg1.coupahost.com/sp/ACS.saml2`|
    | Production | `https://sso-prd1.coupahost.com/sp/ACS.saml2`|
    | | |

4. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **coupa beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Coupa szolgáltatáshoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **coupa** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="configure-coupa-sso"></a>A Coupa SSO konfigurálása

1. Jelentkezzen be a Coupa vállalati webhelyre rendszergazdaként.

2. Nyissa meg a **beállítás \> biztonsági vezérlőt**.

    ![Biztonsági vezérlők](./media/coupa-tutorial/ic791900.png "Biztonsági vezérlők")

3. A **Bejelentkezés a coupa hitelesítő adatokkal** szakaszban hajtsa végre a következő lépéseket:

    ![Coupa SP-metaadatok](./media/coupa-tutorial/ic791901.png "Coupa SP-metaadatok")

    a. Válassza **a bejelentkezés az SAML használatával** lehetőséget.

    b. Kattintson a **Tallózás** gombra a Azure Portal letöltött metaadatok feltöltéséhez.

    c. Kattintson a **Mentés** gombra.

### <a name="create-coupa-test-user"></a>Coupa-teszt felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek a Coupa-be, a Coupa-be kell építeni.  

* A Coupa esetében a kiépítés manuális feladat.

**A felhasználók üzembe helyezésének konfigurálásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **coupa** vállalati webhelyre rendszergazdaként.

2. A felső menüben kattintson a **telepítés** elemre, majd kattintson a **felhasználók** elemre.

    ![Felhasználók](./media/coupa-tutorial/ic791908.png "Felhasználók")

3. Kattintson a **Létrehozás** lehetőségre.

    ![Felhasználók létrehozása](./media/coupa-tutorial/ic791909.png "Felhasználók létrehozása")

4. A **felhasználó létrehozása** szakaszban hajtsa végre a következő lépéseket:

    ![Felhasználó adatai](./media/coupa-tutorial/ic791910.png "Felhasználó adatai")

    a. Adja meg a kapcsolódó szövegmezőbe beépíteni kívánt érvényes Azure Active Directory-fiók **bejelentkezési** **nevét, utónevét,** **VEZETÉKNEVét**, **egy Sign-On azonosítóját**, **e-mail-** attribútumait.

    b. Kattintson a **Létrehozás** lehetőségre.

    >[!NOTE]
    >A Azure Active Directory fiók tulajdonosa egy e-mailt fog kapni, amely egy hivatkozással megerősíti a fiókot, mielőtt az aktívvá válna.
    >

>[!NOTE]
>Az Azure AD felhasználói fiókjainak kiépítéséhez a Coupa által biztosított bármely egyéb felhasználói fiók létrehozási eszközét vagy API-t használhatja.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a Coupa bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül a Coupa bejelentkezési URL-címére, és indítsa el innen a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Ha a saját alkalmazások Coupa csempére kattint, akkor automatikusan be kell jelentkeznie arra a Puccsba, amelyhez be kell állítania az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)használatába.

## <a name="next-steps"></a>Következő lépések

A Coupa konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)