---
title: Ismerkedés az U-SQL nyelvével Azure Data Lake Analytics
description: Ismerkedjen meg az U-SQL nyelv alapjaival Azure Data Lake Analyticsban. Megírhatja az első lekérdezést változók használatával a fájlokból származó további adatokra, a sorhalmaz átalakítására és az összesített adatokra.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 06/23/2017
ms.openlocfilehash: 5920f2b5823e568d56d13107200eb0025a654db0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92219921"
---
# <a name="get-started-with-u-sql-in-azure-data-lake-analytics"></a>Ismerkedés az U-SQL Azure Data Lake Analytics

Az U-SQL egy olyan nyelv, amely a deklaratív SQL-t a kényszerített C# használatával kombinálva lehetővé teszi az adatfeldolgozást bármilyen méretben. A U-SQL skálázható, elosztott lekérdezési funkciója révén hatékonyan elemezheti az adatátviteli tárolókat, például a Azure SQL Database. A U-SQL használatával strukturálatlan adatmennyiséget dolgozhat fel az egyéni logikai és UDF olvasására és beszúrására szolgáló séma alkalmazásával. Emellett a U-SQL olyan bővíthetőséget is tartalmaz, amely részletesen szabályozza a méretezést.

## <a name="learning-resources"></a>Képzési források

* A [u-SQL-oktatóanyag](https://aka.ms/usqltutorial) az u-SQL nyelvének többségét bemutató interaktív útmutatót nyújt. Ez a dokumentum az U-SQL-t megtanulni kívánó fejlesztők számára ajánlott olvasmány.
* Az **u-SQL nyelvi szintaxissal** kapcsolatos részletes információkért tekintse meg az [u-SQL nyelvi referenciáját](/u-sql/).
* Az **u-SQL tervezési filozófiájának** megismeréséhez tekintse meg a Visual Studio blogbejegyzését, amely bemutatja az [u-SQL-t – egy olyan nyelvet, amely megkönnyíti a Big adatfeldolgozást](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

## <a name="prerequisites"></a>Előfeltételek

Mielőtt átugorja a dokumentumban található U-SQL-mintákat, olvassa el és fejezze be a következő [oktatóanyagot: u-SQL-szkriptek fejlesztése a Data Lake Tools for Visual Studio használatával](data-lake-analytics-data-lake-tools-get-started.md). Ez az oktatóanyag ismerteti az U-SQL Azure Data Lake Tools for Visual Studio használatával történő használatának mechanikaát.

## <a name="your-first-u-sql-script"></a>Az első U-SQL-szkript

A következő U-SQL-szkript egyszerű, és lehetővé teszi a U-SQL nyelv számos aspektusának megismerését.

```usql
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

OUTPUT @searchlog
    TO "/output/SearchLog-first-u-sql.csv"
    USING Outputters.Csv();
```

Ez a parancsfájl nem rendelkezik átalakítási lépésekkel. Beolvassa a nevű forrásfájlt `SearchLog.tsv` , schematizes, és visszaírja a sorhalmazt egy SearchLog-first-u-sql.csv nevű fájlba.

Figyelje meg a mező adattípusa melletti kérdőjelet `Duration` . Ez azt jelenti, hogy a `Duration` mező értéke null lehet.

### <a name="key-concepts"></a>Fő fogalmak

* **Sorhalmaz változói**: a sorhalmazt előállító összes lekérdezési kifejezés hozzárendelhető egy változóhoz. A U-SQL a T-SQL változó elnevezési mintáját ( `@searchlog` például) követi a parancsfájlban.
* A **kinyerési** kulcsszó beolvassa az adatokat egy fájlból, és az olvasáskor meghatározza a sémát. `Extractors.Tsv` a egy beépített U-SQL-kivonó a tabulátorral tagolt fájlokhoz. Egyéni kiállítók fejlesztése is megtehető.
* A **kimenet** egy sorhalmazból egy fájlba írja az adatokat. `Outputters.Csv()` a egy beépített U-SQL-leválasztó, vesszővel tagolt értékű fájl létrehozásához. Egyéni előállítók is fejleszthetők.

### <a name="file-paths"></a>Fájlelérési utak

A kinyerési és a kimeneti utasítások fájlelérési utakat használnak. A fájlelérési utak értéke lehet abszolút vagy relatív:

A fájl abszolút elérési útja a következő nevű Data Lake Store fájlra hivatkozik `mystore` :

```usql
adl://mystore.azuredatalakestore.net/Samples/Data/SearchLog.tsv
```

Az alábbi elérési út a következővel kezdődik: `"/"` . Az alapértelmezett Data Lake Store fiókban található fájlra hivatkozik:

```usql
/output/SearchLog-first-u-sql.csv
```

## <a name="use-scalar-variables"></a>Skaláris változók használata

Skaláris változókat is használhat, hogy egyszerűbbé tegye a parancsfájlok karbantartását. Az előző U-SQL-szkript a következőképpen is írható:

```usql
DECLARE @in  string = "/Samples/Data/SearchLog.tsv";
DECLARE @out string = "/output/SearchLog-scalar-variables.csv";
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM @in
    USING Extractors.Tsv();
OUTPUT @searchlog
    TO @out
    USING Outputters.Csv();
```

## <a name="transform-rowsets"></a>Sorhalmazok átalakítása

A sorhalmazok átalakításához használja a **Select parancsot** :

```usql
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
@rs1 =
    SELECT Start, Region, Duration
    FROM @searchlog
WHERE Region == "en-gb";
OUTPUT @rs1
    TO "/output/SearchLog-transform-rowsets.csv"
    USING Outputters.Csv();
```

A WHERE záradék [C# logikai kifejezést](/dotnet/csharp/language-reference/operators/index)használ. A C# kifejezés nyelvével saját kifejezéseit és funkcióit is elvégezheti. Akár összetettebb szűrést is végrehajthat, ha azokat a logikai (and) és a (leválasztási) műveletekkel kombinálja.

A következő parancsfájl a DateTime. Parse () metódust és egy-egy társítást használ.

```usql
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
@rs1 =
    SELECT Start, Region, Duration
    FROM @searchlog
WHERE Region == "en-gb";
@rs1 =
    SELECT Start, Region, Duration
    FROM @rs1
    WHERE Start >= DateTime.Parse("2012/02/16") AND Start <= DateTime.Parse("2012/02/17");
OUTPUT @rs1
    TO "/output/SearchLog-transform-datetime.csv"
    USING Outputters.Csv();
```

 >[!NOTE]
 >A második lekérdezés az első sorhalmaz eredményén működik, amely a két szűrő összetett kombinációját hozza létre. A változók neve is felhasználható, a nevek pedig lexikálisan vannak kiválasztva.

## <a name="aggregate-rowsets"></a>Összesített sorhalmazok

Az U-SQL a megszokott SORRENDet, CSOPORTOSÍTÁSt és összesítéseket biztosít.

A következő lekérdezés megkeresi a teljes időtartamot régiónként, majd az első öt időtartamot jeleníti meg sorrendben.

Az U-SQL-sorhalmazok nem őrzik meg a következő lekérdezés sorrendjét. Így a kimenet megrendeléséhez hozzá kell adnia a SORRENDet a kimeneti utasításhoz:

```usql
DECLARE @outpref string = "/output/Searchlog-aggregation";
DECLARE @out1    string = @outpref+"_agg.csv";
DECLARE @out2    string = @outpref+"_top5agg.csv";
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
@rs1 =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM @searchlog
GROUP BY Region;
@res =
    SELECT *
    FROM @rs1
    ORDER BY TotalDuration DESC
    FETCH 5 ROWS;
OUTPUT @rs1
    TO @out1
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
OUTPUT @res
    TO @out2
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

Az U-SQL ORDER BY záradék használatához a FETCH záradékot kell használni egy SELECT kifejezésben.

A U-SQL HAVING záradék használatával korlátozhatja a kimenetet olyan csoportokra, amelyek megfelelnek a HAVING feltételnek:

```usql
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
@res =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM @searchlog
    GROUP BY Region
    HAVING SUM(Duration) > 200;
OUTPUT @res
    TO "/output/Searchlog-having.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

A speciális összesítési forgatókönyvek esetében tekintse meg a U-SQL-dokumentációt az [aggregált, analitikus és hivatkozási függvények](/u-sql/built-in-functions) esetében.

## <a name="next-steps"></a>Következő lépések

* [A Microsoft Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [U-SQL-szkriptek fejlesztése a Data Lake Tools for Visual Studio használatával](data-lake-analytics-data-lake-tools-get-started.md)