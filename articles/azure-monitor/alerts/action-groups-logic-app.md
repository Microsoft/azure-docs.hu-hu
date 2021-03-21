---
title: Összetett műveletek elindítása Azure Monitor riasztásokkal
description: Megtudhatja, hogyan hozhat létre logikai alkalmazás-műveletet Azure Monitor riasztások feldolgozásához.
author: dkamstra
ms.author: dukek
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: a1371e00a6d4c5db609466e25c9d94aad5e73398
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102045717"
---
# <a name="how-to-trigger-complex-actions-with-azure-monitor-alerts"></a>Összetett műveletek elindítása Azure Monitor riasztásokkal

Ebből a cikkből megtudhatja, hogyan állíthat be és indíthat el egy logikai alkalmazást a Microsoft Teams-beszélgetések létrehozásához, amikor riasztást küld.

## <a name="overview"></a>Áttekintés

Amikor egy Azure Monitor riasztást indít el, meghívja a [műveleti csoportot](./action-groups.md). A műveleti csoportok lehetővé teszik, hogy egy vagy több műveletet indítson el, hogy értesítse másokat a riasztásokról, és azt is javítsa.

Az általános folyamat:

-   Hozza létre a logikai alkalmazást a megfelelő riasztási típushoz.

-   Importáljon egy minta adattartalmat a megfelelő riasztási típusba a logikai alkalmazásba.

-   Adja meg a logikai alkalmazás viselkedését.

-   Másolja a logikai alkalmazás HTTP-végpontját egy Azure-műveleti csoportba.

A folyamat hasonló, ha azt szeretné, hogy a logikai alkalmazás más műveletet hajtson végre.

## <a name="create-an-activity-log-alert-administrative"></a>Műveletnapló riasztásának létrehozása: adminisztráció

1. [Logikai alkalmazás létrehozása](~/articles/logic-apps/quickstart-create-first-logic-app-workflow.md)

2.  Válassza ki a triggert: **http-kérés fogadásakor**.

1. A **http-kérés fogadásakor** a párbeszédpanelen válassza a **minta hasznos adatok használata a séma létrehozásához** lehetőséget.

    ![Képernyőkép, amely megjeleníti a H T T P-kérések párbeszédpanelét, valamint a minta hasznos adatok használatával létrehozott séma-opion. ](~/articles/app-service/media/tutorial-send-email/generate-schema-with-payload.png)

3.  Másolja és illessze be az alábbi minta-adattartalmat a párbeszédpanelbe:

    ```json
        {
            "schemaId": "Microsoft.Insights/activityLogs",
            "data": {
                "status": "Activated",
                "context": {
                "activityLog": {
                    "authorization": {
                    "action": "microsoft.insights/activityLogAlerts/write",
                    "scope": "/subscriptions/…"
                    },
                    "channels": "Operation",
                    "claims": "…",
                    "caller": "logicappdemo@contoso.com",
                    "correlationId": "91ad2bac-1afa-4932-a2ce-2f8efd6765a3",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-04-03T22:33:11.762469+00:00",
                    "eventDataId": "ec74c4a2-d7ae-48c3-a4d0-2684a1611ca0",
                    "level": "Informational",
                    "operationName": "microsoft.insights/activityLogAlerts/write",
                    "operationId": "61f59fc8-1442-4c74-9f5f-937392a9723c",
                    "resourceId": "/subscriptions/…",
                    "resourceGroupName": "LOGICAPP-DEMO",
                    "resourceProviderName": "microsoft.insights",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:33:36.1068742+00:00",
                    "resourceType": "microsoft.insights/activityLogAlerts"
                }
                },
                "properties": {}
            }
        }
    ```

9. A **Logic app Designer** egy előugró ablakot jelenít meg, amely figyelmezteti, hogy a logikai alkalmazásnak elküldett kérelemnek be kell állítania a **Content-Type** fejlécet az **Application/JSON** értékre. Az előugró ablak bezárásához. A Azure Monitor riasztás beállítja a fejlécet.

    ![A Content-Type fejléc beállítása](media/action-groups-logic-app/content-type-header.png "A Content-Type fejléc beállítása")

10. Válassza **+** az **új lépés** , majd **a művelet hozzáadása** lehetőséget.

    ![Művelet hozzáadása](media/action-groups-logic-app/add-action.png "Művelet hozzáadása")

11. Keresse meg és válassza ki a Microsoft Teams-összekötőt. Válassza ki a **Microsoft Teams-post üzenet** műveletet.

    ![Microsoft Teams-műveletek](media/action-groups-logic-app/microsoft-teams-actions.png "Microsoft Teams-műveletek")

12. Konfigurálja a Microsoft Teams műveletet. A **Logic apps Designer** arra kéri, hogy végezzen hitelesítést a munkahelyi vagy iskolai fiókjával. Válassza ki a **csoport azonosítóját** és a **csatorna azonosítóját** , hogy elküldje az üzenetet.

13. Konfigurálja az üzenetet statikus szöveg kombinációjának használatával, és a \<fields\> dinamikus tartalomra mutató hivatkozásokat. Másolja és illessze be a következő szöveget az **üzenet** mezőbe:

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Ezután keresse meg és cserélje le az \<fields\> azonos nevű dinamikus tartalom címkéit.

    > [!NOTE]
    > Két, **állapot** nevű dinamikus mező van. Adja hozzá mindkét mezőt az üzenethez. Használja a **activityLog** tulajdonság táskájában található mezőt, és törölje a másik mezőt. Vigye az egérmutatót az **állapot** mező fölé a teljes mező hivatkozás megjelenítéséhez, ahogy az alábbi képernyőképen is látható:

    ![Microsoft Teams művelet: üzenet közzététele](media/action-groups-logic-app/teams-action-post-message.png "Microsoft Teams művelet: üzenet közzététele")

14. A **Logic apps Designer** tetején válassza a **Mentés** lehetőséget a logikai alkalmazás mentéséhez.

15. Nyissa meg a meglévő műveleti csoportot, és adjon hozzá egy műveletet a logikai alkalmazásra való hivatkozáshoz. Ha nem rendelkezik meglévő műveleti csoporttal, tekintse meg [a műveleti csoportok létrehozása és kezelése a Azure Portalban](./action-groups.md) című témakört. Ne felejtse el menteni a módosításokat.

    ![A műveleti csoport frissítése](media/action-groups-logic-app/update-action-group.png "A műveleti csoport frissítése")

Amikor legközelebb riasztást hív meg a műveleti csoportnak, a rendszer a logikai alkalmazást hívja meg.

## <a name="create-a-service-health-alert"></a>Szolgáltatás állapotára vonatkozó riasztás létrehozása

Azure Service Health bejegyzések a műveletnapló részét képezik. A riasztás létrehozásának folyamata hasonló a műveletnapló- [riasztás létrehozásához](#create-an-activity-log-alert-administrative), de néhány módosítással:

- Az 1 – 3. lépés megegyeznek.
- A 4. lépésnél használja a következő minta adattartalmat a HTTP-kérelem triggerhez:

    ```json
    {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "channels": "Admin",
                    "correlationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "description": "…",
                    "eventSource": "ServiceHealth",
                    "eventTimestamp": "2018-04-03T22:44:43.7467716+00:00",
                    "eventDataId": "9ce152f5-d435-ee31-2dce-104228486a6d",
                    "level": "Informational",
                    "operationName": "Microsoft.ServiceHealth/incident/action",
                    "operationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "properties": {
                        "title": "...",
                        "service": "...",
                        "region": "Global",
                        "communication": "...",
                        "incidentType": "Incident",
                        "trackingId": "...",
                        "impactStartTime": "2018-03-22T21:40:00.0000000Z",
                        "impactMitigationTime": "2018-03-22T21:41:00.0000000Z",
                        "impactedServices": "[{\"ImpactedRegions\"}]",
                        "defaultLanguageTitle": "...",
                        "defaultLanguageContent": "...",
                        "stage": "Active",
                        "communicationId": "11000001466525",
                        "version": "0.1.1"
                    },
                    "status": "Active",
                    "subscriptionId": "...",
                    "submissionTimestamp": "2018-04-03T22:44:50.8013523+00:00"
                }
            },
            "properties": {}
        }
    }
    ```

-  Az 5. és a 6. lépés azonos.
-  A 7 – 11. lépésnél használja a következő folyamatot:

   1. Válassza **+** az **új lépés** , majd **a feltétel hozzáadása** lehetőséget. Állítsa be a következő feltételeket, hogy a logikai alkalmazás csak akkor fusson, ha a bemeneti adatok megfelelnek az alábbi értékeknek.  Ha a Version (verzió) értéket a szövegmezőbe írja, idézőjelek közé helyezi a kifejezést ("0.1.1"), hogy az kiértékelése karakterláncként történjen, és ne numerikus típusú legyen.  A System nem jeleníti meg az idézőjeleket, ha visszatér az oldalhoz, de az alapul szolgáló kód továbbra is megőrzi a karakterlánc típusát.   
       - `schemaId == Microsoft.Insights/activityLogs`
       - `eventSource == ServiceHealth`
       - `version == "0.1.1"`

      !["Service Health hasznos adat feltétele"](media/action-groups-logic-app/service-health-payload-condition.png "Service Health hasznos adatok feltétele")

   1. A **Ha igaz** állapotban a Microsoft Teams művelet hozzáadásához kövesse a [tevékenység naplójának létrehozása](#create-an-activity-log-alert-administrative) című cikk 11 – 13. lépésében található utasításokat.

   1. Az üzenet definiálása HTML-és dinamikus tartalom kombinációjának használatával. Másolja és illessze be az alábbi tartalmat az **üzenet** mezőbe. Cserélje le a `[incidentType]` ,, `[trackingID]` `[title]` , és `[communication]` mezőket az azonos nevű dinamikus tartalom címkére:

       ```html
       <p>
       <b>Alert Type:</b>&nbsp;<strong>[incidentType]</strong>&nbsp;
       <strong>Tracking ID:</strong>&nbsp;[trackingId]&nbsp;
       <strong>Title:</strong>&nbsp;[title]</p>
       <p>
       <a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.</a>
       </p>
       <p>[communication]</p>
       ```

       !["Service Health igaz feltétel utáni művelet"](media/action-groups-logic-app/service-health-true-condition-post-action.png "Service Health igaz feltétel utáni művelet")

   1. A **Ha hamis** feltétel esetében hasznos üzenetet adjon meg:

       ```html
       <p><strong>Service Health Alert</strong></p>
       <p><b>Unrecognized alert schema</b></p>
       <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.\</a></p>
       ```

       !["Service Health hamis feltétel utáni művelet"](media/action-groups-logic-app/service-health-false-condition-post-action.png "Service Health hamis feltétel utáni művelet")

- A 15. lépés ugyanaz. Kövesse az utasításokat a logikai alkalmazás mentéséhez és a műveleti csoport frissítéséhez.

## <a name="create-a-metric-alert"></a>Metrikai riasztás létrehozása

A metrikai riasztások létrehozásának folyamata hasonló a [műveletnapló-riasztások létrehozásához](#create-an-activity-log-alert-administrative), de néhány módosítással:

- Az 1 – 3. lépés megegyeznek.
- A 4. lépésnél használja a következő minta adattartalmat a HTTP-kérelem triggerhez:

    ```json
    {
    "schemaId": "AzureMonitorMetricAlert",
    "data": {
        "version": "2.0",
        "status": "Activated",
        "context": {
        "timestamp": "2018-04-09T19:00:07.7461615Z",
        "id": "...",
        "name": "TEST-VM CPU Utilization",
        "description": "",
        "conditionType": "SingleResourceMultipleMetricCriteria",
        "condition": {
            "windowSize": "PT15M",
            "allOf": [
                {
                    "metricName": "Percentage CPU",
                    "dimensions": [
                    {
                        "name": "ResourceId",
                        "value": "d92fc5cb-06cf-4309-8c9a-538eea6a17a6"
                    }
                ],
                "operator": "GreaterThan",
                "threshold": "5",
                "timeAggregation": "PT15M",
                "metricValue": 1.0
            }
            ]
        },
        "subscriptionId": "...",
        "resourceGroupName": "TEST",
        "resourceName": "test-vm",
        "resourceType": "Microsoft.Compute/virtualMachines",
        "resourceId": "...",
        "portalLink": "..."
        },
        "properties": {}
    }
    }
    ```

- Az 5. és a 6. lépés azonos.
- A 7 – 11. lépésnél használja a következő folyamatot:

  1. Válassza **+** az **új lépés** , majd **a feltétel hozzáadása** lehetőséget. Állítsa be a következő feltételeket, hogy a logikai alkalmazás csak akkor fusson, ha a bemeneti adatok megfelelnek az alábbi értékeknek. Ha a Version (verzió) értéket a szövegmezőbe írja, az idézőjelek közé kerül ("2,0"), hogy megbizonyosodjon róla, hogy kiértékelése sztringként, nem numerikus típusúként történik.  A System nem jeleníti meg az idézőjeleket, ha visszatér az oldalhoz, de az alapul szolgáló kód továbbra is megőrzi a karakterlánc típusát. 
     - `schemaId == AzureMonitorMetricAlert`
     - `version == "2.0"`
       
       !["Metrikus riasztás hasznos feltétele"](media/action-groups-logic-app/metric-alert-payload-condition.png "Metrikus riasztás hasznos feltétele")

  1. A **Ha igaz** állapotban adja hozzá az **egyes** hurkokat és a Microsoft Teams műveletet. Az üzenet definiálása HTML-és dinamikus tartalom kombinációjának használatával.

      !["Metrikus riasztás igaz feltétel utáni művelet"](media/action-groups-logic-app/metric-alert-true-condition-post-action.png "Metrikus riasztás – igaz feltétel utáni művelet")

  1. **Ha hamis** állapotban van, Definiáljon egy Microsoft Teams műveletet, amely közli, hogy a mérőszám riasztása nem felel meg a logikai alkalmazás elvárásainak. A JSON-adattartalom belefoglalása. Figyelje meg, hogyan hivatkozhat a `triggerBody` kifejezésben szereplő dinamikus tartalomra `json()` .

      !["Metrika riasztás hamis feltétele utáni művelet"](media/action-groups-logic-app/metric-alert-false-condition-post-action.png "Metrika riasztás hamis feltétele utáni művelet")

- A 15. lépés ugyanaz. Kövesse az utasításokat a logikai alkalmazás mentéséhez és a műveleti csoport frissítéséhez.

## <a name="calling-other-applications-besides-microsoft-teams"></a>Más alkalmazások hívása a Microsoft Teams szolgáltatáson kívül
Logic Apps számos különböző összekötővel rendelkezik, amelyek lehetővé teszik, hogy számos alkalmazás és adatbázis között aktiválja a műveleteket. A Slack, SQL Server, Oracle, Salesforce, csak néhány példa. További információ az összekötők használatáról: [Logic app-összekötők](../../connectors/apis-list.md).  

## <a name="next-steps"></a>Következő lépések
* [Tekintse át az Azure-tevékenységek naplójának riasztásait](./alerts-overview.md) , és Ismerje meg, hogyan fogadhat riasztásokat.  
* Megtudhatja, hogyan [konfigurálhat riasztásokat Azure Service Health értesítés közzétételekor](../../service-health/alerts-activity-log-service-notifications-portal.md).
* További információ a [műveleti csoportokról](./action-groups.md).