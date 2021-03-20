---
title: A U-SQL-katalógus használata Azure Data Lake Analytics
description: Megtudhatja, hogyan oszthatja meg a kódot és az információkat a U-SQL-katalógus használatával. Táblázat értékű függvények létrehozása, nézetek létrehozása, táblák létrehozása és lekérdezése.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 05/09/2017
ms.openlocfilehash: f92aadc8ccf18dd91b5dd4b35285f60b174e4cf7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92220074"
---
# <a name="get-started-with-the-u-sql-catalog-in-azure-data-lake-analytics"></a>Ismerkedés az U-SQL-katalógussal Azure Data Lake Analytics

## <a name="create-a-tvf"></a>TVF létrehozása

Az előző U-SQL-parancsfájlban megismétli, hogy a kinyerés ugyanazzal a forrásfájl használatával olvasson. Ha a U-SQL tábla értékű függvényt (TVF) használja, akkor a későbbiekben újra beágyazhatja az adategységeket.  

A következő szkript létrehoz egy nevű TVF `Searchlog()` az alapértelmezett adatbázisban és sémában:

```usql
DROP FUNCTION IF EXISTS Searchlog;

CREATE FUNCTION Searchlog()
RETURNS @searchlog TABLE
(
            UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
)
AS BEGIN
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
USING Extractors.Tsv();
RETURN;
END;
```

Az alábbi szkript bemutatja, hogyan használhatja az előző parancsfájlban definiált TVF:

```usql
@res =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM Searchlog() AS S
GROUP BY Region
HAVING SUM(Duration) > 200;

OUTPUT @res
    TO "/output/SearchLog-use-tvf.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

## <a name="create-views"></a>Nézetek létrehozása

Ha egyetlen lekérdezési kifejezéssel rendelkezik, a TVF helyett egy U-SQL-nézetet használhat a kifejezés beágyazásához.

A következő szkript létrehoz egy nevű nézetet `SearchlogView` az alapértelmezett adatbázisban és sémában:

```usql
DROP VIEW IF EXISTS SearchlogView;

CREATE VIEW SearchlogView AS  
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
USING Extractors.Tsv();
```

A következő parancsfájl a definiált nézet használatát mutatja be:

```usql
@res =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM SearchlogView
GROUP BY Region
HAVING SUM(Duration) > 200;

OUTPUT @res
    TO "/output/Searchlog-use-view.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

## <a name="create-tables"></a>Táblák létrehozása
Csakúgy, mint a kapcsolódó adatbázistáblák esetében, az U-SQL használatával létrehozhat egy előre definiált sémával rendelkező táblázatot, vagy létrehozhat egy táblázatot, amely kikövetkezteti a sémát a táblát feltöltő lekérdezésből (más néven a SELECT vagy a CTAS CREATE TABLE).

Hozzon létre egy adatbázist és két táblázatot a következő parancsfájl használatával:

```usql
DROP DATABASE IF EXISTS SearchLogDb;
CREATE DATABASE SearchLogDb;
USE DATABASE SearchLogDb;

DROP TABLE IF EXISTS SearchLog1;
DROP TABLE IF EXISTS SearchLog2;

CREATE TABLE SearchLog1 (
            UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string,

            INDEX sl_idx CLUSTERED (UserId ASC)
                DISTRIBUTED BY HASH (UserId)
);

INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;

CREATE TABLE SearchLog2(
    INDEX sl_idx CLUSTERED (UserId ASC)
            DISTRIBUTED BY HASH (UserId)
) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here
```

## <a name="query-tables"></a>Táblák lekérdezése
Az adatfájlok lekérdezéséhez hasonló módon, például az előző szkriptben létrehozott táblákat is lekérdezheti. A sorhalmaz a kinyerés használatával történő létrehozása helyett a tábla nevére is hivatkozhat.

A táblákból való olvasáshoz módosítsa a korábban használt átalakítási parancsfájlt:

```usql
@rs1 =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM SearchLogDb.dbo.SearchLog2
GROUP BY Region;

@res =
    SELECT *
    FROM @rs1
    ORDER BY TotalDuration DESC
    FETCH 5 ROWS;

OUTPUT @res
    TO "/output/Searchlog-query-table.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

 >[!NOTE]
 >Jelenleg nem futtathat olyan táblát, amely ugyanabban a parancsfájlban van, mint ahol a táblázatot létrehozta.

## <a name="next-steps"></a>Következő lépések
* [A Microsoft Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával](data-lake-analytics-data-lake-tools-get-started.md)
* [Az Azure Data Lake Analytics-feladatok figyelése és hibaelhárítása az Azure Portal használatával](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
