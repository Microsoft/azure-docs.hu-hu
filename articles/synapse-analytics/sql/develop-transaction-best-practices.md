---
title: Tranzakciók optimalizálása dedikált SQL-készlethez
description: Útmutató a tranzakciós kód teljesítményének optimalizálásához a dedikált SQL-készletben.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 8b1f820cfca0e352b49d815e2b99d407ccc8ce43
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102505698"
---
# <a name="optimize-transactions-with-dedicated-sql-pool-in-azure-synapse-analytics"></a>Tranzakciók optimalizálása dedikált SQL-készlettel az Azure szinapszis Analyticsben 

Megtudhatja, hogyan optimalizálhatja a tranzakciós kód teljesítményét a dedikált SQL-készletben, miközben minimalizálja a hosszú visszaállítások kockázatát.

## <a name="transactions-and-logging"></a>Tranzakciók és naplózás

A tranzakciók a kapcsolatok adatbázis-működtetői egyik fontos összetevője. A dedikált SQL-készlet tranzakciókat használ az adatmódosítás során. Ezek a tranzakciók explicitek vagy implicitek lehetnek. Az egyszeri INSERT, UPDATE és DELETE utasítások mindegyike implicit tranzakcióra mutat. A Explicit tranzakciók a BEGIN TRAN, a COMMon TRAN vagy a VISSZAÁLLÍTÁSi TRAN szolgáltatást használják. A explicit tranzakciókat jellemzően akkor használják, ha több módosítási utasítást kell egyesíteni egyetlen atomi egységben.

A dedikált SQL-készlet a tranzakciós naplók használatával véglegesíti az adatbázis módosításait. Minden elosztás saját tranzakciónaplóval rendelkezik. A tranzakciónapló-írások automatikusak. Nincs szükség konfigurációra. Bár ez a folyamat garantálja az írást, a rendszer terhelést vezet be. Ezt a hatást a tranzakciós hatékony kód írásával csökkentheti. A tranzakciós szempontból hatékony kód nagyjából két kategóriába esik.

* Minimális naplózási szerkezetek használata, ha lehetséges
* Az adatfeldolgozás hatókörrel rendelkező kötegek használatával a hosszú ideig futó tranzakciók elkerülése érdekében
* Partíciós váltási minta elfogadása egy adott partíció nagy módosításaihoz

## <a name="minimal-vs-full-logging"></a>Minimális és teljes naplózás

A teljesen naplózott műveletekkel ellentétben, amelyek a tranzakciónapló használatával követik nyomon az összes sor módosítását, a minimálisan naplózott műveletek nyomon követhetik a kiosztásokat és a metaadatokat érintő módosításokat. Ezért a minimális naplózási funkció csak azokat az információkat naplózza, amelyek szükségesek a tranzakció visszaállításához a hiba után, vagy egy explicit kérelemhez (VISSZAÁLLÍTÁSi TRAN). Minél kevesebb információt követnek nyomon a tranzakciónaplóban, egy minimálisan naplózott művelet jobb, mint a hasonló méretű, teljesen naplózott művelet. Továbbá, mivel kevesebb írási műveletet végez a tranzakciónaplóban, a rendszer sokkal kevesebb naplózási adatot hoz létre, így az nagyobb I/O-hatékonyságot okoz.

A tranzakció biztonsági korlátai csak a teljesen naplózott műveletekre érvényesek.

> [!NOTE]
> A minimálisan naplózott műveletek részt vehetnek explicit tranzakciókban. A foglalási struktúrák változásainak nyomon követése a minimálisan naplózott műveletek visszaállítása lehetséges.

## <a name="minimally-logged-operations"></a>Minimálisan naplózott műveletek

A következő műveletek képesek minimálisan naplózni:

* CREATE TABLE a SELECT ([CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json))
* Beszúrás.. Válassza
* CREATE INDEX
* AZ ALTER INDEX ÚJRAÉPÍTÉSE
* DROP INDEX
* TRUNCATE TABLE
* DROP TABLE
* MÓDOSÍTÁSI TÁBLA KAPCSOLÓJÁNAK PARTÍCIÓJA

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

> [!NOTE]
> A tranzakciós biztonsági korlát nem érinti a belső adatáthelyezési műveleteket (például a SZÓRÁSt és a SHUFFLE-t).

## <a name="minimal-logging-with-bulk-load"></a>Minimális naplózás tömeges betöltéssel

CTAS és Beszúrás... Válassza a tömeges betöltési műveletek lehetőséget. Mindkettőt azonban a céltábla definíciója befolyásolja, és a terhelési forgatókönyvtől függ. Az alábbi táblázat a tömeges műveletek teljes vagy minimális naplózását ismerteti:  

| Elsődleges index | Betöltési forgatókönyv | Naplózási mód |
| --- | --- | --- |
| Halommemória |Bármelyik |**Minimális** |
| Fürtözött index |Üres céltábla |**Minimális** |
| Fürtözött index |A betöltött sorok nem fedik át a cél meglévő lapjait |**Minimális** |
| Fürtözött index |A betöltött sorok átfedésben vannak a cél meglévő lapjaival |Összes |
| Fürtözött Oszlopcentrikus index |Batch-méret >= 102 400/partícióra igazított eloszlás |**Minimális** |
| Fürtözött Oszlopcentrikus index |Köteg mérete < 102 400/partícióra igazított eloszlás |Összes |

Érdemes megjegyezni, hogy a másodlagos vagy nem fürtözött indexek frissítésére vonatkozó írások mindig teljesen naplózott műveletnek számítanak.

> [!IMPORTANT]
> A dedikált SQL-készlet 60-disztribúcióval rendelkezik. Ezért feltételezve, hogy az összes sort egyenletesen osztják el, és egy partíción belül kikerülnek, a kötegnek legalább 6 144 000 sort kell tartalmaznia, hogy a rendszer minimálisan naplózza a fürtözött Oszlopcentrikus indexbe való íráskor. Ha a tábla particionálva van, és a sorok kiosztási partíció határain belül vannak beszúrva, akkor a partíciók határán 6 144 000 sor szükséges, feltéve, hogy az adateloszlás is megtörténik. Az egyes eloszlások mindegyik partíciójának egymástól függetlennek kell lennie, mint a Beszúrás minimálisan megengedett 102 400-as küszöbértéke.

A fürtözött indexekkel rendelkező, nem üres táblába való betöltés általában a teljesen naplózott és a minimálisan naplózott sorok keverékét is tartalmazhatja. A fürtözött indexek lapok kiegyensúlyozott fastruktúrája (b-Tree). Ha a megírt lap már tartalmaz egy másik tranzakció sorait, akkor ezek az írások teljesen be lesznek jelentkezve. Ha azonban az oldal üres, akkor az adott oldalra való írás a legkevesebb naplóba kerül.

## <a name="optimize-deletes"></a>Törlések optimalizálása

A DELETE egy teljesen naplózott művelet.  Ha egy táblán vagy partíción nagy mennyiségű adattal kell törölnie, gyakran több értelme van `SELECT` a megőrizni kívánt információknak, ami minimálisan naplózott műveletként futtatható.  Az adatválasztáshoz hozzon létre egy új táblát a [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)használatával.  A létrehozást követően az [Átnevezés](/sql/t-sql/statements/rename-transact-sql?view=azure-sqldw-latest&preserve-view=true) használatával cserélje ki a régi táblát az újonnan létrehozott táblázatra.

```sql
-- Delete all sales transactions for Promotions except PromotionKey 2.

--Step 01. Create a new table select only the records we want to kep (PromotionKey 2)
CREATE TABLE [dbo].[FactInternetSales_d]
WITH
(    CLUSTERED COLUMNSTORE INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
)
AS
SELECT     *
FROM     [dbo].[FactInternetSales]
WHERE    [PromotionKey] = 2
OPTION (LABEL = 'CTAS : Delete')
;

--Step 02. Rename the Tables to replace the
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_d] TO [FactInternetSales];
```

## <a name="optimize-updates"></a>Frissítések optimalizálása

A frissítés egy teljesen naplózott művelet.  Ha nagy számú sort kell frissítenie egy táblában vagy partícióban, gyakran sokkal hatékonyabb lehet a minimálisan naplózott műveletek, például a [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) használata.

Az alábbi példában egy teljes táblázatos frissítés lett konvertálva egy CTAS, így minimális naplózás lehetséges.

Ebben az esetben a táblázatban szereplő értékesítésekhez visszamenőlegesen adunk hozzá kedvezményt:

```sql
--Step 01. Create a new table containing the "Update".
CREATE TABLE [dbo].[FactInternetSales_u]
WITH
(    CLUSTERED INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
                )
)
AS
SELECT
    [ProductKey]  
,    [OrderDateKey]
,    [DueDateKey]  
,    [ShipDateKey]
,    [CustomerKey]
,    [PromotionKey]
,    [CurrencyKey]
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber]
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
OPTION (LABEL = 'CTAS : Update')
;

--Step 02. Rename the tables
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_u] TO [FactInternetSales];

--Step 03. Drop the old table
DROP TABLE [dbo].[FactInternetSales_old]
```

> [!NOTE]
> A nagyméretű táblák újbóli létrehozása kihasználhatja a dedikált SQL Pool számítási feladatok kezelési funkcióit. További információ: erőforrás- [osztályok a számítási feladatok kezeléséhez](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="optimize-with-partition-switching"></a>Optimalizálás partíciós váltással

Ha a [táblázatos partíción](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)belül nagy léptékű módosításokkal szembesül, akkor a partíciók váltási mintája logikus. Ha az adatmódosítás jelentős, és több partíciót is átnyúlik, akkor a partíciók megismétlése ugyanazt az eredményt éri el.

A partíciós kapcsolók végrehajtásának lépései a következők:

1. Üres partíció létrehozása
2. A frissítés végrehajtása CTAS
3. A meglévő adatértékek kiváltása a kimenő táblára
4. Váltás az új adatpontra
5. Az adattisztítás

A váltani kívánt partíciók azonosításához azonban hozza létre a következő segítő eljárást.  

```sql
CREATE PROCEDURE dbo.partition_data_get
    @schema_name           NVARCHAR(128)
,    @table_name               NVARCHAR(128)
,    @boundary_value           INT
AS
IF OBJECT_ID('tempdb..#ptn_data') IS NOT NULL
BEGIN
    DROP TABLE #ptn_data
END
CREATE TABLE #ptn_data
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
WITH CTE
AS
(
SELECT     s.name                            AS [schema_name]
,        t.name                            AS [table_name]
,         p.partition_number                AS [ptn_nmbr]
,        p.[rows]                        AS [ptn_rows]
,        CAST(r.[value] AS INT)            AS [boundary_value]
FROM        sys.schemas                    AS s
JOIN        sys.tables                    AS t    ON  s.[schema_id]        = t.[schema_id]
JOIN        sys.indexes                    AS i    ON     t.[object_id]        = i.[object_id]
JOIN        sys.partitions                AS p    ON     i.[object_id]        = p.[object_id]
                                                AND i.[index_id]        = p.[index_id]
JOIN        sys.partition_schemes        AS h    ON     i.[data_space_id]    = h.[data_space_id]
JOIN        sys.partition_functions        AS f    ON     h.[function_id]        = f.[function_id]
LEFT JOIN    sys.partition_range_values    AS r     ON     f.[function_id]        = r.[function_id]
                                                AND r.[boundary_id]        = p.[partition_number]
WHERE i.[index_id] <= 1
)
SELECT    *
FROM    CTE
WHERE    [schema_name]        = @schema_name
AND        [table_name]        = @table_name
AND        [boundary_value]    = @boundary_value
OPTION (LABEL = 'dbo.partition_data_get : CTAS : #ptn_data')
;
GO
```

Ez az eljárás maximalizálja a kód újrafelhasználását, és megtartja a partíció váltásának példáját.

A következő kód a korábban említett lépéseket mutatja be a teljes partíciós váltási rutin eléréséhez.

```sql
--Create a partitioned aligned empty table to switch out the data
IF OBJECT_ID('[dbo].[FactInternetSales_out]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_out]
END

CREATE TABLE [dbo].[FactInternetSales_out]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE 1=2
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Create a partitioned aligned table and update the data in the select portion of the CTAS
IF OBJECT_ID('[dbo].[FactInternetSales_in]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_in]
END

CREATE TABLE [dbo].[FactInternetSales_in]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS
SELECT
    [ProductKey]  
,    [OrderDateKey]
,    [DueDateKey]  
,    [ShipDateKey]
,    [CustomerKey]
,    [PromotionKey]
,    [CurrencyKey]
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber]
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
WHERE    OrderDateKey BETWEEN 20020101 AND 20021231
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Use the helper procedure to identify the partitions
--The source table
EXEC dbo.partition_data_get 'dbo','FactInternetSales',20030101
DECLARE @ptn_nmbr_src INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_src

--The "in" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_in',20030101
DECLARE @ptn_nmbr_in INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_in

--The "out" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_out',20030101
DECLARE @ptn_nmbr_out INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_out

--Switch the partitions over
DECLARE @SQL NVARCHAR(4000) = '
ALTER TABLE [dbo].[FactInternetSales]    SWITCH PARTITION '+CAST(@ptn_nmbr_src AS VARCHAR(20))    +' TO [dbo].[FactInternetSales_out] PARTITION '    +CAST(@ptn_nmbr_out AS VARCHAR(20))+';
ALTER TABLE [dbo].[FactInternetSales_in] SWITCH PARTITION '+CAST(@ptn_nmbr_in AS VARCHAR(20))    +' TO [dbo].[FactInternetSales] PARTITION '        +CAST(@ptn_nmbr_src AS VARCHAR(20))+';'
EXEC sp_executesql @SQL

--Perform the clean-up
TRUNCATE TABLE dbo.FactInternetSales_out;
TRUNCATE TABLE dbo.FactInternetSales_in;

DROP TABLE dbo.FactInternetSales_out
DROP TABLE dbo.FactInternetSales_in
DROP TABLE #ptn_data
```

## <a name="minimize-logging-with-small-batches"></a>Kis kötegekkel történő naplózás csökkentése

A nagyméretű adatmódosítási műveletek esetében érdemes lehet a műveletet adattömbökre vagy kötegekre osztani a munkaegység hatókörének meghatározásához.

A következő kód egy működő példa. A köteg mérete egy triviális számra van állítva, hogy kiemelje a technikát. A valóságban a köteg mérete lényegesen nagyobb lenne.

```sql
SET NO_COUNT ON;
IF OBJECT_ID('tempdb..#t') IS NOT NULL
BEGIN
    DROP TABLE #t;
    PRINT '#t dropped';
END

CREATE TABLE #t
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
SELECT    ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS seq_nmbr
,        SalesOrderNumber
,        SalesOrderLineNumber
FROM    dbo.FactInternetSales
WHERE    [OrderDateKey] BETWEEN 20010101 and 20011231
;

DECLARE    @seq_start        INT = 1
,        @batch_iterator    INT = 1
,        @batch_size        INT = 50
,        @max_seq_nmbr    INT = (SELECT MAX(seq_nmbr) FROM dbo.#t)
;

DECLARE    @batch_count    INT = (SELECT CEILING((@max_seq_nmbr*1.0)/@batch_size))
,        @seq_end        INT = @batch_size
;

SELECT COUNT(*)
FROM    dbo.FactInternetSales f

PRINT 'MAX_seq_nmbr '+CAST(@max_seq_nmbr AS VARCHAR(20))
PRINT 'MAX_Batch_count '+CAST(@batch_count AS VARCHAR(20))

WHILE    @batch_iterator <= @batch_count
BEGIN
    DELETE
    FROM    dbo.FactInternetSales
    WHERE EXISTS
    (
            SELECT    1
            FROM    #t t
            WHERE    seq_nmbr BETWEEN  @seq_start AND @seq_end
            AND        FactInternetSales.SalesOrderNumber        = t.SalesOrderNumber
            AND        FactInternetSales.SalesOrderLineNumber    = t.SalesOrderLineNumber
    )
    ;

    SET @seq_start = @seq_end
    SET @seq_end = (@seq_start+@batch_size);
    SET @batch_iterator +=1;
END
```

## <a name="pause-and-scaling-guidance"></a>Útmutató szüneteltetése és skálázása

Az Azure szinapszis Analytics lehetővé teszi a dedikált SQL-készlet [szüneteltetését, folytatását és méretezését](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) igény szerint. 

Ha szünetelteti vagy méretezi a dedikált SQL-készletet, fontos tisztában lennie azzal, hogy a repülés közbeni tranzakciók azonnal megszűnnek; a nyitott tranzakciók visszaállításának visszavonása. 

Ha a munkaterhelés a szüneteltetési vagy a skálázási művelet előtt hosszú ideig futó és hiányos adatmódosítást adott ki, akkor ennek a munkának vissza kell maradnia. Ezzel a művelettel a dedikált SQL-készlet szüneteltetéséhez vagy méretezéséhez szükséges idő is hatással lehet. 

> [!IMPORTANT]
> Mindkettő `UPDATE` és `DELETE` teljesen naplózott művelet, így ezek a visszavonási/ismétlési műveletek jelentősen hosszabb időt vehetnek igénybe, mint az egyenértékű, minimálisan naplózott műveletek.

A legjobb megoldás, ha a dedikált SQL-készlet szüneteltetése vagy skálázása előtt a repülési adatmódosítási tranzakciók befejeződik. Előfordulhat azonban, hogy ez a forgatókönyv nem mindig praktikus. A hosszú visszaállítás kockázatának enyhítéséhez vegye figyelembe az alábbi lehetőségek egyikét:

* A hosszú ideig futó műveletek újraírása a [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) használatával
* A művelet felosztása darabokra; a sorok egy részhalmazán működik

## <a name="next-steps"></a>Következő lépések

Az elkülönítési szintekkel és a tranzakciós korlátokkal kapcsolatos további tudnivalókért tekintse meg a [DEDIKÁLT SQL-készlet tranzakciói](develop-transactions.md) című témakört.  Az egyéb ajánlott eljárások áttekintését lásd: [DEDIKÁLT SQL Pool](best-practices-sql-pool.md)– ajánlott eljárások.
