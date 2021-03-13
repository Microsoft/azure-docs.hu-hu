---
title: Engedélyezési kód folyamatábrája – Azure Active Directory B2C | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre webalkalmazásokat a Azure AD B2C és az OpenID Connect hitelesítési protokoll használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: a6a993fdf4fd266afb9459fedd13412d8796e0a5
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102611504"
---
# <a name="oauth-20-authorization-code-flow-in-azure-active-directory-b2c"></a>OAuth 2,0 engedélyezési kód folyamata Azure Active Directory B2C

Az eszközön telepített alkalmazásokban a OAuth 2,0-as engedélyezési kód segítségével hozzáférhet a védett erőforrásokhoz, például a webes API-khoz. A OAuth 2,0 Azure Active Directory B2C (Azure AD B2C) implementációjának használatával felveheti a regisztrációs, bejelentkezési és egyéb Identitáskezelés-felügyeleti feladatokat az egyoldalas, mobil és asztali alkalmazásokhoz. Ez a cikk nyelvtől független. A cikkben bemutatjuk, hogyan küldhet és fogadhat HTTP-üzeneteket a nyílt forráskódú kódtárak használata nélkül. Ha lehetséges, javasoljuk, hogy használja a támogatott Microsoft-hitelesítési kódtárakat (MSAL-ket). Tekintse át az MSAL-t [használó minta alkalmazásokat](code-samples.md).

Az OAuth 2,0 engedélyezési kód folyamatát a [OAuth 2,0 specifikáció 4,1. szakasza](https://tools.ietf.org/html/rfc6749)ismerteti. Használhatja hitelesítésre és engedélyezésre a legtöbb alkalmazás- [típusban](application-types.md), beleértve a webalkalmazásokat, az egylapos alkalmazásokat és a natív módon telepített alkalmazásokat. Az OAuth 2,0 engedélyezési kód folyamatával biztonságosan kaphat hozzáférési jogkivonatokat, és frissítheti az alkalmazásaihoz tartozó jogkivonatokat, amelyek az [engedélyezési kiszolgáló](protocols-overview.md)által védett erőforrások elérésére használhatók.  A frissítési jogkivonat lehetővé teszi, hogy az ügyfél új hozzáférési (és frissítési) jogkivonatokat szerezzen be a hozzáférési jogkivonat lejárta után, általában egy óra elteltével.

Ez a cikk a **nyilvános ügyfelek** OAuth 2,0 engedélyezési kód folyamatát ismerteti. A nyilvános ügyfél olyan ügyfélalkalmazás, amely nem megbízható a titkos jelszó integritásának biztonságos fenntartásához. Ilyenek például az egylapos alkalmazások, a mobil alkalmazások, az asztali alkalmazások és lényegében bármely olyan alkalmazás, amely nem fut a kiszolgálón.

> [!NOTE]
> Ha Azure AD B2C használatával szeretne identitás-kezelést hozzáadni egy webalkalmazáshoz, használja az [OpenID connectet](openid-connect.md) a OAuth 2,0 helyett.

Azure AD B2C kiterjeszti a standard OAuth 2,0-as folyamatait, hogy több, mint egyszerű hitelesítést és engedélyezést végezzenek. Bevezeti a [felhasználói folyamatot](user-flow-overview.md). A felhasználói folyamatok esetében a OAuth 2,0 használatával felhasználói élményeket adhat hozzá az alkalmazáshoz, például a regisztráláshoz, a bejelentkezéshez és a profilok kezeléséhez. Az OAuth 2,0 protokollt használó azonosítók közé tartozik az [Amazon](identity-provider-amazon.md), a [Azure Active Directory](identity-provider-azure-ad-single-tenant.md), a [Facebook](identity-provider-facebook.md), a [GitHub](identity-provider-github.md), a [Google](identity-provider-google.md)és a [LinkedIn](identity-provider-linkedin.md).

A cikkben szereplő HTTP-kérelmek kipróbálásához:

1. Cserélje le a helyére `{tenant}` a Azure ad B2C bérlő nevét.
1. Cserélje le `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` egy olyan alkalmazás azonosítóját, amelyet korábban már regisztrált a Azure ad B2C-bérlőben.
1. Cserélje le a helyére `{policy}` a bérlőben létrehozott szabályzat nevét, például: `b2c_1_sign_in` .

## <a name="redirect-uri-setup-required-for-single-page-apps"></a>Az egyoldalas alkalmazásokhoz szükséges átirányítási URI-beállítás

Az egyoldalas alkalmazásokhoz tartozó engedélyezési kód esetében további beállításokra van szükség.  Kövesse az [egyoldalas alkalmazás létrehozásának](tutorial-register-spa.md) utasításait, hogy az átirányítási URI-t helyesen megjelölje a CORS. Ha frissíteni szeretne egy meglévő átirányítási URI-t a CORS engedélyezéséhez, kattintson az áttelepítés promptra az **alkalmazás regisztrációjának** **hitelesítése** lap "web" szakaszában. Másik lehetőségként megnyithatja a **Alkalmazásregisztrációk jegyzékfájl-szerkesztőt** , és beállíthatja az `type` átirányítási URI mezőjét `spa` a következő `replyUrlsWithType` szakaszra:.

Az `spa` átirányítás típusa visszamenőlegesen kompatibilis az implicit folyamattal. Azok az alkalmazások, amelyek jelenleg az implicit folyamatot használják a jogkivonatok fogadására, problémák nélkül áthelyezhetők az `spa` átirányítási URI-típusra, és folytatják az implicit folyamat használatát.

## <a name="1-get-an-authorization-code"></a>1. engedélyezési kód beszerzése
Az engedélyezési kód folyamata azzal kezdődik, hogy az ügyfél átirányítja a felhasználót a `/authorize` végpontra. Ez a folyamat interaktív része, ahol a felhasználó végrehajtja a műveletet. Ebben a kérelemben az ügyfél a (z `scope` ) paraméterben azt jelzi, hogy milyen engedélyek szükségesek a felhasználótól való beolvasáshoz. A következő három példa (az olvashatóság érdekében sortöréssel) minden más felhasználói folyamatot használ.


```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&code_challenge=YTFjNjI1OWYzMzA3MTI4ZDY2Njg5M2RkNmVjNDE5YmEyZGRhOGYyM2IzNjdmZWFhMTQ1ODg3NDcxY2Nl
&code_challenge_method=S256
```

| Paraméter | Kötelező? | Leírás |
| --- | --- | --- |
|Bérlő| Kötelező | A Azure AD B2C bérlő neve|
| politika | Kötelező | A futtatandó felhasználói folyamat. Adja meg a Azure AD B2C bérlőben létrehozott felhasználói folyamat nevét. Például: `b2c_1_sign_in` , `b2c_1_sign_up` , vagy `b2c_1_edit_profile` . |
| client_id |Kötelező |Az alkalmazáshoz a [Azure Portal](https://portal.azure.com)hozzárendelt alkalmazás-azonosító. |
| response_type |Kötelező |A válasz típusa, amelynek tartalmaznia kell `code` az engedélyezési kód folyamatát. |
| redirect_uri |Kötelező |Az alkalmazás átirányítási URI-ja, ahol az alkalmazás elküldi és fogadja a hitelesítési válaszokat. Pontosan meg kell egyeznie a portálon regisztrált átirányítási URI-k egyikével, azzal a különbséggel, hogy az URL-kódolásnak kell lennie. |
| scope |Kötelező |A hatókörök szóközzel tagolt listája. Egyetlen hatóköri érték azt jelzi, hogy Azure Active Directory (Azure AD) a kért engedélyek mindegyikét. Az ügyfél-azonosító hatókörének használata azt jelzi, hogy az alkalmazásnak olyan hozzáférési jogkivonatra van szüksége, amely a saját szolgáltatásán vagy webes API-ban használható, ugyanazokat az ügyfél-azonosítót jelképezve.  A `offline_access` hatókör azt jelzi, hogy az alkalmazásnak frissítési jogkivonatra van szüksége az erőforrásokhoz való hosszú élettartamú hozzáféréshez. A `openid` hatókör használatával Azure ad B2C azonosító jogkivonatot is igényelhet. |
| response_mode |Ajánlott |Az eredményül kapott engedélyezési kód az alkalmazásba való visszaküldéséhez használt módszer. Ez lehet a `query` , `form_post` a vagy a `fragment` . |
| állapot |Ajánlott |A kérelemben szereplő érték, amely a használni kívánt tartalom karakterlánca lehet. A rendszer általában véletlenszerűen generált egyedi értéket használ, hogy megakadályozza a helyek közötti kérelmek hamisításának támadásait. Az állapot az alkalmazásban a felhasználó állapotára vonatkozó információk kódolására is használatos, mielőtt a hitelesítési kérelem bekövetkezett volna. Például a felhasználó lapja vagy a végrehajtás alatt álló felhasználói folyamat. |
| gyors |Választható |A kötelező felhasználói beavatkozás típusa. Jelenleg az egyetlen érvényes érték a `login` , amely arra kényszeríti a felhasználót, hogy adja meg hitelesítő adatait a kérésen. Az egyszeri bejelentkezés nem lép érvénybe. |
| code_challenge  | ajánlott/szükséges | Az engedélyezési kód támogatásának biztosítására szolgál a Code Exchange (PKCE) igazolási kulcsán keresztül. Kötelező `code_challenge_method` , ha szerepel benne. További információ: [PKCE RFC](https://tools.ietf.org/html/rfc7636). Ezt mostantól ajánlott az összes alkalmazás típusa – a natív alkalmazások, a fürdők és a bizalmas ügyfelek, például a Web Apps. | 
| `code_challenge_method` | ajánlott/szükséges | A paraméter kódolásához használt metódus `code_verifier` `code_challenge` . Ennek *a következőnek* kell lennie `S256` , de a specifikáció lehetővé teszi a használatát, `plain` Ha valamilyen okból kifolyólag az ügyfél nem támogatja a sha256. <br/><br/>Ha a szolgáltatás ki van zárva, a rendszer azt `code_challenge` feltételezi, hogy egyszerű szöveg, ha `code_challenge` szerepel benne. A Microsoft Identity platform mind `plain` a, mind a-t támogatja `S256` . További információ: [PKCE RFC](https://tools.ietf.org/html/rfc7636). Ez az [engedélyezési kód folyamatát használó egylapos alkalmazások](tutorial-register-spa.md)esetében szükséges.|
| login_hint | No| A bejelentkezési oldal bejelentkezési név mezőjének előzetes kitöltésére is használható. További információ: [a bejelentkezési név előre való feltöltése](direct-signin.md#prepopulate-the-sign-in-name).  |
| domain_hint | No| Útmutatást nyújt Azure AD B2C a bejelentkezéshez használandó közösségi identitás-szolgáltatóról. Ha érvényes értéket tartalmaz, a felhasználó közvetlenül az Identity Provider bejelentkezési lapjára lép.  További információ: [Bejelentkezés átirányítása a közösségi szolgáltatóba](direct-signin.md#redirect-sign-in-to-a-social-provider). |
| Egyéni paraméterek | No| Egyéni [házirendek](custom-policy-overview.md)használatával használható egyéni paraméterek. Például a [dinamikus egyéni oldal tartalmi URI-ja](customize-ui-with-html.md?pivots=b2c-custom-policy#configure-dynamic-custom-page-content-uri)vagy a [kulcs-érték jogcím feloldója](claim-resolver-overview.md#oauth2-key-value-parameters). |

Ekkor a rendszer megkéri a felhasználót, hogy fejezze be a felhasználói folyamat munkafolyamatát. Ez magában foglalhatja a felhasználónevet és a jelszót, illetve a közösségi identitással való bejelentkezést, a címtárra való regisztrációt, illetve az egyéb lépések számát is. A felhasználói műveletek attól függnek, hogy a felhasználói folyamat hogyan van definiálva.

Miután a felhasználó befejezte a felhasználói folyamatot, az Azure AD egy választ küld az alkalmazásnak a használt értékre `redirect_uri` . A paraméterben megadott metódust használja `response_mode` . A válasz pontosan ugyanaz, mint a felhasználói műveletek egyes forgatókönyvei esetében, a végrehajtott felhasználói folyamattól függetlenül.

Sikeres válasz, amely a `response_mode=query` következőképpen néz ki:

```http
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Paraméter | Leírás |
| --- | --- |
| code |Az alkalmazás által kért engedélyezési kód. Az alkalmazás az engedélyezési kóddal kérhet hozzáférési tokent a cél erőforráshoz. Az engedélyezési kódok nagyon rövid életűek. Általában körülbelül 10 perc elteltével lejárnak. |
| állapot |Az előző szakaszban található táblázatban tekintse meg a teljes leírást. Ha egy `state` paraméter szerepel a kérelemben, akkor a válaszban ugyanazt az értéket kell megjelennie. Az alkalmazásnak ellenőriznie kell, hogy a `state` kérelemben és a válaszban szereplő értékek azonosak-e. |

A rendszer a hibaüzeneteket is elküldheti az átirányítási URI-nak, hogy az alkalmazás megfelelően tudja kezelni őket:

```http
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paraméter | Leírás |
| --- | --- |
| error |Hibakód-karakterlánc, amely a felmerülő hibák típusának besorolására használható. A karakterláncot is használhatja a hibákra való reagálásra. |
| error_description |Egy adott hibaüzenet, amely segítséget nyújt a hitelesítési hiba kiváltó okának azonosításában. |
| állapot |Tekintse meg az előző táblázatban szereplő teljes leírást. Ha egy `state` paraméter szerepel a kérelemben, akkor a válaszban ugyanazt az értéket kell megjelennie. Az alkalmazásnak ellenőriznie kell, hogy a `state` kérelemben és a válaszban szereplő értékek azonosak-e. |

## <a name="2-get-an-access-token"></a>2. hozzáférési jogkivonat beszerzése
Most, hogy beszerzett egy engedélyezési kódot, beválthatja a `code` tokent a kívánt erőforrásra egy post-kérelem küldésével a `/token` végpontnak. A Azure AD B2Cban a [más API-k számára a szokásos módon kérhet hozzáférési jogkivonatokat](access-tokens.md#request-a-token) a kérelemben szereplő hatókör (ek) megadásával.

Az alkalmazás saját háttérbeli webes API-hoz is kérhet hozzáférési tokent, ha az alkalmazás ügyfél-AZONOSÍTÓját használja a kért hatókörként (amely az ügyfél-AZONOSÍTÓhoz tartozó hozzáférési jogkivonatot eredményezi a "célközönség"):

```http
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&code_verifier=ThisIsntRandomButItNeedsToBe43CharactersLong 
```

| Paraméter | Kötelező? | Leírás |
| --- | --- | --- |
|Bérlő| Kötelező | A Azure AD B2C bérlő neve|
|politika| Kötelező| Az engedélyezési kód beszerzéséhez használt felhasználói folyamat. Ebben a kérelemben nem használhat másik felhasználói folyamatot. |
| client_id |Kötelező |Az alkalmazáshoz a [Azure Portal](https://portal.azure.com)hozzárendelt alkalmazás-azonosító.|
| client_secret | Igen, Web Apps | Az [Azure Portalban](https://portal.azure.com/)létrehozott alkalmazás titka. A folyamat az ügyfél titkos kulcsait használja a webalkalmazási forgatókönyvek esetében, ahol az ügyfél biztonságosan tárolhat egy ügyfél titkát. A natív alkalmazások (nyilvános ügyfelek) esetében az ügyfél titkos kulcsai nem tárolhatók biztonságosan, ezért nem használhatók ebben a hívásban. Ha ügyfél-titkos kulcsot használ, rendszeres időközönként módosítsa azt. |
| grant_type |Kötelező |A támogatás típusa. Az engedélyezési kód folyamatához a típusnak a következőnek kell lennie: `authorization_code` . |
| scope |Ajánlott |A hatókörök szóközzel tagolt listája. Egyetlen hatóköri érték azt jelzi, hogy az Azure AD-t mind a kért engedélyek jelentik. Az ügyfél-azonosító hatókörének használata azt jelzi, hogy az alkalmazásnak olyan hozzáférési jogkivonatra van szüksége, amely a saját szolgáltatásán vagy webes API-ban használható, ugyanazokat az ügyfél-azonosítót jelképezve.  A `offline_access` hatókör azt jelzi, hogy az alkalmazásnak frissítési jogkivonatra van szüksége az erőforrásokhoz való hosszú élettartamú hozzáféréshez.  A `openid` hatókör használatával Azure ad B2C azonosító jogkivonatot is igényelhet. |
| code |Kötelező |A folyamat első szakaszában beszerzett engedélyezési kód. |
| redirect_uri |Kötelező |Annak az alkalmazásnak az átirányítási URI azonosítója, amelyen az engedélyezési kódot megkapta. |
| code_verifier | ajánlott | Ugyanaz a code_verifier, amelyet a authorization_code beszerzéséhez használt. Kötelező, ha a PKCE az engedélyezési kód Grant kérelmében használták. További információ: [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

Egy sikeres jogkivonat-válasz a következőképpen néz ki:

```json
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Paraméter | Leírás |
| --- | --- |
| not_before |Az az időpont, amikor a jogkivonat érvényesnek minősül, a kor időpontjában. |
| token_type |A jogkivonat típusának értéke. Az Azure AD által támogatott egyetlen típus a tulajdonos. |
| access_token |A kért aláírt JSON Web Token (JWT). |
| scope |Azok a hatókörök, amelyekhez a jogkivonat érvényes. Hatóköröket is használhat a tokenek későbbi használatra való gyorsítótárazásához. |
| expires_in |Az az időtartam, ameddig a jogkivonat érvényes (másodpercben). |
| refresh_token |Egy OAuth 2,0 frissítési token. Az alkalmazás az aktuális jogkivonat lejárta után további jogkivonatok beszerzésére használhatja ezt a tokent. A frissítési tokenek hosszú életűek. A segítségével hosszabb ideig megőrizheti az erőforrásokhoz való hozzáférést. További információ: [Azure ad B2C jogkivonat-hivatkozás](tokens-overview.md). |

A hibaüzenetek így néznek ki:

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Paraméter | Leírás |
| --- | --- |
| error |Hibakód-karakterlánc, amely a felmerülő hibák típusának besorolására használható. A karakterláncot is használhatja a hibákra való reagálásra. |
| error_description |Egy adott hibaüzenet, amely segítséget nyújt a hitelesítési hiba kiváltó okának azonosításában. |

## <a name="3-use-the-token"></a>3. a jogkivonat használata
Most, hogy sikeresen beszerzett egy hozzáférési jogkivonatot, a jogkivonatot a háttérbeli webes API-khoz a kérésekben a fejlécbe belefoglalva is használhatja `Authorization` :

```http
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. a jogkivonat frissítése
A hozzáférési tokenek és az azonosító tokenek rövid életűek. A lejárat után frissítenie kell őket, hogy továbbra is hozzáférhessenek az erőforrásokhoz. Ehhez egy másik POST-kérést kell elküldeni a `/token` végpontnak. Ezúttal adja meg a következő `refresh_token` helyet `code` :

```http
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Paraméter | Kötelező? | Leírás |
| --- | --- | --- |
|Bérlő| Kötelező | A Azure AD B2C bérlő neve|
|politika |Kötelező |Az eredeti frissítési jogkivonat beszerzéséhez használt felhasználói folyamat. Ebben a kérelemben nem használhat másik felhasználói folyamatot. |
| client_id |Kötelező |Az alkalmazáshoz a [Azure Portal](https://portal.azure.com)hozzárendelt alkalmazás-azonosító. |
| client_secret | Igen, Web Apps | Az [Azure Portalban](https://portal.azure.com/)létrehozott alkalmazás titka. A folyamat az ügyfél titkos kulcsait használja a webalkalmazási forgatókönyvek esetében, ahol az ügyfél biztonságosan tárolhat egy ügyfél titkát. A natív alkalmazások (nyilvános ügyfelek) esetében az ügyfél titkos kulcsai nem tárolhatók biztonságosan, ezért nem használhatók ebben a hívásban. Ha ügyfél-titkos kulcsot használ, rendszeres időközönként módosítsa azt. |
| grant_type |Kötelező |A támogatás típusa. Az engedélyezési kód folyamatának ezen szakasza esetében a Grant típusnak a következőnek kell lennie: `refresh_token` . |
| scope |Ajánlott |A hatókörök szóközzel tagolt listája. Egyetlen hatóköri érték azt jelzi, hogy az Azure AD-t mind a kért engedélyek jelentik. Az ügyfél-azonosító hatókörének használata azt jelzi, hogy az alkalmazásnak olyan hozzáférési jogkivonatra van szüksége, amely a saját szolgáltatásán vagy webes API-ban használható, ugyanazokat az ügyfél-azonosítót jelképezve.  A `offline_access` hatókör azt jelzi, hogy az alkalmazásnak frissítési tokenre lesz szüksége az erőforrásokhoz való hosszú élettartamú hozzáféréshez.  A `openid` hatókör használatával Azure ad B2C azonosító jogkivonatot is igényelhet. |
| redirect_uri |Választható |Annak az alkalmazásnak az átirányítási URI azonosítója, amelyen az engedélyezési kódot megkapta. |
| refresh_token |Kötelező |A folyamat második szakaszában beszerzett eredeti frissítési jogkivonat. |

Egy sikeres jogkivonat-válasz a következőképpen néz ki:

```json
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Paraméter | Leírás |
| --- | --- |
| not_before |Az az időpont, amikor a jogkivonat érvényesnek minősül, a kor időpontjában. |
| token_type |A jogkivonat típusának értéke. Az Azure AD által támogatott egyetlen típus a tulajdonos. |
| access_token |A kért aláírt JWT. |
| scope |Azok a hatókörök, amelyekhez a jogkivonat érvényes. A hatóköröket használhatja a tokenek későbbi használatra való gyorsítótárazásához is. |
| expires_in |Az az időtartam, ameddig a jogkivonat érvényes (másodpercben). |
| refresh_token |Egy OAuth 2,0 frissítési token. Az alkalmazás az aktuális jogkivonat lejárta után további jogkivonatok beszerzésére használhatja ezt a tokent. A frissítési tokenek hosszú életűek, és az erőforrásokhoz való hozzáférés hosszabb ideig is használhatók. További információ: [Azure ad B2C jogkivonat-hivatkozás](tokens-overview.md). |

A hibaüzenetek így néznek ki:

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Paraméter | Leírás |
| --- | --- |
| error |Hibakód-karakterlánc, amely a hibák típusának besorolására használható. A karakterláncot is használhatja a hibákra való reagálásra. |
| error_description |Egy adott hibaüzenet, amely segítséget nyújt a hitelesítési hiba kiváltó okának azonosításában. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>Saját Azure AD B2C címtár használata
Ha szeretné kipróbálni ezeket a kéréseket, hajtsa végre az alábbi lépéseket. Cserélje le a cikkben használt példa értékeket a saját értékeire.

1. [Hozzon létre egy Azure ad B2C könyvtárat](tutorial-create-tenant.md). Használja a címtár nevét a kérelmekben.
2. [Hozzon létre egy alkalmazást](tutorial-register-applications.md) egy alkalmazás-azonosító és egy ÁTirányítási URI beszerzéséhez. Adjon meg egy natív ügyfelet az alkalmazásban.
3. [Hozza létre a felhasználói folyamatokat](user-flow-overview.md) a felhasználói folyamatok nevének beszerzéséhez.
