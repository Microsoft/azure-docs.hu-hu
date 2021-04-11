---
title: Bicep operátorok
description: A Azure Resource Manager üzemelő példányokhoz elérhető bicep operátorokat ismerteti.
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 4bf1005a11b1dcfea9f4b28d6bd3fa7c33e3278f
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107211309"
---
# <a name="bicep-operators"></a>Bicep operátorok

Ez a cikk azokat a bicep operátorokat ismerteti, amelyek akkor érhetők el, amikor létrehoz egy bicep-sablont, és a Azure Resource Manager használatával helyezi üzembe az erőforrásokat. Az operátorok az értékek kiszámításához, az értékek összehasonlításához vagy a feltételek kiértékeléséhez használatosak. A bicep operátorok három típusa létezik: [összehasonlítás](#comparison), [logikai](#logical)és [numerikus](#numeric).

## <a name="comparison"></a>Összehasonlítás

Az összehasonlító operátorok összehasonlítják az értékeket, és a vagy a értéket adják vissza `true` `false` .

| Operátor | Név | Leírás |
| ---- | ---- | ---- |
| `>=` | [Nagyobb vagy egyenlő mint](bicep-operators-comparison.md#greater-than-or-equal-) | Kiértékeli, hogy az első érték nagyobb-e vagy egyenlő-e a második értékkel. |
| `>`  | [Nagyobb, mint](bicep-operators-comparison.md#greater-than-) | Kiértékeli, hogy az első érték nagyobb-e a második értéknél. |
| `<=` | [Kisebb vagy egyenlő mint](bicep-operators-comparison.md#less-than-or-equal-) | Kiértékeli, hogy az első érték kisebb-e vagy egyenlő-e a második értékkel. |
| `<`  | [Kisebb, mint](bicep-operators-comparison.md#less-than-) | Kiértékeli, hogy az első érték kisebb-e a második értéknél. |
| `==` | [Egyenlő](bicep-operators-comparison.md#equals-) | Kiértékeli, hogy két érték egyenlő-e. |
| `!=` | [Nem egyenlő](bicep-operators-comparison.md#not-equal-) | Kiértékeli, hogy két érték **nem** egyenlő-e. |
| `=~` | [Egyenlő kis-és nagybetűk](bicep-operators-comparison.md#equal-case-insensitive-) | Figyelmen kívül hagyja az esetet annak megállapításához, hogy két érték egyenlő-e. |
| `!~` | [Nem egyenlő kis-és nagybetűk](bicep-operators-comparison.md#not-equal-case-insensitive-) | Figyelmen kívül hagyja az esetet annak megállapításához, hogy két érték **nem** egyenlő-e. |

## <a name="logical"></a>Logikai

A logikai operátorok kiértékelik a logikai értékeket, nem null értékű értékeket adnak vissza, vagy kiértékelnek egy feltételes kifejezést.

| Operátor | Név | Leírás |
| ---- | ---- | ---- |
| `&&` | [És](bicep-operators-logical.md#and-) | Visszaadja `true` , ha az összes érték igaz. |
| `||`| [Vagy](bicep-operators-logical.md#or-) | `true`Ha bármelyik érték igaz, akkor a függvény visszaadja. |
| `!` | [Nem](bicep-operators-logical.md#not-) | Egy logikai értéket tagad meg. |
| `??` | [Összefonódik](bicep-operators-logical.md#coalesce-) | Az első nem null értéket adja vissza. |
| `?` `:` | [Feltételes kifejezés](bicep-operators-logical.md#conditional-expression--) | Kiértékeli a feltételt TRUE vagy FALSE értékre, és egy értéket ad vissza. |

## <a name="numeric"></a>Numerikus

A numerikus operátorok egész számokat használnak a számítások elvégzéséhez és egész értékek visszaküldéséhez.

| Operátor | Név | Leírás |
| ---- | ---- | ---- |
| `*` | [Szorzás](bicep-operators-numeric.md#multiply-) | Két egész szám összeszorzása. |
| `/` | [Osztani](bicep-operators-numeric.md#divide-) | Egész számot oszt szét. |
| `%` | [Moduló](bicep-operators-numeric.md#modulo-) | Egész számot oszt szét, és a maradékot adja vissza. |
| `+` | [Hozzáadás](bicep-operators-numeric.md#add-) | Két egész számot hoz létre. |
| `-` | [Kivonás](bicep-operators-numeric.md#subtract--) | Egész számból kivonja az egész számot. |
| `-` | [Mínusz](bicep-operators-numeric.md#minus--) | Egész szám összeszorzása `-1` . |

> [!NOTE]
> A kivonás és a mínusz ugyanazt a kezelőt használja. A funkció eltérő, mert a kivonás két operandust használ, és a mínusz egy operandust használ.

## <a name="next-steps"></a>Következő lépések

- A bicep-fájlok létrehozásáról az [oktatóanyag: első Azure Resource Manager bicep-fájl létrehozása és központi telepítése](bicep-tutorial-create-first-bicep.md)című témakörben olvashat.
- A bicep típusú hibák elhárításával kapcsolatos további információkért lásd: [bármely függvény a bicep](template-functions-any.md).
- A bicep és a JSON szintaxisának összehasonlításához tekintse meg [a JSON és a bicep a sablonok összehasonlítását](compare-template-syntax.md)ismertető témakört.
- A bicep és az ARM template függvények példáit lásd: [ARM template functions](template-functions.md).
