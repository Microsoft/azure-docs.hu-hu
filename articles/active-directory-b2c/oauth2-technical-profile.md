---
title: OAuth2 műszaki profil definiálása egyéni házirendben
titleSuffix: Azure AD B2C
description: OAuth2-technikai profilt definiálhat egy egyéni szabályzatban Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/24/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cda04ad57f1984064692cb1df4accc5a99de0910
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85204030"
---
# <a name="define-an-oauth2-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>OAuth2 műszaki profil definiálása egy Azure Active Directory B2C egyéni házirendben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A Azure Active Directory B2C (Azure AD B2C) támogatást nyújt a OAuth2 protokoll identitás-szolgáltatója számára. A OAuth2 az engedélyezés és a delegált hitelesítés elsődleges protokollja. További információ: [RFC 6749 The OAuth 2,0 Authorization Framework](https://tools.ietf.org/html/rfc6749). A OAuth2-alapú technikai profillal OAuth2-alapú összevonása, például a Facebook használatával is elvégezhető. Az egyesítő lehetővé teszi, hogy a felhasználók bejelentkezzenek a meglévő közösségi vagy vállalati identitásokkal.

## <a name="protocol"></a>Protokoll

A **protokoll** elem **Name** attribútumát be kell állítani `OAuth2` . A **Facebook-OAUTH** technikai profilhoz tartozó protokoll például a következő `OAuth2` :

```xml
<TechnicalProfile Id="Facebook-OAUTH">
  <DisplayName>Facebook</DisplayName>
  <Protocol Name="OAuth2" />
  ...
```

## <a name="input-claims"></a>Bemeneti jogcímek

A **szabályzattípushoz** és a **InputClaimsTransformations** elemek nem szükségesek. Előfordulhat azonban, hogy további paramétereket szeretne küldeni az identitás-szolgáltatónak. A következő példa hozzáadja a **domain_hint** lekérdezési karakterlánc paramétert az `contoso.com` engedélyezési kérelem értékével.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Kimeneti jogcímek

A **OutputClaims** elem tartalmazza a OAuth2-identitás szolgáltatója által visszaadott jogcímek listáját. Előfordulhat, hogy le kell képeznie a szabályzatban definiált jogcím nevét az identitás-szolgáltatóban definiált névre. Olyan jogcímeket is megadhat, amelyeket nem ad vissza az identitás-szolgáltató, ha beállítja az `DefaultValue` attribútumot.

A **OutputClaimsTransformations** elem olyan **OutputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja, amelyek a kimeneti jogcímek módosítására vagy újak előállítására szolgálnak.

A következő példa a Facebook-identitás szolgáltatója által visszaadott jogcímeket mutatja be:

- Az **first_name** jogcím a **givenName** jogcímhez van rendelve.
- A **last_name** jogcím a **vezetéknév** jogcímere van leképezve.
- Név-hozzárendelés nélküli **DisplayName** jogcím.
- A név leképezése nélküli **e-mail-** jogcím.

A technikai profil az Identitáskezelő által nem visszaadott jogcímeket is visszaadja:

- Az **identityProvider** -jogcím, amely tartalmazza az identitás-szolgáltató nevét.
- A **authenticationSource** jogcím alapértelmezett **socialIdpAuthentication**-értékkel rendelkezik.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="facebook.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metaadatok

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| client_id | Yes | Az identitás-szolgáltató alkalmazás-azonosítója. |
| IdTokenAudience | No | A id_token célközönsége. Ha meg van adva, Azure AD B2C ellenőrzi, hogy a jogkivonat az identitás-szolgáltató által visszaadott jogcímben van-e, és megegyezik-e a megadott értékkel. |
| authorization_endpoint | Yes | Az engedélyezési végpont URL-címe RFC 6749-ként. |
| AccessTokenEndpoint | Yes | A jogkivonat-végpont URL-címe RFC 6749-ként. |
| ClaimsEndpoint | Yes | A felhasználói adatok végpontjának URL-címe RFC 6749-ként. |
| AccessTokenResponseFormat | No | A hozzáférési jogkivonat-végpont hívásának formátuma. Például a Facebook HTTP GET metódust igényel, de a hozzáférési jogkivonat válasza JSON formátumú. |
| AdditionalRequestQueryParameters | No | További lekérdezési paraméterek. Előfordulhat például, hogy további paramétereket szeretne küldeni az identitás-szolgáltatónak. Vesszővel elválasztó használatával több paramétert is megadhat. |
| ClaimsEndpointAccessTokenName | No | A hozzáférési jogkivonat lekérdezési karakterlánc-paraméterének neve. Egyes identitás-szolgáltatók jogcímei végpontok támogatják a HTTP-kérések beolvasását. Ebben az esetben a tulajdonosi jogkivonatot egy lekérdezési karakterlánc paraméterrel kell elküldeni az engedélyezési fejléc helyett. |
| ClaimsEndpointFormatName | No | A Format lekérdezési karakterlánc paraméterének neve. Például megadhatja a nevet `format` ebben a LinkedIn jogcím-végpontban `https://api.linkedin.com/v1/people/~?format=json` . |
| ClaimsEndpointFormat | No | A Format lekérdezési karakterlánc paraméter értéke. Például megadhatja az értéket `json` ebben a LinkedIn jogcím-végpontban `https://api.linkedin.com/v1/people/~?format=json` . |
| ProviderName | No | Az identitás-szolgáltató neve. |
| response_mode | No | Az a metódus, amelyet az Identitáskezelő használ az eredmény Azure AD B2Cba való visszaküldéséhez. Lehetséges értékek: `query` , `form_post` (alapértelmezett) vagy `fragment` . |
| scope | No | A kérelem hatóköre, amely a OAuth2-identitás szolgáltatójának specifikációja szerint van meghatározva. Például: `openid` , `profile` , és `email` . |
| HttpBinding | No | A hozzáférési jogkivonat és a jogcímek jogkivonat-végpontjának várt HTTP-kötése. Lehetséges értékek: `GET` vagy `POST` .  |
| ResponseErrorCodeParamName | No | A HTTP 200 (ok) protokollon keresztül visszaadott hibaüzenetet tartalmazó paraméter neve. |
| ExtraParamsInAccessTokenEndpointResponse | No | Azokat a felesleges paramétereket tartalmazza, amelyeket egyes identitás-szolgáltatók a **AccessTokenEndpoint** válaszában adhatnak vissza. A **AccessTokenEndpoint** válasza például egy további paramétert tartalmaz `openid` , amely egy kötelező paraméter, amely a Access_token mellett egy **ClaimsEndpoint** kérelem lekérdezési karakterláncában szerepel. Több paraméter nevét el kell kerülni és el kell különíteni a vessző "," elválasztóval. |
| ExtraParamsInClaimsEndpointRequest | No | Azokat a felesleges paramétereket tartalmazza, amelyeket egyes identitás-szolgáltatók a **ClaimsEndpoint** -kérelemben adhatnak vissza. Több paraméter nevét el kell kerülni és el kell különíteni a vessző "," elválasztóval. |
| IncludeClaimResolvingInClaimsHandling  | No | A bemeneti és a kimeneti jogcímek esetén megadja, hogy a [jogcímek feloldása](claim-resolver-overview.md) szerepel-e a technikai profilban. Lehetséges értékek: `true` , vagy `false`   (alapértelmezett). Ha a technikai profilban a jogcím-feloldót szeretné használni, állítsa be a következőt: `true` . |
| ResolveJsonPathsInJsonTokens  | No | Azt jelzi, hogy a technikai profil feloldja-e a JSON-útvonalakat. Lehetséges értékek: `true` , vagy `false` (alapértelmezett). A metaadatok használatával beolvashatja az adatokat egy beágyazott JSON-elemből. A [OutputClaim](technicalprofiles.md#outputclaims)állítsa be a `PartnerClaimType` elemet a kimenetként használni kívánt JSON-útvonal elemre. Például: `firstName.localized` , vagy `data.0.to.0.email` .|

## <a name="cryptographic-keys"></a>Titkosítási kulcsok

A **CryptographicKeys** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| client_secret | Yes | Az Identity Provider alkalmazás ügyfél-titka. A titkosítási kulcs csak akkor szükséges, ha a **response_types** metaadatok értéke `code` . Ebben az esetben Azure AD B2C egy másik hívást kezdeményez a hozzáférési token engedélyezési kódjának cseréjéhez. Ha a metaadatok értéke, akkor `id_token` kihagyhatja a titkosítási kulcsot. |

## <a name="redirect-uri"></a>Átirányítási URI

Az Identitáskezelő átirányítási URL-címének konfigurálásakor adja meg a (z `https://login.microsoftonline.com/te/tenant/policyId/oauth2/authresp` ) értéket. Ne felejtse el **lecserélni a bérlő nevét** (például contosob2c.onmicrosoft.com) és a **policyId** a szabályzat azonosítójával (például b2c_1a_policy). Az átirányítási URI-nak minden kisbetűsnek kell lennie.

Ha a **b2clogin.com** tartományt használja a **login.microsoftonline.com** helyett, ügyeljen arra, hogy a login.microsoftonline.com helyett a b2clogin.com használja.

Példák:

- [Google + hozzáadása OAuth2-identitás-szolgáltatóként egyéni szabályzatok használatával](identity-provider-google-custom.md)













