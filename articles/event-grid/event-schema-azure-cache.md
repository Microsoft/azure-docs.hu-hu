---
title: Azure cache a Redis-hez Event Grid forrásként
description: Az Azure cache szolgáltatáshoz megadott tulajdonságokat ismerteti Azure Event Grid Redis
ms.topic: conceptual
ms.date: 02/11/2021
author: curib
ms.author: cauribeg
ms.openlocfilehash: 1a2995bc9ef40cd4eab320ce1bb4c5faf61e0e6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100371277"
---
# <a name="azure-cache-for-redis-as-an-event-grid-source"></a>Azure cache a Redis Event Grid forrásaként

Ez a cikk az Azure cache Redis eseményeinek tulajdonságait és sémáját ismerteti. Az események sémáinak bemutatása: [Azure Event Grid Event Schema](event-schema.md). 

## <a name="available-event-types"></a>Elérhető események típusai
Ezek az események akkor aktiválódnak, ha az ügyfél a Redis REST API-k meghívásával exportálja, importálja vagy méretezi az Azure cache-t. A javítási eseményt a Redis Update váltja ki.

 |Esemény neve |Leírás|
 |----------|-----------|
 |**Microsoft. cache. ExportRDBCompleted** |A gyorsítótár-adatexportáláskor aktiválódik. |
 |**Microsoft. cache. ImportRDBCompleted** |A gyorsítótárban tárolt adatimportáláskor aktiválódik. |
 |**Microsoft. cache. PatchingCompleted** |A javítás befejezésekor aktiválódik. |
 |**Microsoft. cache. ScalingCompleted** |A skálázás befejezésekor aktiválódik. |

## <a name="example-event"></a>Példa eseményre
Egy esemény indításakor a Event Grid szolgáltatás adatokat küld az eseményről a végpontra való feliratkozáshoz. Ez a szakasz egy példát mutat be arra, hogy az egyes Azure cache-Redis-események milyen módon néznek ki.

# <a name="event-grid-event-schema"></a>[Event Grid-eseményséma](#tab/event-grid-event-schema)

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

# <a name="cloud-event-schema"></a>[Felhő-eseményséma](#tab/cloud-event-schema)


### <a name="microsoftcachepatchingcompleted-event"></a>Microsoft. cache. PatchingCompleted esemény

```json
[{
    "id": "9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
    "type": "Microsoft.Cache.PatchingCompleted",
    "source": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
    "data": {
        "name": "PatchingCompleted",
        "timestamp": "2020-12-09T21:50:19.9995668+00:00",
        "status": "Succeeded"
    },
    "subject": "PatchingCompleted",
    "time": "2020-12-09T21:50:19.9995668+00:00",
    "specversion": "1.0"
}]
```

### <a name="microsoftcacheimportrdbcompleted-event"></a>Microsoft. cache. ImportRDBCompleted esemény

```json
[{
    "id": "9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
    "type": "Microsoft.Cache.ImportRDBCompleted",
    "source": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
    "data": {
        "name": "ImportRDBCompleted",
        "timestamp": "2020-12-09T21:50:19.9995668+00:00",
        "status": "Succeeded"
    },
    "subject": "ImportRDBCompleted",
    "eventTime": "2020-12-09T21:50:19.9995668+00:00",
    "specversion": "1.0"
}]
```

### <a name="microsoftcacheexportrdbcompleted-event"></a>Microsoft. cache. ExportRDBCompleted esemény

```json
[{
    "id": "9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
    "type": "Microsoft.Cache.ExportRDBCompleted",
    "source": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
    "data": {
        "name": "ExportRDBCompleted",
        "timestamp": "2020-12-09T21:50:19.9995668+00:00",
        "status": "Succeeded"
    },
    "subject": "ExportRDBCompleted",
    "time": "2020-12-09T21:50:19.9995668+00:00",
    "specversion": "1.0"
}]
```

### <a name="microsoftcachescalingcompleted"></a>Microsoft. cache. ScalingCompleted

```json
[{
    "id": "9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
    "type": "Microsoft.Cache.ScalingCompleted",
    "source": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
    "data": {
        "name": "ScalingCompleted",
        "timestamp": "2020-12-09T21:50:19.9995668+00:00",
        "status": "Succeeded"
    },
    "subject": "ScalingCompleted",
    "time": "2020-12-09T21:50:19.9995668+00:00",
    "specversion": "1.0"
}]
```

---

## <a name="event-properties"></a>Esemény tulajdonságai

# <a name="event-grid-event-schema"></a>[Event Grid-eseményséma](#tab/event-grid-event-schema)

Egy esemény a következő legfelső szintű adattal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| `topic` | sztring | Az eseményforrás teljes erőforrás-elérési útja. Ez a mező nem írható. Az értéket az Event Grid adja meg. |
| `subject` | sztring | Az esemény tárgyra mutató, a közzétevő által megadott elérési út. |
| `eventType` | sztring | Az eseményforráshoz felvett eseménytípusok egyike. |
| `eventTime` | sztring | Az esemény a szolgáltató UTC-ideje alapján történő létrehozásakor. |
| `id` | sztring | Az esemény egyedi azonosítója. |
| `data` | object | Azure cache a Redis-eseményekhez. |
| `dataVersion` | sztring | Az adatobjektum sémaverziója. A sémaverziót a közzétevő határozza meg. |
| `metadataVersion` | sztring | Az esemény metaadatok sémaverziója. A legfelső szintű tulajdonságokra az Event Grid határozza meg a sémát. Az értéket az Event Grid adja meg. |


# <a name="cloud-event-schema"></a>[Felhő-eseményséma](#tab/cloud-event-schema)


Egy esemény a következő legfelső szintű adattal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| `source` | sztring | Az eseményforrás teljes erőforrás-elérési útja. Ez a mező nem írható. Az értéket az Event Grid adja meg. |
| `subject` | sztring | Az esemény tárgyra mutató, a közzétevő által megadott elérési út. |
| `type` | sztring | Az eseményforráshoz felvett eseménytípusok egyike. |
| `time` | sztring | Az esemény a szolgáltató UTC-ideje alapján történő létrehozásakor. |
| `id` | sztring | Az esemény egyedi azonosítója. |
| `data` | object | Azure cache a Redis-eseményekhez. |
| `specversion` | sztring | A CloudEvents séma specifikációjának verziója. |

---


Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| `timestamp` | sztring | Az az időpont, amikor az esemény bekövetkezett. |
| `name` | sztring | Az esemény neve. |
| `status` | sztring | Az esemény állapota. Sikertelen vagy sikeres. |

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

