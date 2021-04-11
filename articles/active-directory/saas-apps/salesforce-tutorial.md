---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Salesforce | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directory és a Salesforce közötti egyszeri bejelentkezést.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/18/2021
ms.author: jeedes
ms.openlocfilehash: 0f800d2d42d0d8815021f1582b04750d87aa5abc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101651425"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Salesforce

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Salesforce a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Salesforce-t az Azure AD-vel, a következőket teheti:

* A Salesforce-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Salesforce az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Salesforce egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Salesforce támogatja az **SP** által kezdeményezett egyszeri bejelentkezést.

* A Salesforce támogatja [a felhasználók **automatikus** üzembe](salesforce-provisioning-tutorial.md) helyezését és megszüntetését (ajánlott).

* A Salesforce **csak időben támogatja a** felhasználók üzembe helyezését.

* Az Salesforce Mobile Application mostantól konfigurálható az Azure AD-vel az egyszeri bejelentkezés engedélyezéséhez. Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

## <a name="adding-salesforce-from-the-gallery"></a>Salesforce hozzáadása a gyűjteményből

A Salesforce Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Salesforce a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Salesforce** kifejezést a keresőmezőbe.
1. Válassza ki a **Salesforce** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-salesforce"></a>Azure AD SSO konfigurálása és tesztelése a Salesforce-hez

Konfigurálja és tesztelje az Azure AD SSO-t a Salesforce a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Salesforce-ben.

Az Azure AD SSO és a Salesforce konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[SALESFORCE SSO konfigurálása](#configure-salesforce-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre Salesforce-teszt felhasználót](#create-salesforce-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-Salesforce rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **Salesforce** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:
    
    a. Az **azonosító** szövegmezőbe írja be az értéket a következő minta használatával:

    Vállalati fiók: `https://<subdomain>.my.salesforce.com`

    Fejlesztői fiók: `https://<subdomain>-dev-ed.my.salesforce.com`

    b. A **Válasz URL-címe** szövegmezőbe írja be az értéket a következő minta használatával:

    Vállalati fiók: `https://<subdomain>.my.salesforce.com`

    Fejlesztői fiók: `https://<subdomain>-dev-ed.my.salesforce.com`

    c. A **bejelentkezési URL** szövegmezőbe írja be az értéket a következő minta használatával:

    Vállalati fiók: `https://<subdomain>.my.salesforce.com`

    Fejlesztői fiók: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Az értékek lekéréséhez forduljon a Salesforce ügyfélszolgálati [csapatához](https://help.salesforce.com/support) .

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **Salesforce beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Salesforce.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Salesforce** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-salesforce-sso"></a>Salesforce SSO konfigurálása

1. A Salesforce belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

1. Miután hozzáadta a bővítményt a böngészőhöz, a **Salesforce beállítása** lehetőségre kattintva a Salesforce egyetlen Sign-On alkalmazást fogja irányítani. Itt adja meg a rendszergazdai hitelesítő adatokat az Salesforce egyszeri bejelentkezésre való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-13-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

1. Ha manuálisan szeretné beállítani a Salesforce, nyisson meg egy új böngészőablakot, és jelentkezzen be a Salesforce vállalati webhelyére rendszergazdaként, és hajtsa végre a következő lépéseket:

1. Kattintson **az oldal** jobb felső sarkában található **Beállítások ikonra** .

    ![Egyetlen Sign-On beállítások ikonjának konfigurálása](./media/salesforce-tutorial/configure1.png)

1. Görgessen le a **beállításokhoz** a navigációs ablaktáblán, és kattintson az **Identity (identitás** ) elemre a kapcsolódó szakasz kibontásához. Ezután kattintson az **egyszeres Sign-On beállítások** lehetőségre.

    ![Önálló Sign-On beállítások konfigurálása](./media/salesforce-tutorial/sf-admin-sso.png)

1. Az **egyszeres Sign-On beállítások** lapon kattintson a **Szerkesztés** gombra.

    ![Egyszeres Sign-On szerkesztésének konfigurálása](./media/salesforce-tutorial/sf-admin-sso-edit.png)

    > [!NOTE]
    > Ha nem tudja engedélyezni a Salesforce-fiók egyetlen Sign-On beállításait, akkor előfordulhat, hogy kapcsolatba kell lépnie a [Salesforce ügyfél-támogatási csapatával](https://help.salesforce.com/support).

1. Válassza az **SAML engedélyezve** lehetőséget, majd kattintson a **Mentés** gombra.

    ![Egyszeri Sign-On SAML konfigurálása engedélyezve](./media/salesforce-tutorial/sf-enable-saml.png)

1. Az SAML egyszeri bejelentkezés beállításainak konfigurálásához kattintson **az új elemre a metaadat-fájlból**.

    ![Egyetlen Sign-On konfigurálása az új metaadat-fájlból](./media/salesforce-tutorial/sf-admin-sso-new.png)

1. Kattintson a **fájl kiválasztása** lehetőségre a Azure Portal letöltött metaadat XML-fájl feltöltéséhez, majd kattintson a **Létrehozás** gombra.

    ![Egyetlen Sign-On konfigurálása fájl kiválasztása](./media/salesforce-tutorial/xmlchoose.png)

1. Az SAML-alapú **Sign-On beállítások** lapon a mezők automatikusan frissülnek, ha az SAML JIT-t szeretné használni, jelölje be a **felhasználó kiépítés engedélyezése** jelölőnégyzetet, és válassza ki az **SAML-identitás típusa** elemet a **felhasználói objektum összevonási azonosítójának** kiválasztásával, törölje a felhasználó kihelyezésének **engedélyezése** beállítást, és válassza az **SAML-identitás típusa** beállítást **a felhasználó Salesforce-felhasználónevének** megadásakor. Kattintson a **Mentés** gombra.

    ![Egyszeri Sign-On felhasználó-kiépítés engedélyezése](./media/salesforce-tutorial/salesforcexml.png)

1. A Salesforce bal oldali navigációs paneljén kattintson a **Vállalati beállítások** elemre a kapcsolódó szakasz kibontásához, majd kattintson **a saját tartomány** elemre.

    ![Önálló Sign-On konfigurálása saját tartományhoz](./media/salesforce-tutorial/sf-my-domain.png)

1. Görgessen le a **hitelesítési konfiguráció** szakaszhoz, és kattintson a **Szerkesztés** gombra.

    ![Egy Sign-On hitelesítési konfiguráció konfigurálása](./media/salesforce-tutorial/sf-edit-auth-config.png)

1. A **hitelesítési konfiguráció** szakaszban tekintse meg a **bejelentkezési oldalt** és  **a AzureSSO** az SAML SSO-konfiguráció **hitelesítési szolgáltatásaként** , majd kattintson a **Mentés** gombra.

    ![Egy Sign-On hitelesítési szolgáltatás konfigurálása](./media/salesforce-tutorial/authentication.png)

    > [!NOTE]
    > Ha egynél több hitelesítési szolgáltatás van kiválasztva, a rendszer a felhasználókat arra kéri, hogy válassza ki, hogy melyik hitelesítési szolgáltatást szeretnék bejelentkezni a Salesforce-környezetbe való egyszeri bejelentkezés kezdeményezése során. Ha nem szeretné, hogy megtörténjen, az **összes többi hitelesítési szolgáltatást ne jelölje be**.

### <a name="create-salesforce-test-user"></a>Salesforce-tesztelési felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre a Salesforce-ben. A Salesforce támogatja az igény szerinti üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a Salesforce-ben, akkor létrejön egy új, amikor megpróbál hozzáférni a Salesforce. A Salesforce az automatikus felhasználó-kiépítés használatát is támogatja, további részleteket [itt](salesforce-provisioning-tutorial.md) talál az automatikus felhasználó-kiépítés konfigurálásával kapcsolatban.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a Salesforce bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül a Salesforce bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások Salesforce csempére kattint, automatikusan be kell jelentkeznie arra a Salesforce, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="test-sso-for-salesforce-mobile"></a>Salesforce-teszt (SSO) tesztelése (mobil)

1. Nyissa meg a Salesforce Mobile Application alkalmazást. A bejelentkezés lapon kattintson az **egyéni tartomány használata** lehetőségre.

    ![Salesforce Mobile App egyéni tartomány használata](media/salesforce-tutorial/mobile-app1.png)

1. Az **egyéni tartomány** szövegmezőbe írja be a regisztrált egyéni tartománynevet, és kattintson a **Folytatás** gombra.

    ![Salesforce Mobile App – egyéni tartomány](media/salesforce-tutorial/mobile-app2.png)

1. Adja meg az Azure AD-beli hitelesítő adatait, hogy bejelentkezzen a Salesforce alkalmazásba, és kattintson a **tovább** gombra.

    ![Salesforce Mobile App Azure AD-beli hitelesítő adatok](media/salesforce-tutorial/mobile-app3.png)

1. A **hozzáférés engedélyezése** lapon az alább látható módon kattintson az **Engedélyezés** lehetőségre a Salesforce alkalmazáshoz való hozzáféréshez.

    ![Salesforce Mobile App hozzáférés engedélyezése](media/salesforce-tutorial/mobile-app4.png)

1. Végül a sikeres bejelentkezés után megjelenik az alkalmazás kezdőlapja.

    ![Salesforce Mobile App homepage ](media/salesforce-tutorial/mobile-app5.png) ![ Salesforce Mobile alkalmazás](media/salesforce-tutorial/mobile-app6.png)

## <a name="next-steps"></a>Következő lépések

A Salesforce konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)