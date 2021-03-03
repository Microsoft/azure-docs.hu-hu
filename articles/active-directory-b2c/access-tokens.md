---
title: Hozzáférési jogkivonat kérése – Azure Active Directory B2C | Microsoft Docs
description: Útmutató hozzáférési jogkivonat kéréséhez az Azure Active Directory B2C-től.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/26/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e5168d5e5e3935da267fb26f38735a88bdfd7837
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101654476"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>Hozzáférési jogkivonat kérése az Azure Active Directory B2C-ben

A *hozzáférési jogkivonat* olyan jogcímeket tartalmaz, amelyet az Azure Active Directory B2C-ben (Azure AD B2C) használhat az API-knak megadott engedélyek azonosítására. Az erőforrás-kiszolgálók meghívásakor a HTTP-kérésnek tartalmaznia kell egy hozzáférési jogkivonatot. A hozzáférési jogkivonatot az **access_token** sztring jelöli az Azure AD B2C-től kapott válaszban.

Ez a cikk bemutatja, hogyan kérhet hozzáférési jogkivonatot a webalkalmazások és webes API-k számára. Az Azure AD B2C-ben használt jogkivonatokról [az Azure Active Directory B2C-ben használt jogkivonatok áttekintését](tokens-overview.md) ismertető cikkben talál további információt.

> [!NOTE]
> **Az Azure AD B2C nem támogatja a webes API-láncokat (meghatalmazásos folyamat).** – Számos architektúrában szerepelnek olyan webes API-k, amelyek más, alsóbb rétegbeli webes API-kat hívnak meg, és mindkét API biztonságát az Azure AD B2C garantálja. A forgatókönyv olyan ügyfelekben gyakori, amelyek webes API-háttérrel rendelkeznek, amely egy másik szolgáltatást hív meg. Ez a láncolatba fűzött webes API-megoldás az OAuth 2.0 JWT tulajdonosi hitelesítő adatok megadásával (vagy más néven a meghatalmazásos folyamat segítségével) valósítható meg. A meghatalmazásos folyamatot azonban még nem implementáltuk az Azure AD B2C-ben.

## <a name="prerequisites"></a>Előfeltételek

- [Hozzon létre egy felhasználói folyamatot](tutorial-create-user-flows.md), amellyel a felhasználók regisztrálhatnak és bejelentkezhetnek az alkalmazásba.
- Ha még nem tette meg, [adjon hozzá egy webes API-alkalmazást az Azure Active Directory B2C-bérlőhöz](add-web-api-application.md).

## <a name="scopes"></a>Hatókörök

A hatókörök lehetőséget nyújtanak a védett erőforrásokra vonatkozó engedélyek kezelésére. Hozzáférési jogkivonat kérésekor az ügyfélalkalmazásnak meg kell adni a kívánt engedélyeket a kérés **scope** paraméterében. Ha például a(z) `read` **hatókörértéket** szeretné megadni a(z) `https://contoso.onmicrosoft.com/api` **alkalmazásazonosító URI-vel** rendelkező API-ra vonatkozóan, a hatókör `https://contoso.onmicrosoft.com/api/read` lenne.

A hatóköröket a webes API a hatóköralapú hozzáférés-vezérlés megvalósításához használja. A webes API-k bizonyos felhasználói például rendelkezhetnek olvasási és írási hozzáféréssel is, míg mások csak olvasási hozzáféréssel. Ha több engedélyt szeretne beszerezni ugyanabban a kérésben, hozzáadhat több bejegyzést is a kérés **scope** paraméteréhez, szóközzel elválasztva.

Az alábbi példában URL-címben dekódolt hatókörök láthatók:

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

Az alábbi példában URL-címben dekódolt hatókörök láthatók:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

Ha az ügyfélalkalmazásnak megadottnál több hatókört kér, a hívás akkor lesz sikeres, ha legalább egy engedély megadása megtörténik. Az eredményül kapott hozzáférési jogkivonatban található **scp** jogcímhez csak a sikeresen megadott engedélyeket adja hozzá a rendszer. 

### <a name="openid-connect-scopes"></a>OpenID Connect-hatókörök

Az OpenID Connect szabvány több speciális hatókörértéket megad. Az alábbi hatókörök a felhasználó profiljának elérését lehető tévő engedélyt jelölik:

- **openid** – Azonosító jogkivonatot kér.
- **offline_access** – Frissítési jogkivonatot kér [hitelesítésikód-folyamatokkal](authorization-code-flow.md).
- **00000000-0000-0000-0000-000000000000** – az ügyfél-azonosító használata hatókörként azt jelzi, hogy az alkalmazásnak rendelkeznie kell egy olyan hozzáférési jogkivonattal, amely a saját szolgáltatásán vagy webes API-ban használható, ugyanazokat az ügyfél-azonosítókat jelképezve.

Ha egy `/authorize` kérésben található **response_type** paraméterben az `token` szerepel, a **hatókör** paraméternek tartalmaznia kell legalább egy olyan erőforrás-hatókört, amely meg lesz adva, és amely nem az `openid` vagy az `offline_access`. Az `/authorize` kérés ellenkező esetben meghiúsul.

## <a name="request-a-token"></a>Jogkivonat kérése

Hozzáférési jogkivonat kéréséhez engedélyezési kódra van szüksége. Alább az `/authorize` végpontjának küldött, engedélyezési kód igénylésére irányuló kérésre látható példa. Az egyéni tartományok hozzáférési jogkivonatokkal való használata nem támogatott. A kérés URL-címében használja saját bérlőnév.onmicrosoft.com tartományát.

A következő példában ezeket az értékeket fogja lecserélni:

- `<tenant-name>` – Az Azure AD B2C-bérlőjének neve.
- `<policy-name>` – Az egyéni szabályzatának vagy felhasználói folyamatának neve.
- `<application-ID>` – A felhasználói folyamat támogatása érdekében regisztrált webalkalmazás alkalmazásazonosítója.
- `<redirect-uri>` – Az ügyfélalkalmazás regisztrálásakor megadott **átirányítási URI**.

```http
GET https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&response_type=code
```

Az engedélyezési kódot tartalmazó válasznak a következő példához hasonlónak kell lennie:

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

Ha sikeresen megkapta az engedélyezési kódot, annak segítségével kérhet hozzáférési jogkivonatot:

```http
POST <tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/token HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=<application-ID>
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
&redirect_uri=https://jwt.ms
&client_secret=2hMG2-_:y12n10vwH...
```

Az alábbihoz hasonló válasznak kell megjelennie:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrN...",
    "token_type": "Bearer",
    "not_before": 1549647431,
    "expires_in": 3600,
    "expires_on": 1549651031,
    "resource": "f2a76e08-93f2-4350-833c-965c02483b11",
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiJjNjRhNGY3ZC0zMDkxLTRjNzMtYTcyMi1hM2YwNjk0Z..."
}
```

Amikor a visszaadott hozzáférési jogkivonatot vizsgálja a https://jwt.ms használatával, az alábbi példához hasonló eredménynek kell megjelennie:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dl..."
}.{
  "iss": "https://contoso0926tenant.b2clogin.com/c64a4f7d-3091-4c73-a7.../v2.0/",
  "exp": 1549651031,
  "nbf": 1549647431,
  "aud": "f2a76e08-93f2-4350-833c-965...",
  "oid": "1558f87f-452b-4757-bcd1-883...",
  "sub": "1558f87f-452b-4757-bcd1-883...",
  "name": "David",
  "tfp": "B2C_1_signupsignin1",
  "nonce": "anyRandomValue",
  "scp": "read",
  "azp": "38307aee-303c-4fff-8087-d8d2...",
  "ver": "1.0",
  "iat": 1549647431
}.[Signature]
```

## <a name="next-steps"></a>Következő lépések

- Tudnivalók a [jogkivonatok Azure AD B2C-ben történő konfigurálásáról](configure-tokens.md)
