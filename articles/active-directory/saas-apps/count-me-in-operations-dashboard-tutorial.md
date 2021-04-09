---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Count me in-Operations irányítópulton | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és számíthat a működés közbeni irányítópultra.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/30/2020
ms.author: jeedes
ms.openlocfilehash: 3339516193af6e1ff832ac586f4a81f8799c5b83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98727677"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-count-me-in---operations-dashboard"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Count me in-Operations irányítópulton

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a me in-Operations irányítópultot a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Azure AD-ben a működés közbeni irányítópultot, a következőket teheti:

* Az Azure AD-ben a működés közbeni irányítópult megszámlálásához hozzáférő vezérlőelem.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek nekem a működés közbeni irányítópultok Azure AD-fiókjával való megszámlálásához.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Számlálás a működés közbeni irányítópulton egyszeri bejelentkezésre (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Számlálás a működés közbeni irányítópult támogatja az **SP** által kezdeményezett SSO-t

## <a name="adding-count-me-in---operations-dashboard-from-the-gallery"></a>A katalógusban szereplő i-Operations irányítópult hozzáadása

Ha konfigurálni szeretné a Count in-Operations irányítópult az Azure AD-ba való integrálását, akkor a katalógusban szereplő, a felügyelt SaaS-alkalmazások listájához is meg kell adnia a Count i in-Operations irányítópultot.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a következőt: **Count me in-Operations Dashboard** a keresőmezőbe.
1. Válassza a **számlálás az operatív irányítópulton** az eredmények panelen lehetőséget, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-count-me-in---operations-dashboard"></a>Az Azure AD SSO konfigurálása és tesztelése a működés közbeni irányítópultok számlálásához

Konfigurálja és tesztelje az Azure AD SSO-t a Count i in-Operations irányítópulton egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között, a me in-Operations irányítópulton.

Az Azure AD SSO konfigurálásához és teszteléséhez a Count me in-Operations irányítópulton hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[Count Me In-Operations az irányítópult egyszeri bejelentkezésének konfigurálása](#configure-count-me-in-operations-dashboard-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre egy számlálást In-Operations irányítópult-tesztelési felhasználó](#create-count-me-in-operations-dashboard-test-user)** –, hogy a B. Simon a Count me in-Operations irányítópult, amely a felhasználó Azure ad-képviseletéhez kapcsolódik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portalban a **me in-Operations Dashboard** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://api-us.localz.io/user/v1/saml/initsso?projectId=<PROJECT_ID>`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `api-us.localz.io/<PROJECT_ID>`

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://api-us.localz.io/user/v1/saml/initsso?projectId=<PROJECT_ID>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Vegye fel [a kapcsolatot az Operations Dashboard ügyfél-támogatási csapatával](mailto:support@localz.co) az értékek lekéréséhez. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Számlálás a működés közbeni irányítópult alkalmazásban az SAML-kikötések egy adott formátumban várnak, amelyhez egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül számíthat a működés közbeni irányítópult alkalmazásra, amely néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.
    
    | Name |  Forrás attribútum|
    | ----------- | --------- |
    | hozzárendelt szerepkörök | User. assignedroles |

    > [!NOTE]
    > Számlálás a működés közbeni irányítópulton az alkalmazáshoz hozzárendelt felhasználók szerepköreinek számítanak. Állítsa be ezeket a szerepköröket az Azure AD-ben, hogy a felhasználók hozzá lehessen rendelni a megfelelő szerepköröket. A szerepkörök Azure AD-ben való konfigurálásának megismeréséhez lásd [itt](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview).

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **készleten belüli számlálás a műveletekhez irányítópulton** szakaszban másolja ki a megfelelő URL-címet (ka) t a követelmény alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a működés közbeni irányítópult megszámlálásához.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **számlálás a működés közben irányítópultot** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-count-me-in-operations-dashboard-sso"></a>A Count Me In-Operations az irányítópult egyszeri bejelentkezésének konfigurálása

Ha be szeretné állítani az egyszeri bejelentkezést a **Count i-Operations Dashboard** oldalon, el kell küldenie a letöltött **tanúsítványt (Base64)** és a megfelelő másolt url-címeket a Azure Portalról a működés közbeni [irányítópultok támogatási csapatának megszámlálásához](mailto:support@localz.co). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-count-me-in-operations-dashboard-test-user"></a>Számláló létrehozása In-Operations irányítópult-tesztelési felhasználó

Ebben a szakaszban létrehoz egy Britta Simon nevű felhasználót a Count me in-Operations irányítópulton. A Count i in-Operations irányítópult- [támogatási csoport](mailto:support@localz.co) használata a felhasználók számának megszámlálása az operatív irányítópult-platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ez a művelet átirányítja a működés közbeni irányítópult bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Ugrás a következőre: az Operations irányítópult bejelentkezési URL-címe közvetlenül, majd a bejelentkezési folyamat elindítása innen.

* Használhatja a Microsoft saját alkalmazásait. Ha rákattint a saját alkalmazások számlálás a működés közbeni irányítópult csempére, a rendszer átirányítja az Operations Dashboard bejelentkezési URL-címére. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


## <a name="next-steps"></a>Következő lépések

Miután konfigurálta a számlálás a működés közben irányítópultot, kikényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).
