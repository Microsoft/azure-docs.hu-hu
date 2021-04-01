---
title: SQL functions – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Függvények a nagy kapacitású (Citus) SQL API-ban
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: f324ef44d002f50bf27c08072e904c1d92b5512f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026233"
---
# <a name="functions-in-the-hyperscale-citus-sql-api"></a>Függvények a nagy kapacitású (Citus) SQL API-ban

Ez a szakasz a nagy kapacitású (Citus) által biztosított, felhasználó által definiált függvények hivatkozási információit tartalmazza. Ezek a függvények segítséget nyújtanak a szabványos SQL-parancsokon kívüli nagy kapacitású (Citus) további elosztott funkciók biztosításához.

> [!NOTE]
>
> Előfordulhat, hogy a Citus-motor régebbi verzióit futtató nagy kapacitású-(Citus-) kiszolgálócsoportok nem biztosítanak az összes alább felsorolt funkciót.

## <a name="table-and-shard-ddl"></a>Táblázat és szegmens DDL

### <a name="create_distributed_table"></a>\_elosztott \_ tábla létrehozása

Az \_ elosztott \_ tábla létrehozása () függvény használatával egy elosztott tábla definiálható, és létrehozható a szegmensek, ha kivonattal elosztott tábla. Ez a függvény egy tábla nevét, a terjesztési oszlopot és egy opcionális terjesztési módszert vesz igénybe, és beszúrja a megfelelő metaadatokat a tábla elosztottként való megjelöléséhez. A függvény alapértelmezés szerint a "hash" eloszlást adja meg, ha nincs megadva terjesztési módszer. Ha a tábla kivonattal van elterjesztve, a függvény a szegmensek száma és a szegmens replikációs faktor konfigurációs értékei alapján is létrehoz feldolgozói szegmenseket. Ha a tábla bármely sort tartalmaz, a rendszer automatikusan elosztja azokat a feldolgozó csomópontokra.

Ez a függvény lecseréli a Master \_ create \_ Distributed \_ Table () függvényt, majd a Master \_ create \_ Worker szegmenseket \_ ().

#### <a name="arguments"></a>Argumentumok

**Táblanév \_ :** az terjeszteni kívánt tábla neve.

**terjesztési \_ oszlop:** az az oszlop, amelyen a táblázatot terjeszteni kell.

**eloszlás \_ típusa:** (nem kötelező) az a módszer, amely szerint a tábla terjeszthető. A megengedett értékek a következők: Hozzáfűzés vagy kivonat, alapértelmezett érték: "hash".

**közös keresés \_ a** következővel: (nem kötelező) egy másik tábla közös elhelyezési csoportjában lévő aktuális tábla belefoglalása. Alapértelmezés szerint a táblázatok abban az esetben helyezkednek el, ha azonos típusú oszlopokban vannak elosztva, és ugyanazzal a replikálási tényezővel rendelkeznek. A lehetséges értékei a `colocate_with` következők: `default` `none` új közös elhelyezési csoport elindítása vagy egy másik tábla neve, amely a táblázattal együtt található.  (Lásd: [táblázatos elhelyezés](concepts-hyperscale-colocation.md).)

Ne feledje, hogy az alapértelmezett érték az `colocate_with` implicit elhelyezés. A közös [elhelyezés](concepts-hyperscale-colocation.md) nagyszerű dolog lehet, ha a táblák kapcsolódnak vagy csatlakozva lesznek.  Ha azonban két tábla nem kapcsolódik egymáshoz, de a terjesztési oszlopokhoz ugyanazt az adattípust használja, akkor véletlenül egymással is csökkentheti a teljesítményt a szegmensek közötti [újraelosztás](howto-hyperscale-scale-rebalance.md)során.  A tábla szegmensei szükségtelenül lesznek áthelyezve egy \" lépcsőzetesen.\"

Ha egy új elosztott tábla nem kapcsolódik más táblákhoz, érdemes megadnia a legjobbat `colocate_with => 'none'` .

#### <a name="return-value"></a>Visszatérési érték

N/A

#### <a name="example"></a>Példa

Ez a példa arról tájékoztatja az adatbázist, hogy a GitHub- \_ események táblázatának kivonattal kell elosztania a tárház \_ azonosító oszlopán.

```postgresql
SELECT create_distributed_table('github_events', 'repo_id');

-- alternatively, to be more explicit:
SELECT create_distributed_table('github_events', 'repo_id',
                                colocate_with => 'github_repo');
```

### <a name="create_reference_table"></a>\_hivatkozási \_ tábla létrehozása

A Create \_ Reference \_ Table () függvény egy kis hivatkozás vagy dimenzió tábla definiálására szolgál. Ez a függvény egy tábla nevét veszi át, és egy elosztott táblázatot hoz létre egyetlen szegmenssel, amely minden munkavégző csomópontra replikálódik.

#### <a name="arguments"></a>Argumentumok

**Táblanév \_ :** az terjeszteni kívánt kis dimenzió vagy hivatkozási tábla neve.

#### <a name="return-value"></a>Visszatérési érték

N/A

#### <a name="example"></a>Példa

Ez a példa tájékoztatja az adatbázist arról, hogy a nemzet táblázatát hivatkozási táblázatként kell meghatározni

```postgresql
SELECT create_reference_table('nation');
```

### <a name="upgrade_to_reference_table"></a>frissítés \_ a \_ hivatkozási \_ táblára

A frissítés \_ a \_ hivatkozási \_ táblára () függvény egy meglévő, a szegmensek közötti számú elosztott táblára mutat, és frissíti azt egy felismert hivatkozási táblázatként. A függvény meghívása után a tábla úgy fog működni, mintha az [create_reference_table](#create_reference_table)-vel lett létrehozva.

#### <a name="arguments"></a>Argumentumok

**Táblanév \_ :** az elosztott tábla neve (szegmensek száma = 1), amelyet a rendszer a hivatkozási táblázatként terjeszt.

#### <a name="return-value"></a>Visszatérési érték

N/A

#### <a name="example"></a>Példa

Ez a példa tájékoztatja az adatbázist arról, hogy a nemzet táblázatát hivatkozási táblázatként kell meghatározni

```postgresql
SELECT upgrade_to_reference_table('nation');
```

### <a name="mark_tables_colocated"></a>táblázatok megjelölése közös \_ \_ helyen

A megjelölési \_ táblák \_ () függvény egy elosztott táblát (a forrást) és a mások listáját (a célokat) veszi át, és a célokat ugyanabba a közös elhelyezésű csoportba helyezi, mint a forrás. Ha a forrás még nincs egy csoportban, akkor ez a függvény létrehoz egyet, és hozzárendeli a forrást és a célokat.

A táblázatok összeadásának időpontját a `colocate_with` [create_distributed_table](#create_distributed_table)paraméterének használatával kell elvégezni, de szükség esetén `mark_tables_colocated` később is elvégezhető.

#### <a name="arguments"></a>Argumentumok

**forrástábla \_ \_ neve:** annak az elosztott táblának a neve, amelynek a csoportjának egyeztetéséhez a célok hozzá lesznek rendelve.

**Céltábla \_ \_ neve:** az elosztott célként megadott táblák neveinek tömbje nem lehet üres. Ezeknek az elosztott tábláknak meg kell egyezniük a forrás táblával a következőben:

> -   terjesztési módszer
> -   terjesztési oszlop típusa
> -   replikálás típusa
> -   szegmensek száma

Ha a fentiek egyike sem érvényes, akkor a nagy kapacitású (Citus) hibát jelez. Ha például olyan táblákat próbál meg elhelyezni, `apples` `oranges` amelyek terjesztési oszlopainak típusai eltérőek a következő esetekben:

```
ERROR:  XX000: cannot colocate tables apples and oranges
DETAIL:  Distribution column types don't match for apples and oranges.
```

#### <a name="return-value"></a>Visszatérési érték

N/A

#### <a name="example"></a>Példa

Ez a példa a következőt helyezi `products` `line_items` el ugyanahhoz a közös elhelyezésű csoportban: `stores` . A példa azt feltételezi, hogy ezek a táblák mindegyike egy egyező típusú oszlopon van elosztva, valószínűleg egy \" áruházbeli azonosító.\"

```postgresql
SELECT mark_tables_colocated('stores', ARRAY['products', 'line_items']);
```

### <a name="create_distributed_function"></a>\_elosztott \_ függvény létrehozása

Egy függvényt propagál a koordinátor csomópontról a feldolgozók számára, és kijelöli azt az elosztott végrehajtáshoz. Ha egy elosztott függvényt hívnak meg a koordinátoron, a nagy kapacitású (Citus) a terjesztési argumentum értékét használja a \" \" feldolgozói csomópont kiválasztásához a függvény futtatásához. A funkciónak a feldolgozók általi végrehajtása növeli a párhuzamosságot, és az alacsonyabb késés érdekében a kódot a szegmensekben lévő adathoz közelebb helyezheti.

A postgres keresési útvonala nincs propagálva a koordinátortól a feldolgozók számára az elosztott függvények végrehajtása során, ezért az elosztott függvény kódjának teljes mértékben meg kell egyeznie az adatbázis-objektumok neveivel. A függvények által kibocsátott értesítések nem jelennek meg a felhasználó számára.

#### <a name="arguments"></a>Argumentumok

**függvény \_ neve:** az terjeszteni kívánt függvény neve. A névnek zárójelek közé kell tartoznia a függvény paraméterei között, mert több függvény is rendelkezhet ugyanazzal a névvel a PostgreSQL-ben. A például `'foo(int)'` különbözik a következőtől: `'foo(int, text)'` .

**terjesztési \_ ARG \_ neve:** (nem kötelező) a terjeszteni kívánt argumentum neve. A kényelem érdekében (vagy ha a függvény argumentumai nem rendelkeznek névvel), a pozíciós helyőrző engedélyezett, például: `'$1'` . Ha ez a paraméter nincs megadva, akkor az nevű függvény `function_name` csak a feldolgozók számára jön létre. Ha a munkavégző csomópontokat a jövőben hozzáadják, akkor a függvény automatikusan létrejön.

**közös elhelyezés \_ :** (nem kötelező) Ha az elosztott függvény egy elosztott táblába olvas vagy ír, akkor ügyeljen arra, hogy a paraméter használatával nevezze el a táblázatot `colocate_with` . Ezután a függvény minden meghívása az érintett szegmenseket tartalmazó munkavégző csomóponton fog futni.

#### <a name="return-value"></a>Visszatérési érték

N/A

#### <a name="example"></a>Példa

```postgresql
-- an example function which updates a hypothetical
-- event_responses table which itself is distributed by event_id
CREATE OR REPLACE FUNCTION
  register_for_event(p_event_id int, p_user_id int)
RETURNS void LANGUAGE plpgsql AS $fn$
BEGIN
  INSERT INTO event_responses VALUES ($1, $2, 'yes')
  ON CONFLICT (event_id, user_id)
  DO UPDATE SET response = EXCLUDED.response;
END;
$fn$;

-- distribute the function to workers, using the p_event_id argument
-- to determine which shard each invocation affects, and explicitly
-- colocating with event_responses which the function updates
SELECT create_distributed_function(
  'register_for_event(int, int)', 'p_event_id',
  colocate_with := 'event_responses'
);
```

## <a name="metadata--configuration-information"></a>Metaadatok/konfigurációs információk

### <a name="master_get_table_metadata"></a>Master \_ Get \_ Table \_ metaadatok

A Master \_ Get \_ Table \_ Metadata () függvény használható az elosztott táblák eloszlással kapcsolatos metaadatainak visszaküldéséhez. Ez a metaadatok tartalmazzák a kapcsolatok AZONOSÍTÓját, a tárolási típust, a terjesztési módszert, a terjesztési oszlopot, a replikálási darabszámot, a teljes szegmens méretét és a szegmens elhelyezési házirendjét a táblához. Ez a függvény lekérdezi a nagy kapacitású (Citus) metaadat-táblázatokat a szükséges információk beszerzéséhez, és összefűzi azt egy rekordba, mielőtt visszaadná azt a felhasználónak.

#### <a name="arguments"></a>Argumentumok

**Táblanév \_ :** annak az elosztott táblának a neve, amelynek a metaadatait be szeretné olvasni.

#### <a name="return-value"></a>Visszatérési érték

A következő adatokat tartalmazó rekord:

**logikai \_ relid:** az elosztott tábla OID azonosítója. A relfilenode oszlopra hivatkozik a PG \_ Class Rendszerkatalógus táblában.

**rész \_ tárolási \_ típusa:** a táblához használt tároló típusa. Lehetséges, hogy nem "(standard tábla)," f "(idegen tábla) vagy" c "(oszlopos tábla).

**rész \_ módszere:** a táblához használt terjesztési módszer. Lehet "a" (Hozzáfűzés) vagy "h" (kivonat).

**rész \_ kulcs:** a tábla terjesztési oszlopa.

**rész \_ replikáinak \_ száma:** a szegmensek jelenlegi replikálásának száma.

**rész \_ maximális \_ mérete:** a szilánkok jelenlegi maximális mérete bájtban megadva.

**rész \_ elhelyezési \_ házirendje:** a tábla szegmensei elhelyezésére használt szegmens elhelyezési házirend. Lehet 1 (helyi-csomópont-első) vagy 2 (ciklikus multiplexelés).

#### <a name="example"></a>Példa

Az alábbi példa beolvassa és megjeleníti a GitHub-események táblázatának táblázatos metaadatait \_ .

```postgresql
SELECT * from master_get_table_metadata('github_events');
 logical_relid | part_storage_type | part_method | part_key | part_replica_count | part_max_size | part_placement_policy 
---------------+-------------------+-------------+----------+--------------------+---------------+-----------------------
         24180 | t                 | h           | repo_id  |                  2 |    1073741824 |                     2
(1 row)
```

### <a name="get_shard_id_for_distribution_column"></a>\_ \_ \_ a \_ terjesztési \_ oszlophoz tartozó szegmens azonosítójának beolvasása

A nagy kapacitású (Citus) egy elosztott tábla minden sorát hozzárendeli egy szegmenshez a sor terjesztési oszlopának és a tábla eloszlási módszerének értéke alapján. A legtöbb esetben a pontos leképezés egy olyan alacsony szintű adat, amelyet az adatbázis-rendszergazda figyelmen kívül hagyhat. Azonban hasznos lehet a sorok szegmensének meghatározása, akár kézi adatbázis-karbantartási feladatok esetén, akár csak a kíváncsiság eléréséhez. A `get_shard_id_for_distribution_column` függvény megadja ezt az információt a kivonat és tartomány elosztott tábláihoz, valamint a hivatkozási táblákhoz. Nem működik a hozzáfűzési eloszlásban.

#### <a name="arguments"></a>Argumentumok

**táblázat \_ neve:** az elosztott tábla.

**terjesztési \_ érték:** a terjesztési oszlop értéke.

#### <a name="return-value"></a>Visszatérési érték

A szegmens azonosító nagy kapacitású (Citus) társítva van az adott tábla terjesztési oszlopának értékével.

#### <a name="example"></a>Példa

```postgresql
SELECT get_shard_id_for_distribution_column('my_table', 4);

 get_shard_id_for_distribution_column
--------------------------------------
                               540007
(1 row)
```

### <a name="column_to_column_name"></a>oszlop \_ \_ \_ neve

Lefordítja az `partkey` oszlopot `pg_dist_partition` egy szöveges oszlop nevére. A fordítás hasznos lehet egy elosztott tábla terjesztési oszlopának meghatározásához.

Részletesebb megbeszélést a [terjesztési oszlop kiválasztása](concepts-hyperscale-choose-distribution-column.md)című témakörben talál.

#### <a name="arguments"></a>Argumentumok

**táblázat \_ neve:** az elosztott tábla.

**oszlop \_ var \_ szövege:** a tábla értéke `partkey` `pg_dist_partition` .

#### <a name="return-value"></a>Visszatérési érték

A `table_name` terjesztési oszlop neve.

#### <a name="example"></a>Példa

```postgresql
-- get distribution column name for products table

SELECT column_to_column_name(logicalrelid, partkey) AS dist_col_name
  FROM pg_dist_partition
 WHERE logicalrelid='products'::regclass;
```

Kimenet:

```
┌───────────────┐
│ dist_col_name │
├───────────────┤
│ company_id    │
└───────────────┘
```

### <a name="citus_relation_size"></a>citus- \_ kapcsolatok \_ mérete

Szerezze be a megadott elosztott tábla összes szegmense által használt lemezterületet.
A lemezterület magában foglalja a \" fő elágazás méretét, \" de kizárja a láthatósági térképet és a szegmensek szabad területének leképezését.

#### <a name="arguments"></a>Argumentumok

**logicalrelid:** egy elosztott tábla neve.

#### <a name="return-value"></a>Visszatérési érték

Mérete bájtban bigint.

#### <a name="example"></a>Példa

```postgresql
SELECT pg_size_pretty(citus_relation_size('github_events'));
```

```
pg_size_pretty
--------------
23 MB
```

### <a name="citus_table_size"></a>citus \_ táblázat \_ mérete

Szerezze be a megadott elosztott tábla összes szegmense által használt lemezterületet, kivéve az indexeket (a PIRÍTÓSt, a szabad terület térképet és a láthatósági leképezést is beleértve).

#### <a name="arguments"></a>Argumentumok

**logicalrelid:** egy elosztott tábla neve.

#### <a name="return-value"></a>Visszatérési érték

Mérete bájtban bigint.

#### <a name="example"></a>Példa

```postgresql
SELECT pg_size_pretty(citus_table_size('github_events'));
```

```
pg_size_pretty
--------------
37 MB
```

### <a name="citus_total_relation_size"></a>citus \_ összesített \_ \_ mérete

Szerezze be a megadott elosztott tábla összes szegmense által használt teljes lemezterületet, beleértve az indexeket és a PIRÍTÓSt is.

#### <a name="arguments"></a>Argumentumok

**logicalrelid:** egy elosztott tábla neve.

#### <a name="return-value"></a>Visszatérési érték

Mérete bájtban bigint.

#### <a name="example"></a>Példa

```postgresql
SELECT pg_size_pretty(citus_total_relation_size('github_events'));
```

```
pg_size_pretty
--------------
73 MB
```

### <a name="citus_stat_statements_reset"></a>a citus \_ stat \_ utasításait \_ alaphelyzetbe állítja

Eltávolítja a [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table)összes sorát.
Ez a függvény a alkalmazástól függetlenül működik `pg_stat_statements_reset()` . Az összes statisztika alaphelyzetbe állításához hívja mindkét függvényt.

#### <a name="arguments"></a>Argumentumok

N/A

#### <a name="return-value"></a>Visszatérési érték

Nincsenek

## <a name="server-group-management-and-repair"></a>Kiszolgálócsoport-kezelés és-javítás

### <a name="master_copy_shard_placement"></a>fő \_ másolási szegmens \_ \_ elhelyezése

Ha egy szegmens elhelyezése nem frissül egy módosítási parancs vagy egy DDL-művelet során, akkor az inaktívként lesz megjelölve. A fő \_ másolási \_ \_ szegmens elhelyezési funkciója ezután meghívható, hogy egy kifogástalan állapotú adatok használatával javítsa ki az inaktív szegmensek elhelyezését.

A szegmensek kijavításához a függvény először eldobja a nem kifogástalan állapotú szegmens elhelyezést, és újra létrehozza a koordinátor sémájának használatával. A szegmens elhelyezése után a függvény átmásolja az adatokat a kifogástalan elhelyezésből, és frissíti a metaadatokat az új szegmens elhelyezésének Kifogástalan állapotba való megjelöléséhez. Ez a funkció biztosítja, hogy a szegmens a javítás során minden egyidejű módosítás esetén védve legyen.

#### <a name="arguments"></a>Argumentumok

**szegmens \_ azonosítója:** a kijavítani kívánt szegmens azonosítója.

**forrás \_ csomópont \_ neve:** annak a csomópontnak a DNS-neve, amelyen az egészséges szegmens elhelyezése található ( \" forrás \" csomópont).

**forrás \_ csomópont \_ portja:** a forrás munkavégző csomóponton található Port, amelyen az adatbázis-kiszolgáló figyel.

**cél \_ csomópont \_ neve:** annak a csomópontnak a DNS-neve, amelyen az érvénytelen szegmens elhelyezése található ( \" cél \" csomópont).

**cél \_ csomópont \_ portja:** a cél munkavégző csomópont azon portja, amelyen az adatbázis-kiszolgáló figyel.

#### <a name="return-value"></a>Visszatérési érték

N/A

#### <a name="example"></a>Példa

Az alábbi példa kijavítja a 12345-es porton található, a "hibás gazdagépen" futó adatbázis-kiszolgálót, amely az 5432-es \_ porton fut. A kijavításához a porton a "Good host"-t futtató kiszolgálón található, kifogástalan állapotú szegmensből származó adatok jelennek meg. \_
5432.

```postgresql
SELECT master_copy_shard_placement(12345, 'good_host', 5432, 'bad_host', 5432);
```

### <a name="master_move_shard_placement"></a>fő \_ áthelyezési szegmens \_ \_ elhelyezése

Ez a függvény az egyik csomópontról a másikra helyezi át az adott szilánkot (és a benne található szegmenseket). Általában közvetetten használatos a szegmensek közötti egyensúlyban, ahelyett, hogy közvetlenül egy adatbázis-rendszergazda hívja meg őket.

Az adatáthelyezés kétféleképpen végezhető el: blokkolás vagy Letiltás. A blokkolási módszer azt jelenti, hogy a rendszer szünetelteti az összes módosítás áthelyezése a szegmensbe.
A második módszer, amely megakadályozza, hogy blokkolja a szilánkok írásait, a postgres 10 logikai replikációra támaszkodik.

Sikeres áthelyezési művelet után a rendszer a forrás csomópont szegmenseit törli. Ha az áthelyezés bármely ponton meghiúsul, ez a függvény hibát jelez, és változatlanul hagyja a forrás-és a cél csomópontokat.

#### <a name="arguments"></a>Argumentumok

**szegmens \_ azonosítója:** az áthelyezni kívánt szegmens azonosítója.

**forrás \_ csomópont \_ neve:** annak a csomópontnak a DNS-neve, amelyen az egészséges szegmens elhelyezése található ( \" forrás \" csomópont).

**forrás \_ csomópont \_ portja:** a forrás munkavégző csomóponton található Port, amelyen az adatbázis-kiszolgáló figyel.

**cél \_ csomópont \_ neve:** annak a csomópontnak a DNS-neve, amelyen az érvénytelen szegmens elhelyezése található ( \" cél \" csomópont).

**cél \_ csomópont \_ portja:** a cél munkavégző csomópont azon portja, amelyen az adatbázis-kiszolgáló figyel.

**horizontális \_ átadási \_ mód:** (nem kötelező) adja meg a replikálási módszert, legyen szó a PostgreSQL logikai replikálásának vagy a több munkavégzős másolási parancsnak a használatáról. Lehetséges értékek:

> -   `auto`: A replika identitásának megkövetelése, ha a logikai replikáció lehetséges, ellenkező esetben használja a régi viselkedést (például a szegmensek javítása érdekében, PostgreSQL 9,6). Ez az alapértelmezett érték.
> -   `force_logical`: Logikai replikáció használata akkor is, ha a tábla nem rendelkezik replika-identitással. A replikáció során a táblázat minden egyidejű frissítési/törlési utasítása sikertelen lesz.
> -   `block_writes`: Használja a másolás (írások blokkolása) műveletet az elsődleges kulcs vagy a replika identitását nélkülöző táblákhoz.

#### <a name="return-value"></a>Visszatérési érték

N/A

#### <a name="example"></a>Példa

```postgresql
SELECT master_move_shard_placement(12345, 'from_host', 5432, 'to_host', 5432);
```

### <a name="rebalance_table_shards"></a>táblázatos szegmensek újraelosztása \_ \_

A (z \_ ) függvény a tábla szegmensei \_ () függvénye áthelyezi az adott tábla szegmenseit, hogy azok egyenletesen legyenek elosztva a feldolgozók között. A függvény először kiszámítja a szükséges áthelyezések listáját annak érdekében, hogy a kiszolgálócsoport a megadott küszöbértéken belül kiegyensúlyozott legyen. Ezután áthelyezi a szegmensek közötti elhelyezéseket a forrás csomópontról a cél csomópontra, és frissíti a megfelelő szegmens metaadatokat az áthelyezésnek megfelelően.

Minden szegmenshez hozzá kell rendelni egy díjat, amely meghatározza, hogy a szegmensek \" egyenletesen oszlanak-e el. \" Alapértelmezés szerint minden szegmensnek ugyanaz a díja (1 érték), így a feldolgozók költségeit egyenlően kell kiosztani, hogy kiegyenlítse az egyes szegmensek számát. Az állandó Cost-stratégiát a szegmensek \" száma alapján hívják \_ \_ \" , és ez az alapértelmezett kiegyenlítő stratégia.

Az alapértelmezett stratégia a következő esetekben megfelelő:

*  A szegmensek nagyjából azonos méretűek
*  A szegmensek nagyjából azonos mennyiségű forgalmat kapnak
*  A feldolgozó csomópontok mérete és típusa azonos
*  Nem rögzített szegmensek adott feldolgozók számára

Ha a feltételezések bármelyike nem tart fenn, akkor az alapértelmezett kiegyensúlyozás rossz tervet eredményezhet. Ebben az esetben a (z) paraméter használatával testreszabhatja a stratégiát `rebalance_strategy` .

A [](#get_rebalance_table_shards_plan) \_ \_ végrehajtandó műveletek megtekintéséhez és ellenőrzéséhez ajánlott a get_rebalance_table_shards_plan hívása a tábla újraelosztása előtt.

#### <a name="arguments"></a>Argumentumok

**Táblanév \_ :** (nem kötelező) annak a táblának a neve, amelynek a szegmenseit újra kell kiegyensúlyozni. Ha a NULL értéket adja meg, akkor az összes meglévő egyhelyes csoport újraelosztása.

**küszöbérték:** (nem kötelező) a 0,0 és 1,0 közötti lebegőpontos szám, amely a csomópont kihasználtságának maximális különbségi arányát jelzi az átlagos kihasználtság alapján. Például a 0,1 érték megadásával a szegmensek közötti egyensúly megpróbálkozik az összes csomópont elosztásával, hogy az azonos számú szegmenst (± 10%) tartsa.
Pontosabban, a szegmens-újrakiegyensúlyozó megpróbálja átszervezni az összes feldolgozó csomópont kihasználtságát az (1 – küszöbérték) \* átlagos \_ kihasználtságra \. . (1
+ küszöbérték) \* átlagos \_ kihasználtsági tartomány.

szegmensek **maximális \_ \_ áthelyezése:** (nem kötelező) az áthelyezni kívánt szegmensek maximális száma.

**kizárt szegmensek \_ \_ listája:** (nem kötelező) az újraelosztási művelet során nem áthelyezett szegmensek azonosítói.

**horizontális \_ átadási \_ mód:** (nem kötelező) adja meg a replikálási módszert, legyen szó a PostgreSQL logikai replikálásának vagy a több munkavégzős másolási parancsnak a használatáról. Lehetséges értékek:

> -   `auto`: A replika identitásának megkövetelése, ha a logikai replikáció lehetséges, ellenkező esetben használja a régi viselkedést (például a szegmensek javítása érdekében, PostgreSQL 9,6). Ez az alapértelmezett érték.
> -   `force_logical`: Logikai replikáció használata akkor is, ha a tábla nem rendelkezik replika-identitással. A replikáció során a táblázat minden egyidejű frissítési/törlési utasítása sikertelen lesz.
> -   `block_writes`: Használja a másolás (írások blokkolása) műveletet az elsődleges kulcs vagy a replika identitását nélkülöző táblákhoz.

**\_ csak ürítés:** (nem kötelező) Ha igaz, akkor a `shouldhaveshards` [pg_dist_nodeban](reference-hyperscale-metadata.md#worker-node-table)hamis értékre beállított munkavégző csomópontokon helyezze át a szegmenseket; ne helyezzen át más szegmenseket.

**újraelosztási \_ stratégia:** (nem kötelező) a stratégia neve [pg_dist_rebalance_strategyban](reference-hyperscale-metadata.md#rebalancer-strategy-table).
Ha ez az argumentum nincs megadva, a függvény kiválasztja az alapértelmezett stratégiát, ahogy azt a táblázat is jelzi.

#### <a name="return-value"></a>Visszatérési érték

N/A

#### <a name="example"></a>Példa

Az alábbi példa megkísérli a GitHub Events tábla szilánkjának újraelosztását \_ az alapértelmezett küszöbértéken belül.

```postgresql
SELECT rebalance_table_shards('github_events');
```

Ez a példa a GitHub-események táblázatának újraelosztását kísérli meg az \_ 1. és 2. azonosítójú szegmensek áthelyezése nélkül.

```postgresql
SELECT rebalance_table_shards('github_events', excluded_shard_list:='{1,2}');
```

### <a name="get_rebalance_table_shards_plan"></a>a \_ tábla szegmensek újraelosztása \_ \_ \_ tervének beolvasása

Kiadja a [rebalance_table_shards](#rebalance_table_shards) tervezett szegmensének mozgatását anélkül, hogy elvégezze őket.
Habár nem valószínű, hogy a "rebalance Table" (szegmensek \_ újraelosztása \_ \_ \_ ) terve némileg eltérő tervet tud kiadni, mint amit \_ az azonos argumentumokkal rendelkező szegmensek közötti átegyensúlyi \_ hívás eredményez. Ezeket a rendszer nem hajtja végre egyszerre, így a kiszolgáló csoportra vonatkozó tények – \- például a lemezterület \- – eltérőek lehetnek a hívások között.

#### <a name="arguments"></a>Argumentumok

Ugyanazok az argumentumok, mint a táblázatos szegmensek újraelosztása \_ \_ : kapcsolatok, küszöbértékek, a szilánkok maximális száma, a kizárt szegmensek \_ \_ \_ \_ listája és a kiürítés \_ . Lásd a függvény dokumentációját az argumentumok jelentéséhez.

#### <a name="return-value"></a>Visszatérési érték

Az alábbi oszlopokat tartalmazó rekordok:

-   **táblázat \_ neve**: az a tábla, amelynek a szegmensei áthelyezhetők
-   **shardid**: a szóban forgó szilánk
-   **szegmens \_ mérete**: méret bájtban
-   **SourceName**: a forrás csomópont állomásneve
-   **sourceport**: a forrás csomópont portja
-   **targetname**: a célként megadott csomópont állomásneve
-   **célport**: a cél csomópont portja

### <a name="get_rebalance_progress"></a>a \_ folyamat egyensúlyának beolvasása \_

A szegmensek újraegyensúlyának megkezdése után a `get_rebalance_progress()` függvény felsorolja az összes érintett szegmens állapotát. Figyeli a által tervezett és végrehajtott lépéseket `rebalance_table_shards()` .

#### <a name="arguments"></a>Argumentumok

N/A

#### <a name="return-value"></a>Visszatérési érték

Az alábbi oszlopokat tartalmazó rekordok:

-   **munkamenet**-azonosító: a postgres PID azonosítója
-   **Táblanév \_**: az a tábla, amelynek a szegmensei át vannak mozgatva
-   **shardid**: a szóban forgó szilánk
-   **szegmens \_ mérete**: méret bájtban
-   **SourceName**: a forrás csomópont állomásneve
-   **sourceport**: a forrás csomópont portja
-   **targetname**: a célként megadott csomópont állomásneve
-   **célport**: a cél csomópont portja
-   **folyamat**: 0 = áthelyezésre váró várakozás; 1 = áthelyezés; 2 = Befejezés

#### <a name="example"></a>Példa

```sql
SELECT * FROM get_rebalance_progress();
```

```
┌───────────┬────────────┬─────────┬────────────┬───────────────┬────────────┬───────────────┬────────────┬──────────┐
│ sessionid │ table_name │ shardid │ shard_size │  sourcename   │ sourceport │  targetname   │ targetport │ progress │
├───────────┼────────────┼─────────┼────────────┼───────────────┼────────────┼───────────────┼────────────┼──────────┤
│      7083 │ foo        │  102008 │    1204224 │ n1.foobar.com │       5432 │ n4.foobar.com │       5432 │        0 │
│      7083 │ foo        │  102009 │    1802240 │ n1.foobar.com │       5432 │ n4.foobar.com │       5432 │        0 │
│      7083 │ foo        │  102018 │     614400 │ n2.foobar.com │       5432 │ n4.foobar.com │       5432 │        1 │
│      7083 │ foo        │  102019 │       8192 │ n3.foobar.com │       5432 │ n4.foobar.com │       5432 │        2 │
└───────────┴────────────┴─────────┴────────────┴───────────────┴────────────┴───────────────┴────────────┴──────────┘
```

### <a name="citus_add_rebalance_strategy"></a>citus- \_ hozzáadási \_ \_ stratégia

Egy sor hozzáfűzése [pg_dist_rebalance_strategyhoz](reference-hyperscale-metadata.md?#rebalancer-strategy-table) .

#### <a name="arguments"></a>Argumentumok

Az argumentumokkal kapcsolatos további tudnivalókért tekintse meg a megfelelő oszlop értékeit a következőben: `pg_dist_rebalance_strategy` .

**Name:** az új stratégia azonosítója

**szegmens \_ Cost \_ függvény:** az egyes szegmensek díjainak meghatározásához használt függvényt azonosítja. \" \"

**csomópont \_ kapacitása \_ függvény:** a csomópont kapacitásának mérésére szolgáló függvényt azonosítja.

**szegmens \_ engedélyezett \_ a \_ Node \_ függvényben:** azonosítja azt a függvényt, amely meghatározza, hogy mely szegmensek helyezhetők el a csomópontokon.

**alapértelmezett \_ küszöbérték:** egy lebegőpontos küszöbérték, amely azt mutatja, hogy pontosan milyen arányban kell egyensúlyt adni a csomópontok között.

**minimális \_ küszöbérték:** (nem kötelező) a védelmi oszlop, amely a minimális értéket tárolja a táblázatos szegmensek újraelosztása küszöbérték argumentumában \_ \_ (). Az alapértelmezett értéke 0

#### <a name="return-value"></a>Visszatérési érték

N/A

### <a name="citus_set_default_rebalance_strategy"></a>citus \_ \_ alapértelmezett \_ egyensúlyi \_ stratégia beállítása

Frissítse a [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table) táblázatot, és módosítsa az argumentum által megnevezett stratégiát úgy, hogy az alapértelmezettként legyen kiválasztva a szegmensek kiegyensúlyozásakor.

#### <a name="arguments"></a>Argumentumok

**név:** a stratégia neve a PG \_ dist Balance- \_ \_ stratégiában

#### <a name="return-value"></a>Visszatérési érték

N/A

#### <a name="example"></a>Példa

```postgresql
SELECT citus_set_default_rebalance_strategy('by_disk_size');
```

### <a name="citus_remote_connection_stats"></a>citus \_ távoli \_ kapcsolatok \_ statisztikája

A citus \_ Remote \_ Connection \_ stats () függvény az egyes távoli csomópontok aktív kapcsolatainak számát jeleníti meg.

#### <a name="arguments"></a>Argumentumok

N/A

#### <a name="example"></a>Példa

```postgresql
SELECT * from citus_remote_connection_stats();
```

```
    hostname    | port | database_name | connection_count_to_node
----------------+------+---------------+--------------------------
 citus_worker_1 | 5432 | postgres      |                        3
(1 row)
```

### <a name="master_drain_node"></a>Master \_ Drain \_ csomópont

A Master \_ Drain \_ csomópont () függvény áthelyezi a szegmenseket a kijelölt csomópontról, és olyan csomópontokra, amelyeken `shouldhaveshards` igaz értékre van állítva [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table). Hívja meg a függvényt, mielőtt eltávolítja a csomópontot a kiszolgáló csoportjából, és kikapcsolja a csomópont fizikai kiszolgálóját.

#### <a name="arguments"></a>Argumentumok

**csomópontnév:** A kiüríteni kívánt csomópont állomásneve.

**nodeport:** A kiüríteni kívánt csomópont portszáma.

**horizontális \_ átadási \_ mód:** (nem kötelező) adja meg a replikálási módszert, legyen szó a PostgreSQL logikai replikálásának vagy a több munkavégzős másolási parancsnak a használatáról. Lehetséges értékek:

> -   `auto`: A replika identitásának megkövetelése, ha a logikai replikáció lehetséges, ellenkező esetben használja a régi viselkedést (például a szegmensek javítása érdekében, PostgreSQL 9,6). Ez az alapértelmezett érték.
> -   `force_logical`: Logikai replikáció használata akkor is, ha a tábla nem rendelkezik replika-identitással. A replikáció során a táblázat minden egyidejű frissítési/törlési utasítása sikertelen lesz.
> -   `block_writes`: Használja a másolás (írások blokkolása) műveletet az elsődleges kulcs vagy a replika identitását nélkülöző táblákhoz.

**újraelosztási \_ stratégia:** (nem kötelező) a stratégia neve [pg_dist_rebalance_strategyban](reference-hyperscale-metadata.md#rebalancer-strategy-table).
Ha ez az argumentum nincs megadva, a függvény kiválasztja az alapértelmezett stratégiát, ahogy azt a táblázat is jelzi.

#### <a name="return-value"></a>Visszatérési érték

N/A

#### <a name="example"></a>Példa

Az alábbiakban az egyetlen csomópont (például a standard PostgreSQL-port "10.0.0.1") eltávolításának tipikus lépései találhatók:

1.  Ürítse ki a csomópontot.

    ```postgresql
    SELECT * from master_drain_node('10.0.0.1', 5432);
    ```

2.  Várjon, amíg a parancs befejeződik

3.  Csomópont eltávolítása

Több csomópont kiürítése esetén ajánlott inkább [rebalance_table_shards](#rebalance_table_shards) használni. Ez lehetővé teszi, hogy a nagy kapacitású (Citus) előre tervezze meg a szegmenseket, és a minimális számú alkalommal helyezze át a szegmenseket.

1.  Futtassa az összes eltávolítani kívánt csomópontot:

    ```postgresql
    SELECT * FROM master_set_node_property(node_hostname, node_port, 'shouldhaveshards', false);
    ```

2.  Egyszerre [rebalance_table_shards](#rebalance_table_shards)

    ```postgresql
    SELECT * FROM rebalance_table_shards(drain_only := true);
    ```

3.  Várjon, amíg befejeződik a kiürítési egyensúly

4.  Csomópontok eltávolítása

### <a name="replicate_table_shards"></a>táblázatos szegmensek replikálása \_ \_

A replikálási \_ tábla szegmensek \_ () függvénye replikálja az adott tábla alatt replikált szegmenseket. A függvény először kiszámítja a replikált szegmensek és azok helyeinek listáját, amelyekről le lehet kérni a replikálást. A függvény ezután átmásolja ezeket a szegmenseket, és frissíti a megfelelő szegmens metaadatokat a másolásnak megfelelően.

#### <a name="arguments"></a>Argumentumok

**táblázat \_ neve:** annak a táblának a neve, amelynek a szegmenseit replikálni kell.

**szegmens \_ replikációs \_ tényező:** (nem kötelező) az egyes szegmensek számára elérhető kívánt replikációs tényező.

szegmensek **maximális \_ \_ példányszáma:** (nem kötelező) a másolandó szegmensek maximális száma, hogy elérje a kívánt replikációs tényezőt.

**kizárt szegmensek \_ \_ listája:** (nem kötelező) a replikálási művelet során nem másolható szegmensek azonosítói.

#### <a name="return-value"></a>Visszatérési érték

N/A

#### <a name="examples"></a>Példák

Az alábbi példa megkísérli replikálni a GitHub \_ -események táblázatának szegmenseit a szilánkok közötti \_ replikálási \_ faktorba.

```postgresql
SELECT replicate_table_shards('github_events');
```

Ez a példa megkísérli a GitHub \_ -események táblázatának szegmenseit a kívánt replikációs tényezőre, legfeljebb 10 szegmensből álló másolattal. A rebalanceer legfeljebb 10 szegmenst másol a kívánt replikációs tényező elérésére tett kísérlet során.

```postgresql
SELECT replicate_table_shards('github_events', max_shard_copies:=10);
```

### <a name="isolate_tenant_to_new_shard"></a>\_bérlő elkülönítése \_ \_ új \_ szegmensre

Ez a függvény létrehoz egy új szegmenst, amely egy adott értékkel rendelkező sorokat tart a terjesztési oszlopban. Ez különösen hasznos a több-bérlős nagy kapacitású (Citus) esetében, ahol a nagyméretű bérlők önállóan, a saját szegmensén és végső soron a saját fizikai csomópontján helyezhetők el.

#### <a name="arguments"></a>Argumentumok

**táblázat \_ neve:** a tábla neve, amely új szegmenst kap.

**bérlő \_ azonosítója:** a terjesztési oszlop értéke, amely az új szegmenshez lesz rendelve.

**Lépcsőzetes \_ beállítás:** (nem kötelező), \" Ha be van állítva A KASZKÁD \" az aktuális tábla [együttes elhelyezési csoportjának](concepts-hyperscale-colocation.md)összes táblájából is elkülöníti a szegmenst.

#### <a name="return-value"></a>Visszatérési érték

**szegmens \_ azonosítója:** a függvény az újonnan létrehozott szegmenshez rendelt egyedi azonosítót adja vissza.

#### <a name="examples"></a>Példák

Hozzon létre egy új szegmenst, hogy a listaelemek a bérlő 135:

```postgresql
SELECT isolate_tenant_to_new_shard('lineitem', 135);
```

```
┌─────────────────────────────┐
│ isolate_tenant_to_new_shard │
├─────────────────────────────┤
│                      102240 │
└─────────────────────────────┘
```

## <a name="next-steps"></a>Következő lépések

* A cikkben szereplő függvények többsége módosítja a [rendszermetaadatok táblázatait](reference-hyperscale-metadata.md)
* A [kiszolgálói paraméterek](reference-hyperscale-parameters.md) testreszabják egyes függvények viselkedését
