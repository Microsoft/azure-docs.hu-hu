---
title: 'Oktatóanyag: Azure Active Directory integráció a beli jfrog Artifactory | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és beli jfrog Artifactory között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: e6e20f4b86a58f0bf31f57aa1221daf12f397fbc
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560096"
---
# <a name="tutorial-integrate-jfrog-artifactory-with-azure-active-directory"></a>Oktatóanyag: a beli jfrog-Artifactory integrálása Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a beli jfrog-Artifactory a Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a beli jfrog Artifactory, a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér a beli jfrog Artifactory.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a beli jfrog Artifactory az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Beli jfrog Artifactory egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A beli jfrog Artifactory támogatja **az SP és a identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést.
* A beli jfrog Artifactory **csak időben támogatja a** felhasználók üzembe helyezését.

## <a name="add-jfrog-artifactory-from-the-gallery"></a>Beli jfrog-Artifactory hozzáadása a gyűjteményből

A beli jfrog-Artifactory Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a beli jfrog-Artifactory a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **beli jfrog Artifactory** kifejezést a keresőmezőbe.
1. Válassza ki a **beli jfrog Artifactory** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-jfrog-artifactory"></a>Azure AD SSO konfigurálása és tesztelése beli jfrog-Artifactory

Konfigurálja és tesztelje az Azure AD SSO-t a beli jfrog Artifactory egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a beli jfrog Artifactory.

Az Azure AD SSO beli jfrog-Artifactory való konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Beli jfrog-ARTIFACTORY SSO konfigurálása](#configure-jfrog-artifactory-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Beli jfrog-Artifactory-teszt felhasználó létrehozása](#create-jfrog-artifactory-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon beli jfrog-Artifactory rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **beli jfrog Artifactory** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeres Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** ceruza ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `<servername>.jfrog.io`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:
    
    - Artifactory 6. x esetén: `https://<servername>.jfrog.io/artifactory/webapp/saml/loginResponse`
    - Artifactory 7. x esetén: `https://<servername>.jfrog.io/<servername>/webapp/saml/loginResponse`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:
    - Artifactory 6. x esetén: `https://<servername>.jfrog.io/<servername>/webapp/`
    - Artifactory 7. x esetén: `https://<servername>.jfrog.io/ui/login`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Az értékek lekéréséhez forduljon a beli jfrog Artifactory-ügyfélszolgálati [csapatához](https://support.jfrog.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A beli jfrog Artifactory alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a felhasználói attribútumok párbeszédpanel megnyitásához.

    ![A képernyőképen a felhasználói attribútumok láthatók a szerkesztési vezérlővel.](common/edit-attribute.png)

1. A fentiek mellett a beli jfrog Artifactory számos további attribútumot vár, amelyeket vissza kell adni az SAML-válaszban. A **csoport jogcímek (előzetes verzió)** párbeszédpanel **felhasználói attribútumok & jogcímek** szakaszában hajtsa végre a következő lépéseket:

    a. Kattintson a **kérelemben visszaadott csoportok** melletti **tollra** .

    ![Képernyőfelvétel: felhasználói attribútumok & jogcímek kijelölése a szerkesztési ikonnal.](./media/jfrog-artifactory-tutorial/configuration-4.png)

    ![Képernyőfelvétel: a Group jogcímek szakasz az összes kiválasztott csoporttal együtt.](./media/jfrog-artifactory-tutorial/configuration-5.png)

    b. Válassza az **összes csoport** lehetőséget a választógombok listájáról.

    c. Kattintson a **Mentés** gombra.

4. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítványt (Base64)** , majd válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](./media/jfrog-artifactory-tutorial/certificate-base.png)

6. Konfigurálja a Artifactory (SAML-szolgáltató nevét) az "azonosító" mezővel (lásd: 4. lépés). A **beli jfrog Artifactory beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény alapján.

   - Artifactory 6. x esetén: `https://<servername>.jfrog.io/artifactory/webapp/saml/loginResponse` 
   - Artifactory 7. x esetén: `https://<servername>.jfrog.io/<servername>/webapp/saml/loginResponse`

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a beli jfrog Artifactory.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **beli jfrog Artifactory** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-jfrog-artifactory-sso"></a>Beli jfrog Artifactory SSO konfigurálása

Ha az egyszeri bejelentkezést szeretné konfigurálni a **beli jfrog Artifactory** oldalon, el kell küldenie a letöltött **tanúsítványt (RAW)** és a megfelelő másolt url-címeket a Azure Portalból a [beli jfrog Artifactory-támogató csapatának](https://support.jfrog.com). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-jfrog-artifactory-test-user"></a>Beli jfrog Artifactory-tesztelési felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre a beli jfrog Artifactory. A beli jfrog Artifactory támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha a felhasználó még nem létezik a beli jfrog Artifactory, a hitelesítés után létrejön egy új.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ez átirányítja a beli jfrog Artifactory bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.  

* Lépjen közvetlenül a beli jfrog Artifactory bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a beli jfrog-Artifactory, amelyhez be szeretné állítani az egyszeri bejelentkezést. 

A Microsoft My Apps használatával bármilyen módban tesztelheti az alkalmazást. Ha a saját alkalmazások beli jfrog Artifactory csempére kattint, ha az SP módban van konfigurálva, a rendszer átirányítja az alkalmazás bejelentkezési lapjára a bejelentkezési folyamat elindításához, és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie arra a beli jfrog-Artifactory, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)használatába.

## <a name="next-steps"></a>Következő lépések

A beli jfrog-Artifactory konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
