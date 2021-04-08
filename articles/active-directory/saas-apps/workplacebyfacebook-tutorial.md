---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a munkahelyen Facebook használatával | Microsoft Docs'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory és a munkahely között a Facebook használatával.
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
ms.openlocfilehash: 3f66da38d3303b47c2a9b6cefeee19af6bf64ec1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98725507"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workplace-by-facebook"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a munkahelyen Facebook használatával

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a munkahelyeket Facebook-ba a Azure Active Directory (Azure AD) használatával. A munkahelyi Facebook és az Azure AD integrálásával a következőket teheti:

* Az Azure AD-ben elérhető, a Facebook által a munkahelyhez hozzáférő vezérlő.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a munkahelyre a Facebookban az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.


## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A munkahelyi Facebook egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

> [!NOTE]
> A Facebook két termékkel rendelkezik, a munkahelyi standard (ingyenes) és a munkahelyi prémium (fizetős). A munkahelyhez tartozó prémium bérlők a SCIM és az SSO-integrációt más, a szükséges díjakra vagy licencekre vonatkozó tényezők nélkül is konfigurálhatják. Az SSO és a SCIM nem érhető el a munkahelyi standard példányokban.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A munkahelyi Facebook által támogatott, **SP** által kezdeményezett egyszeri bejelentkezés
* A munkahelyen a Facebook az **igény szerinti üzembe** helyezést támogatja
* A munkahelyi Facebook által támogatott **[automatikus felhasználó-kiépítés](workplacebyfacebook-provisioning-tutorial.md)**
* A Facebook Mobile alkalmazás munkahelye mostantól konfigurálható az Azure AD-vel az egyszeri bejelentkezés engedélyezéséhez. Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.


## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Munkahelyi felvétel a Facebook használatával a katalógusból

A munkahelyi Facebook és az Azure AD közötti integráció konfigurálásához a Facebookban fel kell vennie a munkaterületet a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **munkahely a Facebookon** kifejezést a keresőmezőbe.
1. Válassza a **munkahely a facebookban** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-workplace-by-facebook"></a>Az Azure AD SSO konfigurálása és tesztelése a munkahelyen Facebook használatával

Konfigurálja és tesztelje az Azure AD SSO-t a munkahelyen a Facebook használatával egy **B. Simon** nevű teszt felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a munkahelyi felhasználó között a Facebook használatával.

Az Azure AD SSO és a munkahely a Facebook használatával történő konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
2. A **[munkahelyi beállítások konfigurálása a Facebook SSO](#configure-workplace-by-facebook-sso)** -vel – az alkalmazás oldalának egyetlen Sign-On beállításainak konfigurálása.
    1. **[Munkahelyi munkahely létrehozása Facebook-teszttel](#create-workplace-by-facebook-test-user)** – a felhasználó által a munkahelyi Azure ad-képviselethez kapcsolódó B. Simon-partnernek.
3. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **munkahelyi Facebook** -alkalmazás integrációja lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeres Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** ceruza ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL** -cím (a munkahelyen a címzett URL-címe) szövegmezőbe írja be az URL-címet a következő minta használatával: `https://.workplace.com/work/saml.php`

    b. Az **azonosító (entitás azonosítója)** (a munkahelyen a célközönség URL-címe) szövegmezőbe írja be az URL-címet a következő minta használatával: `https://www.workplace.com/company/`

    c. A **Válasz URL-címe** (a munkahelyen az állítási fogyasztói szolgáltatás) szövegmezőbe írja be az URL-címet a következő minta használatával: `https://.workplace.com/work/saml.php`

    > [!NOTE]
    > Ezek az értékek nem valódiak. Frissítse ezeket az értékeket a tényleges Sign-On URL-címmel, azonosítóval és válasz URL-címmel. Tekintse meg a munkahelyi Közösség megfelelő értékeit a munkahelyi vállalati irányítópult hitelesítés lapján, amelyet az oktatóanyag későbbi részében ismertet.

1. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **munkahely beállítása Facebook használatával** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést a munkahelyi Facebook-hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **munkahely a Facebook alapján** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-workplace-by-facebook-sso"></a>Munkahelyi konfiguráció konfigurálása a Facebook SSO használatával

1. Ha a Facebookon belül szeretné automatizálni a konfigurációt, telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

1. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **munkahely beállítása a facebookban** lehetőségre a Facebook-alkalmazás által a munkahelyre irányítva. Itt adja meg a rendszergazdai hitelesítő adatokat, hogy a Facebook bejelentkezzen a munkahelyre. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-5-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

1. Ha manuálisan szeretné beállítani a munkahelyet a Facebook használatával, nyisson meg egy új böngészőablakot, és jelentkezzen be a munkahelyre a Facebook vállalati webhelyén rendszergazdaként, és hajtsa végre a következő lépéseket:

    > [!NOTE]
    > Az SAML hitelesítési folyamat részeként a munkahely akár 2,5 kilobájtos lekérdezési karakterláncot is használhat a paraméterek Azure AD-ba való továbbításához.

1. A bal oldali navigációs panelen navigáljon a **biztonsági**  >  **hitelesítés** lapra.

    ![Felügyeleti panel](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure01.png)

    a. Keresse **meg az egyszeri bejelentkezés (SSO)** beállítást.
    
    b. Kattintson az **+ új SSO-szolgáltató hozzáadása** lehetőségre.
    > [!NOTE]
    > Győződjön meg arról, hogy a jelszó-bejelentkezési jelölőnégyzet is be van jelölve. Előfordulhat, hogy a rendszergazdáknak be kell jelentkezniük a bejelentkezés során a tanúsítvány átváltásához, hogy le tudják zárni magukat.

1. A **hitelesítés** lapon válassza az **egyszeri bejelentkezés (SSO)** lehetőséget, és hajtsa végre a következő lépéseket:

    ![Hitelesítés lap](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure02.png)

    a. Az **SSO-szolgáltató neve** mezőben adja meg az SSO-példány nevét (például Azureadsso).

    b. Az **SAML URL-címe** szövegmezőbe illessze be a **bejelentkezési URL-cím** értékét, amelyet a Azure Portalból másolt.

    c. Az **SAML kiállító URL-címe** szövegmezőben illessze be az **Azure ad-azonosító** értékét, amelyet a Azure Portalból másolt.

    d. Nyissa meg az **alapszintű 64-kódolású tanúsítványt** a jegyzettömbben, Azure Portal letöltve, másolja ki a tartalmat a vágólapra, majd illessze be az **SAML-tanúsítvány** szövegmezőbe.

    e. Másolja a példány **célközönségének URL-címét** , és illessze be az **azonosító (Entity ID)** szövegmezőbe a Azure Portal **alapszintű SAML-konfiguráció** szakaszában.

    f. Másolja a példányhoz tartozó **címzett URL-címet** , és illessze be a **bejelentkezési URL-cím** szövegmezőbe az **alapszintű SAML-konfiguráció** szakaszának Azure Portalján.

    : Másolja ki a példányhoz tartozó **ACS-URL-címet** , és illessze be a **Válasz URL** -szövegmezőbe a Azure Portal **alapszintű SAML-konfiguráció** szakaszában.

    h. Görgessen a szakasz aljára, és kattintson az **egyszeri bejelentkezés tesztelése** gombra. Ennek eredményeként megjelenik egy előugró ablak, amely az Azure AD bejelentkezési oldalán jelenik meg. A hitelesítéshez adja meg a hitelesítő adatait a szokásos módon.

    **Hibaelhárítás:** Győződjön meg arról, hogy az Azure AD-ből visszaadott e-mail-cím megegyezik azzal a munkahelyi fiókkal, amelybe bejelentkezett.

    i. Miután a teszt sikeresen befejeződött, görgessen a lap aljára, és kattintson a Save ( **Mentés** ) gombra.

    j. A munkahelyen használó összes felhasználó most megjelenik az Azure AD bejelentkezési oldala a hitelesítéshez.

1. **SAML-kijelentkezés átirányítása (nem kötelező)** -

    Dönthet úgy, hogy opcionálisan konfigurál egy SAML kijelentkezési URL-címet, amely az Azure AD kijelentkezési oldalán is használható. Ha ez a beállítás engedélyezve és konfigurálva van, a felhasználó nem lesz átirányítva a munkahelyi kijelentkezés lapra. Ehelyett a rendszer átirányítja a felhasználót az SAML kijelentkezési átirányítási beállításban hozzáadott URL-címre.

### <a name="configuring-reauthentication-frequency"></a>Az újrahitelesítés gyakoriságának konfigurálása

A munkahelyet beállíthatja úgy, hogy minden nap, három nap, hét, két hét, hónap vagy soha ne Kérdezzen rá SAML-vizsgálatra.

> [!NOTE]
> A mobileszközök SAML-vizsgálatának minimális értéke egy hétig van beállítva.

Az SAML-visszaállítást az összes felhasználó számára is kényszerítheti a következő gomb használatával: SAML-hitelesítés megkövetelése minden felhasználó számára.

### <a name="create-workplace-by-facebook-test-user"></a>Munkahely létrehozása Facebook-teszt felhasználó által

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre a munkahelyen a Facebookban. A munkahelyen a Facebook az igény szerinti üzembe helyezést támogatja, ami alapértelmezés szerint engedélyezve van.

Ebben a szakaszban nincs művelet. Ha a Facebookban nem szerepel egy felhasználó a munkahelyen, a rendszer létrehoz egy újat, amikor megpróbál hozzáférni a munkahelyi Facebook-hoz.

>[!Note]
>Ha manuálisan kell létrehoznia egy felhasználót, lépjen kapcsolatba a munkahelyi kapcsolattal a Facebook ügyfélszolgálati [csapatával](https://www.workplace.com/help/work/).

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ez átirányítja a munkahelyre a Facebook bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen a munkahelyi Facebook bejelentkezési URL-címére közvetlenül a munkahelyre, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Ha a saját alkalmazások Facebook-csempéje alapján rákattint a munkahelyre, a rendszer átirányítja a munkahelyi Facebook bejelentkezési URL-címére. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="test-sso-for-workplace-by-facebook-mobile"></a>A munkahelyi egyszeri bejelentkezés tesztelése a Facebookban (mobil)

1. Nyissa meg a munkahelyet Facebook Mobile-alkalmazással. A bejelentkezés lapon kattintson a **Bejelentkezés** elemre.

    ![A bejelentkezés](./media/workplacebyfacebook-tutorial/test05.png)

2. Adja meg az üzleti levelezését, és kattintson a **Folytatás** gombra.

    ![Az e-mail cím](./media/workplacebyfacebook-tutorial/test02.png)

3. Kattintson **egyszer csak egyszer**.

    ![Egyszer](./media/workplacebyfacebook-tutorial/test04.png)

4. Kattintson az **Engedélyezés** gombra.

    ![Az engedélyezés](./media/workplacebyfacebook-tutorial/test03.png)

5. Végül a sikeres bejelentkezés után megjelenik az alkalmazás kezdőlapja.    

    ![A Kezdőlap](./media/workplacebyfacebook-tutorial/test01.png)

## <a name="next-steps"></a>Következő lépések

Ha a munkahelyet a Facebook használatával konfigurálta, akkor kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)