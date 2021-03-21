---
title: Azure Monitor tervező átalakítása munkafüzetekbe konvertálás gyakori feladatai
description: Gyakori feladatok a Azure Monitor munkafüzetek nézeteiből való átváltáskor.
author: austonli
ms.author: aul
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: d479e64620519491ba4847349ea33820747fcea7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102043473"
---
# <a name="view-designer-to-workbooks-conversion-common-tasks"></a>Designerek megtekintése a munkafüzetek átalakításához – gyakori feladatok
A [tervező](view-designer.md) a Azure monitor egyik funkciója, amely lehetővé teszi, hogy egyéni nézeteket hozzon létre, amelyek segítségével megjelenítheti a log Analytics-munkaterületen lévő, diagramokkal, listákkal és időpontokkal kapcsolatos adatait. A rendszer fokozatosan lecseréli azokat a munkafüzetekkel, amelyek további funkciókat biztosítanak. Ez a cikk azokat a feladatokat ismerteti, amelyek gyakoriak a nézetek munkafüzetekbe való konvertálása során.


## <a name="quickstart-with-preset-view-designer-templates"></a>Gyors üzembe helyezés előre beállított tervezői sablonokkal

A Log Analytics munkaterületeken található munkafüzetek már rendelkeznek olyan sablonokkal, amelyek megfelelnek a View Designerben lévő egyes nézeteknek. A **tervezői útmutatók** kategóriában válassza a **tervezői átmenet áttekintése** lehetőséget a beállítások megismeréséhez, vagy válasszon egyet az előre definiált sablonok közül.

![Példák sablonokra](media/view-designer-conversion-tasks/templates.png)

## <a name="enabling-time-range-filter"></a>Időtartomány-szűrő engedélyezése
A Designer nézet beépített alapértelmezett időtartomány-szűrőt tartalmaz, azonban a munkafüzetekben ez a beállítás alapértelmezés szerint nincs engedélyezve. A munkafüzetek lehetővé teszik a felhasználók számára, hogy saját időtartomány-szűrőket hozzanak létre, amelyek az adatnaplókra is érvényesek lehetnek. A szűrő létrehozásához szükséges lépések az alábbi listában láthatók:

Válassza a **Paraméterek hozzáadása** lehetőséget. Az alapértelmezett **stílus** a *tabletták* értékre van állítva.

![Param hozzáadása](media/view-designer-conversion-tasks/add-param.png)

 Kattintson a **paraméter hozzáadása** gombra.

![Paraméter hozzáadása](media/view-designer-conversion-tasks/add-parameter.png)

Az oldalsáv menüjében, a **paraméter neve** szövegmezőbe írja be a következőt: *TimeRange*. **Paraméter típusának** beállítása *időtartomány-választóként* Jelölje be a **kötelező?** jelölőnégyzetet.

![Paraméter menü](media/view-designer-conversion-tasks/parameter-menu.png)

Mentse a paramétert az oldalsáv menüjének bal felső sarkában. Alapértelmezés *szerint a* legördülő lista nem törölhető, vagy kiválaszthat egy alapértelmezett **TimeRange** értéket (például *24 óra*). Válassza a **Szerkesztés kész** lehetőséget.

A paraméterek használhatók a lekérdezésekben kapcsos zárójelek hozzáadásával {} a paraméter neve körül. A paraméterekkel kapcsolatos további részleteket a [munkafüzetek dokumentációjában](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/Parameters/Parameters.md)találhat.

## <a name="updating-queries-with-the-timerange-parameter"></a>Lekérdezések frissítése a TimeRange paraméterrel

### <a name="option-1-select-timerange-from-the-time-range-dropdown"></a>1. lehetőség: a TimeRange kiválasztása az időtartomány legördülő listából

![Time paraméter](media/view-designer-conversion-tasks/time-parameter.png)

### <a name="option-2-update-your-log-queries"></a>2. lehetőség: a napló lekérdezésének frissítése

A lekérdezésben adja hozzá a sort: az `| where TimeGenerated {TimeRange}` alábbi példában látható módon:

Eredeti lekérdezés
```KQL
search * 
| summarize count() by Type
```

Frissített lekérdezés
```KQL
search * 
| where TimeGenerated {TimeRange} 
| summarize count() by Type
```

## <a name="including-a-list"></a>Listával együtt
A nézet tervezői nézeteinek többsége tartalmaz egy listát, és ezt a standard listát egy munkafüzetben is reprodukálhatja.

![Csempe listája](media/view-designer-conversion-tasks/tile-list.png)

Adjon hozzá egy vizualizációt a cella beállításai közül a **lekérdezés hozzáadása** lehetőségre kattintva.

![Param hozzáadása](media/view-designer-conversion-tasks/add-param.png)

A Tervező nézet egy alapértelmezett lekérdezést alkalmaz, amely megfelel az eredeti példa szintaxisának. Ezt úgy frissítheti, ha a lekérdezést a következő példában látható módon módosítja a frissített űrlapra:

Eredeti lekérdezés
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

Frissített lekérdezés
```KQL
search * 
| summarize Count = count() by Type
```

Ez a következőhöz hasonló listát hoz majd ki:

![Példa a listára](media/view-designer-conversion-tasks/list-example.png)

## <a name="enabling-sparklines"></a>Értékgörbék engedélyezése
A Gridek gyakori funkciója, hogy értékgörbék hozzáadásával összegzi a különböző adatmintázatokat az idő múlásával. A Designer nézet az összes listához **engedélyezi az értékgörbék engedélyezése** funkciót, akárcsak a munkafüzetek. Ha olyan értékgörbék közé szeretné foglalni az adatnézeteket, amelyek megfelelnek a View designernek, csatlakoztassa az adatait az eredeti lekérdezéshez, ahogy az alábbi példában is látható:

Eredeti lekérdezés
```KQL
search *
| summarize AggregatedValue = count() by Type) on Type
```

Frissített lekérdezés
```KQL
search * 
| summarize AggregatedValue = count() by Type
| join kind = inner (search * 
    | make-series Trend = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type) on Type
| project Type, AggregatedValue, Trend
```

Válassza az **oszlop beállításai** lehetőséget.
![Oszlop beállításai](media/view-designer-conversion-tasks/column-settings.png)

Az **oszlop megjelenítő** legördülő lista frissítése Spark- *területekként*.
![Értékgörbék](media/view-designer-conversion-tasks/sparkline.png)

Mentse a beállításokat, majd futtassa újra a lekérdezést a tábla frissítéséhez, hogy tartalmazzon egy értékgörbe-t is.

Az eredményül kapott rács a következőhöz hasonlóan fog kinézni: ![ értékgörbék példa](media/view-designer-conversion-tasks/sparkline-example.png)

## <a name="advanced-cell-settings"></a>Speciális cella beállításai
A nézet Designer tükrözéséhez olyan feladatokat hajthat végre, mint például a munkafüzet cellái méretének módosítása vagy a PIN-kódok és külső hivatkozások hozzáadása a naplókhoz.

A **Speciális beállítások** eléréséhez válassza a fogaskerék ikont az egyes cellák alján.

![Speciális beállítások](media/view-designer-conversion-tasks/advanced-settings.png)

Ekkor megjelenik egy menü, amely különböző lehetőségeket kínál:

![Speciális beállítások beállításai](media/view-designer-conversion-tasks/advanced-settings-settings.png)

PIN-kód hozzáadásához és külső lekérdezésre mutató hivatkozás kiválasztásához jelölje ki a megfelelő jelölőnégyzeteket. Ha címet szeretne felvenni a cellába, írja be a kívánt címet a **diagram címe** szakaszba.

Alapértelmezés szerint a munkafüzetek cellája úgy van beállítva, hogy a teljes oldal szélességét elvégezze, de ezt úgy is beállíthatja, hogy a **Speciális beállítások** menü **Style (stílus** ) lapján a cella lefelé skálázásával leállítja a cellát.

![Speciális beállítások stílusa](media/view-designer-conversion-tasks/advanced-settings-style.png)

 
## <a name="additional-parameters"></a>További paraméterek
Válassza a **paraméter hozzáadása** lehetőséget egy új paraméter létrehozásához a munkafüzetben. 

Az előfizetés kiválasztásához írja *be az előfizetést* a **paraméter neve** mezőbe az oldalsó menüben, és válassza az *előfizetés választó* elemet a **paraméter típusa** legördülő listából.

![Előfizetés menü](media/view-designer-conversion-tasks/subscription-filter.png)

Ha ki szeretne választani egy erőforrást, írja be az *erőforrást* a **paraméter neve** mezőbe az oldalsó menüben, majd válassza az *erőforrás-választó* elemet a **paraméter típusa** legördülő listából.

![Erőforrás menü](media/view-designer-conversion-tasks/resource-filter.png)

Ekkor a legördülő menüvel érheti el a különböző előfizetéseit és erőforrásait.

![Előfizetési erőforrás legördülő listája](media/view-designer-conversion-tasks/subscription-resource.png)


## <a name="next-steps"></a>Következő lépések
- [Csempeátalakítások](view-designer-conversion-tiles.md)
