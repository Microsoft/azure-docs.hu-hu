---
title: Az Azure Percept látási és látási moduljaival kapcsolatos problémák elhárítása
description: Hibaelhárítási tippek az intelligencia prototípus-készítésének tapasztalataiban talált leggyakoribb problémák némelyikéhez
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: e7351079e7325aa7750dc0d10f0923bc847ccc3c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663520"
---
# <a name="vision-solution-troubleshooting"></a>A látási megoldás hibaelhárítása

Az alábbi útmutatást követve további információkat talál az Azure Percept Studióban elérhető, nem Code-alapú megoldások hibaelhárításáról.

## <a name="delete-a-vision-project"></a>Víziós projekt törlése

1. Nyissa meg a következőt: https://www.customvision.ai/projects.

1. Vigye a kurzort a törölni kívánt projekt fölé, és kattintson a Kuka ikonra a projekt törléséhez.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-project.png" alt-text="Custom Vision projektek lapja törlés ikon kiemelve.":::

## <a name="check-which-modules-are-on-a-device"></a>Az eszközön található modulok kikeresése

1. Nyissa meg az [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home).

1. Kattintson az **IOT hub** ikonra.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub-2-inline.png" alt-text="Azure Portal a kezdőlapot a IOT hub ikon kiemelve." lightbox= "./media/vision-solution-troubleshooting/vision-iot-hub-2.png":::

1. Válassza ki azt a IoT Hub, amelyhez a célként megadott eszköz csatlakozik.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub.png" alt-text="Az IoT-hubok listája.":::

1. Válassza a **IoT Edge** lehetőséget, majd az eszköz **azonosítója** lapon kattintson az eszközre.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-edge.png" alt-text="IoT Edge kezdőlapja.":::

1. Az eszköz moduljai a **modulok** lapon jelennek meg.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-device-modules-inline.png" alt-text="A IOT Edge lapja a kiválasztott eszközhöz, amely a modulok lap tartalmát jeleníti meg." lightbox= "./media/vision-solution-troubleshooting/vision-device-modules.png":::

## <a name="delete-a-device"></a>Eszköz törlése

1. Nyissa meg az [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home).

1. Kattintson az **IOT hub** ikonra.

1. Válassza ki azt a IoT Hub, amelyhez a célként megadott eszköz csatlakozik.

1. Válassza a **IoT Edge** lehetőséget, majd jelölje be a cél eszköz azonosítója melletti jelölőnégyzetet. Kattintson a Kuka ikonra az eszköz törléséhez.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-device.png" alt-text="Az IOT Edge kezdőlapján Kiemelt törlési ikon.":::

## <a name="eye-module-troubleshooting-tips"></a>Eye modul hibaelhárítási tippek

Ha probléma merül fel a **WebStreamModule**, ellenőrizze, hogy fut-e a **azureeyemodule**, amely a látási modellre hivatkozik. A futtatókörnyezet állapotának megtekintéséhez nyissa meg a [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) , és keresse meg az **összes erőforrást**  ->  **\<your IoT hub>**  ->  **IoT Edge**  ->  **\<your device ID>** . Kattintson a **modulok** lapra az összes telepített modul futtatókörnyezeti állapotának megtekintéséhez.

:::image type="content" source="./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page-inline.png" alt-text="Eszköz modul futtatókörnyezetének állapota képernyő" lightbox= "./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page.png":::

Ha a **azureeyemodule** futásidejű állapota nem **futként** van felsorolva, kattintson a **modulok beállítása**  ->  **azureeyemodule** elemre. A **modul beállításai** lapon állítsa a **kívánt állapotot** a **Futtatás** elemre, majd kattintson a **frissítés** gombra.

 :::image type="content" source="./media/vision-solution-troubleshooting/firmware-desired-status-stopped.png" alt-text="A modul beállítási képernyője.":::

## <a name="view-device-rtsp-video-stream"></a>Eszköz RTSP videó stream megtekintése

Megtekintheti az eszköz RTSP video streamjét az [Azure Percept Studióban](./how-to-view-video-stream.md) vagy a [VLC Media Playerben](https://www.videolan.org/vlc/index.html).

A VLC Media Playerben lévő RTSP-adatfolyam megnyitásához nyissa meg a **Media**  ->  **Open Network stream**  ->  **RTSP://[eszköz IP-címe]/result**.

## <a name="next-steps"></a>Következő lépések

Az Azure Percept DK hibaelhárításával kapcsolatos további információkért tekintse meg az [általános hibaelhárítási útmutatót](./troubleshoot-dev-kit.md) .