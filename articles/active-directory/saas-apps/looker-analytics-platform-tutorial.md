---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrációja a szemlélő Analytics platformmal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a szemlélő elemzési platformja között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/10/2020
ms.author: jeedes
ms.openlocfilehash: dbb6f6d278256730e77677e78f452615fe4b611e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96180741"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-looker-analytics-platform"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrációja a szemlélő elemzési platformmal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a szemlélő elemzési platformot Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a szemlélő elemzési platformot, a következőket teheti:

* Az olyan Azure AD-beli vezérlők, akik hozzáférnek a szemlélő analitikai platformhoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a szemlélő Analytics platformba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A szemlélő Analytics-platform egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A szemlélő elemzési platform támogatja **az SP és a identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést
* A szemlélő elemzési platformja **csak időben támogatja a** felhasználók üzembe helyezését


## <a name="adding-looker-analytics-platform-from-the-gallery"></a>Szemlélő elemzési platform hozzáadása a gyűjteményből

A szemlélő elemzési platform Azure AD-be való integrálásának konfigurálásához a galériából a felügyelt SaaS-alkalmazások listájához hozzá kell adnia a szemlélő elemzés platformját.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a következőt: **szemlélő elemzési platform** a keresőmezőbe.
1. Válassza ki a **szemlélő elemzési platformot** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-looker-analytics-platform"></a>Az Azure AD SSO konfigurálása és tesztelése a szemlélő elemzési platformon

Konfigurálja és tesztelje az Azure AD SSO-t a szemlélő analitikai platformmal egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között, a szemlélő elemzési platformon.

Az Azure AD SSO és a szemlélő elemzési platform konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[szemlélő elemzési platform SSO konfigurálása](#configure-looker-analytics-platform-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre egy szemlélő analitikai platform tesztelési felhasználót](#create-looker-analytics-platform-test-user)** –, hogy a B. Simon partnere legyen a szemlélő elemzési platformján, amely a felhasználó Azure ad-képviseletéhez van társítva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **szemlélő analitikai platform** alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `<SPN>_looker`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<SUBDOMAIN>.looker.com/samlcallback`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<SUBDOMAIN>.looker.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Az értékek beszerzéséhez vegye fel a kapcsolatot a [szemlélő Analytics platform ügyfél-támogatási csapatával](mailto:support@looker.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. Az **igen hasznos elemzési platform beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a szemlélő elemzési platformhoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **szemlélő elemzési platform** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-looker-analytics-platform-sso"></a>A szemlélő elemzési platformjának egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a szemlélő Analytics platform webhelyére rendszergazdaként.

1. Ugrás a **rendszergazdai**  ->  **hitelesítés**  ->  **SAML** -re

    ![az SAML-beállítás képernyőképe](./media/looker-analytics-platform-tutorial/admin.png)

1. Illessze be a Azure Portalból a **identitásszolgáltató metadata** szövegmezőbe másolt **összevonási metaadat** -információkat, majd kattintson a **Betöltés** elemre.

    ![képernyőfelvétel a metaadatok feltöltéséről](./media/looker-analytics-platform-tutorial/metadata.png)

1. Hajtsa végre a következő lépéseket a **felhasználói attribútumok beállításai** szakaszban.

    ![képernyőfelvétel a felhasználói attribútumok beállításairól](./media/looker-analytics-platform-tutorial/user-attribute-settings.png)

    a. Adja hozzá a következő értéket az E-mail attr mezőben: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    b. Adja hozzá a következő értéket a fname attr mezőhöz: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    c. Adja hozzá a következő értéket a Vnév mezőnevet kívánjuk attr mezőhöz: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    d. A **teszt felhasználói hitelesítés** területen kattintson az **SAML-hitelesítés tesztelése** elemre. Ha az oldal, amely betölti a "kiszolgálói válasz sikeresen érvényesítve" kifejezést, sikeresen beállította a példányt az SAML-integrációhoz.
    
    e. A **Mentés és beállítások alkalmazása** területen jelölje be a **fenti konfiguráció megerősítése és a globálisan történő alkalmazásának engedélyezése** jelölőnégyzetet.

    f. Kattintson a **frissítési beállítások** gombra.

### <a name="create-looker-analytics-platform-test-user"></a>Szemlélő analitikai platform tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a szemlélő elemzési platformon. A szemlélő analitikai platform támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a szemlélő elemzési platformon, a rendszer egy újat hoz létre a hitelesítés után.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ez átirányítja a szemlélő elemzési platform bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.  

* Nyissa meg közvetlenül a szemlélő Analytics platform bejelentkezési URL-címét, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a szemlélő elemzési platformra, amelyhez be szeretné állítani az egyszeri bejelentkezést 

A Microsoft Access panel használatával bármilyen módban tesztelheti az alkalmazást. Ha a hozzáférési panelen az "a személyre épülő elemzési platform" csempére kattint, ha az SP módban van konfigurálva, a rendszer átirányítja az alkalmazás bejelentkezési lapjára a bejelentkezési folyamat elindításához, és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie arra a szemlélő elemzési platformra, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Következő lépések

A szemlélő elemzési platform konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).