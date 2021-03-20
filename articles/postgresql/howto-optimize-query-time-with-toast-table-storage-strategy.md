---
title: A lekérdezési idő optimalizálása a pirítós táblázat tárolási stratégiájának használatával Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk azt ismerteti, hogyan optimalizálható a lekérdezési idő a pirítós táblázat tárolási stratégiájával egy Azure Database for PostgreSQL – egyetlen kiszolgálón.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 3f1fa0affb821b00d4f5529841533e854e634377
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86116182"
---
# <a name="optimize-query-time-with-the-toast-table-storage-strategy"></a>A lekérdezési idő optimalizálása a pirítós táblázat tárolási stratégiájával 
Ez a cikk azt ismerteti, hogyan optimalizálható a lekérdezési idő a nagyméretű attribútumú tárolási technikák (TOAST) tábla tárolási stratégiájával.

## <a name="toast-table-storage-strategies"></a>A pirítós táblázat tárolási stratégiái
Négy különböző stratégia van használatban a PIRÍTÓSt használó lemezeken található oszlopok tárolására. A tömörítés és a beépített tárolók különböző kombinációit jelentik. A stratégia az adattípus és az oszlop szintjén állítható be.
- Az **egyszerűség** megakadályozza a tömörítést vagy a beépített tárolást. Letiltja az egybájtos fejlécek használatát a varlena-típusokhoz. A sima az egyetlen lehetséges stratégia a PIRÍTÓSt nem használó adattípusok oszlopaihoz.
- A **kibővített** funkció lehetővé teszi mind a tömörítést, mind a soron kívüli tárolást. A kibővített érték az alapértelmezett a legtöbb adattípushoz, amely a PIRÍTÓSt használhatja. Először a tömörítést próbálja meg. Ha a sor még túl nagy, akkor a rendszer nem tudja elvégezni a tárolást.
- A **külső** lehetővé teszi a beépített tárolást, de nem tömöríti a tömörítést. A külső lehetővé teszi az alkarakterlánc-műveletek használatát a széles szöveges és bytea oszlopokon. Ez a sebesség a megnövekedett tárolóhelyek büntetésével jár. Ezek a műveletek úgy vannak optimalizálva, hogy csak az elfogyott érték kötelező részeit lehessen beolvasni, ha nem tömörítik.
- A **Main** lehetővé teszi a tömörítést, de nem beépített tárterületet. Az ilyen oszlopokhoz továbbra is végre van hajtva a beépített tárterület, de csak utolsó megoldásként. Akkor következik be, amikor nincs más mód arra, hogy a sor elég kicsi legyen ahhoz, hogy illeszkedjen az oldalhoz.

## <a name="use-toast-table-storage-strategies"></a>A TOAST Table Storage-stratégiák használata
Ha a lekérdezések a PIRÍTÓSt használó adattípusokhoz férnek hozzá, érdemes lehet a fő stratégiát használni a lekérdezési idők csökkentése érdekében az alapértelmezett kiterjesztett beállítás helyett. A fő nem zárja ki a beépített tárolást. Ha a lekérdezések nem férnek hozzá a PIRÍTÓSt használó adattípusokhoz, hasznos lehet a kiterjesztett lehetőség megőrzése. A fő tábla sorainak nagyobb része illeszkedik a megosztott puffer gyorsítótárába, ami segít a teljesítményben.

Ha olyan számítási feladattal rendelkezik, amely széles táblázatokkal és nagy méretű karakterekkel rendelkező sémát használ, érdemes lehet PostgreSQL TOAST-táblákat használni. Egy példaként szolgáló Customer tábla több mint 350 oszlopot tartalmaz, amelyek több, 255 karakterből álló oszloppal rendelkeznek. Miután áttért a TOAST Table fő stratégiájára, a teljesítményteszt lekérdezési ideje 4203 másodperctől 467 másodpercre csökkent. Ez 89 százalékos javulást jelent.

## <a name="next-steps"></a>Következő lépések
Tekintse át az előző jellemzőkre vonatkozó számítási feladatokat. 

Tekintse át a következő PostgreSQL-dokumentációt: 
- [68. fejezet, adatbázis fizikai tárolása](https://www.postgresql.org/docs/current/storage-toast.html) 