---
title: Azure szolgáltatás állapotára vonatkozó riasztások küldése a PagerDuty
description: Személyre szabott értesítések beszerzése a PagerDuty-példány szolgáltatás állapotával kapcsolatos eseményekről.
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 7ae5141f33ebf4ec6e32029625af3a199a04d67b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594525"
---
# <a name="send-azure-service-health-alerts-with-pagerduty-using-webhooks"></a>Azure szolgáltatásbeli állapottal kapcsolatos riasztások küldése a PagerDuty webhookok használatával

Ebből a cikkből megtudhatja, hogyan állíthatja be az Azure szolgáltatás állapotára vonatkozó értesítéseket a PagerDuty webhook használatával. A [PagerDuty](https://www.pagerduty.com/)egyéni Microsoft Azure integrációs típusának használatával könnyedén hozzáadhat Service Health-riasztásokat az új vagy meglévő PagerDuty-szolgáltatásokhoz.

## <a name="creating-a-service-health-integration-url-in-pagerduty"></a>Szolgáltatás állapot-integrációs URL-cím létrehozása a PagerDuty-ben
1.  Győződjön meg arról, hogy regisztrált, és be van jelentkezve a [PagerDuty](https://www.pagerduty.com/) -fiókjába.

1.  Navigáljon a **szolgáltatások** szakaszhoz a PagerDuty.

    ![A PagerDuty "szolgáltatások" szakasza](./media/webhook-alerts/pagerduty-services-section.png)

1.  Válassza az **új szolgáltatás hozzáadása** vagy a beállított meglévő szolgáltatás megnyitása lehetőséget.

1.  Az **integrációs beállítások** területen válassza ki a következőket:

    a. **Integráció típusa**: Microsoft Azure

    b. **Integráció neve**: \<Name\>

    ![Az "integrációs beállítások" a PagerDuty](./media/webhook-alerts/pagerduty-integration-settings.png)

1.  Töltse ki a többi kötelező mezőt, és válassza a **Hozzáadás** lehetőséget.

1.  Nyissa meg az új integrációt, és másolja és mentse az **integrációs URL-címet**.

    ![Az "integrációs URL" a PagerDuty](./media/webhook-alerts/pagerduty-integration-url.png)

## <a name="create-an-alert-using-pagerduty-in-the-azure-portal"></a>Riasztás létrehozása a Azure Portal PagerDuty használatával
### <a name="for-a-new-action-group"></a>Új műveleti csoport esetén:
1. Kövesse az 1 – 8. lépést a [riasztás létrehozása a szolgáltatás állapotáról szóló értesítésben egy új műveleti csoportra vonatkozóan a Azure Portal használatával](./alerts-activity-log-service-notifications-portal.md).

1. Definiálás a **műveletek** listájában:

    a. **Művelet típusa:** *webhook*

    b. **Részletek:** A korábban mentett PagerDuty **-integrációs URL-cím** .

    c. **Név:** Webhook neve, aliasa vagy azonosítója.

1. A riasztás létrehozásához válassza a **Mentés** lehetőséget.

### <a name="for-an-existing-action-group"></a>Meglévő műveleti csoport esetén:
1. A [Azure Portal](https://portal.azure.com/)válassza a **figyelő** elemet.

1. A **Beállítások** szakaszban válassza a **műveleti csoportok** lehetőséget.

1. Keresse meg és válassza ki a szerkeszteni kívánt műveleti csoportot.

1. Hozzáadás a **műveletek** listájához:

    a. **Művelet típusa:** *webhook*

    b. **Részletek:** A korábban mentett PagerDuty **-integrációs URL-cím** .

    c. **Név:** Webhook neve, aliasa vagy azonosítója.

1. A műveleti csoport frissítéséhez válassza a **Mentés** lehetőséget.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Webhook-integráció tesztelése HTTP POST-kérelem használatával
1. Hozza létre a küldeni kívánt szolgáltatás-állapot adattartalmát. Az Azure-beli [tevékenységekre vonatkozó riasztások webhookok](../azure-monitor/alerts/activity-log-alerts-webhook.md)szolgáltatásában például a Service Health webhook hasznos adatai találhatók.

1. Hozzon létre egy HTTP POST-kérelmet a következőképpen:

    ```
    POST        https://events.pagerduty.com/integration/<IntegrationKey>/enqueue

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Az `202 Accepted` "eseményazonosító" üzenetet tartalmazó üzenet jelenik meg.

1. Nyissa meg a [PagerDuty](https://www.pagerduty.com/) , és ellenőrizze, hogy sikeresen beállította-e az integrációt.

## <a name="next-steps"></a>Következő lépések
- Megtudhatja, hogyan [konfigurálhat webhook-értesítéseket a meglévő probléma-felügyeleti rendszerekhez](service-health-alert-webhook-guide.md).
- Tekintse át a [tevékenység naplójának riasztása webhook sémáját](../azure-monitor/alerts/activity-log-alerts-webhook.md). 
- Tudnivalók a [szolgáltatás állapotával kapcsolatos értesítésekről](./service-notifications.md).
- További információ a [műveleti csoportokról](../azure-monitor/alerts/action-groups.md).
