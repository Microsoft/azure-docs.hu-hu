---
title: A bejelentkezés SAML-identitás-szolgáltatói beállításokkal való megadása
titleSuffix: Azure Active Directory B2C
description: A bejelentkezési SAML-identitás-szolgáltató (identitásszolgáltató) beállításainak konfigurálása Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 43c57950d317de42df666ddd25cbcb2e9a4c9611
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488873"
---
# <a name="configure-saml-identity-provider-options-with-azure-active-directory-b2c"></a>SAML-identitás-szolgáltatói beállítások konfigurálása Azure Active Directory B2C

A Azure Active Directory B2C (Azure AD B2C) támogatja az SAML 2,0 identitás-szolgáltatókkal való összevonást. Ez a cikk azokat a konfigurációs beállításokat ismerteti, amelyek akkor érhetők el, ha engedélyezi a bejelentkezést SAML-identitás-szolgáltatóval.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="claims-mapping"></a>Jogcím-hozzárendelés

A **OutputClaims** elem tartalmazza a SAML-identitás szolgáltatója által visszaadott jogcímek listáját. Le kell képeznie a szabályzatban definiált jogcím nevét az identitás-szolgáltatóban definiált névre. A jogcímek listájának megadásához keresse meg az identitás-szolgáltatót. Azt is megtekintheti, hogy az Ön Identity Provider által visszaadott SAML-válasz tartalma is megjelenik-e. További információkért lásd [az SAML-üzenetek hibakeresését](#debug-saml-protocol)ismertető témakört. Jogcímek hozzáadásához először [Definiáljon egy jogcímet](claimsschema.md), majd adja hozzá a jogcímet a kimeneti jogcímek gyűjteményéhez.

Olyan jogcímeket is megadhat, amelyeket nem ad vissza az identitás-szolgáltató, ha beállítja az `DefaultValue` attribútumot. Az alapértelmezett érték lehet statikus vagy dinamikus, [környezeti jogcímek](#enable-use-of-context-claims)használatával.

A kimeneti jogcím elem a következő attribútumokat tartalmazza:

- A **ClaimTypeReferenceId** a jogcím típusára mutató hivatkozás. 
- A **PartnerClaimType** az SAML-kijelentést megjelenő tulajdonság neve. 
- A **DefaultValue** egy előre definiált alapértelmezett érték. Ha a jogcím üres, a rendszer az alapértelmezett értéket fogja használni. A [jogcímek feloldóit](claim-resolver-overview.md) környezetfüggő értékkel is használhatja, például a korrelációs azonosítót vagy a felhasználói IP-címet.

### <a name="subject-name"></a>Tulajdonos neve

Ha **az SAML** -kijelentést normalizált jogcímként **NameId** szeretné olvasni, állítsa a jogcím **PartnerClaimType** az attribútum értékére `SPNameQualifier` . Ha az `SPNameQualifier` attribútum nem jelenik meg, állítsa a jogcím **PartnerClaimType** értéket az attribútum értékére `NameQualifier` .

SAML-állítás:

```xml
<saml:Subject>
  <saml:NameID SPNameQualifier="http://your-idp.com/unique-identifier" Format="urn:oasis:names:tc:SAML:2.0:nameid-format:transient">david@contoso.com</saml:NameID>
  <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
    <SubjectConfirmationData InResponseTo="_cd37c3f2-6875-4308-a9db-ce2cf187f4d1" NotOnOrAfter="2020-02-15T16:23:23.137Z" Recipient="https://<your-tenant>.b2clogin.com/<your-tenant>.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" />
    </SubjectConfirmation>
  </saml:SubjectConfirmation>
</saml:Subject>
```

Kimeneti jogcím:

```xml
<OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="http://your-idp.com/unique-identifier" />
```

Ha mindkettő `SPNameQualifier` vagy `NameQualifier` attribútumok nem jelennek meg az SAML-állításban, állítsa be a jogcím **PartnerClaimType** `assertionSubjectName` . Győződjön meg arról, hogy a **NameId** az érvényesítési XML első értéke. Ha egynél több kijelentést határoz meg, Azure AD B2C kiválasztja a tárgy értékét az utolsó állításból.


## <a name="configure-saml-protocol-bindings"></a>SAML protokoll kötések konfigurálása

Az SAML-kérelmeket az Identity Provider metaadat-elemében megadott módon küldik el az identitás-szolgáltatónak `SingleSignOnService` . Az Identitáskezelés legtöbb hitelesítési kérelmét közvetlenül egy HTTP GET-kérelem URL-lekérdezési karakterlánca végzi (mivel az üzenetek viszonylag rövidek). Az SAML-kérelmek kötéseinek konfigurálásához tekintse meg az Identity Provider dokumentációját.

A következő példa egy Azure AD-beli metaadat-alapú egyszeri bejelentkezési szolgáltatásra mutat be két kötést. Az `HTTP-Redirect` elsőbbséget élvez az `HTTP-POST` , mert az SAML-identitás szolgáltatójának metaadataiban elsőként jelenik meg.

```xml
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/saml2"/>
  <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/saml2"/>
</IDPSSODescriptor>
```

Az SAML-válaszokat a rendszer a HTTP POST-kötésen keresztül továbbítja Azure AD B2C. Azure AD B2C házirend metaadatainak beállítja a kötést a következőre: `AssertionConsumerService` `urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST` .

A következőkben egy példa látható egy Azure AD B2C házirend metaadat-felállítására szolgáló fogyasztói szolgáltatási elemre.

```xml
<SPSSODescriptor AuthnRequestsSigned="true" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  <AssertionConsumerService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://your-tenant.b2clogin.com/your-tenant/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" index="0" isDefault="true"/>
</SPSSODescriptor>
```

## <a name="configure-the-saml-request-signature"></a>Az SAML-kérelem aláírásának konfigurálása

Azure AD B2C aláírja az összes kimenő hitelesítési kérelmet a **SamlMessageSigning** titkosítási kulcs használatával. Az SAML-kérelem aláírásának letiltásához állítsa be a **WantsSignedRequests** a következőre: `false` . Ha a metaadatok értéke `false` , a **SigAlg** és az **aláírás** paraméterei (lekérdezési karakterlánc vagy post paraméter) ki vannak hagyva a kérelemből.

Ez a metaadatok a **AuthnRequestsSigned** attribútumot is szabályozza, amely tartalmazza az identitás-szolgáltatóval megosztott Azure ad B2C technikai profil metaadatait. Azure AD B2C nem írja alá a kérelmet, ha a technikai profil metaadatainak **WantsSignedRequests** értéke értékre van állítva, `false` és az Identity Provider metaadatainak **WantAuthnRequestsSigned** értéke `false` vagy nincs megadva.

A következő példa eltávolítja az aláírást az SAML-kérelemből.

```xml
<Metadata>
  ...
  <Item Key="WantsSignedRequests">false</Item>
</Metadata>
```

### <a name="signature-algorithm"></a>Aláírási algoritmus

Azure AD B2C `Sha1` az SAML-kérelem aláírására használja. A használni kívánt algoritmus konfigurálásához használja a **XmlSignatureAlgorithm** metaadatait. A lehetséges értékek:,, `Sha256` `Sha384` `Sha512` vagy `Sha1` (alapértelmezett). Ez a metaadatok az SAML-kérelemben szereplő  **SigAlg** paraméter (lekérdezési karakterlánc vagy post paraméter) értékét vezérlik. Győződjön meg arról, hogy az aláírási algoritmus mindkét oldalon ugyanazzal az értékkel van konfigurálva. Csak a tanúsítvány által támogatott algoritmust használja.

### <a name="include-key-info"></a>Fontos adatok belefoglalása

Ha az Identitáskezelő azt jelzi, hogy Azure AD B2C kötés van beállítva `HTTP-POST` , Azure ad B2C az SAML-kérelem törzsében lévő aláírást és algoritmust is tartalmazza. Az Azure AD-t úgy is beállíthatja, hogy tartalmazza a tanúsítvány nyilvános kulcsát, amikor a kötés be van állítva `HTTP-POST` . Használja a **IncludeKeyInfo** metaadatokat a következőhöz: `true` vagy `false` . A következő példában az Azure AD nem fogja tartalmazni a tanúsítvány nyilvános kulcsát.

```xml
<Metadata>
  ...
  <Item Key="IncludeKeyInfo">false</Item>
</Metadata>
```

## <a name="configure-saml-request-name-id"></a>SAML-kérelem nevének AZONOSÍTÓjának konfigurálása

Az SAML-engedélyezési kérelem `<NameID>` elem az SAML-név azonosítójának formátumát jelzi. Ez a szakasz az alapértelmezett konfigurációt és a név-azonosító elem testreszabását ismerteti.

## <a name="preferred-username"></a>Előnyben részesített Felhasználónév

A bejelentkezési felhasználói úton a függő entitás alkalmazás egy adott felhasználót célozhat meg. Azure AD B2C lehetővé teszi, hogy egy előnyben részesített felhasználónevet küldjön a SAML-identitás szolgáltatójának. A **szabályzattípushoz** elem az SAML-engedélyezési kérelem **tárgyában** található **NameId** küldésére szolgál.

A tulajdonos nevének AZONOSÍTÓjának az engedélyezési kérelembe való felvételéhez adja hozzá a következő `<InputClaims>` elemet közvetlenül a után `<CryptographicKeys>` . A **PartnerClaimType** a következőre kell beállítani: `subject` .

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="subject" />
</InputClaims>
```

Ebben a példában a Azure AD B2C az SAML-engedélyezési kérelem **tárgyában** lévő **NameId** küldi el a **signInName** jogcím értékét.

```xml
<samlp:AuthnRequest ... >
  ...
  <saml:Subject>
    <saml:NameID>sam@contoso.com</saml:NameID>
  </saml:Subject>
</samlp:AuthnRequest>
```

[Környezeti jogcímeket](claim-resolver-overview.md)is használhat, például `{OIDC:LoginHint}` feltöltheti a jogcím értékét.

```xml
<Metadata>
  ...
  <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
</Metadata>
  ...
<InputClaims>
  <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="subject" DefaultValue="{OIDC:LoginHint}" AlwaysUseDefaultValue="true" />
</InputClaims>
```

### <a name="name-id-policy-format"></a>Név-azonosító házirend formátuma

Alapértelmezés szerint az SAML-engedélyezési kérelem meghatározza a `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` szabályzatot. Ez azt jelzi, hogy a kért tárgyhoz tartozó személyazonossági szolgáltató által támogatott bármely típusú azonosító használható.

Ennek a viselkedésnek a megváltoztatásához tekintse meg az identitás-szolgáltató dokumentációját, amely útmutatást nyújt a név-azonosító szabályzatok támogatásához. Ezután adja hozzá a `NameIdPolicyFormat` metaadatokat a megfelelő házirend-formátumhoz. Például:

```xml
<Metadata>
  ...
  <Item Key="NameIdPolicyFormat">urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress</Item>
</Metadata>
```

A következő SAML-engedélyezési kérelem tartalmazza a név-azonosító házirendet.

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress" />
</samlp:AuthnRequest>
```

### <a name="allow-creating-new-accounts"></a>Új fiókok létrehozásának engedélyezése

Ha megadja a [név-azonosító házirend formátumát](#name-id-policy-format), a NameIDPolicy tulajdonságát is megadhatja `AllowCreate` annak jelzésére, hogy az identitás-szolgáltató jogosult-e új fiókot létrehozni a bejelentkezési folyamat során.  Útmutatásért tekintse meg az Identitáskezelő dokumentációját.

Azure AD B2C alapértelmezés szerint kihagyja a `AllowCreate` tulajdonságot. Ezt a viselkedést a metaadatok használatával módosíthatja `NameIdPolicyAllowCreate` . A metaadatok értéke `true` vagy `false` .

Az alábbi példa bemutatja, hogyan állíthatja be a tulajdonságát a következőre: `AllowCreate` `NameIDPolicy` `true` .

```xml
<Metadata>
  ...
  <Item Key="NameIdPolicyFormat">urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress</Item>
  <Item Key="NameIdPolicyAllowCreate">true</Item>
</Metadata>
```


Az alábbi példa egy engedélyezési kérelmet mutat be az engedélyezési kérelemben található **NameIDPolicy** elem **AllowCreate** .


```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:NameIDPolicy 
      Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress" 
      AllowCreate="true" />
</samlp:AuthnRequest>
```

### <a name="include-authentication-context-class-references"></a>A hitelesítési környezet osztályára vonatkozó referenciák belefoglalása

Egy SAML-engedélyezési kérelem tartalmazhat egy **AuthnContext** elemet, amely meghatározza egy engedélyezési kérelem környezetét. Az elem tartalmazhatja a hitelesítési környezet osztályának hivatkozását, amely megadja a SAML-identitás szolgáltatójának, hogy a felhasználó milyen hitelesítési mechanizmust tud bemutatni.

A hitelesítési környezet osztályok hivatkozásainak konfigurálásához adja hozzá a **IncludeAuthnContextClassReferences** metaadatait. Az érték mezőben válasszon ki egy vagy több URI-hivatkozást a hitelesítési környezet osztályainak azonosításához. Vesszővel tagolt listaként több URI-t is meg kell adni. A támogatott **AuthnContextClassRef** URI azonosítókkal kapcsolatos útmutatásért tekintse meg az Identity Provider dokumentációját.

A következő példa lehetővé teszi a felhasználók számára, hogy felhasználónévvel és jelszóval jelentkezzenek be, és a felhasználónévvel és jelszóval bejelentkezhetnek egy védett munkamenetben (SSL/TLS).

```xml
<Metadata>
  ...
  <Item Key="IncludeAuthnContextClassReferences">urn:oasis:names:tc:SAML:2.0:ac:classes:Password,urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</Item>
</Metadata>
```

A következő SAML-engedélyezési kérelem tartalmazza a hitelesítési környezet osztályának hivatkozásait.

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:RequestedAuthnContext>
    <saml:AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</saml:AuthnContextClassRef>
    <saml:AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</saml:AuthnContextClassRef>
  </samlp:RequestedAuthnContext>
</samlp:AuthnRequest>
```

## <a name="include-custom-data-in-the-authorization-request"></a>Egyéni adatértékek belefoglalása az engedélyezési kérelembe

Megadhatja a protokoll üzenet-bővítmény elemeit is, amelyeket az Azure AD BC és az identitás-szolgáltató is elfogad. A bővítmény XML formátumban jelenik meg. A kiterjesztési elemek belefoglalásával XML-adatokat adhat hozzá a CDATA elemen belül `<![CDATA[Your IDP metadata]]>` . Tekintse meg az Identitáskezelő dokumentációját, és ellenőrizze, hogy a bővítmények elem támogatott-e.

Az alábbi példa a bővítményi adatmennyiség használatát szemlélteti:

```xml
<Metadata>
  ...
  <Item Key="AuthenticationRequestExtensions"><![CDATA[
            <ext:MyCustom xmlns:ext="urn:ext:custom">
              <ext:AssuranceLevel>1</ext:AssuranceLevel>
              <ext:AssuranceDescription>Identity verified to level 1.</ext:AssuranceDescription>
            </ext:MyCustom>]]></Item>
</Metadata>
```

Az SAML protokoll üzenet-bővítményének használatakor az SAML-válasz a következő példához hasonlóan fog kinézni:

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:Extensions>
    <ext:MyCustom xmlns:ext="urn:ext:custom">
      <ext:AssuranceLevel>1</ext:AssuranceLevel>
      <ext:AssuranceDescription>Identity verified to level 1.</ext:AssuranceDescription>
    </ext:MyCustom>
  </samlp:Extensions>
</samlp:AuthnRequest>
```

## <a name="require-signed-saml-responses"></a>Aláírt SAML-válaszok megkövetelése

Azure AD B2C megköveteli az összes bejövő érvényesítés aláírását. Ezt a követelményt a **WantsSignedAssertions** beállításával távolíthatja el `false` . Az identitás-szolgáltatónak ebben az esetben nem kell aláírnia az állításokat, de még ha igen, Azure AD B2C nem ellenőrzi az aláírást.

A **WantsSignedAssertions** -metaadatok vezérlik az SAML-metaadatok jelző **WantAssertionsSigned**, amely szerepel az identitás-szolgáltatóval megosztott Azure ad B2C technikai profil metaadataiban.

```xml
<SPSSODescriptor AuthnRequestsSigned="true" WantAssertionsSigned="true" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```

Ha letiltja a kijelentések érvényesítését, előfordulhat, hogy le szeretné tiltani a válaszüzenet aláírásának érvényesítését is. Állítsa be a **ResponsesSigned** metaadatait a következőre: `false` . Ebben az esetben az Identity Provider nem írja alá az SAML-válaszüzenetet, de még ha igen, Azure AD B2C nem ellenőrzi az aláírást.

Az alábbi példa eltávolítja az üzenetet és az érvényesítési aláírást is:

```xml
<Metadata>
  ...
  <Item Key="WantsSignedAssertions">false</Item>
  <Item Key="ResponsesSigned">false</Item>
</Metadata>
```

## <a name="require-encrypted-saml-responses"></a>Titkosított SAML-válaszok szükségesek

Az összes bejövő utasítás titkosításának megköveteléséhez állítsa be a **WantsEncryptedAssertions** metaadatait. Ha titkosításra van szükség, az identitás-szolgáltató egy Azure AD B2C technikai profilban lévő titkosítási tanúsítvány nyilvános kulcsát használja. Azure AD B2C a titkosítási tanúsítvány privát részének használatával visszafejti a válasz kijelentését.

Ha engedélyezi a kijelentések titkosítását, előfordulhat, hogy le kell tiltania a válasz aláírásának érvényesítését (további információért lásd: [aláírt SAML-válaszok megkövetelése](#require-signed-saml-responses).

Ha a **WantsEncryptedAssertions** metaadata be van állítva `true` , a Azure ad B2C technikai profil metaadatai tartalmazzák a **titkosítási** szakaszt. Az identitás-szolgáltató beolvassa a metaadatokat, és titkosítja az SAML-válaszok érvényesítését a Azure AD B2C technikai profil metaadataiban megadott nyilvános kulccsal.

Az alábbi példa a titkosításhoz használt SAML-metaadatok kulcs leíró szakaszát mutatja be:

```xml
<SPSSODescriptor AuthnRequestsSigned="true"  protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  <KeyDescriptor use="encryption">
    <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
      <X509Data>
        <X509Certificate>valid certificate</X509Certificate>
      </X509Data>
    </KeyInfo>
  </KeyDescriptor>
  ...
</SPSSODescriptor>
```

Az SAML-válasz kijelentésének titkosítása:

1. [Hozzon létre egy olyan házirend-kulcsot](identity-provider-generic-saml.md#create-a-policy-key) , amely egyedi azonosítóval rendelkezik. Például: `B2C_1A_SAMLEncryptionCert`.
2. Az SAML technikai profil **CryptographicKeys** gyűjteményében. Állítsa a **StorageReferenceId** az első lépésben létrehozott házirend-kulcs nevére. Az `SamlAssertionDecryption` azonosító a titkosítási kulcs használatát jelzi az SAML-válasz érvényesítésének titkosításához és visszafejtéséhez.

    ```xml
    <CryptographicKeys>
            ...
      <Key Id="SamlAssertionDecryption" StorageReferenceId="B2C_1A_SAMLEncryptionCert"/>
    </CryptographicKeys>
    ```

3. Állítsa be a technikai profil metaadatainak **WantsEncryptedAssertions** a következőre: `true` .
    
    ```xml
    <Metadata>
      ...
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
    ```

4. Frissítse az identitás-szolgáltatót az új Azure AD B2C technikai profil metaadataival. Látnia kell a **leírót** a **use** tulajdonsággal, amely `encryption` a tanúsítvány nyilvános kulcsát tartalmazza.

## <a name="enable-use-of-context-claims"></a>Környezeti jogcímek használatának engedélyezése

A bemeneti és kimeneti jogcímek gyűjteményben olyan jogcímeket is megadhat, amelyeket az identitás-szolgáltató nem ad vissza, ha beállítja az `DefaultValue` attribútumot. [Környezeti jogcímeket](claim-resolver-overview.md) is használhat a technikai profilba való felvételhez. Környezeti jogcímek használata:

1. Adja hozzá a jogcím típusát a [ClaimsSchema](claimsschema.md) elemhez a [BuildingBlocks](buildingblocks.md)-n belül.
2. Adja hozzá a kimeneti jogcímet a bemeneti vagy kimeneti gyűjteményhez. Az alábbi példában az első jogcím a személyazonosság-szolgáltató értékét állítja be. A második jogcím a felhasználói IP-cím [környezeti jogcímeit](claim-resolver-overview.md)használja.
    
    ```xml
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" AlwaysUseDefaultValue="true" />
      <OutputClaim ClaimTypeReferenceId="IpAddress" DefaultValue="{Context:IPAddress}" AlwaysUseDefaultValue="true" />
    </OutputClaims>
    ```

## <a name="disable-single-logout"></a>Egyszeri kijelentkezés letiltása

Egy alkalmazás kijelentkezési kérelme Azure AD B2C megpróbál kijelentkezni az SAML-identitás szolgáltatójából. További információ: Azure AD B2C- [munkamenet](session-behavior.md#sign-out)kijelentkezése.  Az egyszeri kijelentkezési viselkedés letiltásához állítsa be a **SingleLogoutEnabled** metaadatait a következőre: `false` .

```xml
<Metadata>
  ...
  <Item Key="SingleLogoutEnabled">false</Item>
</Metadata>
```

## <a name="debug-saml-protocol"></a>SAML-protokoll hibakeresése

Az SAML-identitás-szolgáltatóval való összevonás konfigurálásához és hibakereséséhez használhat egy böngésző-bővítményt az SAML protokollhoz, például az [SAML devtools bővítményt](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio) a Chrome, az [SAML-Tracer](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) for Firefox, vagy a [Edge vagy az IE fejlesztői eszközökhöz](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957).

Ezekkel az eszközökkel ellenőrizhető a Azure AD B2C és az SAML-identitás szolgáltatója közötti integráció. Például:

* Ellenőrizze, hogy az SAML-kérelem tartalmaz-e aláírást, és határozza meg, hogy milyen algoritmust használ a rendszer az engedélyezési kérelemben való bejelentkezéshez.
* Szerezze be a jogcímeket (kijelentéseket) a `AttributeStatement` szakasz alatt.
* Ellenőrizze, hogy az identitás-szolgáltató egy hibaüzenetet ad-e vissza.
* Ellenőrizze, hogy az állítás szakasz titkosítva van-e.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan diagnosztizálhatja az egyéni szabályzatokkal kapcsolatos problémákat [Application Insights](troubleshoot-with-application-insights.md)használatával. 

::: zone-end
