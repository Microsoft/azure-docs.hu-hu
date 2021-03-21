---
title: Microsoft Identity platform és OAuth 2.0-alapú meghatalmazott folyamat | Azure
titleSuffix: Microsoft identity platform
description: Ez a cikk azt ismerteti, hogyan használhatók a HTTP-üzenetek a szolgáltatás és a szolgáltatás hitelesítésének megvalósításához a OAuth 2.0-s verziójának használatával.
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
ms.openlocfilehash: ff8e03b813e2cb890192667e3466d920eaabc72c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98756085"
---
# <a name="microsoft-identity-platform-and-oauth-20-on-behalf-of-flow"></a>Microsoft Identity platform és OAuth 2,0-alapú folyamat


Az OAuth 2,0-es adatforgalom (OBO) arra szolgál, hogy az alkalmazás hogyan hívja meg a szolgáltatás/webes API-t, ami viszont egy másik szolgáltatást vagy webes API-t hív meg. Az a cél, hogy a delegált felhasználói identitást és engedélyeket a kérési láncon keresztül propagálja. Ahhoz, hogy a középső szintű szolgáltatás hitelesített kéréseket továbbítson az alárendelt szolgáltatásnak, a felhasználó nevében védenie kell egy hozzáférési jogkivonatot a Microsoft Identity platformon.

Ez a cikk azt ismerteti, hogyan lehet programozni közvetlenül az alkalmazás protokollját.  Ha lehetséges, javasoljuk, hogy a támogatott Microsoft hitelesítési kódtárakat (MSAL) használja a [jogkivonatok beszerzése és a biztonságos webes API-k hívása](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)helyett.  Tekintse meg az MSAL-t [használó példákat](sample-v2-code.md)is.


Előfordulhat, hogy a 2018-es számú implicit folyamat `id_token` nem használható az OBO-flow-hoz. Az egyoldalas alkalmazások (Gyógyfürdők) **hozzáférési** jogkivonatot továbbítanak egy közepes szintű bizalmas ügyfélnek az OBO-folyamatok elvégzéséhez. További információ arról, hogy mely ügyfelek végezhetnek OBO-hívásokat: [korlátozások](#client-limitations).

## <a name="protocol-diagram"></a>Protokoll diagramja

Tegyük fel, hogy a felhasználó hitelesítése megtörtént egy olyan alkalmazáson, amely a [OAuth 2,0 engedélyező kódot](v2-oauth2-auth-code-flow.md) vagy egy másik bejelentkezési folyamatot használ. Ezen a ponton az alkalmazás rendelkezik egy hozzáférési jogkivonattal *az API* -hoz (A token a) a felhasználó jogcímeivel és a középső rétegbeli webes API (a API) eléréséhez szükséges engedélyekkel. Most az API-nak hitelesített kérést kell tennie az alárendelt webes API-nak (B API).

A követendő lépések az OBO-folyamatot alkotják, és az alábbi ábra segítségével magyarázható.

![A OAuth 2.0-alapú meghatalmazott folyamat megjelenítése](./media/v2-oauth2-on-behalf-of-flow/protocols-oauth-on-behalf-of-flow.png)

1. Az ügyfélalkalmazás kérelmet küld az a API-nak az a jogkivonat (az a `aud` API-val rendelkező jogcím) használatával.
1. Az API A hitelesíti a Microsoft Identity platform jogkivonat-kiállítási végpontját, és tokent kér a B API eléréséhez.
1. A Microsoft Identity platform token kiállítási végpontja ellenőrzi az API A hitelesítő adatait az A jogkivonattal együtt, és kiadja a B API (token B) hozzáférési jogkivonatát az A API-nak.
1. A "B" tokent az API-nak a B API-ra irányuló kérelem engedélyezési fejlécében a következő értékre állítja be.
1. A biztonságos erőforrásból származó adatok a B API-val, az API-hoz, majd az ügyfélhez kerülnek vissza.

Ebben az esetben a középső rétegbeli szolgáltatásnak nincs felhasználói beavatkozása ahhoz, hogy a felhasználó beleegyezett az alsóbb rétegbeli API eléréséhez. Ezért az alsóbb rétegbeli API-hoz való hozzáférés engedélyezésének lehetősége előzetesen megjelenik a jóváhagyás lépés részeként a hitelesítés során. Ha meg szeretné tudni, hogyan állíthatja be ezt az alkalmazásra, tekintse meg [a a középső rétegbeli alkalmazáshoz](#gaining-consent-for-the-middle-tier-application)való hozzájárulások beszerzése című témakört.

## <a name="middle-tier-access-token-request"></a>Közepes szintű hozzáférési jogkivonat kérése

Hozzáférési jogkivonat igényléséhez a következő paraméterekkel hozzon végre egy HTTP-BEJEGYZÉST a bérlő-specifikus Microsoft Identity platform token-végponton.

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

Két eset attól függően, hogy az ügyfélalkalmazás egy megosztott titok vagy egy tanúsítvány által védett-e.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Első eset: hozzáférési jogkivonat-kérelem közös titokkal

Közös titkos kulcs használata esetén a szolgáltatás-szolgáltatás hozzáférési jogkivonat-kérelem a következő paramétereket tartalmazza:

| Paraméter | Típus | Leírás |
| --- | --- | --- |
| `grant_type` | Kötelező | A jogkivonat-kérelem típusa. JWT használó kérelmek esetén az értéknek a következőnek kell lennie: `urn:ietf:params:oauth:grant-type:jwt-bearer` . |
| `client_id` | Kötelező | Az alkalmazás (ügyfél) azonosítója, amelyhez [az Azure Portal-Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lap hozzá van rendelve az alkalmazáshoz. |
| `client_secret` | Kötelező | Az Azure Portal-Alkalmazásregisztrációk lapon az alkalmazáshoz generált ügyfél-titkos kulcs. |
| `assertion` | Kötelező | A középső rétegbeli API-nak eljuttatott hozzáférési jogkivonat.  Ennek a jogkivonatnak rendelkeznie kell egy `aud` , az adott OBO-kérelmet készítő alkalmazás célközönségével () `client-id` . Az alkalmazások nem válthatnak ki jogkivonatot egy másik alkalmazás számára (például ha egy ügyfél az MS Graph-hoz készült API-tokent küld, az API nem tudja beváltani az OBO használatával.  Ehelyett a tokent el kell utasítania.  |
| `scope` | Kötelező | A jogkivonat-kérelem hatókörének szóközzel tagolt listája. További információ: [hatókörök](v2-permissions-and-consent.md). |
| `requested_token_use` | Kötelező | Megadja a kérelem feldolgozásának módját. Az OBO-flow-ban az értéket a értékre kell beállítani `on_behalf_of` . |

#### <a name="example"></a>Példa

A következő HTTP POST hozzáférési jogkivonatot és frissítési jogkivonatot kér `user.read` a https://graph.microsoft.com webes API hatókörével.

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

### <a name="second-case-access-token-request-with-a-certificate"></a>Második eset: hozzáférési jogkivonat kérése tanúsítvánnyal

Egy tanúsítványhoz tartozó szolgáltatás-szolgáltatás hozzáférési jogkivonat-kérelem a következő paramétereket tartalmazza:

| Paraméter | Típus | Leírás |
| --- | --- | --- |
| `grant_type` | Kötelező | A jogkivonat-kérelem típusa. JWT használó kérelmek esetén az értéknek a következőnek kell lennie: `urn:ietf:params:oauth:grant-type:jwt-bearer` . |
| `client_id` | Kötelező |  Az alkalmazás (ügyfél) azonosítója, amelyhez [az Azure Portal-Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lap hozzá van rendelve az alkalmazáshoz. |
| `client_assertion_type` | Kötelező | Az értéknek a számnak kell lennie `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` . |
| `client_assertion` | Kötelező | Egy, az alkalmazáshoz hitelesítő adatként regisztrált tanúsítvánnyal rendelkező (JSON webes jogkivonat). A tanúsítvány regisztrálásának és az állítás formátumának megismeréséhez lásd: [tanúsítvány hitelesítő adatai](active-directory-certificate-credentials.md). |
| `assertion` | Kötelező |  A középső rétegbeli API-nak eljuttatott hozzáférési jogkivonat.  Ennek a jogkivonatnak rendelkeznie kell egy `aud` , az adott OBO-kérelmet készítő alkalmazás célközönségével () `client-id` . Az alkalmazások nem válthatnak ki jogkivonatot egy másik alkalmazás számára (például ha egy ügyfél az MS Graph-hoz készült API-tokent küld, az API nem tudja beváltani az OBO használatával.  Ehelyett a tokent el kell utasítania.  |
| `requested_token_use` | Kötelező | Megadja a kérelem feldolgozásának módját. Az OBO-flow-ban az értéket a értékre kell beállítani `on_behalf_of` . |
| `scope` | Kötelező | A jogkivonat-kérelem hatókörének szóközzel tagolt listája. További információ: [hatókörök](v2-permissions-and-consent.md).|

Figyelje meg, hogy a paraméterek majdnem ugyanazok, mint a megosztott titkos kérelem esetében, kivéve, ha a `client_secret` paramétert két paraméter helyettesíti: a `client_assertion_type` és `client_assertion` .

#### <a name="example"></a>Példa

A következő HTTP-bejegyzés olyan hozzáférési jogkivonatot kér, amely `user.read` hatókörrel rendelkezik a https://graph.microsoft.com webes API-hoz egy tanúsítvánnyal.

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

## <a name="middle-tier-access-token-response"></a>Közepes szintű hozzáférési jogkivonat válasza

A sikeres válasz egy JSON-OAuth 2,0-válasz a következő paraméterekkel.

| Paraméter | Leírás |
| --- | --- |
| `token_type` | Megadja a jogkivonat típusának értékét. Az egyetlen típus, amelyet a Microsoft Identity platform támogat `Bearer` . A tulajdonosi jogkivonatokkal kapcsolatos további információkért tekintse meg a [OAuth 2,0 engedélyezési keretrendszert: tulajdonosi jogkivonat használata (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| `scope` | A jogkivonatban megadott hozzáférési hatókör. |
| `expires_in` | Az az időtartam (másodpercben), ameddig a hozzáférési jogkivonat érvényes. |
| `access_token` | A kért hozzáférési jogkivonat. A hívó szolgáltatás ezt a tokent használhatja a fogadó szolgáltatásban való hitelesítéshez. |
| `refresh_token` | A kért hozzáférési jogkivonat frissítési jogkivonata. A hívó szolgáltatás ezzel a jogkivonattal kérhet egy másik hozzáférési tokent az aktuális hozzáférési jogkivonat lejárta után. A frissítési token csak akkor van megadva, ha a `offline_access` hatókört kérték. |

### <a name="success-response-example"></a>Sikeres válasz – példa

Az alábbi példa egy, a webes API hozzáférési jogkivonatára vonatkozó kérelemre adott sikeres választ mutat be https://graph.microsoft.com .

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

A fenti hozzáférési jogkivonat a Microsoft Graph 1.0-s verziójának formátuma. Ennek az az oka, hogy a jogkivonat formátuma az elérni kívánt **erőforráson** alapul, és nem kapcsolódik a kérelemhez használt végpontokhoz. A Microsoft Graph a telepítője a v 1.0 tokenek elfogadására, így a Microsoft Identity platform 1.0-s verziójú hozzáférési jogkivonatokat hoz létre, amikor az ügyfél a Microsoft Graph jogkivonatait kéri le. Más alkalmazások azt jelezhetik, hogy a 2.0 formátumú jogkivonatok, a 1.0 formátumú tokenek, vagy akár a tulajdonosi, akár a titkosított jogkivonat-formátumok is megadhatók.  A 1.0-s és a v 2.0-s végpontok egyaránt rendelkezhetnek tokenek formátumával – így az erőforrás mindig a token megfelelő formátumát tudja lekérdezni, függetlenül attól, hogy az ügyfél milyen módon vagy hol kérte a jogkivonatot. 

Csak az alkalmazásoknak kell megkeresniük a hozzáférési jogkivonatokat. Az ügyfeleknek **nem kell** megvizsgálniuk azokat. A kód más alkalmazásaihoz tartozó hozzáférési jogkivonatok vizsgálatakor az alkalmazás váratlanul leáll, amikor az alkalmazás megváltoztatja a tokenek formátumát, vagy megkezdi a titkosítást. 

### <a name="error-response-example"></a>Hiba-válasz példa

A jogkivonat-végpont egy hibaüzenetet ad vissza, amikor hozzáférési tokent próbál beszerezni az alsóbb rétegbeli API számára, ha az alsóbb rétegbeli API feltételes hozzáférési szabályzattal (például [többtényezős hitelesítéssel](../authentication/concept-mfa-howitworks.md)) van beállítva. A középső rétegbeli szolgáltatásnak ezt a hibát fel kell vennie az ügyfélalkalmazás számára, hogy az ügyfélalkalmazás meg tudja adni a felhasználói beavatkozást a feltételes hozzáférési szabályzat teljesítése érdekében.

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

## <a name="use-the-access-token-to-access-the-secured-resource"></a>A biztonságos erőforrás eléréséhez használja a hozzáférési jogkivonatot.

Most a középső rétegbeli szolgáltatás a fentiekben beszerzett token használatával hitelesített kéréseket végez az alárendelt webes API-nak, a tokent a `Authorization` fejlécben állíthatja be.

### <a name="example"></a>Példa

```HTTP
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiO ... 0X2tnSQLEANnSPHY0gKcgw
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>OAuth 2.0 OBO-flow-val kapott SAML-kijelentések

Néhány OAuth-alapú webszolgáltatásnak hozzá kell férnie más webszolgáltatási API-khoz, amelyek nem interaktív folyamatokban fogadják el az SAML-kijelentéseket. A Azure Active Directory az SAML-alapú webszolgáltatás forrásként való megadására irányuló, az általa létrehozott folyamatra adott válaszként SAML-jogcímet adhat meg.

Ez egy nem szabványos bővítmény a OAuth 2,0-hez, amely lehetővé teszi, hogy egy OAuth2-alapú alkalmazás hozzáférhessen az SAML-jogkivonatokat használó webszolgáltatás API-végpontokhoz.

> [!TIP]
> Ha SAML-védelemmel ellátott webszolgáltatást hív meg egy előtér-webalkalmazásból, egyszerűen hívja meg az API-t, és kezdeményezzen egy normál, interaktív hitelesítési folyamatot a felhasználó meglévő munkamenetével. Csak egy OBO-folyamatot kell használnia, ha egy szolgáltatás-szolgáltatás hívásához SAML-jogkivonat szükséges a felhasználói környezet biztosításához.

## <a name="gaining-consent-for-the-middle-tier-application"></a>Beleegyezik a középső rétegbeli alkalmazásra

Az alkalmazás architektúrájának vagy használati módjától függően különböző stratégiákat tekinthet meg az OBO-folyamat sikerességének biztosításához. Minden esetben a végső cél a megfelelő hozzájárulás biztosítása, hogy az ügyfélalkalmazás meghívja a középső rétegbeli alkalmazást, és a középső rétegbeli alkalmazás jogosult legyen a háttérbeli erőforrás meghívására.

> [!NOTE]
> Korábban a Microsoft-fiók rendszer (személyes fiókok) nem támogatta az "ismert ügyfélalkalmazás" mezőt, és nem tudta megjeleníteni a kombinált hozzájárulásukat.  Ez hozzá lett adva, és a Microsoft Identity platformon futó összes alkalmazás használhatja az ismert ügyfélalkalmazás megközelítését az OBO-hívások beszerzéséhez.

### <a name="default-and-combined-consent"></a>/.default és kombinált engedély

A középső rétegbeli alkalmazás hozzáadja az ügyfelet az ismert ügyfélalkalmazások listájához a jegyzékfájljában, majd az ügyfél egyszerre több és a középső rétegbeli alkalmazáshoz is elindíthat egy kombinált engedélyezési folyamatot. A Microsoft Identity platformon ez a [ `/.default` hatókör](v2-permissions-and-consent.md#the-default-scope)használatával végezhető el. Ha ismert ügyfélalkalmazások használatával aktivál egy beleegyezési képernyőt `/.default` , a beleegyezési képernyő a középső  rétegbeli API számára is megjeleníti az ügyfélre vonatkozó engedélyeket, valamint a középső rétegbeli API számára szükséges engedélyeket is. A felhasználó mindkét alkalmazáshoz hozzájárul, majd az OBO-folyamat működik.

### <a name="pre-authorized-applications"></a>Előzetesen jóváhagyott alkalmazások

Az erőforrások azt jelezhetik, hogy egy adott alkalmazásnak mindig van engedélye bizonyos hatókörök fogadására. Ez elsősorban akkor hasznos, ha az előtér-ügyfél és a háttérbeli erőforrás közötti kapcsolat zökkenőmentesebb. Egy erőforrás több előre engedélyezett alkalmazást is deklarálhat – bármely ilyen alkalmazás kérheti ezeket az engedélyeket egy OBO-folyamatba, és a felhasználó belefoglalása nélkül fogadhatja azokat.

### <a name="admin-consent"></a>Rendszergazdai jóváhagyás

A bérlői rendszergazda garantálhatja, hogy az alkalmazások jogosultak legyenek a szükséges API-k meghívására a középső szintű alkalmazás rendszergazdai beleegyezésének biztosításával. Ehhez a rendszergazda megkeresheti a középső rétegű alkalmazást a bérlőben, megnyithatja a szükséges engedélyek lapot, és engedélyt adhat az alkalmazás számára. Ha többet szeretne megtudni a rendszergazdai jogosultságokról, tekintse meg a [beleegyezett és az engedélyek dokumentációját](v2-permissions-and-consent.md).

### <a name="use-of-a-single-application"></a>Egyetlen alkalmazás használata

Bizonyos helyzetekben csak a középső rétegbeli és az előtér-ügyfél egyetlen párosítása lehet. Ebben a forgatókönyvben könnyebben lehet ezt egy alkalmazást létrehozni, és nem kell megtagadni a középső rétegbeli alkalmazás szükségességét. Az előtér-és a webes API-k közötti hitelesítéshez használhat cookie-kat, id_token vagy az alkalmazáshoz igényelt hozzáférési tokent. Ezt követően a kérelem beleegyezik az adott alkalmazástól a háttér-erőforráshoz.

## <a name="client-limitations"></a>Ügyfél korlátozásai

Ha az ügyfél az implicit folyamattal id_token kap, és az ügyfél a válasz URL-címében helyettesítő karaktereket is tartalmaz, akkor a id_token nem használható OBO-folyamathoz.  Az implicit engedélyezési folyamaton keresztül beszerzett hozzáférési tokenek azonban továbbra is beválthatók egy bizalmas ügyfél számára, még akkor is, ha a kezdeményező ügyfélhez a helyettesítő karakteres válasz URL-címe van regisztrálva.

## <a name="next-steps"></a>Következő lépések

További információ a OAuth 2,0 protokollról, valamint a szolgáltatás és a szolgáltatás hitelesítésének másik módja ügyfél-hitelesítő adatok használatával.

* [OAuth 2,0 ügyfél-hitelesítő adatok megadása a Microsoft Identity platformon](v2-oauth2-client-creds-grant-flow.md)
* [OAuth 2,0-programkód a Microsoft Identity platformon](v2-oauth2-auth-code-flow.md)
* [A `/.default` hatókör használata](v2-permissions-and-consent.md#the-default-scope)
