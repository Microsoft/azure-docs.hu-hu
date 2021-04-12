---
title: Azure Active Directory B2C konfigurálása SAML-identitásszolgáltató az alkalmazásaihoz
title-suffix: Azure Active Directory B2C
description: A Azure Active Directory B2C konfigurálása az SAML protokoll érvényesítéséhez az alkalmazásokban (szolgáltatók). A Azure AD B2C az SAML-alkalmazáshoz (identitásszolgáltató) fog működni.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/05/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 652bc9a236a4e4b9d3f99dab640919f2be985984
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257721"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>SAML-alkalmazás regisztrálása Azure AD B2C

Ebből a cikkből megtudhatja, hogyan csatlakoztathatók a Security Assertion Markup Language (SAML) alkalmazások (szolgáltatók) a Azure Active Directory B2C (Azure AD B2C) hitelesítéshez.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="overview"></a>Áttekintés

Azok a szervezetek, amelyek az ügyfél-identitás-és hozzáférés-kezelési megoldás Azure AD B2C használják, az SAML protokollt használó alkalmazásokkal való integrációra lehet szükség. Az alábbi ábra azt mutatja be, hogy Azure AD B2C hogyan szolgál *identitás-szolgáltatóként* (identitásszolgáltató) az egyszeri bejelentkezés (SSO) SAML-alapú alkalmazásokkal való eléréséhez.

![Diagram a B2C mint Identity Provider a bal oldalon és a B2C szolgáltatóként a jobb oldalon.](media/saml-service-provider/saml-service-provider-integration.png)

1. Az alkalmazás létrehoz egy SAML-AuthN kérelmet, amelyet a rendszer az Azure AD B2C's SAML bejelentkezési végpontjának küld.
2. A felhasználó használhat egy Azure AD B2C helyi fiókot vagy bármely más összevont identitás-szolgáltatót (ha be van állítva) a hitelesítéshez.
3. Ha a felhasználó egy összevont identitás-szolgáltató használatával jelentkezik be, a rendszer egy jogkivonat-választ kap a Azure AD B2C.
4. Azure AD B2C létrehoz egy SAML-jogkivonatot, és elküldi azt az alkalmazásnak.

## <a name="prerequisites"></a>Előfeltételek

* Hajtsa végre a következő témakörben ismertetett lépéseket: Ismerkedés az [Egyéni szabályzatokkal Azure ad B2Cban](tutorial-create-user-flows.md?pivots=b2c-custom-policy). A *SocialAndLocalAccounts* egyéni házirendre van szüksége a cikkben tárgyalt egyéni házirend alapszintű csomagjából.
* Az SAML protokoll alapvető ismerete és az alkalmazás SAML-implementációjának ismerete.
* SAML-alkalmazásként konfigurált webalkalmazás. Ebben az oktatóanyagban egy általunk biztosított [SAML-teszt alkalmazást][samltest] használhat.

## <a name="components"></a>Összetevők

Ehhez a forgatókönyvhöz három fő összetevő szükséges:

* Egy SAML- **alkalmazás** , amely SAML-AuthN-kérelmek küldését, valamint az SAML-válaszok fogadását, dekódolását és ellenőrzését is lehetővé teszi Azure ad B2C. Az SAML-alkalmazást a függő entitás alkalmazásának vagy szolgáltatójának is nevezzük.
* Az SAML-alkalmazás nyilvánosan elérhető SAML- **metaadatainak végpontja** vagy XML-dokumentuma.
* [Azure ad B2C bérlő](tutorial-create-tenant.md)

Ha még nem rendelkezik SAML-alkalmazással és a hozzá tartozó metaadat-végponttal, a teszteléshez elérhetővé tett SAML-alkalmazást használhatja.

[SAML-teszt alkalmazás][samltest]

## <a name="set-up-certificates"></a>Tanúsítványok beállítása

Az alkalmazás és a Azure AD B2C közötti megbízhatósági kapcsolat létrehozásához mindkét szolgáltatásnak képesnek kell lennie létrehozni és érvényesíteni egymás aláírásait. Konfigurálhatja a X509-tanúsítványok konfigurálását Azure AD B2Cban és az alkalmazásban.

**Alkalmazás-tanúsítványok**

| Használat | Kötelező | Leírás |
| --------- | -------- | ----------- |
| SAML-kérelem aláírása  | No | A webalkalmazásban tárolt titkos kulccsal rendelkező tanúsítvány, amelyet az alkalmazás használ a Azure AD B2Cba küldött SAML-kérelmek aláírására. A webalkalmazásnak fel kell tüntetnie a nyilvános kulcsot az SAML metaadat-végpontján keresztül. Azure AD B2C érvényesíti az SAML-kérelem aláírását a nyilvános kulccsal az alkalmazás metaadatainak használatával.|
| SAML-állítás titkosítása  | No | A webalkalmazásban tárolt titkos kulccsal rendelkező tanúsítvány. A webalkalmazásnak fel kell tüntetnie a nyilvános kulcsot az SAML metaadat-végpontján keresztül. A Azure AD B2C a nyilvános kulcs használatával titkosíthatja az alkalmazásra vonatkozó állításokat. Az alkalmazás a titkos kulcsot használja az állítás visszafejtéséhez.|

**Tanúsítványok Azure AD B2C**

| Használat | Kötelező | Leírás |
| --------- | -------- | ----------- |
| SAML-válaszok aláírása | Yes  | Azure AD B2Cban tárolt titkos kulccsal rendelkező tanúsítvány. Ezt a tanúsítványt a Azure AD B2C használja az alkalmazásnak küldött SAML-válasz aláírásához. Az alkalmazás beolvassa a Azure AD B2C metaadatok nyilvános kulcsát az SAML-válasz aláírásának ellenőrzéséhez. |
| SAML-állítás aláírása | Yes | Azure AD B2Cban tárolt titkos kulccsal rendelkező tanúsítvány. Ezt a tanúsítványt a Azure AD B2C használja az SAML-válasz érvényesítésének aláírásához. Az `<saml:Assertion>` SAML-válasz része.  |

Éles környezetben javasoljuk, hogy használjon egy nyilvános hitelesítésszolgáltató által kiadott tanúsítványokat. Ezt az eljárást azonban önaláírt tanúsítványokkal is elvégezheti.

### <a name="create-a-policy-key"></a>Házirend-kulcs létrehozása

Az alkalmazás és a Azure AD B2C közötti megbízhatósági kapcsolat létrehozásához hozzon létre egy SAML-válasz aláíró tanúsítványát. Azure AD B2C ezt a tanúsítványt használja az alkalmazásnak küldött SAML-válasz aláírására. Az alkalmazás beolvassa a Azure AD B2C metaadatok nyilvános kulcsát az SAML-válasz aláírásának ellenőrzéséhez. 

> [!TIP]
> Az ebben a szakaszban létrehozott házirend-kulcsot más célra is használhatja, például az [SAML](saml-service-provider-options.md#saml-assertions-signature)-jogcímen való bejelentkezéshez. 

### <a name="obtain-a-certificate"></a>Tanúsítvány beszerzése

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

### <a name="upload-the-certificate"></a>A tanúsítvány feltöltése

A tanúsítványt a Azure AD B2C bérlőben kell tárolnia.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. Az Áttekintés lapon válassza az **identitási élmény keretrendszert**.
1. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás** gombra.
1. A **Beállítások** területen válassza a lehetőséget `Upload` .
1. Adja meg a szabályzat kulcsának **nevét** . Például: `SamlIdpCert`. A rendszer automatikusan hozzáadja az előtagot a `B2C_1A_` kulcs nevéhez.
1. Tallózással keresse meg és válassza ki a tanúsítvány. pfx fájlját a titkos kulccsal.
1. Kattintson a **Létrehozás** lehetőségre.

## <a name="enable-your-policy-to-connect-with-a-saml-application"></a>Az SAML-alkalmazáshoz való kapcsolódás engedélyezése a házirend számára

Az SAML-alkalmazáshoz való kapcsolódáshoz a Azure AD B2C SAML-válaszokat kell tudnia létrehozni.

Nyissa meg `SocialAndLocalAccounts\` **`TrustFrameworkExtensions.xml`** az egyéni házirend alapszintű csomagját.

Keresse meg a `<ClaimsProviders>` szakaszt, és adja hozzá a következő XML-kódrészletet az SAML-válasz létrehozójának megvalósításához.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>

    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="IssuerUri">https://issuerUriMyAppExpects</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
      </CryptographicKeys>
      <InputClaims/>
      <OutputClaims/>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="configure-the-issueruri-of-the-saml-response"></a>Az SAML-válasz IssuerUri konfigurálása

Az `IssuerUri` SAML-jogkivonat kiállítói technikai profiljában módosíthatja a metaadat-elem értékét. Ez a módosítás a `issuerUri` Azure ad B2C SAML-válaszában visszaadott attribútumban jelenik meg. Az alkalmazást úgy kell konfigurálni, hogy az `issuerUri` SAML-válaszok érvényesítése során is elfogadja.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="IssuerUri">https://issuerUriMyAppExpects</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="configure-your-policy-to-issue-a-saml-response"></a>A szabályzat konfigurálása SAML-válasz kibocsátásához

Most, hogy a házirend létrehozhat SAML-válaszokat, konfigurálnia kell a szabályzatot, hogy az alapértelmezett JWT válasz helyett SAML-választ adjon ki az alkalmazásnak.

### <a name="create-a-sign-up-or-sign-in-policy-configured-for-saml"></a>SAML használatára konfigurált regisztrációs vagy bejelentkezési szabályzat létrehozása

1. Hozzon létre egy másolatot a *SignUpOrSignin.xml* fájlról a Starter Pack munkakönyvtárában, és mentse azt egy új néven. Például *SignUpOrSigninSAML.xml*. Ez a fájl a függő entitások házirendjének fájlja, és alapértelmezés szerint JWT-válasz kibocsátására van konfigurálva.

1. Nyissa meg a *SignUpOrSigninSAML.xml* fájlt az előnyben részesített szerkesztőben.

1. Módosítsa az `PolicyId` és a `PublicPolicyUri` házirendet úgy, hogy _B2C_1A_signup_signin_saml_ és az `http://<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin_saml` alább látható módon.

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. A felhasználói út végén Azure AD B2C tartalmaz egy `SendClaims` lépést. Ez a lépés a jogkivonat kiállítói technikai profiljára hivatkozik. Ha az alapértelmezett JWT válasz helyett SAML-választ szeretne kiadni, módosítsa a `SendClaims` lépést az új SAML-jogkivonat kiállítói technikai profiljára való hivatkozáshoz `Saml2AssertionIssuer` .

Adja hozzá a következő XML-kódrészletet közvetlenül az `<RelyingParty>` elem előtt. Ez az XML felülírja a 7. számot a _SignUpOrSignIn_ felhasználói úton. Ha az alapszintű csomag egy másik mappájából indult, vagy a felhasználói utat a előkészítési lépések hozzáadásával vagy eltávolításával testreszabta, győződjön meg arról, `order` hogy az elemben szereplő szám megfelel a jogkivonat-kiállítói lépés felhasználói útján megadott számnak. Például a másik alapszintű csomag mappáiban a megfelelő lépés száma 4 a, a `LocalAccounts` 6 és a `SocialAccounts` 9 esetében `SocialAndLocalAccountsWithMfa` ).

```xml
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>
      <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys>
```

A függő entitás elem határozza meg, hogy az alkalmazás melyik protokollt használja. A mező alapértelmezett értéke: `OpenId`. Az elemet a következőre kell `Protocol` módosítani: `SAML` . A kimeneti jogcímek létrehozzák a jogcímek hozzárendelését az SAML-állításhoz.

Cserélje le az `<TechnicalProfile>` elem teljes elemét a `<RelyingParty>` következő technikai profil XML-kódjára. Frissítse a `tenant-name` Azure ad B2C bérlő nevét.

```xml
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
```

A végleges függő entitás házirend-fájljának a következő XML-kódhoz hasonlóan kell kinéznie:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="contoso.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin_saml"
  PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_signup_signin_saml">

  <BasePolicy>
    <TenantId>contoso.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <UserJourneys>
    <UserJourney Id="SignUpOrSignIn">
      <OrchestrationSteps>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
      </OrchestrationSteps>
    </UserJourney>
  </UserJourneys>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

> [!NOTE]
> Ugyanezt a folyamatot követve más típusú felhasználói folyamatokat is megvalósíthat (például a bejelentkezést, a jelszó-visszaállítást vagy a profil szerkesztési folyamatait).

### <a name="upload-your-policy"></a>A szabályzat feltöltése

Mentse a módosításokat, és töltse fel az új **TrustFrameworkExtensions.xml** és **SignUpOrSigninSAML.xml** házirend-fájlokat a Azure Portal.

### <a name="test-the-azure-ad-b2c-idp-saml-metadata"></a>A Azure AD B2C identitásszolgáltató SAML-metaadatok tesztelése

A házirend-fájlok feltöltése után a Azure AD B2C a konfigurációs adatok használatával hozza elő az Identity Provider SAML-metaadatokat tartalmazó dokumentumát, amelyet az alkalmazás használni fog. Az SAML-metaadatok dokumentuma tartalmazza a szolgáltatások (például a bejelentkezés és a kijelentkezési módszerek, a tanúsítványok stb.) helyét.

A Azure AD B2C szabályzat metaadatai a következő URL-címen érhetők el:

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/samlp/metadata`

Cserélje le a helyére `<tenant-name>` a Azure ad B2C bérlő nevét és a `<policy-name>` szabályzat nevét (azonosítóját), például:

`https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1A_signup_signin_saml/samlp/metadata`

## <a name="register-your-saml-application-in-azure-ad-b2c"></a>Az SAML-alkalmazás regisztrálása Azure AD B2C

Ahhoz Azure AD B2C, hogy megbízzon az alkalmazásban, létre kell hoznia egy Azure AD B2C alkalmazás-regisztrációt, amely olyan konfigurációs adatokat tartalmaz, mint például az alkalmazás metaadat-végpontja.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C** lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C** lehetőséget.
1. Válassza a **Alkalmazásregisztrációk** lehetőséget, majd válassza az **új regisztráció** lehetőséget.
1. Adja meg az alkalmazás **nevét** . Például: *SAMLApp1*.
1. A **támogatott fiókok típusai** területen **csak a szervezeti címtárban** válassza a fiókok elemet.
1. Az **átirányítási URI** területen válassza a Web lehetőséget, majd írja be a **következőt**: `https://localhost` . Ezt az értéket később módosíthatja az alkalmazás regisztrációjának jegyzékfájljában.
1. Válassza a **Regisztráció** lehetőséget.

### <a name="configure-your-application-in-azure-ad-b2c"></a>Az alkalmazás konfigurálása Azure AD B2C

Az SAML-alkalmazások esetében több tulajdonságot kell konfigurálnia az alkalmazás regisztrációs jegyzékfájljában.

1. A [Azure Portal](https://portal.azure.com)navigáljon az előző szakaszban létrehozott alkalmazás-regisztrációhoz.
1. A **kezelés** területen válassza a **jegyzékfájl** elemet a jegyzékfájl-szerkesztő megnyitásához, majd módosítsa a következő szakaszokban ismertetett tulajdonságokat.

#### <a name="add-the-identifier"></a>Az azonosító hozzáadása

Amikor az SAML-alkalmazás kérelmet küld Azure AD B2Cra, az SAML-AuthN kérelem tartalmaz egy `Issuer` attribútumot, amely általában megegyezik az alkalmazás metaadatainak értékével `entityID` . Azure AD B2C ezt az értéket használja az alkalmazás regisztrálásának megkereséséhez a címtárban, és a konfiguráció beolvasása. Ahhoz, hogy a keresés sikeres legyen, az `identifierUri` alkalmazás regisztrációjában szerepelnie kell egy olyan értéknek, amely megfelel az `Issuer` attribútumnak.

A regisztrációs jegyzékfájlban keresse meg a `identifierURIs` paramétert, és adja hozzá a megfelelő értéket. Ez az érték ugyanaz lesz az érték, amely az alkalmazás EntityId SAML AuthN-kérelmében, valamint az `entityID` alkalmazás metaadatainak értékében van konfigurálva.

Az alábbi példa az `entityID` SAML-metaadatokat mutatja be:

```xml
<EntityDescriptor ID="id123456789" entityID="https://samltestapp2.azurewebsites.net" validUntil="2099-12-31T23:59:59Z" xmlns="urn:oasis:names:tc:SAML:2.0:metadata">
```

A `identifierUris` tulajdonság csak a tartomány URL-címeinek elfogadását fogja elfogadni `tenant-name.onmicrosoft.com` .

```json
"identifierUris":"https://samltestapp2.azurewebsites.net",
```

#### <a name="share-the-applications-metadata-with-azure-ad-b2c"></a>Az alkalmazás metaadatainak megosztása Azure AD B2C

Miután betöltötte az alkalmazás regisztrációját `identifierUri` , Azure ad B2C az alkalmazás metaadataival érvényesíti az SAML-AuthN kérelmet, és meghatározza a válaszadás módját.

Javasoljuk, hogy az alkalmazás nyilvánosan elérhető metaadat-végpontot tegyen elérhetővé.

Ha az SAML-metaadatok URL-címében és az alkalmazás regisztrációs jegyzékfájljában *is vannak* megadva tulajdonságok, a rendszer *egyesíti* őket. A metaadatok URL-címében megadott tulajdonságokat a rendszer először dolgozza fel, és elsőbbséget élvez.

Ha például az SAML-teszt alkalmazást használja, a következő értéket kell használnia az `samlMetadataUrl` alkalmazás jegyzékfájljában:

```json
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="override-or-set-the-assertion-consumer-url-optional"></a>Felülbírálja vagy beállítja az állítási felhasználó URL-címét (nem kötelező)

Beállíthatja azt a válasz-URL-címet, amelyre az Azure AD B2C SAML-válaszokat küld. A válasz URL-címei konfigurálhatók az alkalmazás jegyzékfájlján belül. Ez a konfiguráció akkor hasznos, ha az alkalmazás nem tesz közzé nyilvánosan elérhető metaadat-végpontot.

Az SAML-alkalmazások válaszának URL-címe az a végpont, amelyen az alkalmazás az SAML-válaszokat várja. Az alkalmazás általában megadja ezt az URL-címet a metaadat-dokumentumban az `AssertionConsumerServiceUrl` attribútum alatt, az alábbi ábrán látható módon:

```xml
<SPSSODescriptor AuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    ...
    <AssertionConsumerService index="0" isDefault="true" Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://samltestapp2.azurewebsites.net/SP/AssertionConsumer" />        
</SPSSODescriptor>
```

Ha szeretné felülbírálni az attribútumban megadott metaadatokat `AssertionConsumerServiceUrl` , vagy az URL-cím nem szerepel a metaadat-dokumentumban, akkor az URL-címet a tulajdonságban konfigurálhatja a jegyzékfájlban `replyUrlsWithType` . A a következőre lesz `BindingType` beállítva: `HTTP POST` .

Ha például az SAML-teszt alkalmazást használja, a `url` tulajdonságot a `replyUrlsWithType` következő JSON-kódrészletben látható értékre állíthatja be.

```json
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="override-or-set-the-logout-url-optional"></a>A kijelentkezési URL-cím felülbírálása vagy beállítása (nem kötelező)

Beállíthatja azt a kijelentkezési URL-címet, amelyre a Azure AD B2C elküldi a felhasználót egy kijelentkezési kérelem után. A válasz URL-címei konfigurálhatók az alkalmazás Jegyzékfájlján belül.

Ha szeretné felülbírálni az attribútumban megadott metaadatokat `SingleLogoutService` , vagy az URL-cím nem szerepel a metaadat-dokumentumban, akkor a tulajdonságban konfigurálhatja azt a jegyzékfájlban `Logout` . A a következőre lesz `BindingType` beállítva: `Http-Redirect` .

Az alkalmazás általában megadja ezt az URL-címet a metaadat-dokumentumban az `AssertionConsumerServiceUrl` attribútum alatt, az alábbi ábrán látható módon:

```xml
<IDPSSODescriptor WantAuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://samltestapp2.azurewebsites.net/logout" ResponseLocation="https://samltestapp2.azurewebsites.net/logout" />

</IDPSSODescriptor>
```

Ha például az SAML-teszt alkalmazást szeretné használni, hagyja a következőt `logoutUrl` `https://samltestapp2.azurewebsites.net/logout` :

```json
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

> [!NOTE]
> Ha úgy dönt, hogy a válasz URL-címét és a kijelentkezési URL-címet az alkalmazás jegyzékfájljában úgy állítja be, hogy az alkalmazás metaadat-végpontját a tulajdonságon keresztül tölti fel `samlMetadataUrl` , Azure ad B2C nem ellenőrzi az SAML-kérelem aláírását, és nem fogja titkosítani az SAML-választ.

## <a name="configure-azure-ad-b2c-as-a-saml-idp-in-your-saml-application"></a>Azure AD B2C konfigurálása SAML-identitásszolgáltató az SAML-alkalmazásban

Az utolsó lépés a Azure AD B2C engedélyezése SAML-identitásszolgáltató az SAML-alkalmazásban. Minden alkalmazás eltérő, és a lépések változhatnak. Részletekért olvassa el az alkalmazás dokumentációját.

A metaadatok *statikus metaadatok* vagy *dinamikus metaadatok* használatával konfigurálhatók az alkalmazásban. Statikus módban másolja a metaadatokat vagy annak egy részét a Azure AD B2C házirend metaadataiból. Dinamikus módban adja meg a metaadatok URL-címét, és lehetővé teszi, hogy az alkalmazás dinamikusan olvassa a metaadatokat.

Általában a következők szükségesek:

* **Metaadatok**: használja a formátumot `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata` .
* **Kiállító**: az SAML-kérelem `issuer` értékének meg kell egyeznie az `identifierUris` alkalmazás regisztrációs jegyzékfájljának elemében konfigurált URI-k egyikével. Ha az SAML `issuer` -kérelem neve nem létezik az `identifierUris` elemben, [vegye fel azt az alkalmazás regisztrációs jegyzékfájlba](#add-the-identifier). Például: `https://contoso.onmicrosoft.com/app-name`. 
* **Bejelentkezési URL-cím/SAML-végpont/SAML URL-cím**: az XML-elem Azure ad B2C SAML-házirend metaadat-fájljában található értéket kell megnéznie `<SingleSignOnService>` .
* **Tanúsítvány**: Ez a tanúsítvány *B2C_1A_SamlIdpCert*, de a titkos kulcs nélkül. A tanúsítvány nyilvános kulcsának beszerzése:

    1. Nyissa meg a fent megadott metaadat-URL-címet.
    1. Másolja a `<X509Certificate>` elemben található értéket.
    1. Illessze be egy szövegfájlba.
    1. Mentse a szövegfájlt *. cer* fájlként.

### <a name="test-with-the-saml-test-app"></a>Tesztelés az SAML-teszt alkalmazással

A konfiguráció teszteléséhez [SAML-teszt alkalmazást][samltest] használhat:

* Módosítsa a bérlő nevét.
* Frissítse a szabályzat nevét, például *B2C_1A_signup_signin_saml*.
* Adja meg ezt a kiállítói URI-t. Használja az alkalmazás regisztrációs jegyzékfájljában található elem egyik URI-azonosítóját `identifierUris` , például: `https://contoso.onmicrosoft.com/app-name` .

Válassza a **Bejelentkezés** lehetőséget, és a felhasználói bejelentkezési képernyőn kell megjelennie. Bejelentkezéskor a rendszer visszaküldi az SAML-választ a minta alkalmazásnak.

## <a name="supported-and-unsupported-saml-modalities"></a>Támogatott és nem támogatott SAML-módozatok

A következő SAML-alkalmazási forgatókönyvek a saját metaadat-végpontján keresztül támogatottak:

* Több kijelentkezési URL-cím vagy POST-kötés a kijelentkezési URL-címhez az alkalmazás/szolgáltatás egyszerű objektumában.
* Egy aláíró kulcs megadásával ellenőrizheti a függő entitás (RP) kérelmeit az alkalmazás/szolgáltatás egyszerű objektumában.
* A jogkivonat-titkosítási kulcs megadásához az alkalmazás/szolgáltatás egyszerű objektumában.
* Személyazonosság-szolgáltató – kezdeményezett bejelentkezés, ahol az Identitáskezelő Azure AD B2C.

## <a name="next-steps"></a>Következő lépések

- Szerezze be az SAML-teszt webalkalmazást [Azure ad B2C GitHub közösségi](https://github.com/azure-ad-b2c/saml-sp-tester)adattárból.
- Tekintse [meg az SAML-alkalmazások regisztrálásának lehetőségeit Azure ad B2C](saml-service-provider-options.md)

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

::: zone-end
