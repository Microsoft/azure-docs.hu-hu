---
title: A Microsoft identitásplatform-azonosító jogkivonatai | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan használhatja id_tokens Azure AD v1.0 és a Microsoft Identity Platform (v2.0) végpontok által kibocsátott virtuális gépeket.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/02/2021
ms.author: hirsin
ms.reviewer: hirsin
ms.custom:
- aaddev
- identityplatformtop40
- fasttrack-edit
ms.openlocfilehash: 885379a02c8866f2829fb681683a93b1d8d314fa
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530020"
---
# <a name="microsoft-identity-platform-id-tokens"></a>A Microsoft identitásplatform-azonosító jogkivonatai

`id_tokens` A rendszer egy OIDC-folyamat részeként küldi el [OpenID Connect](v2-protocols-oidc.md) ügyfélalkalmazásnak. Egy hozzáférési jogkivonattal együtt vagy helyett elküldhetők, és az ügyfél használja őket a felhasználó hitelesítéséhez.

## <a name="using-the-id_token"></a>A id_token

Azonosító jogkivonatokkal kell ellenőrizni, hogy a felhasználó az-e, akinek állítja magát, és további hasznos információkat kell kapnia róla – hozzáférési jogkivonat helyett nem szabad használni az [engedélyezéshez.](access-tokens.md) Az itt elérhető jogcímek felhasználhatók az alkalmazáson belüli felhasználói felülethez, egy adatbázisban lévő kulcsként, valamint az ügyfélalkalmazáshoz való hozzáférés biztosításához. [](#using-claims-to-reliably-identify-a-user-subject-and-object-id)  

## <a name="claims-in-an-id_token"></a>Jogcímek egy id_token

`id_tokens` A [JWT-k](https://tools.ietf.org/html/rfc7519) (JSON webes jogkivonatok), ami azt jelenti, hogy egy fejlécből, hasznos tartalomból és aláírási részből állnak. A fejléc és az aláírás használatával ellenőrizheti a jogkivonat hitelességét, a hasznos adatok pedig az ügyfél által kért felhasználóval kapcsolatos információkat tartalmaznak. Kivéve, ahol fel van jelölve, az itt felsorolt összes JWT-jogcím 1.0-s és 2.0-s tokenben is megjelenik.

### <a name="v10"></a>1.0-s verzió

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

Tekintse meg ezt az 1.0-s minta-jogkivonatot [a jwt.ms.](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q)

### <a name="v20"></a>2.0-s verzió

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

Tekintse meg ezt a 2.0-s minta-jogkivonatot [a jwt.ms.](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw)

### <a name="header-claims"></a>Fejléc jogcímek

|Jogcím | Formátum | Leírás |
|-----|--------|-------------|
|`typ` | Sztring – mindig "JWT" | Azt jelzi, hogy a jogkivonat egy JWT-jogkivonat.|
|`alg` | Sztring | A jogkivonat aláíráshoz használt algoritmust jelzi. Például: "RS256" |
|`kid` | Sztring | A jogkivonat ellenőrzéséhez használt nyilvános kulcs ujjlenyomata. Az 1.0-s és a 2.0-s v2-ben is ki van `id_tokens` bocsátva. |
|`x5t` | Sztring | Ugyanaz (használatban és értékben), mint `kid` a . Ez azonban egy örökölt jogcím, amely kompatibilitási célokból csak az 1.0-s `id_tokens` verzióban van kiadva. |

### <a name="payload-claims"></a>Hasznos terhelési jogcímek

Ez a lista a legtöbb alapértelmezett JWT-jogcímet id_tokens (kivéve, ahol fel van jelölve).  Az alkalmazás azonban [](active-directory-optional-claims.md) opcionális jogcímekkel kérhet további JWT-jogcímeket a id_token.  Ezek a jogcímtől `groups` a felhasználó nevével kapcsolatos információkon át is eltávodnak.

|Jogcím | Formátum | Leírás |
|-----|--------|-------------|
|`aud` |  Sztring, alkalmazásazonosító URI | A jogkivonat kívánt címzettjének azonosítására szolgál. A alkalmazásban a célközönség az alkalmazás alkalmazásazonosítója, amely az alkalmazáshoz van rendelve a `id_tokens` Azure Portal. Az alkalmazásnak ellenőriznie kell ezt az értéket, és el kell utasítania a jogkivonatot, ha az érték nem egyezik. |
|`iss` |  Sztring, STS URI | Azonosítja a jogkivonatot felépítő és visszaküldő biztonsági jogkivonat-szolgáltatást (STS), valamint azt az Azure AD-bérlőt, amelyben a felhasználó hitelesítése történt. Ha a jogkivonatot a 2.0-s végpont bocsátotta ki, az URI a következőre végződik: `/v2.0` .  A guid azonosító, amely azt jelzi, hogy a felhasználó felhasználó egy Microsoft-fiók `9188040d-6c67-4c5b-b112-36a304b66dad` felhasználója. Az alkalmazásnak a jogcím GUID részét kell használnia az alkalmazásba bejelentkező bérlők halmazának korlátozására, ha van ilyen. |
|`iat` |  int, UNIX időbélyegző | A "Kibocsátó" azt jelzi, hogy mikor történt a jogkivonat hitelesítése.  |
|`idp`|Sztring, általában STS URI | A jogkivonat alanyát hitelesítő identitásszolgáltatót adja meg. Ez az érték megegyezik a Kiállító jogcím értékével, kivéve, ha a felhasználói fiók nem ugyanabban a bérlőben található, mint a kiállító – vendégek. Ha a jogcím nincs jelen, az azt jelenti, hogy a értéke `iss` használható helyette.  A szervezeti környezetben használt személyes fiókok (például egy Azure AD-bérlőbe meghívott személyes fiók) esetében a jogcím lehet "live.com" vagy egy STS URI, amely tartalmazza a `idp` Microsoft-fiók-bérlőt. `9188040d-6c67-4c5b-b112-36a304b66dad` |
|`nbf` |  int, UNIX időbélyegző | Az "nbf" (nem előtte) jogcím azonosítja azt az időt, amely előtt a JWT NEM fogadható el feldolgozásra.|
|`exp` |  int, UNIX időbélyegző | Az "exp" (lejárati idő) jogcím azonosítja azt a lejárati időt, amely után a JWT NEM fogadható el feldolgozásra.  Fontos megjegyezni, hogy az erőforrás ezt megelőzően is elutasíthatja a jogkivonatot – például ha változásra van szükség a hitelesítésben, vagy jogkivonat-visszavonást észleltek. |
| `c_hash`| Sztring |A kódkivonat csak akkor szerepel az azonosító jogkivonatban, ha az azonosító jogkivonatot OAuth 2.0 hitelesítési kóddal adták ki. Felhasználható egy engedélyezési kód hitelességének ellenőrzésére. Az ellenőrzés elvégzésével kapcsolatos részletekért lásd a OpenID Connect [specifikációját.](https://openid.net/specs/openid-connect-core-1_0.html) |
|`at_hash`| Sztring |A hozzáférési jogkivonat csak akkor szerepel az azonosító jogkivonatban, ha az azonosító jogkivonatot OAuth 2.0 hozzáférési jogkivonattal adták ki a `/authorize` végpontról. Használható a hozzáférési jogkivonat hitelességének ellenőrzéshez. Az ellenőrzés elvégzésével kapcsolatos részletekért lásd a OpenID Connect [specifikációját.](https://openid.net/specs/openid-connect-core-1_0.html#HybridIDToken) Ez nem lesz visszaadva a végpont azonosító `/token` jogkivonatainál. |
|`aio` | Átlátszatlan sztring | Egy belső jogcím, amelyet az Azure AD az adatok a jogkivonatok újbóli felhasználásához való rögzítésére használ. Figyelmen kívül kell hagyni.|
|`preferred_username` | Sztring | A felhasználót képviselő elsődleges felhasználónév. Ez lehet egy e-mail-cím, telefonszám vagy egy általános felhasználónév megadott formátum nélkül. Az értéke változtatható, és idővel változhat. Mivel ez az érték nem állítható be, nem használható engedélyezési döntések meghozatalához. A `profile` jogcím fogadásához a hatókör szükséges.|
|`email` | Sztring | A `email` jogcím alapértelmezés szerint jelen van az e-mail-címmel rendelkező vendégfiókok esetében.  Az alkalmazás a választható jogcím használatával kérheti a felügyelt felhasználók (az erőforrással azonos bérlőről származó) e-mailes `email` [jogcímét.](active-directory-optional-claims.md)  A 2.0-s végponton az alkalmazás a OpenID Connect-hatókört is le tudja kérni – a jogcím lekéréséhez nem szükséges a nem kötelező jogcímet és a hatókört `email` is kérnie.  Az e-mail jogcím csak a felhasználó profilinformációiból származó címezhető e-maileket támogatja. |
|`name` | Sztring | A jogcím egy emberi olvasásra alkalmas értéket biztosít, amely `name` azonosítja a jogkivonat tárgyát. Az érték nem garantáltan egyedi, hanem testreszabható, és úgy van kialakítva, hogy csak megjelenítési célokra legyen használható. A `profile` jogcím fogadásához a hatókör szükséges. |
|`nonce`| Sztring | Az nonce megegyezik az eredeti /authorize kérelemben szereplő paraméterrel az internetszolgáltatónak. Ha nem egyezik, az alkalmazásnak el kell utasítania a jogkivonatot. |
|`oid` | Sztring, GUID | Egy objektum nem módosítható azonosítója a Microsoft identitásrendszerében, ebben az esetben egy felhasználói fiókban. Ez az azonosító egyedileg azonosítja a felhasználót a különböző alkalmazásokban – az ugyanazon felhasználóba bejelentkező két különböző alkalmazás ugyanazt az értéket fogja kapni a `oid` jogcímben. A Microsoft Graph ezt az azonosítót adja vissza tulajdonságként `id` egy adott felhasználói fiókhoz. Mivel `oid` a lehetővé teszi, hogy több alkalmazás korreláljon a felhasználókkal, a hatókör szükséges a `profile` jogcím fogadásához. Vegye figyelembe, hogy ha egy felhasználó több bérlőben is létezik, a felhasználó minden bérlőben más objektumazonosítót fog tartalmazni – akkor is különböző fiókoknak minősül, ha mindegyik fiókba ugyanazokkal a hitelesítő adatokkal jelentkezik be. A `oid` jogcím egy GUID, és nem használható újra. |
|`roles`| Sztringek tömbje | A bejelentkező felhasználóhoz rendelt szerepkörök. |
|`rh` | Átlátszatlan sztring |Egy belső jogcím, amelyet az Azure a jogkivonatok újraértékeléseként használ. Figyelmen kívül kell hagyni. |
|`sub` | Sztring | A rendszerbiztonsági tag, amelyről a jogkivonat adatokat érvényesít, például egy alkalmazás felhasználója. Ez az érték nem módosítható, és nem lehet újból hozzárendelni vagy újra felhasználni. A tárgy egy párban megadott azonosító – egyedi egy adott alkalmazásazonosítóhoz. Ha egyetlen felhasználó két különböző alkalmazásba jelentkezik be két különböző ügyfél-azonosítóval, ezek az alkalmazások két különböző értéket kapnak a tárgy jogcíméhez. Ez az architektúra és az adatvédelmi követelmények függvényében lehet, hogy nem is szükséges. |
|`tid` | Sztring, GUID | Egy GUID, amely azt az Azure AD-bérlőt jelöli, amelyről a felhasználó rendelkezik. Munkahelyi és iskolai fiókok esetén a GUID annak a szervezetnek a nem módosítható bérlőazonosítója, amelyhez a felhasználó tartozik. Személyes fiókok esetén az érték `9188040d-6c67-4c5b-b112-36a304b66dad` . A `profile` jogcím fogadásához a hatókör szükséges. |
|`unique_name` | Sztring | A jogkivonat alanyát azonosító, ember által olvasható értéket ad meg. Ez az érték bármely adott időpontban egyedi, de mivel az e-mailek és más azonosítók újra felhasználhatók, ez az érték újra megjeleníthető más fiókokban, ezért csak megjelenítési célokra használható. Csak az 1.0-s v-ben van `id_tokens` kibocsátva. |
|`uti` | Átlátszatlan sztring | Az Azure által a jogkivonatok újraértékeléseként használt belső jogcím. Figyelmen kívül kell hagyni. |
|`ver` | Sztring, 1.0 vagy 2.0 | A id_token. |
|`hasgroups`|Logikai|Ha jelen van, mindig igaz, a felhasználó jelölése legalább egy csoportban van. A JWT-k csoportigénylése helyére használatos az implicit engedélyfolyamatok esetében, ha a teljes csoportok jogcíme az URL-cím hosszkorlátján túlra (jelenleg 6 vagy több csoport) kiterjeszti az URI-töredéket. Azt jelzi, hogy az ügyfélnek a Microsoft Graph API-val kell meghatároznia a felhasználói csoportokat ( `https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects` ).|
|`groups:src1`|JSON-objektum | A nem hosszal korlátozott (lásd fent) jogkivonat-kérelmeknél, de még mindig túl nagy a jogkivonathoz, a felhasználó teljes csoportok listájára mutató hivatkozás `hasgroups` is megjelenik. A JWT-k elosztott jogcímként való igénylése az SAML-hez a jogcím helyére új `groups` jogcímként. <br><br>**JWT-példaérték:** <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }`<br><br> További információ: [Csoportok túlóra jogcíme.](#groups-overage-claim)|

> [!NOTE]
> Az 1.0-s és id_token 2.0-s id_token a fenti példáknak megfelelő mennyiségű információt fognak hordozni. A verzió azon a végponton alapul, ahonnan a kérés történt. Bár a meglévő alkalmazások valószínűleg az Azure AD-végpontot használják, az új alkalmazásoknak a "Microsoft identitásplatformot" kell használniuk.
>
> - v1.0: Azure AD-végpontok: `https://login.microsoftonline.com/common/oauth2/authorize`
> - v2.0: Microsoft Identity Platform-végpontok: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

### <a name="using-claims-to-reliably-identify-a-user-subject-and-object-id"></a>Jogcímek használata a felhasználó megbízható azonosításához (tárgy és objektumazonosító)

Egy felhasználó azonosításakor (például egy adatbázisban való keresésekor vagy annak eldöntésekor, hogy milyen engedélyekkel rendelkezik) rendkívül fontos olyan információk használata, amelyek folyamatosan állandóak és egyediek maradnak. A régebbi alkalmazások néha olyan mezőket használnak, mint az e-mail-cím, a telefonszám vagy az UPN.  Ezek idővel változhatnak, és idővel újra felhasználhatók – amikor egy alkalmazott megváltoztatja a nevét, vagy olyan e-mail-címet kap, amely megegyezik egy korábbi, már nem jelenlott alkalmazott e-mail-címével). Ezért rendkívül **fontos,** hogy az alkalmazás ne használjon ember által olvasható adatokat a felhasználó azonosításához . Az emberi olvasás általában azt jelenti, hogy valaki elolvassa, és módosítani szeretné. Ehelyett használja az OIDC szabvány által biztosított jogcímeket vagy a Microsoft által biztosított kiterjesztési jogcímeket – a `sub` és `oid` jogcímeket.

Az adatok felhasználónkénti megfelelő tárolására használja a vagy az egyedül (amelyek egyedi GUID azonosítóként) az útválasztáshoz vagy a horizontális skálázáshoz `sub` `oid` `tid` használatosak, ha szükséges.  Ha adatokat kell megosztania a szolgáltatások között, a legjobb, ha minden alkalmazás ugyanazt és jogcímeket kap `oid` + `tid` egy `oid` adott `tid` felhasználóhoz.  A Microsoft identitásplatformján a jogcím "párosítva" – a jogkivonat címzettje, a bérlő és a felhasználó kombinációja `sub` alapján egyedi.  Így két alkalmazás, amely azonosító jogkivonatokat kér egy adott felhasználóhoz, eltérő jogcímeket kap, de ugyanezeket a jogcímeket az `sub` `oid` adott felhasználó számára.

>[!NOTE]
> Ne használja a jogcímet arra, hogy egy felhasználó adatait tárolja a felhasználók bérlők közötti `idp` korrelálatának megkísérlő kísérlete esetén.  Nem fog működni, mivel a felhasználó és jogcímei a bérlők között úgy változnak, hogy az alkalmazások ne tudják nyomon követni a felhasználókat a `oid` `sub` bérlők között.  
>
> A vendégforgatókönyvek, amelyekben a felhasználó az egyik bérlőben van, és egy másik bérlőben hitelesít, úgy kell kezelnie a felhasználót, mintha a szolgáltatás új felhasználója lenne.  A Contoso-bérlőben található dokumentumok és jogosultságok nem alkalmazhatók a Fabrikam-bérlőben. Ez azért fontos, hogy megakadályozza a bérlők közötti véletlen adatszivárgást.

### <a name="groups-overage-claim"></a>Csoportokra vonatkozó túlóraigény
Annak érdekében, hogy a jogkivonat mérete ne haladja meg a HTTP-fejléc méretkorlátját, az Azure AD korlátozza a jogcímbe foglalható objektum-azonosítók `groups` számát. Ha egy felhasználó több csoport tagja, mint a túlórakeret (SAML-jogkivonatok esetén 150, JWT-jogkivonatok esetén 200), akkor az Azure AD nem bocsátja ki a csoport jogcímet a jogkivonatban. Ehelyett egy overage jogcímet tartalmaz a jogkivonatban, amely azt jelzi az alkalmazásnak, hogy lekérdezi a Microsoft Graph API-t a felhasználó csoporttagságának lekéréséhez.

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

## <a name="id-token-lifetime"></a>Azonosító jogkivonat élettartama

Alapértelmezés szerint az azonosító jogkivonat 1 óráig érvényes – 1 óra után az ügyfélnek új azonosító jogkivonatot kell szereznie.

Módosíthatja az azonosító jogkivonatok élettartamát, így szabályozhatja, hogy az ügyfélalkalmazás milyen gyakran járjon le az alkalmazás-munkamenet lejártakor, és milyen gyakran igényli a felhasználótól az újra hitelesítést (beavatkozás nélkül vagy interaktív módon). További információ: [Configurable token lifetimes (A jogkivonatok élettartamának konfigurálása).](active-directory-configurable-token-lifetimes.md)

## <a name="validating-an-id_token"></a>A id_token

Az érvényesítése hasonlít a hozzáférési jogkivonat érvényesítésének első lépésére – az ügyfél ellenőrizheti, hogy a megfelelő kiállító küldte-e vissza a jogkivonatot, és hogy nem módosították-e. `id_token` [](access-tokens.md#validating-tokens) Mivel ezek mindig JWT-jogkivonatok, számos kódtár létezik a jogkivonatok érvényesítéséhez– javasoljuk, hogy ne saját maga, hanem ezek `id_tokens` egyikét használja.  Vegye figyelembe, hogy csak a bizalmas ügyfeleknek (titkos okkal) kell érvényesítenie az azonosító jogkivonatokat.  A nyilvános alkalmazások (teljes egészében az Ön által nem vezérelt eszközön vagy hálózaton futó kódok – például egy felhasználó böngészője vagy a saját hálózatuk) nem használják ki az azonosító jogkivonat érvényesítését, mivel egy rosszindulatú felhasználó elfoghatja és szerkesztheti a jogkivonat érvényesítéséhez használt kulcsokat.

A jogkivonat manuális érvényesítéséhez tekintse meg a hozzáférési jogkivonat érvényesítésének [lépéseit.](access-tokens.md#validating-tokens) A jogkivonat aláírásának ellenőrzése után a következő JWT-jogcímeket kell ellenőrizni a id_token -ban (ezeket a jogkivonat-érvényesítési kódtár is megteheti):

* Időbélyegek: a , , és időbélyegnek az aktuális időpont előtt vagy után kell `iat` `nbf` `exp` esni, szükség szerint.
* Célközönség: a `aud` jogcímnek meg kell egyeznie az alkalmazás azonosítójával.
* Nonce: a hasznos adatokat meg kell egyeznie az /authorize végpontnak a kezdeti kérés során átadott `nonce` nonce paraméterrel.

## <a name="next-steps"></a>Következő lépések

* Tudnivalók a [hozzáférési jogkivonatokkal kapcsolatban](access-tokens.md)
* Testre szabhatja a JWT-jogcímeket a id_token [opcionális jogcímek használatával.](active-directory-optional-claims.md)
