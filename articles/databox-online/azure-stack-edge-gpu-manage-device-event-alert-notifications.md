---
title: Eszköz-riasztási értesítések kezelése az Azure Stack Edge Pro-erőforrásokhoz | Microsoft Docs
description: Ismerteti, hogyan kezelheti a Azure Portal az eszköz eseményeire vonatkozó riasztások kezeléséhez az Azure Stack Edge Pro-erőforrásokban.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 585343137a4a8fd8a1fb591c640e1183d71c0fd3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443097"
---
# <a name="manage-device-event-alert-notifications-on-azure-stack-edge-pro-resources"></a>Eszköz-riasztási értesítések kezelése Azure Stack Edge Pro-erőforrásokon

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Ez a cikk azt ismerteti, hogyan hozhatók létre műveleti szabályok a Azure Portalban az erőforráscsoportok, Azure-előfizetések vagy egyéni Azure Stack Edge-erőforrások riasztási értesítéseinek elindításához vagy letiltásához. Ez a cikk az Azure Stack Edge összes modelljére vonatkozik.  

## <a name="about-action-rules"></a>Tudnivalók a műveleti szabályokról

A műveleti szabályok riasztási értesítéseket indíthatnak vagy hagyhatnak el. A műveleti szabály hozzá van adva egy *műveleti csoporthoz* – az értesítési beállítások egy készlete, amely a felhasználók értesítésére szolgál, akik az erőforrás vagy az erőforrások készletének különböző kontextusában aktiválva vannak.

További információ a műveleti szabályokról: [műveleti szabály konfigurálása](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule). A műveleti csoportokkal kapcsolatos további információkért lásd: [műveleti csoportok létrehozása és kezelése a Azure Portalban](../azure-monitor/alerts/action-groups.md).

> [!NOTE]
> A műveleti szabályok funkció előzetes verzióban érhető el. Egyes képernyők és lépések megváltozhatnak, mivel a folyamat finomítva van.


## <a name="create-an-action-rule"></a>Műveleti szabály létrehozása

Az Azure Stack Edge-eszközhöz tartozó műveleti szabály létrehozásához hajtsa végre a következő lépéseket a Azure Portalben.

> [!NOTE]
> Ezek a lépések olyan műveleti szabályt hoznak létre, amely értesítéseket küld egy műveleti csoportnak. További információ az értesítések letiltására szolgáló műveleti szabály létrehozásáról: [műveleti szabály konfigurálása](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule).

1. Nyissa meg az Azure Stack Edge eszközt a Azure Portal, majd lépjen a **figyelés > riasztások** elemre. Válassza a **műveletek kezelése** lehetőséget.

   ![Riasztások figyelése, műveletek nézet kezelése](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/action-rules-open-view-01.png)

2. Válassza a **műveleti szabályok (előzetes verzió)** lehetőséget, majd válassza az **+ új műveleti szabály** lehetőséget.

   ![Műveletek kezelése, műveleti szabályok beállítás](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/action-rules-open-view-02.png)

3. A **műveleti szabály létrehozása** képernyőn a **hatókör** használatával válassza ki az Azure-előfizetést, az erőforráscsoportot vagy a célként megadott erőforrást. A műveleti szabály az adott hatókörön belül létrehozott összes riasztást végrehajtja.

   1. Válassza a kiválasztás **hatókör** alapján **lehetőséget** .

      ![Új műveleti szabály hatókörének kiválasztása](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-01.png)

   2. Válassza ki az **előfizetést** a műveleti szabályhoz, opcionálisan szűrheti az **erőforrás** típusát. Ha Azure Stack peremhálózati erőforrásokra szeretne szűrni, válassza a **Data Box Edge-eszközök (dataBoxEdge)** lehetőséget.

      Az **erőforrás** -munkaterület felsorolja az elérhető erőforrásokat a választott lehetőségek alapján.
  
      ![Rendelkezésre álló erőforrások a hatókör kiválasztása képernyőn](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-02.png)

   3. Jelölje be a jelölőnégyzetet minden olyan erőforrásnál, amelyre alkalmazni kívánja a szabályt. Kiválaszthatja az előfizetést, az erőforráscsoportot vagy az egyes erőforrásokat. Ezután válassza a **Done** (Kész) elemet.

      ![A műveleti szabály hatókörére vonatkozó minta beállításai](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-03.png)

      A **műveleti szabály létrehozása** képernyő a kiválasztott hatókört jeleníti meg.

      ![Befejezett hatókör egy Azure Stack peremhálózati műveleti szabályhoz](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-04.png)

4. A **szűrési** beállítások használatával szűkítheti a szabály alkalmazását a riasztások részhalmazára a kijelölt hatókörön belül.

   1. A **Hozzáadás** gombra kattintva nyissa meg a **szűrők hozzáadása** panelt.

      ![Szűrők műveleti szabályhoz való hozzáadásának lehetősége](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-filter-01.png)

   2. A **szűrők** területen adja hozzá az alkalmazni kívánt szűrőket. Az egyes szűrők esetében válassza ki a szűrő típusát, az **operátort** és az **értéket**.
   
      A szűrési beállítások listáját a [szűrési feltételek](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#filter-criteria)című részben tekintheti meg.

      Az alábbi mintakód az összes riasztásra vonatkozik, a 2., 3. és 4. súlyossági szinten, a figyelő szolgáltatás pedig Azure Stack peremhálózati erőforrásokra.

      Ha befejezte a szűrők hozzáadását, válassza a **kész** lehetőséget.
   
      ![Minta szűrő egy műveleti szabályhoz](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-filter-02.png)

5. A **műveleti szabály létrehozása** képernyőn válassza ki a **műveleti csoport** elemet az értesítéseket küldő szabály létrehozásához. Ezután a **műveletek** területen válassza a **kiválasztás** lehetőséget.

   ![Műveleti csoport beállítása az értesítéseket küldő műveleti szabály létrehozásához](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-action-group-01.png)

   > [!NOTE]
   > Ha olyan szabályt szeretne létrehozni, amely letiltja az értesítéseket, válassza az **Eltávolítás** lehetőséget. További információ: [műveleti szabály konfigurálása](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule).

6. Válassza ki a műveleti szabállyal használni kívánt műveleti csoportot. Ezután kattintson a **Kiválasztás** gombra. Az új műveleti szabály hozzá lesz adva a kiválasztott műveleti csoport értesítési beállításaihoz.

   Ha új műveleti csoportot kell létrehoznia, válassza a **+ műveleti csoport létrehozása** lehetőséget, majd kövesse a [műveleti csoport létrehozása a Azure Portal használatával](../azure-monitor/alerts/action-groups.md#create-an-action-group-by-using-the-azure-portal)című szakasz lépéseit.

   ![Válassza ki a szabályhoz használni kívánt műveleti csoportot, majd válassza a kiválasztás lehetőséget.](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-action-group-02.png)

7. Adja meg az új művelet szabályának **nevét** és **leírását**, majd rendelje hozzá a szabályt egy erőforráscsoporthoz.

9. Az új szabály alapértelmezés szerint engedélyezve van. Ha nem szeretné azonnal használni a szabályt, válassza a **nem** lehetőséget a **szabályok frissítésének engedélyezéséhez**.

10. Amikor befejezi a beállításokat, válassza a **Létrehozás** lehetőséget.

    ![A riasztási értesítéseket küldő műveleti szabályhoz befejezett beállítások](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-completed-settings.png)

    Megnyílik a **műveleti szabályok (előzetes verzió)** képernyő, de előfordulhat, hogy azonnal nem jelenik meg az új műveleti szabály. A fókusz az **összes** erőforráscsoport.

11. Az új műveleti szabály megtekintéséhez válassza ki a szabályhoz tartozó erőforráscsoportot.

    ![A műveleti szabályok képernyő, amelyen megjelenik az új szabály](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-displayed.png)


## <a name="view-notifications"></a>Értesítések megtekintése

Az értesítések akkor lépnek fel, amikor egy új esemény riasztást indít egy műveleti szabály hatókörén belüli erőforráshoz.

Egy olyan szabálykészlet műveleti csoportja, amely értesítést kap, valamint az elküldött e-mail, a rövid üzenetküldési szolgáltatás (SMS) vagy mindkét üzenet típusát.

A riasztás elindítása után néhány percet is igénybe vehet az értesítések fogadása.

Az e-mail-értesítés a következőhöz hasonlóan fog kinézni.

![Példa e-mail-értesítésre egy műveleti szabályhoz](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/sample-action-rule-email-notification.png)


## <a name="next-steps"></a>Következő lépések

<!-- - See [Create and manage action groups in the Azure portal](../azure-monitor/alerts/action-groups.md) for guidance on creating a new action group.
- See [Configure an action rule](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule) for more info about creating action rules that send or suppress alert notifications. -2 bullets referenced above. Making room for local tasks in "Next Steps." --> 
- Az eszközök eseményeinek, a hardver állapotának és a metrikák diagramjának áttekintését lásd: [a Azure stack Edge Pro figyelése](azure-stack-edge-monitor.md) . 
- A Azure Monitor Azure Stack Edge Pro GPU-eszközökhöz való optimalizálásával kapcsolatos információkért tekintse meg a [Azure monitor használatát](azure-stack-edge-gpu-enable-azure-monitor.md) ismertető témakört.
- Az egyes riasztások kezelésével kapcsolatos információkért tekintse meg a [metrikus riasztások létrehozása, megtekintése és kezelése Azure monitor hivatkozás használatával](../azure-monitor/alerts/alerts-metric.md) című témakört.