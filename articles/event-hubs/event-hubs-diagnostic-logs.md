---
title: Diagnosztikai naplók beállítása – Azure Event hub | Microsoft Docs
description: Ismerje meg, hogyan állíthatja be a tevékenységek naplóit és a diagnosztikai naplókat az Azure-beli Event hubokhoz.
ms.topic: article
ms.date: 10/27/2020
ms.openlocfilehash: dc5cbea31583a4e8126897b2dcda63b216438e1b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573177"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Diagnosztikai naplók beállítása az Azure-eseményközpontokhoz

Az Azure Event Hubs-hoz két típusú naplót tekinthet meg:

* **[Tevékenységnaplók](../azure-monitor/essentials/platform-logs-overview.md)**: ezek a naplók egy adott feladaton végzett műveletekkel kapcsolatos információkat tartalmaznak. A naplók mindig engedélyezve vannak. A tevékenység naplójának bejegyzései a Azure Portalban az Event hub-névtér bal oldali ablaktábláján a **műveletnapló** lehetőség kiválasztásával láthatók. Például: "névtér létrehozása vagy frissítése", "az Event hub létrehozása vagy frissítése".

    ![Event Hubs névtérhez tartozó műveletnapló](./media/event-hubs-diagnostic-logs/activity-log.png)
* **[Diagnosztikai naplók](../azure-monitor/essentials/platform-logs-overview.md)**: a diagnosztikai naplók részletes információkat biztosítanak a névtérhez az API használatával vagy a Language SDK felügyeleti ügyfelein keresztül végrehajtott műveletekről és műveletekről. 
    
    A következő szakasz bemutatja, hogyan engedélyezheti a diagnosztikai naplókat egy Event Hubs névtérhez.

## <a name="enable-diagnostic-logs"></a>Diagnosztikai naplók engedélyezése
A diagnosztikai naplók alapértelmezés szerint le vannak tiltva. A diagnosztikai naplók engedélyezéséhez kövesse az alábbi lépéseket:

1.  A [Azure Portal](https://portal.azure.com)navigáljon a Event Hubs-névtérhez. 
2. A bal oldali ablaktábla **figyelés** területén válassza a **diagnosztikai beállítások** lehetőséget, majd válassza a **+ diagnosztikai beállítás hozzáadása** elemet. 

    ![Diagnosztikai beállítások lap – diagnosztikai beállítás hozzáadása](./media/event-hubs-diagnostic-logs/diagnostic-settings-page.png)
4. A **Kategória részletei** szakaszban válassza ki az engedélyezni kívánt **diagnosztikai naplók típusait** . Ezekről a kategóriákról a jelen cikk későbbi részében talál részleteket. 
5. A **cél részletei** szakaszban állítsa be a kívánt archiválási célt (célhelyet); például egy Storage-fiók, egy Event hub vagy egy Log Analytics munkaterület.

    ![Diagnosztikai beállítások hozzáadása lap](./media/event-hubs-diagnostic-logs/aDD-diagnostic-settings-page.png)
6.  A diagnosztikai beállítások mentéséhez válassza az eszköztár **Mentés** elemét.

    Az új beállítások körülbelül 10 percen belül lépnek érvénybe. Ezt követően a naplók megjelennek a konfigurált archiválási célhelyen a **diagnosztikai naplók** panelen.

    A diagnosztika konfigurálásával kapcsolatos további információkért tekintse meg az [Azure diagnosztikai naplók áttekintése](../azure-monitor/essentials/platform-logs-overview.md)című témakört.

## <a name="diagnostic-logs-categories"></a>Diagnosztikai naplók kategóriái

Event Hubs a következő kategóriákhoz tartozó diagnosztikai naplókat rögzíti:

| Kategória | Leírás | 
| -------- | ----------- | 
| Archiválási naplók | Adatokat rögzít [Event Hubs rögzítési](event-hubs-capture-overview.md) műveletekről, pontosabban a rögzítési hibákkal kapcsolatos naplókat. |
| Operatív naplók | Rögzítse az Azure Event Hubs-névtéren végrehajtott összes felügyeleti műveletet. Az adatműveletek nem kerülnek rögzítésre, mert az Azure Event Hubson végrehajtott nagy mennyiségű adatművelet miatt. |
| Naplók automatikus méretezése | A Event Hubs névtérben végzett automatikus kikapcsolású műveletek rögzítése. |
| Kafka-koordinátor naplói | A Event Hubs kapcsolódó Kafka-koordinátori műveleteket rögzíti. |
| Kafka felhasználói hibák naplói | Az Event Hubs-on meghívott Kafka API-k adatait rögzíti. |
| Event Hubs Virtual Network (VNet) kapcsolati esemény | Az IP-címekre és a virtuális hálózatokra vonatkozó adatokat rögzíti, amelyek a Event Hubs felé irányuló forgalmat küldenek. |
| Ügyfél által felügyelt kulcsfontosságú felhasználói naplók | Az ügyfél által felügyelt kulcshoz kapcsolódó műveletek rögzítése. |


Az összes napló JavaScript Object Notation (JSON) formátumban van tárolva. Minden bejegyzés tartalmaz egy karakterlánc-mezőt, amely a következő szakaszokban ismertetett formátumot használja.

## <a name="archive-logs-schema"></a>Archiválási naplók sémája

Az Archive log JSON-karakterláncok az alábbi táblázatban felsorolt elemeket tartalmazzák:

Név | Leírás
------- | -------
`TaskName` | A sikertelen feladat leírása
`ActivityId` | A nyomon követéshez használt belső azonosító
`trackingId` | A nyomon követéshez használt belső azonosító
`resourceId` | Erőforrás-azonosító Azure Resource Manager
`eventHub` | Event hub teljes neve (tartalmazza a névtér nevét)
`partitionId` | Az Event hub-partíció írása folyamatban
`archiveStep` | lehetséges értékek: ArchiveFlushWriter, DestinationInit
`startTime` | Sikertelen kezdési idő
`failures` | A hiba előfordulási idejének száma
`durationInSeconds` | A hiba időtartama
`message` | Hibaüzenet
`category` | ArchiveLogs

A következő kód egy példa egy archivált log JSON-karakterláncra:

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "000000000-0000-0000-0000-0000000000000",
   "trackingId": "0000000-0000-0000-0000-00000000000000000",
   "resourceId": "/SUBSCRIPTIONS/000000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs Namespace Name>",
   "eventHub": "<Event Hub full name>",
   "partitionId": "1",
   "archiveStep": "ArchiveFlushWriter",
   "startTime": "9/22/2016 5:11:21 AM",
   "failures": 3,
   "durationInSeconds": 360,
   "message": "Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (404) Not Found. ---> System.Net.WebException: The remote server returned an error: (404) Not Found.\r\n   at Microsoft.WindowsAzure.Storage.Shared.Protocol.HttpResponseParsers.ProcessExpectedStatusCodeNoException[T](HttpStatusCode expectedStatusCode, HttpStatusCode actualStatusCode, T retVal, StorageCommandBase`1 cmd, Exception ex)\r\n   at Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob.<PutBlockImpl>b__3e(RESTCommand`1 cmd, HttpWebResponse resp, Exception ex, OperationContext ctx)\r\n   at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndGetResponse[T](IAsyncResult getResponseResult)\r\n   --- End of inner exception stack trace ---\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.StorageAsyncResult`1.End()\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.AsyncExtensions.<>c__DisplayClass4.<CreateCallbackVoid>b__3(IAsyncResult ar)\r\n--- End of stack trace from previous location where exception was thrown ---\r\n   at System.",
   "category": "ArchiveLogs"
}
```

## <a name="operational-logs-schema"></a>Operatív naplók sémája

Az operatív napló JSON-karakterláncai az alábbi táblázatban felsorolt elemeket tartalmazzák:

Név | Leírás
------- | -------
`ActivityId` | Belső azonosító, követési célokra használatos |
`EventName` | A művelet neve. Az elem értékeinek listáját az [események nevei](#event-names) részben tekintheti meg. |
`resourceId` | Erőforrás-azonosító Azure Resource Manager |
`SubscriptionId` | Előfizetés azonosítója |
`EventTimeString` | Működési idő |
`EventProperties` |A művelet tulajdonságai. Ez az elem további információkat nyújt az eseményről az alábbi példában látható módon. |
`Status` | Művelet állapota. Az érték lehet **sikeres** vagy **sikertelen**.  |
`Caller` | A művelet hívója (Azure Portal vagy felügyeleti ügyfél) |
`Category` | OperationalLogs |

A következő kód példa egy operatív napló JSON-karakterláncára:

```json
Example:
{
   "ActivityId": "00000000-0000-0000-0000-00000000000000",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs namespace name>",
   "SubscriptionId": "000000000-0000-0000-0000-000000000000",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"0000000000-0000-0000-0000-000000000000\",\"Namespace\":\"<Namespace Name>\",\"Via\":\"https://<Namespace Name>.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

### <a name="event-names"></a>Események nevei
Az esemény neve a művelet típusa + erőforrástípus a következő enumerálások alapján. Például:, `Create Queue` , `Retrieve Event Hu` vagy `Delete Rule` . 

| Művelettípus | Erőforrás típusa | 
| -------------- | ------------- | 
| <ul><li>Létrehozás</li><li>Frissítés</li><li>Törlés</li><li>Beolvasni</li><li>Ismeretlen</li></ul> | <ul><li>Névtér</li><li>Üzenetsor</li><li>Témakör</li><li>Előfizetés</li><li>EventHub</li><li>EventHubSubscription</li><li>NotificationHub</li><li>NotificationHubTier</li><li>SharedAccessPolicy</li><li>UsageCredit</li><li>NamespacePnsCredentials</li>Szabály</li>ConsumerGroup</li> |

## <a name="autoscale-logs-schema"></a>Naplók autoskálázása séma
Az autoscale log JSON az alábbi táblázatban felsorolt elemeket tartalmazza:

| Név | Leírás |
| ---- | ----------- | 
| `TrackingId` | Belső azonosító, amely nyomkövetési célokra szolgál |
| `ResourceId` | Azure Resource Manager erőforrás-azonosító. |
| `Message` | Tájékoztató üzenet, amely részletesen ismerteti az automatikus feltöltés műveleteit. Az üzenet tartalmazza az adott névtér átviteli egységének előző és aktuális értékét, valamint azt, hogy mi indította el a TU-t. |

Íme egy példa az autoscale eseményre: 

```json
{
    "TrackingId": "fb1b3676-bb2d-4b17-85b7-be1c7aa1967e",
    "Message": "Scaled-up EventHub TUs (UpdateStartTimeUTC: 5/13/2020 7:48:36 AM, PreviousValue: 1, UpdatedThroughputUnitValue: 2, AutoScaleReason: 'IncomingMessagesPerSecond reached 2170')",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name"
}
```

## <a name="kafka-coordinator-logs-schema"></a>A Kafka-koordinátor naplói sémája
A Kafka-koordinátor log JSON a következő táblázatban felsorolt elemeket tartalmazza:

| Név | Leírás |
| ---- | ----------- | 
| `RequestId` | A kérelem azonosítója, amely nyomkövetési célokra szolgál |
| `ResourceId` | Erőforrás-azonosító Azure Resource Manager |
| `Operation` | A csoport koordinálásakor végzett művelet neve |
| `ClientId` | Ügyfél-azonosító |
| `NamespaceName` | Névtér neve | 
| `SubscriptionId` | Azure-előfizetés azonosítója |
| `Message` | Tájékoztató vagy figyelmeztető üzenet, amely részletesen ismerteti a csoportos koordináció során végrehajtott műveleteket. |

### <a name="example"></a>Példa

```json
{
    "RequestId": "FE01001A89E30B020000000304620E2A_KafkaExampleConsumer#0",
    "Operation": "Join.Start",
    "ClientId": "KafkaExampleConsumer#0",
    "Message": "Start join group for new member namespace-name:c:$default:I:KafkaExampleConsumer#0-cc40856f7f3c4607915a571efe994e82, current group size: 0, API version: 2, session timeout: 10000ms, rebalance timeout: 300000ms.",
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "KafkaCoordinatorLogs"
}
```

## <a name="kafka-user-error-logs-schema"></a>Kafka felhasználói hiba naplóinak sémája
A Kafka felhasználói hibanapló JSON a következő táblázatban felsorolt elemeket tartalmazza:

| Név | Leírás |
| ---- | ----------- |
| `TrackingId` | Nyomkövetési azonosító, amely nyomkövetési célokra szolgál. |
| `NamespaceName` | Névtér neve |
| `Eventhub` | Event Hubs neve |
| `PartitionId` | Partícióazonosító |
| `GroupId` | Csoportazonosító |
| `ClientId` | Ügyfél-azonosító |
| `ResourceId` | Azure Resource Manager erőforrás-azonosító. |
| `Message` | Tájékoztató üzenet, amely a hibával kapcsolatos részleteket tartalmaz |

## <a name="event-hubs-virtual-network-connection-event-schema"></a>Event Hubs virtuális hálózati kapcsolati esemény sémája
Event Hubs Virtual Network (VNet) kapcsolati esemény JSON az alábbi táblázatban felsorolt elemeket tartalmazza:

| Név | Leírás |
| ---  | ----------- | 
| `SubscriptionId` | Azure-előfizetés azonosítója |
| `NamespaceName` | Névtér neve |
| `IPAddress` | Az Event Hubs szolgáltatáshoz csatlakozó ügyfél IP-címe |
| `Action` | A Event Hubs szolgáltatás által a kapcsolódási kérelmek kiértékelése során végzett művelet. A támogatott műveletek **elfogadják a kapcsolatokat** , és **megtagadják a kapcsolatokat**. |
| `Reason` | A művelet elvárt okát adja meg |
| `Count` | Az adott művelet előfordulásainak száma |
| `ResourceId` | Azure Resource Manager erőforrás-azonosító. |

A virtuális hálózati naplók csak akkor jönnek létre, ha a névtér engedélyezi a hozzáférést a **kiválasztott hálózatokról** vagy **adott IP-címekről** (IP-szűrési szabályok). Ha nem szeretné korlátozni a névtérhez való hozzáférést ezekkel a funkciókkal, és továbbra is szeretné lekérni a virtuális hálózati naplókat a Event Hubs névtérhez csatlakozó ügyfelek IP-címeinek nyomon követéséhez, a következő megkerülő megoldást használhatja. Engedélyezze az IP-szűrést, és adja hozzá a teljes címezhető IPv4-tartományt (1.0.0.0/1-255.0.0.0/1). A Event Hubs nem támogatja az IPv6-tartományokat. 

### <a name="example"></a>Példa

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

## <a name="customer-managed-key-user-logs"></a>Ügyfél által felügyelt kulcsfontosságú felhasználói naplók
Az ügyfél által felügyelt kulcs felhasználói napló JSON a következő táblázatban felsorolt elemeket tartalmazza:

| Név | Leírás |
| ---- | ----------- | 
| `Category` | Az üzenet kategóriájának típusa A következő értékek egyike: **hiba** és **információ** |
| `ResourceId` | Belső erőforrás-azonosító, amely tartalmazza az Azure-előfizetés AZONOSÍTÓját és a névtér nevét |
| `KeyVault` | A Key Vault erőforrás neve |
| `Key` | A Key Vault kulcs neve. |
| `Version` | A Key Vault kulcs verziója |
| `Operation` | A kérelmek kiszolgálására tett művelet neve |
| `Code` | Állapotkód |
| `Message` | Üzenet, amely egy hiba vagy tájékoztató üzenet részleteit tartalmazza |



## <a name="next-steps"></a>Következő lépések
- [Bevezetés a Event Hubsba](./event-hubs-about.md)
- [Event Hubs minták](sdks.md)
- Bevezetés az Event Hubs használatába
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-java-get-started-send.md)
