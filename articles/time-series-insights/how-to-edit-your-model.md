---
title: Adatmodellezés a Gen2-környezetekben – Azure Time Series Insights | Microsoft Docs
description: Tudnivalók az adatmodellezésről Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: 89efc1d4f34b250d211f9fd7492588bd2896eb6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95016853"
---
# <a name="data-modeling-in-azure-time-series-insights-gen2"></a>Adatmodellezés Azure Time Series Insights Gen2

Ez a cikk azt ismerteti, hogyan használható a Time Series-modell a Azure Time Series Insights Gen2. Számos gyakori adatesetet részletez.

> [!TIP]
>
> * További információ a [Time Series-modellről](concepts-model-overview.md).
> * További információ a [Azure Time Series Insights Gen2 Explorer alkalmazásban](./concepts-ux-panels.md)való navigálásról.

## <a name="instances"></a>példányszám

A Azure Time Series Insights Explorer támogatja a példányok **létrehozási**, **olvasási**, **frissítési** és **törlési** műveleteit a böngészőben.

A kezdéshez válassza ki a **modell** nézetet a Azure Time Series Insights Explorer **elemzés** nézetében.

### <a name="create-a-single-instance"></a>Egyetlen példány létrehozása

1. Nyissa meg az idősorozat-modell kiválasztása panelt, és válassza a menü **példányok** elemét. Ekkor megjelenik a kiválasztott Azure Time Series Insights-környezethez társított összes példány.

    [![Hozzon létre egy példányt úgy, hogy először kiválasztja a példányokat.](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png#lightbox)

1. Válassza a **+ Hozzáadás** lehetőséget.

    [![Adjon hozzá egy példányt a + Hozzáadás gomb kiválasztásával.](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png#lightbox)

1. Adja meg a példány részleteit, válassza ki a típus és hierarchia társítást, és válassza a **Létrehozás** lehetőséget.

### <a name="bulk-upload-one-or-more-instances"></a>Egy vagy több példány tömeges feltöltése

> [!TIP]
> Előfordulhat, hogy a példányokat a JSON-ban menti az asztalra. A letöltött JSON-fájl ezután a következő lépésekkel tölthető fel.

1. Válassza a **JSON feltöltése** lehetőséget.
1. Válassza ki a példányok hasznos adatait tartalmazó fájlt.

    [![Példányok tömeges feltöltése a JSON-n keresztül.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png#lightbox)

1. Válassza a **Feltöltés** lehetőséget.

### <a name="edit-a-single-instance"></a>Egyetlen példány szerkesztése

1. Válassza ki a példányt, és válassza a **Szerkesztés** vagy a **ceruza ikont**.
1. Végezze el a szükséges módosításokat, majd kattintson a **Mentés** gombra.

    [![Egyetlen példány szerkesztése.](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png#lightbox)

### <a name="delete-an-instance"></a>Példány törlése

1. Válassza ki a példányt, és válassza a **Törlés** vagy a **hulladék raktárhely ikont**.

   [![Törölje a példányt a Delete (Törlés) lehetőség kiválasztásával.](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png#lightbox)

1. A **törlés megerősítéséhez válassza a** Törlés lehetőséget.

> [!NOTE]
> Egy példánynak sikeresen át kell adnia egy mező-ellenőrzési ellenőrzést, amelyet törölni kell.

## <a name="hierarchies"></a>Hierarchiák

A Azure Time Series Insights Explorer támogatja a hierarchia **létrehozási**, **olvasási**, **frissítési** és **törlési** műveleteit a böngészőben.

A kezdéshez válassza ki a **modell** nézetet a Azure Time Series Insights Explorer **elemzés** nézetében.

### <a name="create-a-single-hierarchy"></a>Egyetlen hierarchia létrehozása

1. Lépjen az idősorozat-modell kiválasztó paneljére, és válassza a menü **hierarchiák** elemét. Ekkor megjelenik a kiválasztott Azure Time Series Insights-környezethez társított összes hierarchia.

    [![Hozzon létre egy hierarchiát a panelen.](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png#lightbox)

1. Válassza a **+ Hozzáadás** lehetőséget.

    [![Hierarchia + Hozzáadás gomb.](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png#lightbox)

1. A jobb oldali ablaktáblán kattintson a **+ Hozzáadás szint** lehetőségre.

    [![Szint hozzáadása a hierarchiához.](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png)](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png#lightbox)

1. Adja meg a hierarchia részleteit, és kattintson a **Mentés** gombra.

    [![Határozza meg a hierarchia részleteit.](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png)](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Egy vagy több hierarchia tömeges feltöltése

> [!TIP]
> A hierarchiákat a JSON-ban is mentheti az asztalra. A letöltött JSON-fájl ezután a következő lépésekkel tölthető fel.

1. Válassza a **JSON feltöltése** lehetőséget.
1. Válassza ki a hierarchia adattartalmát tartalmazó fájlt.
1. Válassza a **Feltöltés** lehetőséget.

    [![A hierarchiák tömeges feltöltésének kiválasztása.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Egyetlen hierarchia szerkesztése

1. Válassza ki a hierarchiát, és válassza a **Szerkesztés** vagy a **ceruza ikont**.
1. Végezze el a szükséges módosításokat, majd kattintson a **Mentés** gombra.

    [![Egyetlen hierarchia szerkesztésére szolgáló kijelölés.](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png#lightbox)

### <a name="delete-a-hierarchy"></a>Hierarchia törlése

1. Válassza ki a hierarchiát, és válassza a **Törlés** vagy a **hulladék raktárhely ikont**.

    [![A Törlés gombra kattintva törölheti a hierarchiát.](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png#lightbox)

1. A **törlés megerősítéséhez válassza a** Törlés lehetőséget.

## <a name="types"></a>Típusok

A Azure Time Series Insights Explorer támogatja a típus **létrehozási**, **olvasási**, **frissítési** és **törlési** műveleteit a böngészőben.

A kezdéshez válassza ki a **modell** nézetet a Azure Time Series Insights Explorer **elemzés** nézetében.

### <a name="create-a-single-type"></a>Egyetlen típus létrehozása

1. Lépjen az idősorozat-modell kiválasztó paneljére, és válassza a menü **típusok** elemét. Ekkor megjelenik a kiválasztott Azure Time Series Insights-környezethez társított összes típus.

    [![Idősorozat-modell típusai panel.](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png#lightbox)

1. Kattintson a **+ Hozzáadás** elemre az **új típus hozzáadása** felbukkanó mód megjelenítéséhez.
1. Adja meg a típus tulajdonságait és változóit. A bevitelt követően válassza a **Mentés** lehetőséget.

    [![A típus hozzáadására szolgáló konfigurációs beállítások.](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Egy vagy több típus tömeges feltöltése

> [!TIP]
> A típusokat a JSON-ban is mentheti az asztalra. A letöltött JSON-fájl ezután a következő lépésekkel tölthető fel.

1. Válassza a **JSON feltöltése** lehetőséget.
1. Válassza ki azt a fájlt, amely a típus adattartalmát tartalmazza.
1. Válassza a **Feltöltés** lehetőséget.

    [![Tömeges típusok feltöltési beállításai.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png#lightbox)

### <a name="edit-a-single-type"></a>Egyetlen típus szerkesztése

1. Válassza ki a típust, és válassza a **Szerkesztés** vagy a **ceruza ikont**.
1. Végezze el a szükséges módosításokat, majd kattintson a **Mentés** gombra.

    [![Szerkesszen egy típust a panelen.](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png#lightbox)

### <a name="delete-a-type"></a>Típus törlése

1. Válassza ki a típust, és válassza a **Törlés** vagy a **hulladék raktárhely ikont**.

   [![Egy típus törléséhez válassza a Törlés lehetőséget.](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png#lightbox)

1. A **törlés megerősítéséhez válassza a** Törlés lehetőséget.

## <a name="next-steps"></a>Következő lépések

* A Time Series-modellel kapcsolatos további információkért olvassa el az [adatmodellezést](./concepts-model-overview.md)ismertető témakört.

* Ha többet szeretne megtudni a Gen2, olvassa el [az Azure Time Series Insights Gen2 Explorer adatainak megjelenítése](./concepts-ux-panels.md)című témakörét.

* A támogatott JSON-alakzatokkal kapcsolatos további tudnivalókért olvassa el a [támogatott JSON-alakzatok](./time-series-insights-send-events.md#supported-json-shapes)című témakört.