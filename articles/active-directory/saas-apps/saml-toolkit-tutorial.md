---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az Azure AD SAML Toolkit használatával | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az Azure AD SAML Toolkit között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 26618382223a87f779f95452000a39126f37efbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92675431"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-azure-ad-saml-toolkit"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az Azure AD SAML Toolkit használatával

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az Azure AD SAML-eszközkészletet Azure Active Directory (Azure AD) használatával. Ha az Azure AD SAML-eszközkészletet az Azure AD-vel integrálja, a következőket teheti:

* Olyan Azure AD-beli vezérlés, amely hozzáfér az Azure AD SAML Toolkit-hez.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az Azure AD SAML-eszközkészletbe az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Azure AD SAML-eszközkészlet – egyszeri bejelentkezés (SSO) – engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az Azure AD SAML Toolkit támogatja az **SP** által kezdeményezett egyszeri bejelentkezést

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="adding-azure-ad-saml-toolkit-from-the-gallery"></a>Az Azure AD SAML Toolkit hozzáadása a katalógusból

Az Azure ad SAML Toolkit Azure AD-integrációjának konfigurálásához hozzá kell adnia az Azure AD SAML-eszközkészletet a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a keresőmezőbe az **Azure ad SAML Toolkit** kifejezést.
1. Válassza az **Azure ad SAML Toolkit** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-azure-ad-saml-toolkit"></a>Azure AD SSO konfigurálása és tesztelése az Azure AD SAML-eszközkészlethez

Konfigurálja és tesztelje az Azure AD SSO-t az Azure AD SAML Toolkit segítségével egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure ad-felhasználó és a kapcsolódó felhasználó között az Azure AD SAML Toolkit-ben.

Az Azure AD SSO Azure AD SAML Toolkit használatával történő konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. Az **[Azure ad SAML TOOLKIT SSO konfigurálása](#configure-azure-ad-saml-toolkit-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre egy Azure ad SAML Toolkit-tesztet](#create-azure-ad-saml-toolkit-test-user)** , hogy az Azure ad SAML-eszközkészletben található, a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-partnert.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. Az Azure Portal az **Azure ad SAML Toolkit** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** lapon adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet: `https://samltoolkit.azurewebsites.net/`

    b. A **Válasz URL-cím** szövegmezőbe írja be az URL-címet: `https://samltoolkit.azurewebsites.net/SAML/Consume`

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (RAW)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificateraw.png)

1. Az **Azure ad SAML-eszközkészlet beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban az Azure AD SAML Toolkit elérésének biztosításával engedélyezi a B. Simon számára az Azure egyszeri bejelentkezés használatát.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **Azure ad SAML Toolkit** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-azure-ad-saml-toolkit-sso"></a>Az Azure AD SAML Toolkit egyszeri bejelentkezésének konfigurálása

1. Nyisson meg egy új böngészőablakot, ha még nem regisztrált az Azure AD SAML-eszközkészlet webhelyén, először regisztráljon a **regisztrálás** gombra kattintva. Ha már regisztrált, jelentkezzen be az Azure AD SAML Toolkit vállalati webhelyre a regisztrált bejelentkezési hitelesítő adatok használatával.

    ![Azure AD SAML Toolkit-regisztráció](./media/saml-toolkit-tutorial/register.png)

1. Kattintson az **SAML-konfigurációra**.

    ![Azure AD SAML Toolkit SAML-konfiguráció](./media/saml-toolkit-tutorial/saml-configure.png)

1. Kattintson a **Létrehozás** lehetőségre.

    ![Azure AD SAML Toolkit](./media/saml-toolkit-tutorial/createsso.png)

1. Az **SAML SSO-konfiguráció** lapon hajtsa végre a következő lépéseket:

    ![Azure AD SAML-eszközkészlet – SSO-konfiguráció létrehozása](./media/saml-toolkit-tutorial/fill-details.png)

    1. A **bejelentkezési URL** szövegmezőbe illessze be a **bejelentkezési URL-címet** , amelyet a Azure Portal másolt.

    1. Az **Azure ad-azonosító** szövegmezőbe illessze be az **Azure ad-azonosító** értékét, amelyet a Azure Portal másolt.

    1. A **kijelentkezési URL** szövegmezőbe illessze be a **kijelentkezési URL-** értéket, amelyet a Azure Portal másolt.

    1. Kattintson a **fájl kiválasztása** elemre, és töltse fel a Azure Portalból letöltött **tanúsítvány-(RAW-)** fájlt.

    1. Kattintson a **Létrehozás** lehetőségre.

    1. Másolja be a bejelentkezési URL-címet, az azonosítót és az ACS URL-értékeket az SAML Toolkit SSO konfigurációs lapján, és illessze be a beillesztett szövegmezőket a Azure Portal **alapszintű SAML-konfiguráció szakaszában** .

### <a name="create-azure-ad-saml-toolkit-test-user"></a>Azure AD SAML Toolkit-teszt felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre az Azure AD SAML Toolkit-ben. Hozzon létre egy teszt felhasználót az eszközön egy új felhasználó regisztrálásával és az összes felhasználói adat megadásával. 

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

1. Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ekkor a rendszer átirányítja az SAML Toolkit bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

2. Lépjen közvetlenül az SAML Toolkit bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

3. Használhatja a Microsoft Access panelt. Amikor a hozzáférési panelen a SAML-eszközkészlet csempére kattint, automatikusan be kell jelentkeznie az SAML-eszközkészletbe, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Következő lépések

Az Azure AD SAML Toolkit konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)