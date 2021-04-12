---
title: A málna PI összekötése az Azure IoT Hub C használatával | Microsoft Docs
description: Ismerje meg, hogyan telepítheti és csatlakoztatható a málna PI az Azure IoT Hub for málna PI-hoz az Azure Cloud platformba való adatküldéshez
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: wesmc
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: 52da3c28faa375f8b308e3fe78329fec4f996af9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97804054"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>Málna PI összekötése az Azure IoT Hub (C)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Ebben az oktatóanyagban elkezdi megtanulni a Raspbian-t futtató málna-PI-k használatának alapjait. Ezután megtudhatja, hogyan csatlakoztathatók zökkenőmentesen az eszközök a felhőhöz az [Azure IoT hub](about-iot-hub.md)használatával. A Windows 10 IoT Core-minták esetében nyissa meg a [Windows fejlesztői központot](https://www.windowsondevices.com/).

Még nem rendelkezik csomaggal? Próbálja ki a [málna PI online szimulátort](iot-hub-raspberry-pi-web-simulator-get-started.md). Vagy vásároljon [itt](https://azure.microsoft.com/develop/iot/starter-kits)egy új csomagot.

## <a name="what-you-do"></a>Teendők

* Hozzon létre egy IoT hubot.

* Regisztráljon egy eszközt a PI-hez az IoT hub-ban.

* A málna PI beállítása.

* Futtasson egy minta alkalmazást a PI-ben, hogy elküldje az érzékelő adatait az IoT hubhoz.

A málna PI összekötése egy Ön által létrehozott IoT hubhoz. Ezután egy minta alkalmazást futtat a PI-ben a hőmérséklet és a páratartalom adatainak a BME280-érzékelőből való összegyűjtéséhez. Végül elküldi az érzékelő adatait az IoT hubhoz.

## <a name="what-you-learn"></a>Ismertetett témák

* Azure IoT hub létrehozása és az új eszköz-kapcsolódási karakterlánc beszerzése.

* A PI és a BME280-érzékelő összekapcsolásának módja.

* Az érzékelők adatainak összegyűjtése egy minta alkalmazás a PI-on való futtatásával.

* Szenzorok adatainak küldése az IoT hubhoz.

## <a name="what-you-need"></a>Amire szükség lesz

![Amire szükség lesz](./media/iot-hub-raspberry-pi-kit-c-get-started/0-starter-kit.png)

* A málna pi 2 vagy a málna PI 3 tábla.

* Aktív Azure-előfizetés. Ha nem rendelkezik Azure-fiókkal, mindössze néhány perc alatt [hozzon létre egy ingyenes Azure próbaverziós fiókot](https://azure.microsoft.com/free/) .

* Egy figyelő, egy USB-billentyűzet és egy egér, amely a PI-hez csatlakozik.

* Mac vagy Windows vagy Linux rendszerű számítógép.

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
> Ezek az elemek nem kötelezőek, mert a kód minta támogatja a szimulált érzékelői adatokat.
>

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hub-ban

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Málna PI beállítása

Most állítsa be a málna PI-t.

### <a name="install-the-raspbian-operating-system-for-pi"></a>A PI Raspbian operációs rendszerének telepítése

Készítse elő a microSD-kártyát a Raspbian-rendszerkép telepítéséhez.

1. Töltse le a Raspbian.

   1. [Töltse le a Raspbian stretch asztalt](https://www.raspberrypi.org/software/) (a. zip fájlt).

   2. Bontsa ki a Raspbian-rendszerképet a számítógép egyik mappájába.

2. Telepítse a Raspbian-t a microSD-kártyára.

   1. [Töltse le és telepítse a etcher SD-kártya írója segédprogramot](https://etcher.io/).

   2. Futtassa az Etcher parancsot, és válassza ki az 1. lépésben kibontott Raspbian-rendszerképet.

   3. Válassza ki a microSD-kártya meghajtóját. Vegye figyelembe, hogy az Etcher valószínűleg már kiválasztotta a megfelelő meghajtót.

   4. A Flash gombra kattintva telepítse a Raspbian a microSD-kártyára.

   5. A telepítés befejezésekor távolítsa el a microSD-kártyát a számítógépről. A microSD-kártyát közvetlenül is el lehet távolítani, mert az marató automatikusan kiadja vagy leválasztja a microSD-kártyát a befejezés után.

   6. Szúrja be a microSD-kártyát a PI-be.

### <a name="enable-ssh-and-spi"></a>SSH és SPI engedélyezése

1. Kapcsolja össze a PI-t a figyelőhöz, a billentyűzethez és az egérhez, majd indítsa el a PI-t, majd jelentkezzen be a Raspbian a `pi` Felhasználónév és `raspberry` a jelszó használatával.
 
2. Kattintson a málna ikonra > **Beállítások**  >  **málna PI konfiguráció**.

   ![A Raspbian beállítások menü](./media/iot-hub-raspberry-pi-kit-c-get-started/1-raspbian-preferences-menu.png)

3. Az **illesztőfelületek** lapon állítsa be az **SPI** -t és az **SSH** -t az **engedélyezéshez**, majd kattintson **az OK** gombra. Ha nem rendelkezik fizikai érzékelőkkel, és szimulált érzékelőt szeretne használni, ezt a lépést nem kötelező megadni.

   ![SPI és SSH engedélyezése a málna PI-ben](./media/iot-hub-raspberry-pi-kit-c-get-started/2-enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE]
> Az SSH és az SPI lehetővé tételéhez további dokumentációt talál a [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) és a [Ráspi-config](https://www.raspberrypi.org/documentation/configuration/raspi-config.md)szolgáltatásban.
>

### <a name="connect-the-sensor-to-pi"></a>Az érzékelő összekötése a PI-vel

A kenyérvágódeszka és a jumper huzalok segítségével a következő módon csatlakoztatható egy LED és egy BME280 a PI-hez. Ha nem rendelkezik az érzékelővel, [ugorja át ezt a szakaszt](#connect-pi-to-the-network).

![A málna PI és az érzékelő közötti kapcsolatok](./media/iot-hub-raspberry-pi-kit-c-get-started/3-raspberry-pi-sensor-connection.png)

A BME280 érzékelő képes a hőmérséklet és a páratartalom adatainak gyűjtésére. A LED pedig villogni fog, ha az eszköz és a felhő között kommunikáció történik.

Az érzékelő PIN-kódokhoz használja a következő huzalozást:

| Start (érzékelő & LED)     | Befejezés (tábla)            | Kábel színe   |
| -----------------------  | ---------------------- | ------------: |
| LED VDD (5G PIN-kód)         | 4. GPIO (7. PIN)         | Fehér kábel   |
| LED GND (6G PIN-kód)         | GND (6. PIN)            | Fekete kábel   |
| VDD (PIN-kód 18F)            | 3.3 v PWR (17. PIN)      | Fehér kábel   |
| GND (PIN-kód 20F)            | GND (20. PIN-kód)           | Fekete kábel   |
| SCK (PIN-kód 21F)            | SPI0-SCLK (23. PIN)     | Narancssárga kábel  |
| SDO (PIN-kód 22F)            | SPI0 MISO (21. PIN)     | Sárga kábel  |
| SDI (PIN 23F üvegajtó)            | SPI0-MOSI (19. PIN)     | Zöld kábel   |
| CS (PIN-kód 24F)             | SPI0 CS (PIN-kód 24)       | Kék kábel    |

Kattintson ide a [málna pi 2 & 3 PIN-kód megfeleltetésének](/windows/iot-core/learn-about-hardware/pinmappings/pinmappingsrpi) megtekintéséhez a hivatkozáshoz.

Miután sikeresen csatlakoztatta a BME280 a málna PI-hoz, az alábbihoz hasonlónak kell lennie.

![Csatlakoztatott PI és BME280](./media/iot-hub-raspberry-pi-kit-c-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>A PI összekötése a hálózattal

Kapcsolja be a PI-t a Micro USB-kábel és a tápegység használatával. Az Ethernet-kábellel csatlakoztassa a PI-t a vezetékes hálózathoz, vagy kövesse a [málna PI Foundation utasításait](https://www.raspberrypi.org/documentation/configuration/wireless/) a PI és a vezeték nélküli hálózat összekapcsolásához. Miután a PI sikeresen csatlakozott a hálózathoz, jegyezze fel a [PI IP-címét](https://www.raspberrypi.org/documentation/remote-access/ip-address.md).

![Csatlakoztatva a vezetékes hálózathoz](./media/iot-hub-raspberry-pi-kit-c-get-started/5-power-on-pi.png)

## <a name="run-a-sample-application-on-pi"></a>Minta alkalmazás futtatása a PI-ben

### <a name="sign-into-your-raspberry-pi"></a>Jelentkezzen be a málna PI-be

1. Használja az alábbi SSH-ügyfelek egyikét a gazdagépről a málna PI-hez való kapcsolódáshoz.
   
   **Windows-felhasználók**
   1. Töltse le és telepítse a Windows [Putty](https://www.putty.org/) -t. 
   1. Másolja a PI IP-címét az állomásnév (vagy IP-cím) szakaszba, és válassza az SSH lehetőséget a kapcsolattípus mezőben.
   
   ![PuTTy](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Mac-és Ubuntu-felhasználók**

   Használja a beépített SSH-ügyfelet Ubuntu vagy macOS rendszeren. Előfordulhat, hogy a `ssh pi@<ip address of pi>` PI SSH-n keresztüli összekapcsolásához futtatnia kell a parancsot.
   > [!NOTE]
   > Az alapértelmezett Felhasználónév a `pi` , a jelszó pedig `raspberry` .


### <a name="configure-the-sample-application"></a>A mintaalkalmazás konfigurálása

1. A következő parancs futtatásával klónozott a minta alkalmazást:

   ```bash
   sudo apt-get install git-core
   git clone https://github.com/Azure-Samples/iot-hub-c-raspberrypi-client-app.git
   ```

2. Telepítési parancsfájl futtatása:

   ```bash
   cd ./iot-hub-c-raspberrypi-client-app
   sudo chmod u+x setup.sh
   sudo ./setup.sh
   ```

   > [!NOTE] 
   > Ha **nem rendelkezik fizikai BME280**, a "---szimulált-az-adattípust" parancssori paraméterként használhatja a hőmérséklet&a páratartalom-adatértékek szimulálása érdekében. `sudo ./setup.sh --simulated-data`
   >

### <a name="build-and-run-the-sample-application"></a>A minta alkalmazás létrehozása és futtatása

1. Hozza létre a minta alkalmazást a következő parancs futtatásával:

   ```bash
   cmake . && make
   ```
   
   ![Kiépítés kimenete](./media/iot-hub-raspberry-pi-kit-c-get-started/7-build-output.png)

1. Futtassa a minta alkalmazást a következő parancs futtatásával:

   ```bash
   sudo ./app '<DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   > Ügyeljen rá, hogy az eszközhöz tartozó kapcsolatok sztringjét másolja be az aposztrófok közé.
   >

A következő kimenetnek kell megjelennie, amely tartalmazza az érzékelők adatait és az IoT Hubnak üzeneteket.

![Kimenet – A Raspberry Pi-ról az IoT Hubba küldött érzékelői adatok](./media/iot-hub-raspberry-pi-kit-c-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>A hub által fogadott üzenetek olvasása

Az IoT hub által az eszközről fogadott üzenetek figyelésének egyik módja a Visual Studio Code-hoz készült Azure IoT Tools használata. További információ: az [Azure IoT Tools for Visual Studio Code használata üzenetek küldéséhez és fogadásához az eszköz és a IoT hub között](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Az eszköz által elküldhető adatfeldolgozás további módjairól folytassa a következő szakasszal.

## <a name="next-steps"></a>Következő lépések

Egy minta alkalmazást futtatott az érzékelő adatainak összegyűjtéséhez és az IoT hubhoz való elküldéséhez.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]