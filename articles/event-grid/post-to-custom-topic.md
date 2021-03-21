---
title: Esemény közzététele egyéni Azure Event Grid témakörben
description: Ez a cikk azt ismerteti, hogyan lehet elküldeni egy eseményt egy egyéni témakörbe. Megjeleníti a bejegyzés és az esemény formátumát.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: ed126487938e524264c94544903460854ffc4d41
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98681616"
---
# <a name="post-to-custom-topic-for-azure-event-grid"></a>Közzététel a Azure Event Grid egyéni témakörében

Ez a cikk azt ismerteti, hogyan lehet elküldeni egy eseményt egy egyéni témakörbe. Megjeleníti a bejegyzés és az esemény formátumát. A [szolgáltatói szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) csak a várt formátumnak megfelelő bejegyzésekre vonatkozik.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="endpoint"></a>Végpont

Ha a HTTP-BEJEGYZÉST egyéni témakörbe küldi, használja az URI formátumot: `https://<topic-endpoint>?api-version=2018-01-01` .

Érvényes URI például a következő: `https://exampletopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01` .

Az Azure CLI-vel rendelkező egyéni témakör végpontjának beszerzéséhez használja a következőt:

```azurecli-interactive
az eventgrid topic show --name <topic-name> -g <topic-resource-group> --query "endpoint"
```

Ahhoz, hogy a végpontot a Azure PowerShell használó egyéni témakörhöz lehessen beolvasni, használja a következőt:

```powershell
(Get-AzEventGridTopic -ResourceGroupName <topic-resource-group> -Name <topic-name>).Endpoint
```

## <a name="header"></a>Fejléc

A kérelemben adjon meg egy nevű fejléc-értéket, `aeg-sas-key` amely tartalmaz egy kulcsot a hitelesítéshez.

Egy érvényes fejléc-érték például: `aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==` .

Az Azure CLI-vel való egyéni témakör kulcsának beszerzéséhez használja a következőt:

```azurecli
az eventgrid topic key list --name <topic-name> -g <topic-resource-group> --query "key1"
```

A PowerShell-lel rendelkező egyéni témakör kulcsának beszerzéséhez használja a következőt:

```powershell
(Get-AzEventGridTopicKey -ResourceGroupName <topic-resource-group> -Name <topic-name>).Key1
```

## <a name="event-data"></a>Eseményadatok

Egyéni témakörök esetén a legfelső szintű adat ugyanazokat a mezőket tartalmazza, mint a szabványos erőforrás-definíciós események. Ezen tulajdonságok egyike egy olyan adattulajdonság, amely az egyéni témakörben egyedi tulajdonságokat tartalmaz. Esemény-közzétevőként meg kell határoznia az adatobjektum tulajdonságait. Használja az alábbi sémát:

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

A tulajdonságok leírását itt tekintheti meg: [Azure Event Grid Event Schema](event-schema.md). Amikor eseményeket küld egy Event Grid-témakörbe, a tömb legfeljebb 1 MB méretű lehet. Egy esemény maximálisan megengedett mérete 1 MB is. Az 64 KB-nál nagyobb számú esemény díja 64 KB. 

Egy érvényes esemény-Adatséma például a következő:

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

## <a name="response"></a>Reagálás

A témakör-végpontra való közzététel után választ kap. A válasz egy szabványos HTTP-válasz kódja. Néhány gyakori Válasz:

|Eredmény  |Reagálás  |
|---------|---------|
|Siker  | 200 OK  |
|Az esemény adatformátuma helytelen formátumú | 400 Hibás kérés |
|Érvénytelen hozzáférési kulcs | 401 Nem engedélyezett |
|Helytelen végpont | 404 Nem található |
|Tömb vagy esemény meghaladja a méretkorlátot | 413 túl nagy a hasznos adat |

Hibák esetén az üzenet törzsének formátuma a következő:

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

## <a name="next-steps"></a>Következő lépések

* További információ az események kézbesítésének figyeléséről: [Event Grid üzenet kézbesítésének figyelése](monitor-event-delivery.md).
* További információ a hitelesítési kulcsról: [Event Grid biztonság és hitelesítés](security-authentication.md).
* Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid előfizetés sémája](subscription-creation-schema.md).
