---
title: Gyakori lekérdezési minták a Azure Stream Analytics
description: Ez a cikk számos gyakori lekérdezési mintát és kialakítást ismertet, amelyek hasznosak Azure Stream Analytics feladatokban.
services: stream-analytics
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/18/2019
ms.custom: devx-track-js
ms.openlocfilehash: 8fcda77858b3feb78a04971a7ad1f20c0ea51fa1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019992"
---
# <a name="common-query-patterns-in-azure-stream-analytics"></a>Gyakori lekérdezési minták a Azure Stream Analytics

Az Azure Stream Analyticsban lévő lekérdezések SQL-szerű lekérdezési nyelven vannak kifejezve. A nyelvi összeállítások leírását a [stream Analytics lekérdezési nyelvi](/stream-analytics-query/stream-analytics-query-language-reference) útmutatója tartalmazza. 

A lekérdezési terv egyszerű áteresztő logikát biztosít az események egyik bemeneti streamből egy kimeneti adattárba való áthelyezéséhez, vagy a különböző időkeretek szerinti elemzések elvégzéséhez az összesített adatok kiszámításához, mint a [IoT megoldás létrehozása stream Analytics útmutató segítségével](stream-analytics-build-an-iot-solution-using-stream-analytics.md) . Több bemenetből is csatlakoztathat adatokat a folyamatos átviteli események egyesítéséhez, és a statikus referenciák használatával végezheti el a keresést. Több kimenetre is írhat adatokat.

Ez a cikk a különböző gyakori lekérdezési mintákkal kapcsolatos megoldásokat ismerteti valós forgatókönyvek alapján.

## <a name="supported-data-formats"></a>Támogatott adatformátumok

A Azure Stream Analytics támogatja az események CSV-, JSON-és Avro-adatformátumokban való feldolgozását.

A JSON és a Avro olyan összetett típusokat is tartalmazhat, mint például a beágyazott objektumok (rekordok) vagy a tömbök. Ezen összetett adattípusokkal kapcsolatos további információkért tekintse meg a [JSON-és AVRO-adatok elemzése](stream-analytics-parsing-json.md) című cikket.

## <a name="send-data-to-multiple-outputs"></a>Adatokat küldhet több kimenetre

Több **Select** utasítás is használható az adatkimenet különböző kimeneti mosdóba való exportálásához. Egy **Select (kiválasztható** ) érték például egy küszöbérték-alapú riasztás kimenete lehet, míg egy másik az eseményeket blob Storage-tárolóba exportálhatja.

**Bemenet**:

| Gyártó | Idő |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make1 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:03.0000000 Z |

**Kimeneti ArchiveOutput**:

| Gyártó | Idő |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make1 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:03.0000000 Z |

**Kimeneti AlertOutput**:

| Gyártó | Idő | Darabszám |
| --- | --- | --- |
| Make2 |2015-01-01T00:00:10.0000000 Z |3 |

**Lekérdezés**:

```SQL
SELECT
    *
INTO
    ArchiveOutput
FROM
    Input TIMESTAMP BY Time

SELECT
    Make,
    System.TimeStamp() AS Time,
    COUNT(*) AS [Count]
INTO
    AlertOutput
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
HAVING
    [Count] >= 3
```

A **into** záradékban megtudhatja, hogy a kimenetek közül melyeknek kell írnia az adatokat a stream Analytics. Az első **Select** olyan átmenő lekérdezést határoz meg, amely adatokat fogad a bemenettől, és elküldi azt a **ArchiveOutput** nevű kimenetnek. A második lekérdezés egyszerű összesítést és szűrést végez, mielőtt elküldi az eredményeket egy **AlertOutput** nevű alsóbb szintű riasztási rendszer-kimenetre.

Vegye figyelembe, hogy a **with** záradék használatával több allekérdezési blokk is definiálható. Ezzel a beállítással kevesebb olvasót nyithat meg a bemeneti forráshoz.

**Lekérdezés**:

```SQL
WITH ReaderQuery AS (
    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
)

SELECT * INTO ArchiveOutput FROM ReaderQuery

SELECT 
    Make,
    System.TimeStamp() AS Time,
    COUNT(*) AS [Count] 
INTO AlertOutput 
FROM ReaderQuery
GROUP BY
    Make,
    TumblingWindow(second, 10)
HAVING [Count] >= 3
```

További információért lásd a [ **with** záradékot](/stream-analytics-query/with-azure-stream-analytics).

## <a name="simple-pass-through-query"></a>Egyszerű átmenő lekérdezés

Egy egyszerű átmenő lekérdezéssel másolhatók a bemeneti adatfolyam-adatok a kimenetbe. Ha például egy valós idejű jármű adatait tartalmazó adatfolyamot kell menteni egy SQL-adatbázisban a levél elemzése céljából, egy egyszerű, áteresztő lekérdezés fogja elvégezni a feladatot.

**Bemenet**:

| Gyártó | Idő | Tömeg |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000 Z |"2000" |

**Kimenet**:

| Gyártó | Idő | Tömeg |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000 Z |"2000" |

**Lekérdezés**:

```SQL
SELECT
    *
INTO Output
FROM Input
```

A Select * lekérdezés a bejövő események összes mezőjét **kijelöli** , és elküldi őket a kimenetnek. Ugyanezt a módszert **választva** a csak a bemenetből kitöltendő mezőket is használhatja. Ebben a példában, ha a *járműnek* és az *időpontnak* csak a szükséges mezőket kell megadnia, ezek a mezők a **Select** utasításban adhatók meg.

**Bemenet**:

| Gyártó | Idő | Tömeg |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |1000 |
| Make1 |2015-01-01T00:00:02.0000000 Z |2000 |
| Make2 |2015-01-01T00:00:04.0000000 Z |1500 |

**Kimenet**:

| Gyártó | Idő |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make1 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:04.0000000 Z |

**Lekérdezés**:

```SQL
SELECT
    Make, Time
INTO Output
FROM Input
```

## <a name="string-matching-with-like-and-not-like"></a>Karakterlánc-egyeztetés hasonló és nem hasonló

**Hasonlóan** és **nem** , mint az, hogy egy adott mező megfelel-e egy adott mintának. Létrehozhat például egy szűrőt úgy, hogy csak az "A" betűvel kezdődő és a 9. számú licencet tartalmazó lemezeket küldje vissza.

**Bemenet**:

| Gyártó | License_plate | Idő |
| --- | --- | --- |
| Make1 |ABC-123 |2015-01-01T00:00:01.0000000 Z |
| Make2 |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Make3 |ABC-369 |2015-01-01T00:00:03.0000000 Z |

**Kimenet**:

| Gyártó | License_plate | Idő |
| --- | --- | --- |
| Make2 |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Make3 |ABC-369 |2015-01-01T00:00:03.0000000 Z |

**Lekérdezés**:

```SQL
SELECT
    *
FROM
    Input TIMESTAMP BY Time
WHERE
    License_plate LIKE 'A%9'
```

A **Like** utasítás használatával ellenőrizhető a **License_plate** mező értéke. Az alkalmazásnak az "A" betűvel kell kezdődnie, majd a 9-es számú karakterláncot kell használnia.

## <a name="calculation-over-past-events"></a>Korábbi események kiszámítása

A **lag** függvény használatával megtekintheti a múltbeli eseményeket egy adott időszakon belül, és összehasonlíthatja őket az aktuális eseménnyel. Például az aktuális autó kimenete kiosztható, ha az nem egyezik meg az utolsó autótól, amely az autópályadíjon keresztül járt.

**Bemenet**:

| Gyártó | Idő |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |

**Kimenet**:

| Gyártó | Idő |
| --- | --- |
| Make2 |2015-01-01T00:00:02.0000000 Z |

**Lekérdezés**:

```SQL
SELECT
    Make,
    Time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make
```

A **lag** használatával betekintést nyerhet a bemeneti streambe egy eseményre, lekérheti a *make* értéket, és összehasonlíthatja az aktuális esemény *make* értékével és az esemény kimenetével.

További információkért tekintse meg a [**késést**](/stream-analytics-query/lag-azure-stream-analytics).

## <a name="return-the-last-event-in-a-window"></a>Az utolsó esemény visszaadása egy ablakban

Mivel a rendszer valós időben használja az eseményeket, nincs olyan függvény, amely meghatározza, hogy az adott időpontra az utolsó egy esemény lesz-e megérkezni. Ennek eléréséhez a bemeneti adatfolyamot egy másikhoz kell csatlakoztatni, ahol az esemény ideje az adott ablakban lévő összes esemény maximális ideje.

**Bemenet**:

| License_plate | Gyártó | Idő |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000 Z |
| YZK 5704 |Make3 |2015-07-27T00:02:17.0000000 Z |
| RMV 8282 |Make1 |2015-07-27T00:05:01.0000000 Z |
| YHN 6970 |Make2 |2015-07-27T00:06:00.0000000 Z |
| VFE 1616 |Make2 |2015-07-27T00:09:31.0000000 Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000 Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000 Z |

**Kimenet**:

| License_plate | Gyártó | Idő |
| --- | --- | --- |
| VFE 1616 |Make2 |2015-07-27T00:09:31.0000000 Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000 Z |

**Lekérdezés**:

```SQL
WITH LastInWindow AS
(
    SELECT 
        MAX(Time) AS LastEventTime
    FROM 
        Input TIMESTAMP BY Time
    GROUP BY 
        TumblingWindow(minute, 10)
)

SELECT 
    Input.License_plate,
    Input.Make,
    Input.Time
FROM
    Input TIMESTAMP BY Time 
    INNER JOIN LastInWindow
    ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
    AND Input.Time = LastInWindow.LastEventTime
```

A lekérdezés első lépése 10 percenként megkeresi a maximális időbélyeget, amely az adott ablak utolsó eseményének időbélyegzője. A második lépés összekapcsolja az első lekérdezés eredményét az eredeti streamtel, hogy megkeresse az összes ablakban az utolsó időbélyegnek megfelelő eseményt. 

A **DATEDIFF** egy dátum-specifikus függvény, amely összehasonlítja és visszaadja a két datetime mező közötti időeltérést. További információ: [Date functions](/stream-analytics-query/date-and-time-functions-azure-stream-analytics).

A streamek összekapcsolásával kapcsolatos további információkért lásd: [**Csatlakozás**](/stream-analytics-query/join-azure-stream-analytics).

## <a name="data-aggregation-over-time"></a>Adatösszesítés az idő függvényében

Az adatok egy adott időtartományon keresztüli kiszámításához összesítheti az adatokat. Ebben a példában a rendszer minden egyes autó esetében az elmúlt 10 másodpercben számítja ki a darabszámot.

**Bemenet**:

| Gyártó | Idő | Tömeg |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |1000 |
| Make1 |2015-01-01T00:00:02.0000000 Z |2000 |
| Make2 |2015-01-01T00:00:04.0000000 Z |1500 |

**Kimenet**:

| Gyártó | Darabszám |
| --- | --- |
| Make1 | 2 |
| Make2 | 1 |

**Lekérdezés**:

```SQL
SELECT
    Make,
    COUNT(*) AS Count
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
```

Ez az Összesítés csoportosítja az autókat úgy, *hogy* 10 másodpercenként megszámolja őket. A kimenetben az autópályadíjon áthaladó autók *száma és darabszáma* is *elérhető* .

A TumblingWindow egy ablakkezelő-függvény, amely az események csoportosítására szolgál. Összesítést alkalmazhat az összes csoportosított eseményre. További információ: [ablakkezelő functions](stream-analytics-window-functions.md).

Az összesítéssel kapcsolatos további információkért tekintse meg az [összesítő függvények](/stream-analytics-query/aggregate-functions-azure-stream-analytics)című témakört.

## <a name="periodically-output-values"></a>Rendszeres kimeneti értékek

Szabálytalan vagy hiányzó események esetén rendszeres időközi kimenet hozható létre a ritka adatbevitelből. Például állítson elő 5 másodpercenként egy eseményt, amely a legutóbb látott adatpontot jelenti.

**Bemenet**:

| Idő | Érték |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**Kimenet (első 10 sor)**:

| Window_end | Last_event. Idő | Last_event. Érték |
| --- | --- | --- |
| 2014-01-01T14:01:00.000 Z |2014-01-01T14:01:00.000 Z |1 |
| 2014-01-01T14:01:05.000 Z |2014-01-01T14:01:05.000 Z |2 |
| 2014-01-01T14:01:10.000 Z |2014-01-01T14:01:10.000 Z |3 |
| 2014-01-01T14:01:15.000 Z |2014-01-01T14:01:15.000 Z |4 |
| 2014-01-01T14:01:20.000 Z |2014-01-01T14:01:15.000 Z |4 |
| 2014-01-01T14:01:25.000 Z |2014-01-01T14:01:15.000 Z |4 |
| 2014-01-01T14:01:30.000 Z |2014-01-01T14:01:30.000 Z |5 |
| 2014-01-01T14:01:35.000 Z |2014-01-01T14:01:35.000 Z |6 |
| 2014-01-01T14:01:40.000 Z |2014-01-01T14:01:35.000 Z |6 |
| 2014-01-01T14:01:45.000 Z |2014-01-01T14:01:35.000 Z |6 |

**Lekérdezés**:

```SQL
SELECT
    System.Timestamp() AS Window_end,
    TopOne() OVER (ORDER BY Time DESC) AS Last_event
FROM
    Input TIMESTAMP BY Time
GROUP BY
    HOPPINGWINDOW(second, 300, 5)
```

A lekérdezés 5 másodpercenként hoz létre eseményeket, és a korábban kapott utolsó eseményt adja eredményül. A **HOPPINGWINDOW** időtartama határozza meg, hogy a lekérdezés milyen mértékben keresi meg a legújabb eseményt.

További információkért tekintse meg a [hopping ablakát](/stream-analytics-query/hopping-window-azure-stream-analytics).

## <a name="correlate-events-in-a-stream"></a>Események korrelációja egy adatfolyamban

Az egyazon adatfolyamban található események korrelációját a **lag** függvény használatával végezheti el a múltbeli események megtekintésével. Például létrehozhat egy kimenetet minden alkalommal, amikor két egymást követő *autót ugyanabból* a továbbításból az utolsó 90 másodpercig.

**Bemenet**:

| Gyártó | License_plate | Idő |
| --- | --- | --- |
| Make1 |ABC-123 |2015-01-01T00:00:01.0000000 Z |
| Make1 |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Make2 |DEF-987 |2015-01-01T00:00:03.0000000 Z |
| Make1 |GHI-345 |2015-01-01T00:00:04.0000000 Z |

**Kimenet**:

| Gyártó | Idő | Current_car_license_plate | First_car_license_plate | First_car_time |
| --- | --- | --- | --- | --- |
| Make1 |2015-01-01T00:00:02.0000000 Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000 Z |

**Lekérdezés**:

```SQL
SELECT
    Make,
    Time,
    License_plate AS Current_car_license_plate,
    LAG(License_plate, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_license_plate,
    LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make
```

A **lag** függvény áttekintheti a bemeneti adatfolyamot egy eseményre, és lekérdezheti a *make* értéket, és összehasonlíthatja *az aktuális esemény értékével* .  A feltétel teljesülése után az előző eseményből származó adatok a **Select** utasításban a **lag** használatával is kiállíthatók.

További információkért tekintse meg a [késést](/stream-analytics-query/lag-azure-stream-analytics).

## <a name="detect-the-duration-between-events"></a>Az események közötti időtartam észlelése

Az események időtartamát úgy számíthatja ki, hogy megtekinti az utolsó indítási eseményt a befejezési esemény kézhezvétele után. Ez a lekérdezés hasznos lehet annak megállapításához, hogy a felhasználó mennyi időt tölt egy adott oldalon vagy szolgáltatásban.

**Bemenet**:  

| User | Szolgáltatás | Esemény | Idő |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Indítás |2015-01-01T00:00:01.0000000 Z |
| user@location.com |RightMenu |Befejezés |2015-01-01T00:00:08.0000000 Z |

**Kimenet**:  

| User | Szolgáltatás | Időtartam |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**Lekérdezés**:

```SQL
SELECT
    [user],
    feature,
    DATEDIFF(
        second,
        LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'),
        Time) as duration
FROM input TIMESTAMP BY Time
WHERE
    Event = 'end'
```

Az **utolsó** függvény használatával lehet lekérni az utolsó eseményt egy adott feltételen belül. Ebben a példában a feltétel egy Start típusú esemény, amely a keresést **a felhasználó és a szolgáltatás szerint** particionálja. Így minden felhasználó és szolgáltatás egymástól függetlenül kezelhető a Start esemény keresésekor. A **korlát időtartama** a befejezési és a kezdési események között 1 órára korlátozza a keresést.

## <a name="count-unique-values"></a>Egyedi értékek megszámlálása

A **darabszám** és a **DISTINCT** érték az adatfolyamban megjelenő egyedi mezőértékek számának megszámlálására használható az adott időszakon belül. A rendszer létrehoz egy lekérdezést, amely kiszámítja *, hogy* a két másodperces ablakban hány egyedi módon halad át az autópályadíj-kezelő.

**Bemenet**:

| Gyártó | Idő |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make1 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:03.0000000 Z |

**Kimeneti**

| Count_make | Idő |
| --- | --- |
| 2 |2015-01-01T00:00:02.000 Z |
| 1 |2015-01-01T00:00:04.000 Z |

**Lekérdezés:**

```SQL
SELECT
     COUNT(DISTINCT Make) AS Count_make,
     System.TIMESTAMP() AS Time
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```

A **Count (DISTINCT do)** függvény a **make** oszlopban szereplő különböző értékek számát adja vissza egy időablakon belül.
További információt a [ **Count** összesítő függvényben](/stream-analytics-query/count-azure-stream-analytics)találhat.

## <a name="retrieve-the-first-event-in-a-window"></a>Az első esemény beolvasása egy ablakban

A **IsFirst** az első esemény egy időablakban való lekérésére használható. Tegyük fel például, hogy az első autó adatait 10 percenként kell kiterjeszteni.

**Bemenet**:

| License_plate | Gyártó | Idő |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000 Z |
| YZK 5704 |Make3 |2015-07-27T00:02:17.0000000 Z |
| RMV 8282 |Make1 |2015-07-27T00:05:01.0000000 Z |
| YHN 6970 |Make2 |2015-07-27T00:06:00.0000000 Z |
| VFE 1616 |Make2 |2015-07-27T00:09:31.0000000 Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000 Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000 Z |

**Kimenet**:

| License_plate | Gyártó | Idő |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000 Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000 Z |

**Lekérdezés**:

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) = 1
```

A **IsFirst** az adatparticionálást is elvégezheti, és az első eseményt kiszámíthatja minden *egyes autóra* 10 percenként.

**Kimenet**:

| License_plate | Gyártó | Idő |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000 Z |
| YZK 5704 |Make3 |2015-07-27T00:02:17.0000000 Z |
| YHN 6970 |Make2 |2015-07-27T00:06:00.0000000 Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000 Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000 Z |

**Lekérdezés**:

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) OVER (PARTITION BY Make) = 1
```

További információkért tekintse meg a következőt: [**IsFirst**](/stream-analytics-query/isfirst-azure-stream-analytics).

## <a name="remove-duplicate-events-in-a-window"></a>Ismétlődő események eltávolítása egy ablakban

Ha olyan műveletet hajt végre, mint például az átlagok kiszámítása egy adott időintervallumban, az ismétlődő eseményeket szűrni kell. A következő példában a második esemény az első másolat.

**Bemenet**:  

| DeviceId | Idő | Attribútum | Érték |
| --- | --- | --- | --- |
| 1 |2018-07-27T00:00:01.0000000 Z |Hőmérséklet |50 |
| 1 |2018-07-27T00:00:01.0000000 Z |Hőmérséklet |50 |
| 2 |2018-07-27T00:00:01.0000000 Z |Hőmérséklet |40 |
| 1 |2018-07-27T00:00:05.0000000 Z |Hőmérséklet |60 |
| 2 |2018-07-27T00:00:05.0000000 Z |Hőmérséklet |50 |
| 1 |2018-07-27T00:00:10.0000000 Z |Hőmérséklet |100 |

**Kimenet**:  

| AverageValue | DeviceId |
| --- | --- |
| 70 | 1 |
|45 | 2 |

**Lekérdezés**:

```SQL
With Temp AS (
SELECT
    COUNT(DISTINCT Time) AS CountTime,
    Value,
    DeviceId
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Value,
    DeviceId,
    SYSTEM.TIMESTAMP()
)

SELECT
    AVG(Value) AS AverageValue, DeviceId
INTO Output
FROM Temp
GROUP BY DeviceId,TumblingWindow(minute, 5)
```

**Darabszám (eltérő idő)** : az időoszlopban lévő különböző értékek számát adja vissza az időtartományon belül. Az első lépés kimenete ezután felhasználható az eszközök átlagának kiszámítására az ismétlődések elvetésével.

További információkért lásd: [darabszám (eltérő idő)](/stream-analytics-query/count-azure-stream-analytics).

## <a name="specify-logic-for-different-casesvalues-case-statements"></a>Logika megadása különböző esetekhez/értékekhez (CASE utasítások)

A **Case** utasítások különböző számításokat biztosíthatnak különböző mezőkhöz az adott feltétel alapján. Például rendelje hozzá az "A" sávot a *Make1* és a "B" Lane-hez bármely más gyártmányhoz.

**Bemenet**:

| Gyártó | Idő |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:03.0000000 Z |

**Kimenet**:

| Gyártó |Dispatch_to_lane | Idő |
| --- | --- | --- |
| Make1 |Egy |2015-01-01T00:00:01.0000000 Z |
| Make2 |B |2015-01-01T00:00:02.0000000 Z |

**Megoldás**:

```SQL
SELECT
    Make
    CASE
        WHEN Make = "Make1" THEN "A"
        ELSE "B"
    END AS Dispatch_to_lane,
    System.TimeStamp() AS Time
FROM
    Input TIMESTAMP BY Time
```

A **Case** kifejezés összehasonlítja egy kifejezést egy egyszerű kifejezésekkel, hogy meghatározza annak eredményét. Ebben a példában a *Make1* lévő járműveket az "A" sávban küldi el a rendszer, míg más gyártmányú járműveket a "B" sáv jelöl.

További információkért tekintse meg a [Case kifejezést](/stream-analytics-query/case-azure-stream-analytics).

## <a name="data-conversion"></a>Adatátalakítás

Az adatátviteli **módszer használatával** valós időben lehet átadni az adatfeldolgozást. Például a **nvarchar (max)** típusról a **bigint** típusra konvertálható, és numerikus számításokban használható.

**Bemenet**:

| Gyártó | Idő | Tömeg |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000 Z |"2000" |

**Kimenet**:

| Gyártó | Tömeg |
| --- | --- |
| Make1 |3000 |

**Lekérdezés**:

```SQL
SELECT
    Make,
    SUM(CAST(Weight AS BIGINT)) AS Weight
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
```

Az adattípusának megadásához használjon **Cast** utasítást. Tekintse meg a támogatott adattípusok listáját az [adattípusokon (Azure stream Analytics)](/stream-analytics-query/data-types-azure-stream-analytics).

További információ az [Adatátalakítási függvényekről](/stream-analytics-query/conversion-functions-azure-stream-analytics).

## <a name="detect-the-duration-of-a-condition"></a>Feltétel időtartamának észlelése

A több eseményre kiterjedő feltételek esetén a **lag** függvény használatával azonosítható a feltétel időtartama. Tegyük fel például, hogy egy hiba az összes olyan autót eredményezett, amely nem megfelelő súlyozású (20 000 kilós), a hiba időtartamát pedig számításba kell venni.

**Bemenet**:

| Gyártó | Idő | Tömeg |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |2000 |
| Make2 |2015-01-01T00:00:02.0000000 Z |25000 |
| Make1 |2015-01-01T00:00:03.0000000 Z |26000 |
| Make2 |2015-01-01T00:00:04.0000000 Z |25000 |
| Make1 |2015-01-01T00:00:05.0000000 Z |26000 |
| Make2 |2015-01-01T00:00:06.0000000 Z |25000 |
| Make1 |2015-01-01T00:00:07.0000000 Z |26000 |
| Make2 |2015-01-01T00:00:08.0000000 Z |2000 |

**Kimenet**:

| Start_fault | End_fault |
| --- | --- |
| 2015-01-01T00:00:02.000 Z |2015-01-01T00:00:07.000 Z |

**Lekérdezés**:

```SQL
WITH SelectPreviousEvent AS
(
SELECT
    *,
    LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previous_time,
    LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previous_weight
FROM input TIMESTAMP BY [time]
)

SELECT 
    LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previous_weight < 20000 ) [Start_fault],
    previous_time [End_fault]
FROM SelectPreviousEvent
WHERE
    [weight] < 20000
    AND previous_weight > 20000
```
Az első SELECT utasítás az előző méréssel összekapcsolja az aktuális súlyozási mérést, és az aktuális méréssel együtt **kijelöli** . A második **lehetőség** arra az utolsó eseményre mutat vissza, ahol a *previous_weight* kisebb, mint 20000, ahol az aktuális súlyozás kisebb, mint 20000, és az aktuális esemény *previous_weight* nagyobb volt, mint 20000.

A End_fault a jelenlegi nem hibás esemény, amelyben az előző esemény hibás volt, és a Start_fault az utolsó nem hibás esemény.

## <a name="process-events-with-independent-time-substreams"></a>Események feldolgozása független időponttal (alstreamek)

Az események az esemény-előállítók közötti óra döntése, illetve a partíciók közötti időeltérések vagy a hálózati késések közötti időeltérések miatt megérkeznek.
Például a 2. *TollID* eszköz órája öt másodperccel az 1. *TollID* mögött, a *TollID* 3 eszköz órája pedig tíz másodperccel az 1. *TollID* mögött. A számítások egymástól függetlenül is megtörténhetnek, mivel csak a saját óra-és időbélyeg-adatvesztést kell figyelembe venni.

**Bemenet**:

| LicensePlate | Gyártó | Idő | TollID |
| --- | --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:01.0000000 Z | 1 |
| YHN 6970 |Make2 |2015-07-27T00:00:05.0000000 Z | 1 |
| QYF 9358 |Make1 |2015-07-27T00:00:01.0000000 Z | 2 |
| GXF 9462 |Make3 |2015-07-27T00:00:04.0000000 Z | 2 |
| VFE 1616 |Make2 |2015-07-27T00:00:10.0000000 Z | 1 |
| RMV 8282 |Make1 |2015-07-27T00:00:03.0000000 Z | 3 |
| MDR 6128 |Make3 |2015-07-27T00:00:11.0000000 Z | 2 |
| YZK 5704 |Make4 |2015-07-27T00:00:07.0000000 Z | 3 |

**Kimenet**:

| TollID | Darabszám |
| --- | --- |
| 1 | 2 |
| 2 | 2 |
| 1 | 1 |
| 3 | 1 |
| 2 | 1 |
| 3 | 1 |

**Lekérdezés**:

```SQL
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId
```

Az **időbélyegzővel** ellátott záradék az egyes eszközök idővonalait a alstreamek egymástól független használatával vizsgálja. Az egyes *TollID* kimeneti eseményét a rendszer a számított módon hozza létre, ami azt jelenti, hogy az események sorrendben jelennek meg az egyes *TollID* , ahelyett, hogy az összes eszköz ugyanarra az időpontra van állítva.

További információkért tekintse meg az [időbélyeget](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering).

## <a name="session-windows"></a>Munkamenet-ablakok

A munkamenet-ablak egy ablak, amely az események kibővítését és a számítások lezárását eredményezi, ha egy adott időtartam után nem érkezik esemény, vagy ha az ablak eléri a maximális időtartamot.
Ez az ablak különösen akkor hasznos, ha a felhasználói interakciós adatfeldolgozást. Egy ablak akkor indul el, amikor egy felhasználó megkezdi a rendszerrel való interakciót, és bezárja, ha nem figyelhetők meg több esemény, ami azt jelenti, hogy a felhasználó megszakította a műveleteket.
Ha például egy felhasználó egy olyan weblappal működik együtt, amelyben a kattintások száma naplózva van, egy munkamenet-ablak segítségével megtudhatja, hogy a felhasználó mennyi ideig használja a webhelyet.

**Bemenet**:

| User_id | Idő | URL-cím |
| --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000 Z | "www.example.com/a.html" |
| 0 | 2017-01-26T00:00:20.0000000 Z | "www.example.com/b.html" |
| 1 | 2017-01-26T00:00:55.0000000 Z | "www.example.com/c.html" |
| 0 | 2017-01-26T00:01:10.0000000 Z | "www.example.com/d.html" |
| 1 | 2017-01-26T00:01:15.0000000 Z | "www.example.com/e.html" |

**Kimenet**:

| User_id | StartTime | EndTime | Duration_in_seconds |
| --- | --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000 Z | 2017-01-26T00:01:10.0000000 Z | 70 |
| 1 | 2017-01-26T00:00:55.0000000 Z | 2017-01-26T00:01:15.0000000 Z | 20 |

**Lekérdezés**:

``` SQL
SELECT
    user_id,
    MIN(time) as StartTime,
    MAX(time) as EndTime,
    DATEDIFF(second, MIN(time), MAX(time)) AS duration_in_seconds
FROM input TIMESTAMP BY time
GROUP BY
    user_id,
    SessionWindow(minute, 1, 60) OVER (PARTITION BY user_id)
```

A **kiválasztott** projekt a felhasználói beavatkozáshoz kapcsolódó adatokat, valamint a beavatkozás időtartamát adja meg. Csoportosítsa az adatmennyiséget a felhasználó és a **SessionWindow** között, amely akkor zárja be, ha egy percen belül nem történik interakció, és az ablak maximális mérete 60 perc.

A **SessionWindow** kapcsolatos további információkért tekintse meg a [munkamenet ablakát](/stream-analytics-query/session-window-azure-stream-analytics) .

## <a name="language-extensibility-with-user-defined-function-in-javascript-and-c"></a>Nyelvi bővíthetőség a felhasználó által definiált Függvénysel a JavaScriptben és a C-ben #

Azure Stream Analytics a lekérdezés nyelve a JavaScript vagy C# nyelven írt egyéni függvények használatával bővíthető. A felhasználó által definiált függvények (UDF) olyan egyéni/összetett számítások, amelyek nem használhatók egyszerűen az **SQL** nyelv használatával. Ezek a UDF egyszer határozhatók meg, és többször is használhatók egy lekérdezésen belül. Az UDF például egy hexadecimális *nvarchar (max)* érték *bigint* értékre való átalakítására használható.

**Bemenet**:

| Device_id | HexValue |
| --- | --- |
| 1 | B4 |
| 2 | 11b |
| 3 | "121" |

**Kimenet**:

| Device_id | Tizedesjegy |
| --- | --- |
| 1 | 180 |
| 2 | 283 |
| 3 | 289 |

```JavaScript
function hex2Int(hexValue){
    return parseInt(hexValue, 16);
}
```

```C#
public static class MyUdfClass {
    public static long Hex2Int(string hexValue){
        return int.Parse(hexValue, System.Globalization.NumberStyles.HexNumber);
    }
}
```

```SQL
SELECT
    Device_id,
    udf.Hex2Int(HexValue) AS Decimal
From
    Input
```

A felhasználó által definiált függvény minden felhasznált eseménynél kiszámítja a *bigint* értékét a HexValue.

További információkért tekintse meg a [JavaScriptet](./stream-analytics-javascript-user-defined-functions.md) és a [C#](./stream-analytics-edge-csharp-udf.md)-ot.

## <a name="advanced-pattern-matching-with-match_recognize"></a>Speciális minta egyeztetése MATCH_RECOGNIZE

A **MATCH_RECOGNIZE** egy olyan speciális minta-megfeleltetési mechanizmus, amely az események sorozatát egy jól definiált reguláris kifejezési mintának megfelelően felhasználhatja.
Az ATM-et például valós időben figyelik a hibák, az ATM működése során, ha két egymást követő figyelmeztető üzenet jelenik meg, a rendszergazdának értesítést kell kapnia.

**Bemenet**:

| ATM_id | Operation_id | Return_Code | Idő |
| --- | --- | --- | --- |
| 1 | "PIN-kód beírása" | Sikeres | 2017-01-26T00:10:00.0000000 Z |
| 2 | "A pénz megnyitása tárolóhely" | Sikeres | 2017-01-26T00:10:07.0000000 Z |
| 2 | "Záró pénz tárolóhelye" | Sikeres | 2017-01-26T00:10:11.0000000 Z |
| 1 | "Visszavont mennyiség beírása" | Sikeres | 2017-01-26T00:10:08.0000000 Z |
| 1 | "A pénz megnyitása tárolóhely" | Figyelmeztetés | 2017-01-26T00:10:14.0000000 Z |
| 1 | "Banki egyenleg nyomtatása" | Figyelmeztetés | 2017-01-26T00:10:19.0000000 Z |

**Kimenet**:

| ATM_id | First_Warning_Operation_id | Warning_Time |
| --- | --- | --- |
| 1 | "A pénz megnyitása tárolóhely" | 2017-01-26T00:10:14.0000000 Z |

```SQL
SELECT *
FROM input TIMESTAMP BY time OVER ATM_id
MATCH_RECOGNIZE (
    LIMIT DURATION(minute, 1)
    PARTITON BY ATM_id
    MEASURES
        First(Warning.ATM_id) AS ATM_id,
        First(Warning.Operation_Id) AS First_Warning_Operation_id,
        First(Warning.Time) AS Warning_Time
    AFTER MATCH SKIP TO NEXT ROW
    PATTERN (Success* Warning{2,})
    DEFINE
        Success AS Succes.Return_Code = 'Success',
        Failure AS Warning.Return_Code <> 'Success'
) AS patternMatch
```

Ez a lekérdezés legalább két egymást követő sikertelen eseményt egyeztet, és riasztást állít elő, ha teljesülnek a feltételek.
A **minta** határozza meg a megfeleltetéshez használandó reguláris kifejezést, ebben az esetben a sikeres műveletek számát, majd legalább két egymást követő hibát.
A sikeres és sikertelen művelet Return_Code értékkel van definiálva, és a feltétel teljesülése után a **mértékek** a *ATM_id*, az első figyelmeztetési művelettel és az első figyelmeztetési idővel vannak kiértékelve.

További információkért tekintse meg a [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics).

## <a name="geofencing-and-geospatial-queries"></a>Geokerítések és térinformatikai lekérdezések

A Azure Stream Analytics beépített térinformatikai funkciókat biztosít, amelyek olyan forgatókönyvek megvalósítására használhatók, mint például a flotta-felügyelet, a Ride Sharing, a csatlakoztatott autók és az eszközök nyomon követése.
A térinformatikai adatmennyiség GeoJSON vagy WKT formátumban is betölthető az esemény-adatfolyam vagy a hivatkozási adatforrások részeként.
Például egy vállalat, amely a gépeket a Passportok nyomtatására, a gépeket a kormányoknak és a konzulátusoknak való bérletére specializálódott. A gépek helyét szigorúan úgy ellenőrzik, hogy elkerülje a Passportok hamisításának és lehetséges felhasználásának elkerülését. Az egyes gépek GPS-nyomkövetővel vannak ellátva, az információk továbbítása Azure Stream Analytics feladathoz történik.
A gyártás szeretné nyomon követni ezeknek a gépeknek a helyét, és riasztást küld, ha valamelyikük jogosult területtel rendelkezik, így távolról letilthatják a riasztási hatóságokat, és lekérhetik a berendezést.

**Bemenet**:

| Equipment_id | Equipment_current_location | Idő |
| --- | --- | --- |
| 1 | "PONT (-122.13288797982818 47.64082002051315)" | 2017-01-26T00:10:00.0000000 Z |
| 1 | "PONT (-122.13307252987875 47.64081350934929)" | 2017-01-26T00:11:00.0000000 Z |
| 1 | "PONT (-122.13308862313283 47.6406508603241)" | 2017-01-26T00:12:00.0000000 Z |
| 1 | "PONT (-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000 Z |

**Hivatkozási adatok bevitele**:

| Equipment_id | Equipment_lease_location |
| --- | --- |
| 1 | "SOKSZÖG ((-122.13326028450979 47.6409833866794,-122.13261655434621 47.6409833866794,-122.13261655434621 47.64061471602751,-122.13326028450979 47.64061471602751,-122.13326028450979 47.6409833866794))" |

**Kimenet**:

| Equipment_id | Equipment_alert_location | Idő |
| --- | --- | --- |
| 1 | "PONT (-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000 Z |

```SQL
SELECT
    input.Equipment_id AS Equipment_id,
    input.Equipment_current_location AS Equipment_current_location,
    input.Time AS Time
FROM input TIMESTAMP BY time
JOIN
    referenceInput 
    ON input.Equipment_id = referenceInput.Equipment_id
    WHERE 
        ST_WITHIN(input.Equipment_currenct_location, referenceInput.Equipment_lease_location) = 1
```

A lekérdezés lehetővé teszi, hogy a gyártó automatikusan figyelje a gépek helyét, és riasztást kapjon, ha egy gép elhagyja az engedélyezett geokerítésen. A beépített térinformatikai funkció lehetővé teszi a felhasználók számára, hogy a lekérdezésen belüli GPS-adatszolgáltatásokat külső könyvtárak nélkül használják.

További információkért tekintse meg a [geokerítések és térinformatikai összesítési forgatókönyveket Azure stream Analytics](geospatial-scenarios.md) cikkben.

## <a name="get-help"></a>Segítség kérése

További segítségért próbálja ki a [Microsoft Q&a Azure stream Analytics kérdéseit](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Következő lépések
* [Bevezetés a Azure Stream Analyticsba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](/rest/api/streamanalytics/)