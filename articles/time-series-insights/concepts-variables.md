---
title: Modell változók – Azure Time Series Insights Gen2 | Microsoft Docs
description: Modell változói
author: shreyasharmamsft
ms.author: shresha
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 01/22/2021
ms.openlocfilehash: fd08dc98fa47617bbc7c8d1fff895377837a7327
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736734"
---
# <a name="time-series-model-variables"></a>Idősorozat-modell változói

Ez a cikk az idősorozat-modell változóit ismerteti, amelyek az eseményekre vonatkozó képlet-és számítási szabályokat határozzák meg.

Mindegyik változó a három típus egyike lehet: *numerikus*, *kategorikus* és *aggregált*.

* A **numerikus típusú számok** folytonos numerikus értékekkel működnek.
* A **kategorikus** típusok a különálló értékek meghatározott készletével működnek.
* Az **összesített** típusú változók egyetlen típusú változót egyesítenek (vagy az összes numerikus vagy az összes kategorikus).

Az alábbi táblázat az egyes változókhoz tartozó tulajdonságokat mutatja be.

[![Idősorozat-modell változó táblázata](media/v2-update-tsm/time-series-model-variable-table.png)](media/v2-update-tsm/time-series-model-variable-table.png#lightbox)

## <a name="numeric-variables"></a>Numerikus változók

| Változó tulajdonság | Leírás |
| --- | ---|
| Változó szűrő | A szűrők nem kötelező feltételes záradékok, amelyek korlátozzák a számításba veendő sorok számát. |
| Változó értéke | Az eszközről vagy érzékelőkről származó számításhoz használt telemetria-értékek, vagy az idősorozat-kifejezések használatával alakíthatók át. A numerikus típusú változóknak *dupla* típusúnak kell lenniük.|
| Változó interpoláció | A interpoláció azt adja meg, hogyan lehet a jeleket újradefiniálni a meglévő adatforgalom használatával. A *Step* és a *lineáris* interpolációs beállítások a numerikus változókhoz érhetők el. |
| Változó összesítése | Számítások végrehajtása a támogatott [aggregációs függvények segítségével numerikus változó típusú változók esetén](/rest/api/time-series-insights/reference-time-series-expression-syntax#numeric-variable-kind). |

A változók megfelelnek a következő JSON-példának:

```JSON
"Interpolated Speed": {
  "kind": "numeric",
  "value": {
    "tsx": "$event['Speed-Sensor'].Double"
  },
  "filter": null,
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span": "P1D"
    }
  },
  "aggregation": {
    "tsx": "right($value)"
  }
}
```

## <a name="categorical-variables"></a>Kategorikus változók

| Változó tulajdonság | Leírás |
| --- | ---|
| Változó szűrő | A szűrők nem kötelező feltételes záradékok, amelyek korlátozzák a számításba veendő sorok számát. |
| Változó értéke | Az eszközről vagy érzékelőkről származó számításhoz használt telemetria-értékek. A kategorikus típus változóinak vagy *hosszúnak* vagy *sztringnek* kell lenniük. |
| Változó interpoláció | A interpoláció azt adja meg, hogyan lehet a jeleket újradefiniálni a meglévő adatforgalom használatával. A *Step* interpolációs beállítás a kategorikus változóknál érhető el. |
| Változók kategóriái | A kategóriák az eszközről vagy érzékelőkről a címkére érkező értékek közötti leképezést hoznak létre. |
| Változó alapértelmezett kategóriája | Az alapértelmezett kategória minden olyan értékhez tartozik, amely nincs leképezve a "Categories" tulajdonságban. |

A változók megfelelnek a következő JSON-példának:

```JSON
"Status": {
  "kind": "categorical",
  "value": {
     "tsx": "$event.Status.Long"
},
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span" : "PT1M"
    }
  },
  "categories": [
    {
      "values": [0, 1, 2, 3],
      "label": "Good"
    },
    {
      "values": [4],
      "label": "Bad"
    }
  ],
  "defaultCategory": {
    "label": "Not Applicable"
  }
}
```

## <a name="aggregate-variables"></a>Összesített változók

| Változó tulajdonság | Leírás |
| --- | ---|
| Változó szűrő | A szűrők nem kötelező feltételes záradékok, amelyek korlátozzák a számításba veendő sorok számát. |
| Változó összesítése | Számításokat hajthat végre a támogatott [összesítési függvényekben az összesített változó típusokhoz](/rest/api/time-series-insights/reference-time-series-expression-syntax#aggregate-variable-kind). |

A változók megfelelnek a következő JSON-példának:

```JSON
"Speed Range": {
  "kind": "aggregate",
  "filter": null,
  "aggregation": {
    "tsx": "max($event.Speed.Double) - min($event.Speed.Double)"
  }
}
```

A változók egy idősorozat-modell definíciójában tárolódnak, és az API-kon keresztül is elérhetők a tárolt definíció felülbírálásához vagy kiegészítéséhez.

## <a name="next-steps"></a>Következő lépések

* További információ a [Time Series-modellről](./concepts-model-overview.md).

* További információ a változók definiálásáról a [lekérdezési API](./concepts-query-overview.md)-k használatával.
