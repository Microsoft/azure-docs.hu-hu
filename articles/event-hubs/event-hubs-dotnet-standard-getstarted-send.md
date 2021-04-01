---
title: Események küldése vagy fogadása az Azure Event Hubs .NET használatával (legújabb)
description: Ez a cikk egy olyan .NET Core-alkalmazás létrehozását ismerteti, amely az Azure-Event Hubs a legújabb Azure. Messaging. EventHubs csomag használatával küld/fogad eseményeket.
ms.topic: quickstart
ms.date: 09/25/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 36551a52bc095a37aa5176ab945dd5d27c640954
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97504610"
---
# <a name="send-events-to-and-receive-events-from-azure-event-hubs---net-azuremessagingeventhubs"></a>Események küldése és fogadása az Azure Event Hubs-.NET-ről (Azure. Messaging. EventHubs) 
Ez a rövid útmutató bemutatja, hogyan lehet eseményeket küldeni és fogadni az Event hub eseményeiről az **Azure. Messaging. EventHubs** .net-kódtár használatával. 

> [!IMPORTANT]
> Ez a rövid útmutató az új **Azure. Messaging. EventHubs** könyvtárat használja. A régi **Microsoft. Azure. EventHubs** könyvtárat használó gyors útmutató: [események küldése és fogadása a Microsoft. Azure. EventHubs könyvtár használatával](event-hubs-dotnet-standard-get-started-send-legacy.md). 



## <a name="prerequisites"></a>Előfeltételek
Ha még nem ismeri az Azure Event Hubsét, a rövid útmutató elvégzése előtt tekintse meg a [Event Hubs áttekintése](event-hubs-about.md) című témakört. 

A rövid útmutató elvégzéséhez a következő előfeltételek szükségesek:

- **Microsoft Azure előfizetés**. Az Azure-szolgáltatások, például az Azure Event Hubs használatához előfizetésre van szükség.  Ha még nem rendelkezik Azure-fiókkal, regisztrálhat az [ingyenes próbaverzióra](https://azure.microsoft.com/free/) , vagy a [fiók létrehozásakor](https://azure.microsoft.com)használhatja az MSDN-előfizetői előnyeit.
- **Microsoft Visual Studio 2019**. Az Azure Event Hubs ügyféloldali kódtár a C# 8,0-ben bevezetett új funkciók használatát teszi lehetővé.  Továbbra is használhatja a könyvtárat a korábbi C# nyelvi verziókkal, de az új szintaxis nem lesz elérhető. A teljes szintaxis használatához javasolt a [.NET Core SDK](https://dotnet.microsoft.com/download) 3,0-as vagy újabb [verzióra](/dotnet/csharp/language-reference/configure-language-version#override-a-default) való fordítás, és a nyelv a következőre van beállítva: `latest` . Ha a Visual studiót használja, a Visual Studio 2019 előtti verziói nem kompatibilisek a C# 8,0-projektek létrehozásához szükséges eszközökkel. A Visual Studio 2019, beleértve az ingyenes Community Edition verziót, [itt](https://visualstudio.microsoft.com/vs/)tölthető le.
- **Hozzon létre egy Event Hubs névteret és egy Event hubot**. Első lépésként a [Azure Portal](https://portal.azure.com) használatával hozzon létre egy Event Hubs típusú névteret, és szerezze be azokat a felügyeleti hitelesítő adatokat, amelyekre az alkalmazásnak szüksége van az Event hub-vel való kommunikációhoz. A névtér és az Event hub létrehozásához kövesse az [ebben a cikkben](event-hubs-create.md)ismertetett eljárást. Ezután szerezze be a **Event Hubs névtérhez tartozó kapcsolatok karakterláncot** a cikk utasításait követve: a [kapcsolatok karakterláncának beolvasása](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). A rövid útmutató későbbi részében használja a kapcsolatok karakterláncát.

## <a name="send-events"></a>Események küldése 
Ebből a szakaszból megtudhatja, hogyan hozhat létre olyan .NET Core Console-alkalmazást, amely eseményeket küld az Event hub-nak. 

### <a name="create-a-console-application"></a>Konzolalkalmazás létrehozása

1. Indítsa el a Visual Studio 2019-et. 
1. Válassza **az új projekt létrehozása** lehetőséget. 
1. A **create a New Project (új projekt létrehozása** ) párbeszédpanelen hajtsa végre a következő lépéseket: Ha nem látja ezt a párbeszédpanelt, válassza a **fájl** lehetőséget a menüben, majd válassza az **új**, majd a **projekt** elemet. 
    1. Válassza a **C#** lehetőséget a programozási nyelvhez.
    1. Válassza ki a **konzolt** az alkalmazás típusához. 
    1. Válassza ki a **Console app (.net Core)** elemet az eredmények listából. 
    1. Ezután válassza a **tovább** lehetőséget. 

        ![A New project (Új projekt) párbeszédpanel](./media/getstarted-dotnet-standard-send-v2/new-send-project.png)    
1. Adja meg a **EventHubsSender** a projekt neve mezőben, a **EventHubsQuickStart** , majd kattintson az **OK** gombra a projekt létrehozásához. 

    ![C# > Console-alkalmazás](./media/getstarted-dotnet-standard-send-v2/project-solution-names.png)

### <a name="add-the-event-hubs-nuget-package"></a>Az Event Hubs NuGet-csomag hozzáadása

1. Válassza ki az **eszközök**  >  **NuGet Package** Manager  >  **csomagkezelő konzolt** a menüből. 
1. Futtassa az alábbi parancsot az **Azure. Messaging. EventHubs** NuGet-csomag telepítéséhez:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```


### <a name="write-code-to-send-messages-to-the-event-hub"></a>Kód írása az üzenetek eseményközpontba való küldésére

1. Adja hozzá a következő `using` utasításokat a **program. cs** fájl elejéhez:

    ```csharp
    using System;
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Producer;
    ```

2. Adjon állandókat a `Program` osztályhoz az Event Hubs kapcsolati karakterlánc és az Event hub neve számára. Cserélje le a zárójelben lévő helyőrzőket az Event hub létrehozásakor kapott megfelelő értékekre. Győződjön meg arról, hogy az `{Event Hubs namespace connection string}` a névtérszintű kapcsolati sztring, és nem az eseményközpont sztringje. 

    ```csharp
    private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
    private const string eventHubName = "<EVENT HUB NAME>";
    ```

3. Cserélje le a `Main` metódust a következő `async Main` metódusra. A részletekért tekintse meg a kód megjegyzéseit. 

    ```csharp
        static async Task Main()
        {
            // Create a producer client that you can use to send events to an event hub
            await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
            {
                // Create a batch of events 
                using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

                // Add events to the batch. An event is a represented by a collection of bytes and metadata. 
                eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First event")));
                eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second event")));
                eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Third event")));

                // Use the producer client to send the batch of events to the event hub
                await producerClient.SendAsync(eventBatch);
                Console.WriteLine("A batch of 3 events has been published.");
            }
        }
    ```
5. Hozza létre a projektet, és ellenőrizze, hogy nincsenek-e hibák.
6. Futtassa a programot, és várjon a megerősítő üzenetre. 
7. A Azure Portalon ellenőrizheti, hogy az Event hub fogadta-e az üzeneteket. Váltson az **üzenetek** nézetre a **metrikák** szakaszban. Frissítse a lapot a diagram frissítéséhez. Eltarthat néhány másodpercig, amíg meg nem jelenik az üzenetek fogadása. 

    [![Annak ellenőrzése, hogy az Event hub fogadta-e az üzeneteket](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > A teljes forráskódot további tájékoztató megjegyzésekkel tekintheti [meg a githubon](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md) .

## <a name="receive-events"></a>Események fogadása
Ez a szakasz bemutatja, hogyan írhat olyan .NET Core Console-alkalmazást, amely az Event hub-ból származó üzeneteket fogad egy eseményvezérelt processzor használatával. Az esemény-feldolgozó leegyszerűsíti az események fogadását az Event hubokból az állandó ellenőrzőpontok és a párhuzamos fogadások az adott esemény hubokból való kezelésével. Egy esemény-feldolgozó társítva van egy adott Event hubhoz és egy fogyasztói csoporthoz. Az Event hub több partíciójának eseményeit fogadja el, amelyek az Ön által megadott kóddal való feldolgozásra szolgálnak. 


> [!WARNING]
> Ha Azure Stack hub-on futtatja ezt a kódot, futásidejű hibákat tapasztal, kivéve, ha egy adott tárolási API-verziót céloz meg. Ennek oka az, hogy az Event Hubs SDK az Azure-ban elérhető legújabb elérhető Azure Storage API-t használja, amely esetleg nem érhető el az Azure Stack hub platformon. Az Azure Stack hub a Storage blob SDK egy másik verzióját is támogatja, mint az Azure-ban általában elérhető. Ha az Azure Blob Storage ellenőrzőpont-tárolóként használja, tekintse meg a [Azure stack hub támogatott Azure Storage API-verzióját](/azure-stack/user/azure-stack-acs-differences?#api-version) , és célozza meg az adott verziót a kódban. 
>
> Ha például Azure Stack hub 2005-es verzióján fut, a Storage szolgáltatás legmagasabb rendelkezésre álló verziója a 2019-02-02-es verzió. Alapértelmezés szerint az Event Hubs SDK ügyféloldali kódtár az Azure legmagasabb rendelkezésre állású verzióját használja (2019-07-07 az SDK kiadásának időpontjában). Ebben az esetben a szakasz következő lépésein kívül kódot is hozzá kell adnia a Storage szolgáltatás API 2019-02-02-es verziójának célzásához. Az adott tárolási API-verzió célzásának példáját a [githubon](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/)tekintheti meg. 
 

### <a name="create-an-azure-storage-and-a-blob-container"></a>Azure Storage és blob-tároló létrehozása
Ebben a rövid útmutatóban az Azure Storage-t használja ellenőrzőpont-tárolóként. Egy Azure Storage-fiók létrehozásához kövesse az alábbi lépéseket. 

1. [Azure Storage-fiók létrehozása](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Blobtároló létrehozása](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [A Storage-fiókhoz tartozó kapcsolódási karakterlánc lekérése](../storage/common/storage-configure-connection-string.md)

    Jegyezze fel a kapcsolatok sztringjét és a tároló nevét. Ezeket a fogadási kódban fogja használni. 


### <a name="create-a-project-for-the-receiver"></a>Projekt létrehozása a fogadó számára

1. A Megoldáskezelő ablakban kattintson a jobb gombbal a **EventHubQuickStart** -megoldásra, mutasson a **Hozzáadás** elemre, majd válassza az **új projekt** lehetőséget. 
1. Válassza ki a **Console app (.net Core)** elemet, majd kattintson a **Tovább gombra**. 
1. Adja meg a **EventHubsReceiver** a **projekt neveként**, majd válassza a **Létrehozás** lehetőséget. 

### <a name="add-the-event-hubs-nuget-package"></a>Az Event Hubs NuGet-csomag hozzáadása

1. Válassza ki az **eszközök**  >  **NuGet Package** Manager  >  **csomagkezelő konzolt** a menüből. 
1. Futtassa az alábbi parancsot az **Azure. Messaging. EventHubs** NuGet-csomag telepítéséhez:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
1. Futtassa az alábbi parancsot az **Azure. Messaging. EventHubs. Processor** NuGet csomag telepítéséhez:

    ```cmd
    Install-Package Azure.Messaging.EventHubs.Processor
    ```    

### <a name="update-the-main-method"></a>A Main metódus frissítése 

1. Adja hozzá a következő `using` utasításokat a **program. cs** fájl elejéhez.

    ```csharp
    using System;
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Storage.Blobs;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Consumer;
    using Azure.Messaging.EventHubs.Processor;
    ```
1. Adjon állandókat a `Program` osztályhoz az Event Hubs kapcsolati karakterlánc és az Event hub neve számára. Cserélje le a zárójelben lévő helyőrzőket az Event hub létrehozásakor kapott megfelelő értékekre. Cserélje le a zárójelben lévő helyőrzőket az Event hub és a Storage-fiók létrehozásakor kapott megfelelő értékekre (hozzáférési kulcsok – elsődleges kapcsolati sztring). Győződjön meg arról, hogy az `{Event Hubs namespace connection string}` a névtérszintű kapcsolati sztring, és nem az eseményközpont sztringje.

    ```csharp
        private const string ehubNamespaceConnectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
        private const string eventHubName = "<EVENT HUB NAME>";
        private const string blobStorageConnectionString = "<AZURE STORAGE CONNECTION STRING>";
        private const string blobContainerName = "<BLOB CONTAINER NAME>";
    ```
3. Cserélje le a `Main` metódust a következő `async Main` metódusra. A részletekért tekintse meg a kód megjegyzéseit. 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;

            // Create a blob container client that the event processor will use 
            BlobContainerClient storageClient = new BlobContainerClient(blobStorageConnectionString, blobContainerName);

            // Create an event processor client to process events in the event hub
            EventProcessorClient processor = new EventProcessorClient(storageClient, consumerGroup, ehubNamespaceConnectionString, eventHubName);

            // Register handlers for processing events and handling errors
            processor.ProcessEventAsync += ProcessEventHandler;
            processor.ProcessErrorAsync += ProcessErrorHandler;

            // Start the processing
            await processor.StartProcessingAsync();

            // Wait for 10 seconds for the events to be processed
            await Task.Delay(TimeSpan.FromSeconds(10));

            // Stop the processing
            await processor.StopProcessingAsync();
        }    
    ```
1. Most adja hozzá a következő eseményt és hibakezelő metódusokat a osztályhoz. 

    ```csharp
        static async Task ProcessEventHandler(ProcessEventArgs eventArgs)
        {
            // Write the body of the event to the console window
            Console.WriteLine("\tReceived event: {0}", Encoding.UTF8.GetString(eventArgs.Data.Body.ToArray()));

            // Update checkpoint in the blob storage so that the app receives only new events the next time it's run
            await eventArgs.UpdateCheckpointAsync(eventArgs.CancellationToken);
        }

        static Task ProcessErrorHandler(ProcessErrorEventArgs eventArgs)
        {
            // Write details about the error to the console window
            Console.WriteLine($"\tPartition '{ eventArgs.PartitionId}': an unhandled exception was encountered. This was not expected to happen.");
            Console.WriteLine(eventArgs.Exception.Message);
            return Task.CompletedTask;
        }    
    ```
1. Hozza létre a projektet, és ellenőrizze, hogy nincsenek-e hibák.

    > [!NOTE]
    > A teljes forráskódot további tájékoztató megjegyzésekkel tekintheti [meg a githubon](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample01_HelloWorld.md).
6. Futtassa a fogadó alkalmazást. 
1. Ekkor megjelenik egy üzenet, amely szerint az esemény érkezett. 

    ![Esemény érkezett](./media/getstarted-dotnet-standard-send-v2/event-received.png)

    Ezek az események a küldő program futtatásával korábban a következő három eseményt küldik el az Event hub számára. 


## <a name="next-steps"></a>Következő lépések
Tekintse meg a mintákat a GitHubon. 

- [Event Hubs minták a GitHubon](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [Event Processor-minták a GitHubon](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples)
- [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) minta](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
