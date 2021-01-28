---
title: Bejelentkezés beállítása Azure AD-szervezetekhez
titleSuffix: Azure AD B2C
description: Be kell állítania a bejelentkezést egy adott Azure Active Directory szervezet számára a Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/27/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit, project-no-code
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 8a3cca7740adb6fa44b162e8c8740d1be1c7aa6b
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98953886"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Bejelentkezés beállítása egy adott Azure Active Directory szervezet számára Azure Active Directory B2C

Ez a cikk bemutatja, hogyan engedélyezheti a bejelentkezést egy adott Azure AD-szervezet felhasználóinak a Azure AD B2C felhasználói folyamatával.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="register-an-azure-ad-app"></a>Azure AD-alkalmazás regisztrálása

Ha engedélyezni szeretné a bejelentkezést az Azure AD-fiókkal rendelkező felhasználók számára egy adott Azure AD-szervezetből, Azure Active Directory B2C (Azure AD B2C), létre kell hoznia egy alkalmazást a [Azure Portal](https://portal.azure.com). További információ: [alkalmazás regisztrálása a Microsoft Identity platformmal](../active-directory/develop/quickstart-register-app.md).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Győződjön meg arról, hogy a szervezeti Azure AD-bérlőt (például contoso.com) tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés szűrőt** a felső menüben, majd válassza ki azt a könyvtárat, amely tartalmazza az Azure ad-bérlőt.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Alkalmazásregisztrációk**.
1. Válassza az **új regisztráció** lehetőséget.
1. Adja meg az alkalmazás **nevét** . Például: `Azure AD B2C App`.
1. Fogadja el a fiókok alapértelmezett kiválasztását ebben a **szervezeti könyvtárban csak** ehhez az alkalmazáshoz.
1. Az **átirányítási URI** esetében fogadja el a **web** értékét, és írja be az alábbi URL-címet minden kisbetűvel, ahol a `your-B2C-tenant-name` helyére a Azure ad B2C bérlő neve kerül.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Például: `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

1. Válassza a **Regisztráció** lehetőséget. Jegyezze fel az **alkalmazás (ügyfél) azonosítóját** egy későbbi lépésben való használatra.
1. Válassza a **tanúsítványok & titkok** lehetőséget, majd válassza az **új ügyfél titka** lehetőséget.
1. Adja meg a titok **leírását** , válasszon ki egy lejáratot, majd kattintson a **Hozzáadás** gombra. Jegyezze fel a titok **értékét** egy későbbi lépésben való használatra.

### <a name="configuring-optional-claims"></a>Választható jogcímek konfigurálása

Ha a `family_name` és a `given_name` jogcímeket az Azure ad-től szeretné beszerezni, az alkalmazáshoz a Azure Portal felhasználói felületén vagy az alkalmazás-jegyzékfájlban konfigurálhat választható jogcímeket is. További információkért lásd: [opcionális jogcímek megadása az Azure ad-alkalmazáshoz](../active-directory/develop/active-directory-optional-claims.md).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Keresse meg és válassza ki az **Azure Active Directoryt**.
1. A **kezelés** szakaszban válassza a **Alkalmazásregisztrációk** lehetőséget.
1. Válassza ki azt az alkalmazást, amelyhez választható jogcímeket szeretne konfigurálni a listában.
1. A **kezelés** szakaszban válassza a **jogkivonat-konfiguráció** elemet.
1. Válassza a **választható jogcím hozzáadása** lehetőséget.
1. A **jogkivonat típusa** beállításnál válassza az **azonosító** lehetőséget.
1. Válassza ki a hozzáadandó választható jogcímeket, `family_name` és `given_name` .
1. Kattintson a **Hozzáadás** parancsra.

::: zone pivot="b2c-user-flow"

## <a name="configure-azure-ad-as-an-identity-provider"></a>Az Azure AD konfigurálása identitás-szolgáltatóként

1. Győződjön meg arról, hogy a Azure AD B2C bérlőt tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. Válassza az **identitás-szolgáltatók**, majd az **új OpenID Connect Provider** lehetőséget.
1. Adjon meg egy **nevet**. Adja meg például a *contoso Azure ad*-t.
1. A **metaadatok URL-címéhez** adja meg az alábbi URL-címet, `{tenant}` amely az Azure ad-bérlő tartománynevét helyettesíti:

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    Például: `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.
    Például: `https://login.microsoftonline.com/contoso.com/v2.0/.well-known/openid-configuration`.

1. Az **ügyfél-azonosító** mezőben adja meg a korábban rögzített alkalmazás azonosítóját.
1. Az **ügyfél titkos kulcsa** mezőben adja meg a korábban rögzített ügyfél-titkot.
1. A **hatókörben** adja meg a következőt: `openid profile` .
1. Hagyja meg a **Válasz típusa** és a **válasz mód** alapértelmezett értékeit.
1. Választható A **tartományhoz tartozó tipp** esetében adja meg a következőt: `contoso.com` . További információ: [közvetlen bejelentkezés beállítása Azure Active Directory B2C használatával](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. Az **Identity Provider jogcímek leképezése** területen válassza ki a következő jogcímeket:

    - **Felhasználói azonosító**: *OID*
    - **Megjelenítendő név**: *név*
    - **Utónév**: *given_name*
    - **Vezetéknév**: *family_name*
    - **E-mail**: *preferred_username*

1. Kattintson a **Mentés** gombra.

## <a name="add-azure-ad-identity-provider-to-a-user-flow"></a>Azure AD Identity Provider hozzáadása felhasználói folyamathoz 

1. A Azure AD B2C-bérlőben válassza a **felhasználói folyamatok** lehetőséget.
1. Kattintson arra a felhasználói folyamatra, amelyhez hozzá szeretné adni az Azure AD-identitás szolgáltatóját.
1. A **közösségi identitás-szolgáltatók** területen válassza a **contoso Azure ad** elemet.
1. Kattintson a **Mentés** gombra.
1. A szabályzat teszteléséhez válassza a **felhasználói folyamat futtatása** lehetőséget.
1. Az **alkalmazás** lapon válassza ki a korábban regisztrált *testapp1* nevű webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Kattintson a **felhasználói folyamat futtatása** elemre.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Házirend-kulcs létrehozása

A Azure AD B2C bérlőben létrehozott alkalmazás-kulcsot kell tárolnia.

1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés szűrőt** a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. A **szabályzatok** területen válassza az **identitási élmény keretrendszere** elemet.
1. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás** gombra.
1. A **Beállítások** területen válassza a lehetőséget `Manual` .
1. Adja meg a szabályzat kulcsának **nevét** . Például: `ContosoAppSecret`.  A rendszer `B2C_1A_` automatikusan hozzáadja az előtagot a kulcs neveként a létrehozáskor, ezért a következő szakaszban található XML-ben szereplő hivatkozás a *B2C_1A_ContosoAppSecret*.
1. A **Secret (titkos kulcs**) mezőben adja meg a korábban feljegyzett ügyfél-titkot.
1. A **kulcshasználat** beállításnál válassza a elemet `Signature` .
1. Válassza a **Létrehozás** lehetőséget.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Az Azure AD konfigurálása identitás-szolgáltatóként

Annak engedélyezéséhez, hogy a felhasználók Azure AD-fiókkal jelentkezzenek be, az Azure AD-t olyan jogcím-szolgáltatóként kell megadnia, amely Azure AD B2C tud kommunikálni egy végponton keresztül. A végpont olyan jogcímeket biztosít, amelyeket a Azure AD B2C használ annak ellenőrzéséhez, hogy egy adott felhasználó hitelesítve van-e.

Az Azure AD-t jogcím-szolgáltatóként is megadhatja, ha hozzáadja az Azure AD-t az **ClaimsProvider** elemhez a szabályzat bővítmény fájljában.

1. Nyissa meg a *TrustFrameworkExtensions.xml* fájlt.
2. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a gyökérelem elemhez.
3. Vegyen fel egy új **ClaimsProvider** a következőképpen:
    ```xml
    <ClaimsProvider>
      <Domain>Contoso</Domain>
      <DisplayName>Login using Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AADContoso-OpenIdConnect">
          <DisplayName>Contoso Employee</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration</Item>
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
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

4. A **ClaimsProvider** elem alatt frissítse a **tartomány** értékét egy olyan egyedi értékre, amelyet a más identitás-szolgáltatóktól való megkülönböztetéshez használhat. Például: `Contoso`. A tartomány végén nem kerül be a érték `.com` .
5. A **ClaimsProvider** elem alatt frissítse a **DisplayName** értékét a jogcím-szolgáltató rövid nevére. Ez az érték jelenleg nincs használatban.

### <a name="update-the-technical-profile"></a>A technikai profil frissítése

Az Azure AD-végponttól kapott jogkivonat lekéréséhez meg kell határoznia azokat a protokollokat, amelyeket az Azure AD B2C az Azure AD-vel való kommunikációhoz használnia kell. Ez a **ClaimsProvider** **kivonatjogcím** elemén belül történik.

1. Frissítse a **kivonatjogcím** elem azonosítóját. Ezzel az AZONOSÍTÓval hivatkozhat erre a műszaki profilra a szabályzat más részeiről, például: `AADContoso-OpenIdConnect` .
1. A **DisplayName** értékének frissítése. Ez az érték a bejelentkezési képernyőjén a bejelentkezés gombján jelenik meg.
1. A **Leírás** értékének frissítése.
1. Az Azure AD az OpenID Connect protokollt használja, ezért győződjön meg arról, hogy a **protokoll** értéke `OpenIdConnect` .
1. Állítsa be a **metaadatok** értékét a értékre `https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration` , ahol `tenant-name` az az Azure ad-bérlő neve. Például: `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`
1. **Client_id** beállítása az alkalmazás-azonosítóhoz az alkalmazás regisztrációja során.
1. A **CryptographicKeys** alatt frissítse a **StorageReferenceId** értékét a korábban létrehozott házirend-kulcs nevére. Például: `B2C_1A_ContosoAppSecret`.


[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADContosoExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AzureADContosoExchange" TechnicalProfileReferenceId="AADContoso-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

[!INCLUDE [active-directory-b2c-test-relying-party-policy](../../includes/active-directory-b2c-test-relying-party-policy-user-journey.md)]

## <a name="next-steps"></a>Következő lépések

Egyéni szabályzatok használatakor előfordulhat, hogy további információra van szüksége, amikor a fejlesztés során hibaelhárítást végez a házirendben.

A problémák diagnosztizálásához ideiglenesen a "fejlesztői módba" helyezheti a szabályzatot, és összegyűjtheti a naplókat az Azure Application Insights használatával. Ismerje meg, hogyan történik a [Azure Active Directory B2C: naplók gyűjtése](troubleshoot-with-application-insights.md).

::: zone-end