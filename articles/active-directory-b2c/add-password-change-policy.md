---
title: Jelszó módosításának beállítása egyéni szabályzatok használatával
titleSuffix: Azure AD B2C
description: Ismerje meg, hogy a felhasználók miként módosíthatják a jelszavukat egyéni szabályzatok használatával Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/17/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a42cb97d123d0943dab02bf1f70fcf306d6bcd96
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97629126"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>A jelszó módosításának konfigurálása egyéni házirendek használatával Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) esetében engedélyezheti, hogy a helyi fiókkal bejelentkezett felhasználók a jelszavukat úgy változtassák meg, hogy az e-mail-ellenőrzésen alapuló eredetiségük bizonyítása nélkül is megváltozzon. A jelszó-módosítási folyamat a következő lépéseket foglalja magában:

1. Bejelentkezés helyi fiókkal. Ha a munkamenet továbbra is aktív, Azure AD B2C engedélyezi a felhasználó számára, és a következő lépéshez ugorjon.
1. A felhasználóknak ellenőriznie kell a **régi jelszót**, létre kell hozniuk és meg kell erősíteniük az **új jelszót**.

![Jelszó-módosítási folyamat](./media/add-password-change-policy/password-change-flow.png)

## <a name="prerequisites"></a>Előfeltételek

* Hajtsa végre a következő témakörben ismertetett lépéseket: Ismerkedés az [Egyéni szabályzatokkal Active Directory B2Cban](custom-policy-get-started.md).
* Ha még nem tette meg, [regisztráljon egy webalkalmazást Azure Active Directory B2C](tutorial-register-applications.md).

## <a name="add-the-elements"></a>Elemek hozzáadása

1. Nyissa meg *TrustframeworkExtensions.xml* fájlt, és adja hozzá a következő **claimType** elemet egy azonosítóval `oldPassword` a [ClaimsSchema](claimsschema.md) elemhez:

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="oldPassword">
          <DisplayName>Old Password</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Enter your old password</UserHelpText>
          <UserInputType>Password</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. A [ClaimsProvider](claimsproviders.md) elem tartalmazza azt a technikai profilt, amely hitelesíti a felhasználót. Adja hozzá a következő jogcím-szolgáltatókat a **ClaimsProviders** elemhez:

    ```xml
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive-PasswordChange">
            <DisplayName>Local Account SignIn</DisplayName>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
              </InputClaims>
            <IncludeTechnicalProfile ReferenceId="login-NonInteractive" />
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
      <ClaimsProvider>
        <DisplayName>Local Account Password Change</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="LocalAccountWritePasswordChangeUsingObjectId">
            <DisplayName>Change password (username)</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
              <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item>
            </Metadata>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="oldPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
            </OutputClaims>
            <ValidationTechnicalProfiles>
              <ValidationTechnicalProfile ReferenceId="login-NonInteractive-PasswordChange" />
              <ValidationTechnicalProfile ReferenceId="AAD-UserWritePasswordUsingObjectId" />
            </ValidationTechnicalProfiles>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

3. A [UserJourney](userjourneys.md) elem azt az elérési utat határozza meg, amelyet a felhasználó az alkalmazással való interakció során használ. Adja hozzá a **UserJourneys** elemet, ha az nem létezik a következőképpen azonosított **UserJourney** `PasswordChange` :

    ```xml
    <UserJourneys>
      <UserJourney Id="PasswordChange">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.signuporsignin">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordChangeUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="5" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

4. Mentse a *TrustFrameworkExtensions.xml* házirend-fájlt.
5. Másolja az alapszintű csomaggal letöltött *ProfileEdit.xml* fájlt, és nevezze el *ProfileEditPasswordChange.xml*.
6. Nyissa meg az új fájlt, és frissítse a **PolicyId** attribútumot egyedi értékkel. Ez az érték a szabályzat neve. Például *B2C_1A_profile_edit_password_change*.
7. Módosítsa a **ReferenceId** attribútumát, `<DefaultUserJourney>` hogy az megfeleljen a létrehozott új felhasználói út azonosítójának. Például: *PasswordChange*.
8. Mentse a módosításokat.

## <a name="upload-and-test-the-policy"></a>A szabályzat feltöltése és tesztelése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Válassza az **identitási élmény keretrendszert**.
5. Az egyéni házirendek lapon kattintson a **házirend feltöltése** elemre.
6. Ha létezik, válassza a **házirend felülírása** lehetőséget, majd keresse meg és válassza ki a *TrustframeworkExtensions.xml* fájlt.
7. Kattintson a **Feltöltés** gombra.
8. Ismételje meg az 5 – 7. lépést a függő entitás fájljánál, például *ProfileEditPasswordChange.xml*.

### <a name="run-the-policy"></a>A házirend futtatása

1. Nyissa meg a módosított szabályzatot. Például *B2C_1A_profile_edit_password_change*.
2. **Alkalmazás** esetén válassza ki a korábban regisztrált alkalmazást. A token megjelenítéséhez a **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
3. Kattintson a **Futtatás most** parancsra. Jelentkezzen be a korábban létrehozott fiókkal. Most lehetősége van a jelszó módosítására.

## <a name="next-steps"></a>Következő lépések

- Keresse meg a minta szabályzatot a [githubon](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change).
- Ismerje meg, hogyan [konfigurálhatja a jelszó bonyolultságát Azure ad B2C](password-complexity.md).
- Állítsa be a [jelszó-visszaállítási folyamatot](add-password-reset-policy.md).

::: zone-end
