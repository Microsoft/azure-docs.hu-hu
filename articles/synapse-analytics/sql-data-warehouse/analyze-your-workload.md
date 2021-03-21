---
title: Számítási feladatok elemzése dedikált SQL-készlethez
description: Technikák a lekérdezés rangsorolásának elemzéséhez az Azure szinapszis Analytics dedikált SQL-készlete számára.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 14c3ad30bac7cec4c11822d825323bb9db2ba440
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96454528"
---
# <a name="analyze-your-workload-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>A dedikált SQL-készlet számítási feladatainak elemzése az Azure szinapszis Analyticsben

Technikák a dedikált SQL Pool-számítási feladatok elemzéséhez az Azure szinapszis Analyticsben. 

## <a name="resource-classes"></a>Erőforrásosztályok

A dedikált SQL-készlet erőforrás-osztályokat biztosít a rendszererőforrásoknak a lekérdezésekhez való hozzárendeléséhez.  Az erőforrás-osztályokkal kapcsolatos további információkért lásd: [erőforrás-osztályok & munkaterhelés-kezelés](resource-classes-for-workload-management.md).  A lekérdezések megvárhatják, ha a lekérdezéshez hozzárendelt erőforrás-osztálynak több erőforrásra van szüksége, mint amennyi jelenleg elérhető.

## <a name="queued-query-detection-and-other-dmvs"></a>Várólistán lévő lekérdezések észlelése és egyéb DMV

A `sys.dm_pdw_exec_requests` DMV használatával azonosíthatja a párhuzamossági sorban Várakozó lekérdezéseket. Az egyidejű tárolóhelyre várakozó lekérdezések **felfüggesztve** állapottal rendelkeznek.

```sql
SELECT  r.[request_id]                           AS Request_ID
,       r.[status]                               AS Request_Status
,       r.[submit_time]                          AS Request_SubmitTime
,       r.[start_time]                           AS Request_StartTime
,       DATEDIFF(ms,[submit_time],[start_time])  AS Request_InitiateDuration_ms
,       r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r
;
```

A számítási feladatok felügyeleti szerepkörei a használatával tekinthetők meg `sys.database_principals` .

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

A következő lekérdezés azt jeleníti meg, hogy az egyes felhasználók melyik szerepkörhöz vannak rendelve.

```sql
SELECT  r.name AS role_principal_name
,       m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id      = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc')
;
```

A dedikált SQL-készlet a következő várakozási típusokkal rendelkezik:

* **LocalQueriesConcurrencyResourceType**: az egyidejű tárolóhely-keretrendszeren kívüli lekérdezések. A DMV-lekérdezések és a rendszerfüggvények, például a `SELECT @@VERSION` helyi lekérdezések példái.
* **UserConcurrencyResourceType**: az egyidejű tárolóhely-keretrendszerben található lekérdezések. A végfelhasználói táblákra irányuló lekérdezések olyan példákat mutatnak be, amelyek ezt az erőforrástípust használják.
* **DmsConcurrencyResourceType**: megvárja az adatáthelyezési műveletek eredményét.
* **BackupConcurrencyResourceType**: Ez a várakozás azt jelzi, hogy egy adatbázis biztonsági mentése folyamatban van. Az erőforrástípus maximális értéke 1. Ha egy időben több biztonsági mentést is kértek, a többi üzenetsor. Általánosságban elmondható, hogy az egymást követő Pillanatképek közül legalább 10 percet ajánlunk.

A `sys.dm_pdw_waits` DMV használatával megtekintheti, hogy mely erőforrásokra vár kérelmek.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]                                           AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,       SESSION_ID()                                       AS Current_session
,       s.[status]                                         AS Session_status
,       s.[login_name]
,       s.[query_count]
,       s.[client_id]
,       s.[sql_spid]
,       r.[command]                                        AS Request_command
,       r.[label]
,       r.[status]                                         AS Request_status
,       r.[submit_time]
,       r.[start_time]
,       r.[end_compile_time]
,       r.[end_time]
,       DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,       DATEDIFF(ms,r.[start_time],r.[end_compile_time])   AS Request_compile_time_ms
,       DATEDIFF(ms,r.[end_compile_time],r.[end_time])     AS Request_execution_time_ms
,       r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID()
;
```

A `sys.dm_pdw_resource_waits` DMV egy adott lekérdezés várakozási adatait jeleníti meg. Az erőforrás-várakozási idő az erőforrások számára várakozási időt méri. A várakozási idő az az idő, ameddig a mögöttes SQL-kiszolgálók a lekérdezéseket a CPU-ra ütemezhetik.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID()
;
```

Azt is megteheti, `sys.dm_pdw_resource_waits` hogy a DMV kiszámítja, hogy hány párhuzamossági tárolóhelyet adtak meg.

```sql
SELECT  SUM([concurrency_slots_used]) as total_granted_slots
FROM    sys.[dm_pdw_resource_waits]
WHERE   [state]           = 'Granted'
AND     [resource_class] is not null
AND     [session_id]     <> session_id()
;
```

A `sys.dm_pdw_wait_stats` DMV a várakozások történelmi trendek elemzésére használható.

```sql
SELECT   w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w
;
```

## <a name="next-steps"></a>Következő lépések

További információ az adatbázis-felhasználók és a biztonság kezeléséről: [DEDIKÁLT SQL-készlet biztonságossá tétele (korábban SQL DW)](sql-data-warehouse-overview-manage-security.md). További információ arról, hogy a nagyobb erőforrás-osztályok Hogyan javíthatják a fürtözött oszlopcentrikus index minőségét: [az indexek újraépítése a szegmensek minőségének javítása érdekében](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).
