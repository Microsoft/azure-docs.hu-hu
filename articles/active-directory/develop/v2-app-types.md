---
title: A Microsoft Identity platform alkalmazási típusai | Azure
description: A Microsoft Identity platform által támogatott alkalmazások és forgatókönyvek típusai.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev, fasttrack-edit, contperf-fy21q2
ms.openlocfilehash: 7ec309f016e73642262399bd75e7b5146bc5e497
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98752784"
---
# <a name="application-types-for-the-microsoft-identity-platform"></a>A Microsoft Identity platform alkalmazási típusai

A Microsoft Identity platform számos modern alkalmazás-architektúra hitelesítését támogatja, amelyek mindegyike az iparági szabványoknak megfelelő protokollok, az [2,0-es vagy az OpenID Connect OAuth](active-directory-v2-protocols.md)alapul. Ez a cikk a Microsoft Identity platform használatával felépíthető alkalmazások típusait ismerteti, az Ön által választott nyelvtől és platformtól függetlenül. Az információ úgy van kialakítva, hogy segítsen megérteni a magas szintű forgatókönyveket, mielőtt elkezdi a kód használatát az [alkalmazási forgatókönyvekben](authentication-flows-app-scenarios.md#application-scenarios).

## <a name="the-basics"></a>Az alapok

Regisztrálnia kell minden olyan alkalmazást, amely a Microsoft Identity platformot használja a Azure Portal [Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908). Az alkalmazás regisztrációs folyamata összegyűjti és hozzárendeli ezeket az értékeket az alkalmazáshoz:

* Egy **alkalmazás-(ügyfél-) azonosító** , amely egyedileg azonosítja az alkalmazást
* Egy **átirányítási URI** , amely a válaszok visszairányítására használható az alkalmazásba
* Néhány más forgatókönyv-specifikus érték, például a támogatott fióktípus

Részletekért olvassa el az [alkalmazások regisztrálását](quickstart-register-app.md)ismertető témakört.

Az alkalmazás regisztrálása után az alkalmazás kommunikál a Microsoft Identity platformmal, ha kéréseket küld a végpontnak. Nyílt forráskódú keretrendszereket és könyvtárakat biztosítunk, amelyek kezelik a kérelmek részleteit. Lehetősége van arra is, hogy saját maga is megvalósítsa a hitelesítési logikát, ha kéréseket hoz létre ezekhez a végpontokhoz:

```HTTP
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>Egyoldalas alkalmazások (JavaScript)

Számos modern alkalmazás rendelkezik egy egyoldalas alkalmazás-előtérrel, amely elsősorban a JavaScriptben íródott, gyakran olyan keretrendszerrel, mint a szögletes, a reakciós vagy a Vue. A Microsoft Identity platform támogatja ezeket az alkalmazásokat az [OpenID Connect](v2-protocols-oidc.md) protokoll használatával történő hitelesítéshez, illetve az [OAuth 2,0 implicit engedélyezési folyamat](v2-oauth2-implicit-grant-flow.md) vagy a legújabb [OAUTH 2,0 engedélyezési kód + PKCE folyamat](v2-oauth2-auth-code-flow.md) engedélyezéséhez (lásd alább).

Az alábbi folyamatábra a OAuth 2,0 engedélyezési kód engedélyezését mutatja be (a PKCE kihagyott részletekkel), ahol az alkalmazás kódot kap a Microsoft Identity platform `authorize` végponttól, és visszaváltja a tokenekhez, és frissíti a tokeneket a helyek közötti webes kérelmek használatával. A frissítési jogkivonat 24 óránként lejár, és az alkalmazásnak egy másik kódot kell igényelnie. A hozzáférési tokenen kívül a `id_token` bejelentkezett felhasználót az ügyfélalkalmazás számára általában ugyanarra a folyamatra és/vagy egy külön OpenID Connect-kérelemre is kéri (itt nem látható).

:::image type="content" source="media/v2-oauth-auth-code-spa/active-directory-oauth-code-spa.svg" alt-text="A OAuth 2 engedélyezési kód egy egyoldalas alkalmazás és a biztonsági jogkivonat szolgáltatás végpontja közötti folyamatát ábrázoló diagram." border="false":::

Ha szeretné megtekinteni ezt a forgatókönyvet működés közben, tekintse meg az [oktatóanyagot: Jelentkezzen be a felhasználókba, és hívja meg a Microsoft Graph API-t egy JavaScript Spa-beli hitelesítési kód használatával](tutorial-v2-javascript-auth-code.md).

### <a name="authorization-code-flow-vs-implicit-flow"></a>Engedélyezési kód folyamatábrája és implicit folyamata

A OAuth 2,0-es verziójának többsége esetében az [implicit folyamat](v2-oauth2-implicit-grant-flow.md) az ajánlott módszer az egylapos alkalmazások létrehozására. A [harmadik féltől származó cookie-k](reference-third-party-cookies-spas.md) eltávolításával, valamint az implicit folyamat körüli biztonsági vonatkozásokkal járó [nagyobb odafigyeléssel](https://tools.ietf.org/html/draft-ietf-oauth-security-topics-14) a rendszer áthelyezte az egyoldalas alkalmazások engedélyezési kódjának folyamatát.

Az alkalmazás Safariban és más adatvédelmi böngészőkben való kompatibilitásának biztosításához már nem javasoljuk az implicit folyamat használatát, ehelyett javasoljuk az engedélyezési kód áramlását.

## <a name="web-apps"></a>Webalkalmazások

Webalkalmazásokhoz (.NET, PHP, Java, Ruby, Python, node) a felhasználó egy böngészőben keresztül fér hozzá, az [OpenID Connect](active-directory-v2-protocols.md) felhasználói bejelentkezéshez használható. Az OpenID Connect szolgáltatásban a webalkalmazás megkapja az azonosító tokent. Az azonosító jogkivonat egy biztonsági jogkivonat, amely ellenőrzi a felhasználó identitását, és a jogcímek formájában szolgáltat információkat a felhasználóról:

```JSON
// Partial raw ID token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded ID token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

A Microsoft Identity platformon használt különböző típusú tokenek további részletei a [hozzáférési jogkivonat](access-tokens.md) referenciájában és a [id_token dokumentációjában](id-tokens.md) találhatók.

A webkiszolgáló alkalmazásokban a bejelentkezési hitelesítési folyamat a következő magas szintű lépéseket hajtja végre:

![A webalkalmazás-hitelesítési folyamat megjelenítése](./media/v2-app-types/convergence-scenarios-webapp.svg)

A felhasználó identitását úgy ellenőrizheti, hogy az azonosító tokent a Microsoft Identity platformtól kapott nyilvános aláírási kulccsal ellenőrzi. Be van állítva egy munkamenet-cookie, amely a felhasználó azonosítására szolgál a következő lapokra vonatkozó kérelmeknél.

Ha szeretné megtekinteni ezt a forgatókönyvet működés közben, próbálja ki a [webalkalmazásban a felhasználók forgatókönyvében](scenario-web-app-sign-user-overview.md)megjelenő kód mintáit.

Az egyszerű bejelentkezés mellett előfordulhat, hogy egy webkiszolgáló alkalmazásnak hozzá kell férnie egy másik webszolgáltatáshoz, például egy REST API. Ebben az esetben a webkiszolgáló alkalmazás egy kombinált OpenID Connect és OAuth 2,0 flow-t alkalmaz a [OAuth 2,0 engedélyezési kód folyamatával](v2-oauth2-auth-code-flow.md). További információ erről a forgatókönyvről: [Ismerkedés a Web Apps szolgáltatással és a webes API-](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIDConnect-DotNet)kkal.

## <a name="web-apis"></a>Webes API-k

A Microsoft Identity platform használatával biztonságossá teheti a webszolgáltatásokat, például az alkalmazás REST-alapú webes API-ját. A webes API-kat számos platformon és nyelven lehet megvalósítani. A Azure Functions HTTP-eseményindítók használatával is megvalósítható. Az azonosító tokenek és a munkamenet-cookie-k helyett a webes API egy OAuth 2,0 hozzáférési tokent használ az adatai védelméhez és a bejövő kérések hitelesítéséhez. A webes API hívója hozzáfűz egy hozzáférési jogkivonatot egy HTTP-kérelem engedélyezési fejlécében, például a következőhöz:

```HTTP
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

A webes API a hozzáférési jogkivonattal ellenőrzi az API-hívó identitását, és Kinyeri a hívó információit a hozzáférési jogkivonatban kódolt jogcímekről. A Microsoft Identity platformon használt különböző típusú tokenek további részletei a [hozzáférési jogkivonat](access-tokens.md) referenciájában és a [id_token](id-tokens.md) -referenciában érhetők el.

A webes API-k lehetővé teszik a felhasználók számára, hogy a jogosultságok, más néven [hatókörök](v2-permissions-and-consent.md)megadásával vagy letiltásával eldönthetik, hogy bizonyos funkciókat vagy adatokból választhatnak. Ahhoz, hogy egy hívó alkalmazás egy hatókörhöz engedélyt szerezzen, a felhasználónak a folyamat során hozzá kell járulnia a hatókörhöz. A Microsoft Identity platform megkéri a felhasználótól az engedélyt, majd rögzíti az engedélyeket a webes API által fogadott összes hozzáférési jogkivonatban. A webes API ellenőrzi az egyes hívásokhoz kapott hozzáférési jogkivonatokat, és végrehajtja az engedélyezési ellenőrzéseket.

A webes API-k bármilyen típusú alkalmazásból fogadhatnak hozzáférési jogkivonatokat, beleértve a webkiszolgáló alkalmazásokat, az asztali és a mobil alkalmazásokat, az egyoldalas alkalmazásokat, a kiszolgálóoldali démonokat és akár más webes API-kat is. A webes API-k magas szintű folyamata így néz ki:

![A webes API-hitelesítési folyamat megjelenítése](./media/v2-app-types/convergence-scenarios-webapi.svg)

Ha szeretné megtudni, hogyan védheti meg a webes API-t a OAuth2 hozzáférési jogkivonatok használatával, tekintse meg a webes API-kód mintáit a [védett webes API-forgatókönyvben](scenario-protected-web-api-overview.md).

Sok esetben a webes API-knak a Microsoft Identity platform által védett más, az alsóbb rétegbeli webes API-khoz is el kell végezniük a kimenő kérelmeket. Ehhez a webes API **-** k kihasználhatják a folyamaton kívüli folyamatot, amely lehetővé teszi a webes API számára a bejövő hozzáférési tokenek cseréjét egy másik hozzáférési jogkivonat számára, amelyet a kimenő kérésekben használni fog. További információ: [Microsoft Identity platform és OAuth 2,0 on-Half-of flow](v2-oauth2-on-behalf-of-flow.md).

## <a name="mobile-and-native-apps"></a>Mobil- és natív alkalmazások

Az eszközre telepített alkalmazások, például a mobil-és asztali alkalmazások, gyakran olyan háttér-szolgáltatásokhoz vagy webes API-khoz szükségesek, amelyek az adatok tárolását és a függvények a felhasználók nevében való elvégzését végzik. Ezek az alkalmazások a [OAuth 2,0 engedélyezési kód folyamatával](v2-oauth2-auth-code-flow.md)adhatnak hozzá bejelentkezést és engedélyezést a háttér-szolgáltatásokhoz.

Ebben a folyamatban az alkalmazás megkapja a Microsoft Identity platform engedélyezési kódját, amikor a felhasználó bejelentkezik. Az engedélyezési kód az alkalmazásnak a bejelentkezett felhasználó nevében történő meghívására vonatkozó engedélyét jelöli. Az alkalmazás a háttérben egy OAuth 2,0 hozzáférési jogkivonat és egy frissítési jogkivonat számára is kicserélheti az engedélyezési kódot. Az alkalmazás a hozzáférési token használatával hitelesíti a webes API-kat a HTTP-kérelmekben, és a frissítési token használatával új hozzáférési jogkivonatokat kérhet le, ha a régebbi hozzáférési tokenek lejárnak.

![Megjeleníti a natív alkalmazás hitelesítési folyamatát.](./media/v2-app-types/convergence-scenarios-native.svg)

> [!NOTE]
> Ha az alkalmazás az alapértelmezett webnézetet használja, tekintse meg a "bejelentkezési adatok megerősítése" funkció és a hibakód AADSTS50199 az [Azure ad-hitelesítési és-engedélyezési hibakódokban](reference-aadsts-error-codes.md)című témakört.

## <a name="daemons-and-server-side-apps"></a>Démonok és kiszolgálóoldali alkalmazások

A hosszan futó folyamatokkal rendelkező vagy a felhasználóval való interakció nélkül működő alkalmazások esetében is szükség van a biztonságos erőforrásokhoz, például a webes API-khoz való hozzáférésre. Ezek az alkalmazások a felhasználó delegált identitása helyett az alkalmazás identitását használva hitelesíthetők és lekérhetik a jogkivonatokat a OAuth 2,0 ügyfél-hitelesítő adatokkal. Az alkalmazás identitását az ügyfél titkos kódjával vagy tanúsítványával igazolhatja. További információ: [.net Core Daemon Console Application a Microsoft Identity platform használatával](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2).

Ebben a folyamatban az alkalmazás közvetlenül a `/token` végpontot használja a hozzáférés eléréséhez:

![A démon-alkalmazás hitelesítési folyamatát mutatja](./media/v2-app-types/convergence-scenarios-daemon.svg)

Daemon-alkalmazás létrehozásához tekintse meg az [ügyfél hitelesítő adatait tartalmazó dokumentációt](v2-oauth2-client-creds-grant-flow.md), vagy próbálkozzon egy [.net-minta alkalmazással](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).

## <a name="next-steps"></a>Következő lépések

Most, hogy már ismeri a Microsoft Identity platform által támogatott típusú alkalmazásokat, ismerkedjen meg a [OAuth 2,0 és az OpenID Connecttel](active-directory-v2-protocols.md) , hogy megértse a különböző forgatókönyvekben használt protokoll-összetevőket.
