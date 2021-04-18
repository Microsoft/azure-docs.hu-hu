---
title: Táblák indexelése
description: Javaslatok és példák a táblák dedikált SQL-készletben való indexelésével kapcsolatban.
services: synapse-analytics
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/16/2021
author: XiaoyuMSFT
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 58f3eed8b16ff3ed02c6dfac6dc7d72ebb4ca374
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599978"
---
# <a name="indexing-dedicated-sql-pool-tables-in-azure-synapse-analytics"></a>Dedikált SQL-készlet tábláinak indexelése a Azure Synapse Analytics

Javaslatok és példák a táblák dedikált SQL-készletben való indexelésével kapcsolatban.

## <a name="index-types"></a>Indextípusok

A dedikált SQL-készlet számos indexelési lehetőséget kínál, beleértve a fürtözött oszlopcentrikus [indexeket,](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)a fürtözött [indexeket](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)és a nem fürtözött indexeket, valamint egy nem indexelési lehetőséget, más néven [halommemópontot.](/sql/relational-databases/indexes/heaps-tables-without-clustered-indexes?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)  

Ha indexszel hoz létre táblát, tekintse meg a CREATE TABLE [(dedikált SQL-készlet) dokumentációját.](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="clustered-columnstore-indexes"></a>Fürtözött oszlopcentrikus indexek

Alapértelmezés szerint a dedikált SQL-készlet egy fürtözött oszlopcentrikus indexet hoz létre, ha nincs indexbeállítás megadva egy táblán. A fürtözött oszlopcentrikus táblák a legmagasabb szintű adattömörítést és a legjobb teljes lekérdezési teljesítményt kínálják.  A fürtözött oszlopcentrikus táblák általában jobbak, mint a fürtözött index- vagy halomtáblák, és általában a legjobb választás nagy táblák esetén.  Ezen okokból a fürtözött oszlopcentrikus a legjobb választás, ha nem biztos benne, hogyan indexelné a táblát.  

Fürtözött oszlopcentrikus tábla létrehozásához egyszerűen adja meg a CLUSTERED COLUMNSTORE INDEX értéket a WITH záradékban, vagy hagyja kikapcsolva a WITH záradékot:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

Néhány esetben előfordulhat, hogy a fürtözött oszlopcentrikus adattár nem jó választás:

- Az oszlopcentrikus táblák nem támogatják a varchar(max), nvarchar(max) és varbinary(max) értékeket. Érdemes inkább halommemóba vagy fürtözött indexet használni.
- Az oszlopcentrikus táblák kevésbé hatékonyak lehetnek az átmeneti adatoknál. Vesszünk fel halommemócát, vagy akár ideiglenes táblákat is.
- Kisebb táblák, amelyek kevesebb mint 60 millió sort tartalmaznak. Vesszünk meg halomtáblákat.

## <a name="heap-tables"></a>Halomtáblák

Amikor ideiglenesen egy dedikált SQL-készletbe adatokat ad vissza, előfordulhat, hogy egy halomtábla használatával a teljes folyamat gyorsabb lesz. Ennek az az oka, hogy a halommemócákba való beterhelés gyorsabb, mint a táblák indexelése, és bizonyos esetekben a későbbi olvasás a gyorsítótárból is használhatja.  Ha csak a további átalakítások futtatása előtt tölt be adatokat, a tábla halomtáblába való betöltése sokkal gyorsabb, mint egy fürtözött oszlopcentrikus táblába betölteni. Emellett az adatok ideiglenes táblába való [betöltése](sql-data-warehouse-tables-temporary.md) gyorsabb, mint egy tábla állandó tárolóba való betöltése.  Az adatok betöltése után indexeket hozhat létre a táblában a gyorsabb lekérdezési teljesítmény érdekében.  

A fürt oszlopcentrikus táblái optimális tömörítést érhetnek el, ha több mint 60 millió sor van.  A kisebb, 60 millió sornál kisebb keresési táblák esetén érdemes lehet HEAP vagy fürtözött indexet használni a gyorsabb lekérdezési teljesítmény érdekében. 

Halomtábla létrehozásához egyszerűen adja meg a HEAP értéket a WITH záradékban:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( HEAP );
```

## <a name="clustered-and-nonclustered-indexes"></a>Fürtözött és nem fürtözött indexek

A fürtözött indexek akkor lehetnek nagyobbak, mint a fürtözött oszlopcentrikus táblák, ha egyetlen sort kell gyorsan lekérni. Olyan lekérdezések esetén, ahol egy vagy nagyon kevés sorkeresésre van szükség a rendkívül gyors végrehajtásához, érdemes lehet fürtözött indexet vagy nem fürtözött másodlagos indexet használni. A fürtözött index használatának hátránya, hogy csak azok a lekérdezések élveznek előnyt, amelyek kifejezetten szelektív szűrőt alkalmaznak a fürtözött indexoszlopra. A más oszlopokra vonatkozó szűrő javításához hozzá lehet adni egy nemclusterált indexet más oszlopokhoz. A táblához adott indexek azonban a betöltése során lemezterületet és feldolgozási időt is hozzáadnak.

Fürtözött indextábla létrehozásához egyszerűen adja meg a CLUSTERED INDEX értéket a WITH záradékban:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

Ha nem fürtözött indexet szeretne hozzáadni egy táblához, használja a következő szintaxist:

```SQL
CREATE INDEX zipCodeIndex ON myTable (zipCode);
```

## <a name="optimizing-clustered-columnstore-indexes"></a>Fürtözött oszlopcentrikus indexek optimalizálása

A fürtözött oszlopcentrikus táblák adatszegmensekbe vannak rendezve.  A magas szegmensminőség kritikus fontosságú az optimális lekérdezési teljesítmény eléréséhez egy oszlopcentrikus táblán.  A szegmensminőség a tömörített sorcsoportban lévő sorok száma alapján mérhető.  A szegmensminőség akkor a legoptimálisabb, ha tömörített sorcsoportonként legalább 100 000 sor van, és a teljesítmény is jobb, mivel a sorok száma sorcsoportonként megközelítés 1 048 576 sor, amely a sorcsoport által tartalmazott legtöbb sor.

Az alábbi nézetet a rendszeren lehet létrehozni és használni a sorcsoportonkénti átlagos sorok kiszámításához és az optimálisnál nem optimális fürtözött oszlopcentrikus indexek azonosításához.  A nézet utolsó oszlopa létrehoz egy SQL-utasítást, amely az indexek újraépítésére használható.

```sql
CREATE VIEW dbo.vColumnstoreDensity
AS
SELECT
        GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       s.name                                                                  AS [schema_name]
,       t.name                                                                  AS [table_name]
,    COUNT(DISTINCT rg.[partition_number])                    AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,    CEILING    ((SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id]))/1048576) AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 0 THEN 1                   ELSE 0    END)    AS [INVISIBLE_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE 0    END)    AS [INVISIBLE_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 1 THEN 1                   ELSE 0    END)    AS [OPEN_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE 0    END)    AS [OPEN_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 2 THEN 1                   ELSE 0    END)    AS [CLOSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE 0    END)    AS [CLOSED_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 3 THEN 1                   ELSE 0    END)    AS [COMPRESSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE 0    END)    AS [COMPRESSED_rowgroup_rows]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[deleted_rows]   ELSE 0    END)    AS [COMPRESSED_rowgroup_rows_DELETED]
,       MIN(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_AVG]
,       'ALTER INDEX ALL ON ' + s.name + '.' + t.NAME + ' REBUILD;'             AS [Rebuild_Index_SQL]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
JOIN    sys.[schemas] s                             ON t.[schema_id]            = s.[schema_id]
GROUP BY
        s.[name]
,       t.[name]
;
```

Most, hogy létrehozta a nézetet, futtassa ezt a lekérdezést a 100 000-esnél kevesebb sort tartalmazó sorcsoportokat tartalmazó táblák azonosításához. Ha optimálisabb szegmensminőséget keres, természetesen növelheti a 100 000-es küszöbértéket.

```sql
SELECT    *
FROM    [dbo].[vColumnstoreDensity]
WHERE    COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

A lekérdezés futtatása után megkezdheti az adatok elemzését. Ez a táblázat ismerteti, hogy mit kell keresnie a sorcsoport elemzésében.

| Oszlop | Az adatok használata |
| --- | --- |
| [table_partition_count] |Ha a tábla particionálva van, akkor előfordulhat, hogy a nyitott sorcsoportszámok száma magasabb lesz. Az eloszlás minden partíciója elméletileg egy nyitott sorcsoporttal is társítható. Ezt is figyelembe kell vennie az elemzésben. Egy particionált kis tábla optimalizálható a particionálás teljes eltávolításával, mivel ez javítaná a tömörítést. |
| [row_count_total] |A tábla összes sorának száma. Ezzel az értékkel például kiszámíthatja a tömörített állapotban lévő sorok százalékos arányát. |
| [row_count_per_distribution_MAX] |Ha minden sor egyenlően van elosztva, ez az érték a sorok eloszlásonkénti célszáma lenne. Hasonlítsa össze ezt az értéket a compressed_rowgroup_count. |
| [COMPRESSED_rowgroup_rows] |A tábla oszlopcentrikus formátumú sorai teljes száma. |
| [COMPRESSED_rowgroup_rows_AVG] |Ha a sorok átlagos száma lényegesen kevesebb, mint a sorcsoport sorai maximális száma, fontolja meg a CTAS vagy az ALTER INDEX REBUILD használata az adatok újrakompresszálását |
| [COMPRESSED_rowgroup_count] |Sorcsoportok száma oszlopcentrikus formátumban. Ha ez a szám nagyon magas a táblához képest, az azt jelzi, hogy az oszlopcentrikus sűrűség alacsony. |
| [COMPRESSED_rowgroup_rows_DELETED] |A sorok logikailag oszlopcentrikus formátumban törlődnek. Ha a szám nagy a táblamérethez képest, érdemes lehet újraépíteni a partíciót vagy újraépíteni az indexet, mivel ezzel fizikailag eltávolítja őket. |
| [COMPRESSED_rowgroup_rows_MIN] |Ezt az AVG és a MAX oszlopokkal együtt használva megértheti az oszlopcentrikus sorcsoportok értékeinek tartományát. Ha egy alacsony szám túllépi a betöltési küszöbértéket (partícióhoz igazított eloszlásonként 102 400), az azt sugallja, hogy optimalizálási lehetőségek érhetők el az adatbetöltésben |
| [COMPRESSED_rowgroup_rows_MAX] |A fentiek szerint |
| [OPEN_rowgroup_count] |A nyitott sorcsoportok normálak. Táblaeloszlásonként ésszerűen egy OPEN sorcsoportra számíthatunk (60). A túl sok adat partíciók közötti adatbetöltésre utal. Ellenőrizze, hogy a particionálási stratégia megfelelő-e |
| [OPEN_rowgroup_rows] |Minden sorcsoport legfeljebb 1 048 576 sorral lehet. Ezzel az értékkel láthatja, hogy jelenleg milyen teljesek a nyitott sorcsoportok |
| [OPEN_rowgroup_rows_MIN] |A nyitott csoportok azt jelzik, hogy az adatok betöltése vagy a táblába kerül, vagy hogy az előző terhelés a fennmaradó sorokon keresztül ebbe a sorcsoportba ömlött. A MIN, MAX, AVG oszlopokkal láthatja, hogy mennyi adat van az OPEN sorcsoportokban. Kis tábláknál ez az összes adat 100%-a lehet! Ebben az esetben az ALTER INDEX REBUILD ,hogy az adatokat oszlopcentrikusra kényszerítse. |
| [OPEN_rowgroup_rows_MAX] |A fentiek szerint |
| [OPEN_rowgroup_rows_AVG] |A fentiek szerint |
| [CLOSED_rowgroup_rows] |Tekintse meg a lezárt sorcsoport sorait sanitás-ellenőrzésként. |
| [CLOSED_rowgroup_count] |A zárt sorcsoportok számának alacsonynak kell lennie, ha van ilyen. A zárt sorcsoportok tömörített sorcsoportokká konvertálhatók az ALTER INDEX ... használatával. REORGANIZE parancs. Ez azonban általában nem szükséges. A zárt csoportokat a rendszer automatikusan oszlopcentrikus sorcsoportokká alakítja a háttérben futó "rekordátvételi" folyamat során. |
| [CLOSED_rowgroup_rows_MIN] |A zárt sorcsoportoknak nagyon magas kitöltési sebességűnek kell lennie. Ha egy zárt sorcsoport kitöltési sebessége alacsony, akkor az oszlopcentrikus oszlopcentrikus további elemzésre van szükség. |
| [CLOSED_rowgroup_rows_MAX] |A fentiek szerint |
| [CLOSED_rowgroup_rows_AVG] |A fentiek szerint |
| [Rebuild_Index_SQL] |SQL egy tábla oszlopcentrikus indexének újraépítésére |

## <a name="impact-of-index-maintenance"></a>Az indexkarbantartás hatása

A nézet `Rebuild_Index_SQL` oszlopa tartalmaz egy utasítást, amely `vColumnstoreDensity` az `ALTER INDEX REBUILD` indexek újraépítésére használható. Az indexek újraépítésekor győződjön meg arról, hogy elegendő memóriát foglal le az indexet újraépítő munkamenethez. Ehhez növelje egy [](resource-classes-for-workload-management.md) olyan felhasználó erőforrásosztályát, aki engedéllyel rendelkezik a táblázat indexének újraépítésére a javasolt minimális értékre. Erre a cikk későbbi részében, az [Indexek újraépítése](#rebuilding-indexes-to-improve-segment-quality) a szegmensminőség javítása érdekében részében talál példát.

Egy rendezett fürtözött oszlopcentrikus indexet tartalmazó tábla esetén a újra fogja rendezni az adatokat `ALTER INDEX REBUILD` a tempdb használatával. A tempdb figyelése az újraépítési műveletek során. Ha több tempdb-tárhelyre van szüksége, skálázja fel horizontálisan az adatbáziskészletet. Az index újraépítésének befejezése után skálázjon le.

Rendezett fürtözött oszlopcentrikus indexet tartalmazó tábla esetén a `ALTER INDEX REORGANIZE` nem rendezi újra az adatokat. Az adatok újrarendezési sorrendbe való rendezéséhez használja a `ALTER INDEX REBUILD` et.

A rendezett fürtözött oszlopcentrikus indexekkel kapcsolatos további információkért lásd: Teljesítmény-finomhangolás rendezett fürtözött [oszlopcentrikus indexszel.](performance-tuning-ordered-cci.md)

## <a name="causes-of-poor-columnstore-index-quality"></a>az oszlopcentrikus indexek gyenge minőségének okait

Ha azonosította a gyenge szegmensminőségű táblákat, akkor azonosítania kell a kiváltó okot.  Az alábbiakban a gyenge szegmensminőség egyéb gyakori okait olvashatja:

1. Memórianyomás az index létrehozásakor
2. Nagy mennyiségű DML-művelet
3. Kisebb vagy bonyolult betöltési műveletek
4. Túl sok partíció

Ezek a tényezők azt okozhatják, hogy egy oszlopcentrikus index sorcsoportonként jelentősen kevesebb lesz az optimális 1 millió sornál. Emellett azt is okozhatják, hogy a sorok tömörített sorcsoport helyett a delta sorcsoportba fognak átmenni.

### <a name="memory-pressure-when-index-was-built"></a>Memórianyomás az index létrehozásakor

A sorok tömörített sorcsoportonkénti száma közvetlenül kapcsolódik a sor szélességéhez és a sorcsoport feldolgozásához rendelkezésre álló memória mennyiségéhez.  Amikor a sorokat nagy memóriaterhelés mellett írja oszlopcentrikus táblákba, az oszlopcentrikus szegmens minősége gyengülhet.  Ezért az ajánlott eljárás az, hogy az oszlopcentrikus indextáblákba író munkamenet a lehető legnagyobb memóriához fér hozzá.  Mivel a memória és az egyidejűség között különbségek vannak, a megfelelő memóriakiosztásra vonatkozó útmutatás a tábla egyes sorai adataitól, a rendszerhez lefoglalt adattárházegységektől és az adatokat a táblába író munkamenethez hozzárendelt egyidejűségi tárolóhelyek számától függ.

### <a name="high-volume-of-dml-operations"></a>Nagy mennyiségű DML-művelet

Ha nagy mennyiségű DML-művelet frissíti és törli a sorokat, az nem lesz megfelelő az oszloptárban. Ez különösen igaz, ha egy sorcsoport legtöbb sorát módosítják.

- Egy sor tömörített sorcsoportból való törlése csak logikusan töröltként jelöli meg a sort. A sor a tömörített sorcsoportban marad a partíció vagy a tábla újraépítésig.
- Egy sor beszúrása hozzáadja a sort egy delta sorcsoport nevű belső sorcentrikus táblához. A beszúrt sor csak akkor lesz oszlopcentrikus, ha a Delta sorcsoport megtelt, és zártként van megjelölve. A sorcsoportok bezárulnak, ha elérik az 1 048 576 sor maximális kapacitását.
- Az oszlopcentrikus formátumú sorok frissítése logikai törlésként, majd beszúrásként lesz feldolgozva. A beszúrt sor tárolható a Delta-tárolóban.

A kötegelt frissítési és beszúrási műveletek, amelyek túllépik a partícióhoz igazított elosztásonkénti 102 400 sor tömeges küszöbértékét, közvetlenül az oszlopcentrikus formátumba lépnek. Egyenletes eloszlást feltételezve azonban több mint 6,144 millió sort kell módosítania egyetlen műveletben ahhoz, hogy ez megúsa. Ha egy adott partícióhoz igazított eloszlás sorai száma kevesebb, mint 102 400, akkor a sorok a Delta-tárolóba kerülnek, és ott is maradnak, amíg elegendő sor be nem szúr vagy módosít a sorcsoport bezárása vagy az index újraépítése érdekében.

### <a name="small-or-trickle-load-operations"></a>Kisebb vagy bonyolult betöltési műveletek

A dedikált SQL-készletbe áramló kis terheléseket néha bonyolult terhelésnek is nevezik. Ezek általában a rendszer által betöltött adatok közel állandó streamét jelentik. Mivel azonban ez a stream közel folyamatos, a sorok mennyisége nem túl nagy. Az adatok gyakran jelentősen az oszlopcentrikus formátumba való közvetlen betöltéshez szükséges küszöbérték alatt vannak.

Ezekben az esetekben gyakran jobb először az adatokat az Azure Blob Storage-ba lehozni, és a betöltés előtt összegyűlni. Ezt a technikát gyakran *mikroköteezésnek is nevezik.*

### <a name="too-many-partitions"></a>Túl sok partíció

Egy másik megfontolható megfontolni a particionálásnak a fürtözött oszlopcentrikus táblákra gyakorolt hatását.  A particionálás előtt a dedikált SQL-készlet már 60 adatbázisra osztja az adatokat.  A particionálás tovább osztja az adatokat.  Ha particionálta az  adatokat, vegye figyelembe, hogy minden partíciónak legalább 1 millió sorra van szüksége a fürtözött oszlopcentrikus indexek kihasználása érdekében.  Ha a táblát 100 partícióra particionálta, akkor a táblának legalább 6 milliárd sorra van szüksége ahhoz, hogy kihasználja a fürtözött oszlopcentrikus indexet (60 elosztás *100* partíció 1 millió sor). Ha a 100 partíciós tábla nem tartalmaz 6 milliárd sort, csökkentse a partíciók számát, vagy inkább egy halomtáblát érdemes használnia.

Miután betöltötte a táblákat adatokkal, az alábbi lépéseket követve azonosíthatja és építse újra a táblákat az optimálisnál alacsonyabb fürtözött oszlopcentrikus indexekkel.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Indexek újraépítése a szegmens minőségének javítása érdekében

### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>1. lépés: A megfelelő erőforrásosztályt használó felhasználó azonosítása vagy létrehozása

A szegmensminőség azonnali javításának egyik gyors módja az index újraépítése.  A fenti nézet által visszaadott SQL egy ALTER INDEX REBUILD utasítást ad vissza, amely az indexek újraépítésére használható. Az indexek újraépítésekor győződjön meg arról, hogy elegendő memóriát foglal le az indexet újraépítő munkamenethez. Ehhez növelje egy olyan felhasználó erőforrásosztályát, aki engedéllyel rendelkezik a tábla indexének újraépítésére a javasolt minimumra.

Az alábbi példa azt mutatja be, hogyan foglalhat le több memóriát egy felhasználó számára az erőforrásosztály növelésével. Az erőforrásosztályokkal való munkához lásd: [Erőforrásosztályok a számítási feladatok kezeléséhez.](resource-classes-for-workload-management.md)

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser';
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>2. lépés: Fürtözött oszlopcentrikus indexek újraépítése magasabb erőforrásosztály-felhasználóval

Jelentkezzen be felhasználóként az 1. lépésben (LoadUser), amely most egy magasabb erőforrásosztályt használ, és hajtsa végre az ALTER INDEX utasításokat. Győződjön meg arról, hogy a felhasználó ALTER engedéllyel rendelkezik az indexet újraépítő táblákhoz. Ezek a példák bemutatják, hogyan lehet újraépíteni a teljes oszlopcentrikus indexet, vagy hogyan lehet újraépíteni egyetlen partíciót. Nagy táblákon célszerűbb egyszerre egyetlen partíciót újraépíteni.

Másik lehetőségként az index újraépítése helyett átmásolhatja a táblát egy új táblába a [CTAS használatával.](sql-data-warehouse-develop-ctas.md) Melyik a legjobb módszer? Nagy mennyiségű adat esetén a CTAS általában gyorsabb, mint az [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Kisebb adatmennyiségek esetén az ALTER INDEX könnyebben használható, és nem kell kicserélni a táblát.

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5;
```

```sql
-- Rebuild a single partition with archival compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE_ARCHIVE);
```

```sql
-- Rebuild a single partition with columnstore compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE);
```

Az indexek dedikált SQL-készletben való újraépítése offline művelet.  Az indexek újraépítésére vonatkozó további információkért lásd az oszlopcentrikus [indexek](/sql/relational-databases/indexes/columnstore-indexes-defragmentation?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)töredezettségmentesítésének ALTER INDEX REBUILD szakaszát és [az ALTER INDEX szakaszt.](/sql/t-sql/statements/alter-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>3. lépés: Annak ellenőrzése, hogy a fürtözött oszlopcentrikus szegmens minősége javult-e

Futtassa újra a gyenge szegmensminőségű táblát azonosított lekérdezést, és ellenőrizze, hogy javult-e a szegmens minősége.  Ha a szegmens minősége nem javul, akkor az is lehet, hogy a tábla sorai extra szélesek.  Érdemes lehet magasabb erőforrásosztályt vagy DWU-t használni az indexek újraépítésekor.

## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>Indexek újraépítése CTAS és partícióváltással

Ez a példa az [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) utasítást és a partícióváltást használja egy táblapartíció újraépítésére.

```sql
-- Step 1: Select the partition of data and write it out to a new table using CTAS
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 2: Switch IN the rebuilt data with TRUNCATE_TARGET option
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2 WITH (TRUNCATE_TARGET = ON);
```

A partíciók CTAS használatával való újralétrehozására vonatkozó további információkért lásd: [Partíciók használata dedikált SQL-készletben.](sql-data-warehouse-tables-partition.md)

## <a name="next-steps"></a>Következő lépések

További információ a táblák fejlesztésről: [Táblák fejlesztése.](sql-data-warehouse-tables-overview.md)
