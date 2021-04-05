---
title: CREATE TABLE A SELECT (CTAS)
description: Magyarázat és példák az CREATE TABLE AS SELECT (CTAS) utasításra a szinapszis SQL-ben megoldások fejlesztéséhez.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/26/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seoapril2019, azure-synapse
ms.openlocfilehash: 68bab754142538fc6067cf2593ae6244a03a48d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98734814"
---
# <a name="create-table-as-select-ctas"></a>CREATE TABLE A SELECT (CTAS)

Ez CREATE TABLE a cikk ismerteti a CTAS-T a szinapszis SQL-ben a megoldások fejlesztésére szolgáló SELECT (kiválasztva) T-SQL-utasítással. A cikk emellett példákat is tartalmaz.

## <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

A [CREATE TABLE as Select](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (CTAS) utasítás az elérhető legfontosabb T-SQL-funkciók egyike. A CTAS egy párhuzamos művelet, amely egy SELECT utasítás kimenete alapján létrehoz egy új táblázatot. A CTAS az a legegyszerűbb és leggyorsabb módszer, amellyel egyetlen paranccsal hozhatók létre és helyezhetők be az adattábla.

## <a name="selectinto-vs-ctas"></a>Válassza a... lehetőséget. A vs. CTAS

A CTAS a [Select... testreszabható verziója. INTO](/sql/t-sql/queries/select-into-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) utasítás.

A következő példa egy egyszerű KIJELÖLÉSt mutat be... A

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

Válassza a... lehetőséget. A-ben nem teszi lehetővé, hogy a művelet részeként módosítsa a terjesztési módszert vagy az index típusát. A létrehozásához `[dbo].[FactInternetSales_new]` használja a ROUND_ROBIN alapértelmezett terjesztési típusát, és a FÜRTÖZÖTT OSZLOPCENTRIKUS index alapértelmezett felépítését.

A CTAS segítségével azonban megadhatja a tábla és a tábla szerkezetének eloszlását is. Az előző példa átalakítása CTAS:

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
 DISTRIBUTION = ROUND_ROBIN
 ,CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales];
```

> [!NOTE]
> Ha csak a CTAS-művelet indexét próbálja módosítani, és a forrástábla kivonatot terjesztett, akkor ugyanazt a terjesztési oszlopot és adattípust kell fenntartania. Ezzel elkerülhető a művelet során felmerülő adatáthelyezés, ami hatékonyabb.

## <a name="use-ctas-to-copy-a-table"></a>Táblázat másolása CTAS használatával

A CTAS egyik leggyakoribb felhasználási célja, hogy a DDL módosításához létrehozza a tábla másolatát. Tegyük fel, hogy eredetileg létrehozta a táblát `ROUND_ROBIN` , és most módosítani szeretné egy oszlopra terjesztett táblára. A CTAS a terjesztési oszlop módosítása. A CTAS a particionálás, az indexelés vagy az oszlopok típusának módosítására is használhatja.

Tegyük fel, hogy létrehozta ezt a táblázatot az alapértelmezett terjesztési típusával `ROUND_ROBIN` , és nem határoz meg terjesztési oszlopot a alkalmazásban `CREATE TABLE` .

```sql
CREATE TABLE FactInternetSales
(
    ProductKey int NOT NULL,
    OrderDateKey int NOT NULL,
    DueDateKey int NOT NULL,
    ShipDateKey int NOT NULL,
    CustomerKey int NOT NULL,
    PromotionKey int NOT NULL,
    CurrencyKey int NOT NULL,
    SalesTerritoryKey int NOT NULL,
    SalesOrderNumber nvarchar(20) NOT NULL,
    SalesOrderLineNumber tinyint NOT NULL,
    RevisionNumber tinyint NOT NULL,
    OrderQuantity smallint NOT NULL,
    UnitPrice money NOT NULL,
    ExtendedAmount money NOT NULL,
    UnitPriceDiscountPct float NOT NULL,
    DiscountAmount float NOT NULL,
    ProductStandardCost money NOT NULL,
    TotalProductCost money NOT NULL,
    SalesAmount money NOT NULL,
    TaxAmt money NOT NULL,
    Freight money NOT NULL,
    CarrierTrackingNumber nvarchar(25),
    CustomerPONumber nvarchar(25));
```

Most létre kell hoznia egy új példányt a táblából, amelynek segítségével `Clustered Columnstore Index` kihasználhatja a fürtözött oszlopcentrikus-táblák teljesítményét. Ezt a táblázatot is el kell osztania `ProductKey` , mert az az oszlophoz való csatlakozást tervezi, és el szeretné kerülni az adatáthelyezést az illesztések során `ProductKey` . Végül pedig a particionálást is fel kell vennie `OrderDateKey` , így a régi partíciók eldobásával gyorsan törölheti a régi adatbázisokat. Itt látható a CTAS utasítás, amely egy új táblába másolja a régi táblát.

```sql
CREATE TABLE FactInternetSales_new
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = HASH(ProductKey),
    PARTITION
    (
        OrderDateKey RANGE RIGHT FOR VALUES
        (
        20000101,20010101,20020101,20030101,20040101,20050101,20060101,20070101,20080101,20090101,
        20100101,20110101,20120101,20130101,20140101,20150101,20160101,20170101,20180101,20190101,
        20200101,20210101,20220101,20230101,20240101,20250101,20260101,20270101,20280101,20290101
        )
    )
)
AS SELECT * FROM FactInternetSales;
```

Végül átnevezheti a táblázatokat, és felcserélheti az új táblázatba, majd elhúzhatja a régi táblát.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="use-ctas-to-work-around-unsupported-features"></a>A CTAS használata a nem támogatott funkciók megkerülésére

A CTAS használatával az alább felsorolt nem támogatott funkciók körét is megtalálhatja. Ez a módszer gyakran bizonyulhat hasznosnak, mivel nem csak a kódnak megfelelőnek kell lennie, de gyakran gyorsabban futnak a szinapszis SQL-ben. Ez a teljesítmény teljesen párhuzamos kialakításának eredménye. A forgatókönyvek a következők:

* ANSI-ILLESZTÉSek a frissítésekhez
* A törlésekhez tartozó ANSI-illesztések
* MERGE utasítás

> [!TIP]
> Próbálja ki először a "CTAS" kifejezést. A CTAS használatával történő probléma megoldása általában jó megoldás, még akkor is, ha az eredmény több adattal is rendelkezik.

## <a name="ansi-join-replacement-for-update-statements"></a>ANSI-csatlakozás a frissítési utasításokhoz

Előfordulhat, hogy összetett frissítéssel rendelkezik. A frissítés a frissítés vagy a törlés végrehajtásához a kettőnél több táblát is összekapcsol az ANSI JOIN szintaxis használatával.

Képzelje el, hogy frissítenie kell ezt a táblázatot:

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
( [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
, [CalendarYear]                    SMALLINT        NOT NULL
, [TotalSalesAmount]                MONEY            NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
);
```

Lehetséges, hogy az eredeti lekérdezés a következő példához hasonlóan néz ki:

```sql
UPDATE    acs
SET        [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT [EnglishProductCategoryName]
        , [CalendarYear]
        , SUM([SalesAmount])                AS [TotalSalesAmount]
        FROM    [dbo].[FactInternetSales]        AS s
        JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
        JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
        JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
        JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
        WHERE     [CalendarYear] = 2004
        GROUP BY
                [EnglishProductCategoryName]
        ,        [CalendarYear]
        ) AS fis
ON    [acs].[EnglishProductCategoryName]    = [fis].[EnglishProductCategoryName]
AND    [acs].[CalendarYear]                = [fis].[CalendarYear];
```

A szinapszis SQL nem támogatja az ANSI-illesztéseket `FROM` egy `UPDATE` utasítás záradékában, ezért módosítás nélkül nem használhatja az előző példát.

Az előző példát lecserélve egy CTAS és egy implicit illesztés kombinációját is használhatja:

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT    ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0) AS [EnglishProductCategoryName]
, ISNULL(CAST([CalendarYear] AS SMALLINT),0)  AS [CalendarYear]
, ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)  AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]        AS s
JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
WHERE     [CalendarYear] = 2004
GROUP BY [EnglishProductCategoryName]
, [CalendarYear];

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]  = AnnualCategorySales.[CalendarYear] ;

--Drop the interim table
DROP TABLE CTAS_acs;
```

## <a name="ansi-join-replacement-for-merge"></a>ANSI illesztés cseréje EGYESÍTÉShez 

Az Azure szinapszis Analyticsben az [Egyesítés](/sql/t-sql/statements/merge-transact-sql?view=azure-sqldw-latest&preserve-view=true) (előzetes verzió) és a TARGET nem egyezik meg a cél, hogy kivonatos elosztott tábla legyen.  A felhasználók az ANSI-CSATLAKOZÁSt [frissítéssel](/sql/t-sql/queries/update-transact-sql?view=azure-sqldw-latest&preserve-view=true) vagy [törléssel](/sql/t-sql/statements/delete-transact-sql?view=azure-sqldw-latest&preserve-view=true) megkerülő megoldásként használhatják a céltábla adatainak módosítására egy másik táblához való csatlakozás eredménye alapján.  Íme egy példa.

```sql
CREATE TABLE dbo.Table1   
    (ColA INT NOT NULL, ColB DECIMAL(10,3) NOT NULL);  
GO  
CREATE TABLE dbo.Table2   
    (ColA INT NOT NULL, ColB DECIMAL(10,3) NOT NULL);  
GO  
INSERT INTO dbo.Table1 VALUES(1, 10.0);  
INSERT INTO dbo.Table2 VALUES(1, 0.0);  
GO  
UPDATE dbo.Table2   
SET dbo.Table2.ColB = dbo.Table2.ColB + dbo.Table1.ColB  
FROM dbo.Table2   
    INNER JOIN dbo.Table1   
    ON (dbo.Table2.ColA = dbo.Table1.ColA);  
GO  
SELECT ColA, ColB   
FROM dbo.Table2;

```

## <a name="explicitly-state-data-type-and-nullability-of-output"></a>Explicit módon állapot adattípus és a kimenet nullára

A kód áttelepítésekor előfordulhat, hogy az ilyen típusú kódolási mintán keresztül futtatja a következőt:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f;
```

Előfordulhat, hogy érdemes áttelepítenie ezt a kódot a CTAS-re, és helyes lenne. Itt azonban rejtett probléma van.

A következő kód nem eredményezi ugyanazt az eredményt:

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result;
```

Figyelje meg, hogy a "result" oszlop továbbítja a kifejezés adattípusát és nullára vonatkozó értékeit. Ha nem vigyáz rá, az adattípusok továbbítása enyhe eltéréseket eredményezhet az értékekben.

Próbálja ki ezt a példát:

```sql
SELECT result,result*@d
from result;

SELECT result,result*@d
from ctas_r;
```

Az eredményként tárolt érték eltér. Mivel az eredmény oszlopban megőrzött érték más kifejezésekben is szerepel, a hiba még jelentősebb lesz.

![Képernyőkép a CTAS eredményeiről](./media/sql-data-warehouse-develop-ctas/ctas-results.png)

Ez az adatáttelepítés esetében fontos. Bár a második lekérdezés vitathatatlanul pontosabb, probléma merült fel. Az adatok eltérnek a forrásoldali rendszertől, és ez az áttelepítés során az integritással kapcsolatos kérdésekhez vezet. Ez az egyik ritka eset, amikor a "rossz" válasz valójában a megfelelő.

Ennek az az oka, hogy a két eredmény közötti eltérések a implicit típusú öntvények miatt láthatók. Az első példában a tábla az oszlop definícióját határozza meg. A sor beszúrásakor egy implicit típusú konverzió történik. A második példában nincs implicit típusú átalakítás, mivel a kifejezés az oszlop adattípusát határozza meg.

Figyelje meg azt is, hogy a második példában szereplő oszlop üres oszlopként van definiálva, míg az első példában nem. Ha a tábla az első példában lett létrehozva, az oszlop nullát explicit módon definiálták. A második példában a kifejezést a kifejezésre hagyták, és alapértelmezés szerint NULL definíciót eredményezne.

A problémák megoldásához explicit módon be kell állítania a típus átalakítását és a nullát a CTAS utasítás SELECT részében. Ezek a tulajdonságok nem állíthatók be a következőben: "CREATE TABLE".
A következő példa a kód kijavítását mutatja be:

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Vegye figyelembe a következőket:

* A CAST vagy a CONVERT is használható.
* Ne EGYESÍTse a ISNULL, hogy kényszerítse a NULLát. Tekintse meg a következő megjegyzést.
* A ISNULL a legkülső függvény.
* A ISNULL második része állandó, 0.

> [!NOTE]
> Ahhoz, hogy a nullák helyesen legyenek beállítva, elengedhetetlen a ISNULL használata és a nem EGYESÍTÉS. Az EGYESÍTÉS nem determinisztikus-függvény, ezért a kifejezés eredménye mindig üres lesz. A ISNULL eltér. Ez a determinisztikus. Ezért ha a ISNULL függvény második része állandó vagy literál, az eredményül kapott érték nem NULL.

A számítások integritásának biztosítása szintén fontos a tábla partíciós váltásakor. Képzelje el, hogy ezt a táblázatot a tábla határozza meg:

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
, [product]   INT     NOT NULL
, [store]     INT     NOT NULL
, [quantity]  INT     NOT NULL
, [price]     MONEY   NOT NULL
, [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
);
```

Az összeg mező azonban számított kifejezés. Nem a forrásadatok részét képezi.

Particionált adatkészlet létrehozásához a következő kódot érdemes használni:

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]
,   [product]
,   [store]
,   [quantity]
,   [price]
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

A lekérdezés teljesen jól fut. A probléma a partíciós kapcsoló kipróbálásakor jön. A tábla definíciói nem egyeznek. Ahhoz, hogy a táblázat-definíciók megegyezzenek, módosítsa a CTAS egy függvény hozzáadásához, `ISNULL` amely az oszlop nullára vonatkozó attribútumának megőrzését végzi.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
  [date]
, [product]
, [store]
, [quantity]
, [price]
, ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Láthatja, hogy a típus konzisztenciája és a CTAS tulajdonság fenntartása egy mérnöki ajánlott eljárás. Segít megőrizni az integritást a számításokban, és biztosítja a partíciók váltásának lehetőségét is.

A CTAS a szinapszis SQL egyik legfontosabb utasítása. Győződjön meg róla, hogy alaposan megértette. Tekintse meg a [CTAS dokumentációját](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="next-steps"></a>Következő lépések

További fejlesztési tippekért tekintse meg a [fejlesztés áttekintése](sql-data-warehouse-overview-develop.md)című témakört.