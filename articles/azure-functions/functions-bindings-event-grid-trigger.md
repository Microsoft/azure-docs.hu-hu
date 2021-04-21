---
title: Azure Event Grid eseményindító a Azure Functions
description: Megtudhatja, hogyan futtathat kódot Event Grid események Azure Functions elküldésekor.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: devx-track-csharp, fasttrack-edit, devx-track-python
ms.openlocfilehash: 17968f2c137eef51eecdb6c7098c7056944dc970
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782188"
---
# <a name="azure-event-grid-trigger-for-azure-functions"></a>Azure Event Grid eseményindító a Azure Functions

A függvény-eseményindítóval válaszolhat egy olyan eseményre, amely egy Event Grid elküldve.

A beállítással és a konfigurációval kapcsolatos információkért lásd az [áttekintést.](./functions-bindings-event-grid.md)

## <a name="example"></a>Példa

# <a name="c"></a>[C#](#tab/csharp)

EGY HTTP-eseményindító példáért lásd: [Események fogadása HTTP-végpontra.](../event-grid/receive-events.md)

### <a name="c-2x-and-higher"></a>C# (2.x és újabb verziók)

Az alábbi példa egy [olyan C#-függvényt mutat be,](functions-dotnet-class-library.md) amely a függvényhez kötődik: `EventGridEvent`

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

További információ: Csomagok, [Attribútumok,](#attributes-and-annotations) [Konfiguráció](#configuration)és [Használat.](#usage)

### <a name="version-1x"></a>1.x verzió

Az alábbi példa egy Függvények 1.x [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely a függvényhez kötődik: `JObject`

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

# <a name="c-script"></a>[C#-szkript](#tab/csharp-script)

Az alábbi példa egy eseményindító-kötést mutat be egyfunction.js *fájlon,* és egy [C#-szkript függvényt,](functions-reference-csharp.md) amely a kötést használja.

A fájlban található kötési *function.jsa* következő:

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

### <a name="version-2x-and-higher"></a>2.x és újabb verziók

Az alábbi példa a következőt köti `EventGridEvent` össze:

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(EventGridEvent eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.Data.ToString());
}
```

További információ: Csomagok, [Attribútumok,](#attributes-and-annotations) [Konfiguráció](#configuration)és [Használat.](#usage)

### <a name="version-1x"></a>1.x verzió

A függvény 1.x C#-szkriptkódja a következőhöz `JObject` kötődik:

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

* [Event Grid eseményindító, Sztring paraméter](#event-grid-trigger-string-parameter)
* [Event Grid trigger, POJO paraméter](#event-grid-trigger-pojo-parameter)

Az alábbi példák a [Java](functions-reference-java.md) eseményindító-kötését mutatják be, amelyek a kötést használják, és kinyomtatnak egy eseményt, először POJO-ként fogadva `String` az eseményt, a másodikat pedig POJO-ként.

### <a name="event-grid-trigger-string-parameter"></a>Event Grid eseményindító, Sztring paraméter

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

Ez a példa a következő POJO-t használja, amely egy esemény legfelső szintű tulajdonságait Event Grid képviseli:

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

A megérkezéskor az esemény JSON-hasznos terhelését a rendszer de szerializálja ```EventSchema``` a POJO-ba a függvény által való használatra. Ez a folyamat lehetővé teszi, hogy a függvény objektumorientált módon fér hozzá az esemény tulajdonságaihoz.

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

A [Java-függvények futásidejű kódtárában](/java/api/overview/azure/functions/runtime)használja a jegyzetet olyan paramétereken, amelyeknek az értéke `EventGridTrigger` az EventGridből adna vissza. Az ezekkel a jegyzetekkel megadott paraméterek hatására a függvény akkor fut, amikor egy esemény érkezik.  Ez a jegyzet használható natív Java-típusokkal, POJO-okkal vagy nullázható értékekkel a `Optional<T>` használatával.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Az alábbi példa egy eseményindító-kötést mutatfunction.js *fájlon,* és egy [JavaScript-függvényt,](functions-reference-node.md) amely a kötést használja.

A fájlban található *kötésifunction.jsa* következő:

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

A JavaScript-kód a következő:

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

Az alábbi példa bemutatja, hogyan konfigurálhat egy Event Grid eseményindító-kötést a *function.jsfájlban.*

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

A Event Grid esemény egy nevű paraméterrel érhető el a függvény számára az alábbi `eventGridEvent` PowerShell-példában látható módon.

```powershell
param($eventGridEvent, $TriggerMetadata)

# Make sure to pass hashtables to Out-String so they're logged correctly
$eventGridEvent | Out-String | Write-Host
```

# <a name="python"></a>[Python](#tab/python)

Az alábbi példa egy eseményindító-kötést mutat be egyfunction.js *fájlon,* és egy Python-függvényt, amely a kötést használja. [](functions-reference-python.md)

A fájlban található *kötésifunction.jsa* következő:

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

A Python-kód a következő:

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

A [C#-osztálytárakban](functions-dotnet-class-library.md)használja az [EventGridTrigger attribútumot.](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/TriggerBinding/EventGridTriggerAttribute.cs)

A metódusaláírások `EventGridTrigger` attribútumai a következőek:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, ILogger log)
{
    ...
}
```

A teljes példát a C#-példában láthatja.

# <a name="c-script"></a>[C#-szkript](#tab/csharp-script)

A C#-szkript nem támogatja az attribútumokat.

# <a name="java"></a>[Java](#tab/java)

Az [EventGridTrigger-jegyzet](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/EventGridTrigger.java) lehetővé teszi, hogy deklaratív módon konfigurálja a Event Grid kötést konfigurációs értékek megírása révén. További [részletekért tekintse](#example) meg [a példa-](#configuration) és konfigurációs szakaszokat.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A PowerShell nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat ismerteti a fájlon a fájlon a fájlfunction.js *beállított kötéskonfigurációs tulajdonságokat.* A attribútumban nem kell konstruktorparamétereket vagy tulajdonságokat `EventGridTrigger` beállítani.

|function.jstulajdonságon |Description|
|---------|---------|
| **Típus** | Kötelező – a beállításnak a (kötelező) beállításnak kell `eventGridTrigger` lennie. |
| **irány** | Kötelező – a beállításnak a (kötelező) beállításnak kell `in` lennie. |
| **név** | Kötelező – az eseményadatokat megkapó paraméter függvénykódban használt változóneve. |

## <a name="usage"></a>Használat

# <a name="c"></a>[C#](#tab/csharp)

Az Azure Functions 1.x-ben a következő paramétertípusokat használhatja a Event Grid eseményindítóhoz:

* `JObject`
* `string`

A Azure Functions 2.x vagy újabb verzióban a következő paramétertípust is használhatja a Event Grid eseményindítóhoz:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`– Meghatározza az összes eseménytípus közös mezőinek tulajdonságait.

> [!NOTE]
> Ha a Függvények v1-ben megpróbál kötődni a-hez, a fordító egy "elavult" üzenetet jelenít meg, és javasolja, hogy használja `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent` `Microsoft.Azure.EventGrid.Models.EventGridEvent` helyette. Az újabb típust úgy használhatja, ha a [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet-csomagra hivatkozik, és teljes mértékben minősíti a típusnevet a `EventGridEvent` előtaggal. `Microsoft.Azure.EventGrid.Models`

# <a name="c-script"></a>[C#-szkript](#tab/csharp-script)

Az Azure Functions 1.x-ben a következő paramétertípusokat használhatja a Event Grid eseményindítóhoz:

* `JObject`
* `string`

A Azure Functions 2.x vagy újabb verzióban a következő paramétertípust is használhatja a Event Grid eseményindítóhoz:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`– Meghatározza az összes eseménytípus közös mezőinek tulajdonságait.

> [!NOTE]
> Ha a Függvények v1-ben megpróbál kötődni a-hez, a fordító egy "elavult" üzenetet jelenít meg, és javasolja, hogy használja `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent` `Microsoft.Azure.EventGrid.Models.EventGridEvent` helyette. Az újabb típust úgy használhatja, ha a [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet-csomagra hivatkozik, és teljes mértékben minősíti a típusnevet a `EventGridEvent` előtaggal. `Microsoft.Azure.EventGrid.Models` A NuGet-csomagok C#-szkript függvényben való hivatkozásával kapcsolatos információkért lásd: [NuGet-csomagok használata](functions-reference-csharp.md#using-nuget-packages)

# <a name="java"></a>[Java](#tab/java)

A Event Grid eseménypéldány a attribútumhoz társított paraméterrel érhető `EventGridTrigger` el, típusként begépelve. `EventSchema` További [részletekért](#example) tekintse meg a példát.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A Event Grid példány a fájl tulajdonságában található fájlfunction.js *konfigurált* paraméterrel érhető `name` el.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A Event Grid példány a fájl tulajdonságában található fájlfunction.js *konfigurált* paraméterrel érhető `name` el.

# <a name="python"></a>[Python](#tab/python)

A Event Grid példány a fájl tulajdonságában a  fájlfunction.jskonfigurált paraméterrel érhető `name` el, a következő begépelve: `func.EventGridEvent` .

---

## <a name="event-schema"></a>Eseményséma

Egy esemény Event Grid JSON-objektumként érkezik egy HTTP-kérés törzsébe. A JSON az alábbi példához hasonlóan néz ki:

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

Az itt látható példa egy elem tömbje. Event Grid mindig küld egy tömböt, és a tömbben több eseményt is küldhet. A runtime minden tömbelemhez egyszer hívja meg a függvényt.

Az esemény JSON-adatainak legfelső szintű tulajdonságai az összes eseménytípus esetében azonosak, a tulajdonság tartalma pedig az egyes `data` eseménytípusokra jellemző. Az itt látható példa egy Blob Storage-eseményre mutat.

A gyakori és eseményspecifikus tulajdonságok magyarázatát [](../event-grid/event-schema.md#event-properties) az eseménytulajdonságokról a dokumentáció eseménytulajdonságokat Event Grid.

A `EventGridEvent` típus csak a legfelső szintű tulajdonságokat határozza meg; a tulajdonság egy `Data` `JObject` .

## <a name="create-a-subscription"></a>Előfizetés létrehozása

A HTTP-Event Grid fogadásához hozzon létre egy Event Grid előfizetést, amely meghatározza a függvényt meghívó végponti URL-címet.

### <a name="azure-portal"></a>Azure Portal

Az Azure Portal Event Grid-eseményindítóval létrehozott függvények esetén válassza az  Integráció lehetőséget, majd az **Event Grid-eseményindítót,** és válassza a Create Event Grid subscription (Előfizetés **létrehozása) lehetőséget.**

:::image type="content" source="media/functions-bindings-event-grid/portal-sub-create.png" alt-text="Új esemény-előfizetés csatlakoztatása a portálon való aktiváláshoz.":::

Ha erre a hivatkozásra kattint, a portál megnyitja az **Esemény-előfizetés** létrehozása oldalt a már definiált aktuális eseményindító-végponttal.

:::image type="content" source="media/functions-bindings-event-grid/endpoint-url.png" alt-text="Esemény-előfizetés létrehozása már definiált függvényvégponttal" :::

Az előfizetések a Azure Portal használatával való létrehozásáról további információt az egyéni esemény létrehozása – [Azure Portal](../event-grid/custom-event-quickstart-portal.md) az Event Grid dokumentációjában.

### <a name="azure-cli"></a>Azure CLI

Ha az Azure [CLI](/cli/azure/get-started-with-azure-cli)használatával hoz létre előfizetést, használja [az az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) parancsot.

A parancshoz szükség van a függvényt meghívó végponti URL-címre. Az alábbi példa a verzióspecifikus URL-mintát mutatja be:

#### <a name="version-2x-and-higher-runtime"></a>2.x (és újabb) verziójú futtatás

```http
https://{functionappname}.azurewebsites.net/runtime/webhooks/eventgrid?functionName={functionname}&code={systemkey}
```

#### <a name="version-1x-runtime"></a>1.x verziójú runtime

```http
https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}
```

A rendszerkulcs egy engedélyezési kulcs, amely szerepelni fog a végpont URL-címében egy Event Grid eseményindítóhoz. A következő szakasz ismerteti, hogyan lehet lekért rendszerkulcsot.

Az alábbi példa feliratkozik egy Blob Storage-fiókra (a rendszerkulcs helyőrzővel):

#### <a name="version-2x-and-higher-runtime"></a>2.x (és újabb) verziójú futtatás

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

#### <a name="version-1x-runtime"></a>1.x verziójú runtime

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

Az előfizetések létrehozásáról további információt a [Blob Storage](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) gyorsútmutatója vagy a többi Event Grid tartalmaz.

### <a name="get-the-system-key"></a>A rendszerkulcs lekérte

A rendszerkulcsot a következő API-val (HTTP GET) kaphatja meg:

#### <a name="version-2x-and-higher-runtime"></a>2.x -es (és újabb) verziójú futtatás

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgrid_extension?code={masterkey}
```

#### <a name="version-1x-runtime"></a>1.x verziójú runtime

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={masterkey}
```

Ez egy rendszergazdai API, ezért a függvényalkalmazás főkulcsát [igényli.](functions-bindings-http-webhook-trigger.md#authorization-keys) Ne keverje össze a rendszerkulcsot (a Event Grid-eseményindító függvények Event Grid) a fő kulccsal (felügyeleti feladatok végrehajtásához a függvényalkalmazáson). Amikor előfizet egy Event Grid témakörre, mindenképpen a rendszerkulcsot használja.

Az alábbi példa a rendszerkulcsot megadhatja válaszként:

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

A függvényalkalmazás főkulcsát a portál Függvényalkalmazás beállításai **lapján** kaphatja meg.

> [!IMPORTANT]
> A főkulcs rendszergazdai hozzáférést biztosít a függvényalkalmazáshoz. Ne ossza meg ezt a kulcsot harmadik felekkel, és ne ossza meg natív ügyfélalkalmazások között.

További információkért lásd [a](functions-bindings-http-webhook-trigger.md#authorization-keys) HTTP-eseményindítók referenciacikkének engedélyezési kulcsait.

Másik lehetőségként elküldhet egy HTTP PUT-t, hogy saját maga adja meg a kulcs értékét.

## <a name="local-testing-with-viewer-web-app"></a>Helyi tesztelés megjelenítő webalkalmazással

Az eseményindító Event Grid helyi teszteléséhez be kell szereznie Event Grid HTTP-kéréseket, amelyek a felhőből a helyi gépre vannak kézbesítve. Ennek egyik módja a kérések online rögzítése és manuális újraküldése a helyi gépen:

1. [Hozzon létre egy megjelenítő webalkalmazást,](#create-a-viewer-web-app) amely rögzíti az eseményüzeneteket.
1. [Hozzon létre egy Event Grid,](#create-an-event-grid-subscription) amely eseményeket küld a megjelenítő alkalmazásnak.
1. [Hozzon létre egy kérést,](#generate-a-request) és másolja ki a kérelem törzsét a megjelenítő alkalmazásból.
1. [Manuálisan tegye közzé a kérést](#manually-post-the-request) az eseményindító függvény localhost URL Event Grid url-címére.

Ha végzett a teszteléssel, használhatja ugyanazt az előfizetést éles környezetben is a végpont frissítésével. Használja az [az eventgrid event-subscription update](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_update) Azure CLI-parancsot.

### <a name="create-a-viewer-web-app"></a>Megjelenítő webalkalmazás létrehozása

Az eseményüzenetek rögzítésének egyszerűsítése [](https://github.com/Azure-Samples/azure-event-grid-viewer) érdekében üzembe helyezhet egy előre felépített webalkalmazást, amely megjeleníti az eseményüzeneteket. Az üzembe helyezett megoldás egy App Service-csomagot, egy App Service-webalkalmazást és egy, a GitHubról származó forráskódot tartalmaz.

A megoldásnak az előfizetésébe való telepítéséhez válassza az **Üzembe helyezés az Azure-ban** lehetőséget. Az Azure Portalon adjon meg értékeket a paraméterekhez.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png" alt="Button to Deploy to Azure." /></a>

Az üzembe helyezés befejezése eltarthat néhány percig. A sikeres üzembe helyezést követően tekintse meg a webalkalmazást, hogy meggyőződjön annak működéséről. Egy webböngészőben navigáljon a következő helyre: `https://<your-site-name>.azurewebsites.net`.

A hely látható, de még nem lett közzétéve esemény.

![Új hely megtekintése](media/functions-bindings-event-grid/view-site.png)

### <a name="create-an-event-grid-subscription"></a>Event Grid-előfizetés létrehozása

Hozzon Event Grid tesztelni kívánt típusú előfizetést, és adja meg a webalkalmazásból származó URL-címet az eseményértesítés végpontjaként. A webalkalmazás végpontjának az `/api/updates/` utótagot kell tartalmaznia. A teljes URL-cím tehát a következő: `https://<your-site-name>.azurewebsites.net/api/updates`

Az előfizetések a Azure Portal használatával való létrehozásáról az egyéni esemény létrehozása – [Azure Portal](../event-grid/custom-event-quickstart-portal.md) az Event Grid dokumentációjában.

### <a name="generate-a-request"></a>Kérés létrehozása

Esemény aktiválása, amely HTTP-forgalmat generál a webalkalmazás végpontjára.  Ha például blobtároló-előfizetést hozott létre, töltsön fel vagy töröljön egy blobot. Amikor egy kérés megjelenik a webalkalmazásban, másolja ki a kérelem törzsét.

Először az előfizetés érvényesítési kérése érkezik meg; hagyja figyelmen kívül az érvényesítési kérelmeket, és másolja ki az eseménykérést.

![Kérelem törzsének másolása a webalkalmazásból](media/functions-bindings-event-grid/view-results.png)

### <a name="manually-post-the-request"></a>Manuálisan tegye közzé a kérést

Futtassa a Event Grid függvényt helyileg. A és fejléceket manuálisan kell beállítani, míg az összes többi értéket `Content-Type` `aeg-event-type` meghagyhatja alapértelmezettként.

HTTP POST-kérés létrehozásához használjon olyan eszközt, mint a [Postman](https://www.getpostman.com/) vagy a [curl:](https://curl.haxx.se/docs/httpscripting.html)

* Állítson be `Content-Type: application/json` egy fejlécet.
* Állítson be `aeg-event-type: Notification` egy fejlécet.
* Illessze be a RequestBin-adatokat a kérelem törzsébe.
* Tegye közzé a címet az eseményindító Event Grid URL-címére.
  * A 2.x és újabb verziókhoz használja a következő mintát:

    ```
    http://localhost:7071/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
    ```

  * Az 1.x-hez használja a következőt:

    ```
    http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
    ```

A `functionName` paraméternek az attribútumban megadott névnek kell `FunctionName` lennie.

Az alábbi képernyőképek a Postman fejlécét és kérelem törzsét mutatják be:

![Fejlécek a Postmanben](media/functions-bindings-event-grid/postman2.png)

![Kérelem törzse a Postmanben](media/functions-bindings-event-grid/postman.png)

A Event Grid eseményindító függvény fut, és az alábbi példához hasonló naplókat mutat be:

![Példa Event Grid eseményindító függvénynaplóira](media/functions-bindings-event-grid/eg-output.png)

## <a name="next-steps"></a>Következő lépések

* [Esemény elküldése Event Grid esemény](./functions-bindings-event-grid-output.md)
