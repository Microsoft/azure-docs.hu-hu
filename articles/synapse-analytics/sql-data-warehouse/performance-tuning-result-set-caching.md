---
title: Teljesítmény-finomhangolás eredményhalmaz gyorsítótárazásával
description: Az eredményhalmaz gyorsítótárazási funkcióinak áttekintése a dedikált SQL-készlethez az Azure szinapszis Analyticsben
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.custom: azure-synapse
ms.openlocfilehash: d8c6c8d22c059c63fb4f84c84a02a70de30d4ebe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98678525"
---
# <a name="performance-tuning-with-result-set-caching"></a>Teljesítmény-finomhangolás eredményhalmaz gyorsítótárazásával

Ha az eredményhalmaz gyorsítótárazása engedélyezve van, a dedikált SQL-készlet automatikusan gyorsítótárazza a lekérdezés eredményeit a felhasználói adatbázisban ismétlődő használatra.  Ez lehetővé teszi, hogy a későbbi lekérdezés-végrehajtások közvetlenül a megőrzött gyorsítótárból kapjanak eredményeket, így az újraszámításra nincs szükség.   Az eredményhalmaz gyorsítótárazása javítja a lekérdezési teljesítményt, és csökkenti a számítási erőforrások használatát.  Emellett a gyorsítótárazott eredményeket használó lekérdezések nem használnak párhuzamossági tárolóhelyeket, így nem számítanak bele a meglévő egyidejűségi korlátokba. A biztonság érdekében a felhasználók csak akkor érhetik el a gyorsítótárazott eredményeket, ha ugyanazokat az adatelérési engedélyeket használják, mint a gyorsítótárazott eredményeket létrehozó felhasználók.  

## <a name="key-commands"></a>Legfontosabb parancsok

[Eredményhalmaz gyorsítótárazásának be-vagy kikapcsolása felhasználói adatbázishoz](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

[Eredményhalmaz gyorsítótárazásának be-vagy kikapcsolása munkamenethez](/sql/t-sql/statements/set-result-set-caching-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

[A gyorsítótárazott eredményhalmaz méretének ellenőrzéséhez](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)  

[A gyorsítótár kiürítése](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="whats-not-cached"></a>Nem gyorsítótárazott  

Ha az eredményhalmaz gyorsítótárazása be van kapcsolva egy adatbázishoz, a rendszer az összes lekérdezés esetében gyorsítótárazza az eredményeket, amíg a gyorsítótár megtelt, kivéve a következő lekérdezéseket:

- Olyan beépített függvényekkel vagy futásidejű kifejezésekkel rendelkező lekérdezések, amelyek nem determinisztikus, még akkor is, ha az alaptáblák adatai vagy lekérdezése nem változik. Például: DateTime. Now (), GetDate ().
- Felhasználó által definiált függvényeket használó lekérdezések
- A sor szintű biztonsági vagy az oszlop szintű biztonsággal rendelkező táblákat használó lekérdezések
- Az 64 kb nagyobb sorszámú adatvisszaadó lekérdezések
- Nagyméretű adatmennyiséget (>10 GB-ot) visszaadó lekérdezések 
>[!NOTE]
> - Egyes nem determinisztikus függvények és futtatókörnyezeti kifejezések is determinisztikus az azonos adatokkal való ismétlődő lekérdezésekhez. Például ROW_NUMBER ().  
> - Használja a SORRENDet a lekérdezésben, ha a lekérdezési eredményhalmaz sorainak sorrendje és sorrendje fontos az alkalmazás logikája számára.
> - Ha az ORDER BY oszlopokban lévő adatok nem egyediek, az ORDER BY Columns utasításban nem szerepelnek garanteed sorok, függetlenül attól, hogy az eredményhalmaz gyorsítótárazása engedélyezve van vagy le van tiltva.

> [!IMPORTANT]
> Az eredményhalmaz gyorsítótárának létrehozásához és az adatok a gyorsítótárból való lekéréséhez szükséges műveletek egy dedikált SQL Pool-példány vezérlési csomópontján történnek.
> Ha a eredményhalmaz gyorsítótárazása be van kapcsolva, a nagyméretű eredményhalmaz (például >1GB) visszaadó lekérdezések futtatása nagy sávszélességet eredményezhet a vezérlő csomóponton, és lelassítja a példányon a lekérdezésre adott teljes választ.  Ezeket a lekérdezéseket általában az adatfelderítési vagy ETL-műveletek során használják. A vezérlő csomópontjának kihangsúlyozása és a teljesítménnyel kapcsolatos probléma elkerülése érdekében a felhasználóknak ki kell kapcsolniuk az eredményhalmaz gyorsítótárazását az adatbázison az ilyen típusú lekérdezések futtatása előtt.  

Futtassa ezt a lekérdezést az eredményhalmaz gyorsítótárazási műveletei által a lekérdezéshez megadott időtartamra:

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command
FROM sys.dm_pdw_request_steps
WHERE request_id  = <'request_id'>;
```

Az alábbi példa egy, az eredményhalmaz gyorsítótárazásával letiltott lekérdezés kimenetét jeleníti meg.

![A képernyőképen a lekérdezés eredményei láthatók, beleértve a hely típusát és a parancsot.](./media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

Az alábbi példa egy, az eredményhalmaz gyorsítótárazásával végrehajtott lekérdezés kimenetét jeleníti meg.

![A képernyőkép a lekérdezés eredményét jeleníti meg a kiválasztott paranccsal * a következőből: [D W ResultCache D b] dot d b o kinevezve.](./media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>A gyorsítótárazott eredmények használatakor

A gyorsítótárazott eredményhalmaz egy lekérdezés esetében újra használatos, ha az alábbi követelmények mindegyike teljesül:

- A lekérdezést futtató felhasználónak hozzáférése van a lekérdezésben hivatkozott összes táblához.
- Az új lekérdezés és az eredményül kapott gyorsítótárat létrehozó korábbi lekérdezés között pontos egyezés van.
- Nincs olyan adatvagy séma-változás abban a táblázatban, amelyben a gyorsítótárazott eredményhalmaz létrejött.

Futtassa ezt a parancsot annak ellenőrzéséhez, hogy egy lekérdezés végrehajtása sikeres volt-e a találati gyorsítótárban. A result_cache_hit oszlop 1 értéket ad vissza a gyorsítótár találata esetén, 0 a gyorsítótár-Kihagyás esetén, valamint a negatív értékeket, amelyek miatt a rendszer nem használta az eredményhalmaz gyorsítótárazását. Részletekért olvassa el a [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .

```sql
SELECT request_id, command, result_cache_hit FROM sys.dm_pdw_exec_requests
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>Gyorsítótárazott eredmények kezelése

Az eredményhalmaz gyorsítótárának maximális mérete 1 TB/adatbázis.  A gyorsítótárazott eredményeket a rendszer automatikusan érvényteleníti, amikor az alapul szolgáló lekérdezési adat megváltoznak.  

A gyorsítótár kiürítését a dedikált SQL-készlet kezeli automatikusan az alábbi ütemterv szerint:

- 48 óránként, ha az eredményhalmaz nem lett felhasználva vagy érvénytelenítve lett.
- Ha az eredményhalmaz gyorsítótára a maximális méretet közelíti meg.

A felhasználók a teljes eredményhalmaz gyorsítótárát manuálisan is kiürítheti a következő lehetőségek egyikének használatával:

- Az eredményhalmaz gyorsítótár-funkciójának kikapcsolása az adatbázishoz
- DBCC-DROPRESULTSETCACHE futtatása az adatbázishoz való csatlakozás közben

Az adatbázis szüneteltetése nem üres a gyorsítótárazott eredményhalmaz számára.  

## <a name="next-steps"></a>Következő lépések

További fejlesztési tippek: a [fejlesztés áttekintése](sql-data-warehouse-overview-develop.md).
