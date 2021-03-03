---
title: Tárolt eljárások használata
description: Tippek a tárolt eljárások megvalósításához a szinapszis SQL használatával az Azure szinapszis Analyticsben a megoldás fejlesztéséhez.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/03/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 083077b2b29bc415e83d6f5a76941fa83fa853e3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101674154"
---
# <a name="stored-procedures-using-synapse-sql-in-azure-synapse-analytics"></a>Tárolt eljárások a szinapszis SQL használatával az Azure szinapszis Analyticsben

Az SQL kiosztott és kiszolgáló nélküli készletei lehetővé teszik összetett adatfeldolgozási logikák elhelyezését az SQL tárolt eljárásaiba. A tárolt eljárások nagyszerű lehetőséget biztosítanak az SQL-kód beágyazására és az adattárházban lévő adataihoz való közvetlen tárolásra. A tárolt eljárások segítséget nyújtanak a fejlesztőknek, hogy modularize a kódot a felügyelhető egységbe ágyazva, és megkönnyítsék a kód nagyobb mértékű újrahasználhatóságát. Az egyes tárolt eljárások is elfogadják a paramétereket, hogy azok még rugalmasabbak legyenek.
Ebben a cikkben néhány tippet talál a tárolt eljárások megvalósításához a szinapszis SQL-készletben a megoldások fejlesztéséhez.

## <a name="what-to-expect"></a>Amire számíthat

A szinapszis SQL számos, a SQL Serverban használt T-SQL-funkciót támogat. Még ennél is fontosabb, hogy a megoldás teljesítményének maximalizálása érdekében Felskálázási funkciók érhetők el. Ebből a cikkből megtudhatja, milyen funkciókat helyezhet el a tárolt eljárásokban.

> [!NOTE]
> Az eljárás törzsében csak a szinapszis SQL Surface területén támogatott funkciókat használhatja. Tekintse át [ezt a cikket](overview-features.md) a tárolt eljárásokban használható objektumok és utasítások azonosításához. A cikkben szereplő példák olyan általános funkciókat használnak, amelyek kiszolgáló nélküli és dedikált felületen is elérhetők. A cikk végén további [korlátozásokat talál a kiépített és a kiszolgáló nélküli SZINAPSZIS SQL-készletekben](#limitations) .

Az SQL-készlet méretezésének és teljesítményének fenntartása érdekében vannak olyan funkciók és funkciók is, amelyek viselkedési különbségekkel és másokkal rendelkeznek, amelyek nem támogatottak.

## <a name="stored-procedures-in-synapse-sql"></a>Tárolt eljárások a szinapszis SQL-ben

A következő példában megtekintheti azokat az eljárásokat, amelyek eldobják a külső objektumokat, ha az adatbázisban vannak:

```sql
CREATE PROCEDURE drop_external_table_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_tables WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL TABLE ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
GO
CREATE PROCEDURE drop_external_file_format_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_file_formats WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL FILE FORMAT ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
GO
CREATE PROCEDURE drop_external_data_source_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_data_sources WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL DATA SOURCE ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
```

Ezek az eljárások olyan utasítás használatával hajthatók végre `EXEC` , amelynél megadhatja az eljárás nevét és paramétereit:

```sql
EXEC drop_external_table_if_exists 'mytest';
EXEC drop_external_file_format_if_exists 'mytest';
EXEC drop_external_data_source_if_exists 'mytest';
```

A szinapszis SQL egyszerűsített és gördülékenyen tárolt eljárások megvalósítását teszi lehetővé. SQL Server képest a legnagyobb különbség az, hogy a tárolt eljárás nem előre lefordított kód. Az adattárházak esetében a fordítási idő kisebb, mint a nagyméretű adatköteteken végzett lekérdezések futtatásához szükséges idő. Fontos, hogy a tárolt eljárás kódja megfelelően legyen optimalizálva a nagyméretű lekérdezésekhez. A cél az óra, perc és másodperc megtakarítása, nem ezredmásodperc. Ezért hasznos, ha a tárolt eljárásokat SQL Logic tárolóként szeretné meggondolni.

Amikor a szinapszis SQL végrehajtja a tárolt eljárást, az SQL-utasítások elemzése, lefordítása és futási ideje optimalizált. A folyamat során minden utasítás elosztott lekérdezésekre lesz konvertálva. Az adatbázison végrehajtott SQL-kód eltér az elküldött lekérdezéstől.

## <a name="encapsulate-validation-rules"></a>Érvényesítési szabályok beágyazása

A tárolt eljárások lehetővé teszik az ellenőrzési logikák megkeresését az SQL Database-ben tárolt egyetlen modulban. A következő példában láthatja, hogyan ellenőrizheti a paraméterek értékeit, és módosíthatja az alapértelmezett értékeket.

```sql
CREATE PROCEDURE count_objects_by_date_created 
                            @start_date DATETIME2,
                            @end_date DATETIME2
AS BEGIN 

    IF( @start_date >= GETUTCDATE() )
    BEGIN
        THROW 51000, 'Invalid argument @start_date. Value should be in past.', 1;  
    END

    IF( @end_date IS NULL )
    BEGIN
        SET @end_date = GETUTCDATE();
    END

    IF( @start_date >= @end_date )
    BEGIN
        THROW 51000, 'Invalid argument @end_date. Value should be greater than @start_date.', 2;  
    END

    SELECT
         year = YEAR(create_date),
         month = MONTH(create_date),
         objects_created = COUNT(*)
    FROM
        sys.objects
    WHERE
        create_date BETWEEN @start_date AND @end_date
    GROUP BY
        YEAR(create_date), MONTH(create_date);
END
```

Az SQL-eljárás logikája érvényesíti a bemeneti paramétereket az eljárás meghívásakor.

```sql

EXEC count_objects_by_date_created '2020-08-01', '2020-09-01'

EXEC count_objects_by_date_created '2020-08-01', NULL

EXEC count_objects_by_date_created '2020-09-01', '2020-08-01'
-- Error
-- Invalid argument @end_date. Value should be greater than @start_date.

EXEC count_objects_by_date_created '2120-09-01', NULL
-- Error
-- Invalid argument @start_date. Value should be in past.
```

## <a name="nesting-stored-procedures"></a>Tárolt eljárások beágyazása

Ha a tárolt eljárás más tárolt eljárásokat hív meg, vagy dinamikus SQL-t hajt végre, akkor a belső tárolt eljárás vagy a kód meghívása beágyazottnak mondható.
A beágyazott eljárás példája az alábbi kódban látható:

```sql
CREATE PROCEDURE clean_up @name SYSNAME
AS BEGIN
    EXEC drop_external_table_if_exists @name;
    EXEC drop_external_file_format_if_exists @name;
    EXEC drop_external_data_source_if_exists @name;
END
```

Ez az eljárás olyan paramétert fogad el, amely valamilyen nevet jelöl, majd más eljárásokat hív meg, hogy eldobják az objektumokat ezen a néven.
A szinapszis SQL-készlet legfeljebb nyolc beágyazási szintet támogat. Ez a képesség némileg eltér a SQL Server. A SQL Server beágyazási szintje 32.

A legfelső szintű tárolt eljárás az 1. szintű beágyazásnak felel meg.

```sql
EXEC clean_up 'mytest'
```

Ha a tárolt eljárás egy másik EXEC hívást is végrehajt, a beágyazási szint két értékre nő.

```sql
CREATE PROCEDURE clean_up @name SYSNAME
AS
    EXEC drop_external_table_if_exists @name  -- This call is nest level 2
GO
EXEC clean_up 'mytest'  -- This call is nest level 1
```

Ha a második eljárás elvégez néhány dinamikus SQL-műveletet, a beágyazási szint háromra nő.

```sql
CREATE PROCEDURE drop_external_table_if_exists @name SYSNAME
AS BEGIN
    /* See full code in the previous example */
    EXEC sp_executesql @tsql = @drop_stmt;  -- This call is nest level 3
END
GO
CREATE PROCEDURE clean_up @name SYSNAME
AS
    EXEC drop_external_table_if_exists @name  -- This call is nest level 2
GO
EXEC clean_up 'mytest'  -- This call is nest level 1
```

> [!NOTE]
> A szinapszis SQL jelenleg nem támogatja [a @NESTLEVEL @](/sql/t-sql/functions/nestlevel-transact-sql?view=azure-sqldw-latest&preserve-view=true)-t. Követnie kell a beágyazási szintet. Nem valószínű, hogy túllépi a nyolcnál nagyobb korlátot, de ha igen, újra kell dolgoznia a kódot, hogy az illeszkedjen a korláton belüli beágyazási szintre.

## <a name="insertexecute"></a>INSERT..EXEARANYOS

A kiépített szinapszis SQL-készlet nem teszi lehetővé, hogy a tárolt eljárás eredményét egy INSERT utasítással használja. Egy alternatív megközelítés is használható. Példaként tekintse meg a következő cikket: [ideiglenes táblák](develop-tables-temporary.md) a kiépített szinapszis SQL-készlethez.

## <a name="limitations"></a>Korlátozások

Léteznek olyan Transact-SQL tárolt eljárások néhány aspektusa, amelyek nincsenek megvalósítva a szinapszis SQL-ben, például:

| Szolgáltatás/beállítás | Kiépítve | Kiszolgáló nélküli |
| --- | --- |
| Ideiglenes tárolt eljárások | Nem | Igen |
| Számozott tárolt eljárások | Nem | Nem |
| Bővített tárolt eljárások | Nem | Nem |
| CLR tárolt eljárásai | Nem | Nem |
| Titkosítási beállítás | Nem | Igen |
| Replikációs beállítás | Nem | Nem |
| Ideiglenes értékű paraméterek | Nem | Nem |
| Csak olvasható paraméterek | Nem | Nem |
| Alapértelmezett paraméterek | Nem | Igen |
| Végrehajtási környezetek | Nem | Nem |
| Visszatérési utasítás | Nem | Igen |
| BEILLESZTÉS A.. EXEC | Nem | Igen |

## <a name="next-steps"></a>Következő lépések

További fejlesztési tippek: a [fejlesztés áttekintése](develop-overview.md).
