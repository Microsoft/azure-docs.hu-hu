---
title: Kiterjesztése GeoJSON geometriája Azure Maps-|} A Microsoft Docs
description: Ismerje meg, hogyan kiterjesztése GeoJSON geometriája Azure Maps-Közösséghez
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: be3c31951c4721a861f9239c5220419dec11b6bf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60799147"
---
# <a name="extending-geojson-geometries"></a>Kiterjesztése GeoJSON geometriája

Az Azure Maps belső/mentén földrajzi funkciókat keresés hatékony API-k listáját tartalmazza.
Ezen API-k egységesen ezeket [GeoJSON specifikációja] [ 1] megjelenítésére a földrajzi funkciókat (például: állapot határokat, útvonalak).  

A [GeoJSON specifikációja] [ 1] csak az a következő geometriája támogatja:

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* Pont
* Sokszög

Néhány Azure Maps API-k (például: [Keresés belül geometriai](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)) fogadja el a "Kör", például geometriája nem része a [GeoJSON specifikációja][1].

Ez a cikk részletes leírást nyújt a hogyan terjeszti ki az Azure Maps a [GeoJSON specifikációja] [ 1] képviseletére geometriája bizonyos.

### <a name="circle"></a>Kör

A `Circle` geometriai nem támogatja a [GeoJSON specifikációja][1]. Használjuk a `GeoJSON Feature` kör képviselő objektum.

A `Circle` geometriai jelölt használatával a `GeoJSON Feature` objektum __kell__ tartalmazhatja a következő:

1. Középre
   >A kör center jelölt használatával egy `GeoJSON Point` típusa.

2. Sugár
   >A kör `radius` használatával jelölt `GeoJSON Feature`a tulajdonságai. A radius-érték _mérőszámok_ típusúnak kell lennie, és `double`.

3. SubType
   >A körgeometriának a `subType` tulajdonságot is tartalmaznia kell. Ennek a tulajdonságnak szerepelnie kell a `GeoJSON Feature` tulajdonságai között, és _Circle_ értékűnek kell lennie


#### <a name="example"></a>Példa

Itt látható, hogyan fogja kijelenti középre kör (földrajzi szélesség: 47.639754, földrajzi hosszúság:-122.126986) 100 mérőszámok használatával egyenlő egy radius-szal egy `GeoJSON Feature` objektum:

```json            
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}          
```

[1]: https://tools.ietf.org/html/rfc7946
