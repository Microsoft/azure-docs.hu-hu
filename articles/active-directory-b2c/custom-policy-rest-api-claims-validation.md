---
title: Jogcímek REST API érvényesítésként
titleSuffix: Azure AD B2C
description: Útmutató a REST-szolgáltatásokkal kommunikáló Azure AD B2C felhasználói út létrehozásához.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 761bc4db7760ef5e84e3fc3c8a5deea5d4508f51
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94951927"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-to-validate-user-input"></a>Bemutató: REST API jogcímek cseréje a Azure AD B2C felhasználói úton a felhasználói bevitel érvényesítéséhez

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory B2C (Azure AD B2C) által kiépített Identity Experience Framework (IEF) lehetővé teszi az identitás-fejlesztők számára, hogy a felhasználói úton lévő REST API-val integrálják az interakciót.  A bemutató végén létrehozhat egy Azure AD B2C felhasználói utat, amely a [Rest-szolgáltatásokkal](custom-policy-rest-api-intro.md) együttműködve érvényesíti a felhasználói adatokat.

Ebben a forgatókönyvben felvesszük a lehetőséget, hogy a felhasználók hűséget adjanak meg a Azure AD B2C regisztrációs oldalon. A rendszer ellenőrzi, hogy az e-mailek és a lojalitási számok e kombinációja egy promóciós kódra van-e rendelve, ha az adatokat egy REST API küldi el. Ha a REST API promóciós kódot talál ehhez a felhasználóhoz, a rendszer visszaadja a Azure AD B2C. Végül a promóciós kód bekerül a jogkivonat jogcímeibe, amelyeket az alkalmazás használni fog.

Az interakciót összehangoló lépésként is megtervezheti. Ez akkor megfelelő, ha a REST API nem fogja érvényesíteni a képernyőn lévő adatellenőrzést, és mindig visszaadja a jogcímeket. További információkért lásd: bemutató [: REST API jogcím-cserék integrálása a Azure ad B2C felhasználói úton](custom-policy-rest-api-claims-exchange.md)az előkészítési lépésként.

## <a name="prerequisites"></a>Előfeltételek

- Hajtsa végre az [Ismerkedés az egyéni szabályzatokkal](custom-policy-get-started.md)című témakör lépéseit. A bejelentkezéshez és a helyi fiókokkal való bejelentkezéshez egyéni szabályzatot kell használnia.
- Ismerje meg, hogyan [integrálhatja REST API jogcímek cseréjét a Azure ad B2C egyéni szabályzatba](custom-policy-rest-api-intro.md).

## <a name="prepare-a-rest-api-endpoint"></a>REST API végpont előkészítése

Ehhez a bemutatóhoz rendelkeznie kell egy REST API, amely ellenőrzi, hogy van-e e-mail-cím regisztrálva a háttérrendszer-AZONOSÍTÓval. Ha regisztrálva van, a REST API egy regisztrációs promóciós kódot kell visszaadnia, amelyet az ügyfél az alkalmazáson belüli termékek vásárlásához használhat. Ellenkező esetben a REST API HTTP 409 hibaüzenetet ad vissza: a (z) "törzsvásárlói azonosító {törzsvásárlói azonosító}" nincs társítva a (z) {e-mail} e-mail-címével. "

A következő JSON-kód azt mutatja be, Azure AD B2C az REST API végpontnak küldi el azokat. 

```json
{
    "email": "User email address",
    "language": "Current UI language",
    "loyaltyId": "User loyalty ID"
}
```

Miután a REST API érvényesíti az adatait, a következő JSON-adataival kell visszaadnia a HTTP 200 (ok) t:

```json
{
    "promoCode": "24534"
}
```

Ha az ellenőrzés nem sikerült, a REST API HTTP 409 (ütközés) értéket kell visszaadnia a `userMessage` JSON-elemmel. A IEF a `userMessage` REST API által visszaadott jogcímet várja. Ez a jogcím karakterláncként jelenik meg a felhasználó számára, ha az ellenőrzés sikertelen.

```json
{
    "version": "1.0.1",
    "status": 409,
    "userMessage": "LoyaltyId ID '1234' is not associated with 'david@contoso.com' email address."
}
```

A REST API végpont beállítása kívül esik a jelen cikk hatókörén. Létrehoztunk egy [Azure functions](../azure-functions/functions-reference.md) mintát. Az Azure-függvény teljes kódját a [githubon](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function)érheti el.

## <a name="define-claims"></a>Jogcímek meghatározása

A jogcím a Azure AD B2C szabályzat végrehajtása során ideiglenes adattárolást biztosít. A jogcímeket a [jogcímek sémája](claimsschema.md) szakaszon belül deklarálhatja. 

1. Nyissa meg a szabályzat Extensions (bővítmények) fájlját. Például: <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Keresse meg a [BuildingBlocks](buildingblocks.md) elemet. Ha az elem nem létezik, adja hozzá.
1. Keresse meg a [ClaimsSchema](claimsschema.md) elemet. Ha az elem nem létezik, adja hozzá.
1. Adja hozzá a következő jogcímeket a **ClaimsSchema** elemhez.  

```xml
<ClaimType Id="loyaltyId">
  <DisplayName>Your loyalty ID</DisplayName>
  <DataType>string</DataType>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
<ClaimType Id="promoCode">
  <DisplayName>Your promo code</DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
</ClaimType>
  <ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="add-the-restful-api-technical-profile"></a>A REST API technikai profiljának hozzáadása 

A [Rest-technikai profil](restful-technical-profile.md) támogatja a saját Rest-szolgáltatáshoz való kapcsolódást. Azure AD B2C adatokat küld a REST-szolgáltatásnak egy `InputClaims` gyűjteményben, és adatokat fogad vissza egy `OutputClaims` gyűjteményben. Keresse meg a **ClaimsProviders** elemet, és vegyen fel egy új jogcím-szolgáltatót az alábbiak szerint:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Check loyaltyId Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- Set the ServiceUrl with your own REST API endpoint -->
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/ValidateProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="loyaltyId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="promoCode" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Ebben a példában a a `userLanguage` rendszer elküldi a REST szolgáltatásnak a `lang` JSON-adattartalommal. A `userLanguage` jogcím értéke az aktuális felhasználói nyelvi azonosítót tartalmazza. További információ: jogcím- [feloldó](claim-resolver-overview.md).

### <a name="configure-the-restful-api-technical-profile"></a>A REST API technikai profiljának konfigurálása 

A REST API üzembe helyezése után állítsa be a `REST-ValidateProfile` technikai profil metaadatait úgy, hogy az tükrözze a saját Rest APIét, beleértve a következőket:

- **ServiceUrl**. Adja meg az REST API végpont URL-címét.
- **SendClaimsIn**. Adja meg, hogy a rendszer hogyan küldje el a bemeneti jogcímeket a REST-jogcím-szolgáltatónak.
- **AuthenticationType**. Állítsa be a REST-alapú jogcím-szolgáltató által végrehajtott hitelesítés típusát. 
- **AllowInsecureAuthInProduction**. Éles környezetben ügyeljen arra, hogy a metaadatokat a következőre állítsa be: `true`
    
További konfigurációkért tekintse meg a [Rest technikai profil metaadatait](restful-technical-profile.md#metadata) .

A fenti megjegyzések `AuthenticationType` és az `AllowInsecureAuthInProduction` éles környezetbe való áttéréskor végrehajtott módosítások megadása. A REST API-k éles környezetben történő biztonságossá tételéhez lásd: [biztonságos REST API](secure-rest-api.md).

## <a name="validate-the-user-input"></a>Felhasználói bevitel ellenőrzése

A felhasználó hűségi számának a regisztráció során való beszerzéséhez engedélyeznie kell a felhasználónak, hogy adja meg ezeket az adatfájlokat a képernyőn. Adja hozzá a **loyaltyId** kimeneti jogcímet a regisztrációs laphoz úgy, hogy hozzáadja a meglévő regisztrációs technikai profil szakaszának `OutputClaims` eleméhez. Adja meg a kimeneti jogcímek teljes listáját, hogy szabályozni lehessen a jogcímek megjelenítésének sorrendjét a képernyőn.  

Adja hozzá az érvényesítési technikai profil referenciáját a regisztrációs technikai profilhoz, amely meghívja a-t `REST-ValidateProfile` . Az új érvényesítési technikai profil az `<ValidationTechnicalProfiles>` alapházirendben definiált gyűjtemény tetejéhez lesz hozzáadva. Ez azt jelenti, hogy csak a sikeres ellenőrzés után Azure AD B2C továbblép a fiók létrehozásához a címtárban.   

1. Keresse meg a **ClaimsProviders** elemet. Vegyen fel egy új jogcím-szolgáltatót az alábbiak szerint:

    ```xml
    <ClaimsProvider>
      <DisplayName>Local Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="newPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surName"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    <ClaimsProvider>
      <DisplayName>Self Asserted</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SelfAsserted-Social">
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" />
          </InputClaims>
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surname"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile"/>
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

## <a name="include-a-claim-in-the-token"></a>Jogcím belefoglalása a jogkivonatba 

Ha vissza szeretné állítani a promóciós kód jogcímet a függő entitás alkalmazásához, adjon hozzá egy kimeneti jogcímet a <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> fájlhoz. A kimeneti jogcímek lehetővé teszik, hogy a rendszer sikeres felhasználói út után hozzáadja a jogcímet a jogkivonathoz, és az alkalmazás megkapja az alkalmazást. Módosítsa a technikai profil elemet a függő entitás szakaszban a kimeneti Jogcím hozzáadásához `promoCode` .
 
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
      <OutputClaim ClaimTypeReferenceId="promoCode" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>Egyéni szabályzat tesztelése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Győződjön meg arról, hogy az Azure AD-bérlőt tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki az Azure ad-bérlőt tartalmazó könyvtárat.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Alkalmazásregisztrációk**.
1. Válassza az **identitási élmény keretrendszert**.
1. Válassza az **egyéni házirend feltöltése** lehetőséget, majd töltse fel a módosított házirend-fájlokat: *TrustFrameworkExtensions.xml* és *SignUpOrSignin.xml*. 
1. Válassza ki a feltöltött regisztrációs vagy bejelentkezési szabályzatot, majd kattintson a **Futtatás most** gombra.
1. Regisztrálnia kell egy e-mail-cím használatával.
1. Kattintson a **regisztrálás most** hivatkozásra.
1. A **saját hűség-azonosítójában** írja be a 1234 értéket, majd kattintson a **Folytatás** gombra. Ezen a ponton egy érvényesítési hibaüzenet jelenik meg.
1. Váltson át egy másik értékre, és kattintson a **Folytatás** gombra.
1. Az alkalmazásnak visszaadott jogkivonat tartalmazza a `promoCode` jogcímet.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584295703,
  "nbf": 1584292103,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584292103,
  "auth_time": 1584292103,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "promoCode": "84362"
  ...
}
```

## <a name="next-steps"></a>Következő lépések

Az API-k biztonságossá tételéhez tekintse meg a következő cikkeket:

- [Bemutató: REST API jogcím-cserék integrálása a Azure AD B2C felhasználói úton az előkészítési lépésként](custom-policy-rest-api-claims-exchange.md)
- [A REST API biztonságossá tétele](secure-rest-api.md)
- [Hivatkozás: REST-technikai profil](restful-technical-profile.md)