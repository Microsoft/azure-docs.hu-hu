---
title: Bejelentkezés beállítása Salesforce SAML-szolgáltatóval SAML protokoll használatával
titleSuffix: Azure AD B2C
description: Salesforce SAML-szolgáltatóval történő bejelentkezés beállítása SAML protokoll használatával Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: e740fdb9cd232892dadfe98c4d739759be66bf55
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488720"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-saml-protocol-in-azure-active-directory-b2c"></a>Salesforce SAML-szolgáltatóval való bejelentkezés beállítása SAML protokoll használatával Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"
[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk bemutatja, hogyan engedélyezheti a bejelentkezést egy Salesforce-szervezet felhasználói számára a Azure Active Directory B2C (Azure AD B2C) [Egyéni házirendjeivel](custom-policy-overview.md) . A bejelentkezést egy [SAML-identitás szolgáltatójának](identity-provider-generic-saml.md) egyéni szabályzatba való felvételével engedélyezheti.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]
- Ha még nem tette meg, regisztráljon egy [ingyenes Developer Edition-fiókra](https://developer.salesforce.com/signup). Ez a cikk a [Salesforce villám-élményt](https://developer.salesforce.com/page/Lightning_Experience_FAQ)használja.
- [Állítsa be a saját tartományát](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) a Salesforce-szervezet számára.

## <a name="set-up-salesforce-as-an-identity-provider"></a>Salesforce beállítása identitás-szolgáltatóként

1. [Jelentkezzen be a Salesforce](https://login.salesforce.com/).
2. A bal oldali menüben a **Beállítások** alatt bontsa ki az **identitás** csomópontot, majd válassza az **identitás-szolgáltató** elemet.
3. Válassza az **Identitáskezelő engedélyezése** lehetőséget.
4. A **tanúsítvány kiválasztása** területen válassza ki azt a tanúsítványt, amelyet a Salesforce használni szeretne a Azure ad B2Csal való kommunikációhoz. Használhatja az alapértelmezett tanúsítványt.
5. Kattintson a **Mentés** gombra.

### <a name="create-a-connected-app-in-salesforce"></a>Csatlakoztatott alkalmazás létrehozása a Salesforce-ben

1. Az **Identitáskezelő** lapon válassza a szolgáltatók a **csatlakoztatott alkalmazások használatával lehetőséget. Kattintson ide.**
2. Az **alapszintű információ** területen adja meg a csatlakoztatott alkalmazás szükséges értékeit.
3. A **webalkalmazás beállításai** területen jelölje be az **SAML engedélyezése** jelölőnégyzetet.
4. Az **entitás azonosítója** mezőben adja meg a következő URL-címet. Győződjön meg arról, hogy az értékét a `your-tenant` Azure ad B2C bérlő nevére cseréli.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

      [Egyéni tartomány](custom-domain.md)használatakor használja a következő formátumot:

      ```
      https://your-domain-name/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

6. Az **ACS URL-címe** mezőbe írja be a következő URL-címet. Győződjön meg arról, hogy az értékét a `your-tenant` Azure ad B2C bérlő nevére cseréli.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```

      [Egyéni tartomány](custom-domain.md)használatakor használja a következő formátumot:

      ```
      https://your-domain-name/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```

7. Görgessen a lista aljára, majd kattintson a **Mentés** gombra.

### <a name="get-the-metadata-url"></a>A metaadatok URL-címének beolvasása

1. A csatlakoztatott alkalmazás áttekintés lapján kattintson a **kezelés** elemre.
2. Másolja a metaadat- **felderítési végpont** értékét, majd mentse. Ezt a cikk későbbi részében fogja használni.

### <a name="set-up-salesforce-users-to-federate"></a>Salesforce-felhasználók beállítása a összevonása

1. A csatlakoztatott alkalmazás **kezelés** lapján kattintson a **profilok kezelése** lehetőségre.
2. Válassza ki a összevonása használni kívánt profilokat (vagy felhasználói csoportokat) Azure AD B2C. Rendszergazdaként jelölje be a rendszergazda jelölőnégyzetet, hogy **összevonása a Salesforce** -fiók használatával.

## <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

## <a name="create-a-policy-key"></a>Házirend-kulcs létrehozása

A Azure AD B2C bérlőben létrehozott tanúsítványt kell tárolnia.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Az Áttekintés lapon válassza az **identitási élmény keretrendszert**.
5. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás** gombra.
6. A **Beállítások** területen válassza a lehetőséget `Upload` .
7. Adja meg a szabályzat **nevét**. Például: SAMLSigningCert. Az előtag `B2C_1A_` automatikusan hozzáadódik a kulcs nevéhez.
8. Keresse meg és válassza ki a létrehozott B2CSigningCert. pfx-tanúsítványt.
9. Adja meg a tanúsítványhoz tartozó **jelszót** .
3. Kattintson a **Létrehozás** lehetőségre.

## <a name="add-a-claims-provider"></a>Jogcím-szolgáltató hozzáadása

Ha azt szeretné, hogy a felhasználók Salesforce-fiókkal jelentkezzenek be, meg kell adnia a fiókot jogcím-szolgáltatóként, amely Azure AD B2C tud kommunikálni egy végponton keresztül. A végpont olyan jogcímeket biztosít, amelyeket a Azure AD B2C használ annak ellenőrzéséhez, hogy egy adott felhasználó hitelesítve van-e.

A Salesforce-fiókot jogcím-szolgáltatóként is meghatározhatja, ha hozzáadja azt a **ClaimsProviders** elemhez a szabályzat fájlkiterjesztés fájljában. További információ: SAML- [identitás szolgáltatójának meghatározása](identity-provider-generic-saml.md).

1. Nyissa meg a *TrustFrameworkExtensions.xml*.
1. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a gyökérelem elemhez.
1. Vegyen fel egy új **ClaimsProvider** a következőképpen:

    ```xml
    <ClaimsProvider>
      <Domain>salesforce.com</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Salesforce-SAML2">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="salesforce.com" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Frissítse a **PartnerEntity** értékét a korábban átmásolt Salesforce metaadat URL-címével.
1. Frissítse a **StorageReferenceId** mindkét példányának értékét az aláíró tanúsítvány kulcsának nevére. Például B2C_1A_SAMLSigningCert.
1. Keresse meg a `<ClaimsProviders>` szakaszt, és adja hozzá a következő XML-kódrészletet. Ha a házirend már tartalmazza a `SM-Saml-idp` technikai profilt, ugorjon a következő lépésre. További információ: [egyszeri bejelentkezéses munkamenet-kezelés](custom-policy-reference-sso.md).

    ```xml
    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-Saml-idp">
          <DisplayName>Session Management Provider</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="IncludeSessionIndex">false</Item>
            <Item Key="RegisterServiceProviders">false</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```
1. Mentse a fájlt.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="Salesforce-SAML2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Egyéni szabályzat tesztelése

1. Válassza ki a függő entitás házirendjét, például: `B2C_1A_signup_signin` .
1. **Alkalmazás** esetén válasszon ki egy [korábban regisztrált](troubleshoot-custom-policies.md#troubleshoot-the-runtime)webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Kattintson a **Futtatás most** gombra.
1. A regisztráció vagy bejelentkezés lapon válassza a **Salesforce** lehetőséget a Salesforce-fiókkal való bejelentkezéshez.

Ha a bejelentkezési folyamat sikeres, a rendszer átirányítja a böngészőt `https://jwt.ms` , amely a Azure ad B2C által visszaadott jogkivonat tartalmát jeleníti meg.

::: zone-end