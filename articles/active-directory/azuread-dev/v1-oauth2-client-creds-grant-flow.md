---
title: Az Azure AD szolgáltatás és szolgáltatás közötti hitelesítés a OAuth 2.0 használatával | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használhatók a HTTP-üzenetek a szolgáltatás és szolgáltatás hitelesítésének megvalósításához a OAuth 2.0-ügyfél hitelesítő adatainak megadásakor.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: ryanwi
ms.reviewer: nacanuma
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 977dfea28c5c0dc3f34ada0c138556d70c979e04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85551703"
---
# <a name="service-to-service-calls-using-client-credentials-shared-secret-or-certificate"></a>Szolgáltatások közötti hívások ügyfél-hitelesítő adatokkal (közös titok vagy tanúsítvány)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

A OAuth 2,0 ügyfél-hitelesítő adatok engedélyezési folyamata engedélyezi egy webszolgáltatás (*bizalmas ügyfél*) számára a saját hitelesítő adatainak használatát a felhasználó megszemélyesítése helyett, a hitelesítéshez egy másik webszolgáltatás hívásakor. Ebben az esetben az ügyfél általában egy közepes szintű webszolgáltatás, egy démon-szolgáltatás vagy egy webhely. A magasabb szintű megbízhatóság érdekében az Azure AD azt is lehetővé teszi, hogy a hívó szolgáltatás hitelesítő adatként használjon tanúsítványokat (közös titok helyett).

## <a name="client-credentials-grant-flow-diagram"></a>Ügyfél-hitelesítő adatok megadása folyamat diagramja
Az alábbi ábra azt mutatja be, hogy az ügyfél hitelesítő adatainak megadása hogyan működik a Azure Active Directory (Azure AD) szolgáltatásban.

![OAuth 2.0-ügyfél hitelesítő adatainak megadása](./media/v1-oauth2-client-creds-grant-flow/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. Az ügyfélalkalmazás hitelesíti magát az Azure AD jogkivonat-kiállítási végponton, és hozzáférési jogkivonatot kér.
2. Az Azure AD-jogkivonat kiállítási végpontja kiadja a hozzáférési jogkivonatot.
3. A hozzáférési jogkivonat a biztonságos erőforráshoz való hitelesítésre szolgál.
4. A biztonságos erőforrásból származó adatok az ügyfélalkalmazás számára kerülnek vissza.

## <a name="register-the-services-in-azure-ad"></a>Szolgáltatások regisztrálása az Azure AD-ben
Regisztrálja mind a hívó szolgáltatást, mind a fogadó szolgáltatást Azure Active Directoryban (Azure AD). Részletes útmutatásért lásd: [alkalmazások integrálása a Azure Active Directorysal](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="request-an-access-token"></a>Hozzáférési jogkivonat igénylése
Hozzáférési jogkivonat igényléséhez használjon HTTP-BEJEGYZÉST a bérlő-specifikus Azure AD-végpontra.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>Szolgáltatás-szolgáltatás hozzáférési jogkivonat kérése
Két eset attól függően, hogy az ügyfélalkalmazás egy közös titok vagy egy tanúsítvány biztonságossá tételére van-e kiválasztva.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Első eset: hozzáférési jogkivonat-kérelem közös titokkal
Közös titkos kulcs használata esetén a szolgáltatás-szolgáltatás hozzáférési jogkivonat-kérelem a következő paramétereket tartalmazza:

| Paraméter | Típus | Leírás |
| --- | --- | --- |
| grant_type |kötelező |Megadja a kért engedélyezési típust. Az ügyfél hitelesítő adatainak megadása esetén a **client_credentials** értéket kell megadni. |
| client_id |kötelező |Megadja a hívó webszolgáltatás Azure AD-ügyfél-azonosítóját. A hívó alkalmazás ügyfél-AZONOSÍTÓjának megkereséséhez kattintson a [Azure Portal](https://portal.azure.com) **Azure Active Directory**, majd a **Alkalmazásregisztrációk** elemre, majd az alkalmazásra. A client_id az *alkalmazás azonosítója* |
| client_secret |kötelező |Adja meg az Azure AD-ben a hívó webszolgáltatás vagy démon alkalmazáshoz regisztrált kulcsot. Kulcs létrehozásához kattintson a Azure Portal **Azure Active Directory**, majd a **Alkalmazásregisztrációk** elemre, kattintson az alkalmazásra, kattintson a **Beállítások**, majd a **kulcsok** elemre, és adjon hozzá egy kulcsot.  URL – ezt a titkot kódolja a megadásakor. |
| erőforrás |kötelező |Adja meg a fogadó webszolgáltatás alkalmazás-azonosító URI azonosítóját. Az alkalmazás-azonosító URI azonosítójának megkereséséhez kattintson a Azure Portal **Azure Active Directory**, majd a **Alkalmazásregisztrációk** lehetőségre, majd a szolgáltatásalkalmazás elemre, végül a **Beállítások** és **Tulajdonságok** elemre. |

#### <a name="example"></a>Példa
A következő HTTP-bejegyzés egy [hozzáférési jogkivonatot](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) kér a `https://service.contoso.com/` webszolgáltatáshoz. Az `client_id` azonosítja azt a webszolgáltatást, amely a hozzáférési jogkivonatot kéri.

```
POST /contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Második eset: hozzáférési jogkivonat kérése tanúsítvánnyal
Egy tanúsítványhoz tartozó szolgáltatás-szolgáltatás hozzáférési jogkivonat-kérelem a következő paramétereket tartalmazza:

| Paraméter | Típus | Leírás |
| --- | --- | --- |
| grant_type |kötelező |A kért válasz típusát adja meg. Az ügyfél hitelesítő adatainak megadása esetén a **client_credentials** értéket kell megadni. |
| client_id |kötelező |Megadja a hívó webszolgáltatás Azure AD-ügyfél-azonosítóját. A hívó alkalmazás ügyfél-AZONOSÍTÓjának megkereséséhez kattintson a [Azure Portal](https://portal.azure.com) **Azure Active Directory**, majd a **Alkalmazásregisztrációk** elemre, majd az alkalmazásra. A client_id az *alkalmazás azonosítója* |
| client_assertion_type |kötelező |Az értéknek meg kell felelnie `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |kötelező | Az alkalmazáshoz hitelesítő adatként regisztrált tanúsítvány létrehozásához és aláírásához szükséges állítás (JSON Web Token). Tudnivalók a [tanúsítvány hitelesítő adatairól](../develop/active-directory-certificate-credentials.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) : a tanúsítvány regisztrálásának és az állítás formátumának megismerése.|
| erőforrás | kötelező |Adja meg a fogadó webszolgáltatás alkalmazás-azonosító URI azonosítóját. Az alkalmazás-azonosító URI azonosítójának megkereséséhez kattintson a Azure Portal **Azure Active Directory**, majd a **Alkalmazásregisztrációk** lehetőségre, majd a szolgáltatásalkalmazás elemre, végül a **Beállítások** és **Tulajdonságok** elemre. |

Figyelje meg, hogy a paraméterek majdnem ugyanazok, mint a közös titok által benyújtott kérelem esetében, kivéve, ha a client_secret paramétert két paraméter helyettesíti: egy client_assertion_type és client_assertion.

#### <a name="example"></a>Példa
A következő HTTP POST hozzáférési jogkivonatot kér a `https://service.contoso.com/` webszolgáltatáshoz egy tanúsítvánnyal. Az `client_id` azonosítja azt a webszolgáltatást, amely a hozzáférési jogkivonatot kéri.

```
POST /<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

resource=https%3A%2F%contoso.onmicrosoft.com%2Ffc7664b4-cdd6-43e1-9365-c2e1c4e1b3bf&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

### <a name="service-to-service-access-token-response"></a>A szolgáltatás és a szolgáltatás közötti hozzáférési jogkivonat válasza

A sikeres válasz egy JSON OAuth 2,0 választ tartalmaz, amely a következő paraméterekkel rendelkezik:

| Paraméter | Leírás |
| --- | --- |
| access_token |A kért hozzáférési jogkivonat. A hívó webszolgáltatás ezt a tokent használhatja a fogadó webszolgáltatáshoz való hitelesítéshez. |
| token_type |Megadja a jogkivonat típusának értékét. Az Azure AD által támogatott egyetlen típus a **tulajdonos**. A tulajdonosi jogkivonatokkal kapcsolatos további információkért tekintse meg a [OAuth 2,0 engedélyezési keretrendszert: tulajdonosi jogkivonat használata (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| expires_in |A hozzáférési jogkivonat érvényességi ideje (másodpercben). |
| expires_on |A hozzáférési jogkivonat lejáratának időpontja. A dátum az 1970-01-01T0:0: 0Z UTC számú másodperc, a lejárati időpontig. Ez az érték a gyorsítótárazott tokenek élettartamának meghatározására szolgál. |
| not_before |Az az idő, ameddig a hozzáférési jogkivonat használhatóvá válik. A dátum a (z) 1970-01-01T0:0: 0Z UTC számú másodpercnek felel meg a jogkivonat érvényességi idejéig.|
| erőforrás |A fogadó webszolgáltatás alkalmazás-azonosító URI-ja. |

#### <a name="example-of-response"></a>Példa a válaszra
A következő példa egy webszolgáltatáshoz való hozzáférési jogkivonatra vonatkozó kérelem sikerességét mutatja be.

```
{
"access_token":"eyJ0eXAiO ... 0X2tnSQLEANnSPHY0gKcgw",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```
## <a name="use-the-access-token-to-access-the-secured-resource"></a>A biztonságos erőforrás eléréséhez használja a hozzáférési jogkivonatot.

A szolgáltatás a beszerzett hozzáférési jogkivonattal hitelesített kéréseket végez az alárendelt webes API-hoz, ha a tokent a `Authorization` fejlécben állítja be.

### <a name="example"></a>Példa

```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiO ... 0X2tnSQLEANnSPHY0gKcgw
```

## <a name="see-also"></a>Lásd még
* [OAuth 2,0 az Azure AD-ben](v1-protocols-oauth-code.md)
* Minta a szolgáltatás és szolgáltatás közötti [híváshoz egy közös titokkal](https://github.com/Azure-Samples/active-directory-dotnet-daemon) , és [a szolgáltatás és a szolgáltatás közötti hívás a tanúsítvánnyal](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)
