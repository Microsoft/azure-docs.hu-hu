---
title: Azure Monitor munkafüzet Graph vizualizációk
description: Ismerkedjen meg az összes Azure Monitor munkafüzet gráf vizualizációval.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: a538eaf06013dcce6529c91816b419159a2600a1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100620658"
---
# <a name="graph-visualizations"></a>Graph-vizualizációk

A munkafüzetek lehetővé támogatják a naplókból származó adatok alapján tetszőleges gráfok megjelenítését a figyelési entitások közötti kapcsolatok megjelenítéséhez.

Az alábbi ábrán egy számítógépnek a külső számítógépekre irányuló és kívüli különböző portokon keresztül áramló adatok láthatók. Színes típus szerint (számítógép vagy port vagy külső IP-cím), a peremhálózat mérete pedig a közti adatforgalom mennyiségének felel meg. A mögöttes adatok a virtuálisgép-kapcsolatokra irányuló KQL-lekérdezésekből származnak.

[![Képernyőfelvétel a csempe összefoglaló nézetéről](./media/workbooks-graph-visualizations/graph.png)](./media/workbooks-graph-visualizations/graph.png#lightbox)

## <a name="adding-a-graph"></a>Gráf hozzáadása
1. Az eszköztár _szerkesztése_ elemre kattintva váltson át a munkafüzet szerkesztési módjára.
2. A **lekérdezés hozzáadása** hivatkozásra kattintva hozzáadhat egy log lekérdezési vezérlőt a munkafüzethez.
3. Válassza ki a lekérdezés típusát **naplóként**, erőforrástípusként (például Application Insights) és a célként megadott erőforrásokat.
4. A lekérdezési szerkesztővel adja meg az elemzéshez használandó KQL.

    ```kusto
    let data = dependencies
    | summarize Calls = count() by App = appName, Request = operation_Name, Dependency = name
    | extend RequestId = strcat(App, '::', Request);
    let links = data
    | summarize Calls = sum(Calls) by App, RequestId
    | project SourceId = App, TargetId = RequestId, Calls, Kind = 'App -> Request'
    | union (data
        | project SourceId = RequestId, TargetId = Dependency, Calls, Kind = 'Request -> Dependency');
    let nodes = data
    | summarize Calls = sum(Calls) by App
    | project Id = App, Name = App, Calls, Kind = 'App'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Kind = 'Request')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Kind = 'Dependency');
    nodes
    | union (links)
    ```

5. Vizualizáció beállítása **gráfra**
6. A beállítások ablaktábla megnyitásához kattintson a **Graph-beállítások** gombra.
7. A lenti _elrendezés mezőkben_ állítsa be a következőt:
    * Csomópont-azonosító: `Id`
    * Forrás azonosítója: `SourceId`
    * Cél azonosítója: `TargetId`
    * Edge felirat: `None`
    * Peremhálózat mérete: `Calls`
    * Csomópont mérete: `None`
    * Színezés típusa: `Categorical`
    * Csomópont színe mező: `Kind`
    * Színpaletta: `Pastel`
8. A felső _csomópont-formátum beállításainál_ állítsa be a következőt:
    * _Top Content_-use oszlop: `Name` , oszlop megjelenítő: `Text`
    * _Center Content_-use oszlop: `Calls` , oszlop megjelenítő: `Big Number` , színpaletta: `None`
    * _Alsó Content_-use oszlop: `Kind` , oszlop megjelenítő: `Text`
9. Kattintson a _Mentés és bezárás_ gombra a panel alján.

[![Képernyőkép a csempe összefoglaló nézetéről a fenti lekérdezéssel és beállításokkal.](./media/workbooks-graph-visualizations/graph-settings.png)](./media/workbooks-graph-visualizations/graph-settings.png#lightbox)

## <a name="graph-settings"></a>Gráf beállításai

| Beállítás         | Magyarázat                                                                                                        |
|:----------------|:-------------------------------------------------------------------------------------------------------------------|
| `Node Id`       | Kiválaszt egy oszlopot, amely a gráf csomópontjainak egyedi AZONOSÍTÓját adja meg. Az oszlop értéke lehet string vagy szám. |
| `Source Id`     | Kiválaszt egy oszlopot, amely megadja a forrás-csomópontok azonosítóit a gráf szélei számára. Az értékeknek a _csomópont-azonosító_ oszlopban szereplő értékre kell leképezniük. |
| `Target Id`     | Kiválaszt egy oszlopot, amely a diagram szélei számára megadja a cél csomópontok azonosítóit. Az értékeknek a _csomópont-azonosító_ oszlopban szereplő értékre kell leképezniük. |
| `Edge Label`    | Kiválaszt egy oszlopot, amely a diagramon él feliratokat biztosít.                                                            |
| `Edge Size`     | Kiválaszt egy oszlopot, amely megadja azt a metrikát, amelyen a peremhálózat szélessége alapul.                                |
| `Node Size`     | Kiválaszt egy oszlopot, amely megadja azt a metrikát, amelyen a csomópont területe alapulni fog.                                 |
| `Coloring Type` | A Node Coloring séma kiválasztására szolgál.                                                                            |

## <a name="node-coloring-types"></a>Csomópontok színezési típusai

| Színezés típusa | Magyarázat |
|:------------- |:------------|
| `None`        | Minden csomópontnak azonos színnel kell rendelkeznie. |
| `Categorical` | A csomópontok az eredményhalmaz egyik oszlopának értéke vagy kategóriája alapján vannak hozzárendelve. A fenti példában a színezés az eredményhalmaz oszlopának _típusa_ alapján történik. A támogatott paletták a következők:, `Default` `Pastel` és `Cool tone` .  |
| `Field Based` | Ebben a típusban egy oszlop adott RGB-értékeket biztosít a csomóponthoz. A legnagyobb rugalmasságot biztosít, de általában több munkát igényel az engedélyezéshez.  |

## <a name="node-format-settings"></a>Csomópont formátumának beállításai

A graphs-szerzők megadhatják, hogy milyen tartalom kerül a csomópont különböző részeire: felül, balra, középen, jobbra és lefelé. A gráfok bármely megjeleníthető munkafüzetet használhatnak (szöveg, nagy szám, Spark-vonal, ikon stb.).

## <a name="field-based-node-coloring"></a>Mező alapú csomópont színezése

1. Az eszköztár _szerkesztése_ elemre kattintva váltson át a munkafüzet szerkesztési módjára.
2. A **lekérdezés hozzáadása** hivatkozásra kattintva hozzáadhat egy log lekérdezési vezérlőt a munkafüzethez.
3. Válassza ki a lekérdezés típusát **naplóként**, erőforrástípusként (például Application Insights), és a célként megadott erőforrásokat.
4. A lekérdezési szerkesztővel adja meg az elemzéshez használandó KQL.

     ```kusto
    let data = dependencies
    | summarize Calls = count() by App = appName, Request = operation_Name, Dependency = name
    | extend RequestId = strcat(App, '::', Request);
    let links = data
    | summarize Calls = sum(Calls) by App, RequestId
    | project SourceId = App, TargetId = RequestId, Calls, Kind = 'App -> Request'
    | union (data
        | project SourceId = RequestId, TargetId = Dependency, Calls, Kind = 'Request -> Dependency');
    let nodes = data
    | summarize Calls = sum(Calls) by App
    | project Id = App, Name = App, Calls, Color = 'FD7F23'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Color = 'B3DE8E')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Color = 'C9B3D5');
    nodes
    | union (links)
    ```
5. Vizualizáció beállítása *gráfra*
6. Kattintson a **Graph-beállítások**  gombra a beállítások ablaktábla megnyitásához.
7. A lenti *elrendezés mezőkben* állítsa be a következőt:
    * Node Id (Csomópont-azonosító):`Id`
    * Forrás azonosítója: `SourceId`
    * Cél azonosítója: `TargetId`
    * Edge felirat: `None`
    * Peremhálózat mérete: `Calls`
    * Csomópont mérete: `Node`
    * Színezés típusa: `Field Based`
    * Csomópont színe mező: `Color`
8. A felső *csomópont-formátum beállításainál* adja meg a következőt.
    * A *legfelső szintű tartalom* beállításnál állítsa be a következőket:
        * Oszlop használata: `Name` .
        * Oszlop megjelenítő: `Text` .
    * A *középső tartalom* beállításnál állítsa be a következőket:
        * Oszlop használata: `Calls`
        * Oszlop megjelenítő: `Big Number`
        * Színpaletta: `None`
    * A *lenti tartalom* mezőben állítsa be a következőket:
        * Oszlop használata: `Kind`
        * Oszlop megjelenítő: `Text` .
9. Kattintson a **Mentés és bezárás gombra** a panel alján.

[![Képernyőkép a Graph-vizualizációk létrehozásáról a mező alapcsomópontjának színezése alapján.](./media/workbooks-graph-visualizations/graph-field-based.png)](./media/workbooks-graph-visualizations/graph-field-based.png#lightbox)

## <a name="next-steps"></a>Következő lépések

* A gráfok támogatják az összetett görgetősávok megjelenítését is. További tudnivalókért tekintse meg az [összetett sáv dokumentációját](workbooks-composite-bar.md).
* További információ a munkafüzetekben használható [adatforrásokról](workbooks-data-sources.md) .
