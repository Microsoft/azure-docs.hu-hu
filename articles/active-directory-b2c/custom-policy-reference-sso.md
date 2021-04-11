---
title: Egyszeri bejelentkezés munkamenet-kezelés egyéni szabályzatok használatával
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan kezelheti az egyszeri bejelentkezéses munkameneteket a Azure AD B2C egyéni házirendjeivel.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1f3fb07eaf7f63d15232f4c94eeee45f43c81616
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075134"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Egyszeri bejelentkezés munkamenet-kezelés a Azure Active Directory B2C-ben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az [egyszeri bejelentkezési (SSO) munkamenet](session-behavior.md) -kezelés ugyanazt a szemantikai kapcsolatot használja, mint bármely más technikai profil az egyéni házirendekben. Egy előkészítési lépés végrehajtásakor a rendszer a lépéshez társított technikai profilt kérdezi le `UseTechnicalProfileForSessionManagement` . Ha van ilyen, a rendszer ellenőrzi a hivatkozott SSO munkamenet-szolgáltatót, hogy a felhasználó munkamenet-résztvevő-e. Ha igen, az SSO-munkamenet-szolgáltató a munkamenet újrafeltöltésére szolgál. Hasonlóképpen, ha egy előkészítési lépés végrehajtása befejeződött, a szolgáltató a munkamenetben lévő információk tárolására szolgál, ha meg van adva egy egyszeri bejelentkezéses munkamenet-szolgáltató.

Azure AD B2C több SSO munkamenet-szolgáltatót definiált:

|Munkamenet-szolgáltató  |Hatókör  |
|---------|---------|
|[NoopSSOSessionProvider](#noopssosessionprovider)     |  Nincsenek       |       
|[DefaultSSOSessionProvider](#defaultssosessionprovider)    | Azure AD B2C belső munkamenet-kezelő.      |       
|[ExternalLoginSSOSessionProvider](#externalloginssosessionprovider)     | Azure AD B2C és OAuth1, a OAuth2 vagy az OpenId Connect Identity Provider között.        | 
|[OAuthSSOSessionProvider](#oauthssosessionprovider)     | Egy OAuth2 vagy OpenId Connect függő entitás alkalmazás és Azure AD B2C között.        |        
|[SamlSSOSessionProvider](#samlssosessionprovider)     | Azure AD B2C és SAML-identitás szolgáltatója között. És egy SAML szolgáltató (függő entitás alkalmazás) és Azure AD B2C között.  |        




Az egyszeri bejelentkezéses felügyeleti osztályok `<UseTechnicalProfileForSessionManagement ReferenceId="{ID}" />` egy technikai profil eleme alapján vannak megadva.

## <a name="input-claims"></a>Bemeneti jogcímek

Az `InputClaims` elem üres vagy hiányzik.

## <a name="persisted-claims"></a>Megőrzött jogcímek

Azokat a jogcímeket, amelyeket vissza kell adni az alkalmazásnak, vagy a későbbi lépésekben az előfeltételeket kell használni, a munkamenetben kell tárolni, vagy a felhasználó profiljából kell kibővíteni a címtárban. A megőrzött jogcímek használata biztosítja, hogy a hitelesítési útvonalak ne legyenek sikertelenek a hiányzó jogcímek esetében. Ha jogcímeket szeretne felvenni a munkamenetbe, használja a `<PersistedClaims>` technikai profil elemét. Ha a szolgáltató a munkamenet újrafeltöltésére szolgál, a rendszer hozzáadja a megőrzött jogcímeket a jogcímek táskához.

## <a name="output-claims"></a>Kimeneti jogcímek

A a `<OutputClaims>` jogcímek munkamenetből való beolvasására szolgál.

## <a name="session-providers"></a>Munkamenet-szolgáltatók

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Ahogy a név diktálja, a szolgáltató nem tesz semmit. Ez a szolgáltató használható egy adott technikai profil SSO-viselkedésének letiltására. A következő `SM-Noop` technikai profil szerepel az [egyéni házirend-indító csomagban](custom-policy-get-started.md#custom-policy-starter-pack).

```xml
<TechnicalProfile Id="SM-Noop">
  <DisplayName>Noop Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.NoopSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Ezt a szolgáltatót a jogcímek egy munkamenetben való tárolására lehet használni. Ez a szolgáltató általában a helyi és összevont fiókok kezeléséhez használt technikai profilban hivatkozik. A következő `SM-AAD` technikai profil szerepel az [egyéni házirend-indító csomagban](custom-policy-get-started.md#custom-policy-starter-pack).

```xml
<TechnicalProfile Id="SM-AAD">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="signInName" />
    <PersistedClaim ClaimTypeReferenceId="authenticationSource" />
    <PersistedClaim ClaimTypeReferenceId="identityProvider" />
    <PersistedClaim ClaimTypeReferenceId="newUser" />
    <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```


A következő `SM-MFA` technikai profil szerepel az [egyéni házirend-indító csomagban](custom-policy-get-started.md#custom-policy-starter-pack) `SocialAndLocalAccountsWithMfa` . Ez a technikai profil a multi-Factor Authentication munkamenetet kezeli.

```xml
<TechnicalProfile Id="SM-MFA">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isActiveMFASession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```

### <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Ez a szolgáltató a "személyazonossági szolgáltató választása" képernyő letiltására és egy összevont identitás-szolgáltatótól való kijelentkezésre szolgál. Ez általában egy összevont identitás-szolgáltatóhoz konfigurált technikai profilban hivatkozik, például Facebook vagy Azure Active Directory. A következő `SM-SocialLogin` technikai profil szerepel az [egyéni házirend-indító csomagban](custom-policy-get-started.md#custom-policy-starter-pack).

```xml
<TechnicalProfile Id="SM-SocialLogin">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="AlternativeSecurityId" />
  </PersistedClaims>
</TechnicalProfile>
```

#### <a name="metadata"></a>Metaadatok

| Attribútum | Kötelező | Leírás|
| --- | --- | --- |
| AlwaysFetchClaimsFromProvider | No | Jelenleg nincs használatban, figyelmen kívül hagyható. |

### <a name="oauthssosessionprovider"></a>OAuthSSOSessionProvider

Ez a szolgáltató a OAuth2 vagy OpenId Connect függő entitás és Azure AD B2C közötti Azure AD B2C munkamenetek kezelésére szolgál.

```xml
<TechnicalProfile Id="SM-jwt-issuer">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Ez a szolgáltató az Azure AD B2C SAML-munkamenetek felügyeletére szolgál a függő entitás alkalmazása vagy egy összevont SAML-szolgáltató között. Ha az SSO-szolgáltatót használja az SAML-identitás szolgáltatói munkamenetének tárolására, akkor a értékének a következőnek kell `RegisterServiceProviders` lennie: `false` . Az `SM-Saml-idp` [SAML-identitás szolgáltatója](identity-provider-generic-saml.md)a következő technikai profilt használja.

```xml
<TechnicalProfile Id="SM-Saml-idp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="RegisterServiceProviders">false</Item>
  </Metadata>
</TechnicalProfile>
```

A B2C SAML-munkamenet tárolására szolgáló szolgáltató használata esetén a értékének a következőnek `RegisterServiceProviders` kell lennie: `true` . Az SAML-munkamenet kijelentkezéséhez a `SessionIndex` és a `NameID` Befejezés szükséges.

Az `SM-Saml-issuer` [SAML kiállítói műszaki profil](saml-service-provider.md) a következő műszaki profilt használja

```xml
<TechnicalProfile Id="SM-Saml-issuer">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
</TechnicalProfile>
```

#### <a name="metadata"></a>Metaadatok

| Attribútum | Kötelező | Leírás|
| --- | --- | --- |
| IncludeSessionIndex | No | Jelenleg nincs használatban, figyelmen kívül hagyható.|
| RegisterServiceProviders | No | Azt jelzi, hogy a szolgáltatónak regisztrálnia kell az összes olyan SAML-szolgáltatót, amely kiállított egy állítást. Lehetséges értékek: `true` (alapértelmezett) vagy `false` .|


## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [konfigurálhatja a munkamenet viselkedését](session-behavior.md).
