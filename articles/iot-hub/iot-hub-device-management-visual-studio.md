---
title: Azure IoT-eszközkezelés w/Visual Studio Cloud Explorer
description: Használja a Cloud Explorer for Visual Studio for Azure IoT Hub eszközkezelés szolgáltatást, amely a közvetlen metódusokat és a Twin kívánt tulajdonságok kezelési lehetőségeit is felhasználja.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: 6fe5a45dda6632c56b3c6714827950e25e7d26af
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "73953187"
---
# <a name="use-cloud-explorer-for-visual-studio-for-azure-iot-hub-device-management"></a>A Cloud Explorer használata az Azure-hoz készült Visual Studio IoT Hub-eszközök kezeléséhez

![Végpontok közötti diagram](media/iot-hub-device-management-visual-studio/iot-e2e-simple.png)

A [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) egy hasznos Visual Studio-bővítmény, amely lehetővé teszi, hogy megtekintse az Azure-erőforrásokat, megvizsgálják a tulajdonságaikat, és a Visual studióból is elvégezzenek kulcsfontosságú fejlesztői műveleteket. Olyan felügyeleti lehetőségeket tartalmaz, amelyek különböző feladatok elvégzésére használhatók.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Felügyeleti lehetőség          | Feladat                    |
|----------------------------|--------------------------------|
| Közvetlen metódusok             | Hajtson végre olyan eszközt, amely elindítja vagy leállítja az üzenetek küldését vagy az eszköz újraindítását.                                        |
| Írásvédett eszköz – Twin           | Egy eszköz jelentett állapotának beolvasása. Az eszköz például azt jelenti, hogy a LED azonnal villog.                                    |
| Eszköz dupla frissítése         | Helyezzen egy eszközt bizonyos állapotba, például állítsa be a LED-et zöldre, vagy állítsa a telemetria küldési intervallumát 30 percre.         |
| Felhőből az eszközre irányuló üzenetek   | Értesítések küldése egy eszközre. Például: "nagyon valószínű, hogy még ma is esik az eső. Ne felejtsen el egy esernyőt bevinni. "              |

További információ az ilyen beállításokkal kapcsolatos különbségekről és útmutatásról: az [eszközről a felhőbe irányuló kommunikációs útmutató](iot-hub-devguide-d2c-guidance.md) és a [felhőből az eszközre irányuló kommunikációs útmutató](iot-hub-devguide-c2d-guidance.md).

Az ikereszközök JSON-dokumentumok, amelyek az eszközök állapotinformációit, például a metaadatokat, konfigurációkat és állapotokat tárolják. A IoT Hub minden olyan eszközön megtartja a különálló eszközt, amely csatlakozik hozzá. További információ az eszközök Twins-ról: Ismerkedés [az eszközök ikrekkel](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Ismertetett témák

Ebből a cikkből megtudhatja, hogyan használhatja a Cloud Explorer for Visual Studio alkalmazást a fejlesztői számítógép különböző felügyeleti lehetőségeivel.

## <a name="what-you-do"></a>Teendők

Ebből a cikkből megtudhatja, hogyan futtathatja a Cloud Explorer for Visual Studio alkalmazást különböző felügyeleti lehetőségekkel.

## <a name="what-you-need"></a>Amire szükség lesz

A következő előfeltételek szükségesek:

- Aktív Azure-előfizetés.

- Az előfizetéshez tartozó Azure-IoT Hub.

- Microsoft Visual Studio 2017 Update 9-es vagy újabb verzió. Ez a cikk a [Visual studio 2017 vagy a Visual studio 2019](https://www.visualstudio.com/vs/)használatát ismerteti.

- A Visual Studio Installer Cloud Explorer összetevője, amely alapértelmezés szerint az Azure számítási feladattal van kiválasztva.

## <a name="update-cloud-explorer-to-latest-version"></a>A Cloud Explorer frissítése a legújabb verzióra

A Visual Studio 2017-es verziójának Cloud Explorer összetevője csak az eszközről a felhőbe irányuló és a felhőből az eszközre irányuló üzenetek figyelését támogatja. A Visual Studio 2017 használatához töltse le és telepítse a legújabb [Cloud Explorer böngészőt](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS).

## <a name="sign-in-to-access-your-hub"></a>Bejelentkezés a hub eléréséhez

1. A Visual Studióban kattintson a Cloud Explorer **megtekintése** elemre  >   a Cloud Explorer megnyitásához.

1. Az előfizetések megjelenítéséhez válassza az Account Management (Fiókkezelés) ikont.

    ![Fiókkezelés ikon](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Ha bejelentkezett az Azure-ba, a fiókok megjelennek. Ha első alkalommal szeretne bejelentkezni az Azure-ba, válassza a **fiók hozzáadása** lehetőséget.

1. Válassza ki a használni kívánt Azure-előfizetéseket, majd válassza az **alkalmaz** lehetőséget.

1. Bontsa ki az előfizetést, majd bontsa ki az **IoT hubok** elemet.  Az egyes központokban megtekintheti az adott hubhoz tartozó eszközöket. Kattintson a jobb gombbal egy eszközre a felügyeleti lehetőségek eléréséhez.

    ![Felügyeleti beállítások](media/iot-hub-device-management-visual-studio/management-options-vs2019.png)

## <a name="direct-methods"></a>Közvetlen metódusok

A közvetlen módszerek használatához hajtsa végre a következő lépéseket:

1. Kattintson a jobb gombbal az eszközre, és válassza a **közvetlen eszköz metódusának meghívása** lehetőséget.

1. Adja meg a metódus nevét és a hasznos adatokat a **közvetlen hívási metódusban**, majd kattintson **az OK gombra**.

    Az eredmények megjelennek a **kimenetben**.

## <a name="update-device-twin"></a>Eszköz dupla frissítése

Az eszközök kettős szerkesztéséhez hajtsa végre a következő lépéseket:

1. Kattintson a jobb gombbal az eszközre, és válassza az **eszközök dupla szerkesztése** lehetőséget.

   Megnyílik egy **azure-iot-device-twin.jsa** fájlon, amely az eszköz iker tartalmát nyitja meg.

1. Szerkessze a **címkéket** vagy **tulajdonságokat. a kívánt** mezőket a fájl **azure-iot-device-twin.js** .

1. Nyomja le a **CTRL + S** billentyűkombinációt az eszköz dupla frissítéséhez.

   Az eredmények megjelennek a **kimenetben**.

## <a name="send-cloud-to-device-messages"></a>Üzenetküldés a felhőből az eszközökre

Ha üzenetet szeretne küldeni a IoT Hub az eszközére, kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal az eszközre, és válassza a **C2D-üzenet küldése** lehetőséget.

1. Adja meg az üzenetet a **C2D küldése üzenetben** , majd kattintson **az OK gombra**.

   Az eredmények megjelennek a **kimenetben**.

## <a name="next-steps"></a>Következő lépések

Megtanulta, hogyan használhatja a Cloud Explorer for Visual Studio különböző felügyeleti lehetőségeket.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
