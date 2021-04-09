---
title: Felhasználó által definiált JavaScript-összesítések Azure Stream Analytics
description: Ez a cikk azt ismerteti, hogyan hajtható végre speciális lekérdezési mechanika JavaScript felhasználó által definiált összesítésekkel Azure Stream Analyticsban.
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2017
ms.custom: devx-track-js
ms.openlocfilehash: e9695a268d1c6ec43a737afd36536e4ec2a41a41
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98012478"
---
# <a name="azure-stream-analytics-javascript-user-defined-aggregates"></a>JavaScript felhasználó által definiált összesítések Azure Stream Analytics
 
Azure Stream Analytics támogatja a JavaScriptben írt, felhasználó által definiált összesítéseket (UDA), így összetett állapot-nyilvántartó üzleti logikát valósíthat meg. A UDA-en belül teljes mértékben szabályozhatja az állapot adatstruktúráját, az állapot felhalmozódását, az állapot-összesítést és az összesített eredmények számítását. A cikk bemutatja a két különböző JavaScript UDA felületet, a UDA létrehozásának lépéseit, valamint azt, hogy miként használhatók a UDA az ablakos műveletekkel Stream Analytics lekérdezésben.

## <a name="javascript-user-defined-aggregates"></a>Felhasználó által definiált JavaScript-összesítések

A felhasználó által definiált összesítést a rendszer egy időablak-specifikáción felül használja az adott ablakban lévő események összesítésére, és egyetlen eredmény értékét eredményezi. A UDA két típusa van, amelyek Stream Analytics támogatják a ma, a AccumulateOnly és a AccumulateDeaccumulate szolgáltatást. Mindkét típusú UDA felhasználható a bukdácsoló, a hopping, a csúszó és a munkamenet ablakban is. A AccumulateDeaccumulate UDA jobb, mint a AccumulateOnly-UDA, ha a használata a hopping, a csúszó és a munkamenet ablakával együtt történik. A két típus egyikét választhatja a használt algoritmus alapján.

### <a name="accumulateonly-aggregates"></a>AccumulateOnly-összesítések

A AccumulateOnly aggregátumok csak az új eseményeket tudják felhalmozni az állapotukba, az algoritmus nem engedélyezi az értékek felhalmozódását. Válassza ezt az összesítési típust, ha az állapot értékének kibontása nem lehetséges. A AccumulatOnly-összesítések JavaScript-sablonja a következő:

```JavaScript
// Sample UDA which state can only be accumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.computeResult = function () {
        return this.state;
    }
}
```

### <a name="accumulatedeaccumulate-aggregates"></a>AccumulateDeaccumulate-összesítések

A AccumulateDeaccumulate összesítések lehetővé teszik egy korábbi halmozott érték kivonását az állapotból, például eltávolít egy kulcs-érték párokat az események listájából, vagy kivonja az értéket a Sum aggregált állapotból. A AccumulateDeaccumulate-összesítések JavaScript-sablonja a következő:

```JavaScript
// Sample UDA which state can be accumulated and deaccumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.deaccumulate = function (value, timestamp) {
        this.state -= value;
    }

    this.deaccumulateState = function (otherState){
        this.state -= otherState.state;
    }

    this.computeResult = function () {
        return this.state;
    }
}
```

## <a name="uda---javascript-function-declaration"></a>UDA – JavaScript-függvény deklarációja

Minden JavaScript-UDA egy Function Object deklaráció definiál. A UDA definíciójának fő elemei a következők.

### <a name="function-alias"></a>Függvény aliasa

A függvény aliasa a UDA azonosítója. Stream Analytics lekérdezésben való híváskor mindig használjon UDA aliast a "uda" értékkel együtt. előtagot.

### <a name="function-type"></a>Függvény típusa

A UDA a Function típusának JavaScript- **uda** kell lennie.

### <a name="output-type"></a>Kimenet típusa

Egy adott típus, amely Stream Analytics a feladatokhoz, vagy "bármelyik", ha a lekérdezésben szeretné kezelni a típust.

### <a name="function-name"></a>Függvény neve

A függvény objektumának neve. A függvény nevének meg kell egyeznie a UDA aliasával.

### <a name="method---init"></a>Metódus – init ()

Az init () metódus inicializálja az Összesítés állapotát. Ezt a metódust az ablak indításakor hívja meg a rendszer.

### <a name="method--accumulate"></a>Metódus – felhalmozási ()

A felhalmozási () metódus a UDA állapotát az előző állapot és az aktuális esemény értékei alapján számítja ki. Ezt a metódust akkor kell meghívni, amikor egy esemény időablakba kerül (TUMBLINGWINDOW, HOPPINGWINDOW, SLIDINGWINDOW vagy SESSIONWINDOW).

### <a name="method--deaccumulate"></a>Metódus – felhalmozás ()

A defelhalmozási () metódus az előző állapot és az aktuális esemény értékei alapján újraszámítja az állapotot. Ezt a metódust akkor kell meghívni, amikor egy esemény elhagyja a SLIDINGWINDOW vagy a SESSIONWINDOW.

### <a name="method--deaccumulatestate"></a>Metódus – deaccumulateState ()

A deaccumulateState () metódus újraszámítja az állapotot az előző állapot és egy ugrás állapota alapján. Ezt a metódust akkor kell meghívni, ha egy adott esemény egy HOPPINGWINDOW hagy.

### <a name="method--computeresult"></a>Metódus – computeResult ()

A computeResult () metódus összesített eredményt ad vissza az aktuális állapot alapján. Ezt a metódust az időablak végén kell meghívni (TUMBLINGWINDOW, HOPPINGWINDOW, SLIDINGWINDOW vagy SESSIONWINDOW).

## <a name="javascript-uda-supported-input-and-output-data-types"></a>JavaScript UDA támogatott bemeneti és kimeneti adattípusok
A JavaScript UDA-adattípusok esetében tekintse meg a **stream Analytics és a JavaScript típusú** [integrálás a JavaScript-UDF](stream-analytics-javascript-user-defined-functions.md)való átalakítását ismertető szakaszt.

## <a name="adding-a-javascript-uda-from-the-azure-portal"></a>JavaScript-UDA hozzáadása a Azure Portal

Az alábbiakban bemutatjuk, hogyan hozhat létre UDA a portálról. Az itt használt példa a számítási idő súlyozott átlaga.

Most hozzon létre egy JavaScript-UDA egy meglévő ASA-feladatban a következő lépések végrehajtásával.

1. Jelentkezzen be Azure Portal, és keresse meg a meglévő Stream Analytics feladatot.
1. Ezután kattintson a functions hivatkozásra a **feladatok topológiája** alatt.
1. Új függvény hozzáadásához kattintson a **Hozzáadás** ikonra.
1. Az új függvény nézetben válassza a **JavaScript uda** lehetőséget a függvény típusaként, majd megjelenik egy alapértelmezett uda-sablon a szerkesztőben.
1. Töltse ki a "TWA" nevet a UDA-aliasként, és módosítsa a függvény implementációját a következőképpen:

    ```JavaScript
    // Sample UDA which calculate Time-Weighted Average of incoming values.
    function main() {
        this.init = function () {
            this.totalValue = 0.0;
            this.totalWeight = 0.0;
        }

        this.accumulate = function (value, timestamp) {
            this.totalValue += value.level * value.weight;
            this.totalWeight += value.weight;

        }

        // Uncomment below for AccumulateDeaccumulate implementation
        /*
        this.deaccumulate = function (value, timestamp) {
            this.totalValue -= value.level * value.weight;
            this.totalWeight -= value.weight;
        }

        this.deaccumulateState = function (otherState){
            this.state -= otherState.state;
            this.totalValue -= otherState.totalValue;
            this.totalWeight -= otherState.totalWeight;
        }
        */

        this.computeResult = function () {
            if(this.totalValue == 0) {
                result = 0;
            }
            else {
                result = this.totalValue/this.totalWeight;
            }
            return result;
        }
    }
    ```

1. Ha a Save (Mentés) gombra kattint, a UDA megjelenik a függvények listáján.

1. Kattintson a "TWA" nevű új függvényre, és tekintse meg a függvény definícióját.

## <a name="calling-javascript-uda-in-asa-query"></a>JavaScript UDA meghívása az ASA-lekérdezésben

Azure Portal és nyissa meg a feladatot, szerkessze a lekérdezést, és hívja meg a TWA () függvényt egy "uda." mandátum-előtaggal. Például:

```SQL
WITH value AS
(
    SELECT
    NoiseLevelDB as level,
    DurationSecond as weight
FROM
    [YourInputAlias] TIMESTAMP BY EntryTime
)
SELECT
    System.Timestamp as ts,
    uda.TWA(value) as NoseDoseTWA
FROM value
GROUP BY TumblingWindow(minute, 5)
```

## <a name="testing-query-with-uda"></a>Lekérdezés tesztelése a UDA

Hozzon létre egy helyi JSON-fájlt az alábbi tartalommal, töltse fel a fájlt Stream Analytics feladatokba, és tesztelje a fenti lekérdezést.

```JSON
[
  {"EntryTime": "2017-06-10T05:01:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 22.0},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 81, "DurationSecond": 37.8},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 85, "DurationSecond": 26.3},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 95, "DurationSecond": 13.7},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 88, "DurationSecond": 10.3},
  {"EntryTime": "2017-06-10T05:05:00-07:00", "NoiseLevelDB": 103, "DurationSecond": 5.5},
  {"EntryTime": "2017-06-10T05:06:00-07:00", "NoiseLevelDB": 99, "DurationSecond": 23.0},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 1.76},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 79, "DurationSecond": 17.9},
  {"EntryTime": "2017-06-10T05:08:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 27.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 91, "DurationSecond": 17.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 115, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 28.3},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 55, "DurationSecond": 18.2},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 25.8},
  {"EntryTime": "2017-06-10T05:11:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 11.4},
  {"EntryTime": "2017-06-10T05:12:00-07:00", "NoiseLevelDB": 89, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 112, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 9.7},
  {"EntryTime": "2017-06-10T05:18:00-07:00", "NoiseLevelDB": 96, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 0.99},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 113, "DurationSecond": 25.1},
  {"EntryTime": "2017-06-10T05:22:00-07:00", "NoiseLevelDB": 110, "DurationSecond": 5.3}
]
```

## <a name="get-help"></a>Segítség kérése

További segítségért próbálja ki a [Microsoft Q&a Azure stream Analytics kérdéseit](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Következő lépések

* [Bevezetés a Azure Stream Analyticsba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics lekérdezés nyelvi referenciája](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics felügyeleti REST API referenciája](/rest/api/streamanalytics/)