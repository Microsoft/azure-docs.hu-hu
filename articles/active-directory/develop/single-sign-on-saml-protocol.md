---
title: Azure egyszeri bejelentkezéses SAML-protokoll
titleSuffix: Microsoft identity platform
description: Ez a cikk az egyszeri Sign-On (SSO) SAML protokollt ismerteti Azure Active Directory
services: active-directory
documentationcenter: .net
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: kenwith
ms.custom: aaddev
ms.reviewer: paulgarn
ms.openlocfilehash: 40bf202e0f14f18d817e4e918f8372ba3c0a4ad8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91950669"
---
# <a name="single-sign-on-saml-protocol"></a>Egyszeri bejelentkezéses SAML-protokoll

Ez a cikk az SAML 2,0 hitelesítési kéréseit és válaszait ismerteti, amelyeket Azure Active Directory (Azure AD) támogat egyetlen Sign-On (SSO) számára.

Az alábbi protokoll-diagram az egyszeri bejelentkezési sorozatot ismerteti. A Cloud Service (a szolgáltató) egy HTTP-átirányítási kötést használ a `AuthnRequest` (hitelesítési kérelem) elem Azure ad-be (az identitás-szolgáltatóhoz) való átadásához. Az Azure AD ezután egy HTTP Post-kötést használ egy `Response` elem a Cloud Service-be való közzétételéhez.

![Egyszeri Sign-On (SSO) munkafolyamat](./media/single-sign-on-saml-protocol/active-directory-saml-single-sign-on-workflow.png)

> [!NOTE]
> Ez a cikk az SAML egyszeri bejelentkezéshez való használatát ismerteti. Az egyszeri bejelentkezés egyéb módjaival kapcsolatos további információkért (például az OpenID Connect vagy az integrált Windows-hitelesítés használatával) tekintse meg az [egyszeri bejelentkezést a Azure Active Directory lévő alkalmazásokba](../manage-apps/what-is-single-sign-on.md).

## <a name="authnrequest"></a>AuthnRequest

Felhasználói hitelesítés igényléséhez a Cloud Services egy elemet küld az `AuthnRequest` Azure ad-nek. Az SAML-2,0 minta `AuthnRequest` az alábbi példához hasonlóan néz ki:

```
<samlp:AuthnRequest
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="id6c1c178c166d486687be4aaf5e482730"
Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
</samlp:AuthnRequest>
```

| Paraméter | Típus | Leírás |
| --- | --- | --- |
| ID (Azonosító) | Kötelező | Az Azure AD ezt az attribútumot használja a `InResponseTo` visszaadott válasz attribútumának feltöltéséhez. Az azonosító nem kezdődhet számmal, ezért a közös stratégia egy olyan karakterláncot, mint az "id", egy GUID karakterlánc-ábrázolására. Például `id6c1c178c166d486687be4aaf5e482730` érvényes azonosító. |
| Verzió | Kötelező | Ezt a paramétert **2,0**-re kell állítani. |
| IssueInstant | Kötelező | Ez egy UTC-értékkel rendelkező DateTime karakterlánc, amely az ["o" formátummal](/dotnet/standard/base-types/standard-date-and-time-format-strings)rendelkezik. Az Azure AD egy ilyen típusú DateTime értéket vár, de nem értékeli vagy nem használja az értéket. |
| AssertionConsumerServiceURL | Választható | Ha meg van adni, ennek a paraméternek meg kell egyeznie az `RedirectUri` Azure ad-ban található felhőalapú szolgáltatással. |
| ForceAuthn | Választható | Ez egy logikai érték. Ha az értéke igaz, az azt jelenti, hogy a felhasználónak újra hitelesítenie kell magát, még akkor is, ha az Azure AD-vel érvényes munkamenetük van. |
| IsPassive | Választható | Ez egy logikai érték, amely azt határozza meg, hogy az Azure AD-nak csendes felhasználói beavatkozás nélkül kell-e hitelesítenie a felhasználót a munkamenet-cookie-val, ha van ilyen. Ha ez igaz, az Azure AD megkísérli hitelesíteni a felhasználót a munkamenet-cookie használatával. |

`AuthnRequest`A **rendszer figyelmen kívül hagyja** az összes többi attribútumot, például a beleegyezett, a célhelyet, a AssertionConsumerServiceIndex, a AttributeConsumerServiceIndex és a ProviderName.

Az Azure AD emellett figyelmen kívül hagyja a `Conditions` elemet a alkalmazásban `AuthnRequest` .

### <a name="issuer"></a>Kiállító

Az `Issuer` egy elemének `AuthnRequest` pontosan egyeznie kell az Azure ad felhőalapú szolgáltatásának egyik **ServicePrincipalNames** . Ez általában az alkalmazás regisztrációja során megadott **alkalmazás-azonosító URI-ra** van beállítva.

Az elemet tartalmazó SAML-kivonat `Issuer` az alábbi példához hasonlóan néz ki:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>NameIDPolicy

Ez az elem egy adott név-azonosító formátumot kér a válaszban, és nem kötelező az `AuthnRequest` Azure ad-ba küldött elemekben.

Egy `NameIdPolicy` elem az alábbi példához hasonlóan néz ki:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Ha `NameIDPolicy` meg van adni, a választható attribútumot is megadhatja `Format` . Az `Format` attribútum csak a következő értékek egyikével rendelkezhet: bármely más érték hibát eredményez.

* `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: Azure Active Directory a NameID jogcím páros-azonosítóként való kiszolgálása.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: Azure Active Directory a NameID-jogcímet e-mail cím formátumban adja ki.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: Ez az érték engedélyezi Azure Active Directory számára a jogcím formátumának kiválasztását. Azure Active Directory a NameID páros-azonosítóként adja ki a problémát.
* `urn:oasis:names:tc:SAML:2.0:nameid-format:transient`: Azure Active Directory a NameID-jogcímet véletlenszerűen generált értékként adja ki, amely egyedi az aktuális egyszeri bejelentkezési művelethez. Ez azt jelenti, hogy az érték ideiglenes, és nem használható a hitelesítő felhasználó azonosítására.

Ha `SPNameQualifier` meg van adva, az Azure ad a válaszban megegyező értéket fog tartalmazni `SPNameQualifier` .

Az Azure AD figyelmen kívül hagyja az `AllowCreate` attribútumot.

### <a name="requestauthncontext"></a>RequestAuthnContext
Az `RequestedAuthnContext` elem megadja a kívánt hitelesítési módszereket. Az `AuthnRequest` Azure ad-be eljuttatott elemek esetében nem kötelező. Az Azure AD olyan `AuthnContextClassRef` értékeket támogat, mint például a `urn:oasis:names:tc:SAML:2.0:ac:classes:Password` .

### <a name="scoping"></a>Tartalmazó
Az `Scoping` identitás-szolgáltatók listáját tartalmazó elem nem kötelező az `AuthnRequest` Azure ad-be eljuttatott elemekben.

Ha meg van adni, ne adja meg az `ProxyCount` attribútumot `IDPListOption` vagy az `RequesterID` elemet, mivel azok nem támogatottak.

### <a name="signature"></a>Aláírás
`Signature`Elemek elemének `AuthnRequest` megadása nem kötelező. Az Azure AD nem érvényesíti az aláírt hitelesítési kérelmeket, ha van ilyen aláírás. A kérelmező általi ellenőrzést csak a regisztrált jogcímek fogyasztói szolgáltatásának URL-címeire válaszolva biztosítjuk.

### <a name="subject"></a>Tárgy
Ne tartalmazzon egy `Subject` elemet. Az Azure AD nem támogatja a kérelem tárgyának megadását, és hibaüzenetet ad vissza, ha van ilyen.

## <a name="response"></a>Reagálás
Ha a kért bejelentkezés sikeresen befejeződik, az Azure AD választ küld a Cloud Service-nek. A sikeres bejelentkezési kísérletre adott válasz az alábbi példához hasonlóan néz ki:

```
<samlp:Response ID="_a4958bfd-e107-4e67-b06d-0d85ade2e76a" Version="2.0" IssueInstant="2013-03-18T07:38:15.144Z" Destination="https://contoso.com/identity/inboundsso.aspx" InResponseTo="id758d0ef385634593a77bdf7e632984b6" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
    ...
  </ds:Signature>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
  <Assertion ID="_bf9c623d-cc20-407a-9a59-c2d0aee84d12" IssueInstant="2013-03-18T07:38:15.144Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
    <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      ...
    </ds:Signature>
    <Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
  </Assertion>
</samlp:Response>
```

### <a name="response"></a>Reagálás

Az `Response` elem az engedélyezési kérelem eredményét tartalmazza. Az Azure AD beállítja `ID` az `Version` elemet és az `IssueInstant` értékeket a `Response` elemben. A következő attribútumokat is beállítja:

* `Destination`: Ha a bejelentkezés sikeresen befejeződött, ezt a szolgáltatást a szolgáltató `RedirectUri` (Cloud Service) adja meg.
* `InResponseTo`: A `ID` választ kezdeményező elem attribútumára van beállítva `AuthnRequest` .

### <a name="issuer"></a>Kiállító

Az Azure AD az `Issuer` elemet arra az esetre állítja be, ahol az az `https://sts.windows.net/<TenantIDGUID>/` \<TenantIDGUID> Azure ad-bérlő bérlői azonosítója.

A kiállítói elemmel kapcsolatos válasz például a következő mintához hasonlóan néz ki:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>Állapot

Az `Status` elem a bejelentkezés sikerességét vagy meghibásodását közvetíti. Tartalmazza a `StatusCode` elemet, amely tartalmaz egy kódot vagy egy beágyazott kódot, amely a kérelem állapotát jelöli. Emellett tartalmazza a `StatusMessage` elemet is, amely a bejelentkezési folyamat során létrehozott egyéni hibaüzeneteket tartalmazza.

<!-- TODO: Add an authentication protocol error reference -->

Az alábbi példa egy SAML-válasz egy sikertelen bejelentkezési kísérletre.

```
<samlp:Response ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Requester">
      <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:RequestUnsupported" />
    </samlp:StatusCode>
    <samlp:StatusMessage>AADSTS75006: An error occurred while processing a SAML2 Authentication request. AADSTS90011: The SAML authentication request property 'NameIdentifierPolicy/SPNameQualifier' is not supported.
Trace ID: 66febed4-e737-49ff-ac23-464ba090d57c
Timestamp: 2013-03-18 08:49:24Z</samlp:StatusMessage>
  </samlp:Status>
```

### <a name="assertion"></a>Állítás

A `ID` `IssueInstant` és az Azure ad mellett a `Version` Válasz elemében a következő elemeket is beállítja `Assertion` .

#### <a name="issuer"></a>Kiállító

Ez az a érték, ahol az az `https://sts.windows.net/<TenantIDGUID>/` \<TenantIDGUID> Azure ad-bérlő bérlői azonosítója.

```
<Issuer>https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>Aláírás

Az Azure AD a sikeres bejelentkezésre adott válaszként aláírja az állítást. Az `Signature` elem egy digitális aláírást tartalmaz, amelyet a felhőalapú szolgáltatás a forrás hitelesítésére használhat a kikényszerítés integritásának ellenőrzéséhez.

A digitális aláírás létrehozásához az Azure AD az aláíró kulcsot használja a `IDPSSODescriptor` metaadat-dokumentum elemében.

```
<ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>Tárgy

Ez határozza meg az utasításban szereplő utasítások tárgyát képező rendszerbiztonsági tag. Tartalmaz egy `NameID` elemet, amely a hitelesített felhasználót jelöli. Az `NameID` érték egy célzott azonosító, amely csak a jogkivonat célközönségét képező szolgáltatóhoz van irányítva. Állandó – visszavonható, de soha nem lesz hozzárendelve. Az is átlátszatlan, hogy nem fedi fel a felhasználót, és nem használható azonosítóként az attribútumok lekérdezéséhez.

Az `Method` elem attribútuma mindig a következőre `SubjectConfirmation` van beállítva: `urn:oasis:names:tc:SAML:2.0:cm:bearer` .

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### <a name="conditions"></a>Feltételek

Ez az elem olyan feltételeket határoz meg, amelyek meghatározzák az SAML-érvényesítések elfogadható használatát.

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

A `NotBefore` és `NotOnOrAfter` attribútumok határozzák meg azt az időközt, amely alatt az érvényesítés érvényes.

* Az `NotBefore` attribútum értéke nem lehet kisebb (másodpercnél kevesebb), mint az `IssueInstant` elem attribútumának értéke `Assertion` . Az Azure AD nem veszi figyelembe a saját maga és a felhőalapú szolgáltatás (szolgáltató) közötti időeltérést, és nem ad hozzá puffert ehhez az időponthoz.
* Az `NotOnOrAfter` attribútum értéke 70 perccel későbbi, mint az `NotBefore` attribútum értéke.

#### <a name="audience"></a>Célközönség

Ez egy olyan URI-t tartalmaz, amely a célközönséget azonosítja. Az Azure AD az elem értékét a `Issuer` bejelentkezést kezdeményező elem értékére állítja be `AuthnRequest` . Az érték kiértékeléséhez `Audience` használja az `App ID URI` alkalmazás regisztrációja során megadott értéket.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Az `Issuer` értékhez hasonlóan az `Audience` értéknek pontosan egyeznie kell az Azure ad-ben a Cloud Service-t jelölő egyszerű szolgáltatásnév egyikével. Ha azonban az `Issuer` elem értéke nem URI-érték, a válaszban szereplő érték az `Audience` előtaggal megadott érték `Issuer` `spn:` .

#### <a name="attributestatement"></a>AttributeStatement

Ez a tulajdonossal vagy a felhasználóval kapcsolatos jogcímeket tartalmaz. A következő részlet egy minta `AttributeStatement` elemet tartalmaz. A három pont azt jelzi, hogy az elem több attribútumot és attribútumérték is tartalmazhat.

```
<AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
</AttributeStatement>
```        

* **Név jogcím** – az `Name` attribútum () értéke a `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` hitelesített felhasználó egyszerű felhasználóneve, például: `testuser@managedtenant.com` .
* **Típusinformáció jogcím** – az `ObjectIdentifier` attribútum () értéke az `http://schemas.microsoft.com/identity/claims/objectidentifier` `ObjectId` Azure ad-ben a hitelesített felhasználót jelképező címtár-objektum. `ObjectId` a hitelesített felhasználó nem módosítható, globálisan egyedi, és használható biztonságos azonosítója.

#### <a name="authnstatement"></a>AuthnStatement

Ez az elem azt állítja be, hogy az állítás tárgyát egy adott időpontban hitelesítették.

* Az `AuthnInstant` attribútum azt az időpontot határozza meg, amikor a felhasználó az Azure ad-vel lett hitelesítve.
* Az `AuthnContext` elem a felhasználó hitelesítéséhez használt hitelesítési környezetet határozza meg.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```
