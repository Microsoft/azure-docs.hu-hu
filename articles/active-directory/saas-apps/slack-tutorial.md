---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Slacktel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Slack között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/19/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 395aa82d47f4f84070af557c2c3b741776fb51ba
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834407"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-slack"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Slacktel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Slackt Azure Active Directory (Azure AD-val). A Slack Azure AD-val való integrálásával a következőket teheti:

* Az Azure AD-ben a Slackhez hozzáférő vezérlő.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Slackbe az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A Slack egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Slack támogatja az **SP** által KEZDEMÉNYEZett SSO-t
* A Slack **csak időben támogatja a** felhasználók üzembe helyezését
* A Slack támogatja a [felhasználók **automatikus** kiépítési felállítását](https://docs.microsoft.com/azure/active-directory/saas-apps/slack-provisioning-tutorial)
* A Slack konfigurálása után kényszerítheti a munkamenet-vezérlést, amely a szervezet bizalmas adatainak valós idejű kiszűrése és beszivárgását is biztosítja. A munkamenet-vezérlő kiterjeszthető a feltételes hozzáférésből. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-slack-from-the-gallery"></a>Slack hozzáadása a gyűjteményből

A Slack Azure AD-be való integrálásának konfigurálásához hozzá kell adnia Slack-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban a keresőmezőbe írja be a **Slack** kifejezést.
1. Válassza ki a **Slack** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-slack"></a>Azure AD-beli egyszeri bejelentkezés konfigurálása és tesztelése a Slackhez

Konfigurálja és tesztelje az Azure AD SSO-t a Slack használatával egy **B. Simon**nevű tesztelési felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Slackben.

Az Azure AD SSO tartalékidővel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[SLACK SSO konfigurálása](#configure-slack-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Slack-teszt felhasználó létrehozása](#create-slack-test-user)** – a felhasználó Azure ad-képviseletéhez csatolt B. Simon-beli partneri kapcsolattal rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **Slack** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://< DOMAIN NAME>.slack.com/sso/saml/start`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet:`https://slack.com`

    > [!NOTE]
    > A bejelentkezési URL-cím értéke nem valós. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Az érték beszerzéséhez vegye fel a kapcsolatot a [Slack-ügyfél támogatási csoportjával](https://slack.com/help/contact) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A Slack-alkalmazás megadott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/edit-attribute.png)

1. A fentieken kívül a Slack alkalmazás néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket. Ha a felhasználók nem rendelkeznek e-mail-címmel, akkor rendelje hozzá az **EmailAddress** -t a **User. userPrincipalName**.

    | Name | Forrás attribútum |
    | -----|---------|
    | EmailAddress | User. userPrincipalName |
    | | |

> [!NOTE]
    > A szolgáltatói (SP) konfiguráció beállításához kattintson a **Kibontás** lehetőségre a SAML-konfiguráció lapon a **Speciális beállítások** elem mellett. A **szolgáltatói kiállító** mezőben adja meg a munkaterület URL-címét. Az alapértelmezett érték a slack.com. 

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **tartalékidő beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás**gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést a Slack elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **tartalékidő**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-slack-sso"></a>Slack SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Slack vállalati webhelyre rendszergazdaként.

2. Lépjen a **Microsoft Azure ADra** , majd lépjen a **Team Settings**elemre.

     ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalán](./media/slack-tutorial/tutorial-slack-001.png)

3. A **csapat beállításai** szakaszban kattintson a **hitelesítés** lapra, majd a **beállítások módosítása**parancsra.

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalán](./media/slack-tutorial/tutorial-slack-002.png)

4. Az **SAML hitelesítési beállítások** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalán](./media/slack-tutorial/tutorial-slack-003.png)

    a.  Az **SAML 2,0-végpont (http)** szövegmezőbe illessze be a **bejelentkezési URL-cím**értékét, amelyet a Azure Portalból másolt.

    b.  Az **Identity Provider kiállító** szövegmezőben illessze be az **Azure ad-azonosító**értékét, amelyet a Azure Portalból másolt.

    c.  Nyissa meg a letöltött tanúsítványfájl a Jegyzettömbben, másolja a vágólapra a tartalmát, majd illessze be a **nyilvános tanúsítvány** szövegmezőbe.

    d. Konfigurálja a fenti három beállítást a Slack csapatának megfelelően. A beállításokkal kapcsolatos további információkért tekintse meg a **SLACK SSO konfigurációs útmutatóját** itt. `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalán](./media/slack-tutorial/tutorial-slack-004.png)

    e. Kattintson a **Kibontás** gombra, és írja be `https://slack.com` az **Identity Provider kiállító** szövegmezőbe.

    f.  Kattintson a **konfiguráció mentése**gombra.

### <a name="create-slack-test-user"></a>Slack-teszt felhasználó létrehozása

A szakasz célja, hogy létrehozzon egy B. Simon nevű felhasználót a Slackben. A Slack az igény szerinti üzembe helyezést is támogatja, ami alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. A Slack elérésére tett kísérlet során új felhasználó jön létre, ha még nem létezik. A Slack a felhasználók automatikus üzembe helyezését is támogatja, további részleteket [itt](slack-provisioning-tutorial.md) talál az automatikus felhasználó-kiépítés konfigurálásával kapcsolatban.

> [!NOTE]
> Ha manuálisan kell létrehoznia egy felhasználót, vegye fel a kapcsolatot a [Slack support csapatával](https://slack.com/help/contact).

> [!NOTE]
> Azure AD Connect a szinkronizációs eszköz, amely képes szinkronizálni a helyszíni Active Directory identitásait az Azure AD-ben, majd ezek a szinkronizált felhasználók is használhatják az alkalmazásokat, mint más Felhőbeli felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Slack (tartalékidő) csempére kattint, automatikusan be kell jelentkeznie arra a Slack-re, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A Slack kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
