---
title: Egy fejlesztői készlet eszköz csatlakoztatása az Azure IoT Central alkalmazáshoz |} A Microsoft Docs
description: Eszköz a fejlesztők megtudhatja, hogyan az MXChip IoT DevKit eszköz csatlakoztatása az Azure IoT Central alkalmazáshoz.
author: dominicbetts
ms.author: dobett
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 82222dd927f46761941a6a750d96222cc626e71b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60887266"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Az MXChip IoT DevKit eszköz csatlakoztatása az Azure IoT Central alkalmazáshoz

Ez a cikk azt ismerteti, hogyan eszköz a fejlesztők az MXChip IoT fejlesztői készlet (DevKit) eszköz csatlakoztatása a Microsoft Azure IoT Central alkalmazáshoz.

## <a name="before-you-begin"></a>Előzetes teendők

A jelen cikkben ismertetett lépések végrehajtásához szüksége van az alábbi forrásanyagokat:

1. A létrehozott Azure IoT Central alkalmazáshoz a **minta Devkits** alkalmazássablon. További információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](quick-deploy-iot-central.md).
1. Egy fejlesztői készlet eszköz. DevKit eszköz vásárol, a Microsoft [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/).

## <a name="sample-devkits-application"></a>Devkits mintaalkalmazás

A létrehozott alkalmazáshoz a **minta Devkits** alkalmazást sablon tartalmaz egy **MXChip** eszköz sablon, amely meghatározza a következő eszköz jellemzői:

- A telemetriai adatok mérések **páratartalom**, **hőmérséklet**, **nyomás**, **magnetométer** (X, Y, mentén mért Z tengely), **Érzékelőből** (X, Y, mentén mért Z tengely), és **Giroszkóp** (X, Y, mentén mért Z tengelyen).
- A mérési állapot **Eszközállapot**.
- Esemény meghatározásáért **B bekapcsolva**.
- A beállítások **feszültségérzékelő**, **aktuális**, **ventilátor sebesség**, és a egy **integrációs modul** be-vagy kikapcsolása.
- Eszköztulajdonságok **die szám** és **eszköz helye**, vagyis a location tulajdonsághoz.
- A felhő tulajdonság **gyártott a**.
- Parancsok **Echo** és **visszaszámlálás**. Ha egy igazi eszközön kap egy **Echo** parancs azt jeleníti meg az elküldött érték felolvassa az eszköz képernyőjén. Ha egy igazi eszközön kap egy **visszaszámlálás** paranccsal, a LED Váltás egy olyan mintát, és az eszköz visszaszámlálás értékek küld vissza az IoT-központ.

A konfiguráció teljes kapcsolatban lásd: [MXChip eszköz sablon részletei](#mxchip-device-template-details)

## <a name="add-a-real-device"></a>Valós eszköz hozzáadása

### <a name="get-your-device-connection-details"></a>Az eszköz kapcsolat részleteinek beolvasása

Az Azure IoT Central-alkalmazás hozzáadása a valós eszközöknek a **MXChip** eszköz sablont, és jegyezze fel az eszköz kapcsolat részletei: **Hatókör azonosítója, az eszköz Azonosítóját és az elsődleges kulcs**:

1. Adjon hozzá egy **valós eszköz** Device Explorer, jelölje ki **+ új > valós** valós eszköz hozzáadásához.

    * Adjon meg egy kisbetűs **Eszközazonosító**, vagy használja a javasolt **Eszközazonosító**.
    * Adjon meg egy **eszköznév**, vagy használja a javasolt név

    ![Eszköz hozzáadása](media/howto-connect-devkit/add-device.png)

1. Kérheti le az eszköz kapcsolat adatait, **hatókör azonosítója**, **Eszközazonosító**, és **elsődleges kulcs**válassza **Connect** az eszköz oldalon.

    ![Kapcsolat adatai](media/howto-connect-devkit/device-connect.png)

1. Jegyezze fel a kapcsolati adatok. Ideiglenesen megszakadt a kapcsolat az internetről a következő lépésben DevKit eszközét előkészítésekor.

### <a name="prepare-the-devkit-device"></a>A fejlesztői készlet eszköz előkészítése

Ha korábban már használta az eszköz és a kívánt újrakonfigurálásának egy másik Wi-Fi hálózatot, a kapcsolati karakterlánc vagy a telemetriai mérési, nyomja le az mind a **A** és **B** gombok egyszerre. Ha nem működik, nyomja le az **alaphelyzetbe** gombra, és próbálkozzon újra.

#### <a name="to-prepare-the-devkit-device"></a>Az DevKit eszköz előkészítése

1. Az MXChip a a legújabb előregyártott Azure IoT Central belső vezérlőprogram letöltése a [kiadások](https://aka.ms/iotcentral-docs-MXChip-releases) lapját a githubon.
1. A fejlesztői készlet eszköz csatlakoztatása a fejlesztői gépén, USB-kábel segítségével. Windows, a fájl explorer megnyílik egy ablak a tárolót a fejlesztői készlet eszközön leképezve meghajtón. Ha például a meghajtó neve lehet **AZ3166 (D:)**.
1. Húzza a **iotCentral.bin** fájlt arra a meghajtóra ablakot. A másolás befejeződése után az eszköz újraindul, új belső vezérlőprogramját.

1. A fejlesztői készlet eszköz újraindításakor a következő képernyő jelenik meg:

    ```
    Connect HotSpot:
    AZ3166_??????
    go-> 192.168.0.1
    PIN CODE xxxxx
    ```

    > [!NOTE]
    > A képernyőn megjelenik az bármi más, ha alaphelyzetbe az eszközt, majd kattintson a **A** és **B** gombok használatát az eszközön, az eszköz újraindítását egy időben.

1. Az eszköz most már hozzáférési pont (AP) módban van. A Wi-Fi hozzáférési pont a számítógép vagy mobileszköz csatlakozhat.

1. A számítógépen telefonon vagy táblagépen kapcsolódni a Wi-Fi hálózat nevét, az eszköz képernyőjén látható. Ehhez a hálózathoz való csatlakozáskor nincs internet-hozzáféréssel. Ebben az állapotban várható és csak csatlakozott a hálózathoz, rövid időre az eszköz konfigurálása közben.

1. Nyissa meg a webböngészőjét, és navigáljon a [ http://192.168.0.1/start ](http://192.168.0.1/start). A következő weblapon megjelenik:

    ![Eszköz konfigurációs lapján](media/howto-connect-devkit/configpage.png)

    Adja meg a weblap:
    - A Wi-Fi-hálózat neve
    - a Wi-Fi-hálózat jelszavát
    - A PIN-kódot az eszköz képernyőjén megjelenő
    - A kapcsolat adatai **hatókör azonosítója**, **Eszközazonosító**, és **elsődleges kulcs** eszköze (kell már mentette-e a következő lépéseket követve)
    - Válassza ki az összes elérhető telemetriai adat mérések

1. Miután kiválasztotta **konfigurálása eszköz**, ezt oldal jelenik meg:

    ![Eszköz konfigurálva](media/howto-connect-devkit/deviceconfigured.png)

1. Nyomja le az **alaphelyzetbe** gomb az eszközön.

## <a name="view-the-telemetry"></a>A telemetriai adatok megtekintése

A fejlesztői készlet eszköz újraindításakor az eszközön a képernyőn látható:

* A telemetriai adatokat küldött üzenetek száma.
* Hibák száma.
* A kapott kívánt tulajdonságok száma és a jelentett tulajdonságok számát.

> [!NOTE]
> Ha az eszköz megjelenik az iterációhoz való csatlakozás, ellenőrizze, hogy az eszköz e **letiltott** az IoT-központ és **feloldása** az eszköz így képes csatlakozni az alkalmazáshoz.

Rázza meg az eszköz a jelentett tulajdonságok küldéséhez. Az eszköz küld egy véletlenszerű számot, a **Die szám** eszköztulajdonság.

A telemetriai adatok mérések és a jelentett tulajdonságértékeket megtekintése, és a beállítások konfigurálása az Azure IoT Central:

1. Használat **Device Explorer** navigálhat a **mérések** lapját valós MXChip hozzáadott:

    ![Navigáljon a valódi eszköz](media/howto-connect-devkit/realdevicenew.png)

1. Az a **mérések** lapon megtekintheti az MXChip eszközről érkező telemetriai adatok:

    ![Valódi eszköz telemetriájának megtekintése](media/howto-connect-devkit/devicetelemetrynew.png)

1. Az a **tulajdonságok** lapon megtekintheti a legutóbbi száma és az eszköz helye, az eszköz által jelentett:

    ![Az eszköz tulajdonságai](media/howto-connect-devkit/devicepropertynew.png)

1. Az a **beállítások** lapon módosíthatja a beállításokat az MXChip eszközön:

    ![Eszköz-beállítások megjelenítése](media/howto-connect-devkit/devicesettingsnew.png)

1. Az a **parancsok** lapon segítségével meghívhatja a **Echo** és **visszaszámlálás** parancsokat:

    ![Hívás-parancsok](media/howto-connect-devkit/devicecommands.png)

1. Az a **irányítópult** lapon láthatja a hely hozzárendelése

    ![Eszköz-irányítópult megtekintése](media/howto-connect-devkit/devicedashboardnew.png)

## <a name="download-the-source-code"></a>Letöltheti a forráskódot

Ha azt szeretné, és módosítsa az eszköz kódot, letöltheti a Githubról. Ha azt tervezi, a kód módosítása, kövesse ezeket az utasításokat [a fejlesztési környezet előkészítését](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/#step-5-prepare-the-development-environment) az asztali operációs rendszerének.

Letöltheti a forráskódot, az asztali gépen futtassa a következő parancsot:

```cmd/sh
git clone https://github.com/Azure/iot-central-firmware
```

Az előző parancs letölti a forráskód nevű mappába `iot-central-firmware`.

> [!NOTE]
> Ha **git** nincs telepítve a fejlesztési környezet töltheti le a [ https://git-scm.com/download ](https://git-scm.com/download).

## <a name="review-the-code"></a>A kód áttekintése

A Visual Studio Code segítségével nyissa meg a `MXCHIP/mxchip_advanced` mappájában a `iot-central-firmware` mappa:

![Visual Studio Code](media/howto-connect-devkit/vscodeview.png)

Hogyan a telemetriája el lesz küldve az Azure IoT Central alkalmazásnak megtekintéséhez nyissa meg a **telemetry.cpp** fájlt a `src` mappa:

- A függvény `TelemetryController::buildTelemetryPayload` hoz létre a telemetria hasznos JSON-az érzékelőkről származó adatok az eszközön.

- A függvény `TelemetryController::sendTelemetryPayload` hívások `sendTelemetry` a a **AzureIOTClient.cpp** a JSON-adattartalom küldése az IoT hubra az Azure IoT központi alkalmazás használja.

Hogyan jelentett tulajdonságértékeket az Azure IoT Central alkalmazásnak megtekintéséhez nyissa meg a **telemetry.cpp** fájlt a `src` mappa:

- A függvény `TelemetryController::loop` küld a **hely** jelentett tulajdonság körülbelül 30 másodpercenként. Használja a `sendReportedProperty` működni a **AzureIOTClient.cpp** forrásfájl.

- A függvény `TelemetryController::loop` küld a **dieNumber** jelentett tulajdonságot, ha az eszköz érzékelőből észlel olyan dupla koppintással. Használja a `sendReportedProperty` működni a **AzureIOTClient.cpp** forrásfájl.

Az eszközt az IoT-központ alkalmazás nevű parancsok reakciója megtekintéséhez nyissa meg a **registeredMethodHandlers.cpp** fájlt a `src` mappa:

- A **dmEcho** feladata a kezelő a **echo** parancsot. Megjeleníti a **displayedValue** mezőjénél a hasznos adatok az eszköz képernyőjén.

- A **dmCountdown** feladata a kezelő a **visszaszámlálás** parancsot. Ez az eszköz LED színének módosítása és a jelentett tulajdonságok használ a visszaszámlálási értéknek küldésére az IoT Central alkalmazásnak. A jelentett tulajdonságnak a neve megegyezik a parancsot. A függvény használja a `sendReportedProperty` működni a **AzureIOTClient.cpp** forrásfájl.

A kód a **AzureIOTClient.cpp** forrásfájl funkciókat használ a [a Microsoft Azure IoT SDK-k és tárak a c nyelvhez készült](https://github.com/Azure/azure-iot-sdk-c) kommunikáljon az IoT Hub.

Módosítására, hozhat létre, és töltse fel a mintakódot az eszköz kapcsolatos információkért tekintse meg a **readme.md** fájlt a `MXCHIP/mxchip_advanced` mappát.

## <a name="mxchip-device-template-details"></a>Az MXChip eszköz sablon részletei

A minta Devkits alkalmazást sablon alapján létrehozott alkalmazás tartalmaz egy MXChip eszköz sablont a következő jellemzőkkel:

### <a name="measurements"></a>Mérések

#### <a name="telemetry"></a>Telemetria

| Mező neve     | Egység  | Minimális | Maximum | Tizedeshelyek |
| -------------- | ------ | ------- | ------- | -------------- |
| páratartalom       | %      | 0       | 100     | 0              |
| TEMP           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

#### <a name="states"></a>Állapotok 
| Name (Név)          | Megjelenített név   | NORMÁL | FIGYELMEZTETÉS | DANGER | 
| ------------- | -------------- | ------ | ------- | ------ | 
| DeviceState   | Eszköz állapota   | Zöld  | Narancssárga  | Piros    | 

#### <a name="events"></a>Események 
| Name (Név)             | Megjelenített név      | 
| ---------------- | ----------------- | 
| ButtonBPressed   | B gomb megnyomásakor  | 

### <a name="settings"></a>Beállítások

Numerikus beállításai

| Megjelenített név | Mező neve | Egység | Tizedeshelyek | Minimális | Maximum | Kezdeti |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Feszültségérzékelő      | setVoltage | V | 0              | 0       | 240     | 0       |
| Aktuális      | setCurrent | Teljesítménytényező  | 0              | 0       | 100     | 0       |
| Sebesség ventilátor    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

A beállítások ki-/ bekapcsolása

| Megjelenített név | Mező neve | A szöveg | Ki a szöveg | Kezdeti |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ON      | KI      | Ki     |

### <a name="properties"></a>Tulajdonságok

| Típus            | Megjelenített név | Mező neve | Adattípus |
| --------------- | ------------ | ---------- | --------- |
| Eszköztulajdonság | Die száma   | dieNumber  | szám    |
| Eszköztulajdonság | Eszköz helye   | location  | location    |
| Text            | Az előállított     | manufacturedIn   | –       |

### <a name="commands"></a>Parancsok

| Megjelenített név | Mező neve | Visszatérési típusa | A beviteli mező megjelenített neve | A beviteli mező neve | A beviteli mező típusa |
| ------------ | ---------- | ----------- | ------------------------ | ---------------- | ---------------- |
| echo         | echo       | szöveg        | érték megjelenítése         | displayedValue   | szöveg             |
| Visszaszámlálás    | Visszaszámlálás  | szám      | A darabszám               | countFrom        | szám           |

## <a name="next-steps"></a>További lépések

Most, hogy megismerte a Raspberry Pi csatlakoztatása az Azure IoT Central alkalmazáshoz, a javasolt következő lépésre megtudhatja, hogyan [állítson be egy egyéni sablont](howto-set-up-template.md) a saját IoT-eszköz.
