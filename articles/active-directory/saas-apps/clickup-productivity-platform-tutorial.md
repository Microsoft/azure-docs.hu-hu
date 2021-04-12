---
title: 'Oktatóanyag: Azure Active Directory integráció a ClickUp hatékonyságnövelő platformmal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a ClickUp hatékonyságnövelő platformja között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/30/2021
ms.author: jeedes
ms.openlocfilehash: 29bc02466825aa2ea2c1a9ece2826b1262293392
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285193"
---
# <a name="tutorial-azure-active-directory-integration-with-clickup-productivity-platform"></a>Oktatóanyag: Azure Active Directory integráció a ClickUp hatékonyságnövelő platformmal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a ClickUp hatékonyságnövelő platformot Azure Active Directory (Azure AD) használatával. Ha integrálja a ClickUp hatékonyságnövelő platformot az Azure AD-vel, a következőket teheti:

* A ClickUp hatékonyságnövelő platformhoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek, hogy az Azure AD-fiókjával ClickUp a hatékonyságnövelő platformot.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* ClickUp a termelékenységi platform egyszeri bejelentkezés (SSO) engedélyezett előfizetését.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A ClickUp hatékonyságnövelő platform támogatja az **SP** által kezdeményezett egyszeri bejelentkezést.

## <a name="add-clickup-productivity-platform-from-the-gallery"></a>ClickUp hatékonyságnövelő platform hozzáadása a katalógusból

A ClickUp hatékonyságnövelő platform Azure AD-integrációjának konfigurálásához hozzá kell adnia a ClickUp hatékonyságnövelő platformot a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **ClickUp hatékonyságnövelő platform** kifejezést a keresőmezőbe.
1. Válassza ki a **ClickUp hatékonyságnövelő platformot** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-clickup-productivity-platform"></a>Azure AD SSO konfigurálása és tesztelése a ClickUp hatékonyságnövelő platformhoz

Konfigurálja és tesztelje az Azure AD SSO-t a ClickUp hatékonyságnövelő platformmal egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a ClickUp hatékonyságnövelő platformon.

Az Azure AD SSO ClickUp hatékonyságnövelő platformmal való konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[ClickUp hatékonyságnövelő platform SSO konfigurálása](#configure-clickup-productivity-platform-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre egy ClickUp hatékonyságnövelő platformot](#create-clickup-productivity-platform-test-user)** , amely a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-ClickUp.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **ClickUp hatékonyságnövelő platform** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet: `https://app.clickup.com/login/sso`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://api.clickup.com/v1/team/<team_id>/microsoft`

    > [!NOTE]
    > Az azonosító értéke nem valódi. Frissítse ezt az értéket a tényleges azonosítóval, amelyet az oktatóanyag későbbi részében ismertetünk.

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a ClickUp hatékonyságnövelő platformhoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **ClickUp hatékonyságnövelő platform** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-clickup-productivity-platform-sso"></a>A ClickUp hatékonyságnövelő platform egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a ClickUp hatékonyságnövelő platform-bérlőre rendszergazdaként.

2. Kattintson a **felhasználói profilra**, majd válassza a **Beállítások** lehetőséget.

    ![Képernyőfelvétel: a ClickUp termelékenységi bérlője a beállítások ikon kiválasztásával jelenik meg.](./media/clickup-productivity-platform-tutorial/configure-0.png)

    ![A képernyőfelvételen a beállítások láthatók.](./media/clickup-productivity-platform-tutorial/configure-1.png)

3. Válassza a **Microsoft** lehetőséget az egyszeri Sign-On (SSO) szolgáltató alatt.

    ![A képernyőképen a Microsoft által kiválasztott hitelesítési ablaktábla látható.](./media/clickup-productivity-platform-tutorial/configure-2.png)

4. A **Microsoft egyszeri bejelentkezés konfigurálása** lapon hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: a Microsoft egyszeri bejelentkezési oldalának konfigurálása, amelyen átmásolhatja a D entitást, és mentheti az Azure összevonási metaadatokat. R L.](./media/clickup-productivity-platform-tutorial/configure-3.png)

    a. Kattintson a **Másolás** elemre az entitás-azonosító értékének másolásához, majd illessze be a Azure Portal **ALAPszintű SAML-konfiguráció** szakaszában lévő **azonosító (Entity ID)** szövegmezőbe.

    b. Az **Azure összevonási metaadatok URL-címe** szövegmezőbe illessze be az alkalmazás-összevonási metaadatok URL-címét, amelyet a Azure Portal másolt, majd kattintson a **Mentés** gombra.

5. A telepítés befejezéséhez kattintson a **hitelesítés a Microsofttal lehetőségre a telepítés befejezéséhez** és a Microsoft-fiókkal való hitelesítéshez.

    ![Képernyőfelvétel: a hitelesítés a Microsofttal a telepítés befejezéséhez gomb.](./media/clickup-productivity-platform-tutorial/configure-4.png)


### <a name="create-clickup-productivity-platform-test-user"></a>ClickUp hatékonyságnövelő platform tesztelési felhasználó létrehozása

1. Egy másik böngészőablakban jelentkezzen be a ClickUp hatékonyságnövelő platform-bérlőre rendszergazdaként.

2. Kattintson a **felhasználói profilra**, majd válassza a **személyek** lehetőséget.

    ![Képernyőfelvétel: a ClickUp hatékonyságnövelő bérlője.](./media/clickup-productivity-platform-tutorial/configure-0.png)

    ![Képernyőfelvétel: a személyek hivatkozás kiválasztva.](./media/clickup-productivity-platform-tutorial/user-1.png)

3. Adja meg a felhasználó e-mail-címét a szövegmezőben, majd kattintson a **meghívás** elemre.

    ![Képernyőfelvétel: a csoport felhasználói beállításai, ahol e-mailben meghívhatja a személyeket.](./media/clickup-productivity-platform-tutorial/user-2.png)

    > [!NOTE]
    > A felhasználó megkapja az értesítést, és el kell fogadnia a meghívót a fiók aktiválásához.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ez átirányítja a ClickUp hatékonyságnövelő platform bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen a ClickUp termelékenységi platform bejelentkezési URL-címére közvetlenül, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Ha a saját alkalmazások ClickUp hatékonyságnövelő platform csempére kattint, a rendszer átirányítja a ClickUp hatékonyságnövelő platform bejelentkezési URL-címére. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)használatába.

## <a name="next-steps"></a>Következő lépések

A ClickUp hatékonyságnövelő platform konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).