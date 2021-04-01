---
title: Azure Functions Azure Event Grid kimeneti kötése
description: Megtudhatja, hogyan küldhet Event Grid eseményt Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: devx-track-csharp, fasttrack-edit, devx-track-python
ms.openlocfilehash: 888afdc2764fed9f0b2c8b548c3e2b1c48e9a31e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97094676"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Azure Functions Azure Event Grid kimeneti kötése

A Event Grid kimeneti kötés használatával írhat eseményeket egyéni témakörbe. [Az egyéni témakörhöz érvényes hozzáférési kulccsal](../event-grid/security-authenticate-publishing-clients.md)kell rendelkeznie.

További információ a telepítésről és a konfigurációról: [Áttekintés](./functions-bindings-event-grid.md).

> [!NOTE]
> A Event Grid kimeneti kötés nem támogatja a közös hozzáférésű aláírásokat (SAS-tokeneket). A témakör elérési kulcsát kell használnia.

> [!IMPORTANT]
> A Event Grid kimeneti kötés csak a 2. x és újabb függvények esetében érhető el.

## <a name="example"></a>Példa

# <a name="c"></a>[C#](#tab/csharp)

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely egy Event Grid egyéni témakörbe ír egy üzenetet, amely a metódus visszatérési értékét használja kimenetként:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

Az alábbi példa azt szemlélteti, hogyan használható az `IAsyncCollector` interfész egy köteg üzenetek küldésére.

```csharp
[FunctionName("EventGridAsyncOutput")]
public static async Task Run(
    [TimerTrigger("0 */5 * * * *")] TimerInfo myTimer,
    [EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]IAsyncCollector<EventGridEvent> outputEvents,
    ILogger log)
{
    for (var i = 0; i < 3; i++)
    {
        var myEvent = new EventGridEvent("message-id-" + i, "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
        await outputEvents.AddAsync(myEvent);
    }
}
```

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

Az alábbi példa a *function.js* fájl Event Grid kimeneti kötési adatokat mutatja be.

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Az alábbi C#-kód egy eseményt hoz létre:

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out EventGridEvent outputEvent, ILogger log)
{
    outputEvent = new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

A következő C#-kód több eseményt hoz létre:

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ICollector<EventGridEvent> outputEvent, ILogger log)
{
    outputEvent.Add(new EventGridEvent("message-id-1", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
    outputEvent.Add(new EventGridEvent("message-id-2", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
}
```

# <a name="java"></a>[Java](#tab/java)

A Event Grid kimeneti kötés nem érhető el a Javához.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Az alábbi példa a *function.js* fájl Event Grid kimeneti kötési adatokat mutatja be.

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Az alábbi JavaScript-kód egyetlen eseményt hoz létre:

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = {
        id: 'message-id',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    };
    context.done();
};
```

Az alábbi JavaScript-kód több eseményt hoz létre:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = [];

    context.bindings.outputEvent.push({
        id: 'message-id-1',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.bindings.outputEvent.push({
        id: 'message-id-2',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Az alábbi példa bemutatja, hogyan konfigurálhat egy függvényt egy Event Gridi esemény üzenetének kimenetére. Az a szakasz, amelyben be `type` van állítva `eventGrid` a Event Grid kimeneti kötés létrehozásához szükséges értékek konfigurálása.

```powershell
{
  "bindings": [
    {
      "type": "eventGrid",
      "name": "outputEvent",
      "topicEndpointUri": "MyEventGridTopicUriSetting",
      "topicKeySetting": "MyEventGridTopicKeySetting",
      "direction": "out"
    },
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
    }
  ]
}
```

A függvényben a paranccsal `Push-OutputBinding` küldhet egy eseményt egy egyéni témakörbe a Event Grid kimeneti kötés használatával.

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = $Request.Query.Message

Push-OutputBinding -Name outputEvent -Value  @{
    id = "1"
    EventType = "testEvent"
    Subject = "testapp/testPublish"
    EventTime = "2020-08-27T21:03:07+00:00"
    Data = @{
        Message = $message
    }
    DataVersion = "1.0"
}

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

# <a name="python"></a>[Python](#tab/python)

Az alábbi példa egy trigger kötést mutat be egy *function.jsa* fájlban, és egy [Python-függvényt](functions-reference-python.md) , amely a kötést használja. Ezután egy eseményben küldi el az egyéni témakört, amelyet a következő ad meg: `topicEndpointUri` .

A *function.js* fájlban található kötési adatfájlok:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    },
    {
      "type": "eventGrid",
      "name": "outputEvent",
      "topicEndpointUri": "MyEventGridTopicUriSetting",
      "topicKeySetting": "MyEventGridTopicKeySetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Itt látható a Python-minta, amely egy eseményt küld egy egyéni témakörre a következő beállításával `EventGridOutputEvent` :

```python
import logging
import azure.functions as func
import datetime

def main(eventGridEvent: func.EventGridEvent, 
         outputEvent: func.Out[func.EventGridOutputEvent]) -> None:

    logging.log("eventGridEvent: ", eventGridEvent)

    outputEvent.set(
        func.EventGridOutputEvent(
            id="test-id",
            data={"tag1": "value1", "tag2": "value2"},
            subject="test-subject",
            event_type="test-event-1",
            event_time=datetime.datetime.utcnow(),
            data_version="1.0"))
```

---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C#](#tab/csharp)

[C# szintű kódtárak](functions-dotnet-class-library.md)esetén használja a [EventGridAttribute](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs) attribútumot.

Az attribútum konstruktora egy olyan Alkalmazásbeállítás nevét adja meg, amely az egyéni témakör nevét és egy, a témakör kulcsát tartalmazó Alkalmazásbeállítás nevét tartalmazza. További információ ezekről a beállításokról: [kimeneti konfiguráció](#configuration). `EventGrid`Példa egy attribútumra:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Teljes példa: [példa](#example).

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A C# parancsfájl nem támogatja az attribútumokat.

# <a name="java"></a>[Java](#tab/java)

A Event Grid kimeneti kötés nem érhető el a Javához.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A PowerShell nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

A Event Grid kimeneti kötés nem érhető el a Pythonhoz.

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a fájl és attribútum *function.jsjában* beállított kötési konfigurációs tulajdonságokat ismerteti `EventGrid` .

|function.jsa tulajdonságon | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**típusa** | n.a. | "EventGrid" értékre kell állítani. |
|**irányba** | n.a. | "Out" értékre kell állítani. Ez a paraméter automatikusan be van állítva, amikor létrehozza a kötést a Azure Portalban. |
|**név** | n.a. | Az eseményt jelölő függvény kódjában használt változó neve. |
|**topicEndpointUri** |**TopicEndpointUri** | Az egyéni témakör URI azonosítóját tartalmazó Alkalmazásbeállítás neve, például: `MyTopicEndpointUri` . |
|**topicKeySetting** |**TopicKeySetting** | Az egyéni témakör elérési kulcsát tartalmazó Alkalmazásbeállítás neve. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Győződjön meg arról, hogy a Configuration ( `TopicEndpointUri` konfiguráció) tulajdonság értékét egy olyan Alkalmazásbeállítás nevére állítja be, amely az egyéni témakör URI-ját tartalmazza. Ne határozza meg az egyéni témakör URI-JÁT közvetlenül ebben a tulajdonságban.

## <a name="usage"></a>Használat

# <a name="c"></a>[C#](#tab/csharp)

Üzenetek küldése a metódus paraméterének használatával, például: `out EventGridEvent paramName` . Több üzenet írásához használhatja a `ICollector<EventGridEvent>` vagy `IAsyncCollector<EventGridEvent>` a helyét `out EventGridEvent` .

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

Üzenetek küldése a metódus paraméterének használatával, például: `out EventGridEvent paramName` . A C#-szkriptben a `paramName` `name` *function.js* tulajdonságában megadott érték van megadva. Több üzenet írásához használhatja a `ICollector<EventGridEvent>` vagy `IAsyncCollector<EventGridEvent>` a helyét `out EventGridEvent` .

# <a name="java"></a>[Java](#tab/java)

A Event Grid kimeneti kötés nem érhető el a Javához.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A kimeneti esemény eléréséhez használja `context.bindings.<name>` a `<name>` `name` *function.js* tulajdonságában megadott értéket.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A kimeneti eseményt a parancsmagot használatával érheti el az `Push-OutputBinding` Event Grid kimeneti kötéshez.

# <a name="python"></a>[Python](#tab/python)

A Event Grid kimeneti kötés nem érhető el a Pythonhoz.

---

## <a name="next-steps"></a>Következő lépések

* [Event Grid esemény elküldése](./functions-bindings-event-grid-trigger.md)
