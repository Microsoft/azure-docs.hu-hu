---
title: Bevezetés az Azure Service Bus által kezelt üzenetsorok használatába | Microsoft Docs
description: Ebben az oktatóanyagban .NET Core Console-alkalmazásokat hoz létre, amelyek üzeneteket küldenek és fogadnak egy Service Bus üzenetsor üzeneteit.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 09/01/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: e8e70884838d56003694e2da09668527ce5b6c7b
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652989"
---
# <a name="get-started-with-service-bus-queues"></a>Bevezetés a Service Bus által kezelt üzenetsorok használatába
Ebben az oktatóanyagban .NET Core Console-alkalmazásokat hoz létre, amelyek üzeneteket küldenek és fogadnak egy Service Bus üzenetsor üzeneteit.

> [!WARNING]
> Ez a rövid útmutató a régi Microsoft. Azure. ServiceBus csomagot használja. A legújabb Azure. Messaging. ServiceBus csomagot használó gyors útmutatóért lásd az [események küldése és fogadása az Azure. Messaging. ServiceBus csomag használatával](service-bus-dotnet-get-started-with-queues.md)című témakört. 

## <a name="prerequisites"></a>Előfeltételek

- [Visual Studio 2019](https://www.visualstudio.com/vs).
- [NET Core SDK](https://www.microsoft.com/net/download/windows), 2.0-s vagy újabb verzió.
- Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja MSDN- [előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) , vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Ha nem rendelkezik várólistával, hogy működjön a szolgáltatással, a várólista létrehozásához kövesse az [Azure Portal használata Service Bus üzenetsor létrehozásához](service-bus-quickstart-portal.md) című cikket.

  - Olvassa el Service Bus várólisták gyors áttekintését.
  - Hozzon létre egy Service Bus névteret.
  - A kapcsolatok karakterláncának beolvasása.
  - Hozzon létre egy Service Bus üzenetsor.

## <a name="send-messages"></a>Üzenetek küldése

A Visual Studio használatával írjon C# konzolalkalmazást az üzenetek üzenetsorba való küldéséhez.

### <a name="create-a-console-application"></a>Konzolalkalmazás létrehozása

Indítsa el a Visual studiót, és hozzon létre egy új **Console app (.net Core)** projektet a C# nyelvhez. Ez a példa az alkalmazás *CoreSenderApp* nevet.

### <a name="add-the-service-bus-nuget-package"></a>A Service Bus NuGet-csomag hozzáadása

1. Kattintson a jobb gombbal az újonnan létrehozott projektre, és válassza a **Manage Nuget Packages** (NuGet-csomagok kezelése) lehetőséget.
1. Válassza a **Tallózás** lehetőséget. Keresse meg és válassza ki a **[Microsoft. Azure. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)** elemet.
1. A telepítés befejezéséhez válassza a **telepítés** lehetőséget, majd a NuGet csomagkezelő elemet.

    ![NuGet-csomag kiválasztása][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-queue"></a>Kód írása az üzenetek üzenetsorba való küldéséhez

1. A *program.cs*-ben adja hozzá a következő `using` utasításokat a névtér definíciójának elejéhez az osztály deklarációja előtt:

    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

1. A `Program` osztályban deklarálja a következő változókat:

    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ```

    Adja meg a névtérhez tartozó kapcsolatok karakterláncát `ServiceBusConnectionString` változóként. Adja meg a várólista nevét.

1. Cserélje le a `Main()` metódust a következő **aszinkron** `Main` metódusra. Meghívja a `SendMessagesAsync()` metódust, amelyet a következő lépésben fog hozzáadni, hogy üzeneteket küldjön a várólistára. 

    ```csharp
    public static async Task Main(string[] args)
    {    
        const int numberOfMessages = 10;
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();

        await queueClient.CloseAsync();
    }
    ```
1. Közvetlenül a `MainAsync()` metódus után adja hozzá a következő `SendMessagesAsync()` metódust, amely a által megadott számú üzenet küldését végzi `numberOfMessagesToSend` (jelenleg 10 értékre van állítva):

    ```csharp
    static async Task SendMessagesAsync(int numberOfMessagesToSend)
    {
        try
        {
            for (var i = 0; i < numberOfMessagesToSend; i++)
            {
                // Create a new message to send to the queue.
                string messageBody = $"Message {i}";
                var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                // Write the body of the message to the console.
                Console.WriteLine($"Sending message: {messageBody}");

                // Send the message to the queue.
                await queueClient.SendAsync(message);
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
        }
    }
    ```

A *program.cs* -fájlnak így kell kinéznie.

```csharp
namespace CoreSenderApp
{
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;

    class Program
    {
        // Connection String for the namespace can be obtained from the Azure portal under the 
        // 'Shared Access policies' section.
        const string ServiceBusConnectionString = "<your_connection_string>";
        const string QueueName = "<your_queue_name>";
        static IQueueClient queueClient;

        public static async Task Main(string[] args)
        {    
            const int numberOfMessages = 10;
            queueClient = new QueueClient(ServiceBusConnectionString, QueueName);
    
            Console.WriteLine("======================================================");
            Console.WriteLine("Press ENTER key to exit after sending all the messages.");
            Console.WriteLine("======================================================");
    
            // Send messages.
            await SendMessagesAsync(numberOfMessages);
    
            Console.ReadKey();
    
            await queueClient.CloseAsync();
        }

        static async Task SendMessagesAsync(int numberOfMessagesToSend)
        {
            try
            {
                for (var i = 0; i < numberOfMessagesToSend; i++)
                {
                    // Create a new message to send to the queue
                    string messageBody = $"Message {i}";
                    var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                    // Write the body of the message to the console
                    Console.WriteLine($"Sending message: {messageBody}");

                    // Send the message to the queue
                    await queueClient.SendAsync(message);
                }
            }
            catch (Exception exception)
            {
                Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
            }
        }
    }
}
```

Futtassa a programot, és keresse meg a Azure Portal.

A várólista- **alapok** megjelenítéséhez válassza ki a várólista nevét a névtér **áttekintési** ablakában.

![Fogadott üzenetek száma és mérete][queue-message]

A várólista **aktív üzenetek száma** értékének értéke most **10**. Minden alkalommal, amikor a küldő alkalmazást az üzenetek lekérése nélkül futtatja, ez az érték 10-re nő.

A várólista jelenlegi mérete minden alkalommal megnöveli az **alapok** **aktuális** értékét, amikor az alkalmazás üzeneteket hoz létre a várólistába.

A következő szakasz az üzenetek lekérésének módját ismerteti.

## <a name="receive-messages"></a>Üzenetek fogadása

Az elküldött üzenetek fogadásához hozzon létre egy másik **Console app (.net Core)** alkalmazást. Telepítse a **Microsoft. Azure. ServiceBus** NuGet csomagot, ahogy azt a küldő alkalmazás esetében tette.

### <a name="write-code-to-receive-messages-from-the-queue"></a>Kód írása az üzenetek üzenetsorból történő fogadásához

1. A *program.cs*-ben adja hozzá a következő `using` utasításokat a névtér definíciójának elejéhez az osztály deklarációja előtt:

    ```csharp
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

1. A `Program` osztályban deklarálja a következő változókat:

    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ```

    Adja meg a névtérhez tartozó kapcsolatok karakterláncát `ServiceBusConnectionString` változóként. Adja meg a várólista nevét.

1. Cserélje le az `Main()` metódust az alábbi kódra:

    ```csharp
    static void Main(string[] args)
    {
        MainAsync().GetAwaiter().GetResult();
    }

    static async Task MainAsync()
    {
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register QueueClient's MessageHandler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await queueClient.CloseAsync();
    }
    ```

1. Közvetlenül a `MainAsync()` metódus után adja hozzá a következő metódust, amely regisztrálja az üzenetkezelőt, és fogadja a küldő alkalmazás által küldött üzeneteket:

    ```csharp
    static void RegisterOnMessageHandlerAndReceiveMessages()
    {
        // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
        var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
        {
            // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
            // Set it according to how many messages the application wants to process in parallel.
            MaxConcurrentCalls = 1,

            // Indicates whether the message pump should automatically complete the messages after returning from user callback.
            // False below indicates the complete operation is handled by the user callback as in ProcessMessagesAsync().
            AutoComplete = false
        };

        // Register the function that processes messages.
        queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
    }
    ```

1. Közvetlenül az előző metódus után adja hozzá a következő `ProcessMessagesAsync()` metódust a fogadott üzenetek feldolgozásához:

    ```csharp
    static async Task ProcessMessagesAsync(Message message, CancellationToken token)
    {
        // Process the message.
        Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

        // Complete the message so that it is not received again.
        // This can be done only if the queue Client is created in ReceiveMode.PeekLock mode (which is the default).
        await queueClient.CompleteAsync(message.SystemProperties.LockToken);

        // Note: Use the cancellationToken passed as necessary to determine if the queueClient has already been closed.
        // If queueClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
        // to avoid unnecessary exceptions.
    }
    ```

1. Végül adja hozzá a következő metódust az esetlegesen előforduló kivételek kezeléséhez:

    ```csharp
    // Use this handler to examine the exceptions received on the message pump.
    static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
    {
        Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
        var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
        Console.WriteLine("Exception context for troubleshooting:");
        Console.WriteLine($"- Endpoint: {context.Endpoint}");
        Console.WriteLine($"- Entity Path: {context.EntityPath}");
        Console.WriteLine($"- Executing Action: {context.Action}");
        return Task.CompletedTask;
    }
    ```

A *program.cs* -fájlnak így kell kinéznie:

```csharp
namespace CoreReceiverApp
{
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;

    class Program
    {
        // Connection String for the namespace can be obtained from the Azure portal under the 
        // 'Shared Access policies' section.
        const string ServiceBusConnectionString = "<your_connection_string>";
        const string QueueName = "<your_queue_name>";
        static IQueueClient queueClient;

        static void Main(string[] args)
        {
            MainAsync().GetAwaiter().GetResult();
        }

        static async Task MainAsync()
        {
            queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

            Console.WriteLine("======================================================");
            Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
            Console.WriteLine("======================================================");

            // Register QueueClient's MessageHandler and receive messages in a loop
            RegisterOnMessageHandlerAndReceiveMessages();
 
            Console.ReadKey();

            await queueClient.CloseAsync();
        }

        static void RegisterOnMessageHandlerAndReceiveMessages()
        {
            // Configure the MessageHandler Options in terms of exception handling, number of concurrent messages to deliver etc.
            var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
            {
                // Maximum number of Concurrent calls to the callback `ProcessMessagesAsync`, set to 1 for simplicity.
                // Set it according to how many messages the application wants to process in parallel.
                MaxConcurrentCalls = 1,

                // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
                // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
                AutoComplete = false
            };

            // Register the function that will process messages
            queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
        }

        static async Task ProcessMessagesAsync(Message message, CancellationToken token)
        {
            // Process the message
            Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

            // Complete the message so that it is not received again.
            // This can be done only if the queueClient is created in ReceiveMode.PeekLock mode (which is default).
            await queueClient.CompleteAsync(message.SystemProperties.LockToken);

            // Note: Use the cancellationToken passed as necessary to determine if the queueClient has already been closed.
            // If queueClient has already been Closed, you may chose to not call CompleteAsync() or AbandonAsync() etc. calls 
            // to avoid unnecessary exceptions.
        }

        static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
        {
            Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
            var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
            Console.WriteLine("Exception context for troubleshooting:");
            Console.WriteLine($"- Endpoint: {context.Endpoint}");
            Console.WriteLine($"- Entity Path: {context.EntityPath}");
            Console.WriteLine($"- Executing Action: {context.Action}");
            return Task.CompletedTask;
        }
    }
}
```

Futtassa a programot, majd ellenőrizze ismét a portálon. Az **aktív üzenetek száma** és a **jelenlegi** értékek mostantól **0**.

![Üzenetsor üzenetek fogadása után][queue-message-receive]

Gratulálunk! Létrehozta a várólistát, elküldött egy üzenetet a várólistára, és megkapta az üzeneteket ugyanabból a várólistából.

> [!NOTE]
> [Service Bus Explorerrel](https://github.com/paolosalvatori/ServiceBusExplorer/)kezelheti Service Bus erőforrásait. A Service Bus Explorer lehetővé teszi, hogy a felhasználók könnyedén kapcsolódjanak egy Service Bus névtérhez, és felügyelje az üzenetkezelési entitásokat. Az eszköz olyan speciális funkciókat biztosít, mint az importálási/exportálási funkció, illetve a témakörök, várólisták, előfizetések, továbbító szolgáltatások, értesítési központok és Event hubok tesztelésének lehetősége.

## <a name="next-steps"></a>Következő lépések

Tekintse meg a [GitHub-tárunkat, ahol további példákat talál](https://github.com/Azure/azure-service-bus/tree/master/samples), amelyek a Service Bus üzenetkezelési szolgáltatásának néhány speciális funkcióját mutatják be.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues-legacy/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues-legacy/messages-sent-to-essentials.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues-legacy/queue-message-receive-in-essentials.png

