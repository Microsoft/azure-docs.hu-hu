---
title: Azure-alkalmazás konfigurálása Event Grid forrásként
description: Ez a cikk azt ismerteti, hogyan használható az Azure app Configuration Event Grid-eseményforrásként. Ez biztosítja a sémát és az oktatóanyagra és útmutatókra mutató hivatkozásokat.
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: a64c6fead5e6d95ba11bc98d7e9a52e3021c3be2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100366772"
---
# <a name="azure-app-configuration-as-an-event-grid-source"></a>Azure-alkalmazás konfigurálása Event Grid forrásként
Ez a cikk az Azure-alkalmazások konfigurációs eseményeinek tulajdonságait és sémáját ismerteti. Az események sémáinak bemutatása: [Azure Event Grid Event Schema](event-schema.md). Emellett a gyors indulások és oktatóanyagok listáját is tartalmazza, amelyekkel az Azure-alkalmazások konfigurációját használhatja az esemény forrásaként.

## <a name="available-event-types"></a>Elérhető események típusai

Az Azure-alkalmazások konfigurálása a következő típusú eseményeket bocsátja ki:

| Eseménytípus | Leírás |
| ---------- | ----------- |
| Microsoft. AppConfiguration. KeyValueModified | Kulcs-érték létrehozásakor vagy cseréjekor következik be. |
| Microsoft. AppConfiguration. KeyValueDeleted | Kulcs-érték törlésekor következik be. |

## <a name="example-event"></a>Példa eseményre

# <a name="event-grid-event-schema"></a>[Event Grid-eseményséma](#tab/event-grid-event-schema)
A következő példa egy kulcs-érték módosítási esemény sémáját mutatja be: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

A kulcs-érték törölt esemény sémája hasonló: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueDeleted",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```
# <a name="cloud-event-schema"></a>[Felhő-eseményséma](#tab/cloud-event-schema)

A következő példa egy kulcs-érték módosítási esemény sémáját mutatja be: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "source": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "type": "Microsoft.AppConfiguration.KeyValueModified",
  "time": "2019-05-31T20:05:03Z",
  "specversion": "1.0"
}]
```

A kulcs-érték törölt esemény sémája hasonló: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "source": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "type": "Microsoft.AppConfiguration.KeyValueDeleted",
  "time": "2019-05-31T20:05:03Z",
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
| `data` | object | Az alkalmazás konfigurációs eseményeinek adatkészlete. |
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
| `data` | object | Az alkalmazás konfigurációs eseményeinek adatkészlete. |
| `specversion` | sztring | A CloudEvents séma specifikációjának verziója. |

---

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| `key` | sztring | A módosított vagy törölt kulcs-érték kulcsa. |
| `label` | sztring | A módosított vagy törölt kulcs-érték címkéje (ha van). |
| `etag` | sztring | Az `KeyValueModified` új kulcs-érték ETAG. A `KeyValueDeleted` törölt kulcs-érték ETAG. |


## <a name="tutorials-and-how-tos"></a>Oktatóanyagok és útmutatók

|Cím | Leírás |
|---------|---------|
| [Az Azure-alkalmazás konfigurációs eseményeire való reagálás Event Grid használatával](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Az Azure-alkalmazások konfigurációjának Event Gridsal való integrálásának áttekintése. |
| [Adatmódosítási értesítések Event Grid használata](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Ismerje meg, hogy az Azure-alkalmazás konfigurációs esemény-előfizetéseit használva hogyan küldhet kulcs-érték módosítási eseményeket egy webes végpontra. |

## <a name="next-steps"></a>Következő lépések

* A Azure Event Grid bemutatása: [Mi az Event Grid?](overview.md)
* Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid előfizetés sémája](subscription-creation-schema.md).
* Az Azure-alkalmazás konfigurációs eseményeivel kapcsolatos tudnivalókat lásd: [Event Grid használata adatváltozási értesítésekhez](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json). 