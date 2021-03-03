---
title: CSOPORTOSÍTÁSi lehetőségek használata a szinapszis SQL-ben
description: A szinapszis SQL lehetővé teszi a megoldások fejlesztését különböző CSOPORTOSÍTÁSi beállításokkal.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: c2e1ddbfb87df40a0e3683e7bca7539c26191a7b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101671311"
---
# <a name="group-by-options-in-synapse-sql"></a>CSOPORTOSÍTÁSi lehetőségek a szinapszis SQL-ben

A szinapszis SQL lehetővé teszi a megoldások fejlesztését különböző CSOPORTOSÍTÁSi beállításokkal. 

## <a name="what-group-by-does"></a>A csoportosítás alapja

A [Group By](/sql/t-sql/queries/select-group-by-transact-sql?view=azure-sqldw-latest&preserve-view=true) T-SQL záradék összesíti az adatokat egy sorok összesítési halmazával.

A kiszolgáló nélküli SQL-készlet a CSOPORTOSÍTÁSi lehetőségek teljes körét támogatja. A dedikált SQL-készlet korlátozott számú CSOPORTOSÍTÁSi lehetőséget támogat.

## <a name="group-by-options-supported-in-dedicated-sql-pool"></a>Csoportosítás a dedikált SQL-készletben támogatott beállítások alapján

A CSOPORTOSÍTÁSi lehetőségek a dedikált SQL-készlet által nem támogatott beállítások. Ezek a beállítások megkerülő megoldásokkal rendelkeznek, amelyek a következők:

* Csoportosítás ÖSSZESÍTÉSsel
* CSOPORTOSÍTÁSI KÉSZLETEK
* Csoportosítás a KOCKÁval

### <a name="rollup-and-grouping-sets-options"></a>Kumulatív és csoportosítási beállítások

A legegyszerűbb lehetőség az, ha az Unió összes elemét használja a kumulatív végrehajtáshoz, és nem az explicit szintaxisra támaszkodik. Az eredmény pontosan ugyanaz

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

### <a name="cube-options"></a>Adatkocka-beállítások

Létrehozhat egy CSOPORTOT az adatkockával a UNION ALL megközelítés használatával. A probléma az, hogy a kód gyors és nehézkes lehet. A probléma megoldásához ezt a fejlettebb megközelítést használhatja.

Az első lépés a "Cube" meghatározása, amely meghatározza a létrehozni kívánt összesítési szinteket. Jegyezze fel a két származtatott tábla több ILLESZTÉSét, mivel az összes szintet létrehozta. A kód további része a formázáshoz.

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

Az alábbi képen a [CREATE TABLE](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true)eredményei láthatók:

![Csoportosítás adatkockával](./media/develop-group-by-options/develop-group-by-cube.png)

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

További fejlesztési tippek: a [fejlesztés áttekintése](develop-overview.md).
