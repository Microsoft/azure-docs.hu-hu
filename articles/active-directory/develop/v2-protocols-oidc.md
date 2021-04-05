---
title: Microsoft Identity platform és OpenID Connect protokoll | Azure
titleSuffix: Microsoft identity platform
description: Hozzon létre webalkalmazásokat az OpenID Connect hitelesítési protokoll Microsoft Identity platform-implementációjának használatával.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/22/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: de1fcdc259de3f72e35feb411bcc836354352eb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98752595"
---
# <a name="microsoft-identity-platform-and-openid-connect-protocol"></a>Microsoft Identity platform és OpenID Connect protokoll

Az OpenID Connect (OIDC) egy OAuth 2,0-re épülő hitelesítési protokoll, amely segítségével a felhasználók biztonságosan bejelentkezhetnek egy alkalmazásba. Ha az OpenID Connect Microsoft Identity platformjának megvalósítását használja, hozzáadhat bejelentkezési és API-hozzáférést az alkalmazásaihoz. Ebből a cikkből megtudhatja, hogyan teheti meg ezt a nyelvet, és leírja, hogyan küldhet és fogadhat HTTP-üzeneteket a [Microsoft nyílt forráskódú kódtárai](reference-v2-libraries.md)használata nélkül.

Az [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) kibővíti a *OAuth 2,0 hitelesítési protokollt,* amely *hitelesítési* protokollként használható, így az egyszeri bejelentkezést a OAuth használatával végezheti el. Az OpenID Connect bevezeti az *azonosító jogkivonat* fogalmát, amely egy biztonsági jogkivonat, amely lehetővé teszi az ügyfél számára a felhasználó identitásának ellenőrzését. Az azonosító jogkivonat a felhasználó alapszintű profiljának adatait is beolvassa. Emellett bemutatja a [UserInfo-végpontot](userinfo.md), amely egy API, amely a felhasználóra vonatkozó adatokat ad vissza. 


## <a name="protocol-diagram-sign-in"></a>Protokoll diagramja: bejelentkezés

A legalapvetőbb bejelentkezési folyamat a következő ábrán látható lépéseket mutatja be. A jelen cikk részletesen ismerteti az egyes lépéseket.

![OpenID Connect protokoll: bejelentkezés](./media/v2-protocols-oidc/convergence-scenarios-webapp.svg)

## <a name="fetch-the-openid-connect-metadata-document"></a>Az OpenID Connect metaadat-dokumentumának beolvasása

Az OpenID Connect egy metaadat [-dokumentumot (RFC)](https://openid.net/specs/openid-connect-discovery-1_0.html) tartalmaz, amely az alkalmazás számára a bejelentkezéshez szükséges információk nagy részét tartalmazza. Ide tartozik például a használni kívánt URL-címek és a szolgáltatás nyilvános aláíró kulcsainak helye. A dokumentumot úgy keresheti meg, hogy hozzáfűzi a felderítési dokumentum elérési útját a szolgáltató URL-címéhez:

Felderítési dokumentum elérési útja: `/.well-known/openid-configuration`

Hitelesítésszolgáltató `https://login.microsoftonline.com/{tenant}/v2.0`

A `{tenant}` négy érték egyikét is elvégezheti:

| Érték | Leírás |
| --- | --- |
| `common` |Az Azure AD-ben személyes Microsoft-fiók és munkahelyi vagy iskolai fiókkal rendelkező felhasználók is bejelentkezhetnek az alkalmazásba. |
| `organizations` |Csak az Azure AD-ből munkahelyi vagy iskolai fiókkal rendelkező felhasználók jelentkezhetnek be az alkalmazásba. |
| `consumers` |Csak a személyes Microsoft-fiók rendelkező felhasználók jelentkezhetnek be az alkalmazásba. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` vagy `contoso.onmicrosoft.com` | Csak egy adott Azure AD-bérlőből származó felhasználók (függetlenül attól, hogy a címtár tagjai munkahelyi vagy iskolai fiókkal, vagy a címtárban lévő vendégek személyes Microsoft-fiók) bejelentkezhetnek az alkalmazásba. Az Azure AD-bérlő vagy a bérlő GUID azonosítójának felhasználóbarát tartományneve is használható. A bérlő helyett használhatja a fogyasztói bérlőt is `9188040d-6c67-4c5b-b112-36a304b66dad` `consumers` .  |

A hatóság különbözik az egyes nemzeti felhőktől – például `https://login.microsoftonline.de` Az Azure ad Germany-példány esetében. Ha nem használja a nyilvános felhőt, tekintse át a [nemzeti Felhőbeli végpontokat](authentication-national-cloud.md#azure-ad-authentication-endpoints) , és keresse meg a megfelelőt. Győződjön meg arról, hogy a bérlő és a kérése megtalálható, hogy a `/v2.0/` végpont v 2.0-s verzióját használja.

> [!TIP]
> Próbálja ki! Ide kattintva [https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) megtekintheti a `common` konfigurációt.

### <a name="sample-request"></a>Példa a kérelemre

A nyilvános felhőben található általános szolgáltató UserInfo-végpontjának meghívásához használja a következőt:

```http
GET /common/v2.0/.well-known/openid-configuration
Host: login.microsoftonline.com
```

### <a name="sample-response"></a>Mintaválasz

A metaadatok egy egyszerű JavaScript Object Notation (JSON) dokumentum. Példaként tekintse meg a következő kódrészletet. A tartalom teljes leírását az [OpenID Connect specifikációja](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2)tartalmazza.

```json
{
  "authorization_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize",
  "token_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys",
  "userinfo_endpoint": "https://graph.microsoft.com/oidc/userinfo",
  "subject_types_supported": [
      "pairwise"
  ],
  ...

}
```

Ha az alkalmazás a [jogcím-leképezési](active-directory-claims-mapping.md) funkció használata miatt egyéni aláíró kulcsokkal rendelkezik, akkor `appid` az alkalmazás azonosítóját tartalmazó lekérdezési paramétert hozzá kell fűzni ahhoz, hogy az `jwks_uri` alkalmazáshoz tartozó aláíró kulcs adataira mutasson. Például a a következőt `https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` tartalmazza: `jwks_uri` `https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e` .

Ez a metaadat-dokumentum általában egy OpenID Connect-függvénytár vagy SDK konfigurálására használható. a könyvtár a metaadatokat fogja használni a munkája elvégzéséhez. Ha azonban nem használ előre elkészített OpenID Connect-függvénytárat, a cikk további részében ismertetett lépéseket követve a Microsoft Identity platform használatával bejelentkezhet a webalkalmazásba.

## <a name="send-the-sign-in-request"></a>Bejelentkezési kérelem küldése

Ha a webalkalmazásnak hitelesítenie kell a felhasználót, akkor a felhasználó a végpontra irányíthatja a felhasználót `/authorize` . Ez a kérelem hasonló a [OAuth 2,0 engedélyezési kód](v2-oauth2-auth-code-flow.md)első szakaszához, ezekkel a fontos különbségekkel:

* A kérelemnek tartalmaznia kell a `openid` hatókört a `scope` paraméterben.
* A `response_type` paraméternek tartalmaznia kell a következőket: `id_token` .
* A kérelemnek tartalmaznia kell a `nonce` paramétert.

> [!IMPORTANT]
> Ahhoz, hogy sikeresen igényeljen egy azonosító jogkivonatot a/Authorization-végponttól, a [regisztrációs portálon](https://portal.azure.com) lévő alkalmazás-regisztrációnak a hitelesítés lapon engedélyeznie kell a id_tokens engedélyezését (amely a `oauth2AllowIdTokenImplicitFlow` jelölőt az [alkalmazás jegyzékfájljában](reference-app-manifest.md) adja meg `true` ). Ha nincs engedélyezve, `unsupported_response` hibaüzenetet kap: "a (z) response_type bemeneti paraméter megadott értéke nem engedélyezett ehhez az ügyfélhez. A várt érték a "code" (kód)

Például:

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=678910
```

| Paraméter | Feltétel | Leírás |
| --- | --- | --- |
| `tenant` | Kötelező | A `{tenant}` kérelem elérési útjának értékét követve szabályozhatja, hogy ki jelentkezhet be az alkalmazásba. Az engedélyezett értékek:,, `common` `organizations` `consumers` és bérlői azonosítók. További információ: [protokoll alapjai](active-directory-v2-protocols.md#endpoints). |
| `client_id` | Kötelező | Az alkalmazáshoz hozzárendelt [Azure Portal – Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) felhasználói felület **(ügyfél) azonosítója** . |
| `response_type` | Kötelező | Tartalmaznia kell `id_token` az OpenID Connect bejelentkezést. Más értékeket is tartalmazhatnak `response_type` , például: `code` . |
| `redirect_uri` | Ajánlott | Az alkalmazás átirányítási URI-ja, ahol az alkalmazás elküldhet és fogadhat hitelesítési válaszokat. Pontosan meg kell egyeznie a portálon regisztrált átirányítási URI-k egyikével, azzal a különbséggel, hogy az URL-címet kell kódolni. Ha nem található, a végpont véletlenszerűen kiválaszt egy regisztrált redirect_uri, hogy visszaküldje a felhasználót a következőnek:. |
| `scope` | Kötelező | A hatókörök szóközzel tagolt listája. Az OpenID Connect esetében tartalmaznia kell a hatókört `openid` , amely a hozzájárulási felhasználói felületen a "Bejelentkezés" engedélyre van lefordítva. A kérelemben szereplő további hatóköröket is megadhatja a kéréshez a belefoglaláshoz. |
| `nonce` | Kötelező | Az alkalmazás által generált kérelemben szereplő érték, amely az eredményül kapott id_token értéket tartalmazza jogcímként. Az alkalmazás ellenőrizheti ezt az értéket a jogkivonat-Visszajátszási támadások enyhítése érdekében. Az érték általában egy véletlenszerű, egyedi karakterlánc, amely a kérelem forrásának azonosítására szolgál. |
| `response_mode` | Ajánlott | Meghatározza azt a módszert, amellyel az eredményül kapott engedélyezési kódot vissza lehet küldeni az alkalmazásnak. A következők egyike lehet: `form_post` vagy `fragment`. A webalkalmazások esetében javasoljuk `response_mode=form_post` , hogy a használatával biztosítsa a tokenek legbiztonságosabb átvitelét az alkalmazásba. |
| `state` | Ajánlott | A kérelemben szereplő érték, amely a jogkivonat-válaszban is szerepelni fog. A kívánt tartalom sztringje lehet. A véletlenszerűen generált egyedi érték általában a [helyek közötti kérelmek hamisításának megelőzésére](https://tools.ietf.org/html/rfc6749#section-10.12)szolgál. Az állapot az alkalmazásban a felhasználó állapotára vonatkozó információk kódolására is használatos, mielőtt a hitelesítési kérelem bekövetkezett volna, például a lap vagy a felhasználó megtekintése. |
| `prompt` | Választható | Megadja a szükséges felhasználói beavatkozás típusát. Ebben az esetben az egyetlen érvényes érték a, a `login` `none` és a `consent` . A `prompt=login` jogcím arra kényszeríti a felhasználót, hogy adja meg hitelesítő adatait a kérésen, amely megtagadja az egyszeri bejelentkezést. A `prompt=none` jogcím az ellenkezője. Ez a jogcím biztosítja, hogy a felhasználó semmilyen interaktív kéréssel sem jelenik meg. Ha a kérést nem lehet csendes úton végrehajtani egyszeri bejelentkezéssel, a Microsoft Identity platform hibát jelez. A `prompt=consent` jogcím a felhasználó bejelentkezése után elindítja a OAuth-engedélyezési párbeszédpanelt. A párbeszédpanel arra kéri a felhasználót, hogy adjon engedélyt az alkalmazásnak. |
| `login_hint` | Választható | Ezzel a paraméterrel előre kitöltheti a felhasználó bejelentkezési oldalának username és e-mail-cím mezőjét, ha már ismeri a felhasználónevet az idő előtt. Az alkalmazások gyakran ezt a paramétert használják az újrahitelesítés során, miután már kicsomagolta a felhasználónevet egy korábbi bejelentkezésből a `preferred_username` jogcím használatával. |
| `domain_hint` | Választható | A felhasználó tartománya egy összevont címtárban.  Ezzel kihagyhatja a felhasználó e-mail-alapú felderítési folyamatát, amely egy valamivel egyszerűbb felhasználói élmény érdekében a bejelentkezési oldalon halad át. A helyszíni címtárban (például AD FS) összevont bérlők esetében ez gyakran zökkenőmentes bejelentkezést eredményez a meglévő bejelentkezési munkamenet miatt. |

Ekkor a rendszer felszólítja a felhasználót, hogy adja meg a hitelesítő adatait, és fejezze be a hitelesítést. A Microsoft Identity platform ellenőrzi, hogy a felhasználó beleegyezett a `scope` lekérdezési paraméterben megadott engedélyekkel. Ha a felhasználó nem járult hozzá ezen engedélyek bármelyikéhez, a Microsoft Identity platform arra kéri a felhasználót, hogy fogadja el a szükséges engedélyeket. További információ: [engedélyek, beleegyező és több-bérlős alkalmazások](v2-permissions-and-consent.md).

A felhasználó hitelesítése és a hozzájárulás engedélyezése után a Microsoft Identity platform egy választ küld az alkalmazásnak a jelzett átirányítási URI-n a paraméterben megadott metódus használatával `response_mode` .

### <a name="successful-response"></a>Sikeres válasz

Sikeres válasz, ha a `response_mode=form_post` következőt használja:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Paraméter | Leírás |
| --- | --- |
| `id_token` | Az alkalmazás által kért azonosító jogkivonat. A `id_token` paraméter használatával ellenőrizheti a felhasználó identitását, és megkezdheti a munkamenetet a felhasználóval. Az azonosító jogkivonatokkal és azok tartalmával kapcsolatos további információkért tekintse meg a [ `id_tokens` hivatkozást](id-tokens.md). |
| `state` | Ha egy `state` paraméter szerepel a kérelemben, akkor a válaszban ugyanazt az értéket kell megjelennie. Az alkalmazásnak ellenőriznie kell, hogy a kérelemben és a válaszban szereplő állapot értékek azonosak-e. |

### <a name="error-response"></a>Hiba válasza

A hibaüzenetek az átirányítási URI-ba is küldhetők, hogy az alkalmazás kezelni tudja őket. A hiba a következőképpen néz ki:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Paraméter | Leírás |
| --- | --- |
| `error` | Hibakód-karakterlánc, amely a felmerülő hibák típusának besorolására és a hibákra való reagálásra használható. |
| `error_description` | Egy adott hibaüzenet, amely segítséget nyújt a hitelesítési hiba kiváltó okának azonosításában. |

### <a name="error-codes-for-authorization-endpoint-errors"></a>Az engedélyezési végpont hibáinak kódjai

A következő táblázat azokat a hibakódokat ismerteti, amelyeket a rendszer a `error` hiba válaszának paraméterében adhat vissza:

| Hibakód | Leírás | Ügyfél művelete |
| --- | --- | --- |
| `invalid_request` | Protokollhiba, például hiányzó, kötelező paraméter. |Javítsa ki és küldje el újra a kérelmet. Ez egy olyan fejlesztési hiba, amelyet általában a kezdeti tesztelés során kapnak. |
| `unauthorized_client` | Az ügyfélalkalmazás nem kérhet engedélyezési kódot. |Ez általában akkor fordul elő, ha az ügyfélalkalmazás nincs regisztrálva az Azure AD-ben, vagy nem kerül be a felhasználó Azure AD-bérlőbe. Az alkalmazás arra kéri a felhasználót, hogy telepítse az alkalmazást, és adja hozzá az Azure AD-hez. |
| `access_denied` | Az erőforrás tulajdonosa megtagadta a jóváhagyást. |Az ügyfélalkalmazás értesíti a felhasználót, hogy csak akkor tud továbblépni, ha a felhasználó beleegyezett. |
| `unsupported_response_type` |Az engedélyezési kiszolgáló nem támogatja a válasz típusát a kérelemben. |Javítsa ki és küldje el újra a kérelmet. Ez egy olyan fejlesztési hiba, amelyet általában a kezdeti tesztelés során kapnak. |
| `server_error` | A kiszolgáló váratlan hibát észlelt. |Próbálja megismételni a kérelmet. Ezek a hibák ideiglenes feltételekből származhatnak. Előfordulhat, hogy az ügyfélalkalmazás egy ideiglenes hiba miatt késlelteti a felhasználót, hogy a válasza késik. |
| `temporarily_unavailable` | A kiszolgáló átmenetileg túl elfoglalt a kérelem kezeléséhez. |Próbálja megismételni a kérelmet. Előfordulhat, hogy az ügyfélalkalmazás megmagyarázza a felhasználót, hogy a válasza egy ideiglenes feltétel miatt késleltetve van. |
| `invalid_resource` | A célként megadott erőforrás érvénytelen, mert vagy nem létezik, az Azure AD nem találja, vagy helytelenül van konfigurálva. |Ez azt jelzi, hogy az erőforrás (ha létezik) nem lett konfigurálva a bérlőben. Az alkalmazás arra kéri a felhasználót, hogy az alkalmazás telepítéséhez és az Azure AD-hez való hozzáadásához útmutatást adjon. |

## <a name="validate-the-id-token"></a>AZONOSÍTÓ jogkivonat ellenőrzése

Egy id_token fogadása nem mindig elegendő a felhasználó hitelesítéséhez; szükség lehet a id_token aláírásának érvényesítésére is, és az alkalmazás követelményeinek megfelelően ellenőriznie kell a jogkivonatok jogcímeit. Az összes OIDC platformhoz hasonlóan a Microsoft Identity platform [JSON webes tokeneket (JWTs)](https://tools.ietf.org/html/rfc7519) és nyilvános kulcsú titkosítást használ az azonosító jogkivonatok aláírásához, és ellenőrzi, hogy érvényesek-e.

Nem minden alkalmazás élvezheti az azonosító jogkivonat-natív alkalmazások és az egyoldalas alkalmazások ellenőrzését, például az azonosító jogkivonat érvényesítése ritkán jár el.  Az eszközhöz (vagy böngészőhöz) való fizikai hozzáféréssel rendelkező személy számos módon megkerülheti az érvényesítést – a webes forgalom az eszközre való szerkesztésével hamis jogkivonatokat és kulcsokat adhat meg az alkalmazásnak az érvényesítési logika kihagyásához.  Másfelől az azonosító jogkivonatot használó webalkalmazásoknak és API-knak a hitelesítéshez körültekintően kell ellenőriznie az azonosító jogkivonatot, mivel azok kapuzás férnek hozzá az adataihoz.

Miután ellenőrizte a id_token aláírását, van néhány jogcím, amelyet ellenőrizni kell. További információért lásd a [ `id_token` hivatkozást](id-tokens.md) , beleértve a [jogkivonatok érvényesítését](id-tokens.md#validating-an-id_token) és az [aláíró kulcsok átváltásával kapcsolatos fontos információkat](active-directory-signing-key-rollover.md). Azt javasoljuk, hogy használjon egy könyvtárat a tokenek elemzéséhez és érvényesítéséhez – legalább egy elérhető a legtöbb nyelven és platformon.

A forgatókönyvtől függően további jogcímeket is érdemes lehet érvényesíteni. Egyes gyakori érvényesítések a következők:

* Annak biztosítása, hogy a felhasználó/szervezet regisztrálva legyen az alkalmazásra.
* Annak biztosítása, hogy a felhasználó megfelelő jogosultságokkal rendelkezzen
* A hitelesítés bizonyos erősségének biztosítása, például a [többtényezős hitelesítés](../authentication/concept-mfa-howitworks.md).

Miután ellenőrizte a id_token, elindíthat egy munkamenetet a felhasználóval, és a id_token jogcímeit használva adatokat szerezhet be a felhasználóval kapcsolatban az alkalmazásban. Ezek az információk megjeleníthetők, rekordok, személyre szabás stb. esetén használhatók.

## <a name="protocol-diagram-access-token-acquisition"></a>Protokoll diagramja: hozzáférési jogkivonat beszerzése

Számos webalkalmazásnak nem csak a felhasználónak kell aláírnia a szolgáltatást, hanem a felhasználó nevében egy webszolgáltatáshoz is hozzá kell férnie a OAuth használatával. Ez a forgatókönyv ötvözi az OpenID connectet a felhasználói hitelesítéshez, miközben olyan engedélyezési kódot kap, amelyet a hozzáférési jogkivonatok beszerzéséhez használhat, ha a OAuth engedélyezési kód folyamatát használja.

A teljes OpenID Connect-bejelentkezés és a jogkivonat-gyűjtési folyamat a következő diagramhoz hasonlóan néz ki. Az egyes lépéseket részletesen ismertetjük a cikk következő fejezeteiben.

![OpenID Connect protokoll: token beszerzése](./media/v2-protocols-oidc/convergence-scenarios-webapp-webapi.svg)

## <a name="get-an-access-token-to-call-userinfo"></a>Hozzáférési jogkivonat beszerzése a UserInfo meghívásához

A OIDC UserInfo-végponthoz tartozó jogkivonat beszerzéséhez módosítsa a bejelentkezési kérést:

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token%20token                       // this will return both an id_token and an access token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered redirect URI, URL encoded
&response_mode=form_post                              // 'form_post' or 'fragment'
&scope=openid+profile+email                           // `openid` is required.  `profile` and `email` provide additional information in the UserInfo endpoint the same way they do in an ID token. 
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

Az alkalmazáshoz tartozó jogkivonat lekéréséhez használhatja az [engedélyezési kód folyamatát](v2-oauth2-auth-code-flow.md), az [eszköz kódjának folyamatát](v2-oauth2-device-code.md)vagy a helyett egy [frissítési jogkivonatot](v2-oauth2-auth-code-flow.md#refresh-the-access-token) is `response_type=token` .

> [!TIP]
> A kérelem végrehajtásához kattintson a következő hivatkozásra. A bejelentkezést követően a rendszer átirányítja a böngészőt `https://localhost/myapp/` , egy azonosító jogkivonattal és egy tokenrel a címsorban. Vegye figyelembe, hogy ez a kérelem `response_mode=fragment` csak demonstrációs célokat szolgál – egy WebApp esetében ajánlott `form_post` további biztonságot használni, ahol lehetséges. 
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid+profile+email&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

### <a name="successful-token-response"></a>Sikeres jogkivonat-válasz

A használatból való sikeres válasz a `response_mode=form_post` következőképpen néz ki:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded
 access_token=eyJ0eXAiOiJKV1QiLCJub25jZSI6I....
 &token_type=Bearer
 &expires_in=3598
 &scope=email+openid+profile
 &id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI....
 &state=12345
```

A válasz paraméterei ugyanazt a dolgot jelentik, függetlenül attól, hogy milyen folyamatot használnak.

| Paraméter | Leírás |
| --- | --- |
| `access_token` | Az UserInfo-végpont meghívásához használt jogkivonat.|
| `token_type` | Mindig "tulajdonos" |
| `expires_in`| A hozzáférési jogkivonat lejárati ideje (másodperc). |
| `scope` | A hozzáférési jogkivonaton megadott engedélyek.  Vegye figyelembe, hogy mivel az UserInfo-végpontot MS Graph üzemelteti, előfordulhat, hogy további gráf-hatókörök is szerepelnek itt (például user. Read), ha korábban már megadták az alkalmazást.  Ennek az az oka, hogy egy adott erőforráshoz tartozó jogkivonat mindig tartalmazza az ügyfél számára jelenleg biztosított összes engedélyt.  |
| `id_token` | Az alkalmazás által kért azonosító jogkivonat. Az azonosító token használatával ellenőrizheti a felhasználó identitását, és megkezdheti a munkamenetet a felhasználóval. Az azonosító jogkivonatokkal és azok tartalmával kapcsolatos további részletekért tekintse meg a [ `id_tokens` hivatkozást](id-tokens.md). |
| `state` | Ha a kérelemben szerepel egy State paraméter, akkor a válaszban ugyanazt az értéket kell megjelennie. Az alkalmazásnak ellenőriznie kell, hogy a kérelemben és a válaszban szereplő állapot értékek azonosak-e. |

### <a name="error-response"></a>Hiba válasza

A rendszer az átirányítási URI-nak is küldhet hibákat, hogy az alkalmazás megfelelően tudja kezelni őket. A hiba a következőképpen néz ki:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Paraméter | Leírás |
| --- | --- |
| `error` | Hibakód-karakterlánc, amely a felmerülő hibák típusának besorolására és a hibákra való reagálásra használható. |
| `error_description` | Egy adott hibaüzenet, amely segítséget nyújt a hitelesítési hiba kiváltó okának azonosításában. |

A lehetséges hibakódok és az ajánlott ügyfél-válaszok leírását lásd: [hibakódok az engedélyezési végpont hibáihoz](#error-codes-for-authorization-endpoint-errors).

Ha rendelkezik egy engedélyezési kóddal és egy azonosító jogkivonattal, akkor aláírhatja a felhasználót a alkalmazásban, és hozzáférési jogkivonatokat kaphat a nevében. A felhasználó a alkalmazásban való aláírásához [pontosan a leírtak szerint](id-tokens.md#validating-an-id_token)ellenőriznie kell az azonosító tokent. A hozzáférési jogkivonatok beszerzéséhez kövesse a [OAuth Code flow dokumentációjában](v2-oauth2-auth-code-flow.md#request-an-access-token)ismertetett lépéseket.

### <a name="calling-the-userinfo-endpoint"></a>Az UserInfo-végpont meghívása

Tekintse át a [UserInfo dokumentációját](userinfo.md#calling-the-api) , és tekintse át, hogyan hívja meg a UserInfo-végpontot ezzel a jogkivonattal.

## <a name="send-a-sign-out-request"></a>Kijelentkezési kérelem küldése

Ha a felhasználót az alkalmazásból szeretné kijelentkezni, nem elegendő az alkalmazás cookie-jai törléséhez, vagy más módon a felhasználó munkamenetének befejezéséhez. A kijelentkezéshez a felhasználót is át kell irányítani a Microsoft Identity platformra. Ha ezt nem teszi meg, a felhasználó a hitelesítő adatok újbóli megadása nélkül újra hitelesíti az alkalmazást, mivel a Microsoft Identity platformmal érvényes egyszeri bejelentkezési munkamenet lesz.

Átirányíthatja a felhasználót az `end_session_endpoint` OpenID Connect metadata dokumentum listájában szereplő listára:

```HTTP
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Paraméter | Feltétel | Leírás |
| ----------------------- | ------------------------------- | ------------ |
| `post_logout_redirect_uri` | Ajánlott | Az URL-cím, amelyet a felhasználó átirányított a sikeres kijelentkezés után. Ha a paraméter nincs feltüntetve, a felhasználó a Microsoft Identity platform által generált általános üzenetet jelenít meg. Ennek az URL-címnek meg kell egyeznie az alkalmazás regisztrációs portálján az alkalmazáshoz regisztrált átirányítási URI-k egyikével. |

## <a name="single-sign-out"></a>Egyszeri kijelentkezés

Amikor átirányítja a felhasználót a alkalmazásba `end_session_endpoint` , a Microsoft Identity platform törli a felhasználó munkamenetét a böngészőből. Előfordulhat azonban, hogy a felhasználó továbbra is bejelentkezett a Microsoft-fiókokat használó más alkalmazásokba a hitelesítéshez. Annak engedélyezéséhez, hogy az alkalmazások egyidejűleg írják alá a felhasználót, a Microsoft Identity platform egy HTTP GET kérelmet küld az összes olyan alkalmazás regisztrálásához, `LogoutUrl` amelyhez a felhasználó jelenleg be van jelentkezve. Az alkalmazásoknak válaszolnia kell erre a kérelemre a felhasználót azonosító munkamenetek törlésével és a válasz visszaadásával `200` . Ha szeretné támogatni az egyszeri kijelentkezést az alkalmazásban, akkor az alkalmazás kódjában be kell vezetnie egy ilyen `LogoutUrl` alkalmazást. Az `LogoutUrl` alkalmazást az alkalmazás regisztrációs portálján állíthatja be.

## <a name="next-steps"></a>Következő lépések

* A [UserInfo dokumentációjának](userinfo.md) áttekintése
* Megtudhatja, hogyan [szabhatja testre a tokenek értékeit](active-directory-claims-mapping.md) a helyszíni rendszerek adataival. 
* Megtudhatja, hogyan [vehet fel további standard jogcímeket a jogkivonatokban](active-directory-optional-claims.md).  
