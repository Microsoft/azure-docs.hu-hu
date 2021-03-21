---
title: BuildingBlocks
titleSuffix: Azure AD B2C
description: A Azure Active Directory B2C egyéni házirendjének BuildingBlocks elemének megadásához.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a42c1d06051c283f0e911c4cd166884ddd060f45
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100633274"
---
# <a name="buildingblocks"></a>BuildingBlocks

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A **BuildingBlocks** elem hozzá van adva a [TrustFrameworkPolicy](trustframeworkpolicy.md) elemhez.

```xml
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
    <DisplayControls>
      ...
    </DisplayControls>
 </BuildingBlocks>
```

A **BuildingBlocks** elem a következő elemeket tartalmazza, amelyeket meg kell adni a megadott sorrendben:

- [ClaimsSchema](claimsschema.md) – meghatározza a szabályzat részeként hivatkozható jogcím-típusokat. A jogcím-séma az a hely, ahol deklarálja a jogcímek típusait. A jogcím típusa hasonló egy változóhoz számos programozási nyelven. A jogcím típusa segítségével adatokat gyűjthet az alkalmazás felhasználója számára, fogadhat jogcímeket a közösségi identitás-szolgáltatóktól, adatokat küldhet és fogadhat az egyéni REST APItól, vagy az egyéni házirend által használt belső adatokat is tárolhatja.

- [Predikátumok és PredicateValidationsInput](predicates.md) – lehetővé teszi egy érvényesítési folyamat elvégzését annak biztosítására, hogy csak a megfelelően formázott adatok legyenek beírva a jogcímek.

- [ClaimsTransformations](claimstransformations.md) – a szabályzatban használható jogcímek átalakításának listáját tartalmazza.  A jogcím-átalakítás egy jogcímet konvertál egy másikra. A jogcím-átalakításban meg kell adnia egy átalakítási metódust, például:
  - Egy karakterlánc-jogcím esetének módosítása a megadott értékre. Például egy sztring kisbetűsről nagybetűre való módosítása.
  - Két jogcím összevetése és a jogcímek visszaküldése igaz értékkel, ami azt jelzi, hogy a jogcímek egyeznek.
  - Karakterlánc-jogcím létrehozása a házirendben megadott paraméter alapján.
  - Véletlenszerű karakterlánc létrehozása a véletlenszám-generátor használatával.
  - Jogcím formázása a megadott formázó sztringnek megfelelően. Ez a transzformáció a C# `String.Format` metódust használja.

- InputValidation – ez az elem lehetővé teszi, hogy olyan logikai összesítéseket végezzen el, amelyek hasonlóak a *és* a és a *vagy* a rendszerhez.

- [ContentDefinitions](contentdefinitions.md) – a felhasználói úton használandó HTML5-sablonok URL-jeit tartalmazza. Egy egyéni szabályzatban a tartalom definíciója határozza meg a felhasználói út adott lépéséhez használt HTML5-oldal URI-JÁT. Például a bejelentkezés vagy a regisztráció, a jelszó alaphelyzetbe állítása vagy a hibák lapja. A megjelenést és a működést úgy változtathatja meg, hogy felülbírálja a HTML5-fájl Tartalomdefinícióban. Az igényeknek megfelelően új tartalmi definíciókat is létrehozhat. Ez az elem honosított erőforrás-referenciát tartalmazhat a honosítási azonosító használatával.

- [Honosítás](localization.md) – lehetővé teszi több nyelv támogatását. A házirendek honosítási támogatása lehetővé teszi, hogy beállítsa a szabályzatban támogatott nyelvek listáját, és válasszon egy alapértelmezett nyelvet. A nyelvspecifikus karakterláncok és gyűjtemények is támogatottak.

- [DisplayControls](display-controls.md) – meghatározza a lapon megjelenítendő vezérlőket. A megjelenítési vezérlők speciális funkciókkal rendelkeznek, és együttműködnek a háttér-ellenőrzési technikai profilokkal. 
