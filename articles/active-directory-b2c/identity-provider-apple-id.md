---
title: Regisztráció és bejelentkezés beállítása Apple ID azonosítóval
titleSuffix: Azure AD B2C
description: Az Apple ID-vel való regisztrációt és bejelentkezést a Azure Active Directory B2C használatával biztosíthatja az alkalmazásokban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 63183eb6a77b3a7aecfb6f3e8a7c9ee7c2544de2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105543907"
---
# <a name="set-up-sign-up-and-sign-in-with-an-apple-id--using-azure-active-directory-b2c-preview"></a>Az Apple ID-vel való regisztráció és bejelentkezés beállítása Azure Active Directory B2C (előzetes verzió) használatával

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-apple-id-application"></a>Apple ID-alkalmazás létrehozása

Ha engedélyezni szeretné a bejelentkezést Azure Active Directory B2C (Azure AD B2C) Apple ID azonosítóval rendelkező felhasználók számára, létre kell hoznia egy alkalmazást a alkalmazásban [https://developer.apple.com](https://developer.apple.com) . További információ: bejelentkezés az [Apple-szel](https://developer.apple.com/sign-in-with-apple/get-started/). Ha még nem rendelkezik Apple Developer-fiókkal, regisztrálhat az [Apple fejlesztői programba](https://developer.apple.com/programs/enroll/).

1. Jelentkezzen be az [Apple fejlesztői portálra](https://developer.apple.com/account) a fiókja hitelesítő adataival.
1. A menüben válassza a **tanúsítványok, azonosítók, & profilok** lehetőséget, majd válassza a **(+)** lehetőséget.
1. **Új azonosító regisztrálásához** válassza az **alkalmazás-azonosítók** lehetőséget, majd válassza a **Folytatás** lehetőséget.
1. A **típus kiválasztásához** válassza az **alkalmazás** lehetőséget, majd kattintson a **Folytatás** gombra.
1. **Alkalmazás-azonosító regisztrálása**:
    1. Adja meg a **leírást** 
    1. Adja meg a **köteg azonosítóját**, például: `com.contoso.azure-ad-b2c` . 
    1. A **képességek** területen válassza a **Bejelentkezés az Apple-szel** lehetőséget a képességek listából. 
    1. Jegyezze fel az alkalmazás AZONOSÍTÓjának előtagját (a csoport AZONOSÍTÓját) Ebből a lépésből. Erre később még szüksége lesz.
    1. Válassza a **Folytatás** , majd a **regisztráció** lehetőséget.
1. A menüben válassza a **tanúsítványok, azonosítók, & profilok** lehetőséget, majd válassza a **(+)** lehetőséget.
1. **Új azonosító regisztrálásához** válassza a **szolgáltatások azonosítók** lehetőséget, majd kattintson a **Folytatás** gombra.
1. **Szolgáltatások azonosítójának regisztrálása**:
    1. Adja meg a **leírást**. A leírás megjelenik a felhasználó számára a beleegyezési képernyőn.
    1. Adja meg az **azonosítót**, például: `com.consoto.azure-ad-b2c-service` . Az azonosító az OpenID Connect folyamat ügyfél-azonosítója.
    1. Válassza a **Folytatás** lehetőséget, majd válassza a **regisztráció** lehetőséget.
1. Az **azonosítók** listából válassza ki a létrehozott azonosítót.
1. Válassza **a bejelentkezés az Apple-szel** lehetőséget, majd válassza a **Konfigurálás** lehetőséget.
    1. Válassza ki azt az **elsődleges alkalmazás-azonosítót** , amelyhez be szeretné állítani az Apple-vel való bejelentkezést.
    1. A **tartományok és altartományok** területen adja meg a értéket `your-tenant-name.b2clogin.com` . Cserélje le a-bérlő nevét a bérlő nevére. Ha [Egyéni tartományt](custom-domain.md)használ, írja be a értéket `https://your-domain-name` .
    1. A **visszatérési URL-címek** mezőben adja meg a értéket `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Ha [Egyéni tartományt](custom-domain.md)használ, írja be a értéket `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Cserélje le a `your-tenant-name` nevet a bérlő nevére, és az `your-domain-name` Egyéni tartományra.
    1. Válassza a **tovább**, majd a **kész** lehetőséget.
    1. Az előugró ablak bezárásakor válassza a **Folytatás** lehetőséget, majd kattintson a **Mentés** gombra.

## <a name="creating-an-apple-client-secret"></a>Apple-ügyfél titkos kulcsának létrehozása

1. Az Apple fejlesztői portál menüjében válassza a **kulcsok**, majd a **(+)** lehetőséget.
1. **Új kulcs regisztrálása**:
    1. Írja be a **kulcs nevét**.
    1. Válassza **a bejelentkezés az Apple-szel** lehetőséget, majd válassza a **Konfigurálás** lehetőséget.
    1. Az **elsődleges alkalmazás azonosítójának** esetében válassza ki a korábban létrehozott alkalmazást, és kattintson a **Mentés** gombra.
    1. Válassza a **Konfigurálás** lehetőséget, majd válassza a **regisztráció** lehetőséget a regisztrációs folyamat befejezéséhez.
1. A **kulcs letöltéséhez** kattintson a **Letöltés** gombra a kulcsot tartalmazó. P8 fájl letöltéséhez.


::: zone pivot="b2c-user-flow"

## <a name="configure-apple-as-an-identity-provider"></a>Az Apple konfigurálása identitás-szolgáltatóként

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) a Azure ad B2C bérlő globális rendszergazdájaként.
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. Az **Azure-szolgáltatások** területen válassza a **Azure ad B2C** lehetőséget. Vagy a keresőmező használatával megkeresheti és kiválaszthatja a **Azure ad B2C**.
1. Válassza az **identitás-szolgáltatók**, majd az **Apple (előzetes verzió)** lehetőséget.
1. Adjon meg egy **nevet**. Például: *Apple*.
1. Adja meg az **Apple Developer ID (Team ID)** értéket.
1. Adja meg az **Apple szolgáltatás azonosítóját (ügyfél-azonosító)**.
1. Adja meg az **Apple-kulcs azonosítóját**.
1. Válassza ki és töltse fel az **Apple-tanúsítványokat**.
1. Kattintson a **Mentés** gombra.


> [!IMPORTANT] 
> - Az Apple-be való bejelentkezéshez a rendszergazdának 6 havonta meg kell újítania az ügyfél titkos kulcsát. 
> - A szolgáltatás nyilvános előzetes verziója során az Apple-ügyfél titkos kulcsát manuálisan kell megújítani, ha lejár. Egy figyelmeztetés jelenik meg előre az Apple Identity Providers szolgáltatásban a közösségi IDENTITÁSSZOLGÁLTATÓ konfigurálása lapon, de javasoljuk, hogy saját emlékeztetőt állítson be. 
> - Ha meg kell újítania a titkot, nyissa meg Azure ad B2C a Azure Portalban, lépjen az **Identity Providers**  >  **Apple** elemre, és válassza a **titkos kulcs megújítása** lehetőséget.

## <a name="add-the-apple-identity-provider-to-a-user-flow"></a>Az Apple Identity Provider hozzáadása felhasználói folyamathoz

Ha engedélyezni szeretné a felhasználók számára az Apple ID azonosítóval való bejelentkezést, hozzá kell adnia az Apple Identity providert egy felhasználói folyamathoz. Az Apple-be való bejelentkezés csak a felhasználói folyamatok **ajánlott** verziójára konfigurálható. Az Apple Identity Provider hozzáadása felhasználói folyamathoz:

1. A Azure AD B2C-bérlőben válassza a **felhasználói folyamatok** lehetőséget.
1. Válassza ki azt a felhasználói folyamatot, amelyhez hozzá szeretné adni az Apple Identity providert. 
1. A **közösségi identitás-szolgáltatók** területen válassza az **Apple (előzetes verzió)** lehetőséget.
1. Kattintson a **Mentés** gombra.
1. A szabályzat teszteléséhez válassza a **felhasználói folyamat futtatása** lehetőséget.
1. Az **alkalmazás** lapon válassza ki a korábban regisztrált *testapp1* nevű webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Kattintson a **felhasználói folyamat futtatása** gombra.
1. A regisztrációs vagy bejelentkezési oldalon válassza az **Apple** lehetőséget az Apple ID-vel való bejelentkezéshez.

Ha a bejelentkezési folyamat sikeres, a rendszer átirányítja a böngészőt `https://jwt.ms` , amely a Azure ad B2C által visszaadott jogkivonat tartalmát jeleníti meg.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="signing-the-client-secret"></a>Az ügyfél titkos kulcsának aláírása

Használja a korábban letöltött. P8 fájlt, hogy aláírja az ügyfél titkos kulcsát egy JWT-jogkivonatban. A JWT létrehozásához és aláírásához számos kódtárat használhat. Használja az [Azure-függvényt, amely létrehoz egy jogkivonatot](https://github.com/azure-ad-b2c/samples/tree/master/policies/sign-in-with-apple/azure-function) . 

1. Hozzon létre egy [Azure-függvényt](../azure-functions/functions-create-function-app-portal.md).
1. A **fejlesztő** területen válassza a **Code + test** elemet. 
1. Másolja a [Run. CSX](https://github.com/azure-ad-b2c/samples/blob/master/policies/sign-in-with-apple/azure-function/run.csx) fájl tartalmát, és illessze be a szerkesztőbe.
1. Kattintson a **Mentés** gombra.
1. Hozzon végre egy HTTP- `POST` kérelmet, és adja meg a következő információkat:

    - **appleTeamId**: az Apple fejlesztői CSAPATának azonosítója
    - **appleServiceId**: az Apple Service ID (az ügyfél-azonosító is)
    - **p8key**: a PEM formátumának kulcsa. Ezt úgy szerezheti be, hogy megnyitja a. P8 fájlt egy szövegszerkesztőben, és minden `-----BEGIN PRIVATE KEY-----` `-----END PRIVATE KEY-----` Sortörés nélkül másol mindent.
 
A következő JSON egy példa az Azure-függvény hívására:

```json
{
    "appleTeamId": "ABC123DEFG",
    "appleKeyId": "URKEYID001",
    "appleServiceId": "com.yourcompany.app1",
    "p8key": "MIGTAgEAMBMGByqGSM49AgEGCCqGSM49AwEHBHkwdwIBAQQg+s07NiAcuGEu8rxsJBG7ttupF6FRe3bXdHxEipuyK82gCgYIKoZIzj0DAQehRANCAAQnR1W/KbbaihTQayXH3tuAXA8Aei7u7Ij5OdRy6clOgBeRBPy1miObKYVx3ki1msjjG2uGqRbrc1LvjLHINWRD"
}
```

Az Azure-függvény egy válaszban megfelelően formázott és aláírt ügyfél titkos JWT válaszol, például:

```json
{
    "token": "eyJhbGciOiJFUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJjb20ueW91cmNvbXBhbnkuYXBwMSIsIm5iZiI6MTU2MDI2OTY3NSwiZXhwIjoxNTYwMzU2MDc1LCJpc3MiOiJBQkMxMjNERUZHIiwiYXVkIjoiaHR0cHM6Ly9hcHBsZWlkLmFwcGxlLmNvbSJ9.Dt9qA9NmJ_mk6tOqbsuTmfBrQLFqc9BnSVKR6A-bf9TcTft2XmhWaVODr7Q9w1PP3QOYShFXAnNql5OdNebB4g"
}
```

## <a name="create-a-policy-key"></a>Házirend-kulcs létrehozása

A Azure AD B2C bérlőben korábban rögzített ügyfél-titkos kulcsot kell tárolnia.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
2. Az **Azure-szolgáltatások** területen válassza a **Azure ad B2C** lehetőséget. Vagy a keresőmező használatával megkeresheti és kiválaszthatja a **Azure ad B2C**.
1. Az **Áttekintés** lapon válassza az **identitási élmény keretrendszert**.
1. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás** gombra.
1. A **Beállítások** területen válassza a **manuális** lehetőséget.
1. Adja meg a szabályzat kulcsának **nevét** . Például: "AppleSecret". A rendszer automatikusan hozzáadja a (z) "B2C_1A_" előtagot a kulcs nevéhez.
1. A **Secret (titkos kulcs**) mezőben adja meg az Azure-függvény által visszaadott token értékét (JWT token).
1. A **kulcshasználat** beállításnál válassza az **aláírás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.

> [!IMPORTANT] 
> - Az Apple-be való bejelentkezéshez a rendszergazdának 6 havonta meg kell újítania az ügyfél titkos kulcsát.
> - Ha lejár, és az új értéket a házirend-kulcsban tárolja, manuálisan kell megújítania az Apple-ügyfél titkát.
> - Javasoljuk, hogy 6 hónapon belül állítsa be a saját emlékeztetőjét új ügyfél-titkos kulcs létrehozásához. 

## <a name="configure-apple-as-an-identity-provider"></a>Az Apple konfigurálása identitás-szolgáltatóként

Ha engedélyezni szeretné a felhasználók számára az Apple ID azonosítóval való bejelentkezést, meg kell adnia a fiókot jogcím-szolgáltatóként, amely Azure AD B2C tud kommunikálni egy végponton keresztül. A végpont olyan jogcímeket biztosít, amelyeket a Azure AD B2C használ annak ellenőrzéséhez, hogy egy adott felhasználó hitelesítve van-e.

Az Apple ID-t jogcím-szolgáltatóként is meghatározhatja, ha hozzáadja azt a **ClaimsProviders** elemhez a szabályzat bővítmény fájljában.

1. Nyissa meg a *TrustFrameworkExtensions.xml*.
2. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a gyökérelem elemhez.
3. Vegyen fel egy új **ClaimsProvider** a következőképpen:

    ```xml
    <ClaimsProvider>
      <Domain>apple.com</Domain>
      <DisplayName>Apple</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Apple-OIDC">
          <DisplayName>Apple</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">apple</Item>
            <Item Key="authorization_endpoint">https://appleid.apple.com/auth/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://appleid.apple.com/auth/token</Item>
            <Item Key="JWKS">https://appleid.apple.com/auth/keys</Item>
            <Item Key="issuer">https://appleid.apple.com</Item>
            <Item Key="scope">name email openid</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="response_types">code</Item>
            <Item Key="external_user_identity_claim_id">sub</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="ReadBodyClaimsOnIdpRedirect">user.name.firstName user.name.lastName user.email</Item>
            <Item Key="client_id">You Apple ID</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AppleSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="https://appleid.apple.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="user.name.firstName"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="user.name.lastName"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="user.email"/>
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. **Client_id** beállítása a szolgáltatás azonosítójára. Például: `com.consoto.azure-ad-b2c-service`.
5. Mentse a fájlt.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AppleExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AppleExchange" TechnicalProfileReferenceId="Apple-OIDC" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Egyéni szabályzat tesztelése

1. Válassza ki a függő entitás házirendjét, például: `B2C_1A_signup_signin` .
1. **Alkalmazás** esetén válasszon ki egy [korábban regisztrált](troubleshoot-custom-policies.md#troubleshoot-the-runtime)webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Kattintson a **Futtatás most** gombra.
1. A regisztrációs vagy bejelentkezési oldalon válassza az **Apple** lehetőséget az Apple ID-vel való bejelentkezéshez.

Ha a bejelentkezési folyamat sikeres, a rendszer átirányítja a böngészőt `https://jwt.ms` , amely a Azure ad B2C által visszaadott jogkivonat tartalmát jeleníti meg.

::: zone-end
