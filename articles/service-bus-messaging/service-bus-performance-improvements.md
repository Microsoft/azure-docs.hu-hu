---
title: Ajánlott eljárások a teljesítmény javításához a Azure Service Bus használatával
description: Ismerteti, hogyan optimalizálható a teljesítmény a Service Bus használatával a felügyelt üzenetek cseréjekor.
ms.topic: article
ms.date: 03/09/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: d4093d93da11e992ed9e6558a5386eb88f417ef9
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967761"
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Ajánlott eljárások a teljesítmény növeléséhez Service Bus üzenetkezelés használatával

Ez a cikk azt ismerteti, hogyan optimalizálható a teljesítmény a felügyelt üzenetek cseréjekor a Azure Service Bus használatával. A cikk első része a teljesítmény növelésére szolgáló különböző mechanizmusokat ismerteti. A második rész útmutatást nyújt a Service Bus használatáról oly módon, hogy a lehető legjobb teljesítményt nyújtsa egy adott forgatókönyvben.

Ebben a cikkben az "ügyfél" kifejezés minden olyan entitásra hivatkozik, amely hozzáfér Service Bushoz. Egy ügyfél elvégezheti a küldő vagy fogadó szerepkörét. A "Sender" kifejezés egy Service Bus üzenetsor-ügyfélhez vagy egy olyan témakör-ügyfélhez használatos, amely üzeneteket küld egy Service Bus-várólistába vagy egy témakörbe. A "fogadó" kifejezés olyan Service Bus üzenetsor-ügyfelet vagy előfizetési ügyfelet jelöl, amely egy Service Bus üzenetsor vagy előfizetés üzeneteit fogadja.

## <a name="protocols"></a>Protokollok
Service Bus lehetővé teszi az ügyfelek számára az üzenetek küldését és fogadását a következő három protokoll egyikének használatával:

1. Advanced Message Queueing Protocol (AMQP)
2. Service Bus üzenetküldési protokoll (SBMP)
3. Hypertext Transfer Protocol (HTTP)

A AMQP a leghatékonyabb, mert a Service Bus kapcsolatot tart fenn. Emellett a [kötegelt](#batching-store-access) és a [beolvasási](#prefetching)művelet is megvalósítható. Hacsak nem kifejezetten említettük, az ebben a cikkben szereplő összes tartalom a AMQP vagy a SBMP használatát feltételezi.

> [!IMPORTANT]
> A SBMP csak a .NET-keretrendszerhez érhető el. A AMQP a .NET Standard alapértelmezett értéke.

## <a name="choosing-the-appropriate-service-bus-net-sdk"></a>A megfelelő Service Bus .NET SDK kiválasztása
A .NET SDK-k három Azure Service Bus támogatottak. Az API-k hasonlóak, és zavaró lehet, hogy melyiket érdemes választani. A döntés meghozatalához tekintse meg a következő táblázatot. Az Azure. Messaging. ServiceBus SDK a legújabb, és azt javasoljuk, hogy más SDK-kat használjon. Az Azure. Messaging. ServiceBus és a Microsoft. Azure. ServiceBus SDK-k a modern, a teljesítményű és a platformfüggetlen platformon kompatibilisek. Emellett támogatják a AMQP-t a websocketeken keresztül, és a nyílt forráskódú projektek Azure .NET SDK-gyűjteményének részét képezik.

| NuGet-csomag | Elsődleges névtér (ek) | Minimális platform (ok) | Protokoll(ok) |
|---------------|----------------------|---------------------|-------------|
| [Azure. Messaging. ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus) | `Azure.Messaging.ServiceBus`<br>`Azure.Messaging.ServiceBus.Administration` | .NET Core 2.0<br>.NET-keretrendszer 4.6.1<br>Monó 5,4<br>Xamarin. iOS 10,14<br>Xamarin. Mac 3,8<br>Xamarin. Android 8,0<br>Univerzális Windows-platform 10.0.16299 | AMQP<br>HTTP |
| [Microsoft. Azure. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | `Microsoft.Azure.ServiceBus`<br>`Microsoft.Azure.ServiceBus.Management` | .NET Core 2.0<br>.NET-keretrendszer 4.6.1<br>Monó 5,4<br>Xamarin. iOS 10,14<br>Xamarin. Mac 3,8<br>Xamarin. Android 8,0<br>Univerzális Windows-platform 10.0.16299 | AMQP<br>HTTP |
| [WindowsAzure. ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus) | `Microsoft.ServiceBus`<br>`Microsoft.ServiceBus.Messaging` | .NET-keretrendszer 4.6.1 | AMQP<br>SBMP<br>HTTP |

További információ a .NET Standard szintű platform támogatásáról: [.net-implementáció támogatása](/dotnet/standard/net-standard#net-implementation-support).

## <a name="reusing-factories-and-clients"></a>Üzemek és ügyfelek újrafelhasználása
# <a name="azuremessagingservicebus-sdk"></a>[Azure. Messaging. ServiceBus SDK](#tab/net-standard-sdk-2)
A szolgáltatással kommunikáló Service Bus objektumokat (például a [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient), a [ServiceBusSender](/dotnet/api/azure.messaging.servicebus.servicebussender), a [ServiceBusReceiver](/dotnet/api/azure.messaging.servicebus.servicebusreceiver)és a [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor)) regisztrálni kell a függőségi injektáláshoz (vagy egyszeres és megosztott példányként). A ServiceBusClient regisztrálható a függőségi injektáláshoz a [ServiceBusClientBuilderExtensions](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/servicebus/Azure.Messaging.ServiceBus/src/Compatibility/ServiceBusClientBuilderExtensions.cs). 

Azt javasoljuk, hogy az egyes üzenetek küldése vagy fogadása után ne zárjunk be vagy ne távolítsa el ezeket az objektumokat. Az entitás-specifikus objektumok (ServiceBusSender/fogadó/processzor) bezárása vagy ártalmatlanítása a Service Bus szolgáltatásra mutató hivatkozás lebontását eredményezi. A ServiceBusClient ártalmatlanítása a Service Bus szolgáltatással létesített kapcsolatok lebontását eredményezi. 

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. ServiceBus SDK](#tab/net-standard-sdk)

Service Bus az ügyfélalkalmazások, például a vagy a implementációját, [`IQueueClient`][QueueClient] [`IMessageSender`][MessageSender] regisztrálni kell a függőségi injektáláshoz (vagy egyszeres és megosztott). Azt javasoljuk, hogy az üzenet elküldése után ne zárjunk be üzenetküldési gyárakat, üzenetsor-kezelést, témakört vagy előfizetési ügyfelet, majd hozza létre újra a következő üzenet elküldésekor. Az üzenetküldési gyár bezárása törli a Service Bus szolgáltatáshoz való kapcsolódást. Új kapcsolódás jön létre a gyár újbóli létrehozásakor. 

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. ServiceBus SDK](#tab/net-framework-sdk)

Service Bus-objektumok, például `QueueClient` a vagy a `MessageSender` egy [MessagingFactory][MessagingFactory] objektumon keresztül jönnek létre, amely a kapcsolatok belső felügyeletét is biztosítja. Azt javasoljuk, hogy az üzenet elküldése után ne zárjunk be üzenetküldési gyárakat, üzenetsor-kezelést, témakört vagy előfizetési ügyfelet, majd hozza létre újra a következő üzenet elküldésekor. Az üzenetküldési gyár bezárása törli a Service Bus szolgáltatással létesített kapcsolódást, és a gyár újbóli létrehozásakor létrejön egy új csatlakozás. 

---

A következő Megjegyzés minden SDK-ra vonatkozik:

> [!NOTE]
> A kapcsolatok létrehozása költséges művelet, amellyel elkerülhető, hogy a több művelethez ugyanazt a gyári és ügyféloldali objektumot használja. Ezeket az ügyféloldali objektumokat biztonságosan használhatja egyidejű aszinkron műveletekhez és több szálból is.

## <a name="concurrent-operations"></a>Egyidejű műveletek
Az olyan műveletek, mint a küldés, fogadás, törlés stb., eltarthat egy ideig. Ez az idő magában foglalja azt az időpontot, ameddig a Service Bus szolgáltatásnak a művelet feldolgozásához és a kérés késéséhez, valamint a válaszhoz kell kapcsolódnia. A műveletek másodpercenkénti számának növeléséhez a műveleteknek egyidejűleg kell futniuk.

Az ügyfél **aszinkron** műveletek végrehajtásával ütemezhet egyidejű műveleteket. A következő kérelem az előző kérelem befejeződése előtt indul el. A következő kódrészlet példa egy aszinkron küldési műveletre:

# <a name="azuremessagingservicebus-sdk"></a>[Azure. Messaging. ServiceBus SDK](#tab/net-standard-sdk-2)
```csharp
var messageOne = new ServiceBusMessage(body);
var messageTwo = new ServiceBusMessage(body);

var sendFirstMessageTask =
    sender.SendMessageAsync(messageOne).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #1");
    });
var sendSecondMessageTask =
    sender.SendMessageAsync(messageTwo).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #2");
    });

await Task.WhenAll(sendFirstMessageTask, sendSecondMessageTask);
Console.WriteLine("All messages sent");

```

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. ServiceBus SDK](#tab/net-standard-sdk)

```csharp
var messageOne = new Message(body);
var messageTwo = new Message(body);

var sendFirstMessageTask =
    queueClient.SendAsync(messageOne).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #1");
    });
var sendSecondMessageTask =
    queueClient.SendAsync(messageTwo).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #2");
    });

await Task.WhenAll(sendFirstMessageTask, sendSecondMessageTask);
Console.WriteLine("All messages sent");
```

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. ServiceBus SDK](#tab/net-framework-sdk)

```csharp
var messageOne = new BrokeredMessage(body);
var messageTwo = new BrokeredMessage(body);

var sendFirstMessageTask =
    queueClient.SendAsync(messageOne).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #1");
    });
var sendSecondMessageTask =
    queueClient.SendAsync(messageTwo).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #2");
    });

await Task.WhenAll(sendFirstMessageTask, sendSecondMessageTask);
Console.WriteLine("All messages sent");
```

---

Az alábbi kód egy aszinkron fogadási műveletre mutat példát.

# <a name="azuremessagingservicebus-sdk"></a>[Azure. Messaging. ServiceBus SDK](#tab/net-standard-sdk-2)

```csharp
var client = new ServiceBusClient(connectionString);
var options = new ServiceBusProcessorOptions 
{

      AutoCompleteMessages = false,
      MaxConcurrentCalls = 20
};
await using ServiceBusProcessor processor = client.CreateProcessor(queueName,options);
processor.ProcessMessageAsync += MessageHandler;
processor.ProcessErrorAsync += ErrorHandler;

static Task ErrorHandler(ProcessErrorEventArgs args)
{
    Console.WriteLine(args.Exception);
    return Task.CompletedTask;
};

static async Task MessageHandler(ProcessMessageEventArgs args)
{
    Console.WriteLine("Handle message");
    await args.CompleteMessageAsync(args.Message);
}

await processor.StartProcessingAsync();
```

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. ServiceBus SDK](#tab/net-standard-sdk)

A teljes <a href="https://github.com/Azure/azure-service-bus/blob/master/samples/DotNet/Microsoft.Azure.ServiceBus/SendersReceiversWithQueues" target="_blank">forráskódra vonatkozó példákat <span class="docon docon-navigate-external x-hidden-focus"></span> </a>a GitHub-tárházban tekintheti meg:

```csharp
var receiver = new MessageReceiver(connectionString, queueName, ReceiveMode.PeekLock);

static Task LogErrorAsync(Exception exception)
{
    Console.WriteLine(exception);
    return Task.CompletedTask;
};

receiver.RegisterMessageHandler(
    async (message, cancellationToken) =>
    {
        Console.WriteLine("Handle message");
        await receiver.CompleteAsync(message.SystemProperties.LockToken);
    },
    new MessageHandlerOptions(e => LogErrorAsync(e.Exception))
    {
        AutoComplete = false,
        MaxConcurrentCalls = 20
    });
```

Az `MessageReceiver` objektum példánya a következő: a kapcsolatok karakterlánca, a várólista neve és a betekintés fogadása mód. Ezután a `receiver` példány az üzenetkezelő regisztrálására szolgál.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. ServiceBus SDK](#tab/net-framework-sdk)

A teljes <a href="https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/SendersReceiversWithQueues" target="_blank">forráskódra vonatkozó példákat <span class="docon docon-navigate-external x-hidden-focus"></span> </a>a GitHub-tárházban tekintheti meg:

```csharp
var factory = MessagingFactory.CreateFromConnectionString(connectionString);
var receiver = await factory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);

// Register the handler to receive messages asynchronously
receiver.OnMessageAsync(
    async message =>
    {
        Console.WriteLine("Handle message");
        await message.CompleteAsync();
    },
    new OnMessageOptions
    {
        AutoComplete = false,
        MaxConcurrentCalls = 20
    });
```

A `MessagingFactory` létrehoz egy `factory` objektumot a kapcsolatok sztringből. A `factory` példányt a példánya hozza `MessageReceiver` létre. Ezután a `receiver` példány az üzenetkezelő kezelő regisztrálására szolgál.

---

## <a name="receive-mode"></a>Fogadási mód

Üzenetsor vagy előfizetési ügyfél létrehozásakor megadhat egy fogadási módot: *betekintés zárolás* vagy *fogadás és törlés*. Az alapértelmezett fogadási mód a `PeekLock` . Ha az alapértelmezett módban működik, az ügyfél kérést küld, hogy üzenetet kapjon Service Busról. Miután az ügyfél megkapta az üzenetet, elküld egy kérést az üzenet végrehajtásához.

A fogadási mód beállításakor `ReceiveAndDelete` mindkét lépés egyetlen kérelemben van egyesítve. Ezek a lépések csökkentik a műveletek teljes számát, és javítják az üzenetek teljes átviteli sebességét. Ez a teljesítmény az üzenetek elvesztésének kockázatával jár.

A Service Bus nem támogatja a fogadási és törlési műveletek tranzakcióit. A betekintés zárolása olyan helyzetekben is szükséges, amikor az ügyfél késleltetni vagy [kézbesíteni](service-bus-dead-letter-queues.md) kívánja az üzenetet.

## <a name="client-side-batching"></a>Ügyféloldali kötegek

Az ügyféloldali kötegek lehetővé teszik egy üzenetsor vagy egy témakör-ügyfél számára, hogy egy adott időtartamon belül késleltetni lehessen az üzenet küldését. Ha az ügyfél további üzeneteket küld ezen időszakon belül, a rendszer egyetlen kötegben továbbítja ezen üzeneteket. Az ügyféloldali kötegek azt is eredményezik, hogy egy üzenetsor vagy egy előfizetési ügyfél több **teljes** kérelmet küld egyetlen kérelembe. A kötegelt feldolgozás csak aszinkron **küldési** és **befejezési** műveletekhez érhető el. A szinkron műveleteket a rendszer azonnal elküldi a Service Bus szolgáltatásnak. A betekintési és fogadási műveletekhez nem kerül sor, és a kötegelt feldolgozás nem történik meg az ügyfeleken.

# <a name="azuremessagingservicebus-sdk"></a>[Azure. Messaging. ServiceBus SDK](#tab/net-standard-sdk-2)
A .NET Standard SDK batching funkciója még nem tesz elérhetővé egy tulajdonságot a kezeléshez.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. ServiceBus SDK](#tab/net-standard-sdk)

A .NET Standard SDK batching funkciója még nem tesz elérhetővé egy tulajdonságot a kezeléshez.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. ServiceBus SDK](#tab/net-framework-sdk)

Alapértelmezés szerint az ügyfél 20 ms-os batch-intervallumot használ. A Batch-intervallum módosításához állítsa a [BatchFlushInterval][BatchFlushInterval] tulajdonságot az üzenetküldési gyár létrehozása előtt. Ez a beállítás a gyár által létrehozott összes ügyfelet érinti.

A kötegelt feldolgozás letiltásához állítsa a [BatchFlushInterval][BatchFlushInterval] tulajdonságot **TimeSpan. Zero** értékre. Például:

```csharp
var settings = new MessagingFactorySettings
{
    NetMessagingTransportSettings =
    {
        BatchFlushInterval = TimeSpan.Zero
    }
};
var factory = MessagingFactory.Create(namespaceUri, settings);
```

A kötegelt feldolgozás nem befolyásolja a számlázható üzenetkezelési műveletek számát, és csak a [Microsoft. ServiceBus. Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) függvénytár használatával érhető el a Service Bus ügyfél protokollja számára. A HTTP protokoll nem támogatja a kötegelt feldolgozást.

> [!NOTE]
> A beállítás `BatchFlushInterval` biztosítja, hogy a kötegelt feldolgozás implicit legyen az alkalmazás szemszögéből. i.e. az alkalmazás kezdeményez `SendAsync` és `CompleteAsync` hív, és nem hajt végre konkrét batch-hívásokat.
>
> A explicit ügyféloldali kötegelt feldolgozást az alábbi metódus hívásával lehet megvalósítani:
> ```csharp
> Task SendBatchAsync(IEnumerable<BrokeredMessage> messages);
> ```
> Itt az üzenetek összesített méretének kisebbnek kell lennie az árképzési szinten támogatott maximális méretnél.

---

## <a name="batching-store-access"></a>Tároló-hozzáférés feldolgozása

Egy üzenetsor, témakör vagy előfizetés átviteli sebességének növeléséhez Service Bus batchs több üzenetet, amikor a belső tárolóba ír. 

- Ha engedélyezi a kötegelt feldolgozást egy várólistán, az üzeneteket az áruházba írja, és a tárolóban lévő üzenetek törlését a rendszer kötegbe állítja. 
- Ha engedélyezi a kötegelt feldolgozást egy témakörben, az üzenetek a tárolóba való írása kötegelt. 
- Ha engedélyezi a kötegelt feldolgozást egy előfizetésre, az üzenetek az áruházból való törlése kötegelt. 
- Ha egy entitáshoz engedélyezve van a batchd Store-hozzáférés, Service Bus akár 20 MS-ra késlelteti az adott entitás tárolási írási műveletét.

> [!NOTE]
> A kötegelt feldolgozással nem lehet az üzenetek elvesztésének kockázata, még akkor is, ha a 20ms-köteg intervallumának végén Service Bus hiba történt.

Az ezen intervallumban megjelenő további tárolási műveletek hozzáadódnak a köteghez. A kötegelt tárolók hozzáférése csak a **küldési** és a **befejezési** műveleteket érinti; a fogadási műveletek nem érintettek. A kötegelt tár hozzáférése egy entitás egyik tulajdonsága. A kötegelt tárolás az összes olyan entitáson megtörténik, amelyek engedélyezik a Batch-tárolók elérését.

Új üzenetsor, témakör vagy előfizetés létrehozásakor a kötegelt tároló-hozzáférés alapértelmezés szerint engedélyezve van.


# <a name="azuremessagingservicebus-sdk"></a>[Azure. Messaging. ServiceBus SDK](#tab/net-standard-sdk-2)
A kötegelt tárolás hozzáférésének letiltásához szüksége lesz egy példányára `ServiceBusAdministrationClient` . Hozzon létre egy `CreateQueueOptions` üzenetsor-leírást, amely a `EnableBatchedOperations` tulajdonságot állítja be `false` .

```csharp
var options = new CreateQueueOptions(path)
{
    EnableBatchedOperations = false
};
var queue = await administrationClient.CreateQueueAsync(options);
```


# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. ServiceBus SDK](#tab/net-standard-sdk)

A kötegelt tárolás hozzáférésének letiltásához szüksége lesz egy példányára `ManagementClient` . Hozzon létre egy üzenetsor-leírást, amely a `EnableBatchedOperations` tulajdonságot állítja be `false` .

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = await managementClient.CreateQueueAsync(queueDescription);
```

További információért tekintse át a következő cikkeket:
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.queuedescription.enablebatchedoperations" target="_blank">`Microsoft.Azure.ServiceBus.Management.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.subscriptiondescription.enablebatchedoperations" target="_blank">`Microsoft.Azure.ServiceBus.Management.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.topicdescription.enablebatchedoperations" target="_blank">`Microsoft.Azure.ServiceBus.Management.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. ServiceBus SDK](#tab/net-framework-sdk)

A kötegelt tárolás hozzáférésének letiltásához szüksége lesz egy példányára `NamespaceManager` . Hozzon létre egy üzenetsor-leírást, amely a `EnableBatchedOperations` tulajdonságot állítja be `false` .

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = namespaceManager.CreateQueue(queueDescription);
```

További információért tekintse át a következő cikkeket:
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations" target="_blank">`Microsoft.ServiceBus.Messaging.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.enablebatchedoperations" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.topicdescription.enablebatchedoperations" target="_blank">`Microsoft.ServiceBus.Messaging.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

A batchd Store-hozzáférés nem befolyásolja a számlázható üzenetkezelési műveletek számát. Ez egy üzenetsor, témakör vagy előfizetés tulajdonsága. Független a fogadási módtól és az ügyfél és a Service Bus szolgáltatás között használt protokolltól.

## <a name="prefetching"></a>Prefetching

Az előzetes [beolvasás](service-bus-prefetch.md) lehetővé teszi, hogy a várólista vagy az előfizetési ügyfél további üzeneteket töltsön be a szolgáltatástól, amikor üzeneteket fogad. Az ügyfél helyi gyorsítótárban tárolja ezeket az üzeneteket. A gyorsítótár méretét a vagy a tulajdonságok határozzák `QueueClient.PrefetchCount` meg `SubscriptionClient.PrefetchCount` . Minden olyan ügyfél, amely lehetővé teszi, hogy az előhívások saját gyorsítótárat tároljanak. A gyorsítótárak nem oszthatók meg az ügyfelek között. Ha az ügyfél elindít egy fogadási műveletet, és a gyorsítótára üres, a szolgáltatás egy köteg üzenetet küld. A köteg mérete megegyezik a gyorsítótár vagy a 256 KB-os méretével, attól függően, hogy melyik a kisebb. Ha az ügyfél elindít egy fogadási műveletet, és a gyorsítótár egy üzenetet tartalmaz, a rendszer az üzenetet a gyorsítótárból veszi át.

Egy üzenet előhívásakor a szolgáltatás zárolja az előhívott üzenetet. A zárolással az előhívott üzenetet nem lehet egy másik fogadótól fogadni. Ha a fogadó nem tudja befejezni az üzenetet a zárolás lejárta előtt, az üzenet elérhetővé válik más fogadók számára. Az üzenet előre beolvasott másolata a gyorsítótárban marad. A lejárt gyorsítótárazott példányt használó fogadó kivételt fog kapni, amikor megpróbálja befejezni az üzenetet. Alapértelmezés szerint az üzenet zárolása 60 másodperc után lejár. Ez az érték 5 percre bővíthető. A lejárt üzenetek felhasználásának megakadályozásához állítsa be a gyorsítótár méretét a zárolási időkorlát intervallumán belül az ügyfél által felhasználható üzenetek számánál kisebb értékre.

A 60 másodperces alapértelmezett zárolás lejáratának használatakor a megfelelő érték `PrefetchCount` 20-szor a gyári vevőkészülékek maximális feldolgozási sebességével. Egy gyár például három fogadót hoz létre, és az egyes fogadók másodpercenként akár 10 üzenetet is feldolgozhatnak. A behívások száma nem haladhatja meg a 20 X 3 X 10 = 600. Alapértelmezés szerint a `PrefetchCount` értéke 0, ami azt jelenti, hogy a szolgáltatás nem olvas be további üzeneteket.

Az üzenetek előhívása növeli a várólista vagy előfizetés teljes átviteli sebességét, mivel csökkenti az üzenetküldési műveletek teljes számát vagy az oda-és visszaútt. Az első üzenet beolvasása azonban hosszabb időt vesz igénybe (a megnövekedett üzenet mérete miatt). Az előre beolvasott üzenetek a gyorsítótárból való fogadása gyorsabb, mert az ügyfél már letöltötte ezeket az üzeneteket.

Az üzenet élettartam (TTL) tulajdonságát a kiszolgáló ellenőrzi, amikor a kiszolgáló elküldi az üzenetet az ügyfélnek. Az üzenet fogadásakor az ügyfél nem vizsgálja meg az üzenet TTL tulajdonságát. Ehelyett az üzenet akkor is megjelenhet, ha az üzenet TTL-értéke megtelt, miközben az ügyfél gyorsítótárazta az üzenetet.

Az előolvasás nem befolyásolja a számlázható üzenetkezelési műveletek számát, és csak az Service Bus ügyfél protokollja számára érhető el. A HTTP protokoll nem támogatja a beolvasást. Az előhívás a szinkron és aszinkron fogadási műveletekhez is elérhető.

# <a name="azuremessagingservicebus-sdk"></a>[Azure. Messaging. ServiceBus SDK](#tab/net-standard-sdk-2)
További információkért lásd a következő `PrefetchCount` tulajdonságokat:

- [ServiceBusReceiver.PrefetchCount](/dotnet/api/azure.messaging.servicebus.servicebusreceiver.prefetchcount)
- [ServiceBusProcessor.PrefetchCount](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.prefetchcount)

Ezen tulajdonságok értékeit a [ServiceBusReceiverOptions](/dotnet/api/azure.messaging.servicebus.servicebusreceiveroptions) vagy a [ServiceBusProcessorOptions](/dotnet/api/azure.messaging.servicebus.servicebusprocessoroptions)tulajdonságban adhatja meg.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. ServiceBus SDK](#tab/net-standard-sdk)

További információkért lásd a következő `PrefetchCount` tulajdonságokat:

* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount" target="_blank">`Microsoft.Azure.ServiceBus.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.subscriptionclient.prefetchcount" target="_blank">`Microsoft.Azure.ServiceBus.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. ServiceBus SDK](#tab/net-framework-sdk)

További információkért lásd a következő `PrefetchCount` tulajdonságokat:

* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queueclient.prefetchcount" target="_blank">`Microsoft.ServiceBus.Messaging.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.prefetchcount" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

## <a name="prefetching-and-receivebatch"></a>Előhívás és ReceiveBatch
Habár a több üzenet előhívásának fogalmai hasonló szemantikai feladattal rendelkeznek a Batch () üzeneteinek feldolgozásához `ReceiveBatch` , néhány kisebb eltérést kell szem előtt tartani, amikor ezeket a módszereket együtt használják.

A kiküldés egy konfiguráció (vagy mód) az ügyfélen ( `QueueClient` és `SubscriptionClient` ), és `ReceiveBatch` egy művelet (amely a kérés-válasz szemantikagal rendelkezik).

A megközelítések együttes használata közben vegye figyelembe a következő eseteket:

* A prefektusnak nagyobbnak vagy egyenlőnek kell lennie, mint a várt üzenetek száma `ReceiveBatch` .
* A kiállítók akár n/3-szor is lehetnek a feldolgozott üzenetek másodpercenkénti száma, ahol n az alapértelmezett zárolási időtartam.

Vannak olyan kihívások, amelyek kapzsi megközelítéssel rendelkeznek, azaz a visszahívások száma magas, mert ez azt jelenti, hogy az üzenet egy adott fogadó számára van zárolva. A javaslat célja, hogy kipróbálja a fenti küszöbértékek közötti kiugró értékeket, és empirikusan azonosítsa, hogy mi illik hozzá.

## <a name="multiple-queues"></a>Több várólista

Ha egyetlen üzenetsor vagy témakör nem tudja kezelni a vártat, használjon több üzenetkezelési entitást. Ha több entitást használ, hozzon létre egy dedikált ügyfelet az egyes entitásokhoz ahelyett, hogy ugyanazt az ügyfelet használja az összes entitáshoz.

## <a name="development-and-testing-features"></a>Fejlesztési és tesztelési funkciók

> [!NOTE]
> Ez a szakasz csak a WindowsAzure. ServiceBus SDK-ra vonatkozik, mivel a Microsoft. Azure. ServiceBus és az Azure. Messaging. ServiceBus nem teszi elérhetővé ezt a funkciót.

Service Bus rendelkezik egy, a fejlesztéshez használt funkcióval, amelyet **soha nem szabad éles konfigurációban használni**: [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering] .

Ha új szabályok vagy szűrők vannak hozzáadva a témakörhöz, a segítségével [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering] ellenőrizheti, hogy az új szűrő kifejezés a várt módon működik-e.

## <a name="scenarios"></a>Forgatókönyvek

A következő szakaszok ismertetik a tipikus üzenetkezelési forgatókönyveket, és körvonalazzák az előnyben részesített Service Bus beállításait. Az átviteli sebességet kisméretű (kevesebb, mint 1 üzenet/másodperc), mérsékelt (1 üzenet/másodperc vagy nagyobb, de kevesebb mint 100 üzenet/másodperc) és magas (100 üzenet/másodperc vagy nagyobb) értékre sorolják be. Az ügyfelek száma kisméretű (5 vagy kevesebb), mérsékelt (több mint 5, de legfeljebb 20) és nagy (több mint 20).

### <a name="high-throughput-queue"></a>Nagy átviteli sebességű üzenetsor

Cél: egyetlen üzenetsor átviteli sebességének maximalizálása. A küldők és a fogadók száma kicsi.

* A teljes küldési aránynak a várólistára való növeléséhez használjon több Message Factoryt a küldők létrehozásához. Mindegyik feladó esetében használjon aszinkron műveleteket vagy több szálat.
* A várólista általános fogadási sebességének növeléséhez használjon több Message Factoryt a fogadók létrehozásához.
* Aszinkron műveletek használatával kihasználhatja az ügyféloldali kötegek előnyeit.
* Állítsa a batching intervallumot 50 MS értékre, hogy csökkentse a Service Bus ügyfél-protokollok átvitelének számát. Ha több feladót használ, növelje a kötegelt intervallumot 100 MS-ra.
* A kötegelt tárolók elérésének engedélyezése engedélyezve. Ez a hozzáférés növeli az üzenetek várólistába írásának teljes mértékét.
* Állítsa be a visszahívások számát a gyári összes fogadójának maximális feldolgozási sebességének 20-szor értékére. Ez a szám csökkenti a Service Bus ügyfél-protokollok átvitelét.

### <a name="multiple-high-throughput-queues"></a>Több nagy átviteli sebességű várólista

Cél: több várólista teljes átviteli sebességének maximalizálása. Az egyes várólisták átviteli sebessége közepes vagy magas.

A maximális átviteli sebesség több várólistára történő beszerzéséhez használja a következő beállításokat, hogy maximalizálja egy adott várólista átviteli sebességét. Más gyárakat is használhat a különböző várólistákból küldött vagy fogadott ügyfelek létrehozásához.

### <a name="low-latency-queue"></a>Kis késleltetésű várólista

Cél: egy üzenetsor vagy témakör késésének csökkentése. A küldők és a fogadók száma kicsi. A várólista átviteli sebessége kicsi vagy mérsékelt.

* Tiltsa le az ügyféloldali kötegeket. Az ügyfél azonnal üzenetet küld.
* A kötegelt tárolók elérésének letiltása. A szolgáltatás azonnal beírja az üzenetet az áruházba.
* Ha egyetlen ügyfelet használ, állítsa be a visszahívások számát a fogadó feldolgozási sebessége szerint 20-szor. Ha egyszerre több üzenet érkezik a várólistára, a Service Bus ügyfél-protokoll egyszerre továbbítja őket. Amikor az ügyfél megkapja a következő üzenetet, az üzenet már szerepel a helyi gyorsítótárban. A gyorsítótárnak kicsinek kell lennie.
* Ha több ügyfelet használ, állítsa a prefektusok száma értéket 0-ra. A szám beállításával a második ügyfél a második üzenetet is megkapja, amikor az első ügyfél még dolgozza fel az első üzenetet.

### <a name="queue-with-a-large-number-of-senders"></a>Üzenetsor nagy számú küldővel

Cél: egy üzenetsor vagy témakör maximális átviteli sebessége nagy számú küldővel. Mindegyik feladó mérsékelt sebességgel küld üzeneteket. A fogadók száma kicsi.

A Service Bus akár 1000 egyidejű kapcsolatot tesz lehetővé egy üzenetküldési entitással. Ezt a korlátot a névtér szintjén kell kikényszeríteni, és a várólisták, témakörök vagy előfizetések maximális száma a névtér egyidejű kapcsolatainak korlátja. A várólisták esetében ez a szám a küldők és a fogadók között van megosztva. Ha az összes 1000-kapcsolat szükséges a küldők számára, cserélje le a várólistát egy témakörre és egy előfizetésre. A témakörök legfeljebb 1000 egyidejű kapcsolatot fogadnak el a küldők. Az előfizetés további 1000 egyidejű kapcsolatokat fogad el a fogadók között. Ha több mint 1000 egyidejű küldőre van szükség, a küldőknek HTTP-n keresztül kell üzeneteket küldeniük a Service Bus protokollnak.

Az átviteli sebesség maximalizálása érdekében hajtsa végre az alábbi lépéseket:

* Ha minden feladó egy másik folyamatban van, csak egyetlen gyárat használjon egy folyamaton belül.
* Aszinkron műveletek használatával kihasználhatja az ügyféloldali kötegek előnyeit.
* A 20 MS alapértelmezett batch-intervallum használatával csökkentse a Service Bus az ügyfél-protokollok átvitelének számát.
* A kötegelt tárolók elérésének engedélyezése engedélyezve. Ez a hozzáférés növeli az üzenetek üzenetsor vagy témakörbe való írásának általános sebességét.
* Állítsa be a visszahívások számát a gyári összes fogadójának maximális feldolgozási sebességének 20-szor értékére. Ez a szám csökkenti a Service Bus ügyfél-protokollok átvitelét.

### <a name="queue-with-a-large-number-of-receivers"></a>Üzenetsor nagy számú fogadóval

Cél: a várólista vagy előfizetés fogadási sebességének maximalizálása nagy számú fogadóval. Minden fogadó mérsékelt sebességgel fogad üzeneteket. A küldők száma kicsi.

A Service Bus akár 1000 egyidejű kapcsolatot tesz lehetővé egy entitással. Ha egy üzenetsor több mint 1000 fogadót igényel, cserélje le a várólistát egy témakörre és több előfizetésre. Az egyes előfizetések akár 1000 egyidejű kapcsolatot is támogatnak. Másik lehetőségként a fogadók a HTTP protokollon keresztül érhetik el a várólistát.

Az átviteli sebesség maximalizálása érdekében kövesse az alábbi irányelveket:

* Ha minden fogadó egy másik folyamatban van, csak egyetlen gyárat használjon a folyamaton belül.
* A fogadók szinkron vagy aszinkron műveleteket is használhatnak. Az egyes fogadók mérsékelt fogadási sebessége miatt a teljes kérelem ügyféloldali kötegelt feldolgozása nem befolyásolja a fogadó átviteli sebességét.
* A kötegelt tárolók elérésének engedélyezése engedélyezve. Ez a hozzáférés csökkenti az entitás teljes terhelését. Emellett csökkenti az üzenetek üzenetsor vagy témakörbe való írásának általános sebességét is.
* Állítsa be a prefektusi számot egy kis értékre (például PrefetchCount = 10). Ez a szám megakadályozza, hogy a fogadók tétlenek legyenek, míg a többi fogadó nagy számú üzenetet gyorsítótárazott.

### <a name="topic-with-a-few-subscriptions"></a>Témakör néhány előfizetéssel

Cél: a témakör teljesítményének maximalizálása néhány előfizetéssel. Számos előfizetés fogad egy üzenetet, ami azt jelenti, hogy az összes előfizetéshez tartozó összesített fogadási arány nagyobb a küldési aránynál. A küldők száma kicsi. Az előfizetéshez tartozó fogadók száma kicsi.

Az átviteli sebesség maximalizálása érdekében kövesse az alábbi irányelveket:

* A teljes küldési aránynak a témakörben való növeléséhez használjon több Message Factoryt a küldők létrehozásához. Mindegyik feladó esetében használjon aszinkron műveleteket vagy több szálat.
* Az előfizetés teljes fogadási arányának növeléséhez használjon több Message Factoryt a fogadók létrehozásához. Minden egyes fogadó esetében használjon aszinkron műveleteket vagy több szálat.
* Aszinkron műveletek használatával kihasználhatja az ügyféloldali kötegek előnyeit.
* A 20 MS alapértelmezett batch-intervallum használatával csökkentse a Service Bus az ügyfél-protokollok átvitelének számát.
* A kötegelt tárolók elérésének engedélyezése engedélyezve. Ez a hozzáférés növeli a témakörbe írt üzenetek összesített arányát.
* Állítsa be a visszahívások számát a gyári összes fogadójának maximális feldolgozási sebességének 20-szor értékére. Ez a szám csökkenti a Service Bus ügyfél-protokollok átvitelét.

### <a name="topic-with-a-large-number-of-subscriptions"></a>Számos előfizetéssel rendelkező témakör

Cél: nagy mennyiségű előfizetéssel maximalizálhatja a témakör átviteli sebességét. Számos előfizetés fogad egy üzenetet, ami azt jelenti, hogy az összes előfizetéshez tartozó összesített fogadási arány sokkal nagyobb, mint a küldési sebesség. A küldők száma kicsi. Az előfizetéshez tartozó fogadók száma kicsi.

A nagy számú előfizetéssel rendelkező témakörök általában alacsony teljes átviteli sebességet tesznek elérhetővé, ha az összes üzenet az összes előfizetéshez van irányítva. Ez azért van, mert minden üzenet többször érkezik, és a témakörben szereplő összes üzenet és az összes előfizetése ugyanabban a tárolóban van tárolva. Ennek feltételezése, hogy a küldők száma és az előfizetéshez tartozó fogadók száma kicsi. A Service Bus egy témakörben legfeljebb 2 000 előfizetést támogat.

Az átviteli sebesség maximalizálása érdekében próbálkozzon az alábbi lépésekkel:

* Aszinkron műveletek használatával kihasználhatja az ügyféloldali kötegek előnyeit.
* A 20 MS alapértelmezett batch-intervallum használatával csökkentse a Service Bus az ügyfél-protokollok átvitelének számát.
* A kötegelt tárolók elérésének engedélyezése engedélyezve. Ez a hozzáférés növeli a témakörbe írt üzenetek összesített arányát.
* Állítsa be a visszahívások darabszámát a várt fogadási sebesség (másodperc) értékének 20-szor. Ez a szám csökkenti a Service Bus ügyfél-protokollok átvitelét.

<!-- .NET Standard SDK, Microsoft.Azure.ServiceBus -->
[QueueClient]: /dotnet/api/microsoft.azure.servicebus.queueclient
[MessageSender]: /dotnet/api/microsoft.azure.servicebus.core.messagesender

<!-- .NET Framework SDK, Microsoft.Azure.ServiceBus -->
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[BatchFlushInterval]: /dotnet/api/microsoft.servicebus.messaging.messagesender.batchflushinterval
[ForcePersistence]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.forcepersistence
[EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnableFiltering]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablefilteringmessagesbeforepublishing

<!-- Local links -->
[Partitioned messaging entities]: service-bus-partitioning.md
