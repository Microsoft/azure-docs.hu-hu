---
title: FELHASZNÁLÓIFELÜLET-definíciós függvények létrehozása
description: A Azure Managed Applications felhasználói felületi definícióinak összeállításakor használandó függvények ismertetése
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: e4255f0d42e28a72ad55d9b7f81d0dc49b2950cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87040991"
---
# <a name="createuidefinition-functions"></a>CreateUiDefinition függvények

Ez a cikk áttekintést nyújt a CreateUiDefinition által támogatott függvényekről.

## <a name="function-syntax"></a>Függvény szintaxisa

A függvények használatához a szögletes zárójelekkel meghívást kell használni. Például:

```json
"[function()]"
```

A karakterláncok és egyéb függvények a függvények paramétereinek is szerepelhetnek, de a sztringeket aposztrófok között kell megadni. Például:

```json
"[fn1(fn2(), 'demo text')]"
```

Adott esetben a függvények kimenetének tulajdonságaira hivatkozhat a pont operátor használatával. Például:

```json
"[func().prop1]"
```

## <a name="collection-functions"></a>Gyűjteményfüggvények

Ezek a függvények gyűjtemények, például JSON-karakterláncok, tömbök és objektumok használatával használhatók.

* [tartalmaz](create-ui-definition-collection-functions.md#contains)
* [üres](create-ui-definition-collection-functions.md#empty)
* [szűrő](create-ui-definition-collection-functions.md#filter)
* [első](create-ui-definition-collection-functions.md#first)
* [utolsó](create-ui-definition-collection-functions.md#last)
* [length](create-ui-definition-collection-functions.md#length) (hossz)
* [Térkép](create-ui-definition-collection-functions.md#map)
* [kihagyása](create-ui-definition-collection-functions.md#skip)
* [felosztása](create-ui-definition-collection-functions.md#split)
* [eltarthat](create-ui-definition-collection-functions.md#take)

## <a name="conversion-functions"></a>Konverziós függvények

Ezek a függvények a JSON-adattípusok és a kódolások közötti értékek átalakítására használhatók.

* [logikai](create-ui-definition-conversion-functions.md#bool)
* [decodeBase64](create-ui-definition-conversion-functions.md#decodebase64)
* [decodeUriComponent](create-ui-definition-conversion-functions.md#decodeuricomponent)
* [encodeBase64](create-ui-definition-conversion-functions.md#encodebase64)
* [encodeUriComponent](create-ui-definition-conversion-functions.md#encodeuricomponent)
* [float](create-ui-definition-conversion-functions.md#float)
* [int](create-ui-definition-conversion-functions.md#int)
* [parse](create-ui-definition-conversion-functions.md#parse)
* [sztring](create-ui-definition-conversion-functions.md#string)

## <a name="date-functions"></a>Dátumfüggvények

* [addHours](create-ui-definition-date-functions.md#addhours)
* [addMinutes](create-ui-definition-date-functions.md#addminutes)
* [addSeconds](create-ui-definition-date-functions.md#addseconds)
* [utcNow](create-ui-definition-date-functions.md#utcnow)

## <a name="logical-functions"></a>Logikai függvények

Ezek a függvények feltételes használatban is használhatók. Előfordulhat, hogy egyes függvények nem támogatják az összes JSON-adattípust.

* [and](create-ui-definition-logical-functions.md#and)
* [összefonódik](create-ui-definition-logical-functions.md#coalesce)
* [egyenlő](create-ui-definition-logical-functions.md#equals)
* [greater](create-ui-definition-logical-functions.md#greater)
* [greaterOrEquals](create-ui-definition-logical-functions.md#greaterorequals)
* [Ha](create-ui-definition-logical-functions.md#if)
* [kisebb](create-ui-definition-logical-functions.md#less)
* [lessOrEquals](create-ui-definition-logical-functions.md#lessorequals)
* [nem](create-ui-definition-logical-functions.md#not)
* [vagy](create-ui-definition-logical-functions.md#or)

## <a name="math-functions"></a>Matematikai függvények

* [add](create-ui-definition-math-functions.md#add)
* [ceil](create-ui-definition-math-functions.md#ceil)
* [div](create-ui-definition-math-functions.md#div)
* [padló](create-ui-definition-math-functions.md#floor)
* [Max](create-ui-definition-math-functions.md#max)
* [p](create-ui-definition-math-functions.md#min)
* [mod](create-ui-definition-math-functions.md#mod)
* [mul](create-ui-definition-math-functions.md#mul)
* [Rand](create-ui-definition-math-functions.md#rand)
* [tartomány](create-ui-definition-math-functions.md#range)
* [Sub](create-ui-definition-math-functions.md#sub)

## <a name="referencing-functions"></a>Hivatkozási függvények

* [alapjai](create-ui-definition-referencing-functions.md#basics)
* [helyen](create-ui-definition-referencing-functions.md#location)
* [resourceGroup](create-ui-definition-referencing-functions.md#resourcegroup)
* [lépéseket](create-ui-definition-referencing-functions.md#steps)
* [előfizetés](create-ui-definition-referencing-functions.md#subscription)

## <a name="string-functions"></a>Sztringfüggvények

* [concat](create-ui-definition-string-functions.md#concat)
* [endsWith](create-ui-definition-string-functions.md#endswith)
* [guid](create-ui-definition-string-functions.md#guid)
* [indexOf](create-ui-definition-string-functions.md#indexof)
* [lastIndexOf](create-ui-definition-string-functions.md#lastindexof)
* [csere](create-ui-definition-string-functions.md#replace)
* [startsWith](create-ui-definition-string-functions.md#startswith)
* [substring](create-ui-definition-string-functions.md#substring)
* [toLower](create-ui-definition-string-functions.md#tolower)
* [toUpper](create-ui-definition-string-functions.md#toupper)

## <a name="next-steps"></a>Következő lépések

* A Azure Resource Manager bevezetését lásd: [Azure Resource Manager áttekintése](../management/overview.md).
