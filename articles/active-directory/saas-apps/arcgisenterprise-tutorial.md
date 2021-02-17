---
title: 'Oktatóanyag: Azure Active Directory integráció a ArcGIS Enterprise szolgáltatással | Microsoft Docs'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory és a ArcGIS Enterprise között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: ef64d857cb2215281b50617e030c634618e14dc4
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100556637"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-enterprise"></a>Oktatóanyag: Azure Active Directory integráció a ArcGIS Enterprise-nal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a ArcGIS Enterprise-t Azure Active Directory (Azure AD-val). Ha integrálja a ArcGIS Enterprise-t az Azure AD-vel, a következőket teheti:

* A ArcGIS Enterprise-hoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a ArcGIS Enterprise szolgáltatásba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* ArcGIS Enterprise egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

> [!NOTE]
> Ez az integráció az Azure AD USA kormányzati felhőalapú környezetének használatával is elérhető. Ezt az alkalmazást az Azure AD US government Cloud Application Galleryben találja, és ugyanúgy konfigurálhatja, mint a nyilvános felhőben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A ArcGIS Enterprise **az SP és a identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést támogatja.
* A ArcGIS Enterprise **csak időben támogatja a** felhasználók üzembe helyezését.

## <a name="add-arcgis-enterprise-from-the-gallery"></a>ArcGIS Enterprise hozzáadása a katalógusból

A ArcGIS Enterprise Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a ArcGIS Enterprise-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **ArcGIS Enterprise** kifejezést a keresőmezőbe.
1. Válassza ki a **ArcGIS Enterprise** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-arcgis-enterprise"></a>Azure AD SSO konfigurálása és tesztelése a ArcGIS Enterprise-hoz

Konfigurálja és tesztelje az Azure AD SSO-t a ArcGIS Enterprise használatával egy **B. Simon** nevű tesztelési felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a ArcGIS Enterprise kapcsolódó felhasználója között.

Az Azure AD SSO és a ArcGIS Enterprise konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[ArcGIS vállalati egyszeri bejelentkezés konfigurálása](#configure-arcgis-enterprise-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre egy ArcGIS Enterprise test User](#create-arcgis-enterprise-test-user)** -t, hogy rendelkezzen a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-ArcGIS.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **ArcGIS Enterprise** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `<EXTERNAL_DNS_NAME>.portal`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin2`

    c. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Az értékek beszerzéséhez lépjen kapcsolatba a [ArcGIS Enterprise ügyfél-támogatási csapatával](mailto:support@esri.com) . Ekkor megjelenik az azonosító érték az **Identity Provider beállítása szakaszban**, amelyet az oktatóanyag későbbi részében ismertetünk.

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a ArcGIS Enterprise-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **ArcGIS Enterprise** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-arcgis-enterprise-sso"></a>ArcGIS vállalati egyszeri bejelentkezés konfigurálása

1. A ArcGIS Enterprise-on belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

1. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **ArcGIS Enterprise beállítása** elemre, és a ArcGIS Enterprise alkalmazásra irányítja. A ArcGIS Enterprise-ba való bejelentkezéshez adja meg a rendszergazdai hitelesítő adatokat. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-7-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

1. Ha manuálisan szeretné beállítani a ArcGIS Enterprise-t, jelentkezzen be a ArcGIS Enterprise vállalati webhelyre rendszergazdaként.


1. Válassza a **szervezet >beállítások szerkesztése** lehetőséget.

    ![A képernyőképen a ArcGIS Enterprise Organization (vállalati szervezet) lap jelenik meg, amely a szerkesztési beállításokat tartalmazza.](./media/arcgisenterprise-tutorial/configure-1.png)

1. Válassza a **Biztonság** fület.

    ![Képernyőfelvétel: a Biztonság lap van kiválasztva.](./media/arcgisenterprise-tutorial/configure-2.png)

1. Görgessen le a **vállalati bejelentkezések SAML használatával** szakaszban, és válassza a **vállalati bejelentkezés beállítása** lehetőséget.

    ![A képernyőképen a vállalati bejelentkezések az SAML-n keresztül láthatók, ahol kiválaszthatja a vállalati bejelentkezés beállítása lehetőséget.](./media/arcgisenterprise-tutorial/configure-3.png)

1. Az **identitás-szolgáltató beállítása** szakaszban hajtsa végre a következő lépéseket:

    ![A képernyőképen az itt ismertetett lépéseket követve állíthatja be az identitás-szolgáltatót.](./media/arcgisenterprise-tutorial/configure-4.png)

    a. Adjon meg egy nevet, például **Azure Active Directory tesztet** a **Name (név** ) szövegmezőben.

    b. Az **URL** szövegmezőbe illessze be az **alkalmazás-összevonási metaadatok URL-címét** , amelyet a Azure Portal másolt.

    c. Kattintson a **Speciális beállítások megjelenítése** elemre, és másolja be az **entitás-azonosító** értékét, és illessze be az **azonosító** szövegmezőbe a **ArcGIS Enterprise domain és URLs** szakaszának Azure Portal.

    ![Képernyőfelvétel: a D entitás beszerzése és az azonosítási szolgáltató frissítése.](./media/arcgisenterprise-tutorial/configure-5.png)

    d. Kattintson az **identitás-szolgáltató frissítése** elemre.

### <a name="create-arcgis-enterprise-test-user"></a>ArcGIS vállalati tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a ArcGIS Enterprise-ban. A ArcGIS Enterprise támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a ArcGIS Enterprise-ban, a hitelesítés után létrejön egy újat.

> [!Note]
> Ha manuálisan kell létrehoznia egy felhasználót, lépjen kapcsolatba a [ArcGIS Enterprise Support csapatával](mailto:support@esri.com).

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a ArcGIS vállalati bejelentkezési URL-címre, ahol elindíthatja a bejelentkezési folyamatot.  

* Lépjen közvetlenül a ArcGIS vállalati bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a ArcGIS Enterprise-ba, amelyhez be szeretné állítani az egyszeri bejelentkezést. 

A Microsoft My Apps használatával bármilyen módban tesztelheti az alkalmazást. Ha a saját alkalmazások ArcGIS vállalati csempére kattint, ha az SP módban van konfigurálva, a rendszer átirányítja az alkalmazás bejelentkezési lapjára a bejelentkezési folyamat elindításához, és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie arra a ArcGIS-vállalatba, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)használatába.

## <a name="next-steps"></a>Következő lépések

A ArcGIS Enterprise konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
