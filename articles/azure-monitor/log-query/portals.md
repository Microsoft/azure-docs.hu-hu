---
title: Az Azure Monitor log-adatok megtekintésére és elemzésére |} A Microsoft Docs
description: Ez a cikk ismerteti a Log Analytics szolgáltatást az Azure Portalon hozhat létre, és az Azure Monitor log-lekérdezések szerkesztése.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: bwren
ms.openlocfilehash: 0e5b9b43e528b37fd994f9131f145abadb33c53b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61425936"
---
# <a name="viewing-and-analyzing-log-data-in-azure-monitor"></a>Az Azure Monitor log-adatok megtekintésére és elemzésére
Log Analytics a Teljesítménynapló-adatok kezelése és a lekérdezések létrehozása az Azure monitorban elsődleges élményét. Nyissa meg a Log Analytics **naplók** a a **Azure Monitor** menü. Ismerkedjen meg ezen a portálon, és vizsgálja meg a szolgáltatásokat [Log Analytics első lépései az Azure Portalon](get-started-portal.md).

A log Analytics naplóbeli lekérdezések való munkához a következő szolgáltatásokat biztosítja.

* Több lap – hozzon létre külön lapokra több lekérdezés használata.
* Látványos vizualizációkkal – különböző értékeléséről vagy beállítások.
* Továbbfejlesztett Intellisense és nyelvi automatikus kiegészítését.
* Szintaxiselemek kiemelése – az az olvashatóságot a lekérdezések. 
* Lekérdezéskezelő – mentett hozzáférés lekérdezése, és működik.
* Séma nézet – tekintse át a lekérdezések írásának módját segítség az adatok struktúráját.
* Megosztás – lekérdezések vagy PIN-kód-lekérdezést minden olyan megosztott Azure-irányítópultra mutató hivatkozások létrehozása.
* Az intelligens elemzés – azonosítja a diagramok és a egy gyors elemzés a OK adatforgalmi csúcsokhoz.
* Oszlop kiválasztása – rendezheti, és a lekérdezési eredményekben oszlopok csoportban.

> [!NOTE]
> A log Analytics a bővített analitika portál, amely egy külső eszköz, az Azure Portalon kívül ugyanazokkal a funkciókkal rendelkezik. A bővített analitika portál továbbra is elérhető, de hivatkozások és a rá mutató hivatkozásokat az Azure Portalon váltotta fel az új lap.

![Log Analytics](media/portals/log-analytics.png)

## <a name="resource-logs"></a>Erőforrás-naplók
A log Analytics különféle Azure-erőforrások például a virtuális gépek integrálható. Ez azt jelenti, hogy meg tudja nyitni a Log Analytics közvetlenül az erőforrás figyelési menü átváltás az Azure Monitor és az erőforrás-környezet elvesztése nélkül. **Naplók** még nincs engedélyezve az összes Azure-erőforrások, de elindul, a különböző erőforrások menü a portálon megjelenő típusok esetében.

A Log Analytics egy adott erőforráshoz megnyitásakor automatikusan hatóköre csak erőforrás rekordok naplózására.   Ha szeretné, hogy más rekordokat tartalmaz, majd kell az Azure figyelő menü megnyitásához.

A következő beállítások még nem érhető el a Log Analytics-erőforrás nézeten keresztül:

- Mentés
- riasztás beállítása
- Lekérdezéskezelő
- Váltás másik munkaterületet és az erőforrások (jelenleg nem tervezett)


## <a name="firewall-requirements"></a>Tűzfalra vonatkozó követelmények
A böngészőben a következő címek eléréséhez a Log Analytics hozzáférésre van szüksége.  Ha a böngésző tűzfalon keresztül fér hozzá az Azure Portalon, engedélyeznie kell ezeket a címeket a hozzáférést.

| URI | IP | Portok |
|:---|:---|:---|
| portal.loganalytics.io | Dinamikus | 80,443 |
| api.loganalytics.io    | Dinamikus | 80,443 |
| docs.loganalytics.io   | Dinamikus | 80,443 |


## <a name="next-steps"></a>További lépések

- Végig egy [az oktatóanyagban a Log Analytics használatával](../../azure-monitor/log-query/get-started-portal.md).
- Végig egy [oktatóanyag naplóbeli keresés használatával](../../azure-monitor/learn/tutorial-viewdata.md).

