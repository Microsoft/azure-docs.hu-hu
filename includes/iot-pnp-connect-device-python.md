---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 607c83bd97f8e371896a5a8ac0c9aa05ab34fa2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95510598"
---
Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre IoT Plug and Play-alkalmazást, hogyan csatlakoztathatja az IoT hubhoz, és az Azure IoT Explorer eszköz használatával megtekintheti az általa küldött telemetria. A minta alkalmazás a Pythonhoz készült, és megtalálható a Pythonhoz készült Azure IoT Hub Device SDK-ban. A megoldás-szerkesztő az Azure IoT Explorer eszköz használatával képes értelmezni egy IoT Plug and Play eszköz képességeit anélkül, hogy meg kellene tekintenie az eszköz kódját.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

A rövid útmutató elvégzéséhez a fejlesztői gépen a Python 3,7 szükséges. A [Python.org](https://www.python.org/)több platformon is letöltheti a legújabb ajánlott verziót. A Python-verziót a következő paranccsal tekintheti meg:  

```cmd/sh
python --version
```

A helyi Python-környezetben a következő módon telepítse a csomagot:

```cmd/sh
pip install azure-iot-device
```

A Python SDK IoT-tárház klónozása és a **Master**:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="run-the-sample-device"></a>A minta eszköz futtatása

A *Azure-IOT-SDK-python\azure-IOT-device\samples\pnp* mappa tartalmazza a IOT Plug and Play eszközhöz tartozó mintakód kódját. Ez a rövid útmutató a *simple_thermostat.* a fájlt használja. Ez a mintakód egy IoT Plug and Play kompatibilis eszközt valósít meg, és az Azure IoT Python-eszköz ügyféloldali függvénytárát használja.

Nyissa meg a **simple_thermostat.** a fájlt egy szövegszerkesztőben. Figyelje meg, hogyan:

1. Meghatározza a [termosztátot](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)egyedileg jelölő, egyetlen eszközből álló Twin Model-azonosítót (DTMI). A felhasználónak ismernie kell a DTMI, és az eszköz implementációjának típusától függően változhat. Az aktuális minta esetében a modell egy olyan termosztátot jelöl, amely a figyelési hőmérséklettel társított telemetria, tulajdonságokkal és parancsokkal rendelkezik.

1. A a parancssori implementációk definiálásához használható függvényeket tartalmaz. Ezeket a kezelőket úgy írhatja be, hogy meghatározza, hogyan válaszol az eszköz a parancs kéréseire.

1. Függvényt ad meg a parancs válaszának definiálásához. A parancs-válasz függvények segítségével visszaküldhet egy választ az IoT hub-ra.

1. Definiál egy bemeneti billentyűzet-figyelő függvényt, amely lehetővé teszi az alkalmazásból való kilépést.

1. **Fő** funkciója van. A **fő** függvény:

    1. Az eszközoldali SDK használatával hozza létre az eszköz ügyfelet, és csatlakozik az IoT hubhoz.

    1. Frissítések tulajdonságai. Az általunk használt modell, a **termosztát**, valamint `targetTemperature` `maxTempSinceLastReboot` a termosztát két tulajdonsága, hogy mi lesz a használata. A tulajdonságok a `patch_twin_reported_properties` on definiált metódus használatával frissülnek `device_client` .

    1. A **execute_command_listener** függvény használatával elindítja a parancsokra vonatkozó kérelmek figyelését. A függvény egy "figyelőt" állít be a szolgáltatásból érkező parancsok figyelésére. A figyelő beállításakor a, a `method_name` `user_command_handler` és a `create_user_response_handler` .
        - A `user_command_handler` függvény határozza meg, hogy az eszköz mit tegyen, ha parancsot kap. Ha például a riasztás leáll, a parancs fogadásának hatása felébred. Gondolja át ezt a parancsot a meghívott parancs hatására.
        - A `create_user_response_handler` függvény létrehoz egy választ, amelyet a rendszer a IoT hub számára fog elküldeni, ha a parancs végrehajtása sikeresen befejeződött. Ha például a riasztás leáll, a Szundi késleltetéssel válaszol, amely a szolgáltatásnak küldött visszajelzés. Erre a szolgáltatásra adott válaszként gondoljon. Ez a válasz a portálon tekinthető meg.

    1. Elindítja a telemetria küldését. A **pnp_send_telemetry** a pnp_methods. file. reszelő fájlban van definiálva. A mintakód egy hurok használatával hívja meg ezt a függvényt nyolc másodpercenként.

    1. Letiltja az összes figyelőt és feladatot, és a **q** vagy a **q** gomb megnyomásakor a hurok is létezik.

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

A minta-konfigurációval kapcsolatos további tudnivalókért tekintse meg a [minta](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md)információit.

Most, hogy már látta a kódot, a következő paranccsal futtathatja a mintát:

```cmd/sh
python simple_thermostat.py
```

A következő kimenet jelenik meg, amely azt jelzi, hogy az eszköz telemetria küld a központba, és most már készen áll a parancsok és a tulajdonságok frissítéseinek fogadására:

```cmd/sh
Listening for command requests and property updates
Press Q to quit
Sending telemetry for temperature
Sent message
```

A következő lépések elvégzése közben tartsa a mintát.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>A kód érvényesítése az Azure IoT Explorer használatával

Az ügyfél mintájának elindítása után az Azure IoT Explorer eszköz használatával ellenőrizze, hogy működik-e.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]
