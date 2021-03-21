---
title: Azure Functions Azure Service Bus trigger
description: Ismerje meg, hogyan futtathat Azure-függvényeket Azure Service Bus üzenetek létrehozásakor.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 4b95c25400317b2baac694f4ba2b1b1dc1eae098
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102435154"
---
# <a name="azure-service-bus-trigger-for-azure-functions"></a>Azure Functions Azure Service Bus trigger

A Service Bus triggerrel válaszolhat Service Bus üzenetsor vagy témakör üzeneteire. A bővítmény 3.1.0 kezdődően a munkamenet-kompatibilis üzenetsor vagy témakör aktiválható.

További információ a telepítésről és a konfigurációról: [Áttekintés](functions-bindings-service-bus.md).

## <a name="example"></a>Példa

# <a name="c"></a>[C#](#tab/csharp)

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely beolvassa az [üzenet metaadatait](#message-metadata) , és naplóz egy Service Bus üzenetsor-üzenetet:

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
    string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    ILogger log)
{
    log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"DeliveryCount={deliveryCount}");
    log.LogInformation($"MessageId={messageId}");
}
```

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

Az alábbi példa egy Service Bus trigger kötést mutat be a fájlban lévő *function.js* , és egy [C# parancsfájl-függvényt](functions-reference-csharp.md) , amely a kötést használja. A függvény beolvassa az [üzenet metaadatait](#message-metadata) , és naplóz egy Service Bus üzenetsor-üzenetet.

A *function.js* fájlban található kötési adatfájlok:

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

A C# szkript kódja:

```cs
using System;

public static void Run(string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"DeliveryCount={deliveryCount}");
    log.Info($"MessageId={messageId}");
}
```

# <a name="java"></a>[Java](#tab/java)

A következő Java-függvény a `@ServiceBusQueueTrigger` [Java functions Runtime könyvtárának](/java/api/overview/azure/functions/runtime) megjegyzését használja egy Service Bus üzenetsor-trigger konfigurációjának leírásához. A függvény megragadja a várólistára helyezett üzenetet, és hozzáadja azt a naplókhoz.

```java
@FunctionName("sbprocessor")
 public void serviceBusProcess(
    @ServiceBusQueueTrigger(name = "msg",
                             queueName = "myqueuename",
                             connection = "myconnvarname") String message,
   final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
```

A Java-függvények akkor is elindíthatók, amikor egy üzenet bekerül egy Service Bus témakörbe. Az alábbi példa a `@ServiceBusTopicTrigger` jegyzetet használja az trigger konfigurációjának leírásához.

```java
@FunctionName("sbtopicprocessor")
    public void run(
        @ServiceBusTopicTrigger(
            name = "message",
            topicName = "mytopicname",
            subscriptionName = "mysubscription",
            connection = "ServiceBusConnection"
        ) String message,
        final ExecutionContext context
    ) {
        context.getLogger().info(message);
    }
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Az alábbi példa egy Service Bus trigger kötést mutat be a fájlban lévő *function.js* , és egy olyan [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja. A függvény beolvassa az [üzenet metaadatait](#message-metadata) , és naplóz egy Service Bus üzenetsor-üzenetet.

A *function.js* fájlban található kötési adatfájlok:

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Itt látható a JavaScript-szkript kódja:

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('DeliveryCount =', context.bindingData.deliveryCount);
    context.log('MessageId =', context.bindingData.messageId);
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Az alábbi példa egy Service Bus trigger kötést mutat be a fájlban lévő *function.js* , és egy olyan [PowerShell-függvényt](functions-reference-powershell.md) , amely a kötést használja. 

A *function.js* fájlban található kötési adatfájlok:

```json
{
  "bindings": [
    {
      "name": "mySbMsg",
      "type": "serviceBusTrigger",
      "direction": "in",
      "topicName": "mytopic",
      "subscriptionName": "mysubscription",
      "connection": "AzureServiceBusConnectionString"
    }
  ]
}
```

A Service Bus-üzenet elküldésekor futó függvény.

```powershell
param([string] $mySbMsg, $TriggerMetadata)

Write-Host "PowerShell ServiceBus queue trigger function processed message: $mySbMsg"
```

# <a name="python"></a>[Python](#tab/python)

Az alábbi példa bemutatja, hogyan olvashat egy Service Bus üzenetsor-üzenetet egy trigger használatával.

Service Bus kötés van definiálva a *function.jsn* , ahol a *típus* értéke: `serviceBusTrigger` .

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "serviceBusTrigger",
      "direction": "in",
      "queueName": "inputqueue",
      "connection": "AzureServiceBusConnectionString"
    }
  ]
}
```

Az *_\_ init_. a (z \_ ). a. a. a. a.* a (z) kód deklarál egy paramétert `func.ServiceBusMessage` , amely lehetővé teszi, hogy elolvassa az üzenetsor

```python
import azure.functions as func

import logging
import json

def main(msg: func.ServiceBusMessage):
    logging.info('Python ServiceBus queue trigger processed message.')

    result = json.dumps({
        'message_id': msg.message_id,
        'body': msg.get_body().decode('utf-8'),
        'content_type': msg.content_type,
        'expiration_time': msg.expiration_time,
        'label': msg.label,
        'partition_key': msg.partition_key,
        'reply_to': msg.reply_to,
        'reply_to_session_id': msg.reply_to_session_id,
        'scheduled_enqueue_time': msg.scheduled_enqueue_time,
        'session_id': msg.session_id,
        'time_to_live': msg.time_to_live,
        'to': msg.to,
        'user_properties': msg.user_properties,
        'metadata' : msg.metadata
    })

    logging.info(result)
```

---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C#](#tab/csharp)

A [C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a következő attribútumokat egy Service Bus trigger konfigurálásához:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  Az attribútum konstruktora a várólista nevét, illetve a témakört és az előfizetést veszi át. A Azure Functions 1. x verziójában megadhatja a kapcsolat hozzáférési jogosultságait is. Ha nem ad meg hozzáférési jogosultságokat, az alapértelmezett érték `Manage` . További információ: [trigger-Configuration](#configuration) szakasz.

  Az alábbi példa egy sztring paraméterrel használt attribútumot mutatja be:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Mivel a `Connection` tulajdonság nincs definiálva, a functions megkeresi a nevű Alkalmazásbeállítások értékét `AzureWebJobsServiceBus` , amely az alapértelmezett név a Service Bus a kapcsolatok karakterláncához. A tulajdonságot beállíthatja `Connection` úgy is, hogy megadja a használni kívánt Service Bus-kapcsolódási karakterláncot tartalmazó Alkalmazásbeállítás nevét, az alábbi példában látható módon:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  A teljes példa: [trigger-example](#example).

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAccountAttribute.cs)

  Egy másik módszert biztosít a használni kívánt Service Bus-fiók megadásához. A konstruktor egy Service Bus kapcsolatot megadó karakterláncot tartalmazó Alkalmazásbeállítás nevét veszi fel. Az attribútumot a paraméter, a metódus vagy az osztály szintjén lehet alkalmazni. Az alábbi példa az osztály szintjét és a metódus szintjét mutatja be:

  ```csharp
  [ServiceBusAccount("ClassLevelServiceBusAppSetting")]
  public static class AzureFunctions
  {
      [ServiceBusAccount("MethodLevelServiceBusAppSetting")]
      [FunctionName("ServiceBusQueueTriggerCSharp")]
      public static void Run(
          [ServiceBusTrigger("myqueue", AccessRights.Manage)] 
          string myQueueItem, ILogger log)
  {
      ...
  }
  ```

A használni kívánt Service Bus fiók a következő sorrendben van meghatározva:

* Az `ServiceBusTrigger` attribútum `Connection` tulajdonsága.
* A `ServiceBusAccount` attribútummal megegyező paraméterre alkalmazott attribútum `ServiceBusTrigger` .
* A `ServiceBusAccount` függvényre alkalmazott attribútum.
* Az `ServiceBusAccount` osztályra alkalmazott attribútum.
* A "AzureWebJobsServiceBus" alkalmazás beállításai.

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A C# parancsfájl nem támogatja az attribútumokat.

# <a name="java"></a>[Java](#tab/java)

A `ServiceBusQueueTrigger` jegyzet lehetővé teszi, hogy egy Service Bus üzenetsor létrehozásakor futó függvényt hozzon létre. Az elérhető konfigurációs lehetőségek közé tartozik a várólista neve és a kapcsolatok karakterláncának neve.

A `ServiceBusTopicTrigger` jegyzet lehetővé teszi, hogy egy témakört és egy előfizetést jelöljön ki a függvényt kiváltó adat megcélzásához.

További részletekért tekintse meg az trigger [példáját](#example) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A PowerShell nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a fájl és attribútum *function.jsjában* beállított kötési konfigurációs tulajdonságokat ismerteti `ServiceBusTrigger` .

|function.jsa tulajdonságon | Attribútum tulajdonsága |Description|
|---------|---------|----------------------|
|**típusa** | n.a. | "ServiceBusTrigger" értékre kell állítani. Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az triggert a Azure Portalban.|
|**irányba** | n.a. | "In" értékre kell állítani. Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az triggert a Azure Portalban. |
|**név** | n.a. | Annak a változónak a neve, amely a függvény kódjában szereplő üzenetsor vagy témakör üzenetét jelöli. |
|**queueName**|**QueueName**|A figyelni kívánt várólista neve.  Csak akkor állítható be, ha egy üzenetsor figyelése nem a témakörhöz szükséges.
|**topicName**|**TopicName**|A figyelni kívánt témakör neve. Csak akkor állítsa be, ha egy témakör figyelése nem várólistára van állítva.|
|**subscriptionName**|**SubscriptionName**|A figyelni kívánt előfizetés neve. Csak akkor állítsa be, ha egy témakör figyelése nem várólistára van állítva.|
|**kapcsolat**|**Kapcsolat**|A kötéshez használni kívánt Service Bus kapcsolati karakterláncot tartalmazó Alkalmazásbeállítás neve. Ha az Alkalmazásbeállítások neve "AzureWebJobs" előtaggal kezdődik, akkor csak a név hátralévő részét adhatja meg. Ha például a "MyServiceBus" értékre van állítva `connection` , a functions futtatókörnyezet egy "AzureWebJobsMyServiceBus" nevű alkalmazás-beállítást keres. Ha `connection` üresen hagyja, a functions Runtime az alapértelmezett Service Bus a "AzureWebJobsServiceBus" nevű alkalmazás-beállításban található.<br><br>A kapcsolódási karakterlánc beszerzéséhez kövesse a [felügyeleti hitelesítő adatok beolvasása](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)című cikkben ismertetett lépéseket. A kapcsolódási karakterláncnak egy Service Bus névtérhez kell tartoznia, nem csak egy adott várólistára vagy témakörre. |
|**accessRights**|**Hozzáférés**|Hozzáférési jogosultságok a kapcsolati karakterlánchoz. Az elérhető értékek: `manage` és `listen` . Az alapértelmezett érték a `manage` , amely azt jelzi, hogy a `connection` rendelkezik a **kezelés** engedéllyel. Ha olyan kapcsolódási karakterláncot használ, amely nem rendelkezik a **kezelés** engedéllyel, állítsa a `accessRights` "Listen" (figyelés) lehetőséget. Ellenkező esetben előfordulhat, hogy a functions futtatókörnyezete nem próbálkozik a kezelési jogosultságokat igénylő műveletekkel. A Azure Functions 2. x vagy újabb verziójában ez a tulajdonság nem érhető el, mert a Service Bus SDK legújabb verziója nem támogatja a kezelési műveleteket.|
|**isSessionsEnabled**|**IsSessionsEnabled**|`true` Ha [munkamenet-kompatibilis](../service-bus-messaging/message-sessions.md) várólistához vagy előfizetéshez csatlakozik. `false` Ellenkező esetben ez az alapértelmezett érték.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Használat

# <a name="c"></a>[C#](#tab/csharp)

A következő típusparaméter-típusok érhetők el a várólista vagy a témakör üzeneteihez:

* `string` – Ha az üzenet szövege.
* `byte[]` – Hasznos a bináris adattároláshoz.
* Egyéni típus – ha az üzenet JSON-t tartalmaz, Azure Functions megpróbálja deszerializálni a JSON-adattípust.
* `BrokeredMessage` -Megadja a deszerializált üzenetet a [BrokeredMessage. GetBody \<T> ()](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) metódussal.
* [`MessageReceiver`](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver) -Az üzenet-tárolóból érkező üzenetek fogadására és visszaigazolására használatos (kötelező, ha a értéke [`autoComplete`](functions-bindings-service-bus-output.md#hostjson-settings) `false` )

Ezek a paraméterek a Azure Functions 1. x verzióra vonatkoznak. 2. x és újabb verziók esetén használja [`Message`](/dotnet/api/microsoft.azure.servicebus.message) a helyett a következőt: `BrokeredMessage` .

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A következő típusparaméter-típusok érhetők el a várólista vagy a témakör üzeneteihez:

* `string` – Ha az üzenet szövege.
* `byte[]` – Hasznos a bináris adattároláshoz.
* Egyéni típus – ha az üzenet JSON-t tartalmaz, Azure Functions megpróbálja deszerializálni a JSON-adattípust.
* `BrokeredMessage` -Megadja a deszerializált üzenetet a [BrokeredMessage. GetBody \<T> ()](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) metódussal.

Ezek a paraméterek az 1. x Azure Functions-verzióra vonatkoznak. 2. x és újabb verziók esetén használja [`Message`](/dotnet/api/microsoft.azure.servicebus.message) a helyett a következőt: `BrokeredMessage` .

# <a name="java"></a>[Java](#tab/java)

A bejövő Service Bus üzenet a vagy a paraméter használatával érhető el `ServiceBusQueueMessage` `ServiceBusTopicMessage` .

[A részletekért tekintse meg a példát](#example).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A várólista vagy a témakör üzenetének elérése a használatával `context.bindings.<name from function.json>` . A Service Bus üzenetet karakterlánc vagy JSON-objektumként adja át a függvénynek.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A Service Bus példány a fájl Name tulajdonságában a *function.js* konfigurált paraméterrel érhető el.

# <a name="python"></a>[Python](#tab/python)

Az üzenetsor-üzenet a következőként beírt paraméterrel érhető el a függvény számára: `func.ServiceBusMessage` . A Service Bus üzenetet karakterlánc vagy JSON-objektumként adja át a függvénynek.

---

## <a name="poison-messages"></a>Üzenetek megmérgezve

A megmérgezett üzenetküldés nem szabályozható és nem konfigurálható Azure Functionsban. Service Bus maga kezeli a megmérgezhető üzeneteket.

## <a name="peeklock-behavior"></a>PeekLock viselkedés

A functions futtatókörnyezet [PeekLock módban](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode)fogad üzenetet. Ha a `Complete` függvény sikeresen befejeződik, meghívja az üzenetet, vagy meghívja, `Abandon` Ha a függvény meghibásodik. Ha a függvény hosszabb ideig fut az `PeekLock` időtúllépésnél, a rendszer automatikusan megújítja a zárolást, amíg a függvény fut.

Az a `maxAutoRenewDuration` *host.json* konfigurálható, amely a [OnMessageOptions. MaxAutoRenewDuration](/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration)leképezésére szolgál. Az ehhez a beállításhoz engedélyezett maximális érték 5 perc a Service Bus dokumentációjában, míg a functions időkorlát az alapértelmezett 5 perc és 10 perc között is megnövelhető. Olyan Service Bus függvények esetében, amelyeket nem kíván végrehajtani, mert túllépi a Service Bus megújítási korlátot.

## <a name="message-metadata"></a>Üzenet metaadatai

A Service Bus trigger számos [metaadat-tulajdonságot](./functions-bindings-expressions-patterns.md#trigger-metadata)biztosít. Ezek a tulajdonságok a más kötésekben lévő kötési kifejezések vagy a kódban szereplő paraméterek részeként is használhatók. Ezek a tulajdonságok az [üzenet](/dotnet/api/microsoft.azure.servicebus.message) osztály tagjai.

|Tulajdonság|Típus|Description|
|--------|----|-----------|
|`ContentType`|`string`|A küldő és a fogadó által az alkalmazásspecifikus logikához használt tartalomtípus-azonosító.|
|`CorrelationId`|`string`|A korrelációs azonosító.|
|`DeadLetterSource`|`string`|A kézbesítetlen levél forrása.|
|`DeliveryCount`|`Int32`|A kézbesítések száma.|
|`EnqueuedTimeUtc`|`DateTime`|A várólistán lévő idő UTC szerint.|
|`ExpiresAtUtc`|`DateTime`|A lejárati idő UTC szerint.|
|`Label`|`string`|Az alkalmazásra vonatkozó címke.|
|`MessageId`|`string`|Felhasználó által definiált érték, amelyet a Service Bus az ismétlődő üzenetek azonosítására használhat, ha engedélyezve van.|
|`MessageReceiver`|`MessageReceiver`|Service Bus üzenet fogadója. Felhasználható az üzenet lemondása, befejezése vagy kézbesítetlen levelek.|
|`MessageSession`|`MessageSession`|Egy üzenet-fogadó kifejezetten a munkamenet-kompatibilis várólistákhoz és témakörökhöz.|
|`ReplyTo`|`string`|A várólista-címnek küldött válasz.|
|`SequenceNumber`|`long`|A Service Bus által az üzenethez hozzárendelt egyedi szám.|
|`To`|`string`|A Küldés címe.|
|`UserProperties`|`IDictionary<string, object>`|A küldő által beállított tulajdonságok.|

Tekintse meg a jelen cikk korábbi részében említett tulajdonságokat használó [példákat](#example) .

## <a name="next-steps"></a>Következő lépések

- [Azure Service Bus üzenetek küldése Azure Functionsból (kimeneti kötés)](./functions-bindings-service-bus-output.md)
