---
title: Az adatösszekötők állapotának figyelése az Azure Sentinel-munkafüzettel | Microsoft Docs
description: Az állapot-figyelési munkafüzettel nyomon követheti az adatösszekötők kapcsolatát és teljesítményét.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2020
ms.author: yelevin
ms.openlocfilehash: 7d52b76601a617f62ae5b10fa38841ef2608bf49
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94656990"
---
# <a name="monitor-the-health-of-your-data-connectors-with-this-azure-sentinel-workbook"></a>Az adatösszekötők állapotának figyelése az Azure Sentinel-munkafüzettel

Az **adatösszekötők állapot-figyelési munkafüzete** lehetővé teszi, hogy nyomon kövesse az adatösszekötők állapotát, a kapcsolatot és a teljesítményt az Azure Sentinel szolgáltatásból. A munkafüzet további figyelőket biztosít, észleli a rendellenességeket, és betekintést nyújt a munkaterület adatfeldolgozási állapotára. A munkafüzet logikája segítségével figyelheti a betöltött adatmennyiség általános állapotát, és egyéni nézeteket és szabály-alapú riasztásokat hozhat létre.

## <a name="use-the-health-monitoring-workbook"></a>Az állapot figyelése munkafüzet használata

1. Az Azure Sentinel-portálon válassza a **munkafüzetek** lehetőséget a **fenyegetések kezelése** menüből.

1. A **munkafüzetek** katalógusában adja meg a keresősáv *állapotát* , majd az eredmények közül válassza az **adatgyűjtési állapot figyelése** lehetőséget.

1. Válassza a **sablon megtekintése** lehetőséget, hogy a munkafüzetet használja, vagy válassza a **Mentés** lehetőséget a munkafüzet szerkeszthető másolatának létrehozásához. A másolat létrehozásakor válassza a **mentett munkafüzet megtekintése** lehetőséget.

1. A munkafüzetben először válassza ki a megtekinteni kívánt **előfizetést** és **munkaterületet** , majd adja meg a **TimeRange** az igényeinek megfelelő szűréshez. A **Súgó megjelenítése** váltógomb használatával jelenítheti meg a munkafüzet helyi magyarázatát.

    :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-1.png" alt-text="adatösszekötő állapotának figyelése munkafüzet kezdőlapja" lightbox="media/monitor-data-connector-health/data-health-workbook-1.png":::

A munkafüzet három Többlapos szakaszt tartalmaz:

1. Az **Áttekintés** lapon a kiválasztott munkaterületen lévő adatfeldolgozás általános állapota látható: mennyiségi mértékek, EPS-díjak és utolsó naplózott idő.

1. Az **adatgyűjtési rendellenességek** lapon az adatgyűjtési folyamat, tábla és adatforrás által észlelt rendellenességek észlelhetők. Az egyes lapok rendellenességeket mutatnak be egy adott táblára vonatkozóan (az **általános** lapon táblázatok gyűjteménye szerepel). A rendellenességek kiszámítása a **series_decompose_anomalies ()** függvénnyel történik, amely egy **anomália pontszámot** ad vissza. [További információk erről a függvényről](/azure/data-explorer/kusto/query/series-decompose-anomaliesfunction?WT.mc_id=Portal-fx). A következő paraméterek megadásával értékelje ki a függvényt:

    - **AnomaliesTimeRange**: Ez az időválasztó kizárólag az adatgyűjtési anomáliák nézetéhez használható.
    - **SampleInterval**: Az adatok mintavételezésének időintervalluma az adott időtartományban. Az anomália-pontszám kiszámítása csak az utolsó intervallum adataira vonatkozóan történik.
    - **PositiveAlertThreshold**: Ez az érték határozza meg az anomália-pontszám pozitív küszöbértékét. Decimális értékeket fogad el.
    - **NegativeAlertThreshold**: Ez az érték határozza meg az anomália-pontszám negatív küszöbértékét. Decimális értékeket fogad el.

        :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-2.png" alt-text="adatösszekötő állapot-figyelési munkafüzetének rendellenességei lap" lightbox="media/monitor-data-connector-health/data-health-workbook-2.png":::

1. Az **ügynök adatai** lapon láthatók a különböző gépekre telepített log Analytics ügynökök állapotával kapcsolatos információk, akár az Azure-beli virtuális gép, akár a Felhőbeli virtuális gép, a helyszíni virtuális gép vagy a fizikai. A következőket figyelheti:

   - Rendszerhely

   - Szívverés állapota és késése

   - Rendelkezésre álló memória és lemezterület

   - Ügynök műveletei

    Ebben a szakaszban ki kell választania a gépek környezetét ismertető lapot: válassza az **Azure által felügyelt gépek** fület, ha csak az Azure arc által felügyelt gépeket szeretné megtekinteni; Válassza a **minden gép** fület, hogy a felügyelt és a nem Azure-beli gépeket is megtekintse a telepített log Analytics ügynökkel.

    :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-3.png" alt-text="adatösszekötő állapotának figyelése munkafüzet-ügynök adatai lap" lightbox="media/monitor-data-connector-health/data-health-workbook-3.png":::

## <a name="next-steps"></a>Következő lépések
Megtudhatja, hogyan [helyezheti üzembe](quickstart-get-visibility.md) [adatait az Azure sentinelben](quickstart-onboard.md), hogyan [csatlakozhat az adatforrásokhoz](connect-data-sources.md), és hogyan sajátíthatja el az adatait