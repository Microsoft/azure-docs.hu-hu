---
title: Eseménynapló-riasztások fogadása az Azure szolgáltatási értesítésein Azure Portal használatával
description: Megtudhatja, hogyan állíthatja be a Azure Portal a szolgáltatás állapotával kapcsolatos értesítésekhez a Azure Portal használatával.
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 48126d923cb0baa33058c6fd55e48f31d793fade
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100570173"
---
# <a name="create-activity-log-alerts-on-service-notifications-using-the-azure-portal"></a>Műveletnapló-riasztások létrehozása a szolgáltatási értesítéseken a Azure Portal használatával
## <a name="overview"></a>Áttekintés

Ebből a cikkből megtudhatja, hogyan állíthatja be a Azure Portal a szolgáltatás állapotával kapcsolatos értesítésekhez a Azure Portal használatával.  

A szolgáltatás állapotával kapcsolatos értesítéseket az [Azure-tevékenység naplójában](../azure-monitor/essentials/platform-logs-overview.md)tárolja a rendszer. A tevékenység naplójában tárolt nagy mennyiségű információ miatt külön felhasználói felület van, amely megkönnyíti a riasztások megtekintését és beállítását a szolgáltatás állapotára vonatkozó értesítésekben. 

Riasztást kaphat, ha az Azure szolgáltatás-állapotra vonatkozó értesítéseket küld az Azure-előfizetésre. A riasztást a következő alapján állíthatja be:

- A szolgáltatás állapotáról szóló értesítés (szolgáltatási problémák, tervezett karbantartás, egészségügyi tanácsadók, biztonsági tanácsadók) osztálya.
- Az érintett előfizetés.
- Az érintett szolgáltatás (ok).
- Az érintett régió (k).

> [!NOTE]
> A szolgáltatás állapotával kapcsolatos értesítések nem küldenek riasztásokat az erőforrás-állapot eseményeihez.

Azt is beállíthatja, hogy a rendszer kik számára küldje a riasztást:

- Válasszon ki egy meglévő műveleti csoportot.
- Hozzon létre egy új műveleti csoportot (amely a jövőbeli riasztásokhoz használható).

A műveletcsoportokkal kapcsolatban további információt a [műveletcsoportok létrehozásáról és kezeléséről](../azure-monitor/alerts/action-groups.md) szóló cikkben talál.

A Service Health értesítési riasztások Azure Resource Manager sablonok használatával történő konfigurálásával kapcsolatos információkért lásd: [Resource Manager-sablonok](../azure-monitor/alerts/alerts-activity-log.md).

### <a name="watch-a-video-on-setting-up-your-first-azure-service-health-alert"></a>Tekintse meg az első Azure Service Health riasztás beállítását bemutató videót

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="create-service-health-alert-using-azure-portal"></a>Service Health riasztás létrehozása Azure Portal használatával
1. A [portálon](https://portal.azure.com)válassza a **Service Health** lehetőséget.

    ![A "Service Health" szolgáltatás](media/alerts-activity-log-service-notifications/home-servicehealth.png)

1. A **riasztások** szakaszban válassza az **állapot riasztások** lehetőséget.

    ![Az "állapot-riasztások" lap](media/alerts-activity-log-service-notifications/alerts-blades-sh.png)

1. Válassza a **szolgáltatás állapota riasztás hozzáadása** lehetőséget, és töltse ki a mezőket.

    ![A "szolgáltatás állapotára figyelmeztető riasztás létrehozása" parancs](media/alerts-activity-log-service-notifications/service-health-alert.png)

1. Válassza ki azokat az **előfizetéseket**, **szolgáltatásokat** és **régiókat** , amelyekről riasztást szeretne kapni.

    [![A "műveletnapló hozzáadása riasztás" párbeszédpanel](./media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png)](./media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png#lightbox)

> [!NOTE]
>Ez az előfizetés a műveletnapló riasztásának mentéséhez használatos. A riasztási erőforrás erre az előfizetésre van telepítve, és figyeli az eseményeket a tevékenység naplójában.

5. Válassza ki a riasztáshoz használni kívánt **esemény típusát** : *szolgáltatási probléma*, *tervezett karbantartás*, *egészségügyi tanácsadók* és *biztonsági tanácsadó*.

6. Kattintson a **műveleti csoport kiválasztása** lehetőségre egy meglévő műveleti csoport kiválasztásához, vagy hozzon létre egy új műveleti csoportot. A műveleti csoportokkal kapcsolatos további információkért lásd: [műveleti csoportok létrehozása és kezelése a Azure Portalban](../azure-monitor/alerts/action-groups.md).


7. Adja meg a riasztás részleteit a **riasztási szabály nevének** és **leírásának** megadásával.

8. Válassza ki azt az **erőforráscsoportot** , amelyben menteni szeretné a riasztást.



Néhány percen belül a riasztás aktív, és a létrehozáskor megadott feltételek alapján kezdi meg az indítást.

Megtudhatja, hogyan [konfigurálhat webhook-értesítéseket a meglévő probléma-felügyeleti rendszerekhez](service-health-alert-webhook-guide.md). A műveletnapló-riasztások webhook-sémájával kapcsolatos információkért lásd: [webhookok az Azure-beli tevékenység naplójában](../azure-monitor/alerts/activity-log-alerts-webhook.md).


## <a name="next-steps"></a>Következő lépések
- További információ [a Azure Service Health riasztások beállításával kapcsolatos ajánlott eljárásokról](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa).
- Ismerje meg, hogyan állíthat be [Azure Service Health Mobile leküldéses értesítéseket](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw).
- Megtudhatja, hogyan [konfigurálhat webhook-értesítéseket a meglévő probléma-felügyeleti rendszerekhez](service-health-alert-webhook-guide.md).
- Tudnivalók a [szolgáltatás állapotával kapcsolatos értesítésekről](service-notifications.md).
- Tudnivalók az [értesítési ráta korlátozásáról](../azure-monitor/alerts/alerts-rate-limiting.md).
- Tekintse át a [tevékenység naplójának riasztása webhook sémáját](../azure-monitor/alerts/activity-log-alerts-webhook.md).
- [Tekintse át a tevékenységek naplójának riasztásait](../azure-monitor/alerts/alerts-overview.md), és Ismerje meg, hogyan fogadhat riasztásokat.
- További információ a [műveleti csoportokról](../azure-monitor/alerts/action-groups.md).
