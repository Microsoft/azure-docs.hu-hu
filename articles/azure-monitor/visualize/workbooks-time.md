---
title: Azure Monitor munkafüzetek időbeli paraméterei
description: Megtudhatja, hogyan állíthatja be az időparamétereket, hogy a felhasználók beállítsák az elemzés időkörnyezetét. Az idő paramétereit szinte minden jelentés használja.
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 9f4f5f82423f0ecb4e810eab2d675e95b8b66763
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101717162"
---
# <a name="workbook-time-parameters"></a>Munkafüzet-idő paraméterei

Az időparaméterek lehetővé teszik a felhasználók számára az elemzés időkörnyezetének beállítását, és szinte minden jelentés használja őket. Viszonylag egyszerű beállítás és használat – lehetővé teszi a szerzők számára, hogy megadják a legördülő menüben megjelenítendő időtartományokat, beleértve az egyéni időtartományok beállítását is. 

## <a name="creating-a-time-parameter"></a>Time paraméter létrehozása
1. Kezdés egy üres munkafüzettel szerkesztési módban.
2. Válassza a _Paraméterek hozzáadása_ lehetőséget a munkafüzetben található hivatkozások közül.
3. Kattintson a kék _paraméter hozzáadása_ gombra.
4. A felugró új paraméter panelen írja be a következőket:
    1. Paraméter neve: `TimeRange`
    2. Paraméter típusa: `Time range picker`
    3. Szükséges `checked`
    4. Rendelkezésre álló időtartományok: elmúlt óra, utolsó 12 óra, utolsó 24 óra, utolsó 48 óra, elmúlt 3 nap, utolsó 7 nap, és egyéni időtartomány-kijelölés engedélyezése
5. A paraméter létrehozásához válassza az eszköztár mentés elemét.

    ![A Time Range paraméter létrehozását bemutató kép](./media/workbooks-time/time-settings.png)

A munkafüzet a következőképpen fog kinézni, mint az olvasási módban.

![Az időtartomány-paramétert olvasási módban ábrázoló kép](./media/workbooks-time/parameters-time.png)

## <a name="referencing-a-time-parameter"></a>Egy Time paraméterre hivatkozik
### <a name="via-bindings"></a>Kötések útján
1. Vegyen fel egy lekérdezés vezérlőelemet a munkafüzetbe, és válasszon ki egy Application Insights erőforrást.
2. A legtöbb munkafüzet-vezérlő támogatja az _időtartomány_ hatókör-választóját. Nyissa meg az _időtartomány_ legördülő listát, és válassza ki az `{TimeRange}` időkorlát-paraméterek csoportot alul.
3. Ezzel a beállítással az időtartomány paraméter a diagram időtartományához köthető. A minta lekérdezés időbeli hatóköre mostantól az elmúlt 24 órában érhető el.
4. Lekérdezés futtatása az eredmények megtekintéséhez

    ![A kötéseken keresztül hivatkozott időtartomány-paramétert ábrázoló kép](./media/workbooks-time/time-binding.png)

### <a name="in-kql"></a>A KQL
1. Vegyen fel egy lekérdezés vezérlőelemet a munkafüzetbe, és válasszon ki egy Application Insights erőforrást.
2. A KQL adja meg az időtartomány szűrőt a (z) paraméter használatával: `| where timestamp {TimeRange}`
3. Ez kibővíti a lekérdezés értékelésének idejét a értékre `| where timestamp > ago(1d)` , amely a paraméter időtartományának értéke.
4. Lekérdezés futtatása az eredmények megtekintéséhez

    ![A KQL-ben hivatkozott időtartományt ábrázoló kép](./media/workbooks-time/time-in-code.png)

### <a name="in-text"></a>Szövegben 
1. Adjon hozzá egy szövegbeviteli vezérlőt a munkafüzethez.
2. A Markdown írja be a `The chosen time range is {TimeRange:label}`
3. Válassza a _Szerkesztés kész_ lehetőséget
4. A szöveg vezérlőelem szövege a következő lesz: _a kiválasztott időtartomány az elmúlt 24 óra_

## <a name="time-parameter-options"></a>Az idő paraméter beállításai
| Paraméter | Magyarázat | Példa |
| ------------- |:-------------|:-------------|
| `{TimeRange}` | Időtartomány felirata | Az elmúlt 24 óra |
| `{TimeRange:label}` | Időtartomány felirata | Az elmúlt 24 óra |
| `{TimeRange:value}` | Időtartomány értéke | > ago (1d) |
| `{TimeRange:query}` | Időtartomány-lekérdezés | > ago (1d) |
| `{TimeRange:start}` | Időtartomány kezdési időpontja | 3/20/2019 4:18 PM |
| `{TimeRange:end}` | Időbeli tartomány befejezési időpontja | 3/21/2019 4:18 PM |
| `{TimeRange:grain}` | Időtartomány-gabona | 30 m |


### <a name="using-parameter-options-in-a-query"></a>Paraméter-beállítások használata lekérdezésekben
```kusto
requests
| make-series Requests = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain}
```

## <a name="next-steps"></a>Következő lépések

* [Ismerkedjen](./workbooks-overview.md#visualizations) meg a munkafüzetek számos gazdag vizualizációs lehetőségével.
* A munkafüzet erőforrásaihoz való hozzáférés [szabályozása](./workbooks-access-control.md) és megosztása.