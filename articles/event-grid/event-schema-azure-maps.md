---
title: Azure Maps Event Grid forrásként
description: A Azure Maps eseményekhez megadott tulajdonságokat és sémát ismerteti Azure Event Grid
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 88cf0c8274d685a45862bc7b7884b5e4a686c22d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100363679"
---
# <a name="azure-maps-as-an-event-grid-source"></a>Azure Maps Event Grid forrásként

Ez a cikk a Azure Maps eseményeinek tulajdonságait és sémáját ismerteti. Az események sémáinak bemutatása: [Azure Event Grid Event Schema](./event-schema.md). Emellett a gyors indulások és oktatóanyagok listáját is tartalmazza, amelyekkel Azure Maps lehet használni az esemény forrásaként.

## <a name="available-event-types"></a>Elérhető események típusai

Egy Azure Maps fiók a következő típusú eseményeket bocsátja ki:

| Eseménytípus | Leírás |
| ---------- | ----------- |
| Microsoft. maps. GeofenceEntered | Akkor következik be, amikor a kapott koordináták egy adott geokerítésen kívülre kerültek a következőn belül: |
| Microsoft. maps. GeofenceExited | Akkor következik be, amikor a kapott koordináták egy adott geokerítésen kívülre kerültek |
| Microsoft. maps. GeofenceResult | Minden alkalommal, amikor egy geokerítések-lekérdezés eredményt ad vissza, az állapottól függetlenül |

## <a name="example-events"></a>Példa események

# <a name="event-grid-event-schema"></a>[Event Grid-eseményséma](#tab/event-grid-event-schema)
Az alábbi példa egy **GeofenceEntered** -esemény sémáját mutatja be.

```JSON
{   
   "id":"7f8446e2-1ac7-4234-8425-303726ea3981", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":-999.0, 
            "nearestLat":47.618786, 
            "nearestLon":-122.132151 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceEntered", 
   "eventTime":"2018-11-08T00:54:17.6408601Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

Az alábbi példa a **GeofenceResult** sémáját mutatja be 

```JSON
{   
   "id":"451675de-a67d-4929-876c-5c2bf0b2c000", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"1", 
            "distance":999.0, 
            "nearestLat":47.609833, 
            "nearestLon":-122.148274 
         }, 
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":999.0, 
            "nearestLat":47.621954, 
            "nearestLon":-122.131841 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceResult", 
   "eventTime":"2018-11-08T00:52:08.0954283Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

# <a name="cloud-event-schema"></a>[Felhő-eseményséma](#tab/cloud-event-schema)
Az alábbi példa egy **GeofenceEntered** -esemény sémáját mutatja be.

```JSON
{   
   "id":"7f8446e2-1ac7-4234-8425-303726ea3981", 
   "source":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":-999.0, 
            "nearestLat":47.618786, 
            "nearestLon":-122.132151 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "type":"Microsoft.Maps.GeofenceEntered", 
   "time":"2018-11-08T00:54:17.6408601Z", 
   "specversion":"1.0" 
}
```

Az alábbi példa a **GeofenceResult** sémáját mutatja be 

```JSON
{   
   "id":"451675de-a67d-4929-876c-5c2bf0b2c000", 
   "source":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"1", 
            "distance":999.0, 
            "nearestLat":47.609833, 
            "nearestLon":-122.148274 
         }, 
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":999.0, 
            "nearestLat":47.621954, 
            "nearestLon":-122.131841 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "type":"Microsoft.Maps.GeofenceResult", 
   "time":"2018-11-08T00:52:08.0954283Z", 
   "specversion":"1.0" 
}
```
---

## <a name="event-properties"></a>Esemény tulajdonságai

# <a name="event-grid-event-schema"></a>[Event Grid-eseményséma](#tab/event-grid-event-schema)
Egy esemény a következő legfelső szintű adattal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| `topic` | sztring | Az eseményforrás teljes erőforrás-elérési útja. Ez a mező nem írható. Az értéket az Event Grid adja meg. |
| `subject` | sztring | Az esemény tárgyra mutató, a közzétevő által megadott elérési út. |
| `eventType` | sztring | Az eseményforráshoz felvett eseménytípusok egyike. |
| `eventTime` | sztring | Az esemény a szolgáltató UTC-ideje alapján történő létrehozásakor. |
| `id` | sztring | Az esemény egyedi azonosítója. |
| `data` | object | Geokerítések. |
| `dataVersion` | sztring | Az adatobjektum sémaverziója. A sémaverziót a közzétevő határozza meg. |
| `metadataVersion` | sztring | Az esemény metaadatok sémaverziója. A legfelső szintű tulajdonságokra az Event Grid határozza meg a sémát. Az értéket az Event Grid adja meg. |

# <a name="cloud-event-schema"></a>[Felhő-eseményséma](#tab/cloud-event-schema)
Egy esemény a következő legfelső szintű adattal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| `source` | sztring | Az eseményforrás teljes erőforrás-elérési útja. Ez a mező nem írható. Az értéket az Event Grid adja meg. |
| `subject` | sztring | Az esemény tárgyra mutató, a közzétevő által megadott elérési út. |
| `type` | sztring | Az eseményforráshoz felvett eseménytípusok egyike. |
| `time` | sztring | Az esemény a szolgáltató UTC-ideje alapján történő létrehozásakor. |
| `id` | sztring | Az esemény egyedi azonosítója. |
| `data` | object | Geokerítések. |
| `specversion` | sztring | A CloudEvents séma specifikációjának verziója. |

---

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| `apiCategory` | sztring | Az esemény API-kategóriája. |
| `apiName` | sztring | Az esemény API-neve. |
| `issues` | object | A feldolgozás során felmerülő problémák felsorolása. Ha bármilyen hibát ad vissza, akkor a válaszban nem lesznek visszaadott geometriák. |
| `responseCode` | szám | HTTP-válasz kódja |
| `geometries` | object | Felsorolja a koordináta pozícióját tartalmazó kerítési geometriákat, vagy átfedésben van a searchBuffer a pozíció körül. |

A rendszer visszaadja a hiba objektumot, ha hiba történik a Maps API-ban. A hiba objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| `error` | ErrorDetails |Ezt az objektumot akkor adja vissza a rendszer, ha hiba történik a Maps API-ban  |

A ErrorDetails objektum visszaadása akkor történik meg, ha hiba lép fel a Maps API-ban. A ErrorDetails vagy objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| `code` | sztring | A HTTP-állapotkód. |
| `message` | sztring | Ha elérhető, a hiba olvasható leírása. |
| `innererror` | InnerError | Ha elérhető, a hibával kapcsolatos szolgáltatás-specifikus adatokat tartalmazó objektum. |

A InnerError egy olyan objektum, amely szolgáltatás-specifikus adatokat tartalmaz a hibáról. A InnerError objektum a következő tulajdonságokkal rendelkezik: 

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| `code` | sztring | A hibaüzenet. |

A geometriák objektum felsorolja a kérelemben a felhasználói időponthoz képest lejárt geofences geometriai azonosítóit. A geometriák objektum a következő tulajdonságokkal rendelkező geometriai elemeket tartalmaz: 

| Tulajdonság | Típus | Description |
|:-------- |:---- |:----------- |
| `deviceid` | sztring | Az eszköz azonosítója. |
| `distance` | sztring | <p>A koordináta távolsága a geokerítésen legközelebbi szegélyéhez. A pozitív érték azt jelenti, hogy a koordináta a geokerítésen kívül esik. Ha a koordináta a geokerítésen kívül esik, de a legközelebb geokerítésen searchBuffer nagyobb a távolság, akkor az érték 999. Negatív érték azt jelenti, hogy a koordináta a geokerítésen belül van. Ha a koordináta a sokszögen belül van, de a legközelebbi geokerítések searchBuffer nem, akkor az érték-999. A 999 érték azt jelenti, hogy nagy a megbízhatóság, hogy a koordináta jól kívül van a geokerítésen. A-999 érték azt jelenti, hogy nagy a megbízhatóság, hogy a koordináta jól működik a geokerítésen belül.<p> |
| `geometryid` |sztring | Az egyedi azonosító azonosítja a geokerítésen geometriát. |
| `nearestlat` | szám | A geometria legközelebbi pontjának földrajzi szélessége |
| `nearestlon` | szám | A geometria legközelebbi pontjának hosszúsága |
| `udId` | sztring | A felhasználói feltöltési szolgáltatás által visszaadott egyedi azonosító a geokerítésen feltöltésekor. Nem szerepel a geokerítések post API-ban. |

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| `expiredGeofenceGeometryId` | karakterlánc [] | Azon geokerítésen geometria-AZONOSÍTÓinak listája, amelyek a kérelemben megadott felhasználói időhöz képest lejárnak. |
| `geometries` | geometriák [] |Felsorolja a koordináta pozícióját tartalmazó kerítési geometriákat, vagy átfedésben van a searchBuffer a pozíció körül. |
| `invalidPeriodGeofenceGeometryId` | karakterlánc []  | A kérelemben szereplő felhasználói időhöz képest érvénytelen időszakra vonatkozó geokerítésen geometriai AZONOSÍTÓjának listája. |
| `isEventPublished` | boolean | Igaz, ha legalább egy eseményt közzétesznek a Azure Maps esemény előfizetője számára, hamis értéket, ha egyetlen eseményt sem tesznek közzé a Azure Maps esemény előfizetője számára. |

## <a name="tutorials-and-how-tos"></a>Oktatóanyagok és útmutatók
|Cím  |Leírás  |
|---------|---------|
| [Azure Maps eseményekre való reagálás Event Grid használatával](../azure-maps/azure-maps-event-grid-integration.md?toc=%2fazure%2fevent-grid%2ftoc.json) | A Azure Maps és a Event Grid integrálásának áttekintése. |
| [Oktatóanyag: geokerítésen beállítása](../azure-maps/tutorial-geofence.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Ez az oktatóanyag végigvezeti a geokerítésen a Azure Maps használatával történő beállításához szükséges alapismereteken. Azure Event Grid használatával továbbíthatja a geokerítésen eredményeit, és beállíthat egy értesítést a geokerítésen eredményei alapján. |

## <a name="next-steps"></a>Következő lépések

* A Azure Event Grid bemutatása: [Mi az Event Grid?](overview.md)
* Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid előfizetés sémája](subscription-creation-schema.md).
