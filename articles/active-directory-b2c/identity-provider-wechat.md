---
title: Regisztráció és bejelentkezés beállítása WeChat-fiókkal
titleSuffix: Azure AD B2C
description: A Azure Active Directory B2C használatával WeChat-fiókkal rendelkező ügyfelek számára biztosítson regisztrációt és bejelentkezést az alkalmazásokban.
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
ms.openlocfilehash: c7538cf052fcf51d03d1ac854d4da50db02004ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103488559"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>WeChat-fiókkal történő regisztráció és bejelentkezés beállítása Azure Active Directory B2C


[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-wechat-application"></a>WeChat-alkalmazás létrehozása

Ha Azure Active Directory B2C (Azure AD B2C) WeChat-fiókkal rendelkező felhasználók számára szeretné engedélyezni a bejelentkezést, létre kell hoznia egy alkalmazást a [WeChat felügyeleti központban](https://open.weixin.qq.com/). Ha még nem rendelkezik WeChat-fiókkal, a következő címen kérhet információt: [https://kf.qq.com](https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html) .

### <a name="register-a-wechat-application"></a>WeChat-alkalmazás regisztrálása

1. Jelentkezzen be a [https://open.weixin.qq.com/](https://open.weixin.qq.com/) WeChat hitelesítő adataival.
1. Válassza a **管理中心** (felügyeleti központ) lehetőséget.
1. Új alkalmazás regisztrálásához kövesse az alábbi lépéseket.
1. A **授权回调域** (visszahívási URL-cím) mezőbe írja be a következőt: `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Ha [Egyéni tartományt](custom-domain.md)használ, írja be a értéket `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Cserélje le a `your-tenant-name` nevet a bérlő nevére, és az `your-domain-name` Egyéni tartományra.
1. Másolja az **alkalmazás azonosítóját** és az **alkalmazás kulcsát**. Mindkettőre szüksége van, hogy konfigurálja az identitás-szolgáltatót a bérlőhöz.

::: zone pivot="b2c-user-flow"

## <a name="configure-wechat-as-an-identity-provider"></a>WeChat konfigurálása identitás-szolgáltatóként

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
1. Válassza az **identitás-szolgáltatók**, majd a **WeChat (előzetes verzió)** lehetőséget.
1. Adjon meg egy **nevet**. Például: *WeChat*.
1. Az **ügyfél-azonosító** mezőben adja meg a korábban létrehozott WeChat-alkalmazás alkalmazás-azonosítóját.
1. Az **ügyfél titkos** kulcsa mezőben adja meg a rögzített alkalmazás kulcsát.
1. Kattintson a **Mentés** gombra.

## <a name="add-wechat-identity-provider-to-a-user-flow"></a>WeChat-identitás szolgáltatójának hozzáadása felhasználói folyamathoz 

1. A Azure AD B2C-bérlőben válassza a **felhasználói folyamatok** lehetőséget.
1. Kattintson arra a felhasználói folyamatra, amelyhez hozzá szeretné adni a WeChat-identitás szolgáltatóját.
1. A **közösségi identitás-szolgáltatók** területen válassza a **WeChat** lehetőséget.
1. Kattintson a **Mentés** gombra.
1. A szabályzat teszteléséhez válassza a **felhasználói folyamat futtatása** lehetőséget.
1. Az **alkalmazás** lapon válassza ki a korábban regisztrált *testapp1* nevű webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Kattintson a **felhasználói folyamat futtatása** gombra.
1. A regisztráció vagy bejelentkezés lapon válassza a **WeChat** lehetőséget a WeChat-fiókkal való bejelentkezéshez.

Ha a bejelentkezési folyamat sikeres, a rendszer átirányítja a böngészőt `https://jwt.ms` , amely a Azure ad B2C által visszaadott jogkivonat tartalmát jeleníti meg.

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
7. Adja meg a szabályzat kulcsának **nevét** . Például: `WeChatSecret`. A rendszer automatikusan hozzáadja az előtagot a `B2C_1A_` kulcs nevéhez.
8. A **Secret (titkos kulcs**) mezőben adja meg a korábban rögzített ügyfél-titkot.
9. A **kulcshasználat** beállításnál válassza a elemet `Signature` .
10. Kattintson a **Létrehozás** lehetőségre.

## <a name="configure-wechat-as-an-identity-provider"></a>WeChat konfigurálása identitás-szolgáltatóként

Annak engedélyezéséhez, hogy a felhasználók WeChat-fiókkal jelentkezzenek be, meg kell adnia a fiókot jogcím-szolgáltatóként, amely Azure AD B2C tud kommunikálni egy végponton keresztül. A végpont olyan jogcímeket biztosít, amelyeket a Azure AD B2C használ annak ellenőrzéséhez, hogy egy adott felhasználó hitelesítve van-e.

A WeChat-fiókot jogcím-szolgáltatóként is meghatározhatja, ha hozzáadja azt a **ClaimsProviders** elemhez a szabályzat fájlkiterjesztés fájljában.

1. Nyissa meg a *TrustFrameworkExtensions.xml*.
2. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a gyökérelem elemhez.
3. Vegyen fel egy új **ClaimsProvider** a következőképpen:

    ```xml
    <ClaimsProvider>
      <Domain>wechat.com</Domain>
      <DisplayName>WeChat (Preview)</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="WeChat-OAuth2">
          <DisplayName>WeChat</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">wechat</Item>
            <Item Key="authorization_endpoint">https://open.weixin.qq.com/connect/qrconnect</Item>
            <Item Key="AccessTokenEndpoint">https://api.weixin.qq.com/sns/oauth2/access_token</Item>
            <Item Key="ClaimsEndpoint">https://api.weixin.qq.com/sns/userinfo</Item>
            <Item Key="scope">snsapi_login</Item>
            <Item Key="HttpBinding">GET</Item>
            <Item Key="AccessTokenResponseFormat">json</Item>
            <Item Key="ClientIdParamName">appid</Item>
            <Item Key="ClientSecretParamName">secret</Item>
            <Item Key="ExtraParamsInAccessTokenEndpointResponse">openid</Item>
            <Item Key="ExtraParamsInClaimsEndpointRequest">openid</Item>
            <Item Key="ResponseErrorCodeParamName">errcode</Item>
            <Item Key="external_user_identity_claim_id">unionid</Item>
          <Item Key="client_id">Your WeChat application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_WeChatSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="UserId" PartnerClaimType="unionid" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="wechat.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
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

4. **Client_id** beállítása az alkalmazás-azonosítóhoz az alkalmazás regisztrációja során.
5. Mentse a fájlt.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="WeChatExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="WeChatExchange" TechnicalProfileReferenceId="WeChat-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Egyéni szabályzat tesztelése

1. Válassza ki a függő entitás házirendjét, például: `B2C_1A_signup_signin` .
1. **Alkalmazás** esetén válasszon ki egy [korábban regisztrált](troubleshoot-custom-policies.md#troubleshoot-the-runtime)webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Kattintson a **Futtatás most** gombra.
1. A regisztráció vagy bejelentkezés lapon válassza a **WeChat** lehetőséget a WeChat-fiókkal való bejelentkezéshez.

Ha a bejelentkezési folyamat sikeres, a rendszer átirányítja a böngészőt `https://jwt.ms` , amely a Azure ad B2C által visszaadott jogkivonat tartalmát jeleníti meg.

::: zone-end
