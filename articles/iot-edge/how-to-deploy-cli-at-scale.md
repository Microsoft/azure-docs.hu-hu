---
title: Modulok üzembe helyezése méretezéssel az Azure CLI-vel – Azure IoT Edge
description: Az Azure CLI-hez készült IoT-bővítmény használatával automatikus központi telepítéseket hozhat létre IoT Edge eszközök csoportjaihoz
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 4ecb1c3dc0e72523b19e3183e17306774b3ce164
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370376"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>IoT Edge-modulok üzembe helyezése és figyelése az Azure CLI használatával

Hozzon létre egy **IoT Edge automatikus központi telepítést** az Azure parancssori felület használatával, amellyel egyszerre több eszközön is kezelheti a folyamatban lévő központi telepítéseket. A IoT Edge automatikus központi telepítései a IoT Hub [automatikus Eszközkezelő](../iot-hub/iot-hub-automatic-device-management.md) funkciójának részét képezik. A központi telepítések olyan dinamikus folyamatok, amelyek lehetővé teszik több modul üzembe helyezését több eszközön, nyomon követni a modulok állapotát és állapotát, és szükség esetén módosításokat hajthat végre.

További információ: [IoT Edge automatikus központi telepítésének ismertetése egyetlen eszközön vagy nagy méretekben](module-deployment-monitoring.md).

Ebben a cikkben az Azure CLI-t és a IoT-bővítményt kell beállítania. Ezután megtudhatja, hogyan telepíthet modulokat IoT Edge eszközök készletére, és hogyan figyelheti meg a folyamatot az elérhető CLI-parancsokkal.

## <a name="prerequisites"></a>Előfeltételek

* Egy [IoT hub](../iot-hub/iot-hub-create-using-cli.md) az Azure-előfizetésében.
* Egy vagy több IoT Edge eszköz.

  Ha nincs beállítva IoT Edge eszköz, létrehozhat egyet egy Azure-beli virtuális gépen. A [virtuális Linux-eszköz létrehozásához](quickstart-linux.md) vagy [virtuális Windows-eszköz létrehozásához](quickstart.md)kövesse az egyik rövid útmutató lépéseit.

* [Azure CLI](/cli/azure/install-azure-cli) a környezetben. Legalább az Azure CLI-verziójának 2.0.70 vagy újabbnak kell lennie. A verziószámot az `az --version` paranccsal ellenőrizheti. Ez a verzió támogatja az „az” bővítményparancsokat, és ebben a verzióban került bevezetésre a Knack parancskeretrendszer.
* Az [Azure CLI-hez készült IoT-bővítmény](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Központi telepítési jegyzék konfigurálása

Az üzembe helyezési jegyzék egy JSON-dokumentum, amely leírja, hogy mely modulokat kell telepíteni, hogyan zajlik az adatforgalom a modulok és a modul kívánt tulajdonságai között. További információkért lásd: [modulok központi telepítése és útvonalak létrehozása IoT Edgeban](module-composition.md).

A modulok Azure CLI használatával történő üzembe helyezéséhez mentse a központi telepítési jegyzéket. txt fájlként. A következő szakaszban a fájl elérési útját kell használnia, amikor a parancs futtatásával alkalmazza a konfigurációt az eszközre.

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

## <a name="layered-deployment"></a>Rétegzett üzembe helyezés

A többrétegű központi telepítések olyan automatikus üzembe helyezési típusok, amelyek egymáshoz halmozható. A rétegzett központi telepítésekkel kapcsolatos további információkért lásd: [IoT Edge automatikus központi telepítések megismerése egyetlen eszközön vagy nagy méretekben](module-deployment-monitoring.md).

A többrétegű központi telepítések az Azure CLI-vel ugyanúgy hozhatók létre és kezelhetők, mint bármely automatikus üzembe helyezés, mindössze néhány különbséggel. A többrétegű központi telepítés létrehozása után ugyanaz az Azure CLI működik a rétegzett központi telepítésekhez, mint bármely üzemelő példány. Rétegzett központi telepítés létrehozásához adja hozzá a `--layered` jelölőt a Create parancshoz.

A második különbség az üzembe helyezési jegyzék kialakítása. Habár a normál automatikus központi telepítésnek tartalmaznia kell a rendszerszintű modulokat a felhasználói modulok mellett, a rétegzett központi telepítések csak felhasználói modulokat tartalmazhatnak. Ehelyett a többrétegű központi telepítéseknek szabványos automatikus központi telepítésre van szükségük egy eszközön, hogy minden IoT Edge eszköz, például a rendszerfuttatókörnyezet moduljai számára megadják a szükséges összetevőket.

Íme egy alapszintű, rétegzett üzembe helyezési jegyzék, amely egy modult tartalmaz példaként:

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired.modules.SimulatedTemperatureSensor": {
          "settings": {
            "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": ""
          },
          "type": "docker",
          "status": "running",
          "restartPolicy": "always",
          "version": "1.0"
        }
      },
      "$edgeHub": {
        "properties.desired.routes.upstream": "FROM /messages/* INTO $upstream"
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

Az előző példában egy többrétegű üzembe helyezési beállítást mutatott be a `properties.desired` modulhoz. Ha ez a rétegzett központi telepítés olyan eszközt céloz meg, amelyben ugyanaz a modul már alkalmazva lett, a meglévő kívánt tulajdonságokat felülírhatja. A felülírás helyett a kívánt tulajdonságok megadásához megadhat egy új alszakaszt. Például:

```json
"SimulatedTEmperatureSensor": {
  "properties.desired.layeredProperties": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

További információ az ikrek többrétegű központi telepítések konfigurálásáról: [rétegzett üzembe helyezés](module-deployment-monitoring.md#layered-deployment)

## <a name="identify-devices-using-tags"></a>Címkék használatával azonosíthatja az eszközöket

A központi telepítés létrehozása előtt meg kell határozni, hogy mely eszközöket kívánja érinteni. A Azure IoT Edge a **címkével** rendelkező eszközöket azonosítja az eszköz Twin-ben. Minden eszköz több címkével is rendelkezhet, amelyeket bármilyen módon meghatározhat a megoldásához. Ha például egy intelligens épületből álló campusot kezel, a következő címkéket adhatja hozzá egy eszközhöz:

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

További információ az eszközök ikrekről és címkékről: [az eszközök összevetése és használata az IoT Hubban](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Központi telepítés létrehozása

A eszközöket üzembe helyezheti a céleszköz olyan központi telepítés létrehozásával, amely az üzembe helyezési jegyzékből és egyéb paraméterekből áll.

Központi telepítés létrehozásához használja az az [IOT Edge Deployment Create](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-create) parancsot:

```azurecli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

`--layered`Egy rétegzett központi telepítés létrehozásához használja ugyanazt a parancsot a jelzővel.

A központi telepítési Create parancs a következő paramétereket veszi figyelembe:

* **--rétegzett** – egy opcionális jelző, amely a központi telepítést rétegzett központi telepítésként azonosítja.
* **--Deployment-ID** – az IoT hub-ban létrehozandó központi telepítés neve. Adja meg az üzembe helyezést egy egyedi névvel, amely akár 128 kisbetűt is tartalmazhat. Kerülje a szóközöket, és a következő érvénytelen karaktereket: `& ^ [ ] { } \ | " < > /` . Szükséges paraméter.
* **--Content** -filepath az üzembe helyezési jegyzékhez tartozó JSON-ra. Szükséges paraméter.
* **--hub-Name** -annak az IoT hub-nek a neve, amelyben a központi telepítés létrejön. A hubhoz a jelenlegi előfizetésben kell lennie. Módosítsa a jelenlegi előfizetését a `az account set -s [subscription name]` paranccsal.
* **--labels** – Címkék hozzáadása az üzemelő példányok nyomon követéséhez. A címkék név, érték párok, amelyek leírják az üzemelő példányt. A címkék a nevek és az értékek JSON-formázását végzik. Például: `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **– cél – feltétel** – adja meg a cél feltételt annak meghatározásához, hogy mely eszközök lesznek megcélozva a központi telepítéssel. A feltétel a Device Twin-címkék vagy az eszközök Twin jelentett tulajdonságain alapul, és meg kell egyeznie a kifejezés formátumával. Például: `tags.environment='test' and properties.reported.devicemodel='4000x'`.
* **--priority** – pozitív egész szám. Abban az esetben, ha két vagy több üzemelő példány ugyanarra az eszközre irányul, a prioritáshoz tartozó legmagasabb numerikus értékkel rendelkező üzemelő példány érvényes lesz.
* **--mérőszámok** – mérőszámok létrehozása, amelyek lekérdezik a edgeHub jelentett tulajdonságokat a központi telepítés állapotának nyomon követéséhez. A metrikák JSON-bemenetet vagy filepath tesznek. Például: `'{"queries": {"mymetric": "SELECT deviceId FROM devices WHERE properties.reported.lastDesiredStatus.code = 200"}}'`.

A központi telepítés Azure CLI használatával történő figyelését lásd: [IoT Edge központi telepítések figyelése](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-with-azure-cli).

## <a name="modify-a-deployment"></a>Központi telepítés módosítása

Amikor módosít egy központi telepítést, a módosítások azonnal replikálódnak az összes megadott eszközre.

Ha frissíti a célként megadott feltételt, a következő frissítések történnek:

* Ha egy eszköz nem felelt meg a régi célként megadott feltételnek, de megfelel az új célként megadott feltételnek, és ez a központi telepítés az eszköz legmagasabb prioritása, akkor ez a központi telepítés az eszközre lesz alkalmazva.
* Ha a központi telepítést jelenleg futtató eszköz már nem felel meg a célként megadott feltételnek, akkor eltávolítja ezt a központi telepítést, és a következő legmagasabb prioritású üzemelő példányra kerül.
* Ha a központi telepítést jelenleg futtató eszköz már nem felel meg a célként megadott feltételnek, és nem felel meg a többi üzemelő példány céljának, akkor az eszközön nem történik változás. Az eszköz továbbra is a jelenlegi állapotukban futtatja az aktuális modulokat, de az üzembe helyezés részeként már nem felügyelhető. Ha teljesíti az egyéb üzemelő példányok célját, eltávolítja ezt az üzembe helyezést, és az újat veszi.

A központi telepítés tartalma nem frissíthető, beleértve az üzembe helyezési jegyzékben definiált modulokat és útvonalakat is. Ha szeretné frissíteni egy központi telepítés tartalmát, hozzon létre egy új központi telepítést, amely a magasabb prioritású eszközöket célozza meg. A meglévő modulok bizonyos tulajdonságai módosíthatók, beleértve a célként feltételt, a címkéket, a metrikákat és a prioritást is.

A központi telepítés frissítéséhez használja az az [IOT Edge Deployment Update](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-update) parancsot:

```azurecli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

A központi telepítés frissítése parancs a következő paramétereket veszi figyelembe:

* **--Deployment-ID** – az IoT hub-ban található központi telepítés neve.
* **--hub-Name** -annak a IoT-hubhoz a neve, amelyben a központi telepítés létezik. A hubhoz a jelenlegi előfizetésben kell lennie. Váltson a kívánt előfizetésre a paranccsal `az account set -s [subscription name]`
* **--set** -tulajdonság frissítése a központi telepítésben. A következő tulajdonságokat frissítheti:
  * targetCondition – például `targetCondition=tags.location.state='Oregon'`
  * Címkék
  * prioritású
* **--Add** -új tulajdonság hozzáadása a központi telepítéshez, beleértve a megcélzott feltételeket vagy címkéket.
* **--** eltávolít egy meglévő tulajdonságot, beleértve a megcélzott feltételeket vagy címkéket.

## <a name="delete-a-deployment"></a>Központi telepítés törlése

Ha töröl egy központi telepítést, minden eszköz a következő legmagasabb prioritású központi telepítésre kerül. Ha az eszközök nem felelnek meg az egyéb üzemelő példányok céljának, akkor a rendszer nem távolítja el a modulokat a központi telepítés törlésekor.

A központi telepítés törléséhez használja az az [IOT Edge Deployment delete](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-delete) parancsot:

```azurecli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name]
```

Az üzembe helyezés törlése parancs a következő paramétereket veszi figyelembe:

* **--Deployment-ID** – az IoT hub-ban található központi telepítés neve.
* **--hub-Name** -annak a IoT-hubhoz a neve, amelyben a központi telepítés létezik. A hubhoz a jelenlegi előfizetésben kell lennie. Váltson a kívánt előfizetésre a paranccsal `az account set -s [subscription name]`

## <a name="next-steps"></a>Következő lépések

További információ a [modulok IoT Edge eszközökön való telepítéséről](module-deployment-monitoring.md).