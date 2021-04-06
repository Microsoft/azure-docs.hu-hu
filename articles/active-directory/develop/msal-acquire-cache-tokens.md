---
title: '& gyorsítótár-tokenek beszerzése a Microsoft Authentication Library (MSAL) szolgáltatással | Azure'
titleSuffix: Microsoft identity platform
description: Tudnivalók a tokenek beszerzéséről és gyorsítótárazásáról a MSAL használatával.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/04/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 98ae81626db637f5b0bd6bfe9e294c32293d09e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755062"
---
# <a name="acquire-and-cache-tokens-using-the-microsoft-authentication-library-msal"></a>Tokenek beszerzése és gyorsítótárazása a Microsoft Authentication Library (MSAL) használatával

A [hozzáférési tokenek](access-tokens.md) lehetővé teszik az ügyfelek számára az Azure által védett webes API-k biztonságos meghívását. A Microsoft Authentication Library (MSAL) segítségével többféleképpen is beszerezheti a jogkivonatokat. Néhány felhasználói interakcióra van szükség egy webböngészőn keresztül, míg másoknak nincs szükségük felhasználói beavatkozásra. Általában a jogkivonat beszerzéséhez használt módszer attól függ, hogy az alkalmazás egy nyilvános ügyfélalkalmazás, például asztali vagy mobil alkalmazás, vagy egy bizalmas ügyfélalkalmazás, például a webalkalmazás, a webes API vagy a démon alkalmazás.

A MSAL a beszerzése után gyorsítótárazza a tokent. Az alkalmazás kódjának először a gyorsítótárból kell próbálkoznia a token más módon történő beszerzésére tett kísérlet előtt.

Törölheti a jogkivonat-gyorsítótárat is, amely a fiókoknak a gyorsítótárból való eltávolításával érhető el. Ez azonban nem távolítja el a böngészőben található munkamenet-cookie-t.

## <a name="scopes-when-acquiring-tokens"></a>Hatókörök a jogkivonatok beszerzése során

A [hatókörök](v2-permissions-and-consent.md) azok az engedélyek, amelyeket a webes API tesz elérhetővé, amelyekkel az ügyfélalkalmazások hozzáférést igényelhetnek. Az ügyfélalkalmazások kérik a felhasználó belefoglalását ezekhez a hatókörökhöz, amikor hitelesítési kéréseket kap a tokenek eléréséhez a webes API-khoz. A MSAL lehetővé teszi, hogy jogkivonatokat kapjon az Azure AD for Developers (v 1.0) és a Microsoft Identity platform API-k eléréséhez. a v 2.0 protokoll hatóköröket használ a kérelmekben lévő erőforrások helyett. További információért olvassa el a következőt: [v 1.0 és v 2.0 összehasonlítás](../azuread-dev/azure-ad-endpoint-comparison.md). Az által elfogadott jogkivonat-verzió webes API-konfigurációja alapján a v 2.0-végpont visszaadja a hozzáférési jogkivonatot a MSAL.

A MSAL jogkivonat-beszerzési módszerei közül több is `scopes` paramétert igényel. A `scopes` paraméter azon karakterláncok listája, amelyek deklarálják a kívánt engedélyeket és a kért erőforrásokat. A jól ismert hatókörök a [Microsoft Graph engedélyek](/graph/permissions-reference).

A MSAL a v 1.0 erőforrásokhoz is hozzáfér. További információ: [hatókörök egy v 1.0-alkalmazáshoz](msal-v1-app-scopes.md).

### <a name="request-scopes-for-a-web-api"></a>Kérelmek hatóköre webes API-hoz

Ha az alkalmazásnak olyan hozzáférési jogkivonatot kell igényelnie, amely adott engedélyekkel rendelkezik egy erőforrás-API-hoz, adja át az API-nak az alkalmazás-azonosító URI-JÁT tartalmazó hatóköröket a formátumban `<app ID URI>/<scope>` .

Néhány példa a különböző erőforrásokra vonatkozó hatókör-értékekre:

- Microsoft Graph API: `https://graph.microsoft.com/User.Read`
- Egyéni webes API: `api://11111111-1111-1111-1111-111111111111/api.read`

A hatókör értékének formátuma a hozzáférési jogkivonatot fogadó erőforrástól (az API-tól) és az `aud` általa elfogadott jogcím-értéktől függően változhat.

Csak Microsoft Graph esetében a `user.read` hatókör leképezése a-re `https://graph.microsoft.com/User.Read` , és mindkét hatókör formátuma használható egyszerre.

Bizonyos webes API-k (például a Azure Resource Manager API) a https://management.core.windows.net/) hozzáférési jogkivonat célközönség-jogcíme () egy záró perjelet ("/") várnak `aud` . Ebben az esetben a hatókört a következőképpen adja át: `https://management.core.windows.net//user_impersonation` , beleértve a dupla továbbítási perjelet ("//").

Más API-k esetében előfordulhat, hogy a hatókör értéke *nem tartalmaz sémát vagy gazdagépet* , és csak az alkalmazás azonosítóját (GUID) és a hatókör nevét várja, például:

`11111111-1111-1111-1111-111111111111/api.read`

> [!TIP]
> Ha az alsóbb rétegbeli erőforrás nem a vezérlő alatt található, akkor előfordulhat, hogy különböző hatókör-értékeket kell kipróbálnia (például séma és gazdagép nélkül), ha a `401` hozzáférési tokent az erőforráshoz továbbítja, vagy más hibaüzenetet kap.

### <a name="request-dynamic-scopes-for-incremental-consent"></a>Dinamikus hatókörök kérése növekményes engedélyhez

Az alkalmazás vagy a hozzá tartozó követelmények által biztosított funkciók megváltozásakor a hatókör paraméter használatával további engedélyeket is igényelhet. Ilyen *dinamikus hatókörök* lehetővé teszik a felhasználók számára, hogy növekményes beleegyezett a hatókörbe.

Előfordulhat például, hogy bejelentkezik a felhasználóba, de kezdetben megtagadja a hozzáférést az erőforrásokhoz. Később a naptár hatókörének megadásával megtekintheti a naptár hatókörét a beszerzési jogkivonat metódusban, és megszerezheti a felhasználó belefoglalását. Például a és a hatókörök bekérésével `https://graph.microsoft.com/User.Read` `https://graph.microsoft.com/Calendar.Read` .

## <a name="acquiring-tokens-silently-from-the-cache"></a>Tokenek csendes beszerzése (a gyorsítótárból)

A MSAL megőrzi a jogkivonat-gyorsítótárat (vagy két gyorsítótárat a bizalmas ügyfélalkalmazások számára), és a beszerzése után gyorsítótárazza a tokent. Sok esetben, ha egy jogkivonat csendes beolvasását kísérli meg, a gyorsítótárban lévő tokenen alapuló további hatókörök is megszerezhetnek egy másik tokent. Emellett képes a jogkivonatok frissítésére is, ha a lejárathoz közeledik (mivel a jogkivonat-gyorsítótár frissítési jogkivonatot is tartalmaz).

### <a name="recommended-call-pattern-for-public-client-applications"></a>A nyilvános ügyfélalkalmazások ajánlott hívási mintája

Az alkalmazás kódjának először a gyorsítótárból kell próbálkoznia a jogkivonat lekéréséhez. Ha a metódus hívása "felhasználói felület szükséges" hibát vagy kivételt ad vissza, próbálkozzon más módon a jogkivonat beszerzésével.

Két folyamat van azonban, amelyekben **nem** próbálkozhat a jogkivonat csendes beszerzésével:

- Az [ügyfél-hitelesítő adatok folyamata](msal-authentication-flows.md#client-credentials), amely nem használja a felhasználói jogkivonat gyorsítótárát, de az alkalmazás-jogkivonat gyorsítótára. Ez a módszer gondoskodik az alkalmazás-jogkivonat gyorsítótárának ellenőrzéséről, mielőtt elküld egy kérést a biztonsági jogkivonat szolgáltatásnak (STS).
- Az [engedélyezési kód](msal-authentication-flows.md#authorization-code) a web Appsben történik, mivel bevált egy olyan kódot, amelyet az alkalmazás a felhasználó bejelentkezésekor kapott, és amelyhez több hatókör is beleegyezik. Mivel a kód nem ad át egy fiókot paraméterként, a metódus a kód beváltását megelőzően nem tud megjelenni a gyorsítótárban, amely a szolgáltatás hívását hívja meg.

### <a name="recommended-call-pattern-in-web-apps-using-the-authorization-code-flow"></a>Ajánlott hívási minta a Web Apps alkalmazásban az engedélyezési kód folyamatával

Az [OpenID Connect engedélyezési kódját](v2-protocols-oidc.md)használó webalkalmazások esetében a vezérlők ajánlott mintája a következő:

- Egy bizalmas ügyfélalkalmazás létrehozása a jogkivonat-gyorsítótárral testreszabott szerializálással.
- A jogkivonat beszerzése az engedélyezési kód folyamatábrájának használatával

## <a name="acquiring-tokens"></a>Jogkivonatok beszerzése

Általában a token beszerzésének módja attól függ, hogy nyilvános ügyfél vagy bizalmas ügyfélalkalmazás-e.

### <a name="public-client-applications"></a>Nyilvános ügyfélalkalmazások

A nyilvános ügyfélalkalmazások (asztali vagy mobil alkalmazások) esetében:

- A tokenek gyakran interaktív módon szerezhetők be, és a felhasználó felhasználói felület vagy előugró ablak használatával jelentkeznek be.
- [Tokent kaphat a bejelentkezett felhasználó számára](msal-authentication-flows.md#integrated-windows-authentication) az integrált Windows-hitelesítés (IWA/Kerberos) használatával, ha az asztali alkalmazás egy tartományhoz vagy az Azure-hoz csatlakoztatott Windows-számítógépen fut.
- A .NET-keretrendszer asztali ügyfélalkalmazások [számára a felhasználónévvel és jelszóval rendelkező tokent kaphat](msal-authentication-flows.md#usernamepassword) (nem ajánlott). Ne használja a username/Password nevet a bizalmas ügyfélalkalmazások számára.
- A nem webböngészővel rendelkező eszközökön futó alkalmazások [programkód-folyamatán](msal-authentication-flows.md#device-code) keresztül kaphat jogkivonatot. A felhasználónak egy URL-címmel és egy kóddal kell megadnia egy webböngészőt, és be kell írnia a kódot, és be kell jelentkeznie. Az Azure AD ezután visszaküldi a jogkivonatot a böngésző nélküli eszközre.

### <a name="confidential-client-applications"></a>Bizalmas ügyfélalkalmazások

A bizalmas ügyfélalkalmazások (webalkalmazások, webes API-k vagy egy Windows-szolgáltatáshoz hasonló Daemon-alkalmazások) esetén:

- Az [ügyfél hitelesítő adatainak](msal-authentication-flows.md#client-credentials)beszerzésével **saját maga is megvásárolhatja az alkalmazáshoz** tartozó jogkivonatokat, nem pedig felhasználó számára. Ezzel a technikával olyan eszközöket vagy eszközöket lehet szinkronizálni, amelyek általános és nem egy adott felhasználó számára dolgozzák fel a felhasználókat.
- A webes API-k által használt, a felhasználó nevében egy API-t meghívó [helyszíni folyamat](msal-authentication-flows.md#on-behalf-of) . Az alkalmazás ügyfél-hitelesítő adatokkal van azonosítva, hogy felhasználói állítás (SAML, például vagy JWT token) alapján szerezzen be jogkivonatot. Ezt a folyamatot olyan alkalmazások használják, amelyeknek hozzá kell férniük egy adott felhasználó erőforrásaihoz a szolgáltatások közötti hívásokban.
- Jogkivonatok beszerzése a webalkalmazások [engedélyezési kódjának](msal-authentication-flows.md#authorization-code) használatával, miután a felhasználó bejelentkezik az engedélyezési kérelem URL-címén. Az OpenID Connect alkalmazás általában ezt a mechanizmust használja, amely lehetővé teszi a felhasználó számára a bejelentkezést az Open ID Connect használatával, majd a felhasználó nevében hozzáférni a webes API-khoz.

## <a name="authentication-results"></a>Hitelesítési eredmények

Amikor az ügyfél hozzáférési jogkivonatot kér, az Azure AD egy hitelesítési eredményt is ad vissza, amely tartalmazza a hozzáférési jogkivonat metaadatait. Ez az információ tartalmazza a hozzáférési jogkivonat lejárati idejét, valamint azokat a hatóköröket, amelyekhez érvényes. Ezek az adatmennyiség lehetővé teszi, hogy az alkalmazás a hozzáférési tokenek elemzése nélkül végezzen intelligens gyorsítótárazást a hozzáférési jogkivonatok használatával. A hitelesítési eredmények a következőket teszik elérhetővé:

- A webes API [hozzáférési jogkivonata](access-tokens.md) az erőforrások eléréséhez. Ez a karakterlánc általában Base64 kódolású JWT, de az ügyfélnek soha nem kell megkeresnie a hozzáférési jogkivonatban. A formátum nem garantált, hogy stabil maradjon, és titkosítva lehet az erőforrás számára. Az ügyfél hozzáférési jogkivonatának tartalmától függően a kód írása a leggyakoribb hibák egyike, az ügyfél logikája pedig törés.
- A felhasználó [azonosító jogkivonata](id-tokens.md) (JWT).
- A jogkivonat lejárata, amely a jogkivonat lejárati dátumát és időpontját jelzi.
- A bérlő azonosítója tartalmazza azt a bérlőt, amelyben a felhasználó található. A vendég felhasználók (Azure AD B2B forgatókönyvek) esetében a bérlői azonosító a vendég bérlő, nem az egyedi bérlő. Ha a tokent egy felhasználó nevében küldi el a rendszer, a hitelesítési eredmény a felhasználóval kapcsolatos információkat is tartalmaz. A bizalmas ügyfél-folyamatok esetében, amelyekben a rendszer a jogkivonatokat nem felhasználó (az alkalmazás esetében) kéri, ez a felhasználói információ null értékű.
- Azok a hatókörök, amelyekhez a jogkivonat ki lett állítva.
- A felhasználó egyedi azonosítója.

## <a name="advanced-accessing-the-users-cached-tokens-in-background-apps-and-services"></a>Speciális A felhasználó gyorsítótárazott jogkivonatának elérése a háttérben futó alkalmazásokban és szolgáltatásokban

[!INCLUDE [advanced-token-caching](../../../includes/advanced-token-cache.md)]

## <a name="next-steps"></a>Következő lépések

A MSAL által támogatott platformok közül több további jogkivonat-gyorsítótárral kapcsolatos információkkal rendelkezik a platform könyvtárának dokumentációjában. Például:
- [Jogkivonat beszerzése a jogkivonat-gyorsítótárból a MSAL.NET használatával](msal-net-acquire-token-silently.md)
- [Egyszeri bejelentkezés az MSAL.js-sel](msal-js-sso.md)
- [Egyéni jogkivonat-gyorsítótár szerializálása a MSAL-ben a Pythonhoz](msal-python-token-cache-serialization.md)
- [Egyéni jogkivonat-gyorsítótár szerializálás a MSAL-ben Javához](msal-java-token-cache-serialization.md)
