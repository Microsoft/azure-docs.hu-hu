---
title: Eseményadatok értelmezése
titleSuffix: Azure Digital Twins
description: 'Lásd: a különböző eseménytípus értelmezése és a különböző értesítési üzenetek.'
author: baanders
ms.author: baanders
ms.date: 6/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 1901104aa05b4e7ea3a318ee8e886c745f2a6eb4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105936044"
---
# <a name="understand-event-data"></a>Az események értelmezése

Az Azure Digital Twins különböző eseményeinek **értesítéseket** hoznak létre, amelyek lehetővé teszik a megoldási háttér használatát, ha különböző műveletek történnek. Ezeket a rendszer az Azure digitális Ikreken belüli és azon kívüli különböző helyszínekre [irányítja](concepts-route-events.md) át, amelyek felhasználhatják ezeket az információkat a művelet elvégzésére.

Számos típusú értesítés hozható létre, és az értesítési üzenetek eltérőek lehetnek attól függően, hogy milyen típusú eseményt generáltak. Ez a cikk részletesen ismerteti a különböző típusú üzeneteket, és azok megjelenését.

Ez a diagram a különböző értesítési típusokat mutatja:

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

## <a name="notification-structure"></a>Értesítési struktúra

Az értesítések általában két részből állnak: a fejlécből és a törzsből. 

### <a name="event-notification-headers"></a>Esemény-értesítési fejlécek

Az értesítési üzenetek fejléce a kulcs-érték párokkal együtt jelenik meg. A használt protokolltól (MQTT, AMQP vagy HTTP) függően az üzenetek fejlécei másképp szerializálva lesznek. Ez a szakasz az értesítési üzenetek általános fejléc-információit ismerteti, a választott protokolltól és szerializálástól függetlenül.

Egyes értesítések megfelelnek az [CloudEvents](https://cloudevents.io/) szabványnak. A CloudEvents-megfelelőség a következő.
* Az eszközökről kibocsátott értesítések továbbra is követik az értesítések meglévő specifikációit
* Az IoT Hub által feldolgozott és kibocsátott értesítések továbbra is követik az értesítés meglévő specifikációit, kivéve, ha IoT Hub úgy dönt, hogy támogatja a CloudEvents, például: Event Grid
* A [digitális ikrek](concepts-twins-graph.md) által kibocsátott, a [modellel](concepts-models.md) megegyező CloudEvents-értesítések
* Az Azure Digital Twins által feldolgozott és kibocsátott értesítések megfelelnek a CloudEvents

A szolgáltatásoknak sorozatszámot kell hozzáadniuk az összes értesítéshez, hogy megadják a sorrendjét, vagy más módon megtartsák a saját rendelését. 

Az Azure Digital Twins által kibocsátott értesítések Event Grid automatikusan a CloudEvents séma vagy a EventGridEvent sémára lesznek formázva az Event Grid témakörben definiált séma típusától függően. 

A fejlécek bővítmény-attribútumai a Event Grid séma tulajdonságaiként lesznek hozzáadva a hasznos adatokhoz. 

### <a name="event-notification-bodies"></a>Esemény-értesítési testületek

Az értesítési üzenetek törzseit itt találja a JSON-ban. Az üzenet törzséhez (például JSON, CBOR, Protopuf stb.) való szerializálástól függően előfordulhat, hogy az üzenet törzse másképp szerializálható.

A törzs által tartalmazott mezők halmaza eltérő értesítési típusokkal rendelkezik. Íme két példa az üzenet törzsére, hogy megtudja, mit láthatnak általában, és hogyan tartalmazhatnak.

Telemetria-üzenet:

```json
{
  "specversion": "1.0",
  "id": "df5a5992-817b-4e8a-b12c-e0b18d4bf8fb",
  "type": "microsoft.iot.telemetry",
  "source": "contoso-adt.api.wus2.digitaltwins.azure.net/digitaltwins/room1",
  "data": {
    "Temperature": 10
  },
  "dataschema": "dtmi:example:com:floor4;2",
  "datacontenttype": "application/json",
  "traceparent": "00-7e3081c6d3edfb4eaf7d3244b2036baa-23d762f4d9f81741-01"
}
```

Életciklus-értesítési üzenet:

```json
{
  "specversion": "1.0",
  "id": "d047e992-dddc-4a5a-b0af-fa79832235f8",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "contoso-adt.api.wus2.digitaltwins.azure.net",
  "data": {
    "$dtId": "floor1",
    "$etag": "W/\"e398dbf4-8214-4483-9d52-880b61e491ec\"",
    "$metadata": {
      "$model": "dtmi:example:Floor;1"
    }
  },
  "subject": "floor1",
  "time": "2020-06-23T19:03:48.9700792Z",
  "datacontenttype": "application/json",
  "traceparent": "00-18f4e34b3e4a784aadf5913917537e7d-691a71e0a220d642-01"
}
```

A következő részek részletesebben ismertetik a IoT Hub és az Azure Digital Twins (vagy más Azure IoT-szolgáltatások) által kibocsátott különböző típusú értesítéseket. Itt olvashat az egyes értesítési típusokat kiváltó dolgokról, valamint az egyes típusú értesítési törzsekhez tartozó mezők készletéről.

## <a name="digital-twin-change-notifications"></a>Digitális kettős változások értesítései

A digitális **kettős változásokról szóló értesítéseket** a rendszer akkor indítja el, ha a digitális iker frissítése folyamatban van, például:
* A tulajdonságértékek vagy a metaadatok módosításakor.
* Ha a digitális iker-vagy összetevő-metaadatok módosulnak. Ebben a forgatókönyvben egy példa a digitális iker modelljét változtatja meg.

### <a name="properties"></a>Tulajdonságok

Itt láthatók a digitális kettős változásokról szóló értesítés törzsének mezői.

| Name    | Érték |
| --- | --- |
| `id` | Az értesítés azonosítója, például egy UUID vagy a szolgáltatás által karbantartott számláló. `source` + `id` egyedi a különböző eseményekhez |
| `source` | Az IoT hub vagy az Azure Digital Twins-példány neve, például *myhub.Azure-Devices.net* vagy *mydigitaltwins.westus2.azuredigitaltwins.net*
| `specversion` | *1.0*<br>Az üzenet megfelel a [CloudEvents-specifikáció](https://github.com/cloudevents/spec)ezen verziójának. |
| `type` | `Microsoft.DigitalTwins.Twin.Update` |
| `datacontenttype` | `application/json` |
| `subject` | A digitális Twin azonosító |
| `time` | Időbélyeg a digitális Twin művelet bekövetkeztekor |
| `traceparent` | Az esemény W3C-nyomkövetési kontextusa |

### <a name="body-details"></a>Törzs részletei

Az értesítés törzse `Twin.Update` egy JSON-javítási dokumentum, amely a digitális dupla frissítést tartalmazza.

Tegyük fel például, hogy a digitális iker frissítése a következő javítás használatával történt.

:::code language="json" source="~/digital-twins-docs-samples/models/patch-component-2.json":::

A megfelelő értesítés (ha a szolgáltatás szinkron módon hajtja végre, mint például az Azure digitális ikrek frissítése egy digitális Twin-et), a következőhöz hasonló szervnek kell lennie:

```json
{
    "modelId": "dtmi:example:com:floor4;2",
    "patch": [
      {
        "value": 40,
        "path": "/Temperature",
        "op": "replace"
      },
      {
        "value": 30,
        "path": "/comp1/prop1",
        "op": "add"
      }
    ]
  }
```

## <a name="digital-twin-lifecycle-notifications"></a>Digitális kettős életciklussal kapcsolatos értesítések

Minden [digitális ikrek](concepts-twins-graph.md) értesítéseket bocsát ki, függetlenül attól, hogy [IoT hub-eszközöket képviselnek-e az Azure Digital ikrekben](how-to-ingest-iot-hub-data.md) vagy sem. Ez az életciklus- **értesítések**, amelyek a digitális Twin-re vonatkoznak.

Az életciklus-értesítések a következők esetén aktiválódnak:
* Létrejön egy digitális Twin
* Egy digitális Twin törlésre kerül

### <a name="properties"></a>Tulajdonságok

Az életciklus-értesítés törzsének mezői.

| Name | Érték |
| --- | --- |
| `id` | Az értesítés azonosítója, például egy UUID vagy a szolgáltatás által karbantartott számláló. `source` + `id` minden különböző esemény esetében egyedi. |
| `source` | Az IoT hub vagy az Azure Digital Twins-példány neve, például *myhub.Azure-Devices.net* vagy *mydigitaltwins.westus2.azuredigitaltwins.net* |
| `specversion` | *1.0*<br>Az üzenet megfelel a [CloudEvents-specifikáció](https://github.com/cloudevents/spec)ezen verziójának. |
| `type` | `Microsoft.DigitalTwins.Twin.Create`<br>`Microsoft.DigitalTwins.Twin.Delete` |
| `datacontenttype` | `application/json` |
| `subject` | A digitális Twin azonosító |
| `time` | Időbélyeg, amikor a művelet bekövetkezett a Twin |
| `traceparent` | Az esemény W3C-nyomkövetési kontextusa |

### <a name="body-details"></a>Törzs részletei

A törzs az érintett digitális Twin, amely JSON formátumban van megjelenítve. A séma ehhez a *digitális Twins-erőforrás 7,1*.

A létrehozási események esetében a hasznos adatok az erőforrás létrehozása után a Twin állapotot tükrözik, így az összes rendszer által generált elemnek kell szerepelnie, mint a `GET` hívás.

Íme egy példa egy [IoT Plug and Play (PNP)](../iot-pnp/overview-iot-plug-and-play.md) eszközre, amely összetevőkkel és nem legfelső szintű tulajdonságokkal rendelkezik. Az eszközökre (például a jelentett tulajdonságokra) nem jellemző tulajdonságokat el kell hagyni.

```json
{
  "$dtId": "device-digitaltwin-01",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "thermostat": {
    "temperature": 80,
    "humidity": 45,
    "$metadata": {
      "$model": "dtmi:com:contoso:Thermostat;1",
      "temperature": {
        "desiredValue": 85,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      },
      "humidity": {
        "desiredValue": 40,
        "desiredVersion": 1,
        "ackVersion": 1,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Thermostat_X500;1",
  }
}
```

Íme egy másik példa a digitális Twin-re. Ez egy [modellen](concepts-models.md)alapul, és nem támogatja az összetevőket:

```json
{
  "$dtId": "logical-digitaltwin-01",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "avgTemperature": 70,
  "comfortIndex": 85,
  "$metadata": {
    "$model": "dtmi:com:contoso:Building;1",
    "avgTemperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "comfortIndex": {
      "desiredValue": 90,
      "desiredVersion": 1,
      "ackVersion": 3,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

## <a name="digital-twin-relationship-change-notifications"></a>Digitális kettős kapcsolat változási értesítései

A **kapcsolat változásával kapcsolatos értesítések** akkor aktiválódnak, ha a digitális kettős kapcsolat létrejött, frissítve vagy törölve lett. 

### <a name="properties"></a>Tulajdonságok

Itt láthatók az Edge Change-értesítések törzsének mezői.

| Name    | Érték |
| --- | --- |
| `id` | Az értesítés azonosítója, például egy UUID vagy a szolgáltatás által karbantartott számláló. `source` + `id` egyedi a különböző eseményekhez |
| `source` | Az Azure Digital Twins-példány neve, például *mydigitaltwins.westus2.azuredigitaltwins.net* |
| `specversion` | *1.0*<br>Az üzenet megfelel a [CloudEvents-specifikáció](https://github.com/cloudevents/spec)ezen verziójának. |
| `type` | `Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br>`Microsoft.DigitalTwins.Relationship.Delete`
|`datacontenttype`| `application/json` |
| `subject` | A kapcsolat azonosítója, például: `<twinID>/relationships/<relationshipID>` |
| `time` | Időbélyeg, hogy mikor történt a művelet a kapcsolaton |
| `traceparent` | Az esemény W3C-nyomkövetési kontextusa |

### <a name="body-details"></a>Törzs részletei

A törzs a kapcsolat hasznos adatai JSON formátumban is. Ugyanazt a formátumot használja, mint egy `GET` kapcsolat kérése a [DigitalTwins API](/rest/api/digital-twins/dataplane/twins)-n keresztül. 

A "kapcsolat frissítése" érték azt jelenti, hogy a kapcsolat tulajdonságai megváltoztak. 

Íme egy példa egy frissítési kapcsolati értesítésre, amely frissíti a tulajdonságot:

```json
{
    "modelId": "dtmi:example:Floor;1",
    "patch": [
      {
        "value": "user3",
        "path": "/ownershipUser",
        "op": "replace"
      }
    ]
  }
```

A esetében `Relationship.Delete` a törzs megegyezik a `GET` kérelemmel, és a törlés előtt lekéri a legutóbbi állapotot.

Íme egy példa egy Create vagy DELETE kapcsolati értesítésre:

```json
{
    "$relationshipId": "device_to_device",
    "$etag": "W/\"72479873-0083-41a8-83e2-caedb932d881\"",
    "$relationshipName": "Connected",
    "$targetId": "device2",
    "connectionType": "WIFI"
}
```

## <a name="next-steps"></a>Következő lépések

Tekintse meg, hogyan hozhatók létre végpontok és útvonalak az események kézbesítéséhez:
* [*Útmutató: végpontok és útvonalak kezelése*](how-to-manage-routes-apis-cli.md)

Vagy további információ az Azure Digital Twins API-król és az SDK lehetőségeiről:
* [*Útmutató: az Azure Digital Twins API-k és SDK-k használata*](how-to-use-apis-sdks.md)