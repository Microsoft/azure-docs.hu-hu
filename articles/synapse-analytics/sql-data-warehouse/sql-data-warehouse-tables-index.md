---
title: Táblázatok indexelése
description: Javaslatok és példák a táblák indexeléséhez dedikált SQL-készletben.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/18/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: fabbdf330d43737ffa85379f9cc4d5ac59c4a734
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98673518"
---
# <a name="indexing-dedicated-sql-pool-tables-in-azure-synapse-analytics"></a>Dedikált SQL Pool-táblázatok indexelése az Azure szinapszis Analyticsben

Javaslatok és példák a táblák indexeléséhez dedikált SQL-készletben.

## <a name="index-types"></a>Indextípusok

A dedikált SQL-készlet több indexelési lehetőséget is kínál, többek között a [fürtözött oszlopcentrikus indexeket](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), a [fürtözött indexeket és a nem fürtözött indexeket](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [, valamint a](/sql/relational-databases/indexes/heaps-tables-without-clustered-indexes?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)nem indexelt lehetőséget is.  

Indextel rendelkező tábla létrehozásához tekintse meg a [create Table (DEDIKÁLT SQL-készlet)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) dokumentációját.

## <a name="clustered-columnstore-indexes"></a>Fürtözött oszlopcentrikus indexek

Alapértelmezés szerint a dedikált SQL-készlet létrehoz egy fürtözött oszlopcentrikus indexet, ha nincs megadva tárgymutató-beállítás a táblán. A fürtözött oszlopcentrikus táblák a legmagasabb szintű adattömörítést, valamint a legjobb összesített lekérdezési teljesítményt egyaránt tartalmazzák.  A fürtözött oszlopcentrikus táblák általában jobban teljesítik a fürtözött indexet vagy a kupac táblákat, és általában a legjobb választás a nagyméretű táblákhoz.  Ezen okok miatt a fürtözött oszlopcentrikus a legjobb kiindulópont, ha nem tudja, hogyan indexelheti a táblázatot.  

Fürtözött oszlopcentrikus-tábla létrehozásához egyszerűen írja be a FÜRTÖZÖTT OSZLOPCENTRIKUS INDEXet a WITH záradékban, vagy hagyja ki a WITH záradékot:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

Vannak olyan helyzetek, amikor a fürtözött oszlopcentrikus esetleg nem jó megoldás:

- A oszlopcentrikus táblák nem támogatják a varchar (max), a nvarchar (max) és a varbinary (max) típust. Ehelyett vegye figyelembe a halom vagy fürtözött indexet.
- Előfordulhat, hogy a oszlopcentrikus-táblák kevésbé hatékonyak az átmeneti adatmennyiségek esetében. Gondolja át a kupacot, és talán akár ideiglenes táblákat is.
- Kisebb táblák, amelyek kevesebb mint 60 000 000 sorral rendelkeznek. Vegye figyelembe a halom táblákat.

## <a name="heap-tables"></a>Halom táblák

Ha ideiglenesen kiveszi az adattárolást a dedikált SQL-készletben, akkor előfordulhat, hogy egy halom tábla használatával a folyamat gyorsabb lesz. Ennek az az oka, hogy a halomba való betöltés gyorsabb, mint a táblák indexelése, és bizonyos esetekben az ezt követő olvasás a gyorsítótárból hajtható végre.  Ha csak a további átalakítások futtatása előtt kívánja betölteni az adatgyűjtést, a tábla betöltése a halom táblába sokkal gyorsabb, mint a fürtözött oszlopcentrikus táblába való betöltés. Emellett az adatok [ideiglenes táblába](sql-data-warehouse-tables-temporary.md) való betöltése gyorsabban betöltődik, mint a tábla állandó tárterületre való betöltése.  Az betöltést követően indexeket hozhat létre a táblában a lekérdezési teljesítmény gyorsabb elérése érdekében.  

A fürt oszlopcentrikus táblái megkezdik az optimális tömörítést, ha több mint 60 000 000 sor van.  Kisméretű keresési táblák esetében, kevesebb mint 60 000 000 sor, érdemes lehet HEAP vagy fürtözött indexet használni a gyorsabb lekérdezési teljesítmény érdekében. 

Egy halom tábla létrehozásához egyszerűen írja be a KUPACot a WITH záradékban:

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

A fürtözött indexek jobban teljesítik a fürtözött oszlopcentrikus táblákat, amikor egyetlen sor gyors lekérését kell lekérni. Olyan lekérdezéseknél, amelyekben egy vagy nagyon kevés sor keresésére van szükség a szélsőséges sebességű végrehajtáshoz, vegye fontolóra a fürt vagy a nem fürtözött másodlagos index megtartását. A fürtözött indexek használatának hátránya, hogy csak azok a lekérdezések jelennek meg, amelyek előnyben részesülnek a fürtözött index oszlopban található, nagyon szelektív szűrő használatával. Más oszlopok szűrésének javítása érdekében a nem fürtözött indexet más oszlopokhoz is hozzáadhatja. A táblához hozzáadott összes index azonban a terheléshez is felveszi a tárhelyet és a feldolgozási időt.

Fürtözött index tábla létrehozásához egyszerűen írja be a FÜRTÖZÖTT INDEXet a WITH záradékban:

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

A fürtözött oszlopcentrikus táblák az adatszegmensbe vannak rendezve.  A magas szegmensek minősége kritikus fontosságú a oszlopcentrikus-táblák optimális lekérdezési teljesítményének eléréséhez.  A szegmens minőségét a tömörített sorcsoport sorainak száma alapján lehet mérni.  A szegmens minősége a legalkalmasabb, ahol legalább 100 000 sor van egy tömörített sorcsoport esetében, és a teljesítményük a sorban álló sorok száma alapján 1 048 576 sor, amely a sorcsoport számára a legtöbb sort tartalmazni tudja.

Az alábbi nézet létrehozható és felhasználható a rendszeren, hogy kiszámítsa az átlagos sorok/sorok csoportosítását, és azonosítsa az optimális oszlopcentrikus indexeket.  A nézet utolsó oszlopa egy SQL-utasítást hoz létre, amely az indexek újraépítéséhez használható.

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

Most, hogy létrehozta a nézetet, futtassa ezt a lekérdezést a több mint 100 000 sorból álló sorcsoport-táblák azonosításához. Természetesen előfordulhat, hogy az optimális szegmensek minőségének növeléséhez érdemes megnövelni a 100K küszöbértékét.

```sql
SELECT    *
FROM    [dbo].[vColumnstoreDensity]
WHERE    COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

A lekérdezés futtatása után megkezdheti az adatgyűjtés megkeresését és az eredmények elemzését. Ez a táblázat ismerteti, hogy mit kell keresni a sorcsoport-elemzésben.

| Oszlop | Az adathasználat használata |
| --- | --- |
| [table_partition_count] |Ha a tábla particionálva van, akkor várhatóan nagyobb számú nyitott sorcsoport is látható. A terjesztés minden partíciója elméletileg rendelkezhet egy nyitott sorcsoport-csoporttal. Ezt a faktort elemezze. A particionált kis tábla optimalizálható úgy, hogy teljesen eltávolítja a particionálást, mivel ez javítaná a tömörítést. |
| [row_count_total] |A tábla teljes sorainak száma. Ezzel az értékkel például kiszámíthatja a tömörített állapotú sorok százalékos arányát. |
| [row_count_per_distribution_MAX] |Ha az összes sor egyenletesen van elosztva, ez az érték lesz az eloszlásban megcélzott sorok száma. Hasonlítsa össze ezt az értéket a compressed_rowgroup_countával. |
| [COMPRESSED_rowgroup_rows] |A táblához tartozó oszlopcentrikus-formátumú sorok teljes száma. |
| [COMPRESSED_rowgroup_rows_AVG] |Ha a sorok átlagos száma jelentősen kisebb, mint a sorcsoport sorainak maximális száma, akkor érdemes lehet CTAS használni, vagy módosítani az INDEX újraépítését az adatok újratömörítéséhez |
| [COMPRESSED_rowgroup_count] |Oszlopcentrikus formátumú sorok száma Ha ez a szám nagyon magas a táblázathoz képest, az azt jelzi, hogy a oszlopcentrikus sűrűsége alacsony. |
| [COMPRESSED_rowgroup_rows_DELETED] |A sorok logikailag törlődnek oszlopcentrikus formátumban. Ha a szám a táblázat méretéhez képest magas, érdemes lehet újból létrehozni a partíciót vagy újraépíteni az indexet, mivel ez fizikailag eltávolítja őket. |
| [COMPRESSED_rowgroup_rows_MIN] |Használja ezt az AVG és a MAX oszlopok együttes használatával, hogy megértse a oszlopcentrikus lévő Sorcsoportok értékeinek tartományát. A betöltési küszöbérték (102 400/partícióra igazított eloszlás) alsó száma azt jelzi, hogy az optimalizálások elérhetők az adatterhelésben |
| [COMPRESSED_rowgroup_rows_MAX] |Mint fent |
| [OPEN_rowgroup_count] |A sorcsoport-csoportok megnyitása normális. Az egyik a tábla eloszlása (60) szerint egy nyitott sorcsoport. A túlzott szám a partíciók közötti betöltést javasolja. Ellenőrizze a particionálási stratégiát, és győződjön meg róla, hogy a hang |
| [OPEN_rowgroup_rows] |Az egyes sorcsoport-csoportok legfeljebb 1 048 576 sort tartalmazhatnak. Ezzel az értékkel megtekintheti, hogy a nyitott sorcsoport-csoportok jelenleg milyen teljes körűek |
| [OPEN_rowgroup_rows_MIN] |A nyitott csoportok azt jelzik, hogy az adat folyamatban van a táblába való betöltéskor, vagy hogy az előző terhelés a fennmaradó sorok között lett kiosztva ebbe a sorcsoport-csoportba. A MIN, a MAX, az AVG oszlop használatával megtekintheti, hogy mennyi az információ a nyitott sorcsoport-csoportokban. Kis táblák esetében az összes adat 100%-a lehet! Ebben az esetben módosítsa az INDEX újraépítését, hogy az oszlopcentrikus-re kényszerítse az adatvesztést. |
| [OPEN_rowgroup_rows_MAX] |Mint fent |
| [OPEN_rowgroup_rows_AVG] |Mint fent |
| [CLOSED_rowgroup_rows] |Tekintse meg a lezárt sorcsoport sorait a józan ész-vizsgálat során. |
| [CLOSED_rowgroup_count] |A lezárt sorcsoport-csoportok számának alacsonynak kell lennie, ha egyáltalán látható. A lezárt sorcsoport a ALTER INDEX használatával konvertálható tömörített sorcsoport-csoportokra... Parancs átrendezése. Ez azonban általában nem kötelező. A lezárt csoportok automatikusan oszlopcentrikus lesznek konvertálva a "rekordot mozgató" folyamat hátterében. |
| [CLOSED_rowgroup_rows_MIN] |A zárt sorcsoport-csoportoknak nagyon nagy kitöltési sebességgel kell rendelkezniük. Ha egy lezárt sorcsoport kitöltési sebessége alacsony, akkor a oszlopcentrikus további elemzésére van szükség. |
| [CLOSED_rowgroup_rows_MAX] |Mint fent |
| [CLOSED_rowgroup_rows_AVG] |Mint fent |
| [Rebuild_Index_SQL] |SQL egy tábla oszlopcentrikus indexének újraépítéséhez |

## <a name="causes-of-poor-columnstore-index-quality"></a>az oszlopcentrikus indexek gyenge minőségének okait

Ha a szegmensek gyenge minőségével rendelkező táblákat azonosította, a kiváltó okot érdemes azonosítania.  Az alábbiakban a gyenge szegmensek minőségének leggyakoribb okai láthatók:

1. Memória-nyomás az index létrehozásakor
2. Nagy mennyiségű DML-művelet
3. Kis-vagy csepegtető terhelési műveletek
4. Túl sok partíció

Ezek a tényezők azt okozhatják, hogy a oszlopcentrikus indexe jelentősen kevesebb legyen, mint az optimális 1 000 000-sorok száma. Azt is okozhatják, hogy a sorok nem egy tömörített sorcsoport helyett a különbözeti sorcsoport-csoportra lépjenek.

### <a name="memory-pressure-when-index-was-built"></a>Memória-nyomás az index létrehozásakor

A tömörített sorcsoport sorainak száma közvetlenül kapcsolódik a sor szélességéhez és a sorcsoport feldolgozásához rendelkezésre álló memória mennyiségéhez.  Amikor a sorokat nagy memóriaterhelés mellett írja oszlopcentrikus táblákba, az oszlopcentrikus szegmens minősége gyengülhet.  Ezért az ajánlott eljárás az, hogy a munkamenetet adja meg, amely a lehető legtöbb memóriához fér hozzá a oszlopcentrikus index tábláihoz.  Mivel a memória és a Egyidejűség között kompromisszum van, a megfelelő memória kiosztására vonatkozó útmutatás a táblázat minden egyes sorában található adatoktól, a rendszer számára lefoglalt adatraktár-egységektől, valamint a-munkamenetbe felhasználható egyidejűségi tárolóhelyek számától függ.

### <a name="high-volume-of-dml-operations"></a>Nagy mennyiségű DML-művelet

A sorok frissítését és törlését nagy mennyiségű DML-művelet jelentheti a oszlopcentrikus. Ez különösen akkor igaz, ha egy sorcsoport sorainak többségét módosítják.

- Ha egy sort töröl egy tömörített sorcsoport-csoportból, csak logikailag jelöli meg a sort. A sor addig marad a tömörített sorcsoport-csoportban, amíg a partíció vagy a tábla újra nem épül.
- Egy sor beszúrása a sort egy belső sortárindex létrehozását táblázatba helyezi, amelynek neve különbözeti sorcsoport. A beszúrt sor nem konvertálható oszlopcentrikus, amíg a különbözeti sor csoport megtelt, és a jelölése lezártként van megjelölve. A sorcsoport bezárult, ha elérik a 1 048 576-es sorok maximális kapacitását.
- A oszlopcentrikus-formátumú sorok frissítése logikai törlésként, majd beszúrásként történik. Előfordulhat, hogy a beszúrt sor a különbözeti tárolóban tárolódik.

A kötegelt frissítési és beszúrási műveletek, amelyek túllépik az 102 400-es sorok tömeges kiosztását a partíciók által igazított eloszlásban, közvetlenül a oszlopcentrikus formátumra kerülnek. Azonban feltételezve, hogy még eloszlás is van, egyetlen műveletnél több mint 6 144 000 sort kell módosítania. Ha egy adott partícióhoz igazított eloszlás sorainak száma kevesebb, mint 102 400, akkor a sorok a különbözeti tárolóba kerülnek, és ott maradnak, amíg be nem szúrják vagy módosították a sort a sorcsoport bezárásához, vagy az index újraépítése megtörtént.

### <a name="small-or-trickle-load-operations"></a>Kis-vagy csepegtető terhelési műveletek

A dedikált SQL-készletbe beáramló kis terhelések is más néven szivárgási terhelésként is ismertek. Általában a rendszer által betöltött, közel állandó adatfolyamot jelentenek. Mivel azonban ez az adatfolyam közel folyamatos, a sorok mennyisége nem különösebben nagy. Gyakrabban, mint nem az adat jelentősen a közvetlen betöltés oszlopcentrikus formátumhoz szükséges küszöbérték alatt van.

Ezekben az esetekben általában jobb az Azure Blob Storage-ban az adatgyűjtés, és a betöltés előtt is felhalmozható. Ezt a technikát gyakran nevezik *mikro-kötegnek*.

### <a name="too-many-partitions"></a>Túl sok partíció

Egy másik megfontolandó dolog a fürtözött oszlopcentrikus-táblák particionálásának hatása.  A particionálás előtt a dedikált SQL-készlet már 60-adatbázisba osztja az adatait.  A particionálás tovább osztja az adatait.  Ha particionálja az adatait, vegye figyelembe, hogy az **egyes** partíciók legalább 1 000 000 sort igényelnek egy fürtözött oszlopcentrikus-index kihasználása érdekében.  Ha 100 partícióra particionálja a táblázatot, a táblának legalább 6 000 000 000 sort kell használnia a fürtözött oszlopcentrikus-index (60-disztribúciók *100-partíciók* 1 000 000-sorok) kihasználása érdekében. Ha az 100-Partition tábla nem rendelkezik 6 000 000 000-sorral, csökkentse a partíciók számát, vagy használjon egy halom táblát.

Miután betöltötte a táblákat néhány adattal, az alábbi lépéseket követve azonosíthatja és újraépítheti a táblákat az optimális oszlopcentrikus-indexekkel.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Indexek újraépítése a szegmens minőségének javítása érdekében

### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>1. lépés: a megfelelő erőforrás-osztályt használó felhasználó azonosítása vagy létrehozása

Egy gyors módszer a szegmens minőségének azonnali javítására az index újraépítése.  A fenti nézet által visszaadott SQL ALTER INDEX Rebuild utasítást ad vissza, amely az indexek újraépítésére használható. Az indexek újraépítésekor ügyeljen arra, hogy elegendő memóriát foglaljon le a munkamenethez, amely újraépíti az indexet.  Ehhez növelje egy olyan felhasználó erőforrás-osztályát, amely jogosult a táblázat indexének újraépítésére a javasolt minimumra.

Az alábbi példa bemutatja, hogyan oszthat ki több memóriát a felhasználók számára az erőforrás-osztályának növelésével. Az erőforrás-osztályokkal való munkavégzéshez lásd: [erőforrás-osztályok a számítási feladatok kezeléséhez](resource-classes-for-workload-management.md).

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>2. lépés: a fürtözött oszlopcentrikus indexek újraépítése magasabb erőforrás-osztállyal rendelkező felhasználóval

Jelentkezzen be felhasználóként az 1. lépésből (pl. LoadUser), amely már magasabb erőforrás-osztályt használ, és végrehajtja az ALTER INDEX utasításait. Ügyeljen arra, hogy a felhasználó megváltoztassa a táblákat, amelyeken az index újraépítése zajlik. Ezek a példák azt mutatják be, hogyan lehet újraépíteni a teljes oszlopcentrikus-indexet, illetve hogyan lehet újraépíteni egy partíciót. A nagyméretű táblákon az indexek újraépítése sokkal hatékonyabb, ha egyszerre egy partíciót hoz létre.

Az index újraépítése helyett átmásolhatja a táblát egy új táblába a [CTAS használatával](sql-data-warehouse-develop-ctas.md). Melyik a legjobb módszer? Nagy adatmennyiségek esetén a CTAS általában gyorsabb, mint az [Alter index](/sql/t-sql/statements/alter-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Kisebb adatmennyiségek esetén az ALTER INDEX könnyebben használható, és nem szükséges a tábla kicserélése.

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

```sql
-- Rebuild a single partition with archival compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE_ARCHIVE)
```

```sql
-- Rebuild a single partition with columnstore compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE)
```

Az indexek újraépítése a dedikált SQL-készletben offline művelet.  Az indexek újraépítésével kapcsolatos további információkért tekintse meg az indexek átállítása az [Oszlopcentrikus indexek töredezettségmentesítéséről](/sql/relational-databases/indexes/columnstore-indexes-defragmentation?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)című szakaszt, és [módosítsa az indexet](/sql/t-sql/statements/alter-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>3. lépés: a fürtözött oszlopcentrikus szegmens minőségének ellenőrzése javult

Futtassa újra a lekérdezést, amely a gyenge szegmensek minőségével rendelkező táblázatot azonosította, és a szegmens minőségének ellenőrzése javult.  Ha a szegmens minősége nem javult, akkor előfordulhat, hogy a tábla sorainak használata extra széles.  Az indexek újraépítésekor érdemes nagyobb erőforrás-osztályt vagy DWU használni.

## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>Indexek újraépítése a CTAS és a partíciós váltással

Ebben a példában a [CREATE TABLE as Select (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) utasítás és a partíciók váltása lehetőségre kattintva újraépíthető egy tábla-partíció.

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

További információ a partíciók újbóli létrehozásáról a CTAS használatával: [partíciók használata DEDIKÁLT SQL-készletben](sql-data-warehouse-tables-partition.md).

## <a name="next-steps"></a>Következő lépések

További információ a táblázatok létrehozásáról: a [táblázatok fejlesztése](sql-data-warehouse-tables-overview.md).
