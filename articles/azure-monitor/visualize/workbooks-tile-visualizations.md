---
title: Azure Monitor munkafüzet csempe vizualizációk
description: Ismerkedjen meg az összes Azure Monitor munkafüzet csempés vizualizációval.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 07de9fcd01559e59a1bcd5d8928075aebcf79cc7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100615938"
---
# <a name="tile-visualizations"></a>Csempék vizualizációi

A csempék hasznos módot jelentenek a munkafüzetek összegző adatainak megjelenítéséhez. Az alábbi képen a csempék általános használati esetei láthatók, amelyek az alkalmazás szintű összegzéssel rendelkeznek a részletes rácson.

[![Képernyőfelvétel a csempe összefoglaló nézetéről](./media/workbooks-tile-visualizations/tiles-summary.png)](./media/workbooks-tile-visualizations/tiles-summary.png#lightbox)

A munkafüzet csempék támogatják a címet, a feliratot, a nagyméretű szöveget, az ikonokat, a metrikai átmeneteket, a Spark-vonalakat, a sávokat, a láblécet stb.

## <a name="adding-a-tile"></a>Csempe hozzáadása

1. Az eszköztár _szerkesztése_ elemre kattintva váltson át a munkafüzet szerkesztési módjára.
2. Válassza a **Hozzáadás** , majd a *lekérdezés hozzáadása* lehetőséget, ha egy log lekérdezés vezérlőelemet szeretne hozzáadni a munkafüzethez.
3. Válassza ki a lekérdezés típusát **naplóként**, erőforrástípusként (például Application Insights) és a célként megadott erőforrásokat.
4. A lekérdezési szerkesztővel adja meg az elemzéshez használandó KQL.

    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```

5. A méret beállítása **megtelt**.
6. Állítsa be a vizualizációt **mozaikokra**.
7. Kattintson a **csempe beállításai** gombra a beállítások ablaktábla megnyitásához.
    1. A *title (cím*) beállításnál állítsa be a következőket:
        * Oszlop használata: `name` .
    2. A *bal oldalon* állítsa be a következőket:
        * Oszlop használata: `Requests` .
        * Oszlop megjelenítő: `Big Number` .
        * Színpaletta: `Green to Red`
        * Minimális érték: `0` .
    3. A *lenti* beállításnál állítsa be a következőket:
        * Oszlop használata: `appName` .
8. Kattintson a **Mentés és bezárás** gombra a panel alján.

[![Képernyőkép a csempe összefoglaló nézetéről a fenti lekérdezési és csempe-beállításokkal.](./media/workbooks-tile-visualizations/tile-settings.png)](./media/workbooks-tile-visualizations/tile-settings.png#lightbox)

A csempék olvasási módban:

[![Képernyőkép a csempe összefoglaló nézetéről olvasási módban.](./media/workbooks-tile-visualizations/tiles-read-mode.png)](./media/workbooks-tile-visualizations/tiles-read-mode.png#lightbox)

## <a name="spark-lines-in-tiles"></a>A csempén található Spark-vonalak

1. Az eszköztár _szerkesztése_ elemre kattintva váltson át a munkafüzet szerkesztési módjára.
2. Adjon hozzá egy nevű időtartomány-paramétert `TimeRange` .
    1. Válassza a **Hozzáadás** lehetőséget, majd *adja hozzá a paramétereket*.
    2. A paraméter vezérlőelemben válassza a **paraméter hozzáadása** elemet.
    3. Adja meg `TimeRange` a *paraméter neve* mezőt, és válassza `Time range picker` a *paraméter típusa* lehetőséget.
    4. Válassza a **Mentés** lehetőséget a panel tetején, majd válassza a **Szerkesztés kész** lehetőséget a paraméter vezérlőelemben.
3. Válassza a **Hozzáadás** , majd a *lekérdezés hozzáadása* lehetőséget, és adja hozzá a log lekérdezés vezérlőelemet a paraméter vezérlőelem alá.
4. Válassza ki a lekérdezés típusát **naplóként**, erőforrástípusként (például Application Insights) és a célként megadott erőforrásokat.
5. A lekérdezési szerkesztővel adja meg az elemzéshez használandó KQL.

    ```kusto
    let topRequests = requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc;
    let topRequestNames = topRequests | project name;
    requests
    | where name in (topRequestNames)
    | make-series Trend = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by name
    | join (topRequests) on name
    | project-away name1, timestamp
    ```

6. Válassza a **Lekérdezés futtatása** lehetőséget. (A lekérdezés futtatása előtt győződjön meg arról, hogy a `TimeRange` választott értékre van állítva.)
7. Állítsa a *vizualizációt* "csempe" értékre.
8. Állítsa a *méretet* "Full" értékre.
9. Válassza a **csempe beállításai** lehetőséget.
    1. A *csempén* állítsa be a következőket:
        * Oszlop használata: `name` .
    2. Az *alcsempén* állítsa be a következőket:
        *  Oszlop használata: `appNAme` .
    3. A *bal oldalon* állítsa be a következőket:
        *  Oszlop használata: `Requests` .
        * Oszlop megjelenítő: `Big Number` .
        * Színpaletta: `Green to Red` .
        * Minimális érték: `0` .
    4. A *lenti* beállításnál állítsa be a következőket:
        * Oszlop használata: `Trend` .
        * Oszlop megjelenítő: `Spark line` .
        * Színpaletta: `Green to Red` .
        * Minimális érték: `0` .
10. Kattintson a **Mentés és bezárás** gombra a panel alján.

![Képernyőfelvétel a csempe vizualizációról Spark-sorral](./media/workbooks-tile-visualizations/spark-line.png)

## <a name="tile-sizes"></a>Csempe mérete

A szerzőnek lehetősége van a csempe szélességének beállítására a csempe beállításaiban.

* `fixed` alapértelmezett

    A csempék alapértelmezett viselkedése azonos rögzített szélesség, kb. 160 képpont széles, valamint a csempék körüli terület.

    ![Képernyőfelvétel a rögzített szélességű csempék megjelenítéséről](./media/workbooks-tile-visualizations/tiles-fixed.png)
* `auto`

    Az egyes címek azonban a tartalom méretének megfelelően csökkennek vagy növekednek, a csempék pedig a csempék nézetének szélességére korlátozódnak (vízszintes görgetés nélkül).

    ![Képernyőfelvétel az automatikus szélességű csempék megjelenítéséről](./media/workbooks-tile-visualizations/tiles-auto.png)
* `full size`

    Minden cím mindig a csempék nézet teljes szélessége lesz, soronként egy cím.

     ![Képernyőfelvétel a teljes méretű szélességű csempék megjelenítéséről](./media/workbooks-tile-visualizations/tiles-full.png)

## <a name="next-steps"></a>Következő lépések

* A csempék is támogatják az összetett görgetősávok megjelenítését. További információt az [összetett sáv dokumentációjában](workbooks-composite-bar.md)találhat.
* Ha többet szeretne megtudni az időparaméterekről, például a `TimeRange` [munkafüzet-idő paraméterei dokumentációjának](workbooks-time.md)megtekintéséhez.