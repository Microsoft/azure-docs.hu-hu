---
title: Modulok üzembe helyezése méretezéssel a Visual Studio Code-Azure IoT Edge használatával
description: A Visual Studio Code-hoz készült IoT bővítmény használatával automatikus központi telepítéseket hozhat létre IoT Edge eszközök csoportjaihoz.
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/8/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9ae7009468b548cb9386fd405abfbe110d62f9a3
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377516"
---
# <a name="deploy-iot-edge-modules-at-scale-using-visual-studio-code"></a>IoT Edge modulok méretezése a Visual Studio Code használatával

A Visual Studio Code használatával létrehozhat egy **IoT Edge automatikus üzembe** helyezést, amellyel egyszerre több eszközön is kezelheti a folyamatban lévő központi telepítéseket. A IoT Edge automatikus központi telepítései a IoT Hub [automatikus Eszközkezelő](../iot-hub/iot-hub-automatic-device-management.md) funkciójának részét képezik. A központi telepítések olyan dinamikus folyamatok, amelyek lehetővé teszik több modul üzembe helyezését több eszközön. Emellett nyomon követheti a modulok állapotát és állapotát, és szükség esetén módosításokat hajthat végre.

További információ: [IoT Edge automatikus központi telepítésének ismertetése egyetlen eszközön vagy nagy méretekben](module-deployment-monitoring.md).

Ebben a cikkben a Visual Studio Code és a IoT bővítményt kell beállítania. Ezután megtudhatja, hogyan helyezhet üzembe modulokat IoT Edge eszközök készletén.

## <a name="prerequisites"></a>Előfeltételek

* Egy [IoT hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-előfizetésében.
* Egy vagy több IoT Edge eszköz.

  Ha nincs beállítva IoT Edge eszköz, létrehozhat egyet egy Azure-beli virtuális gépen. A [virtuális Linux-eszköz létrehozásához](quickstart-linux.md) vagy [virtuális Windows-eszköz létrehozásához](quickstart.md)kövesse az egyik rövid útmutató lépéseit.

* [Visual Studio Code](https://code.visualstudio.com/).
* A Visual Studio Code-hoz készült [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) .

## <a name="sign-in-to-access-your-iot-hub"></a>Jelentkezzen be az IoT hub eléréséhez

A Visual Studio Code-hoz készült Azure IoT Extensions használatával műveleteket hajthat végre a központtal. Ezeknek a műveleteknek a működéséhez be kell jelentkeznie az Azure-fiókjába, és ki kell választania a IoT hub-t, amelyen dolgozik.

1. A Visual Studio Code-ban nyissa meg a **Explorer** nézetet.

1. Az Explorer alján bontsa ki az **Azure IoT hub** szakaszt.

1. Kattintson a **...** elemre az **Azure IoT hub** szakasz fejlécében. Ha nem látja a három pontot, vigye a kurzort a fejléc fölé.

1. Válassza a **IoT hub kiválasztása** lehetőséget.

1. Ha nincs bejelentkezve az Azure-fiókjába, kövesse az utasításokat.

1. Válassza ki Azure-előfizetését.

1. Válassza ki az IoT hubot.

## <a name="configure-a-deployment-manifest"></a>Központi telepítési jegyzék konfigurálása

Az üzembe helyezési jegyzék egy JSON-dokumentum, amely leírja, hogy mely modulokat kell telepíteni. Azt is leírja, hogyan áramlik az adatfolyamok a modulok és a modul kívánt tulajdonságai között. További információkért lásd: [modulok központi telepítése és útvonalak létrehozása IoT Edgeban](module-composition.md).

Ha Visual Studio Code-t használó modulokat kíván üzembe helyezni, mentse helyileg a telepítési jegyzéket. JSON-fájl. Ha a parancs futtatásával alkalmazza a konfigurációt az eszközre, meg kell adnia a helyét.

Íme egy alapszintű üzembe helyezési jegyzék egy modullal, például:

>[!NOTE]
>Ez a minta telepítési jegyzékfájl a 1,1-es sémát használja a IoT Edge-ügynökhöz és a hubhoz. A 1,1-es verziójú séma a IoT Edge verzió 1.0.10 együtt lett közzétéve, és lehetővé teszi, hogy a modulok indítási sorrendje és az útvonal rangsorolása is megtörténjen.

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.1",
          "runtime": {
            "type": "docker",
            "settings": {
              "minDockerVersion": "v1.25",
              "loggingOptions": "",
              "registryCredentials": {}
            }
          },
          "systemModules": {
            "edgeAgent": {
              "type": "docker",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
                "createOptions": "{}"
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "SimulatedTemperatureSensor": {
              "version": "1.1",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                "createOptions": "{}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "upstream": "FROM /messages/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

Ha meg kell határoznia, hogy mely IoT Edge-eszközöket kívánja konfigurálni, futtassa a **IoT Edge: az eszköz adatainak beolvasása** parancsot.

## <a name="identify-devices-with-target-conditions"></a>A megcélzott feltételekkel rendelkező eszközök azonosítása

A központi telepítés fogadására szolgáló IoT Edge-eszközök azonosításához meg kell adnia egy célként megadott feltételt. A cél feltétel akkor teljesül, ha a megadott feltételek egy deviceId, egy címke vagy egy jelentett tulajdonságérték szerint egyeznek.

A címkéket a Twin eszközön konfigurálja. Íme egy példa a címkével rendelkező eszközökre:

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

Ez az eszköz egy központi telepítést fog kapni, ha a központi telepítéshez tartozó cél feltétel olyan kifejezést tartalmaz, amely megfelel a címke értékeinek (például) valamelyikének `tag.location.building = '20'` .

Ha egy adott eszközt szeretne megcélozni a címkétől vagy más értéktől függetlenül, csak az értéket kell megadnia a `deviceId` cél feltételhez.

Íme néhány példa:

* deviceId = ' linuxprod1 '
* deviceId = ' linuxprod1 ' vagy deviceId = ' linuxprod2 ' vagy deviceId = ' linuxprod3 '
* Címkék. környezet = "Prod"
* Tags. environment = ' Prod ' és Tags. location = ' westus2 '
* Tags. environment = ' Prod ' vagy Tags. location = ' westus2 '
* Tags. operator = ' John ' és Tags. environment = ' Prod ' és NOT deviceId = ' linuxprod1 '

A részletekért tekintse meg a [cél feltételt](module-deployment-monitoring.md#target-condition) . További információ az eszközök ikrekről és címkékről: [az eszközök összevetése és használata az IoT Hubban](../iot-hub/iot-hub-devguide-device-twins.md).

### <a name="edit-the-device-twin"></a>Az eszköz kettős szerkesztése

A címkék konfigurálásához a Visual Studio Code-ban a Twin eszközt is szerkesztheti. A **nézet** menüben válassza ki a **Command paletta** elemet, és futtassa a **IoT Edge: az eszközök kettős szerkesztése** parancsot. Válassza ki IoT Edge eszközét, és megjelenik az eszköz dupla.

Ebben a példában nincsenek definiálva címkék. Cserélje le az aktuális üres szakaszt a `"tags": {}` saját címkék definíciójában.

```json
{
    "deviceId": "myEdgeDevice",
    "etag": "AAAAAAAAAAE=",
    "deviceEtag": "NTgwMDg5MDAz",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "0001-01-01T00:00:00Z",
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
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        }
    },
    "capabilities": {
        "iotEdge": true
    },
    "deviceScope": "ms-azure-iot-edge://myEdgeDevice-637131779299315265",
    "tags": {}
}
```

A helyi fájl mentése után futtassa a **IoT Edge: Update Device Twin** parancsot.

## <a name="create-deployment-at-scale"></a>Központi telepítés létrehozása nagy léptékben

Miután konfigurálta az üzembe helyezési jegyzéket és a konfigurált címkéket az eszköz Twin-ben, készen áll a telepítésre.

1. A **nézet** menüben válassza a **parancs paletta** lehetőséget, majd válassza ki a **Azure IoT Edge: központi telepítés létrehozása a Scale** parancsban.

1. Navigáljon a használni kívánt üzembe helyezési jegyzékfájlhoz tartozó JSON-fájlhoz, majd kattintson a **peremhálózat-telepítési jegyzék kiválasztása** elemre.

1. Adja meg az értékeket a rendszer a **központi telepítési azonosítótól** kezdve.

   ![Telepítési azonosító meghatározása](./media/how-to-deploy-monitor-vscode/create-deployment-at-scale.png)

   A paraméterek értékeinek megadása:

  | Paraméter | Leírás |
  | --- | --- |
  | Központi telepítés azonosítója | Az IoT hub-ban létrehozandó központi telepítés neve. Adja meg az üzembe helyezést egy egyedi névvel, amely akár 128 kisbetűt is tartalmazhat. Kerülje a szóközöket, és a következő érvénytelen karaktereket: `& ^ [ ] { } \ | " < > /` . |
  | Cél feltétel | Adja meg a cél feltételt annak meghatározásához, hogy mely eszközök lesznek megcélozva a központi telepítéssel. A feltétel a Device Twin-címkék vagy az eszközök Twin jelentett tulajdonságain alapul, és meg kell egyeznie a kifejezés formátumával. Például: `tags.environment='test' and properties.reported.devicemodel='4000x'`. |
  | Prioritás |  Pozitív egész szám. Ha két vagy több üzemelő példány ugyanarra az eszközre van célozva, akkor a prioritáshoz tartozó legmagasabb numerikus értékkel rendelkező üzemelő példány érvényes lesz. |

  A prioritás meghatározása után a terminálnak az alábbi ábrához hasonló kimenetet kell megjelenítenie:

   ```cmd
   [Edge] Start deployment with deployment id [{specified-value}] and target condition [{specified-value}]
   [Edge] Deployment with deployment id [{specified-value}] succeeded.
   ```

## <a name="monitoring-and-modifying-deployments"></a>Központi telepítések figyelése és módosítása

A [Azure Portal](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-in-the-azure-portal) vagy az [Azure CLI](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-with-azure-cli) használatával figyelheti, módosíthatja és törölheti a központi telepítéseket. Mindkettő mérőszámokat biztosít az üzemelő példányokhoz.

## <a name="next-steps"></a>Következő lépések

További információ a [modulok IoT Edge eszközökön való telepítéséről](module-deployment-monitoring.md).