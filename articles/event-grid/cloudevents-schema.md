---
title: Azure Event Grid használata a CloudEvents-sémában lévő eseményekkel
description: Ismerteti, hogyan használható a CloudEvents séma a Azure Event Grid eseményeihez. A szolgáltatás támogatja a CloudEvents JSON-implementációjában lévő eseményeket.
ms.topic: conceptual
ms.date: 11/10/2020
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 484f118791d57c082a9f4383b1af4a22c04849c4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101737902"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>CloudEvents v 1.0 séma használata Event Grid
Az [alapértelmezett esemény sémáján](event-schema.md)kívül Azure Event Grid natív módon támogatja a [CloudEvents v 1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) és a [http protokoll kötésének](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md)JSON-implementációjában lévő eseményeket. A [CloudEvents](https://cloudevents.io/) egy [nyílt specifikáció](https://github.com/cloudevents/spec/blob/v1.0/spec.md) az események leírásához.

A CloudEvents a felhőalapú események közzétételére és felhasználására szolgáló közös esemény sémájának használatával egyszerűsíti az együttműködési képességet. Ez a séma lehetővé teszi az egységes szerszámozást, az események útválasztását és kezelését, valamint a külső esemény sémájának deszerializálásának általános módjait. Közös sémával könnyebben integrálhatja a munkafolyamatokat a különböző platformokon.

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

Az elérhető mezők, a típusok és a definíciók részletes ismertetését lásd: [CloudEvents v 1.0](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes).

A CloudEvents-sémában és a Event Grid sémában leszállított események fejlécének értékei ugyanazok, kivéve a következőt: `content-type` . A CloudEvents séma esetében a fejléc értéke a következő: `"content-type":"application/cloudevents+json; charset=utf-8"` . A Event Grid séma esetében a fejléc értéke a következő: `"content-type":"application/json; charset=utf-8"` .

## <a name="configure-event-grid-for-cloudevents"></a>A CloudEvents Event Grid konfigurálása

A CloudEvents sémában az események bemenetére és kimenetére Event Grid is használhatja. Az alábbi táblázat a lehetséges átalakításokat ismerteti:

 Erőforrás Event Grid | Bemeneti séma       | Kézbesítési séma
|---------------------|-------------------|---------------------
| Rendszertémakörök       | Event Grid-séma | Event Grid séma-vagy CloudEvents séma
| Felhasználói témakörök/tartományok | Event Grid-séma | Event Grid séma-vagy CloudEvents séma
| Felhasználói témakörök/tartományok | CloudEvents séma | CloudEvents séma
| Felhasználói témakörök/tartományok | Egyéni séma     | Egyéni séma, Event Grid séma vagy CloudEvents séma
| PartnerTopics       | CloudEvents séma | CloudEvents séma

Az összes esemény sémája esetében Event Grid érvényesítésre van szükség Event Grid témakörben való közzétételkor és esemény-előfizetés létrehozásakor.

További információ: [Event Grid biztonság és hitelesítés](security-authentication.md).

### <a name="input-schema"></a>Bemeneti séma

Egyéni témakörhöz tartozó bemeneti sémát az egyéni témakör létrehozásakor állíthatja be.

Az Azure CLI esetén használja a következőt:

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

Az Azure CLI esetén használja a következőt:

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

Ha már ismeri a Event Gridt, előfordulhat, hogy a visszaélések megelőzése érdekében a végpont-ellenőrzési kézfogást ismeri. A CloudEvents 1.0-s verziójában a HTTP-beállítások módszer használatával valósítja meg a saját [visszaélés elleni védelem szemantikai](webhook-event-delivery.md) beállításait. További információ: [HTTP 1,1 Webhookok az Event Delivery – 1,0-es verzióhoz](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). Ha a kimenethez a CloudEvents sémát használja, akkor a Event Grid a Event Grid érvényesítési esemény mechanizmusa helyett az CloudEvents 1.0-s verziójának védelme funkciót használja.

<a name="azure-functions"></a>

## <a name="use-with-azure-functions"></a>Használat Azure Functions

A [Azure Functions Event Grid kötés](../azure-functions/functions-bindings-event-grid.md) nem támogatja natív módon a CloudEvents, ezért a http által aktivált függvények a CloudEvents üzenetek olvasásához használatosak. Ha HTTP-triggert használ a CloudEvents olvasására, a Event Grid-trigger automatikus működéséhez kódot kell írnia:

* Érvényesítési választ küld egy [előfizetés-ellenőrzési kérelemre](../event-grid/webhook-event-delivery.md)
* Egyszer meghívja a függvényt a kérelem törzsében szereplő Event Array elem alapján.

A függvény helyileg való meghívásához vagy az Azure-ban való futtatásához használt URL-címmel kapcsolatos információkért tekintse meg a [http-trigger kötési útmutatójának dokumentációját](../azure-functions/functions-bindings-http-webhook.md).

A következő minta C#-kód a HTTP-triggerekhez Event Grid trigger viselkedését szimulálja. Ezt a példát a CloudEvents sémában elküldött eseményekhez használhatja.

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

A következő minta JavaScript-kód a HTTP-triggerekhez szimulálja Event Grid trigger viselkedését. Ezt a példát a CloudEvents sémában elküldött eseményekhez használhatja.

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

* További információ az események kézbesítésének figyeléséről: [Event Grid üzenet kézbesítésének figyelése](monitor-event-delivery.md).
* Javasoljuk, hogy tesztelje, véleményezze és [járuljon hozzá a CloudEvents](https://github.com/cloudevents/spec/blob/master/community/CONTRIBUTING.md).
* Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid előfizetés sémája](subscription-creation-schema.md).
