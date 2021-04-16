---
title: Teljesítmény-finomhangolás rendezett fürtözött oszlopcentrikus index használatával
description: A rendezett fürtözött oszlopcentrikus index használata során figyelembe kell vennie a javaslatokat és szempontokat a dedikált SQL-készletek lekérdezési teljesítményének javításához.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/13/2021
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 3245f560d9a5afb1f9cf8824eeaa3bc681706794
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389672"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>Teljesítmény-finomhangolás rendezett fürtözött oszlopcentrikus index használatával  

Amikor a felhasználók egy dedikált SQL-készletben lévő oszlopcentrikus táblátkérdeznek le, az optimalizáló ellenőrzi az egyes szegmensek minimális és maximális értékeit.  A lekérdezési predikátumon kívüli szegmenseket a rendszer nem olvassa be a lemezről a memóriába.  A lekérdezések gyorsabb teljesítményt eredményeznek, ha az olvasni kívánt szegmensek száma és a teljes méretük kicsi.   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>Rendezett és nem rendezett fürtözött oszlopcentrikus index

Alapértelmezés szerint minden indexbeállítás nélkül létrehozott táblához egy belső összetevő (indexszerkesztő) hoz létre egy nem rendezett fürtözött oszlopcentrikus indexet (CCI).  Az egyes oszlopokban lévő adatok külön CCI sorcsoport-szegmensbe tömörítve adatokat tartalmaznak.  Az egyes szegmensek értéktartományában metaadatok vannak, így a lekérdezési predikátumon kívüli szegmensek nem olvashatók be a lemezről a lekérdezés végrehajtása során.  A CCI a legmagasabb szintű adattömörítést kínálja, és csökkenti az olvasni kívánt szegmensek méretét, így a lekérdezések gyorsabban futnak. Mivel azonban az indexszerkesztő nem rendezi az adatokat, mielőtt szegmensekre tömöríti őket, átfedésben lévő értéktartományú szegmensek fordulhatnak elő, így a lekérdezések több szegmenst olvasnak be a lemezről, és hosszabb ideig tart a befejezése.  

Rendezett CCI létrehozásakor a dedikált SQL-készletmotor a rendelési kulcs(ak) alapján rendezi a memóriában lévő adatokat, mielőtt az indexkészítő indexszegmensekbe tömöríti azokat.  A rendezési adatok esetén a szegmensek átfedődése csökkenti a szegmensek hatékonyabb kiiktató és ezáltal gyorsabb teljesítményét, mivel a lemezről beolvasható szegmensek száma kisebb.  Ha az összes adat egyszerre rendezhető a memóriában, akkor elkerülhető a szegmensek átfedése.  Az adattárház nagy méretű táblái miatt ez a forgatókönyv nem gyakran fordul elő.  

Egy oszlop szegmenstartományának ellenőrzéshez futtassa a következő parancsot a tábla és az oszlop nevével:

```sql
SELECT o.name, pnp.index_id, 
cls.row_count, pnp.data_compression_desc, 
pnp.pdw_node_id, pnp.distribution_id, cls.segment_id, 
cls.column_id, 
cls.min_data_id, cls.max_data_id, 
cls.max_data_id-cls.min_data_id as difference
FROM sys.pdw_nodes_partitions AS pnp
   JOIN sys.pdw_nodes_tables AS Ntables ON pnp.object_id = NTables.object_id AND pnp.pdw_node_id = NTables.pdw_node_id
   JOIN sys.pdw_table_mappings AS Tmap  ON NTables.name = TMap.physical_name AND substring(TMap.physical_name,40, 10) = pnp.distribution_id
   JOIN sys.objects AS o ON TMap.object_id = o.object_id
   JOIN sys.pdw_nodes_column_store_segments AS cls ON pnp.partition_id = cls.partition_id AND pnp.distribution_id  = cls.distribution_id
JOIN sys.columns as cols ON o.object_id = cols.object_id AND cls.column_id = cols.column_id
WHERE o.name = '<Table Name>' and cols.name = '<Column Name>'  and TMap.physical_name  not like '%HdTable%'
ORDER BY o.name, pnp.distribution_id, cls.min_data_id 


```

> [!NOTE] 
> Egy rendezett CCI-táblában az ugyanannak a kötegelt DML-nek vagy adatbetöltési műveleteknek az eredményeként kapott új adatok ebben a kötegben vannak rendezve, a táblában lévő összes adat között nincs globális rendezés.  A felhasználók újraépítik a rendezett CCI-t a táblában található összes adat rendezése érdekében.  A dedikált SQL-készletben az oszlopcentrikus INDEX REBUILD egy offline művelet.  Particionált táblánál a REBUILD egyszerre egy partícióval történik.  Az újraépítés alatt található partíció adatai "offline" állapotúak, és nem érhetők el, amíg a rebuild be nem fejeződik az ezen a partíción. 

## <a name="query-performance"></a>Lekérdezési teljesítmény

A lekérdezések egy rendezett CCI-hez való teljesítménybeli nyeresége a lekérdezési mintáktól, az adatok méretétől, az adatok rendezettségétől, a szegmensek fizikai struktúrájától, valamint a lekérdezés végrehajtáshoz kiválasztott DWU-tól és erőforrásosztálytól függ.  Egy rendezett CCI-tábla tervezésekor a felhasználóknak érdemes áttekintenünk ezeket a tényezőket, mielőtt kiválasztanának egy rendelési oszlopot.

Az ilyen mintákkal kapcsolatos lekérdezések általában gyorsabbak a rendezett CCI-k használatával.  
1. A lekérdezések egyenlőségi, egyenlőtlenségi vagy tartomány-predikátumokkal is
1. A predikátumoszlopok és a rendezett CCI-oszlopok azonosak.  
1. A predikátumoszlopok ugyanabban a sorrendben vannak használva, mint a rendezett CCI-oszlopok oszlopainak sorrendje.  
 
Ebben a példában a T1 tábla egy fürtözött oszlopcentrikus indexet tartalmaz, amely az Col_C, a Col_B és a Col_A.

```sql

CREATE CLUSTERED COLUMNSTORE INDEX MyOrderedCCI ON  T1
ORDER (Col_C, Col_B, Col_A)

```

Az 1. lekérdezés teljesítménye nagyobb előnyt biztosít a rendezett CCI-nek, mint a másik három lekérdezésnek. 

```sql
-- Query #1: 

SELECT * FROM T1 WHERE Col_C = 'c' AND Col_B = 'b' AND Col_A = 'a';

-- Query #2

SELECT * FROM T1 WHERE Col_B = 'b' AND Col_C = 'c' AND Col_A = 'a';

-- Query #3
SELECT * FROM T1 WHERE Col_B = 'b' AND Col_A = 'a';

-- Query #4
SELECT * FROM T1 WHERE Col_A = 'a' AND Col_C = 'c';

```

## <a name="data-loading-performance"></a>Adatbetöltési teljesítmény

Az adatok rendezett CCI-táblába való betöltésének teljesítménye hasonló a particionált táblához.  Az adatok rendezett CCI-táblába való betöltése az adatrendezési művelet miatt hosszabb időt is igénybe vehet, mint egy nem rendezett CCI-tábla, de a lekérdezések később gyorsabban futnak a rendezett CCI-k használatával.  

Az alábbi példa az adatok különböző sémákat és táblákba való betöltését hasonlítja össze.

![Sávdiagram az adatok különböző sémákat ábrázoló táblákba való betöltésének teljesítmény-összehasonlításával.](./media/performance-tuning-ordered-cci/cci-data-loading-performance.png)


Példa a CCI és a rendezett CCI lekérdezési teljesítményének összehasonlítására.

![Performance_comparison_data_loading](./media/performance-tuning-ordered-cci/occi_query_performance.png)

 
## <a name="reduce-segment-overlapping"></a>Szegmensek átfedésének csökkentése

Az átfedésben lévő szegmensek száma a rendezni kívánt adatok méretétől, a rendelkezésre álló memóriától és a maximális párhuzamosság fokától (MAXDOP) függ a rendezett CCI létrehozása során. Az alábbi lehetőségek a rendezett CCI létrehozásakor csökkentik a szegmensek átfedését.

- Az xlargerc erőforrásosztályt egy magasabb DWU-ban használva több memóriát használhat az adatok rendezéséhez, mielőtt az indexkészítő szegmensekbe tömöríti az adatokat.  Az indexszegmensben az adatok fizikai helye nem módosítható.  Egy szegmensen belül és szegmensek között nem rendezési adatok vannak.  

- Rendezett CCI létrehozása MAXDOP = 1 értékekkel.  A rendezett CCI-létrehozáshoz használt szálak az adatok egy részkészletében működnek, és helyben rendezik őket.  A különböző szálak szerint rendezett adatok között nincs globális rendezés.  A párhuzamos szálak használata csökkentheti egy rendezett CCI létrehozásához szükséges időt, de több átfedő szegmenst hoz létre, mint egyetlen szál használata.  Jelenleg a MAXDOP beállítás csak rendezett CCI-tábla az AS SELECT paranccsal CREATE TABLE támogatott.  Rendezett CCI létrehozása CREATE INDEX vagy CREATE TABLE parancsokkal nem támogatja a MAXDOP beállítást. Példa:

```sql
CREATE TABLE Table1 WITH (DISTRIBUTION = HASH(c1), CLUSTERED COLUMNSTORE INDEX ORDER(c1) )
AS SELECT * FROM ExampleTable
OPTION (MAXDOP 1);
```

- Rendezheti előre az adatokat a rendezési kulcs(ak)ra, mielőtt betölti őket a táblákba.

Lássunk egy példát egy olyan rendezett CCI-táblaeloszlásra, amely a fenti javaslatok után nulla szegmens átfedésben van. A rendezett CCI-tábla egy DWU1000c adatbázisban jön létre a CTAS használatával egy 20 GB-os halomtáblából a MAXDOP 1 és az xlargerc használatával.  A CCI egy BIGINT-oszlopba van rendelve, ismétlődések nélkül.  

![Segment_No_Overlapping](./media/performance-tuning-ordered-cci/perfect-sorting-example.png)

## <a name="create-ordered-cci-on-large-tables"></a>Rendezett CCI létrehozása nagyméretű táblákon

A rendezett CCI létrehozása offline művelet.  A partíciót nem használó táblák esetén az adatok nem lesznek elérhetők a felhasználók számára, amíg a CCI rendezett létrehozási folyamata be nem fejeződik.   Particionált táblák esetén, mivel a motor partíciók szerint hozza létre a rendezett CCI-partíciót, a felhasználók továbbra is hozzáférhetnek az adatokhoz olyan partíciókban, ahol a rendezett CCI létrehozása még nincs folyamatban.   Ezzel a beállítással minimalizálhatja az állásidőt a CCI nagy táblákon való rendezett létrehozása során: 

1.    Partíciók létrehozása a célként használt nagyméretű táblán (Table_A).
2.    Hozzon létre egy üres rendezett CCI-táblát (Table_B) ugyanazokkal a tábla- és partíciósémával, mint az A tábla.
3.    Váltson egy partíciót az A táblázatról a B táblára.
4.    Futtassa az ALTER INDEX <Ordered_CCI_Index> ON <Table_B> REBUILD PARTITION = <Partition_ID> a B táblán a bekapcsolt partíció újraépítéshez.  
5.    Ismételje meg a 3. és a 4. lépést a Table_A.
6.    Miután az összes partíciót átkapcsolta Table_A Table_B- és újraépítette, Table_A és nevezze át a Table_B-Table_A. 

>[!TIP]
> Egy rendezett CCI-t használó dedikált SQL-készlettábla esetén az ALTER INDEX REBUILD újra rendezi az adatokat a tempdb használatával. A tempdb figyelése az újraépítési műveletek során. Ha több tempdb-tárhelyre van szüksége, skálázja fel a készletet. Az index újraépítésének befejezése után skálázjon le.
>
> Rendezett CCI-t futtató dedikált SQL-készlettábla esetén az ALTER INDEX REORGANIZE nem rendezi újra az adatokat. Az adatok megoldásának érdekében használja az ALTER INDEX REBUILD (ALTER INDEX REBUILD) adatokat.

## <a name="examples"></a>Példák

**A. A rendezett oszlopok és a sorrend sorrendének ellenőrzése:**

```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0
```

**B. Az oszlopok sorrendje úgy változtatható meg, hogy oszlopokat ad hozzá vagy távolít el a rendeléslistából, vagy a CCI-ről a rendezett CCI-be vált:**

```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON  InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON)
```

## <a name="next-steps"></a>Következő lépések

További fejlesztési tippekért tekintse meg a [fejlesztési áttekintést.](sql-data-warehouse-overview-develop.md)
