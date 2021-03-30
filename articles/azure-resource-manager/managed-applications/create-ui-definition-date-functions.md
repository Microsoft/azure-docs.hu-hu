---
title: Felhasználói felületi definíció dátumának létrehozása függvények
description: A DÁTUMÉRTÉK használatakor használandó függvények leírása.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 80484fd15e51bae7036c43bd3ca8fe8167387ede
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87096958"
---
# <a name="createuidefinition-date-functions"></a>CreateUiDefinition Date functions

A dátumok értékének használatakor használandó függvények

## <a name="addhours"></a>addHours

A megadott időbélyeghez tartozó egész számú órát ad hozzá.

Az alábbi példa a következőt adja vissza `"1991-01-01T00:59:59.000Z"` :

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="addminutes"></a>addMinutes

Egy egész számú percet ad hozzá a megadott időbélyeghez.

Az alábbi példa a következőt adja vissza `"1991-01-01T00:00:59.000Z"` :

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

## <a name="addseconds"></a>addSeconds
Egy egész számú másodpercet ad hozzá a megadott időbélyeghez.

Az alábbi példa a következőt adja vissza `"1991-01-01T00:00:00.000Z"` :

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

## <a name="utcnow"></a>utcNow

A helyi számítógépen az aktuális dátum és idő ISO 8601 formátumú karakterláncát adja vissza.

A következő példa visszatérhet `"1990-12-31T23:59:59.000Z"` :

```json
"[utcNow()]"
```

## <a name="next-steps"></a>Következő lépések

* A Azure Resource Manager bevezetését lásd: [Azure Resource Manager áttekintése](../management/overview.md).
