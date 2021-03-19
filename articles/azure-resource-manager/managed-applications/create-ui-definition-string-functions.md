---
title: FELHASZNÁLÓIFELÜLET-definíciós karakterlánc-függvények létrehozása
description: A Azure Managed Applications felhasználói felületi definícióinak összeállításakor használandó karakterlánc-függvények ismertetése
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: c662948542c36cd93f889ca045ee245c15c7bb11
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87096940"
---
# <a name="createuidefinition-string-functions"></a>CreateUiDefinition karakterlánc-függvények

Ezek a függvények JSON-karakterláncokkal használhatók.

## <a name="concat"></a>concat

Egy vagy több karakterlánc összefűzése.

Ha például a kimenet értéke `element1` IF `"Contoso"` , akkor ez a példa a következő karakterláncot adja vissza `"Demo Contoso app"` :

```json
"[concat('Demo ', steps('step1').element1, ' app')]"
```

## <a name="endswith"></a>endsWith

Meghatározza, hogy egy karakterlánc egy értékkel végződik-e.

Az alábbi minta igaz értéket ad vissza.

```json
"[endsWith('tuvwxyz', 'xyz')]"
```

## <a name="guid"></a>guid

Globálisan egyedi karakterláncot (GUID) hoz létre.

A következő példa egy értéket ad vissza, például `"c7bc8bdc-7252-4a82-ba53-7c468679a511"` :

```json
"[guid()]"
```

## <a name="indexof"></a>indexOf

Egy érték első pozícióját adja vissza egy sztringen belül, vagy-1, ha nem található.

A következő minta a 2 értéket adja vissza.

```json
"[indexOf('abcdef', 'cd')]"
```

## <a name="lastindexof"></a>lastIndexOf

Egy karakterlánc vagy-1 érték utolsó pozícióját adja vissza, ha nem található.

A következő minta a 3 értéket adja vissza.

```json
"[lastIndexOf('test', 't')]"
```

## <a name="replace"></a>csere

Egy olyan karakterláncot ad vissza, amelyben az aktuális karakterláncban szereplő megadott sztring összes előfordulása egy másik karakterlánccal van lecserélve.

Az alábbi példa a következőt adja vissza `"Contoso.com web app"` :

```json
"[replace('Contoso.net web app', '.net', '.com')]"
```

## <a name="startswith"></a>startsWith

Meghatározza, hogy egy karakterlánc egy értékkel kezdődik-e.

Az alábbi minta igaz értéket ad vissza.

```json
"[startsWith('abcdefg', 'ab')]"
```

## <a name="substring"></a>substring

A megadott karakterlánc alsztringjét adja vissza. Az alkarakterlánc a megadott indexnél kezdődik, és a megadott hosszúságú.

Az alábbi példa a következőt adja vissza `"web"` :

```json
"[substring('Contoso.com web app', 12, 3)]"
```

## <a name="tolower"></a>toLower

Egy kisbetűsre konvertált karakterláncot ad vissza.

Az alábbi példa a következőt adja vissza `"contoso"` :

```json
"[toLower('CONTOSO')]"
```

## <a name="toupper"></a>toUpper

Egy nagybetűre konvertált karakterláncot ad vissza.

Az alábbi példa a következőt adja vissza `"CONTOSO"` :

```json
"[toUpper('contoso')]"
```

## <a name="next-steps"></a>Következő lépések

* A Azure Resource Manager bevezetését lásd: [Azure Resource Manager áttekintése](../management/overview.md).

