---
title: A Visual studióhoz készült Azure Data Lake-eszközök feloldása
description: A lehetséges megoldásokkal kapcsolatos problémák elhárítása a Visual studióhoz készült Azure Data Lake Tools használatával.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 12/16/2016
ms.openlocfilehash: 0b82ce187c06afa69e54ea93931e1745f0d52674
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92219190"
---
# <a name="resolve-data-skew-problems-by-using-azure-data-lake-tools-for-visual-studio"></a>Egyenetlen adateloszlással kapcsolatos hibák megoldása az Azure Data Lake Tools for Visual Studióval

## <a name="what-is-data-skew"></a>Mi az az adattorzítás?

Röviden kijelentette, hogy az adat-elferdítés egy túlreprezentált érték. Képzelje el, hogy a 50-es adózási vizsgáztatókat az adóbevallások naplózásához rendelte, és egy vizsgáztatót az USA minden állama számára. A Wyoming-vizsgáztató, mert a populáció kicsi, kevés a teendő. Kaliforniában azonban a vizsgáztatónak nagyon elfoglaltnak kell lennie az állam nagy populációja miatt.
    ![Adatelemzési probléma – példa](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/data-skew-problem.png)

Ebben az esetben az adatelemzések egyenlőtlenül oszlanak el az összes adóhatóságnál, ami azt jelenti, hogy egyes vizsgáztatóknak többet kell dolgozniuk, mint a többinél. A saját feladataiban gyakran tapasztalnak olyan szituációkat, mint például az adó-vizsgáztató példa. További technikai szempontból az egyik csúcspont sokkal több adatmennyiséget kap, mint a társai, olyan helyzet, amellyel a csúcspont a többinél nagyobb mértékben működik, és végül lelassítja a teljes feladatot. Ami még rosszabb, a feladat meghiúsulhat, mivel a csúcspontok rendelkezhetnek például egy 5 órás futtatókörnyezeti korlátozással és egy 6 GB-os memória korlátozásával.

## <a name="resolving-data-skew-problems"></a>Az adatelhárítási problémák elhárítása

A Visual studióhoz készült Azure Data Lake Tools segítségével észlelhető, hogy a feladatainak van-e elferdíthető problémája. Ha probléma merül fel, az ebben a szakaszban ismertetett megoldások kipróbálásával megoldhatja.

## <a name="solution-1-improve-table-partitioning"></a>1. megoldás: a tábla particionálásának javítása

### <a name="option-1-filter-the-skewed-key-value-in-advance"></a>1. lehetőség: a ferde kulcs értékének szűrése előre

Ha nem befolyásolja az üzleti logikát, akkor előre szűrheti a magasabb frekvenciájú értékeket. Ha például sok 000-000-000 szerepel az oszlop GUID azonosítójában, akkor előfordulhat, hogy nem szeretné összesíteni ezt az értéket. Az összesítés előtt írja be a "WHERE GUID! =" 000-000-000 "" kifejezést a nagy gyakoriságú érték szűréséhez.

### <a name="option-2-pick-a-different-partition-or-distribution-key"></a>2. lehetőség: válasszon másik partíciót vagy terjesztési kulcsot

Az előző példában, ha azt szeretné, hogy csak az összes ország/régió esetében ellenőrizze az adózási naplózási feladatokat, javítsa az adateloszlást úgy, hogy kijelöli az azonosító számot a kulcsként. A különböző partíciók vagy terjesztési kulcsok kiválogatása esetenként egyenletesen terjesztheti az adatait, de meg kell győződnie arról, hogy ez a választás nem befolyásolja az üzleti logikát. Például az egyes állapotokhoz tartozó adózási összeg kiszámításához érdemes lehet kijelölni az _állapotot_ partíciós kulcsként. Ha továbbra is ezt a problémát tapasztalja, próbálkozzon a 3. lehetőséggel.

### <a name="option-3-add-more-partition-or-distribution-keys"></a>3. lehetőség: további partíciók vagy terjesztési kulcsok hozzáadása

Ahelyett, hogy csak az _állapotot_ használja partíciós kulcsként, több kulcsot is használhat a particionáláshoz. Tegyük fel például, hogy a _zip-kódot_ további partíciós kulcsként adja hozzá az adatpartíciós méretek csökkentése és az adatmennyiség egyenletes elosztása érdekében.

### <a name="option-4-use-round-robin-distribution"></a>4. lehetőség: a ciklikus multiplexelés eloszlásának használata

Ha nem talál megfelelő kulcsot a particionáláshoz és a terjesztéshez, próbálja meg a ciklikus multiplexelés eloszlását használni. A ciklikus multiplexelés eloszlása minden sort egyenlően kezel, és véletlenszerűen helyezi őket a megfelelő gyűjtőbe. Az adatok egyenletes eloszlásban jelentkeznek, de a rendszer elveszti a helységek adatait, ami csökkenti a feladatok teljesítményét bizonyos műveletek esetében. Emellett, ha a ferde kulcshoz tartozó összesítést végez, az adatsérülést okozó probléma továbbra is fennáll. Ha többet szeretne megtudni a ciklikus időszeleteléses terjesztésről, tekintse meg a [create Table (u-SQL)](/u-sql/ddl/tables/create/managed/create-table-u-sql-creating-a-table-with-schema#dis_sch)u-SQL-táblázat disztribúciók szakaszát: tábla létrehozása sémával.

## <a name="solution-2-improve-the-query-plan"></a>2. megoldás: a lekérdezési terv továbbfejlesztése

### <a name="option-1-use-the-create-statistics-statement"></a>1. lehetőség: a CREATE STATISTICS utasítás használata

A U-SQL biztosítja a CREATE STATISTICS utasítást a táblákon. Ez az utasítás további információkat nyújt a lekérdezés-optimalizáló szolgáltatásról az adatjellemzőkkel, például az értékek eloszlásával kapcsolatban, amelyek egy táblában vannak tárolva. A lekérdezés-optimalizáló a legtöbb lekérdezés esetében már létrehozta a szükséges statisztikai adatokat egy magas színvonalú lekérdezési tervhez. Esetenként előfordulhat, hogy a lekérdezési teljesítmény javításához további statisztikák létrehozásával vagy a lekérdezési terv módosításával kell megadnia a statisztikát. További információ: [create Statistics (U-SQL)](/u-sql/ddl/statistics/create-statistics) oldal.

Kód példa:

```usql
CREATE STATISTICS IF NOT EXISTS stats_SampleTable_date ON SampleDB.dbo.SampleTable(date) WITH FULLSCAN;
```

>[!NOTE]
>A statisztikai adatok nem frissülnek automatikusan. Ha egy táblában lévő adatokat a statisztikák újbóli létrehozása nélkül frissíti, a lekérdezés teljesítménye elutasításra kerülhet.

### <a name="option-2-use-skewfactor"></a>2. lehetőség: a SKEWFACTOR használata

Ha az egyes állapotokhoz tartozó adót szeretné összefoglalni, akkor a CSOPORTOSÍTÁSi állapotot kell használnia, egy olyan megközelítést, amely nem kerüli el az adatvesztést okozó problémát. Azonban megadhat egy adatcélzást a lekérdezésben, hogy azonosítsa az adattorzítást a kulcsokban, hogy az optimalizáló előkészítse a végrehajtási tervet.

A paramétert általában 0,5-as és 1-es értékre állíthatja, és a 0,5 szó nem sokkal ferde és 1 jelentéssel bír. Mivel a mutató hatással van az aktuális utasítás és az összes alárendelt utasítás végrehajtási tervének optimalizálására, ügyeljen arra, hogy a tippet a lehetséges elferdített kulcs-Wise összesítés előtt adja hozzá.

```usql
SKEWFACTOR (columns) = x
```

Azt a tippet adja meg, hogy a megadott oszlopokban az x a 0 (nem ferde) értékkel van elferdítve 1 (nagyon nagy ferdeség).

Kód példa:

```usql
//Add a SKEWFACTOR hint.
@Impressions =
    SELECT * FROM
    searchDM.SML.PageView(@start, @end) AS PageView
    OPTION(SKEWFACTOR(Query)=0.5)
    ;
//Query 1 for key: Query, ClientId
@Sessions =
    SELECT
        ClientId,
        Query,
        SUM(PageClicks) AS Clicks
    FROM
        @Impressions
    GROUP BY
        Query, ClientId
    ;
//Query 2 for Key: Query
@Display =
    SELECT * FROM @Sessions
        INNER JOIN @Campaigns
            ON @Sessions.Query == @Campaigns.Query
    ;
```

### <a name="option-3-use-rowcount"></a>3. lehetőség: a sorszám használata
A SKEWFACTOR mellett, ha tudja, hogy a másik csatlakoztatott sor kisebb, akkor az optimalizáláshoz adja meg a sorszám mutatót az U-SQL-utasításban a csatlakozás előtt. Így a-optimalizáló a teljesítmény javítása érdekében a szórásos csatlakoztatási stratégiát is kiválaszthatja. Vegye figyelembe, hogy a sorszám nem oldja meg az adatvesztést okozó problémát, de további segítséget nyújthat.

```usql
OPTION(ROWCOUNT = n)
```

Azonosítson egy kis sort a csatlakozás előtt, és adjon meg egy becsült egész sort.

Kód példa:

```usql
//Unstructured (24-hour daily log impressions)
@Huge   = EXTRACT ClientId int, ...
            FROM @"wasb://ads@wcentralus/2015/10/30/{*}.nif"
            ;
//Small subset (that is, ForgetMe opt out)
@Small  = SELECT * FROM @Huge
            WHERE Bing.ForgetMe(x,y,z)
            OPTION(ROWCOUNT=500)
            ;
//Result (not enough information to determine simple broadcast JOIN)
@Remove = SELECT * FROM Bing.Sessions
            INNER JOIN @Small ON Sessions.Client == @Small.Client
            ;
```

## <a name="solution-3-improve-the-user-defined-reducer-and-combiner"></a>3. megoldás: a felhasználó által definiált szűkítő és-kombinálás javítása

Előfordulhat, hogy egy felhasználó által definiált operátort is írhat a bonyolult folyamat logikájának kezelésére, és egy jól megírt csökkentő és kombináló megoldás bizonyos esetekben enyhítheti az adattorzítási problémákat.

### <a name="option-1-use-a-recursive-reducer-if-possible"></a>1. lehetőség: rekurzív szűkítő használata, ha lehetséges

Alapértelmezés szerint a felhasználó által definiált szűkítő nem rekurzív módban fut, ami azt jelenti, hogy a kulcsok működésének csökkentése egyetlen csúcsponton történik. Ha azonban az adatai elferdítve vannak, előfordulhat, hogy a hatalmas adatkészletek feldolgozása egyetlen csúcsponton történik, és hosszú ideig fut.

A teljesítmény javítása érdekében hozzáadhat egy attribútumot a kódban, amellyel meghatározható, hogy a szűkítő rekurzív módban fusson. Ezt követően a hatalmas adatkészletek több csúcspontra is kiterjeszthetők, és párhuzamosan futtathatók, ami felgyorsítja a feladatot.

A nem rekurzív szűkítő rekurzív megváltoztatásához meg kell győződnie arról, hogy az algoritmus asszociatív. Az összeg például asszociatív, a medián pedig nem. Azt is meg kell győződnie arról, hogy a redukáló bemenete és kimenete megtartja ugyanazt a sémát.

Rekurzív szűkítő attribútuma:

```usql
[SqlUserDefinedReducer(IsRecursive = true)]
```

Kód példa:

```usql
[SqlUserDefinedReducer(IsRecursive = true)]
public class TopNReducer : IReducer
{
    public override IEnumerable<IRow>
        Reduce(IRowset input, IUpdatableRow output)
    {
        //Your reducer code goes here.
    }
}
```

### <a name="option-2-use-row-level-combiner-mode-if-possible"></a>2. lehetőség: a sor szintű kombináló mód használata, ha lehetséges

Az adott ferde kulcsú illesztési esetekhez hasonlóan a sorszám-mutató is hasonló, így a többszörösen elferdített kulcsos értékek több csúcspontra is kiterjeszthetők, így a munka egyidejűleg is végrehajtható. A Combiner mód nem tudja feloldani az adat-elferdítő problémákat, de további segítséget nyújthat a nagy méretű, ferde kulcsú értékekhez.

Alapértelmezés szerint a Combiner mód megtelt, ami azt jelenti, hogy a bal oldali sor beállítása és a jobb oldali sor beállítása nem választható el egymástól. A mód beállítása a bal/jobb/belső beállítás lehetővé teszi a sor szintű csatlakozást. A rendszer elválasztja a megfelelő sorokat, és elosztja azokat több, párhuzamosan futó csúcsponton. A Combiner mód konfigurálása előtt azonban ügyeljen arra, hogy a megfelelő sorok külön legyenek elkülönítve.

Az alábbi példa egy elkülönített bal oldali sort mutat be. Az egyes kimeneti sorok a bal oldali bemeneti sorból függenek, és az is előfordulhat, hogy a jobb oldaliból származó összes sorból ugyanazzal a kulcs értékkel van ellátva. Ha a bal oldali összevonási módot állítja be, a rendszeren a hatalmas bal oldali sorok kisebbre vannak állítva, és több csúcsponthoz rendeli őket.

![Kombináló mód illusztrációja](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/combiner-mode-illustration.png)

>[!NOTE]
>Ha helytelen kombinált módot állít be, a kombináció kevésbé hatékony, és lehet, hogy az eredmények helytelenek.

A Combiner mód attribútumai:

- SqlUserDefinedCombiner (Mode = CombinerMode. Full): minden kimeneti sor a bal és a jobb oldali bemeneti soroktól függ, ugyanazzal a kulccsal.

- SqlUserDefinedCombiner (Mode = CombinerMode. Left): minden kimeneti sor a bal oldali egy bemeneti sorból (és a jobb oldali, azonos kulccsal rendelkező sorokból) függ.

- qlUserDefinedCombiner (Mode = CombinerMode. right): minden kimeneti sor a jobb oldaliból egy bemeneti sorból, a bal oldalitól pedig az azonos kulccsal rendelkező összes sorból függ.

- SqlUserDefinedCombiner (Mode = CombinerMode. Inner): minden kimeneti sor egy, a bal oldali és a jobb oldali bemeneti sorból függ ugyanazzal az értékkel.

Kód példa:

```usql
[SqlUserDefinedCombiner(Mode = CombinerMode.Right)]
public class WatsonDedupCombiner : ICombiner
{
    public override IEnumerable<IRow>
        Combine(IRowset left, IRowset right, IUpdatableRow output)
    {
    //Your combiner code goes here.
    }
}
```
