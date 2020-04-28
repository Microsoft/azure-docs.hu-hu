---
title: REST-technikai profil definiálása egyéni házirendben
titleSuffix: Azure AD B2C
description: Megadhat egy REST-es technikai profilt egy egyéni szabályzatban Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 410f413fc8450c0ee33c3ca95e860a3e8de34107
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80332612"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>REST-technikai profil definiálása egy Azure Active Directory B2C egyéni házirendben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A Azure Active Directory B2C (Azure AD B2C) támogatást nyújt a saját REST-szolgáltatás integrálásához. Azure AD B2C adatokat küld a REST-szolgáltatásnak a bemeneti jogcímek gyűjteményében, és visszafogadja az adatokat a kimeneti jogcímek gyűjteményében. További információ: az [REST API jogcím-cserék integrálása az Azure ad B2C egyéni házirendbe](custom-policy-rest-api-intro.md).  

## <a name="protocol"></a>Protocol (Protokoll)

A **protokoll** elem `Proprietary` **Name** attribútumát be kell állítani. A **kezelő** attribútumnak tartalmaznia kell a Azure ad B2C által használt protokollkezelő-szerelvény teljes nevét: `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

Az alábbi példa egy REST-technikai profilt mutat be:

```XML
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="input-claims"></a>Bemeneti jogcímek

A **szabályzattípushoz** elem tartalmazza a REST API küldendő jogcímek listáját. A jogcím nevét a REST APIban definiált névre is leképezheti. A következő példa a házirend és a REST API közötti leképezést mutatja be. A rendszer a **givenName** -jogcímet **firstName**-ként elküldi a REST APInak, míg a **vezetéknevet** **utónévként**küldjük el. Az **e-mail-** jogcím a következőképpen van beállítva:.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="email" />
  <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
  <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
</InputClaims>
```

A **InputClaimsTransformations** elem olyan **InputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja, amelyek a bemeneti jogcímek módosítására vagy újak előállítására szolgálnak a REST API való elküldés előtt.

## <a name="send-a-json-payload"></a>JSON-adattartalom küldése

A REST API technikai profil lehetővé teszi, hogy összetett JSON-adattartalmat küldjön egy végpontnak.

Összetett JSON-adattartalom küldése:

1. Hozzon létre JSON-adattartalmat a [GenerateJson](json-transformations.md) jogcím-átalakításával.
1. A REST API technikai profilban:
    1. Adjon hozzá egy bemeneti jogcím-átalakítást a `GenerateJson` jogcím-átalakításra mutató hivatkozással.
    1. A `SendClaimsIn` metaadatok beállításának beállítása`body`
    1. Állítsa be `ClaimUsedForRequestPayload` a metaadatok beállítást a JSON-adattartalomot tartalmazó jogcím nevére.
    1. A bemeneti jogcímben adjon hozzá egy hivatkozást a JSON-adattartalomot tartalmazó bemeneti jogcímhez.

Az alábbi példa `TechnicalProfile` egy ellenőrző e-mailt küld egy harmadik féltől származó e-mail-szolgáltatással (ebben az esetben a SendGrid).

```XML
<TechnicalProfile Id="SendGrid">
  <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridApiKey" />
  </CryptographicKeys>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="GenerateSendGridRequestBody" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="sendGridReqBody" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="output-claims"></a>Kimeneti jogcímek

A **OutputClaims** elem a REST API által visszaadott jogcímek listáját tartalmazza. Előfordulhat, hogy le kell képeznie a szabályzatban definiált jogcím nevét a REST APIban definiált névre. Olyan jogcímeket is tartalmazhat, amelyeket nem ad vissza a REST API Identity Provider, ha beállítja az `DefaultValue` attribútumot.

A **OutputClaimsTransformations** elem olyan **OutputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja, amelyek a kimeneti jogcímek módosítására vagy újak előállítására szolgálnak.

A következő példa a REST API által visszaadott jogcímet mutatja be:

- A **loyaltyNumber** jogcím nevére leképezett **MembershipId** jogcím.

A technikai profil a jogcímeket is visszaadja, amelyeket az identitás-szolgáltató nem ad vissza:

- A **loyaltyNumberIsNew** jogcím, amelyhez alapértelmezett érték van beállítva `true`.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>Metaadatok

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ServiceUrl | Igen | Az REST API végpont URL-címe |
| AuthenticationType | Igen | A REST-alapú jogcím-szolgáltató által végrehajtott hitelesítés típusa. Lehetséges értékek: `None`, `Basic` `Bearer`,, vagy `ClientCertificate`. Az `None` érték azt jelzi, hogy a REST API nem anonim. Az `Basic` érték azt jelzi, hogy a REST API a http alapszintű hitelesítéssel védett. Csak ellenőrzött felhasználók férhetnek hozzá az API-hoz, beleértve a Azure AD B2C is. A `ClientCertificate` (javasolt) érték azt jelzi, hogy a REST API ügyféltanúsítvány-alapú hitelesítés használatával korlátozza a hozzáférést. Csak a megfelelő tanúsítvánnyal rendelkező szolgáltatások férhetnek hozzá az API-hoz (például Azure AD B2C). Az `Bearer` érték azt jelzi, hogy a REST API az ügyfél OAuth2 tulajdonosi jogkivonatát használva korlátozza a hozzáférést. |
| AllowInsecureAuthInProduction| Nem| Azt jelzi `Production`, `AuthenticationType` hogy a beállítható `none` -e éles környezetben`DeploymentMode` (a [TrustFrameworkPolicy](trustframeworkpolicy.md) beállítása, vagy nincs megadva). Lehetséges értékek: true vagy FALSE (alapértelmezett). |
| SendClaimsIn | Nem | Megadja, hogy a rendszer hogyan küldje el a bemeneti jogcímeket a REST-jogcím-szolgáltatónak. Lehetséges értékek: `Body` (alapértelmezett), `Form`, `Header`, vagy `QueryString`. Az `Body` érték a kérelem törzsében a JSON formátumban elküldett bemeneti jogcím. Az `Form` érték az a bemeneti jogcím, amelyet a rendszer a kérés törzsében küld el egy "&" tagolt kulcs értékének formátumában. Az `Header` érték a kérelem fejlécében elküldhető bemeneti jogcím. Az `QueryString` érték a kérelem lekérdezési karakterláncában elküldhető bemeneti jogcím. Az egyesek által meghívott HTTP-műveletek a következők:<br /><ul><li>`Body`: POST</li><li>`Form`: POST</li><li>`Header`: GET</li><li>`QueryString`: GET</li></ul> |
| ClaimsFormat | Nem | Jelenleg nincs használatban, figyelmen kívül hagyható. |
| ClaimUsedForRequestPayload| Nem | Egy olyan karakterlánc-jogcím neve, amely a REST API küldendő adattartalmat tartalmazza. |
| DebugMode | Nem | A technikai profilt hibakeresési módban futtatja. Lehetséges értékek: `true`, vagy `false` (alapértelmezett). Hibakeresési módban a REST API további információkat adhat vissza. Lásd a [visszatérési hibaüzenet](#returning-error-message) szakaszt. |
| IncludeClaimResolvingInClaimsHandling  | Nem | A bemeneti és a kimeneti jogcímek esetén megadja, hogy a [jogcímek feloldása](claim-resolver-overview.md) szerepel-e a technikai profilban. Lehetséges értékek: `true`, vagy `false`  (alapértelmezett). Ha a technikai profilban a jogcím-feloldót szeretné használni, állítsa be a következőt: `true`. |
| ResolveJsonPathsInJsonTokens  | Nem | Azt jelzi, hogy a technikai profil feloldja-e a JSON-útvonalakat. Lehetséges értékek: `true`, vagy `false` (alapértelmezett). A metaadatok használatával beolvashatja az adatokat egy beágyazott JSON-elemből. A [OutputClaim](technicalprofiles.md#outputclaims)állítsa be a `PartnerClaimType` elemet a kimenetként használni kívánt JSON-útvonal elemre. Például: `firstName.localized`, vagy `data.0.to.0.email`.|
| UseClaimAsBearerToken| Nem| A tulajdonosi jogkivonatot tartalmazó jogcím neve.|

## <a name="cryptographic-keys"></a>Titkosítási kulcsok

Ha a hitelesítés típusa értéke `None`, a rendszer nem használja a **CryptographicKeys** elemet.

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
</TechnicalProfile>
```

Ha a hitelesítés típusa értékre van állítva `Basic`, a **CryptographicKeys** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| BasicAuthenticationUsername | Igen | A hitelesítéshez használt Felhasználónév. |
| BasicAuthenticationPassword | Igen | A hitelesítéshez használt jelszó. |

Az alábbi példa egy egyszerű hitelesítéssel rendelkező technikai profilt mutat be:

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Basic</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
  </CryptographicKeys>
</TechnicalProfile>
```

Ha a hitelesítés típusa értékre van állítva `ClientCertificate`, a **CryptographicKeys** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ClientCertificate | Igen | A hitelesítéshez használandó X509-tanúsítvány (RSA-kulcs). |

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">ClientCertificate</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
  </CryptographicKeys>
</TechnicalProfile>
```

Ha a hitelesítés típusa értékre van állítva `Bearer`, a **CryptographicKeys** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| BearerAuthenticationToken | Nem | A OAuth 2,0 tulajdonosi jogkivonata. |

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_B2cRestClientAccessToken" />
  </CryptographicKeys>
</TechnicalProfile>
```

## <a name="returning-error-message"></a>Hibaüzenet küldése

Előfordulhat, hogy a REST API hibaüzenetet kell visszaadnia, például "a felhasználó nem található a CRM rendszerben". Ha hiba történik, a REST API egy HTTP-4xx hibaüzenetet ad vissza, például: 400 (hibás kérelem) vagy 409 (ütközés) Response állapotkód. A válasz törzse a JSON-ban formázott hibaüzenetet tartalmaz:

```JSON
{
  "version": "1.0.0",
  "status": 409,
  "code": "API12345",
  "requestId": "50f0bd91-2ff4-4b8f-828f-00f170519ddb",
  "userMessage": "Message for the user",
  "developerMessage": "Verbose description of problem and how to fix it.",
  "moreInfo": "https://restapi/error/API12345/moreinfo"
}
```

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| version | Igen | Az REST API verziója. Például: 1.0.1 |
| status | Igen | 409-nek kell lennie |
| code | Nem | A REST-végpont szolgáltatójának hibakódja, amely akkor jelenik meg, `DebugMode` ha az engedélyezve van. |
| Kérelemazonosító | Nem | A REST végpont-szolgáltatótól származó kérelem azonosítója, amely akkor jelenik meg `DebugMode` , ha az engedélyezve van. |
| userMessage | Igen | Egy hibaüzenet jelenik meg, amely megjelenik a felhasználó számára. |
| developerMessage | Nem | A probléma részletes leírása és a kijavítása, amely akkor jelenik meg, ha `DebugMode` az engedélyezve van. |
| moreInfo | Nem | Olyan URI, amely további információra mutat, amely akkor jelenik `DebugMode` meg, ha az engedélyezve van. |


Az alábbi példa egy C# osztályt mutat be, amely egy hibaüzenetet ad vissza:

```csharp
public class ResponseContent
{
  public string version { get; set; }
  public int status { get; set; }
  public string code { get; set; }
  public string userMessage { get; set; }
  public string developerMessage { get; set; }
  public string requestId { get; set; }
  public string moreInfo { get; set; }
}
```

## <a name="next-steps"></a>További lépések

Tekintse meg a következő cikkeket a REST-technikai profil használatának példáit illetően:

- [REST API jogcímek cseréjének integrálása a Azure AD B2C egyéni szabályzatba](custom-policy-rest-api-intro.md)
- [Bemutató: REST API jogcím-cserék integrálása a Azure AD B2C felhasználói úton a felhasználói bevitel ellenőrzésekor](custom-policy-rest-api-claims-validation.md)
- [Forgatókönyv: REST API-jogcímek hozzáadása egyéni házirendekhez Azure Active Directory B2C](custom-policy-rest-api-claims-validation.md)
- [A REST API szolgáltatásainak védelme](secure-rest-api.md)

