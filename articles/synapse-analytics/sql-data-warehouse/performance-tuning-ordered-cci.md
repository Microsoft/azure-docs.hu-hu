---
title: Teljesítmény-finomhangolás rendezett fürtözött oszlopcentrikus index használatával
description: A dedikált SQL-készletekben a lekérdezési teljesítmény javítása érdekében érdemes tudni, hogy a rendezett fürtözött oszlopcentrikus-index használata során szükségesek-e a javaslatok és szempontok.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: afb6efcee2ad4f5cf25a411eed353ff2fc27d75c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96460794"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>Teljesítmény-finomhangolás rendezett fürtözött oszlopcentrikus index használatával  

Amikor a felhasználók egy dedikált SQL-készletben kérdeznek le egy oszlopcentrikus-táblázatot, a-optimalizáló ellenőrzi az egyes szegmensekben tárolt minimális és maximális értékeket.  A lekérdezési predikátum határain kívüli szegmensek nem olvashatók be a lemezről a memóriába.  A lekérdezés gyorsabb teljesítményt érhet el, ha a beolvasandó szegmensek száma és a teljes mérete kicsi.   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>Rendezett és nem rendezett, fürtözött oszlopcentrikus index

Alapértelmezés szerint minden olyan tábla esetében, amely index-beállítás nélkül lett létrehozva, a belső összetevő (index Builder) nem rendezett, fürtözött oszlopcentrikus indexet (CCI) hoz létre.  Az egyes oszlopokban lévő összes adathalmaz egy külön CCI sorcsoport-szegmensbe van tömörítve.  Vannak metaadatok az egyes szegmensek érték-tartományán, így a lekérdezési predikátum határain kívüli szegmensek nem olvashatók be a lemezről a lekérdezés végrehajtása során.  A KKU a legmagasabb szintű adattömörítést kínálja, és csökkenti a szegmensek méretét az olvasáshoz, hogy a lekérdezések gyorsabban fussanak. Mivel azonban az index-szerkesztő nem rendezi az adatait, mielőtt tömöríti őket a szegmensekre, az átfedésben lévő értékekkel rendelkező szegmensek felmerülhetnek, ami miatt a lekérdezések több szegmenst olvasnak be a lemezről, és hosszabb ideig tartanak.  

Rendezett CCI létrehozásakor a dedikált SQL-készlet motorja a rendelkezésre álló adatok alapján rendezi a memóriában lévő meglévő adatmennyiséget, mielőtt az index-szerkesztő tömöríti őket az index szegmensbe.  A rendezett adatok esetében a szegmens átfedésben van, ami lehetővé teszi a lekérdezések hatékonyabb szegmensének megszüntetését, és így gyorsabb teljesítményt, mivel a lemezről beolvasott szegmensek száma kisebb.  Ha az összes adatmennyiséget egyszerre rendezheti a memóriában, akkor a szegmens átfedése elkerülhető lehet.  Az adattárházak nagyméretű táblái miatt ez a forgatókönyv nem fordul elő gyakran.  

Egy oszlop szegmens tartományának vizsgálatához futtassa a következő parancsot a tábla nevével és az oszlop nevével:

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
> Egy rendezett CCI-táblázatban a DML-ből vagy az betöltési műveletből eredő új adatok a kötegen belül vannak rendezve, a tábla összes adathalmaza esetében nincs globális rendezés.  A felhasználók újra felépíthetik a rendezett CCI-t, hogy a táblázatban szereplő összes adattal sorba lehessen rendezni.  A dedikált SQL-készletben a oszlopcentrikus index újraépítése offline művelet.  Particionált tábla esetén az Újraépítés egyszerre egy partíciót hajt végre.  Az újraépített partícióban lévő adatkapcsolat "offline" állapotú, és nem érhető el, amíg a partíció újraépítése be nem fejeződik. 

## <a name="query-performance"></a>Lekérdezési teljesítmény

Egy lekérdezésnek a rendezett CCI-ből származó teljesítménybeli nyeresége a lekérdezési mintáktól, az adatok méretétől, az adatok rendezési módjától, a szegmensek fizikai struktúrájától, valamint a DWU és az erőforrás osztálytól függ.  A felhasználóknak át kell tekinteniük ezeket a tényezőket, mielőtt kiválasztja a rendezési oszlopokat a rendezett CCI-táblázat tervezésekor.

Az ezekkel a mintázatokkal rendelkező lekérdezések általában gyorsabban futnak a rendezett KKU-val.  
1. A lekérdezések egyenlőséggel, egyenlőtlenséggel vagy tartományon alapuló predikátumokkal rendelkeznek
1. A predikátum oszlopai és a rendezett CCI-oszlopok azonosak.  
1. A predikátum oszlopai a rendezett CCI-oszlopok oszlopának sorszámával azonos sorrendben használatosak.  
 
Ebben a példában a T1 tábla egy fürtözött oszlopcentrikus indextel rendelkezik, Col_C, Col_B és Col_A sorrendje szerint rendezve.

```sql

CREATE CLUSTERED COLUMNSTORE INDEX MyOrderedCCI ON  T1
ORDER (Col_C, Col_B, Col_A)

```

Az 1. lekérdezés teljesítménye jobban kihasználhatja a megrendelt KKU-t, mint a többi három lekérdezést. 

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

## <a name="data-loading-performance"></a>Adattöltési teljesítmény

A rendezett CCI-táblázatba betöltött adatmennyiség hasonló egy particionált táblához.  A rendezett CCI-táblázatba betöltött adatbetöltések az adatrendezési művelet miatt hosszabb időt vehetnek igénybe, de a lekérdezések gyorsabban futhatnak a rendezett CCI-mel.  

Az alábbi példa egy olyan teljesítménybeli összehasonlítást mutat be, amely a különböző sémákkal rendelkező táblákba való betöltést hasonlítja

![Oszlopdiagram, amely a különböző sémákkal rendelkező táblákba való betöltés teljesítményének összehasonlítását mutatja.](./media/performance-tuning-ordered-cci/cci-data-loading-performance.png)


Íme egy példa a lekérdezés teljesítményének összehasonlítására a KKU és a rendezett CCI között.

![Performance_comparison_data_loading](./media/performance-tuning-ordered-cci/occi_query_performance.png)

 
## <a name="reduce-segment-overlapping"></a>Szegmens átfedésének csökkentése

Az átfedésben lévő szegmensek száma a rendezni kívánt adatok méretétől, a rendelkezésre álló memóriától és a maximális párhuzamossági fok (MAXDOP) beállítástól függ. Az alábbiakban megtalálhatja azokat a lehetőségeket, amelyekkel csökkenthető a rendezett CCI létrehozásakor a szegmens átfedés.

- A xlargerc-erőforrás osztályának használata magasabb DWU, így több memória is engedélyezhető az adatok rendezéséhez, mielőtt az index-szerkesztő tömöríti az adatszegmenseket.  Az index szegmensben az adatfizikai hely nem módosítható.  Egy szegmensen vagy szegmensen belül nincs Adatrendezés.  

- Rendezett CCI létrehozása a MAXDOP = 1 paranccsal.  A rendezett CCI-létrehozáshoz használt minden szál az adathalmazon működik, és helyileg rendezi.  Nincs globális rendezés a különböző szálak között rendezve.  A párhuzamos szálak használatával csökkentheti a rendezett CCI létrehozásának idejét, de több átfedésben lévő szegmenst fog létrehozni, mint egyetlen szál használata esetén.  Jelenleg a MAXDOP beállítás csak olyan rendezett CCI-táblázat létrehozásakor támogatott, CREATE TABLE AS SELECT parancs használatával.  A rendezett CCI LÉTREHOZÁSi INDEXen vagy CREATE TABLE parancsok használatával történő létrehozása nem támogatja a MAXDOP beállítást. Példa:

```sql
CREATE TABLE Table1 WITH (DISTRIBUTION = HASH(c1), CLUSTERED COLUMNSTORE INDEX ORDER(c1) )
AS SELECT * FROM ExampleTable
OPTION (MAXDOP 1);
```

- A rendezési kulcs (ok) alapján a táblázatokba való betöltés előtt előre rendezheti az adathalmazt.

Itt látható egy példa arra, hogy egy rendezett CCI-táblázat eloszlása nulla szegmenst tartalmaz, amely a fenti ajánlásokat követve átfedésben van. A rendezett CCI-táblázat egy DWU1000c-adatbázisban jön létre a CTAS-on keresztül egy 20 GB-os halom-táblából, amely az 1. és a xlargerc-t használja.  A KKU nem duplikált BIGINT oszlopra van rendezve.  

![Segment_No_Overlapping](./media/performance-tuning-ordered-cci/perfect-sorting-example.png)

## <a name="create-ordered-cci-on-large-tables"></a>Rendezett CCI létrehozása nagyméretű táblákon

A rendezett CCI létrehozása offline művelet.  A partíciókat nem tartalmazó táblák esetében az adathozzáférés nem lesz elérhető a felhasználók számára, amíg a rendezett CCI-létrehozási folyamat be nem fejeződik.   A particionált táblák esetében, mivel a motor partíció alapján hozza létre a rendezett CCI-partíciót, a felhasználók továbbra is hozzáférhetnek az olyan partíciókban lévő adatbázisokhoz, ahol a rendezett CCI-létrehozás nincs folyamatban.   Ezzel a beállítással minimálisra csökkentheti az állásidőt a nagy táblákon a rendezett CCI-létrehozás során: 

1.    Hozzon létre partíciókat a célként megadott nagyméretű táblán (Table_A néven).
2.    Hozzon létre egy üres rendezett CCI-táblázatot (Table_B) ugyanazzal a tábla-és partíciós sémával az A táblázattal.
3.    Váltson át egy partíciót az A táblából a B táblázatba.
4.    Futtassa az ALTER INDEX <Ordered_CCI_Index> a <Table_B> Újraépítés PARTITION = <Partition_ID> a B táblázatban a bekapcsolt partíció újraépítéséhez.  
5.    Ismételje meg a 3. és a 4. lépést a Table_A minden partícióján.
6.    Ha az összes partíciót Table_Aról Table_Bre váltották át, a rendszer újraépíti, elvetette Table_A, majd átnevezi Table_B Table_Are. 

## <a name="examples"></a>Példák

**Egy. A rendezett oszlopok és a sorrend sorszámának keresése:**

```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0
```

**B. Az oszlopok sorszámának módosításához, oszlopok hozzáadásához vagy eltávolításához a sorrend listából, vagy a CCI-ből a rendezett CCI-re való váltáshoz:**

```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON  InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON)
```

## <a name="next-steps"></a>Következő lépések

További fejlesztési tippek: a [fejlesztés áttekintése](sql-data-warehouse-overview-develop.md).
