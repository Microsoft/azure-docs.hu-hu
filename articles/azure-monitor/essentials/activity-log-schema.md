---
title: Azure Activity log esemény sémája
description: Az Azure-tevékenység naplójának egyes kategóriáira vonatkozó esemény sémáját ismerteti.
author: bwren
services: azure-monitor
ms.topic: reference
ms.date: 09/30/2020
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: a050e9832537dd9b6690c7f9409bfbb5b795af2c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100613461"
---
# <a name="azure-activity-log-event-schema"></a>Azure Activity log esemény sémája
Az [Azure-tevékenység naplója](../platform/platform-logs-overview.md) betekintést nyújt az Azure-ban történt előfizetési szintű eseményekre. Ez a cikk a tevékenységek naplójának kategóriáit és az egyes sémákat ismerteti. 

A séma attól függően változhat, hogy hogyan fér hozzá a naplóhoz:
 
- A cikkben ismertetett sémák akkor érhetők el, amikor a [Rest APIról](/rest/api/monitor/activitylogs)fér hozzá a tevékenység naplójához. Ez a séma akkor is használatos, amikor a Azure Portal egy eseményének megtekintésekor a **JSON** lehetőséget választja.
- Ha [diagnosztikai beállítást](../platform/diagnostic-settings.md) használ a műveletnapló Azure Storage-ba vagy Azure Event Hubsba való elküldéséhez, tekintse meg a séma utolsó szakaszának [sémáját a Storage-fiók és az esemény-hubok](#schema-from-storage-account-and-event-hubs) szakaszban.
- A tevékenység naplójának Log Analytics-munkaterületre való elküldéséhez [diagnosztikai beállítással](../platform/diagnostic-settings.md) [Azure monitor adathivatkozást](/azure/azure-monitor/reference/) a sémához.

## <a name="severity-level"></a>Súlyossági szint
A tevékenység naplójának minden bejegyzése súlyossági szinttel rendelkezik. A súlyossági szint a következő értékek egyike lehet:  

| Súlyosság | Leírás |
|:---|:---|
| Kritikus | Olyan események, amelyek azonnali figyelmet igényelnek a rendszergazdától. Azt jelezheti, hogy egy alkalmazás vagy rendszer nem válaszolt vagy nem válaszol.
| Hiba | A problémát jelző események, de nem igényelnek azonnali beavatkozást.
| Figyelmeztetés | A lehetséges problémák forewarning biztosító események, bár nem tényleges hiba. Azt jelzi, hogy egy erőforrás nem ideális állapotban van, és később csökkenhet a hibák vagy kritikus események megjelenítéséhez.  
| Tájékoztató | Olyan események, amelyek nem kritikus információkat továbbítanak a rendszergazdának. Ehhez hasonlóan vegye figyelembe a következőt: "az Ön adatait". 

Az egyes erőforrás-szolgáltatók devlopers válassza ki az erőforrás-bejegyzéseik súlyossági szintjeit. Ennek eredményeképpen a tényleges súlyossága attól függően változhat, hogy az alkalmazás hogyan épül fel. Előfordulhat például, hogy a "kritikus" állapotú elemek elkülönítése egy adott erőforrás esetében nem olyan fontos, mint a "hibák" egy olyan erőforrástípus esetében, amely az Azure-alkalmazás központi eleme. Ügyeljen arra, hogy ez a tény a riasztást kiváltó események meghatározásakor vegye figyelembe.  

## <a name="categories"></a>Kategóriák
A tevékenység naplójának minden eseménye egy adott kategóriával rendelkezik, amelyet az alábbi táblázat ismertet. Az egyes kategóriákra és azok sémájára vonatkozó további részletekért tekintse meg az alábbi szakaszt, amikor a portál, a PowerShell, a CLI és a REST API a tevékenység naplóját éri el. A séma különbözik [a tevékenység naplójának tárolóba vagy Event Hubsba való továbbításakor](../platform/resource-logs.md#send-to-azure-event-hubs). Az [erőforrás-naplók sémájának](../platform/resource-logs-schema.md) tulajdonságainak leképezése a cikk utolsó szakaszában található.

| Kategória | Leírás |
|:---|:---|
| [Adminisztratív](#administrative-category) | A Resource Manageren keresztül végrehajtott összes létrehozási, frissítési, törlési és műveleti művelet rekordját tartalmazza. Ilyenek például a _virtuális gépek létrehozása_ és a _hálózati biztonsági csoport törlése_.<br><br>Egy felhasználó vagy alkalmazás által a Resource Managerrel végrehajtott összes művelet egy adott erőforrástípus műveletének megfelelően van modellezve. Ha a művelet típusa _írás_, _Törlés_ vagy _művelet_, akkor a művelet kezdési és sikerességi rekordjait is rögzíti a rendszer a felügyeleti kategóriában. A rendszergazdai események az Azure szerepköralapú hozzáférés-vezérlésének változásait is tartalmazzák egy előfizetésben. |
| [Service Health](#service-health-category) | Az Azure-ban történt összes szolgáltatás-egészségügyi incidens rekordját tartalmazza. Az _USA keleti régiójában SQL Azure Service Health eseménynek például állásidőt tapasztalhat_. <br><br>Az események Service Health hat fajta: _beavatkozás szükséges_, _támogatott helyreállítás_, _incidens_, _karbantartás_, _információ_ vagy _Biztonság_. Ezek az események csak akkor jönnek létre, ha van olyan erőforrása az előfizetésben, amelyet az esemény érint.
| [Resource Health](#resource-health-category) | Az Azure-erőforrásokra vonatkozó összes erőforrás-állapottal kapcsolatos esemény rekordját tartalmazza. Resource Health eseményre például a _virtuális gép állapota nem érhető el értékre módosult_.<br><br>Resource Health események a négy állapot egyikét jelezhetik: _elérhető_, nem _elérhető_, _csökkentett teljesítményű_ és _ismeretlen_. Emellett Resource Health eseményeket úgy is kategorizálhatja, hogy _platform kezdeményezett_ vagy _felhasználó által kezdeményezett_. |
| [Riasztás](#alert-category) | Az Azure-riasztások aktiválási rekordját tartalmazza. Egy riasztási esemény például a _MyVM CPU%-a az elmúlt 5 percben 80_.|
| [Automatikus méretezés](#autoscale-category) | Az adott előfizetésben definiált bármely, az autoskálázási motor működésével kapcsolatos események rekordját tartalmazza. Az autoskálázási eseményre például _nem sikerült a vertikális Felskálázási művelet_. |
| [Ajánlás](#recommendation-category) | A Azure Advisor ajánlásainak eseményeit tartalmazza. |
| [Biztonság](#security-category) | A Azure Security Center által generált riasztások rekordját tartalmazza. A biztonsági eseményekre példaként a rendszer _gyanús kettős kiterjesztésű fájlt futtat_. |
| [Szabályzat](#policy-category) | A Azure Policy által végrehajtott összes hatás művelet műveleteit tartalmazza. Példák a házirendi eseményekre: _naplózás_ és _Megtagadás_. A házirend által végrehajtott összes művelet az erőforráson végzett műveletként van modellezve. |

## <a name="administrative-category"></a>Felügyeleti kategória
Ez a kategória a Resource Manageren keresztül végrehajtott összes létrehozási, frissítési, törlési és műveleti művelet rekordját tartalmazza. Az ebben a kategóriában látható események típusai közé tartozik például a "virtuális gép létrehozása" és a "hálózati biztonsági csoport törlése" művelet, amelyet egy felhasználó vagy alkalmazás a Resource Manager használatával végzett minden művelet egy adott erőforrástípus működésének megfelelően modellez. Ha a művelet típusa írás, törlés vagy művelet, akkor a művelet kezdési és sikerességi rekordjait is rögzíti a rendszer a felügyeleti kategóriában. A felügyeleti kategória az Azure szerepköralapú hozzáférés-vezérlésének változásait is magában foglalja egy előfizetésben.

### <a name="sample-event"></a>Minta esemény
```json
{
    "authorization": {
        "action": "Microsoft.Network/networkSecurityGroups/write",
        "scope": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG"
    },
    "caller": "rob@contoso.com",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.core.windows.net/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "_claim_names": "{\"groups\":\"src1\"}",
        "_claim_sources": "{\"src1\":{\"endpoint\":\"https://graph.microsoft.com/1114444b-7467-4144-a616-e3a5d63e147b/users/f409edeb-4d29-44b5-9763-ee9348ad91bb/getMemberObjects\"}}",
        "http://schemas.microsoft.com/claims/authnclassreference": "1",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
        "appid": "355249ed-15d9-460d-8481-84026b065942",
        "appidacr": "2",
        "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "10845a4d-ffa4-4b61-a3b4-e57b9b31cdb5",
        "e_exp": "262800",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Robertson",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Rob",
        "ipaddr": "111.111.1.111",
        "name": "Rob Robertson",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "onprem_sid": "S-1-5-21-4837261184-168309720-1886587427-18514304",
        "puid": "18247BBD84827C6D",
        "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "rob@contoso.com",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "rob@contoso.com",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2018-01-29T20:42:31.3810679Z",
    "id": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG/events/d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d/ticks/636528553513810679",
    "level": "Informational",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Network/networkSecurityGroups/write",
        "localizedValue": "Microsoft.Network/networkSecurityGroups/write"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Network",
        "localizedValue": "Microsoft.Network"
    },
    "resourceType": {
        "value": "Microsoft.Network/networkSecurityGroups",
        "localizedValue": "Microsoft.Network/networkSecurityGroups"
    },
    "resourceId": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-01-29T20:42:50.0724829Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "statusCode": "Created",
        "serviceRequestId": "a4c11dbd-697e-47c5-9663-12362307157d",
        "responseBody": "",
        "requestbody": ""
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Tulajdonságok leírása
| Elem neve | Description |
| --- | --- |
| engedélyezés |Az esemény Azure RBAC-tulajdonságainak blobja. Általában a "művelet", a "szerepkör" és a "hatókör" tulajdonságokat tartalmazza. |
| hívó |Annak a felhasználónak az e-mail-címe, aki a művelet, UPN-jogcím vagy SPN jogcím alapján végrehajtotta a rendelkezésre állást. |
| csatornák |A következő értékek egyike: "admin", "művelet" |
| jogcímek |Az Active Directory által használt JWT-jogkivonat hitelesíti a felhasználót vagy alkalmazást a művelet végrehajtásához a Resource Managerben. |
| correlationId |Általában egy GUID formátumú karakterlánc. A correlationId osztozó események ugyanahhoz az Über-művelethez tartoznak. |
| leírás |Egy esemény statikus szöveges leírása. |
| eventDataId |Egy esemény egyedi azonosítója. |
| eventName | A felügyeleti esemény rövid neve. |
| category | Mindig "adminisztráció" |
| httpRequest |A HTTP-kérést leíró blob. Általában a "ügyfélkérelem", a "clientIpAddress" és a "metódus" (HTTP-metódus) szerepel. Például: PUT). |
| szint |Az esemény szintje. A következő értékek egyike: "kritikus", "hiba", "figyelmeztetés" és "tájékoztató" |
| resourceGroupName |Az érintett erőforráshoz tartozó erőforráscsoport neve. |
| resourceProviderName |Az érintett erőforráshoz tartozó erőforrás-szolgáltató neve |
| resourceType | A felügyeleti esemény által érintett erőforrás típusa. |
| resourceId |Az érintett erőforrás erőforrás-azonosítója. |
| operationId |Az egyetlen műveletnek megfelelő események között megosztva lévő GUID. |
| operationName |A művelet neve. |
| properties |Az `<Key, Value>` esemény részleteit leíró párok (azaz a szótár) halmaza. |
| status |A művelet állapotát leíró karakterlánc. Néhány gyakori érték: elindítva, folyamatban, sikeres, sikertelen, aktív, megoldva. |
| Részállapot |Általában a megfelelő REST-hívás HTTP-állapotkód, de tartalmazhat más, alállapotot leíró karakterláncokat is. ilyen gyakori értékek: OK (HTTP-állapotkód: 200), létrehozva (HTTP-állapotkód: 201), elfogadva (HTTP-állapotkód: 202), nincs tartalom (HTTP-állapotkód: 204), hibás kérés (http-állapotkód: 400), nem található (http-állapotkód: 404), ütközés (HTTP-állapotkód : 409), belső kiszolgálóhiba (HTTP-állapotkód: 500), a szolgáltatás nem érhető el (HTTP-állapotkód: 503), átjáró időkorlátja (HTTP-állapotkód: 504). |
| eventTimestamp |Időbélyeg, ha az eseményt az Azure-szolgáltatás hozta létre, és az eseményt az eseménynek megfelelő kérelem dolgozza fel. |
| submissionTimestamp |Időbélyeg, ha az esemény elérhetővé válik a lekérdezéshez. |
| subscriptionId |Azure-előfizetés azonosítója. |

## <a name="service-health-category"></a>Szolgáltatás állapotának kategóriája
Ez a kategória tartalmazza az Azure-ban történt összes szolgáltatás-egészségügyi incidens rekordját. Ebben a kategóriában a "SQL Azure az USA keleti régiójában az állásidőt tapasztalja." A szolgáltatás állapotával kapcsolatos események öt különböző változatban állnak rendelkezésre: a szükséges beavatkozás, a helyreállítás, az incidens, a karbantartás, az információ vagy a biztonság, és csak akkor jelenik meg, ha az előfizetésben az esemény által érintett erőforrás van.

### <a name="sample-event"></a>Minta esemény
```json
{
  "channels": "Admin",
  "correlationId": "c550176b-8f52-4380-bdc5-36c1b59d3a44",
  "description": "Active: Network Infrastructure - UK South",
  "eventDataId": "c5bc4514-6642-2be3-453e-c6a67841b073",
  "eventName": {
      "value": null
  },
  "category": {
      "value": "ServiceHealth",
      "localizedValue": "Service Health"
  },
  "eventTimestamp": "2017-07-20T23:30:14.8022297Z",
  "id": "/subscriptions/<subscription ID>/events/c5bc4514-6642-2be3-453e-c6a67841b073/ticks/636361902148022297",
  "level": "Warning",
  "operationName": {
      "value": "Microsoft.ServiceHealth/incident/action",
      "localizedValue": "Microsoft.ServiceHealth/incident/action"
  },
  "resourceProviderName": {
      "value": null
  },
  "resourceType": {
      "value": null,
      "localizedValue": ""
  },
  "resourceId": "/subscriptions/<subscription ID>",
  "status": {
      "value": "Active",
      "localizedValue": "Active"
  },
  "subStatus": {
      "value": null
  },
  "submissionTimestamp": "2017-07-20T23:30:34.7431946Z",
  "subscriptionId": "<subscription ID>",
  "properties": {
    "title": "Network Infrastructure - UK South",
    "service": "Service Fabric",
    "region": "UK South",
    "communication": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Cognitive Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "incidentType": "Incident",
    "trackingId": "NA0F-BJG",
    "impactStartTime": "2017-07-20T21:41:00.0000000Z",
    "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"UK South\"}],\"ServiceName\":\"Service Fabric\"}]",
    "defaultLanguageTitle": "Network Infrastructure - UK South",
    "defaultLanguageContent": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Cognitive Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "stage": "Active",
    "communicationId": "636361902146035247",
    "version": "0.1.1"
  }
}
```
A tulajdonságok között található értékekkel kapcsolatos dokumentációért tekintse meg a [Service Health Notifications](../../service-health/service-notifications.md) című cikket.

## <a name="resource-health-category"></a>Erőforrás állapotának kategóriája
Ez a kategória az Azure-erőforrásokra vonatkozó összes erőforrás-állapottal kapcsolatos esemény rekordját tartalmazza. Ebben a kategóriában a "virtuális gép állapota nem érhető el értékre módosult" típusú eseményre mutat példát. Az erőforrás-állapot eseményei a négy állapot egyikét jelezhetik: elérhető, nem elérhető, csökkentett teljesítményű és ismeretlen. Emellett az erőforrás-állapot eseményei a platform által kezdeményezett vagy a felhasználó által kezdeményezett módon kategorizálva is lehetnek.

### <a name="sample-event"></a>Minta esemény

```json
{
    "channels": "Admin, Operation",
    "correlationId": "28f1bfae-56d3-7urb-bff4-194d261248e9",
    "description": "",
    "eventDataId": "a80024e1-883d-37ur-8b01-7591a1befccb",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "ResourceHealth",
        "localizedValue": "Resource Health"
    },
    "eventTimestamp": "2018-09-04T15:33:43.65Z",
    "id": "/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>/events/a80024e1-883d-42a5-8b01-7591a1befccb/ticks/636716720236500000",
    "level": "Critical",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Resourcehealth/healthevent/Activated/action",
        "localizedValue": "Health Event Activated"
    },
    "resourceGroupName": "<resource group>",
    "resourceProviderName": {
        "value": "Microsoft.Resourcehealth/healthevent/action",
        "localizedValue": "Microsoft.Resourcehealth/healthevent/action"
    },
    "resourceType": {
        "value": "Microsoft.Compute/virtualMachines",
        "localizedValue": "Microsoft.Compute/virtualMachines"
    },
    "resourceId": "/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-09-04T15:36:24.2240867Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "stage": "Active",
        "title": "Virtual Machine health status changed to unavailable",
        "details": "Virtual machine has experienced an unexpected event",
        "healthStatus": "Unavailable",
        "healthEventType": "Downtime",
        "healthEventCause": "PlatformInitiated",
        "healthEventCategory": "Unplanned"
    },
    "relatedEvents": []
}
```

### <a name="property-descriptions"></a>Tulajdonságok leírása
| Elem neve | Description |
| --- | --- |
| csatornák | Mindig a "rendszergazda, művelet" |
| correlationId | A karakterlánc formátumú GUID. |
| leírás |A riasztási esemény statikus szöveges leírása. |
| eventDataId |A riasztási esemény egyedi azonosítója. |
| category | Mindig "ResourceHealth" |
| eventTimestamp |Időbélyeg, ha az eseményt az Azure-szolgáltatás hozta létre, és az eseményt az eseménynek megfelelő kérelem dolgozza fel. |
| szint |Az esemény szintje. A következő értékek egyike: "kritikus", "hiba", "figyelmeztetés", "tájékoztató" és "részletes" |
| operationId |Az egyetlen műveletnek megfelelő események között megosztva lévő GUID. |
| operationName |A művelet neve. |
| resourceGroupName |Az erőforrást tartalmazó erőforráscsoport neve. |
| resourceProviderName |Mindig "Microsoft. Resourcehealth/healthevent/Action". |
| resourceType | Resource Health esemény által érintett erőforrás típusa. |
| resourceId | Az érintett erőforráshoz tartozó erőforrás-azonosító neve. |
| status |Az állapotadatok állapotát leíró karakterlánc. Az értékek a következőket okozhatják: aktív, megoldott, Inprogress, frissítve. |
| Részállapot | Általában null a riasztásokhoz. |
| submissionTimestamp |Időbélyeg, ha az esemény elérhetővé válik a lekérdezéshez. |
| subscriptionId |Azure-előfizetés azonosítója. |
| properties |Az `<Key, Value>` esemény részleteit leíró párok (azaz a szótár) halmaza.|
| tulajdonságok. cím | Egy felhasználóbarát karakterlánc, amely leírja az erőforrás állapotát. |
| Properties. details | Egy felhasználóbarát karakterlánc, amely az esemény további részleteit ismerteti. |
| Properties. currentHealthStatus | Az erőforrás jelenlegi állapotának állapota. A következő értékek egyike: "elérhető", "nem elérhető", "csökkentett teljesítményű" és "ismeretlen". |
| Properties. previousHealthStatus | Az erőforrás előző állapotának állapota. A következő értékek egyike: "elérhető", "nem elérhető", "csökkentett teljesítményű" és "ismeretlen". |
| tulajdonságok. típus | A Resource Health esemény típusának leírása. |
| tulajdonságok. ok | A Resource Health esemény okának leírása. Vagy "UserInitiated" és "PlatformInitiated". |


## <a name="alert-category"></a>Riasztási kategória
Ez a kategória a klasszikus Azure-riasztások összes aktiválásának rekordját tartalmazza. Ebben a kategóriában a "CPU% on myVM az elmúlt 5 percben a 80-as számú esemény látható." Számos Azure-rendszer rendelkezik riasztási koncepcióval – meghatározhat valamilyen rendezési szabályt, és értesítést kaphat, ha a feltételek megfelelnek a szabálynak. Minden alkalommal, amikor egy támogatott Azure-riasztási típus aktiválódik, vagy ha teljesülnek a feltételek, a rendszer az aktiválási rekordot is leküldi a tevékenységi napló ezen kategóriájára.

### <a name="sample-event"></a>Minta esemény

```json
{
  "caller": "Microsoft.Insights/alertRules",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/alertRules"
  },
  "correlationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "description": "'Disk read LessThan 100000 ([Count]) in the last 5 minutes' has been resolved for CloudService: myResourceGroup/Production/Event.BackgroundJobsWorker.razzle (myResourceGroup)",
  "eventDataId": "149d4baf-53dc-4cf4-9e29-17de37405cd9",
  "eventName": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "category": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle/events/149d4baf-53dc-4cf4-9e29-17de37405cd9/ticks/636362258535221920",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "Microsoft.ClassicCompute",
    "localizedValue": "Microsoft.ClassicCompute"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle",
  "resourceType": {
    "value": "Microsoft.ClassicCompute/domainNames/slots/roles",
    "localizedValue": "Microsoft.ClassicCompute/domainNames/slots/roles"
  },
  "operationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "operationName": {
    "value": "Microsoft.Insights/AlertRules/Resolved/Action",
    "localizedValue": "Microsoft.Insights/AlertRules/Resolved/Action"
  },
  "properties": {
    "RuleUri": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert",
    "RuleName": "myalert",
    "RuleDescription": "",
    "Threshold": "100000",
    "WindowSizeInMinutes": "5",
    "Aggregation": "Average",
    "Operator": "LessThan",
    "MetricName": "Disk read",
    "MetricUnit": "Count"
  },
  "status": {
    "value": "Resolved",
    "localizedValue": "Resolved"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T09:24:13.522192Z",
  "submissionTimestamp": "2017-07-21T09:24:15.6578651Z",
  "subscriptionId": "<subscription ID>"
}
```

### <a name="property-descriptions"></a>Tulajdonságok leírása
| Elem neve | Description |
| --- | --- |
| hívó | Mindig Microsoft. bepillantást/alertRules |
| csatornák | Mindig a "rendszergazda, művelet" |
| jogcímek | JSON-blob a riasztási motor SPN-vel (egyszerű szolgáltatásnév) vagy erőforrás-típussal. |
| correlationId | A karakterlánc formátumú GUID. |
| leírás |A riasztási esemény statikus szöveges leírása. |
| eventDataId |A riasztási esemény egyedi azonosítója. |
| category | Mindig "riasztás" |
| szint |Az esemény szintje. A következő értékek egyike: "kritikus", "hiba", "figyelmeztetés" és "tájékoztató" |
| resourceGroupName |Az érintett erőforráshoz tartozó erőforráscsoport neve, ha metrikai riasztás. Más típusú riasztások esetén ez a riasztást tartalmazó erőforráscsoport neve. |
| resourceProviderName |Az érintett erőforráshoz tartozó erőforrás-szolgáltató neve, ha metrikai riasztás. Más típusú riasztások esetén a riasztáshoz tartozó erőforrás-szolgáltató neve. |
| resourceId | Az érintett erőforráshoz tartozó erőforrás-azonosító neve, ha metrikai riasztás. Más típusú riasztások esetén maga a riasztási erőforrás erőforrás-azonosítója. |
| operationId |Az egyetlen műveletnek megfelelő események között megosztva lévő GUID. |
| operationName |A művelet neve. |
| properties |Az `<Key, Value>` esemény részleteit leíró párok (azaz a szótár) halmaza. |
| status |A művelet állapotát leíró karakterlánc. Néhány gyakori érték: elindítva, folyamatban, sikeres, sikertelen, aktív, megoldva. |
| Részállapot | Általában null a riasztásokhoz. |
| eventTimestamp |Időbélyeg, ha az eseményt az Azure-szolgáltatás hozta létre, és az eseményt az eseménynek megfelelő kérelem dolgozza fel. |
| submissionTimestamp |Időbélyeg, ha az esemény elérhetővé válik a lekérdezéshez. |
| subscriptionId |Azure-előfizetés azonosítója. |

### <a name="properties-field-per-alert-type"></a>Tulajdonságok mező/riasztás típusa
A tulajdonságok mező a riasztási esemény forrásától függően eltérő értékeket fog tartalmazni. Két gyakori riasztási esemény szolgáltatója a tevékenységek naplójának riasztásai és a metrikák riasztásai.

#### <a name="properties-for-activity-log-alerts"></a>A műveletnapló-riasztások tulajdonságai
| Elem neve | Description |
| --- | --- |
| Properties. subscriptionId | A tevékenység naplójának eseményéhez tartozó előfizetés-azonosító, amely miatt a tevékenység naplójának riasztási szabálya aktiválva lett. |
| Properties. eventDataId | A tevékenység naplójának eseményéhez tartozó esemény-azonosító, amely miatt a tevékenység naplójának riasztási szabálya aktiválva lett. |
| Properties. resourceGroup | Az a műveletnapló eseményből származó erőforráscsoport, amely miatt a tevékenység naplójának riasztási szabálya aktiválva lett. |
| Properties. resourceId | A tevékenység naplójának eseményéhez tartozó erőforrás-azonosító, amely miatt a tevékenység naplójának riasztási szabálya aktiválva lett. |
| Properties. eventTimestamp | A tevékenység naplózási eseményének az esemény időbélyegzője, amely miatt a tevékenység naplójának riasztási szabálya aktiválva lett. |
| Properties. operationName | A művelet neve a tevékenység naplójának eseményében, amely miatt a tevékenység naplójának riasztási szabálya aktiválva lett. |
| tulajdonságok. status | A tevékenység naplózási eseményének állapota, amely miatt a rendszer aktiválja a tevékenység naplójának riasztási szabályát.|

#### <a name="properties-for-metric-alerts"></a>Metrikus riasztások tulajdonságai
| Elem neve | Description |
| --- | --- |
| Tulajdonságok. RuleUri | A metrika riasztási szabályának erőforrás-azonosítója. |
| Tulajdonságok. RuleName | A metrika riasztási szabályának neve. |
| Tulajdonságok. RuleDescription | A metrika riasztási szabályának leírása (a riasztási szabályban meghatározottak szerint). |
| Tulajdonságok. Küszöb | A metrika riasztási szabályának kiértékeléséhez használt küszöbérték. |
| Tulajdonságok. WindowSizeInMinutes | A metrika riasztási szabályának kiértékeléséhez használt ablakméret |
| Tulajdonságok. Összesítési | A metrika riasztási szabályában definiált összesítési típus. |
| Tulajdonságok. Üzemeltető | A metrika riasztási szabályának kiértékelésében használt feltételes operátor. |
| Tulajdonságok. MetricName | A metrika riasztási szabályának kiértékeléséhez használt metrika metrikai neve. |
| Tulajdonságok. MetricUnit | A metrika riasztási szabályának kiértékeléséhez használt metrika mérőszáma. |

## <a name="autoscale-category"></a>Méretezési kategória
Ez a kategória tartalmazza az adott előfizetésben definiált, az előfizetéshez megadott összes autoskálázási beállítás alapján az autoskálázási motor működésével kapcsolatos események rekordját. Egy példa arra, hogy milyen típusú eseményre lenne szüksége ebben a kategóriában: "a vertikális Felskálázási művelet nem sikerült." Az automatikus méretezés használatával automatikusan kibővítheti vagy méretezheti a támogatott erőforrástípus példányainak számát a nap-és/vagy betöltési (metrikus) adatokon alapuló automatikus méretezési beállítással. Ha a feltételek teljesülnek a vertikális fel-vagy leskálázáshoz, a rendszer a kezdő és a sikeres vagy sikertelen eseményeket rögzíti ebben a kategóriában.

### <a name="sample-event"></a>Minta esemény
```json
{
  "caller": "Microsoft.Insights/autoscaleSettings",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/autoscaleSettings"
  },
  "correlationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
  "eventDataId": "a5b92075-1de9-42f1-b52e-6f3e4945a7c7",
  "eventName": {
    "value": "AutoscaleAction",
    "localizedValue": "AutoscaleAction"
  },
  "category": {
    "value": "Autoscale",
    "localizedValue": "Autoscale"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup/events/a5b92075-1de9-42f1-b52e-6f3e4945a7c7/ticks/636361956518681572",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "microsoft.insights",
    "localizedValue": "microsoft.insights"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup",
  "resourceType": {
    "value": "microsoft.insights/autoscalesettings",
    "localizedValue": "microsoft.insights/autoscalesettings"
  },
  "operationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "operationName": {
    "value": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action",
    "localizedValue": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action"
  },
  "properties": {
    "Description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
    "ResourceName": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource",
    "OldInstancesCount": "3",
    "NewInstancesCount": "2",
    "LastScaleActionTime": "Fri, 21 Jul 2017 01:00:51 GMT"
  },
  "status": {
    "value": "Succeeded",
    "localizedValue": "Succeeded"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T01:00:51.8681572Z",
  "submissionTimestamp": "2017-07-21T01:00:52.3008754Z",
  "subscriptionId": "<subscription ID>"
}

```

### <a name="property-descriptions"></a>Tulajdonságok leírása
| Elem neve | Description |
| --- | --- |
| hívó | Mindig Microsoft. bepillantást/autoscaleSettings |
| csatornák | Mindig a "rendszergazda, művelet" |
| jogcímek | JSON-blob az autoskálázási motor SPN-vel (egyszerű szolgáltatásnév) vagy erőforrás-típussal. |
| correlationId | A karakterlánc formátumú GUID. |
| leírás |Az autoskálázási esemény statikus szöveges leírása. |
| eventDataId |Az autoskálázási esemény egyedi azonosítója. |
| szint |Az esemény szintje. A következő értékek egyike: "kritikus", "hiba", "figyelmeztetés" és "tájékoztató" |
| resourceGroupName |Az autoskálázási beállításhoz tartozó erőforráscsoport neve. |
| resourceProviderName |Az erőforrás-szolgáltató neve az autoskálázási beállításhoz. |
| resourceId |Az autoskálázási beállítás erőforrás-azonosítója. |
| operationId |Az egyetlen műveletnek megfelelő események között megosztva lévő GUID. |
| operationName |A művelet neve. |
| properties |Az `<Key, Value>` esemény részleteit leíró párok (azaz a szótár) halmaza. |
| Tulajdonságok. Leírás | Annak részletes leírása, hogy mit csinált az autoskálázási motor. |
| Tulajdonságok. ResourceName | Az érintett erőforrás erőforrás-azonosítója (az erőforrás, amelyen a skálázási műveletet végrehajtották) |
| Tulajdonságok. OldInstancesCount | A példányok száma, mielőtt az autoskálázási művelet érvénybe lépett. |
| Tulajdonságok. NewInstancesCount | A példányok száma, miután az autoskálázás művelet érvénybe lépett. |
| Tulajdonságok. LastScaleActionTime | Az az időbélyeg, amikor az autoskálázás művelet bekövetkezett. |
| status |A művelet állapotát leíró karakterlánc. Néhány gyakori érték: elindítva, folyamatban, sikeres, sikertelen, aktív, megoldva. |
| Részállapot | Általában null az autoskálázáshoz. |
| eventTimestamp |Időbélyeg, ha az eseményt az Azure-szolgáltatás hozta létre, és az eseményt az eseménynek megfelelő kérelem dolgozza fel. |
| submissionTimestamp |Időbélyeg, ha az esemény elérhetővé válik a lekérdezéshez. |
| subscriptionId |Azure-előfizetés azonosítója. |

## <a name="security-category"></a>Biztonsági kategória
Ez a kategória tartalmazza a Azure Security Center által generált riasztások rekordját. Ebben a kategóriában egy példa arra, hogy milyen típusú eseményt láthat a "gyanús dupla kiterjesztésű fájl végrehajtása".

### <a name="sample-event"></a>Minta esemény
```json
{
    "channels": "Operation",
    "correlationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "description": "Suspicious double extension file executed. Machine logs indicate an execution of a process with a suspicious double extension.\r\nThis extension may trick users into thinking files are safe to be opened and might indicate the presence of malware on the system.",
    "eventDataId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "eventName": {
        "value": "Suspicious double extension file executed",
        "localizedValue": "Suspicious double extension file executed"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2017-10-18T06:02:18.6179339Z",
    "id": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/events/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/ticks/636439033386179339",
    "level": "Informational",
    "operationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Microsoft.Security/locations/alerts/activate/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/2518939942613820660_a48f8653-3fc6-4166-9f19-914f030a13d3",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": null
    },
    "submissionTimestamp": "2017-10-18T06:02:52.2176969Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "accountLogonId": "0x2r4",
        "commandLine": "c:\\mydirectory\\doubleetension.pdf.exe",
        "domainName": "hpc",
        "parentProcess": "unknown",
        "parentProcess id": "0",
        "processId": "6988",
        "processName": "c:\\mydirectory\\doubleetension.pdf.exe",
        "userName": "myUser",
        "UserSID": "S-3-2-12",
        "ActionTaken": "Detected",
        "Severity": "High"
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Tulajdonságok leírása
| Elem neve | Description |
| --- | --- |
| csatornák | Mindig "művelet" |
| correlationId | A karakterlánc formátumú GUID. |
| leírás |A biztonsági esemény statikus szöveges leírása. |
| eventDataId |A biztonsági esemény egyedi azonosítója. |
| eventName |A biztonsági esemény rövid neve. |
| category | Mindig "biztonság" |
| ID (Azonosító) |A biztonsági esemény egyedi erőforrás-azonosítója. |
| szint |Az esemény szintje. A következő értékek egyike: "kritikus", "hiba", "figyelmeztetés" vagy "tájékoztató" |
| resourceGroupName |Az erőforrás erőforráscsoport neve. |
| resourceProviderName |A Azure Security Center erőforrás-szolgáltatójának neve. Mindig "Microsoft. Security". |
| resourceType |A biztonsági eseményt létrehozó erőforrás típusa, például "Microsoft. Security/Locations/riasztások" |
| resourceId |A biztonsági riasztás erőforrás-azonosítója. |
| operationId |Az egyetlen műveletnek megfelelő események között megosztva lévő GUID. |
| operationName |A művelet neve. |
| properties |Az `<Key, Value>` esemény részleteit leíró párok (azaz a szótár) halmaza. Ezek a tulajdonságok a biztonsági riasztás típusától függően változnak. [Ezen az oldalon](../../security-center/security-center-alerts-overview.md) megtekintheti az Security Centerból származó riasztási típusok leírását. |
| Tulajdonságok. Súlyosság |A súlyossági szint. A lehetséges értékek: "magas", "közepes" vagy "alacsony". |
| status |A művelet állapotát leíró karakterlánc. Néhány gyakori érték: elindítva, folyamatban, sikeres, sikertelen, aktív, megoldva. |
| Részállapot | Általában null a biztonsági eseményekhez. |
| eventTimestamp |Időbélyeg, ha az eseményt az Azure-szolgáltatás hozta létre, és az eseményt az eseménynek megfelelő kérelem dolgozza fel. |
| submissionTimestamp |Időbélyeg, ha az esemény elérhetővé válik a lekérdezéshez. |
| subscriptionId |Azure-előfizetés azonosítója. |

## <a name="recommendation-category"></a>Javaslat kategóriája
Ez a kategória a szolgáltatásokhoz létrehozott új javaslatok rekordját tartalmazza. Egy javaslat példaként a "rendelkezésre állási csoportok használata a jobb hibatűrés érdekében" értékre mutat. A rendszer négyféle ajánlási eseményt hozhat létre: magas rendelkezésre állást, teljesítményt, biztonságot és költségmegtakarítást. 

### <a name="sample-event"></a>Minta esemény
```json
{
    "channels": "Operation",
    "correlationId": "92481dfd-c5bf-4752-b0d6-0ecddaa64776",
    "description": "The action was successful.",
    "eventDataId": "06cb0e44-111b-47c7-a4f2-aa3ee320c9c5",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Recommendation",
        "localizedValue": "Recommendation"
    },
    "eventTimestamp": "2018-06-07T21:30:42.976919Z",
    "id": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM/events/06cb0e44-111b-47c7-a4f2-aa3ee320c9c5/ticks/636640038429769190",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Advisor/generateRecommendations/action",
        "localizedValue": "Microsoft.Advisor/generateRecommendations/action"
    },
    "resourceGroupName": "MYRESOURCEGROUP",
    "resourceProviderName": {
        "value": "MICROSOFT.COMPUTE",
        "localizedValue": "MICROSOFT.COMPUTE"
    },
    "resourceType": {
        "value": "MICROSOFT.COMPUTE/virtualmachines",
        "localizedValue": "MICROSOFT.COMPUTE/virtualmachines"
    },
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-06-07T21:30:42.976919Z",
    "subscriptionId": "<Subscription ID>",
    "properties": {
        "recommendationSchemaVersion": "1.0",
        "recommendationCategory": "Security",
        "recommendationImpact": "High",
        "recommendationRisk": "None"
    },
    "relatedEvents": []
}

```
### <a name="property-descriptions"></a>Tulajdonságok leírása
| Elem neve | Description |
| --- | --- |
| csatornák | Mindig "művelet" |
| correlationId | A karakterlánc formátumú GUID. |
| leírás |A javaslati esemény statikus szöveges leírása |
| eventDataId | A javaslati esemény egyedi azonosítója. |
| category | Mindig "javaslat" |
| ID (Azonosító) |A javaslati esemény egyedi erőforrás-azonosítója. |
| szint |Az esemény szintje. A következő értékek egyike: "kritikus", "hiba", "figyelmeztetés" vagy "tájékoztató" |
| operationName |A művelet neve.  Mindig "Microsoft. Advisor/generateRecommendations/Action"|
| resourceGroupName |Az erőforrás erőforráscsoport neve. |
| resourceProviderName |Azon erőforrás-szolgáltató neve, amelyre ez a javaslat vonatkozik, például "MICROSOFT. számítás" |
| resourceType |Annak az erőforrásnak a neve, amelyre ez az ajánlás vonatkozik, például "MICROSOFT. számítás/virtualmachines" |
| resourceId |Azon erőforrás erőforrás-azonosítója, amelyre a javaslat vonatkozik |
| status | Mindig "aktív" |
| submissionTimestamp |Időbélyeg, ha az esemény elérhetővé válik a lekérdezéshez. |
| subscriptionId |Azure-előfizetés azonosítója. |
| properties |A `<Key, Value>` javaslatok részleteit leíró pár (azaz egy szótár).|
| Properties. recommendationSchemaVersion| A tevékenység naplójának bejegyzésében közzétett ajánlási tulajdonságok sémájának verziója |
| Properties. recommendationCategory | A javaslat kategóriája. A lehetséges értékek a "magas rendelkezésre állás", a "teljesítmény", a "biztonság" és a "Cost" |
| Properties. recommendationImpact| A javaslat hatása. A lehetséges értékek a következők: "magas", "közepes", "alacsony" |
| Properties. recommendationRisk| A javaslat kockázata. A lehetséges értékek a következők: "Error", "Warning", "None" |

## <a name="policy-category"></a>Szabályzat kategóriája

Ez a kategória a [Azure Policy](../../governance/policy/overview.md)által végrehajtott összes hatás művelet műveleteit tartalmazza. Az ebben a kategóriában látható események típusai közé tartoznak például a _naplózás_ és a _Megtagadás_. A házirend által végrehajtott összes művelet az erőforráson végzett műveletként van modellezve.

### <a name="sample-policy-event"></a>Példa házirend-eseményre

```json
{
    "authorization": {
        "action": "Microsoft.Resources/checkPolicyCompliance/read",
        "scope": "/subscriptions/<subscriptionID>"
    },
    "caller": "33a68b9d-63ce-484c-a97e-94aef4c89648",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.azure.com/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "appid": "1d78a85d-813d-46f0-b496-dd72f50a3ec0",
        "appidacr": "2",
        "http://schemas.microsoft.com/identity/claims/identityprovider": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "description": "",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Policy",
        "localizedValue": "Policy"
    },
    "eventTimestamp": "2019-01-15T13:19:56.1227642Z",
    "id": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/contososqlpolicy/events/13bbf75f-36d5-4e66-b693-725267ff21ce/ticks/636831551961227642",
    "level": "Warning",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Authorization/policies/audit/action",
        "localizedValue": "Microsoft.Authorization/policies/audit/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Sql",
        "localizedValue": "Microsoft SQL"
    },
    "resourceType": {
        "value": "Microsoft.Resources/checkPolicyCompliance",
        "localizedValue": "Microsoft.Resources/checkPolicyCompliance"
    },
    "resourceId": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/contososqlpolicy",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-01-15T13:20:17.1077672Z",
    "subscriptionId": "<subscriptionID>",
    "properties": {
        "isComplianceCheck": "True",
        "resourceLocation": "westus2",
        "ancestors": "72f988bf-86f1-41af-91ab-2d7cd011db47",
        "policies": "[{\"policyDefinitionId\":\"/subscriptions/<subscriptionID>/providers/Microsoft.
            Authorization/policyDefinitions/5775cdd5-d3d3-47bf-bc55-bb8b61746506/\",\"policyDefiniti
            onName\":\"5775cdd5-d3d3-47bf-bc55-bb8b61746506\",\"policyDefinitionEffect\":\"Deny\",\"
            policyAssignmentId\":\"/subscriptions/<subscriptionID>/providers/Microsoft.Authorization
            /policyAssignments/991a69402a6c484cb0f9b673/\",\"policyAssignmentName\":\"991a69402a6c48
            4cb0f9b673\",\"policyAssignmentScope\":\"/subscriptions/<subscriptionID>\",\"policyAssig
            nmentParameters\":{}}]"
    },
    "relatedEvents": []
}
```

### <a name="policy-event-property-descriptions"></a>Házirend-esemény tulajdonságainak leírása

| Elem neve | Description |
| --- | --- |
| engedélyezés | Az esemény Azure RBAC-tulajdonságainak tömbje. Az új erőforrások esetében ez a művelet és a kiértékelést kiváltó kérelem hatóköre. A meglévő erőforrások esetében a művelet a következő: "Microsoft. Resources/checkPolicyCompliance/Read". |
| hívó | Új erőforrások esetén a központi telepítést kezdeményező identitás. Meglévő erőforrások esetében a Microsoft Azure Policy bepillantást az RP GUID azonosító. |
| csatornák | A házirend-események csak a "művelet" csatornát használják. |
| jogcímek | Az Active Directory által használt JWT-jogkivonat hitelesíti a felhasználót vagy alkalmazást a művelet végrehajtásához a Resource Managerben. |
| correlationId | Általában egy GUID formátumú karakterlánc. A correlationId osztozó események ugyanahhoz az Über-művelethez tartoznak. |
| leírás | Ez a mező a házirend eseményeinél üres. |
| eventDataId | Egy esemény egyedi azonosítója. |
| eventName | Vagy "BeginRequest" vagy "EndRequest". A "BeginRequest" a késleltetett auditIfNotExists és deployIfNotExists-értékelések, valamint a deployIfNotExists-effektusok elindítására szolgál. Minden más művelet "EndRequest" értéket ad vissza. |
| category | Deklarálja a tevékenység naplójának eseményét a "szabályzat" kifejezésnek megfelelően. |
| eventTimestamp | Időbélyeg, ha az eseményt az Azure-szolgáltatás hozta létre, és az eseményt az eseménynek megfelelő kérelem dolgozza fel. |
| ID (Azonosító) | Az esemény egyedi azonosítója az adott erőforráson. |
| szint | Az esemény szintje. A naplózás a "figyelmeztetés" kifejezést használja, és a megtagadás a "hiba" kifejezést használja. Egy auditIfNotExists vagy deployIfNotExists hiba a súlyosságtól függően "figyelmeztetés" vagy "hiba" hozható elő. Minden más házirend-esemény a "tájékoztató" kifejezést használja. |
| operationId | Az egyetlen műveletnek megfelelő események között megosztva lévő GUID. |
| operationName | A művelet neve, és közvetlenül összefügg a házirend hatásával. |
| resourceGroupName | A kiértékelt erőforráshoz tartozó erőforráscsoport neve. |
| resourceProviderName | A kiértékelt erőforráshoz tartozó erőforrás-szolgáltató neve. |
| resourceType | Az új erőforrások esetében ez a típus kiértékelése. A meglévő erőforrások esetében a "Microsoft. Resources/checkPolicyCompliance" értéket adja vissza. |
| resourceId | A kiértékelt erőforrás erőforrás-azonosítója. |
| status | A szabályzat kiértékelési eredményének állapotát leíró karakterlánc. A legtöbb házirend-Értékelés "sikeres" értéket ad vissza, de a Megtagadás effektus a "sikertelen" értéket adja vissza. A auditIfNotExists vagy a deployIfNotExists hibája szintén "sikertelen" értéket ad vissza. |
| Részállapot | A mező üres a házirend eseményeinél. |
| submissionTimestamp | Időbélyeg, ha az esemény elérhetővé válik a lekérdezéshez. |
| subscriptionId | Azure-előfizetés azonosítója. |
| Properties. isComplianceCheck | A "false" értéket adja vissza új erőforrás telepítésekor vagy egy meglévő erőforrás Resource Manager-tulajdonságainak frissítésekor. Minden más [kiértékelési eseményindító](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers) "igaz" értéket eredményez. |
| Properties. resourceLocation | A kiértékelt erőforrás Azure-régiója. |
| tulajdonságok. ősök | A fölérendelt felügyeleti csoportok vesszővel elválasztott listája, amely a közvetlen szülőről a legtávolabbi nagyszülőre van rendezve. |
| tulajdonságok. házirendek | A szabályzat-definícióval, a hozzárendeléssel, a hatással és a paraméterekkel kapcsolatos, a házirend kiértékelését eredményező adatokat tartalmazza. |
| relatedEvents | Ez a mező a házirend eseményeinél üres. |


## <a name="schema-from-storage-account-and-event-hubs"></a>A Storage-fiók és az Event hubok sémája
Amikor az Azure-tevékenység naplóját egy Storage-fiókba vagy egy Event hubhoz viszi, az adatforrások az [erőforrás-napló sémáját](../platform/resource-logs-schema.md)követik. Az alábbi táblázat a fenti sémák tulajdonságainak hozzárendelését mutatja be az erőforrás-naplók sémába.

> [!IMPORTANT]
> A Storage-fiókba írt tevékenység-naplófájlok formátuma JSON-sorokra módosult november 1. és 2018. között. A formátum változásának részleteiért lásd: [felkészülés a formátum módosítására Azure monitor erőforrás-naplók archiválása egy Storage-fiókba](../platform/resource-logs-blob-format.md) .


| Erőforrás-naplók sémájának tulajdonsága | Műveletnapló REST API Schema tulajdonság | Jegyzetek |
| --- | --- | --- |
| time | eventTimestamp |  |
| resourceId | resourceId | a subscriptionId, a resourceType és a resourceGroupName a resourceId. |
| operationName | operationName. Value |  |
| category | Művelet nevének része | A művelet típusának feltörése – "írás"/"Törlés"/"művelet" |
| resultType | status. Value | |
| resultSignature | alállapot. érték | |
| resultDescription | leírás |  |
| durationMs | N/A | Mindig 0 |
| callerIpAddress | httpRequest. clientIpAddress |  |
| correlationId | correlationId |  |
| identity | jogcímek és engedélyezési tulajdonságok |  |
| Szint | Szint |  |
| location | N/A | Az esemény feldolgozásának helye. *Ez nem az erőforrás helye, hanem az eseményt feldolgozták. A rendszer eltávolítja ezt a tulajdonságot egy jövőbeli frissítésben.* |
| Tulajdonságok | Properties. eventProperties |  |
| Properties. eventCategory | category | Ha a Properties. eventCategory nincs jelen, a kategória a "rendszergazda" |
| Properties. eventName | eventName |  |
| Properties. operationId | operationId |  |
| Properties. eventProperties | properties |  |

A következő példa egy olyan eseményt mutat be, amely ezt a sémát használja.

``` JSON
{
    "records": [
        {
            "time": "2019-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "00000000-0000-0000-0000-000000000000",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```





## <a name="next-steps"></a>Következő lépések
* [További információ a tevékenység naplóról](../platform/platform-logs-overview.md)
* [Diagnosztikai beállítás létrehozása a műveletnapló Log Analytics munkaterületre, Azure Storage-ba vagy Event hubokba való küldéséhez](../platform/diagnostic-settings.md)
