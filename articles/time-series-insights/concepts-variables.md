---
title: Modellváltozók – Azure Time Series Insights Gen2 | Microsoft Docs
description: Modellváltozók
author: shreyasharmamsft
ms.author: shresha
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 01/22/2021
ms.openlocfilehash: 01184a4eb2aac81bbcabcebf89ef10afeabddbe8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872964"
---
# <a name="time-series-model-variables"></a>Time Series Model-változók

Ez a cikk azokat a Time Series Model-változókat ismerteti, amelyek képleteket és számítási szabályokat határoznak meg az eseményekre.

Minden változó háromféle lehet: *numerikus,* *kategorikus* és *összesítő*.

* **A** numerikus típusú értékek folyamatos numerikus értékekkel működnek.
* **A kategorikus** típus különálló értékek egy meghatározott halmazával működik.
* **Az** aggregátum típusú értékek több változót kombinálnak (akár numerikus, akár mind kategorikus).

Az alábbi táblázatban látható, hogy mely tulajdonságok relevánsak az egyes változókhoz.

[![A Time Series Model változótáblája](media/v2-update-tsm/time-series-model-variable-table.png)](media/v2-update-tsm/time-series-model-variable-table.png#lightbox)

## <a name="numeric-variables"></a>Numerikus változók

| Változó tulajdonság | Description |
| --- | ---|
| Változószűrő | A szűrők választható feltételes záradékok, amelyek korlátozzák a számításokhoz figyelembe venni kívánt sorok számát. |
| Változó értéke | Az eszközről vagy érzékelőkből származó vagy Time Series Expressions használatával átalakított számításokhoz használt telemetriaértékek. A numerikus típusú változóknak vagy vagynak kell lennie a bejövő adatok `Double` `Long` adattípusának megfelelőnek.|
| Változó-interpoláció | Az interpoláció azt határozza meg, hogyan rekonstruálhatja a jelet a meglévő adatok használatával. *A* *lépés- és lineáris* interpolációs lehetőségek numerikus változókhoz érhetők el. |
| Változók összesítése | Számításokat végezhet a numerikus változókhoz [támogatott összesítő függvényekkel.](/rest/api/time-series-insights/reference-time-series-expression-syntax#numeric-variable-kind) |

A változók az alábbi JSON-példának felelnek meg:

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

| Változó tulajdonság | Description |
| --- | ---|
| Változószűrő | A szűrők választható feltételes záradékok, amelyek korlátozzák a számításokhoz figyelembe venni kívánt sorok számát. |
| Változó értéke | Az eszközről vagy érzékelőktől érkező számításokhoz használt telemetriaértékek. A kategorikus típusú változóknak vagy vagynak kell lennie a bejövő adatok `Long` `String` adattípusának megfelelőnek. |
| Változó interpoláció | Az interpoláció azt határozza meg, hogyan rekonstruálhatja a jelet a meglévő adatok használatával. A *Lépés* interpoláció lehetőség kategorikus változókhoz érhető el. |
| Változókategóriák | A kategóriák leképezést hoznak létre az eszközről érkező értékek vagy az érzékelők és a címkék között. |
| Változó alapértelmezett kategóriája | Az alapértelmezett kategória minden olyan értékhez tartozik, amely nincs leképezve a "categories" tulajdonságban. |

A változók az alábbi JSON-példának felelnek meg:

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

## <a name="aggregate-variables"></a>Változók összesítése

| Változó tulajdonság | Description |
| --- | ---|
| Változószűrő | A szűrők választható feltételes záradékok, amelyek korlátozzák a számításokhoz figyelembe venni kívánt sorok számát. |
| Változók összesítése | Végezzen számításokat a támogatott aggregátum-függvényekkel az [Összesítés változókhoz.](/rest/api/time-series-insights/reference-time-series-expression-syntax#aggregate-variable-kind) |

A változók az alábbi JSON-példának felelnek meg:

```JSON
"Speed Range": {
  "kind": "aggregate",
  "filter": null,
  "aggregation": {
    "tsx": "max($event.Speed.Double) - min($event.Speed.Double)"
  }
}
```

A változókat az idősorozat-modell típusdefiníciója tárolja, és API-kon keresztül, a tárolt definíció felülírása vagy kiegészítése érdekében beágyazottan is meg lehet adni.

## <a name="next-steps"></a>Következő lépések

* További információ a [Time Series Modelről.](./concepts-model-overview.md)

* További információ a változók beágyazott definiálásról a [Lekérdezési API-k használatával.](./concepts-query-overview.md)
