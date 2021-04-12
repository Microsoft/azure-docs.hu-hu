---
title: Felhasználói attribútumok hozzáadása és felhasználói bevitel testreszabása
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan szabhatja testre a felhasználói adatokat, és hogyan adhat hozzá felhasználói attribútumokat a regisztrációhoz vagy a bejelentkezési útvonalhoz Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/10/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 257ba16cf015705b8f6da264d9c25f28cef2ebb1
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443440"
---
#  <a name="add-user-attributes-and-customize-user-input-in-azure-active-directory-b2c"></a>Felhasználói attribútumok hozzáadása és felhasználói bevitel testreszabása Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

Ebben a cikkben egy új attribútumot gyűjt a regisztráció során Azure Active Directory B2C (Azure AD B2C). Megszerezheti a felhasználók városát, legördülőként konfigurálhatja, és meghatározhatja, hogy meg kell-e adni.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="add-user-attributes-your-user-flow"></a>Felhasználó-attribútumok hozzáadása a felhasználói folyamathoz

1. A Azure AD B2C-bérlőben válassza a **felhasználói folyamatok** lehetőséget.
1. Válassza ki a szabályzatot (például "B2C_1_SignupSignin") a megnyitásához.
1. Válassza ki a **felhasználói attribútumok** elemet, majd válassza ki a felhasználói attribútumot (például "város"). 
1. Kattintson a **Mentés** gombra.

## <a name="provide-optional-claims-to-your-app"></a>Opcionális jogcímek megadása az alkalmazás számára

Az alkalmazás jogcímei az alkalmazásnak visszaadott értékek. Frissítse a felhasználói folyamatot, hogy tartalmazza a kívánt jogcímeket.

1. Válassza ki a szabályzatot (például "B2C_1_SignupSignin") a megnyitásához.
1. Válassza az **Alkalmazásjogcímek** lehetőséget.
1. Válassza ki azokat az attribútumokat, amelyeket vissza szeretne küldeni az alkalmazásnak (például "város").
1. Kattintson a **Mentés** gombra.
 
## <a name="configure-user-attributes-input-type"></a>Felhasználói attribútumok bemeneti típusának konfigurálása

1. Válassza ki a szabályzatot (például "B2C_1_SignupSignin") a megnyitásához.
1. Válassza **ki a lapelrendezések elemet**.
1. Válassza a **helyi fiók regisztrálása lapot**.
1. A **felhasználói attribútumok** területen válassza a **város** lehetőséget.
    1. A **felhasználói bevitel típusa** legördülő menüben válassza a **DropdownSingleSelect** lehetőséget. Nem kötelező: a "fel/le" gomb használatával rendezheti a szöveges sorrendet a regisztrációs oldalon.
    1. A nem **kötelező** legördülő menüben válassza a **nem** lehetőséget.
1. Kattintson a **Mentés** gombra. 

### <a name="provide-a-list-of-values-by-using-localized-collections"></a>Értékek listájának megadása honosított gyűjtemények használatával

A City attribútumhoz tartozó értékek listájának megadása: 

1. [Nyelvi Testreszabás engedélyezése a felhasználói folyamaton](language-customization.md#support-requested-languages-for-ui_locales)
1. Válassza ki a szabályzatot (például "B2C_1_SignupSignin") a megnyitásához.
1. A felhasználói folyamat **nyelvek** lapján válassza ki a testreszabni kívánt nyelvet.
1. Az **oldal szintű erőforrások fájljai** területen válassza a **helyi fiók regisztrálása lapot**.
1. Válassza az **Alapértelmezések letöltése** (vagy a **felülbírálások letöltése** lehetőséget, ha korábban már szerkesztette ezt a nyelvet).
1. Hozzon létre egy `LocalizedCollections` attribútumot.

A a `LocalizedCollections` és a párok tömbje `Name` `Value` . Az elemek sorrendje a megjelenő sorrendben jelenik meg. 

* `ElementId` a felhasználó attribútuma, amelyhez ez az `LocalizedCollections` attribútum válaszol.
* `Name` a felhasználó számára megjelenített érték.
* `Value` Ha ez a beállítás be van jelölve, a rendszer a jogcímben visszaadott értéket adja vissza.

```json
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [
    {
      "ElementType": "ClaimType",
      "ElementId": "city",
      "TargetCollection": "Restriction",
      "Override": false,
      "Items": [
        {
          "Name": "Berlin",
          "Value": "Berlin"
        },
        {
          "Name": "London",
          "Value": "London"
        },
        {
          "Name": "Seattle",
          "Value": "Seattle"
        }
      ]
    }
  ]
}
```

#### <a name="upload-your-changes"></a>A módosítások feltöltése

1. A JSON-fájl módosításainak befejezése után lépjen vissza a B2C-bérlőre.
1. Válassza a **felhasználói folyamatok** lehetőséget, és válassza ki a szabályzatot (például "B2C_1_SignupSignin") a megnyitásához.
1. Válassza a **nyelvek** lehetőséget.
1. Válassza ki a nyelvet, amelyet le szeretne fordítani.
1. Válassza ki a **helyi fiók regisztrálása lapot**.
1. Válassza ki a mappa ikont, és válassza ki a feltölteni kívánt JSON-fájlt. A módosításokat a rendszer automatikusan menti a felhasználói folyamatba.

## <a name="test-your-user-flow"></a>A felhasználói folyamat tesztelése

1. Válassza ki a szabályzatot (például "B2C_1_SignupSignin") a megnyitásához.
1. A szabályzat teszteléséhez válassza a **felhasználói folyamat futtatása** lehetőséget.
1. Az **alkalmazás** lapon válassza ki a korábban regisztrált *testapp1* nevű webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Kattintson a **felhasználói folyamat futtatása** elemre.

::: zone-end

::: zone pivot="b2c-custom-policy"

> [!NOTE]
> Ez a példa a beépített "City" jogcímet használja. Ehelyett kiválaszthatja az egyik támogatott [Azure ad B2C beépített attribútumot](user-profile-attributes.md) vagy egy egyéni attribútumot is. Ha egyéni attribútumot szeretne használni, [engedélyezze az egyéni attribútumok](user-flow-custom-attributes.md)használatát. Ha más beépített vagy egyéni attribútumot szeretne használni, cserélje le a "City" kulcsszót az Ön által választott attribútumra, például a beépített attribútum *beosztás* vagy egy olyan egyéni attribútumra, mint a *extension_loyaltyId*.  

A felhasználóktól származó kezdeti adatokat a regisztrációs vagy bejelentkezési felhasználói úton gyűjtheti be. A további jogcímek később is összegyűjthetők, ha a felhasználói úton szerkeszti a profilt. A bármikor Azure AD B2C az adatokat közvetlenül a felhasználótól gyűjti össze interaktív módon, az Identity Experience Framework saját [maga által megadott műszaki profilt](self-asserted-technical-profile.md)használja. Ebben a példában a következőket látja:

1. Adja meg a "város" jogcímet. 
1. Kérje meg a felhasználót a városra.
1. A Azure AD B2C könyvtárban maradjon a város a felhasználói profilhoz.
1. Olvassa el a városi jogcímet az Azure AD B2C könyvtárából minden bejelentkezéskor.
1. A bejelentkezés vagy a regisztráció után adja vissza a várost a függő entitás alkalmazásához.  

## <a name="define-a-claim"></a>Jogcím definiálása

A jogcím a Azure AD B2C szabályzat végrehajtása során ideiglenes adattárolást biztosít. A [jogcím-séma](claimsschema.md) az a hely, ahol deklarálja a jogcímeket. A jogcím definiálásához a következő elemek használhatók:

- **DisplayName** – a felhasználó felé irányuló címkét meghatározó karakterlánc.
- [Adattípus](claimsschema.md#datatype) – a jogcím típusa.
- **UserHelpText** – segít a felhasználónak megérteni, hogy mi szükséges.
- [UserInputType](claimsschema.md#userinputtype) – a beviteli vezérlőelem típusa, például a szövegmező, a választógomb, a legördülő lista vagy több lehetőség.

Nyissa meg a szabályzat Extensions (bővítmények) fájlját. Például: <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .

1. Keresse meg a [BuildingBlocks](buildingblocks.md) elemet. Ha az elem nem létezik, adja hozzá.
1. Keresse meg a [ClaimsSchema](claimsschema.md) elemet. Ha az elem nem létezik, adja hozzá.
1. Adja hozzá a város jogcímet a **ClaimsSchema** elemhez.  

```xml
<!-- 
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="city">
      <DisplayName>City where you work</DisplayName>
      <DataType>string</DataType>
      <UserInputType>DropdownSingleSelect</UserInputType>
      <Restriction>
        <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
        <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
        <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
      </Restriction>
    </ClaimType>
  <!-- 
  </ClaimsSchema>
</BuildingBlocks>-->
```

## <a name="add-a-claim-to-the-user-interface"></a>Jogcím hozzáadása a felhasználói felülethez

A következő műszaki profilok [önmagukban vannak érvényesítve](self-asserted-technical-profile.md), amikor a felhasználónak meg kell adnia a bemenetet:

- **LocalAccountSignUpWithLogonEmail** – helyi fiók regisztrálási folyamata.
- **SelfAsserted – közösségi** összevont fiók – az első alkalommal bejelentkezett felhasználói bejelentkezés.
- **SelfAsserted-ProfileUpdate** – a profil folyamatának szerkesztése.

Ahhoz, hogy regisztrálni lehessen a városi jogcímet a regisztráció során, kimeneti jogcímként hozzá kell adni a `LocalAccountSignUpWithLogonEmail` technikai profilhoz. Bírálja felül ezt a technikai profilt a kiterjesztési fájlban. Adja meg a kimeneti jogcímek teljes listáját, hogy szabályozni lehessen a jogcímek megjelenítésének sorrendjét a képernyőn. Keresse meg a **ClaimsProviders** elemet. Vegyen fel egy új ClaimsProviders a következőképpen:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <!--Local account sign-up page-->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
       <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="displayName" />
       <OutputClaim ClaimTypeReferenceId="givenName" />
       <OutputClaim ClaimTypeReferenceId="surName" />
       <OutputClaim ClaimTypeReferenceId="city"/>
     </OutputClaims>
   </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

A városi jogcímek összevont fiókkal való első bejelentkezés után történő összegyűjtéséhez hozzá kell adni a technikai profil kimeneti jogcímeként `SelfAsserted-Social` . Ahhoz, hogy a helyi és összevont fiókok a felhasználók később szerkesszék a profiljaikat, adja hozzá a bemeneti és a kimeneti jogcímeket a `SelfAsserted-ProfileUpdate` technikai profilhoz. Bírálja felül ezeket a technikai profilokat a kiterjesztési fájlban. Adja meg a kimeneti jogcímek teljes listáját, hogy szabályozni lehessen a jogcímek megjelenítésének sorrendjét a képernyőn. Keresse meg a **ClaimsProviders** elemet. Vegyen fel egy új ClaimsProviders a következőképpen:

```xml
<ClaimsProvider>
  <DisplayName>Self Asserted</DisplayName>
  <TechnicalProfiles>
    <!--Federated account first-time sign-in page-->
    <TechnicalProfile Id="SelfAsserted-Social">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="city" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName"/>
        <OutputClaim ClaimTypeReferenceId="surname"/>
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
    <!--Edit profile page-->
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="city" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="read-and-write-a-claim"></a>Jogcím olvasása és írása

A következő műszaki profilok [Active Directory műszaki profilok](active-directory-technical-profile.md), amelyek az Azure Active Directoryba való adatolvasást és-írást írják le.  
A használatával `PersistedClaims` adatok írhatók a felhasználói profilba, valamint az `OutputClaims` adatok beolvasása a felhasználói profilból a megfelelő Active Directory technikai profilokban.

Bírálja felül ezeket a technikai profilokat a kiterjesztési fájlban. Keresse meg a **ClaimsProviders** elemet.  Vegyen fel egy új ClaimsProviders a következőképpen:

```xml
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <!-- Write data during a local account sign-up flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during a federated account first-time sign-in flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during edit profile flow. -->
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Read data after user resets the password. -->
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a local account. -->
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a federated account. -->
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="include-a-claim-in-the-token"></a>Jogcím belefoglalása a jogkivonatba 

Ha vissza szeretné állítani a város jogcímet a függő entitás alkalmazásba, adjon hozzá egy kimeneti jogcímet a <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> fájlhoz. A kimeneti jogcím a sikeres felhasználói út után lesz hozzáadva a jogkivonathoz, és a rendszer elküldi az alkalmazásnak. Módosítsa a technikai profil elemet a függő entitás szakaszban a város kimeneti jogcímként való hozzáadásához.
 
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
      <OutputClaim ClaimTypeReferenceId="city" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>Egyéni szabályzat tesztelése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy az Azure AD-bérlőt tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki az Azure ad-bérlőt tartalmazó könyvtárat.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Alkalmazásregisztrációk**.
4. Válassza az **identitási élmény keretrendszert**.
5. Válassza az **egyéni házirend feltöltése** lehetőséget, majd töltse fel a módosított két házirendet.
2. Válassza ki a feltöltött regisztrációs vagy bejelentkezési szabályzatot, majd kattintson a **Futtatás most** gombra.
3. Regisztrálnia kell egy e-mail-cím használatával.

::: zone-end

A regisztrációs képernyőnek az alábbi képernyőképhez hasonlóan kell kinéznie:

![A módosított regisztrációs lehetőség képernyőképe](./media/configure-user-input/signup-with-city-claim-drop-down-example.png)

Az alkalmazásnak visszaadott jogkivonat tartalmazza a `city` jogcímet.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1583500140,
  "nbf": 1583496540,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1583496540,
  "auth_time": 1583496540,
  "name": "Emily Smith",
  "email": "joe@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "city": "Bellevue"
  ...
}
```

::: zone pivot="b2c-custom-policy"

## <a name="next-steps"></a>Következő lépések

- További információ a [ClaimsSchema](claimsschema.md) elemről a IEF-hivatkozásban.
- Ismerje meg, hogyan [használhatja az egyéni attribútumokat a Azure ad B2Cban](user-flow-custom-attributes.md).

::: zone-end
