---
title: A Microsoft Identity platform hitelesítési folyamatai & alkalmazás forgatókönyvei | Azure
description: Ismerje meg a Microsoft Identity platform alkalmazási forgatókönyveit, beleértve az identitások hitelesítését, a jogkivonatok beszerzését és a védett API-k meghívását.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/03/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: ffa52805a5e2680d534b2b24a210465cb3fc7cac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100557852"
---
# <a name="authentication-flows-and-application-scenarios"></a>Hitelesítési folyamatok és alkalmazási helyzetek

A Microsoft Identity platform különböző típusú modern alkalmazás-architektúrák hitelesítését támogatja. Az összes architektúra az iparági szabványnak megfelelő protokollok ( [OAuth 2,0 és OpenID Connect](active-directory-v2-protocols.md)) alapján történik. A [Microsoft Identity platform hitelesítési kódtárainak](reference-v2-libraries.md)használatával az alkalmazások hitelesítik az identitásokat, és jogkivonatokat kapnak a védett API-k eléréséhez.

Ez a cikk a hitelesítési folyamatokat és azokat az alkalmazási forgatókönyveket ismerteti, amelyekben használatban van.

## <a name="application-categories"></a>Alkalmazás-kategóriák

A jogkivonatok számos különböző típusú alkalmazásból szerezhetők be, többek között:

- Webalkalmazások
- Mobilalkalmazások
- Asztali alkalmazások
- Webes API-k

A tokeneket olyan eszközökön futó alkalmazások is beszerezhetik, amelyek nem rendelkeznek böngészővel, vagy amelyek a eszközök internetes hálózataon (IoT) futnak.

Az alábbi szakaszok az alkalmazások kategóriáit írják le.

### <a name="protected-resources-vs-client-applications"></a>Védett erőforrások és ügyfélalkalmazások

A hitelesítési forgatókönyvek két tevékenységet foglalnak magukban:

- **Biztonsági jogkivonatok beszerzése egy védett webes API**-hoz: javasoljuk, hogy használja a Microsoft által fejlesztett és támogatott [Microsoft Authentication Library (MSAL) könyvtárat](reference-v2-libraries.md).
- **Webes API-k vagy webalkalmazások védelme**: az erőforrások védelmének egyik kihívása a biztonsági jogkivonat ellenőrzése. Egyes platformokon a Microsoft [köztes könyvtárakat](reference-v2-libraries.md)is kínál.

### <a name="with-users-or-without-users"></a>Felhasználók vagy felhasználók nélkül

A legtöbb hitelesítési forgatókönyv a bejelentkezett felhasználók nevében jogkivonatokat vásárol.

![Felhasználói helyzetek](media/scenarios/scenarios-with-users.svg)

Vannak azonban démon-alkalmazások is. Ezekben az esetekben az alkalmazások a felhasználók nevében jogkivonatokat is beszerezzenek.

![Forgatókönyvek Daemon-alkalmazásokkal](media/scenarios/daemon-app.svg)

### <a name="single-page-public-client-and-confidential-client-applications"></a>Egyoldalas, nyilvános ügyfél és bizalmas ügyfélalkalmazások

A biztonsági jogkivonatokat többféle típusú alkalmazás is beszerezheti. Ezek az alkalmazások általában a következő három kategóriába sorolhatók. Mindegyiket különböző kódtárak és objektumok használják.

- **Egyoldalas alkalmazások**: ezek olyan webalkalmazások, amelyekben a böngészőben futó JavaScript-vagy írógéppel-alkalmazás jogkivonatokat szerez be. Számos modern alkalmazás rendelkezik egy egyoldalas alkalmazással a kezelőfelületen, amely elsősorban JavaScript nyelven íródott. Az alkalmazás gyakran olyan keretrendszert használ, mint a szögletes, a reakciós vagy a Vue. MSAL.js az egyetlen olyan Microsoft-hitelesítési függvénytár, amely támogatja az egyoldalas alkalmazásokat.

- **Nyilvános ügyfélalkalmazások**: ebben a kategóriában található alkalmazások, például a következő típusokhoz, mindig jelentkezzen be a felhasználókba:
  - A bejelentkezett felhasználók nevében webes API-kat meghívó asztali alkalmazások
  - Mobilalkalmazások
  - A böngészővel nem rendelkező eszközökön futó alkalmazások, például a IoT-on futó alkalmazások

- **Bizalmas ügyfélalkalmazások**: az ebben a kategóriában található alkalmazások a következők:
  - Webes API-t meghívó webalkalmazások
  - Webes API-kat meghívó webes API-k
  - Daemon-alkalmazások, még akkor is, ha a konzol szolgáltatásként, például Linux-démonként vagy Windows-szolgáltatásként van implementálva

### <a name="sign-in-audience"></a>Bejelentkezési közönség

A rendelkezésre álló hitelesítési folyamatok a bejelentkezési közönségtől függően eltérőek. Néhány folyamat csak munkahelyi vagy iskolai fiókokhoz érhető el. Mások egyaránt elérhetők munkahelyi vagy iskolai fiókokhoz, valamint személyes Microsoft-fiókokhoz.

További információ: [támogatott fióktípus](v2-supported-account-types.md#account-type-support-in-authentication-flows).

## <a name="application-scenarios"></a>Alkalmazáshasználati helyzetek

A Microsoft Identity platform támogatja az alábbi alkalmazás-architektúrák hitelesítését:

- Egyoldalas alkalmazások
- Webalkalmazások
- Webes API-k
- Mobilalkalmazások
- Natív alkalmazások
- Démonalkalmazások
- Kiszolgálóoldali alkalmazások

Az alkalmazások a különböző hitelesítési folyamatokat használják a felhasználói bejelentkezéshez és a jogkivonatok lekéréséhez a védett API-k meghívásához.

### <a name="single-page-application"></a>Egyoldalas alkalmazás

Számos modern webalkalmazás úgy van kialakítva, mint az ügyféloldali egyoldalas alkalmazások. Ezek az alkalmazások JavaScriptet vagy olyan keretrendszert használnak, mint a szögletes, a Vue és a reakció. Ezek az alkalmazások egy böngészőben futnak.

Az egyoldalas alkalmazások különböznek a hagyományos kiszolgálóoldali webalkalmazástól a hitelesítési jellemzők szempontjából. A Microsoft Identity platform használatával az egyoldalas alkalmazások bejelentkezhetnek a felhasználókba, és jogkivonatokat kérhetnek a háttér-szolgáltatások vagy a webes API-k eléréséhez. A Microsoft Identity platform két engedélyezési típust kínál a JavaScript-alkalmazásokhoz:

| MSAL.js (2. x) | MSAL.js (1. x) |
|---|---|
| ![Egyoldalas alkalmazás-hitelesítés](media/scenarios/spa-app-auth.svg) | ![Egyoldalas alkalmazás implicit](media/scenarios/spa-app.svg) |

### <a name="web-app-that-signs-in-a-user"></a>Felhasználóhoz tartozó webalkalmazás

![Egy webalkalmazás, amely egy felhasználónál jelentkezik be](media/scenarios/scenario-webapp-signs-in-users.svg)

Egy olyan webalkalmazás védelmének elősegítése, amely egy felhasználónál jelentkezik:

- Ha a .NET-ben fejleszt fejlesztést, a ASP.NET vagy a ASP.NET Core a ASP.NET OpenID Connect middleware használatával. Az erőforrások védelme magában foglalja a biztonsági jogkivonat érvényesítését, amelyet a .NET- [hez készült IdentityModel-bővítmények](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) és nem MSAL-tárak végeznek.

- Ha Node.json fejleszt, a [MSAL csomópontot](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) vagy [Passport.js](https://github.com/AzureAD/passport-azure-ad)használja.

További információ: [a felhasználók által bejelentkező webalkalmazás](scenario-web-app-sign-user-overview.md).

### <a name="web-app-that-signs-in-a-user-and-calls-a-web-api-on-behalf-of-the-user"></a>A felhasználó nevében egy webes API-t meghívó webalkalmazás

![Webes API-kat hívó webalkalmazás](media/scenarios/web-app.svg)

Ha egy webes API-t egy felhasználó nevében szeretne meghívni egy webalkalmazásból, használja az engedélyezési kód folyamatát, és tárolja a beszerzett jogkivonatokat a jogkivonat-gyorsítótárban. Ha szükséges, a MSAL frissíti a jogkivonatokat, és a vezérlő csendesen beszerzi a tokeneket a gyorsítótárból.

További információt a webes [API-kat meghívó webalkalmazás](scenario-web-app-call-api-overview.md)című témakörben talál.

### <a name="desktop-app-that-calls-a-web-api-on-behalf-of-a-signed-in-user"></a>A bejelentkezett felhasználó nevében webes API-t meghívó asztali alkalmazás

Ahhoz, hogy egy asztali alkalmazás meghívjon egy webes API-t, amely a felhasználók számára jelentkezik be, használja a MSAL interaktív jogkivonat-gyűjtési módszereit. Ezeknek az interaktív módszereknek a segítségével szabályozhatja a bejelentkezési felhasználói felületet. A MSAL webböngészőt használ ehhez az interakcióhoz.

![Webes API-t hívó asztali alkalmazás](media/scenarios/desktop-app.svg)

A Windows által üzemeltetett alkalmazások számára egy másik lehetőség van a Windows-tartományhoz vagy Azure Active Directory (Azure AD) csatlakoztatott számítógépeken. Ezek az alkalmazások a tokenek csendes beszerzését teszik lehetővé [integrált Windows-hitelesítés](https://aka.ms/msal-net-iwa)használatával.

A böngésző nélküli eszközön futó alkalmazások továbbra is hívhatnak API-t egy felhasználó nevében. A hitelesítéshez a felhasználónak egy webböngészővel rendelkező másik eszközön kell bejelentkeznie. Ehhez a forgatókönyvhöz az [eszköz kódjának folyamatát](https://aka.ms/msal-net-device-code-flow)kell használnia.

![Eszköz kódjának folyamata](media/scenarios/device-code-flow-app.svg)

Habár nem javasoljuk, hogy használja, a [Felhasználónév/jelszó folyamat](scenario-desktop-acquire-token.md#username-and-password) nyilvános ügyfélalkalmazások esetében elérhető. Erre a folyamatra továbbra is szükség van bizonyos esetekben, például a DevOps.

A Felhasználónév/jelszó folyamat használata korlátozza az alkalmazásokat. Az alkalmazások például nem jelentkezhetnek be olyan felhasználók számára, akiknek többtényezős hitelesítést vagy az Azure AD feltételes hozzáférési eszközét kell használniuk. Az alkalmazások az egyszeri bejelentkezés előnyeit is kihasználhatják. A Felhasználónév/jelszó folyamattal való hitelesítés a modern hitelesítés elvein alapul, és csak az örökölt okok miatt biztosítható.

Ha azt szeretné, hogy a jogkivonat-gyorsítótár továbbra is megmaradjon, testreszabhatja a [jogkivonat-gyorsítótár szerializálását](scenario-desktop-acquire-token.md#file-based-token-cache)az asztali alkalmazásokban. A [kettős jogkivonat-gyorsítótár szerializálásának](scenario-desktop-acquire-token.md#dual-token-cache-serialization-msal-unified-cache--adal-v3)megvalósításával visszamenőlegesen kompatibilis és továbbítható jogkivonat-gyorsítótárak is használhatók. Ezek a jogkivonatok támogatják a hitelesítési kódtárak előző generációit. Az adott kódtárak közé tartozik a .NET-hez készült Azure AD Authentication Library (ADAL.NET) 3-as és 4-es verziója.

További információ: a [webes API-kat meghívó asztali alkalmazás](scenario-desktop-overview.md).

### <a name="mobile-app-that-calls-a-web-api-on-behalf-of-an-interactive-user"></a>Egy interaktív felhasználó nevében webes API-t meghívó mobil alkalmazás

Az asztali alkalmazásokhoz hasonlóan a Mobile apps is meghívja a MSAL interaktív jogkivonat-beszerzési módszereit, hogy jogkivonatot szerezzen a webes API-k meghívásához.

![Webes API-t hívó mobil alkalmazás](media/scenarios/mobile-app.svg)

Az iOS és a MSAL Android MSAL alapértelmezés szerint a rendszerböngészőt használja. A beágyazott webes nézetet azonban közvetlenül is használhatja. Vannak olyan sajátosságok, amelyek a mobil platformtól függenek: Univerzális Windows-platform (UWP), iOS vagy Android.

Bizonyos forgatókönyvek, például az eszköz-AZONOSÍTÓhoz vagy az eszközök regisztrálásához kapcsolódó feltételes hozzáférésre vonatkozó feltételek, a szükséges, hogy a bróker telepítve legyen az eszközön. Ilyenek például a Microsoft céges portál az Androidon és a Microsoft Authenticator Androidon és iOS-en. A MSAL mostantól képes együttműködni a brókerekkel. A brókerekkel kapcsolatos további információkért lásd: [a brókerek kihasználása Androidon és iOS-](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS)en.

További információt a [webes API-kat meghívó Mobile App](scenario-mobile-overview.md)című témakörben talál.

> [!NOTE]
> MSAL. iOS, MSAL használó mobil alkalmazás. Az androidos vagy a Xamarin-alapú MSAL.NET az alkalmazásra vonatkozó védelmi szabályzatok is alkalmazhatók. Előfordulhat például, hogy a házirendek megakadályozhatják a felhasználó számára a védett szöveg másolását. A Mobile alkalmazást az Intune felügyeli, és az Intune felügyelt alkalmazásként ismeri fel. További információ: [Microsoft Intune app SDK – áttekintés](/intune/app-sdk).
>
> Az [Intune app SDK](/intune/app-sdk-get-started) elkülöníti a MSAL-kódtárakat, és saját maga kezeli az Azure ad-t.

### <a name="protected-web-api"></a>Védett webes API

A Microsoft Identity platform végpontján keresztül biztonságossá teheti a webszolgáltatásokat, például az alkalmazás REST-alapú webes API-ját. A védett webes API-t egy hozzáférési jogkivonat hívja meg. A jogkivonat segíti az API-k adatvédelmét és a bejövő kérések hitelesítését. Egy webes API hívója hozzáfűz egy hozzáférési jogkivonatot egy HTTP-kérelem engedélyezési fejlécében.

Ha szeretné megóvni a ASP.NET vagy a ASP.NET Core webes API-t, ellenőrizze a hozzáférési jogkivonatot. Ehhez az ellenőrzéshez a ASP.NET JWT middleware-t használja. Az érvényesítést a .NET Library [IdentityModel bővítményei](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) , és nem a MSAL.net végzi el.

További információ: [Protected web API](scenario-protected-web-api-overview.md).

### <a name="web-api-that-calls-another-web-api-on-behalf-of-a-user"></a>Webes API, amely egy másik webes API-t hív meg egy felhasználó nevében

Ahhoz, hogy a védett webes API meghívjon egy másik webes API-t egy felhasználó nevében, az alkalmazásnak meg kell vásárolnia egy jogkivonatot az alárendelt webes API-hoz. Az ilyen hívásokat más néven *szolgáltatások közötti* hívásoknak nevezzük. A más webes API-kat meghívó webes API-knak egyéni gyorsítótár-szerializálást kell biztosítaniuk.

![Egy webes API, amely egy másik webes API-t hív meg](media/scenarios/web-api.svg)

További információt a webes [API-kat meghívó webes API](scenario-web-api-call-api-overview.md)-k című témakörben talál.

### <a name="daemon-app-that-calls-a-web-api-in-the-daemons-name"></a>Daemon-alkalmazás, amely meghívja a webes API-t a démon nevében

A hosszan futó folyamatokkal rendelkező vagy felhasználói interakció nélkül működő alkalmazásokhoz is szükség van a biztonságos webes API-k elérésére. Egy ilyen alkalmazás az alkalmazás identitásával hitelesítheti és lekérheti a jogkivonatokat. Az alkalmazás egy ügyfél-titkos vagy egy tanúsítvány használatával igazolja identitását.

A MSAL-ben az [ügyfél-hitelesítő adatok](scenario-daemon-acquire-token.md#acquiretokenforclient-api) megszerzésének módszereivel olyan démoni alkalmazásokat írhat, amelyek tokent szereznek a hívó alkalmazás számára. Ezekhez a módszerekhez az Azure AD-ben az alkalmazás regisztrálásához hozzáadott ügyfél-titkos kulcs szükséges. Az alkalmazás ezután megosztja a titkot a nevezett démonsal. Ilyen titkok például az alkalmazás jelszavai, a tanúsítvány-érvényesítés és az ügyfél-érvényesítés.

![Más alkalmazások és API-k által hívott Daemon-alkalmazás](media/scenarios/daemon-app.svg)

További információ: a [webes API-kat meghívó alkalmazás](scenario-daemon-overview.md).

## <a name="scenarios-and-supported-authentication-flows"></a>Forgatókönyvek és támogatott hitelesítési folyamatok

A hitelesítési folyamatokat a jogkivonatokat kérő alkalmazási forgatókönyvek megvalósításához használja. Az alkalmazási forgatókönyvek és a hitelesítési folyamatok között nincs egy az egyhez típusú hozzárendelés.

A jogkivonatok beszerzését érintő forgatókönyvek a OAuth 2,0 hitelesítési folyamatokra is leképezhetők. További információ: [OAuth 2,0 és OpenID Connect protokollok a Microsoft Identity platformon](active-directory-v2-protocols.md).

<table>
 <thead>
  <tr><th>Eset</th> <th>Részletes forgatókönyv – útmutató</th> <th>OAuth 2,0 flow és Grant</th> <th>Célközönség</th></tr>
 </thead>
 <tbody>
  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App with Auth code" src="media/scenarios/spa-app-auth.svg"></a></td>
   <td><a href="scenario-spa-overview.md">Egyoldalas alkalmazás</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Engedélyezési kód</a> PKCE</td>
   <td>Munkahelyi vagy iskolai fiókok, személyes fiókok és Azure Active Directory B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App with Implicit" src="media/scenarios/spa-app.svg"></a></td>
   <td><a href="scenario-spa-overview.md">Egyoldalas alkalmazás</a></td>
   <td><a href="v2-oauth2-implicit-grant-flow.md">Implicit</a></td>
   <td>Munkahelyi vagy iskolai fiókok, személyes fiókok és Azure Active Directory B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-sign-user-overview.md"><img alt="Web app that signs in users" src="media/scenarios/scenario-webapp-signs-in-users.svg"></a></td>
   <td><a href="scenario-web-app-sign-user-overview.md">Felhasználót bejelentkeztető webalkalmazás</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Engedélyezési kód</a></td>
   <td>Munkahelyi vagy iskolai fiókok, személyes fiókok és Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-call-api-overview.md"><img alt="Web app that calls web APIs" src="media/scenarios/web-app.svg"></a></td>
   <td><a href="scenario-web-app-call-api-overview.md">Webes API-kat hívó webalkalmazás</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Engedélyezési kód</a></td>
   <td>Munkahelyi vagy iskolai fiókok, személyes fiókok és Azure AD B2C</td>
 </tr>

  <tr>
   <td rowspan="3"><a href="scenario-desktop-overview.md"><img alt=Desktop app that calls web APIs" src="media/scenarios/desktop-app.svg"></a></td>
   <td rowspan="4"><a href="scenario-desktop-overview.md">Webes API-kat hívó asztali alkalmazás</a></td>
   <td>Interaktív az <a href="v2-oauth2-auth-code-flow.md">engedélyezési kód</a> és a PKCE használatával</td>
   <td>Munkahelyi vagy iskolai fiókok, személyes fiókok és Azure AD B2C</td>
 </tr>

  <tr>
   <td>Beépített Windows-hitelesítés</td>
   <td>Munkahelyi vagy iskolai fiókok</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Erőforrás-tulajdonos jelszava</a></td>
   <td>Munkahelyi vagy iskolai fiókok és Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser"><img alt="Browserless application" src="media/scenarios/device-code-flow-app.svg"></a></td>
   <td><a href="v2-oauth2-device-code.md">Eszköz kódja</a></td>
   <td>Munkahelyi vagy iskolai fiókok, személyes fiókok és Azure AD B2C</td>
 </tr>

 <tr>
   <td rowspan="2"><a href="scenario-mobile-overview.md"><img alt="Mobile app that calls web APIs" src="media/scenarios/mobile-app.svg"></a></td>
   <td rowspan="2"><a href="scenario-mobile-overview.md">Webes API-kat hívó mobilalkalmazás</a></td>
   <td>Interaktív az <a href="v2-oauth2-auth-code-flow.md">engedélyezési kód</a> és a PKCE használatával</td>
   <td>Munkahelyi vagy iskolai fiókok, személyes fiókok és Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Erőforrás-tulajdonos jelszava</a></td>
   <td>Munkahelyi vagy iskolai fiókok és Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-daemon-overview.md"><img alt="Daemon app that calls web APIs" src="media/scenarios/daemon-app.svg"></a></td>
   <td><a href="scenario-daemon-overview.md">Webes API-kat meghívó Daemon-alkalmazás</a></td>
   <td><a href="v2-oauth2-client-creds-grant-flow.md">Ügyfél-hitelesítő adatok</a></td>
   <td>Csak az Azure AD-szervezeteknél használatos engedélyek, amelyek nem rendelkeznek felhasználóval</td>
 </tr>

  <tr>
   <td><a href="scenario-web-api-call-api-overview.md"><img alt="Web API that calls web APIs" src="media/scenarios/web-api.svg"></a></td>
   <td><a href="scenario-web-api-call-api-overview.md">Webes API-kat hívó webes API</a></td>
   <td><a href="v2-oauth2-on-behalf-of-flow.md">Nevében</a></td>
   <td>Munkahelyi vagy iskolai fiókok és személyes fiókok</td>
 </tr>

 </tbody>
</table>

## <a name="scenarios-and-supported-platforms-and-languages"></a>Forgatókönyvek és támogatott platformok és nyelvek

A Microsoft hitelesítési könyvtárai több platformot támogatnak:

- .NET Core
- .NET-keretrendszer
- Java
- JavaScript
- macOS
- Natív Android
- Natív iOS
- Node.js
- Python
- Windows 10/UWP
- Xamarin.iOS
- Xamarin.Android

Az alkalmazások létrehozásához különböző nyelveket is használhat.

> [!NOTE]
> Egyes alkalmazások típusai nem érhetők el minden platformon.

A következő táblázat Windows oszlopában a .NET-keretrendszer minden alkalommal megemlíti a .NET-keretrendszert is. Az utóbbi elhagyja a tábla zsúfoltságának elkerülését.

|Eset  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [Egyoldalas alkalmazás](scenario-spa-overview.md) <br/>[![Egyoldalas alkalmazás hitelesítése](media/scenarios/spa-app-auth.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [Egyoldalas alkalmazás](scenario-spa-overview.md) <br/>[![Egyoldalas alkalmazás implicit](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [Felhasználót bejelentkeztető webalkalmazás](scenario-web-app-sign-user-overview.md) <br/>[![Webes alkalmazás, amely bejelentkezik a felhasználók számára](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core ![MSAL csomópont](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL csomópont<br/>| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core ![MSAL csomópont](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL csomópont<br/>| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core ![MSAL csomópont](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL csomópont<br/>
| [Webes API-kat hívó webalkalmazás](scenario-web-app-call-api-overview.md) <br/> <br/>[![Webes API-kat hívó webalkalmazás](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png) <br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Lombik + MSAL Python ![MSAL csomópont](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL csomópont<br/>| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Lombik + MSAL Python ![MSAL csomópont](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL csomópont<br/>| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Lombik + MSAL Python ![MSAL csomópont](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL csomópont<br/>
| [Webes API-kat hívó asztali alkalmazás](scenario-desktop-overview.md) <br/> <br/>[ ![ Webes API-kat](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) ![ meghívó asztali alkalmazás Eszköz kódjának folyamata](media/scenarios/device-code-flow-app.svg) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![MSAL csomópont](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL csomópont<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![MSAL csomópont](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL csomópont<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python <br/> ![MSAL csomópont](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL csomópont<br/> ![iOS/Objective C vagy SWIFT](media/sample-v2-code/small_logo_iOS.png) MSAL. ObjC |
| [Webes API-kat hívó mobilalkalmazás](scenario-mobile-overview.md) <br/> [![Webes API-kat hívó mobilalkalmazás](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![UWP](media/sample-v2-code/small_logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/small_logo_xamarin.png) MSAL.NET | | | ![iOS/Objective C vagy SWIFT](media/sample-v2-code/small_logo_iOS.png) MSAL. ObjC | ![Android](media/sample-v2-code/small_logo_Android.png) MSAL. Android
| [Démonalkalmazások](scenario-daemon-overview.md) <br/> [![Démonalkalmazások](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![MSAL csomópont](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL csomópont<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![MSAL csomópont](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL csomópont<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![MSAL csomópont](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL csomópont<br/>
| [Webes API-kat hívó webes API](scenario-web-api-call-api-overview.md) <br/><br/> [![Webes API-kat hívó webes API](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![MSAL csomópont](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL csomópont<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![MSAL csomópont](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL csomópont<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![MSAL csomópont](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL csomópont<br/>

További információ: [Microsoft Identity platform Authentication librarys](reference-v2-libraries.md).

## <a name="next-steps"></a>Következő lépések

* További információ a [hitelesítés alapjairól](./authentication-vs-authorization.md) és [a hozzáférési jogkivonatokról a Microsoft Identity platformon](access-tokens.md).
* További információ a [IoT-alkalmazásokhoz való hozzáférés biztonságossá tételéről](/azure/architecture/example-scenario/iot-aad/iot-aad).
