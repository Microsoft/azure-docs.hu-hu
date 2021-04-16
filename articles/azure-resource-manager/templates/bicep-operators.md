---
title: Bicep-operátorok
description: Ismerteti a központi telepítéshez elérhető Bicep Azure Resource Manager operátorokat.
ms.topic: conceptual
ms.date: 04/15/2021
ms.openlocfilehash: 0838ebf6bc03f4237ef76e07f1eb6f25aa996fc0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537822"
---
# <a name="bicep-operators"></a>Bicep-operátorok

Ez a cikk azokat a Bicep-operátorokat ismerteti, amelyek akkor érhetők el, ha Létrehoz egy Bicep-sablont, és Azure Resource Manager erőforrások üzembe helyezéséhez. Az operátorok értékek kiszámítására, értékek összehasonlítására vagy feltételek kiértékeléséhez használhatók. A Bicep-operátorok háromféle típusa létezik:

- [Összehasonlítás](#comparison)
- [Logikai](#logical)
- [Numerikus](#numeric)

Egy kifejezés és közé való bezárása lehetővé teszi az alapértelmezett `(` `)` Bicep-operátor precedenciának felülbírálást. Az x + y /z kifejezés például először az osztást, majd az összeadást értékeli ki. Az (x + y) / z kifejezés azonban az összeadás első és osztás másodpercét értékeli ki.

## <a name="comparison"></a>Összehasonlítás

Az összehasonlító operátorok összehasonlítják az értékeket, és vagy vagy értéket `true` adnak `false` vissza.

| Operátor | Név | Leírás |
| ---- | ---- | ---- |
| `>=` | [Nagyobb vagy egyenlő mint](bicep-operators-comparison.md#greater-than-or-equal-) | Kiértékeli, hogy az első érték nagyobb vagy egyenlő-e a második értékkel. |
| `>`  | [Nagyobb, mint](bicep-operators-comparison.md#greater-than-) | Kiértékeli, hogy az első érték nagyobb-e a második értéknél. |
| `<=` | [Kisebb vagy egyenlő mint](bicep-operators-comparison.md#less-than-or-equal-) | Kiértékeli, hogy az első érték kisebb vagy egyenlő-e a második értékkel. |
| `<`  | [Kisebb, mint](bicep-operators-comparison.md#less-than-) | Kiértékeli, hogy az első érték kisebb-e a második értéknél. |
| `==` | [Egyenlő](bicep-operators-comparison.md#equals-) | Kiértékeli, hogy két érték egyenlő-e. |
| `!=` | [Nem egyenlő](bicep-operators-comparison.md#not-equal-) | Kiértékeli, hogy két érték **nem egyenlő-e.** |
| `=~` | [A kis- és a kis- és a kis- és a nagy- és a kis- és a nagy- és a kis- és a](bicep-operators-comparison.md#equal-case-insensitive-) | Figyelmen kívül hagyja a kis- és a kis- és a nagy |
| `!~` | [Nem egyenlő a kis- és a nagy- és a kis- és a nagy- és a kis](bicep-operators-comparison.md#not-equal-case-insensitive-) | Figyelmen kívül hagyja a kis- és a kis- és a nagy  |

## <a name="logical"></a>Logikai

A logikai operátorok kiértékelik a logikai értékeket, nem null értékeket adnak vissza, vagy kiértékelnek egy feltételes kifejezést.

| Operátor | Név | Leírás |
| ---- | ---- | ---- |
| `&&` | [És](bicep-operators-logical.md#and-) | Értéket `true` ad vissza, ha az összes érték igaz. |
| `||`| [Vagy](bicep-operators-logical.md#or-) | Értéket `true` ad vissza, ha bármelyik érték igaz. |
| `!` | [Nem](bicep-operators-logical.md#not-) | Logikai értéket ad vissza. |
| `??` | [Coalesce (Összeesés)](bicep-operators-logical.md#coalesce-) | Az első nem null értéket adja vissza. |
| `?` `:` | [Feltételes kifejezés](bicep-operators-logical.md#conditional-expression--) | Kiértékel egy igaz vagy hamis feltételt, és visszaad egy értéket. |

## <a name="numeric"></a>Numerikus

A numerikus operátorok egész számokkal végeznek számításokat, és egész számértékeket adnak vissza.

| Operátor | Név | Leírás |
| ---- | ---- | ---- |
| `*` | [Szorzás](bicep-operators-numeric.md#multiply-) | Két egész szám szorzása. |
| `/` | [Osztani](bicep-operators-numeric.md#divide-) | Egy egész szám elosztja egy egész számra. |
| `%` | [Moduló](bicep-operators-numeric.md#modulo-) | Egy egész szám elosztja egy egész számra, és a maradékot adja vissza. |
| `+` | [Hozzáadás](bicep-operators-numeric.md#add-) | Két egész szám összead. |
| `-` | [Kivonás](bicep-operators-numeric.md#subtract--) | Egy egész szám kivonása egy egész számból. |
| `-` | [Mínusz](bicep-operators-numeric.md#minus--) | Egy egész szám szorzása a `-1` következővel: . |

> [!NOTE]
> A Kivonás és a Mínusz operátort használja. A funkció más, mert a kivonás két operandusból, a mínusz pedig egy operandusból áll.

## <a name="next-steps"></a>Következő lépések

- Bicep-fájl létrehozásához lásd: [Oktatóanyag: Első bicep Azure Resource Manager létrehozása és telepítése.](bicep-tutorial-create-first-bicep.md)
- A Bicep-típusokkal kapcsolatos hibák elhárításával kapcsolatos információkért lásd: [Bármely függvény a Bicephez.](template-functions-any.md)
- A Bicep és a JSON szintaxisának összehasonlításával [lásd: Comparing JSON and Bicep for templates (A JSON és a Bicep összehasonlítása sablonokhoz).](compare-template-syntax.md)
- Példák a Bicep- és ARM-sablonf függvények használatára: [ARM-sablonf függvények.](template-functions.md)
