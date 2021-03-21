---
title: Adja át az identitás-szolgáltató hozzáférési jogkivonatát az alkalmazásnak
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan adhat hozzáférési jogkivonatot a OAuth 2,0-identitás szolgáltatójának jogcímként a Azure Active Directory B2C felhasználói folyamatában.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/15/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a99d41f5f9fc9538aaf563bd3ae56075d269c94a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97584646"
---
# <a name="pass-an-identity-provider-access-token-to-your-application-in-azure-active-directory-b2c"></a>Adja át az identitás-szolgáltató hozzáférési jogkivonatát az alkalmazásának Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) [felhasználói folyamata](user-flow-overview.md) lehetővé teszi az alkalmazás felhasználói számára, hogy regisztráljon vagy jelentkezzen be az identitás-szolgáltatóval. Az utazás megkezdésekor a Azure AD B2C [hozzáférési jogkivonatot](tokens-overview.md) kap az identitás-szolgáltatótól. Azure AD B2C a token használatával kéri le a felhasználó adatait. A felhasználói folyamat egy jogcímet engedélyez, hogy átadja a jogkivonatot a Azure AD B2Cban regisztrált alkalmazásoknak.

::: zone pivot="b2c-user-flow"

A Azure AD B2C támogatja a [OAuth 2,0](add-identity-provider.md) Identity Providers hozzáférési jogkivonatának továbbítását, amely tartalmazza a [Facebookot](identity-provider-facebook.md) és a [Google](identity-provider-google.md)-t is. Az összes többi Identity Provider esetében a rendszer üresen adja vissza a jogcímet.

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure AD B2C támogatja a [OAuth 2,0](authorization-code-flow.md) és az [OpenID Connect](openid-connect.md) Identity Providers hozzáférési jogkivonatának átadását. Az összes többi Identity Provider esetében a rendszer üresen adja vissza a jogcímet.

::: zone-end

Az alábbi ábra bemutatja, hogyan tér vissza az identitás-szolgáltatói jogkivonat az alkalmazásba: 

![Az identitás-szolgáltató áthalad a folyamaton](./media/idp-pass-through-user-flow/identity-provider-pass-through-flow.png)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="enable-the-claim"></a>Jogcím engedélyezése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
4. Válassza a **felhasználói folyamatok (szabályzatok)** lehetőséget, majd válassza ki a felhasználói folyamatot. Például **B2C_1_signupsignin1**.
5. Válassza az **Alkalmazásjogcímek** lehetőséget.
6. Engedélyezze az **identitás-szolgáltató hozzáférési jogkivonat** jogcímet.

    ![Az identitás-szolgáltató hozzáférési jogkivonat jogcímének engedélyezése](./media/idp-pass-through-user-flow/identity-provider-pass-through-app-claim.png)

7. A felhasználói folyamat mentéséhez kattintson a **Save (Mentés** ) gombra.

## <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

Az alkalmazások Azure AD B2C-ben történő tesztelésekor hasznos lehet, hogy a Azure AD B2C token visszaadja a `https://jwt.ms` jogcímeket a jogcímek áttekintéséhez.

1. A felhasználói folyamat áttekintés lapján válassza a **felhasználói folyamat futtatása** lehetőséget.
2. **Alkalmazás** esetén válassza ki a korábban regisztrált alkalmazást. Az alábbi példában szereplő token megjelenítéséhez a **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
3. Kattintson a **felhasználói folyamat futtatása** elemre, majd jelentkezzen be a fiókja hitelesítő adataival. Ekkor meg kell jelennie az Identitáskezelő hozzáférési jogkivonatának az **idp_access_token** jogcímben.

    Az alábbi példához hasonlónak kell megjelennie:

    ![Dekódolású token a jwt.ms-ben idp_access_token blokk kiemelve](./media/idp-pass-through-user-flow/identity-provider-pass-through-token.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-the-claim-elements"></a>Jogcím-elemek hozzáadása

1. Nyissa meg *TrustframeworkExtensions.xml* fájlt, és adja hozzá a következő **claimType** elemet egy azonosítóval `identityProviderAccessToken` a **ClaimsSchema** elemhez:

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="identityProviderAccessToken">
          <DisplayName>Identity Provider Access Token</DisplayName>
          <DataType>string</DataType>
          <AdminHelpText>Stores the access token of the identity provider.</AdminHelpText>
        </ClaimType>
        ...
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Adja hozzá a **OutputClaim** elemet a **kivonatjogcím** elemhez minden olyan OAuth 2,0-identitáshoz, amelyhez hozzá szeretné adni a hozzáférési jogkivonatot. A következő példa a Facebook technikai profiljához hozzáadott elemet mutatja be:

    ```xml
    <ClaimsProvider>
      <DisplayName>Facebook</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Facebook-OAUTH">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="{oauth2:access_token}" />
          </OutputClaims>
          ...
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

3. Mentse a *TrustframeworkExtensions.xml* fájlt.
4. Nyissa meg a függő entitás házirend-fájlját, például *SignUpOrSignIn.xml*, és adja hozzá a **OutputClaim** elemet a **kivonatjogcím**:

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="idp_access_token"/>
        </OutputClaims>
        ...
      </TechnicalProfile>
    </RelyingParty>
    ```

5. Mentse a házirend-fájlt.

## <a name="test-your-policy"></a>A szabályzat tesztelése

Az alkalmazások Azure AD B2C-ben történő tesztelésekor hasznos lehet, hogy a Azure AD B2C token visszaadja a `https://jwt.ms` jogcímeket, hogy áttekintse a benne lévő jogcímeket.

### <a name="upload-the-files"></a>A fájlok feltöltése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Győződjön meg arról, hogy az Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben kattintson a **könyvtár + előfizetés** szűrőre, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Válassza az **identitási élmény keretrendszert**.
5. Az egyéni házirendek lapon kattintson a **házirend feltöltése** elemre.
6. Ha létezik, válassza a **házirend felülírása** lehetőséget, majd keresse meg és válassza ki a *TrustframeworkExtensions.xml* fájlt.
7. Válassza a **Feltöltés** lehetőséget.
8. Ismételje meg az 5 – 7. lépést a függő entitás fájljánál, például *SignUpOrSignIn.xml*.

### <a name="run-the-policy"></a>A házirend futtatása

1. Nyissa meg a módosított szabályzatot. Például *B2C_1A_signup_signin*.
2. **Alkalmazás** esetén válassza ki a korábban regisztrált alkalmazást. Az alábbi példában szereplő token megjelenítéséhez a **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
3. Válassza a **Futtatás most** lehetőséget.

    Az alábbi példához hasonlónak kell megjelennie:

    ![Dekódolású token a jwt.ms-ben idp_access_token blokk kiemelve](./media/idp-pass-through-user-flow/identity-provider-pass-through-token-custom.png)

::: zone-end

## <a name="next-steps"></a>Következő lépések

További információ: [Azure ad B2C tokenek áttekintése](tokens-overview.md).
