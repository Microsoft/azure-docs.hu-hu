---
title: Regisztráció és bejelentkezés beállítása ID.me-fiókkal
titleSuffix: Azure AD B2C
description: A Azure Active Directory B2C használatával ID.me-fiókkal rendelkező ügyfelek számára biztosítson regisztrációt és bejelentkezést az alkalmazásokban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/15/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: c7d43a55878a07e424ce1b6f55782502c244239c
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2021
ms.locfileid: "98537942"
---
# <a name="set-up-sign-up-and-sign-in-with-a-idme-account-using-azure-active-directory-b2c"></a>ID.me-fiókkal történő regisztráció és bejelentkezés beállítása Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]


## <a name="create-an-idme-application"></a>ID.me-alkalmazás létrehozása

Ha Azure Active Directory B2C (Azure AD B2C) ID.me-fiókkal rendelkező felhasználók számára szeretné engedélyezni a bejelentkezést, létre kell hoznia egy alkalmazást a [ID.me fejlesztői erőforrásaiban az API & SDK](https://developers.id.me/)-hoz. További információ: OAuth- [integrációs útmutató](https://developers.id.me/documentation/oauth/overview/kyc). Ha még nem rendelkezik ID.me-fejlesztői fiókkal, regisztrálhat a következő címen: [https://developers.id.me/registration/new](https://developers.id.me/registration/new) .

1. Jelentkezzen be a [ID.me fejlesztői erőforrásaiba API & SDK](https://developers.id.me/) -val a id.me-fiókja hitelesítő adataival.
1. Válassza **a saját alkalmazások megtekintése** lehetőséget, majd válassza a **Folytatás** lehetőséget.
1. Válassza az **Új létrehozása** lehetőséget
    1. Adja meg a **nevet** és a **megjelenítendő nevet**.
    1. Az **átirányítási URI** mezőbe írja be a értéket `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Cserélje le a helyére a `your-tenant-name` bérlő nevét. 
1. Kattintson a **Folytatás** gombra.
1. Másolja ki az **ügyfél-azonosító** és az **ügyfél titkos kulcsának** értékeit. Mindkét eszközhöz hozzá kell adnia az identitás-szolgáltatót a bérlőhöz.

## <a name="create-a-policy-key"></a>Házirend-kulcs létrehozása

A Azure AD B2C bérlőben korábban rögzített ügyfél-titkos kulcsot kell tárolnia.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Az Áttekintés lapon válassza az **identitási élmény keretrendszert**.
5. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás** gombra.
6. A **Beállítások** területen válassza a lehetőséget `Manual` .
7. Adja meg a szabályzat kulcsának **nevét** . Például: `IdMeSecret`. A rendszer automatikusan hozzáadja az előtagot a `B2C_1A_` kulcs nevéhez.
8. A **Secret (titkos kulcs**) mezőben adja meg a korábban rögzített ügyfél-titkot.
9. A **kulcshasználat** beállításnál válassza a elemet `Signature` .
10. Kattintson a **Létrehozás** lehetőségre.

## <a name="add-a-claims-provider"></a>Jogcím-szolgáltató hozzáadása

Ha azt szeretné, hogy a felhasználók ID.me-fiókkal jelentkezzenek be, meg kell adnia a fiókot jogcím-szolgáltatóként, amely Azure AD B2C tud kommunikálni egy végponton keresztül. A végpont olyan jogcímeket biztosít, amelyeket a Azure AD B2C használ annak ellenőrzéséhez, hogy egy adott felhasználó hitelesítve van-e.

A ID.me-fiókot jogcím-szolgáltatóként is meghatározhatja, ha hozzáadja azt a **ClaimsProviders** elemhez a szabályzat fájlkiterjesztés fájljában.

1. Nyissa meg a *TrustFrameworkExtensions.xml*.
2. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a gyökérelem elemhez.
3. Vegyen fel egy új **ClaimsProvider** a következőképpen:

    ```xml
    <ClaimsProvider>
      <Domain>id.me</Domain>
      <DisplayName>ID.me</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="IdMe-OAUTH2">
          <DisplayName>IdMe</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">api.id.me</Item>
            <Item Key="authorization_endpoint">https://api.id.me/oauth/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://api.id.me/oauth/token</Item>
            <Item Key="ClaimsEndpoint">https://api.id.me/api/public/v2/attributes.json</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="scope">openid alumni</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">Your ID.me application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_IdMeSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="uuid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="fname" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lname" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="me.id.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateDisplayNameFromFirstNameAndLastName" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. **Client_id** beállítása az alkalmazás-azonosítóhoz az alkalmazás regisztrációja során.
5. Mentse a fájlt.

### <a name="add-the-claims-transformations"></a>Jogcím-átalakítások hozzáadása

Ezután szükség van egy jogcím-átalakításra a displayName jogcím létrehozásához. Adja hozzá a következő jogcím-átalakítást a `<ClaimsTransformations>` elemen belül `<BuildingBlocks>` . 

```xml
 <ClaimsTransformations>
  <ClaimsTransformation Id="CreateDisplayNameFromFirstNameAndLastName" TransformationMethod="FormatStringMultipleClaims">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputClaim1" />
      <InputClaim ClaimTypeReferenceId="surName" TransformationClaimType="inputClaim2" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="stringFormat" DataType="string" Value="{0} {1}" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" TransformationClaimType="outputClaim" />
    </OutputClaims>
   </ClaimsTransformation>
</ClaimsTransformations>
```

### <a name="upload-the-extension-file-for-verification"></a>A bővítmény fájljának feltöltése ellenőrzéshez

Most úgy konfigurálta a szabályzatot, hogy Azure AD B2C tudja, hogyan kommunikálhat a ID.me-fiókjával. Próbálja megismételni a szabályzat kiterjesztési fájljának feltöltését, hogy megbizonyosodjon róla, hogy eddig nincs probléma.

1. A Azure AD B2C-bérlő **Egyéni házirendek** lapján válassza a **házirend feltöltése** lehetőséget.
2. **Ha létezik, engedélyezze a házirend felülírását**, majd keresse meg és válassza ki a *TrustFrameworkExtensions.xml* fájlt.
3. Kattintson a **Feltöltés** gombra.

## <a name="register-the-claims-provider"></a>A jogcím-szolgáltató regisztrálása

Ezen a ponton az identitás-szolgáltató beállítása megtörtént, de a regisztrációs és bejelentkezési képernyőkön nem érhető el. Az elérhetővé tételéhez hozzon létre egy másolatot egy meglévő sablon felhasználói utazásról, majd módosítsa úgy, hogy az a ID.me-identitás szolgáltatója is legyen.

1. Nyissa meg a *TrustFrameworkBase.xml* fájlt az alapszintű csomagból.
2. A **UserJourney** elem teljes tartalmának megkeresése és másolása `Id="SignUpOrSignIn"` .
3. Nyissa meg a *TrustFrameworkExtensions.xmlt* , és keresse meg a **UserJourneys** elemet. Ha az elem nem létezik, vegyen fel egyet.
4. Illessze be a **UserJourney** elem teljes tartalmát, amelyet a **UserJourneys** elem gyermekeiként másolt.
5. Nevezze át a felhasználói út AZONOSÍTÓját. Például: `SignUpSignInIdMe`.

### <a name="display-the-button"></a>A gomb megjelenítése

A **ClaimsProviderSelection** elem a bejelentkezési és bejelentkezési képernyőn lévő Identity Provider gombhoz hasonlít. Ha hozzáad egy **ClaimsProviderSelection** elemet egy id.me-fiókhoz, egy új gomb jelenik meg, amikor a felhasználó az oldalon landol.

1. Keresse meg  az `Order="1"` Ön által létrehozott felhasználói útra kiterjedő OrchestrationStep elemet.
2. A **ClaimsProviderSelects** területen adja hozzá a következő elemet. Állítsa a **TargetClaimsExchangeId** értékét egy megfelelő értékre, például `IdMeExchange` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="IdMeExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Gomb csatolása egy művelethez

Most, hogy van egy gomb a helyén, össze kell kapcsolni egy művelettel. Ebben az esetben a művelet arra vonatkozik, hogy a Azure AD B2C kommunikáljon egy ID.me-fiókkal a jogkivonat fogadásához.

1. Keresse meg a felhasználói útra kiterjedő **OrchestrationStep** `Order="2"` .
2. Adja hozzá a következő **ClaimsExchange** elemet, és győződjön meg arról, hogy ugyanazt az értéket használja a **TargetClaimsExchangeId** használt azonosítóhoz:

    ```xml
    <ClaimsExchange Id="IdMeExchange" TechnicalProfileReferenceId="IdMe-OAUTH2" />
    ```

    Frissítse a **TechnicalProfileReferenceId** értékét a korábban létrehozott műszaki profil azonosítójával. Például: `IdMe-OAUTH2`.

3. Mentse a *TrustFrameworkExtensions.xml* fájlt, és töltse fel újra az ellenőrzéshez.

## <a name="update-and-test-the-relying-party-file"></a>A függő entitás fájljának frissítése és tesztelése

Frissítse a függő entitás (RP) fájlját, amely kezdeményezi a létrehozott felhasználói utat.

1. Készítsen másolatot *SignUpOrSignIn.xml* a munkakönyvtárában, és nevezze át. Nevezze át például *SignUpSignInIdMe.xmlra*.
1. Nyissa meg az új fájlt, és frissítse a **PolicyId** attribútum értékét a **TrustFrameworkPolicy** egyedi értékkel. Például: `SignUpSignIdMe`.
1. Frissítse a **PublicPolicyUri** értékét a szabályzat URI azonosítójának értékével. Például:`http://contoso.com/B2C_1A_signup_signin_IdMe`
1. Frissítse a **ReferenceId** attribútum értékét a **DefaultUserJourney** -ben, hogy az megfeleljen a létrehozott új felhasználói út azonosítójának (SignUpSignIdMe).
1. Mentse a módosításokat, töltse fel a fájlt.
1. Az **Egyéni házirendek** területen válassza a **B2C_1A_signup_signin** lehetőséget.
1. Az **alkalmazás kiválasztása** lapon válassza ki a korábban regisztrált *testapp1* nevű webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Kattintson a **Futtatás most** lehetőségre, és válassza a id.me lehetőséget a id.me való bejelentkezéshez és az egyéni szabályzat teszteléséhez.

::: zone-end