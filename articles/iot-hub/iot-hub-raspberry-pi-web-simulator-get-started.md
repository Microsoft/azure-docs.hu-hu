---
title: Raspberry Pi szimulált felhőbe (Node.js) – csatlakozás Raspberry Pi-webszimulátor Azure IoT hubra |} A Microsoft Docs
description: Raspberry Pi-webszimulátor csatlakozhat az Azure IoT Hub használata a Raspberry Pi adatokat küldeni az Azure-felhőben.
author: wesmc7777
manager: philmea
keywords: raspberry pi simulator, azure iot raspberry pi, raspberry pi iot hub, raspberry pi send data to cloud, raspberry pi to cloud
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: wesmc
ms.openlocfilehash: 9af2f0860c415ddb701e24ed8a698fae36d42e1f
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67838711"
---
# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>Online szimulátor Raspberry Pi csatlakoztatása Azure IoT hubhoz (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Ez az oktatóanyag első lépésként, tanulás a Raspberry Pi online szimulátor való használatának alapjait. Ezután megismerheti, hogyan zökkenőmentes csatlakozás használatával a felhőbe a Pi szimulátor [Azure IoT Hub](about-iot-hub.md).

Ha a fizikai eszközökön, látogasson el a [Raspberry Pi csatlakoztatása Azure IoT Hub](iot-hub-raspberry-pi-kit-node-get-started.md) a kezdéshez.

<p>
<div id="diag" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/3-banner.png" alt="Connect Raspberry Pi web simulator to Azure IoT Hub" width="400">
</div>
<p>
<div id="button" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/6-button-default.png" alt="Start Raspberry Pi simulator" width="400" onmouseover="this.src='media/iot-hub-raspberry-pi-web-simulator/5-button-click.png';" onmouseout="this.src='media/iot-hub-raspberry-pi-web-simulator/6-button-default.png';">
</div>

## <a name="what-you-do"></a>TEENDŐ

* Ismerje meg, a Raspberry Pi online szimulátor alapjait.

* Hozzon létre egy IoT hubot.

* Eszköz regisztrálása az IoT hub pi.

* A mintaalkalmazás futtatása az IoT hubra szimulált érzékelői adatokat küldhet Pi-on.

Szimulált Raspberry Pi csatlakozni az IoT hub által létrehozott. Ezután meg egy mintaalkalmazást a szimulátor érzékelőktől kapott adatok létrehozásához futtassa. Végül az érzékelő adatokat küld az IoT hubnak.

## <a name="what-you-learn"></a>Ismertetett témák

* Útmutató az Azure IoT hub létrehozása és az új eszköz kapcsolati karakterláncának beszerzése. Ha nem rendelkezik Azure-fiók [hozzon létre egy ingyenes Azure próbafiókot](https://azure.microsoft.com/free/) mindössze néhány perc múlva.

* Raspberry Pi online szimulátor használatának módját.

* Hogyan küldhet az IoT hub érzékelői adatokat.

## <a name="overview-of-raspberry-pi-web-simulator"></a>Raspberry Pi-webszimulátor áttekintése

Kattintson a gombra kattintva indítsa el a Raspberry Pi online szimulátor.

> [!div class="button"]
> <a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#GetStarted" target="_blank">Indítsa el a Raspberry Pi-szimulátor</a>

Három olyan terület a webalkalmazás-szimulátorban történő.

1. Szerelvény terület – az alapértelmezett kapcsolatcsoport-e, hogy kapcsolódik-e a Pi BME280 érzékelő és a egy LED. A nézet zárolva van az előzetes verzió ezért jelenleg nem hajtható végre testreszabása.

2. Kódolási terület – egy online Kódszerkesztő, a Raspberry Pi-kódot. Az alapértelmezett mintaalkalmazás segít BME280 érzékelő érzékelői adatokat gyűjteni, és az Azure IoT hubra. Az alkalmazás teljes mértékben kompatibilis a Pi valódi eszközön. 

3. Integrált konzol ablakának - kódját kimenetét mutatja. Ez az ablak tetején lévő nincsenek három gombbal.

   * **Futtatás** – az alkalmazás futtatásához a kódolási területen.

   * **Alaphelyzetbe** – a kódolási terület visszaállítása az alapértelmezett mintaalkalmazáshoz.

   * **Modellrészek/Kibontás** – a jobb oldalon van egy gombot, hogy a konzolablakban modellrészek/bontsa ki.

> [!NOTE]
> A Raspberry Pi-webszimulátor jelenleg előzetes verzióban érhető el. A Hangminta hallani szeretnénk a [Gitter Chatroom](https://gitter.im/Microsoft/raspberry-pi-web-simulator). A forráskódja a nyilvános [GitHub](https://github.com/Azure-Samples/raspberry-pi-web-simulator).

![Online szimulátor Pi áttekintése](media/iot-hub-raspberry-pi-web-simulator/0-overview.png)

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hubban

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="run-a-sample-application-on-pi-web-simulator"></a>A mintaalkalmazás futtatása a Pi-webszimulátor

1. Kódolási terület, győződjön meg arról, az alapértelmezett mintaalkalmazás dolgozik. A sor 15 a helyőrzőt cserélje le az Azure IoT hub eszköz kapcsolati karakterláncát.
1. 
   ![Cserélje le az eszköz kapcsolati karakterláncának](media/iot-hub-raspberry-pi-web-simulator/1-connectionstring.png)

2. Válassza ki **futtatása** vagy típus `npm start` az alkalmazás futtatásához.

Amely az érzékelőktől kapott adatok és az IoT hubnak küldött üzeneteket jeleníti meg a következő kimenetnek kell megjelennie ![kimeneti - Raspberry Pi az IoT hubnak küldött érzékelőktől kapott adatok](media/iot-hub-raspberry-pi-web-simulator/2-run-application.png)

## <a name="read-the-messages-received-by-your-hub"></a>Olvassa el a hub által fogadott üzeneteket

Egy a szimulált eszközről az IoT hub által fogadott üzenetek figyeléséhez módja az Azure IoT-eszközök használata a Visual Studio Code. További tudnivalókért lásd: [használata az Azure IoT Tools for Visual Studio Code használatával az eszközön, és az IoT Hub közötti üzenetek küldése és fogadása](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

A további lehetőségek a eszköz által küldött adatok feldolgozásához folytassa a következő szakaszban.

## <a name="next-steps"></a>További lépések

Egy mintaalkalmazás érzékelőktől kapott adatok összegyűjtésére, és küldje el az IoT hubnak küldött futtatott.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
