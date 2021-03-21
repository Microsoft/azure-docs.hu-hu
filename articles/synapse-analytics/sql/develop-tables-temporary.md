---
title: Ideiglenes táblák használata a szinapszis SQL-ben
description: Alapvető útmutató az ideiglenes táblák használatához a szinapszis SQL-ben.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 06faa1da71331c299245a93af96166880e7732de
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96451788"
---
# <a name="temporary-tables-in-synapse-sql"></a>Ideiglenes táblák a szinapszis SQL-ben

Ez a cikk alapvető útmutatást tartalmaz az ideiglenes táblák használatához, és kiemeli a munkamenetek szintjének ideiglenes tábláinak alapelveit a szinapszis SQL-en belül. 

A dedikált SQL-készlet és a kiszolgáló nélküli SQL-készlet erőforrásai is használhatják az ideiglenes táblákat. A kiszolgáló nélküli SQL-készlet korlátozásait a cikk végén tárgyaljuk. 

## <a name="temporary-tables"></a>Ideiglenes táblák

Az ideiglenes táblák hasznosak az adatfeldolgozás során, különösen az átalakítás során, ahol a közbenső eredmények átmenetiek. A szinapszis SQL esetében ideiglenes táblák találhatók a munkamenet szintjén.  Csak abban a munkamenetben láthatók, amelyben létrehozták őket. Ilyenkor a rendszer automatikusan elveti őket, amikor a munkamenet kijelentkezik. 

## <a name="temporary-tables-in-dedicated-sql-pool"></a>Ideiglenes táblák a dedikált SQL-készletben

A dedikált SQL Pool-erőforrásban az ideiglenes táblák teljesítménybeli előnyt biztosítanak, mert az eredményeket a rendszer a távoli tárterület helyett a helyi környezetbe írja.

### <a name="create-a-temporary-table"></a>Ideiglenes tábla létrehozása

Az ideiglenes táblákat a táblanév előtaggal való előállításával hozza létre a rendszer `#` .  Például:

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

Az ideiglenes táblák is létrehozhatók a `CTAS` használatával, pontosan ugyanazzal a módszerrel:

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
> `CTAS` a egy hatékony parancs, és az előnye, hogy hatékony a tranzakciós napló területének használatakor. 
> 
> 

### <a name="drop-temporary-tables"></a>Ideiglenes táblák eldobása

Új munkamenet létrehozásakor nem létezhet ideiglenes tábla.  Ha azonban ugyanazt a tárolt eljárást hívja meg, amely egy ideiglenest hoz létre ugyanazzal a névvel, akkor győződjön meg arról, hogy az `CREATE TABLE` utasítások sikeresek, és egy egyszerű, előfeltétel-ellenőrzéssel ellenőrizze az alábbiakat  `DROP` : 

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

A kódolási konzisztencia esetében érdemes ezt a mintát használni a táblák és az ideiglenes táblák esetében is.  Azt is érdemes használni, hogy az `DROP TABLE` ideiglenes táblákat is távolítsa el, ha elkészült velük.  

A tárolt eljárások fejlesztése során gyakran előfordul, hogy az eljárások végén a legördülő parancsok együttesen jelennek meg, így biztosítva az objektumok tisztítását.

```sql
DROP TABLE #stats_ddl
```

### <a name="modularize-code"></a>Modularize-kód

Az ideiglenes táblák bárhol használhatók egy felhasználói munkamenetben. Ezt a képességet azután kihasználhatja, hogy modularize az alkalmazás kódját.  A következő tárolt eljárással lehet bemutatni a DDL-et az adatbázis összes statisztikájának a statisztikai név alapján történő frissítéséhez:

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
FROM    t1
;
GO
```

Ebben a szakaszban az egyetlen művelet történt egy olyan tárolt eljárás létrehozásakor, amely létrehozza a #stats_ddl ideiglenes táblát.  Ha már létezik, a tárolt eljárás elveszíti #stats_ddl. Ez a csökkenés gondoskodik arról, hogy az ne legyen sikertelen, ha egy munkameneten belül többször fut.  

Mivel a tárolt eljárás végén nem található a tárolt eljárás `DROP TABLE` , a létrehozott tábla továbbra is elérhető marad, és a tárolt eljáráson kívül is olvasható.  

Más SQL Server-adatbázisokkal szemben a szinapszis SQL lehetővé teszi az ideiglenes tábla használatát az azt létrehozó eljáráson kívül.  A dedikált SQL-készleten keresztül létrehozott ideiglenes táblák a munkameneten belül **bárhol** használhatók. Ennek eredményeképpen több moduláris és felügyelhető kóddal fog rendelkezni, ahogy azt az alábbi minta mutatja:

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

### <a name="temporary-table-limitations"></a>Ideiglenes táblák korlátozásai

A dedikált SQL-készletnek van néhány implementációs korlátja az ideiglenes táblákhoz:

- Csak a munkamenet-hatókörű ideiglenes táblák támogatottak.  A globális ideiglenes táblák nem támogatottak.
- Nem hozhatók létre nézetek ideiglenes táblákon.
- Ideiglenes táblákat csak kivonattal vagy ciklikus multiplexelés eloszlással lehet létrehozni.  A replikált ideiglenes tábla eloszlása nem támogatott. 

## <a name="temporary-tables-in-serverless-sql-pool"></a>Ideiglenes táblák a kiszolgáló nélküli SQL-készletben

A kiszolgáló nélküli SQL-készletben lévő ideiglenes táblák támogatottak, de a használatuk korlátozott. Nem használhatók olyan lekérdezésekben, amelyek megcélozják a fájlokat. 

Nem lehet például olyan ideiglenes táblát csatlakoztatni, amely a tárolóban lévő fájlokból származó adatokkal rendelkezik. Az ideiglenes táblák száma 100, a teljes méretük pedig 100 MB-ra van korlátozva.

## <a name="next-steps"></a>Következő lépések

A táblázatok létrehozásával kapcsolatos további tudnivalókért tekintse meg a [táblák tervezése a SZINAPSZIS SQL-erőforrások használatával](develop-tables-overview.md) című cikket.

