---
title: Regisztráció és bejelentkezés beállítása LinkedIn-fiókkal
titleSuffix: Azure AD B2C
description: Adja meg a regisztrációt, és jelentkezzen be az alkalmazásokban a LinkedIn-fiókkal rendelkező ügyfeleknek Azure Active Directory B2C használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/27/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 1ce9c00cb58253e2cca9a7d60c4cce9b77709688
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98953852"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Bejelentkezés és bejelentkezés beállítása LinkedIn-fiókkal Azure Active Directory B2C használatával

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-linkedin-application"></a>LinkedIn-alkalmazás létrehozása

Ha Azure Active Directory B2C (Azure AD B2C) LinkedIn-fiókkal rendelkező felhasználók számára szeretné engedélyezni a bejelentkezést, létre kell hoznia egy alkalmazást a [LinkedIn Developers webhelyén](https://www.developer.linkedin.com/). További információ: [engedélyezési kód folyamata](/linkedin/shared/authentication/authorization-code-flow). Ha még nem rendelkezik LinkedIn-fiókkal, regisztrálhat a következő címen: [https://www.linkedin.com/](https://www.linkedin.com/) .

1. Jelentkezzen be a [LinkedIn Developers webhelyére](https://www.developer.linkedin.com/) a LinkedIn-fiókja hitelesítő adataival.
1. Válassza **a saját alkalmazások** lehetőséget, majd kattintson az **alkalmazás létrehozása** elemre.
1. Adja meg az **alkalmazás nevét**, a **LinkedIn oldalt**, az **adatvédelmi szabályzat URL-címét** és az **alkalmazás emblémáját**.
1. Fogadja el a LinkedIn **API használati feltételeit** , és kattintson az **alkalmazás létrehozása** elemre.
1. Válassza az **Auth (hitelesítés** ) fület. A **hitelesítési kulcsok** területen másolja ki az **ügyfél-azonosító** és az **ügyfél titkos** kulcsának értékeit. Mindkettőre szüksége lesz a LinkedIn identitás-szolgáltatóként való konfigurálásához a bérlőben. Az **ügyfél titkos kulcsa** fontos biztonsági hitelesítő adat.
1. Jelölje be az **alkalmazáshoz tartozó, a hitelesítő átirányítási URL-címek** melletti ceruza szerkesztése elemet, majd válassza az **átirányítási URL-cím hozzáadása** lehetőséget Adja meg a `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` kifejezést a `your-tenant-name` bérlő nevével. A bérlő nevének megadásakor az összes kisbetűt kell használnia, még akkor is, ha a bérlőt nagybetűvel definiálták Azure AD B2C. Válassza a **Frissítés** lehetőséget.
2. Alapértelmezés szerint a LinkedIn alkalmazás nem rendelkezik jóváhagyással a bejelentkezéshez kapcsolódó hatókörökhöz. Ha felülvizsgálatot szeretne kérni, válassza a **termékek** lapot, majd válassza a **Bejelentkezés a LinkedIn** használatával lehetőséget. A felülvizsgálat befejezésekor a rendszer hozzáadja a szükséges hatóköröket az alkalmazáshoz.
   > [!NOTE]
   > Az alkalmazáshoz jelenleg engedélyezett hatóköröket az **OAuth 2,0-hatókörök** szakaszban található **hitelesítés** lapon tekintheti meg.

::: zone pivot="b2c-user-flow"

## <a name="configure-linkedin-as-an-identity-provider"></a>A LinkedIn konfigurálása identitás-szolgáltatóként

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
1. Válassza az **identitás-szolgáltatók**, majd a **LinkedIn** lehetőséget.
1. Adjon meg egy **nevet**. Például: *LinkedIn*.
1. Az **ügyfél-azonosító** mezőben adja meg a korábban létrehozott LinkedIn-alkalmazás ügyfél-azonosítóját.
1. Az **ügyfél titka** mezőben adja meg a rögzített ügyfél-titkot.
1. Kattintson a **Mentés** gombra.

## <a name="add-linkedin-identity-provider-to-a-user-flow"></a>LinkedIn Identity Provider hozzáadása felhasználói folyamathoz 

1. A Azure AD B2C-bérlőben válassza a **felhasználói folyamatok** lehetőséget.
1. Kattintson arra a felhasználói folyamatra, amelyhez hozzá szeretné adni a LinkedIn Identity providert.
1. A **közösségi identitás-szolgáltatók** területen válassza a **LinkedIn** lehetőséget.
1. Kattintson a **Mentés** gombra.
1. A szabályzat teszteléséhez válassza a **felhasználói folyamat futtatása** lehetőséget.
1. Az **alkalmazás** lapon válassza ki a korábban regisztrált *testapp1* nevű webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Kattintson a **felhasználói folyamat futtatása** elemre.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Házirend-kulcs létrehozása

A Azure AD B2C bérlőben korábban rögzített ügyfél-titkos kulcsot kell tárolnia.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Az Áttekintés lapon válassza az **identitási élmény keretrendszert**.
5. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás** gombra.
6. A **Beállítások** területen válassza a lehetőséget `Manual` .
7. Adja meg a szabályzat kulcsának **nevét** . Például: `LinkedInSecret`. A rendszer automatikusan hozzáadja a kulcs nevét a *B2C_1A_* előtaghoz.
8. A **Secret (titkos kulcs**) mezőben adja meg a korábban rögzített ügyfél-titkot.
9. A **kulcshasználat** beállításnál válassza a elemet `Signature` .
10. Kattintson a **Létrehozás** lehetőségre.

## <a name="configure-linkedin-as-an-identity-provider"></a>A LinkedIn konfigurálása identitás-szolgáltatóként

Annak engedélyezéséhez, hogy a felhasználók egy LinkedIn-fiókkal jelentkezzenek be, meg kell adnia a fiókot jogcím-szolgáltatóként, amely Azure AD B2C tud kommunikálni egy végponton keresztül. A végpont olyan jogcímeket biztosít, amelyeket a Azure AD B2C használ annak ellenőrzéséhez, hogy egy adott felhasználó hitelesítve van-e.

Definiáljon egy LinkedIn-fiókot jogcím-szolgáltatóként, ha hozzáadja azt a **ClaimsProviders** elemhez a szabályzat bővítmény fájljában.

1. Nyissa meg a * SocialAndLocalAccounts/**TrustFrameworkExtensions.xml** _ fájlt a szerkesztőben. Ez a fájl az egyes előfeltételek egyikének részeként letöltött [egyéni házirend alapszintű csomagban][starter-pack] található.
1. Keresse meg a _ *ClaimsProviders** elemet. Ha nem létezik, adja hozzá a gyökérelem elemhez.
1. Vegyen fel egy új **ClaimsProvider** a következőképpen:

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAuth2">
          <DisplayName>LinkedIn</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">linkedin</Item>
            <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
            <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
            <Item Key="scope">r_emailaddress r_liteprofile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="external_user_identity_claim_id">id</Item>
            <Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
            <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <InputClaims />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Cserélje le a **client_id** értékét a korábban rögzített LinkedIn-alkalmazás ügyfél-azonosítójával.
1. Mentse a fájlt.

### <a name="add-the-claims-transformations"></a>Jogcím-átalakítások hozzáadása

A LinkedIn technikai profilja megköveteli, hogy a **ExtractGivenNameFromLinkedInResponse** és a **ExtractSurNameFromLinkedInResponse** jogcím-átalakítások hozzá legyenek adva a ClaimsTransformations listájához. Ha nem rendelkezik a fájlban definiált **ClaimsTransformations** elemmel, adja hozzá a SZÜLŐ XML-elemeket az alább látható módon. A jogcímek átalakításához egy új, **nullStringClaim** nevű jogcím-típusra is szükség van.

Adja hozzá a **BuildingBlocks** elemet a *TrustFrameworkExtensions.xml* fájl teteje közelében. Példa: *TrustFrameworkBase.xml* .

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

[!INCLUDE [active-directory-b2c-test-relying-party-policy](../../includes/active-directory-b2c-test-relying-party-policy-user-journey.md)]

## <a name="migration-from-v10-to-v20"></a>Migrálás v 1.0-ről v 2.0-ra

A LinkedIn nemrég [frissítette az API-kat 1.0-s és v 2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates)-s verzióra. Ha a meglévő konfigurációt az új konfigurációra szeretné áttelepíteni, az alábbi részekben található információk segítségével frissítheti a technikai profil elemeit.

### <a name="replace-items-in-the-metadata"></a>A metaadatokban szereplő elemek cseréje

A **kivonatjogcím** meglévő **metaadat** elemében frissítse a következő **elem** elemeit:

```xml
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
<Item Key="scope">r_emailaddress r_basicprofile</Item>
```

Címzett:

```xml
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
<Item Key="scope">r_emailaddress r_liteprofile</Item>
```

### <a name="add-items-to-the-metadata"></a>Elemek hozzáadása a metaadatokhoz

A Kivonatjogcím **metaadataiban** adja hozzá a következő **elem** elemeit:

```xml
<Item Key="external_user_identity_claim_id">id</Item>
<Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
<Item Key="ResolveJsonPathsInJsonTokens">true</Item>
```

### <a name="update-the-outputclaims"></a>A OutputClaims frissítése

A **kivonatjogcím** meglévő **OutputClaims** frissítse a következő **OutputClaim** elemeket:

```xml
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
```

Címzett:

```xml
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
```

### <a name="add-new-outputclaimstransformation-elements"></a>Új OutputClaimsTransformation elemek hozzáadása

A Kivonatjogcím **OutputClaimsTransformations** adja hozzá a következő **OutputClaimsTransformation** elemeket:

```xml
<OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
<OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
```

### <a name="define-the-new-claims-transformations-and-claim-type"></a>Az új jogcím-átalakítások és jogcím típusának megadása

Az utolsó lépésben új jogcím-átalakításokat adott hozzá, amelyeket meg kell határozni. A jogcímek átalakításának definiálásához adja hozzá őket a **ClaimsTransformations** listájához. Ha nem rendelkezik a fájlban definiált **ClaimsTransformations** elemmel, adja hozzá a SZÜLŐ XML-elemeket az alább látható módon. A jogcímek átalakításához egy új, **nullStringClaim** nevű jogcím-típusra is szükség van.

A **BuildingBlocks** elemet hozzá kell adni a fájl elejéhez. Példaként tekintse meg a *TrustframeworkBase.xml* .

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="obtain-an-email-address"></a>E-mail-cím beszerzése

A LinkedIn 1.0-ről v 2.0-ra való áttelepítésének részeként egy másik API meghívása szükséges az e-mail cím beszerzéséhez. Ha a regisztráció során meg kell szereznie az e-mail-címet, tegye a következőket:

1. A fenti lépések végrehajtásával engedélyezheti, hogy a Azure AD B2C összevonása a LinkedIn használatával, hogy a felhasználó bejelentkezzen. Az összevonás részeként Azure AD B2C fogadja a LinkedIn hozzáférési jogkivonatát.
1. Mentse a LinkedIn hozzáférési tokent egy jogcímbe. [Tekintse meg az itt található utasításokat](idp-pass-through-user-flow.md).
1. Adja hozzá a következő jogcím-szolgáltatót, amely a kérést a LinkedIn API-ját hozza létre `/emailAddress` . A kérelem engedélyezéséhez szüksége lesz a LinkedIn hozzáférési jogkivonatra.

    ```xml
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="API-LinkedInEmail">
          <DisplayName>Get LinkedIn email</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
              <Item Key="ServiceUrl">https://api.linkedin.com/v2/emailAddress?q=members&amp;projection=(elements*(handle~))</Item>
              <Item Key="AuthenticationType">Bearer</Item>
              <Item Key="UseClaimAsBearerToken">identityProviderAccessToken</Item>
              <Item Key="SendClaimsIn">Url</Item>
              <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
          </Metadata>
          <InputClaims>
              <InputClaim ClaimTypeReferenceId="identityProviderAccessToken" />
          </InputClaims>
          <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="elements[0].handle~.emailAddress" />
          </OutputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Adja hozzá a következő előkészítési lépést a felhasználói útra, hogy az API jogcím-szolgáltató aktiválódik, amikor egy felhasználó bejelentkezik a LinkedIn használatával. Ügyeljen rá, hogy megfelelően frissítse a `Order` számot. Adja hozzá ezt a lépést közvetlenül a LinkedIn műszaki profilt kiváltó előkészítési lépés után.

    ```xml
    <!-- Extra step for LinkedIn to get the email -->
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Value>linkedin.com</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="GetEmail" TechnicalProfileReferenceId="API-LinkedInEmail" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

Az e-mail-cím beszerzése a LinkedIn-ből a regisztráció során nem kötelező. Ha úgy dönt, hogy nem kívánja beolvasni az e-mailt a LinkedIn-től, de a regisztráció során egyet kér, a felhasználónak manuálisan kell megadnia az e-mail-címet és hitelesítenie.

A LinkedIn Identity providert használó szabályzat teljes mintájának megtekintéséhez tekintse meg az [egyéni házirend alapszintű csomagját](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/linkedin-identity-provider).

<!-- Links - EXTERNAL -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="migration-from-v10-to-v20"></a>Migrálás v 1.0-ről v 2.0-ra

A LinkedIn nemrég [frissítette az API-kat 1.0-s és v 2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates)-s verzióra. A Migrálás részeként Azure AD B2C csak a LinkedIn-felhasználó teljes nevét tudja megszerezni a regisztráció során. Ha egy e-mail-cím a regisztráció során összegyűjtött attribútumok egyike, a felhasználónak manuálisan kell megadnia az e-mail-címet, és hitelesítenie kell azt.

::: zone-end