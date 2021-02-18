---
title: Tevékenységek naplói a Azure DevTest Labsban | Microsoft Docs
description: Ez a cikk a Azure DevTest Labs tartozó tevékenységek naplóinak megtekintésére szolgáló lépéseket ismerteti.
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: 51bdfc6c3857a3e59d75094b4c847c80c58de045
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582780"
---
# <a name="view-activity-logs-for-labs-in-azure-devtest-labs"></a>A Labs-beli tevékenységek naplóinak megtekintése Azure DevTest Labs 
Egy vagy több labor létrehozása után valószínűleg figyelnie kell a laborok elérésének, módosításának és kezelésének, valamint a kinek a figyelését. A Azure DevTest Labs Azure Monitor, konkrét **tevékenységi naplókat** használ, hogy információt szolgáltasson ezekről a műveletekről a Labs szolgáltatásban. 

Ez a cikk azt ismerteti, hogyan lehet megtekinteni a tesztkörnyezet tevékenység-naplóit Azure DevTest Labs.

## <a name="view-activity-log-for-a-lab"></a>Tesztkörnyezet megtekintése a tevékenység naplójában

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza a **minden szolgáltatás** lehetőséget, majd válassza a **DevTest Labs** elemet a **DEVOPS** szakaszban. Ha a * (csillag) lehetőséget választja a **DevTest Labs** mellett a **DEVOPS** szakaszban. Ez a művelet hozzáadja a **DevTest Labs** szolgáltatást a bal oldali navigációs menühöz, hogy a következő alkalommal könnyen elérhető legyen. Ezután kiválaszthatja a bal oldali navigációs menü **DevTest Labs** elemét.

    ![Minden szolgáltatás – válassza a DevTest Labs lehetőséget](./media/devtest-lab-create-lab/all-services-select.png)
1. A Labs listából válassza ki a labort.
1. A labor kezdőlapján válassza a bal oldali menüben a **konfigurációk és házirendek** elemet. 

    :::image type="content" source="./media/activity-logs/configuration-policies-link.png" alt-text="Válassza ki a konfigurációt és a házirendeket a bal oldali menüben":::
1. A **konfiguráció és házirendek** lapon válassza a **tevékenység napló** elemet a **kezelés** alatt a bal oldali menüben. Ekkor meg kell jelennie a laboron végzett műveletek bejegyzéseinek. 

    :::image type="content" source="./media/activity-logs/activity-log.png" alt-text="Tevékenységnapló":::    
1. Válasszon ki egy eseményt, és tekintse meg a részleteket. Az **Összefoglalás** lapon megtekintheti az olyan információkat, mint például a művelet neve, az időbélyegző, valamint a művelet. 
    
    :::image type="content" source="./media/activity-logs/stop-vm-event.png" alt-text="VM-esemény leállítása – összefoglalás":::        
1. A további részletek megtekintéséhez váltson a **JSON** lapra. A következő példában megtekintheti a virtuális gép nevét, valamint a virtuális gépen végrehajtott műveletet (leállítva).

    :::image type="content" source="./media/activity-logs/stop-vm-event-json.png" alt-text="VM-esemény leállítása – JSON":::           
1. Váltson a **change History (előzetes verzió)** lapra a változások előzményeinek megtekintéséhez. A következő példában a virtuális gépen végrehajtott módosítás látható. 

    :::image type="content" source="./media/activity-logs/change-history.png" alt-text="VM-események változási előzményeinek leállítása":::             
1. A módosítással kapcsolatos további részletek megtekintéséhez kattintson a Change History (változások előzményei) listára. 

    :::image type="content" source="./media/activity-logs/change-details.png" alt-text="VM-esemény leállítása – részletek":::             

További információ a tevékenységi naplókról: [Azure-tevékenység naplója](../azure-monitor/essentials/activity-log.md).

## <a name="next-steps"></a>Következő lépések

- A **riasztások** a tevékenység naplóiban való beállításával kapcsolatos további tudnivalókért lásd: [riasztások létrehozása](create-alerts.md).
- További információ a tevékenységi naplókról:  [Azure-tevékenység naplója](../azure-monitor/essentials/activity-log.md).

