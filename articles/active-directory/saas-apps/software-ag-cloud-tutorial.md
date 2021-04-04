---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Software AG Cloud-nal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a Software AG-felhő között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/09/2020
ms.author: jeedes
ms.openlocfilehash: c2a2446da0e1ccf1b3dab88fe898b179291ddaba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98726117"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-software-ag-cloud"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Software AG Cloud-nal

Ebből az oktatóanyagból megtudhatja, hogyan integrálható a Software AG Cloud a Azure Active Directory (Azure AD) szolgáltatással. A Software AG Cloud és az Azure AD integrálásával a következőket teheti:

* A szoftveres AG felhőhöz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Software AG Cloudba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A Software AG felhőalapú egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Software AG Cloud támogatja az **SP** által KEZDEMÉNYEZett SSO-t
* A Software AG Cloud **csak időben támogatja a** felhasználók üzembe helyezését

## <a name="adding-software-ag-cloud-from-the-gallery"></a>A Software AG Cloud hozzáadása a katalógusból

A Software AG Cloud Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Software AG Cloudt a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Software AG Cloud** kifejezést a keresőmezőbe.
1. Válassza ki a **Software AG Cloud** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-software-ag-cloud"></a>Az Azure AD SSO konfigurálása és tesztelése a Software AG Cloud-hoz

Konfigurálja és tesztelje az Azure AD SSO-t a Software AG Cloud segítségével egy **B. Simon** nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a Software AG-felhőben található kapcsolódó felhasználó között.

Az Azure AD SSO és a Software AG felhő közötti konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[Software AG Cloud SSO konfigurálása](#configure-software-ag-cloud-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Szoftveres AG Cloud test-felhasználó létrehozása](#create-software-ag-cloud-test-user)** – a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-beli, a Software AG-felhőben található.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **Software AG Cloud** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    1. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:

        `https://*.softwareag.cloud/auth/realms/TENANT-NAME/broker/IDENTITY-PROVIDER-NAME/endpoint`

    1. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával:

        `https://*.softwareag.cloud/auth/realms/TENANT-NAME`

        > [!NOTE]
        > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Vegye fel a kapcsolatot a [Software AG felhőalapú](mailto:support@softwareag.com) ügyfélszolgálatával az értékek beszerzéséhez. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **szoftveres AG-felhő beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést a Software AG-felhőhöz való hozzáférés megadásával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Software AG Cloud** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-software-ag-cloud-sso"></a>A Software AG Cloud SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a Software AG Cloud webhelyére.

1.  Kattintson a **felügyelet** elemre

    ![A Software AG felhőalapú felügyeletének konfigurálása](./media/software-ag-cloud-tutorial/admin.png)

1. Ugrás az **egyszeri bejelentkezés > identitás-szolgáltató hozzáadása**

    ![A Software AG felhőalapú identitás-szolgáltató konfigurálása](./media/software-ag-cloud-tutorial/add-identity-provider.png)

1. Hajtsa végre a következő lépéseket a következő lapon.

    ![A Software AG Cloud konfigurálása – kövesse a lépéseket](./media/software-ag-cloud-tutorial/saml-1.png)

    a. Az **Identitáskezelő megjelenítendő neve** szövegmezőben adja meg a nevet (például `azure ad` ).

    b. A **Software AG felhőalapú átirányítási URI-szövegmezőben használt Identity Provider egyedi azonosítójában** adjon meg egy egyedi nevet az identitás-szolgáltató számára. A **szoftveres AG felhőalapú átirányítási URI** mezője frissülni fog, és az URI azonosítóval lesz feltöltve. Másolja ezt az URI-t, és használja az **entitás azonosítójának** és egyéb információinak a Azure Portal a definiált minták alapján történő konfigurálásához.

    c. Importálja az **összevonási metaadatok XML-** fájlját az **Identity Provider konfigurációjában** , és kattintson a **tovább** gombra.

    d. Lépjen a **konfiguráció** lapra, és szükség szerint fejezze be a mezőket.

### <a name="create-software-ag-cloud-test-user"></a>Software AG Cloud test-felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a Software AG-felhőben. A Software AG Cloud támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a Software AG-felhőben, a hitelesítés után létrejön egy újat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőséggel tesztelheti az Azure AD egyszeri bejelentkezési konfigurációját. 

Feltételezve, hogy a Microsoft Azure szolgáltatóként van konfigurálva a Software AG-felhőben, navigáljon, `www.softwareag.cloud` és kattintson a Bejelentkezés gombra, és adja meg a környezet nevét. A következő képernyőn kattintson a "bejelentkezés <IDP NAME> " hivatkozásra, és adja meg a hitelesítő adatokat. A hitelesítés után a rendszer bejelentkezik, és átveszi a Software AG Cloud kezdőlapjára.

## <a name="next-steps"></a>Következő lépések

A Software AG Cloud konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).