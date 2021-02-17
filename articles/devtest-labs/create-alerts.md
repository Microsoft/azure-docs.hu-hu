---
title: Műveletnapló-riasztások létrehozása a laborokhoz Azure DevTest Labs
description: Ez a cikk a Azure DevTest Labs a laborhoz tartozó műveletnapló-riasztások létrehozásának lépéseit ismerteti.
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: f774e3291961c58f55a9ed24026535e076235b98
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588761"
---
# <a name="create-activity-log-alerts-for-labs-in-azure-devtest-labs"></a>Műveletnapló-riasztások létrehozása a laborokhoz Azure DevTest Labs
Ez a cikk azt ismerteti, hogyan lehet tevékenység-naplózási riasztásokat létrehozni a laborokhoz Azure DevTest Labsban (például virtuális gép létrehozásakor vagy egy virtuális gép törlésekor).

## <a name="create-alerts"></a>Riasztások létrehozása
Ebben a példában riasztást hoz létre az összes felügyeleti művelethez egy tesztkörnyezetben egy olyan művelettel, amely e-mailt küld az előfizetés-tulajdonosoknak. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A Azure Portal keresési sávjában írja be a **monitor** kifejezést, majd válassza a **figyelő** lehetőséget az eredmények listából. 

    :::image type="content" source="./media/activity-logs/search-monitor.png" alt-text="Figyelő keresése":::        
1. Válassza a bal oldali menüben a **riasztások** elemet, majd az eszköztáron válassza az **új riasztási szabály** lehetőséget. 

    :::image type="content" source="./media/activity-logs/alerts-page.png" alt-text="Riasztások lap":::    
1. A **riasztási szabály létrehozása** lapon kattintson az **erőforrás kiválasztása** elemre. 

    :::image type="content" source="./media/activity-logs/select-resource-link.png" alt-text="Válassza ki az erőforrást a riasztáshoz":::        
1. Válassza a **DevTest Labs** lehetőséget az **erőforrástípus szerinti szűréshez**, válassza ki a labort a listából, majd válassza a **kész** lehetőséget.

    :::image type="content" source="./media/activity-logs/select-lab-resource.png" alt-text="Válassza ki a labort erőforrásként":::
1. Vissza a **riasztási szabály létrehozása** lapon kattintson a **feltétel kiválasztása** elemre. 

    :::image type="content" source="./media/activity-logs/select-condition-link.png" alt-text="Feltétel kiválasztása hivatkozás":::    
1. A **jel logikai beállítása** lapon válasszon ki egy, a DevTest Labs által támogatott jelet. 

    :::image type="content" source="./media/activity-logs/select-signal.png" alt-text="Jel kiválasztása":::
1. Szűrés **eseményvezérelt szint** alapján (részletes, tájékoztató, figyelmeztetés, hiba, kritikus, mind), **állapot** (sikertelen, elindított, sikeres), és **ki kezdeményezte** az eseményt. 
1. A feltétel konfigurálásának befejezéséhez válassza a **kész** lehetőséget. 

    :::image type="content" source="./media/activity-logs/configure-signal-logic-done.png" alt-text="Jel logikájának konfigurálása – kész":::
1. Meghatározta a hatókört (labor) és a riasztás feltételét. Most meg kell adnia egy műveleti csoportot a feltétel teljesülése esetén futtatandó műveletekkel. Vissza a **riasztási szabály létrehozása** lapon válassza a **műveleti csoport kiválasztása** lehetőséget. 

    :::image type="content" source="./media/activity-logs/select-action-group-link.png" alt-text="Műveleti csoport hivatkozásának kiválasztása":::
1. Válassza a **műveleti csoport létrehozása** hivatkozást az eszköztáron. 

    :::image type="content" source="./media/activity-logs/create-action-group-link.png" alt-text="Műveleti csoport létrehozása hivatkozás":::
1. A **műveleti csoport hozzáadása** oldalon hajtsa végre az alábbi lépéseket:
    1. Adja meg a műveleti csoport **nevét** .
    1. Adja meg a műveleti csoport **rövid nevét** . 
    1. Válassza ki azt az **erőforráscsoportot** , amelyben létre szeretné hozni a riasztást. 
    1. Adja meg **a művelet nevét**. 
    1. Válassza ki a **művelet típusát** (ebben a példában az **e-mail-Azure Resource Manager szerepkör**). 

        :::image type="content" source="./media/activity-logs/add-action-group.png" alt-text="Műveleti csoport hozzáadása lap":::
    1. Az **e-mail Azure Resource Manager szerepkör** lapon válassza ki a szerepkört. Ebben a példában ez a **tulajdonos**. Ez után válassza az **OK** gombot. 

        :::image type="content" source="./media/activity-logs/select-role.png" alt-text="Válasszon szerepkört":::            
    1. A **műveleti csoport hozzáadása** lapon kattintson **az OK gombra** . 
1. Most a **riasztási szabály létrehozása** lapon adja meg a riasztási szabály nevét, majd kattintson az **OK gombra**. 

    :::image type="content" source="./media/activity-logs/create-alert-rule-done.png" alt-text="Riasztási szabály létrehozása – kész":::

## <a name="view-alerts"></a>Riasztások megtekintése 
1. **A riasztások az összes** felügyeleti művelethez (ebben a példában) jelennek meg. Előfordulhat, hogy a riasztások egy kis ideig eltartanak. 

    :::image type="content" source="./media/activity-logs/alerts.png" alt-text="A képernyőfelvétel megjeleníti a riasztásokat az irányítópulton.":::
1. Ha a szám elemet választja egy oszlopban (például: **összes riasztás**), akkor a felmerült riasztások megjelennek. 

    :::image type="content" source="./media/activity-logs/all-alerts.png" alt-text="Minden riasztás":::
1. Ha kijelöl egy riasztást, a rendszer részletes információkat jelenít meg. 

    :::image type="content" source="./media/activity-logs/alert-details.png" alt-text="Riasztás részletei":::
1. Ebben a példában egy e-mailt is kap a tartalommal az alábbi példában látható módon: 

    :::image type="content" source="./media/activity-logs/alert-email.png" alt-text="Riasztási e-mail":::

## <a name="next-steps"></a>Következő lépések
- További információ a műveleti csoportok különböző Művelettípus használatával történő létrehozásáról: [műveleti csoportok létrehozása és kezelése a Azure Portalban](../azure-monitor/alerts/action-groups.md).
- További információ a tevékenységi naplókról:  [Azure-tevékenység naplója](../azure-monitor/essentials/activity-log.md).
- A riasztások a tevékenység naplófájljaiban való beállításával kapcsolatos további információkért lásd: [riasztások a tevékenység naplójában](../azure-monitor/alerts/activity-log-alerts.md).

