---
title: 'Oktatóanyag: Azure Active Directory integráció a polgári platformmal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a polgári platform között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/25/2019
ms.author: jeedes
ms.openlocfilehash: 26109d4b8875dd0b442521513dbd219dc0de06e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92455969"
---
# <a name="tutorial-integrate-civic-platform-with-azure-active-directory"></a>Oktatóanyag: a polgári platform integrálása a Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a polgári platformot Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a polgári platformot, a következőket teheti:

* A polgári platformhoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a polgári platformba az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [itt](https://azure.microsoft.com/pricing/free-trial/)kérhet egy hónapos ingyenes próbaverziót.
* Polgári platformos egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Civic platform támogatja az **SP** által KEZDEMÉNYEZett SSO-t





## <a name="adding-civic-platform-from-the-gallery"></a>Polgári platform hozzáadása a katalógusból

A polgári platform Azure AD-integrációjának konfigurálásához hozzá kell adnia egy polgári platformot a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Civic platform** kifejezést a keresőmezőbe.
1. Válassza a **Civic platform** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t a Civic platformmal egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a polgári platformon.

Az Azure AD SSO polgári platformmal való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
2. A **[polgári platform SSO konfigurálása](#configure-civic-platform-sso)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
4. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
5. **[Hozzon létre egy Civic platform test User](#create-civic-platform-test-user)** -t, hogy a B. Simon partnere legyen a Civic platformon, amely a felhasználó Azure ad-képviseletéhez van társítva.
6. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **Civic platform** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<SUBDOMAIN>.accela.com`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet: `civicplatform.accela.com`

    > [!NOTE]
    > A bejelentkezési URL-cím értéke nem valós. Frissítse ezt az értéket a tényleges bejelentkezési URL-címmel. Az érték beszerzéséhez lépjen kapcsolatba a [Civic platform ügyfél-támogatási csapatával](mailto:skale@accela.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a számítógépre mentéséhez.

    ![Képernyőfelvétel: az SAML aláíró tanúsítvány lapja, amelyen átmásolhatja az alkalmazások összevonási metaadatait.](common/copy-metadataurl.png)

1. Navigáljon **Azure Active Directory**  >  **Alkalmazásregisztrációk** az Azure ad-ben, majd válassza ki az alkalmazást.

1. Másolja a **címtár-(bérlői) azonosítót** , és tárolja azt a Jegyzettömbben.

    ![Másolja a könyvtárat (bérlői azonosítót), és tárolja azt az alkalmazás kódjában.](media/civic-platform-tutorial/directoryid.png)

1. Másolja ki az **alkalmazás azonosítóját** , és tárolja azt a Jegyzettömbben.

   ![Az alkalmazás (ügyfél) AZONOSÍTÓjának másolása](media/civic-platform-tutorial/applicationid.png)

1. Navigáljon **Azure Active Directory**  >  **Alkalmazásregisztrációk** az Azure ad-ben, majd válassza ki az alkalmazást. Válassza ki a **tanúsítványok & Secrets** elemet.

1. Válassza ki az **ügyfél titkai-> új ügyfél titkát**.

1. Adja meg a titok leírását és időtartamát. Ha elkészült, válassza a **Hozzáadás** lehetőséget.

   > [!NOTE]
   > Az ügyfél titkos kulcsának mentése után megjelenik az ügyfél titkos kulcsának értéke. Másolja ezt az értéket, mert később nem tudja lekérni a kulcsot.

   ![Másolja a titkos értéket, mert később nem lehet beolvasni](media/civic-platform-tutorial/secretkey.png)

### <a name="configure-civic-platform-sso"></a>A polgári platform egyszeri bejelentkezésének konfigurálása

1. Nyisson meg egy új böngészőablakot, és jelentkezzen be a Atlassian felhőalapú vállalati webhelyére rendszergazdaként.

1. Kattintson a **normál beállítások lehetőségre**.

    ![A képernyőképen a Atlassian Felhőbeli webhely látható, amely a felügyeleti eszközök alatt elnevezett általános döntéseket tartalmazza.](media/civic-platform-tutorial/standard-choices.png)

1. Hozzon létre egy szabványos választható **ssoconfig**.

1. Keresse meg a **ssoconfig**  , és küldje el.

    ![A képernyőképen a standard szintű lehetőségek keresését láthatja az s s o-konfigurációban megadott névvel.](media/civic-platform-tutorial/sso-config.png)

1. A piros pontra kattintva bontsa ki a SSOCONFIG elemet.

    ![A képernyőfelvételen a szokásos lehetőségek között böngészhet az S S O-KONFIGURÁCIÓval.](media/civic-platform-tutorial/sso-config01.png)

1. Adja meg az SSO-hez kapcsolódó konfigurációs információkat a következő lépésben:

    ![Képernyőfelvétel: a standard Choices elemek szerkesztése az S S O-KONFIGURÁCIÓKhoz.](media/civic-platform-tutorial/sso-config02.png)

    1. A **ApplicationId** mezőben adja meg az **alkalmazás azonosítójának** értékét, amelyet a Azure Portal másolt.

    1. A **clientSecret** mezőben adja meg a **titkos** értéket, amelyet a Azure Portal másolt.

    1. A **Könyvtárazonosító** mezőben adja meg a **könyvtár (bérlő) azonosítójának** értékét, amelyet a Azure Portal másolt.

    1. Adja meg a idpName. Pl.: – `Azure` .

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a polgári platformhoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **polgári platform** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-civic-platform-test-user"></a>Polgári platform tesztelési felhasználó létrehozása

Ebben a szakaszban létrehoz egy B. Simon nevű felhasználót a Civic platformon. Működjön együtt a Civic platform támogatási csapatával, hogy hozzáadja a felhasználókat a [Civic platform ügyfél-támogatási csapatához](mailto:skale@accela.com). Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Civic platform csempére kattint, automatikusan be kell jelentkeznie arra a Civic platformra, amelyhez az SSO-t beállította. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)