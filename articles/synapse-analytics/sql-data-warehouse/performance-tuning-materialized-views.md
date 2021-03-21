---
title: Teljesítmény-Finomhangolás az anyagbeli nézetekkel
description: Ismerje meg, hogy milyen ajánlásokat és szempontokat érdemes tudnia, mivel a lekérdezési teljesítmény javítása érdekében az anyagokhoz tartozó nézeteket használ.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick; azure-synapse
ms.openlocfilehash: e137611809e2d2beefecfeaea11b4295bf6ba141
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98678491"
---
# <a name="performance-tune-with-materialized-views"></a>Teljesítmény-Finomhangolás az anyagbeli nézetekkel

Az Azure-beli szinapszis SQL-készletben található jelentős nézetek alacsony karbantartási módszert biztosítanak a komplex analitikai lekérdezésekhez, így a lekérdezés módosítása nélkül juthatnak hozzá a gyors teljesítményhez. Ebből a cikkből megtudhatja, hogyan használhatja az általános útmutatást a jelentős nézetek használatával kapcsolatban.

## <a name="materialized-views-vs-standard-views"></a>Az alapvető nézetek és a szabványos nézetek

Az Azure Szinapszisban az SQL-készlet szabványos és jelentős nézeteket támogat.  Mindkettő olyan virtuális táblákat hoz létre, amelyek a SELECT kifejezésekkel lettek létrehozva, és a lekérdezéseket logikai táblázatként mutatja  A nézetek összefoglalják a közös adatszámítások összetettségét, és felvesznek egy absztrakt réteget a számítási változásokhoz, így nincs szükség a lekérdezések újraírására.  

A normál nézet minden alkalommal kiszámítja az adatmegjelenítést, amikor a nézet használatban van.  A lemezen nem találhatók adattárolók. A személyek általában szabványos nézeteket használnak olyan eszközként, amely segít megszervezni a logikai objektumokat és lekérdezéseket egy SQL-készletben.  Normál nézet használatához a lekérdezésnek közvetlen hivatkozást kell tartalmaznia.

Egy anyagilag megtekinthető nézet előre kiszámítja, tárolja és karbantartja az adataikat az SQL-készletben, ugyanúgy, mint egy tábla.  A rendszer minden alkalommal nem igényel újraszámítást, amikor egy anyagbeli nézetet használ.  Ezért az olyan lekérdezések, amelyek az összes vagy az adatok egy részhalmazát használják az anyagokban, gyorsabb teljesítményt érhet el.  Még jobb is, ha a lekérdezések egy anyagbeli nézetet is használhatnak közvetlen hivatkozás nélkül, ezért nincs szükség az alkalmazás kódjának módosítására.  

A standard nézetekre vonatkozó követelmények többsége továbbra is érvényes egy anyagbeli nézetre. Az anyag nézet szintaxisának és egyéb követelményeinek részletes ismertetését a következő témakörben találja: [anyagelszámolású nézet létrehozása KIválasztva](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

| Összehasonlítás                     | Nézet                                         | Materialized View
|:-------------------------------|:---------------------------------------------|:--------------------------------------------------------------|
|Meghatározás megtekintése                 | SQL-készletben tárolva.              | SQL-készletben tárolva.
|Tartalom megtekintése                    | A rendszer minden alkalommal létrehozta a nézetet, amikor a nézet használatban van.   | Az SQL-készletben előre feldolgozva és tárolva a nézet létrehozása során. Frissítve, mert a rendszer az adatokat hozzáadja az alapul szolgáló táblákhoz.
|Adatfrissítés                    | Mindig frissítve                               | Mindig frissítve
|Az összetett lekérdezések adatainak megtekintési sebessége     | Lassú                                         | Gyors  
|Extra tárterület                   | Nem                                           | Igen
|Syntax                          | CREATE VIEW                                  | A KIVÁLASZTÁSNAK MEGFELELŐEN HOZZON LÉTRE EGY ANYAGBELI NÉZETET

## <a name="benefits-of-using-materialized-views"></a>A lényeges nézetek használatának előnyei

A megfelelően megtervezett, jelentős nézet a következő előnyöket biztosítja:

- Csökkentse az összetett lekérdezések végrehajtási idejét az illesztések és az összesítő függvények esetében. Minél összetettebb a lekérdezés, annál nagyobb a végrehajtás-idő megtakarításának lehetősége. A legtöbb előny akkor érhető el, ha a lekérdezés számítási díja magas, és az eredményül kapott adatkészlet kicsi.  
- Az SQL-készletben lévő Optimizer a lekérdezés-végrehajtási tervek tökéletesítéséhez automatikusan használhat központilag telepített, jelentős nézeteket.  Ez a folyamat olyan felhasználók számára transzparens, akik gyorsabb lekérdezési teljesítményt biztosítanak, és nem igénylik a lekérdezések közvetlen hivatkozását a jelentős nézetekre.
- Kis karbantartás szükséges a nézetekben.  Az alaptáblákból származó növekményes adatváltozásokat a rendszer a szinkron módon automatikusan hozzáadja az anyagbeli nézetekhez.  Ez a kialakítás lehetővé teszi a jelentős nézetek lekérdezését, hogy ugyanazokat az adatokkal adják vissza, mint az alaptáblák közvetlen lekérdezése.
- A rendszer az alaptáblákból eltérő módon oszthatja meg az adatok egy anyagbeli nézetben.  
- Az anyagilag megtekintett nézetekben lévő adatok ugyanolyan magas rendelkezésre állást és rugalmasságot biztosítanak, mint a hagyományos táblák adatai.  

Az SQL-készletben megvalósított, anyagilag megtekinthető nézetek a következő előnyöket is biztosítják:

Az egyéb adattárház-szolgáltatók összehasonlításával az Azure szinapszis Analytics szolgáltatásban megvalósított, lényeges nézetek is a következő előnyöket nyújtják:

- Automatikus és szinkron Adatfrissítés az alaptáblákban tárolt adatváltozásokkal. Nincs szükség felhasználói beavatkozásra.
- Széleskörű összesítő függvények támogatása. Lásd: [anyagelszámolású nézet létrehozása Select (Transact-SQL) néven](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).
- A lekérdezés-specifikus, jelentős megjelenítésre vonatkozó javaslat támogatása.  Lásd: [Magyarázat (Transact-SQL)](/sql/t-sql/queries/explain-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="common-scenarios"></a>Gyakori forgatókönyvek  

A rendszer általában az alábbi helyzetekben használja az anyagilag látható nézeteket:

**Javítania kell a nagy méretű adatmennyiséggel kapcsolatos összetett analitikai lekérdezések teljesítményét.**

Az összetett analitikai lekérdezések jellemzően több aggregációs funkciót és táblázatos illesztéseket használnak, így több számítási igényű művelet, például véletlenszerű és illesztések is megtalálhatók a lekérdezés-végrehajtás során.  Ezért a lekérdezések végrehajtása hosszabb ideig tarthat, különösen nagy táblákon.  

A felhasználók a gyakori számításokból származó adatokhoz hozhatnak létre egyszerű nézeteket, így nincs szükség újraszámításra, ha az adatok lekérdezésekhez szükségesek, így kevesebb számítási díj és gyorsabb lekérdezési válasz érhető el.

**Gyorsabb teljesítményre van szükség a lekérdezési módosítások nélkül**

Az SQL-készletekben a séma-és lekérdezési módosításokat általában legalább a normál ETL-műveletek és jelentéskészítés támogatásához kell tartani.  A lekérdezések teljesítményének finomhangolása érdekében a felhasználók a lekérdezés teljesítményének növelésével ellensúlyozni tudják a kihasználható nézeteket.

A más hangolási lehetőségekkel, például a skálázással és a statisztikai kezeléssel összehasonlítva sokkal kevésbé befolyásolja az éles környezetbeli nézet létrehozását és karbantartását, valamint a potenciális teljesítményt is.

- Az adatbázis-nézetek létrehozása vagy fenntartása nem befolyásolja az alaptáblákon futó lekérdezéseket.
- A lekérdezés-optimalizáló automatikusan használhatja a központilag telepített, az üzembe helyezett nézeteket, anélkül, hogy a lekérdezésre mutató hivatkozásokat kellene használni. Ez a funkció csökkenti a lekérdezés módosításának szükségességét a teljesítmény finomhangolása során.

**A lekérdezési teljesítmény gyorsabb elvégzéséhez eltérő adatelosztási stratégia szükséges**

Az Azure szinapszis Analytics egy elosztott lekérdezés-feldolgozó rendszer.  Az SQL-táblákban lévő adatokat 60-csomópontok osztják szét a három [terjesztési stratégia](sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (kivonat, round_robin vagy replikált) egyikének használatával.   

Az adateloszlás a tábla létrehozási idején van megadva, és a tábla eldobása előtt változatlan marad. A lemezes virtuális tábla a kivonatoló és az round_robin adateloszlást is támogatja.  A felhasználók kiválaszthatnak egy adateloszlást, amely eltér az alaptábláktól, de optimális a nézeteket használó lekérdezések teljesítményéhez.  

## <a name="design-guidance"></a>Tervezési útmutató

A lekérdezési teljesítmény javítása érdekében az alábbi általános útmutatást követve hozhatja ki a jelentős nézetek használatát:

**Számítási feladatok tervezése**

Mielőtt megkezdené az alapvető nézetek létrehozását, fontos, hogy alapos ismeretekkel rendelkezzen a számítási feladatokról a lekérdezési minták, a fontosság, a gyakoriság és az eredményül kapott adatok mérete tekintetében.  

A felhasználók a lekérdezés-optimalizáló által ajánlott, a WITH_RECOMMENDATIONS <SQL_statement> is futtathatják.  Mivel ezek a javaslatok lekérdezés-specifikusak, az egyetlen lekérdezés előnyeit kihasználó, egy adott számítási feladathoz tartozó más lekérdezések esetében nem lehet optimális.  

Értékelje ki ezeket az ajánlásokat a számítási feladatokhoz szükséges szem előtt tartva.  Az ideális, lényeges nézetek a számítási feladatok teljesítményének kihasználása.  

**Vegye figyelembe a gyorsabb lekérdezések és a díjak közötti kompromisszumot.**

Minden egyes anyag nézet esetében az adattárolási költségeket és a nézet fenntartásának költségeit kell megfizetni.  Az alaptáblákban megjelenő adatváltozások esetén az anyagilag megjelenő nézet mérete megnő, és a fizikai szerkezete is megváltozik.  A lekérdezési teljesítmény romlásának elkerülése érdekében az SQL Analytics motor külön kezeli az összes anyagra vonatkozó nézetet.  

A karbantartási munkaterhelés magasabb lesz, ha az anyagilag megjelenő nézetek és az alaptábla változásai növekednek.   A felhasználóknak ellenőriznie kell, hogy az összes jelentős nézetből felmerült költségek ellensúlyozzák-e a lekérdezési teljesítmény nyereségét.  

Ezt a lekérdezést futtathatja egy SQL-készletben található, az anyagként szolgáló nézet listájára:

```sql
SELECT V.name as materialized_view, V.object_id
FROM sys.views V
JOIN sys.indexes I ON V.object_id= I.object_id AND I.index_id < 2;
```

A kimutatott nézetek számának csökkentésére szolgáló beállítások:

- Azonosítsa a számítási feladatok összetett lekérdezései által gyakran használt közös adatkészleteket.  Hozzon létre az adathalmazok tárolására szolgáló, anyagilag megtekinthető nézeteket, hogy az optimalizáló a végrehajtási tervek létrehozásakor építőelemeket használjanak.  

- Dobja el az alacsony kihasználtságú vagy már nem szükséges anyagbeli nézeteket.  A letiltott anyagbeli nézet nem tart fenn, de továbbra is A tárolási költségekkel jár.  

- Összekapcsolhatja az azonos vagy hasonló alaptáblákon létrehozott, az adatok átfedését még akkor is, ha az adatok nem fedik át egymást.  A jelentős nézetek kombinálásával a különálló nézetek összege nagyobb méretű nézetet eredményezhet, a karbantartási költségeket azonban csökkenteni kell.  Például:

```sql

-- Query 1 would benefit from having a materialized view created with this SELECT statement

SELECT A, SUM(B)
FROM T
GROUP BY A

-- Query 2 would benefit from having a materialized view created with this SELECT statement

SELECT C, SUM(D)
FROM T
GROUP BY C

-- You could create a single mateiralized view of this form

SELECT A, C, SUM(B), SUM(D)
FROM T
GROUP BY A, C

```

**A teljesítmény finomhangolása nem igényli a lekérdezés módosítását**

Az SQL Analytics-optimalizáló a lekérdezési teljesítmény javítása érdekében automatikusan felhasználja a központilag telepített, jelentős nézeteket.  Ez a támogatás transzparens módon van alkalmazva olyan lekérdezésekre, amelyek nem hivatkoznak az összesítéseket használó nézetekre és lekérdezésekre, és nem támogatottak az anyagilag létrehozott nézetek létrehozásakor.  Nincs szükség lekérdezési módosításra. Megtekintheti a lekérdezés becsült végrehajtási tervét, és ellenőrizheti, hogy van-e egy anyagbeli nézet.  

**A lényeges nézetek figyelése**

A rendszer az SQL-készletben található, mint egy fürtözött oszlopcentrikus indexet (CCI) tartalmazó táblát.  Az adatok egy anyagbeli nézetből történő beolvasása magában foglalja a CCI-index szegmensek vizsgálatát és az alaptáblák növekményes változásainak alkalmazását. Ha a növekményes módosítások száma túl magas, a lekérdezés egy anyagbeli nézetből való feloldása hosszabb időt vehet igénybe, mint az alaptáblák közvetlen lekérdezése.  

A lekérdezési teljesítmény romlásának elkerülése érdekében érdemes futtatni a [DBCC-PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) a nézet overhead_ratio (total_rows/Max (1, base_view_row)) figyeléséhez.  Ha a overhead_ratio túl magas, a felhasználóknak újra kell építeniük az anyagbeli nézetet.

**Anyagelszámolású nézet és eredményhalmaz gyorsítótárazása**

Ez a két funkció a lekérdezési teljesítmény finomhangolásához egy időben, az SQL Analyticsben is elérhető.  Az eredményhalmaz gyorsítótárazásával nagy párhuzamosságot és gyors választ kaphat a statikus adatokra irányuló ismétlődő lekérdezésektől.  

A gyorsítótárazott eredmény használatához a lekérdezést kérő gyorsítótárnak egyeznie kell azzal a lekérdezéssel, amely a gyorsítótárat hozta létre.  Emellett a gyorsítótárazott eredményt is a teljes lekérdezésre kell alkalmazni.  

A jelentős nézetek lehetővé teszik az alaptáblákban lévő adatváltozásokat.  Az anyagilag megtekintett nézetekben tárolt adatok alkalmazhatók egy adott lekérdezésre.  Ez a támogatás lehetővé teszi, hogy ugyanazokat a különböző lekérdezéseket használja, amelyek a gyorsabb teljesítmény érdekében több számítási feladattal rendelkeznek.

## <a name="example"></a>Példa

Ez a példa egy TPCDS-szerű lekérdezést használ, amely megkeresi azokat az ügyfeleket, akik több pénzt költenek a katalóguson keresztül, mint a boltokban, azonosítják az előnyben részesített ügyfeleket és a származási országot   A lekérdezés magában foglalja a legfontosabb 100-rekordok kiválasztását a következő három alkijelölési utasítás közül: SUM () és GROUP BY.

```sql
WITH year_total AS (
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
       ,'s' sale_type
FROM customer
     ,store_sales
     ,date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
       ,'c' sale_type
FROM customer
     ,catalog_sales
     ,date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
       ,'w' sale_type
FROM customer
     ,web_sales
     ,date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
         )
  SELECT TOP 100
                  t_s_secyear.customer_id
                 ,t_s_secyear.customer_first_name
                 ,t_s_secyear.customer_last_name
                 ,t_s_secyear.customer_birth_country
FROM year_total t_s_firstyear
     ,year_total t_s_secyear
     ,year_total t_c_firstyear
     ,year_total t_c_secyear
     ,year_total t_w_firstyear
     ,year_total t_w_secyear
WHERE t_s_secyear.customer_id = t_s_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_c_secyear.customer_id
   AND t_s_firstyear.customer_id = t_c_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_secyear.customer_id
   AND t_s_firstyear.sale_type = 's'
   AND t_c_firstyear.sale_type = 'c'
   AND t_w_firstyear.sale_type = 'w'
   AND t_s_secyear.sale_type = 's'
   AND t_c_secyear.sale_type = 'c'
   AND t_w_secyear.sale_type = 'w'
   AND t_s_firstyear.dyear+0 =  1999
   AND t_s_secyear.dyear+0 = 1999+1
   AND t_c_firstyear.dyear+0 =  1999
   AND t_c_secyear.dyear+0 =  1999+1
   AND t_w_firstyear.dyear+0 = 1999
   AND t_w_secyear.dyear+0 = 1999+1
   AND t_s_firstyear.year_total > 0
   AND t_c_firstyear.year_total > 0
   AND t_w_firstyear.year_total > 0
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_s_firstyear.year_total > 0 THEN t_s_secyear.year_total / t_s_firstyear.year_total ELSE NULL END
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_w_firstyear.year_total > 0 THEN t_w_secyear.year_total / t_w_firstyear.year_total ELSE NULL END
ORDER BY t_s_secyear.customer_id
         ,t_s_secyear.customer_first_name
         ,t_s_secyear.customer_last_name
         ,t_s_secyear.customer_birth_country
OPTION ( LABEL = 'Query04-af359846-253-3');
```

Keresse meg a lekérdezés becsült végrehajtási tervét.  A rendszer 18 Shuffle és 17 összekapcsolási műveletet hajt végre, amelyek több időt vesznek igénybe. Most hozzon létre egy, a három alkijelölési utasításhoz tartozó, egy anyagból álló nézetet.

```sql
CREATE materialized view nbViewSS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.store_sales
     ,dbo.date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
GO
CREATE materialized view nbViewCS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
          , count_big(*) as cb
FROM dbo.customer
     ,dbo.catalog_sales
     ,dbo.date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

GO
CREATE materialized view nbViewWS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.web_sales
     ,dbo.date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

```

Vizsgálja meg újra az eredeti lekérdezés végrehajtási tervét.  Most az összekapcsolások száma 17 és 5 között változik, és többé nem áll rendelkezésre shuffle.  Kattintson a terv szűrési művelet ikonjára, annak kimeneti listája megjeleníti az adatokat az alaptáblák helyett az anyagból származó nézetből.  

 ![Plan_Output_List_with_Materialized_Views](./media/performance-tuning-materialized-views/output-list.png)

A nagy mennyiségű nézetekkel ugyanaz a lekérdezés sokkal gyorsabban fut a kód módosítása nélkül.  

## <a name="next-steps"></a>Következő lépések

További fejlesztési tippek: a [SZINAPSZIS SQL-készlet fejlesztése – áttekintés](sql-data-warehouse-overview-develop.md).
