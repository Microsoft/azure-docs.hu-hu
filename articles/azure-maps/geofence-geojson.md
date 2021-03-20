---
title: GeoJSON adatformátuma geokerítésen | Microsoft Azure térképek
description: 'Ismerkedjen meg a Azure Maps geokerítésen-információkkal. Lásd: a Geokerítésen beolvasása és a Geokerítésen API-k használata a koordinátáknak a geokerítésen viszonyított pozíciójának beolvasásakor.'
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: e880710b93a6764df50780e685c89b5f569b4ec0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92897194"
---
# <a name="geofencing-geojson-data"></a>Geokerítések GeoJSON-adatbázis

A Azure Maps [Get geokerítésen](/rest/api/maps/spatial/getgeofence) és [post geokerítésen](/rest/api/maps/spatial/postgeofence) API-k lehetővé teszik egy koordináta közelségének lekérését egy megadott geokerítésen vagy kerítések készletéhez képest. Ez a cikk részletesen ismerteti a Azure Maps GET és POST API-ban felhasználható geokerítésen-adatok előkészítését.

A geokerítésen vagy geofences vonatkozó adatmennyiséget `Feature` objektum és objektum jelképezi `FeatureCollection` `GeoJSON` , formátum szerint, amely a [rfc7946](https://tools.ietf.org/html/rfc7946)-ben van meghatározva. Emellett:

* A GeoJSON objektum típusa lehet `Feature` objektum vagy `FeatureCollection` objektum.
* A geometria objektum típusa lehet a,,,,, `Point` `MultiPoint` `LineString` `MultiLineString` `Polygon` `MultiPolygon` és `GeometryCollection` .
* A szolgáltatás összes tulajdonságának tartalmaznia kell a `geometryId` -t, amely a geokerítésen azonosítására szolgál.
* A-ben a, a, `Point` `MultiPoint` `LineString` `MultiLineString` `radius` a tulajdonságban szerepelnie kell a szolgáltatásnak. `radius` az érték mérése méterben történik, az `radius` érték 1-től 10000-ig terjed.
* A `polygon` és a `multipolygon` geometria típusú szolgáltatáshoz nem tartozik RADIUS-tulajdonság.
* `validityTime` a egy nem kötelező tulajdonság, amely lehetővé teszi a felhasználó számára a geokerítésen-adatmennyiség érvényességi időtartamának és érvényességi idejének beállítását. Ha nincs megadva, az adatértékek soha nem járnak le, és mindig érvényesek.
* A a `expiredTime` geokerítések-adatok lejárati dátuma és időpontja. Ha a kérelemben szereplő érték későbbi ennél az `userTime` értéknél, a megfelelő geokerítésen-adatmennyiség lejárt adatként minősül, és a rendszer nem kérdezi le. Ekkor a geokerítésen-geometryId a `expiredGeofenceGeometryId` geokerítésen-válaszban található tömbbe kerül.
* A a `validityPeriod` geokerítésen érvényességi időtartamának listája. Ha a `userTime` kérelemben szereplő érték az érvényességi időtartamon kívül esik, a megfelelő geokerítésen-adatmennyiség érvénytelennek minősül, és nem lesz lekérdezve. A geokerítésen-geometryId a `invalidPeriodGeofenceGeometryId` geokerítésen-válaszon belüli tömb tartalmazza. A következő táblázat a validityPeriod elem tulajdonságait mutatja be.

| Név | Típus | Kötelező  | Leírás |
| :------------ |:------------: |:---------------:| :-----|
| startTime | Datetime  | true | Az érvényességi időtartam kezdő dátumának időpontja. |
| endTime   | Datetime  | true |  Az érvényességi időszak befejezési dátumának időpontja. |
| naptárkivételhez RecurrenceType érték | sztring | hamis |   Az időszak ismétlődési típusa Az érték lehet:,, `Daily` `Weekly` `Monthly` vagy `Yearly` . Az alapértelmezett érték `Daily`.|
| businessDayOnly | Logikai | hamis |  Jelezze, hogy az adatai csak munkanapokon érvényesek-e. Az alapértelmezett érték `false`.|


* Az összes koordináta-érték [hosszúság, szélesség] szerint van megadva a ben `WGS84` .
* Minden olyan szolgáltatás esetében, amely tartalmazza a, a, `MultiPoint` `MultiLineString` `MultiPolygon` vagy `GeometryCollection` a tulajdonságokat, a rendszer az összes elemre alkalmazza a tulajdonságokat. például: a (z) minden pontja `MultiPoint` ugyanazt a sugarat fogja használni, hogy több kör alakú geokerítésen.
* A pont-kör forgatókönyvben a kör alakú geometria egy geometriai objektum használatával ábrázolható, `Point` amely a [GeoJSON geometriák kiterjesztésével lett kibővítve](./extend-geojson.md).      

A következő példa egy minta-kérelem törzse, amely egy geokerítésen és egy sugarat használó kör alakú geokerítésen geometriát jelöl `GeoJSON` . A geokerítésen-adatok érvényes időtartama 2018-10-22, 9:00 és 17:00 között, a hétvégén kívül minden nap megismétlődik. `expiredTime` azt jelzi, hogy ez a geokerítésen-érték lejártnak minősül, ha `userTime` a kérelem későbbi, mint `2019-01-01` .  

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "geometryId" : "1",
        "subType": "Circle",
        "radius": 500,
        "validityTime": 
        {
            "expiredTime": "2019-01-01T00:00:00",
            "validityPeriod": [
                {
                    "startTime": "2018-10-22T09:00:00",
                    "endTime": "2018-10-22T17:00:00",
                    "recurrenceType": "Daily",
                    "recurrenceFrequency": 1,
                    "businessDayOnly": true
                }
            ]
        }
    }
}
```