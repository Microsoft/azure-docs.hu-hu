---
title: Intelligens csoportok
description: Az intelligens csoportok olyan riasztások összesítései, amelyek segítenek csökkenteni a riasztási zajt
ms.topic: conceptual
ms.date: 05/15/2018
ms.openlocfilehash: 8a164fe6e5fd5be58da969d9266329755705ea82
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037965"
---
# <a name="smart-groups"></a>Intelligens csoportok

A riasztások kezelése során felmerülő gyakori kihívás, hogy megtudja, mi a helyzet, ha az intelligens csoportok a probléma megoldására szolgálnak.  

Az intelligens csoportok automatikusan létrejönnek a gépi tanulási algoritmusok használatával, amelyek egyetlen problémát jelképező kapcsolódó riasztásokat egyesítenek.  Riasztás létrehozásakor az algoritmus hozzáadja azt egy új intelligens csoporthoz vagy egy meglévő intelligens csoporthoz olyan információk alapján, mint például a korábbi minták, a hasonló tulajdonságok és a hasonló struktúra. Ha például egy előfizetésben egyszerre több virtuális gépen található% CPU több különböző riasztáshoz vezet, és ha az ilyen riasztások a múltban bármikor bekövetkeztek, akkor ezek a riasztások valószínűleg egyetlen intelligens csoportba lesznek csoportosítva, ami lehetséges általános kiváltó okot jelent. Ez azt jelenti, hogy ha valaki hibaelhárítási riasztást használ, az intelligens csoportok nem csak a zaj csökkentését teszik lehetővé a kapcsolódó riasztások egyetlen aggregált egységként való kezelésével, a riasztások lehetséges gyakori kiváltó okaival is.

Az algoritmus jelenleg csak ugyanazon figyelő szolgáltatásból származó riasztásokat tekinti át egy előfizetésen belül. Az intelligens csoportok a riasztási zaj 99%-át csökkenthetik a konszolidáción keresztül. Megtekintheti annak okát, hogy a riasztások a csoportba tartoznak az intelligens csoport részletei lapon.

Megtekintheti az intelligens csoportok részleteit, és a riasztásokhoz hasonlóan állíthatja be az állapotot is. Mindegyik riasztás egy és csak egy intelligens csoport tagja. 

## <a name="smart-group-state"></a>Intelligens csoport állapota

Az intelligens csoport állapota a riasztási állapothoz hasonló fogalom, amely lehetővé teszi a megoldási folyamat kezelését egy intelligens csoport szintjén. Hasonlóan a riasztási állapothoz, amikor egy intelligens csoportot hoznak létre, az **új** állapottal rendelkezik, amely módosítható a **nyugtázott** vagy a **lezárt** értékre is.

A következő intelligens csoportos állapotok támogatottak.

| Állam | Leírás |
|:---|:---|
| Új | A probléma nemrég észlelhető, és még nem lett ellenőrizve. |
| Nyugtázva | A rendszergazda áttekintette az intelligens csoportot, és megkezdte a munkát. |
| Lezárt | A probléma megoldódott. Egy intelligens csoport bezárása után újra megnyithatja azt egy másik állapotba való módosításával. |

[Megtudhatja, hogyan módosíthatja az intelligens csoport állapotát.](./alerts-managing-alert-states.md?toc=%2fazure%2fazure-monitor%2ftoc.json)

> [!NOTE]
>  Az intelligens csoportok állapotának módosítása nem változtatja meg az egyes tagokra vonatkozó riasztások állapotát.

## <a name="smart-group-details-page"></a>Intelligens csoport részletei lap

Ha intelligens csoportot választ, az intelligens csoport részletei lap jelenik meg. Részletes információkat nyújt az intelligens csoportról, beleértve a csoport létrehozásához használt indoklást is, és lehetővé teszi az állapotának módosítását.
 
![Intelligens csoport részletei](media/alerts-smartgroups-overview/smart-group-detail.png)


Az intelligens csoport részleteit tartalmazó oldal a következő részekből áll.

| Section | Leírás |
|:---|:---|
| Riasztások | Felsorolja az intelligens csoportban található egyes riasztásokat. Válasszon ki egy riasztást a riasztás részletei lap megnyitásához. |
| Előzmények | Felsorolja az intelligens csoport által végrehajtott összes műveletet, valamint a rajta végrehajtott módosításokat. Ez jelenleg csak az állapot változásaira és a riasztások tagságának változásaira korlátozódik. |

## <a name="smart-group-taxonomy"></a>Intelligens csoport besorolása

Az intelligens csoport neve az első riasztás neve. Intelligens csoportok nem hozhatók létre és nem nevezhetők át.

## <a name="next-steps"></a>Következő lépések

- [Intelligens csoportok kezelése](./alerts-managing-smart-groups.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
- [A riasztás és az intelligens csoport állapotának módosítása](./alerts-managing-alert-states.md?toc=%2fazure%2fazure-monitor%2ftoc.json)