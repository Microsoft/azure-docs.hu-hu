---
title: Azure Monitor munkafüzet faszerkezetes megjelenítése
description: Ismerkedjen meg az összes Azure Monitor munkafüzet favizualizációval.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: a815f4dcf7c76d4c11b4f2e227802b15f9c92a54
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100619965"
---
# <a name="tree-visualizations"></a>Fa vizualizációk

A munkafüzetek farácson keresztül támogatják a hierarchikus nézeteket. A fák lehetővé teszik, hogy egyes sorok kiterjeszthetők legyenek a következő szintre a részletezési élmény érdekében.

Az alábbi példában a tároló állapot metrikái (Munkakészlet mérete) láthatók farácsként. A legfelső szintű csomópontok itt az Azure Kubernetes Service (ak) csomópontjai, a következő szint hüvelyek, a végső szint pedig tároló. Figyelje meg, hogy továbbra is formázhatja az oszlopokat, például egy rácsban (hő, ikonok, hivatkozás). Ebben az esetben az alapul szolgáló adatforrás egy Log Analytics munkaterület, AK-naplókkal.

[![Képernyőfelvétel a csempe összefoglaló nézetéről](./media/workbooks-tree-visualizations/trees.png)](./media/workbooks-tree-visualizations/trees.png#lightbox)

## <a name="adding-a-tree-grid"></a>Fa rács hozzáadása
1. Az eszköztár _szerkesztése_ elemre kattintva váltson át a munkafüzet szerkesztési módjára.
2. Válassza a **Hozzáadás** , majd a *lekérdezés hozzáadása* lehetőséget, ha egy log lekérdezés vezérlőelemet szeretne hozzáadni a munkafüzethez.
3. Válassza ki a lekérdezés típusát **naplóként**, erőforrástípusként (például Application Insights), és a célként megadott erőforrásokat.
4. Az elemzéshez használandó KQL megadásához használja a lekérdezés-szerkesztőt
    ```kusto
    requests
    | summarize Requests = count() by ParentId = appName, Id = name
    | extend Kind = 'Request', Name = strcat('🌐 ', Id)
    | union (requests
    | summarize Requests = count() by Id = appName
    | extend Kind = 'Request', ParentId = '', Name = strcat('📱 ', Id))
    | project Name, Kind, Requests, Id, ParentId
    | order by Requests desc
    ```
5. Vizualizáció beállítása **rácsra**
6. A beállítások panel megnyitásához kattintson az **oszlop beállításai** gombra.
7. Az alul található **Tree/Group By Settings** szakaszban állítsa be a következőt:
    * Fa típusa: `Parent/Child`
    * Azonosító mező: `Id`
    * Szülő-azonosító mező: `ParentId`
    * A Expander megjelenítése: `Name`
    * Jelölje be a *fa felső szintjének kibontása* jelölőnégyzetet.
8. Az _oszlopok_ szakaszban, a felső részen állítsa be a következőt:
    * _Azonosító_ – oszlop megjelenítő: `Hidden`
    * _Szülő-azonosító_ – oszlop megjelenítő: `Hidden`
    * _Kérelmek_ – oszlop megjelenítő: `Bar` , szín: `Blue` , minimális érték: `0`
9. Kattintson a **Mentés és bezárás** gombra a panel alján.

[![Képernyőkép a csempe összefoglaló nézetéről a fenti lekérdezéssel és beállításokkal.](./media/workbooks-tree-visualizations/tree-settings.png)](./media/workbooks-tree-visualizations/tree-settings.png#lightbox)

## <a name="tree-settings"></a>Fa beállításai

| Beállítás | Magyarázat |
|:------------- |:-------------|
| `Id Field` | A rács minden sorának egyedi azonosítója. |
| `Parent Id Field` | Az aktuális sor szülőjének azonosítója. |
| `Show the expander on` | Az az oszlop, amelyen be kell mutatni a fa kiexpanderét. A farácsok esetében gyakori, hogy elrejtsék az azonosítót és a szülő azonosító mezőt, mert nem nagyon olvashatók. Ehelyett a Expander egy olvashatóbb értéket tartalmazó mezőben jelenik meg, például az entitás nevével. |
| `Expand the top level of the tree` | Ha be van jelölve, a farács a legfelső szinten lesz kibontva. Hasznos, ha további információkat szeretne megjeleníteni alapértelmezetten. |

## <a name="grouping-in-a-grid"></a>Csoportosítás rácson

A csoportosítás lehetővé teszi, hogy a fentiekhez hasonló hierarchikus nézeteket hozzon létre lényegesen egyszerűbb lekérdezésekkel. Elveszíti az összesítést a fa belső csomópontjain, de bizonyos esetekben elfogadható lesz. A *Group By* használatával fanézeteket építhet ki, ha a mögöttes eredményhalmaz nem alakítható át megfelelő szabad formátumba, például: riasztás, állapot és metrikus adat.

## <a name="adding-a-tree-using-grouping"></a>Fa hozzáadása csoportosítás használatával

1. Az eszköztár _szerkesztése_ elemre kattintva váltson át a munkafüzet szerkesztési módjára.
2. Válassza a **Hozzáadás** , majd a *lekérdezés hozzáadása* lehetőséget, ha egy log lekérdezés vezérlőelemet szeretne hozzáadni a munkafüzethez.
3. Válassza ki a lekérdezés típusát **naplóként**, erőforrástípusként (például Application Insights) és a célként megadott erőforrásokat.
4. Az elemzéshez használandó KQL megadásához használja a lekérdezés-szerkesztőt
    ```kusto
    requests
    | summarize Requests = count() by App = appName, RequestName = name
    | order by Requests desc
    ```
1. Állítsa be a vizualizációt *rácsra*.
2. A beállítások panel megnyitásához kattintson az **oszlop beállításai gombra** .
3. Az alul található *Tree/Group By Settings* szakaszban állítsa be a következőt:
    * Fa típusa: `Group By`
    * Csoportosítás: `App`
    * Ezután: `None`
    * Jelölje be a *fa felső szintjének kibontása* jelölőnégyzetet.
4. Az *oszlopok* szakaszban, a felső részen állítsa be a következőt:
    * *Alkalmazás* -oszlop megjelenítő: `Hidden`
    * *Kérelmek* – oszlop megjelenítő: `Bar` , szín: `Blue` , minimális érték: `0`
5. Kattintson a **Mentés és bezárás** gombra a panel alján.

[![A favizualizációk munkafüzetekbe való létrehozását bemutató képernyőkép](./media/workbooks-tree-visualizations/tree-group-create.png)](./media/workbooks-tree-visualizations/tree-group-create.png#lightbox)

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan hozhat létre [gráfokat a munkafüzetekben](workbooks-graph-visualizations.md).
* Megtudhatja, hogyan hozhat létre [csempéket a munkafüzetekben](workbooks-tile-visualizations.md).
