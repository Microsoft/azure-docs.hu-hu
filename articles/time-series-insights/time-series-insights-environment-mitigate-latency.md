---
title: A szabályozás figyelése és csökkentése – Azure Time Series Insights | Microsoft Docs
description: Megtudhatja, hogyan figyelheti, diagnosztizálhatja és csökkentheti a késést és a szabályozást okozó teljesítménnyel kapcsolatos problémákat Azure Time Series Insightsban.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: e89189b22b144d9e92ee8315bc6fd9aabe699eec
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91531649"
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights-gen1"></a>A Azure Time Series Insights Gen1 késésének csökkentése a szabályozás monitorozásával és enyhítésével

> [!CAUTION]
> Ez egy Gen1-cikk.

Ha a bejövő adatmennyiség meghaladja a környezete konfigurációját, késést vagy szabályozást tapasztalhat Azure Time Series Insightsban.

A késleltetés és a szabályozás elkerülése érdekében az elemezni kívánt adatmennyiséghez megfelelően konfigurálja a környezetet.

A következő esetekben valószínűleg késést és szabályozást tapasztal:

- Adjon hozzá olyan eseményforrás, amely olyan régi adatforrásokat tartalmaz, amelyek túllépik a megadott bejövő forgalom arányát (Azure Time Series Insights fel kell vennie).
- További eseményforrás hozzáadása egy környezethez, amely a további eseményekből származó tüske (ami túllépheti a környezet kapacitását).
- Nagy mennyiségű történelmi eseményt küldhet egy adott esemény forrásaként, ami késést eredményez (Azure Time Series Insights fel kell fognia).
- Összekapcsolhatja a telemetria-vel való hivatkozási adatmennyiséget, ami nagyobb méretű eseményt eredményez. Az engedélyezett csomagméret maximális mérete 32 KB; az 32 KB-nál nagyobb adatcsomagok csonkítva vannak.

## <a name="video"></a>Videó

### <a name="learn-about-azure-time-series-insights-data-ingress-behavior-and-how-to-plan-for-itbr"></a>Ismerkedjen meg Azure Time Series Insights adatforgalom viselkedésével és a tervezésével.</br>

> [!VIDEO https://www.youtube.com/embed/npeZLAd9lxo]

## <a name="monitor-latency-and-throttling-with-alerts"></a>Késés és szabályozás figyelése riasztásokkal

A riasztások segíthetnek a környezetében előforduló késési problémák diagnosztizálásában és enyhítésében.

1. A Azure Portal válassza ki Azure Time Series Insights-környezetét. Ezután válassza a **riasztások** lehetőséget.

   [![Riasztás hozzáadása a Azure Time Series Insights-környezethez](media/environment-mitigate-latency/mitigate-latency-add-alert.png)](media/environment-mitigate-latency/mitigate-latency-add-alert.png#lightbox)

1. Válassza a **+ Új riasztási szabály** lehetőséget. Ekkor megjelenik a **szabály létrehozása** panel. Válassza a **Hozzáadás** lehetőséget a **feltétel** alatt.

   [![Riasztás panel hozzáadása](media/environment-mitigate-latency/mitigate-latency-add-pane.png)](media/environment-mitigate-latency/mitigate-latency-add-pane.png#lightbox)

1. Ezután konfigurálja a jel logikájának pontos feltételeit.

   [![Jellogika konfigurálása](media/environment-mitigate-latency/configure-alert-rule.png)](media/environment-mitigate-latency/configure-alert-rule.png#lightbox)

   Innentől kezdve a következő feltételek valamelyikével konfigurálhatja a riasztásokat:

   |Metric  |Leírás  |
   |---------|---------|
   |**Bejövő fogadott bájtok száma**     | Az esemény forrásaiból beolvasott nyers bájtok száma. A nyers darabszám általában a tulajdonság nevét és értékét tartalmazza.  |  
   |**A bejövő forgalom érvénytelen üzeneteket kapott**     | Az összes Azure-Event Hubs vagy az Azure IoT Hub-eseményforrás által beolvasott érvénytelen üzenetek száma.      |
   |**Bejövő fogadott üzenetek**   | Az összes Event Hubs vagy IoT hub-eseményforrás által olvasott üzenetek száma.        |
   |**Bejövő forgalomban tárolt bájtok**     | A lekérdezéshez tárolt és elérhető események teljes mérete. A méret kiszámítása csak a tulajdonság értékén történik.        |
   |**Bejövő tárolt események**     |   A lekérdezéshez rendelkezésre álló és elérhető összeolvasztott események száma.      |
   |**Beérkező üzenetek időeltolódása**    |  Az üzenet az várólistán lévő való beérkezése és a bejövő forgalomban történő feldolgozás időpontja közötti különbség másodpercben kifejezve.      |
   |**Bejövő fogadott üzenetek számának késése**    |  Az eseményforrás-partíció utolsó várólistán lévő-üzenetének sorszáma és a bejövő forgalomban feldolgozott üzenet sorszáma közötti különbség.      |

   Válassza a **Kész** lehetőséget.

1. A kívánt jel logikájának konfigurálása után vizuálisan tekintse át a kiválasztott riasztási szabályt.

   [![Késés nézet és ábrázolás](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png)](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png#lightbox)

## <a name="throttling-and-ingress-management"></a>Szabályozás és bejövő forgalom kezelése

- Ha szabályozza a szabályozást, akkor a rendszer a *beérkező üzenetek időkorlátja* értékének megadásával értesíti arról, hogy hány másodpercig tart a Azure Time Series Insights-környezete, és hogy az üzenet mikor kapja meg az esemény forrását (kivéve a Appx indexelési idejét. 30-60 másodperc).  

  A *bejövő fogadott üzenetek számának késési* értékének is szerepelnie kell, ami lehetővé teszi, hogy megtudja, hány üzenet mögött van.  A legkönnyebben elsajátítható, hogy növelje a környezet kapacitását olyan méretre, amely lehetővé teszi a különbség leküzdését.  

  Ha például az S1-környezet 5 000 000-üzenetek késését mutatja be, akkor a környezet méretét akár hat egységre is növelheti, hogy felkészüljön.  A gyorsabb felzárkózás érdekében még tovább növelheti. A felmerülési időszak gyakori esemény, amikor először telepítenek egy környezetet, különösen akkor, ha olyan eseménnyel csatlakozik, amely már rendelkezik eseményekkel, vagy ha nagy mennyiségű előzményt tölt fel.

- Egy másik módszer egy **bejövő tárolt események** riasztásának beállítása >= egy küszöbérték kis mértékben a teljes környezeti kapacitás alatt 2 órán keresztül.  Ez a riasztás segít megismerni, hogy folyamatosan van-e kapacitása, ami nagy valószínűséggel jelzi a késést.

  Ha például három S1 egység van kiépítve (vagy 2100 eseményt percenként beáramló kapacitással), megadhat egy **bejövő tárolt események** riasztást >= 1900 eseményekhez 2 órán keresztül. Ha folyamatosan túllépi ezt a küszöbértéket, és így aktiválja a riasztást, valószínűleg kiosztották.  

- Ha azt gyanítja, hogy a szabályozása folyamatban van, összehasonlíthatja a **bejövő beérkező üzeneteket** az eseményforrás egressed üzeneteivel.  Ha az Event hub-ba való belépés nagyobb, mint a **bejövő fogadott üzenetek**, a Azure Time Series Insights valószínűleg szabályozva lesznek.

## <a name="improving-performance"></a>A teljesítmény javítása

A szabályozás és a késések csökkentése érdekében a legjobb megoldás a környezet kapacitásának növelésére.

A késleltetés és a szabályozás elkerülése érdekében az elemezni kívánt adatmennyiséghez megfelelően konfigurálja a környezetet. Ha további információt szeretne arról, hogyan adhat hozzá kapacitást a környezethez, olvassa el [a környezet skálázása](time-series-insights-how-to-scale-your-environment.md)című témakört.

## <a name="next-steps"></a>Következő lépések

- További információ a [Azure Time Series Insights-környezetben felmerülő problémák diagnosztizálásáról és megoldásáról](time-series-insights-diagnose-and-solve-problems.md).

- Ismerje meg [, hogyan méretezheti Azure Time Series Insights-környezetét](time-series-insights-how-to-scale-your-environment.md).
