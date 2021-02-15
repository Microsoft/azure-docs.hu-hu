---
title: Azure Container Registry Event Grid forrásként
description: A Container Registry eseményekhez megadott tulajdonságokat ismerteti Azure Event Grid
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 7e19b223e43f30a532c1cd8bdc86f9a29220f5aa
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100363576"
---
# <a name="azure-container-registry-as-an-event-grid-source"></a>Azure Container Registry Event Grid forrásként

Ez a cikk a Container Registry eseményeinek tulajdonságait és sémáját ismerteti. Az események sémáinak bemutatása: [Azure Event Grid Event Schema](event-schema.md).

## <a name="available-event-types"></a>Elérhető események típusai

Azure Container Registry a következő típusú eseményeket bocsátja ki:

| Eseménytípus | Leírás |
| ---------- | ----------- |
| Microsoft. ContainerRegistry. ImagePushed | Egy rendszerkép leküldésekor következik be. |
| Microsoft. ContainerRegistry. ImageDeleted | Rendszerkép törlésekor következik be. |
| Microsoft. ContainerRegistry. ChartPushed | Egy Helm-diagram leküldésekor következik be. |
| Microsoft. ContainerRegistry. ChartDeleted | Egy Helm-diagram törlésekor következik be. |

## <a name="example-event"></a>Példa eseményre

# <a name="event-grid-event-schema"></a>[Event Grid-eseményséma](#tab/event-grid-event-schema)

Az alábbi példa egy leküldhető esemény sémáját mutatja be: 

```json
[{
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld:v1",
  "eventType": "ImagePushed",
  "eventTime": "2018-04-25T21:39:47.6549614Z",
  "data": {
    "id": "31c51664-e5bd-416a-a5df-e5206bc47ed0",
    "timestamp": "2018-04-25T21:39:47.276585742Z",
    "action": "push",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 3023,
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "length": 3023,
      "repository": "aci-helloworld",
      "tag": "v1"
    },
    "request": {
      "id": "7c66f28b-de19-40a4-821c-6f5f6c0003a4",
      "host": "demo.azurecr.io",
      "method": "PUT",
      "useragent": "docker/18.03.0-ce go/go1.9.4 git-commit/0520e24 os/windows arch/amd64 UpstreamClient(Docker-Client/18.03.0-ce \\\\(windows\\\\))"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

A rendszerkép törölt eseményének sémája hasonló:

```json
[{
  "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld",
  "eventType": "ImageDeleted",
  "eventTime": "2018-04-26T17:56:01.8211268Z",
  "data": {
    "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
    "timestamp": "2018-04-26T17:56:00.996603117Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "repository": "aci-helloworld"
    },
    "request": {
      "id": "aeda5b99-4197-409f-b8a8-ff539edb7de2",
      "host": "demo.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

A diagram leküldve eseményének sémája hasonlít a rendszerképbe beküldött események sémájához, de nem tartalmaz kérelem objektumot:

```json
[{
  "id": "ea3a9c28-5b17-40f6-a500-3f02b6829277",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "mychart:1.0.0",
  "eventType": "Microsoft.ContainerRegistry.ChartPushed",
  "eventTime": "2019-03-12T22:16:31.5164086Z",
  "data": {
    "id":"ea3a9c28-5b17-40f6-a500-3f02b682927",
    "timestamp":"2019-03-12T22:16:31.0087496+00:00",
    "action":"chart_push",
    "target":{
      "mediaType":"application/vnd.acr.helm.chart",
      "size":25265,
      "digest":"sha256:7f060075264b5ba7c14c23672698152ae6a3ebac1c47916e4efe19cd624d5fab",
      "repository":"repo",
      "tag":"mychart-1.0.0.tgz",
      "name":"mychart",
      "version":"1.0.0"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

A diagram törölt eseményének sémája hasonlít a rendszerképben törölt esemény sémájához, de nem tartalmaz kérelem objektumot:

```json
[{
  "id": "39136b3a-1a7e-416f-a09e-5c85d5402fca",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "mychart:1.0.0",
  "eventType": "Microsoft.ContainerRegistry.ChartDeleted",
  "eventTime": "019-03-12T22:42:08.7034064Z",
  "data": {
    "id":"ea3a9c28-5b17-40f6-a500-3f02b682927",
    "timestamp":"2019-03-12T22:42:08.3783775+00:00",
    "action":"chart_delete",
    "target":{
      "mediaType":"application/vnd.acr.helm.chart",
      "size":25265,
      "digest":"sha256:7f060075264b5ba7c14c23672698152ae6a3ebac1c47916e4efe19cd624d5fab",
      "repository":"repo",
      "tag":"mychart-1.0.0.tgz",
      "name":"mychart",
      "version":"1.0.0"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

# <a name="cloud-event-schema"></a>[Felhő-eseményséma](#tab/cloud-event-schema)

Az alábbi példa egy leküldhető esemény sémáját mutatja be: 

```json
[{
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "source": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld:v1",
  "type": "ImagePushed",
  "time": "2018-04-25T21:39:47.6549614Z",
  "data": {
    "id": "31c51664-e5bd-416a-a5df-e5206bc47ed0",
    "timestamp": "2018-04-25T21:39:47.276585742Z",
    "action": "push",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 3023,
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "length": 3023,
      "repository": "aci-helloworld",
      "tag": "v1"
    },
    "request": {
      "id": "7c66f28b-de19-40a4-821c-6f5f6c0003a4",
      "host": "demo.azurecr.io",
      "method": "PUT",
      "useragent": "docker/18.03.0-ce go/go1.9.4 git-commit/0520e24 os/windows arch/amd64 UpstreamClient(Docker-Client/18.03.0-ce \\\\(windows\\\\))"
    }
  },
  "specversion": "1.0"
}]
```

A rendszerkép törölt eseményének sémája hasonló:

```json
[{
  "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
  "source": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld",
  "type": "ImageDeleted",
  "time": "2018-04-26T17:56:01.8211268Z",
  "data": {
    "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
    "timestamp": "2018-04-26T17:56:00.996603117Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "repository": "aci-helloworld"
    },
    "request": {
      "id": "aeda5b99-4197-409f-b8a8-ff539edb7de2",
      "host": "demo.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  },
  "specversion": "1.0"
}]
```

A diagram leküldve eseményének sémája hasonlít a rendszerképbe beküldött események sémájához, de nem tartalmaz kérelem objektumot:

```json
[{
  "id": "ea3a9c28-5b17-40f6-a500-3f02b6829277",
  "source": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "mychart:1.0.0",
  "type": "Microsoft.ContainerRegistry.ChartPushed",
  "time": "2019-03-12T22:16:31.5164086Z",
  "data": {
    "id":"ea3a9c28-5b17-40f6-a500-3f02b682927",
    "timestamp":"2019-03-12T22:16:31.0087496+00:00",
    "action":"chart_push",
    "target":{
      "mediaType":"application/vnd.acr.helm.chart",
      "size":25265,
      "digest":"sha256:7f060075264b5ba7c14c23672698152ae6a3ebac1c47916e4efe19cd624d5fab",
      "repository":"repo",
      "tag":"mychart-1.0.0.tgz",
      "name":"mychart",
      "version":"1.0.0"
    }
  },
  "specversion": "1.0"
}]
```

A diagram törölt eseményének sémája hasonlít a rendszerképben törölt esemény sémájához, de nem tartalmaz kérelem objektumot:

```json
[{
  "id": "39136b3a-1a7e-416f-a09e-5c85d5402fca",
  "source": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "mychart:1.0.0",
  "type": "Microsoft.ContainerRegistry.ChartDeleted",
  "time": "019-03-12T22:42:08.7034064Z",
  "data": {
    "id":"ea3a9c28-5b17-40f6-a500-3f02b682927",
    "timestamp":"2019-03-12T22:42:08.3783775+00:00",
    "action":"chart_delete",
    "target":{
      "mediaType":"application/vnd.acr.helm.chart",
      "size":25265,
      "digest":"sha256:7f060075264b5ba7c14c23672698152ae6a3ebac1c47916e4efe19cd624d5fab",
      "repository":"repo",
      "tag":"mychart-1.0.0.tgz",
      "name":"mychart",
      "version":"1.0.0"
    }
  },
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
| `data` | object | BLOB Storage-események |
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
| `data` | object | BLOB Storage-események |
| `specversion` | sztring | A CloudEvents séma specifikációjának verziója. |

---

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| `id` | sztring | Az eseményazonosító. |
| `timestamp` | sztring | Az az időpont, amikor az esemény bekövetkezett. |
| `action` | sztring | A megadott eseményt magában foglaló művelet. |
| `target` | object | Az esemény célja. |
| `request` | object | Az eseményt létrehozó kérelem. |


A célobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| `mediaType` | sztring | A hivatkozott objektum MIME-típusa. |
| `size` | egész szám | A tartalom bájtjainak száma. Ugyanaz, mint a Length mező. |
| `digest` | sztring | A tartalom kivonata, amelyet a Registry v2 HTTP API-specifikáció határoz meg. |
| `length` | egész szám | A tartalom bájtjainak száma. Ugyanaz, mint a size mező. |
| `repository` | sztring | A tárház neve. |
| `tag` | sztring | A címke neve. |
| `name` | sztring | A diagram neve. |
| `version` | sztring | A diagram verziója. |

A kérelem objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| `id` | sztring | Az eseményt kezdeményező kérelem azonosítója. |
| `addr` | sztring | Az eseményt kezdeményező ügyfél-kapcsolat IP-címe vagy állomásneve, illetve esetleg portja. Ez az érték a szabványos HTTP-kérelem RemoteAddr. |
| `host` | sztring | A beállításjegyzék-példány külsőleg elérhető állomásneve, amelyet a HTTP-állomásfejléc a bejövő kérelmekben megadott. |
| `method` | sztring | Az eseményt létrehozó kérelem metódusa. |
| `useragent` | sztring | A kérelem felhasználói ügynökének fejléce. |

## <a name="tutorials-and-how-tos"></a>Oktatóanyagok és útmutatók
|Cím |Leírás  |
|---------|---------|
| [Gyors útmutató: tárolói beállításjegyzékbeli események küldése](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Bemutatja, hogyan küldhet Container Registry eseményeket az Azure CLI használatával. |


## <a name="next-steps"></a>Következő lépések

* A Azure Event Grid bemutatása: [Mi az Event Grid?](overview.md)
* Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid előfizetés sémája](subscription-creation-schema.md).
