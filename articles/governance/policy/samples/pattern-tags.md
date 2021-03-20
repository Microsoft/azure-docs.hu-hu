---
title: 'Minta: címkék használata házirend-definícióban'
description: Ez a Azure Policy minta példákat tartalmaz a paraméteres címkék hozzáadására vagy a címkék öröklésére egy házirend-definícióban lévő erőforráscsoport használatával.
ms.date: 08/17/2020
ms.topic: sample
ms.openlocfilehash: 3016fc7889f68fd13e993c67ca645a4af055c651
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88545556"
---
# <a name="azure-policy-pattern-tags"></a>Azure Policy minta: Címkék

A [címkék](../../..//azure-resource-manager/management/tag-resources.md) fontos részét képezik az Azure-erőforrások kezelésének, rendszerezésének és szabályozásának. Azure Policy lehetővé teszi, hogy a címkék az új és a meglévő erőforrásokra is érvényesek legyenek a [módosítás](../concepts/effects.md#modify) hatásával és a [szervizelési feladatokkal](../how-to/remediate-resources.md).

## <a name="sample-1-parameterize-tags"></a>1. példa: Parametrizálja Címkék

Ez a házirend-definíció két paramétert használ: **TagName** és **tagValue** , amelyekkel beállíthatja, hogy a szabályzat-hozzárendelés mire keres az erőforráscsoportok esetében. Ez a formátum lehetővé teszi, hogy a házirend-definíció tetszőleges számú címkét és címke értékű kombinációt adjon meg, de csak egyetlen házirend-definíciót tart fenn.

> [!NOTE]
> Habár ez a házirend-definíciós minta a következő [mintában látható: paraméterek-Sample #1](./pattern-parameters.md#sample-1-string-parameters), ez a példa a **Mode** _all_ és a Targets erőforráscsoportot használja.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json":::

### <a name="sample-1-explanation"></a>1. példa: magyarázat

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="2-8" highlight="3":::

Ebben a példában a **mód** az _összes_ , mert egy erőforráscsoportot céloz meg. A legtöbb esetben a **üzemmódot** _indexelve_ kell beállítani a címkék használatakor. További információ: [üzemmódok](../concepts/definition-structure.md#resource-manager-modes).

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="26-36" highlight="7-8":::

A házirend-definíció ezen részében a `concat` a paraméteres **TagName** paraméter és a `tags['name']` Tell **mező** formátuma határozza meg, hogy a **tagValue** paraméter kiértékelje a címkét.
A **notEquals** használata esetén, ha **a \[ címkék \] TagName** nem egyenlő **tagValue**, a rendszer elindítja a **módosítás** hatását.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="43-47" highlight="3-4":::

Itt a **addOrReplace** művelet ugyanazt a formátumot használja a paraméteres címke értékeinek használatához, hogy a címkét a kiértékelt erőforráscsoport kívánt értékére hozza létre vagy frissítse.

## <a name="sample-2-inherit-tag-value-from-resource-group"></a>2. minta: a címke értékének öröklése az erőforráscsoport alapján

Ez a házirend-definíció a **TagName** paraméter használatával határozza meg, hogy melyik címke értéke örökli a szülő erőforráscsoportot.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json":::

### <a name="sample-2-explanation"></a>2. minta: magyarázat

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="2-8" highlight="3":::

Ebben a példában a **mód** _indexelve_ értékre van állítva, mert nem céloz meg erőforráscsoportot vagy előfizetést, még akkor is, ha az egy erőforráscsoport értékét kapja meg. További információ: [üzemmódok](../concepts/definition-structure.md#resource-manager-modes).

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="19-29" highlight="3-4,7-8":::

A **' policyrule osztály. if** `concat` például [minta #1](#sample-1-parameterize-tags) használja a **TagName** értékének kiértékeléséhez, de a `resourceGroup()` függvénnyel összehasonlítja a szülő erőforráscsoport azonos címkéjének értékével. A második záradék itt ellenőrzi, hogy az erőforráscsoport címkéje tartalmaz-e értéket, és nem null értékű.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="36-40" highlight="3-4":::

Itt az erőforráshoz tartozó **TagName** -címkéhez rendelt érték a `resourceGroup()` szülő erőforráscsoport értékének beolvasására is a függvényt használja. Így örökölheti a címkéket a szülő erőforráscsoportokből. Ha már létrehozta az erőforrást, de nem adta hozzá a címkét, akkor ugyanez a házirend-definíció és egy [szervizelési feladat](../how-to/remediate-resources.md) frissítheti a meglévő erőforrásokat.

## <a name="next-steps"></a>Következő lépések

- Tekintse át [a többi mintázatot és a beépített definíciókat](./index.md).
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).