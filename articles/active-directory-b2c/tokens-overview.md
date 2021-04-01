---
title: Tokenek áttekintése – Azure Active Directory B2C
description: Ismerkedjen meg a Azure Active Directory B2C használt jogkivonatokkal.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b4e268d35a2e31db0ce92ff61e66fd23bce68e38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97516358"
---
# <a name="overview-of-tokens-in-azure-active-directory-b2c"></a>A Azure Active Directory B2C jogkivonatok áttekintése

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) számos különböző típusú biztonsági jogkivonatot bocsát ki, mivel az egyes [hitelesítési](application-types.md)folyamatokat dolgozza fel. Ez a dokumentum az egyes típusú tokenek formátumát, biztonsági jellemzőit és tartalmát ismerteti.

## <a name="token-types"></a>Token-típusok

A Azure AD B2C támogatja a [OAuth 2,0 és az OpenID Connect protokollokat](protocols-overview.md), ami a hitelesítéshez és az erőforrásokhoz való biztonságos hozzáféréshez szükséges jogkivonatok használatát teszi lehetővé. Az Azure AD B2Cban használt összes jogkivonat [JSON webes token (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) , amely a tulajdonossal és a jogkivonat tárgyával kapcsolatos információkat tartalmaz.

A következő jogkivonatok használatosak a Azure AD B2Csal folytatott kommunikációban:

- *Azonosító jogkivonat* – olyan JWT, amely az alkalmazásban lévő felhasználók azonosítására használható jogcímeket tartalmaz. Ezt a jogkivonatot a rendszer biztonságos módon továbbítja a HTTP-kérésekben az ugyanazon alkalmazás vagy szolgáltatás két összetevője közötti kommunikációhoz. Az azonosító jogkivonatban lévő jogcímeket használhatja, ahogy az illik. Általában a fiókadatok megjelenítésére, illetve a hozzáférés-vezérlési döntések meghozatalára szolgálnak az alkalmazásokban. Az azonosító tokenek alá vannak írva, de nincsenek titkosítva. Ha az alkalmazás vagy az API azonosító jogkivonatot kap, a jogkivonat eredetiségének igazolásához ellenőriznie kell az aláírást. Az alkalmazásnak vagy API-nak a jogkivonatban található néhány jogcímet is ellenőriznie kell, hogy az érvényes legyen. A forgatókönyv követelményeitől függően az alkalmazás által érvényesített jogcímek eltérőek lehetnek, de az alkalmazásnak minden esetben el kell végeznie néhány gyakori jogcím-érvényesítést.
- *Hozzáférési jogkivonat* – olyan JWT, amely az API-khoz megadott engedélyek azonosítására használható jogcímeket tartalmaz. A hozzáférési tokenek alá vannak írva, de nincsenek titkosítva. A hozzáférési jogkivonatok az API-k és az erőforrás-kiszolgálók hozzáférésének biztosítására szolgálnak.  Ha az API hozzáférési jogkivonatot kap, akkor ellenőriznie kell az aláírást annak bizonyítására, hogy a jogkivonat hiteles. Az API-nak a jogkivonat néhány jogcímet is ellenőriznie kell, hogy az érvényes legyen. A forgatókönyv követelményeitől függően az alkalmazás által érvényesített jogcímek eltérőek lehetnek, de az alkalmazásnak minden esetben el kell végeznie néhány gyakori jogcím-érvényesítést.
- A *refresh token* -refresh tokens használatával új azonosító jogkivonatok és hozzáférési tokenek szerezhetők be egy OAuth 2,0-flow-ban. Lehetővé teszik az alkalmazás számára, hogy a felhasználók nevében hosszú távú hozzáféréssel lássa el az erőforrásokat, anélkül, hogy ezekkel a felhasználókkal való interakcióra lenne szükség. A frissítési tokenek átlátszatlanok az alkalmazásban. Ezeket a Azure AD B2C bocsátja ki, és csak Azure AD B2C által vizsgálható és értelmezhető. Hosszú életűek, de az alkalmazás nem írható azzal a várakozással, hogy egy frissítési jogkivonat egy adott időszakra érvényes lesz. A frissítési tokenek számos okból érvényteleníthető. Az alkalmazás egyetlen módja annak, hogy a frissítési jogkivonat érvényes legyen, ha egy jogkivonat-kérést küld a Azure AD B2C. Új jogkivonat frissítési jogkivonatának beváltásakor a jogkivonat-válaszban egy új frissítési token jelenik meg. Mentse az új frissítési tokent. Lecseréli a kérelemben korábban használt frissítési jogkivonatot. Ez a művelet segít garantálni, hogy a frissítési tokenek mindaddig érvényesek maradjanak, ameddig csak lehetséges. Vegye figyelembe, hogy az engedélyezési kódot használó egyoldalas alkalmazások esetében a PKCE mindig 24 órás frissítési jogkivonat élettartama szükséges. [További információ a frissítési tokenek biztonsági vonatkozásairól a böngészőben](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser).

## <a name="endpoints"></a>Végpontok

A [regisztrált alkalmazások](tutorial-register-applications.md) jogkivonatokat fogadnak és kommunikálnak a Azure ad B2C a következő végpontoknak küldött kérések küldésével:

- `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize`
- `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/token`

Az alkalmazástól a Azure AD B2Ctól kapott biztonsági jogkivonatok származhatnak a `/authorize` vagy `/token` végpontokból. Ha azonosító tokeneket szereznek be a `/authorize` végpontból, az [implicit folyamat](implicit-flow-single-page-application.md)használatával történik, amelyet gyakran használnak a JavaScript-alapú webalkalmazásokhoz való bejelentkezéshez. Ha azonosító tokeneket szereznek be a `/token` végpontból, az [engedélyezési kód folyamatával](openid-connect.md#get-a-token)történik, amely megtartja a tokent a böngészőből.

## <a name="claims"></a>Jogcímek

Azure AD B2C használatakor részletesen szabályozhatja a tokenek tartalmát. A [felhasználói folyamatokat](user-flow-overview.md) és az [egyéni házirendeket](custom-policy-overview.md) úgy is konfigurálhatja, hogy bizonyos felhasználói adatkészleteket küldjön az alkalmazáshoz szükséges jogcímekbe. Ezek a jogcímek olyan szabványos tulajdonságokat is tartalmazhatnak, mint a **DisplayName** és az **emailAddress**. Az alkalmazások ezeket a jogcímeket használhatják a felhasználók és a kérelmek biztonságos hitelesítéséhez.

Az azonosító jogkivonatokban lévő jogcímeket nem adja vissza a rendszer adott sorrendben. Az új jogcímek bármikor bevezethetők azonosító jogkivonatokban. Az alkalmazás nem szakítható meg új jogcímek bevezetésekor. A jogcímek [egyéni felhasználói attribútumokat](user-flow-custom-attributes.md) is tartalmazhatnak.

A következő táblázat felsorolja azokat a jogcímeket, amelyeket az azonosító jogkivonatok és a Azure AD B2C által kiállított hozzáférési jogkivonatok várhatnak.

| Name | Jogcím | Példaérték | Leírás |
| ---- | ----- | ------------- | ----------- |
| Célközönség | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Azonosítja a jogkivonat kívánt címzettjét. Azure AD B2C esetében a célközönség az alkalmazás azonosítója. Az alkalmazásnak érvényesíteni kell ezt az értéket, és el kell utasítania a tokent, ha az nem egyezik. A célközönség szinonimája az erőforrásnak. |
| Kiállító | `iss` |`https://<tenant-name>.b2clogin.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Azonosítja a tokent létrehozó és visszaküldő biztonságijogkivonat-szolgáltatást (STS). Emellett azt a könyvtárat is azonosítja, amelyben a felhasználó hitelesítése megtörtént. Az alkalmazásnak ellenőriznie kell a kiállítói jogcímet, hogy ellenőrizze, a jogkivonat a megfelelő végpontból származik-e. |
| Kiadás időpontja: | `iat` | `1438535543` | Az az időpont, amikor a jogkivonatot kiállították, és ez a korszakban szerepel. |
| Lejárati idő | `exp` | `1438539443` | Az az idő, amikor a jogkivonat érvénytelenné válik, és ez a korszak időpontjában jelenik meg. Az alkalmazásnak ezt a jogcímet kell használnia a jogkivonat élettartamának érvényességének ellenőrzéséhez. |
| Nem előtte | `nbf` | `1438535543` | Az az idő, amikor a jogkivonat érvényes lesz, és az időpontokban jelenik meg. Ez az idő általában megegyezik a jogkivonat kiadásának időpontjával. Az alkalmazásnak ezt a jogcímet kell használnia a jogkivonat élettartamának érvényességének ellenőrzéséhez. |
| Verzió | `ver` | `1.0` | Az azonosító jogkivonatának verziója Azure AD B2C által definiált módon. |
| Kód kivonata | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Egy azonosító jogkivonatban szereplő kód kivonat csak akkor, ha a tokent egy OAuth 2,0-es engedélyezési kóddal együtt adják ki. A kód kivonata egy engedélyezési kód hitelességének ellenőrzéséhez használható. Az érvényesítés végrehajtásával kapcsolatos további információkért tekintse meg az [OpenID Connect specifikációját](https://openid.net/specs/openid-connect-core-1_0.html).  |
| Hozzáférési jogkivonat kivonata | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Egy hozzáférési jogkivonat kivonata csak akkor szerepel egy azonosító jogkivonatban, ha a tokent egy OAuth 2,0 hozzáférési jogkivonattal együtt állították ki. A hozzáférési jogkivonat kivonata használható a hozzáférési token hitelességének ellenőrzéséhez. Az érvényesítés végrehajtásával kapcsolatos további információkért lásd az [OpenID Connect specifikációját](https://openid.net/specs/openid-connect-core-1_0.html) .  |
| Egyszeri | `nonce` | `12345` | Egy alkalom a jogkivonat-újrajátszás elleni támadások enyhítésére szolgáló stratégia. Az alkalmazás a lekérdezési paraméter használatával megadhat egy egyszeres engedélyt egy engedélyezési kérelemben `nonce` . A kérelemben megadott értéket a rendszer nem módosítja `nonce` csak azonosító jogkivonat jogcímen. Ez a jogcím lehetővé teszi az alkalmazás számára, hogy ellenőrizze az értéket a kérelemben megadott értékkel. Az alkalmazásnak el kell végeznie ezt az ellenőrzést az azonosító jogkivonat érvényesítési folyamata során. |
| Tárgy | `sub` | `884408e1-2918-4cz0-b12d-3aa027d7563b` | Az a rendszerbiztonsági tag, amelyről a jogkivonat adatokat érvényesít, például egy alkalmazás felhasználóját. Ez az érték nem módosítható, és nem rendelhető hozzá újra, és nem használható újra. Az engedélyezési ellenőrzések biztonságos elvégzésére használható, például ha a jogkivonat egy erőforrás elérésére szolgál. Alapértelmezés szerint a tulajdonos jogcímet a rendszer a címtárban lévő felhasználó objektumazonosító alapján tölti fel. |
| Hitelesítési környezet osztályának referenciája | `acr` | Nem alkalmazható | Csak régebbi házirendekkel használható. |
| Megbízhatósági keretrendszer szabályzata | `tfp` | `b2c_1_signupsignin1` | Az azonosító jogkivonat beszerzéséhez használt szabályzat neve. |
| Hitelesítési idő | `auth_time` | `1438535543` | Az az idő, amikor a felhasználó legutóbb megadta a hitelesítő adatokat. A hitelesítés nem jelent különbséget a friss bejelentkezés, az egyszeri bejelentkezési (SSO) munkamenet vagy egy másik bejelentkezési típus között. Az az `auth_time` utolsó alkalommal, amikor az alkalmazás (vagy felhasználó) hitelesítési kísérletet kezdeményezett Azure ad B2C. A hitelesítéshez használt metódus nincs megkülönböztetve. |
| Hatókör | `scp` | `Read`| Az erőforráshoz hozzáférési jogkivonat számára megadott engedélyek. Több megadott engedély is szóközzel elválasztva. |
| Felhatalmazott fél | `azp` | `975251ed-e4f5-4efd-abcb-5f1a8f566ab7` | A kérelmet kezdeményező ügyfélalkalmazás **alkalmazás-azonosítója** . |

## <a name="configuration"></a>Konfiguráció

A következő tulajdonságok a Azure AD B2C által kibocsátott [biztonsági jogkivonatok élettartamának kezelésére](configure-tokens.md) szolgálnak:

- **Hozzáférési & azonosító jogkivonat élettartama (perc)** – a védett erőforráshoz való hozzáféréshez használt OAuth 2,0 tulajdonosi jogkivonat élettartama. Az alapértelmezett érték 60 perc. A minimális (inkluzív) érték 5 perc. A maximális érték 1440 perc.

- **Frissítési jogkivonat élettartama (nap)** – az a maximális időtartam, ameddig egy frissítési jogkivonat felhasználható új hozzáférési vagy azonosító jogkivonat beszerzésére. Az időszak az új frissítési token beszerzését is magában foglalja, ha az alkalmazása megkapta a `offline_access` hatókört. Az alapértelmezett érték 14 nap. A minimális (inkluzív) egy nap. A maximális érték 90 nap.

- A **token csúszó ablakának élettartama (nap)** – az adott időszak lejárta után a felhasználónak újra hitelesítenie kell magát, az alkalmazás által beszerzett legutóbbi frissítési jogkivonat érvényességi idejétől függetlenül. Csak akkor adható meg, ha a kapcsoló **korlátos** értékre van állítva. Nagyobbnak vagy egyenlőnek kell lennie a **frissítési jogkivonat élettartama (nap)** értékével. Ha a kapcsoló nem **kötöttre** van beállítva, nem adhat meg konkrét értéket. Az alapértelmezett érték 90 nap. A minimális (inkluzív) egy nap. A maximális érték 365 nap.

A következő használati esetek engedélyezettek a következő tulajdonságok használatával:

- Lehetővé teszi, hogy a felhasználó határozatlan ideig bejelentkezzen a Mobile Application szolgáltatásba, ha a felhasználó folyamatosan aktív az alkalmazásban. A **frissítési jogkivonat csúszó ablakának élettartama (nap)** beállítható **a bejelentkezési felhasználói folyamat során.**
- A megfelelő hozzáférési jogkivonat élettartamának beállításával teljesítheti az iparág biztonsági és megfelelőségi követelményeit.

Ezek a beállítások nem érhetők el a felhasználói folyamatok jelszavának alaphelyzetbe állításához.

## <a name="compatibility"></a>Kompatibilitás

A [jogkivonatok kompatibilitásának kezeléséhez](configure-tokens.md)a következő tulajdonságok használatosak:

- **Kiállítói (ISS) jogcím** – ez a tulajdonság azonosítja a jogkivonatot kiállító Azure ad B2C bérlőt. Az alapértelmezett érték `https://<domain>/{B2C tenant GUID}/v2.0/`. Az érték a `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/` Azure ad B2C bérlő és a jogkivonat-kérelemben használt felhasználói folyamat azonosítóit tartalmazza. Ha az alkalmazásnak vagy a könyvtárnak Azure AD B2Cnak kell lennie az [OpenID Connect Discovery 1,0 specifikációjának](https://openid.net/specs/openid-connect-discovery-1_0.html)megfelelőnek, ezt az értéket kell használnia.

- **Tulajdonos (Sub) jogcím** – ez a tulajdonság azonosítja azt az entitást, amelyhez a jogkivonat adatokat érvényesít. Az alapértelmezett érték a **ObjectId**, amely a `sub` jogkivonatban lévő jogcímet a felhasználó objektum-azonosítójával tölti fel. A **nem támogatott** érték csak a visszamenőleges kompatibilitás érdekében adható meg. Javasoljuk, hogy a **ObjectId** -re váltson, amint tudja.

- **Házirend-azonosítót jelölő jogcím** – ez a tulajdonság azonosítja azt a jogcím-típust, amelybe a jogkivonat-kérelemben használt házirend neve fel van töltve. Az alapértelmezett érték `tfp`. A értéke `acr` csak a visszamenőleges kompatibilitás érdekében van megadva.

## <a name="pass-through"></a>Továbbítás

A felhasználói utazás megkezdésekor Azure AD B2C hozzáférési jogkivonatot kap egy identitás-szolgáltatótól. Azure AD B2C a token használatával kéri le a felhasználó adatait. A felhasználói folyamat egy jogcímet engedélyez, hogy [átadja a jogkivonatot](idp-pass-through-user-flow.md) a Azure ad B2Cban regisztrált alkalmazásoknak. Az alkalmazásnak [ajánlott felhasználói folyamatot](user-flow-versions.md) kell használnia ahhoz, hogy kihasználhassa a jogkivonatot jogcímként.

Azure AD B2C jelenleg csak az OAuth 2,0 Identity Providers hozzáférési jogkivonatának továbbítását támogatja, beleértve a Facebookot és a Google-t is. Az összes többi Identity Provider esetében a rendszer üresen adja vissza a jogcímet.

## <a name="validation"></a>Érvényesítés

A jogkivonatok érvényesítéséhez az alkalmazásnak ellenőriznie kell a jogkivonat aláírását és jogcímeit is. Számos nyílt forráskódú kódtár elérhető a JWTs érvényesítéséhez, az Ön által választott nyelvtől függően. Javasoljuk, hogy a saját érvényesítési logikájának megvalósítása helyett ezeket a lehetőségeket vizsgálja meg.

### <a name="validate-signature"></a>Aláírás ellenőrzése

A JWT három szegmenst, egy *fejlécet*, egy *törzset* és egy *aláírást* tartalmaznak. Az aláírási szegmens használatával ellenőrizheti a jogkivonat hitelességét, hogy az alkalmazás megbízható legyen. Azure AD B2C tokenek az iparági szabványnak megfelelő aszimmetrikus titkosítási algoritmusok, például az RSA 256 használatával vannak aláírva.

A jogkivonat fejléce a jogkivonat aláírásához használt kulcs-és titkosítási módszerről tartalmaz információkat:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

A **ALG** jogcím értéke a jogkivonat aláírásához használt algoritmus. A **Kid** jogcím értéke a jogkivonat aláírásához használt nyilvános kulcs. Azure AD B2C bármikor aláírhat egy jogkivonatot egy nyilvános titkos kulcspár egyik készletének használatával. A Azure AD B2C rendszeresen elforgatja a kulcsok lehetséges készletét. Az alkalmazást úgy kell megírni, hogy ezeket a kulcsfontosságú módosításokat automatikusan kezeljék. Az Azure AD B2C által használt nyilvános kulcsok frissítéseinek ellenőrzéséhez szükséges ésszerű gyakoriság 24 óránként. A nem várt legfontosabb változások kezeléséhez az alkalmazást úgy kell megírni, hogy újra lekérje a nyilvános kulcsokat, ha nem várt értékű **gyerek** értéket kap.

Azure AD B2C OpenID Connect metaadat-végponttal rendelkezik. Ennek a végpontnak a használatával az alkalmazások a Azure AD B2Cról kérhetnek le adatokat. Ezek az adatok a végpontokat, a jogkivonat tartalmát és a jogkivonat-aláíró kulcsokat tartalmazzák. Az Azure AD B2C-bérlő minden házirendhez tartalmaz egy JSON-metaadatokat tartalmazó dokumentumot. A metaadat-dokumentum egy JSON-objektum, amely számos hasznos információt tartalmaz. A metaadatok **jwks_uri** tartalmaznak, amely a jogkivonatok aláírásához használt nyilvános kulcsok helyét adja meg. Ez a hely itt érhető el, de érdemes dinamikusan beolvasni a helyet a metaadatokat tartalmazó dokumentum és az elemzési **jwks_uri** használatával:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/b2c_1_signupsignin1/discovery/v2.0/keys
```
Az ezen az URL-címen található JSON-dokumentum tartalmazza az adott pillanatban használatban lévő összes nyilvános kulcsra vonatkozó információt. Az alkalmazás a JWT- `kid` fejlécben szereplő jogcím használatával kiválaszthatja az adott token aláírásához használt JSON-dokumentum nyilvános kulcsát. Ezután a megfelelő nyilvános kulccsal és a jelzett algoritmussal hajthatja végre az aláírások érvényesítését.

A `B2C_1_signupsignin1` bérlői házirend metaadat-dokumentuma a `contoso.onmicrosoft.com` következő helyen található:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/b2c_1_signupsignin1/v2.0/.well-known/openid-configuration
```

Ha meg szeretné határozni, hogy melyik házirendet használták a jogkivonat aláírására (és hová kell megadnia a metaadatokat), két lehetőség közül választhat. Először is a szabályzat neve szerepel a `tfp` tokenben (alapértelmezett) vagy `acr` jogcímben (konfiguráltként). A jogcímeket a JWT törzse alapján elemezheti Base-64 kódolással a törzsben, és deszerializálhatja az eredményül kapott JSON-karakterláncot. A vagy a jogcím a jogkivonat kiküldésére `tfp` `acr` használt szabályzat neve. A másik lehetőség az, hogy a szabályzatot a paraméter értékeként kódolja a `state` kérelem kiadása után, majd dekódolja annak meghatározásához, hogy melyik házirendet használta. Bármelyik metódus érvényes.

Az aláírás-ellenőrzés végrehajtásának leírása kívül esik a dokumentum hatókörén. Számos nyílt forráskódú kódtár használható a jogkivonat érvényesítéséhez.

### <a name="validate-claims"></a>Jogcímek ellenőrzése

Ha az alkalmazások vagy API azonosító jogkivonatot kap, akkor az azonosító jogkivonatban található jogcímek több ellenőrzést is elvégezhetnek. A következő jogcímeket kell ellenőrizni:

- **célközönség** – ellenőrzi, hogy az azonosító jogkivonatot az alkalmazásnak szánták-e.
- **nem** az idő és a **lejárat időpontja** – ellenőrzi, hogy az azonosító jogkivonat nem járt-e le.
- **kiállító** – ellenőrzi, hogy a tokent a Azure ad B2C adta-e ki az alkalmazásnak.
- **alkalom** – stratégia a jogkivonat-újrajátszás elleni támadás mérsékléséhez.

Az alkalmazás által végrehajtandó érvényesítések teljes listáját az [OpenID Connect specifikációjában](https://openid.net)találja.

## <a name="next-steps"></a>Következő lépések

További információ a [hozzáférési tokenek használatáról](access-tokens.md).

