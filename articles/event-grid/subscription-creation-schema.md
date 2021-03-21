---
title: Azure Event Grid előfizetési séma
description: Ez a cikk a Azure Event Gridval való eseményre való feliratkozás tulajdonságait ismerteti. Event Grid előfizetési séma.
ms.topic: reference
ms.date: 07/07/2020
ms.openlocfilehash: f6e33171cbba65cfeaca49ab6a8954be8bb89acb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102199951"
---
# <a name="event-grid-subscription-schema"></a>Event Grid előfizetési séma

Event Grid előfizetés létrehozásához küld egy kérést az esemény-előfizetés létrehozása művelethez. Használja az alábbi formátumot:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Ha például egy nevű erőforráscsoporthoz tartozó Storage-fiókhoz szeretne esemény-előfizetést létrehozni `examplestorage` `examplegroup` , használja a következő formátumot:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Az esemény-előfizetés nevének 3-64 karakter hosszúnak kell lennie, és csak a-z, A-Z, 0-9 és a "-" karaktereket tartalmazhat. A cikk a kérelem törzsének tulajdonságait és sémáját ismerteti.
 
## <a name="event-subscription-properties"></a>Esemény-előfizetés tulajdonságai

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| destination | object | A végpontot meghatározó objektum. |
| filter (szűrő) | object | Egy választható mező az események típusának szűréséhez. |

### <a name="destination-object"></a>célobjektum

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| endpointType | sztring | Az előfizetés végpontjának típusa (webhook/HTTP, Event hub vagy üzenetsor). | 
| endpointUrl | sztring | Az esemény-előfizetésben szereplő események céljának URL-címe. | 

### <a name="filter-object"></a>objektum szűrése

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| includedEventTypes | array | Egyezés, ha az eseményben szereplő eseménytípus pontos egyezést mutat az adott eseménytípus egyikének megadásához. Hibát jelez, ha az esemény neve nem egyezik az eseményforrás regisztrált eseménytípus nevével. Az alapértelmezett érték minden eseménytípus esetében megfelel. |
| subjectBeginsWith | sztring | Előtag – az üzenet tárgy mezőjének megfelelő szűrő. Az alapértelmezett vagy az üres karakterlánc mindennek megfelel. | 
| subjectEndsWith | sztring | Utótag – az esemény üzenet tárgy mezőjének megfelelő szűrő. Az alapértelmezett vagy az üres karakterlánc mindennek megfelel. |
| isSubjectCaseSensitive | sztring | A kis-és nagybetűk megkülönböztetését szabályozza a szűrőkhöz. |
| enableAdvancedFilteringOnArrays | boolean | Lehetővé teszi a tömbök használatát a speciális szűrési kulcsokhoz. További információ: [speciális szűrés](event-filtering.md#advanced-filtering). |


## <a name="example-subscription-schema"></a>Példa előfizetési sémára

```json
{
  "properties": {
    "destination": {
      "endpointType": "webhook",
      "properties": {
          "endpointUrl": "https://example.azurewebsites.net/api/HttpTriggerCSharp1?code=VXbGWce53l48Mt8wuotr0GPmyJ/nDT4hgdFj9DpBiRt38qqnnm5OFg=="
      }
    },
    "filter": {
      "includedEventTypes": [ "Microsoft.Storage.BlobCreated", "Microsoft.Storage.BlobDeleted" ],
      "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
      "subjectEndsWith": ".jpg",
      "isSubjectCaseSensitive ": "true"
    }
  }
}
```

## <a name="next-steps"></a>Következő lépések

* A Event Grid bemutatása: [Mi az Event Grid?](overview.md)
