---
title: ClaimsTransformations – Azure Active Directory B2C | Microsoft Docs
description: A ClaimsTransformations elem definíciója az Azure Active Directory B2C identitás-keretrendszer sémájában.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 37d9bd78a80ac52d2a790537bf47e33807720349
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85202959"
---
# <a name="claimstransformations"></a>ClaimsTransformations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A **ClaimsTransformations** elem tartalmazza a jogcímek átalakítási funkcióinak listáját, amelyeket a felhasználói útvonalakban használhat [Egyéni szabályzat](custom-policy-overview.md)részeként. A jogcím-átalakítás a megadott jogcímet egy másik jogcímmé alakítja. A jogcím-átalakításban megadhatja az átalakítási metódust, például hozzáadhat egy elemeket egy karakterlánc-gyűjteményhez, vagy megváltoztathatja a karakterláncok esetét.

A felhasználói úton használható jogcím-átalakítási függvények listájának felvételéhez egy ClaimsTransformations XML-elemet kell deklarálni a szabályzat BuildingBlocks szakaszában.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
    ...
  </ClaimsTransformation>
</ClaimsTransformations>
```

A **ClaimsTransformation** elem a következő attribútumokat tartalmazza:

| Attribútum |Kötelező | Leírás |
| --------- |-------- | ----------- |
| Id |Yes | Egy azonosító, amely a jogcím-átalakítás egyedi azonosítására szolgál. Az azonosító a házirendben szereplő egyéb XML-elemektől hivatkozik. |
| TransformationMethod | Yes | A jogcím-átalakításban használandó átalakítási módszer. Minden jogcím-átalakítás saját értékekkel rendelkezik. A rendelkezésre álló értékek teljes listájáért tekintse meg a [jogcím-átalakítási referenciát](#claims-transformations-reference) . |

## <a name="claimstransformation"></a>ClaimsTransformation

A **ClaimsTransformation** elem a következő elemeket tartalmazza:

```xml
<ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
  <InputClaims>
    ...
  </InputClaims>
  <InputParameters>
    ...
  </InputParameters>
  <OutputClaims>
    ...
  </OutputClaims>
</ClaimsTransformation>
```


| Elem | Események | Description |
| ------- | -------- | ----------- |
| Szabályzattípushoz | 0:1 | Azoknak a **InputClaim** -elemeknek a listája, amelyek a jogcím-átalakításhoz bemenetként bevitt jogcímeket határozzák meg. Ezen elemek mindegyike egy, a szabályzat ClaimsSchema szakaszában már definiált ClaimType mutató hivatkozást tartalmaz. |
| InputParameters | 0:1 | A jogcím-átalakításhoz bemenetként megadott **InputParameter** -elemek listája.
| OutputClaims | 0:1 | Azon **OutputClaim** elemek listája, amelyek a ClaimsTransformation meghívása után előállított jogcímeket határozzák meg. Ezen elemek mindegyike egy, a ClaimsSchema szakaszban már definiált ClaimType mutató hivatkozást tartalmaz. |

### <a name="inputclaims"></a>Szabályzattípushoz

A **szabályzattípushoz** elem a következő elemet tartalmazza:

| Elem | Események | Description |
| ------- | ----------- | ----------- |
| InputClaim | 1: n | Egy várt bemeneti jogcím típusa. |

#### <a name="inputclaim"></a>InputClaim

A **InputClaim** elem a következő attribútumokat tartalmazza:

| Attribútum |Kötelező | Leírás |
| --------- | ----------- | ----------- |
| ClaimTypeReferenceId |Yes | A szabályzat ClaimsSchema szakaszában már definiált ClaimType mutató hivatkozás. |
| TransformationClaimType |Yes | Egy átalakítási jogcím típusára hivatkozó azonosító. Minden jogcím-átalakítás saját értékekkel rendelkezik. A rendelkezésre álló értékek teljes listájáért tekintse meg a [jogcím-átalakítási referenciát](#claims-transformations-reference) . |

### <a name="inputparameters"></a>InputParameters

A **InputParameters** elem a következő elemet tartalmazza:

| Elem | Események | Description |
| ------- | ----------- | ----------- |
| InputParameter | 1: n | Egy várt bemeneti paraméter. |

#### <a name="inputparameter"></a>InputParameter

| Attribútum | Kötelező |Leírás |
| --------- | ----------- |----------- |
| Id | Yes | Egy azonosító, amely a jogcím-átalakítási módszer egyik paraméterére hivatkozik. Minden jogcím-átalakítási módszer saját értékekkel rendelkezik. A rendelkezésre álló értékek teljes listájáért tekintse meg a jogcím-átalakítási táblázatot. |
| DataType | Yes | A paraméter adattípusa (például string, Boolean, int vagy DateTime) az egyéni házirend XML-sémájában az adattípus enumerálása alapján. Ez a típus a aritmetikai műveletek megfelelő végrehajtásához használatos. Minden jogcím-átalakítás saját értékekkel rendelkezik. A rendelkezésre álló értékek teljes listájáért tekintse meg a [jogcím-átalakítási referenciát](#claims-transformations-reference) . |
| Érték | Yes | Egy olyan érték, amely szó szerint az átalakításhoz lett átadva. Egyes értékek tetszőlegesek, néhányat a jogcím-átalakítási módszer közül választhat ki. |

### <a name="outputclaims"></a>OutputClaims

A **OutputClaims** elem a következő elemet tartalmazza:

| Elem | Események | Description |
| ------- | ----------- | ----------- |
| OutputClaim | 0: n | A várt kimeneti jogcím típusa. |

#### <a name="outputclaim"></a>OutputClaim

A **OutputClaim** elem a következő attribútumokat tartalmazza:

| Attribútum |Kötelező | Leírás |
| --------- | ----------- |----------- |
| ClaimTypeReferenceId | Yes | A szabályzat ClaimsSchema szakaszában már definiált ClaimType mutató hivatkozás.
| TransformationClaimType | Yes | Egy átalakítási jogcím típusára hivatkozó azonosító. Minden jogcím-átalakítás saját értékekkel rendelkezik. A rendelkezésre álló értékek teljes listájáért tekintse meg a [jogcím-átalakítási referenciát](#claims-transformations-reference) . |

Ha a bemeneti jogcím és a kimeneti jogcím azonos típusú (karakterlánc vagy logikai), akkor ugyanazt a bemeneti jogcímet használhatja, mint a kimeneti jogcím. Ebben az esetben a jogcím-átalakítás megváltoztatja a bemeneti jogcímet a kimeneti értékkel.

## <a name="example"></a>Példa

Előfordulhat például, hogy a felhasználó által elfogadott szolgáltatási feltételek utolsó verzióját tárolja. A szolgáltatások használati feltételeinek frissítésekor megkérheti a felhasználót, hogy fogadja el az új verziót. A következő példában a **HasTOSVersionChanged** jogcím-átalakítás összehasonlítja a **TOSVersion** -jogcím értékét az **LastTOSAcceptedVersion** jogcím értékével, majd visszaadja a logikai **TOSVersionChanged** jogcímet.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="TOSVersionChanged">
      <DisplayName>Indicates if the TOS version accepted by the end user is equal to the current version</DisplayName>
      <DataType>boolean</DataType>
    </ClaimType>
    <ClaimType Id="TOSVersion">
      <DisplayName>TOS version</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    <ClaimType Id="LastTOSAcceptedVersion">
      <DisplayName>TOS version accepted by the end user</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <ClaimsTransformation Id="HasTOSVersionChanged" TransformationMethod="CompareClaims">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="TOSVersion" TransformationClaimType="inputClaim1" />
        <InputClaim ClaimTypeReferenceId="LastTOSAcceptedVersion" TransformationClaimType="inputClaim2" />
      </InputClaims>
      <InputParameters>
        <InputParameter Id="operator" DataType="string" Value="NOT EQUAL" />
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="TOSVersionChanged" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

## <a name="claims-transformations-reference"></a>Jogcím-átalakítások referenciája

A jogcímek átalakítására vonatkozó példákért tekintse meg a következő hivatkozási lapokat:

- [Logikai](boolean-transformations.md)
- [Date](date-transformations.md)
- [Egész szám](integer-transformations.md)
- [JSON](json-transformations.md)
- [Telefonszám](phone-number-claims-transformations.md)
- [Általános](general-transformations.md)
- [Közösségi fiók](social-transformations.md)
- [Sztring](string-transformations.md)
- [StringCollection](stringcollection-transformations.md)

