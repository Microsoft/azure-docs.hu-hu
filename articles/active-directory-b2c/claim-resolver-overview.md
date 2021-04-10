---
title: Igénylések feloldói az egyéni házirendekben
titleSuffix: Azure AD B2C
description: Ismerje meg, hogyan használhatók a jogcímek feloldói a Azure Active Directory B2C egyéni házirendjében.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 929463e346311d58da5ac75b28c774bcfe258f08
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557530"
---
# <a name="about-claim-resolvers-in-azure-active-directory-b2c-custom-policies"></a>Tudnivalók a jogcímek feloldóinak Azure Active Directory B2C egyéni házirendekben

A Azure Active Directory B2C (Azure AD B2C) [Egyéni szabályzatok](custom-policy-overview.md) által felismert feloldók az engedélyezési kérelemre vonatkozó környezeti információkat biztosítanak, például a szabályzat nevét, a kérelem KORRELÁCIÓs azonosítóját, a felhasználói felület nyelvét és egyebeket.

Ha a jogcím-feloldót egy bemeneti vagy kimeneti jogcímben szeretné használni, Definiáljon egy karakterlánc- **claimType** a [ClaimsSchema](claimsschema.md) elem alatt, majd állítsa be a **DefaultValue** -feloldót a bemeneti vagy kimeneti jogcím elemben. Azure AD B2C beolvassa a jogcím-feloldó értékét, és a technikai profilban szereplő értéket használja.

A következő példában egy nevű jogcím-típus `correlationId` van definiálva egy **adattípussal** `string` .

```xml
<ClaimType Id="correlationId">
  <DisplayName>correlationId</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Request correlation Id</UserHelpText>
</ClaimType>
```

A technikai profilban rendelje hozzá a jogcím feloldóját a jogcím típusához. Azure AD B2C feltölti a jogcím feloldójának értékét `{Context:CorrelationId}` a jogcímbe `correlationId` , és elküldi a jogcímet a technikai profilnak.

```xml
<InputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
```

## <a name="claim-resolver-types"></a>Jogcím-feloldási típusok

A következő fejezetei az elérhető jogcímek feloldóit sorolja fel.

### <a name="culture"></a>Kultúra

| Jogcím | Leírás | Példa |
| ----- | ----------- | --------|
| {Kulturális környezet: LanguageName} | A nyelv két betűs ISO-kódja. | hu |
| {Kulturális környezet: LCID}   | A nyelvi kód LCID-je. | 1038 |
| {Kulturális környezet: RegionName} | A régió két betűs ISO-kódja. | USA |
| {Kulturális környezet: RFC5646} | A RFC5646 nyelvének kódja | en-US |

### <a name="policy"></a>Szabályzat

| Jogcím | Leírás | Példa |
| ----- | ----------- | --------|
| {Policy: PolicyId} | A függő entitás házirendjének neve. | B2C_1A_signup_signin |
| {Policy: RelyingPartyTenantId} | A függő entitás házirendjének bérlői azonosítója. | your-tenant.onmicrosoft.com |
| {Policy: TenantObjectId} | A függő entitás házirendjének bérlői objektumának azonosítója. | 00000000-0000-0000-0000-000000000000 |
| {Policy: TrustFrameworkTenantId} | A megbízhatósági keretrendszer bérlői azonosítója. | your-tenant.onmicrosoft.com |

### <a name="openid-connect"></a>OpenID Connect

| Jogcím | Leírás | Példa |
| ----- | ----------- | --------|
| {OIDC: AuthenticationContextReferences} |A `acr_values` lekérdezési karakterlánc paraméter. | N/A |
| {OIDC: ClientId} |A `client_id`  lekérdezési karakterlánc paraméter. | 00000000-0000-0000-0000-000000000000 |
| {OIDC: DomainHint} |A `domain_hint`  lekérdezési karakterlánc paraméter. | facebook.com |
| {OIDC: LoginHint} |  A `login_hint` lekérdezési karakterlánc paraméter. | someone@contoso.com |
| {OIDC: MaxAge} | `max_age`. | N/A |
| {OIDC: alkalom} |A `Nonce`  lekérdezési karakterlánc paraméter. | defaultNonce |
| {OIDC: Password}| Az [erőforrás-tulajdonos jelszava hitelesíti](add-ropc-policy.md) a felhasználói jelszót.| jelszó1| 
| {OIDC: prompt} | A `prompt` lekérdezési karakterlánc paraméter. | bejelentkezés |
| {OIDC: RedirectUri} |A `redirect_uri`  lekérdezési karakterlánc paraméter. | https://jwt.ms |
| {OIDC: erőforrás} |A `resource`  lekérdezési karakterlánc paraméter. | N/A |
| {OIDC: hatókör} |A `scope`  lekérdezési karakterlánc paraméter. | OpenID |
| {OIDC: username}| Az [erőforrás-tulajdonos jelszavának hitelesítő adatai folyamat](add-ropc-policy.md) felhasználójának felhasználóneve.| emily@contoso.com| 

### <a name="context"></a>Környezet

| Jogcím | Leírás | Példa |
| ----- | ----------- | --------|
| {Context: BuildNumber} | Az Identity Experience Framework verziószáma (Build száma).  | 1.0.507.0 |
| {Context: CorrelationId} | A korrelációs azonosító.  | 00000000-0000-0000-0000-000000000000 |
| {Context: DateTimeInUtc} |A dátum és idő (UTC).  | 10/10/2018 12:00:00 PM |
| {Context: Deploymentmode beállítása} |A házirend-telepítési mód.  | Production |
| {Context: állomásnév} | Az aktuális kérelem állomásneve.  | contoso.b2clogin.com |
| {Context: Ip_cím} | A felhasználó IP-címe. | 11.111.111.11 |
| {Context: KMSI} | Azt jelzi, hogy be van-e jelölve [a bejelentkezve marad](session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) jelölőnégyzet. |  true |

### <a name="claims"></a>Jogcímek 

| Jogcím | Leírás | Példa |
| ----- | ----------- | --------|
| {Jogcím: jogcím típusa} | Egy jogcím-típus azonosítója már definiálva van a ClaimsSchema szakaszban a házirend fájl vagy a szülő házirend fájljában.  Például: `{Claim:displayName}` , vagy `{Claim:objectId}` . | Jogcím típusú érték.|


### <a name="oauth2-key-value-parameters"></a>OAuth2 kulcs-érték paraméterek

Egy OIDC-vagy OAuth2-kérelem részeként megadott paraméter neve a felhasználói úton lévő jogcímek számára képezhető le. Előfordulhat például, hogy az alkalmazástól érkező kérelem tartalmaz egy lekérdezési karakterlánc paramétert, amelynek a neve, `app_session` `loyalty_number` vagy bármely egyéni lekérdezési karakterlánc.

| Jogcím | Leírás | Példa |
| ----- | ----------------------- | --------|
| {OAUTH-KV: campaignId} | Egy lekérdezési karakterlánc paraméter. | Hawaii |
| {OAUTH-KV: app_session} | Egy lekérdezési karakterlánc paraméter. | A3C5R |
| {OAUTH-KV: loyalty_number} | Egy lekérdezési karakterlánc paraméter. | 1234 |
| {OAUTH-KV: bármely egyéni lekérdezési karakterlánc} | Egy lekérdezési karakterlánc paraméter. | N/A |

### <a name="oauth2"></a>OAuth2

| Jogcím | Leírás | Példa |
| ----- | ----------------------- | --------|
| {oauth2: access_token} | A hozzáférési jogkivonat. | N/A |
| {oauth2: refresh_token} | A frissítési jogkivonat. | N/A |


### <a name="saml"></a>SAML

| Jogcím | Leírás | Példa |
| ----- | ----------- | --------|
| {SAML: AuthnContextClassReferences} | Az `AuthnContextClassRef` elem értéke az SAML-kérelemből. | urn: Oasis: nevek: TC: SAML: 2.0: AC: osztályok: PasswordProtectedTransport |
| {SAML: NameIdPolicyFormat} | Az `Format` attribútum az SAML- `NameIDPolicy` kérelem eleme alapján. | urn: Oasis: nevek: TC: SAML: 1.1: nameid-Format: emailAddress |
| {SAML: kiállító} |  Az SAML-kérelem SAML- `Issuer` elemének értéke.| `https://contoso.com` |
| {SAML: AllowCreate} | Az `AllowCreate` attribútum értéke az SAML- `NameIDPolicy` kérelem eleme alapján. | Igaz |
| {SAML: ForceAuthn} | Az `ForceAuthN` attribútum értéke az SAML- `AuthnRequest` kérelem eleme alapján. | Igaz |
| {SAML: ProviderName} | Az `ProviderName` attribútum értéke az SAML- `AuthnRequest` kérelem eleme alapján.| Contoso.com |
| {SAML: RelayState} | A `RelayState` lekérdezési karakterlánc paraméter.| 
| {SAML: tárgy} | Az `Subject` SAML-AuthN kérelem NameId eleme.| 

## <a name="using-claim-resolvers"></a>Jogcímek feloldóinak használata

A jogcímek feloldóit a következő elemekkel használhatja:

| Elem | Elem | Beállítások |
| ----- | ----------------------- | --------|
|Application Insights műszaki profil |`InputClaim` | |
|[Azure Active Directory](active-directory-technical-profile.md) műszaki profil| `InputClaim`, `OutputClaim`| 1, 2|
|[OAuth2](oauth2-technical-profile.md) műszaki profil| `InputClaim`, `OutputClaim`| 1, 2|
|[OpenID Connect](openid-connect-technical-profile.md) – technikai profil| `InputClaim`, `OutputClaim`| 1, 2|
|[Jogcím-átalakítási](claims-transformation-technical-profile.md) technikai profil| `InputClaim`, `OutputClaim`| 1, 2|
|[Rest-szolgáltató](restful-technical-profile.md) technikai profilja| `InputClaim`| 1, 2|
|[SAML-identitás szolgáltatójának](identity-provider-generic-saml.md)  műszaki profilja| `OutputClaim`| 1, 2|
|[Önérvényesített](self-asserted-technical-profile.md) technikai profil| `InputClaim`, `OutputClaim`| 1, 2|
|[ContentDefinition](contentdefinitions.md)| `LoadUri`| |
|[ContentDefinitionParameters](relyingparty.md#contentdefinitionparameters)| `Parameter` | |
|[RelyingParty](relyingparty.md#technicalprofile) műszaki profil| `OutputClaim`| 2 |

Beállítások
1. A metaadatokat a következőre kell `IncludeClaimResolvingInClaimsHandling` beállítani: `true` .
1. A bemeneti vagy kimeneti jogcímek attribútumát `AlwaysUseDefaultValue` be kell állítani `true` .

## <a name="claim-resolvers-samples"></a>A jogcím-feloldók mintái

### <a name="restful-technical-profile"></a>REST-technikai profil

A [Rest](restful-technical-profile.md) -alapú műszaki profilokban érdemes lehet elküldeni a felhasználó nyelvét, a szabályzat nevét, a hatókört és az ügyfél-azonosítót. A jogcímek alapján a REST API képes egyéni üzleti logikát futtatni, és ha szükséges, honosított hibaüzenetet kell létrehoznia.

A következő példa egy REST-alapú műszaki profilt mutat be ebben a forgatókönyvben:

```xml
<TechnicalProfile Id="REST">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="policyName" DefaultValue="{Policy:PolicyId}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="{OIDC:Scope}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="clientId" DefaultValue="{OIDC:ClientId}" AlwaysUseDefaultValue="true" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="direct-sign-in"></a>Közvetlen bejelentkezés

A jogcímek feloldóinak használatával előre feltöltheti a bejelentkezési nevet vagy a közvetlen bejelentkezést egy adott közösségi identitás-szolgáltatóba, például a Facebook, a LinkedIn vagy egy Microsoft-fiók. További információ: [közvetlen bejelentkezés beállítása Azure Active Directory B2C használatával](direct-signin.md).

### <a name="dynamic-ui-customization"></a>Dinamikus felhasználói felület testreszabása

Azure AD B2C lehetővé teszi a lekérdezési karakterlánc paramétereinek átadását a HTML-tartalom definíciós végpontjai számára az oldal tartalmának dinamikus megjelenítéséhez. Ez a funkció lehetővé teszi például a háttérkép módosítását a Azure AD B2C regisztrációs vagy bejelentkezési oldalon a webes vagy mobil alkalmazásból származó egyéni paraméter alapján. További információ: [a felhasználói felület dinamikus konfigurálása egyéni házirendek használatával Azure Active Directory B2Cban](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri). A HTML-lapot nyelvi paraméter alapján is honosíthatja, vagy az ügyfél-azonosító alapján módosíthatja a tartalmat.

A következő példa a **campaignId** nevű lekérdezési karakterlánc paraméter értékét adja át, az `Hawaii` ügyfél-azonosítót jelölő **nyelvi** kóddal `en-US` és **alkalmazással** :

```xml
<UserJourneyBehaviors>
  <ContentDefinitionParameters>
    <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    <Parameter Name="language">{Culture:RFC5646}</Parameter>
    <Parameter Name="app">{OIDC:ClientId}</Parameter>
  </ContentDefinitionParameters>
</UserJourneyBehaviors>
```

Ennek eredményeképpen a Azure AD B2C elküldi a fenti paramétereket a HTML-tartalom oldalának:

```
/selfAsserted.aspx?campaignId=hawaii&language=en-US&app=0239a9cc-309c-4d41-87f1-31288feb2e82
```

### <a name="content-definition"></a>Tartalom definíciója

Egy [ContentDefinition](contentdefinitions.md) `LoadUri` elküldheti a jogcímek feloldóit, hogy a különböző helyekről származó tartalmat lekérjen a használt paraméterek alapján.

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

### <a name="application-insights-technical-profile"></a>Application Insights műszaki profil

Az Azure Application Insights és a jogcím-feloldók segítségével elemzéseket nyerhet a felhasználói viselkedésről. A Application Insights technikai profilban elküldheti azokat a bemeneti jogcímeket, amelyeket az Azure Application Insights tart fenn. További információ: a [felhasználói viselkedés nyomon követése Azure ad B2C-útvonalakon Application Insights használatával](analytics-with-application-insights.md). Az alábbi példa a házirend-azonosítót, a korrelációs azonosítót, a nyelvet és az ügyfél-azonosítót küldi az Azure Application Insightsnak.

```xml
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Alternate Email</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
    <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
    <InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
    <InputClaim ClaimTypeReferenceId="AppId" PartnerClaimType="{property:App}" DefaultValue="{OIDC:ClientId}" />
  </InputClaims>
</TechnicalProfile>
```

### <a name="relying-party-policy"></a>Függő entitások házirendje

A [függő entitások](relyingparty.md) szabályzatának technikai profiljában érdemes lehet a bérlői azonosítót vagy korrelációs azonosítót küldeni a függő entitás alkalmazásnak a JWT belül.

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
        <OutputClaim ClaimTypeReferenceId="correlationId" AlwaysUseDefaultValue="true" DefaultValue="{Context:CorrelationId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
```
