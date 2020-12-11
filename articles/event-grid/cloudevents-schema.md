---
title: Azure Event Grid használata a CloudEvents-sémában lévő eseményekkel
description: Ismerteti, hogyan használható a CloudEvents séma a Azure Event Grid eseményeihez. A szolgáltatás támogatja a felhőalapú események JSON-implementációjában lévő eseményeket.
ms.topic: conceptual
ms.date: 11/10/2020
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: e13c3635da7e7a86f4fa2d31215303152167741c
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109249"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>CloudEvents v 1.0 séma használata Event Grid
Az [alapértelmezett esemény sémáján](event-schema.md)kívül Azure Event Grid natív módon támogatja a [CloudEvents v 1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) és a [http protokoll kötésének](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md)JSON-implementációjában lévő eseményeket. A [CloudEvents](https://cloudevents.io/) egy [nyílt specifikáció](https://github.com/cloudevents/spec/blob/v1.0/spec.md) az események leírásához.

A CloudEvents a felhőalapú események közzétételére és felhasználására szolgáló közös esemény sémájának használatával egyszerűsíti az együttműködési képességet. Ez a séma lehetővé teszi, hogy egységes eszközöket biztosítson, és szabványos módon irányítsa át & az események kezelését, valamint a külső esemény sémájának deszerializálásának univerzális módszereit. Közös sémával könnyebben integrálhatja a munkafolyamatokat a különböző platformokon.

A CloudEvents számos [közreműködő](https://github.com/cloudevents/spec/blob/master/community/contributors.md), például a Microsoft, a [Felhőbeli natív számítástechnikai alaprendszer](https://www.cncf.io/)segítségével építhető ki. Jelenleg 1,0-as verzióként érhető el.

Ez a cikk azt ismerteti, hogyan használható a CloudEvents séma a Event Grid használatával.


## <a name="cloudevent-schema"></a>CloudEvent séma

Íme egy példa egy Azure Blob Storage eseményre CloudEvents formátumban:

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

[Itt](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes)találja a CloudEvents 1.0-s verziójában elérhető mezők, típusok és definíciók részletes leírását.

A CloudEvents-sémában és a Event Grid sémában leszállított események fejlécének értékei ugyanazok, kivéve a következőt: `content-type` . A CloudEvents séma esetében a fejléc értéke a következő: `"content-type":"application/cloudevents+json; charset=utf-8"` . Event Grid séma esetében a fejléc értéke a következő: `"content-type":"application/json; charset=utf-8"` .

## <a name="configure-event-grid-for-cloudevents"></a>A CloudEvents Event Grid konfigurálása

A CloudEvents sémában az események bemenetére és kimenetére Event Grid is használhatja. Az alábbi táblázat a lehetséges átalakításokat ismerteti:

 Erőforrás Event Grid | Bemeneti séma       | Kézbesítési séma
|---------------------|-------------------|---------------------
| Rendszertémakörök       | Event Grid séma | Event Grid séma-vagy CloudEvent séma
| Felhasználói témakörök/tartományok | Event Grid séma | Event Grid séma
| Felhasználói témakörök/tartományok | CloudEvent séma | CloudEvent séma
| Felhasználói témakörök/tartományok | Egyéni séma     | Egyéni séma vagy Event Grid séma vagy CloudEvent séma
| PartnerTopics       | CloudEvent séma | CloudEvent séma


Az összes esemény sémája esetében a Event Grid érvényesítést igényel Event Grid témakörre való közzétételkor és esemény-előfizetés létrehozásakor.

További információ: [Event Grid biztonság és hitelesítés](security-authentication.md).

### <a name="input-schema"></a>Bemeneti séma

Egyéni témakörhöz tartozó bemeneti sémát az egyéni témakör létrehozásakor állíthatja be.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventschemav1_0
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
New-AzEventGridTopic `
  -ResourceGroupName gridResourceGroup `
  -Location westcentralus `
  -Name <topic_name> `
  -InputSchema CloudEventSchemaV1_0
```

### <a name="output-schema"></a>Kimeneti séma

A kimeneti séma az esemény-előfizetés létrehozásakor állítható be.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
topicID=$(az eventgrid topic show --name <topic-name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --source-resource-id $topicID \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventschemav1_0
```

PowerShell esetén használja az alábbi parancsot:
```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventSchemaV1_0
```

 Jelenleg nem használhat Event Grid eseményindítót egy Azure Functions alkalmazáshoz, ha az esemény a CloudEvents-sémában kerül kézbesítésre. HTTP-trigger használata. A CloudEvents sémában eseményeket fogadó HTTP-triggerek megvalósítására példákat a [CloudEvents használata a Azure functions használatával](#azure-functions)című témakörben talál.

 ## <a name="endpoint-validation-with-cloudevents-v10"></a>Végpont ellenőrzése a CloudEvents 1.0-s verziójában

Ha már ismeri a Event Gridt, érdemes lehet Event Grid végpont-ellenőrzési kézfogását a visszaélések megelőzésére. A CloudEvents 1.0-s verziójában a HTTP-beállítások módszer használatával valósítja meg a saját [visszaélés elleni védelem szemantikai](webhook-event-delivery.md) beállításait. [Itt](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection)olvashat bővebben. Ha a kimenethez a CloudEvents sémát használja, a Event Grid a Event Grid érvényesítési esemény mechanizmusa helyett az CloudEvents 1.0-s verziójának védelme szolgáltatást használja.

<a name="azure-functions"></a>

## <a name="use-with-azure-functions"></a>Használat Azure Functions

A [Azure Functions Event Grid kötés](../azure-functions/functions-bindings-event-grid.md) nem támogatja natív módon a CloudEvents, ezért a http által aktivált függvények a CloudEvents üzenetek olvasására használhatók. Ha HTTP-triggert használ a CloudEvents olvasására, a Event Grid-trigger automatikus működéséhez kódot kell írnia:

* Érvényesítési választ küld egy [előfizetés-ellenőrzési kérelemre](../event-grid/webhook-event-delivery.md).
* Egyszer meghívja a függvényt a kérelem törzsében található Event tömbben.

További információ a függvény helyi meghívásához vagy az Azure-ban való futtatásához használt URL-címről: [http-trigger kötési útmutatója – dokumentáció](../azure-functions/functions-bindings-http-webhook.md)

A következő minta C#-kód a HTTP-triggerekhez Event Grid trigger viselkedését szimulálja.  Ezt a példát a CloudEvents sémában elküldött eseményekhez használhatja.

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run([HttpTrigger(AuthorizationLevel.Anonymous, "post", "options", Route = null)]HttpRequestMessage req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");
    if (req.Method == HttpMethod.Options)
    {
        // If the request is for subscription validation, send back the validation code
        
        var response = req.CreateResponse(HttpStatusCode.OK);
        response.Headers.Add("Webhook-Allowed-Origin", "eventgrid.azure.net");

        return response;
    }

    var requestmessage = await req.Content.ReadAsStringAsync();
    var message = JToken.Parse(requestmessage);

    // The request is not for subscription validation, so it's for an event.
    // CloudEvents schema delivers one event at a time.
    log.LogInformation($"Source: {message["source"]}");
    log.LogInformation($"Time: {message["eventTime"]}");
    log.LogInformation($"Event data: {message["data"].ToString()}");

    return req.CreateResponse(HttpStatusCode.OK);
}
```

A következő minta JavaScript-kód a HTTP-triggerekhez szimulálja Event Grid trigger viselkedését. Ezt a példát a CloudEvents sémában elküldött eseményekhez használhatja.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    
    if (req.method == "OPTIONS") {
        // If the request is for subscription validation, send back the validation code
        
        context.log('Validate request received');
        context.res = { status: 200 };
        context.res.headers.append('Webhook-Allowed-Origin', 'eventgrid.azure.net');
    }
    else
    {
        var message = req.body;
        
        // The request is not for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        var event = JSON.parse(message);
        context.log('Source: ' + event.source);
        context.log('Time: ' + event.eventTime);
        context.log('Data: ' + JSON.stringify(event.data));
    }
 
    context.done();
};
```

## <a name="next-steps"></a>Következő lépések

* További információ az események kézbesítésének figyeléséről: [Event Grid üzenet kézbesítésének figyelése](monitor-event-delivery.md).
* Javasoljuk, hogy tesztelje, véleményezze és [járuljon hozzá](https://github.com/cloudevents/spec/blob/master/community/CONTRIBUTING.md) a CloudEvents.
* Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid előfizetés sémája](subscription-creation-schema.md).
