---
title: Az Azure IoT Hub szolgáltatás és erőforrás állapotának keresése | Microsoft Docs
description: Azure Service Health és Azure Resource Health használata a IoT Hub figyeléséhez
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: robinsh
ms.custom:
- amqp
- 'Role: Cloud Development'
- 'Role: Technical Support'
- devx-track-csharp
ms.openlocfilehash: 27fca7b76ab148fc355eb7d52ee0cbcbd3540458
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92548517"
---
# <a name="check-iot-hub-service-and-resource-health"></a>IoT Hub szolgáltatás és erőforrás állapotának keresése

Az Azure IoT Hub integrálható a [Azure Service Health szolgáltatással](../service-health/overview.md) , hogy lehetővé teszi a IoT hub szolgáltatás és az egyes IoT-hubok szolgáltatási szintjének figyelését. Beállíthat riasztásokat is, amelyekről értesítést kaphat, ha a IoT Hub szolgáltatás vagy egy IoT hub állapota megváltozik. A Azure Service Health szolgáltatás három kisebb szolgáltatás kombinációja: Azure Resource Health, Azure Service Health és az Azure status oldal. A cikk szakaszai ismertetik az egyes szolgáltatásokat és azok kapcsolatát, amelyekkel részletesebben IoT Hub.

A Azure Service Health szolgáltatás segítségével figyelheti a szolgáltatási szintű eseményeket, például a kimaradásokat és a frissítéseket, amelyek befolyásolhatják a IoT Hub szolgáltatás és az egyes IoT-hubok rendelkezésre állását. A IoT Hub a Azure Monitor is integrálható, hogy IoT Hub platform metrikáit és IoT Hub erőforrás-naplókat biztosítson, amelyek segítségével figyelheti az adott IoT hub működési hibáit és feltételeit. További információért lásd: [IoT hub figyelése](monitor-iot-hub.md).

## <a name="check-health-of-an-iot-hub-with-azure-resource-health"></a>IoT hub állapotának megkeresése Azure Resource Health

A Azure Resource Health Azure Service Health szolgáltatás része, amely nyomon követi az egyes erőforrások állapotát. Az IoT hub állapotát közvetlenül a portálról tekintheti meg.

Az alábbi lépéseket követve megtekintheti az IoT hub állapotának és állapotának előzményeit a portál használatával:

1. A [Azure Portalban](https://portal.azure.com)nyissa meg az IoT hubot Azure Portal.

1. A bal oldali ablaktábla **támogatás + hibaelhárítás** területén válassza a **Resource Health** lehetőséget.

    :::image type="content" source="./media/iot-hub-azure-service-health-integration/iot-hub-resource-health.png" alt-text="Az IoT hub erőforrás-állapot lapja":::

Ha többet szeretne megtudni a Azure Resource Healthről és az állapotadatok értelmezéséről, tekintse meg a [Resource Health áttekintést](../service-health/resource-health-overview.md) a Azure Service Health dokumentációjában.

Kiválaszthatja a **Hozzáadás az erőforrás-állapothoz riasztás** lehetőséget is a riasztások beállításához, ha az IoT hub állapota megváltozik. További információ: a [riasztások konfigurálása a szolgáltatás állapotára vonatkozó eseményekhez](../service-health/alerts-activity-log-service-notifications-portal.md) és a kapcsolódó témakörökhöz a Azure Service Health dokumentációjában.

## <a name="check-health-of-iot-hubs-in-your-subscription-with-azure-service-health"></a>Az előfizetésben található IoT-hubok állapotának megtekintése Azure Service Health

A Azure Service Health segítségével megtekintheti az előfizetésben lévő összes IoT-hub állapotát.

Az IoT-hubok állapotának vizsgálatához kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Navigáljon **Service Health**  >  **erőforrás-állapothoz**.

3. A legördülő listából válassza ki az előfizetést, majd válassza az **IoT hub** lehetőséget az erőforrástípus mezőben.

Ha többet szeretne megtudni a Azure Service Healthről és az állapotadatok értelmezéséről, tekintse meg a [Service Health áttekintést](../service-health/service-health-overview.md) a Azure Service Health dokumentációjában.

Ha meg szeretné tudni, hogyan állíthatja be a riasztásokat a Azure Service Health használatával, tekintse meg a [riasztások konfigurálása a szolgáltatás állapotával kapcsolatos események](../service-health/alerts-activity-log-service-notifications-portal.md) és a kapcsolódó témaköröket a Azure Service Health dokumentációjában

## <a name="check-health-of-the-iot-hub-service-by-region-on-azure-status-page"></a>A IoT Hub szolgáltatás állapotának megtekintése az Azure status lapon, régiónként

Ha IoT Hub és más szolgáltatások állapotát szeretné megtekinteni régiónként világszerte, használhatja az [Azure Status (állapot) lapot](https://status.azure.com/status). További információ az Azure status oldaláról: az [Azure állapotának áttekintése](../service-health/azure-status-overview.md) az Azure Service Health dokumentációjában.

## <a name="next-steps"></a>Következő lépések

* A Azure Service Health, a Azure Resource Health és az Azure status lapon a [Azure Service Health szolgáltatásban](../service-health/overview.md) talál részleteket.
* Lásd: az Azure- [IoT hub](monitor-iot-hub.md) figyelése az azure-IoT hub figyelésének leírásához.
