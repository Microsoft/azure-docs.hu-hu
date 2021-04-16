---
title: Az Azure Event Grid használata a CloudEvents-sémában
description: Leírja, hogyan használható a CloudEvents-séma az Azure Event Grid. A szolgáltatás támogatja a CloudEvents JSON-implementációja eseményeit.
ms.topic: conceptual
ms.date: 11/10/2020
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 0ee816663a385601d4a31edbf87f8c787ea5aa91
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389502"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>CloudEvents v1.0-séma használata Event Grid
Az alapértelmezett [](event-schema.md)eseménysémán kívül a Azure Event Grid natív módon támogatja a [CloudEvents v1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) JSON-implementációban és a [HTTP-protokollkötésben található eseményeket.](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md) [A CloudEvents](https://cloudevents.io/) egy [nyílt specifikáció az](https://github.com/cloudevents/spec/blob/v1.0/spec.md) eseményadatok leírására.

A CloudEvents leegyszerűsíti az együttműködést azáltal, hogy közös eseménysémát biztosít a felhőalapú események közzétételéhez és fogyasztáshoz. Ez a séma lehetővé teszi az egységes eszközök, az események útválasztásának és kezelésének szabványos módjait, valamint a külső eseménysémák deszerializálásának univerzális módjait. Egy közös sémával könnyebben integrálhatja a munkát a platformok között.

A CloudEventst számos közreműködő, köztük [a](https://github.com/cloudevents/spec/blob/master/community/contributors.md)Microsoft is építi a [Cloud Native Computing Foundation segítségével.](https://www.cncf.io/) Jelenleg 1.0-ás verzióként érhető el.

Ez a cikk bemutatja, hogyan használható a CloudEvents-séma Event Grid.

## <a name="cloudevent-schema"></a>CloudEvent-séma

Példa egy CloudEvents Azure Blob Storage eseményre:

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

Az elérhető mezők, azok típusai és definíciói részletes leírását lásd: [CloudEvents v1.0](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes).

A CloudEvents sémában és a Event Grid sémában kézbesített események fejlécértékei a kivételével `content-type` azonosak. A CloudEvents séma fejlécértéke `"content-type":"application/cloudevents+json; charset=utf-8"` . A Event Grid a fejléc értéke `"content-type":"application/json; charset=utf-8"` .

## <a name="configure-event-grid-for-cloudevents"></a>A Event Grid CloudEvents szolgáltatáshoz

A cloudevents Event Grid események bemenetéhez és kimenetéhez is használhatja az adatokat. Az alábbi táblázat a lehetséges átalakításokat ismerteti:

 Event Grid erőforrás | Bemeneti séma       | Kézbesítési séma
|---------------------|-------------------|---------------------
| Rendszerrel kapcsolatos témakörök       | Event Grid-séma | Event Grid vagy CloudEvents-séma
| Egyéni témakörök/tartományok | Event Grid-séma | Event Grid vagy CloudEvents-séma
| Egyéni témakörök/tartományok | CloudEvents-séma | CloudEvents-séma
| Egyéni témakörök/tartományok | Egyéni séma     | Egyéni séma, Event Grid vagy CloudEvents-séma
| Partnertémakörök       | CloudEvents-séma | CloudEvents-séma

Az összes eseménysémához a Event Grid ellenőrzést igényel, amikor egy Event Grid-témakörben teszi közzé, és amikor esemény-előfizetést hoz létre.

További információ: Event Grid [és hitelesítés.](security-authentication.md)

### <a name="input-schema"></a>Bemeneti séma

Az egyéni témakör létrehozásakor be kell állítania egy egyéni témakör bemeneti sémáját.

Az Azure CLI-hez használja a következőt:

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

A kimeneti sémát az esemény-előfizetés létrehozásakor állíthatja be.

Az Azure CLI-hez használja a következőt:

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

 Jelenleg nem használhat eseményindítót Event Grid alkalmazáshoz Azure Functions amikor az esemény a CloudEvents-sémában lesz kézbesítve. HASZNÁLJON HTTP-eseményindítót. A CloudEvents-sémában eseményeket fogadó HTTP-eseményindítók megvalósításának példáiért lásd: [Using CloudEvents with Azure Functions](#azure-functions).

## <a name="endpoint-validation-with-cloudevents-v10"></a>Végpontérvényesítés a CloudEvents v1.0-val

Ha már ismeri a Event Grid, előfordulhat, hogy ismeri a végpontérvényesítési kézfogást a visszaélések megelőzése érdekében. A CloudEvents v1.0 a HTTP OPTIONS metódussal valósítja meg a saját visszaélés elleni védelmi [szemantikáját.](webhook-event-delivery.md) További információkért lásd: [HTTP 1.1 Web Hooks for event delivery - Version 1.0 (HTTP 1.1 web hookok eseményk kézbesítéshez – 1.0-s verzió).](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection) Ha a CloudEvents sémát használja a kimenethez, a Event Grid a CloudEvents 1.0-s vagy Event Grid elleni védelemmel.

<a name="azure-functions"></a>

## <a name="use-with-azure-functions"></a>Használat Azure Functions

A [Azure Functions Event Grid kötés](../azure-functions/functions-bindings-event-grid.md) nem támogatja natív módon a CloudEvents-eseményeket, így a HTTP által aktivált függvények a CloudEvents-üzenetek olvasására használhatók. Ha HTTP-eseményindítót használ a CloudEvents beolvassa, kódot kell írnia a Event Grid eseményindító által automatikusan:

* Érvényesítési választ küld egy [előfizetés-érvényesítési kérelemre](../event-grid/webhook-event-delivery.md)
* Egyszer hívja meg a függvényt a kérelem törzsében található eseménytömb elemenként

A függvény helyi vagy Az Azure-ban való futtatásakor használt URL-címről további információt a HTTP-eseményindító kötési referenciadokumentációjában [talál.](../azure-functions/functions-bindings-http-webhook.md)

A HTTP-eseményindító következő C#-kódmintája az eseményindító Event Grid szimulálja. Ezt a példát a CloudEvents sémában kézbesített eseményekhez használhatja.

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

    // The request isn't for subscription validation, so it's for an event.
    // CloudEvents schema delivers one event at a time.
    log.LogInformation($"Source: {message["source"]}");
    log.LogInformation($"Time: {message["eventTime"]}");
    log.LogInformation($"Event data: {message["data"].ToString()}");

    return req.CreateResponse(HttpStatusCode.OK);
}
```

A HTTP-eseményindítók következő JavaScript-kódmintája az eseményindító Event Grid szimulálja. Ezt a példát a CloudEvents sémában kézbesített eseményekhez használhatja.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    
    if (req.method == "OPTIONS") {
        // If the request is for subscription validation, send back the validation code
        
        context.log('Validate request received');
        context.res = {
            status: 200,
            headers: {
                'Webhook-Allowed-Origin': 'eventgrid.azure.net',
            },
         };
    }
    else
    {
        var message = req.body;
        
        // The request isn't for subscription validation, so it's for an event.
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

* További információ az események kézbesítésének figyelésével kapcsolatban: [Monitorozás Event Grid üzenetek kézbesítése.](monitor-event-delivery.md)
* Javasoljuk, hogy tesztelje a CloudEvents-et, megjegyzésekkel járuljon hozzá és [járuljon hozzá.](https://github.com/cloudevents/spec/blob/master/community/CONTRIBUTING.md)
* További információ a Azure Event Grid létrehozásáról: Event Grid [előfizetési séma.](subscription-creation-schema.md)
