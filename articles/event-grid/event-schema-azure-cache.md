---
title: Azure cache a Redis-hez Event Grid forrásként
description: Az Azure cache szolgáltatáshoz megadott tulajdonságokat ismerteti Azure Event Grid Redis
ms.topic: conceptual
ms.date: 12/21/2020
author: curib
ms.author: cauribeg
ms.openlocfilehash: f446f3f469a7404e6e74ba67ee24bf32578fe9d8
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055969"
---
# <a name="azure-cache-for-redis-as-an-event-grid-source"></a>Azure cache a Redis Event Grid forrásaként

Ez a cikk az Azure cache Redis eseményeinek tulajdonságait és sémáját ismerteti. Az események sémáinak bemutatása: [Azure Event Grid Event Schema](event-schema.md). 

## <a name="event-grid-event-schema"></a>Event Grid-eseményséma

### <a name="list-of-events-for-azure-cache-for-redis-rest-apis"></a>Az Azure cache Redis REST API-k eseményeinek listája

Ezek az események akkor aktiválódnak, ha az ügyfél a Redis REST API-k meghívásával exportálja, importálja vagy méretezi az Azure cache-t. A javítási eseményt a Redis Update váltja ki.

 |Esemény neve |Description|
 |----------|-----------|
 |**Microsoft. cache. ExportRDBCompleted** |A gyorsítótár-adatexportáláskor aktiválódik. |
 |**Microsoft. cache. ImportRDBCompleted** |A gyorsítótárban tárolt adatimportáláskor aktiválódik. |
 |**Microsoft. cache. PatchingCompleted** |A javítás befejezésekor aktiválódik. |
 |**Microsoft. cache. ScalingCompleted** |A skálázás befejezésekor aktiválódik. |

<a name="example-event"></a>
### <a name="the-contents-of-an-event-response"></a>Egy eseményre adott válasz tartalma

Egy esemény indításakor a Event Grid szolgáltatás adatokat küld az eseményről a végpontra való feliratkozáshoz.

Ez a szakasz egy példát mutat be arra, hogy az egyes Azure cache-Redis-események milyen módon néznek ki.

### <a name="microsoftcachepatchingcompleted-event"></a>Microsoft. cache. PatchingCompleted esemény

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.PatchingCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"PatchingCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"PatchingCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcacheimportrdbcompleted-event"></a>Microsoft. cache. ImportRDBCompleted esemény

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ImportRDBCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ImportRDBCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ImportRDBCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcacheexportrdbcompleted-event"></a>Microsoft. cache. ExportRDBCompleted esemény

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ExportRDBCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ExportRDBCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ExportRDBCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcachescalingcompleted"></a>Microsoft. cache. ScalingCompleted

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ScalingCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ScalingCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ScalingCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="event-properties"></a>Esemény tulajdonságai

Egy esemény a következő legfelső szintű adattal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| témakör | sztring | Az eseményforrás teljes erőforrás-elérési útja. Ez a mező nem írható. Az értéket az Event Grid adja meg. |
| tárgy | sztring | Az esemény tárgyra mutató, a közzétevő által megadott elérési út. |
| eventType | sztring | Az eseményforráshoz felvett eseménytípusok egyike. |
| eventTime | sztring | Az esemény a szolgáltató UTC-ideje alapján történő létrehozásakor. |
| id | sztring | Az esemény egyedi azonosítója. |
| adatok | object | Azure cache a Redis-eseményekhez. |
| dataVersion | sztring | Az adatobjektum sémaverziója. A sémaverziót a közzétevő határozza meg. |
| metadataVersion | sztring | Az esemény metaadatok sémaverziója. A legfelső szintű tulajdonságokra az Event Grid határozza meg a sémát. Az értéket az Event Grid adja meg. |

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| időbélyeg | sztring | Az az időpont, amikor az esemény bekövetkezett. |
| name | sztring | Az esemény neve. |
| status | sztring | Az esemény állapota. Sikertelen vagy sikeres. |


## <a name="quickstarts"></a>Rövid útmutatók

Ha szeretné kipróbálni az Azure cache-t a Redis eseményeihez, tekintse meg az alábbi rövid útmutatókat:

|Ha ezt az eszközt szeretné használni:    |Tekintse meg ezt a cikket: |
|--|-|
|Azure Portal    |[Gyors útmutató: az Azure cache átirányítása a Redis-események webes végpontba Azure Portal](../azure-cache-for-redis/cache-event-grid-quickstart-portal.md)|
|PowerShell    |[Gyors útmutató: az Azure cache átirányítása a Redis-események webes végponthoz a PowerShell használatával](../azure-cache-for-redis/cache-event-grid-quickstart-powershell.md)|
|Azure CLI    |[Gyors útmutató: az Azure cache átirányítása a Redis eseményeihez a webes végponthoz az Azure CLI-vel](../azure-cache-for-redis/cache-event-grid-quickstart-cli.md)|

## <a name="next-steps"></a>Következő lépések

* A Azure Event Grid bemutatása: [Mi az Event Grid?](overview.md)
* Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid előfizetés sémája](subscription-creation-schema.md).

