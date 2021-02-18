---
title: Azure Monitor a Designer és a munkafüzetek mozaik konverziójának megtekintése
description: A csempék munkafüzetekbe való átalakításának részletei a Azure Monitor nézeteiből való áttéréskor.
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 0fe1c5a4e6da1cb2955542c2651fcaf2d015b8f8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100619028"
---
# <a name="azure-monitor-view-designer-tile-conversions"></a>Azure Monitor View Designer csempe konverziók
A [tervező](view-designer.md) a Azure monitor egyik funkciója, amely lehetővé teszi, hogy egyéni nézeteket hozzon létre, amelyek segítségével megjelenítheti a log Analytics-munkaterületen lévő, diagramokkal, listákkal és időpontokkal kapcsolatos adatait. A rendszer fokozatosan lecseréli azokat a munkafüzetekkel, amelyek további funkciókat biztosítanak. Ez a cikk a különböző csempék munkafüzetekbe konvertálásának részleteit ismerteti.

## <a name="donut--list-tile"></a>Fánk & lista csempe

![Fánk-lista](media/view-designer-conversion-tiles/donut-list.png)

A fánk & listájának a munkafüzetekbe való újbóli létrehozása két különálló vizualizációt is magában foglal. A fánk résznél két lehetőség közül választhat.
Mindkét kezdéshez válassza a **lekérdezés hozzáadása** lehetőséget, majd illessze be az eredeti lekérdezést a tervezőből a cellába.

**1. lehetőség:** **Kördiagram** kiválasztása a **vizualizáció** legördülő menüből: ![ tortadiagram vizualizációs menüjéből](media/view-designer-conversion-tiles/pie-chart.png)

**2. lehetőség:** Válassza a **beállítás lekérdezés szerint** lehetőséget a **vizualizáció** legördülő menüből, és adja hozzá `| render piechart` a lekérdezéshez:

 ![Vizualizáció menü](media/view-designer-conversion-tiles/set-by-query.png)

**Példa**

Eredeti lekérdezés
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc
```

Frissített lekérdezés
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc 
| render piechart
```

A lista létrehozásához és az értékgörbék engedélyezéséhez tekintse meg a [gyakori feladatokról](view-designer-conversion-tasks.md)szóló cikket.

A következő példa azt szemlélteti, hogy a fánk & a lista csempe hogyan értelmezhető újra a munkafüzetekben:

![Fánk-lista-munkafüzetek](media/view-designer-conversion-tiles/donut-workbooks.png)

## <a name="line-chart--list-tile"></a>Vonal diagram & lista csempe
![Vonalas diagram listája](media/view-designer-conversion-tiles/line-list.png) 

Ha újra létre szeretné hozni a vonalas diagramot, frissítse a lekérdezést a következőképpen:

Eredeti lekérdezés
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

Frissített lekérdezés
```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type
```

A vonalas diagram megjelenítéséhez két lehetőség közül választhat.

**1. lehetőség:** Válassza ki a **vonal diagramot** a **vizualizáció** legördülő menüből:
 
 ![Vonalas diagram menü](media/view-designer-conversion-tiles/line-visualization.png)

**2. lehetőség:** Válassza a **beállítás lekérdezés szerint** lehetőséget a **vizualizáció** legördülő menüből, és adja hozzá `| render linechart` a lekérdezéshez:

 ![Vizualizáció menü](media/view-designer-conversion-tiles/set-by-query.png)

**Példa**

```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type 
| render linechart_
```

A lista létrehozásához és az értékgörbék engedélyezéséhez tekintse meg a [gyakori feladatokról](view-designer-conversion-tasks.md)szóló cikket.

A következő példa azt szemlélteti, hogyan lehet újraértelmezni a vonalas diagram & a munkafüzetekben:

![Diagramok listája – munkafüzetek](media/view-designer-conversion-tiles/line-workbooks.png)

## <a name="number--list-tile"></a>Szám & lista csempe

 ![Csempe listája](media/view-designer-conversion-tiles/tile-list-example.png)

A number (szám) csempén a következőképpen frissítheti a lekérdezést:

Eredeti lekérdezés
```KQL
search * 
| summarize AggregatedValue = count() by Computer | count
```

Frissített lekérdezés
```KQL
search *
| summarize AggregatedValue = count() by Computer 
| summarize Count = count()
```

Módosítsa a vizualizáció legördülő menüt **csempére** , majd válassza a **csempe beállításai** lehetőséget.
 ![Csempe vizualizációja](media/view-designer-conversion-tiles/tile-visualization.png)

Hagyja üresen a **cím** szakaszt, és válassza a **bal oldali** lehetőséget. Módosítsa a **használat oszlop értékét:** a **darabszámra** és az **oszlopok megjelenítéséhez** a **Big Number** értékre:

![Csempe beállításai](media/view-designer-conversion-tiles/tile-settings.png)

 
A lista létrehozásához és az értékgörbék engedélyezéséhez tekintse meg a [gyakori feladatokról](view-designer-conversion-tasks.md)szóló cikket.

A következő példa azt mutatja be, hogy a rendszer hogyan értelmezi a listát tartalmazó csempe & számát a munkafüzetekben:

![Számok listája – munkafüzetek](media/view-designer-conversion-tiles/number-workbooks.png)

## <a name="timeline--list"></a>Ütemterv és lista

 ![Idősor-lista](media/view-designer-conversion-tiles/time-list.png)

Az idősoron a következőképpen frissítheti a lekérdezést:

Eredeti lekérdezés
```KQL
search * 
| sort by TimeGenerated desc
```

Frissített lekérdezés
```KQL
search * 
| summarize Count = count() by Computer, bin(TimeGenerated,{TimeRange:grain})
```

A lekérdezés sávdiagramként való megjelenítésének két lehetősége van:

**1. lehetőség:** **Sávdiagram** kiválasztása a **vizualizáció** legördülő menüjéből: ![ Barchart vizualizáció](media/view-designer-conversion-tiles/bar-visualization.png)
 
**2. lehetőség:** Válassza a **beállítás lekérdezés szerint** lehetőséget a **vizualizáció** legördülő menüből, és adja hozzá `| render barchart` a lekérdezéshez:

 ![Vizualizáció menü](media/view-designer-conversion-tiles/set-by-query.png)

 
A lista létrehozásához és az értékgörbék engedélyezéséhez tekintse meg a [gyakori feladatokról](view-designer-conversion-tasks.md)szóló cikket.

Az alábbi példa azt szemlélteti, hogyan lehet újraértelmezni a munkafüzetekben az idősor & lista csempéjét:

![Idősor-lista-munkafüzetek](media/view-designer-conversion-tiles/time-workbooks.png)

## <a name="next-steps"></a>Következő lépések

- [A Tervező nézetének áttekintése a munkafüzetek átváltásához](view-designer-conversion-overview.md)
