---
title: Microsoft Identity platform és OAuth 2,0 engedélyezési kód flow | Azure
titleSuffix: Microsoft identity platform
description: Webalkalmazásokat készíthet a OAuth 2,0 hitelesítési protokoll Microsoft Identity platform-implementációjának használatával.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: aeed031025b9c494b35886861c273e2a7f9d2ac4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101653728"
---
# <a name="microsoft-identity-platform-and-oauth-20-authorization-code-flow"></a>Microsoft Identity platform és OAuth 2,0 engedélyezési kód folyamatábrája

A OAuth 2,0 engedélyezési kód megadása az eszközre telepített alkalmazásokban használható a védett erőforrásokhoz, például a webes API-khoz való hozzáféréshez. A OAuth 2,0 Microsoft Identity platform-implementációjának használatával bejelentkezhet a mobil-és asztali alkalmazásokhoz való bejelentkezéshez és API-hoz.

Ez a cikk azt ismerteti, hogyan lehet közvetlenül az alkalmazás protokollján keresztül programozni az alkalmazást bármilyen nyelven.  Ha lehetséges, javasoljuk, hogy a támogatott Microsoft hitelesítési kódtárakat (MSAL) használja a [jogkivonatok beszerzése és a biztonságos webes API-k hívása](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)helyett.  Tekintse meg az MSAL-t [használó példákat](sample-v2-code.md)is.

Az OAuth 2,0 engedélyezési kód folyamatát a [OAuth 2,0 specifikáció 4,1. szakasza](https://tools.ietf.org/html/rfc6749)ismerteti. A hitelesítés és az engedélyezés az alkalmazások többségében, többek között [egyoldalas alkalmazásokban](v2-app-types.md#single-page-apps-javascript), [webalkalmazásokban](v2-app-types.md#web-apps)és [natív módon telepített alkalmazásokban](v2-app-types.md#mobile-and-native-apps)történik. A folyamat lehetővé teszi, hogy az alkalmazások biztonságosan beszerezzék access_tokens, amelyek a Microsoft Identity platform által védett erőforrások elérésére használhatók, valamint a tokenek frissítése további access_tokens és azonosító jogkivonatok beszerzéséhez a bejelentkezett felhasználó számára.

## <a name="protocol-diagram"></a>Protokoll diagramja

Magas szinten az alkalmazás teljes hitelesítési folyamata a következőképpen néz ki:

![OAuth-hitelesítési kód folyamatábrája](./media/v2-oauth2-auth-code-flow/convergence-scenarios-native.svg)

## <a name="redirect-uri-setup-required-for-single-page-apps"></a>Az egyoldalas alkalmazásokhoz szükséges átirányítási URI-beállítás

Az egyoldalas alkalmazásokhoz tartozó engedélyezési kód esetében további beállításokra van szükség.  Kövesse az [egyoldalas alkalmazás létrehozásának](scenario-spa-app-registration.md#redirect-uri-msaljs-20-with-auth-code-flow) utasításait, hogy az átirányítási URI-t helyesen megjelölje a CORS. Ha frissíteni szeretne egy meglévő átirányítási URI-t a CORS engedélyezéséhez, nyissa meg a jegyzékfájl-szerkesztőt, és állítsa be az `type` átirányítási URI mezőjét `spa` a `replyUrlsWithType` szakaszba. Az átirányítási URI-ra is kattinthat a hitelesítés lap "web" szakaszában, és kiválaszthatja az áttelepíteni kívánt URI azonosítókat az engedélyezési kód folyamatának használatával.

Az `spa` átirányítás típusa visszamenőlegesen kompatibilis az implicit folyamattal. Azok az alkalmazások, amelyek jelenleg az implicit folyamatot használják a jogkivonatok fogadására, problémák nélkül áthelyezhetők az `spa` átirányítási URI-típusra, és folytatják az implicit folyamat használatát.

Ha megpróbálja használni az engedélyezési kód folyamatát, és ezt a hibaüzenetet látja:

`access to XMLHttpRequest at 'https://login.microsoftonline.com/common/v2.0/oauth2/token' from origin 'yourApp.com' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.`

Ezután keresse fel az alkalmazás regisztrációját, és frissítse az alkalmazás átirányítási URI-JÁT a Type (típus) értékre `spa` .

## <a name="request-an-authorization-code"></a>Engedélyezési kód kérése

Az engedélyezési kód folyamata azzal kezdődik, hogy az ügyfél átirányítja a felhasználót a `/authorize` végpontra. Ebben a kérelemben az ügyfél a `openid` , a `offline_access` és az `https://graph.microsoft.com/mail.read ` engedélyeket kéri a felhasználótól.  Bizonyos engedélyek rendszergazdai korlátozások alá esnek, például a segítségével a szervezet címtárában lévő adatírással `Directory.ReadWrite.All` . Ha az alkalmazás egy szervezeti felhasználótól kéri a fenti engedélyek egyikének elérését, a felhasználó hibaüzenetet kap, amely szerint nem jogosult beleegyezni az alkalmazás engedélyeivel. A rendszergazda által korlátozott hatókörökhöz való hozzáférés kéréséhez közvetlenül egy globális rendszergazdától kell kérnie őket.  További információért olvassa el a [rendszergazda által korlátozott engedélyeket](v2-permissions-and-consent.md#admin-restricted-permissions).

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read%20api%3A%2F%2F
&state=12345
&code_challenge=YTFjNjI1OWYzMzA3MTI4ZDY2Njg5M2RkNmVjNDE5YmEyZGRhOGYyM2IzNjdmZWFhMTQ1ODg3NDcxY2Nl
&code_challenge_method=S256
```

> [!TIP]
> A kérelem végrehajtásához kattintson az alábbi hivatkozásra. A bejelentkezést követően a böngészőt át kell irányítani a `https://localhost/myapp/` `code` címsorába.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Paraméter    | Kötelező/nem kötelező | Description |
|--------------|-------------|--------------|
| `tenant`    | kötelező    | A `{tenant}` kérelem elérési útjának értéke használható annak szabályozására, hogy ki jelentkezhet be az alkalmazásba. Az engedélyezett értékek:,, `common` `organizations` `consumers` és bérlői azonosítók. További részletek: [protokoll alapjai](active-directory-v2-protocols.md#endpoints).  |
| `client_id`   | kötelező    | Az alkalmazáshoz hozzárendelt [Azure Portal – Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) felhasználói felület **(ügyfél) azonosítója** .  |
| `response_type` | kötelező    | Tartalmaznia kell `code` az engedélyezési kód folyamatát. `id_token` `token` A [hibrid folyamat](#request-an-id-token-as-well-hybrid-flow)használatával is felvehető. |
| `redirect_uri`  | kötelező | Az alkalmazás redirect_uri, ahol az alkalmazás elküldhet és fogadhat hitelesítési válaszokat. Pontosan meg kell egyeznie a portálon regisztrált redirect_urisével, kivéve, ha az URL-címet kódolni kell. Natív & Mobile-alkalmazások esetén az ajánlott értékek egyikét kell használnia  `https://login.microsoftonline.com/common/oauth2/nativeclient` (beágyazott böngészőket használó alkalmazások esetén) vagy `http://localhost` (a rendszerböngészőket használó alkalmazásokhoz). |
| `scope`  | kötelező    | Egy szóközzel tagolt lista, melyben a felhasználónak jóvá kell hagynia a [hatókört](v2-permissions-and-consent.md) .  A `/authorize` kérelemhez több erőforrást is képes kiszolgálni, így az alkalmazás több, a hívni kívánt webes API-hoz kap beleegyezik. |
| `response_mode`   | ajánlott | Meghatározza azt a módszert, amelyet az eredményül kapott jogkivonat az alkalmazásba való visszaküldéséhez kell használni. A következők egyike lehet:<br/><br/>- `query`<br/>- `fragment`<br/>- `form_post`<br/><br/>`query` a kódot lekérdezési karakterlánc paraméterként adja meg az átirányítási URI-n. Ha az implicit folyamat használatával kér azonosító jogkivonatot, az `query` [OpenID specifikációban](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations)megadott módon nem használható. Ha csak a kódot kéri, használhatja a, vagy a-t is `query` `fragment` `form_post` . `form_post` végrehajt egy BEJEGYZÉST, amely tartalmazza a kódot az átirányítási URI-nak. |
| `state`                 | ajánlott | A kérelemben szereplő érték, amelyet a rendszer a jogkivonat-válaszban is visszaad. Bármely kívánt tartalom sztringje lehet. A véletlenszerűen generált egyedi érték általában a [helyek közötti kérelmek hamisításának megelőzésére](https://tools.ietf.org/html/rfc6749#section-10.12)szolgál. Az érték az alkalmazásban lévő felhasználó állapotára vonatkozó adatokat is kódolhatja, mielőtt a hitelesítési kérelem bekövetkezett volna, például az oldal vagy a megtekintés. |
| `prompt`  | választható    | Megadja a szükséges felhasználói beavatkozás típusát. Ebben az esetben az egyetlen érvényes érték a, a `login` `none` és a `consent` .<br/><br/>- `prompt=login` kényszeríti a felhasználót, hogy adja meg hitelesítő adatait a kérésen, és zárja be az egyszeri bejelentkezést.<br/>- `prompt=none` Ez ellentétes – biztosítja, hogy a felhasználó semmilyen interaktív kérdés nélkül is megjelenik. Ha a kérést nem lehet csendes úton végrehajtani az egyszeri bejelentkezésen keresztül, a Microsoft Identity platform hibát ad vissza `interaction_required` .<br/>- `prompt=consent` a a felhasználó bejelentkezése után elindítja a OAuth jóváhagyása párbeszédpanelt, amely arra kéri a felhasználót, hogy adjon engedélyt az alkalmazásnak.<br/>- `prompt=select_account` megszakítja az egyszeri bejelentkezést, amely lehetővé teszi a fiókok kiválasztási élményét, amely a munkamenetben vagy a megjegyzett fiókban található összes fiókot, illetve egy másik fiók használatának lehetőségét választja.<br/> |
| `login_hint`  | választható    | A használatával előre kitöltheti a felhasználó bejelentkezési oldalának username/e-mail címe mezőjét, ha már ismeri a felhasználónevét. Az alkalmazások gyakran ezt a paramétert fogják használni az ismételt hitelesítés során, miután már kibontotta a felhasználónevet egy korábbi bejelentkezésből a `preferred_username` jogcím használatával.   |
| `domain_hint`  | választható    | Ha belefoglalja ezt a funkciót, a rendszer kihagyja az e-mailes felderítési folyamatot, amelyet a felhasználó a bejelentkezési oldalon áthalad, ami valamivel fejlettebb felhasználói élményt eredményez, például elküldi őket az összevont identitás-szolgáltatónak. Az alkalmazások gyakran ezt a paramétert fogják használni az ismételt hitelesítés során, ha kinyeri az alkalmazást `tid` egy korábbi bejelentkezésből. Ha a `tid` jogcím értéke `9188040d-6c67-4c5b-b112-36a304b66dad` , használja a ( `domain_hint=consumers` ) értéket. Egyéb esetben használja a t `domain_hint=organizations` .  |
| `code_challenge`  | ajánlott/szükséges | Az engedélyezési kód támogatásának biztosítására szolgál a Code Exchange (PKCE) igazolási kulcsán keresztül. Kötelező `code_challenge_method` , ha szerepel benne. További információ: [PKCE RFC](https://tools.ietf.org/html/rfc7636). Ezt mostantól ajánlott minden alkalmazás-típushoz – a nyilvános és a bizalmas ügyfelekhez is –, és a Microsoft Identity platformnak kell megadnia [egy egyoldalas alkalmazásokhoz az engedélyezési kód folyamatát használva](reference-third-party-cookies-spas.md). |
| `code_challenge_method` | ajánlott/szükséges | A paraméter kódolásához használt metódus `code_verifier` `code_challenge` . Ennek *a következőnek* kell lennie `S256` , de a specifikáció lehetővé teszi a használatát, `plain` Ha valamilyen okból kifolyólag az ügyfél nem támogatja a sha256. <br/><br/>Ha a szolgáltatás ki van zárva, a rendszer azt `code_challenge` feltételezi, hogy egyszerű szöveg, ha `code_challenge` szerepel benne. A Microsoft Identity platform a és a együttes használatát is támogatja `plain` `S256` . További információ: [PKCE RFC](https://tools.ietf.org/html/rfc7636). Ez az [engedélyezési kód folyamatát használó egylapos alkalmazások](reference-third-party-cookies-spas.md)esetében szükséges.|


Ekkor a rendszer megkéri a felhasználót, hogy adja meg a hitelesítő adatait, és fejezze be a hitelesítést. A Microsoft Identity platform azt is biztosítja, hogy a felhasználó beleegyezett a `scope` lekérdezési paraméterben megadott engedélyekkel. Ha a felhasználó nem járult hozzá ezen engedélyek bármelyikéhez, a felhasználó beleegyezést kér a szükséges engedélyekkel. Az [engedélyek, a beleegyezett és a több-bérlős alkalmazások részletei itt](v2-permissions-and-consent.md)érhetők el.

Miután a felhasználó hitelesíti és beleegyezik, a Microsoft Identity platform az adott alkalmazásra adott választ ad vissza a `redirect_uri` paraméterben megadott metódus használatával `response_mode` .

#### <a name="successful-response"></a>Sikeres válasz

Sikeres válasz a `response_mode=query` következőképpen:

```HTTP
GET http://localhost?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Paraméter | Leírás  |
|-----------|--------------|
| `code` | Az alkalmazás által kért authorization_code. Az alkalmazás az engedélyezési kóddal kérhet hozzáférési jogkivonatot a célként megadott erőforráshoz. Authorization_codes rövid életűek, jellemzően körülbelül 10 perc múlva lejárnak. |
| `state` | Ha a kérelemben szerepel egy State paraméter, akkor a válaszban ugyanazt az értéket kell megjelennie. Az alkalmazásnak ellenőriznie kell, hogy a kérelemben és a válaszban szereplő állapot értékek azonosak-e. |

Akkor is kaphat azonosító jogkivonatot, ha kérelmezi, és az alkalmazás regisztrációjában engedélyezve van az implicit engedély.  Ezt más néven ["hibrid folyamatnak"](#request-an-id-token-as-well-hybrid-flow)is nevezik, és a keretrendszerek, például a ASP.net használják.

#### <a name="error-response"></a>Hiba válasza

A rendszer a hibaüzeneteket is elküldheti, hogy `redirect_uri` az alkalmazás megfelelően tudja kezelni őket:

```HTTP
GET http://localhost?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Paraméter | Leírás  |
|----------|------------------|
| `error`  | Hibakód-karakterlánc, amely a felmerülő hibák típusának besorolására használható, és felhasználható a hibákra való reagálásra. |
| `error_description` | Egy adott hibaüzenet, amely segítséget nyújt a fejlesztőknek a hitelesítési hiba kiváltó okának azonosításában. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Az engedélyezési végpont hibáinak kódjai

Az alábbi táblázat azokat a hibakódokat ismerteti, amelyeket a rendszer a `error` hiba válaszának paraméterében adhat vissza.

| Hibakód  | Description    | Ügyfél művelete   |
|-------------|----------------|-----------------|
| `invalid_request` | Protokollhiba, például hiányzó kötelező paraméter. | Javítsa ki és küldje el újra a kérelmet. Ez a kezdeti tesztelés során általában felmerülő fejlesztési hiba. |
| `unauthorized_client` | Az ügyfélalkalmazás nem jogosult engedélyezési kód igénylésére. | Ez a hiba általában akkor fordul elő, ha az ügyfélalkalmazás nincs regisztrálva az Azure AD-ben, vagy nem kerül be a felhasználó Azure AD-bérlőbe. Az alkalmazás arra kéri a felhasználót, hogy telepítse az alkalmazást, és hozzáadja azt az Azure AD-hez. |
| `access_denied`  | Erőforrás-tulajdonos által megtagadott jóváhagyás  | Az ügyfélalkalmazás értesíti a felhasználót, hogy csak akkor tud továbblépni, ha a felhasználó beleegyezett. |
| `unsupported_response_type` | Az engedélyezési kiszolgáló nem támogatja a válasz típusát a kérelemben. | Javítsa ki és küldje el újra a kérelmet. Ez a kezdeti tesztelés során általában felmerülő fejlesztési hiba. Ha a [hibrid folyamat](#request-an-id-token-as-well-hybrid-flow)látható, a jelzi, hogy engedélyeznie kell az azonosító jogkivonat implicit engedélyezési beállítását az ügyfélalkalmazás regisztrálásakor. |
| `server_error`  | A kiszolgáló váratlan hibát észlelt.| Próbálja megismételni a kérelmet. Ezek a hibák ideiglenes feltételekből származhatnak. Előfordulhat, hogy az ügyfélalkalmazás elmagyarázza a felhasználónak, hogy a válasza ideiglenes hibára késleltetve van. |
| `temporarily_unavailable`   | A kiszolgáló átmenetileg túl elfoglalt a kérelem kezeléséhez. | Próbálja megismételni a kérelmet. Előfordulhat, hogy az ügyfélalkalmazás megmagyarázza a felhasználót, hogy a válasza egy ideiglenes feltétel miatt késleltetve van. |
| `invalid_resource`  | A célként megadott erőforrás érvénytelen, mert nem létezik, az Azure AD nem találja, vagy helytelenül van konfigurálva. | Ez a hiba azt jelzi, hogy az erőforrás (ha létezik) nem lett konfigurálva a bérlőben. Az alkalmazás arra kéri a felhasználót, hogy telepítse az alkalmazást, és hozzáadja azt az Azure AD-hez. |
| `login_required` | Túl sok vagy nem található felhasználó | Az ügyfél csendes hitelesítést ( `prompt=none` ) kért, de egyetlen felhasználó nem található. Ez azt jelentheti, hogy több felhasználó is aktív a munkamenetben, vagy nincsenek felhasználók. Ez a kiválasztott bérlőt veszi figyelembe (például ha két Azure AD-fiók aktív és egy Microsoft-fiók van `consumers` kiválasztva, a csendes hitelesítés működni fog). |
| `interaction_required` | A kérés felhasználói beavatkozást igényel. | További hitelesítési lépés vagy beleegyező engedély szükséges. Próbálkozzon újra a kérelem nélkül `prompt=none` . |

### <a name="request-an-id-token-as-well-hybrid-flow"></a>Igényeljen azonosító jogkivonatot is (hibrid folyamat)

Annak megismeréséhez, hogy ki a felhasználó az engedélyezési kód beváltása előtt, az alkalmazások esetében az azonosító tokent is kérni kell, ha az engedélyezési kódot kérik. Ezt nevezzük *hibrid folyamatnak* , mivel az implicit engedélyezést az engedélyezési kód folyamatával keveri. A hibrid folyamat gyakran használatos olyan webalkalmazásokban, amelyek a kód beváltásának letiltása nélkül szeretnék megjeleníteni a felhasználók oldalát, például [ASP.net](quickstart-v2-aspnet-core-webapp.md). Az egyoldalas alkalmazások és a hagyományos webalkalmazások is igénybe vehetik a modell kisebb késését.

A hibrid folyamat ugyanaz, mint a korábban ismertetett engedélyezési kód, de három kiegészítéssel, amelyek mindegyike azonosító token igényléséhez szükséges: új hatókörök, új response_type és új `nonce` lekérdezési paraméter.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code%20id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=fragment
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&state=12345
&nonce=abcde
&code_challenge=YTFjNjI1OWYzMzA3MTI4ZDY2Njg5M2RkNmVjNDE5YmEyZGRhOGYyM2IzNjdmZWFhMTQ1ODg3NDcxY2Nl
&code_challenge_method=S256
```

| Frissített paraméter | Kötelező/nem kötelező | Leírás |
|---------------|-------------|--------------|
|`response_type`| Kötelező | A Hozzáadás `id_token` azt jelzi, hogy az alkalmazás a végponttól kapott válaszban azonosító jogkivonatot szeretne kapni a kiszolgálónak `/authorize` .  |
|`scope`| Kötelező | Az azonosító jogkivonatok esetében frissíteni kell, hogy tartalmazza az azonosító jogkivonat hatóköreit – `openid` és opcionálisan `profile` és `email` . |
|`nonce`| Kötelező|     Az alkalmazás által generált kérelemben szereplő érték, amelyet a rendszer az eredményül kapott id_token tartalmaz. Az alkalmazás ezután ellenőrizheti ezt az értéket a jogkivonat-Visszajátszási támadások enyhítése érdekében. Az érték általában egy véletlenszerű, egyedi karakterlánc, amely a kérelem forrásának azonosítására szolgál. |
|`response_mode`| Ajánlott | Meghatározza azt a módszert, amelyet az eredményül kapott jogkivonat az alkalmazásba való visszaküldéséhez kell használni. Az alapértelmezett érték `query` csak egy engedélyezési kód esetében, de `fragment` Ha a kérelem id_token tartalmaz `response_type` .  Az alkalmazásokat azonban ajánlott használni `form_post` , különösen akkor, ha `http:/localhost` átirányítási URI-ként használják. |

A `fragment` válaszként való használata problémát okoz olyan webalkalmazások esetében, amelyek beolvasják a kódot az átirányítás alól, mivel a böngészők nem adják át a töredéket a webkiszolgálónak.  Ezekben az esetekben az alkalmazásoknak a `form_post` Válasz módot kell használniuk annak biztosítására, hogy minden adatküldés a kiszolgálónak. 

#### <a name="successful-response"></a>Sikeres válasz

Sikeres válasz a `response_mode=fragment` következőképpen:

```HTTP
GET https://login.microsoftonline.com/common/oauth2/nativeclient#
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&id_token=eYj...
&state=12345
```

| Paraméter | Leírás  |
|-----------|--------------|
| `code` | Az alkalmazás által kért engedélyezési kód. Az alkalmazás az engedélyezési kóddal kérhet hozzáférési jogkivonatot a célként megadott erőforráshoz. Az engedélyezési kódok rövid életűek, általában körülbelül 10 perc elteltével lejárnak. |
| `id_token` | A felhasználóhoz tartozó azonosító jogkivonatot, amely *implicit engedélyen* keresztül lett kiállítva. Egy speciális `c_hash` jogcímet tartalmaz, amely a kérelem kivonata `code` . |
| `state` | Ha a kérelemben szerepel egy State paraméter, akkor a válaszban ugyanazt az értéket kell megjelennie. Az alkalmazásnak ellenőriznie kell, hogy a kérelemben és a válaszban szereplő állapot értékek azonosak-e. |

## <a name="request-an-access-token"></a>Hozzáférési jogkivonat kérése

Most, hogy beszerzett egy authorization_code, és engedélyt kapott a felhasználótól, beválthatja az `code` `access_token` -t a kívánt erőforráshoz. Ehhez egy `POST` kérést kell küldenie a `/token` végpontnak:

```HTTP
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&code_verifier=ThisIsntRandomButItNeedsToBe43CharactersLong 
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps. This secret needs to be URL-Encoded.
```

> [!TIP]
> Próbálja meg végrehajtani a kérelmet postán! (Ne felejtse el lecserélni a `code` ) [ ![ Próbálja meg futtatni ezt a kérelmet postán](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

| Paraméter  | Kötelező/nem kötelező | Description     |
|------------|-------------------|----------------|
| `tenant`   | kötelező   | A `{tenant}` kérelem elérési útjának értéke használható annak szabályozására, hogy ki jelentkezhet be az alkalmazásba. Az engedélyezett értékek:,, `common` `organizations` `consumers` és bérlői azonosítók. További részletek: [protokoll alapjai](active-directory-v2-protocols.md#endpoints).  |
| `client_id` | kötelező  | Az alkalmazáshoz hozzárendelt [Azure Portal – Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lap alkalmazás-(ügyfél-) azonosítója. |
| `grant_type` | kötelező   | `authorization_code`Az engedélyezési kód folyamatábrájának kell lennie.   |
| `scope`      | választható   | A hatókörök szóközzel tagolt listája. A hatóköröknek egyetlen erőforrásból kell származnia, a OIDC hatókörökkel együtt ( `profile` , `openid` , `email` ). A hatókörök részletesebb ismertetését az [engedélyek, a beleegyezések és a hatókörök](v2-permissions-and-consent.md)című témakörben találja. Ez egy Microsoft-bővítmény az engedélyezési kód folyamatához, amely lehetővé teszi, hogy az alkalmazások deklarálják a tokent a jogkivonat beváltásakor.|
| `code`          | kötelező  | A folyamat első szakaszában beszerzett authorization_code. |
| `redirect_uri`  | kötelező  | A authorization_code megvásárlásához használt redirect_uri érték. |
| `client_secret` | bizalmas webalkalmazásokhoz szükséges | Az alkalmazás regisztrációs portálján létrehozott alkalmazás-titkos kód az alkalmazáshoz. Az alkalmazás titkos kulcsát nem szabad natív alkalmazásban vagy egyoldalas alkalmazásban használni, mert client_secrets nem lehet megbízhatóan tárolni eszközökön vagy weblapokon. A webalkalmazásokhoz és a webes API-khoz szükséges, amelyek képesek a client_secret biztonságos tárolására a kiszolgálóoldali oldalon.  Az itt tárgyalt paraméterekhez hasonlóan az ügyfél titkos kulcsának URL-kódolású kell lennie a küldés előtt, amelyet általában az SDK hajt végre. Az URI-kódolással kapcsolatos további információkért tekintse meg az [URI általános szintaxisának specifikációját](https://tools.ietf.org/html/rfc3986#page-12). |
| `code_verifier` | ajánlott  | Ugyanaz a code_verifier, amelyet a authorization_code beszerzéséhez használt. Kötelező, ha a PKCE az engedélyezési kód Grant kérelmében használták. További információ: [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

### <a name="successful-response"></a>Sikeres válasz

A jogkivonat sikeres válasza A következőképpen fog kinézni:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| Paraméter     | Leírás   |
|---------------|------------------------------|
| `access_token`  | A kért hozzáférési jogkivonat. Az alkalmazás használhatja ezt a tokent a biztonságos erőforráshoz, például egy webes API-hoz való hitelesítéshez.  |
| `token_type`    | Megadja a jogkivonat típusának értékét. Az Azure AD által támogatott egyetlen típus a tulajdonos |
| `expires_in`    | A hozzáférési jogkivonat érvényességi ideje (másodpercben). |
| `scope`         | Azok a hatókörök, amelyekre a access_token érvényes. Nem kötelező – ez nem szabványos, és ha nincs megadva, a jogkivonat a folyamat kezdeti szakaszán kért hatókörökre lesz kiosztva. |
| `refresh_token` | Egy OAuth 2,0 frissítési token. Az alkalmazás használhatja ezt a tokent további hozzáférési jogkivonatok beszerzésére az aktuális hozzáférési jogkivonat lejárta után. A Refresh_tokens hosszú élettartamú, és az erőforrásokhoz való hozzáférés hosszabb ideig való megtartására is használható. A hozzáférési tokenek frissítésével kapcsolatos további részletekért tekintse meg az [alábbi szakaszt](#refresh-the-access-token). <br> **Megjegyzés:** Csak akkor van megadva, ha a `offline_access` hatókört kérték. |
| `id_token`      | Egy JSON Web Token (JWT). Az alkalmazás képes dekódolni a token szegmenseit, hogy adatokat kérjen a bejelentkezett felhasználótól. Az alkalmazás gyorsítótárazhatja az értékeket, és megjelenítheti őket, a bizalmas ügyfelek pedig használhatják ezt az engedélyt. További információ a id_tokensról: [`id_token reference`](id-tokens.md) . <br> **Megjegyzés:** Csak akkor van megadva, ha a `openid` hatókört kérték. |

### <a name="error-response"></a>Hiba válasza

A következőhöz hasonló hibaüzenetek jelennek meg:

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Paraméter         | Leírás    |
|-------------------|----------------|
| `error`       | Hibakód-karakterlánc, amely a felmerülő hibák típusának besorolására használható, és felhasználható a hibákra való reagálásra. |
| `error_description` | Egy adott hibaüzenet, amely segítséget nyújt a fejlesztőknek a hitelesítési hiba kiváltó okának azonosításában. |
| `error_codes` | Az STS-specifikus hibakódok listája, amelyek segíthetnek a diagnosztikaben.  |
| `timestamp`   | Az az idő, amikor a hiba bekövetkezett. |
| `trace_id`    | Az a kérelem egyedi azonosítója, amely segíthet a diagnosztikaben. |
| `correlation_id` | A kérelem egyedi azonosítója, amely segíthet az összetevők diagnosztizálásában. |

### <a name="error-codes-for-token-endpoint-errors"></a>Hibakódok jogkivonat-végponti hibákhoz

| Hibakód         | Description        | Ügyfél művelete    |
|--------------------|--------------------|------------------|
| `invalid_request`  | Protokollhiba, például hiányzó kötelező paraméter. | Javítsa ki a kérést vagy az alkalmazást, és küldje el újra a kérelmet.   |
| `invalid_grant`    | Az engedélyezési kód vagy a PKCE-ellenőrző érvénytelen vagy lejárt. | Próbálkozzon egy új kéréssel a `/authorize` végponton, és ellenőrizze, hogy helyesek-e a code_verifier paraméter.  |
| `unauthorized_client` | A hitelesített ügyfél nem jogosult az engedélyezési támogatás típusának használatára. | Ez általában akkor fordul elő, ha az ügyfélalkalmazás nincs regisztrálva az Azure AD-ben, vagy nem kerül be a felhasználó Azure AD-bérlőbe. Az alkalmazás arra kéri a felhasználót, hogy telepítse az alkalmazást, és hozzáadja azt az Azure AD-hez. |
| `invalid_client` | Nem sikerült az ügyfél-hitelesítés.  | Az ügyfél hitelesítő adatai nem érvényesek. A javításhoz az alkalmazás rendszergazdája frissíti a hitelesítő adatokat.   |
| `unsupported_grant_type` | Az engedélyezési kiszolgáló nem támogatja az engedélyezési engedély típusát. | Módosítsa a kérelemben szereplő Grant típust. Ez a típusú hiba csak a fejlesztés során fordul elő, és a rendszer a kezdeti tesztelés során észleli. |
| `invalid_resource` | A célként megadott erőforrás érvénytelen, mert nem létezik, az Azure AD nem találja, vagy helytelenül van konfigurálva. | Ez azt jelzi, hogy az erőforrás (ha létezik) nem lett konfigurálva a bérlőben. Az alkalmazás arra kéri a felhasználót, hogy telepítse az alkalmazást, és hozzáadja azt az Azure AD-hez.  |
| `interaction_required` | Nem szabványos, mivel a OIDC-specifikáció csak a végponton hív meg `/authorize` . A kérés felhasználói beavatkozást igényel. Például további hitelesítési lépésre van szükség. | Próbálja megismételni a `/authorize` kérelmet ugyanazzal a hatókörökkel. |
| `temporarily_unavailable` | A kiszolgáló átmenetileg túl elfoglalt a kérelem kezeléséhez. | Kis késleltetés után próbálja megismételni a kérést. Előfordulhat, hogy az ügyfélalkalmazás megmagyarázza a felhasználót, hogy a válasza egy ideiglenes feltétel miatt késleltetve van. |
|`consent_required` | A kérés felhasználói beleegyezett. Ez a hiba nem szabványos, mivel ez általában csak a `/authorize` végponton, OIDC-specifikációk esetében adható vissza. Akkor tért vissza `scope` , ha olyan paramétert használtak a kód-visszaváltási folyamathoz, amelyhez az ügyfélalkalmazás nem jogosult a kérésre.  | Az ügyfélnek a megfelelő hatókörrel kell visszaküldenie a felhasználót a `/authorize` végponthoz, hogy el lehessen indítani az engedélyt. |
|`invalid_scope` | Az alkalmazás által igényelt hatókör érvénytelen.  | Frissítse a hatókör-paraméter értékét a hitelesítési kérelemben érvényes értékre. |

> [!NOTE]
> Az egyoldalas alkalmazások olyan `invalid_request` hibaüzenetet kaphatnak, amely azt jelzi, hogy a több eredetű jogkivonat beváltásának engedélyezése csak az "egylapos alkalmazás" ügyfél-típus esetében engedélyezett.  Ez azt jelzi, hogy a jogkivonat kéréséhez használt átirányítási URI nem lett átirányítási URI-ként megjelölve `spa` .  Tekintse át az [alkalmazás regisztrációjának lépéseit](#redirect-uri-setup-required-for-single-page-apps) a folyamat engedélyezéséhez.

## <a name="use-the-access-token"></a>Hozzáférési jogkivonat használata

Most, hogy sikeresen megszerezte a `access_token` -t, használhatja a jogkivonatot a webes API-khoz a kérésekben a fejlécbe való belefoglalásával `Authorization` :

> [!TIP]
> A kérelem végrehajtása postán! (Először cserélje le a `Authorization` fejlécet) futtassa [ ![ ezt a kérelmet a Poster-ben](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>A hozzáférési jogkivonat frissítése

Access_tokens rövid életűek, és a lejáratuk után frissítenie kell őket, hogy továbbra is hozzáférhessenek az erőforrásokhoz. Ezt úgy teheti meg, hogy elküld egy másik `POST` kérést a `/token` végpontnak, ezúttal megadja a helyett a következőt: `refresh_token` `code` .  A frissítési jogkivonatok érvényesek minden olyan engedélyhez, amelyet az ügyfél már kapott, ezért a kérelemre kiadott frissítési jogkivonat használható `scope=mail.read` új hozzáférési jogkivonat igénylésére a alkalmazáshoz `scope=api://contoso.com/api/UseResource` .

A Web Apps és a natív alkalmazások jogkivonatai nem rendelkeznek megadott élettartammal. A frissítési tokenek élettartama általában viszonylag hosszú. Bizonyos esetekben azonban a frissítési tokenek lejárnak, visszavonásra kerülnek, vagy hiányoznak a megfelelő jogosultságok a kívánt művelethez. Az alkalmazásnak megfelelően kell elvárnia és kezelnie [a jogkivonat-kiállítási végpont által visszaadott hibákat](#error-codes-for-token-endpoint-errors) . Egyoldalas alkalmazások esetében azonban egy 24 órás élettartammal rendelkező tokent kap, amely naponta új hitelesítést igényel.  Ez csendesen hajtható végre egy iframe-ben, ha a harmadik féltől származó cookie-k engedélyezve vannak, de a legfelső szintű keretben (teljes oldal vagy előugró ablakban) kell elvégezni a böngészőben, külső cookie-k, például a Safari nélkül.

Bár a frissítési tokenek nem vonhatók vissza, amikor új hozzáférési jogkivonatok beszerzésére használják, el kell vetni a régi frissítési tokent. A [OAuth 2,0 spec](https://tools.ietf.org/html/rfc6749#section-6) azt mondja: "az engedélyezési kiszolgáló új frissítési tokent adhat ki, amely esetben az ügyfélnek el kell vetnie a régi frissítési tokent, és le kell cserélnie az új frissítési jogkivonattal. Az engedélyezési kiszolgáló visszavonhatja a régi frissítési tokent, miután kiadott egy új frissítési jogkivonatot az ügyfélnek. "

>[!IMPORTANT]
> A-ben regisztrált átirányítási URI-ként elküldett frissítési tokenek esetében `spa` a frissítési token 24 óra elteltével lejár. A kezdeti frissítési jogkivonat használatával beszerzett további frissítési tokenek a lejárati idő alatt lesznek elvégezve, ezért az alkalmazásoknak elő kell készülniük az engedélyezési kód folyamatának újbóli futtatására egy interaktív hitelesítéssel, hogy 24 óránként új frissítési tokent szerezzenek be. A felhasználóknak nem kell megadniuk a hitelesítő adataikat, és általában nem is láthatják az alkalmazás újraindítását, de a böngészőnek legfelső szintű keretben kell megkeresnie a bejelentkezési oldalt a bejelentkezési munkamenet megtekintéséhez.  Ennek oka a [harmadik féltől származó cookie-kat blokkoló böngészők adatvédelmi funkciói](reference-third-party-cookies-spas.md).

```HTTP

// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh      // NOTE: Only required for web apps. This secret needs to be URL-Encoded
```

> [!TIP]
> Próbálja meg végrehajtani a kérelmet postán! (Ne felejtse el lecserélni a `refresh_token` ) [ ![ Próbálja meg futtatni ezt a kérelmet postán](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)
>

| Paraméter     | Típus           | Description        |
|---------------|----------------|--------------------|
| `tenant`        | kötelező     | A `{tenant}` kérelem elérési útjának értéke használható annak szabályozására, hogy ki jelentkezhet be az alkalmazásba. Az engedélyezett értékek:,, `common` `organizations` `consumers` és bérlői azonosítók. További részletek: [protokoll alapjai](active-directory-v2-protocols.md#endpoints).   |
| `client_id`     | kötelező    | Az alkalmazáshoz hozzárendelt [Azure Portal – Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) felhasználói felület **(ügyfél) azonosítója** . |
| `grant_type`    | kötelező    | `refresh_token`Az engedélyezési kód folyamatának ezen szakaszához kell tartoznia. |
| `scope`         | kötelező    | A hatókörök szóközzel tagolt listája. Az ebben a lábában kért hatóköröknek meg kell egyeznie az eredeti authorization_code kérelem lábában kért hatókörökkel vagy azok egy részhalmazával. Ha a kérelemben megadott hatókörök több erőforrás-kiszolgálóra is kiterjednek, akkor a Microsoft Identity platform az első hatókörben megadott erőforráshoz tartozó jogkivonatot ad vissza. A hatókörök részletesebb ismertetését az [engedélyek, a beleegyezések és a hatókörök](v2-permissions-and-consent.md)című témakörben találja. |
| `refresh_token` | kötelező    | A folyamat második szakaszában beszerzett refresh_token. |
| `client_secret` | webalkalmazásokhoz szükséges | Az alkalmazás regisztrációs portálján létrehozott alkalmazás-titkos kód az alkalmazáshoz. Nem használható natív alkalmazásban, mert client_secrets nem lehet megbízhatóan tárolni az eszközökön. A webalkalmazásokhoz és a webes API-khoz szükséges, amelyek képesek a client_secret biztonságos tárolására a kiszolgálóoldali oldalon. A titkos kód URL-kódolású. További információ: [URI általános szintaxisának specifikációja](https://tools.ietf.org/html/rfc3986#page-12). |

#### <a name="successful-response"></a>Sikeres válasz

A jogkivonat sikeres válasza A következőképpen fog kinézni:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| Paraméter     | Leírás         |
|---------------|-------------------------------------------------------------|
| `access_token`  | A kért hozzáférési jogkivonat. Az alkalmazás használhatja ezt a tokent a biztonságos erőforráshoz, például egy webes API-hoz való hitelesítéshez. |
| `token_type`    | Megadja a jogkivonat típusának értékét. Az Azure AD által támogatott egyetlen típus a tulajdonos |
| `expires_in`    | A hozzáférési jogkivonat érvényességi ideje (másodpercben).   |
| `scope`         | Azok a hatókörök, amelyekre a access_token érvényes.    |
| `refresh_token` | Új OAuth 2,0 frissítési jogkivonat. A régi frissítési tokent cserélje le erre az újonnan beszerzett frissítési jogkivonatra annak biztosításához, hogy a frissítési tokenek a lehető leghosszabb ideig érvényesek maradjanak. <br> **Megjegyzés:** Csak akkor van megadva, ha a `offline_access` hatókört kérték.|
| `id_token`      | Aláíratlan JSON Web Token (JWT). Az alkalmazás képes dekódolni a token szegmenseit, hogy adatokat kérjen a bejelentkezett felhasználótól. Az alkalmazás gyorsítótárazhatja az értékeket, és megjelenítheti őket, de nem hivatkozhat rájuk az engedélyezési vagy biztonsági határokra. További információ a id_tokensról: [`id_token reference`](id-tokens.md) . <br> **Megjegyzés:** Csak akkor van megadva, ha a `openid` hatókört kérték. |

#### <a name="error-response"></a>Hiba válasza

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Paraméter         | Leírás                                                                                        |
|-------------------|----------------------------------------------------------------------------------------------------|
| `error`           | Hibakód-karakterlánc, amely a felmerülő hibák típusának besorolására használható, és felhasználható a hibákra való reagálásra. |
| `error_description` | Egy adott hibaüzenet, amely segítséget nyújt a fejlesztőknek a hitelesítési hiba kiváltó okának azonosításában.           |
| `error_codes` |Az STS-specifikus hibakódok listája, amelyek segíthetnek a diagnosztikaben. |
| `timestamp` | Az az idő, amikor a hiba bekövetkezett. |
| `trace_id` | Az a kérelem egyedi azonosítója, amely segíthet a diagnosztikaben. |
| `correlation_id` | A kérelem egyedi azonosítója, amely segíthet az összetevők diagnosztizálásában. |

A hibakódok és az ajánlott ügyfél-művelet leírását a jogkivonat- [végponti hibák hibakódja](#error-codes-for-token-endpoint-errors)című témakörben tekintheti meg.
