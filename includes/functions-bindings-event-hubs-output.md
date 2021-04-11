---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: bc2bec364f8d752b7416ecccf0b00d0fbec4c8e8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729758"
---
Az események egy esemény-adatfolyamba való írásához használja a Event Hubs kimeneti kötést. Küldési engedéllyel kell rendelkeznie ahhoz, hogy eseményeket írhasson egy eseményközpontba.

A kimeneti kötés megvalósítása előtt győződjön meg arról, hogy a szükséges csomagokra vonatkozó hivatkozások vannak érvényben.

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Az alábbi példa egy [C#-függvényt](../articles/azure-functions/functions-dotnet-class-library.md) mutat be, amely egy üzenetet ír egy Event hub-ba, a metódus visszatérési értékeként a kimenet:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

Az alábbi példa azt szemlélteti, hogyan használható az `IAsyncCollector` interfész egy köteg üzenetek küldésére. Ez a forgatókönyv gyakori, ha egy adott esemény központból érkező üzeneteket dolgoz fel, és az eredményt egy másik Event hub számára küldi el.

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

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A következő példa egy Event hub eseményindító-kötést mutat be egy *function.jsa* fájlban, és egy [C# parancsfájl-függvényt](../articles/azure-functions/functions-reference-csharp.md) , amely a kötést használja. A függvény egy üzenetet ír az Event hub-ba.

Az alábbi példák a fájl *function.js* Event Hubs kötési adataikat mutatják be. Az első példa a 2. x és újabb függvények esetében, a második pedig az 1. x függvényre mutat. 

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

Az alábbi C#-kód egy üzenetet hoz létre:

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

A következő C#-kód több üzenetet hoz létre:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A következő példa egy Event hub eseményindító kötést mutat be egy *function.jsa* fájlban, és egy [JavaScript-függvényt](../articles/azure-functions/functions-reference-node.md) , amely a kötést használja. A függvény egy üzenetet ír az Event hub-ba.

Az alábbi példák a fájl *function.js* Event Hubs kötési adataikat mutatják be. Az első példa a 2. x és újabb függvények esetében, a második pedig az 1. x függvényre mutat. 

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

Az alábbi JavaScript-kód egyetlen üzenetet küld:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Message created at: " + timeStamp;
    context.done();
};
```

Az alábbi JavaScript-kód több üzenetet küld:

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

# <a name="python"></a>[Python](#tab/python)

A következő példa egy Event hub eseményindító-kötést mutat be egy *function.jsa* fájlban, és egy [Python-függvényt](../articles/azure-functions/functions-reference-python.md) , amely a kötést használja. A függvény egy üzenetet ír az Event hub-ba.

Az alábbi példák a fájl *function.js* Event Hubs kötési adataikat mutatják be.

```json
{
    "type": "eventHub",
    "name": "$return",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

A következő Python-kód egyetlen üzenetet küld:

```python
import datetime
import logging
import azure.functions as func


def main(timer: func.TimerRequest) -> str:
    timestamp = datetime.datetime.utcnow()
    logging.info('Message created at: %s', timestamp)
    return 'Message created at: {}'.format(timestamp)
```

# <a name="java"></a>[Java](#tab/java)

Az alábbi példa egy Java-függvényt mutat be, amely egy Event hub aktuális időpontját tartalmazó üzenetet ír.

```java
@FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 */5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja a `@EventHubOutput` Megjegyzés azon paramétereket, amelyek értékét közzé szeretné tenni az Event hub-ban.  A paraméternek olyan típusúnak kell lennie `OutputBinding<T>` , ahol a T egy POJO vagy bármely natív Java-típus.

---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C#](#tab/csharp)

[C# szintű kódtárak](../articles/azure-functions/functions-dotnet-class-library.md)esetén használja a [EventHubAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubAttribute.cs) attribútumot.

Az attribútum konstruktora az Event hub nevét, valamint egy, a kapcsolati karakterláncot tartalmazó Alkalmazásbeállítás nevét veszi át. További információ ezekről a beállításokról: [kimeneti konfiguráció](#configuration). `EventHub`Példa egy attribútumra:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Teljes példa: [output-C# példa](#example).

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A C# parancsfájl nem támogatja az attribútumokat.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

# <a name="java"></a>[Java](#tab/java)

A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja a [EventHubOutput](/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) -jegyzetet azon paramétereknél, amelyek értékét közzé szeretné tenni az Event hub-ban. A paraméternek típusúnak kell lennie `OutputBinding<T>` , ahol a egy `T` POJO vagy bármely natív Java-típus.

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a fájl és attribútum *function.jsjában* beállított kötési konfigurációs tulajdonságokat ismerteti `EventHub` .

|function.jsa tulajdonságon | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**típusa** | n.a. | "EventHub" értékre kell állítani. |
|**irányba** | n.a. | "Out" értékre kell állítani. Ez a paraméter automatikusan be van állítva, amikor létrehozza a kötést a Azure Portalban. |
|**név** | n.a. | Az eseményt jelölő függvény kódjában használt változó neve. |
|**elérési útja** |**EventHubName** | Csak 1. x függvények. Az Event hub neve. Ha az Event hub neve szerepel a kapcsolati sztringben is, ez az érték felülbírálja ezt a tulajdonságot futásidőben. |
|**eventHubName** |**EventHubName** | A 2. x és újabb függvények. Az Event hub neve. Ha az Event hub neve szerepel a kapcsolati sztringben is, ez az érték felülbírálja ezt a tulajdonságot futásidőben. |
|**kapcsolat** |**Kapcsolat** | Az Event hub névteréhez tartozó kapcsolati sztringet tartalmazó Alkalmazásbeállítás neve. Másolja ezt a kapcsolati karakterláncot a [névtér](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace) **kapcsolati adatok** gombjára kattintva, nem az Event hub-t. Ez a kapcsolati karakterláncnak küldési engedéllyel kell rendelkeznie az üzenet küldéséhez az esemény-adatfolyamba. <br><br>Ha [a bővítmény 5. x vagy újabb verzióját](../articles/azure-functions/functions-bindings-event-hubs.md#event-hubs-extension-5x-and-higher)használja, a kapcsolati karakterlánc helyett megadhat egy olyan konfigurációs szakaszra mutató hivatkozást, amely meghatározza a kapcsolódást. Lásd: [kapcsolatok](../articles/azure-functions/functions-reference.md#connections).|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Használat

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default"></a>Alapértelmezett

Az Event hub kimeneti kötéséhez a következő paramétereket használhatja:

* `string`
* `byte[]`
* `POCO`
* `EventData` – A EventData alapértelmezett tulajdonságai a [Microsoft. Azure. EventHubs névtérben](/dotnet/api/microsoft.azure.eventhubs.eventdata)vannak megadva.

Üzenetek küldése a metódus paraméterének használatával, például: `out string paramName` . A C#-szkriptben a `paramName` `name` *function.js* tulajdonságában megadott érték van megadva. Több üzenet írásához használhatja a `ICollector<string>` vagy `IAsyncCollector<string>` a helyét `out string` .

### <a name="additional-types"></a>További típusok 
Az Event hub-bővítmény 5.0.0 vagy újabb verzióját használó alkalmazások a `EventData` [Microsoft. Azure. EventHubs névtérben](/dotnet/api/microsoft.azure.eventhubs.eventdata)használják a típust az [Azure. Messaging. EventHubs](/dotnet/api/azure.messaging.eventhubs.eventdata) helyett. Ez a verzió a `Body` következő típusok javára elveszíti a örökölt típus támogatását:

- [EventBody](/dotnet/api/azure.messaging.eventhubs.eventdata.eventbody)

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

### <a name="default"></a>Alapértelmezett

Az Event hub kimeneti kötéséhez a következő paramétereket használhatja:

* `string`
* `byte[]`
* `POCO`
* `EventData` – A EventData alapértelmezett tulajdonságai a [Microsoft. Azure. EventHubs névtérben](/dotnet/api/microsoft.azure.eventhubs.eventdata)vannak megadva.

Üzenetek küldése a metódus paraméterének használatával, például: `out string paramName` . A C#-szkriptben a `paramName` `name` *function.js* tulajdonságában megadott érték van megadva. Több üzenet írásához használhatja a `ICollector<string>` vagy `IAsyncCollector<string>` a helyét `out string` .

### <a name="additional-types"></a>További típusok 
Az Event hub-bővítmény 5.0.0 vagy újabb verzióját használó alkalmazások a `EventData` [Microsoft. Azure. EventHubs névtérben](/dotnet/api/microsoft.azure.eventhubs.eventdata)használják a típust az [Azure. Messaging. EventHubs](/dotnet/api/azure.messaging.eventhubs.eventdata) helyett. Ez a verzió a `Body` következő típusok javára elveszíti a örökölt típus támogatását:

- [EventBody](/dotnet/api/azure.messaging.eventhubs.eventdata.eventbody)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A kimeneti esemény eléréséhez használja `context.bindings.<name>` a `<name>` `name` *function.js* tulajdonságában megadott értéket.

# <a name="python"></a>[Python](#tab/python)

Az Event hub-üzenetek egy függvényből való üzembe helyezésének két lehetősége van:

- Visszaadott **érték**: állítsa be `name` *function.js* tulajdonságát a értékre `$return` . Ezzel a konfigurációval a függvény visszatérési értéke Event hub-üzenetként is megmarad.

- **Elengedhetetlen**: adjon meg egy értéket a [set](/python/api/azure-functions/azure.functions.out#set-val--t-----none) metódusnak, amely [kimenő](/python/api/azure-functions/azure.functions.out) típusként van deklarálva. Az átadott érték az `set` Event hub-üzenetként is megmarad.

# <a name="java"></a>[Java](#tab/java)

Az Event hub-üzenetek egy függvényből való üzembe helyezésére két lehetőség áll rendelkezésre a [EventHubOutput](/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) jegyzet használatával:

- Visszaadott **érték**: Ha a jegyzetet a függvényhez alkalmazza, a függvény visszatérési értéke Event hub-üzenetként marad.

- **Elengedhetetlen**: Ha explicit módon be szeretné állítani az üzenet értékét, alkalmazza a jegyzetet egy adott paraméterre [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.OutputBinding) , ahol a egy `T` POJO vagy bármely natív Java-típus. Ezzel a konfigurációval a metódus értékének átadása az `setValue` Event hub-üzenetként megőrzi az értéket.

---

## <a name="exceptions-and-return-codes"></a>Kivételek és visszatérési kódok

| Kötés | Referencia |
|---|---|
| Eseményközpont | [Üzemeltetési útmutató](/rest/api/eventhub/publisher-policy-operations) |