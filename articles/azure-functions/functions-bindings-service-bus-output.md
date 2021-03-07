---
title: Azure Functions Azure Service Bus kimeneti kötései
description: Megtudhatja, hogyan küldhet Azure Service Bus üzeneteket a Azure Functionsból.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: ae2be8dbcb4839c7d16b864c484c3360fdcfe324
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102425583"
---
# <a name="azure-service-bus-output-binding-for-azure-functions"></a>Azure Functions Azure Service Bus kimeneti kötése

A várólista vagy a témakör üzeneteinek küldéséhez használjon Azure Service Bus kimeneti kötést.

További információ a telepítésről és a konfigurációról: [Áttekintés](functions-bindings-service-bus.md).

## <a name="example"></a>Példa

# <a name="c"></a>[C#](#tab/csharp)

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely Service Bus üzenetsor-üzenetet küld:

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

Az alábbi példa egy Service Bus kimeneti kötést mutat be a fájlban lévő *function.js* , és egy [C# parancsfájl-függvényt](functions-reference-csharp.md) , amely a kötést használja. A függvény egy időzítő trigger használatával küld üzenetsor-üzenetet 15 másodpercenként.

A *function.js* fájlban található kötési adatfájlok:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Az alábbi C#-kód egyetlen üzenetet hoz létre:

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

A következő C#-kód több üzenetet hoz létre:

```cs
public static async Task Run(TimerInfo myTimer, ILogger log, IAsyncCollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    await outputSbQueue.AddAsync("1 " + message);
    await outputSbQueue.AddAsync("2 " + message);
}
```

# <a name="java"></a>[Java](#tab/java)

Az alábbi példa egy Java-függvényt mutat be, amely egy HTTP- `myqueue` kérelem által aktivált üzenetet küld egy Service Bus üzenetsor számára.

```java
@FunctionName("httpToServiceBusQueue")
@ServiceBusQueueOutput(name = "message", queueName = "myqueue", connection = "AzureServiceBusConnection")
public String pushToQueue(
  @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
  final String message,
  @HttpOutput(name = "response") final OutputBinding<T> result ) {
      result.setValue(message + " has been sent.");
      return message;
 }
```

 A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja a `@QueueOutput` Megjegyzések a függvény azon paramétereit, amelyek értékét egy Service Bus üzenetsor számára szeretné írni.  A paraméter típusának olyannak kell lennie `OutputBinding<T>` , ahol a T egy POJO natív Java-típusa.

A Java functions is írhat Service Bus témakörbe. A következő példa a `@ServiceBusTopicOutput` jegyzetet használja a kimeneti kötés konfigurációjának leírásához. 

```java
@FunctionName("sbtopicsend")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @ServiceBusTopicOutput(name = "message", topicName = "mytopicname", subscriptionName = "mysubscription", connection = "ServiceBusConnection") OutputBinding<String> message,
            final ExecutionContext context) {
        
        String name = request.getBody().orElse("Azure Functions");

        message.setValue(name);
        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        
    }
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Az alábbi példa egy Service Bus kimeneti kötést mutat be egy *function.jsa* fájlban, valamint egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja. A függvény egy időzítő trigger használatával küld üzenetsor-üzenetet 15 másodpercenként.

A *function.js* fájlban található kötési adatfájlok:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Itt található a JavaScript-szkript kódja, amely egyetlen üzenetet hoz létre:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = message;
    context.done();
};
```

Az alábbi JavaScript-parancsfájl-kód több üzenetet hoz létre:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = [];
    context.bindings.outputSbQueue.push("1 " + message);
    context.bindings.outputSbQueue.push("2 " + message);
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Az alábbi példa egy Service Bus kimeneti kötést mutat be a fájlban lévő *function.js* , és egy, a kötést használó [PowerShell-függvényt](functions-reference-powershell.md) . 

A *function.js* fájlban található kötési adatfájlok:

```json
{
  "bindings": [
    {
      "type": "serviceBus",
      "direction": "out",
      "connection": "AzureServiceBusConnectionString",
      "name": "outputSbMsg",
      "queueName": "outqueue",
      "topicName": "outtopic"
    }
  ]
}
```

Itt látható a PowerShell, amely egy üzenetet hoz létre a függvény kimenete.

```powershell
param($QueueItem, $TriggerMetadata) 

Push-OutputBinding -Name outputSbMsg -Value @{ 
    name = $QueueItem.name 
    employeeId = $QueueItem.employeeId 
    address = $QueueItem.address 
} 
```

# <a name="python"></a>[Python](#tab/python)

Az alábbi példa bemutatja, hogyan írhat egy Service Bus üzenetsor-várólistára a Pythonban.

Service Bus kötés definíciója van definiálva a *function.jsn* , ahol a *típus* értéke: `serviceBus` .

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
      "type": "serviceBus",
      "direction": "out",
      "connection": "AzureServiceBusConnectionString",
      "name": "msg",
      "queueName": "outqueue"
    }
  ]
}
```

Az *_\_ init_ \_ .* másolás eszközben a metódus értékének megadásával kiírhat egy üzenetet a várólistába `set` .

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C#](#tab/csharp)

A [C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

Az attribútum konstruktora a várólista nevét, illetve a témakört és az előfizetést veszi át. Megadhatja a kapcsolat hozzáférési jogosultságait is. A hozzáférési jogosultságok beállításának kiválasztását a [kimenet – konfiguráció](#configuration) szakasz ismerteti. Az alábbi példa bemutatja a függvény visszatérési értékére alkalmazott attribútumot:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

A tulajdonság beállításával `Connection` megadhatja a használni kívánt Service Bus-kapcsolódási karakterláncot tartalmazó Alkalmazásbeállítások nevét, ahogy az a következő példában is látható:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Teljes példa: [kimenet – példa](#example).

Az `ServiceBusAccount` attribútum segítségével megadhatja az osztály, a metódus vagy a paraméter szintjén használandó Service Bus fiókot.  További információ: [trigger-attributes](functions-bindings-service-bus-trigger.md#attributes-and-annotations).

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A C# parancsfájl nem támogatja az attribútumokat.

# <a name="java"></a>[Java](#tab/java)

A `ServiceBusQueueOutput` és a `ServiceBusTopicOutput` jegyzetek elérhetők egy üzenet függvény kimenetként való írásához. Az ezekkel a megjegyzésekkel díszített paramétert az `OutputBinding<T>` `T` üzenet típusának megfelelő típusként kell deklarálni.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A PowerShell nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a fájl és attribútum *function.jsjában* beállított kötési konfigurációs tulajdonságokat ismerteti `ServiceBus` .

|function.jsa tulajdonságon | Attribútum tulajdonsága |Description|
|---------|---------|----------------------|
|**típusa** | n.a. | "ServiceBus" értékre kell állítani. Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az triggert a Azure Portalban.|
|**irányba** | n.a. | "Out" értékre kell állítani. Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az triggert a Azure Portalban. |
|**név** | n.a. | Annak a változónak a neve, amely a függvény kódjában szereplő üzenetsor vagy témakör üzenetét jelöli. A függvény visszatérési értékének hivatkozásához állítsa a "$return" értéket. |
|**queueName**|**QueueName**|A várólista neve.  Csak akkor állítsa be, ha üzenetsor-üzeneteket küld, nem pedig egy témakörhöz.
|**topicName**|**TopicName**|A témakör neve. Csak akkor állítható be, ha nem várólistára küldi a témakör üzeneteit.|
|**kapcsolat**|**Kapcsolat**|A kötéshez használni kívánt Service Bus kapcsolati karakterláncot tartalmazó Alkalmazásbeállítás neve. Ha az Alkalmazásbeállítások neve "AzureWebJobs" előtaggal kezdődik, akkor csak a név hátralévő részét adhatja meg. Ha például a "MyServiceBus" értékre van állítva `connection` , a functions futtatókörnyezet egy "AzureWebJobsMyServiceBus" nevű alkalmazás-beállítást keres. Ha `connection` üresen hagyja, a functions Runtime az alapértelmezett Service Bus a "AzureWebJobsServiceBus" nevű alkalmazás-beállításban található.<br><br>A kapcsolódási karakterlánc beszerzéséhez kövesse a [felügyeleti hitelesítő adatok beolvasása](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)című cikkben ismertetett lépéseket. A kapcsolódási karakterláncnak egy Service Bus névtérhez kell tartoznia, nem csak egy adott várólistára vagy témakörre.|
|**accessRights** (csak v1)|**Hozzáférés**|Hozzáférési jogosultságok a kapcsolati karakterlánchoz. Az elérhető értékek: `manage` és `listen` . Az alapértelmezett érték a `manage` , amely azt jelzi, hogy a `connection` rendelkezik a **kezelés** engedéllyel. Ha olyan kapcsolódási karakterláncot használ, amely nem rendelkezik a **kezelés** engedéllyel, állítsa a `accessRights` "Listen" (figyelés) lehetőséget. Ellenkező esetben előfordulhat, hogy a functions futtatókörnyezete nem próbálkozik a kezelési jogosultságokat igénylő műveletekkel. A Azure Functions 2. x vagy újabb verziójában ez a tulajdonság nem érhető el, mert a Service Bus SDK legújabb verziója nem támogatja a kezelési műveleteket.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Használat

Azure Functions 1. x esetén a futtatókörnyezet létrehozza a várólistát, ha az nem létezik, és beállította a következőt: `accessRights` `manage` . A functions 2. x vagy újabb verziójában a várólistának vagy a témakörnek már léteznie kell; Ha olyan üzenetsor vagy témakört ad meg, amely nem létezik, a függvény sikertelen lesz. 

# <a name="c"></a>[C#](#tab/csharp)

A kimeneti kötéshez használja a következő paramétereket:

* `out T paramName` - `T` bármely JSON-szerializálható típus lehet. Ha a paraméter értéke null, ha a függvény kilép, a függvény Null objektummal hozza létre az üzenetet.
* `out string` -Ha a paraméter értéke null, ha a függvény kilép, a functions nem hoz létre üzenetet.
* `out byte[]` -Ha a paraméter értéke null, ha a függvény kilép, a functions nem hoz létre üzenetet.
* `out BrokeredMessage` -Ha a paraméter értéke null, ha a függvény kilép, a functions nem hoz létre üzenetet (az 1. x függvények esetében)
* `out Message` – Ha a paraméter értéke null, ha a függvény kilép, a functions nem hoz létre üzenetet (a 2. x és újabb függvények esetében).
* `ICollector<T>` vagy `IAsyncCollector<T>` (aszinkron módszerekhez) – több üzenet létrehozásához. A metódus meghívásakor létrejön egy üzenet `Add` .

C#-függvények használata esetén:

* Az aszinkron függvények visszatérési értékének vagy paraméterének kell lennie `IAsyncCollector` `out` .

* A munkamenet-azonosító eléréséhez kötést kell kötnie egy [`Message`](/dotnet/api/microsoft.azure.servicebus.message) típushoz, és a tulajdonságot kell használnia `sessionId` .

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A kimeneti kötéshez használja a következő paramétereket:

* `out T paramName` - `T` bármely JSON-szerializálható típus lehet. Ha a paraméter értéke null, ha a függvény kilép, a függvény Null objektummal hozza létre az üzenetet.
* `out string` -Ha a paraméter értéke null, ha a függvény kilép, a functions nem hoz létre üzenetet.
* `out byte[]` -Ha a paraméter értéke null, ha a függvény kilép, a functions nem hoz létre üzenetet.
* `out BrokeredMessage` -Ha a paraméter értéke null, ha a függvény kilép, a functions nem hoz létre üzenetet (az 1. x függvények esetében)
* `out Message` – Ha a paraméter értéke null, ha a függvény kilép, a functions nem hoz létre üzenetet (a 2. x és újabb függvények esetében).
* `ICollector<T>` vagy `IAsyncCollector<T>` – több üzenet létrehozásához. A metódus meghívásakor létrejön egy üzenet `Add` .

C#-függvények használata esetén:

* Az aszinkron függvények visszatérési értékének vagy paraméterének kell lennie `IAsyncCollector` `out` .

* A munkamenet-azonosító eléréséhez kötést kell kötnie egy [`Message`](/dotnet/api/microsoft.azure.servicebus.message) típushoz, és a tulajdonságot kell használnia `sessionId` .

# <a name="java"></a>[Java](#tab/java)

A beépített kimeneti kötés helyett használja a [Azure Service Bus SDK](../service-bus-messaging/index.yml) -t.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Az üzenetsor vagy témakör eléréséhez használja a következőt: `context.bindings.<name from function.json>` . Hozzárendelhet egy karakterláncot, egy bájt tömböt vagy egy JavaScript-objektumot (JSON-ként deszerializált) a következőhöz: `context.binding.<name>` .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A Service Bus kimenete a (z) parancsmagon keresztül érhető el, `Push-OutputBinding` ahol olyan argumentumok adhatók át, amelyek megfelelnek a kötés Name paraméterében a fájlban található *function.js* .

# <a name="python"></a>[Python](#tab/python)

A beépített kimeneti kötés helyett használja a [Azure Service Bus SDK](../service-bus-messaging/index.yml) -t.

---

## <a name="exceptions-and-return-codes"></a>Kivételek és visszatérési kódok

| Kötés | Referencia |
|---|---|
| Service Bus | [Service Bus hibakódok](../service-bus-messaging/service-bus-messaging-exceptions.md) |
| Service Bus | [Service Bus korlátok](../service-bus-messaging/service-bus-quotas.md) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Beállítások host.js

Ez a szakasz a kötéshez elérhető globális konfigurációs beállításokat ismerteti 2. x vagy újabb verziókban. Az alábbi példában szereplő host.jscsak a kötés beállításait tartalmazza. A globális konfigurációs beállításokkal kapcsolatos további információkért lásd: [host.jsAzure functions verzióra vonatkozó hivatkozás](functions-host-json.md).

> [!NOTE]
> Az 1. x függvények host.jsának hivatkozását lásd:host.jsaz [ Azure functions 1. x](functions-host-json-v1.md)esetében.

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "prefetchCount": 100,
            "messageHandlerOptions": {
                "autoComplete": true,
                "maxConcurrentCalls": 32,
                "maxAutoRenewDuration": "00:05:00"
            },
            "sessionHandlerOptions": {
                "autoComplete": false,
                "messageWaitTimeout": "00:00:30",
                "maxAutoRenewDuration": "00:55:00",
                "maxConcurrentSessions": 16
            }
        }
    }
}
```

Ha a értékre van `isSessionsEnabled` állítva `true` , a `sessionHandlerOptions` rendszer tiszteletben tartja a következőt:.  Ha a értékre van `isSessionsEnabled` állítva `false` , a `messageHandlerOptions` rendszer tiszteletben tartja a következőt:.

|Tulajdonság  |Alapértelmezett | Description |
|---------|---------|---------|
|prefetchCount|0|Lekérdezi vagy beállítja az üzenet fogadója által egyidejűleg igényelhető üzenetek számát.|
|maxAutoRenewDuration|00:05:00|Az a maximális időtartam, amelyen belül az üzenet zárolása automatikusan meg lesz újítva.|
|Automatikus kiegészítés|true|Azt jelzi, hogy a triggernek a feldolgozás után automatikusan kell-e meghívnia a feladatokat, vagy ha a függvény kódja manuálisan hívja meg a műveletet.<br><br>A beállítás `false` értéke csak C# esetén támogatott.<br><br>Ha a értékre van állítva `true` , akkor az trigger automatikusan befejeződik, ha a függvény végrehajtása sikeresen befejeződött, és a rendszer nem hagyja el az üzenetet.<br><br>Ha a értékre `false` van állítva, akkor a [MessageReceiver](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver) metódusok meghívásával kell meghívnia az üzenetet az üzenet befejezésére, elhagyására vagy kézbesítetlen levelek. Ha kivétel keletkezik (és egyik `MessageReceiver` metódus sem lett meghívva), akkor a zárolás továbbra is fennáll. A zárolás lejárta után a rendszer újra várólistára helyezi az üzenetet, `DeliveryCount` és automatikusan megújítja a zárolást.<br><br>A nem C # függvények esetében a függvényben lévő kivételek a háttérben futó futásidejű hívásokat eredményezik `abandonAsync` . Ha nem történik kivétel, `completeAsync` a rendszer a háttérben hívja meg. |
|maxConcurrentCalls|16|A visszahívás egyidejű hívások maximális száma, amelyet az üzenet-szivattyúnak méretezhető példányon kell kezdeményeznie. Alapértelmezés szerint a függvények futtatókörnyezete egyszerre több üzenetet dolgoz fel.|
|maxConcurrentSessions|2000|A többméretű példányon egyidejűleg kezelhető munkamenetek maximális száma.|

## <a name="next-steps"></a>Következő lépések

- [Függvény futtatása Service Bus üzenetsor vagy témakör-üzenet létrehozásakor (trigger)](./functions-bindings-service-bus-trigger.md)
