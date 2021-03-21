---
title: Azure Monitor munkafüzetek szöveges paraméterei
description: Egyszerűsítse a komplex jelentéskészítést előre összeépített és egyéni paraméteres munkafüzetekkel. További információ a munkafüzet szövegének paramétereivel kapcsolatban.
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 22078f242ddeb882b39b85769537b9a282741250
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101725526"
---
# <a name="workbook-text-parameters"></a>Munkafüzet szövegének paraméterei

A szövegmező paraméterei egyszerű módszert biztosítanak a munkafüzet felhasználóitól érkező szövegbeviteli adatok gyűjtésére. Akkor használhatók, ha nem célszerű legördülő listát használni a bevitel összegyűjtéséhez (például egy tetszőleges küszöbértéket vagy általános szűrőket). A munkafüzetek lehetővé teszik a szerzők számára a szövegmező alapértelmezett értékének lekérését egy lekérdezésből. Ez olyan érdekes forgatókönyveket tesz lehetővé, mint például az alapértelmezett küszöbérték beállítása a metrika p95 alapján.

A szövegmezők gyakori használata olyan belső változó, amelyet más munkafüzetek vezérlői használnak. Ez a lekérdezés alapértelmezett értékekre való kihasználása, valamint a bemeneti vezérlő olvasási módban való láthatatlanná tétele révén történik. Előfordulhat például, hogy egy felhasználó egy képletből (nem egy felhasználóból) származó küszöbértéket szeretne használni, majd a küszöbértéket a későbbi lekérdezésekben.

## <a name="creating-a-text-parameter"></a>Szöveges paraméter létrehozása
1. Kezdés egy üres munkafüzettel szerkesztési módban.
2. Válassza a _Paraméterek hozzáadása_ lehetőséget a munkafüzetben található hivatkozások közül.
3. Kattintson a kék _paraméter hozzáadása_ gombra.
4. A felugró új paraméter panelen írja be a következőket:
    1. Paraméter neve: `SlowRequestThreshold`
    2. Paraméter típusa: `Text`
    3. Szükséges `checked`
    4. Alapértelmezett érték beolvasása a lekérdezésből: `unchecked`
5. A paraméter létrehozásához válassza az eszköztár mentés elemét.

    ![Egy szöveges paraméter létrehozását bemutató kép](./media/workbooks-text/text-create.png)

A munkafüzet a következőképpen fog kinézni, mint az olvasási módban.

![Egy szöveges paramétert olvasási módban ábrázoló kép](./media/workbooks-text/text-readmode.png)

## <a name="referencing-a-text-parameter"></a>Egy szöveges paraméterre hivatkozik
1. Vegyen fel egy lekérdezés vezérlőelemet a munkafüzetbe úgy, hogy kiválasztja a kék `Add query` hivatkozást, és kiválaszt egy Application Insights erőforrást.
2. A KQL mezőben adja meg a következő kódrészletet:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= {SlowRequestThreshold}) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
3. A 500 értékkel rendelkező Text paraméterrel párosítva a lekérdezés vezérlővel, amely az alábbi lekérdezést ténylegesen futtatja:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= 500) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
4. Lekérdezés futtatása az eredmények megtekintéséhez

    ![A KQL-ben hivatkozott szöveges paramétert ábrázoló kép](./media/workbooks-text/text-reference.png)

> [!NOTE]
> A fenti példában az `{SlowRequestThreshold}` egész értéket jelöli. Ha olyan karakterláncot kérdez le, mint például `{ComputerName}` , módosítania kell a Kusto-lekérdezést úgy, hogy idézőjeleket adjon hozzá `"{ComputerName}"` , hogy a paraméter mezőben idézőjelek nélkül fogadja el a bemenetet.

## <a name="setting-default-values"></a>Alapértelmezett értékek beállítása
1. Kezdés egy üres munkafüzettel szerkesztési módban.
2. Válassza a _Paraméterek hozzáadása_ lehetőséget a munkafüzetben található hivatkozások közül.
3. Kattintson a kék _paraméter hozzáadása_ gombra.
4. A felugró új paraméter panelen írja be a következőket:
    1. Paraméter neve: `SlowRequestThreshold`
    2. Paraméter típusa: `Text`
    3. Szükséges `checked`
    4. Alapértelmezett érték beolvasása a lekérdezésből: `checked`
5. A KQL mezőben adja meg a következő kódrészletet:
    ```kusto
    requests
    | summarize round(percentile(duration, 95), 2)
    ```
    Ez a lekérdezés beállítja a szövegmező alapértelmezett értékét az alkalmazásban lévő összes kérelem 95. percentilis százalékának időtartamára.
6. Lekérdezés futtatása az eredmény megtekintéséhez
7. A paraméter létrehozásához válassza az eszköztár mentés elemét.

    ![Az KQL alapértelmezett értékkel rendelkező szöveges paramétert ábrázoló kép](./media/workbooks-text/text-default-value.png)

> [!NOTE]
> Habár ez a példa Application Insightsi az adatlekérdezéseket, a módszer használható bármilyen log-alapú adatforráshoz – Log Analytics, Azure Resource Graph stb.

## <a name="next-steps"></a>Következő lépések

* [Ismerkedjen](./workbooks-overview.md#visualizations) meg a munkafüzetek számos gazdag vizualizációs lehetőségével.
* A munkafüzet erőforrásaihoz való hozzáférés [szabályozása](./workbooks-access-control.md) és megosztása.