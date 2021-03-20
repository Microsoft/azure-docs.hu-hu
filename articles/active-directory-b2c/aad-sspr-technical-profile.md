---
title: Azure AD-SSPR – technikai profilok egyéni házirendekben
titleSuffix: Azure AD B2C
description: Egyéni házirend-hivatkozás az Azure AD SSPR műszaki profiljaihoz a Azure AD B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3e6fcf956639d827a8654c5ee80e7cab8cadf930
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85383597"
---
# <a name="define-an-azure-ad-sspr-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Azure AD SSPR műszaki profil definiálása egy Azure AD B2C egyéni házirendben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A Azure Active Directory B2C (Azure AD B2C) támogatást nyújt az önkiszolgáló jelszó-visszaállítás (SSPR) e-mail-címének ellenőrzéséhez. Az Azure AD SSPR műszaki profiljának használatával kódot hozhatja és küldhet e-mail-címre, majd ellenőrizheti a kódot. Az Azure AD SSPR technikai profilja szintén hibaüzenetet adhat vissza. Az érvényesítési technikai profil ellenőrzi a felhasználó által megadott, a felhasználói utazás előtt megjelenő adatmennyiséget. Az érvényesítési technikai profillal egy önérvényesített oldalon egy hibaüzenet jelenik meg.

Ez a technikai profil:

- Nem biztosít felületet a felhasználóval való kommunikációhoz. Ehelyett a felhasználói felületet egy [önérvényesített](self-asserted-technical-profile.md) technikai profilból, vagy egy [megjelenítési vezérlőként](display-controls.md) kell meghívni [érvényesítési technikai profilként](validation-technical-profile.md).
- Az Azure AD SSPR szolgáltatás használatával kódot hoz majd egy e-mail-címre, majd ellenőrzi a kódot.  
- Ellenőrző kóddal érvényesíti az e-mail-címet.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protokoll

A **protokoll** elem **Name** attribútumát be kell állítani `Proprietary` . A **kezelő** attribútumnak tartalmaznia kell az Azure ad B2C által használt protokollkezelő-szerelvény teljesen minősített nevét:

```
Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

Az alábbi példa egy Azure AD SSPR-technikai profilt mutat be:

```XML
<TechnicalProfile Id="AadSspr-SendCode">
  <DisplayName>Send Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-email"></a>E-mail küldése

Ennek a technikai profilnak az első módja egy kód létrehozása és elküldése. Ehhez a módhoz a következő beállítások konfigurálhatók.

### <a name="input-claims"></a>Bemeneti jogcímek

A **szabályzattípushoz** elem tartalmazza az Azure ad-SSPR küldendő jogcímek listáját. A jogcím nevét a SSPR technikai profilban definiált névre is leképezheti.

| ClaimReferenceId | Kötelező | Leírás |
| --------- | -------- | ----------- |
| emailAddress | Yes | Az e-mail-címet birtokló felhasználó azonosítója. A `PartnerClaimType` bemeneti jogcím tulajdonságát a következőre kell beállítani: `emailAddress` . |


A **InputClaimsTransformations** elem olyan **InputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja, amelyek a bemeneti jogcímek módosítására vagy újak előállítására szolgálnak az Azure ad SSPR szolgáltatásba való küldés előtt.

### <a name="output-claims"></a>Kimeneti jogcímek

Az Azure AD SSPR protokoll szolgáltatója nem ad vissza **OutputClaims**, így nem kell megadnia a kimeneti jogcímeket. Megadhat azonban olyan jogcímeket is, amelyeket nem ad vissza az Azure AD SSPR protokoll szolgáltatója, feltéve, hogy az `DefaultValue` attribútumot beállította.

A **OutputClaimsTransformations** elem olyan **OutputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja, amelyek a kimeneti jogcímek módosítására vagy újak előállítására szolgálnak.

### <a name="metadata"></a>Metaadatok

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Művelet | Yes | **SendCode** kell lennie.  |

#### <a name="ui-elements"></a>Felhasználói felület elemei

A következő metaadatokkal konfigurálhatja az SMS-hibák küldésekor megjelenő hibaüzeneteket. A metaadatokat az [önérvényesített](self-asserted-technical-profile.md) technikai profilban kell konfigurálni. A hibaüzenetek [honosítható](localization-string-ids.md#azure-ad-sspr).

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| UserMessageIfInternalError | No | Felhasználói hibaüzenet, ha a kiszolgáló belső hibát észlelt. |
| UserMessageIfThrottled| No | Felhasználói hibaüzenet, ha a kérelem szabályozása megtörtént.|


### <a name="example-send-an-email"></a>Példa: e-mail küldése

Az alábbi példa egy Azure AD SSPR-technikai profilt mutat be, amely e-mailben küld egy kódot.

```XML
<TechnicalProfile Id="AadSspr-SendCode">
  <DisplayName>Send Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">SendCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress"/>
  </InputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Kód ellenőrzése

A technikai profil második módja egy kód ellenőrzése. Ehhez a módhoz a következő beállítások konfigurálhatók.

### <a name="input-claims"></a>Bemeneti jogcímek

A **szabályzattípushoz** elem tartalmazza az Azure ad-SSPR küldendő jogcímek listáját. A jogcím nevét a SSPR technikai profilban definiált névre is leképezheti.

| ClaimReferenceId | Kötelező | Leírás |
| --------- | -------- | ----------- | ----------- |
| emailAddress| Yes | Ugyanazt az e-mail-címet használja, mint korábban a kód küldéséhez. A rendszer az e-mailek ellenőrzési munkamenetének megkeresésére is használja. A `PartnerClaimType` bemeneti jogcím tulajdonságát a következőre kell beállítani: `emailAddress` .|
| verificationCode  | Yes | A felhasználó által megadott ellenőrző kód ellenőrzése. A `PartnerClaimType` bemeneti jogcím tulajdonságát a következőre kell beállítani: `verificationCode` . |

A **InputClaimsTransformations** elem olyan **InputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja, amelyek a bemeneti jogcímek módosítására vagy újak létrehozására szolgálnak az Azure ad SSPR szolgáltatás meghívása előtt.

### <a name="output-claims"></a>Kimeneti jogcímek

Az Azure AD SSPR protokoll szolgáltatója nem ad vissza **OutputClaims**, így nem kell megadnia a kimeneti jogcímeket. Megadhat azonban olyan jogcímeket is, amelyeket nem ad vissza az Azure AD SSPR protokoll szolgáltatója, feltéve, hogy az `DefaultValue` attribútumot beállította.

A **OutputClaimsTransformations** elem olyan **OutputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja, amelyek a kimeneti jogcímek módosítására vagy újak előállítására szolgálnak.

### <a name="metadata"></a>Metaadatok

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Művelet | Yes | **VerifyCode** kell lennie |

#### <a name="ui-elements"></a>Felhasználói felület elemei

A következő metaadatokkal konfigurálhatja a kód-ellenőrzési hiba esetén megjelenő hibaüzeneteket. A metaadatokat az [önérvényesített](self-asserted-technical-profile.md) technikai profilban kell konfigurálni. A hibaüzenetek [honosítható](localization-string-ids.md#azure-ad-sspr).

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
|UserMessageIfChallengeExpired | A felhasználónak megjelenítendő üzenet, ha a kód-ellenőrzési munkamenet lejárt. A kód lejárt, vagy a kód soha nem lett létrehozva egy adott azonosítóhoz.|
|UserMessageIfInternalError | Felhasználói hibaüzenet, ha a kiszolgáló belső hibát észlelt.|
|UserMessageIfThrottled | Felhasználói hibaüzenet, ha a kérelem szabályozása megtörtént.|
|UserMessageIfVerificationFailedNoRetry | A felhasználónak megjelenítendő üzenet, ha érvénytelen kódot adott meg, és a felhasználó nem jogosult a megfelelő kód megadására.|
|UserMessageIfVerificationFailedRetryAllowed | A felhasználónak megjelenítendő üzenet, ha érvénytelen kódot adott meg, és a felhasználó megadhatja a megfelelő kódot.|

### <a name="example-verify-a-code"></a>Példa: kód ellenőrzése

Az alábbi példa egy Azure AD-SSPR technikai profilt mutat be, amely a kód ellenőrzéséhez használatos.

```XML
<TechnicalProfile Id="AadSspr-VerifyCode">
  <DisplayName>Verify Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">VerifyCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="verificationCode" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress"/>
  </InputClaims>
</TechnicalProfile>
```