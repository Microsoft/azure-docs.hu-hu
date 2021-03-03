---
title: 'Oktatóanyag: Azure Active Directory integráció a tábla megismeréséhez – Shibboleth | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a tábla – Learn-Shibboleth között.
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
ms.openlocfilehash: b1b3f265d0e1fcad2953292c5227c2630c6df229
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101649898"
---
# <a name="tutorial-azure-active-directory-integration-with-blackboard-learn---shibboleth"></a>Oktatóanyag: Azure Active Directory integráció a tábla megismeréséhez – Shibboleth

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Shibboleth a Azure Active Directory (Azure AD) szolgáltatással. Ha az Azure AD-vel integrálja a Shibboleth-et, a következőket teheti:

* Vezérlés az Azure AD-ben, akik hozzáférnek a tábla Learn-Shibboleth-hez.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az Shibboleth az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:
 
* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Egy tábla – Shibboleth egyszeri bejelentkezésre (SSO) alkalmas előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* Táblás Learn-Shibboleth az **SP** által kezdeményezett SSO-t támogatja

## <a name="add-blackboard-learn---shibboleth-from-the-gallery"></a>Tábla Learn-Shibboleth hozzáadása a katalógusból

A tábla Learn-Shibboleth az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Shibboleth a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **tábla Learn-Shibboleth** kifejezést a keresőmezőbe.
1. Válassza a **tábla Learn-Shibboleth** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-blackboard-learn---shibboleth"></a>Azure AD SSO konfigurálása és tesztelése a tábla megismeréséhez – Shibboleth

Konfigurálja és tesztelje az Azure AD SSO-t a tábla Learn-Shibboleth használatával egy **B. Simon** nevű teszt felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a következő táblán: Learn-Shibboleth.

Az Azure AD SSO konfigurálásához és teszteléséhez a (z) Learn-Shibboleth használatával végezze el a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[tábla megismerése – Shibboleth egyszeri bejelentkezés](#configure-blackboard-learn---shibboleth-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Create tábla Learn-Shibboleth test User](#create-blackboard-learn---shibboleth-test-user)** -to have a B. Simon in tábla Learn-Shibboleth, amely a felhasználó Azure ad-képviseletéhez van csatolva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Ha az Azure AD egyszeri bejelentkezést a tábla Learn-Shibboleth használatával szeretné konfigurálni, hajtsa végre a következő lépéseket:

1. Az Azure Portal a **tábla Learn-Shibboleth** Application Integration oldalon válassza az **egyszeri bejelentkezés** lehetőséget.

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

3. Az **egyszeres Sign-On beállítása az SAML-vel** lapon kattintson a ceruza ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<yourblackoardlearnserver>.blackboardlearn.com/Shibboleth.sso/Login`

    b. Az **azonosító** mezőbe írjon be egy URL-címet a következő minta használatával: `https://<yourblackoardlearnserver>.blackboardlearn.com/shibboleth-sp`

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<yourblackoardlearnserver>.blackboardlearn.com/Shibboleth.sso/SAML2/POST`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Sign-On URL-címmel, azonosítóval és válasz URL-címmel. Az értékek megszerzéséhez forduljon a [táblához a Learn-Shibboleth ügyfél-támogatási csapatához](https://www.blackboard.com/forms/contact-us_form.aspx) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **tábla megismerése – Shibboleth szakaszban adja** meg a megfelelő URL-címeket (ek) a követelménynek megfelelően.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a táblához: Learn-Shibboleth.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **tábla Learn-Shibboleth** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="configure-blackboard-learn---shibboleth-sso"></a>A tábla megismerése – Shibboleth SSO

Ha be szeretné állítani az egyszeri bejelentkezést a **tábla Shibboleth** oldalán, el kell küldenie a letöltött **összevonás-metaadatokat tartalmazó XML-fájlt** és a megfelelő másolt URL-címeket Azure Portalról a [tábla Shibboleth támogatási csapatához](https://www.blackboard.com/forms/contact-us_form.aspx). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-blackboard-learn---shibboleth-test-user"></a>Create tábla Learn-Shibboleth test User

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a tábla Learn-Shibboleth. Működjön együtt a [tábla Learn-Shibboleth támogatási csapatával](https://www.blackboard.com/forms/contact-us_form.aspx) , és vegye fel a felhasználókat a Shibboleth platformba. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a Shibboleth bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen a tábla Learn-Shibboleth bejelentkezési URL-címére, és indítsa el innen a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Ha a saját alkalmazások Shibboleth csempére kattint, automatikusan be kell jelentkeznie a Shibboleth, amelyhez be kell állítania az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

A tábla megismerése – Shibboleth a munkamenet-vezérlés kikényszeríthető, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).