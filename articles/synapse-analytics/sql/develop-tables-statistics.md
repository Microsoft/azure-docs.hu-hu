---
title: Statisztikák létrehozása és frissítése az Azure szinapszis SQL-erőforrásaival
description: Javaslatok és példák a lekérdezés-optimalizálási statisztikák létrehozására és frissítésére a szinapszis SQL-ben.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/19/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 450a089c6cc1c77ac26cb0aa339277d5c49b41c8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104594784"
---
# <a name="statistics-in-synapse-sql"></a>Statisztika a szinapszis SQL-ben

Ez a cikk ajánlásokat és példákat tartalmaz a lekérdezés-optimalizálási statisztikák létrehozására és frissítésére a szinapszis SQL-erőforrások használatával: dedikált SQL-készlet és kiszolgáló nélküli SQL-készlet.

## <a name="statistics-in-dedicated-sql-pool"></a>Statisztika a dedikált SQL-készletben

### <a name="why-use-statistics"></a>Miért használja a statisztikát?

Minél több dedikált SQL-készlet ismeri az adatait, annál gyorsabban végezhet lekérdezéseket. Miután betöltötte az adatokat egy dedikált SQL-készletbe, az adatok statisztikáinak összegyűjtése az egyik legfontosabb dolog, amit a lekérdezés optimalizálásához használhat.  

A dedikált SQL Pool lekérdezés-optimalizáló egy költséghatékony optimalizáló. Összehasonlítja a különböző lekérdezési csomagok költségeit, majd kiválasztja a legalacsonyabb díjszabású csomagot. A legtöbb esetben azt a tervet választja, amely a leggyorsabb végrehajtást fogja végrehajtani.

Ha például az optimalizáló becslése szerint a lekérdezés szűrésének dátuma egy sort ad vissza, akkor az egy csomagot fog választani. Ha úgy becsüli, hogy a kijelölt dátum 1 000 000 sort ad vissza, akkor egy másik csomagot ad vissza.

### <a name="automatic-creation-of-statistics"></a>Statisztikák automatikus létrehozása

A dedikált SQL Pool motor elemzi a hiányzó statisztikai adatok bejövő felhasználói lekérdezéseit, amikor az adatbázis AUTO_CREATE_STATISTICS beállítás értéke: `ON` .  Ha hiányoznak a statisztikák, a lekérdezés-optimalizáló a lekérdezési predikátumban vagy a csatlakozás feltételben lévő egyes oszlopokra vonatkozó statisztikát hoz létre. 

Ez a függvény a lekérdezési tervhez tartozó kardinális becslések javítására szolgál.

> [!IMPORTANT]
> A statisztikák automatikus létrehozása jelenleg alapértelmezés szerint be van kapcsolva.

A következő parancs futtatásával megtekintheti, hogy az adattárház AUTO_CREATE_STATISTICS konfigurálva van-e:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Ha az adattárház nem rendelkezik AUTO_CREATE_STATISTICS engedélyezve, javasoljuk, hogy engedélyezze ezt a tulajdonságot a következő parancs futtatásával:

```sql
ALTER DATABASE <yourdatawarehousename>
SET AUTO_CREATE_STATISTICS ON
```

Ezek az utasítások a statisztikák automatikus létrehozását fogják kiváltani:

- SELECT
- BESZÚRÁS – KIJELÖLÉS
- CTAS
- UPDATE
- DELETE
- MAGYARÁZAT a csatlakozás vagy a predikátum jelenlétének észlelésekor

> [!NOTE]
> A statisztikák automatikus létrehozása nem ideiglenes vagy külső táblákon jön létre.

A statisztikák automatikus létrehozása szinkron módon történik. Így előfordulhat, hogy némileg csökkentett teljesítményű lekérdezési teljesítményt von maga után, ha az oszlopok hiányoznak a statisztikák. Az egyetlen oszlop statisztikáinak létrehozási ideje a tábla méretétől függ.

A mérhető teljesítmény elkerülése érdekében először létre kell hoznia a statisztikákat úgy, hogy a szolgáltatás profilkészítése előtt végrehajtja a teljesítményteszt munkaterhelését.

> [!NOTE]
> A statisztikák létrehozása a [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true) egy másik felhasználói környezetben történik.

Az automatikus statisztikák létrehozásakor a rendszer az alábbiakat használja: _WA_Sys_<8 jegyű oszlop azonosítóját hexadecimális>_<8 számjegyű tábla azonosítóját hexadecimális>. A már létrehozott statisztikákat a [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true) parancs futtatásával tekintheti meg:

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

A table_name a megjelenítendő statisztikát tartalmazó tábla neve, amely nem lehet külső tábla. A cél annak a célként megadott indexnek, statisztikának vagy oszlopnak a neve, amelynek a statisztikai adatait meg szeretné megjeleníteni.

### <a name="update-statistics"></a>Frissítési statisztika

Az egyik legjobb megoldás az, ha naponta frissíti a dátum oszlopokat az új dátumok hozzáadásakor. Minden alkalommal, amikor új sorok töltődnek be az adatraktárba, új betöltési dátumok vagy tranzakciós dátumok lesznek hozzáadva. Ezek a hozzáadások módosítják az adateloszlást, és a statisztikákat elavultan teszik elérhetővé.

Előfordulhat, hogy az ügyfél tábla ország vagy régió oszlopának statisztikáját soha nem kell frissíteni, mert az értékek eloszlása általában nem változik. Feltételezve, hogy a terjesztés állandó az ügyfelek között, és új sorokat ad hozzá a táblázat variációhoz, nem fogja módosítani az adateloszlást.

Ha azonban az adattárház csak egy országot vagy régiót tartalmaz, és az adatokat egy új országból vagy régióból hozza, akkor frissítenie kell az ország vagy régió oszlop statisztikáit.

A következő javaslatok frissülnek a statisztikák frissítéséhez:

|Típus|Ajánlás|
|-|-|
| **A statisztika frissítéseinek gyakorisága**  | Konzervatív: naponta </br> Az adatai betöltése vagy átalakítása után |
| **Mintavételezés** |  Kevesebb mint 1 000 000 000 sor, használja az alapértelmezett mintavételezést (20 százalék). </br> A több mint 1 000 000 000 sorral a mintavételezést két százalékkal kell használni. |

### <a name="determine-last-statistics-update"></a>Utolsó statisztikai frissítés meghatározása

Az egyik első kérdés a lekérdezés hibaelhárításakor: **"a statisztikák naprakészek?"**

Ezt a kérdést nem lehet megválaszolni az adatkor alapján. Előfordulhat, hogy a naprakész statisztikai objektumok elavultak, ha a mögöttes adatok nem módosultak. Ha a sorok száma lényegesen módosult, vagy egy oszlop értékeinek eloszlása megváltozik, *akkor* itt az ideje, hogy frissítse a statisztikát.

Nincs elérhető dinamikus felügyeleti nézet annak megállapításához, hogy a táblázaton belüli adatok módosultak-e a legutóbbi statisztika frissítése óta. A statisztikák korának ismerete a kép egy részének megadását is lehetővé teszi. 

A következő lekérdezéssel megállapíthatja, hogy az egyes táblákon mikor frissítették utoljára a statisztikát.

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

Az adatraktárban lévő **dátumok oszlopai** például általában gyakori statisztikai frissítésekre van szükség. Minden alkalommal, amikor új sorok töltődnek be az adatraktárba, új betöltési dátumok vagy tranzakciós dátumok lesznek hozzáadva. Ezek a hozzáadások módosítják az adateloszlást, és a statisztikákat elavultan teszik elérhetővé.

Előfordulhat, hogy az ügyfél táblában nem kell frissíteni a nemek oszlopának statisztikáit. Feltételezve, hogy a terjesztés állandó az ügyfelek között, és új sorokat ad hozzá a táblázat variációhoz, nem fogja módosítani az adateloszlást.

Ha azonban az adattárház csak egyetlen nemet tartalmaz, és egy új követelmény több nemet eredményez, akkor frissítenie kell a nemek oszlop statisztikáit. 

További információkért tekintse át a [statisztikai](/sql/relational-databases/statistics/statistics) adatokat ismertető cikket.

### <a name="implement-statistics-management"></a>A statisztikák kezelésének megvalósítása

Gyakran érdemes kiterjeszteni az betöltési folyamatot annak érdekében, hogy a statisztikák a terhelés végén frissüljenek. Az adatok betöltése akkor történik meg, amikor a táblázatok leggyakrabban változnak a méretük, az értékek eloszlása vagy mindkettő. Így a betöltési folyamat olyan logikai hely, amely bizonyos felügyeleti folyamatokat implementál.

A következő irányadó elveket kell megadnia a statisztikák frissítéséhez a betöltési folyamat során:

- Győződjön meg arról, hogy minden betöltött táblában legalább egy statisztikai objektum frissült. Ez a folyamat a statisztikai frissítés részeként frissíti a tábla méretét (a sorok számát és a lapok számát).
- A JOIN, a GROUP BY, a ORDER BY és a DISTINCT záradékban részt vevő oszlopokra koncentrálhat.
- Érdemes lehet frissíteni a "növekvő kulcs" oszlopokat, például a tranzakciók dátumát gyakrabban, mert ezek az értékek nem szerepelnek a statisztikai hisztogramon.
- Érdemes lehet ritkábban frissíteni a statikus terjesztési oszlopokat.
- Ne feledje, hogy minden egyes statisztikai objektum sorba van frissítve. A megvalósítás egyszerűen `UPDATE STATISTICS <TABLE_NAME>` nem mindig ideális, különösen a sok statisztikai objektummal rendelkező széles táblák esetében.

További információ: a [kardinális becslése](/sql/relational-databases/performance/cardinality-estimation-sql-server).

### <a name="examples-create-statistics"></a>Példák: statisztikák létrehozása

Ezek a példák azt mutatják be, hogyan használhatók a különböző beállítások a statisztikák létrehozásához. Az egyes oszlopokhoz használt beállítások az adatok jellemzőitől és az oszlopnak a lekérdezésekben való használatának módjától függnek.

#### <a name="create-single-column-statistics-with-default-options"></a>Egyoszlopos statisztikák létrehozása alapértelmezett beállításokkal

Egy oszlop statisztikáinak létrehozásához adja meg a statisztikai objektum nevét és az oszlop nevét.
Ez a szintaxis az összes alapértelmezett beállítást használja. Alapértelmezés szerint a dedikált SQL Pool a tábla **20 százalékát** , amikor statisztikai adatokat hoz létre.

```sql
CREATE STATISTICS [statistics_name]
    ON [schema_name].[table_name]([column_name]);
```

Például:

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1);
```

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Egyoszlopos statisztikák létrehozása az egyes sorok vizsgálatával

A legtöbb esetben a 20%-os alapértelmezett mintavételi sebesség elegendő. Azonban beállíthatja a mintavételezési sebességet. A teljes táblázat mintavételezéséhez használja a következő szintaxist:

```sql
CREATE STATISTICS [statistics_name]
    ON [schema_name].[table_name]([column_name])
    WITH FULLSCAN;
```

Például:

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1)
    WITH FULLSCAN;
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Egyoszlopos statisztikák létrehozása a minta méretének megadásával

Egy másik lehetőség a minta méretének megadása százalékként:

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1)
    WITH SAMPLE = 50 PERCENT;
```

#### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Egyoszlopos statisztikák létrehozása csak néhány sorban

Statisztikákat is létrehozhat a tábla sorainak egy részén, amely neve szűrt statisztika.

Használhatja például a szűrt statisztikát, ha egy nagyméretű particionált tábla adott partíciójának lekérdezését tervezi. Ha csak a partíciós értékekre vonatkozó statisztikát hoz létre, a statisztikák pontossága javulni fog. A lekérdezési teljesítmény javulását is tapasztalhatja.

Ez a példa az értékek tartományán alapuló statisztikát hoz létre. Az értékek könnyen meghatározhatók úgy, hogy egyezzenek egy partícióban lévő értékek tartományával.

```sql
CREATE STATISTICS stats_col1
    ON table1(col1)
    WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Ahhoz, hogy a lekérdezés optimalizálva fontolják meg a szűrt statisztikák használatát, ha az elosztott lekérdezési tervet választja, a lekérdezésnek a statisztikai objektum definíciójában kell lennie. Az előző példában a lekérdezés WHERE záradékának meg kell adnia a 2000101 és a 20001231 közötti col1 értékeket.

#### <a name="create-single-column-statistics-with-all-the-options"></a>Egyoszlopos statisztikák létrehozása az összes lehetőséggel

A beállításokat együtt is egyesítheti. Az alábbi példa egy szűrt statisztikai objektumot hoz létre egyéni minta mérettel:

```sql
CREATE STATISTICS stats_col1
    ON table1 (col1)
    WHERE col1 > '2000101' AND col1 < '20001231'
    WITH SAMPLE = 50 PERCENT;
```

A teljes referenciáért lásd: [statisztikák létrehozása](/sql/t-sql/statements/create-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true).

#### <a name="create-multi-column-statistics"></a>Több oszlopból álló statisztikák létrehozása

Több oszlopból álló statisztikai objektum létrehozásához használja az előző példákat, de további oszlopokat is meg kell adnia.

> [!NOTE]
> A lekérdezési eredményben szereplő sorok számának becsléséhez használt hisztogram csak a statisztikai objektum definíciójában felsorolt első oszlop esetében érhető el.

Ebben a példában a hisztogram a *termék \_ kategóriájában* van. Az oszlopokra vonatkozó statisztikákat a *termék \_ kategóriája* és a *termék \_ sub_category* alapján számítjuk ki:

```sql
CREATE STATISTICS stats_2cols
    ON table1 (product_category, product_sub_category)
    WHERE product_category > '2000101' AND product_category < '20001231'
    WITH SAMPLE = 50 PERCENT;
```

Mivel a *termék \_ kategóriája* és a *termék \_ \_ alkategóriája* közötti korreláció létezik, a többoszlopos statisztikai objektum akkor lehet hasznos, ha ezek az oszlopok egy időben érhetők el.

#### <a name="create-statistics-on-all-columns-in-a-table"></a>Statisztikák létrehozása egy tábla összes oszlopához

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

#### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>Tárolt eljárás használata statisztikák létrehozásához az adatbázis összes oszlopán

Az SQL-készlet nem rendelkezik olyan rendszerszintű tárolt eljárással, amely egyenértékű a SQL Server sp_create_statsával. Ez a tárolt eljárás egyetlen oszlopos statisztikai objektumot hoz létre az adatbázis minden olyan oszlopán, amely még nem rendelkezik statisztikával.

Az alábbi példa segítséget nyújt az adatbázis kialakításának megkezdéséhez. Nyugodtan alkalmazkodhat az igényeihez:

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default, 2 Fullscan, 3 Sample
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

A következő eljárással hozhat létre statisztikát a tábla összes oszlopához egy fullscan használatával:

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

A táblázat összes oszlopához tartozó mintavételes statisztikák létrehozásához írja be a 3 értéket, a minta százalékot. Az alábbi eljárás 20 százalékos mintavételi sebességet használ.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

### <a name="examples-update-statistics"></a>Példák: frissítési statisztikák

A statisztikák frissítéséhez a következőket teheti:

- Frissítsen egy statisztikai objektumot. Adja meg a frissíteni kívánt statisztikai objektum nevét.
- Egy tábla összes statisztikai objektumának frissítése. Adja meg a tábla nevét egy adott statisztikai objektum helyett.

#### <a name="update-one-specific-statistics-object"></a>Egy adott statisztikai objektum frissítése

A következő szintaxissal frissíthet egy adott statisztikai objektumot:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Például:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Adott statisztikai objektumok frissítésével minimálisra csökkentheti a statisztikák kezeléséhez szükséges időt és erőforrásokat. Ehhez a művelethez néhány gondolatot kell kiválasztania a frissítendő legjobb statisztikai objektumok kiválasztásához.

#### <a name="update-all-statistics-on-a-table"></a>Egy tábla összes statisztikájának frissítése

Egy egyszerű módszer a tábla összes statisztikai objektumának frissítésére:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Például:

```sql
UPDATE STATISTICS dbo.table1;
```

A frissítés STATISZTIKÁi utasítás egyszerűen használható. Ne feledje, hogy a táblázat *összes* statisztikáját frissíti, és a szükségesnél több munkát kér. 

Ha a teljesítmény nem jelent problémát, ez a módszer a legegyszerűbb és legteljesebb mód annak biztosítására, hogy a statisztikák naprakészek legyenek.

> [!NOTE]
> Egy tábla összes statisztikájának frissítésekor a dedikált SQL-készlet ellenőrzi, hogy az egyes statisztikai objektumok táblázatát kell-e felvenni. Ha a tábla nagyméretű, és sok oszlopot és számos statisztikát tartalmaz, akkor lehet, hogy hatékonyabban kell frissíteni az egyes statisztikákat igény szerint.

Egy eljárás végrehajtásához `UPDATE STATISTICS` tekintse meg az [ideiglenes táblákat](develop-tables-temporary.md). A megvalósítási módszer némileg eltér az előző `CREATE STATISTICS` eljárástól, de az eredmény ugyanaz.
A teljes szintaxist a [statisztika frissítése](/sql/t-sql/statements/update-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true)című részben tekintheti meg.

### <a name="statistics-metadata"></a>Statisztikai metaadatok

A statisztikával kapcsolatos információk megkereséséhez számos rendszernézet és függvény használható. Láthatja például, hogy egy statisztikai objektum elavult-e a STATS_DATE () függvény használatával. A STATS_DATE () segítségével megtekintheti, hogy a statisztikák mikor lettek utoljára létrehozva vagy frissítve.

#### <a name="catalog-views-for-statistics"></a>Statisztikai katalógus nézetei

Ezek a rendszernézetek a statisztikával kapcsolatos információkat tartalmaznak:

| Katalógus nézet | Leírás |
|:--- |:--- |
| [sys. Columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?view=azure-sqldw-latest&preserve-view=true) |Egy sor az egyes oszlopokhoz. |
| [sys. Objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?view=azure-sqldw-latest&preserve-view=true) |Egy sor az adatbázis minden objektumához. |
| [sys. schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?view=azure-sqldw-latest&preserve-view=true) |Az adatbázis minden sémájának egy sora. |
| [sys. stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?view=azure-sqldw-latest&preserve-view=true) |Egy sor az egyes statisztikai objektumokhoz. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?view=azure-sqldw-latest&preserve-view=true) |Egy sor a statisztikai objektum minden oszlopához. Hivatkozásokat tartalmaz a sys. Columns fájlra. |
| [sys. Tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?view=azure-sqldw-latest&preserve-view=true) |Az egyes táblák egy sora (beleértve a külső táblákat is). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?view=azure-sqldw-latest&preserve-view=true) |Minden adattípus egy sora. |

#### <a name="system-functions-for-statistics"></a>A statisztikák rendszerfunkciói

Ezek a rendszerfunkciók a statisztikákkal való munkavégzéshez hasznosak:

| System függvény | Leírás |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?view=azure-sqldw-latest&preserve-view=true) |A statisztikai objektum utolsó frissítésének dátuma. |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true) |Összegző szint és részletes információk az értékek eloszlásáról a statisztikai objektum által értelmezett módon. |

#### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Statisztikai oszlopok és függvények egyesítése egyetlen nézetben

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
JOIN    sys.tables          AS tb ON    co.[object_id]      = tb.[object_id]
JOIN    sys.schemas         AS sm ON    tb.[schema_id]      = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

### <a name="dbcc-show_statistics-examples"></a>DBCC SHOW_STATISTICS () példák

A DBCC SHOW_STATISTICS () megjeleníti a statisztikai objektumon belül tárolt adatokat. Ezek az adatkészletek három részből állnak:

- Fejléc
- Sűrűség vektor
- Hisztogram

A fejléc a statisztikával kapcsolatos metaadatokat tartalmaz. A hisztogram megjeleníti az értékek eloszlását a statisztikai objektum első kulcs oszlopában. 

A sűrűség vektor az oszlopok közötti korrelációt méri. A dedikált SQL-készlet a statisztikai objektum bármely adatával kiszámítja a kardinális becsléseket.

#### <a name="show-header-density-and-histogram"></a>Fejléc, sűrűség és hisztogram megjelenítése

Ez az egyszerű példa egy statisztikai objektum mindhárom részét megjeleníti:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Például:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

#### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>DBCC SHOW_STATISTICS () egy vagy több részének megjelenítése

Ha csak bizonyos részeket szeretne megtekinteni, használja a `WITH` záradékot, és határozza meg, hogy mely részeket szeretné megtekinteni:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
    WITH stat_header, histogram, density_vector
```

Például:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1)
    WITH histogram, density_vector
```

### <a name="dbcc-show_statistics-differences"></a>DBCC SHOW_STATISTICS () különbségek

`DBCC SHOW_STATISTICS()` szigorúbban implementálva van a dedikált SQL-készletben a SQL Serverhoz képest:

- A nem dokumentált funkciók nem támogatottak.
- A Stats_stream nem használható.
- Nem lehet csatlakozni az eredményekhez a statisztikai adatok adott részhalmaza számára. STAT_HEADER CSATLAKOZHAT például DENSITY_VECTORhoz.
- NO_INFOMSGS nem állítható be az üzenetek letiltásához.
- A statisztikai nevekhez tartozó szögletes zárójelek nem használhatók.
- Nem használhatók oszlopnevek a statisztikai objektumok azonosításához.
- A 2767-es egyéni hiba nem támogatott.


## <a name="statistics-in-serverless-sql-pool"></a>Statisztika a kiszolgáló nélküli SQL-készletben

Az adott adatkészlethez (tárolási útvonal) tartozó statisztikákat egy adott oszlop alapján hozza létre a rendszer.

> [!NOTE]
> LOB-oszlopokhoz nem hozhatók létre statisztikák.

### <a name="why-use-statistics"></a>Miért használja a statisztikát?

Minél több kiszolgáló nélküli SQL-készlet ismeri az adatait, annál gyorsabban végezhet lekérdezéseket. Az adatokra vonatkozó statisztikák gyűjtése az egyik legfontosabb dolog, amit a lekérdezések optimalizálásához is el lehet végezni. 

A kiszolgáló nélküli SQL Pool lekérdezés-optimalizáló egy költséghatékony optimalizáló. Összehasonlítja a különböző lekérdezési csomagok költségeit, majd kiválasztja a legalacsonyabb díjszabású csomagot. A legtöbb esetben azt a tervet választja, amely a leggyorsabb végrehajtást fogja végrehajtani. 

Ha például az optimalizáló becslése szerint a lekérdezés szűrésének dátuma egy sort ad vissza, akkor egy csomagot fog kiválasztani. Ha úgy becsüli, hogy a kijelölt dátum 1 000 000 sort ad vissza, akkor egy másik csomagot ad vissza.

### <a name="automatic-creation-of-statistics"></a>Statisztikák automatikus létrehozása

A kiszolgáló nélküli SQL-készlet elemzi a bejövő felhasználói lekérdezéseket a hiányzó statisztikákhoz. Ha a statisztikai adatok hiányoznak, a lekérdezés-optimalizáló a lekérdezési predikátum egyes oszlopaira vonatkozó statisztikát hoz létre, vagy összekapcsolási feltételt biztosít a lekérdezési tervhez tartozó sarkalatos becslések javítására

A SELECT utasítás elindítja a statisztikák automatikus létrehozását.

> [!NOTE]
> A statisztikák automatikus létrehozása be van kapcsolva a Parquet-fájlok esetében. CSV-fájlok esetén manuálisan kell létrehoznia a statisztikát, amíg a CSV-fájlok statisztikájának automatikus létrehozása nem támogatott.

A statisztikák automatikus létrehozása szinkron módon történik, így előfordulhat, hogy az oszlopok hiányoznak a statisztikákból. Az egyetlen oszlop statisztikáinak létrehozási ideje a megcélozott fájlok méretétől függ.

### <a name="manual-creation-of-statistics"></a>Statisztikák manuális létrehozása

A kiszolgáló nélküli SQL-készlet lehetővé teszi a statisztikák manuális létrehozását. CSV-fájlok esetén manuálisan kell létrehoznia a statisztikát, mivel a statisztikai adatok automatikus létrehozása nincs bekapcsolva a CSV-fájlokhoz. 

A statisztikák manuális létrehozásával kapcsolatos útmutatásért tekintse meg az alábbi példákat.

### <a name="update-statistics"></a>Frissítési statisztika

A fájlokban lévő adatok, a törlés és a fájlok hozzáadásával végzett módosítások adateloszlási változásokat eredményeznek, és elavult statisztikákat tesznek elérhetővé. Ebben az esetben frissíteni kell a statisztikát.

A kiszolgáló nélküli SQL-készlet automatikusan újból létrehozza a statisztikát, ha az adatok jelentősen változnak. A rendszer minden alkalommal automatikusan létrehozza a statisztikát, és az adatkészlet aktuális állapotát is menti: fájlelérési utak, méretek, utolsó módosítás dátuma.

Ha a statisztikák elavultak, újak jönnek létre. Az algoritmus áthalad az adatokat, és összehasonlítja az adatkészlet aktuális állapotával. Ha a módosítások mérete meghaladja az adott küszöbértéket, a rendszer törli a régi statisztikákat, és az új adatkészleten újra létrehozza azokat.

A manuális statisztikák sosem lettek deklarálva elavultnak.

> [!NOTE]
> A statisztikák automatikus kiszolgálása a Parquet-fájlok esetében be van kapcsolva. CSV-fájlok esetében manuálisan kell eldobnia és létrehoznia a statisztikát, amíg a CSV-fájlok statisztikájának automatikus létrehozása nem támogatott. A statisztikák eldobásához és létrehozásához lásd az alábbi példákat.

Az egyik első kérdés a lekérdezés hibaelhárításakor: **"a statisztikák naprakészek?"**

Ha a sorok száma jelentősen megváltozott, vagy jelentős változás van egy oszlop értékeinek eloszlásában, *akkor* itt az ideje, hogy frissítse a statisztikát.

> [!NOTE]
> Ha egy oszlop értékeinek eloszlásában jelentős változások történnek, akkor frissítse a statisztikát, függetlenül attól, hogy mikor frissítették őket.

### <a name="implement-statistics-management"></a>A statisztikák kezelésének megvalósítása

Érdemes lehet kiterjeszteni az adatfolyamatot annak biztosítására, hogy a statisztikák frissítve legyenek, ha az adatok a Hozzáadás, a törlés vagy a fájlok módosítása révén jelentősen változnak.

A statisztikák frissítéséhez a következő irányadó elveket kell megadnia:

- Győződjön meg arról, hogy az adatkészlet legalább egy statisztikai objektummal frissült. A frissítések mérete (sorok száma és oldalszáma) a statisztikák frissítésének részeként.
- Olyan oszlopokra koncentrálhat, amelyek részt vesznek a WHERE, a JOIN, a GROUP BY, a ORDER BY és a DISTINCT záradékban.
- Frissítse a "növekvő kulcs" oszlopokat, például a tranzakciók dátumát gyakrabban, mert ezek az értékek nem szerepelnek a statisztikai hisztogramon.
- Ritkábban frissítse a statikus terjesztési oszlopokat.

További információ: a [kardinális becslése](/sql/relational-databases/performance/cardinality-estimation-sql-server).

### <a name="examples-create-statistics-for-column-in-openrowset-path"></a>Példák: statisztikák létrehozása az oszlophoz a OPENROWSET elérési útján

Az alábbi példák bemutatják, hogyan használhatók a különböző beállítások a statisztikák létrehozásához. Az egyes oszlopokhoz használt beállítások az adatok jellemzőitől és az oszlopnak a lekérdezésekben való használatának módjától függnek.

> [!NOTE]
> Egyoszlopos statisztikákat csak jelenleg hozhat létre.
>
> Sp_create_openrowset_statistics és sp_drop_openrowset_statistics végrehajtásához a következő engedélyek szükségesek: TÖMEGES műveletek felügyelete vagy az adatbázis TÖMEGES MŰVELETEInek felügyelete.

A következő tárolt eljárással lehet statisztikát létrehozni:

```sql
sys.sp_create_openrowset_statistics [ @stmt = ] N'statement_text'
```

Argumentumok: [ @stmt =] N "statement_text" – egy Transact-SQL-utasítás, amely a statisztikához használandó oszlop-értékeket adja vissza. A TABLESAMPLE segítségével megadhatja a használni kívánt adatmintákat. Ha a TABLESAMPLE nincs megadva, a rendszer a FULLSCAN fogja használni.

```syntaxsql
<tablesample_clause> ::= TABLESAMPLE ( sample_number PERCENT )
```

> [!NOTE]
> A CSV-mintavételezés jelenleg nem működik, csak a FULLSCAN támogatja a CSV-t.

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Egyoszlopos statisztikák létrehozása az egyes sorok vizsgálatával

Egy oszlop statisztikáinak létrehozásához adjon meg egy lekérdezést, amely visszaadja azt az oszlopot, amelyhez statisztikára van szüksége.

Ha nem ad meg mást, alapértelmezés szerint a kiszolgáló nélküli SQL-készlet az adatkészletben megadott adatok 100%-át használja statisztikai adatok létrehozásakor.

Ha például az alapértelmezett beállításokkal (FULLSCAN) szeretne statisztikákat létrehozni az adatkészlet év oszlopához a population.csv fájl alapján:

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_openrowset_statistics N'SELECT year
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv'',
        FORMAT = ''CSV'',
        FIELDTERMINATOR ='','',
        ROWTERMINATOR = ''\n''
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
'
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Egyoszlopos statisztikák létrehozása a minta méretének megadásával

A minta mérete százalékként adható meg:

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_openrowset_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-update-statistics"></a>Példák: frissítési statisztikák

A statisztikák frissítéséhez el kell dobnia és létre kell hoznia a statisztikát. A következő tárolt eljárással lehet eldobni a statisztikát:

```sql
sys.sp_drop_openrowset_statistics [ @stmt = ] N'statement_text'
```

> [!NOTE]
> Sp_create_openrowset_statistics és sp_drop_openrowset_statistics végrehajtásához a következő engedélyek szükségesek: TÖMEGES műveletek felügyelete vagy az adatbázis TÖMEGES MŰVELETEInek felügyelete.

Argumentumok: [ @stmt =] N "statement_text" – a statisztikák létrehozásakor használt Transact-SQL-utasítást adja meg.

Az adatkészletben található év oszlop statisztikáinak frissítéséhez, amely a population.csv fájlon alapul, el kell dobnia és létre kell hoznia a statisztikát:

```sql
EXEC sys.sp_drop_openrowset_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
GO

/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_openrowset_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-create-statistics-for-external-table-column"></a>Példák: statisztikák létrehozása külső tábla oszlopaihoz

Az alábbi példák bemutatják, hogyan használhatók a különböző beállítások a statisztikák létrehozásához. Az egyes oszlopokhoz használt beállítások az adatok jellemzőitől és az oszlopnak a lekérdezésekben való használatának módjától függnek.

> [!NOTE]
> Egyoszlopos statisztikákat csak jelenleg hozhat létre.

Egy oszlop statisztikáinak létrehozásához adja meg a statisztikai objektum nevét és az oszlop nevét.

```sql
CREATE STATISTICS statistics_name
ON { external_table } ( column )
    WITH
        { FULLSCAN
          | [ SAMPLE number PERCENT ] }
        , { NORECOMPUTE }
```

Argumentumok: external_table megadja a statisztikát létrehozó külső táblát.

Az összes sor vizsgálatával FULLSCAN a számítási statisztikát. A FULLSCAN és a SAMPLE 100 százalék ugyanazokat az eredményeket eredményezi. A FULLSCAN nem használható a SAMPLE kapcsolóval.

A SAMPLE Number százalék meghatározza a lekérdezés-optimalizáló által a statisztikai adatok létrehozásakor használandó sorok hozzávetőleges százalékos arányát vagy számát. 0 és 100 közötti szám lehet.

A minta nem használható a FULLSCAN kapcsolóval.

> [!NOTE]
> A CSV-mintavételezés jelenleg nem működik, csak a FULLSCAN támogatja a CSV-t.

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Egyoszlopos statisztikák létrehozása az egyes sorok vizsgálatával

```sql
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH FULLSCAN, NORECOMPUTE
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Egyoszlopos statisztikák létrehozása a minta méretének megadásával

```sql
-- following sample creates statistics with sampling 20%
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH SAMPLE 5 percent, NORECOMPUTE
```

### <a name="examples-update-statistics"></a>Példák: frissítési statisztikák

A statisztikák frissítéséhez el kell dobnia és létre kell hoznia a statisztikát. Először dobja el a statisztikát:

```sql
DROP STATISTICS census_external_table.sState
```

És hozzon létre statisztikát:

```sql
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH FULLSCAN, NORECOMPUTE
```

### <a name="statistics-metadata"></a>Statisztikai metaadatok

A statisztikával kapcsolatos információk megkereséséhez számos rendszernézet és függvény használható. Láthatja például, hogy egy statisztikai objektum elavult-e a STATS_DATE () függvény használatával. A STATS_DATE () segítségével megtekintheti, hogy a statisztikák mikor lettek utoljára létrehozva vagy frissítve.

> [!NOTE]
> A statisztikai metaadatok csak a külső táblák oszlopai számára érhetők el. A statisztikai metaadatok nem érhetők el OPENROWSET oszlopokhoz.

#### <a name="catalog-views-for-statistics"></a>Statisztikai katalógus nézetei

Ezek a rendszernézetek a statisztikával kapcsolatos információkat tartalmaznak:

| Katalógus nézet                                                 | Leírás                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [sys. Columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Egy sor az egyes oszlopokhoz.                                     |
| [sys. Objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Egy sor az adatbázis minden objektumához.                     |
| [sys. schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Az adatbázis minden sémájának egy sora.                     |
| [sys. stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Egy sor az egyes statisztikai objektumokhoz.                          |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Egy sor a statisztikai objektum minden oszlopához. Hivatkozásokat tartalmaz a sys. Columns fájlra. |
| [sys. Tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Az egyes táblák egy sora (beleértve a külső táblákat is).           |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Minden adattípus egy sora.                                  |

#### <a name="system-functions-for-statistics"></a>A statisztikák rendszerfunkciói

Ezek a rendszerfunkciók a statisztikákkal való munkavégzéshez hasznosak:

| System függvény                                              | Leírás                                  |
| :----------------------------------------------------------- | :------------------------------------------- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?view=azure-sqldw-latest&preserve-view=true) | A statisztikai objektum utolsó frissítésének dátuma. |

#### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Statisztikai oszlopok és függvények egyesítése egyetlen nézetben

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
JOIN    sys.tables          AS tb ON    co.[object_id]      = tb.[object_id]
JOIN    sys.schemas         AS sm ON    tb.[schema_id]      = sm.[schema_id]
WHERE   st.[user_created] = 1
;
```

## <a name="next-steps"></a>Következő lépések

A dedikált SQL-készlet lekérdezési teljesítményének növeléséhez lásd: a számítási feladatok és ajánlott eljárások [figyelése](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) [dedikált SQL-készlethez](best-practices-dedicated-sql-pool.md#maintain-statistics).

A kiszolgáló nélküli SQL-készlet lekérdezési teljesítményének növeléséhez tekintse meg az [ajánlott eljárások a kiszolgáló nélküli SQL-készlethez](best-practices-serverless-sql-pool.md) című témakört.
