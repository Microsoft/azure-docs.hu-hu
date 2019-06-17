---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 421e0db48f045c5cbce52a0641902e6d2a11276e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66132460"
---
## <a name="trigger"></a>Eseményindító

A függvény trigger használatával egy event hub eseménystream küldött esemény válaszolni. Az eseményindító beállítása az alapul szolgáló eseményközpont olvasási hozzáféréssel kell rendelkeznie. A függvény akkor aktiválódik, ha az üzenet a függvénynek átadott írta be a karakterlánc formájában.

## <a name="trigger---scaling"></a>Trigger - méretezés

Az esemény által aktivált függvény minden példánya egyetlen biztonsági [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) példány. Az eseményindító (az Event Hubs működteti) biztosítja, hogy csak egy [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) példány beszerezheti a címbérletet egy adott partíción.

Vegyük példaként egy Eseményközpontba módon:

* 10 partíciók
* 1000 esemény oszlanak el minden, az egyes partíciók 100 üzenetekkel

Ha a függvény első engedélyezve van, nincs a függvény csak egy példánya. Adjuk meg az első függvény példány `Function_0`. A `Function_0` függvénynek egyetlen példánya [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) , amely rendelkezik az összes tíz partíción. Ez a példány események olvasásakor a partíciók 0-9. Ettől kezdve a következők történnek:

* **Nincs szükség új függvény példányok**: `Function_0` a funkciók logic skálázás érvénybe lépése előtt minden 1000 esemény feldolgozására képes. Ebben az esetben az összes 1000 üzenetek dolgozza fel `Function_0`.

* **Egy további függvény példány kerül hozzáadásra**: Ha az funkciók logic skálázás határozza meg, amely `Function_0` további üzeneteket, mint amennyit fel tud dolgozni, egy új funkció-példányt tartalmaz (`Function_1`) jön létre. Az új funkció is rendelkezik egy kapcsolódó példányát [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor). Az alapul szolgáló Event Hubs felderítheti, hogy egy új gazdagép-példányt az üzenetek olvasásakor a megpróbálja, egyenlegek betöltése a partíciók között a saját példányait üzemeltetni. Ha például a partíciók 0 és 4 közötti rendelhetők hozzá `Function_0` és 5 – 9, particionálja `Function_1`.

* **További függvény példányt adja hozzá N**: Ha az funkciók logic skálázás határozza meg, amely mindkét `Function_0` és `Function_1` további üzeneteket, mint azok képes feldolgozni, új `Functions_N` függvény alkalmazáspéldány jönnek létre.  Alkalmazások jönnek létre a pont, ahol `N` nagyobb, mint az event hub-partíciók száma. Ebben a példában az Event Hubs újra elosztja a terhelést a partíciók ebben az esetben a példányok között `Function_0`... `Functions_9`.

Amikor függvények skálázását követve rugalmasan méretezhető, `N` példányok az a szám, amely nagyobb, mint az event hub-partíciók száma. Ez azért van szükség, [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) példányok érhetők el a többi példány elérhetővé váló, szerezze be a partíciók zárolását. Csak amikor végrehajtja a függvényt példány használt erőforrások díjkötelesek. Más szóval nem terheli a fölösleges üzembe.

Minden függvény végrehajtása befejeződött (a vagy hibák nélkül), ellenőrzőpontok bővül a társított storage-fiókban. Ha sikeres ellenőrzőpontos, 1000 üzenetek soha nem újra beolvasása.

## <a name="trigger---example"></a>Az eseményindító – példa

Tekintse meg az adott nyelvű példa:

* [C#](#trigger---c-example)
* [C# script (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Eseményindító - C#-példa

A következő példa bemutatja egy [C#-függvény](../articles/azure-functions/functions-dotnet-class-library.md) , amely naplózza az eseményközpont-eseményindító üzenet törzse.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Hozzáférhet a [esemény-metaadatok](#trigger---event-metadata) függvénykód, a kötést létrehozni egy [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) objektum (egy használata szükséges a utasítás `Microsoft.Azure.EventHubs`). Az azonos tulajdonságokkal a podpis metody kötés kifejezések használatával is elérheti.  Az alábbi példában is lekérdezheti ugyanezen adatokat módszert mutat be:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run(
    [EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    ILogger log)
{
    log.LogInformation($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    // Metadata accessed by binding to EventData
    log.LogInformation($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.LogInformation($"Offset={myEventHubMessage.SystemProperties.Offset}");
    // Metadata accessed by using binding expressions in method parameters
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={sequenceNumber}");
    log.LogInformation($"Offset={offset}");
}
```

Események fogadása a kötegben, győződjön meg arról, `string` vagy `EventData` tömböt.  

> [!NOTE]
> Nem köthető a módszerparaméterekre kötegben fogadásakor, például a fenti példában a `DateTime enqueuedTimeUtc` és kell kapnia, ezek az egyes `EventData` objektum  

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData[] eventHubMessages, ILogger log)
{
    foreach (var message in eventHubMessages)
    {
        log.LogInformation($"C# function triggered to process a message: {Encoding.UTF8.GetString(message.Body)}");
        log.LogInformation($"EnqueuedTimeUtc={message.SystemProperties.EnqueuedTimeUtc}");
    }
}
```

### <a name="trigger---c-script-example"></a>Eseményindító - C#-szkript példa

Az alábbi példa bemutatja egy eseményközpont-eseményindító a kötés egy *function.json* fájl és a egy [C#-szkriptfüggvény](../articles/azure-functions/functions-reference-csharp.md) , amely a kötés használja. A függvény naplózza az eseményközpont-eseményindító üzenet törzse.

Az alábbi példák bemutatják az Event Hubs-kötés adatok a *function.json* fájlt.

#### <a name="version-2x"></a>Verzió 2.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Verzió 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Íme a C#-szkriptkódot:

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Hozzáférhet a [esemény-metaadatok](#trigger---event-metadata) függvénykód, a kötést létrehozni egy [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) objektum (egy használata szükséges a utasítás `Microsoft.Azure.EventHubs`). Az azonos tulajdonságokkal a podpis metody kötés kifejezések használatával is elérheti.  Az alábbi példában is lekérdezheti ugyanezen adatokat módszert mutat be:

```cs
#r "Microsoft.Azure.EventHubs"

using System.Text;
using System;
using Microsoft.ServiceBus.Messaging;
using Microsoft.Azure.EventHubs;

public static void Run(EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.SystemProperties.Offset}");

    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

Események fogadása a kötegben, győződjön meg arról, `string` vagy `EventData` tömböt:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

### <a name="trigger---f-example"></a>Eseményindító - F# példa

Az alábbi példa bemutatja egy eseményközpont-eseményindító a kötés egy *function.json* fájl és a egy [ F# függvény](../articles/azure-functions/functions-reference-fsharp.md) , amely a kötés használja. A függvény naplózza az eseményközpont-eseményindító üzenet törzse.

Az alábbi példák bemutatják az Event Hubs-kötés adatok a *function.json* fájlt. 

#### <a name="version-2x"></a>Verzió 2.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Verzió 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Íme a F# kódot:

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Log(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

### <a name="trigger---javascript-example"></a>Eseményindító - JavaScript-példa

Az alábbi példa bemutatja egy eseményközpont-eseményindító a kötés egy *function.json* fájl és a egy [JavaScript-függvény](../articles/azure-functions/functions-reference-node.md) , amely a kötés használja. A függvény beolvassa [esemény-metaadatok](#trigger---event-metadata) és naplózza az üzenetet.

Az alábbi példák bemutatják az Event Hubs-kötés adatok a *function.json* fájlt.

#### <a name="version-2x"></a>Verzió 2.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Verzió 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

A következő JavaScript-kódot:

```javascript
module.exports = function (context, eventHubMessage) {
    context.log('Function triggered to process a message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);

    context.done();
};
```

Egy kötegben események fogadásához állítsa `cardinality` való `many` a a *function.json* fájljához a következő példákban látható módon.

#### <a name="version-2x"></a>Verzió 2.x

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Verzió 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "path": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

A következő JavaScript-kódot:

```javascript
module.exports = function (context, eventHubMessages) {
    context.log(`JavaScript eventhub trigger function called for message array ${eventHubMessages}`);

    eventHubMessages.forEach((message, index) => {
        context.log(`Processed message ${message}`);
        context.log(`EnqueuedTimeUtc = ${context.bindingData.enqueuedTimeUtcArray[index]}`);
        context.log(`SequenceNumber = ${context.bindingData.sequenceNumberArray[index]}`);
        context.log(`Offset = ${context.bindingData.offsetArray[index]}`);
    });

    context.done();
};
```

### <a name="trigger---python-example"></a>Eseményindító - Python-példát

Az alábbi példa bemutatja egy eseményközpont-eseményindító a kötés egy *function.json* fájl és a egy [funkce Pythonu](../articles/azure-functions/functions-reference-python.md) , amely a kötés használja. A függvény beolvassa [esemény-metaadatok](#trigger---event-metadata) és naplózza az üzenetet.

Az alábbi példák bemutatják az Event Hubs-kötés adatok a *function.json* fájlt.

```json
{
  "type": "eventHubTrigger",
  "name": "event",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

A Python-kód itt látható:

```python
import logging
import azure.functions as func

def main(event: func.EventHubEvent):
    logging.info('Function triggered to process a message: ', event.get_body())
    logging.info('  EnqueuedTimeUtc =', event.enqueued_time)
    logging.info('  SequenceNumber =', event.sequence_number)
    logging.info('  Offset =', event.offset)
```

### <a name="trigger---java-example"></a>Eseményindító - Java-példában

Az alábbi példa bemutatja egy Eseményközpont-eseményindító a kötés egy *function.json* fájl és a egy [Java függvény](../articles/azure-functions/functions-reference-java.md) , amely a kötés használja. A függvény naplózza az Eseményközpont-eseményindító üzenet törzse.

```json
{
  "type": "eventHubTrigger",
  "name": "msg",
  "direction": "in",
  "eventHubName": "myeventhubname",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

```java
@FunctionName("ehprocessor")
public void eventHubProcessor(
  @EventHubTrigger(name = "msg",
                  eventHubName = "myeventhubname",
                  connection = "myconnvarname") String message,
       final ExecutionContext context )
       {
          context.getLogger().info(message);
 }
```

 Az a [Java-függvények futásidejű kódtár](/java/api/overview/azure/functions/runtime), használja a `EventHubTrigger` jegyzet paraméterekkel, amelynek az értéke az Event Hubs lenne származnak. Ezek a jegyzetek a paraméterek miatt a funkció futtatását, amikor az esemény érkezik.  A jegyzet használható natív Java-típusokat, POJOs vagy nullázható értékek használata nem kötelező<T>.

## <a name="trigger---attributes"></a>Eseményindító - attribútumok

A [C#-osztálykódtárakat](../articles/azure-functions/functions-dotnet-class-library.md), használja a [EventHubTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs) attribútum.

Az attribútum konstruktorának paramétereként meg az eseményközpont nevével, a fogyasztói csoport neve és a kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. Ezekkel a beállításokkal kapcsolatos további információkért lásd: a [konfigurációs szakasz aktiválása](#trigger---configuration). Íme egy `EventHubTriggerAttribute` attribútum példa:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

Egy teljes példa: [eseményindító – C#-példa](#trigger---c-example).

## <a name="trigger---configuration"></a>Eseményindító - konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `EventHubTrigger` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a | Meg kell `eventHubTrigger`. Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon.|
|**direction** | n/a | Meg kell `in`. Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon. |
|**name** | n/a | A függvénykód esemény elemet képviselő változó neve. |
|**path** |**EventHubName** | Csak 1.x működik. Az eseményközpont neve. Ha az eseményközpont neve is szerepel a kapcsolati karakterláncot, ezt az értéket felülbírálja ezt a tulajdonságot, futásidőben. |
|**eventHubName** |**EventHubName** | Csak a 2.x működik. Az eseményközpont neve. Ha az eseményközpont neve is szerepel a kapcsolati karakterláncot, ezt az értéket felülbírálja ezt a tulajdonságot, futásidőben. |
|**consumerGroup** |**a fogyasztói csoporttól** | Egy nem kötelező tulajdonsággal, amely beállítja a [fogyasztói csoportot](../articles/event-hubs/event-hubs-features.md)#event-felhasználói) használt előfizetni a hubon eseményeket. Ha nincs megadva, a `$Default` fogyasztói csoportot használja. |
|**számossága** | n/a | A Javascripthez. Állítsa be `many` , és engedélyezze a kötegelés.  Ha nincs megadva vagy `one`, függvénynek átadott egyetlen üzenetben. |
|**kapcsolat** |**kapcsolat** | Az event hubs-névtér a kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. Ez a kapcsolati karakterlánc másolása kattintva a **kapcsolatadatok** gombot a [névtér](../articles/event-hubs/event-hubs-create.md)#create-az-event-hubs-névtér), nem pedig maga az event hubs. Ez a kapcsolati karakterlánc legalább olvasás engedéllyel rendelkezik az eseményindító aktiválása.|
|**path**|**EventHubName**|Az eseményközpont neve. Alkalmazásbeállítások keresztül lehet rá hivatkozni `%eventHubName%`|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="trigger---event-metadata"></a>Eseményindító - esemény-metaadatok

Az Event Hubs-eseményindító biztosít több [metaadat-tulajdonságot](../articles/azure-functions/./functions-bindings-expressions-patterns.md). Ezek a tulajdonságok a kötési kifejezésekben való használata más kötések részeként vagy a kód paramétereiben használható. Ezek a tulajdonságai a [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata) osztály.

|Tulajdonság|Típus|Leírás|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|A `PartitionContext` példány.|
|`EnqueuedTimeUtc`|`DateTime`|A várólistán lévő időpontja (UTC).|
|`Offset`|`string`|Az adatokat az Event Hub-partíció adatfolyam képest eltolva. Az eltolás egy nyílhegy vagy az Event Hubs streamen belül esemény azonosítója. Az azonosító, amely egyedi az Event Hubs-adatfolyam egy partíción belül.|
|`PartitionKey`|`string`|A partíció, amely esetben adatok rendszer.|
|`Properties`|`IDictionary<String,Object>`|A felhasználó tulajdonságai az eseményadatokat.|
|`SequenceNumber`|`Int64`|Az esemény logikai sorrendben száma.|
|`SystemProperties`|`IDictionary<String,Object>`|A rendszer-tulajdonságokat, beleértve az eseményadatokat.|

Lásd: [kódpéldák](#trigger---example) , használja ezeket a tulajdonságokat a cikk korábbi részében.

## <a name="trigger---hostjson-properties"></a>Eseményindító - host.json tulajdonságai

A [host.json](../articles/azure-functions/functions-host-json.md#eventhub) fájl az Event Hubs eseményindító viselkedését vezérlő beállításokat tartalmaz.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>Kimenet

Az Event Hubs-kimeneti kötését beírni az eseményeket az eseménystream használata. Eseményeket írni egy eseményközpontba küldési engedéllyel kell rendelkeznie.

Ellenőrizze, hogy a szükséges csomag hivatkozást a rendszer: Függvények 1.x vagy 2.x függvények

## <a name="output---example"></a>Kimenete – példa

Tekintse meg az adott nyelvű példa:

* [C#](#output---c-example)
* [C# script (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [Java](#output---java-example)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Kimenet – C#-példa

A következő példa bemutatja egy [C#-függvény](../articles/azure-functions/functions-dotnet-class-library.md) , amely üzenetet ír egy eseményközpontba, a metódus visszatérési érték a kimeneteként:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

A következő minta bemutatja, hogyan használhatja a `IAsyncCollector` felület kötegelt üzenetek küldéséhez. Ebben a forgatókönyvben a közös üzeneteket egy eseményközpontból érkező és az eredmény küld egy másik Event Hub feldolgozásakor.

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest", Connection = "EventHubConnectionAppSetting")]IAsyncCollector<string> outputEvents,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // do some processing:
        var myProcessedEvent = DoSomething(eventData);

        // then send the message
        await outputEvents.AddAsync(JsonConvert.SerializeObject(myProcessedEvent));
    }
}
```

### <a name="output---c-script-example"></a>Kimenet – C#-szkript példa

Az alábbi példa bemutatja egy eseményközpont-eseményindító a kötés egy *function.json* fájl és a egy [C#-szkriptfüggvény](../articles/azure-functions/functions-reference-csharp.md) , amely a kötés használja. A függvény üzenetet ír egy eseményközpontba.

Az alábbi példák bemutatják az Event Hubs-kötés adatok a *function.json* fájlt. Az első példa 2.x működik, és a másikat az funkciók 1.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Itt látható C#-szkriptkódot, amely egy üzenetet hoz létre:

```cs
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, ILogger log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.LogInformation(msg);   
    outputEventHubMessage = msg;
}
```

Az alábbiakban C#-szkriptkódot, amely több üzenetet hoz létre:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Kimenete – F# példa

Az alábbi példa bemutatja egy eseményközpont-eseményindító a kötés egy *function.json* fájl és a egy [ F# függvény](../articles/azure-functions/functions-reference-fsharp.md) , amely a kötés használja. A függvény üzenetet ír egy eseményközpontba.

Az alábbi példák bemutatják az Event Hubs-kötés adatok a *function.json* fájlt. Az első példa 2.x működik, és a másikat az funkciók 1.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```
```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Íme a F# kódot:

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: ILogger) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.LogInformation(msg);
    outputEventHubMessage <- msg;
```

### <a name="output---javascript-example"></a>Kimenet – JavaScript-példa

Az alábbi példa bemutatja egy eseményközpont-eseményindító a kötés egy *function.json* fájl és a egy [JavaScript-függvény](../articles/azure-functions/functions-reference-node.md) , amely a kötés használja. A függvény üzenetet ír egy eseményközpontba.

Az alábbi példák bemutatják az Event Hubs-kötés adatok a *function.json* fájlt. Az első példa 2.x működik, és a másikat az funkciók 1.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Az alábbiakban JavaScript-kódot, amely egy üzenet küld:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Message created at: " + timeStamp;
    context.done();
};
```

Az alábbiakban JavaScript-kódot, amely több üzenetet küld:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

### <a name="output---python-example"></a>Kimenet – Python-példát

Az alábbi példa bemutatja egy eseményközpont-eseményindító a kötés egy *function.json* fájl és a egy [funkce Pythonu](../articles/azure-functions/functions-reference-python.md) , amely a kötés használja. A függvény üzenetet ír egy eseményközpontba.

Az alábbi példák bemutatják az Event Hubs-kötés adatok a *function.json* fájlt.

```json
{
    "type": "eventHub",
    "name": "$return",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Itt a Python-kód, amely egy üzenet küld:

```python
import datetime
import logging
import azure.functions as func

def main(timer: func.TimerRequest) -> str:
    timestamp = datetime.datetime.utcnow()
    logging.info('Message created at: %s', timestamp);   
    return 'Message created at: {}'.format(timestamp)
```

### <a name="output---java-example"></a>Kimenet – Java-példában

Az alábbi példa bemutatja egy Java-függvény, amely az aktuális időt egy üzenet csipesz ír egy eseményközpontba.

```java
@}FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 *&#47;5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

Az a [Java-függvények futásidejű kódtár](/java/api/overview/azure/functions/runtime), használja a `@EventHubOutput` jegyzet paraméterekkel, amelynek az értéke kívánják közzétenni az Eseményközpontba.  A paraméter típusúnak kell lennie `OutputBinding<T>` , ahol a T egy pojo-vá vagy bármilyen natív Java-típus.

## <a name="output---attributes"></a>Kimenet – attribútumok

A [C#-osztálykódtárakat](../articles/azure-functions/functions-dotnet-class-library.md), használja a [EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs) attribútum.

Az attribútum konstruktorának paramétereként meg az eseményközpont nevét és a egy kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. Ezekkel a beállításokkal kapcsolatos további információkért lásd: [kimenete – konfigurációs](#output---configuration). Íme egy `EventHub` attribútum példa:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Egy teljes példa: [kimenet – C#-példa](#output---c-example).

## <a name="output---configuration"></a>Kimenete – konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `EventHub` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a | "Az eventHub" kell állítani. |
|**direction** | n/a | Állítsa "out". Ez a paraméter értéke automatikusan a kötés létrehozásakor az Azure Portalon. |
|**name** | n/a | A függvény kódját a esemény a használt változó neve. |
|**path** |**EventHubName** | Csak 1.x működik. Az eseményközpont neve. Ha az eseményközpont neve is szerepel a kapcsolati karakterláncot, ezt az értéket felülbírálja ezt a tulajdonságot, futásidőben. |
|**eventHubName** |**EventHubName** | Csak a 2.x működik. Az eseményközpont neve. Ha az eseményközpont neve is szerepel a kapcsolati karakterláncot, ezt az értéket felülbírálja ezt a tulajdonságot, futásidőben. |
|**kapcsolat** |**kapcsolat** | Az event hubs-névtér a kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. Ez a kapcsolati karakterlánc másolása kattintva a **kapcsolatadatok** gombot a *névtér*, nem pedig maga az event hubs. Ez a kapcsolati karakterlánc az üzenet küldéséhez az eseménystream küldési jogosultsággal kell rendelkeznie.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Kimenet – használat

C# és a C#-szkript, üzeneteket küldhet egy metódus-paramétert használatával `out string paramName`. A C#-szkript `paramName` az értéket a megadott a `name` tulajdonsága *function.json*. Több üzenetet ír, használható `ICollector<string>` vagy `IAsyncCollector<string>` helyén `out string`.

A JavaScript, elérni a kimeneti esemény `context.bindings.<name>`. `<name>` az érték szerepel a `name` tulajdonsága *function.json*.

## <a name="exceptions-and-return-codes"></a>Kivételek és a visszatérési kódok

| Kötés | Leírások |
|---|---|
| Eseményközpont | [Üzemeltetési útmutató](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Host.JSON-beállítások

Ez a szakasz ismerteti a globális konfigurációs beállításoknak a kötéshez verziójában elérhető 2.x. Az alábbi példa host.json-fájl csak a verzió 2.x beállításait tartalmazza ezt a kötést. További információ a globális konfigurációs beállításoknak verzióban 2.x verzióját, lásd: [verzióját az Azure Functions – host.json referencia 2.x](../articles/azure-functions/functions-host-json.md).

> [!NOTE]
> Az a funkciók host.json odkaz 1.x, lásd: [Azure Functions – host.json referencia 1.x](../articles/azure-functions/functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            }
        }
    }
}  
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------|
|maxBatchSize|64|A ciklust a fogadás egy fogadott események maximális száma.|
|prefetchCount|n/a|Az alapértelmezett PrefetchCount, amely az alapul szolgáló EventProcessorHost használni fog.|
|batchCheckpointFrequency|1|Az EventHub kurzor ellenőrzőpont létrehozása előtt feldolgozható kötegek esemény száma.|