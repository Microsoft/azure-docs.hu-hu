---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Meraki-irányítópulttal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Meraki irányítópult között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 74009c7e7f2ad28655c9c5322a063a17da96e0c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99493922"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-meraki-dashboard"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Meraki-irányítópulttal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Meraki-irányítópultot Azure Active Directory (Azure AD) használatával. Ha az Meraki-irányítópultot az Azure AD-vel integrálja, a következőket teheti:

* A Meraki-irányítópulthoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az irányítópultra az Azure AD-fiókjával való Meraki.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Meraki-irányítópult egyszeri bejelentkezéses (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Meraki-irányítópult támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="adding-meraki-dashboard-from-the-gallery"></a>Meraki-irányítópult hozzáadása a gyűjteményből

A Meraki-irányítópult Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Meraki-irányítópultot a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Meraki Dashboard** kifejezést a keresőmezőbe.
1. Válassza az **Meraki-irányítópult** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-meraki-dashboard"></a>Az Azure AD SSO konfigurálása és tesztelése a Meraki irányítópultján

Konfigurálja és tesztelje az Azure AD SSO-t a Meraki-irányítópulttal egy **B. Simon** nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Meraki-irányítópulton.

Az Azure AD SSO és a Meraki irányítópultjának konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Meraki-irányítópult egyszeri bejelentkezésének konfigurálása](#configure-meraki-dashboard-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre egy Meraki-irányítópultot](#create-meraki-dashboard-test-user)** , amely a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon Meraki-irányítópulton található.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **Meraki-irányítópult** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:
     
    A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://n27.meraki.com/saml/login/m9ZEgb/< UNIQUE ID >`

    > [!NOTE]
    > A válasz URL-cím értéke nem valódi. Frissítse ezt az értéket a tényleges válasz URL-címének értékével, amelyet az oktatóanyag későbbi részében ismertet.

1. Kattintson a **Mentés** gombra.

1. A Meraki irányítópult-alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül a Meraki irányítópult-alkalmazás néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.
    
    | Name | Forrás attribútum|
    | ---------------| --------- |
    | `https://dashboard.meraki.com/saml/attributes/username` | User. userPrincipalName |
    | `https://dashboard.meraki.com/saml/attributes/role` | User. assignedroles |

    > [!NOTE]
    > A szerepkörök Azure AD-ben való konfigurálásának megismeréséhez lásd [itt](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview).

1. Az **SAML aláíró tanúsítvány** szakaszban kattintson a **Szerkesztés** gombra az **SAML aláíró tanúsítvány** párbeszédpanel megnyitásához.

    ![SAML aláíró tanúsítvány szerkesztése](common/edit-certificate.png)

1. Az **SAML aláíró tanúsítvány** szakaszban másolja az **ujjlenyomat értékét** , és mentse a számítógépre. Ezt az értéket át kell alakítani, hogy tartalmazza a kettőspontokat, hogy a Meraki-irányítópult megértse azt. Ha például az Azure-ból származó ujjlenyomatot kívánja használni, akkor `C2569F50A4AAEDBB8E` később a `C2:56:9F:50:A4:AA:ED:BB:8E` Meraki-irányítópulton kell megváltoztatnia.

    ![Ujjlenyomat értékének másolása](common/copy-thumbprint.png)

1. A **Meraki-irányítópult beállítása** szakaszban másolja ki a kijelentkezési URL-címet, és mentse a számítógépre.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Meraki-irányítópulthoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Meraki-irányítópult** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.

    ![felhasználói szerepkör](./media/meraki-dashboard-tutorial/user-role.png)

    > [!NOTE]
    > **Válassza ki a szerepkör** lehetőséget, és a kiválasztott felhasználó alapértelmezett szerepköre a felhasználó lesz.

1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-meraki-dashboard-sso"></a>Meraki-irányítópult egyszeri bejelentkezésének konfigurálása

1. Az Meraki-irányítópulton belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **Meraki-irányítópult beállítása** elemre, majd a Meraki irányítópult alkalmazásra irányítja. Itt adja meg a rendszergazdai hitelesítő adatokat a Meraki-irányítópultra való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-7-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a Meraki-irányítópultot, egy másik böngészőablakban jelentkezzen be a Meraki irányítópult vállalati webhelyére rendszergazdaként.

1. Navigáljon a **szervezeti**  ->  **Beállítások** elemre.

    ![Meraki irányítópult-beállítások lap](./media/meraki-dashboard-tutorial/configure-1.png)

1. A hitelesítés területen módosítsa az **SAML SSO** -t az **SAML SSO**-ra engedélyezve.

    ![Meraki-irányítópult hitelesítése](./media/meraki-dashboard-tutorial/configure-2.png)

1. Kattintson **a SAML-identitásszolgáltató hozzáadása** lehetőségre.

    ![Meraki-irányítópult – SAML-identitásszolgáltató hozzáadása](./media/meraki-dashboard-tutorial/configure-3.png)

1. Illessze be a konvertált **ujjlenyomat** értékét, amelyet a Azure Portal másolt át, és konvertált a megadott formátumban, ahogy azt a korábbi szakasz 9. lépésében említettük, az **X. 590 CERT SHA1 ujjlenyomat** szövegmezőbe. Ezután kattintson a **Mentés** gombra. A mentés után megjelenik a fogyasztói URL-cím. Másolja a fogyasztói URL-címet, és illessze be a **Válasz URL-** szövegmezőbe a Azure Portal **ALAPszintű SAML-konfiguráció szakaszában** .

    ![Meraki-irányítópult konfigurálása](./media/meraki-dashboard-tutorial/configure-4.png)

### <a name="create-meraki-dashboard-test-user"></a>Meraki-irányítópult tesztelési felhasználójának létrehozása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a Meraki-irányítópultra.

1. Navigáljon a **szervezet**  ->  **rendszergazdái** számára.

    ![Meraki-irányítópult rendszergazdái](./media/meraki-dashboard-tutorial/user-1.png)

1. Az SAML rendszergazdai szerepkörök szakaszban kattintson az SAML- **szerepkör hozzáadása** gombra.

    ![Meraki-irányítópult – SAML-szerepkör hozzáadása gomb](./media/meraki-dashboard-tutorial/user-2.png)

1. Adja meg a szerepkört **meraki_full_admin**, a **szervezeti hozzáférés** megjelölése **teljesként** , majd kattintson a **szerepkör létrehozása** lehetőségre. Ismételje meg a folyamatot **meraki_readonly_admin**, ez az idő **csak olvasható** mezőként való megjelölése a **szervezet** számára.
 
    ![Meraki-irányítópult – felhasználó létrehozása](./media/meraki-dashboard-tutorial/user-3.png)

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját.

* Kattintson az alkalmazás tesztelése Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a Meraki-irányítópultra, amelyhez be szeretné állítani az egyszeri bejelentkezést

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások Meraki irányítópult csempére kattint, automatikusan be kell jelentkeznie arra a Meraki-irányítópultra, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


## <a name="next-steps"></a>Következő lépések

A Meraki irányítópult konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).
