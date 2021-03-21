---
title: Jogkivonatok konfigurálása – Azure Active Directory B2C | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja a jogkivonatok élettartamát és kompatibilitási beállításait a Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: f22a55a65b7dc4fd4f714d880804e6fd65dbbe46
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101654374"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Jogkivonatok konfigurálása az Azure Active Directory B2C-ben

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Ebből a cikkből megtudhatja, hogyan konfigurálhatja a [jogkivonatok élettartamát és kompatibilitását](tokens-overview.md) Azure Active Directory B2C (Azure ad B2C).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="token-lifetime-behavior"></a>Jogkivonat élettartamának viselkedése

Beállíthatja a jogkivonat élettartamát, beleértve a következőket:

- **Hozzáférési és azonosító jogkivonat élettartama (perc)** – a OAuth 2,0 tulajdonosi JOGKIVONAT és azonosító jogkivonatok élettartama. Az alapértelmezett érték 60 perc (1 óra). A minimális (inkluzív) érték 5 perc. A maximális érték 1 440 perc (24 óra).
- **Frissítési jogkivonat élettartama (nap)** – az a maximális időtartam, ameddig egy frissítési jogkivonat felhasználható új hozzáférési jogkivonat beszerzéséhez, ha az alkalmazás már megkapta a `offline_access` hatókört. Az alapértelmezett érték 14 nap. A minimális (inkluzív) egy nap. A maximális (inkluzív) 90 nap.
- A **jogkivonat-toló ablak élettartamának frissítése** – a jogkivonat-legördülő ablak típusának frissítése. `Bounded` azt jelzi, hogy a frissítési jogkivonat kiterjeszthető az **élettartam hosszában (nap)** megadott értékkel. `No expiry` azt jelzi, hogy a frissítési jogkivonat csúszó ablakának élettartama soha nem jár le.
- **Élettartam hossza (nap)** – az adott időszak után eltelt idő elteltével a felhasználónak újra hitelesítenie kell magát, függetlenül attól, hogy milyen érvényességi időtartamra van az alkalmazás által beszerzett legutóbbi frissítési jogkivonat. Az értéknek nagyobbnak vagy egyenlőnek kell lennie a **frissítési jogkivonat élettartamának** értékével.

A következő ábrán a jogkivonat-lecsúszó ablak élettartamának viselkedése látható.

![Jogkivonat élettartamának frissítése](./media/configure-tokens/refresh-token-lifetime.png)

> [!NOTE]
> >Az engedélyezési kódot használó egylapos alkalmazások esetében a PKCE mindig 24 órás frissítési tokent használnak, míg a Mobile apps, az asztali alkalmazások és a webalkalmazások nem tapasztalják ezt a korlátozást. [További információ a frissítési tokenek biztonsági vonatkozásairól a böngészőben](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser).


## <a name="configure-token-lifetime"></a>Jogkivonat élettartamának konfigurálása

::: zone pivot="b2c-user-flow"

A felhasználói folyamat jogkivonat-élettartamának konfigurálása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. Válassza a **felhasználói folyamatok (szabályzatok)** lehetőséget.
1. Nyissa meg a korábban létrehozott felhasználói folyamatot.
1. Válassza ki a **Tulajdonságok** elemet.
1. A **jogkivonat élettartama** alatt állítsa be a tulajdonságokat úgy, hogy az megfeleljen az alkalmazás igényeinek.
1. Kattintson a **Mentés** gombra.

::: zone-end

::: zone pivot="b2c-custom-policy"

Ha módosítani szeretné a jogkivonat-kompatibilitás beállításait, állítsa be a [jogkivonat kiállítói](jwt-issuer-technical-profile.md) technikai profiljának metaadatait a bővítményben, vagy az érintett szabályzat függő entitását tartalmazó fájlt. A jogkivonat kiállítói technikai profilja a következő példához hasonlít:

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          <Item Key="token_lifetime_secs">3600</Item>
          <Item Key="id_token_lifetime_secs">3600</Item>
          <Item Key="refresh_token_lifetime_secs">1209600</Item>
          <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
          <!--<Item Key="allow_infinite_rolling_refresh_token">true</Item>-->
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

Az előző példában az alábbi értékek vannak megadva:

- **token_lifetime_secs** – hozzáférési jogkivonat élettartama (másodperc). Az alapértelmezett érték 3 600 (1 óra). A minimális érték 300 (5 perc). A maximális érték 86 400 (24 óra). 
- **id_token_lifetime_secs** -azonosító jogkivonat élettartama (másodperc). Az alapértelmezett érték 3 600 (1 óra). A minimális érték 300 (5 perc). A maximális érték 86 400 (24 óra). 
- **refresh_token_lifetime_secs** A jogkivonat élettartamának (másodperc) frissítése. Az alapértelmezett érték 120, 9600 (14 nap). A minimális érték 86 400 (24 óra). A maximális érték 7 776 000 (90 nap). 
- **rolling_refresh_token_lifetime_secs** – a jogkivonat legördülő ablakának élettartama (másodperc) Az alapértelmezett érték 7 776 000 (90 nap). A minimális érték 86 400 (24 óra). A maximális érték 31 536 000 (365 nap). Ha nem szeretné kikényszeríteni a csúszó ablak élettartamát, állítsa a értékét a következőre: `allow_infinite_rolling_refresh_token` `true` . 
- **allow_infinite_rolling_refresh_token** – a jogkivonat-kicsúsztatható ablak élettartamának frissítése soha nem jár le. 

::: zone-end


## <a name="token-compatibility-settings"></a>Jogkivonat-kompatibilitási beállítások

Megadhatja a jogkivonat-kompatibilitást, beleértve a következőket:

- **Kiállítói (ISS) jogcím** – a hozzáférési és azonosító jogkivonat kiállítójának formátuma.
- **Tulajdonos (Sub) jogcím** – a rendszerbiztonsági tag, amelyről a jogkivonat adatokat, például egy alkalmazás felhasználóját érvényesíti. Ez az érték nem módosítható, és nem rendelhető hozzá újra, és nem használható újra. Az engedélyezési ellenőrzések biztonságos elvégzésére használható, például ha a jogkivonat egy erőforrás elérésére szolgál. Alapértelmezés szerint a tulajdonos jogcímet a rendszer a címtárban lévő felhasználó objektumazonosító alapján tölti fel.
- **Felhasználói folyamatot jelképező jogcím** – ez a jogcím azonosítja a végrehajtott felhasználói folyamatot. Lehetséges értékek: `tfp` (alapértelmezett) vagy `acr` .

::: zone pivot="b2c-user-flow"

A felhasználói folyamatok kompatibilitási beállításainak konfigurálása:

1. Válassza a **felhasználói folyamatok (szabályzatok)** lehetőséget.
1. Nyissa meg a korábban létrehozott felhasználói folyamatot.
1. Válassza ki a **Tulajdonságok** elemet.
1. A **jogkivonat-kompatibilitási beállítások** területen állítsa be a tulajdonságokat úgy, hogy az megfeleljen az alkalmazás igényeinek.
1. Kattintson a **Mentés** gombra.

::: zone-end

::: zone pivot="b2c-custom-policy"

Ha módosítani szeretné a jogkivonat-kompatibilitás beállításait, állítsa be a [jogkivonat kiállítói](jwt-issuer-technical-profile.md) technikai profiljának metaadatait a bővítményben, vagy az érintett szabályzat függő entitását tartalmazó fájlt. A jogkivonat kiállítói technikai profilja a következő példához hasonlít:

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          ...
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

- **Kiállítói (ISS) jogcím** – a kibocsátó (ISS) jogcím a **IssuanceClaimPattern** metaadat-elemmel van beállítva. A megfelelő értékek: `AuthorityAndTenantGuid` és `AuthorityWithTfp` .
- **Házirend-azonosítót jelölő jogcím beállítása** – az érték beállításának beállításai `TFP` (megbízhatósági keretrendszer-házirend) és `ACR` (hitelesítési környezetre vonatkozó hivatkozás). `TFP` az ajánlott érték. Állítsa be a **AuthenticationContextReferenceClaimPattern** értéket a értékkel `None` .

    A **ClaimsSchema** elemben adja hozzá a következő elemet:

    ```xml
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    A **OutputClaims** elemben adja hozzá a következő elemet:

    ```xml
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    Az ACR esetében távolítsa el a **AuthenticationContextReferenceClaimPattern** -tételt.

- **Tulajdonos (Sub) jogcím** – ez a beállítás alapértelmezés szerint a ObjectId, ha ezt a beállítást szeretné váltani `Not Supported` , cserélje le a következő sort:

    ```xml
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    Ezzel a sorral:

    ```xml
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

::: zone-end

## <a name="provide-optional-claims-to-your-app"></a>Opcionális jogcímek megadása az alkalmazás számára

Az alkalmazás jogcímei az alkalmazásnak visszaadott értékek. Frissítse a felhasználói folyamatot, hogy tartalmazza a kívánt jogcímeket.

::: zone pivot="b2c-user-flow"

1. Válassza a **felhasználói folyamatok (szabályzatok)** lehetőséget.
1. Nyissa meg a korábban létrehozott felhasználói folyamatot.
1. Válassza az **Alkalmazásjogcímek** lehetőséget.
1. Válassza ki azokat a jogcímeket és attribútumokat, amelyeket vissza szeretne küldeni az alkalmazásnak.
1. Kattintson a **Mentés** gombra.

::: zone-end

::: zone pivot="b2c-custom-policy"

A [függő entitásra vonatkozó házirend technikai profiljának](relyingparty.md#technicalprofile) kimeneti jogcímei az alkalmazásnak visszaadott értékek. A kimeneti jogcímek hozzáadásával a jogcímeket a rendszer sikeres felhasználói út után kiállítja a jogkivonatba, és a rendszer elküldi az alkalmazásnak. Módosítsa a technikai profil elemet a függő entitás szakaszban a kívánt jogcímek kimeneti jogcímként való hozzáadásához.

1. Nyissa meg az egyéni házirend-fájlt. Például SignUpOrSignin.xml.
1. Keresse meg a OutputClaims elemet. Adja hozzá a tokenbe felvenni kívánt OutputClaim. 
1. Állítsa be a kimeneti jogcímek attribútumait. 

A következő példa hozzáadja a `accountBalance` jogcímet. Az accountBalance-jogcímet egyensúlyt kell elküldeni az alkalmazásnak. 

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <!--Add the optional claims here-->
      <OutputClaim ClaimTypeReferenceId="accountBalance" DefaultValue="" PartnerClaimType="balance" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

A OutputClaim elem a következő attribútumokat tartalmazza:

- **ClaimTypeReferenceId** – egy jogcím típusának azonosítója már definiálva van a [ClaimsSchema](claimsschema.md) szakaszban a házirend fájl vagy a szülő házirend fájljában.
- **PartnerClaimType** – lehetővé teszi a jogcím nevének módosítását a jogkivonatban. 
- **DefaultValue** – alapértelmezett érték. Beállíthatja az alapértelmezett értéket is egy jogcím- [feloldóhoz](claim-resolver-overview.md), például a bérlő azonosítóját.
- **AlwaysUseDefaultValue** – az alapértelmezett érték használatának kényszerítése.

::: zone-end

## <a name="next-steps"></a>Következő lépések

További információ a [hozzáférési tokenek igényléséről](access-tokens.md).