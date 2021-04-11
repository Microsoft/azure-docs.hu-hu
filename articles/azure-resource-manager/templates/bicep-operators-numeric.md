---
title: Bicep numerikus operátorok
description: Az értékeket kiszámító bicep numerikus operátorokat ismerteti.
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: f2c7f722a3f13648c94b69039d31e5095a3256f7
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107211301"
---
# <a name="bicep-numeric-operators"></a>Bicep numerikus operátorok

A numerikus operátorok egész számokat használnak a számítások elvégzéséhez és egész értékek visszaküldéséhez. A példák futtatásához az Azure CLI vagy a Azure PowerShell használatával [helyezzen üzembe egy bicep-fájlt](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

| Operátor | Name |
| ---- | ---- |
| `*` | [Szorzás](#multiply-) |
| `/` | [Osztani](#divide-) |
| `%` | [Moduló](#modulo-) |
| `+` | [Hozzáadás](#add-) |
| `-` | [Kivonás](#subtract--) |
| `-` | [Mínusz](#minus--) |

> [!NOTE]
> A kivonás és a mínusz ugyanazt a kezelőt használja. A funkció eltérő, mert a kivonás két operandust használ, és a mínusz egy operandust használ.

## <a name="multiply-"></a>Szorzása

`operand1 * operand2`

Két egész szám összeszorzása.

### <a name="operands"></a>Operandusok

| Operandus | Típus | Description |
| ---- | ---- | ---- |
| `operand1`  | egész szám | A szorzáshoz használandó szám. |
| `operand2`  | egész szám  | A szám szorzója. |

### <a name="return-value"></a>Visszatérési érték

A szorzás a terméket egész számként adja vissza.

### <a name="example"></a>Példa

Két egész számot kell megszorozni, és visszaadni a terméket.

```bicep
param firstInt int = 5
param secondInt int = 2

output product int = firstInt * secondInt
```

A példa kimenete:

| Név | Típus | Érték |
| ---- | ---- | ---- |
| `product` | egész szám | 10 |

## <a name="divide-"></a>Osztani

`operand1 / operand2`

Egész számot oszt szét.

### <a name="operands"></a>Operandusok

| Operandus | Típus | Description |
| ---- | ---- | ---- |
| `operand1` | egész szám | A felosztott egész szám. |
| `operand2` | egész szám | Az osztáshoz használt egész szám. Nem lehet nulla. |

### <a name="return-value"></a>Visszatérési érték

Az osztás egész számként adja vissza a hányadost.

### <a name="example"></a>Példa

A rendszer két egész számot oszt ki, és visszaküldi a hányadost.

```bicep
param firstInt int = 10
param secondInt int = 2

output quotient int = firstInt / secondInt
```

A példa kimenete:

| Név | Típus | Érték |
| ---- | ---- | ---- |
| `quotient` | egész szám | 5 |

## <a name="modulo-"></a>Modulo

`operand1 % operand2`

Egész számot oszt szét, és a maradékot adja vissza.

### <a name="operands"></a>Operandusok

| Operandus | Típus | Description |
| ---- | ---- | ---- |
| `operand1`  | egész szám  | A felosztott egész szám. |
| `operand2`  | egész szám  | Az osztáshoz használt egész szám. Nem lehet 0. |

### <a name="return-value"></a>Visszatérési érték

A maradékot egész számként adja vissza. Ha a divízió nem hoz létre maradékot, a rendszer 0 értéket ad vissza.

### <a name="example"></a>Példa

A rendszer két pár egész számot oszt szét, és visszaküldi a maradékokat.

```bicep
param firstInt int = 10
param secondInt int = 3

param thirdInt int = 8
param fourthInt int = 4

output remainder int = firstInt % secondInt
output zeroRemainder int = thirdInt % fourthInt
```

A példa kimenete:

| Név | Típus | Érték |
| ---- | ---- | ---- |
| `remainder` | egész szám | 1 |
| `zeroRemainder` | egész szám | 0 |

## <a name="add-"></a>Hozzáadás +

`operand1 + operand2`

Két egész számot hoz létre.

### <a name="operands"></a>Operandusok

| Operandus | Típus | Description |
| ---- | ---- | ---- |
| `operand1` | egész szám | Hozzáadandó szám. |
| `operand2` | egész szám | Egy számhoz hozzáadott szám. |

### <a name="return-value"></a>Visszatérési érték

A Hozzáadás az összeget egész számként adja vissza.

### <a name="example"></a>Példa

A rendszer két egész számot ad hozzá, és visszaadja az összeget.

```bicep
param firstInt int = 10
param secondInt int = 2

output sum int = firstInt + secondInt
```

A példa kimenete:

| Név | Típus | Érték |
| ---- | ---- | ---- |
| `sum` | egész szám | 12 |

## <a name="subtract--"></a>Kivonása

`operand1 - operand2`

Egész számból kivonja az egész számot.

### <a name="operands"></a>Operandusok

| Operandus | Típus | Description |
| ---- | ---- | ---- |
| `operand1` | egész szám | A kivonni kívánt nagyobb szám. |
| `operand2` | egész szám | A nagyobb számból kivont szám. |

### <a name="return-value"></a>Visszatérési érték

A kivonás a különbséget egész számként adja vissza.

### <a name="example"></a>Példa

A rendszer kivonja az egész számot, és visszaadja a különbséget.

```bicep
param firstInt int = 10
param secondInt int = 4

output difference int = firstInt - secondInt
```

A példa kimenete:

| Név | Típus | Érték |
| ---- | ---- | ---- |
| `difference` | egész szám | 6 |

## <a name="minus--"></a>Mínusz

`-integerValue`

Egész szám összeszorzása `-1` .

### <a name="operand"></a>Operandus

| Operandus | Típus | Description |
| ---- | ---- | ---- |
| `integerValue` | egész szám | Egész szám szorozva `-1` . |

### <a name="return-value"></a>Visszatérési érték

Az egész számot szorozza meg `-1` . A pozitív egész szám negatív egész számot ad vissza, a negatív egész szám pedig pozitív egész számot ad vissza. Az értékeket zárójelekkel lehet becsomagolni.

### <a name="example"></a>Példa

```bicep
param posInt int = 10
param negInt int = -20

output startedPositive int = -posInt
output startedNegative int = -(negInt)
```

A példa kimenete:

| Név | Típus | Érték |
| ---- | ---- | ---- |
| `startedPositive` | egész szám | -10 |
| `startedNegative` | egész szám | 20 |

## <a name="next-steps"></a>Következő lépések

- A bicep-fájlok létrehozásáról az [oktatóanyag: első Azure Resource Manager bicep-fájl létrehozása és központi telepítése](bicep-tutorial-create-first-bicep.md)című témakörben olvashat.
- A bicep típusú hibák elhárításával kapcsolatos további információkért lásd: [bármely függvény a bicep](template-functions-any.md).
- A bicep és a JSON szintaxisának összehasonlításához tekintse meg [a JSON és a bicep a sablonok összehasonlítását](compare-template-syntax.md)ismertető témakört.
- A bicep és az ARM template függvények példáit lásd: [ARM template functions](template-functions.md).
