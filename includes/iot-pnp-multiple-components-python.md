---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 94451cfefefe30bbae1748844f9303b2cfdd7be1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104612069"
---
Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre IoT Plug and Play-eszköz-alkalmazást összetevőkkel, hogyan csatlakoztathatja az IoT hubhoz, és az Azure IoT Explorer eszköz használatával megtekintheti a központnak küldött adatokat. A minta alkalmazás Pythonban van megírva, és része a Pythonhoz készült Azure IoT Device SDK-nak. A megoldás-szerkesztő az Azure IoT Explorer eszköz használatával képes értelmezni egy IoT Plug and Play eszköz képességeit anélkül, hogy meg kellene tekintenie az eszköz kódját.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * A mintakód letöltése.
> * Futtassa a minta-eszköz alkalmazást, és ellenőrizze, hogy csatlakozik-e az IoT hubhoz.
> * Tekintse át a forráskódot.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Az oktatóanyag elvégzéséhez Python 3,7 szükséges a fejlesztői gépen. A [Python.org](https://www.python.org/)több platformon is letöltheti a legújabb ajánlott verziót. A Python-verziót a következő paranccsal tekintheti meg:  

```cmd/sh
python --version
```

A [Python.org](https://www.python.org/)több platformon is letöltheti a legújabb ajánlott verziót.

## <a name="download-the-code"></a>A kód letöltése

Az **Azure-IOT-Device** csomag pip-ként van közzétéve.

A helyi Python-környezetben a következő módon telepítse a csomagot:

```cmd/sh
pip install azure-iot-device
```

Ha befejezte a gyors üzembe helyezést [: csatlakoztasson egy IoT Plug and Play Windows rendszerű eszközt IoT hub (Python)](../articles/iot-pnp/quickstart-connect-device.md), már klónozotta a tárházat.

A Python SDK IoT-tárház klónozása:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="review-the-code"></a>A kód áttekintése

Ez a példa egy IoT Plug and Play hőmérséklet-vezérlő eszközt valósít meg. A minta által megvalósított modell [több összetevőt](../articles/iot-pnp/concepts-modeling-guide.md)használ. A [hőmérséklet-eszköz digitális Twins Definition Language (DTDL) modellje](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) az eszköz által megvalósított telemetria, tulajdonságokat és parancsokat határozza meg.

A *Azure-IOT-SDK-python\azure-IOT-device\samples\pnp* mappa tartalmazza a IOT Plug and Play eszközhöz tartozó mintakód kódját. A hőmérséklet-vezérlő mintájának fájljai a következők:

- temp_controller_with_thermostats.
- pnp_helper.

A hőmérséklet-vezérlő több összetevővel és egy alapértelmezett összetevővel rendelkezik, a hőmérséklet-vezérlő DTDL modellje alapján.

Nyissa meg a *temp_controller_with_thermostats.* a fájlt egy tetszőleges szerkesztőben. A fájlban található kód:

1. Importálás `pnp_helper.py` a segítő módszerekhez való hozzáféréshez.

1. A két digitális kettős modell azonosítóját (DTMIs) határozza meg, amelyek egyedileg jelölik a DTDL modellben definiált két különböző felületet. A valós hőmérséklet-vezérlő összetevőinek meg kell valósítaniuk ezt a két felületet. Ez a két csatoló már közzé van téve egy központi tárházban. Ezeknek a DTMIs ismerniük kell a felhasználó számára, és az eszközök megvalósítási forgatókönyvtől függően változhatnak. Az aktuális minta esetében ez a két csatoló a következőket képviseli:

    - Egy termosztát
    - Az Azure által fejlesztett eszközök adatai.

1. A `model_id` megvalósított eszköz DTMI határozza meg. A DTMI felhasználó által definiált, és meg kell egyeznie a DTMI a DTDL-modell fájljában.

1. Meghatározza a DTDL-fájlban található összetevők nevét. Két termosztát található a DTDL és egy eszköz-információs összetevőben. `serial_number`Az alapértelmezett összetevőben is meg van határozva egy konstans. Egy `serial_number` eszköz nem módosítható.

1. A parancs-kezelő implementációit határozza meg. Ezek határozzák meg, hogy az eszköz mikor kapja meg a parancs kéréseit.

1. Függvények definiálása a parancs válaszának létrehozásához. Ezek határozzák meg, hogy az eszköz hogyan válaszol a parancs kéréseire. Ha egy parancsnak egyéni választ kell küldenie az IoT hubhoz, a parancs-válasz függvényeket kell létrehoznia. Ha nincs megadva egy parancshoz tartozó Response függvény, a rendszer általános választ kap. Ebben a mintában csak a **getMaxMinReport** parancs egyéni választ tartalmaz.

1. Definiál egy függvényt, amely telemetria küld az eszközről. Mind a Termosztátok, mind az alapértelmezett összetevő telemetria küld. Ez a függvény egy opcionális összetevő-Name paramétert használ, amely lehetővé teszi annak azonosítását, hogy a telemetria melyik összetevőt küldték.

1. Egy figyelőt határoz meg a parancsok kéréséhez.

1. Egy figyelőt határoz meg a kívánt tulajdonságok frissítéseihez.

1. A funkciója a következőket tartalmazhatja `main` :

    - Az eszközoldali SDK használatával hozza létre az eszköz ügyfelet, és csatlakozik az IoT hubhoz. Az eszköz elküldi `model_id` azt, hogy az IoT hub azonosítani tudja az eszközt IoT Plug and Play eszközként.

    - A a `create_reported_properties` segítő fájl függvényét használja a tulajdonságok létrehozásához. Adja át az összetevő nevét és a tulajdonságokat kulcs érték párokként ehhez a függvényhez.

    - A meghívásával frissíti az összetevők olvasható tulajdonságait `patch_twin_reported_properties` .

    - A függvény használatával elindítja a parancsokra vonatkozó kérelmek figyelését `execute_command_listener` . A függvény egy figyelőt állít be a szolgáltatástól érkező parancsokhoz. A figyelő beállításakor a, a `method_name` `user_command_handler` és az opcionális `create_user_response_handler` paramétert adja meg.
        - Az `method_name` határozza meg a parancs kérését. Ebben a példában a modell meghatározza a parancsok **újraindítását** és a **getMaxMinReport**.
        - A `user_command_handler` függvény határozza meg, hogy az eszköz mit tegyen, ha parancsot kap.
        - A `create_user_response_handler` függvény létrehoz egy választ, amelyet a rendszer a IoT hub számára fog elküldeni, ha a parancs végrehajtása sikeresen befejeződött. Ez a válasz a portálon tekinthető meg. Ha ez a függvény nincs megadva, a rendszer általános választ kap a szolgáltatásnak.

    - A használatával `execute_property_listener` figyeli a tulajdonságok frissítéseit.

    - Megkezdi a telemetria küldését a használatával `send_telemetry` . A mintakód egy hurok használatával hívja meg a három telemetria küldési függvényt. Mindegyiket nyolc másodpercenként hívja meg

    - Letiltja az összes figyelőt és feladatot, és a **q** vagy a **q** gomb megnyomásakor kilép a hurokból.

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

A minta-konfigurációval kapcsolatos további tudnivalókért tekintse meg a [minta](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md)információit.

A minta futtatásához használja a következő parancsot:

```cmd/sh
python temp_controller_with_thermostats.py
```

A mintául szolgáló eszköz néhány másodpercenként telemetria üzenetet küld az IoT hubhoz.

A következő kimenet jelenik meg, amely azt jelzi, hogy az eszköz telemetria küld a központba, és most már készen áll a parancsok és a tulajdonságok frissítéseinek fogadására.

![Eszköz megerősítő üzenetei](media/iot-pnp-multiple-components-python/multiple-component.png)

A következő lépések elvégzése közben tartsa a mintát.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>A kód érvényesítése az Azure IoT Explorer használatával

Az ügyfél mintájának elindítása után az Azure IoT Explorer eszköz használatával ellenőrizze, hogy működik-e.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]
