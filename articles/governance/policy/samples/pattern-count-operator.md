---
title: 'Minta: egy házirend-definícióban szereplő Count operátor'
description: Ez a Azure Policy minta azt szemlélteti, hogyan használható a Count operátor egy házirend-definícióban.
ms.date: 10/14/2020
ms.topic: sample
ms.openlocfilehash: 1339dff7f8bc92a8e38ec5635690cc2069dd8df4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96005418"
---
# <a name="azure-policy-pattern-the-count-operator"></a>Azure Policy minta: a Count operátor

A [Count](../concepts/definition-structure.md#count) operátor kiértékeli az alias tagjait \[ \* \] .

## <a name="sample-policy-definition"></a>Példa a házirend-definícióra

Ez a házirend-definíció [naplózza](../concepts/effects.md#audit) a bejövő RDP protokoll (RDP) forgalom engedélyezésére konfigurált hálózati biztonsági csoportokat.

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json":::

### <a name="explanation"></a>Magyarázat

A **Count** operátor alapvető összetevői a _mezők_, _ahol_ és a feltételt. Mindegyik az alábbi kódrészletben van kiemelve.

- a _mező_ a tagok kiértékeléséhez szükséges [aliasok](../concepts/definition-structure.md#aliases) számát mutatja. Itt a hálózati biztonsági csoport **\[ \* \] securityRules** alias _tömbjét_ tekintjük meg.
- _ahol_ a a szabályzat nyelvét használja annak meghatározásához, hogy mely _tömb_ tagjai felelnek meg a feltételeknek. Ebben a példában egy **allOf** logikai operátor az alias _tömb_ tulajdonságainak három különböző feltétel-értékelését csoportosítja: _irány_, _hozzáférés_ és _destinationPortRange_.
- Ebben a példában a Count feltétel **nagyobb**. A Count értéke TRUE (igaz), ha az alias _tömb_ egy vagy több tagja megegyezik a _Where_ záradékkal.

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json" range="12-32" highlight="3,4,20":::

## <a name="next-steps"></a>Következő lépések

- Tekintse át [a többi mintázatot és a beépített definíciókat](./index.md).
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).