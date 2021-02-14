---
title: Azure Service Bus Queues használata Javával (Azure-Messaging-servicebus)
description: Ebből az oktatóanyagból megtudhatja, hogyan használható a Java egy Azure Service Bus üzenetsor üzeneteinek üzenetküldésére és fogadására. Használja az új Azure-Messaging-servicebus csomagot.
ms.devlang: Java
ms.topic: quickstart
ms.date: 02/13/2021
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: bfe7835bea4415085279fb77eb85d67ed3f5f0f3
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518606"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-java"></a>Üzenetek küldése és fogadása Azure Service Bus várólistákból (Java)
Ebben a rövid útmutatóban egy Java-alkalmazást fog létrehozni, amely üzeneteket küld egy Azure Service Bus üzenetsor üzeneteit, és fogadja az üzeneteket. 

> [!IMPORTANT]
> Ez a rövid útmutató az új Azure-Messaging-servicebus csomagot használja. A régi Azure-servicebus csomagot használó gyors útmutatóért lásd: [üzenetek küldése és fogadása az Azure-servicebus használatával](service-bus-java-how-to-use-queues-legacy.md).


## <a name="prerequisites"></a>Előfeltételek
- Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja MSDN- [előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) , vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Ha nem rendelkezik várólistával, hogy működjön a szolgáltatással, a várólista létrehozásához kövesse az [Azure Portal használata Service Bus üzenetsor létrehozásához](service-bus-quickstart-portal.md) című cikket. Jegyezze **fel a Service Bus névtér és a létrehozott** **várólista** nevét.
- Telepítse [a Javához készült Azure SDK][Azure SDK for Java]-t. Ha az Eclipse-t használja, akkor telepítheti a Javához készült Azure SDK-t tartalmazó [Azure Toolkit for Eclipse][Azure Toolkit for Eclipse] . Ezután hozzáadhatja a **Javához készült Microsoft Azure kódtárakat** a projekthez. Ha a IntelliJ-t használja, tekintse meg [a Azure Toolkit for IntelliJ telepítését](/azure/developer/java/toolkit-for-intellij/installation)ismertető témakört. 


## <a name="send-messages-to-a-queue"></a>Üzenetek küldése egy üzenetsorba
Ebben a szakaszban létre fog hozni egy Java-konzol projektet, és hozzá kell adnia egy kódot, amely üzeneteket küld a korábban létrehozott várólistába. 

### <a name="create-a-java-console-project"></a>Java-konzol projekt létrehozása
Hozzon létre egy Java-projektet az Eclipse vagy egy tetszőleges eszköz használatával. 

### <a name="configure-your-application-to-use-service-bus"></a>Az alkalmazás konfigurálása Service Bus használatára
Hivatkozások hozzáadása az Azure Core-hoz és a Azure Service Bus könyvtárakhoz. 

Ha Eclipse-et használ, és létrehozott egy Java-konzol alkalmazást, alakítsa át a Java-projektet a mavenbe: kattintson a jobb gombbal a projektre a **Package Explorer** ablakban, **majd válassza a**  ->  **Konvertálás a Maven projektbe** lehetőséget. Ezután vegyen fel függőségeket a két kódtárara az alábbi példában látható módon.

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>org.myorg.sbusquickstarts</groupId>
    <artifactId>sbustopicqs</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <build>
        <sourceDirectory>src</sourceDirectory>
        <plugins>
            <plugin>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
                <configuration>
                    <release>15</release>
                </configuration>
            </plugin>
        </plugins>
    </build>
    <dependencies>
        <dependency>
            <groupId>com.azure</groupId>
            <artifactId>azure-core</artifactId>
            <version>1.13.0</version>
        </dependency>
        <dependency>
            <groupId>com.azure</groupId>
            <artifactId>azure-messaging-servicebus</artifactId>
            <version>7.0.2</version>
        </dependency>
    </dependencies>
</project>
```

### <a name="add-code-to-send-messages-to-the-queue"></a>Kód hozzáadása az üzenetek várólistába küldéséhez
1. Adja hozzá a következő `import` utasításokat a Java-fájl témaköréhez. 

    ```java
    import com.azure.messaging.servicebus.*;
    
    import java.util.concurrent.CountDownLatch;
    import java.util.concurrent.TimeUnit;
    import java.util.Arrays;
    import java.util.List;
    ```    
5. A osztályban adja meg a kapcsolatok karakterláncának és a várólista nevének a megtartásához szükséges változókat az alább látható módon: 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String queueName = "<QUEUE NAME>";    
    ```

    Cserélje le a `<NAMESPACE CONNECTION STRING>` karakterláncot a Service Bus névtérhez tartozó kapcsolódási sztringre. És cserélje le a `<QUEUE NAME>` nevet a várólista nevére.
3. Adjon hozzá egy nevű metódust `sendMessage` a osztályhoz, hogy egy üzenetet küldjön a várólistára. 

    ```java
    static void sendMessage()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .queueName(queueName)
                .buildClient();
        
        // send one message to the queue
        senderClient.sendMessage(new ServiceBusMessage("Hello, World!"));
        System.out.println("Sent a single message to the queue: " + queueName);        
    }
    ```
1. Az üzenetek listájának létrehozásához vegyen fel egy nevű metódust `createMessages` a osztályban. Ezeket az üzeneteket általában az alkalmazás különböző részeiről szerezheti be. Itt a mintaadatok listáját fogjuk létrehozni.

    ```java
    static List<ServiceBusMessage> createMessages()
    {
        // create a list of messages and return it to the caller
        ServiceBusMessage[] messages = {
                new ServiceBusMessage("First message"),
                new ServiceBusMessage("Second message"),
                new ServiceBusMessage("Third message")
        };
        return Arrays.asList(messages);
    }
    ```
1. Adjon hozzá egy metódus nevű metódust `sendMessageBatch` az üzenetek küldéséhez a létrehozott várólistára. Ez a metódus létrehoz egy- `ServiceBusSenderClient` t a várólistához, meghívja a `createMessages` metódust az üzenetek listájának lekéréséhez, előkészíti egy vagy több köteget, és elküldi a kötegeket a várólistába. 

    ```java
    static void sendMessageBatch()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .queueName(queueName)
                .buildClient();

        // Creates an ServiceBusMessageBatch where the ServiceBus.
        ServiceBusMessageBatch messageBatch = senderClient.createMessageBatch();        
        
        // create a list of messages
        List<ServiceBusMessage> listOfMessages = createMessages();
        
        // We try to add as many messages as a batch can fit based on the maximum size and send to Service Bus when
        // the batch can hold no more messages. Create a new batch for next set of messages and repeat until all
        // messages are sent.        
        for (ServiceBusMessage message : listOfMessages) {
            if (messageBatch.tryAddMessage(message)) {
                continue;
            }

            // The batch is full, so we create a new batch and send the batch.
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the queue: " + queueName);
            
            // create a new batch
            messageBatch = senderClient.createMessageBatch();

            // Add that message that we couldn't before.
            if (!messageBatch.tryAddMessage(message)) {
                System.err.printf("Message is too large for an empty batch. Skipping. Max size: %s.", messageBatch.getMaxSizeInBytes());
            }
        }
        
        if (messageBatch.getCount() > 0) {
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the queue: " + queueName);
        }

        //close the client
        senderClient.close();
    }
    ```

## <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása egy várólistából
Ebben a szakaszban kódot fog hozzáadni az üzenetek várólistából való lekéréséhez. 

1. Adjon hozzá egy nevű metódust `receiveMessages` az üzenetek fogadásához a várólistából. Ez a metódus létrehoz egy `ServiceBusProcessorClient` -t az üzenetsor számára egy kezelő megadásával az üzenetek feldolgozásához, és egy másikat a hibák kezeléséhez. Ezután elindítja a processzort, megvárja a pár másodpercig, kinyomtatja a fogadott üzeneteket, majd leállítja és bezárja a processzort.

    > [!IMPORTANT]
    > A kód helyére írja be az `QueueTest` `QueueTest::processMessage` osztály nevét. 

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        CountDownLatch countdownLatch = new CountDownLatch(1);

        // Create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .queueName(queueName)
            .processMessage(QueueTest::processMessage)
            .processError(context -> processError(context, countdownLatch))
            .buildProcessorClient();

        System.out.println("Starting the processor");
        processorClient.start();

        TimeUnit.SECONDS.sleep(10);
        System.out.println("Stopping and closing the processor");
        processorClient.close();        
    }   
    ```
2. Adja hozzá a `processMessage` metódust a Service Bus-előfizetésből fogadott üzenet feldolgozásához. 

    ```java
    private static void processMessage(ServiceBusReceivedMessageContext context) {
        ServiceBusReceivedMessage message = context.getMessage();
        System.out.printf("Processing message. Session: %s, Sequence #: %s. Contents: %s%n", message.getMessageId(),
            message.getSequenceNumber(), message.getBody());
    }    
    ```
3. Adja hozzá a `processError` metódust a hibaüzenetek kezeléséhez.

    ```java
    private static void processError(ServiceBusErrorContext context, CountDownLatch countdownLatch) {
        System.out.printf("Error when receiving messages from namespace: '%s'. Entity: '%s'%n",
            context.getFullyQualifiedNamespace(), context.getEntityPath());

        if (!(context.getException() instanceof ServiceBusException)) {
            System.out.printf("Non-ServiceBusException occurred: %s%n", context.getException());
            return;
        }

        ServiceBusException exception = (ServiceBusException) context.getException();
        ServiceBusFailureReason reason = exception.getReason();

        if (reason == ServiceBusFailureReason.MESSAGING_ENTITY_DISABLED
            || reason == ServiceBusFailureReason.MESSAGING_ENTITY_NOT_FOUND
            || reason == ServiceBusFailureReason.UNAUTHORIZED) {
            System.out.printf("An unrecoverable error occurred. Stopping processing with reason %s: %s%n",
                reason, exception.getMessage());

            countdownLatch.countDown();
        } else if (reason == ServiceBusFailureReason.MESSAGE_LOCK_LOST) {
            System.out.printf("Message lock lost for message: %s%n", context.getException());
        } else if (reason == ServiceBusFailureReason.SERVICE_BUSY) {
            try {
                // Choosing an arbitrary amount of time to wait until trying again.
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                System.err.println("Unable to sleep for period of time");
            }
        } else {
            System.out.printf("Error source %s, reason %s, message: %s%n", context.getErrorSource(),
                reason, context.getException());
        }
    }  
    ```
2. Frissítse a `main` metódust, hogy meghívja `sendMessage` , `sendMessageBatch` , és `receiveMessages` metódusokat, és dobja `InterruptedException` .     

    ```java
    public static void main(String[] args) throws InterruptedException {        
        sendMessage();
        sendMessageBatch();
        receiveMessages();
    }   
    ```

## <a name="run-the-app"></a>Az alkalmazás futtatása
Az alkalmazás futtatásakor a konzol ablakban a következő üzenetek jelennek meg. 

```console
Sent a single message to the queue: myqueue
Sent a batch of messages to the queue: myqueue
Starting the processor
Processing message. Session: 88d961dd801f449e9c3e0f8a5393a527, Sequence #: 1. Contents: Hello, World!
Processing message. Session: e90c8d9039ce403bbe1d0ec7038033a0, Sequence #: 2. Contents: First message
Processing message. Session: 311a216a560c47d184f9831984e6ac1d, Sequence #: 3. Contents: Second message
Processing message. Session: f9a871be07414baf9505f2c3d466c4ab, Sequence #: 4. Contents: Third message
Stopping and closing the processor
```

A Azure Portal Service Bus névterének **Áttekintés** lapján láthatók a **bejövő** és a **kimenő** üzenetek száma. Előfordulhat, hogy várnia kell egy percet, majd frissítenie kell a lapot a legfrissebb értékek megtekintéséhez. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Bejövő és kimenő üzenetek száma" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

Válassza ki a várólistát ezen az **áttekintő** lapon, és navigáljon a **Service Bus üzenetsor** lapra. Ezen a lapon a **bejövő** és a **kimenő** üzenetek száma is látható. Más információk is megjelennek, például a várólista **jelenlegi mérete** , a **maximális méret**, az **aktív üzenetek száma** és így tovább. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="Üzenetsor részletei" lightbox="./media/service-bus-java-how-to-use-queues/queue-details.png":::



## <a name="next-steps"></a>Következő lépések
Tekintse meg a következő dokumentációt és mintákat:

- [A Javához készült ügyféloldali kódtár Azure Service Bus – readme](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [Minták a GitHubon](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Java API-referencia](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-messaging-servicebus/7.0.0/index.html)

[Azure SDK for Java]: /azure/developer/java/sdk/java-sdk-azure-get-started
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage