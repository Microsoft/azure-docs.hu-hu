---
title: Az Azure SQL-adatbázis teljesítmény-finomhangolási útmutató |} A Microsoft Docs
description: Ismerje meg manuálisan a az Azure SQL Database lekérdezési teljesítmény hangolásához ajánlásokat használva.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 10/22/2018
ms.openlocfilehash: 95e09532616b4aff05dad7440dcda6872fd27484
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49645524"
---
# <a name="manual-tune-query-performance-in-azure-sql-database"></a>Manuális hangolás az Azure SQL Database lekérdezési teljesítmény

Miután azonosította, hogy az SQL Database szolgáltatással fennálló egy teljesítményprobléma, ez a cikk célja segítséget:

- Hallgassa meg az alkalmazást, és a alkalmazni néhány ajánlott eljárást, amely növelheti a teljesítményt.
- Az adatbázis hangolása indexek és lekérdezések hatékonyabban dolgozhat az adatokkal való módosításával.

Ez a cikk feltételezi, hogy Ön már már használta az Azure SQL Database révén [advisor-javaslatok adatbázis](sql-database-advisor.md) és az Azure SQL Database [automatikus finomhangolási ajánlásait](sql-database-automatic-tuning.md). Azt is feltételezi, hogy áttekintette [megfigyelés és finomhangolás áttekintést](sql-database-monitor-tune-overview.md) és a kapcsolódó cikkekben kapcsolatos teljesítménnyel kapcsolatos problémák elhárítása. Emellett a jelen cikk feltételezi, hogy nem kell a Processzor-erőforrások, futó kapcsolatos teljesítménnyel kapcsolatos problémák, megoldhatók a számítási méret növelése, vagy az adatbázis további erőforrásokat a szolgáltatásszintet.

## <a name="tune-your-application"></a>Az alkalmazás hangolása

A hagyományos helyszíni SQL Server a folyamat a kezdeti a kapacitástervezés során gyakran elkülönül egy alkalmazást az éles üzemben futó folyamat. Hardver- és licenceket vásárolt először, és ezt követően teljesítményhangolás történik. Azure SQL Database használata esetén célszerű interweave alkalmazások futtatásához és a finomhangolás azt a folyamatot. Az igény szerinti kapacitás és a modell hangolhassa az alkalmazás helyett a későbbi növekedési tervek egy alkalmazáshoz, amely gyakran helytelenek Találgatások alapján hardveren túlzott kiosztása most szükséges minimális erőforrásokat használhatja. Egyes ügyfeleink előfordulhat, hogy dönt, hogy nem egy alkalmazás hangolása, és inkább be túltervezés hardver-erőforrások. Ez a megközelítés lehet célszerű, ha nem szeretné módosítani egy fő alkalmazást egy foglalt időszakban. De, egy alkalmazás hangolása minimalizálhatja több erőforrást és alacsonyabb havi számlák a szolgáltatási szintek az Azure SQL Database használatakor.

### <a name="application-characteristics"></a>Alkalmazástulajdonságok

Bár az Azure SQL Database szolgáltatási szinteken javíthatja a teljesítményt stabilitását és kiszámíthatóságot nyújtanak az alkalmazás készültek, néhány ajánlott eljárást segíthet az alkalmazás jobban kihasználhatja az erőforrásokat a számítási méret hangolása. Bár számos alkalmazás jelentős teljesítménynövekedést egyszerűen szerint átválthat egy nagyobb méretű számítási vagy szolgáltatásszintet, bizonyos alkalmazásokat kell, hogy további hangolásra számára, hogy egy magasabb szintű szolgáltatást. A nagyobb teljesítmény érdekében fontolja meg a további alkalmazás hangolása az alkalmazásokat, amelyek a következő jellemzőkkel rendelkezik:

- **Az alkalmazásokat, "forgalmas" viselkedése miatt lassú**

  Forgalmas alkalmazások győződjön meg a túlzott adatelérési műveletek, amelyek a hálózati késés-és nagybetűket. Előfordulhat, hogy módosítania az ilyen típusú alkalmazásokat, az SQL Database adatelérési műveletek számának csökkentése érdekében. Alkalmazások teljesítményének javíthatja például ugyanazzal a módszerrel, például a kötegelés ad hoc lekérdezéseket, és a lekérdezések áthelyezése az tárolt eljárásokat. További információkért lásd: [lekérdezések Batch](#batch-queries).

- **Egy nagy számításigényű számítási feladatot, amely egy teljes egyetlen gép által nem támogatott az adatbázisok**

   Keretet túllépő része az erőforrásokat a legnagyobb prémium szintű adatbázisok számítási méret előnyös lehet a számítási feladatok skálázása. További információkért lásd: [adatbázisközi horizontális skálázási](#cross-database-sharding) és [funkcionális particionálást](#functional-partitioning).

- **Az alkalmazásokat, az optimálisnál rosszabb lekérdezések**

  Alkalmazások, különösen az adatelérési réteg, hogy a rosszul hangolt lekérdezések nem előnyös lehet a nagyobb számítási méretét. Ez magában foglalja a lekérdezéseket, amelyek nem tartozik a WHERE záradék, a hiányzó indexek vagy statisztika elavult. Ezek az alkalmazások kihasználhatják a standard szintű lekérdezési teljesítmény hangolása módszerek. További információkért lásd: [hiányzó index](#identifying-and-adding-missing-indexes) és [lekérdezések hangolása, és a felhasználóknak](#query-tuning-and-hinting).

- **Ez az optimálisnál rosszabb adatok hozzáférési tervezési rendelkező alkalmazások**

   Az alkalmazásokat, adatokat rejlő hozzáférés egyidejűségi problémák, például deadlocking, nem előnyös lehet a nagyobb számítási méretét. Vegye fontolóra az Azure Caching service és a egy másik gyorsítótárazási technológián ügyféloldali gyorsítótárazási adatok által adatváltások ellen az Azure SQL Database csökkentését. Lásd: [alkalmazás szinten gyorsítótárazás](#application-tier-caching).

## <a name="tune-your-database"></a>Az adatbázis hangolása

Ebben a szakaszban áttekintjük egyes módszereket, amelyek segítségével az Azure SQL Database kapjanak az alkalmazáshoz a lehető legjobb teljesítményt, és futtassa azt a legkisebb lehetséges számítási méret hangolása. Néhány, az alábbi eljárások egyezik, hagyományos SQL Server ajánlott hangolási, de mások csak az adott Azure SQL Database. Néhány esetben vizsgálja meg a felhasznált erőforrások területek további finomhangolása és kiterjesztése a hagyományos SQL Server technikákkal működjön az Azure SQL Database-ben található adatbázishoz.

### <a name="identifying-and-adding-missing-indexes"></a>Azonosító és a hiányzó indexek hozzáadása

Az OLTP adatbázis-teljesítmény gyakran okoz problémát a fizikai adatbázis-tervezésben vonatkozik. Gyakran adatbázissémák tervezett, és nagy mennyiségű (akár a terhelés, akár az adatok mennyisége) tesztelési nélkül. Sajnos a lekérdezésterv teljesítményét előfordulhat, hogy kis léptékű elfogadható lehet, de jelentősen csökkentheti a termelési szintű adatmennyiség alapján. A leggyakoribb probléma forrása kielégíteni szűrők vagy egyéb korlátozások egy lekérdezést a megfelelő indexek hiánya. Hiányzó indexek jegyzékek táblázatként gyakran előfordul, ha egy index pozicionálási sikerült elegendő vizsgálata.

Ebben a példában a kiválasztott lekérdezésterv vizsgálat használja, amikor egy pozicionálási elegendő lenne:

```sql
DROP TABLE dbo.missingindex;
CREATE TABLE dbo.missingindex (col1 INT IDENTITY PRIMARY KEY, col2 INT);
DECLARE @a int = 0;
SET NOCOUNT ON;
BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO dbo.missingindex(col2) VALUES (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION;
    GO
SELECT m1.col1
    FROM dbo.missingindex m1 INNER JOIN dbo.missingindex m2 ON(m1.col1=m2.col1)
    WHERE m1.col2 = 4;
```

![Hiányzó indexekkel rendelkező lekérdezésterv](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

Az Azure SQL Database segítségével, keresés és a javítás közös hiányzó index feltételeket. DMV-vel az Azure SQL Database-be épített tekintse meg a lekérdezés fordítások, amelyben az index lenne jelentősen csökkentheti a becsült költség, a lekérdezés futtatásához. Lekérdezés-végrehajtás során az SQL Database nyomon követi, milyen gyakran az egyes lekérdezési terv végrehajtásakor, és a becsült gap követi a lekérdezést végrehajtó terv és az imagined között, ahol újjáépítésére létezett. Ezeket a dinamikus felügyeleti nézetek segítségével gyorsan kitalálni milyen módosításokat a fizikai adatbázis-tervezésben való javíthatja az adatbázis és a valós számítási feladat a számítási feladatok összköltség.

Ez a lekérdezés segítségével lehetséges a hiányzó indexek kiértékelése:

```sql
SELECT
   CONVERT (varchar, getdate(), 126) AS runtime
   , mig.index_group_handle
   , mid.index_handle
   , CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
        (migs.user_seeks + migs.user_scans)) AS improvement_measure
   , 'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
        CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
        (' + ISNULL (mid.equality_columns,'')
        + CASE WHEN mid.equality_columns IS NOT NULL
        AND mid.inequality_columns IS NOT NULL
        THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '') + ')'
        + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement
   , migs.*
   , mid.database_id
   , mid.[object_id]
FROM sys.dm_db_missing_index_groups AS mig
   INNER JOIN sys.dm_db_missing_index_group_stats AS migs
      ON migs.group_handle = mig.index_group_handle
   INNER JOIN sys.dm_db_missing_index_details AS mid
      ON mig.index_handle = mid.index_handle
 ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC
```

Ebben a példában a lekérdezés eredményezett, ez a javaslat:

```sql
CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  
```

A létrehozást követően, hogy ugyanazon SELECT utasítás választja ki egy másik sémát használ, a keresés nem egy vizsgálatot, és hatékonyabban végrehajtja a terv:

![Javított indexekkel rendelkező lekérdezésterv](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

A legfontosabb elemzéseket, hogy a megosztott, hagyományos rendszer IO-kapacitás korlátozottabb, mint a dedikált kiszolgáló gép. Nincs prémium minimalizálja a szükségtelen I/O igénybe maximális a rendszer az a dtu-k minden számítási méretű, az Azure SQL Database szolgáltatási csomagjai. Választási lehetőségek jelentősen növelheti a késést az egyes lekérdezések esetén megfelelő fizikai adatbázis-tervezésben javítani az egyidejű kérelmek skálázási egység kezeli, és minimalizálja a költségeket, a lekérdezés kielégítéséhez szükséges. A hiányzó index DMV-vel kapcsolatos további információkért lásd: [sys.dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx).

### <a name="query-tuning-and-hinting"></a>Lekérdezések hangolása, és a felhasználóknak

A lekérdezésoptimalizáló Azure SQL Database-ben a hagyományos SQL Server-lekérdezésoptimalizáló hasonlít. A legtöbb ajánlott eljárást a Lekérdezések finomhangolása és az indoklást ismertetése a lekérdezésoptimalizáló modell korlátozásai is vonatkoznak az Azure SQL Database. Ha az Azure SQL Database lekérdezések hangolás kaphat további előnye, hogy az összesített erőforrás-igényű csökkentését. Lehet, hogy az alkalmazás futtatható, mint a nem érdemes figyelemmel kísérnie egyenértékű alacsonyabb költségek, mert futtatható egy alacsonyabb számítási méret.

Ez gyakori az SQL Server, és amely akkor is érvényes az Azure SQL Database például hogyan a lekérdezésoptimalizáló "megszerzésén" paraméterek. A lekérdezésoptimalizáló fordításkor, kiértékeli a meghatározásához, hogy létrehozhat-e több optimális lekérdezésterv paraméter aktuális értékének. Bár ez a stratégia gyakran is vezet, amely jelentősen gyorsabb, mint egy csomag nélkül ismert paraméterértékeket összeállított lekérdezésterv, jelenleg működik imperfectly mind az SQL Server és az Azure SQL Database-ben. Néha az a paraméter nem felszippantásra, és néha a paraméter felszippantásra van, de a létrehozott terv az optimálisnál rosszabb a paraméterértékeket a számítási feladat teljes készletét. Microsoft lekérdezésmutatókat (irányelvek) tartalmaz, adja meg a leképezés több szándékosan, és felülírhatja az alapértelmezett viselkedést, a paraméter elemző. Gyakran mutatók használatakor orvosolásával eseteket, amelyben az alapértelmezett SQL Server vagy az Azure SQL Database viselkedés hiányos, az egy adott ügyfél számítási feladata.

A következő példa bemutatja, hogyan hozhat létre a lekérdezésfeldolgozó az optimálisnál rosszabb, mind a teljesítmény- és erőforrás-igényű csomagot. Ebben a példában is látható, hogy ha a lekérdezés-végrehajtási használja, csökkentheti a lekérdezés futtatása időre és erőforrás-követelmények az SQL Database:

```sql
DROP TABLE psptest1;
CREATE TABLE psptest1(col1 int primary key identity, col2 int, col3 binary(200));
DECLARE @a int = 0;
SET NOCOUNT ON;
BEGIN TRANSACTION
   WHILE @a < 20000
   BEGIN
     INSERT INTO psptest1(col2) values (1);
     INSERT INTO psptest1(col2) values (@a);
     SET @a += 1;
   END
   COMMIT TRANSACTION
   CREATE INDEX i1 on psptest1(col2);
GO

CREATE PROCEDURE psp1 (@param1 int)
   AS
   BEGIN
      INSERT INTO t1 SELECT * FROM psptest1
      WHERE col2 = @param1
      ORDER BY col2;
    END
    GO

CREATE PROCEDURE psp2 (@param2 int)
   AS
   BEGIN
      INSERT INTO t1 SELECT * FROM psptest1 WHERE col2 = @param2
      ORDER BY col2
      OPTION (OPTIMIZE FOR (@param2 UNKNOWN))
   END
   GO

CREATE TABLE t1 (col1 int primary key, col2 int, col3 binary(200));
GO
```

A telepítő kódot hoz létre egy táblát, amely rendelkezik torzítja az adatok terjesztési. Az optimális lekérdezési terv eltérő alapján mely paraméter van kiválasztva. Sajnos az gyorsítótárazási viselkedésének a csomag nem mindig újrafordítottuk a lekérdezés a leggyakrabban használt paraméter értéke alapján. Így lehetőség az optimálisnál rosszabb csomag gyorsítótárazott és sok érték, akkor is, ha egy másik csomagot lehet jobb választás terv átlagosan használt. Ezután a lekérdezésterv hoz létre két tárolt eljárások, amelyek azonos, azzal a különbséggel, hogy egy olyan speciális lekérdezés-végrehajtási rendelkezik.

```sql
-- Prime Procedure Cache with scan plan
EXEC psp1 @param1=1;
TRUNCATE TABLE t1;

-- Iterate multiple times to show the performance difference
DECLARE @i int = 0;
WHILE @i < 1000
   BEGIN
      EXEC psp1 @param1=2;
      TRUNCATE TABLE t1;
      SET @i += 1;
    END
```

Azt javasoljuk, hogy várjon legalább 10 percig, mielőtt megkezdené a példában a 2. rész, hogy az eredmények nem azonos az eredményül kapott telemetriai adatok.

```sql
EXEC psp2 @param2=1;
TRUNCATE TABLE t1;

DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END
```

Ebben a példában az egyes részek megpróbál futtatni egy paraméteres insert utasítás 1000 alkalommal (az egy megfelelő terhelést generálhat, használja, mint egy tesztelési adatkészlet). Tárolt eljárások végrehajtása, ha a lekérdezésfeldolgozó megvizsgálja a paraméter értéket, amelyet az eljárás során az első fordítás (paraméter "elemzése"). A processzor gyorsítótárazza az eredményül kapott csomagot, és felhasználja újabb meghívásához, még akkor is, ha a paraméter értéke nem egyezik. Az optimális tervet előfordulhat, hogy nem minden esetben használható. Néha szüksége is az optimalizáló jobb, ha az adott esetben helyett az átlagos eset, amikor először a lekérdezés fordítása csomagot választja ki. Ebben a példában a kezdeti terv, amely beolvassa az összes sort található minden egyes érték, amely megegyezik a paraméter "vizsgálat" csomag hoz létre:

![Lekérdezés hangolása a vizsgálati csomag használatával](./media/sql-database-performance-guidance/query_tuning_1.png)

Hajtottunk végre az eljárást az 1 értéket, mert az eredményül kapott terv optimális 1 érték volt, de az optimálisnál rosszabb értéket a táblázatban minden más helyen lett. Az eredmény valószínűleg nem mit érdemes Ha véletlenszerűen kiválasztható minden egyes előfizetés, mert a csomag lassabban hajt végre, és több erőforrást használ.

Ha a teszt- `SET STATISTICS IO` beállítása `ON`, a logikai vizsgálat ebben a példában a munkát a háttérben. Láthatja, hogy nincsenek-e a terv (ez nem elég hatékony, ha az átlagos eset, hogy csak egy sort adja vissza) által végzett 1,148 olvasások:

![Lekérdezés hangolása egy logikai vizsgálat használatával](./media/sql-database-performance-guidance/query_tuning_2.png)

Második része a példában a lekérdezés-végrehajtási állapítható meg, hogy egy adott érték használata a fordítás során az optimalizáló használ. Ebben az esetben arra kényszeríti a lekérdezésfeldolgozó figyelmen kívül hagyja az értéket, amelyet paraméterként, és ehelyett feltételezzük `UNKNOWN`. Ez az érték, amely az átlagos gyakoriság tartozik a táblában (a döntés figyelmen kívül hagyása) hivatkozik. Az eredményül kapott terv Ez gyorsabb és kevesebb erőforrást, átlagosan, mint a csomagot használ, az 1. rész ebben a példában a keresés-alapú csomag:

![A lekérdezés-végrehajtási lekérdezések hangolása](./media/sql-database-performance-guidance/query_tuning_3.png)

Láthatja, hogy a hatása a **sys.resource_stats** tábla (késik, hajtsa végre a vizsgálat, és ha az adatokat tölti fel a tábla kezdve). Ebben a példában, 1. rész a 22-es: 25-ös: 00 időszakban végrehajtott és 2. rész 22:35:00-kor végrehajtott. A korábbi időtartomány további erőforrások az adott időtartományban, mint az újabb már (miatt a terv hatékonysága fejlesztései) használja.

```sql
SELECT TOP 1000 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![Lekérdezés hangolása példa eredmények](./media/sql-database-performance-guidance/query_tuning_4.png)

> [!NOTE]
> Bár ebben a példában a kötet nem szándékosan kis méretű, ez az optimálisnál rosszabb paraméterek hatásának jelentős, különösen a nagyobb adatbázisok lehet. A különbség a szélsőséges esetben másodperc azokra az esetekre, gyors és lassú esetek óra közötti lehet.

Megvizsgálhatja **sys.resource_stats** -e az erőforrás, tesztelje erőforrást használ, több vagy kevesebb mint egy másik meghatározásához. Ha összehasonlítjuk az adatokat, külön tesztek ütemezése úgy, hogy azok nem azonos az 5 perces ablakban az a **sys.resource_stats** megtekintése. A gyakorlat célja használt erőforrások teljes mennyisége minimalizálása érdekében, illetve nem csökkentheti minimálisra csúcs erőforrásokat. Általában a optimalizálása késésének kódrészleteket is csökkenti erőforrás-használat. Győződjön meg arról, hogy szükség az alkalmazáshoz a módosításokat, és, hogy a módosítások nem negatívan befolyásolják a vásárlói élményt, a személy, aki lekérdezésmutatókat használhatja az alkalmazásban.

Ha egy számítási feladat ismétlődő lekérdezések készletével rendelkezik, gyakran logikus rögzítéséhez és érvényesíteni a terv lehetősége az optimalizálási, mert azt a minimális erőforrás mérete egység az adatbázis üzemeltetéséhez szükséges meghajtók. Azt ellenőrzése után időnként újra a segítségével győződjön meg arról, hogy azok rendelkeznek csökkent, a csomagok. További információ [mutatók (Transact-SQL) lekérdezés](https://msdn.microsoft.com/library/ms181714.aspx).

### <a name="cross-database-sharding"></a>Adatbázisközi horizontális skálázás

Mivel az Azure SQL Database a hagyományos hardvereken fut, egy önálló adatbázis kapacitáskorlátjai alacsonyabb, mint a hagyományos helyszíni SQL Server telepítésekor. Egyes ügyfeleink a horizontális skálázás technikák használatával terjednek több adatbázis adatbázis-műveletek, a műveletek nem illő önálló adatbázis az Azure SQL Database keretein belül. A legtöbb ügyfeleink, akik a horizontális skálázás technikák az Azure SQL Database felosztása egy dimenziót az adataikat több adatbázis között. Az ezzel a módszerrel kell megérteni, hogy OLTP alkalmazások gyakran hajtják végre a tranzakciók, amelyek a alkalmazni egyetlen sort vagy sorokat a sémában egy kisebb csoportot.

> [!NOTE]
> Az SQL Database mostantól lehetőséget nyújt, amelyek segítik a horizontális skálázás könyvtárba. További információkért lásd: [Elastic Database-ügyfélkódtár áttekintése](sql-database-elastic-database-client-library.md).

Például ha egy adatbázis ügyfél neve, sorrend és rendelés részletei (például a hagyományos példa Northwind adatbázist részét képező SQL Server), akkor tehet fel ezeket az adatokat több adatbázis-okat a kapcsolódó rendelési és a rendelések részleteit ügyfél információk. Garantálhatja, hogy az ügyfél adatainak egyetlen adatbázisban marad. Az alkalmazás különböző ügyfelektől lenne elosztja a adatbázisok gyakorlatilag a terhelés elosztható a több adatbázis között. Horizontális skálázási ügyfelek nem csupán elkerülheti a maximális méretkorlátot, de Azure SQL Database is képes feldolgozni, amelyek jelentősen nagyobb, mint a különböző számítási méretekre korlátozásait, mindaddig, amíg minden egyes adatbázis illik bele a dtu-k számítási feladatokhoz.

Bár az adatbázis horizontális skálázási megoldás összesített erőforrás-kapacitása nem csökkenthető, több adatbázis futó nagyon nagy méretű megoldások támogatása rendkívül hatékony. Minden adatbázis a különböző számítási méret támogatása nagyon nagy méretű, magas erőforrás-igényű adatbázisok "érvényes" futtathatja.

### <a name="functional-partitioning"></a>Funkcionális particionálás

SQL Server-felhasználók gyakran kombinálásával egyetlen adatbázisban számos függvénye. Például ha egy alkalmazás logikai kezelheti a készletet egy tároló, a, hogy az adatbázis előfordulhat Készletkövetés vásárlási megrendelést, a tárolt eljárások és a hónap vége jelentéskészítés kezelése az indexelt vagy tényleges táblán alapuló nézetek társított logikát. Ez a technika megkönnyíti a különböző műveletek, mint például a biztonsági mentés felügyeletéhez, de azt is szükséges, hogy a hardvert, hogy a maximális terhelés kezeléséhez minden functions-alkalmazások közötti méretre.

Ha egy Azure SQL Database-ben kibővített architektúrát használ, érdemes felosztani a különböző funkciók különböző adatbázisok egy alkalmazás Ez a módszer használata esetén mindegyik alkalmazás egymástól függetlenül méretezhető. Egy alkalmazás elfoglaltabb válik (és az adatbázison a terhelés növekszik), a rendszergazdák kiválaszthatják független számítási méretei minden függvény az alkalmazásban. Címen a határt, ebben az architektúrában az alkalmazás lehet nagyobb, mint egy hagyományos egyetlen gép képes kezelni, mert a terhelés több gép között megoszlik.

### <a name="batch-queries"></a>Kötegelt lekérdezések

Alkalmazásokhoz, melyek segítségével a nagy mennyiségű adatok eléréséhez az ad hoc kérdez le, gyakori válaszidő jelentős mennyiségű van költött az alkalmazás és az Azure SQL Database réteget közötti hálózati kommunikáció. Akkor is, ha az alkalmazás és az Azure SQL Database találhatók ugyanabban az adatközpontban, a kettő közötti hálózati késés előfordulhat, hogy lehet rögzített adatok nagy számú adatelérési műveletek. A csökkentéséhez le ciklikus léptető az adatelérési műveletek esetében, érdemes lehet a beállítást vagy a az ad hoc lekérdezéseket kötegelt, illetve a tárolt eljárások fordítsa őket. Ha az ad hoc lekérdezéseket kötegelt, az Azure SQL Database több lekérdezés elküldheti az egyetlen adatváltási egy nagy méretű Batch. Összeállíthatja a tárolt ad hoc lekérdezéseket, mintha azokat, a batch érhet el tudta ugyanazt az eredményt. Tárolt eljárás használatával is biztosít az Azure SQL Database-ben a lekérdezésterveket gyorsítótárazási, így ismét használhatja a tárolt eljárás nagyobb eséllyel előnyeit.

Egyes alkalmazások célozzák. Néha csökkentheti egy adatbázis teljes i/o terhelését a mérlegeli írások kötegelendő hogyan. Ez gyakran előfordul, a bonyolultabb, mint az explicit tranzakciók a tárolt eljárások és ad-hoc kötegek automatikusan véglegesítési tranzakció helyett. Különböző módszereket használhat értékelése, lásd: [Azure SQL Database-alkalmazások technikák kötegelés](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx). Kísérletezzen a saját számítási feladatok, a jobb oldali modell található a kötegelés. Győződjön meg arról, a modell előfordulhat, hogy kissé eltérő tranzakciós konzisztenciagaranciákat rendelkezni. Keresés, így segít csökkenti az erőforrás-használat megfelelő számítási feladatokra van szükség konzisztencia és a teljesítmény feláldozását kombinációjával keresése.

### <a name="application-tier-caching"></a>Alkalmazásrétegek gyorsítótárazás

Egyes adatbázis-alkalmazások rendelkezik olvasási számítási feladatokhoz. Gyorsítótárazás rétegek előfordulhat, hogy csökkentse az adatbázis terhelését, és előfordulhat, hogy lehetséges csökkentése érdekében a számítási méret az Azure SQL Database-adatbázis támogatásához szükséges. A [Azure Redis Cache](https://azure.microsoft.com/services/cache/), ha egy olvasási munkaterhelés, többször is beolvashatja az adatokat (vagy egyszer gépenként alkalmazásrétegek – konfigurációjától függően), és biztonságosan tárolja a kívül az SQL-adatbázis az adatokat. Ezzel a módszerrel (Processzor- és olvasási i/o-) adatbázisok terhelésének csökkentése érdekében, de nincs tranzakció-konzisztencia hatással lesz, mivel előfordulhat, hogy az adatok olvasása a gyorsítótárból nincs szinkronban az adatokat az adatbázisban. Számos alkalmazásban a bizonyos fokú inkonzisztenciát nem elfogadható, ez nem igaz, bármely számítási feladat számára. Egy alkalmazás szintű gyorsítótárazási stratégia megvalósítása előtt, teljes mértékben ismernie kell minden olyan alkalmazás követelményeinek.

## <a name="next-steps"></a>További lépések

- További információ a DTU-alapú szolgáltatásszintek: [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md).
- További információ a Virtuálismag-alapú szolgáltatásszintek: [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md).
- Rugalmas készletek kapcsolatos további információkért lásd: [Mi az az Azure rugalmas készlet?](sql-database-elastic-pool.md)
- Teljesítmény és a rugalmas készletek kapcsolatos információkért lásd: [mikor érdemes rugalmas készlet](sql-database-elastic-pool-guidance.md)