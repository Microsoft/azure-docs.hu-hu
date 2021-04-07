---
title: Az Azure üzenetsor-tároló kimeneti kötése Azure Functions
description: Ismerje meg, hogyan hozhat létre Azure üzenetsor-tárolási üzeneteket a Azure Functionsban.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: devx-track-csharp, cc996988-fb4f-47, devx-track-python
ms.openlocfilehash: 5d94625e3eb121e556b28038cf59626be1332966
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "102455805"
---
# <a name="azure-queue-storage-output-bindings-for-azure-functions"></a>Az Azure üzenetsor-tároló kimeneti kötései Azure Functions

Azure Functions új Azure üzenetsor-tárolási üzeneteket hozhat létre egy kimeneti kötés beállításával.

További információ a telepítésről és a konfigurációról: [Áttekintés](./functions-bindings-storage-queue.md).

## <a name="example"></a>Példa

# <a name="c"></a>[C#](#tab/csharp)

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely üzenetsor-üzenetet hoz létre minden fogadott http-kérelemhez.

```csharp
[StorageAccount("MyStorageConnectionAppSetting")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  ILogger log)
    {
        log.LogInformation($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

Az alábbi példa egy HTTP-trigger kötést mutat be egy olyan *function.jsa* fájl-és [C#-parancsfájl (. CSX)](functions-reference-csharp.md) kódjában, amely a kötést használja. A függvény egy **CustomQueueMessage** objektum-adattartalommal rendelkező üzenetsor-objektumot hoz létre minden fogadott http-kérelemhez.

A fájl *function.js* :

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

Az itt található C#-kód egyetlen üzenetsor-üzenetet hoz létre:

```cs
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, ILogger log)
{
    return input;
}
```

Egy vagy paraméter használatával egyszerre több üzenetet is küldhet `ICollector` `IAsyncCollector` . Az itt található C#-kód több üzenetet küld, egyet a HTTP-kérési adatokkal, az egyiket pedig a nehezen kódolt értékekkel:

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItems, 
    ILogger log)
{
    myQueueItems.Add(input);
    myQueueItems.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

# <a name="java"></a>[Java](#tab/java)

 Az alábbi példa egy Java-függvényt mutat be, amely üzenetsor-üzenetet hoz létre a HTTP-kérések indításakor.

```java
@FunctionName("httpToQueue")
@QueueOutput(name = "item", queueName = "myqueue-items", connection = "MyStorageConnectionAppSetting")
 public String pushToQueue(
     @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
     final String message,
     @HttpOutput(name = "response") final OutputBinding<String> result) {
       result.setValue(message + " has been added.");
       return message;
 }
```

A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja a `@QueueOutput` Megjegyzés azon paramétereket, amelyek értékét a várólista-tárolóba kívánja írni.  A paraméternek a következőnek kell lennie: `OutputBinding<T>` , ahol a egy `T` POJO natív Java-típusa.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Az alábbi példa egy HTTP trigger kötést mutat be egy *function.jsa* fájlban, és egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja. A függvény minden fogadott HTTP-kérelemhez létrehoz egy üzenetsor-tételt.

A fájl *function.js* :

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "myQueueItem",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

Itt látható a JavaScript-kód:

```javascript
module.exports = function (context, input) {
    context.bindings.myQueueItem = input.body;
    context.done();
};
```

Egyszerre több üzenetet is küldhet, ha a kimeneti kötéshez definiál egy üzenet tömböt `myQueueItem` . A következő JavaScript-kód két üzenetsor-üzenetet küld az egyes fogadott HTTP-kérelmekhez rögzített értékekkel.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Az alábbi példák azt mutatják be, hogyan lehet egy üzenetsor-üzenetet egy HTTP által aktivált függvényből kimenetként megjeleníteni. A konfigurációs szakasza `type` `queue` meghatározza a kimeneti kötést.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "Msg",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

A kötési konfiguráció használatával egy PowerShell-függvény létrehoz egy üzenetsor-üzenetet a használatával `Push-OutputBinding` . Ebben a példában egy üzenet jön létre egy lekérdezési sztringből vagy egy szövegtörzsből.

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = $Request.Query.Message
Push-OutputBinding -Name Msg -Value $message
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

Ha egyszerre több üzenetet szeretne küldeni, Definiáljon egy üzenetet, és használja az `Push-OutputBinding` üzeneteket az üzenetsor kimeneti kötéseinek küldéséhez.

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = @("message1", "message2")
Push-OutputBinding -Name Msg -Value $message
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

# <a name="python"></a>[Python](#tab/python)

Az alábbi példa bemutatja, hogyan lehet egy és több értéket kiadni a tárolási várólistáknak. A *function.jshoz* szükséges konfiguráció ugyanaz, mint az egyik módja.

A tárolási várólista kötése olyanfunction.jsvan definiálva, ahol *a* *típus* értékre van állítva `queue` .

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

Ha egyéni üzenetet szeretne beállítani a várólistán, egyetlen értéket kell átadnia a `set` metódusnak.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

Ha több üzenetet szeretne létrehozni a várólistán, deklaráljon egy paramétert a megfelelő lista típusaként, és adjon át egy értékeket tartalmazó tömböt (amely megfelel a lista típusának) a `set` metódusnak.

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C#](#tab/csharp)

A [C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueAttribute.cs).

Az attribútum egy `out` paraméterre vagy a függvény visszatérési értékére vonatkozik. Az attribútum konstruktora a várólista nevét veszi át, ahogy az az alábbi példában látható:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

A tulajdonság beállításával `Connection` megadhatja a használni kívánt Storage-fiókot, ahogy az az alábbi példában is látható:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Teljes példa: [kimeneti példa](#example).

Az `StorageAccount` attribútummal megadhatja a Storage-fiókot az osztály, a metódus vagy a paraméter szintjén. További információ: trigger-attributes.

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A C# parancsfájl nem támogatja az attribútumokat.

# <a name="java"></a>[Java](#tab/java)

A `QueueOutput` jegyzet lehetővé teszi a függvények kimenetének megírását. Az alábbi példa egy HTTP által aktivált függvényt mutat be, amely üzenetsor-üzenetet hoz létre.

```java
package com.function;
import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerQueueOutput {
    @FunctionName("HttpTriggerQueueOutput")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") OutputBinding<String> message,
            final ExecutionContext context) {

        message.setValue(request.getQueryParameters().get("name"));
        return request.createResponseBuilder(HttpStatus.OK).body("Done").build();
    }
}
```

| Tulajdonság    | Leírás |
|-------------|-----------------------------|
|`name`       | Deklarálja a paraméter nevét a függvény aláírásában. A függvény indításakor ennek a paraméternek az értéke az üzenetsor üzenetének tartalmát jeleníti meg. |
|`queueName`  | Deklarálja a várólista nevét a Storage-fiókban. |
|`connection` | A Storage-fiók kapcsolódási karakterláncára mutat. |

A jegyzethez társított paraméter `QueueOutput` [OutputBinding \<T\> ](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/OutputBinding.java) -példányként van beírva.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A PowerShell nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a fájl és attribútum *function.jsjában* beállított kötési konfigurációs tulajdonságokat ismerteti `Queue` .

|function.jsa tulajdonságon | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**típusa** | n.a. | Értékre kell állítani `queue` . Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az triggert a Azure Portalban.|
|**irányba** | n.a. | Értékre kell állítani `out` . Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az triggert a Azure Portalban. |
|**név** | n.a. | Annak a változónak a neve, amely a függvény kódjában a várólistát jelképezi. Állítsa a értékre `$return` a függvény visszatérési értékének hivatkozásához.|
|**queueName** |**QueueName** | A várólista neve. |
|**kapcsolat** | **Kapcsolat** |Egy olyan Alkalmazásbeállítás neve, amely a kötéshez használandó tárolási kapcsolati karakterláncot tartalmazza. Ha az Alkalmazásbeállítások neve "AzureWebJobs" előtaggal kezdődik, akkor itt csak a nevet adja meg.<br><br>Ha például a "MyStorage" értékre van állítva `connection` , a functions futtatókörnyezet egy "MyStorage" nevű alkalmazás-beállítást keres. Ha `connection` üresen hagyja, a functions futtatókörnyezet az alapértelmezett tárolási kapcsolatok karakterláncát használja a nevű alkalmazás-beállításban `AzureWebJobsStorage` .<br><br>Ha [a bővítmény 5. x vagy újabb verzióját](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher)használja, a kapcsolati karakterlánc helyett megadhat egy olyan konfigurációs szakaszra mutató hivatkozást, amely meghatározza a kapcsolódást. Lásd: [kapcsolatok](./functions-reference.md#connections).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Használat

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default"></a>Alapértelmezett

Egyetlen üzenetsor-üzenetet írhat egy metódus-paraméter (például `out T paramName` ) használatával. Paraméter helyett a metódus visszatérési típusát is használhatja `out` , és a `T` következő típusok bármelyike lehet:

* Egy, JSON-ként szerializálható objektum
* `string`
* `byte[]`
* [CloudQueueMessage] 

Ha egy hibaüzenetet próbál meg kötni `CloudQueueMessage` , és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e [a megfelelő Storage SDK-verzióra](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

A C# és C# parancsfájlban több üzenetsor-üzenetet is írhat a következő típusok egyikének használatával: 

* `ICollector<T>` vagy `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

### <a name="additional-types"></a>További típusok

A [5.0.0 vagy újabb verzióját](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) használó alkalmazások a [.net-hez készült Azure SDK](/dotnet/api/overview/azure/storage.queues-readme)-ból is használhatnak típusokat. Ez a verzió a következő típusok mellett támogatja a örökölt `CloudQueue` és a `CloudQueueMessage` típusokat:

- [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage)
- [QueueClient](/dotnet/api/azure.storage.queues.queueclient) több üzenetsor írásához

A fenti típusokat használó példákat a [bővítmény GitHub-tárházában](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Queues#examples)tekintheti meg.

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

### <a name="default"></a>Alapértelmezett

Egyetlen üzenetsor-üzenetet írhat egy metódus-paraméter (például `out T paramName` ) használatával. A `paramName` értéke `name` *function.json* tulajdonságában megadott érték. Paraméter helyett a metódus visszatérési típusát is használhatja `out` , és a `T` következő típusok bármelyike lehet:

* Egy, JSON-ként szerializálható objektum
* `string`
* `byte[]`
* [CloudQueueMessage] 

Ha egy hibaüzenetet próbál meg kötni `CloudQueueMessage` , és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e [a megfelelő Storage SDK-verzióra](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

A C# és C# parancsfájlban több üzenetsor-üzenetet is írhat a következő típusok egyikének használatával: 

* `ICollector<T>` vagy `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

### <a name="additional-types"></a>További típusok

A [5.0.0 vagy újabb verzióját](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) használó alkalmazások a [.net-hez készült Azure SDK](/dotnet/api/overview/azure/storage.queues-readme)-ból is használhatnak típusokat. Ez a verzió a következő típusok mellett támogatja a örökölt `CloudQueue` és a `CloudQueueMessage` típusokat:

- [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage)
- [QueueClient](/dotnet/api/azure.storage.queues.queueclient) több üzenetsor írásához

A fenti típusokat használó példákat a [bővítmény GitHub-tárházában](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Queues#examples)tekintheti meg.

# <a name="java"></a>[Java](#tab/java)

Az üzenetsor-üzenetek egy függvényből való kiosztására két lehetőség áll rendelkezésre a [QueueOutput](/java/api/com.microsoft.azure.functions.annotation.queueoutput) jegyzet használatával:

- Visszaadott **érték**: Ha a jegyzetet a függvényhez alkalmazza, a függvény visszatérési értéke üzenetsor-üzenetként marad.

- **Elengedhetetlen**: Ha explicit módon be szeretné állítani az üzenet értékét, alkalmazza a jegyzetet egy adott paraméterre [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) , ahol a egy `T` POJO vagy bármely natív Java-típus. Ezzel a konfigurációval a metódus értékének átadásakor a rendszer `setValue` várólista-üzenetként is megőrzi az értéket.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A kimeneti várólista elem elérhető, `context.bindings.<NAME>` ahol a `<NAME>` megegyezik a *function.js* által megadott névvel. A várólista-elem hasznos adatainak karakterláncot vagy JSON-szerializálható objektumot is használhat.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A várólista-üzenet kimenete elérhető, `Push-OutputBinding` ahol olyan argumentumokat ad át, amelyek megfelelnek a kötés paraméterében megadott névnek a `name` fájl *function.js* .

# <a name="python"></a>[Python](#tab/python)

A várólista-üzenetek egy függvényből való üzembe helyezésének két lehetősége van:

- Visszaadott **érték**: állítsa be `name` *function.js* tulajdonságát a értékre `$return` . Ezzel a konfigurációval a függvény visszatérési értéke üzenetsor-tárolási üzenetként is megmarad.

- **Elengedhetetlen**: adjon meg egy értéket a [set](/python/api/azure-functions/azure.functions.out#set-val--t-----none) metódusnak, amely [kimenő](/python/api/azure-functions/azure.functions.out) típusként van deklarálva. Az átadott értéket `set` üzenetsor-tárolási üzenetként őrzi meg a rendszer.

---

## <a name="exceptions-and-return-codes"></a>Kivételek és visszatérési kódok

| Kötés |  Referencia |
|---|---|
| Üzenetsor | [Üzenetsor-hibakódok](/rest/api/storageservices/queue-service-error-codes) |
| BLOB, tábla, üzenetsor | [Tárolási hibakódok](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB, tábla, üzenetsor |  [Hibaelhárítás](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Következő lépések

- [Függvény futtatása üzenetsor-tárolási adatváltozásként (trigger)](./functions-bindings-storage-queue-trigger.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
