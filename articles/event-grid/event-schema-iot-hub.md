---
title: Azure-IoT Hub Event Grid forrásként
description: Ez a cikk az Azure IoT Hub eseményeinek tulajdonságait és sémáját ismerteti. Felsorolja a rendelkezésre álló eseménytípus, a példa esemény és az esemény tulajdonságait.
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 5f43b9d0041fa5842bc2557a61c5145ce588758a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100363526"
---
# <a name="azure-iot-hub-as-an-event-grid-source"></a>Azure-IoT Hub Event Grid forrásként
Ez a cikk az Azure IoT Hub eseményeinek tulajdonságait és sémáját ismerteti. Az események sémáinak bemutatása: [Azure Event Grid Event Schema](event-schema.md). 

## <a name="available-event-types"></a>Elérhető események típusai

Az Azure IoT Hub a következő típusú eseményeket bocsátja ki:

| Eseménytípus | Leírás |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Közzétett, ha egy eszköz regisztrálva van egy IoT-hubhoz. |
| Microsoft.Devices.DeviceDeleted | Közzétételre kerül, ha egy eszközt törölnek egy IoT-hubhoz. | 
| Microsoft.Devices.DeviceConnected | Akkor jelenik meg, amikor egy eszköz IoT-hubhoz csatlakozik. |
| Microsoft.Devices.DeviceDisconnected | Akkor jelenik meg, ha egy eszköz le van választva egy IoT hubhoz. | 
| Microsoft.Devices.DeviceTelemetry | Közzétételre kerül, amikor egy telemetria üzenetet küld egy IoT hub-nak. |

## <a name="example-event"></a>Példa eseményre

# <a name="event-grid-event-schema"></a>[Event Grid-eseményséma](#tab/event-grid-event-schema)

A DeviceConnected-és DeviceDisconnected-események sémája ugyanazzal a szerkezettel rendelkezik. Ez a példa egy olyan esemény sémáját mutatja be, amely akkor következik be, amikor egy eszköz egy IoT hubhoz csatlakozik:

```json
[{
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8", 
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceConnected", 
  "eventTime": "2018-06-02T19:17:44.4383997Z", 
  "data": {
    "deviceConnectionStateEventInfo": {
      "sequenceNumber":
        "000000000000000001D4132452F67CE200000002000000000000000000000001"
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID"
  }, 
  "dataVersion": "1", 
  "metadataVersion": "1" 
}]
```

A DeviceTelemetry esemény akkor következik be, amikor telemetria eseményt küld egy IoT Hub. Az eseményhez tartozó minta séma alább látható.

```json
[{
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {        
      "body": {            
          "Weather": {                
              "Temperature": 900            
          },
          "Location": "USA"        
      },
        "properties": {            
          "Status": "Active"        
        },
        "systemProperties": {            
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "d1",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "123455432199234570",
            "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
            "iothub-message-source": "Telemetry"        
        }    
    },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

A DeviceCreated-és DeviceDeleted-események sémája ugyanazzal a szerkezettel rendelkezik. Ez a példa egy olyan esemény sémáját mutatja be, amely akkor következik be, amikor egy eszköz regisztrálva van egy IoT-hubhoz:

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

# <a name="cloud-event-schema"></a>[Felhő-eseményséma](#tab/cloud-event-schema)

A DeviceConnected-és DeviceDisconnected-események sémája ugyanazzal a szerkezettel rendelkezik. Ez a példa egy olyan esemény sémáját mutatja be, amely akkor következik be, amikor egy eszköz egy IoT hubhoz csatlakozik:

```json
[{
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8", 
  "source": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "type": "Microsoft.Devices.DeviceConnected", 
  "time": "2018-06-02T19:17:44.4383997Z", 
  "data": {
    "deviceConnectionStateEventInfo": {
      "sequenceNumber":
        "000000000000000001D4132452F67CE200000002000000000000000000000001"
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID"
  }, 
  "specversion": "1.0"
}]
```

A DeviceTelemetry esemény akkor következik be, amikor telemetria eseményt küld egy IoT Hub. Az eseményhez tartozó minta séma alább látható.

```json
[{
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "source": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "type": "Microsoft.Devices.DeviceTelemetry",
  "time": "2019-01-07T20:58:30.48Z",
  "data": {        
      "body": {            
          "Weather": {                
              "Temperature": 900            
          },
          "Location": "USA"        
      },
        "properties": {            
          "Status": "Active"        
        },
        "systemProperties": {            
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "d1",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "123455432199234570",
            "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
            "iothub-message-source": "Telemetry"        
        }    
    },
  "specversion": "1.0"
}]
```

A DeviceCreated-és DeviceDeleted-események sémája ugyanazzal a szerkezettel rendelkezik. Ez a példa egy olyan esemény sémáját mutatja be, amely akkor következik be, amikor egy eszköz regisztrálva van egy IoT-hubhoz:

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "source": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "type": "Microsoft.Devices.DeviceCreated",
  "time": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "specversion": "1.0"
}]
```

---


### <a name="event-properties"></a>Esemény tulajdonságai

# <a name="event-grid-event-schema"></a>[Event Grid-eseményséma](#tab/event-grid-event-schema)

Minden esemény ugyanazt a legfelső szintű adatértéket tartalmazza: 

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| `id` | sztring | Az esemény egyedi azonosítója. |
| `topic` | sztring | Az eseményforrás teljes erőforrás-elérési útja. Ez a mező nem írható. Az értéket az Event Grid adja meg. |
| `subject` | sztring | Az esemény tárgyra mutató, a közzétevő által megadott elérési út. |
| `eventType` | sztring | Az eseményforráshoz felvett eseménytípusok egyike. |
| `eventTime` | sztring | Az esemény a szolgáltató UTC-ideje alapján történő létrehozásakor. |
| `data` | object | IoT Hub az eseményekre vonatkozó adatgyűjtést.  |
| `dataVersion` | sztring | Az adatobjektum sémaverziója. A sémaverziót a közzétevő határozza meg. |
| `metadataVersion` | sztring | Az esemény metaadatok sémaverziója. A legfelső szintű tulajdonságokra az Event Grid határozza meg a sémát. Az értéket az Event Grid adja meg. |

# <a name="cloud-event-schema"></a>[Felhő-eseményséma](#tab/cloud-event-schema)

Minden esemény ugyanazt a legfelső szintű adatértéket tartalmazza: 


| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| `id` | sztring | Az esemény egyedi azonosítója. |
| `source` | sztring | Az eseményforrás teljes erőforrás-elérési útja. Ez a mező nem írható. Az értéket az Event Grid adja meg. |
| `subject` | sztring | Az esemény tárgyra mutató, a közzétevő által megadott elérési út. |
| `type` | sztring | Az eseményforráshoz felvett eseménytípusok egyike. |
| `time` | sztring | Az esemény a szolgáltató UTC-ideje alapján történő létrehozásakor. |
| `data` | object | IoT Hub az eseményekre vonatkozó adatgyűjtést.  |
| `specversion` | sztring | A CloudEvents séma specifikációjának verziója. |

---

Az adatobjektum minden IoT Hub eseményhez a következő tulajdonságokat tartalmazza:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| `hubName` | sztring | Azon IoT Hub neve, ahová az eszközt létrehozták vagy törölték. |
| `deviceId` | sztring | Az eszköz egyedi azonosítója. Ez a kis-és nagybetűket megkülönböztető karakterlánc legfeljebb 128 karakter hosszúságú lehet, és támogatja az ASCII 7 bites alfanumerikus karaktereket, valamint a következő speciális karaktereket: `- : . + % _ # * ? ! ( ) , = @ ; $ '` . |

Az adatobjektum tartalma eltér az egyes esemény-közzétevők esetében. 

A **csatlakoztatott eszköz** és az **eszköz leválasztott** IoT hub eseményeihez az adatobjektum a következő tulajdonságokat tartalmazza:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| `moduleId` | sztring | A modul egyedi azonosítója. Ez a mező csak modul-eszközök esetén kimenet. Ez a kis-és nagybetűket megkülönböztető karakterlánc legfeljebb 128 karakter hosszúságú lehet, és támogatja az ASCII 7 bites alfanumerikus karaktereket, valamint a következő speciális karaktereket: `- : . + % _ # * ? ! ( ) , = @ ; $ '` . |
| `deviceConnectionStateEventInfo` | object | Az eszköz kapcsolati állapotával kapcsolatos események adatai
| `sequenceNumber` | sztring | Egy szám, amely segít jelezni az eszköz csatlakoztatott vagy leválasztott eseményeinek sorrendjét. A legutóbbi eseménynél az előző eseménynél nagyobb sorszám szerepel. Ez a szám több mint 1, de szigorúan növekszik. Lásd: [a sorozatszám használata](../iot-hub/iot-hub-how-to-order-connection-state-events.md). |

Az **eszköz telemetria** IoT hub esemény esetén az adatobjektum tartalmazza az eszközről a felhőbe irányuló üzenetet az [IoT hub üzenet formátumában](../iot-hub/iot-hub-devguide-messages-construct.md) , és a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| `body` | sztring | Az üzenet tartalma az eszközről. |
| `properties` | sztring | Az alkalmazás tulajdonságai olyan felhasználó által definiált karakterláncok, amelyek hozzáadhatók az üzenethez. Ezeket a mezőket nem kötelező megadni. |
| `system properties` | sztring | A [rendszer tulajdonságai](../iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties) segítenek az üzenetek tartalmának és forrásának azonosításában. Az eszköz telemetria érvényes JSON-formátumúnak kell lennie, és a contentType JSON-ra, a contentEncoding pedig UTF-8 értékre kell állítani az üzenetrendszer tulajdonságaiban. Ha a beállítás nincs megadva, akkor a IoT Hub a 64 kódolású formátumban fogja írni az üzeneteket.  |

Az **eszköz által létrehozott** és az **eszköz törölte** IoT hub eseményeket, az adatobjektum a következő tulajdonságokat tartalmazza:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| `twin` | object | A Twin eszközre vonatkozó információ, amely az alkalmazás-eszköz metaadatainak Felhőbeli ábrázolása. | 
| `deviceID` | sztring | Az eszköz egyedi azonosítója. | 
| `etag` | sztring | Egy érvényesítő, amely biztosítja, hogy a frissítések konzisztensek legyenek egy adott eszközön. Minden ETAG egyedinek kell lennie eszközönként. |  
| `deviceEtag` | sztring | Egy érvényesítő, amely biztosítja a frissítések egységességét az eszköz beállításjegyzékében. Minden deviceEtag egyedinek kell lennie az eszközönkénti beállításjegyzékben. |
| `status` | sztring | Azt jelzi, hogy a Twin eszköz engedélyezve van-e vagy le van tiltva. | 
| `statusUpdateTime` | sztring | Az utolsó eszköz kettős állapotának ISO8601 időbélyegzője. |
| `connectionState` | sztring | Azt jelzi, hogy az eszköz csatlakoztatva van-e, vagy le van választva. | 
| `lastActivityTime` | sztring | Az utolsó tevékenység ISO8601 időbélyegzője. | 
| `cloudToDeviceMessageCount` | egész szám | Az eszközre küldött Felhőbeli üzenetek száma. | 
| `authenticationType` | sztring | Az eszközhöz használt hitelesítési típus: vagy `SAS` , `SelfSigned` , vagy `CertificateAuthority` . |
| `x509Thumbprint` | sztring | Az ujjlenyomat a x509 tanúsítvány egyedi értéke, amely általában egy adott tanúsítvány megkeresésére szolgál a tanúsítványtárolóban. Az ujjlenyomatot a rendszer dinamikusan hozza létre az SHA1 algoritmus használatával, és fizikailag nem létezik a tanúsítványban. | 
| `primaryThumbprint` | sztring | Az x509-tanúsítvány elsődleges ujjlenyomata. |
| `secondaryThumbprint` | sztring | A x509-tanúsítvány másodlagos ujjlenyomata. | 
| `version` | egész szám | Egy egész szám, amely eggyel nő, amikor az eszköz külön frissül. |
| `desired` | object | A tulajdonságok egy része, amely csak az alkalmazás hátterében írható, és az eszköz beolvassa. | 
| `reported` | object | A tulajdonságok egy része, amelyet csak az eszköz írhat, és az alkalmazás háttérbe is beolvashatja. |
| `lastUpdated` | sztring | Az utolsó eszköz Twin tulajdonságának ISO8601 időbélyegzője. | 

## <a name="tutorials-and-how-tos"></a>Oktatóanyagok és útmutatók
|Cím  |Leírás  |
|---------|---------|
| [Azure IoT Hub-eseményekkel kapcsolatos e-mail-értesítések küldése a Logic Apps használatával](publish-iot-hub-events-to-logic-apps.md) | A logikai alkalmazások minden alkalommal elküldenek egy értesítő e-mailt, amikor egy eszköz bekerül a IoT Hubba. |
| [IoT Hub eseményekre való reagálás Event Grid használatával a műveletek elindításához](../iot-hub/iot-hub-event-grid.md) | A IoT Hub és a Event Grid integrálásának áttekintése. |
| [Eszköz csatlakoztatott és leválasztott eseményeinek megrendelése](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | Az eszköz kapcsolódási állapotával kapcsolatos események megrendelésének módját mutatja be. |

## <a name="next-steps"></a>Következő lépések

* A Azure Event Grid bemutatása: [Mi az Event Grid?](overview.md)
* Ha többet szeretne megtudni a IoT Hub és a Event Grid együttműködéséről, olvassa el a következő témakört: [reagálás a IoT hub eseményekre Event Grid használatával a műveletek elindításához](../iot-hub/iot-hub-event-grid.md).