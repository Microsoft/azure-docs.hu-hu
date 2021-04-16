---
title: Azure IoT-eszközkezelés Azure IoT Tools VSCode-hoz
description: A Azure IoT Tools a Visual Studio Code for Azure IoT Hub eszközkezeléshez, amely tartalmazza a közvetlen metódusokat és az ikereszköz kívánt tulajdonságainak kezelési lehetőségeit.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: b48def283ea27fdd0eaa3230a2eb9a8327461ff1
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567013"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-for-azure-iot-hub-device-management"></a>Az Azure IoT Tools használata az Visual Studio Code Azure IoT Hub eszközkezeléshez

![Végpontok között diagram](media/iot-hub-get-started-e2e-diagram/2.png)

Ebből a cikkből megtudhatja, hogyan használhatja a Azure IoT Tools a Visual Studio Code-hoz különböző felügyeleti lehetőségekkel a fejlesztői gépen. [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) kódbővítmény hasznos Visual Studio, amely megkönnyíti a IoT Hub felügyeletét és az IoT-alkalmazások fejlesztését. Felügyeleti lehetőségeket tartalmaz, amelyek különböző feladatok elvégzésére használhatók.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Felügyeleti lehetőség          | Feladat                    |
|----------------------------|--------------------------------|
| Közvetlen metódusok             | Az eszközök úgy viselkednek, mint az üzenetek küldésének indítása vagy leállítása, vagy az eszköz újraindítása.                                        |
| Ikereszköz olvasása           | Lekérte az eszköz jelentett állapotát. Az eszköz például azt jelenti, hogy a LED villog.                                    |
| Ikereszköz frissítése         | Az eszközt bizonyos körülmények között helyezze el, például zöldre állítja a LED-et, vagy 30 percre állítja a telemetria küldési időközét.         |
| Felhőből az eszközre küldött üzenetek   | Értesítések küldése egy eszközre. Például: "Nagyon valószínű, hogy ma esni fog. Ne felejtsen el edzni egy eskedőt."              |

A lehetőségek használatának különbségeit és útmutatóját [](iot-hub-devguide-d2c-guidance.md) az eszközről a felhőbe való kommunikációra vonatkozó útmutatóban és a felhőből az eszközre való kommunikációra vonatkozó [útmutatóban láthatja részletesen.](iot-hub-devguide-c2d-guidance.md)

Az ikereszközök JSON-dokumentumok, amelyek az eszközök állapotinformációit (metaadatokat, konfigurációkat és állapotokat) tárolják. IoT Hub az ikereszközt minden olyan eszközhöz, amely csatlakozik hozzá. További információ az ikereszközről: [Ikereszköz – első lépések.](iot-hub-node-node-twin-getstarted.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

* Aktív Azure-előfizetés.
* Egy Azure IoT Hub az előfizetés alatt.
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure IoT Tools VS Code-ba, vagy](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) másolja ki ezt az URL-címet, és illessze be egy böngészőablakba: `vscode:extension/vsciot-vscode.azure-iot-tools` .

## <a name="sign-in-to-access-your-iot-hub"></a>Az IoT Hub eléréséhez jelentkezzen be

1. A VS Code **Explorer** nézetében bontsa ki **Azure IoT Hub bal** alsó sarokban található Eszközök szakaszt.

2. Kattintson **a IoT Hub** elemre a helyi menüben.

3. A jobb alsó sarokban megjelenik egy előugró ablak, hogy először bejelentkezzen az Azure-ba.

4. Miután bejelentkezik, megjelenik az Azure-előfizetés listája, majd válassza az Azure-előfizetés lehetőséget, és IoT Hub.

5. Az eszközlista néhány másodpercen belül megjelenik **Azure IoT Hub Eszközök** lapon.

   > [!Note]
   > A telepítést elvégezheti a **Set IoT Hub Connection String** (IoT Hub kapcsolati sztring beállítása) elemre kattintva is. Az előugró ablakban adja meg annak az IoT Hubnak az **iothubowner** szabályzatkapcsolati sztringet, amelyhez az IoT-eszköz csatlakozik.

## <a name="direct-methods"></a>Közvetlen metódusok

1. Kattintson a jobb gombbal az eszközre, és válassza a **Közvetlen metódus meghívása lehetőséget.** 

2. Adja meg a metódus nevét és hasznos adatát a beviteli mezőben.

3. Az eredmények az **OUTPUT**(KIMENET) Azure IoT Hub  >   jelennek meg.

## <a name="read-device-twin"></a>Ikereszköz olvasása

1. Kattintson a jobb gombbal az eszközre, és válassza **az Ikereszköz szerkesztése lehetőséget.** 

2. A **azure-iot-device-twin.jsfájlban** található fájl megnyílik az ikereszköz tartalmával.

## <a name="update-device-twin"></a>Ikereszköz frissítése

1. Szerkessze a **címkéket vagy a** **properties.desired mezőt.**

2. Kattintson a jobb gombbal a **fájlazure-iot-device-twin.jselemre.**

3. Az **ikereszköz frissítéséhez** válassza az Ikereszköz frissítése lehetőséget.

## <a name="send-cloud-to-device-messages"></a>Üzenetküldés a felhőből az eszközökre

Ha üzenetet küld az IoT Hubról az eszközére, kövesse az alábbi lépéseket:
 
1. Kattintson a jobb gombbal az eszközre, és válassza a **C2D-üzenet küldése az eszközre lehetőséget.** 

2. Írja be az üzenetet a beviteli mezőbe.

3. Az eredmények az **OUTPUT**(KIMENET) Azure IoT Hub  >   jelennek meg.

## <a name="next-steps"></a>Következő lépések

Megtanulta, hogyan használhatja a Azure IoT Tools a Visual Studio Code-hoz különböző felügyeleti lehetőségekkel.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
