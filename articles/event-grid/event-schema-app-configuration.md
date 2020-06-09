---
title: Azure-alkalmazás konfigurálása Event Grid forrásként
description: Ez a cikk azt ismerteti, hogyan használható az Azure app Configuration Event Grid-eseményforrásként. Ez biztosítja a sémát és az oktatóanyagra és útmutatókra mutató hivatkozásokat.
services: event-grid
author: femila
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: femila
ms.openlocfilehash: e233b5d27df3e25c2d7c1464aea9a1e80dfbffb0
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84553149"
---
# <a name="azure-app-configuration-as-an-event-grid-source"></a>Azure-alkalmazás konfigurálása Event Grid forrásként
Ez a cikk az Azure-alkalmazások konfigurációs eseményeinek tulajdonságait és sémáját ismerteti. Az események sémáinak bemutatása: [Azure Event Grid Event Schema](event-schema.md). Emellett a gyors indulások és oktatóanyagok listáját is tartalmazza, amelyekkel az Azure-alkalmazások konfigurációját használhatja az esemény forrásaként.

## <a name="event-grid-event-schema"></a>Event Grid-eseményséma

### <a name="available-event-types"></a>Elérhető események típusai

Az Azure-alkalmazások konfigurálása a következő típusú eseményeket bocsátja ki:

| Eseménytípus | Leírás |
| ---------- | ----------- |
| Microsoft. AppConfiguration. KeyValueModified | Kulcs-érték létrehozásakor vagy cseréjekor következik be. |
| Microsoft. AppConfiguration. KeyValueDeleted | Kulcs-érték törlésekor következik be. |

### <a name="example-event"></a>Példa eseményre

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
 
### <a name="event-properties"></a>Esemény tulajdonságai

Egy esemény a következő legfelső szintű adattal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| témakör | sztring | Az eseményforrás teljes erőforrás-elérési útja. Ez a mező nem írható. Az értéket az Event Grid adja meg. |
| tulajdonos | sztring | Az esemény tárgyra mutató, a közzétevő által megadott elérési út. |
| eventType | sztring | Az eseményforráshoz felvett eseménytípusok egyike. |
| eventTime | sztring | Az esemény a szolgáltató UTC-ideje alapján történő létrehozásakor. |
| ID | sztring | Az esemény egyedi azonosítója. |
| data | objektum | Az alkalmazás konfigurációs eseményeinek adatkészlete. |
| dataVersion | sztring | Az adatobjektum sémaverziója. A sémaverziót a közzétevő határozza meg. |
| metadataVersion | sztring | Az esemény metaadatok sémaverziója. A legfelső szintű tulajdonságokra az Event Grid határozza meg a sémát. Az értéket az Event Grid adja meg. |

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| key | sztring | A módosított vagy törölt kulcs-érték kulcsa. |
| címke | sztring | A módosított vagy törölt kulcs-érték címkéje (ha van). |
| ETAG | sztring | Az `KeyValueModified` új kulcs-érték ETAG. A `KeyValueDeleted` törölt kulcs-érték ETAG. |

## <a name="tutorials-and-how-tos"></a>Oktatóanyagok és útmutatók

|Cím | Leírás |
|---------|---------|
| [Az Azure-alkalmazás konfigurációs eseményeire való reagálás Event Grid használatával](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Az Azure-alkalmazások konfigurációjának Event Gridsal való integrálásának áttekintése. |
| [Gyors útmutató: Azure-alkalmazás konfigurációs eseményeinek átirányítása egyéni webes végpontra az Azure CLI-vel](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Bemutatja, hogyan küldhet Azure-alkalmazások konfigurációs eseményeit webhookba az Azure CLI használatával. |

## <a name="next-steps"></a>Következő lépések

* A Azure Event Grid bemutatása: [Mi az Event Grid?](overview.md)
* Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid előfizetés sémája](subscription-creation-schema.md).
* Az Azure-alkalmazások konfigurációs eseményeinek használatának bemutatása: az [Azure-alkalmazás konfigurációs eseményeinek irányítása – Azure CLI](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json). 