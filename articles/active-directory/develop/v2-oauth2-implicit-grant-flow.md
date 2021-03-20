---
title: OAuth 2,0 implicit engedélyezési folyamat – a Microsoft Identity platform | Azure
description: Egyoldalas alkalmazások biztonságossá tétele a Microsoft Identity platform implicit folyamatával.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: f3598c6f072d09d7e427db66dcfbf8721b92a3a1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99226488"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Microsoft Identity platform és implicit engedélyezési folyamat

A Microsoft Identity platform támogatja a OAuth 2,0 implicit engedélyezési folyamatot a [OAuth 2,0 specifikációjában](https://tools.ietf.org/html/rfc6749#section-4.2)leírtak szerint. Az implicit támogatás meghatározó jellemzője, hogy a tokeneket (azonosító jogkivonatokat vagy hozzáférési jogkivonatokat) közvetlenül a/Authorize-végpontról adja vissza a/token végpont helyett. Ezt gyakran használják az [engedélyezési kód folyamatának](v2-oauth2-auth-code-flow.md)részeként, az úgynevezett "hibrid folyamat" – az azonosító token lekérése a/Authorize kérelemre, valamint egy engedélyezési kódot.

[!INCLUDE [suggest-msal-from-protocols](includes/suggest-msal-from-protocols.md)]

## <a name="prefer-the-auth-code-flow"></a>Az Auth Code folyamat előnyben részesített

A [harmadik féltől származó cookie-k böngészőkből való eltávolítására](reference-third-party-cookies-spas.md)szolgáló csomagok esetében az **implicit engedélyezési folyamat már nem megfelelő hitelesítési módszer**.  Az implicit folyamat [csendes bejelentkezési funkciói](#getting-access-tokens-silently-in-the-background) nem működnek harmadik féltől származó cookie-k nélkül, így az alkalmazások megszakadnak, amikor új jogkivonatot próbálnak beolvasni. Azt javasoljuk, hogy minden új alkalmazás használja az [engedélyezési kód folyamatát](v2-oauth2-auth-code-flow.md) , amely mostantól támogatja az egyoldalas alkalmazásokat az implicit folyamat helyett, és a [meglévő egyoldalas alkalmazások is megkezdik az áttelepítést az engedélyezési kód folyamatára](migrate-spa-implicit-to-auth-code.md) .

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>A OAuth2 implicit engedélyezésének megfelelő forgatókönyvei

Az implicit támogatás csak a bejelentkezési folyamat kezdeti, interaktív részénél megbízható, ahol a [harmadik féltől származó cookie-k](reference-third-party-cookies-spas.md) hiánya nem befolyásolhatja az alkalmazását. Ez a korlátozás azt jelenti, hogy kizárólag a hibrid folyamat részeként használja, ahol az alkalmazás egy kódot és egy jogkivonatot kér az engedélyezési végponttól. Ez biztosítja, hogy az alkalmazás megkapja a frissítési tokenhez beváltható kódot, így biztosítva, hogy az alkalmazás bejelentkezési munkamenete időben érvénybe lépjen.

## <a name="protocol-diagram"></a>Protokoll diagramja

Az alábbi ábrán látható, hogy a teljes implicit bejelentkezési folyamat hogyan néz ki, valamint az egyes lépéseket követő szakaszok részletesebb leírását.

![Az implicit bejelentkezési folyamatot bemutató diagram](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Bejelentkezési kérelem küldése

Ahhoz, hogy először aláírja a felhasználót az alkalmazásba, elküldheti az [OpenID Connect](v2-protocols-oidc.md) hitelesítési kérését, és lekérheti a `id_token` Microsoft Identity platformot.

> [!IMPORTANT]
> Egy azonosító jogkivonat és/vagy hozzáférési jogkivonat sikeres igényléséhez az alkalmazásnak a [Azure Portal-Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lapon való regisztrálásához engedélyezve kell lennie a megfelelő implicit engedélyezési folyamatnak, az **azonosító tokenek** és a **hozzáférési tokenek** kiválasztásával az **implicit engedélyezési és a hibrid folyamatok** szakaszban. Ha nincs engedélyezve, a rendszer `unsupported_response` hibaüzenetet küld: `The provided value for the input parameter 'response_type' is not allowed for this client. Expected value is 'code'`

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> Az implicit folyamat használatával történő bejelentkezés teszteléséhez kattintson a elemre <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank"> https://login.microsoftonline.com/common/oauth2/v2.0/authorize.. .</a> A bejelentkezést követően a böngészőt át kell irányítani `https://localhost/myapp/` egy-ra `id_token` a címsorba.
>

| Paraméter | Típus | Description |
| --- | --- | --- |
| `tenant` | kötelező |A `{tenant}` kérelem elérési útjának értéke használható annak szabályozására, hogy ki jelentkezhet be az alkalmazásba. Az engedélyezett értékek:,, `common` `organizations` `consumers` és bérlői azonosítók. További részletek: [protokoll alapjai](active-directory-v2-protocols.md#endpoints). |
| `client_id` | kötelező | Az alkalmazáshoz hozzárendelt [Azure Portal-Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) oldal alkalmazás-(ügyfél-) azonosítója. |
| `response_type` | kötelező |Tartalmaznia kell `id_token` az OpenID Connect bejelentkezést. Emellett a response_type is szerepelhetnek `token` . Az `token` itt elérhetővé teszi, hogy az alkalmazás azonnal kapjon hozzáférési jogkivonatot az engedélyező végponttól anélkül, hogy egy második kérést kellene létesítenie az engedélyezés végpontjának. Ha a response_type használja `token` , a `scope` paraméternek tartalmaznia kell egy hatókört, amely jelzi, hogy melyik erőforrást kell kibocsátania a jogkivonat számára (például: user. read on Microsoft Graph). Emellett az `code` `token` [engedélyezési](v2-oauth2-auth-code-flow.md)kód megadásához is tartalmazhatja az engedélyezési kódot. Ezt a id_token + kód választ néha hibrid folyamatnak is nevezik.  |
| `redirect_uri` | ajánlott |Az alkalmazás redirect_uri, ahol az alkalmazás elküldhet és fogadhat hitelesítési válaszokat. Pontosan meg kell egyeznie a portálon regisztrált redirect_urisével, kivéve, ha az URL-címet kódolni kell. |
| `scope` | kötelező |A [hatókörök](v2-permissions-and-consent.md)szóközzel tagolt listája. Az OpenID Connect (id_tokens) esetében tartalmaznia kell a hatókört `openid` , amely a hozzájárulási felhasználói felületen a "Bejelentkezés" engedélyre van lefordítva. Opcionálisan érdemes lehet a és a hatóköröket is használni a `email` `profile` további felhasználói információkhoz való hozzáféréshez. A kérelemben más hatókörök is szerepelhetnek, ha hozzáférési jogkivonatot kér a különböző erőforrásokhoz való hozzáféréshez. |
| `response_mode` | választható |Meghatározza azt a módszert, amelyet az eredményül kapott jogkivonat az alkalmazásba való visszaküldéséhez kell használni. Az alapértelmezett érték csak egy hozzáférési jogkivonat lekérdezése, de töredék, ha a kérelem tartalmaz egy id_token. |
| `state` | ajánlott |A kérelemben szereplő érték, amelyet a rendszer a jogkivonat-válaszban is visszaad. Bármely kívánt tartalom sztringje lehet. A véletlenszerűen generált egyedi érték általában a [helyek közötti kérelmek hamisításának megelőzésére](https://tools.ietf.org/html/rfc6749#section-10.12)szolgál. Az állapot az alkalmazásban a felhasználó állapotára vonatkozó információk kódolására is használatos, mielőtt a hitelesítési kérelem bekövetkezett volna, például az oldal vagy a megtekintés. |
| `nonce` | kötelező |Az alkalmazás által generált kérelemben szereplő érték, amelyet a rendszer az eredményül kapott id_token tartalmaz. Az alkalmazás ezután ellenőrizheti ezt az értéket a jogkivonat-Visszajátszási támadások enyhítése érdekében. Az érték általában egy véletlenszerű, egyedi karakterlánc, amely a kérelem forrásának azonosítására szolgál. Csak id_token kérelmezése esetén szükséges. |
| `prompt` | választható |Megadja a szükséges felhasználói beavatkozás típusát. Ebben az esetben az egyetlen érvényes érték a következők egyike: "bejelentkezhessen," None "," select_account "és" beleegyező ". `prompt=login` kényszeríti a felhasználót, hogy adja meg hitelesítő adatait a kérésen, és zárja be az egyszeri bejelentkezést. `prompt=none` Ez ellentétes – biztosítja, hogy a felhasználó semmilyen interaktív kérdés nélkül is megjelenik. Ha a kérést nem lehet csendes úton végrehajtani az egyszeri bejelentkezésen keresztül, a Microsoft Identity platform hibát ad vissza. `prompt=select_account` elküldi a felhasználót egy olyan fiók-választónak, ahol a munkamenetben megjegyezett összes fiók megjelenik. `prompt=consent` a a felhasználó bejelentkezése után elindítja a OAuth jóváhagyása párbeszédpanelt, amely arra kéri a felhasználót, hogy adjon engedélyt az alkalmazásnak. |
| `login_hint`  |választható |A felhasználó számára a bejelentkezési oldal Felhasználónév/e-mail cím mezőjének előzetes kitöltésére is használható, ha a felhasználónevet az idő előtt ismeri. Az alkalmazások gyakran ezt a paramétert fogják használni az újrahitelesítés során, miután már kibontotta a felhasználónevet egy korábbi bejelentkezésből a `preferred_username` jogcím használatával.|
| `domain_hint` | választható |Ha belefoglalja ezt a funkciót, a rendszer kihagyja az e-mailes felderítési folyamatot, amelyet a felhasználó a bejelentkezési oldalon áthalad, ami valamivel egyszerűbb felhasználói élményt nyújt. Ezt a paramétert általában olyan üzletági alkalmazások esetében használják, amelyek egyetlen bérlőn működnek, ahol egy adott bérlőn belüli tartománynevet biztosítanak, továbbítva a felhasználót az adott bérlő összevonási szolgáltatójának.  Vegye figyelembe, hogy ez a tipp megakadályozza, hogy a vendégek bejelentkezzenek ebbe az alkalmazásba, és korlátozzák a Felhőbeli hitelesítő adatok, például a rendszer használatát.  |

Ekkor a rendszer megkéri a felhasználót, hogy adja meg a hitelesítő adatait, és fejezze be a hitelesítést. A Microsoft Identity platform azt is biztosítja, hogy a felhasználó beleegyezett a `scope` lekérdezési paraméterben megadott engedélyekkel. Ha a felhasználó beleegyezett a fenti engedélyek **egyikére sem** , a felhasználó beleegyezést kér a szükséges engedélyekkel. További információ: [engedélyek, beleegyezikés és több-bérlős alkalmazások](v2-permissions-and-consent.md).

Miután a felhasználó hitelesíti és beleegyezik, a Microsoft Identity platform az adott alkalmazásra adott választ ad vissza a `redirect_uri` paraméterben megadott metódus használatával `response_mode` .

#### <a name="successful-response"></a>Sikeres válasz

A sikeres válasz `response_mode=fragment` és `response_type=id_token+code` a következőképpen néz ki (az olvashatóság érdekében sortöréseket használ):

```HTTP
GET https://localhost/myapp/#
code=0.AgAAktYV-sfpYESnQynylW_UKZmH-C9y_G1A
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Paraméter | Leírás |
| --- | --- |
| `code` | Tartalmazza, ha `response_type` tartalmaz `code` . Ez egy engedélyezési kód, amely alkalmas az [engedélyezési kód folyamatában](v2-oauth2-auth-code-flow.md)való használatra.  |
| `access_token` |Tartalmazza, ha `response_type` tartalmaz `token` . Az alkalmazás által kért hozzáférési jogkivonat. A hozzáférési tokent nem lehet dekódolni vagy más módon megvizsgálni, átlátszatlan karakterláncként kell kezelni. |
| `token_type` |Tartalmazza, ha `response_type` tartalmaz `token` . Mindig a lesz `Bearer` . |
| `expires_in`|Tartalmazza, ha `response_type` tartalmaz `token` . Azt jelzi, hogy a jogkivonat hány másodpercig érvényes, gyorsítótárazási célokra. |
| `scope` |Tartalmazza, ha `response_type` tartalmaz `token` . Azokat a hatókör (eke) t jelöli, amelyekhez a access_token érvényes lesz. Előfordulhat, hogy nem tartalmazza az összes kért hatókört, ha nem voltak érvényesek a felhasználóra (csak az Azure AD-hatókörök esetében, amikor a rendszer személyes fiókot használ a bejelentkezéshez). |
| `id_token` | Aláírt JSON Web Token (JWT). Az alkalmazás képes dekódolni a token szegmenseit, hogy adatokat kérjen a bejelentkezett felhasználótól. Az alkalmazás gyorsítótárazhatja az értékeket, és megjelenítheti őket, de nem támaszkodhat rájuk az engedélyezési vagy biztonsági határokra. További információ a id_tokensról: [`id_token reference`](id-tokens.md) . <br> **Megjegyzés:** Csak akkor van megadva `openid` , ha a hatókört kérték és `response_type` tartalmazza `id_tokens` . |
| `state` |Ha a kérelemben szerepel egy State paraméter, akkor a válaszban ugyanazt az értéket kell megjelennie. Az alkalmazásnak ellenőriznie kell, hogy a kérelemben és a válaszban szereplő állapot értékek azonosak-e. |

#### <a name="error-response"></a>Hiba válasza

A rendszer a hibaüzeneteket is elküldheti, hogy `redirect_uri` az alkalmazás megfelelően tudja kezelni őket:

```HTTP
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Paraméter | Leírás |
| --- | --- |
| `error` |Hibakód-karakterlánc, amely a felmerülő hibák típusának besorolására használható, és felhasználható a hibákra való reagálásra. |
| `error_description` |Egy adott hibaüzenet, amely segítséget nyújt a fejlesztőknek a hitelesítési hiba kiváltó okának azonosításában. |

## <a name="getting-access-tokens-silently-in-the-background"></a>Hozzáférési tokenek csendes beolvasása a háttérben

> [!Important]
> Az implicit folyamat ezen része nem valószínű, hogy az alkalmazáshoz hasonló módon működjön, mivel a [harmadik féltől származó cookie-k alapértelmezés szerint történő eltávolítása](reference-third-party-cookies-spas.md)miatt nem használhatók a különböző böngészőkben.  Habár ez továbbra is olyan króm-alapú böngészőkben működik, amelyek nem az inkognitóban-ban vannak, a fejlesztőknek újra meg kell fontolniuk a folyamat ezen részének használatát. A harmadik féltől származó cookie-kat nem támogató böngészőkben hibaüzenet jelenik meg, amely jelzi, hogy nincs bejelentkezve felhasználó, mivel a böngésző a bejelentkezési oldal munkamenet-cookie-jait eltávolította. 

Most, hogy aláírta a felhasználót egy egyoldalas alkalmazásba, a Microsoft Identity platform által védett webes API-k meghívásához (például a [Microsoft Graph](https://developer.microsoft.com/graph)) csendes módon kaphat hozzáférési jogkivonatokat. Akkor is használhatja ezt a módszert, ha a response_type használatával már kapott jogkivonatot `token` , és nem kell átirányítani a felhasználót, hogy újra be kellene jelentkeznie.

A normál OpenID Connect/OAuth folyamat során ezt a Microsoft Identity platform-végpontra irányuló kéréssel teheti meg `/token` . A kérést rejtett iframe-ben is megteheti, hogy új jogkivonatokat kapjon más webes API-khoz:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&response_mode=fragment
&state=12345
&nonce=678910
&prompt=none
&login_hint=myuser@mycompany.com
```

Az URL-cím lekérdezési paramétereinek részletes ismertetését lásd: [a bejelentkezési kérelem küldése](#send-the-sign-in-request).

> [!TIP]
> Próbálja meg másolni & az alábbi kérelem beillesztése egy böngésző lapra! (Ne felejtse el lecserélni az `login_hint` értékeket a felhasználó helyes értékére)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint={your-username}`
>
> Vegye figyelembe, hogy ez még akkor is működni fog, ha a böngészőben a harmadik féltől származó cookie-támogatás nélkül is működik, mert közvetlenül a böngésző címsorába nyitja meg az IFRAME-en belül. 

A `prompt=none` paraméternek köszönhetően ez a kérés sikeres vagy sikertelen lesz, és visszatér az alkalmazáshoz. A rendszer elküldi a választ az alkalmazásnak a jelzett időpontban `redirect_uri` , a paraméterben megadott metódus használatával `response_mode` .

#### <a name="successful-response"></a>Sikeres válasz

Sikeres válasz a `response_mode=fragment` következőképpen:

```HTTP
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fdirectory.read
```

| Paraméter | Leírás |
| --- | --- |
| `access_token` |Tartalmazza, ha `response_type` tartalmaz `token` . Az alkalmazás által kért hozzáférési jogkivonat, ebben az esetben a Microsoft Graph. A hozzáférési tokent nem lehet dekódolni vagy más módon megvizsgálni, átlátszatlan karakterláncként kell kezelni. |
| `token_type` | Mindig a lesz `Bearer` . |
| `expires_in` | Azt jelzi, hogy a jogkivonat hány másodpercig érvényes, gyorsítótárazási célokra. |
| `scope` | Azokat a hatókör (eke) t jelöli, amelyekhez a access_token érvényes lesz. Előfordulhat, hogy nem tartalmazza az összes kért hatókört, ha nem voltak érvényesek a felhasználóra (csak az Azure AD-hatókörök esetében, amikor a rendszer személyes fiókot használ a bejelentkezéshez). |
| `id_token` | Aláírt JSON Web Token (JWT). Tartalmazza, ha `response_type` tartalmaz `id_token` . Az alkalmazás képes dekódolni a token szegmenseit, hogy adatokat kérjen a bejelentkezett felhasználótól. Az alkalmazás gyorsítótárazhatja az értékeket, és megjelenítheti őket, de nem támaszkodhat rájuk az engedélyezési vagy biztonsági határokra. További információ a id_tokensről: [ `id_token` hivatkozás](id-tokens.md). <br> **Megjegyzés:** Csak akkor van megadva, ha a `openid` hatókört kérték. |
| `state` |Ha a kérelemben szerepel egy State paraméter, akkor a válaszban ugyanazt az értéket kell megjelennie. Az alkalmazásnak ellenőriznie kell, hogy a kérelemben és a válaszban szereplő állapot értékek azonosak-e. |

#### <a name="error-response"></a>Hiba válasza

A rendszer a hibaüzeneteket is elküldheti, hogy `redirect_uri` az alkalmazás megfelelően tudja kezelni őket. A esetében a `prompt=none` várt hiba a következő lesz:

```HTTP
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Paraméter | Leírás |
| --- | --- |
| `error` |Hibakód-karakterlánc, amely a felmerülő hibák típusának besorolására használható, és felhasználható a hibákra való reagálásra. |
| `error_description` |Egy adott hibaüzenet, amely segítséget nyújt a fejlesztőknek a hitelesítési hiba kiváltó okának azonosításában. |

Ha ezt a hibát az IFRAME-kérelemben kapja, a felhasználónak interaktív módon be kell jelentkeznie egy új jogkivonat lekéréséhez. Ezt az esetet úgy is eldöntheti, hogy bármilyen módon ésszerű legyen az alkalmazáshoz.

## <a name="refreshing-tokens"></a>Tokenek frissítése

Az implicit támogatás nem biztosít frissítési jogkivonatokat. Az `id_token` s és `access_token` az s is rövid idő után lejár, így az alkalmazásnak fel kell készülnie, hogy rendszeresen frissítse ezeket a jogkivonatokat. A jogkivonat bármelyik típusának frissítéséhez ugyanezt a rejtett iframe-kérést is végrehajthatja a fentiekben a `prompt=none` paraméterrel az Identity platform viselkedésének szabályozásához. Ha újt szeretne kapni, ne `id_token` felejtse el használni `id_token` a és a `response_type` `scope=openid` , valamint a `nonce` paramétert.

A harmadik féltől származó cookie-kat nem támogató böngészőkben ez a hibaüzenet azt jelzi, hogy nincs bejelentkezve felhasználó. 

## <a name="send-a-sign-out-request"></a>Kijelentkezési kérelem küldése

Az OpenID Connect `end_session_endpoint` lehetővé teszi, hogy az alkalmazás küldjön egy kérést a Microsoft Identity platformra a felhasználó munkamenetének befejezéséhez és a Microsoft Identity platform által beállított cookie-k törléséhez. Ha egy felhasználót teljes mértékben szeretne aláírni egy webalkalmazásból, az alkalmazásnak saját munkamenetét kell végződnie a felhasználóval (általában a jogkivonat-gyorsítótár törlésével vagy a cookie-k eldobásával), majd át kell irányítani a böngészőt a következőre:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Paraméter | Típus | Description |
| --- | --- | --- |
| `tenant` |kötelező |A `{tenant}` kérelem elérési útjának értéke használható annak szabályozására, hogy ki jelentkezhet be az alkalmazásba. Az engedélyezett értékek:,, `common` `organizations` `consumers` és bérlői azonosítók. További részletek: [protokoll alapjai](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | ajánlott | A felhasználó által a kijelentkezés befejeződése után visszaadott URL-cím. Ennek az értéknek meg kell egyeznie az alkalmazáshoz regisztrált átirányítási URI-k egyikével. Ha nem szerepel, a felhasználó egy általános üzenetet fog megjeleníteni a Microsoft Identity platformon. |

## <a name="next-steps"></a>Következő lépések

* Ugorjon a [MSAL js-mintákra](sample-v2-code.md) az első lépések kódolásához.
* Tekintse át az [engedélyezési kód folyamatát](v2-oauth2-auth-code-flow.md) újabb, jobb alternatíva az implicit támogatáshoz. 
