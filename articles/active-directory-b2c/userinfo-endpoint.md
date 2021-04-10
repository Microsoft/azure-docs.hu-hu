---
title: UserInfo végpont | Microsoft Docs
description: Definiáljon egy UserInfo-végpontot a Azure Active Directory B2C egyéni házirendjében.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/09/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: c060a029b1cdbdd890ced96cab732966cb652de0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "102500580"
---
# <a name="userinfo-endpoint"></a>UserInfo végpont

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Az UserInfo végpont az [OpenID Connect standard](https://openid.net/specs/openid-connect-core-1_0.html#UserInfo) (OIDC) specifikáció részét képezi, és úgy van kialakítva, hogy visszaadja a hitelesített felhasználó jogcímeit. Az UserInfo végpontot a függő entitás házirendjében határozzák meg a [végpont](relyingparty.md#endpoints) elem használatával.  

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="userinfo-endpoint-overview"></a>A UserInfo-végpont áttekintése

A felhasználói adatok UserJourney a következőket határozza meg:

- **Engedélyezés**: a UserInfo végpont tulajdonosi jogkivonattal van ellátva. A kiállított hozzáférési jogkivonat az engedélyezési fejlécben jelenik meg a UserInfo végpont számára. A házirend meghatározza azt a technikai profilt, amely érvényesíti a bejövő jogkivonatot, és Kinyeri a jogcímeket, például a felhasználó objectId. A felhasználó objectId az UserInfo-végponti út válaszában visszaadott jogcímek beolvasására szolgál. 
- Előkészítési **lépés**: 
  - A rendszer egy előkészítési lépést használ a felhasználóval kapcsolatos adatok gyűjtésére. A bejövő hozzáférési jogkivonaton belüli jogcímek alapján a felhasználói utazás egy [Azure Active Directory technikai profilt](active-directory-technical-profile.md) hív meg a felhasználó adatainak lekéréséhez, például a felhasználó objectId való beolvasásához. 
  - **Opcionális** előkészítési lépések – további előkészítési lépéseket adhat hozzá, például egy REST API technikai profilt a felhasználóval kapcsolatos további információk lekéréséhez. 
  - **UserInfo-kiállító** – az UserInfo-végpont által visszaadott jogcímek listáját adja meg.

## <a name="create-a-userinfo-endpoint"></a>UserInfo-végpont létrehozása

### <a name="1-add-the-token-issuer-technical-profile"></a>1. a jogkivonat kiállítói technikai profiljának hozzáadása

1. Nyissa meg a *TrustFrameworkExtensions.xml* fájlt.
1. Ha még nem létezik, vegyen fel egy ClaimsProvider elemet és annak alárendelt elemeit az BuildingBlocks elem alatti első elemként.
1. Adja hozzá a következő jogcím-szolgáltatót:

    ```xml
    <!-- 
    <ClaimsProviders> -->
      <ClaimsProvider>
        <DisplayName>Token Issuer</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="UserInfoIssuer">
            <DisplayName>JSON Issuer</DisplayName>
            <Protocol Name="None" />
            <OutputTokenFormat>JSON</OutputTokenFormat>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <!-- The Below claims are what will be returned on the UserInfo Endpoint if in the Claims Bag-->
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId"/>
              <InputClaim ClaimTypeReferenceId="givenName"/>
              <InputClaim ClaimTypeReferenceId="surname"/>
              <InputClaim ClaimTypeReferenceId="displayName"/>
              <InputClaim ClaimTypeReferenceId="signInNames.emailAddress"/>
            </InputClaims>
          </TechnicalProfile>
          <TechnicalProfile Id="UserInfoAuthorization">
            <DisplayName>UserInfo authorization</DisplayName>
            <Protocol Name="None" />
            <InputTokenFormat>JWT</InputTokenFormat>
            <Metadata>
              <!-- Update the Issuer and Audience below -->
              <!-- Audience is optional, Issuer is required-->
              <Item Key="issuer">https://yourtenant.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/</Item>
              <Item Key="audience">[ "22222222-2222-2222-2222-222222222222", "33333333-3333-3333-3333-333333333333" ]</Item>
              <Item Key="client_assertion_type">urn:ietf:params:oauth:client-assertion-type:jwt-bearer</Item>
            </Metadata>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
              <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" PartnerClaimType="email"/>
              <!-- Optional claims to read from the access token. -->
              <!-- <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
                 <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
                 <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/> -->
            </OutputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    <!-- 
    </ClaimsProviders> -->
    ``` 

1. A **UserInfoIssuer** technikai profiljában található szabályzattípushoz szakasz a visszaadni kívánt attribútumokat határozza meg. A UserInfoIssuer technikai profilt a felhasználói út végén hívja meg. 
1. A **UserInfoAuthorization** technikai profilja ellenőrzi az aláírást, a kiállító nevét és a jogkivonat célközönségét, és kibontja a jogcímet a bejövő jogkivonatból. Módosítsa a következő metaadatokat a környezetének megfelelően:
    1. **kiállító** – ennek az értéknek meg kell egyeznie a `iss` jogcímet a hozzáférési jogkivonat jogcímen belül. A Azure AD B2C által kiállított jogkivonatok a kibocsátót használják a formátumban `https://yourtenant.b2clogin.com/your-tenant-id/v2.0/` . További információ a [token testreszabásáról](configure-tokens.md).
    1. **IdTokenAudience** – `aud` a hozzáférési jogkivonat jogcímen belüli jogcímet meg kell egyeznie. Azure AD B2C a `aud` jogcím a függő entitás alkalmazásának azonosítója. Ez az érték egy gyűjtemény, amely vesszővel elválasztó karakter használatával több értéket is támogat.

        A következő hozzáférési jogkivonatban a `iss` jogcím értéke: `https://contoso.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/` . A `aud` jogcím értéke: `22222222-2222-2222-2222-222222222222` .

        ```json
        {
          "exp": 1605549468,
          "nbf": 1605545868,
          "ver": "1.0",
          "iss": "https://contoso.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/",
          "sub": "44444444-4444-4444-4444-444444444444",
          "aud": "22222222-2222-2222-2222-222222222222",
          "acr": "b2c_1a_signup_signin",
          "nonce": "defaultNonce",
          "iat": 1605545868,
          "auth_time": 1605545868,
          "name": "John Smith",
          "given_name": "John",
          "family_name": "Smith",
          "tid": "11111111-1111-1111-1111-111111111111"
        }
        ```
    
1.  A **UserInfoAuthorization** műszaki profil OutputClaims eleme határozza meg a hozzáférési tokenből beolvasni kívánt attribútumokat. A **ClaimTypeReferenceId** a jogcím típusára mutató hivatkozás. A választható **PartnerClaimType** a hozzáférési jogkivonatban definiált jogcím neve.



### <a name="2-add-the-userjourney-element"></a>2. adja hozzá a UserJourney elemet 

A [UserJourney](userjourneys.md) elem azt az elérési utat határozza meg, amelyet a felhasználó az alkalmazással való interakció során használ. Adja hozzá a **UserJourneys** elemet, ha az nem létezik a következőképpen azonosított **UserJourney** `UserInfoJourney` :

```xml
<!-- 
<UserJourneys> -->
  <UserJourney Id="UserInfoJourney" DefaultCpimIssuerTechnicalProfileReferenceId="UserInfoIssuer">
    <Authorization>
      <AuthorizationTechnicalProfiles>
        <AuthorizationTechnicalProfile ReferenceId="UserInfoAuthorization" />
      </AuthorizationTechnicalProfiles>
    </Authorization>
    <OrchestrationSteps >
      <OrchestrationStep Order="1" Type="ClaimsExchange">
        <Preconditions>
          <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
            <Value>objectId</Value>
            <Action>SkipThisOrchestrationStep</Action>
          </Precondition>
        </Preconditions>
        <ClaimsExchanges UserIdentity="false">
          <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="2" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="UserInfoIssuer" />
    </OrchestrationSteps>
  </UserJourney>
<!-- 
</UserJourneys> -->
```

### <a name="3-include-the-endpoint-to-the-relying-party-policy"></a>3. a végpont belefoglalása a függő entitás házirendjébe

Ha a UserInfo-végpontot szeretné felvenni a függő entitás alkalmazásba, adjon hozzá egy [végpont](relyingparty.md#endpoints) elemet a *SocialAndLocalAccounts/SignUpOrSignIn.xml* fájlhoz. 

```xml
<!--
<RelyingParty> -->
  <Endpoints>
    <Endpoint Id="UserInfo" UserJourneyReferenceId="UserInfoJourney" />
  </Endpoints>
<!-- 
</RelyingParty> -->
```

A függő entitás kitöltött eleme a következő lesz:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0" TenantId="yourtenant.onmicrosoft.com" PolicyId="B2C_1A_signup_signin" PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin">
  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <Endpoints>
      <Endpoint Id="UserInfo" UserJourneyReferenceId="UserInfoJourney" />
    </Endpoints>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

### <a name="4-upload-the-files"></a>4. Töltse fel a fájlokat

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. Válassza az **identitási élmény keretrendszert**.
1. Az **Egyéni házirendek** lapon válassza az **egyéni házirend feltöltése** lehetőséget.
1. **Ha már létezik, válassza az egyéni házirend felülírása** lehetőséget, majd keresse meg és válassza ki a *TrustframeworkExtensions.xml* fájlt.
1. Kattintson a **Feltöltés** gombra.
1. Ismételje meg az 5 – 7. lépést a függő entitás fájljánál, például *SignUpOrSignIn.xml*.

## <a name="calling-the-userinfo-endpoint"></a>Az UserInfo-végpont meghívása

Az UserInfo-végpont a standard OAuth2 tulajdonosi jogkivonat API-t használja, amelyet az alkalmazáshoz tartozó jogkivonat beszerzése során kapott hozzáférési jogkivonat használ. Egy JSON-választ ad vissza, amely a felhasználóval kapcsolatos jogcímeket tartalmaz. A UserInfo végpont Azure AD B2C a következő helyen található:

```http
https://yourtenant.b2clogin.com/yourtenant.onmicrosoft.com/policy-name/openid/v2.0/userinfo
```

A/.well-known konfigurálása végpont (OpenID Connect Discovery-dokumentum) felsorolja a `userinfo_endpoint` mezőt. Az UserInfo végpont programozott módon is felderíthető a/.well-known configure végpont használatával: 

```http
https://yourtenant.b2clogin.com/yourtenant.onmicrosoft.com/policy-name/v2.0/.well-known/openid-configuration 
```

### <a name="test-the-policy"></a>A szabályzat tesztelése

1. Az **Egyéni házirendek** területen válassza ki azt a szabályzatot, amelyhez az UserInfo-végpontot integrálni kívánja. Például *B2C_1A_SignUpOrSignIn*.
1. Válassza a **Futtatás most** lehetőséget. 
1. Az **alkalmazás kiválasztása** területen válassza ki a korábban regisztrált alkalmazást. Válassza a **Válasz URL-cím kiválasztása** lehetőséget `https://jwt.ms` . További információ: [webalkalmazás regisztrálása a Azure Active Directory B2Cban](tutorial-register-applications.md).
1. Regisztráljon, vagy jelentkezzen be egy e-mail-címmel vagy egy közösségi fiókkal.
1. Másolja a id_token a webhelyről származó kódolt formátumban [https://jwt.ms](https://jwt.ms) . Ezzel elküldheti a GET kérelmet a UserInfo-végpontnak, és lekérheti a felhasználói adatokat.
1. Küldje el a GET kérelmet a UserInfo-végpontnak, és kérje le a felhasználói adatokat.

```http
GET /yourtenant.onmicrosoft.com/b2c_1a_signup_signin/openid/v2.0/userinfo
Host: b2cninja.b2clogin.com
Authorization: Bearer <your access token>
```

A sikeres válasz A következőképpen néz ki:

```json
{
    "objectId": "44444444-4444-4444-4444-444444444444",
    "givenName": "John",
    "surname": "Smith",
    "displayName": "John Smith",
    "signInNames.emailAddress": "john.s@contoso.com"
}
```

## <a name="next-steps"></a>Következő lépések

- A [githubon](https://github.com/azure-ad-b2c/samples/tree/master/policies/user-info-endpoint)megtalálhatja például a UserInfo-végponti szabályzatot.

::: zone-end
