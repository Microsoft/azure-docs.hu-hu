---
title: Azure cache-Azure Monitor a Redis-hez | Microsoft Docs
description: Ez a cikk a Azure Redis Cache funkció Azure Monitor ismerteti, amely a gyorsítótár-tulajdonosokat a teljesítmény-és kihasználtsági problémák gyors megismerésével biztosítja.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 09/10/2020
ms.openlocfilehash: fee454073c50b9542e140576ef0629a39b8f4294
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100573466"
---
# <a name="explore-azure-monitor-for-azure-cache-for-redis"></a>Ismerkedés az Azure cache Azure Monitor Redis

A Redis Azure cache Azure Monitor-hez készült Redis-erőforrások összes Azure-gyorsítótára esetében a következő egységes, interaktív képet nyújt:

- Teljes teljesítmény
- Hibák
- Kapacitás
- Működési állapot

Ez a cikk segít megérteni az új figyelési élmény előnyeit. Azt is bemutatja, hogyan lehet módosítani és alkalmazkodni a felhasználói élményhez, hogy illeszkedjenek a szervezet egyedi igényeihez.

## <a name="introduction"></a>Bevezetés

A felhasználói élmény megkezdése előtt tisztában kell lennie azzal, hogy az Azure cache Azure Monitor for Redis vizuálisan hogyan jeleníti meg az információkat.

A következőket biztosítja:

- Az Azure cache **méretezési perspektívája** a Redis-erőforrások egyetlen helyen az összes előfizetésben. A hatókört csak a kiértékelni kívánt előfizetésekre és erőforrásokra szűkítheti.

- Egy adott Azure cache részletes **elemzése** a Redis-erőforráshoz. Diagnosztizálhatja a problémákat, és megtekintheti a kihasználtság, a hibák, a kapacitás és a műveletek részletes elemzését. Ezen kategóriák bármelyikét kiválasztva részletesen megtekintheti a kapcsolódó információkat.  

- Ennek a élménynek a **testre szabása** Azure monitor munkafüzet-sablonokból épül fel. Az élmény lehetővé teszi a mérőszámok megjelenítését és módosítását, illetve a határértékekhez igazított küszöbértékek beállítását. Mentheti a módosításokat egy egyéni munkafüzetben, majd rögzítheti a munkafüzet-diagramokat az Azure-irányítópultokon.

Ez a funkció nem igényli, hogy bármit engedélyezzen vagy konfiguráljan. A rendszer alapértelmezés szerint gyűjti össze az Azure cache-t a Redis-adatokhoz.

>[!NOTE]
>A szolgáltatáshoz való hozzáférés díjmentes. Csak az Ön által megadott vagy engedélyezett Azure Monitor alapvető funkciókért kell fizetnie, a [Azure monitor díjszabását ismertető](https://azure.microsoft.com/pricing/details/monitor/) oldalon leírtak szerint.

## <a name="view-utilization-and-performance-metrics-for-azure-cache-for-redis"></a>Az Azure cache kihasználtsági és teljesítmény-metrikáinak megtekintése a Redis-hez

Ha szeretné megtekinteni a Storage-fiókok kihasználtságát és teljesítményét az összes előfizetésében, hajtsa végre a következő lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Keresse meg a **figyelőt**, és válassza a **figyelő** lehetőséget.

    ![A "figyelő" szót tartalmazó keresőmező és a szolgáltatások keresési eredménye, amely a "monitor" karaktert mutatja egy sebességmérő szimbólummal](./media/cosmosdb-insights-overview/search-monitor.png)

1. Válassza ki **Az Azure cache-t a Redis**. Ha ez a beállítás nincs jelen, válassza a **további**  >  **Azure cache Redis** lehetőséget.

### <a name="overview"></a>Áttekintés

Az **Áttekintés** során a táblázat a Redis-metrikák interaktív Azure cache-t jeleníti meg. Az eredményeket az alábbi legördülő listából kiválasztott beállítások alapján szűrheti:

- **Előfizetések**: csak az Azure cache-t használó előfizetések jelennek meg a Redis-erőforráshoz.  

- **Azure cache for Redis**: kiválaszthatja az összes, egy részhalmazt vagy egyetlen Azure cache-t a Redis-erőforráshoz.

- **Időtartomány**: alapértelmezés szerint a tábla az utolsó négy órányi információt jeleníti meg a megfelelő beállítások alapján.

A legördülő lista alatt található egy számláló csempe. A csempén a kiválasztott előfizetések Redis erőforrásaihoz tartozó Azure cache teljes száma látható. A munkafüzet oszlopaihoz tartozó feltételes színkódok vagy Heat Maps jelentés tranzakciós metrikái. A legmélyebb szín a legmagasabb értéket jelöli. A világosabb színek alacsonyabb értékeket jelentenek.

A Redis-erőforrások Azure-gyorsítótára melletti legördülő lista kiválasztásával a teljesítménymutatók részletezése az egyes erőforrások szintjén.

![Képernyőkép az áttekintő felületről](./media/redis-cache-insights-overview/overview.png)

Ha a kék színnel jelölt Redis-erőforráshoz az Azure cache elemet választja, akkor a társított fiókhoz tartozó alapértelmezett **áttekintési** táblázat jelenik meg. Ezeket az oszlopokat jeleníti meg:

- **Felhasznált memória**
- **Felhasznált memória százalékos aránya**
- **Kiszolgáló terhelése**
- **Kiszolgáló terhelése – idővonal**
- **CPU**
- **Csatlakoztatott ügyfelek**
- **Gyorsítótár-tévesztések**
- **Hibák (max.)**

### <a name="operations"></a>Üzemeltetés

Amikor kiválasztja a **műveletek** elemet a lap tetején, megnyílik a munkafüzet sablonjának **műveleti** táblázata. Ezeket az oszlopokat jeleníti meg:

- **Műveletek összesen**
- **Műveletek összesen – idővonal**
- **Műveletek száma másodpercenként**
- **Lekérések**
- **Halmazok**

![Képernyőkép az üzemeltetési élményről](./media/redis-cache-insights-overview/operations.png)

### <a name="usage"></a>Használat

Amikor kiválasztja a **használat** lehetőséget az oldal tetején, megnyílik a munkafüzet sablonjának **használati** táblázata. Ezeket az oszlopokat jeleníti meg:

- **Gyorsítótár-olvasás**
- **Gyorsítótár-olvasás – idővonal**
- **Gyorsítótár-írás**
- **Gyorsítótár-találatok**
- **Gyorsítótár-tévesztések**

![Képernyőkép a használati élményről](./media/redis-cache-insights-overview/usage.png)

### <a name="failures"></a>Hibák

Ha a lap tetején a **hibák** lehetőséget választja, megnyílik a munkafüzet sablonjának **hibák** táblázata. Ezeket az oszlopokat jeleníti meg:

- **Összes hiba**
- **Feladatátvétel/hibák**
- **UnresponsiveClient/hibák**
- **RDB/hibák**
- **AOF/hibák**
- **Exportálás/hibák**
- **Dataloss/hibák**
- **Importálás/hibák**

![Képernyőfelvétel a HTTP-kérelem típusa szerinti bontásban előforduló hibákról](./media/redis-cache-insights-overview/failures.png)

### <a name="metric-definitions"></a>Metrika-definíciók

A munkafüzeteket alkotó metrika-definíciók teljes listáját az [elérhető metrikák és jelentéskészítési időközök című cikkben](../../azure-cache-for-redis/cache-how-to-monitor.md#available-metrics-and-reporting-intervals)tekintheti meg.

## <a name="view-from-an-azure-cache-for-redis-resource"></a>Megtekintés Azure-gyorsítótárból Redis-erőforráshoz

Az Azure cache Azure Monitor az Redis közvetlenül egy adott erőforrásból való elérése:

1. A Azure Portal válassza az Azure cache lehetőséget a Redis számára.

2. A listáról válassza ki a Redis-erőforráshoz tartozó egyedi Azure cache-t. A figyelés szakaszban válassza az áttekintések lehetőséget.

    ![A menüpontok képernyőképe a piros mezőben Kiemelt "bepillantások" kifejezéssel](./media/redis-cache-insights-overview/insights.png)

Ezek a nézetek a Azure Monitor szintű munkafüzetből származó Redis-erőforráshoz tartozó Azure cache-erőforrás nevének kiválasztásával is elérhetők.

### <a name="resource-level-overview"></a>Erőforrás szintű áttekintés

A Azure Redis Cache **áttekintő** munkafüzetben számos olyan teljesítménymutató látható, amely hozzáférést biztosít a következőhöz:

- Interaktív teljesítménymutatók, amelyek az Azure cache Redis-teljesítményével kapcsolatos legfontosabb adatokat mutatják be.

- Metrikák és állapotjelző csempék kiemelve a szegmensek teljesítményét, a csatlakoztatott ügyfelek teljes számát és a teljes késést.

![Képernyőkép az áttekintő irányítópultról a CPU teljesítményével, a felhasznált memóriával, a csatlakoztatott ügyfelekkel, a hibákkal, a lejárt kulcsokkal és a kizárt kulcsokkal kapcsolatban](./media/redis-cache-insights-overview/resource-overview.png)

A **teljesítmény** vagy a **műveletek** egyéb lapjainak kiválasztásával megnyílik a megfelelő munkafüzetek.

### <a name="resource-level-performance"></a>Erőforrás-szintű teljesítmény

![Az erőforrás-teljesítményi diagramok képernyőképe](./media/redis-cache-insights-overview/resource-performance.png)

### <a name="resource-level-operations"></a>Erőforrás-szintű műveletek

![A Resource Operations-diagramok képernyőképe](./media/redis-cache-insights-overview/resource-operations.png)

## <a name="pin-export-and-expand"></a>PIN-kód, exportálás és Kibontás

Ha a metrikai szakaszt egy [Azure-irányítópultra](../../azure-portal/azure-portal-dashboards.md)szeretné rögzíteni, válassza a gombostű szimbólumot a szakasz jobb felső részén.

![A gombostű szimbólummal jelölt metrikai szakasz](./media/cosmosdb-insights-overview/pin.png)

Az Excel-formátumba való exportáláshoz kattintson a gombostű szimbólum bal oldalán lévő lefelé mutató nyíl szimbólumra.

![Kiemelt exportálási munkafüzet szimbóluma](./media/cosmosdb-insights-overview/export.png)

Egy munkafüzet összes nézetének kibontásához vagy összecsukásához válassza az Exportálás szimbólumtól balra található kibontási szimbólumot.

![Kiemelt kibontott munkafüzet szimbóluma](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cache-for-redis"></a>Az Azure cache Azure Monitor testreszabása a Redis

Mivel ez a felület Azure monitor munkafüzet-sablonokon alapul, kiválaszthatja a   >  **szerkesztési**  >  **Mentés** testreszabása lehetőséget, hogy a módosított verzió másolatát egy egyéni munkafüzetbe mentse.

![A Kiemelt testreszabást tartalmazó parancssáv](./media/cosmosdb-insights-overview/customize.png)

A munkafüzetek a **saját jelentések** szakaszban vagy a **megosztott jelentések** szakaszban található erőforráscsoporthoz lesznek mentve. **Saját jelentések** csak Ön számára érhető el. A **megosztott jelentések** mindenki számára elérhetők az erőforráscsoporthoz való hozzáféréssel.

Az Egyéni munkafüzet mentése után nyissa meg a munkafüzet-katalógust.

![Egy, a katalógust kiemelő parancssáv](./media/cosmosdb-insights-overview/gallery.png)

## <a name="troubleshooting"></a>Hibaelhárítás

A hibaelhárítással kapcsolatos útmutatásért tekintse meg a dedikált munkafüzet-alapú információkkal [kapcsolatos hibaelhárítási cikket](troubleshoot-workbooks.md).

## <a name="next-steps"></a>Következő lépések

* A [metrikai riasztások](../alerts/alerts-metric.md) és a [szolgáltatás állapotára vonatkozó értesítések](../../service-health/alerts-activity-log-service-notifications-portal.md) konfigurálása a problémák észlelését segítő automatizált riasztások beállításához.

* Megismerheti a munkafüzetek által támogatott forgatókönyveket, a jelentések készítését és testreszabását, valamint az [interaktív jelentések létrehozásával Azure monitor munkafüzetek](../visualize/workbooks-overview.md)használatával történő áttekintésével.
