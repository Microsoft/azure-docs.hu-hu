---
title: StringCollection stb jogcím-átalakítási példák egyéni házirendekhez
titleSuffix: Azure AD B2C
description: A StringCollection stb jogcím-átalakítási példákat tartalmaz a Azure Active Directory B2C Identity Experience Framework (IEF) sémájához.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/21/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cac7e6feb632456b63b97ead057f9ecaf49322ea
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81729718"
---
# <a name="stringcollection-claims-transformations"></a>StringCollection stb jogcímek átalakításai

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk példákat tartalmaz a karakterlánc-gyűjtési jogcímek átalakítására a Azure Active Directory B2C (Azure AD B2C) identitási élmény keretrendszere-sémájának használatával. További információ: [ClaimsTransformations](claimstransformations.md).

## <a name="additemtostringcollection"></a>AddItemToStringCollection

Karakterlánc-jogcímet hoz létre egy új, egyedi értékekre StringCollection stb jogcímként.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | sztring | A kimeneti jogcímhez hozzáadni kívánt ClaimType. |
| InputClaim | gyűjtemény | StringCollection stb | Választható Ha meg van adva, a jogcím-átalakítás átmásolja az elemeket ebből a gyűjteményből, és hozzáadja az elemet a kimeneti gyűjteményi jogcím végéhez. |
| OutputClaim | gyűjtemény | StringCollection stb | A jogcímek átalakítását követően létrehozott ClaimType a bemeneti jogcímben megadott értékkel lett meghívva. |

A jogcím-átalakítás használatával hozzáadhat egy karakterláncot egy új vagy egy meglévő StringCollection stb. Ez általában egy **HRE-UserWriteUsingAlternativeSecurityId** technikai profilban használatos. Új közösségi fiók létrehozása előtt a **CreateOtherMailsFromEmail** jogcím-átalakítás beolvassa a claimType, és hozzáadja az értéket a **otherMails** -claimType.

A következő jogcím-átalakítás hozzáadja az **e-mail-** claimType az **otherMails** claimType.

```XML
<ClaimsTransformation Id="CreateOtherMailsFromEmail" TransformationMethod="AddItemToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
  - **gyűjtemény**: ["someone@outlook.com"]
  - **elem**: "admin@contoso.com"
- Kimeneti jogcímek:
  - **gyűjtemény**: ["someone@outlook.com", "admin@contoso.com"]

## <a name="addparametertostringcollection"></a>AddParameterToStringCollection

Egy karakterlánc-paramétert hoz létre egy új, egyedi értékek StringCollection stb jogcímként.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | gyűjtemény | StringCollection stb | Választható Ha meg van adva, a jogcím-átalakítás átmásolja az elemeket ebből a gyűjteményből, és hozzáadja az elemet a kimeneti gyűjteményi jogcím végéhez. |
| InputParameter | item | sztring | A kimeneti jogcímhez hozzáadandó érték. |
| OutputClaim | gyűjtemény | StringCollection stb | A jogcím-átalakítást követően létrehozott ClaimType a bemeneti paraméterben megadott értékkel lett meghívva. |

Ezzel a jogcím-átalakítással adhat hozzá új vagy meglévő StringCollection stb karakterlánc-értéket. A következő példa egy állandó e-mail-admin@contoso.comcímet () hoz létre a **otherMails** jogcímhez.

```XML
<ClaimsTransformation Id="SetCompanyEmail" TransformationMethod="AddParameterToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="item" DataType="string" Value="admin@contoso.com" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
  - **gyűjtemény**: ["someone@outlook.com"]
- Bemeneti paraméterek
  - **elem**: "admin@contoso.com"
- Kimeneti jogcímek:
  - **gyűjtemény**: ["someone@outlook.com", "admin@contoso.com"]

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

A megadott karakterlánc-gyűjtemény első elemének beolvasása.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | gyűjtemény | StringCollection stb | A jogcím-átalakítás által az elemek beolvasásához használt ClaimTypes. |
| OutputClaim | extractedItem | sztring | A ClaimsTransformation után létrehozott ClaimTypes meghívása megtörtént. A gyűjtemény első eleme. |

A következő példa beolvassa a **otherMails** jogcímet, és az első tételt visszaküldi az **e-mail-** jogcímbe.

```XML
<ClaimsTransformation Id="CreateEmailFromOtherMails" TransformationMethod="GetSingleItemFromStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedItem" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
  - **gyűjtemény**: ["someone@outlook.com", "someone@contoso.com"]
- Kimeneti jogcímek:
  - **extractedItem**: "someone@outlook.com"


## <a name="stringcollectioncontains"></a>StringCollectionContains

Annak ellenőrzése, hogy egy StringCollection stb jogcím típusa tartalmaz-e elemet

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | StringCollection stb | A keresendő jogcím típusa. |
|InputParameter|item|sztring|A keresendő érték.|
|InputParameter|ignoreCase|sztring|Meghatározza, hogy az összehasonlítás figyelmen kívül hagyja-e az összehasonlított karakterláncok esetét.|
| OutputClaim | outputClaim | logikai | A ClaimsTransformation után létrehozott ClaimType meghívása megtörtént. Logikai kijelző, ha a gyűjtemény tartalmaz egy ilyen karakterláncot. |

A következő példa ellenőrzi, `roles` hogy a StringCollection stb jogcím típusa tartalmazza-e a **rendszergazda**értéket.

```XML
<ClaimsTransformation Id="IsAdmin" TransformationMethod="StringCollectionContains">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="item" DataType="string" Value="Admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

- Bemeneti jogcímek:
    - **inputClaim**: ["olvasó", "author", "admin"]
- Bemeneti paraméterek:
    - **elem**: "rendszergazda"
    - **ignoreCase**: "true"
- Kimeneti jogcímek:
    - **outputClaim**: "true"

## <a name="stringcollectioncontainsclaim"></a>StringCollectionContainsClaim

Ellenőrzi, hogy egy StringCollection stb jogcím-típust tartalmaz-e.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | gyűjtemény | StringCollection stb | A keresendő jogcím típusa. |
| InputClaim | item|sztring| A keresendő értéket tartalmazó jogcím típusa.|
|InputParameter|ignoreCase|sztring|Meghatározza, hogy az összehasonlítás figyelmen kívül hagyja-e az összehasonlított karakterláncok esetét.|
| OutputClaim | outputClaim | logikai | A ClaimsTransformation után létrehozott ClaimType meghívása megtörtént. Logikai kijelző, ha a gyűjtemény tartalmaz egy ilyen karakterláncot. |

A következő példa ellenőrzi, `roles` hogy a StringCollection stb jogcím típusa tartalmazza-e `role` a jogcím típusának értékét.

```XML
<ClaimsTransformation Id="HasRequiredRole" TransformationMethod="StringCollectionContainsClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="collection" />
    <InputClaim ClaimTypeReferenceId="role" TransformationClaimType="item" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hasAccess" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation> 
```

- Bemeneti jogcímek:
    - **gyűjtemény**: ["olvasó", "author", "admin"]
    - **elem**: "rendszergazda"
- Bemeneti paraméterek:
    - **ignoreCase**: "true"
- Kimeneti jogcímek:
    - **outputClaim**: "true"
