---
title: Azure AD MFA technikai profilok az egyéni házirendekben
titleSuffix: Azure AD B2C
description: Egyéni házirend-hivatkozás az Azure AD Multi-Factor Authentication (MFA) technikai profiljaihoz a Azure AD B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e81ac35555e6653cecb602e5af2f19aa3e2f05e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94840593"
---
# <a name="define-an-azure-ad-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Azure AD MFA technikai profil definiálása egy Azure AD B2C egyéni házirendben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory B2C (Azure AD B2C) támogatja a telefonszámok ellenőrzését az Azure AD többtényezős hitelesítésével (MFA). Használja ezt a műszaki profilt egy kód létrehozásához és egy telefonszámra való elküldéséhez, majd a kód ellenőrzéséhez. Az Azure AD MFA technikai profilja szintén hibaüzenetet adhat vissza.  Az érvényesítési technikai profil ellenőrzi a felhasználó által megadott, a felhasználói utazás előtt megjelenő adatmennyiséget. Az érvényesítési technikai profillal egy önérvényesített oldalon egy hibaüzenet jelenik meg.

Ez a technikai profil:

- Nem biztosít felületet a felhasználóval való kommunikációhoz. Ehelyett a felhasználói felületet egy [önérvényesített](self-asserted-technical-profile.md) technikai profilból, vagy egy [megjelenítési vezérlőként](display-controls.md) kell meghívni [érvényesítési technikai profilként](validation-technical-profile.md).
- Az Azure AD MFA szolgáltatást használja a kód létrehozásához és elküldéséhez egy telefonszámra, majd ellenőrzi a kódot.  
- A telefonszámot szöveges üzenetekben érvényesíti.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protokoll

A **protokoll** elem **Name** attribútumát be kell állítani `Proprietary` . A **kezelő** attribútumnak tartalmaznia kell az Azure ad B2C által használt protokollkezelő-szerelvény teljesen minősített nevét:

```
Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

Az alábbi példa egy Azure AD MFA technikai profilt mutat be:

```xml
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-sms"></a>SMS küldése

Ennek a technikai profilnak az első módja egy kód létrehozása és elküldése. Ehhez a módhoz a következő beállítások konfigurálhatók.

### <a name="input-claims"></a>Bemeneti jogcímek

A **szabályzattípushoz** elem tartalmazza az Azure ad MFA számára küldendő jogcímek listáját. A jogcím nevét a MFA technikai profiljában definiált névre is leképezheti.

| ClaimReferenceId | Kötelező | Leírás |
| --------- | -------- | ----------- |
| userPrincipalName | Yes | A telefonszámot birtokló felhasználó azonosítója. |
| Telefonszám | Yes | Az SMS-kód küldésére szolgáló telefonszám. |
| companyName | No |A vállalat neve az SMS-ben. Ha nincs megadva, a rendszer az alkalmazás nevét használja. |
| területi beállítás | No | Az SMS területi beállítása. Ha nincs megadva, a rendszer a felhasználó böngésző területi beállítását használja. |

A **InputClaimsTransformations** elem olyan **InputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja, amelyek a bemeneti jogcímek módosítására vagy újak előállítására szolgálnak az Azure ad MFA szolgáltatásba való küldés előtt.

### <a name="output-claims"></a>Kimeneti jogcímek

Az Azure AD MFA protokoll szolgáltatója nem ad vissza **OutputClaims**, így nem kell megadnia a kimeneti jogcímeket. Megadhat azonban olyan jogcímeket is, amelyeket nem ad vissza az Azure AD MFA-identitás szolgáltatója, feltéve, hogy az `DefaultValue` attribútumot beállította.

A **OutputClaimsTransformations** elem olyan **OutputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja, amelyek a kimeneti jogcímek módosítására vagy újak előállítására szolgálnak.

### <a name="metadata"></a>Metaadatok

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Művelet | Yes | **OneWaySMS** kell lennie.  |

#### <a name="ui-elements"></a>Felhasználói felület elemei

A következő metaadatokkal konfigurálhatja az SMS-hibák küldésekor megjelenő hibaüzeneteket. A metaadatokat az [önérvényesített](self-asserted-technical-profile.md) technikai profilban kell konfigurálni. A hibaüzenetek [honosítható](localization-string-ids.md#azure-ad-mfa-error-messages).

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| UserMessageIfCouldntSendSms | No | Felhasználói hibaüzenet, ha a megadott telefonszám nem fogad SMS-üzenetet. |
| UserMessageIfInvalidFormat | No | Felhasználói hibaüzenet, ha a megadott telefonszám nem érvényes telefonszám. |
| UserMessageIfServerError | No | Felhasználói hibaüzenet, ha a kiszolgáló belső hibát észlelt. |
| UserMessageIfThrottled| No | Felhasználói hibaüzenet, ha a kérelem szabályozása megtörtént.|

### <a name="example-send-an-sms"></a>Példa: SMS küldése

Az alábbi példa egy Azure AD MFA technikai profilt mutat be, amely egy kód SMS-ben való küldésére szolgál.

```xml
<TechnicalProfile Id="AzureMfa-SendSms">
  <DisplayName>Send Sms</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">OneWaySMS</Item>
  </Metadata>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CombinePhoneAndCountryCode" />
    <InputClaimsTransformation ReferenceId="ConvertStringToPhoneNumber" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="fullPhoneNumber" PartnerClaimType="phoneNumber" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Kód ellenőrzése

A technikai profil második módja egy kód ellenőrzése. Ehhez a módhoz a következő beállítások konfigurálhatók.

### <a name="input-claims"></a>Bemeneti jogcímek

A **szabályzattípushoz** elem tartalmazza az Azure ad MFA számára küldendő jogcímek listáját. A jogcím nevét a MFA technikai profiljában definiált névre is leképezheti.

| ClaimReferenceId | Kötelező | Leírás |
| --------- | -------- | ----------- | ----------- |
| Telefonszám| Yes | Ugyanazt a telefonszámot használja, mint korábban a kód elküldéséhez. A rendszer a telefonos ellenőrzési munkamenetek megkeresésére is használatos. |
| verificationCode  | Yes | A felhasználó által ellenőrizendő ellenőrző kód |

A **InputClaimsTransformations** elem olyan **InputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja, amelyek a bemeneti jogcímek módosítására vagy újak létrehozására szolgálnak az Azure ad MFA szolgáltatás meghívása előtt.

### <a name="output-claims"></a>Kimeneti jogcímek

Az Azure AD MFA protokoll szolgáltatója nem ad vissza **OutputClaims**, így nem kell megadnia a kimeneti jogcímeket. Megadhat azonban olyan jogcímeket is, amelyeket nem ad vissza az Azure AD MFA-identitás szolgáltatója, feltéve, hogy az `DefaultValue` attribútumot beállította.

A **OutputClaimsTransformations** elem olyan **OutputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja, amelyek a kimeneti jogcímek módosítására vagy újak előállítására szolgálnak.

### <a name="metadata"></a>Metaadatok

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Művelet | Yes | **Ellenőrizni** kell |

#### <a name="ui-elements"></a>Felhasználói felület elemei

A következő metaadatokkal konfigurálhatja a kód-ellenőrzési hiba esetén megjelenő hibaüzeneteket. A metaadatokat az [önérvényesített](self-asserted-technical-profile.md) technikai profilban kell konfigurálni. A hibaüzenetek [honosítható](localization-string-ids.md#azure-ad-mfa-error-messages).

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| UserMessageIfMaxAllowedCodeRetryReached| No | Felhasználói hibaüzenet, ha a felhasználó túl sokszor próbált meg ellenőrző kódot. |
| UserMessageIfServerError | No | Felhasználói hibaüzenet, ha a kiszolgáló belső hibát észlelt. |
| UserMessageIfThrottled| No | Felhasználói hibaüzenet, ha a kérelem szabályozása megtörtént.|
| UserMessageIfWrongCodeEntered| No| Felhasználói hibaüzenet, ha az ellenőrzéshez megadott kód nem megfelelő.|

### <a name="example-verify-a-code"></a>Példa: kód ellenőrzése

A következő példa egy Azure AD MFA technikai profilt mutat be, amely a kód ellenőrzéséhez használatos.

```xml
<TechnicalProfile Id="AzureMfa-VerifySms">
    <DisplayName>Verify Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">Verify</Item>
    </Metadata>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="phoneNumber" PartnerClaimType="phoneNumber" />
        <InputClaim ClaimTypeReferenceId="verificationCode" />
    </InputClaims>
</TechnicalProfile>
```
