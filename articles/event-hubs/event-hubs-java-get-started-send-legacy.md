---
title: Események küldése vagy fogadása az Azure Event Hubs Java (örökölt) használatával
description: Ez a cikk bemutatja, hogyan hozhat létre olyan Java-alkalmazást, amely az Azure Event Hubs a régi Azure-eventhubs csomag használatával küld/fogad eseményeket.
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: e86ca76f3eb661e1407a02b58e60b62b391f5702
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97607983"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-eventhubs"></a>Az Azure Event Hubs (Azure-eventhubs) eseményeinek küldése vagy fogadása a Java használatával

Ez a rövid útmutató bemutatja, hogyan küldhet eseményeket az Event hub eseményeiről és fogadhat az **Azure-eventhubs Java-** csomag használatával.

> [!WARNING]
> Ez a rövid útmutató a régi **Azure-eventhubs** és az **Azure-eventhubs-EF** csomagokat használja. Az  **Azure-Messaging-eventhubs** csomagot használó gyors útmutató: [események küldése és fogadása az Azure-Messaging-eventhubs használatával](event-hubs-java-get-started-send.md). Ha át szeretné helyezni az alkalmazást a régi csomagról az új verzióra, tekintse meg az [Azure-eventhubs az Azure-üzenetküldés-eventhubs való Migrálás útmutatóját](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/migration-guide.md). 

## <a name="prerequisites"></a>Előfeltételek

Ha még nem ismeri az Azure Event Hubs-t, a rövid útmutató elvégzése előtt tekintse meg a [Event Hubs áttekintése](event-hubs-about.md) című témakört. 

A rövid útmutató elvégzéséhez a következő előfeltételek szükségesek:

- **Microsoft Azure előfizetés**. Az Azure-szolgáltatások, például az Azure Event Hubs használatához előfizetésre van szükség.  Ha még nem rendelkezik Azure-fiókkal, regisztrálhat az [ingyenes próbaverzióra](https://azure.microsoft.com/free/) , vagy a [fiók létrehozásakor](https://azure.microsoft.com)használhatja az MSDN-előfizetői előnyeit.
- Java-fejlesztési környezet. Ez a rövid útmutató az [Eclipse](https://www.eclipse.org/)-t használja.
- **Hozzon létre egy Event Hubs névteret és egy Event hubot**. Első lépésként a [Azure Portal](https://portal.azure.com) használatával hozzon létre egy Event Hubs típusú névteret, és szerezze be azokat a felügyeleti hitelesítő adatokat, amelyekre az alkalmazásnak szüksége van az Event hub-vel való kommunikációhoz. A névtér és az Event hub létrehozásához kövesse az [ebben a cikkben](event-hubs-create.md)ismertetett eljárást. Ezután szerezze be az Event hub elérési kulcsának értékét a következő cikk utasításait követve: [kapcsolati karakterlánc beolvasása](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). A rövid útmutatóban később írt kód elérési kulcsát kell használnia. Az alapértelmezett kulcs neve: **RootManageSharedAccessKey**.

## <a name="send-events"></a>Események küldése 
Ebből a szakaszból megtudhatja, hogyan hozhat létre egy Java-alkalmazást az Event hub eseményeinek elküldéséhez. 

> [!NOTE]
> A rövid útmutatót mintaként letöltheti a [GitHubról](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend). Cserélje le az `EventHubConnectionString` és `EventHubName` sztringeket a saját eseményközpontja értékeire, majd futtassa a mintát. Azt is megteheti, hogy a rövid útmutatóban szereplő lépéseket követve létrehozhatja sajátját.

### <a name="add-reference-to-azure-event-hubs-library"></a>Hivatkozás hozzáadása az Azure Event Hubs Library-hoz

A Event Hubs Java-ügyféloldali kódtára a [Maven Central adattárból](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)származó Maven-projektekben használható. Ezt a kódtárat a következő függőségi deklarációval hivatkozhat a Maven-projektfájl használatával:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
```

A különböző típusú Build-környezetek esetében explicit módon beszerezheti a legújabb kiadott JAR-fájlokat a [Maven központi adattárból](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22).  

Egy egyszerű esemény-közzétevő esetében importálja a *com. microsoft. Azure. eventhubs* csomagot a Event Hubs ügyfél osztályokhoz és a *com. microsoft. Azure. servicebus* csomaghoz a segédprogram-osztályokhoz, például a Azure Service Bus üzenetkezelési ügyféllel megosztott általános kivételekhez. 

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Kód írása az üzenetek eseményközpontba való küldésére

A következő mintában először hozzon létre egy új Maven-projektet egy konzol/felületalkalmazáshoz a kedvenc Java-fejlesztőkörnyezetében. Vegyen fel egy nevű osztályt `SimpleSend` , és adja hozzá a következő kódot a osztályhoz:

```java
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
import com.microsoft.azure.eventhubs.EventData;
import com.microsoft.azure.eventhubs.EventHubClient;
import com.microsoft.azure.eventhubs.EventHubException;

import java.io.IOException;
import java.nio.charset.Charset;
import java.time.Instant;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;

public class SimpleSend {

    public static void main(String[] args)
            throws EventHubException, ExecutionException, InterruptedException, IOException {

    }
 }
```

### <a name="construct-connection-string"></a>Kapcsolatok karakterláncának létrehozása

A ConnectionStringBuilder osztály használatával hozza létre a Event Hubs ügyfél példányának átadandó kapcsolódási karakterlánc értékét. Cserélje le a helyőrzőket a névtér és az Event hub létrehozásakor beszerzett értékekre:

```java
        final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
                .setNamespaceName("<EVENTHUB NAMESPACE") 
                .setEventHubName("EVENT HUB")
                .setSasKeyName("RootManageSharedAccessKey")
                .setSasKey("SHARED ACCESS KEY");
```

### <a name="write-code-to-send-events"></a>Kód írása az események küldéséhez

Hozzon létre egy számokból álló eseményt egy karakterláncnak az UTF-8 bájtos kódolásba alakításával. Ezután hozzon létre egy új Event Hubs ügyfél-példányt a kapcsolatok sztringből, és küldje el az üzenetet:   

```java 
        final Gson gson = new GsonBuilder().create();

        // The Executor handles all asynchronous tasks and this is passed to the EventHubClient instance.
        // This enables the user to segregate their thread pool based on the work load.
        // This pool can then be shared across multiple EventHubClient instances.
        // The following sample uses a single thread executor, as there is only one EventHubClient instance,
        // handling different flavors of ingestion to Event Hubs here.
        final ScheduledExecutorService executorService = Executors.newScheduledThreadPool(4);

        // Each EventHubClient instance spins up a new TCP/TLS connection, which is expensive.
        // It is always a best practice to reuse these instances. The following sample shows this.
        final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);

        try {
            for (int i = 0; i < 10; i++) {

                String payload = "Message " + Integer.toString(i);
                byte[] payloadBytes = gson.toJson(payload).getBytes(Charset.defaultCharset());
                EventData sendEvent = EventData.create(payloadBytes);

                // Send - not tied to any partition
                // Event Hubs service will round-robin the events across all Event Hubs partitions.
                // This is the recommended & most reliable way to send to Event Hubs.
                ehClient.sendSync(sendEvent);
            }

            System.out.println(Instant.now() + ": Send Complete...");
            System.out.println("Press Enter to stop.");
            System.in.read();
        } finally {
            ehClient.closeSync();
            executorService.shutdown();
        }

``` 

Hozza létre és futtassa a programot, és ellenőrizze, hogy nincsenek-e hibák.

Gratulálunk! Üzeneteket küldött egy eseményközpontba.

### <a name="appendix-how-messages-are-routed-to-eventhub-partitions"></a>Függelék: az üzenetek EventHub-partíciókhoz való továbbításának módja

Mielőtt a felhasználók lekérik az üzeneteket, azokat közzé kell tenni a-közzétevők által először a partíciókban. Ha az üzeneteket a com. microsoft. Azure. eventhubs. EventHubClient objektum sendSync () metódusával szinkronban közzétesszük az Event hub-ban, az üzenetet elküldheti egy adott partíciónak, vagy átadható az összes rendelkezésre álló partícióra, attól függően, hogy a partíciós kulcs meg van-e adva.

Ha meg van adva a partíciós kulcsot megadó karakterlánc, a rendszer a kulcsot kivonattal határozza meg, hogy melyik partícióra küldje az eseményt.

Ha a partíciós kulcs nincs beállítva, az üzenetek az összes rendelkezésre álló partícióra kerekítve lesznek

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

// close the client at the end of your program
eventHubClient.closeSync();

```

## <a name="receive-events"></a>Események fogadása
Az oktatóanyagban szereplő kód a [githubon lévő EventProcessorSample-kód](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/EventProcessorSample)alapján történik, amelyet megvizsgálva megtekintheti a teljes működő alkalmazást.

### <a name="receive-messages-with-eventprocessorhost-in-java"></a>Üzenetek fogadása az EventProcessorHost használatával Javában

A **EventProcessorHost** egy Java-osztály, amely leegyszerűsíti az események fogadását a Event Hubsről az állandó ellenőrzőpontok és a párhuzamos fogadások kezelésével ezekből a Event Hubsokból. Az EventProcessorHost használatával feloszthatja az eseményeket több fogadóra, még akkor is, ha ezek különböző csomópontokon üzemelnek. Ez a példa bemutatja, hogyan használható az EventProcessorHost egyetlen fogadóhoz.

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

A EventProcessorHost használatához rendelkeznie kell egy [Azure Storage-fiókkal] [Azure Storage-fiókkal]:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), majd válassza az **erőforrás létrehozása** lehetőséget a képernyő bal oldalán.
2. Ezután válassza a **Tárolás**, majd a **Tárfiók** lehetőséget. A **Storage-fiók létrehozása** ablakban adja meg a Storage-fiók nevét. Fejezze be a többi mezőt, válassza ki a kívánt régiót, majd válassza a **Létrehozás** lehetőséget.

    ![Hozzon létre egy Storage-fiókot Azure Portal](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-azure-storage-account.png)

3. Válassza ki az újonnan létrehozott Storage-fiókot, majd válassza a **hozzáférési kulcsok** elemet:

    ![Hozzáférési kulcsainak beolvasása Azure Portal](./media/event-hubs-dotnet-framework-getstarted-receive-eph/select-azure-storage-access-keys.png)

    Másolja a key1 értéket egy ideiglenes helyre. Az oktatóanyag későbbi részében használni fogja.

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>Java-projekt létrehozása az EventProcessor Hosttal

A Event Hubs Java-ügyféloldali könyvtára a Maven [központi tárházból](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)származó Maven-projektekben használható, és a következő függőségi deklarációval hivatkozhat a Maven-projektfájl használatával: 

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>2.4.0</version>
</dependency>
```

A különböző típusú Build-környezetek esetében explicit módon beszerezheti a legújabb kiadott JAR-fájlokat a [Maven központi adattárból](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22).

1. A következő mintában először hozzon létre egy új Maven-projektet egy konzol/felületalkalmazáshoz a kedvenc Java-fejlesztőkörnyezetében. Az osztály neve `ErrorNotificationHandler` .     

    ```java
    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;

    public class ErrorNotificationHandler implements Consumer<ExceptionReceivedEventArgs>
    {
        @Override
        public void accept(ExceptionReceivedEventArgs t)
        {
            System.out.println("SAMPLE: Host " + t.getHostname() + " received general error notification during " + t.getAction() + ": " + t.getException().toString());
        }
    }
    ```
2. A következő kóddal hozzon létre egy `EventProcessorSample` nevű új osztályt. Cserélje le a helyőrzőket az Event hub és a Storage-fiók létrehozásakor használt értékekre:

   ```java
   package com.microsoft.azure.eventhubs.samples.eventprocessorsample;

   import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
   import com.microsoft.azure.eventhubs.EventData;
   import com.microsoft.azure.eventprocessorhost.CloseReason;
   import com.microsoft.azure.eventprocessorhost.EventProcessorHost;
   import com.microsoft.azure.eventprocessorhost.EventProcessorOptions;
   import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   import com.microsoft.azure.eventprocessorhost.IEventProcessor;
   import com.microsoft.azure.eventprocessorhost.PartitionContext;

   import java.util.concurrent.ExecutionException;
   import java.util.function.Consumer;

   public class EventProcessorSample
   {
       public static void main(String args[]) throws InterruptedException, ExecutionException
       {
           String consumerGroupName = "$Default";
           String namespaceName = "----NamespaceName----";
           String eventHubName = "----EventHubName----";
           String sasKeyName = "----SharedAccessSignatureKeyName----";
           String sasKey = "----SharedAccessSignatureKey----";
           String storageConnectionString = "----AzureStorageConnectionString----";
           String storageContainerName = "----StorageContainerName----";
           String hostNamePrefix = "----HostNamePrefix----";

           ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey);

           EventProcessorHost host = new EventProcessorHost(
                EventProcessorHost.createHostName(hostNamePrefix),
                eventHubName,
                consumerGroupName,
                eventHubConnectionString.toString(),
                storageConnectionString,
                storageContainerName);

           System.out.println("Registering host named " + host.getHostName());
           EventProcessorOptions options = new EventProcessorOptions();
           options.setExceptionNotification(new ErrorNotificationHandler());

           host.registerEventProcessor(EventProcessor.class, options)
           .whenComplete((unused, e) ->
           {
               if (e != null)
               {
                   System.out.println("Failure while registering: " + e.toString());
                   if (e.getCause() != null)
                   {
                       System.out.println("Inner exception: " + e.getCause().toString());
                   }
               }
           })
           .thenAccept((unused) ->
           {
               System.out.println("Press enter to stop.");
                 try 
               {
                   System.in.read();
               }
               catch (Exception e)
               {
                   System.out.println("Keyboard read failed: " + e.toString());
               }
           })
           .thenCompose((unused) ->
            {
                return host.unregisterEventProcessor();
           })
           .exceptionally((e) ->
           {
               System.out.println("Failure while unregistering: " + e.toString());
               if (e.getCause() != null)
               {
                   System.out.println("Inner exception: " + e.getCause().toString());
               }
               return null;
           })
           .get(); // Wait for everything to finish before exiting main!

           System.out.println("End of sample");
       }
   }
   ```
3. Hozzon létre még egy nevű osztályt `EventProcessor` a következő kód használatával:

    ```java
    public static class EventProcessor implements IEventProcessor
    {
        private int checkpointBatchingCount = 0;

        // OnOpen is called when a new event processor instance is created by the host. 
        @Override
        public void onOpen(PartitionContext context) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
        }

        // OnClose is called when an event processor instance is being shut down. 
        @Override
        public void onClose(PartitionContext context, CloseReason reason) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
        }

        // onError is called when an error occurs in EventProcessorHost code that is tied to this partition, such as a receiver failure.
        @Override
        public void onError(PartitionContext context, Throwable error)
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
        }

        // onEvents is called when events are received on this partition of the Event Hub. 
        @Override
        public void onEvents(PartitionContext context, Iterable<EventData> events) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got event batch");
            int eventCount = 0;
            for (EventData data : events)
            {
                try
                {
                    System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                            data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBytes(), "UTF8"));
                    eventCount++;

                    // Checkpointing persists the current position in the event stream for this partition and means that the next
                    // time any host opens an event processor on this event hub+consumer group+partition combination, it will start
                    // receiving at the event after this one. 
                    this.checkpointBatchingCount++;
                    if ((checkpointBatchingCount % 5) == 0)
                    {
                        System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                               data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
                        // Checkpoints are created asynchronously. It is important to wait for the result of checkpointing
                        // before exiting onEvents or before creating the next checkpoint, to detect errors and to ensure proper ordering.
                        context.checkpoint(data).get();
                    }
                }
                catch (Exception e)
                {
                    System.out.println("Processing failed for an event: " + e.toString());
                }
            }
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + eventCount + " for host " + context.getOwner());
        }
    }
    ```

Ez az oktatóprogram az EventProcessorHost egyetlen példányát használja. Az átviteli sebesség növelése érdekében javasoljuk, hogy a EventProcessorHost több példányát futtassa, lehetőleg különálló gépeken.  Redundanciát is biztosít. Ilyen esetekben a különböző példányok automatikusan koordinálnak egymással a fogadott események terhelésének kiegyenlítéséhez. Ha több fogadóval szeretné feldolgoztatni az *összes* eseményt, a **ConsumerGroup** szolgáltatást kell használnia. Ha több gépről fogad eseményeket, célszerű lehet az azokat futtató gépeken (vagy szerepkörökön) alapuló neveket adni az EventProcessorHost példányoknak.

### <a name="publishing-messages-to-eventhub"></a>Üzenetek közzététele a EventHub-ben

Mielőtt a felhasználók lekérik az üzeneteket, azokat közzé kell tenni a-közzétevők által először a partíciókban. Érdemes megjegyezni, hogy ha a sendSync () metódust a com. microsoft. Azure. eventhubs. EventHubClient objektumon keresztül teszi közzé az Event hub-ban, az üzenetet elküldheti egy adott partícióra, vagy az összes rendelkezésre álló partícióra kiterjeszthető, attól függően, hogy a partíciós kulcs meg van-e adva.

Ha meg van adva a partíciós kulcsot megadó karakterlánc, a rendszer kivonattal határozza meg, hogy melyik partíciót kell elküldeni az eseménynek.

Ha a partíciós kulcs nincs beállítva, az üzenetek az összes rendelkezésre álló partícióra kerekítve lesznek

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

```

### <a name="implementing-a-custom-checkpointmanager-for-eventprocessorhost-eph"></a>Egyéni CheckpointManager megvalósítása a EventProcessorHost (EF) számára

Az API egy mechanizmust biztosít az egyéni ellenőrzőpont-kezelő megvalósítására olyan forgatókönyvek esetén, amelyekben az alapértelmezett implementáció nem kompatibilis a használati esettel.

Az alapértelmezett ellenőrzőpont-kezelő blob Storage-t használ, de ha felülbírálja az EF által a saját implementációja által használt ellenőrzőpont-kezelőt, akkor bármilyen tárolót használhat, amelyet vissza szeretne állítani a Checkpoint Manager megvalósításához.

Hozzon létre egy osztályt, amely megvalósítja a com. microsoft. Azure. eventprocessorhost. ICheckpointManager felületet

Az ellenőrzőpont-kezelő egyéni implementációjának használata (com. microsoft. Azure. eventprocessorhost. ICheckpointManager)

A megvalósításon belül felülbírálhatja az alapértelmezett ellenőrzőpont-ellenőrzési mechanizmust, és saját adattárolójuk alapján implementálhatja a saját ellenőrzőpontokat (például SQL Server, CosmosDB és Azure cache for Redis). Javasoljuk, hogy az ellenőrzőpont-kezelő megvalósításához használt áruház elérhető legyen minden olyan EF-példány számára, amely a fogyasztói csoport eseményeinek feldolgozását végzi.

Bármilyen, a környezetében elérhető adattárat használhat.

A com. microsoft. Azure. eventprocessorhost. EventProcessorHost osztály két konstruktort biztosít, amelyek segítségével felülbírálhatja a EventProcessorHost ellenőrzőpont-kezelőjét.

## <a name="next-steps"></a>Következő lépések
Olvassa el a következő cikkeket: 

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Az Azure Event Hubs funkciói és terminológiája](event-hubs-features.md)
- [Event Hubs – gyakori kérdések](event-hubs-faq.md)
