---
title: A málna PI web Simulator összekötése az Azure IoT Hub (Node.js)
description: A málna PI webszimulátor összekapcsolásával Azure-IoT Hub a málna PI-hoz az Azure-felhőbe való adatküldéshez.
author: wesmc7777
manager: philmea
keywords: málna PI szimulátor, Azure IOT málna PI, málna PI IOT hub, málna PI az adatküldés a felhőbe, málna PI a felhőbe
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: wesmc
ms.custom:
- 'Role: Cloud Development'
- devx-track-js
ms.openlocfilehash: 702dee108577665eded6dd1a92203236d74e866e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91308337"
---
# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>A málna PI online szimulátor összekötése az Azure IoT Hub (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Ebben az oktatóanyagban elkezdi megtanulni a málna PI online szimulátor használatának alapjait. Ezután megtudhatja, hogyan csatlakoztatható zökkenőmentesen a PI-szimulátor a felhőhöz az [Azure IoT hub](about-iot-hub.md)használatával.

<p>
<div id="diag" style="width:100%; text-align:center">
<img src="media/iot-hub-raspberry-pi-web-simulator/3-banner.png" alt="Connect Raspberry Pi web simulator to Azure IoT Hub" width="400">
</div>
</p>
<p>
<div id="button" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/6-button-default.png" alt="Start Raspberry Pi simulator" width="400" onmouseover="this.src='media/iot-hub-raspberry-pi-web-simulator/5-button-click.png';" onmouseout="this.src='media/iot-hub-raspberry-pi-web-simulator/6-button-default.png';">
</a>
</div>
</p>

Ha fizikai eszközökkel rendelkezik, a kezdéshez látogasson el a [málna PI csatlakoztatása az Azure IoT hubhoz](iot-hub-raspberry-pi-kit-node-get-started.md) című oldalon.

## <a name="what-you-do"></a>Teendők

* Ismerkedjen meg a málna PI online szimulátor alapjaival.

* Hozzon létre egy IoT hubot.

* Regisztráljon egy eszközt a PI-hez az IoT hub-ban.

* Futtasson egy minta alkalmazást a PI-ben, hogy szimulált érzékelői adatait küldjön az IoT hub-nak.

Szimulált málna PI összekötése egy Ön által létrehozott IoT hubhoz. Ezután futtasson egy minta alkalmazást a szimulátor használatával az érzékelők létrehozásához. Végül elküldi az érzékelő adatait az IoT hubhoz.

## <a name="what-you-learn"></a>Ismertetett témák

* Azure IoT hub létrehozása és az új eszköz-kapcsolódási karakterlánc beszerzése. Ha nem rendelkezik Azure-fiókkal, mindössze néhány perc alatt [hozzon létre egy ingyenes Azure próbaverziós fiókot](https://azure.microsoft.com/free/) .

* A málna PI online szimulátor használata.

* Szenzorok adatainak küldése az IoT hubhoz.

## <a name="overview-of-raspberry-pi-web-simulator"></a>A málna PI web Simulator áttekintése

A gombra kattintva elindíthatja a málna PI online szimulátort.

> [!div class="button"]
> <a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#GetStarted" target="_blank">A málna PI szimulátor elindítása</a>

A webes szimulátorban három terület található.

1. Szerelési terület – Az alapértelmezett áramkör, amelyen a Pi egy BME280 érzékelővel és egy LED-del van összekötve. Ez a terület az előzetes verzióban zárolva van, így jelenleg nem módosítható.

2. Kódolási terület – Online kódszerkesztő, amelyben a Raspberry Pi-hoz írhat kódot. Az alapértelmezett mintaalkalmazás segítségével egy BME280 érzékelő adatait gyűjtheti, amelyeket az Azure IoT Hubba küld tovább. Az alkalmazás teljesen kompatibilis a valódi Pi eszközökkel. 

3. Integrált konzolablak – A kód kimenetét jeleníti meg. Ennek az ablaknak a felső részén három gomb található.

   * **Futtatás** – Futtatja a kódterületen lévő alkalmazást.

   * **Alaphelyzetbe állítás** – Visszaállítja az alapértelmezett mintaalkalmazást a kódolási területre.

   * **Összecsukás/Kibontás** – A jobb szélső gombbal a konzolablak kicsinyíthető le és bontható ki.

> [!NOTE]
> A málna PI webes szimulátor már előzetes verzióban érhető el. Szeretnénk hallani a hangját a [Gitter chaten](https://gitter.im/Microsoft/raspberry-pi-web-simulator). A forráskód nyilvános a [githubon](https://github.com/Azure-Samples/raspberry-pi-web-simulator).

![A PI online Simulator áttekintése](media/iot-hub-raspberry-pi-web-simulator/0-overview.png)

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hub-ban

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="run-a-sample-application-on-pi-web-simulator"></a>Mintaalkalmazás futtatása a Pi webes szimulátorán

1. Ügyeljen rá, hogy a kódolási területen az alapértelmezett mintaalkalmazással dolgozik. A 15. sor helyőrzőjét írja át az Azure IoT Hub eszközkapcsolati sztringjére.
1. 
   ![Az eszköz kapcsolati sztringjének cseréje](media/iot-hub-raspberry-pi-web-simulator/1-connectionstring.png)

2. Válassza a **Futtatás** vagy `npm start` a típus lehetőséget az alkalmazás futtatásához.

A következő kimenetnek kell megjelennie, amely megjeleníti az érzékelő adatait és az IoT hub ![ kimeneti-érzékelő adatait a málna PI-ből a IoT hub-ba küldött üzenetekben](media/iot-hub-raspberry-pi-web-simulator/2-run-application.png)

## <a name="read-the-messages-received-by-your-hub"></a>A hub által fogadott üzenetek olvasása

Az IoT hub által a szimulált eszközről fogadott üzenetek figyelésének egyik módja a Visual Studio Code-hoz készült Azure IoT Tools használata. További információ: az [Azure IoT Tools for Visual Studio Code használata üzenetek küldéséhez és fogadásához az eszköz és a IoT hub között](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Az eszköz által elküldhető adatfeldolgozás további módjairól folytassa a következő szakasszal.

## <a name="next-steps"></a>Következő lépések

Egy minta alkalmazást futtatott az érzékelő adatainak összegyűjtéséhez és az IoT hubhoz való elküldéséhez.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
