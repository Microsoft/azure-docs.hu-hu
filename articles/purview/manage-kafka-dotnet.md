---
title: Üzenetek közzététele az Azure Purview Atlas Kafka-témaköreibe, illetve üzenetek feldolgozása Event Hubs .NET használatával
description: Ez a cikk egy olyan .NET Core-alkalmazás létrehozásához nyújt bemutatót, amely a legújabb Azure.Messaging.EventHubs csomag használatával eseményeket küld a Purview Apache Atlas Kafka-témaköreibe vagy onnan más szolgáltatásokba.
ms.topic: quickstart
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: dotnet
ms.date: 04/15/2021
ms.openlocfilehash: 60c177c913c78dbcfcbfe1d465044b69b0e6dd2e
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590421"
---
# <a name="publish-messages-to-and-process-messages-from-azure-purviews-atlas-kafka-topics-via-event-hubs-using-net"></a>Üzenetek közzététele az Azure Purview Atlas Kafka-témaköreibe, illetve üzenetek feldolgozása Event Hubs .NET használatával 
Ez a rövid útmutató bemutatja, hogyan küldhet eseményeket és fogadhat eseményeket az Azure Purview Atlas Kafka-témaköreiből az eseményközponton keresztül az **Azure.Messaging.EventHubs** .NET-kódtár használatával. 

> [!IMPORTANT]
> A felügyelt eseményközpont a Fiók véglegesnézete fiók létrehozása során jön létre. Lásd: Fiók létrehozása [véglegesen.](create-catalog-portal.md) Az üzeneteket közzéteheti az eseményközpont kafka-témakörében, ATLAS_HOOK a Purview felhasználja és feldolgozza azt. A Purview értesíti az eseményközpont kafka-témakörének entitásváltozását ATLAS_ENTITIES és a felhasználó felhasználhatja és feldolgozhatja azt. Ez a rövid útmutató az új **Azure.Messaging.EventHubs kódtárat használja.** 


## <a name="prerequisites"></a>Előfeltételek
Ha még csak most kezd el Azure Event Hubs, tekintse Event Hubs [áttekintését](../event-hubs/event-hubs-about.md) a rövid útmutatóhoz. 

A rövid útmutató befejezéséhez a következő előfeltételekre lesz szüksége:

- **Microsoft Azure előfizetéshez.** Az Azure-szolgáltatások (Azure Event Hubs is) előfizetésre van szüksége.  Ha még nem rendelkezik Azure-fiókkal, regisztrálhat [](https://azure.microsoft.com/free/) egy ingyenes próbaverzióra, vagy használhatja MSDN-előfizetői előnyeit a [fiók létrehozásakor.](https://azure.microsoft.com)
- **Microsoft Visual Studio 2019**. A Azure Event Hubs ügyféloldali kódtár a C# 8.0-ban bevezetett új funkciókat használja.  Továbbra is használhatja a kódtárat a C# korábbi nyelvi verzióival, de az új szintaxis nem lesz elérhető. A teljes szintaxis használata érdekében ajánlott a 3.0-s vagy újabb .NET Core SDK [](/dotnet/csharp/language-reference/configure-language-version#override-a-default) és a nyelvi verziót a következőre állítani: [](https://dotnet.microsoft.com/download) `latest` . Ha a 2019 Visual Studio előtti verziókat használja, Visual Studio nem kompatibilisek a C# 8.0-projektek felépítéséhez szükséges eszközökkel. Visual Studio 2019-es kiadását, beleértve az ingyenes Community kiadást, itt [töltheti le.](https://visualstudio.microsoft.com/vs/)

## <a name="publish-messages-to-purview"></a>Üzenetek közzététele a Nézetben 
Ez a szakasz bemutatja, hogyan hozhat létre egy .NET Core-konzolalkalmazást, amely eseményeket küld egy Purview-nézetbe az eseményközpont kafka-témakörének **ATLAS_HOOK.** 

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

Ezután hozzon létre egy C# .NET-konzolalkalmazást a Visual Studio:

1. Indítsa **el Visual Studio.**
2. A Start ablakban válassza az Új projekt **létrehozása Konzolalkalmazás**  >  **(.NET-keretrendszer) lehetőséget.** A lépések elvégzéséhez a .NET 4.5.2-es vagy újabb verziója szükséges.
3. A **Projekt neve alatt adja** meg a **PurviewKafkaProducer nevet.**
4. A projekt létrehozásához válassza a **Létrehozás** lehetőséget.

### <a name="create-a-console-application"></a>Konzolalkalmazás létrehozása

1. Indítsa el a Visual Studio 2019-et. 
1. Válassza **az Új projekt létrehozása lehetőséget.** 
1. A **Create a new project (Új** projekt létrehozása) párbeszédpanelen tegye a  következőket: Ha nem látja ezt a párbeszédpanelt, válassza a menü File (Fájl) parancsát, válassza a **New**(Új) lehetőséget, majd a **Project (Projekt) lehetőséget.** 
    1. A **programozási nyelvhez** válassza a C# lehetőséget.
    1. Válassza **a Konzol** lehetőséget az alkalmazás típusaként. 
    1. Válassza **a Console App (.NET Core) (Konzolalkalmazás (.NET Core) lehetőséget az** eredménylistából. 
    1. Ezután válassza a **Tovább lehetőséget.** 


### <a name="add-the-event-hubs-nuget-package"></a>Az Event Hubs NuGet-csomag hozzáadása

1. Válassza **a Tools**  >  NuGet Csomagkezelő Csomagkezelő Console (Eszközök NuGet-konzolja) lehetőséget a  >   menüben. 
1. Futtassa a következő parancsot az **Azure.Messaging.EventHubs** NuGet-csomag és az **Azure.Messaging.EventHubs.Producer** NuGet-csomag telepítéséhez:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
    
    ```cmd
    Install-Package Azure.Messaging.EventHubs.Producer
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

2. Adjon állandókat a osztályhoz a Event Hubs `Program` kapcsolati sztring és az eseményközpont neve számára. 

    ```csharp
    private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
    private const string eventHubName = "<EVENT HUB NAME>";
    ```

    A purview fiókhoz társított eseményközpont-névteret a Purview-fiók Tulajdonságok lapján, az Atlas kafka-végpont elsődleges/másodlagos kapcsolati sztringjénél kaphatja meg.

    :::image type="content" source="media/manage-eventhub-kafka-dotnet/properties.png" alt-text="Eseményközpont-névtér":::

    Az eseményközpont nevének meg **kell ATLAS_HOOK,** hogy üzeneteket küldsen a Purview alkalmazásba.

3. Cserélje le a metódust a következő metódusra, és adjon hozzá egy et az üzenetek `Main` `async Main` a `async ProduceMessage` Purview nézetbe való leküldéshez. A részletekért tekintse meg a kód megjegyzéseit. 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;
            
            / Create an event producer client to add events in the event hub
            EventHubProducerClient producer = new EventHubProducerClient(ehubNamespaceConnectionString, eventHubName);
            
            await ProduceMessage(producer);
        }
        
        static async Task ProduceMessage(EventHubProducerClient producer)
     
        {
            // Create a batch of events 
            using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

            // Add events to the batch. An event is a represented by a collection of bytes and metadata. 
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<First event>")));
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<Second event>")));
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<Third event>")));

            // Use the producer client to send the batch of events to the event hub
            await producerClient.SendAsync(eventBatch);
            Console.WriteLine("A batch of 3 events has been published.");
             
        }
    ```
5. Buildje a projektet, és győződjön meg arról, hogy nincsenek hibák.
6. Futtassa a programot, és várja meg a megerősítést kérő üzenetet. 

    > [!NOTE]
    > A további tájékoztató megjegyzéseket is tartalmazó teljes forráskódért tekintse meg ezt [a fájlt a GitHubon](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md)

### <a name="sample-create-entity-json-message-to-create-a-sql-table-with-two-columns"></a>Entitás JSON-üzenetének létrehozása minta egy kétoszlopos SQL-tábla létrehozásához.

```json
    
    {
    "msgCreatedBy": "nayenama",
    "message": {
    "entities": {
    "referredEntities": {
        "-1102395743156037": {
            "typeName": "azure_sql_column",
            "attributes": {
                "owner": null,
                "userTypeId": 61,
                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderID",
                "precision": 23,
                "length": 8,
                "description": "Sales Order ID",
                "scale": 3,
                "name": "OrderID",
                "data_type": "int",
                "table": {
                    "guid": "-1102395743156036",
                    "typeName": "azure_sql_table",
                    "entityStatus": "ACTIVE",
                    "displayText": "SalesOrderTable",
                    "uniqueAttributes": {
                                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable"
                    }
            }
            },
            "guid": "-1102395743156037",
            "version": 2
        },
        "-1102395743156038": {
         "typeName": "azure_sql_column",
            "attributes": {
                "owner": null,
                "userTypeId": 61,
                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderDate",
                "description": "Sales Order Date",
                "scale": 3,
                "name": "OrderDate",
                "data_type": "datetime",
                "table": {
                    "guid": "-1102395743156036",
                    "typeName": "azure_sql_table",
                    "entityStatus": "ACTIVE",
                    "displayText": "SalesOrderTable",
                    "uniqueAttributes": {
                                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable"
                    }
            }
            },
            "guid": "-1102395743156038",
            "status": "ACTIVE",
            "createdBy": "ServiceAdmin",
            "version": 0
        }
        },
        "entity": 
                {
                    "typeName": "azure_sql_table",
                    "attributes": {
                        "owner": "admin",
                        "temporary": false,
                        "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable",
                        "name" : "SalesOrderTable",
                        "description": "Sales Order Table added via Kafka",
                        "columns": [
                            {
                                "guid": "-1102395743156037",
                                "typeName": "azure_sql_column",
                                "uniqueAttributes": {
                                    "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderID"
                                }
                            },
                            {
                                "guid": "-1102395743156038",
                                "typeName": "azure_sql_column",
                                "uniqueAttributes": {
                                    "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderDate"
                                }
                            }
                        ]
                        },
                        "guid": "-1102395743156036",
                    "version": 0                
                    }
                },
        "type": "ENTITY_CREATE_V2",
        "user": "admin"
    },
    "version": {
        "version": "1.0.0"
    },
    "msgCompressionKind": "NONE",
    "msgSplitIdx": 1,
    "msgSplitCount": 1
}

``` 

## <a name="consume-messages-from-purview"></a>Üzenetek fogadása a Purview-ból
Ez a szakasz bemutatja, hogyan írhat olyan .NET Core-konzolalkalmazást, amely egy eseményfeldolgozóval fogad üzeneteket egy eseményközpontból. A Purview-ból származó üzenetek fogadásához egy ATLAS_ENTITIES-eseményközpontot kell használnia. Az eseményfeldolgozó leegyszerűsíti az események fogadását az eseményközpontok állandó ellenőrzőpontjai és párhuzamos fogadásai kezelésével. 

> [!WARNING]
> Ha ezt a kódot a Azure Stack Hub futtatja, futásidejű hibákat fog tapasztalni, kivéve, ha egy adott Storage API-verziót céloz meg. Ennek az az oka, hogy az Event Hubs SDK az Azure-ban elérhető legújabb elérhető Azure Storage API-t használja, amely nem mindig érhető el a Azure Stack Hub platformon. Azure Stack Hub a Storage Blob SDK más verzióját is támogatja, mint az Azure-ban. Ha ellenőrzőpont-Azure Blob Storage használ, ellenőrizze a támogatott Azure Storage API-verziót a Azure Stack Hub [buildhez](/azure-stack/user/azure-stack-acs-differences?#api-version) és célverzióhoz a kódban. 
>
> Ha például a 2005-ös Azure Stack Hub futtatja, a Storage szolgáltatáshoz elérhető legmagasabb verzió a 2019.02. 02. verzió. Alapértelmezés szerint az Event Hubs SDK ügyféloldali kódtára az Azure-ban elérhető legmagasabb verziót használja (2019-07-07 az SDK kiadásakor). Ebben az esetben a szakasz lépései mellett a Storage szolgáltatás API 2019-02-02-es verziójának megcélzott kódját is hozzá kell adni. Egy adott Storage API-verzió megcélozására vonatkozó példát ebben a [githubon található példában láthat.](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/) 
 

### <a name="create-an-azure-storage-and-a-blob-container"></a>Azure Storage és blobtároló létrehozása
Ebben a rövid útmutatóban az Azure Storage-et használja ellenőrzőpont-tárolóként. Kövesse az alábbi lépéseket egy Azure Storage-fiók létrehozásához. 

1. [Azure Storage-fiók létrehozása](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Blobtároló létrehozása](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [A tárfiókhoz való kapcsolati sztring lekért](../storage/common/storage-configure-connection-string.md)

    Jegyezze fel a kapcsolati sztringet és a tároló nevét. Ezeket a fogadási kódban fogja használni. 


### <a name="create-a-project-for-the-receiver"></a>Projekt létrehozása a fogadó számára

1. A Megoldáskezelő kattintson a jobb gombbal az **EventHubQuickStart** megoldásra, mutasson az **Add**(Hozzáadás) parancsra, és válassza a **New Project (Új projekt) lehetőséget.** 
1. Válassza **a Console App (.NET Core) (Konzolalkalmazás (.NET Core))** lehetőséget, majd a Next (Tovább) **lehetőséget.** 
1. A Projekt neve mezőben adja meg a **PurviewKafkaConsumer** **nevet,** majd válassza a **Létrehozás lehetőséget.** 

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
    
    A purview fiókhoz társított eseményközpont-névteret a Purview-fiók Tulajdonságok lapján, az Atlas kafka-végpont elsődleges/másodlagos kapcsolati sztringjénél kaphatja meg.

    :::image type="content" source="media/manage-eventhub-kafka-dotnet/properties.png" alt-text="Eseményközpont-névtér":::

    Az eseményközpont nevét úgy **kell ATLAS_ENTITIES,** hogy üzeneteket küldsen a Purview alkalmazásba.

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

            // Wait for 10 seconds for the events to be processed
            await Task.Delay(TimeSpan.FromSeconds(10));

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

### <a name="sample-message-received-from-purview"></a>A Purview által fogadott mintaüzenet

```json
{
    "version":
        {"version":"1.0.0",
         "versionParts":[1]
        },
         "msgCompressionKind":"NONE",
         "msgSplitIdx":1,
         "msgSplitCount":1,
         "msgSourceIP":"10.244.155.5",
         "msgCreatedBy":
         "",
         "msgCreationTime":1618588940869,
         "message":{
            "type":"ENTITY_NOTIFICATION_V2",
            "entity":{
                "typeName":"azure_sql_table",
                    "attributes":{
                        "owner":"admin",
                        "createTime":0,
                        "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable",
                        "name":"SalesOrderTable",
                        "description":"Sales Order Table"
                        },
                        "guid":"ead5abc7-00a4-4d81-8432-d5f6f6f60000",
                        "status":"ACTIVE",
                        "displayText":"SalesOrderTable"
                    },
                    "operationType":"ENTITY_UPDATE",
                    "eventTime":1618588940567
                }
}
```

> [!IMPORTANT]
> Az Atlas jelenleg a következő művelettípusokat támogatja: **ENTITY_CREATE_V2**, **ENTITY_PARTIAL_UPDATE_V2**, **ENTITY_FULL_UPDATE_V2**, **ENTITY_DELETE_V2.** Az üzenetek küldése a Purview-ba jelenleg alapértelmezés szerint engedélyezve van. Ha a forgatókönyvben a Purview nézetből kell olvasnia, lépjen kapcsolatba velünk, mert engedélyeznie kell a listázatát. (adja meg az előfizetés azonosítóját és a Purview-fiók nevét).


## <a name="next-steps"></a>Következő lépések
Tekintse meg a mintákat a GitHubon. 

- [Event Hubs a GitHubon](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [Eseményfeldolgozó-minták a GitHubon](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples)
- [Az Atlas bemutatása az értesítésekben](https://atlas.apache.org/2.0.0/Notifications.html)