---
title: Bejelentkezés beállítása a több-bérlős Azure AD-hoz egyéni házirendek alapján
titleSuffix: Azure AD B2C
description: Több-bérlős Azure AD-identitás-szolgáltató hozzáadása a Azure Active Directory B2Cban található egyéni házirendek használatával.
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
ms.openlocfilehash: 5089a289e617aa8c2ec153320763647fc8afac9e
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103489036"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Több-bérlős Azure Active Directory bejelentkezésének beállítása egyéni házirendek használatával Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Ez a cikk bemutatja, hogyan engedélyezheti a bejelentkezést a Azure Active Directory (Azure AD) több-bérlős végpontját használó felhasználók számára. Lehetővé teszi, hogy több Azure AD-bérlő felhasználói jelentkezzenek be Azure AD B2C használatával, anélkül, hogy az egyes bérlők számára kellene konfigurálnia az identitás-szolgáltatót. Ezeknek a bérlőknek a vendég tagjai azonban nem tudnak **majd** bejelentkezni. Ehhez [külön kell konfigurálnia az egyes bérlőket](identity-provider-azure-ad-single-tenant.md).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="register-an-application"></a>Egy alkalmazás regisztrálása

Ha Azure Active Directory B2C (Azure AD B2C) Azure AD-fiókkal rendelkező felhasználók számára szeretné engedélyezni a bejelentkezést, létre kell hoznia egy alkalmazást a [Azure Portalban](https://portal.azure.com). További információ: [alkalmazás regisztrálása a Microsoft Identity platformmal](../active-directory/develop/quickstart-register-app.md).


1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Győződjön meg arról, hogy a szervezeti Azure AD-bérlőt (például contoso.com) tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés szűrőt** a felső menüben, majd válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Alkalmazásregisztrációk**.
1. Válassza az **új regisztráció** lehetőséget.
1. Adja meg az alkalmazás **nevét** . Például: `Azure AD B2C App`.
1. Válassza a **fiókok lehetőséget bármely szervezeti címtárban (bármely Azure ad-címtár – több-bérlő)** ehhez az alkalmazáshoz.
1. Az **átirányítási URI** esetében fogadja el a **web** értékét, és írja be az alábbi URL-címet minden kisbetűvel, ahol a `your-B2C-tenant-name` helyére a Azure ad B2C bérlő neve kerül.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Például: `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

    Ha [Egyéni tartományt](custom-domain.md)használ, írja be a értéket `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Cserélje le az `your-domain-name` -t az egyéni tartományra, és `your-tenant-name` a bérlő nevét.

1. Válassza a **Regisztráció** lehetőséget. Jegyezze fel az **alkalmazás (ügyfél) azonosítóját** egy későbbi lépésben való használatra.
1. Válassza a **tanúsítványok & titkok** lehetőséget, majd válassza az **új ügyfél titka** lehetőséget.
1. Adja meg a titok **leírását** , válasszon ki egy lejáratot, majd kattintson a **Hozzáadás** gombra. Jegyezze fel a titok **értékét** egy későbbi lépésben való használatra.

## <a name="configuring-optional-claims"></a>Választható jogcímek konfigurálása

Ha a `family_name` és az Azure ad-beli `given_name` jogcímeket szeretné beszerezni, a Azure Portal felhasználói felületén vagy az alkalmazás jegyzékfájljában konfigurálhatja az alkalmazáshoz választható jogcímeket is. További információkért lásd: [opcionális jogcímek megadása az Azure ad-alkalmazáshoz](../active-directory/develop/active-directory-optional-claims.md).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Keresse meg és válassza ki az **Azure Active Directoryt**.
1. A **kezelés** szakaszban válassza a **Alkalmazásregisztrációk** lehetőséget.
1. Válassza ki azt az alkalmazást, amelyhez választható jogcímeket szeretne konfigurálni a listában.
1. A **kezelés** szakaszban válassza a **jogkivonat-konfiguráció** elemet.
1. Válassza a **választható jogcím hozzáadása** lehetőséget.
1. A **jogkivonat típusa** beállításnál válassza az **azonosító** lehetőséget.
1. Válassza ki a hozzáadni kívánt jogcímeket, `family_name` és `given_name` .
1. Kattintson a **Hozzáadás** parancsra.

## <a name="create-a-policy-key"></a>Házirend-kulcs létrehozása

A Azure AD B2C bérlőben létrehozott alkalmazás-kulcsot kell tárolnia.

1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés szűrőt** a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. A **szabályzatok** területen válassza az **identitási élmény keretrendszere** elemet.
1. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás** gombra.
1. A **Beállítások** területen válassza a lehetőséget `Manual` .
1. Adja meg a szabályzat kulcsának **nevét** . Például: `AADAppSecret`.  A rendszer `B2C_1A_` automatikusan hozzáadja az előtagot a kulcs neveként a létrehozáskor, ezért a következő szakaszban található XML-ben szereplő hivatkozás a *B2C_1A_AADAppSecret*.
1. A **Secret (titkos kulcs**) mezőben adja meg a korábban feljegyzett ügyfél-titkot.
1. A **kulcshasználat** beállításnál válassza a elemet `Signature` .
1. Válassza a **Létrehozás** lehetőséget.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Az Azure AD konfigurálása identitás-szolgáltatóként

Annak engedélyezéséhez, hogy a felhasználók Azure AD-fiókkal jelentkezzenek be, az Azure AD-t olyan jogcím-szolgáltatóként kell megadnia, amely Azure AD B2C tud kommunikálni egy végponton keresztül. A végpont olyan jogcímeket biztosít, amelyeket a Azure AD B2C használ annak ellenőrzéséhez, hogy egy adott felhasználó hitelesítve van-e.

Az Azure AD-t jogcím-szolgáltatóként is megadhatja, ha hozzáadja az Azure AD-t az **ClaimsProvider** elemhez a szabályzat bővítmény fájljában.

1. Nyissa meg a *TrustFrameworkExtensions.xml* fájlt.
1. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a gyökérelem elemhez.
1. Vegyen fel egy új **ClaimsProvider** a következőképpen:

    ```xml
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AADCommon-OpenIdConnect">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000,https://login.microsoftonline.com/11111111-1111-1111-1111-111111111111</Item>
            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. A **ClaimsProvider** elem alatt frissítse a **tartomány** értékét egy olyan egyedi értékre, amelyet a más identitás-szolgáltatóktól való megkülönböztetéshez használhat.
1. A **kivonatjogcím** elem alatt frissítse a **DisplayName** értéket, például: `Contoso Employee` . Ez az érték jelenik meg a bejelentkezési oldalon a bejelentkezés gombon.
1. **Client_id** beállítása a korábban regisztrált Azure ad több-bérlős alkalmazás alkalmazás-azonosítójára.
1. A **CryptographicKeys** alatt frissítse a **StorageReferenceId** értékét a korábban létrehozott házirend-kulcs nevére. Például: `B2C_1A_AADAppSecret`.

### <a name="restrict-access"></a>Hozzáférés korlátozása

A `https://login.microsoftonline.com/` **ValidTokenIssuerPrefixes** értékének használata lehetővé teszi, hogy az összes Azure ad-felhasználó bejelentkezzen az alkalmazásba. Frissítse az érvényes jogkivonat-kiállítók listáját, és korlátozza a hozzáférést az Azure AD-bérlő azon felhasználói számára, akik be tudnak jelentkezni.

Az értékek beszerzéséhez tekintse meg az OpenID Connect Discovery metaadatait minden olyan Azure AD-bérlőhöz, amelyhez be szeretné jelentkezni a felhasználóktól. A metaadatok URL-címének formátuma hasonló a következőhöz: `https://login.microsoftonline.com/your-tenant/v2.0/.well-known/openid-configuration` , ahol `your-tenant` az az Azure ad-bérlő neve. Például:

`https://login.microsoftonline.com/fabrikam.onmicrosoft.com/v2.0/.well-known/openid-configuration`

Hajtsa végre ezeket a lépéseket minden olyan Azure AD-bérlő esetében, amelyet a bejelentkezéshez használnia kell:

1. Nyissa meg a böngészőt, és lépjen a bérlő OpenID Connect metadata URL-címére. Keresse meg a **kiállító** objektumot, és jegyezze fel az értékét. A következőhöz hasonlóan kell kinéznie: `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/` .
1. Másolja és illessze be az értéket a **ValidTokenIssuerPrefixes** kulcsba. Több kiállítót vesszővel kell elválasztani. Az előző `ClaimsProvider` XML-mintában két kiállító látható.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADCommonExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AzureADCommonExchange" TechnicalProfileReferenceId="AADCommon-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Egyéni szabályzat tesztelése

1. Válassza ki a függő entitás házirendjét, például: `B2C_1A_signup_signin` .
1. **Alkalmazás** esetén válasszon ki egy [korábban regisztrált](troubleshoot-custom-policies.md#troubleshoot-the-runtime)webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Kattintson a **Futtatás most** gombra.
1. A regisztrációs vagy bejelentkezési oldalon válassza az **általános HRE** lehetőséget az Azure ad-fiókkal való bejelentkezéshez.

A több-bérlős bejelentkezési képesség teszteléséhez hajtsa végre az utolsó két lépést egy másik Azure AD-bérlőt használó felhasználó hitelesítő adataival. Másolja a **Futtatás most végpontot** , és nyissa meg egy privát böngészőablakban, például: Incognito mód a Google Chrome-ban vagy egy InPrivate-ablak a Microsoft Edge-ben. A privát böngészőablak megnyitása lehetővé teszi a teljes felhasználói út tesztelését, ha nem használja a jelenleg gyorsítótárazott Azure AD-beli hitelesítő adatokat.

Ha a bejelentkezési folyamat sikeres, a rendszer átirányítja a böngészőt `https://jwt.ms` , amely a Azure ad B2C által visszaadott jogkivonat tartalmát jeleníti meg.

## <a name="next-steps"></a>Következő lépések

Egyéni szabályzatok használatakor előfordulhat, hogy további információra van szüksége, amikor a fejlesztés során hibaelhárítást végez a házirendben.

A problémák diagnosztizálásához ideiglenesen a "fejlesztői módba" helyezheti a szabályzatot, és összegyűjtheti a naplókat az Azure Application Insights használatával. Ismerje meg, hogyan történik a [Azure Active Directory B2C: naplók gyűjtése](troubleshoot-with-application-insights.md).

::: zone-end
