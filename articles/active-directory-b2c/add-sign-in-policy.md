---
title: Bejelentkezési folyamat beállítása
titleSuffix: Azure Active Directory B2C
description: Megtudhatja, hogyan állíthatja be a bejelentkezési folyamatot a Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/04/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 2310bd39c39036b6d6ac919517fa5539d7b70779
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581864"
---
# <a name="set-up-a-sign-in-flow-in-azure-active-directory-b2c"></a>Bejelentkezési folyamat beállítása Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="sign-in-flow-overview"></a>Bejelentkezési folyamat – áttekintés

A bejelentkezési szabályzat lehetővé teszi a felhasználók számára a következőket: 

* A felhasználók Azure AD B2C helyi fiókkal jelentkezhetnek be
* Közösségi fiókkal való regisztráció vagy bejelentkezés
* Új jelszó létrehozása
* A felhasználók nem regisztrálhatnak Azure AD B2C helyi fiókra. Fiók létrehozásához a rendszergazda [Azure Portal](manage-users-portal.md#create-a-consumer-user)vagy [MS Graph APIt](microsoft-graph-operations.md)használhat.

![Profil szerkesztési folyamata](./media/add-sign-in-policy/sign-in-user-flow.png)

## <a name="prerequisites"></a>Előfeltételek

Ha még nem tette meg, [regisztráljon egy webalkalmazást Azure Active Directory B2C](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-sign-in-user-flow"></a>Bejelentkezési felhasználói folyamat létrehozása

Bejelentkezési szabályzat hozzáadása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A Azure Portal keresse meg és válassza a **Azure ad B2C** lehetőséget.
1. A **házirendek** területen válassza a **felhasználói folyamatok** lehetőséget, majd válassza az **új felhasználói folyamat** elemet.
1. A **felhasználói folyamat létrehozása** lapon válassza a **bejelentkezési** felhasználói folyamat elemet.
1. **A verzió kiválasztása** területen válassza az **ajánlott** lehetőséget, majd válassza a **Létrehozás** lehetőséget. ([További](user-flow-versions.md) információ a felhasználói folyamatok verzióiról.)
1. Adja meg a felhasználói folyamat **nevét** . Például: *signupsignin1*.
1. Az **Identity Providers** esetében válassza az **e-mail bejelentkezés** lehetőséget.
1. **Alkalmazási jogcímek** esetében válassza ki azokat a jogcímeket és attribútumokat, amelyeket el szeretne küldeni az alkalmazásnak. Válassza például a **továbbiak megjelenítése** lehetőséget, majd válassza a **megjelenítendő név**, a **név**, a  **vezetéknév** és a **felhasználó objektumazonosító** attribútumait és jogcímeit. Kattintson az **OK** gombra.
1. A felhasználói folyamat hozzáadásához kattintson a **Létrehozás** gombra. A *B2C_1* előtagja automatikusan előtagértéke a nevet.

### <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. Válassza ki a létrehozott felhasználói folyamatot az Áttekintés oldal megnyitásához, majd válassza a **felhasználói folyamat futtatása** lehetőséget.
1. Az **alkalmazás** lapon válassza ki a korábban regisztrált *webapp1* nevű webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Kattintson a **felhasználói folyamat futtatása** elemre.
1. A regisztrációs hivatkozás nélkül be kell jelentkeznie a létrehozott fiókkal (MS Graph API használatával). A visszaadott jogkivonat tartalmazza a kiválasztott jogcímeket.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="remove-the-sign-up-link"></a>A regisztrációs hivatkozás eltávolítása

A **SelfAsserted-LocalAccountSignin-e-mailes** technikai profil egy [önérvényesített](self-asserted-technical-profile.md), amelyet a rendszer a regisztrálási vagy bejelentkezési folyamat során hív meg. A regisztrációs hivatkozás eltávolításához állítsa a metaadatokat a következőre: `setting.showSignupLink` `false` . Bírálja felül a SelfAsserted-LocalAccountSignin-e-mailes technikai profilokat a bővítmény fájljában. 

1. Nyissa meg a szabályzat Extensions (bővítmények) fájlját. Például: _`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**_ .
1. Keresse meg az `ClaimsProviders` elemet. Ha az elem nem létezik, adja hozzá.
1. Adja hozzá a következő jogcím-szolgáltatót a `ClaimsProviders` elemhez:

    ```xml
    <!--
    <ClaimsProviders> -->
      <ClaimsProvider>
        <DisplayName>Local Account</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
            <Metadata>
              <Item Key="setting.showSignupLink">false</Item>
            </Metadata>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    <!--
    </ClaimsProviders> -->
    ```

1. A `<BuildingBlocks>` elemen belül adja hozzá a következő [ContentDefinition](contentdefinitions.md) a verzió 1.2.0 vagy újabb adaturi-azonosítóra való hivatkozáshoz:

    ```XML
    <!-- 
    <BuildingBlocks> 
      <ContentDefinitions>-->
        <ContentDefinition Id="api.localaccountsignup">
          <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
        </ContentDefinition>
      <!--
      </ContentDefinitions>
    </BuildingBlocks> -->
    ```

## <a name="update-and-test-your-policy"></a>A szabályzat frissítése és tesztelése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Győződjön meg arról, hogy az Azure AD-bérlőt tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki az Azure ad-bérlőt tartalmazó könyvtárat.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Alkalmazásregisztrációk**.
1. Válassza az **identitási élmény keretrendszert**.
1. Válassza az **egyéni házirend feltöltése** lehetőséget, majd töltse fel a módosított házirend-fájlt *TrustFrameworkExtensions.xml*.
1. Válassza ki a feltöltött bejelentkezési szabályzatot, majd kattintson a **Futtatás most** gombra.
1. A regisztrációs hivatkozás nélkül be kell jelentkeznie a létrehozott fiókkal (MS Graph API használatával).

::: zone-end

## <a name="next-steps"></a>Következő lépések

* Vegyen fel egy [bejelentkezést a közösségi identitás-szolgáltatóval](add-identity-provider.md).
* Állítsa be a [jelszó-visszaállítási folyamatot](add-password-reset-policy.md).
