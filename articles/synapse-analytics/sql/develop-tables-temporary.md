---
title: Ideiglenes táblák használata a Synapse SQL
description: Alapvető útmutatás az ideiglenes táblák Synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 8513df83196b3521a749515c6bb22caad7d255b7
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816040"
---
# <a name="temporary-tables-in-synapse-sql"></a>Ideiglenes táblák a Synapse SQL

Ez a cikk alapvető útmutatást nyújt az ideiglenes táblákhoz, és kiemeli a munkamenetszintű ideiglenes táblák alapelveit a Synapse SQL. 

Mind a dedikált SQL-készlet, mind a kiszolgáló nélküli SQL-készlet erőforrásai ideiglenes táblákat tudnak használni. A kiszolgáló nélküli SQL-készlet korlátozásokkal rendelkezik, amelyekről a cikk végén lesz szó. 

## <a name="temporary-tables"></a>Ideiglenes táblák

Az ideiglenes táblák hasznosak az adatok feldolgozásakor, különösen az átalakítás során, ahol a köztes eredmények átmenetiek. A Synapse SQL ideiglenes táblák a munkamenet szintjén léteznek.  Csak abban a munkamenetben láthatók, amelyben létrehozták őket. Így a munkamenet kijelentkezésekor a rendszer automatikusan eldobja őket. 

## <a name="temporary-tables-in-dedicated-sql-pool"></a>Ideiglenes táblák a dedikált SQL-készletben

A dedikált SQL-készlet erőforrásában az ideiglenes táblák teljesítménybeli előnyt kínálnak, mivel az eredményeik nem távoli, hanem helyi tárolóba vannak írva.

### <a name="create-a-temporary-table"></a>Ideiglenes tábla létrehozása

Az ideiglenes táblák a táblanév előtaggal való előtaggal való előtaggal vannak `#` létrehozva.  Például:

```sql
CREATE TABLE #stats_ddl
(
    [schema_name]        NVARCHAR(128) NOT NULL
,    [table_name]            NVARCHAR(128) NOT NULL
,    [stats_name]            NVARCHAR(128) NOT NULL
,    [stats_is_filtered]     BIT           NOT NULL
,    [seq_nmbr]              BIGINT        NOT NULL
,    [two_part_name]         NVARCHAR(260) NOT NULL
,    [three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
```

Az ideiglenes táblák ugyanezen módszer használatával `CTAS` is létre is hozhatóak:

```sql
CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
;
```

> [!NOTE]
> `CTAS` A egy hatékony parancs, és a tranzakciós naplóterület hatékony használatának további előnye. 
> 
> 

### <a name="drop-temporary-tables"></a>Ideiglenes táblák eldobása

Új munkamenet létrehozásakor nem létezhetnek ideiglenes táblák.  Ha azonban ugyanazt a tárolt eljárást hívja meg, amely egy azonos nevű ideiglenes folyamatot hoz létre, hogy az utasítások sikeresek legyenek, használjon egy egyszerű előzetes ellenőrzést `CREATE TABLE` a  `DROP` következővel: 

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

A kódolási konzisztencia érdekében ezt a mintát a táblákhoz és az ideiglenes táblákhoz is használhatja.  A használatával is eltávolíthatja az ideiglenes `DROP TABLE` táblákat, ha végzett velük.  

A tárolt eljárásfejlesztés során gyakori, hogy az eljárás végén az elejtés parancsokat összecsomagoltuk, hogy az objektumok el legyen különedve.

```sql
DROP TABLE #stats_ddl
```

### <a name="modularize-code"></a>Kód modularizálása

Az ideiglenes táblák bárhol használhatók a felhasználói munkamenetben. Ezt a képességet aztán kihasználva modularizálhatja az alkalmazás kódját.  Az alábbi tárolt eljárás úgy hozza létre a DDL-t, hogy statisztikai név alapján frissítse az adatbázisban az összes statisztikát:

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
    ,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    #stats_ddl
;
GO
```

Ezen a ponton az egyetlen művelet, amely történt, az egyetlen olyan tárolt eljárás létrehozása, amely létrehozza a #stats_ddl táblát.  A tárolt eljárás nem #stats_ddl ha már létezik. Ez a leesés biztosítja, hogy ne hibásodjon meg, ha egy munkameneten belül egynél többet futtat.  

Mivel nincs a tárolt eljárás végén, a tárolt eljárás befejezésekor a létrehozott tábla megmarad, és a tárolt eljáráson kívül `DROP TABLE` is olvasható lesz.  

Más adatbázis-SQL Server ellentétben a Synapse SQL lehetővé teszi az ideiglenes tábla használatát az azt létrehozó eljáráson kívül.  A dedikált SQL-készleten keresztül létrehozott ideiglenes táblák a **munkameneten belül** bárhol használhatók. Ennek eredményeképpen modulárisabb és kezelhetőbb kódot fog tartalmazni, ahogyan azt az alábbi minta szemlélteti:

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

### <a name="temporary-table-limitations"></a>Ideiglenes tábla korlátozásai

A dedikált SQL-készletre vonatkozik néhány megvalósítási korlátozás az ideiglenes táblákra:

- Csak a munkamenet-hatókörű ideiglenes táblák támogatottak.  A globális ideiglenes táblák nem támogatottak.
- Ideiglenes táblákon nem lehet nézeteket létrehozni.
- Az ideiglenes táblák csak kivonatokkal vagy a round robin elosztással hozhatóak létre.  A replikált ideiglenes táblaelosztás nem támogatott. 

## <a name="temporary-tables-in-serverless-sql-pool"></a>Ideiglenes táblák a kiszolgáló nélküli SQL-készletben

A kiszolgáló nélküli SQL-készletben lévő ideiglenes táblák támogatottak, de használatuk korlátozott. Nem használhatók a célfájlokat megcélzott lekérdezésekben. 

Például nem illeszthet össze ideiglenes táblát a tárolóban lévő fájlokból származó adatokkal. Az ideiglenes táblák száma 100-ra, a teljes méretük pedig 100 MB-ra van korlátozva.

## <a name="next-steps"></a>Következő lépések

További információ a táblák fejlesztésről: Táblák tervezése a Synapse SQL [erőforrásokkal.](develop-tables-overview.md)

