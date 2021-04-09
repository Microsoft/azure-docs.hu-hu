---
title: REST API követelések cseréje – Azure Active Directory B2C
description: REST API a jogcímeket a Active Directory B2C egyéni házirendjeihez adja hozzá.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 84053df34ffda0d4686ad80a9e5f3af00ac53d72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94949496"
---
# <a name="walkthrough-add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Forgatókönyv: REST API-jogcímek hozzáadása egyéni házirendekhez Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A Azure Active Directory B2C (Azure AD B2C) lehetővé teszi az identitás-fejlesztők számára, hogy a felhasználói úton lévő REST API-val integrálják az interakciókat. A bemutató végén létrehozhat egy Azure AD B2C felhasználói utat, amely a [Rest-szolgáltatásokkal](custom-policy-rest-api-intro.md)kommunikál.

Ebben az esetben a felhasználó jogkivonat-adatfeldolgozását a vállalati üzletági munkafolyamatok integrálásával gazdagítjuk. Helyi vagy összevont fiókkal történő regisztráció vagy bejelentkezés során Azure AD B2C meghívja a REST APIt, hogy lekérje a felhasználó kibővített profiljának adatait egy távoli adatforrásból. Ebben a példában a Azure AD B2C elküldi a felhasználó egyedi azonosítóját, a objectId. A REST API ezután visszaadja a felhasználó fiókjának egyenlegét (véletlenszerű szám). Ez a minta kiindulási pontként használható a saját CRM-rendszer, a marketing-adatbázis vagy az üzletági munkafolyamatok integrálásához.

Az interakciót érvényesítő technikai profilként is megtervezheti. Ez akkor megfelelő, ha a REST API a képernyőn lévő és a jogcímek visszaadását fogja érvényesíteni. További információ [: bemutató: REST API jogcím-cserék integrálása Azure ad B2C felhasználói úton a felhasználói bevitel érvényesítéséhez](custom-policy-rest-api-claims-validation.md).

## <a name="prerequisites"></a>Előfeltételek

- Hajtsa végre az [Ismerkedés az egyéni szabályzatokkal](custom-policy-get-started.md)című témakör lépéseit. A bejelentkezéshez és a helyi fiókokkal való bejelentkezéshez egyéni szabályzatot kell használnia.
- Ismerje meg, hogyan [integrálhatja REST API jogcímek cseréjét a Azure ad B2C egyéni szabályzatba](custom-policy-rest-api-intro.md).

## <a name="prepare-a-rest-api-endpoint"></a>REST API végpont előkészítése

Ehhez a bemutatóhoz rendelkeznie kell egy REST API, amely ellenőrzi, hogy a felhasználó Azure AD B2C objectId regisztrálva van-e a háttérrendszer-szolgáltatásban. Ha regisztrálva van, a REST API a felhasználói fiók egyenlegét adja vissza. Ellenkező esetben a REST API regisztrálja az új fiókot a címtárban, és visszaadja a kezdő egyenleget `50.00` .

A következő JSON-kód azt mutatja be, Azure AD B2C az REST API végpontnak küldi el azokat. 

```json
{
    "objectId": "User objectId",
    "lang": "Current UI language"
}
```

Miután a REST API érvényesíti az adatait, a következő JSON-adataival kell visszaadnia a HTTP 200 (ok) t:

```json
{
    "balance": "760.50"
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
<ClaimType Id="balance">
  <DisplayName>Your Balance</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="add-the-restful-api-technical-profile"></a>A REST API technikai profiljának hozzáadása 

A [Rest-es technikai profil](restful-technical-profile.md) támogatást nyújt a saját Rest-szolgáltatással való együttműködéshez. Azure AD B2C adatokat küld a REST-szolgáltatásnak egy `InputClaims` gyűjteményben, és adatokat fogad vissza egy `OutputClaims` gyűjteményben. Keresse meg a **ClaimsProviders** elemet a <em>**`TrustFrameworkExtensions.xml`**</em> fájlban, és vegyen fel egy új jogcím-szolgáltatót az alábbiak szerint:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- Set the ServiceUrl with your own REST API endpoint -->
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="balance" />
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

## <a name="add-an-orchestration-step"></a>Előkészítési lépés hozzáadása

A [felhasználói interakciósorozatok](userjourneys.md) explicit elérési utakat határoznak meg, amelyen keresztül egy szabályzat lehetővé teszi a jogcímfelhasználó alkalmazásnak a kívánt jogcímek beszerzését a felhasználó számára. A felhasználói út olyan összehangoló sorozatot jelöl, amelyet egy sikeres tranzakcióhoz kell követni. Felvehet vagy kivonja az előkészítési lépéseket. Ebben az esetben egy új előkészítési lépést fog hozzáadni, amely az alkalmazásnak a REST API hívásával történő regisztráció vagy bejelentkezés után a felhasználó által megadott információk kiegészítésére szolgál.

1. Nyissa meg a szabályzat alapfájlját. Például: <em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em> .
1. Keresse meg az `<UserJourneys>` elemet. Másolja a teljes elemet, majd törölje.
1. Nyissa meg a szabályzat Extensions (bővítmények) fájlját. Például: <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Illessze be a `<UserJourneys>` into the Extensions (kiterjesztések) fájlba a elem bezárását követően `<ClaimsProviders>` .
1. Keresse meg a t `<UserJourney Id="SignUpOrSignIn">` , és adja hozzá az alábbi előkészítési lépést az utolsó előtt.

    ```xml
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

1. Az utolsó előkészítési lépés újrabontása a `Order` to értékre való módosításával `8` . Az utolsó két előkészítési lépésnek a következőhöz hasonlóan kell kinéznie:

    ```xml
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>

    <OrchestrationStep Order="8" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    ```

1. Ismételje meg az utolsó két lépést a **ProfileEdit** és a **PasswordReset** felhasználói útvonalon.


## <a name="include-a-claim-in-the-token"></a>Jogcím belefoglalása a jogkivonatba 

Ha vissza szeretné `balance` állítani a jogcímet a függő entitás alkalmazásához, adjon hozzá egy kimeneti jogcímet a <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> fájlhoz. A kimeneti jogcímek hozzáadásával a rendszer a jogkivonatot sikeres felhasználói út után kiállítja a jogkivonatba, és a rendszer elküldi az alkalmazásnak. Módosítsa a technikai profil elemet a függő entitás szakaszban a `balance` kimeneti jogcímként való hozzáadáshoz.
 
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
      <OutputClaim ClaimTypeReferenceId="balance" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

Ismételje meg ezt a lépést a **ProfileEdit.xml**, és **PasswordReset.xml** a felhasználói útvonalakat.

Mentse a módosított fájlokat: *TrustFrameworkBase.xml*, *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml* és *PasswordReset.xml*. 

## <a name="test-the-custom-policy"></a>Egyéni szabályzat tesztelése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Győződjön meg arról, hogy az Azure AD-bérlőt tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki az Azure ad-bérlőt tartalmazó könyvtárat.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Alkalmazásregisztrációk**.
1. Válassza az **identitási élmény keretrendszert**.
1. Válassza **az egyéni házirend feltöltése** lehetőséget, majd töltse fel a módosított házirend-fájlokat: *TrustFrameworkBase.xml*, és *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml* és *PasswordReset.xml*. 
1. Válassza ki a feltöltött regisztrációs vagy bejelentkezési szabályzatot, majd kattintson a **Futtatás most** gombra.
1. Regisztrálnia kell egy e-mail-cím vagy egy Facebook-fiók használatával.
1. Az alkalmazásnak visszaadott jogkivonat tartalmazza a `balance` jogcímet.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584961516,
  "nbf": 1584957916,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584957916,
  "auth_time": 1584957916,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "balance": "202.75"
  ...
}
```

## <a name="next-steps"></a>Következő lépések

Az API-k biztonságossá tételéhez tekintse meg a következő cikkeket:

- [Bemutató: REST API jogcím-cserék integrálása a Azure AD B2C felhasználói úton az előkészítési lépésként](custom-policy-rest-api-claims-exchange.md)
- [A REST API biztonságossá tétele](secure-rest-api.md)
- [Hivatkozás: REST-technikai profil](restful-technical-profile.md)