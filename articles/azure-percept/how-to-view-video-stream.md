---
title: Az Azure Percept DK RTSP videós stream megtekintése
description: Ismerje meg, hogyan tekintheti meg az Azure Percept DK-ből származó RTSP videó streamet
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 5f77e99dc5c34867fef2b0ac47c709824fa4477d
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096003"
---
# <a name="view-your-azure-percept-dks-rtsp-video-stream"></a>Az Azure Percept DK RTSP videós stream megtekintése

Ezt az útmutatót követve megtekintheti az Azure Percept DK-ből származó RTSP videó streamet az Azure Percept Studióban. Az eszközre központilag telepített, az eszközön üzembe helyezett mesterséges intelligencia-modelleket a rendszer a webes streamben tekinti meg.

## <a name="prerequisites"></a>Előfeltételek

- Azure Percept DK (fejlesztői készlet)
- [Azure-előfizetés](https://azure.microsoft.com/free/)
- Az [Azure PERCEPT DK telepítési felülete](./quickstart-percept-dk-set-up.md): csatlakoztatta a fejlesztői készlet egy Wi-Fi hálózathoz, létrehozott egy IoT hub, és csatlakoztatta a fejlesztői készlet a IoT hub

## <a name="view-the-rtsp-video-stream"></a>Az RTSP videó stream megtekintése

1. Kapcsolja be a fejlesztői készlet.

1. Navigáljon az [Azure Percept studióhoz](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Az Áttekintés lap bal oldalán kattintson az **eszközök** elemre.

    :::image type="content" source="./media/how-to-view-video-stream/overview-devices-inline.png" alt-text="Az Azure Percept Studio áttekintése képernyő." lightbox="./media/how-to-view-video-stream/overview-devices.png":::

1. Válassza ki a fejlesztői készlet a listából.

    :::image type="content" source="./media/how-to-view-video-stream/select-device.png" alt-text="Képernyőkép az elérhető eszközökről az Azure Percept Studióban.":::

1. Kattintson **az eszköz adatfolyamának megtekintése** elemre.

    :::image type="content" source="./media/how-to-view-video-stream/view-device-stream.png" alt-text="Képernyőfelvétel az eszköz oldaláról, amely a rendelkezésre álló víziós projektek műveleteit mutatja.":::

    Ez egy külön lapot nyit meg, amely az Azure Percept DK élő webes streamjét mutatja.

    :::image type="content" source="./media/how-to-view-video-stream/webstream.png" alt-text="Képernyőfelvétel az eszköz webes streamről.":::

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan tekintheti meg az [Azure PERCEPT DK-telemetria](./how-to-view-telemetry.md).