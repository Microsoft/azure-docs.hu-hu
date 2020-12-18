---
title: RabbitMQ kimeneti kötéseinek Azure Functions
description: Megtudhatja, hogyan küldhet RabbitMQ-üzeneteket Azure Functionsból.
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/17/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: 8715fd3d71a5f65695b045f8a32a1b88bcfdd308
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672542"
---
# <a name="rabbitmq-output-binding-for-azure-functions-overview"></a>RabbitMQ kimeneti kötés a Azure Functions áttekintéséhez

> [!NOTE]
> A RabbitMQ-kötések csak a **Windows Premium és a dedikált** csomagok esetében teljes mértékben támogatottak. A felhasználás és a Linux jelenleg nem támogatott.

Az RabbitMQ kimeneti kötés használatával üzeneteket küldhet egy RabbitMQ-várólistába.

További információ a telepítésről és a konfigurációról: [Áttekintés](functions-bindings-rabbitmq-output.md).

## <a name="example"></a>Példa

# <a name="c"></a>[C#](#tab/csharp)

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely RabbitMQ üzenetet küld, amikor 5 percenként indít el egy TimerTrigger, amely a metódus visszatérési értékét használja kimenetként:

```cs
[FunctionName("RabbitMQOutput")]
[return: RabbitMQ("outputQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

Az alábbi példa bemutatja, hogyan használható az IAsyncCollector felület az üzenetek küldéséhez.

```cs
[FunctionName("RabbitMQOutput")]
public static async Task Run(
[RabbitMQTrigger("sourceQueue", ConnectionStringSetting = "TriggerConnectionString")] string rabbitMQEvent,
[RabbitMQ("destinationQueue", ConnectionStringSetting = "OutputConnectionString")]IAsyncCollector<string> outputEvents,
ILogger log)
{
    // processing:
    var myProcessedEvent = DoSomething(rabbitMQEvent);
    
     // send the message
    await outputEvents.AddAsync(JsonConvert.SerializeObject(myProcessedEvent));
}
```

Az alábbi példa bemutatja, hogyan küldhet üzeneteket a POCOs néven.

```cs
public class TestClass
{
    public string x { get; set; }
}

[FunctionName("RabbitMQOutput")]
public static async Task Run(
[RabbitMQTrigger("sourceQueue", ConnectionStringSetting = "TriggerConnectionString")] TestClass rabbitMQEvent,
[RabbitMQ("destinationQueue", ConnectionStringSetting = "OutputConnectionString")]IAsyncCollector<TestClass> outputPocObj,
ILogger log)
{
    // send the message
    await outputPocObj.Add(rabbitMQEvent);
}
```

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

Az alábbi példa egy RabbitMQ kimeneti kötést mutat be egy *function.jsa* fájlban és egy [C# parancsfájl-függvényt](functions-reference-csharp.md) , amely a kötést használja. A függvény egy HTTP-triggerből olvassa be az üzenetet, és a RabbitMQ-várólistára írja.

A *function.js* fájlban található kötési adatfájlok:

```json
{
    "bindings": [
        {
            "type": "httpTrigger",
            "direction": "in",
            "authLevel": "function",
            "name": "input",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting",
            "direction": "out"
        }
    ]
}
```

A C# szkript kódja:

```csx
using System;
using Microsoft.Extensions.Logging;

public static void Run(string input, out string outputMessage, ILogger log)
{
    log.LogInformation(input);
    outputMessage = input;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Az alábbi példa egy RabbitMQ kimeneti kötést mutat be egy *function.jsa* fájlban, és egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja. A függvény egy HTTP-triggerből olvassa be az üzenetet, és a RabbitMQ-várólistára írja.

A *function.js* fájlban található kötési adatfájlok:

```json
{
    "bindings": [
        {
            "type": "httpTrigger",
            "direction": "in",
            "authLevel": "function",
            "name": "input",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting",
            "direction": "out"
        }
    ]
}
```

Itt található a JavaScript-kód:

```javascript
module.exports = function (context, input) {
    context.bindings.myQueueItem = input.body;
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Az alábbi példa egy RabbitMQ kimeneti kötést mutat be egy *function.jsa* fájlban, és egy Python-függvényt, amely a kötést használja. A függvény egy HTTP-triggerből olvassa be az üzenetet, és a RabbitMQ-várólistára írja.

A *function.js* fájlban található kötési adatfájlok:

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
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting",
            "direction": "out"
        }
    ]
}
```

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:
    input_msg = req.params.get('message')
    msg.set(input_msg)
    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

Az alábbi példa egy Java-függvényt mutat be, amely a RabbitMQ-várólistára küldött üzenetet küld, amikor 5 percenként egy TimerTrigger indítja el.

```java
@FunctionName("RabbitMQOutputExample")
public void run(
@TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
@RabbitMQOutput(connectionStringSetting = "rabbitMQConnectionAppSetting", queueName = "hello") OutputBinding<String> output,
final ExecutionContext context) {
    output.setValue("Some string");
}
```

---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C#](#tab/csharp)

A [C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [RabbitMQAttribute](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/src/RabbitMQAttribute.cs).

Itt található egy `RabbitMQAttribute` attribútum egy metódus-aláírásban:

```csharp
[FunctionName("RabbitMQOutput")]
public static async Task Run(
[RabbitMQTrigger("SourceQueue", ConnectionStringSetting = "TriggerConnectionString")] string rabbitMQEvent,
[RabbitMQ("DestinationQueue", ConnectionStringSetting = "OutputConnectionString")]IAsyncCollector<string> outputEvents,
ILogger log)
{
    ...
}
```

A teljes példa: C# [példa](#example).

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A C# parancsfájl nem támogatja az attribútumokat.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

# <a name="java"></a>[Java](#tab/java)

A `RabbitMQOutput` jegyzet lehetővé teszi, hogy olyan függvényt hozzon létre, amely egy RabbitMQ-üzenet küldésekor fut. Az elérhető konfigurációs lehetőségek közé tartozik a várólista neve és a kapcsolatok karakterláncának neve. A további paraméterek részleteiért keresse fel a [RabbitMQOutput Java-jegyzeteket](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/binding-library/java/src/main/java/com/microsoft/azure/functions/rabbitmq/annotation/RabbitMQOutput.java).

További részletekért tekintse meg a kimeneti kötési [példát](#example) .

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a fájl és attribútum *function.jsjában* beállított kötési konfigurációs tulajdonságokat ismerteti `RabbitMQ` .

|function.jsa tulajdonságon | Attribútum tulajdonsága |Description|
|---------|---------|----------------------|
|**típusa** | n/a | "RabbitMQ" értékre kell állítani.|
|**irányba** | n/a | "Out" értékre kell állítani. |
|**név** | n/a | Annak a változónak a neve, amely a függvény kódjában a várólistát jelképezi. |
|**queueName**|**QueueName**| Azon várólista neve, ahová üzeneteket szeretne küldeni. |
|**hostName**|**HostName**|(ConnectStringSetting használata esetén figyelmen kívül hagyva) <br>A várólista állomásneve (pl.: 10.26.45.210)|
|**userName**|**UserName**|(ConnectionStringSetting használata esetén figyelmen kívül hagyva) <br>Annak az alkalmazás-beállításnak a neve, amely a várólistához való hozzáféréshez használt felhasználónevet tartalmazza. Például: UserNameSetting: "< UserNameFromSettings >"|
|**alaphelyzetbe állítása**|**Jelszó**|(ConnectionStringSetting használata esetén figyelmen kívül hagyva) <br>Annak az alkalmazás-beállításnak a neve, amely a várólista eléréséhez szükséges jelszót tartalmazza. Például: UserNameSetting: "< UserNameFromSettings >"|
|**connectionStringSetting**|**ConnectionStringSetting**|Annak az RabbitMQ a neve, amely az üzenetsor-kapcsolatok karakterláncát tartalmazza. Vegye figyelembe, hogy ha a (z) local.settings.json lévő alkalmazás-beállításon keresztül közvetlenül adja meg a kapcsolatok karakterláncát, akkor az trigger nem fog működni. (Pl.: *function.json*: connectionStringSetting: "rabbitMQConnection" <br> *local.settings.json*: "rabbitMQConnection": "< ActualConnectionstring >")|
|**Port**|**Port**|(ConnectionStringSetting használata esetén figyelmen kívül hagyva) Lekérdezi vagy beállítja a használt portot. Az alapértelmezett érték 0.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Használat

# <a name="c"></a>[C#](#tab/csharp)

A kimeneti kötéshez használja a következő paramétereket:

* `byte[]` -Ha a paraméter értéke null, ha a függvény kilép, a functions nem hoz létre üzenetet.
* `string` -Ha a paraméter értéke null, ha a függvény kilép, a functions nem hoz létre üzenetet.
* `POCO` – Ha a paraméter értéke nem C#-objektumként van formázva, hibaüzenetet kap. A teljes példa: C# [példa](#example).

C#-függvények használata esetén:

* Az aszinkron függvények visszatérési értékének vagy paraméterének kell lennie `IAsyncCollector` `out` .

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A kimeneti kötéshez használja a következő paramétereket:

* `byte[]` -Ha a paraméter értéke null, ha a függvény kilép, a functions nem hoz létre üzenetet.
* `string` -Ha a paraméter értéke null, ha a függvény kilép, a functions nem hoz létre üzenetet.
* `POCO` – Ha a paraméter értéke nem C#-objektumként van formázva, hibaüzenetet kap. Teljes példa: C# parancsfájl- [példa](#example).

C# parancsfájl-függvények használata esetén:

* Az aszinkron függvények visszatérési értékének vagy paraméterének kell lennie `IAsyncCollector` `out` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Az üzenetsor-üzenet a Context. kötések használatával érhető el.<NAME> ahol a <NAME> megegyezik a function.jsáltal megadott névvel. Ha a hasznos adat JSON, az érték deszerializálása egy objektumba történik.

# <a name="python"></a>[Python](#tab/python)

Tekintse meg a Python- [példát](#example).

# <a name="java"></a>[Java](#tab/java)

A kimeneti kötéshez használja a következő paramétereket:

* `byte[]` -Ha a paraméter értéke null, ha a függvény kilép, a functions nem hoz létre üzenetet.
* `string` -Ha a paraméter értéke null, ha a függvény kilép, a functions nem hoz létre üzenetet.
* `POJO` – Ha a paraméter értéke nem Java-objektumként van formázva, hibaüzenetet kap.

---

## <a name="next-steps"></a>Következő lépések

- [Függvény futtatása RabbitMQ-üzenet létrehozásakor (trigger)](./functions-bindings-rabbitmq-trigger.md)
