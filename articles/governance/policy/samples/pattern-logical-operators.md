---
title: 'Minta: logikai operátorok egy házirend-definícióban'
description: Ez a Azure Policy minta bemutatja, hogyan használhatók a logikai operátorok egy házirend-definícióban.
ms.date: 03/31/2021
ms.topic: sample
ms.openlocfilehash: feb9e50b0c73c19027b747cf0f95fa1cb6fbd47c
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106093350"
---
# <a name="azure-policy-pattern-logical-operators"></a>Azure Policy minta: logikai operátorok

Egy házirend-definíció több feltételes utasítást is tartalmazhat. Előfordulhat, hogy minden utasításnak igaz értékűnek kell lennie, vagy csak néhánynak igaznak kell lennie. Ezeknek az igényeknek a támogatásához a nyelvnek [logikai operátorai](../concepts/definition-structure.md#logical-operators) vannak a **nem**, a **allOf** és a **anyOf**. Ezeket nem kötelező megadni, és összetett forgatókönyvek létrehozásához is beágyazható.

## <a name="sample-1-one-logical-operator"></a>1. példa: egy logikai operátor

Ez a szabályzat-definíció kiértékeli [Azure Cosmos db](../../../cosmos-db/introduction.md) fiókokat, hogy az automatikus feladatátvételek és a több írási hely van-e konfigurálva. Ha nem, a [naplózás](../concepts/effects.md#audit) elindítja és létrehoz egy naplóbejegyzést a nem megfelelő erőforrás létrehozásakor vagy frissítésekor.

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json":::

### <a name="sample-1-explanation"></a>1. példa: magyarázat

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json" range="6-22" highlight="3":::

A **' policyrule osztály. if** blokk egyetlen **allOf** használ annak biztosítására, hogy mindhárom feltétel igaz legyen.
Csak akkor, ha az összes ilyen feltétel igaz értékre van állítva, a **naplózási** effektus aktiválva van.

## <a name="sample-2-multiple-logical-operators"></a>2. minta: több logikai operátor

Ez a házirend-definíció kiértékeli az erőforrásokat egy elnevezési mintához. Ha egy erőforrás nem egyezik, a rendszer [megtagadja](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json":::

### <a name="sample-2-explanation"></a>2. minta: magyarázat

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json" range="7-21" highlight="2,3,9":::

Ez a **' policyrule osztály. Ha** a Letiltás egyetlen **allOf** is tartalmaz, az egyes feltételek a **nem** logikai operátorral vannak becsomagolva. A **nem** logikai operátoron belüli feltételesség először kiértékeli, majd kiértékeli **, hogy a** teljes záradék igaz vagy hamis. Ha a logikai operátorok egyike **sem** igaz, a rendszer elindítja a házirend hatását.

## <a name="sample-3-combining-logical-operators"></a>3. minta: logikai operátorok egyesítése

Ez a szabályzat-definíció kiértékeli az Azure-fiókok [rugóját](/azure/developer/java/spring-framework) , hogy ellenőrizze, hogy nincs-e engedélyezve a nyomkövetés, vagy a nyomkövetés nem sikeres állapotban van-e.

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-3.json":::

### <a name="sample-3-explanation"></a>3. minta: magyarázat

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-3.json" range="6-28" highlight="3,8":::

Ez a **' policyrule osztály. Ha** a blokk a **AllOf** és a **anyOf** logikai operátort is tartalmazza. A **anyOf** logikai operátor igaz értéket ad vissza, feltéve, hogy az egyik feltétel igaz. Ahogy a _típus_ a **allOf** magja, mindig igaz értéket kell kiértékelnie. Ha a _típus_ és a **anyOf** egyik feltétele igaz, a rendszer elindítja a házirend-effektust.

## <a name="next-steps"></a>Következő lépések

- Tekintse át [a többi mintázatot és a beépített definíciókat](./index.md).
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).