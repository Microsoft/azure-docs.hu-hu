---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az SAP Cloud for Customer szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az SAP-felhő között az ügyfelek számára.
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
ms.openlocfilehash: 60381c7d8c452277b53e1af67ae7fc85349521c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735674"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-for-customer"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az SAP Cloud-nal az ügyfelek számára

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az SAP Cloud ügyfelet Azure Active Directory (Azure AD) használatával. Az SAP Cloud Azure AD-vel való integrálásakor a következőket teheti:

* Az SAP Cloud for Customer szolgáltatáshoz hozzáféréssel rendelkező Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy az Azure AD-fiókjával rendelkező ügyfelek számára automatikusan bejelentkezzenek az SAP-felhőbe.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.


## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* SAP-felhő az ügyfél egyszeri bejelentkezési (SSO) engedélyezett előfizetéséhez.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az SAP Cloud for Customer támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>SAP-felhő hozzáadása az ügyfélhez a katalógusból

Az SAP Cloud az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az SAP Cloud for Customer szolgáltatást a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be az **SAP Cloud for Customer** kifejezést a keresőmezőbe.
1. Válassza az **SAP Cloud az ügyfelek számára** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-sap-cloud-for-customer"></a>Az Azure AD SSO konfigurálása és tesztelése az SAP Cloud ügyfél számára

Konfigurálja és tesztelje az Azure AD SSO-t az SAP Cloud for Customer használatával egy **B. Simon** nevű teszt felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az SAP Cloud for Customer szolgáltatásban.

Az Azure AD SSO és az SAP Cloud az ügyfél számára történő konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. Az **[SAP Cloud beállítása az ügyfél egyszeri](#configure-sap-cloud-for-customer-sso)** bejelentkezéséhez – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
    1. **[SAP-felhő létrehozása az ügyfél-tesztelési felhasználó](#create-sap-cloud-for-customer-test-user)** számára – ha az SAP-felhőben található, a felhasználó Azure ad-képviseletéhez kapcsolódó ügyfélhez tartozó B. Simon partnere.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal az **SAP Cloud for Customer** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<server name>.crm.ondemand.com`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<server name>.crm.ondemand.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Vegye fel a kapcsolatot [az SAP Cloud ügyfélszolgálatával az ügyfelek ügyfélszolgálati csapatával](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) az értékek beszerzéséhez. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az ügyfél-alkalmazáshoz készült SAP-felhő adott formátumban elvárja az SAML-állításokat, így egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a felhasználói attribútumok párbeszédpanel megnyitásához.

    ![A "felhasználó attribútumai" párbeszédpanelt megjelenítő képernyőkép, amelyben a "szerkesztés" ikon van kiválasztva.](common/edit-attribute.png)

1. A felhasználói **attribútumok** **& a jogcímek** párbeszédpanelen hajtsa végre a következő lépéseket:

    a. Kattintson a **Szerkesztés ikonra** a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![Képernyőfelvétel: a "szerkesztés" ikon kiválasztásával megjelenítheti a "felhasználói attribútumok & jogcímek" kifejezést.](./media/sap-customer-cloud-tutorial/tutorial_usermail.png)

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermailedit.png)

    b. Válassza  a transzformáció **forrásként** lehetőséget.

    c. Az **átalakítás** listából válassza a **ExtractMailPrefix ()** elemet.

    d. Az **1. paraméter** listából válassza ki azt a felhasználói attribútumot, amelyet a megvalósításához használni kíván.
    Ha például az Alkalmazottkód-et egyedi felhasználói azonosítóként szeretné használni, és az attribútum értékét a ExtensionAttribute2 tárolja, válassza a User. ExtensionAttribute2 elemet.

    e. Kattintson a **Mentés** gombra.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. Az **SAP-felhő beállítása ügyfél számára** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezést az SAP Cloud for Customer elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **SAP Cloud az ügyfél számára** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-sap-cloud-for-customer-sso"></a>SAP-felhő konfigurálása az ügyfél egyszeri bejelentkezéséhez

1. Nyisson meg egy új böngészőablakot, és jelentkezzen be az SAP-felhőbe az ügyfél vállalati webhelyén rendszergazdaként.

2. A menü bal oldalán kattintson az **Identitáskezelő**  >  **vállalati identitás-szolgáltatók**  >  **Hozzáadás** elemre, majd az előugró ablakban adja meg az identitás-szolgáltató nevét (például az **Azure ad**-t), kattintson a **Mentés** , majd a **SAML 2,0-konfiguráció** elemre.

    ![Képernyőkép, amely megjeleníti a "személyazonosság-szolgáltató hozzáadása" párbeszédpanelt és a kiválasztott "Mentés" gombot.](./media/sap-customer-cloud-tutorial/configure01.png)

3. A **SAML 2,0 konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![A "Tallózás" gomb kiválasztásával megjelenített "S A M L 2,0 konfiguráció".](./media/sap-customer-cloud-tutorial/configure02.png)

    a. Kattintson a **Tallózás** gombra az összevonási metaadatok XML-fájljának feltöltéséhez, amelyet a Azure Portal letöltött.

    b. Az XML-fájl feltöltése után az alábbi értékek automatikusan feltöltve lesznek automatikusan, majd a **Mentés** gombra kattintanak.

### <a name="create-sap-cloud-for-customer-test-user"></a>SAP-felhő létrehozása az ügyfél-tesztelési felhasználó számára

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek az SAP-felhőbe az ügyfelek számára, az SAP-felhőben kell kiépíteni az ügyfelet. Az SAP Cloud for Customer esetében a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be az SAP-felhőbe az ügyfél biztonsági rendszergazdájaként.

2. A menü bal oldalán kattintson a **felhasználók & engedélyek**  >  **felhasználói kezelés** felhasználó  >  **hozzáadása** elemre.

    ![Képernyőkép, amely a "felhasználó hozzáadása" gomb kiválasztásával jeleníti meg a "felhasználói kezelés" lapot.](./media/sap-customer-cloud-tutorial/configure03.png)

3. Az **új felhasználó hozzáadása** szakaszban hajtsa végre a következő lépéseket:

    ![SAP-konfiguráció](./media/sap-customer-cloud-tutorial/configure04.png)

    a. Az **Utónév** szövegmezőbe írja be a felhasználó nevét (például **B**).

    b. A **vezetéknév** szövegmezőbe írja be a felhasználó nevét, például **Simon**.

    c. Az **e-mail** szövege mezőben adja meg a felhasználóhoz hasonló e-mail címet `B.Simon@contoso.com` .

    d. A **bejelentkezési név** szövegmezőbe írja be a (z **) B. Simon** nevű felhasználó nevét.

    e. Adja meg a **felhasználó típusát** a követelménynek megfelelően.

    f. Igény szerint válassza a **fiók aktiválása** lehetőséget.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ez átirányítja az SAP-felhőbe az ügyfél bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen az SAP Cloud az ügyfél bejelentkezési URL-címére közvetlenül, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Ha a My apps szolgáltatásban a felhasználók számára az SAP-felhőre kattint, a rendszer átirányítja az SAP Cloud-ra az ügyfél bejelentkezési URL-címére. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


## <a name="next-steps"></a>Következő lépések

Miután konfigurálta az SAP-felhőt az ügyfél számára, kikényszerítheti a munkamenet-vezérlőket, amelyek valós időben védik a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlőelemek kiterjeszthetők a feltételes hozzáféréstől. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-aad).