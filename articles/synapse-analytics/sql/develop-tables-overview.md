---
title: Táblázatok kialakítása a szinapszis SQL használatával
description: Bevezetés a táblázatok a szinapszis SQL-ben való tervezéséhez.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 83c5595dc64b46e1c30f3c36866e0efbbd8d3c7f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101674127"
---
# <a name="design-tables-using-synapse-sql-in-azure-synapse-analytics"></a>Táblázatok kialakítása a szinapszis SQL használatával az Azure szinapszis Analyticsben

Ez a dokumentum a táblázatok dedikált SQL-készlettel és kiszolgáló nélküli SQL-készlettel való tervezésével kapcsolatos főbb fogalmakat tartalmazza.  

A [kiszolgáló nélküli SQL-készlet](on-demand-workspace-overview.md) egy lekérdezési szolgáltatás a adatközpontban található adaton keresztül. Nem rendelkezik helyi tárterülettel az adatfeldolgozáshoz. A [DEDIKÁLT SQL-készlet](best-practices-sql-pool.md) a szinapszis SQL használatakor kiépített analitikus erőforrások gyűjteményét jelöli. A dedikált SQL-készlet méretét az adattárház-egységek (DWU-EK) határozzák meg.

A következő táblázat felsorolja a dedikált SQL-készlethez és a kiszolgáló nélküli SQL-készlethez kapcsolódó témaköröket:

| Témakör                                                        | dedikált SQL-készlet | kiszolgáló nélküli SQL-készlet |
| ------------------------------------------------------------ | ------------------ | ----------------------- |
| [Táblázat kategóriájának meghatározása](#determine-table-category)        | Igen                | Nem                      |
| [Sémák nevei](#schema-names)                                | Igen                | Igen                     |
| [Táblák nevei](#table-names)                                  | Igen                | Nem                      |
| [Tábla megőrzése](#table-persistence)                      | Igen                | Nem                      |
| [Normál tábla](#regular-table)                              | Igen                | Nem                      |
| [Ideiglenes tábla](#temporary-table)                          | Igen                | Igen                     |
| [Külső tábla](#external-table)                            | Igen                | Igen                     |
| [Adattípusok](#data-types)                                    | Igen                | Igen                     |
| [Elosztott táblák](#distributed-tables)                    | Igen                | Nem                      |
| [Kivonat alapján elosztott táblák](#hash-distributed-tables)          | Igen                | Nem                      |
| [Replikált táblák](#replicated-tables)                      | Igen                | Nem                      |
| [Ciklikus időszeletelésű táblák](#round-robin-tables)                    | Igen                | Nem                      |
| [Gyakori terjesztési módszerek a táblákhoz](#common-distribution-methods-for-tables) | Igen                | Nem                      |
| [Partíciók](#partitions)                                    | Igen                | Igen                     |
| [Oszlopcentrikus indexek](#columnstore-indexes)                  | Igen                | Nem                      |
| [Statisztika](#statistics)                                    | Igen                | Igen                     |
| [Elsődleges kulcs és egyedi kulcs](#primary-key-and-unique-key)    | Igen                | Nem                      |
| [Táblázatok létrehozásához szükséges parancsok](#commands-for-creating-tables) | Igen                | Nem                      |
| [Forrásadatok igazítása az adatraktárhoz](#align-source-data-with-the-data-warehouse) | Igen                | Nem                      |
| [Nem támogatott táblázat-funkciók](#unsupported-table-features)    | Igen                | Nem                      |
| [Táblázat mérete – lekérdezések](#table-size-queries)                    | Igen                | Nem                      |

## <a name="determine-table-category"></a>Táblázat kategóriájának meghatározása

A [csillag sémák](https://en.wikipedia.org/wiki/Star_schema) a tény-és dimenziós táblákba rendezik az adathalmazokat. Bizonyos táblákat az integrációhoz vagy az előkészítéshez kell használni, mielőtt egy tény-vagy dimenzió táblázatba költöznek. A tábla tervezésekor döntse el, hogy a tábla adategysége tartalmaz-e egy tény-, dimenzió-vagy integrációs táblát. Ez a döntés a megfelelő tábla szerkezetét és eloszlását ismerteti.

- A **Fact Tables** olyan mennyiségi adatmennyiségeket tartalmaz, amelyeket általában egy tranzakciós rendszer generál, majd betölti az adattárházba. Egy kiskereskedelmi vállalat például minden nap értékesítési tranzakciókat hoz létre, majd betölti az adattárházat egy adatraktárba az elemzéshez.

- A **dimenzió táblák** olyan attribútum-adatértékeket tartalmaznak, amelyek változhatnak, de általában ritkán változnak. Az ügyfél neve és címe például egy dimenzió táblában tárolódik, és csak akkor frissül, ha az ügyfél profilja megváltozik. A nagyméretű egyedkapcsolatok méretének minimalizálásához az ügyfél nevét és a címet nem kell a Fact tábla minden sorában megadni. Ehelyett a tény tábla és a dimenzió tábla megoszthat egy ügyfél-azonosítót. Egy lekérdezés csatlakozhat a két táblához, és hozzárendelheti az ügyfél profilját és tranzakcióit.

- Az **integrációs táblázatok** helyet biztosítanak az adatintegráláshoz vagy az előkészítéshez. Létrehozhat egy integrációs táblázatot egy normál tábla, egy külső tábla vagy egy ideiglenes tábla használatával. Például betöltheti az adatok betöltését egy előkészítési táblába, elvégezheti az átmeneti adatok átalakítását, majd beszúrhatja az adatok egy éles táblába.

## <a name="schema-names"></a>Sémák nevei

A sémák a hasonló módon használt objektumok csoportosítására alkalmasak. A következő kód egy, az első világháború nevű [felhasználó által definiált sémát](/sql/t-sql/statements/create-schema-transact-sql?view=azure-sqldw-latest&preserve-view=true) hoz létre.

```sql
CREATE SCHEMA wwi;
```

## <a name="table-names"></a>Táblák nevei

Ha több adatbázist telepít át egy helyszíni megoldásból a dedikált SQL-készletbe, az ajánlott eljárás az összes tény, dimenzió és integrációs tábla egyetlen SQL Pool-sémába való áttelepítése. A [wideworldimportersdw adattárházat](/sql/samples/wide-world-importers-dw-database-catalog?view=azure-sqldw-latest&preserve-view=true) minta adattárházban lévő összes táblát tárolhatja például egy első világháború nevű sémán belül.

A táblák Szervezetének a dedikált SQL-készletben való megjelenítéséhez a Fact, a Dim és az int előtagot használhatja a táblázatok neveihez. Az alábbi táblázat a Wideworldimportersdw adattárházat sémájának és táblázatának egyes neveit mutatja be.  

| Wideworldimportersdw adattárházat táblázat  | Tábla típusa | dedikált SQL-készlet |
|:-----|:-----|:------|:-----|
| City | Dimenzió | WWI. DimCity |
| Sorrend | Fact | WWI. FactOrder |

## <a name="table-persistence"></a>Tábla megőrzése

A táblák az Azure Storage-ban, ideiglenesen az Azure Storage-ban vagy az adatraktáron kívüli adattárban tárolják az adattárolást.

### <a name="regular-table"></a>Normál tábla

A normál tábla az Azure Storage-ban tárolja az adattárház részét képező adatmennyiséget. A tábla és az adatkapcsolat megmarad, függetlenül attól, hogy nyitva van-e a munkamenet.  Az alábbi példa egy normál táblát hoz létre két oszloppal.

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Ideiglenes tábla

Egy ideiglenes tábla csak a munkamenet időtartama alatt létezik. Egy ideiglenes tábla használatával megakadályozhatja, hogy más felhasználók is lássák az ideiglenes eredményeket. Az ideiglenes táblák használata csökkenti a karbantartás szükségességét is.  Az ideiglenes táblák a helyi tárterületet használják, és a dedikált SQL-készletekben gyorsabb teljesítményt nyújthatnak.  

A kiszolgáló nélküli SQL-készlet támogatja az ideiglenes táblákat. A használata azonban korlátozott, mert egy ideiglenes táblából választhat, de nem csatlakoztatható a tárolóban található fájlokhoz.

További információ:  [ideiglenes táblák](develop-tables-temporary.md).

### <a name="external-table"></a>Külső tábla

A [külső táblák](develop-tables-external-tables.md) az Azure Storage-blobban vagy Azure Data Lake Storageban található adatterületekre mutatnak.

Adatok importálása külső táblákból dedikált SQL-készletekbe a [CREATE TABLE as Select](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) utasítás használatával. A betöltési oktatóanyagért lásd: az [adatok Azure Blob Storage-ból való betöltésének használata](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).

Kiszolgáló nélküli SQL-készlet esetén a [CETAS](develop-tables-cetas.md) használatával mentheti a lekérdezés eredményét egy külső táblába az Azure Storage-ban.

## <a name="data-types"></a>Adattípusok

A dedikált SQL-készlet a leggyakrabban használt adattípusokat támogatja. A támogatott adattípusok listáját a CREATE TABLE utasításban található [CREATE TABLE-hivatkozás adattípusai](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest#DataTypes&preserve-view=true) című témakörben tekintheti meg. Az adattípusok használatával kapcsolatos további információkért lásd az [adattípusokat](../sql/develop-tables-data-types.md).

## <a name="distributed-tables"></a>Elosztott táblák

A dedikált SQL-készlet alapvető funkciója, hogy miként tárolhatók és használhatók a táblázatok között a [disztribúciókban](../sql-data-warehouse/massively-parallel-processing-mpp-architecture.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#distributions).  A dedikált SQL-készlet három módszert támogat az adatterjesztéshez:

- Ciklikus időszeletelés (alapértelmezett)
- Kivonat
- Replikált

### <a name="hash-distributed-tables"></a>Kivonat alapján elosztott táblák

A kivonatok elosztott tábla a terjesztési oszlopban lévő érték alapján osztja el a sorokat. A kivonatoló elosztott tábla úgy van kialakítva, hogy nagy teljesítményű lekérdezéseket érjen el nagyméretű táblákon. A terjesztési oszlopok kiválasztásakor több tényezőt is figyelembe kell venni.

További információ: [tervezési útmutató az elosztott táblákhoz](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="replicated-tables"></a>Replikált táblák

A replikált táblák minden számítási csomóponton elérhetők a tábla teljes másolatával. A lekérdezések gyorsan futnak a replikált táblákon, mivel a replikált táblákon lévő illesztések nem igénylik az adatáthelyezést A replikáláshoz külön tárterület szükséges, azonban a nagyméretű táblák esetében nem praktikus.

További információ: [tervezési útmutató a replikált táblákhoz](../sql-data-warehouse/design-guidance-for-replicated-tables.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="round-robin-tables"></a>Ciklikus időszeletelésű táblák

A Round-Robin tábla egyenletesen osztja el a táblázat sorait az összes eloszlás között. A sorok eloszlása véletlenszerűen történik. Az adatbetöltések gyorsak.  A lekérdezések azonban több adatátvitelt is igényelhetnek, mint a többi terjesztési módszer.

További információ: [tervezési útmutató az elosztott táblákhoz](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="common-distribution-methods-for-tables"></a>Gyakori terjesztési módszerek a táblákhoz

A táblázat kategóriája gyakran meghatározza a táblázatok terjesztésének optimális lehetőségét.

| Táblázat kategóriája | Ajánlott terjesztési lehetőség |
|:---------------|:--------------------|
| Fact           | Használjon kivonatoló eloszlást a fürtözött oszlopcentrikus index használatával. A teljesítmény akkor javul, ha két kivonatoló tábla ugyanahhoz a terjesztési oszlophoz van csatlakoztatva. |
| Dimenzió      | Kisebb táblák esetében replikált használata. Ha a táblák túl nagyok az egyes számítási csomópontokon való tároláshoz, használja a kivonatoló eloszlást. |
| Előkészítés        | Ciklikus multiplexelés használata az előkészítési táblához. A CTAS terhelése gyors. Ha az adatgyűjtés az előkészítési táblában található, használja az INSERT... Ezzel a beállítással áthelyezheti az adatlemezeket az éles táblákba. |

## <a name="partitions"></a>Partíciók

A dedikált SQL-készletek esetében egy particionált tábla tárolja és végrehajtja a műveleteket a táblázat soraiban az adattartományok alapján. Egy tábla lehet például nap, hónap vagy év szerint particionálva. Javíthatja a lekérdezési teljesítményt a partíciók eltávolításán keresztül, ami korlátozza a lekérdezési vizsgálatát egy partíción belül.

Az adattárolást partíciós váltással is megtarthatja. Mivel egy dedikált SQL-készletben lévő adat már el van terjesztve, túl sok partíció lassítja a lekérdezések teljesítményét. További információ: [particionálási útmutató](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).  

> [!TIP]
> Ha a partíció nem üres táblázatos partícióra vált, érdemes lehet az [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?view=azure-sqldw-latest&preserve-view=true) utasítás TRUNCATE_TARGET kapcsolóját használni, ha a meglévő adatok csonkítva lesznek.

Az alábbi kód az átalakított napi adatkészletet egy SalesFact-partícióra vált, és felülírja a meglévőket.

```sql
ALTER TABLE SalesFact_DailyFinalLoad SWITCH PARTITION 256 TO SalesFact PARTITION 256 WITH (TRUNCATE_TARGET = ON);  
```

A kiszolgáló nélküli SQL-készletben korlátozhatja a lekérdezés által beolvasott fájlokat és mappákat (partíciókat). A particionálás elérési útja a filepath és a fileinfo függvények használatával támogatott a [tárolási fájlok lekérdezése](develop-storage-files-overview.md)című témakörben leírtak szerint. Az alábbi példa egy, a 2017-es évre vonatkozó adatokat tartalmazó mappát olvas be:

```sql
SELECT
    nyc.filepath(1) AS [year],
    payment_type,
    SUM(fare_amount) AS fare_total
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc
WHERE
    nyc.filepath(1) = 2017
GROUP BY
    nyc.filepath(1),
    payment_type
ORDER BY
    nyc.filepath(1),
    payment_type
```

## <a name="columnstore-indexes"></a>Oszlopcentrikus indexek

Alapértelmezés szerint a dedikált SQL-készlet fürtözött oszlopcentrikus indexként tárolja a táblát. Ez az adattárolási űrlap nagy méretű táblákon biztosítja a nagy adattömörítést és a lekérdezési teljesítményt.  A fürtözött oszlopcentrikus index általában a legjobb választás, de bizonyos esetekben egy fürtözött index vagy egy halom a megfelelő tárolási struktúra.  

> [!TIP]
> Egy halom tábla különösen hasznos lehet az átmeneti adatfájlok (például egy előkészítési tábla) betöltéséhez, amely egy végső táblába lett átalakítva.

A oszlopcentrikus-funkciók listáját a [oszlopcentrikus indexek újdonságai](/sql/relational-databases/indexes/columnstore-indexes-what-s-new?view=azure-sqldw-latest&preserve-view=true)című témakörben tekintheti meg. A oszlopcentrikus index teljesítményének növeléséhez tekintse meg [a sorcsoport minőségének maximalizálása a oszlopcentrikus indexekhez](../sql/data-load-columnstore-compression.md)című témakört.

## <a name="statistics"></a>Statisztika

A lekérdezés-optimalizáló oszlop szintű statisztikát használ, amikor létrehoz egy lekérdezést végrehajtó tervet. A lekérdezési teljesítmény javítása érdekében fontos, hogy az egyes oszlopokra vonatkozó statisztikát, különösen a lekérdezési illesztésekben használt oszlopokat használja. A szinapszis SQL támogatja a statisztikák automatikus létrehozását. 

A statisztikai frissítés nem történik meg automatikusan. A statisztikák frissítése jelentős számú sor hozzáadása vagy módosítása után. Például egy terhelés után frissítse a statisztikát. További információkat a [statisztikai útmutatóban](develop-tables-statistics.md) talál.

## <a name="primary-key-and-unique-key"></a>Elsődleges kulcs és egyedi kulcs

A dedikált SQL-készlet esetében az elsődleges kulcs csak akkor támogatott, ha a nem FÜRTÖZÖTT és a nem KÉNYSZERÍTett érték is használatban van.  Az egyedi megkötés csak akkor támogatott, ha nincs kikényszerítve.  További információkért lásd az SQL- [készlet táblázatának korlátozásait](../sql-data-warehouse/sql-data-warehouse-table-constraints.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) ismertető cikket.

## <a name="commands-for-creating-tables"></a>Táblázatok létrehozásához szükséges parancsok

A dedikált SQL-készlet esetében új üres táblaként hozhat létre táblát. Létrehozhat és fel is tölthet egy táblát a SELECT utasítás eredményeivel. A következő a T-SQL-parancsok egy tábla létrehozásához.

| T-SQL-utasítás | Leírás |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) | Egy üres táblát hoz létre a tábla összes oszlopának és beállításának definiálásával. |
| [KÜLSŐ TÁBLA LÉTREHOZÁSA](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Létrehoz egy külső táblát. A tábla definícióját a dedikált SQL-készlet tárolja. A tábla az Azure Blob Storage-ban vagy Azure Data Lake Storageban tárolódik. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) | Egy új táblát tölt ki egy SELECT utasítás eredményeivel. A táblázat oszlopai és adattípusai a SELECT utasítás eredményein alapulnak. Az adatok importálásához ez az utasítás külső táblából is kiválasztható. |
| [KÜLSŐ TÁBLA LÉTREHOZÁSA KIJELÖLÉSKÉNT](/sql/t-sql/statements/create-external-table-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Új külső tábla létrehozása egy SELECT utasítás eredményének külső helyre való exportálásával.  A hely vagy az Azure Blob Storage vagy a Azure Data Lake Storage. |

## <a name="align-source-data-with-the-data-warehouse"></a>Forrásadatok igazítása az adatraktárhoz

A dedikált SQL Pool-táblázatok az adatok másik adatforrásból való betöltésével vannak feltöltve. A sikeres betöltés eléréséhez a forrásadatok oszlopainak számát és adattípusait az adattárházban lévő táblázat definíciójának megfelelően kell összehangolni.

> [!NOTE]
> A táblázatok megtervezésének legnehezebb része lehet az igazítani kívánt adatmennyiség.

Ha az adatok több adattárból származnak, az adatok az adatraktárba is behelyezhetők, és egy integrációs táblában tárolhatók. Miután az adatok bekerültek az integrációs táblába, használhatja a dedikált SQL-készlet erejét az átalakítási műveletek megvalósításához. Az adatelőkészítést követően beillesztheti az éles táblákba.

## <a name="unsupported-table-features"></a>Nem támogatott táblázat-funkciók

A dedikált SQL-készlet a más adatbázisok által kínált tábla-funkciók számos, de nem az összes funkcióját támogatja.  Az alábbi lista a dedikált SQL-készletben nem támogatott táblázat-funkciókat mutatja be.

- Külső kulcs, ellenőrzési [táblázat megkötései](/sql/t-sql/statements/alter-table-table-constraint-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [Számított oszlopok](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [Indexelt nézetek](/sql/relational-databases/views/create-indexed-views?view=azure-sqldw-latest&preserve-view=true)
- [Sequence](/sql/t-sql/statements/create-sequence-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [Ritka oszlopok](/sql/relational-databases/tables/use-sparse-columns?view=azure-sqldw-latest&preserve-view=true)
- Helyettes kulcsok, megvalósítás [identitással](../sql-data-warehouse/sql-data-warehouse-tables-identity.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Szinonimák](/sql/t-sql/statements/create-synonym-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [Eseményindítók](/sql/t-sql/statements/create-trigger-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [Egyedi indexek](/sql/t-sql/statements/create-index-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [Felhasználó által definiált típusok](/sql/relational-databases/native-client/features/using-user-defined-types?view=azure-sqldw-latest&preserve-view=true)

## <a name="table-size-queries"></a>Táblázat mérete – lekérdezések

A dedikált SQL-készletben egy egyszerű módja annak, hogy azonosítsa az egyes 60-disztribúciókban a táblák által felhasznált helyet és sorokat, hogy a [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?view=azure-sqldw-latest&preserve-view=true)használja.

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Ne feledje, hogy a DBCC parancsok használata meglehetősen korlátozó lehet.  A dinamikus felügyeleti nézetek (DMV) több részletet mutatnak, mint a DBCC parancsai. Először hozza létre az alábbi nézetet.

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

Ez a lekérdezés a sorokat és a szóközt adja vissza táblázat szerint.  A tábla területének összegzése lehetővé teszi, hogy a legnagyobb táblák közül melyik táblákat kell megtekinteni. Azt is láthatja, hogy a ciklikus multiplexelés, a replikált vagy a hash eloszlású.  Kivonatoló eloszlású táblák esetén a lekérdezés a terjesztési oszlopot jeleníti meg.  

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

Miután létrehozta az adattárházhoz tartozó táblákat, a következő lépés az adatai betöltése a táblába.  Betöltési oktatóanyag: az [adat betöltése DEDIKÁLT SQL-készletbe](../sql-data-warehouse/load-data-wideworldimportersdw.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#load-the-data-into-sql-pool).