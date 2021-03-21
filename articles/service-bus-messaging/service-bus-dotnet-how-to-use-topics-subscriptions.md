---
title: Üzenetek küldése Azure Service Bus témakörökbe az Azure-Messaging-servicebus használatával
description: Ez a rövid útmutató bemutatja, hogyan küldhet üzeneteket Azure Service Bus témakörökbe az Azure-Messaging-servicebus csomag használatával.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 03/16/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 7b313caf6709429de9e0dcac219a4180c7391cf7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104607584"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-net"></a>Üzenetek küldése egy Azure Service Bus témakörnek, és üzenetek fogadása az előfizetésből a témakörbe (.NET)
Ebben az oktatóanyagban egy C#-alkalmazást hoz létre a következő feladatok elvégzéséhez:

1. Üzenetek küldése egy Service Bus témakörnek. 

    A Service Bus témakör végpontot biztosít a küldő alkalmazások számára az üzenetek küldéséhez. Egy témakörhöz egy vagy több előfizetés is tartozhat. A témakör minden előfizetése a témakörbe küldött üzenet egy másolatát kapja meg. Service Bus témakörökkel kapcsolatos további információkért lásd: [Mi az Azure Service Bus?](service-bus-messaging-overview.md) 
1. Üzenetek fogadása a témakör előfizetéséről. 

    :::image type="content" source="./media/service-bus-messaging-overview/about-service-bus-topic.png" alt-text="Service Bus-témakörök és -előfizetések":::

    > [!Important]
    > Ez a rövid útmutató az új **Azure. Messaging. ServiceBus** csomagot használja. Ha a régi Microsoft. Azure. ServiceBus csomagot használja, tekintse meg [az üzenetek küldése és fogadása a Microsoft. Azure. ServiceBus csomag használatával](service-bus-dotnet-how-to-use-topics-subscriptions-legacy.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja [Visual Studio-vagy MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) , vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- [Az ebben a](service-bus-quickstart-topics-subscriptions-portal.md) rövid útmutatóban ismertetett lépéseket követve létrehozhat egy Service Bus témakört és előfizetéseket a témakörbe. 

    > [!NOTE]
    > A kapcsolódási karakterláncot a névtérhez, a témakör nevéhez, valamint az oktatóanyagban szereplő témakörhöz tartozó egyik előfizetés nevéhez fogja használni.  
- [Visual Studio 2019](https://www.visualstudio.com/vs). 
 
## <a name="send-messages-to-a-topic"></a>Üzenetek küldése egy üzenettémakörbe
Ebben a szakaszban létrehoz egy .NET Core Console-alkalmazást a Visual Studióban, kód hozzáadásával üzeneteket küld a létrehozott Service Bus témakörnek. 

### <a name="create-a-console-application"></a>Konzolalkalmazás létrehozása
Hozzon létre egy .NET Core Console-alkalmazást a Visual Studióval. 

1. Indítsa el a Visual studiót.  
1. Ha megjelenik az **első lépések** lap, válassza az **új projekt létrehozása** lehetőséget. 
1. Az **új projekt létrehozása** lapon kövesse az alábbi lépéseket: 
    1. A programozási nyelv esetében válassza a **C#** elemet. 
    1. A projekt típusa beállításnál válassza a **konzol** lehetőséget. 
    1. Válassza ki a **konzol alkalmazás (.net Core)** elemet a sablonok listájában. 
    1. Ezután válassza a **tovább** lehetőséget. 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-console-project.png" alt-text="Konzolos alkalmazás projekt létrehozása":::
1. Az **új projekt konfigurálása** lapon hajtsa végre az alábbi lépéseket: 
    1. A **Project Name (projekt neve**) mezőben adja meg a projekt nevét. 
    1. A **hely** mezőben válassza ki a projekt és a megoldás fájljainak helyét. 
    1. A **megoldás neve** mezőben adja meg a megoldás nevét. Egy Visual Studio-megoldáshoz egy vagy több projekt is tartozhat. Ebben a rövid útmutatóban a megoldásnak csak egy projektje lesz. 
    1. A projekt létrehozásához válassza a **Létrehozás** lehetőséget. 
            
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-console-project-2.png" alt-text="Adja meg a projekt és a megoldás nevét és helyét":::    


### <a name="add-the-service-bus-nuget-package"></a>A Service Bus NuGet-csomag hozzáadása
1. Kattintson a jobb gombbal az újonnan létrehozott projektre, és válassza a **Manage Nuget Packages** (NuGet-csomagok kezelése) lehetőséget.

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/manage-nuget-packages-menu.png" alt-text="NuGet-csomagok kezelése menü":::        
1. Váltson a **Tallózás** lapra.
1. Keresse meg és válassza ki az **[Azure. Messaging. ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/)** elemet.
1. Kattintson a **Telepítés** gombra a telepítés befejezéséhez.

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-service-bus-package.png" alt-text="Válassza ki Service Bus NuGet csomagot.":::
5. Ha megjelenik a **módosítások előnézete** párbeszédpanel, kattintson az **OK gombra** a folytatáshoz. 
1. Ha megjelenik a **licenc elfogadását** kérő oldal, válassza az **Elfogadom** lehetőséget a folytatáshoz. 
    

### <a name="add-code-to-send-messages-to-the-topic"></a>Kód hozzáadása az üzenetek a témakörbe való küldéséhez 

1. A **megoldáskezelő** ablakban kattintson duplán a **program. cs** fájlra, és nyissa meg a fájlt a szerkesztőben. 
1. Adja hozzá a következő `using` utasításokat a névtér definíciójának elejéhez az osztály deklarációja előtt:
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    ```
1. A `Program` függvény fölötti osztályban `Main` deklarálja a következő változókat:

    ```csharp
        static string connectionString = "<NAMESPACE CONNECTION STRING>";
        static string topicName = "<SERVICE BUS TOPIC NAME>";
        static string subscriptionName = "<SERVICE BUS - TOPIC SUBSCRIPTION NAME>";
    ```

    Cserélje le a következő értékeket:
    - `<NAMESPACE CONNECTION STRING>` a Service Bus névtérhez tartozó kapcsolódási karakterlánccal
    - `<TOPIC NAME>` a témakör nevével
    - `<SUBSCRIPTION NAME>` az előfizetés nevével

### <a name="send-a-single-message-to-the-topic"></a>Egyetlen üzenet küldése a témakörnek
Adjon hozzá egy nevű metódust `SendMessageToTopicAsync` a `Program` metódus alatti vagy alatti osztályhoz `Main` . Ez a metódus egyetlen üzenetet küld a témakörnek.

```csharp
    static async Task SendMessageToTopicAsync()
    {
        // create a Service Bus client 
        await using (ServiceBusClient client = new ServiceBusClient(connectionString))
        {
            // create a sender for the topic
            ServiceBusSender sender = client.CreateSender(topicName);
            await sender.SendMessageAsync(new ServiceBusMessage("Hello, World!"));
            Console.WriteLine($"Sent a single message to the topic: {topicName}");
        }
    }
```

Ez a módszer a következő lépéseket hajtja végre: 
1. Létrehoz egy [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) objektumot a névtérhez tartozó kapcsolódási karakterlánc használatával. 
1. Az `ServiceBusClient` objektum használatával hoz létre egy [ServiceBusSender](/dotnet/api/azure.messaging.servicebus.servicebussender) -objektumot a megadott Service Bus témakörhöz. Ez a lépés a [ServiceBusClient. CreateSender](/dotnet/api/azure.messaging.servicebus.servicebusclient.createsender) metódust használja.
1. Ezt követően a metódus egyetlen tesztüzenet küldését küldi el a Service Bus témakörnek az [ServiceBusSender. SendMessageAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.sendmessageasync) metódus használatával. 

### <a name="send-a-batch-of-messages-to-the-topic"></a>Üzenetek kötegének elküldése a témakörbe
1. Adjon hozzá egy nevű metódust `CreateMessages` egy üzenetsor létrehozásához (.net-várólista, nem Service Bus üzenetsor) az `Program` osztályhoz. Ezeket az üzeneteket általában az alkalmazás különböző részeiről szerezheti be. Itt hozzuk létre a mintaadatok várólistáját. Ha nem ismeri a .NET-várólistákat, tekintse meg a [Queue. sorba helyezni](/dotnet/api/system.collections.queue.enqueue)című témakört.

    ```csharp
        static Queue<ServiceBusMessage> CreateMessages()
        {
            // create a queue containing the messages and return it to the caller
            Queue<ServiceBusMessage> messages = new Queue<ServiceBusMessage>();
            messages.Enqueue(new ServiceBusMessage("First message"));
            messages.Enqueue(new ServiceBusMessage("Second message"));
            messages.Enqueue(new ServiceBusMessage("Third message"));
            return messages;
        }
    ```
1. Adjon hozzá egy osztály nevű metódust `SendMessageBatchAsync` `Program` , és adja hozzá a következő kódot. Ez a metódus az üzenetek várólistáját veszi fel, és előkészíti egy vagy több köteg küldését az Service Bus témakörbe. 

    ```csharp
        static async Task SendMessageBatchToTopicAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {

                // create a sender for the topic 
                ServiceBusSender sender = client.CreateSender(topicName);

                // get the messages to be sent to the Service Bus topic
                Queue<ServiceBusMessage> messages = CreateMessages();

                // total number of messages to be sent to the Service Bus topic
                int messageCount = messages.Count;

                // while all messages are not sent to the Service Bus topic
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

                Console.WriteLine($"Sent a batch of {messageCount} messages to the topic: {topicName}");
            }
        }
    ```    

    A kód fontos lépései:
    1. Létrehoz egy [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) objektumot a névtérhez tartozó kapcsolódási karakterlánc használatával. 
    1. Meghívja a [CreateSender](/dotnet/api/azure.messaging.servicebus.servicebusclient.createsender) metódust az `ServiceBusClient` objektumon, hogy [ServiceBusSender](/dotnet/api/azure.messaging.servicebus.servicebussender) objektumot hozzon létre a megadott Service Bus témakörben. 
    1. Meghívja a segítő metódust az `GetMessages` Service Bus témakörbe küldendő üzenetek várólistájának lekéréséhez. 
    1. Létrehoz egy [ServiceBusMessageBatch](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch) a [ServiceBusSender. CreateMessageBatchAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.createmessagebatchasync)használatával.
    1. Üzenetek hozzáadása a köteghez a [ServiceBusMessageBatch. TryAddMessage](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch.tryaddmessage)használatával. Ahogy az üzenetek hozzáadódnak a köteghez, el lesznek távolítva a .NET-sorból. 
    1. Az üzenetek kötegét elküldi a Service Bus témakörnek az [ServiceBusSender. SendMessagesAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.sendmessagesasync) metódussal.

### <a name="update-the-main-method"></a>A Main metódus frissítése
Cserélje le a `Main()` metódust a következő **aszinkron** `Main` metódusra. Meghívja a küldési metódusokat is, hogy egyetlen üzenetet és egy köteget küldjön a témakörnek.  

```csharp
    static async Task Main()
    {
        // send a single message to the topic
        await SendMessageToTopicAsync();

        // send a batch of messages to the topic
        await SendMessageBatchToTopicAsync();
    }
```

### <a name="test-the-app-to-send-messages-to-the-topic"></a>Az alkalmazás tesztelése az üzenetek küldéséhez a témakörbe
1. Futtassa az alkalmazást. A következő kimenetnek kell megjelennie:

    ```console
    Sent a single message to the topic: mytopic
    Sent a batch of 3 messages to the topic: mytopic
    ```
1. A Azure Portal hajtsa végre az alábbi lépéseket:
    1. Navigáljon a Service Bus névtérhez. 
    1. Az **Áttekintés** lap alsó középső paneljén váltson a **témakörök** lapra, és válassza ki a Service Bus témakört. Az alábbi példában ez a következő: `mytopic` .
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-topic.png" alt-text="Témakör kiválasztása":::
    1. A **Service Bus témakör** lap alsó **mérőszámok** szakaszában található **üzenetek** diagramon láthatja, hogy négy bejövő üzenet van a témakörhöz. Ha nem látja az értéket, várjon néhány percet, és frissítse az oldalt a frissített diagram megjelenítéséhez. 

        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png" alt-text="A témakörbe küldött üzenetek" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png":::
    4. Válassza ki az előfizetést az alsó ablaktáblán. A következő példában ez az **S1**. Az **Service Bus-előfizetés** lapon az **aktív üzenetek száma** **4**. Az előfizetés a témakörbe küldött négy üzenetet fogadta, de még nem vette át a fogadót. 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png" alt-text="Az előfizetésben fogadott üzenetek" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png":::
    

## <a name="receive-messages-from-a-subscription"></a>Üzenetek fogadása egy előfizetésből

1. Adja hozzá a következő metódusokat a `Program` osztályhoz, amely kezeli az üzeneteket és az esetleges hibákat. 

    ```csharp
        static async Task MessageHandler(ProcessMessageEventArgs args)
        {
            string body = args.Message.Body.ToString();
            Console.WriteLine($"Received: {body} from subscription: {subscriptionName}");

            // complete the message. messages is deleted from the queue. 
            await args.CompleteMessageAsync(args.Message);
        }

        static Task ErrorHandler(ProcessErrorEventArgs args)
        {
            Console.WriteLine(args.Exception.ToString());
            return Task.CompletedTask;
        }
    ```
1. Adja hozzá a következő metódust `ReceiveMessagesFromSubscriptionAsync` a `Program` osztályhoz.

    ```csharp
        static async Task ReceiveMessagesFromSubscriptionAsync()
        {
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a processor that we can use to process the messages
                ServiceBusProcessor processor = client.CreateProcessor(topicName, subscriptionName, new ServiceBusProcessorOptions());

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

    A kód fontos lépései:
    1. Létrehoz egy [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) objektumot a névtérhez tartozó kapcsolódási karakterlánc használatával. 
    1. Meghívja a [CreateProcessor](/dotnet/api/azure.messaging.servicebus.servicebusclient.createprocessor) metódust az `ServiceBusClient` objektumon, hogy [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) objektumot hozzon létre a megadott Service Bus témakörhöz és az előfizetés-kombinációhoz. 
    1. Meghatározza az objektum [ProcessMessageAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync) és [ProcessErrorAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processerrorasync) eseményeinek kezelőit `ServiceBusProcessor` . 
    1. Megkezdi az üzenetek feldolgozását az objektum [StartProcessingAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.startprocessingasync) meghívásával `ServiceBusProcessor` . 
    1. Amikor a felhasználó megnyom egy billentyűt a feldolgozás befejezéséhez, meghívja a [StopProcessingAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.stopprocessingasync) az `ServiceBusProcessor` objektumon. 
1. Adjon hozzá egy hívást a metódushoz `ReceiveMessagesFromSubscriptionAsync` a `Main` metódushoz. `SendMessagesToTopicAsync`Ha csak az üzenetek fogadását szeretné tesztelni, tegye megjegyzésbe a metódust. Ha nem, akkor a témakörbe további négy üzenetet is láthat. 

    ```csharp
        static async Task Main()
        {
            // send a message to the topic
            await SendMessageToTopicAsync();

            // send a batch of messages to the topic
            await SendMessageBatchToTopicAsync();

            // receive messages from the subscription
            await ReceiveMessagesFromSubscriptionAsync();
        }
    ```
## <a name="run-the-app"></a>Az alkalmazás futtatása
Futtassa az alkalmazást. Várjon egy percet, majd nyomja le az egyik billentyűt az üzenetek fogadásának leállításához. A következő kimenetnek kell megjelennie (szóköz a kulcshoz). 

```console
Sent a single message to the topic: mytopic
Sent a batch of 3 messages to the topic: mytopic
Wait for a minute and then press any key to end the processing
Received: Hello, World! from subscription: mysub
Received: First message from subscription: mysub
Received: Second message from subscription: mysub
Received: Third message from subscription: mysub
Received: Hello, World! from subscription: mysub
Received: First message from subscription: mysub
Received: Second message from subscription: mysub
Received: Third message from subscription: mysub

Stopping the receiver...
Stopped receiving messages
```

Próbálkozzon újra a portálon. 

- A **Service Bus témakör** lap **üzenetek** diagramján nyolc bejövő üzenet és nyolc kimenő üzenet jelenik meg. Ha nem látja ezeket a számokat, várjon néhány percet, és frissítse a lapot a frissített diagram megjelenítéséhez. 

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png" alt-text="Küldött és fogadott üzenetek" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png":::
- Az **Service Bus-előfizetés** lapon az **aktív üzenetek száma** nullaként jelenik meg. Ez azért van, mert a fogadó üzenetet kapott az előfizetésből, és befejezte az üzeneteket. 

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png" alt-text="Az aktív üzenetek száma az előfizetés végén" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png":::
    


## <a name="next-steps"></a>Következő lépések
Tekintse meg a következő dokumentációt és mintákat:

- [A .NET-hez készült ügyféloldali kódtár Azure Service Bus – readme](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [.NET-minták Azure Service Bus a GitHubon](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [.NET API-referencia](/dotnet/api/azure.messaging.servicebus?preserve-view=true)