---
title: Azure szolgáltatásbeli állapottal kapcsolatos riasztások küldése a OpsGenie webhookok használatával
description: Személyre szabott értesítések beszerzése a OpsGenie-példány szolgáltatás állapotával kapcsolatos eseményekről.
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: d8867d442d0c7fe563f6429fc1ff4edb212737c5
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594599"
---
# <a name="send-azure-service-health-alerts-with-opsgenie-using-webhooks"></a>Azure szolgáltatásbeli állapottal kapcsolatos riasztások küldése a OpsGenie webhookok használatával

Ebből a cikkből megtudhatja, hogyan állíthatja be az Azure szolgáltatás állapotával kapcsolatos riasztásokat a OpsGenie webhook használatával. A [OpsGenie](https://www.opsgenie.com/)Azure Service Health-integrációjának használatával Azure Service Health riasztásokat továbbíthat a OpsGenie. A OpsGenie meg tudja határozni a megfelelő személyeket a hívási ütemezés alapján, e-mailben, SMS-ben, telefonhívásokkal, iOS & Android leküldéses értesítésekkel és a riasztások kiterjesztésével, amíg a riasztást nem igazolják vagy bezárták.

## <a name="creating-a-service-health-integration-url-in-opsgenie"></a>Szolgáltatás állapot-integrációs URL-cím létrehozása a OpsGenie-ben
1.  Győződjön meg arról, hogy regisztrált, és be van jelentkezve a [OpsGenie](https://www.opsgenie.com/) -fiókjába.

1.  Navigáljon a OpsGenie **integrációk** szakaszához.

    ![A OpsGenie integrációs szakasza](./media/webhook-alerts/opsgenie-integrations-section.png)

1.  Válassza a **Azure Service Health** Integration (integráció) gombot.

    ![A "Azure Service Health gomb" a OpsGenie](./media/webhook-alerts/opsgenie-azureservicehealth-button.png)

1.  **Nevezze** el a riasztást, és adja meg a **hozzárendelt csoport** mezőt.

1.  Töltse ki a többi mezőt, például a **címzetteket**, az **engedélyezést** és az **értesítések mellőzését**.

1.  Másolja ki és mentse el az **integrációs URL-címet**, amelynek már tartalmaznia kell a `apiKey` hozzáfűzést a végéhez.

    ![Az "integrációs URL" a OpsGenie](./media/webhook-alerts/opsgenie-integration-url.png)

1.  **Integrációs mentés** kiválasztása

## <a name="create-an-alert-using-opsgenie-in-the-azure-portal"></a>Riasztás létrehozása a Azure Portal OpsGenie használatával
### <a name="for-a-new-action-group"></a>Új műveleti csoport esetén:
1. Kövesse az 1 – 8. lépést a [riasztás létrehozása a szolgáltatás állapotáról szóló értesítésben egy új műveleti csoportra vonatkozóan a Azure Portal használatával](./alerts-activity-log-service-notifications-portal.md).

1. Definiálás a **műveletek** listájában:

    a. **Művelet típusa:** *webhook*

    b. **Részletek:** A korábban mentett OpsGenie **-integrációs URL-cím** .

    c. **Név:** Webhook neve, aliasa vagy azonosítója.

1. A riasztás létrehozásához válassza a **Mentés** lehetőséget.

### <a name="for-an-existing-action-group"></a>Meglévő műveleti csoport esetén:
1. A [Azure Portal](https://portal.azure.com/)válassza a **figyelő** elemet.

1. A **Beállítások** szakaszban válassza a **műveleti csoportok** lehetőséget.

1. Keresse meg és válassza ki a szerkeszteni kívánt műveleti csoportot.

1. Hozzáadás a **műveletek** listájához:

    a. **Művelet típusa:** *webhook*

    b. **Részletek:** A korábban mentett OpsGenie **-integrációs URL-cím** .

    c. **Név:** Webhook neve, aliasa vagy azonosítója.

1. A műveleti csoport frissítéséhez válassza a **Mentés** lehetőséget.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Webhook-integráció tesztelése HTTP POST-kérelem használatával
1. Hozza létre a küldeni kívánt szolgáltatás-állapot adattartalmát. Az Azure-beli [tevékenységekre vonatkozó riasztások webhookok](../azure-monitor/alerts/activity-log-alerts-webhook.md)szolgáltatásában például a Service Health webhook hasznos adatai találhatók.

1. Hozzon létre egy HTTP POST-kérelmet a következőképpen:

    ```
    POST        https://api.opsgenie.com/v1/json/azureservicehealth?apiKey=<APIKEY>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. A `200 OK` "sikeres" állapotú üzenetnek kell megjelennie.

1. Nyissa meg a [OpsGenie](https://www.opsgenie.com/) , és ellenőrizze, hogy sikeresen beállította-e az integrációt.

## <a name="next-steps"></a>Következő lépések
- Megtudhatja, hogyan [konfigurálhat webhook-értesítéseket a meglévő probléma-felügyeleti rendszerekhez](service-health-alert-webhook-guide.md).
- Tekintse át a [tevékenység naplójának riasztása webhook sémáját](../azure-monitor/alerts/activity-log-alerts-webhook.md). 
- Tudnivalók a [szolgáltatás állapotával kapcsolatos értesítésekről](./service-notifications.md).
- További információ a [műveleti csoportokról](../azure-monitor/alerts/action-groups.md).
