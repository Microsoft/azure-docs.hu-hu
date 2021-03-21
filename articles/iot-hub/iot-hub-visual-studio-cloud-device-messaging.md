---
title: Az Azure IoT Hub eszköz üzenetkezelésének kezelése a VS Cloud Explorer használatával
description: Megtudhatja, hogyan figyelheti a Cloud Explorer for Visual Studio eszközt az eszköz Felhőbeli üzeneteinek figyelésére és Felhőbeli üzenetek küldésére az Azure IoT Hubban.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: c56bb7030b2ebc12e3afc24e2d8cb29ce2dda0bf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "74079480"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Üzenetek küldése és fogadása az eszköz és a IoT Hub között a Cloud Explorer for Visual Studio használatával

![Végpontok közötti diagram](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

A [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) egy hasznos Visual Studio-bővítmény, amely lehetővé teszi, hogy megtekintse az Azure-erőforrásokat, megvizsgálják a tulajdonságaikat, és a Visual studióból is elvégezzenek kulcsfontosságú fejlesztői műveleteket. Ez a cikk azt ismerteti, hogyan használható a Cloud Explorer üzenetek küldése és fogadása az eszköz és a központ között.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-learn"></a>Ismertetett témák

Ebből a cikkből megtudhatja, hogyan használhatja a Cloud Explorer for Visual Studio eszközt az eszközről a felhőbe irányuló üzenetek figyeléséhez és a felhőből az eszközre irányuló üzenetek küldéséhez. Az eszközről a felhőbe irányuló üzenetek lehetnek az eszköz által gyűjtött, majd a IoT Hubnak küldött érzékelők adatai. A felhőből az eszközre irányuló üzenetek lehetnek a IoT Hub által az eszközre küldött parancsok. Tegyük fel például, hogy az eszközhöz csatlakozó LED villog.

## <a name="what-you-do"></a>Teendők

Ebben a cikkben a következő feladatokat hajtja végre:

- Az eszközről a felhőbe irányuló üzenetek figyeléséhez használja a Cloud Explorer for Visual Studio eszközt.

- Felhőből az eszközre irányuló üzenetek küldéséhez használja a Cloud Explorer for Visual Studio alkalmazást.

## <a name="what-you-need"></a>Amire szükség lesz

A következő előfeltételek szükségesek:

- Aktív Azure-előfizetés.

- Az előfizetéshez tartozó Azure-IoT Hub.

- Microsoft Visual Studio 2017 Update 9-es vagy újabb verzió. Ez a cikk a [Visual Studio 2019](https://www.visualstudio.com/vs/)-et használja.

- A Visual Studio telepítőből származó Cloud Explorer összetevő, amely alapértelmezés szerint az Azure munkaterhelés használatával van kiválasztva.

## <a name="update-cloud-explorer-to-latest-version"></a>A Cloud Explorer frissítése a legújabb verzióra

A Visual Studio 2017-es verziójának Cloud Explorer összetevője csak az eszközről a felhőbe irányuló és a felhőből az eszközre irányuló üzenetek figyelését támogatja. A Visual Studio 2017 használatához töltse le és telepítse a legújabb [Cloud Explorer böngészőt](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS).

## <a name="sign-in-to-access-your-hub"></a>Bejelentkezés a hub eléréséhez

A hub eléréséhez kövesse az alábbi lépéseket:

1. A Visual Studióban kattintson a Cloud Explorer **megtekintése** elemre  >   a Cloud Explorer megnyitásához.

1. Az előfizetések megjelenítéséhez válassza az Account Management (Fiókkezelés) ikont.

    ![Fiókkezelés ikon](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Ha bejelentkezett az Azure-ba, a fiókok megjelennek. Ha első alkalommal szeretne bejelentkezni az Azure-ba, válassza a **fiók hozzáadása** lehetőséget.

1. Válassza ki a használni kívánt Azure-előfizetéseket, majd válassza az **alkalmaz** lehetőséget.

1. Bontsa ki az előfizetést, majd bontsa ki az **IoT hubok** elemet.  Az egyes központokban megtekintheti az adott hubhoz tartozó eszközöket.

    ![Eszközök listája](media/iot-hub-visual-studio-cloud-device-messaging/hub-device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>Az eszközről a felhőbe irányuló üzenetek figyelése

Az eszközről a IoT Hub küldött üzenetek figyeléséhez kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal a IoT Hub vagy az eszközre, és válassza a **figyelés D2C üzenet** lehetőséget.

    ![D2C-üzenet figyelésének megkezdése](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message-vs2019.png)

1. A figyelt üzenetek a **kimenet** területen jelennek meg.

    ![Figyelési D2C üzenetének eredménye](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result-vs2019.png)

1. A figyelés leállításához kattintson a jobb gombbal bármelyik IoT Hubra vagy eszközre, és válassza a **figyelés D2C üzenet** lehetőséget.

## <a name="send-cloud-to-device-messages"></a>Üzenetküldés a felhőből az eszközökre

Ha üzenetet szeretne küldeni a IoT Hub az eszközére, kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal az eszközre, és válassza a **C2D-üzenet küldése** lehetőséget.

1. Adja meg az üzenetet a beviteli mezőben.

    ![C2D-üzenet küldése](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-test.png)

    Az eredmények a **kimenet** területen jelennek meg.

    ![C2D-üzenet eredményének küldése](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result-vs2019.png)

## <a name="next-steps"></a>Következő lépések

Megtanulta, hogyan figyelheti az eszközről a felhőbe irányuló üzeneteket, és hogyan küldhet a felhőből az eszközre irányuló üzeneteket a IoT-eszköz és az Azure IoT Hub között.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]