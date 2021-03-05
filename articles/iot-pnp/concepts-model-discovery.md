---
title: IoT-Plug and Play modellek használata a megoldásban | Microsoft Docs
description: Megoldás-szerkesztőként megismerheti, hogyan használhatja a IoT Plug and Play modelleket a IoT-megoldásban.
author: arunmannengal
ms.author: arunmann
ms.date: 07/23/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 7e0d93d3fd179d672b49967c575bbfb9c0fc5de0
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102213691"
---
# <a name="use-iot-plug-and-play-models-in-an-iot-solution"></a>IoT-Plug and Play modellek használata IoT-megoldásokban

Ez a cikk azt ismerteti, hogyan lehet egy IoT-megoldásban azonosítani egy IoT Plug and Play eszköz modell-AZONOSÍTÓját, majd beolvasni a modell definícióját.

Egy IoT-megoldásnak két széles kategóriája van:

- Egy *erre a célra készült megoldás* a IoT Plug and Play eszközök ismert modelljeivel működik, amelyek a megoldáshoz fognak csatlakozni. Ezeket a modelleket a megoldás fejlesztésekor használhatja.

- A *modellen alapuló* megoldás bármilyen IoT Plug and Play eszköz modelljét képes működni. A modellen alapuló megoldás létrehozása összetettebb, de az előnye, hogy a megoldás minden olyan eszközzel működik, amelyet később is hozzáadhat. A modellen alapuló IoT-megoldások lekérik a modelleket, és az eszköz által megvalósított telemetria, tulajdonságokat és parancsokat határozzák meg.

IoT Plug and Play-modell használatához IoT-megoldás:

1. A megoldáshoz csatlakoztatott IoT Plug and Play eszköz, modul vagy IoT Edge modul által megvalósított modell AZONOSÍTÓját azonosítja.

1. A modell AZONOSÍTÓjának használatával kéri le a csatlakoztatott eszköz modell-definícióját egy modell-adattárból vagy egy egyéni tárolóból.

## <a name="identify-model-id"></a>Modell AZONOSÍTÓjának azonosítása

Amikor egy IoT Plug and Play-eszköz csatlakozik a IoT Hubhoz, regisztrálja az általa megvalósított modell AZONOSÍTÓját a IoT Hub használatával.

IoT Hub értesíti a megoldást az eszköz modell-azonosítójával az eszköz kapcsolati folyamatának részeként.

A megoldás a következő három módszer egyikének használatával kérheti le a IoT Plug and Play eszköz modell-AZONOSÍTÓját:

### <a name="get-device-twin-api"></a>Eszköz – Twin API beszerzése

A megoldás a IoT Plug and Play eszköz modell-AZONOSÍTÓjának lekéréséhez használhatja az [eszköz Twin](/java/api/com.microsoft.azure.sdk.iot.device.deviceclient.getdevicetwin) API-t.

> [!TIP]
> Modulok és IoT Edge modulok esetében használja a [ModuleClient. getTwin](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient.gettwin).

A következő eszköz Twin Response kódrészletben `modelId` a IoT Plug and Play eszköz modell-azonosítóját tartalmazza:

```json
{
    "deviceId": "sample-device",
    "etag": "AAAAAAAAAAc=",
    "deviceEtag": "NTk0ODUyODgx",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "2020-07-17T06:12:26.8402249Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "modelId": "dtmi:com:example:TemperatureController;1",
    "version": 15,
    "properties": {...}
    }
}
```

### <a name="get-digital-twin-api"></a>Digitális Twin API beszerzése

A megoldás az IoT Plug and Play eszköz által megvalósított modell AZONOSÍTÓjának lekéréséhez a [Digital Twin](/rest/api/iothub/service/digitaltwin/getdigitaltwin) API beolvasása lehetőséget használhatja.

A következő digitális kettős válasz kódrészletben `$metadata.$model` a IoT Plug and Play eszköz modell-azonosítóját tartalmazza:

```json
{
    "$dtId": "sample-device",
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

### <a name="digital-twin-change-event-notification"></a>Digitális kettős változási eseményről szóló értesítés

Az eszköz kapcsolata egy [digitális kettős változási eseményről](concepts-digital-twin.md#digital-twin-change-events) szóló értesítést eredményez. A megoldásnak erre az eseményre vonatkozó értesítésre kell előfizetnie. Ha meg szeretné tudni, hogyan engedélyezheti az útválasztást a digitális kettős események esetében, tekintse meg az [eszközről a felhőbe irányuló üzenetek különböző végpontokra való küldéséhez IoT hub üzenet-útválasztás használata](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)témakört

A megoldás az alábbi kódrészletben látható eseményt követve megismerheti a IoT Plug and Play eszközt, amely a csatlakozáshoz és a modell AZONOSÍTÓjának lekéréséhez használható:

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/22/2020 8:02:27 PM
iothub-message-source:digitalTwinChangeEvents
correlation-id:100f322dc2c5
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:com:example:TemperatureController;1"
  }
]
```

## <a name="retrieve-a-model-definition"></a>Modell definíciójának beolvasása

Egy megoldás a fentebb azonosított modell-azonosítót használja a megfelelő modell definíciójának lekéréséhez.

A megoldás a következő lehetőségek egyikének használatával kérheti le a modell definícióját:

### <a name="model-repository"></a>Modelladattár

A megoldások a [modell tárházát](concepts-model-repository.md) használhatják modellek beolvasására. Az eszközök építői vagy a megoldás-építők előre fel kell tölteniük a modelljeiket a tárházba, így a megoldás lekérheti őket.

Miután azonosította a modell AZONOSÍTÓját egy új eszköz kapcsolataihoz, kövesse az alábbi lépéseket:

1. Kérje le a modell definícióját a modell-adattárból a modell AZONOSÍTÓjának használatával. További információ: [eszköz modell tárháza](concepts-model-repository.md).

1. A csatlakoztatott eszköz modell-definíciójának használatával enumerálhatja az eszköz képességeit.

1. Az eszköz enumerálási képességeinek használatával lehetővé teheti a felhasználók számára [az eszköz interakcióját](quickstart-service.md).

### <a name="custom-store"></a>Egyéni tároló

A megoldások tárolhatják ezeket a modell-definíciókat egy helyi fájlrendszerben, egy nyilvános fájlkiszolgálón, vagy egyéni implementációt is használhatnak.

Miután azonosította a modell AZONOSÍTÓját egy új eszköz kapcsolataihoz, kövesse az alábbi lépéseket:

1. Kérje le a modell definícióját a modell AZONOSÍTÓjának használatával az egyéni tárolóból.

1. A csatlakoztatott eszköz modell-definíciójának használatával enumerálhatja az eszköz képességeit. 

1. Az eszköz enumerálási képességeinek használatával lehetővé teheti a felhasználók számára [az eszköz interakcióját](quickstart-service.md).  

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan integrálhatja a IoT Plug and Play modelleket egy IoT-megoldásba, néhány javasolt következő lépés:

- [Kommunikáció egy eszközzel a megoldásból](quickstart-service.md)
- [IoT Digital Twin REST API](/rest/api/iothub/service/digitaltwin)
- [Azure IoT Explorer](howto-use-iot-explorer.md)
