---
title: A VS Cloud Explorer használata az Azure IoT Hub kezeléséhez
description: Megtudhatja, hogyan használhatja a Cloud Explorer for Visual Studio eszközről a felhőbe küldött üzenetek figyelését és a felhőből az eszközökre küldött üzenetek küldését Azure IoT Hub.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: 8461a77d06a63c2ac319323a91b5577ca4dce1cf
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567030"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>A Cloud Explorerrel Visual Studio küldhet és fogadhat üzeneteket az eszköz és a IoT Hub

![Végpontok között diagram](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

Ebből a cikkből megtudhatja, hogyan használhatja a Cloud Explorer for Visual Studio az eszközről a felhőbe küldött üzenetek figyelését és a felhőből az eszközre küldött üzenetek küldését. Az eszközről a felhőbe küldött üzenetek az eszköz által gyűjtött, majd az eszköznek küldött érzékelőadatok IoT Hub. A felhőből az eszközre küldött üzenetek olyan parancsok, IoT Hub küld az eszközre. Például villogjon az eszközhöz csatlakoztatott LED-ről.

[A Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) hasznos Visual Studio, amely lehetővé teszi az Azure-erőforrások megtekintését, a tulajdonságaik vizsgálatát és a fő fejlesztői műveletek elvégzését a Visual Studio. Ez a cikk arra összpontosít, hogyan küldhet és fogadhat üzeneteket az eszköz és a központ között a Cloud Explorerrel.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="prerequisites"></a>Előfeltételek

- Aktív Azure-előfizetés.

- Egy Azure IoT Hub előfizetéshez.

- Microsoft Visual Studio 2017 9-es vagy újabb frissítése. Ez a cikk [a 2019 Visual Studio t használja.](https://www.visualstudio.com/vs/)

- A Cloud Explorer összetevő a Visual Studio telepítőből, amely alapértelmezés szerint ki van választva az Azure-beli számítási feladatokkal.

## <a name="update-cloud-explorer-to-latest-version"></a>A Cloud Explorer frissítése a legújabb verzióra

Az Visual Studio 2017-hez Visual Studio Cloud Explorer összetevő csak az eszközről a felhőbe és a felhőből az eszközre küldött üzenetek monitorozását támogatja. A 2017-es Visual Studio való használathoz töltse le és telepítse a legújabb [Cloud Explorert.](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)

## <a name="sign-in-to-access-your-hub"></a>Jelentkezzen be a központ eléréséhez

A központ eléréséhez kövesse az alábbi lépéseket:

1. A Visual Studio a   >  **Cloud Explorer megtekintése gombra a** Cloud Explorer megnyitásához.

1. Válassza a Fiókkezelés ikont az előfizetések kiválasztásához.

    ![Fiókkezelés ikon](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Ha bejelentkezett az Azure-ba, megjelennek a fiókjai. Az Azure-ba való első bejelentkezéshez válassza a **Fiók hozzáadása lehetőséget.**

1. Válassza ki a használni kívánt Azure-előfizetéseket, majd válassza az **Alkalmaz lehetőséget.**

1. Bontsa ki az előfizetését, majd bontsa ki az **IoT Hubs csomópontot.**  Az egyes központok alatt láthatja az eszközeiket az egyes központokhoz.

    ![Eszközlista](media/iot-hub-visual-studio-cloud-device-messaging/hub-device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>Az eszközről a felhőbe küldött üzenetek figyelése

Az eszközről a saját eszközére küldött üzenetek IoT Hub kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal a IoT Hub vagy eszközére, és válassza a **Start Monitoring D2C Message (D2C-üzenetek figyelése) lehetőséget.**

    ![D2C-üzenetek monitorozásának elkezdése](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message-vs2019.png)

1. A figyelt üzenetek a Kimenet alatt **jelennek meg.**

    ![A D2C-üzenetek eredményének monitorozása](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result-vs2019.png)

1. A figyelés leállításhoz kattintson a jobb gombbal bármelyik IoT Hub vagy eszközre, és válassza a **Stop Monitoring D2C Message (D2C-üzenetek** figyelése) lehetőséget.

## <a name="send-cloud-to-device-messages"></a>Üzenetküldés a felhőből az eszközökre

Ha üzenetet küld a IoT Hub eszközére, kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal az eszközre, és válassza a **C2D-üzenet küldése lehetőséget.**

1. Írja be az üzenetet a beviteli mezőbe.

    ![C2D-üzenet küldése](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-test.png)

    Az eredmények a Kimenet **alatt jelennek meg.**

    ![C2D-üzenet küldése eredmény](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result-vs2019.png)

## <a name="next-steps"></a>Következő lépések

Megtanulta, hogyan figyelheti az eszközről a felhőbe küldött üzeneteket, és hogyan küldhet felhőből az eszközre üzeneteket az IoT-eszköz és a Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]