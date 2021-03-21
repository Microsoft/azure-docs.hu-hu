---
ms.openlocfilehash: 0e800668bea2f744f8862292b21be814858b0c16
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97486774"
---
> [!div class="mx-imgBorder"]
> :::image type="content" source="../../../media/quickstarts/overview-qs5.svg" alt-text="A jelek folyamata":::

Ez az ábra azt mutatja be, hogyan áramlik be a gyors útmutatóban szereplő jelek. Az [Edge-modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) szimulál egy Real-Time Streaming Protocol-(RTSP-) kiszolgálót futtató IP-kamerát. Az [RTSP-forrás](../../../media-graph-concept.md#rtsp-source) csomópontja lekéri a videó csatornáját a kiszolgálóról, és a [http-bővítmény processzor](../../../media-graph-concept.md#http-extension-processor) -csomópontjára küldi a videó kereteket. 

A HTTP-bővítmény csomópont egy proxy szerepét játssza le. Ez a mező alapján a beérkező képkockákat is megtekinti, `samplingOptions` és a képkockákat a megadott képtípusra alakítja. Ezt követően továbbítja a képet a REST-ben egy másik Edge-modulba, amely egy HTTP-végpont mögötti AI-modellt futtat. Ebben a példában az Edge-modul a [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) modell használatával készült, amely számos típusú objektumot képes érzékelni. A HTTP-bővítmény processzor-csomópontja összegyűjti az észlelés eredményeit, és közzéteszi az eseményeket a [IoT hub](../../../media-graph-concept.md#iot-hub-message-sink) fogadó csomópontban. A csomópont ezután elküldi ezeket az eseményeket [IoT Edge hubhoz](../../../../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

Ebben a rövid útmutatóban a következőket fogja megtekinteni:

1. A Media Graph létrehozása és üzembe helyezése.
1. Az eredmények értelmezése.
1. Az erőforrások eltávolítása.
