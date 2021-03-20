---
title: AZONOSÍTÓ jogkivonat-emlékeztető technikai profiljának definiálása egyéni házirendben
titleSuffix: Azure AD B2C
description: Adjon meg egy azonosító jogkivonat-emlékeztető technikai profilt a Azure Active Directory B2C egyéni házirendjében.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d77e145cabcef2931d5fe6e76599da7931e576e8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97669159"
---
# <a name="define-an-id-token-hint-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>AZONOSÍTÓ jogkivonat-útmutató technikai profiljának definiálása egy Azure Active Directory B2C egyéni házirendben

Azure AD B2C lehetővé teszi, hogy a függő entitások egy bejövő JWT küldjenek a OAuth2-engedélyezési kérelem részeként. Az JWT-tokent egy függő entitás vagy egy identitás-szolgáltató is kiállíthatja, és a felhasználóra vagy az engedélyezési kérelemre vonatkozó tippet adhat át. Azure AD B2C érvényesíti az aláírást, a kiállító nevét és a jogkivonat célközönségét, és kibontja a jogcímet a bejövő jogkivonatból.

## <a name="use-cases"></a>Használati esetek

Ezt a megoldást használhatja arra, hogy a rendszer egyetlen JWT-tokenbe ágyazza az adatküldés Azure AD B2C. A [Feliratkozás e-mailben meghívásos megoldásával](https://github.com/azure-ad-b2c/samples/blob/master/policies/invite/README.md), ahol a rendszergazda elküldheti a felhasználók számára az aláírt meghívót, id_token_hinton alapul. Csak a meghívó e-mail-hozzáféréssel rendelkező felhasználók hozhatnak létre fiókot a címtárban.

## <a name="token-signing-approach"></a>Jogkivonat-aláírási módszer

A id_token_hint a jogkivonat kiállítója (egy függő entitás vagy egy identitás-szolgáltató) alkotja a jogkivonatot, majd aláírja azt egy aláíró kulccsal, hogy igazolja, hogy a jogkivonat megbízható forrásból származik. Az aláíró kulcs szimmetrikus vagy aszimmetrikus lehet. A szimmetrikus titkosítás vagy a titkos kulcs titkosítása közös titkos kulcsot használ az aláírás aláírására és érvényesítésére. Az aszimmetrikus titkosítás vagy a nyilvános kulcsú titkosítás egy olyan titkosítási rendszer, amely egy titkos kulcsot és egy nyilvános kulcsot is használ. A titkos kulcsot csak a jogkivonat kiállítója ismeri, és a rendszer a jogkivonat aláírására használja. A nyilvános kulcsot a rendszer megosztja a Azure AD B2C házirenddel a jogkivonat aláírásának ellenőrzéséhez.

## <a name="token-format"></a>Jogkivonat formátuma

A id_token_hintnak érvényes JWT-tokennek kell lennie. A következő táblázat felsorolja a kötelező jogcímeket. További jogcímek megadása nem kötelező.

| Name | Jogcím | Példaérték | Description |
| ---- | ----- | ------------- | ----------- |
| Célközönség | `aud` | `a489fc44-3cc0-4a78-92f6-e413cd853eae` | Azonosítja a jogkivonat kívánt címzettjét. A célközönség a jogkivonat-kiállító által definiált tetszőleges karakterlánc. Azure AD B2C érvényesíti ezt az értéket, és elutasítja a tokent, ha az nem egyezik.  |
| Kiállító | `iss` |`https://localhost` | Azonosítja a biztonsági jogkivonat szolgáltatást (jogkivonat-kiállítót). A kiállító a jogkivonat-kiállító által definiált tetszőleges URI. Azure AD B2C érvényesíti ezt az értéket, és elutasítja a tokent, ha az nem egyezik.  |
| Lejárati idő | `exp` | `1600087315` | Az az idő, amikor a jogkivonat érvénytelenné válik, és ez a korszak időpontjában jelenik meg. Azure AD B2C érvényesíti ezt az értéket, és elutasítja a tokent, ha a jogkivonat lejárt.|
| Nem előtte | `nbf` | `1599482515` | Az az idő, amikor a jogkivonat érvényes lesz, és az időpontokban jelenik meg. Ez az idő általában megegyezik a jogkivonat kiadásának időpontjával. Azure AD B2C érvényesíti ezt az értéket, és elutasítja a tokent, ha a jogkivonat élettartama érvénytelen. |

 Az alábbi jogkivonat egy érvényes azonosító tokent mutat be:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "displayName": " John Smith",
  "userId": "john.s@contoso.com",
  "nbf": 1599482515,
  "exp": 1600087315,
  "iss": "https://localhost",
  "aud": "a489fc44-3cc0-4a78-92f6-e413cd853eae"
}
```

## <a name="protocol"></a>Protokoll

A **protokoll** elem **Name** attribútumát be kell állítani `None` . A **IdTokenHint_ExtractClaims** technikai profilhoz tartozó protokoll például a következő `None` :

```xml
<TechnicalProfile Id="IdTokenHint_ExtractClaims">
  <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
  <Protocol Name="None" />
  ...
```

A technikai profilt a következő típusú előkészítési lépéssel hívja meg: `GetClaims` .

```xml
<OrchestrationStep Order="1" Type="GetClaims" CpimIssuerTechnicalProfileReferenceId="IdTokenHint_ExtractClaims" />
``` 

## <a name="output-claims"></a>Kimeneti jogcímek

A **OutputClaims** elem tartalmazza a JWT-tokenből kinyerhető jogcímek listáját. Előfordulhat, hogy a szabályzatban definiált jogcím nevét le kell képeznie a JWT-jogkivonatban definiált névre. Olyan jogcímeket is tartalmazhat, amelyeket nem ad vissza a JWT-jogkivonat, feltéve, hogy az `DefaultValue` attribútumot beállítja.

## <a name="metadata"></a>Metaadatok

A szimmetrikus kulcs használatakor a következő metaadatok érvényesek. 

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| kiállító | Yes | Azonosítja a biztonsági jogkivonat szolgáltatást (jogkivonat-kiállítót). Ennek az értéknek meg kell egyeznie a `iss` jogcímek JWT jogkivonat-jogcímen belül. | 
| IdTokenAudience | Yes | Azonosítja a jogkivonat kívánt címzettjét. Meg kell egyeznie a `aud` jogcímek JWT-jogkivonat jogcímen belüli jogcímevel. | 

Aszimmetrikus kulcs használata esetén a következő metaadatok érvényesek. 

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| METAADATOK| Yes | Egy olyan URL-cím, amely egy jogkivonat-kiállítói konfigurációs dokumentumra mutat, amely az OpenID Well-known Configuration Endpoint néven is ismert.   |
| kiállító | No | Azonosítja a biztonsági jogkivonat szolgáltatást (jogkivonat-kiállítót). Ez az érték használható a metaadatokban konfigurált érték felülírására, és meg kell egyeznie a `iss` jogcímek JWT jogkivonat-jogcímen belüli jogcímevel. |  
| IdTokenAudience | No | Azonosítja a jogkivonat kívánt címzettjét. Meg kell egyeznie a `aud` jogcímek JWT-jogkivonat jogcímen belüli jogcímevel. |  

## <a name="cryptographic-keys"></a>Titkosítási kulcsok

Szimmetrikus kulcs használatakor a **CryptographicKeys** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| client_secret | Yes | Az JWT-jogkivonat aláírásának ellenőrzéséhez használt kriptográfiai kulcs.|


## <a name="how-to-guide"></a>Útmutató

### <a name="issue-a-token-with-symmetric-keys"></a>Token kibocsátása szimmetrikus kulcsokkal

#### <a name="step-1-create-a-shared-key"></a>1. lépés Megosztott kulcs létrehozása 

Hozzon létre egy kulcsot, amely használható a jogkivonat aláírására. A kulcs létrehozásához például használja a következő PowerShell-kódot.

```powershell
$bytes = New-Object Byte[] 32
$rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
$rand.GetBytes($bytes)
$rand.Dispose()
$newClientSecret = [System.Convert]::ToBase64String($bytes)
$newClientSecret
```

Ez a kód egy titkos karakterláncot hoz létre, például: `VK62QTn0m1hMcn0DQ3RPYDAr6yIiSvYgdRwjZtU5QhI=` .

#### <a name="step-2-add-the-signing-key-to-azure-ad-b2c"></a>2. lépés Az aláíró kulcs hozzáadása a Azure AD B2C

Ugyanazt a kulcsot kell létrehoznia, amelyet a jogkivonat-kiállító használ a Azure AD B2C szabályzat kulcsaiban.  

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A Azure Portal keresse meg és válassza a **Azure ad B2C** lehetőséget.
1. Az Áttekintés lap **szabályzatok** területén válassza az **identitási élmény keretrendszere** elemet.
1. **Házirend-kulcsok** kiválasztása 
1. Válassza a **manuális** lehetőséget.
1. A **név mezőben** használja a következőt: `IdTokenHintKey` .  
   Lehet, hogy az előtag `B2C_1A_` automatikusan hozzá lesz adva.
1. A **titok** mezőben adja meg a korábban létrehozott bejelentkezési kulcsot.
1. A **kulcshasználat** esetében használja a **titkosítást**.
1. Válassza a **Létrehozás** lehetőséget.
1. Erősítse meg, hogy létrehozta a kulcsot `B2C_1A_IdTokenHintKey` .


#### <a name="step-3-add-the-id-token-hint-technical-profile"></a>3. lépés Az ID jogkivonat-útmutató technikai profiljának hozzáadása

A következő technikai profil ellenőrzi a jogkivonatot, és Kinyeri a jogcímeket. 

```xml
<ClaimsProvider>
  <DisplayName>My ID Token Hint ClaimsProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="IdTokenHint_ExtractClaims">
      <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
      <Protocol Name="None" />
      <Metadata>
        <Item Key="IdTokenAudience">a489fc44-3cc0-4a78-92f6-e413cd853eae</Item>
        <Item Key="issuer">https://localhost</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_IdTokenHintKey" />
      </CryptographicKeys>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="step-4-prepare-your-policy"></a>4. lépés: A szabályzat előkészítése

Fejezze be a [házirend konfigurálása](#configure-your-policy) lépést.

#### <a name="step-5-prepare-the-code"></a>5. lépés A kód előkészítése  

A [GitHub-minta](https://github.com/azure-ad-b2c/id_token_hint/tree/master/dotnet_core_symmetric_key) egy ASP.net-webalkalmazás és-konzol alkalmazás, amely egy szimmetrikus kulccsal aláírt azonosító jogkivonatot hoz létre. 


### <a name="issue-a-token-with-asymmetric-keys"></a>Token kibocsátása aszimmetrikus kulccsal

Aszimmetrikus kulccsal a jogkivonat RSA-tanúsítványokkal van aláírva. Ez az alkalmazás egy Open ID-kapcsolat metaadat-végpontját és JSON-webkulcsokat (JWKs) futtató végpontot üzemeltet, amelyet a Azure AD B2C használ az azonosító jogkivonat aláírásának ellenőrzéséhez.

A jogkivonat kiállítójának a következő végpontokat kell megadnia:

* `/.well-known/openid-configuration` – Egy jól ismert konfigurációs végpont, amely a jogkivonattal kapcsolatos információkat, például a jogkivonat kiállítójának nevét és a JWK-végpontra mutató hivatkozást mutat be. 
* `/.well-known/keys` – a JSON webkulcs (JWK) végpontja a kulcs aláírásához használt nyilvános kulccsal (a tanúsítvány titkos kulcsával).

Tekintse meg a [TokenMetadataController. cs](https://github.com/azure-ad-b2c/id-token-builder/blob/master/source-code/B2CIdTokenBuilder/Controllers/TokenMetadataController.cs) .net MVC vezérlő mintát.

#### <a name="step-1-prepare-a-self-signed-certificate"></a>1. lépés Önaláírt tanúsítvány előkészítése

Ha még nem rendelkezik tanúsítvánnyal, akkor ehhez az útmutatóhoz önaláírt tanúsítványt is használhat. Windows rendszeren a PowerShell [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) parancsmag segítségével hozhatja elő a tanúsítványt.

Futtassa ezt a PowerShell-parancsot egy önaláírt tanúsítvány létrehozásához. Módosítsa az `-Subject` argumentumot megfelelően az alkalmazáshoz, és Azure ad B2C a bérlő nevét. A dátumot úgy is beállíthatja `-NotAfter` , hogy a tanúsítványhoz eltérő lejáratot határozzon meg.

```PowerShell
New-SelfSignedCertificate `
    -KeyExportPolicy Exportable `
    -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
    -KeyAlgorithm RSA `
    -KeyLength 2048 `
    -KeyUsage DigitalSignature `
    -NotAfter (Get-Date).AddMonths(12) `
    -CertStoreLocation "Cert:\CurrentUser\My"
```


#### <a name="step-2-add-the-id-token-hint-technical-profile"></a>2. lépés Az ID jogkivonat-útmutató technikai profiljának hozzáadása 

A következő technikai profil ellenőrzi a jogkivonatot, és Kinyeri a jogcímeket. Módosítsa a metaadatok URI-JÁT a jogkivonat kiállítója jól ismert konfigurációs végpontra.  

```xml
<ClaimsProvider>
  <DisplayName>My ID Token Hint ClaimsProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="IdTokenHint_ExtractClaims">
      <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
      <Protocol Name="None" />
      <Metadata>
        <!-- Replace with your endpoint location -->
        <Item Key="METADATA">https://your-app.azurewebsites.net/.well-known/openid-configuration</Item>
        <Item Key="IdTokenAudience">your_optional_audience</Item> -->
        <!-- <Item Key="issuer">your_optional_token_issuer_override</Item> -->
      </Metadata>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="step-3-prepare-your-policy"></a>3. lépés A szabályzat előkészítése

Fejezze be a [házirend konfigurálása](#configure-your-policy) lépést.

#### <a name="step-4-prepare-the-code"></a>4. lépés: A kód előkészítése 

Ez a [GitHub-minta](https://github.com/azure-ad-b2c/id-token-builder) ASP.net WEBalkalmazás azonosító jogkivonatokat hoz létre, és azokat a metaadat-végpontokat üzemelteti, amelyek szükségesek a "id_token_hint" paraméter használatához Azure ad B2C.


### <a name="configure-your-policy"></a>A házirend konfigurálása

A szimmetrikus és az aszimmetrikus megközelítés esetében a `id_token_hint` technikai profilt egy előkészítési lépéssel kell meghívni, `GetClaims` és meg kell adnia a függő entitás házirendjének bemeneti jogcímeit.

1. Adja hozzá a IdTokenHint_ExtractClaims technikai profilt a bővítmény házirendjéhez.
1. Adja hozzá a következő előkészítési lépést a felhasználói útra az első elemként.  

    ```xml
    <OrchestrationStep Order="1" Type="GetClaims" CpimIssuerTechnicalProfileReferenceId="IdTokenHint_ExtractClaims" />
    ``` 
1. A függő entitás házirendjében ismételje meg ugyanazokat a bemeneti jogcímeket, amelyeket a IdTokenHint_ExtractClaims technikai profilban konfigurált. Például:
    ```xml
   <RelyingParty>
     <DefaultUserJourney ReferenceId="SignUp" />
     <TechnicalProfile Id="PolicyProfile">
       <DisplayName>PolicyProfile</DisplayName>
       <Protocol Name="OpenIdConnect" />
       <InputClaims>
         <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
        </InputClaims>
       <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
       </OutputClaims>
       <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

Az üzleti igényektől függően szükség lehet a jogkivonat-érvényesítések hozzáadására, például az e-mail-cím formátumának ellenőrzéséhez. Ehhez vegyen fel egy, a [jogcím-átalakítással kapcsolatos technikai profilt](claims-transformation-technical-profile.md)meghívó előkészítési lépést. Vegyen fel egy [önjelölt technikai profilt](self-asserted-technical-profile.md) is, amely egy hibaüzenetet jelenít meg. 

### <a name="create-and-sign-a-token"></a>Token létrehozása és aláírása

A GitHub-minták bemutatják, hogyan hozhat létre ilyen tokent olyan JWT, amelyet később `id_token_hint` lekérdezési karakterlánc paraméterként küldött. A következő példa egy id_token_hint paraméterrel rendelkező engedélyezési kérelemre mutat.
 
```html
https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin/oauth2/v2.0/authorize?client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&id_token_hint=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkaXNwbGF5TmFtZSI6IiBKb2huIFNtaXRoIiwidXNlcklkIjoiam9obi5zQGNvbnRvc28uY29tIiwibmJmIjoxNTk5NDgyNTE1LCJleHAiOjE2MDAwODczMTUsImlzcyI6Imh0dHBzOi8vbG9jYWxob3N0IiwiYXVkIjoiYTQ4OWZjNDQtM2NjMC00YTc4LTkyZjYtZTQxM2NkODUzZWFlIn0.nPmLXydI83PQCk5lRBYUZRu_aX58pL1khahHyQuupig
```

## <a name="next-steps"></a>Következő lépések

- A Azure AD B2C Community GitHub-tárházban keresse meg a [Feliratkozás a meghívó e-mail](https://github.com/azure-ad-b2c/samples/blob/master/policies/invite/README.md) megoldással.
