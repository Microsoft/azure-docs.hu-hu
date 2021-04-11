---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Saba Cloud-nal | Microsoft Docs'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory és a Saba Cloud között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: jeedes
ms.openlocfilehash: 5ce9eb41755d7faa2ce00b38dfd971313443bfb7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572640"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-saba-cloud"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Saba Cloud-nal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Saba Cloudot a Azure Active Directory (Azure AD) szolgáltatással. A Saba Cloud Azure AD-val való integrálásával a következőket teheti:

* A Saba Cloud-hoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Saba Cloudba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Saba Cloud egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Saba Cloud támogatja **az SP és a identitásszolgáltató** által kezdeményezett SSO-t.
* A Saba **-felhő csak időben támogatja a** felhasználók üzembe helyezését.
* A Saba Cloud Mobile Application mostantól az Azure AD-vel is konfigurálható az egyszeri bejelentkezés engedélyezéséhez. Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

## <a name="adding-saba-cloud-from-the-gallery"></a>Stern-felhő hozzáadása a katalógusból

A Saba Cloud Azure AD-integrációjának konfigurálásához a katalógusból a Managed SaaS-alkalmazások listájához hozzá kell adnia a Saba Cloud-t.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a következőt a keresőmezőbe: **Saba Cloud** .
1. Válassza ki a **Saba Cloud** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-saba-cloud"></a>Azure AD SSO konfigurálása és tesztelése a Saba Cloud-hoz

Konfigurálja és tesztelje az Azure AD SSO-t a Saba Cloud segítségével egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és egy, a Saba-felhőben található kapcsolódó felhasználó között.

A Saba Cloud Azure AD SSO konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[Saba Cloud SSO konfigurálása](#configure-saba-cloud-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Saba Cloud test User](#create-saba-cloud-test-user)** – a felhasználó Azure ad-beli képviseletéhez kapcsolódó B. Simon-beli partneri kapcsolat létrehozásához.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.
1. Ellenőrizze, hogy a **[Saba Cloud (mobil) esetében](#test-sso-for-saba-cloud-mobile)** a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal, a **Saba Cloud** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `<CUSTOMER_NAME>_SPLN_PRINCIPLE`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<SIGN-ON URL>/Saba/saml/SSO/alias/<ENTITY_ID>`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<CUSTOMER_NAME>.sabacloud.com`

    b. A **továbbítási állapot** szövegmezőbe írja be az URL-címet a következő mintával: `IDP_INIT---SAML_SSO_SITE=<SITE_ID> ` vagy ha az SAML a microsite használatára van konfigurálva, írja be az URL-címet a következő minta használatával: `IDP_INIT---SAML_SSO_SITE=<SITE_ID>---SAML_SSO_MICRO_SITE=<MicroSiteId>`

    > [!NOTE]
    > A RelayState konfigurálásával kapcsolatos további információkért tekintse meg [ezt](https://help.sabacloud.com/sabacloud/help-system/topics/help-system-idp-and-sp-initiated-sso-for-a-microsite.html) a hivatkozást.

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével, a bejelentkezési URL-címmel és a továbbítási állapottal. Ezekhez az értékekhez lépjen kapcsolatba [Saba Cloud Client támogatási csapatával](mailto:support@saba.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **ster-felhő beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Saba Cloud-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza ki a **Saba Cloud** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-saba-cloud-sso"></a>Saba Cloud SSO konfigurálása

1. Jelentkezzen be a Saba Cloud vállalati webhelyre rendszergazdaként.
1. Kattintson a **menü** ikonra, majd a **rendszergazda** elemre, majd válassza a **rendszeradminisztrátor** lapot.

    ![képernyőkép a rendszeradminisztrátorról](./media/saba-cloud-tutorial/system.png)

1. A **rendszer konfigurálása** területen válassza az **SAML SSO beállítása** lehetőséget, majd kattintson az **SAML SSO beállítása** gombra. 

    ![képernyőfelvétel a konfigurációról](./media/saba-cloud-tutorial/configure.png)

1. A felugró ablakban válassza a legördülő menüből a **microsite** elemet, majd kattintson a **Hozzáadás és konfigurálás** elemre.

    ![képernyőkép a hely/microsite hozzáadásához](./media/saba-cloud-tutorial/microsite.png)

1. A **identitásszolgáltató konfigurálása** szakaszban kattintson a **Tallózás** gombra az **összevonási metaadatok XML-** fájljának feltöltéséhez, amelyet a Azure Portal letöltött. Engedélyezze a **helyhez tartozó identitásszolgáltató** jelölőnégyzetet, majd kattintson az **Importálás** gombra.

    ![képernyőfelvétel a tanúsítvány importálásáról](./media/saba-cloud-tutorial/certificate.png) 

1. Az **SP konfigurálása** szakaszban másolja ki az **entitás aliasának** értékét, és illessze be ezt az értéket a Azure Portal **alapszintű SAML-konfiguráció** szakaszában lévő **azonosító (Entity ID)** szövegmezőbe. Kattintson a **generált** elemre.

    ![képernyőkép az SP konfigurálásához](./media/saba-cloud-tutorial/generate-metadata.png) 

1. A **tulajdonságok konfigurálása** szakaszban ellenőrizze a feltöltött mezőket, majd kattintson a **Mentés** gombra. 

    ![képernyőfelvétel a tulajdonságok konfigurálásáról](./media/saba-cloud-tutorial/configure-properties.png) 

### <a name="create-saba-cloud-test-user"></a>Saba Cloud test-felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a Saba-felhőben. A Saba-felhő támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha a felhasználó még nem létezik a Saba-felhőben, a hitelesítés után létrejön egy új.

> [!NOTE]
> Ha az SAML-t időben szeretné kiépíteni a Saba Cloud használatával, tekintse meg [ezt](https://help.sabacloud.com/sabacloud/help-system/topics/help-system-user-provisioning-with-saml.html) a dokumentációt.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a Saba Cloud bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.  

* Nyissa meg közvetlenül a Saba Cloud bejelentkezési URL-címét, és onnan indítsa el a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie a Saba-felhőbe, amelyhez be kell állítania az egyszeri bejelentkezést 

A Microsoft My Apps használatával bármilyen módban tesztelheti az alkalmazást. Ha a My apps-ben a Saba Cloud csempére kattint, ha az SP módban van konfigurálva, akkor a bejelentkezési folyamat elindításához és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie a Saba felhőbe, amelyhez be kell állítania az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)használatába.

> [!NOTE]
> Ha a bejelentkezési URL-cím nincs feltöltve az Azure AD-ben, akkor az alkalmazás IDENTITÁSSZOLGÁLTATÓ kezdeményezett módként lesz kezelve, és ha a bejelentkezési URL-cím fel van töltve, akkor az Azure AD mindig átirányítja a felhasználót a Saba Cloud alkalmazásba a szolgáltató által kezdeményezett folyamathoz.

## <a name="test-sso-for-saba-cloud-mobile"></a>Egyszeri bejelentkezéses teszt a Saba Cloud-hoz (mobil)

1. Nyissa meg a Saba Cloud Mobile applicationt, adja meg a **hely nevét** a szövegmezőben, majd kattintson az **ENTER** gombra.

    ![A hely nevének képernyőképe.](./media/saba-cloud-tutorial/site-name.png)

1. Adja meg az **e-mail-címét** , és kattintson a **tovább** gombra.

    ![Képernyőfelvétel az e-mail-címekről.](./media/saba-cloud-tutorial/email-address.png)

1. Végül a sikeres bejelentkezés után megjelenik az alkalmazás lap.

    ![A sikeres bejelentkezés képernyőképe.](./media/saba-cloud-tutorial/dashboard.png)

## <a name="next-steps"></a>Következő lépések

A Saba Cloud beállítása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


