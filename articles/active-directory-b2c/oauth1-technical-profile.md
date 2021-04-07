---
title: OAuth1 műszaki profil definiálása egyéni házirendben
titleSuffix: Azure AD B2C
description: OAuth 1,0 technikai profilt definiálhat egy egyéni szabályzatban Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7a2cbca8c02dcbfc0e59d31040d1fca7a790cd35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96936658"
---
# <a name="define-an-oauth1-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>OAuth1 műszaki profil definiálása egy Azure Active Directory B2C egyéni házirendben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A Azure Active Directory B2C (Azure AD B2C) támogatást nyújt a [OAuth 1,0 protokoll](https://tools.ietf.org/html/rfc5849) identitás-szolgáltatója számára. Ez a cikk a szabványos protokollt támogató jogcím-szolgáltatóval való interakcióra szolgáló technikai profil sajátosságait ismerteti. A OAuth1-alapú technikai profillal OAuth1-alapú összevonása (például Twitter) is használható. Az egyesítő lehetővé teszi, hogy a felhasználók bejelentkezzenek a meglévő közösségi vagy vállalati identitásokkal.

## <a name="protocol"></a>Protokoll

A **protokoll** elem **Name** attribútumát be kell állítani `OAuth1` . A **Twitter-OAUTH1** technikai profilhoz tartozó protokoll például a következő: `OAuth1` .

```xml
<TechnicalProfile Id="Twitter-OAUTH1">
  <DisplayName>Twitter</DisplayName>
  <Protocol Name="OAuth1" />
  ...
```

## <a name="input-claims"></a>Bemeneti jogcímek

A **szabályzattípushoz** és a **InputClaimsTransformations**  elemek üresek vagy hiányoznak.

## <a name="output-claims"></a>Kimeneti jogcímek

A **OutputClaims** elem tartalmazza a OAuth1-identitás szolgáltatója által visszaadott jogcímek listáját. Előfordulhat, hogy le kell képeznie a szabályzatban definiált jogcím nevét az identitás-szolgáltatóban definiált névre. Olyan jogcímeket is tartalmazhat, amelyeket nem ad vissza az identitás-szolgáltató, ha beállítja a **DefaultValue** attribútumot.

A **OutputClaimsTransformations** elem olyan **OutputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja, amelyek a kimeneti jogcímek módosítására vagy újak előállítására szolgálnak.

A következő példa a Twitter Identity Provider által visszaadott jogcímeket mutatja be:

- A **issuerUserId** jogcímhez rendelt **user_id** jogcím.
- A **DisplayName** jogcímhez hozzárendelt **screen_name** jogcím.
- A név leképezése nélküli **e-mail-** jogcím.

A technikai profil az Identitáskezelő által nem visszaadott jogcímeket is visszaadja:

- Az **identityProvider** -jogcím, amely tartalmazza az identitás-szolgáltató nevét.
- A **authenticationSource** jogcím alapértelmezett értéke `socialIdpAuthentication` .

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

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| client_id | Yes | Az identitás-szolgáltató alkalmazás-azonosítója. |
| ProviderName | No | Az identitás-szolgáltató neve. |
| request_token_endpoint | Yes | A kérelem jogkivonat-végpontjának URL-címe RFC 5849-ként. |
| authorization_endpoint | Yes | Az engedélyezési végpont URL-címe RFC 5849-ként. |
| access_token_endpoint | Yes | A jogkivonat-végpont URL-címe RFC 5849-ként. |
| ClaimsEndpoint | No | A felhasználói információs végpont URL-címe |
| ClaimsResponseFormat | No | A jogcímek válaszának formátuma.|

## <a name="cryptographic-keys"></a>Titkosítási kulcsok

A **CryptographicKeys** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| client_secret | Yes | Az Identity Provider alkalmazás ügyfél-titka.   |

## <a name="redirect-uri"></a>Átirányítási URI

Ha az Identitáskezelő átirányítási URI-JÁT konfigurálja, írja be a (z `https://{tenant-name}.b2clogin.com/{tenant-name}.onmicrosoft.com/{policy-id}/oauth1/authresp` ) értéket. Ne felejtse el lecserélni a `{tenant-name}` bérlő nevét (például contosob2c) és a `{policy-id}` szabályzat azonosítóját (például b2c_1a_policy). Az átirányítási URI-nak minden kisbetűsnek kell lennie. Adjon hozzá egy átirányítási URL-címet az összes olyan házirendhez, amely az identitás-szolgáltatói bejelentkezést használja.

Angol nyelvű Példák:

- [Twitter hozzáadása OAuth1-identitás-szolgáltatóként egyéni szabályzatok használatával](identity-provider-twitter.md)
