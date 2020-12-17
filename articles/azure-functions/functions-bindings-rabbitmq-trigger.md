---
title: RabbitMQ-trigger Azure Functions
description: Ismerje meg, hogyan futtathat egy Azure-függvényt egy RabbitMQ-üzenet létrehozásakor.
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/15/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: 26dee5200a60f4900ed20c2fd49a874552272776
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617221"
---
# <a name="rabbitmq-trigger-for-azure-functions-overview"></a>RabbitMQ-trigger Azure Functions – áttekintés

> [!NOTE]
> A RabbitMQ-kötések csak a **Windows prémium** csomagokban teljes mértékben támogatottak. A felhasználás és a Linux jelenleg nem támogatott.

A RabbitMQ trigger használatával válaszolhat az RabbitMQ-üzenetsor üzeneteire.

További információ a telepítésről és a konfigurációról: [Áttekintés](functions-bindings-rabbitmq.md).

## <a name="example"></a>Példa

# <a name="c"></a>[C#](#tab/csharp)

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely a RabbitMQ üzenetet olvassa és naplózza [RabbitMQ-eseményként](https://www.rabbitmq.com/releases/rabbitmq-dotnet-client/v3.2.2/rabbitmq-dotnet-client-3.2.2-client-htmldoc/html/type-RabbitMQ.Client.Events.BasicDeliverEventArgs.html):

```cs
[FunctionName("RabbitMQTriggerCSharp")]
public static void RabbitMQTrigger_BasicDeliverEventArgs(
    [RabbitMQTrigger("queue", ConnectionStringSetting = "rabbitMQConnection")] BasicDeliverEventArgs args,
    ILogger logger
    )
{
    logger.LogInformation($"C# RabbitMQ queue trigger function processed message: {Encoding.UTF8.GetString(args.Body)}");
}
```

Az alábbi példa bemutatja, hogyan olvashatja el az üzenetet POCOként.

```cs
public class TestClass
{
    public string x { get; set; }
}

[FunctionName("RabbitMQTriggerCSharp")]
public static void RabbitMQTrigger_BasicDeliverEventArgs(
    [RabbitMQTrigger("queue", ConnectionStringSetting = "rabbitMQConnection")] TestClass pocObj,
    ILogger logger
    )
{
    logger.LogInformation($"C# RabbitMQ queue trigger function processed message: {Encoding.UTF8.GetString(pocObj)}");
}
```

A JSON-objektumokhoz hasonlóan hiba történik, ha az üzenet nem megfelelően van formázva C#-objektumként. Ha igen, akkor a pocObj változóhoz lesz kötve, amely a esetében használható, ami szükséges ahhoz, amire szüksége lehet.

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

Az alábbi példa egy RabbitMQ-trigger kötést mutat be egy *function.jsa* fájlban, és egy [C# parancsfájl-függvényt](functions-reference-csharp.md) , amely a kötést használja. A függvény beolvassa és naplózza a RabbitMQ üzenetet.

A *function.js* fájlban található kötési adatfájlok:

```json
{
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnection"
        }
    ]
}
```

A C# szkript kódja:

```csx
using System;

public static void Run(string myQueueItem, ILogger log)
{
    log.LogInformation($"C# Script RabbitMQ trigger function processed: {myQueueItem}");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Az alábbi példa egy RabbitMQ-trigger kötést mutat be egy *function.jsa* fájlban, és egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja. A függvény RabbitMQ üzenetet olvas és naplóz.

A *function.js* fájlban található kötési adatfájlok:

```json
{
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnection"
        }
    ]
}
```

Itt látható a JavaScript-szkript kódja:

```javascript
module.exports = async function (context, myQueueItem) {
    context.log('JavaScript RabbitMQ trigger function processed work item', myQueueItem);
};
```

# <a name="python"></a>[Python](#tab/python)

Az alábbi példa bemutatja, hogyan olvashat egy RabbitMQ üzenetsor-üzenetet egy trigger használatával.

RabbitMQ kötés van definiálva a *function.js* , ahol a *típus* értéke: `RabbitMQTrigger` .

```json
{
    "scriptFile": "__init__.py",
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnection"
        }
    ]
}
```

```python
import logging
import azure.functions as func

def main(myQueueItem) -> None:
    logging.info('Python RabbitMQ trigger function processed a queue item: %s', myQueueItem)
```

# <a name="java"></a>[Java](#tab/java)

A következő Java-függvény a `@RabbitMQTrigger` [Java RabbitMQ-típusok](https://mvnrepository.com/artifact/com.microsoft.azure.functions/azure-functions-java-library-rabbitmq) jegyzetét használja a RabbitMQ-várólista-trigger konfigurációjának leírásához. A függvény megragadja a várólistára helyezett üzenetet, és hozzáadja azt a naplókhoz.

```java
@FunctionName("RabbitMQTriggerExample")
public void run(
    @RabbitMQTrigger(connectionStringSetting = "rabbitMQConnection", queueName = "queue") String input,
    final ExecutionContext context)
{
    context.getLogger().info("Java HTTP trigger processed a request." + input);
}
```

---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C#](#tab/csharp)

A [C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [RabbitMQTrigger](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/src/Trigger/RabbitMQTriggerAttribute.cs) attribútumot.

Itt található egy `RabbitMQTrigger` attribútum egy metódus-aláírásban:

```csharp
[FunctionName("RabbitMQTest")]
public static void RabbitMQTest([RabbitMQTrigger("queue")] string message, ILogger log)
{
    ...
}
```

A teljes példa: C# példa.

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A C# parancsfájl nem támogatja az attribútumokat.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

# <a name="java"></a>[Java](#tab/java)

A `RabbitMQTrigger` jegyzet lehetővé teszi, hogy olyan függvényt hozzon létre, amely egy RabbitMQ-üzenet létrehozásakor fut. Az elérhető konfigurációs lehetőségek közé tartozik a várólista neve és a kapcsolatok karakterláncának neve. A további paraméterek részleteiért keresse fel a [RabbitMQTrigger Java-jegyzeteket](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/binding-library/java/src/main/java/com/microsoft/azure/functions/rabbitmq/annotation/RabbitMQTrigger.java).

További részletekért tekintse meg az trigger [példáját](#example) .

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a fájl és attribútum *function.jsjában* beállított kötési konfigurációs tulajdonságokat ismerteti `RabbitMQTrigger` .

|function.jsa tulajdonságon | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**típusa** | n/a | "RabbitMQTrigger" értékre kell állítani.|
|**irányba** | n/a | "In" értékre kell állítani.|
|**név** | n/a | Annak a változónak a neve, amely a függvény kódjában a várólistát jelképezi. |
|**queueName**|**QueueName**| Azon várólista neve, amelyről üzeneteket szeretne fogadni. |
|**hostName**|**HostName**|(ConnectStringSetting használata esetén figyelmen kívül hagyva) <br>A várólista állomásneve (pl.: 10.26.45.210)|
|**userNameSetting**|**UserNameSetting**|(ConnectionStringSetting használata esetén figyelmen kívül hagyva) <br>Annak az alkalmazás-beállításnak a neve, amely a várólistához való hozzáféréshez használt felhasználónevet tartalmazza. Például: UserNameSetting: "% < UserNameFromSettings >%"|
|**passwordSetting**|**PasswordSetting**|(ConnectionStringSetting használata esetén figyelmen kívül hagyva) <br>Annak az alkalmazás-beállításnak a neve, amely a várólista eléréséhez szükséges jelszót tartalmazza. Például: PasswordSetting: "% < PasswordFromSettings >%"|
|**connectionStringSetting**|**ConnectionStringSetting**|Annak az RabbitMQ a neve, amely az üzenetsor-kapcsolatok karakterláncát tartalmazza. Vegye figyelembe, hogy ha a (z) local.settings.json lévő alkalmazás-beállításon keresztül közvetlenül adja meg a kapcsolatok karakterláncát, akkor az trigger nem fog működni. (Pl.: *function.json*: connectionStringSetting: "rabbitMQConnection" <br> *local.settings.json*: "rabbitMQConnection": "< ActualConnectionstring >")|
|**Port**|**Port**|(ConnectionStringSetting használata esetén figyelmen kívül hagyva) Lekérdezi vagy beállítja a használt portot. Az alapértelmezett érték 0.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Használat

# <a name="c"></a>[C#](#tab/csharp)

Az alapértelmezett üzenet típusa [RabbitMQ esemény](https://www.rabbitmq.com/releases/rabbitmq-dotnet-client/v3.2.2/rabbitmq-dotnet-client-3.2.2-client-htmldoc/html/type-RabbitMQ.Client.Events.BasicDeliverEventArgs.html), és a `Body` RabbitMQ esemény tulajdonsága az alább felsorolt típusok szerint olvasható:

* `An object serializable as JSON` – Az üzenet érvényes JSON-sztringként lesz továbbítva.
* `string`
* `byte[]`
* `POCO` – Az üzenet C#-objektumként van formázva. A teljes példa: C# [példa](#example).

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

Az alapértelmezett üzenet típusa [RabbitMQ esemény](https://www.rabbitmq.com/releases/rabbitmq-dotnet-client/v3.2.2/rabbitmq-dotnet-client-3.2.2-client-htmldoc/html/type-RabbitMQ.Client.Events.BasicDeliverEventArgs.html), és a `Body` RabbitMQ esemény tulajdonsága az alább felsorolt típusok szerint olvasható:

* `An object serializable as JSON` – Az üzenet érvényes JSON-sztringként lesz továbbítva.
* `string`
* `byte[]`
* `POCO` – Az üzenet C#-objektumként van formázva. Teljes példa: C# parancsfájl- [példa](#example).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Az üzenetsor-üzenet a Context. kötések használatával érhető el.<NAME> ahol a <NAME> megegyezik a function.jsáltal megadott névvel. Ha a hasznos adat JSON, az érték deszerializálása egy objektumba történik.

# <a name="python"></a>[Python](#tab/python)

Tekintse meg a Python- [példát](#example).

# <a name="java"></a>[Java](#tab/java)

Tekintse meg a Java [-attribútumokat és-megjegyzéseket](#attributes-and-annotations).

---

## <a name="dead-letter-queues"></a>Kézbesítetlen levelek várólistái
A kézbesítetlen levelek várólistái és az cserék nem állíthatók be és nem konfigurálhatók a RabbitMQ triggerből.  A kézbesítetlen levelek várólistáinak használatához előre be kell állítania az trigger által a RabbitMQ-ben használt várólistát. Tekintse meg a [RabbitMQ dokumentációját](https://www.rabbitmq.com/dlx.html).

## <a name="hostjson-settings"></a>Beállítások host.js

Ez a szakasz a kötéshez elérhető globális konfigurációs beállításokat ismerteti 2. x vagy újabb verziókban. Az alábbi példában szereplő *host.js* csak a kötés beállításait tartalmazza. A globális konfigurációs beállításokkal kapcsolatos további információkért lásd: [host.jsAzure functions verzióra vonatkozó hivatkozás](functions-host-json.md).

```json
{
    "version": "2.0",
    "extensions": {
        "rabbitMQ": {
            "prefetchCount": 100,
            "queueName": "queue",
            "connectionString": "amqp://user:password@url:port",
            "port": 10
        }
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------|
|prefetchCount|30|Lekérdezi vagy beállítja az üzenet fogadója által egyidejűleg kérelmezhető és gyorsítótárazott üzenetek számát.|
|queueName|n/a| Azon várólista neve, amelyről üzeneteket szeretne fogadni. |
|connectionString|n/a|Annak az RabbitMQ a neve, amely az üzenetsor-kapcsolatok karakterláncát tartalmazza. Vegye figyelembe, hogy ha a (z) local.settings.json lévő alkalmazás-beállításon keresztül közvetlenül adja meg a kapcsolatok karakterláncát, akkor az trigger nem fog működni.|
|port|0|(a connectionString használatával figyelmen kívül hagyva) A többméretű példányon egyidejűleg kezelhető munkamenetek maximális száma.|

## <a name="local-testing"></a>Helyi tesztelés

> [!NOTE]
> A connectionString elsőbbséget élvez a "hostName", a "userName" és a "password" kifejezéssel szemben. Ha ezek mindegyike be van állítva, akkor a connectionString felülbírálja a másik kettőt.

Ha a helyi tesztelést nem a kapcsolatok karakterlánca nélkül végzi, állítsa be a "hostName" beállítást és a "userName" és a "password" értéket, ha az a *host.js*"rabbitMQ" szakaszában szerepel:

```json
{
    "version": "2.0",
    "extensions": {
        "rabbitMQ": {
            ...
            "hostName": "localhost",
            "username": "userNameSetting",
            "password": "passwordSetting"
        }
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------|
|hostName|n/a|(ConnectStringSetting használata esetén figyelmen kívül hagyva) <br>A várólista állomásneve (pl.: 10.26.45.210)|
|userName (Felhasználónév)|n/a|(ConnectionStringSetting használata esetén figyelmen kívül hagyva) <br>A várólista eléréséhez használandó név |
|jelszó|n/a|(ConnectionStringSetting használata esetén figyelmen kívül hagyva) <br>A várólista eléréséhez szükséges jelszó|

## <a name="monitoring-rabbitmq-endpoint"></a>RabbitMQ-végpont figyelése
A várólisták és az egyes RabbitMQ-végpontok cseréjének figyelése:

* A [RabbitMQ-kezelő beépülő modul](https://www.rabbitmq.com/management.html) engedélyezése
* Keresse meg a http://{node-hostname}: 15672, és jelentkezzen be a felhasználónevével és jelszavával.

## <a name="next-steps"></a>További lépések

- [RabbitMQ üzenetek küldése Azure Functionsból (kimeneti kötés)](./functions-bindings-rabbitmq-output.md)
