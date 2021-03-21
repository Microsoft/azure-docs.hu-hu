---
title: Az Azure hatáskörébe tartozó adatelemzések vizsgálata
description: Ez a útmutató ismerteti, hogyan lehet megtekinteni és használni az adatain belüli, a hatáskörébe vonatkozó elemzések vizsgálatát.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: 7807659a30127f39bb79ad99bdb733c12eb1d25d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100548679"
---
# <a name="scan-insights-on-your-data-in-azure-purview"></a>Az Azure hatáskörébe tartozó adatelemzések vizsgálata

Ez a útmutató útmutatást nyújt az Azure-beli és az adataihoz való hozzáféréshez, megtekintéséhez és szűréséhez.

Ebben a útmutatóban megismerheti a következőket:

> [!div class="checklist"]
> * Megtekintheti a hatáskörébe tartozó fiókból származó bepillantást.
> * Megtekintheti a vizsgálatokhoz tartozó madártávlatból.

## <a name="prerequisites"></a>Előfeltételek

A hatáskörébe tartozó ismeretek megismerése előtt győződjön meg arról, hogy végrehajtotta a következő lépéseket:

* Állítsa be az Azure-erőforrásokat, és töltse fel a fiókot adatokkal.
* Állítson be és végezzen el egy vizsgálatot az adatforráson.

További információ: [adatforrások kezelése az Azure hatáskörébe (előzetes verzió)](manage-data-sources.md).

## <a name="use-purview-scan-insights"></a>A hatáskörébe-ellenőrzési ismeretek használata

Az Azure-ban a források regisztrálása és bevizsgálása végezhető el. A vizsgálat állapotát időben tekintheti meg a vizsgálati eredményekben. Az ismeretekből megtudhatja, hogy hány vizsgálat meghiúsult, sikeres volt, vagy egy adott időszakon belül megszakítva.

### <a name="view-scan-insights"></a>Vizsgálattal kapcsolatos megállapítások megtekintése

1. Nyissa meg a Azure Portal **Azure hatáskörébe** tartozó példány képernyőjét, és válassza ki a hatáskörébe tartozó fiókot.

1. Az **Áttekintés** lap első **lépések** szakaszában válassza ki a **hatáskörébe tartozó Studio** -csempét.

   :::image type="content" source="./media/scan-insights/portal-access.png" alt-text="A hatáskörébe tartozó Azure Portal elindítása":::

1. A hatáskörébe tartozó **kezdőlapon** válassza az eredmények **megtekintése** csempét az információhoz való hozzáféréshez  :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: .

   :::image type="content" source="./media/scan-insights/view-insights.png" alt-text="Tekintse meg az Azure Portal":::

1. Az **észlelések** :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: területen válassza a vizsgálat lehetőséget  a hatáskörébe- **ellenőrzési** információk jelentés megjelenítéséhez.

### <a name="view-high-level-kpis-to-show-count-of-scans-by-status-and-deep-dive-into-each-scan"></a>Tekintse meg a magas szintű KPI-ket, hogy megjelenjenek a vizsgálatok száma az állapot alapján, valamint az egyes vizsgálatokban való mély merülések
 
1. A magas szintű KPI-k az adott időszakon belül futtatott összes vizsgálatot megjelenítik. Az időtartam az elmúlt 30 napban van megadva. Azonban az elmúlt hét nap is kiválasztható. Az Időszűrő alapján a KPI-értékek a megfelelő vizsgálatok számát tükrözik.


1. A kiválasztott Időszűrő érték alapján megtekintheti a sikeres, sikertelen és megszakított vizsgálatok eloszlását hetente vagy a gráf napján.

1. A gráf alján további hivatkozásokat láthat a további **részletek** megismeréséhez. A hivatkozás megnyitja a  **vizsgálat állapota** lapot a vizsgálati eredmények között. Itt megtekintheti a vizsgálat nevét, valamint az elmúlt 30 napban a sikeres, sikertelen vagy megszakított időt.

    :::image type="content" source="./media/scan-insights/main-graph.png" alt-text="Vizsgálat állapotának megtekintése az idő függvényében":::

4. Egy adott vizsgálat további megismeréséhez kattintson a **vizsgálat nevére** , amely az Azure hatáskörébe tartozó **források** tapasztalatain belül a vizsgálat előzményeihez fog csatlakozni. A futtatási Előzmények lapon beszerezheti a futtatási azonosítót, amely segítséget nyújt a további hibák kivizsgálásához.

    :::image type="content" source="./media/scan-insights/scan-status.png" alt-text="Vizsgálat részleteinek megtekintése":::

5. Végezetül visszatérhet az elemzések **vizsgálatának állapotának** vizsgálatához a következő, a futtatási Előzmények lap bal felső sarkában látható zsemlemorzsával.

    :::image type="content" source="./media/scan-insights/scan-history.png" alt-text="Vizsgálati előzmények megtekintése"::: 

## <a name="next-steps"></a>Következő lépések

* További információ az Azure- **beli és az** [adatelemzési](./concept-insights.md) információkról

* További információ az Azure-beli hatáskörébe **forrásairól** az [adatforrások kezeléséhez](./manage-data-sources.md)