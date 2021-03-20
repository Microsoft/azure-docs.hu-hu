---
title: A málna PI és az Azure IoT Hub összekötése a felhőben (Node.js)
description: Ebből az oktatóanyagból megtudhatja, hogyan állíthatja be és kapcsolja össze a málna PI-t az Azure IoT Hub for málna PI használatával az Azure Cloud platformba való adatküldéshez.
author: wesmc7777
manager: eliotgra
keywords: Azure IOT málna PI, málna PI IOT hub, málna PI az adatküldés a felhőbe, málna PI a felhőbe
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: wesmc
ms.custom:
- 'Role: Cloud Development'
- devx-track-js
ms.openlocfilehash: c96f674b64401250d45542d0f59f13654cf37caa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97802524"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-nodejs"></a>Málna PI összekötése az Azure IoT Hub (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Ebben az oktatóanyagban elkezdi megtanulni a Raspbian-t futtató málna-PI-k használatának alapjait. Ezután megtudhatja, hogyan csatlakoztathatók zökkenőmentesen az eszközök a felhőhöz az [Azure IoT hub](about-iot-hub.md)használatával. A Windows 10 IoT Core-minták esetében nyissa meg a [Windows fejlesztői központot](https://www.windowsondevices.com/).

Még nem rendelkezik csomaggal? Próbálja ki a [málna PI online szimulátort](iot-hub-raspberry-pi-web-simulator-get-started.md). Vagy vásároljon [itt](https://azure.microsoft.com/develop/iot/starter-kits)egy új csomagot.

## <a name="what-you-do"></a>Teendők

* Hozzon létre egy IoT hubot.

* Regisztráljon egy eszközt a PI-hez az IoT hub-ban.

* A málna PI beállítása.

* Futtasson egy minta alkalmazást a PI-ben, hogy elküldje az érzékelő adatait az IoT hubhoz.

## <a name="what-you-learn"></a>Ismertetett témák

* Azure IoT hub létrehozása és az új eszköz-kapcsolódási karakterlánc beszerzése.

* A PI és a BME280-érzékelő összekapcsolásának módja.

* Az érzékelők adatainak összegyűjtése egy minta alkalmazás a PI-on való futtatásával.

* Szenzorok adatainak küldése az IoT hubhoz.

## <a name="what-you-need"></a>Amire szükség lesz

![Amire szükség lesz](./media/iot-hub-raspberry-pi-kit-node-get-started/0-starter-kit.png)

* Egy málna pi 2 vagy málna PI 3 tábla.

* Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* A PI-hoz csatlakozó figyelő, USB-billentyűzet és-egér.

* Windows vagy Linux rendszert futtató Mac vagy PC.

* Internetkapcsolat.

* 16 GB vagy több microSD-kártya.

* USB-SD-adapter vagy microSD-kártya, amellyel az operációs rendszer lemezképét a microSD-kártyára írhatja.

* Egy 5 voltos 2 amperes tápegység a 6 lábes Micro USB-kábellel.

A következő elemek választhatók:

* Egy összeállított adafruit BME280 hőmérséklet-, nyomás-és páratartalom-érzékelő.

* Egy kenyérvágódeszka.

* 6 F/M áthidaló drótok.

* Egy diffúz 10 mm-es LED.

> [!NOTE]
> Ha nem rendelkezik a választható elemekkel, szimulált érzékelő adatokat használhat.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hub-ban

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Málna PI beállítása

### <a name="install-the-raspbian-operating-system-for-pi"></a>A PI Raspbian operációs rendszerének telepítése

Készítse elő a microSD-kártyát a Raspbian-rendszerkép telepítéséhez.

1. Töltse le a Raspbian.

   a. [Raspbian Buster és Desktop](https://www.raspberrypi.org/software/) (a. zip fájl).

   b. Bontsa ki a Raspbian-rendszerképet a számítógép egyik mappájába.

2. Telepítse a Raspbian-t a microSD-kártyára.

   a. [Töltse le és telepítse a etcher SD-kártya írója segédprogramot](https://etcher.io/).

   b. Futtassa az Etcher parancsot, és válassza ki az 1. lépésben kibontott Raspbian-rendszerképet.

   c. Válassza ki a microSD-kártya meghajtóját. Lehetséges, hogy az etch már kiválasztotta a megfelelő meghajtót.

   d. A Flash gombra kattintva telepítse a Raspbian a microSD-kártyára.

   e. A telepítés befejezésekor távolítsa el a microSD-kártyát a számítógépről. A microSD-kártyát közvetlenül is el lehet távolítani, mert az marató automatikusan kiadja vagy leválasztja a microSD-kártyát a befejezés után.

   f. Szúrja be a microSD-kártyát a PI-be.

### <a name="enable-ssh-and-i2c"></a>SSH és I2C engedélyezése

1. Kapcsolja össze a PI-t a figyelővel, a billentyűzettel és az egérrel.

2. Indítsa el a PI-t, majd jelentkezzen be a Raspbian `pi` felhasználónévvel és `raspberry` jelszóként.

3. Kattintson a málna ikonra > **Beállítások**  >  **málna PI konfiguráció**.

   ![A Raspbian beállítások menü](./media/iot-hub-raspberry-pi-kit-node-get-started/1-raspbian-preferences-menu.png)

4. Az **illesztőfelületek** lapon állítsa az **I2C** és az **SSH** lehetőséget az **engedélyezéshez**, majd kattintson **az OK** gombra. Ha nem rendelkezik fizikai érzékelőkkel, és szimulált érzékelőt szeretne használni, ezt a lépést nem kötelező megadni.

   ![Az I2C és az SSH engedélyezése a málna PI-ben](./media/iot-hub-raspberry-pi-kit-node-get-started/2-enable-i2c-ssh-on-raspberry-pi.png)

> [!NOTE]
> Az SSH és az I2C engedélyezéséhez további hivatkozási dokumentumokat talál a [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) és a [adafruit.com](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-4-gpio-setup/configuring-i2c).

### <a name="connect-the-sensor-to-pi"></a>Az érzékelő összekötése a PI-vel

A kenyérvágódeszka és a jumper huzalok segítségével a következő módon csatlakoztatható egy LED és egy BME280 a PI-hez. Ha nem rendelkezik az érzékelővel, [ugorja át ezt a szakaszt](#connect-pi-to-the-network).

![A málna PI és az érzékelő közötti kapcsolatok](./media/iot-hub-raspberry-pi-kit-node-get-started/3-raspberry-pi-sensor-connection.png)

A BME280 érzékelő képes a hőmérséklet és a páratartalom adatainak gyűjtésére. A LED villog, amikor az eszköz üzenetet küld a felhőnek.

Az érzékelő PIN-kódokhoz használja a következő huzalozást:

| Start (érzékelő & LED)     | Befejezés (tábla)            | Kábel színe   |
| -----------------------  | ---------------------- | ------------: |
| VDD (5G PIN-kód)             | 3.3 v PWR (1. PIN)       | Fehér kábel   |
| GND (PIN-kód 7G)             | GND (6. PIN)            | Barna kábel   |
| SDI (10G PIN-kód)            | I2C1 SDA (PIN-kód 3)       | Piros kábel     |
| SCK (PIN-kód 8G)             | I2C1 SCL (PIN 5)       | Narancssárga kábel  |
| LED VDD (18F PIN-kód)        | 24. GPIO (18. PIN)       | Fehér kábel   |
| LED GND (17F PIN-kód)        | GND (20. PIN-kód)           | Fekete kábel   |

Kattintson ide a [málna pi 2 & 3 PIN-kód megfeleltetésének](/windows/iot-core/learn-about-hardware/pinmappings/pinmappingsrpi) megtekintéséhez a hivatkozáshoz.

Miután sikeresen csatlakoztatta a BME280 a málna PI-hoz, az alábbihoz hasonlónak kell lennie.

![Csatlakoztatott PI és BME280](./media/iot-hub-raspberry-pi-kit-node-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>A PI összekötése a hálózattal

Kapcsolja be a PI-t a Micro USB-kábel és a tápegység használatával. Az Ethernet-kábellel csatlakoztassa a PI-t a vezetékes hálózathoz, vagy kövesse a [málna PI Foundation utasításait](https://www.raspberrypi.org/documentation/configuration/wireless/) a PI és a vezeték nélküli hálózat összekapcsolásához. Miután a PI sikeresen csatlakozott a hálózathoz, jegyezze fel a [PI IP-címét](https://www.raspberrypi.org/documentation/remote-access/ip-address.md).

![Csatlakoztatva a vezetékes hálózathoz](./media/iot-hub-raspberry-pi-kit-node-get-started/5-power-on-pi.png)

> [!NOTE]
> Győződjön meg arról, hogy a PI ugyanahhoz a hálózathoz csatlakozik, mint a számítógép. Ha például a számítógép vezeték nélküli hálózathoz csatlakozik, miközben a PI csatlakoztatva van egy vezetékes hálózathoz, előfordulhat, hogy az IP-cím nem jelenik meg a devdisco kimenetében.

## <a name="run-a-sample-application-on-pi"></a>Minta alkalmazás futtatása a PI-ben

### <a name="clone-sample-application-and-install-the-prerequisite-packages"></a>Alkalmazás klónozása és az előfeltételként szükséges csomagok telepítése

1. Csatlakozzon a málna PI-hez az alábbi SSH-ügyfelek egyikével a gazdagép számítógépről:

   **Windows-felhasználók**

   a. Töltse le és telepítse a Windows [Putty](https://www.putty.org/) -t.

   b. Másolja a PI IP-címét az állomásnév (vagy IP-cím) szakaszba, és válassza az SSH lehetőséget a kapcsolattípus mezőben.

   ![PuTTy](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Mac-és Ubuntu-felhasználók**

   Használja a beépített SSH-ügyfelet Ubuntu vagy macOS rendszeren. Előfordulhat, hogy a `ssh pi@<ip address of pi>` PI SSH-n keresztüli összekapcsolásához futtatnia kell a parancsot.

   > [!NOTE]
   > Az alapértelmezett Felhasználónév a `pi` és a jelszó `raspberry` .

2. Telepítse a Node.js és a NPM a PI-re.

   Először vizsgálja meg a Node.js verzióját.

   ```bash
   node -v
   ```

   Ha a verzió alacsonyabb, mint 10. x, vagy ha nincs Node.js a PI-ben, telepítse a legújabb verziót.

   ```bash
   curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash
   sudo apt-get -y install nodejs
   ```

3. A minta alkalmazás klónozása.

   ```bash
   git clone https://github.com/Azure-Samples/azure-iot-samples-node.git
   ```

4. Telepítse a minta összes csomagját. A telepítés magában foglalja az Azure IoT Device SDK, a BME280 Sensor Library és a huzalozási PI függvénytárat.

   ```bash
   cd azure-iot-samples-node/iot-hub/Tutorials/RaspberryPiApp
   npm install
   ```

   > [!NOTE]
   >Több percet is igénybe vehet, amíg a hálózati kapcsolatban a telepítési folyamat befejeződik.

### <a name="configure-the-sample-application"></a>A mintaalkalmazás konfigurálása

1. Nyissa meg a konfigurációs fájlt a következő parancsok futtatásával:

   ```bash
   nano config.json
   ```

   ![Konfigurációs fájl](./media/iot-hub-raspberry-pi-kit-node-get-started/6-config-file.png)

   A fájlban két elem is konfigurálható. Az első a `interval` , amely meghatározza a felhőbe küldött üzenetek közötti időtartamot (ezredmásodpercben). A második a `simulatedData` , amely egy logikai érték, amely azt jelzi, hogy szimulált érzékelőt használ-e, vagy sem.

   Ha **nem rendelkezik az érzékelővel**, állítsa be `simulatedData` úgy az értéket, `true` hogy a minta alkalmazás szimulált szenzor-adattípust hozzon létre és használjon.

   *Megjegyzés: az oktatóanyagban használt I2C-címnek alapértelmezés szerint 0x77 kell lennie. A konfigurációtól függően előfordulhat, hogy 0x76: Ha I2C hibába ütközik, próbálja meg módosítani az értéket 118-re, és ellenőrizze, hogy az jobban működik-e. Ha szeretné megtekinteni, hogy az érzékelő milyen címeket használ, futtassa a t a `sudo i2cdetect -y 1` málna PI-ben lévő rendszerhéjban.*

2. Mentse és zárja be a Control-O > írja be a > Control-X szöveget.

### <a name="run-the-sample-application"></a>A mintaalkalmazás futtatása

Futtassa a minta alkalmazást a következő parancs futtatásával:

   ```bash
   sudo node index.js '<YOUR AZURE IOT HUB DEVICE CONNECTION STRING>'
   ```

   > [!NOTE]
   > Ügyeljen rá, hogy az eszközhöz tartozó kapcsolatok sztringjét másolja be az aposztrófok közé.

A következő kimenetnek kell megjelennie, amely tartalmazza az érzékelők adatait és az IoT Hubnak üzeneteket.

![Kimenet – A Raspberry Pi-ról az IoT Hubba küldött érzékelői adatok](./media/iot-hub-raspberry-pi-kit-node-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>A hub által fogadott üzenetek olvasása

Az IoT hub által az eszközről fogadott üzenetek figyelésének egyik módja a Visual Studio Code-hoz készült Azure IoT Tools használata. További információ: az [Azure IoT Tools for Visual Studio Code használata üzenetek küldéséhez és fogadásához az eszköz és a IoT hub között](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Az eszköz által elküldhető adatfeldolgozás további módjairól folytassa a következő szakasszal.

## <a name="next-steps"></a>Következő lépések

Egy minta alkalmazást futtatott az érzékelő adatainak összegyűjtéséhez és az IoT hubhoz való elküldéséhez.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
