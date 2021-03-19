---
title: Webhook-esemény kézbesítése
description: Ez a cikk a webhookok használatakor a webhook-esemény kézbesítését és a végpontok érvényesítését ismerteti.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: e9a52d0cb3e4e880d91e1b748d97ef3041298930
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87461238"
---
# <a name="webhook-event-delivery"></a>Webhook-esemény kézbesítése
A webhookok egyike a Azure Event Grid események fogadásának számos módja. Ha egy új esemény elkészült, Event Grid a szolgáltatás HTTP-kérelmet küld a konfigurált végpontnak a kérelem törzsében lévő eseménnyel.

A webhookokat támogató számos más szolgáltatáshoz hasonlóan a Event Grid megköveteli, hogy igazolja a webhook-végpont tulajdonosát, mielőtt megkezdi az események továbbítását a végpontnak. Ez a követelmény megakadályozza, hogy egy rosszindulatú felhasználó elárasztsa a végpontot az eseményekkel. Ha az alább felsorolt három Azure-szolgáltatás bármelyikét használja, az Azure-infrastruktúra automatikusan kezeli ezt az ellenőrzést:

- Azure Logic Apps [Event Grid-összekötővel](/connectors/azureeventgrid/)
- Azure Automation [webhookon](../event-grid/ensure-tags-exists-on-new-virtual-machines.md) keresztül
- Azure Functions [Event Grid triggerrel](../azure-functions/functions-bindings-event-grid.md)

## <a name="endpoint-validation-with-event-grid-events"></a>Végpontok ellenőrzése Event Grid eseményekkel
Ha bármilyen más típusú végpontot használ, például egy HTTP-triggeren alapuló Azure-függvényt, a végponti kódnak a Event Grid használatával kell részt vennie egy érvényesítési kézfogásban. Event Grid az előfizetés érvényesítésének két módját támogatja.

1. **Szinkron kézfogás**: az esemény-előfizetés létrehozásakor Event Grid elküld egy előfizetés-ellenőrzési eseményt a végpontnak. Az esemény sémája hasonló a többi Event Grid eseményhez. Az esemény adatrésze tartalmaz egy `validationCode` tulajdonságot. Az alkalmazás ellenőrzi, hogy az érvényesítési kérelem a várt esemény-előfizetésre van-e kiválasztva, és a válaszban szereplő érvényesítési kódot adja vissza szinkron módon. Ez a kézfogási mechanizmus minden Event Grid verzióban támogatott.

2. **Aszinkron kézfogás**: bizonyos esetekben nem lehet szinkron módon visszaadni a ValidationCode. Ha például harmadik féltől származó szolgáltatást (például [`Zapier`](https://zapier.com) vagy [IFTTT](https://ifttt.com/)) használ, az érvényesítési kóddal nem lehet programozott módon válaszolni.

   Az 2018-05-01-es verziótól kezdődően a Event Grid támogatja a manuális ellenőrzési kézfogást. Ha olyan SDK-val vagy eszközzel hoz létre egy esemény-előfizetést, amely az 2018-05-01-es vagy újabb API-verziót használja, akkor Event Grid egy `validationUrl` tulajdonságot küld az előfizetés-ellenőrzési esemény adatrészében. A kézfogás elvégzéséhez keresse meg az adott URL-címet az eseményekhez, és tegye meg a GET-kérést. Használhatja a REST-ügyfelet vagy a webböngészőt is.

   A megadott URL-cím **5 percig** érvényes. Ebben az időszakban az esemény-előfizetés kiépítési állapota a következő: `AwaitingManualAction` . Ha nem hajtja végre a manuális ellenőrzést 5 percen belül, a kiépítési állapot értéke: `Failed` . A manuális ellenőrzés megkezdése előtt újra létre kell hoznia az esemény-előfizetést.

   Ehhez a hitelesítési mechanizmushoz a webhook-végpontnak egy 200-es HTTP-állapotkódot kell visszaadnia, hogy az tudja, hogy az érvényesítési eseményre vonatkozó bejegyzés el lett fogadva, mielőtt a manuális érvényesítés módba kerül. Más szóval, ha a végpont 200-as értéket ad vissza, de nem ad vissza egy érvényesítési válasz szinkron módon történő visszaadását, a mód a manuális ellenőrzési módba kerül. Ha 5 percen belül lekéri az érvényesítési URL-címet, az ellenőrzési kézfogás sikeresnek tekintendő.

> [!NOTE]
> Önaláírt tanúsítványok használata nem támogatott az érvényesítéshez. Ehelyett használjon egy aláírt tanúsítványt egy kereskedelmi hitelesítésszolgáltatótól (CA).

### <a name="validation-details"></a>Érvényesítés részletei

- Az esemény-előfizetés létrehozása/frissítése során Event Grid egy előfizetés-ellenőrzési eseményt könyvel a célként megadott végpontra.
- Az esemény egy "AEG-Event-Type: SubscriptionValidation" fejléc-értéket tartalmaz.
- Az esemény törzsének ugyanaz a sémája, mint a többi Event Grid eseménynek.
- Az esemény eventType tulajdonsága `Microsoft.EventGrid.SubscriptionValidationEvent` .
- Az esemény adattulajdonsága egy `validationCode` véletlenszerűen generált karakterlánccal rendelkező tulajdonságot tartalmaz. Például: "validationCode: acb13...".
- Az eseményhez tartozik egy `validationUrl` tulajdonság is, amely egy URL-címet tartalmaz az előfizetés manuális érvényesítéséhez.
- A tömb csak az érvényesítési eseményt tartalmazza. A többi eseményt külön kérelemben küldi el a rendszer az érvényesítési kód visszhangjának visszalépése után.
- A EventGrid Adatsík SDK-k az előfizetés-ellenőrzési esemény és az előfizetés-ellenőrzési válasznak megfelelő osztályokkal rendelkeznek.

A következő példában egy SubscriptionValidationEvent látható:

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
      "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=512d38b6-c7b8-40c8-89fe-f46f9e9622b6&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1A1A1A1A"
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2018-01-25T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

A végpont tulajdonjogának bizonyításához ECHO a validationResponse tulajdonságban található érvényesítési kódot az alábbi példában látható módon adja vissza:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Egy HTTP 200 OK-válasz állapotkódot kell visszaadnia. A HTTP 202 elfogadva nem ismerhető fel érvényes Event Grid előfizetés-ellenőrzési válaszként. A http-kérelemnek 30 másodpercen belül el kell végeznie. Ha a művelet 30 másodpercen belül nem fejeződik be, akkor a rendszer megszakítja a műveletet, és 5 másodperc elteltével újra próbálkozik. Ha az összes próbálkozás sikertelen, akkor az érvényesítési kézfogási hibaként lesz kezelve.

Vagy manuálisan is érvényesítheti az előfizetést egy GET kérelem küldésével az érvényesítési URL-címre. Az esemény-előfizetés függő állapotban marad mindaddig, amíg az érvényesítés be nem fejeződik. Az érvényesítési URL-cím a 553-es portot használja. Ha a tűzfalszabályok letiltják a 553-es portot, akkor előfordulhat, hogy a rendszernek frissítenie kell a sikeres manuális kézfogásra vonatkozó szabályokat.

Az előfizetés-ellenőrzési kézfogás kezelésére példát a [C#-minta](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs)című témakörben talál.

## <a name="endpoint-validation-with-cloudevents-v10"></a>Végpont ellenőrzése a CloudEvents 1.0-s verziójában
Ha már ismeri a Event Gridt, érdemes lehet Event Grid végpont-ellenőrzési kézfogását a visszaélések megelőzésére. A CloudEvents 1.0-s verziójában a HTTP-beállítások módszer használatával valósítja meg a saját [visszaélés elleni védelem szemantikai](webhook-event-delivery.md) beállításait. [Itt](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection)olvashat bővebben. Ha a kimenethez a CloudEvents sémát használja, a Event Grid a Event Grid érvényesítési esemény mechanizmusa helyett az CloudEvents 1.0-s verziójának védelme szolgáltatást használja.

## <a name="next-steps"></a>Következő lépések
A következő cikkből megtudhatja, hogyan lehet elhárítani az esemény-előfizetés érvényességét: 

[Az esemény-előfizetés érvényességi hibáinak megoldása](troubleshoot-subscription-validation.md)
