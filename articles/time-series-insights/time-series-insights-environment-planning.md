---
title: Tervezze meg Gen1-környezetét – Azure Time Series Insights | Microsoft Docs
description: Ajánlott eljárások a Azure Time Series Insights Gen1-környezet előkészítéséhez, konfigurálásához és üzembe helyezéséhez.
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 5e0f1ea42aa2ba888b89dd652d3397a3a2163a3e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95016207"
---
# <a name="plan-your-azure-time-series-insights-gen1-environment"></a>A Azure Time Series Insights Gen1-környezet megtervezése

> [!CAUTION]
> Ez egy Gen1-cikk.

Ez a cikk azt ismerteti, hogyan tervezze meg Azure Time Series Insights Gen1-környezetét a várható bejövő forgalom és az adatmegőrzési követelmények alapján.

## <a name="video"></a>Videó

**Tekintse meg ezt a videót, ha többet szeretne megtudni az adatok megőrzéséről Azure Time Series Insights és a tervezéshez**:<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Ajánlott eljárások

A Azure Time Series Insightsének megkezdéséhez érdemes megismerni, hogy mennyi adattal kell leküldeni a percben, és hogy mennyi ideig kell tárolnia az adatait.  

A Azure Time Series Insights SKU kapacitásával és megtartásával kapcsolatos további információkért olvassa el [Azure Time Series Insights díjszabását](https://azure.microsoft.com/pricing/details/time-series-insights/).

Az Azure Time Series Insights-környezet hosszú távú sikeres megtervezéséhez vegye figyelembe a következő attribútumokat:

- [Tárkapacitás](#storage-capacity)
- [Adatmegőrzés időtartama](#data-retention)
- [Bejövő kapacitás](#ingress-capacity)
- [Az események átalakítása](#shape-your-events)
- [A rendelkezésre álló adathivatkozások biztosítása](#ensure-that-you-have-reference-data)

## <a name="storage-capacity"></a>Tárkapacitás

Alapértelmezés szerint a Azure Time Series Insights a kiépített tárterület mennyisége alapján megőrzi az adatmennyiséget (egységként &#215; az egységenkénti tárterület mennyiségét) és a bejövő forgalmat.

## <a name="data-retention"></a>Adatmegőrzés

Módosíthatja az **adatmegőrzési idő** beállításait a Azure Time Series Insights-környezetben. Akár 400 napig is engedélyezheti a megőrzést.

Azure Time Series Insights két mód van:

- Az egyik mód a legnaprakészebb adatokra optimalizált. Kikényszeríti a szabályzatot, hogy **kiürítse a korábbi** , a példánnyal elérhetővé vált adatvesztést. Alapértelmezés szerint ez a mód be van kapcsolva.
- A másik optimalizálja az adatoknak a beállított megőrzési korlátok alatt maradnak. A **Bejövő forgalom szüneteltetése** megakadályozza, hogy az új adatok ingressed legyenek, amikor a **tárolási korlát túllépte a tárterületet**.

Az adatmegőrzés és a váltás a Azure Portal környezet konfigurációs lapjának két módja között állítható be.

> [!IMPORTANT]
> A Azure Time Series Insights Gen1-környezetben legfeljebb 400 napos adatmegőrzést állíthat be.

### <a name="configure-data-retention"></a>Az adatmegőrzés konfigurálása

1. A [Azure Portal](https://portal.azure.com)válassza ki Time Series Insights-környezetét.

1. A **Time Series Insights környezet** ablaktáblán a **Beállítások** területen válassza a **tárolási konfiguráció** elemet.

1. Az **adatmegőrzési idő (nap)** mezőben adjon meg egy 1 és 400 közötti értéket.

   [![Megőrzés konfigurálása](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

> [!TIP]
> Ha többet szeretne megtudni a megfelelő adatmegőrzési szabályzat megvalósításáról, olvassa el a [megőrzés konfigurálását](./time-series-insights-how-to-configure-retention.md)ismertető témakört.

## <a name="ingress-capacity"></a>Bejövő kapacitás

[!INCLUDE [Azure Time Series Insights Gen1 limits](../../includes/time-series-insights-ga-limits.md)]

### <a name="environment-planning"></a>Környezet tervezése

A Azure Time Series Insights-környezet megtervezésére koncentráló második terület a bejövő kapacitás. A napi bejövő tárterület és az esemény kapacitása percenként mérve, 1 KB-os blokkokban. Az engedélyezett csomagméret maximális mérete 32 KB. Az 32 KB-nál nagyobb adatcsomagok csonkítva vannak.

Egyetlen környezetben növelheti egy S1 vagy S2 SKU kapacitását 10 egységre. S1-környezetből S2-re nem lehet áttelepíteni. S2-környezetből S1-re nem lehet áttelepíteni.

A beáramlási kapacitás esetében először határozza meg, hogy havonta hány bejövő forgalom szükséges. Ezután határozza meg a percenkénti igényeket.

A szabályozás és a késés percenkénti kapacitásban játszik szerepet. Ha van egy olyan csúcsa az adatforgalomban, amely 24 óránál rövidebb ideig tart, Azure Time Series Insights "felzárkózhat" az előző táblázatban felsorolt díjszabási arányban.

Ha például egyetlen S1 SKU-val rendelkezik, az adatforgalom percenként 720 eseményt, az adatforgalom pedig kevesebb, mint egy órán keresztül, 1 440 esemény vagy annál kevesebb értékre van kiterjedően, a környezetében nincs észrevehető késés. Ha azonban több mint egy óráig meghaladja az 1 440 eseményt, akkor valószínű, hogy a környezetben megjelenített és lekérdezésre rendelkezésre álló adatmennyiség késést tapasztal.

Előfordulhat, hogy nem tudja előre, hogy mennyi adattal kell leküldeni. Ebben az esetben az [azure IoT hub](../iot-hub/monitor-iot-hub.md) és az [Azure Event Hubs](/archive/blogs/cloud_solution_architect/using-the-azure-rest-apis-to-retrieve-event-hub-metrics) adattelemetria az Azure Portal-előfizetésében találhatja meg. A telemetria segítségével eldöntheti, hogyan építheti ki a környezetet. A megfelelő eseményforrás megjelenítéséhez használja a Azure Portal **metrika** ablaktábláját a telemetria megtekintéséhez. Ha megérti az eseményforrás mérőszámait, hatékonyabban megtervezheti és kiépítheti Azure Time Series Insights-környezetét.

### <a name="calculate-ingress-requirements"></a>Bejövő forgalomra vonatkozó követelmények kiszámítása

A bejövő forgalomra vonatkozó követelmények kiszámítása:

- Ellenőrizze, hogy a bejövő kapacitás meghaladja-e az átlagos percenkénti sebességet, valamint azt, hogy a környezet elég nagy-e, hogy a várható beáramlási arányt legalább egy óránál kevesebb kapacitással kezelje.

- Ha a bejövő tüskék 1 óránál hosszabb ideig tartanak, akkor az átlagnál használja a csúcs arányát. Hozzon létre egy olyan környezetet, amely képes kezelni a csúcsok arányát.

### <a name="mitigate-throttling-and-latency"></a>A szabályozás és a késés csökkentése

A szabályozás és a késés elkerülésével kapcsolatos további információkért olvassa el a [késés és a szabályozás mérséklését](time-series-insights-environment-mitigate-latency.md)ismertető témakört.

## <a name="shape-your-events"></a>Az események formázása

Fontos, hogy az események Azure Time Series Insights való küldésének módja támogassa a kiépített környezet méretét. (Fordítva a környezet méretét leképezheti, hogy hány esemény Azure Time Series Insights olvasás és az egyes események mérete.) Azt is fontos tudni, hogy milyen attribútumokat érdemes használni a szeleteléshez és a szűréshez az adatai lekérdezése során.

> [!TIP]
> Tekintse át a JSON-kialakítási dokumentációt az [események küldése](time-series-insights-send-events.md)című dokumentumban.

## <a name="ensure-that-you-have-reference-data"></a>Ellenőrizze, hogy vannak-e referenciák

A *hivatkozási adatkészlet* olyan elemek gyűjteménye, amelyek kibővítik az esemény forrásának eseményeit. A Azure Time Series Insights beáramlási motor a hivatkozási adatkészlet megfelelő adatsorával összekapcsolja az adott esemény forrását. A kibővített esemény ezután elérhető a lekérdezéshez. Az illesztés a hivatkozási adatkészletben definiált **elsődleges kulcs** oszlopain alapul.

> [!NOTE]
> A hivatkozási adathivatkozások nincsenek visszamenőlegesen csatlakoztatva. A rendszer csak a jelenlegi és a jövőbeli bejövő adatokat egyezteti és csatlakoztatja a hivatkozási adatkészlethez a konfigurálás és a feltöltés után. Ha nagy mennyiségű korábbi adatmennyiséget szeretne elküldeni a Azure Time Series Insightsba, és nem szeretné először feltölteni vagy létrehozni referenciákat a Azure Time Series Insightsban, előfordulhat, hogy meg kell ismételni a munkát (Tipp: nem szórakoztató).  

Ha szeretne többet megtudni arról, hogyan hozhatók létre, tölthetők fel és kezelhetők a Azure Time Series Insightsban található hivatkozási adatok, olvassa el a [hivatkozási adatkészlet dokumentációját](time-series-insights-add-reference-data-set.md).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Következő lépések

- Első lépésként hozzon létre [egy új Azure Time Series Insights környezetet a Azure Portal](time-series-insights-get-started.md).

- Megtudhatja, hogyan [adhat hozzá egy Event Hubs eseményforrás](./how-to-ingest-data-event-hub.md) Azure Time Series Insightshoz.

- További információ az [IoT hub eseményforrás konfigurálásáról](./how-to-ingest-data-iot-hub.md).