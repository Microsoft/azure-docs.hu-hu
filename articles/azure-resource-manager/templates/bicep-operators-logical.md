---
title: Bicep logikai operátorok
description: Leírja a feltételt kiértékelő bicep logikai operátorokat.
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 1eecbe589ecf08e32246dd97d137c60fa7b5078f
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107211310"
---
# <a name="bicep-logical-operators"></a>Bicep logikai operátorok

A logikai operátorok kiértékelik a logikai értékeket, nem null értékű értékeket adnak vissza, vagy kiértékelnek egy feltételes kifejezést. A példák futtatásához az Azure CLI vagy a Azure PowerShell használatával [helyezzen üzembe egy bicep-fájlt](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

| Operátor | Name |
| ---- | ---- |
| `&&` | [És](#and-) |
| `||` | [Vagy](#or-) |
| `!` | [Nem](#not-) |
| `??` | [Összefonódik](#coalesce-) |
| `?` `:` | [Feltételes kifejezés](#conditional-expression--) |

## <a name="and-"></a>És &&

`operand1 && operand2`

Meghatározza, hogy mindkét érték igaz-e.

### <a name="operands"></a>Operandusok

| Operandus | Típus | Description |
| ---- | ---- | ---- |
| `operand1` | boolean | Az első érték, amely az igaz értéket jelöli. |
| `operand2` | boolean | A második érték, amely az igaz értéket vizsgálja. |
| További operandusok | boolean | További operandusok is lehetnek. |

### <a name="return-value"></a>Visszatérési érték

`True` Ha mindkét érték igaz, `false` akkor a rendszer a visszaadott értéket adja vissza.

### <a name="example"></a>Példa

Kiértékeli a paraméterek értékeit és a kifejezések egy halmazát.

```bicep
param operand1 bool = true
param operand2 bool = true

output andResultParm bool = operand1 && operand2
output andResultExp bool = bool(10 >= 10) && bool(5 > 2)
```

A példa kimenete:

| Név | Típus | Érték |
| ---- | ---- | ---- |
| `andResultParm` | boolean | true |
| `andResultExp` | boolean | true |

## <a name="or-"></a>Vagy | |

`operand1 || operand2`

Meghatározza, hogy az egyik érték igaz-e.

### <a name="operands"></a>Operandusok

| Operandus | Típus | Description |
| ---- | ---- | ---- |
| `operand1` | boolean | Az első érték, amely az igaz értéket jelöli. |
| `operand2` | boolean | A második érték, amely az igaz értéket vizsgálja. |
| További operandusok | boolean | További operandusok is lehetnek. |

### <a name="return-value"></a>Visszatérési érték

`True` Ha bármelyik érték igaz, `false` akkor a rendszer a visszaadott értéket adja vissza.

### <a name="example"></a>Példa

Kiértékeli a paraméterek értékeit és a kifejezések egy halmazát.

```bicep
param operand1 bool = true
param operand2 bool = false

output orResultParm bool = operand1 || operand2
output orResultExp bool = bool(10 >= 10) || bool(5 < 2)
```

A példa kimenete:

| Név | Típus | Érték |
| ---- | ---- | ---- |
| `orResultParm` | boolean | true |
| `orResultExp` | boolean | true |

## <a name="not-"></a>Nem!

`!boolValue`

Egy logikai értéket tagad meg.

### <a name="operand"></a>Operandus

| Operandus | Típus | Description |
| ---- | ---- | ---- |
| `boolValue` | boolean | A megtagadott logikai érték. |

### <a name="return-value"></a>Visszatérési érték

Megtagadja a kezdeti értéket, és logikai értéket ad vissza. Ha a kezdeti érték `true` , akkor a `false` rendszer a visszaadott értéket adja vissza.

### <a name="example"></a>Példa

Az `not` operátor megtagad egy értéket. Az értékeket zárójelekkel lehet becsomagolni.

```bicep
param initTrue bool = true
param initFalse bool = false

output startedTrue bool = !(initTrue)
output startedFalse bool = !initFalse
```

A példa kimenete:

| Név | Típus | Érték |
| ---- | ---- | ---- |
| `startedTrue` | boolean | hamis |
| `startedFalse` | boolean | true |

## <a name="coalesce-"></a>Egyesíteni?

`operand1 ?? operand2`

Az operandusokból származó első nem null értéket adja vissza.

### <a name="operands"></a>Operandusok

| Operandus | Típus | Description |
| ---- | ---- | ---- |
| `operand1` | karakterlánc, egész szám, logikai, objektum, tömb | A vizsgálandó érték `null` . |
| `operand2` | karakterlánc, egész szám, logikai, objektum, tömb | A vizsgálandó érték `null` . |
| További operandusok | karakterlánc, egész szám, logikai, objektum, tömb | A vizsgálandó érték `null` . |

### <a name="return-value"></a>Visszatérési érték

Az első nem null értéket adja vissza. Üres karakterláncok, üres tömbök és üres objektumok nem, `null` és a \<empty> rendszer egy értéket ad vissza.

### <a name="example"></a>Példa

A kimeneti utasítások a nem null értékeket adják vissza. A kimeneti típusnak meg kell egyeznie az összehasonlításban szereplő típussal, vagy egy hiba jön létre.

```bicep
param myObject object = {
  'isnull1': null
  'isnull2': null
  'string': 'demoString'
  'emptystr': ''
  'integer': 10
  }

output nonNullStr string = myObject.isnull1 ?? myObject.string ?? myObject.isnull2
output nonNullInt int = myObject.isnull1 ?? myObject.integer ?? myObject.isnull2
output nonNullEmpty string = myObject.isnull1 ?? myObject.emptystr ?? myObject.string ?? myObject.isnull2
```

A példa kimenete:

| Név | Típus | Érték |
| ---- | ---- | ---- |
| `nonNullStr` | sztring | demoString |
| `nonNullInt` | int | 10 |
| `nonNullEmpty` | sztring | \<empty> |

## <a name="conditional-expression--"></a>Feltételes kifejezés? :

`condition ? true-value : false-value`

Kiértékel egy feltételt, és visszaad egy értéket, ha a feltétel igaz vagy hamis.

### <a name="operands"></a>Operandusok

| Operandus | Típus | Description |
| ---- | ---- | ---- |
| `condition` | boolean | Az igaz vagy a hamis érték kiértékelésének feltétele. |
| `true-value` | karakterlánc, egész szám, logikai, objektum, tömb | Az érték, ha a feltétel igaz. |
| `false-value` | karakterlánc, egész szám, logikai, objektum, tömb | Az érték, ha a feltétel hamis. |

### <a name="example"></a>Példa

Ez a példa kiértékel egy paraméter kezdeti értékét, és visszaad egy értéket, ha a feltétel igaz vagy hamis.

```bicep
param initValue bool = true

output outValue string = initValue ? 'true value' : 'false value'
```

A példa kimenete:

| Név | Típus | Érték |
| ---- | ---- | ---- |
| `outValue` | sztring | Igaz érték |

## <a name="next-steps"></a>Következő lépések

- A bicep-fájlok létrehozásáról az [oktatóanyag: első Azure Resource Manager bicep-fájl létrehozása és központi telepítése](bicep-tutorial-create-first-bicep.md)című témakörben olvashat.
- A bicep típusú hibák elhárításával kapcsolatos további információkért lásd: [bármely függvény a bicep](template-functions-any.md).
- A bicep és a JSON szintaxisának összehasonlításához tekintse meg [a JSON és a bicep a sablonok összehasonlítását](compare-template-syntax.md)ismertető témakört.
- A bicep és az ARM template függvények példáit lásd: [ARM template functions](template-functions.md).
