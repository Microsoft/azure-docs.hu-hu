---
title: Rendszertáblák – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Elosztott lekérdezés-végrehajtás metaadatainak
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 74403365fe48584fa5d1db0e349c9dfc3772d874
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97652852"
---
# <a name="system-tables-and-views"></a>Rendszertáblák és nézetek

A nagy kapacitású (Citus) olyan speciális táblákat hoz létre és tart fenn, amelyek adatokat tartalmaznak a kiszolgálói csoportban lévő elosztott adatokról. A koordinátor csomópontja a lekérdezéseknek a munkavégző csomópontokon való futtatásának megtervezése során kérdezi le ezeket a táblázatokat.

## <a name="coordinator-metadata"></a>Koordinátori metaadatok

A nagy kapacitású (Citus) az egyes elosztott táblákat több logikai szegmensre osztja a terjesztési oszlop alapján. A koordinátor ezután metaadatokat tart fenn a statisztikai adatok és a szegmensek állapotával és helyével kapcsolatos információk nyomon követése érdekében.

Ebben a szakaszban ezeket a metaadat-táblákat és azok sémáját írják le.
Ezeket a táblákat az SQL használatával tekintheti meg és kérdezheti le a koordinátor csomópontba való bejelentkezés után.

> [!NOTE]
>
> Előfordulhat, hogy a Citus-motor régebbi verzióit futtató nagy kapacitású-(Citus-) kiszolgálócsoportok nem biztosítanak az alább felsorolt táblákat.

### <a name="partition-table"></a>Partíciós tábla

A PG \_ dist \_ Partition tábla tárolja a metaadatokat arról, hogy az adatbázis mely táblái vannak elosztva. Minden elosztott tábla esetében a terjesztési módszerről és a terjesztési oszlop részletes adatairól is információt tárol.

| Név         | Típus     | Leírás                                                                                                                                                                                                                                           |
|--------------|----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| logicalrelid | regclass | Az elosztott tábla, amelyhez ez a sor megfelel. Ez az érték a pg_class Rendszerkatalógus tábla relfilenode oszlopára hivatkozik.                                                                                                                   |
| partmethod   | char     | A particionáláshoz/terjesztéshez használt metódus. A különböző terjesztési módszereknek megfelelő oszlop értékei hozzáfűzése: "a", kivonat: "h", hivatkozási tábla: "n"                                                                          |
| partkey      | szöveg     | Részletes információk a terjesztési oszlopról, beleértve az oszlop számát, típusát és egyéb kapcsolódó információkat.                                                                                                                                      |
| colocationid | egész szám  | Az a csoportos elhelyezési csoport, amelyhez ez a tábla tartozik. Ugyanabban a csoportban lévő táblák lehetővé teszik a közös elhelyezésű illesztések és az elosztott összesítések használatát más optimalizációk között. Ez az érték a pg_dist_colocation tábla colocationid oszlopára hivatkozik.                      |
| repmodel     | char     | Az adatreplikáláshoz használt metódus. Ennek az oszlopnak a különböző replikációs módszereknek megfelelő értékei a következők: Citus-utasításokon alapuló replikáció: "c", PostgreSQL adatfolyam-továbbítási replikáció: ", kétfázisú végrehajtás (hivatkozási táblákhoz): 't" |

```
SELECT * from pg_dist_partition;
 logicalrelid  | partmethod |                                                        partkey                                                         | colocationid | repmodel 
---------------+------------+------------------------------------------------------------------------------------------------------------------------+--------------+----------
 github_events | h          | {VAR :varno 1 :varattno 4 :vartype 20 :vartypmod -1 :varcollid 0 :varlevelsup 0 :varnoold 1 :varoattno 4 :location -1} |            2 | c
 (1 row)
```

### <a name="shard-table"></a>Szegmens tábla

A PG \_ dist \_ szegmens táblázat a tábla egyes részeinek metaadatait tárolja. Pg_dist_shard információval rendelkezik arról, hogy az elosztott tábla mely szegmensekre vonatkozik, valamint a szegmensek eloszlás oszlopával kapcsolatos statisztikát.
Az elosztott táblák hozzáfűzése esetén ezek a statisztikák a terjesztési oszlop min/max értékének felelnek meg. Kivonatoló elosztott táblák esetén ezek a szegmenshez rendelt kivonatoló jogkivonat-tartományok. Ezek a statisztikák a nem kapcsolódó szegmensek törlésére szolgálnak a választó lekérdezések során.

| Név          | Típus     | Leírás                                                                                                                                                                                  |
|---------------|----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| logicalrelid  | regclass | Az elosztott tábla, amelyhez ez a sor megfelel. Ez az érték a pg_class Rendszerkatalógus tábla relfilenode oszlopára hivatkozik.                                                          |
| shardid       | bigint   | Globálisan egyedi azonosító társítva ehhez a szegmenshez.                                                                                                                                           |
| shardstorage  | char     | A szegmenshez használt tároló típusa. Az alábbi táblázatban különböző típusú tárolási típusok jelennek meg.                                                                                               |
| shardminvalue | szöveg     | Az elosztott táblák hozzáfűzése, a felosztási oszlop minimális értéke ebben a szegmensben (beleértve a következőt). Kivonatoló elosztott táblák esetén az adott szegmenshez hozzárendelt minimális kivonatoló jogkivonat értéke (beleértve a következőt is). |
| shardmaxvalue | szöveg     | Elosztott táblák hozzáfűzése esetén ebben a szegmensben (beleértve) a terjesztési oszlop maximális értéke. Kivonatoló elosztott táblák esetében az adott szegmenshez hozzárendelt kivonatoló jogkivonat maximális értéke (beleértve az értéket is). |

```
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue 
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

#### <a name="shard-storage-types"></a>Szegmens típusú tárolók

A PG dist szegmens shardstorage oszlopa a szegmenshez \_ \_ használt tárterület típusát jelöli. Az alábbi rövid áttekintést nyújt a különböző szegmens típusú tárolási típusokról és azok ábrázolásáról.

| Tárhelytípusa | Shardstorage érték | Leírás                                                                        |
|--------------|--------------------|------------------------------------------------------------------------------------|
| TÁBLA        | nem                | Azt jelzi, hogy a szegmens egy normál elosztott táblához tartozó adattárolót tárol.         |
| OSZLOPOS     | c                | Azt jelzi, hogy a szegmens oszlopos adattárolást tárol. (Elosztott cstore_fdw-táblázatok használják) |
| KÜLFÖLDI      | f                | Azt jelzi, hogy a szegmens a külső adattárolást tárolja. (Elosztott file_fdw-táblázatok használják)    |

### <a name="shard-placement-table"></a>Szilánk elhelyezése tábla

A PG \_ dist \_ elhelyezési táblázat a munkavégző csomópontokon lévő szegmens replikák helyét követi nyomon. Egy adott csomóponthoz rendelt szegmens minden replikáját egy szegmens elhelyezésnek nevezzük. Ez a táblázat az egyes szegmensek állapotával és helyével kapcsolatos információkat tárolja.

| Név        | Típus   | Leírás                                                                                                                               |
|-------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------|
| shardid     | bigint | Az elhelyezéshez társított szegmens-azonosító. Ez az érték a pg_dist_shard Catalog tábla shardid oszlopára hivatkozik.             |
| shardstate  | int    | Az elhelyezés állapotát ismerteti. Az alábbi szakaszban a különböző szegmens állapotokat tárgyaljuk.                                         |
| shardlength | bigint | Az elosztott táblák hozzáfűzése esetén a szegmens elhelyezésének mérete bájtban megadva a munkavégző csomóponton. Kivonatoló elosztott táblák esetén a nulla értéket.            |
| placementid | bigint | Egyedi, automatikusan generált azonosító minden egyes elhelyezéshez.                                                                           |
| GroupID     | int    | Egy elsődleges kiszolgáló és nulla vagy több másodlagos kiszolgáló csoportját jelöli, ha az adatfolyam-replikációs modell van használatban. |

```
SELECT * from pg_dist_placement;
  shardid | shardstate | shardlength | placementid | groupid
 ---------+------------+-------------+-------------+---------
   102008 |          1 |           0 |           1 |       1
   102008 |          1 |           0 |           2 |       2
   102009 |          1 |           0 |           3 |       2
   102009 |          1 |           0 |           4 |       3
   102010 |          1 |           0 |           5 |       3
   102010 |          1 |           0 |           6 |       4
   102011 |          1 |           0 |           7 |       4
```

#### <a name="shard-placement-states"></a>Szegmens elhelyezési állapotok

A nagy kapacitású (Citus) a szilánkok állapotát az elhelyezési alapon kezeli. Ha egy elhelyezés inkonzisztens állapotba helyezi a rendszerbe, a Citus automatikusan nem elérhetőként jelöli meg azt. Az elhelyezési állapot rögzítése a pg_dist_shard_placement táblában, a shardstate oszlopon belül történik. Íme egy rövid áttekintés a különböző szegmens elhelyezési állapotokról:

| Állapot neve | Shardstate érték | Leírás                                                                                                                                                                                                                                                                                                                                                                                                                         |
|------------|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| VÉGLEGESÍTETT  | 1                | Az állapot új szegmensek jönnek létre a alkalmazásban. Ebben az állapotban a szegmensek közötti elhelyezések naprakésznek tekintendők, és a lekérdezés tervezése és végrehajtása során használatosak.                                                                                                                                                                                                                                                                                 |
| INAKTÍV   | 3                | Az ebben az állapotban lévő szilánkok inaktívnak minősülnek, mivel az azonos szegmens más replikáinak szinkronizálása nem történt meg. Az állapot akkor fordulhat elő, ha egy Hozzáfűzés, módosítás (Beszúrás, frissítés, törlés) vagy egy DDL művelet sikertelen ehhez az elhelyezéshez. A lekérdezési tervező figyelmen kívül hagyja az ebben az állapotban lévő elhelyezéseket a tervezés és a végrehajtás során. A felhasználók az ezekben a szegmensekben található adatokat egy véglegesített replikával szinkronizálják háttérbeli tevékenységként. |
| TO_DELETE  | 4                | Ha a Citus egy master_apply_delete_command hívásra válaszol, és a művelet meghiúsul, a rendszer áthelyezi az elhelyezést erre az állapotba. A felhasználók ezután törölhetik ezeket a szegmenseket egy későbbi háttérbeli tevékenységként.                                                                                                                                                                                                              |

### <a name="worker-node-table"></a>Munkavégző csomópont táblája

A PG \_ dist \_ Node tábla a fürt munkavégző csomópontjaival kapcsolatos információkat tartalmaz.

| Név             | Típus    | Leírás                                                                                                                                                                                |
|------------------|---------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| nodeId           | int     | Egy adott csomóponthoz tartozó automatikusan generált azonosító.                                                                                                                                          |
| GroupID          | int     | Egy elsődleges kiszolgáló és nulla vagy több másodlagos kiszolgáló csoportjának jelölésére szolgáló azonosító, ha az adatfolyam-replikációs modell használatban van. Alapértelmezés szerint ez ugyanaz, mint a nodeId.         |
| csomópontnév         | szöveg    | A PostgreSQL Worker csomópont állomásneve vagy IP-címe.                                                                                                                                     |
| nodeport         | int     | A PostgreSQL munkavégző csomópontját figyelő portszám.                                                                                                                              |
| noderack         | szöveg    | Választható A munkavégző csomóponthoz tartozó állvány elhelyezési adatai.                                                                                                                                 |
| hasmetadata      | boolean | Belső használatra fenntartva.                                                                                                                                                                 |
| IsActive         | boolean | Azt jelzi, hogy aktív-e a csomópont a szilánkok fogadására.                                                                                                                                     |
| noderole         | szöveg    | Azt jelzi, hogy a csomópont elsődleges vagy másodlagos                                                                                                                                                 |
| nodecluster      | szöveg    | A csomópontot tartalmazó fürt neve                                                                                                                                               |
| shouldhaveshards | boolean | Ha a False (hamis), a szegmensek kimaradnak a csomópontról (kiürítve), ha az újrakiegyensúlyozást végzi, és az új elosztott táblákból származó szegmensek is el lesznek helyezve a csomópontra, kivéve, ha ezek a szegmensek már léteznek. |

```
SELECT * from pg_dist_node;
 nodeid | groupid | nodename  | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | shouldhaveshards
--------+---------+-----------+----------+----------+-------------+----------+----------+-------------+------------------
      1 |       1 | localhost |    12345 | default  | f           | t        | primary  | default     | t
      2 |       2 | localhost |    12346 | default  | f           | t        | primary  | default     | t
      3 |       3 | localhost |    12347 | default  | f           | t        | primary  | default     | t
(3 rows)
```

### <a name="distributed-object-table"></a>Elosztott objektum táblázata

Az citus.pg \_ dist \_ Object tábla olyan objektumok listáját tartalmazza, mint például a koordinátor csomóponton létrehozott és a feldolgozó csomópontokra propagált típusok és függvények. Amikor egy rendszergazda új munkavégző csomópontokat hoz létre a fürthöz, a nagy kapacitású (Citus) automatikusan létrehozza az elosztott objektumok másolatait az új csomópontokon (a megfelelő sorrendben az objektumok függőségeinek kielégítéséhez).

| Név                        | Típus    | Leírás                                          |
|-----------------------------|---------|------------------------------------------------------|
| ClassID                     | OID     | Az elosztott objektum osztálya                      |
| objid                       | OID     | Az elosztott objektum objektumazonosító                  |
| objsubid                    | egész szám | Az elosztott objektum objektum-alazonosítója, például attnum |
| típus                        | szöveg    | A PG-frissítések során használt stabil címek egy része   |
| object_names                | szöveg []  | A PG-frissítések során használt stabil címek egy része   |
| object_args                 | szöveg []  | A PG-frissítések során használt stabil címek egy része   |
| distribution_argument_index | egész szám | Csak az elosztott függvények/eljárások esetében érvényes      |
| colocationid                | egész szám | Csak az elosztott függvények/eljárások esetében érvényes      |

\"A stabil címek \" egyedi módon azonosítják az objektumokat egy adott kiszolgálótól függetlenül. A nagy kapacitású (Citus) az objektumokat egy PostgreSQL-frissítés során követi, és a [PG \_ \_ -azonosító \_ objektum \_ cím ()](https://www.postgresql.org/docs/current/functions-info.html#FUNCTIONS-INFO-OBJECT-TABLE) függvénnyel létrehozott stabil címeket használ.

Íme \' egy példa arra, hogyan adhat `create_distributed_function()` hozzá bejegyzéseket a `citus.pg_dist_object` táblához:

```psql
CREATE TYPE stoplight AS enum ('green', 'yellow', 'red');

CREATE OR REPLACE FUNCTION intersection()
RETURNS stoplight AS $$
DECLARE
        color stoplight;
BEGIN
        SELECT *
          FROM unnest(enum_range(NULL::stoplight)) INTO color
         ORDER BY random() LIMIT 1;
        RETURN color;
END;
$$ LANGUAGE plpgsql VOLATILE;

SELECT create_distributed_function('intersection()');

-- will have two rows, one for the TYPE and one for the FUNCTION
TABLE citus.pg_dist_object;
```

```
-[ RECORD 1 ]---------------+------
classid                     | 1247
objid                       | 16780
objsubid                    | 0
type                        |
object_names                |
object_args                 |
distribution_argument_index |
colocationid                |
-[ RECORD 2 ]---------------+------
classid                     | 1255
objid                       | 16788
objsubid                    | 0
type                        |
object_names                |
object_args                 |
distribution_argument_index |
colocationid                |
```

### <a name="colocation-group-table"></a>Együttes elhelyezési csoport tábla

A PG \_ dist \_ közös elhelyezési táblázata információt tartalmaz arról, hogy mely táblákat kell összekapcsolni \' , illetve elhelyezni. [](concepts-hyperscale-colocation.md)
Ha két tábla ugyanabban a közös helyen található, a nagy kapacitású (Citus) biztosítja, hogy ugyanazokat a feldolgozói csomópontokat tartalmazó szegmensek is ugyanazok legyenek.
A megosztott elhelyezés lehetővé teszi a csatlakoztatási optimalizációkat, bizonyos elosztott összesítéseket és a külső kulcsok támogatását. A szegmensek közötti közös elhelyezés akkor következtetett, ha a szegmensek száma, a replikálási tényezők és a partíciós oszlopok típusai mind egyeznek két tábla között. az elosztott táblák létrehozásakor azonban szükség esetén egy egyéni csoportos csoport is megadható.

| Név                   | Típus | Leírás                                                                   |
|------------------------|------|-------------------------------------------------------------------------------|
| colocationid           | int  | A tárolási csoport egyedi azonosítója, amely ebben a sorban a következőnek felel meg:.          |
| shardcount             | int  | A csoport összes táblájának szilánkok száma                          |
| replicationfactor      | int  | Replikációs tényező a csoport összes táblájához.                  |
| distributioncolumntype | OID  | A csoport összes táblájának terjesztési oszlopának típusa. |

```
SELECT * from pg_dist_colocation;
  colocationid | shardcount | replicationfactor | distributioncolumntype 
 --------------+------------+-------------------+------------------------
             2 |         32 |                 2 |                     20
  (1 row)
```

### <a name="rebalancer-strategy-table"></a>Kiegyensúlyozó stratégiai táblázat

Ez a táblázat azokat a stratégiákat határozza meg, amelyeket a [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) használhat a szegmensek áthelyezési helyének meghatározására.

| Név                           | Típus    | Leírás                                                                                                                                       |
|--------------------------------|---------|---------------------------------------------------------------------------------------------------------------------------------------------------|
| default_strategy               | boolean | Azt határozza meg, hogy rebalance_table_shards válassza ezt a stratégiát alapértelmezetten. Az oszlop frissítése citus_set_default_rebalance_strategy használatával             |
| shard_cost_function            | regproc | A Cost függvény azonosítója, amelynek shardid bigint kell lennie, és vissza kell adnia egy Cost (valódi típus) fogalmát.                                |
| node_capacity_function         | regproc | A Capacity függvény azonosítója, amelynek egy nodeId kell lennie, és vissza kell adnia a csomópont-kapacitás fogalmát valós típusként.                          |
| shard_allowed_on_node_function | regproc | A shardid bigint és a nodeidarg int értéket használó függvények azonosítója logikai értékre ad vissza, hogy a Citus tárolhatja-e a szegmenst a csomóponton. |
| default_threshold              | float4  | A csomópontok túl teljes vagy túl üresnek minősülő küszöbértéke, amely meghatározza, hogy a rebalance_table_shards Mikor próbálkozzon a szegmensek áthelyezésével                    |
| minimum_threshold              | float4  | A rebalance_table_shards () küszöbérték-argumentumának megakadályozása a túl alacsony értékre állításával                                                  |

A következő táblázatban szereplő stratégiákkal rendelkező nagy kapacitású (Citus) telepítése:

```postgresql
SELECT * FROM pg_dist_rebalance_strategy;
```

```
-[ RECORD 1 ]-------------------+-----------------------------------
Name                            | by_shard_count
default_strategy                | true
shard_cost_function             | citus_shard_cost_1
node_capacity_function          | citus_node_capacity_1
shard_allowed_on_node_function  | citus_shard_allowed_on_node_true
default_threshold               | 0
minimum_threshold               | 0
-[ RECORD 2 ]-------------------+-----------------------------------
Name                            | by_disk_size
default_strategy                | false
shard_cost_function             | citus_shard_cost_by_disk_size
node_capacity_function          | citus_node_capacity_1
shard_allowed_on_node_function  | citus_shard_allowed_on_node_true
default_threshold               | 0.1
minimum_threshold               | 0.01
```

Az alapértelmezett stratégia, amely minden szegmenshez `by_shard_count` ugyanazt a díjat rendeli. Ennek hatása a szegmensek közötti szegmensek számának kiegyenlítése a csomópontok között. A másik előre definiált stratégia `by_disk_size` minden olyan szegmenshez hozzárendeli a költségeket, amely megfelel a lemez méretének (bájtban), valamint a benne található szegmensek mennyiségét. A lemez méretének kiszámítása a használatával történik `pg_total_relation_size` , így indexeket is tartalmaz. Ez a stratégia megpróbál ugyanazon lemezterületet elérni minden csomóponton. Jegyezze fel a 0,1 küszöbértékét – megakadályozza, hogy a lemezterület nem jelentős különbségei miatt a felesleges szegmenses mozgást okozza.

#### <a name="creating-custom-rebalancer-strategies"></a>Egyéni újrakiegyensúlyozó stratégiák létrehozása

Íme néhány példa olyan függvényekre, amelyek felhasználhatók az új szegmensek közötti áttekintő stratégiákban, és a [citus_add_rebalance_strategy](reference-hyperscale-functions.md#citus_add_rebalance_strategy) függvénnyel regisztrálva vannak a [pg_dist_rebalance_strategyban](reference-hyperscale-metadata.md?#rebalancer-strategy-table) .

-   Csomópont-kapacitási kivétel beállítása állomásnév-minta alapján:

    ```postgresql
    CREATE FUNCTION v2_node_double_capacity(nodeidarg int)
        RETURNS boolean AS $$
        SELECT
            (CASE WHEN nodename LIKE '%.v2.worker.citusdata.com' THEN 2 ELSE 1 END)
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;
    ```

-   A szegmensre ugrásra kerülő lekérdezések száma szerinti kiegyensúlyozás a [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table)alapján mérve:

    ```postgresql
    -- example of shard_cost_function

    CREATE FUNCTION cost_of_shard_by_number_of_queries(shardid bigint)
        RETURNS real AS $$
        SELECT coalesce(sum(calls)::real, 0.001) as shard_total_queries
        FROM citus_stat_statements
        WHERE partition_key is not null
            AND get_shard_id_for_distribution_column('tab', partition_key) = shardid;
    $$ LANGUAGE sql;
    ```

-   Egy adott szegmens (10000) elkülönítése egy csomóponton ( \' 10.0.0.1 \' ):

    ```postgresql
    -- example of shard_allowed_on_node_function

    CREATE FUNCTION isolate_shard_10000_on_10_0_0_1(shardid bigint, nodeidarg int)
        RETURNS boolean AS $$
        SELECT
            (CASE WHEN nodename = '10.0.0.1' THEN shardid = 10000 ELSE shardid != 10000 END)
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;

    -- The next two definitions are recommended in combination with the above function.
    -- This way the average utilization of nodes is not impacted by the isolated shard.
    CREATE FUNCTION no_capacity_for_10_0_0_1(nodeidarg int)
        RETURNS real AS $$
        SELECT
            (CASE WHEN nodename = '10.0.0.1' THEN 0 ELSE 1 END)::real
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;
    CREATE FUNCTION no_cost_for_10000(shardid bigint)
        RETURNS real AS $$
        SELECT
            (CASE WHEN shardid = 10000 THEN 0 ELSE 1 END)::real
        $$ LANGUAGE sql;
    ```

### <a name="query-statistics-table"></a>Lekérdezési statisztikák táblázata

A nagy kapacitású (Citus) `citus_stat_statements` a lekérdezések végrehajtásával, valamint azokkal kapcsolatos statisztikákat biztosít. Ez \' a következőhöz hasonló: (és csatlakoztatható a) a PostgreSQL-ben a [PG \_ stat \_ utasítások](https://www.postgresql.org/docs/current/static/pgstatstatements.html) nézetében, amely nyomon követi a lekérdezési sebességgel kapcsolatos statisztikát.

Ez a nézet képes a több-bérlős alkalmazásban lévő bérlők lekérdezéseit nyomon követni, ami segít a bérlői elkülönítés eldöntésében.

| Név          | Típus   | Leírás                                                                      |
|---------------|--------|----------------------------------------------------------------------------------|
| queryid       | bigint | azonosító (jó pg_stat_statements illesztésekhez)                                   |
| userid        | OID    | a lekérdezést futtató felhasználó                                                           |
| DBID          | OID    | koordinátor adatbázis-példánya                                                 |
| lekérdezés         | szöveg   | névtelen lekérdezési karakterlánc                                                          |
| végrehajtó      | szöveg   | Használt Citus-végrehajtó: adaptív, valós idejű, feladat-követés, útválasztó vagy Beszúrás – kijelölés |
| partition_key | szöveg   | a terjesztési oszlop értéke az útválasztó által végrehajtott lekérdezésekben, máskülönben NULL               |
| hívások         | bigint | a lekérdezés futási idejének száma                                                |

```sql
-- create and populate distributed table
create table foo ( id int );
select create_distributed_table('foo', 'id');
insert into foo select generate_series(1,100);

-- enable stats
-- pg_stat_statements must be in shared_preload libraries
create extension pg_stat_statements;

select count(*) from foo;
select * from foo where id = 42;

select * from citus_stat_statements;
```

Eredmények:

```
-[ RECORD 1 ]-+----------------------------------------------
queryid       | -909556869173432820
userid        | 10
dbid          | 13340
query         | insert into foo select generate_series($1,$2)
executor      | insert-select
partition_key |
calls         | 1
-[ RECORD 2 ]-+----------------------------------------------
queryid       | 3919808845681956665
userid        | 10
dbid          | 13340
query         | select count(*) from foo;
executor      | adaptive
partition_key |
calls         | 1
-[ RECORD 3 ]-+----------------------------------------------
queryid       | 5351346905785208738
userid        | 10
dbid          | 13340
query         | select * from foo where id = $1
executor      | adaptive
partition_key | 42
calls         | 1
```

Figyelmeztetések

-   A statisztikai adatok nem replikálódnak, és a rendszer a \' t túlélte az adatbázis összeomlását vagy feladatátvételét
-   Korlátozott számú lekérdezést nyomon követ, amelyet a GUC határoz meg `pg_stat_statements.max` (alapértelmezett 5000)
-   A tábla csonkítása a `citus_stat_statements_reset()` függvény használatával

### <a name="distributed-query-activity"></a>Elosztott lekérdezési tevékenység

A nagy kapacitású (Citus) speciális nézeteket biztosít a lekérdezések és a zárolások megtekintésére a fürtben, beleértve az elosztott lekérdezések eredményeinek létrehozásához belsőleg használt szilánk-specifikus lekérdezéseket is.

-   **citus \_ dist \_ stat \_ tevékenység**: az összes csomóponton végrehajtás alatt álló elosztott lekérdezések megjelenítése. Egy, az `pg_stat_activity` utóbbi által felhasználható kibővített.
-   **citus \_ Worker \_ stat \_ tevékenység**: a feldolgozók lekérdezéseit jeleníti meg, beleértve az egyes szegmensekhez tartozó töredékes lekérdezéseket is.
-   **citus \_ zárolási \_ várakozások**: a fürt összes letiltott lekérdezése.

Az első két nézet a [PG \_ stats \_ tevékenység](https://www.postgresql.org/docs/current/static/monitoring-stats.html#PG-STAT-ACTIVITY-VIEW) összes oszlopát, valamint a lekérdezést kezdeményező feldolgozó gazdagépét/portját, valamint a fürt koordinátori csomópontjának gazdagépét/portját tartalmazza.

Tegyük fel például, hogy megszámolja az elosztott tábla sorait:

```postgresql
-- run from worker on localhost:9701

SELECT count(*) FROM users_table;
```

Láthatjuk, hogy a lekérdezés megjelenik a következő helyen `citus_dist_stat_activity` :

```postgresql
SELECT * FROM citus_dist_stat_activity;

-[ RECORD 1 ]----------+----------------------------------
query_hostname         | localhost
query_hostport         | 9701
master_query_host_name | localhost
master_query_host_port | 9701
transaction_number     | 1
transaction_stamp      | 2018-10-05 13:27:20.691907+03
datid                  | 12630
datname                | postgres
pid                    | 23723
usesysid               | 10
usename                | citus
application\_name      | psql
client\_addr           | 
client\_hostname       | 
client\_port           | -1
backend\_start         | 2018-10-05 13:27:14.419905+03
xact\_start            | 2018-10-05 13:27:16.362887+03
query\_start           | 2018-10-05 13:27:20.682452+03
state\_change          | 2018-10-05 13:27:20.896546+03
wait\_event_type       | Client
wait\_event            | ClientRead
state                  | idle in transaction
backend\_xid           | 
backend\_xmin          | 
query                  | SELECT count(*) FROM users_table;
backend\_type          | client backend
```

A lekérdezés minden szegmensről igényel információt. Néhány információ a szegmensben található `users_table_102038` , amely a következő helyen tárolódik: `localhost:9700` . Láthatjuk a szegmenshez hozzáférő lekérdezést a `citus_worker_stat_activity` nézet megtekintésével:

```postgresql
SELECT * FROM citus_worker_stat_activity;

-[ RECORD 1 ]----------+-----------------------------------------------------------------------------------------
query_hostname         | localhost
query_hostport         | 9700
master_query_host_name | localhost
master_query_host_port | 9701
transaction_number     | 1
transaction_stamp      | 2018-10-05 13:27:20.691907+03
datid                  | 12630
datname                | postgres
pid                    | 23781
usesysid               | 10
usename                | citus
application\_name      | citus
client\_addr           | ::1
client\_hostname       | 
client\_port           | 51773
backend\_start         | 2018-10-05 13:27:20.75839+03
xact\_start            | 2018-10-05 13:27:20.84112+03
query\_start           | 2018-10-05 13:27:20.867446+03
state\_change          | 2018-10-05 13:27:20.869889+03
wait\_event_type       | Client
wait\_event            | ClientRead
state                  | idle in transaction
backend\_xid           | 
backend\_xmin          | 
query                  | COPY (SELECT count(*) AS count FROM users_table_102038 users_table WHERE true) TO STDOUT
backend\_type          | client backend
```

A `query` mező azt mutatja, hogy a szegmensből kimásolt adatmennyiség megszámlálásra kerül.

> [!NOTE]
> Ha egy útválasztó-lekérdezés (például egy bérlő egy több-bérlős alkalmazásban, válassza a
> * A (z) tenant_id = X ') tábla nem tranzakciós blokk nélkül fut, majd a \_ főlekérdezési \_ gazdagép \_ neve és \_ a főlekérdezési \_ gazdagép \_ portjának oszlopai null értékűek lesznek a citus \_ Worker \_ stat \_ tevékenységben.

A `citus_lock_waits` működésének megtekintéséhez a zárolási helyzetet manuálisan is létrehozhatjuk. Először \' be kell állítania egy tesztelési táblázatot a koordinátortól:

```postgresql
CREATE TABLE numbers AS
  SELECT i, 0 AS j FROM generate_series(1,10) AS i;
SELECT create_distributed_table('numbers', 'i');
```

Ezután a koordinátoron két munkamenet használatával futtathatjuk a következő utasításokat:

```postgresql
-- session 1                           -- session 2
-------------------------------------  -------------------------------------
BEGIN;
UPDATE numbers SET j = 2 WHERE i = 1;
                                       BEGIN;
                                       UPDATE numbers SET j = 3 WHERE i = 1;
                                       -- (this blocks)
```

A `citus_lock_waits` nézet megjeleníti a helyzetet.

```postgresql
SELECT * FROM citus_lock_waits;

-[ RECORD 1 ]-------------------------+----------------------------------------
waiting_pid                           | 88624
blocking_pid                          | 88615
blocked_statement                     | UPDATE numbers SET j = 3 WHERE i = 1;
current_statement_in_blocking_process | UPDATE numbers SET j = 2 WHERE i = 1;
waiting_node_id                       | 0
blocking_node_id                      | 0
waiting_node_name                     | coordinator_host
blocking_node_name                    | coordinator_host
waiting_node_port                     | 5432
blocking_node_port                    | 5432
```

Ebben a példában a lekérdezések a koordinátortól származnak, de a nézet a feldolgozók által kezdeményezett lekérdezések közötti zárolásokat is listázhatja (nagy kapacitású-(Citus-) MX-példányok esetén végrehajtva).

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogy a [nagy kapacitású (Citus) függvények](reference-hyperscale-functions.md) hogyan változtathatják meg a rendszertáblákat
* A [csomópontok és táblák](concepts-hyperscale-nodes.md) fogalmának áttekintése
