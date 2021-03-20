---
title: Az Azure IoT Tools for VSCode használata az informatikai központ üzenetkezeléséhez
description: Ismerje meg, hogy miként lehet az Azure IoT Tools for Visual Studio Code-ot használni az eszköz Felhőbeli üzeneteinek figyelésére és a felhőben lévő üzenetek küldésére az Azure IoT Hubban.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/18/2019
ms.author: junhan
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: b9d8f0c2a573ba1749b414a389cd7181ddd5f95a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87321683"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>A Visual Studio Code-hoz készült Azure IoT Tools használata üzenetek küldéséhez és fogadásához az eszköz és a IoT Hub között

![Végpontok közötti diagram](./media/iot-hub-vscode-iot-toolkit-cloud-device-messaging/e-to-e-diagram.png)

Az [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) egy hasznos Visual Studio Code-bővítmény, amely megkönnyíti a IoT hub felügyeletét és a IoT alkalmazás-fejlesztését. Ez a cikk azt ismerteti, hogyan használható a Visual Studio Code-hoz készült Azure IoT Tools, hogy üzeneteket küldjön és fogadjon az eszköz és az IoT hub között.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>Mit fog tanulni?

Megtudhatja, hogyan figyelheti az eszközről a felhőbe irányuló üzenetek és a felhőből az eszközre irányuló üzenetek küldését a Visual Studio Code-hoz készült Azure IoT Tools használatával. Az eszközről a felhőbe irányuló üzenetek lehetnek az eszköz által gyűjtött, majd az IoT hub-ba küldött érzékelők adatai. A felhőből az eszközre irányuló üzenetek olyan parancsok, amelyeket az IoT hub az eszközhöz csatlakoztatott LED villogására küld az eszközre.

## <a name="what-you-will-do"></a>Mit fog tenni?

* Az eszközről a felhőbe irányuló üzenetek figyeléséhez használja a Visual Studio Code-hoz készült Azure IoT Tools eszközt.

* A felhőből az eszközre irányuló üzenetek küldéséhez használja a Visual Studio Code-hoz készült Azure IoT Tools eszközt.

## <a name="what-you-need"></a>Amire szükség lesz

* Aktív Azure-előfizetés.

* Az előfizetéshez tartozó Azure IoT hub.

* [Visual Studio Code](https://code.visualstudio.com/)

* A VS Code-hoz készült [Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) és az URL-cím másolása és beillesztése böngészőablakba:`vscode:extension/vsciot-vscode.azure-iot-tools`

## <a name="sign-in-to-access-your-iot-hub"></a>Jelentkezzen be az IoT hub eléréséhez

1. A VS Code **Explorer** nézetében bontsa ki az **Azure IoT hub-eszközök** szakaszt a bal alsó sarokban.

2. Kattintson a helyi menü **IoT hub kiválasztása** elemére.

3. A jobb alsó sarokban megjelenik egy előugró ablak, amely lehetővé teszi az Azure-ba való bejelentkezést az első alkalommal.

4. A bejelentkezést követően megjelenik az Azure-előfizetések listája, majd válassza az Azure-előfizetés és a IoT Hub lehetőséget.

5. Az eszközök listája az **Azure IoT hub-eszközök** lapon, néhány másodperc alatt jelenik meg.

   > [!Note]
   > A telepítést elvégezheti a **Set IoT Hub Connection String** (IoT Hub kapcsolati sztring beállítása) elemre kattintva is. Adja meg annak a IoT hub- **iothubowner** a házirend-kapcsolati karakterláncát, amelyhez a IoT-eszköz csatlakozik az előugró ablakban.

## <a name="monitor-device-to-cloud-messages"></a>Az eszközről a felhőbe irányuló üzenetek figyelése

Az eszközről az IoT hubhoz küldött üzenetek figyeléséhez kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal az eszközre, és válassza a **figyelés beépített esemény végpontjának elindítása** lehetőséget.

2. A figyelt üzenetek megjelennek a **kimenet**  >  **Azure IoT hub** nézetben.

3. A figyelés leállításához kattintson a jobb gombbal a **kimenet** nézetre, és válassza a **beépített esemény-végpont figyelésének leállítása** lehetőséget.

## <a name="send-cloud-to-device-messages"></a>Üzenetküldés a felhőből az eszközökre

Az alábbi lépéseket követve küldhet üzenetet az IoT hub-ról az eszközre:

1. Kattintson a jobb gombbal az eszközre, és válassza az **C2D-üzenet küldése az eszközre** lehetőséget.

2. Adja meg az üzenetet a beviteli mezőben.

3. Az eredmények a **kimenet**  >  **Azure IoT hub** nézetben jelennek meg.

## <a name="next-steps"></a>Következő lépések

Megtanulta, hogyan figyelheti az eszközről a felhőbe irányuló üzeneteket, és hogyan küldhet a felhőből az eszközre irányuló üzeneteket a IoT-eszköz és az Azure IoT Hub között.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]