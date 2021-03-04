---
title: OpenID Connect műszaki profil definiálása egyéni házirendben
titleSuffix: Azure AD B2C
description: Definiáljon egy OpenID Connect műszaki profilt egy egyéni szabályzatban Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 670fbeeb006d21e29675f88895018d1a453a1c54
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120300"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>OpenID Connect műszaki profil definiálása egy Azure Active Directory B2C egyéni házirendben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A Azure Active Directory B2C (Azure AD B2C) támogatást nyújt az [OpenID Connect](https://openid.net/2015/04/17/openid-connect-certification-program/) protokoll identitás-szolgáltatójának támogatásához. Az OpenID Connect 1,0 a OAuth 2,0-es számú identitási réteget definiálja, és a modern hitelesítési protokollokban lévő Art állapotot jelöli. Az OpenID Connect technikai profillal összevonása az OpenID Connect-alapú identitás-szolgáltatóval, például az Azure AD-vel. Az egyesítő lehetővé teszi, hogy a felhasználók bejelentkezzenek a meglévő közösségi vagy vállalati identitásokkal.

## <a name="protocol"></a>Protokoll

A **protokoll** elem **Name** attribútumát be kell állítani `OpenIdConnect` . A **MSA-OIDC** technikai profilhoz tartozó protokoll például a következő `OpenIdConnect` :

```xml
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
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

A **OutputClaims** elem tartalmazza az OpenID Connect Identity Provider által visszaadott jogcímek listáját. Előfordulhat, hogy le kell képeznie a szabályzatban definiált jogcím nevét az identitás-szolgáltatóban definiált névre. Olyan jogcímeket is megadhat, amelyeket nem ad vissza az identitás-szolgáltató, ha beállítja az `DefaultValue` attribútumot.

A **OutputClaimsTransformations** elem olyan **OutputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja, amelyek a kimeneti jogcímek módosítására vagy újak előállítására szolgálnak.

A következő példában a Microsoft-fiók identitás-szolgáltatója által visszaadott jogcímek láthatók:

- Az **issuerUserId** jogcímhez hozzárendelt **aljogcím.**
- A **DisplayName** jogcímhez hozzárendelt **név** jogcím.
- Az **e-mail** név leképezése nélkül.

A technikai profil az Identitáskezelő által nem visszaadott jogcímeket is visszaadja:

- Az **identityProvider** -jogcím, amely tartalmazza az identitás-szolgáltató nevét.
- A **authenticationSource** jogcím alapértelmezett **socialIdpAuthentication**-értékkel rendelkezik.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
</OutputClaims>
```

## <a name="metadata"></a>Metaadatok

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| client_id | Igen | Az identitás-szolgáltató alkalmazás-azonosítója. |
| IdTokenAudience | Nem | A id_token célközönsége. Ha meg van adva, Azure AD B2C ellenőrzi, hogy az `aud` identitás-szolgáltató által visszaadott jogkivonatban szereplő jogcím egyenlő-e a IdTokenAudience-metaadatokban megadott értékkel.  |
| METAADATOK | Igen | Egy olyan URL-cím, amely az OpenID Connect Identity Provider konfigurációs dokumentumra mutat, amely az OpenID Well-known Configuration Endpoint néven is ismert. Az URL-cím tartalmazhatja a `{tenant}` kifejezést, amelyet a rendszer a bérlő nevével cserél le.  |
| authorization_endpoint | Nem | Egy olyan URL-cím, amely egy OpenID Connect Identity Provider konfigurációs engedélyezési végpontra mutat. Authorization_endpoint metaadatok értéke elsőbbséget élvez az `authorization_endpoint` OpenID jól ismert konfigurációs végpontjában megadott értékkel. Az URL-cím tartalmazhatja a `{tenant}` kifejezést, amelyet a rendszer a bérlő nevével cserél le. |
| end_session_endpoint | Nem | A végponti munkamenet végpontjának URL-címe Authorization_endpoint metaadatok értéke elsőbbséget élvez az `end_session_endpoint` OpenID jól ismert konfigurációs végpontjában megadott értékkel. |
| kiállító | Nem | Az OpenID Connect-identitás szolgáltatójának egyedi azonosítója. A kiállítói metaadatok értéke elsőbbséget élvez az `issuer` OpenID jól ismert konfigurációs végpontjában megadott értékkel.  Ha meg van adva, Azure AD B2C ellenőrzi, hogy az `iss` identitás-szolgáltató által visszaadott jogkivonatban szereplő jogcím egyenlő-e a kiállítói metaadatokban megadott értékkel. |
| ProviderName | Nem | Az identitás-szolgáltató neve.  |
| response_types | Nem | A válasz típusa az OpenID Connect Core 1,0 specifikációnak megfelelően. Lehetséges értékek: `id_token` , `code` , vagy `token` . |
| response_mode | Nem | Az a metódus, amelyet az Identitáskezelő használ az eredmény Azure AD B2Cba való visszaküldéséhez. Lehetséges értékek: `query` , `form_post` (alapértelmezett) vagy `fragment` . |
| scope | Nem | Az OpenID Connect Core 1,0 specifikáció alapján meghatározott kérelem hatóköre. Például: `openid` , `profile` , és `email` . |
| HttpBinding | Nem | A hozzáférési jogkivonat és a jogcímek jogkivonat-végpontjának várt HTTP-kötése. Lehetséges értékek: `GET` vagy `POST` .  |
| ValidTokenIssuerPrefixes | Nem | Olyan kulcs, amely az egyes bérlők számára való bejelentkezéshez használható több-bérlős identitás-szolgáltató, például Azure Active Directory használata esetén. |
| UsePolicyInRedirectUri | Nem | Azt jelzi, hogy az átirányítási URI létrehozásakor szabályzatot kell-e használni. Ha az alkalmazást az identitás-szolgáltatóban konfigurálja, meg kell adnia az átirányítási URI-t. Az átirányítási URI a következőre mutat: Azure AD B2C `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp` .  Ha megadja `true` , minden használt szabályzathoz hozzá kell adnia egy átirányítási URI-t. Példa: `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/{policy-name}/oauth2/authresp`. |
| MarkAsFailureOnStatusCode5xx | Nem | Azt jelzi, hogy egy külső szolgáltatásra irányuló kérést hibaként kell-e megjelölni, ha a http-állapotkód a 5xx tartományban van. A mező alapértelmezett értéke: `false`. |
| DiscoverMetadataByTokenIssuer | Nem | Azt jelzi, hogy a OIDC metaadatait fel kell-e deríteni a JWT jogkivonat kiállítójának használatával. |
| IncludeClaimResolvingInClaimsHandling  | Nem | A bemeneti és a kimeneti jogcímek esetén megadja, hogy a [jogcímek feloldása](claim-resolver-overview.md) szerepel-e a technikai profilban. Lehetséges értékek: `true` , vagy `false` (alapértelmezett). Ha a technikai profilban a jogcím-feloldót szeretné használni, állítsa be a következőt: `true` . |
| token_endpoint_auth_method | Nem | Meghatározza, hogy a Azure AD B2C hogyan küldi el a hitelesítési fejlécet a jogkivonat-végpontnak. Lehetséges értékek: `client_secret_post` (alapértelmezett) és `client_secret_basic` (nyilvános előzetes verzió). További információ: [OpenID Connect ügyfél-hitelesítés szakasz](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication). |
| token_signing_algorithm | Nem | Az ügyfél-kijelentésekhez használt aláírási algoritmus, amikor a **token_endpoint_auth_method** metaadatok be vannak állítva `private_key_jwt` . Lehetséges értékek: `RS256` (alapértelmezett). |
| SingleLogoutEnabled | Nem | Azt jelzi, hogy a technikai profilba való bejelentkezés során a rendszer megpróbál-e kijelentkezni az összevont identitás-szolgáltatókról. További információ: Azure AD B2C- [munkamenet](./session-behavior.md#sign-out)kijelentkezése.  Lehetséges értékek: `true` (alapértelmezett) vagy `false` . |
|ReadBodyClaimsOnIdpRedirect| Nem| Állítsa be a (z) értékre, `true` Ha olvasási jogcímeket szeretne olvasni az Identitáskezelő átirányításához. Ezeket a metaadatokat az [Apple ID](identity-provider-apple-id.md)-vel együtt használják, ahol a jogcímek a válasz adattartalomban térnek vissza.|

```xml
<Metadata>
  <Item Key="ProviderName">https://login.live.com</Item>
  <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
  <Item Key="response_types">code</Item>
  <Item Key="response_mode">form_post</Item>
  <Item Key="scope">openid profile email</Item>
  <Item Key="HttpBinding">POST</Item>
  <Item Key="UsePolicyInRedirectUri">false</Item>
  <Item Key="client_id">Your Microsoft application client ID</Item>
</Metadata>
```

### <a name="ui-elements"></a>Felhasználói felület elemei
 
A következő beállításokkal megadhatja a hiba esetén megjelenő hibaüzenetet. A metaadatokat az OpenID Connect műszaki profiljában kell konfigurálni. A hibaüzenetek [honosítható](localization-string-ids.md#sign-up-or-sign-in-error-messages).

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalDoesNotExist | Nem | A felhasználónak megjelenítendő üzenet, ha a megadott felhasználónévvel rendelkező fiók nem található a címtárban. |
| UserMessageIfInvalidPassword | Nem | A felhasználónak megjelenítendő üzenet, ha a jelszó helytelen. |
| UserMessageIfOldPasswordUsed| Nem |  A felhasználó számára a régi jelszó használatakor megjelenítendő üzenet.|

## <a name="cryptographic-keys"></a>Titkosítási kulcsok

A **CryptographicKeys** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| client_secret | Igen | Az Identity Provider alkalmazás ügyfél-titka. Erre a titkosítási kulcsra csak akkor van szükség, ha a **response_types** metaadata be van állítva, `code` és **token_endpoint_auth_method** értéke `client_secret_post` vagy `client_secret_basic` . Ebben az esetben Azure AD B2C egy másik hívást kezdeményez a hozzáférési token engedélyezési kódjának cseréjéhez. Ha a metaadatok értéke, akkor `id_token` kihagyhatja a titkosítási kulcsot.  |
| assertion_signing_key | Igen | Az ügyfél-érvényesítés aláírására szolgáló RSA titkos kulcs. Erre a titkosítási kulcsra csak akkor van szükség, ha a **token_endpoint_auth_method** metaadatok értéke `private_key_jwt` . |

## <a name="redirect-uri"></a>Átirányítási URI

Ha az Identitáskezelő átirányítási URI-JÁT konfigurálja, írja be a (z `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp` ) értéket. Ne felejtse el lecserélni a `{your-tenant-name}` bérlő nevét. Az átirányítási URI-nak minden kisbetűsnek kell lennie.

Angol nyelvű Példák:

- [Microsoft-fiók (MSA) hozzáadása identitás-szolgáltatóként egyéni szabályzatok használatával](identity-provider-microsoft-account.md)
- [Bejelentkezés Azure AD-fiókok használatával](identity-provider-azure-ad-single-tenant.md)
- [Lehetővé teszi, hogy a felhasználók egyéni szabályzatok használatával jelentkezzenek be egy több-bérlős Azure AD-identitás-szolgáltatóba](identity-provider-azure-ad-multi-tenant.md)
