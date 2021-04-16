---
title: A Microsoft identitásplatformja és az OAuth2.0-s, a folyamat nevében történő | Azure
titleSuffix: Microsoft identity platform
description: Ez a cikk azt ismerteti, hogyan implementálja a HTTP-üzenetekkel a szolgáltatások és szolgáltatások hitelesítését az OAuth2.0-alapú , nevébenkénti folyamat használatával.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/7/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 1c8ea1580047910cb2d6634aad885d61e99113f3
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529965"
---
# <a name="microsoft-identity-platform-and-oauth-20-on-behalf-of-flow"></a>A Microsoft identitásplatformja és az OAuth 2.0-s verziójának nevében történő folyamat


Az OAuth 2.0-alapú , nevébenkénti folyamat (OBO) azt a alkalmazási esetet szolgálja ki, amikor egy alkalmazás egy szolgáltatást/webes API-t hív meg, amelynek pedig egy másik szolgáltatást/webes API-t kell meghívnia. A cél a delegált felhasználói identitás és engedélyek propagálása a kérésláncon keresztül. Ahhoz, hogy a középső rétegbeli szolgáltatás hitelesített kéréseket nyújtson az lefelé irányuló szolgáltatásnak, biztonságossá kell tennie egy hozzáférési jogkivonatot a Microsoft identitásplatformján a felhasználó nevében.

Ez a cikk azt ismerteti, hogyan programolhat közvetlenül az alkalmazás protokollja szerint.  Ha lehetséges, javasoljuk, hogy a támogatott Microsoft Authentication Libraries (MSAL) használatával szerezzen be jogkivonatokat, és hívja meg a [biztonságos webes API-kat.](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)  Emellett az MSAL-t tartalmazó [mintaalkalmazásokat is nézze meg.](sample-v2-code.md)


2018 májusában néhány implicit folyamat nem használható `id_token` az OBO-folyamathoz. Az egyoldalas alkalmazásoknak (SPA-k) hozzáférési jogkivonatot kell átadniuk egy középső rétegbeli bizalmas ügyfélnek OBO-folyamatok végrehajtásához.  További információ arról, hogy mely ügyfelek hajthat végre OBO-hívásokat: [korlátozások.](#client-limitations)

## <a name="protocol-diagram"></a>Protokolldiagram

Tegyük fel, hogy a felhasználó hitelesítése egy alkalmazásban az [OAuth 2.0](v2-oauth2-auth-code-flow.md) engedélyezési kódengedélyezési folyamat vagy egy másik bejelentkezési folyamat használatával történt. Ezen a ponton az alkalmazás rendelkezik egy hozzáférési jogkivonattal az *A API-hoz (token* A) a felhasználó jogcímével, és hozzájárul a középső rétegbeli webes API (API A) eléréséhez. Az A API-nak most hitelesített kérést kell végeznie az lefelé irányuló webes API-nak (B API).

Az OBO-folyamatot a következő lépések alkotják, és az alábbi diagramon látható.

![Megjeleníti az OAuth2.0-s, nevébenkénti folyamatot](./media/v2-oauth2-on-behalf-of-flow/protocols-oauth-on-behalf-of-flow.png)

1. Az ügyfélalkalmazás az A jogkivonattal (A API jogcímmel) kérést ad az A `aud` API-nak.
1. Az A API hitelesíti magát a Microsoft identitásplatform jogkivonat-kiállítási végpontján, és jogkivonatot kér a B API eléréséhez.
1. A Microsoft identitásplatformjának jogkivonat-kiállítási végpontja ellenőrzi az A API hitelesítő adatait az A jogkivonattal együtt, és ki ad ki hozzáférési jogkivonatot a B API-hoz (b token) az A API-hoz.
1. A B tokent az A API állíthatja be a B API-hoz való kérelem engedélyezési fejlécében.
1. A B API visszaadja a biztonságos erőforrás adatait az A API-nak, majd az ügyfélnek.

Ebben a forgatókönyvben a középső rétegbeli szolgáltatásnak nincs felhasználói beavatkozása ahhoz, hogy a felhasználó beleegyezését adja az lefelé irányuló API-hoz való hozzáféréshez. Ezért az lefelé irányuló API-hoz való hozzáférés megadásának lehetősége a hitelesítés során a hozzájárulási lépés részeként előre jelennek meg. További információ arról, hogyan állíthatja be ezt az alkalmazáshoz: Hozzájárulás a középső rétegbeli [alkalmazáshoz.](#gaining-consent-for-the-middle-tier-application)

## <a name="middle-tier-access-token-request"></a>Középső réteg hozzáférési jogkivonat-kérése

Hozzáférési jogkivonat kéréséhez http POST-et kell elküldenie a bérlőspecifikus Microsoft-identitásplatform jogkivonatvégpontjára az alábbi paraméterekkel.

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

Két eset van attól függően, hogy az ügyfélalkalmazás megosztott titkos okkal vagy tanúsítvánnyal véd.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Első eset: Hozzáférési jogkivonat kérése közös titkos okkal

Közös titkos elérésű titkos elérésű hozzáférési jogkivonat kérése a következő paramétereket tartalmazza:

| Paraméter | Típus | Leírás |
| --- | --- | --- |
| `grant_type` | Kötelező | A jogkivonat-kérelem típusa. JWT-t használó kérések esetén a értéknek a következőnek kell lennie: `urn:ietf:params:oauth:grant-type:jwt-bearer` . |
| `client_id` | Kötelező | Az alkalmazás (ügyfél) azonosítója, Azure Portal [– Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lap van hozzárendelve az alkalmazáshoz. |
| `client_secret` | Kötelező | Az alkalmazáshoz a következő lapon létrehozott titkos Azure Portal – Alkalmazásregisztrációk oldalon. |
| `assertion` | Kötelező | A középső rétegbeli API-nak küldött hozzáférési jogkivonat.  Ennek a jogkivonatnak egy célközönséggel ( ) kell jogcímet igényelnie az OBO-kérést indító alkalmazásra (a mező által `aud` megjegyzett `client-id` alkalmazásra). Az alkalmazások nem tudják beváltani a jogkivonatokat egy másik alkalmazáshoz (például ha egy ügyfél egy MS Graph-hoz szánt jogkivonatot küld egy API-nak, az API nem tudja beváltani az OBO használatával.  Ehelyett el kell utasítania a jogkivonatot).  |
| `scope` | Kötelező | A jogkivonat-kérelem hatókörei szóközrel elválasztott listája. További információ: [hatókörök.](v2-permissions-and-consent.md) |
| `requested_token_use` | Kötelező | A kérés feldolgozásának folyamatát határozza meg. Az OBO-folyamatban az értéket értékre kell `on_behalf_of` állítani. |

#### <a name="example"></a>Példa

Az alábbi HTTP POST hozzáférési jogkivonatot és frissítési jogkivonatot kér a `user.read` https://graph.microsoft.com webes API hatókörével.

```HTTP
//line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com/<tenant>
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer
&client_id=2846f71b-a7a4-4987-bab3-760035b2f389
&client_secret=BYyVnAt56JpLwUcyo47XODd
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiIyO{a lot of characters here}
&scope=https://graph.microsoft.com/user.read+offline_access
&requested_token_use=on_behalf_of
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Második eset: Hozzáférési jogkivonat kérése tanúsítvánnyal

A tanúsítványt tartalmazó szolgáltatások között a hozzáférési jogkivonatra vonatkozó kérések a következő paramétereket tartalmazják:

| Paraméter | Típus | Leírás |
| --- | --- | --- |
| `grant_type` | Kötelező | A jogkivonat-kérelem típusa. JWT-t használó kérések esetén a értéknek értékűnek kell `urn:ietf:params:oauth:grant-type:jwt-bearer` lennie. |
| `client_id` | Kötelező |  Az alkalmazás (ügyfél) azonosítója, Azure Portal [– Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) oldal hozzá van rendelve az alkalmazáshoz. |
| `client_assertion_type` | Kötelező | Az értéknek a következőnek kell `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` lennie: . |
| `client_assertion` | Kötelező | Helyességi feltétel (JSON webes jogkivonat), amely az alkalmazás hitelesítő adataiként regisztrált tanúsítvánnyal hozható létre és írható alá. A tanúsítvány regisztrálásának és a helyességi feltétel formátumának megismerése: [tanúsítvány hitelesítő adatai.](active-directory-certificate-credentials.md) |
| `assertion` | Kötelező |  A középső rétegbeli API-nak küldött hozzáférési jogkivonat.  Ennek a jogkivonatnak egy célközönséggel ( ) kell jogcímet igényelnie az OBO-kérést indító alkalmazástól (az alkalmazást a mező `aud` `client-id` jelöli). Az alkalmazások nem tudják beváltani a jogkivonatokat egy másik alkalmazáshoz (például ha egy ügyfél egy MS Graph-hoz szánt API-jogkivonatot küld, az API nem tudja beváltani az OBO használatával.  Ehelyett el kell utasítania a jogkivonatot).  |
| `requested_token_use` | Kötelező | A kérés feldolgozásának a folyamatát határozza meg. Az OBO-folyamatban az értéket értékre kell `on_behalf_of` állítani. |
| `scope` | Kötelező | A jogkivonat-kérelem hatókörei szóközrel elválasztott listája. További információ: [hatókörök.](v2-permissions-and-consent.md)|

Figyelje meg, hogy a paraméterek szinte azonosak a közös titkos secret kérésével, azzal a kivételsel, hogy a paramétert két paraméter váltja `client_secret` fel: egy `client_assertion_type` és `client_assertion` .

#### <a name="example"></a>Példa

Az alábbi HTTP POST egy hozzáférési jogkivonatot kér a webes API hatókörével `user.read` https://graph.microsoft.com és egy tanúsítvánnyal.

```HTTP
// line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com/<tenant>
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiO{a lot of characters here}
&requested_token_use=on_behalf_of
&scope=https://graph.microsoft.com/user.read+offline_access
```

## <a name="middle-tier-access-token-response"></a>Középső réteg hozzáférési jogkivonatának válasza

A sikeres válasz egy JSON OAuth 2.0-válasz, amely a következő paraméterekkel rendelkezik.

| Paraméter | Leírás |
| --- | --- |
| `token_type` | A jogkivonat típusának értékét jelzi. A Microsoft identitásplatformja csak a típust `Bearer` támogatja: . A bearer tokenekkel kapcsolatos további információkért lásd: [OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750) (OAuth 2.0 engedélyezési keretrendszer: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt)). |
| `scope` | A jogkivonatban megadott hozzáférés hatóköre. |
| `expires_in` | A hozzáférési jogkivonat érvényességének hossza másodpercben. |
| `access_token` | A kért hozzáférési jogkivonat. A hívó szolgáltatás ezzel a jogkivonattal hitelesítheti magát a fogadó szolgáltatásban. |
| `refresh_token` | A kért hozzáférési jogkivonat frissítési jogkivonata. A hívó szolgáltatás ezzel a jogkivonattal kérhet egy másik hozzáférési jogkivonatot az aktuális hozzáférési jogkivonat lejárta után. A frissítési jogkivonat csak akkor lesz megtéve, ha `offline_access` a hatókört kérelmezték. |

### <a name="success-response-example"></a>Példa sikeres válaszra

Az alábbi példa a webes API hozzáférési jogkivonatának kérésére adott sikeres https://graph.microsoft.com választ mutatja be.

```json
{
  "token_type": "Bearer",
  "scope": "https://graph.microsoft.com/user.read",
  "expires_in": 3269,
  "ext_expires_in": 0,
  "access_token": "eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkQ0NDYy0tY0hGa18wZE50MVEtc2loVzRMd2RwQVZISGpnTVdQZ0tQeVJIaGlDbUN2NkdyMEpmYmRfY1RmMUFxU21TcFJkVXVydVJqX3Nqd0JoN211eHlBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMzA1LCJuYmYiOjE0OTM5MzAzMDUsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQU9KYnFFWlRNTnEyZFcxYXpKN1RZMDlYeDdOT29EMkJEUlRWMXJ3b2ZRc1k9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJWR1ItdmtEZlBFQ2M1dWFDaENRSkFBIiwidmVyIjoiMS4wIn0.cubh1L2VtruiiwF8ut1m9uNBmnUJeYx4x0G30F7CqSpzHj1Sv5DCgNZXyUz3pEiz77G8IfOF0_U5A_02k-xzwdYvtJUYGH3bFISzdqymiEGmdfCIRKl9KMeoo2llGv0ScCniIhr2U1yxTIkIpp092xcdaDt-2_2q_ql1Ha_HtjvTV1f9XR3t7_Id9bR5BqwVX5zPO7JMYDVhUZRx08eqZcC-F3wi0xd_5ND_mavMuxe2wrpF-EZviO3yg0QVRr59tE3AoWl8lSGpVc97vvRCnp4WVRk26jJhYXFPsdk4yWqOKZqzr3IFGyD08WizD_vPSrXcCPbZP3XWaoTUKZSNJg",
  "refresh_token": "OAQABAAAAAABnfiG-mA6NTae7CdWW7QfdAALzDWjw6qSn4GUDfxWzJDZ6lk9qRw4An{a lot of characters here}"
}
```

A fenti hozzáférési jogkivonat egy v1.0 formátumú jogkivonat a Microsoft Graph. Ennek az az oka, hogy  a jogkivonat formátuma az elért erőforráson alapul, és nem kapcsolódik a kéréshez használt végponthoz. A Microsoft Graph 1.0-s tokenek elfogadására van beállítva, így a Microsoft identitásplatformja 1.0-s hozzáférési jogkivonatokat állít elő, amikor egy ügyfél jogkivonatokat kér Microsoft Graph. Más alkalmazások jelezhetnek, hogy v2.0 formátumú jogkivonatokat, 1.0-s vagy akár jogvédett vagy titkosított jogkivonat-formátumokat is használni fognak.  Mind az 1.0-s, mind a 2.0-s végpont kibocsáthatja a jogkivonat mindkét formátumát– így az erőforrás mindig a jogkivonat megfelelő formátumát tudja kapni, függetlenül attól, hogy az ügyfél hogyan és hol kérte a jogkivonatot. 

Csak az alkalmazásoknak kell hozzáférési jogkivonatokat keresniük. Az **ügyfelek nem vizsgálhatja** meg őket. Ha megvizsgálja a kódban más alkalmazások hozzáférési jogkivonatait, az alkalmazás váratlan leállást fog eredményezni, amikor az alkalmazás módosítja a jogkivonatok formátumát, vagy megkezdi a titkosításukat. 

### <a name="error-response-example"></a>Példa hibaválaszra

A tokenvégpont hibaüzenetet ad vissza, amikor hozzáférési jogkivonatot próbál szerezni az lefelé irányuló [API-hoz,](../authentication/concept-mfa-howitworks.md)ha az lefelé irányuló API feltételes hozzáférési szabályzattal (például többtényezős hitelesítéssel) rendelkezik. A középső rétegbeli szolgáltatásnak ezt a hibát az ügyfélalkalmazásnak kell kihat adnia, hogy az ügyfélalkalmazás biztosítani tudja a felhasználói beavatkozást a feltételes hozzáférési szabályzatnak való megfelelőség érdekében.

```json
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## <a name="use-the-access-token-to-access-the-secured-resource"></a>A biztonságos erőforrás elérése a hozzáférési jogkivonattal

A középső rétegbeli szolgáltatás most már használhatja a fent beszerzett jogkivonatot a hitelesített kérések igényléséhez az lefelé irányuló webes API-hoz a token fejlécben való `Authorization` beállításával.

### <a name="example"></a>Példa

```HTTP
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiO ... 0X2tnSQLEANnSPHY0gKcgw
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>OAuth2.0 OBO-folyamatokkal kapott SAML-helyességi feltétel

Egyes OAuth-alapú webszolgáltatásoknak más webszolgáltatás API-khoz kell hozzáférniük, amelyek elfogadják az SAML-helyességi feltételt a nem interaktív folyamatokban. Azure Active Directory SAML-helyességi feltételt tud biztosítani egy olyan, az SAML-alapú webszolgáltatást célerőforrásként használó, nevében alapuló folyamatra válaszul.

Ez az OAuth 2.0-alapú, nevébenkénti folyamat nem szabványos bővítménye, amely lehetővé teszi, hogy egy OAuth2-alapú alkalmazás hozzáférjen az SAML-jogkivonatokat használó webszolgáltatás API-végpontjaihoz.

> [!TIP]
> Amikor egy SAML-védelemmel védett webszolgáltatást hív meg egy előoldali webalkalmazásból, egyszerűen meg kell hívnia az API-t, és normál interaktív hitelesítési folyamatot kezdeményezhet a felhasználó meglévő munkamenete segítségével. OBO-folyamatot csak akkor kell használnia, ha egy szolgáltatásokhoz való hívás SAML-jogkivonatot igényel a felhasználói környezethez.
 
 ### <a name="obtain-a-saml-token-by-using-an-obo-request-with-a-shared-secret"></a>SAML-jogkivonat beszerzése közös titkos okkal és OBO-kéréssel

Az SAML helyességi feltételre vonatkozó szolgáltatásokra vonatkozó kérések a következő paramétereket tartalmazják:

| Paraméter | Típus | Leírás |
| --- | --- | --- |
| grant_type |kötelező | A jogkivonat-kérelem típusa. JWT-t használó kérések esetén az értéknek **urn:ietf:params:oauth:grant-type:jwt-bearer** értékűnek kell lennie. |
| Állítás |kötelező | A kérésben használt hozzáférési jogkivonat értéke.|
| client_id |kötelező | A hívó szolgáltatáshoz az Azure AD-regisztráció során hozzárendelt alkalmazásazonosító. Az alkalmazásazonosító megkereséhez a Azure Portal **válassza** a Active Directory a könyvtárat, majd válassza ki az alkalmazás nevét. |
| client_secret |kötelező | A hívó szolgáltatáshoz az Azure AD-ban regisztrált kulcs. Ezt az értéket fel kell jegyezni a regisztrációkor. |
| Erőforrás |kötelező | A fogadó szolgáltatás (biztonságos erőforrás) alkalmazásazonosító URI-ja. Ez az erőforrás lesz az SAML-jogkivonat célközönsége. Az alkalmazásazonosító URI megkereséhez a Azure Portal válassza **Active Directory** lehetőséget, és válassza ki a címtárat. Válassza ki az alkalmazás nevét, válassza a **Minden beállítás,** majd a Tulajdonságok **lehetőséget.** |
| requested_token_use |kötelező | A kérés feldolgozásának a folyamatát határozza meg. A "Nevében" folyamatban az értéknek a következőnek **kell on_behalf_of.** |
| requested_token_type | kötelező | Megadja a kért jogkivonat típusát. Az érték lehet **urn:ietf:params:oauth:token-type:saml2** vagy **urn:ietf:params:oauth:token-type:saml1** az elért erőforrás követelményeitől függően. |

A válasz egy UTF8 és Base64url kódolású SAML-jogkivonatot tartalmaz.

- **SubjectConfirmationData** egy OBO-hívásból származó SAML helyességi feltételhez: Ha a célalkalmazásnak címzett értéket kell tartalmaznie a **SubjectConfirmationData** fájlban, akkor az értéknek nem helyettesítő karakteres válasz URL-címnek kell lennie az erőforrás-alkalmazás konfigurációjában.
- **SubjectConfirmationData** csomópont: A csomópont nem tartalmazhat **InResponseTo** attribútumot, mivel nem része az SAML-válasznak. Az SAML-jogkivonatot fogadó alkalmazásnak képesnek kell lennie fogadni az SAML helyességi feltételt **inResponseTo attribútum** nélkül.

- **Hozzájárulás:** Jóváhagyást kell adni az OAuth-folyamat felhasználói adatait tartalmazó SAML-jogkivonat fogadására. Az engedélyekkel és a rendszergazdai jóváhagyás megszerzésével kapcsolatos információkért lásd az engedélyeket és hozzájárulásokat [Azure Active Directory 1.0-s Azure Active Directory végpontban.](https://docs.microsoft.com/azure/active-directory/azuread-dev/v1-permissions-consent)

### <a name="response-with-saml-assertion"></a>Válasz SAML helyességi feltételsel

| Paraméter | Leírás |
| --- | --- |
| token_type |A jogkivonat típusának értékét jelzi. Az Azure AD csak a **Bearer típust támogatja.** A bearer tokenekkel kapcsolatos további információkért lásd: [OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750) ( OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt)). |
| scope |A jogkivonatban megadott hozzáférés hatóköre. |
| expires_in |A hozzáférési jogkivonat érvényességének hossza (másodpercben). |
| expires_on |A hozzáférési jogkivonat lejáratának ideje. A dátum az 1970-01-01T0:0:0Z UTC és a lejárati idő között másodpercek számaként van ábrázolva. Ez az érték határozza meg a gyorsítótárazott jogkivonatok élettartamát. |
| Erőforrás |A fogadó szolgáltatás alkalmazásazonosító URI-ját (biztonságos erőforrás). |
| access_token |Az SAML helyességi feltételt visszaadó paraméter. |
| refresh_token |A frissítési jogkivonat. A hívó szolgáltatás ezzel a jogkivonattal kérhet egy másik hozzáférési jogkivonatot az aktuális SAML-helyességi feltétel lejárta után. |

- token_type: Bearer
- expires_in: 3296
- ext_expires_in: 0
- expires_on: 1529627844
- Erőforrás: `https://api.contoso.com`
- access_token: \<SAML assertion\>
- issued_token_type: urn:ietf:params:oauth:token-type:saml2
- refresh_token: \<Refresh token\>


## <a name="gaining-consent-for-the-middle-tier-application"></a>Hozzájárulás a középső rétegbeli alkalmazáshoz

Az alkalmazás architektúrától vagy használatától függően különböző stratégiákat érdemes megfontolni az OBO-folyamat sikeres voltának biztosításához. A végső cél minden esetben a megfelelő hozzájárulás biztosítása, hogy az ügyfélalkalmazás meg tudja hívni a középső rétegbeli alkalmazást, a középső rétegbeli alkalmazás pedig engedélyt kap a háttérerőforrás hívására.

> [!NOTE]
> Korábban a Microsoft-fiók (személyes fiókok) nem támogatták az "Ismert ügyfélalkalmazás" mezőt, és nem is lehetett kombinált jóváhagyást mutatni.  Ez hozzá lett adva, és a Microsoft identitásplatformján minden alkalmazás használhatja az ismert ügyfélalkalmazás-megközelítést az OBO-hívásokhoz való hozzájáruláshoz.

### <a name="default-and-combined-consent"></a>/.default és a kombinált hozzájárulás

A középső rétegbeli alkalmazás hozzáadja az ügyfelet az ismert ügyfélalkalmazások listájához a jegyzékfájlban, majd az ügyfél aktiválhat egy összevont jóváhagyási folyamatot saját maga és a középső rétegbeli alkalmazás számára is. A Microsoft identitásplatformján ez a hatókör használatával [ `/.default` történik.](v2-permissions-and-consent.md#the-default-scope) Ha ismert ügyfélalkalmazások és használatával aktivál egy jóváhagyási képernyőt, a hozzájárulási képernyő az ügyfélnek a középső réteg API-hoz való engedélyeinek megjelenítése mellett a középső réteg API-ja által megkövetelt engedélyeket is `/.default` kérni fogja.  A felhasználó mindkét alkalmazáshoz jóváhagyást ad, majd az OBO-folyamat működik.

### <a name="pre-authorized-applications"></a>Előre engedélyezett alkalmazások

Az erőforrások jelezheti, hogy egy adott alkalmazás mindig rendelkezik engedéllyel bizonyos hatókörök fogadásához. Ez elsősorban akkor hasznos, ha zökkenőmentesebb kapcsolatot létesít egy előoldali ügyfél és egy háttérerőforrás között. Egy erőforrás több előre engedélyezett alkalmazást is deklarálhat – egy ilyen alkalmazás kérheti ezeket az engedélyeket egy OBO-folyamaton belül, és anélkül fogadhatja azokat, hogy a felhasználó beleegyezést ad.

### <a name="admin-consent"></a>Rendszergazdai jóváhagyás

A bérlői rendszergazda a középső rétegbeli alkalmazás rendszergazdai jóváhagyásával garantálhatja, hogy az alkalmazásoknak van engedélyük a szükséges API-k meghívására. Ehhez a rendszergazda megkeresheti a középső rétegbeli alkalmazást a bérlőben, megnyithatja a szükséges engedélyeket, és dönthet úgy, hogy engedélyt ad az alkalmazásnak. A rendszergazdai jóváhagyással kapcsolatos további információkért tekintse meg a [hozzájárulási és engedélyekkel kapcsolatos dokumentációt.](v2-permissions-and-consent.md)

### <a name="use-of-a-single-application"></a>Egyetlen alkalmazás használata

Bizonyos esetekben előfordulhat, hogy csak egyetlen párja van a középső rétegbeli és az előoldali ügyfélnek. Ebben a forgatókönyvben egyszerűbb lehet egyetlen alkalmazást, így nincs szükség egy középső rétegbeli alkalmazásra. Az előtere és a webes API közötti hitelesítéshez használhatja a cookie-kat, id_token vagy magának az alkalmazásnak a hozzáférési jogkivonatát. Ezután kérjen jóváhagyást ettől az alkalmazástól a háttérerőforráshoz.

## <a name="client-limitations"></a>Ügyfélkorlátozások

Ha egy ügyfél az implicit folyamat használatával szerez be egy id_token- és az ügyfél helyettesítő karaktereket is tartalmaz egy válasz URL-címben, az id_token nem használható OBO-folyamathoz.  Az implicit engedélyfolyamon keresztül beszerzett hozzáférési jogkivonatokat azonban továbbra is beválthatja egy bizalmas ügyfél, még akkor is, ha az kezdeményező ügyfél rendelkezik regisztrált helyettesítő karakteres válasz URL-lel.

## <a name="next-steps"></a>Következő lépések

Tudjon meg többet az OAuth 2.0 protokollról és az ügyfél-hitelesítő adatokkal történő szolgáltatás-szolgáltatási hitelesítés egyéb módjairól.

* [OAuth 2.0-ügyfél hitelesítő adatainak megadása a Microsoft identitásplatformján](v2-oauth2-client-creds-grant-flow.md)
* [OAuth 2.0-kódfolyam a Microsoft identitásplatformján](v2-oauth2-auth-code-flow.md)
* [A hatókör `/.default` használata](v2-permissions-and-consent.md#the-default-scope)
