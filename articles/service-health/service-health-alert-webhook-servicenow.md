---
title: Azure szolgáltatás állapotára vonatkozó riasztások küldése a ServiceNow
description: Személyre szabott értesítések beszerzése a ServiceNow-példány szolgáltatás állapotával kapcsolatos eseményekről.
ms.topic: conceptual
ms.date: 06/10/2019
ms.custom: devx-track-js
ms.openlocfilehash: df6596dd9853a792b5bfdb333361a2b9cd02a347
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588185"
---
# <a name="send-azure-service-health-alerts-with-servicenow-using-webhooks"></a>Azure szolgáltatásbeli állapottal kapcsolatos riasztások küldése a ServiceNow webhookok használatával

Ez a cikk bemutatja, hogyan integrálhatja az Azure szolgáltatás állapotával kapcsolatos riasztásokat a ServiceNow webhook használatával. Miután beállította a webhook-integrációt a ServiceNow-példánnyal, a meglévő értesítési infrastruktúrán keresztül riasztást kap, ha az Azure szolgáltatással kapcsolatos problémák hatással vannak. Minden alkalommal, amikor egy Azure Service Health riasztás következik be, egy webhookot hív meg a ServiceNow parancsfájlban megadott REST API.

## <a name="creating-a-scripted-rest-api-in-servicenow"></a>Parancsfájlból álló REST API létrehozása a ServiceNow-ben

1.  Győződjön meg arról, hogy regisztrált, és be van jelentkezve a [ServiceNow](https://www.servicenow.com/) -fiókjába.

1.  Navigáljon a ServiceNow **System Web Services** szakaszához, és válassza a **Parancsfájlozott REST API**-k elemet.

    ![A ServiceNow-ben a "megírt webszolgáltatás" szakasz](./media/webhook-alerts/servicenow-sws-section.png)

1.  Válassza az **új** lehetőséget egy új, parancsfájlban lévő REST-szolgáltatás létrehozásához.
 
    ![Az "új megírt REST API" gomb a ServiceNow](./media/webhook-alerts/servicenow-new-button.png)

1.  Adjon hozzá egy **nevet** a REST APIhoz, és állítsa be az **API-azonosítót** a következőre: `azureservicehealth` .

1.  Válassza a **Küldés** lehetőséget.

    ![A "REST API beállítások" a ServiceNow](./media/webhook-alerts/servicenow-restapi-settings.png)

1.  Válassza ki a létrehozott REST API, majd az **erőforrások** lapon válassza az **új** lehetőséget.

    ![A ServiceNow erőforrás lapja](./media/webhook-alerts/servicenow-resources-tab.png)

1.  **Nevezze** el az új erőforrást `event` , és módosítsa a **http-metódust** a következőre: `POST` .

1.  A **parancsfájl** szakaszban adja hozzá a következő JavaScript-kódot:

    >[!NOTE]
    >Az alábbi szkriptben frissítenie kell a `<secret>` , a `<group>` és a `<email>` értéket.
    >* `<secret>` véletlenszerű sztringnek kell lennie, mint például egy GUID
    >* `<group>` annak a ServiceNow-csoportnak kell lennie, amelyhez az incidenst hozzá szeretné rendelni
    >* `<email>` azon személynek kell lennie, akit hozzá kíván rendelni az incidenshez (nem kötelező)
    >

    ```javascript
    (function process( /*RESTAPIRequest*/ request, /*RESTAPIResponse*/ response) {
        var apiKey = request.queryParams['apiKey'];
        var secret = '<secret>';
        if (apiKey == secret) {
            var event = request.body.data;
            var responseBody = {};
            if (event.data.context.activityLog.operationName == 'Microsoft.ServiceHealth/incident/action') {
                var inc = new GlideRecord('incident');
                var incidentExists = false;
                inc.addQuery('number', event.data.context.activityLog.properties.trackingId);
                inc.query();
                if (inc.hasNext()) {
                    incidentExists = true;
                    inc.next();
                } else {
                    inc.initialize();
                }
                var short_description = "Azure Service Health";
                if (event.data.context.activityLog.properties.incidentType == "Incident") {
                    short_description += " - Service Issue - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Maintenance") {
                    short_description += " - Planned Maintenance - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Informational" || event.data.context.activityLog.properties.incidentType == "ActionRequired") {
                    short_description += " - Health Advisory - ";
                }
                short_description += event.data.context.activityLog.properties.title;
                inc.short_description = short_description;
                inc.description = event.data.context.activityLog.properties.communication;
                inc.work_notes = "Impacted subscription: " + event.data.context.activityLog.subscriptionId;
                if (incidentExists) {
                    if (event.data.context.activityLog.properties.stage == 'Active') {
                        inc.state = 2;
                    } else if (event.data.context.activityLog.properties.stage == 'Resolved') {
                        inc.state = 6;
                    } else if (event.data.context.activityLog.properties.stage == 'Closed') {
                        inc.state = 7;
                    }
                    inc.update();
                    responseBody.message = "Incident updated.";
                } else {
                    inc.number = event.data.context.activityLog.properties.trackingId;
                    inc.state = 1;
                    inc.impact = 2;
                    inc.urgency = 2;
                    inc.priority = 2;
                    inc.assigned_to = '<email>';
                    inc.assignment_group.setDisplayValue('<group>');
                    var subscriptionId = event.data.context.activityLog.subscriptionId;
                    var comments = "Azure portal Link: https://app.azure.com/h";
                    comments += "/" + event.data.context.activityLog.properties.trackingId;
                    comments += "/" + subscriptionId.substring(0, 3) + subscriptionId.slice(-3);
                    var impactedServices = JSON.parse(event.data.context.activityLog.properties.impactedServices);
                    var impactedServicesFormatted = "";
                    for (var i = 0; i < impactedServices.length; i++) {
                        impactedServicesFormatted += impactedServices[i].ServiceName + ": ";
                        for (var j = 0; j < impactedServices[i].ImpactedRegions.length; j++) {
                            if (j != 0) {
                                impactedServicesFormatted += ", ";
                            }
                            impactedServicesFormatted += impactedServices[i].ImpactedRegions[j].RegionName;
                        }

                        impactedServicesFormatted += "\n";

                    }
                    comments += "\n\nImpacted Services:\n" + impactedServicesFormatted;
                    inc.comments = comments;
                    inc.insert();
                    responseBody.message = "Incident created.";
                }
            } else {
                responseBody.message = "Hello from the other side!";
            }
            response.setBody(responseBody);
        } else {
            var unauthorized = new sn_ws_err.ServiceError();
            unauthorized.setStatus(401);
            unauthorized.setMessage('Invalid apiKey');
            response.setError(unauthorized);
        }
    })(request, response);
    ```

1.  A biztonság lapon törölje a **hitelesítést** , majd válassza a **Küldés** lehetőséget. A `<secret>` beállította ezt az API-t.

    ![A "hitelesítés szükséges" jelölőnégyzet a ServiceNow](./media/webhook-alerts/servicenow-resource-settings.png)

1.  A megírt REST API-k szakaszban találhatja meg az új REST API **Alap API-elérési útját** :

     ![Az "alapszintű API elérési útja" a ServiceNow-ben](./media/webhook-alerts/servicenow-base-api-path.png)

1.  A teljes integrációs URL-cím a következőképpen néz ki:

    ```http
    https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>
    ```

## <a name="create-an-alert-using-servicenow-in-the-azure-portal"></a>Riasztás létrehozása a Azure Portal ServiceNow használatával
### <a name="for-a-new-action-group"></a>Új műveleti csoport esetén:
1. Kövesse a [cikk](./alerts-activity-log-service-notifications-portal.md) 1 – 8. lépését, hogy riasztást hozzon létre egy új műveleti csoporttal.

1. Definiálás a **műveletek** listájában:

    a. **Művelet típusa:** *webhook*

    b. **Részletek:** A korábban mentett ServiceNow **-integrációs URL-cím** .

    c. **Név:** Webhook neve, aliasa vagy azonosítója.

1. A riasztás létrehozásához válassza a **Mentés** lehetőséget.

### <a name="for-an-existing-action-group"></a>Meglévő műveleti csoport esetén:
1. A [Azure Portal](https://portal.azure.com/)válassza a **figyelő** elemet.

1. A **Beállítások** szakaszban válassza a **műveleti csoportok** lehetőséget.

1. Keresse meg és válassza ki a szerkeszteni kívánt műveleti csoportot.

1. Hozzáadás a **műveletek** listájához:

    a. **Művelet típusa:** *webhook*

    b. **Részletek:** A korábban mentett ServiceNow **-integrációs URL-cím** .

    c. **Név:** Webhook neve, aliasa vagy azonosítója.

1. A műveleti csoport frissítéséhez válassza a **Mentés** lehetőséget.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Webhook-integráció tesztelése HTTP POST-kérelem használatával
1. Hozza létre a küldeni kívánt szolgáltatás-állapot adattartalmát. Az Azure-beli [tevékenységekre vonatkozó riasztások webhookok](../azure-monitor/alerts/activity-log-alerts-webhook.md)szolgáltatásában például a Service Health webhook hasznos adatai találhatók.

1. Hozzon létre egy HTTP POST-kérelmet a következőképpen:

    ```
    POST        https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Meg kell kapnia egy `200 OK` választ az "incidens létrehozva" üzenettel.

1. Nyissa meg a [ServiceNow](https://www.servicenow.com/) , és ellenőrizze, hogy sikeresen beállította-e az integrációt.

## <a name="next-steps"></a>Következő lépések
- Megtudhatja, hogyan [konfigurálhat webhook-értesítéseket a meglévő probléma-felügyeleti rendszerekhez](service-health-alert-webhook-guide.md).
- Tekintse át a [tevékenység naplójának riasztása webhook sémáját](../azure-monitor/alerts/activity-log-alerts-webhook.md). 
- Tudnivalók a [szolgáltatás állapotával kapcsolatos értesítésekről](./service-notifications.md).
- További információ a [műveleti csoportokról](../azure-monitor/alerts/action-groups.md).
