---
title: 'Oktatóanyag: a munkavégző csomópontok szegmense szerinti adathalmazok – nagy kapacitású (Citus) – Azure Database for PostgreSQL'
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre elosztott táblázatokat, és hogyan jelenítheti meg az adateloszlást Azure Database for PostgreSQL nagy kapacitású (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 12/16/2020
ms.openlocfilehash: 7d93002af866aa653972182a13ea37d37e912ce8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97630309"
---
# <a name="tutorial-shard-data-on-worker-nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Oktatóanyag: az Azure Database for PostgreSQL-beli munkavégző csomópontok szegmense szerinti adathalmazok – nagy kapacitású (Citus)

Ebben az oktatóanyagban a Azure Database for PostgreSQL-nagy kapacitású (Citus) használatával megismerheti a következőket:

> [!div class="checklist"]
> * Kivonatok létrehozása – elosztott szegmensek
> * Itt láthatja, hol vannak elhelyezve a tábla szegmensei
> * Ferde eloszlás azonosítása
> * Megkötések létrehozása az elosztott táblákon
> * Lekérdezések futtatása elosztott adatforgalomhoz

## <a name="prerequisites"></a>Előfeltételek

Ehhez az oktatóanyaghoz egy futó nagy kapacitású-(Citus-) kiszolgálócsoport szükséges két feldolgozói csomóponttal. Ha nem rendelkezik futó kiszolgálói csoporttal, kövesse a [kiszolgálócsoport létrehozása](tutorial-hyperscale-server-group.md) oktatóanyagot, és térjen vissza ehhez a csoporthoz.

## <a name="hash-distributed-data"></a>Kivonat – elosztott adatforgalom

A táblázat sorainak több PostgreSQL-kiszolgálón való terjesztése a nagy kapacitású (Citus) méretezhető lekérdezésének egyik kulcsfontosságú módszere. Egyszerre több csomópont is tarthat több adatmennyiséget, mint egy hagyományos adatbázis, és számos esetben párhuzamosan használhatja a feldolgozói processzorokat a lekérdezések végrehajtásához.

Az előfeltételek szakaszban létrehozott egy nagy kapacitású-(Citus-) kiszolgálót két feldolgozói csomóponttal.

![koordinátor és két feldolgozó](tutorial-hyperscale-shard/nodes.png)

A koordinátori csomópont metaadat-táblái nyomon követik a dolgozókat és az elosztott adatokat. Az aktív munkatársakat a [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table) táblában tekintheti meg.

```sql
select nodeid from pg_dist_node where isactive;
```
```
 nodeid | nodename
--------+-----------
      1 | 10.0.0.21
      2 | 10.0.0.23
```

> [!NOTE]
> A nagy kapacitású (Citus) Nodenames belső IP-címek egy virtuális hálózaton belül, és a ténylegesen megjelenő címek eltérőek lehetnek.

### <a name="rows-shards-and-placements"></a>Sorok, szilánkok és elhelyezések

A munkavégző csomópontok CPU-és tárolási erőforrásainak használatához a tábla-adatmennyiséget a kiszolgálói csoporton belül kell terjeszteni.  Egy tábla terjesztése az egyes sorokat egy szegmens nevű logikai csoportba rendeli *.* Hozzon létre egy táblázatot, és ossza meg a következőket:

```sql
-- create a table on the coordinator
create table users ( email text primary key, bday date not null );

-- distribute it into shards on workers
select create_distributed_table('users', 'email');
```

A nagy kapacitású (Citus) az egyes sorokat egy szegmenshez rendeli, a *terjesztési oszlop* értéke alapján, ami esetünkben a következőt adta: `email` . Minden sor pontosan egy szegmensbe kerül, és minden szegmens több sort is tartalmazhat.

![a felhasználók tábla a szegmensekre mutató sorokkal](tutorial-hyperscale-shard/table.png)

Alapértelmezés szerint `create_distributed_table()` a 32-as szegmenseket teszi elérhetővé, ahogy azt a metaadatok táblázatának [pg_dist_shard](reference-hyperscale-metadata.md#shard-table):

```sql
select logicalrelid, count(shardid)
  from pg_dist_shard
 group by logicalrelid;
```
```
 logicalrelid | count
--------------+-------
 users        |    32
```

A nagy kapacitású (Citus) a táblát használja a sorok szegmensekhez `pg_dist_shard` rendeléséhez a terjesztési oszlopban lévő érték kivonata alapján. Ebben az oktatóanyagban a kivonatolási adatok nem fontosak. Az is fontos, hogy a lekérdezéssel megtekintheti, hogy mely értékek képezhetők le a szegmensek azonosítói:

```sql
-- Where would a row containing hi@test.com be stored?
-- (The value doesn't have to actually be present in users, the mapping
-- is a mathematical operation consulting pg_dist_shard.)
select get_shard_id_for_distribution_column('users', 'hi@test.com');
```
```
 get_shard_id_for_distribution_column
--------------------------------------
                               102008
```

A sorok szegmensekre való leképezése tisztán logikai. A szegmenseket a tároláshoz meghatározott munkavégző csomópontokhoz kell hozzárendelni, amelyekben a nagy kapacitású (Citus) a szegmens *elhelyezését* hívja meg.

![feldolgozókhoz rendelt szegmensek](tutorial-hyperscale-shard/shard-placement.png)

Megvizsgáljuk [pg_dist_placement](reference-hyperscale-metadata.md#shard-placement-table)szegmensek elhelyezéseit is.
Összekapcsolja azt a többi metaadat-táblázattal, amelyekben látható, hogy hol vannak az egyes szegmensek.

```sql
-- limit the output to the first five placements

select
    shard.logicalrelid as table,
    placement.shardid as shard,
    node.nodename as host
from
    pg_dist_placement placement,
    pg_dist_node node,
    pg_dist_shard shard
where placement.groupid = node.groupid
  and shard.shardid = placement.shardid
order by shard
limit 5;
```
```
 table | shard  |    host
-------+--------+------------
 users | 102008 | 10.0.0.21
 users | 102009 | 10.0.0.23
 users | 102010 | 10.0.0.21
 users | 102011 | 10.0.0.23
 users | 102012 | 10.0.0.21
```

### <a name="data-skew"></a>Az adattorzítás

A kiszolgálócsoport a leghatékonyabban fut, ha a munkavégző csomópontokon egyenletesen helyezi el az adattárolást, és ha egymással összefüggő adatait ugyanazon a feldolgozón helyezi el. Ebben a szakaszban az elhelyezés egységességét fogjuk figyelembe venni.

Ennek bemutatásához hozzon létre mintaadatok a `users` táblához:

```sql
-- load sample data
insert into users
select
    md5(random()::text) || '@test.com',
    date_trunc('day', now() - random()*'100 years'::interval)
from generate_series(1, 1000);
```

A szegmensek méretének megjelenítéséhez a szegmensek [táblázatos méret függvényeit](https://www.postgresql.org/docs/current/functions-admin.html#FUNCTIONS-ADMIN-DBSIZE) is futtathatjuk.

```sql
-- sizes of the first five shards
select *
from
    run_command_on_shards('users', $cmd$
      select pg_size_pretty(pg_table_size('%1$s'));
    $cmd$)
order by shardid
limit 5;
```
```
 shardid | success | result
---------+---------+--------
  102008 | t       | 16 kB
  102009 | t       | 16 kB
  102010 | t       | 16 kB
  102011 | t       | 16 kB
  102012 | t       | 16 kB
```

Láthatjuk, hogy a szegmensek mérete egyenlő. Már láttuk, hogy a kihelyezés a feldolgozók között egyenletesen oszlik meg, ezért azt is tudjuk következtetni, hogy a feldolgozó csomópontok nagyjából azonos számú sort tartanak fenn.

A példában szereplő sorok `users` egyenletesen oszlanak el, mivel a terjesztési oszlop tulajdonságai megtalálhatók `email` .

1. Az e-mail-címek száma nagyobb vagy egyenlő, mint a szegmensek száma.
2. Az e-mail-címeken belüli sorok száma hasonló volt (esetünkben pontosan egy sor van megadva, mert az e-mail-címet deklaráljuk).

Bármely olyan tábla-és terjesztési oszlop közül választhat, amelyben bármelyik tulajdonság meghiúsul, és a feldolgozók egyenetlen adatméretet eredményeznek, azaz az *adat-elferdítés*.

### <a name="add-constraints-to-distributed-data"></a>Megkötések hozzáadása az elosztott adatforgalomhoz

A nagy kapacitású (Citus) használata lehetővé teszi, hogy továbbra is élvezhesse a kapcsolódó adatbázisok biztonságát, beleértve az [adatbázisra vonatkozó korlátozásokat](https://www.postgresql.org/docs/current/ddl-constraints.html)is.
Van azonban egy korlátozás. Az elosztott rendszerek jellegéből adódóan a nagy kapacitású (Citus) nem hivatkozhat az egyediségi megkötésekre vagy a munkavégző csomópontok közötti hivatkozási integritásra.

Tekintsük át a `users` táblázat példáját egy kapcsolódó táblával.

```sql
-- books that users own
create table books (
    owner_email text references users (email),
    isbn text not null,
    title text not null
);

-- distribute it
select create_distributed_table('books', 'owner_email');
```

A hatékonyság érdekében a `books` `users` tulajdonos e-mail-címével megegyező módon osztjuk meg a következőt:. A hasonló oszlopok értékeinek terjesztése a következő: [egyhelyes](concepts-hyperscale-colocation.md).

Nem volt probléma a könyvek külső kulccsal való terjesztése a felhasználók számára, mert a kulcs egy terjesztési oszlopban volt. Azonban nem sikerült `isbn` a kulcs készítése:

```sql
-- will not work
alter table books add constraint books_isbn unique (isbn);
```
```
ERROR:  cannot create constraint on "books"
DETAIL: Distributed relations cannot have UNIQUE, EXCLUDE, or
        PRIMARY KEY constraints that do not include the partition column
        (with an equality operator if EXCLUDE).
```

Egy elosztott táblázatban a legjobb, ha az oszlopok egyedi, a terjesztési oszlopból álló oszlopokat alkotnak:

```sql
-- a weaker constraint is allowed
alter table books add constraint books_isbn unique (owner_email, isbn);
```

A fenti korlátozás csupán egyedi ISBN-t tesz lehetővé felhasználónként. Egy másik lehetőség, hogy egy, az elosztott táblázat helyett egy [hivatkozási táblázatot](howto-hyperscale-modify-distributed-tables.md#reference-tables) hozzon létre, és létrehoz egy külön elosztott táblázatot, amely a könyveket társítja a felhasználókkal.

## <a name="query-distributed-tables"></a>Elosztott táblák lekérdezése

Az előző fejezetekben láttuk, hogyan helyezhetők el az elosztott táblák sorai a munkavégző csomópontokon. Az idő nagy részében nem kell tudnia, hogyan vagy hol tárolja az adattárolást egy kiszolgálócsoport. A nagy kapacitású (Citus) olyan elosztott lekérdezés-végrehajtót tartalmaz, amely automatikusan felbontja a normál SQL-lekérdezéseket. Ezeket párhuzamosan futtatja a munkavégző csomópontokon az adatkezeléshez.

Például futtathatunk egy lekérdezést a felhasználók átlagos életkorának megkereséséhez, az elosztott tábla kezeléséhez `users` hasonlóan, mint egy normál tábla a koordinátoron.

```sql
select avg(current_date - bday) as avg_days_old from users;
```
```
    avg_days_old
--------------------
 17926.348000000000
```

![a lekérdezés a szegmenseket a koordinátoron keresztül fogja felvenni](tutorial-hyperscale-shard/query-fragments.png)

A színfalak mögött a nagy kapacitású (Citus) végrehajtója külön lekérdezést hoz létre az egyes szegmensekhez, futtatja őket a feldolgozókon, és kombinálja az eredményt. A PostgreSQL-magyarázat parancs használata esetén a következőt láthatja:

```sql
explain select avg(current_date - bday) from users;
```
```
                                  QUERY PLAN
----------------------------------------------------------------------------------
 Aggregate  (cost=500.00..500.02 rows=1 width=32)
   ->  Custom Scan (Citus Adaptive)  (cost=0.00..0.00 rows=100000 width=16)
     Task Count: 32
     Tasks Shown: One of 32
     ->  Task
       Node: host=10.0.0.21 port=5432 dbname=citus
       ->  Aggregate  (cost=41.75..41.76 rows=1 width=16)
         ->  Seq Scan on users_102040 users  (cost=0.00..22.70 rows=1270 width=4)
```

A kimenet egy példát mutat be egy, a szegmens 102040-on futó *lekérdezési töredékre* (a Worker 10.0.0.21 táblára) vonatkozó végrehajtási tervre `users_102040` . A többi töredék nem jelenik meg, mert hasonlóak. Láthatjuk, hogy a feldolgozó csomópont megkeresi a szegmens táblákat, és alkalmazza az összesítést. A koordinátor csomópont a végső eredmény összesítéseit kombinálja.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehoztunk egy elosztott táblázatot, és megismertük a szegmenseit és elhelyezéseit. Feltettük az egyediség és a külső kulcsok korlátozásait, és végül azt is láttuk, hogy az elosztott lekérdezések hogyan működnek magas szinten.

* További információ a nagy kapacitású (Citus) [típusú táblákról](concepts-hyperscale-nodes.md)
* További tippek a [terjesztési oszlopok kiválasztásához](concepts-hyperscale-choose-distribution-column.md)
* A [táblázatos elhelyezés](concepts-hyperscale-colocation.md) előnyeinek megismerése
