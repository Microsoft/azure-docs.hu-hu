---
title: Egyéni Azure Event Grid-témakör esemény közzététele
description: Ismerteti, hogyan lehet egy egyéni témakört az esemény közzététele az Azure Event Gridhez
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 14ae5f2a0b6a950889d8587cd4d03ff4fc9a171b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66304215"
---
# <a name="post-to-custom-topic-for-azure-event-grid"></a>Közzététel egyéni témakörben az Azure Event Gridhez

Ez a cikk ismerteti egy egyéni témakört az esemény közzététele. Azt mutatja, hogy a post és az adatok formátumát. A [szolgáltatói szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) riasztáspéldányhoz tartoznak, amelyekre a várt formátumú.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="endpoint"></a>Végpont

Amikor a HTTP POST küld egy egyéni témakör, használja az URI-formátum: `https://<topic-endpoint>?api-version=2018-01-01`.

Ha például egy érvényes URI-ja: `https://exampletopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01`.

A végpont egy egyéni témakör az Azure CLI-vel használja:

```azurecli-interactive
az eventgrid topic show --name <topic-name> -g <topic-resource-group> --query "endpoint"
```

Egy egyéni témakört az Azure PowerShell használatával a végpont beszerzéséhez használja:

```powershell
(Get-AzEventGridTopic -ResourceGroupName <topic-resource-group> -Name <topic-name>).Endpoint
```

## <a name="header"></a>Fejléc

A kérelemben, például egy nevű fejléc értéke `aeg-sas-key` , amelyek a hitelesítési kulcsot tartalmaz.

Ha például egy érvényes fejléc értéke `aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==`.

Egy egyéni témakört az Azure CLI-vel a kulcsot használja:

```azurecli
az eventgrid topic key list --name <topic-name> -g <topic-resource-group> --query "key1"
```

Egy egyéni témakör a PowerShell-lel a kulcs lekéréséhez használja:

```powershell
(Get-AzEventGridTopicKey -ResourceGroupName <topic-resource-group> -Name <topic-name>).Key1
```

## <a name="event-data"></a>Eseményadatok

Egyéni témakörök a legfelső szintű adatok tartalmazzák a standard szintű erőforrás által definiált események, ugyanazokat a mezőket. Ezek a tulajdonságok egyike az egyéni témakörbe egyedi tulajdonságokat tartalmazó adatok tulajdonság. Esemény-közzétevő, mint az adott objektum tulajdonságait határozza meg. Használja a következő mintát követik:

```json
[
  {
    "id": string,    
    "eventType": string,
    "subject": string,
    "eventTime": string-in-date-time-format,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string
  }
]
```

Ezek a tulajdonságok leírását lásd: [Azure Event Grid-esemény séma](event-schema.md). Az eseményeket egy event grid-témakör az üzenetküldés, ha a tömb rendelkezhet egy teljes mérete legfeljebb 1 MB. A tömbben szereplő minden esemény korlátozódik 64 KB-os (nyilvánosan elérhetők) vagy 1 MB (előzetes verzió).

> [!NOTE]
> Egy esemény mérete legfeljebb 64 KB-os által általánosan elérhető (GA) szolgáltatói szerződés (SLA) vonatkozik. Támogatást biztosít az esemény mérete legfeljebb 1 MB jelenleg előzetes verzióban érhető el. Események több mint 64 KB-os 64 KB-os egységekben számoljuk. 

Például egy érvényes adatok eseménysémája van:

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2017-08-10T21:03:07+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0"
}]
```

## <a name="response"></a>Válasz

A témakör végpontra könyvelés után kapott választ. A válasz egy normál HTTP-válaszkód. Néhány gyakori válaszokat a következők:

|Eredmény  |Válasz  |
|---------|---------|
|Siker  | 200 OK  |
|Eseményadatok formátuma helytelen | 400 Hibás kérés |
|Érvénytelenek a hozzáférési kulcs | 401-es nem engedélyezett |
|Nem megfelelő végpont | 404 – Nem található |
|Tömb vagy esemény meghaladja a méretbeli korlátokat | 413 adattartalom túl nagy |

A hibákat az üzenet törzsének formátuma a következő:

```json
{
    "error": {
        "code": "<HTTP status code>",
        "message": "<description>",
        "details": [{
            "code": "<HTTP status code>",
            "message": "<description>"
    }]
  }
}
```

## <a name="next-steps"></a>További lépések

* Esemény kézbesítések figyelésével kapcsolatos további információkért lásd: [figyelő Event Grid üzenetkézbesítése](monitor-event-delivery.md).
* A hitelesítési kulcs kapcsolatos további információkért lásd: [Event Grid biztonsági és hitelesítési](security-authentication.md).
* Az Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid-előfizetés séma](subscription-creation-schema.md).
