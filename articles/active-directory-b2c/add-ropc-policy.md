---
title: Erőforrás-tulajdonosi jelszóhoz tartozó hitelesítő adatok folyamatának beállítása
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan állíthatja be az erőforrás-tulajdonosi jelszó hitelesítő adatait (ROPC) a Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/11/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 3a7c93bb0e0dcc51e35bc27fa0799d8410e66df6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104581881"
---
# <a name="set-up-a-resource-owner-password-credentials-flow-in-azure-active-directory-b2c"></a>Erőforrás-tulajdonosi jelszó hitelesítő adatainak beállítása Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) esetében az erőforrás-tulajdonos jelszava hitelesítő adatai (ROPC) folyamata egy szabványos OAuth-hitelesítési folyamat. Ebben a folyamatban egy alkalmazás, más néven a függő entitás, érvényes hitelesítő adatokat cserél a tokenekhez. A hitelesítő adatok tartalmazzák a felhasználói azonosítót és a jelszót. A visszaadott tokenek azonosító tokenek, hozzáférési tokenek és frissítési tokenek.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]


## <a name="ropc-flow-notes"></a>ROPC-folyamatok megjegyzései

Azure Active Directory B2C (Azure AD B2C) esetén a következő lehetőségek támogatottak:

- **Natív ügyfél**: a hitelesítés során felhasználói interakció történik, ha a kód egy felhasználói oldali eszközön fut. Az eszköz lehet egy natív operációs rendszer, például Android és iOS rendszerű mobil alkalmazás.
- **Nyilvános ügyféloldali folyamat**: a rendszer csak az alkalmazás által összegyűjtött felhasználói hitelesítő adatokat TOVÁBBÍTJA az API-hívásban. Az alkalmazás hitelesítő adatai nem lesznek elküldve.
- **Új jogcímek hozzáadása**: az azonosító jogkivonat tartalma módosítható új jogcímek hozzáadásához.

A következő folyamatok nem támogatottak:

- **Kiszolgálók közötti** kapcsolat: az Identity Protection rendszernek megbízható IP-címet kell begyűjtenie a hívótól (a natív ügyféltől) a beavatkozás részeként. Kiszolgálóoldali API-hívás esetén csak a kiszolgáló IP-címe van használatban. Ha túllépi a sikertelen hitelesítések dinamikus küszöbértékét, az Identity Protection rendszer azonosíthatja az ismétlődő IP-címet támadóként.
- **Bizalmas ügyféloldali folyamat**: az alkalmazás ügyfél-azonosítója érvényesítve van, de az alkalmazás titkos kulcsa nincs érvényesítve.

A ROPC folyamat használatakor vegye figyelembe a következőket:

- A ROPC nem működik, ha megszakad a felhasználói beavatkozást igénylő hitelesítési folyamat. Ha például egy jelszó lejárt vagy meg kell változtatnia, [többtényezős hitelesítésre](multi-factor-authentication.md) van szükség, vagy ha a bejelentkezés során további adatokat kell gyűjteni (például felhasználói beleegyezik).
- A ROPC csak a helyi fiókokat támogatja. A felhasználók nem jelentkezhetnek be olyan [Összevont identitás-szolgáltatókkal](add-identity-provider.md) , mint például a Microsoft, a Google +, a Twitter, az AD-FS vagy a Facebook.
- A [munkamenet-kezelés](session-behavior.md), beleértve a [bejelentkezett KMSI](session-behavior.md#enable-keep-me-signed-in-kmsi)is, nem alkalmazható.


## <a name="register-an-application"></a>Egy alkalmazás regisztrálása

[!INCLUDE [active-directory-b2c-appreg-ropc](../../includes/active-directory-b2c-appreg-ropc.md)]

::: zone pivot="b2c-user-flow"

##  <a name="create-a-resource-owner-user-flow"></a>Erőforrás-tulajdonos felhasználói folyamat létrehozása

1. Jelentkezzen be az Azure Portalra az Azure AD B2C-bérlő globális rendszergazdájaként.
2. A Azure AD B2C bérlőre való váltáshoz válassza a B2C könyvtárat a portál jobb felső sarkában.
3. Válassza a **felhasználói folyamatok** lehetőséget, és válassza az **új felhasználói folyamat** lehetőséget.
4. Válassza **a bejelentkezés erőforrás-tulajdonos jelszava hitelesítő adataival (ROPC)** lehetőséget.
5. A **verzió** területen győződjön meg arról, hogy az **előnézet** lehetőség van kiválasztva, majd válassza a **Létrehozás** lehetőséget.
7. Adja meg a felhasználói folyamat nevét, például *ROPC_Auth*.
8. Az **alkalmazás jogcímei** területen kattintson a **továbbiak megjelenítése** lehetőségre.
9. Válassza ki az alkalmazáshoz szükséges jogcímeket, például a megjelenítendő nevet, az e-mail-címet és az identitás-szolgáltatót.
10. Kattintson az **OK**, majd a **Létrehozás** gombra.

::: zone-end

::: zone pivot="b2c-custom-policy"

##  <a name="create-a-resource-owner-policy"></a>Erőforrás-tulajdonosi szabályzat létrehozása

1. Nyissa meg a *TrustFrameworkExtensions.xml* fájlt.
2. Ha még nem létezik, vegyen fel egy **ClaimsSchema** elemet és annak alárendelt elemeit az **BuildingBlocks** elem alatti első elemként:

    ```xml
    <ClaimsSchema>
      <ClaimType Id="logonIdentifier">
        <DisplayName>User name or email address that the user can use to sign in</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="resource">
        <DisplayName>The resource parameter passes to the ROPC endpoint</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokenIssuedOnDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokensValidFromDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
    </ClaimsSchema>
    ```

3. **ClaimsSchema** után adjon hozzá egy **ClaimsTransformations** elemet és annak alárendelt elemeit a **BuildingBlocks** elemhez:

    ```xml
    <ClaimsTransformations>
      <ClaimsTransformation Id="CreateSubjectClaimFromObjectID" TransformationMethod="CreateStringClaim">
        <InputParameters>
          <InputParameter Id="value" DataType="string" Value="Not supported currently. Use oid claim." />
        </InputParameters>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="sub" TransformationClaimType="createdClaim" />
        </OutputClaims>
      </ClaimsTransformation>

      <ClaimsTransformation Id="AssertRefreshTokenIssuedLaterThanValidFromDate" TransformationMethod="AssertDateTimeIsGreaterThan">
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" TransformationClaimType="leftOperand" />
          <InputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" TransformationClaimType="rightOperand" />
        </InputClaims>
        <InputParameters>
          <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
          <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
        </InputParameters>
      </ClaimsTransformation>
    </ClaimsTransformations>
    ```

4. Keresse meg azt a **ClaimsProvider** -elemet, amely a **DisplayName** paraméterrel rendelkezik, `Local Account SignIn` és adja hozzá a következő technikai profilt:

    ```xml
    <TechnicalProfile Id="ResourceOwnerPasswordCredentials-OAUTH2">
      <DisplayName>Local Account SignIn</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <Metadata>
        <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
        <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
        <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
        <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item>
        <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
        <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
        <Item Key="response_types">id_token</Item>
        <Item Key="response_mode">query</Item>
        <Item Key="scope">email openid</Item>
        <Item Key="grant_type">password</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="logonIdentifier" PartnerClaimType="username" Required="true" DefaultValue="{OIDC:Username}"/>
        <InputClaim ClaimTypeReferenceId="password" Required="true" DefaultValue="{OIDC:Password}" />
        <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
        <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
        <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
        <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppId" />
        <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppId" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
        <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
      </OutputClaimsTransformations>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
    ```

    Cserélje le a **Client_id** **DefaultValue** értékét az előfeltételként megadott oktatóanyagban létrehozott PROXYIDENTITYEXPERIENCEFRAMEWORK alkalmazás alkalmazás-azonosítójával. Ezután cserélje le a **Resource_id** **DefaultValue** értékét a IdentityExperienceFramework alkalmazás azon alkalmazás-azonosítójával, amelyet az előfeltételként szolgáló oktatóanyagban is létrehozott.

5. Adja hozzá a következő **ClaimsProvider** elemeket a technikai profiljaihoz a **ClaimsProviders** elemhez:

    ```xml
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-UserReadUsingObjectId-CheckRefreshTokenDate">
          <Metadata>
            <Item Key="Operation">Read</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="AssertRefreshTokenIssuedLaterThanValidFromDate" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
          </OutputClaimsTransformations>
          <IncludeTechnicalProfile ReferenceId="AAD-Common" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-RefreshTokenReadAndSetup">
          <DisplayName>Trustframework Policy Engine Refresh Token Setup Technical Profile</DisplayName>
          <Protocol Name="None" />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" />
          </OutputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="JwtIssuer">
          <Metadata>
            <!-- Point to the redeem refresh token user journey-->
            <Item Key="RefreshTokenUserJourneyId">ResourceOwnerPasswordCredentials-RedeemRefreshToken</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

6. Adjon hozzá egy **UserJourneys** elemet és annak alárendelt elemeit a **TrustFrameworkPolicy** elemhez:

    ```xml
    <UserJourney Id="ResourceOwnerPasswordCredentials">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="ResourceOwnerFlow" TechnicalProfileReferenceId="ResourceOwnerPasswordCredentials-OAUTH2" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    <UserJourney Id="ResourceOwnerPasswordCredentials-RedeemRefreshToken">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="RefreshTokenSetupExchange" TechnicalProfileReferenceId="SM-RefreshTokenReadAndSetup" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="CheckRefreshTokenDateFromAadExchange" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId-CheckRefreshTokenDate" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    ```

7. A Azure AD B2C-bérlő **Egyéni házirendek** lapján válassza a **házirend feltöltése** lehetőséget.
8. **Ha létezik, engedélyezze a házirend felülírását**, majd keresse meg és válassza ki a *TrustFrameworkExtensions.xml* fájlt.
9. Kattintson a **Feltöltés** gombra.

## <a name="create-a-relying-party-file"></a>Függő entitás fájljának létrehozása

Következő lépésként frissítse a függő entitás fájlját, amely kezdeményezi a létrehozott felhasználói utat:

1. Készítsen másolatot *SignUpOrSignin.xml* fájlról a munkakönyvtárában, és nevezze át *ROPC_Auth.xmlre*.
2. Nyissa meg az új fájlt, és módosítsa a **PolicyId** attribútum értékét a **TrustFrameworkPolicy** egyedi értékre. A házirend-azonosító a szabályzat neve. Például **B2C_1A_ROPC_Auth**.
3. Módosítsa a **DefaultUserJourney** **ReferenceId** attribútum értékét a következőre: `ResourceOwnerPasswordCredentials` .
4. Módosítsa a **OutputClaims** elemet úgy, hogy csak a következő jogcímeket tartalmazza:

    ```xml
    <OutputClaim ClaimTypeReferenceId="sub" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="displayName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="givenName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="surname" DefaultValue="" />
    ```

5. A Azure AD B2C-bérlő **Egyéni házirendek** lapján válassza a **házirend feltöltése** lehetőséget.
6. **Ha létezik, engedélyezze a házirend felülírását**, majd keresse meg és válassza ki a *ROPC_Auth.xml* fájlt.
7. Kattintson a **Feltöltés** gombra.


::: zone-end

## <a name="test-the-ropc-flow"></a>A ROPC folyamat tesztelése

Egy API-hívás létrehozásához használja kedvenc API-fejlesztési alkalmazását, és tekintse át a szabályzat hibakeresésére adott választ. A POST kérelem törzsének a következő információk alapján hozhat létre egy hívást:

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

- Cserélje le a helyére `<tenant-name>` a Azure ad B2C bérlő nevét.
- A helyére írja `B2C_1A_ROPC_Auth` be az erőforrás-tulajdonosi jelszó hitelesítő adatainak teljes nevét.

| Kulcs | Érték |
| --- | ----- |
| username | `user-account` |
| jelszó | `password1` |
| grant_type | jelszó |
| scope | OpenID `application-id` offline_access |
| client_id | `application-id` |
| response_type | token id_token |

- Cserélje le a `user-account` nevet a bérlő felhasználói fiókjának nevére.
- Cserélje le a- `password1` t a felhasználói fiók jelszavára.
- Cserélje le az `application-id` elemet az alkalmazás-azonosítóra a *ROPC_Auth_app* regisztrációban.
- A *Offline_access* megadása nem kötelező, ha frissítési tokent szeretne kapni.

A tényleges POST-kérelem a következő példához hasonlóan néz ki:

```https
POST /<tenant-name>.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

username=contosouser.outlook.com.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```

Az offline-hozzáférés sikeres válasza a következő példához hasonlóan néz ki:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki...",
    "token_type": "Bearer",
    "expires_in": "3600",
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6Ij...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki..."
}
```

## <a name="redeem-a-refresh-token"></a>Frissítési jogkivonat beváltása

Állítson össze egy POST hívást, amely az itt láthatóhoz hasonló. Használja a következő táblázatban szereplő információkat a kérelem törzse:

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

- Cserélje le a helyére `<tenant-name>` a Azure ad B2C bérlő nevét.
- A helyére írja `B2C_1A_ROPC_Auth` be az erőforrás-tulajdonosi jelszó hitelesítő adatainak teljes nevét.

| Kulcs | Érték |
| --- | ----- |
| grant_type | refresh_token |
| response_type | id_token |
| client_id | `application-id` |
| erőforrás | `application-id` |
| refresh_token | `refresh-token` |

- Cserélje le az `application-id` elemet az alkalmazás-azonosítóra a *ROPC_Auth_app* regisztrációban.
- Cserélje le az értékét az `refresh-token` előző válaszban visszaküldött **refresh_tokenra** .

A sikeres válasz a következő példához hasonlít:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQndhT...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQn...",
    "token_type": "Bearer",
    "not_before": 1533672990,
    "expires_in": 3600,
    "expires_on": 1533676590,
    "resource": "bef2222d56-552f-4a5b-b90a-1988a7d634c3",
    "id_token_expires_in": 3600,
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI1MTZmYzA2NS1mZjM2LTRiOTMtYWE1YS1kNmVlZGE3Y2JhYzgiLCJzdWIiOm51bGwsIm5hbWUiOiJEYXZpZE11IiwicHJlZmVycmVkX3VzZXJuYW1lIjpudWxsLCJpZHAiOiJMb2NhbEFjY291bnQifQ",
    "refresh_token": "eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMCIsInppcCI6IkRlZmxhdGUiLCJzZXIiOiIxLjAi...",
    "refresh_token_expires_in": 1209600
}
```

## <a name="use-a-native-sdk-or-app-auth"></a>Natív SDK vagy App-Auth használata

Azure AD B2C megfelel a OAuth 2,0 szabványoknak a nyilvános ügyfél erőforrás-tulajdonos jelszavának hitelesítő adataihoz, és kompatibilisnek kell lennie a legtöbb ügyféloldali SDK-val. A legfrissebb információkért tekintse meg a [OAuth 2,0-hez készült natív app SDK-t és az OpenID connectet a modern ajánlott eljárások megvalósításához](https://appauth.io/).

## <a name="next-steps"></a>Következő lépések

Töltse le a GitHubról, [az Android](https://aka.ms/aadb2cappauthropc) és [az iOS](https://aka.ms/aadb2ciosappauthropc)rendszerhez készült Azure ad B2C használatára konfigurált munkamintákat.
