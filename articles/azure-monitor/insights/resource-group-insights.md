---
title: Erőforráscsoport-bepillantást Azure Monitor | Microsoft Docs
description: Az erőforráscsoport szintjén megismerheti az elosztott alkalmazások és szolgáltatások állapotát és teljesítményét Azure Monitor
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.openlocfilehash: bfe197befc781548dc41ff389f2d9558af7a8aae
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567402"
---
# <a name="monitor-resource-groups-with-azure-monitor-preview"></a>Erőforráscsoportok figyelése Azure Monitorvel (előzetes verzió)

A modern alkalmazások gyakran bonyolultak, és számos különálló részből állnak, amelyek egy szolgáltatás nyújtására szolgálnak. Az összetettség felismerése Azure Monitor az erőforráscsoportok figyelését teszi lehetővé. Így egyszerűen osztályozhatja és diagnosztizálhatja az egyes erőforrásaival kapcsolatos problémákat, miközben az erőforráscsoport állapotának és teljesítményének, valamint az &mdash; alkalmazás egészének a kontextusát kínálja &mdash; .

## <a name="access-insights-for-resource-groups"></a>Erőforrás-csoportok elérésének elérése

1. Válassza az **erőforráscsoportok**  lehetőséget a bal oldali navigációs sávon.
2. Válasszon ki egy, a felderíteni kívánt erőforráscsoportot. (Ha az előfizetés nagy számú erőforráscsoport-szűrést használ, időnként hasznos lehet.)
3. Egy erőforráscsoport információinak eléréséhez kattintson az összes erőforráscsoport bal oldali menüjének **elemzése elemére** .

![Az erőforráscsoport-információk áttekintési oldalának képernyőképe](./media/resource-group-insights/0001-overview.png)

## <a name="resources-with-active-alerts-and-health-issues"></a>Aktív riasztásokkal és egészségügyi problémákkal rendelkező erőforrások

Az Áttekintés oldalon látható, hogy hány riasztást indítottak el, és amelyek még aktívak, valamint az egyes erőforrások aktuális Azure Resource Health. Ezen információk együttes használatával gyorsan megtalálhatja a problémákat okozó erőforrásokat. A riasztások segítséget nyújtanak a kódban felmerülő problémák észlelésében és az infrastruktúra konfigurálásában. Azure Resource Health felületek az Azure platformmal kapcsolatos problémát jelentenek, amely nem kifejezetten az egyes alkalmazásokra vonatkozik.

![Azure Resource Health panel képernyőképe](./media/resource-group-insights/0002-overview.png)

### <a name="azure-resource-health"></a>Azure Resource Health

Azure Resource Health megjelenítéséhez jelölje be a táblázat feletti **Azure Resource Health megjelenítése** jelölőnégyzetet. Ez az oszlop alapértelmezés szerint el van rejtve az oldal betöltésének gyors megkönnyítése érdekében.

![Képernyőfelvétel a Resource Health gráf hozzáadásával](./media/resource-group-insights/0003-overview.png)

Alapértelmezés szerint az erőforrások az alkalmazási réteg és az erőforrástípus szerint vannak csoportosítva. Az **alkalmazási réteg** az erőforrástípusok egyszerű kategorizálása, amely csak az erőforráscsoport-információk Áttekintés oldalának kontextusában létezik. Az alkalmazás kódjához, a számítási infrastruktúrához, a hálózatkezeléshez, a tároláshoz és az adatbázisokhoz kapcsolódó erőforrástípusok tartoznak. A felügyeleti eszközök beszerezhetik saját alkalmazási rétegeiket, és minden más erőforrás kategorizálva van a **másik** alkalmazás rétegében. Ez a csoportosítás segíthet megtekinteni, hogy az alkalmazás mely alrendszerei egészségesek és nem kifogástalanok.

## <a name="diagnose-issues-in-your-resource-group"></a>Az erőforráscsoport problémáinak diagnosztizálása

Az erőforráscsoport-elemzések oldal számos más eszközt is tartalmaz, amelyek a problémák diagnosztizálásához nyújtanak segítséget

   | Eszköz | Leírás |
   | ---------------- |:-----|
   | [**Riasztások**](../alerts/alerts-overview.md)      |  A riasztások megtekintése, létrehozása és kezelése. |
   | [**Mérőszámok**](../data-platform.md) | Jelenítse meg és vizsgálja meg a metrika-alapú adatokat.    |
   | [**Tevékenységnaplók**](../essentials/platform-logs-overview.md) | Az Azure-ban történt előfizetési szintű események.  |
   | [**Alkalmazás-hozzárendelés**](../app/app-map.md) | Navigáljon az elosztott alkalmazás topológiájában a teljesítménnyel kapcsolatos szűk keresztmetszetek vagy a meghibásodási pontok azonosításához. |

## <a name="failures-and-performance"></a>Hibák és teljesítmény

Mi a teendő, ha észrevette, hogy az alkalmazás lassan fut, vagy a felhasználók hibát jelentettek? Itt az idő, hogy az összes erőforrásra rákeresve elkülönítse a problémákat.

A **teljesítmény** -és **meghibásodási** lapok leegyszerűsítik ezt a folyamatot azáltal, hogy számos gyakori erőforrástípus esetében egyesítik a teljesítménnyel és a hibákkal kapcsolatos diagnosztikai nézeteket.

A legtöbb erőforrástípus megnyit egy Azure Monitor munkafüzet-sablonok gyűjteményét. A létrehozott munkafüzetek testreszabhatók, menthetők, megoszthatók a csapatával, és a jövőben újra felhasználhatók a hasonló problémák diagnosztizálására.

### <a name="investigate-failures"></a>Hibák vizsgálata

A hibák lap teszteléséhez válassza a bal oldali menüben a **vizsgálat** alatt a **hibák** lehetőséget.

A bal oldali menüsor a kijelölés után módosul, és új lehetőségeket kínál.

![A hiba áttekintése panel képernyőképe](./media/resource-group-insights/00004-failures.png)

Ha App Service van kiválasztva, Azure Monitor munkafüzet-sablonok gyűjteménye jelenik meg.

![Képernyőfelvétel az Application munkafüzet-katalógusról](./media/resource-group-insights/0005-failure-insights-workbook.png)

A hiba miatti bepillantást nem tartalmazó sablon kiválasztása megnyitja a munkafüzetet.

![A hibajelentési jelentés képernyőképe](./media/resource-group-insights/0006-failure-visual.png)

Bármelyik sort kiválaszthatja. A kijelölés ekkor megjelenik a grafikus Részletek nézetben.

![Képernyőkép a hiba részleteiről](./media/resource-group-insights/0007-failure-details.png)

A munkafüzetek elvonták az egyéni jelentések és vizualizációk egyszerű formátumba való létrehozásának nehéz munkáját. Míg egyes felhasználók csak az előre elkészített paramétereket szeretnék módosítani, a munkafüzetek teljesen testreszabhatók.

A munkafüzet belső működésének megértéséhez válassza a **Szerkesztés** lehetőséget a felső sávon.

![Képernyőkép a további szerkesztési lehetőségekről](./media/resource-group-insights/0008-failure-edit.png)

A munkafüzet különböző elemei közelében számos **szerkesztési** mező jelenik meg. Válassza a művelet tábla alatti **Szerkesztés** mezőt.

![Képernyőfelvétel a mezők szerkesztéséről](./media/resource-group-insights/0009-failure-edit-graph.png)

Ez felfedi a tábla vizualizációját vezető mögöttes napló-lekérdezést.

 ![A napló lekérdezési ablakának képernyőképe](./media/resource-group-insights/0010-failure-edit-query.png)

A lekérdezést közvetlenül is módosíthatja. Használhatja hivatkozásként, és felhasználhatja azt a saját, egyéni paraméteres munkafüzet tervezésekor.

### <a name="investigate-performance"></a>Teljesítmény vizsgálata

A teljesítmény a munkafüzetek saját katalógusát kínálja. App Service az előre elkészített alkalmazás-teljesítmény munkafüzet a következő nézetet kínálja:

 ![A teljesítmény nézet képernyőképe](./media/resource-group-insights/0011-performance.png)

Ebben az esetben, ha a szerkesztés lehetőséget választja, látni fogja, hogy a vizualizációk ezen készlete Azure Monitor mérőszámokkal van-e kikapcsolva.

 ![Képernyőkép a teljesítmény nézetről az Azure-metrikákkal](./media/resource-group-insights/0012-performance-metrics.png)

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="enabling-access-to-alerts"></a>Riasztásokhoz való hozzáférés engedélyezése

Ha meg szeretné tekinteni az erőforráscsoportok Azure Monitor riasztásait, akkor az előfizetéshez tartozó tulajdonosi vagy közreműködői szerepkörrel rendelkező személynek meg kell nyitnia az erőforrás-csoportokhoz tartozó Azure Monitor az előfizetésben található összes erőforráscsoport számára. Ezzel a beállítással bárki olvasási hozzáféréssel láthatja el a riasztásokat az előfizetésben lévő összes erőforráscsoport számára az erőforráscsoportok Azure Monitor. Ha rendelkezik tulajdonosi vagy közreműködői szerepkörrel, frissítse ezt a lapot néhány perc múlva.

Az erőforráscsoportok Azure Monitor a riasztási állapot lekéréséhez Azure Monitor riasztások felügyeleti rendszerére támaszkodik. A riasztások kezelése nincs konfigurálva minden erőforráscsoport és előfizetés számára alapértelmezés szerint, és csak egy tulajdonos vagy közreműködő szerepkörrel rendelkező személy engedélyezhető. A következők bármelyikét engedélyezheti:
* Az erőforrás-csoportokhoz tartozó Azure Monitor megnyitása az előfizetés egyik erőforráscsoport számára.
* Vagy nyissa meg az előfizetést, kattintson az **erőforrás-szolgáltatók**, majd a **regisztráció a riasztásokhoz** elemre.

## <a name="next-steps"></a>Következő lépések

- [Munkafüzetek Azure Monitor](../visualize/workbooks-overview.md)
- [Azure Resource Health](../../service-health/resource-health-overview.md)
- [Azure Monitor-riasztások](../alerts/alerts-overview.md)
