---
title: Jogcím-átalakítási technikai profil definiálása
titleSuffix: Azure AD B2C
description: Definiáljon egy jogcím-átalakítási technikai profilt a Azure Active Directory B2C egyéni házirendjében.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bf06fe7d4e529eb04b156a2d61011198a6fe0978
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949423"
---
# <a name="define-a-claims-transformation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Jogcím-átalakítási technikai profil definiálása egy Azure Active Directory B2C egyéni házirendben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A jogcím-átalakítás technikai profilja lehetővé teszi a kimeneti jogcímek átalakításának meghívását a jogcímek értékeinek, a jogcímek érvényesítésének vagy a kimeneti jogcímek alapértelmezett értékeinek megadására.

## <a name="protocol"></a>Protocol (Protokoll)

A **protokoll** elem **Name** attribútumát `Proprietary`értékre kell állítani. A **kezelő** attribútumnak tartalmaznia kell a Azure AD B2C: `Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`által használt protokollkezelő-szerelvény teljesen minősített nevét.

A következő példa egy jogcím-átalakítási technikai profilt mutat be:

```XML
<TechnicalProfile Id="Facebook-OAUTH-UnLink">
    <DisplayName>Unlink Facebook</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="output-claims"></a>Kimeneti jogcímek

A **OutputClaims** elem megadása kötelező. Meg kell adnia legalább egy, a technikai profil által visszaadott kimeneti jogcímet. Az alábbi példa bemutatja, hogyan állíthatja be a kimeneti jogcímek alapértelmezett értékeit:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="ageGroup" DefaultValue="Undefined" />
  <OutputClaim ClaimTypeReferenceId="ageGroupValueChanged" DefaultValue="false" />
</OutputClaims>
```

## <a name="output-claims-transformations"></a>Kimeneti jogcímek átalakítása

A **OutputClaimsTransformations** elem tartalmazhatja a jogcímek módosításához vagy újak létrehozásához használt **OutputClaimsTransformation** -elemek gyűjteményét. A következő technikai profil hívja meg a **RemoveAlternativeSecurityIdByIdentityProvider** jogcím-átalakítást. Ez a jogcím-átalakítás eltávolítja a közösségi azonosítást a **AlternativeSecurityIds**gyűjteményéből. Ennek a technikai profilnak a kimeneti jogcímei a **identityProvider2**, amely `facebook.com`re van beállítva, és a **AlternativeSecurityIds**, amely a Facebook.com identitás eltávolítása után a felhasználóhoz társított közösségi identitások listáját tartalmazza.

```XML
<ClaimsTransformations>
  <ClaimsTransformation Id="RemoveAlternativeSecurityIdByIdentityProvider"
TransformationMethod="RemoveAlternativeSecurityIdByIdentityProvider">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="IdentityProvider2"
TransformationClaimType="identityProvider" />
      <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds"
TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds"
TransformationClaimType="collection" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
...
<TechnicalProfile Id="Facebook-OAUTH-UnLink">
    <DisplayName>Unlink Facebook</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="identityProvider2" DefaultValue="facebook.com" AlwaysUseDefaultValue="true" />
    </OutputClaims>
    <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="RemoveAlternativeSecurityIdByIdentityProvider" />
    </OutputClaimsTransformations>
    <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

A jogcím-átalakítás technikai profilja lehetővé teszi, hogy a jogcímek átalakítását bármely felhasználói út előkészítési lépése alapján végrehajtsa. A következő példában a előkészítési lépés meghívja az egyik leválasztási technikai profilt, például a **-Facebook-OAUTH csatolását**. Ez a technikai profil meghívja a jogcím-átalakítási technikai profil **RemoveAlternativeSecurityIdByIdentityProvider**, amely létrehoz egy új **AlternativeSecurityIds2** -jogcímet, amely tartalmazza a felhasználói közösségi identitások listáját, miközben eltávolítja a Facebook-identitást a gyűjteményből.

```XML
<UserJourney Id="AccountUnLink">
  <OrchestrationSteps>
    ...
    <OrchestrationStep Order="8" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="UnLinkFacebookExchange" TechnicalProfileReferenceId="UnLink-Facebook-OAUTH" />
        <ClaimsExchange Id="UnLinkMicrosoftExchange" TechnicalProfileReferenceId="UnLink-Microsoft-OAUTH" />
        <ClaimsExchange Id="UnLinkGitHubExchange" TechnicalProfileReferenceId="UnLink-GitHub-OAUTH" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ...
  </OrchestrationSteps>
</UserJourney>
```

## <a name="use-a-validation-technical-profile"></a>Érvényesítési technikai profil használata

A jogcím-átalakítás technikai profilja az információk érvényesítésére használható. A következő példában a **LocalAccountSignUpWithLogonEmail** nevű [önjelölt technikai profil](self-asserted-technical-profile.md) megkéri a felhasználót, hogy kétszer adja meg az e-mailt, majd meghívja a **validate-email** nevű [érvényesítési technikai profilt](validation-technical-profile.md) az e-mailek érvényesítéséhez. A **validate-email** technikai profil meghívja a jogcím-átalakítási **AssertEmailAreEqual** , hogy összehasonlítsa a két jogcím **e-mail-címét** és **emailRepeat**, és kivételt jelez, ha a megadott összehasonlításnak megfelelően nem egyeznek.

```XML
<ClaimsTransformations>
  <ClaimsTransformation Id="AssertEmailAreEqual" TransformationMethod="AssertStringClaimsAreEqual">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim1" />
      <InputClaim ClaimTypeReferenceId="emailRepeat" TransformationClaimType="inputClaim2" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    </InputParameters>
  </ClaimsTransformation>
</ClaimsTransformations>
```

A jogcím-átalakítási technikai profil meghívja a **AssertEmailAreEqual** jogcímek átalakítását, amely azt állítja, hogy a felhasználó által megadott e-mailek megegyeznek.

```XML
<TechnicalProfile Id="Validate-Email">
  <DisplayName>Unlink Facebook</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailRepeat" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAreEqual" />
  </OutputClaimsTransformations>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

Az önellenőrzött műszaki profilok meghívhatják az érvényesítési technikai profilt, és megjeleníthetik a hibaüzenetet, ahogyan az a **UserMessageIfClaimsTransformationStringsAreNotEqual** -metaadatokban meg van adva.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>User ID signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    ...
    <Item Key="UserMessageIfClaimsTransformationStringsAreNotEqual">The email addresses you provided are not the same</Item>
  </Metadata>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="emailRepeat" />
    ...
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="Validate-Email" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```
