---
title: Bevezetés az Azure Service Bus által kezelt üzenetsorok használatába | Microsoft Docs
description: Ebben az oktatóanyagban .NET Core-konzolalkalmazásokat hoz létre, amelyek üzeneteket küldenek egy üzenetsorba, és üzeneteket Service Bus az üzenetsorból.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 09/01/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: d92a5e0b0d1a6953d0043f85ff98f6740aaacebb
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864013"
---
# <a name="get-started-with-service-bus-queues"></a>Bevezetés a Service Bus által kezelt üzenetsorok használatába
Ebben az oktatóanyagban .NET Core-konzolalkalmazásokat hoz létre, amelyek üzeneteket küldenek egy üzenetsorba, és üzeneteket Service Bus az üzenetsorból.

> [!WARNING]
> Ez a rövid útmutató a régi Microsoft.Azure.ServiceBus csomagot használja. A legújabb Azure.Messaging.ServiceBus csomagot használó rövid útmutatóért lásd: Események küldése és fogadása [az Azure.Messaging.ServiceBus csomag használatával.](service-bus-dotnet-get-started-with-queues.md) 

## <a name="prerequisites"></a>Előfeltételek

- [Visual Studio 2019. július.](https://www.visualstudio.com/vs)
- [NET Core SDK](https://dotnet.microsoft.com/download), 2.0-s vagy újabb verzió.
- Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja [MSDN-előfizetői](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) előnyeit, vagy regisztrálhat egy [ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Ha nincs üzenetsora, kövesse az Üzenetsor létrehozása Azure Portal üzenetsor létrehozásához [Service Bus](service-bus-quickstart-portal.md) cikk lépéseit.

  - Olvassa el az üzenetsorok Service Bus áttekintését.
  - Hozzon létre Service Bus névteret.
  - Szerezze be a kapcsolati sztringet.
  - Hozzon létre egy Service Bus üzenetsort.

## <a name="send-messages"></a>Üzenetek küldése

A Visual Studio használatával írjon C# konzolalkalmazást az üzenetek üzenetsorba való küldéséhez.

### <a name="create-a-console-application"></a>Konzolalkalmazás létrehozása

Indítsa Visual Studio, és hozzon létre egy új **Console App (.NET Core)** projektet a C# programhoz. Ebben a példában a *CoreSenderApp nevet adhatja az alkalmazásnak.*

### <a name="add-the-service-bus-nuget-package"></a>A Service Bus NuGet-csomag hozzáadása

1. Kattintson a jobb gombbal az újonnan létrehozott projektre, és válassza a **Manage Nuget Packages** (NuGet-csomagok kezelése) lehetőséget.
1. Válassza a **Tallózás** lehetőséget. Keresse meg és válassza ki a **[Microsoft.Azure.ServiceBus szolgáltatásokat.](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)**
1. A **telepítés befejezéséhez** válassza a Telepítés lehetőséget, majd zárja be a NuGet-Csomagkezelő.

    ![NuGet-csomag kiválasztása][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-queue"></a>Kód írása az üzenetek üzenetsorba való küldéséhez

1. A *Program.cs fájlban* adja hozzá a következő utasításokat a névtér definíciójának tetejéhez, `using` az osztálydeklaráció előtt:

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

    Adja meg a névtér kapcsolati sztringját `ServiceBusConnectionString` változóként. Adja meg az üzenetsor nevét.

1. Cserélje le `Main()` a metódust a következő **async** `Main` metódusra. Ez a metódust hívja meg, amely a következő lépésben hozzáadva `SendMessagesAsync()` üzeneteket küld az üzenetsorba. 

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
1. Közvetlenül a metódus után adja hozzá a következő metódust, amely a által megadott számú (jelenleg `MainAsync()` `SendMessagesAsync()` 10-re beállított) üzenet küldését `numberOfMessagesToSend` teszi:

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

A *Program.cs fájlnak* így kell kinéznie.

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

Futtassa a programot, és ellenőrizze a Azure Portal.

Válassza ki az üzenetsor nevét a névtér **Áttekintés ablakában** az **üzenetsor Alapvető szolgáltatások megjelenítéséhez.**

![Fogadott üzenetek darabszámmal és mérettel][queue-message]

Az **üzenetsor Aktív üzenetek** száma értéke mostantól **10.** Minden alkalommal, amikor az üzenetek leolvasása nélkül futtatja ezt a küldőalkalmazást, ez az érték 10-sel növekszik.

Az üzenetsor aktuális mérete minden alkalommal megnöveli a **CURRENT** értéket az **Alapvető**  szolgáltatásokban, amikor az alkalmazás üzeneteket ad hozzá az üzenetsorhoz.

A következő szakasz ezeknek az üzeneteknek a lekérését ismerteti.

## <a name="receive-messages"></a>Üzenetek fogadása

Az elküldött üzenetek fogadásához hozzon létre egy másik **Konzolalkalmazás (.NET Core)** alkalmazást. Telepítse a **Microsoft.Azure.ServiceBus** NuGet-csomagot a küldőalkalmazáshoz is.

### <a name="write-code-to-receive-messages-from-the-queue"></a>Kód írása az üzenetek üzenetsorból történő fogadásához

1. A *Program.cs fájlban* adja hozzá a következő utasításokat a névtér definíciójának tetejéhez, `using` az osztálydeklaráció előtt:

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

    Változóként adja meg a névtér kapcsolati `ServiceBusConnectionString` sztringét. Adja meg az üzenetsor nevét.

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

1. Közvetlenül a metódus után adja hozzá a következő metódust, amely regisztrálja az üzenetkezelőt, és fogadja a `MainAsync()` küldőalkalmazás által küldött üzeneteket:

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

A *Program.cs fájlnak* így kell kinéznie:

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

Futtassa a programot, majd ellenőrizze ismét a portálon. Az **Aktív üzenetek száma és** a **CURRENT** értékek mostantól **0.**

![Üzenetsor az üzenetek fogadása után][queue-message-receive]

Gratulálunk! Ezzel létrehozott egy üzenetsort, elküldött egy üzenetkészletet az üzenetsorba, és fogadott üzeneteket ugyanattól az üzenetsortól.

> [!NOTE]
> Az erőforrás-Service Bus a [Service Bus Explorer.](https://github.com/paolosalvatori/ServiceBusExplorer/) A Service Bus Explorer lehetővé teszi a felhasználók számára, hogy könnyedén csatlakozznak egy Service Bus névtérhez, és felügyelni az üzenetküldési entitásokat. Az eszköz fejlett funkciókat biztosít, például az importálási/exportálási funkciókat, illetve a témakörök, üzenetsorok, előfizetések, továbbítási szolgáltatások, értesítési központok és eseményközpontok tesztelésének képességét.

## <a name="next-steps"></a>Következő lépések

Tekintse meg a [GitHub-tárunkat, ahol további példákat talál](https://github.com/Azure/azure-service-bus/tree/master/samples), amelyek a Service Bus üzenetkezelési szolgáltatásának néhány speciális funkcióját mutatják be.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues-legacy/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues-legacy/messages-sent-to-essentials.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues-legacy/queue-message-receive-in-essentials.png

