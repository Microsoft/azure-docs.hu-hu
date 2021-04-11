---
title: Bicep összehasonlító operátorok
description: Leírja a bicep összehasonlító operátorokat, amelyek az értékeket hasonlítják össze.
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: c382ef355131d271d9f4fa4a978a807b9aac1e4f
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107211302"
---
# <a name="bicep-comparison-operators"></a>Bicep összehasonlító operátorok

Az összehasonlító operátorok összehasonlítják az értékeket, és a vagy a értéket adják vissza `true` `false` . A példák futtatásához az Azure CLI vagy a Azure PowerShell használatával [helyezzen üzembe egy bicep-fájlt](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

| Operátor | Name |
| ---- | ---- |
| `>=` | [Nagyobb vagy egyenlő mint](#greater-than-or-equal-) |
| `>`  | [Nagyobb, mint](#greater-than-) |
| `<=` | [Kisebb vagy egyenlő mint](#less-than-or-equal-) |
| `<`  | [Kisebb, mint](#less-than-) |
| `==` | [Egyenlő](#equals-) |
| `!=` | [Nem egyenlő](#not-equal-) |
| `=~` | [Egyenlő kis-és nagybetűk](#equal-case-insensitive-) |
| `!~` | [Nem egyenlő kis-és nagybetűk](#not-equal-case-insensitive-) |

## <a name="greater-than-or-equal-"></a>Nagyobb vagy egyenlő >=

`operand1 >= operand2`

Kiértékeli, hogy az első érték nagyobb-e vagy egyenlő-e a második értékkel.

### <a name="operands"></a>Operandusok

| Operandus | Típus | Description |
| ---- | ---- | ---- |
| `operand1` | egész szám, karakterlánc | Az összehasonlítás első értéke. |
| `operand2` | egész szám, karakterlánc | Az összehasonlítás második értéke. |

### <a name="return-value"></a>Visszatérési érték

Ha az első érték nagyobb vagy egyenlő, mint a második érték, a `true` rendszer a visszaadott értéket adja vissza. Ellenkező esetben `false` a függvény a visszaadott értéket adja vissza.

### <a name="example"></a>Példa

A rendszer összehasonlítja az egész számokból és a karakterláncokból álló párokat.

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'A'
param secondString string = 'A'

output intGtE bool = firstInt >= secondInt
output stringGtE bool = firstString >= secondString
```

A példa kimenete:

| Név | Típus | Érték |
| ---- | ---- | ---- |
| `intGtE` | boolean | true |
| `stringGtE` | boolean | true |

## <a name="greater-than-"></a>Nagyobb, mint >

`operand1 > operand2`

Kiértékeli, hogy az első érték nagyobb-e a második értéknél.

### <a name="operands"></a>Operandusok

| Operandus | Típus | Description |
| ---- | ---- | ---- |
| `operand1` | egész szám, karakterlánc | Az összehasonlítás első értéke. |
| `operand2` | egész szám, karakterlánc | Az összehasonlítás második értéke. |

### <a name="return-value"></a>Visszatérési érték

Ha az első érték nagyobb, mint a második érték, a függvény a `true` visszaadott értéket adja vissza. Ellenkező esetben `false` a függvény a visszaadott értéket adja vissza.

### <a name="example"></a>Példa

A rendszer összehasonlítja az egész számokból és a karakterláncokból álló párokat.

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'bend'
param secondString string = 'band'

output intGt bool = firstInt > secondInt
output stringGt bool = firstString > secondString
```

A példa kimenete:

Az **e** a **kanyarban** az első sztring nagyobb lesz.

| Név | Típus | Érték |
| ---- | ---- | ---- |
| `intGt` | boolean | true |
| `stringGt` | boolean | true |

## <a name="less-than-or-equal-"></a>Kisebb vagy egyenlő <=

`operand1 <= operand2`

Kiértékeli, hogy az első érték kisebb-e vagy egyenlő-e a második értékkel.

### <a name="operands"></a>Operandusok

| Operandus | Típus | Description |
| ---- | ---- | ---- |
| `operand1` | egész szám, karakterlánc | Az összehasonlítás első értéke. |
| `operand2` | egész szám, karakterlánc | Az összehasonlítás második értéke. |

### <a name="return-value"></a>Visszatérési érték

Ha az első érték kisebb vagy egyenlő, mint a második érték, a `true` rendszer a visszaadott értéket adja vissza. Ellenkező esetben `false` a függvény a visszaadott értéket adja vissza.

### <a name="example"></a>Példa

A rendszer összehasonlítja az egész számokból és a karakterláncokból álló párokat.

```bicep
param firstInt int = 5
param secondInt int = 10

param firstString string = 'demo'
param secondString string = 'demo'

output intLtE bool = firstInt <= secondInt
output stringLtE bool = firstString <= secondString
```

A példa kimenete:

| Név | Típus | Érték |
| ---- | ---- | ---- |
| `intLtE` | boolean | true |
| `stringLtE` | boolean | true |

## <a name="less-than-"></a>Kisebb, mint <

`operand1 < operand2`

Kiértékeli, hogy az első érték kisebb-e a második értéknél.

### <a name="operands"></a>Operandusok

| Operandus | Típus | Description |
| ---- | ---- | ---- |
| `operand1` | egész szám, karakterlánc | Az összehasonlítás első értéke. |
| `operand2` | egész szám, karakterlánc | Az összehasonlítás második értéke. |

### <a name="return-value"></a>Visszatérési érték

Ha az első érték kisebb, mint a második érték, a `true` rendszer a visszaadott értéket adja vissza. Ellenkező esetben `false` a függvény a visszaadott értéket adja vissza.

### <a name="example"></a>Példa

A rendszer összehasonlítja az egész számokból és a karakterláncokból álló párokat.

```bicep
param firstInt int = 5
param secondInt int = 10

param firstString string = 'demo'
param secondString string = 'Demo'

output intLt bool = firstInt < secondInt
output stringLt bool = firstString < secondString
```

A példa kimenete:

A karakterlánc azért van, mert a kisbetűk `true` kisebbek, mint a nagybetűk.

| Név | Típus | Érték |
| ---- | ---- | ---- |
| `intLt` | boolean | true |
| `stringLt` | boolean | true |

## <a name="equals-"></a>Egyenlő = =

`operand1 == operand2`

Kiértékeli, hogy az értékek egyenlőek-e.

### <a name="operands"></a>Operandusok

| Operandus | Típus | Description |
| ---- | ---- | ---- |
| `operand1` | karakterlánc, egész szám, logikai, tömb, objektum | Az összehasonlítás első értéke. |
| `operand2` | karakterlánc, egész szám, logikai, tömb, objektum | Az összehasonlítás második értéke. |

### <a name="return-value"></a>Visszatérési érték

Ha az operandusok egyenlőek, a függvény a `true` visszaadott értéket adja vissza. Ha az operandusok eltérnek, a függvény a `false` visszaadott értéket adja vissza.

### <a name="example"></a>Példa

A rendszer összehasonlítja az egész szám, karakterlánc és logikai érték párokat.

```bicep
param firstInt int = 5
param secondInt int = 5

param firstString string = 'demo'
param secondString string = 'demo'

param firstBool bool = true
param secondBool bool = true

output intEqual bool = firstInt == secondInt
output stringEqual bool = firstString == secondString
output boolEqual bool = firstBool == secondBool
```

A példa kimenete:

| Név | Típus | Érték |
| ---- | ---- | ---- |
| `intEqual` | boolean | true |
| `stringEqual` | boolean | true |
| `boolEqual` | boolean | true |

## <a name="not-equal-"></a>Nem egyenlő! =

`operand1 != operand2`

Kiértékeli, hogy két érték **nem** egyenlő-e.

### <a name="operands"></a>Operandusok

| Operandus | Típus | Description |
| ---- | ---- | ---- |
| `operand1` | karakterlánc, egész szám, logikai, tömb, objektum | Az összehasonlítás első értéke. |
| `operand2` | karakterlánc, egész szám, logikai, tömb, objektum | Az összehasonlítás második értéke. |

### <a name="return-value"></a>Visszatérési érték

Ha az operandusok **nem** egyenlőek, a függvény a `true` visszaadott értéket adja vissza. Ha az operandusok egyenlőek, a függvény a `false` visszaadott értéket adja vissza.

### <a name="example"></a>Példa

A rendszer összehasonlítja az egész szám, karakterlánc és logikai érték párokat.

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'demo'
param secondString string = 'test'

param firstBool bool = false
param secondBool bool = true

output intNotEqual bool = firstInt != secondInt
output stringNotEqual bool = firstString != secondString
output boolNotEqual bool = firstBool != secondBool
```

A példa kimenete:

| Név | Típus | Érték |
| ---- | ---- | ---- |
| `intNotEqual` | boolean | true |
| `stringNotEqual` | boolean | true |
| `boolNotEqual` | boolean | true |

## <a name="equal-case-insensitive-"></a>Egyenlő kis-és nagybetűk között = ~

`operand1 =~ operand2`

Figyelmen kívül hagyja az esetet annak megállapításához, hogy a két érték egyenlő-e.

### <a name="operands"></a>Operandusok

| Operandus | Típus | Description |
| ---- | ---- | ---- |
| `operand1`  | sztring | Az összehasonlítás első karakterlánca. |
| `operand2`  | sztring | Második sztring az összehasonlításban. |

### <a name="return-value"></a>Visszatérési érték

Ha a karakterláncok egyenlőek, a függvény a `true` visszaadott értéket adja vissza. Ellenkező esetben `false` a függvény a visszaadott értéket adja vissza.

### <a name="example"></a>Példa

Összehasonlítja a kevert betűket használó karakterláncokat.

```bicep
param firstString string = 'demo'
param secondString string = 'DEMO'

param thirdString string = 'demo'
param fourthString string = 'TEST'

output strEqual1 bool = firstString =~ secondString
output strEqual2 bool = thirdString =~ fourthString
```

A példa kimenete:

| Név | Típus | Érték |
| ---- | ---- | ---- |
| `strEqual1` | boolean | true |
| `strEqual2` | boolean | hamis |

## <a name="not-equal-case-insensitive-"></a>Nem egyenlő kis-és nagybetűk nélkül! ~

`operand1 !~ operand2`

Figyelmen kívül hagyja az esetet annak megállapításához, hogy a két érték **nem** egyenlő-e.

### <a name="operands"></a>Operandusok

| Operandus | Típus | Description |
| ---- | ---- | ---- |
| `operand1` | sztring | Az összehasonlítás első karakterlánca. |
| `operand2` | sztring | Második sztring az összehasonlításban. |

### <a name="return-value"></a>Visszatérési érték

Ha a karakterláncok **nem** egyenlőek, a függvény a `true` visszaadott értéket adja vissza. Ellenkező esetben `false` a függvény a visszaadott értéket adja vissza.

### <a name="example"></a>Példa

Összehasonlítja a kevert betűket használó karakterláncokat.

```bicep
param firstString string = 'demo'
param secondString string = 'TEST'

param thirdString string = 'demo'
param fourthString string = 'DeMo'

output strNotEqual1 bool = firstString !~ secondString
output strEqual2 bool = thirdString !~ fourthString
```

A példa kimenete:

| Név | Típus | Érték |
| ---- | ---- | ---- |
| `strNotEqual1` | boolean | true |
| `strNotEqual2` | boolean | hamis |

## <a name="next-steps"></a>Következő lépések

- A bicep-fájlok létrehozásáról az [oktatóanyag: első Azure Resource Manager bicep-fájl létrehozása és központi telepítése](bicep-tutorial-create-first-bicep.md)című témakörben olvashat.
- A bicep típusú hibák elhárításával kapcsolatos további információkért lásd: [bármely függvény a bicep](template-functions-any.md).
- A bicep és a JSON szintaxisának összehasonlításához tekintse meg [a JSON és a bicep a sablonok összehasonlítását](compare-template-syntax.md)ismertető témakört.
- A bicep és az ARM template függvények példáit lásd: [ARM template functions](template-functions.md).
