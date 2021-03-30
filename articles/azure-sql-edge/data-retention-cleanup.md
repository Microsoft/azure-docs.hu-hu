---
title: Korábbi adatok kezelése adatmegőrzési házirenddel – Azure SQL Edge
description: Ismerje meg, hogyan kezelheti a korábbi adatmegőrzési szabályzatot az Azure SQL Edge szolgáltatásban
keywords: SQL Edge, adatmegőrzés
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: f547f0b3219889b54364c4805c2dd5b183a9861a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93392367"
---
# <a name="manage-historical-data-with-retention-policy"></a>Korábbi adatok kezelése adatmegőrzési házirenddel

Az adatmegőrzés az adatbázison és a mögöttes táblákon is engedélyezhető, így a felhasználók rugalmas korosítási házirendeket hozhatnak létre a tábláikban és adatbázisaikban. Az adatmegőrzés alkalmazása egyszerű: ehhez csak egy paramétert kell beállítani a tábla létrehozásakor vagy az ALTER TABLE művelet részeként. 

Miután az adatmegőrzési szabályzat egy adatbázis és a mögöttes tábla esetében a deördög, a háttérben futó idő időzítő feladat lefuttatja az összes elavult rekordot, amely az adatok megőrzéséhez engedélyezve van a táblából. Az egyező sorok azonosítása és a táblázatból való eltávolítása transzparens módon történik a rendszer által ütemezett és futtatott háttér-feladatban. A tábla sorainak életkori feltétele a tábla definíciójában használt oszlop alapján van bejelölve `filter_column` . Ha például a megőrzési idő egy hétig van beállítva, a törlésre jogosult táblák a következő feltétel valamelyikét elégítik ki: 

- Ha a szűrő oszlop DATETIMEOFFSET adattípust használ, akkor a feltétel `filter_column < DATEADD(WEEK, -1, SYSUTCDATETIME())`
- Máskülönben a feltétel `filter_column < DATEADD(WEEK, -1, SYSDATETIME())`

## <a name="data-retention-cleanup-phases"></a>Adatmegőrzési karbantartási fázisok

Az adatmegőrzési karbantartási művelet két fázisból áll. 
- Felderítési fázis – ebben a fázisban a karbantartási művelet azonosítja a felhasználói adatbázisokon belüli összes táblát a törlési lista létrehozásához. A felderítés naponta egyszer fut.
- Karbantartási fázis – ebben a fázisban a tisztítás minden olyan táblán fut, amely a felderítési fázisban azonosított, véges adatmegőrzéssel van ellátva. Ha a karbantartási művelet nem hajtható végre egy táblán, akkor a rendszer kihagyja a táblát az aktuális futtatásban, és a következő iteráció során újra próbálkozik. A tisztítás során a következő alapelvek használatosak
    - Ha egy elavult sort egy másik tranzakció zárol, a rendszer kihagyja ezt a sort. 
    - A karbantartás az alapértelmezett 5 másodperces zárolási időtúllépési beállítással fut. Ha a zárolások nem szerezhetők be az időkorlát-időszakon belüli táblákon, a rendszer kihagyja a táblát az aktuális futtatásban, és a következő iteráció során újra próbálkozik.
    - Ha hiba történt egy tábla tisztítása során, a rendszer kihagyja a táblát, és a következő iterációban fogja felvenni.

## <a name="manual-cleanup"></a>Manuális karbantartás

A tábla adatmegőrzési beállításaitól és az adatbázis számítási feladatainak természetétől függően előfordulhat, hogy az automatikus karbantartási szál nem távolítja el teljesen az összes elavult sort a futása során. Ha segítségre van ez, és lehetővé teszi a felhasználók számára az elavult sorok manuális eltávolítását, a `sys.sp_cleanup_data_retention` tárolt eljárás az Azure SQL Edge-ben lett bevezetve. 

Ez a tárolt eljárás három paramétert vesz igénybe. 
    - Séma neve – a tábla tulajdonosi sémájának neve. Ez egy kötelező paraméter. 
    - Táblanév – annak a táblának a neve, amelynek a manuális karbantartását futtatja. Ez egy kötelező paraméter. 
    - sorszám (kimenet) – kimeneti változó. A manuális karbantartási SP által megtisztított sorok számát adja vissza. Ez egy opcionális paraméter. 

Az alábbi példa a manuális karbantartási SP for Table végrehajtását mutatja be `dbo.data_retention_table` .

```sql
declare @rowcnt bigint 
EXEC sys.sp_cleanup_data_retention 'dbo', 'data_retention_table', @rowcnt output 
select @rowcnt 
```

## <a name="how-obsolete-rows-are-deleted"></a>Az elavult sorok törlése

A karbantartási folyamat a tábla indexének elrendezésével függ. A rendszer háttérbeli feladatot hoz létre az elavult Adattisztítás végrehajtásához az összes, véges megőrzési időtartammal rendelkező táblában. A sortárindex létrehozását (B-Tree vagy heap) indexének logikája törli az elavult sort kisebb adattömbökben (akár 10K-ig), ami minimalizálja az adatbázis-naplót és az IO-alrendszert. Bár a karbantartási logika a szükséges B-fa indexet használja, a megőrzési időtartamnál régebbi sorok törlésének sorrendjét nem lehet határozottan garantálni. Ezért ne használja az alkalmazásokban a karbantartási sorrendet.

A fürtözött oszlopcentrikus tisztítási feladata egyszerre eltávolítja a teljes sorcsoport-csoportokat (általában az egyes sorok 1 000 000), ami rendkívül hatékony, különösen az adatok generálása és a nagy ütemben való kiesése esetén.

![Adatmegőrzési karbantartás](./media/data-retention-cleanup/data-retention-cleanup.png)

A kiváló adattömörítés és a hatékony megőrzés érdekében a fürtözött oszlopcentrikus index tökéletes választás olyan forgatókönyvek esetén, amikor a munkaterhelés gyorsan nagy mennyiségű adat generálható.

> [!Note]
> B-fa indexek és halmok esetén az adatmegőrzés az alapul szolgáló táblákon futtat egy törlő lekérdezést, amely ütközik a táblák törlési eseményindítókkal. Azt javasoljuk, hogy távolítsa el a törlési eseményindítókat a táblákból, vagy ne engedélyezze az adatmegőrzést a DML-triggert tartalmazó táblákon.

## <a name="monitoring-data-retention-cleanup"></a>Adatmegőrzési karbantartás figyelése

Az adatmegőrzési szabályzatok karbantartási műveletei a kiterjesztett események (Xevent típusú eseményekhez-EK) használatával figyelhetők meg az Azure SQL Edge-ben. További információ a kiterjesztett eseményekről: [Xevent típusú eseményekhez – áttekintés](/sql/relational-databases/extended-events/extended-events). 

A következő hat kiterjesztett esemény segít nyomon követni a karbantartási műveletek állapotát. 

| Név | Leírás |
|------| ------------|
| data_retention_task_started  | Akkor következik be, amikor az adatmegőrzési szabályzattal rendelkező táblák tisztítására szolgáló háttérbeli feladat elindul. |
| data_retention_task_completed  | Akkor következik be, amikor az adatmegőrzési szabályzattal rendelkező táblák tisztítására szolgáló háttérbeli feladat lejár. |
| data_retention_task_exception  | Akkor következik be, amikor az adatmegőrzési szabályzattal rendelkező táblák tisztítására szolgáló háttérben végzett feladat a táblára vonatkozó megőrzési karbantartási folyamaton kívül esik. |
| data_retention_cleanup_started  | Akkor következik be, amikor megkezdődik az adatmegőrzési szabályzattal rendelkező tábla törlése. |
| data_retention_cleanup_exception  | Az adatmegőrzési szabályzattal rendelkező tábla karbantartási folyamata sikertelen lesz. |
| data_retention_cleanup_completed  | Akkor következik be, amikor az adatmegőrzési szabályzattal rendelkező tábla tisztítási folyamata lejár. |  

Emellett egy nevű új gyűrűs puffer-típus lett `RING_BUFFER_DATA_RETENTION_CLEANUP` hozzáadva sys.dm_os_ring_buffers dinamikus felügyeleti nézethez. Ez a nézet használható az adatmegőrzési karbantartási műveletek figyelésére. 


## <a name="next-steps"></a>Következő lépések
- [Adatmegőrzési szabályzat](data-retention-overview.md)
- [Adatmegőrzési szabályzatok engedélyezése és letiltása](data-retention-enable-disable.md)