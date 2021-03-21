---
title: SAML-technikai profil definiálása egyéni házirendben
titleSuffix: Azure AD B2C
description: Hozzon létre egy SAML-technikai profilt egy egyéni szabályzatban Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/01/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2f16de49518e334f2f5e679ce24e24a262a1e231
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98674943"
---
# <a name="define-a-saml-identity-provider-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>SAML-identitás szolgáltatójának műszaki profiljának meghatározása egy Azure Active Directory B2C egyéni házirendben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A Azure Active Directory B2C (Azure AD B2C) támogatást nyújt az SAML 2,0 identitás-szolgáltatóhoz. Ez a cikk a szabványos protokollt támogató jogcím-szolgáltatóval való interakcióra szolgáló technikai profil sajátosságait ismerteti. SAML-alapú műszaki profillal összevonása az SAML-alapú identitás-szolgáltatóval, például az [ADFS](./identity-provider-adfs.md) -vel és a [Salesforce](identity-provider-salesforce-saml.md)-mel. Ez az összevonás lehetővé teszi, hogy a felhasználók bejelentkezzenek a meglévő közösségi vagy vállalati identitásokkal.

## <a name="metadata-exchange"></a>Metaadatok cseréje

A metaadatok az SAML-protokollban használt információk az SAML-felek, például a szolgáltatók vagy az Identitáskezelő konfigurációjának elérhetővé tétele érdekében. A metaadatok határozzák meg a szolgáltatások helyét, például a bejelentkezést és a kijelentkezést, a tanúsítványokat, a bejelentkezési metódusokat és egyebeket. Az identitás-szolgáltató a metaadatok használatával tudja, hogyan kommunikálhat a Azure AD B2Cokkal. A metaadatok XML-formátumban vannak konfigurálva, és digitális aláírással is aláírva, így a másik fél ellenőrizheti a metaadatok integritását. Ha egy SAML-összevonja Azure AD B2C, az SAML-kérelmet kezdeményező szolgáltatóként funkcionál, és egy SAML-válaszra vár. És bizonyos esetekben elfogadja a nem kért SAML-hitelesítést, amely más néven az identitás-szolgáltató kezdeményezett.

A metaadatok mindkét fél esetében a "statikus metaadatok" vagy a "dinamikus metaadatok" is konfigurálhatók. Statikus módban másolja a teljes metaadatokat az egyik féltől, és állítsa be a másik fél számára. Dinamikus módban beállíthatja a metaadatok URL-címét, míg a másik fél dinamikusan beolvassa a konfigurációt. Az alapelvek megegyeznek a Azure AD B2C technikai profil metaadatainak megadásával az identitás-szolgáltatóban, és az identitás-szolgáltató metaadatait Azure AD B2C.

Mindegyik SAML-identitás szolgáltatójának különböző lépései vannak a szolgáltató kifejtéséhez és beállításához, ebben az esetben Azure AD B2C, majd a Azure AD B2C metaadatokat a személyazonosság-szolgáltatóban kell megadni. Tekintse meg az Identity Provider dokumentációját, amely útmutatást nyújt ennek elvégzéséhez.

Az alábbi példa egy Azure AD B2C technikai profil SAML-metaadatainak URL-címét jeleníti meg:

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

Cserélje le a következő értékeket:

- **saját** bérlő neve a bérlő nevével, például fabrikam.b2clogin.com.
- **az Ön** házirendje a szabályzat nevével. Használja azt a szabályzatot, amelyben az SAML-szolgáltató műszaki profilját konfigurálja, vagy egy olyan házirendet, amely az adott szabályzattól örököl.
- **az Ön-technikai profil** a SAML-identitás szolgáltatójának technikai profiljának nevével.

## <a name="digital-signing-certificates-exchange"></a>Digitális aláírási tanúsítványok cseréje

A Azure AD B2C és az SAML-identitás szolgáltatója közötti megbízhatósági kapcsolat létrehozásához meg kell adnia egy érvényes X509-tanúsítványt a titkos kulccsal. Feltölti a tanúsítványt a titkos kulccsal (. pfx fájllal) a Azure AD B2C Policy Key Store-ba. Azure AD B2C digitálisan aláírja az SAML bejelentkezési kérést az Ön által megadott tanúsítvány használatával.

A tanúsítvány a következő módokon használható:

- Azure AD B2C létrehoz és aláír egy SAML-kérelmet a tanúsítvány Azure AD B2C titkos kulcsának használatával. Az SAML-kérelmet a rendszer elküldi az identitás-szolgáltatónak, amely a tanúsítvány Azure AD B2C nyilvános kulcsával érvényesíti a kérelmet. A Azure AD B2C nyilvános tanúsítvány a technikai profil metaadatain keresztül érhető el. Azt is megteheti, hogy manuálisan feltölti a. cer fájlt az SAML-identitás szolgáltatójának.
- Az identitás-szolgáltató aláírja Azure AD B2C elküldte a tanúsítványt az Identity Provider titkos kulcsával. Azure AD B2C érvényesíti az adatforrást az identitás-szolgáltató nyilvános tanúsítványának használatával. Az egyes identitás-szolgáltatók különböző lépéseket tesznek a telepítéshez, és útmutatásért tekintse meg az Identity Provider dokumentációját. Azure AD B2C a házirendnek a tanúsítvány nyilvános kulcsát kell használnia az Identity Provider metaadataival.

Az önaláírt tanúsítványok a legtöbb esetben elfogadhatók. Éles környezetekben ajánlott a hitelesítésszolgáltató által kiadott X509-tanúsítványt használni. A dokumentum későbbi részében leírtaknak megfelelően a nem éles környezetekben mindkét oldalon le lehet tiltani az SAML-aláírást.

Az alábbi ábrán a metaadatok és a tanúsítványok cseréje látható:

![metaadatok és tanúsítványok cseréje](media/saml-technical-profile/technical-profile-idp-saml-metadata.png)

## <a name="digital-encryption"></a>Digitális titkosítás

Az SAML-válasz kiállításának titkosításához az identitás-szolgáltató mindig egy Azure AD B2C technikai profilban lévő titkosítási tanúsítvány nyilvános kulcsát használja. Amikor Azure AD B2Cnak vissza kell fejtenie az adattitkosítást, a titkosítási tanúsítvány privát részét használja.

Az SAML-válasz kijelentésének titkosítása:

1. Töltsön fel egy érvényes X509-tanúsítványt a titkos kulccsal (. pfx fájl) a Azure AD B2C Policy Key Store-ba.
2. Vegyen fel egy **CryptographicKey** elemet a `SamlAssertionDecryption` technikai profil **CryptographicKeys** gyűjteményéhez tartozó azonosítóval. Állítsa a **StorageReferenceId** az 1. lépésben létrehozott házirend-kulcs nevére.
3. Állítsa be a technikai profil metaadatainak **WantsEncryptedAssertions** a következőre: `true` .
4. Frissítse az identitás-szolgáltatót az új Azure AD B2C technikai profil metaadataival. Látnia kell a **leírót** a **use** tulajdonsággal, amely `encryption` a tanúsítvány nyilvános kulcsát tartalmazza.

Az alábbi példa a titkosításhoz használt SAML-metaadatok kulcs leíró szakaszát mutatja be:

```xml
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

## <a name="protocol"></a>Protokoll

A protokoll elem **Name** attribútumát be kell állítani `SAML2` .

## <a name="input-claims"></a>Bemeneti jogcímek

A **szabályzattípushoz** elem az SAML-AuthN kérelem **tárgyában** lévő **NameId** küldésére szolgál. Ennek eléréséhez adjon hozzá egy **PartnerClaimType** -készlettel rendelkező bemeneti jogcímet az `subject` alább látható módon.

```xml
<InputClaims>
    <InputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="subject" />
</InputClaims>
```

## <a name="output-claims"></a>Kimeneti jogcímek

A **OutputClaims** elem tartalmazza a SAML-identitás szolgáltatója által visszaadott jogcímek listáját a `AttributeStatement` szakasz alatt. Előfordulhat, hogy le kell képeznie a szabályzatban definiált jogcím nevét az identitás-szolgáltatóban definiált névre. Olyan jogcímeket is megadhat, amelyeket nem ad vissza az identitás-szolgáltató, ha beállítja az `DefaultValue` attribútumot.

### <a name="subject-name-output-claim"></a>Tulajdonos neve kimeneti jogcím

Ha **az SAML** -állítást normalizált jogcímként **NameId** szeretné olvasni, állítsa a jogcím **PartnerClaimType** az attribútum értékére `SPNameQualifier` . Ha az `SPNameQualifier` attribútum nem jelenik meg, állítsa a jogcím **PartnerClaimType** értéket az attribútum értékére `NameQualifier` . 


SAML-állítás: 

```xml
<saml:Subject>
  <saml:NameID SPNameQualifier="http://your-idp.com/unique-identifier" Format="urn:oasis:names:tc:SAML:2.0:nameid-format:transient">david@contoso.com</saml:NameID>
  <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
    <SubjectConfirmationData InResponseTo="_cd37c3f2-6875-4308-a9db-ce2cf187f4d1" NotOnOrAfter="2020-02-15T16:23:23.137Z" Recipient="https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" />
    </SubjectConfirmation>
  </saml:SubjectConfirmation>
</saml:Subject>
```

Kimeneti jogcím:

```xml
<OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="http://your-idp.com/unique-identifier" />
```

Ha mindkettő `SPNameQualifier` vagy `NameQualifier` attribútumok nem jelennek meg az SAML-állításban, állítsa be a jogcím **PartnerClaimType** `assertionSubjectName` . Győződjön meg arról, hogy a **NameId** az érvényesítési XML első értéke. Ha egynél több kijelentést határoz meg, Azure AD B2C kiválasztja a tárgy értékét az utolsó állításból.

Az alábbi példa egy SAML-identitás szolgáltató által visszaadott jogcímeket mutatja be:

- A **issuerUserId** jogcím a **assertionSubjectName** jogcímhez van rendelve.
- Az **first_name** jogcím a **givenName** jogcímhez van rendelve.
- A **last_name** jogcím a **vezetéknév** jogcímere van leképezve.
- A **DisplayName** jogcím a **Name** jogcímre van leképezve.
- A név leképezése nélküli **e-mail-** jogcím.

A technikai profil az Identitáskezelő által nem visszaadott jogcímeket is visszaadja:

- Az **identityProvider** -jogcím, amely tartalmazza az identitás-szolgáltató nevét.
- A **authenticationSource** jogcím alapértelmezett **socialIdpAuthentication**-értékkel rendelkezik.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="assertionSubjectName" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email"  />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

A **OutputClaimsTransformations** elem olyan **OutputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja, amelyek a kimeneti jogcímek módosítására vagy újak előállítására szolgálnak.

## <a name="metadata"></a>Metaadatok

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| PartnerEntity | Yes | Az SAML-identitás szolgáltatójának metaadatait tartalmazó URL-cím. Másolja az Identity Provider metaadatait, és vegye fel azt a CDATA elembe. `<![CDATA[Your IDP metadata]]>` |
| WantsSignedRequests | No | Azt jelzi, hogy a technikai profil megköveteli-e az összes kimenő hitelesítési kérelem aláírását. Lehetséges értékek: `true` vagy `false` . Az alapértelmezett érték `true`. Ha a értékre van állítva `true` , a **SamlMessageSigning** titkosítási kulcsot meg kell adni, és az összes kimenő hitelesítési kérelem alá van írva. Ha a érték értéke `false` , a **SigAlg** és az **aláírás** paraméterei (lekérdezési karakterlánc vagy post paraméter) ki vannak hagyva a kérelemből. Ez a metaadatok a metaadatok **AuthnRequestsSigned** attribútumát is szabályozza, amely a Azure ad B2C az identitás-szolgáltatóval megosztott technikai profil metaadataiban található. Azure AD B2C nem írja alá a kérelmet, ha a technikai profil metaadatainak **WantsSignedRequests** értéke értékre van állítva, `false` és az Identity Provider metaadatainak **WantAuthnRequestsSigned** értéke `false` vagy nincs megadva. |
| XmlSignatureAlgorithm | No | Az a módszer, amelyet a Azure AD B2C az SAML-kérelem aláírására használ. Ez a metaadatok az SAML-kérelemben szereplő  **SigAlg** paraméter (lekérdezési karakterlánc vagy post paraméter) értékét vezérlik. Lehetséges értékek: `Sha256` , `Sha384` ,, `Sha512` vagy `Sha1` (alapértelmezett). Győződjön meg arról, hogy az aláírási algoritmus mindkét oldalon ugyanazzal az értékkel van konfigurálva. Csak a tanúsítvány által támogatott algoritmust használja. |
| WantsSignedAssertions | No | Azt jelzi, hogy a technikai profilhoz szükséges-e az összes bejövő érvényesítés aláírása. Lehetséges értékek: `true` vagy `false` . Az alapértelmezett érték `true`. Ha a értékre van állítva `true` , a rendszer `saml:Assertion` az identitás-szolgáltató által az Azure ad B2C számára eljuttatott összes érvényesítési szakaszt alá kell írni. Ha a érték értéke `false` , az identitás-szolgáltató nem írja alá az állításokat, de még ha igen, Azure ad B2C nem ellenőrzi az aláírást. Ez a metaadatok a metaadatok jelző **WantsAssertionsSigned** is szabályozzák, amely az identitás-szolgáltatóval megosztott Azure ad B2C technikai profil metaadatainak kimenete. Ha letiltja a kijelentések érvényesítését, érdemes lehet letiltani a válasz aláírásának érvényesítését is (További információ: **ResponsesSigned**). |
| ResponsesSigned | No | Lehetséges értékek: `true` vagy `false` . Az alapértelmezett érték `true`. Ha a érték értéke `false` , akkor az identitás-szolgáltató nem írja alá az SAML-választ, de még ha igen, Azure ad B2C nem ellenőrzi az aláírást. Ha a érték értéke, akkor `true` a rendszer aláírja az Identitáskezelő által az Azure ad B2C számára küldött SAML-választ, és érvényesíteni kell. Ha letiltja az SAML-válaszok érvényesítését, érdemes lehet letiltani az érvényesítési aláírás érvényességét (További információ: **WantsSignedAssertions**). |
| WantsEncryptedAssertions | No | Azt jelzi, hogy a technikai profil minden bejövő állítását titkosítani kíván-e. Lehetséges értékek: `true` vagy `false` . Az alapértelmezett érték `false`. Ha a érték értéke, akkor `true` az Identitáskezelő által az Azure ad B2Cnak eljuttatott érvényesítéseket alá kell írni, és meg kell adni a **SamlAssertionDecryption** titkosítási kulcsát. Ha a érték értéke, akkor a `true` Azure ad B2C technikai profil metaadatai tartalmazzák a **titkosítási** szakaszt. Az identitás-szolgáltató beolvassa a metaadatokat, és titkosítja az SAML-válaszok érvényesítését a Azure AD B2C technikai profil metaadataiban megadott nyilvános kulccsal. Ha engedélyezi a kijelentések titkosítását, előfordulhat, hogy le kell tiltania a válasz aláírásának érvényesítését (további információért lásd: **ResponsesSigned**). |
| NameIdPolicyFormat | No | Meghatározza a kért tárgyat képviselő név azonosítójának korlátozásait. Ha nincs megadva, a rendszer a kért tárgyhoz tartozó személyazonossági szolgáltató által támogatott bármely típusú azonosítót használhatja. Például:  `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`. A **NameIdPolicyFormat** használható a **NameIdPolicyAllowCreate**. Tekintse meg az Identity Provider dokumentációját, amely útmutatást nyújt arról, hogy mely név-azonosító házirendeket támogatja a rendszer. |
| NameIdPolicyAllowCreate | No | A **NameIdPolicyFormat** használatakor a `AllowCreate` **NameIDPolicy** tulajdonságot is megadhatja. A metaadatok értéke `true` vagy `false` annak jelzése, hogy az identitás-szolgáltató jogosult-e új fiókot létrehozni a bejelentkezési folyamat során. Tekintse meg az Identity Provider dokumentációját, amely útmutatást nyújt ennek elvégzéséhez. |
| AuthenticationRequestExtensions | No | Az Azure AD BC és az identitás-szolgáltató között megállapodott opcionális protokoll-üzenet kiterjesztési elemek. A bővítmény XML formátumban jelenik meg. Az XML-adatértékeket a CDATA elemen belül adja hozzá `<![CDATA[Your IDP metadata]]>` . Tekintse meg az Identitáskezelő dokumentációját, és ellenőrizze, hogy a bővítmények elem támogatott-e. |
| IncludeAuthnContextClassReferences | No | Egy vagy több URI-hivatkozást ad meg a hitelesítési környezet osztályainak azonosításához. Ha például engedélyezni szeretné, hogy egy felhasználó csak a felhasználónévvel és a jelszóval jelentkezzen be, állítsa a értéket a következőre: `urn:oasis:names:tc:SAML:2.0:ac:classes:Password` . Ha engedélyezni szeretné a bejelentkezést a felhasználónévvel és a jelszóval védett munkameneten keresztül (SSL/TLS), adja meg a következőt: `PasswordProtectedTransport` . Tekintse meg az Identity Provider dokumentációját, amely útmutatást nyújt a támogatott **AuthnContextClassRef** URI azonosítókkal kapcsolatban. Vesszővel tagolt listaként több URI-t is meg kell adni. |
| IncludeKeyInfo | No | Azt jelzi, hogy az SAML-hitelesítési kérelem tartalmazza-e a tanúsítvány nyilvános kulcsát, amikor a kötés be van állítva `HTTP-POST` . Lehetséges értékek: `true` vagy `false` . |
| IncludeClaimResolvingInClaimsHandling  | No | A bemeneti és a kimeneti jogcímek esetén megadja, hogy a [jogcímek feloldása](claim-resolver-overview.md) szerepel-e a technikai profilban. Lehetséges értékek: `true` , vagy `false` (alapértelmezett). Ha a technikai profilban a jogcím-feloldót szeretné használni, állítsa be a következőt: `true` . |
|SingleLogoutEnabled| No| Azt jelzi, hogy a technikai profilba való bejelentkezés során a rendszer megpróbál-e kijelentkezni az összevont identitás-szolgáltatókról. További információ: Azure AD B2C- [munkamenet](session-behavior.md#sign-out)kijelentkezése.  Lehetséges értékek: `true` (alapértelmezett) vagy `false` .|

## <a name="cryptographic-keys"></a>Titkosítási kulcsok

A **CryptographicKeys** elem a következő attribútumokat tartalmazza:

| Attribútum |Kötelező | Leírás |
| --------- | ----------- | ----------- |
| SamlMessageSigning |Yes | Az SAML-üzenetek aláírásához használt X509-tanúsítvány (RSA-kulcs). Azure AD B2C ezzel a kulccsal aláírja a kéréseket, és elküldi azokat az identitás-szolgáltatónak. |
| SamlAssertionDecryption |No | Az X509-tanúsítvány (RSA-kulcs beállítása). Az SAML-identitás szolgáltatója a tanúsítvány nyilvános részét használja az SAML-válasz érvényesítésének titkosítására. Azure AD B2C a tanúsítvány privát részét használja az állítás visszafejtéséhez. |
| MetadataSigning |No | Az SAML-metaadatok aláírásához használt X509-tanúsítvány (RSA-kulcs). Azure AD B2C ezt a kulcsot használja a metaadatok aláírásához.  |

## <a name="saml-entityid-customization"></a>SAML-entityID testreszabása

Ha több SAML-alkalmazással is rendelkezik, amelyek különböző entityID függenek, felülbírálhatja a `issueruri` függő entitás fájljának értékét. Ehhez másolja a technikai profilt az alapfájl "Saml2AssertionIssuer" azonosítójával, és bírálja felül az `issueruri` értéket.

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

## <a name="next-steps"></a>Következő lépések

A következő cikkekben példákat talál az SAML-identitás-szolgáltatók használatának Azure AD B2C:

- [ADFS hozzáadása SAML-identitás-szolgáltatóként egyéni szabályzatok használatával](identity-provider-adfs.md)
- [Bejelentkezés Salesforce-fiókok használatával SAML-n keresztül](identity-provider-salesforce-saml.md)