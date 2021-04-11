---
title: Azure-erőforrásnaplók
description: Ismerje meg, hogyan továbbíthatja az Azure-erőforrás-naplókat Log Analytics munkaterületre Azure Monitorban.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: bwren
ms.openlocfilehash: 2435e4ed16889d9d4701b6047c0a1f602ee7ae91
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102558695"
---
# <a name="azure-resource-logs"></a>Azure-erőforrásnaplók
Az Azure-erőforrás-naplók olyan [platform-naplók](../essentials/platform-logs-overview.md) , amelyek betekintést nyújtanak az Azure-erőforrásokon belül végrehajtott műveletekre. Az erőforrás-naplók tartalma az Azure-szolgáltatás és az erőforrás típusa szerint változik. A rendszer alapértelmezés szerint nem gyűjti az erőforrás-naplókat. Minden egyes Azure-erőforráshoz létre kell hoznia egy diagnosztikai beállítást, hogy az erőforrás-naplókat egy Log Analytics munkaterületre küldje el, amelyet [Azure monitor naplók](../logs/data-platform-logs.md), az Azure Event Hubs az Azure-on kívüli továbbítására, illetve az Azure Storage-ba történő archiválásra kíván használni.

További információ a diagnosztikai beállítások létrehozásáról és a Azure Monitor méretezése a [különböző célhelyeken](../essentials/diagnostic-settings.md) című témakörben talál további információt a diagnosztikai beállítások létrehozásáról és a nagy [Azure Policy méretekben történő üzembe helyezéséről](../deploy-scale.md) , ha a Azure Policy használatával automatikusan hozza létre a létrehozott Azure-erőforrások diagnosztikai beállításait.

## <a name="send-to-log-analytics-workspace"></a>Küldés a Log Analytics-munkaterületre
 Az erőforrás-naplók elküldése egy Log Analytics munkaterületre a [Azure monitor naplók](../logs/data-platform-logs.md) funkcióinak engedélyezéséhez, amely a következőket tartalmazza:

- Az erőforrás-naplózási adatok korrelálása a Azure Monitor által gyűjtött egyéb megfigyelési adatokkal.
- Több Azure-erőforrásból, előfizetésből és bérlőből származó naplóbejegyzések konszolidálása egyetlen helyre az elemzéshez.
- A naplózási lekérdezésekkel összetett elemzéseket végezhet, és részletes elemzéseket készíthet a naplózási adatokról.
- A naplózási riasztásokat összetett riasztási logikával használhatja.

[Hozzon létre egy diagnosztikai beállítást](../essentials/diagnostic-settings.md) , amely erőforrás-naplókat küld egy log Analytics munkaterületre. Ezeket az adattáblákat a [Azure monitor naplók struktúrája](../logs/data-platform-logs.md)című témakörben leírtak szerint tárolja a rendszer. Az erőforrás-naplók által használt táblák attól függnek, hogy az erőforrás milyen típusú gyűjteményt használ:

- Azure Diagnostics – az összes írásos érték a [AzureDiagnostics](/azure/azure-monitor/reference/tables/azurediagnostics) tábla.
- Az erőforrás-specifikus – az erőforrás minden kategóriája esetében az egyes táblákba kerül.

### <a name="azure-diagnostics-mode"></a>Azure Diagnostics üzemmód 
Ebben a módban a diagnosztikai beállításokból származó összes adatok gyűjtése a [AzureDiagnostics](/azure/azure-monitor/reference/tables/azurediagnostics) táblában történik. Ez az örökölt módszer, amelyet jelenleg a legtöbb Azure-szolgáltatás használ. Mivel a több erőforrástípus adatokat küld ugyanahhoz a táblához, annak sémája a begyűjtött különböző adattípusok sémáinak kiterjesztése. Tekintse meg a táblázat struktúrájának részleteit, valamint azt, hogy ez milyen nagy számú oszloppal használható a [AzureDiagnostics](/azure/azure-monitor/reference/tables/azurediagnostics) .

Vegye figyelembe a következő példát, ahol a diagnosztikai beállítások gyűjtése ugyanabban a munkaterületen történik a következő adattípusok esetén:

- Az 1. szolgáltatás naplófájljai (az A, B és C oszlopokból álló sémával)  
- Az 1. szolgáltatás naplófájljai (a D, E és F oszlopokból álló sémával)  
- A 2. szolgáltatás naplófájljai (a G, H és I oszlopokból álló sémák)  

A AzureDiagnostics táblázat a következőképpen jelenik meg:  

| ResourceProvider    | Kategória     | A  | B  | C  | T  | E  | F  | G  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft. service1 | AuditLogs    | X1 | y1 | Z1 |    |    |    |    |    |    |
| Microsoft. service1 | Alkalmazásnaplókat    |    |    |    | első | W1 | E1 csomag |    |    |    |
| Microsoft. service2 | AuditLogs    |    |    |    |    |    |    | J1 | K1 csomag | L1 |
| Microsoft. service1 | Alkalmazásnaplókat    |    |    |    | Q2 | W2 | E2 |    |    |    |
| Microsoft. service2 | AuditLogs    |    |    |    |    |    |    | J3 | K3 | L3 |
| Microsoft. service1 | AuditLogs    | x5 | y5 | Z5 |    |    |    |    |    |    |
| ... |

### <a name="resource-specific"></a>Erőforrás-specifikus
Ebben a módban a kiválasztott munkaterület egyes táblái jönnek létre a diagnosztikai beállításban kiválasztott kategóriákhoz. Ez a módszer azért ajánlott, mert sokkal könnyebben használható a naplózott lekérdezések adataival, így a sémák és azok struktúrájának jobb észlelését, valamint a betöltési késés és a lekérdezési időpontok teljesítményének növelését, valamint az Azure RBAC jogosultságok egy adott táblára való megadásának lehetőségét is lehetővé teszi. A rendszer az összes Azure-szolgáltatást végül áttelepíti a Resource-Specific módba. 

A fenti példa három tábla létrehozását eredményezi:
 
- A tábla *Service1AuditLogs* a következőképpen történik:

    | Erőforrás-szolgáltató | Kategória | A | B | C |
    | -- | -- | -- | -- | -- |
    | Service1 | AuditLogs | X1 | y1 | Z1 |
    | Service1 | AuditLogs | x5 | y5 | Z5 |
    | ... |

- A tábla *Service1ErrorLogs* a következőképpen történik:  

    | Erőforrás-szolgáltató | Kategória | T | E | F |
    | -- | -- | -- | -- | -- | 
    | Service1 | Alkalmazásnaplókat |  első | W1 | E1 csomag |
    | Service1 | Alkalmazásnaplókat |  Q2 | W2 | E2 |
    | ... |

- A tábla *Service2AuditLogs* a következőképpen történik:  

    | Erőforrás-szolgáltató | Kategória | G | H | I |
    | -- | -- | -- | -- | -- |
    | Service2 | AuditLogs | J1 | K1 csomag | L1|
    | Service2 | AuditLogs | J3 | K3 | L3|
    | ... |



### <a name="select-the-collection-mode"></a>Gyűjtési mód kiválasztása
A legtöbb Azure-erőforrás az **Azure-diagnosztika** vagy az **erőforrás-specifikus mód** használatával fogja írni az adatok a munkaterületre való bevitelét anélkül, hogy választ kellene adni. Az [egyes szolgáltatásokhoz tartozó dokumentációban](./resource-logs-schema.md) megtekintheti az általa használt üzemmód részleteit. Az összes Azure-szolgáltatás végül Resource-Specific módot fog használni. Az áttérés részeként egyes erőforrások lehetővé teszik a mód kiválasztását a diagnosztikai beállításokban. Adjon meg erőforrás-specifikus módot az új diagnosztikai beállításokhoz, mivel így könnyebben kezelheti az adatok kezelését, és a későbbiekben elkerülheti az összetett Migrálás elkerülését.
  
   ![Diagnosztikai beállítások mód választója](media/resource-logs/diagnostic-settings-mode-selector.png)

> [!NOTE]
> Ha például egy Resource Manager-sablonnal állítja be a gyűjtési módot, tekintse meg a [Resource Manager-sablonok mintákat a diagnosztikai beállításokhoz a Azure monitor](./resource-manager-diagnostic-settings.md#diagnostic-setting-for-recovery-services-vault).


Egy meglévő diagnosztikai beállítást erőforrás-specifikus módra is módosíthat. Ebben az esetben a már összegyűjtött adatok a _AzureDiagnostics_ táblában maradnak, amíg el nem távolítják a munkaterület megőrzési beállításának megfelelően. Az új adatok gyűjtése a dedikált táblában történik. A [Union](/azure/kusto/query/unionoperator) operátorral több táblázaton keresztül is lekérdezheti az adatlekérdezéseket.

Tekintse meg az [Azure Updates](https://azure.microsoft.com/updates/) blogot, amely a Resource-Specific módot támogató Azure-szolgáltatásokra vonatkozó közleményeket is támogatja.


## <a name="send-to-azure-event-hubs"></a>Küldés az Azure Event Hubsba
Az Azure-on kívül, például egy harmadik féltől származó SIEM vagy más log Analytics-megoldásként küldött erőforrás-naplók küldése az Event hub-nak. Az Event hubokból származó erőforrás-naplók JSON formátumban vannak felhasználva, és `records` az egyes adattartalomban lévő rekordokat tartalmazó elemmel rendelkeznek. A séma az erőforrás típusától függ, az [Azure-erőforrás-naplókhoz tartozó általános és szolgáltatás-specifikus séma](resource-logs-schema.md)szerint.

Az alábbi példa kimeneti adatokat Event Hubs egy erőforrás-naplóhoz:

```json
{
    "records": [
        {
            "time": "2019-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2019-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```

## <a name="send-to-azure-storage"></a>Küldés az Azure Storage-be
Erőforrás-naplók küldése az Azure Storage-ba, hogy az archivált maradjon. Miután létrehozta a diagnosztikai beállítást, a rendszer egy tárolót hoz létre a Storage-fiókban, amint egy esemény bekövetkezik az engedélyezett naplózási kategóriák egyikében. A tárolóban lévő Blobok a következő elnevezési konvenciót használják:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Például egy hálózati biztonsági csoport blobjának neve a következőhöz hasonló lehet:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Mindegyik PT1H.json blob tartalmazza a blob URL-jében meghatározott órában (például h=12) bekövetkezett események JSON-blobját. Az aktuális órában az események az előfordulásukkor lesznek a PT1H.json fájlhoz fűzve. A perc értéke (m = 00) mindig 00, mivel az erőforrás-naplózási események óránként egyedi blobokra vannak bontva.

A PT1H.jsfájlon belül minden eseményt a következő formátumban tárol a rendszer. Ez közös legfelső szintű sémát használ, de minden egyes Azure-szolgáltatás esetében egyedinek kell lennie az [erőforrás-naplók sémájában](./resource-logs-schema.md)leírtak szerint.

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> A platform naplói a blob Storage-ba kerülnek a [JSON-vonalak](http://jsonlines.org/)használatával, ahol minden esemény egy sor, a sortörési karakter pedig új eseményt jelez. Ez a formátum a 2018 novemberében lett implementálva. Ezen időpont előtt a naplók a blob Storage-ba kerülnek, mint a rekordok JSON-tömbje, amelyet a következő témakörben talál: [felkészülés a formátum változására a Storage-fiókba archivált Azure monitor platform-naplók](resource-logs-blob-format.md)számára.


## <a name="next-steps"></a>Következő lépések

* [További információ az erőforrás-naplókról](../essentials/platform-logs-overview.md).
* [Diagnosztikai beállítások létrehozása a platform naplófájljainak és metrikáinak különböző célhelyekre küldéséhez](../essentials/diagnostic-settings.md).
