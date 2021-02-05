---
title: Microsoft Identity platform – hozzáférési tokenek | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg az Azure AD v 1.0 és a Microsoft Identity platform (v 2.0) végpontok által kibocsátott hozzáférési jogkivonatokat.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: hirsin
ms.reviewer: mmacy, hirsin
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: e1dcd52660ff43a93c6a170912fea5a5847fe9d3
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575754"
---
# <a name="microsoft-identity-platform-access-tokens"></a>Microsoft Identity platform hozzáférési jogkivonatok

A hozzáférési jogkivonatok lehetővé teszik az ügyfelek számára a védett webes API-k biztonságos meghívását, és a webes API-k használják a hitelesítést és engedélyezést. A OAuth-specifikáció alapján a hozzáférési tokenek átlátszatlan karakterláncok, amelyek nem rendelkeznek beállított formátummal – egyes identitás-szolgáltatók (IDP-EK) GUID azonosítókat használnak, mások pedig titkosított blobokat használnak. A Microsoft Identity platform a tokent fogadó API konfigurációjától függően számos hozzáférési jogkivonat-formátumot használ. A fejlesztők által a Microsoft Identity platformon [regisztrált egyéni API](quickstart-configure-app-expose-web-apis.md) -k két különböző formátumban választhatnak a JSON webes tokenek (JWTs), a "v1" és a "v2" néven, valamint a Microsoft által fejlesztett API-k, például az Azure Microsoft Graph vagy API-jai további tulajdonosi jogkivonatokat is tartalmazhatnak. Ezek a saját formátumok lehetnek titkosított tokenek, JWTs vagy olyan speciális JWT tokenek, amelyek nem lesznek érvényesítve.

Az ügyfeleknek átlátszatlan karakterláncként kell kezelniük a hozzáférési jogkivonatokat, mivel a jogkivonat tartalma csak az erőforráshoz (az API-hoz) készült. *Csak* ellenőrzési és hibakeresési célokra a fejlesztők a JWTs-t egy olyan webhelyről tudja dekódolni, mint például a [JWT.MS](https://jwt.ms). Vegye figyelembe azonban, hogy a Microsoft API-hoz kapott tokenek nem mindig JWT, és nem lehet mindig dekódolni őket.

A hozzáférési jogkivonattal kapcsolatos részletekért az ügyfeleknek a hozzáférési jogkivonattal visszaadott token Response adatokat kell használniuk az ügyfélhez. Amikor az ügyfél hozzáférési jogkivonatot kér, a Microsoft Identity platform az alkalmazás fogyasztásának hozzáférési jogkivonatával kapcsolatos metaadatokat is ad vissza. Ez az információ tartalmazza a hozzáférési jogkivonat lejárati idejét, valamint azokat a hatóköröket, amelyekhez érvényes. Ezek az adatmennyiség lehetővé teszi, hogy az alkalmazás a hozzáférési tokenek elemzése nélkül végezzen intelligens gyorsítótárazást a hozzáférési jogkivonatok használatával.

A következő részekben megtudhatja, hogyan érvényesítheti az API-t, és hogyan használhatja a jogcímeket egy hozzáférési jogkivonaton belül.  

> [!NOTE]
> Az ezen az oldalon található összes dokumentáció csak a regisztrált API-khoz kiadott jogkivonatokra vonatkozik.  Ez nem vonatkozik a Microsoft tulajdonú API-k számára kiállított jogkivonatokra, és ezek a tokenek nem használhatók annak ellenőrzésére, hogy a Microsoft Identity platform milyen jogkivonatokat állít ki egy Ön által létrehozott API-hoz.  

## <a name="token-formats-and-ownership"></a>Jogkivonat-formátumok és-tulajdonos

### <a name="v10-and-v20"></a>1.0 és v 2.0 

A Microsoft Identity platformon két különböző verziójú hozzáférési token érhető el: 1.0 és v 2.0.  Ezek a verziók szabályozzák, hogy milyen jogcímek vannak a jogkivonatban, biztosítva, hogy a webes API-k szabályozhatják a tokenek megjelenését. A webes API-k közül ezek egyike alapértelmezettként van kijelölve az Azure AD-only alkalmazások és a 2.0-s verzió esetében a felhasználói fiókokat támogató alkalmazások esetében.  Ez az alkalmazás `accessTokenAcceptedVersion` [jegyzékfájljának](reference-app-manifest.md#manifest-reference)beállítását használó alkalmazások számára ellenőrizhető, és a v `null` `1` 1.0 jogkivonatokat eredményezi, és `2` a v 2.0-tokeneket eredményezi.

### <a name="what-app-is-a-token-for"></a>Mi az alkalmazás token "for"?

A hozzáférési jogkivonat iránti kérelemben két fél vesz részt: az ügyfél, aki kéri a jogkivonatot, valamint az API meghívásakor a jogkivonatot fogadó erőforrást (API-t). A `aud` jogkivonatban szereplő jogcím azt az erőforrást jelzi, amelyet a jogkivonat szán (a *célközönségének*). Az ügyfelek a jogkivonatot használják, de nem értik vagy nem kísérlik meg az elemzését. Az erőforrások elfogadják a jogkivonatot.  

A Microsoft Identity platform támogatja a jogkivonatok bármely verziójú végpontból való kiadását – ezek nem kapcsolódnak egymáshoz. Ezért az erőforrás `accessTokenAcceptedVersion` -beállítás `2` azt jelenti, hogy egy ügyfél, amely a v 1.0 végpontot hívja meg az API-hoz, egy v 2.0 hozzáférési tokent kap.  Az erőforrások mindig a jogkivonatok (a `aud` jogcímek) tulajdonosai, és az egyetlen olyan alkalmazás, amelyik megváltoztathatja a token részleteit. Ezért nem módosítja az *ügyfélhez* tartozó hozzáférési jogkivonat [választható jogcímeit](active-directory-optional-claims.md) , mert az `user.read` Microsoft Graph erőforrás tulajdonában lévő jogkivonatot kér.

### <a name="sample-tokens"></a>Mintául szolgáló tokenek

a 1.0-s és a 2.0-s verzióbeli tokenek hasonlóak, és számos azonos jogcímet tartalmaznak. Itt talál példát. Ezek a példák azonban nem [érvényesítik](#validating-tokens)a jogkivonatokat, mivel a rendszer a közzététel előtt elforgatta a kulcsokat, és a személyes adatok el lettek távolítva belőlük.

#### <a name="v10"></a>1.0-s verzió

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd
```

Tekintse meg ezt az 1.0-s tokent a [JWT.MS](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd)-ben.

#### <a name="v20"></a>2.0-s verzió

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt
```

Tekintse meg ezt a v 2.0-tokent a [JWT.MS](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt)-ben.

## <a name="claims-in-access-tokens"></a>Hozzáférési jogkivonatokban lévő jogcímek

A JWTs (JSON webes tokenek) három darabra oszlanak:

* **Fejléc** – információt nyújt [a jogkivonat érvényesítéséről](#validating-tokens) , beleértve a jogkivonat típusával és aláírásával kapcsolatos információkat.
* **Hasznos** adat – tartalmazza a felhasználóval vagy alkalmazással kapcsolatos összes fontos adatot, amely a szolgáltatás hívását kísérli meg.
* **Aláírás** – a jogkivonat érvényesítéséhez használt nyersanyag.

Mindegyik darabot egy pont ( `.` ) és egy külön Base64 kódolás választja el egymástól.

A jogcímek csak akkor jelennek meg, ha egy érték van kitöltve. Az alkalmazás nem veheti igénybe a függőségeket. Ilyenek például a következők: `pwd_exp` (nem minden bérlő igényli a jelszavak lejáratát) és `family_name` (az[ügyfél hitelesítő adatai](v2-oauth2-client-creds-grant-flow.md) a névvel nem rendelkező alkalmazások nevében vannak). A hozzáférési jogkivonat érvényesítéséhez használt jogcímek mindig jelen lesznek.

Egyes jogcímek segítségével az Azure AD biztonságos tokeneket használhat újbóli használat esetén. Ezeket a rendszer a leírásban nem a nyilvános felhasználáshoz, hanem "átlátszatlan" jelöléssel jelölte meg. Ezek a jogcímek esetleg nem jelenhetnek meg a jogkivonatokban, és az újakat értesítés nélkül lehet felvenni.

### <a name="header-claims"></a>Fejléc jogcímei

|Jogcím | Formátum | Leírás |
|--------|--------|-------------|
| `typ` | String – mindig "JWT" | Azt jelzi, hogy a token egy JWT.|
| `nonce` | Sztring | A jogkivonat-Visszajátszási támadások elleni védelemhez használt egyedi azonosító. Az erőforrás rögzítheti ezt az értéket a visszajátszás elleni védelemhez. |
| `alg` | Sztring | Megadja a token aláírásához használt algoritmust (például "RS256"). |
| `kid` | Sztring | Megadja a jogkivonat aláírásához használt nyilvános kulcs ujjlenyomatát. Az 1.0-s és a v 2.0-s hozzáférési jogkivonatokban lett kibocsátva. |
| `x5t` | Sztring | A (használatban és értékben) ugyanazokat a függvényeket használja, mint a `kid` . `x5t` egy örökölt jogcím, amely csak a v 1.0 hozzáférési jogkivonatokban, kompatibilitási célból lett kibocsátva. |

### <a name="payload-claims"></a>Hasznos adatokhoz tartozó jogcímek

| Jogcím | Formátum | Leírás |
|-----|--------|-------------|
| `aud` | Karakterlánc, alkalmazás-azonosító URI vagy GUID | Azonosítja a token címzettjeit – a célközönségét.  Az API-nak ellenőriznie kell ezt az értéket, és el kell utasítania a tokent, ha az érték nem egyezik. A 2.0-s verziókban ez mindig az API ügyfél-azonosítója, a 1.0-s verzióban pedig az ügyfél-azonosító vagy a kérelemben használt erőforrás-URI lehet attól függően, hogy az ügyfél hogyan kérelmezte a jogkivonatot.|
| `iss` | Karakterlánc, STS URI | Azonosítja azt a biztonságijogkivonat-szolgáltatást (STS), amely létrehozza és visszaadja a tokent, valamint azt az Azure AD-bérlőt, amelyben a felhasználó hitelesítése megtörtént. Ha a kiállított jogkivonat egy v 2.0-token (lásd a `ver` jogcímet), akkor az URI-ja befejeződik `/v2.0` . Az a GUID, amely azt jelzi, hogy a felhasználó egy Microsoft-fiók vásárló felhasználója `9188040d-6c67-4c5b-b112-36a304b66dad` . Az alkalmazás a jogcím GUID részét használhatja arra, hogy korlátozza azon bérlők készletét, amelyek be tudnak jelentkezni az alkalmazásba, ha vannak ilyenek. |
|`idp`| Karakterlánc, általában egy STS URI | A jogkivonat alanyát hitelesítő identitásszolgáltatót adja meg. Ez az érték megegyezik a kiállítói jogcímek értékével, kivéve, ha a felhasználói fiók nem ugyanabban a bérlőben található, mint a kiállító – vendég. Ha a jogcím nem létezik, az azt jelenti, hogy a értéke használható `iss` .  A szervezeti környezetben használt személyes fiókok esetében (például egy Azure AD-bérlőnek meghívott személyes fiók) a `idp` jogcím "Live.com" vagy a Microsoft-fiók bérlőt tartalmazó STS URI lehet `9188040d-6c67-4c5b-b112-36a304b66dad` . |
| `iat` | int, egy UNIX timestamp | A "kiállító" érték azt jelzi, hogy a jogkivonat hitelesítése mikor történt. |
| `nbf` | int, egy UNIX timestamp | A "NBF" (nem korábban) jogcím azt az időpontot határozza meg, ameddig a JWT nem fogadható el a feldolgozáshoz. |
| `exp` | int, egy UNIX timestamp | Az "exp" (lejárati idő) jogcím azt a lejárati időt azonosítja, amely után a JWT nem fogadható el feldolgozásra. Fontos megjegyezni, hogy egy erőforrás még a token elutasítása előtt is elutasítja a jogkivonatot, például ha a hitelesítés megváltozása vagy jogkivonat-visszavonás észlelhető. |
| `aio` | Átlátszatlan karakterlánc | Az Azure AD által az adatok a jogkivonat újrafelhasználásához használt belső jogcímek. Az erőforrások nem használhatják ezt a jogcímet. |
| `acr` | Karakterlánc, "0" vagy "1" | Csak a v 1.0 jogkivonatokban jelennek meg. A "hitelesítési környezet osztálya" jogcím. A "0" érték azt jelzi, hogy a végfelhasználói hitelesítés nem felelt meg az ISO/IEC 29115 követelményeinek. |
| `amr` | Karakterláncok JSON-tömbje | Csak a v 1.0 jogkivonatokban jelennek meg. Azt határozza meg, hogy a jogkivonat tárgya hogyan lett hitelesítve. További részletekért tekintse meg [az AMR-jogcím szakaszt](#the-amr-claim) . |
| `appid` | Karakterlánc, GUID | Csak a v 1.0 jogkivonatokban jelennek meg. A tokent használó ügyfél alkalmazás-azonosítója. Az alkalmazás önállóan vagy egy felhasználó nevében működhet. Az alkalmazás azonosítója általában egy Application objektumot jelöl, de az Azure AD-ben egy egyszerű szolgáltatásnév-objektumot is jelenthet. |
| `azp` | Karakterlánc, GUID | Csak a v 2.0-tokenekben jelennek meg, a helyett `appid` . A tokent használó ügyfél alkalmazás-azonosítója. Az alkalmazás önállóan vagy egy felhasználó nevében működhet. Az alkalmazás azonosítója általában egy Application objektumot jelöl, de az Azure AD-ben egy egyszerű szolgáltatásnév-objektumot is jelenthet. |
| `appidacr` | "0", "1" vagy "2" | Csak a v 1.0 jogkivonatokban jelennek meg. Azt jelzi, hogy az ügyfél hogyan lett hitelesítve. Nyilvános ügyfél esetén az érték "0". Ha az ügyfél-azonosító és az ügyfél titkos kulcsa van használatban, az érték "1". Ha ügyféltanúsítvány lett használva a hitelesítéshez, az érték a következő: "2". |
| `azpacr` | "0", "1" vagy "2" | Csak a v 2.0-tokenekben jelennek meg, a helyett `appidacr` . Azt jelzi, hogy az ügyfél hogyan lett hitelesítve. Nyilvános ügyfél esetén az érték "0". Ha az ügyfél-azonosító és az ügyfél titkos kulcsa van használatban, az érték "1". Ha ügyféltanúsítvány lett használva a hitelesítéshez, az érték a következő: "2". |
| `preferred_username` | Sztring | A felhasználót jelölő elsődleges Felhasználónév. Ez lehet egy e-mail-cím, telefonszám vagy egy általános Felhasználónév megadott formátum nélkül. Az értéke változtatható, és idővel változhat. Mivel ez az érték változhat, nem használható az engedélyezési döntések elvégzéséhez.  A felhasználónevek, valamint az emberi használatra alkalmas felhasználói felületen felhasználónévként is használható. A `profile` hatókör a jogcím megszerzéséhez szükséges. Csak v 2.0-tokenekben szerepel. |
| `name` | Sztring | Egy ember által olvasható értéket biztosít, amely azonosítja a jogkivonat tárgyát. Az érték nem garantált, hogy egyedi, megváltoztathatatlan, és kizárólag megjelenítési célokra való használatra készült. A `profile` hatókör a jogcím megszerzéséhez szükséges. |
| `scp` | Karakterlánc, a hatókörök szóközzel tagolt listája | Annak az alkalmazásnak a hatóköre, amelyhez az ügyfélalkalmazás kérelmezte (és fogadta) a hozzájárulásukat. Az alkalmazásnak ellenőriznie kell, hogy ezek a hatókörök érvényesek-e az alkalmazás számára, és a hatókörök értéke alapján kell-e engedélyezési döntéseket hoznia. Csak a [felhasználói jogkivonatok](#user-and-application-tokens)részeként. |
| `roles` | Karakterláncok tömbje, engedélyek listája | Az alkalmazás által a kérelmező alkalmazásnak vagy felhasználónak megadott engedélyekkel rendelkező engedélyek készlete. Az [alkalmazás-jogkivonatok](#user-and-application-tokens)esetében ez az ügyfél-hitelesítő adatok ([v 1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md), [v 2.0](v2-oauth2-client-creds-grant-flow.md)) felhasználói hatókörök helyett használatos.  [Felhasználói jogkivonatok](#user-and-application-tokens) esetén ez a felhasználó által a célalkalmazás számára hozzárendelt szerepkörökkel van feltöltve. |
| `wids` | [RoleTemplateID](../roles/permissions-reference.md#role-template-ids) GUID-azonosítók tömbje | Az ehhez a felhasználóhoz rendelt bérlői szintű szerepköröket jelöli a [rendszergazdai szerepkörök lapon](../roles/permissions-reference.md#role-template-ids)lévő szerepkörök szakasza alapján.  Ez a jogcím az `groupMembershipClaims` [alkalmazás jegyzékfájljának](reference-app-manifest.md)tulajdonságán alapuló, alkalmazáson belüli alapon van konfigurálva.  Az "all" vagy a "DirectoryRole" beállítás megadása kötelező.  Előfordulhat, hogy a jogkivonat-hossz miatti implicit folyamat során beszerzett jogkivonatok nem jelennek meg. |
| `groups` | GUID-azonosítók JSON-tömbje | A tulajdonos csoportjának tagságát képviselő objektumazonosítók benyújtása. Ezek az értékek egyediek (lásd: objektumazonosító), és biztonságosan használhatók a hozzáférés felügyeletéhez, például az erőforrásokhoz való hozzáférés engedélyezésének kényszerítéséhez. A groups jogcímben szereplő csoportok alkalmazáson belüli alapon vannak konfigurálva, az `groupMembershipClaims` [alkalmazás jegyzékfájljának](reference-app-manifest.md)tulajdonságán keresztül. A Null érték kizárja az összes csoportot, a "SecurityGroup" érték pedig csak Active Directory biztonsági csoporttagság részét fogja tartalmazni, az "all" érték pedig a biztonsági csoportokat és a Microsoft 365 terjesztési listát is tartalmazza. <br><br>Tekintse `hasgroups` meg az alábbi jogcímet a `groups` jogcímek implicit támogatással történő használatával kapcsolatos részletekért. <br>Más folyamatok esetében, ha a felhasználó által birtokolt csoportok száma meghaladja a határértéket (150 az SAML, 200 for JWT esetében), a rendszer felvesz egy túllépési jogcímet a felhasználóhoz tartozó csoportok listáját tartalmazó Microsoft Graph végpontra. |
| `hasgroups` | Logikai | Ha van, akkor mindig `true` , ha a felhasználó legalább egy csoportban szerepel. A `groups` JWTs implicit engedélyezési folyamatokban használt jogcímek helyett, ha a teljes csoportok jogcím kiterjeszti az URI-töredéket az URL-cím hosszának korlátain túl (jelenleg 6 vagy több csoport). Azt jelzi, hogy az ügyfélnek a Microsoft Graph API-t kell használnia a felhasználó csoportjainak () meghatározásához `https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects` . |
| `groups:src1` | JSON-objektum | A nem hosszúságú jogkivonat-kérelmek esetében (lásd a `hasgroups` fentieket), de még mindig túl nagy a tokenhez, a rendszer a felhasználó teljes csoportok listájára mutató hivatkozást tartalmaz. Elosztott jogcímek esetén az SAML-t a jogcím helyett új jogcímként JWTs `groups` . <br><br>**Példa JWT értékre**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }` |
| `sub` | Sztring | Az a rendszerbiztonsági tag, amelyről a jogkivonat adatokat érvényesít, például egy alkalmazás felhasználóját. Ez az érték nem módosítható, és nem rendelhető hozzá újra, és nem használható újra. Az engedélyezési ellenőrzések biztonságos végrehajtásához használható, például ha a jogkivonat egy erőforrás elérésére szolgál, és kulcsként használható az adatbázis tábláiban. Mivel a tulajdonos mindig szerepel az Azure AD által felmerülő jogkivonatokban, javasoljuk, hogy használja ezt az értéket egy általános célú engedélyezési rendszeren. A tulajdonos azonban egy páros-azonosító – egyedi egy adott alkalmazás-AZONOSÍTÓhoz. Ezért ha egyetlen felhasználó két különböző alkalmazásba jelentkezik be két különböző ügyfél-azonosítóval, akkor ezek az alkalmazások két különböző értéket kapnak a tulajdonos jogcímek számára. Az architektúrától és az adatvédelmi követelményektől függően előfordulhat, hogy ez nem szükséges. Lásd még a `oid` jogcím (amely a bérlőn belüli alkalmazások esetében is változatlan marad). |
| `oid` | Karakterlánc, GUID | Egy objektum megváltoztathatatlan azonosítója a Microsoft Identity platformon, ebben az esetben egy felhasználói fiók. Emellett az engedélyezési ellenőrzések biztonságos elvégzésére, valamint az adatbázis tábláiban lévő kulcsra is felhasználható. Ez az azonosító egyedileg azonosítja a felhasználót az alkalmazások között – két különböző alkalmazás, amely ugyanazon a felhasználón jelentkezik be, ugyanazokat az értékeket kapja meg a `oid` jogcímben. Így `oid` a Microsoft online szolgáltatások (például a Microsoft Graph) lekérdezéseit is felhasználhatja. A Microsoft Graph `id` egy adott [felhasználói fiókhoz](/graph/api/resources/user)tartozó tulajdonságként adja vissza ezt az azonosítót. Mivel a `oid` lehetővé teszi, hogy több alkalmazás is összekapcsolja a felhasználókat, a `profile` hatókörre azért van szükség, hogy megkapja ezt a jogcímet. Vegye figyelembe, hogy ha egyetlen felhasználó több bérlőn is létezik, akkor a felhasználó minden bérlőn egy másik objektumazonosítót fog tartalmazni – ezeket a rendszer akkor is eltérő fióknak tekinti, ha a felhasználó ugyanazzal a hitelesítő adatokkal jelentkezik be az egyes fiókokba. |
| `tid` | Karakterlánc, GUID | Azt az Azure AD-bérlőt jelöli, amelyhez a felhasználó származik. Munkahelyi és iskolai fiókok esetén a GUID az a szervezet, amelyhez a felhasználó tartozik. A személyes fiókok esetében az érték a következő: `9188040d-6c67-4c5b-b112-36a304b66dad` . A `profile` hatókör a jogcím megszerzéséhez szükséges. |
| `unique_name` | Sztring | Csak a v 1.0 jogkivonatokban jelennek meg. A jogkivonat alanyát azonosító, ember által olvasható értéket ad meg. Ez az érték nem garantált, hogy a bérlőn belül egyediek legyenek, és csak megjelenítési célokra használhatók. |
| `uti` | Átlátszatlan karakterlánc | Az Azure által a jogkivonatok újraellenőrzéséhez használt belső jogcím. Az erőforrások nem használhatják ezt a jogcímet. |
| `rh` | Átlátszatlan karakterlánc | Az Azure által a jogkivonatok újraellenőrzéséhez használt belső jogcím. Az erőforrások nem használhatják ezt a jogcímet. |
| `ver` | Karakterlánc, `1.0` vagy `2.0` | Megadja a hozzáférési jogkivonat verzióját. |

**Csoporton túli jogcímek**

Annak biztosítása érdekében, hogy a jogkivonat mérete ne haladja meg a HTTP-fejlécek méretének korlátait, az Azure AD korlátozza a csoportok jogcímeben foglalt objektumazonosítók számát. Ha a felhasználó több csoport tagja, mint a túlhasználati korlát (150 az SAML-tokenekhez, 200 az JWT tokenekhez, és csak 6, ha az implicit folyamaton keresztül van kiadva), akkor az Azure AD nem bocsátja ki a groups jogcímet a jogkivonatban. Ehelyett a jogkivonat olyan túlhasználati jogcímet tartalmaz, amely azt jelzi, hogy az alkalmazás lekérdezi a Microsoft Graph API-t a felhasználó csoporttagság beolvasásához.

```JSON
{
  ...
  "_claim_names": {
   "groups": "src1"
    },
    {
  "_claim_sources": {
    "src1": {
        "endpoint":"[Url to get this user's group membership from]"
        }
       }
     }
  ...
}
```

Az `BulkCreateGroups.ps1` [alkalmazás-létrehozási parancsfájlok](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-2-Groups/AppCreationScripts) mappában találhatóak segítségével tesztelheti a túlhasználatos forgatókönyveket.

#### <a name="v10-basic-claims"></a>v 1.0 alapszintű jogcímek

A következő jogcímek a v 1.0 jogkivonatokban lesznek felszámítva, ha vannak ilyenek, de alapértelmezés szerint nem szerepelnek a 2.0-s jogkivonatokban. Ha a 2.0-s verzióját használja, és szüksége van ezekre a jogcímek egyikére, kérje meg őket az [opcionális jogcímek](active-directory-optional-claims.md)használatával.

| Jogcím | Formátum | Leírás |
|-----|--------|-------------|
| `ipaddr`| Sztring | A felhasználó által hitelesített IP-cím. |
| `onprem_sid`| Karakterlánc, [SID-formátumban](/windows/desktop/SecAuthZ/sid-components) | Azokban az esetekben, amikor a felhasználó helyszíni hitelesítéssel rendelkezik, ez a jogcím a biztonsági azonosítóját adja meg. `onprem_sid`A for Authorization használatával örökölt alkalmazásokban is használható.|
| `pwd_exp`| int, egy UNIX timestamp | Azt jelzi, hogy a felhasználó jelszava lejár-e. |
| `pwd_url`| Sztring | Egy URL-cím, amelyben a felhasználók elküldhetik a jelszavukat alaphelyzetbe. |
| `in_corp`| boolean | Azt jelzi, hogy az ügyfél bejelentkezik-e a vállalati hálózatról. Ha nem, a rendszer nem tartalmazza a jogcímet. |
| `nickname`| Sztring | A felhasználó további neve, amely az utónévtől és az utónévtől elválasztva.|
| `family_name` | Sztring | A felhasználó vezetéknevét, vezetéknevét vagy családjának nevét adja meg a felhasználói objektumban definiált módon. |
| `given_name` | Sztring | A felhasználó első vagy megadott nevét adja meg a felhasználói objektumra vonatkozóan. |
| `upn` | Sztring | A felhasználó felhasználóneve. Lehet telefonszám, e-mail-cím vagy formázatlan karakterlánc. Csak megjelenítési célokra kell használni, és a Felhasználónév-emlékeztetőket is meg kell adni az újrahitelesítési helyzetekben. |

#### <a name="the-amr-claim"></a>A `amr` jogcím

A Microsoft-identitások különböző módokon hitelesíthetők, ami az alkalmazás szempontjából fontos lehet. A `amr` jogcím olyan tömb, amely több elemet is tartalmazhat, például egy `["mfa", "rsa", "pwd"]` jelszót és a hitelesítő alkalmazást egyaránt használó hitelesítéshez.

| Érték | Leírás |
|-----|-------------|
| `pwd` | Jelszó-hitelesítés, vagy egy felhasználó Microsoft-jelszava vagy egy alkalmazás ügyfél-titka. |
| `rsa` | A hitelesítés egy RSA-kulcs igazolásán alapul, például a [Microsoft Authenticator alkalmazással](https://aka.ms/AA2kvvu). Ez azt is magában foglalja, hogy a hitelesítés egy önaláírt JWT történt, amely egy szolgáltatás tulajdonában álló X509 tanúsítvánnyal rendelkezik. |
| `otp` | Egyszeri jelszó, amely e-mailt vagy szöveges üzenetet használ. |
| `fed` | A rendszer összevont hitelesítési állítást (például JWT vagy SAML) használt. |
| `wia` | Integrált Windows-hitelesítés |
| `mfa` | A [többtényezős hitelesítés](../authentication/concept-mfa-howitworks.md) használatban volt. Ha ez jelen van, a többi hitelesítési módszer is szerepelni fog. |
| `ngcmfa` | Ezzel egyenértékű `mfa` , bizonyos speciális hitelesítő adatok kiépítési céljára szolgál. |
| `wiaormfa`| A felhasználó Windows vagy MFA hitelesítő adatokat használt a hitelesítéshez. |
| `none` | Nem történt hitelesítés. |

## <a name="validating-tokens"></a>Tokenek ellenőrzése

Nem minden alkalmazásnak kell érvényesíteni a jogkivonatokat. Csak bizonyos helyzetekben kell érvényesíteni a jogkivonatot:

* A [webes API](quickstart-configure-app-expose-web-apis.md) -knak ellenőrizniük kell, hogy az ügyfél által továbbított hozzáférési jogkivonatok érvényesek-e.  Csak a jogcímeket tartalmazó jogkivonatokat fogadják el `aud` .
* A bizalmas webalkalmazások, például a ASP.NET Corenak ellenőrizniük kell a nekik a hibrid folyamat felhasználói böngészőjén keresztül továbbított azonosító jogkivonatokat, mielőtt engedélyezi a hozzáférést a felhasználó adataihoz vagy munkamenetet hozzon létre.

Ha a fenti forgatókönyvek egyike sem teljesül, az alkalmazás nem fogja kihasználni a jogkivonat érvényesítését, és biztonsági és megbízhatósági kockázatot jelenthet, ha a jogkivonatok érvényességén alapuló döntések történnek.  A nyilvános ügyfelek, például a natív alkalmazások vagy a gyógyfürdők nem élvezik a jogkivonatok érvényesítését – az alkalmazás közvetlenül kommunikál a IDENTITÁSSZOLGÁLTATÓ, így az SSL-védelem biztosítja, hogy a jogkivonatok érvényesek legyenek.

Az API-k és a webalkalmazások csak olyan jogkivonatokat érvényesítenek, amelyeknek az `aud` alkalmazása megfelel az alkalmazásnak; más erőforrásokhoz egyéni jogkivonat-ellenőrzési szabályok tartozhatnak. A Microsoft Graph jogkivonatai például a saját formátumuk miatt nem lesznek érvényesítve a szabályok alapján. Egy másik erőforráshoz tartozó jogkivonatok érvényesítése és elfogadása egy példa a [zavaros helyettes](https://cwe.mitre.org/data/definitions/441.html) problémájára.

Ha az alkalmazásnak ellenőriznie kell egy id_token vagy egy access_token a fentieknek megfelelően, az alkalmazásnak először ellenőriznie kell a jogkivonat aláírását és kiállítóját az OpenID-felderítési dokumentum értékeivel. A dokumentum bérlőtől független verziója például a következő helyen található: [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration) .

Az alábbi információk azokra a felhasználókra vonatkoznak, akik meg szeretnék ismerni a mögöttes folyamatot. Az Azure AD-alapú middleware beépített funkciókat biztosít a hozzáférési jogkivonatok érvényesítéséhez, és böngészhet a [minták](sample-v2-code.md) között, és megkeresheti az Ön által választott nyelven. A JWT ellenőrzéséhez több harmadik féltől származó nyílt forráskódú kódtár is rendelkezésre áll – a szinte minden platformhoz és nyelvhez legalább egy lehetőség van. Az Azure AD-hitelesítési kódtárakkal és a kódokkal kapcsolatos további információkért tekintse meg a [hitelesítési kódtárakat](reference-v2-libraries.md).

### <a name="validating-the-signature"></a>Az aláírás ellenőrzése

A JWT három szegmenst tartalmaz, amelyek a karakterrel vannak elválasztva `.` . Az első szegmens a **fejléc**, a második a **törzs**, a harmadik pedig az **aláírás**. Az aláírási szegmens használatával ellenőrizheti a jogkivonat hitelességét, hogy az alkalmazás megbízható legyen.

Az Azure AD által kiállított jogkivonatok az iparági szabványnak megfelelő aszimmetrikus titkosítási algoritmusokkal (például RS256) vannak aláírva. A JWT fejléce a jogkivonat aláírásához használt kulcs-és titkosítási módszerről tartalmaz információkat:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk",
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

A `alg` jogcím a jogkivonat aláírásához használt algoritmust jelzi, míg a `kid` jogcím azt a nyilvános kulcsot jelzi, amelyet a jogkivonat érvényesítéséhez használt.

Az Azure AD bármely adott időpontban aláírhat egy id_token a nyilvános titkos kulcsok egy adott készletének egyikével. Az Azure AD rendszeres időközönként elforgatja a kulcsok lehetséges készletét, így az alkalmazást úgy kell megírni, hogy a kulcs módosításait automatikusan kezeljék. Az Azure AD által használt nyilvános kulcsok frissítéseinek ellenőrzéséhez szükséges ésszerű gyakoriság 24 óránként.

Az aláírás ellenőrzéséhez szükséges aláíró kulcs adatokat a következő helyen található [OpenID Connect metaadat-dokumentum](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document) használatával érheti el:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Próbálja ki ezt az [URL-címet](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) egy böngészőben!

Ez a metaadat-dokumentum:

* A egy JSON-objektum, amely számos hasznos információt tartalmaz, például az OpenID Connect hitelesítéshez szükséges különböző végpontok helyét.
* A tartalmaz egy `jwks_uri` , amely megadja a jogkivonatok aláírásához használt nyilvános kulcsok helyét. A (z) helyen található JSON webkulcs (JWK) tartalmazza az adott `jwks_uri` pillanatban használatban lévő összes nyilvánoskulcs-információt.  A JWK formátumot a 7517- [es RFC-dokumentum](https://tools.ietf.org/html/rfc7517)írja le.  Az alkalmazás a `kid` JWT fejlécben szereplő jogcím használatával kiválaszthatja, hogy a dokumentum melyik nyilvános kulcsát használták egy adott token aláírására. Ezt követően az aláírás érvényesítése a helyes nyilvános kulccsal és a jelzett algoritmussal végezhető el.

> [!NOTE]
> Javasoljuk `kid` , hogy a jogkivonatának érvényesítéséhez használja a jogcímet. Bár a 1.0-s verzióban a `x5t` és a `kid` jogcímek is szerepelnek, a v 2.0-tokenek csak a jogcímeket tartalmazzák `kid` .

Az aláírás érvényesítése kívül esik a dokumentum hatókörén – számos nyílt forráskódú kódtár elérhető, így szükség esetén segítséget nyújt.  A Microsoft Identity platformnak azonban van egy jogkivonat-aláíró kiterjesztése a Standards-Custom aláíró kulcsokra.

Ha az alkalmazás a [jogcímek leképezése](active-directory-claims-mapping.md) funkciójának eredményeként egyéni aláíró kulcsokkal rendelkezik, akkor az alkalmazás `appid` azonosítóját tartalmazó lekérdezési paramétert kell hozzáfűzni, hogy az `jwks_uri` alkalmazás aláírási kulcsára vonatkozó információkra mutasson, amelyeket az ellenőrzéshez kell használni. Például a a következőt `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` tartalmazza: `jwks_uri` `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e` .

### <a name="claims-based-authorization"></a>Jogcím-alapú hitelesítés

Az alkalmazás üzleti logikája ezt a lépést fogja megállapítani, néhány gyakori engedélyezési módszert az alábbiakban talál.

* Ellenőrizze a `scp` vagy a `roles` jogcímet annak ellenőrzéséhez, hogy az összes jelen hatókör megfelel-e az API által elérhetőnek, és engedélyezze az ügyfélnek a kért műveletet.
* Győződjön meg arról, hogy a hívó ügyfél számára engedélyezett az API meghívása a `appid` jogcím használatával.
* A hívó ügyfél hitelesítési állapotának ellenőrzése a használatával `appidacr` – nem lehet 0, ha a nyilvános ügyfelek nem hívhatják meg az API-t.
* Tekintse át a korábbi `nonce` jogcímek listáját, és ellenőrizze, hogy a jogkivonat nem lett-e újrajátszva.
* Győződjön meg arról, hogy az `tid` megegyezik egy olyan Bérlővel, amely számára engedélyezett az API meghívása.
* A `amr` jogcím használatával ellenőrizze, hogy a felhasználó végezte-e az MFA-t. Ezt a [feltételes hozzáférés](../conditional-access/overview.md)használatával kell kikényszeríteni.
* Ha a `roles` `groups` hozzáférési jogkivonatban kérte a vagy jogcímeket, ellenőrizze, hogy a felhasználó jogosult-e a művelet végrehajtására.
  * Az implicit folyamattal lekért tokenek esetében valószínűleg le kell kérdezni az ilyen [adatMicrosoft Grapht](https://developer.microsoft.com/graph/) , mivel gyakran túl nagy ahhoz, hogy illeszkedjen a tokenhez.

## <a name="user-and-application-tokens"></a>Felhasználói és alkalmazási jogkivonatok

Előfordulhat, hogy az alkalmazás jogkivonatokat kap a felhasználó számára (általában a folyamatot tárgyalja), vagy közvetlenül egy alkalmazásból (az [ügyfél hitelesítő adatainak folyamatán](../azuread-dev/v1-oauth2-client-creds-grant-flow.md)keresztül). Ezek az alkalmazási tokenek azt jelzik, hogy ez a hívás egy alkalmazásból származik, és nem rendelkezik a felhasználó biztonsági mentésével. Ezek a jogkivonatok kezelése nagyjából azonos:

* A használatával `roles` megtekintheti a jogkivonat tulajdonosának biztosított engedélyeket.
* A `oid` vagy a használatával `sub` ellenőrizze, hogy a hívó szolgáltatásnév a várt érték-e.

Ha az alkalmazásnak meg kell különböztetnie az alkalmazáshoz tartozó hozzáférési jogkivonatokat és a felhasználók hozzáférési jogkivonatait, használja az `idtyp` [opcionális jogcímet](active-directory-optional-claims.md).  `idtyp`Ha a jogcímet hozzáadja a `accessToken` mezőhöz, és ellenőrzi az értéket `app` , akkor csak az alkalmazáshoz tartozó hozzáférési jogkivonatok észlelhetők.  A felhasználók azonosító jogkivonatai és hozzáférési jogkivonatai nem `idtyp` foglalják magukban a jogcímeket.

## <a name="token-revocation"></a>Jogkivonat visszavonása

A frissítési tokeneket a rendszer bármikor, különböző okok miatt érvénytelenítheti vagy visszavonhatja. Ezek két fő kategóriába sorolhatók: időtúllépések és visszavonások.

### <a name="token-timeouts"></a>Jogkivonat-időtúllépések

A [jogkivonat-élettartam konfigurációjának](active-directory-configurable-token-lifetimes.md)használatával megváltoztatható a frissítési tokenek élettartama.  Normális és várható, hogy egyes tokenek használata nélkül megy (például a felhasználó nem nyitja meg az alkalmazást 3 hónapig), ezért lejár.  Az alkalmazások olyan forgatókönyveket fognak tapasztalni, amelyekben a bejelentkezési kiszolgáló elutasítja a frissítési jogkivonatot a kora miatt.

* MaxInactiveTime: Ha a frissítési token nem lett használva a MaxInactiveTime által diktált időn belül, a frissítési token többé nem lesz érvényes.
* MaxSessionAge: Ha a MaxAgeSessionMultiFactor vagy a MaxAgeSessionSingleFactor értéke nem az alapértelmezett (a visszavonás után) értékre van állítva, akkor az újrahitelesítésre akkor van szükség, amikor a MaxAgeSession * eltelik.
* Angol nyelvű Példák:
  * A bérlőnek öt napja van egy MaxInactiveTime, és a felhasználó egy hétig ment a vakáción, így az Azure AD nem kapott új jogkivonat-kérelmet a felhasználótól 7 napon belül. Amikor a felhasználó legközelebb új jogkivonatot kér, megtalálják a frissítési jogkivonatot, és újra meg kell adniuk a hitelesítő adataikat.
  * Egy bizalmas alkalmazásnak egy nap MaxAgeSessionSingleFactor kell lennie. Ha a felhasználó hétfőn és kedden (25 óra elteltével) bejelentkezik, újra kell hitelesítenie.

### <a name="revocation"></a>Visszavont

A frissítési tokeneket a kiszolgáló visszavonhatja a hitelesítő adatok módosítása, illetve a használati vagy rendszergazdai műveletek miatt.  A frissítési tokenek két osztályba sorolhatók – ezek a bizalmas ügyfelek (a jobb szélső oszlop) és a nyilvános ügyfelek számára kiállítottak (az összes többi oszlop).

| Módosítás | Jelszó alapú cookie | Jelszó alapú jogkivonat | Nem jelszó alapú cookie | Nem jelszó alapú jogkivonat | Bizalmas ügyfél jogkivonata |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| A jelszó lejár | Életben marad | Életben marad | Életben marad | Életben marad | Életben marad |
| Jelszó módosítva felhasználó által | Revoked | Revoked | Életben marad | Életben marad | Életben marad |
| A felhasználó nem SSPR | Revoked | Revoked | Életben marad | Életben marad | Életben marad |
| Rendszergazdai jelszó alaphelyzetbe állítása | Revoked | Revoked | Életben marad | Életben marad | Életben marad |
| A felhasználó visszavonja a frissítési jogkivonatokat a [PowerShell](/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) használatával | Revoked | Revoked | Revoked | Revoked | Revoked |
| A rendszergazda visszavonja a felhasználó összes frissítési jogkivonatát a [PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken) használatával | Revoked | Revoked |Revoked | Revoked | Revoked |
| Egyszeri kijelentkezés ([v 1.0](../azuread-dev/v1-protocols-openid-connect-code.md#single-sign-out), [v 2.0](v2-protocols-oidc.md#single-sign-out) ) a weben | Revoked | Életben marad | Revoked | Életben marad | Életben marad |

#### <a name="non-password-based"></a>Nem jelszó alapú

A *nem jelszó alapú* bejelentkezés olyan esetben, amikor a felhasználó nem adott meg jelszót a letöltéshez. Példák a nem jelszó alapú bejelentkezésre:

- Az arc és a Windows Hello használata
- FIDO2 kulcs
- SMS
- Hang
- PIN kód

Az elsődleges frissítési tokenekkel kapcsolatos további részletekért tekintse meg az [elsődleges frissítési jogkivonatokat](../devices/concept-primary-refresh-token.md) .

## <a name="next-steps"></a>Következő lépések

* Ismerje meg [ `id_tokens` Az Azure ad-](id-tokens.md)t.
* További információ az engedélyekről és a hozzájárulásról ( [1.0](../azuread-dev/v1-permissions-consent.md), [v 2.0](v2-permissions-and-consent.md)).
