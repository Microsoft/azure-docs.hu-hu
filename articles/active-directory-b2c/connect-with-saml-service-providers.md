---
title: Azure AD B2C konfigurálása SAML-identitásszolgáltató az alkalmazásaihoz
title-suffix: Azure AD B2C
description: A Azure AD B2C konfigurálása az SAML protokoll érvényesítéséhez az alkalmazásokban (szolgáltatók). A Azure AD B2C az SAML-alkalmazáshoz (identitásszolgáltató) fog működni.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/17/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 96a72dbc0e45ebd50a49000ae66e3713cb28aa9a
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98916921"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>SAML-alkalmazás regisztrálása Azure AD B2C

Ebből a cikkből megtudhatja, hogyan konfigurálhatja a Azure Active Directory B2C (Azure AD B2C), hogy Security Assertion Markup Language (SAML) identitás-szolgáltatóként (identitásszolgáltató) működjön az alkalmazásaihoz.

## <a name="scenario-overview"></a>A forgatókönyv áttekintése

Azok a szervezetek, amelyek az ügyfél identitás-és hozzáférés-kezelési megoldásként Azure AD B2C használják, interakciót igényelhetnek az SAML protokoll használatával történő hitelesítésre konfigurált identitás-szolgáltatókkal vagy alkalmazásokkal.

Azure AD B2C az SAML együttműködési képességet kétféleképpen éri el:

* *Identitás-szolgáltatóként* (identitásszolgáltató) és az egyszeri bejelentkezés (SSO) az SAML-alapú szolgáltatók (az alkalmazások) használatával történő megvalósítása
* *Szolgáltatóként (SP* ) és az SAML-alapú Identitáskezelés, például a Salesforce és az ADFS használatával való interakció

![Diagram a B2C mint Identity Provider a bal oldali és a B2C szolgáltatóként a jobb oldalon](media/saml-identity-provider/saml-idp-diagram-01.jpg)

A két nem kizárólagos alapszintű forgatókönyvek összefoglalása az SAML használatával:

| Eset | Azure AD B2C szerepkör | Használati útmutató |
| -------- | ----------------- | ------- |
| Az alkalmazás egy SAML-állítást vár a hitelesítés elvégzéséhez. | **Azure AD B2C identitás-szolgáltatóként (identitásszolgáltató) működik**<br />Azure AD B2C SAML-identitásszolgáltató viselkedik az alkalmazásokban. | Ez a cikk. |
| A felhasználóknak egyszeri bejelentkezésre van szükségük egy SAML-kompatibilis identitás-szolgáltatóval, például az ADFS, a Salesforce vagy a Shibboleth.  | **Azure AD B2C szolgáltatóként működik (SP)**<br />A Azure AD B2C szolgáltatóként működik, amikor az SAML-identitás szolgáltatóhoz csatlakozik. Ez egy összevonási proxy az alkalmazás és a SAML-identitás szolgáltatója között.  | <ul><li>[Bejelentkezés beállítása SAML-identitásszolgáltató az ADFS-ben egyéni szabályzatok használatával](identity-provider-adfs.md)</li><li>[Bejelentkezés beállítása Salesforce SAML-szolgáltatóval egyéni szabályzatok használatával](identity-provider-salesforce-saml.md)</li></ul> |

## <a name="prerequisites"></a>Előfeltételek

* Hajtsa végre a következő témakörben ismertetett lépéseket: Ismerkedés az [Egyéni szabályzatokkal Azure ad B2Cban](custom-policy-get-started.md). A *SocialAndLocalAccounts* egyéni házirendre van szüksége a cikkben tárgyalt egyéni házirend alapszintű csomagjából.
* A Security Assertion Markup Language (SAML) protokoll alapszintű ismerete.
* SAML-szolgáltatóként (SP) konfigurált webalkalmazás. Ebben az oktatóanyagban egy általunk biztosított [SAML-teszt alkalmazást][samltest] használhat.

## <a name="components-of-the-solution"></a>A megoldás összetevői

Ehhez a forgatókönyvhöz három fő összetevő szükséges:

* Az SAML- **szolgáltató** lehetővé teszi az SAML-kérelmek küldését, valamint a Azure ad B2C-ból származó SAML-kijelentések fogadását, dekódolását és reagálását. A szolgáltatót a függő entitás alkalmazásnak is nevezzük.
* Nyilvánosan elérhető SAML- **metaadatok végpontja** a szolgáltató számára.
* [Azure AD B2C bérlő](tutorial-create-tenant.md)

Ha még nem rendelkezik SAML-szolgáltatóval és a hozzá tartozó metaadat-végponttal, akkor a teszteléshez elérhetővé tett SAML-alkalmazást használhatja.

[SAML-teszt alkalmazás][samltest]

## <a name="1-set-up-certificates"></a>1. tanúsítványok beállítása

A szolgáltató és a Azure AD B2C közötti megbízhatósági kapcsolat létrehozásához meg kell adnia a webalkalmazás X509 tanúsítványait.

* **Szolgáltatói tanúsítványok**
  * A webalkalmazásban tárolt titkos kulccsal rendelkező tanúsítvány. Ezt a tanúsítványt a szolgáltató használja a Azure AD B2Cba küldendő SAML-kérelem aláírásához. Azure AD B2C beolvassa a nyilvános kulcsot a szolgáltatói metaadatokból az aláírás ellenőrzéséhez.
  * Választható A webalkalmazásban tárolt titkos kulccsal rendelkező tanúsítvány. Azure AD B2C beolvassa a nyilvános kulcsot a szolgáltatói metaadatokból az SAML-állítás titkosításához. A szolgáltató ezután a titkos kulcsot használja az állítás visszafejtéséhez.
* **Tanúsítványok Azure AD B2C**
  * Azure AD B2C található titkos kulccsal rendelkező tanúsítvány. Ezt a tanúsítványt a Azure AD B2C a szolgáltatónak küldött SAML-válasz aláírására használja. A szolgáltató beolvassa a Azure AD B2C metaadatok nyilvános kulcsát az SAML-válasz aláírásának ellenőrzéséhez.

Használhat egy nyilvános hitelesítésszolgáltató által kiadott tanúsítványt, vagy ebben az oktatóanyagban egy önaláírt tanúsítványt.

### <a name="11-create-a-self-signed-certificate"></a>1,1 önaláírt tanúsítvány létrehozása

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

### <a name="12-upload-the-certificate"></a>1,2 a tanúsítvány feltöltése

Ezután töltse fel az SAML-jogkivonatot és a válasz aláíró tanúsítványát Azure AD B2C.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és keresse meg a Azure ad B2C bérlőt.
1. A **házirendek** területen válassza az **identitási élmény keretrendszere** , majd a **házirend-kulcsok** elemet.
1. Válassza a **Hozzáadás**, majd a **Beállítások**  >  **feltöltés** lehetőséget.
1. Írjon be egy **nevet**, például *SamlIdpCert*. Az előtag *B2C_1A_* automatikusan hozzáadódik a kulcs nevéhez.
1. Töltse fel a tanúsítványt a fájl feltöltése vezérlőelem használatával.
1. Adja meg a tanúsítvány jelszavát.
1. Válassza a **Létrehozás** lehetőséget.
1. Ellenőrizze, hogy a kulcs a várt módon jelenik-e meg. Például *B2C_1A_SamlIdpCert*.

## <a name="2-prepare-your-policy"></a>2. a szabályzat előkészítése

### <a name="21-create-the-saml-token-issuer"></a>2,1 az SAML-jogkivonat kiállítójának létrehozása

Most adja hozzá a bérlői képességet, hogy SAML-jogkivonatokat bocsásson ki az [SAML-jogkivonat kiállítója](saml-issuer-technical-profile.md) és a [SAML-munkamenet-szolgáltató](custom-policy-reference-sso.md#samlssosessionprovider) technikai profiljai használatával.

Nyissa meg `SocialAndLocalAccounts\` **`TrustFrameworkExtensions.xml`** az egyéni házirend alapszintű csomagját.

Keresse meg a `<ClaimsProviders>` szakaszt, és adja hozzá a következő XML-kódrészletet.

Módosíthatja a `IssuerUri` metaadatok értékét. Ez az a kiállítói URI, amelyet a rendszer az SAML-válaszban ad vissza Azure AD B2Cból. A függő entitás alkalmazását úgy kell konfigurálni, hogy a kiállítói URI-t fogadja az SAML-érvényesítés ellenőrzése során.

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
        <!-- The issuer contains the policy name; it should be the same name as configured in the relying party application. B2C_1A_signup_signin_SAML is used below. -->
        <!--<Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml</Item>-->
      </Metadata>
      <CryptographicKeys>
        <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
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

## <a name="3-add-the-saml-relying-party-policy"></a>3. adja hozzá az SAML függő entitás házirendjét

Most, hogy a bérlő kiállíthatja az SAML-kijelentéseket, létre kell hoznia az SAML függő entitásra vonatkozó házirendet, és módosítania kell a felhasználói utat, hogy JWT helyett SAML-állítást adjon ki.

### <a name="31-create-sign-up-or-sign-in-policy"></a>3,1 regisztrációs vagy bejelentkezési szabályzat létrehozása

1. Hozzon létre egy másolatot a *SignUpOrSignin.xml* fájlról a Starter Pack munkakönyvtárában, és mentse azt egy új néven. Például *SignUpOrSigninSAML.xml*. Ez a függő entitás házirend-fájlja.

1. Nyissa meg a *SignUpOrSigninSAML.xml* fájlt az előnyben részesített szerkesztőben.

1. Módosítsa az `PolicyId` és a `PublicPolicyUri` házirendet úgy, hogy _B2C_1A_signup_signin_saml_ és az `http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml` alább látható módon.

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. Közvetlenül az elem előtt adja hozzá a következő XML-kódrészletet `<RelyingParty>` . Ez az XML felülírja a _SignUpOrSignIn_ felhasználói út 7-es számú lépését. Ha az alapszintű csomag egy másik mappájából indult, vagy az előkészítési lépések hozzáadásával vagy eltávolításával testreszabta a felhasználói utat, akkor győződjön meg arról, hogy a `order` token kiállítói lépésnél megadott szám (az elemben) a felhasználó által megadott értékre van-e igazítva (például a másik alapszintű csomag mappáiban `LocalAccounts` , a 6 `SocialAccounts` . és a 9-es értéknél `SocialAndLocalAccountsWithMfa` ).

    ```xml
    <UserJourneys>
      <UserJourney Id="SignUpOrSignIn">
        <OrchestrationSteps>
          <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
        </OrchestrationSteps>
      </UserJourney>
    </UserJourneys>
    ```

1. Cserélje le az `<TechnicalProfile>` elem teljes elemét a `<RelyingParty>` következő technikai profil XML-kódjára.

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

1. Frissítse a `tenant-name` Azure ad B2C bérlő nevét.

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
> Más típusú felhasználói folyamatok (például a bejelentkezés, a jelszó-visszaállítás vagy a profil szerkesztése) megvalósításakor a folyamat lényegében ugyanaz, mint az ebben a részben leírtak szerint. A fenti 4. lépésben a felhasználói utazás utolsó lépését fogja módosítani a verzióról a verzióra `JWTIssuer` `Saml2AssertionIssuer` . A fenti 6. lépésben pedig a függő entitás szakaszban a **protokollt** a verzióról a verzióra módosítja `OpenIdConnect` `SAML2` .

### <a name="32-upload-and-test-your-policy-metadata"></a>3,2 a szabályzat metaadatainak feltöltése és tesztelése

Mentse a módosításokat, és töltse fel az új házirend-fájlt. Miután feltöltötte mindkét szabályzatot (a kiterjesztést és a függő entitás fájljait), nyisson meg egy webböngészőt, és navigáljon a szabályzat metaadataihoz.

Azure AD B2C Policy IDENTITÁSSZOLGÁLTATÓ metaadatok az SAML-protokollban használt információk, amelyek lehetővé teszik az SAML-identitások szolgáltatójának konfigurálását. A metaadatok határozzák meg a szolgáltatások helyét, például a bejelentkezést és a kijelentkezést, a tanúsítványokat, a bejelentkezési metódusokat és egyebeket. A Azure AD B2C szabályzat metaadatai a következő URL-címen érhetők el. Cserélje le a helyére `tenant-name` a Azure ad B2C bérlő nevét, és a `policy-name` szabályzat nevét (azonosítóját), például:.../B2C_1A_signup_signin_saml/samlp/metadata:

`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`

Most már készen áll az egyéni házirend-és Azure AD B2C-bérlőre. Ezután hozzon létre egy alkalmazás-regisztrációt Azure AD B2C.

## <a name="4-setup-application-in-the-azure-ad-b2c-directory"></a>4. telepítő alkalmazás a Azure AD B2C könyvtárban

### <a name="41-register-your-application-in-azure-ad-b2c"></a>4,1 az alkalmazás regisztrálása Azure AD B2C

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C** lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C** lehetőséget.
1. Válassza a **Alkalmazásregisztrációk** lehetőséget, majd válassza az **új regisztráció** lehetőséget.
1. Adja meg az alkalmazás **nevét** . Például: *SAMLApp1*.
1. A **támogatott fióktípus** területen válassza az **ebben a szervezeti könyvtárban lévő fiókok** lehetőséget
1. Az **átirányítási URI** területen válassza a Web lehetőséget, majd írja be a **következőt**: `https://localhost` . Ezt az értéket később módosíthatja az alkalmazás regisztrációs jegyzékfájljában.
1. Válassza a **Regisztráció** lehetőséget.

### <a name="42-update-the-app-manifest"></a>4,2 az alkalmazás jegyzékfájljának frissítése

Az SAML-alkalmazások esetében több tulajdonságot kell konfigurálni az alkalmazás regisztrációs jegyzékfájljában.

1. A [Azure Portal](https://portal.azure.com)navigáljon az előző szakaszban létrehozott alkalmazás-regisztrációhoz.
1. A **kezelés** területen válassza a **jegyzékfájl** elemet a jegyzékfájl-szerkesztő megnyitásához. A következő fejezetekben számos tulajdonságot kell módosítania.

#### <a name="identifieruris"></a>identifierUris

A `identifierUris` egy olyan, felhasználó által definiált URI-t tartalmazó karakterlánc-gyűjtemény, amely egyedileg azonosít egy webalkalmazást a Azure ad B2C bérlőn belül. Az URI azonosítónak meg kell egyeznie az SAML-kérelem `Issuer` nevével. A felhasználó által definiált URI-azonosító általában a szolgáltatói metaadatokkal megegyező érték `entityID` .

#### <a name="samlmetadataurl"></a>samlMetadataUrl

Ez a tulajdonság a szolgáltató nyilvánosan elérhető metaadat-URL-címét jelöli. A metaadatok URL-címe bármely névtelenül elérhető végpontra feltöltött metaadat-fájlra mutathat, például a blob Storage-hoz.

A metaadatok az SAML-protokollban használt információk egy SAML-fél, például egy szolgáltató konfigurációjának elérhetővé tétele érdekében. A metaadatok határozzák meg a szolgáltatások helyét, például a bejelentkezést és a kijelentkezést, a tanúsítványokat, a bejelentkezési metódusokat és egyebeket. Azure AD B2C beolvassa a szolgáltatói metaadatokat, és ennek megfelelően működik. A metaadatok nem szükségesek. Megadhat néhány attribútumot is, például a válasz URI-t és a kijelentkezési URI-t közvetlenül az alkalmazás jegyzékfájljában.

Ha az SAML-metaadatok URL-címében és az alkalmazás regisztrációs jegyzékfájljában *is vannak* megadva tulajdonságok, a rendszer **egyesíti** őket. A metaadatok URL-címében megadott tulajdonságokat a rendszer először dolgozza fel, és elsőbbséget élvez.

Ebben az oktatóanyagban, amely az SAML-teszt alkalmazást használja, a következő értéket használja `samlMetadataUrl` :

```json
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="replyurlswithtype-optional"></a>replyUrlsWithType (nem kötelező)

Ha nem ad meg metaadat-URI-t, explicit módon megadhatja a válasz URL-címét. Ez a választható tulajdonság a `AssertionConsumerServiceUrl` (z) (a `SingleSignOnService` szolgáltatói metaadatokban szereplő URL-cím) és a `BindingType` feltételezett érték `HTTP POST` .

Ha a válasz URL-címét és a kijelentkezési URL-címet a szolgáltatói metaadatok használata nélkül konfigurálja az alkalmazás jegyzékfájljában, akkor a Azure AD B2C nem ellenőrzi az SAML-kérelem aláírását, és nem titkosítja az SAML-választ.

Ebben az oktatóanyagban, amelyben az SAML-teszt alkalmazást használja, állítsa a `url` tulajdonságot a `replyUrlsWithType` következő JSON-kódrészletben látható értékre.

```json
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="logouturl-optional"></a>logoutUrl (nem kötelező)

Ez a választható tulajdonság az `Logout` URL-címet ( `SingleLogoutService` a függő entitások metaadataiban lévő URL-címet) jelöli, és a ehhez `BindingType` szükséges `Http-Redirect` .

Ebben az oktatóanyagban, amely az SAML-teszt alkalmazást használja, hagyja a következőt `logoutUrl` `https://samltestapp2.azurewebsites.net/logout` :

```json
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

## <a name="5-update-your-application-code"></a>5. az alkalmazás kódjának frissítése

Az utolsó lépés a Azure AD B2C engedélyezése SAML-identitásszolgáltató az SAML függő entitás alkalmazásában. Minden alkalmazás eltérő, és ennek lépései eltérőek. Részletekért olvassa el az alkalmazás dokumentációját.

A metaadatokat "statikus metaadatok" vagy "dinamikus metaadatok"ként lehet konfigurálni a szolgáltatóban. Statikus módban a metaadatok teljes vagy egy részét a Azure AD B2C házirend metaadataiból másolhatja. Dinamikus módban beállíthatja a metaadatok URL-címét, és lehetővé teheti, hogy az alkalmazás dinamikusan beolvassa a metaadatokat.

Általában a következők szükségesek:

* **Metaadatok**: `https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`
* **Kiállító**: az SAML-kérelem `issuer` értékének meg kell egyeznie az `identifierUris` alkalmazás regisztrációs jegyzékfájljának elemében konfigurált URI-k egyikével. Ha az SAML `issuer` -kérelem neve nem létezik az `identifierUris` elemben, [vegye fel azt az alkalmazás regisztrációs jegyzékfájlba](#identifieruris). Például: `https://contoso.onmicrosoft.com/app-name`. 
* **Bejelentkezési URL-cím/SAML-végpont/SAML URL-cím**: az `<SingleSignOnService>` XML-elem Azure ad B2C SAML-házirend metaadatainak fájljában található érték bejelölése
* **Tanúsítvány**: ez *B2C_1A_SamlIdpCert*, de a titkos kulcs nélkül. A tanúsítvány nyilvános kulcsának beszerzése:

    1. Nyissa meg a fent megadott metaadat-URL-címet.
    1. Másolja a `<X509Certificate>` elemben található értéket.
    1. Illessze be egy szövegfájlba.
    1. Mentse a szövegfájlt *. cer* fájlként.

### <a name="51-test-with-the-saml-test-app-optional"></a>5,1 tesztelés az SAML-teszt alkalmazással (opcionális)

Az oktatóanyag elvégzéséhez az [SAML-teszt alkalmazás][samltest]használatával:

* A bérlő nevének frissítése
* Frissítse a házirend nevét, például *B2C_1A_signup_signin_saml*
* Adja meg ezt a kiállítói URI-t. Használja az alkalmazás regisztrációs jegyzékfájljában található elem egyik URI-azonosítóját `identifierUris` , például: `https://contoso.onmicrosoft.com/app-name` .

Válassza a **Bejelentkezés** lehetőséget, és a felhasználói bejelentkezési képernyőn kell megjelennie. Bejelentkezéskor az SAML-jogcímet vissza kell adni a minta alkalmazásnak.

## <a name="enable-encrypted-assertions-optional"></a>Titkosított kijelentések engedélyezése (nem kötelező)

A szolgáltatónak visszaadott SAML-kijelentések titkosításához Azure AD B2C a szolgáltató nyilvánoskulcs-tanúsítványát fogja használni. A nyilvános kulcsnak léteznie kell a fenti ["samlMetadataUrl"](#samlmetadataurl) elemben leírt SAML-metaadatokban a "Encryption" használatával.

A következő XML-kód egy példa arra, hogy az SAML metaadat-leírót használja a titkosításra beállított használatával:

```xml
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

Ha engedélyezni szeretné a Azure AD B2C számára a titkosított kijelentések küldését, állítsa a **WantsEncryptedAssertion** metaadat-elemét a `true` [függő entitás technikai profiljába](relyingparty.md#technicalprofile). Beállíthatja az SAML-állítás titkosításához használt algoritmust is. További információ: [függő entitások technikai profiljának metaadatai](relyingparty.md#metadata). 

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="enable-identity-provider-initiated-flow-optional"></a>Identitás-szolgáltató által kezdeményezett folyamat engedélyezése (nem kötelező)

Az identitás-szolgáltató által kezdeményezett folyamat során a bejelentkezési folyamatot az Identitáskezelő (Azure AD B2C) kezdeményezi, amely egy kéretlen SAML-választ küld a szolgáltatónak (a függő entitás alkalmazásának). Jelenleg nem támogatunk olyan forgatókönyveket, amelyekben a kezdeményező személyazonosság szolgáltatója külső identitás-szolgáltató, például [AD-FS](identity-provider-adfs.md)vagy [Salesforce](identity-provider-salesforce-saml.md).

Az Identitáskezelő (Azure AD B2C) által kezdeményezett folyamat engedélyezéséhez állítsa a **IdpInitiatedProfileEnabled** metaadat-elemét a `true` [függő entitás technikai profiljába](relyingparty.md#technicalprofile).

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="IdpInitiatedProfileEnabled">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

A következő URL-cím használatával jelentkezhet be vagy regisztrálhat egy felhasználót a személyazonosság-szolgáltató által kezdeményezett folyamaton keresztül:

```
https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/generic/login?EntityId=app-identifier-uri 
```

Cserélje le a következő értékeket:

* **bérlő** neve a bérlő nevével
* a **Házirend neve** az SAML függő entitás házirendjének nevével
* az **app-Identifier-URI** a `identifierUris` metaadat-fájllal, például:`https://contoso.onmicrosoft.com/app-name`
## <a name="sample-policy"></a>Minta szabályzat

Egy teljes minta szabályzatot biztosítunk, amely az SAML-teszt alkalmazással való teszteléshez használható.

1. Az [SAML-SP által kezdeményezett bejelentkezési minta házirend](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated) letöltése
1. Frissítsen a `TenantId` bérlő nevére, például *contoso.b2clogin.com*
1. A szabályzat nevének megtartása *B2C_1A_signup_signin_saml*

## <a name="supported-and-unsupported-saml-modalities"></a>Támogatott és nem támogatott SAML-módozatok

A saját metaadat-végponton keresztül a következő, SAML-függő entitások (RP) támogatottak:

* Több kijelentkezési URL-cím vagy POST-kötés a kijelentkezési URL-címhez az Application/Service Principal objektumban.
* Az alkalmazás/szolgáltatás egyszerű objektumában az RP-kérelmek ellenőrzéséhez írja be az aláíró kulcsot.
* A jogkivonat-titkosítási kulcs megadásához az Application/Service Principal objektumban.
* Az identitás-szolgáltató kezdeményezte a bejelentkezést, ahol az Identity Provider Azure AD B2C.

## <a name="saml-token"></a>SAML-jogkivonat

Az SAML-token olyan biztonsági jogkivonat, amelyet a sikeres bejelentkezés után Azure AD B2C állít ki. A felhasználóval, a szolgáltatóval kapcsolatos információkat tartalmaz, amelyek esetében a jogkivonat célja, aláírása és érvényességi ideje. A következő táblázat felsorolja azokat a jogcímeket és tulajdonságokat, amelyeket Azure AD B2C által kiállított SAML-jogkivonatban várhat.

|Elem  |Tulajdonság  |Jegyzetek  |
|---------|---------|---------|
|`<Response>`| `ID` | A válasz automatikusan generált egyedi azonosítója. | 
|`<Response>`| `InResponseTo` | Annak az SAML-kérésnek az azonosítója, amelyre ez az üzenet válaszol. | 
|`<Response>` | `IssueInstant` | A válasz kibocsátásának pillanata. Az időérték az UTC szerint van kódolva.  Ha módosítani szeretné a jogkivonat élettartamának beállításait, állítsa be az `TokenNotBeforeSkewInSeconds` SAML-jogkivonat kiállítói technikai profiljának [metaadatait](saml-issuer-technical-profile.md#metadata) . | 
|`<Response>` | `Destination`| URI-hivatkozás, amely arra a címre utal, amelyre a válasz elküldése megtörtént. Az érték megegyezik az SAML-kérelemmel `AssertionConsumerServiceURL` . | 
|`<Response>` `<Issuer>` | |Azonosítja a jogkivonat kiállítóját. Ez egy tetszőleges URI, amelyet az SAML-jogkivonat probléma metaadatai határoznak meg. `IssuerUri` [](saml-issuer-technical-profile.md#metadata)     |
|`<Response>` `<Assertion>` `<Subject>` `<NameID>`     |         |Az a rendszerbiztonsági tag, amelyről a jogkivonat adatokat érvényesít, például a felhasználói objektum AZONOSÍTÓját. Ez az érték nem módosítható, és nem rendelhető hozzá újra, és nem használható újra. Az engedélyezési ellenőrzések biztonságos elvégzésére használható, például ha a jogkivonat egy erőforrás elérésére szolgál. Alapértelmezés szerint a tulajdonos jogcímet a rendszer a címtárban lévő felhasználó objektumazonosító alapján tölti fel.|
|`<Response>` `<Assertion>` `<Subject>` `<NameID>`     | `Format` | A karakterlánc-alapú azonosító adatainak besorolását jelölő URI-hivatkozás. Alapértelmezés szerint ez a tulajdonság nincs megadva. A függő entitás [SubjectNamingInfo](relyingparty.md#subjectnaminginfo) beállíthatja a formátum megadását `NameID` , például: `urn:oasis:names:tc:SAML:2.0:nameid-format:transient` . |
|`<Response>` `<Assertion>` `<Subject>` `<Conditions>` |`NotBefore` |Az az időpont, amikor a jogkivonat érvényes lesz. Az időérték az UTC szerint van kódolva. Az alkalmazásnak ezt a jogcímet kell használnia a jogkivonat élettartamának érvényességének ellenőrzéséhez. Ha módosítani szeretné a jogkivonat élettartamának beállításait, állítsa be az `TokenNotBeforeSkewInSeconds` SAML-jogkivonat kiadása technikai profil [metaadatait](saml-issuer-technical-profile.md#metadata) . |
|`<Response>` `<Assertion>` `<Subject>` `<Conditions>` | `NotOnOrAfter` | Az az idő, amikor a jogkivonat érvénytelenné válik. Az alkalmazásnak ezt a jogcímet kell használnia a jogkivonat élettartamának érvényességének ellenőrzéséhez. Az alapértelmezett érték 5 perccel a és a frissítése után, az `NotBefore` `TokenLifeTimeInSeconds` SAML-jogkivonat kiadása technikai profil [metaadatainak](saml-issuer-technical-profile.md#metadata) hozzáadásával.|
|`<Response>` `<Assertion>` `<Conditions>` `<AudienceRestriction>` `<Audience>` | |Egy célközönséget azonosító URI-hivatkozás. Azonosítja a jogkivonat kívánt címzettjét. Az érték megegyezik az SAML-kérelemmel `AssertionConsumerServiceURL` .|
|`<Response>``<Assertion>` `<AttributeStatement>` gyűjtemény`<Attribute>` | | Kijelentések gyűjteménye (jogcímek), a [függő entitások technikai profiljának](relyingparty.md#technicalprofile) kimeneti jogcímeiben konfiguráltak szerint. Az állítás nevét a kimeneti jogcím beállításával állíthatja be `PartnerClaimType` . |

## <a name="next-steps"></a>Következő lépések

- További információt az [SAML-protokollról az Oasis webhelyén](https://www.oasis-open.org/)talál.
- Szerezze be az SAML-teszt webalkalmazást [Azure ad B2C GitHub közösségi](https://github.com/azure-ad-b2c/saml-sp-tester)adattárból.

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp
