---
ms.openlocfilehash: e96975bce5e5d99db4ea78c80c027a95d4b662e3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92038440"
---
![Áttekintés](../../../media/quickstarts/gRPC-extension.svg)

Ez az ábra azt mutatja be, hogyan áramlik be a gyors útmutatóban szereplő jelek. Az [Edge-modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) szimulál egy Real-Time Streaming Protocol-(RTSP-) kiszolgálót futtató IP-kamerát. Az [RTSP-forrás](../../../media-graph-concept.md#rtsp-source) csomópontja lekéri a videó csatornáját a kiszolgálóról, és a video-képkockákat a [mozgásérzékelő processzor](../../../media-graph-concept.md#motion-detection-processor) -csomópontjára küldi. Ez a processzor észleli a mozgást, és az észlelés után leküldi a képkockákat a [gRPC-bővítmény processzor](../../../media-graph-concept.md#grpc-extension-processor) -csomópontjára.

A gRPC-bővítmény csomópontja egy proxy szerepét játssza le. A képkockákat a megadott képtípusra konvertálja. Ezt követően továbbítja a képet a gRPC-on keresztül egy másik Edge-modulnak, amely egy gRPC-végpont mögött futó AI-modellt futtat egy [megosztott memórián](https://en.wikipedia.org/wiki/Shared_memory)keresztül. Ebben a példában az Edge-modul a [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) modell használatával készült, amely számos típusú objektumot képes érzékelni. A gRPC-bővítmény processzor-csomópontja összegyűjti az észlelés eredményeit, és közzéteszi az eseményeket a [IoT hub](../../../media-graph-concept.md#iot-hub-message-sink) fogadó csomópontban. A csomópont ezután elküldi ezeket az eseményeket [IoT Edge hubhoz](../../../../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

Ebben a rövid útmutatóban a következőket fogja megtekinteni:

1. A Media Graph létrehozása és üzembe helyezése.
1. Az eredmények értelmezése.
1. Az erőforrások eltávolítása.