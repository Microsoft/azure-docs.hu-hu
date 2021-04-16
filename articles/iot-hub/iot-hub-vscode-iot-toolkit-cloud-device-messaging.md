---
title: A Azure IoT Tools HASZNÁLATA a VSCode-hoz az it hub üzenetkezelésének kezelőjeként
description: Megtudhatja, hogyan használhatja a Azure IoT Tools for Visual Studio Code-et az eszközről a felhőbe küldött üzenetek figyelése és a felhőből az eszközökre küldött üzenetek Azure IoT Hub.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/18/2019
ms.author: junhan
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: 1c4a840233e576c528e9c58d57eca0b3d524bf4d
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566928"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>A Azure IoT Tools használatával Visual Studio Code-hoz üzeneteket küldhet és fogadhat az eszköz és a IoT Hub

![Végpontok között ábrázolt diagram](./media/iot-hub-vscode-iot-toolkit-cloud-device-messaging/e-to-e-diagram.png)

Ebből a cikkből megtudhatja, hogyan használhatja a Azure IoT Tools for Visual Studio Code-ban az eszközről a felhőbe küldött üzenetek figyelését és a felhőből az eszközre küldött üzenetek küldését. Az eszközről a felhőbe küldött üzenetek az eszköz által gyűjtött, majd az IoT Hubnak küldött érzékelőadatok. A felhőből az eszközre küldött üzenetek olyan parancsok, amelyek az IoT Hub által az eszközre küldött, az eszközhöz csatlakoztatott LED villogása miatt.

[Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) kódbővítmény hasznos Visual Studio, amely megkönnyíti a IoT Hub felügyeletét és az IoT-alkalmazások fejlesztését. Ez a cikk arra összpontosít, hogyan használhatja Azure IoT Tools Visual Studio Code-hoz az eszköz és az IoT Hub közötti üzenetek küldését és fogadását.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="prerequisites"></a>Előfeltételek

* Aktív Azure-előfizetés.

* Egy Azure IoT Hub az előfizetés alatt.

* [Visual Studio Code](https://code.visualstudio.com/)

* [Azure IoT Tools VS Code-ba,](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) vagy másolja és illessze be ezt az URL-címet egy böngészőablakba: `vscode:extension/vsciot-vscode.azure-iot-tools`

## <a name="sign-in-to-access-your-iot-hub"></a>Az IoT Hub eléréséhez jelentkezzen be

1. A VS Code **Explorer** nézetében bontsa ki **Azure IoT Hub bal** alsó sarokban található Eszközök szakaszt.

2. Kattintson **a IoT Hub** elemre a helyi menüben.

3. A jobb alsó sarokban megjelenik egy előugró ablak, hogy először bejelentkezzen az Azure-ba.

4. Miután bejelentkezik, megjelenik az Azure-előfizetés listája, majd válassza az Azure-előfizetés lehetőséget, és IoT Hub.

5. Az eszközlista néhány másodpercen belül megjelenik **Azure IoT Hub Eszközök** lapon.

   > [!Note]
   > A telepítést elvégezheti a **Set IoT Hub Connection String** (IoT Hub kapcsolati sztring beállítása) elemre kattintva is. Az előugró ablakban adja meg annak az IoT Hubnak az **iothubowner** szabályzatkapcsolati sztringet, amelyhez az IoT-eszköz csatlakozik.

## <a name="monitor-device-to-cloud-messages"></a>Eszközről a felhőbe küldött üzenetek figyelése

Az eszközről az IoT Hubra küldött üzenetek figyelése érdekében kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal az eszközre, és válassza a **Start Monitoring Built-in Event Endpoint (Beépített eseményvégpont figyelése) lehetőséget.**

2. A figyelt üzenetek az **OUTPUT (KIMENET)** Azure IoT Hub  >   jelennek meg.

3. A monitorozás leállításához kattintson a jobb gombbal a **KIMENET** nézetre, és válassza a Monitorozás beépített **eseményvégpontjának leállítása lehetőséget.**

## <a name="send-cloud-to-device-messages"></a>Üzenetküldés a felhőből az eszközökre

Ha üzenetet küld az IoT Hubról az eszközére, kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal az eszközére, és válassza a **C2D-üzenet küldése az eszközre lehetőséget.**

2. Írja be az üzenetet a beviteli mezőbe.

3. Az eredmények az **OUTPUT (KIMENET)** Azure IoT Hub  >   jelennek meg.

## <a name="next-steps"></a>Következő lépések

Megtanulta, hogyan figyelheti az eszközről a felhőbe küldött üzeneteket, és hogyan küldhet felhőből az eszközre üzeneteket az IoT-eszköz és a Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]