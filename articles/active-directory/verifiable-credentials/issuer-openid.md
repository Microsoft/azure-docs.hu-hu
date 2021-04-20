---
title: Példák kiállító szolgáltatáskommunikációra (előzetes verzió) – Azure Active Directory hitelesítő adatok megjelenítése
description: Az identitásszolgáltató és a kiállító szolgáltatás közötti kommunikáció részletei
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.workload: identity
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 942b77f8338636f9dda5dcf6cd4262dad57b4b0a
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726267"
---
# <a name="issuer-service-communication-examples-preview"></a>Példák kiállító szolgáltatáskommunikációra (előzetes verzió)

Az Azure AD ellenőrizhető hitelesítőadat-szolgáltatása igazolható hitelesítő adatokat tud kiadni a szervezet OpenID-kompatibilis identitásszolgáltatója által létrehozott azonosító jogkivonat jogcímek leolvasásával. Ez a cikk bemutatja, hogyan állíthatja be az identitásszolgáltatót úgy, hogy az Authenticator kommunikáljon vele, és lekérje a megfelelő azonosító jogkivonatot, hogy átadja azt a kiállító szolgáltatásnak. 

> [!IMPORTANT]
> Azure Active Directory hitelesítő adatok ellenőrizhetővé tehetők jelenleg nyilvános előzetes verzióban.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Az Ellenőrizhető hitelesítő adatok kiállításához az Authenticator a szerződés letöltésével arra utasítja, hogy gyűjtsön adatokat a felhasználótól, és küldje el az adatokat a kiállító szolgáltatásnak. Ha azonosító jogkivonatot kell használnia, úgy kell beállítania az identitásszolgáltatót, hogy az Authenticator bejelentkeztetsen egy felhasználót a OpenID Connect protokoll használatával. Az eredményül kapott azonosító jogkivonatban a jogcímek az ellenőrizhető hitelesítő adatok tartalmának feltöltéséhez használatosak. Az Authenticator az engedélyezési kódfolyam OpenID Connect hitelesíti a felhasználót. Az OpenID-szolgáltatónak támogatnia kell a következő OpenID Connect funkciókat: 

| Szolgáltatás | Leírás |
| ------- | ----------- |
| Engedély típusa | Támogatnia kell az engedélyezési kód engedélyezési típusát. |
| Jogkivonat formátuma | Titkosítatlan, kompakt JWT-ket kell előállítania. |
| Aláírási algoritmus | RSA 256 használatával aláírt JWT-ket kell előállítania. |
| Konfigurációs dokumentum | Támogatnia kell OpenID Connect konfigurációs dokumentumát és `jwks_uri` a et. | 
| Ügyfélregisztráció | Támogatnia kell a nyilvános ügyfélregisztrációt a `redirect_uri` érték `vclient://openid/` használatával. | 
| PKCE | Biztonsági okokból ajánlott, de nem kötelező. |

Az alábbiakban példákat talál az identitásszolgáltatónak küldött HTTP-kérelmekre. Az identitásszolgáltatónak el kell fogadnia ezeket a kéréseket, és válaszolnia kell ezekre a OpenID Connect szabványnak megfelelően.

## <a name="client-registration"></a>Ügyfélregisztráció

Az ellenőrizhető hitelesítő adatok fogadása érdekében a felhasználóknak be kell jelentkeznie az internetszolgáltatóba az Microsoft Authenticator alkalmazásból. 

Az adatcsere engedélyezéséhez regisztráljon egy alkalmazást az identitásszolgáltatónál. Ha az Azure AD-t használja, itt találja az [utasításokat.](../develop/quickstart-register-app.md) A regisztrációhoz használja a következő értékeket.

| Beállítás | Érték |
| ------- | ----- |
| Alkalmazásnév | `<Issuer Name> Verifiable Credential Service` |
| Átirányítási URI | `vcclient://openid/ ` |


Miután regisztrált egy alkalmazást az identitásszolgáltatónál, jegyezte fel annak ügyfél-azonosítóját. Ezt a következő szakaszban fogja használni. Le kell írnia az OIDC-kompatibilis identitásszolgáltató jól ismert végpontjára mutató URL-címet is. A kiállító szolgáltatás ezt a végpontot használja az azonosító jogkivonat érvényesítéséhez szükséges nyilvános kulcsok letöltéséhez, miután az Authenticator elküldte azt.

Az Authenticator a konfigurált átirányítási URI-t használja, így tudja, mikor fejeződött be a bejelentkezés, és le tudjakérni az azonosító jogkivonatot. 

## <a name="authorization-request"></a>Engedélyezési kérelem

Az identitásszolgáltatónak küldött engedélyezési kérelem formátuma a következő.

```HTTP
GET /authorize?client_id=<client-id>&redirect_uri=portableidentity%3A%2F%2Fverify&response_mode=query&response_type=code&scope=openid&state=12345&nonce=12345 HTTP/1.1
Host: www.contoso.com
Connection: Keep-Alive
```

| Paraméter | Érték |
| ------- | ----------- |
| `client_id` | Az alkalmazásregisztrációs folyamat során lekért ügyfél-azonosító. |
| `redirect_uri` | A-t kell `vcclient://openid/` használnia. |
| `response_mode` | Támogatnia kell a `query` et. |
| `response_type` | Támogatnia kell a `code` et. |
| `scope` | Támogatnia kell a `openid` et. |
| `state` | Az ügyfélnek a következő szabványnak megfelelően kell OpenID Connect vissza. |
| `nonce` | Jogcímként kell visszaadni az azonosító jogkivonatban a OpenID Connect szerint. |

Amikor megkapja az engedélyezési kérelmet, az identitásszolgáltatónak hitelesítenie kell a felhasználót, és meg kell tennie a bejelentkezéshez szükséges lépéseket, például a többtényezős hitelesítést.

A bejelentkezési folyamatot igény szerint testreszabhatja. Megkérhet felhasználókat, hogy adjanak meg további információkat, fogadjanak el szolgáltatási feltételeket, fizessen a hitelesítő adataikért stb. Ha minden lépés befejeződött, válaszoljon az engedélyezési kérésre az átirányítási URI-hoz való átirányítással, az alább látható módon. 

```HTTP
vcclient://openid/?code=nbafhjbh1ub1yhbj1h4jr1&state=12345
```

| Paraméter | Érték |
| ------- | ----------- |
| `code` |  Az identitásszolgáltató által visszaadott engedélyezési kód. |
| `state` | Az ügyfélnek a következő szabványnak megfelelően kell OpenID Connect vissza. |

## <a name="token-request"></a>Jogkivonat-kérelem

Az identitásszolgáltatónak küldött jogkivonat-kérelem a következő űrlapot fogja tartalmazza.

```HTTP
POST /token HTTP/1.1
Host: www.contoso.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 291

client_id=<client-id>&redirect_uri=vcclient%3A%2F%2Fopenid%2F&grant_type=authorization_code&code=nbafhjbh1ub1yhbj1h4jr1&scope=openid
```

| Paraméter | Érték |
| ------- | ----------- |
| `client_id` | Az alkalmazásregisztrációs folyamat során lekért ügyfél-azonosító. |
| `redirect_uri` | A-t kell `vcclient://openid/` használnia. |
| `scope` | Támogatnia kell a `openid` et. |
| `grant_type` | Támogatnia kell a `authorization_code` et. |
| `code` | Az identitásszolgáltató által visszaadott engedélyezési kód. |

A jogkivonat-kérelem fogadása után az identitásszolgáltatónak egy azonosító jogkivonattal kell válaszolnia.

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

Az azonosító jogkivonatnak a JWT kompakt szerializálási formátumot kell használnia, és nem lehet titkosítva. Az azonosító jogkivonatnak a következő jogcímeket kell tartalmaznia.

| Jogcím | Érték |
| ------- | ----------- |
| `kid` | Az azonosító jogkivonatának aláíráshoz használt kulcs azonosítója, amely megfelel az OpenID-szolgáltató `jwks_uri` bejegyzésének. |
| `aud` | Az alkalmazásregisztrációs folyamat során lekért ügyfél-azonosító. |
| `iss` | A konfigurációs `issuer` dokumentumban a OpenID Connect értéknek kell lennie. |
| `exp` | Tartalmaznia kell az azonosító jogkivonat lejárati idejét. |
| `iat` | Tartalmaznia kell az azonosító jogkivonat kibocsátásának idejét. |
| `nonce` | Az engedélyezési kérelemben szereplő érték. |
| További jogcímek | Az azonosító jogkivonatnak tartalmaznia kell minden olyan további jogcímet, amelynek az értékei szerepelni fognak az ellenőrizhető hitelesítő adatokban, amelyek ki lesznek bocsátva. Ebben a szakaszban meg kell adnunk a felhasználóval kapcsolatos attribútumokat, például a nevüket. |

## <a name="next-steps"></a>Következő lépések

- [A hitelesítő adatok Azure Active Directory testreszabása](credential-design.md)
