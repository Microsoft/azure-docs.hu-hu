---
title: Azure Functions Azure Event Grid trigger
description: Megtudhatja, hogyan futtathat programkódot, ha a Azure Functions Event Grid eseményeit elküldi a rendszer.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: devx-track-csharp, fasttrack-edit, devx-track-python
ms.openlocfilehash: 886db905008af94b66a902cc551e4d55b36572a8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98250128"
---
# <a name="azure-event-grid-trigger-for-azure-functions"></a>Azure Functions Azure Event Grid trigger

A függvény eseményindítójának használatával válaszolhat egy Event Grid témakörbe küldött eseményre.

További információ a telepítésről és a konfigurációról: [Áttekintés](./functions-bindings-event-grid.md).

## <a name="example"></a>Példa

# <a name="c"></a>[C#](#tab/csharp)

HTTP-trigger esetén például az [események fogadása http-végpontra](../event-grid/receive-events.md)című témakörben talál további információt.

### <a name="c-2x-and-higher"></a>C# (2. x vagy újabb)

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely a következőhöz kötődik `EventGridEvent` :

```cs
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTest")]
        public static void EventGridTest([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

További információ: csomagok, [attribútumok](#attributes-and-annotations), [konfiguráció](#configuration)és [használat](#usage).

### <a name="version-1x"></a>1. x verzió

Az alábbi példa egy 1. x [C# függvényt](functions-dotnet-class-library.md) mutat be, amely a következőhöz kötődik `JObject` :

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Microsoft.Extensions.Logging;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTriggerCSharp")]
        public static void Run([EventGridTrigger]JObject eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
        }
    }
}
```

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

Az alábbi példa egy trigger kötést mutat be egy *function.jsa* fájlban, és egy [C# parancsfájl-függvényt](functions-reference-csharp.md) , amely a kötést használja.

A *function.js* fájlban található kötési adatfájlok:

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

### <a name="version-2x-and-higher"></a>2. x vagy újabb verzió

Íme egy példa, amely a következőhöz kötődik `EventGridEvent` :

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(EventGridEvent eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.Data.ToString());
}
```

További információ: csomagok, [attribútumok](#attributes-and-annotations), [konfiguráció](#configuration)és [használat](#usage).

### <a name="version-1x"></a>1. x verzió

Az itt található functions 1. x C# parancsfájl kód, amely a következőhöz kötődik `JObject` :

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

# <a name="java"></a>[Java](#tab/java)

Ez a szakasz a következő példákat tartalmazza:

* [Event Grid trigger, karakterlánc-paraméter](#event-grid-trigger-string-parameter)
* [Event Grid trigger, POJO paraméter](#event-grid-trigger-pojo-parameter)

Az alábbi példák olyan eseményindító-kötést mutatnak be a [javában](functions-reference-java.md) , amely a kötést használja, és kinyomtat egy eseményt, és először a POJO kapja meg az eseményt `String` .

### <a name="event-grid-trigger-string-parameter"></a>Event Grid trigger, karakterlánc-paraméter

```java
  @FunctionName("eventGridMonitorString")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    String content, 
    final ExecutionContext context) {
      context.getLogger().info("Event content: " + content);      
  }
```

### <a name="event-grid-trigger-pojo-parameter"></a>Event Grid trigger, POJO paraméter

Ez a példa a következő POJO használja, amely egy Event Grid esemény legfelső szintű tulajdonságait jelöli:

```java
import java.util.Date;
import java.util.Map;

public class EventSchema {

  public String topic;
  public String subject;
  public String eventType;
  public Date eventTime;
  public String id;
  public String dataVersion;
  public String metadataVersion;
  public Map<String, Object> data;

}
```

Érkezéskor az esemény JSON-tartalma deszerializálható a ```EventSchema``` POJO a függvény általi használatra. Ez a folyamat lehetővé teszi, hogy a függvény objektumorientált módon hozzáférhessen az esemény tulajdonságaihoz.

```java
  @FunctionName("eventGridMonitor")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    EventSchema event, 
    final ExecutionContext context) {
      context.getLogger().info("Event content: ");
      context.getLogger().info("Subject: " + event.subject);
      context.getLogger().info("Time: " + event.eventTime); // automatically converted to Date by the runtime
      context.getLogger().info("Id: " + event.id);
      context.getLogger().info("Data: " + event.data);
  }
```

A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja a `EventGridTrigger` jegyzeteket azon paramétereknél, amelyek értéke a EventGrid származik. Az ezekkel a megjegyzésekkel rendelkező paraméterek a függvény futását okozzák, amikor egy esemény érkezik.  Ez a jegyzet natív Java-típusokkal, Szerializálói vagy NULL értékű értékekkel használható a használatával `Optional<T>` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Az alábbi példa egy trigger kötést mutat be egy *function.jsa* fájlban, és egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja.

A *function.js* fájlban található kötési adatfájlok:

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Itt látható a JavaScript-kód:

```javascript
module.exports = function (context, eventGridEvent) {
    context.log("JavaScript Event Grid function processed a request.");
    context.log("Subject: " + eventGridEvent.subject);
    context.log("Time: " + eventGridEvent.eventTime);
    context.log("Data: " + JSON.stringify(eventGridEvent.data));
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Az alábbi példa bemutatja, hogyan konfigurálhat egy Event Grid trigger kötést a fájlban található *function.js* .

```powershell
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ]
}
```

Az Event Grid eseményt egy nevű paraméterrel elérhetővé teheti a függvény számára `eventGridEvent` , ahogy az alábbi PowerShell-példában is látható.

```powershell
param($eventGridEvent, $TriggerMetadata)

# Make sure to pass hashtables to Out-String so they're logged correctly
$eventGridEvent | Out-String | Write-Host
```

# <a name="python"></a>[Python](#tab/python)

Az alábbi példa egy trigger kötést mutat be egy *function.jsa* fájlban, és egy [Python-függvényt](functions-reference-python.md) , amely a kötést használja.

A *function.js* fájlban található kötési adatfájlok:

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "event",
      "direction": "in"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Itt látható a Python-kód:

```python
import json
import logging

import azure.functions as func

def main(event: func.EventGridEvent):

    result = json.dumps({
        'id': event.id,
        'data': event.get_json(),
        'topic': event.topic,
        'subject': event.subject,
        'event_type': event.event_type,
    })

    logging.info('Python EventGrid trigger processed an event: %s', result)
```

---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C#](#tab/csharp)

A [C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [EventGridTrigger](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/TriggerBinding/EventGridTriggerAttribute.cs) attribútumot.

Itt egy `EventGridTrigger` attribútum szerepel a metódus-aláírásban:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, ILogger log)
{
    ...
}
```

A teljes példa: C# példa.

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A C# parancsfájl nem támogatja az attribútumokat.

# <a name="java"></a>[Java](#tab/java)

A [EventGridTrigger](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/EventGridTrigger.java) jegyzet lehetővé teszi a Event Grid kötések deklaratív konfigurálását a konfigurációs értékek megadásával. További részletekért tekintse meg a [példa](#example) és a [konfigurációs](#configuration) szakaszt.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A PowerShell nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a *function.js* fájlon beállított kötési konfigurációs tulajdonságait ismerteti. Nincsenek beállítva konstruktor-paraméterek vagy tulajdonságok az `EventGridTrigger` attribútumban.

|function.jsa tulajdonságon |Description|
|---------|---------|
| **típusa** | Kötelező – a következőre kell beállítani: `eventGridTrigger` . |
| **irányba** | Kötelező – a következőre kell beállítani: `in` . |
| **név** | Kötelező – a függvény kódjában használt változó neve az esemény-adatfogadási paraméterhez. |

## <a name="usage"></a>Használat

# <a name="c"></a>[C#](#tab/csharp)

Azure Functions 1. x esetén a következő paramétereket használhatja a Event Grid triggerhez:

* `JObject`
* `string`

Azure Functions 2. x vagy újabb verzióban a következő paraméter-típust is használhatja a Event Grid triggerhez:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`-Az összes eseménytípus közös mezőinek tulajdonságait határozza meg.

> [!NOTE]
> Ha a functions v1-re próbál kötni `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent` , a fordító egy "elavult" üzenetet jelenít meg, és azt tanácsolja, hogy használja `Microsoft.Azure.EventGrid.Models.EventGridEvent` helyette. Az újabb típus használatához hivatkozzon a [Microsoft. Azure. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet-csomagra, és teljes mértékben minősítse a `EventGridEvent` típus nevét a következővel: `Microsoft.Azure.EventGrid.Models` .

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

Azure Functions 1. x esetén a következő paramétereket használhatja a Event Grid triggerhez:

* `JObject`
* `string`

Azure Functions 2. x vagy újabb verzióban a következő paraméter-típust is használhatja a Event Grid triggerhez:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`-Az összes eseménytípus közös mezőinek tulajdonságait határozza meg.

> [!NOTE]
> Ha a functions v1-re próbál kötni `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent` , a fordító egy "elavult" üzenetet jelenít meg, és azt tanácsolja, hogy használja `Microsoft.Azure.EventGrid.Models.EventGridEvent` helyette. Az újabb típus használatához hivatkozzon a [Microsoft. Azure. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet-csomagra, és teljes mértékben minősítse a `EventGridEvent` típus nevét a következővel: `Microsoft.Azure.EventGrid.Models` . További információ a NuGet-csomagok egy C# parancsfájl-függvényben való hivatkozásáról: [NuGet-csomagok használata](functions-reference-csharp.md#using-nuget-packages)

# <a name="java"></a>[Java](#tab/java)

Az Event Grid esemény a (z) attribútumhoz társított paraméterrel érhető el `EventGridTrigger` `EventSchema` . További részletekért tekintse meg a [példát](#example) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A Event Grid-példány a fájl tulajdonságában található *function.js* konfigurált paraméterrel érhető el `name` .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A Event Grid-példány a fájl tulajdonságában található *function.js* konfigurált paraméterrel érhető el `name` .

# <a name="python"></a>[Python](#tab/python)

A Event Grid-példány a fájl tulajdonságában megadott *function.js* konfigurált paraméterrel érhető el `name` `func.EventGridEvent` .

---

## <a name="event-schema"></a>Eseményséma

Egy Event Grid eseményhez tartozó adatmennyiség egy HTTP-kérelem törzsében JSON-objektumként érkezik. A JSON a következő példához hasonlóan néz ki:

```json
[{
  "topic": "/subscriptions/{subscriptionid}/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstore",
  "subject": "/blobServices/default/containers/{containername}/blobs/blobname.jpg",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2018-01-23T17:02:19.6069787Z",
  "id": "{guid}",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "{guid}",
    "requestId": "{guid}",
    "eTag": "0x8D562831044DDD0",
    "contentType": "application/octet-stream",
    "contentLength": 2248,
    "blobType": "BlockBlob",
    "url": "https://egblobstore.blob.core.windows.net/{containername}/blobname.jpg",
    "sequencer": "000000000000272D000000000003D60F",
    "storageDiagnostics": {
      "batchId": "{guid}"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

A megjelenített példa egy elem tömbje. Event Grid mindig egy tömböt küld, és több eseményt is küldhet a tömbben. A futtatókörnyezet minden egyes tömb elemnél egyszer meghívja a függvényt.

Az Event JSON-adattípusok legfelső szintű tulajdonságai megegyeznek az összes eseménytípus között, míg a `data` tulajdonság tartalma minden eseménytípus esetében egyedi. A példa egy blob Storage-eseményre mutat.

A Common és az Event-specifikus tulajdonságok magyarázatát az Event Grid [dokumentációjában találja.](../event-grid/event-schema.md#event-properties)

A `EventGridEvent` típus csak a legfelső szintű tulajdonságokat definiálja, a `Data` tulajdonság a `JObject` .

## <a name="create-a-subscription"></a>Előfizetés létrehozása

Event Grid HTTP-kérelmek fogadásának megkezdéséhez hozzon létre egy Event Grid-előfizetést, amely megadja a függvényt meghívó végpont URL-címét.

### <a name="azure-portal"></a>Azure Portal

Az Event Grid triggerrel Azure Portalban fejlesztett függvények esetében válassza az **integráció** lehetőséget, majd válassza ki a **Event Grid triggert** , és válassza a **Event Grid előfizetés létrehozása** lehetőséget.

:::image type="content" source="media/functions-bindings-event-grid/portal-sub-create.png" alt-text="Új esemény-előfizetés összekapcsolása a portálon való aktiváláshoz.":::

Ha ezt a hivatkozást választja, a portálon megnyílik az **esemény-előfizetés létrehozása** lap, amelyen már meg van adva az aktuális eseményindító-végpont.

:::image type="content" source="media/functions-bindings-event-grid/endpoint-url.png" alt-text="Már definiált függvény-végponttal rendelkező esemény-előfizetés létrehozása" :::

Az előfizetések Azure Portal használatával történő létrehozásával kapcsolatos további információkért tekintse meg az [egyéni esemény-Azure Portal létrehozása](../event-grid/custom-event-quickstart-portal.md) a Event Grid dokumentációjában.

### <a name="azure-cli"></a>Azure CLI

Ha [Az Azure CLI](/cli/azure/get-started-with-azure-cli)használatával szeretne előfizetést létrehozni, használja az az [eventgrid Event-előfizetés Create](/cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create) parancsot.

A parancshoz meg kell adni a végpont URL-címét, amely meghívja a függvényt. A következő példa a verzióra jellemző URL-mintát mutatja:

#### <a name="version-2x-and-higher-runtime"></a>Version 2. x (és újabb) futtatókörnyezet

```http
https://{functionappname}.azurewebsites.net/runtime/webhooks/eventgrid?functionName={functionname}&code={systemkey}
```

#### <a name="version-1x-runtime"></a>1. x verzió futtatókörnyezete

```http
https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}
```

A rendszerkulcs egy olyan engedélyezési kulcs, amely egy Event Grid trigger végponti URL-címében szerepelnie kell. A következő szakasz ismerteti, hogyan kérheti le a rendszerkulcsot.

Az alábbi példa egy blob Storage-fiókra való előfizetést mutat be (a rendszerkulcs helyőrzője):

#### <a name="version-2x-and-higher-runtime"></a>Version 2. x (és újabb) futtatókörnyezet

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
    --provider-namespace Microsoft.Storage --resource-type storageAccounts \
    --resource-name myblobstorage12345 --name myFuncSub \
    --included-event-types Microsoft.Storage.BlobCreated \
    --subject-begins-with /blobServices/default/containers/images/blobs/ \
    --endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup ^
    --provider-namespace Microsoft.Storage --resource-type storageAccounts ^
    --resource-name myblobstorage12345 --name myFuncSub ^
    --included-event-types Microsoft.Storage.BlobCreated ^
    --subject-begins-with /blobServices/default/containers/images/blobs/ ^
    --endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

---

#### <a name="version-1x-runtime"></a>1. x verzió futtatókörnyezete

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
    --provider-namespace Microsoft.Storage --resource-type storageAccounts \
    --resource-name myblobstorage12345 --name myFuncSub \
    --included-event-types Microsoft.Storage.BlobCreated \
    --subject-begins-with /blobServices/default/containers/images/blobs/ \
    --endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup ^
    --provider-namespace Microsoft.Storage --resource-type storageAccounts ^
    --resource-name myblobstorage12345 --name myFuncSub ^
    --included-event-types Microsoft.Storage.BlobCreated ^
    --subject-begins-with /blobServices/default/containers/images/blobs/ ^
    --endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

---

Az előfizetés létrehozásával kapcsolatos további információkért tekintse meg [a blob Storage](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) rövid útmutatóját vagy a többi Event Grid rövid útmutatót.

### <a name="get-the-system-key"></a>A rendszerkulcs beszerzése

A rendszerkulcs a következő API-val (HTTP GET) szerezhető be:

#### <a name="version-2x-and-higher-runtime"></a>Version 2. x (és újabb) futtatókörnyezet

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgrid_extension?code={masterkey}
```

#### <a name="version-1x-runtime"></a>1. x verzió futtatókörnyezete

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={masterkey}
```

Ez egy felügyeleti API, ezért a Function app [főkulcsát](functions-bindings-http-webhook-trigger.md#authorization-keys)igényli. Ne tévesszük össze a rendszerkulcsot (egy Event Grid trigger függvény meghívásához) a főkulccsal (a Function alkalmazás felügyeleti feladatainak végrehajtásához). Amikor előfizet egy Event Grid témakörre, ügyeljen arra, hogy a rendszerkulcsot használja.

Íme egy példa a rendszerkulcsot biztosító válaszra:

```
{
  "name": "eventgridextensionconfig_extension",
  "value": "{the system key for the function}",
  "links": [
    {
      "rel": "self",
      "href": "{the URL for the function, without the system key}"
    }
  ]
}
```

A Function alkalmazás főkulcsát a portál **Function app Settings** lapján szerezheti be.

> [!IMPORTANT]
> A főkulcs rendszergazdai hozzáférést biztosít a Function alkalmazáshoz. Ne ossza meg ezt a kulcsot harmadik felekkel, vagy terjessze azt natív ügyfélalkalmazásokba.

További információ: [engedélyezési kulcsok](functions-bindings-http-webhook-trigger.md#authorization-keys) a http-trigger dokumentációjában.

Azt is megteheti, hogy HTTP-PUT-t küld a kulcs értékének megadásához.

## <a name="local-testing-with-viewer-web-app"></a>Helyi tesztelés a megjelenítői webalkalmazással

Event Grid-trigger helyi teszteléséhez be kell szereznie Event Grid HTTP-kéréseket a felhőből a helyi gépre. Ennek egyik módja a kérelmek online rögzítésének és manuális újraküldése a helyi gépen:

1. [Hozzon létre egy megjelenítői webalkalmazást](#create-a-viewer-web-app) , amely rögzíti az események üzeneteit.
1. [Hozzon létre egy Event Grid-előfizetést](#create-an-event-grid-subscription) , amely eseményeket küld a megjelenítői alkalmazásnak.
1. [Kérelem létrehozása](#generate-a-request) és a kérelem törzsének másolása a megjelenítői alkalmazásból.
1. [Manuálisan tegye közzé a kérést](#manually-post-the-request) a Event Grid trigger függvény localhost URL-címére.

Ha végzett a teszteléssel, a végpont frissítésével ugyanazt az előfizetést használhatja éles környezethez. Használja az az [eventgrid Event-előfizetés Update](/cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-update) Azure CLI parancsot.

### <a name="create-a-viewer-web-app"></a>Megjelenítői Webalkalmazás létrehozása

Az üzenetek rögzítésének egyszerűbbé tétele érdekében üzembe helyezhet egy [előre elkészített webalkalmazást](https://github.com/Azure-Samples/azure-event-grid-viewer) , amely megjeleníti az esemény üzeneteit. Az üzembe helyezett megoldás egy App Service-csomagot, egy App Service-webalkalmazást és egy, a GitHubról származó forráskódot tartalmaz.

A megoldásnak az előfizetésébe való telepítéséhez válassza az **Üzembe helyezés az Azure-ban** lehetőséget. Az Azure Portalon adjon meg értékeket a paraméterekhez.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png" alt="Button to Deploy to Azure." /></a>

Az üzembe helyezés befejezése eltarthat néhány percig. A sikeres üzembe helyezést követően tekintse meg a webalkalmazást, hogy meggyőződjön annak működéséről. Egy webböngészőben navigáljon a következő helyre: `https://<your-site-name>.azurewebsites.net`.

A hely látható, de még nem lett közzétéve esemény.

![Új hely megtekintése](media/functions-bindings-event-grid/view-site.png)

### <a name="create-an-event-grid-subscription"></a>Event Grid-előfizetés létrehozása

Hozzon létre egy Event Grid-előfizetést a tesztelni kívánt típushoz, és adja meg a webalkalmazás URL-címét az eseményekről szóló értesítés végpontja. A webalkalmazás végpontjának az `/api/updates/` utótagot kell tartalmaznia. Tehát a teljes URL-cím `https://<your-site-name>.azurewebsites.net/api/updates`

További információ az előfizetések létrehozásáról a Azure Portal használatával: [Egyéni Event-Azure Portal létrehozása](../event-grid/custom-event-quickstart-portal.md) a Event Grid dokumentációjában.

### <a name="generate-a-request"></a>Kérelem létrehozása

Indítson el egy eseményt, amely a webalkalmazás-végpontra HTTP-forgalmat fog előállítani.  Ha például létrehozta a blob Storage-előfizetést, feltölt vagy töröl egy blobot. Ha egy kérelem megjelenik a webalkalmazásban, másolja a kérelem törzsét.

A rendszer először az előfizetés-ellenőrzési kérelmet kapja meg; hagyja figyelmen kívül az érvényesítési kérelmeket, és másolja az eseményre vonatkozó kérelmet.

![Kérelem törzsének másolása a webalkalmazásból](media/functions-bindings-event-grid/view-results.png)

### <a name="manually-post-the-request"></a>A kérelem manuális közzététele

Futtassa helyileg a Event Grid függvényt. A `Content-Type` és a `aeg-event-type` fejléceket manuálisan kell beállítani, míg az összes többi érték alapértelmezettként is maradhat.

HTTP POST-kérelem létrehozásához használjon olyan eszközt, mint például a [Poster](https://www.getpostman.com/) vagy a [curl](https://curl.haxx.se/docs/httpscripting.html) :

* Adja meg a `Content-Type: application/json` fejlécet.
* Adja meg a `aeg-event-type: Notification` fejlécet.
* Illessze be a RequestBin a kérelem törzsébe.
* Tegye közzé a Event Grid trigger függvény URL-címét.
  * A 2. x és újabb verziók esetében használja a következő mintát:

    ```
    http://localhost:7071/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
    ```

  * 1. x esetén használja a következőt:

    ```
    http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
    ```

A `functionName` paraméternek az attribútumban megadott névnek kell lennie `FunctionName` .

A következő Képernyőképek a Poster fejléceit és a kérelem törzsét mutatják be:

![Fejlécek a Poster-ban](media/functions-bindings-event-grid/postman2.png)

![Kérelem törzse a Poster-ban](media/functions-bindings-event-grid/postman.png)

A Event Grid trigger függvény végrehajtja és megjeleníti a következő példához hasonló naplókat:

![Minta Event Grid trigger-függvények naplói](media/functions-bindings-event-grid/eg-output.png)

## <a name="next-steps"></a>Következő lépések

* [Event Grid esemény elküldése](./functions-bindings-event-grid-output.md)
