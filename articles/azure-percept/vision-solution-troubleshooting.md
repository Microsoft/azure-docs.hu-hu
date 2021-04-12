---
title: Az Azure Percept látási és látási moduljaival kapcsolatos problémák elhárítása
description: Hibaelhárítási tippek az intelligencia prototípus-készítésének tapasztalataiban talált leggyakoribb problémák némelyikéhez
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/29/2021
ms.custom: template-how-to
ms.openlocfilehash: 78de5ef0ef77a181d4a2da91e4b468db1b47f208
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106074692"
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

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-device-modules-inline.png" alt-text="A modulok lap tartalmát megjelenítő kiválasztott eszköz IoT Edge lapja." lightbox= "./media/vision-solution-troubleshooting/vision-device-modules.png":::

## <a name="delete-a-device"></a>Eszköz törlése

1. Nyissa meg az [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home).

1. Kattintson az **IOT hub** ikonra.

1. Válassza ki azt a IoT Hub, amelyhez a célként megadott eszköz csatlakozik.

1. Válassza a **IoT Edge** lehetőséget, majd jelölje be a cél eszköz azonosítója melletti jelölőnégyzetet. Kattintson a Kuka ikonra az eszköz törléséhez.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-device.png" alt-text="IoT Edge kezdőlapon kijelölt ikon törlése.":::

## <a name="eye-module-troubleshooting-tips"></a>Eye modul hibaelhárítási tippek

### <a name="check-the-runtime-status-of-azureeyemodule"></a>A azureeyemodule futásidejű állapotának keresése

Ha probléma merül fel a **WebStreamModule**, győződjön meg arról, hogy a **azureeyemodule**, amely kezeli az alapul szolgáló látási modellt, fut. A futtatókörnyezet állapotának megtekintéséhez nyissa meg a [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) , és keresse meg az **összes erőforrást**  ->  **\<your IoT hub>**  ->  **IoT Edge**  ->  **\<your device ID>** . Kattintson a **modulok** lapra az összes telepített modul futtatókörnyezeti állapotának megtekintéséhez.

:::image type="content" source="./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page-inline.png" alt-text="Eszköz modul futtatókörnyezetének állapota képernyő" lightbox= "./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page.png":::

Ha a **azureeyemodule** futásidejű állapota nem **futként** van felsorolva, kattintson a **modulok beállítása**  ->  **azureeyemodule** elemre. A **modul beállításai** lapon állítsa a **kívánt állapotot** a **Futtatás** elemre, majd kattintson a **frissítés** gombra.

 :::image type="content" source="./media/vision-solution-troubleshooting/firmware-desired-status-stopped.png" alt-text="A modul beállítási képernyője.":::

### <a name="update-telemetryintervalneuralnetworkms"></a>TelemetryIntervalNeuralNetworkMs frissítése

Ha a következő számú korlátozási hibába ütközik, a azureeyemodule modul Twin beállításainak TelemetryIntervalNeuralNetworkMs értékét frissíteni kell.

|Hibaüzenet|
|------|
|A "XXXXXXXXX" IotHub lévő üzenetek teljes száma túllépte a lefoglalt kvótát. Engedélyezett üzenetek maximális száma: "8000", jelenlegi üzenetek száma: "xxxx". A küldési és fogadási műveletek az adott hubhoz a következő UTC napig le vannak tiltva. A kvóta növeléséhez érdemes megfontolni a hub egységének növelését.|

A TelemetryIntervalNeuralNetworkMs határozza meg, hogy milyen gyakran kell üzeneteket küldeni (ezredmásodpercben) a neurális hálózatról. Az Azure-előfizetések naponta korlátozott számú üzenettel rendelkeznek, az előfizetési szintjétől függően. Ha úgy találja magát, hogy túl sok üzenetet küldött, akkor növelje ezt nagyobb számra. 12000 (jelentése 12 másodpercenként) egy szép, kerek 7200 üzenetet biztosít naponta, amely az ingyenes előfizetés 8000-as üzenetének korlátja alá esik.

A TelemetryIntervalNeuralNetworkMs értékének frissítéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure Portalba](https://ms.portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod#home) , és nyissa meg az **összes erőforrást**.

1. A **minden erőforrás** lapon kattintson a fejlesztői készlet kiépített IoT hub nevére a telepítési élmény során.

1. A IoT Hub lap bal oldalán kattintson a **IoT Edge** az **automatikus eszközkezelés** elemre. A IoT Edge eszközök lapon keresse meg a fejlesztői készlet eszközének AZONOSÍTÓját. Kattintson a fejlesztői készlet eszköz-AZONOSÍTÓJÁRA a IoT Edge eszköz oldalának megnyitásához.

1. A **modulok** lapon válassza a **azureeyemodule** lehetőséget.

1. A azureeyemodule lapon nyissa meg a **modul Identity twin (dupla) elemét**.

    :::image type="content" source="./media/vision-solution-troubleshooting/module-page-inline.png" alt-text="A modul oldalának képernyőképe" lightbox= "./media/vision-solution-troubleshooting/module-page.png":::

1. Görgessen le a **Tulajdonságok menüpontra**. Vegye figyelembe, hogy a "Running" és a "Logging" tulajdonság jelenleg nem aktív.

    :::image type="content" source="./media/vision-solution-troubleshooting/module-identity-twin-inline.png" alt-text="Képernyőfelvétel a modul Twin tulajdonságairól." lightbox= "./media/vision-solution-troubleshooting/module-identity-twin.png":::

1. Frissítse a **TelemetryIntervalNeuralNetworkMs** értéket a kívánt módon, és kattintson a **Save (Mentés** ) ikonra.

## <a name="view-device-rtsp-video-stream"></a>Eszköz RTSP videó stream megtekintése

Megtekintheti az eszköz RTSP video streamjét az [Azure Percept Studióban](./how-to-view-video-stream.md) vagy a [VLC Media Playerben](https://www.videolan.org/vlc/index.html).

A VLC Media Playerben lévő RTSP-adatfolyam megnyitásához nyissa meg a **Media**  ->  **Open Network stream**  ->  **RTSP://[eszköz IP-címe]/result**.

## <a name="next-steps"></a>Következő lépések

Az Azure Percept DK hibaelhárításával kapcsolatos további információkért tekintse meg az [általános hibaelhárítási útmutatót](./troubleshoot-dev-kit.md) .