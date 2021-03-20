---
title: 'Gyors útmutató: Azure Time Series Insights Explorer – Azure Time Series Insights | Microsoft Docs'
description: Ismerje meg, hogyan kezdheti el a Azure Time Series Insights Explorert. A környezet nagy mennyiségű IoT-adatait és a bemutató főbb funkcióit jelenítheti meg.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 09/30/2020
ms.openlocfilehash: dd5ed52187a7cd23142b59e20c91c6d125946a72
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91613781"
---
# <a name="quickstart-explore-azure-time-series-insights-gen1"></a>Gyors útmutató: Azure Time Series Insights Gen1 megismerése

> [!CAUTION]
> Ez egy Gen1-cikk.

Ez a Azure Time Series Insights Explorer rövid útmutató segítséget nyújt a Azure Time Series Insights ingyenes bemutató környezetben való megkezdéséhez. Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a webböngészőt nagy mennyiségű IoT-információ és az általánosan elérhető főbb funkciók megjelenítésére.

A Azure Time Series Insights egy teljes körűen felügyelt elemzési, tárolási és vizualizációs szolgáltatás, amely leegyszerűsíti a IoT-események milliárdjainak egyidejű feltárását és elemzését. Globális áttekintést nyújt az adatairól, így gyorsan ellenőrizheti IoT-megoldását, és elkerülheti az üzleti szempontból kritikus fontosságú eszközök költséges leállását. Azure Time Series Insights segít felderíteni a rejtett trendeket, a helyszíni rendellenességeket és a kiváltó okok elemzését közel valós időben.

A további rugalmasság érdekében a hatékony [REST API](./concepts-query-overview.md) -kon és az [ügyfél-SDK](https://github.com/microsoft/tsiclient)-n keresztül Azure Time Series Insights adhat hozzá egy meglévő alkalmazáshoz. Az API-k segítségével az idősorozat-adatai tárolhatók, lefoglalhatók és felhasználhatók az Ön által választott ügyfélalkalmazások számára. Az ügyfél-SDK használatával felhasználói felületi összetevőket is hozzáadhat a meglévő alkalmazáshoz.

Ez a Azure Time Series Insights Explorer rövid útmutató a funkciók interaktív bemutatóját kínálja.

> [!IMPORTANT]
> Hozzon létre egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) , ha még nem hozott létre egyet.

## <a name="prepare-the-demo-environment"></a>A bemutató környezet előkészítése

1. A böngészőben nyissa meg a [Gen1 bemutatóját](https://insights.timeseries.azure.com/demo).

1. Ha a rendszer kéri, jelentkezzen be a Azure Time Series Insights Explorerbe az Azure-fiókja hitelesítő adataival.

1. Megjelenik a Azure Time Series Insights gyors bemutató oldal. A gyors bemutató elindításához kattintson a **tovább** gombra.

   [![Gyors útmutató – válassza a tovább lehetőséget](media/quickstart/quickstart-welcome.png)](media/quickstart/quickstart-welcome.png#lightbox)

## <a name="explore-the-demo-environment"></a>Ismerkedés a bemutató környezettel

1. Ekkor megjelenik az **időkijelölés panel** . Ezen a panelen választhatja ki a megjeleníteni kívánt időkeretet.

   [![Időkiválasztás panel](media/quickstart/quickstart-time-selection-panel.png)](media/quickstart/quickstart-time-selection-panel.png#lightbox)

1. Válasszon ki egy időkeretet, és húzza a régióba. Ezután válassza a **Keresés** lehetőséget.

   [![Időkeret kiválasztása](media/quickstart/quickstart-select-time.png)](media/quickstart/quickstart-select-time.png#lightbox)

   Azure Time Series Insights megjeleníti a megadott időkeret diagramjának vizualizációját. A vonalas diagramon különböző műveleteket végezhet el. Például szűrheti, rögzítheti, rendezheti és halmozhatja fel.

   Az **időkijelölési panelre** való visszatéréshez kattintson a lefelé mutató nyílra az ábrán látható módon:

   [![Diagram](media/quickstart/quickstart-select-down-arrow.png)](media/quickstart/quickstart-select-down-arrow.png#lightbox)

1. Új keresési kifejezés hozzáadásához a **feltételek panelen** válassza a **Hozzáadás** lehetőséget.

   [![Keresési feltételek panel hozzáadása](media/quickstart/quickstart-add-terms.png)](media/quickstart/quickstart-add-terms.png#lightbox)

1. A diagramon válasszon ki egy régiót, kattintson rá a jobb gombbal, majd válassza az **Explore Events** (Események áttekintése) elemet.

   [![Események áttekintése](media/quickstart/quickstart-explore-events.png)](media/quickstart/quickstart-explore-events.png#lightbox)

   A nyers adatok egy rácsa jelenik meg a feltárt régióból.

   [![Események megismerése – rács adatnézete](media/quickstart/quickstart-explore-events-grid-data.png)](media/quickstart/quickstart-explore-events-grid-data.png#lightbox)

## <a name="select-and-filter-data"></a>Az adatszűrő kiválasztása és szűrése

1. Szerkessze a használati feltételeket a diagram értékeinek módosításához. Adjon hozzá egy másik kifejezést a különböző típusú értékek kereszthivatkozásához.

   [![Kifejezés hozzáadása](media/quickstart/quickstart-add-a-term.png)](media/quickstart/quickstart-add-a-term.png#lightbox)

1. Hagyja üresen a **szűrő adatsorozat** mezőt az összes kijelölt keresési kifejezés megjelenítéséhez, vagy adjon meg egy szűrési **kifejezést az improvizált** adatsorozat-szűréshez.

   [![Sorozatok szűrése](media/quickstart/quickstart-filter-series.png)](media/quickstart/quickstart-filter-series.png#lightbox)

   A rövid útmutatóhoz adja meg a **Station5** értéket az állomásra jellemző hőmérséklet és nyomás összevetéséhez.

A rövid útmutató befejezése után a mintaadatokkal kísérletezve különböző vizualizációkat hozhat létre.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Most, hogy elvégezte az oktatóanyagot, törölje a létrehozott erőforrásokat:

1. A [Azure Portal](https://portal.azure.com)bal oldali menüjében válassza a **minden erőforrás** lehetőséget, keresse meg a Azure Time Series Insights erőforráscsoportot.
1. Törölje a teljes erőforráscsoportot (és az abban található összes erőforrást) úgy, hogy kiválasztja az egyes erőforrások **törlését** vagy eltávolítását.

## <a name="next-steps"></a>Következő lépések

* Készen áll a saját Azure Time Series Insights környezet létrehozására. Olvassa el [a Azure Time Series Insights-környezet megtervezése](time-series-insights-environment-planning.md)című cikkét.
