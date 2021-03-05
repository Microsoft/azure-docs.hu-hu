---
title: Azure Stream Analytics térinformatikai függvények bemutatása
description: Ez a cikk a Azure Stream Analytics feladatokban használt térinformatikai függvényeket ismerteti.
ms.service: stream-analytics
author: jasonwhowell
ms.author: jasonh
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: 2835918cf381cb0fbd917ce9bf4650730878d711
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102178499"
---
# <a name="introduction-to-stream-analytics-geospatial-functions"></a>Stream Analytics térinformatikai függvények bemutatása

A Azure Stream Analytics térinformatikai funkciói lehetővé teszik a valós idejű elemzést a térinformatikai adatok folyamatos átviteléhez. Mindössze néhány sornyi kóddal, összetett forgatókönyvekhez fejlesztheti az éles környezet kialakítására szolgáló megoldást. Ezek a függvények támogatják az összes WKT-típust, valamint a GeoJSON pontot, a sokszöget és a LineString.

Példa a térinformatikai függvények előnyeit kihasználó forgatókönyvekre:

* Ride-Sharing
* A flotta kezelése
* Eszközkövetés
* Földrajzi kerítés
* Telefonos nyomon követés a cella helyei között

Stream Analytics lekérdezési nyelv hét beépített térinformatikai funkcióval rendelkezik: **CreateLineString**, **CreatePoint**, **CreatePolygon**, **ST_DISTANCE**, **ST_OVERLAPS**, **ST_INTERSECTS** és **ST_WITHIN**.

## <a name="createlinestring"></a>CreateLineString

A `CreateLineString` függvény fogadja a pontokat, és egy GeoJSON LineString ad vissza, amely egy Térkép szerinti sorba rajzolható. LineString létrehozásához legalább két pontnak kell lennie. A LineString pontok sorrendben lesznek csatlakoztatva.

A következő lekérdezés `CreateLineString` három pont használatával hoz létre egy LineString. Az első pont a streaming bemeneti adatokból jön létre, míg a másik kettő manuálisan jön létre.

```SQL 
SELECT  
     CreateLineString(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5))  
FROM input  
```  

### <a name="input-example"></a>Példa bemenetre  
  
|latitude|hosszúság|  
|--------------|---------------|  
|3.0|– 10,2|  
|– 87,33|20,2321|  
  
### <a name="output-example"></a>Példa kimenetre  

 {"type": "LineString", "koordináták": [[-10,2, 3,0], [10,0, 10,0], [10,5, 10,5]]}

 {"type": "LineString", "koordináták": [[20,2321,-87,33], [10,0, 10,0], [10,5, 10,5]]}

További tudnivalókért tekintse meg a [CreateLineString](/stream-analytics-query/createlinestring) -referenciát.

## <a name="createpoint"></a>CreatePoint

A `CreatePoint` függvény a szélességet és a hosszúságot fogadja, és egy GeoJSON pontot ad vissza, amely egy térképen ábrázolható. A szélességi és hosszúsági köröknek **lebegőpontos** adattípusnak kell lenniük.

A következő példa `CreatePoint` egy pontot hoz létre a szélességi és hosszúsági szinttel a bemeneti adatok folyamatos átviteléhez.

```SQL 
SELECT  
     CreatePoint(input.latitude, input.longitude)  
FROM input 
```  

### <a name="input-example"></a>Példa bemenetre  
  
|latitude|hosszúság|  
|--------------|---------------|  
|3.0|– 10,2|  
|– 87,33|20,2321|  
  
### <a name="output-example"></a>Példa kimenetre
  
 {"type": "pont", "koordináták": [-10,2, 3,0]}  
  
 {"type": "pont", "koordináták": [20,2321,-87,33]}  

További tudnivalókért tekintse meg a [CreatePoint](/stream-analytics-query/createpoint) -referenciát.

## <a name="createpolygon"></a>CreatePolygon

A `CreatePolygon` függvény elfogadja a pontokat, és egy GeoJSON-sokszög-rekordot ad vissza. A pontok sorrendjének a jobb oldali gyűrű tájolását kell követnie, vagy az óramutató járásával ellentétes irányban kell állnia. Képzelje el, hogy az egyik pontról a másikra mutat a deklarált sorrendben. A sokszög középpontja a teljes idő alatt marad.

A következő példában a lekérdezés `CreatePolygon` három pontból hoz létre sokszöget. Az első két pont létrehozása manuálisan történik, és a rendszer az utolsó pontot hozza létre a bemeneti adatokból.

```SQL 
SELECT  
     CreatePolygon(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5), CreatePoint(input.latitude, input.longitude))  
FROM input  
```  

### <a name="input-example"></a>Példa bemenetre  
  
|latitude|hosszúság|  
|--------------|---------------|  
|3.0|– 10,2|  
|– 87,33|20,2321|  
  
### <a name="output-example"></a>Példa kimenetre  

 {"type": "sokszög", "koordináták": [[[-10,2, 3,0], [10,0, 10,0], [10,5, 10,5], [-10,2, 3,0]]]}
 
 {"type": "sokszög", "koordináták": [[[20,2321,-87,33], [10,0, 10,0], [10,5, 10,5], [20,2321,-87,33]]]}

További tudnivalókért tekintse meg a [CreatePolygon](/stream-analytics-query/createpolygon) -referenciát.


## <a name="st_distance"></a>ST_DISTANCE
A `ST_DISTANCE` függvény a két geometria közötti távolságot adja vissza méterben. 

A következő lekérdezés `ST_DISTANCE` egy esemény előállítására szolgál, ha a gáz állomása kevesebb, mint 10 km az autótól.

```SQL
SELECT Cars.Location, Station.Location 
FROM Cars c  
JOIN Station s ON ST_DISTANCE(c.Location, s.Location) < 10 * 1000
```

További tudnivalókért tekintse meg a [ST_DISTANCE](/stream-analytics-query/st-distance) -referenciát.

## <a name="st_overlaps"></a>ST_OVERLAPS
A `ST_OVERLAPS` függvény összehasonlítja a két geometriát. Ha a geometriák átfedésben vannak, a függvény 1 értéket ad vissza. A függvény 0 értéket ad vissza, ha a geometriák nem fedik át egymást. 

A következő lekérdezés a használatával `ST_OVERLAPS` hoz létre egy eseményt, amikor egy épület egy lehetséges árvízi zónán belül van.

```SQL
SELECT Building.Polygon, Building.Polygon 
FROM Building b 
JOIN Flooding f ON ST_OVERLAPS(b.Polygon, b.Polygon) 
```

A következő példa egy eseményt hoz létre, amikor egy Storm egy autó irányába mutat.

```SQL
SELECT Cars.Location, Storm.Course
FROM Cars c, Storm s
JOIN Storm s ON ST_OVERLAPS(c.Location, s.Course)
```

További tudnivalókért tekintse meg a [ST_OVERLAPS](/stream-analytics-query/st-overlaps) -referenciát.

## <a name="st_intersects"></a>ST_INTERSECTS
A `ST_INTERSECTS` függvény összehasonlítja a két geometriát. Ha a geometriák metszik egymást, akkor a függvény 1 értéket ad vissza. A függvény a 0 értéket adja vissza, ha a geometriák nem metszik egymást.

A következő példában a lekérdezés `ST_INTERSECTS` azt határozza meg, hogy egy burkolt út a Dirt Roadot metszi-e.

```SQL 
SELECT  
     ST_INTERSECTS(input.pavedRoad, input.dirtRoad)  
FROM input  
```  

### <a name="input-example"></a>Példa bemenetre  
  
|datacenterArea|stormArea|  
|--------------------|---------------|  
|{"type": "LineString", "koordináták": [[-10,0, 0,0], [0,0, 0,0], [10,0, 0,0]]}|{"type": "LineString", "koordináták": [[0,0, 10,0], [0,0, 0,0], [0,0,-10,0]]}|  
|{"type": "LineString", "koordináták": [[-10,0, 0,0], [0,0, 0,0], [10,0, 0,0]]}|{"type": "LineString", "koordináták": [[-10,0, 10,0], [0,0, 10,0], [10,0, 10,0]]}|  
  
### <a name="output-example"></a>Példa kimenetre  

 1  
  
 0  

További tudnivalókért tekintse meg a [ST_INTERSECTS](/stream-analytics-query/st-intersects) -referenciát.

## <a name="st_within"></a>ST_WITHIN
A `ST_WITHIN` függvény meghatározza, hogy a geometria egy másik geometrián belül van-e. Ha az első az utolsó, a függvény 1 értéket ad vissza. A függvény a 0 értéket fogja visszaadni, ha az első geometria nem az utolsón belül található.

A következő példa a lekérdezés használatával `ST_WITHIN` határozza meg, hogy a kézbesítési célhely a megadott raktári sokszögen belülre esik-e.

```SQL 
SELECT  
     ST_WITHIN(input.deliveryDestination, input.warehouse)  
FROM input 
```  

### <a name="input-example"></a>Példa bemenetre  
  
|deliveryDestination|adatraktár|  
|-------------------------|---------------|  
|{"type": "pont", "koordináták": [76,6, 10,1]}|{"type": "sokszög", "koordináták": [[0,0, 0,0], [10,0, 0,0], [10,0, 10,0], [0,0, 10,0], [0,0, 0,0]]}|  
|{"type": "pont", "koordináták": [15,0, 15,0]}|{"type": "sokszög", "koordináták": [[10,0, 10,0], [20,0, 10,0], [20,0, 20,0], [10,0, 20,0], [10,0, 10,0]]}|  
  
### <a name="output-example"></a>Példa kimenetre  

 0  
  
 1  

További tudnivalókért tekintse meg a [ST_WITHIN](/stream-analytics-query/st-within) -referenciát.

## <a name="next-steps"></a>Következő lépések

* [Bevezetés a Azure Stream Analyticsba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](/rest/api/streamanalytics/)
