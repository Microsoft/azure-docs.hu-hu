---
title: Egyoldalas bejelentkezés implicit folyamat használatával
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan adhat hozzá egyoldalas bejelentkezést a OAuth 2,0 implicit folyamattal Azure Active Directory B2C használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/19/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: fe31e1bf095d15cfdd7945288486cb866ace8246
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94840610"
---
# <a name="single-page-sign-in-using-the-oauth-20-implicit-flow-in-azure-active-directory-b2c"></a>Egyoldalas bejelentkezés a OAuth 2,0 implicit flow használatával Azure Active Directory B2C

Számos modern alkalmazás rendelkezik egy egyoldalas alkalmazás előtérrel, amely elsősorban a JavaScriptben van írva. Az alkalmazás gyakran egy olyan keretrendszer használatával történik, mint a reagálás, a szögletes vagy a Vue.js. Az egylapos alkalmazások és más, elsősorban böngészőben futtatott JavaScript-alkalmazások további kihívásokkal rendelkeznek a hitelesítéshez:

- Ezeknek az alkalmazásoknak a biztonsági jellemzői eltérnek a hagyományos, kiszolgáló-alapú webalkalmazásokkal.
- Számos engedélyezési kiszolgáló és Identitáskezelő nem támogatja a különböző eredetű erőforrás-megosztási (CORS) kérelmeket.
- A teljes képernyős böngésző átirányítja az alkalmazásból a felhasználói élményhez.

Az egyoldalas alkalmazások támogatásának ajánlott módja a [OAuth 2,0 engedélyezési kód folyamata (PKCE)](./authorization-code-flow.md).

Egyes keretrendszerek, például [MSAL.js 1. x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core), csak az implicit engedélyezési folyamatot támogatják. Ezekben az esetekben a Azure Active Directory B2C (Azure AD B2C) támogatja a OAuth 2,0-engedélyezés implicit engedélyezési folyamatát. [A OAuth 2,0 specifikáció 4,2. szakasza](https://tools.ietf.org/html/rfc6749)ismerteti az Ön folyamatát. Az implicit folyamat során az alkalmazás közvetlenül a Azure Active Directory (Azure AD) engedélyezi a végpontot, a kiszolgáló és a kiszolgáló közötti Exchange nélkül. Az összes hitelesítési logika és munkamenet-kezelés teljes egészében a JavaScript-ügyfélen történik, vagy egy oldal átirányítása vagy egy előugró ablak jelenik meg.

Azure AD B2C kiterjeszti a standard OAuth 2,0 implicit folyamatot több mint egyszerű hitelesítésre és engedélyezésre. Azure AD B2C bevezeti a [házirend paramétert](user-flow-overview.md). A Policy paraméterrel a OAuth 2,0 használatával szabályzatokat adhat hozzá az alkalmazáshoz, például a regisztráláshoz, a bejelentkezéshez és a profilokhoz tartozó felhasználói folyamatokhoz. A jelen cikkben szereplő HTTP-kérelmekben a **{bérlő}. onmicrosoft. com** példaként van használatban. Cserélje le a `{tenant}` nevet a bérlő nevére, ha van ilyen, és létrehozta a felhasználói folyamatot is.

Az implicit bejelentkezési folyamat az alábbi ábrához hasonlóan néz ki. Az egyes lépéseket a cikk későbbi részében részletesen ismertetjük.

![Sáv stílusú diagram, amely az OpenID Connect implicit folyamatát mutatja](./media/implicit-flow-single-page-application/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>Hitelesítési kérelmek küldése

Ha a webalkalmazásnak hitelesítenie kell a felhasználót, és futtatnia kell egy felhasználói folyamatot, akkor a felhasználó a végpontra irányíthatja a felhasználót `/authorize` . A felhasználó a felhasználói folyamattól függően végrehajtja a műveletet.

Ebben a kérelemben az ügyfél a `scope` paraméterben és a felhasználói folyamat futtatásához szükséges engedélyeket jelzi. Ha úgy érzi, hogy a kérés hogyan működik, próbálja meg beilleszteni a kérést egy böngészőben, és futtassa azt. Cserélje le a helyére `{tenant}` a Azure ad B2C bérlő nevét. A helyére írja be annak az `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` alkalmazásnak az azonosítóját, amelyet korábban regisztrált a bérlőben. Cserélje le a helyére `{policy}` a bérlőben létrehozott szabályzat nevét, például: `b2c_1_sign_in` .

```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| Paraméter | Kötelező | Leírás |
| --------- | -------- | ----------- |
|Bérlő| Yes | A Azure AD B2C bérlő neve|
|politika| Yes| A futtatandó felhasználói folyamat. Adja meg a Azure AD B2C bérlőben létrehozott felhasználói folyamat nevét. Például: `b2c_1_sign_in` , `b2c_1_sign_up` , vagy `b2c_1_edit_profile` . |
| client_id | Yes | Az alkalmazáshoz hozzárendelt [Azure Portal](https://portal.azure.com/) alkalmazás azonosítója. |
| response_type | Yes | Tartalmaznia kell `id_token` az OpenID Connect bejelentkezést. Belefoglalhatja a válasz típusát is `token` . Ha használja `token` , az alkalmazás azonnal kap egy hozzáférési jogkivonatot az engedélyezési végponttól anélkül, hogy egy második kérést kellene benyújtani az engedélyezési végpontnak.  Ha a `token` válasz típusát használja, a `scope` paraméternek tartalmaznia kell egy hatókört, amely megadja, hogy melyik erőforrást kell kibocsátani a jogkivonat számára. |
| redirect_uri | No | Az alkalmazás átirányítási URI-ja, ahol az alkalmazás elküldhet és fogadhat hitelesítési válaszokat. Pontosan meg kell egyeznie a portálon regisztrált átirányítási URI-k egyikével, azzal a különbséggel, hogy az URL-kódolásnak kell lennie. |
| response_mode | No | Meghatározza az eredményül kapott jogkivonat az alkalmazásba való visszaküldéséhez használandó módszert.  Implicit folyamatok esetén használja a következőt: `fragment` . |
| scope | Yes | A hatókörök szóközzel tagolt listája. Egyetlen hatóköri érték azt jelzi, hogy az Azure AD-t mind a kért engedélyek jelentik. A `openid` hatókör a felhasználónak való bejelentkezéshez és az azonosító tokenek formájában kapott információk beszerzéséhez szükséges engedélyt jelöli. A `offline_access` hatókör a Web Apps esetében nem kötelező. Azt jelzi, hogy az alkalmazásnak frissítési jogkivonatra van szüksége az erőforrásokhoz való hosszú élettartamú hozzáféréshez. |
| állapot | No | A kérelemben szereplő, a jogkivonat-válaszban is visszaadott érték. A használni kívánt tartalom karakterlánca lehet. A rendszer általában véletlenszerűen generált, egyedi értéket használ, hogy megakadályozza a helyek közötti kérelmek hamisítás elleni támadásait. Az állapot az alkalmazásban a felhasználó állapotára vonatkozó információk kódolására is használatos, mielőtt a hitelesítési kérelem bekövetkezett volna, például a laphoz. |
| egyszeri | Yes | A kérelemben szereplő, az eredményül kapott azonosító jogkivonatban található (az alkalmazás által generált) érték. Az alkalmazás ezután ellenőrizheti ezt az értéket a jogkivonat-Visszajátszási támadások enyhítése érdekében. Az érték általában egy véletlenszerű, egyedi karakterlánc, amely a kérelem forrásának azonosítására szolgál. |
| gyors | No | A kötelező felhasználói beavatkozás típusa. Jelenleg az egyetlen érvényes érték a `login` . Ez a paraméter arra kényszeríti a felhasználót, hogy adja meg a kéréshez tartozó hitelesítő adatait. Az egyszeri bejelentkezés nem lép érvénybe. |

Ekkor a rendszer megkéri a felhasználót, hogy fejezze be a szabályzat munkafolyamatát. Előfordulhat, hogy a felhasználónak meg kell adnia felhasználónevét és jelszavát, be kell jelentkeznie egy közösségi identitással, regisztrálnia kell a címtárban, vagy bármely más lépéssel. A felhasználói műveletek attól függnek, hogy a felhasználói folyamat hogyan van definiálva.

Miután a felhasználó befejezte a felhasználói folyamatot, az Azure AD egy választ küld az alkalmazásnak a használt értékre `redirect_uri` . A paraméterben megadott metódust használja `response_mode` . A válasz pontosan ugyanaz, mint a felhasználói műveletek egyes forgatókönyvei esetében, a végrehajtott felhasználói folyamattól függetlenül.

### <a name="successful-response"></a>Sikeres válasz
Sikeres válasz, amely `response_mode=fragment` `response_type=id_token+token` az alábbihoz hasonló, és az olvashatóság érdekében sortöréseket használ:

```http
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
```

| Paraméter | Leírás |
| --------- | ----------- |
| access_token | Az alkalmazás által kért hozzáférési jogkivonat. |
| token_type | A jogkivonat típusának értéke. Az Azure AD által támogatott egyetlen típus a tulajdonos. |
| expires_in | A hozzáférési jogkivonat érvényességének időtartama (másodpercben). |
| scope | Azok a hatókörök, amelyekhez a jogkivonat érvényes. Hatóköröket is használhat a tokenek későbbi használatra való gyorsítótárazásához. |
| id_token | Az alkalmazás által kért azonosító jogkivonat. Az azonosító token használatával ellenőrizheti a felhasználó identitását, és megkezdheti a munkamenetet a felhasználóval. Az azonosító jogkivonatokkal és azok tartalmával kapcsolatos további információkért tekintse meg az [Azure ad B2C jogkivonat-referenciát](tokens-overview.md). |
| állapot | Ha egy `state` paraméter szerepel a kérelemben, akkor a válaszban ugyanazt az értéket kell megjelennie. Az alkalmazásnak ellenőriznie kell, hogy a `state` kérelemben és a válaszban szereplő értékek azonosak-e. |

### <a name="error-response"></a>Hiba válasza
A rendszer a hibaüzeneteket is elküldheti az átirányítási URI-nak, hogy az alkalmazás megfelelően tudja kezelni őket:

```http
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paraméter | Leírás |
| --------- | ----------- |
| error | A felmerülő hibák típusának besorolására szolgáló kód. |
| error_description | Egy adott hibaüzenet, amely segítséget nyújt a hitelesítési hiba kiváltó okának azonosításában. |
| állapot | Ha egy `state` paraméter szerepel a kérelemben, akkor a válaszban ugyanazt az értéket kell megjelennie. Az alkalmazásnak ellenőriznie kell, hogy a `state` kérelemben és a válaszban szereplő értékek azonosak-e.|

## <a name="validate-the-id-token"></a>AZONOSÍTÓ jogkivonat ellenőrzése

Az azonosító jogkivonat fogadása nem elegendő a felhasználó hitelesítéséhez. Érvényesítse az azonosító jogkivonat aláírását, és ellenőrizze a jogcímek jogcímeit az alkalmazás követelményei szerint. Azure AD B2C [JSON webes tokeneket (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) és nyilvános kulcsú titkosítást használ a tokenek aláírásához, és ellenőrizze, hogy érvényesek-e.

Számos nyílt forráskódú kódtár elérhető a használni kívánt nyelvtől függően a JWTs érvényesítéséhez. A saját érvényesítési logikájának megvalósítása helyett érdemes lehet kideríteni a rendelkezésre álló nyílt forráskódú kódtárakat. A cikkben található információk segítségével megismerheti, hogyan használhatja ezeket a kódtárakat.

Azure AD B2C OpenID Connect metaadat-végponttal rendelkezik. Egy alkalmazás a végpont használatával adatokat tud beolvasni a Azure AD B2Cról. Ezek az adatok a végpontokat, a jogkivonat tartalmát és a jogkivonat-aláíró kulcsokat tartalmazzák. A Azure AD B2C-bérlő minden egyes felhasználói folyamatához JSON-metaadatokat tartalmazó dokumentum található. A fabrikamb2c.onmicrosoft.com-bérlő b2c_1_sign_in felhasználói folyamatához tartozó metaadat-dokumentum például a következő helyen található:

```http
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

A konfigurációs dokumentum egyik tulajdonsága a `jwks_uri` . Az azonos felhasználói folyamat értéke a következő lesz:

```http
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

Ha meg szeretné határozni, hogy melyik felhasználói folyamatot használták egy azonosító jogkivonat aláírására (és honnan szeretné beolvasni a metaadatokat), két lehetőség közül választhat. Először is a felhasználói folyamat neve szerepel a `acr` jogcímben `id_token` . További információ a jogcímek azonosító jogkivonat alapján történő elemzéséről: [Azure ad B2C jogkivonat-hivatkozás](tokens-overview.md). A másik lehetőség, hogy a kérés kiadásakor a paraméter értékében kódolja a felhasználói folyamatot `state` . Ezután dekódolja a `state` paramétert annak meghatározásához, hogy melyik felhasználói folyamatot használta. Bármelyik metódus érvényes.

Miután megszerezte a metaadat-dokumentumot az OpenID Connect metaadat-végpontból, használhatja az RSA-256 nyilvános kulcsokat (ebben a végpontban) az azonosító jogkivonat aláírásának ellenőrzéséhez. Ebben a végpontban több kulcs is szerepelhet, amelyek mindegyike egy adott időpontban szerepel `kid` . A fejléc `id_token` is tartalmaz egy `kid` jogcímet. Azt jelzi, hogy a kulcsok közül melyeket használták az azonosító token aláírására. További információért, beleértve a [jogkivonatok érvényesítésével](tokens-overview.md)kapcsolatos ismereteket, tekintse meg a [Azure ad B2C jogkivonat-referenciát](tokens-overview.md).
<!--TODO: Improve the information on this-->

Az azonosító jogkivonat aláírásának ellenőrzése után számos jogcím ellenőrzésre szorul. Például:

* Érvényesítse a `nonce` jogcímet a jogkivonat-újrapróbálkozások elleni támadások megelőzése érdekében. A bejelentkezési kérelemben megadott értéknek kell lennie.
* Érvényesítse a `aud` jogcímet, és győződjön meg arról, hogy az azonosító jogkivonat ki lett állítva az alkalmazáshoz. Az értéknek az alkalmazás alkalmazásspecifikus AZONOSÍTÓjának kell lennie.
* Ellenőrizze a `iat` és a `exp` jogcímeket, és győződjön meg arról, hogy az azonosító jogkivonat nem járt le.

Az [OpenID Connect Core specifikáció](https://openid.net/specs/openid-connect-core-1_0.html)részletesen ismerteti a végrehajtandó több érvényesítést. A forgatókönyvtől függően további jogcímeket is érdemes lehet érvényesíteni. Egyes gyakori érvényesítések a következők:

* Győződjön meg arról, hogy a felhasználó vagy szervezet regisztrált az alkalmazásra.
* Győződjön meg arról, hogy a felhasználó rendelkezik a megfelelő engedélyekkel és jogosultságokkal.
* A hitelesítés bizonyos erősségének biztosítása, például az Azure AD Multi-Factor Authentication használatával.

Az azonosító jogkivonatok jogcímeivel kapcsolatos további információkért tekintse meg az [Azure ad B2C jogkivonat-referenciát](tokens-overview.md).

Az azonosító jogkivonat ellenőrzése után megkezdheti a munkamenetet a felhasználóval. Az alkalmazásban az azonosító jogkivonatban található jogcímek segítségével szerezzen be információkat a felhasználóról. Ezek az információk megjeleníthetők, rekordok, engedélyezés és így tovább használhatók.

## <a name="get-access-tokens"></a>Hozzáférési jogkivonatok beolvasása
Ha az egyetlen dolog, amit a webalkalmazásoknak végre kell hajtania, akkor kihagyhatja a következő néhány szakaszt. A következő részekben található információk csak azokra a webalkalmazásokra érvényesek, amelyeknek hitelesített hívásokat kell végezniük a webes API-k számára, és amelyeket a Azure AD B2C véd.

Most, hogy aláírta a felhasználót egy egyoldalas alkalmazásba, hozzáférési jogkivonatokat kérhet le az Azure AD által védett webes API-k meghívásához. Akkor is használhatja ezt a módszert, ha a válasz típusának használatával már kapott jogkivonatot `token` , és nem irányítja át a felhasználót, hogy újra bejelentkezzen.

Egy tipikus webalkalmazás-folyamat esetében kérelmet kell benyújtani a `/token` végpontnak. A végpont azonban nem támogatja a CORS-kérelmeket, így az AJAX-hívások frissítési token beszerzése nem lehetséges. Ehelyett a rejtett HTML iframe-elemek implicit folyamatával új jogkivonatokat kérhet le más webes API-khoz. Íme egy példa, amely sortöréseket mutat be az olvashatóság érdekében:

```http
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
&response_mode=fragment
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&prompt=none
```

| Paraméter | Kötelező? | Leírás |
| --- | --- | --- |
|Bérlő| Kötelező | A Azure AD B2C bérlő neve|
politika| Kötelező| A futtatandó felhasználói folyamat. Adja meg a Azure AD B2C bérlőben létrehozott felhasználói folyamat nevét. Például: `b2c_1_sign_in` , `b2c_1_sign_up` , vagy `b2c_1_edit_profile` . |
| client_id |Kötelező |Az alkalmazáshoz a [Azure Portal](https://portal.azure.com)hozzárendelt alkalmazás-azonosító. |
| response_type |Kötelező |Tartalmaznia kell `id_token` az OpenID Connect bejelentkezést.  Előfordulhat, hogy a válasz típusát is tartalmazza `token` . Ha itt használja `token` , az alkalmazás azonnal kap egy hozzáférési jogkivonatot az engedélyezési végponttól anélkül, hogy egy második kérést kellene benyújtani az engedélyezési végpontnak. Ha a `token` válasz típusát használja, a `scope` paraméternek tartalmaznia kell egy hatókört, amely megadja, hogy melyik erőforrást kell kibocsátani a jogkivonat számára. |
| redirect_uri |Ajánlott |Az alkalmazás átirányítási URI-ja, ahol az alkalmazás elküldhet és fogadhat hitelesítési válaszokat. Pontosan egyeznie kell a portálon regisztrált átirányítási URI-k egyikével, azzal a különbséggel, hogy az URL-kódolású. |
| scope |Kötelező |A hatókörök szóközzel tagolt listája.  A jogkivonatok beolvasásához adja meg a kívánt erőforráshoz szükséges összes hatókört. |
| response_mode |Ajánlott |Meghatározza az eredményül kapott jogkivonat az alkalmazásba való visszaküldéséhez használt módszert. Implicit folyamat esetén használja a következőt: `fragment` . Két másik mód is megadható, `query` és `form_post` nem használható az implicit folyamat során. |
| állapot |Ajánlott |A jogkivonat-válaszban visszaadott kérelemben szereplő érték.  A használni kívánt tartalom karakterlánca lehet.  A rendszer általában véletlenszerűen generált, egyedi értéket használ, hogy megakadályozza a helyek közötti kérelmek hamisítás elleni támadásait.  Az állapot az alkalmazásban a felhasználó állapotára vonatkozó információk kódolására is használatos, mielőtt a hitelesítési kérelem bekövetkezett volna. Például a lap vagy a felhasználó megtekinthető. |
| egyszeri |Kötelező |Az alkalmazás által generált kérelemben szereplő érték, amely az eredményül kapott azonosító jogkivonat jogcímként szerepel.  Az alkalmazás ezután ellenőrizheti ezt az értéket a jogkivonat-Visszajátszási támadások enyhítése érdekében. Az érték általában egy véletlenszerű, egyedi karakterlánc, amely azonosítja a kérelem forrását. |
| gyors |Kötelező |Ha egy rejtett iframe-ben szeretné frissíteni és lekérni a jogkivonatokat, a használatával `prompt=none` Győződjön meg arról, hogy az IFRAME nem ragadt meg a bejelentkezési oldalon, és azonnal visszaadja. |
| login_hint |Kötelező |A rejtett iframe-ben lévő tokenek frissítéséhez és lekéréséhez adja meg a felhasználó felhasználónevét, hogy megkülönböztesse az adott időpontban a felhasználó által esetlegesen használt munkameneteket. A felhasználónevet kinyerheti egy korábbi bejelentkezésből a `preferred_username` jogcím használatával (a hatókör a `profile` jogcím fogadásához szükséges `preferred_username` ). |
| domain_hint |Kötelező |A következők egyike lehet: `consumers` vagy `organizations`.  A rejtett iframe-ben lévő tokenek frissítéséhez és `domain_hint` lekéréséhez adja meg a kérelemben szereplő értéket.  A `tid` jogcímek kinyerése egy korábbi bejelentkezés azonosító jogkivonatával annak meghatározásához, hogy melyik értéket kell használni (a `profile` hatókört kötelező megadni a jogcím fogadásához `tid` ). Ha a `tid` jogcím értéke `9188040d-6c67-4c5b-b112-36a304b66dad` , használja a értéket `domain_hint=consumers` .  Egyéb esetben használja a t `domain_hint=organizations` . |

`prompt=none`Ha a paramétert állítja be, akkor ez a kérelem vagy azonnal meghiúsul, és visszatér az alkalmazáshoz.  A rendszer sikeres választ kap az alkalmazásnak a jelzett átirányítási URI-n, a paraméterben megadott metódus használatával `response_mode` .

### <a name="successful-response"></a>Sikeres válasz
A következő példához hasonló módon választhatja ki a sikeres válaszokat `response_mode=fragment` :

```http
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
```

| Paraméter | Leírás |
| --- | --- |
| access_token |Az alkalmazás által kért jogkivonat. |
| token_type |A jogkivonat típusa mindig a tulajdonos lesz. |
| állapot |Ha egy `state` paraméter szerepel a kérelemben, akkor a válaszban ugyanazt az értéket kell megjelennie. Az alkalmazásnak ellenőriznie kell, hogy a `state` kérelemben és a válaszban szereplő értékek azonosak-e. |
| expires_in |A hozzáférési jogkivonat érvényességi ideje (másodpercben). |
| scope |Azok a hatókörök, amelyekre érvényes a hozzáférési jogkivonat. |

### <a name="error-response"></a>Hiba válasza
A rendszer a hibaüzeneteket is elküldheti az átirányítási URI-nak, hogy az alkalmazás megfelelően tudja kezelni őket.  A esetében a `prompt=none` várt hiba a következő példához hasonlít:

```http
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Paraméter | Leírás |
| --- | --- |
| error |Hibakód-karakterlánc, amely a felmerülő hibák típusának besorolására használható. A karakterláncot is használhatja a hibákra való reagálásra. |
| error_description |Egy adott hibaüzenet, amely segítséget nyújt a hitelesítési hiba kiváltó okának azonosításában. |

Ha ezt a hibát az IFRAME-kérelemben kapja, a felhasználónak interaktív módon be kell jelentkeznie egy új jogkivonat lekéréséhez.

## <a name="refresh-tokens"></a>Tokenek frissítése
Az azonosító tokenek és a hozzáférési tokenek rövid idő után lejárnak. Az alkalmazásnak fel kell készülnie, hogy rendszeresen frissítse ezeket a jogkivonatokat.  A jogkivonat bármelyik típusának frissítéséhez végezze el ugyanazt a rejtett iframe-kérelmet, amelyet egy korábbi példában használunk az `prompt=none` Azure ad-lépések vezérlésére szolgáló paraméter használatával.  Ha új értéket szeretne kapni `id_token` , ügyeljen arra, hogy a `response_type=id_token` és `scope=openid` a `nonce` paramétert használja.

## <a name="send-a-sign-out-request"></a>Kijelentkezési kérelem küldése
Ha alá szeretné írni a felhasználót az alkalmazásból, a kijelentkezéshez irányítsa át a felhasználót az Azure AD-ba. Ha nem irányítja át a felhasználót, előfordulhat, hogy újra kell hitelesítenie az alkalmazást anélkül, hogy újra be kellene írnia a hitelesítő adatait, mert az Azure AD-vel érvényes egyszeri bejelentkezéses munkamenet van.

Egyszerűen átirányíthatja a felhasználót az `end_session_endpoint` [azonosító-jogkivonat ellenőrzése](#validate-the-id-token)című témakörben ismertetett OpenID Connect metaadat-dokumentumban felsoroltra. Például:

```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Paraméter | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Bérlő | Yes | A Azure AD B2C bérlő neve |
| politika | Yes | Az alkalmazásból a felhasználó aláírásához használni kívánt felhasználói folyamat. |
| post_logout_redirect_uri | No | Az URL-cím, amelyet a felhasználónak át kell irányítani a sikeres kijelentkezés után. Ha nem tartalmazza, a Azure AD B2C általános üzenetet jelenít meg a felhasználó számára. |
| állapot | No | Ha egy `state` paraméter szerepel a kérelemben, akkor a válaszban ugyanazt az értéket kell megjelennie. Az alkalmazásnak ellenőriznie kell, hogy a `state` kérelemben és a válaszban szereplő értékek azonosak-e. |


> [!NOTE]
> A felhasználó átirányítása az egyes `end_session_endpoint` felhasználók egyszeri bejelentkezési állapotának törlésére Azure ad B2C használatával. Azonban nem írja alá a felhasználót a felhasználó közösségi identitás-szolgáltatói munkamenetében. Ha a felhasználó ugyanazt az identitás-szolgáltatót választja egy későbbi bejelentkezéskor, a rendszer újra hitelesíti a felhasználót a hitelesítő adatok megadása nélkül. Ha a felhasználó ki szeretne jelentkezni a Azure AD B2C alkalmazásból, nem feltétlenül jelenti azt, hogy teljesen ki szeretné jelentkezni a Facebook-fiókjából, például:. Helyi fiókok esetében azonban a felhasználó munkamenete megfelelően fog megjelenni.
>

## <a name="next-steps"></a>Következő lépések

### <a name="code-sample-azure-ad-b2c-with-microsoft-authentication-library-for-javascript"></a>Mintakód: Azure AD B2C a JavaScripthez készült Microsoft Authentication Library-val

[Egyoldalas alkalmazás, amely a msal.js Azure ad B2C][github-msal-js-example] (GitHub) készült.

A GitHubon ez a példa arra szolgál, hogy segítséget nyújtson Azure AD B2C a [msal.js][github-msal-js] beépített és előugró stílusú hitelesítést használó egyszerű webalkalmazásban.

<!-- Links - EXTERNAL -->
[github-msal-js-example]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
[github-msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
