---
title: Technikai profil definiálása egy JWT-kiállítóhoz egyéni szabályzatban
titleSuffix: Azure AD B2C
description: Technikai profil definiálása egy JSON webes jogkivonat-(JWT-) kiállítóhoz a Azure Active Directory B2C egyéni házirendjében.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4faa7e68b50b83368837b75cd04be566d816f6d3
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102119807"
---
# <a name="define-a-technical-profile-for-a-jwt-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>JWT jogkivonat-kiállító technikai profiljának meghatározása egy Azure Active Directory B2C egyéni házirendben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) számos különböző típusú biztonsági jogkivonatot bocsát ki, mivel az egyes hitelesítési folyamatokat dolgozza fel. A JWT jogkivonat-kiállító technikai profilja olyan JWT-jogkivonatot bocsát ki, amely vissza lesz visszaadva a függő entitás alkalmazásának. Ez a technikai profil általában a felhasználói út utolsó előkészítési lépése.

## <a name="protocol"></a>Protokoll

A **protokoll** elem **Name** attribútumát be kell állítani `OpenIdConnect` . Állítsa a **OutputTokenFormat** elemet a következőre: `JWT` .

A következő példa egy technikai profilt mutat be `JwtIssuer` :

```xml
<TechnicalProfile Id="JwtIssuer">
  <DisplayName>JWT Issuer</DisplayName>
  <Protocol Name="OpenIdConnect" />
  <OutputTokenFormat>JWT</OutputTokenFormat>
  <Metadata>
    <Item Key="client_id">{service:te}</Item>
    <Item Key="issuer_refresh_token_user_identity_claim_type">objectId</Item>
    <Item Key="SendTokenResponseBodyWithJsonNumbers">true</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
    <Key Id="issuer_refresh_token_key" StorageReferenceId="B2C_1A_TokenEncryptionKeyContainer" />
  </CryptographicKeys>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-jwt-issuer" />
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Bemeneti, kimeneti és megőrzési jogcímek

A **szabályzattípushoz**, a **OutputClaims** és a **PersistClaims** elemek üresek vagy hiányoznak. A **InutputClaimsTransformations** és a **OutputClaimsTransformations** elemek is hiányoznak.

## <a name="metadata"></a>Metaadatok

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| issuer_refresh_token_user_identity_claim_type | Igen | Az a jogcím, amelyet felhasználói identitási jogcímként kell használni a OAuth2-engedélyezési kódokban és a jogkivonatok frissítésében. Alapértelmezés szerint a értékre kell állítani `objectId` , ha nem ad meg másik SubjectNamingInfo-jogcím-típust. |
| SendTokenResponseBodyWithJsonNumbers | Nem | Mindig állítsa be a következőt: `true` . Olyan örökölt formátum esetén, ahol a numerikus értékek JSON-számok helyett karakterláncként vannak megadva, a következőre: `false` . Ez az attribútum olyan ügyfelek esetében szükséges, akik egy korábbi implementációtól függenek, amely a tulajdonságokat karakterláncként adja vissza. |
| token_lifetime_secs | Nem | Hozzáférési jogkivonat élettartama. A védett erőforrásokhoz való hozzáféréshez használt OAuth 2,0 tulajdonosi jogkivonat élettartama. Az alapértelmezett érték 3 600 másodperc (1 óra). A minimális (inkluzív) érték 300 másodperc (5 perc). A maximális érték 86 400 másodperc (24 óra). |
| id_token_lifetime_secs | Nem | AZONOSÍTÓ jogkivonat élettartama. Az alapértelmezett érték 3 600 másodperc (1 óra). A minimális (inkluzív) érték 300 másodperc (5 perc). A maximális (inkluzív) érték 86 400 (24 óra). |
| refresh_token_lifetime_secs | Nem | A jogkivonat élettartamának frissítése. Az a maximális időtartam, ameddig egy frissítési jogkivonat felhasználható új hozzáférési jogkivonat beszerzéséhez, ha az alkalmazás megkapta a offline_access hatókört. Az alapértelmezett érték 120, 9600 másodperc (14 nap). A minimális (inkluzív) érték 86 400 másodperc (24 óra). A maximális érték 7 776 000 másodperc (90 nap). |
| rolling_refresh_token_lifetime_secs | Nem | A jogkivonat-toló ablak élettartamának frissítése. Az időszak lejárta után a felhasználónak újra hitelesítenie kell magát, függetlenül attól, hogy milyen érvényességi időszakra van az alkalmazás által beszerzett legutóbbi frissítési jogkivonat. Ha nem szeretné kényszeríteni a csúszó ablak élettartamát, állítsa a allow_infinite_rolling_refresh_token értékét a következőre: `true` . Az alapértelmezett érték 7 776 000 másodperc (90 nap). A minimális (inkluzív) érték 86 400 másodperc (24 óra). A maximális érték 31 536 000 másodperc (365 nap). |
| allow_infinite_rolling_refresh_token | Nem | Ha a értékre van állítva `true` , a jogkivonat-lecsúszó ablak élettartama soha nem jár le. |
| IssuanceClaimPattern | Nem | A kiállítói (ISS) jogcímet vezérli. Az értékek egyike:<ul><li>AuthorityAndTenantGuid – az ISS-jogcím magában foglalja a tartománynevet (például vagy), `login.microsoftonline` `tenant-name.b2clogin.com` valamint a bérlői azonosítóját (https: \/ /login.microsoftonline.com/00000000-0000-0000-0000-000000000000/v2.0/)</li><li>AuthorityWithTfp – az ISS-jogcím magában foglalja a tartománynevet, `login.microsoftonline` például `tenant-name.b2clogin.com` a vagy a bérlői azonosítóját, valamint a függő entitás házirendjének nevét. https: \/ /login.microsoftonline.com/TFP/00000000-0000-0000-0000-000000000000/b2c_1a_tp_sign-up-or-Sign-in/v2.0/</li></ul> Alapértelmezett érték: AuthorityAndTenantGuid |
| AuthenticationContextReferenceClaimPattern | Nem | A `acr` jogcím értékének szabályozása.<ul><li>Nincs – Azure AD B2C nem adja ki az ACR-jogcímet</li><li>PolicyId – a `acr` jogcím tartalmazza a szabályzat nevét.</li></ul>Az érték beállításának beállításai a következők: TFP (megbízhatósági keretrendszer házirendje) és ACR (hitelesítési környezet referenciája). Azt javasoljuk, hogy ezt az értéket TFP adja meg az érték beállításához, és győződjön meg arról, hogy a `<Item>` `Key="AuthenticationContextReferenceClaimPattern"` létezik és az érték `None` . A függő entitás házirendjében vegyen fel egy `<OutputClaims>` elemet, és adja hozzá ezt az elemet `<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />` . Győződjön meg arról is, hogy a szabályzat tartalmazza a jogcím típusát `<ClaimType Id="trustFrameworkPolicy">   <DisplayName>trustFrameworkPolicy</DisplayName>     <DataType>string</DataType> </ClaimType>` |
|RefreshTokenUserJourneyId| Nem | Egy felhasználói út azonosítója, amelyet a [hozzáférési jogkivonat frissítése](authorization-code-flow.md#4-refresh-the-token) a végpontra történő frissítéskor kell végrehajtani `/token` . |

## <a name="cryptographic-keys"></a>Titkosítási kulcsok

A CryptographicKeys elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| issuer_secret | Igen | A JWT-token aláírásához használt X509-tanúsítvány (RSA-kulcs). Ezt a `B2C_1A_TokenSigningKeyContainer` kulcsot konfigurálja az [Egyéni házirendek használatába](custom-policy-get-started.md). |
| issuer_refresh_token_key | Igen | A frissítési jogkivonat titkosításához használt X509-tanúsítvány (RSA-kulcs). A kulcsot az `B2C_1A_TokenEncryptionKeyContainer` [Egyéni szabályzatok első lépéseiben](custom-policy-get-started.md) konfigurálta. |

## <a name="session-management"></a>Munkamenet-kezelés

A Azure AD B2C és a függő entitások közötti Azure AD B2C munkamenetek konfigurálásához a elem attribútumában `UseTechnicalProfileForSessionManagement` adjon hozzá egy hivatkozást a [OAuthSSOSessionProvider](custom-policy-reference-sso.md#oauthssosessionprovider) SSO-munkamenethez.














