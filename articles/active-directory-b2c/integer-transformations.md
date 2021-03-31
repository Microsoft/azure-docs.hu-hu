---
title: Egész szám típusú jogcím-átalakítási példák egyéni házirendekhez
titleSuffix: Azure AD B2C
description: Egész szám értékű jogcím-átalakítási példák a Azure Active Directory B2C Identity Experience Framework (IEF) sémájához.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 066a6489e6244369453ec5d9f21d5e1e83fcd6c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85201750"
---
# <a name="integer-claims-transformations"></a>Egész szám típusú jogcímek átalakítása

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk példákat tartalmaz a Azure Active Directory B2C (Azure AD B2C) Identity Experience Framework sémájának egész számú jogcím-átalakításának használatára. További információ: [ClaimsTransformations](claimstransformations.md).

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim

Hosszú adattípust alakít át karakterlánc adattípusra.

| Elem | TransformationClaimType | Adattípus | Jegyzetek |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | hosszú | A ClaimType karakterlánccá alakítja át. |
| OutputClaim | outputClaim | sztring | A ClaimsTransformation után létrehozott ClaimType meghívása megtörtént. |

Ebben a példában a `numericUserId` Long értékkel rendelkező jogcím egy `UserId` karakterlánc típusú értékű jogcímre lett konvertálva.

```xml
<ClaimsTransformation Id="CreateUserId" TransformationMethod="ConvertNumberToStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="UserId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
    - **inputClaim**: 12334 (hosszú)
- Kimeneti jogcímek:
    - **outputClaim**: "12334" (karakterlánc)

