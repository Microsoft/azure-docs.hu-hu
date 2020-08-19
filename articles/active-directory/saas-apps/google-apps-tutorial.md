---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Google Cloud (G Suite) összekötővel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Google Cloud (G Suite) összekötő között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: jeedes
ms.openlocfilehash: 5ff34b967e2ba42c01c904b8854ecfa04c394e17
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551521"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-google-cloud-g-suite-connector"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Google Cloud (G Suite) összekötővel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Google Cloud (G Suite) összekötőt Azure Active Directory (Azure AD) használatával. Ha integrálja a Google Cloud (G Suite) összekötőt az Azure AD-val, a következőket teheti:

* A Google Cloud (G Suite) összekötőhöz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Google Cloud (G Suite) Összekötőbe az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

- Egy Azure AD-előfizetés.
- Google Cloud (G Suite) összekötő egyszeri bejelentkezés (SSO) engedélyezve előfizetés.
- Google Apps-előfizetés vagy Google Cloud Platform-előfizetés.

> [!NOTE]
> Az oktatóanyag lépéseinek teszteléséhez nem javasoljuk éles környezet használatát. Ez a dokumentum az új felhasználói egyszeri bejelentkezés használatával lett létrehozva. Ha továbbra is a régit használja, a telepítő eltérő lesz. A G-Suite alkalmazás egyszeri bejelentkezés beállításaiban engedélyezheti az új felhasználói élményt. Nyissa meg az **Azure ad-t, a vállalati alkalmazásokat**, válassza a **Google Cloud (G Suite) összekötőt**, válassza az **egyszeri bejelentkezés** lehetőséget, majd kattintson az **új felhasználói élmény kipróbálása**lehetőségre.

Az oktatóanyag lépéseinek teszteléséhez kövesse az alábbi javaslatokat:

- Ne használja éles környezetét, ha szükséges.
- Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

1. **K: az integráció támogatja az Azure AD-vel való Google Cloud Platform SSO-integrációt?**

    V: Igen. A Google Cloud Platform és a Google Apps ugyanazt a hitelesítési platformot használja. Így a GCP-integrációhoz konfigurálnia kell az egyszeri bejelentkezést a Google Apps szolgáltatásban.

2. **K: az Azure AD egyszeri bejelentkezéssel kompatibilis Chromebook és más Chrome-eszközök?**
  
    Válasz: igen, a felhasználók Azure AD-beli hitelesítő adataikkal jelentkezhetnek be a Chromebook-eszközökre. A [Google Cloud (G Suite)-összekötő támogatási cikkében](https://support.google.com/chrome/a/answer/6060880) talál információt arról, hogy a felhasználók miért kaphatják meg kétszer a hitelesítő adatok megadását.

3. **K: ha engedélyezem az egyszeri bejelentkezést, a felhasználók Azure AD-beli hitelesítő adataikkal jelentkezhetnek be bármely Google-termékbe, például a Google Tanteremba, a GMailbe, a Google Drive-ba, a YouTube-ra stb.**

    Válasz: igen, attól függően, hogy a [Google Cloud (G Suite) összekötője](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) hogyan engedélyezheti vagy tilthatja le a szervezetét.

4. **K: engedélyezhető az egyszeri bejelentkezés a Google Cloud (G Suite) Connector-felhasználók csak egy részhalmaza számára?**

    A: nem, az egyszeri bejelentkezés azonnali bekapcsolása esetén a Google Cloud (G Suite) összekötő felhasználóinak azonnal be kell jelentkezniük az Azure AD-beli hitelesítő adataikkal. Mivel a Google Cloud (G Suite) összekötő nem támogatja több identitás-szolgáltató használatát, a Google Cloud (G Suite) összekötő-környezete Azure AD-vagy Google--, de nem egyszerre is használható.

5. **K: Ha a felhasználó a Windowson keresztül jelentkezik be, a rendszer automatikusan hitelesíti a Google Cloud (G Suite) összekötőt a jelszó kérése nélkül?**

    A: két lehetőség van ennek a forgatókönyvnek a engedélyezésére. Először is a felhasználók bejelentkezhetnek a Windows 10-es eszközökre [Azure Active Directory JOIN](../device-management-introduction.md)használatával. Azt is megteheti, hogy a felhasználók bejelentkezhetnek olyan Windows-eszközökre, amelyek tartományhoz csatlakoznak egy olyan helyszíni Active Directoryhoz, amely engedélyezve van az Azure AD-be való egyszeri bejelentkezésre egy [Active Directory összevonási szolgáltatások (AD FS) (AD FS)](../hybrid/plan-connect-user-signin.md) üzemelő példányon keresztül. Mindkét beállításhoz el kell végeznie az alábbi oktatóanyag lépéseit, hogy engedélyezze az egyszeri bejelentkezést az Azure AD és a Google Cloud (G Suite) összekötő között.

6. **K: mit tegyek, ha "érvénytelen e-mail" hibaüzenet jelenik meg?**

    A: ehhez a beállításhoz az e-mail-attribútum szükséges ahhoz, hogy a felhasználók be tudják jelentkezni. Ez az attribútum nem állítható be manuálisan.

    Az e-mail-attribútum minden érvényes Exchange-licenccel rendelkező felhasználó számára automatikusan fel van töltve. Ha a felhasználó nem rendelkezik e-mail-üzenettel, akkor a rendszer ezt a hibaüzenetet kapja, mivel az alkalmazásnak hozzáférést kell adnia ehhez az attribútumhoz.

    A portal.office.com rendszergazdai fiókkal, majd a felügyeleti központban, a számlázásban, az előfizetések területen válassza ki az Office 365-előfizetést, majd kattintson a hozzárendelés felhasználókhoz lehetőségre, válassza ki azokat a felhasználókat, akikkel ellenőriznie szeretné az előfizetését, és a jobb oldali ablaktáblában kattintson a licencek szerkesztése lehetőségre.

    A O365-licenc hozzárendelése után néhány percet is igénybe vehet. Ezt követően a User. mail attribútum automatikusan ki lesz töltve, és a problémát fel kell oldani.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Google Cloud (G Suite)-összekötő támogatja az **SP** által kezdeményezett egyszeri bejelentkezést

* A Google Cloud (G Suite) összekötő támogatja a [felhasználók **automatikus** kiépítési felállítását](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)
* Miután konfigurálta a Google Cloud (G Suite) összekötőt, kikényszerítheti a munkamenet-vezérlést, amely valós időben biztosítja a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlő kiterjeszthető a feltételes hozzáférésből. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-google-cloud-g-suite-connector-from-the-gallery"></a>Google Cloud (G Suite) összekötő hozzáadása a katalógusból

A Google Cloud (G Suite)-összekötő Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Google Cloud (G Suite) összekötőt a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Google Cloud (G Suite) összekötőt** a keresőmezőbe.
1. Válassza ki a **Google Cloud (G Suite) összekötőt** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-google-cloud-g-suite-connector"></a>Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Google Cloud (G Suite) összekötőhöz

Konfigurálja és tesztelje az Azure AD SSO-t a Google Cloud (G Suite) összekötővel egy **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Google Cloud (G Suite) összekötőben.

Az Azure AD SSO és a Google Cloud (G Suite) összekötő konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[Google Cloud (G Suite) összekötő egyszeri bejelentkezésének konfigurálása](#configure-google-cloud-g-suite-connector-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozza létre a Google Cloud (g Suite)-összekötő tesztelési felhasználóját](#create-google-cloud-g-suite-connector-test-user)** , hogy a felhasználó Azure ad-képviseletéhez kapcsolódó, a Google Cloud (g Suite) összekötővel rendelkező B. Simon partnere legyen.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **Google Cloud (G Suite) Connector** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban, ha a **Gmail** konfigurálását szeretné elvégezni, hajtsa végre a következő lépéseket:

    a. A **bejelentkezési URL** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával:

    ```http
    google.com/a/<yourdomain.com>
    google.com
    https://google.com
    https://google.com/a/<yourdomain.com>
    ```

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: 

    ```http
    https://www.google.com
    https://www.google.com/a/<yourdomain.com>
    ```

1. Ha az **alapszintű SAML-konfiguráció** szakaszban szeretné konfigurálni a **Google Cloud platform** , hajtsa végre a következő lépéseket:

    a. A **bejelentkezési URL** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com`

    b. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával:
    
    ```http
    google.com/a/<yourdomain.com>
    google.com
    https://google.com
    https://google.com/a/<yourdomain.com>
    ```
    
    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: 
    
    ```http
    https://www.google.com
    https://www.google.com/a/<yourdomain.com>
    ```

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. A Google Cloud (G Suite) összekötő nem ad meg entitás-azonosító/azonosító értéket az egyszeri bejelentkezés konfigurációjában, így ha törli a **tartományi specifikus kiállítói** beállítást, az azonosító értéke a következő lesz: `google.com` . Ha bejelöli a **tartományra jellemző kiállítói** lehetőséget, az lesz `google.com/a/<yourdomainname.com>` . A **tartomány-specifikus kiállítói** beállítás bejelöléséhez vagy a kijelölés kikapcsolásához lépjen a **Google Cloud (G Suite)-összekötő egyszeri bejelentkezésre szolgáló SSO** szakaszra, amelyet az oktatóanyag későbbi részében ismertet. További információért forduljon a [Google Cloud (G Suite) Connector ügyfél-támogatási csapatához](https://www.google.com/contact/).

1. A Google Cloud (G Suite) Connector-alkalmazás megadott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. A következő képernyőképen egy példa látható. Az **egyedi felhasználói azonosító** alapértelmezett értéke a **User. userPrincipalName** , de a Google Cloud (G Suite) összekötő arra vár, hogy a felhasználó e-mail-címével legyen leképezve. Ehhez használhatja a **User. mail** attribútumot a listából, vagy használhatja a megfelelő attribútumot a szervezeti konfiguráció alapján.

    ![image](common/default-attributes.png)


1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Google Cloud (G Suite)-összekötő beállítása** szakaszban másolja ki a megfelelő URL-címet (ka) t a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Google Cloud (G Suite) összekötőhöz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **Google Cloud (G Suite) összekötőt**.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-google-cloud-g-suite-connector-sso"></a>A Google Cloud (G Suite) összekötő egyszeri bejelentkezésének konfigurálása

1. Nyisson meg egy új fület a böngészőben, és jelentkezzen be a [Google Cloud (G Suite) Connector felügyeleti konzolra](https://admin.google.com/) a rendszergazdai fiókjával.

2. Kattintson a **Biztonság**elemre. Ha nem látja a hivatkozást, akkor előfordulhat, hogy a képernyő alján található **további vezérlők** menü alatt rejtve van.

    ![Kattintson a Biztonság elemre.][10]

3. A **Biztonság** lapon kattintson az **egyszeri bejelentkezés beállítása (SSO)** lehetőségre.

    ![Kattintson az SSO elemre.][11]

4. Hajtsa végre a következő konfigurációs módosításokat:

    ![Egyszeri bejelentkezés konfigurálása][12]

    a. Válassza **a beállítás egyszeri bejelentkezés külső szolgáltatótól**lehetőséget.

    b. A Google Cloud (G Suite) összekötő **bejelentkezési oldal URL-címe** mezőjébe illessze be azt a **bejelentkezési URL-címet** , amelyet a Azure Portalból másolt.

    c. A Google Cloud (G Suite)-összekötő kijelentkezési **oldal URL-címe** mezőjébe illessze be a **KIJELENTKEZÉSI URL-cím** értékét, amelyet a Azure Portal másolt.

    d. A Google Cloud (G Suite) összekötő **jelszó módosítása URL-címe** mezőjébe illessze be a **jelszó módosítása URL-cím módosítását** , amelyet a Azure Portal másolt.

    e. A Google Cloud (G Suite) összekötőben az **ellenőrző tanúsítványhoz**töltse fel a Azure Portalról letöltött tanúsítványt.

    f. Jelölje be/törölje a **tartományi specifikus kiállítói lehetőség használata** lehetőséget az Azure ad fenti **alapszintű SAML-konfiguráció** szakaszában említett Megjegyzés alapján.

    : Kattintson a **Save Changes** (Módosítások mentése) gombra.

### <a name="create-google-cloud-g-suite-connector-test-user"></a>Google Cloud (G Suite) összekötő tesztelési felhasználó létrehozása

Ennek a szakasznak a célja [egy olyan felhasználó létrehozása a Google Cloud (G Suite) összekötőn](https://support.google.com/a/answer/33310?hl=en) , amelynek neve B. Simon. Miután a felhasználó manuálisan létrejött a Google Cloud (G Suite)-összekötőben, a felhasználó már bejelentkezhet az Office 365 bejelentkezési hitelesítő adataival.

A Google Cloud (G Suite) összekötő a felhasználók automatikus üzembe helyezését is támogatja. Az automatikus felhasználó-kiépítés konfigurálásához először [konfigurálnia kell a Google Cloud (G Suite) összekötőt a felhasználók automatikus kiépítési felállításához](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial).

> [!NOTE]
> Győződjön meg arról, hogy a felhasználó már létezik a Google Cloud (G Suite) összekötőben, ha az Azure AD-ben való kiépítés nincs bekapcsolva az egyszeri bejelentkezés tesztelése előtt.

> [!NOTE]
> Ha manuálisan kell létrehoznia egy felhasználót, lépjen kapcsolatba a [Google támogatási csoportjával](https://www.google.com/contact/).

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Google Cloud (G Suite) összekötő csempére kattint, automatikusan be kell jelentkeznie a Google Cloud (G Suite) Összekötőbe, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A felhasználók üzembe helyezésének konfigurálása](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)

- [Próbálja ki a Google Cloud (G Suite)-összekötőt az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [A Google Cloud (G Suite) összekötő speciális láthatósággal és vezérlőkkel való ellátása](https://docs.microsoft.com/cloud-app-security/protect-gsuite)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png