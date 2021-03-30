---
title: Az egyéni házirendekben a telefonszámot jogcímek átalakítása
titleSuffix: Azure AD B2C
description: Egyéni házirend-hivatkozás a Azure AD B2Cban található telefonszám-jogcímek átalakításához.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e175a81efc1ab0950c1fda314efb206ff97a2b7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85385382"
---
# <a name="define-phone-number-claims-transformations-in-azure-ad-b2c"></a>Telefonszám-jogcímek átalakításának meghatározása Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk a Azure Active Directory B2C (Azure AD B2C) identitás-keretrendszer sémájának a telefonszám-jogcímek átalakításának használatára vonatkozó hivatkozásokat és példákat tartalmaz. A jogcímek átalakításával kapcsolatos további információkért lásd: [ClaimsTransformations](claimstransformations.md).

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="convertphonenumberclaimtostring"></a>ConvertPhoneNumberClaimToString

Az `phoneNumber` adattípust `string` adattípusba alakítja.

| Elem | TransformationClaimType | Adattípus | Jegyzetek |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | Telefonszám | Telefonszám |  A ClaimType karakterlánccá alakítja át. |
| OutputClaim | phoneNumberString | sztring | A jogcím-átalakítás után létrehozott ClaimType meghívása megtörtént. |

Ebben a példában a értékkel rendelkező cellPhoneNumber jogcím a következő `phoneNumber` értékkel rendelkező mobil jogcímre lesz konvertálva: `string` .

```xml
<ClaimsTransformation Id="PhoneNumberToString" TransformationMethod="ConvertPhoneNumberClaimToString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="cellPhoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="cellPhone" TransformationClaimType="phoneNumberString" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
  - **telefonszám**: + 11234567890 (telefonszám)
- Kimeneti jogcímek:
  - **phoneNumberString**: + 11234567890 (karakterlánc)


## <a name="convertstringtophonenumberclaim"></a>ConvertStringToPhoneNumberClaim

Ez a jogcím-átalakítás érvényesíti a telefonszám formátumát. Ha érvényes formátumú, módosítsa az Azure AD B2C által használt szabványos formátumra. Ha a megadott telefonszám formátuma érvénytelen, hibaüzenetet kap.

| Elem | TransformationClaimType | Adattípus | Jegyzetek |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumberString | sztring |  A telefonszámhoz tartozó karakterlánc-jogcím. A telefonszámnak nemzetközi formátumúnak kell lennie, és meg kell felelnie egy vezető "+" és ország/régió kódnak. Ha a bemeneti jogcímet adja `country` meg, a telefonszám helyi formátumban van megadva (az ország/régió kódja nélkül). |
| InputClaim | ország | sztring | Választható A ISO3166 formátumú (kétbetűs ISO-3166 ország/régió kódja) típusú telefonszám ország/régió kódjához tartozó jogcíme. |
| OutputClaim | outputClaim | Telefonszám | A jogcímek átalakításának eredménye. |

A **ConvertStringToPhoneNumberClaim** jogcímek átalakítását a rendszer mindig egy [önérvényesített technikai profil](self-asserted-technical-profile.md) vagy [megjelenítési vezérlő](display-controls.md)által hívott [érvényesítési műszaki profilból](validation-technical-profile.md) hajtja végre. A **UserMessageIfClaimsTransformationInvalidPhoneNumber** önérvényesített technikai profil metaadatai a felhasználónak megjelenített hibaüzenetet vezérlik.

![A hibaüzenet végrehajtási útvonalának ábrája](./media/phone-authentication/assert-execution.png)

A jogcím-átalakítással ellenőrizheti, hogy a megadott karakterlánc-jogcím érvényes telefonszám-e. Ha nem, a rendszer hibaüzenetet küld. A következő példa ellenőrzi, hogy a **phoneString** claimType valóban érvényes telefonszám-e, majd a telefonszámot a standard Azure ad B2C formátumban adja vissza. Ellenkező esetben a rendszer hibaüzenetet küld.

```xml
<ClaimsTransformation Id="ConvertStringToPhoneNumber" TransformationMethod="ConvertStringToPhoneNumberClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneString" TransformationClaimType="phoneNumberString" />
    <InputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="country" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

A jogcímek átalakítását tartalmazó, az érvényesítési technikai profilt meghívó önérvényesített technikai profil meghatározza a hibaüzenetet.

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationInvalidPhoneNumber">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>1\. példa

- Bemeneti jogcímek:
  - **phoneNumberString**: 033 456-7890
  - **ország**: DK
- Kimeneti jogcímek:
  - **outputClaim**: + 450334567890

### <a name="example-2"></a>2\. példa

- Bemeneti jogcímek:
  - **phoneNumberString**: + 1 (123) 456-7890
- Kimeneti jogcímek:
  - **outputClaim**: + 11234567890


## <a name="getnationalnumberandcountrycodefromphonenumberstring"></a>GetNationalNumberAndCountryCodeFromPhoneNumberString

Ezzel kibontja az ország/régió kódját és a nemzeti számot a bemeneti jogcímen, és opcionálisan kivételt jelez, ha a megadott telefonszám érvénytelen.

| Elem | TransformationClaimType | Adattípus | Jegyzetek |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | Telefonszám | sztring | A telefonszám karakterlánc-jogcíme. A telefonszámnak nemzetközi formátumúnak kell lennie, és meg kell felelnie egy vezető "+" és ország/régió kódnak. |
| InputParameter | throwExceptionOnFailure | boolean | Választható Egy paraméter, amely azt jelzi, hogy a rendszer kivételt jelez-e, ha a telefonszám érvénytelen. Az alapértelmezett érték false (hamis). |
| InputParameter | countryCodeType | sztring | Választható Egy paraméter, amely az ország/régió kódjának típusát jelzi a kimeneti jogcímek között. Az elérhető értékek a következők: **CallingCode** (egy ország/régió nemzetközi hívási kódja, például + 1) vagy **ISO3166** (kétbetűs ISO-3166 ország/régió kódja). |
| OutputClaim | nationalNumber | sztring | A telefonszám országos számára vonatkozó jogcímek száma. |
| OutputClaim | Országhívószám | sztring | A telefonszám ország-/régiókódjának karakterlánc-jogcíme. |


Ha a **GetNationalNumberAndCountryCodeFromPhoneNumberString** jogcím-átalakítás olyan [érvényesítési műszaki profilból](validation-technical-profile.md) lett végrehajtva, amelyet egy [önérvényesített technikai profil](self-asserted-technical-profile.md) vagy egy [megjelenítési vezérlő művelet hív meg](display-controls.md#display-control-actions), akkor a **UserMessageIfPhoneNumberParseFailure** önérvényesített technikai profil metaadatai a felhasználónak megjelenített hibaüzenetet vezérlik.

![A hibaüzenet végrehajtási útvonalának ábrája](./media/phone-authentication/assert-execution.png)

A jogcímek átalakításával teljes telefonszámot oszthat meg az ország/régió kódja és a nemzeti szám szerint. Ha a megadott telefonszám érvénytelen, dönthet úgy, hogy eldönti, hogy van-e hibaüzenet.

A következő példában a telefonszámot a nemzeti szám és az ország/régió kódja között próbálja meg megosztani. Ha a telefonszám érvényes, a telefonszámot a rendszer felülbírálja a nemzeti szám alapján. Ha a telefonszám érvénytelen, a rendszer nem dobja el a kivételt, és a telefonszám továbbra is az eredeti értékkel rendelkezik.

```xml
<ClaimsTransformation Id="GetNationalNumberAndCountryCodeFromPhoneNumberString" TransformationMethod="GetNationalNumberAndCountryCodeFromPhoneNumberString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="throwExceptionOnFailure" DataType="boolean" Value="false" />
    <InputParameter Id="countryCodeType" DataType="string" Value="ISO3166" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="nationalNumber" TransformationClaimType="nationalNumber" />
    <OutputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="countryCode" />
  </OutputClaims>
</ClaimsTransformation>
```

A jogcímek átalakítását tartalmazó, az érvényesítési technikai profilt meghívó önérvényesített technikai profil meghatározza a hibaüzenetet.

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfPhoneNumberParseFailure">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>1\. példa

- Bemeneti jogcímek:
  - **telefonszám**: + 49 (123) 456-7890
- Bemeneti paraméterek:
  - **throwExceptionOnFailure**: hamis
  - **countryCodeType**: ISO3166
- Kimeneti jogcímek:
  - **nationalNumber**: 1234567890
  - **országhívószám**: de

### <a name="example-2"></a>2\. példa

- Bemeneti jogcímek:
  - **telefonszám**: + 49 (123) 456-7890
- Bemeneti paraméterek
  - **throwExceptionOnFailure**: hamis
  - **countryCodeType**: CallingCode
- Kimeneti jogcímek:
  - **nationalNumber**: 1234567890
  - **országhívószám**: + 49
