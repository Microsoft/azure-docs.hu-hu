---
title: Azure Event Hubs-beli események küldése vagy fogadása a Javával (legújabb)
description: Ez a cikk bemutatja, hogyan hozhat létre olyan Java-alkalmazást, amely az Azure-Event Hubs legújabb Azure-Messaging-eventhubs csomag használatával küld/fogad eseményeket.
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 640f6c4dcb223e55e10f7cb5d7daaa44dbd41578
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102172023"
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
    <version>5.0.1</version>
</dependency>
```

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Kód írása az üzenetek eseményközpontba való küldésére

A következő mintában először hozzon létre egy új Maven-projektet egy konzol/felületalkalmazáshoz a kedvenc Java-fejlesztőkörnyezetében. Vegyen fel egy nevű osztályt `Sender` , és adja hozzá a következő kódot a osztályhoz:

```java
import com.azure.messaging.eventhubs.*;
import static java.nio.charset.StandardCharsets.UTF_8;

public class Sender {
       public static void main(String[] args) {
    }
}
```

### <a name="connection-string-and-event-hub"></a>Kapcsolati sztring és Event hub
Ez a kód a kapcsolati karakterláncot használja a Event Hubs névtérhez és az Event hub nevéhez Event Hubs ügyfél létrehozásához. 

```java
String connectionString = "<CONNECTION STRING to EVENT HUBS NAMESPACE>";
String eventHubName = "<EVENT HUB NAME>";
```

### <a name="create-an-event-hubs-producer-client"></a>Event Hubs gyártói ügyfél létrehozása 
Ez a kód létrehoz egy termelői ügyféltanúsítványt, amely az Event hub-hoz tartozó események előállítására és küldésére szolgál. 

```java
EventHubProducerClient producer = new EventHubClientBuilder()
    .connectionString(connectionString, eventHubName)
    .buildProducerClient();
```

### <a name="prepare-a-batch-of-events"></a>Események kötegének előkészítése
Ez a kód előkészíti az események kötegét. 

```java
EventDataBatch batch = producer.createBatch();
batch.tryAdd(new EventData("First event"));
batch.tryAdd(new EventData("Second event"));
batch.tryAdd(new EventData("Third event"));
batch.tryAdd(new EventData("Fourth event"));
batch.tryAdd(new EventData("Fifth event"));
```

### <a name="send-the-batch-of-events-to-the-event-hub"></a>Események kötegének elküldése az Event hub-nak
Ez a kód az előző lépésben az Event hub-ba előkészített események kötegét küldi el. A küldési művelet következő kódrészletei. 

```java
producer.send(batch);
```

### <a name="close-and-cleanup"></a>Bezárás és törlés
Ez a kód lezárja a gyártót. 

```java
producer.close();
```
### <a name="complete-code-to-send-events"></a>Az események küldésére szolgáló kód végrehajtása
Itt látható a teljes kód, amely az Event hub eseményeinek küldésére szolgál. 

```java
import com.azure.messaging.eventhubs.*;

public class Sender {
    public static void main(String[] args) {
        final String connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
        final String eventHubName = "EVENT HUB NAME";

        // create a producer using the namespace connection string and event hub name
        EventHubProducerClient producer = new EventHubClientBuilder()
            .connectionString(connectionString, eventHubName)
            .buildProducerClient();

        // prepare a batch of events to send to the event hub    
        EventDataBatch batch = producer.createBatch();
        batch.tryAdd(new EventData("First event"));
        batch.tryAdd(new EventData("Second event"));
        batch.tryAdd(new EventData("Third event"));
        batch.tryAdd(new EventData("Fourth event"));
        batch.tryAdd(new EventData("Fifth event"));

        // send the batch of events to the event hub
        producer.send(batch);

        // close the producer
        producer.close();
    }
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
        <version>5.1.1</version>
    </dependency>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs-checkpointstore-blob</artifactId>
        <version>1.5.0</version>
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
    import java.util.concurrent.TimeUnit;
    ```
2. Hozzon létre egy nevű osztályt `Receiver` , és adja hozzá a következő karakterlánc-változókat a osztályhoz. Cserélje le a helyőrzőket a megfelelő értékekre. 
    ```java
    private static final String EH_NAMESPACE_CONNECTION_STRING = "<EVENT HUBS NAMESPACE CONNECTION STRING>";
    private static final String eventHubName = "<EVENT HUB NAME>";
    private static final String STORAGE_CONNECTION_STRING = "<AZURE STORAGE CONNECTION STRING>";
    private static final String STORAGE_CONTAINER_NAME = "<AZURE STORAGE CONTAINER NAME>";
    ```
3. Adja hozzá a következő `main` metódust a osztályhoz. 

    ```java
    public static void main(String[] args) throws Exception {
        // Create a blob container client that you use later to build an event processor client to receive and process events
        BlobContainerAsyncClient blobContainerAsyncClient = new BlobContainerClientBuilder()
            .connectionString(STORAGE_CONNECTION_STRING) 
            .containerName(STORAGE_CONTAINER_NAME) 
            .buildAsyncClient();
    
        // Create a builder object that you will use later to build an event processor client to receive and process events and errors.
        EventProcessorClientBuilder eventProcessorClientBuilder = new EventProcessorClientBuilder()
            .connectionString(EH_NAMESPACE_CONNECTION_STRING, eventHubName) 
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
3. A teljes kódnak így kell kinéznie: 

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
    import java.util.concurrent.TimeUnit;
    
    public class Receiver {
        
        private static final String EH_NAMESPACE_CONNECTION_STRING = "<EVENT HUBS NAMESPACE CONNECTION STRING>";
        private static final String eventHubName = "<EVENT HUB NAME>";
        private static final String STORAGE_CONNECTION_STRING = "<AZURE STORAGE CONNECTION STRING>";
        private static final String STORAGE_CONTAINER_NAME = "<AZURE STORAGE CONTAINER NAME>";
    
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
        
        public static void main(String[] args) throws Exception {
            BlobContainerAsyncClient blobContainerAsyncClient = new BlobContainerClientBuilder()
                .connectionString(STORAGE_CONNECTION_STRING)
                .containerName(STORAGE_CONTAINER_NAME)
                .buildAsyncClient();
    
            EventProcessorClientBuilder eventProcessorClientBuilder = new EventProcessorClientBuilder()
                .connectionString(EH_NAMESPACE_CONNECTION_STRING, eventHubName)
                .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
                .processEvent(PARTITION_PROCESSOR)
                .processError(ERROR_HANDLER)
                .checkpointStore(new BlobCheckpointStore(blobContainerAsyncClient));
    
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
        
    }
    ```
3. Hozza létre a programot, és ellenőrizze, hogy nincsenek-e hibák. 

## <a name="run-the-applications"></a>Az alkalmazások futtatása
1. Először futtassa a **fogadó** alkalmazást.
1. Ezután futtassa a **küldő** alkalmazást. 
1. A **fogadó** alkalmazás ablakban ellenőrizze, hogy láthatók-e a küldő alkalmazás által közzétett események.
1. Az alkalmazás leállításához nyomja le az **ENTER** billentyűt a fogadó alkalmazás ablakban. 

## <a name="next-steps"></a>Következő lépések
Tekintse meg a következő mintákat a GitHubon:

- [Azure-üzenetkezelés – eventhubs minták](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [Azure-Messaging-eventhubs-checkpointstore-blob minták](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob).  
