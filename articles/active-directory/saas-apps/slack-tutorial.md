---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Slacktel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Slack között.
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
ms.openlocfilehash: 6e2428967b8e3b4c677752955ea743c5b7d144e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98729630"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-slack"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Slacktel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Slackt Azure Active Directory (Azure AD-val). A Slack Azure AD-val való integrálásával a következőket teheti:

* Az Azure AD-ben a Slackhez hozzáférő vezérlő.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Slackbe az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A Slack egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

> [!NOTE]
> Ha több Slack-példánnyal kell integrálnia egy bérlőben, az egyes alkalmazások azonosítója változó lehet.

> [!NOTE]
> Ez az integráció az Azure AD USA kormányzati felhőalapú környezetének használatával is elérhető. Ezt az alkalmazást az Azure AD US government Cloud Application Galleryben találja, és ugyanúgy konfigurálhatja, mint a nyilvános felhőben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Slack támogatja az **SP** által KEZDEMÉNYEZett SSO-t
* A Slack **csak időben támogatja a** felhasználók üzembe helyezését
* A Slack támogatja a [felhasználók **automatikus** kiépítési felállítását](./slack-provisioning-tutorial.md)

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="adding-slack-from-the-gallery"></a>Slack hozzáadása a gyűjteményből

A Slack Azure AD-be való integrálásának konfigurálásához hozzá kell adnia Slack-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban a keresőmezőbe írja be a **Slack** kifejezést.
1. Válassza ki a **Slack** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-slack"></a>Azure AD SSO konfigurálása és tesztelése a Slackhez

Konfigurálja és tesztelje az Azure AD SSO-t a Slack használatával egy **B. Simon** nevű tesztelési felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Slackben.

Az Azure AD SSO tartalékidővel való konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[SLACK SSO konfigurálása](#configure-slack-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Slack-teszt felhasználó létrehozása](#create-slack-test-user)** – a felhasználó Azure ad-képviseletéhez csatolt B. Simon-beli partneri kapcsolattal rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **Slack** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<DOMAIN NAME>.slack.com/sso/saml/start`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet: `https://slack.com`
    
    c. A **Válasz URL-cím** mezőben adja meg a következő URL-minta egyikét:
    
    | Válasz URL-cím|
    |----------|
    | `https://<DOMAIN NAME>.slack.com/sso/saml` |
    | `https://<DOMAIN NAME>.enterprise.slack.com/sso/saml` |

    > [!NOTE]
    > Ezek az értékek nem valósak. Ezeket az értékeket a tényleges bejelentkezési URL-címmel és a válasz URL-címével kell frissítenie. Az érték beszerzéséhez vegye fel a kapcsolatot a [Slack-ügyfél támogatási csoportjával](https://slack.com/help/contact) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

    > [!NOTE]
    > Az **azonosító (Entity ID)** értéke változó lehet, ha több Slack-példánnyal rendelkezik, amelyet integrálni kell a Bérlővel. Használja a mintát `https://<DOMAIN NAME>.slack.com` . Ebben az esetben a Slack egy másik beállításával is párosítani kell ugyanazzal az értékkel.

1. A Slack-alkalmazás megadott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/edit-attribute.png)

1. A fentieken kívül a Slack alkalmazás néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket. Az attribútumot is hozzá kell adnia `email` . Ha a felhasználó nem rendelkezik e-mail-címmel, rendelje hozzá az **EmailAddress** címet a **User. userPrincipalName** -hez, és képezze le az **e-maileket** a **User. userPrincipalName**

    | Name | Forrás attribútum |
    | -----|---------|
    | EmailAddress | User. userPrincipalName |
    | e-mail | User. userPrincipalName |

   > [!NOTE]
   > A szolgáltatói (SP) konfiguráció beállításához kattintson a **Kibontás** lehetőségre a SAML-konfiguráció lapon a **Speciális beállítások** elem mellett. A **szolgáltatói kiállító** mezőben adja meg a munkaterület URL-címét. Az alapértelmezett érték a slack.com. 

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **tartalékidő beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést a Slack elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listájában válassza a **Slack** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-slack-sso"></a>Slack SSO konfigurálása

1. A tartalékidőn belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **tartalékidő beállítása** elemre, majd a Slack alkalmazásra irányítja. Itt adja meg a rendszergazdai hitelesítő adatokat a Slack-ba való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-6-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a Slacket, egy másik böngészőablakban jelentkezzen be a Slack vállalati webhelyre rendszergazdaként.

2. Lépjen a **Microsoft Azure ADra** , majd lépjen a **Team Settings** elemre.

     ![Egyszeri bejelentkezés konfigurálása Microsoft Azure AD](./media/slack-tutorial/tutorial-slack-team-settings.png)

3. A **csapat beállításai** szakaszban kattintson a **hitelesítés** lapra, majd a **beállítások módosítása** parancsra.

    ![Egyszeri bejelentkezés konfigurálása a csoport beállításaiban](./media/slack-tutorial/tutorial-slack-authentication.png)

4. Az **SAML hitelesítési beállítások** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Az SAML-hitelesítési beállítások egyszeri bejelentkezésének konfigurálása](./media/slack-tutorial/tutorial-slack-save-authentication.png)

    a.  Az **SAML 2,0-végpont (http)** szövegmezőbe illessze be a **bejelentkezési URL-cím** értékét, amelyet a Azure Portalból másolt.

    b.  Az **Identity Provider kiállító** szövegmezőben illessze be az **Azure ad-azonosító** értékét, amelyet a Azure Portalból másolt.

    c.  Nyissa meg a letöltött tanúsítványfájl a Jegyzettömbben, másolja a vágólapra a tartalmát, majd illessze be a **nyilvános tanúsítvány** szövegmezőbe.

    d. Konfigurálja a fenti három beállítást a Slack csapatának megfelelően. A beállításokkal kapcsolatos további információkért tekintse meg a **SLACK SSO konfigurációs útmutatóját** itt. `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalán](./media/slack-tutorial/tutorial-slack-expand.png)

    e. Kattintson a **Kibontás** elemre, és adja meg `https://slack.com` a **szolgáltató kiállítójának** szövegmezőjét.

    f.  Kattintson a **konfiguráció mentése** gombra.
    
    > [!NOTE]
    > Ha egynél több Slack-példánnyal kell integrálni az Azure AD-val, állítsa `https://<DOMAIN NAME>.slack.com` a **szolgáltatói kiállítót** úgy, hogy az az Azure-alkalmazás **azonosítójának** beállításával párosítható legyen.

### <a name="create-slack-test-user"></a>Slack-teszt felhasználó létrehozása

A szakasz célja, hogy létrehozzon egy B. Simon nevű felhasználót a Slackben. A Slack az igény szerinti üzembe helyezést is támogatja, ami alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. A Slack elérésére tett kísérlet során új felhasználó jön létre, ha még nem létezik. A Slack a felhasználók automatikus üzembe helyezését is támogatja, további részleteket [itt](slack-provisioning-tutorial.md) talál az automatikus felhasználó-kiépítés konfigurálásával kapcsolatban.

> [!NOTE]
> Ha manuálisan kell létrehoznia egy felhasználót, vegye fel a kapcsolatot a [Slack support csapatával](https://slack.com/help/contact).

> [!NOTE]
> Azure AD Connect a szinkronizációs eszköz, amely képes szinkronizálni a helyszíni Active Directory identitásait az Azure AD-ben, majd ezek a szinkronizált felhasználók is használhatják az alkalmazásokat, mint más Felhőbeli felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját.

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ekkor a rendszer átirányítja a Slack bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.

* Lépjen közvetlenül a Slack bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások Slack csempére kattint, a rendszer átirányítja a Slack bejelentkezési URL-címére. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

A Slack konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)