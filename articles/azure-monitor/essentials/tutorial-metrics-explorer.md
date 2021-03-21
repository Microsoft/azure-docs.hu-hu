---
title: Oktatóanyag – metrikai diagram létrehozása Azure Monitor
description: Megtudhatja, hogyan hozhat létre metrikus diagramokat az Azure mérőszámok Explorerrel.
author: bwren
ms.author: bwren
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 9923bfcbd439ef5e20e44e9c397199fe06f40b43
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102032908"
---
# <a name="tutorial-create-a-metrics-chart-in-azure-monitor"></a>Oktatóanyag: metrikai diagram létrehozása Azure Monitor
Azure Portal a metrikák Explorer a Azure Monitor egyik funkciója, amely lehetővé teszi, hogy diagramokat hozzon létre a metrikák értékeiből, vizuálisan korrelálja a trendeket, és vizsgálja meg a mérőszámok és a dips értékeit. A metrikák Explorerrel megvizsgálhatja az Azure-erőforrások állapotát és kihasználtságát, illetve diagramokat rajzolhat az egyéni metrikák használatával. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Válassza ki azt a metrikát, amelynek diagramját ábrázolni szeretné
> * A metrikai értékek különböző összesítésének végrehajtása
> * A diagram időtartományának és részletességének módosítása

Az alábbi videó az ebben a cikkben ismertetett eljárásnál átfogóbb forgatókönyvet mutat be. Ha még nem ismeri a metrikákat, javasoljuk, hogy először olvassa el ezt a cikket, majd tekintse meg a videót a további részletek megtekintéséhez. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4qO59]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez szüksége lesz egy Azure-erőforrásra a figyeléshez. Használhatja az Azure-előfizetésében lévő összes olyan erőforrást, amely támogatja a metrikákat. Annak megállapításához, hogy egy erőforrás támogatja-e a metrikákat, lépjen a Azure Portal menüjére, és ellenőrizze, hogy van-e **metrikai** lehetőség a menü **figyelés** szakaszában.


## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Jelentkezzen be a Azure Portalba a következő címen: [https://portal.azure.com](https://portal.azure.com) .

## <a name="open-metrics-explorer-and-select-a-scope"></a>A metrikák Explorer megnyitása és hatókör kiválasztása
A metrikák Explorert a Azure Monitor menüjéből vagy a Azure Portal erőforrás menüjéből nyithatja meg. Az összes erőforrás metrikái elérhetők, függetlenül attól, hogy melyik beállítást használja. 

1. Válassza a **metrikák** lehetőséget a **Azure monitor** menüből vagy az erőforrás menüjének **figyelés** szakaszában.

1. Válassza ki a **hatókört**, amely az az erőforrás, amelynek a metrikáit meg szeretné jeleníteni. A hatókör már fel van töltve, ha az erőforrás menüjéből megnyitotta a metrikák Explorert. Ha többet szeretne megtudni az erőforrás-hatókör választójának különböző képességeiről, tekintse meg [ezt a cikket](../essentials/metrics-charts.md#resource-scope-picker).

    ![Hatókör kiválasztása](media/tutorial-metrics-explorer/scope-picker.png)

2. Válassza ki a **névteret** , ha a hatókör egynél több van. A névtér csak a mérőszámok rendszerezését teszi lehetővé, így könnyen megtalálhatja őket. A Storage-fiókok például különálló névtereket biztosítanak a fájlok, táblák, blobok és várólisták metrikáinak tárolásához. Számos erőforrástípus csak egy névteret tartalmaz.

3. Válasszon ki egy mérőszámot a kiválasztott hatókörhöz és névtérhez elérhető metrikák listájából.

    ![Metrika kiválasztása](media/tutorial-metrics-explorer/metric-picker.png)

4. Szükség esetén módosíthatja a metrikák **összesítését**. Ez határozza meg, hogy a metrikai értékek hogyan legyenek összesítve a gráf időbeli részletessége során. Ha például az időrészletesség 15 percre van beállítva, és az Összesítés értéke Sum, akkor a gráf minden pontja az összes összegyűjtött érték összege lesz az egyes 15 percenkénti szegmensekben.

    ![A képernyőképen egy, a contosoretailweb-ra vonatkozó összegű bejövő forgalomra vonatkozó diagram látható.](media/tutorial-metrics-explorer/chart.png)

5. Használja a **metrika hozzáadása** gombot, és ismételje meg ezeket a lépéseket, ha több, ugyanabban a diagramban ábrázolt mérőszámot szeretne látni. Egyetlen nézetben több diagram esetében válassza az **új diagram** gombot.

## <a name="select-a-time-range-and-granularity"></a>Válasszon időtartományt és részletességet

Alapértelmezés szerint a diagram a legutóbbi 24 órányi metrikai adatokat jeleníti meg. A Time Picker használatával módosíthatja a diagram **időtartományát** , illetve az egyes adatpontok időtartományát meghatározó **időrészletességet** . A diagram a megadott összesítés használatával számítja ki az összes mintavételi értéket a megadott időrészletességgel.

![Időtartomány-panel módosítása](media/tutorial-metrics-explorer/time-picker.png)


A diagram egy érdekes területének (például egy tüske vagy egy dip) vizsgálatához használja a **Time ecsetet** . Vigye az egérmutatót a terület elejére, kattintson és tartsa nyomva a bal egérgombot, húzza a terület másik oldalára, és szabadítsa fel a gombot. A diagram az adott időtartományon nagyítja fel. 

![Idő ecset](media/tutorial-metrics-explorer/time-brush.png)

## <a name="apply-dimension-filters-and-splitting"></a>Dimenzió szűrők alkalmazása és felosztás
Tekintse át a következő, speciális funkciókra mutató hivatkozásokat, amelyek lehetővé teszik a metrikák további elemzését, valamint az adatok lehetséges kiugró állapotának azonosítását.

- A [szűréssel](../essentials/metrics-charts.md#filters) kiválaszthatja, hogy mely dimenzió értékeket tartalmazza a diagram. Előfordulhat például, hogy csak a sikeres kérelmeket szeretné megjeleníteni a *kiszolgálói válaszidő* mérőszámának ábrázolásakor. 

- A [felosztás](../essentials/metrics-charts.md#apply-splitting) azt szabályozza, hogy a diagram külön sorokat jelenít-e meg egy dimenzió minden értékéhez, vagy összesíti az értékeket egyetlen sorba. Előfordulhat például, hogy egy sort szeretne látni az összes kiszolgálói példány átlagos válaszideje esetén, vagy ha külön sort szeretne használni az egyes kiszolgálókon. 

Tekintse át a szűrést és a felosztást alkalmazó [diagramok példáit](../essentials/metric-chart-samples.md) .

## <a name="advanced-chart-settings"></a>Speciális diagram beállításai

Testreszabhatja a diagram stílusát, címét és a speciális diagram beállításainak módosítását. Ha végzett a testreszabással, rögzítse azt egy irányítópulton, hogy mentse a munkáját. A metrikák riasztásait is konfigurálhatja. Tekintse meg az [Azure Metrikaböngésző speciális funkcióit](../essentials/metrics-charts.md#locking the-range-of-the-y-axis) , amelyekkel megismerheti a Azure monitor metrikák Explorer egyéb speciális funkcióit.


## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte, hogyan használhatók a metrikák a Azure Monitorban, megtudhatja, hogyan küldhet mérőszámokat az proaktív riasztások küldéséhez.

> [!div class="nextstepaction"]
> [Metrikariasztások létrehozása, megtekintése és kezelése az Azure Monitorral](../essentials/metrics-charts.md#alert-rules)

