---
title: Események küldése vagy fogadása Azure Event Hubs .NET használatával (legújabb)
description: Ez a cikk egy .NET Core-alkalmazás létrehozásához nyújt útmutatót, amely a legújabb Azure.Messaging.EventHubs-csomaggal küld/fogad eseményeket a Azure Event Hubs-nek.
ms.topic: quickstart
ms.date: 09/25/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 58da331336481614cf0f85bdf6c1136c8bdc8db7
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536493"
---
# <a name="send-events-to-and-receive-events-from-azure-event-hubs---net-azuremessagingeventhubs"></a>Események küldése és fogadása a Azure Event Hubs - .NET (Azure.Messaging.EventHubs) 
Ez a rövid útmutató bemutatja, hogyan küldhet eseményeket egy eseményközpontba, illetve fogadhat eseményeket az **Azure.Messaging.EventHubs .NET-kódtár** használatával. 

> [!IMPORTANT]
> Ez a rövid útmutató az új **Azure.Messaging.EventHubs kódtárat** használja. A régi **Microsoft.Azure.EventHubs** kódtárat használó rövid útmutatóért lásd: Események küldése és fogadása [a Microsoft.Azure.EventHubs kódtár használatával.](event-hubs-dotnet-standard-get-started-send-legacy.md) 



## <a name="prerequisites"></a>Előfeltételek
Ha még csak most kezd el [](event-hubs-about.md) Azure Event Hubs útmutatót, Event Hubs a rövid útmutatót megelőzően tekintse meg az áttekintést. 

A rövid útmutató befejezéséhez a következő előfeltételekre lesz szüksége:

- **Microsoft Azure előfizetést.** Az Azure-szolgáltatások , köztük a Azure Event Hubs is, előfizetésre van szüksége.  Ha még nem rendelkezik Azure-fiókkal, regisztrálhat [](https://azure.microsoft.com/free/) egy ingyenes próbaverzióra, vagy használhatja MSDN-előfizetői előnyeit a [fiók létrehozásakor.](https://azure.microsoft.com)
- **Microsoft Visual Studio 2019**. A Azure Event Hubs ügyféloldali kódtár a C# 8.0-ban bevezetett új funkciókat használja.  Továbbra is használhatja a kódtárat a C# korábbi nyelvi verzióival, de az új szintaxis nem lesz elérhető. A teljes szintaxis használata érdekében ajánlott a 3.0-s vagy újabb .NET Core SDK [](/dotnet/csharp/language-reference/configure-language-version#override-a-default) és a nyelvi verziót a következőre beállítani: [](https://dotnet.microsoft.com/download) `latest` . Ha a 2019 Visual Studio előtti verziókat használja, Visual Studio nem kompatibilisek a C# 8.0-projektek felépítéséhez szükséges eszközökkel. Visual Studio 2019-es kiadását, beleértve az ingyenes Community kiadást, itt [töltheti le.](https://visualstudio.microsoft.com/vs/)
- **Hozzon létre Event Hubs névteret és egy eseményközpontot.** Az első lépés a [Azure Portal](https://portal.azure.com) használata egy Event Hubs típusú névtér létrehozásához, és az alkalmazás és az eseményközpont kommunikációja során szükséges felügyeleti hitelesítő adatok beszerzése. Névtér és eseményközpont létrehozásához kövesse az ebben a cikkben [olvasható eljárást.](event-hubs-create.md) Ezután szerezze be a kapcsolati **sztringet Event Hubs a** következő cikk utasításait követve: [Kapcsolati sztring lekérte.](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) A rövid útmutató későbbi, a kapcsolati sztringet használja.

## <a name="send-events"></a>Események küldése 
Ez a szakasz bemutatja, hogyan hozhat létre egy .NET Core-konzolalkalmazást, amely eseményeket küld egy eseményközpontba. 

### <a name="create-a-console-application"></a>Konzolalkalmazás létrehozása

1. Indítsa el a Visual Studio 2019-et. 
1. Válassza **az Új projekt létrehozása lehetőséget.** 
1. A **Create a new project (Új** projekt létrehozása) párbeszédpanelen tegye a  következőket: Ha nem látja ezt a párbeszédpanelt, válassza a menü File (Fájl) parancsát, válassza a **New**(Új) lehetőséget, majd a **Project (Projekt) lehetőséget.** 
    1. A **programozási nyelvhez** válassza a C# lehetőséget.
    1. Válassza **a Konzol** lehetőséget az alkalmazás típusaként. 
    1. Válassza **a Console App (.NET Core) (Konzolalkalmazás (.NET Core) lehetőséget az** eredménylistából. 
    1. Ezután válassza a **Tovább lehetőséget.** 

        ![A New project (Új projekt) párbeszédpanel](./media/getstarted-dotnet-standard-send-v2/new-send-project.png)    
1. Adja **meg az EventHubsSender** nevet a projekt neveként, **az EventHubsQuickStart** nevet a megoldás neveként, majd kattintson az **OK** gombra a projekt létrehozásához. 

    ![C# > konzolalkalmazás](./media/getstarted-dotnet-standard-send-v2/project-solution-names.png)

### <a name="add-the-event-hubs-nuget-package"></a>Az Event Hubs NuGet-csomag hozzáadása

1. Válassza **a Tools**  >  NuGet Csomagkezelő Csomagkezelő Console (Eszközök **NuGet-konzol)** lehetőséget  >   a menüben. 
1. Futtassa a következő parancsot az **Azure.Messaging.EventHubs** NuGet-csomag telepítéséhez:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```


### <a name="write-code-to-send-messages-to-the-event-hub"></a>Kód írása az üzenetek eseményközpontba való küldésére

1. Adja hozzá a következő `using` utasításokat a **Program.cs fájl tetejéhez:**

    ```csharp
    using System;
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Producer;
    ```

2. Adjon állandókat a osztályhoz a Event Hubs kapcsolati sztring `Program` és az eseményközpont neve számára. Cserélje le a zárójelben lévő helyőrzőket az eseményközpont létrehozásakor kapott megfelelő értékekre. Győződjön meg arról, hogy az `{Event Hubs namespace connection string}` a névtérszintű kapcsolati sztring, és nem az eseményközpont sztringje. 

    ```csharp
    private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
    private const string eventHubName = "<EVENT HUB NAME>";
    ```

3. Cserélje le `Main` a metódust a következő `async Main` metódusra. A részletekért tekintse meg a kód megjegyzéseit. 

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
5. Buildje a projektet, és győződjön meg arról, hogy nincsenek hibák.
6. Futtassa a programot, és várja meg a megerősítő üzenetet. 
7. A Azure Portal ellenőrizheti, hogy az eseményközpont megkapta-e az üzeneteket. Váltson **Az üzenetek** nézetre a **Metrikák szakaszban.** Frissítse az oldalt a diagram frissítéséhez. Az üzenetek fogadásának megjelenítése néhány másodpercet is igénybe vehet. 

    [![Ellenőrizze, hogy az eseményközpont megkapta-e az üzeneteket](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > A további tájékoztató megjegyzéseket is tartalmazó teljes forráskódért tekintse meg [ezt a fájlt a GitHubon](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md)

## <a name="receive-events"></a>Események fogadása
Ez a szakasz bemutatja, hogyan írhat olyan .NET Core-konzolalkalmazást, amely egy eseményfeldolgozóval fogad üzeneteket egy eseményközpontból. Az eseményfeldolgozó leegyszerűsíti az események fogadását az eseményközpontból, mert kezeli az állandó ellenőrzőpontokat és a párhuzamos fogadásokat az eseményközpontból. Az eseményfeldolgozó egy adott eseményközponthoz és egy fogyasztói csoporthoz van társítva. Eseményeket fogad az eseményközpont több partíciójáról, és azokat egy kezelő delegáltnak adja át az Ön által küldött kód használatával történő feldolgozáshoz. 


> [!WARNING]
> Ha ezt a kódot a Azure Stack Hub futtatja, futásidejű hibákat fog tapasztalni, kivéve, ha egy adott Storage API-verziót céloz meg. Ennek az az oka, hogy az Event Hubs SDK az Azure-ban elérhető legújabb elérhető Azure Storage API-t használja, amely nem mindig érhető el a Azure Stack Hub platformon. Azure Stack Hub Storage Blob SDK más verzióját is támogatja, mint az Azure-ban. Ha ellenőrzőpont-Azure Blob Storage használ, ellenőrizze a támogatott Azure Storage API-verziót a Azure Stack Hub [buildhez](/azure-stack/user/azure-stack-acs-differences?#api-version) és célverzióhoz a kódban. 
>
> Ha például a 2005-ös Azure Stack Hub futtatja, a Storage szolgáltatáshoz elérhető legmagasabb verzió a 2019-02-02-es verzió. Alapértelmezés szerint a Event Hubs SDK ügyféloldali kódtára az Azure-ban elérhető legmagasabb verziót használja (2019-07-07 az SDK kiadásakor). Ebben az esetben a szakasz lépései mellett a Storage szolgáltatás API 2019-02-02-es verzióját célzó kódot is hozzá kell adni. Egy adott Storage API-verzió megcélozására vonatkozó példáért tekintse meg ezt a mintát a [GitHubon.](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/) 
 

### <a name="create-an-azure-storage-and-a-blob-container"></a>Azure Storage-tároló és blobtároló létrehozása
Ebben a rövid útmutatóban az Azure Storage-et használja ellenőrzőpont-tárolóként. Kövesse az alábbi lépéseket egy Azure Storage-fiók létrehozásához. 

1. [Azure Storage-fiók létrehozása](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Blobtároló létrehozása](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [A tárfiók kapcsolati sztringének lekérte](../storage/common/storage-configure-connection-string.md)

    Jegyezze fel a kapcsolati sztringet és a tároló nevét. Ezeket a fogadási kódban fogja használni. 


### <a name="create-a-project-for-the-receiver"></a>Projekt létrehozása a fogadó számára

1. A Megoldáskezelő kattintson a jobb gombbal az **EventHubQuickStart** megoldásra, mutasson az **Add**(Hozzáadás) parancsra, és válassza a **New Project (Új projekt) lehetőséget.** 
1. Válassza **a Console App (.NET Core) (Konzolalkalmazás (.NET Core))** lehetőséget, majd a Next (Tovább) **lehetőséget.** 
1. A Projekt neve mezőben adja meg az **EventHubsReceiver** **nevet,** majd válassza a **Létrehozás lehetőséget.** 

### <a name="add-the-event-hubs-nuget-package"></a>Az Event Hubs NuGet-csomag hozzáadása

1. Válassza **a Tools**  >  NuGet Csomagkezelő Csomagkezelő Console (Eszközök NuGet-konzolja) lehetőséget a  >   menüben. 
1. Futtassa a következő parancsot az **Azure.Messaging.EventHubs** NuGet-csomag telepítéséhez:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
1. Futtassa a következő parancsot az **Azure.Messaging.EventHubs.Processor** NuGet-csomag telepítéséhez:

    ```cmd
    Install-Package Azure.Messaging.EventHubs.Processor
    ```    

### <a name="update-the-main-method"></a>A Main metódus frissítése 

1. Adja hozzá a következő `using` utasításokat a **Program.cs fájl tetején.**

    ```csharp
    using System;
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Storage.Blobs;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Consumer;
    using Azure.Messaging.EventHubs.Processor;
    ```
1. Adjon állandókat a osztályhoz a Event Hubs kapcsolati sztring `Program` és az eseményközpont neve számára. Cserélje le a zárójelben lévő helyőrzőket az eseményközpont létrehozásakor kapott megfelelő értékekre. Cserélje le a zárójelben lévő helyőrzőket az eseményközpont és a tárfiók létrehozásakor kapott megfelelő értékekre (hozzáférési kulcsok – elsődleges kapcsolati sztring). Győződjön meg arról, hogy az `{Event Hubs namespace connection string}` a névtérszintű kapcsolati sztring, és nem az eseményközpont sztringje.

    ```csharp
        private const string ehubNamespaceConnectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
        private const string eventHubName = "<EVENT HUB NAME>";
        private const string blobStorageConnectionString = "<AZURE STORAGE CONNECTION STRING>";
        private const string blobContainerName = "<BLOB CONTAINER NAME>";
    ```
3. Cserélje le `Main` a metódust a következő `async Main` metódusra. A részletekért tekintse meg a kód megjegyzéseit. 

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

            // Wait for 30 seconds for the events to be processed
            await Task.Delay(TimeSpan.FromSeconds(30));

            // Stop the processing
            await processor.StopProcessingAsync();
        }    
    ```
1. Most adja hozzá a következő esemény- és hibakezelő metódusokat a osztályhoz. 

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
1. Buildje a projektet, és győződjön meg arról, hogy nincsenek hibák.

    > [!NOTE]
    > A további tájékoztató megjegyzésekkel kiegészított teljes forráskódért tekintse meg ezt [a fájlt a GitHubon.](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample01_HelloWorld.md)
6. Futtassa a fogadóalkalmazást. 
1. Megjelenik egy üzenet arról, hogy az esemény érkezett. 

    ![Fogadott esemény](./media/getstarted-dotnet-standard-send-v2/event-received.png)

    Ezek az események a küldőprogram futtatásával korábban az eseményközpontba küldött három esemény. 


## <a name="next-steps"></a>Következő lépések
Tekintse meg a mintákat a GitHubon. 

- [Event Hubs a GitHubon](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [Eseményfeldolgozó-minták a GitHubon](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples)
- [Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC) minta](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
