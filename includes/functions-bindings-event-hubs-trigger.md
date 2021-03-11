---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 145db7693db126d4e114e8c8a885ea7fd7809e69
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102608910"
---
Az Event hub Event streambe küldött eseményre való reagáláshoz használja a függvény eseményindítóját. Az eseményindító beállításához olvasási hozzáféréssel kell rendelkeznie a mögöttes Event hub-hoz. A függvény elindításakor a függvénynek átadott üzenet karakterláncként van beírva.

## <a name="scaling"></a>Méretezés

Egy esemény által aktivált függvény minden példányát egyetlen [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor) -példány támogatja. A trigger (Event Hubs) biztosítja, hogy csak egy [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor) -példány kapjon bérletet egy adott partíción.

Vegyünk például egy Event hub-t az alábbiak szerint:

* 10 partíció
* 1 000 az összes partíción egyenletesen elosztott események, 100-es üzenetekkel az egyes partíciók között

A függvény első engedélyezésekor a függvénynek csak egy példánya van. Hívjuk meg az első függvény példányát `Function_0` . A `Function_0` függvény egyetlen [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor) -példánnyal rendelkezik, amely minden tíz partíción rendelkezik bérlettel. Ez a példány az 0-9-es partíciókhoz származó eseményeket olvas. Ettől a ponttól kezdve a következők egyike történik:

* Az **új függvények példányai nem szükségesek**: az `Function_0` összes 1 000 eseményt feldolgozhatja, mielőtt a függvények skálázási logikája érvénybe lép. Ebben az esetben a rendszer az összes 1 000 üzenetet dolgozza fel `Function_0` .

* **További függvény-példány hozzáadása**: Ha a függvények skálázási logikája meghatározza, hogy a `Function_0` feldolgozható több üzenettel rendelkezik, akkor létrejön egy új Function App-példány ( `Function_1` ). Ez az új függvény a [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor)társított példányával is rendelkezik. Mivel az alapul szolgáló Event Hubs azt vizsgálja, hogy egy új gazdagép-példány próbálkozik az olvasási üzenetekkel, a terheléselosztás a gazdagép példányai között osztja el a partíciókat. Előfordulhat például, hogy a 0-4-es partíciók hozzá vannak rendelve a 5-9-es partícióhoz `Function_0` `Function_1` .

* **N további függvény-példányok lettek hozzáadva**: Ha a függvények skálázási logikája meghatározza, hogy a és a rendszer `Function_0` `Function_1` több üzenetet is feldolgozzon, akkor az új `Functions_N` Function app-példányok jönnek létre.  Az alkalmazások arra a pontra jönnek létre, ahol a `N` nagyobb, mint az Event hub-partíciók száma. A példánkban Event Hubs újra a partíciók elosztása, ebben az esetben a példányok között `Function_0` ... `Functions_9`

Skálázás esetén `N` a példányok száma nagyobb, mint az Event hub-partíciók száma. Ez a minta annak biztosítására szolgál, hogy a [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor) példányok elérhetők legyenek a partíciók zárolásának beszerzéséhez, mivel azok más példányokból elérhetővé válnak Csak a függvény példányának végrehajtásakor használt erőforrásokért kell fizetnie. Más szóval nem számítunk fel díjat a kiépítés során.

Ha az összes függvény végrehajtása befejeződött (hibákkal vagy anélkül), a rendszer ellenőrzőpontokat ad hozzá a társított Storage-fiókhoz. Ha a pipa sikeres, a rendszer az összes 1 000 üzenetet soha nem kéri le.

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Az alábbi példa egy [C#-függvényt](../articles/azure-functions/functions-dotnet-class-library.md) mutat be, amely az Event hub eseményindítójának üzenet törzsét naplózza.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Ahhoz, hogy hozzáférést kapjon az [esemény metaadataihoz](#event-metadata) a Function Code-ban, kötést kell kötnie egy [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) -objektumhoz (a használatához a using utasítás szükséges `Microsoft.Azure.EventHubs` ). Ugyanezeket a tulajdonságokat a metódus aláírása kötési kifejezések használatával is elérheti.  Az alábbi példa mindkét módszert mutatja be, hogy ugyanazokat az eszközöket kapja:

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

Ha egy kötegben szeretne eseményeket fogadni, végezze el `string` vagy `EventData` egy tömböt.  

> [!NOTE]
> Egy kötegben való fogadáskor nem lehet olyan metódusokhoz kötni, mint például a fenti példában, `DateTime enqueuedTimeUtc` és meg kell kapnia ezeket az egyes `EventData` objektumokból.  

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

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A következő példa egy Event hub eseményindító-kötést mutat be egy *function.jsa* fájlban, és egy [C# parancsfájl-függvényt](../articles/azure-functions/functions-reference-csharp.md) , amely a kötést használja. A függvény naplózza az Event hub eseményindítójának üzenet törzsét.

Az alábbi példák a fájl *function.js* Event Hubs kötési adataikat mutatják be.

### <a name="version-2x-and-higher"></a>2. x vagy újabb verzió

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>1. x verzió

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

A C# szkript kódja:

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Ahhoz, hogy hozzáférést kapjon az [esemény metaadataihoz](#event-metadata) a Function Code-ban, kötést kell kötnie egy [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) -objektumhoz (a használatához a using utasítás szükséges `Microsoft.Azure.EventHubs` ). Ugyanezeket a tulajdonságokat a metódus aláírása kötési kifejezések használatával is elérheti.  Az alábbi példa mindkét módszert mutatja be, hogy ugyanazokat az eszközöket kapja:

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

Ha egy kötegben szeretne eseményeket fogadni, hozzon `string` vagy `EventData` egy tömböt:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A következő példa egy Event hub eseményindító kötést mutat be egy *function.jsa* fájlban, és egy [JavaScript-függvényt](../articles/azure-functions/functions-reference-node.md) , amely a kötést használja. A függvény beolvassa az [esemény metaadatait](#event-metadata) , és naplózza az üzenetet.

Az alábbi példák a fájl *function.js* Event Hubs kötési adataikat mutatják be.

### <a name="version-2x-and-higher"></a>2. x vagy újabb verzió

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>1. x verzió

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Itt látható a JavaScript-kód:

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Function triggered to process a message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);

    context.done();
};
```

Ha egy kötegben szeretne eseményeket fogadni, `cardinality` a `many` következő példákban látható módon állítsa a (z) *function.js* fájlra.

### <a name="version-2x-and-higher"></a>2. x vagy újabb verzió

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

### <a name="version-1x"></a>1. x verzió

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

Itt látható a JavaScript-kód:

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

# <a name="python"></a>[Python](#tab/python)

A következő példa egy Event hub eseményindító-kötést mutat be egy *function.jsa* fájlban, és egy [Python-függvényt](../articles/azure-functions/functions-reference-python.md) , amely a kötést használja. A függvény beolvassa az [esemény metaadatait](#event-metadata) , és naplózza az üzenetet.

Az alábbi példák a fájl *function.js* Event Hubs kötési adataikat mutatják be.

```json
{
  "type": "eventHubTrigger",
  "name": "event",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Itt látható a Python-kód:

```python
import logging
import azure.functions as func


def main(event: func.EventHubEvent):
    logging.info('Function triggered to process a message: ', event.get_body())
    logging.info('  EnqueuedTimeUtc =', event.enqueued_time)
    logging.info('  SequenceNumber =', event.sequence_number)
    logging.info('  Offset =', event.offset)

    # Metadata
    for key in event.metadata:
        logging.info(f'Metadata: {key} = ', event.metadata[key])
```

# <a name="java"></a>[Java](#tab/java)

Az alábbi példa egy Event hub eseményindító-kötést mutat be, amely az Event hub-eseményindító üzenet törzsét naplózza.

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

 A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja a `EventHubTrigger` jegyzeteket azon paramétereknél, amelyek értéke az Event hubből származik. Az ezekkel a megjegyzésekkel rendelkező paraméterek a függvény futását okozzák, amikor egy esemény érkezik.  Ez a jegyzet natív Java-típusokkal, Szerializálói vagy NULL értékű értékekkel használható a használatával `Optional<T>` .

 ---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C#](#tab/csharp)

A [C# osztályok könyvtáraiban](../articles/azure-functions/functions-dotnet-class-library.md)használja a [EventHubTriggerAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs) attribútumot.

Az attribútum konstruktora az Event hub nevét, a fogyasztói csoport nevét és a kapcsolati karakterláncot tartalmazó Alkalmazásbeállítás nevét tartalmazza. További információ ezekről a beállításokról: [trigger konfiguráció szakasz](#configuration). `EventHubTriggerAttribute`Példa egy attribútumra:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

A teljes példa: [trigger-C# példa](#example).

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A C# parancsfájl nem támogatja az attribútumokat.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

# <a name="java"></a>[Java](#tab/java)

A Java [functions futtatókörnyezet könyvtárában](/java/api/overview/azure/functions/runtime)használja a [EventHubTrigger](/java/api/com.microsoft.azure.functions.annotation.eventhubtrigger) -jegyzetet olyan paramétereknél, amelyek értéke az Event hub-ból származik. Az ezekkel a megjegyzésekkel rendelkező paraméterek a függvény futását okozzák, amikor egy esemény érkezik. Ez a jegyzet natív Java-típusokkal, Szerializálói vagy NULL értékű értékekkel használható a használatával `Optional<T>` .

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a fájl és attribútum *function.jsjában* beállított kötési konfigurációs tulajdonságokat ismerteti `EventHubTrigger` .

|function.jsa tulajdonságon | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**típusa** | n.a. | Értékre kell állítani `eventHubTrigger` . Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az triggert a Azure Portalban.|
|**irányba** | n.a. | Értékre kell állítani `in` . Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az triggert a Azure Portalban. |
|**név** | n.a. | Annak a változónak a neve, amely a függvény kódjában szereplő Event tételt jelöli. |
|**elérési útja** |**EventHubName** | Csak 1. x függvények. Az Event hub neve. Ha az Event hub neve szerepel a kapcsolati sztringben is, ez az érték felülbírálja ezt a tulajdonságot futásidőben. |
|**eventHubName** |**EventHubName** | A 2. x és újabb függvények. Az Event hub neve. Ha az Event hub neve szerepel a kapcsolati sztringben is, ez az érték felülbírálja ezt a tulajdonságot futásidőben. Az [alkalmazás beállításain](../articles/azure-functions/functions-bindings-expressions-patterns.md#binding-expressions---app-settings) keresztül lehet hivatkozni `%eventHubName%` |
|**consumerGroup** |**ConsumerGroup** | Egy opcionális tulajdonság, amely a központban lévő eseményekre való előfizetéshez használt [fogyasztói csoportot](../articles/event-hubs/event-hubs-features.md#event-consumers) állítja be. Ha nincs megadva, a `$Default` rendszer a fogyasztói csoportot használja. |
|**számosság** | n.a. | Az összes nem C nyelvhez használatos. A `many` kötegelt feldolgozás engedélyezéséhez állítsa a következőre:.  Ha nincs megadva vagy a értékre `one` van állítva, a függvény egyetlen üzenetet ad át.<br><br>A C# nyelvben ez a tulajdonság automatikusan lesz hozzárendelve, amikor az trigger rendelkezik tömbvel a típushoz.|
|**kapcsolat** |**Kapcsolat** | Az Event hub névteréhez tartozó kapcsolati sztringet tartalmazó Alkalmazásbeállítás neve. Másolja ezt a kapcsolati karakterláncot a [névtér](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace) **kapcsolati adatok** gombjára kattintva, nem az Event hub-t. A kapcsolódási karakterláncnak legalább olvasási engedéllyel kell rendelkeznie az trigger aktiválásához.<br><br>Ha [a bővítmény 5. x vagy újabb verzióját](../articles/azure-functions/functions-bindings-event-hubs.md#event-hubs-extension-5x-and-higher)használja, a kapcsolati karakterlánc helyett megadhat egy olyan konfigurációs szakaszra mutató hivatkozást, amely meghatározza a kapcsolódást. Lásd: [kapcsolatok](../articles/azure-functions/functions-reference.md#connections).|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Használat

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default"></a>Alapértelmezett

A következő típusú paramétereket használhatja az eseményindító esemény központjának:

* `string`
* `byte[]`
* `POCO`
* `EventData` – A EventData alapértelmezett tulajdonságai a [Microsoft. Azure. EventHubs névtérben](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet)vannak megadva.

### <a name="additional-types"></a>További típusok 
Az Event hub-bővítmény 5.0.0 vagy újabb verzióját használó alkalmazások a `EventData` [Microsoft. Azure. EventHubs névtérben](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet)használják a típust az [Azure. Messaging. EventHubs](https://docs.microsoft.com/dotnet/api/azure.messaging.eventhubs.eventdata?view=azure-dotnet) helyett. Ez a verzió a `Body` következő típusok javára elveszíti a örökölt típus támogatását:

- [EventBody](https://docs.microsoft.com/dotnet/api/azure.messaging.eventhubs.eventdata.eventbody?view=azure-dotnet)

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

### <a name="default"></a>Alapértelmezett

A következő típusú paramétereket használhatja az eseményindító esemény központjának:

* `string`
* `byte[]`
* `POCO`
* `EventData` – A EventData alapértelmezett tulajdonságai a [Microsoft. Azure. EventHubs névtérben](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet)vannak megadva.

### <a name="additional-types"></a>További típusok 
Az Event hub-bővítmény 5.0.0 vagy újabb verzióját használó alkalmazások a `EventData` [Microsoft. Azure. EventHubs névtérben](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet)használják a típust az [Azure. Messaging. EventHubs](https://docs.microsoft.com/dotnet/api/azure.messaging.eventhubs.eventdata?view=azure-dotnet) helyett. Ez a verzió a `Body` következő típusok javára elveszíti a örökölt típus támogatását:

- [EventBody](https://docs.microsoft.com/dotnet/api/azure.messaging.eventhubs.eventdata.eventbody?view=azure-dotnet)

# <a name="java"></a>[Java](#tab/java)

A részletekért tekintse meg a Java [trigger példáját](#example) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A részletekért tekintse meg a JavaScript [trigger példáját](#example) .

# <a name="python"></a>[Python](#tab/python)

A részletekért tekintse meg a Python [trigger példáját](#example) .


---


## <a name="event-metadata"></a>Esemény metaadatainak

A Event Hubs trigger számos [metaadat-tulajdonságot](../articles/azure-functions/./functions-bindings-expressions-patterns.md)biztosít. A metaadatok tulajdonságai más kötésekben vagy a kódban szereplő paraméterek részeként is használhatók. A tulajdonságok a [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) osztályból származnak.

|Tulajdonság|Típus|Leírás|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|A `PartitionContext` példány.|
|`EnqueuedTimeUtc`|`DateTime`|A várólistán lévő idő UTC szerint.|
|`Offset`|`string`|Az adateltolás az Event hub-partíciós adatfolyamhoz viszonyítva. Az eltolás a Event Hubs streamen belüli esemény jelölője vagy azonosítója. Az azonosító egyedi az Event Hubs stream egy partícióján belül.|
|`PartitionKey`|`string`|Az a partíció, amelybe az eseményt el kell juttatni.|
|`Properties`|`IDictionary<String,Object>`|Az eseményhez tartozó adatértékek felhasználói tulajdonságai|
|`SequenceNumber`|`Int64`|Az esemény logikai sorszáma.|
|`SystemProperties`|`IDictionary<String,Object>`|A Rendszertulajdonságok, beleértve az eseményre vonatkozó adattípusokat is.|

Tekintse meg a jelen cikk korábbi részében említett tulajdonságokat használó [példákat](#example) .