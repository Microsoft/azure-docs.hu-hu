---
title: Az elosztott táblák tervezési útmutatója
description: Javaslatok a kivonatok elosztott és ciklikusan elosztott tábláinak tervezéséhez, dedikált SQL-készlettel az Azure szinapszis Analytics szolgáltatásban.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 5aefe869041d9fff8112b6aa380961ca6568ae0b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98673569"
---
# <a name="guidance-for-designing-distributed-tables-using-dedicated-sql-pool-in-azure-synapse-analytics"></a>Útmutató elosztott táblák tervezéséhez dedikált SQL-készlet használatával az Azure szinapszis Analyticsben

Javaslatok kivonatok elosztott és ciklikusan elosztott tábláinak tervezéséhez dedikált SQL-készletekben.

Ez a cikk feltételezi, hogy ismeri az adateloszlási és adatáthelyezési fogalmakat a dedikált SQL-készletben.  További információ: [Azure szinapszis Analytics-architektúra](massively-parallel-processing-mpp-architecture.md).

## <a name="what-is-a-distributed-table"></a>Mi az elosztott tábla?

Egy elosztott tábla egyetlen táblázatként jelenik meg, de a sorok ténylegesen 60-eloszlásban vannak tárolva. A sorok kivonattal vagy ciklikus multiplexelés algoritmussal vannak elosztva.  

**Kivonat – az elosztott táblák** javítják a lekérdezési teljesítményt a nagyméretű táblákon, és a cikk középpontjában állnak. A **ciklikusan** megjelenő táblázatok a betöltési sebesség javításához hasznosak. Ezek a tervezési döntések jelentős hatással vannak a lekérdezések és a teljesítmény-betöltés javítására.

Egy másik tábla tárolási lehetőség egy kis tábla replikálása az összes számítási csomóponton. További információ: [tervezési útmutató a replikált táblákhoz](design-guidance-for-replicated-tables.md). A három lehetőség közül a gyors választáshoz tekintse meg az elosztott táblák című részt a [táblázatok áttekintésében](sql-data-warehouse-tables-overview.md).

A tábla kialakításának részeként a lehető legnagyobb mértékben megismerheti az adatait és az adatlekérdezés módját.  Vegyük például a következő kérdéseket:

- Mekkora a táblázat?
- Milyen gyakran frissül a tábla?
- Van egy dedikált SQL-készletben a tény-és dimenziós táblák?

### <a name="hash-distributed"></a>Kivonat kiosztva

A kivonatok – az elosztott tábla a számítási csomópontok között egy determinisztikus kivonatoló függvény használatával osztja el a táblázat sorait egy [eloszláshoz](massively-parallel-processing-mpp-architecture.md#distributions).

![Elosztott tábla](./media/sql-data-warehouse-tables-distribute/hash-distributed-table.png "Elosztott tábla")  

Mivel az azonos értékek mindig ugyanazzal az eloszlással vannak kialakítva, az SQL Analytics beépített ismeretekkel rendelkezik a sorok helyeiről. A dedikált SQL-készletben ez az információ az adatáthelyezés minimalizálására szolgál a lekérdezések során, ami javítja a lekérdezési teljesítményt.

A kivonattal terjesztett táblázatok jól működnek a csillag sémában található nagyméretű táblákhoz. Nagyon nagy mennyiségű sorból állhatnak, és továbbra is nagy teljesítmény érhető el. Természetesen vannak olyan kialakítási megfontolások, amelyek segítenek az elosztott rendszer által biztosított teljesítmény megszerzésében. A megfelelő terjesztési oszlop kiválasztása az ebben a cikkben ismertetett szempontok egyike.

Használjon kivonatoló eloszlású táblázatot, ha:

- A lemez mérete nagyobb, mint 2 GB.
- A tábla gyakori INSERT, Update és DELETE műveletekkel rendelkezik.

### <a name="round-robin-distributed"></a>Ciklikus multiplexelés – elosztott

A Round-Robin elosztott tábla egyenletesen osztja el a táblázat sorait az összes eloszlásban. A sorok eloszláshoz való hozzárendelése véletlenszerű. A kivonatok elosztott tábláitól eltérően az egyenlő értékekkel rendelkező sorok nem garantáltan ugyanahhoz a terjesztéshez rendelhetők hozzá.

Ennek eredményeképpen előfordulhat, hogy a rendszernek időnként meg kell hívnia egy adatáthelyezési műveletet, hogy jobban meg tudja szervezni az adatait, mielőtt feloldja a lekérdezést.  Ez az extra lépés lelassíthatja a lekérdezéseket. Egy ciklikus multiplexelés-táblázathoz való csatlakozáshoz például általában a sorok újrakeverése szükséges, ami a teljesítmény.

A következő helyzetekben érdemes lehet a táblázat ciklikus multiplexelés használatával történő terjesztését használni:

- Az első lépések egyszerű kiindulási pontként, mivel ez az alapértelmezett
- Ha nincs nyilvánvaló csatlakozási kulcs
- Ha nincs jó jelölt oszlop a táblát elosztó kivonathoz
- Ha a tábla nem oszt meg közös illesztési kulcsot más táblákkal
- Ha az illesztés kevésbé jelentős, mint a lekérdezés többi illesztése
- Ha a tábla ideiglenes előkészítési tábla

A [New York-i taxik-adatgyűjtési útmutató betöltési](./load-data-from-azure-blob-storage-using-copy.md#load-the-data-into-your-data-warehouse) példát mutat be arra, hogyan tölthetők be az adathalmazok egy ciklikus időszeleteléses előkészítés

## <a name="choosing-a-distribution-column"></a>Terjesztési oszlop kiválasztása

A kivonat – elosztott tábla olyan terjesztési oszlopot tartalmaz, amely a kivonatoló kulcs. A következő kód például egy kivonattal elosztott táblázatot hoz létre a termékkel, amely a terjesztési oszlop.

```sql
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,  DISTRIBUTION = HASH([ProductKey])
)
;
```

A terjesztési oszlopban tárolt adatforgalom frissítve lehet. A terjesztési oszlopban lévő adatfrissítések adatshuffle-művelethez vezethetnek.

A terjesztési oszlop kiválasztása fontos tervezési döntés, mivel az oszlopban szereplő értékek határozzák meg a sorok elosztásának módját. A legjobb választás több tényezőtől függ, és általában kompromisszumokat is magában foglal. A terjesztési oszlop kiválasztása után nem módosítható.  

Ha az első alkalommal nem választotta ki a legjobb oszlopot, használhatja a [CREATE TABLE as Select (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) parancsot a tábla újbóli létrehozásához egy másik terjesztési oszloppal.

### <a name="choose-a-distribution-column-with-data-that-distributes-evenly"></a>Válasszon ki egy olyan terjesztési oszlopot, amely egyenletesen osztja el az adatelosztást

A legjobb teljesítmény érdekében az összes eloszlásnak nagyjából azonos számú sorral kell rendelkeznie. Ha egy vagy több eloszlás nem arányos számú sort tartalmaz, egyes disztribúciók egy párhuzamos lekérdezés részét fejezik be mások előtt. Mivel a lekérdezés nem hajtható végre, amíg az összes eloszlás befejezte a feldolgozást, az egyes lekérdezések csak a leglassabb eloszlással gyorsak.

- Az adattorzítás azt jelenti, hogy az adatforgalom nem egyenletesen oszlik meg a disztribúciók között
- Az elferdítés azt jelenti, hogy egyes eloszlások a párhuzamos lekérdezések futtatásakor több időt is igénybe vehetnek. Ez akkor fordulhat elő, ha az adatdöntés megtörténik.
  
A párhuzamos feldolgozás kiegyensúlyozásához válasszon egy terjesztési oszlopot, amely a következő:

- **Számos egyedi értékkel rendelkezik.** Az oszlopnak lehet néhány ismétlődő értéke. Azonban az azonos értékkel rendelkező sorok ugyanahhoz a disztribúcióhoz vannak rendelve. Mivel a 60 eloszlások vannak, az oszlopnak legalább 60 egyedi értékkel kell rendelkeznie.  Általában az egyedi értékek száma sokkal nagyobb.
- **Nem rendelkezik NULLával, vagy csak néhány NULLával rendelkezik.** Ha például az oszlopban szereplő összes érték NULL értékű, az összes sor ugyanahhoz a disztribúcióhoz van rendelve. Ennek eredményeképpen a lekérdezés feldolgozása egy eloszlásra van elferdítve, és nem használja a párhuzamos feldolgozást.
- **Nem dátum típusú oszlop**. Az azonos dátummal rendelkező összes adat ugyanabban a disztribúcióban található. Ha több felhasználó mindegyike azonos dátummal van szűrve, akkor a 60-disztribúciók közül mindössze 1 az összes feldolgozási munkát végrehajtja.

### <a name="choose-a-distribution-column-that-minimizes-data-movement"></a>Az adatáthelyezést lecsökkentő terjesztési oszlop kiválasztása

A lekérdezési eredmények helyes lekérdezéséhez az adatok az egyik számítási csomópontról egy másikra helyezhetők át. Az adatáthelyezés általában akkor történik, amikor a lekérdezések összekapcsolással és összesítésekkel rendelkeznek az elosztott táblákon. Egy olyan terjesztési oszlop kiválasztása, amely segít az adatáthelyezés minimalizálásában, az egyik legfontosabb stratégia a dedikált SQL-készlet teljesítményének optimalizálásához.

Az adatáthelyezés minimalizálásához válasszon ki egy terjesztési oszlopot:

- A,, `JOIN` , `GROUP BY` `DISTINCT` `OVER` és `HAVING` záradékokban van használatban. Ha két nagy egyedkapcsolat-táblázat gyakran csatlakozik egymáshoz, a lekérdezés teljesítménye javul, ha mindkét táblát az egyik illesztési oszlopon osztja el.  Ha egy tábla nem használatos az illesztésekben, érdemes lehet a táblát olyan oszlopra terjeszteni, amely gyakran szerepel a `GROUP BY` záradékban.
- *Nem* használatos a `WHERE` záradékokban. Ez leszűkítheti a lekérdezést úgy, hogy az ne fusson az összes disztribúción.
- *Nem* dátum típusú oszlop. WHERE záradékok gyakran dátum szerint vannak szűrve.  Ebben az esetben az összes feldolgozás csak néhány disztribúción futhat.

### <a name="what-to-do-when-none-of-the-columns-are-a-good-distribution-column"></a>Mi a teendő, ha egyik oszlop sem jó terjesztési oszlop.

Ha egyik oszlop sem rendelkezik elég eltérő értékkel egy terjesztési oszlophoz, egy új oszlopot hozhat létre egy vagy több érték összetett összetételéhez. Ha el szeretné kerülni az adatáthelyezést a lekérdezés végrehajtása során, használja az összetett terjesztési oszlopot a lekérdezések JOIN oszlopa.

Miután megtervezte a kivonatok elosztott tábláját, a következő lépés az adatokat a táblába betölteni.  Útmutatásért lásd: [Betöltés áttekintése](design-elt-data-loading.md).

## <a name="how-to-tell-if-your-distribution-column-is-a-good-choice"></a>Hogyan állapítható meg, hogy a terjesztési oszlop jó választás-e

Miután betöltötte az adatokat egy kivonat-elosztott táblába, ellenőrizze, hogy a sorok egyenletesen oszlanak-e el az 60-disztribúciók között. A eloszlásban lévő sorok akár 10%-kal is változhatnak, és nem észlelhető hatással a teljesítményre.

### <a name="determine-if-the-table-has-data-skew"></a>Annak megállapítása, hogy a tábla tartalmaz-e adattorzítást

Az elferdítés gyors módja az [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)használata. A következő SQL-kód a 60-eloszlásokban tárolt sorok számát adja vissza. A kiegyensúlyozott teljesítmény érdekében az elosztott tábla sorait egyenletesen kell elosztani az összes eloszlás között.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

A 10%-nál több adateltérést tartalmazó táblák azonosításához:

1. Hozza létre a dbo. vTableSizes nézetet, amely a [táblázatok áttekintő](sql-data-warehouse-tables-overview.md#table-size-queries) cikkében látható.  
2. Futtassa az alábbi lekérdezést:

```sql
select *
from dbo.vTableSizes
where two_part_name in
    (
    select two_part_name
    from dbo.vTableSizes
    where row_count > 0
    group by two_part_name
    having (max(row_count * 1.000) - min(row_count * 1.000))/max(row_count * 1.000) >= .10
    )
order by two_part_name, row_count
;
```

### <a name="check-query-plans-for-data-movement"></a>Az adatáthelyezés lekérdezési terveinek keresése

A jó terjesztési oszlopok lehetővé teszik az illesztések és az összesítések minimális adatáthelyezést. Ez befolyásolja a csatlakozások írásának módját. Ahhoz, hogy az illesztés minimális adatátvitelt kapjon két kivonattal elosztott táblán, az egyik illesztési oszlopnak a terjesztési oszlopnak kell lennie.  Ha két kivonatoló eloszlású tábla ugyanahhoz az adattípushoz tartozó terjesztési oszlophoz csatlakozik, az illesztés nem igényli az adatáthelyezést. Az illesztések további oszlopokat is használhatnak az adatáthelyezés felmerülése nélkül.

Az adatáthelyezés elkerülése a csatlakozás során:

- Az illesztésben részt vevő táblákat kivonattal kell elosztani az illesztésben részt vevő oszlopok **egyikén** .
- Az illesztési oszlopok adattípusának egyeznie kell mindkét tábla között.
- Az oszlopokat egyenrangú operátorral kell csatlakoztatni.
- Az illesztés típusa nem lehet a következő: `CROSS JOIN` .

Ha szeretné megtudni, hogy a lekérdezések adatáthelyezést tapasztalnak-e, tekintse meg a lekérdezési tervet.  

## <a name="resolve-a-distribution-column-problem"></a>Terjesztési oszlop problémáinak elhárítása

Nem szükséges az adattorzítás összes esetének feloldása. Az Adatelosztás a megfelelő egyensúly megtalálása az adattorzítás és az adatáthelyezés minimalizálása között. Nem mindig lehet minimálisra csökkenteni az adattorzítást és az adatáthelyezést. Előfordulhat, hogy a minimális adatáthelyezés előnye meghaladja az adattorzítás következményeit.

Annak eldöntéséhez, hogy fel kell-e oldani az adatok eldöntését egy táblában, a számítási feladatban szereplő adatmennyiségekről és lekérdezésekről a lehető legnagyobb mértékben tisztában kell lennie. A lekérdezési teljesítményre gyakorolt hatás befolyásolása érdekében a [lekérdezés figyelése](sql-data-warehouse-manage-monitor.md) című cikk lépéseit követve ellenőrizheti. Pontosabban tekintse meg, hogy mennyi ideig tart a nagyméretű lekérdezések végrehajtása az egyes disztribúciók esetében.

Mivel a meglévő tábla eloszlás oszlopa nem módosítható, az adattorzítás feloldásának tipikus módja, ha újra létrehozza a táblát egy másik terjesztési oszloppal.  

### <a name="re-create-the-table-with-a-new-distribution-column"></a>A tábla újbóli létrehozása új terjesztési oszloppal

Ez a példa a [CREATE TABLE használja,](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) ha egy másik kivonatoló terjesztési oszlopot tartalmazó táblát szeretne újból létrehozni.

```sql
CREATE TABLE [dbo].[FactInternetSales_CustomerKey]
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  HASH([CustomerKey])
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_CustomerKey')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_CustomerKey] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_CustomerKey] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_CustomerKey] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_CustomerKey] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_CustomerKey] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_CustomerKey] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_CustomerKey] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_CustomerKey] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_ProductKey];
RENAME OBJECT [dbo].[FactInternetSales_CustomerKey] TO [FactInternetSales];
```

## <a name="next-steps"></a>Következő lépések

Elosztott tábla létrehozásához használja az alábbi utasítások egyikét:

- [CREATE TABLE (dedikált SQL-készlet)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [CREATE TABLE a SELECT (dedikált SQL Pool) ELEMnél](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)