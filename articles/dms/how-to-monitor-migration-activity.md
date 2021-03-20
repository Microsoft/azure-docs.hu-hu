---
title: Áttelepítési tevékenység figyelése – Azure Database Migration Service
description: Ismerje meg, hogyan figyelheti az áttelepítési tevékenységet a Azure Database Migration Service használatával.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: dc9123735ef65bec89e99a14d25cba881be306fe
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91297470"
---
# <a name="monitor-migration-activity-using-the-azure-database-migration-service"></a>Áttelepítési tevékenység figyelése a Azure Database Migration Service használatával
Ebből a cikkből megtudhatja, hogyan figyelheti az áttelepítés előrehaladását az adatbázis szintjén és a tábla szintjén is.

## <a name="monitor-at-the-database-level"></a>Figyelés az adatbázis szintjén
Az adatbázis szintjén a tevékenységek figyeléséhez tekintse meg az adatbázis szintű panelt:

![Adatbázis szintű panel](media/how-to-monitor-migration-activity/dms-database-level-blade.png)

> [!NOTE]
> Az adatbázis hiperhivatkozásának kiválasztásakor megjelenik a táblák listája és az áttelepítés folyamata.

A következő táblázat felsorolja az adatbázis szintű panel mezőit, és ismerteti az egyesekhez társított különböző állapot-értékeket.

<table id='overview' class='overview'>
  <thead>
    <tr>
      <th class="x-hidden-focus"><strong>Mezőnév</strong></th>
      <th><strong>Mező alállapota</strong></th>
      <th><strong>Leírás</strong></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="3" class="ActivityStatus"><strong>Tevékenység állapota</strong></td>
      <td>Futó</td>
      <td>Az áttelepítési tevékenység fut.</td>
    </tr>
    <tr>
      <td>Sikeres</td>
      <td>Az áttelepítési tevékenység problémák nélkül sikerült.</td>
    </tr>
    <tr>
      <td>Hibás</td>
      <td>Az áttelepítés nem sikerült. A teljes hibaüzenetnél válassza a "hiba részletei" hivatkozást a Migrálás részletei részben.</td>
    </tr>
    <tr>
      <td rowspan="4" class="Status"><strong>Állapot</strong></td>
      <td>Inicializálás</td>
      <td>A DMS az áttelepítési folyamat beállítására szolgál.</td>
    </tr>
    <tr>
      <td>Futó</td>
      <td>A DMS-folyamat fut és az áttelepítés végrehajtása folyamatban van.</td>
    </tr>
    <tr>
      <td>Kész</td>
      <td>Az áttelepítés befejeződött.</td>
    </tr>
    <tr>
      <td>Sikertelen</td>
      <td>Az áttelepítés nem sikerült. Az áttelepítési hibák megtekintéséhez kattintson az áttelepítés részletei elemre.</td>
    </tr>
    <tr>
      <td rowspan="5" class="migration-details"><strong>Áttelepítési adatok</strong></td>
      <td>Az áttelepítési folyamat indítása</td>
      <td>A DMS az áttelepítési folyamat beállítására szolgál.</td>
    </tr>
    <tr>
      <td>Teljes adatterhelés folyamatban</td>
      <td>A DMS a kezdeti terhelést végrehajtja.</td>
    </tr>
    <tr>
      <td>Készen áll a átváltás</td>
      <td>A kezdeti betöltés befejezése után a DMS a átváltás számára készként jelöli meg az adatbázist. A felhasználónak ellenőriznie kell, hogy a folyamatos szinkronizálás során megtörtént-e az adatgyűjtés.</td>
    </tr>
    <tr>
      <td>Az összes alkalmazott módosítás</td>
      <td>A kezdeti betöltés és a folyamatos szinkronizálás befejeződött. Ez az állapot akkor is előfordulhat, ha az adatbázis sikeresen átváltás.</td>
    </tr>
    <tr>
      <td>A hiba részleteinek megtekintése</td>
      <td>Kattintson a hivatkozásra a hiba részleteinek megjelenítéséhez.</td>
    </tr>
    <tr>
      <td rowspan="1" class="duration"><strong>Időtartam</strong></td>
      <td>N/A</td>
      <td>Az áttelepítési tevékenységnek az áttelepítésre való inicializálásának teljes ideje, vagy hiba történt az áttelepítés során.</td>
    </tr>
     </tbody>
</table>

## <a name="monitor-at-table-level--quick-summary"></a>Figyelés a tábla szintjén – gyors összefoglalás
A tábla szintjén a tevékenységek figyeléséhez tekintse meg a tábla szintjének panelt. A panel felső részén láthatók a teljes terheléssel és növekményes frissítésekkel áttelepített sorok részletes száma. 

A panel alsó részén láthatók a táblák, és az áttelepítés előrehaladásának gyors összegzését mutatják.

![Táblázat szintű panel – gyors összefoglalás](media/how-to-monitor-migration-activity/dms-table-level-blade-summary.png)

Az alábbi táblázat a tábla szintű részletekben látható mezőket ismerteti.

| Mező neve        | Description       |
| ------------- | ------------- |
| **A teljes terhelés befejeződött**      | A táblák száma teljes adatterheléssel fejeződött be. |
| **Teljes terhelés várólistán**      | A teljes betöltésre váró táblák száma.      |
| **Teljes terhelés betöltése** | A táblák száma nem sikerült.      |
| **Növekményes frissítések**      | Az adatváltozások rögzítése (CDC) frissítéseinek száma a célhelyre alkalmazott sorokban. |
| **Növekményes beszúrások**      | A célhelyre alkalmazott CDC-beszúrások száma.      |
| **Növekményes törlések** | A megadott számú CDC-törlés a célhelyre alkalmazott sorokban.      |
| **Függőben lévő módosítások**      | Azon a sorban lévő CDC száma, amelyek továbbra is várnak a célhelyre való alkalmazásra. |
| **Alkalmazott módosítások**      | A CDC összes frissítésének, beszúrásának és törlésének összege a célhelyre alkalmazott sorokban.      |
| **Hibás állapotú táblák** | A "hiba" állapotú táblák száma az áttelepítés során. Néhány példa arra, hogy a táblák a hibás állapotba kerülnek, ha a cél vagy az adathalmazban azonosított duplikált elemek nem kompatibilisek a céltábla betöltésével.      |

## <a name="monitor-at-table-level--detailed-summary"></a>Figyelés a tábla szintjén – részletes összefoglalás
Két lap jelenik meg, amelyek az áttelepítés előrehaladását mutatják be a teljes terhelés és a növekményes adatszinkronizálás során.
    
![Teljes terhelés lap](media/how-to-monitor-migration-activity/dms-full-load-tab.png)

![Növekményes adatszinkronizálás lap](media/how-to-monitor-migration-activity/dms-incremental-data-sync-tab.png)

Az alábbi táblázat a táblázat szintű áttelepítési folyamat során megjelenő mezőket ismerteti.

| Mező neve        | Description       |
| ------------- | ------------- |
| **Állapot – szinkronizálás**      | Folyamatos szinkronizálás fut. |
| **Insert**      | A célhelyre alkalmazott CDC-beszúrások száma.      |
| **Frissítés** | A cél értékre alkalmazott CDC-frissítések száma.      |
| **Törlés**      | A megadott számú CDC-törlés a célhelyre alkalmazott sorokban. |
| **Összes alkalmazott**      | A CDC összes frissítésének, beszúrásának és törlésének összege a célhelyre alkalmazott sorokban. |
| **Adathibák** | A táblázatban szereplő adathibák száma. Néhány példa a hibákra *511: nem hozható létre a (z)% d méretű sor, amely nagyobb, mint a maximálisan megengedett% d, 8114: hiba történt a (z)% ls% ls adattípusának konvertálása során.*  Az ügyfélnek az Azure Target dms_apply_exceptions táblájából kell lekérdezni a hiba részleteinek megtekintéséhez.    |

> [!NOTE]
> Az INSERT, az Update és a DELETE és az összes alkalmazott CDC értéke csökkenhet az adatbázis átváltás vagy az áttelepítés újraindításakor.

## <a name="next-steps"></a>Következő lépések
- Tekintse át az áttelepítési útmutatót a Microsoft [Database áttelepítési útmutatójában](https://datamigration.microsoft.com/).