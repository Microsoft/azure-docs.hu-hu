---
title: Mintamodellek
description: A minta modellek forrásait sorolja fel.
author: florianborn71
ms.author: flborn
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 6817601659c841ca98031f4e3e1590743bbed171
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530535"
---
# <a name="sample-models"></a>Mintamodellek

Ez a cikk az Azure távoli renderelési szolgáltatás teszteléséhez használható mintaadatok egyes erőforrásait sorolja fel.

## <a name="built-in-sample-model"></a>Beépített minta modell

Egy beépített minta modellt biztosítunk, amely mindig betölthető az URL- **Builtin://Engine** használatával

![Mintamodell](./media/sample-model.png "Mintamodell")

Modell statisztikái:

| Name | Érték |
|-----------|:-----------|
| [Szükséges kiszolgáló mérete](../reference/vm-sizes.md) | Standard |
| Háromszögek száma | 18 700 000 |
| Mozgó részek száma | 2073 |
| Anyagok száma | 94 |

## <a name="third-party-data"></a>Harmadik féltől származó adatszolgáltatások

A Khronos csoport a teszteléshez glTF-mintákat tart fenn. Az ARR a glTF formátumot is támogatja szöveg (*. glTF*) és bináris (*. borai*) formában. Javasoljuk, hogy használja a PBR-modelleket a legjobb vizuális eredményekhez:

* [glTF-minták](https://github.com/KhronosGroup/glTF-Sample-Models)

## <a name="next-steps"></a>Következő lépések

* [Gyors útmutató: modell megjelenítése egységgel](../quickstarts/render-model.md)
* [Gyorsútmutató: Modell konvertálása a renderelés előtt](../quickstarts/convert-model.md)
