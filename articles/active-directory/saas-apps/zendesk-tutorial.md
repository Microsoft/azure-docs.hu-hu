---
title: 'Oktatóanyag: Azure Active Directory integráció a zendesk-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és zendesk között.
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
ms.openlocfilehash: 58e9cc2fda7779cf0d62db8e0b6f78e5bb4d95f8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98731802"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zendesk"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a zendesk

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a zendesk a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az zendesk-t az Azure AD-vel, a következőket teheti:

* A zendesk-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a zendesk az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.


## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Zendesk egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A zendesk támogatja az **SP** által KEZDEMÉNYEZett SSO-t
* A zendesk támogatja az [ **automatikus** felhasználó-kiépítés használatát](zendesk-provisioning-tutorial.md)


## <a name="adding-zendesk-from-the-gallery"></a>Zendesk hozzáadása a gyűjteményből

A zendesk Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a zendesk a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **zendesk** kifejezést a keresőmezőbe.
1. Válassza ki a **zendesk** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-zendesk"></a>Azure AD SSO konfigurálása és tesztelése a zendesk-hez

Konfigurálja és tesztelje az Azure AD SSO-t a zendesk a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a zendesk-ben.

Az Azure AD SSO és a zendesk konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[ZENDESK SSO konfigurálása](#configure-zendesk-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre zendesk-teszt felhasználót](#create-zendesk-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-zendesk rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **zendesk** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<subdomain>.zendesk.com`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<subdomain>.zendesk.com`

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<subdomain>.zendesk.com/access/saml`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és válasz URL-címmel. Az értékek lekéréséhez forduljon a zendesk ügyfélszolgálati [csapatához](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A zendesk alkalmazás megadott formátumban várja az SAML-kijelentéseket. Nincsenek kötelező SAML-attribútumok, de opcionálisan kezelhetők a **felhasználói attribútumok** szakasz az alkalmazás-integráció lapon. Az **egyszeres Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** gombra a **felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![A képernyőképen a szerkesztési ikonnal jelölt felhasználói attribútumok láthatók.](common/edit-attribute.png)

    > [!NOTE]
    > A bővítmény attribútumaival olyan attribútumokat adhat hozzá, amelyek alapértelmezés szerint nem az Azure AD-ben vannak. A **zendesk** által elfogadott SAML-attribútumok teljes listájának lekéréséhez kattintson az [SAML-ban beállítható felhasználói attribútumok](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) elemre.

1. Az **SAML aláíró tanúsítvány** szakaszban kattintson a **Szerkesztés** gombra az **SAML aláíró tanúsítvány** párbeszédpanel megnyitásához.

    ![SAML aláíró tanúsítvány szerkesztése](common/edit-certificate.png)

1. Az **SAML aláíró tanúsítvány** szakaszban másolja az **ujjlenyomat értékét** , és mentse a számítógépre.

    ![Ujjlenyomat értékének másolása](common/copy-thumbprint.png)

1. A **zendesk beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a zendesk.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **zendesk** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-zendesk-sso"></a>Zendesk SSO konfigurálása

1. A **zendesk** belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőségre kattintva.

    ![A képernyőképen a bővítmény telepítése gomb látható.](./media/target-process-tutorial/install_extension.png)

1. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **telepítés zendesk** gombra a zendesk alkalmazáshoz. Itt adja meg a rendszergazdai hitelesítő adatokat a zendesk való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-6-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

1. Ha manuálisan szeretné beállítani a zendesk, nyisson meg egy új böngészőablakot, és jelentkezzen be a zendesk vállalati webhelyére rendszergazdaként, és hajtsa végre a következő lépéseket:

1. A **zendesk felügyeleti központban** kattintson a biztonsági **Beállítások** elemre a **Biztonság** lapon.

    ![A képernyőképen a zendesk felügyeleti központ látható a kiválasztott biztonsági beállításokkal.](./media/zendesk-tutorial/settings.png "Biztonság")

1. Nyissa meg az **egyszeri bejelentkezési** oldalt, és kattintson a **Szerkesztés** elemre az **SAML**-ban.

    ![A képernyőképen látható az egyszeri bejelentkezési oldal, ahol a Szerkesztés van kiválasztva.](./media/zendesk-tutorial/saml-sso.png "Biztonság")

1. Hajtsa végre az alábbi lépéseket az **egyszeri bejelentkezés** lapon.

    ![Egyszeri bejelentkezés](./media/zendesk-tutorial/saml-configuration.png "Egyszeri bejelentkezés")

    a. Az **SAML SSO URL** szövegmezőben illessze be azt a **bejelentkezési URL-címet** , amelyet a Azure Portalból másolt.

    b. A **Tanúsítvány ujjlenyomata** szövegmezőbe illessze be a tanúsítvány **ujjlenyomatának** értékét, amelyet a Azure Portal másolt.

    c. A **távoli kijelentkezési URL** szövegmezőben illessze be a **KIJELENTKEZÉSI URL-címet** , amelyet a Azure Portal másolt.

    d. Kattintson a **Mentés** gombra.

### <a name="create-zendesk-test-user"></a>Zendesk-tesztelési felhasználó létrehozása

Ennek a szakasznak a célja egy Britta Simon nevű felhasználó létrehozása a zendesk-ben. A zendesk támogatja az automatikus felhasználó-kiépítés használatát, amely alapértelmezés szerint engedélyezve van. További részletekért tekintse [meg az automatikus](Zendesk-provisioning-tutorial.md) felhasználó-kiépítés konfigurálását ismertető témakört.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a zendesk bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül a zendesk bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások zendesk csempére kattint, a rendszer átirányítja a zendesk bejelentkezési URL-címére. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

A zendesk konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).