---
title: A Motion & Record Videójának észlelése az Edge-eszközökön – Azure
description: Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a IoT Edge élő videós elemzéseket az élő videó-hírcsatornák (szimulált) IP-kameráról való elemzéséhez, hogy észlelje, hogy van-e mozgás, és ha igen, jegyezzen fel egy MP4-videoklipet a peremhálózati eszköz helyi fájljába.
ms.topic: quickstart
ms.date: 04/27/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: e4334cdd14242337a2a870f31886606020654685
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96498353"
---
# <a name="quickstart-detect-motion-and-record-video-on-edge-devices"></a>Rövid útmutató: mozgás észlelése és videó rögzítése a peremhálózati eszközökön
 
Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a Live Video Analytics szolgáltatást a IoT Edgeon az élő videó hírcsatornájának (szimulált) IP-kamerából való elemzéséhez. Bemutatja, hogyan lehet észlelni, hogy van-e mozgás, és ha igen, jegyezzen fel egy MP4-videoklipet a peremhálózati eszköz helyi fájljába. A rövid útmutató egy Azure-beli virtuális gépet használ IoT Edge eszközként, és egy szimulált élő videó streamet is használ. 

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/header.md)]
::: zone-end

## <a name="prerequisites"></a>Előfeltételek

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>A minta videó áttekintése

::: zone pivot="programming-language-csharp"
[!INCLUDE [review-sample-video](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/review-sample-video.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [review-sample-video](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/review-sample-video.md)]
::: zone-end

## <a name="overview"></a>Áttekintés

::: zone pivot="programming-language-csharp"
[!INCLUDE [overview](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/overview.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [overview](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/overview.md)]
::: zone-end

## <a name="examine-and-edit-the-sample-files"></a>A mintaadatok vizsgálata és szerkesztése

::: zone pivot="programming-language-csharp"
[!INCLUDE [examine-edit-sample-files](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/examine-edit-sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [examine-edit-sample-files](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/examine-edit-sample-files.md)]
::: zone-end

## <a name="review---check-the-modules-status"></a>Ellenőrzés – a modulok állapotának ellenőrzése

::: zone pivot="programming-language-csharp"
[!INCLUDE [check-modules-status](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/check-modules-status.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [check-modules-status](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/check-modules-status.md)]
::: zone-end

## <a name="review---prepare-for-monitoring-events"></a>Felülvizsgálat – felkészülés a figyelési eseményekre

::: zone pivot="programming-language-csharp"
[!INCLUDE [prepare-monitoring-events](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/prepare-monitoring-events.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prepare-monitoring-events](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/prepare-monitoring-events.md)]
::: zone-end

## <a name="run-the-sample-program"></a>A minta program futtatása

::: zone pivot="programming-language-csharp"
[!INCLUDE [run-sample-program](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/run-sample-program.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [run-sample-program](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/run-sample-program.md)]
::: zone-end

## <a name="interpret-results"></a>Eredmények értelmezése 

::: zone pivot="programming-language-csharp"
[!INCLUDE [interpret-results](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/interpret-results.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [interpret-results](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/interpret-results.md)]
::: zone-end

## <a name="play-the-mp4-clip"></a>MP4-klip lejátszása

::: zone pivot="programming-language-csharp"
[!INCLUDE [play-mp4-clip](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/play-mp4-clip.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [play-mp4-clip](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/play-mp4-clip.md)]
::: zone-end

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha szeretné kipróbálni a többi rövid útmutatót, tartsa meg a létrehozott erőforrásokat. Ellenkező esetben a Azure Portal nyissa meg az erőforráscsoportot, válassza ki azt az erőforráscsoportot, amelyben a rövid útmutatót futtatta, majd törölje az összes erőforrást.

## <a name="next-steps"></a>Következő lépések

* Kövesse a [Live Video Analytics futtatása a saját modell](use-your-model-quickstart.md) rövid útmutatójában a mesterséges intelligenciát élő video-hírcsatornák alkalmazásához.
* Tekintse át a speciális felhasználókra vonatkozó további kihívásokat:

    * Használjon olyan [IP-kamerát](https://en.wikipedia.org/wiki/IP_camera) , amely támogatja az RTSP-t az RTSP-szimulátor használata helyett. Az RTSP-t támogató IP-kamerákat a [ONVIF-megfelelőségi termékek](https://www.onvif.org/conformant-products) lapon találja. Keresse meg azokat az eszközöket, amelyek megfelelnek a G, S vagy T profiloknak.
    * A Linux rendszerű virtuális gépek helyett AMD64 vagy x64 Linux-eszközt használjon az Azure-ban. Az eszköznek ugyanabban a hálózaton kell lennie, mint az IP-kamerának. Kövesse a következő témakör utasításait: [Install Azure IoT Edge Runtime on Linux](../../iot-edge/how-to-install-iot-edge.md). Ezután kövesse az [első IoT Edge modul üzembe helyezése egy virtuális Linux-eszközön](../../iot-edge/quickstart-linux.md) című témakör útmutatását az eszköz Azure IoT hub való regisztrálásához.