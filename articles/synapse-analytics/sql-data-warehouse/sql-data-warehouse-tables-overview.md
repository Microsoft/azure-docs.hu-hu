---
title: Táblák tervezése
description: Bevezetés a táblák megtervezésére a dedikált SQL-készlet használatával az Azure szinapszis Analyticsben.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/15/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: c55edbd24553189c11070999ddc5d3b3516f2d97
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98737931"
---
# <a name="design-tables-using-dedicated-sql-pool-in-azure-synapse-analytics"></a>Táblák tervezése dedikált SQL-készlettel az Azure szinapszis Analytics szolgáltatásban

Ez a cikk kulcsfontosságú bevezető fogalmakat tartalmaz a táblázatok dedikált SQL-készletben való tervezéséhez.

## <a name="determine-table-category"></a>Táblázat kategóriájának meghatározása

A [csillag sémák](https://en.wikipedia.org/wiki/Star_schema) a tény-és dimenziós táblákba rendezik az adathalmazokat. Bizonyos táblákat az integrációhoz vagy az előkészítéshez kell használni, mielőtt a rendszer egy tény-vagy dimenzió-táblázatba helyezi át őket. A tábla tervezésekor döntse el, hogy a tábla adategysége tartalmaz-e egy tény-, dimenzió-vagy integrációs táblát. Ez a döntés a megfelelő tábla szerkezetét és eloszlását ismerteti.

- A **Fact Tables** olyan mennyiségi adatmennyiségeket tartalmaz, amelyek a tranzakciós rendszerekben gyakran keletkeznek, majd betöltődik a dedikált SQL-készletbe. Egy kiskereskedelmi vállalat például minden nap értékesítési tranzakciókat hoz létre, majd betölti az adathalmazt egy dedikált SQL Pool egyedkapcsolat-táblázatba az elemzéshez.

- A **dimenzió táblák** olyan attribútum-adatértékeket tartalmaznak, amelyek változhatnak, de általában ritkán változnak. Az ügyfél neve és címe például egy dimenzió táblában tárolódik, és csak akkor frissül, ha az ügyfél profilja megváltozik. A nagyméretű egyedkapcsolatok méretének minimalizálásához az ügyfél nevét és a címet nem kell a Fact tábla minden sorában megadni. Ehelyett a tény tábla és a dimenzió tábla megoszthat egy ügyfél-azonosítót. Egy lekérdezés csatlakozhat a két táblához, és hozzárendelheti az ügyfél profilját és tranzakcióit.

- Az **integrációs táblázatok** helyet biztosítanak az adatintegráláshoz vagy az előkészítéshez. Létrehozhat egy integrációs táblázatot egy normál tábla, egy külső tábla vagy egy ideiglenes tábla használatával. Például betöltheti az adatok betöltését egy előkészítési táblába, elvégezheti az átmeneti adatok átalakítását, majd beszúrhatja az adatok egy éles táblába.

## <a name="schema-and-table-names"></a>Séma-és táblanév

A sémák a táblázatok hasonló módon történő csoportosítására szolgálnak.  Ha több adatbázist telepít át egy helyszíni megoldásból egy dedikált SQL-készletbe, a legjobb, ha az összes tényt, dimenziót és integrációs táblát áttelepíti egy dedikált SQL-készlet egyik sémájára.

Például a [wideworldimportersdw adattárházat](/sql/sample/world-wide-importers/database-catalog-wwi-olap?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) -minta összes tábláját tárolhatja egy, az első világháború nevű sémán belül a dedikált SQL-készletben. A következő kód egy, az első világháború nevű [felhasználó által definiált sémát](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) hoz létre.

```sql
CREATE SCHEMA wwi;
```

A táblák Szervezetének a dedikált SQL-készletben való megjelenítéséhez a Fact, a Dim és az int előtagot használhatja a táblázatok neveihez. A következő táblázat a Wideworldimportersdw adattárházat sémájának és táblázatának egyes neveit mutatja be.  

| Wideworldimportersdw adattárházat táblázat  | Tábla típusa | Dedikált SQL-készlet |
|:-----|:-----|:------|:-----|
| City | Dimenzió | WWI. DimCity |
| Sorrend | Fact | WWI. FactOrder |

## <a name="table-persistence"></a>Tábla megőrzése

A táblák az Azure Storage-ban, ideiglenesen az Azure Storage-ban vagy a dedikált SQL-készleten kívüli adattárban tárolják az adataikat.

### <a name="regular-table"></a>Normál tábla

A normál táblázat a dedikált SQL-készlet részeként tárolja az Azure Storage-ban tárolt adatait. A tábla és az adatmennyiség attól függetlenül megmarad, hogy nyitva van-e a munkamenet.  A következő példa egy normál táblát hoz létre két oszloppal.

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Ideiglenes tábla

Egy ideiglenes tábla csak a munkamenet időtartama alatt létezik. Egy ideiglenes tábla használatával megakadályozhatja, hogy más felhasználók ideiglenes eredményeket láthassanak, és csökkentse a törlés szükségességét.  

Az ideiglenes táblák a helyi tárterületet használják a gyors teljesítmény érdekében.  További információ:  [ideiglenes táblák](sql-data-warehouse-tables-temporary.md).

### <a name="external-table"></a>Külső tábla

A külső tábla az Azure Storage-blobban vagy Azure Data Lake Storeban található adatterületre mutat. Ha a CREATE TABLE AS SELECT utasítással együtt használja, a külső táblából való kijelöléskor a rendszer a dedikált SQL-készletbe importálja az adatokból.

Így a külső táblázatok hasznosak az betöltéshez. A betöltési oktatóanyagért lásd: az [adatok Azure Blob Storage-ból való betöltésének használata](./load-data-from-azure-blob-storage-using-copy.md).

## <a name="data-types"></a>Adattípusok

A dedikált SQL-készlet a leggyakrabban használt adattípusokat támogatja. A támogatott adattípusok listáját a CREATE TABLE utasításban található [CREATE TABLE-hivatkozás adattípusai](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json?view=azure-sqldw-latest&preserve-view=true#datatypes) című témakörben tekintheti meg. Az adattípusok használatára vonatkozó útmutatásért lásd az [adattípusokat](sql-data-warehouse-tables-data-types.md).

## <a name="distributed-tables"></a>Elosztott táblák

A dedikált SQL-készlet alapvető funkciója, hogy miként tárolhatók és használhatók a táblázatok között a [disztribúciókban](massively-parallel-processing-mpp-architecture.md#distributions).  A dedikált SQL-készlet három módszert támogat az adatterjesztéshez: ciklikus multiplexelés (alapértelmezett), kivonat és replikált.

### <a name="hash-distributed-tables"></a>Kivonat alapján elosztott táblák

A kivonatok elosztott tábla a terjesztési oszlopban lévő érték alapján osztja el a sorokat. A kivonatoló elosztott tábla úgy van kialakítva, hogy nagy teljesítményű lekérdezéseket érjen el nagyméretű táblákon. A terjesztési oszlopok kiválasztásakor több tényezőt is figyelembe kell venni.

További információ: [tervezési útmutató az elosztott táblákhoz](sql-data-warehouse-tables-distribute.md).

### <a name="replicated-tables"></a>Replikált táblák

A replikált táblák minden számítási csomóponton elérhetők a tábla teljes másolatával. A lekérdezések gyorsan futnak a replikált táblákon, mivel a replikált táblákhoz tartozó illesztések nem igénylik az adatáthelyezést A replikáláshoz külön tárterület szükséges, azonban a nagyméretű táblák esetében nem praktikus.

További információ: [tervezési útmutató a replikált táblákhoz](design-guidance-for-replicated-tables.md).

### <a name="round-robin-tables"></a>Ciklikus időszeletelésű táblák

A Round-Robin tábla egyenletesen osztja el a táblázat sorait az összes eloszlás között. A sorok eloszlása véletlenszerűen történik. Az adatbetöltések gyorsak.  Ne feledje, hogy a lekérdezések a többi terjesztési módszernél több adatátvitelt is igényelhetnek.

További információ: [tervezési útmutató az elosztott táblákhoz](sql-data-warehouse-tables-distribute.md).

### <a name="common-distribution-methods-for-tables"></a>Gyakori terjesztési módszerek a táblákhoz

A tábla kategóriája általában meghatározza, hogy melyik lehetőséget kell választani a tábla terjesztéséhez.

| Táblázat kategóriája | Ajánlott terjesztési lehetőség |
|:---------------|:--------------------|
| Fact           | Használjon kivonatoló eloszlást a fürtözött oszlopcentrikus index használatával. A teljesítmény akkor javul, ha két kivonatoló tábla ugyanahhoz a terjesztési oszlophoz van csatlakoztatva. |
| Dimenzió      | Kisebb táblák esetében replikált használata. Ha a táblák túl nagyok az egyes számítási csomópontokon való tároláshoz, használja a kivonatoló eloszlást. |
| Előkészítés        | Ciklikus multiplexelés használata az előkészítési táblához. A CTAS terhelése gyors. Ha az adatgyűjtés az előkészítési táblában található, használja az INSERT... Ezzel a beállítással áthelyezheti az adatlemezeket az éles táblákba. |

## <a name="table-partitions"></a>Tábla partíciói

A particionált táblák az adattartományok szerint tárolják és végrehajtják a táblázat sorain lévő műveleteket. Egy tábla lehet például nap, hónap vagy év szerint particionálva. Javíthatja a lekérdezési teljesítményt a partíciók eltávolításán keresztül, ami korlátozza a lekérdezési vizsgálatát egy partíción belül. Az adattárolást partíciós váltással is megtarthatja. Mivel az SQL-készletben lévő adat már el van terjesztve, túl sok partíció lassítja a lekérdezések teljesítményét. További információ: [particionálási útmutató](sql-data-warehouse-tables-partition.md).  Ha a partíció nem üres táblázatos partícióra vált, érdemes lehet az [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) utasítás TRUNCATE_TARGET kapcsolóját használni, ha a meglévő adatok csonkítva lesznek. Az alábbi kód az átalakított napi adatértékeket a SalesFact felülírja a meglévő összes adattal.

```sql
ALTER TABLE SalesFact_DailyFinalLoad SWITCH PARTITION 256 TO SalesFact PARTITION 256 WITH (TRUNCATE_TARGET = ON);  
```

## <a name="columnstore-indexes"></a>Oszlopcentrikus indexek

Alapértelmezés szerint a dedikált SQL-készlet fürtözött oszlopcentrikus indexként tárolja a táblát. Ez az adattárolási űrlap nagy méretű táblákon biztosítja a nagy adattömörítést és a lekérdezési teljesítményt.  

A fürtözött oszlopcentrikus index általában a legjobb választás, de bizonyos esetekben egy fürtözött index vagy egy halom a megfelelő tárolási struktúra.  

> [!TIP]
> Egy halom tábla különösen hasznos lehet az átmeneti adatfájlok, például egy végleges táblázatba átalakított előkészítési tábla betöltéséhez.

A oszlopcentrikus-funkciók listáját a [oszlopcentrikus indexek újdonságai](/sql/relational-databases/indexes/columnstore-indexes-what-s-new?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)című témakörben tekintheti meg. A oszlopcentrikus index teljesítményének növeléséhez tekintse meg [a sorcsoport minőségének maximalizálása a oszlopcentrikus indexekhez](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)című témakört.

## <a name="statistics"></a>Statisztika

A lekérdezés-optimalizáló oszlop szintű statisztikát használ, amikor létrehoz egy lekérdezést végrehajtó tervet.

A lekérdezési teljesítmény javítása érdekében fontos, hogy az egyes oszlopokra vonatkozó statisztikát, különösen a lekérdezési illesztésekben használt oszlopokat használja. A [statisztikák létrehozása](sql-data-warehouse-tables-statistics.md#automatic-creation-of-statistic) automatikusan megtörténik.  

A statisztikák frissítése nem történik meg automatikusan. A statisztikák frissítése jelentős számú sor hozzáadása vagy módosítása után. Például egy terhelés után frissítse a statisztikát. További információ: [statisztikai útmutató](sql-data-warehouse-tables-statistics.md).

## <a name="primary-key-and-unique-key"></a>Elsődleges kulcs és egyedi kulcs

Az elsődleges kulcs csak akkor támogatott, ha nem FÜRTÖZÖTT és nem KÉNYSZERÍTett érték is használatban van.  Az egyedi korlátozás csak a nem KÉNYSZERÍTett használata esetén támogatott.  A [DEDIKÁLT SQL-készlet táblázatának korlátozásai](sql-data-warehouse-table-constraints.md).

## <a name="commands-for-creating-tables"></a>Táblázatok létrehozásához szükséges parancsok

Táblát új üres táblaként is létrehozhat. Létrehozhat és fel is tölthet egy táblát a SELECT utasítás eredményeivel. A következő a T-SQL-parancsok egy tábla létrehozásához.

| T-SQL-utasítás | Description |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Egy üres táblát hoz létre a tábla összes oszlopának és beállításának definiálásával. |
| [KÜLSŐ TÁBLA LÉTREHOZÁSA](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Létrehoz egy külső táblát. A tábla definícióját a dedikált SQL-készlet tárolja. A tábla az Azure Blob Storage-ban vagy Azure Data Lake Storeban tárolódik. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Egy új táblát tölt ki egy SELECT utasítás eredményeivel. A táblázat oszlopai és adattípusai a SELECT utasítás eredményein alapulnak. Az adatok importálásához ez az utasítás külső táblából is kiválasztható. |
| [KÜLSŐ TÁBLA LÉTREHOZÁSA KIJELÖLÉSKÉNT](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Új külső tábla létrehozása egy SELECT utasítás eredményének külső helyre való exportálásával.  A hely vagy az Azure Blob Storage vagy a Azure Data Lake Store. |

## <a name="aligning-source-data-with-dedicated-sql-pool"></a>Forrásadatok igazítása dedikált SQL-készlettel

A dedikált SQL Pool-táblázatok az adatok másik adatforrásból való betöltésével vannak feltöltve. A sikeres betöltés végrehajtásához a forrásadatok oszlopainak számát és adattípusait a dedikált SQL-készletben található tábla-definícióval kell összehangolni. A táblázatok megtervezésének legnehezebb része lehet az igazítani kívánt adatmennyiség.

Ha az adatok több adattárból származnak, az adatok betölthetők a dedikált SQL-készletbe, és egy integrációs táblában tárolhatók. Miután az adatok bekerültek az integrációs táblába, használhatja a dedikált SQL-készlet erejét az átalakítási műveletek elvégzéséhez. Az adatelőkészítést követően beillesztheti az éles táblákba.

## <a name="unsupported-table-features"></a>Nem támogatott táblázat-funkciók

A dedikált SQL-készlet a más adatbázisok által kínált tábla-funkciók számos, de nem az összes funkcióját támogatja.  Az alábbi lista a dedikált SQL-készletben nem támogatott táblázat-funkciókat mutatja be:

- Külső kulcs, ellenőrzési [táblázat megkötései](/sql/t-sql/statements/alter-table-table-constraint-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Számított oszlopok](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Indexelt nézetek](/sql/relational-databases/views/create-indexed-views?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Sequence](/sql/t-sql/statements/create-sequence-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Ritka oszlopok](/sql/relational-databases/tables/use-sparse-columns?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- Helyettesítő kulcsok. Megvalósítás [identitással](sql-data-warehouse-tables-identity.md).
- [Szinonimák](/sql/t-sql/statements/create-synonym-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Eseményindítók](/sql/t-sql/statements/create-trigger-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Egyedi indexek](/sql/t-sql/statements/create-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Felhasználó által definiált típusok](/sql/relational-databases/native-client/features/using-user-defined-types?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="table-size-queries"></a>Táblázat mérete – lekérdezések

Az egyes 60-disztribúciókban a táblák által felhasznált tárhelyek és sorok azonosításának egyik egyszerű módja a [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)használata.

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

A DBCC-parancsok használata azonban meglehetősen korlátozó lehet.  A dinamikus felügyeleti nézetek (DMV) több részletet mutatnak, mint a DBCC parancsai. Első lépésként hozza létre a nézetet:

```sql
CREATE VIEW dbo.vTableSizes
AS
WITH base
AS
(
SELECT
 GETDATE()                                                             AS  [execution_time]
, DB_NAME()                                                            AS  [database_name]
, s.name                                                               AS  [schema_name]
, t.name                                                               AS  [table_name]
, QUOTENAME(s.name)+'.'+QUOTENAME(t.name)                              AS  [two_part_name]
, nt.[name]                                                            AS  [node_table_name]
, ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))     AS  [node_table_name_seq]
, tp.[distribution_policy_desc]                                        AS  [distribution_policy_name]
, c.[name]                                                             AS  [distribution_column]
, nt.[distribution_id]                                                 AS  [distribution_id]
, i.[type]                                                             AS  [index_type]
, i.[type_desc]                                                        AS  [index_type_desc]
, nt.[pdw_node_id]                                                     AS  [pdw_node_id]
, pn.[type]                                                            AS  [pdw_node_type]
, pn.[name]                                                            AS  [pdw_node_name]
, di.name                                                              AS  [dist_name]
, di.position                                                          AS  [dist_position]
, nps.[partition_number]                                               AS  [partition_nmbr]
, nps.[reserved_page_count]                                            AS  [reserved_space_page_count]
, nps.[reserved_page_count] - nps.[used_page_count]                    AS  [unused_space_page_count]
, nps.[in_row_data_page_count]
    + nps.[row_overflow_used_page_count]
    + nps.[lob_used_page_count]                                        AS  [data_space_page_count]
, nps.[reserved_page_count]
 - (nps.[reserved_page_count] - nps.[used_page_count])
 - ([in_row_data_page_count]
         + [row_overflow_used_page_count]+[lob_used_page_count])       AS  [index_space_page_count]
, nps.[row_count]                                                      AS  [row_count]
from
    sys.schemas s
INNER JOIN sys.tables t
    ON s.[schema_id] = t.[schema_id]
INNER JOIN sys.indexes i
    ON  t.[object_id] = i.[object_id]
    AND i.[index_id] <= 1
INNER JOIN sys.pdw_table_distribution_properties tp
    ON t.[object_id] = tp.[object_id]
INNER JOIN sys.pdw_table_mappings tm
    ON t.[object_id] = tm.[object_id]
INNER JOIN sys.pdw_nodes_tables nt
    ON tm.[physical_name] = nt.[name]
INNER JOIN sys.dm_pdw_nodes pn
    ON  nt.[pdw_node_id] = pn.[pdw_node_id]
INNER JOIN sys.pdw_distributions di
    ON  nt.[distribution_id] = di.[distribution_id]
INNER JOIN sys.dm_pdw_nodes_db_partition_stats nps
    ON nt.[object_id] = nps.[object_id]
    AND nt.[pdw_node_id] = nps.[pdw_node_id]
    AND nt.[distribution_id] = nps.[distribution_id]
LEFT OUTER JOIN (select * from sys.pdw_column_distribution_properties where distribution_ordinal = 1) cdp
    ON t.[object_id] = cdp.[object_id]
LEFT OUTER JOIN sys.columns c
    ON cdp.[object_id] = c.[object_id]
    AND cdp.[column_id] = c.[column_id]
WHERE pn.[type] = 'COMPUTE'
)
, size
AS
(
SELECT
   [execution_time]
,  [database_name]
,  [schema_name]
,  [table_name]
,  [two_part_name]
,  [node_table_name]
,  [node_table_name_seq]
,  [distribution_policy_name]
,  [distribution_column]
,  [distribution_id]
,  [index_type]
,  [index_type_desc]
,  [pdw_node_id]
,  [pdw_node_type]
,  [pdw_node_name]
,  [dist_name]
,  [dist_position]
,  [partition_nmbr]
,  [reserved_space_page_count]
,  [unused_space_page_count]
,  [data_space_page_count]
,  [index_space_page_count]
,  [row_count]
,  ([reserved_space_page_count] * 8.0)                                 AS [reserved_space_KB]
,  ([reserved_space_page_count] * 8.0)/1000                            AS [reserved_space_MB]
,  ([reserved_space_page_count] * 8.0)/1000000                         AS [reserved_space_GB]
,  ([reserved_space_page_count] * 8.0)/1000000000                      AS [reserved_space_TB]
,  ([unused_space_page_count]   * 8.0)                                 AS [unused_space_KB]
,  ([unused_space_page_count]   * 8.0)/1000                            AS [unused_space_MB]
,  ([unused_space_page_count]   * 8.0)/1000000                         AS [unused_space_GB]
,  ([unused_space_page_count]   * 8.0)/1000000000                      AS [unused_space_TB]
,  ([data_space_page_count]     * 8.0)                                 AS [data_space_KB]
,  ([data_space_page_count]     * 8.0)/1000                            AS [data_space_MB]
,  ([data_space_page_count]     * 8.0)/1000000                         AS [data_space_GB]
,  ([data_space_page_count]     * 8.0)/1000000000                      AS [data_space_TB]
,  ([index_space_page_count]  * 8.0)                                   AS [index_space_KB]
,  ([index_space_page_count]  * 8.0)/1000                              AS [index_space_MB]
,  ([index_space_page_count]  * 8.0)/1000000                           AS [index_space_GB]
,  ([index_space_page_count]  * 8.0)/1000000000                        AS [index_space_TB]
FROM base
)
SELECT *
FROM size
;
```

### <a name="table-space-summary"></a>Táblázat területének összegzése

Ez a lekérdezés a sorokat és a szóközt adja vissza táblázat szerint.  Lehetővé teszi, hogy megtekintse, mely táblák a legnagyobb táblák, és hogy azok ciklikus multiplexelés, replikált vagy kivonattal vannak-e elterjesztve.  Kivonatoló eloszlású táblák esetén a lekérdezés a terjesztési oszlopot jeleníti meg.  

```sql
SELECT
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
,    COUNT(distinct partition_nmbr) as nbr_partitions
,    SUM(row_count)                 as table_row_count
,    SUM(reserved_space_GB)         as table_reserved_space_GB
,    SUM(data_space_GB)             as table_data_space_GB
,    SUM(index_space_GB)            as table_index_space_GB
,    SUM(unused_space_GB)           as table_unused_space_GB
FROM
    dbo.vTableSizes
GROUP BY
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### <a name="table-space-by-distribution-type"></a>Tábla területe eloszlási típus szerint

```sql
SELECT
     distribution_policy_name
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY distribution_policy_name
;
```

### <a name="table-space-by-index-type"></a>Tábla területe index típusa szerint

```sql
SELECT
     index_type_desc
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY index_type_desc
;
```

### <a name="distribution-space-summary"></a>Terjesztési terület összegzése

```sql
SELECT
    distribution_id
,    SUM(row_count)                as total_node_distribution_row_count
,    SUM(reserved_space_MB)        as total_node_distribution_reserved_space_MB
,    SUM(data_space_MB)            as total_node_distribution_data_space_MB
,    SUM(index_space_MB)           as total_node_distribution_index_space_MB
,    SUM(unused_space_MB)          as total_node_distribution_unused_space_MB
FROM dbo.vTableSizes
GROUP BY     distribution_id
ORDER BY    distribution_id
;
```

## <a name="next-steps"></a>Következő lépések

Miután létrehozta a táblákat a dedikált SQL-készlethez, a következő lépés az adat betöltése a táblába.  A betöltési oktatóanyagért lásd: [az adattöltés DEDIKÁLT SQL-készletbe](load-data-wideworldimportersdw.md).