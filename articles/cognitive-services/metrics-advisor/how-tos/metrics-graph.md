---
title: Metrikai tanácsadó metrikái gráf
titleSuffix: Azure Cognitive Services
description: A metrikák gráfjának konfigurálása és a kapcsolódó rendellenességek megjelenítése az adatokban.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mbullwin
ms.openlocfilehash: bcedef4a1339dacaff3fe841e97f985c42320819
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92043153"
---
# <a name="how-to-build-a-metrics-graph-to-analyze-related-metrics"></a>Útmutató: mérőszámok gráfjának létrehozása a kapcsolódó metrikák elemzéséhez

A metrikák tanácsadójában minden mérőszámot egy olyan modell külön figyel, amely a múltbeli adatokból tanulja meg a jövőbeli trendek előrejelzését. Minden metrika külön modellt alkalmaz. Bizonyos esetekben azonban több mérőszám is kapcsolódhat egymáshoz, és a rendellenességeket több mérőszámban kell elemezni. A **metrikák gráf** ezt segíti. 

Ha például különböző telemetria-adatfolyamok vannak külön mérőszámokban, a metrikák Advisor külön fogja figyelni őket. Ha az egyik mérőszámban mutatkozó anomáliák más rendellenességeket okoznak, a kapcsolatok megkeresése és az adatok alapvető oka hasznos lehet az incidensek kezelése során. A metrikák gráf segítségével létrehozhat egy észlelt rendellenességekből álló vizuális topológiai gráfot. 

## <a name="select-a-metric-to-put-the-first-node-to-the-graph"></a>Válassza ki azt a metrikát, amely az első csomópontot a gráfba helyezi.

Kattintson a **mérőszámok gráf** fülére a navigációs sávon. A mérőszámok gráfjának első lépése a csomópont elhelyezése a gráfon. Válasszon ki egy adatcsatornát és egy mérőszámot az oldal tetején. Egy csomópont jelenik meg az alsó panelen. 

:::image type="content" source="../media/graph/metrics-graph-select.png" alt-text="Metrika kiválasztása":::

## <a name="add-a-noderelation-on-existing-node"></a>Csomópont/kapcsolatok hozzáadása a meglévő csomóponthoz

Ezután hozzá kell adnia egy másik csomópontot, és meg kell adnia egy kapcsolatot egy meglévő csomópont (ok) hoz. Válasszon ki egy meglévő csomópontot, és kattintson rá a jobb gombbal. A helyi menü több lehetőséggel is megjelenhet. 

Kattintson a **kapcsolat hozzáadása** lehetőségre, és választhat egy másik metrikát, és megadhatja a két csomópont közötti kapcsolat típusát. Adott dimenzió-szűrőket is alkalmazhat. 

:::image type="content" source="../media/graph/metrics-graph-node-action.png" alt-text="Csomópont és rokon hozzáadása":::

A fenti lépések megismétlése után az összes kapcsolódó metrika közötti kapcsolatokat leíró metrikai diagramot fog tartalmazni.
**Tipp a csomópontok színeit**
> [!TIP]
> - Amikor kijelöl egy mérőszámot és egy dimenzió szűrőt, a gráfban található azonos metrikával és dimenzióval rendelkező csomópontok **<font color=blue>kék színnel</font>** lesznek színezve.
> - A diagram metrikáját jelölő, nem kijelölt csomópontok színe **<font color=green>zöld</font>** színű lesz.
> - Ha az aktuális metrika rendellenességet észlel, a csomópont színe **<font color=red>piros</font>** lesz.

## <a name="view-related-metrics-anomaly-status-in-incident-hub"></a>Kapcsolódó metrikai rendellenesség állapotának megtekintése az incidens hub-ban

A metrikák gráfjának létrehozásakor a diagramon belüli mérőszámokon észlelt rendellenességek észlelése esetén megtekintheti a kapcsolódó anomáliák állapotát, és magas szintű nézetet kaphat az incidensről. 

Kattintson egy incidensre a gráfon belül, majd görgessen le a **több mérőszám elemzése** alá a diagnosztikai adatok alatt.

:::image type="content" source="../media/graph/metrics-graph-cross-metrics-analysis.png" alt-text="Kapcsolódó mérőszámok és rendellenességek megtekintése":::

## <a name="next-steps"></a>Következő lépések

- [Anomáliadetektálás beállítása visszajelzés használatával](anomaly-feedback.md)
- [Incidens diagnosztizálása](diagnose-incident.md).
- [Metrikák konfigurálása és a konfiguráció észlelésének finomhangolása](configure-metrics.md)
