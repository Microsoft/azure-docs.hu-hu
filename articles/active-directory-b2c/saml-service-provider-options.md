---
title: Az SAML-szolgáltató beállításainak konfigurálása
title-suffix: Azure Active Directory B2C
description: Azure Active Directory B2C SAML-szolgáltató beállításainak konfigurálása
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
ms.openlocfilehash: fea39388b6b4387dfc4fe95d1cdfb3e523a8089c
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382436"
---
# <a name="options-for-registering-a-saml-application-in-azure-ad-b2c"></a>Az SAML-alkalmazások Azure AD B2C-ben való regisztrálásának lehetőségei

Ez a cikk a Azure Active Directory (Azure AD B2C) SAML-alkalmazáshoz való csatlakoztatásakor elérhető konfigurációs beállításokat ismerteti.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="saml-response-signature"></a>SAML-válasz aláírása

Megadhat egy tanúsítványt, amelyet az SAML-üzenetek aláírásához kíván használni. Az üzenet az `<samlp:Response>` alkalmazásnak küldött SAML-válasz eleme.

Ha még nem rendelkezik házirend-kulccsal, [hozzon létre egyet](saml-service-provider.md#create-a-policy-key). Ezután konfigurálja a `SamlMessageSigning` metaadat-elemet az SAML-jogkivonat kiállítói technikai profiljában. A `StorageReferenceId` névnek hivatkoznia kell a szabályzat kulcsának nevére.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlMessageCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```

### <a name="saml-response-signature-algorithm"></a>SAML-válasz aláírási algoritmusa

Beállíthatja az SAML-állítás aláírásához használt aláírási algoritmust. A lehetséges értékek:,, `Sha256` `Sha384` `Sha512` vagy `Sha1` . Győződjön meg arról, hogy a technikai profil és az alkalmazás ugyanazt az aláírási algoritmust használja. Csak a tanúsítvány által támogatott algoritmust használja.

Konfigurálja az aláírási algoritmust a `XmlSignatureAlgorithm` függő entitás metaadatainak elemében található metaadatok használatával.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="XmlSignatureAlgorithm">Sha256</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="saml-assertions-signature"></a>SAML-kijelentések aláírása

Ha az alkalmazás az SAML-állítási szakaszt aláírja, győződjön meg arról, hogy az SAML-szolgáltató beállítja a `WantAssertionsSigned` -t `true` . Ha a értéke `false` vagy nem létezik, az állítás szakasz nem lesz aláírva. Az alábbi példa egy SAML szolgáltatói metaadatokat mutat be a következőre: `WantAssertionsSigned` `true` .

```xml
<EntityDescriptor ID="id123456789" entityID="https://samltestapp2.azurewebsites.net" validUntil="2099-12-31T23:59:59Z" xmlns="urn:oasis:names:tc:SAML:2.0:metadata">
  <SPSSODescriptor  WantAssertionsSigned="true" AuthnRequestsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  </SPSSODescriptor>
</EntityDescriptor>
```  

### <a name="saml-assertions-signature-certificate"></a>SAML-kijelentések aláírási tanúsítványa

A szabályzatnak meg kell adnia egy tanúsítványt, amelyet az SAML-válasz SAML-kijelentések szakaszának aláírásához kíván használni. Ha még nem rendelkezik házirend-kulccsal, [hozzon létre egyet](saml-service-provider.md#create-a-policy-key). Ezután konfigurálja a `SamlAssertionSigning` metaadat-elemet az SAML-jogkivonat kiállítói technikai profiljában. A `StorageReferenceId` névnek hivatkoznia kell a szabályzat kulcsának nevére.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlMessageCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```

## <a name="saml-assertions-encryption"></a>SAML-kijelentések titkosítása

Ha az alkalmazás az SAML-állításokat titkosított formátumban várja, győződjön meg arról, hogy a titkosítás engedélyezve van a Azure AD B2C szabályzatban.

A Azure AD B2C a szolgáltató nyilvánoskulcs-tanúsítványát használja az SAML-állítás titkosítására. A nyilvános kulcsnak léteznie kell az SAML-alkalmazás metaadatainak végpontjában a "use" beállítással a "Encryption" értékre, az alábbi példában látható módon:

```xml
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

Ha engedélyezni szeretné a Azure AD B2C számára a titkosított kijelentések küldését, állítsa a **WantsEncryptedAssertion** metaadat-elemét a `true` [függő entitás technikai profiljába](relyingparty.md#technicalprofile). Beállíthatja az SAML-állítás titkosításához használt algoritmust is.

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

### <a name="encryption-method"></a>Titkosítási módszer

Az SAML-kikötési adatok titkosításához használt titkosítási módszer konfigurálásához állítsa be a `DataEncryptionMethod` metaadat-kulcsot a függő entitáson belül. A lehetséges értékek a következők: `Aes256` (default), `Aes192` , `Sha512` , vagy `Aes128` . A metaadatok az `<EncryptedData>` SAML-válasz elemének értékét vezérlik.

A kulcs másolatának titkosításához használt titkosítási módszer konfigurálásához, amely az SAML-kikötések adatainak titkosítására szolgál, állítsa be a `KeyEncryptionMethod` metaadat-kulcsot a függő entitáson belül. A lehetséges értékek a következők: `Rsa15` (alapértelmezett) – RSA nyilvánoskulcs-titkosítási szabvány (PKCS) 1,5 algoritmus, és `RsaOaep` -RSA optimális aszimmetrikus titkosítási kitöltés (OAEP) titkosítási algoritmus.  A metaadatok az  `<EncryptedKey>` SAML-válasz elemének értékét vezérlik.

Az alábbi példa `EncryptedAssertion` egy SAML-állítás szakaszát mutatja be. A titkosított adatmódszer a `Aes128` , a titkosított kulcs módszere pedig `Rsa15` .

```xml
<saml:EncryptedAssertion>
  <xenc:EncryptedData xmlns:xenc="http://www.w3.org/2001/04/xmlenc#"
    xmlns:dsig="http://www.w3.org/2000/09/xmldsig#" Type="http://www.w3.org/2001/04/xmlenc#Element">
    <xenc:EncryptionMethod Algorithm="http://www.w3.org/2001/04/xmlenc#aes128-cbc" />
    <dsig:KeyInfo>
      <xenc:EncryptedKey>
        <xenc:EncryptionMethod Algorithm="http://www.w3.org/2001/04/xmlenc#rsa-1_5" />
        <xenc:CipherData>
          <xenc:CipherValue>...</xenc:CipherValue>
        </xenc:CipherData>
      </xenc:EncryptedKey>
    </dsig:KeyInfo>
    <xenc:CipherData>
      <xenc:CipherValue>...</xenc:CipherValue>
    </xenc:CipherData>
  </xenc:EncryptedData>
</saml:EncryptedAssertion>
```

Módosíthatja a titkosított állítások formátumát. A titkosítási formátum konfigurálásához állítsa a `UseDetachedKeys` metaadat-kulcsot a függő entitáson belül. Lehetséges értékek: `true` , vagy `false` (alapértelmezett). Ha a érték van beállítva `true` , a leválasztott kulcsok hozzáadja a titkosított kijelentést a ( `EncrytedAssertion` szemben) helyett `EncryptedData` .

Konfigurálja a titkosítási módszert és a formátumot, használja a metaadatok kulcsait a [függő entitások technikai profilján](relyingparty.md#technicalprofile)belül:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="DataEncryptionMethod">Aes128</Item>
      <Item Key="KeyEncryptionMethod">Rsa15</Item>
      <Item Key="UseDetachedKeys">false</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="identity-provider-initiated-flow"></a>Identitás-szolgáltató – kezdeményezett folyamat

Ha az alkalmazás egy SAML-AuthN kérésének első elküldése nélkül kéri az SAML-jogcímet, konfigurálnia kell Azure AD B2C a személyazonosság-szolgáltató által kezdeményezett folyamathoz.

A személyazonosság-szolgáltató által kezdeményezett folyamat során a bejelentkezési folyamatot az Identitáskezelő (Azure AD B2C) kezdeményezi, amely egy kéretlen SAML-választ küld a szolgáltatónak (a függő entitás alkalmazásának).

Jelenleg nem támogatunk olyan forgatókönyveket, amelyekben a kezdeményező identitás szolgáltatója egy Azure AD B2C összevont külső identitás-szolgáltató, például [AD-FS](identity-provider-adfs.md)vagy [Salesforce](identity-provider-salesforce-saml.md). Csak Azure AD B2C helyi fiók hitelesítése esetén támogatott.

Az identitás-szolgáltató által kezdeményezett folyamat engedélyezéséhez állítsa a **IdpInitiatedProfileEnabled** metaadat-elemet a `true` [függő entitás technikai profiljába](relyingparty.md#technicalprofile).

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

Ha be szeretne jelentkezni, vagy regisztrál egy felhasználót a személyazonosság-szolgáltató által kezdeményezett folyamaton keresztül, használja a következő URL-címet:

```
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/generic/login?EntityId=app-identifier-uri 
```

Cserélje le a következő értékeket:

* **bérlő** neve a bérlő nevével
* a **Házirend neve** az SAML függő entitás házirendjének nevével
* az **app-Identifier-URI** a `identifierUris` metaadat-fájllal, például:`https://contoso.onmicrosoft.com/app-name`

### <a name="sample-policy"></a>Minta szabályzat

Egy teljes minta szabályzatot biztosítunk, amely az SAML-teszt alkalmazással való teszteléshez használható.

1. Töltse le az [SAML-SP által kezdeményezett bejelentkezési minta házirendet](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated).
1. Frissítsen a `TenantId` bérlő nevére, például *contoso.b2clogin.com*.
1. Tartsa meg a szabályzat nevét *B2C_1A_signup_signin_saml*.

## <a name="saml-response-lifetime"></a>SAML-válasz élettartama

Beállíthatja azt az időtartamot, ameddig az SAML-válasz érvényes marad. Állítsa be az élettartamot az `TokenLifeTimeInSeconds` SAML-jogkivonat kiállítói technikai profiljában található metaadatok elem használatával. Ez az érték azon másodpercek száma, amelyek eltérhetnek a `NotBefore` jogkivonat-kiállítási idő alapján kiszámított időbélyegtől. Az alapértelmezett élettartam 300 másodperc (5 perc).

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="TokenLifeTimeInSeconds">400</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="saml-response-valid-from-skew"></a>SAML-válasz érvényes a döntéstől

Beállíthatja az SAML-válasz időbélyegzőre alkalmazott időeltérést `NotBefore` . Ez a konfiguráció biztosítja, hogy ha a két platform közötti időpontok nincsenek szinkronban, akkor az SAML-jogcímek továbbra is érvényesek lesznek, ha ebben az időszakban az adott időkereten belül

Állítsa be az időeltérést az `TokenNotBeforeSkewInSeconds` SAML-jogkivonat kiállítói technikai profiljában található metaadat-elem használatával. A ferdeség értéke másodpercben megadva, alapértelmezett értéke pedig 0. A maximális érték 3600 (egy óra).

Például ha a `TokenNotBeforeSkewInSeconds` értéke másodpercre van állítva `120` :

- A jogkivonatot a 13:05:10 UTC adja ki
- A jogkivonat 13:03:10 UTC alapján érvényes

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="TokenNotBeforeSkewInSeconds">120</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="remove-milliseconds-from-date-and-time"></a>Ezredmásodpercek eltávolítása a dátumtól és az időponttól

Megadhatja, hogy az ezredmásodpercek el legyenek-e távolítva az SAML-válasz datetime értékeiből (ezek közé tartozik az IssueInstant, a NotBefore, a NotOnOrAfter és a AuthnInstant). Az ezredmásodpercek eltávolításához állítsa a `RemoveMillisecondsFromDateTime
` metaadat-kulcsot a függő entitáson belül. Lehetséges értékek: `false` (alapértelmezett) vagy `true` .

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="RemoveMillisecondsFromDateTime">true</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="azure-ad-b2c-issuer-id"></a>Azure AD B2C kiállító azonosítója

Ha több SAML-alkalmazással is rendelkezik, amelyek eltérő `entityID` értéktől függenek, felülbírálhatja a `issueruri` függő entitás fájljában lévő értéket. A kiállító URI-azonosító felülbírálásához másolja a "Saml2AssertionIssuer" azonosítót az alapfájlból, és bírálja felül az `issueruri` értéket.

> [!TIP]
> Másolja a `<ClaimsProviders>` szakaszt az Alapból, és őrizze meg ezeket az elemeket a jogcímek szolgáltatóján belül: `<DisplayName>Token Issuer</DisplayName>` , `<TechnicalProfile Id="Saml2AssertionIssuer">` és `<DisplayName>Token Issuer</DisplayName>` .
 
Példa:

```xml
   <ClaimsProviders>   
    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Saml2AssertionIssuer">
          <DisplayName>Token Issuer</DisplayName>
          <Metadata>
            <Item Key="IssuerUri">customURI</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
  </ClaimsProviders>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpInSAML" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2" />
      <Metadata>
     …
```

## <a name="session-management"></a>Munkamenet-kezelés

A munkamenetet kezelheti Azure AD B2C és az SAML függő entitás alkalmazás között az `UseTechnicalProfileForSessionManagement` elem és a [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider)használatával.

## <a name="force-users-to-reauthenticate"></a>A felhasználók újrahitelesítésének kényszerítése 

A felhasználók újrahitelesítésének kényszerítéséhez az alkalmazás az `ForceAuthn` SAML hitelesítési kérelemben szereplő attribútumot is tartalmazhatja. Az `ForceAuthn` attribútum logikai érték. Ha igaz értékre van állítva, a rendszer a felhasználói munkamenetet érvényteleníti Azure AD B2Ckor, és a felhasználónak újra hitelesítenie kell magát. A következő SAML-hitelesítési kérelem bemutatja, hogyan állíthatja igaz értékre az `ForceAuthn` attribútumot. 


```xml
<samlp:AuthnRequest 
       Destination="https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1A_SAML2_signup_signin/samlp/sso/login"
       ForceAuthn="true" ...>
    ...
</samlp:AuthnRequest>
```

## <a name="sign-the-azure-ad-b2c-idp-saml-metadata"></a>A Azure AD B2C identitásszolgáltató SAML-metaadatok aláírása

Ha az alkalmazás megköveteli, utasíthatja Azure AD B2C az SAML-identitásszolgáltató metaadat-dokumentum aláírására. Ha még nem rendelkezik házirend-kulccsal, [hozzon létre egyet](saml-service-provider.md#create-a-policy-key). Ezután konfigurálja a `MetadataSigning` metaadat-elemet az SAML-jogkivonat kiállítói technikai profiljában. A `StorageReferenceId` névnek hivatkoznia kell a szabályzat kulcsának nevére.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlMetadataCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```

## <a name="debug-the-saml-protocol"></a>Az SAML protokoll hibakeresése

A szolgáltatóval való integráció konfigurálásához és hibakereséséhez használhat egy böngésző-bővítményt az SAML protokollhoz, például az [SAML devtools bővítményt](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio) a Chrome, az [SAML-Tracer](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) for Firefox, vagy a [Edge vagy az IE fejlesztői eszközökhöz](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957).

Ezen eszközök használatával megtekintheti az alkalmazás és a Azure AD B2C közötti integrációt. Például:

* Ellenőrizze, hogy az SAML-kérelem tartalmaz-e aláírást, és határozza meg, hogy melyik algoritmust használja a rendszer az engedélyezési kérelemben való bejelentkezéshez.
* Ellenőrizze, hogy a Azure AD B2C hibaüzenetet ad-e vissza.
* Győződjön meg róla, hogy az érvényesítés szakasz titkosított.

## <a name="next-steps"></a>Következő lépések

- További információ az SAML- [protokollról az Oasis webhelyén](https://www.oasis-open.org/).
- Szerezze be az SAML-teszt webalkalmazást a [Azure ad B2C GitHub Community](https://github.com/azure-ad-b2c/saml-sp-tester)adattárból.

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

::: zone-end
