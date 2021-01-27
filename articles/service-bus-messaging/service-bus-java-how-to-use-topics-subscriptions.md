---
title: Azure Service Bus témakörök és előfizetések használata Javával (Azure-Messaging-servicebus)
description: Ebben a rövid útmutatóban Java-kódokat ír az Azure-Messaging-servicebus csomag használatával, amely üzeneteket küld egy Azure Service Bus témakörnek, majd üzeneteket fogad az előfizetésből az adott témakörbe.
ms.devlang: Java
ms.topic: quickstart
ms.date: 11/09/2020
ms.openlocfilehash: 46dc6bed7e51a5157d7eb42dac75c0240d440780
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881618"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-java"></a>Üzenetek küldése egy Azure Service Bus témakörnek, és üzenetek fogadása az előfizetésből a témakörbe (Java)
Ebben a rövid útmutatóban Java-kódokat ír az Azure-Messaging-servicebus csomag használatával, amely üzeneteket küld egy Azure Service Bus témakörnek, majd üzeneteket fogad az előfizetésből az adott témakörbe.

> [!IMPORTANT]
> Ez a rövid útmutató az új Azure-Messaging-servicebus csomagot használja. A régi Azure-servicebus csomagot használó gyors útmutatóért lásd: [üzenetek küldése és fogadása az Azure-servicebus használatával](service-bus-java-how-to-use-topics-subscriptions-legacy.md).

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja [Visual Studio-vagy MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) , vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Kövesse a rövid útmutató lépéseit [: a Azure Portal használatával hozzon létre egy Service Bus témakört és előfizetéseket a témakörbe](service-bus-quickstart-topics-subscriptions-portal.md). Jegyezze fel a kapcsolatok karakterláncát, a témakör nevét és az előfizetés nevét. Ehhez a rövid útmutatóhoz csak egy előfizetést fog használni. 
- Telepítse [a Javához készült Azure SDK][Azure SDK for Java]-t. Ha az Eclipse-t használja, akkor telepítheti a Javához készült Azure SDK-t tartalmazó [Azure Toolkit for Eclipse][Azure Toolkit for Eclipse] . Ezután hozzáadhatja a **Javához készült Microsoft Azure kódtárakat** a projekthez. Ha a IntelliJ-t használja, tekintse meg [a Azure Toolkit for IntelliJ telepítését](/azure/developer/java/toolkit-for-intellij/installation)ismertető témakört. 


## <a name="send-messages-to-a-topic"></a>Üzenetek küldése egy üzenettémakörbe
Ebben a szakaszban létre fog hozni egy Java-konzol projektjét, és hozzá kell adnia egy kódot, amely üzeneteket küld a létrehozott témakörnek. 

### <a name="create-a-java-console-project"></a>Java-konzol projekt létrehozása
Hozzon létre egy Java-projektet az Eclipse vagy egy tetszőleges eszköz használatával. 

### <a name="configure-your-application-to-use-service-bus"></a>Az alkalmazás konfigurálása Service Bus használatára
Azure Service Bus könyvtárra mutató hivatkozás hozzáadása. A Service Bus Java-ügyféloldali könyvtára a [Maven Central adattárában](https://search.maven.org/search?q=a:azure-messaging-servicebus)érhető el. Ezt a kódtárat a következő függőségi deklarációval hivatkozhat a Maven-projektfájl használatával:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-servicebus</artifactId>
    <version>7.0.0</version>
</dependency>
```

### <a name="add-code-to-send-messages-to-the-topic"></a>Kód hozzáadása az üzenetek a témakörbe való küldéséhez
1. Adja hozzá a következő `import` utasításokat a Java-fájl témaköréhez. 

    ```java
    import com.azure.messaging.servicebus.*;
    import com.azure.messaging.servicebus.models.*;
    import java.util.concurrent.TimeUnit;
    import java.util.function.Consumer;
    import java.util.Arrays;
    import java.util.List;
    ```    
5. A osztályban adja meg a kapcsolódási karakterlánc és a témakör nevét a következő ábrán látható változók alapján: 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String topicName = "<TOPIC NAME>";    
    static String subName = "<SUBSCRIPTION NAME>";
    ```

    Cserélje le a `<NAMESPACE CONNECTION STRING>` karakterláncot a Service Bus névtérhez tartozó kapcsolódási sztringre. És cserélje le a `<TOPIC NAME>` nevet a témakör nevére.
3. Adjon hozzá egy nevű metódust `sendMessage` a osztályhoz, hogy egy üzenetet küldjön a témakörnek. 

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
1. Adjon hozzá egy metódus nevű metódust `sendMessageBatch` az üzenetek küldéséhez a létrehozott témakörbe. Ez a metódus létrehoz egy- `ServiceBusSenderClient` t a témakörhöz, meghívja a `createMessages` metódust az üzenetek listájának beolvasásához, előkészíti egy vagy több köteget, és elküldi a kötegeket a témakörnek. 

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

## <a name="receive-messages-from-a-subscription"></a>Üzenetek fogadása egy előfizetésből
Ebben a szakaszban kód hozzáadásával kérheti le az üzeneteket egy előfizetésből a témakörbe. 

1. Adjon hozzá egy nevű metódust az `receiveMessages` előfizetésből érkező üzenetek fogadásához. Ez a metódus létrehoz egy `ServiceBusProcessorClient` -előfizetést egy kezelő megadásával az üzenetek feldolgozásához, és egy másikat a hibák kezeléséhez. Ezután elindítja a processzort, megvárja a pár másodpercig, kinyomtatja a fogadott üzeneteket, majd leállítja és bezárja a processzort.

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        // Consumer that processes a single message received from Service Bus
        Consumer<ServiceBusReceivedMessageContext> messageProcessor = context -> {
            ServiceBusReceivedMessage message = context.getMessage();
            System.out.println("Received message: " + message.getBody().toString() + " from the subscription: " + subName);
        };

        // Consumer that handles any errors that occur when receiving messages
        Consumer<Throwable> errorHandler = throwable -> {
            System.out.println("Error when receiving messages: " + throwable.getMessage());
            if (throwable instanceof ServiceBusReceiverException) {
                ServiceBusReceiverException serviceBusReceiverException = (ServiceBusReceiverException) throwable;
                System.out.println("Error source: " + serviceBusReceiverException.getErrorSource());
            }
        };

        // Create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .topicName(topicName)
            .subscriptionName(subName)
            .processMessage(messageProcessor)
            .processError(errorHandler)
            .buildProcessorClient();

        System.out.println("Starting the processor");
        processorClient.start();

        TimeUnit.SECONDS.sleep(10);
        System.out.println("Stopping and closing the processor");
        processorClient.close();        
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
A program futtatásával tekintse meg a következő kimenethez hasonló kimenetet:

```console
Sent a batch of messages to the topic: mytopic
Starting the processor
Received message: First message from the subscription: mysub
Received message: Second message from the subscription: mysub
Received message: Third message from the subscription: mysub
Stopping and closing the processor
```

A Azure Portal Service Bus névterének **Áttekintés** lapján láthatók a **bejövő** és a **kimenő** üzenetek száma. Előfordulhat, hogy várnia kell egy percet, majd frissítenie kell a lapot a legfrissebb értékek megtekintéséhez. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Bejövő és kimenő üzenetek száma" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

Váltson a **témakörök** lapra a középső alsó ablaktáblán, és válassza ki a témakört, és tekintse meg a témakör **Service Bus témakör** lapját. Ezen az oldalon négy bejövő és négy kimenő üzenetet kell látnia az **üzenetek** diagramon. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="Bejövő és kimenő üzenetek" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png":::

Ha kiírja a `receiveMessages` hívást a `main` metódusban, és ismét futtatja az alkalmazást, akkor a **Service Bus témakör** oldalon 8 bejövő üzenet jelenik meg (4 új), de négy kimenő üzenet. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png" alt-text="Frissített témakör lap" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png":::

Ezen az oldalon, ha kijelöl egy előfizetést, a **Service Bus előfizetés** oldalra kerül. Ezen az oldalon megtekintheti az aktív üzenetek darabszámát, a kézbesítetlen üzenetek darabszámát és további információt. Ebben a példában négy olyan aktív üzenet van, amelyet a fogadó még nem fogadott el. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png" alt-text="Aktív üzenetek száma" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png":::

## <a name="next-steps"></a>További lépések
Tekintse meg a következő dokumentációt és mintákat:

- [A Javához készült ügyféloldali kódtár Azure Service Bus – readme](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [Példák a GitHubon](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Java API-referencia](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-messaging-servicebus/7.0.0/index.html)


[Azure SDK for Java]: /java/api/overview/azure/
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.azure.servicebus.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.azure.servicebus.sqlfilter.sqlexpression
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage