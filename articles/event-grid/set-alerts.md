---
title: Riasztások beállítása Azure Event Grid mérőszámokhoz és tevékenység-naplózási műveletekhez
description: Ez a cikk azt ismerteti, hogyan hozhatók létre riasztások Azure Event Grid mérőszámokra és tevékenység-naplózási műveletekre.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 48cb402e31435cb3e9390e8aeb461fcc5f90702f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100572022"
---
# <a name="set-alerts-on-azure-event-grid-metrics-and-activity-logs"></a>Riasztások beállítása Azure Event Grid mérőszámokra és tevékenységi naplókra
Ez a cikk azt ismerteti, hogyan hozhatók létre riasztások Azure Event Grid mérőszámokra és tevékenység-naplózási műveletekre. Riasztásokat hozhat létre a közzétételi és kézbesítési mérőszámokon Azure Event Grid erőforrásokhoz (témakörökhöz és tartományokhoz) is. Rendszertémakörök esetén [hozzon létre riasztásokat a **metrikák** lapon](#create-alerts-using-the-metrics-page).

## <a name="create-alerts-on-dead-lettered-events"></a>Riasztásokat hozhat létre a kézbesítetlen eseményeken
A következő eljárás azt mutatja be, hogyan hozhat létre riasztást a **Kézbesítetlen események** metrikájában egy egyéni témakörhöz. Ebben a példában egy e-mailt küldünk az Azure erőforráscsoport-tulajdonosának, amikor egy témakörhöz tartozó kézbesítetlen események száma 10 fölé esik. 

1. A témakör **Event Grid témakör** lapján válassza a bal oldali menüben a **riasztások** elemet, majd válassza az **+ új riasztási szabály** lehetőséget. 

    :::image type="content" source="./media/monitor-event-delivery/new-alert-button.png" alt-text="Riasztások lap – új riasztási szabály gomb":::
2. A **riasztási szabály létrehozása** lapon ellenőrizze, hogy a témakör ki van-e választva az erőforráshoz. Ezután kattintson a **feltétel kiválasztása** elemre. 

    :::image type="content" source="./media/monitor-event-delivery/alert-select-condition.png" alt-text="Riasztások lap – feltétel kiválasztása":::    
3. A **jel logikai beállítása** lapon kövesse az alábbi lépéseket:
    1. Válasszon ki egy mérőszámot vagy egy tevékenységi naplóbejegyzés-bejegyzést. Ebben a példában a **Kézbesítetlen karakteres események** van kiválasztva. 

        :::image type="content" source="./media/monitor-event-delivery/select-dead-lettered-events.png" alt-text="Elhalt levelekből származó események kiválasztása":::        
    2. Válasszon dimenziókat (nem kötelező). 
        
        :::image type="content" source="./media/monitor-event-delivery/configure-signal-logic.png" alt-text="Jellogika konfigurálása":::        

        > [!NOTE]
        > A **+** **EventSubscriptionName** gomb kiválasztásával megadhatja az események szűrésére szolgáló esemény-előfizetés nevét. 
    3. Görgessen le. A **riasztási logika** szakaszban válasszon ki egy **operátort**, egy **összesítési típust**, és adjon meg egy **küszöbértéket**, és válassza a **kész** lehetőséget. Ebben a példában a riasztás akkor aktiválódik, ha a teljes kézbesítetlen üzenetek száma nagyobb, mint 10. 
    
        :::image type="content" source="./media/monitor-event-delivery/alert-logic.png" alt-text="Riasztási logika":::                
4. Vissza a **riasztási szabály létrehozása** lapon kattintson a **műveleti csoport kiválasztása** elemre.

    :::image type="content" source="./media/monitor-event-delivery/select-action-group-button.png" alt-text="Műveleti csoport kiválasztása gomb":::
5. Új műveleti csoport létrehozásához válassza a **műveleti csoport létrehozása** lehetőséget az eszköztáron. Választhat egy meglévő műveleti csoportot is.        
6. A **műveleti csoport hozzáadása** oldalon hajtsa végre az alábbi lépéseket:
    1. Adja meg **a műveleti csoport nevét**.
    1. Adja meg a műveleti csoport **rövid nevét** .
    1. Válasszon ki egy **Azure-előfizetést** , amelyben létre kívánja hozni a műveleti csoportot.
    1. Válassza ki azt az **Azure-erőforráscsoportot** , amelyben létre kívánja hozni a műveleti csoportot.
    1. Adja meg **a művelet nevét**. 
    1. Válassza ki a **művelet típusát**. Ebben a példában az **e-mail-Azure Resource Manager szerepkör** van kiválasztva, konkrétan a **tulajdonosi** szerepkör. 
    1. A lap bezárásához kattintson **az OK gombra** . 
    
        :::image type="content" source="./media/monitor-event-delivery/add-action-group-page.png" alt-text="Műveleti csoport hozzáadása lap":::                   
7. Vissza a **riasztási szabály létrehozása** lapon adja meg a riasztási szabály nevét, majd válassza a **riasztási szabály létrehozása** lehetőséget.

    :::image type="content" source="./media/monitor-event-delivery/alert-rule-name.png" alt-text="Riasztási szabály neve":::  
8. A témakör **riasztások** lapján megjelenik egy hivatkozás a riasztási szabályok kezelésére, ha még nincsenek riasztások. Ha vannak riasztások, válassza a **kezelő riasztási szabályok** lehetőséget az eszköztáron.  

    :::image type="content" source="./media/monitor-event-delivery/manage-alert-rules.png" alt-text="Riasztások kezelése":::

## <a name="create-alerts-on-other-metrics-or-activity-log-operations"></a>Riasztások létrehozása más mérőszámokkal vagy tevékenység-naplózási műveletekkel
Az előző szakaszban megmutatjuk, hogyan hozhat létre riasztásokat a kézbesítetlen eseményeken. A riasztások más mérőszámokra vagy tevékenység-naplózási műveletekre való létrehozásának lépései hasonlóak. 

Ha például egy kézbesítési hiba eseményéről szeretne riasztást létrehozni, válassza ki a **sikertelen kézbesítések eseményei** lehetőséget a **jel logikai beállítása** lapon. 

:::image type="content" source="./media/set-alerts/delivery-failed-events.png" alt-text="Sikertelen kézbesítési események kiválasztása":::


## <a name="create-alerts-using-the-metrics-page"></a>Riasztások létrehozása a metrikák lapról
Riasztásokat a **metrikák** lapon is létrehozhat. A lépések hasonlóak. A rendszertémakörökhöz csak a **metrikák** lapot használhatja riasztások létrehozásához, mert a **riasztások** lap nem érhető el. 

:::image type="content" source="./media/monitor-event-delivery/metric-page-create-alert-button.png" alt-text="Metrikák lap – riasztás létrehozása gomb":::   
    

> [!NOTE]
> Ez a cikk nem tárgyalja a riasztások létrehozásához használható különböző lépéseket és kombinációkat. A riasztások áttekintését lásd: [riasztások áttekintése](../azure-monitor/alerts/alerts-metric.md).

## <a name="next-steps"></a>Következő lépések

* További információ az események kézbesítéséről és újrapróbálkozásáról, [Event Grid az üzenetek kézbesítéséről, és próbálkozzon újra](delivery-and-retry.md).
* Az Event Grid ismertetése: [Az Event Grid bemutatása](overview.md).
* Az Event Grid használatának gyors megkezdéséhez tekintse meg [az egyéni események létrehozása és irányítása Azure Event Grid](custom-event-quickstart.md)használatával című témakört.
