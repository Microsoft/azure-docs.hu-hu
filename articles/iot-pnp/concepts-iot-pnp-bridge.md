---
title: IoT Plug and Play híd | Microsoft Docs
description: Ismerje meg a IoT Plug and Play hidat, és hogyan használható a Windows-vagy Linux-átjáróhoz csatlakoztatott meglévő eszközök csatlakoztatására IoT Plug and Play-eszközökként.
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: e20e8ff6492db39d281fb70e35dd575ac3f8f991
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066857"
---
# <a name="iot-plug-and-play-bridge"></a>IoT Plug and Play-híd

A IoT Plug and Play Bridge egy nyílt forráskódú alkalmazás, amellyel a Windows vagy Linux rendszerű átjáróhoz csatlakoztatott meglévő eszközök IoT Plug and Play-eszközökként csatlakoztathatók. Miután telepítette és konfigurálta az alkalmazást a Windows vagy Linux rendszerű gépen, használhatja a csatlakoztatott eszközök csatlakoztatását egy IoT hubhoz. A Bridge használatával leképezheti a IoT Plug and Play-adaptereket a csatlakoztatott eszközök küldésére, az telemetria és a parancsok meghívására szolgáló kapcsolatokra.

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-high-level.png" alt-text="A bal oldalon van néhány meglévő érzékelő (vezetékes és vezeték nélküli) egy olyan Windows vagy Linux rendszerű számítógéphez, amely IoT Plug and Play hidat tartalmaz. A IoT Plug and Play híd ezután csatlakozik egy IoT hubhoz a jobb oldalon":::

A IoT Plug and Play Bridge önálló végrehajtható fájlként is telepíthető bármely IoT-eszközön, ipari számítógépen, kiszolgálón vagy Windows 10-es vagy Linux rendszerű átjárón. Az alkalmazás kódjába is beállítható. Egy egyszerű konfigurációs JSON-fájl közli a IoT Plug and Play-híddal, hogy mely csatlakoztatott eszközöket/perifériákat kell feltenni az Azure-ba.

## <a name="supported-protocols-and-sensors"></a>Támogatott protokollok és érzékelők

A IoT Plug and Play Bridge alapértelmezés szerint a következő típusú perifériákat támogatja, az adapter dokumentációjának hivatkozásait követve:

|Perifériás|Windows|Linux|
|---------|---------|---------|
|[Bluetooth-érzékelő adaptere](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/bluetooth_sensor_adapter.md) összekapcsolja az észlelt Bluetooth alacsony energiafogyasztású érzékelőket.       |Igen|Nem|
|A [kamera adapter](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/camera_adapter.md) csatlakoztatja a kamerákat egy Windows 10-es eszközön.               |Igen|Nem|
|Az [Modbus adapter](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/modbus_adapters.md) Modbus-eszközön csatlakoztatja az érzékelőket.              |Igen|Yes|
|A [MQTT-adapter](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/mqtt_adapter.md) MQTT-közvetítőt használó eszközöket csatlakoztat.                  |Igen|Yes|
|A [SerialPnP adapter](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/serialpnp/Readme.md) a soros kapcsolaton keresztül kommunikáló eszközöket csatlakoztat.               |Igen|Yes|
|A [Windows USB-perifériák](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/coredevicehealth_adapter.md) az adapter által támogatott eszköz-illesztőfelület osztályok listáját használják az adott hardver-azonosítóval rendelkező eszközök csatlakoztatásához.  |Yes|Nem alkalmazható|

Ha szeretné megtudni, hogyan terjeszthető ki a IoT Plug and Play híd a további eszközök protokolljának támogatásához, tekintse meg [a IoT Plug and Play-híd kiterjesztését](howto-author-pnp-bridge-adapter.md)ismertető témakört. A IoT Plug and Play-híd létrehozásával és üzembe helyezésével kapcsolatos információkért lásd: [a IoT Plug and Play-híd felépítése és üzembe helyezése](howto-build-deploy-extend-pnp-bridge.md).

## <a name="iot-plug-and-play-bridge-architecture"></a>IoT Plug and Play híd architektúrája

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-components.png" alt-text="A bal oldali oldalon több, a IoT Plug and Play hidat tartalmazó Windows-vagy Linux-számítógéphez csatlakoztatott perifériát jelöl. A felső részen egy, a híd felé mutató konfigurációs pontok láthatók. A híd ezután csatlakozik egy IoT hubhoz a diagram jobb oldalán.":::

### <a name="iot-plug-and-play-bridge-adapters"></a>IoT Plug and Play Bridge-adapterek

A IoT Plug and Play Bridge számos különböző típusú eszközhöz támogatja a IoT Plug and Play Bridge-adaptereket. Az *adapterek jegyzékfájlja* statikusan definiálja az adaptereket egy hídhoz.

A Bridge adapter-kezelő a jegyzékfájl használatával azonosítja és hívja meg az adapter funkcióit. Az adapter kezelője csak a konfigurációs fájlban felsorolt illesztőfelület-összetevők által igényelt Bridge-adaptereken hívja meg a Create függvényt. A rendszer minden egyes IoT Plug and Play összetevőhöz létrehoz egy adapter-példányt.

A Bridge-adapterek egy digitális Twin Interface-leírót hoznak létre és vásárolnak. Az adapter ezt a leírót használja az eszköz funkcióinak a digitális Twin szolgáltatáshoz kötéséhez.

A konfigurációs fájlban található információk használatával a Bridge-adapter a következő módszerekkel teszi lehetővé a teljes eszköz digitális kettős kommunikációját a hídon keresztül:

- Közvetlen kommunikációs csatornát hoz létre.
- Létrehoz egy figyelőt, amely arra vár, hogy egy kommunikációs csatorna elérhetővé váljon.

### <a name="configuration-file"></a>Konfigurációs fájl

A IoT Plug and Play Bridge egy JSON-alapú konfigurációs fájlt használ, amely az alábbiakat határozza meg:

- Kapcsolódás IoT hubhoz vagy IoT Central alkalmazáshoz: a lehetőségek közé tartoznak a kapcsolati karakterláncok, a hitelesítési paraméterek vagy a Device kiépítési szolgáltatás (DPS).
- A IoT Plug and Play a híd által használt képesség modellek helye. A modell meghatározza egy IoT Plug and Play eszköz képességeit, és statikus és nem módosítható.
- A IoT Plug and Play felületi összetevőinek listája, valamint az egyes összetevőkre vonatkozó alábbi információk:
- Az illesztőfelület azonosítója és az összetevő neve.
- Az összetevővel való interakcióhoz szükséges híd-adapter.
- Az eszköz információi arról, hogy a híd-adapternek meg kell teremtenie a kommunikációt az eszközzel. Például a hardver azonosítója vagy az adapter, az interfész vagy a protokoll adott adatai.
- Egy opcionális híd-adapter altípusa vagy illesztőfelület-konfigurációja, ha az adapter támogatja a hasonló eszközökkel rendelkező több kommunikációs típust. A példa azt szemlélteti, hogyan konfigurálható a Bluetooth-érzékelő összetevő:

    ```json
    {
      "_comment": "Component BLE sensor",
      "pnp_bridge_component_name": "blesensor1",
      "pnp_bridge_adapter_id": "bluetooth-sensor-pnp-adapter",
      "pnp_bridge_adapter_config": {
        "bluetooth_address": "267541100483311",
        "blesensor_identity" : "Blesensor1"
      }
    }
    ```

- A globális híd-adapter paramétereinek választható listája. A Bluetooth Sensor Bridge-adapter például a támogatott konfigurációk szótárával rendelkezik. A Bluetooth-érzékelő adapterét igénylő illesztőfelület-összetevő a következő konfigurációk egyikét választhatja ki `blesensor_identity` :

    ```json
    {
      "pnp_bridge_adapter_global_configs": {
        "bluetooth-sensor-pnp-adapter": {
          "Blesensor1" : {
            "company_id": "0x499",
            "endianness": "big",
            "telemetry_descriptor": [
              {
                "telemetry_name": "humidity",
                "data_parse_type": "uint8",
                "data_offset": 1,
                "conversion_bias": 0,
                "conversion_coefficient": 0.5
              },
              {
                "telemetry_name": "temperature",
                "data_parse_type": "int8",
                "data_offset": 2,
                "conversion_bias": 0,
                "conversion_coefficient": 1.0
              },
              {
                "telemetry_name": "pressure",
                "data_parse_type": "int16",
                "data_offset": 4,
                "conversion_bias": 0,
                "conversion_coefficient": 1.0
              },
              {
                "telemetry_name": "acceleration_x",
                "data_parse_type": "int16",
                "data_offset": 6,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              },
              {
                "telemetry_name": "acceleration_y",
                "data_parse_type": "int16",
                "data_offset": 8,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              },
              {
                "telemetry_name": "acceleration_z",
                "data_parse_type": "int16",
                "data_offset": 10,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              }
            ]
          }
        }
      }
    }
    ```

## <a name="download-iot-plug-and-play-bridge"></a>IoT Plug and Play-híd letöltése

Letöltheti a híd előre összeállított verzióját a támogatott adapterekkel a [IoT Plug and Play Bridge kiadásokban](https://github.com/Azure/iot-plug-and-play-bridge/releases) , és kiterjesztheti a legújabb kiadáshoz tartozó eszközök listáját. Töltse le az alkalmazás legújabb verzióját az operációs rendszeréhez.

Letöltheti és megtekintheti a [githubon Plug and Play Bridge IoT](https://github.com/Azure/iot-plug-and-play-bridge)forráskódját is.

## <a name="next-steps"></a>Következő lépések

Most, hogy áttekintette a IoT Plug and Play Bridge architektúráját, a következő lépésekkel többet is megtudhat a következőkről:

- [Linux vagy Windows rendszeren futó IoT Plug and Play híd-minta összekapcsolása IoT Hub](./howto-use-iot-pnp-bridge.md)
- [IoT Plug and Play-híd létrehozása és üzembe helyezése](howto-build-deploy-extend-pnp-bridge.md)
- [A IoT Plug and Play-híd kiterjesztése](howto-author-pnp-bridge-adapter.md)
- [IoT Plug and Play híd a GitHubon](https://github.com/Azure/iot-plug-and-play-bridge)
