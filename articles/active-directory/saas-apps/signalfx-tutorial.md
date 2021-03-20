---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a SignalFx | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és SignalFx között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/24/2020
ms.author: jeedes
ms.openlocfilehash: 1fbc42864761360d252ed62cea1aef6f2937b599
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92516069"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-signalfx"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a SignalFx

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a SignalFx a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az SignalFx-t az Azure AD-vel, a következőket teheti:

* A SignalFx-hez hozzáférő Azure AD-beli vezérlés
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a SignalFx az Azure AD-fiókjával. és
* A fiókokat egy helyen kezelheti (a Azure Portal).

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directory](../manage-apps/what-is-single-sign-on.md)használatával című témakört.

## <a name="prerequisites"></a>Előfeltételek

A Kezdés előtt a következőkre lesz szüksége:

* Azure AD-előfizetés
    * Ha nem rendelkezik előfizetéssel, [itt kérhet ingyenes fiókot](https://azure.microsoft.com/free/).
* SignalFx egyszeri bejelentkezéses (SSO) engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli környezetekben végezheti el.

* A SignalFx támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést
* A SignalFx **csak időben támogatja a** felhasználók kiépítési folyamatát
* A SignalFx konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).

## <a name="step-1-add-the-signalfx-application-in-azure"></a>1. lépés: a SignalFx alkalmazás hozzáadása az Azure-ban

A SignalFx-alkalmazás a felügyelt SaaS-alkalmazások listájához való hozzáadásához kövesse az alábbi utasításokat.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com).
1. A bal oldali navigációs ablakban válassza a **Azure Active Directory** lehetőséget.
1. Válassza a **vállalati alkalmazások** lehetőséget, majd válassza **a minden alkalmazás** lehetőséget.
1. Válassza az **Új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban a keresőmezőbe írja be a kifejezést, és válassza a **SignalFx** lehetőséget.
     * Előfordulhat, hogy néhány percet várnia kell, amíg az alkalmazás hozzá lesz adva a bérlőhöz.
1. Hagyja nyitva a Azure Portal, majd nyisson meg egy új weblapot.    

## <a name="step-2-begin-signalfx-sso-configuration"></a>2. lépés: a SignalFx SSO konfigurációjának megkezdése

Az alábbi utasítások segítségével megkezdheti a SignalFx SSO konfigurációs folyamatát.

1. Az újonnan megnyitott lapon nyissa meg a SignalFx felhasználói felületét, és jelentkezzen be. 
1. A felső menüben kattintson az **integrációk** elemre. 
1. A keresés mezőben adja meg és válassza ki a **Azure Active Directory**.
1. Kattintson az **új integráció létrehozása** elemre.
1. A **név** mezőben adjon meg egy könnyen felismerhető nevet, amelyet a felhasználók meg fognak érteni.
1. Megjelölés **megjelenítése a bejelentkezési oldalon**.
    * Ez a funkció egy testreszabott gombot jelenít meg a bejelentkezési oldalon, amelyre a felhasználók rákattintanak. 
    * A **névben** megadott adatok megjelennek a gombon. Ennek eredményeképpen adja meg a felhasználók által felismert **nevet** . 
    * Ez a beállítás csak akkor működik, ha egyéni altartományt használ a SignalFx alkalmazáshoz, például **yourcompanyname.signalfx.com**. Egyéni altartomány beszerzéséhez forduljon a SignalFx támogatási szolgálatához. 
1. Másolja az **integrációs azonosítót**. Ezt az információt egy későbbi lépésben kell megadnia. 
1. Hagyja nyitva a SignalFx felhasználói felületét. 

## <a name="step-3-configure-azure-ad-sso"></a>3. lépés: az Azure AD SSO konfigurálása

Ezekkel az utasításokkal engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. Térjen vissza a [Azure Portal](https://portal.azure.com/), és a **SignalFx** alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt, majd válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** toll (Szerkesztés) ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon végezze el a következő mezőket: 

    a. Az **azonosító** mezőben adja meg a következő URL-címet, `https://api.<realm>.signalfx.com/v1/saml/metadata` és cserélje le a `<realm>` SignalFx-tartományra. 

    b. A **Válasz URL-címe** mezőben adja meg a következő URL-címet, `https://api.<realm>.signalfx.com/v1/saml/acs/<integration ID>` és cserélje le a `<realm>` SignalFx-tartományra, valamint `<integration ID>` a korábban a SignalFx felhasználói felületéről másolt **integrációs azonosítóra** .

1. A SignalFx alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. 
    
1. Tekintse át és ellenőrizze, hogy a következő jogcímek leképezik-e a Active Directoryban kitöltött forrás-attribútumokat. 

    | Name |  Forrás attribútum|
    | ------------------- | -------------------- |
    | Felhasználó. FirstName  | User. givenName |
    | User.email  | User. mail |
    | PersonImmutableID       | User. userPrincipalName    |
    | User. LastName       | felhasználó. vezetéknév    |

    > [!NOTE]
    > Ehhez a folyamathoz az szükséges, hogy a Active Directory legalább egy ellenőrzött egyéni tartománnyal legyen konfigurálva, valamint hogy hozzáférhessen a tartományhoz tartozó e-mail-fiókokhoz. Ha nem biztos abban, hogy ezzel a konfigurációval kapcsolatban segítségre van szüksége, forduljon a SignalFx támogatási szolgálatához.  

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd válassza a **Letöltés** lehetőséget. Töltse le a tanúsítványt, és mentse a számítógépre. Ezután másolja az **alkalmazás-összevonási metaadatok URL-címét** . ezekre az információkra szüksége lesz a SignalFx felhasználói felületének egy későbbi lépésében. 

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **SignalFx beállítása** szakaszban másolja az **Azure ad-azonosító** értékét. Ezekre az információkra szüksége lesz a SignalFx felhasználói felületének egy későbbi lépésében. 

## <a name="step-4-create-an-azure-ad-test-user"></a>4. lépés: Azure AD-tesztkörnyezet létrehozása

Ezekkel az utasításokkal létrehozhat egy teszt felhasználót a **B. Simon** nevű Azure Portalban.

1. A Azure Portal a bal oldali navigációs ablakban válassza a **Azure Active Directory** lehetőséget, majd válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.
1. Az oldal tetején válassza az **új felhasználó** lehetőséget.
1. A **felhasználó** tulajdonságaiban:
   1. A **Felhasználónév** mezőbe írja be a (z `username@companydomain.extension` ) nevet, például: `b.simon@contoso.com` .
   1. A **név** mezőben adja meg a nevet `B.Simon` .
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd másolja a megjelenő értéket a **jelszó** mezőbe. Az integráció teszteléséhez szüksége lesz erre az információra a későbbi lépésben. 
   1. Kattintson a **Létrehozás** lehetőségre.

## <a name="step-5-assign-the-azure-ad-test-user"></a>5. lépés: az Azure AD-teszt felhasználójának kiosztása

Ezekkel az utasításokkal engedélyezheti a felhasználók számára az Azure egyszeri bejelentkezés használatát a SignalFx.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **SignalFx** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, majd válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanel **felhasználók** listájában válassza a **B. Simon** lehetőséget, majd az oldal alján kattintson a **kiválasztás** elemre.
1. Ha a SAML-kijelentésben bármilyen szerepkört vár, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson az oldal alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** elemre.

## <a name="step-6-complete-the-signalfx-sso-configuration"></a>6. lépés: a SignalFx SSO-konfigurációjának befejezése 

1. Nyissa meg az előző fület, és térjen vissza az SignalFx felhasználói felületéhez az aktuális Azure Active Directory integrációs oldal megtekintéséhez. 
1. A **tanúsítvány (Base64)** mellett kattintson a **fájl feltöltése** elemre, majd keresse meg a korábban letöltött **Base64-kódolású tanúsítványfájl** Azure Portal.
1. Az **Azure ad-azonosító** mellett illessze be a korábban a Azure Portalból másolt **Azure ad-azonosító** értékét. 
1. Az **összevonási metaadatok URL-címe** mellett illessze be az **alkalmazás-összevonási metaadatok URL-címét** , amelyet korábban a Azure Portal másolt. 
1. Kattintson a **Mentés** gombra.

## <a name="step-7-test-sso"></a>7. lépés: egyszeri bejelentkezés tesztelése

Tekintse át az alábbi információkat az egyszeri bejelentkezés tesztelésével kapcsolatban, valamint a SignalFx-be való bejelentkezésre vonatkozó elvárásokat. 

### <a name="test-logins"></a>Bejelentkezések tesztelése

* A bejelentkezés teszteléséhez használjon privát/inkognitóban-ablakot, vagy kijelentkezhet a Azure Portalból. Ha nem, akkor az alkalmazást konfiguráló felhasználó cookie-jai megzavarják és meggátolják a sikeres bejelentkezést a tesztelési felhasználóval.

* Amikor első alkalommal jelentkezik be új tesztelési felhasználó, az Azure kényszeríti a jelszó módosítását. Ebben az esetben az SSO bejelentkezési folyamat nem lesz végrehajtva; a teszt felhasználó a Azure Portal lesz átirányítva. A hibakereséshez a felhasználónak meg kell változtatnia a jelszavát, majd navigáljon a SignalFx-bejelentkezési oldalra vagy a hozzáférési panelre, és próbálkozzon újra.
    * Ha a hozzáférési panelen a SignalFx csempére kattint, automatikusan be kell jelentkeznie a SignalFx. 
        * További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

* A SignalFx alkalmazás a hozzáférési panelen vagy a szervezethez rendelt egyéni bejelentkezési oldalon keresztül érhető el. A tesztelési felhasználónak ezen helytől kezdve kell tesztelni az integrációt.
    * A tesztelési felhasználó használhatja a folyamat során korábban létrehozott hitelesítő adatokat **b. simon \@ contoso.com**.

### <a name="first-time-logins"></a>Első bejelentkezések

* Amikor a felhasználó első alkalommal jelentkezik be a SignalFx-be az SAML SSO-ba, a felhasználó egy SignalFx e-mailt fog kapni egy hivatkozással. A felhasználónak a hitelesítési célokra vonatkozó hivatkozásra kell kattintania. Ez az e-mail-ellenőrzés csak az első felhasználó számára lesz érvényben. 

* A SignalFx **támogatja az** igény szerinti felhasználói létrehozását, ami azt jelenti, hogy ha a felhasználó nem létezik a SignalFx-ben, akkor a felhasználó fiókját az első bejelentkezési kísérlet után hozza létre a rendszer.

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)

- [A SignalFx kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)