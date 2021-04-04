---
title: Események fogadása Azure Event Gridról egy HTTP-végpontra
description: Útmutató HTTP-végpontok érvényesítéséhez, majd az események fogadásához és deszerializálásához Azure Event Grid
ms.topic: conceptual
ms.date: 11/19/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 75c80fb85d39298f1130537971bc700897c039d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023717"
---
# <a name="receive-events-to-an-http-endpoint"></a>Események fogadása HTTP-végponton

Ez a cikk azt ismerteti, hogyan lehet [érvényesíteni egy http-végpontot](webhook-event-delivery.md) egy esemény-előfizetés eseményeinek fogadására, majd az események fogadására és deszerializálására. Ez a cikk egy Azure-függvényt használ demonstrációs célokra, azonban ugyanazokat a fogalmakat alkalmazza, függetlenül attól, hogy az alkalmazás hol található.

> [!NOTE]
> **Erősen** ajánlott [Event Grid triggert](../azure-functions/functions-bindings-event-grid.md) használni, amikor egy Azure-függvényt indít el Event Grid használatával. Itt látható az általános webhook-trigger használata.

## <a name="prerequisites"></a>Előfeltételek

HTTP által aktivált függvényt igénylő Function alkalmazásra van szükség.

## <a name="add-dependencies"></a>Függőségek hozzáadása

Ha a .NET-ben fejleszt fejlesztést, [vegyen fel egy függőséget](../azure-functions/functions-reference-csharp.md#referencing-custom-assemblies) a függvényhez a `Microsoft.Azure.EventGrid` [NuGet-csomaghoz](https://www.nuget.org/packages/Microsoft.Azure.EventGrid). A cikkben szereplő példák 1.4.0 vagy újabb verziót igényelnek.

A más nyelvekhez készült SDK-k az SDK-k [közzététele](./sdk-overview.md#data-plane-sdks) hivatkozáson keresztül érhetők el. Ezek a csomagok a natív eseménytípus modelljeit (például `EventGridEvent` ,, `StorageBlobCreatedEventData` és `EventHubCaptureFileCreatedEventData` ) rendelkeznek.

Kattintson a fájlok megtekintése hivatkozásra az Azure-függvényben (az Azure functions portál jobb oldali ablaktábláján), és hozzon létre egy project.jsnevű fájlt. Adja hozzá a következő tartalmat a `project.json` fájlhoz, és mentse azt:

 ```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.Azure.EventGrid": "2.0.0"
      }
    }
   }
}
```

![NuGet-csomag hozzáadva](./media/receive-events/add-dependencies.png)

## <a name="endpoint-validation"></a>Végpont ellenőrzése

Az első dolog, amit végre szeretne hajtani, az `Microsoft.EventGrid.SubscriptionValidationEvent` eseményeket kezeli. Minden alkalommal, amikor valaki előfizet egy eseményre, Event Grid egy érvényesítési eseményt küld a végpontnak az `validationCode` adattartalomban. A végpontnak a válasz törzsében újra kell visszhangja ahhoz, hogy [a végpont érvényes legyen, és az Ön tulajdonában](webhook-event-delivery.md)legyen. Ha a webhook által aktivált függvény helyett [Event Grid triggert](../azure-functions/functions-bindings-event-grid.md) használ, a rendszer a végpont-ellenőrzést kezeli. Ha külső gyártótól származó API-szolgáltatást (például [Zapier](https://zapier.com/home) vagy [IFTTT](https://ifttt.com/)) használ, előfordulhat, hogy nem tudja programozott módon visszhangba állítani az érvényesítési kódot. Ezen szolgáltatások esetében manuálisan érvényesítheti az előfizetést egy érvényesítési URL-cím használatával, amely az előfizetés-ellenőrzési eseményben lett elküldve. Másolja az URL-címet a `validationUrl` tulajdonságba, és küldje el a Get kérelmet egy Rest-ügyfél vagy a webböngésző használatával.

A C# nyelvben a `DeserializeEventGridEvents()` függvény deszerializálja a Event Grid eseményeket. Deszerializálja az eseményeket a megfelelő típusba, például StorageBlobCreatedEventData. Használja a `Microsoft.Azure.EventGrid.EventTypes` osztályt a támogatott eseménytípus és nevek lekéréséhez.

Az érvényesítési kód programozott visszhangjának ellenőrzéséhez használja a következő kódot. A kapcsolódó mintákat [Event Grid fogyasztói példában](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/tree/master/EventGridConsumer)találhatja meg.

```csharp
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.EventGrid;
namespace Function1
{
    public static class Function1
    {
        [FunctionName("Function1")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            string response = string.Empty;
            string requestContent = await new StreamReader(req.Body).ReadToEndAsync();
            log.LogInformation($"Received events: {requestContent}");

            EventGridSubscriber eventGridSubscriber = new EventGridSubscriber();

            EventGridEvent[] eventGridEvents = eventGridSubscriber.DeserializeEventGridEvents(requestContent);

            foreach (EventGridEvent eventGridEvent in eventGridEvents)
            {
                if (eventGridEvent.Data is SubscriptionValidationEventData)
                {
                    var eventData = (SubscriptionValidationEventData)eventGridEvent.Data;
                    log.LogInformation($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
                    // Do any additional validation (as required) and then return back the below response

                    var responseData = new SubscriptionValidationResponse()
                    {
                        ValidationResponse = eventData.ValidationCode
                    };
                    return new OkObjectResult(responseData);
                }
            }
            return new OkObjectResult(response);
        }
   }
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }
    }
    context.done();
};
```

### <a name="test-validation-response"></a>Teszt-ellenőrzési válasz

Tesztelje az érvényesítési válasz függvényt úgy, hogy beillesztette a minta eseményt a függvény teszt mezőjébe:

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

Ha a Futtatás gombra kattint, a kimenetnek 200 OK és a törzsnek kell lennie `{"validationResponse":"512d38b6-c7b8-40c8-89fe-f46f9e9622b6"}` :

:::image type="content" source="./media/receive-events/validation-request.png" alt-text="Érvényesítési kérelem":::

:::image type="content" source="./media/receive-events/validation-output.png" alt-text="Érvényesítési kimenet":::

## <a name="handle-blob-storage-events"></a>BLOB Storage-események kezelése

Most pedig adjuk ki a függvényt a következő kezeléshez `Microsoft.Storage.BlobCreated` :

```cs
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.EventGrid;
using Microsoft.Azure.EventGrid.Models;

namespace FunctionApp1
{
    public static class Function1
    {
        [FunctionName("Function1")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation($"C# HTTP trigger function begun");
            string response = string.Empty;

            string requestContent = await new StreamReader(req.Body).ReadToEndAsync();
            log.LogInformation($"Received events: {requestContent}");

            EventGridSubscriber eventGridSubscriber = new EventGridSubscriber();

            EventGridEvent[] eventGridEvents = eventGridSubscriber.DeserializeEventGridEvents(requestContent);

            foreach (EventGridEvent eventGridEvent in eventGridEvents)
            {
                if (eventGridEvent.Data is SubscriptionValidationEventData)
                {
                    var eventData = (SubscriptionValidationEventData)eventGridEvent.Data;
                    log.LogInformation($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
                    // Do any additional validation (as required) and then return back the below response

                    var responseData = new SubscriptionValidationResponse()
                    {
                        ValidationResponse = eventData.ValidationCode
                    };

                    return new OkObjectResult(responseData);
                }
            }

            return new OkObjectResult(response);
        }
    }
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type  
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }
    }
    context.done();
};

```

### <a name="test-blob-created-event-handling"></a>BLOB-létrehozási események kezelésére szolgáló teszt

A függvény új funkcióinak teszteléséhez egy [blob Storage-eseményt](./event-schema-blob-storage.md#example-event) helyez el a test (teszt) mezőbe, és futtassa a következőt:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Ekkor meg kell jelennie a blob URL-kimenetének a függvény naplójában:

![Kimeneti napló](./media/receive-events/blob-event-response.png)

A teszteléshez blob Storage-fiókot vagy általános célú v2 (GPv2) Storage-fiókot, [hozzáadási és esemény-előfizetést](../storage/blobs/storage-blob-event-quickstart.md)kell létrehoznia, és a végpontot a függvény URL-címére állíthatja:

![Függvény URL-címe](./media/receive-events/function-url.png)

## <a name="handle-custom-events"></a>Egyéni események kezelése

Végül a függvényt még egyszer is kiterjesztheti, hogy az egyéni eseményeket is képes legyen kezelni. 

A C# nyelvben az SDK támogatja az esemény típusú nevek leképezését az esemény adattípusára. A `AddOrUpdateCustomEventMapping()` függvény használatával képezhető le az egyéni esemény.

Adja meg az esemény ellenőrzését `Contoso.Items.ItemReceived` . A végleges kódnak így kell kinéznie:

```cs
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.EventGrid;
using Newtonsoft.Json;

namespace FunctionApp1
{
    class ContosoItemReceivedEventData
    {
        [JsonProperty("itemSku")]
        public string ItemSku { get; set; }
    }
    public static class Function1
    {
        [FunctionName("Function1")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation($"C# HTTP trigger function begun");
            string response = string.Empty;

            string requestContent = await new StreamReader(req.Body).ReadToEndAsync();
            log.LogInformation($"Received events: {requestContent}");

            EventGridSubscriber eventGridSubscriber = new EventGridSubscriber();
            eventGridSubscriber.AddOrUpdateCustomEventMapping("Contoso.Items.ItemReceived", typeof(ContosoItemReceivedEventData));
            EventGridEvent[] eventGridEvents = eventGridSubscriber.DeserializeEventGridEvents(requestContent);

            foreach (EventGridEvent eventGridEvent in eventGridEvents)
            {
                if (eventGridEvent.Data is SubscriptionValidationEventData)
                {
                    var eventData = (SubscriptionValidationEventData)eventGridEvent.Data;
                    log.LogInformation($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
                    // Do any additional validation (as required) and then return back the below response

                    var responseData = new SubscriptionValidationResponse()
                    {
                        ValidationResponse = eventData.ValidationCode
                    };

                    return new OkObjectResult(responseData);
                }
                else if (eventGridEvent.Data is StorageBlobCreatedEventData)
                {
                    var eventData = (StorageBlobCreatedEventData)eventGridEvent.Data;
                    log.LogInformation($"Got BlobCreated event data, blob URI {eventData.Url}");
                }
                else if (eventGridEvent.Data is ContosoItemReceivedEventData)
                {
                    var eventData = (ContosoItemReceivedEventData)eventGridEvent.Data;
                    log.LogInformation($"Got ContosoItemReceived event data, item SKU {eventData.ItemSku}");
                }
            }

            return new OkObjectResult(response);
        }
    }
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";
    var customEventType = "Contoso.Items.ItemReceived";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }

        else if (body.data && body.eventType == customEventType) {
            var payload = body.data;
            context.log("Relaying received custom payload:" + JSON.stringify(payload));
        }
    }
    context.done();
};
```

### <a name="test-custom-event-handling"></a>Egyéni események felügyeletének tesztelése

Végezetül ellenőrizze, hogy a függvény mostantól képes-e kezelni az egyéni eseménytípus típusát:

```json
[{
    "subject": "Contoso/foo/bar/items",
    "eventType": "Contoso.Items.ItemReceived",
    "eventTime": "2017-08-16T01:57:26.005121Z",
    "id": "602a88ef-0001-00e6-1233-1646070610ea",
    "data": { 
            "itemSku": "Standard"
            },
    "dataVersion": "",
    "metadataVersion": "1"
}]
```

Ezt a funkciót élőben is tesztelheti, ha [Egyéni eseményt küld a portálról,](./custom-event-quickstart-portal.md) vagy [egy egyéni témakörbe](./post-to-custom-topic.md)  helyezi el egy olyan szolgáltatással vagy alkalmazással, amely egy végpontra, például [Poster](https://www.getpostman.com/)-re tehet közzé. Hozzon létre egy egyéni témakört és egy esemény-előfizetést a függvény URL-címének megfelelő végponttal.

[!INCLUDE [event-grid-message-headers](../../includes/event-grid-message-headers.md)]

## <a name="next-steps"></a>Következő lépések

* Az [SDK-k Azure Event Grid felügyeletének és közzétételének](./sdk-overview.md) megismerése
* Útmutató [Egyéni témakörhöz való közzétételhez](./post-to-custom-topic.md)
* Próbálja ki a részletes Event Grid és függvények oktatóanyagokat, például a [blob Storage-ba feltöltött rendszerképek átméretezését](resize-images-on-storage-blob-upload-event.md)
