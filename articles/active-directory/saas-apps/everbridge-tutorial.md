---
title: 'Oktatóanyag: Azure Active Directory integráció a Everbridge-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Everbridge között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 6e281931eb4646e09bb9aa3226ed7d0735c84e3f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101643779"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Oktatóanyag: Azure Active Directory integráció a Everbridge

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Everbridge a Azure Active Directory (Azure AD) szolgáltatással.
Ha integrálja az Everbridge-t az Azure AD-vel, a következőket teheti:

* A Everbridge-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezzenek a Everbridge az Azure AD-fiókjával. A hozzáférés-vezérlés neve egyszeri bejelentkezés (SSO).
* A fiókokat egy központi helyen kezelheti a Azure Portal használatával.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Everbridge való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Egyszeri bejelentkezést használó Everbridge-előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Everbridge támogatja a IDENTITÁSSZOLGÁLTATÓ által kezdeményezett egyszeri bejelentkezést.

## <a name="add-everbridge-from-the-gallery"></a>Everbridge hozzáadása a gyűjteményből

A Everbridge Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Everbridge a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Everbridge** kifejezést a keresőmezőbe.
1. Válassza ki a **Everbridge** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-everbridge"></a>Azure AD SSO konfigurálása és tesztelése a Everbridge-hez

Konfigurálja és tesztelje az Azure AD SSO-t a Everbridge a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Everbridge-ben.

Az Azure AD SSO és a Everbridge konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[EVERBRIDGE SSO konfigurálása](#configure-everbridge-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre Everbridge-teszt felhasználót](#create-everbridge-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-Everbridge rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **Everbridge** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeres Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** ceruza ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

    >[!NOTE]
    >Konfigurálja az alkalmazást Manager-portálként *vagy* a Azure Portal és a Everbridge portálon a tag portálként.

4. A **Everbridge** alkalmazás **Everbridge Manager-portálként** való konfigurálásához az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Everbridge tartomány és URL-címek egyszeri bejelentkezési adatai](common/idp-intiated.png)

    a. Az **azonosító** mezőben adja meg a mintát követő URL-címet.
    `https://sso.everbridge.net/<API_Name>`

    b. A **Válasz URL-címe** mezőbe írja be a mintát követő URL-címet.
    `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és a válasz URL-értékekkel. Az értékek beszerzéséhez forduljon a [Everbridge támogatási csapatához](mailto:support@everbridge.com). A Azure Portal az **alapszintű SAML-konfiguráció** szakaszának mintázatait is megtekintheti.

5. Ha a **Everbridge** alkalmazást a Everbridge- **tag portálként** szeretné konfigurálni, az **alapszintű SAML-konfiguráció** szakaszban kövesse az alábbi lépéseket:

  * Ha az alkalmazást IDENTITÁSSZOLGÁLTATÓ módban szeretné konfigurálni, kövesse az alábbi lépéseket:

     ![Everbridge tartomány-és URL-címekre vonatkozó egyszeri bejelentkezési adatok IDENTITÁSSZOLGÁLTATÓ által kezdeményezett módban](common/idp-intiated.png)

    a. Az **azonosító** mezőben adja meg a mintát követő URL-címet `https://sso.everbridge.net/<API_Name>/<Organization_ID>`

    b. A **Válasz URL-címe** mezőbe írja be a mintát követő URL-címet `https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

   * Ha az alkalmazást SP-kezdeményezésű módban szeretné konfigurálni, válassza a **további URL-címek beállítása** lehetőséget, és kövesse ezt a lépést:

     ![Everbridge tartomány és URL-címek egyszeri bejelentkezési információi az SP által kezdeményezett módban](common/both-signonurl.png)

     a. A **bejelentkezési URL-cím** mezőbe írja be a mintát követő URL-címet `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

     > [!NOTE]
     > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével, és jelentkezzen be az URL-címek értékeire. Az értékek beszerzéséhez forduljon a [Everbridge támogatási csapatához](mailto:support@everbridge.com). A Azure Portal az **alapszintű SAML-konfiguráció** szakaszának mintázatait is megtekintheti.

6. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban válassza a **Letöltés** lehetőséget az **összevonási metaadatok XML-fájljának** letöltéséhez. Mentse a számítógépére.

    ![Tanúsítvány letöltési hivatkozása](common/metadataxml.png)

7. A **Everbridge beállítása** szakaszban másolja a követelményekhez szükséges URL-címeket:

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Everbridge.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Everbridge** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="configure-everbridge-sso"></a>Everbridge SSO konfigurálása

Ha az egyszeri bejelentkezést a **Everbridge** **Everbridge Manager portál** alkalmazásként szeretné konfigurálni, kövesse az alábbi lépéseket.
 
1. Egy másik böngészőablakban jelentkezzen be a Everbridge rendszergazdaként.

1. A felső menüben válassza a **Beállítások** lapot. A **Biztonság** területen válassza az **egyszeri bejelentkezés** lehetőséget.
   
     ![Egyszeri bejelentkezés konfigurálása](./media/everbridge-tutorial/sso.png)
   
     a. A **név** mezőbe írja be az azonosító szolgáltató nevét. Ilyen például a vállalat neve.
   
     b. Az **API neve** mezőbe írja be az API nevét.
   
     c. Válassza a **fájl kiválasztása** lehetőséget a Azure Portal letöltött metaadat-fájl feltöltéséhez.
   
     d. Az **SAML-identitás helyéhez** válassza az **identitás elemet a tulajdonos utasítás NameIdentifier elemében**.
   
     e. Az **Identity Provider bejelentkezési URL-címe** mezőbe illessze be a Azure Portalból másolt **bejelentkezési URL-** értéket.
   
     f. A **szolgáltató által kezdeményezett kérelem kötése** beállításnál válassza a **http-átirányítás** lehetőséget.

     : Kattintson a **Mentés** gombra.

### <a name="configure-everbridge-as-everbridge-member-portal-sso"></a>Everbridge konfigurálása Everbridge-tag portál SSO-ként

Ha az egyszeri bejelentkezést **Everbridge** **Everbridge** szeretné konfigurálni, küldje el a letöltött **összevonási metaadatok XML-fájlját** a [Everbridge-támogató csapatnak](mailto:support@everbridge.com). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-everbridge-test-user"></a>Everbridge-tesztelési felhasználó létrehozása

Ebben a szakaszban a Britta Simon Everbridge-ben hozza létre a test user. Ha felhasználókat szeretne felvenni a Everbridge-platformba, működjön együtt a [Everbridge támogatási csapatával](mailto:support@everbridge.com). Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat a Everbridge-ben. 

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját.

* Kattintson az alkalmazás tesztelése Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a Everbridge, amelyhez be szeretné állítani az egyszeri bejelentkezést.

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások Everbridge csempére kattint, automatikusan be kell jelentkeznie arra a Everbridge, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

A Everbridge konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).