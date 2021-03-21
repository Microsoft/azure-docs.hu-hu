---
title: Azure AD-beli technikai profil definiálása egyéni házirendben
titleSuffix: Azure AD B2C
description: Azure Active Directory technikai profilt definiálhat egy egyéni házirendben Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3f92bfe98a45117264c957481a75493de652abc9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97508101"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory műszaki profil definiálása egy Azure Active Directory B2C egyéni házirendben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A Azure Active Directory B2C (Azure AD B2C) támogatást nyújt a Azure Active Directory felhasználói felügyelethez. Ez a cikk a szabványos protokollt támogató jogcím-szolgáltatóval való interakcióra szolgáló technikai profil sajátosságait ismerteti.

## <a name="protocol"></a>Protokoll

A **protokoll** elem **Name** attribútumát be kell állítani `Proprietary` . A **kezelő** attribútumnak tartalmaznia kell a protokollkezelő szerelvényének teljesen minősített nevét `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null` .

A következő [Egyéni szabályzat](custom-policy-get-started.md#custom-policy-starter-pack) -előindítási csomag Azure ad technikai profiljai tartalmazzák a **HRE-közös** technikai profilt. Az Azure AD technikai profiljai nem határozzák meg a protokollt, mert a protokoll a **HRE-közös** technikai profilban van konfigurálva:
 
- **HRE-UserReadUsingAlternativeSecurityId** és **HRE-UserReadUsingAlternativeSecurityId – nem sikerült** – keressen egy közösségi fiókot a címtárban.
- **HRE-UserWriteUsingAlternativeSecurityId** – új közösségi fiók létrehozása.
- **HRE-UserReadUsingEmailAddress** – helyi fiók megkeresése a címtárban.
- **HRE-UserWriteUsingLogonEmail** – új helyi fiók létrehozása.
- **HRE-UserWritePasswordUsingObjectId** – helyi fiók jelszavának frissítése.
- **HRE-UserWriteProfileUsingObjectId** – helyi vagy közösségi fiók felhasználói profiljának frissítése.
- **HRE-UserReadUsingObjectId** – helyi vagy közösségi fiók felhasználói profiljának beolvasása.
- **HRE-UserWritePhoneNumberUsingObjectId** – helyi vagy közösségi fiók MFA-telefonszámának megírása

A következő példa a **HRE-közös** technikai profilt mutatja be:

```xml
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>

  <!-- We need this here to suppress the SelfAsserted provider from invoking SSO on validation profiles. -->
  <IncludeInSso>false</IncludeInSso>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="inputclaims"></a>Szabályzattípushoz

A Szabályzattípushoz elem tartalmaz egy jogcímet, amely egy fiók keresésére szolgál a címtárban, vagy létrehozhat egy újat. A bemeneti jogcímek gyűjteményében pontosan egy InputClaim elemnek kell szerepelnie az összes Azure AD technikai profilhoz. Előfordulhat, hogy le kell képeznie a szabályzatban definiált jogcím nevét a Azure Active Directoryban definiált névre.

Meglévő felhasználói fiók olvasásához, frissítéséhez vagy törléséhez a bemeneti jogcímek egy olyan kulcs, amely egyedileg azonosítja a fiókot az Azure AD-címtárban. Például: **objectId**, **userPrincipalName**, **signInNames. emailAddress**, **signInNames. username** vagy **alternativeSecurityId**. 

Új felhasználói fiók létrehozásához a bemeneti jogcím olyan kulcs, amely egyedileg azonosít egy helyi vagy összevont fiókot. Például: helyi fiók: **signInNames. emailAddress** vagy **signInNames. username**. Összevont fiók esetén: a **alternativeSecurityId**.

A [InputClaimsTransformations](technicalprofiles.md#input-claims-transformations) elem olyan bemeneti jogcím-átalakítási elemek gyűjteményét is tartalmazhatja, amelyek a bemeneti jogcímek módosításához vagy újak létrehozásához használatosak.

## <a name="outputclaims"></a>OutputClaims

A **OutputClaims** elem az Azure ad technikai profil által visszaadott jogcímek listáját tartalmazza. Előfordulhat, hogy le kell képeznie a szabályzatban definiált jogcím nevét a Azure Active Directoryban definiált névre. Olyan jogcímeket is tartalmazhat, amelyeket a Azure Active Directory nem ad vissza, ha beállítja az `DefaultValue` attribútumot.

A [OutputClaimsTransformations](technicalprofiles.md#output-claims-transformations) elem olyan **OutputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja, amelyek a kimeneti jogcímek módosítására vagy újak előállítására szolgálnak.

Például a **HRE-UserWriteUsingLogonEmail** technikai profil egy helyi fiókot hoz létre, és a következő jogcímeket adja vissza:

- **objectId**, amely az új fiók azonosítója
- **newUser**, amely azt jelzi, hogy a felhasználó új-e
- **authenticationSource**, amely a következőre történő hitelesítést állítja be `localAccountAuthentication`
- **userPrincipalName**, az új fiók egyszerű felhasználóneve
- **signInNames. emailAddress**, amely a fiók bejelentkezési neve, hasonlóan az **e-mail** bemeneti jogcímhez

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="objectId" />
  <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
  <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
  <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
</OutputClaims>
```

## <a name="persistedclaims"></a>PersistedClaims

A **PersistedClaims** elem tartalmazza az összes olyan értéket, amelyet az Azure ad a szabályzat és az Azure ad-attribútum neve [ClaimsSchema](claimsschema.md) szakaszában már definiált jogcím-típus közötti lehetséges leképezési információval kell megőrizni.

A **HRE-UserWriteUsingLogonEmail** technikai profil, amely új helyi fiókot hoz létre, a következő jogcímeket tartja fenn:

```xml
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
    <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />

    <!-- Optional claims. -->
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
```

A jogcím neve az Azure AD attribútum neve, kivéve, ha meg van adva a **PartnerClaimType** attribútum, amely tartalmazza az Azure ad-attribútum nevét.

## <a name="requirements-of-an-operation"></a>A művelet követelményei

- Az összes Azure AD technikai profilhoz pontosan egy **InputClaim** elemnek kell tartoznia a jogcím-táskában.
- A [felhasználói profil attribútumai című cikk](user-profile-attributes.md) a bemeneti jogcímek, a kimeneti jogcímek és a megőrzött jogcímek által használható, támogatott Azure ad B2C felhasználóiprofil-attribútumokat ismerteti. 
- Ha a művelet `Write` vagy `DeleteClaims` , akkor azt is meg kell jelennie egy **PersistedClaims** elemben.
- A **userPrincipalName** jogcím értékének formátuma a ( `user@tenant.onmicrosoft.com` ).
- A **DisplayName** jogcím megadása kötelező, és nem lehet üres karakterlánc.

## <a name="azure-ad-technical-provider-operations"></a>Azure AD technikai szolgáltatói műveletek

### <a name="read"></a>Olvasás

Az **olvasási** művelet egyetlen felhasználói fiók adatait olvassa be. A következő technikai profil egy felhasználói fiók adatait olvassa be a felhasználó objectId használatával:

```xml
<TechnicalProfile Id="AAD-UserReadUsingObjectId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims>

    <!-- Required claims -->
    <OutputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />

    <!-- Optional claims -->
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="write"></a>Írás

Az **írási** művelet egyetlen felhasználói fiókot hoz létre vagy frissít. A következő technikai profil új közösségi fiókot hoz létre:

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Write</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />

    <!-- Optional claims -->
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

### <a name="deleteclaims"></a>DeleteClaims

A **DeleteClaims** művelet törli az adatokat egy adott jogcímek listájáról. A következő technikai profil törli a jogcímeket:

```xml
<TechnicalProfile Id="AAD-DeleteClaimsUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaims</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" PartnerClaimType="strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims />
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="deleteclaimsprincipal"></a>DeleteClaimsPrincipal

A **DeleteClaimsPrincipal** művelet egyetlen felhasználói fiókot töröl a címtárból. A következő technikai profil törli a felhasználói fiókot a címtárból az egyszerű felhasználónév használatával:

```xml
<TechnicalProfile Id="AAD-DeleteUserUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

A következő technikai profil töröl egy közösségi felhasználói fiókot a **alternativeSecurityId** használatával:

```xml
<TechnicalProfile Id="AAD-DeleteUserUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```
## <a name="metadata"></a>Metaadatok

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Művelet | Yes | A végrehajtandó művelet. Lehetséges értékek: `Read` ,,, `Write` `DeleteClaims` vagy `DeleteClaimsPrincipal` . |
| RaiseErrorIfClaimsPrincipalDoesNotExist | No | Hiba, ha a felhasználói objektum nem létezik a címtárban. Lehetséges értékek: `true` vagy `false` . |
| RaiseErrorIfClaimsPrincipalAlreadyExists | No | Hiba, ha a felhasználói objektum már létezik. Lehetséges értékek: `true` vagy `false` .|
| ApplicationObjectId | No | A bővítmény attribútumaihoz tartozó alkalmazásobjektum-azonosító. Value: egy alkalmazás ObjectId. További információ: [Egyéni attribútumok használata](user-flow-custom-attributes.md?pivots=b2c-custom-policy). |
| ClientID | No | A bérlő harmadik fél számára való elérésének ügyfél-azonosítója. További információ: egyéni [attribútumok használata egyéni profil szerkesztése házirendben](user-flow-custom-attributes.md?pivots=b2c-custom-policy) |
| IncludeClaimResolvingInClaimsHandling  | No | A bemeneti és a kimeneti jogcímek esetén megadja, hogy a [jogcímek feloldása](claim-resolver-overview.md) szerepel-e a technikai profilban. Lehetséges értékek: `true` , vagy `false` (alapértelmezett). Ha a technikai profilban a jogcím-feloldót szeretné használni, állítsa be a következőt: `true` . |

### <a name="ui-elements"></a>Felhasználói felület elemei
 
A következő beállításokkal megadhatja a hiba esetén megjelenő hibaüzenetet. A metaadatokat az [önérvényesített](self-asserted-technical-profile.md) technikai profilban kell konfigurálni. A hibaüzenetek [honosítható](localization.md).

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalAlreadyExists | No | Ha hiba lép fel (lásd: RaiseErrorIfClaimsPrincipalAlreadyExists-attribútum leírása), akkor a felhasználónak megjelenítendő üzenetet kell megadnia, ha a felhasználói objektum már létezik. |
| UserMessageIfClaimsPrincipalDoesNotExist | No | Ha hiba lép fel (lásd a RaiseErrorIfClaimsPrincipalDoesNotExist attribútum leírását), akkor a felhasználónak megjelenítendő üzenetet kell megadnia, ha a felhasználói objektum nem létezik. |


## <a name="next-steps"></a>Következő lépések

Tekintse meg az alábbi cikket, például az Azure AD technikai profiljának használatát:

- [Jogcímek hozzáadása és felhasználói bevitel testreszabása Egyéni házirendek használatával Azure Active Directory B2C](configure-user-input.md)














