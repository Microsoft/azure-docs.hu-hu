---
title: Élő videó elemzése saját HTTP-modell használatával – Azure
description: Ebben a rövid útmutatóban a számítógép jövőképét fogja alkalmazni, hogy a saját HTTP-modelljével elemezze a (szimulált) IP-kamera élő videó-csatornáját.
ms.topic: quickstart
ms.date: 04/27/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: d3ba937abcc7bbfd9bb2afe7b15aec28ebb57446
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508511"
---
# <a name="quickstart-analyze-live-video-by-using-your-own-http-model"></a>Rövid útmutató: élő videó elemzése saját HTTP-modell használatával

Ez a rövid útmutató bemutatja, hogyan használható a Live Video Analytics a IoT Edge egy élő videó-hírcsatorna (szimulált) IP-kamerából való elemzéséhez. Megtudhatja, hogyan alkalmazhatja a Computer látási modellt az objektumok észlelésére. Az élő video-hírcsatornában található keretek egy részhalmazát a rendszer egy következtetési szolgáltatásnak továbbítja. Az eredményeket a rendszer IoT Edge hubhoz továbbítja. 

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/analyze-live-video-your-http-model-quickstart/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/analyze-live-video-your-http-model-quickstart/python/header.md)]
::: zone-end

## <a name="prerequisites"></a>Előfeltételek

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-http-model-quickstart/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-http-model-quickstart/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>A minta videó áttekintése

::: zone pivot="programming-language-csharp"
[!INCLUDE [review-sample-video](includes/analyze-live-video-your-http-model-quickstart/csharp/review-sample-video.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [review-sample-video](includes/analyze-live-video-your-http-model-quickstart/python/review-sample-video.md)]
::: zone-end

## <a name="overview"></a>Áttekintés

::: zone pivot="programming-language-csharp"
[!INCLUDE [overview](includes/analyze-live-video-your-http-model-quickstart/csharp/overview.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [overview](includes/analyze-live-video-your-http-model-quickstart/python/overview.md)]
::: zone-end

## <a name="create-and-deploy-the-media-graph"></a>A Media Graph létrehozása és üzembe helyezése

::: zone pivot="programming-language-csharp"
[!INCLUDE [create-deploy-media-graph](includes/analyze-live-video-your-http-model-quickstart/csharp/create-deploy-media-graph.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [create-deploy-media-graph](includes/analyze-live-video-your-http-model-quickstart/python/create-deploy-media-graph.md)]
::: zone-end

## <a name="interpret-results"></a>Eredmények értelmezése

::: zone pivot="programming-language-csharp"
[!INCLUDE [interpret-results](includes/analyze-live-video-your-http-model-quickstart/csharp/interpret-results.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [interpret-results](includes/analyze-live-video-your-http-model-quickstart/python/interpret-results.md)]
::: zone-end

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha más gyors útmutatókat szeretne kipróbálni, tartsa meg a létrehozott erőforrásokat. Ellenkező esetben lépjen a Azure Portalra, nyissa meg az erőforráscsoportot, válassza ki azt az erőforráscsoportot, amelyben ezt a rövid útmutatót futtatta, és törölje az összes erőforrást.

## <a name="next-steps"></a>Következő lépések

* Próbálja ki a [YoloV3 modell biztonságos verzióját](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/tls-yolov3-onnx/readme.md) , és telepítse azt a IoT Edge eszközre. 

Tekintse át a speciális felhasználókra vonatkozó további kihívásokat:

* Használjon olyan [IP-kamerát](https://en.wikipedia.org/wiki/IP_camera) , amely támogatja az RTSP-t az RTSP-szimulátor használata helyett. Megkeresheti az RTSP-t támogató IP-kamerákat a [ONVIF-megfelelőséggel](https://www.onvif.org/conformant-products/) rendelkező termékek oldalon. Keresse meg azokat az eszközöket, amelyek megfelelnek a G, S vagy T profiloknak.
* Az Azure Linux rendszerű virtuális gépek helyett AMD64-vagy x64-es Linux-eszközt használjon. Az eszköznek ugyanabban a hálózaton kell lennie, mint az IP-kamerának. A [Azure IoT Edge futtatókörnyezet telepítése Linux rendszeren](../../iot-edge/how-to-install-iot-edge.md)című témakör útmutatását követheti. Ezután regisztrálja az eszközt az Azure IoT Hub az [első IoT Edge modul üzembe helyezése virtuális Linux-eszközre](../../iot-edge/quickstart-linux.md)című témakör útmutatását követve.