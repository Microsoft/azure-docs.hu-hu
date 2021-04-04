---
title: Windows rendszerű virtuális asztali gazdagép-készlet terheléselosztása – Azure
description: Ismerje meg a Windows rendszerű virtuális asztali környezethez tartozó gazdagép-készlet terheléselosztási módszereit.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: fd8f9e4a3ef63cd97f96af3d4f96a2bb65c3cd09
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91951859"
---
# <a name="host-pool-load-balancing-methods"></a>Gazdagépcsoport terheléselosztási módszerei

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali Azure Resource Manager Windows virtuális asztali objektumokkal vonatkozik. Ha a Windowsos virtuális asztalt (klasszikus) Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/host-pool-load-balancing-2019.md).

A Windows rendszerű virtuális asztali gépek két terheléselosztási módszert támogatnak. Az egyes módszerek határozzák meg, hogy melyik munkamenet-állomás fogja tárolni a felhasználó munkamenetét, amikor egy gazdagépen lévő erőforráshoz csatlakoznak.

A következő terheléselosztási módszerek érhetők el a Windows rendszerű virtuális asztali gépen:

- A szélesség – első terheléselosztás lehetővé teszi a felhasználói munkamenetek egyenletes elosztását a gazdagépen lévő munkamenet-gazdagépek között.
- Mélység – az első terheléselosztás lehetővé teszi, hogy a munkamenet-gazdagépet a gazdagépen lévő felhasználói munkamenetekkel telítettül. Miután az első munkamenet eléri a munkamenet-korlátot, a terheléselosztó az új felhasználói kapcsolatokat a gazdagépen a következő munkamenet-állomásra irányítja, amíg meg nem éri a korlátot, és így tovább.

Az egyes gazdagépek készletei csak egy ilyen terheléselosztási típust állíthatnak be. A terheléselosztási módszerek azonban a következő viselkedéseket is megoszthatják, függetlenül attól, hogy melyik gazdagépen vannak:

- Ha a felhasználó már rendelkezik egy munkamenettel a gazdagépen, és újra csatlakozik ehhez a munkamenethez, akkor a terheléselosztó sikeresen átirányítja azokat a munkamenet-gazdagépre a meglévő munkamenettel. Ez a viselkedés akkor is érvényes, ha a munkamenet-állomás AllowNewConnections tulajdonsága hamis értékre van állítva.
- Ha a felhasználó még nem rendelkezik munkamenettel a gazdagépen, akkor a terheléselosztó nem veszi figyelembe, hogy a AllowNewConnections tulajdonság értéke false a terheléselosztás során.

## <a name="breadth-first-load-balancing-method"></a>Szélesség – első terheléselosztási módszer

A szélesség – első terheléselosztási módszer lehetővé teszi a felhasználói kapcsolatok terjesztését a forgatókönyv optimalizálásához. Ez a módszer ideális azoknak a szervezeteknek, akik a legjobb élményt szeretnék biztosítani a készletezett virtuális asztali környezethez csatlakozó felhasználók számára.

A szélesség – első módszer először a munkamenet azon gazdagépeit kérdezi le, amelyek engedélyezik az új kapcsolatokat. A metódus ezután véletlenszerűen kiválasztja a munkamenet-gazdagépet a legkevesebb munkamenetet tartalmazó munkamenet-gazdagépek közül. Ha például kilenc, 11, 12, 13, 14, 15, 16, 17, 18 és 19 munkamenetből álló gép van, akkor a létrehozott új munkamenet nem fog automatikusan belépni az első gépre. Ehelyett a legalacsonyabb számú munkamenettel rendelkező első öt gép közül választhat (11, 12, 13, 14, 15).

## <a name="depth-first-load-balancing-method"></a>Mélység – első terheléselosztási módszer

A mélység – első terheléselosztási módszer lehetővé teszi, hogy egyszerre egy munkamenet-gazdagépet telítetten, hogy optimalizálja ezt a forgatókönyvet. Ez a módszer ideális olyan költséghatékony szervezeteknek, amelyek részletesebben szabályozzák a gazdagépek számára lefoglalt virtuális gépek számát.

A mélység – első módszer először a munkamenet azon gazdagépeit kérdezi le, amelyek engedélyezik az új kapcsolatokat, és nem mentek át a maximális munkamenet-korláton A metódus ezután kiválasztja a munkamenet-gazdagépet a legnagyobb számú munkamenettel. Ha döntetlen van, a metódus kiválasztja az első munkamenet-állomást a lekérdezésben.

>[!IMPORTANT]
>A mélységi – az első terheléselosztási algoritmus a munkamenetek maximális száma alapján osztja el a munkamenet-gazdagépeket. Ezt a paramétert akkor kell megadni, ha a mélység – első terheléselosztási algoritmust használja. A lehető legjobb felhasználói élmény érdekében ügyeljen arra, hogy a maximális munkamenet-gazdagép-korlát paramétert egy olyan számra módosítsa, amely a legjobban megfelel a környezetének.