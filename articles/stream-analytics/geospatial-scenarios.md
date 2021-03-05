---
title: Geokerítések és térinformatikai összesítés Azure Stream Analytics
description: Ez a cikk azt ismerteti, hogyan használható a Azure Stream Analytics a geokerítések és a térinformatikai összesítéshez.
ms.service: stream-analytics
author: jasonwhowell
ms.author: jasonh
ms.topic: how-to
ms.date: 04/02/2019
ms.openlocfilehash: fc9442b01685deb710c49adf6be84af740a11805
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174988"
---
# <a name="geofencing-and-geospatial-aggregation-scenarios-with-azure-stream-analytics"></a>Geokerítések és térinformatikai összesítési forgatókönyvek Azure Stream Analytics

A beépített térinformatikai függvények Azure Stream Analytics használatával olyan forgatókönyvekhez készíthet alkalmazásokat, mint például a flotta-felügyelet, a Ride Sharing, a csatlakoztatott autók és az eszközök nyomon követése.

## <a name="geofencing"></a>Geokerítések kezelése

A Azure Stream Analytics a felhőben és a IoT Edge futtatókörnyezetben is támogatja az alacsony késésű valós idejű geokerítések számításokat.

### <a name="geofencing-scenario"></a>Geokerítések forgatókönyv

A gyártó vállalatnak az épületekben lévő eszközöket kell nyomon követnie. Minden eszközt a GPS-vel szereltek fel, és az értesítéseket szeretnék kapni, ha egy eszköz elhagyja a bizonyos területeket.

Az ebben a példában használt hivatkozási adatok a geokerítésen információkkal szolgálnak az épületekhez és az egyes épületekben engedélyezett eszközökhöz. Ne feledje, hogy a hivatkozási adatértékek lehetnek statikusak vagy lassan változnak. Ehhez a forgatókönyvhöz statikus referenciát kell használni. Az adatstream folyamatosan kibocsátja az eszköz AZONOSÍTÓját és aktuális pozícióját.

### <a name="define-geofences-in-reference-data"></a>Geofences definiálása

Egy geokerítésen GeoJSON objektummal is meghatározható. A 1,2-es vagy újabb kompatibilitási verzióval rendelkező feladatok esetében a geofences a jól ismert szöveg (WKT) használatával is meghatározható `NVARCHAR(MAX)` . A WKT egy Nyílt térinformatikai konzorcium (OGC) szabvány, amely szöveges formátumban jeleníti meg a térbeli adattípusokat.

A beépített térinformatikai függvények a definiált geofences segítségével megtudhatják, hogy egy elem egy adott geokerítésen-sokszögben van-e vagy sem.

Az alábbi táblázat az Azure Blob Storage-ban vagy egy Azure SQL-táblában tárolt geokerítésen-hivatkozásokat szemlélteti. Minden helyet egy térinformatikai sokszög képvisel, és minden eszköz egy engedélyezett hely azonosítójával van társítva.

|SiteID|SiteName|Geokerítésen|AllowedDeviceID|
|------|--------|--------|---------------|
|1|"Redmond-építési 41"|"SOKSZÖG ((-122.1337357922017 47.63782998329432,-122.13373042778369 47.637634793257305,-122.13346757130023 47.637642022530954,-122.13348902897235 47.637508280806806,-122.13361777500506 47.637508280806806,-122.13361241058703 47.63732393354484,-122.13265754417773 47.63730947490855,-122.13266290859576 47.637519124743164,-122.13302232460376 47.637515510097955,-122.13301696018573 47.63764925180358,-122.13272728161212 47.63764925180358,-122.13274873928424 47.63784082716388,-122.13373579220172 47.63782998329432)"|B|
|2|"Redmond-építési 40"|"SOKSZÖG ((-122.1336154507967 47.6366745947009,-122.13361008637867 47.636483015064535,-122.13349206918201 47.636479400347675,-122.13349743360004 47.63636372927573,-122.13372810357532 47.63636372927573,-122.13373346799335 47.63617576323771,-122.13263912671528 47.63616491902258,-122.13264985555134 47.63635649982525,-122.13304682248554 47.636367344000604,-122.13305218690357 47.63650831807564,-122.13276250832996 47.636497473929516,-122.13277323716602 47.63668543881025,-122.1336154507967 47.6366745947009)"|Egy|
|3|"Redmond-építési 22"|"SOKSZÖG ((-122.13611660248233 47.63758544698554,-122.13635263687564 47.6374083293018,-122.13622389084293 47.63733603619712,-122.13622389084293 47.63717699101473,-122.13581619507266 47.63692757827657,-122.13559625393344 47.637046862778135,-122.13569281345798 47.637144458985965,-122.13570890671207 47.637314348246214,-122.13611660248233 47.63758544698554))"|C|

### <a name="generate-alerts-with-geofence"></a>Riasztások előállítása a geokerítésen

Az eszközök percenként bocsátják ki az azonosítót és a helyet a nevű streamen keresztül `DeviceStreamInput` . A következő táblázat a bemeneti adatfolyamot tartalmazza.

|DeviceID|GeoPosition|
|--------|-----------|
|Egy|"PONT (-122.13292341559497 47.636318374032726)"|
|B|"PONT (-122.13338475554553 47.63743531308874)"|
|C|"PONT (-122.13354001095752 47.63627622505007)"|

Írhat olyan lekérdezést, amely összekapcsolja az eszköz streamjét a geokerítésen, és riasztást hoz létre minden alkalommal, amikor egy eszköz egy engedélyezett épületen kívül esik.

```SQL
SELECT DeviceStreamInput.DeviceID, SiteReferenceInput.SiteID, SiteReferenceInput.SiteName 
INTO Output
FROM DeviceStreamInput 
JOIN SiteReferenceInput
ON st_within(DeviceStreamInput.GeoPosition, SiteReferenceInput.Geofence) = 0
WHERE DeviceStreamInput.DeviceID = SiteReferenceInput.AllowedDeviceID
```

Az alábbi ábra a geofences jelöli. Láthatja, hogy az eszközök hol találhatók az adatfolyam-adatok bemenetének megfelelően.

![Geofences építése](./media/geospatial-scenarios/building-geofences.png)

A "C" eszköz a 2. AZONOSÍTÓJÚ épületen belül található, amely nem engedélyezett a hivatkozási érték alapján. Az eszköznek a 3. AZONOSÍTÓJÚ épületen belül kell lennie. A művelet futtatásakor a rendszer riasztást állít elő ehhez a konkrét szabálysértéshez.

### <a name="site-with-multiple-allowed-devices"></a>Hely több engedélyezett eszközzel

Ha egy hely több eszközt is engedélyez, az eszközök azonosítóinak tömbje meghatározható a ben, a záradékban pedig User-Defined függvény használható, amely ellenőrzi, hogy `AllowedDeviceID` `WHERE` az adatfolyam-eszköz azonosítója megfelel-e a listában szereplő összes eszköz azonosítójának. További információkért tekintse meg a [JavaScript UDF](stream-analytics-javascript-user-defined-functions.md) -oktatóanyag a felhőalapú feladatokhoz és a [C# UDF](stream-analytics-edge-csharp-udf.md) -oktatóanyag az Edge-feladatokhoz című témakört.

## <a name="geospatial-aggregation"></a>Térinformatikai összesítés

A Azure Stream Analytics a felhőben és a IoT Edge futtatókörnyezetben is támogatja az alacsony késésű valós idejű térinformatikai összesítést.

### <a name="geospatial-aggregation-scenario"></a>Térinformatikai összesítési forgatókönyv

A CAB-vállalat egy valós idejű alkalmazást szeretne létrehozni, amely a jelenleg nagyobb keresletet tapasztaló városok területein való használatra vágyó vezetőfülke-illesztőprogramokat ismerteti.

A vállalat a város logikai régióit a hivatkozási adatként tárolja. Mindegyik régiót egy RegionID, egy RegionName és egy Geokerítésen határozza meg.

### <a name="define-the-geofences"></a>A geofences megadása

Az alábbi táblázat az Azure Blob Storage-ban vagy egy Azure SQL-táblában tárolt geokerítésen-hivatkozásokat szemlélteti. Minden régiót egy térinformatikai sokszög képvisel, amely a folyamatos átviteli adatoktól érkező kérések összekapcsolására szolgál.

Ezek a sokszögek csak referenciák, és nem jelölik a tényleges város logikai vagy fizikai elkülönítését.

|RegionID|RegionName|Geokerítésen|
|--------|----------|--------|
|1|Soho|"SOKSZÖG ((-74.00279525078275 40.72833625216264,-74.00547745979765 40.721929158663244,-74.00125029839018 40.71893680218994,-73.9957785919998 40.72521409075776,-73.9972377137039 40.72557184584898,-74.00279525078275 40.72833625216264))"|
|2|Chinatown|"SOKSZÖG ((-73.99712367114876 40.71281582267133,-73.9901070123658 40.71336881907936,-73.99023575839851 40.71452359088633,-73.98976368961189 40.71554823078944,-73.99551434573982 40.717337246783735,-73.99480624255989 40.718491949759304,-73.99652285632942 40.719109951574,-73.99776740131233 40.7168005470334,-73.99903340396736 40.71727219249899,-74.00193018970344 40.71938642421256,-74.00409741458748 40.71688186545551,-74.00051398334358 40.71517415773184,-74.0004281526551 40.714377212470005,-73.99849696216438 40.713450141693166,-73.99748845157478 40.71405192594819,-73.99712367114876 40.71281582267133)"|
|3|Tribeca|"SOKSZÖG ((-74.01091641815208 40.72583120006787,-74.01338405044578 40.71436586362705,-74.01370591552757 40.713617702123415,-74.00862044723533 40.711308107057235,-74.00194711120628 40.7194238654018,-74.01091641815208 40.72583120006787))"|

### <a name="aggregate-data-over-a-window-of-time"></a>Összesített adat egy időablakon belül

A következő táblázat a "rides" streaming-adatátviteli szolgáltatásait tartalmazza.

|UserID|FromLocation|ToLocation|TripRequestedTime|
|------|------------|----------|-----------------|
|Egy|"PONT (-74.00726861389182 40.71610611981975)"|"PONT (-73.98615095917779 40.703107386025835)"|"2019-03-12T07:00:00Z"|
|B|"PONT (-74.00249841021645 40.723827238895666)"|"PONT (-74.01160699942085 40.71378884930115)"|"2019-03-12T07:01:00Z"|
|C|"PONT (-73.99680120565864 40.716439898624024)"|"PONT (-73.98289663412544 40.72582343969828)"|"2019-03-12T07:02:00Z"|
|D|"PONT (-74.00741090068288 40.71615626755086)"|"PONT (-73.97999843120539 40.73477895807408)"|"2019-03-12T07:03:00Z"|

A következő lekérdezés összekapcsolja az eszköz streamjét a geokerítésen, és kiszámítja régiónként a kérések számát percenként 15 percen belül.

```SQL
SELECT count(*) as NumberOfRequests, RegionsRefDataInput.RegionName 
FROM UserRequestStreamDataInput
JOIN RegionsRefDataInput 
ON st_within(UserRequestStreamDataInput.FromLocation, RegionsRefDataInput.Geofence) = 1
GROUP BY RegionsRefDataInput.RegionName, hoppingwindow(minute, 1, 15)
```

Ez a lekérdezés percenkénti kérelmek számát adja eredményül az elmúlt 15 percben a város minden régiója számára. Ezeket az információkat könnyen megtekintheti Power BI irányítópulton, vagy az összes illesztőprogramnak SMS szöveges üzenetként is közvetíthető az Azure functions szolgáltatással való integráció révén.

Az alábbi képen látható a lekérdezés kimenete Power BI irányítópultra. 

![Eredmény kimenet Power BI irányítópulton](./media/geospatial-scenarios/power-bi-output.png)


## <a name="next-steps"></a>Következő lépések

* [Stream Analytics térinformatikai függvények bemutatása](stream-analytics-geospatial-functions.md)
* [Térinformatikai függvények (Azure Stream Analytics)](/stream-analytics-query/geospatial-functions)