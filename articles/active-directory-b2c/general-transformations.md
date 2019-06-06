---
title: Általános jogcímek átalakítása példák az identitás élmény keretrendszer sémát az Azure Active Directory B2C |} A Microsoft Docs
description: Az identitás élmény keretrendszer sémát az Azure Active Directory B2C általános jogcímek átalakítása példákat.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a5f8068ea7e97343749c719d2d0800e20701079c
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66510985"
---
# <a name="general-claims-transformations"></a>Általános jogcímek átalakítása

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk példákat ad az Azure Active Directory (Azure AD) B2C általános jogcímek átalakítása az identitás-kezelőfelületi keretrendszer séma használatával. További információkért lásd: [ClaimsTransformations](claimstransformations.md).

## <a name="doesclaimexist"></a>DoesClaimExist

Ellenőrzi, hogy a **bemeneti jogcím** vagy nem létezik, és beállítja a **kimeneti jogcím** igaz vagy hamis értéket annak megfelelően.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| Bemeneti jogcím | Bemeneti jogcím |Bármely | A bemeneti jogcímek, amelynek megléte ellenőrizni kell. |
| OutputClaim | outputClaim | logikai | A takar, amelyek a ClaimsTransformation meghívása után jön létre. |

Használja a jogcím-átalakítás annak ellenőrzésére, ha egy jogcímet létezik, vagy minden olyan értéket tartalmaz. A visszatérési érték logikai érték beolvasása, amely azt jelzi, hogy létezik-e a jogcím. Alábbi példa ellenőrzi, hogy létezik-e az e-mail-cím.

```XML
<ClaimsTransformation Id="CheckIfEmailPresent" TransformationMethod="DoesClaimExist">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim" />
  </InputClaims>                    
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isEmailPresent" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- A bemeneti jogcímek:
  - **bemeneti jogcím**: someone@contoso.com
- Kimeneti jogcímek: 
    - **kimeneti jogcím**: igaz

## <a name="hash"></a>Kivonat

A megadott egyszerű szöveges a védőérték és a titkos kulcs kivonata.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| Bemeneti jogcím | egyszerű szöveg | string | A bemeneti jogcímek titkosítását |
| Bemeneti jogcím | Salt érték | string | A védőérték paramétert. Létrehozhat egy véletlenszerű értéket használ `CreateRandomString` jogcím-átalakítás. |
| InputParameter | randomizerSecret | string | Egy meglévő Azure AD B2C mutat **Szabályzatbejegyzések**. Hozzon létre egy újat: Válassza ki az Azure AD B2C-bérlőben **B2C-beállítások > identitás-kezelőfelületi keretrendszer**. Válassza ki **Szabályzatbejegyzések** a elérhető a bérlői kulcsok megtekintéséhez. Válassza a **Hozzáadás** lehetőséget. A **beállítások**válassza **manuális**. Adjon meg egy nevet (a B2C_1A_ automatikusan hozzáadhatók előtag.). Titkos kód mezőben adja meg minden olyan titkos kulcsot szeretné használni, például a 1234567890. A kulcshasználat, válassza ki a **titkos**. Kattintson a **Létrehozás** gombra. |
| OutputClaim | Ujjlenyomat | string | Az a jogcím-átalakítás után előállított takar meghívása. A konfigurált jogcímszabályok a `plaintext` bemeneti jogcím. |

```XML
<ClaimsTransformation Id="HashPasswordWithEmail" TransformationMethod="Hash">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="password" TransformationClaimType="plaintext" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="salt" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="randomizerSecret" DataType="string" Value="B2C_1A_AccountTransformSecret" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hashedPassword" TransformationClaimType="hash" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- A bemeneti jogcímek:
    - **egyszerű szöveges**: MyPass@word1
    - **védőérték**: 487624568
    - **randomizerSecret**: B2C_1A_AccountTransformSecret
- Kimeneti jogcímek: 
    - **kimeneti jogcím**: CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U=



