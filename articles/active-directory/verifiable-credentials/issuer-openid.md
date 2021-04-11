---
title: A kiállítói szolgáltatás kommunikációs példái (előzetes verzió)
description: Az Identity Provider és a kiállító szolgáltatás közötti kommunikáció részletei
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.workload: identity
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: bd1b7560967ff0c458639a5737a02fca27ffc9f5
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106169993"
---
# <a name="issuer-service-communication-examples-preview"></a>A kiállítói szolgáltatás kommunikációs példái (előzetes verzió)

A ellenőrizhető hitelesítő adat kiállító szolgáltatása a szervezet OpenID-kompatibilis identitás-szolgáltatója által generált jogcímek beolvasásával ellenőrizhető hitelesítő adatokat adhat ki. Ebből a cikkből megtudhatja, hogyan állíthatja be az identitás-szolgáltatót, hogy a hitelesítő képes legyen kommunikálni vele, és beolvasni a megfelelő azonosító tokent, hogy átadja a kiállító szolgáltatásnak. 

> [!IMPORTANT]
> Azure Active Directory ellenőrizhető hitelesítő adatok jelenleg nyilvános előzetes verzióban érhetők el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Egy ellenőrizhető hitelesítő adat kiállításához a hitelesítő utasítást kap a szerződés letöltésével a felhasználótól érkező adatok begyűjtéséhez és az információknak a kiállító szolgáltatásba való elküldéséhez. Ha azonosító tokent kell használnia, úgy kell beállítania az identitás-szolgáltatót, hogy engedélyezze a hitelesítő számára, hogy az OpenID Connect protokoll használatával jelentkezzen be egy felhasználóba. Az eredményül kapott azonosító jogkivonatban található jogcímek a ellenőrizhető hitelesítő adatok tartalmának feltöltésére szolgálnak. A hitelesítő hitelesíti a felhasználót az OpenID Connect engedélyezési kódjának folyamata alapján. Az OpenID-szolgáltatónak támogatnia kell a következő OpenID Connect-funkciókat: 

| Szolgáltatás | Leírás |
| ------- | ----------- |
| Engedélyezési típus | Az engedélyezési kód támogatásának típusát kell támogatnia. |
| Jogkivonat formátuma | Titkosítatlan kompakt JWTs kell létrehoznia. |
| Aláírási algoritmus | JWTs-aláírást kell létrehoznia az RSA 256 használatával. |
| Konfigurációs dokumentum | Támogatnia kell az OpenID Connect konfigurációs dokumentumot és a-t `jwks_uri` . | 
| Ügyfél-regisztráció | A nyilvános ügyfél-regisztrációt kell támogatnia a `redirect_uri` értékének használatával `vclient://openid/` . | 
| PKCE | Biztonsági okokból ajánlott, de nem kötelező. |

Az Ön személyazonosság-szolgáltatójának küldött HTTP-kérelmekre az alábbi példák tartoznak. Az azonosító szolgáltatónak el kell fogadnia és válaszolnia kell ezekre a kérelmekre az OpenID Connect hitelesítési szabványnak megfelelően.

## <a name="client-registration"></a>Ügyfél-regisztráció

Ellenőrizhető hitelesítő adatok fogadásához a felhasználóknak be kell jelentkezniük a IDENTITÁSSZOLGÁLTATÓ az Microsoft Authenticator alkalmazásból. 

Az Exchange engedélyezéséhez regisztráljon egy alkalmazást az identitás-szolgáltatóval. Ha az Azure AD-t használja, [itt](../develop/quickstart-register-app.md)találja az utasításokat. A regisztráláskor használja a következő értékeket.

| Beállítás | Érték |
| ------- | ----- |
| Alkalmazásnév | `<Issuer Name> Verifiable Credential Service` |
| Átirányítási URI | `vcclient://openid/ ` |


Miután regisztrált egy alkalmazást az identitás-szolgáltatónál, jegyezze fel az ügyfél-AZONOSÍTÓját. Ezt a következő szakaszban fogja használni. Le kell írnia az URL-címet a jól ismert végpontra is a OIDC-kompatibilis identitás-szolgáltató számára. A kiállító szolgáltatás ezt a végpontot használja az azonosító jogkivonat ellenőrzéséhez szükséges nyilvános kulcsok letöltéséhez, amint azt a hitelesítő küldi.

A konfigurált átirányítási URI-t a hitelesítő használja, hogy tudja, mikor fejeződik be a bejelentkezés, és lekérheti az azonosító tokent. 

## <a name="authorization-request"></a>Engedélyezési kérelem

Az identitás-szolgáltatónak küldött engedélyezési kérelem formátuma a következő:

```HTTP
GET /authorize?client_id=<client-id>&redirect_uri=portableidentity%3A%2F%2Fverify&response_mode=query&response_type=code&scope=openid&state=12345&nonce=12345 HTTP/1.1
Host: www.contoso.com
Connection: Keep-Alive
```

| Paraméter | Érték |
| ------- | ----------- |
| `client_id` | Az alkalmazás regisztrációs folyamata során kapott ügyfél-azonosító. |
| `redirect_uri` | A-t kell használnia `vcclient://openid/` . |
| `response_mode` | Támogatásnak kell lennie `query` . |
| `response_type` | Támogatásnak kell lennie `code` . |
| `scope` | Támogatásnak kell lennie `openid` . |
| `state` | Az OpenID Connect standard szerint vissza kell adni az ügyfélnek. |
| `nonce` | Az ID-tokenben jogcímként kell visszaadni az OpenID Connect standard alapján. |

Amikor engedélyezési kérelmet kap, az identitás-szolgáltatónak hitelesítenie kell a felhasználót, és el kell végeznie a bejelentkezés befejezéséhez szükséges lépéseket, például a többtényezős hitelesítést.

Az igényeinek megfelelően testreszabhatja a bejelentkezési folyamatot. Megkérheti a felhasználókat további információk megadására, a használati feltételek elfogadására, a hitelesítő adatok megfizetésére és egyebekre. Az összes lépés befejezése után válaszoljon az engedélyezési kérelemre úgy, hogy átirányítja az átirányítási URI-ra az alábbi ábrán látható módon. 

```HTTP
vcclient://openid/?code=nbafhjbh1ub1yhbj1h4jr1&state=12345
```

| Paraméter | Érték |
| ------- | ----------- |
| `code` |  Az identitás-szolgáltató által visszaadott engedélyezési kód. |
| `state` | Az OpenID Connect standard szerint vissza kell adni az ügyfélnek. |

## <a name="token-request"></a>Jogkivonat-kérelem

Az identitás-szolgáltatónak elküldett jogkivonat-kérelem a következő formában fog szerepelni.

```HTTP
POST /token HTTP/1.1
Host: www.contoso.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 291

client_id=<client-id>&redirect_uri=vcclient%3A%2F%2Fopenid%2F&grant_type=authorization_code&code=nbafhjbh1ub1yhbj1h4jr1&scope=openid
```

| Paraméter | Érték |
| ------- | ----------- |
| `client_id` | Az alkalmazás regisztrációs folyamata során kapott ügyfél-azonosító. |
| `redirect_uri` | A-t kell használnia `vcclient://openid/` . |
| `scope` | Támogatásnak kell lennie `openid` . |
| `grant_type` | Támogatásnak kell lennie `authorization_code` . |
| `code` | Az identitás-szolgáltató által visszaadott engedélyezési kód. |

A jogkivonat-kérelem kézhezvétele után az identitás-szolgáltatónak egy azonosító jogkivonattal kell válaszolnia.

```HTTP
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store
Pragma: no-cache

{
"id_token": "eyJhbGciOiJSUzI1NiIsImtpZCI6IjFlOWdkazcifQ.ewogImlzc
    yI6ICJodHRwOi8vc2VydmVyLmV4YW1wbGUuY29tIiwKICJzdWIiOiAiMjQ4Mjg5
    NzYxMDAxIiwKICJhdWQiOiAiczZCaGRSa3F0MyIsCiAibm9uY2UiOiAibi0wUzZ
    fV3pBMk1qIiwKICJleHAiOiAxMzExMjgxOTcwLAogImlhdCI6IDEzMTEyODA5Nz
    AKfQ.ggW8hZ1EuVLuxNuuIJKX_V8a_OMXzR0EHR9R6jgdqrOOF4daGU96Sr_P6q
    Jp6IcmD3HP99Obi1PRs-cwh3LO-p146waJ8IhehcwL7F09JdijmBqkvPeB2T9CJ
    NqeGpe-gccMg4vfKjkM8FcGvnzZUN4_KSP0aAp1tOJ1zZwgjxqGByKHiOtX7Tpd
    QyHE5lcMiKPXfEIQILVq0pc_E2DzL7emopWoaoZTF_m0_N0YzFC6g6EJbOEoRoS
    K5hoDalrcvRYLSrQAZZKflyuVCyixEoV9GfNQC3_osjzw2PAithfubEEBLuVVk4
    XUVrWOLrLl0nx7RkKU8NXNHq-rvKMzqg"
}
```

Az azonosító tokennek a JWT Compact szerializálási formátumát kell használnia, és nem lehet titkosított. Az azonosító tokennek tartalmaznia kell a következő jogcímeket.

| Jogcím | Érték |
| ------- | ----------- |
| `kid` | Az azonosító jogkivonat aláírásához használt kulcs azonosítója, amely az OpenID-szolgáltató egyik bejegyzésének felel meg `jwks_uri` . |
| `aud` | Az alkalmazás regisztrációs folyamata során kapott ügyfél-azonosító. |
| `iss` | Az `issuer` OpenID Connect konfigurációs dokumentum értékének kell lennie. |
| `exp` | Tartalmaznia kell az azonosító token lejárati idejét. |
| `iat` | Az azonosító jogkivonat kiállításának időpontját kell tartalmaznia. |
| `nonce` | Az engedélyezési kérelemben szereplő érték. |
| További jogcímek | Az azonosító jogkivonatnak tartalmaznia kell minden olyan további jogcímet, amelynek az értékei szerepelni fognak a kiállított ellenőrizhető hitelesítő adatokban. Ebben a szakaszban szerepelnie kell a felhasználóval kapcsolatos attribútumok, például a nevük. |