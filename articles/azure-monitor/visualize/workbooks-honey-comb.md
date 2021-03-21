---
title: Azure Monitor munkafüzet mézes fésű-vizualizációk
description: Ismerkedjen meg Azure Monitor munkafüzet mézes fésű-vizualizációkkal.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/18/2020
ms.author: lagayhar
ms.openlocfilehash: c91912f711b912c54c1673f2f92e998b4d9ea9db
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101736933"
---
# <a name="honey-comb-visualizations"></a>Honey comb-vizualizációk

A méz-fésűk lehetővé teszik a mérőszámok vagy kategóriák nagy sűrűségű nézeteit, amelyek opcionálisan csoportokba csoportosíthatók. Hasznosak lehetnek a pontok vizuális azonosításában és a fúrásban.

Az alábbi képen a virtuális gépek CPU-kihasználtsága látható két előfizetésen belül. Minden cella a virtuális gépet jelöli, a szín/címke pedig az átlagos CPU-kihasználtságot (a vörösök a forró gépeket jelenti). A virtuális gépek előfizetéssel vannak csoportosítva.

[![A képernyőfelvételen a virtuális gépek CPU-kihasználtsága látható két előfizetésen belül](.\media\workbooks-honey-comb\cpu-example.png)](.\media\workbooks-honey-comb\cpu-example.png#lightbox)

## <a name="adding-a-honey-comb"></a>Mézes fésű hozzáadása

1. Az eszköztár szerkesztése elemre kattintva váltson át a munkafüzet szerkesztési módjára.
2. A **Hozzáadás**  gombra kattintva *adja hozzá a lekérdezést* , és vegyen fel egy napló lekérdezés vezérlőelemet a munkafüzetbe.
3. Válassza a "naplók" *adatforrásként*"log Analytics" *értéket*, és az *erőforrás* -ponthoz olyan munkaterületet, amelyen a virtuális gép teljesítményének naplója van.
4. A lekérdezési szerkesztővel adja meg az elemzéshez használandó KQL.

```kusto
    Perf
| where CounterName == 'Available MBytes'
| summarize CounterValue = avg(CounterValue) by Computer, _ResourceId
| extend ResourceGroup = extract(@'/subscriptions/.+/resourcegroups/(.+)/providers/microsoft.compute/virtualmachines/.+', 1, _ResourceId)
| extend ResourceGroup = iff(ResourceGroup == '', 'On-premise computers', ResourceGroup), Id = strcat(_ResourceId, '::', Computer)
```

5. Lekérdezés futtatása.
6. Állítsa a *vizualizációt* a "Graph" értékre.
7. Válassza a **Graph-beállítások** lehetőséget.
    1. A lenti *elrendezés mezőkben* állítsa be a következőt:
        1. Gráf típusa: **kaptár-fürtök**.
        2. Csomópont-azonosító: `Id` .
        3. Csoportosítás: `None` .
        4. Csomópont mérete: 100.
        5. Hatszögek közötti margó: `5` .
        6. Színezés típusa: **hő**.
        7. Csomópont színe mező: `CouterValue` .
        8. Színpaletta: `Red to Green` .
        9. Minimális érték: `100` .
        10. Maximális érték: `2000` .
    2. A felső *csomópont-formátum beállításainál* állítsa be a következőt:
        1. Leggyakoribb tartalom:
            1. Oszlop használata: `Computer` .
            2. Oszlop megjelenítő " `Text` .
        9. Központ tartalma:
            1. Oszlop használata: `CounterValue` .
            2. Oszlop megjelenítő: `Big Number` .
            3. Színpaletta: `None` .
            4. Jelölje be az *Egyéni szám formázása* négyzetet.
            5. Egységek: `Megabytes` .
            6. Maximális tört számjegyek: `1` .
8. Kattintson a **Mentés és bezárás** gombra a panel alján.

[![A fenti lekérdezéssel és beállításokkal rendelkező lekérdezés-vezérlőelem, Graph-beállítások és méz-fésű képernyőképe](.\media\workbooks-honey-comb\available-memory.png)](.\media\workbooks-honey-comb\available-memory.png#lightbox)

## <a name="honey-comb-layout-settings"></a>A mézes fésű elrendezésének beállításai

| Beállítás | Magyarázat |
|:------------- |:-------------|
| `Node Id` | Kiválaszt egy oszlopot, amely a csomópontok egyedi AZONOSÍTÓját adja meg. Az oszlop értéke lehet string vagy szám. |
| `Group By Field` | Válassza ki azt az oszlopot, amelyre a csomópontokat be szeretné jelölni. |
| `Node Size` | Beállítja a hatszögletű cellák méretét. Használja a `Margin between hexagons` tulajdonságot a méz-fésű diagramjának kinézetének testreszabásához. |
| `Margin between hexagons` | Beállítja a hatszögletű cellák közötti térközt. Használja a `Node size` tulajdonságot a méz-fésű diagramjának kinézetének testreszabásához. |
| `Coloring type` | Kiválasztja a csomópont színéhez használni kívánt sémát. |
| `Node Color Field` | Kiválaszt egy oszlopot, amely megadja azt a metrikát, amelyen a csomópont területe alapulni fog. |

## <a name="node-coloring-types"></a>Csomópontok színezési típusai

| Színezés típusa | Magyarázat |
|:------------- |:-------------|
| `None` | Minden csomópontnak azonos színnel kell rendelkeznie. |
| `Categorical` | A csomópontok az eredményhalmaz egyik oszlopának értéke vagy kategóriája alapján vannak hozzárendelve. A fenti példában a színezés az eredményhalmaz oszlopának _típusa_ alapján történik. A támogatott paletták a következők:, `Default` `Pastel` és `Cool tone` .  |
| `Heatmap` | Ebben a típusban a cellák színesek egy metrikus oszlop és egy színpaletta alapján. Ez egyszerű módszert kínál a metrikák közötti oldalpárok kiemelésére a cellák között. |
| `Thresholds` | Ebben a típusban a cellák színét küszöbérték-szabályok határozzák meg (például _cpu > 90% => piros, 60% > CPU > 90% => sárga, CPU < 60% => zöld_) |
| `Field Based` | Ebben a típusban egy oszlop adott RGB-értékeket biztosít a csomóponthoz. A legnagyobb rugalmasságot biztosít, de általában több munkát igényel az engedélyezéshez.  |
      
## <a name="node-format-settings"></a>Csomópont formátumának beállításai

A méz-fésű szerzője megadhatja, hogy milyen tartalom kerül a csomópont különböző részeire: felül, balra, középen, jobbra és lefelé. A szerzők szabadon használhatják a renderelő munkafüzeteket (szöveg, nagy szám, Spark-vonal, ikon stb.).

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan hozhat létre [összetett sáv-megjelenítőt a munkafüzetekben](workbooks-composite-bar.md).
- Megtudhatja, hogyan [importálhatja a Azure monitor log-információkat a Power BIba](./powerbi.md).