---
title: 'Oktatóanyag: Azure AD SSO-integráció a Trend Micro Web Security (TMWS) szolgáltatással'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a Trend Micro Web Security (TMWS) között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/21/2020
ms.author: jeedes
ms.openlocfilehash: b76c41787d7a35fb3024fa18c0122bc966243bbc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96008395"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trend-micro-web-security-tmws"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Trend Micro Web Security (TMWS) szolgáltatással

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Trend Micro Web Security (TMWS) Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az TMWS-t az Azure AD-vel, a következőket teheti:

* A TMWS-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a TMWS az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg az [egyszeri bejelentkezést a Azure Active Directory alkalmazásaihoz](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következők szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* TMWS-előfizetés, amely engedélyezve van az egyszeri bejelentkezéshez.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését tesztkörnyezetben végezheti el.

* A TMWS támogatja az SP által kezdeményezett egyszeri bejelentkezést.
* A TMWS konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. Ha meg szeretné tudni, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security használatával, tekintse meg a feltételes hozzáférést biztosító alkalmazás-vezérlő bevezetését [és az alkalmazások üzembe helyezését](/cloud-app-security/proxy-deployment-any-app)

## <a name="add-tmws-from-the-gallery"></a>TMWS hozzáadása a gyűjteményből

A TMWS Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a TMWS a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiók.
1. A bal oldali ablaktáblán válassza ki a **Azure Active Directory** szolgáltatást.
1. Válassza a **vállalati alkalmazások** lehetőséget, majd válassza **a minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban adja meg a **Trend Micro Web Security (TMWS)** kifejezést a keresőmezőbe.
1. Válassza a **Trend Micro Web Security (TMWS)** lehetőséget a keresési eredmények között, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-tmws"></a>Azure AD SSO konfigurálása és tesztelése a TMWS-hez

Az Azure AD SSO-t a TMWS-mel konfigurálhatja és tesztelheti egy B. Simon nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a TMWS-ben.

Ezeket az alapszintű lépéseket az Azure AD SSO konfigurálásához és a TMWS való teszteléséhez hajtsa végre:

1. [Konfigurálja az Azure ad SSO](#configure-azure-ad-sso) -t, hogy engedélyezze a szolgáltatást a felhasználók számára.
    1. Azure [ad-felhasználó létrehozása](#create-an-azure-ad-test-user) az Azure ad egyszeri bejelentkezés teszteléséhez.
    1. [Adja meg az Azure ad-felhasználó](#grant-the-azure-ad-test-user-access-to-tmws) hozzáférését a TMWS.
    1. [A felhasználók és csoportok szinkronizálási beállításainak konfigurálása az Azure ad-ben](#configure-user-and-group-synchronization-settings-in-azure-ad).
1. [Konfigurálja a TMWS SSO](#configure-tmws-sso) -t az alkalmazás oldalán.
1. Ellenőrizze az [egyszeri bejelentkezést](#test-sso) a konfiguráció ellenőrzéséhez.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

A következő lépésekkel engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **Trend Micro Web Security (TMWS)** alkalmazás-integráció lapján, a **kezelés** szakaszban válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon válassza az **ALAPszintű SAML-konfigurációhoz** tartozó toll gombot a beállítások szerkesztéséhez:

   ![Az alapszintű SAML-konfigurációs beállítások szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg az értékeket a következő mezőkben:

    a. Az **azonosító (entitás azonosítója)** mezőben adja meg az URL-címet a következő mintában:

    `https://auth.iws-hybrid.trendmicro.com/([0-9a-f]{16})`

    b. A **Válasz URL-címe** mezőbe írja be a következő URL-címet:

    `https://auth.iws-hybrid.trendmicro.com/simplesaml/module.php/saml/sp/saml2-acs.php/ics-sp`

    > [!NOTE]
    > Az előző lépésben szereplő azonosító érték nem a megadható érték. A tényleges azonosítót kell használnia. Ezt az értéket az Azure AD-hez készült **hitelesítési módszer** lapon, az **Adminisztráció > Directory Services** **szolgáltatásban elérhető Azure felügyeleti portálon** , a szolgáltatói beállítások részben találja.

1. A TMWS meghatározott formátumban várja az SAML-jogkivonatokat, ezért egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. A képernyőképen az alapértelmezett attribútumok láthatók:

    ![Alapértelmezett attribútumok](common/default-attributes.png)

1. Az előző képernyőképen lévő attribútumok mellett a TMWS két további attribútumot vár az SAML-válaszban. Ezek az attribútumok az alábbi táblázatban láthatók. Az attribútumok előre ki vannak töltve, de megváltoztathatók a követelmények teljesítése érdekében.
    
    | Name | Forrás attribútum|
    | --------------- | --------- |
    | sAMAccountName | User. onpremisessamaccountname |
    | uPN | User. userPrincipalName |

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** című szakaszt. Válassza a tanúsítvány neve melletti **Letöltés** hivatkozást a tanúsítvány letöltéséhez, és mentse a számítógépre:

    ![Tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Trend Micro Web Security (TMWS) beállítása** szakaszban a követelmények alapján másolja a megfelelő URL-címet vagy URL-címeket:

    ![A konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy B. Simon nevű teszt felhasználót hoz létre a Azure Portal.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget. Válassza a **felhasználók** lehetőséget, majd válassza **a minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A név mezőbe írja be a **nevet** `B.Simon` .  
   1. A **Felhasználónév** mezőbe írja be a **_Felhasználónév_ @* cégestartomány *.* bővítmény * * *. Például: `B.Simon@contoso.com`.
   1. Válassza a **jelszó megjelenítése** lehetőséget, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Válassza a **Létrehozás** lehetőséget.

### <a name="grant-the-azure-ad-test-user-access-to-tmws"></a>Az Azure AD-felhasználó hozzáférésének engedélyezése a TMWS

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a TMWS.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Trend Micro Web Security (TMWS)** elemet.
1. Az alkalmazás Áttekintés lapjának **kezelés** szakaszában válassza a **felhasználók és csoportok** lehetőséget:

   ![Felhasználók és csoportok kiválasztása](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![Felhasználó hozzáadása kiválasztása](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza az **B. Simon** elemet a **felhasználók** listán, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban a szerepkör értéke várható, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés** lehetőséget.

### <a name="configure-user-and-group-synchronization-settings-in-azure-ad"></a>Felhasználói és csoport szinkronizálási beállításainak konfigurálása az Azure AD-ben

1. A bal oldali panelen válassza az **Azure Active Directory** lehetőséget.

1. A **kezelés** területen válassza a **Alkalmazásregisztrációk** lehetőséget, majd válassza ki az új vállalati alkalmazást az **összes alkalmazás** területen.

1. A **Kezelés** területen válassza a **Tanúsítványok és titkos kódok** lehetőséget.

1. Az **ügyfél titkai** területen válassza az **új ügyfél titka** elemet.

1. Az **ügyfél titkos kulcsának hozzáadása képernyőn** opcionálisan hozzáadhat egy leírást, és kiválaszthatja az ügyfél titkos kulcsának lejárati idejét, majd válassza a **Hozzáadás** lehetőséget. Az új ügyfél titkos kulcsa az **ügyfél titkai** területén jelenik meg.

1. Jegyezze fel az ügyfél titkos értékét. Később megadhatja a TMWS.

1. A **kezelés** területen válassza az **API-engedélyek** lehetőséget. 

1. Az **API-engedélyek** ablakban válassza az **engedély hozzáadása** elemet.

1. A **kérelem API-engedélyek** ablakának **Microsoft API-jai** lapján válassza a **Microsoft Graph** , majd az **alkalmazás engedélyei** lehetőséget.

1. Keresse meg és adja hozzá a következő engedélyeket: 

    * Group.Read.All
    * Felhasználó. Read. All

1. Válassza az **engedélyek hozzáadása** lehetőséget. Megjelenik egy üzenet, amely megerősíti, hogy a beállítások mentése megtörtént. Az új engedélyek az API- **engedélyek** ablakban jelennek meg.

1. A **jóváhagyás engedélyezése** területen jelölje be **a rendszergazdai jóváhagyás megadása a *rendszergazdai fiókhoz* (alapértelmezett könyvtár)** lehetőséget, majd válassza az **Igen** lehetőséget. Megjelenik egy üzenet, amely megerősíti, hogy a kért engedélyekhez tartozó rendszergazdai jóváhagyás meg lett adva.

1. Válassza az **Áttekintés** lehetőséget. 

1. Jegyezze fel a jobb oldali ablaktáblán látható **alkalmazás (ügyfél) azonosítóját** és **könyvtárát (bérlői azonosítóját)** . Később megadhatja az adatokat a TMWS. **Egyéni tartományneveket** is kijelölhet **Azure Active Directory > kezelheti** és rögzítheti a jobb oldali ablaktáblán látható tartománynevet.

## <a name="configure-tmws-sso"></a>TMWS SSO konfigurálása

A következő lépések végrehajtásával konfigurálhatja a TMWS SSO-t az alkalmazás oldalán.

1. Jelentkezzen be a TMWS felügyeleti konzolba, és lépjen a **felügyeleti**  >  **felhasználók & Authentication**  >  **Directory Services** elemre.

1. Kattintson **ide** a képernyő felső részén.

1. A **hitelesítési módszer** lapon válassza az **Azure ad** lehetőséget.

1. Válassza **a** be vagy **ki** lehetőséget annak konfigurálásához, hogy az Azure ad-felhasználók a szervezeten belül TMWS-e a webhelyeket, ha az adataik nincsenek szinkronizálva a TMWS.

    > [!NOTE]
    > Az Azure AD-ből nem szinkronizált felhasználók csak az ismert TMWS-átjárókkal vagy a szervezet dedikált portjával hitelesíthetők.

1. Az **Identity Provider beállításai** szakaszban hajtsa végre a következő lépéseket:

    a. A **szolgáltatás URL-címe** mezőbe írja be a Azure Portalból másolt **bejelentkezési URL-** értéket.

    b. A **bejelentkezési név attribútum** mezőben adja meg a **felhasználói jogcím nevét** a **User. onpremisessamaccountname** forrás attribútummal a Azure Portal.

    c. A **nyilvános SSL-tanúsítvány** mezőben használja a letöltött **tanúsítványt (Base64)** a Azure Portal.

1. A **szinkronizálási beállítások** szakaszban hajtsa végre a következő lépéseket:

    a. A **bérlő** mezőbe írja be a **könyvtár (bérlő) azonosítóját** vagy az **Egyéni tartománynév** értékét a Azure Portalból.

    b. Az **alkalmazás azonosítója** mezőben adja meg az **alkalmazás (ügyfél) azonosítójának** értékét a Azure Portal.

    c. Az **ügyfél titkos kulcsa** mezőben adja meg az **ügyfél titkos kulcsát** a Azure Portal.

    d. Válassza ki a **szinkronizálási ütemtervet** , hogy az Azure ad-val manuálisan vagy egy ütemterv szerint szinkronizáljon. Ha a **manuálisan** lehetőséget választja, valahányszor Active Directory felhasználói adatokat módosítanak, ne feledje, hogy térjen vissza a **Címtárszolgáltatások** lapra, és végezze el a manuális szinkronizálást, hogy a TMWS lévő információk továbbra is naprakészek maradjanak.

    e. Kattintson a **kapcsolat tesztelése** elemre annak ellenőrzéséhez, hogy az Azure ad szolgáltatás sikeresen csatlakoztatva van-e.
    
    f. Kattintson a **Mentés** gombra.
 
 > [!NOTE]
 > A TMWS Azure AD-vel való konfigurálásával kapcsolatos további információkért lásd: [Az Azure ad-beállítások konfigurálása a TMWS-on](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/directory-services/azure-active-directo/configuring-azure-ad.aspx).

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Miután konfigurálta az Azure AD szolgáltatást, és megadta a felhasználói hitelesítési módszerként az Azure AD-t, bejelentkezhet a TMWS a telepítés ellenőrzéséhez. Miután az Azure AD-bejelentkezés ellenőrzi a fiókját, megkeresheti az internetet.

> [!NOTE]
> A TMWS nem támogatja az egyszeri bejelentkezés tesztelését az Azure ad-portálról, az **Áttekintés**  >  **egyszeri bejelentkezés**  >  **beállításával egyszeri bejelentkezést állíthat be** az  >  új vállalati alkalmazás SAML-**tesztelésével** .

1. Törölje az összes cookie böngészőjét, majd indítsa újra a böngészőt. 

1. Irányítsa a böngészőt a TMWS-proxy kiszolgálóra. Részletekért lásd: a [PAC Files használatával továbbított forgalom](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/pac-files/traffic-forwarding-u.aspx#GUID-A4A83827-7A29-4596-B866-01ACCEDCC36B).

1. Látogasson el bármelyik internetes webhelyre. A TMWS a TMWS kötött portálra irányítja.

1. Adjon meg egy Active Directory fiókot (Format: *domain* \\ *sAMAccountName* vagy *sAMAccountName* @ *tartomány*), e-mail-címet vagy egyszerű felhasználónevet, majd válassza **a bejelentkezés** lehetőséget. A TMWS az Azure AD bejelentkezési ablakát küldi el.

1. Az Azure AD bejelentkezési ablakában adja meg az Azure AD-fiókjának hitelesítő adatait. Most be kell jelentkeznie a TMWS-be.

## <a name="additional-resources"></a>További források

- [Útmutatók az SaaS-alkalmazások Azure Active Directory-nal való integrálásához](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)

- [A Trend Micro Web Security kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)

- [A Trend Micro Web Security védelme fejlett láthatósággal és ellenőrzésekkel](/cloud-app-security/proxy-intro-aad)