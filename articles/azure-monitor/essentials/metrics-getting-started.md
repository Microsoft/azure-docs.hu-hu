---
title: Ismerkedés az Azure mérőszámok Explorerrel
description: Ismerje meg, hogyan hozhatja létre első metrikai diagramját az Azure Metrics Explorerrel.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: d914c9b5f96cf502247d40a913ce8ad4651a4a14
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100615098"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>Bevezetés az Azure Metrikaböngésző használatába

## <a name="where-do-i-start"></a>Hol kezdjem el
Azure Monitor a metrikák Explorer a Microsoft Azure Portal egy olyan összetevője, amely lehetővé teszi a diagramok ábrázolását, a trendek vizuális korrelációját és a mérőszámok értékeinek kivizsgálása. Az erőforrások állapotának és kihasználtságának vizsgálatához használja a metrikák Explorert. Kezdje a következő sorrendben:

1. [Válasszon ki egy erőforrást és egy mérőszámot](#create-your-first-metric-chart) , és megtekint egy alapszintű diagramot. Ezután [válasszon ki egy időtartományt](#select-a-time-range) , amely releváns a vizsgálathoz.

1. Próbálkozzon [a dimenzió-szűrők alkalmazásával és a felosztással](#apply-dimension-filters-and-splitting). A szűrők és a felosztás lehetővé teszi, hogy elemezze, hogy a metrika mely szegmensei járulnak hozzá az általános metrikai értékhez, és azonosítsa a lehetséges kiugró adatokat.

1. A [Speciális beállítások](#advanced-chart-settings) használatával testreszabhatja a diagramot az irányítópultokra való rögzítés előtt. [Riasztások konfigurálása](../alerts/alerts-metric-overview.md) az értesítések fogadásához, ha a metrika értéke meghaladja a küszöbértéket, vagy csökken.

## <a name="create-your-first-metric-chart"></a>Az első metrikai diagram létrehozása

Metrikus diagram létrehozásához az erőforrás, Erőforráscsoport, előfizetés vagy Azure Monitor nézetben nyissa meg a **metrikák** lapot, és kövesse az alábbi lépéseket:

1. Kattintson a "hatókör kiválasztása" gombra az erőforrás hatókör-választójának megnyitásához. Ezzel kiválaszthatja azokat az erőforrásokat, amelyekhez mérőszámokat kíván megjeleníteni. Ha az erőforrás menüjéből megnyitotta a metrikák Explorert, az erőforrást már fel kell tölteni. Ha szeretné megtudni, hogyan tekintheti meg a mérőszámokat több erőforrás között, [olvassa el ezt a cikket](../platform/metrics-dynamic-scope.md).
    > ![Erőforrás kiválasztása](./media/metrics-getting-started/scope-picker.png)

2. Egyes erőforrások esetében ki kell választania egy névteret. A névtér csak a mérőszámok rendszerezését teszi lehetővé, így könnyen megtalálhatja őket. A Storage-fiókok például különálló névtereket biztosítanak a fájlok, táblák, blobok és várólisták metrikáinak tárolásához. Számos erőforrástípus csak egy névteret tartalmaz.

3. Válasszon ki egy mérőszámot az elérhető metrikák listájából.

    > ![Metrika kiválasztása](./media/metrics-getting-started/metrics-dropdown.png)

4. Igény szerint [módosíthatja a metrikák összesítését](../essentials/metrics-charts.md#aggregation). Előfordulhat például, hogy a diagramot a metrika minimális, maximális vagy átlagos értékének megjelenítésére szeretné megjeleníteni.

> [!TIP]
> Használja a **metrika hozzáadása** gombot, és ismételje meg ezeket a lépéseket, ha több, ugyanabban a diagramban ábrázolt mérőszámot szeretne látni. Ha egy nézetben több diagramot szeretne megjeleníteni, kattintson a felül található **diagram hozzáadása** gombra.

## <a name="select-a-time-range"></a>Válasszon időtartományt

> [!WARNING]
> [Az Azure legtöbb mérőszáma 93 napig tárolódik](../essentials/data-platform-metrics.md#retention-of-metrics). Egyetlen diagramon azonban legfeljebb 30 napos adatot lehet lekérdezni. Ez a korlátozás a [naplóalapú metrikákra](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics) nem vonatkozik.

Alapértelmezés szerint a diagram a legutóbbi 24 órányi metrikai adatokat jeleníti meg. Használja az **időválasztó** panelt a diagram időtartományának, nagyításának vagy kicsinyítésének módosításához. 

![Időtartomány-panel módosítása](./media/metrics-getting-started/time.png)

> [!TIP]
> Az **idő ecsettel** vizsgálja meg a diagram egy érdekes területét (Spike vagy DIP). Vigye az egérmutatót a terület elejére, kattintson és tartsa nyomva a bal egérgombot, húzza a terület másik oldalára, majd engedje el a gombot. A diagram az adott időtartományon nagyítja fel. 

## <a name="apply-dimension-filters-and-splitting"></a>Dimenzió szűrők alkalmazása és felosztás

A [szűrés](../essentials/metrics-charts.md#filters) és a [felosztás](../essentials/metrics-charts.md#apply-splitting) a dimenziókkal rendelkező mérőszámok hatékony diagnosztikai eszközei. Ezek a funkciók azt mutatják be, hogy a különböző metrikai szegmensek ("dimenzió értékek") milyen hatással vannak a metrika összesített értékére, és lehetővé teszik a lehetséges kiugró értékek azonosítását.

- A **szűréssel** kiválaszthatja, hogy mely dimenzió értékeket tartalmazza a diagram. Előfordulhat például, hogy meg szeretné jeleníteni a sikeres kérelmeket a *kiszolgáló válaszideje* mérőszámának ábrázolásakor. A szűrőt a kérelem dimenziójának *sikerességére* kell alkalmaznia. 

- A **felosztás** azt szabályozza, hogy a diagram külön sorokat jelenít-e meg egy dimenzió minden értékéhez, vagy összesíti az értékeket egyetlen sorba. Láthatja például, hogy egy sor az összes kiszolgálópéldány átlagos válaszideje, vagy az egyes kiszolgálókhoz külön sorok találhatók. A különálló sorok megjelenítéséhez a *kiszolgálópéldány* -dimenzióra kell alkalmaznia a felosztást.

Tekintse át a szűrést és a felosztást alkalmazó [diagramok példáit](../essentials/metric-chart-samples.md) . A cikk a diagramok konfigurálásához szükséges lépéseket mutatja be.

## <a name="advanced-chart-settings"></a>Speciális diagram beállításai

Testreszabhatja a diagram stílusát, címét és a speciális diagram beállításainak módosítását. Ha végzett a testreszabással, rögzítse azt egy irányítópulton, hogy mentse a munkáját. A metrikák riasztásait is konfigurálhatja. A [termék dokumentációját](../essentials/metrics-charts.md) követve megismerheti a Azure monitor Metrics Explorer egyéb speciális funkcióit.

## <a name="next-steps"></a>Következő lépések

* [További információ a Metrikaböngésző speciális funkcióiról](../essentials/metrics-charts.md)
* [Több erőforrás megtekintése Metrikaböngésző](../platform/metrics-dynamic-scope.md)
* [A Metrikaböngésző hibaelhárítása](metrics-troubleshoot.md)
* [Az Azure-szolgáltatásokhoz elérhető metrikák listájának megtekintése](../platform/metrics-supported.md)
* [A konfigurált diagramok példáinak megtekintése](../essentials/metric-chart-samples.md)