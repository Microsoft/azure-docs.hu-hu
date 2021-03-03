---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az AWS egyszeri bejelentkezéssel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és AWS egyszeri bejelentkezés között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/18/2021
ms.author: jeedes
ms.openlocfilehash: 906c7e00cba1e0feb85289e8f2a46e74924dc0c3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101664750"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-aws-single-sign-on"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az AWS egyszeri bejelentkezéssel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az AWS egyszeri bejelentkezést Azure Active Directory (Azure AD) használatával. Ha integrálja az AWS egyszeri bejelentkezést az Azure AD-vel, a következőket teheti:

* Az AWS egyszeri bejelentkezéshez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az AWS egyszeri bejelentkezésre az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* AWS egyszeri bejelentkezéses egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az AWS egyszeri bejelentkezés támogatja az **SP** -t és a identitásszolgáltató kezdeményezett SSO-t

* Az AWS egyszeri bejelentkezés támogatja a [**felhasználók automatikus üzembe**](https://docs.microsoft.com/azure/active-directory/saas-apps/aws-single-sign-on-provisioning-tutorial)helyezését.

## <a name="adding-aws-single-sign-on-from-the-gallery"></a>AWS egyszeri bejelentkezés hozzáadása a katalógusból

Az AWS egyszeri bejelentkezés Azure AD-be való integrálásának konfigurálásához az AWS egyszeri bejelentkezést hozzá kell adnia a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a keresőmezőbe az **AWS egyszeri bejelentkezés** kifejezést.
1. Válassza az **AWS egyszeri bejelentkezés** az eredmények panelen lehetőséget, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-aws-single-sign-on"></a>Azure AD SSO konfigurálása és tesztelése az AWS egyszeri bejelentkezéshez

Konfigurálja és tesztelje az Azure AD SSO-t az AWS egyszeri bejelentkezéssel egy **B. Simon** nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között AWS egyszeri bejelentkezéssel.

Az Azure AD SSO az AWS egyszeri bejelentkezéssel való konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. Az **[AWS egyszeri bejelentkezésének konfigurálása](#configure-aws-single-sign-on-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre AWS egyszeri bejelentkezési tesztet használó felhasználó](#create-aws-single-sign-on-test-user)** -, hogy az AWS-alapú egyszeri bejelentkezéssel rendelkezzen, amely a felhasználó Azure ad-beli képviseletéhez kapcsolódik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. Az Azure Portal az **AWS egyszeri bejelentkezési** alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha **szolgáltatói metaadatokat tartalmazó fájllal** rendelkezik, az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. Kattintson a **metaadat-fájl feltöltése** elemre.

    ![image1](common/upload-metadata.png)

    b. Kattintson a **mappa emblémára** a metaadat-fájl kiválasztásához, majd kattintson a **feltöltés** elemre.

    ![image2](common/browse-upload-metadata.png)

    c. A metaadat-fájl feltöltése után a rendszer az alapszintű SAML-konfiguráció szakaszban automatikusan feltölti az **azonosítót** és a **Válasz URL-** értékeket:

    ![image3](common/idp-intiated.png)

    > [!Note]
    > Ha az **azonosító** és a **Válasz URL-** értékei nem kapnak automatikus polulated, akkor a követelménynek megfelelően adja meg manuálisan az értékeket.

1. Ha nem rendelkezik **szolgáltatói metaadat-fájllal**, hajtsa végre az alábbi lépéseket az **alapszintű SAML-konfiguráció** szakaszban, ha **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni az alkalmazást, adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<REGION>.signin.aws.amazon.com/platform/saml/<ID>`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<REGION>.signin.aws.amazon.com/platform/saml/acs/<ID>`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://portal.sso.<REGION>.amazonaws.com/saml/assertion/<ID>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Az értékek beszerzéséhez vegye fel a kapcsolatot az [AWS egyszeri bejelentkezés ügyfél-támogatási csapatával](mailto:aws-sso-partners@amazon.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az AWS egyszeri bejelentkezési alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/edit-attribute.png)


    > [!NOTE]
    > Ha az ABAC engedélyezve van az AWS SSO-ban, a további attribútumok közvetlenül az AWS-fiókokba adhatók át munkamenet-címkékként.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. Az **AWS egyszeri bejelentkezés beállítása** szakaszban a követelmények alapján másolja ki a megfelelő URL-címeket.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést az AWS egyszeri bejelentkezéshez való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **AWS egyszeri bejelentkezés** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-aws-single-sign-on-sso"></a>AWS egyszeri bejelentkezéses SSO konfigurálása

1. Nyissa meg az **AWS SSO-konzolt** . 
2. A bal oldali navigációs panelen válassza a **Beállítások** lehetőséget.
3. A **Beállítások** lapon keresse meg a **személyazonosság forrása** elemet, majd kattintson a **módosítás** elemre.
4. A címtár módosítása lapon válassza a **külső identitás-szolgáltató** elemet.
5. A **szolgáltatói metaadatok** szakaszban keresse meg az **AWS SSO SAML-metaadatokat** , és válassza a **metaadatok letöltése** lehetőséget a metaadat-fájl letöltéséhez és a számítógépre mentéséhez.
6. Az **Identity Provider metaadatai** szakaszban válassza a **tallózás** lehetőséget a Azure Portal letöltött metaadat-fájl feltöltéséhez.
7. Válassza a **Next (tovább): felülvizsgálat** lehetőséget.
8. A szövegmezőbe írja be a **Confirm (megerősítés** ) lehetőséget a könyvtár módosításának megerősítéséhez.
9. Válassza a **Befejezés** lehetőséget.

### <a name="create-aws-single-sign-on-test-user"></a>AWS egyszeri bejelentkezési teszt felhasználó létrehozása

1. Nyissa meg az **AWS SSO-konzolt**.

2. A bal oldali navigációs panelen válassza a **felhasználók** lehetőséget.

3. A felhasználók lapon válassza a **felhasználó hozzáadása** elemet.

4. A felhasználó hozzáadása lapon kövesse az alábbi lépéseket:

    a. A Felhasználónév mezőbe írja be a B. Simon **nevet** .

    b. Az **e-mail cím** mezőbe írja be a ( `username@companydomain.extension` ) értéket. Például: `B.Simon@contoso.com`.

    c. Az **e-mail cím megerősítése** mezőbe írja be újra az e-mail-címet az előző lépésből.

    d. Az utónév mezőbe írja be a nevet `Jane` .

    e. A vezetéknév mezőbe írja be a nevet `Doe` .

    f. A megjelenítendő név mezőbe írja be a (z `Jane Doe` ) értéket.

    : Válassza a **Tovább: csoportok** lehetőséget.

    > [!NOTE]
    > Győződjön meg arról, hogy az AWS SSO-ban megadott Felhasználónév megegyezik a felhasználó Azure AD-beli bejelentkezési nevével. Ez segít elkerülni a hitelesítési problémákat.

5. Kattintson a **Felhasználó hozzáadása** lehetőségre.
6. Ezután hozzá kell rendelnie a felhasználót az AWS-fiókhoz. Ehhez az AWS SSO-konzol bal oldali navigációs paneljén válassza az **AWS-fiókok** lehetőséget.
7. Az AWS-fiókok lapon válassza az AWS-szervezet lapot, jelölje be a felhasználóhoz hozzárendelni kívánt AWS-fiók melletti jelölőnégyzetet. Ezután válassza a **felhasználók kiosztása** lehetőséget.
8. A felhasználók kiosztása lapon keresse meg és jelölje be a B. Simon felhasználó melletti jelölőnégyzetet. Ezután válassza a **Tovább: engedélyek-készletek** elemet.
9. Az engedélyek kiválasztása szakaszban jelölje be azon engedély beállítása melletti jelölőnégyzetet, amelyet hozzá szeretne rendelni a következő felhasználóhoz: B. Simon. Ha nem rendelkezik meglévő engedélyekkel, válassza az **új engedélyezési csoport létrehozása** lehetőséget.

    > [!NOTE]
    > Az engedélyezési beállítások határozzák meg, hogy a felhasználók és a csoportok milyen hozzáférési szintű AWS-fiókkal rendelkezzenek. Az engedélyekkel kapcsolatos további tudnivalókért tekintse meg az AWS SSO **engedélyek beállítása** lapot.
10. Válassza a **Befejezés** lehetőséget.

> [!NOTE]
> Az AWS egyszeri bejelentkezés a felhasználók automatikus üzembe helyezését is támogatja. [További részletekért](https://docs.microsoft.com/azure/active-directory/saas-apps/aws-single-sign-on-provisioning-tutorial) tekintse meg az automatikus felhasználó-kiépítés konfigurálását ismertető témakört.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja az AWS egyszeri bejelentkezés bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.  

* Lépjen az AWS egyszeri bejelentkezés bejelentkezési URL-címére közvetlenül, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie az AWS egyszeri bejelentkezésre, amelyhez be kell állítania az egyszeri bejelentkezést 

A Microsoft My Apps használatával bármilyen módban tesztelheti az alkalmazást. Ha a saját alkalmazásokban az AWS egyszeri bejelentkezés csempére kattint, ha az SP módban van konfigurálva, a rendszer átirányítja az alkalmazás bejelentkezési lapjára a bejelentkezési folyamat elindításához, és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie az AWS egyszeri Bejelentkezésbe, amelyhez be kell állítania az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)használatába.


## <a name="next-steps"></a>Következő lépések

Az AWS egyszeri bejelentkezés konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


