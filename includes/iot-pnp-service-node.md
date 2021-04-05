---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 7ab17f0d34ba7682778120e11aab562e106b5df7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95510589"
---
A IoT Plug and Play leegyszerűsíti a IoT azáltal, hogy az eszköz képességeinek ismerete nélkül teszi lehetővé az eszközök képességeit. Ez a rövid útmutató azt ismerteti, hogyan használható a Node.js a megoldáshoz csatlakoztatott IoT Plug and Play eszköz csatlakoztatásához és vezérléséhez.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

A rövid útmutató elvégzéséhez Node.jsra van szükség a fejlesztői gépen. A [NodeJS.org](https://nodejs.org)több platformon is letöltheti a legújabb ajánlott verziót.

A Node.js aktuális verzióját a következő paranccsal ellenőrizheti a fejlesztői gépen:

```cmd/sh
node --version
```

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Az SDK-tárház klónozása a mintakód használatával

A minták klónozása a [Node SDK-tárházból](https://github.com/Azure/azure-iot-sdk-node). Nyisson meg egy terminál-ablakot tetszőleges mappában. Futtassa a következő parancsot a Node.jsGitHub-adattárhoz [tartozó Microsoft Azure IOT SDK ](https://github.com/Azure/azure-iot-sdk-node) klónozásához:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="run-the-sample-device"></a>A minta eszköz futtatása

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

A minta-konfigurációval kapcsolatos további tudnivalókért tekintse meg a [minta](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/pnp/readme.md)információit.

Ebben a rövid útmutatóban egy, a IoT Plug and Play eszközként Node.jsba írt minta termosztát-eszközt használ. A minta eszköz futtatása:

1. Nyisson meg egy terminál-ablakot, és navigáljon ahhoz a helyi mappához, amely a GitHubról klónozott Node.js adattárhoz tartozó Microsoft Azure IoT SDK-t tartalmazza.

1. A rendszer ezt a terminált használja az **eszköz** -terminálként. Lépjen a klónozott adattár mappájába, és lépjen a */Azure-IOT-SDK-Node/Device/Samples/PnP* mappára. Telepítse az összes függőséget a következő parancs futtatásával:

    ```cmd/sh
    npm install
    ```

1. Futtassa a minta termosztátos eszközt a következő paranccsal:

    ```cmd/sh
    node simple_thermostat.js
    ```

1. Üzenet jelenik meg, amely közli, hogy az eszköz elküldött néhány információt, és online jelentett. Ezek az üzenetek azt jelzik, hogy az eszköz megkezdte a telemetria-adatok küldését a központba, és készen áll a parancsok és a tulajdonságok frissítéseinek fogadására. Ne zárjuk be ezt a terminált, hogy erősítse meg, hogy a szolgáltatási minta működik-e.

## <a name="run-the-sample-solution"></a>A minta megoldás futtatása

A [környezet beállítása a IoT Plug and Play rövid útmutatók és oktatóanyagok](../articles/iot-pnp/set-up-environment.md) létrehozott két környezeti változót a minta konfigurálásához a IoT hub és az eszközhöz való kapcsolódáshoz:

* **IOTHUB_CONNECTION_STRING**: a IoT hub-kapcsolatok karakterlánca korábban már jegyzett készített.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"` .

Ebben a rövid útmutatóban egy minta IoT megoldást használ a Node.jsban, hogy együttműködjön az imént beállított eszközzel.

1. Nyisson meg egy másik Terminálablak **szolgáltatást a szolgáltatás** -terminálként való használatra.

1. A klónozott Node SDK-adattárban navigáljon a */Azure-IOT-SDK-Node/Service/Samples/JavaScript* mappára. Telepítse az összes függőséget a következő parancs futtatásával:

    ```cmd/sh
    npm install
    ```

### <a name="read-a-property"></a>Tulajdonság beolvasása

1. Amikor futtatta a minta termosztátot az **eszköz** -terminálon, a következő üzenetek láthatók az online állapotáról:

    ```cmd/sh
    properties have been reported for component
    sending telemetry message 0...
    ```

1. Nyissa meg a **szolgáltatás** terminálját, és a következő paranccsal futtassa az eszköz adatainak olvasásához használt mintát:

    ```cmd/sh
    node twin.js
    ```

1. A **szolgáltatás** -terminál kimenetében figyelje meg az eszköz kettős válaszát. Megjelenik az eszköz modell-azonosítója és a hozzá tartozó tulajdonságok jelentés:

    ```json
    Model Id: dtmi:com:example:Thermostat;1
    {
      "deviceId": "my-pnp-device",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "Njc3MDMxNDcy",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00Z",
      "connectionState": "Connected",
      "lastActivityTime": "0001-01-01T00:00:00Z",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "modelId": "dtmi:com:example:Thermostat;1",
      "version": 4,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2020-10-05T11:35:19.4574755Z"
          },
          "$version": 1
        },
        "reported": {
          "maxTempSinceLastReboot": 31.343640523762232,
          "serialNumber": "123abc",
          "$metadata": {
            "$lastUpdated": "2020-10-05T11:35:23.7339042Z",
            "maxTempSinceLastReboot": {
              "$lastUpdated": "2020-10-05T11:35:23.7339042Z"
            },
            "serialNumber": {
              "$lastUpdated": "2020-10-05T11:35:23.7339042Z"
            }
          },
          "$version": 3
        }
      },
      "capabilities": {
        "iotEdge": false
      },
      "tags": {}
    }
    ```

1. A következő kódrészlet a *twin.js* kódot mutatja be, amely az eszköz Twin modell-azonosítóját kérdezi le:

    ```javascript
    var registry = Registry.fromConnectionString(connectionString);
    registry.getTwin(deviceId, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log('Model Id: ' + twin.modelId);
        //...
      }
      //...
    }
    ```

Ebben az esetben a kimenete `Model Id: dtmi:com:example:Thermostat;1` .

> [!NOTE]
> Ezek a szolgáltatási minták a **beállításjegyzék** osztályt használják a **IoT hub szolgáltatás ügyfelének**. Ha többet szeretne megtudni az API-król, beleértve a digitális Twins API-t, tekintse meg a [szolgáltatás fejlesztői útmutatóját](../articles/iot-pnp/concepts-developer-guide-service.md).

### <a name="update-a-writable-property"></a>Írható tulajdonság frissítése

1. Nyissa meg a *twin.js* fájlt a kódszerkesztő programban.

1. Tekintse át a mintakód két módszerét a Twin-eszközök frissítéséhez. Az első módszer használatához módosítsa a `twinPatch` változót az alábbiak szerint:

    ```javascript
    var twinPatch = {
      tags: {
        city: "Redmond"
      },
      properties: {
        desired: {
          targetTemperature: 42
        }
      }
    };
    ```

    A `targetTemperature` tulajdonság a termosztátos eszköz modelljében írható tulajdonságként van definiálva.

1. A **szolgáltatás** -terminálon futtassa a következő parancsot a minta futtatásához a tulajdonság frissítéséhez:

    ```cmd/sh
    node twin.js
    ```

1. Az **eszköz** -terminálon láthatja, hogy az eszköz a következő frissítést kapta:

    ```cmd/sh
    The following properties will be updated for the default component:
    {
      targetTemperature: {
        value: 42,
        ac: 200,
        ad: 'Successfully executed patch for targetTemperature',
        av: 2
      }
    }
    updated the property
    ```

1. A **szolgáltatás** -terminálban futtassa a következő parancsot a tulajdonság frissítésének megerősítéséhez:

    ```cmd/sh
    node twin.js
    ```

1. A **szolgáltatás** -terminál kimenetében, a `reported` Tulajdonságok szakaszban láthatja a frissített célként jelzett hőmérsékletet. Eltarthat egy ideig, amíg az eszköz befejezi a frissítést. Ismételje meg ezt a lépést, amíg az eszköz fel nem dolgozza a tulajdonság frissítését:

    ```json
    "reported": {
      //...
      "targetTemperature": {
        "value": 42,
        "ac": 200,
        "ad": "Successfully executed patch for targetTemperature",
        "av": 4
      },
      //...
    }
    ```

### <a name="invoke-a-command"></a>Parancs meghívása

1. Nyissa meg *device_method.js* fájlt, és tekintse át a kódot.

1. Nyissa meg a **szolgáltatás** terminálját. A következő parancs használatával futtassa a mintát a parancs meghívásához:

    ```cmd/sh
    set IOTHUB_METHOD_NAME=getMaxMinReport
    set IOTHUB_METHOD_PAYLOAD=commandpayload
    node device_method.js
    ```

1. A **szolgáltatás** -terminál kimenete a következő megerősítést jeleníti meg:

    ```cmd/sh
    getMaxMinReport on my-pnp-device:
    {
      "status": 200,
      "payload": {
        "maxTemp": 23.460596940801928,
        "minTemp": 23.460596940801928,
        "avgTemp": 23.460596940801928,
        "endTime": "2020-10-05T12:48:08.562Z",
        "startTime": "2020-10-05T12:47:54.450Z"
      }
    }
    ```

1. Az **eszköz** -terminálon láthatja, hogy a parancs meg van-e ismerve:

    ```cmd/sh
    MaxMinReport commandpayload
    Response to method 'getMaxMinReport' sent successfully.
    ```
