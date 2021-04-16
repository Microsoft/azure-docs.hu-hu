---
title: Üzenetsorok Azure Service Bus használata Javával (azure-messaging-servicebus)
description: Ez az oktatóanyag bemutatja, hogyan küldhet üzeneteket egy üzenetsorba, és fogadhat üzeneteket a Java Azure Service Bus üzenetsorból. Az új azure-messaging-servicebus csomagot használja.
ms.date: 02/13/2021
ms.topic: quickstart
ms.devlang: Java
ms.custom:
- seo-java-july2019
- seo-java-august2019
- seo-java-september2019
- devx-track-java
- mode-api
ms.openlocfilehash: e3998e812bb921ff8bea5e83199bc4e142ed2e83
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533432"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-java"></a>Üzenetek küldése és fogadása Azure Service Bus üzenetsorok között (Java)
Ebben a rövid útmutatóban egy Java-alkalmazást fog létrehozni, amely üzeneteket küld egy üzenetsorba, és üzeneteket fogad Azure Service Bus üzenetsorból. 

> [!IMPORTANT]
> Ez a rövid útmutató az új azure-messaging-servicebus csomagot használja. A régi azure-servicebus csomagot használó rövid útmutatóért lásd: Üzenetek küldése és fogadása [az azure-servicebus használatával.](service-bus-java-how-to-use-queues-legacy.md)


## <a name="prerequisites"></a>Előfeltételek
- Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja [MSDN-előfizetői előnyeit,](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) vagy regisztrálhat egy [ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Ha nincs használható üzenetsora, kövesse a Use [Azure Portal to create a Service Bus queue](service-bus-quickstart-portal.md) (Üzenetsor létrehozása üzenetsor létrehozásához) cikk lépéseit az üzenetsor létrehozásához. Jegyezze fel **a** Service Bus névterének kapcsolati sztringjét és a **létrehozott** üzenetsor nevét.
- Telepítse a [Javához készült Azure SDK-t.][Azure SDK for Java] Ha eclipse-et használ, telepítheti a javához készült Azure [SDK Azure Toolkit for Eclipse][Azure Toolkit for Eclipse] tartalmazó alkalmazást. Ezután hozzáadhatja a **Microsoft Azure Java-kódtárakat** a projekthez. IntelliJ használata esetén lásd: [Install the Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation). 


## <a name="send-messages-to-a-queue"></a>Üzenetek küldése egy üzenetsorba
Ebben a szakaszban létre fog hozni egy Java-konzolprojektet, és hozzáad egy kódot, amely üzeneteket küld a korábban létrehozott üzenetsorba. 

### <a name="create-a-java-console-project"></a>Java-konzolprojekt létrehozása
Hozzon létre egy Java-projektet az Eclipse vagy egy ön által választott eszköz használatával. 

### <a name="configure-your-application-to-use-service-bus"></a>Az alkalmazás konfigurálása a Service Bus
Adjon hozzá Azure Core-referenciákat és Azure Service Bus kódtárakat. 

Ha az Eclipse-et használja, és létrehozott egy Java-konzolalkalmazást, alakítsa át a Java-projektet Mavenre: kattintson a jobb gombbal a projektre a Package **Explorer** (Csomagkezelő) ablakban, és válassza a **Configure** Convert to Maven project (Átalakítás  ->  **Maven-projektre konfigurálása) lehetőséget.** Ezután adja hozzá a függőségeket ehhez a két kódtárhoz az alábbi példában látható módon.

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

### <a name="add-code-to-send-messages-to-the-queue"></a>Kód hozzáadása az üzenetek üzenetsorba való küldése számára
1. Adja hozzá a következő `import` utasításokat a Java-fájl témakörében. 

    ```java
    import com.azure.messaging.servicebus.*;
    
    import java.util.concurrent.CountDownLatch;
    import java.util.concurrent.TimeUnit;
    import java.util.Arrays;
    import java.util.List;
    ```    
5. A osztályban definiálja a kapcsolati sztringet és az üzenetsor nevét tetsző változókat az alábbiak szerint: 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String queueName = "<QUEUE NAME>";    
    ```

    Cserélje le a helyére a saját Service Bus `<NAMESPACE CONNECTION STRING>` sztringet. És cserélje `<QUEUE NAME>` le a helyére az üzenetsor nevét.
3. Adjon hozzá egy nevű metódust a `sendMessage` osztályban, hogy egy üzenetet küldjön az üzenetsorba. 

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
1. Adjon hozzá egy nevű `createMessages` metódust a osztályban az üzenetek listájának létrehozásához. Ezeket az üzeneteket általában az alkalmazás különböző részeiből kapják meg. Itt mintaüzeneteket hozunk létre.

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
1. Adjon hozzá egy metódus nevű `sendMessageBatch` metódust, amely üzeneteket küld a létrehozott üzenetsorba. Ez a metódus létrehoz egy et az üzenetsorhoz, meghívja a metódust az üzenetek listájának le kérése, előkészít egy vagy több köteget, és elküldi a kötegeket az `ServiceBusSenderClient` `createMessages` üzenetsorba. 

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

## <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása üzenetsorból
Ebben a szakaszban kódot fog hozzáadni, amely üzeneteket fog lekérni az üzenetsorból. 

1. Adjon hozzá egy nevű `receiveMessages` metódust, amely üzeneteket fogad az üzenetsorból. Ez a metódus létrehoz egy et az üzenetsorhoz azáltal, hogy megad egy kezelőt az üzenetek feldolgozásához, és egy másikat a `ServiceBusProcessorClient` hibák kezeléshez. Ezután elindítja a processzort, néhány másodpercet vár, kinyomtatja a fogadott üzeneteket, majd leállítja és bezárja a processzort.

    > [!IMPORTANT]
    > A `QueueTest` `QueueTest::processMessage` kódban cserélje le a helyére az osztály nevét. 

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
2. Adja hozzá a metódust a fiók `processMessage` előfizetéséből fogadott Service Bus feldolgozásához. 

    ```java
    private static void processMessage(ServiceBusReceivedMessageContext context) {
        ServiceBusReceivedMessage message = context.getMessage();
        System.out.printf("Processing message. Session: %s, Sequence #: %s. Contents: %s%n", message.getMessageId(),
            message.getSequenceNumber(), message.getBody());
    }    
    ```
3. Adja hozzá `processError` a hibaüzeneteket kezelő metódust.

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
2. Frissítse a `main` metódust úgy, hogy `sendMessage` meghívja `sendMessageBatch` a , és `receiveMessages` metódusokat, és a következőt használja: `InterruptedException` .     

    ```java
    public static void main(String[] args) throws InterruptedException {        
        sendMessage();
        sendMessageBatch();
        receiveMessages();
    }   
    ```

## <a name="run-the-app"></a>Az alkalmazás futtatása
Az alkalmazás futtatásakor a következő üzenetek megjelenik a konzolablakban. 

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

A  Service Bus-névtér Áttekintés Azure Portal láthatja a bejövő és kimenő  **üzenetek** számát. Előfordulhat, hogy várnia kell egy percet, majd frissítenie kell az oldalt a legújabb értékekhez. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Bejövő és kimenő üzenetek száma" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

Az Áttekintés oldalon válassza ki az **üzenetsort,** és lépjen a Service Bus **lapra.** Ezen az oldalon **a bejövő** és **kimenő** üzenetek száma is megjelenik. Egyéb információkat is láthat,  például az üzenetsor aktuális **méretét,** a maximális méretet, az aktív **üzenetek számát** stb. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="Üzenetsor részletei" lightbox="./media/service-bus-java-how-to-use-queues/queue-details.png":::



## <a name="next-steps"></a>Következő lépések
Tekintse meg az alábbi dokumentációt és mintákat:

- [Azure Service Bus Java-ügyféloldali kódtár – Readme](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [Minták a GitHubon](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Java API-referencia](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-messaging-servicebus/7.0.0/index.html)

[Azure SDK for Java]: /azure/developer/java/sdk/get-started
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
