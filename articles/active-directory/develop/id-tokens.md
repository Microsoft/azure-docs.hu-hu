---
title: Microsoft Identity platform azonosító tokenek | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan használhatók az Azure AD v 1.0 és a Microsoft Identity platform (v 2.0) végpontok által kibocsátott id_tokens.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms:custom: fasttrack-edit
ms.openlocfilehash: 21e0b800e06b7a5ad0351ff53d26a5e2cd2ba71c
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175388"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Microsoft Identity platform azonosító tokenek

`id_tokens` az alkalmazás az [OpenID Connect](v2-protocols-oidc.md) (OIDC) folyamat részeként lesz elküldve az ügyfélalkalmazás számára. Egy hozzáférési jogkivonat mellett vagy ahelyett is elküldhetők, amelyet az ügyfél a felhasználó hitelesítésére használ.

## <a name="using-the-id_token"></a>A id_token használata

Az azonosító jogkivonatokat annak ellenőrzésére kell használni, hogy a felhasználó ki és milyen további hasznos információkat igényel róluk – a [hozzáférési jogkivonat](access-tokens.md)helyett nem használható. Az általa biztosított jogcímek az alkalmazásban, az [adatbázis kulcsaiként](#using-claims-to-reliably-identify-a-user-subject-and-object-id)és az ügyfélalkalmazás elérésének biztosítására használhatók.  

## <a name="claims-in-an-id_token"></a>Jogcímek egy id_token

`id_tokens` a [JWTs](https://tools.ietf.org/html/rfc7519) (JSON webes tokenek), ami azt jelenti, hogy fejlécből, adattartalomból és aláírásból álló részből állnak. A fejléc és az aláírás segítségével ellenőrizheti a jogkivonat hitelességét, míg a hasznos adatok tartalmazzák az ügyfél által kért felhasználó adatait. Ha nincs megadva, az itt felsorolt összes JWT-jogcím 1.0-s és v 2.0-tokenekben is megjelenik.

### <a name="v10"></a>1.0-s verzió

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

Tekintse meg ezt a 1.0-s [JWT.MS](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q)-mintát.

### <a name="v20"></a>2.0-s verzió

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

Tekintse meg ezt a v 2.0 minta tokent a [JWT.MS](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw)-ben.

### <a name="header-claims"></a>Fejléc jogcímei

|Jogcím | Formátum | Leírás |
|-----|--------|-------------|
|`typ` | String – mindig "JWT" | Azt jelzi, hogy a token egy JWT token.|
|`alg` | Sztring | Meghatározza a jogkivonat aláírásához használt algoritmust. Példa: "RS256" |
|`kid` | Sztring | A jogkivonat ellenőrzéséhez használt nyilvános kulcs ujjlenyomata. Az 1.0-s és a v 2.0-s verzióban is kibocsátva `id_tokens` . |
|`x5t` | Sztring | Ugyanaz (használatban és értékben), mint `kid` . Ez azonban egy örökölt jogcím, amely csak a v 1.0-s verzióban, kompatibilitási célból lett kibocsátva `id_tokens` . |

### <a name="payload-claims"></a>Hasznos adatokhoz tartozó jogcímek

Ez a lista azokat a JWT jogcímeket jeleníti meg, amelyek alapértelmezés szerint a legtöbb id_tokensban szerepelnek (kivéve, ha van ilyen).  Az alkalmazás azonban [választható jogcímeket](active-directory-optional-claims.md) is használhat a ID_TOKEN további JWT jogcímek igényléséhez.  Ezek a jogcím alapján a `groups` felhasználó nevével kapcsolatos információkra terjedhetnek.

|Jogcím | Formátum | Leírás |
|-----|--------|-------------|
|`aud` |  Karakterlánc, alkalmazás-azonosító URI | Azonosítja a jogkivonat kívánt címzettjét. A-ben a célközönség az alkalmazáshoz `id_tokens` hozzárendelt alkalmazás azonosítója a Azure Portal. Az alkalmazásnak ellenőriznie kell ezt az értéket, és el kell utasítania a jogkivonatot, ha az érték nem egyezik. |
|`iss` |  Karakterlánc, STS URI | Azonosítja azt a biztonságijogkivonat-szolgáltatást (STS), amely létrehozza és visszaadja a tokent, valamint azt az Azure AD-bérlőt, amelyben a felhasználó hitelesítése megtörtént. Ha a jogkivonatot a v 2.0-végpont adta ki, akkor az URI-ja befejeződik `/v2.0` .  Az a GUID, amely azt jelzi, hogy a felhasználó egy Microsoft-fiók vásárló felhasználója `9188040d-6c67-4c5b-b112-36a304b66dad` . Az alkalmazásnak a jogcím GUID részét kell használnia, hogy korlátozza azon bérlők készletét, amelyek be tudnak jelentkezni az alkalmazásba, ha vannak ilyenek. |
|`iat` |  int, egy UNIX timestamp | A "kiállító" érték azt jelzi, hogy a jogkivonat hitelesítése mikor történt.  |
|`idp`|Karakterlánc, általában egy STS URI | A jogkivonat alanyát hitelesítő identitásszolgáltatót adja meg. Ez az érték megegyezik a kiállítói jogcímek értékével, kivéve, ha a felhasználói fiók nem ugyanabban a bérlőben található, mint a kiállító – vendég. Ha a jogcím nem létezik, az azt jelenti, hogy a értéke használható `iss` .  A szervezeti környezetben használt személyes fiókok esetében (például egy Azure AD-bérlőnek meghívott személyes fiók) a `idp` jogcím "Live.com" vagy a Microsoft-fiók bérlőt tartalmazó STS URI lehet `9188040d-6c67-4c5b-b112-36a304b66dad` . |
|`nbf` |  int, egy UNIX timestamp | A "NBF" (nem korábban) jogcím azt az időpontot határozza meg, ameddig a JWT nem fogadható el a feldolgozáshoz.|
|`exp` |  int, egy UNIX timestamp | Az "exp" (lejárati idő) jogcím azt a lejárati időt azonosítja, amely után a JWT nem fogadható el feldolgozásra.  Fontos megjegyezni, hogy egy erőforrás még a token elutasítása előtt is elutasítja a jogkivonatot – Ha például a hitelesítés változására van szükség, vagy a rendszer jogkivonat-visszavonást észlelt. |
| `c_hash`| Sztring |A kód kivonata csak akkor szerepel az azonosító jogkivonatokban, ha az azonosító jogkivonatot egy OAuth 2,0-es engedélyezési kóddal adják ki. Az engedélyezési kódok hitelességének ellenőrzéséhez használható. Az érvényesítés végrehajtásáról az [OpenID Connect specifikációjában](https://openid.net/specs/openid-connect-core-1_0.html)olvashat bővebben. |
|`at_hash`| Sztring |A hozzáférési jogkivonat kivonata csak akkor szerepel az azonosító jogkivonatokban, ha az azonosító jogkivonatot a `/authorize` OAuth 2,0 hozzáférési jogkivonattal adja ki a végpontból. A hozzáférési token hitelességének ellenőrzéséhez használható. Az érvényesítés végrehajtásáról az [OpenID Connect specifikációjában](https://openid.net/specs/openid-connect-core-1_0.html#HybridIDToken)olvashat bővebben. Ez a végpont nem ad vissza azonosító jogkivonatokat `/token` . |
|`aio` | Átlátszatlan karakterlánc | Az Azure AD által az adatok a jogkivonat újrafelhasználásához használt belső jogcímek. Figyelmen kívül kell hagyni.|
|`preferred_username` | Sztring | A felhasználót jelölő elsődleges Felhasználónév. Ez lehet egy e-mail-cím, telefonszám vagy egy általános Felhasználónév megadott formátum nélkül. Az értéke változtatható, és idővel változhat. Mivel ez az érték változhat, nem használható az engedélyezési döntések elvégzéséhez. `profile`Ennek a jogcímnek a fogadásához a hatókörre van szükség.|
|`email` | Sztring | A jogcímek `email` alapértelmezés szerint az e-mail-címmel rendelkező vendég fiókok esetében jelennek meg.  Az alkalmazás kérheti az e-mail-jogcímet a felügyelt felhasználók számára (az erőforrással megegyező bérlőtől) a `email` [választható jogcím](active-directory-optional-claims.md)használatával.  A v 2.0-s végponton az alkalmazás az `email` OpenID Connect hatókörét is kérheti – nem kell megadnia mind a választható jogcímet, mind a hatókört a jogcím beszerzéséhez.  Az e-mail-jogcím csak a felhasználó profiljának adataiból származó címezhető leveleket támogatja. |
|`name` | Sztring | A `name` jogcím egy ember által olvasható értéket biztosít, amely azonosítja a jogkivonat tárgyát. Az érték nem garantált, hogy egyedi, megváltoztathatatlan, és csak megjelenítési célokra használható. `profile`Ennek a jogcímnek a fogadásához a hatókörre van szükség. |
|`nonce`| Sztring | Az egyszeres érték megegyezik az eredeti/Authorize-kérelemben szereplő paraméterrel a IDENTITÁSSZOLGÁLTATÓ. Ha nem egyezik, az alkalmazásnak el kell utasítania a jogkivonatot. |
|`oid` | Karakterlánc, GUID | Egy objektum megváltoztathatatlan azonosítója a Microsoft Identity Systemben, ebben az esetben egy felhasználói fiók. Ez az azonosító egyedileg azonosítja a felhasználót az alkalmazások között – két különböző alkalmazás, amely ugyanazon a felhasználón jelentkezik be, ugyanazokat az értékeket kapja meg a `oid` jogcímben. A Microsoft Graph `id` egy adott felhasználói fiókhoz tartozó tulajdonságként adja vissza ezt az azonosítót. Mivel a `oid` lehetővé teszi, hogy több alkalmazás is összekapcsolja a felhasználókat, a `profile` hatókörnek meg kell kapnia a jogcímet. Vegye figyelembe, hogy ha egyetlen felhasználó több bérlőn is létezik, akkor a felhasználó minden bérlőn egy másik objektumazonosítót fog tartalmazni – ezeket a rendszer akkor is különböző fiókoknak tekinti, ha a felhasználó ugyanazzal a hitelesítő adatokkal jelentkezik be az egyes fiókokba. A `oid` jogcím egy GUID azonosító, és nem használható fel újra. |
|`roles`| Karakterláncok tömbje | A bejelentkezett felhasználóhoz hozzárendelt szerepkörök összessége. |
|`rh` | Átlátszatlan karakterlánc |Az Azure által a jogkivonatok újraellenőrzéséhez használt belső jogcím. Figyelmen kívül kell hagyni. |
|`sub` | Sztring | Az a rendszerbiztonsági tag, amelyről a jogkivonat adatokat érvényesít, például egy alkalmazás felhasználóját. Ez az érték nem módosítható, és nem rendelhető hozzá újra, és nem használható újra. A tulajdonos egy páros-azonosító – egyedi egy adott alkalmazás-AZONOSÍTÓhoz. Ha egyetlen felhasználó két különböző alkalmazásba jelentkezik be két különböző ügyfél-azonosítóval, akkor ezek az alkalmazások két különböző értéket kapnak a tulajdonos jogcímek számára. Az architektúra-és adatvédelmi követelményektől függően ez lehet vagy nem kívánatos. |
|`tid` | Karakterlánc, GUID | Egy GUID, amely azt az Azure AD-bérlőt jelöli, amelyből a felhasználó származik. Munkahelyi és iskolai fiókok esetén a GUID az a szervezet, amelyhez a felhasználó tartozik. A személyes fiókok esetében az érték a következő: `9188040d-6c67-4c5b-b112-36a304b66dad` . `profile`Ennek a jogcímnek a fogadásához a hatókörre van szükség. |
|`unique_name` | Sztring | A jogkivonat alanyát azonosító, ember által olvasható értéket ad meg. Ez az érték egy adott időpontban egyedi, de az e-mailek és más azonosítók újból felhasználhatók, ez az érték más fiókokban is újra szerepelhet, ezért csak megjelenítési célokra használható. Csak a 1.0-s verzióban van kiállítva `id_tokens` . |
|`uti` | Átlátszatlan karakterlánc | Az Azure által a jogkivonatok újraellenőrzéséhez használt belső jogcím. Figyelmen kívül kell hagyni. |
|`ver` | Karakterlánc, vagy 1,0 vagy 2,0 | Megadja a id_token verzióját. |
|`hasgroups`|Logikai|Ha van, mindig igaz, ami azt jelöli, hogy a felhasználó legalább egy csoportban van. A JWTs implicit engedélyezési folyamatokban használt csoportok helyett használható, ha a teljes csoportokra vonatkozó jogcím kiterjeszti az URI-töredéket az URL-cím hosszának korlátain túl (jelenleg 6 vagy több csoport). Azt jelzi, hogy az ügyfélnek a Microsoft Graph API-t kell használnia a felhasználó csoportjainak () meghatározásához `https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects` .|
|`groups:src1`|JSON-objektum | A nem hosszúságú jogkivonat-kérelmek esetében (lásd a `hasgroups` fentieket), de még mindig túl nagy a tokenhez, a rendszer a felhasználó teljes csoportok listájára mutató hivatkozást tartalmaz. Elosztott jogcímek esetén az SAML-t a jogcím helyett új jogcímként JWTs `groups` . <br><br>**Példa JWT értékre**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }`<br><br> További információ: [groups overing jogcím](#groups-overage-claim).|

> [!NOTE]
> A 1.0-s és a 2.0-s verzió id_token a fenti példákban látható információk mennyiségétől függ. A verzió a kért végponton alapul. Habár a meglévő alkalmazások valószínűleg az Azure AD-végpontot használják, az új alkalmazásoknak a Microsoft Identity platformot kell használniuk.
>
> - 1.0-s verzió: Azure AD-végpontok: `https://login.microsoftonline.com/common/oauth2/authorize`
> - v 2.0: Microsoft Identity platform-végpontok: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

### <a name="using-claims-to-reliably-identify-a-user-subject-and-object-id"></a>A felhasználók megbízható azonosítására szolgáló jogcímek használata (tárgy és objektumazonosító)

Amikor azonosít egy felhasználót (például megkeresi őket egy adatbázisban, vagy eldönti, hogy milyen engedélyekkel rendelkeznek), fontos, hogy olyan információkat használjon, amelyek állandó és egyedi maradnak az idő során. Az örökölt alkalmazások időnként olyan mezőket használnak, mint az e-mail-cím, a telefonszám vagy az egyszerű felhasználónév.  Ezek az idő múlásával változhatnak, és idővel újra felhasználhatók, amikor egy alkalmazott megváltoztatja a nevét, vagy egy alkalmazott olyan e-mail-címet kap, amely megegyezik egy korábbi, már meglévő alkalmazottal. Ezért **fontos, hogy az** alkalmazás ne használjon emberi olvasásra alkalmas adatait, hogy azonosítsa a felhasználó által olvasható, általánosságban azt jelenti, hogy valaki el fogja olvasni, és módosítani szeretné. Ehelyett használja a OIDC standard által biztosított jogcímeket, vagy a Microsoft által biztosított kiterjesztési jogcímeket, illetve a `sub` `oid` jogcímeket.

Ha az adatokat felhasználónként szeretné tárolni, használjon `sub` vagy `oid` önállóan (amely egyedi GUID-azonosítók), és szükség esetén használja `tid` az útválasztást vagy a horizontális skálázást.  Ha a szolgáltatások között meg kell osztania az adatmegosztást, a `oid` + `tid` legjobb, ha az összes alkalmazás ugyanazokat `oid` és `tid` jogcímeket kap az adott felhasználó számára.  A `sub` Microsoft Identity platform jogcíme "pair-Wise" – a jogkivonat-címzett, a bérlő és a felhasználó kombinációja alapján egyedi.  Így az adott felhasználóhoz tartozó azonosító jogkivonatokat kérő alkalmazások különböző `sub` jogcímeket kapnak, de az adott `oid` felhasználóhoz tartozó jogcímeket is.

>[!NOTE]
> Ne használja a `idp` jogcímet a felhasználóval kapcsolatos adatok tárolására a bérlők közötti összekapcsolásra tett kísérlet során.  Ez nem fog működni, mivel a `oid` és a `sub` jogcímek a bérlők között megváltoznak, így biztosítva, hogy az alkalmazások ne tudják nyomon követni a felhasználókat a bérlők között.  
>
> A vendég forgatókönyvek, ahol egy felhasználó egy bérlőn van felhasználva, és a hitelesítés egy másikban történik, úgy kell kezelnie a felhasználót, mintha az új felhasználó legyen a szolgáltatásban.  A contoso-bérlő dokumentumai és jogosultságai nem alkalmazhatók a fabrikam-bérlőben. Ez azért fontos, hogy megakadályozza a véletlen adatszivárgást a bérlők között.

### <a name="groups-overage-claim"></a>Csoporton túli jogcímek
Annak biztosítása érdekében, hogy a jogkivonat mérete ne haladja meg a HTTP-fejléc méretének korlátait, az Azure AD korlátozza a jogcímben foglalt objektumazonosítók számát `groups` . Ha a felhasználó több csoport tagja, mint a túlhasználati korlát (SAML-tokenek esetében 150, 200 JWT-token esetében), akkor az Azure AD nem bocsátja ki a groups jogcímet a jogkivonatban. Ehelyett a jogkivonat olyan túlhasználati jogcímet tartalmaz, amely azt jelzi, hogy az alkalmazás lekérdezi a Microsoft Graph API-t a felhasználó csoporttagság beolvasásához.

```json
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

## <a name="validating-an-id_token"></a>Id_token ellenőrzése

A egy `id_token` [hozzáférési jogkivonat érvényesítésének](access-tokens.md#validating-tokens) első lépése hasonló, az ügyfél ellenőrizheti, hogy a megfelelő kiállító visszaküldte-e a tokent, és hogy nem módosították-e illetéktelenül. Mivel `id_tokens` a JWT-tokenek mindig a jogkivonatok érvényesítésére szolgálnak, ezért javasoljuk, hogy ezeket a tokeneket ne használja.  Vegye figyelembe, hogy csak a bizalmas ügyfelek (titkos kulcsokkal rendelkezők) ellenőrzik az azonosító jogkivonatokat.  Nyilvános alkalmazások (a teljes mértékben a nem vezérelt eszközön vagy hálózaton futó kód, például egy felhasználó böngészője vagy otthoni hálózata) nem jogosult az azonosító jogkivonat érvényesítésére, mivel a rosszindulatú felhasználó feltartóztathatja és szerkesztheti a jogkivonat érvényesítéséhez használt kulcsokat.

A jogkivonat manuális érvényesítéséhez tekintse meg a [hozzáférési token érvényesítése](access-tokens.md#validating-tokens)című témakör lépéseit. A jogkivonat aláírásának ellenőrzése után a következő JWT jogcímeket érvényesíteni kell a id_tokenban (ezeket a jogkivonat-ellenőrzési kódtár is megteheti):

* Időbélyegek: a `iat` , `nbf` és az `exp` időbélyegek az aktuális időpont előtt vagy után kell esniük, ha szükséges.
* Célközönség: a `aud` jogcímnek meg kell egyeznie az alkalmazáshoz tartozó alkalmazás-azonosítóval.
* Alkalom: az `nonce` adattartalomban szereplő jogcímnek meg kell egyeznie a/Authorize-végpontnak a kezdeti kérelem során átadott egyszeres paraméterrel.

## <a name="next-steps"></a>Következő lépések

* Tudnivalók a [hozzáférési tokenekről](access-tokens.md)
* A JWT jogcímek testreszabása a id_token [választható jogcímek](active-directory-optional-claims.md)használatával.
