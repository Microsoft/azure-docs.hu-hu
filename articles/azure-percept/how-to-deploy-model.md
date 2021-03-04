---
title: Víziós AI-modell üzembe helyezése az Azure Percept DK-ben
description: Ismerje meg, hogyan helyezhet üzembe egy víziós AI-modellt az Azure Percept DK-ből az Azure Percept studióból
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 01bd3709050d8a2b57c1bf51920308188546fb31
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102035483"
---
# <a name="deploy-a-vision-ai-model-to-your-azure-percept-dk"></a>Víziós AI-modell üzembe helyezése az Azure Percept DK-ben

Ezt az útmutatót követve üzembe helyezhet egy víziós AI-modellt az Azure Percept DK-be az Azure Percept studióból.

## <a name="prerequisites"></a>Előfeltételek

- Azure Percept DK (fejlesztői készlet)
- [Azure-előfizetés](https://azure.microsoft.com/free/)
- Az [Azure PERCEPT DK telepítési felülete](./quickstart-percept-dk-set-up.md): csatlakoztatta a fejlesztői készlet egy Wi-Fi hálózathoz, létrehozott egy IoT hub, és csatlakoztatta a fejlesztői készlet a IoT hub

## <a name="model-deployment"></a>Modell üzembe helyezése

1. Kapcsolja be a fejlesztői készlet.

1. Navigáljon az [Azure Percept studióhoz](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Az Áttekintés lap bal oldalán kattintson az **eszközök** elemre.

    :::image type="content" source="./media/how-to-deploy-model/overview-devices-inline.png" alt-text="Az Azure Percept Studio áttekintése képernyő." lightbox="./media/how-to-deploy-model/overview-devices.png":::

1. Válassza ki a fejlesztői készlet a listából.

    :::image type="content" source="./media/how-to-deploy-model/select-device.png" alt-text="Percept-eszközök listája.":::

1. A következő lapon kattintson a **minta modell üzembe helyezése** lehetőségre, ha az előre betanított minta-modellek egyikét szeretné telepíteni. Ha egy meglévő, [kód nélküli egyéni megoldás](./tutorial-nocode-vision.md)üzembe helyezését szeretné telepíteni, kattintson **a Custom Vision-projekt üzembe helyezése** lehetőségre.

    :::image type="content" source="./media/how-to-deploy-model/deploy-model.png" alt-text="Modellezési lehetőségek a telepítéshez.":::

1. Ha úgy döntött, hogy nem Code-alapú megoldást helyez üzembe, válassza ki a projektet és az előnyben részesített modell iterációját, és kattintson a **telepítés** elemre.

1. Ha úgy döntött, hogy mintát helyez üzembe, válassza ki a modellt, majd kattintson a **telepítés eszközre** elemre.

1. Ha a modell üzembe helyezése sikeres, a képernyő jobb felső sarkában megjelenik egy állapotjelző üzenet. Ha meg szeretné tekinteni a modellre vonatkozó következtetéseket, kattintson az állapot **megtekintése** hivatkozásra az állapotjelző üzenetben, és tekintse meg a fejlesztői készlet tartozó SoM-videó streamjét.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan tekintheti meg az [Azure PERCEPT DK-telemetria](how-to-view-telemetry.md).