---
title: Azure Event Grid – előfizetés-ellenőrzés hibaelhárítása
description: Ez a cikk bemutatja, hogyan lehet elhárítani az előfizetés érvényességét.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 857760182675d5673a3b09495c2faaf7372a4164
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94592940"
---
# <a name="troubleshoot-azure-event-grid-subscription-validations"></a>Azure Event Grid előfizetés érvényességének hibáinak megoldása
Ha az esemény-előfizetés létrehozása során hibaüzenetet kap (például `The attempt to validate the provided endpoint https://your-endpoint-here failed. For more details, visit https://aka.ms/esvalidation` ), az azt jelzi, hogy hiba történt az érvényesítési kézfogásban. A hiba megoldásához ellenőrizze a következő szempontokat:

- Végezzen HTTP-BEJEGYZÉST a webhook URL-címére a Poster vagy a curl vagy hasonló eszköz használatával egy [minta SubscriptionValidationEvent](webhook-event-delivery.md#validation-details) -kérelem Törzsével.
- Ha a webhook szinkron ellenőrzési kézfogási mechanizmust valósít meg, ellenőrizze, hogy a ValidationCode a válasz részeként adja-e vissza.
- Ha a webhook aszinkron ellenőrzési kézfogási mechanizmust valósít meg, ellenőrizze, hogy a HTTP-bejegyzés 200 OK-e.
- Ha a webhook visszaadja `403 (Forbidden)` a választ, ellenőrizze, hogy a webhook egy Azure Application Gateway vagy webalkalmazási tűzfal mögött van-e. Ha igen, akkor le kell tiltania ezeket a tűzfalszabályok, és végre kell hajtania a HTTP-KÖZZÉTÉTELt:
    - 920300 (a kérelemből hiányzik egy Accept fejléc)
    - 942430 (korlátozott SQL-karakteres anomáliák észlelése (argumentumok): a speciális karakterek száma túllépve (12)
    - 920230 (több URL-kódolás észlelhető)
    - 942130 (SQL injection támadás: az SQL-tautológia észlelve.)
    - 931130 (lehetséges távoli fájlok felvételének (RFI) támadása = off-domain Reference/link)

> [!IMPORTANT]
> A webhookok végpont-ellenőrzésével kapcsolatos részletes információkért lásd: [webhook-esemény kézbesítése](webhook-event-delivery.md).

A következő részben bemutatjuk, hogyan lehet érvényesíteni egy esemény-előfizetést a Poster és a curl használatával.  

## <a name="validate-event-grid-event-subscription-using-postman"></a>Event Grid esemény-előfizetés ellenőrzése a Poster használatával
Íme egy példa a Poster használatára egy Event Grid esemény webhook-előfizetésének érvényesítéséhez: 

![Event Grid esemény-előfizetés érvényesítése a Poster használatával](./media/troubleshoot-subscription-validation/event-subscription-validation-postman.png)

Íme egy példa a **SubscriptionValidationEvent** JSON-ra:

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2018-01-25T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

A minta sikeres Válasz:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Ha többet szeretne megtudni a webhookok Event Grid esemény-ellenőrzéséről, tekintse meg a [végpont-ellenőrzés az Event Grid-eseményekkel](webhook-event-delivery.md#endpoint-validation-with-event-grid-events)című témakört.


## <a name="validate-event-grid-event-subscription-using-curl"></a>Event Grid esemény-előfizetés ellenőrzése a curl használatával 
Itt látható a minta curl-parancs egy Event Grid esemény webhook-előfizetésének érvényesítéséhez: 

```bash
curl -X POST -d '[{"id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66","topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx","subject": "","data": {"validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"},"eventType": "Microsoft.EventGrid.SubscriptionValidationEvent","eventTime": "2018-01-25T22:12:19.4556811Z", "metadataVersion": "1","dataVersion": "1"}]' -H 'Content-Type: application/json' https://{your-webhook-url.com}
```

## <a name="validate-cloud-event-subscription-using-postman"></a>Felhőalapú esemény-előfizetés ellenőrzése a Poster használatával
Íme egy példa arra, hogyan használható a Poster egy felhőalapú esemény webhook-előfizetésének érvényesítéséhez: 

![Felhőalapú esemény-előfizetés érvényesítése a Poster használatával](./media/troubleshoot-subscription-validation/cloud-event-subscription-validation-postman.png)

A **http-beállítások** módszert használja a Felhőbeli eseményekkel való ellenőrzéshez. Ha többet szeretne megtudni a webhookok Felhőbeli eseményeinek ellenőrzéséről, tekintse meg a [végpont-ellenőrzés Felhőbeli eseményekkel](webhook-event-delivery.md#endpoint-validation-with-event-grid-events)című témakört.

## <a name="troubleshoot-event-subscription-validation"></a>Az esemény-előfizetés ellenőrzésének hibája

## <a name="next-steps"></a>Következő lépések
Ha további segítségre van szüksége, tegye fel a problémát a [stack overflow fórumba](https://stackoverflow.com/questions/tagged/azure-eventgrid) , vagy nyisson meg egy [támogatási jegyet](https://azure.microsoft.com/support/options/). 
