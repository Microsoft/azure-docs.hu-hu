---
title: 'Oktatóanyag: a video Analytics üzembe helyezése – objektumok és mozgásészlelési észlelések Azure IoT Central alkalmazás sablonja'
description: Oktatóanyag – ez az útmutató összefoglalja az Azure IoT Central-alkalmazások a video Analytics-Object és a Motion Detection alkalmazás sablon használatával történő üzembe helyezésének lépéseit.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: c578da7e83a39f84e72b550038bd87dde3c61d28
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727464"
---
# <a name="tutorial-how-to-deploy-an-iot-central-application-using-the-video-analytics---object-and-motion-detection-application-template"></a>Oktatóanyag: IoT Central-alkalmazás üzembe helyezése a video Analytics – Object és Motion Detection alkalmazás sablon használatával

A Key *video Analytics – Object és Motion Detection alkalmazás-* összetevők áttekintését lásd: [Object and Motion Detection video Analytics Application Architecture](architecture-video-analytics.md).

A következő videó bemutatja, hogyan használható a _video Analytics-Object és a Motion Detection alkalmazás sablonja_ egy IoT Central-megoldás üzembe helyezéséhez:

> [!VIDEO https://www.youtube.com/embed/Bo3FziU9bSA]

Ebben az oktatóanyagban a következőket sajátíthatja el:

> [!div class="checklist"]
> * Az alkalmazás üzembe helyezése
> * Az alkalmazáshoz csatlakozó IoT Edge-példány üzembe helyezése
> * Az alkalmazás figyelése és kezelése

## <a name="prerequisites"></a>Előfeltételek

Az Azure-előfizetés használata ajánlott. Alternatív megoldásként ingyenes, 7 napos próbaverziót is használhat. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egyet az [Azure regisztrációs oldalán](https://aka.ms/createazuresubscription).

## <a name="deploy-the-application"></a>Az alkalmazás üzembe helyezése

Az alábbi lépések végrehajtásával telepítsen egy IoT Central alkalmazást a video Analytics alkalmazás sablonjának használatával:

1. Hajtsa végre a [video Analytics-alkalmazás létrehozása az Azure-ban IoT Central (YOLO v3)](tutorial-video-analytics-create-app-yolo-v3.md) vagy a [videó létrehozása az azure-ban IoT Central (OpenVINO &trade; )](tutorial-video-analytics-create-app-openvino.md) oktatóanyagot a következőkhöz:
    - Azure Media Services-fiók létrehozása
    - Hozza létre a IoT Central alkalmazást a video Analytics-Object és a Motion Detection alkalmazás sablonból.
    - Átjáró-eszköz konfigurálása a IoT Central alkalmazásban. Az átjáró lehetővé teszi, hogy a kamera-eszközök csatlakozzanak az alkalmazáshoz.

1. Hajtsa végre a következőt: [IoT Edge-példány létrehozása videó-elemzéshez (linuxos virtuális gép)](tutorial-video-analytics-iot-edge-vm.md) vagy az [oktatóanyag: IoT Edge-példány létrehozása a video Analytics (Intel NUC)](tutorial-video-analytics-iot-edge-nuc.md) oktatóanyaghoz a következőkhöz:
    - Hozzon létre egy Azure-beli virtuális gépet a telepített Azure IoT Edge futtatókörnyezettel.-Készítse elő a IoT Edge telepítést a video Analytics modul üzemeltetéséhez.
    - A IoT Edge eszköz csatlakoztatása a IoT Central alkalmazáshoz.

1. Töltse ki a [figyelőt, és kezelje a video Analytics-alkalmazás](tutorial-video-analytics-manage.md) oktatóanyagát a következőre:
    - Vegyen fel objektumokat és mozgásészlelési kamerákat az IoT Central-alkalmazásban található átjáróba.
    - Indítsa el a kamera feldolgozását.
    - A rögzített videók AMS-ben való megtekintéséhez telepítsen egy helyi médialejátszó alkalmazást.
    - Az észlelt objektumokat megjelenítő rögzített videó megtekintése.
    - Rendben.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett az alkalmazással, a következőképpen távolíthatja el az összes létrehozott erőforrást:

1. A IoT Central alkalmazásban navigáljon az **alkalmazás** lapjára az **Adminisztráció** szakaszban. Ezután válassza a **Törlés** elemet.
1. A Azure Portal törölje a **LVA-RG** erőforráscsoportot.
1. A helyi gépen állítsa le az **amp-Viewer** Docker-tárolót.

## <a name="next-steps"></a>Következő lépések

Most áttekintheti a video Analytics alkalmazás-sablon üzembe helyezésének és használatának lépéseit:

> [!div class="nextstepaction"]
> [Video Analytics-alkalmazás létrehozása az Azure IoT Central (YOLO v3)](tutorial-video-analytics-create-app-yolo-v3.md) vagy

> [!div class="nextstepaction"]
> [Hozzon létre egy videó-elemzést az Azure IoT Centralban (OpenVINO &trade; )](tutorial-video-analytics-create-app-openvino.md) a kezdéshez.