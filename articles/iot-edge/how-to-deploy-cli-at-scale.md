---
title: Modulok nagy léptékű üzembe helyezése az Azure CLI használatával – Azure IoT Edge
description: Az Azure CLI IoT-bővítményének használatával automatikus üzembe helyezéseket hozhat létre a virtuális IoT Edge számára
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: c502a9c02160c5a92d78ccdbb0532e6f173122da
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479508"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Nagy léptékű IoT Edge üzembe helyezése és monitor helyezése az Azure CLI használatával

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Hozzon **IoT Edge automatikus** üzembe helyezést az Azure parancssori felületének használatával, hogy egyszerre több eszközön is kezelni tudja a folyamatban lévő üzembe helyezéseket. A IoT Edge automatikus központi telepítései az automatikus eszközkezelési szolgáltatás [részét](../iot-hub/iot-hub-automatic-device-management.md) IoT Hub. Az üzembe helyezés dinamikus folyamatok, amelyek lehetővé teszik több modul több eszközön való üzembe helyezését, a modulok állapotának és állapotának nyomon követését, valamint szükség esetén a módosításokat.

További információ: [Az IoT Edge központi telepítések és skálázva](module-deployment-monitoring.md)való üzembe helyezése.

Ebben a cikkben beállítjuk az Azure CLI-t és az IoT-bővítményt. Ezután megtudhatja, hogyan helyezhet üzembe modulokat több IoT Edge, és hogyan figyelheti a folyamat előrehaladását az elérhető CLI-parancsokkal.

## <a name="prerequisites"></a>Előfeltételek

* Egy [IoT Hub az](../iot-hub/iot-hub-create-using-cli.md) Azure-előfizetésben.
* Egy vagy több IoT Edge eszköz.

  Ha nincs beállítva IoT Edge eszköz, létrehozhat egyet egy Azure-beli virtuális gépen. Kövesse az alábbi rövid útmutatókban található [lépéseket:](quickstart-linux.md) Virtuális Linux-eszköz létrehozása vagy Virtuális [Windows-eszköz létrehozása.](quickstart.md)

* [Azure CLI](/cli/azure/install-azure-cli) a környezetben. Az Azure CLI legalább 2.0.70-es vagy újabb verziójának kell lennie. A verziószámot az `az --version` paranccsal ellenőrizheti. Ez a verzió támogatja az „az” bővítményparancsokat, és ebben a verzióban került bevezetésre a Knack parancskeretrendszer.
* Az [Azure CLI IoT-bővítménye.](https://github.com/Azure/azure-iot-cli-extension)

## <a name="configure-a-deployment-manifest"></a>Üzembe helyezési jegyzék konfigurálása

Az üzembe helyezési jegyzék egy JSON-dokumentum, amely leírja, hogy mely modulokat kell üzembe helyezni, hogyan áramlik az adatok a modulok között, és hogy melyek az ikermodulok kívánt tulajdonságai. További információkért lásd: Modulok üzembe helyezése és útvonalak létrehozása a [IoT Edge.](module-composition.md)

A modulok Azure CLI használatával való üzembe helyezéséhez mentse helyileg az üzembe helyezési jegyzékfájlt .txt fájlként. A következő szakaszban a fájl elérési útját használja, amikor a parancs futtatásával alkalmazza a konfigurációt az eszközre.

Az alábbi alapszintű üzembe helyezési jegyzék egy modult mutat be példaként:

>[!NOTE]
>Ez a minta üzembe helyezési jegyzék 1.1-es sémaverziót használ a IoT Edge-ügynökhöz és -központhoz. A séma 1.1-es verziója az IoT Edge 1.0.10-es verziójával együtt jelent meg, és olyan funkciókat tesz elérhetővé, mint a modulok indítási sorrendje és az útvonal-rangsorolás.

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

## <a name="layered-deployment"></a>Többrétegű üzembe helyezés

A réteges üzembe helyezés az automatikus üzembe helyezés egy olyan típusa, amely egymásra halmozható. A többrétegű üzembe helyezésekkel kapcsolatos további információkért lásd az IoT Edge eszközök automatikus központi telepítésének és nagy méretekben való [üzembe helyezésének a tudnivalókat.](module-deployment-monitoring.md)

A többrétegű üzembe helyezéseket az Azure CLI-hez hasonló módon lehet létrehozni és kezelni, mindössze néhány különbséggel. A többrétegű üzembe helyezés létrehozása után az Azure CLI ugyanúgy működik a többrétegű üzembe helyezések esetén, mint bármely más üzemelő példány. Réteges üzembe helyezés létrehozásához adja hozzá a jelzőt a `--layered` create parancshoz.

A második különbség az üzembe helyezési jegyzék felépítése. Bár a szabványos automatikus üzembe helyezésnek minden felhasználói modul mellett a rendszer-futásidejű modulokat is tartalmaznia kell, a réteges üzembe helyezések csak felhasználói modulokat tartalmazhatnak. Ehelyett a többrétegű üzembe helyezéshez szabványos automatikus üzembe helyezésre van szükség az eszközön, hogy minden eszköz szükséges összetevőit IoT Edge, például a rendszer-futásidejű modulokat.

Az alábbi alapszintű, többrétegű üzembe helyezési jegyzék egy modullal, példaként:

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

Az előző példa egy többrétegű üzembe helyezési beállítást mutatott be egy `properties.desired` modulhoz. Ha ez a rétegelt üzembe helyezés olyan eszközt célozna meg, amelyben már alkalmazva lett ugyanez a modul, felülírná a meglévő kívánt tulajdonságokat. Ha felülírás helyett frissíteni szeretné a kívánt tulajdonságokat, definiálhat egy új alszakaszt. Például:

```json
"SimulatedTEmperatureSensor": {
  "properties.desired.layeredProperties": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

A modulikrek többrétegű üzembe helyezésben való konfigurálásával kapcsolatos további információkért lásd: [Többrétegű üzembe helyezés](module-deployment-monitoring.md#layered-deployment)

## <a name="identify-devices-using-tags"></a>Eszközök azonosítása címkék használatával

Mielőtt üzembe helyezést hoz létre, meg kell adnia, hogy mely eszközöket szeretné érinteni. Azure IoT Edge azonosítja az ikereszköz **címkéit** használó eszközöket. Minden eszközhöz több, a megoldásnak megfelelő módon definiált címke is lehet. Ha például intelligens épületek egy kampuszát felügyeli, a következő címkéket adhatja hozzá egy eszközhöz:

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

További információ az ikereszközökről és -címkékről: Az ikereszközök használata a [IoT Hub.](../iot-hub/iot-hub-devguide-device-twins.md)

## <a name="create-a-deployment"></a>Központi telepítés létrehozása

A modulokat úgy helyezheti üzembe a céleszközökön, hogy létrehoz egy üzembe helyezést, amely az üzembe helyezési jegyzékfájlból és más paraméterekből áll.

Üzemelő példány létrehozásához használja az [az iot edge deployment create](/cli/azure/iot/edge/deployment) parancsot:

```azurecli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

Használja ugyanezt a parancsot a `--layered` jelzővel egy réteges üzemelő példány létrehozásához.

A deployment create parancs a következő paramétereket veszi fel:

* **--layered** ( réteges) – Nem kötelező jelző az üzemelő példány réteges üzembe helyezésként való azonosításához.
* **--deployment-id** – Az IoT Hubon létrehozva üzemelő példány neve. Adjon egy egyedi nevet az üzemelő példánynak, amely legfeljebb 128 kisbetűt tartalmaz. Kerülje a szóközöket és a következő érvénytelen karaktereket: `& ^ [ ] { } \ | " < > /` . Szükséges paraméter.
* **--content** – Az üzembe helyezési jegyzék JSON-fájllal való megnyitása. Szükséges paraméter.
* **--hub-name** – Annak az IoT Hubnak a neve, amelyben az üzembe helyezés létre lesz hozva. A központnak az aktuális előfizetésben kell lennie. Módosítsa az aktuális előfizetését az `az account set -s [subscription name]` paranccsal.
* **--labels** – Címkék hozzáadása az üzemelő példányok nyomon követéséhez. A címkék az üzembe helyezést leíró Név és Érték párok. A címkék JSON-formázást használnak a nevekhez és értékekhez. Például: `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **--target-condition** – Adjon meg egy cél feltételt annak meghatározásához, hogy mely eszközöket célozza meg a központi telepítés. A feltétel az ikereszköz-címkéken vagy az ikereszköz jelentett tulajdonságain alapul, és meg kell egyeznie a kifejezésformátummal. Például: `tags.environment='test' and properties.reported.devicemodel='4000x'`.
* **--priority** – Pozitív egész szám. Abban az esetben, ha két vagy több üzemelő példány ugyanazt az eszközt célozza, a Priority (Prioritás) legmagasabb numerikus értékkel rendelkező üzemelő példány lesz érvényben.
* **--metrics – Metrikák** létrehozása, amelyek lekérdezik az edgeHub jelentett tulajdonságait az üzembe helyezés állapotának nyomon követéséhez. A metrikák JSON-bemenetet vagy fájlátmenetet is venek. Például: `'{"queries": {"mymetric": "SELECT deviceId FROM devices WHERE properties.reported.lastDesiredStatus.code = 200"}}'`.

Az üzemelő példányok Azure CLI-vel való figyelése a következő [IoT Edge:](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-with-azure-cli).

## <a name="modify-a-deployment"></a>Üzemelő példány módosítása

A központi telepítés módosításakor a módosítások azonnal replikálódnak az összes megcélzott eszközre.

Ha frissíti a cél feltételt, a következő frissítések következnek be:

* Ha egy eszköz nem felelt meg a régi cél feltételnek, de megfelel az új cél feltételnek, és ez az üzembe helyezés a legmagasabb prioritás az adott eszköz számára, akkor a rendszer ezt az üzembe helyezést alkalmazza az eszközre.
* Ha egy jelenleg ezt a központi telepítést futtató eszköz már nem felel meg a cél feltételnek, eltávolítja ezt a központi telepítést, és a következő legmagasabb prioritású központi telepítést veszi át.
* Ha az üzembe helyezést jelenleg futtató eszköz már nem felel meg a cél feltételnek, és nem felel meg a többi üzemelő példány cél feltételének, akkor az eszközön nem történik változás. Az eszköz továbbra is az aktuális állapotában futtatja az aktuális modulokat, de már nem kezeli az üzembe helyezés részeként. Ha bármely más központi telepítés cél feltételének megfelel, eltávolítja ezt a központi telepítést, és átveszi az újat.

Az üzembe helyezés tartalma nem frissíthető, beleértve az üzembe helyezési jegyzékben meghatározott modulokat és útvonalakat. Ha frissíteni szeretné egy üzemelő példány tartalmát, hozzon létre egy új központi telepítést, amely magasabb prioritással célozza meg ugyanazon eszközöket. Módosíthatja egy meglévő modul bizonyos tulajdonságait, beleértve a célfeltét, a címkéket, a metrikákat és a prioritást.

Az [üzemelő példány frissítéséhez használja](/cli/azure/iot/edge/deployment) az az iot edge deployment update parancsot:

```azurecli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

Az üzembe helyezés frissítési parancsa a következő paramétereket veszi fel:

* **--deployment-id** – Az IoT Hubban található üzemelő példány neve.
* **--hub-name** – Annak az IoT Hubnak a neve, amelyben az üzembe helyezés található. A központnak az aktuális előfizetésben kell lennie. Váltson a kívánt előfizetésre az paranccsal `az account set -s [subscription name]`
* **--set** – Az üzemelő példány egyik tulajdonságának frissítése. A következő tulajdonságokat frissítheti:
  * targetCondition – például `targetCondition=tags.location.state='Oregon'`
  * Címkék
  * Prioritás
* **--add** – Új tulajdonság hozzáadása az üzemelő példányhoz, beleértve a célfeltételeket vagy címkéket.
* **--remove** – Eltávolít egy meglévő tulajdonságot, beleértve a célfeltételeket vagy címkéket.

## <a name="delete-a-deployment"></a>Üzemelő példány törlése

Ha töröl egy központi telepítést, minden eszköz a következő legmagasabb prioritású telepítést veszi át. Ha az eszközök nem teljesítik bármely más üzemelő példány cél feltételét, akkor a modulok nem törlődnek az üzembe helyezés törlésekor.

Használja az [az iot edge deployment delete parancsot](/cli/azure/iot/edge/deployment) egy üzemelő példány törléséhez:

```azurecli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name]
```

Az üzembe helyezés törlési parancsa a következő paramétereket veszi fel:

* **--deployment-id** – Az IoT Hubban található üzemelő példány neve.
* **--hub-name** – Annak az IoT Hubnak a neve, amelyben az üzembe helyezés található. A központnak az aktuális előfizetésben kell lennie. Váltson a kívánt előfizetésre az paranccsal `az account set -s [subscription name]`

## <a name="next-steps"></a>Következő lépések

További információ: [Modulok üzembe helyezése IoT Edge eszközökre.](module-deployment-monitoring.md)