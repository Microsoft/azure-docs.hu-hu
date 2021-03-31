---
title: A jogcímek átalakítására vonatkozó példák egyéni házirendekhez
description: A jogcímek átalakítására vonatkozó példák a Azure Active Directory B2C Identity Experience Framework (IEF) sémájához.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: eaf58b964517162ee7f7eb925e1e64830eedc087
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85202551"
---
# <a name="date-claims-transformations"></a>A jogcímek átalakításának dátuma

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk példákat tartalmaz a Azure Active Directory B2C (Azure AD B2C) Identity Experience Framework sémájának a Date jogcím-átalakításának használatára. További információ: [ClaimsTransformations](claimstransformations.md).

## <a name="assertdatetimeisgreaterthan"></a>AssertDateTimeIsGreaterThan

Ellenőrzi, hogy egy dátum-és időjogcím (karakterlánc adattípusa) későbbi-e, mint egy második dátum és idő jogcím (string adattípus), és kivételt dob.

| Elem | TransformationClaimType | Adattípus | Jegyzetek |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | leftOperand | sztring | Az első jogcím típusa, amelynek a második jogcímnél későbbinek kell lennie. |
| InputClaim | rightOperand | sztring | Második jogcím típusa, amelynek az első jogcímnél korábbinak kell lennie. |
| InputParameter | AssertIfEqualTo | boolean | Meghatározza, hogy ez az állítás akkor legyen-e, ha a bal oldali operandus egyenlő a jobb oldali operandussal. |
| InputParameter | AssertIfRightOperandIsNotPresent | boolean | Azt adja meg, hogy ez az állítás a megfelelő operandus hiánya esetén legyen-e átadva. |
| InputParameter | TreatAsEqualIfWithinMillseconds | int | Megadja, hogy hány ezredmásodpercet kell engedélyezni a két dátum között, hogy az időpontok egyenlőek legyenek (például az óra eldöntése miatt). |

Az **AssertDateTimeIsGreaterThan** jogcímek átalakítását mindig egy [önérvényesített technikai profil](self-asserted-technical-profile.md)által hívott [érvényesítési műszaki profilból](validation-technical-profile.md) hajtja végre a rendszer. A **DateTimeGreaterThan** önérvényesített technikai profil metaadatai a technikai profil által a felhasználónak megjelenített hibaüzenetet vezérlik. A hibaüzenetek [honosítható](localization-string-ids.md#claims-transformations-error-messages).

![AssertStringClaimsAreEqual-végrehajtás](./media/date-transformations/assert-execution.png)

A következő példa összehasonlítja a `currentDateTime` jogcímet a `approvedDateTime` jogcímevel. A rendszer hibát jelez, ha `currentDateTime` a későbbi, mint `approvedDateTime` . Az átalakítás egyenlőként kezeli az értékeket, ha 5 percen belül (30000 ezredmásodperc) eltérnek.

```xml
<ClaimsTransformation Id="AssertApprovedDateTimeLaterThanCurrentDateTime" TransformationMethod="AssertDateTimeIsGreaterThan">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="approvedDateTime" TransformationClaimType="leftOperand" />
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="rightOperand" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
    <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
    <InputParameter Id="TreatAsEqualIfWithinMillseconds" DataType="int" Value="300000" />
  </InputParameters>
</ClaimsTransformation>
```

Az `login-NonInteractive` érvényesítési technikai profil meghívja a `AssertApprovedDateTimeLaterThanCurrentDateTime` jogcím-átalakítást.
```xml
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertApprovedDateTimeLaterThanCurrentDateTime" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Az önérvényesített technikai profil meghívja az érvényesítési **bejelentkezést nem interaktív** technikai profilt.

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="DateTimeGreaterThan">Custom error message if the provided left operand is greater than the right operand.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
    - **leftOperand**: 2020-03-01T15:00:00.0000000 z
    - **rightOperand**: 2020-03-01T14:00:00.0000000 z
- Eredmény: feldobott hiba

## <a name="convertdatetodatetimeclaim"></a>ConvertDateToDateTimeClaim

A **dátum** claimType egy **datetime** claimType alakítja át. A jogcím-átalakítás átalakítja az időformátumot, és hozzáadja a 12:00:00-as dátumot a dátumhoz.

| Elem | TransformationClaimType | Adattípus | Jegyzetek |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | dátum | Az átalakítandó ClaimType. |
| OutputClaim | outputClaim | dateTime | A ClaimsTransformation után létrehozott ClaimType meghívása megtörtént. |

Az alábbi példa bemutatja a jogcím `dateOfBirth` (dátum adattípus) átalakítását egy másik jogcímre `dateOfBirthWithTime` (datetime adattípus).

```xml
  <ClaimsTransformation Id="ConvertToDateTime" TransformationMethod="ConvertDateToDateTimeClaim">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="dateOfBirth" TransformationClaimType="inputClaim" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="dateOfBirthWithTime" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
    - **inputClaim**: 2020-15-03
- Kimeneti jogcímek:
    - **outputClaim**: 2020 – 15 – 03T00:00:00.0000000 z

## <a name="convertdatetimetodateclaim"></a>ConvertDateTimeToDateClaim

Egy **datetime** claimType alakít át egy **dátum** -claimType. A jogcím-átalakítás eltávolítja az időformátumot a dátumból.

| Elem | TransformationClaimType | Adattípus | Jegyzetek |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | dateTime | Az átalakítandó ClaimType. |
| OutputClaim | outputClaim | dátum | A ClaimsTransformation után létrehozott ClaimType meghívása megtörtént. |

Az alábbi példa bemutatja a jogcím `systemDateTime` (datetime adattípus) átalakítását egy másik jogcímre `systemDate` (dátum adattípus).

```xml
<ClaimsTransformation Id="ConvertToDate" TransformationMethod="ConvertDateTimeToDateClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDate" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
  - **inputClaim**: 2020 – 15 – 03T11:34:22.0000000 z
- Kimeneti jogcímek:
  - **outputClaim**: 2020-15-03

## <a name="getcurrentdatetime"></a>GetCurrentDateTime

Az aktuális UTC dátum és idő lekérése és az érték hozzáadása egy ClaimType.

| Elem | TransformationClaimType | Adattípus | Jegyzetek |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | currentDateTime | dateTime | A ClaimsTransformation után létrehozott ClaimType meghívása megtörtént. |

```xml
<ClaimsTransformation Id="GetSystemDateTime" TransformationMethod="GetCurrentDateTime">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="currentDateTime" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

* Kimeneti jogcímek:
    * **currentDateTime**: 2020 – 15 – 03T11:40:35.0000000 z

## <a name="datetimecomparison"></a>DateTimeComparison

Annak megállapítása, hogy egy dateTime később, korábban vagy egyenlő-e egy másikkal. Az eredmény egy új logikai ClaimType logikai érték, amelynek értéke `true` vagy `false` .

| Elem | TransformationClaimType | Adattípus | Jegyzetek |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | firstDateTime | dateTime | Az első dateTime érték, amely azt hasonlítja össze, hogy korábbi vagy későbbi, mint a második dateTime. A Null érték kivételt jelez. |
| InputClaim | secondDateTime | dateTime | A második dátum és idő, amely azt hasonlítja össze, hogy az előbbi vagy későbbi, mint az első dateTime. A null értéket a rendszer az aktuális datetTime kezeli. |
| InputParameter | operátor | sztring | A következő értékek egyike: azonos, későbbi, vagy korábbi, mint. |
| InputParameter | timeSpanInSeconds | int | Adja hozzá a TimeSpan az első datetime értékhez. |
| OutputClaim | result | boolean | A ClaimsTransformation után létrehozott ClaimType meghívása megtörtént. |

Ezzel a jogcím-átalakítással megállapíthatja, hogy két ClaimTypes egyenlő, később vagy korábban, mint egymást. Előfordulhat például, hogy a felhasználó az utolsó alkalommal fogadta el a szolgáltatási feltételeket (TOS). 3 hónap elteltével megkérheti a felhasználót, hogy ismét hozzáférjen a TSz-hez.
A jogcím-átalakítás futtatásához először le kell kérnie az aktuális dateTime értéket, és az utolsó alkalommal a felhasználó elfogadja a TSz-ket.

```xml
<ClaimsTransformation Id="CompareLastTOSAcceptedWithCurrentDateTime" TransformationMethod="DateTimeComparison">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="firstDateTime" />
    <InputClaim ClaimTypeReferenceId="extension_LastTOSAccepted" TransformationClaimType="secondDateTime" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="later than" />
    <InputParameter Id="timeSpanInSeconds" DataType="int" Value="7776000" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="result" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
    - **firstDateTime**: 2020 – 01-01T00:00:00.100000 z
    - **secondDateTime**: 2020 – 04-01T00:00:00.100000 z
- Bemeneti paraméterek:
    - **operátor**: később, mint
    - **timeSpanInSeconds**: 7776000 (90 nap)
- Kimeneti jogcímek:
    - **eredmény**: igaz
