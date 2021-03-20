---
title: 'Oktatóanyag: Azure Active Directory integráció a UltiPro-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és UltiPro között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: 8ae693d7f6974415eac294289288879d9668604c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98735589"
---
# <a name="tutorial-azure-active-directory-integration-with-ultipro"></a>Oktatóanyag: Azure Active Directory integráció a UltiPro

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a UltiPro a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az UltiPro-t az Azure AD-vel, a következőket teheti:

* A UltiPro-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a UltiPro az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.


## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* UltiPro egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A UltiPro támogatja az **SP** által kezdeményezett egyszeri bejelentkezést.

## <a name="adding-ultipro-from-the-gallery"></a>UltiPro hozzáadása a gyűjteményből

A UltiPro Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a UltiPro a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **UltiPro** kifejezést a keresőmezőbe.
1. Válassza ki a **UltiPro** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-ultipro"></a>Azure AD SSO konfigurálása és tesztelése a UltiPro-hez

Konfigurálja és tesztelje az Azure AD SSO-t a UltiPro a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a UltiPro-ben.

Az Azure AD SSO és a UltiPro konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
2. **[ULTIPRO SSO konfigurálása](#configure-ultipro-sso)** – az egyes Sign-On beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre UltiPro-teszt felhasználót](#create-ultipro-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-UltiPro rendelkezik.
3. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **UltiPro** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeres Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** ceruza ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. A **bejelentkezési URL** szövegmezőbe írja be az URL-címet a következő minták egyikének használatával:

    | Bejelentkezési URL-cím |
    |-------------|
    | `https://<companyname>.ultipro.com/` |
    | `https://<companyname>.ultiproworkplace.com?cpi=AZUREADISSSUERURL` |
    | `https://<companyname>.ultipro.ca` |
    
    b. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minták egyikének használatával:

    | Azonosító |
    |-------------|
    | `https://<companyname>.ultipro.com/adfs/services/trust` |
    | `https://<companyname>.ultiproworkplace.com/adfs/services/trust` |
    | `https://<companyname>.ultipro.ca/adfs/services/trust` |
    
    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minták egyikének használatával:

    | Válasz URL-cím |
    |-------------|
    | `https://<companyname>.ultipro.com/<instancename>` |
    | `https://<companyname>.ultiproworkplace.com/<instancename>` |
    | `https://<companyname>.ultipro.ca/<instancename>` |

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Sign-On URL-címmel, azonosítóval és válasz URL-címmel. Az értékek lekéréséhez forduljon a UltiPro ügyfélszolgálati [csapatához](https://www.ultimatesoftware.com/ContactUs) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

4. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **UltiPro beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a UltiPro.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **UltiPro** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.


## <a name="configure-ultipro-sso"></a>UltiPro SSO konfigurálása

Ha az egyszeri bejelentkezést szeretné konfigurálni a **UltiPro** oldalon, el kell küldenie a letöltött **tanúsítványt (Base64)** és a megfelelő másolt url-címeket a Azure Portalról a [UltiPro támogatási csapatához](https://www.ultimatesoftware.com/ContactUs). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-ultipro-test-user"></a>UltiPro-tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a UltiPro-ben. Együttműködik a [UltiPro támogatási csapatával](https://www.ultimatesoftware.com/ContactUs) , hogy hozzáadja a felhasználókat a UltiPro platformhoz. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a UltiPro bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül a UltiPro bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások UltiPro csempére kattint, a rendszer átirányítja a UltiPro bejelentkezési URL-címére. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


## <a name="next-steps"></a>Következő lépések

A UltiPro konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlőelemek kiterjeszthetők a feltételes hozzáféréstől. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)