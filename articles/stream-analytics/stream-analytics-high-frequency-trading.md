---
title: Nagy gyakoriságú kereskedelem a Azure Stream Analytics használatával
description: Lineáris regressziós modellek tanítása és pontozása Azure Stream Analytics-feladatban.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/16/2021
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: f632c916c3de61b94532e96be23da511ad5863ea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104593033"
---
# <a name="high-frequency-trading-simulation-with-stream-analytics"></a>Nagy gyakoriságú kereskedelmi szimuláció a Stream Analyticsszel
Az Azure Stream Analyticsben az SQL-nyelv, illetve a felhasználó által definiált JavaScript-függvények (UDF-ek) és felhasználó által definiált összesítések (UDA-k) kombinációja lehetővé teszi, hogy a felhasználók fejlett elemzéseket végezzenek. A fejlett elemzések magukban foglalhatnak például online gépi tanulásra vonatkozó tanítási és pontozási, valamint állapotalapú folyamat-szimulációkat. Ez a cikk bemutatja, hogyan történik egy nagy gyakoriságú kereskedelmi forgatókönyvben a folyamatos tanítási és pontozási folyamatokat végrehajtó lineáris regressziós modell futtatása egy Azure Stream Analytics-feladatban.

## <a name="high-frequency-trading"></a>Nagy gyakoriságú kereskedelem
A nagy gyakoriságú kereskedelem logikai folyamata a következőképpen épül fel:
1. Valós idejű értéktőzsdei ajánlatok lekérése.
2. Prediktív modell felépítése az ajánlatok köré, hogy kiszámítható legyen az árak mozgása.
3. Vételi vagy eladási megrendelések leadása, hogy pénzt kereshessünk az ármozgás sikeres előrejelzésével. 

Ennek megfelelően a következőkre van szükségünk:
* Valós idejű ajánlatcsatorna.
* Valós idejű ajánlatokat feldolgozni képes prediktív modell.
* Kereskedelmi szimuláció, amely a kereskedelmi algoritmus nyereségeit és veszteségeit mutatja be.

### <a name="real-time-quote-feed"></a>Valós idejű ajánlatcsatorna
Az IEX ingyenes, [valós idejű ajánlattételi és lekérdezési lehetőségeket](https://iextrading.com/developer/docs/#websockets) biztosít a socket.io használatával. Egyszerű konzolprogramokat írhatunk a valós idejű ajánlatok fogadásához és az Azure Event Hubsnak adatforrásként való továbbításához. A következő kód alkotja a program vázát. A kód az áttekinthetőség kedvéért kihagyja a hibakezelést. A projektnek ezenkívül tartalmaznia kell a SocketIoClientDotNet és a WindowsAzure.ServiceBus NuGet-csomagot is.

```csharp
using Quobject.SocketIoClientDotNet.Client;
using Microsoft.ServiceBus.Messaging;
var symbols = "msft,fb,amzn,goog";
var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
var socket = IO.Socket("https://ws-api.iextrading.com/1.0/tops");
socket.On(Socket.EVENT_MESSAGE, (message) =>
{
    eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes((string)message)));
});
socket.On(Socket.EVENT_CONNECT, () =>
{
    socket.Emit("subscribe", symbols);
});
```

Íme néhány példa létrehozott mintaeseményekre:

```json
{"symbol":"MSFT","marketPercent":0.03246,"bidSize":100,"bidPrice":74.8,"askSize":300,"askPrice":74.83,volume":70572,"lastSalePrice":74.825,"lastSaleSize":100,"lastSaleTime":1506953355123,lastUpdated":1506953357170,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"GOOG","marketPercent":0.04825,"bidSize":114,"bidPrice":870,"askSize":0,"askPrice":0,volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,lastUpdated":1506953357633,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"MSFT","marketPercent":0.03244,"bidSize":100,"bidPrice":74.8,"askSize":100,"askPrice":74.83,volume":70572,"lastSalePrice":74.825,"lastSaleSize":100,"lastSaleTime":1506953355123,lastUpdated":1506953359118,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"FB","marketPercent":0.01211,"bidSize":100,"bidPrice":169.9,"askSize":100,"askPrice":170.67,volume":39042,"lastSalePrice":170.67,"lastSaleSize":100,"lastSaleTime":1506953351912,lastUpdated":1506953359641,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"GOOG","marketPercent":0.04795,"bidSize":100,"bidPrice":959.19,"askSize":0,"askPrice":0,volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,lastUpdated":1506953360949,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"FB","marketPercent":0.0121,"bidSize":100,"bidPrice":169.9,"askSize":100,"askPrice":170.7,volume":39042,"lastSalePrice":170.67,"lastSaleSize":100,"lastSaleTime":1506953351912,lastUpdated":1506953362205,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"GOOG","marketPercent":0.04795,"bidSize":114,"bidPrice":870,"askSize":0,"askPrice":0,volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,lastUpdated":1506953362629,"sector":"softwareservices","securityType":"commonstock"}
```

>[!NOTE]
>Az esemény időbélyegzője **lastUpdated**, alapidőpont szerint.

### <a name="predictive-model-for-high-frequency-trading"></a>A nagy gyakoriságú kereskedelem prediktív modellje
Bemutató céljából a jelen anyagban a Darryl Shen [tanulmányában ismertetett](https://docplayer.net/23038840-Order-imbalance-based-strategy-in-high-frequency-trading.html) lineáris modellt használjuk.

A kötetsorrend-egyenetlenség (VOI) az aktuális ajánlattételi/lekérdezési ár és kötet, valamint a legutóbbi órajelhez tartozó ajánlattételi/lekérdezési ár és kötet egyik funkciója. Ez a tanulmány meghatározza az összefüggést a VOI és a jövőbeli ármozgás között. Lineáris modellt hoz létre a legutóbbi 5 VOI-érték és a következő 10 órajel során végbemenő árváltozás között. A modell tanítása az előző nap adatainak lineáris regressziós módszerrel történő feldolgozásával történik. 

A betanított modell ezt követően az ajánlatok árváltozásának előrejelzésére használható az aktuális kereskedési napon, valós időben. Ha a modell kellően nagy árváltozást jelez előre, akkor történik kereskedelmi ügylet végrehajtása. A küszöbérték-beállítástól függően egyetlen kereskedési napon kereskedelmi ügyletek ezreinek lebonyolítása várható egyetlen részvény esetében.

![Mennyiségi sorrendi egyensúlyhiány definíciója](./media/stream-analytics-high-frequency-trading/volume-order-imbalance-formula.png)

Most fejezzük ki a tanítási és előrejelzési műveleteket egy Azure Stream Analytics-feladatban.

Az első lépés a bemeneti adatok törlése. A **DATEADD** függvény használatával az alapidőpont datetime formátumra lesz konvertálva. A **TRY_CAST** függvény használatával az adattípusok anélkül alakíthatók át, hogy a lekérdezés meghiúsulna. Minden esetben jó megoldás a bemeneti mezők átalakítása a várt adattípusok alapján, így nem merül fel váratlan működés a mezők módosításakor vagy összehasonlításakor.

```SQL
WITH
typeconvertedquotes AS (
    /* convert all input fields to proper types */
    SELECT
        System.Timestamp AS lastUpdated,
        symbol,
        DATEADD(millisecond, CAST(lastSaleTime as bigint), '1970-01-01T00:00:00Z') AS lastSaleTime,
        TRY_CAST(bidSize as bigint) AS bidSize,
        TRY_CAST(bidPrice as float) AS bidPrice,
        TRY_CAST(askSize as bigint) AS askSize,
        TRY_CAST(askPrice as float) AS askPrice,
        TRY_CAST(volume as bigint) AS volume,
        TRY_CAST(lastSaleSize as bigint) AS lastSaleSize,
        TRY_CAST(lastSalePrice as float) AS lastSalePrice
    FROM quotes TIMESTAMP BY DATEADD(millisecond, CAST(lastUpdated as bigint), '1970-01-01T00:00:00Z')
),
timefilteredquotes AS (
    /* filter between 7am and 1pm PST, 14:00 to 20:00 UTC */
    /* clean up invalid data points */
    SELECT * FROM typeconvertedquotes
    WHERE DATEPART(hour, lastUpdated) >= 14 AND DATEPART(hour, lastUpdated) < 20 AND bidSize > 0 AND askSize > 0 AND bidPrice > 0 AND askPrice > 0
),
```

Ezt követően a **LAG** függvény használatával lekérjük az értékeket a legutóbbi órajelből. A **LIMIT DURATION** egy órás értékét önkényesen választottuk ki. Az adott ajánlatadási gyakoriság mellett biztonsággal feltételezhető, hogy a legutóbbi órajel egy órás időtartamon belül található.  

```SQL
shiftedquotes AS (
    /* get previous bid/ask price and size in order to calculate VOI */
    SELECT
        symbol,
        (bidPrice + askPrice)/2 AS midPrice,
        bidPrice,
        bidSize,
        askPrice,
        askSize,
        LAG(bidPrice) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS bidPricePrev,
        LAG(bidSize) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS bidSizePrev,
        LAG(askPrice) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS askPricePrev,
        LAG(askSize) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS askSizePrev
    FROM timefilteredquotes
),
```

Ezt követően már ki tudjuk számítani a VOI-értéket. Kiszűrjük a nulla értékeket arra az esetre, ha az előző órajel nem létezik.

```SQL
currentPriceAndVOI AS (
    /* calculate VOI */
    SELECT
        symbol,
        midPrice,
        (CASE WHEN (bidPrice < bidPricePrev) THEN 0
            ELSE (CASE WHEN (bidPrice = bidPricePrev) THEN (bidSize - bidSizePrev) ELSE bidSize END)
         END) -
        (CASE WHEN (askPrice < askPricePrev) THEN askSize
            ELSE (CASE WHEN (askPrice = askPricePrev) THEN (askSize - askSizePrev) ELSE 0 END)
         END) AS VOI
    FROM shiftedquotes
    WHERE
        bidPrice IS NOT NULL AND
        bidSize IS NOT NULL AND
        askPrice IS NOT NULL AND
        askSize IS NOT NULL AND
        bidPricePrev IS NOT NULL AND
        bidSizePrev IS NOT NULL AND
        askPricePrev IS NOT NULL AND
        askSizePrev IS NOT NULL
),
```

A **LAG** függvény használatával létrehozunk egy sorozatot 2 egymást követő VOI-értékkel, amelyeket 10 egymás utáni középár érték követ.

```SQL
shiftedPriceAndShiftedVOI AS (
    /* get 10 future prices and 2 previous VOIs */
    SELECT
        symbol,
        midPrice AS midPrice10,
        LAG(midPrice, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice9,
        LAG(midPrice, 2) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice8,
        LAG(midPrice, 3) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice7,
        LAG(midPrice, 4) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice6,
        LAG(midPrice, 5) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice5,
        LAG(midPrice, 6) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice4,
        LAG(midPrice, 7) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice3,
        LAG(midPrice, 8) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice2,
        LAG(midPrice, 9) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice1,
        LAG(midPrice, 10) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice,
        LAG(VOI, 10) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI1,
        LAG(VOI, 11) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI2
    FROM currentPriceAndVOI
),
```

Ezt követően átalakítjuk az adatokat kétváltozós lineáris modellhez tartozó bemeneti adatokká. Ismételten kiszűrjük azokat az eseményeket, ahol nem áll rendelkezésre minden adat.

```SQL
modelInput AS (
    /* create feature vector, x being VOI, y being delta price */
    SELECT
        symbol,
        (midPrice1 + midPrice2 + midPrice3 + midPrice4 + midPrice5 + midPrice6 + midPrice7 + midPrice8 + midPrice9 + midPrice10)/10.0 - midPrice AS y,
        VOI1 AS x1,
        VOI2 AS x2
    FROM shiftedPriceAndShiftedVOI
    WHERE
        midPrice1 IS NOT NULL AND
        midPrice2 IS NOT NULL AND
        midPrice3 IS NOT NULL AND
        midPrice4 IS NOT NULL AND
        midPrice5 IS NOT NULL AND
        midPrice6 IS NOT NULL AND
        midPrice7 IS NOT NULL AND
        midPrice8 IS NOT NULL AND
        midPrice9 IS NOT NULL AND
        midPrice10 IS NOT NULL AND
        midPrice IS NOT NULL AND
        VOI1 IS NOT NULL AND
        VOI2 IS NOT NULL
),
```

Mivel az Azure Stream Analytics nem rendelkezik beépített lineáris regressziós funkcióval, a lineáris modell együtthatóinak kiszámításához a **SUM** és az **AVG** összesítéseket használjuk.

![Lineáris regressziós matematikai képlet](./media/stream-analytics-high-frequency-trading/linear-regression-formula.png)

```SQL
modelagg AS (
    /* get aggregates for linear regression calculation,
     http://faculty.cas.usf.edu/mbrannick/regression/Reg2IV.html */
    SELECT
        symbol,
        SUM(x1 * x1) AS x1x1,
        SUM(x2 * x2) AS x2x2,
        SUM(x1 * y) AS x1y,
        SUM(x2 * y) AS x2y,
        SUM(x1 * x2) AS x1x2,
        AVG(y) AS avgy,
        AVG(x1) AS avgx1,
        AVG(x2) AS avgx2
    FROM modelInput
    GROUP BY symbol, TumblingWindow(hour, 24, -4)
),
modelparambs AS (
    /* calculate b1 and b2 for the linear model */
    SELECT
        symbol,
        (x2x2 * x1y - x1x2 * x2y)/(x1x1 * x2x2 - x1x2 * x1x2) AS b1,
        (x1x1 * x2y - x1x2 * x1y)/(x1x1 * x2x2 - x1x2 * x1x2) AS b2,
        avgy,
        avgx1,
        avgx2
    FROM modelagg
),
model AS (
    /* calculate a for the linear model */
    SELECT
        symbol,
        avgy - b1 * avgx1 - b2 * avgx2 AS a,
        b1,
        b2
    FROM modelparambs
),
```

Ahhoz, hogy az aktuális esemény pontozásához az előző nap modelljét tudjuk használni, össze szeretnénk kapcsolni az ajánlatokat a modellel. A **JOIN** függvény helyett a **UNION** függvényt használjuk a modellesemények és az ajánlati események egyesítéséhez. Ezután a **LAG** függvény használatával párosítjuk az eseményeket az előző nap modelljével, így pontosan egy egyezést kapunk. A hétvége miatt három napot kell visszakeresnünk. Egy egyszerű **JOIN** függvény használatakor minden ajánlati eseményhez három modell tartozna.

```SQL
shiftedVOI AS (
    /* get two consecutive VOIs */
    SELECT
        symbol,
        midPrice,
        VOI AS VOI1,        
        LAG(VOI, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI2
    FROM currentPriceAndVOI
),
VOIAndModel AS (
    /* combine VOIs and models */
    SELECT
        'voi' AS type,
        symbol,
        midPrice,
        VOI1,
        VOI2,
        0.0 AS a,
        0.0 AS b1,
        0.0 AS b2
    FROM shiftedVOI
    UNION
    SELECT
        'model' AS type,
        symbol,
        0.0 AS midPrice,
        0 AS VOI1,
        0 AS VOI2,
        a,
        b1,
        b2
    FROM model
),
VOIANDModelJoined AS (
    /* match VOIs with the latest model within 3 days (72 hours, to take the weekend into account) */
    SELECT
        symbol,
        midPrice,
        VOI1 as x1,
        VOI2 as x2,
        LAG(a, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS a,
        LAG(b1, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS b1,
        LAG(b2, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS b2
    FROM VOIAndModel
    WHERE type = 'voi'
),
```

A modell alapján most már végezhetünk előrejelzéseket és előállíthatunk vételi/eladási jeleket, 0,02-es küszöbértékkel. A 10-es kereskedési érték felel meg a vételnek, a –10-es kereskedési érték pedig az eladásnak.

``` SQL
prediction AS (
    /* make prediction if there is a model */
    SELECT
        symbol,
        midPrice,
        a + b1 * x1 + b2 * x2 AS efpc
    FROM VOIANDModelJoined
    WHERE
        a IS NOT NULL AND
        b1 IS NOT NULL AND
        b2 IS NOT NULL AND
        x1 IS NOT NULL AND
        x2 IS NOT NULL
),
tradeSignal AS (
    /* generate buy/sell signals */
    SELECT
        DateAdd(hour, -7, System.Timestamp) AS time,
        symbol,     
        midPrice,
        efpc,
        CASE WHEN (efpc > 0.02) THEN 10 ELSE (CASE WHEN (efpc < -0.02) THEN -10 ELSE 0 END) END AS trade,
        DATETIMEFROMPARTS(DATEPART(year, System.Timestamp), DATEPART(month, System.Timestamp), DATEPART(day, System.Timestamp), 0, 0, 0, 0) as date
    FROM prediction
),
```

### <a name="trading-simulation"></a>Kereskedelmi szimuláció
Ha már rendelkezünk a kereskedelmi jelekkel, tesztelni szeretnénk a kereskedelmi stratégia hatékonyságát valós kereskedelmi tevékenység folytatása nélkül. 

Ezt egy percenkénti ugrási gyakoriságú ugróablakokkal ellátott UDA segítségével tehetjük meg. A további dátumalapú csoportosítás és a HAVING záradék lehetővé teszi, hogy az ablakban csak az azonos napi események jelenjenek meg. Két napot átfogó ugróablakok esetén a **GROUP BY** dátum osztja szét a csoportosítást előző és aktuális napra. A **HAVING** záradék az aktuális nap végéig szűri ki az ablakokat, a csoportosítást viszont az előző napig végzi el.

```SQL
simulation AS
(
    /* perform trade simulation for the past 7 hours to cover an entire trading day, and generate output every minute */
    SELECT
        DateAdd(hour, -7, System.Timestamp) AS time,
        symbol,
        date,
        uda.TradeSimulation(tradeSignal) AS s
    FROM tradeSignal
    GROUP BY HoppingWindow(minute, 420, 1), symbol, date
    Having DateDiff(day, date, time) < 1 AND DATEPART(hour, time) < 13
)
```

A JavaScript UDA inicializálja az `init` függvény összes gyűjtőjét, kiszámítja az állapotváltást minden egyes esemény ablakhoz történő hozzáadásakor, valamint az ablak végén visszaadja a szimuláció eredményeit. Az általános kereskedési folyamat a következő:

- Akkor vásárolunk, amikor vételi jelet kapunk, és nincs érvényben állománytartási rendelkezés.
- Akkor adunk el, amikor eladási jelet kapunk, és állománytartási rendelkezés van érvényben.
- Akkor shortolunk, ha nincs érvényben állománytartási rendelkezés. 

Shortolási helyzetben vagy ha vételi jelet kapunk, vásárolunk. Ebben a szimulációban egy készletből vagy rövid 10 részvényből állunk. A tranzakciós költség 8 dollár.

```javascript
function main() {
    var TRADE_COST = 8.0;
    var SHARES = 10;
    this.init = function () {
        this.own = false;
        this.pos = 0;
        this.pnl = 0.0;
        this.tradeCosts = 0.0;
        this.buyPrice = 0.0;
        this.sellPrice = 0.0;
        this.buySize = 0;
        this.sellSize = 0;
        this.buyTotal = 0.0;
        this.sellTotal = 0.0;
    }
    this.accumulate = function (tradeSignal, timestamp) {
        if(!this.own && tradeSignal.trade == 10) {
          // Buy to open
          this.own = true;
          this.pos = 1;
          this.buyPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.buySize += SHARES;
          this.buyTotal += SHARES * tradeSignal.midprice;
        } else if(!this.own && tradeSignal.trade == -10) {
          // Sell to open
          this.own = true;
          this.pos = -1
          this.sellPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.sellSize += SHARES;
          this.sellTotal += SHARES * tradeSignal.midprice;
        } else if(this.own && this.pos == 1 && tradeSignal.trade == -10) {
          // Sell to close
          this.own = false;
          this.pos = 0;
          this.sellPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.pnl += (this.sellPrice - this.buyPrice)*SHARES - 2*TRADE_COST;
          this.sellSize += SHARES;
          this.sellTotal += SHARES * tradeSignal.midprice;
          // Sell to open
          this.own = true;
          this.pos = -1;
          this.sellPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.sellSize += SHARES;        
          this.sellTotal += SHARES * tradeSignal.midprice;
        } else if(this.own && this.pos == -1 && tradeSignal.trade == 10) {
          // Buy to close
          this.own = false;
          this.pos = 0;
          this.buyPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.pnl += (this.sellPrice - this.buyPrice)*SHARES - 2*TRADE_COST;
          this.buySize += SHARES;
          this.buyTotal += SHARES * tradeSignal.midprice;
          // Buy to open
          this.own = true;
          this.pos = 1;
          this.buyPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.buySize += SHARES;         
          this.buyTotal += SHARES * tradeSignal.midprice;
        }
    }
    this.computeResult = function () {
        var result = {
            "pnl": this.pnl,
            "buySize": this.buySize,
            "sellSize": this.sellSize,
            "buyTotal": this.buyTotal,
            "sellTotal": this.sellTotal,
            "tradeCost": this.tradeCost
            };
        return result;
    }
}
```

Végül a képi megjelenítés a Power BI-irányítópulton történik.

```SQL
SELECT * INTO tradeSignalDashboard FROM tradeSignal /* output tradeSignal to PBI */
SELECT
    symbol,
    time,
    date,
    TRY_CAST(s.pnl as float) AS pnl,
    TRY_CAST(s.buySize as bigint) AS buySize,
    TRY_CAST(s.sellSize as bigint) AS sellSize,
    TRY_CAST(s.buyTotal as float) AS buyTotal,
    TRY_CAST(s.sellTotal as float) AS sellTotal
    INTO pnlDashboard
FROM simulation /* output trade simulation to PBI */
```

![Trades Power BI diagram vizualizáció](./media/stream-analytics-high-frequency-trading/trades-power-bi-chart.png)

![PNL Power BI diagram vizualizáció](./media/stream-analytics-high-frequency-trading/pnl-power-bi-chart.png)


## <a name="summary"></a>Összefoglalás
Az Azure Stream Analyticsben valósághű nagy gyakoriságú kereskedelmi modellt hozhatunk létre egy közepesen összetett lekérdezéssel. A beépített lineáris regressziós funkció hiánya miatt le kell egyszerűsítenünk a modellt úgy, hogy öt bemeneti változó helyett csak kettőt használjon. Az eltökélt felhasználók számára azonban a több dimenzióval rendelkező és kifinomultabb algoritmusok implementálhatóak JavaScript UDA-ként is. 

Érdemes megjegyezni, hogy a legtöbb lekérdezés (a JavaScript UDA kivételével) tesztelése és hibakeresése elvégezhető a [Visual Studióhoz készült Azure Stream Analytics](stream-analytics-tools-for-visual-studio-install.md) eszközökkel a Visual Studióban. A kezdeti lekérdezés megírását követően a szerző kevesebb mint 30 percet töltött teszteléssel és hibakereséssel a Visual Studióban. 

Az UDA hibakeresése jelenleg nem végezhető el a Visual Studióban. Dolgozunk ennek lehetővé tételén a JavaScript-kód megírásának lehetőségével együtt. Továbbá vegye figyelembe, hogy az UDA-val kapcsolatos mezők nevei kisbetűsek. A lekérdezés tesztelése során ez nem volt nyilvánvaló. Az Azure Stream Analytics 1.1-es kompatibilitási szintjén azonban megtartjuk a mezőnév kis- és nagybetűhasználatát, természetesebb működést biztosítva.

Reméljük, ez a cikk az összes Azure Stream Analytics-felhasználó számára inspirációt jelent, akik az általunk nyújtott szolgáltatással folyamatos, fejlett elemzéseket tudnak végezni, közel valós időben. Küldje el nekünk visszajelzését, amellyel megkönnyíthető a továbbfejlesztett elemzési forgatókönyvek lekérdezéseinek végrehajtása.
