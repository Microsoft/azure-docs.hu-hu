---
title: 'Oktatóanyag: Event Hubs-adatraktárba való küldés az adattárházba – Event Grid'
description: Ismerteti, hogyan tárolhatók Event Hubs rögzített adatkészletek az Azure szinapszis Analyticsben Azure Functions és Event Grid eseményindítók használatával.
ms.topic: tutorial
ms.date: 12/07/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 7b0e471e32650490e1896bb6ea171c8223b21378
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96854716"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>Oktatóanyag: stream big data adattárházba
Az Azure [Event Grid](overview.md) egy intelligens esemény-útválasztási szolgáltatás, amely lehetővé teszi az alkalmazások és szolgáltatások értesítéseire vagy eseményeire való reagálást. Például elindíthat egy Azure-függvényt a blob Storage-ba vagy Data Lake Storageba rögzített Event Hubs-adat feldolgozásához. Ez a [minta](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) bemutatja, hogyan használhatók a Event Grid és a Azure functions a blob Storage-ból származó rögzített Event Hubs adatok áttelepítésére az Azure szinapszis analyticsbe, pontosabban egy dedikált SQL-készletbe.

[!INCLUDE [event-grid-event-hubs-functions-synapse-analytics.md](../../includes/event-grid-event-hubs-functions-synapse-analytics.md)]

## <a name="next-steps"></a>Következő lépések

* Az Azure üzenetkezelési szolgáltatások különbségeiről [az üzenetkézbesítő Azure-szolgáltatás kiválasztásának ismertetésében](compare-messaging-services.md) olvashat.
* Az Event Grid ismertetése: [Az Event Grid bemutatása](overview.md).
* Az Event Hubs Capture megismeréséhez tekintse meg [az Event Hubs Capture Azure Portallal való engedélyezését](../event-hubs/event-hubs-capture-enable-through-portal.md) bemutató cikket.
* A minta beállításával és futtatásával kapcsolatos további információért lásd az [Event Hubs Capture- és Event Grid-mintát](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).
