---
title: Kiszolgálói paraméterek – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Paraméterek a nagy kapacitású (Citus) SQL API-ban
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 07f966c7b0be542f848f1a0a4eaf2b5549735b4b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91336241"
---
# <a name="server-parameters"></a>Kiszolgálóparaméterek

A nagy kapacitású (Citus) viselkedését számos kiszolgálói paraméter befolyásolja, mindkettő a standard PostgreSQL-től, illetve a nagy kapacitású (Citus).
Ezek a paraméterek beállíthatók a nagy kapacitású-(Citus-) kiszolgálócsoport Azure Portal. A **Beállítások** kategória alatt válassza a feldolgozó **csomópont paramétereinek** vagy a **koordinátor csomópontjának paramétereit**. Ezek a lapok lehetővé teszik az összes munkavégző csomópont paramétereinek beállítását, vagy csak a koordinátor csomóponthoz.

## <a name="hyperscale-citus-parameters"></a>Nagy kapacitású (Citus) paraméterei

> [!NOTE]
>
> Előfordulhat, hogy a Citus-motor régebbi verzióit futtató nagy kapacitású-(Citus-) kiszolgálócsoportok nem biztosítanak az alább felsorolt paramétereket.

### <a name="general-configuration"></a>Általános konfiguráció

#### <a name="citususe_secondary_nodes-enum"></a>citus. \_ másodlagos \_ csomópontok használata (Enum)

Beállítja a kiválasztott lekérdezésekhez tartozó csomópontok kiválasztásakor használandó házirendet. Ha a "mindig" értékre van állítva, akkor a Planner csak a "másodlagos" noderole jelölt csomópontokat kérdezi le [pg_dist_nodeban](reference-hyperscale-metadata.md#worker-node-table).

A felsorolás támogatott értékei a következők:

-   **soha:** (alapértelmezés) az összes olvasás az elsődleges csomópontokon történik.
-   **mindig:** Az olvasás a másodlagos csomópontokon történik, és az INSERT/Update utasítások le vannak tiltva.

#### <a name="cituscluster_name-text"></a>citus. cluster \_ neve (szöveg)

Tájékoztatja a koordinátori csomópont-Plannert, amely a fürt koordinálja. A fürt \_ nevének beállítása után a Planner önállóan fogja lekérdezni a munkavégző csomópontokat a fürtben.

#### <a name="citusenable_version_checks-boolean"></a>citus. Enable \_ verzió \_ -ellenőrzések (logikai)

A nagy kapacitású (Citus) verziójának frissítéséhez a kiszolgáló újraindítása szükséges (az új megosztott könyvtár felvételéhez), majd az ALTER EXTENSION UPDATE paranccsal.  A két lépés végrehajtásának elmulasztása hibát okozhat vagy összeomlik.
A nagy kapacitású (Citus) így ellenőrzi a kód verzióját és a kiterjesztés egyezését, és ha nem, akkor hibákra \' .

Ez az érték alapértelmezés szerint igaz, és a koordinátoron érvényes. Ritka esetekben előfordulhat, hogy az összetett frissítési folyamatokhoz a paraméter hamis értékre állítása szükséges, ami letiltja az ellenőrzési eljárást.

#### <a name="cituslog_distributed_deadlock_detection-boolean"></a>citus. log \_ elosztott \_ holtpont \_ észlelése (logikai)

Azt határozza meg, hogy naplózza-e az elosztott holtpont észlelésével kapcsolatos feldolgozást a kiszolgálói naplóban. Az alapértelmezett érték a False (hamis).

#### <a name="citusdistributed_deadlock_detection_factor-floating-point"></a>citus. Distributed \_ holtpont \_ észlelési \_ tényező (lebegőpontos)

Az elosztott holtpontok ellenőrzése előtti várakozási idő beállítása. Különösen a várakozási idő lesz az érték, és a PostgreSQL \' s holtpont- [ \_ időtúllépési](https://www.postgresql.org/docs/current/static/runtime-config-locks.html) beállítása szorzata. Az alapértelmezett érték `2`. Egy érték `-1` letiltja az elosztott holtpont észlelését.

#### <a name="citusnode_connection_timeout-integer"></a>citus. Node \_ kapcsolat \_ időkorlátja (egész szám)

A `citus.node_connection_timeout` GUC beállítja a kapcsolatok létesítésére való várakozás maximális időtartamát (ezredmásodpercben). A nagy kapacitású (Citus) hibát jelez, ha az időtúllépés legalább egy feldolgozói kapcsolat létrehozása előtt eltelik. Ez a GUC a koordinátor és a dolgozók közötti kapcsolatokat is befolyásolja.

-   Alapértelmezett: öt másodperc
-   Minimum: 10 ezredmásodperc
-   Maximum: egy óra

```postgresql
-- set to 30 seconds
ALTER DATABASE foo
SET citus.node_connection_timeout = 30000;
```

### <a name="query-statistics"></a>Lekérdezési statisztikák

#### <a name="citusstat_statements_purge_interval-integer"></a>citus. stat \_ utasítások \_ kiürítési \_ időköze (egész szám)

Beállítja, hogy a karbantartási démon milyen gyakorisággal távolítsa el a nem egyező [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table) rekordokat `pg_stat_statements` . Ez a konfigurációs érték a kiürítések közötti időintervallumot állítja be másodpercben, alapértelmezett érték: 10. A 0 érték letiltja a kiürítéseket.

```psql
SET citus.stat_statements_purge_interval TO 5;
```

Ez a paraméter a koordinátoron érvényes, és futásidőben is módosítható.

### <a name="data-loading"></a>Betöltés

#### <a name="citusmulti_shard_commit_protocol-enum"></a>citus. multi-szegmens \_ \_ véglegesítő \_ protokoll (Enum)

Beállítja azt a commit protokollt, amelyet a rendszer akkor használ, amikor kivonatot terjesztenek az elosztott táblázatba. Az egyes szegmensek elhelyezésekor a másolás tranzakciós blokkban történik annak biztosítása érdekében, hogy a másolás során hiba esetén a rendszer ne végezzen adatot. Van azonban egy olyan meghibásodási eset, amelyben a másolás minden helyen sikeres, de a (hardveres) hiba az összes tranzakció véglegesíte előtt történik. Ezzel a paraméterrel megelőzhető az adatvesztés ebben az esetben, ha a következő véglegesítő protokollok közül választ:

-   **2PC:** (alapértelmezett) az a tranzakció, amelyben a rendszer a szegmens elhelyezéseken a másolást végzi, először a PostgreSQL \' s [kétfázisú véglegesítés](http://www.postgresql.org/docs/current/static/sql-prepare-transaction.html) , majd a véglegesítés használatával készül el. A sikertelen véglegesítés manuálisan helyreállítható vagy megszakítható a VÉGLEGESÍTett előkészített vagy VISSZAÁLLÍTÁSi művelettel.
    A 2PC használatakor a [maximálisan \_ előkészített \_ tranzakciókat](http://www.postgresql.org/docs/current/static/runtime-config-resource.html) az összes feldolgozóra vonatkozóan növelni kell, jellemzően a maximális \_ kapcsolatokkal azonos értékre.
-   **1db:** Azokat a tranzakciókat, amelyekben a rendszer a szegmensek szerinti elhelyezéseken végzi a MÁSOLÁSt, egyetlen fordulóban véglegesíti. Előfordulhat, hogy az adatvesztés meghiúsul, ha a másolás minden Elhelyezéskor sikeres volt (ritka).

#### <a name="citusshard_replication_factor-integer"></a>citus. szilánk \_ replikációs \_ tényező (egész szám)

Beállítja a replikálási tényezőt a szegmensek esetében, azon csomópontok számát, amelyeken a szegmensek lesznek elhelyezve, és az alapértelmezett érték az 1. Ez a paraméter futásidőben állítható be, és a koordinátorra is érvényes. A paraméter ideális értéke a fürt méretétől és a csomópont meghibásodási sebességtől függ.  Ha például nagyméretű fürtöket futtat, és gyakrabban szeretné megfigyelni a csomópontok hibáit, érdemes lehet ezt a replikációs tényezőt megnövelni.

#### <a name="citusshard_count-integer"></a>citus. szilánkok \_ száma (egész szám)

Beállítja a kivonatoló particionálású táblák és az alapértelmezett értékek 32-re vonatkozó szegmensének darabszámát.  Ezt az értéket a [create_distributed_table](reference-hyperscale-functions.md#create_distributed_table) UDF használja a kivonatoló particionálású táblák létrehozásakor. Ez a paraméter futásidőben állítható be, és a koordinátorra is érvényes.

#### <a name="citusshard_max_size-integer"></a>citus. szilánk \_ maximális \_ mérete (egész szám)

Megadja azt a maximális méretet, ameddig a szegmens növekedni fog, és az alapértelmezett érték 1 GB lesz. Ha az egyik szegmensnél a forrásfájl \' s mérete (amely átmeneti tároláshoz használatos) meghaladja ezt a konfigurációs értéket, az adatbázis biztosítja, hogy az új szegmens létre lesz hozva. Ez a paraméter futásidőben állítható be, és a koordinátorra is érvényes.

### <a name="planner-configuration"></a>Planner konfigurálása

#### <a name="cituslimit_clause_row_fetch_count-integer"></a>citus. limit \_ záradék \_ \_ -sorok beolvasásának \_ száma (egész szám)

Beállítja a lekérdezni kívánt sorok számát a limit záradék optimalizálásához.
Bizonyos esetekben a limit záradékokkal rendelkező lekérdezések esetében előfordulhat, hogy az egyes feladatokból származó összes sort be kell olvasnia az eredmények létrehozásához. Ezekben az esetekben, és ha egy közelítés értelmes eredményeket eredményezne, ez a konfigurációs érték beállítja az egyes szegmensek lekéréséhez szükséges sorok számát. A határérték-közelítések alapértelmezés szerint le vannak tiltva, és ez a paraméter a-1 értékre van beállítva. Ez az érték futásidőben állítható be, és a koordinátorra is érvényes.

#### <a name="cituscount_distinct_error_rate-floating-point"></a>citus. Count \_ \_ – eltérő hibák száma \_ (lebegőpontos)

A nagy kapacitású (Citus) a PostgreSQL-HLL kiterjesztés használatával kiszámíthatja a darabszámot (eltérő). Ez a konfigurációs bejegyzés beállítja a várt hibák arányát a darabszám kiszámításakor (eltérő). 0,0, amely az alapértelmezett, letiltja a darabszám (eltérő) közelítését. és a 1,0 nem biztosít garanciát az eredmények pontosságára vonatkozóan. Javasoljuk, hogy ezt a paramétert 0,005 értékre állítsa a legjobb eredmények érdekében. Ez az érték futásidőben állítható be, és a koordinátorra is érvényes.

#### <a name="citustask_assignment_policy-enum"></a>citus. feladat \_ -hozzárendelési \_ házirend (Enum)

> [!NOTE]
> Ez a GUC csak akkor alkalmazható, ha a [shard_replication_factor](reference-hyperscale-parameters.md#citusshard_replication_factor-integer) nagyobb, mint egy, vagy [reference_tables](concepts-hyperscale-distributed-data.md#type-2-reference-tables)a lekérdezésekre.

A feladatok feladatainak a feladatokhoz való hozzárendeléséhez használandó házirend beállítása. A koordinátor a feladatokat a feladatokhoz a szegmens helyei alapján rendeli hozzá. Ez a konfigurációs érték határozza meg a hozzárendelések végrehajtásakor használandó házirendet.
Jelenleg három lehetséges feladat-hozzárendelési házirend használható.

-   **kapzsi:** A kapzsi házirend az alapértelmezett, és célja, hogy egyenletesen ossza el a feladatokat a feldolgozók között.
-   **ciklikus multiplexelés:** A ciklikus időszeleteléses szabályzat a feladatokat a feldolgozók számára a különböző replikák közötti, ciklikusan megjelenő módon rendeli hozzá. Ez a szabályzat lehetővé teszi a fürt jobb kihasználtságát, ha a tábla szegmensek száma nem elég alacsony a feldolgozók számához képest.
-   **első replika:** Az első replika házirend a szegmensekhez tartozó elhelyezési sorrend (replika) alapján rendel el feladatokat. Más szóval a szegmenshez tartozó töredék-lekérdezés a szegmens első replikáját tartalmazó feldolgozóhoz van rendelve.
    Ez a módszer lehetővé teszi, hogy erős garanciát biztosítson arra vonatkozóan, hogy mely szegmenseket fogja használni a csomópontokon (azaz erősebb memória-rezidensi garancia).

Ez a paraméter futásidőben állítható be, és a koordinátorra is érvényes.

### <a name="intermediate-data-transfer"></a>Köztes Adatátvitel

#### <a name="citusbinary_worker_copy_format-boolean"></a>citus. Binary \_ Worker \_ másolási \_ formátuma (logikai)

A bináris másolási formátum használatával továbbíthatja a köztes adatok feldolgozók közötti átvitelét.
A nagyméretű táblákhoz való csatlakozás során előfordulhat, hogy a nagy kapacitású (Citus) dinamikusan újra kell particionálni és meg kell keverni az adatmennyiséget a különböző munkatársak között Alapértelmezés szerint az adatátvitelt szöveges formátumban kell megadni. A paraméter engedélyezése arra utasítja az adatbázist, hogy a PostgreSQL bináris szerializálási formátumát használja az adatok átviteléhez. Ez a paraméter a feldolgozókon érvényes, és a PostgreSQL. conf fájlban kell módosítani. A konfigurációs fájl szerkesztése után a felhasználók SIGHUP-jelet küldhetnek, vagy újraindíthatják a kiszolgálót a módosítás érvénybe léptetéséhez.

#### <a name="citusbinary_master_copy_format-boolean"></a>citus. bináris \_ fő \_ másolási \_ formátum (logikai)

A bináris másolási formátum használatával továbbíthatja az adatok koordinátor és a munkavégzők között. Az elosztott lekérdezések futtatásakor a dolgozók az időközi eredményeket a végső összesítéshez továbbítják a koordinátornak. Alapértelmezés szerint az adatátvitelt szöveges formátumban kell megadni. A paraméter engedélyezése arra utasítja az adatbázist, hogy a PostgreSQL bináris szerializálási formátumát használja az adatok átviteléhez.
Ez a paraméter beállítható futásidőben, és a koordinátoron is érvényes.

#### <a name="citusmax_intermediate_result_size-integer"></a>citus. max \_ közbenső \_ eredmény \_ mérete (egész szám)

A közös táblakifejezés közbenső eredményeinek maximális mérete (KB), amelyet nem lehet leküldeni a feldolgozó csomópontokra a végrehajtáshoz, valamint az összetett allekérdezésekhez. Az alapértelmezett érték 1 GB, a-1 érték pedig nem korlátot jelent.
A korlátot meghaladó lekérdezések meg lesznek szakítva, és hibaüzenetet hoznak létre.

### <a name="ddl"></a>DDL

#### <a name="citusenable_ddl_propagation-boolean"></a>citus. Enable \_ DDL \_ -propagálás (logikai)

Meghatározza, hogy a rendszer automatikusan propagálja-e a DDL-módosításokat a koordinátortól az összes feldolgozóig. Az alapértelmezett érték az igaz. Mivel egyes sémák módosításainak kizárólagos hozzáférésre van szükségük a táblákhoz, és mivel az automatikus propagálás az összes feldolgozóra vonatkozik, a nagy kapacitású (Citus) fürt átmenetileg kevésbé rugalmasan reagál. Dönthet úgy, hogy letiltja ezt a beállítást, és manuálisan propagálja a módosításokat.

### <a name="executor-configuration"></a>Végrehajtó konfigurációja

#### <a name="general"></a>Általános kérdések

##### <a name="citusall_modifications_commutative"></a>citus. minden \_ módosítás \_ kommutatív

A nagy kapacitású (Citus) kikényszeríti a commutativity-szabályokat, és a megfelelő zárolásokat szerzi be a módosítási műveletekhez a viselkedés javítása érdekében. Tegyük fel például, hogy egy INSERT utasítás egy másik INSERT utasítással, de UPDATE vagy DELETE utasítással nem. Hasonlóképpen azt is feltételezi, hogy egy frissítési vagy TÖRLÉSi utasítás nem egy másik UPDATE vagy DELETE utasítással van felcserélve. Ez az elővigyázatosság azt jelenti, hogy a frissítésekhez és törlésekhez nagy kapacitású (Citus) szükséges az erősebb zárolások beolvasásához.

Ha olyan frissítési utasításokkal rendelkezik, amelyek a lapkákkal vagy más frissítésekkel kommutatív, akkor a paraméter igaz értékre állításával kihasználhatja ezeket a commutativity feltételezéseket. Ha a paraméter értéke TRUE (igaz), az összes parancs kommutatív minősül, és megosztott zárolást igényel, amely javíthatja a teljes átviteli sebességet. Ez a paraméter beállítható futásidőben, és a koordinátoron is érvényes.

##### <a name="citusremote_task_check_interval-integer"></a>citus. távoli \_ feladatok \_ ellenőrzési \_ időköze (egész szám)

Meghatározza, hogy a nagy kapacitású (Citus) milyen gyakorisággal ellenőrizze a feladat-követési végrehajtó által kezelt feladatok állapotát. Alapértelmezés szerint 10 ms. A koordinátor feladatokat rendel a feldolgozókhoz, majd rendszeresen ellenőrzi azokat az egyes feladatok \' előrehaladásával kapcsolatban. Ez a konfigurációs érték állítja be két következő ellenőrzés közötti időtartamot. Ez a paraméter a koordinátoron érvényes, és beállítható futásidőben.

##### <a name="citustask_executor_type-enum"></a>citus. Task \_ végrehajtó \_ típusa (Enum)

A nagy kapacitású (Citus) három végrehajtói típussal rendelkezik az elosztott VÁLASZTÓ lekérdezések futtatásához.  A kívánt végrehajtót a konfigurációs paraméter beállításával lehet kiválasztani. A paraméter elfogadott értékei a következők:

-   **adaptív:** Az alapértelmezett érték. A több szegmens között átfedésben lévő összesítéseket és közös elhelyezésű illesztéseket tartalmazó lekérdezések gyors megválaszolásához optimális megoldás.
-   **feladat-követés:** A Feladatkezelő végrehajtója jól használható a hosszú ideig futó, összetett lekérdezésekhez, amelyek az adatok keverését igénylik a munkavégző csomópontokon és a hatékony erőforrás-kezelésben.
-   **valós idejű:** (elavult) az adaptív végrehajtóhoz hasonló célt szolgál, de kevésbé rugalmas, és a munkavégző csomópontok számára nagyobb hálózati nyomást eredményezhet.

Ez a paraméter futásidőben állítható be, és a koordinátorra is érvényes.

##### <a name="citusmulti_task_query_log_level-enum-multi_task_logging"></a>citus. \_ többfeladatos \_ lekérdezés \_ naplózási \_ szintje (Enum) {#multi_task_logging}

Minden olyan lekérdezés naplózási szintjét állítja be, amely egynél több feladatot hoz létre (azaz több szegmenst üt). A naplózás a több-bérlős alkalmazások áttelepítése során hasznos, mivel választhatja a hibákat, vagy figyelmeztetheti ezeket a lekérdezéseket, így megkeresheti őket, és felvehet egy bérlői \_ azonosító szűrőt. Ez a paraméter beállítható futásidőben, és a koordinátoron is érvényes. A paraméter alapértelmezett értéke \' Off \' .

A felsorolás támogatott értékei a következők:

-   **kikapcsolva:** A több feladatot létrehozó lekérdezések naplózásának kikapcsolása (azaz több szegmensre is kiterjed)
-   **hibakeresés:** A naplók a hibakeresés súlyossági szintjének kimutatására szolgálnak.
-   **napló:** Naplózza az utasítást a napló súlyossági szintjén. A naplófájl tartalmazni fogja a futtatott SQL-lekérdezést.
-   **Figyelmeztetés:** A naplófájlok utasítás súlyossági szintje.
-   **Figyelmeztetés:** A naplók utasítás a figyelmeztetés súlyossági szintjén.
-   **hiba:** A naplók utasítása a hiba súlyossági szintjén.

Hasznos lehet a `error` fejlesztési tesztelés során használni, és alacsonyabb a naplózási szint, mint a `log` tényleges éles üzembe helyezés során.
Ha a lehetőséget választja, `log` akkor a több feladatot tartalmazó lekérdezések megjelennek az adatbázis naplófájljaiban az utasítás után megjelenő lekérdezéssel \" .\"

```
LOG:  multi-task query about to be executed
HINT:  Queries are split to multiple tasks if they have to be split into several queries on the workers.
STATEMENT:  select * from foo;
```

##### <a name="citusenable_repartition_joins-boolean"></a>citus. az \_ újraparticionálási \_ illesztések engedélyezése (logikai)

Általában az újraparticionálási illesztések az adaptív végrehajtóval való elvégzésére tett kísérlet során hibaüzenet jelenik meg.  A True érték megadása azonban lehetővé teszi, hogy a `citus.enable_repartition_joins` nagy kapacitású (Citus) átmenetileg átváltson a Task-Tracker-végrehajtóra az illesztés végrehajtásához.  Az alapértelmezett érték a hamis.

#### <a name="task-tracker-executor-configuration"></a>Feladat-követési végrehajtó konfigurációja

##### <a name="citustask_tracker_delay-integer"></a>citus. a feladat \_ nyomon követésének \_ késleltetése (egész szám)

Ezzel a paraméterrel állítható be a Feladatkezelő alvó időpontja a feladatok kezelése és az alapértelmezett érték között a 200 MS. A feladat-követési folyamat rendszeresen felébred, az összes hozzá rendelt feladatra, valamint a feladatok ütemezett és végrehajtásához.  Ezután a feladat követése egy adott időszakra alvó állapotba lép, mielőtt újra elkezdené ezeket a feladatokat.
Ez a konfigurációs érték határozza meg az adott alvó időszak hosszát. Ez a paraméter a feldolgozókon érvényes, és a PostgreSQL. conf fájlban kell módosítani. A konfigurációs fájl szerkesztése után a felhasználók elküldhetnek egy SIGHUP-jelet, vagy újraindíthatják a kiszolgálót a módosítás érvénybe léptetéséhez.

Ez a paraméter csökkenthető a feladat-követési végrehajtó által okozott késleltetés levágásával a felügyeleti körök közötti időbeli eltérés csökkentése érdekében.
A késleltetés csökkentése olyan esetekben hasznos, amikor a szegmensben lekérdezett lekérdezések rövidek, és így rendszeresen frissítik az állapotukat.

##### <a name="citusmax_assign_task_batch_size-integer"></a>citus. max \_ hozzárendelési \_ feladat \_ kötegének \_ mérete (egész szám)

A koordinátor végrehajtója szinkron módon, a feldolgozókon lévő démonhoz rendel feladatokat a kötegekben. Ezzel a paraméterrel állítható be az egyetlen kötegben hozzárendelhető feladatok maximális száma. Ha nagyobb méretű köteget választ, a gyorsabb feladat-hozzárendelést is lehetővé teszi. Ha azonban a feldolgozók száma nagy, akkor hosszabb időt vehet igénybe az összes feldolgozó számára a feladatok beszerzése.  Ez a paraméter beállítható futásidőben, és a koordinátoron is érvényes.

##### <a name="citusmax_running_tasks_per_node-integer"></a>citus. max \_ futó \_ feladatok \_ / \_ csomópont (egész szám)

A feladat-követési folyamat az adott esetben hozzárendelt feladatokat ütemezze és hajtja végre. Ez a konfigurációs érték azt állítja be, hogy a tevékenységek legfeljebb hány feladatot hajtanak végre egyszerre egy csomóponton egy adott időpontban, és az alapértelmezett érték 8.

A korlát biztosítja, hogy ne \' legyen sok olyan feladat, amely egyszerre több lemezt is tartalmaz, és segít elkerülni a lemez I/O-tartalmát. Ha a lekérdezések a memóriából vagy az SSD-lemezekről vannak kiszolgálva, a \_ csomópontok maximális futási \_ feladatait a művelet \_ \_ nem érinti.

##### <a name="cituspartition_buffer_size-integer"></a>citus. partition \_ puffer \_ mérete (egész szám)

Beállítja a partíciós műveletekhez és az alapértelmezett értékekhez használt puffer méretét 8 MB-ra.
A nagy kapacitású (Citus) lehetővé teszi, hogy a tábla-adatmennyiség több fájlba legyen particionálva, ha két nagyméretű tábla van csatlakoztatva. A partíciós puffer betöltését követően az újraparticionált adatfájlok a lemezen lévő fájlokba kerülnek.  Ez a konfigurációs bejegyzés futásidőben állítható be, és hatékony a feldolgozók számára.

#### <a name="explain-output"></a>Kimenet magyarázata

##### <a name="citusexplain_all_tasks-boolean"></a>citus. \_ az összes \_ feladat magyarázata (logikai)

Alapértelmezés szerint a nagy kapacitású (Citus) egyetlen, tetszőleges feladat kimenetét jeleníti meg, amikor egy elosztott lekérdezésen futtat [magyarázatot](http://www.postgresql.org/docs/current/static/sql-explain.html) . A legtöbb esetben a magyarázat kimenete a feladatok között hasonló lesz. Alkalmanként a tevékenységek némelyike másképp lesz megtervezve, vagy sokkal magasabb végrehajtási idővel rendelkezik. Ezekben az esetekben hasznos lehet ezt a paramétert engedélyezni, amely után a magyarázat kimenete tartalmazza az összes feladatot. Az összes feladat magyarázata miatt a magyarázat hosszabb időt is igénybe vehet.

## <a name="postgresql-parameters"></a>PostgreSQL-paraméterek

* [DateStyle](https://www.postgresql.org/docs/current/datatype-datetime.html#DATATYPE-DATETIME-OUTPUT) – beállítja a dátum-és időértékek megjelenítési formátumát.
* [IntervalStyle](https://www.postgresql.org/docs/current/datatype-datetime.html#DATATYPE-INTERVAL-OUTPUT) – beállítja az intervallumok megjelenítési formátumát
* [Időzóna – beállítja az időzónát](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-TIMEZONE) az időbélyegző megjelenítéséhez és értelmezéséhez.
* [Application_name](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-APPLICATION-NAME) – beállítja a statisztikában és a naplókban jelentendő alkalmazás nevét
* [array_nulls](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-ARRAY-NULLS) – engedélyezi null elemek bevitelét a tömbökben
* [autovacuum](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM) – elindítja az autovákuum alfolyamatot.
* [autovacuum_analyze_scale_factor](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-ANALYZE-SCALE-FACTOR) – a reltuples töredékének elemzése előtt a rekordokban szereplő lapkák, frissítések és törlések száma
* [autovacuum_analyze_threshold](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-ANALYZE-THRESHOLD) – az elemzés előtt a rekordos beszúrások, frissítések és törlések minimális száma
* [autovacuum_naptime](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-NAPTIME) – az autovákuum-futtatások közötti idő
* [autovacuum_vacuum_cost_delay](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-COST-DELAY) – vákuumos késleltetés ezredmásodpercben, autoporszívó esetén
* [autovacuum_vacuum_cost_limit](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-COST-LIMIT) – az autovákuumhoz a pelenka előtt rendelkezésre álló vákuum-érték
* [autovacuum_vacuum_scale_factor](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-SCALE-FACTOR) – a reltuples töredékét megelőzően a vákuum előtti rekordos frissítések száma vagy törlése
* [autovacuum_vacuum_threshold](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-THRESHOLD) – a rekordhoz tartozó frissítések vagy törlések minimális száma vákuum előtt
* [autovacuum_work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-AUTOVACUUM-WORK-MEM) – beállítja az egyes autovákuumos munkavégző folyamatok által használt maximális memóriát
* [backend_flush_after](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BACKEND-FLUSH-AFTER) – azon lapok száma, amely után a rendszer a korábban végrehajtott írásokat lemezre üríti
* [backslash_quote](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-BACKSLASH-QUOTE) – beállítja, hogy \' a "" karakterlánc literálokban engedélyezett-e
* [bgwriter_delay](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-DELAY) – a háttérben író alvó ideje a körök között
* [bgwriter_flush_after](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-FLUSH-AFTER) – azon lapok száma, amely után a rendszer a korábban végrehajtott írásokat lemezre üríti
* [bgwriter_lru_maxpages](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-LRU-MAXPAGES) – a háttér-író LRU-lapok maximális száma kerekítve
* [bgwriter_lru_multiplier](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-LRU-MULTIPLIER) – az átlagos puffer-használat többszöröse, kör nélkül
* [bytea_output](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-BYTEA-OUTPUT) – a bájtok kimeneti formátumának beállítása
* [check_function_bodies](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CHECK-FUNCTION-BODIES) – ellenőrzi a függvények törzseit a Create függvény során
* [checkpoint_completion_target](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-COMPLETION-TARGET) a piszkos pufferek kiürítését az ellenőrzőpont során, az ellenőrzőpontok intervallumának részeként
* [checkpoint_timeout](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-TIMEOUT) – beállítja az automatikus Wal-ellenőrzőpontok közötti maximális időt
* [checkpoint_warning](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-WARNING) – riasztások engedélyezése, ha az ellenőrzőpont-szegmensek gyakrabban vannak kitöltve, mint ez
* [client_encoding](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CLIENT-ENCODING) – az ügyfél karakterkészletének kódolását állítja be
* [client_min_messages](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CLIENT-MIN-MESSAGES) – az ügyfélnek küldött üzenetek szintjének beállítása
* [commit_delay](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-COMMIT-DELAY) – beállítja a késleltetést a tranzakciós véglegesítés és a Wal-lemez kiürítése között
* [commit_siblings](https://www.postgresql.org/docs/12/runtime-config-wal.html#GUC-COMMIT-SIBLINGS) – az egyidejű nyitott tranzakciók beállítása a végrehajtás előtt commit_delay
* [constraint_exclusion](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CONSTRAINT-EXCLUSION) – lehetővé teszi, hogy a Planner megkötéseket használjon a lekérdezések optimalizálásához
* [cpu_index_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-INDEX-TUPLE-COST) – a Planner becslését állítja be az egyes indexek feldolgozásának költsége az index vizsgálata során
* [cpu_operator_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-OPERATOR-COST) – beállítja a Planner által az egyes operátorok vagy függvények hívásának feladatának költségeit.
* [cpu_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-TUPLE-COST) – a Planner becslését állítja be az egyes rekordhalmazok feldolgozási költsége (sor) alapján
* [cursor_tuple_fraction](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CURSOR-TUPLE-FRACTION) – beállítja a Planner azon sorainak a számát, amelyeket a rendszer lekérdez
* [deadlock_timeout](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-DEADLOCK-TIMEOUT) – Beállítja azt az időtartamot ezredmásodpercben, ameddig egy zárolásra várni kell a holtpontra való ellenőrzés előtt
* [debug_pretty_print](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.3.1.3) – az elemzési és tervezési faszerkezetek megjelenítésének behúzása
* [debug_print_parse](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) – az egyes lekérdezések elemzési fájának naplózása
* [debug_print_plan](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) – minden lekérdezés végrehajtási tervének naplózása
* [debug_print_rewritten](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) – az egyes lekérdezések újraírható elemzési fájának naplózása
* [default_statistics_target](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-DEFAULT-STATISTICS-TARGET) – beállítja az alapértelmezett statisztikai célt
* [default_tablespace](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TABLESPACE) – beállítja az alapértelmezett tablespace-t táblák és indexek létrehozásához a következőben:
* [default_text_search_config](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TEXT-SEARCH-CONFIG) – alapértelmezett szöveges keresési konfiguráció beállítása
* [default_transaction_deferrable](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-DEFERRABLE) – az új tranzakciók alapértelmezett késleltetett állapotának beállítása
* [default_transaction_isolation](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-ISOLATION) – az egyes új tranzakciók tranzakció-elkülönítési szintjének beállítása
* [default_transaction_read_only](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-READ-ONLY) – az új tranzakciók alapértelmezett írásvédett állapotának beállítása
* default_with_oids – alapértelmezés szerint új táblákat hoz létre OID használatával
* [effective_cache_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-EFFECTIVE-CACHE-SIZE) – a tervező feltételezésének beállítása a lemezes gyorsítótár méretével kapcsolatban
* [enable_bitmapscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-BITMAPSCAN) – lehetővé teszi a Planner számára a bitképes vizsgálati csomagok használatát
* [enable_gathermerge](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-GATHERMERGE) – lehetővé teszi, hogy a Planner egyesítési terveket gyűjtsön
* [enable_hashagg](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-HASHAGG) – lehetővé teszi, hogy a Planner kihasználja a kivonatoló összesítési terveket
* [enable_hashjoin](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-HASHJOIN) – lehetővé teszi a Planner a kivonatoló csatlakoztatási csomagok használatát
* [enable_indexonlyscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-INDEXONLYSCAN) – lehetővé teszi a Planner csak index-ellenőrzési csomagok használatát
* [enable_indexscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-INDEXSCAN) – lehetővé teszi a Planner index-Scan csomagok használatának használatát
* [enable_material](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-MATERIAL) – engedélyezi a Planner megvalósításának gyakorlatban használatát
* [enable_mergejoin](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-MERGEJOIN) – engedélyezi a Planner egyesítési csomagjainak használatát.
* [enable_nestloop](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-NESTLOOP) – engedélyezi a Planner beágyazott hurok-csatlakozási csomagjainak használatát.
* [enable_seqscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-SEQSCAN) – lehetővé teszi a Planner szekvenciális vizsgálati csomagok használatát
* [enable_sort](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-SORT) – lehetővé teszi a Planner explicit rendezési lépéseinek használatát
* [enable_tidscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-TIDSCAN) – engedélyezi a Planner a TID-ellenőrzési csomagok használatát
* [escape_string_warning](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-ESCAPE-STRING-WARNING) – figyelmezteti a fordított perjeleket a normál karakterláncos literálok esetén
* [exit_on_error](https://www.postgresql.org/docs/current/runtime-config-error-handling.html#GUC-EXIT-ON-ERROR) – a munkamenet megszakítása bármilyen hiba esetén
* [extra_float_digits](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-EXTRA-FLOAT-DIGITS) – beállítja a lebegőpontos értékekhez megjelenített számjegyek számát.
* [force_parallel_mode](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-FORCE-PARALLEL-MODE) – a párhuzamos lekérdezési létesítmények használatának kényszerítése
* [from_collapse_limit](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-FROM-COLLAPSE-LIMIT) – a lista azon méretét állítja be, amely meghaladja az allekérdezések összecsukása nélkül
* [geqo](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO) – engedélyezi a genetikai lekérdezés optimalizálását
* [geqo_effort](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-EFFORT) -geqo: a rendszer az egyéb geqo paraméterek alapértelmezett értékének beállítására használja.
* [geqo_generations](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-GENERATIONS) -geqo: az algoritmus ismétlési száma
* [geqo_pool_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-POOL-SIZE) -geqo: a populációban lévő személyek száma
* [geqo_seed](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-SEED) -geqo: a véletlenszerű útvonal kiválasztásának magja
* [geqo_selection_bias](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-SELECTION-BIAS) -geqo: szelektív nyomás a populáción belül
* [geqo_threshold](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-THRESHOLD) – a küszöbértéket határozza meg a geqo által használt ELEMEKtől számítva
* [gin_fuzzy_search_limit](https://www.postgresql.org/docs/current/runtime-config-client.html#id-1.6.6.14.5.2.2.1.3) – beállítja a teljes megengedett eredményt a gin pontos kereséséhez
* [gin_pending_list_limit](https://www.postgresql.org/docs/current/runtime-config-client.html#id-1.6.6.14.2.2.23.1.3) – beállítja a függőben lévő lista maximális méretét a gin indexhez
* [idle_in_transaction_session_timeout](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-IDLE-IN-TRANSACTION-SESSION-TIMEOUT) – bármely üresjárati tranzakció maximális engedélyezett időtartamának beállítása
* [join_collapse_limit](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-JOIN-COLLAPSE-LIMIT) – a lista azon méretének megadása, amely után a csatlakoztatási szerkezetek nem lapított
* [LC_MONETARY](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LC-MONETARY) – beállítja a pénzügyi összegek formázásának területi beállítását.
* [LC_NUMERIC](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LC-NUMERIC) – beállítja a formázási számok területi beállításait
* [lo_compat_privileges](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-LO-COMPAT-PRIVILEGES) – lehetővé teszi a visszamenőleges kompatibilitási mód használatát a nagy objektumok jogosultsági ellenőrzéséhez
* [lock_timeout](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LOCK-TIMEOUT) – beállítja a zárolási várakozások maximálisan megengedett időtartamát (ezredmásodpercben). 0 kikapcsolja ezt
* [log_autovacuum_min_duration](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#) – beállítja a minimális végrehajtási időt, amely felett az autovákuum műveletei naplózva lesznek
* [log_checkpoints](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-CHECKPOINTS) – az egyes ellenőrzőpontok naplózása
* [log_connections](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-CONNECTIONS) – minden sikeres kapcsolatok naplózása
* [log_destination](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DESTINATION) – beállítja a kiszolgálói napló kimenetének célhelyét
* [log_disconnections](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DISCONNECTIONS) – a munkamenet vége, beleértve az időtartamot is
* [log_duration](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DURATION) – az egyes befejezett SQL-utasítások időtartamának naplózása
* [log_error_verbosity](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-ERROR-VERBOSITY) – a naplózott üzenetek részletességének beállítása
* [log_lock_waits](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LOCK-WAITS) naplók – hosszú zárolási várakozások
* [log_min_duration_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-DURATION-STATEMENT) – beállítja a minimális végrehajtási időt (ezredmásodpercben), amely felett az utasítások naplózása megtörténik. -1 a naplózási utasítások időtartamának letiltása
* [log_min_error_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-ERROR-STATEMENT) – azt eredményezi, hogy az összes utasítás az ezen a szinten vagy fölött hibát generál a naplóba
* [log_min_messages](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-MESSAGES) – a naplózott üzenetek szintjének beállítása
* [log_replication_commands](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-REPLICATION-COMMANDS) – minden replikációs parancs naplózása
* [log_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-STATEMENT) – beállítja a naplózott utasítások típusát
* [log_statement_stats](https://www.postgresql.org/docs/current/runtime-config-statistics.html#id-1.6.6.12.3.2.1.1.3) – minden lekérdezés esetében az összesített teljesítmény statisztikáit írja a kiszolgálói naplóba
* [log_temp_files](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-TEMP-FILES) – az adott számú kilobájtnál nagyobb méretű ideiglenes fájlok használatát naplózza
* [maintenance_work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM) – beállítja a karbantartási műveletekhez használandó maximális memóriát
* [max_parallel_workers](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PARALLEL-WORKERS) – beállítja a párhuzamos feldolgozók maximális számát, mint amennyit egyszerre lehet aktív
* [max_parallel_workers_per_gather](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PARALLEL-WORKERS-PER-GATHER) – a párhuzamos folyamatok maximális számát állítja be egy végrehajtó csomóponton.
* [max_pred_locks_per_page](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-MAX-PRED-LOCKS-PER-PAGE) – a predikátumok által zárolt rekordok maximális számának beállítása oldalanként
* [max_pred_locks_per_relation](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-MAX-PRED-LOCKS-PER-RELATION) – a predikátumok által zárolt lapok és rekordok maximális számának beállítása kapcsolatonként
* [max_standby_archive_delay](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-STANDBY-ARCHIVE-DELAY) – beállítja a maximális késleltetést a lekérdezések megszakítása előtt, amikor egy gyors készenléti kiszolgáló az ARCHIVÁLt Wal-adatok feldolgozását dolgozza fel
* [max_standby_streaming_delay](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-STANDBY-STREAMING-DELAY) – beállítja a maximális késleltetést a lekérdezések megszakítása előtt, amikor egy gyors készenléti kiszolgáló adatfolyamként dolgozza fel a Wal-adatok átvitelét
* [max_sync_workers_per_subscription](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-SYNC-WORKERS-PER-SUBSCRIPTION) – előfizetéshez tartozó tábla-szinkronizálási feldolgozók maximális száma
* [max_wal_size](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-MAX-WAL-SIZE) – az ellenőrzőpontot indító Wal-méretet állítja be
* [min_parallel_index_scan_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-MIN-PARALLEL-INDEX-SCAN-SIZE) – egy párhuzamos vizsgálathoz tartozó indexelő adat minimális mennyiségét állítja be
* [min_wal_size](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-MIN-WAL-SIZE) – beállítja a minimális méretet, hogy a Wal-t
* [operator_precedence_warning](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-OPERATOR-PRECEDENCE-WARNING) – figyelmeztetést bocsát ki a PostgreSQL 9,4 óta a jelentés változásáról
* [parallel_setup_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-PARALLEL-SETUP-COST) – a Planner becslését állítja be a párhuzamos lekérdezés munkavégző folyamatainak megkezdési költsége alapján.
* [parallel_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-PARALLEL-TUPLE-COST) – a Planner becsült értékét adja meg a feldolgozótól a fő háttérig terjedő rekordoknak (soroknak)
* [pg_stat_statements. Save](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) – elmenti az pg_stat_statements statisztikát a kiszolgálói leállítások között
* [pg_stat_statements. Track](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) – kiválasztja, hogy mely utasításokat követik nyomon pg_stat_statements
* [pg_stat_statements. track_utility](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) – kiválasztja, hogy a pg_stat_statements nyomon követik-e a segédprogram parancsait
* [quote_all_identifiers](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-QUOTE-ALL-IDENTIFIERS) – SQL-töredékek generálásakor az idézőjelek minden azonosítót
* [random_page_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-RANDOM-PAGE-COST) – a Planner becslését egy nem szekvenciálisan beolvasott lemez költsége alapján állítja be
* [row_security](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-ROW-SECURITY) – engedélyezi a sor biztonságát
* [search_path](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SEARCH-PATH) – a séma keresési sorrendjét állítja be olyan neveknél, amelyek nem sémának minősítettek
* [seq_page_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-SEQ-PAGE-COST) – a Planner becslését egy szekvenciálisan beolvasott lemez díja alapján állítja be
* [session_replication_role](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SESSION-REPLICATION-ROLE) – beállítja a munkamenet viselkedését az eseményindítók és az Újraírási szabályok számára
* [standard_conforming_strings](https://www.postgresql.org/docs/current/runtime-config-compatible.html#id-1.6.6.16.2.2.7.1.3) – "..." okai karakterláncok a fordított perjelek kezelésére
* [statement_timeout](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-STATEMENT-TIMEOUT) – egy utasítás maximálisan engedélyezett időtartamát határozza meg (ezredmásodpercben). 0 kikapcsolja ezt
* [synchronize_seqscans](https://www.postgresql.org/docs/current/runtime-config-compatible.html#id-1.6.6.16.2.2.8.1.3) – engedélyezi a szinkronizált szekvenciális vizsgálatokat
* [synchronous_commit](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-SYNCHRONOUS-COMMIT) – az aktuális tranzakció szinkronizálási szintjének beállítása
* [tcp_keepalives_count](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-COUNT) – a TCP életben tartási újraküldések maximális száma
* [tcp_keepalives_idle](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-IDLE) -idő a TCP-Keepalives kibocsátása között
* [tcp_keepalives_interval](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-INTERVAL) a TCP életben tartási újraküldések közötti idő
* [temp_buffers](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-TEMP-BUFFERS) – beállítja az egyes adatbázis-munkamenetek által használt ideiglenes pufferek maximális számát.
* [temp_tablespaces](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-TEMP-TABLESPACES) – beállítja az ideiglenes táblákhoz használandó tablespace (ka) t, és rendezi a fájlokat
* [track_activities](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-ACTIVITIES) – információkat gyűjt a parancsok végrehajtásáról
* [track_counts](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-COUNTS) – statisztikai adatokat gyűjt az adatbázis-tevékenységről
* [track_functions](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-FUNCTIONS) – a függvény szintű statisztikát gyűjti az adatbázis tevékenységéről
* [track_io_timing](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-IO-TIMING) – az adatbázis I/O-tevékenységének időzítési statisztikáit gyűjti.
* [transform_null_equals](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-TRANSFORM-NULL-EQUALS) – a "kifejezés = null" kifejezést a "kifejezés null" értékre kezeli.
* [vacuum_cost_delay](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-DELAY) – vákuumos költséghatékonyság ezredmásodpercben
* [vacuum_cost_limit](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-LIMIT) – a pelenka előtt elérhető vákuumos költségadatok
* [vacuum_cost_page_dirty](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-DIRTY) – vákuumos dirtied oldalon
* [vacuum_cost_page_hit](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-HIT) – a puffer gyorsítótárában található oldal vákuumos díja
* [vacuum_cost_page_miss](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-MISS) – az oldal vákuumos díja nem található a puffer gyorsítótárában
* [vacuum_defer_cleanup_age](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-VACUUM-DEFER-CLEANUP-AGE) – a tranzakciók száma, amelyekkel a vákuumot és a gyors tisztítást késleltetni kell, ha van
* [vacuum_freeze_min_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-FREEZE-MIN-AGE) – az a minimális korhatár, amelynél a vákuumnak rögzítenie kell egy táblázat sort
* [vacuum_freeze_table_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-FREEZE-TABLE-AGE) -kor, amikor a vákuumnak a teljes táblázatot be kell olvasnia a rekordok rögzítése érdekében
* [vacuum_multixact_freeze_min_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-MULTIXACT-FREEZE-MIN-AGE) – az a minimális korhatár, amelynél a vákuumnak egy tábla soraiban be kell fagyasztania a többszintű
* [vacuum_multixact_freeze_table_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-MULTIXACT-FREEZE-TABLE-AGE) – a multixact életkora, amikor a vákuumnak a teljes táblázatot be kell olvasnia a rekordok rögzítése érdekében
* [wal_receiver_status_interval](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-WAL-RECEIVER-STATUS-INTERVAL) – beállítja a maximális időtartamot a Wal-fogadói állapotjelentések között az elsődlegesre
* [wal_writer_delay](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-WAL-WRITER-DELAY) -idő a Wal-író által végrehajtott Wal-kiürítések között
* [wal_writer_flush_after](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-WAL-WRITER-FLUSH-AFTER) – a Wal-író által kiváltott, a kiürítést kiváltó Wal-mennyiség
* [work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-WORK-MEM) – beállítja a belső rendezési műveletek és a kivonatoló táblák által az ideiglenes lemezekre való írás előtt használandó memória mennyiségét.
* [xmlbinary](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-XMLBINARY) – beállítja, hogy a bináris értékek KÓDOLÁSa XML-ben történjen
* [xmloption](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-XMLOPTION) – beállítja, hogy az XML-adatok implicit elemzési és szerializálási műveletekben dokumentumként vagy tartalmi töredékként tekintendők-e

## <a name="next-steps"></a>Következő lépések

* A konfiguráció egy másik formája a kiszolgálói paraméterek mellett egy nagy kapacitású-(Citus-) kiszolgálócsoport erőforrás- [konfigurációs beállításai](concepts-hyperscale-configuration-options.md) .
* Az alapul szolgáló PostgreSQL-adatbázis [konfigurációs paramétereket](http://www.postgresql.org/docs/current/static/runtime-config.html)is tartalmaz.
