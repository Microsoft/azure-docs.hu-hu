---
title: Hasznos diagnosztikai lekérdezések – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Lekérdezések az elosztott adatforgalom megismeréséhez
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 1/5/2021
ms.openlocfilehash: 4858f650aca1b704ac79482e0158fd83fc0264b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98165241"
---
# <a name="useful-diagnostic-queries"></a>Hasznos diagnosztikai lekérdezések

## <a name="finding-which-node-contains-data-for-a-specific-tenant"></a>Egy adott bérlő adatait tartalmazó csomópont megkeresése

A több-bérlős használati esetekben meghatározhatjuk, hogy melyik feldolgozó csomópont tartalmazza az adott bérlő sorait.  A nagy kapacitású (Citus) csoportosítja az elosztott táblák sorait a szegmensekre, és az egyes szegmenseket elhelyezi a kiszolgálói csoport munkavégző csomópontján. 

Tegyük fel, hogy az alkalmazás bérlői a tárolók, és szeretnénk megkeresni, melyik feldolgozó csomópont tárolja a Store ID = 4 tárolót.  Más szóval a 4. értékkel rendelkező sorokat tartalmazó szegmens elhelyezését szeretnénk megkeresni:

``` postgresql
SELECT shardid, shardstate, shardlength, nodename, nodeport, placementid
  FROM pg_dist_placement AS placement,
       pg_dist_node AS node
 WHERE placement.groupid = node.groupid
   AND node.noderole = 'primary'
   AND shardid = (
     SELECT get_shard_id_for_distribution_column('stores', 4)
   );
```

A kimenet tartalmazza a feldolgozó adatbázis gazdagépét és portját.

```
┌─────────┬────────────┬─────────────┬───────────┬──────────┬─────────────┐
│ shardid │ shardstate │ shardlength │ nodename  │ nodeport │ placementid │
├─────────┼────────────┼─────────────┼───────────┼──────────┼─────────────┤
│  102009 │          1 │           0 │ 10.0.0.16 │     5432 │           2 │
└─────────┴────────────┴─────────────┴───────────┴──────────┴─────────────┘
```

## <a name="finding-the-distribution-column-for-a-table"></a>Tábla terjesztési oszlopának megkeresése

A nagy kapacitású (Citus) minden elosztott táblájához tartozik egy "terjesztési oszlop". (További információ: [elosztott adatok modellezése](concepts-hyperscale-choose-distribution-column.md).) Fontos tudni, hogy melyik oszlopról van szó. A táblákhoz való csatlakozáskor vagy szűréskor például a "szűrő hozzáadása a terjesztési oszlophoz" hibaüzenetek jelenhetnek meg, például:.

A `pg_dist_*` koordinátor csomópont táblái az elosztott adatbázissal kapcsolatos különböző metaadatokat tartalmaznak. A különösen `pg_dist_partition` az egyes táblák terjesztési oszlopával kapcsolatos információkat tartalmazza. Egy kényelmes segédprogram-függvénnyel megkeresheti a terjesztési oszlop nevét a metaadatok alsó szintű adataiból. Íme egy példa és a kimenete:

``` postgresql
-- create example table

CREATE TABLE products (
  store_id bigint,
  product_id bigint,
  name text,
  price money,

  CONSTRAINT products_pkey PRIMARY KEY (store_id, product_id)
);

-- pick store_id as distribution column

SELECT create_distributed_table('products', 'store_id');

-- get distribution column name for products table

SELECT column_to_column_name(logicalrelid, partkey) AS dist_col_name
  FROM pg_dist_partition
 WHERE logicalrelid='products'::regclass;
```

Példa a kimenetre:

```
┌───────────────┐
│ dist_col_name │
├───────────────┤
│ store_id      │
└───────────────┘
```

## <a name="detecting-locks"></a>Zárolások észlelése

Ez a lekérdezés az összes munkavégző csomóponton fut, és azonosítja a zárolásokat, a megnyíló időtartamot és a jogsértő lekérdezéseket:

``` postgresql
SELECT run_command_on_workers($cmd$
  SELECT array_agg(
    blocked_statement || ' $ ' || cur_stmt_blocking_proc
    || ' $ ' || cnt::text || ' $ ' || age
  )
  FROM (
    SELECT blocked_activity.query    AS blocked_statement,
           blocking_activity.query   AS cur_stmt_blocking_proc,
           count(*)                  AS cnt,
           age(now(), min(blocked_activity.query_start)) AS "age"
    FROM pg_catalog.pg_locks         blocked_locks
    JOIN pg_catalog.pg_stat_activity blocked_activity
      ON blocked_activity.pid = blocked_locks.pid
    JOIN pg_catalog.pg_locks         blocking_locks
      ON blocking_locks.locktype = blocked_locks.locktype
     AND blocking_locks.DATABASE IS NOT DISTINCT FROM blocked_locks.DATABASE
     AND blocking_locks.relation IS NOT DISTINCT FROM blocked_locks.relation
     AND blocking_locks.page IS NOT DISTINCT FROM blocked_locks.page
     AND blocking_locks.tuple IS NOT DISTINCT FROM blocked_locks.tuple
     AND blocking_locks.virtualxid IS NOT DISTINCT FROM blocked_locks.virtualxid
     AND blocking_locks.transactionid IS NOT DISTINCT FROM blocked_locks.transactionid
     AND blocking_locks.classid IS NOT DISTINCT FROM blocked_locks.classid
     AND blocking_locks.objid IS NOT DISTINCT FROM blocked_locks.objid
     AND blocking_locks.objsubid IS NOT DISTINCT FROM blocked_locks.objsubid
     AND blocking_locks.pid != blocked_locks.pid
    JOIN pg_catalog.pg_stat_activity blocking_activity ON blocking_activity.pid = blocking_locks.pid
    WHERE NOT blocked_locks.GRANTED
     AND blocking_locks.GRANTED
    GROUP BY blocked_activity.query,
             blocking_activity.query
    ORDER BY 4
  ) a
$cmd$);
```

Példa a kimenetre:

```
┌───────────────────────────────────────────────────────────────────────────────────┐
│                               run_command_on_workers                              │
├───────────────────────────────────────────────────────────────────────────────────┤
│ (10.0.0.16,5432,t,"")                                                             │
│ (10.0.0.20,5432,t,"{""update ads_102277 set name = 'new name' where id = 1; $ sel…│
│…ect * from ads_102277 where id = 1 for update; $ 1 $ 00:00:03.729519""}")         │
└───────────────────────────────────────────────────────────────────────────────────┘
```

## <a name="querying-the-size-of-your-shards"></a>A szegmensek méretének lekérdezése

Ez a lekérdezés egy adott elosztott tábla minden szegmensének méretét adja meg, amelyet a rendszer a következő néven tartalmaz `my_distributed_table` :

``` postgresql
SELECT *
FROM run_command_on_shards('my_distributed_table', $cmd$
  SELECT json_build_object(
    'shard_name', '%1$s',
    'size',       pg_size_pretty(pg_table_size('%1$s'))
  );
$cmd$);
```

Példa a kimenetre:

```
┌─────────┬─────────┬───────────────────────────────────────────────────────────────────────┐
│ shardid │ success │                                result                                 │
├─────────┼─────────┼───────────────────────────────────────────────────────────────────────┤
│  102008 │ t       │ {"shard_name" : "my_distributed_table_102008", "size" : "2416 kB"}    │
│  102009 │ t       │ {"shard_name" : "my_distributed_table_102009", "size" : "3960 kB"}    │
│  102010 │ t       │ {"shard_name" : "my_distributed_table_102010", "size" : "1624 kB"}    │
│  102011 │ t       │ {"shard_name" : "my_distributed_table_102011", "size" : "4792 kB"}    │
└─────────┴─────────┴───────────────────────────────────────────────────────────────────────┘
```

## <a name="querying-the-size-of-all-distributed-tables"></a>Az összes elosztott tábla méretének lekérdezése

Ez a lekérdezés lekéri az egyes elosztott táblák méreteit és az indexek méretét.

``` postgresql
SELECT
  tablename,
  pg_size_pretty(
    citus_total_relation_size(tablename::text)
  ) AS total_size
FROM pg_tables pt
JOIN pg_dist_partition pp
  ON pt.tablename = pp.logicalrelid::text
WHERE schemaname = 'public';
```

Példa a kimenetre:

```
┌───────────────┬────────────┐
│   tablename   │ total_size │
├───────────────┼────────────┤
│ github_users  │ 39 MB      │
│ github_events │ 98 MB      │
└───────────────┴────────────┘
```

Figyelje meg, hogy vannak-e más nagy kapacitású (Citus) függvények az elosztott tábla méretének lekérdezéséhez: a [táblázat méretének meghatározása](howto-hyperscale-table-size.md).

## <a name="identifying-unused-indices"></a>A nem használt indexek azonosítása

A következő lekérdezés azonosít egy adott elosztott tábla munkavégző csomópontjain nem használt indexeket ( `my_distributed_table` ).

``` postgresql
SELECT *
FROM run_command_on_shards('my_distributed_table', $cmd$
  SELECT array_agg(a) as infos
  FROM (
    SELECT (
      schemaname || '.' || relname || '##' || indexrelname || '##'
                 || pg_size_pretty(pg_relation_size(i.indexrelid))::text
                 || '##' || idx_scan::text
    ) AS a
    FROM  pg_stat_user_indexes ui
    JOIN  pg_index i
    ON    ui.indexrelid = i.indexrelid
    WHERE NOT indisunique
    AND   idx_scan < 50
    AND   pg_relation_size(relid) > 5 * 8192
    AND   (schemaname || '.' || relname)::regclass = '%s'::regclass
    ORDER BY
      pg_relation_size(i.indexrelid) / NULLIF(idx_scan, 0) DESC nulls first,
      pg_relation_size(i.indexrelid) DESC
  ) sub
$cmd$);
```

Példa a kimenetre:

```
┌─────────┬─────────┬───────────────────────────────────────────────────────────────────────┐
│ shardid │ success │                            result                                     │
├─────────┼─────────┼───────────────────────────────────────────────────────────────────────┤
│  102008 │ t       │                                                                       │
│  102009 │ t       │ {"public.my_distributed_table_102009##some_index_102009##28 MB##0"}   │
│  102010 │ t       │                                                                       │
│  102011 │ t       │                                                                       │
└─────────┴─────────┴───────────────────────────────────────────────────────────────────────┘
```

## <a name="monitoring-client-connection-count"></a>Ügyfélkapcsolatok számának figyelése

Az alábbi lekérdezés a koordinátoron nyitva lévő kapcsolatokat számolja, és típus szerint csoportosítja őket.

``` sql
SELECT state, count(*)
FROM pg_stat_activity
GROUP BY state;
```

Példa a kimenetre:

```
┌────────┬───────┐
│ state  │ count │
├────────┼───────┤
│ active │     3 │
│ idle   │     3 │
│ ∅      │     6 │
└────────┴───────┘
```

## <a name="index-hit-rate"></a>Index találati aránya

Ez a lekérdezés az index találati arányát fogja biztosítani az összes csomóponton. Az index találati aránya hasznos annak meghatározásához, hogy milyen gyakran legyenek használatban indexek a lekérdezés során:

``` postgresql
SELECT nodename, result as index_hit_rate
FROM run_command_on_workers($cmd$
  SELECT CASE sum(idx_blks_hit)
    WHEN 0 THEN 'NaN'::numeric
    ELSE to_char((sum(idx_blks_hit) - sum(idx_blks_read)) / sum(idx_blks_hit + idx_blks_read), '99.99')::numeric
    END AS ratio
  FROM pg_statio_user_indexes
$cmd$);
```

Példa a kimenetre:

```
┌───────────┬────────────────┐
│ nodename  │ index_hit_rate │
├───────────┼────────────────┤
│ 10.0.0.16 │ 0.88           │
│ 10.0.0.20 │ 0.89           │
└───────────┴────────────────┘
```

## <a name="cache-hit-rate"></a>Gyorsítótár találati aránya

A legtöbb alkalmazás általában csak egy kis hányadát fér hozzá az összes adathoz. A PostgreSQL a memóriában tartja a gyakran használt adatokat, hogy elkerülje a lemezről érkező lassú olvasást. A [pg_statio_user_tables](https://www.postgresql.org/docs/current/monitoring-stats.html#MONITORING-PG-STATIO-ALL-TABLES-VIEW) nézetben megjelenítheti a statisztikát.

Fontos, hogy az adatok mekkora hányada származik a memória-gyorsítótárból és a számítási feladatban lévő lemezből:

``` postgresql
SELECT
  sum(heap_blks_read) AS heap_read,
  sum(heap_blks_hit)  AS heap_hit,
  sum(heap_blks_hit) / (sum(heap_blks_hit) + sum(heap_blks_read)) AS ratio
FROM
  pg_statio_user_tables;
```

Példa a kimenetre:

```
 heap_read | heap_hit |         ratio
-----------+----------+------------------------
         1 |      132 | 0.99248120300751879699
```

Ha az arány jelentősen alacsonyabb, mint 99%, akkor valószínűleg érdemes megfontolnia, hogy a gyorsítótár elérhető legyen az adatbázis számára.

## <a name="next-steps"></a>Következő lépések

* További információ a diagnosztika hasznos egyéb [rendszertábláiról](reference-hyperscale-metadata.md)
