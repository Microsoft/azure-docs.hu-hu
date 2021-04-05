---
title: E-mail-ellenőrzés letiltása az ügyfél regisztrálása közben
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan tilthatja le az e-mailek ellenőrzését a Azure Active Directory B2C az ügyfelek regisztrációja során.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 0f70c8d501a7d56f4bc29e0f2b065760cad625e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97585020"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Az e-mail-ellenőrzés letiltása az ügyfél-regisztráció során Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Alapértelmezés szerint a Azure Active Directory B2C (Azure AD B2C) ellenőrzi az ügyfél e-mail-címét a helyi fiókok számára (fiókok azon felhasználók számára, akik e-mail-címmel vagy felhasználónévvel jelentkeznek be). Azure AD B2C az érvényes e-mail-címeket biztosítja, hogy az ügyfeleknek meg kell győződniük a regisztrációs folyamat során. Emellett megakadályozza, hogy a rosszindulatú szereplők automatizált folyamatokat használjanak az alkalmazásokban lévő csalárd fiókok létrehozásához.

Egyes alkalmazás-fejlesztők szívesebben szeretnék kihagyni az e-mailek ellenőrzését a regisztrálási folyamat során, hanem az ügyfelek az e-mail-címüket később is ellenőrzik. Ennek támogatásához Azure AD B2C konfigurálható az e-mail ellenőrzésének letiltására. Ez létrehoz egy simább regisztrációs folyamatot, és lehetővé teszi a fejlesztők számára a rugalmasságot, hogy megkülönböztessék azokat az ügyfeleket, akik nem rendelkeznek az e-mail-címükkel.

> [!WARNING]
> Ha letiltja az e-mailek ellenőrzését a regisztrációs folyamatban, a levélszemét is vezethet. Ha letiltja az alapértelmezett Azure AD B2C által biztosított e-mail-ellenőrzést, javasoljuk, hogy hozzon létre egy helyettesítő ellenőrzési rendszerét.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]
## <a name="disable-email-verification"></a>E-mailes ellenőrzés letiltása

::: zone pivot="b2c-user-flow"

Az e-mail-ellenőrzés letiltásához kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)
1. A felső menüben a **könyvtár + előfizetés** szűrő használatával válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C** lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C** lehetőséget.
1. Válassza a **felhasználói folyamatok** lehetőséget.
1. Válassza ki azt a felhasználói folyamatot, amelynek le szeretné tiltani az e-mail-ellenőrzését. Például *B2C_1_signinsignup*.
1. Válassza **ki a lapelrendezések elemet**.
1. Válassza a **helyi fiók regisztrálása lapot**.
1. A **felhasználói attribútumok** területen válassza az **e-mail-cím** elemet.
1. A **szükséges ellenőrzés** legördülő menüben válassza a **nem** lehetőséget.
1. Kattintson a **Mentés** gombra. Az e-mailek ellenőrzése mostantól le van tiltva ennél a felhasználói folyamatnál.

::: zone-end

::: zone pivot="b2c-custom-policy"
A **LocalAccountSignUpWithLogonEmail** technikai profil egy [önérvényesített](self-asserted-technical-profile.md), amelyet a rendszer a regisztrálási folyamat során hív meg. Az e-mail-ellenőrzés letiltásához állítsa a `EnforceEmailVerification` metaadatokat hamis értékre. Bírálja felül a LocalAccountSignUpWithLogonEmail technikai profiljait. 

1. Nyissa meg a szabályzat Extensions (bővítmények) fájlját. Például: <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Keresse meg az `ClaimsProviders` elemet. Ha az elem nem létezik, adja hozzá.
1. Adja hozzá a következő jogcím-szolgáltatót a `ClaimsProviders` elemhez:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <Metadata>
        <Item Key="EnforceEmailVerification">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="test-your-policy"></a>A szabályzat tesztelése 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)
1. A felső menüben a **könyvtár + előfizetés** szűrő használatával válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C** lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C** lehetőséget.
1. Válassza a **felhasználói folyamatok** lehetőséget.
1. Válassza ki azt a felhasználói folyamatot, amelynek le szeretné tiltani az e-mail-ellenőrzését. Például *B2C_1_signinsignup*.
1. A szabályzat teszteléséhez válassza a **felhasználói folyamat futtatása** lehetőséget.
1. Az **alkalmazás** lapon válassza ki a korábban regisztrált *testapp1* nevű webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Kattintson a **felhasználói folyamat futtatása** elemre.
1. Az érvényesítés nélkül egy e-mail-cím használatával regisztrálhat.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>A függő entitás fájljának frissítése és tesztelése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Győződjön meg arról, hogy az Azure AD-bérlőt tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki az Azure ad-bérlőt tartalmazó könyvtárat.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Alkalmazásregisztrációk**.
1. Válassza az **identitási élmény keretrendszert**.
1. Válassza az **egyéni házirend feltöltése** lehetőséget, majd töltse fel a módosított két házirendet.
1. Válassza ki a feltöltött regisztrációs vagy bejelentkezési szabályzatot, majd kattintson a **Futtatás most** gombra.
1. Az érvényesítés nélkül egy e-mail-cím használatával regisztrálhat.

::: zone-end


## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [szabhatja testre a felhasználói felületet Azure Active Directory B2C](customize-ui-with-html.md)

