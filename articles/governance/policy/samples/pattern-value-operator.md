---
title: 'Minta: egy házirend-definícióban szereplő érték operátor'
description: Ez a Azure Policy minta azt szemlélteti, hogyan használható a Value operátor egy házirend-definícióban.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: ace7b7cd4a765cdb8c7aa764b52b180c60508ab2
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172785"
---
# <a name="azure-policy-pattern-the-value-operator"></a>Azure Policy minta: az érték operátor

Az [érték](../concepts/definition-structure.md#value) kezelője kiértékeli a [paramétereket](../concepts/definition-structure.md#parameters), a [támogatott sablon függvényeit](../concepts/definition-structure.md#policy-functions)vagy a konstansokat egy adott [feltétel](../concepts/definition-structure.md#conditions)megadott értékére.

> [!WARNING]
> Ha egy _sablon függvény_ eredménye hibát jelez, a szabályzat kiértékelése sikertelen lesz. A sikertelen értékelés implicit **Megtagadás**. További információ: a [sablon meghibásodásának elkerülése](../concepts/definition-structure.md#avoiding-template-failures).

## <a name="sample-policy-definition"></a>Példa a házirend-definícióra

Ez a szabályzat-definíció hozzáadja vagy lecseréli a **TagName** (_karakterlánc_) paraméterben megadott címkét az erőforrásokon, és örökli a **TagName** értékét az erőforrás által használt erőforráscsoport alapján. Ez az értékelés az erőforrás létrehozásakor vagy frissítésekor történik. A [módosítás](../concepts/effects.md#modify) hatására előfordulhat, hogy a szervizelés a meglévő erőforrásokon egy [szervizelési feladaton](../how-to/remediate-resources.md)keresztül fut.

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json":::

### <a name="explanation"></a>Magyarázat

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json" range="20-30" highlight="7,8":::

Az **érték** operátor a ' policyrule osztály belül használatos **. Ha** a **tulajdonságban**blokk található. Ebben a példában a [logikai operátor](../concepts/definition-structure.md#logical-operators) **allOf** azt adja meg, hogy mindkét feltételes utasításnak igaznak kell lennie a hatás, a **módosítás**, a végrehajtás érdekében.

az **érték** a [resourceGroup ()](../../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup) függvény eredményét értékeli ki egy üres érték **notEquals** . Ha a szülő erőforráscsoport **TagName** megadott címke neve létezik, a feltételes kiértékelés igaz értéket ad vissza.

## <a name="next-steps"></a>Következő lépések

- Tekintse át [a többi mintázatot és a beépített definíciókat](./index.md).
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).