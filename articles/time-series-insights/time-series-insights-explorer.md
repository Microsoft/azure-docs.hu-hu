---
title: Az adatelemzés az Explorer-Azure Time Series Insights használatával | Microsoft Docs
description: Megtudhatja, hogyan tekintheti meg a IoT adatait a Azure Time Series Insights Explorer használatával.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 639f2ef12d190a56e04a9b48d96ea0a6537d243c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95020112"
---
# <a name="azure-time-series-insights-gen1-explorer"></a>Azure Time Series Insights Gen1 Explorer

> [!CAUTION]
> Ez egy Gen1-cikk.

Ez a cikk a Azure Time Series Insights Gen1 [Explorer webalkalmazás](https://insights.timeseries.azure.com/)funkcióit és beállításait ismerteti. A Azure Time Series Insights Explorer bemutatja a szolgáltatás által biztosított hatékony adatvizualizációs képességeket, és a saját környezetében érhető el.

Az Azure Time Series Insights egy teljes körűen felügyelt elemző, tároló és megjelenítő szolgáltatás, amely megkönnyíti IoT-események milliárdjainak párhuzamos felderítését és elemzését. Globális áttekintést nyújt az adatairól, így gyorsan ellenőrizheti IoT-megoldását, és elkerülheti az üzleti szempontból kritikus fontosságú eszközök költséges leállását. Közel valós időben derítheti fel a rejtett trendeket, észlelheti a rendellenességeket, és elvégezheti a kiváltó okok elemzését.

> [!TIP]
> A demonstrációs környezettel kapcsolatos interaktív bemutatóért olvassa el a [Azure Time Series Insights](time-series-quickstart.md)rövid útmutatót.

## <a name="video"></a>Videó

### <a name="learn-about-querying-data-by-using-the-azure-time-series-insights-explorer-br"></a>Tudnivalók az adatlekérdezésekről a Azure Time Series Insights Explorer használatával. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>Az előző <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">"első lépések a Azure Time Series Insights Azure IoT Solution Accelerator használatával</a> " című videó lejátszása.

## <a name="prerequisites"></a>Előfeltételek

A Azure Time Series Insights Explorer használata előtt a következőket kell tennie:

- Hozzon létre egy Azure Time Series Insights környezetet. További információért olvassa el a [Azure Time Series Insights első lépéseit](./time-series-insights-get-started.md)ismertető témakört.
- [Hozzáférés biztosítása](./concepts-access-policies.md) a fiókhoz a környezetben.
- Adjon hozzá egy [IoT hub](./how-to-ingest-data-iot-hub.md) -vagy [Event hub](./how-to-ingest-data-event-hub.md) -esemény forrását.

## <a name="explore-and-query-data"></a>Adatgyűjtés és adatlekérdezés

Az eseményforrás a Azure Time Series Insights-környezethez való csatlakoztatását perceken belül megtekintheti és lekérdezheti az idősorozat-adatait.

1. A kezdéshez nyissa meg a [Azure Time Series Insights Explorert](https://insights.timeseries.azure.com/) a böngészőben. Az ablak bal oldalán válasszon ki egy környezetet. Minden olyan környezet, amelyhez hozzáférése van, betűrendben jelenik meg.

1. Miután kiválasztott egy környezetet, vagy használja a **from** és **a** konfigurációt a felső részen, vagy válassza ki és húzza át a kívánt TimeSpan. Válassza ki a Nagyítót a jobb felső sarokban, vagy kattintson a jobb gombbal a kiválasztott TimeSpan, és válassza a **Keresés** lehetőséget.

1. A rendelkezésre állást automatikusan is frissítheti percenként, ha az **Automatikus bekapcsolás** gombra kattint. Az **Automatikus bekapcsolás** gomb csak a rendelkezésre állási diagramra vonatkozik, nem a fő vizualizáció tartalmára.

1. Az Azure Cloud ikon a Azure Portalban található környezetére viszi.

   [![Azure Time Series Insights környezet kiválasztása](media/time-series-insights-explorer/tsi-ga-explorer-environments.png)](media/time-series-insights-explorer/tsi-ga-explorer-environments.png#lightbox)

1. Ezután egy diagram jelenik meg, amely megjeleníti a kiválasztott TimeSpan összes eseményének számát. Itt számos vezérlőelem van:

    - **Feltételek szerkesztő panel**: az a terület, ahol lekérdezheti a környezetét. A képernyő bal oldalán található:
      - **Mérték**: Ez a legördülő lista az összes numerikus oszlopot (**Double**) jeleníti meg.
      - **Felosztás**: Ez a legördülő lista kategorikus oszlopokat (**karakterláncokat**) jelenít meg.
      - Engedélyezheti a lépésenkénti interpolációt, megtekintheti a minimális és a maximális értéket, és módosíthatja az y tengelyt a vezérlő paneljén a **mérték** elem mellett. Azt is beállíthatja, hogy a megjelenített adatmennyiség darabszám, átlag vagy összeg-e.
      - Akár öt kifejezést is hozzáadhat ugyanazon az x tengelyen. Válassza a **Hozzáadás** lehetőséget egy új kifejezés hozzáadásához, vagy a **jelen kifejezés klónozása** gomb használatával vegye fel egy meglévő kifejezés másolatát.

        [![Feltételek kiválasztása, szűrése és lekérdezési panel](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png)](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png#lightbox)

      - **Predikátum**: a predikátum használatával gyorsan szűrheti az eseményeket az alábbi táblázatban felsorolt operandusok használatával. Ha a keresést úgy végzi, hogy kiválasztja vagy rákattint a gombra, a rendszer a keresés alapján automatikusan frissíti a predikátumot. A támogatott operandusok típusai a következők:

         |Művelet  |Támogatott típusok  |Jegyzetek  |
         |---------|---------|---------|
         |**<**, **>**, **<=**, **>=**    |  **Double**, **datetime**, **TimeSpan**       |         |
         |**=**, **!=**, **<>**     | **Karakterlánc**, **bool**, **Double**, **datetime**, **TimeSpan**, **Null**        |         |
         |**A**     | **Karakterlánc**, **bool**, **Double**, **datetime**, **TimeSpan**, **Null**        |  Az összes operandusnak azonos típusúnak vagy **Null** konstansnak kell lennie.        |
         |**RENDELKEZIK**     | **Sztring**        |  A jobb oldalon csak konstans karakterlánc-literálok engedélyezettek. Üres karakterlánc és **Null érték** nem engedélyezett.       |

      - **Példa a lekérdezésekre**

         [![Példa Gen1-lekérdezésekre](media/time-series-insights-explorer/tsi-ga-example-queries.png)](media/time-series-insights-explorer/tsi-ga-example-queries.png#lightbox)

1. Az **intervallum mérete** csúszka eszköz használatával nagyíthatja és kicsinyítheti az intervallumokat ugyanazon a TimeSpan. A csúszka precízebb vezérlést biztosít a nagy szeletek közötti mozgáshoz, amely az ezredmásodpercben lévő, kis méretű szeletekre mutat, amelyek lehetővé teszik a részletes, nagy felbontású adatmennyiségek megjelenítését és elemzését. A csúszka alapértelmezett kiindulási pontja úgy van beállítva, hogy a kiválasztásból származó adatok legalkalmasabb nézete legyen a felbontás, a lekérdezés sebessége és a részletesség.

1. Az **időecset** eszköz megkönnyíti az egyik TimeSpan a másikba való átjárását.

1. A **Mentés** ikonra kattintva mentheti az aktuális lekérdezést, és megoszthatja azt a környezet más felhasználóival. Az **Open (Megnyitás** ) ikon kiválasztásával áttekintheti az összes mentett lekérdezést és a más felhasználók megosztott lekérdezéseit olyan környezetekben, amelyekhez hozzáfér.

   [![Lekérdezések](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png)](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png#lightbox)

## <a name="visualize-data"></a>Adatok vizualizációja

1. A **perspektíva nézet** eszközzel akár négy egyedi lekérdezés egyidejű nézetét is használhatja. A **perspektíva nézet** gomb a diagram jobb felső sarkában található.

   [![A perspektíva ablaktáblához hozzáadandó lekérdezések kiválasztása](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png)](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png#lightbox)

1. Megtekintheti a diagramot, hogy vizuálisan vizsgálja meg az adatait, és használja a **diagram** -eszközöket:

    - **Válasszon ki** vagy **kattintson** egy adott TimeSpan vagy egyetlen adatsorozatra.
    - A TimeSpan kiválasztásán belül kinagyíthatja vagy megtekintheti az eseményeket.
    - Egy adatsorozaton belül feloszthatja a sorozatot egy másik oszlop alapján, hozzáadhatja a sorozatot új kifejezésként, csak a kiválasztott adatsorozatot, kizárhatja a kiválasztott adatsorozatot, pingelheti az adatsorozatot, vagy megtekintheti az eseményeket a kiválasztott sorozatból.
    - A diagram bal oldalán található szűrő területen áttekintheti az összes megjelenített adatsorozatot és-sorrendet érték vagy név szerint. Az összes adatsorozatot vagy bármely rögzített vagy nem rögzített adatsorozatot is megtekintheti. Kiválaszthat egy adatsorozatot, és feloszthatja az adatsorozatot egy másik oszlop alapján, hozzáadhatja a sorozatot új kifejezésként, csak a kiválasztott adatsorozatot, kizárhatja a kiválasztott adatsorozatot, rögzítheti a sorozatot, vagy megtekintheti az eseményeket a kiválasztott adatsorozatból.
    - Ha egyszerre több kifejezést tekint meg, a verem, a leválasztás és az adatsorozatok további adatai is megtekinthetők, és ugyanazt az y tengelyt használják az összes kifejezésben. Használja a diagram jobb felső sarkában lévő gombokat.

    [![Diagram eszköz jobb felső sarkában beállítási beállítások](media/time-series-insights-explorer/tsi-ga-example-chart-options.png)](media/time-series-insights-explorer/tsi-ga-example-chart-options.png#lightbox)

1. A **hő** segítségével gyorsan kipróbálhatja az egyedi vagy rendellenes adatsorozatokat egy adott lekérdezésben. Hő csak egy keresési kifejezés látható.

    [Time Series Insights Explorer hő-diagramok](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png)] (média/idő – sorozat – áttekintés – Explorer/tsi-ga-example-heatmap-charting.png#lightbox)

1. Ha az eseményeket a jobb gombbal kattintva tárja fel, az **események** panel elérhetővé válik. Itt áttekintheti az összes nyers eseményt, és az eseményeket JSON-vagy CSV-fájlként is exportálhatja. Azure Time Series Insights az összes nyers adathalmazt tárolja.

    [![Események](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png)](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png#lightbox)

1. Válassza a **stats (statisztika** ) lapot, miután megvizsgálta az eseményeket a minták és az oszlopok statisztikáinak megjelenítéséhez.

    - **Mintázatok**: Ez a funkció proaktív módon felfedi a legtöbb statisztikailag jelentős mintát egy kiválasztott adatterületen. Nem kell több ezer eseményt megvizsgálnia, hogy megértsük, milyen mintázatok szükségesek a legtöbb időt és energiát. A Azure Time Series Insights segítségével közvetlenül a statisztikailag jelentős mintákba ugorhat, így folytathatja az elemzést. Ez a funkció hasznos lehet a múltbeli adatfeldolgozást követő vizsgálatok során is.
    - **Oszlop statisztikái**: az oszlop statisztikái olyan diagramokat és táblákat biztosítanak, amelyek a kijelölt adatsorozat egyes oszlopaiból származó adatokkal bontják le a kiválasztott TimeSpan.

      [![STATISZTIKÁK oszlopának diagramja és beállításai](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png)](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png#lightbox)

Most, hogy megismerte az Azure Time Series Insights Explorer webalkalmazásban elérhető főbb funkciókat, konfigurációs beállításokat és megjelenítési beállításokat.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan [diagnosztizálhatja és megoldhatja a Azure Time Series Insights-környezetben felmerülő problémákat](time-series-insights-diagnose-and-solve-problems.md) .

- Végezze el az interaktív [Azure Time Series Insights](time-series-quickstart.md) gyors bemutatóját.