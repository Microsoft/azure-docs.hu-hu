---
title: A műveletnapló-riasztásokban használt webhook-séma ismertetése
description: Ismerje meg a webhook URL-címére küldött JSON sémáját, ha a műveletnapló riasztása aktiválva van.
ms.topic: conceptual
ms.date: 03/31/2017
ms.subservice: alerts
ms.openlocfilehash: c076b8dcea350f9ddd66977e89ce99b81f377b17
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669046"
---
# <a name="webhooks-for-azure-activity-log-alerts"></a>Webhookok az Azure-beli tevékenység naplójának értesítéseihez
A műveleti csoport definíciójának részeként konfigurálhat webhook-végpontokat a műveletnapló riasztási értesítéseinek fogadására. A webhookok segítségével ezeket az értesítéseket más rendszerekre irányíthatja a feldolgozás utáni vagy egyéni műveletekhez. Ez a cikk bemutatja, hogyan néz ki a HTTP-POST webhookhoz tartozó hasznos adat.

További információ a műveletnapló értesítéseiről: [Azure-Tevékenységnaplók riasztások létrehozása](activity-log-alerts.md).

A műveleti csoportokról a [műveleti csoportok létrehozása](../../azure-monitor/platform/action-groups.md)című témakörben olvashat bővebben.

> [!NOTE]
> Használhatja továbbá a [Common Alert sémát](https://aka.ms/commonAlertSchemaDocs)is, amely lehetővé teszi, hogy a webhook-integrációk esetében egyetlen bővíthető és egységesített riasztási adattartalom legyen a Azure monitor összes riasztási szolgáltatásában. [Ismerje meg a riasztási séma általános definícióit.](https://aka.ms/commonAlertSchemaDefinitions)


## <a name="authenticate-the-webhook"></a>A webhook hitelesítése
A webhook igény szerint jogkivonat-alapú hitelesítést is használhat a hitelesítéshez. A webhook URI-ja egy jogkivonat-AZONOSÍTÓval lett mentve, például `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`.

## <a name="payload-schema"></a>Hasznos adatok sémája
A POST műveletben található JSON-adattartalom eltér a hasznos adatok. Context. activityLog. eventSource mező alapján.

### <a name="common"></a>Közös

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Operation",
                "correlationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "eventSource": "Administrative",
                "eventTimestamp": "2017-03-29T15:43:08.0019532+00:00",
                "eventDataId": "8195a56a-85de-4663-943e-1a2bf401ad94",
                "level": "Informational",
                "operationName": "Microsoft.Insights/actionGroups/write",
                "operationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "status": "Started",
                "subStatus": "",
                "subscriptionId": "52c65f65-0518-4d37-9719-7dbbfc68c57a",
                "submissionTimestamp": "2017-03-29T15:43:20.3863637+00:00",
                ...
            }
        },
        "properties": {}
    }
}
```

### <a name="administrative"></a>Adminisztratív

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "authorization": {
                    "action": "Microsoft.Insights/actionGroups/write",
                    "scope": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions"
                },
                "claims": "{...}",
                "caller": "me@contoso.com",
                "description": "",
                "httpRequest": "{...}",
                "resourceId": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions",
                "resourceGroupName": "CONTOSO-TEST",
                "resourceProviderName": "Microsoft.Insights",
                "resourceType": "Microsoft.Insights/actionGroups"
            }
        },
        "properties": {}
    }
}
```

### <a name="security"></a>Biztonság

```json
{
    "schemaId":"Microsoft.Insights/activityLogs",
    "data":{"status":"Activated",
        "context":{
            "activityLog":{
                "channels":"Operation",
                "correlationId":"2518408115673929999",
                "description":"Failed SSH brute force attack. Failed brute force attacks were detected from the following attackers: [\"IP Address: 01.02.03.04\"].  Attackers were trying to access the host with the following user names: [\"root\"].",
                "eventSource":"Security",
                "eventTimestamp":"2017-06-25T19:00:32.607+00:00",
                "eventDataId":"Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "level":"Informational",
                "operationName":"Microsoft.Security/locations/alerts/activate/action",
                "operationId":"Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "properties":{
                    "attackers":"[\"IP Address: 01.02.03.04\"]",
                    "numberOfFailedAuthenticationAttemptsToHost":"456",
                    "accountsUsedOnFailedSignInToHostAttempts":"[\"root\"]",
                    "wasSSHSessionInitiated":"No","endTimeUTC":"06/25/2017 19:59:39",
                    "actionTaken":"Detected",
                    "resourceType":"Virtual Machine",
                    "severity":"Medium",
                    "compromisedEntity":"LinuxVM1",
                    "remediationSteps":"[In case this is an Azure virtual machine, add the source IP to NSG block list for 24 hours (see https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/)]",
                    "attackedResourceType":"Virtual Machine"
                },
                "resourceId":"/subscriptions/12345-5645-123a-9867-123b45a6789/resourceGroups/contoso/providers/Microsoft.Security/locations/centralus/alerts/Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "resourceGroupName":"contoso",
                "resourceProviderName":"Microsoft.Security",
                "status":"Active",
                "subscriptionId":"12345-5645-123a-9867-123b45a6789",
                "submissionTimestamp":"2017-06-25T20:23:04.9743772+00:00",
                "resourceType":"MICROSOFT.SECURITY/LOCATIONS/ALERTS"
            }
        },
        "properties":{}
    }
}
```

### <a name="recommendation"></a>Ajánlás

```json
{
    "schemaId":"Microsoft.Insights/activityLogs",
    "data":{
        "status":"Activated",
        "context":{
            "activityLog":{
                "channels":"Operation",
                "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
                "caller":"Microsoft.Advisor",
                "correlationId":"123b4c54-11bb-3d65-89f1-0678da7891bd",
                "description":"A new recommendation is available.",
                "eventSource":"Recommendation",
                "eventTimestamp":"2017-06-29T13:52:33.2742943+00:00",
                "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
                "eventDataId":"1bf234ef-e45f-4567-8bba-fb9b0ee1dbcb",
                "level":"Informational",
                "operationName":"Microsoft.Advisor/recommendations/available/action",
                "properties":{
                    "recommendationSchemaVersion":"1.0",
                    "recommendationCategory":"HighAvailability",
                    "recommendationImpact":"Medium",
                    "recommendationName":"Enable Soft Delete to protect your blob data",
                    "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/recommendationTypeId/12dbf883-5e4b-4f56-7da8-123b45c4b6e6",
                    "recommendationType":"12dbf883-5e4b-4f56-7da8-123b45c4b6e6"
                },
                "resourceId":"/subscriptions/12345-5645-123a-9867-123b45a6789/resourceGroups/contoso/providers/microsoft.storage/storageaccounts/contosoStore",
                "resourceGroupName":"CONTOSO",
                "resourceProviderName":"MICROSOFT.STORAGE",
                "status":"Active",
                "subStatus":"",
                "subscriptionId":"12345-5645-123a-9867-123b45a6789",
                "submissionTimestamp":"2017-06-29T13:52:33.2742943+00:00",
                "resourceType":"MICROSOFT.STORAGE/STORAGEACCOUNTS"
            }
        },
        "properties":{}
    }
}
```

### <a name="servicehealth"></a>ServiceHealth

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
            "channels": "Admin",
            "correlationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
            "description": "Active: Virtual Machines - Australia East",
            "eventSource": "ServiceHealth",
            "eventTimestamp": "2017-10-18T23:49:25.3736084+00:00",
            "eventDataId": "6fa98c0f-334a-b066-1934-1a4b3d929856",
            "level": "Informational",
            "operationName": "Microsoft.ServiceHealth/incident/action",
            "operationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
            "properties": {
                "title": "Virtual Machines - Australia East",
                "service": "Virtual Machines",
                "region": "Australia East",
                "communication": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
                "incidentType": "Incident",
                "trackingId": "0NIH-U2O",
                "impactStartTime": "2017-10-18T02:48:00.0000000Z",
                "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"}],\"ServiceName\":\"Virtual Machines\"}]",
                "defaultLanguageTitle": "Virtual Machines - Australia East",
                "defaultLanguageContent": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
                "stage": "Active",
                "communicationId": "636439673646212912",
                "version": "0.1.1"
            },
            "status": "Active",
            "subscriptionId": "45529734-0ed9-4895-a0df-44b59a5a07f9",
            "submissionTimestamp": "2017-10-18T23:49:28.7864349+00:00"
        }
    },
    "properties": {}
    }
}
```

A szolgáltatás állapotával kapcsolatos értesítési tevékenységekről értesítő riasztásokról a [szolgáltatás állapotáról szóló értesítésekben](../../azure-monitor/platform/service-notifications.md)talál további információt. Emellett megtudhatja, hogyan [konfigurálhatja a Service Health webhook-értesítéseket a meglévő probléma-kezelési megoldásokkal](../../service-health/service-health-alert-webhook-guide.md).

### <a name="resourcehealth"></a>ResourceHealth

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Admin, Operation",
                "correlationId": "a1be61fd-37ur-ba05-b827-cb874708babf",
                "eventSource": "ResourceHealth",
                "eventTimestamp": "2018-09-04T23:09:03.343+00:00",
                "eventDataId": "2b37e2d0-7bda-4de7-ur8c6-1447d02265b2",
                "level": "Informational",
                "operationName": "Microsoft.Resourcehealth/healthevent/Activated/action",
                "operationId": "2b37e2d0-7bda-489f-81c6-1447d02265b2",
                "properties": {
                    "title": "Virtual Machine health status changed to unavailable",
                    "details": "Virtual machine has experienced an unexpected event",
                    "currentHealthStatus": "Unavailable",
                    "previousHealthStatus": "Available",
                    "type": "Downtime",
                    "cause": "PlatformInitiated"
                },
                "resourceId": "/subscriptions/<subscription Id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
                "resourceGroupName": "<resource group>",
                "resourceProviderName": "Microsoft.Resourcehealth/healthevent/action",
                "status": "Active",
                "subscriptionId": "<subscription Id>",
                "submissionTimestamp": "2018-09-04T23:11:06.1607287+00:00",
                "resourceType": "Microsoft.Compute/virtualMachines"
            }
        }
    }
}
```

| Elem neve | Leírás |
| --- | --- |
| status |Metrikus riasztásokhoz használatos. Mindig az "aktivált" értékre kell beállítani a tevékenység naplójának riasztásai esetében. |
| context |Az esemény kontextusa. |
| resourceProviderName |Az érintett erőforrás erőforrás-szolgáltatója. |
| conditionType |Mindig "esemény". |
| név |A riasztási szabály neve. |
| id |A riasztás erőforrás-azonosítója. |
| leírás |Riasztás leírásának beállítása a riasztás létrehozásakor. |
| subscriptionId |Azure-előfizetés azonosítója. |
| időbélyeg |Az az idő, amikor az eseményt a kérelmet feldolgozó Azure-szolgáltatás hozta létre. |
| resourceId |Az érintett erőforrás erőforrás-azonosítója. |
| resourceGroupName |Az érintett erőforráshoz tartozó erőforráscsoport neve. |
| properties |`<Key, Value>` pár (azaz `Dictionary<String, String>`) készlete, amely az esemény részleteit tartalmazza. |
| esemény |Az eseménnyel kapcsolatos metaadatokat tartalmazó elem. |
| authorization |Az esemény szerepköralapú Access Control tulajdonságai. Ezek a tulajdonságok általában tartalmazzák a műveletet, a szerepkört és a hatókört. |
| category |Az esemény kategóriája. A támogatott értékek a következők: adminisztráció, riasztás, biztonság, ServiceHealth és javaslatok. |
| hívó |A művelet, UPN-jogcím vagy SPN-jogcím végrehajtását végző felhasználó e-mail-címe a rendelkezésre állás alapján. Bizonyos rendszerhívások esetében null értékű lehet. |
| correlationId |Általában egy GUID formátumú karakterlánc. A correlationId események ugyanahhoz a nagyobb művelethez tartoznak, és általában egy correlationId osztoznak. |
| eventDescription |Az esemény statikus szöveges leírása. |
| eventDataId |Az esemény egyedi azonosítója. |
| eventSource |Az eseményt létrehozó Azure-szolgáltatás vagy-infrastruktúra neve. |
| httpRequest |A kérelem általában magában foglalja a ügyfélkérelem, a clientIpAddress és a HTTP-metódust (például PUT). |
| szint |A következő értékek egyike: kritikus, hiba, figyelmeztetés és tájékoztatás. |
| operationId |Általában egy egyedi művelethez tartozó események között megosztva GUID. |
| operationName |A művelet neve. |
| properties |Az esemény tulajdonságai. |
| status |sztring elemet. A művelet állapotát. Az általános értékek a következők: elindítva, folyamatban, sikeres, sikertelen, aktív és megoldott. |
| Részállapot |Általában tartalmazza a megfelelő REST-hívás HTTP-állapotkódot. Tartalmazhat továbbá más, alállapotot leíró karakterláncokat is. Az általános részállapot-értékek közé tartozik az OK (HTTP-állapotkód: 200), létrehozva (HTTP-állapotkód: 201), elfogadva (HTTP-állapotkód: 202), nincs tartalom (HTTP-állapotkód: 204), hibás kérés (http-állapotkód: 400), nem található (http-állapotkód: 404), ütközés (HTTP-állapotkód: 409 ), Belső kiszolgálóhiba (HTTP-állapotkód: 500), a szolgáltatás nem érhető el (HTTP-állapotkód: 503) és az átjáró időtúllépése (HTTP-állapotkód: 504). |

Az egyéb műveletnapló-riasztásokkal kapcsolatos konkrét séma részleteiért lásd: [Az Azure-tevékenység naplójának áttekintése](../../azure-monitor/platform/platform-logs-overview.md).

## <a name="next-steps"></a>Következő lépések
* [További információ a tevékenység naplóról](../../azure-monitor/platform/platform-logs-overview.md).
* [Azure Automation-parancsfájlok (runbookok-EK) végrehajtása az Azure-riasztásokon](https://go.microsoft.com/fwlink/?LinkId=627081).
* [Egy logikai alkalmazás használatával SMS-t küldhet egy Azure-riasztásból a Twilio-on keresztül](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Ez a példa metrikus riasztásokra vonatkozik, de úgy módosítható, hogy működjön a tevékenység naplójának riasztásával.
* [Egy logikai alkalmazás használatával Slack-üzenetet küldhet egy Azure-riasztásból](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Ez a példa metrikus riasztásokra vonatkozik, de úgy módosítható, hogy működjön a tevékenység naplójának riasztásával.
* [Logikai alkalmazás használatával küldhet üzenetet egy Azure-üzenetsor számára egy Azure-riasztásból](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Ez a példa metrikus riasztásokra vonatkozik, de úgy módosítható, hogy működjön a tevékenység naplójának riasztásával.

