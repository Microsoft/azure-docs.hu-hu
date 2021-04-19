---
title: Feltételes hozzáférés műszaki profiljai az egyéni szabályzatban
titleSuffix: Azure AD B2C
description: Egyéni szabályzati referencia a feltételes hozzáférés technikai profiljaihoz a Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/19/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ebdc1c9c92f6e3debf08cb640e46424c4ad9d5ad
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107721888"
---
# <a name="define-a-conditional-access-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Feltételes hozzáférés műszaki profiljának definiálása egyéni Azure Active Directory B2C szabályzatban

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) feltételes hozzáférés az eszköz, amelyet a Azure AD B2C használ a jelek egymáshoz hozása, a döntéshozatal és a szervezeti szabályzatok betartatása érdekében. A kockázatfelmérés szabályzatfeltételekkel való automatizálása azt jelenti, hogy a kockázatos bejelentkezéseket egyszerre azonosítják, javítják vagy blokkolják.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protokoll

A **Protokoll** elem **Name (Név) attribútumát** a következőre kell beállítani: `Proprietary` . A **kezelőattribútumnak** tartalmaznia kell a protokollkezelő szerelvény teljes nevét, amelyet a Azure AD B2C:

```
Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

Az alábbi példa egy feltételes hozzáférés technikai profilját mutatja be:

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
```

## <a name="conditional-access-evaluation"></a>Feltételes hozzáférés kiértékelése

Minden bejelentkezéshez a Azure AD B2C kiértékeli az összes szabályzatot, és gondoskodik arról, hogy minden követelmény teljesül, mielőtt hozzáférést ad a felhasználónak. A "Hozzáférés blokkolása" felülbírálja az összes többi konfigurációs beállítást. A **feltételes** hozzáférés műszaki profiljának Kiértékelési módja kiértékeli a Azure AD B2C által a helyi fiókkal való bejelentkezés során gyűjtött jeleket. A feltételes hozzáférés technikai profiljának eredménye a feltételes hozzáférés értékeléséből eredő jogcímek halmaza. A Azure AD B2C szabályzat ezeket a jogcímeket egy következő vezénylési lépésben használja egy művelet éhez, például letiltja a felhasználót, vagy többtényezős hitelesítést ad a felhasználónak. Ehhez a módhoz a következő beállítások konfigurálhatóak.

### <a name="metadata"></a>Metaadatok

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| OperationType | Yes | Értékelésnek **kell lennie.**  |

### <a name="input-claims"></a>Bemeneti jogcímek

Az **InputClaims** elem tartalmazza a feltételes hozzáférésnek küldhető jogcímek listáját. A jogcím nevét a feltételes hozzáférés technikai profiljában meghatározott névre is leképezheti.

| ClaimReferenceId (Jogcím-igénylésazonosító) | Kötelező | Adattípus | Leírás |
| --------- | -------- | ----------- |----------- |
| UserId (Felhasználóazonosító) | Yes | sztring | A bejelentkező felhasználó azonosítója. |
| AuthenticationMethodsUsed | Yes |stringCollection | A felhasználó által a bejelentkezéshez használt módszerek listája. Lehetséges értékek: `Password` , és `OneTimePasscode` . |
| IsFederated (Befedve) | Yes |boolean | Azt jelzi, hogy egy felhasználó összevont fiókkal jelentkezett-e be. Az értéknek a következőnek kell lennie: `false` . |
| IsMfaRegistered (Regisztrálva) | Yes |boolean | Jelzi, hogy a felhasználó regisztrált-e már telefonszámot többtényezős hitelesítéshez. |


Az **InputClaimsTransformations** elem az **InputClaimsTransformation** elemek gyűjteményét tartalmazhatja, amelyek a bemeneti jogcímek módosítására vagy újak igénylésére használhatók, mielőtt elküldeik azokat a feltételes hozzáférési szolgáltatásnak.

### <a name="output-claims"></a>Kimeneti jogcímek

Az **OutputClaims** elem a ConditionalAccessProtocolProvider által létrehozott jogcímek listáját tartalmazza. A jogcím nevét az alább meghatározott névre is leképezheti.

| ClaimReferenceId (Jogcím-igénylésazonosító) | Kötelező | Adattípus | Leírás |
| --------- | -------- | ----------- |----------- |
| Problémák | Yes |stringCollection | Az azonosított fenyegetést javító műveletek listája. Lehetséges értékek: `block` |
| MultiConditionalAccessStatus | Yes | stringCollection |  |

Az **OutputClaimsTransformations** elem az **OutputClaimsTransformation** elemek gyűjteményét tartalmazhatja, amelyek a kimeneti jogcímek módosításához vagy újak létrehozásához használhatók.

### <a name="example-evaluation"></a>Például: Kiértékelés

Az alábbi példa egy feltételes hozzáférési technikai profilt mutat be, amely a bejelentkezési fenyegetés kiértékeléséhez használható.

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="IsMfaRegisteredCT" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="UserId" />
    <InputClaim ClaimTypeReferenceId="AuthenticationMethodsUsed" />
    <InputClaim ClaimTypeReferenceId="IsFederated" DefaultValue="false" />
    <InputClaim ClaimTypeReferenceId="IsMfaRegistered" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" PartnerClaimType="Challenges" />
    <OutputClaim ClaimTypeReferenceId="ConditionalAccessStatus" PartnerClaimType="MultiConditionalAccessStatus" />
  </OutputClaims>
</TechnicalProfile>
```

## <a name="remediation"></a>Szervizelés

A **feltételes hozzáférés műszaki** profiljának szervizelési módja Azure AD B2C arról, hogy a bejelentkezés által azonosított fenyegetést orvosolták. A szervizelési módhoz a következő beállítások konfigurálhatóak.

### <a name="metadata"></a>Metaadatok

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| OperationType | Yes | **Szervizelésnek kell lennie.**  |

### <a name="input-claims"></a>Bemeneti jogcímek

Az **InputClaims** elem tartalmazza a feltételes hozzáférésnek küldhető jogcímek listáját. A jogcím nevét a feltételes hozzáférés technikai profiljában meghatározott névre is leképezheti.

| ClaimReferenceId (Jogcím-igénylésazonosító) | Kötelező | Adattípus | Leírás |
| --------- | -------- | ----------- |----------- |
| KihívásokTitkos | Yes | stringCollection| Az azonosított fenyegetés kiértékelési módból való visszatéréséhez és a kihívásokhoz való visszatéréshez szükséges, megfelelő kihívások listája.|


Az **InputClaimsTransformations** elem tartalmazhat **inputClaimsTransformation** elemek gyűjteményét, amelyek a bemeneti jogcímek módosításához vagy újak létrehozásához használhatók a feltételes hozzáférési szolgáltatás hívása előtt.

### <a name="output-claims"></a>Kimeneti jogcímek

A feltételes hozzáférés protokollszolgáltatója nem ad vissza **OutputClaims** igénylést, így nincs szükség kimeneti jogcímek megadására. A feltételes hozzáférés protokollszolgáltatója által nem visszaadott jogcímeket azonban felhasználhatja, ha be van állítva az `DefaultValue` attribútum.

Az **OutputClaimsTransformations** elem az **OutputClaimsTransformation** elemek gyűjteményét tartalmazhatja, amelyek a kimeneti jogcímek módosításához vagy újak létrehozásához használhatók.

### <a name="example-remediation"></a>Például: Szervizelés

Az alábbi példa egy feltételes hozzáférési technikai profilt mutat be, amely a bejelentkezési fenyegetés megoldását mutatja be.

```xml
<TechnicalProfile Id="ConditionalAccessRemediation">
  <DisplayName>Conditional Access Remediation</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
  <Metadata>
    <Item Key="OperationType">Remediation</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" PartnerClaimType="ChallengesSatisfied" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [adhat feltételes hozzáférést a felhasználói folyamatokhoz a Azure Active Directory B2C.](conditional-access-user-flow.md)
