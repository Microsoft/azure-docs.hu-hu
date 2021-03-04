---
title: Tekintse meg az Azure Percept DK modell következtetéseit telemetria
description: Megtudhatja, hogyan tekintheti meg az Azure Percept DK víziós modelljét telemetria az Azure IoT Explorerben
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: 07abbc5f5e85c75b73774d11b6b81dd2085735b7
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095323"
---
# <a name="view-your-azure-percept-dks-model-inference-telemetry"></a>Tekintse meg az Azure Percept DK modell következtetéseit telemetria

Ezt az útmutatót követve megtekintheti az Azure Percept DK víziós modelljét telemetria az [Azure IoT Explorerben](https://github.com/Azure/azure-iot-explorer/releases).

## <a name="prerequisites"></a>Előfeltételek

- Azure Percept DK (fejlesztői készlet)
- [Azure-előfizetés](https://azure.microsoft.com/free/)
- Az [Azure PERCEPT DK telepítési felülete](./quickstart-percept-dk-set-up.md): csatlakoztatta a fejlesztői készlet egy Wi-Fi hálózathoz, létrehozott egy IoT hub, és csatlakoztatta a fejlesztői készlet a IoT hub
- [A látási AI-modell üzembe helyezése az Azure Percept DK-ben történt](./how-to-deploy-model.md)

## <a name="view-telemetry"></a>Telemetria megtekintése

1. Kapcsolja be a fejlesztői készlet.

1. Töltse le és telepítse az [Azure IoT Explorer alkalmazást](https://github.com/Azure/azure-iot-explorer/releases). Ha Ön Windows-felhasználó, válassza ki az. msi fájlt.

    :::image type="content" source="./media/how-to-view-telemetry/azure-iot-explorer-download.png" alt-text="Az Azure IoT Explorer letöltési képernyője.":::

1. IoT Hub összekötése az Azure IoT Explorerrel:

    1. Nyissa meg az [Azure Portal](https://portal.azure.com).

    1. Válassza a **Minden erőforrás** elemet.

        :::image type="content" source="./media/how-to-view-telemetry/azure-portal.png" alt-text="Azure Portal kezdőlapja.":::

    1. Válassza ki azt a IoT Hub, amelyhez az Azure Percept DK csatlakozik.

        :::image type="content" source="./media/how-to-view-telemetry/iot-hub.png" alt-text="Azure Portal IoT Hub listája.":::

    1. A IoT Hub lap bal oldalán válassza a **megosztott elérési házirendek** elemet.

        :::image type="content" source="./media/how-to-view-telemetry/shared-access-policies.png" alt-text="IoT Hub a közös hozzáférési házirendeket megjelenítő oldal.":::

    1. Kattintson a **iothubowner** elemre.

        :::image type="content" source="./media/how-to-view-telemetry/iothubowner.png" alt-text="A közös hozzáférésű házirendek képernyő iothubowner kiemelve.":::

    1. Kattintson a **kapcsolódási karakterlánc – elsődleges kulcs** melletti kék másolás ikonra.

        :::image type="content" source="./media/how-to-view-telemetry/connection-string.png" alt-text="a iothubowner ablakban Kiemelt karakterlánc-másolási gomb van kiemelve.":::

    1. Nyissa meg az Azure IoT Explorert, és kattintson a **+ kapcsolatok hozzáadása** lehetőségre.

    1. Illessze be a kapcsolatok karakterláncát a kapcsolatok karakterláncának **hozzáadása** ablak **kapcsolatok karakterlánc** mezőjébe, majd kattintson a **Mentés** gombra.

        :::image type="content" source="./media/how-to-view-telemetry/add-connection-string.png" alt-text="Az Azure IOT Explorer ablaka, amely a kapcsolatok karakterláncának beillesztésére szolgáló jelölőnégyzettel rendelkezik.":::

    1. Mutasson a jövőképre egy olyan objektumon, amely a modell következtetéseit jelöli.

    1. Válassza a **telemetria** lehetőséget.

    1. A **Start** gombra kattintva megtekintheti az eszköz telemetria eseményeit.

## <a name="next-steps"></a>Következő lépések
Ismerje meg, hogyan tekintheti meg az [Azure PERCEPT DK videó streamjét](./how-to-view-video-stream.md).