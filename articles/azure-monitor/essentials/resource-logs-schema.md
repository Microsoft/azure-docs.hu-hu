---
title: Az Azure Resource naplózza a támogatott szolgáltatásokat és sémákat
description: Ismerje meg az Azure-erőforrás-naplók támogatott szolgáltatásait és esemény-sémáját.
ms.subservice: logs
ms.topic: reference
ms.date: 09/01/2020
ms.openlocfilehash: 7c426074bbeb2792f152063d938e867b86dc1f6f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101717536"
---
# <a name="common-and-service-specific-schema-for-azure-resource-logs"></a>Az Azure-erőforrás naplóihoz tartozó általános és szolgáltatás-specifikus séma

> [!NOTE]
> Az erőforrás-naplók korábban diagnosztikai naplóként ismertek. A név 2019 októberében módosult, mivel az Azure Monitor által összegyűjtött naplók típusai többek között az Azure-erőforrást is tartalmazzák. Emellett az ebben a cikkben felsorolt erőforrás-napló-kategóriák listáját is felhasználhatja. Átkerültek az [erőforrás-napló kategóriájára](resource-logs-categories.md). 

[Azure monitor az erőforrás-naplókat](../essentials/platform-logs-overview.md) az Azure-szolgáltatások által kibocsátott naplók, amelyek leírják ezeknek a szolgáltatásoknak vagy erőforrásoknak a működését. A Azure Monitoron keresztül elérhető összes erőforrás-napló közös legfelső szintű sémával rendelkezik, és minden szolgáltatás számára rugalmasságot biztosít, hogy egyedi tulajdonságokat bocsát ki a saját eseményeihez.

Az erőforrástípus (a `resourceId` tulajdonságban elérhető) és a `category` séma egyedileg azonosítható kombinációja. Ez a cikk az erőforrás-naplók legfelső szintű sémáját, valamint az egyes szolgáltatások sémák mutató hivatkozásokat ismerteti.


## <a name="top-level-common-schema"></a>Legfelső szintű közös séma

| Name | Kötelező vagy nem kötelező | Leírás |
|---|---|---|
| time | Kötelező | Az esemény időbélyegzője (UTC). |
| resourceId | Kötelező | Az eseményt kibocsátó erőforrás erőforrás-azonosítója. A bérlői szolgáltatások esetében ez a/tenants/Tenant-ID/Providers/Provider-Name. formátumú. |
| tenantId | A bérlői naplókhoz szükséges | Annak a Active Directory bérlőnek a bérlői azonosítója, amelyhez ez az esemény hozzá van kötve. Ez a tulajdonság csak a bérlői szintű naplók esetében használatos, nem jelenik meg az erőforrás-szintű naplókban. |
| operationName | Kötelező | Az esemény által jelzett művelet neve. Ha az esemény egy Azure RBAC műveletet képvisel, ez az Azure RBAC művelet neve (például Microsoft. Storage/storageAccounts/blobServices/Blobs/Read). Általában Resource Manager-művelet formájában modellezve, még akkor is, ha nem ténylegesen dokumentált erőforrás-kezelői műveletek ( `Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>` ) |
| operationVersion | Választható | A művelethez társított API-verzió, ha a operationName API-val (például) lett elvégezve `http://myservice.windowsazure.net/object?api-version=2016-06-01` . Ha nincs olyan API, amely megfelel a műveletnek, akkor a verzió a művelet azon verzióját jelöli, amely a művelethez kapcsolódó tulajdonságok jövőbeli változásakor következik be. |
| category | Kötelező | Az esemény naplózási kategóriája. A kategória a részletesség, amelyen engedélyezheti vagy letilthatja a naplókat egy adott erőforráson. Az események tulajdonságok blobjában megjelenő tulajdonságok egy adott napló kategóriáján és erőforrás-típusán belül megegyeznek. A szokásos naplózási kategóriák "audit" "működési" "végrehajtás" és "kérelem". |
| resultType | Választható | Az esemény állapota. A tipikus értékek a következők: elindítva, folyamatban, sikeres, sikertelen, aktív és megoldott. |
| resultSignature | Választható | Az esemény alállapota. Ha ez a művelet egy REST API hívásnak felel meg, ez a mező a megfelelő REST-hívás HTTP-állapotkód. |
| resultDescription | Választható | A művelet statikus szöveges leírása, például a "tárolási fájl beolvasása". |
| durationMs | Választható | A művelet időtartama ezredmásodpercben. |
| callerIpAddress | Választható | A hívó IP-címe, ha a művelet olyan API-hívásnak felel meg, amely nyilvánosan elérhető IP-címmel rendelkező entitásból származik. |
| correlationId | Választható | A kapcsolódó események halmazának csoportosítására szolgáló GUID. Általában, ha két esemény ugyanazzal a operationName, de két különböző állapottal rendelkezik (például "Elindítva" és "sikeres"), ugyanazt a korrelációs azonosítót használják. Ez az események közötti egyéb kapcsolatokat is jelenthet. |
| identity | Választható | Egy JSON-blob, amely leírja a műveletet végrehajtó felhasználó vagy alkalmazás identitását. Ez a mező általában az Active Directoryból származó engedélyezési és jogcímek/JWT jogkivonatot tartalmazza. |
| Level | Választható | Az esemény súlyossági szintje. Az egyik tájékoztatási, figyelmeztetési, hiba vagy kritikus értéknek kell lennie. |
| location | Választható | Az eseményt kibocsátó erőforrás régiója, például "az USA keleti régiója" vagy "Dél-Franciaország" |
| properties | Választható | Az adott kategóriába tartozó eseményekhez kapcsolódó további tulajdonságok. Minden egyéni/egyedi tulajdonságot a séma "B része" részévé kell tenni. |

## <a name="service-specific-schemas"></a>Szolgáltatás-specifikus sémák

Az erőforrás-naplók sémája az erőforrás és a napló kategóriától függően változhat. Ez a lista azokat a szolgáltatásokat mutatja be, amelyek elérhetővé teszik az erőforrás-naplókat, valamint a szolgáltatásra mutató hivatkozásokat és a kategóriára jellemző sémát Ez a lista az új szolgáltatások hozzáadásakor folyamatosan változik, így ha nem látja, hogy mire van szüksége, a keresőmotor segítségével további dokumentációkat találhat. Nyugodtan nyisson meg egy GitHub-problémát ebben a cikkben, hogy frissítjük.

| Szolgáltatás | Séma & docs |
| --- | --- |
| Azure Active Directory | [Áttekintés](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), [naplózási napló sémája](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) és [bejelentkezési sémája](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | [Azure Analysis Services – diagnosztikai naplózás beállítása](../../analysis-services/analysis-services-logging.md) |
| API Management | [Erőforrás-naplók API Management](../../api-management/api-management-howto-use-azure-monitor.md#resource-logs) |
| App Service | [Naplók App Service](../../app-service/troubleshoot-diagnostic-logs.md)
| Application Gateway-átjárók |[Application Gateway naplózása](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Azure Automation log Analytics](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure Batch naplózás](../../batch/batch-diagnostics.md) |
| Cognitive Services | [Azure-Cognitive Services naplózása](../../cognitive-services/diagnostic-logging.md) |
| Container Registry | [Azure Container Registry naplózása](../../container-registry/container-registry-diagnostics-audit-logs.md) |
| Content Delivery Network | [A CDN-hez készült Azure-naplók](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Azure Cosmos DB naplózás](../../cosmos-db/monitor-cosmos-db.md) |
| Data Factory | [Az adatüzemek figyelése Azure Monitor használatával](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Naplók elérése Azure Data Lake Analyticshoz](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Storage |[Naplók elérése Azure Data Lake Storehoz](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Azure Data Explorer | [Azure Adatkezelő-naplók](/azure/data-explorer/using-diagnostic-logs) |
| Azure Database for MySQL | [Diagnosztikai naplók Azure Database for MySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Naplók Azure Database for PostgreSQL](../../postgresql/concepts-server-logs.md#resource-logs) |
| Azure Databricks | [Diagnosztikai naplózás az Azure Databricksben](/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs) |
| Azure Digital Twins | [Azure digitális Twins-diagnosztika beállítása](../../digital-twins/troubleshoot-diagnostics.md#log-schemas)
| Event Hubs |[Azure Event Hubs-naplók](../../event-hubs/event-hubs-diagnostic-logs.md) |
| Express Route | A séma nem érhető el. |
| Azure Firewall | A séma nem érhető el. |
| Front Door | [Bejárati ajtó naplózása](../../frontdoor/front-door-diagnostics.md) |
| IoT Hub | [IoT Hub műveletek](../../iot-hub/monitor-iot-hub-reference.md#resource-logs) |
| Key Vault |[Azure Key Vault naplózás](../../key-vault/general/logging.md) |
| Kubernetes Service |[Azure Kubernetes-naplózás](../../aks/view-control-plane-logs.md#log-event-schema) |
| Load Balancer |[Naplóelemzés az Azure Load Balancerhez](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Logic Apps B2B egyéni követési séma](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Network Security Groups (Hálózati biztonsági csoportok) |[Naplóelemzés hálózati biztonsági csoportokhoz](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDoS Protection | [Azure DDoS Protection standard naplózása](../../ddos-protection/diagnostic-logging.md#log-schemas) |
| Power BI – dedikált | [Power BI Embedded naplózása az Azure-ban](/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Azure Backup adatmodellje](../../backup/backup-azure-reports-data-model.md)|
| Keresés |[A keresési Traffic Analytics engedélyezése és használata](../../search/search-traffic-analytics.md) |
| Service Bus |[Naplók Azure Service Bus](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Azure SQL Database naplózás](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md) |
| Stream Analytics |[Feladatnaplók](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Traffic Manager naplózási séma](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Virtuális hálózatok | A séma nem érhető el. |
| Virtuális hálózati átjárók | A séma nem érhető el. |



## <a name="next-steps"></a>Következő lépések

* [A gyűjtött erőforrás-naplózási kategóriák megtekintése](resource-logs-categories.md)
* [További információ az erőforrás-naplókról](../essentials/platform-logs-overview.md)
* [Stream erőforrás-erőforrás naplófájljai **Event Hubs**](./resource-logs.md#send-to-azure-event-hubs)
* [Az erőforrás-napló diagnosztikai beállításainak módosítása a Azure Monitor használatával REST API](/rest/api/monitor/diagnosticsettings)
* [Naplók elemzése az Azure Storage-ból Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)