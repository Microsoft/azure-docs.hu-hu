---
title: Azure IoT Edge és az Azure IoT Central | Microsoft Docs
description: Ismerje meg, hogyan használhatja a Azure IoT Edget egy IoT Central alkalmazással.
author: dominicbetts
ms.author: dobett
ms.date: 02/19/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- device-developer
- iot-edge
ms.openlocfilehash: e0f3464420c5cb429f780999bf5983b2ab142567
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102608631"
---
# <a name="connect-azure-iot-edge-devices-to-an-azure-iot-central-application"></a>Azure IoT Edge-eszközök csatlakoztatása Azure IoT Central-alkalmazáshoz

*Ez a cikk a megoldás-építők és az eszközök fejlesztőire vonatkozik.*

Azure IoT Edge áthelyezi a Felhőbeli elemzéseket és az egyéni üzleti logikát az eszközökre, így a szervezet az adatkezelés helyett az üzleti elemzésekre koncentrálhat. Az üzleti logikát szabványos tárolóba csomagolhatja, üzembe helyezheti a tárolókat az eszközökön, és megfigyelheti őket a felhőből, és kibővítheti a IoT-megoldást.

Ez a cikk a következőket ismerteti:

* Hogyan csatlakozhatnak IoT Edge-eszközök egy IoT Central alkalmazáshoz.
* A IoT Central használata a IoT Edge-eszközök kezeléséhez.

További információ a IoT Edgeről: [Mi az Azure IoT Edge?](../../iot-edge/about-iot-edge.md)

## <a name="iot-edge"></a>IoT Edge

A IoT Edge három összetevőből áll:

* *IoT Edge modulok* az Azure-szolgáltatásokat, a partneri szolgáltatásokat vagy a saját kódokat futtató tárolók. A modulok IoT Edge eszközökön vannak telepítve, és helyileg futnak ezeken az eszközökön. További információ: Azure IoT Edge- [modulok megismerése](../../iot-edge/iot-edge-modules.md).
* A *IoT Edge futtatókörnyezet* minden IoT Edge eszközön fut, és felügyeli az egyes eszközökön üzembe helyezett modulokat. A futtatókörnyezet két IoT Edge modulból áll: *IoT Edge Agent* és *IoT Edge hub*. További információ: [a Azure IoT Edge futtatókörnyezet és az architektúrájának megismerése](../../iot-edge/iot-edge-runtime.md).
* A *felhőalapú felület* lehetővé teszi IoT Edge-eszközök távoli figyelését és kezelését. IoT Central például egy felhőalapú felület.

Egy IoT Edge eszköz a következőket teheti:

* Modulokból álló önálló eszköz.
* *Átjáró-eszköz*, amely az alárendelt eszközökkel csatlakozik hozzá.

## <a name="iot-edge-as-a-gateway"></a>IoT Edge átjáróként

Az IoT Edge-eszközök olyan átjáróként működhetnek, amely kapcsolatot biztosít a hálózaton található más alsóbb rétegbeli eszközök és a IoT Central alkalmazás között.

Két átjárói minta létezik:

* Az *átlátszó átjáró* mintájában a IoT Edge hub modul úgy viselkedik, mint a IoT Central és kezeli a IoT Centralban regisztrált eszközök kapcsolatait. Az üzenetek továbbítva lesznek az alsóbb rétegbeli eszközökről IoT Centralra, mintha nincs átjáró közöttük.

* A *fordítási átjáró* mintájában azok az eszközök, amelyek nem tudnak önállóan csatlakozni a IoT Centralhoz, csatlakozhatnak egy egyéni IoT Edge modulhoz. A IoT Edge eszközön lévő modul feldolgozza az alárendelt eszközök bejövő üzeneteit, majd továbbítja őket a IoT Centralnak.

Az átlátszó és a fordítási átjáró mintázata nem zárják ki egymást. Egyetlen IoT Edge eszköz is működhet egy transzparens átjáróként és egy fordítási átjáróként.

Ha többet szeretne megtudni a IoT Edge átjáró mintázatáról, tekintse meg a [IoT Edge-eszköz átjáróként való használatát](../../iot-edge/iot-edge-as-gateway.md)ismertető témakört.

### <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>Alsóbb rétegbeli eszközök kapcsolatai átjáróval és modulokkal

Az alsóbb rétegbeli eszközök az *IoT Edge hub*  -modulon keresztül kapcsolódhatnak egy IoT Edge átjáró eszközhöz. Ebben az esetben a IoT Edge eszköz transzparens átjáró:

:::image type="content" source="media/concepts-iot-edge/gateway-transparent.png" alt-text="Transzparens átjáró diagramja" border="false":::

Az alárendelt eszközök egy egyéni modulon keresztül is csatlakozhatnak egy IoT Edge átjáró eszközéhez. A következő forgatókönyvben az alsóbb rétegbeli eszközök egy *Modbus* egyéni modulon keresztül csatlakoznak. Ebben az esetben a IoT Edge eszköz egy fordítási átjáró:

:::image type="content" source="media/concepts-iot-edge/gateway-module.png" alt-text="Egyéni modul-kapcsolatok diagramja" border="false":::

Az alábbi ábrán egy IoT Edge átjáró-eszköz kapcsolatai láthatók mindkét típusú modulon keresztül. Ebben az esetben a IoT Edge eszköz transzparens és fordítási átjáró is:

:::image type="content" source="media/concepts-iot-edge/gateway-module-transparent.png" alt-text="A kapcsolódási modulok használatával történő csatlakozás diagramja" border="false":::

Az alsóbb rétegbeli eszközök több egyéni modulon keresztül kapcsolódhatnak egy IoT Edge átjáró-eszközhöz. Az alábbi ábrán egy Modbus egyéni modul, egy egypontos egyéni modul és a *IoT Edge hub*  modul használatával csatlakozó alsóbb rétegbeli eszközök láthatók:

:::image type="content" source="media/concepts-iot-edge/gateway-two-modules-transparent.png" alt-text="Több egyéni modul használatával történő csatlakozás diagramja" border="false":::

## <a name="iot-edge-devices-and-iot-central"></a>IoT Edge eszközök és IoT Central

IoT Edge eszközök *közös hozzáférésű aláírási* jogkivonatokat vagy X. 509 tanúsítványokat használhatnak a IoT Central való hitelesítéshez. A IoT Edge-eszközöket manuálisan is regisztrálhatja IoT Central az első csatlakoztatása előtt, vagy az eszköz kiépítési szolgáltatásával kezelheti a regisztrációt. További információért lásd: [Csatlakozás az Azure IoT Centralhoz](concepts-get-connected.md).

IoT Central az [eszközök használatával](concepts-device-templates.md) határozható meg, hogy a IoT Central hogyan kommunikáljon az eszközzel. Az eszköz sablonja például a következőket határozza meg:

* Az eszköz által küldött telemetria és tulajdonságok típusai, így a IoT Central értelmezheti őket, és vizualizációkat hozhat létre.
* Az eszköz által válaszoló parancsok, így a IoT Central a parancsok meghívásához használható felhasználói felületet jeleníthet meg.

Az IoT Edge-eszközök küldhetnek telemetria, szinkronizálhatják a tulajdonságokat, és ugyanúgy reagálnak a parancsokra, mint a szabványos eszközök. Ezért egy IoT Edge eszköznek szüksége van egy eszköz-sablonra IoT Central.

### <a name="iot-edge-device-templates"></a>IoT Edge-eszközök sablonjai

Az IoT Central-sablonok modellek segítségével írják le az eszközök képességeit. Az alábbi ábrán egy IoT Edge eszköz modellének szerkezete látható:

:::image type="content" source="media/concepts-iot-edge/iot-edge-model.png" alt-text="A modell struktúrája IoT Edge csatlakoztatott eszközhöz IoT Central" border="false":::

A IoT Central IoT Edge eszköz modelljét az alábbiak szerint:

* Minden IoT Edge eszköz sablonja rendelkezik egy képesség modellel.
* Az üzembe helyezési jegyzékben felsorolt összes egyéni modulhoz létrejön egy modul-képesség modell.
* Létrejön egy kapcsolat az egyes modulok képességeinek modellje és az eszköz modellje között.
* A modul-képesség modell egy vagy több modul-felületet valósít meg.
* Mindegyik modul felülete telemetria, tulajdonságokat és parancsokat tartalmaz.

### <a name="iot-edge-deployment-manifests-and-iot-central-device-templates"></a>IoT Edge üzembe helyezési jegyzékek és IoT Central-eszközök sablonjai

IoT Edge az üzleti logikát modulok formájában helyezheti üzembe és kezelheti. A IoT Edge modulok a IoT Edge által kezelt számítási egységek legkisebb egységei, és tartalmazhatnak olyan Azure-szolgáltatásokat, mint például a Azure Stream Analytics vagy a saját megoldásokra vonatkozó kódok.

Az IoT Edge *üzembe helyezési jegyzék* felsorolja az eszközön telepítendő IoT Edge modulokat és azok konfigurálásának módját. További információért lásd: [modulok központi telepítése és útvonalak létrehozása IoT Edgeban](../../iot-edge/module-composition.md).

Az Azure IoT Central egy üzembe helyezési jegyzéket importál a IoT Edge eszközhöz tartozó eszköz létrehozásához.

Az alábbi kódrészlet egy példát mutat be IoT Edge telepítési jegyzékre:

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
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
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0.9",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0.9",
              "createOptions": "{}"
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
        "schemaVersion": "1.0",
        "routes": {
            "route": "FROM /* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "SimulatedTemperatureSensor": {
      "properties.desired": {
           "SendData": true,
           "SendInterval": 10
      }
    }
  }
}
```

Az előző kódrészletben a következőket láthatja:

* Három modul van. A *IoT Edge ügynök* és *IoT Edge hub* rendszermodulja, amelyek az összes üzembe helyezési jegyzékben megtalálhatóak. Az egyéni **SimulatedTemperatureSensor** modul.
* A nyilvános modul lemezképeit olyan Azure Container Registry adattárból kell húzni, amely nem igényel hitelesítő adatokat a kapcsolódáshoz. A privát modulok lemezképei esetében állítsa be a tároló beállításjegyzékbeli hitelesítő adatait, amelyeket a `registryCredentials` *IoT Edge Agent* modul beállításában kíván használni.
* Az egyéni **SimulatedTemperatureSensor** modul két tulajdonsággal `"SendData": true` rendelkezik `"SendInterval": 10` .

Ha ezt az üzembe helyezési jegyzéket egy IoT Central alkalmazásba importálja, a következő sablont hozza létre:

:::image type="content" source="media/concepts-iot-edge/device-template.png" alt-text="Képernyőfelvétel: az üzembe helyezési jegyzékből létrehozott eszköz sablonja.":::

Az előző képernyőképen a következőt láthatja:

* Egy **SimulatedTemperatureSensor** nevű modul. A *IoT Edge ügynök* és *IoT Edge hub* rendszermodul nem jelenik meg a sablonban.
* Egy **felügyelet** nevű illesztőfelület, amely két, **SendData** és **SendInterval** nevű írható tulajdonságot tartalmaz.

Az üzembe helyezési jegyzék nem tartalmaz információkat a **SimulatedTemperatureSensor** -modul által küldött telemetria, vagy az általa válaszoló parancsokról. A közzététel előtt manuálisan adja hozzá ezeket a definíciókat az eszköz sablonhoz.

További információ: [oktatóanyag: Azure IoT Edge eszköz hozzáadása az Azure IoT Central alkalmazáshoz](tutorial-add-edge-as-leaf-device.md).

### <a name="update-a-deployment-manifest"></a>Központi telepítési jegyzék frissítése

Ha létrehoz egy új [verziót](howto-version-device-template.md) az eszköz sablonjában, az üzembe helyezési jegyzéket új verzióra cserélheti:

Az üzembe helyezési jegyzék cseréjekor minden csatlakoztatott IoT Edge eszköz letölti az új jegyzékfájlt, és frissíti a modulokat. A IoT Central azonban nem frissíti az eszközön található felületeket a modul konfigurációjának módosításaival. Ha például lecseréli az előző kódrészletben látható jegyzékfájlt a következő jegyzékre, akkor a **SendUnits** tulajdonság nem jelenik meg automatikusan az eszköz sablonjának **felügyeleti** felületén. Manuálisan adja hozzá az új tulajdonságot a IoT Central **felügyeleti** felületéhez, hogy felismerje:

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
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
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0.9",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0.9",
              "createOptions": "{}"
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
        "schemaVersion": "1.0",
        "routes": {
            "route": "FROM /* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "SimulatedTemperatureSensor": {
      "properties.desired": {
           "SendData": true,
           "SendInterval": 10,
           "SendUnits": "Celsius"
      }
    }
  }
}
```

## <a name="deploy-the-iot-edge-runtime"></a>A IoT Edge futtatókörnyezet üzembe helyezése

A IoT Edge futtatókörnyezet futtatásának megismeréséhez tekintse meg [Azure IoT Edge támogatott rendszerek](../../iot-edge/support.md)című témakört.

A IoT Edge futtatókörnyezetet az alábbi környezetekben is telepítheti:

* [Linuxos Azure IoT Edge telepítése vagy eltávolítása](../../iot-edge/how-to-install-iot-edge.md)
* [A Linux rendszerhez készült Azure IoT Edge telepítése és üzembe helyezése Windows-eszközön (előzetes verzió)](../../iot-edge/how-to-install-iot-edge-on-windows.md)
* [Azure IoT Edge futtatása Ubuntu-Virtual Machines az Azure-ban](../../iot-edge/how-to-install-iot-edge-ubuntuvm.md)

## <a name="iot-edge-gateway-devices"></a>Átjáró-eszközök IoT Edge

Ha IoT Edge eszközt jelölt ki egy átjáró eszközként, akkor az átjáró eszközhöz csatlakozni kívánó eszközökhöz hozzáadhat alsóbb rétegbeli kapcsolatokat az eszköz modelljeihez.

További információ: [eszközök csatlakoztatása IoT Edge transzparens átjárón keresztül](how-to-connect-iot-edge-transparent-gateway.md).

## <a name="next-steps"></a>Következő lépések

Ha Ön egy eszköz fejlesztője, a következő lépés az, hogy megtudja, hogyan [fejlesztheti saját IoT Edge modulait](../../iot-edge/module-development.md).
