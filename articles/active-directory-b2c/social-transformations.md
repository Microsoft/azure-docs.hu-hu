---
title: A közösségi fiók jogcímek átalakítására vonatkozó példák egyéni házirendekhez
titleSuffix: Azure AD B2C
description: A közösségi fiók jogcímek átalakítására vonatkozó példák a Azure Active Directory B2C Identity Experience Framework (IEF) sémájához.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c60cecb9d6bfeeefc0569a1a57185d13f0c6442f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94953117"
---
# <a name="social-accounts-claims-transformations"></a>A közösségi fiókok jogcímei átalakítások

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) esetében a közösségi fiók identitásait egy `userIdentities` **alternativeSecurityIdCollection** -jogcím egyik attribútuma tárolja. A **alternativeSecurityIdCollection** minden eleme megadja a kiállítót (identitás-szolgáltató neve, például Facebook.com) és a `issuerUserId` , amely a kiállító egyedi felhasználói azonosítója.

```json
"userIdentities": [{
    "issuer": "google.com",
    "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"
  },
  {
    "issuer": "facebook.com",
    "issuerUserId": "MTIzNDU="
  }]
```

Ez a cikk példákat mutat be a közösségi fiókra vonatkozó jogcímek átalakítására a Azure AD B2Cban az identitási élmény keretrendszere sémájában. További információ: [ClaimsTransformations](claimstransformations.md).

## <a name="createalternativesecurityid"></a>CreateAlternativeSecurityId

A felhasználó alternativeSecurityId tulajdonságának JSON-ábrázolását hozza létre, amely a Azure Active Directory hívásokban használható. További információ: [AlternativeSecurityId](/graph/api/resources/alternativesecurityid) séma.

| Elem | TransformationClaimType | Adattípus | Jegyzetek |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | kulcs | sztring | A ClaimType, amely a közösségi identitás szolgáltatója által használt egyedi felhasználói azonosítót határozza meg. |
| InputClaim | identityProvider | sztring | Az a ClaimType, amely megadja a közösségi fiók identitás-szolgáltatójának nevét, például facebook.com. |
| OutputClaim | alternativeSecurityId | sztring | A ClaimsTransformation meghívása után létrehozott ClaimType. A közösségi fiók felhasználójának identitásával kapcsolatos információkat tartalmaz. A **kiállító** a `identityProvider` jogcím értéke. A **issuerUserId** a `key` jogcím értéke Base64 formátumban. |

A jogcímek átalakítása claimType létrehozásához `alternativeSecurityId` . Ezt a közösségi identitás-szolgáltató technikai profiljai használják, például: `Facebook-OAUTH` . A következő jogcím-átalakítás fogadja a felhasználó közösségi fiókjának AZONOSÍTÓját és az identitás-szolgáltató nevét. A technikai profil kimenete egy JSON-karakterlánc-formátum, amely az Azure AD-címtár szolgáltatásaiban használható.

```xml
<ClaimsTransformation Id="CreateAlternativeSecurityId" TransformationMethod="CreateAlternativeSecurityId">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="issuerUserId" TransformationClaimType="key" />
    <InputClaim ClaimTypeReferenceId="identityProvider" TransformationClaimType="identityProvider" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="alternativeSecurityId" TransformationClaimType="alternativeSecurityId" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
    - **kulcs**: 12334
    - **identityProvider**: Facebook.com
- Kimeneti jogcímek:
    - **alternativeSecurityId**: {"kiállító": "Facebook.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}

## <a name="additemtoalternativesecurityidcollection"></a>AddItemToAlternativeSecurityIdCollection

Egy jogcím hozzáadására szolgál `AlternativeSecurityId` `alternativeSecurityIdCollection` .

| Elem | TransformationClaimType | Adattípus | Jegyzetek |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | sztring | A kimeneti jogcímhez hozzáadni kívánt ClaimType. |
| InputClaim | gyűjtemény | alternativeSecurityIdCollection | A jogcím-átalakítás által használt ClaimTypes, ha elérhetők a házirendben. Ha meg van adni, a jogcím-átalakítás hozzáadja a `item` gyűjtemény végéhez. |
| OutputClaim | gyűjtemény | alternativeSecurityIdCollection | A ClaimsTransformation után létrehozott ClaimTypes meghívása megtörtént. Az új gyűjtemény, amely a bemenetből és a-ból származó elemeket is tartalmazza `collection` `item` . |

Az alábbi példa egy új közösségi identitást csatol egy meglévő fiókkal. Új közösségi identitás összekapcsolása:
1. A **HRE-UserReadUsingAlternativeSecurityId** és a **HRE-UserReadUsingObjectId** műszaki profilokban adja meg a felhasználó **alternativeSecurityIds** -jogcímeit.
1. Kérje meg a felhasználót, hogy jelentkezzen be az egyik olyan identitás-szolgáltatóval, amely nincs társítva ehhez a felhasználóhoz.
1. A **CreateAlternativeSecurityId** jogcím-átalakítás használatával hozzon létre egy új **alternativeSecurityId** jogcím-típust a következő névvel `AlternativeSecurityId2`
1. Hívja meg a **AddItemToAlternativeSecurityIdCollection** jogcímek átalakítását, és adja hozzá a **AlternativeSecurityId2** -jogcímet a meglévő **AlternativeSecurityIds** -jogcímhez.
1. **AlternativeSecurityIds** jogcím megőrzése a felhasználói fiók számára

```xml
<ClaimsTransformation Id="AddAnotherAlternativeSecurityId" TransformationMethod="AddItemToAlternativeSecurityIdCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId2" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
    - **elem**: {"kiállító": "Facebook.com", "issuerUserId": "MTIzNDU ="}
    - **gyűjtemény**: [{"kiállító": "Live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}]
- Kimeneti jogcímek:
    - **gyűjtemény**: [{"kiállító": "Live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}, {"kiállító": "Facebook.com", "issuerUserId": "MTIzNDU ="}]

## <a name="getidentityprovidersfromalternativesecurityidcollectiontransformation"></a>GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation

A **alternativeSecurityIdCollection** jogcím kiállítóinak listáját adja vissza egy új **StringCollection stb** -jogcímbe.

| Elem | TransformationClaimType | Adattípus | Jegyzetek |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | alternativeSecurityIdCollection | alternativeSecurityIdCollection | Az ClaimType (kiállító) listájának beolvasásához használandó alkalmazás. |
| OutputClaim | identityProvidersCollection | StringCollection stb | A ClaimsTransformation után létrehozott ClaimTypes meghívása megtörtént. Az alternativeSecurityIdCollection bemeneti jogcímevel társított Identitáskezelő-szolgáltatók listája |

A következő jogcím-átalakítás beolvassa a felhasználói **alternativeSecurityIds** jogcímet, és Kinyeri a fiókhoz társított Identity Provider-nevek listáját. A kimeneti **identityProvidersCollection** használatával jelenítheti meg a felhasználó számára a fiókhoz társított identitás-szolgáltatók listáját. Vagy az identitás-szolgáltató kijelölése lapon szűrheti a kimeneti **identityProvidersCollection** -jogcím alapján az identitás-szolgáltatók listáját. Így a felhasználó a fiókhoz még nem társított új közösségi identitás összekapcsolására is választhatja.

```xml
<ClaimsTransformation Id="ExtractIdentityProviders" TransformationMethod="GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityIds" TransformationClaimType="alternativeSecurityIdCollection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="identityProviders" TransformationClaimType="identityProvidersCollection" />
  </OutputClaims>
</ClaimsTransformation>
```

- Bemeneti jogcímek:
    - **alternativeSecurityIdCollection**: [{"kiállító": "Google.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}, {"kiállító": "Facebook.com", "issuerUserId": "MTIzNDU ="}]
- Kimeneti jogcímek:
    - **identityProvidersCollection**: ["Facebook.com", "Google.com"]

## <a name="removealternativesecurityidbyidentityprovider"></a>RemoveAlternativeSecurityIdByIdentityProvider

Eltávolít egy **AlternativeSecurityId** egy **alternativeSecurityIdCollection** -jogcímből.

| Elem | TransformationClaimType | Adattípus | Jegyzetek |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | identityProvider | sztring | Az a ClaimType, amely tartalmazza a gyűjteményből eltávolítandó identitás-szolgáltató nevét. |
| InputClaim | gyűjtemény | alternativeSecurityIdCollection | A jogcím-átalakítás által használt ClaimTypes. A jogcím-átalakítás eltávolítja a identityProvider a gyűjteményből. |
| OutputClaim | gyűjtemény | alternativeSecurityIdCollection | A ClaimsTransformation után létrehozott ClaimTypes meghívása megtörtént. Az új gyűjteményt, miután a identityProvider eltávolította a gyűjteményből. |

Az alábbi példa egy meglévő fiókkal kapcsolja össze a közösségi identitás egyikét. Közösségi identitás leválasztása:
1. A **HRE-UserReadUsingAlternativeSecurityId** és a **HRE-UserReadUsingObjectId** műszaki profilokban adja meg a felhasználó **alternativeSecurityIds** -jogcímeit.
2. Kérje meg a felhasználót, hogy válassza ki, hogy melyik közösségi fiókot kívánja eltávolítani az ehhez a felhasználóhoz hozzárendelt identitás-szolgáltatók listájából.
3. Hívjon fel egy jogcím-átalakítási technikai profilt, amely meghívja a **RemoveAlternativeSecurityIdByIdentityProvider** jogcímek átalakítását, amely eltávolította a kiválasztott közösségi identitást az identitás-szolgáltató neve alapján.
4. A **alternativeSecurityIds** jogcímet a felhasználói fiók számára is megőrzi.

```xml
<ClaimsTransformation Id="RemoveAlternativeSecurityIdByIdentityProvider" TransformationMethod="RemoveAlternativeSecurityIdByIdentityProvider">
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="secondIdentityProvider" TransformationClaimType="identityProvider" />
        <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformations>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
    - **identityProvider**: Facebook.com
    - **gyűjtemény**: [{"kiállító": "Live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}, {"kiállító": "Facebook.com", "issuerUserId": "MTIzNDU ="}]
- Kimeneti jogcímek:
    - **gyűjtemény**: [{"kiállító": "Live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}]