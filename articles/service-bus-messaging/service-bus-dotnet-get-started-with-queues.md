---
title: Ismerkedés a Azure Service Bus Queues szolgáltatással (Azure. Messaging. ServiceBus)
description: Ebben az oktatóanyagban egy .NET Core C#-alkalmazást hoz létre, amely üzeneteket küld és fogad üzeneteket egy Service Bus-várólistából.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 11/13/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ec3f53e6f69614028c013efa5f0e6852cbc3f8ae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98631640"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-net"></a>Üzenetek küldése és fogadása Azure Service Bus várólistákból (.NET)
Ebben az oktatóanyagban egy .NET Core Console-alkalmazást hoz létre, amely az **Azure. Messaging. ServiceBus** csomag használatával üzeneteket küld és fogad üzeneteket egy Service Bus-várólistából. 

> [!Important]
> Ez a rövid útmutató az új Azure. Messaging. ServiceBus csomagot használja. A régi Microsoft. Azure. ServiceBus csomagot használó gyors útmutató: [események küldése és fogadása a Microsoft. Azure. ServiceBus csomag használatával](service-bus-dotnet-get-started-with-queues-legacy.md).

## <a name="prerequisites"></a>Előfeltételek

- [Visual Studio 2019](https://www.visualstudio.com/vs)
- Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja MSDN- [előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) , vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Ha nem rendelkezik várólistával, hogy működjön a szolgáltatással, a várólista létrehozásához kövesse az [Azure Portal használata Service Bus üzenetsor létrehozásához](service-bus-quickstart-portal.md) című cikket. Jegyezze **fel a Service Bus névtér és a létrehozott** **várólista** nevét.

## <a name="send-messages-to-a-queue"></a>Üzenetek küldése egy üzenetsorba
Ebben a rövid útmutatóban létrehoz egy C# .NET Core Console-alkalmazást, amely üzeneteket küld a várólistára.

### <a name="create-a-console-application"></a>Konzolalkalmazás létrehozása
Indítsa el a Visual studiót, és hozzon létre egy új **Console app (.net Core)** projektet a C# nyelvhez. 

### <a name="add-the-service-bus-nuget-package"></a>A Service Bus NuGet-csomag hozzáadása

1. Kattintson a jobb gombbal az újonnan létrehozott projektre, és válassza a **Manage Nuget Packages** (NuGet-csomagok kezelése) lehetőséget.
1. Válassza a **Tallózás** lehetőséget. Keresse meg és válassza ki az **[Azure. Messaging. ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/)** elemet.
1. A telepítés befejezéséhez válassza a **telepítés** lehetőséget, majd a NuGet csomagkezelő elemet.

### <a name="add-code-to-send-messages-to-the-queue"></a>Kód hozzáadása az üzenetek várólistába küldéséhez

1. A *program. cs programban* adja hozzá a következő `using` utasításokat a névtér definíciójának elejéhez, az osztály deklarációja előtt:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    
    using Azure.Messaging.ServiceBus;
    ```

1. A `Program` osztályban deklarálja a következő változókat:

    ```csharp
        static string connectionString = "<NAMESPACE CONNECTION STRING>";
        static string queueName = "<QUEUE NAME>";
    ```

    Adja meg a névtérhez tartozó kapcsolatok karakterláncát `connectionString` változóként. Adja meg a várólista nevét.

1. Közvetlenül a `Main()` metódus után adja hozzá a következő `SendMessagesAsync()` metódust, amely az üzenet küldésének munkáját végzi el:

    ```csharp
        static async Task SendMessageAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a sender for the queue 
                ServiceBusSender sender = client.CreateSender(queueName);

                // create a message that we can send
                ServiceBusMessage message = new ServiceBusMessage("Hello world!");

                // send the message
                await sender.SendMessageAsync(message);
                Console.WriteLine($"Sent a single message to the queue: {queueName}");
            }
        }
    ```
1. Adjon hozzá egy nevű metódust `CreateMessages` egy üzenetsor (.net-várólista) az osztályhoz való létrehozásához `Program` . Ezeket az üzeneteket általában az alkalmazás különböző részeiről szerezheti be. Itt hozzuk létre a mintaadatok várólistáját.

    ```csharp
        static Queue<ServiceBusMessage> CreateMessages()
        {
            // create a queue containing the messages and return it to the caller
            Queue<ServiceBusMessage> messages = new Queue<ServiceBusMessage>();
            messages.Enqueue(new ServiceBusMessage("First message in the batch"));
            messages.Enqueue(new ServiceBusMessage("Second message in the batch"));
            messages.Enqueue(new ServiceBusMessage("Third message in the batch"));
            return messages;
        }
    ```
1. Adjon hozzá egy nevű metódust `SendMessageBatchAsync` a `Program` osztályhoz, és adja hozzá a következő kódot. Ez a metódus az üzenetek várólistáját veszi fel, és előkészíti egy vagy több köteg küldését az Service Bus várólistára. 

    ```csharp
        static async Task SendMessageBatchAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a sender for the queue 
                ServiceBusSender sender = client.CreateSender(queueName);

                // get the messages to be sent to the Service Bus queue
                Queue<ServiceBusMessage> messages = CreateMessages();

                // total number of messages to be sent to the Service Bus queue
                int messageCount = messages.Count;

                // while all messages are not sent to the Service Bus queue
                while (messages.Count > 0)
                {
                    // start a new batch 
                    using ServiceBusMessageBatch messageBatch = await sender.CreateMessageBatchAsync();

                    // add the first message to the batch
                    if (messageBatch.TryAddMessage(messages.Peek()))
                    {
                        // dequeue the message from the .NET queue once the message is added to the batch
                        messages.Dequeue();
                    }
                    else
                    {
                        // if the first message can't fit, then it is too large for the batch
                        throw new Exception($"Message {messageCount - messages.Count} is too large and cannot be sent.");
                    }

                    // add as many messages as possible to the current batch
                    while (messages.Count > 0 && messageBatch.TryAddMessage(messages.Peek()))
                    {
                        // dequeue the message from the .NET queue as it has been added to the batch
                        messages.Dequeue();
                    }
        
                    // now, send the batch
                    await sender.SendMessagesAsync(messageBatch);
        
                    // if there are any remaining messages in the .NET queue, the while loop repeats 
                }

                Console.WriteLine($"Sent a batch of {messageCount} messages to the topic: {queueName}");
            }
        }
    ```
1. Cserélje le a `Main()` metódust a következő **aszinkron** `Main` metódusra. A küldési metódusokat úgy hívja meg, hogy egyetlen üzenetet és egy köteget küldjenek a várólistába. 

    ```csharp
        static async Task Main()
        {
            // send a message to the queue
            await SendMessageAsync();

            // send a batch of messages to the queue
            await SendMessageBatchAsync();
        }
    ```
5. Futtassa az alkalmazást. A következő üzeneteknek kell megjelennie. 

    ```console
    Sent a single message to the queue: myqueue
    Sent a batch of messages to the queue: myqueue
    ```       
1. A Azure Portal hajtsa végre az alábbi lépéseket:
    1. Navigáljon a Service Bus névtérhez. 
    1. Az **Áttekintés** lapon válassza ki a várólistát az alsó középső ablaktáblán. 
    1. Figyelje meg az **alapvető** erőforrások szakaszban található értékeket.

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/sent-messages-essentials.png" alt-text="Fogadott üzenetek száma és mérete" lightbox="./media/service-bus-dotnet-get-started-with-queues/sent-messages-essentials.png":::

    Figyelje meg a következő értékeket:
    - A várólista **aktív üzenetek száma** értékének értéke most **4**. Minden alkalommal, amikor a küldő alkalmazást az üzenetek lekérése nélkül futtatja, ez az érték 4-re nő.
    - A várólista jelenlegi mérete minden alkalommal megnöveli az **alapok** **aktuális** értékét, amikor az alkalmazás üzeneteket hoz létre a várólistába.
    - Az alsó **mérőszámok** szakaszban lévő **üzenetek** diagramon láthatja, hogy négy bejövő üzenet van a várólistához. 

## <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása egy várólistából
Ebben a szakaszban kódot fog hozzáadni az üzenetek várólistából való lekéréséhez.

1. Adja hozzá a következő metódusokat a `Program` osztályhoz, amely kezeli az üzeneteket és az esetleges hibákat. 

    ```csharp
        // handle received messages
        static async Task MessageHandler(ProcessMessageEventArgs args)
        {
            string body = args.Message.Body.ToString();
            Console.WriteLine($"Received: {body}");

            // complete the message. messages is deleted from the queue. 
            await args.CompleteMessageAsync(args.Message);
        }

        // handle any errors when receiving messages
        static Task ErrorHandler(ProcessErrorEventArgs args)
        {
            Console.WriteLine(args.Exception.ToString());
            return Task.CompletedTask;
        }
    ```
1. Adjon hozzá egy nevű metódust `ReceiveMessagesAsync` a `Program` osztályhoz, és adja hozzá a következő kódot az üzenetek fogadásához. 

    ```csharp
        static async Task ReceiveMessagesAsync()
        {
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a processor that we can use to process the messages
                ServiceBusProcessor processor = client.CreateProcessor(queueName, new ServiceBusProcessorOptions());

                // add handler to process messages
                processor.ProcessMessageAsync += MessageHandler;

                // add handler to process any errors
                processor.ProcessErrorAsync += ErrorHandler;

                // start processing 
                await processor.StartProcessingAsync();

                Console.WriteLine("Wait for a minute and then press any key to end the processing");
                Console.ReadKey();

                // stop processing 
                Console.WriteLine("\nStopping the receiver...");
                await processor.StopProcessingAsync();
                Console.WriteLine("Stopped receiving messages");
            }
        }
    ```
1. Adjon hozzá `ReceiveMessagesAsync` metódust a metódushoz `Main` . `SendMessagesAsync`Ha csak az üzenetek fogadását szeretné tesztelni, tegye megjegyzésbe a metódust. Ha nem, akkor a várólistára küldött további négy üzenet jelenik meg. 

    ```csharp
        static async Task Main()
        {
            // send a message to the queue
            await SendMessageAsync();

            // send a batch of messages to the queue
            await SendMessageBatchAsync();

            // receive message from the queue
            await ReceiveMessagesAsync();
        }
    ```

## <a name="run-the-app"></a>Az alkalmazás futtatása
Futtassa az alkalmazást. Várjon egy percet, majd nyomja le az egyik billentyűt az üzenetek fogadásának leállításához. A következő kimenetnek kell megjelennie (szóköz a kulcshoz). 

```console
Sent a single message to the queue: myqueue
Sent a batch of messages to the queue: myqueue
Wait for a minute and then press any key to end the processing
Received: Hello world!
Received: First message in the batch
Received: Second message in the batch
Received: Third message in the batch
Received: Hello world!
Received: First message in the batch
Received: Second message in the batch
Received: Third message in the batch

Stopping the receiver...
Stopped receiving messages
```

Próbálkozzon újra a portálon. 

- Az **aktív üzenetek száma** és a **jelenlegi** értékek mostantól **0**.
- Az alsó **mérőszámok** szakaszban lévő **üzenetek** diagramon láthatja, hogy nyolc bejövő üzenet és nyolc kimenő üzenet van a várólistához. 

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/queue-messages-size-final.png" alt-text="Aktív üzenetek és méret a fogadás után" lightbox="./media/service-bus-dotnet-get-started-with-queues/queue-messages-size-final.png":::

## <a name="next-steps"></a>Következő lépések
Tekintse meg a következő dokumentációt és mintákat:

- [A .NET-hez készült ügyféloldali kódtár Azure Service Bus – readme](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [Minták a GitHubon](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [.NET API-referencia](/dotnet/api/azure.messaging.servicebus?preserve-view=true)