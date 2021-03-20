---
title: 'Oktatóanyag: Azure Active Directory integráció a ArcGIS Online-nal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a ArcGIS Online között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/08/2021
ms.author: jeedes
ms.openlocfilehash: 7a445eefa31e741562105e89fa105d404ccc0c7e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101646350"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Oktatóanyag: Azure Active Directory integráció a ArcGIS Online-nal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a ArcGIS Online-t a Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a ArcGIS Online-t, a következőket teheti:

* Az Azure AD-ben a ArcGIS Online elérésére jogosult vezérlő.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az online ArcGIS az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* ArcGIS Online egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

> [!NOTE]
> Ez az integráció az Azure AD USA kormányzati felhőalapú környezetének használatával is elérhető. Ezt az alkalmazást az Azure AD US government Cloud Application Galleryben találja, és ugyanúgy konfigurálhatja, mint a nyilvános felhőben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A ArcGIS Online támogatja az **SP** által kezdeményezett egyszeri bejelentkezést.

## <a name="add-arcgis-online-from-the-gallery"></a>Online ArcGIS hozzáadása a katalógusból

A ArcGIS Online Azure AD-be való integrálásának konfigurálásához a katalógusból a felügyelt SaaS-alkalmazások listájára kell ArcGIS Online-t hozzáadnia.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **ArcGIS Online** kifejezést a keresőmezőbe.
1. Válassza az **ArcGIS Online** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-arcgis-online"></a>Az Azure AD SSO konfigurálása és tesztelése online ArcGIS

Konfigurálja és tesztelje az Azure AD SSO-t a ArcGIS Online-ban egy **B. Simon** nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a ArcGIS Online-ban.

Az Azure AD SSO és a ArcGIS Online konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[ArcGIS Online egyszeri bejelentkezés konfigurálása](#configure-arcgis-online-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[ArcGIS-alapú online tesztelési felhasználó létrehozása](#create-arcgis-online-test-user)** – ha a felhasználó Azure ad-ArcGIS kapcsolódik, a "B
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **ArcGIS Online** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<companyname>.maps.arcgis.com`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `<companyname>.maps.arcgis.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Az értékek megszerzéséhez forduljon a [ArcGIS Online ügyfélszolgálati csapatához](https://support.esri.com/en/) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **ArcGIS Online-on** belüli konfiguráció automatizálásához a **bővítmény telepítése** lehetőségre kattintva telepítenie kell **az alkalmazások biztonságos bejelentkezési böngésző bővítményét** .

    ![image](./media/arcgis-tutorial/install-extension.png)

7. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **telepítő ArcGIS Online** lehetőségre a ArcGIS Online-alkalmazáshoz. Itt adja meg a rendszergazdai hitelesítő adatokat a ArcGIS Online-ba való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja a **ArcGIS Online egyszeri bejelentkezés konfigurálása** szakasz lépéseit.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a ArcGIS Online-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **ArcGIS Online** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-arcgis-online-sso"></a>ArcGIS Online SSO konfigurálása

1. Ha manuálisan szeretné beállítani a ArcGIS Online-t, nyisson meg egy új böngészőablakot, és jelentkezzen be a ArcGIS vállalati webhelyére rendszergazdaként, és hajtsa végre a következő lépéseket:

2. Kattintson a **beállítások szerkesztése** elemre.

    ![Beállítások szerkesztése](./media/arcgis-tutorial/settings.png "Beállítások szerkesztése")

3. Kattintson a **Biztonság** elemre.

    ![Biztonság](./media/arcgis-tutorial/secure.png "Biztonság")

4. A **vállalati bejelentkezések** területen kattintson az **Identity Provider beállítása** elemre.

    ![Vállalati bejelentkezések](./media/arcgis-tutorial/enterprise.png "Vállalati bejelentkezések")

5. Az **identitás-szolgáltató** konfigurálása lapon hajtsa végre a következő lépéseket:

    ![Identitás-szolgáltató beállítása](./media/arcgis-tutorial/identity-provider.png "Identitás-szolgáltató beállítása")

    a. A **név** szövegmezőbe írja be a szervezet nevét.

    b. **A vállalati identitás-szolgáltató metaadatait a használatával fogja megadni**. Válasszon ki **egy fájlt**.

    c. A letöltött metaadat-fájl feltöltéséhez kattintson a **fájl kiválasztása** lehetőségre.

    d. Kattintson a **személyazonosság-szolgáltató beállítása** elemre.

### <a name="create-arcgis-online-test-user"></a>ArcGIS Online tesztelési felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek a ArcGIS Online-ba, a ArcGIS Online-ban kell kiépíteni őket.  
ArcGIS Online esetén a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **ArcGIS** -bérlőbe.

2. Kattintson a **tagok meghívása** elemre.

    ![Tagok meghívása](./media/arcgis-tutorial/invite.png "Tagok meghívása")

3. Válassza **a tagok hozzáadása automatikusan e-mail küldése nélkül** lehetőséget, majd kattintson a **tovább** gombra.

    ![Tagok automatikus hozzáadása](./media/arcgis-tutorial/members.png "Tagok automatikus hozzáadása")

4. A **tagok** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Hozzáadás és felülvizsgálat](./media/arcgis-tutorial/review.png "Hozzáadás és felülvizsgálat")

     a. Adja meg a kiépíteni kívánt érvényes Azure AD **-fiók e-mail-címét**, **utónevét** és **vezetéknevét** .

     b. Kattintson **a Hozzáadás és áttekintés** gombra.
5. Tekintse át a beírt adataikat, majd kattintson a **Tagok hozzáadása** lehetőségre.

    ![Tag hozzáadása](./media/arcgis-tutorial/add.png "Tag hozzáadása")

    > [!NOTE]
    > A Azure Active Directory fiók tulajdonosa kap egy e-mailt, és egy hivatkozást követve erősítse meg a fiókját, mielőtt az aktívvá válna.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a ArcGIS Online bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül a ArcGIS Online bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások ArcGIS Online csempére kattint, a rendszer átirányítja a ArcGIS Online bejelentkezési URL-címére. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

A ArcGIS Online konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).