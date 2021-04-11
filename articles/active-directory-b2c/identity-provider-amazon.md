---
title: Regisztráció és bejelentkezés beállítása Amazon-fiókkal
titleSuffix: Azure AD B2C
description: A regisztráció és bejelentkezés az Amazon-fiókkal rendelkező ügyfelek számára Azure Active Directory B2C használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.custom: project-no-code
ms.date: 03/17/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: b6c0d9d5430d84006b208c50e78b8d875c95b8ac
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028383"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Egy Amazon-fiókkal történő regisztráció és bejelentkezés beállítása Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-app-in-the-amazon-developer-console"></a>Alkalmazás létrehozása az Amazon fejlesztői konzolján

Az Azure Active Directory B2C (Azure AD B2C) Amazon-fiókkal rendelkező felhasználók bejelentkezésének engedélyezéséhez létre kell hoznia egy alkalmazást az [Amazon fejlesztői szolgáltatásokban és technológiákban](https://developer.amazon.com). További információ: [regisztráció az Amazon](https://developer.amazon.com/docs/login-with-amazon/register-web.html)-ba való bejelentkezéshez. Ha még nem rendelkezik Amazon-fiókkal, regisztrálhat a következő címen: [https://www.amazon.com/](https://www.amazon.com/) .

1. Jelentkezzen be az [Amazon fejlesztői konzolba](https://developer.amazon.com/dashboard) az Amazon-fiókja hitelesítő adataival.
1. Ha még nem tette meg, válassza a regisztráció **lehetőséget,** kövesse a fejlesztői regisztráció lépéseit, majd fogadja el a szabályzatot.
1. Az irányítópulton válassza a **Bejelentkezés az Amazon**-ban lehetőséget.
1. Válassza **az új biztonsági profil létrehozása** lehetőséget.
1. Adja meg a **biztonsági profil nevét**, a **biztonsági profil leírását** és a belefoglalt **adatvédelmi nyilatkozat URL-címét**, például `https://www.contoso.com/privacy` az adatvédelmi nyilatkozat URL-címe a felügyelni kívánt oldal, amely adatvédelmi információkat biztosít a felhasználóknak. Ezután kattintson a **Mentés** gombra.
1. A **Bejelentkezés az Amazon-konfigurációk** szakaszban válassza ki a létrehozott **biztonsági profil nevét** , válassza a **kezelés** ikont, majd válassza a **webes beállítások** lehetőséget.
1. A **webes beállítások** szakaszban másolja ki az **ügyfél-azonosító** értékét. Válassza a **titok megjelenítése** lehetőséget az ügyfél titkos kódjának beolvasásához, majd másolja azt. Egy Amazon-fióknak a bérlőben való identitás-szolgáltatóként való konfigurálásához mindkét érték szükséges. Az **ügyfél titkos kulcsa** fontos biztonsági hitelesítő adat.
1. A **webes beállítások** szakaszban válassza a **Szerkesztés** lehetőséget. 
    1. Az **engedélyezett eredetek** mezőbe írja be a értéket `https://your-tenant-name.b2clogin.com` . Cserélje le a helyére a `your-tenant-name` bérlő nevét. Ha [Egyéni tartományt](custom-domain.md)használ, írja be a értéket `https://your-domain-name` .
    1.  **Engedélyezett visszatérési URL-címek** , írja be a értéket `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` .  Ha [Egyéni tartományt](custom-domain.md)használ, írja be a értéket `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` .  Cserélje le a `your-tenant-name` nevet a bérlő nevére, és az `your-domain-name` Egyéni tartományra.
1. Kattintson a **Mentés** gombra.

::: zone pivot="b2c-user-flow"

## <a name="configure-amazon-as-an-identity-provider"></a>Az Amazon konfigurálása identitás-szolgáltatóként

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
1. Válassza az **identitás-szolgáltatók**, majd az **Amazon** elemet.
1. Adjon meg egy **nevet**. Például: *Amazon*.
1. Az **ügyfél-azonosító** mezőben adja meg a korábban létrehozott Amazon-alkalmazás ügyfél-azonosítóját.
1. Az **ügyfél titka** mezőben adja meg a rögzített ügyfél-titkot.
1. Kattintson a **Mentés** gombra.

## <a name="add-amazon-identity-provider-to-a-user-flow"></a>Amazon Identity Provider hozzáadása felhasználói folyamathoz 

Ezen a ponton az Amazon Identity Provider beállítása megtörtént, de a bejelentkezési oldalakon még nem érhető el. Az Amazon Identity Provider hozzáadása felhasználói folyamathoz:

1. A Azure AD B2C-bérlőben válassza a **felhasználói folyamatok** lehetőséget.
1. Kattintson arra a felhasználói folyamatra, amelyhez hozzá kívánja adni az Amazon Identity providert.
1. A **közösségi identitás-szolgáltatók** területen válassza az **Amazon** lehetőséget.
1. Kattintson a **Mentés** gombra.
1. A szabályzat teszteléséhez válassza a **felhasználói folyamat futtatása** lehetőséget.
1. Az **alkalmazás** lapon válassza ki a korábban regisztrált *testapp1* nevű webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Kattintson a **felhasználói folyamat futtatása** gombra.
1. A regisztrációs vagy bejelentkezési oldalon válassza az **Amazon** lehetőséget az Amazon-fiókkal való bejelentkezéshez.

Ha a bejelentkezési folyamat sikeres, a rendszer átirányítja a böngészőt `https://jwt.ms` , amely a Azure ad B2C által visszaadott jogkivonat tartalmát jeleníti meg.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Házirend-kulcs létrehozása

A Azure AD B2C bérlőben korábban rögzített ügyfél-titkos kulcsot kell tárolnia.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Az Áttekintés lapon válassza az **identitási élmény keretrendszert**.
5. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás** gombra.
6. A **Beállítások** területen válassza a lehetőséget `Manual` .
7. Adja meg a szabályzat kulcsának **nevét** . Például: `AmazonSecret`. A rendszer automatikusan hozzáadja az előtagot a `B2C_1A_` kulcs nevéhez.
8. A **Secret (titkos kulcs**) mezőben adja meg a korábban rögzített ügyfél-titkot.
9. A **kulcshasználat** beállításnál válassza a elemet `Signature` .
10. Kattintson a **Létrehozás** lehetőségre.

## <a name="configure-amazon-as-an-identity-provider"></a>Az Amazon konfigurálása identitás-szolgáltatóként

Annak engedélyezéséhez, hogy a felhasználók egy Amazon-fiókkal jelentkezzenek be, a fiókot jogcím-szolgáltatóként kell megadnia. Ez a Azure AD B2C egy végponton keresztül tud kommunikálni. A végpont olyan jogcímeket biztosít, amelyeket a Azure AD B2C használ annak ellenőrzéséhez, hogy egy adott felhasználó hitelesítve van-e.

Megadhat egy Amazon-fiókot jogcím-szolgáltatóként, ha hozzáadja azt a **ClaimsProviders** elemhez a szabályzat bővítmény fájljában.


1. Nyissa meg a *TrustFrameworkExtensions.xml*.
2. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a gyökérelem elemhez.
3. Vegyen fel egy új **ClaimsProvider** a következőképpen:

    ```xml
    <ClaimsProvider>
      <Domain>amazon.com</Domain>
      <DisplayName>Amazon</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Amazon-OAuth2">
        <DisplayName>Amazon</DisplayName>
        <Protocol Name="OAuth2" />
        <Metadata>
          <Item Key="ProviderName">amazon</Item>
          <Item Key="authorization_endpoint">https://www.amazon.com/ap/oa</Item>
          <Item Key="AccessTokenEndpoint">https://api.amazon.com/auth/o2/token</Item>
          <Item Key="ClaimsEndpoint">https://api.amazon.com/user/profile</Item>
          <Item Key="scope">profile</Item>
          <Item Key="HttpBinding">POST</Item>
          <Item Key="UsePolicyInRedirectUri">false</Item>
          <Item Key="client_id">Your Amazon application client ID</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_AmazonSecret" />
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
          <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="amazon.com" />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        </OutputClaims>
          <OutputClaimsTransformations>
          <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
          <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
          <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
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
    <ClaimsProviderSelection TargetClaimsExchangeId="AmazonExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AmazonExchange" TechnicalProfileReferenceId="Amazon-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Egyéni szabályzat tesztelése

1. Válassza ki a függő entitás házirendjét, például: `B2C_1A_signup_signin` .
1. **Alkalmazás** esetén válasszon ki egy [korábban regisztrált](tutorial-register-applications.md)webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Kattintson a **Futtatás most** gombra.
1. A regisztrációs vagy bejelentkezési oldalon válassza az **Amazon** lehetőséget az Amazon-fiókkal való bejelentkezéshez.

Ha a bejelentkezési folyamat sikeres, a rendszer átirányítja a böngészőt `https://jwt.ms` , amely a Azure ad B2C által visszaadott jogkivonat tartalmát jeleníti meg.

::: zone-end
