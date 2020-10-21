---
title: Az alkalmazás közzététele az Azure AD App Galleryben
description: Megtudhatja, hogyan listázhat egy olyan alkalmazást, amely támogatja az egyszeri bejelentkezést a Azure Active Directory-alkalmazás galériájában.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/19/2020
ms.author: kenwith
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: 5ade98e04853ae8293f762f237b3b3154c876f7e
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92275715"
---
# <a name="publish-your-app-to-the-azure-ad-app-gallery"></a>Az alkalmazás közzététele az Azure AD App Galleryben

Az alkalmazást közzéteheti az Azure AD App Galleryben. Az alkalmazás közzétételekor lehetőség lesz az ügyfelek számára, amikor alkalmazásokat adnak hozzá a bérlőhöz. 

Az alkalmazás Azure AD-katalógusba való felvételének néhány előnye:

- Az ügyfelek a lehető legjobb egyszeri bejelentkezési élményt megtalálják az alkalmazáshoz.
- Az alkalmazás konfigurálása egyszerű és minimális.
- A gyors keresés megkeresi az alkalmazást a galériában.
- Az ingyenes, alapszintű és prémium szintű Azure AD-ügyfelek használhatják ezt az integrációt.
- A kölcsönös ügyfelek részletes konfigurációs oktatóanyagot kapnak.

Emellett számos előnnyel jár, ha az ügyfelek az Azure AD-t használják identitás-szolgáltatóként az alkalmazáshoz. Ilyenek például a következők:

- Egyszeri bejelentkezés biztosítása a felhasználók számára. Az egyszeri bejelentkezéssel csökkentheti a támogatási költségeket azáltal, hogy megkönnyíti az ügyfelek számára az egyszeri bejelentkezést. Ha az egyszeri bejelentkezés engedélyezve van, az ügyfelek informatikai rendszergazdái nem kell megismerniük, hogyan kell konfigurálni az alkalmazást a szervezetében való használatra. További információ az egyszeri bejelentkezésről: [Mi az egyszeri bejelentkezés?](../manage-apps/what-is-single-sign-on.md).
- Az alkalmazás a Microsoft 365 alkalmazás-katalógusban, a Microsoft 365 app Launcherben és a Microsoft Search on Office.com található. 
- Integrált alkalmazások kezelése. Ha többet szeretne megtudni az alkalmazások kezeléséről az Azure AD-ben, olvassa el a [Mi az az Application Management?](../manage-apps/what-is-application-management.md)című témakört.
- Az alkalmazás az [Graph API](https://docs.microsoft.com/graph/) használatával férhet hozzá a felhasználók termelékenységét a Microsoft-ökoszisztémában használó adataihoz.
- A kölcsönös ügyfeleink számára az Azure AD-csapattal együtt létrehozott, alkalmazásspecifikus dokumentáció megkönnyíti a bevezetést.
- Lehetővé teszi ügyfelei számára, hogy teljes körűen kezeljék az alkalmazottak és a vendég identitások hitelesítését és engedélyezését.
- Az összes Fiókkezelés és megfelelőségi feladat elhelyezése az ügyfelek tulajdonosával.
- Lehetővé teszi az egyszeri bejelentkezés engedélyezését vagy letiltását bizonyos identitás-szolgáltatók, csoportok vagy felhasználók számára az üzleti igények kielégítése érdekében.
- Növelheti a piacot és a bevezetést. Számos nagy szervezet megköveteli, hogy az alkalmazottaik zökkenőmentes egyszeri bejelentkezéses felhasználói élményt nyújtsanak az összes alkalmazásban. Az egyszeri bejelentkezés egyszerűvé tétele fontos.
- Csökkentheti a végfelhasználói súrlódást, ami növelheti a végfelhasználói használatot, és növelheti a bevételt.
- Azok a felhasználók, akik a rendszert használják a tartományok közötti Identitáskezelés ([scim](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)) számára, használhatják a kiépítés használatát ugyanahhoz az alkalmazáshoz.
- Biztonság és kényelem hozzáadása, ha a felhasználók az Azure AD SSO használatával jelentkeznek be az alkalmazásokba, és nem kell külön hitelesítő adatokat kiválasztaniuk.

> [!TIP]
> Ha a vásárlás vagy előfizetés keretében más vállalatok is használhatják az alkalmazást, az alkalmazás elérhetővé válik az ügyfelek számára a saját Azure-bérlőn belül. Ezt nevezzük több-bérlős alkalmazás létrehozásakor. A koncepció áttekintését lásd: több [-bérlős alkalmazások az Azure-ban](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications) és [Az Azure Active Directory-ban való bérlet](single-and-multi-tenant-apps.md).

> [!IMPORTANT]
> Az alkalmazás Azure AD-katalógusban való közzétételéhez meg kell egyeznie bizonyos feltételekkel és kikötésekkel. Mielőtt elkezdené, olvassa el és fogadja el a [feltételeket és a kikötéseket](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/).

Az alkalmazás Azure AD-katalógusban való közzétételének lépései a következők:
1. Válassza ki a megfelelő egyszeri bejelentkezési szabványt az alkalmazásához.
2. Egyszeri bejelentkezés implementálása az alkalmazásban.
3. Hozza létre az Azure-bérlőt, és tesztelje az alkalmazást.
4. Dokumentáció létrehozása és közzététele.
5. Küldje el az alkalmazást.
6. Csatlakozzon a Microsoft partner Networkhöz.


## <a name="prerequisites"></a>Előfeltételek

Állandó fiókra van szükség legalább két regisztrált felhasználó teszteléséhez.


## <a name="step-1---choose-the-right-single-sign-on-standard-for-your-app"></a>1. lépés – az alkalmazás megfelelő egyszeri bejelentkezéses szabványának kiválasztása

Egy alkalmazás az Azure AD-katalógusban való listázásához végre kell hajtania a támogatott egyszeri bejelentkezések közül legalább az egyiket. Az egyszeri bejelentkezés lehetőségeinek megismeréséhez, valamint arról, hogy az ügyfelek hogyan fogják konfigurálni őket az Azure AD-ben, lásd: [SSO-beállítások](../manage-apps/sso-options.md).

A következő táblázat összehasonlítja a fő szabványokat: nyílt hitelesítés 2,0 (OAuth 2,0) az OpenID Connect (OIDC), a Security Assertion Markup Language (SAML) és a Web Services Federation (WS-Fed) használatával.

| Képesség| OAuth/OIDC| SAML/WS-Fed |
| - |-|-|
| Webalapú egyszeri bejelentkezés| √| √ |
| Webalapú egyszeri kijelentkezés| √| √ |
| Mobile-alapú egyszeri bejelentkezés| √| √* |
| Mobile-alapú egyszeri kijelentkezés| √| √* |
| A mobil alkalmazások feltételes hozzáférési szabályzatai| √| X |
| Zökkenőmentes MFA-élmény a Mobile Applications szolgáltatáshoz| √| X |
| Hozzáférés Microsoft Graph| √| X |

* Lehetséges, de a Microsoft nem ad meg mintákat vagy útmutatást.

### <a name="oauth-20-and-openid-connect"></a>Az OAuth 2.0 és az OpenID Connect
A OAuth 2,0 egy [iparági szabványnak](https://oauth.net/2/) megfelelő protokoll az engedélyezéshez. Az OpenID Connect (OIDC) az OAuth 2,0 protokollra épülő, [iparági szabványnak](https://openid.net/connect/) megfelelő identitás-hitelesítési réteg. 

**A OAuth/OIDC kiválasztásának okai**
- Az ezekben a protokollokban rejlő engedély lehetővé teszi, hogy az alkalmazás a Microsoft Graph API-n keresztül hozzáférjen és integrálható legyen a gazdag felhasználói és szervezeti adataival.
- Leegyszerűsíti az ügyfelek végfelhasználói élményét az alkalmazás egyszeri bejelentkezésének bevezetésekor. Egyszerűen meghatározhatja a szükséges engedélyeket, amelyeket a rendszer automatikusan a rendszergazda vagy a végfelhasználó beleegyezésével jelöl.
- Ezen protokollok használata lehetővé teszi, hogy az ügyfelek feltételes hozzáférési és Multi-Factor Authentication (MFA) házirendeket használjanak az alkalmazásokhoz való hozzáférés szabályozásához. 
- A Microsoft [több technológiai platformon](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) biztosít könyvtárakat és kódrészleteket a fejlesztés támogatásához.  

**Néhány megfontolandó szempont**
- Ha már végrehajtotta az SAML-alapú egyszeri bejelentkezést az alkalmazáshoz, akkor előfordulhat, hogy nem szeretne új szabványt megvalósítani az alkalmazásnak a katalógusban való beszerzéséhez.

### <a name="saml-20-or-ws-fed"></a>SAML 2,0 vagy WS-Fed

Az SAML egy érett és széles körben elfogadott, [egyszeri bejelentkezéses standard](https://www.oasis-open.org/standards#samlv2.0) a webalkalmazásokhoz. Ha többet szeretne megtudni arról, hogy az Azure hogyan használja az SAML-t, tekintse meg, [hogyan használja az Azure az SAML protokollt](active-directory-saml-protocol-reference.md). 

A webszolgáltatások összevonása (WS-Fed) a .NET platformmal kifejlesztett webalkalmazásokhoz általánosan használt [iparági szabvány](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) .

**Az SAML kiválasztásának okai**
- Az SAML 2,0 egy érett standard és a legtöbb technológiai platform, amely az SAML 2,0 nyílt forráskódú kódtárait támogatja. 
- Az SAML SSO konfigurálásához adminisztrációs felületet biztosíthat ügyfeleinek. Konfigurálhatja az SAML SSO-t a Microsoft Azure ADhoz, valamint az SAML-t támogató egyéb identitás-szolgáltatókat is.

**Néhány megfontolandó szempont**
- Ha SAML 2,0-vagy WSFed-protokollt használ a mobileszközök számára, bizonyos feltételes hozzáférési szabályzatok, többek között a többtényezős hitelesítés (MFA), csökkentett teljesítményű élményt nyújtanak.
- Ha el szeretné érni a Microsoft Graph, a szükséges tokenek létrehozásához a OAuth 2,0-on keresztül kell végrehajtania a hitelesítést. 

### <a name="password-based"></a>Jelszó-alapú
A jelszó-alapú egyszeri bejelentkezés (más néven a jelszó-tároló) lehetővé teszi a felhasználók hozzáférésének és jelszavának kezelését az identitás-összevonást nem támogató webalkalmazásokhoz. Olyan helyzetekben is hasznos, amikor több felhasználónak egyetlen fiókot kell megosztania, például a szervezete közösségi Media app-fiókjaival.


## <a name="step-2---implement-single-sign-on-in-your-app"></a>2. lépés – egyszeri bejelentkezés megvalósítása az alkalmazásban
A katalógusban szereplő összes alkalmazásnak a támogatott egyszeri bejelentkezési lehetőségek egyikét kell megvalósítani. További információ a támogatott lehetőségekről: SSO- [Beállítások](../manage-apps/sso-options.md).

A OAuth és a OIDC kapcsolatban lásd: [útmutató a hitelesítési mintákhoz](v2-app-types.md) és az [Azure Active Directory-kódokhoz](sample-v2-code.md).

Az SAML és a WS-fed esetében az alkalmazásnak képesnek kell lennie az SSO-integrációra SP-vagy IDENTITÁSSZOLGÁLTATÓ módban. Győződjön meg arról, hogy ez a funkció megfelelően működik a kérelem elküldése előtt.

A hitelesítéssel kapcsolatos további tudnivalókért tekintse meg a [Mi a hitelesítés?](../azuread-dev/v1-authentication-scenarios.md)című témakört.

> [!IMPORTANT]
> Összevont alkalmazások (OpenID és SAML/WS-Fed) esetén az alkalmazásnak támogatnia kell a szolgáltatott szoftver (SaaS) modelljét. Az Azure AD Gallery-alkalmazásoknak több ügyfél-konfigurációt is támogatnia kell, és nem lehetnek egyediek egyetlen ügyfél számára sem.

### <a name="implement-oauth-20-and-openid-connect"></a>A OAuth 2,0 és az OpenID Connect implementálása

Az OpenID Connect esetében az alkalmazásnak több-Bérlőnek kell lennie, és az [Azure ad-beli engedélyezési keretrendszert](consent-framework.md) megfelelően kell megvalósítani az alkalmazáshoz. A felhasználó elküldheti a bejelentkezési kérést egy közös végpontnak, így bármely ügyfél beleegyezik az alkalmazásba. A felhasználó hozzáférését a bérlő azonosítója és a tokenben kapott felhasználó egyszerű felhasználóneve alapján szabályozhatja.

Az egyes példák áttekintését lásd: [Microsoft Identity platform Code Samples](sample-v2-code.md). 

A Mobile-specifikus példák áttekintését lásd: 
* [Android](quickstart-v2-android.md)
* [iOS](quickstart-v2-ios.md)
* [Univerzális Windows-platform](quickstart-v2-uwp.md)

### <a name="implement-saml-20"></a>SAML 2,0 implementálása

Ha az alkalmazás támogatja az SAML 2,0-et, akkor közvetlenül integrálható egy Azure AD-Bérlővel. Ha többet szeretne megtudni az SAML-konfigurációról az Azure AD-vel, tekintse meg az [SAML-alapú egyszeri bejelentkezés konfigurálása](../manage-apps/configure-saml-single-sign-on.md)című témakört.

A Microsoft nem biztosít vagy ajánlja az SAML-implementációk függvénytárait. Számos nyílt forráskódú függvénytár érhető el.

### <a name="implement-ws-fed"></a>WS-Fed implementálása
Ha többet szeretne megtudni a ASP.NET Core WS-Fedről, tekintse meg a [felhasználók hitelesítése a WS-Federation használatával ASP.net Core](https://docs.microsoft.com/aspnet/core/security/authentication/ws-federation).

### <a name="implement-password-vaulting"></a>A jelszó-tároló implementálása

Hozzon létre egy webalkalmazást, amely tartalmaz egy HTML-bejelentkezési oldalt. Győződjön meg arról, hogy az alkalmazás támogatja az űrlapos hitelesítést, így a jelszó-tárolót úgy is megteheti, hogy a várt módon működjön az egyszeri bejelentkezéssel.


## <a name="step-3---create-your-azure-tenant-and-test-your-app"></a>3. lépés – az Azure-bérlő létrehozása és az alkalmazás tesztelése

Az alkalmazás teszteléséhez szüksége lesz egy Azure AD-bérlőre. A fejlesztési környezet beállításához tekintse meg [a rövid útmutató: bérlő beállítása](quickstart-create-new-tenant.md)című témakört.

Alternatív megoldásként az Azure AD-bérlő minden Microsoft 365-előfizetéssel rendelkezik. Az ingyenes Microsoft 365 fejlesztési környezet létrehozásához tekintse meg [a Microsoft 365 fejlesztői program csatlakoztatása](https://docs.microsoft.com/office/developer-program/microsoft-365-developer-program)című témakört.

Ha bérlője van, engedélyeznie kell és tesztelni kell az egyszeri bejelentkezéses hozzáférést. 

**OIDC vagy esküt használó alkalmazások**esetén [regisztrálja alkalmazását](quickstart-register-app.md) több-bérlős alkalmazásként. A támogatott fióktípus területen válassza ki a szervezeti címtár és a személyes Microsoft-fiókok lehetőséget.

**Az SAML-és a WS-fed-alapú alkalmazások esetében**az [SAML-alapú egyszeri bejelentkezéses](../manage-apps/configure-saml-single-sign-on.md) alkalmazásokat az Azure ad általános SAML-sablonjának használatával konfigurálhatja.

Ha szükséges, [egyetlen bérlős alkalmazást is át lehet alakítani több-bérlőre](howto-convert-app-to-be-multi-tenant.md) .


## <a name="step-4---create-and-publish-documentation"></a>4. lépés – dokumentáció létrehozása és közzététele

### <a name="documentation-on-your-site"></a>A webhelyen található dokumentáció

A könnyű bevezetést a nagyvállalati szoftverek döntéseinek jelentős része képezi. Az egyszerű, könnyen követhető dokumentumok egyértelművé teszi az ügyfelek bevezetésének útját, és csökkentik a támogatási költségeket. Több ezer szoftvergyártó dolgozik a Microsoftnál, hogy mi működik.

Javasoljuk, hogy a webhelyen legalább a következő elemeket tartalmazza a dokumentációban.

* Az egyszeri bejelentkezés funkciójának bemutatása
  * Támogatott protokollok
  * Verzió és SKU
  * Támogatott identitás-szolgáltatók listája dokumentációs hivatkozásokkal
* Licencelési információk az alkalmazáshoz
* Szerepköralapú hozzáférés-vezérlés az egyszeri bejelentkezés konfigurálásához
* SSO-konfigurációs lépések
  * Az SAML felhasználói felületi konfigurációs elemei a szolgáltatótól várt értékekkel
  * Az azonosító szolgáltatók számára átadandó szolgáltatói információk
* If OIDC/OAuth
  * Az üzleti indoklások megadásához szükséges engedélyek listája
* Tesztelési lépések a próbaüzem felhasználói számára
* Hibaelhárítási információk, például hibakódok és üzenetek
* Támogatási mechanizmusok ügyfelek számára

### <a name="documentation-on-the-microsoft-site"></a>Dokumentáció a Microsoft webhelyén

Ha az alkalmazást az Azure Active Directory alkalmazás-katalógussal sorolja fel, amely az Azure piactéren is közzéteszi az alkalmazást, a Microsoft dokumentációt készít a kölcsönös ügyfeleinknek a lépésenkénti folyamatról. [Itt](https://aka.ms/appstutorial)láthat egy példát. Ezt a dokumentációt a katalógusba való beküldésük alapján hozza létre, és egyszerűen frissítheti, ha az alkalmazást a GitHub-fiók használatával módosítja.


## <a name="step-5---submit-your-app"></a>5. lépés – az alkalmazás elküldése

Miután tesztelte, hogy az alkalmazás-integráció az Azure AD-vel működik, küldje el kérelmét a [Microsoft Application Network portálon](https://microsoft.sharepoint.com/teams/apponboarding/Apps).

Amikor első alkalommal próbál bejelentkezni a portálra, a két képernyő egyikét fogja megjeleníteni. 

Ha a "nem működött" üzenet jelenik meg, akkor kapcsolatba kell lépnie az [Azure ad SSO integrációs csapatával](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Adja meg a kérelem elküldéséhez használni kívánt e-mail-fiókot. Egy üzleti e-mail-cím, például `name@yourbusiness.com` előnyben részesített. Az Azure AD csapata felveszi a fiókot a Microsoft Application Network portálon.

Ha megjelenik a "hozzáférés kérése" oldal, töltse ki az üzleti indoklást, és válassza a **hozzáférés kérése**lehetőséget.

A fiók hozzáadása után bejelentkezhet a Microsoft Application Network portálra, és elküldheti a kérést, ha a kezdőlapon a **küldési kérelem (ISV)** csempét választja.

![A beküldési kérelem (ISV) csempéje a kezdőlapon](./media/howto-app-gallery-listing/homepage.png)

### <a name="issues-on-logging-into-portal"></a>A portálra való bejelentkezéssel kapcsolatos problémák

Ha ezt a hibát látja a bejelentkezés során, akkor itt látható a probléma részletei, és így javítható.

* Ha a bejelentkezés le lett tiltva, ahogy az alábbi ábrán is látható:

  ![az alkalmazás katalógusban való feloldásával kapcsolatos problémák](./media/howto-app-gallery-listing/blocked.png)

**mi történik:**

A vendég felhasználó összevonása egy otthoni Bérlővel, amely egyben Azure AD is. A vendég felhasználó magas kockázatú. A Microsoft nem teszi lehetővé, hogy a magas kockázatú felhasználók hozzáférjenek erőforrásaihoz. Az összes magas kockázatú felhasználó (alkalmazottak vagy vendégek/szállítók) a Microsoft erőforrásaihoz való hozzáférésre vonatkozó kockázatot kell kijavítania/lezárva. A vendég felhasználók számára ez a felhasználói kockázat a Kezdőlap bérlőtől származik, és a szabályzat az erőforrás-bérlőtől származik (ebben az esetben a Microsoft).
 
**Biztonságos megoldások:**

* Az MFA által regisztrált vendég felhasználók a saját felhasználói kockázatot szervizelik. Ezt megteheti a vendég felhasználó a biztonságos jelszó módosításával vagy alaphelyzetbe állításával (a saját https://aka.ms/sspr) bérlőn (ez az MFA és a SSPR szükséges a hazai bérlőn). A biztonságos jelszó módosítását vagy alaphelyzetbe állítását az Azure AD-ben kell elindítani, nem pedig a helyi gépen.

* A vendég felhasználók rendelkeznek a saját adminisztrátorokkal. Ebben az esetben a rendszergazda jelszó-visszaállítást (ideiglenes jelszó létrehozása) hajt végre. Ehhez nincs szükség az identitások védelmére. A vendég felhasználó rendszergazdája a https://aka.ms/RiskyUsers "jelszó alaphelyzetbe állítása" lehetőségre kattintva léphet ki.

* A vendég felhasználók a rendszergazdájuk bezárják vagy elmulasztják a kockázatokat. Ez megint nem szükséges az identitások védelméhez. A rendszergazda a https://aka.ms/RiskyUsers "felhasználói kockázat elvetése" gombra kattintva léphet ki. Azonban a rendszergazdának el kell végeznie az átvilágítás elvégzését annak érdekében, hogy ez hamis pozitív kockázatértékelés legyen a felhasználói kockázat bezárása előtt. Ellenkező esetben a kockázat kiértékelését vizsgálat nélkül letiltva veszélyeztetik a Microsoft erőforrásait.

> [!NOTE]
> Ha bármilyen probléma merül fel a hozzáféréssel kapcsolatban, forduljon az [Azure ad SSO Integration csapatához](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

### <a name="implementation-specific-options"></a>Megvalósítási specifikus beállítások
Ha az OpenID Connect használatával szeretné felvenni az alkalmazást a katalógusban szereplő listára, válassza az **OpenID connect & OAuth 2,0** lehetőséget a képen látható módon.

![OpenID Connect-alkalmazás listázása a katalógusban](./media/howto-app-gallery-listing/openid.png)

Ha az **saml 2,0** vagy a **ws-fed**használatával szeretné felvenni az alkalmazást a katalógusba, válassza az **SAML 2.0/ws-fed** lehetőséget, amint az látható.

![SAML 2,0-vagy WS-Fed-alkalmazás listázása a katalógusban](./media/howto-app-gallery-listing/saml.png)

Ha hozzá szeretné adni az alkalmazást a katalógusban a jelszó SSO használatával, válassza a **jelszó egyszeri bejelentkezés (UserName & Password)** lehetőséget az ábrán látható módon.

![Jelszó-SSO-alkalmazás listázása a katalógusban](./media/howto-app-gallery-listing/passwordsso.png)

Ha SCIM 2,0-végpontot hoz létre a felhasználók üzembe helyezéséhez, válassza a látható lehetőséget. 

   ![A felhasználó kiépítési kérelme](./media/howto-app-gallery-listing/user-provisioning.png)

### <a name="update-or-remove-an-existing-listing"></a>Meglévő lista frissítése vagy eltávolítása

A [Microsoft Application Network portálon](https://microsoft.sharepoint.com/teams/apponboarding/Apps)frissíthet vagy eltávolíthat egy meglévő Gallery-alkalmazást.

![SAML-alkalmazás listázása a katalógusban](./media/howto-app-gallery-listing/updateorremove.png)

> [!NOTE]
> Ha problémák merülnek fel a hozzáféréssel kapcsolatban, tekintse át az előző szakaszt a fiók létrehozásáról. Ha ez nem működik, lépjen kapcsolatba az [Azure ad SSO integrációs csapatával](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).


### <a name="timelines"></a>Idősorok

Az SAML 2,0 vagy WS-Fed alkalmazás a katalógusban való listázásának ütemterve 7 – 10 munkanap.

![Az SAML-alkalmazások katalógusban való listázásának ütemterve](./media/howto-app-gallery-listing/timeline.png)

A katalógusban található OpenID Connect-alkalmazás listázási folyamatának ütemterve 2 – 5 munkanap.

![Az OpenID Connect alkalmazás listázása a katalógusban](./media/howto-app-gallery-listing/timeline2.png)

### <a name="escalations"></a>Azok következményeinek

Bármilyen eszkaláció esetén küldjön e-mailt az [Azure ad SSO integrációs csapatának](mailto:SaaSApplicationIntegrations@service.microsoft.com), és a lehető leghamarabb válaszolunk.


## <a name="step-6---join-the-microsoft-partner-network"></a>6. lépés – csatlakozás a Microsoft partner networkhez
A Microsoft Partner Network azonnali hozzáférést biztosít az exkluzív erőforrásokhoz, programokhoz, eszközökhöz és kapcsolatokhoz. A hálózathoz való csatlakozáshoz és a piacra jutási terv létrehozásához tekintse meg a [kereskedelmi ügyfelek elérhetőségét](https://partner.microsoft.com/explore/commercial#gtm)ismertető témakört.


## <a name="next-steps"></a>Következő lépések
* [SCIM-végpont létrehozása és a felhasználók üzembe helyezésének konfigurálása](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Hitelesítési forgatókönyvek az Azure AD-hez](authentication-flows-app-scenarios.md)
