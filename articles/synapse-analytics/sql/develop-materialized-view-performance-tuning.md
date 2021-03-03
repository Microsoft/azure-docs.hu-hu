---
title: Teljesítmény-finomhangolás tényleges táblán alapuló nézetekkel
description: A lekérdezési teljesítmény javítására szolgáló, a lényeges nézetekre vonatkozó javaslatok és megfontolások.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.openlocfilehash: e6c3987e2de7f9592a1f7f6086657592e1bf0c16
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101676594"
---
# <a name="performance-tuning-with-materialized-views-using-dedicated-sql-pool-in-azure-synapse-analytics"></a>Teljesítmény-Finomhangolás az Azure szinapszis Analytics szolgáltatásban található dedikált SQL-készlettel rendelkező, anyagokból származó nézetekkel

A dedikált SQL-készletben a fontos nézetek alacsony karbantartási módszert biztosítanak a komplex analitikai lekérdezésekhez, így a lekérdezés módosítása nélkül juthatnak hozzá a gyors teljesítményhez. Ebből a cikkből megtudhatja, hogyan használhatja az általános útmutatást a jelentős nézetek használatával kapcsolatban.

## <a name="materialized-views-vs-standard-views"></a>Az alapvető nézetek és a szabványos nézetek

Az SQL-készlet mind a standard, mind a jelentős nézeteket támogatja.  Mindkettő olyan virtuális táblákat hoz létre, amelyek a SELECT kifejezésekkel lettek létrehozva, és a lekérdezéseket logikai táblázatként mutatja  A nézetek felfedik a közös adatszámítások összetettségét, és felvesznek egy absztrakt réteget a számítási változásokhoz, így nem kell újraírni a lekérdezéseket.  

A normál nézet minden alkalommal kiszámítja az adatmegjelenítést, amikor a nézet használatban van.  A lemezen nem találhatók adattárolók. A személyek általában szabványos nézeteket használnak olyan eszközként, amely segít megszervezni a logikai objektumokat és lekérdezéseket egy adatbázisban.  Normál nézet használatához a lekérdezésnek közvetlen hivatkozást kell tartalmaznia.

Egy anyagilag megtekinthető nézet előre kiszámítja, tárolja és karbantartja az adataikat a dedikált SQL-készletben, ugyanúgy, mint egy tábla.  A rendszer minden alkalommal nem igényel újraszámítást, amikor egy anyagbeli nézet van használatban.  Ezért az olyan lekérdezések, amelyek az összes vagy az adatok egy részhalmazát használják az anyagilag megtekintett nézetekben, gyorsabb teljesítményt tudnak nyerni.  Még jobb is, ha a lekérdezések egy anyagbeli nézetet is használhatnak közvetlen hivatkozás nélkül, ezért nincs szükség az alkalmazás kódjának módosítására.  

A szabványos nézetre vonatkozó követelmények többsége továbbra is érvényes egy anyagbeli nézetre. Az anyag nézet szintaxisának és egyéb követelményeinek részletes ismertetését a következő témakörben találja: [anyagelszámolású nézet létrehozása a Select](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true)utasítással.

| Összehasonlítás                     | Nézet                                         | Materialized View
|:-------------------------------|:---------------------------------------------|:--------------------------------------------------------------|
|Meghatározás megtekintése                 | Az Azure-adattárházban tárolva.              | Az Azure-adattárházban tárolva.
|Tartalom megtekintése                    | A rendszer minden alkalommal létrehozta a nézetet, amikor a nézet használatban van.   | Az Azure-adattárházban előre feldolgozva és tárolva a nézet létrehozása során. Frissítve, mert a rendszer az adatokat hozzáadja az alapul szolgáló táblákhoz.
|Adatfrissítés                    | Mindig frissítve                               | Mindig frissítve
|Az összetett lekérdezések adatainak megtekintési sebessége     | Lassú                                         | Gyors  
|Extra tárterület                   | Nem                                           | Igen
|Syntax                          | CREATE VIEW                                  | A KIVÁLASZTÁSNAK MEGFELELŐEN HOZZON LÉTRE EGY ANYAGBELI NÉZETET

## <a name="benefits-of-materialized-views"></a>A lényeges nézetek előnyei

A megfelelően megtervezett, jelentős nézet a következő előnyöket biztosítja:

- Kisebb végrehajtási idő az összekapcsolási és összesítő függvények összetett lekérdezésekhez. Minél összetettebb a lekérdezés, annál nagyobb a végrehajtás-idő megtakarításának lehetősége. A legtöbb előny akkor érhető el, ha a lekérdezés számítási díja magas, és az eredményül kapott adatkészlet kicsi.  

- A dedikált SQL-készletben lévő lekérdezés-optimalizáló a lekérdezés-végrehajtási tervek javítása érdekében automatikusan felhasználhatja a telepített, jelentős nézeteket.  Ez a folyamat olyan felhasználók számára transzparens, akik gyorsabb lekérdezési teljesítményt biztosítanak, és nem igénylik a lekérdezések közvetlen hivatkozását a jelentős nézetekre.

- Alacsony karbantartást igényel a nézetekben.  Egy anyagilag megtekinthető nézet két helyen tárolja az adatok, a kezdeti adatok fürtözött oszlopcentrikus indexét a nézet létrehozási idején, valamint egy különbözeti tárolót a növekményes adatok változásaihoz.  Az alaptáblákból származó adatváltozásokat a rendszer szinkron módon automatikusan hozzáadja a Delta-tárolóhoz.  A háttérben futó folyamat (a rekord mozgatója) rendszeres időközönként áthelyezi az adatait a különbözeti tárolóból a nézet oszlopcentrikus indexére.  Ez a kialakítás lehetővé teszi a jelentős nézetek lekérdezését, hogy ugyanazokat az adatokkal adják vissza, mint az alaptáblák közvetlen lekérdezése.
- A rendszer az alaptáblákból eltérő módon oszthatja meg az adatok egy anyagbeli nézetben.  
- Az anyagilag megtekintett nézetekben lévő adatok ugyanolyan magas rendelkezésre állást és rugalmasságot biztosítanak, mint a hagyományos táblák adatai.  

Az egyéb adattárház-szolgáltatókhoz képest a dedikált SQL-készletben megvalósított, jelentős nézetek a következő előnyöket is biztosítják:

- Automatikus és szinkron Adatfrissítés az alaptáblákban tárolt adatváltozásokkal. Nincs szükség felhasználói beavatkozásra.
- Széleskörű összesítő függvények támogatása. Lásd: [anyagelszámolású nézet létrehozása Select (Transact-SQL) néven](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true).
- A lekérdezés-specifikus, jelentős megjelenítésre vonatkozó javaslat támogatása.  Lásd: [Magyarázat (Transact-SQL)](/sql/t-sql/queries/explain-transact-sql?view=azure-sqldw-latest&preserve-view=true).

## <a name="common-scenarios"></a>Gyakori forgatókönyvek  

A rendszer általában az alábbi helyzetekben használja az anyagilag látható nézeteket:

**Javítania kell a nagy méretű adatmennyiséggel kapcsolatos összetett analitikai lekérdezések teljesítményét.**

Az összetett analitikai lekérdezések jellemzően több aggregációs funkciót és táblázatos illesztéseket használnak, így több számítási igényű művelet, például véletlenszerű és illesztések is megtalálhatók a lekérdezés-végrehajtás során.  Ezért a lekérdezések végrehajtása hosszabb ideig tarthat, különösen nagy táblákon.  

A felhasználók a gyakori számításokból származó adatokhoz hozhatnak létre egyszerű nézeteket, ezért nem szükséges újraszámítást végezni, ha az adatok lekérdezésekhez szükségesek, így csökkentheti a számítási költségeket és a gyorsabb lekérdezési válaszokat.

**Gyorsabb teljesítményre van szükség a lekérdezési módosítások nélkül**

Az adattárházak sémájának és lekérdezésének változásai általában legalább a normál ETL-műveletek és jelentéskészítés támogatásához szükségesek.  A felhasználók a lekérdezési teljesítmény finomhangolásához használhatnak az anyagilag megjelenő nézeteket, ha a nézetekben felmerülő költségek ellensúlyozzák a lekérdezési teljesítmény nyereségét.

A más hangolási lehetőségekkel, például a skálázással és a statisztikai kezeléssel összehasonlítva sokkal kevésbé befolyásolja az éles környezetbeli nézet létrehozását és karbantartását, valamint a potenciális teljesítményt is.

- Az adatbázis-nézetek létrehozása vagy fenntartása nem befolyásolja az alaptáblákon futó lekérdezéseket.
- A lekérdezés-optimalizáló automatikusan használhatja a központilag telepített, az üzembe helyezett nézeteket, anélkül, hogy a lekérdezésre mutató hivatkozásokat kellene használni. Ez a funkció csökkenti a lekérdezés módosításának szükségességét a teljesítmény finomhangolása során.

**A lekérdezési teljesítmény gyorsabb elvégzéséhez eltérő adatelosztási stratégia szükséges**

Az Azure-adattárház elosztott és masszívan párhuzamos feldolgozási (MPP) rendszer.  

A szinapszis SQL egy elosztott lekérdezési rendszer, amely lehetővé teszi, hogy a vállalatok adattárház-és adatvirtualizációs forgatókönyveket alkalmazzanak az adatmérnökök számára ismerős standard T-SQL-élmények használatával. Emellett kibővíti az SQL képességeit a streaming és a gépi tanulási forgatókönyvek kezeléséhez. Az adatraktár-táblában lévő adatokat a rendszer a három [terjesztési stratégia](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (kivonat, round_robin vagy replikált) egyikével osztja el a 60 csomópontok között.  

Az adateloszlás a tábla létrehozási idején van megadva, és a tábla eldobása előtt változatlan marad. A lemezes virtuális tábla a kivonatoló és az round_robin adateloszlást is támogatja.  A felhasználók kiválaszthatnak egy adateloszlást, amely eltér az alaptábláktól, de optimális a nézeteket gyakran használó lekérdezések teljesítményéhez.  

## <a name="design-guidance"></a>Tervezési útmutató

A lekérdezési teljesítmény javítása érdekében az alábbi általános útmutatást követve hozhatja ki a jelentős nézetek használatát:

**Számítási feladatok tervezése**

Mielőtt megkezdené az alapvető nézetek létrehozását, fontos, hogy alapos ismeretekkel rendelkezzen a számítási feladatokról a lekérdezési minták, a fontosság, a gyakoriság és az eredményül kapott adatok mérete tekintetében.  

A felhasználók a lekérdezés-optimalizáló által ajánlott, a WITH_RECOMMENDATIONS <SQL_statement> is futtathatják.  Mivel ezek a javaslatok lekérdezés-specifikusak, az egyetlen lekérdezés előnyeit kihasználó, egy adott számítási feladathoz tartozó más lekérdezések esetében nem lehet optimális.  

Értékelje ki ezeket az ajánlásokat a számítási feladatokhoz szükséges szem előtt tartva.  Az ideális, lényeges nézetek a számítási feladatok teljesítményének kihasználása.  

**Vegye figyelembe a gyorsabb lekérdezések és a díjak közötti kompromisszumot.**

Minden egyes anyag nézet esetében az adattárolási költségeket és a nézet fenntartásának költségeit kell megfizetni.  Ahogy az alaptáblákban az adatváltozások módosulnak, az anyagbeli nézet mérete nő, és a fizikai szerkezete is megváltozik.  

A lekérdezési teljesítmény romlásának elkerülése érdekében a rendszer az adatraktár-motortól külön kezeli az egyes összes típusú nézeteket, beleértve a sorok áthelyezését a különbözeti tárolóból a oszlopcentrikus index szegmensbe, és összevonja az adatok változásait.  

A karbantartási feladat magasabbra emelkedik, ha az anyagilag megjelenő nézetek és az alaptábla változásai növekednek.   A felhasználóknak ellenőriznie kell, hogy az összes jelentős nézetből felmerült költségek ellensúlyozzák-e a lekérdezési teljesítmény nyereségét.  

Ezt a lekérdezést futtathatja a következő adatbázisban található, anyagként szolgáló nézet listájára:

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

Az adatraktár-optimalizáló a lekérdezések teljesítményének növelése érdekében automatikusan felhasználja a telepített, jelentős nézeteket.  Ez a támogatás transzparens módon van alkalmazva olyan lekérdezésekre, amelyek nem hivatkoznak a nézetekre, és olyan lekérdezések, amelyek nem támogatják az összesítéseket a létrehozott nézetek létrehozásakor.  Nincs szükség lekérdezési módosításra. Megtekintheti a lekérdezés becsült végrehajtási tervét, és ellenőrizheti, hogy van-e egy anyagbeli nézet.  

**A lényeges nézetek figyelése**

A rendszer az adatraktárban egy, a fürtözött oszlopcentrikus indextel (CCI) rendelkező táblához hasonlóan egy anyagbeli nézetet tárol.  Az adatok egy anyagbeli nézetből történő beolvasása magában foglalja az index vizsgálatát és a változások a különbözeti tárolóból való alkalmazását.  Ha a különbözeti tárolóban lévő sorok száma túl magas, a lekérdezés egy anyagbeli nézetből való feloldása hosszabb időt vehet igénybe, mint az alaptáblák közvetlen lekérdezése.  

A lekérdezési teljesítmény romlásának elkerülése érdekében célszerű a [DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?view=azure-sqldw-latest&preserve-view=true) futtatni a nézet overhead_ratio (total_rows/base_view_row) figyeléséhez.  Ha a overhead_ratio túl magas, érdemes lehet újraépíteni az anyagbeli nézetet, hogy a különbözeti tároló összes sora át legyen helyezve a oszlopcentrikus indexbe.  

**Anyagelszámolású nézet és eredményhalmaz gyorsítótárazása**

Ez a két funkció a lekérdezési teljesítmény finomhangolásához egy időben, egy dedikált SQL-készletben van bevezetve. Az eredményhalmaz gyorsítótárazása a statikus adatokkal való ismétlődő lekérdezések magas párhuzamossági és gyors reagálási idejének elérésére szolgál.  

A gyorsítótárazott eredmény használatához a lekérdezést kérő gyorsítótárnak egyeznie kell azzal a lekérdezéssel, amely a gyorsítótárat hozta létre.  Emellett a gyorsítótárazott eredményt is a teljes lekérdezésre kell alkalmazni.  

A jelentős nézetek lehetővé teszik az alaptáblákban lévő adatváltozásokat.  Az anyagilag megtekintett nézetekben tárolt adatok alkalmazhatók egy adott lekérdezésre.  Ez a támogatás lehetővé teszi, hogy ugyanazokat a különböző lekérdezéseket használja, amelyek a gyorsabb teljesítmény érdekében több számítási feladattal rendelkeznek.

## <a name="example"></a>Példa

Ez a példa egy TPCDS-szerű lekérdezést használ, amely megkeresi azokat az ügyfeleket, akik több pénzt költenek a katalógusban, mint a boltokban. Emellett azonosítja az előnyben részesített ügyfeleket és a származási országot/régiót.   A lekérdezés magában foglalja a legfontosabb 100-rekordok kiválasztását a következő három alkijelölési utasítás közül: SUM () és GROUP BY.

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

Vizsgálja meg újra az eredeti lekérdezés végrehajtási tervét.  Most az összekapcsolások száma 17 és 5 között változik, és többé nem áll rendelkezésre shuffle.  Válassza ki a szűrő művelet ikont a tervben. A kimeneti lista azt mutatja, hogy az adatok beolvasása az alaptáblák helyett az anyagot ábrázoló nézetből történik.  

 ![Plan_Output_List_with_Materialized_Views](./media/develop-materialized-view-performance-tuning/output-list.png)

A nagy mennyiségű nézetekkel ugyanaz a lekérdezés sokkal gyorsabban fut a kód módosítása nélkül.  

## <a name="next-steps"></a>Következő lépések

További fejlesztési tippek: a [SZINAPSZIS SQL-fejlesztés áttekintése](develop-overview.md).
 