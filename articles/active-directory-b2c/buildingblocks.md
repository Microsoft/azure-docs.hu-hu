---
title: BuildingBlocks – az Azure Active Directory B2C |} A Microsoft Docs
description: Adja meg az egyéni szabályzat BuildingBlocks elem Azure Active Directory B2C-t.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: afa064232c10d3e84e9c301d44b82faae4904253
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60383954"
---
# <a name="buildingblocks"></a>BuildingBlocks

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A **BuildingBlocks** elem belül bekerül a [TrustFrameworkPolicy](trustframeworkpolicy.md) elemet.

```XML
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">

  <BuildingBlocks>
    <ClaimsSchema>
      ...
    </ClaimsSchema>
    <Predicates>
    ...
    </Predicates>
    <PredicateValidations>
    ...
    </PredicateValidations>
    <ClaimsTransformations>
      ...
    </ClaimsTransformations>
    <ContentDefinitions>
      ...
    </ContentDefinitions>
    <Localization>
      ...
    </Localization>
 </BuildingBlocks>
```

A **BuildingBlocks** elem a meghatározott sorrendben kell adni a következő elemeket tartalmazza:

- [ClaimsSchema](claimsschema.md) – határozza meg, amely lehet hivatkozni a jogcímtípusok, amelyeket a szabályzat részeként. A jogcímek séma az a hely, ahol a jogcímtípusok deklarálhatja. Számos programozási nyelvet egy változót egy jogcímtípust hasonlít. A jogcímtípus segítségével adatokat gyűjteni az alkalmazás a felhasználó, jogcímeket fogad a közösségi identitásszolgáltatókat, adatokat küldeni és fogadni egy egyéni REST API-ból, vagy az egyéni házirend által használt belső adatokat tárolja. 

- [Predikátumok és PredicateValidationsInput](predicates.md) – lehetővé teszi, hogy egy érvényesítési folyamat győződjön meg arról, hogy csak a megfelelően formázott adatok írja be a rendszer egy jogcímet.
 
- [ClaimsTransformations](claimstransformations.md) -jogcímek átalakítása a szabályzatban használt listáját tartalmazza.  A jogcímek átalakításáról alakít át egy jogcím egy másik. A jogcímek átalakítását, adja meg egy átalakítási módszer, például: 
    - A kis-és a megadott karakterlánc jogcím módosítása. Például egy karakterlánc történő módosítása kisbetűket nagybetűvé.
    - Két jogcímek összehasonlításával, és a egy jogcímet igaz jelzi, hogy a jogcímek megfelel, egyébként hamis értéket visszaadó.
    - A házirendben a megadott paraméter egy karakterlánc jogcímet hoz létre.
    - A véletlenszám-generátor használatával véletlenszerű karakterlánc létrehozása.
    - Formázási jogcím alapján megadott formázó karakterlánc. Az átalakítás használja a C# `String.Format` metódust.

- [ContentDefinitions](contentdefinitions.md) -tartalmaz URL-címek a HTML5-sablonok használata a felhasználói interakciósorozat. Egyéni házirend tartalomdefiníció határozza meg a HTML5-alapú oldal URI-ja, amely egy adott lépés a felhasználói interakciósorozatban szereplő szolgál. Ha például a bejelentkezési vagy regisztrációs, jelszó-visszaállítás, vagy hibalapok. A HTML5-fájl a LoadUri letiltásával módosíthatja a megjelenését és működését. Vagy létrehozhat új tartalomdefiníciók igény szerint. Ez az elem egy honosítási azonosítójával honosított erőforrások hivatkozást tartalmazhat

- [A honosítás](localization.md) – lehetővé teszi, hogy több nyelv támogatása. A szabályzatok a honosítási támogatás lehetővé teszi, hogy beállít egy házirendet a támogatott nyelvek listáját, és válasszon egy alapértelmezett nyelvet. Nyelvspecifikus karakterláncok és a gyűjtemények is támogatottak.


