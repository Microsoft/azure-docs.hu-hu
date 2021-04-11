---
title: Metrikák megtekintése Azure Monitor
description: Ez a cikk bemutatja, hogyan figyelheti a metrikákat a Azure Portal-diagramokkal és az Azure CLI-vel.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/17/2021
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7f8aa7e8cd38de37f5d3757804f7836612ad64cb
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227755"
---
# <a name="monitor-media-services-metrics"></a>A Media Services metrikáinak monitorozása

[!INCLUDE [media services api v3 logo](../includes/v3-hr.md)]

[Azure monitor](/azure/azure-monitor/overview) lehetővé teszi a metrikák és diagnosztikai naplók figyelését, amelyek segítenek megérteni az alkalmazások teljesítményét. A szolgáltatás részletes ismertetését és a Azure Media Services metrikák és diagnosztikai naplók használatának okát a következő témakörben tekintheti meg: [Media Services metrikák és diagnosztikai naplók figyelése](monitor-media-services-data-reference.md).

A Azure Monitor számos lehetőséget kínál a metrikák használatára, például a portálon való ábrázolásra, a REST API való hozzáférésre, vagy az Azure CLI használatával történő lekérdezésre. Ez a cikk bemutatja, hogyan figyelheti a metrikákat a Azure Portal-diagramokkal és az Azure CLI-vel.

## <a name="prerequisites"></a>Előfeltételek

- [Media Services-fiók létrehozása](../account-create-how-to.md)
- [Figyelő Media Services mérőszámok és diagnosztikai naplók](monitor-media-services-data-reference.md) áttekintése

## <a name="view-metrics-in-azure-portal"></a>Metrikák megtekintése Azure Portal

1. Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.
1. Navigáljon a Azure Media Services-fiókjához, és válassza a **metrikák** lehetőséget.
1. Kattintson a **hatókör** mezőre, és válassza ki a figyelni kívánt erőforrást.

    A **hatókör kiválasztása** ablak a jobb oldalon jelenik meg az elérhető erőforrások listájával. Ebben az esetben a következőket látja:

    * &lt;Media Services fiók neve&gt;
    * &lt;Media Services fiók neve &gt; / &lt; streaming Endpoint neve&gt;
    * &lt;Storage-fiók neve&gt;

    Válassza ki az erőforrást, majd nyomja le az **alkalmaz** gombot. A támogatott erőforrásokkal és mérőszámokkal kapcsolatos részletekért lásd: [Media Services mérőszámok figyelése](monitor-media-services-data-reference.md).

    > [!NOTE]
    > Ha váltani szeretne a figyelni kívánt erőforrások között, kattintson ismét a **forrás** mezőre, és ismételje meg ezt a lépést.

1. Nem kötelező: adjon nevet a diagramnak (szerkessze a nevet a tetején lévő ceruza megnyomásával).
1. Adja hozzá a megtekinteni kívánt metrikákat.
1. A diagramot rögzítheti az irányítópulton.

## <a name="view-metrics-with-azure-cli"></a>Metrikák megtekintése az Azure CLI-vel

Az Azure CLI-vel való "kimenő" metrikák eléréséhez futtassa a következő `az monitor metrics` parancsot:

```azurecli-interactive
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Más mérőszámok beszerzéséhez helyettesítse be a "kimenő forgalom" kifejezést a metrika neve érdekli.

## <a name="see-also"></a>Lásd még

- [Azure Monitor-metrikák](/azure/azure-monitor/data-platform)
- [Metrikai riasztások létrehozása, megtekintése és kezelése Azure monitor használatával](/azure/azure-monitor/alerts/alerts-metric).

## <a name="next-steps"></a>Következő lépések

[Diagnosztikai naplók](../media-services-diagnostic-logs-howto.md)
