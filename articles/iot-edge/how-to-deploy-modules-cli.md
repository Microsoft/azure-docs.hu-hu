---
title: Modulok üzembe helyezése az Azure CLI parancssorból – Azure IoT Edge
description: Az Azure CLI és az Azure IoT bővítmény használatával leküldheti az IoT Edge modult a IoT Hubról a IoT Edge eszközre, amelyet a telepítési jegyzék konfigurál.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: cc4308cf69ecb99fccb09a6668825397675983cd
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201142"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli"></a>Azure IoT Edge-modulok üzembe helyezése az Azure CLI-vel

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Miután létrehozta IoT Edge modulokat az üzleti logikával, üzembe helyezheti azokat az eszközökön a peremhálózat működéséhez. Ha több modullal is együttműködik az adatok gyűjtéséhez és feldolgozásához, egyszerre telepítheti őket, és deklarálhatja az azokat összekötő útválasztási szabályokat.

Az [Azure CLI](/cli/azure) egy nyílt forráskódú, többplatformos parancssori eszköz az Azure-erőforrások, például a IoT Edge kezelésére. Lehetővé teszi az Azure IoT Hub-erőforrások, az eszközök kiépítési szolgáltatás példányainak és a kapcsolt hubok felügyeletét a dobozból. Az új IoT-bővítmény az Azure CLI-t az eszközök kezelésével és a teljes IoT Edge képességgel gazdagítja.

Ez a cikk bemutatja, hogyan hozhat létre JSON központi telepítési jegyzéket, majd ezzel a fájllal leküldheti a központi telepítést egy IoT Edge eszközre. További információ a megosztott címkék alapján több eszközt célzó központi telepítés létrehozásáról: [IoT Edge modulok üzembe helyezése és figyelése nagy léptékben](how-to-deploy-cli-at-scale.md)

## <a name="prerequisites"></a>Előfeltételek

* Egy [IoT hub](../iot-hub/iot-hub-create-using-cli.md) az Azure-előfizetésében.
* Egy IoT Edge eszköz

  Ha nincs beállítva IoT Edge eszköz, létrehozhat egyet egy Azure-beli virtuális gépen. A [virtuális Linux-eszköz létrehozásához](quickstart-linux.md) vagy [virtuális Windows-eszköz létrehozásához](quickstart.md)kövesse az egyik rövid útmutató lépéseit.

* [Azure CLI](/cli/azure/install-azure-cli) a környezetben. Legalább az Azure CLI-verziójának 2.0.70 vagy újabbnak kell lennie. A verziószámot az `az --version` paranccsal ellenőrizheti. Ez a verzió támogatja az „az” bővítményparancsokat, és ebben a verzióban került bevezetésre a Knack parancskeretrendszer.
* Az [Azure CLI-hez készült IoT-bővítmény](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Központi telepítési jegyzék konfigurálása

Az üzembe helyezési jegyzék egy JSON-dokumentum, amely leírja, hogy mely modulokat kell telepíteni, hogyan zajlik az adatforgalom a modulok és a modul kívánt tulajdonságai között. Az üzembe helyezési jegyzékek működésével és létrehozásával kapcsolatos további információkért lásd: [IoT Edge modulok használatának, konfigurálásának és](module-composition.md)újbóli használatának ismertetése.

A modulok Azure CLI használatával történő üzembe helyezéséhez mentse a központi telepítési jegyzéket. JSON-fájlként. A fájl elérési útját a következő szakaszban fogja használni, amikor a parancs futtatásával alkalmazza a konfigurációt az eszközre.

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
              "version": "1.0",
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
          "schemaVersion": "1.1",
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

## <a name="deploy-to-your-device"></a>Üzembe helyezés az eszközön

A modulok az eszközön való üzembe helyezéséhez alkalmazza a modul adataival konfigurált telepítési jegyzékfájlt.

Módosítsa a címtárakat abba a mappába, ahová a telepítési jegyzékfájlt menti. Ha a VS Code IoT Edge-sablonok egyikét használta, használja a `deployment.json` fájlját a megoldás könyvtárának **konfigurációs** mappájába, és ne a `deployment.template.json` fájlt.

A következő parancs használatával alkalmazza a konfigurációt egy IoT Edge eszközre:

   ```azurecli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content [file path]
   ```

A Device ID paraméter megkülönbözteti a kis-és nagybetűket. A Content paraméter a mentett telepítési jegyzékfájlra mutat.

   ![az IOT Edge set-modulok kimenete](./media/how-to-deploy-cli/set-modules.png)

## <a name="view-modules-on-your-device"></a>Az eszközön található modulok megtekintése

Miután telepítette a modulokat az eszközre, a következő paranccsal tekintheti meg az összeset:

A modulok megtekintése az IoT Edge-eszközön:

   ```azurecli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

A Device ID paraméter megkülönbözteti a kis-és nagybetűket.

   ![az IOT hub modul-Identity List output](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [helyezhet üzembe és figyelheti IoT Edge modulokat a skálán](how-to-deploy-at-scale.md)