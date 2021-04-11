---
title: OAuth 2,0 ügyfél-hitelesítő adatok folyamata a Microsoft Identity platformon | Azure
description: Hozzon létre webalkalmazásokat a OAuth 2,0 hitelesítési protokoll Microsoft Identity platform-implementációjának használatával.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 4/1/2021
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 8b3e27f266551eb7425d4801d14261a5e428e4c2
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168110"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-client-credentials-flow"></a>Microsoft Identity platform és a OAuth 2,0 ügyfél-hitelesítő adatok folyamata

Használhatja az RFC 6749-ben megadott [OAuth 2,0 ügyfél-hitelesítő adatokat](https://tools.ietf.org/html/rfc6749#section-4.4) , más néven *kétlábú OAuth*, hogy a webkiszolgálók hozzáférhessenek az alkalmazások identitásának használatával. Ez az engedélytípus általában olyan kiszolgálóközi interakciók esetén használatos, amelyeknek a felhasználóval való azonnali interakció nélkül, a háttérben kell futniuk. Ezeket az alkalmazásokat gyakran *démonoknak* vagy *szolgáltatásfiókoknek* nevezzük.

Ez a cikk azt ismerteti, hogyan lehet programozni közvetlenül az alkalmazás protokollját. Ha lehetséges, javasoljuk, hogy a támogatott Microsoft hitelesítési kódtárakat (MSAL) használja a [jogkivonatok beszerzése és a biztonságos webes API-k hívása](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)helyett.  Tekintse meg az MSAL-t [használó példákat](sample-v2-code.md)is.

A OAuth 2,0 ügyfél-hitelesítő adatok engedélyezési folyamata lehetővé teszi egy webszolgáltatás (bizalmas ügyfél) számára a saját hitelesítő adatainak használatát a felhasználó megszemélyesítése helyett a hitelesítéshez egy másik webszolgáltatás hívásakor. A Microsoft Identity platform lehetővé teszi, hogy a hívó szolgáltatás hitelesítő adatként használjon tanúsítványokat (közös titok helyett).  Mivel az alkalmazások saját hitelesítő adatai vannak használatban, ezeket a hitelesítő adatokat biztonságban kell tartani – _Soha ne_ tegye közzé ezt a hitelesítő adatot a forráskódban, ágyazza be a weblapokba, vagy használja azt egy széles körben elterjedt natív alkalmazásban. 

Az ügyfél-hitelesítő adatok folyamatában az engedélyek közvetlenül az alkalmazáshoz adhatók meg a rendszergazda számára. Ha az alkalmazás jogkivonatot jelenít meg egy erőforráshoz, az erőforrás azt kényszeríti, hogy maga az alkalmazás engedélyezte a művelet végrehajtását, mert nincs olyan felhasználó, aki részt vesz a hitelesítésben.  Ez a cikk az alkalmazások API-nak való [meghívásához](#application-permissions)szükséges lépéseket, valamint [az API meghívásához szükséges jogkivonatok beszerzését](#get-a-token)ismerteti.

## <a name="protocol-diagram"></a>Protokoll diagramja

A teljes ügyfél-hitelesítő adatok folyamata az alábbi ábrához hasonlóan néz ki. A cikk későbbi részében leírt lépéseket ismertetjük.

![Az ügyfél-hitelesítő adatok folyamatát ábrázoló diagram](./media/v2-oauth2-client-creds-grant-flow/convergence-scenarios-client-creds.svg)

## <a name="get-direct-authorization"></a>Közvetlen engedély kérése

Az alkalmazások általában közvetlen engedélyt kapnak az erőforrásokhoz való hozzáférésre a következő két módszer egyikével:

* [Hozzáférés-vezérlési lista (ACL) használatával az erőforráson](#access-control-lists)
* [Alkalmazás-engedélyezési hozzárendelés az Azure AD-ben](#application-permissions)

Ez a két módszer a leggyakoribb az Azure AD-ben, és azt javasoljuk, hogy az ügyfél és a hitelesítő adatok folyamatát végző erőforrások számára. Egy erőforrás más módokon is dönthet az ügyfelek hitelesítéséhez. Minden erőforrás-kiszolgáló kiválaszthatja azt a metódust, amely a leginkább értelmezi az alkalmazását.

### <a name="access-control-lists"></a>Hozzáférés-vezérlési listák

Egy erőforrás-szolgáltató érvényesítheti az engedélyezési ellenőrzési listát az általa ismert alkalmazás-(ügyfél-) azonosítók alapján, és egy adott hozzáférési szintet biztosít a számára. Ha az erőforrás jogkivonatot kap a Microsoft Identity platformtól, az képes dekódolni a jogkivonatot, és kinyerni az ügyfél alkalmazás-AZONOSÍTÓját a `appid` és a `iss` jogcímek közül. Ezután összehasonlítja az alkalmazást egy általa fenntartott hozzáférés-vezérlési listával (ACL). Az ACL részletessége és metódusa jelentősen változhat az erőforrások között.

Gyakori használati eset az, ha egy ACL-t használ a webalkalmazásokhoz vagy webes API-hoz való tesztek futtatásához. A webes API a teljes engedélyek egy részhalmazát is megadhatja egy adott ügyfél számára. A végpontok közötti tesztek az API-on való futtatásához hozzon létre egy teszt-ügyfelet, amely a Microsoft Identity platformból szerzi be a jogkivonatokat, majd elküldi azokat az API-nak. Az API ezt követően ellenőrzi az ügyfél alkalmazás-AZONOSÍTÓJÁHOZ tartozó ACL-t az API teljes funkcionalitásának teljes körű eléréséhez. Ha ezt a típusú ACL-t használja, ügyeljen arra, hogy ne csak a hívó értékét ellenőrizze, `appid` hanem azt is, hogy a `iss` jogkivonat értéke megbízható-e.

Az ilyen típusú hitelesítés olyan démonok és szolgáltatásfiókok esetében fordul elő, amelyek a személyes Microsoft-fiókkal rendelkező fogyasztói felhasználók tulajdonában lévő adatforgalomhoz szükségesek. A szervezetek által birtokolt adattárolók esetében javasoljuk, hogy az alkalmazás engedélyein keresztül szerezze be a szükséges engedélyeket.

#### <a name="controlling-tokens-without-the-roles-claim"></a>Jogkivonatok szabályozása a `roles` jogcím nélkül

Az ACL-alapú engedélyezési minta engedélyezéséhez az Azure AD nem igényli, hogy az alkalmazások jogosultak legyenek jogkivonatok beszerzésére egy másik alkalmazáshoz. Így az alkalmazáshoz tartozó jogkivonatok jogcímek nélkül is kiállíthatók `roles` . Az API-k számára elérhetővé tenni kívánt alkalmazásoknak engedélyeket kell alkalmazniuk a jogkivonatok fogadásához.

Ha szeretné megakadályozni, hogy az alkalmazások csak a csak az alkalmazáshoz tartozó hozzáférési jogkivonatokat férhessenek hozzá az alkalmazáshoz, [Győződjön meg arról, hogy a felhasználói hozzárendelési követelmények engedélyezve vannak](../manage-apps/assign-user-or-group-access-portal.md#configure-an-application-to-require-user-assignment)az alkalmazáshoz. Ez letiltja a felhasználók és az alkalmazások hozzárendelését anélkül, hogy hozzá lehessen adni az alkalmazáshoz tartozó jogkivonatot. 

### <a name="application-permissions"></a>Alkalmazás engedélyei

Az ACL-ek használata helyett API-k használatával teheti elérhetővé az **alkalmazások engedélyeit**. Egy alkalmazás engedélyt kap egy alkalmazás számára egy szervezet rendszergazdája, és csak az adott szervezet és alkalmazottai által birtokolt adathozzáféréshez használható. Microsoft Graph például több alkalmazás-engedélyt tesz elérhetővé a következők elvégzéséhez:

* Levelek olvasása az összes postaládában
* Levelek olvasása és írása az összes postaládában
* E-mail küldése bármely felhasználóként
* Címtáradatok olvasása

Ha az alkalmazás engedélyeit saját API-val szeretné használni (a Microsoft Graph helyett), először ki kell jelölnie [az API](quickstart-configure-app-expose-web-apis.md) -t az API alkalmazás-regisztrálási hatókörének meghatározásával a Azure Portalban. Ezután [konfigurálja az API-hoz való hozzáférést](quickstart-configure-app-access-web-apis.md) úgy, hogy kiválasztja ezeket az engedélyeket az ügyfélalkalmazás alkalmazásának regisztrációjában. Ha nem adott meg hatókört az API alkalmazás-regisztrációjában, nem fog tudni megadnia az alkalmazás engedélyeit az adott API számára az ügyfélalkalmazás alkalmazás-regisztrációjában a Azure Portal.

Ha alkalmazásként (a felhasználóval ellentétben) végez hitelesítést, nem használhat *delegált engedélyeket* – a felhasználók által megadott hatóköröket. Az alkalmazáshoz vagy a webes API általi előzetes engedélyezéshez olyan alkalmazás-engedélyeket kell használni, amelyeket szerepköröknek is neveznek.

További információ az alkalmazás engedélyeiről: [engedélyek és beleegyezik](v2-permissions-and-consent.md#permission-types).

#### <a name="recommended-sign-the-user-into-your-app"></a>Ajánlott: a felhasználó aláírása az alkalmazásba

Az alkalmazás-engedélyeket használó alkalmazások létrehozásakor általában az alkalmazásnak olyan oldalra vagy nézetre van szüksége, amelyen a rendszergazda jóváhagyja az alkalmazás engedélyeit. Ez az oldal lehet az alkalmazás bejelentkezési folyamatának része, az alkalmazás beállításainak egy része, vagy egy dedikált "kapcsolat" folyamat is. Sok esetben érdemes megmutatni, hogy az alkalmazás csak akkor jelenjen meg ez a "kapcsolódás" nézet, ha egy felhasználó munkahelyi vagy iskolai Microsoft-fiók bejelentkezett.

Ha aláírja a felhasználót az alkalmazásba, azonosíthatja azt a szervezetet, amelyhez a felhasználó tartozik, mielőtt megkéri a felhasználót, hogy hagyja jóvá az alkalmazás engedélyeit. Bár ez nem feltétlenül szükséges, a segítségével könnyebben hozhat létre intuitív felhasználói élményt. A felhasználó a alkalmazásban való aláírásához kövesse a [Microsoft Identity platform protokoll oktatóanyagokat](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Engedélyek kérése egy címtár-rendszergazdától

Ha készen áll arra, hogy engedélyt kérjen a szervezet rendszergazdájától, átirányíthatja a felhasználót a Microsoft Identity platform *rendszergazdai engedélyezési végpontján*.

> [!TIP]
> Próbálja meg végrehajtani a kérelmet postán! (Saját alkalmazás-azonosító használata a legjobb eredményekhez – az oktatóanyag alkalmazás nem kér hasznos engedélyeket.) [ ![ Próbálja meg futtatni ezt a kérelmet postán](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

Pro Tipp: próbálja meg beilleszteni a következő kérést egy böngészőben.

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Paraméter | Feltétel | Leírás |
| --- | --- | --- |
| `tenant` | Kötelező | Az a címtár-bérlő, amelyre engedélyt szeretne kérni. Ez lehet a GUID vagy a felhasználóbarát név formátuma. Ha nem tudja, hogy a felhasználó melyik bérlőhöz tartozik, és szeretne bejelentkezni bármelyik Bérlővel, használja a következőt: `common` . |
| `client_id` | Kötelező | Az alkalmazáshoz hozzárendelt [Azure Portal – Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) felhasználói felület **(ügyfél) azonosítója** . |
| `redirect_uri` | Kötelező | Az az átirányítási URI, ahová az alkalmazásnak el kell juttatnia a választ a kezelésére. Pontosan meg kell egyeznie a portálon regisztrált átirányítási URI-k egyikével, azzal a különbséggel, hogy az URL-címet kell kódolni, és további elérésiút-szegmensekkel is rendelkezhet. |
| `state` | Ajánlott | A kérelemben szereplő, a jogkivonat-válaszban is visszaadott érték. Bármely kívánt tartalom sztringje lehet. Az állapot az alkalmazásban a felhasználó állapotára vonatkozó információk kódolására szolgál a hitelesítési kérelem végrehajtása előtt, például az oldal vagy a nézet megtekintését. |

Ezen a ponton az Azure AD azt kényszeríti, hogy csak a bérlői rendszergazda jelentkezhet be a kérelembe. A rendszer felkéri a rendszergazdát, hogy fogadja el az alkalmazás regisztrálásához szükséges összes közvetlen alkalmazást az alkalmazás regisztrációs portálján.

##### <a name="successful-response"></a>Sikeres válasz

Ha a rendszergazda jóváhagyja az alkalmazás engedélyeit, a sikeres válasz a következőképpen néz ki:

```HTTP
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Paraméter | Leírás |
| --- | --- |
| `tenant` | A címtár-bérlő, amely az alkalmazást a kért engedélyekkel rendelkezik, GUID formátumban megadva. |
| `state` | A kérelemben szereplő, a jogkivonat-válaszban is visszaadott érték. Bármely kívánt tartalom sztringje lehet. Az állapot az alkalmazásban a felhasználó állapotára vonatkozó információk kódolására szolgál a hitelesítési kérelem végrehajtása előtt, például az oldal vagy a nézet megtekintését. |
| `admin_consent` | Értéke **true (igaz**). |

##### <a name="error-response"></a>Hiba válasza

Ha a rendszergazda nem hagyja jóvá az alkalmazás engedélyeit, a sikertelen válasz a következőre hasonlít:

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Paraméter | Leírás |
| --- | --- |
| `error` | Hibakód-karakterlánc, amely a hibák típusának osztályozására használható, és amelyek segítségével reagálhat a hibákra. |
| `error_description` | Egy adott hibaüzenet, amely segítséget nyújt a hiba kiváltó okának azonosításában. |

Miután sikeres választ kapott az App kiépítési végponttól, az alkalmazás megszerezte a kért közvetlen alkalmazás-engedélyeket. Most már igényelhet tokent a kívánt erőforráshoz.

## <a name="get-a-token"></a>Jogkivonat beszerzése

Miután megszerezte az alkalmazáshoz szükséges engedélyeket, folytassa az API-k hozzáférési jogkivonatának beszerzésével. Ha a tokent az ügyfél hitelesítő adatainak megadása alapján szeretné lekérni, küldjön egy POST-kérelmet a `/token` Microsoft Identity platformra:

> [!TIP]
> Próbálja meg végrehajtani a kérelmet postán! (Saját alkalmazás-azonosító használata a legjobb eredményekhez – az oktatóanyag alkalmazás nem kér hasznos engedélyeket.) [ ![ Próbálja meg futtatni ezt a kérelmet postán](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Első eset: hozzáférési jogkivonat-kérelem közös titokkal

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

```Bash
# Replace {tenant} with your tenant!
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token'
```

| Paraméter | Feltétel | Leírás |
| --- | --- | --- |
| `tenant` | Kötelező | Az az alkalmazás, amely a (z) GUID-vagy tartománynév-formátumban való működésre tervezi a címtárat. |
| `client_id` | Kötelező | Az alkalmazáshoz hozzárendelt alkalmazás-azonosító. Ezt az információt a portálon találja, ahol regisztrálta az alkalmazást. |
| `scope` | Kötelező | A kérelemben szereplő paraméternek átadott értéknek a `scope` használni kívánt erőforrás azonosítójának (ALKALMAZÁSSPECIFIKUS azonosítójának URI) kell lennie, amelyet az utótaggal kell ellátni `.default` . A Microsoft Graph példában az érték a következő: `https://graph.microsoft.com/.default` . <br/>Ez az érték közli a Microsoft Identity platformot, amely az alkalmazáshoz konfigurált összes közvetlen alkalmazásra érvényes, a végpontnak a használni kívánt erőforráshoz társított jogkivonatot kell kiállítania. A hatókörrel kapcsolatos további tudnivalókért `/.default` tekintse meg a [beleegyezikés dokumentációját](v2-permissions-and-consent.md#the-default-scope). |
| `client_secret` | Kötelező | Az alkalmazás regisztrációs portálján az alkalmazáshoz generált ügyfél-titkos kulcs. Az ügyfél titkos kódjának URL-kódolással kell rendelkeznie a küldés előtt. |
| `grant_type` | Kötelező | Értékre kell állítani `client_credentials` . |

### <a name="second-case-access-token-request-with-a-certificate"></a>Második eset: hozzáférési jogkivonat kérése tanúsítvánnyal

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

| Paraméter | Feltétel | Leírás |
| --- | --- | --- |
| `tenant` | Kötelező | Az az alkalmazás, amely a (z) GUID-vagy tartománynév-formátumban való működésre tervezi a címtárat. |
| `client_id` | Kötelező |Az alkalmazáshoz hozzárendelt alkalmazás (ügyfél) azonosítója. |
| `scope` | Kötelező | A kérelemben szereplő paraméternek átadott értéknek a `scope` használni kívánt erőforrás azonosítójának (ALKALMAZÁSSPECIFIKUS azonosítójának URI) kell lennie, amelyet az utótaggal kell ellátni `.default` . A Microsoft Graph példában az érték a következő: `https://graph.microsoft.com/.default` . <br/>Ez az érték tájékoztatja a Microsoft Identity platformot az alkalmazáshoz konfigurált összes közvetlen alkalmazási engedélyről, ezért a használni kívánt erőforráshoz társított jogkivonatot kell kiállítania. A hatókörrel kapcsolatos további tudnivalókért `/.default` tekintse meg a [beleegyezikés dokumentációját](v2-permissions-and-consent.md#the-default-scope). |
| `client_assertion_type` | Kötelező | Az értéket a következőre kell beállítani: `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` . |
| `client_assertion` | Kötelező | Egy, az alkalmazáshoz hitelesítő adatként regisztrált tanúsítvánnyal rendelkező (JSON webes jogkivonat). Tudnivalók a [tanúsítvány hitelesítő adatairól](active-directory-certificate-credentials.md) : a tanúsítvány regisztrálásának és az állítás formátumának megismerése.|
| `grant_type` | Kötelező | Értékre kell állítani `client_credentials` . |

Figyelje meg, hogy a paraméterek majdnem ugyanazok, mint a közös titok által benyújtott kérelem esetében, kivéve, ha a client_secret paramétert két paraméter helyettesíti: egy client_assertion_type és client_assertion.

### <a name="successful-response"></a>Sikeres válasz

A sikeres válasz így néz ki:

```json
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Paraméter | Leírás |
| --- | --- |
| `access_token` | A kért hozzáférési jogkivonat. Az alkalmazás használhatja ezt a tokent a biztonságos erőforráshoz, például egy webes API-hoz való hitelesítéshez. |
| `token_type` | Megadja a jogkivonat típusának értékét. Az egyetlen típus, amelyet a Microsoft Identity platform támogat `bearer` . |
| `expires_in` | A hozzáférési jogkivonat érvényességi ideje (másodpercben). |

### <a name="error-response"></a>Hiba válasza

A hiba a következőképpen néz ki:

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Paraméter | Leírás |
| --- | --- |
| `error` | Hibakód-karakterlánc, amely a felmerülő hibák típusának besorolására és a hibákra való reagálásra használható. |
| `error_description` | Egy adott hibaüzenet, amely segíthet a hitelesítési hiba kiváltó okának azonosításában. |
| `error_codes` | Az STS-specifikus hibakódok listája, amelyek segíthetnek a diagnosztikaben. |
| `timestamp` | A hiba bekövetkezésének időpontja. |
| `trace_id` | A diagnosztika támogatásához szükséges kérelem egyedi azonosítója. |
| `correlation_id` | Egy egyedi azonosító, amely az egyes összetevőkben a diagnosztika segítésére szolgál. |

## <a name="use-a-token"></a>Token használata

Most, hogy megszerezte a jogkivonatot, a token használatával teheti meg a kéréseket az erőforrásnak. A jogkivonat lejárata után ismételje meg a kérést a `/token` végpontra egy új hozzáférési jogkivonat beszerzéséhez.

```HTTP
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```bash
# Pro tip: Try the following command! (Replace the token with your own.)

curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbG...." 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-samples-and-other-documentation"></a>Példák a kódokra és egyéb dokumentációra

Olvassa el az [ügyfél hitelesítő adatai – áttekintés dokumentációját](https://aka.ms/msal-net-client-credentials) a Microsoft hitelesítési könyvtárából

| Sample | Platform |Leírás |
|--------|----------|------------|
|[Active-Directory-dotnetcore-Daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) | .NET Core 2,1 konzol | Egy egyszerű .NET Core-alkalmazás, amely egy adott bérlő felhasználóit jeleníti meg a Microsoft Graph az alkalmazás identitásával, a felhasználó nevében. A minta azt is szemlélteti, hogy a hitelesítés tanúsítványokat használ a hitelesítéshez. |
|[Active-Directory-DotNet-Daemon-v2](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)|ASP.NET, MVC | Egy webalkalmazás, amely a Microsoft Graph adatait az alkalmazás identitásával szinkronizálja, ahelyett, hogy a felhasználó nevében használja. |
