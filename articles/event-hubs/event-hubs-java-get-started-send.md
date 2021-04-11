---
title: Azure Event Hubs-beli események küldése vagy fogadása a Javával (legújabb)
description: Ez a cikk bemutatja, hogyan hozhat létre olyan Java-alkalmazást, amely az Azure-Event Hubs legújabb Azure-Messaging-eventhubs csomag használatával küld/fogad eseményeket.
ms.topic: quickstart
ms.date: 04/05/2021
ms.custom: devx-track-java
ms.openlocfilehash: bae0d4147fddf57398d494ca7f13c347f892e45e
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448440"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-messaging-eventhubs"></a>Az Azure Event Hubs (Azure-Messaging-eventhubs) eseményeinek küldése vagy fogadása a Java használatával
Ez a rövid útmutató bemutatja, hogyan küldhet eseményeket az Event hub eseményeiről és fogadhat az **Azure-Messaging-eventhubs Java-** csomag használatával.

> [!IMPORTANT]
> Ez a rövid útmutató az új **Azure-Messaging-eventhubs** csomagot használja. A régi **Azure-eventhubs** és **Azure-eventhubs-EF** csomagokat használó gyors útmutató: [események küldése és fogadása az Azure-eventhubs és az Azure-eventhubs-EF használatával](event-hubs-java-get-started-send-legacy.md). 


## <a name="prerequisites"></a>Előfeltételek
Ha még nem ismeri az Azure Event Hubsét, a rövid útmutató elvégzése előtt tekintse meg a [Event Hubs áttekintése](event-hubs-about.md) című témakört. 

A rövid útmutató elvégzéséhez a következő előfeltételek szükségesek:

- **Microsoft Azure előfizetés**. Az Azure-szolgáltatások, például az Azure Event Hubs használatához előfizetésre van szükség.  Ha még nem rendelkezik Azure-fiókkal, regisztrálhat az [ingyenes próbaverzióra](https://azure.microsoft.com/free/) , vagy a [fiók létrehozásakor](https://azure.microsoft.com)használhatja az MSDN-előfizetői előnyeit.
- Java-fejlesztési környezet. Ez a rövid útmutató az [Eclipse](https://www.eclipse.org/)-t használja. Szükség van a Java Development Kit (JDK) 8-as vagy újabb verziójára. 
- **Hozzon létre egy Event Hubs névteret és egy Event hubot**. Első lépésként a [Azure Portal](https://portal.azure.com) használatával hozzon létre egy Event Hubs típusú névteret, és szerezze be azokat a felügyeleti hitelesítő adatokat, amelyekre az alkalmazásnak szüksége van az Event hub-vel való kommunikációhoz. A névtér és az Event hub létrehozásához kövesse az [ebben a cikkben](event-hubs-create.md)ismertetett eljárást. Ezután szerezze be a **Event Hubs névtérhez tartozó kapcsolatok karakterláncot** a cikk utasításait követve: a [kapcsolatok karakterláncának beolvasása](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). A rövid útmutató későbbi részében használja a kapcsolatok karakterláncát.

## <a name="send-events"></a>Események küldése 
Ebből a szakaszból megtudhatja, hogyan hozhat létre egy Java-alkalmazást az Event hub eseményeinek elküldéséhez. 

### <a name="add-reference-to-azure-event-hubs-library"></a>Hivatkozás hozzáadása az Azure Event Hubs Library-hoz

A Event Hubs Java-ügyféloldali könyvtára a [Maven Central adattárában](https://search.maven.org/search?q=a:azure-messaging-eventhubs)érhető el. Ezt a kódtárat a következő függőségi deklarációval hivatkozhat a Maven-projektfájl használatával:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-eventhubs</artifactId>
    <version>5.6.0</version>
</dependency>
```

> [!NOTE]
> Frissítse a verziót a Maven-tárházban közzétett legújabb verzióra. 

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Kód írása az üzenetek eseményközpontba való küldésére

A következő mintában először hozzon létre egy új Maven-projektet egy konzol/felületalkalmazáshoz a kedvenc Java-fejlesztőkörnyezetében. Vegyen fel egy nevű osztályt `Sender` , és adja hozzá a következő kódot a osztályhoz:

> [!IMPORTANT]
> Frissítse a `<Event Hubs namespace connection string>` Event Hubs névtérhez tartozó kapcsolódási karakterlánccal. Frissítse az `<Event hub name>` Event hub nevét a névtérben. 

```java
import com.azure.messaging.eventhubs.*;
import java.util.Arrays;
import java.util.List;

public class Sender {
    private static final String connectionString = "<Event Hubs namespace connection string>";
    private static final String eventHubName = "<Event hub name>";

    public static void main(String[] args) {
        publishEvents();
    }
}
```
### <a name="add-code-to-publish-events-to-the-event-hub"></a>Kód hozzáadása az események az Event hubhoz való közzétételéhez
Adjon hozzá egy nevű metódust `publishEvents` a `Sender` osztályhoz az alább látható módon. 

```java
    /**
     * Code sample for publishing events.
     * @throws IllegalArgumentException if the event data is bigger than max batch size.
     */
    public static void publishEvents() {
        // create a producer client
        EventHubProducerClient producer = new EventHubClientBuilder()
            .connectionString(connectionString, eventHubName)
            .buildProducerClient();

        // sample events in an array
        List<EventData> allEvents = Arrays.asList(new EventData("Foo"), new EventData("Bar"));
        
        // create a batch
        EventDataBatch eventDataBatch = producer.createBatch();

        for (EventData eventData : allEvents) {
            // try to add the event from the array to the batch
            if (!eventDataBatch.tryAdd(eventData)) {
                // if the batch is full, send it and then create a new batch
                producer.send(eventDataBatch);
                eventDataBatch = producer.createBatch();

                // Try to add that event that couldn't fit before.
                if (!eventDataBatch.tryAdd(eventData)) {
                    throw new IllegalArgumentException("Event is too large for an empty batch. Max size: "
                        + eventDataBatch.getMaxSizeInBytes());
                }
            }
        }
        // send the last batch of remaining events
        if (eventDataBatch.getCount() > 0) {
            producer.send(eventDataBatch);
        }
        producer.close();
    }
```

Hozza létre a programot, és ellenőrizze, hogy nincsenek-e hibák. Ezt a programot a fogadó program futtatása után fogja futtatni. 

## <a name="receive-events"></a>Események fogadása
Az oktatóanyagban szereplő kód a [githubon található EventProcessorClient-mintán](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorBlobCheckpointStoreSample.java)alapul, amelyet megvizsgálva megtekintheti a teljes működő alkalmazást.

> [!WARNING]
> Ha Azure Stack hub-on futtatja ezt a kódot, futásidejű hibákat tapasztal, kivéve, ha egy adott tárolási API-verziót céloz meg. Ennek oka az, hogy az Event Hubs SDK az Azure-ban elérhető legújabb elérhető Azure Storage API-t használja, amely esetleg nem érhető el az Azure Stack hub platformon. Az Azure Stack hub a Storage blob SDK egy másik verzióját is támogatja, mint az Azure-ban általában elérhető. Ha az Azure blog Storage-t ellenőrzőpont-tárolóként használja, tekintse [meg a támogatott Azure Storage API-verziót az Azure stack hub](/azure-stack/user/azure-stack-acs-differences?#api-version) -hoz, amely a programkódban található verzióra van kialakítva. 
>
> Ha például Azure Stack hub 2005-es verzióján fut, a Storage szolgáltatás legmagasabb rendelkezésre álló verziója a 2019-02-02-es verzió. Alapértelmezés szerint az Event Hubs SDK ügyféloldali kódtár az Azure legmagasabb rendelkezésre állású verzióját használja (2019-07-07 az SDK kiadásának időpontjában). Ebben az esetben a szakasz következő lépésein kívül kódot is hozzá kell adnia a Storage szolgáltatás API 2019-02-02-es verziójának célzásához. Az adott tárolási API-verzió célzásának példáját a [githubon](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java)tekintheti meg. 


### <a name="create-an-azure-storage-and-a-blob-container"></a>Azure Storage és blob-tároló létrehozása
Ebben a rövid útmutatóban az Azure Storage-t (pontosabban Blob Storage) használja ellenőrzőpont-tárolóként. Az ellenőrzőpontok olyan folyamat, amellyel egy adott esemény processzora megjelöli vagy véglegesíti az utolsó sikeresen feldolgozott esemény pozícióját egy partíción belül. Az ellenőrzőpontok megjelölése általában az eseményeket feldolgozó függvényen belül történik. Az ellenőrzőpontokkal kapcsolatos további információkért lásd: [Event Processor](event-processor-balance-partition-load.md).

Egy Azure Storage-fiók létrehozásához kövesse az alábbi lépéseket. 

1. [Azure Storage-fiók létrehozása](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Blobtároló létrehozása](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [A Storage-fiókhoz tartozó kapcsolódási karakterlánc lekérése](../storage/common/storage-configure-connection-string.md)

    Jegyezze fel a **kapcsolatok sztringjét** és a **tároló nevét**. Ezeket a fogadási kódban fogja használni. 

### <a name="add-event-hubs-libraries-to-your-java-project"></a>Event Hubs kódtárak hozzáadása a Java-projekthez
Adja hozzá a következő függőségeket a pom.xml fájlban. 

- [Azure-üzenetkezelés – eventhubs](https://search.maven.org/search?q=a:azure-messaging-eventhubs)
- [Azure-üzenetkezelés-eventhubs-checkpointstore-blob](https://search.maven.org/search?q=a:azure-messaging-eventhubs-checkpointstore-blob)

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs</artifactId>
        <version>5.6.0</version>
    </dependency>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs-checkpointstore-blob</artifactId>
        <version>1.5.1</version>
    </dependency>
</dependencies>
```

1. Adja hozzá a következő **importálási** utasításokat a Java-fájl elejéhez. 

    ```java
    import com.azure.messaging.eventhubs.EventHubClientBuilder;
    import com.azure.messaging.eventhubs.EventProcessorClient;
    import com.azure.messaging.eventhubs.EventProcessorClientBuilder;
    import com.azure.messaging.eventhubs.checkpointstore.blob.BlobCheckpointStore;
    import com.azure.messaging.eventhubs.models.ErrorContext;
    import com.azure.messaging.eventhubs.models.EventContext;
    import com.azure.storage.blob.BlobContainerAsyncClient;
    import com.azure.storage.blob.BlobContainerClientBuilder;
    import java.util.function.Consumer;
    ```
2. Hozzon létre egy nevű osztályt `Receiver` , és adja hozzá a következő karakterlánc-változókat a osztályhoz. Cserélje le a helyőrzőket a megfelelő értékekre. 

    > [!IMPORTANT]
    > Cserélje le a helyőrzőket a megfelelő értékekre.
    > - `<Event Hubs namespace connection string>` a Event Hubs névtérhez tartozó kapcsolódási karakterlánccal. Frissítés 
    > - `<Event hub name>` az Event hub nevét a névtérben. 
    > - `<Storage connection string>` Az Azure Storage-fiókhoz tartozó kapcsolódási karakterlánccal. 
    > - `<Storage container name>` a tároló nevét az Azure Blob Storage-ban. 

    ```java
    private static final String connectionString = "<Event Hubs namespace connection string>";
    private static final String eventHubName = "<Event hub name>";
    private static final String storageConnectionString = "<Storage connection string>";
    private static final String storageContainerName = "<Storage container name>";
    ```
1. Adja hozzá a következő `main` metódust a osztályhoz. 

    ```java
    public static void main(String[] args) throws Exception {
        // Create a blob container client that you use later to build an event processor client to receive and process events
        BlobContainerAsyncClient blobContainerAsyncClient = new BlobContainerClientBuilder()
            .connectionString(storageConnectionString) 
            .containerName(storageContainerName) 
            .buildAsyncClient();
    
        // Create a builder object that you will use later to build an event processor client to receive and process events and errors.
        EventProcessorClientBuilder eventProcessorClientBuilder = new EventProcessorClientBuilder()
            .connectionString(connectionString, eventHubName) 
            .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
            .processEvent(PARTITION_PROCESSOR) 
            .processError(ERROR_HANDLER) 
            .checkpointStore(new BlobCheckpointStore(blobContainerAsyncClient)); 

        // Use the builder object to create an event processor client 
        EventProcessorClient eventProcessorClient = eventProcessorClientBuilder.buildEventProcessorClient();

        System.out.println("Starting event processor");
        eventProcessorClient.start();

        System.out.println("Press enter to stop.");
        System.in.read();

        System.out.println("Stopping event processor");
        eventProcessorClient.stop();
        System.out.println("Event processor stopped.");

        System.out.println("Exiting process");
    }    
    ```
4. Adja hozzá az eseményeket és hibákat feldolgozó két segítő metódust ( `PARTITION_PROCESSOR` és `ERROR_HANDLER` ) az `Receiver` osztályhoz. 

    ```java
    public static final Consumer<EventContext> PARTITION_PROCESSOR = eventContext -> {
        System.out.printf("Processing event from partition %s with sequence number %d with body: %s %n", 
                eventContext.getPartitionContext().getPartitionId(), eventContext.getEventData().getSequenceNumber(), eventContext.getEventData().getBodyAsString());

        if (eventContext.getEventData().getSequenceNumber() % 10 == 0) {
            eventContext.updateCheckpoint();
        }
    };
    
    public static final Consumer<ErrorContext> ERROR_HANDLER = errorContext -> {
        System.out.printf("Error occurred in partition processor for partition %s, %s.%n",
            errorContext.getPartitionContext().getPartitionId(),
            errorContext.getThrowable());
    };    
    ```
3. Hozza létre a programot, és ellenőrizze, hogy nincsenek-e hibák. 

## <a name="run-the-applications"></a>Az alkalmazások futtatása
1. Először futtassa a **fogadó** alkalmazást.
1. Ezután futtassa a **küldő** alkalmazást. 
1. A **fogadó** alkalmazás ablakban ellenőrizze, hogy láthatók-e a küldő alkalmazás által közzétett események.

    ```cmd
    Starting event processor
    Press enter to stop.
    Processing event from partition 0 with sequence number 331 with body: Foo 
    Processing event from partition 0 with sequence number 332 with body: Bar 
    ```
1. Az alkalmazás leállításához nyomja le az **ENTER** billentyűt a fogadó alkalmazás ablakban. 

    ```cmd
    Starting event processor
    Press enter to stop.
    Processing event from partition 0 with sequence number 331 with body: Foo 
    Processing event from partition 0 with sequence number 332 with body: Bar 
    
    Stopping event processor
    Event processor stopped.
    Exiting process
    ```

## <a name="next-steps"></a>Következő lépések
Tekintse meg a következő mintákat a GitHubon:

- [Azure-üzenetkezelés – eventhubs minták](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [Azure-Messaging-eventhubs-checkpointstore-blob minták](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob).  
