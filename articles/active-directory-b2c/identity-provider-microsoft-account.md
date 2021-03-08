---
title: Regisztráció és bejelentkezés beállítása Microsoft-fiókkal
titleSuffix: Azure AD B2C
description: Az alkalmazásokban Microsoft-fiókkal rendelkező ügyfelek számára Azure Active Directory B2C használatával regisztrálhat és regisztrálhat.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 0d8ba3fc578580e6eee02f435272f4ea53523586
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102448217"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Regisztráció és bejelentkezés beállítása Microsoft-fiók használatával Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-microsoft-account-application"></a>Microsoft-fiók-alkalmazás létrehozása

Ha engedélyezni szeretné a bejelentkezést a Azure Active Directory B2C (Azure AD B2C) Microsoft-fiók rendelkező felhasználók számára, létre kell hoznia egy alkalmazást a [Azure Portalban](https://portal.azure.com). További információ: [alkalmazás regisztrálása a Microsoft Identity platformmal](../active-directory/develop/quickstart-register-app.md). Ha még nem rendelkezik Microsoft-fiókval, beszerezhet egyet [https://www.live.com/](https://www.live.com/) .

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Győződjön meg arról, hogy az Azure AD-bérlőt tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki az Azure ad-bérlőt tartalmazó könyvtárat.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Alkalmazásregisztrációk**.
1. Válassza az **új regisztráció** lehetőséget.
1. Adja meg az alkalmazás **nevét** . Például: *MSAapp1*.
1. A **támogatott fióktípus** területen válassza a **fiókok lehetőséget bármely szervezeti címtárban (bármely Azure ad-címtár-több-bérlős) és a személyes Microsoft-fiókokat (például Skype, Xbox)**.

   A különböző fióktípus-beállításokkal kapcsolatos további információkért tekintse meg a rövid útmutató [: alkalmazás regisztrálása a Microsoft Identity platformmal](../active-directory/develop/quickstart-register-app.md)című témakört.
1. Az **átirányítási URI (nem kötelező)** területen válassza a **web** lehetőséget, és írja be `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/oauth2/authresp` a szövegmezőbe. Cserélje le a helyére a `<tenant-name>` Azure ad B2C bérlő nevét.
1. **Regisztráció** kiválasztása
1. Jegyezze fel az alkalmazás – Áttekintés lapon megjelenő **alkalmazást (ügyfél-azonosítót)** . Az ügyfél-AZONOSÍTÓra akkor van szükség, amikor a következő szakaszban konfigurálja az identitás-szolgáltatót.
1. **Tanúsítványok kiválasztása & Secrets**
1. Kattintson az **Új titkos ügyfélkód** elemre.
1. Adja meg a titok **leírását** , például az *1. alkalmazás jelszava*, majd kattintson a **Hozzáadás** gombra.
1. Jegyezze fel az **érték** oszlopban látható alkalmazás jelszavát. Az identitás-szolgáltató konfigurálásához a következő szakaszban az ügyfél titkos kulcsa szükséges.

::: zone pivot="b2c-user-flow"

## <a name="configure-microsoft-as-an-identity-provider"></a>A Microsoft identitás-szolgáltatóként való konfigurálása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
1. Válassza az **identitás-szolgáltatók**, majd a Microsoft- **fiók** lehetőséget.
1. Adjon meg egy **nevet**. Például: *MSA*.
1. Az **ügyfél-azonosító** mezőben adja meg a korábban létrehozott Azure ad-alkalmazás alkalmazás-(ügyfél-) azonosítóját.
1. Az **ügyfél titka** mezőben adja meg a rögzített ügyfél-titkot.
1. Kattintson a **Mentés** gombra.

## <a name="add-microsoft-identity-provider-to-a-user-flow"></a>Microsoft Identity Provider hozzáadása felhasználói folyamathoz 

1. A Azure AD B2C-bérlőben válassza a **felhasználói folyamatok** lehetőséget.
1. Kattintson arra a felhasználói folyamatra, amelyhez hozzá szeretné adni a Microsoft Identity providert.
1. A **közösségi identitás-szolgáltatók** területen válassza a **Microsoft-fiók** lehetőséget.
1. Kattintson a **Mentés** gombra.
1. A szabályzat teszteléséhez válassza a **felhasználói folyamat futtatása** lehetőséget.
1. Az **alkalmazás** lapon válassza ki a korábban regisztrált *testapp1* nevű webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Kattintson a **felhasználói folyamat futtatása** gombra.
1. A regisztráció vagy bejelentkezés lapon válassza a **Microsoft** lehetőséget a Microsoft-fiók való bejelentkezéshez.

Ha a bejelentkezési folyamat sikeres, a rendszer átirányítja a böngészőt `https://jwt.ms` , amely a Azure ad B2C által visszaadott jogkivonat tartalmát jeleníti meg.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="configuring-optional-claims"></a>Választható jogcímek konfigurálása

Ha a `family_name` és a `given_name` jogcímeket az Azure ad-től szeretné beszerezni, az alkalmazáshoz a Azure Portal felhasználói felületén vagy az alkalmazás-jegyzékfájlban konfigurálhat választható jogcímeket is. További információkért lásd: [opcionális jogcímek megadása az Azure ad-alkalmazáshoz](../active-directory/develop/active-directory-optional-claims.md).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Keresse meg és válassza ki az **Azure Active Directoryt**.
1. A **kezelés** szakaszban válassza a **Alkalmazásregisztrációk** lehetőséget.
1. Válassza ki azt az alkalmazást, amelyhez választható jogcímeket szeretne konfigurálni a listában.
1. A **kezelés** szakaszban válassza a **jogkivonat-konfiguráció (előzetes verzió)** lehetőséget.
1. Válassza a **választható jogcím hozzáadása** lehetőséget.
1. Válassza ki a konfigurálni kívánt jogkivonat-típust.
1. Válassza ki a hozzáadandó választható jogcímeket.
1. Kattintson a **Hozzáadás** parancsra.

## <a name="create-a-policy-key"></a>Házirend-kulcs létrehozása

Most, hogy létrehozta az alkalmazást az Azure AD-bérlőben, az alkalmazás ügyfél-titkos kulcsát az Azure AD B2C-bérlőben kell tárolnia.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. Az Áttekintés lapon válassza az **identitási élmény keretrendszert**.
1. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás** gombra.
1. A **Beállítások** területen válassza a lehetőséget `Manual` .
1. Adja meg a szabályzat kulcsának **nevét** . Például: `MSASecret`. A rendszer automatikusan hozzáadja az előtagot a `B2C_1A_` kulcs nevéhez.
1. A **Secret (titkos kulcs**) mezőben adja meg az előző szakaszban rögzített ügyfél-titkos kulcsot.
1. A **kulcshasználat** beállításnál válassza a elemet `Signature` .
1. Kattintson a **Létrehozás** lehetőségre.

## <a name="configure-microsoft-as-an-identity-provider"></a>A Microsoft identitás-szolgáltatóként való konfigurálása

Annak engedélyezéséhez, hogy a felhasználók egy Microsoft-fiók használatával jelentkezzenek be, meg kell adnia a fiókot jogcím-szolgáltatóként, amely Azure AD B2C tud kommunikálni egy végponton keresztül. A végpont olyan jogcímeket biztosít, amelyeket a Azure AD B2C használ annak ellenőrzéséhez, hogy egy adott felhasználó hitelesítve van-e.

Az Azure AD-t jogcím-szolgáltatóként is meghatározhatja, ha hozzáadja a **ClaimsProvider** elemet a szabályzat fájlkiterjesztés-fájljához.

1. Nyissa meg a *TrustFrameworkExtensions.xml* házirend-fájlt.
1. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a gyökérelem elemhez.
1. Vegyen fel egy új **ClaimsProvider** a következőképpen:

    ```xml
    <ClaimsProvider>
      <Domain>live.com</Domain>
      <DisplayName>Microsoft Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="MSA-MicrosoftAccount-OpenIdConnect">
          <DisplayName>Microsoft Account</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">https://login.live.com</Item>
            <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your Microsoft application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
            <OutputClaim ClaimTypeReferenceId="email" />
          </OutputClaims>
          <OutputClaimsTransformations>
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

1. Cserélje le **client_id** értékét a korábban rögzített Azure ad-alkalmazás *alkalmazás-azonosítójával (ügyfél)* .
1. Mentse a fájlt.

Ezzel konfigurálta a szabályzatot, hogy Azure AD B2C tudja, hogyan kommunikálhat a Microsoft-fiók alkalmazással az Azure AD-ben.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-MicrosoftAccount-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Egyéni szabályzat tesztelése

1. Válassza ki a függő entitás házirendjét, például: `B2C_1A_signup_signin` .
1. **Alkalmazás** esetén válasszon ki egy [korábban regisztrált](troubleshoot-custom-policies.md#troubleshoot-the-runtime)webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Kattintson a **Futtatás most** gombra.
1. A regisztráció vagy bejelentkezés lapon válassza a **Microsoft** lehetőséget a Microsoft-fiók való bejelentkezéshez.

Ha a bejelentkezési folyamat sikeres, a rendszer átirányítja a böngészőt `https://jwt.ms` , amely a Azure ad B2C által visszaadott jogkivonat tartalmát jeleníti meg.

::: zone-end
