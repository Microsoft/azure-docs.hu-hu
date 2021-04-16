---
title: Azure IoT-eszközkezelés Visual Studio Cloud Explorerrel
description: A Cloud Explorerrel Visual Studio eszközkezelést Azure IoT Hub, amely a közvetlen metódusokat és az ikereszköz kívánt tulajdonságainak kezelési lehetőségeit is tartalmaz.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: 96f93325e0f17daaaf2bad91123fea81531ca152
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566962"
---
# <a name="use-cloud-explorer-for-visual-studio-for-azure-iot-hub-device-management"></a>A Cloud Explorer használata Visual Studio Azure IoT Hub eszközkezeléshez

![Végpontok között ábrázolt diagram](media/iot-hub-device-management-visual-studio/iot-e2e-simple.png)

Ebből a cikkből megtudhatja, hogyan használhatja a Cloud Explorert Visual Studio különböző felügyeleti lehetőségekkel a fejlesztői számítógépen. [A Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) hasznos Visual Studio, amely lehetővé teszi az Azure-erőforrások megtekintését, a tulajdonságaik vizsgálatát és a fő fejlesztői műveletek elvégzését a Visual Studio. Olyan felügyeleti lehetőségeket tartalmaz, amelyek segítségével különböző feladatokat hajthat végre.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Felügyeleti lehetőség          | Feladat                    |
|----------------------------|--------------------------------|
| Közvetlen metódusok             | Az eszköz úgy viselkedik, mint az üzenetek küldésének indítása vagy leállítása, vagy az eszköz újraindítása.                                        |
| Ikereszköz olvasása           | Lekérte egy eszköz jelentett állapotát. Az eszköz például azt jelenti, hogy a LED villog.                                    |
| Ikereszköz frissítése         | Az eszközt bizonyos körülmények között helyezze el, például zöldre állítja a LED-et, vagy 30 percre állítja a telemetria küldési időközét.         |
| A felhőből az eszközre küldött üzenetek   | Értesítések küldése egy eszközre. Például: "Nagyon valószínű, hogy ma esik. Ne felejtsen el egy énekkét."              |

A lehetőségek használatának különbségeit és útmutatóját [](iot-hub-devguide-d2c-guidance.md) az eszközről a felhőbe való kommunikációra vonatkozó útmutatóban és a felhőből az eszközre való kommunikációra vonatkozó [útmutatóban részletesen is le](iot-hub-devguide-c2d-guidance.md)tudja magyarázni.

Az ikereszközök JSON-dokumentumok, amelyek az eszközök állapotinformációit, például a metaadatokat, konfigurációkat és állapotokat tárolják. IoT Hub az ikereszközt minden olyan eszközhöz, amely csatlakozik hozzá. További információ az ikereszközről: [Ikereszköz – első lépések.](iot-hub-node-node-twin-getstarted.md)

## <a name="prerequisites"></a>Előfeltételek

- Aktív Azure-előfizetés.

- Egy Azure IoT Hub előfizetéshez.

- Microsoft Visual Studio 2017 9-es vagy újabb frissítése. Ez a cikk [a 2017 Visual Studio a 2019-es Visual Studio használja.](https://www.visualstudio.com/vs/)

- Cloud Explorer összetevő a Visual Studio telepítőből, amely alapértelmezés szerint ki van választva az Azure-beli számítási feladatokkal.

## <a name="update-cloud-explorer-to-latest-version"></a>A Cloud Explorer frissítése a legújabb verzióra

Az Visual Studio 2017-hez Visual Studio Cloud Explorer összetevő csak az eszközről a felhőbe és a felhőből az eszközre küldött üzenetek monitorozását támogatja. A 2017-es Visual Studio való használathoz töltse le és telepítse a legújabb [Cloud Explorert.](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)

## <a name="sign-in-to-access-your-hub"></a>Jelentkezzen be a központ eléréséhez

1. A Visual Studio a   >  **Cloud Explorer megtekintése gombra a** Cloud Explorer megnyitásához.

1. Válassza a Fiókkezelés ikont az előfizetések kiválasztásához.

    ![Fiókkezelés ikon](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Ha bejelentkezett az Azure-ba, megjelennek a fiókjai. Az Azure-ba való első bejelentkezéshez válassza a **Fiók hozzáadása lehetőséget.**

1. Válassza ki a használni kívánt Azure-előfizetéseket, majd válassza az **Alkalmaz lehetőséget.**

1. Bontsa ki az előfizetését, majd bontsa ki az **IoT Hubs csomópontot.**  Az egyes központok alatt láthatja az eszközeiket az egyes központokhoz. Kattintson a jobb gombbal az egyik eszközre a felügyeleti beállítások eléréséhez.

    ![Felügyeleti lehetőségek](media/iot-hub-device-management-visual-studio/management-options-vs2019.png)

## <a name="direct-methods"></a>Közvetlen metódusok

Közvetlen metódusok használata érdekében tegye a következőket:

1. Kattintson a jobb gombbal az eszközre, és válassza az **Eszköz közvetlen metódusának meghívása lehetőséget.**

1. Adja meg a metódus nevét és hasznos ját a Közvetlen metódus meghívása **mezőben,** majd kattintson az **OK gombra.**

    Az eredmények a **kimenetben jelennek meg.**

## <a name="update-device-twin"></a>Ikereszköz frissítése

Az ikereszköz szerkesztéséhez tegye a következőket:

1. Kattintson a jobb gombbal az eszközre, és válassza **az Ikereszköz szerkesztése lehetőséget.**

   Megnyílik **azure-iot-device-twin.jsfájlban** található fájl az ikereszköz tartalmával.

1. Szerkessze a **címkéket** vagy **a properties.desired** mezőket aazure-iot-device-twin.js **fájlban.**

1. Az **ikereszköz frissítéséhez** nyomja le a Ctrl+S billentyűkombinációt.

   Az eredmények a **kimenetben jelennek meg.**

## <a name="send-cloud-to-device-messages"></a>Üzenetküldés a felhőből az eszközökre

Ha üzenetet küld a IoT Hub eszközére, kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal az eszközre, és válassza a **C2D-üzenet küldése lehetőséget.**

1. Írja be az üzenetet a **C2D-üzenet küldése szövegbe,** majd kattintson az **OK gombra.**

   Az eredmények a **kimenetben jelennek meg.**

## <a name="next-steps"></a>Következő lépések

Megtanulta, hogyan használhatja a Cloud Explorert Visual Studio különböző felügyeleti lehetőségekkel.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
