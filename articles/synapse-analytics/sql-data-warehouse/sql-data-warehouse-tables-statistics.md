---
title: Statisztikák létrehozása és frissítése a táblákon
description: Javaslatok és példák a lekérdezés-optimalizálási statisztikák létrehozására és frissítésére a dedikált SQL-készlet tábláiban.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/09/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 3ade41c51cbb8065734e8957cfc8b9f0c22b2df3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98673366"
---
# <a name="table-statistics-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>A dedikált SQL-készlet táblázatos statisztikái az Azure szinapszis Analyticsben

Ez a cikk ajánlásokat és példákat tartalmaz a lekérdezés-optimalizálási statisztikák létrehozására és frissítésére a dedikált SQL-készlet tábláiban.

## <a name="why-use-statistics"></a>Miért használja a statisztikát?

Minél több dedikált SQL-készlet ismeri az adatait, annál gyorsabban végezhet lekérdezéseket. Miután betöltötte az adatokat a dedikált SQL-készletbe, az adatokra vonatkozó statisztikák gyűjtése az egyik legfontosabb dolog, amit a lekérdezések optimalizálására tehet.

A dedikált SQL Pool lekérdezés-optimalizáló egy költséghatékony optimalizáló. Összehasonlítja a különböző lekérdezési csomagok költségeit, majd kiválasztja a legalacsonyabb díjszabású csomagot. A legtöbb esetben azt a tervet választja, amely a leggyorsabb végrehajtást fogja végrehajtani.

Ha például az optimalizáló becslése szerint a lekérdezés szűrésének dátuma egy sort ad vissza, akkor egy csomagot fog kiválasztani. Ha úgy becsüli, hogy a kijelölt dátum 1 000 000 sort ad vissza, akkor egy másik csomagot ad vissza.

## <a name="automatic-creation-of-statistic"></a>Statisztika automatikus létrehozása

Ha az adatbázis AUTO_CREATE_STATISTICS lehetőség be van kapcsolva, a dedikált SQL-készlet elemzi a bejövő felhasználói lekérdezéseket a hiányzó statisztikákhoz.

Ha a statisztikai adatok hiányoznak, a lekérdezés-optimalizáló a lekérdezési predikátum egyes oszlopaira vonatkozó statisztikát hoz létre, vagy összekapcsolási feltételt biztosít a lekérdezési tervhez tartozó sarkalatos becslések javítására

> [!NOTE]
> A statisztikák automatikus létrehozása jelenleg alapértelmezés szerint be van kapcsolva.

A következő parancs futtatásával megtekintheti, hogy a dedikált SQL-készlet AUTO_CREATE_STATISTICS konfigurálva van-e:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Ha a dedikált SQL-készlet nem rendelkezik AUTO_CREATE_STATISTICS konfigurálva, javasoljuk, hogy engedélyezze ezt a tulajdonságot a következő parancs futtatásával:

```sql
ALTER DATABASE <yourdatawarehousename>
SET AUTO_CREATE_STATISTICS ON
```

Ezek az utasítások a statisztikák automatikus létrehozását fogják elindítani:

- SELECT
- BESZÚRÁS – KIJELÖLÉS
- CTAS
- UPDATE
- DELETE
- MAGYARÁZAT a csatlakozás vagy a predikátum jelenlétének észlelésekor

> [!NOTE]
> A statisztikák automatikus létrehozása nem ideiglenes vagy külső táblákon hozható létre.

A statisztikák automatikus létrehozása szinkron módon történik, így előfordulhat, hogy az oszlopok hiányoznak a statisztikákból. Az egyetlen oszlop statisztikáinak létrehozási ideje a tábla méretétől függ.

A mérhető teljesítmény elkerülése érdekében először létre kell hoznia a statisztikákat úgy, hogy a szolgáltatás profilkészítése előtt végrehajtja a teljesítményteszt munkaterhelését.

> [!NOTE]
> A statisztikák létrehozása [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) egy másik felhasználói környezet alatt lesz naplózva.

Az automatikus statisztikák létrehozásakor a rendszer a (z) _WA_Sys_<8 jegyű oszlop azonosítóját hexadecimális>_<8 számjegyű tábla azonosítóját hexadecimális>. A már létrehozott statisztikákat a [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) parancs futtatásával tekintheti meg:

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

A table_name a megjelenítendő statisztikát tartalmazó tábla neve. Ez a tábla nem lehet külső tábla. A cél annak a célként megadott indexnek, statisztikának vagy oszlopnak a neve, amelynek a statisztikai adatait meg szeretné megjeleníteni.

## <a name="update-statistics"></a>Frissítési statisztika

Az egyik legjobb megoldás az, ha naponta frissíti a dátum oszlopokat az új dátumok hozzáadásakor. Minden alkalommal, amikor új sorok töltődnek be a dedikált SQL-készletbe, új betöltési dátumok vagy tranzakciós dátumok lesznek hozzáadva. Ezek a hozzáadások módosítják az adateloszlást, és a statisztikákat elavultan teszik elérhetővé.

Előfordulhat, hogy az ügyfél tábla ország/régió oszlopának statisztikáját soha nem kell frissíteni, mert az értékek eloszlása nem változik. Feltételezve, hogy a terjesztés állandó az ügyfelek között, és új sorokat ad hozzá a táblázat variációhoz, nem fogja módosítani az adateloszlást.

Ha azonban a dedikált SQL-készlet csak egy országot/régiót tartalmaz, és egy új országból/régióból származó adatokat hoz, így több országból vagy régióból származó adatokat tárol, akkor frissítenie kell az ország/régió oszlop statisztikáit.

A következő javaslatok frissítik a statisztikát:

|||
|-|-|
| **A statisztika frissítéseinek gyakorisága**  | Konzervatív: naponta </br> Az adatai betöltése vagy átalakítása után |
| **Mintavételezés** |  Kevesebb mint 1 000 000 000 sor, használja az alapértelmezett mintavételezést (20 százalék). </br> A több mint 1 000 000 000 sorral a mintavételezést két százalékkal kell használni. |

Az egyik első kérdés a lekérdezés hibaelhárításakor: **"a statisztikák naprakészek?"**

Ezt a kérdést nem lehet megválaszolni az adatkor alapján. Előfordulhat, hogy a naprakész statisztikai objektumok elavultak, ha a mögöttes adatok nem módosultak. Ha a sorok száma lényegesen módosult, vagy jelentős változás történik egy oszlop értékeinek eloszlásában, *akkor* itt az ideje, hogy frissítse a statisztikát. 

Nincs dinamikus felügyeleti nézet annak megállapítására, hogy a táblázatban lévő adatok módosultak-e a legutóbbi statisztika frissítése óta.  A következő két lekérdezés segíthet megállapítani, hogy a statisztikák elavultak-e.

**1. lekérdezés:**  Megtudhatja, hogy miben különbözik a sorok száma a statisztikában (**stats_row_count**) és a tényleges sorok száma (**actual_row_count**). 

```sql
select 
objIdsWithStats.[object_id], 
actualRowCounts.[schema], 
actualRowCounts.logical_table_name, 
statsRowCounts.stats_row_count, 
actualRowCounts.actual_row_count,
row_count_difference = CASE
    WHEN actualRowCounts.actual_row_count >= statsRowCounts.stats_row_count THEN actualRowCounts.actual_row_count - statsRowCounts.stats_row_count
    ELSE statsRowCounts.stats_row_count - actualRowCounts.actual_row_count
END,
percent_deviation_from_actual = CASE
    WHEN actualRowCounts.actual_row_count = 0 THEN statsRowCounts.stats_row_count
    WHEN statsRowCounts.stats_row_count = 0 THEN actualRowCounts.actual_row_count
    WHEN actualRowCounts.actual_row_count >= statsRowCounts.stats_row_count THEN CONVERT(NUMERIC(18, 0), CONVERT(NUMERIC(18, 2), (actualRowCounts.actual_row_count - statsRowCounts.stats_row_count)) / CONVERT(NUMERIC(18, 2), actualRowCounts.actual_row_count) * 100)
    ELSE CONVERT(NUMERIC(18, 0), CONVERT(NUMERIC(18, 2), (statsRowCounts.stats_row_count - actualRowCounts.actual_row_count)) / CONVERT(NUMERIC(18, 2), actualRowCounts.actual_row_count) * 100)
END
from
(
    select distinct object_id from sys.stats where stats_id > 1
) objIdsWithStats
left join
(
    select object_id, sum(rows) as stats_row_count from sys.partitions group by object_id
) statsRowCounts
on objIdsWithStats.object_id = statsRowCounts.object_id 
left join
(
    SELECT sm.name [schema] ,
    tb.name logical_table_name ,
    tb.object_id object_id ,
    SUM(rg.row_count) actual_row_count
    FROM sys.schemas sm
    INNER JOIN sys.tables tb ON sm.schema_id = tb.schema_id
    INNER JOIN sys.pdw_table_mappings mp ON tb.object_id = mp.object_id
    INNER JOIN sys.pdw_nodes_tables nt ON nt.name = mp.physical_name
    INNER JOIN sys.dm_pdw_nodes_db_partition_stats rg
    ON rg.object_id = nt.object_id
    AND rg.pdw_node_id = nt.pdw_node_id
    AND rg.distribution_id = nt.distribution_id
    WHERE 1 = 1
    GROUP BY sm.name, tb.name, tb.object_id
) actualRowCounts
on objIdsWithStats.object_id = actualRowCounts.object_id

```

**2. lekérdezés:** Tekintse meg a statisztikák korát úgy, hogy az utolsó alkalommal ellenőrzi, hogy a statisztikáik frissültek-e az egyes táblákon. 

> [!NOTE]
> Ha egy oszlop értékeinek eloszlásában jelentős változások történnek, akkor frissítse a statisztikát, függetlenül attól, hogy mikor frissítették őket.

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

A dedikált **SQL-** készletben például általában gyakori statisztikai frissítésekre van szükség. Minden alkalommal, amikor új sorok töltődnek be a dedikált SQL-készletbe, új betöltési dátumok vagy tranzakciós dátumok lesznek hozzáadva. Ezek a hozzáadások módosítják az adateloszlást, és a statisztikákat elavultan teszik elérhetővé.

Ezzel ellentétben előfordulhat, hogy az ügyfél táblában nem kell frissíteni a nemek szerinti oszlop statisztikáit. Feltételezve, hogy a terjesztés állandó az ügyfelek között, és új sorokat ad hozzá a táblázat variációhoz, nem fogja módosítani az adateloszlást.

Ha a dedikált SQL-készlet csak egyetlen nemet tartalmaz, és az új követelmények több nemet eredményeznek, akkor frissítenie kell a nemek oszlop statisztikáit.

További információ: általános útmutató a [statisztikákhoz](/sql/relational-databases/statistics/statistics?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="implementing-statistics-management"></a>A statisztikák kezelésének megvalósítása

Gyakran érdemes kiterjeszteni az betöltési folyamatot annak biztosítására, hogy a statisztikák a terhelés végén frissüljenek, hogy elkerülje/csökkentse az egyidejű lekérdezések közötti blokkolást vagy erőforrás-tartalmat.  

Az adatok betöltése akkor történik meg, amikor a táblázatok leggyakrabban változnak a méretük és/vagy az értékek eloszlása. Az betöltés olyan logikai hely, amely bizonyos felügyeleti folyamatokat implementál.

A statisztikák frissítéséhez a következő irányadó elveket kell megadnia:

- Győződjön meg arról, hogy minden betöltött táblában legalább egy statisztikai objektum frissült. Ezzel frissíti a tábla méretét (a sorok számát és a lapok számát) a statisztikai frissítés részeként.
- A JOIN, a GROUP BY, a ORDER BY és a DISTINCT záradékban részt vevő oszlopokra koncentrálhat.
- Érdemes lehet frissíteni a "növekvő kulcs" oszlopokat, például a tranzakció dátumát gyakrabban, mert ezek az értékek nem szerepelnek a statisztikai hisztogramon.
- Érdemes lehet ritkábban frissíteni a statikus terjesztési oszlopokat.
- Ne feledje, hogy minden egyes statisztikai objektum sorba van frissítve. A megvalósítás egyszerűen `UPDATE STATISTICS <TABLE_NAME>` nem mindig ideális, különösen a sok statisztikai objektummal rendelkező széles táblák esetében.

További információ: a [kardinális becslése](/sql/relational-databases/performance/cardinality-estimation-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="examples-create-statistics"></a>Példák: statisztikák létrehozása

Ezek a példák azt mutatják be, hogyan használhatók a különböző beállítások a statisztikák létrehozásához. Az egyes oszlopokhoz használt beállítások az adatok jellemzőitől és az oszlopnak a lekérdezésekben való használatának módjától függnek.

### <a name="create-single-column-statistics-with-default-options"></a>Egyoszlopos statisztikák létrehozása alapértelmezett beállításokkal

Egy oszlop statisztikáinak létrehozásához adja meg a statisztikai objektum nevét és az oszlop nevét.

Ez a szintaxis az összes alapértelmezett beállítást használja. Alapértelmezés szerint a tábla **20 százalékát** kell mintát venni a statisztikák létrehozásakor.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Például:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="create-single-column-statistics-by-examining-every-row"></a>Egyoszlopos statisztikák létrehozása az egyes sorok vizsgálatával

A legtöbb esetben a 20%-os alapértelmezett mintavételi sebesség elegendő. Azonban beállíthatja a mintavételezési sebességet.

A teljes táblázat mintavételezéséhez használja a következő szintaxist:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Például:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Egyoszlopos statisztikák létrehozása a minta méretének megadásával

Alternatív megoldásként megadhatja a minta méretét százalékban:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Egyoszlopos statisztikák létrehozása csak néhány sorban

A tábla sorainak egy részén is létrehozhat statisztikát. Ezt szűrt statisztika nevezzük.

Használhatja például a szűrt statisztikát, ha egy nagyméretű particionált tábla adott partíciójának lekérdezését tervezi. Ha csak a partíciós értékekre vonatkozó statisztikát hoz létre, a statisztikák pontossága javulni fog, és így javul a lekérdezési teljesítmény.

Ez a példa az értékek tartományán alapuló statisztikát hoz létre. Az értékek könnyen meghatározhatók úgy, hogy egyezzenek egy partícióban lévő értékek tartományával.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Ahhoz, hogy a lekérdezés optimalizálva fontolják meg a szűrt statisztikák használatát, ha az elosztott lekérdezési tervet választja, a lekérdezésnek a statisztikai objektum definíciójában kell lennie. Az előző példában a lekérdezés WHERE záradékának meg kell adnia a 2000101 és a 20001231 közötti col1 értékeket.

### <a name="create-single-column-statistics-with-all-the-options"></a>Egyoszlopos statisztikák létrehozása az összes lehetőséggel

A beállításokat együtt is egyesítheti. Az alábbi példa egy szűrt statisztikai objektumot hoz létre egyéni minta mérettel:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

A teljes referenciáért lásd: [statisztikák létrehozása](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

### <a name="create-multi-column-statistics"></a>Több oszlopból álló statisztikák létrehozása

Több oszlopból álló statisztikai objektum létrehozásához használja az előző példákat, de további oszlopokat is meg kell adnia.

> [!NOTE]
> A lekérdezési eredményben szereplő sorok számának becsléséhez használt hisztogram csak a statisztikai objektum definíciójában felsorolt első oszlop esetében érhető el.

Ebben a példában a hisztogram a *termék \_ kategóriájában* van. Az oszlopokra vonatkozó statisztikákat a *termék \_ kategóriája* és a *termék \_ sub_category* alapján számítjuk ki:

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Mivel a *termék \_ kategóriája* és a *termék \_ \_ alkategóriája* közötti korreláció áll fenn, a többoszlopos statisztikai objektum akkor lehet hasznos, ha ezek az oszlopok egyszerre érhetők el.

### <a name="create-statistics-on-all-columns-in-a-table"></a>Statisztikák létrehozása egy tábla összes oszlopához

A statisztikák létrehozásának egyik módja az, hogy a tábla létrehozása után HOZZon létre STATISZTIKÁs parancsokat:

```sql
CREATE TABLE dbo.table1
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-sql-pool"></a>Tárolt eljárás használata statisztikák létrehozásához az SQL-készlet összes oszlopán

A dedikált SQL-készlet nem rendelkezik olyan rendszerszintű tárolt eljárással, amely egyenértékű a SQL Server sp_create_statsával. Ez a tárolt eljárás egyetlen oszlopból álló statisztikai objektumot hoz létre egy olyan SQL-készlet minden oszlopán, amely még nem rendelkezik statisztikával.

Az alábbi példa segítséget nyújt az SQL-készlet kialakításának megkezdéséhez. Nyugodtan alkalmazkodhat az igényeihez.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type IS NULL
BEGIN
    SET @create_type = 1;
END;

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
    DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN    sys.[external_tables] e    ON    e.[object_id]        = t.[object_id]
WHERE       l.[object_id] IS NULL
AND            e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+CONVERT(varchar(4),@sample_pct)+' PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

Ha statisztikát szeretne létrehozni a tábla összes oszlopához az alapértelmezett beállításokkal, hajtsa végre a tárolt eljárást.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```

Ha fullscan használatával szeretne statisztikát létrehozni a tábla összes oszlopáról, hívja meg ezt az eljárást.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

A táblázat összes oszlopához tartozó mintavételes statisztikák létrehozásához írja be a 3 értéket, a minta százalékot. Ez az eljárás 20 százalékos mintavételezési sebességet használ.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

## <a name="examples-update-statistics"></a>Példák: frissítési statisztikák

A statisztikák frissítéséhez a következőket teheti:

- Frissítsen egy statisztikai objektumot. Adja meg a frissíteni kívánt statisztikai objektum nevét.
- Egy tábla összes statisztikai objektumának frissítése. Adja meg a tábla nevét egy adott statisztikai objektum helyett.

### <a name="update-one-specific-statistics-object"></a>Egy adott statisztikai objektum frissítése

A következő szintaxissal frissíthet egy adott statisztikai objektumot:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Például:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Adott statisztikai objektumok frissítésével minimálisra csökkentheti a statisztikák kezeléséhez szükséges időt és erőforrásokat. Ehhez némi gondolkodásra van szükség, hogy kiválassza a frissítendő legjobb statisztikai objektumokat.

### <a name="update-all-statistics-on-a-table"></a>Egy tábla összes statisztikájának frissítése

Egy egyszerű módszer a tábla összes statisztikai objektumának frissítésére:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Például:

```sql
UPDATE STATISTICS dbo.table1;
```

A frissítés STATISZTIKÁi utasítás egyszerűen használható. Ne feledje, hogy a táblázat *összes* statisztikáját frissíti, ezért a szükségesnél több munkát is végrehajthat. Ha a teljesítmény nem jelent problémát, ez a legegyszerűbb és legteljesebb módszer annak biztosítására, hogy a statisztikák naprakészek legyenek.

> [!NOTE]
> Egy tábla összes statisztikájának frissítésekor a dedikált SQL-készlet ellenőrzi, hogy az egyes statisztikai objektumok táblázatát kell-e felvenni. Ha a tábla nagyméretű, és sok oszlopot és számos statisztikát tartalmaz, akkor lehet, hogy hatékonyabban kell frissíteni az egyes statisztikákat igény szerint.

Egy eljárás végrehajtásához `UPDATE STATISTICS` tekintse meg az [ideiglenes táblákat](sql-data-warehouse-tables-temporary.md). A megvalósítási módszer némileg eltér az előző `CREATE STATISTICS` eljárástól, de az eredmény ugyanaz.

A teljes szintaxist a [statisztika frissítése](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)című részben tekintheti meg.

## <a name="statistics-metadata"></a>Statisztikai metaadatok

A statisztikával kapcsolatos információk megkereséséhez számos rendszernézet és függvény használható. Megtekintheti például, hogy egy statisztikai objektum elavult-e, ha a stats-Date függvénnyel megtekinti a statisztikák utolsó létrehozását vagy frissítését.

### <a name="catalog-views-for-statistics"></a>Statisztikai katalógus nézetei

Ezek a rendszernézetek a statisztikával kapcsolatos információkat tartalmaznak:

| Katalógus nézet | Description |
|:--- |:--- |
| [sys. Columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Egy sor az egyes oszlopokhoz. |
| [sys. Objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Egy sor az adatbázis minden objektumához. |
| [sys. schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Az adatbázis minden sémájának egy sora. |
| [sys. stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Egy sor az egyes statisztikai objektumokhoz. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Egy sor a statisztikai objektum minden oszlopához. Hivatkozásokat tartalmaz a sys. Columns fájlra. |
| [sys. Tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Az egyes táblák egy sora (beleértve a külső táblákat is). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Minden adattípus egy sora. |

### <a name="system-functions-for-statistics"></a>A statisztikák rendszerfunkciói

Ezek a rendszerfunkciók a statisztikákkal való munkavégzéshez hasznosak:

| System függvény | Description |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |A statisztikai objektum utolsó frissítésének dátuma. |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Összegző szint és részletes információk az értékek eloszlásáról a statisztikai objektum által értelmezett módon. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Statisztikai oszlopok és függvények egyesítése egyetlen nézetben

Ez a nézet olyan oszlopokat helyez el, amelyek a STATS_DATE () függvény statisztikáinak és eredményeinek összekapcsolására vonatkoznak.

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_definition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

## <a name="dbcc-show_statistics-examples"></a>DBCC SHOW_STATISTICS () példák

A DBCC SHOW_STATISTICS () megjeleníti a statisztikai objektumon belül tárolt adatokat. Ezek az adatkészletek három részből állnak:

- Fejléc
- Sűrűség vektor
- Hisztogram

A statisztikával kapcsolatos fejléc-metaadatok. A hisztogram megjeleníti az értékek eloszlását a statisztikai objektum első kulcs oszlopában. A sűrűség vektor az oszlopok közötti korrelációt méri.

> [!NOTE]
> A dedikált SQL-készlet a statisztikai objektum bármely adatával kiszámítja a kardinális becsléseket.

### <a name="show-header-density-and-histogram"></a>Fejléc, sűrűség és hisztogram megjelenítése

Ez az egyszerű példa egy statisztikai objektum mindhárom részét megjeleníti:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Például:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>DBCC SHOW_STATISTICS () egy vagy több részének megjelenítése

Ha csak bizonyos részeket szeretne megtekinteni, használja a `WITH` záradékot, és határozza meg, hogy mely részeket szeretné megtekinteni:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Például:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-show_statistics-differences"></a>DBCC SHOW_STATISTICS () különbségek

A DBCC SHOW_STATISTICS () a SQL Serverhoz képest a dedikált SQL-készletben szigorúan implementálva van:

- A nem dokumentált funkciók nem támogatottak.
- A Stats_stream nem használható.
- Nem lehet csatlakozni az eredményekhez a statisztikai adatok adott részhalmaza számára. STAT_HEADER CSATLAKOZHAT például DENSITY_VECTORhoz.
- A NO_INFOMSGS nem állítható be üzenet letiltására.
- A statisztikai nevekhez tartozó szögletes zárójelek nem használhatók.
- A statisztikai objektumok azonosítására szolgáló oszlopnevek nem használhatók.
- A 2767-es egyéni hiba nem támogatott.

## <a name="next-steps"></a>Következő lépések

A lekérdezési teljesítmény javítása érdekében lásd: [a munkaterhelés figyelése](sql-data-warehouse-manage-monitor.md)
