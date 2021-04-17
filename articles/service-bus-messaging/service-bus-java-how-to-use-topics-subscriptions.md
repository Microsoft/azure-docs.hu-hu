---
title: A Azure Service Bus és előfizetések használata a Javával (azure-messaging-servicebus)
description: Ebben a rövid útmutatóban Java-kódot fog írni az azure-messaging-servicebus csomaggal, amely üzeneteket küld egy Azure Service Bus-témakörbe, majd üzeneteket fogad az előfizetésektől az üzenettémakörbe.
ms.date: 02/13/2021
ms.topic: quickstart
ms.devlang: Java
ms.custom:
- mode-api
ms.openlocfilehash: 6fe0a3a91ebbd5b6daced95494b8eaa5b7db0c46
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536386"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-java"></a>Üzenetek küldése egy Azure Service Bus témakörbe és üzenetek fogadása az előfizetésekből a témakörbe (Java)
Ebben a rövid útmutatóban Java-kódot fog írni az azure-messaging-servicebus csomaggal, amely üzeneteket küld egy Azure Service Bus-témakörbe, majd üzeneteket fogad az előfizetésekből az üzenettémakörbe.

> [!IMPORTANT]
> Ez a rövid útmutató az új azure-messaging-servicebus csomagot használja. A régi azure-servicebus csomagot használó rövid útmutatóért lásd: Üzenetek küldése és fogadása [az azure-servicebus használatával.](service-bus-java-how-to-use-topics-subscriptions-legacy.md)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja a Visual Studio [MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) előfizetői előnyeit, vagy regisztrálhat egy [ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Kövesse a rövid útmutató [lépéseit: A](service-bus-quickstart-topics-subscriptions-portal.md)Azure Portal segítségével hozzon létre egy Service Bus témakört és előfizetéseket a témakörre. Jegyezze fel a kapcsolati sztringet, a témakör nevét és az előfizetés nevét. Ebben a rövid útmutatóban csak egy előfizetést fog használni. 
- Telepítse a [Javához készült Azure SDK-t.][Azure SDK for Java] Ha eclipse-et használ, telepítheti a javához készült Azure [SDK Azure Toolkit for Eclipse][Azure Toolkit for Eclipse] tartalmazó alkalmazást. Ezután hozzáadhatja a **Microsoft Azure Java-kódtárakat** a projekthez. IntelliJ használata esetén lásd: [Install the Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation). 


## <a name="send-messages-to-a-topic"></a>Üzenetek küldése egy üzenettémakörbe
Ebben a szakaszban létre fog hozni egy Java-konzolprojektet, és hozzáad egy kódot, amely üzeneteket küld a létrehozott témakörbe. 

### <a name="create-a-java-console-project"></a>Java-konzolprojekt létrehozása
Hozzon létre egy Java-projektet az Eclipse vagy egy ön által választott eszköz használatával. 

### <a name="configure-your-application-to-use-service-bus"></a>Az alkalmazás konfigurálása a Service Bus
Hivatkozásokat adhat hozzá az Azure Core-hoz és Azure Service Bus kódtárakhoz. 

Ha az Eclipse-et használja, és létrehozott egy Java-konzolalkalmazást, alakítsa át a Java-projektet Mavenre: kattintson a jobb gombbal a projektre a Package **Explorer** (Csomagkezelő) ablakban, és válassza a **Configure** Convert to Maven project (Konvertálás  ->  **konfigurálása Maven-projektre) lehetőséget.** Ezután adja hozzá a függőségeket ehhez a két kódtárhoz az alábbi példában látható módon.

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

### <a name="add-code-to-send-messages-to-the-topic"></a>Kód hozzáadása az üzenetek témakörbe való elküldését
1. Adja hozzá a következő `import` utasításokat a Java-fájl témakörében. 

    ```java
    import com.azure.messaging.servicebus.*;
    
    import java.util.concurrent.CountDownLatch;
    import java.util.concurrent.TimeUnit;
    import java.util.Arrays;
    import java.util.List;
    ```    
5. A osztályban határozzon meg változókat a kapcsolati sztring és a témakör nevének a következő módon való beállításhoz: 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String topicName = "<TOPIC NAME>";    
    static String subName = "<SUBSCRIPTION NAME>";
    ```

    Cserélje le a helyére a saját Service Bus `<NAMESPACE CONNECTION STRING>` sztringet. És cserélje `<TOPIC NAME>` le a helyére a témakör nevét.
3. Adjon hozzá egy nevű metódust a `sendMessage` osztályban, hogy egy üzenetet küldjön a témakörbe. 

    ```java
    static void sendMessage()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .topicName(topicName)
                .buildClient();
        
        // send one message to the topic
        senderClient.sendMessage(new ServiceBusMessage("Hello, World!"));
        System.out.println("Sent a single message to the topic: " + topicName);        
    }
    ```
1. Adjon hozzá egy nevű `createMessages` metódust a osztályban az üzenetek listájának létrehozásához. Ezeket az üzeneteket általában az alkalmazás különböző részeiből kapják meg. Itt létrehozunk egy mintaüzenetek listáját.

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
1. Adjon hozzá egy metódus nevű `sendMessageBatch` metódust, amely üzeneteket küld a létrehozott témakörbe. Ez a metódus létrehoz egy et a témakör számára, meghívja a metódust az üzenetek listájának lekérte, előkészít egy vagy több köteget, és elküldi a kötegeket a `ServiceBusSenderClient` `createMessages` témakörbe. 

    ```java
    static void sendMessageBatch()
    {
        // create a Service Bus Sender client for the topic 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .topicName(topicName)
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
            System.out.println("Sent a batch of messages to the topic: " + topicName);
            
            // create a new batch
            messageBatch = senderClient.createMessageBatch();

            // Add that message that we couldn't before.
            if (!messageBatch.tryAddMessage(message)) {
                System.err.printf("Message is too large for an empty batch. Skipping. Max size: %s.", messageBatch.getMaxSizeInBytes());
            }
        }

        if (messageBatch.getCount() > 0) {
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the topic: " + topicName);
        }

        //close the client
        senderClient.close();
    }
    ```

## <a name="receive-messages-from-a-subscription"></a>Üzenetek fogadása előfizetésből
Ebben a szakaszban olyan kódot fog hozzáadni, amely üzeneteket fog lekérni egy előfizetésből a témakörbe. 

1. Adjon hozzá egy nevű `receiveMessages` metódust, amely üzeneteket fogad az előfizetésből. Ez a metódus létrehoz egy et az előfizetéshez egy kezelő megadásával az üzenetek feldolgozásához, és egy másikat a `ServiceBusProcessorClient` hibák kezeléséhez. Ezután elindítja a processzort, néhány másodpercet vár, kinyomtatja a fogadott üzeneteket, majd leállítja és bezárja a processzort.

    > [!IMPORTANT]
    > A kódban cserélje le a `ServiceBusTopicTest` `ServiceBusTopicTest::processMessage` helyére az osztály nevét. 

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        CountDownLatch countdownLatch = new CountDownLatch(1);

        // Create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .topicName(topicName)
            .subscriptionName(subName)
            .processMessage(ServiceBusTopicTest::processMessage)
            .processError(context -> processError(context, countdownLatch))
            .buildProcessorClient();

        System.out.println("Starting the processor");
        processorClient.start();

        TimeUnit.SECONDS.sleep(10);
        System.out.println("Stopping and closing the processor");
        processorClient.close();        
    }  
    ```
2. Adja hozzá a metódust a Service Bus `processMessage` üzenetének feldolgozásához. 

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
1. Frissítse a `main` metódust, hogy meghívja a `sendMessage` , a és a `sendMessageBatch` `receiveMessages` metódust a és a `InterruptedException` meghívásával.     

    ```java
    public static void main(String[] args) throws InterruptedException {        
        sendMessage();
        sendMessageBatch();
        receiveMessages();
    }   
    ```

## <a name="run-the-app"></a>Az alkalmazás futtatása
Futtassa a programot, hogy az alábbihoz hasonló kimenetet lát:

```console
Sent a single message to the topic: mytopic
Sent a batch of messages to the topic: mytopic
Starting the processor
Processing message. Session: e0102f5fbaf646988a2f4b65f7d32385, Sequence #: 1. Contents: Hello, World!
Processing message. Session: 3e991e232ca248f2bc332caa8034bed9, Sequence #: 2. Contents: First message
Processing message. Session: 56d3a9ea7df446f8a2944ee72cca4ea0, Sequence #: 3. Contents: Second message
Processing message. Session: 7bd3bd3e966a40ebbc9b29b082da14bb, Sequence #: 4. Contents: Third message
```

A  Service Bus-névtér Áttekintés Azure Portal láthatja a bejövő és kimenő  **üzenetek** számát. Előfordulhat, hogy várnia kell egy percet, majd frissítenie kell az oldalt a legújabb értékekhez. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Bejövő és kimenő üzenetek száma" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

Váltson **a Témakörök** lapra a középső alsó panelen, és válassza ki a témakört a **témakör Service Bus témakör** lapján. Ezen az oldalon négy bejövő és négy kimenő üzenetet kell látnia az **Üzenetek diagramon.** 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="Bejövő és kimenő üzenetek" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png":::

Ha megjegyzésbe fűzi a hívást a metódusban, és újra futtatja az alkalmazást, a Service Bus-témakör oldalon 8 bejövő üzenet `receiveMessages` `main` (4  új), de négy kimenő üzenet látható. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png" alt-text="Frissített témaköroldal" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png":::

Ezen a lapon, ha kiválaszt egy előfizetést, a Service Bus **lapjára** jut. Ezen az oldalon láthatja az aktív üzenetek számát, a holtbetűs üzenetek számát és sok más stb. Ebben a példában négy aktív üzenet van, amelyet még nem kapott meg a fogadó. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png" alt-text="Aktív üzenetek száma" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png":::

## <a name="next-steps"></a>Következő lépések
Tekintse meg az alábbi dokumentációt és mintákat:

- [Azure Service Bus Java-ügyféloldali kódtár – Readme](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [Minták a GitHubon](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Java API-referencia](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-messaging-servicebus/7.0.0/index.html)


[Azure SDK for Java]: /java/api/overview/azure/
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.azure.servicebus.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.azure.servicebus.sqlfilter.sqlexpression
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
