---
title: Csoportosítási lehetőségek használata
description: Tippek az Azure szinapszis Analytics szolgáltatásban a dedikált SQL-készletek lehetőségeinek megvalósításához.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 3f0879aa9b6f9e084d0c51f0bb371740d333c1b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98683256"
---
# <a name="group-by-options-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Csoportosítási lehetőségek a dedikált SQL-készletek számára az Azure szinapszis Analyticsben

Ebből a cikkből megtudhatja, hogyan hozhatja ki a csoportok beállításait a dedikált SQL-készletekben.

## <a name="what-does-group-by-do"></a>Mit tesz a GROUP BY do?

A [Group By](/sql/t-sql/queries/select-group-by-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) T-SQL záradék összesíti az adatokat egy sorok összesítési halmazával. A CSOPORTOSÍTÁSi lehetőségek a dedikált SQL-készlet által nem támogatott beállítások. Ezek a beállítások megkerülő megoldásokkal rendelkeznek, amelyek a következők:

* Csoportosítás ÖSSZESÍTÉSsel
* CSOPORTOSÍTÁSI KÉSZLETEK
* Csoportosítás a KOCKÁval

## <a name="rollup-and-grouping-sets-options"></a>Kumulatív és csoportosítási beállítások

A legegyszerűbb lehetőség az, hogy a UNION ALL használatával végezze el az összesítést az explicit szintaxis helyett. Az eredmény pontosan ugyanaz.

A következő példa a GROUP BY utasítást használja a kumulatív beállítással:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount)             AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t       ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY ROLLUP (
                        [SalesTerritoryCountry]
                ,       [SalesTerritoryRegion]
                )
;
```

A kumulatív használatával az előző példa a következő összesítéseket kéri:

* Ország és régió
* Ország
* Végösszeg

Ha az ÖSSZESÍTÉSt cserélni szeretné, és ugyanazokat az eredményeket adja vissza, használhatja a UNION ALL utasítást, és explicit módon megadhatja a szükséges összesítéseket:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
UNION ALL
SELECT [SalesTerritoryCountry]
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
UNION ALL
SELECT NULL
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey;
```

A CSOPORTOSÍTÁSi készletek lecseréléséhez a minta elve érvényes. Csak a megtekinteni kívánt összesítési szintekhez kell létrehoznia a UNION összes szakaszt.

## <a name="cube-options"></a>Adatkocka-beállítások

Létrehozhat egy CSOPORTOT az adatkockával a UNION ALL megközelítés használatával. A probléma az, hogy a kód gyors és nehézkes lehet. A probléma megoldásához ezt a fejlettebb megközelítést használhatja.

Az előző példában az első lépés a "Cube" meghatározása, amely meghatározza a létrehozni kívánt összesítési szinteket.

Jegyezze fel a két származtatott tábla kereszt-összekapcsolását, mivel ez az összes szintet létrehozta nekünk. A kód további része a formázáshoz:

```sql
CREATE TABLE #Cube
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
WITH GrpCube AS
(SELECT    CAST(ISNULL(Country,'NULL')+','+ISNULL(Region,'NULL') AS NVARCHAR(50)) as 'Cols'
,          CAST(ISNULL(Country+',','')+ISNULL(Region,'') AS NVARCHAR(50))  as 'GroupBy'
,          ROW_NUMBER() OVER (ORDER BY Country) as 'Seq'
FROM       ( SELECT 'SalesTerritoryCountry' as Country
             UNION ALL
             SELECT NULL
           ) c
CROSS JOIN ( SELECT 'SalesTerritoryRegion' as Region
             UNION ALL
             SELECT NULL
           ) r
)
SELECT Cols
,      CASE WHEN SUBSTRING(GroupBy,LEN(GroupBy),1) = ','
            THEN SUBSTRING(GroupBy,1,LEN(GroupBy)-1)
            ELSE GroupBy
       END AS GroupBy  --Remove Trailing Comma
,Seq
FROM GrpCube;
```

Az alábbi képen a CTAS eredményei láthatók:

![Csoportosítás adatkockával](./media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

A második lépés egy cél tábla megadása a közbenső eredmények tárolásához:

```sql
DECLARE
 @SQL NVARCHAR(4000)
,@Columns NVARCHAR(4000)
,@GroupBy NVARCHAR(4000)
,@i INT = 1
,@nbr INT = 0
;
CREATE TABLE #Results
(
 [SalesTerritoryCountry] NVARCHAR(50)
,[SalesTerritoryRegion]  NVARCHAR(50)
,[TotalSalesAmount]      MONEY
)
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
;
```

A harmadik lépés az összesítést végző oszlopok adatkockájának áthurkolása. A lekérdezés egyszer fog futni a #Cube ideiglenes tábla minden sorában. Az eredmények a #Results Temp táblában tárolódnak:

```sql
SET @nbr =(SELECT MAX(Seq) FROM #Cube);

WHILE @i<=@nbr
BEGIN
    SET @Columns = (SELECT Cols    FROM #Cube where seq = @i);
    SET @GroupBy = (SELECT GroupBy FROM #Cube where seq = @i);

    SET @SQL ='INSERT INTO #Results
              SELECT '+@Columns+'
              ,      SUM(SalesAmount) AS TotalSalesAmount
              FROM  dbo.factInternetSales s
              JOIN  dbo.DimSalesTerritory t  
              ON s.SalesTerritoryKey = t.SalesTerritoryKey
              '+CASE WHEN @GroupBy <>''
                     THEN 'GROUP BY '+@GroupBy ELSE '' END

    EXEC sp_executesql @SQL;
    SET @i +=1;
END
```

Végül visszaállíthatja az eredményeket az #Results ideiglenes táblából való olvasással:

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

A kód szakaszokra bontásával és a hurkos szerkezet létrehozásával a kód hatékonyabban kezelhető és karbantartható lesz.

## <a name="next-steps"></a>Következő lépések

További fejlesztési tippek: a [fejlesztés áttekintése](sql-data-warehouse-overview-develop.md).
