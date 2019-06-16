---
title: Egy egyéni házirendek az Azure Active Directory B2C egy OAuth1 technikai profil meghatározása |} A Microsoft Docs
description: Adja meg az Azure Active Directory B2C egyéni házirendet egy OAuth1 technikai profil.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 993fc8b2e318b59775f61de391ac75fa765485f0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66513120"
---
# <a name="define-an-oauth1-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Egyéni Azure Active Directory B2C-házirendek egy OAuth1 technikai profil meghatározása

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory (Azure AD) B2C támogatást biztosít a [OAuth 1.0-s protokollt](https://tools.ietf.org/html/rfc5849) identitásszolgáltató. Ez a cikk ismerteti, amely támogatja a szabványos protokoll Jogcímszolgáltatók folytatott interakcióra szolgáló technikai profil adatait. OAuth1 műszaki profillal OAuth1 alapján Identitásszolgáltatóként, mint például a Twitter használatával is összevonható. Összevonás az identitásszolgáltató lehetővé teszi a felhasználóknak, hogy jelentkezzen be a meglévő közösségi vagy vállalati identitásokat.

## <a name="protocol"></a>Protocol

A **neve** attribútuma a **protokoll** elemet hozzá kell beállítani `OAuth1`. Például a protokoll a **Twitter-OAUTH1** technikai profil `OAuth1`.

```XML
<TechnicalProfile Id="Twitter-OAUTH1">
  <DisplayName>Twitter</DisplayName>
  <Protocol Name="OAuth1" />
  ...    
```

## <a name="input-claims"></a>A bemeneti jogcímek

A **InputClaims** és **InputClaimsTransformations** elemek a következők üres vagy nincs jelen.

## <a name="output-claims"></a>Kimeneti jogcímek

A **OutputClaims** elem OAuth1 identitásszolgáltató által visszaküldött jogcímek listáját tartalmazza. Szükség lehet a meghatározott az identitásszolgáltató nevét a szabályzatban meghatározott jogcím nevének való leképezéséhez. Nem adott vissza az identitásszolgáltató mindaddig, amíg meg jogcímeket is használható a **DefaultValue** attribútum.

A **OutputClaimsTransformations** elemet tartalmazhat egy gyűjteményét **OutputClaimsTransformation** elemek, amelyek segítségével módosíthatja a kimeneti jogcímek, és hozzon létre újakat.

Az alábbi példa bemutatja a jogcímeket, a Twitter-identitás szolgáltató által visszaadott:

- A **user_id** jogcímet, amelyet le van képezve a **issuerUserId** jogcím.
- A **screen_name** jogcímet, amelyet le van képezve a **displayName** jogcím.
- A **e-mail** jogcím a felhasználónév-leképezés nélkül.

A technikai profil is az identitásszolgáltató nem adott vissza jogcímeket adja vissza: 

- A **identityProvider** jogcímet, amelyet az identitásszolgáltató nevét tartalmazza.
- A **authenticationSource** jogcím alapértelmezett értéke `socialIdpAuthentication`.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metaadatok

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| client_id | Igen | Az identitásszolgáltató az alkalmazás azonosítója. |
| ProviderName | Nem | Az identitásszolgáltató neve. |
| request_token_endpoint | Igen | A kérelem jogkivonat-végpont megfelelően RFC 5849 URL-címe |
| authorization_endpoint | Igen | RFC 5849 megfelelően az engedélyezési végpont URL-címe |
| access_token_endpoint | Igen | RFC 5849 megfelelően a jogkivonat-végpont URL-címe |
| ClaimsEndpoint | Nem | A végpont URL-címét a felhasználói adatokat. | 
| ClaimsResponseFormat | Nem | A jogcímek válaszának formátuma.|

## <a name="cryptographic-keys"></a>Titkosítási kulcsok

A **CryptographicKeys** elem tartalmazza a következő attribútumot:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| client_secret | Igen | A titkos ügyfélkulcsot az identity provider alkalmazás.   | 

## <a name="redirect-uri"></a>Redirect URI

Az identitásszolgáltató az átirányítási URL-cím konfigurálásakor adja meg a `https://login.microsoftonline.com/te/tenant/policyId/oauth1/authresp`. Cserélje le **bérlői** a bérlő neve (például: contosob2c.onmicrosoft.com) és **policyId** azonosítóval a szabályzat (például b2c_1a_policy). Az átirányítási URI-t kell lennie az összes kisbetűt. Adjon hozzá egy átirányítási URL-címet minden szabályzat, amely az identity provider-bejelentkezés használatához. 

Ha használja a **b2clogin.com** helyett tartományi **login.microsoftonline.com** ügyeljen arra, hogy a b2clogin.com használata helyett login.microsoftonline.com.

Példák:

- [Adja hozzá a Twitter OAuth1 Identitásszolgáltatóként egyéni szabályzatok használatával](active-directory-b2c-custom-setup-twitter-idp.md)













