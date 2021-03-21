---
title: Azure-tevékenység naplójának küldése Log Analytics munkaterületre a Azure Portal használatával
description: A Azure Portal használatával hozzon létre egy Log Analytics-munkaterületet és egy diagnosztikai beállítást, hogy elküldje a tevékenység naplóját Azure Monitor naplókba.
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: fec1f4f3ae13f6c9ed5fdd7ffbcd143e5c5e5f52
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033248"
---
# <a name="send-azure-activity-log-to-log-analytics-workspace-using-azure-portal"></a>Azure-tevékenység naplójának küldése Log Analytics munkaterületre a Azure Portal használatával
A Tevékenységnapló az Azure előfizetési szintű eseményekkel kapcsolatos megállapításokat biztosító platformnaplója. Olyan információkat tartalmaz, mint az erőforrások módosításának vagy a virtuális gépek indításának az időpontja. Megtekintheti a tevékenység naplóját a Azure Portal vagy beolvashatja a bejegyzéseket a PowerShell és a parancssori felület használatával. Ez a rövid útmutató azt ismerteti, hogyan használható a Azure Portal egy Log Analytics-munkaterület létrehozásához és egy diagnosztikai beállításhoz, hogy elküldje a tevékenység naplóját Azure Monitor naplókba, ahol elemezheti a [napló lekérdezések](../logs/log-query-overview.md) használatával, és más funkciók, például a [naplók](../alerts/alerts-log-query.md) és a [munkafüzetek](../visualize/workbooks-overview.md)használatát is lehetővé teheti. 

## <a name="sign-in-to-azure-portal"></a>Bejelentkezés az Azure portálra
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen. 



## <a name="create-a-log-analytics-workspace"></a>Log Analytics-munkaterület létrehozása
A Azure Portal keresse meg, majd válassza ki **log Analytics munkaterületek** lehetőséget. 

![Képernyőfelvétel a Azure Portal a "log Analytics-munkaterületek" kifejezésre, és a szolgáltatások területen Kiemelt Log Analytics-munkaterületekre mutat.](../logs/media/quick-create-workspace/azure-portal-01.png)
  
Kattintson a **Hozzáadás** gombra, majd adja meg az **erőforráscsoport**, a munkaterület **neve** és a **hely** értékét. A munkaterület nevének egyedinek kell lennie az összes Azure-előfizetésen belül.

![Munkaterület létrehozása](media/quick-collect-activity-log/create-workspace.png)

Kattintson a **felülvizsgálat + létrehozás** elemre, és tekintse át a beállításokat, majd **hozza** létre a munkaterületet. Ezzel kiválaszthatja az utólagos elszámolású csomag alapértelmezett díjszabását **, amely nem** eredményez semmilyen változást, amíg el nem éri a megfelelő mennyiségű adatok gyűjtését. A tevékenység naplójának összegyűjtése díjmentes.


## <a name="create-diagnostic-setting"></a>Diagnosztikai beállítások létrehozása
A Azure Portal keresse meg, majd válassza a **figyelő** elemet. 

![Képernyőfelvétel a Azure Portal a "monitor" elemre a keresőmezőbe, és a figyelés kiemelten látható a szolgáltatások területen.](media/quick-collect-activity-log/azure-portal-monitor.png)

Válassza a **műveletnapló** lehetőséget. Az aktuális előfizetéshez tartozó közelmúltbeli eseményeket kell megjelennie. Az előfizetés diagnosztikai beállításainak megtekintéséhez kattintson a **diagnosztikai beállítások** elemre.

![Tevékenységnapló](media/quick-collect-activity-log/activity-log.png)

Új beállítás létrehozásához kattintson a **diagnosztikai beállítás hozzáadása** elemre. 

![Diagnosztikai beállítások létrehozása](media/quick-collect-activity-log/create-diagnostic-setting.png)

Írjon be egy nevet, például *küldje el a tevékenység naplóját a munkaterületre*. Válassza ki a kategóriákat. Válassza a **küldés log Analytics** az egyetlen célhelyként, majd adja meg a létrehozott munkaterületet. Kattintson a **Mentés** gombra a diagnosztikai beállítás létrehozásához, majd a lap bezárásához.

![Új diagnosztikai beállítás](media/quick-collect-activity-log/new-diagnostic-setting.png)

## <a name="generate-log-data"></a>Naplófájlok előállítása
A rendszer csak az Log Analytics munkaterületre küldi el az új tevékenységeket, ezért olyan műveleteket hajt végre az előfizetésben, amelyek naplózásra kerülnek, például egy virtuális gép elindításához vagy leállításához, illetve egy másik erőforrás létrehozásához vagy módosításához. Előfordulhat, hogy néhány percet várnia kell, hogy a rendszer létrehozza a diagnosztikai beállítást, és hogy először a munkaterületre írja be az adatgyűjtést. A késleltetést követően a rendszer a tevékenység naplójába írt összes eseményt néhány másodpercen belül elküldi a munkaterületre.

## <a name="retrieve-data-with-a-log-query"></a>Az adatlekérdezés naplózási lekérdezéssel

A **Azure monitor** menüben válassza a **naplók** lehetőséget. A **példákat tartalmazó lekérdezések** oldalának lezárása. Ha a hatókör nem a létrehozott munkaterületre van beállítva, kattintson a **hatókör kiválasztása** lehetőségre, és keresse meg.

![Log Analytics hatókör](media/quick-collect-activity-log/log-analytics-scope.png)

A lekérdezési ablakban írja be a parancsot, `AzureActivity` majd kattintson a **Futtatás** gombra. Ez egy egyszerű lekérdezés, amely visszaadja a *AzureActivity* tábla összes rekordját, amely a tevékenység naplójából elküldett összes rekordot tartalmazza.

![Egyszerű lekérdezés](media/quick-collect-activity-log/query-01.png)

Bontsa ki az egyik rekordot a részletes tulajdonságainak megtekintéséhez.

![Tulajdonságok kibontása](media/quick-collect-activity-log/expand-properties.png)

Összetettebb lekérdezéseket is kipróbálhat, például `AzureActivity | summarize count() by CategoryValue` a kategória szerint összefoglalt események számát.

![Összetett lekérdezés](media/quick-collect-activity-log/query-02.png)


## <a name="next-steps"></a>Következő lépések
Ebben a rövid útmutatóban úgy konfigurálta a tevékenység naplóját, hogy az Log Analytics munkaterületre legyen küldve. Mostantól más adatokat is konfigurálhat a munkaterületre, ahol elemezheti azt a Azure Monitor található [naplók](../logs/log-query-overview.md) használatával, és olyan funkciókat használhat, mint például a [naplózási riasztások](../alerts/alerts-log-query.md) és a [munkafüzetek](../visualize/workbooks-overview.md). A következő lépésekkel gyűjtsön [erőforrás-naplókat](../essentials/resource-logs.md) az Azure-erőforrásokból, amelyek a tevékenység naplójában lévő adatokat szolgáltatva betekintést nyújtanak az egyes erőforrásokon végrehajtott műveletekre.


> [!div class="nextstepaction"]
> [Erőforrás-naplók összegyűjtése és elemzése Azure Monitor](../essentials/tutorial-resource-logs.md)